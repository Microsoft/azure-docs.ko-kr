---
title: Data Protection Manager (DPM) 및 MABS (Microsoft Azure Backup 서버)-이전 버전에 대 한 오프 라인 백업
description: Azure Backup를 사용 하면 Azure Import/Export 서비스를 사용 하 여 네트워크에서 데이터를 보낼 수 있습니다. 이 문서에서는 이전 버전의 DPM 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b747fd3c682dc1caf7312ba7279470a1e6b38bd5
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88890096"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로 (이전 버전)

>[!IMPORTANT]
>이러한 단계는 DPM 2019 RTM 및 이전 버전 및 MABS v3 RTM 및 이전 버전에 적용 됩니다.

Azure Backup은 데이터를 Azure에 처음 전체 백업하는 동안 네트워크 및 스토리지 비용을 절약하는 여러 가지 기본 제공 효율성 향상 기능이 있습니다. 초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하고 델타/증분만 전송하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요합니다. Azure Backup은 초기 백업을 압축합니다. 오프라인 시드 프로세스를 통해 Azure Backup은 디스크를 사용하여 압축된 초기 백업 데이터를 Azure에 오프라인으로 업로드할 수 있습니다.

Azure Backup의 오프 라인 시드 프로세스는 [Azure Import/Export 서비스](../storage/common/storage-import-export-service.md)와 긴밀 하 게 통합 됩니다. 디스크를 사용 하 여 Azure로 데이터를 전송 하는 데이 서비스를 사용할 수 있습니다. 대기 시간이 짧고 대역폭이 높은 네트워크를 통해 전송 해야 하는 테라바이트 (테라바이트)의 초기 백업 데이터가 있는 경우 오프 라인 시드 워크플로를 사용 하 여 하나 이상의 하드 드라이브에 초기 백업 복사본을 Azure 데이터 센터에 제공할 수 있습니다. 이 문서에서는 System Center Data Protection Manager (DPM) 및 Microsoft Azure Backup 서버 (MABS)에 대해이 워크플로를 완료 하는 개요와 추가 단계를 제공 합니다.

> [!NOTE]
> MARS (Microsoft Azure Recovery Services) 에이전트에 대 한 오프 라인 백업 프로세스는 DPM 및 MABS와는 다릅니다. MARS 에이전트를 사용 하 여 오프 라인 백업을 사용 하는 방법에 대 한 자세한 내용은 [Azure Backup의 오프 라인 백업 워크플로](backup-azure-backup-import-export.md)를 참조 하세요. Azure Backup 에이전트를 사용 하 여 수행 된 시스템 상태 백업에는 오프 라인 백업이 지원 되지 않습니다.
>

## <a name="overview"></a>개요

Azure Backup 및 Azure Import/Export 서비스의 오프 라인 시드 기능을 사용 하면 디스크를 사용 하 여 오프 라인으로 데이터를 Azure에 업로드 하는 것이 간단 합니다. 오프라인 백업 프로세스는 다음과 같은 단계를 포함합니다.

> [!div class="checklist"]
>
> * 백업 데이터는 네트워크를 통해 전송 되지 않고 스테이징 위치에 기록 됩니다.
> * 그런 다음, 준비 위치의 데이터는 *Azureofflinebackupdiskprep* 유틸리티를 사용 하 여 하나 이상의 SATA 디스크에 기록 됩니다.
> * Azure 가져오기 작업은 유틸리티에 의해 자동으로 생성 됩니다.
> * 그런 다음 SATA 드라이브가 가장 가까운 Azure 데이터 센터로 전송 됩니다.
> * Azure에 백업 데이터를 업로드하는 작업이 완료되면 Azure Backup에서 백업 데이터를 백업 자격 증명 모음에 복사하고 증분 백업이 예약됩니다.

## <a name="supported-configurations"></a>지원되는 구성

오프 라인 백업은 온-프레미스에서 Microsoft 클라우드로 데이터를 백업 하는 Azure Backup의 모든 배포 모델에 대해 지원 됩니다. 이러한 모델은 다음과 같습니다.

> [!div class="checklist"]
>
> * MARS 에이전트 또는 Azure Backup 에이전트를 사용 하 여 파일 및 폴더를 백업 합니다.
> * DPM을 사용 하 여 모든 워크 로드 및 파일의 백업
> * MABS를 사용 하 여 모든 워크 로드 및 파일의 백업

