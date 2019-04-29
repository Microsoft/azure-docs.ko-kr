---
title: Azure Backup - Azure Import/Export 서비스를 사용한 오프라인 백업 또는 초기 시드 작업
description: Azure Backup이 Azure Import/Export 서비스를 사용하여 네트워크를 통해 데이터를 보내는 방법에 대해 알아봅니다. 이 문서에서는 Azure 가져오기 내보내기 서비스를 사용한 초기 백업 데이터의 오프라인 시드 작업을 설명합니다.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: b6f0ce1939b2a78ca191d2feb0140506d130b9b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648353"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup의 오프라인 백업 워크플로
Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 저장소 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 "전체" 백업은 일반적으로 많은 양의 데이터를 전송하며 델타/증분만 전송하는 후속 백업에 비해 네트워크 대역폭을 더 많이 요구합니다. 오프라인 시드 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인 백업 데이터를 Azure에 업로드할 수 있습니다.

Azure Backup 오프라인 시드 프로세스는 디스크를 사용하여 초기 백업 데이터를 Azure에 전송할 수 있는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md) 와 밀접하게 통합되어 있습니다. 긴 대기 시간 및 낮은 대역폭 네트워크를 통해 전송해야 하는 TB(테라바이트)의 초기 백업 데이터가 있다면 오프라인 시드 워크플로를 사용하여 하나 이상의 하드 드라이브에 있는 초기 백업 복사본을 Azure 데이터 센터로 배송할 수 있습니다. 다음 이미지는 워크플로의 단계에 대한 개요를 제공합니다.

  ![오프라인 가져오기 워크플로 프로세스의 개요](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

오프라인 백업 프로세스는 다음과 같은 단계를 포함합니다.

1. 네트워크를 통해 백업 데이터를 보내는 대신 준비 위치에 백업 데이터를 기록합니다.
2. AzureOfflineBackupDiskPrep 유틸리티를 사용하여 준비 위치의 데이터를 하나 이상의 SATA 디스크에 기록합니다.
3. 준비 작업의 일환으로 AzureOfflineBackupDiskPrep 유틸리티는 Azure 가져오기 작업을 만듭니다. SATA 드라이브를 가장 가까운 Azure 데이터 센터로 보내고 가져오기 작업을 참조하여 활동을 연결합니다.
4. Azure 데이터 센터에서 디스크의 데이터는 Azure 저장소 계정에 복사됩니다.
5. Azure Backup은 저장소 계정의 백업 데이터를 Recovery Services 자격 증명 모음으로 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성 
다음 Azure Backup 기능 또는 워크로드는 오프라인 백업 사용을 지원합니다.

> [!div class="checklist"]
> * MARS(Microsoft Azure Recovery Services) 에이전트(다른 이름: Azure Backup 에이전트)를 사용하여 파일 및 폴더 백업 
> * SC DPM(System Center Data Protection Manager)을 사용하여 모든 워크로드 및 파일의 백업 
> * Microsoft Azure Backup Server를 사용하여 모든 워크로드 및 파일의 백업 <br/>

   > [!NOTE]
   > 오프라인 백업은 Azure Backup 에이전트를 사용하여 수행된 시스템 상태 백업에 대해 지원되지 않습니다. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>필수 조건

  > [!NOTE]
  > 다음 필수 구성 요소 및 워크플로는 [최신 MARS 에이전트](https://aka.ms/azurebackup_agent)를 사용한 오프라인 파일 및 폴더 백업에만 적용됩니다. System Center DPM 또는 Azure Backup Server를 사용하여 워크로드에 대한 오프라인 백업을 수행하려면 [이 문서](backup-azure-backup-server-import-export-.md)를 참조하세요. 

오프라인 백업 워크플로를 시작하기 전에 다음과 같은 전제 조건을 완료합니다. 
* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)을 만듭니다. 자격 증명 모음을 만들려면 [이 문서](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)의 단계를 참조하세요.
* 해당되는 경우 Windows Server/Windows 클라이언트에 [최신 버전의 Azure Backup 에이전트](https://aka.ms/azurebackup_agent)만 설치되어 있고 컴퓨터가 Recovery Services 자격 증명 모음에 등록되어 있습니다.
* Azure Backup 에이전트를 실행하는 컴퓨터에 Azure PowerShell 3.7.0이 필요합니다. [Azure PowerShell 3.7.0 버전을 다운로드하여 설치](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)하는 것이 좋습니다.
* Azure Backup 에이전트를 실행하는 컴퓨터에 Microsoft Edge 또는 Internet Explorer 11이 설치되어 있고 JavaScript가 활성화되어 있는지 확인합니다. 
* Recovery Services 자격 증명 모음과 동일한 구독에 Azure Storage 계정을 만듭니다. 
* Azure Active Directory 애플리케이션을 만드는 데 [필요한 권한](../active-directory/develop/howto-create-service-principal-portal.md)이 있는지 확인합니다. 오프라인 백업 워크플로는 Azure Storage 계정과 연결된 구독에 Azure Active Directory 애플리케이션을 만듭니다. 애플리케이션의 목표는 Azure Backup에 오프라인 백업 워크플로에 필요한 Azure Import 서비스에 대해 안전하고 범위가 지정된 액세스를 제공하는 것입니다. 
* Azure Storage 계정이 포함된 구독에 Microsoft.ImportExport 리소스 공급자를 등록합니다. 리소스 공급자 등록하려면:
    1. 주 메뉴에서 **구독**을 클릭합니다.
    2. 여러 구독에 등록한 경우 오프라인 백업에 사용하는 구독을 선택합니다. 구독을 하나만 사용하는 경우에는 구독이 나타납니다.
    3. 구독 메뉴에서 **리소스 공급자**를 클릭하여 공급자 목록을 봅니다.
    4. 공급자 목록에서 Microsoft.ImportExport까지 아래로 스크롤합니다. 상태가 NotRegistered이면 **등록**을 클릭합니다.
    ![리소스 공급자 등록](./media/backup-azure-backup-import-export/registerimportexport.png)
* 네트워크 공유 또는 컴퓨터의 추가 드라이브에 있을 수 있는, 초기 복사본을 저장할 충분한 디스크 공간이 있는 내부 또는 외부의 스테이징 위치가 생성됩니다. 예를 들어 500GB 파일 서버를 백업하려는 경우 준비 영역이 500GB인지 확인합니다. (압축으로 인해 더 작은 양이 사용됩니다.)
* Azure에 디스크를 보낼 때는 2.5인치 SSD나 2.5인치 또는 3.5인치 SATA II/III 내장 하드 드라이브만 사용합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)에서 서비스가 지원하는 최신 드라이브를 집합을 확인하세요.
* SATA 드라이브는 *준비 위치*에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. BitLocker가 *복사 컴퓨터*에서 활성화되는지 확인합니다.

## <a name="workflow"></a>워크플로
이 섹션에서는 데이터를 Azure 데이터 센터에 배송하고 Azure Storage에 업로드할 수 있도록 오프라인 백업 워크플로를 설명합니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대한 질문이 있으면 [가져오기 서비스 개요 설명서](../storage/common/storage-import-export-service.md)를 참조하세요.

## <a name="initiate-offline-backup"></a>오프라인 백업 시작
1. MARS 에이전트에서 백업을 예약하면 다음 화면이 표시됩니다.

    ![가져오기 화면](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   입력에 대한 설명은 다음과 같습니다.

    * **스테이징 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 서로 다르면 스테이징 위치의 전체 네트워크 경로를 지정하는 것이 좋습니다.
    * **Azure Resource Manager 스토리지 계정**: Azure 구독에 있는 Resource Manager 유형 스토리지 계정의 이름입니다.
    * **Azure Storage 컨테이너**: Recovery Services 자격 증명 모음에 복사되기 전에 백업 데이터를 가져올 Azure Storage 계정의 대상 스토리지 Blob 이름입니다.
    * **Azure 구독 ID**: Azure Storage 계정이 만들어지는 Azure 구독의 ID입니다.
    * **Azure 가져오기 작업 이름**: Azure 가져오기 서비스 및 Azure Backup이 디스크에서 Azure로 보낸 데이터의 전송을 추적하는 고유한 이름입니다. 
  
   화면에 입력을 제공하고 **다음**을 클릭합니다. 디스크를 준비하는 데 필요하므로 제공한 *준비 위치* 및 *Azure 가져오기 작업 이름*을 저장합니다.

2. 메시지가 표시되면 Azure 구독에 로그인합니다. Azure Backup이 Azure Active Directory 애플리케이션을 만들고 Azure Import 서비스에 액세스하는 데 필요한 권한을 제공할 수 있도록 로그인해야 합니다.

    ![지금 Backup](./media/backup-azure-backup-import-export/azurelogin.png)

3. 워크플로를 완료하고 Azure Backup 에이전트 콘솔에서 **지금 백업**을 클릭합니다.

    ![지금 Backup](./media/backup-azure-backup-import-export/backupnow.png)

4. 마법사의 확인 페이지에서 **백업**을 클릭합니다. 초기 백업은 설정의 일환으로 준비 영역에 기록됩니다.

   ![이제 백업할 준비가 되었는지 확인합니다.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    작업이 완료되면 스테이징 위치를 디스크 준비에 사용할 수 있습니다.

   ![지금 Backup](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure에 제공
*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터에 전송되는 SATA 드라이브를 준비합니다. 이 유틸리티는 Azure Backup 에이전트 설치 디렉터리(다음 경로)에서 사용할 수 있습니다.

   *\Microsoft Azure Recovery Services Agent\Utils\\*

1. 디렉터리로 이동하고 SATA 드라이브가 연결된 다른 컴퓨터에 **AzureOfflineBackupDiskPrep** 디렉터리를 복사합니다. 연결된 SATA 드라이브가 있는 컴퓨터에서 다음을 확인합니다.

   * 복사 컴퓨터는 **오프라인 백업 시작** 워크플로에 제공된 것과 같은 네트워크 경로를 사용하여 오프라인 시드 워크플로에 대해 제공된 스테이징 위치에 액세스할 수 있습니다.
   * BitLocker가 복사 컴퓨터에서 활성화됩니다.
   * Azure PowerShell 3.7.0이 설치되었습니다.
   * 호환되는 최신 브라우저(Microsoft Edge 또는 Internet Explorer 11)가 설치되어 있고 JavaScript가 활성화되어 있습니다. 
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다.
    
     > [!IMPORTANT] 
     > 원본 컴퓨터가 가상 머신인 경우 복사 컴퓨터는 원본 컴퓨터와 다른 물리적 서버 또는 클라이언트 컴퓨터여야 합니다.

2. *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 관리자 권한 명령 프롬프트를 복사 컴퓨터에서 열고 다음 명령을 실행합니다.

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*스테이징 위치 경로*&gt; |**오프라인 백업 시작** 워크플로에 입력한 준비 위치의 경로를 제공하는 데 사용되는 필수 입력입니다. |
    | p:&lt;*PublishSettingsFile에 대한 경로*&gt; |**오프라인 백업 시작** 워크플로에 입력한 **Azure 게시 설정** 파일에 대한 경로를 제공하는 데 사용되는 옵션 입력입니다. |

    명령을 실행할 때 유틸리티에서 준비해야 할 드라이브에 해당하는 Azure 가져오기 작업의 선택을 요청합니다. 제공된 스테이징 위치와 연결된 가져오기 작업이 하나만 있는 경우 다음과 같은 화면이 나타납니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Azure에 전송하도록 준비하려는 탑재된 디스크에 대한 드라이브 문자를 후행 콜론 없이 입력합니다. 
4. 메시지가 표시될 때 드라이브의 서식에 대한 확인을 제공합니다.
5. Azure 구독에 로그인하라는 메시지가 표시됩니다. 자격 증명을 제공 합니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    그런 다음, 도구가 디스크를 준비하고 백업 데이터 복사를 시작합니다. 제공된 디스크에 백업 데이터에 대한 충분한 공간이 없는 경우 도구에서 메시지를 표시할 때 추가 디스크에 연결해야 할 수 있습니다. <br/>

    도구가 성공적으로 실행되면 명령 프롬프트에 세 자기 정보가 제공됩니다.
   1. 제공한 디스크 하나 이상이 Azure에 발송되도록 준비됩니다. 
   2. 가져오기 작업이 만들어졌다는 확인 메시지가 나타납니다. 가져오기 작업에 사용자가 제공한 이름이 사용됩니다.
   3. 이 도구는 Azure 데이터 센터의 발송 주소를 표시합니다.

      ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/console2.png)<br/>

6. 명령 실행이 끝나면 발송 정보를 업데이트할 수 있습니다.

7. 디스크를 도구가 제공한 주소로 배송하고 나중에 참조할 수 있도록 추적 번호를 유지합니다.

   > [!IMPORTANT] 
   > 두 개의 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업 아래의 유틸리티에서 준비한 드라이브가 단일 패키지로 함께 배송되고 패키지에 대한 고유한 단일 추적 번호가 있는지 확인합니다. 별도의 Azure 가져오기 작업의 일부로 준비된 드라이브를 단일 패키지로 결합하지 마십시오.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure 가져오기 작업에 대한 발송 정보 업데이트

다음 절차는 Azure 가져오기 작업 발송 정보를 업데이트합니다. 이러한 정보에는 다음 항목에 대한 세부 정보가 포함됩니다.
* Azure에 디스크를 배달하는 운송 업체의 이름
* 디스크에 대한 반송 정보

1. Azure 구독에 로그인합니다.
2. 주 메뉴에서 **모든 서비스**를 클릭하고 모든 서비스 대화 상자에서 Import를 입력합니다. **가져오기/내보내기 작업**이 표시되면 클릭합니다.
    ![발송 정보 입력](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **가져오기/내보내기 작업** 메뉴 목록이 열리고 선택한 구독의 모든 가져오기/내보내기 작업 목록이 표시됩니다. 

3. 구독이 여러 개인 경우 백업 데이터를 가져오는 데 사용되는 구독을 선택해야 합니다. 그런 다음, 새로 만든 가져오기 작업을 선택하여 세부 정보를 엽니다.

    ![배송 정보 검토](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. 가져오기 작업의 설정 메뉴에서 **발송 정보 관리**를 클릭하고 반송 정보를 입력합니다.

    ![배송 정보 저장](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. 운송 회사에서 추적 번호를 받으면 Azure 가져오기 작업 개요 페이지에서 배너를 클릭하고 다음 세부 정보를 입력합니다.

   > [!IMPORTANT] 
   > Azure 가져오기 작업 생성 후 2주 이내에 운송 업체 정보 및 추적 번호가 업데이트되어야 합니다. 이 정보를 2주 이내에 확인하지 못하면 작업이 삭제되고 드라이브가 처리되지 않습니다.

   ![배송 정보 저장](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![배송 정보 저장](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>드라이브 처리 시간 
Azure 가져오기 작업을 처리하는 데 소요되는 총 시간은 배송 시간, 작업 유형, 복사되는 데이터 유형 및 크기, 제공된 디스크 크기와 같은 다양한 요소에 따라 다릅니다. Azure Import/Export 서비스에는 SLA가 없지만 이 서비스는 디스크가 수신되고 7~10일 내에 Azure 저장소 계정으로 백업 데이터 복사를 완료하려고 합니다. 

### <a name="monitoring-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링
Azure Portal에서 **가져오기/내보내기 작업** 페이지로 이동하여 작업을 선택하면 가져오기 작업의 상태를 모니터링할 수 있습니다. 가져오기 작업의 상태에 대한 자세한 내용은 [저장소 가져오기 내보내기 서비스](../storage/common/storage-import-export-service.md) 문서를 참조하세요.

### <a name="complete-the-workflow"></a>워크플로 완료
가져오기 작업이 성공적으로 완료되면 저장소 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약된 백업 시 Azure Backup은 아래와 같이 데이터의 콘텐츠를 저장소 계정에서 Recovery Services 자격 증명 모음으로 복사합니다. 

   ![Recovery Services 자격 증명 모음에 데이터 복사](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

다음 예약된 백업 시 Azure Backup은 증분 백업을 수행합니다.

### <a name="cleaning-up-resources"></a>리소스 정리
초기 백업이 완료되면 Azure Storage 컨테이너로 가져온 데이터와 준비 위치의 백업 데이터를 안전하게 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* Azure 가져오기/내보내기 워크플로에 대한 질문은 [Microsoft Azure Import/Export 서비스를 사용하여 Blob 저장소에 데이터 전송](../storage/common/storage-import-export-service.md)을 참조하세요.
* 워크플로에 대한 질문이 있으면 Azure Backup [FAQ](backup-azure-backup-faq.md) 의 오프라인 백업 섹션을 참조하세요.
