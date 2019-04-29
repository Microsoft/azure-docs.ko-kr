---
title: Azure Backup - DPM 및 Azure Backup Server에 대한 오프라인 백업
description: Azure Backup이 Azure Import/Export 서비스를 사용하여 네트워크를 통해 데이터를 보내는 방법에 대해 알아봅니다. 이 문서에서는 Azure 가져오기 내보내기 서비스를 사용한 초기 백업 데이터의 오프라인 시드 작업을 설명합니다.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 8d15eb03055aed32c8a99121b750ee5767a87b50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648047"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>DPM 및 Azure Backup Server에 대한 오프라인 백업 워크플로
Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 저장소 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 "전체" 백업은 일반적으로 많은 양의 데이터를 전송하며 델타/증분만 전송하는 후속 백업에 비해 네트워크 대역폭을 더 많이 요구합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시드의 프로세스를 통해 Azure Backup은 디스크를 사용하여 오프라인으로 압축된 초기 백업 데이터를 Azure에 업로드할 수 있습니다.

Azure Backup의 오프라인 시드 프로세스는 디스크를 사용하여 데이터를 Azure에 전송할 수 있는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md) 와 밀접하게 통합됩니다. 긴 대기 시간 및 낮은 대역폭 네트워크를 통해 전송해야 하는 TB(테라바이트)의 초기 백업 데이터가 있다면 오프라인 시드 워크플로를 사용하여 하나 이상의 하드 드라이브에 있는 초기 백업 복사본을 Azure 데이터 센터로 배송할 수 있습니다. 이 문서는 System Center DPM 및 Azure Backup Server에 대한 워크플로를 완료하는 개요 및 세부 단계를 제공합니다.

> [!NOTE]
> MARS(Microsoft Azure Recovery Services) 에이전트에 대한 오프라인 백업 프로세스는 System Center DPM 및 Azure Backup Server가 다릅니다. MARS 에이전트를 통한 오프라인 백업 사용에 대한 정보는 [이 문서](backup-azure-backup-import-export.md)를 참조하세요. 오프라인 백업은 Azure Backup 에이전트를 사용하여 수행된 시스템 상태 백업에 대해 지원되지 않습니다. 
>

## <a name="overview"></a>개요
Azure Backup의 오프라인 시드 기능 및 Azure Import/Export를 사용하면 디스크를 사용하여 오프라인으로 간단하게 Azure에 데이터를 업로드할 수 있습니다. 오프라인 백업 프로세스는 다음과 같은 단계를 포함합니다.

> [!div class="checklist"]
> * 백업 데이터가 네트워크를 통해 전송되는 대신 *스테이징 위치*에 기록됩니다.
> * 그런 다음, *스테이징 위치*의 데이터는 *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 하나 이상의 SATA 디스크에 기록됩니다.
> * Azure 가져오기 작업은 유틸리티에 의해 자동으로 만들어집니다.
> * SATA 드라이브는 가장 가까운 Azure 데이터 센터로 전송됩니다.
> * Azure에 백업 데이터 업로드를 완료한 후 Azure Backup이 백업 데이터를 백업 자격 증명 모음에 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성 
오프라인 백업은 온-프레미스에서 Microsoft 클라우드로 백업 데이터를 오프사이트하는 Azure Backup의 모든 배포 모델에 대해 지원됩니다. 다음 내용이 포함됩니다.

> [!div class="checklist"]
> * MARS(Microsoft Azure Recovery Services) 에이전트 또는 Azure Backup 에이전트를 사용하여 파일 및 폴더의 백업 
> * SC DPM(System Center Data Protection Manager)을 사용하여 모든 워크로드 및 파일의 백업 
> * Microsoft Azure Backup Server를 사용하여 모든 워크로드 및 파일의 백업 <br/>