>[!NOTE]
>Azure CSP 구독은 DPM 2019 RTM 및 이전 버전, MABS v3 RTM 및 이전 버전에 대 한 오프 라인 시드에 사용 하도록 지원 되지 않습니다. 네트워크를 통한 온라인 백업은 계속 지원 됩니다.

## <a name="prerequisites"></a>전제 조건

오프 라인 백업 워크플로를 시작 하기 전에 다음 필수 구성 요소가 충족 되는지 확인 합니다.

* [Recovery Services 자격 증명 모음](backup-azure-recovery-services-vault-overview.md)이 생성되었습니다. 하나를 만들려면 [Recovery Services 자격 증명 모음 만들기](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault) 의 단계를 따르세요.
* 해당 하는 Windows Server 또는 Windows 클라이언트에 Azure Backup 에이전트 또는 MABS 또는 DPM이 설치 되어 있고 해당 컴퓨터가 Recovery Services 자격 증명 모음에 등록 되어 있습니다. [최신 버전의 Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525)만 사용되는지 확인합니다.
* 데이터를 백업 하려는 컴퓨터에 [Azure 게시 설정 파일을 다운로드](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) 합니다. 게시 설정 파일을 다운로드 하는 구독은 Recovery Services 자격 증명 모음이 포함 된 구독과 다를 수 있습니다. 구독이 소 버린 Azure 클라우드에 있는 경우 다음 링크를 적절 하 게 사용 하 여 Azure 게시 설정 파일을 다운로드 합니다.

    | 소버린 클라우드 지역 | Azure 게시 설정 파일 링크 |
    | --- | --- |
    | 미국 | [링크](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 중국 | [링크](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 게시 설정 파일을 다운로드 한 구독에서 리소스 관리자 배포 모델을 사용 하는 Azure storage 계정을 만들었습니다. 저장소 계정에서 대상으로 사용 될 새 blob 컨테이너를 만듭니다.

  ![리소스 관리자 개발을 사용 하 여 저장소 계정 만들기](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* 초기 복사본을 저장하기 위한 충분한 디스크 공간의 컴퓨터 내부 또는 외부에 네트워크 공유 또는 추가 드라이브일 수 있는 준비 위치가 생성됩니다. 예를 들어 500-GB 파일 서버를 백업 하려는 경우 준비 영역이 500 GB 이상 인지 확인 합니다. (압축으로 인해 더 적은 양이 사용됩니다.)
* Azure로 전송 되는 디스크의 경우 2.5 인치 SSD 또는 2.5 인치 또는 3.5 인치 SATA II/III 내부 하드 드라이브만 사용 해야 합니다. 최대 10TB의 하드 드라이브를 사용할 수 있습니다. 서비스에서 지원하는 최신 드라이브 집합의 경우 [Azure Import/Export 서비스 설명서](../storage/common/storage-import-export-requirements.md#supported-hardware)를 확인하세요.
* SATA 드라이브는 준비 위치에서 SATA 드라이브로 백업 데이터의 복사가 수행되는 컴퓨터(*복사 컴퓨터*라고 함)에 연결되어야 합니다. 복사 컴퓨터에서 BitLocker가 사용 하도록 설정 되어 있는지 확인 합니다.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>오프 라인 백업 프로세스를 위한 서버 준비

>[!NOTE]
> MARS 에이전트를 설치 하는 동안 *AzureOfflineBackupCertGen.exe*와 같은 나열 된 유틸리티를 찾을 수 없는 경우에 액세스 하 여 AskAzureBackupTeam@microsoft.com 액세스 권한을 얻습니다.

* 서버에서 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행 합니다.

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    이 도구는 Azure Offline Backup Active Directory 응용 프로그램을 만듭니다 (없는 경우).

    응용 프로그램이 이미 있는 경우이 실행 파일은 테 넌 트에서 응용 프로그램에 인증서를 수동으로 업로드 하 라는 메시지를 표시 합니다. 응용 프로그램에 인증서를 수동으로 업로드 하려면 [이 섹션](#manually-upload-an-offline-backup-certificate) 의 단계를 따르세요.

* *AzureOfflineBackupCertGen.exe* 도구는 *OfflineApplicationParams.xml* 파일을 생성 합니다. MABS 또는 DPM을 사용 하 여이 파일을 서버에 복사 합니다.
* DPM 인스턴스나 Azure Backup 서버에 [최신 MARS 에이전트](https://aka.ms/azurebackup_agent) 를 설치 합니다.
* Azure에 서버를 등록 합니다.
* 다음 명령을 실행합니다.

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* 이전 명령은 파일을 만듭니다 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>오프 라인 백업 인증서 수동 업로드

오프 라인 백업에 대해 이전에 만든 Azure Active Directory 응용 프로그램에 오프 라인 백업 인증서를 수동으로 업로드 하려면 다음 단계를 수행 합니다.

1. Azure Portal에 로그인합니다.
1. **Azure Active Directory**  >  **앱 등록**로 이동 합니다.
1. 소유 하는 **응용 프로그램** 탭에서 표시 이름 형식의 응용 프로그램을 찾습니다 `AzureOfflineBackup _<Azure User Id` .

    ![소유 응용 프로그램 탭에서 응용 프로그램 찾기](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. 애플리케이션을 선택합니다. 왼쪽 창의 **관리** 에서 **인증서 & 암호**로 이동 합니다.
1. 기존 인증서 나 공개 키를 확인 합니다. 없는 경우 응용 프로그램의 **개요** 페이지에서 **삭제** 단추를 선택 하 여 응용 프로그램을 안전 하 게 삭제할 수 있습니다. 그런 다음 [오프 라인 백업 프로세스를 위해 서버를 준비](#prepare-the-server-for-the-offline-backup-process) 하는 단계를 다시 시도 하 고 다음 단계를 건너뛸 수 있습니다. 그렇지 않으면 오프 라인 백업을 구성 하려는 DPM 인스턴스나 Azure Backup 서버에서 다음 단계를 계속 수행 합니다.
1. **시작** – **실행**에서 *를 입력 합니다.* **인증서-로컬 컴퓨터** 창에서 **인증서-로컬 컴퓨터**  >  **개인** 탭을 선택 합니다. 이름이 인 인증서를 찾습니다. `CB_AzureADCertforOfflineSeeding_<ResourceId>`
1. 인증서를 선택 하 고 **모든 작업**을 마우스 오른쪽 단추로 클릭 한 다음, 개인 키가 없는 .cer 형식으로 **내보내기**를 선택 합니다.
1. Azure Portal에서 Azure offline backup 응용 프로그램으로 이동 합니다.
1. 인증서 **관리**  >  **& 비밀**  >  **업로드 인증서**를 선택 합니다. 이전 단계에서 내보낸 인증서를 업로드 합니다.

    ![인증서 업로드](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. 서버에서 실행 창에 **regedit** 를 입력 하 여 레지스트리를 엽니다.
1. 레지스트리 항목 *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider*로 이동 합니다.
1. **Cloudbackupprovider**를 마우스 오른쪽 단추로 클릭 하 고 이름이 인 새 문자열 값을 추가 `AzureADAppCertThumbprint_<Azure User Id>` 합니다.

    >[!NOTE]
    > Azure 사용자 ID를 찾으려면 다음 단계 중 하나를 수행 합니다.
    >
    >* Azure에 연결된 PowerShell에서 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` 명령을 실행합니다.
    >* 레지스트리 경로로 이동 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` 합니다.

1. 이전 단계에서 추가 된 문자열을 마우스 오른쪽 단추로 클릭 하 고 **수정**을 선택 합니다. 값에서 7 단계에서 내보낸 인증서의 지문을 제공 합니다. 그런 다음, **확인**을 선택합니다.
1. 지문 값을 가져오려면 인증서를 두 번 클릭 합니다. **세부 정보** 탭을 선택 하 고 지문 필드가 표시 될 때까지 아래로 스크롤합니다. **손 도장 (Thumbprint**)을 선택 하 고 값을 복사 합니다.

    ![지문 필드에서 값을 복사 합니다.](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. [워크플로](#workflow) 섹션을 계속 진행 하 여 오프 라인 백업 프로세스를 진행 합니다.

## <a name="workflow"></a>워크플로

이 섹션의 정보는 데이터를 Azure 데이터 센터에 배달 하 고 Azure Storage에 업로드할 수 있도록 오프 라인 백업 워크플로를 완료 하는 데 도움이 됩니다. 가져오기 서비스 또는 프로세스의 모든 측면에 대 한 질문이 있는 경우 앞에서 언급 한 [서비스 가져오기 개요 설명서](../storage/common/storage-import-export-service.md) 를 참조 하세요.

### <a name="initiate-offline-backup"></a>오프라인 백업 시작

1. 백업을 예약 하면 Windows Server, Windows 클라이언트 또는 DPM에 다음 페이지가 표시 됩니다.

    ![페이지 가져오기](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    DPM의 해당 페이지는 다음과 같습니다. <br/>

    ![DPM 및 Azure Backup Server 가져오기 페이지](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    입력 하는 상자는 다음과 같습니다.

   * **준비 위치**: 초기 백업 복사본을 쓸 임시 스토리지 위치입니다. 스테이징 위치는 네트워크 공유 또는 로컬 컴퓨터에 있을 수 있습니다. 복사 컴퓨터와 원본 컴퓨터가 다른 경우 스테이징 위치의 전체 네트워크 경로를 지정 합니다.
   * **Azure 게시 설정**: 게시 설정 파일에 대 한 로컬 경로입니다.
   * **Azure 가져오기 작업 이름**: azure Import/Export 서비스와 Azure Backup에서 azure로 전송 되는 데이터의 전송을 추적 하는 고유 이름입니다.
   * **Azure 구독 id**: azure 게시 설정 파일을 다운로드 한 구독에 대 한 AZURE 구독 id입니다.
   * **Azure Storage 계정**: azure 게시 설정 파일과 연결 된 azure 구독의 저장소 계정 이름입니다.
   * **Azure Storage 컨테이너**: 백업 데이터를 가져올 Azure 스토리지 계정의 대상 스토리지 Blob 이름입니다.

   제공 된 **준비 위치** 및 **Azure 가져오기 작업 이름** 정보를 저장 합니다. 디스크를 준비 하는 데 필요 합니다.

1. 워크플로를 완료 합니다. 오프 라인 백업 복사를 시작 하려면 Azure Backup 에이전트 관리 콘솔에서 **지금** 백업을 선택 합니다. 초기 백업은 이 단계의 일환으로 준비 영역에 기록됩니다.

    ![지금 백업](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    DPM 또는 Azure Backup Server에서 해당 워크플로를 완료 하려면 **보호 그룹**을 마우스 오른쪽 단추로 클릭 합니다. **복구 지점 만들기** 옵션을 선택 합니다. 그런 다음 **온라인 보호** 옵션을 선택 합니다.

    ![지금 DPM 및 MABS 백업](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    작업이 완료되면 준비 위치를 디스크 준비에 사용할 수 있습니다.

    ![Backup 진행](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>SATA 드라이브 준비 및 Azure로 배송

*Azureofflinebackupdiskprep* 유틸리티는 가장 가까운 Azure 데이터 센터로 전송 되는 SATA 드라이브를 준비 하는 데 사용 됩니다. 이 유틸리티는 다음 경로에 있는 Recovery Services 에이전트의 설치 디렉터리에서 사용할 수 있습니다.

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 디렉터리로 이동하고 준비할 SATA 드라이브가 연결될 복사 컴퓨터에 *AzureOfflineBackupDiskPrep* 디렉터리를 복사합니다. 다음 사항을 확인합니다.

   * 복사 컴퓨터는 "오프 라인 백업 시작" 섹션의 워크플로에 제공 된 것과 동일한 네트워크 경로를 사용 하 여 오프 라인 시드 워크플로의 준비 위치에 액세스할 수 있습니다.
   * 복사 컴퓨터에서 BitLocker가 사용 설정되어 있습니다.
   * 복사 컴퓨터는 Azure Portal에 액세스할 수 있습니다. 필요한 경우 복사 컴퓨터는 원본 컴퓨터와 동일할 수 있습니다.

     > [!IMPORTANT]
     > 원본 컴퓨터가 가상 컴퓨터인 경우에는 복사 컴퓨터와 다른 실제 서버 또는 클라이언트 컴퓨터를 사용 해야 합니다.

1. *Azureofflinebackupdiskprep* 유틸리티 디렉터리를 현재 디렉터리로 사용 하 여 복사 컴퓨터에서 관리자 권한 명령 프롬프트를 엽니다. 다음 명령을 실행합니다.

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 매개 변수 | 설명 |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |이 필수 입력은 "오프 라인 백업 시작" 섹션의 워크플로에 입력 한 스테이징 위치에 대 한 경로를 제공 하는 데 사용 됩니다. |
    | p:&lt;*Path to PublishSettingsFile*&gt; |이 선택적 입력은 "오프 라인 백업 시작" 섹션의 워크플로에 입력 한 Azure 게시 설정 파일의 경로를 제공 하는 데 사용 됩니다. |

    > [!NOTE]
    > 복사 컴퓨터 및 원본 컴퓨터가 서로 다른 경우 &lt;AzurePublishSettingFile에 대한 경로&gt; 값은 필수입니다.
    >
    >

    명령을 실행 하면 유틸리티가 준비 해야 하는 드라이브에 해당 하는 Azure 가져오기 작업의 선택을 요청 합니다. 제공 된 준비 위치와 연결 된 가져오기 작업이 하나만 있는 경우 다음과 같은 페이지가 표시 됩니다.

    ![Azure 디스크 준비 도구 입력](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Azure로 전송하기 위해 준비하려는 탑재된 디스크에 대해 후행 콜론 없이 드라이브 문자를 입력합니다. 메시지가 표시 되 면 드라이브의 형식에 대 한 확인을 제공 합니다.

    그런 다음 도구는 디스크를 준비 하 고 백업 데이터를 복사 하기 시작 합니다. 제공 된 디스크에 백업 데이터에 대 한 충분 한 공간이 없는 경우 도구에서 메시지가 표시 되 면 추가 디스크를 연결 해야 할 수 있습니다. <br/>

    도구가 성공적으로 완료 되 면 입력 한 하나 이상의 디스크를 Azure로 배송할 준비가 됩니다. "오프 라인 백업 시작" 섹션의 워크플로 중에 제공한 이름의 가져오기 작업이 Azure에도 만들어집니다. 마지막으로 도구는 디스크를 배송해야 하는 Azure 데이터 센터에 대한 배송 주소를 표시합니다.

    ![Azure 디스크 준비 완료](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. 명령 실행이 끝날 때 배송 정보를 업데이트 하는 옵션도 표시 됩니다.

    ![배송 정보 업데이트 옵션](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. 세부 정보를 바로 입력할 수 있습니다. 이 도구는 일련의 입력을 포함 하는 프로세스를 안내 합니다. 추적 번호 또는 배송 관련 기타 세부 정보와 같은 정보가 없는 경우 세션을 종료할 수 있습니다. 배송 세부 정보를 나중에 업데이트하는 단계는 이 문서에서 제공됩니다.

1. 도구가 제공 하는 주소로 디스크를 배송 합니다. 나중에 참조할 수 있도록 추적 번호를 유지 합니다.

   > [!IMPORTANT]
   > 두 Azure 가져오기 작업은 동일한 추적 번호를 가질 수 없습니다. 단일 Azure 가져오기 작업에서 유틸리티에 의해 준비 된 드라이브가 단일 패키지로 함께 배송 되 고 패키지에 대 한 단일 고유 추적 번호가 있는지 확인 합니다. 여러 Azure 가져오기 작업의 일부로 준비 된 드라이브를 단일 패키지로 결합 하지 마세요.

1. 추적 번호 정보가 있으면 가져오기 작업이 완료 될 때까지 대기 중인 원본 컴퓨터로 이동 합니다. *Azureofflinebackupdiskprep* 유틸리티 디렉터리를 현재 디렉터리로 사용 하 여 관리자 권한 명령 프롬프트에서 다음 명령을 실행 합니다.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   필요에 따라 복사 컴퓨터와 같은 다른 컴퓨터에서 *Azureofflinebackupdiskprep* 유틸리티 디렉터리를 현재 디렉터리로 사용 하 여 다음 명령을 실행할 수 있습니다.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 매개 변수 | 설명 |
    | --- | --- |
    | u: | 이 필수 입력은 Azure 가져오기 작업에 대 한 배송 정보를 업데이트 하는 데 사용 됩니다. |
    | s:&lt;*Staging Location Path*&gt; | 이 필수 입력은 명령이 원본 컴퓨터에서 실행 되지 않을 때 사용 됩니다. "오프 라인 백업 시작" 섹션의 워크플로에 입력 한 스테이징 위치에 대 한 경로를 제공 하는 데 사용 됩니다. |
    | p:&lt;*Path to PublishSettingsFile*&gt; | 이 필수 입력은 명령이 원본 컴퓨터에서 실행 되지 않을 때 사용 됩니다. "오프 라인 백업 시작" 섹션의 워크플로에 입력 한 Azure 게시 설정 파일의 경로를 제공 하는 데 사용 됩니다. |

    유틸리티는 다른 컴퓨터에서 명령이 실행 될 때 원본 컴퓨터가 대기 중인 가져오기 작업 또는 스테이징 위치와 연결 된 가져오기 작업을 자동으로 검색 합니다. 그런 다음 일련의 입력을 통해 배송 정보를 업데이트 하는 옵션을 제공 합니다.

    ![배송 정보 입력](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. 입력을 모두 제공한 후 세부 정보를 신중 하 게 검토 하 고 **예**를 입력 하 여 제공한 배송 정보를 커밋합니다.

    ![배송 정보 검토](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. 배송 정보가 성공적으로 업데이트 되 면이 유틸리티는 입력 한 배송 정보가 저장 되는 로컬 위치를 제공 합니다.

    ![배송 정보 저장](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > 드라이버가 *Azureofflinebackupdiskprep* 유틸리티를 사용 하 여 배송 정보를 제공 하는 2 주 이내에 Azure 데이터 센터에 도달 하는지 확인 합니다. 이렇게 하지 않으면 드라이브가 처리되지 않을 수 있습니다.

이전 단계를 완료 한 후에는 Azure 데이터 센터에서 드라이브를 수신 하 고이를 추가로 처리 하 여 드라이브의 백업 데이터를 사용자가 만든 클래식 유형 Azure 저장소 계정으로 전송할 준비가 된 것입니다.

### <a name="time-to-process-the-drives"></a>드라이브를 처리하는 시간

Azure 가져오기 작업을 처리 하는 데 걸리는 시간은 다양 합니다. 프로세스 시간은 배송 시간, 작업 유형, 복사 되는 데이터의 유형 및 크기, 제공 된 디스크 크기와 같은 요소에 따라 달라 집니다. Azure Import/Export 서비스에는 SLA가 없습니다. 디스크가 수신 되 면 서비스는 Azure storage 계정에 대해 7 ~ 10 일 내에 백업 데이터 복사를 완료 합니다. 다음 섹션에서는 Azure 가져오기 작업의 상태를 모니터링할 수 있는 방법을 설명 합니다.

### <a name="monitor-azure-import-job-status"></a>Azure 가져오기 작업 상태 모니터링

드라이브가 전송 중이거나 Azure 데이터 센터에서 저장소 계정으로 복사 될 때 원본 컴퓨터의 Azure Backup 에이전트 또는 DPM 또는 MABS 콘솔은 예약 된 백업에 대해 다음과 같은 작업 상태를 표시 합니다.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

가져오기 작업 상태를 확인 하려면:

1. 원본 컴퓨터에서 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행 합니다.

     `AzureOfflineBackupDiskPrep.exe u:`

1. 출력은 가져오기 작업의 현재 상태를 표시 합니다.

    ![가져오기 작업 상태 확인](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Azure 가져오기 작업의 여러 상태에 대 한 자세한 내용은 [Azure import/Export 작업의 상태 보기](../storage/common/storage-import-export-view-drive-status.md)를 참조 하세요.

### <a name="finish-the-workflow"></a>워크플로 완료

가져오기 작업이 완료되면 스토리지 계정에서 초기 백업 데이터를 사용할 수 있습니다. 다음 예약 된 백업 시 Azure Backup는 저장소 계정에서 Recovery Services 자격 증명 모음으로 데이터의 내용을 복사 합니다.

   ![Recovery Services 자격 증명 모음에 데이터 복사](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

다음 예약된 백업 시 Azure Backup은 초기 백업 복사본을 통해 증분 백업을 수행합니다.

## <a name="next-steps"></a>다음 단계

* Azure Import/Export 서비스 워크플로에 대 한 질문은 [Microsoft Azure Import/Export 서비스를 사용 하 여 Blob storage로 데이터 전송](../storage/common/storage-import-export-service.md)을 참조 하세요.
