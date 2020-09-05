---
title: Azure Import/Export 서비스를 사용한 초기값 오프 라인 백업
description: Azure Backup를 사용 하 여 Azure Import/Export 서비스를 통해 네트워크에서 데이터를 전송 하는 방법을 알아봅니다. 이 문서에서는 Azure Import/Export 서비스를 사용 하 여 초기 백업 데이터의 오프 라인 시드에 대해 설명 합니다.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: f3cf44a34babab79d135923db040630a1c8e3dfe
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892017"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure Backup의 오프라인 백업 워크플로

Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 스토리지 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하고 델타/증분만 전송하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요합니다. 오프라인 시드 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인 백업 데이터를 Azure에 업로드할 수 있습니다.

Azure Backup 오프 라인 시드 프로세스는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md)와 긴밀 하 게 통합 됩니다. 디스크를 사용 하 여 초기 백업 데이터를 Azure로 전송 하는 데이 서비스를 사용할 수 있습니다. 대기 시간이 짧고 대역폭이 낮은 네트워크를 통해 전송 해야 하는 테라바이트 (테라바이트)의 초기 백업 데이터를 사용 하는 경우 오프 라인 시드 워크플로를 사용 하 여 Azure 데이터 센터에 하나 이상의 하드 드라이브에 초기 백업 복사본을 제공할 수 있습니다. 다음 이미지는 워크플로의 단계에 대한 개요를 제공합니다.

  ![오프 라인 가져오기 워크플로 프로세스 개요](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

오프 라인 백업 프로세스에는 다음 단계가 포함 됩니다.

1. 네트워크를 통해 백업 데이터를 보내는 대신 준비 위치에 백업 데이터를 기록합니다.
1. *Azureofflinebackupdiskprep* 유틸리티를 사용 하 여 준비 위치의 데이터를 하나 이상의 SATA 디스크에 씁니다.
1. Azure 가져오기 작업의 일부로 *Azureofflinebackupdiskprep* 유틸리티는 Azure 가져오기 작업을 만듭니다. SATA 드라이브를 가장 가까운 Azure 데이터 센터로 보내고 가져오기 작업을 참조하여 활동을 연결합니다.
1. Azure 데이터 센터에서 디스크의 데이터는 Azure Storage 계정에 복사됩니다.
1. Azure Backup은 스토리지 계정의 백업 데이터를 Recovery Services 자격 증명 모음으로 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성

다음 Azure Backup 기능 또는 워크 로드는에 대 한 오프 라인 백업의 사용을 지원 합니다.

> [!div class="checklist"]
>
> * Microsoft Azure Recovery Services (MARS) 에이전트를 사용 하 여 파일 및 폴더를 백업 합니다 (Azure Backup 에이전트 라고도 함).
> * System Center Data Protection Manager (DPM)을 사용 하 여 모든 워크 로드 및 파일의 백업
> * Microsoft Azure Backup Server를 사용 하 여 모든 워크 로드 및 파일의 백업

   > [!NOTE]
   > Azure Backup 에이전트를 사용 하 여 수행 된 시스템 상태 백업에는 오프 라인 백업이 지원 되지 않습니다.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>전제 조건

  > [!NOTE]
  > 다음 필수 구성 요소 및 워크플로는 [최신 Azure Recovery Services 에이전트](https://aka.ms/azurebackup_agent)를 사용 하 여 파일 및 폴더의 오프 라인 백업에만 적용 됩니다. System Center DPM 또는 Azure Backup Server를 사용 하 여 작업에 대 한 오프 라인 백업을 수행 하려면 [dpm 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로](backup-azure-backup-server-import-export.md)를 참조 하세요.

오프 라인 백업 워크플로를 시작 하기 전에 다음 필수 구성 요소를 완료 합니다.

* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)을 만듭니다. 자격 증명 모음을 만들려면 [Recovery Services 자격 증명 모음 만들기](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)의 단계를 수행 합니다.
* [최신 버전의 Azure Backup 에이전트가](https://aka.ms/azurebackup_agent) windows Server 또는 windows 클라이언트에 설치 되어 있는지 확인 하 고 (해당 하는 경우), 컴퓨터가 Recovery Services 자격 증명 모음에 등록 되어 있는지 확인 합니다.
* Azure PowerShell 3.7.0는 Azure Backup 에이전트를 실행 하는 컴퓨터에 필요 합니다. [Azure PowerShell 3.7.0 버전](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)을 다운로드 하 여 설치 합니다.
* Azure Backup 에이전트를 실행 하는 컴퓨터에서 Microsoft Edge 또는 Internet Explorer 11이 설치 되어 있고 JavaScript가 사용 하도록 설정 되어 있는지 확인 합니다.
* Recovery Services 자격 증명 모음과 동일한 구독에 Azure storage 계정을 만듭니다.
* Azure Active Directory 애플리케이션을 만드는 데 [필요한 권한](../active-directory/develop/howto-create-service-principal-portal.md)이 있는지 확인합니다. 오프 라인 백업 워크플로는 Azure 저장소 계정과 연결 된 구독에 Azure Active Directory 응용 프로그램을 만듭니다. 응용 프로그램의 목표는 오프 라인 백업 워크플로에 필요한 Azure Import/Export 서비스에 대 한 보안 및 범위 액세스를 Azure Backup 제공 하는 것입니다.
* Azure storage 계정이 포함 된 구독과 함께 *Microsoft ImportExport* 리소스 공급자를 등록 합니다. 리소스 공급자를 등록하려면 다음 단계를 따릅니다.
    1. 주 메뉴에서 **구독**을 선택 합니다.
    1. 여러 구독을 구독 하는 경우 오프 라인 백업에 사용 하려는 구독을 선택 합니다. 구독을 하나만 사용하는 경우 해당 구독이 표시됩니다.
    1. 구독 메뉴에서 **리소스 공급자** 를 선택 하 여 공급자 목록을 볼 수 있습니다.
    1. 공급자 목록에서 *Microsoft. ImportExport*로 스크롤합니다. **상태가** **notregistered**인 경우 **등록**을 선택 합니다.

        ![리소스 공급자 등록](./media/backup-azure-backup-import-export/registerimportexport.png)

* 초기 복사본을 저장하기 위한 충분한 디스크 공간의 컴퓨터 내부 또는 외부에 네트워크 공유 또는 추가 드라이브일 수 있는 준비 위치가 생성됩니다. 예를 들어 500-GB 파일 서버를 백업 하려는 경우 준비 영역이 500 GB 이상 인지 확인 합니다. (압축으로 인해 더 적은 양이 사용됩니다.)
* Azure로 디스크를 전송 하는 경우 2.5 인치 SSD 또는 2.5 인치 또는 3.5 인치 SATA II/III 내부 하드 드라이브만 사용 합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. 서비스에서 지원하는 최신 드라이브 집합의 경우 [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)를 확인하세요.
* SATA 드라이브는 준비 위치에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. 복사 컴퓨터에서 BitLocker가 사용 하도록 설정 되어 있는지 확인 합니다.

## <a name="workflow"></a>워크플로

이 섹션에서는 데이터를 Azure 데이터 센터에 배달 하 고 Azure Storage에 업로드할 수 있도록 오프 라인 백업 워크플로에 대해 설명 합니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대 한 질문이 있는 경우 [Azure import/Export 서비스 개요 설명서](../storage/common/storage-import-export-service.md)를 참조 하세요.

## <a name="initiate-offline-backup"></a>오프라인 백업 시작

1. Recovery Services 에이전트에서 백업을 예약할 때이 페이지가 표시 됩니다.

    ![페이지 가져오기](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. **내 디스크를 사용 하 여 전송**옵션을 선택 합니다.

    > [!NOTE]
    > Azure Data Box 옵션을 사용 하 여 초기 백업 데이터를 오프 라인으로 전송 합니다. 이 옵션은 Azure 호환 디스크를 확보 하는 데 필요한 노력을 절감 합니다. Recovery Services 에이전트에서 백업 데이터를 직접 쓸 수 있는 Microsoft 독점, 안전 및 변조 방지 Azure Data Box 장치를 제공 합니다.

1. **다음**을 선택 하 고 상자를 신중 하 게 입력 합니다.

    ![디스크 세부 정보를 입력 하세요.](./media/backup-azure-backup-import-export/your-disk-details.png)

   입력 하는 상자는 다음과 같습니다.

    * **준비 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 다른 경우 스테이징 위치의 전체 네트워크 경로를 지정 합니다.
    * **Azure Resource Manager 스토리지 계정**: Azure 구독의 Resource Manager 유형 스토리지 계정(범용 v1 또는 범용 v2) 이름입니다.
    * **Azure Storage 컨테이너**: Azure 저장소 계정에서 Recovery Services 자격 증명 모음에 복사 하기 전에 가져온 백업 데이터를 가져오는 대상 blob 저장소 컨테이너의 이름입니다.
    * **Azure 구독 ID**: Azure Storage 계정이 생성되는 Azure 구독의 ID입니다.
    * **Azure 가져오기 작업 이름**: azure Import/Export 서비스와 Azure Backup에서 azure로 전송 되는 데이터의 전송을 추적 하는 고유 이름입니다.
  
   상자를 입력 한 후 **다음**을 선택 합니다. **스테이징 위치** 와 **Azure 가져오기 작업 이름** 정보를 저장 합니다. 디스크를 준비 하는 데 필요 합니다.

1. 메시지가 표시 되 면 Azure 구독에 로그인 합니다. Azure Backup Azure Active Directory 응용 프로그램을 만들 수 있도록 로그인 해야 합니다. Azure Import/Export 서비스에 액세스 하는 데 필요한 권한을 입력 합니다.

    ![Azure 구독 로그인 페이지](./media/backup-azure-backup-import-export/azure-login.png)

1. 워크플로를 완료 합니다. Azure Backup 에이전트 콘솔에서 **지금 백업**을 선택 합니다.

    ![지금 백업](./media/backup-azure-backup-import-export/backupnow.png)

1. 마법사의 **확인** 페이지에서 **백업**을 선택 합니다. 초기 백업은 설정의 일환으로 준비 영역에 기록됩니다.

   ![지금 백업할 준비가 되었는지 확인 합니다.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    작업이 완료되면 준비 위치를 디스크 준비에 사용할 수 있습니다.

   ![지금 백업 마법사 페이지](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure로 배송

*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터로 전송되는 SATA 드라이브를 준비합니다. 이 유틸리티는 다음 경로에 있는 Azure Backup 에이전트 설치 디렉터리에서 사용할 수 있습니다.

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. 디렉터리로 이동 하 여 *Azureofflinebackupdiskprep* 디렉터리를 SATA 드라이브가 연결 된 다른 컴퓨터로 복사 합니다. SATA 드라이브가 연결 된 컴퓨터에서 다음을 확인 합니다.

   * 복사 컴퓨터는 "오프 라인 백업 시작" 섹션의 워크플로에 제공 된 것과 동일한 네트워크 경로를 사용 하 여 오프 라인 시드 워크플로의 준비 위치에 액세스할 수 있습니다.
   * 복사 컴퓨터에서 BitLocker가 사용 설정되어 있습니다.
   * Azure PowerShell 3.7.0이 설치되었습니다.
   * 최신 호환 브라우저 (Microsoft Edge 또는 Internet Explorer 11)가 설치 되어 있고 JavaScript가 사용 하도록 설정 되어 있습니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 동일할 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 머신인 경우 복사 컴퓨터는 원본 컴퓨터와 다른 실제 서버 또는 클라이언트 컴퓨터여야 합니다.

1. *Azureofflinebackupdiskprep* 유틸리티 디렉터리를 현재 디렉터리로 사용 하 여 복사 컴퓨터에서 관리자 권한 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |이 필수 입력은 "오프 라인 백업 시작" 섹션의 워크플로에 입력 한 스테이징 위치에 대 한 경로를 제공 하는 데 사용 됩니다. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |이 선택적 입력은 Azure 게시 설정 파일에 대 한 경로를 제공 하는 데 사용 됩니다.  |

    명령을 실행 하면 유틸리티가 준비 해야 하는 드라이브에 해당 하는 Azure 가져오기 작업의 선택을 요청 합니다. 제공 된 준비 위치와 연결 된 가져오기 작업이 하나만 있는 경우 다음과 같은 페이지가 표시 됩니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Azure로 전송하기 위해 준비하려는 탑재된 디스크에 대해 후행 콜론 없이 드라이브 문자를 입력합니다.
1. 메시지가 표시되면 드라이브 형식을 확인합니다.
1. Azure 구독에 로그인 하 라는 메시지가 표시 됩니다. 자격 증명을 입력합니다.

    ![Azure 구독 로그인](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    그런 다음 도구는 디스크를 준비 하 고 백업 데이터를 복사 하기 시작 합니다. 제공 된 디스크에 백업 데이터에 대 한 충분 한 공간이 없는 경우 도구에서 메시지가 표시 되 면 추가 디스크를 연결 해야 할 수 있습니다. <br/>

    도구를 성공적으로 실행한 후 명령 프롬프트에서 다음 세 가지 정보를 제공합니다.

   1. 제공한 하나 이상의 디스크가 Azure로 배송될 준비가 되었습니다.
   1. 가져오기 작업이 생성 되었다는 확인 메시지가 표시 됩니다. 가져오기 작업에 사용자가 제공한 이름이 사용됩니다.
   1. 이 도구는 Azure 데이터 센터에 대한 배송 주소를 표시합니다.

      ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 명령 실행이 끝나면 배송 정보를 업데이트할 수 있습니다.

1. 도구가 제공 하는 주소로 디스크를 배송 합니다. 나중에 참조할 수 있도록 추적 번호를 유지 합니다.

   > [!IMPORTANT]
   > 두 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업에서 유틸리티에 의해 준비 된 드라이브가 단일 패키지로 함께 배송 되 고 패키지에 대 한 단일 고유 추적 번호가 있는지 확인 합니다. 별도의 Azure 가져오기 작업의 일부로 준비 된 드라이브를 단일 패키지로 결합 하지 마세요.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Azure 가져오기 작업의 배송 세부 정보 업데이트

다음 절차에서는 Azure 가져오기 작업 전달 세부 정보를 업데이트 합니다. 이 정보에는 다음에 대한 세부 정보가 포함됩니다.

* Azure에 디스크를 배달 하는 운송 업체의 이름입니다.
* 디스크에 대 한 배송 정보를 반환 합니다.

1. Azure 구독에 로그인합니다.
1. 주 메뉴에서 **모든 서비스**를 선택 합니다. **모든 서비스** 대화 상자에서 **가져오기**를 입력 합니다. **가져오기/내보내기 작업이**표시 되 면 해당 작업을 선택 합니다.

    ![배송 정보 입력](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    **가져오기/내보내기 작업** 메뉴가 열리고 선택한 구독의 모든 가져오기/내보내기 작업 목록이 표시 됩니다.

1. 구독이 여러 개인 경우 백업 데이터를 가져오는 데 사용 되는 구독을 선택 합니다. 그런 다음 새로 만든 가져오기 작업을 선택 하 여 세부 정보를 엽니다.

    ![배송 정보 검토](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 가져오기 작업의 **설정** 메뉴에서 **배송 정보 관리**를 선택 합니다. 반환 배송 세부 정보를 입력 합니다.

    ![배송 정보 저장](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 배송 운송 업체의 추적 번호가 있으면 Azure 가져오기 작업 개요 페이지에서 배너를 선택 하 고 다음 세부 정보를 입력 합니다.

   > [!IMPORTANT]
   > Azure Import 작업 생성 후 2주 이내에 운송업체 정보 및 추적 번호가 업데이트되었는지 확인합니다. 2 주 이내에이 정보를 확인 하지 못하면 작업이 삭제 되 고 드라이브가 처리 되지 않을 수 있습니다.

   ![추적 정보 업데이트 경고](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![캐리어 정보 및 추적 번호](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>드라이브를 처리하는 시간

Azure 가져오기 작업을 처리 하는 데 걸리는 시간은 다양 합니다. 프로세스 시간은 배송 시간, 작업 유형, 복사 되는 데이터의 유형 및 크기, 제공 된 디스크 크기 등의 요소를 기반으로 합니다. Azure Import/Export 서비스에는 SLA가 없습니다. 디스크가 수신 되 면 서비스는 Azure storage 계정에 대해 7 ~ 10 일 내에 백업 데이터 복사를 완료 하는 데 노력 합니다.

### <a name="monitor-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

Azure Portal에서 가져오기 작업의 상태를 모니터링할 수 있습니다. **가져오기/내보내기 작업** 페이지로 이동 하 여 작업을 선택 합니다. 가져오기 작업의 상태에 대 한 자세한 내용은 [Azure import/Export 서비스 란?](../storage/common/storage-import-export-service.md)을 참조 하세요.

### <a name="finish-the-workflow"></a>워크플로 완료

가져오기 작업이 성공적으로 완료되면 스토리지 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약 된 백업 시 Azure Backup는 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터의 내용을 복사 합니다.

   ![Recovery Services 자격 증명 모음에 데이터 복사](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

다음 예약된 백업 시 Azure Backup은 증분 백업을 수행합니다.

### <a name="clean-up-resources"></a>리소스 정리

초기 백업이 완료 된 후 Azure Storage 컨테이너와 준비 위치의 백업 데이터로 가져온 데이터를 안전 하 게 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Import/Export 서비스 워크플로에 대 한 질문은 [Microsoft Azure Import/Export 서비스를 사용 하 여 Blob storage로 데이터 전송](../storage/common/storage-import-export-service.md)을 참조 하세요.