## <a name="prerequisites"></a>필수 조건
오프라인 백업 워크플로를 시작하기 전에 다음 필수 구성 요소를 충족하는지 확인합니다.
* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)이 만들어졌습니다. 만들려면 [이 문서](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)의 단계를 참조하세요.
* Azure Backup 에이전트 또는 Azure Backup Server 또는 SC DPM이 해당되는 Windows Server/Windows 클라이언트에 설치되었고 컴퓨터가 Recovery Services 자격 증명 모음으로 등록됩니다. [최신 버전의 Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525)만 사용되는지 확인합니다. 
* 데이터를 백업하려는 컴퓨터에 [Azure 게시 설정 파일을 다운로드](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)합니다. 게시 설정 파일을 다운로드하는 구독은 Recovery Services 자격 증명 모음을 포함하는 구독과 다를 수 있습니다. 구독이 소버린 Azure 클라우드에 있는 경우 다음 링크를 적절하게 사용하여 Azure 게시 설정 파일을 다운로드합니다.

    | 소버린 클라우드 지역 | Azure 게시 설정 파일 링크 |
    | --- | --- |
    | 미국 | [링크](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 중국 | [링크](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* *클래식* 배포 모델이 있는 Azure Storage 계정이 아래와 같이 게시 설정 파일을 다운로드한 구독에 만들어졌습니다. 

  ![클래식 저장소 계정 만들기](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* 네트워크 공유 또는 컴퓨터의 추가 드라이브에 있을 수 있는, 초기 복사본을 저장할 충분한 디스크 공간이 있는 내부 또는 외부의 스테이징 위치가 생성됩니다. 예를 들어 500GB 파일 서버를 백업하려는 경우 준비 영역이 500GB인지 확인합니다. (압축으로 인해 더 작은 양이 사용됩니다.)
* Azure로 전송되는 디스크의 경우 2.5인치 SSD 또는, 2.5인치 또는 3.5인치 SATA II/III 내부 하드 드라이브가 사용되는지 확인합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)에서 서비스가 지원하는 최신 드라이브를 집합을 확인하세요.
* SATA 드라이브는 *스테이징 위치*에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. BitLocker가 ‘복사 컴퓨터’에서 사용하도록 설정되었는지 확인합니다. 

## <a name="workflow"></a>워크플로
이 섹션에 제공된 정보는 데이터를 Azure 데이터 센터에 배송하고 Azure Storage에 업로드할 수 있도록 오프라인 백업 워크플로를 완료하는 데 도움이 됩니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대한 질문이 있으면 앞에서 언급한 [가져오기 서비스 개요](../storage/common/storage-import-export-service.md)를 참조하세요.

### <a name="initiate-offline-backup"></a>오프라인 백업 시작
1. 백업을 예약할 때 다음 화면이 표시됩니다(Windows Server, Windows 클라이언트 또는 System Center Data Protection Manager에서).

    ![가져오기 화면](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    다음은 System Center Data Protection Manager의 해당 화면입니다. <br/>
    ![SC DPM 및 Azure Backup 서버 가져오기 화면](./media/backup-azure-backup-import-export/dpmoffline.png)

    입력에 대한 설명은 다음과 같습니다.

   * **스테이징 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 서로 다르면 스테이징 위치의 전체 네트워크 경로를 지정하는 것이 좋습니다.
   * **Azure 가져오기 작업 이름**: Azure 가져오기 서비스 및 Azure Backup이 디스크에서 Azure로 보낸 데이터의 전송을 추적하는 고유한 이름입니다.
   * **Azure 게시 설정**: 게시 설정 파일에 대한 로컬 경로를 제공합니다.
   * **Azure 구독 ID**: Azure 게시 설정 파일을 다운로드한 구독의 Azure 구독 ID입니다. 
   * **Azure Storage 계정**: Azure 게시 설정 파일과 연결된 Azure 구독의 스토리지 계정 이름입니다.
   * **Azure Storage 컨테이너**: 백업 데이터를 가져올 Azure Storage 계정의 대상 스토리지 Blob 이름입니다.

     디스크를 준비하는 데 필요하므로 제공한 *스테이징 위치* 및 *Azure 가져오기 작업 이름*을 저장합니다.  
     
2. 워크플로를 완료하고, 오프라인 백업 복사를 시작하려면 Azure Backup 에이전트 관리 콘솔에서 **지금 백업**을 클릭합니다. 초기 백업은 이 단계의 일환으로 준비 영역에 기록됩니다.

    ![지금 Backup](./media/backup-azure-backup-import-export/backupnow.png)

    System Center Data Protection Manager 또는 Azure Backup 서버에서 해당 워크플로를 완료하려면 **보호 그룹**을 마우스 오른쪽 단추로 클릭한 다음, **복구 지점 만들기** 옵션을 선택합니다. 그런 다음 **온라인 보호** 옵션을 선택합니다.

    ![SC DPM 및 Azure Backup 서버 지금 백업](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    작업이 완료되면 스테이징 위치를 디스크 준비에 사용할 수 있습니다.

    ![Backup 진행](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure에 제공
*AzureOfflineBackupDiskPrep* 유틸리티는 가장 가까운 Azure 데이터 센터에 전송되는 SATA 드라이브를 준비하는 데 사용됩니다. 이 유틸리티는 다음 경로에서 Recovery Services 에이전트의 설치 디렉터리에서 사용할 수 있습니다.

   *\Microsoft* *Azure* *Recovery* *Services* \*Agent\Utils\*

1. 디렉터리로 이동하고 준비할 SATA 드라이브가 연결될 복사 컴퓨터에 **AzureOfflineBackupDiskPrep** 디렉터리를 복사합니다. 복사 컴퓨터를 기준으로 다음 사항을 확인합니다.

   * 복사 컴퓨터는 **오프라인 백업 시작** 워크플로에 제공된 것과 같은 네트워크 경로를 사용하여 오프라인 시드 워크플로에 대해 제공된 스테이징 위치에 액세스할 수 있습니다.
   * BitLocker가 복사 컴퓨터에서 활성화됩니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다.

     필요한 경우 복사 컴퓨터는 원본 컴퓨터와 같을 수 있습니다. 
    
     > [!IMPORTANT] 
     > 원본 컴퓨터가 가상 머신인 경우 복사 컴퓨터로 다른 실제 서버 또는 클라이언트 컴퓨터를 사용해야 합니다.
    
    
2. *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 관리자 권한 명령 프롬프트를 복사 컴퓨터에서 열고 다음 명령을 실행합니다.

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*스테이징 위치 경로*&gt; |**오프라인 백업 시작** 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용되는 필수 입력입니다. |
    | p:&lt;*PublishSettingsFile에 대한 경로*&gt; |**오프라인 백업 시작** 워크플로에 입력한 **Azure 게시 설정** 파일에 대한 경로를 제공하는 데 사용되는 옵션 입력입니다. |

    > [!NOTE]
    > 복사 컴퓨터 및 원본 컴퓨터가 서로 다른 경우 &lt;AzurePublishSettingFile에 대한 경로&gt; 값은 필수입니다.
    >
    >

    명령을 실행할 때 유틸리티에서 준비해야 할 드라이브에 해당하는 Azure 가져오기 작업의 선택을 요청합니다. 제공된 스테이징 위치와 연결된 가져오기 작업이 하나만 있는 경우 다음과 같은 화면이 나타납니다.

    ![Azure 디스크 준비 도구 입력](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Azure에 전송하도록 준비하려는 탑재된 디스크에 대한 드라이브 문자를 후행 콜론 없이 입력합니다. 메시지가 표시될 때 드라이브의 서식에 대한 확인을 제공합니다.

    그런 다음, 도구가 디스크를 준비하고 백업 데이터 복사를 시작합니다. 제공된 디스크에 백업 데이터에 대한 충분한 공간이 없는 경우 도구에서 메시지를 표시할 때 추가 디스크에 연결해야 할 수 있습니다. <br/>

    도구의 성공적인 실행의 끝에 제공한 하나 이상의 디스크가 Azure에 전달하도록 준비됩니다. 또한 **오프라인 백업 시작** 워크플로 중에 제공한 이름의 가져오기 작업이 Azure에서 생성됩니다. 마지막으로 도구는 디스크를 배송해야 하는 Azure 데이터 센터에 대한 배송 주소를 표시합니다.

    ![Azure 디스크 준비 완료](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. 명령 실행의 끝에 아래와 같이 배송 정보를 업데이트하는 옵션이 표시됩니다.

    ![배송 정보 업데이트 옵션](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. 세부 정보를 바로 입력할 수 있습니다. 이 도구는 일련의 입력을 포함하는 프로세스를 안내합니다. 그러나 추적 번호와 같은 정보 또는 배송과 관련된 세부 정보가 없는 경우 세션을 종료할 수 있습니다. 배송 세부 정보를 나중에 업데이트하는 단계는 이 문서에서 제공됩니다. 

6. 디스크를 도구가 제공한 주소로 배송하고 나중에 참조할 수 있도록 추적 번호를 유지합니다.

   > [!IMPORTANT] 
   > 두 개의 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업 아래의 유틸리티에서 준비한 드라이브가 단일 패키지로 함께 배송되고 패키지에 대한 고유한 단일 추적 번호가 있는지 확인합니다. 준비된 드라이브를 단일 패키지에서 **다른** Azure 가져오기 작업의 일부로 결합하지 마십시오. 

5. 추적 번호 정보가 있는 경우 가져오기 작업 완료를 대기 중인 원본 컴퓨터로 이동하고, *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 관리자 권한 명령 프롬프트에서 다음 명령을 실행합니다. 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   *AzureOfflineBackupDiskPrep* 유틸리티 디렉터리를 현재 디렉터리로 하여 *복사 컴퓨터*와 같은 다른 컴퓨터에서 다음 명령을 필요에 따라 실행할 수 있습니다.
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 매개 변수 | 설명 |
    | --- | --- |
    | u: | Azure 가져오기 작업에 대한 배송 세부 정보를 업데이트하는 데 사용되는 필수 입력 |
    | s:&lt;*스테이징 위치 경로*&gt; | 명령이 원본 컴퓨터에서 실행되지 않는 경우 필수 입력입니다. **오프라인 백업 시작** 워크플로에 입력한 스테이징 위치에 대한 경로를 제공하는 데 사용됩니다. |
    | p:&lt;*PublishSettingsFile에 대한 경로*&gt; | 명령이 원본 컴퓨터에서 실행되지 않는 경우 필수 입력입니다. **오프라인 백업 시작** 워크플로에 입력한 **Azure 게시 설정** 파일에 대한 경로를 제공하는 데 사용됩니다. |
    
    유틸리티는 원본 컴퓨터가 기다리는 가져오기 작업 또는 명령이 다른 컴퓨터에서 실행되는 경우 스테이징 위치와 관련된 가져오기 작업을 자동으로 검색합니다. 그런 다음, 아래와 같이 일련의 입력을 통해 배송 정보를 업데이트하는 옵션을 제공합니다. 
    
    ![배송 정보 입력](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. 모든 입력이 제공되면 세부 정보를 주의 깊게 검토하고, *예*를 입력하여 제공한 배송 정보를 커밋합니다. 

    ![배송 정보 검토](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. 배송 정보를 성공적으로 업데이트하면 유틸리티는 아래와 같이 사용자가 입력한 배송 세부 정보가 저장되는 로컬 위치를 제공합니다. 

    ![배송 정보 저장](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > *AzureOfflineBackupDiskPrep* 유틸리티를 사용하여 배송 정보를 제공하고 2주 내에 드라이브가 Azure 데이터 센터에 도달하는지 확인합니다. 이렇게 하지 않으면 드라이브가 처리되지 않을 수 있습니다.  

위의 단계를 완료하면 Azure 데이터 센터는 드라이브를 받고 백업 데이터를 드라이브에서 사용자가 만든 기본 형식 Azure 저장소 계정에 전송하도록 처리할 준비가 됩니다. 

### <a name="time-to-process-the-drives"></a>드라이브 처리 시간 
Azure 가져오기 작업을 처리하는 데 소요되는 총 시간은 배송 시간, 작업 유형, 복사되는 데이터 유형 및 크기, 제공된 디스크 크기와 같은 다양한 요소에 따라 다릅니다. Azure Import/Export 서비스에는 SLA가 없지만 이 서비스는 디스크가 수신되고 7~10일 내에 Azure 저장소 계정으로 백업 데이터 복사를 완료하려고 합니다. 다음 섹션은 Azure 가져오기 작업의 상태를 모니터링하는 방법에 대해 자세히 설명합니다. 

### <a name="monitoring-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링
드라이브가 전송 중이거나 저장소 계정으로 복사될 Azure 데이터 센터에 있는 동안 Azure Backup 에이전트 또는 SC DPM 또는 원본 컴퓨터의 Azure Backup 서버 콘솔은 예약된 백업에 대한 다음 작업 상태를 나타냅니다. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

가져오기 작업 상태를 확인하려면 다음 단계를 수행합니다. 
1. 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다.
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  출력은 아래와 같이 가져오기 작업의 현재 상태를 표시합니다. 

    ![가져오기 작업 상태 확인](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Azure 가져오기 작업의 다양한 상태에 대한 자세한 내용은 [이 문서](../storage/common/storage-import-export-view-drive-status.md)를 참조하세요.

### <a name="complete-the-workflow"></a>워크플로 완료
가져오기 작업이 완료된 후 사용자의 저장소 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약된 백업 시 Azure 백업은 아래와 같이 데이터의 콘텐츠를 저장소 계정에서 Recovery Services 자격 증명 모음으로 복사합니다. 

   ![Recovery Services 자격 증명 모음에 데이터 복사](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

다음 예약된 백업 시 Azure Backup은 초기 백업 복사본을 통해 증분 백업을 수행합니다.

## <a name="next-steps"></a>다음 단계
* Azure 가져오기/내보내기 워크플로에 대한 질문은 [Microsoft Azure Import/Export 서비스를 사용하여 Blob 저장소에 데이터 전송](../storage/common/storage-import-export-service.md)을 참조하세요.
* 워크플로에 대한 질문이 있으면 Azure Backup [FAQ](backup-azure-backup-faq.md) 의 오프라인 백업 섹션을 참조하세요.
