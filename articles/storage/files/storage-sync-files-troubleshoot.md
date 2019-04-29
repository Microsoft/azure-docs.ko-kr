---
title: Azure 파일 동기화 문제 해결 | Microsoft Docs
description: Azure 파일 동기화와 관련된 일반적인 문제를 해결합니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 53297a16889190383e0455c484339adc049c1cb1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759425"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure 파일 동기화 문제 해결
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서는 Azure 파일 동기화 배포 시 발생할 수 있는 문제를 해결하는 데 도움을 주기 위해 작성되었습니다. 또한 문제를 좀 더 자세히 조사해야 할 경우 시스템에서 중요한 로그를 수집하는 방법을 설명합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. [Azure Storage 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)
3. Microsoft 지원 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청**을 선택합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>서버의 Azure 파일 동기화에 문제가 발생했습니다(동기화, 클라우드 계층화 등). 서버 엔드포인트를 제거하고 다시 만들어야 하나요?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>에이전트 설치 및 서버 등록
<a id="agent-installation-failures"></a>**에이전트 설치 오류 해결**  
Azure 파일 동기화 에이전트 설치가 실패할 경우 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 에이전트 설치 동안 로깅을 켭니다.

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

installer.log를 검토하여 설치 실패의 원인을 확인합니다.

<a id="agent-installation-on-DC"></a>**Active Directory 도메인 컨트롤러에 에이전트 설치 실패**  
PDC 역할 소유자가 Windows Server 2008 R2 이하 OS 버전에 있는 Active Directory 도메인 컨트롤러에 동기화 에이전트를 설치하려고 하면 동기화 에이전트를 설치할 수 없는 문제가 발생할 수 있습니다.

이 문제를 해결하려면 PDC 역할을 Windows Server 2012 R2 이상을 실행 중인 다른 도메인 컨트롤러로 전송한 다음, 동기화를 설치합니다.

<a id="server-registration-missing"></a>**Azure Portal의 등록된 서버 아래에서 서버가 나열되지 않습니다.**  
저장소 동기화 서비스에 대한 **등록된 서버** 아래에서 서버가 나열되지 않으면 다음을 수행합니다.
1. 등록하려는 서버에 로그인합니다.
2. 파일 탐색기를 열고 Storage 동기화 에이전트 설치 디렉터리(기본 위치 C:\Program Files\Azure\StorageSyncAgent) 로 이동합니다. 
3. ServerRegistration.exe를 실행하고 마법사를 완료하여 서버를 Storage 동기화 서비스에 등록합니다.

<a id="server-already-registered"></a>**Azure 파일 동기화 에이전트가 설치되는 동안 서버 등록 대화 상자에 “서버가 이미 등록되었습니다.”라는 메시지가 표시됩니다.** 

![“This server is already registered”(서버가 이미 등록되었습니다.) 오류 메시지가 있는 서버 등록 대화 상자의 스크린샷](media/storage-sync-files-troubleshoot/server-registration-1.png)

서버가 이전에 Storage 동기화 서비스에 등록된 경우 이 메시지가 표시됩니다. 현재 Storage 동기화 서비스에서 서버 등록을 취소하고 새 Storage 동기화 서비스에 등록하려면 [Azure 파일 동기화로 서버 등록 해제](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 완료하세요.

저장소 동기화 서비스의 **등록된 서버** 아래에서 서버가 나열되지 않으면, 등록을 취소하려는 서버에서 다음 PowerShell 명령을 실행합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 서버가 클러스터에 속할 경우 선택적 *Reset-StorageSyncServer -CleanClusterRegistration* 매개 변수를 사용하여 클러스터 등록도 제거합니다.

<a id="web-site-not-trusted"></a>**서버를 등록할 때 "웹 사이트를 신뢰할 수 없습니다." 응답이 여러 번 표시됩니다. 그 이유는 무엇일까요?**  
이 문제는 서버 등록 중에 **Internet Explorer 보안 강화** 정책이 사용되도록 설정되었기 때문에 발생합니다. **Internet Explorer 보안 강화** 정책을 사용하지 않도록 적절히 설정하는 방법에 대한 자세한 내용은 [Azure 파일 동기화와 함께 사용하도록 Windows Server 준비](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

## <a name="sync-group-management"></a>동기화 그룹 관리
<a id="cloud-endpoint-using-share"></a>**다음 오류로 인해 클라우드 엔드포인트 생성 실패: “지정된 Azure FileShare가 이미 다른 클라우드 엔드포인트에서 사용되고 있습니다.”**  
Azure 파일 공유가 다른 클라우드 엔드포인트에서 이미 사용되고 있으면 이 문제가 발생합니다. 

이 메시지가 표시되고 Azure 파일 공유가 현재 클라우드 엔드포인트에서 사용되고 있지 않으면 다음 단계를 수행하여 Azure 파일 공유에서 Azure 파일 동기화 메타데이터를 지웁니다.

> [!Warning]  
> 클라우드 엔드포인트에서 현재 사용 중인 Azure 파일 공유에서 메타데이터를 삭제하면 Azure 파일 동기화 작업이 실패합니다. 

1. Azure Portal에서 Azure 파일 공유로 이동합니다.  
2. Azure 파일 공유를 마우스 오른쪽 단추로 클릭하고 **메타데이터 편집**을 선택합니다.
3. **SyncService**를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

<a id="cloud-endpoint-authfailed"></a>**다음 오류로 인해 클라우드 엔드포인트 생성 실패: “AuthorizationFailed”**  
사용자 계정에 클라우드 엔드포인트를 만들 수 있는 충분한 권한이 없으면 이 문제가 발생합니다. 

클라우드 엔드포인트를 만들려면 사용자 계정에 다음과 같은 Microsoft 인증 권한이 있어야 합니다.  
* 읽기: 역할 정의 가져오기
* 쓰기: 사용자 지정 역할 정의 만들기 또는 업데이트
* 읽기: 역할 할당 가져오기
* 쓰기: 역할 할당 만들기

다음과 같은 기본 제공 역할은 필요한 Microsoft 인증 권한을 갖고 있습니다.  
* 소유자
* 사용자 액세스 관리자

사용자의 계정 역할에 필요한 사용 권한이 있는지 확인하려면  
1. Azure Portal에서 **리소스 그룹**을 선택합니다.
2. 저장소 계정이 있는 리소스 그룹을 선택하고 **IAM(Access Control)** 를 선택합니다.
3. **역할 할당** 탭을 선택합니다.
4. 사용자 계정에 대한 **역할**(예: 소유자 또는 Contributor)을 선택합니다.
5. **리소스 공급자** 목록에서 **Microsoft 인증**을 선택합니다. 
    * **역할 할당**에 **읽기** 권한과 **쓰기** 권한이 있어야 합니다.
    * **역할 정의**에 **읽기**  권한과 **쓰기** 권한이 있어야 합니다.

<a id="server-endpoint-createjobfailed"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: “MgmtServerJobFailed”(오류 코드: -2134375898)**  
이 문제는 서버 엔드포인트 경로가 시스템 볼륨에 있고 클라우드 계층화를 사용하도록 설정한 경우 발생합니다. 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.

<a id="server-endpoint-deletejobexpired"></a>**다음 오류로 인해 서버 엔드포인트 삭제 실패: “MgmtServerJobExpired”**                
이 문제는 서버가 오프라인 상태이거나 네트워크에 연결되어 있지 않은 경우 발생합니다. 서버를 더 이상 사용할 수 없는 경우 Azure Portal에서 서버의 등록을 취소하여 서버 엔드포인트를 삭제합니다. 서버 엔드포인트를 삭제하려면 [Azure 파일 동기화에서 서버 등록 취소](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 따릅니다.

<a id="server-endpoint-provisioningfailed"></a>**서버 엔드포인트 속성 페이지를 열거나 클라우드 계층화 정책을 업데이트할 수 없습니다.**  
이 문제는 서버 엔드포인트에서 관리 작업이 실패하는 경우 발생합니다. Azure Portal에서 서버 엔드포인트 속성 페이지가 열리지 않는 경우, 서버에서 PowerShell 명령을 사용하여 서버 엔드포인트를 업데이트하면 이 문제가 해결될 수 있습니다. 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**서버 엔드포인트가 "활동 없음" 또는 "보류 중" 상태이며, 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"**  

이 문제는 Storage 동기화 모니터링 프로세스가 실행되지 않거나, 프록시 또는 방화벽으로 인해 서버가 Azure 파일 동기화 서비스와 통신할 수 없는 경우 발생할 수 있습니다.

이 문제를 해결하려면 다음 단계를 수행합니다.

1. 서버에서 작업 관리자를 열고 Storage 동기화 모니터링 (AzureStorageSyncMonitor.exe) 프로세스가 실행 중인지 확인합니다. 프로세스가 실행되지 않으면 먼저 서버를 다시 시작합니다. 서버를 다시 시작해도 문제가 해결되지 않으면 최신 Azure 파일 동기화 [에이전트 버전](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)으로 업그레이드합니다.
2. 방화벽 및 프록시 설정이 올바로 구성되었는지 확인합니다.
    - 서버가 방화벽 뒤에 있는 경우 포트 443 아웃 바운드가 허용되는지 확인합니다. 방화벽이 트래픽을 특정 도메인으로 제한하는 경우 방화벽 [설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall)에 나열된 도메인에 액세스할 수 있는지 확인합니다.
    - 서버가 프록시 뒤에 있는 경우 프록시 [설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)에 있는 단계에 따라 머신 전체 또는 앱 별 프록시 설정을 구성합니다.

<a id="endpoint-noactivity-sync"></a>**서버 엔드포인트의 상태가 "활동 없음"이며, 등록된 서버 블레이드의 서버 상태가 "온라인"입니다.**  

서버 엔드포인트 상태가 "활동 없음"이면 서버 엔드포인트에서 지난 2시간 동안 동기화 활동을 기록하지 않은 것입니다.

서버 엔드포인트에서 동기화 활동을 기록할 수 없는 이유는 다음과 같습니다.

- 에이전트 버전 4.3.0.0 또는 이전 버전이 설치되어 있고 서버에 활성 VSS 동기화 세션(SnapshotSync)이 있습니다. VSS 동기화 세션이 서버 엔드포인트에 대해 활성 상태이면 VSS 동기화 세션이 완료될 때까지 동일한 볼륨의 다른 서버 엔드포인트에서 동기화 시작 세션을 시작할 수 없습니다. 이 문제를 해결하려면 VSS 동기화 섹션이 활성 상태인 경우 볼륨의 여러 서버 엔드포인트 동기화를 지원하는 에이전트 버전 5.0.2.0 이상을 설치합니다.

    서버의 현재 동기화 활동을 확인하려면 [현재 동기화 세션의 진행률을 모니터링 하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-progress-of-a-current-sync-session)를 참조하세요.

- 서버에서 최대 동시 동기화 세션 수에 도달했습니다. 
    - 에이전트 버전 4.x 이상: 제한은 사용할 수 있는 시스템 리소스에 따라 달라집니다.
    - 에이전트 버전 3.x: 프로세서당 2개의 활성 동기화 세션 또는 서버당 최대 8개의 활성 동기화 세션.

> [!Note]  
> 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"인 경우 [서버 엔드포인트가 "활동 없음" 또는 "보류 중" 상태이며, 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"](#server-endpoint-noactivity) 섹션에서 설명하는 단계를 수행하세요.

## <a name="sync"></a>동기화
<a id="afs-change-detection"></a>**SMB 또는 포털을 통해 Azure 파일 공유에 직접 파일을 만든 경우 해당 파일이 동기화 그룹의 서버에 동기화되는 데 얼마나 걸리나요?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**서버 엔드포인트 상태가 여러 시간 동안 보류 중 상태**  
클라우드 엔드포인트를 만들고 데이터가 포함된 Azure 파일 공유를 사용하는 경우 이 문제가 발생할 수 있습니다. Azure 파일 공유의 변경 내용을 검색하는 변경 내용 열거형 작업이 완료되어야만 클라우드 엔드포인트와 서버 엔드포인트 간에 파일을 동기화할 수 있습니다. 작업 완료 시간은 Azure 파일 공유의 네임스페이스 크기에 따라 달라집니다. 변경 열거형 작업이 완료되면 서버 엔드포인트 상태를 업데이트해야 합니다.

### <a id="broken-sync"></a>동기화 상태를 모니터링하려면 어떻게 해야 하나요?
# <a name="portaltabportal1"></a>[포털](#tab/portal1)
각 동기화 그룹 내에서 개별 서버 엔드포인트로 드릴다운하여 마지막으로 완료된 동기화 세션의 상태를 볼 수 있습니다. 녹색 상태 열과 동기화 상태가 아닌 파일 값 0은 동기화가 예상대로 작동함을 나타냅니다. 이와 다른 경우 아래에서 일반적인 동기화 오류 목록 및 동기화되지 않는 파일 처리 방법을 살펴보세요. 

![Azure Portal 스크린샷](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[서버](#tab/server)
서버의 원격 분석 로그로 이동합니다. `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`의 이벤트 뷰어에서 찾을 수 있습니다. 이벤트 9102는 완료된 동기화 세션에 해당합니다. 동기화의 마지막 상태를 보려면 ID가 9102인 최신 이벤트를 살펴보세요. SyncDirection은 이 세션이 업로드 또는 다운로드되었는지 알려줍니다. HResult가 0이면 동기화 세션이 성공한 것입니다. HResult가 0 이외의 값이면 동기화 중에 오류가 발생했다는 의미입니다. 일반적인 오류 목록은 아래를 참조하세요. PerItemErrorCount가 0보다 크면 일부 파일 또는 폴더가 올바르게 동기화되지 않은 것입니다. HResult는 0이지만 PerItemErrorCount가 0보다 클 수 있습니다.

아래는 성공적인 업로드의 예입니다. 간단하게 설명하기 위해, 각 9102 이벤트에 포함된 값 중 일부만 아래에 나열되어 있습니다. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

반대로, 실패한 업로드는 다음과 비슷합니다.

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

동기화 세션이 전체적으로 실패하거나 PerItemErrorCount의 값이 0이 아니지만, 여전히 동기화가 계속 진행되고 일부 파일이 성공적으로 동기화되는 경우가 가끔 있습니다. 적용됨* 필드(AppliedFileCount, AppliedDirCount, AppliedTombstoneCount 및 AppliedSizeBytes)에서 볼 수 있으며, 이 필드는 성공하는 세션 수를 알려줍니다. 여러 동기화 세션이 연속으로 실패하지만 적용됨* 횟수가 증가하는 경우 지원 티켓을 열기 전에 동기화 시간을 지정하고 다시 시도해 보세요.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>현재 동기화 세션의 진행률을 모니터링 하려면 어떻게 해야 하나요?
# <a name="portaltabportal1"></a>[포털](#tab/portal1)
동기화 그룹 내에서 궁금한 서버 엔드포인트로 이동하고, 동기화 작업 섹션을 살펴보고 현재 세션에서 업로드 또는 다운로드된 파일 수를 확인합니다. 이 상태는 약 5분 지연되며, 동기화 세션이 이 시간 내에 완료될 정도로 작은 경우에는 포털에서 보고되지 않을 수 있습니다. 

# <a name="servertabserver"></a>[서버](#tab/server)
원격 분석 로그온 서버에서 가장 최근의 9302 이벤트를 조회합니다(이벤트 뷰어에서 Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry로 이동). 이 이벤트는 현재 동기화 세션의 상태를 나타냅니다. TotalItemCount는 동기화할 파일 수를 나타내고, AppliedItemCount는 지금까지 동기화된 파일 수를 나타내고, PerItemErrorCount는 동기화에 실패한 파일 수를 나타냅니다(처리 방법은 아래를 참조).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>서버가 서로 동기화되었는지 확인하는 방법이 있나요?
# <a name="portaltabportal1"></a>[포털](#tab/portal1)
특정 동기화 그룹의 각 서버에 대해 다음 사항을 확인합니다.
- 업로드 및 다운로드에 대한 마지막 동기화 시도 타임스탬프가 최근입니다.
- 업로드 및 다운로드의 상태가 모두 녹색입니다.
- 동기화 작업 필드에 남아 있는 동기화할 파일이 거의 없거나 하나도 없습니다.
- 동기화 상태가 아닌 파일 필드의 값이 업로드 및 다운로드 모두 0입니다.

# <a name="servertabserver"></a>[서버](#tab/server)
완료된 동기화 세션을 살펴보세요. 각 서버에 대한 원격 분석 이벤트 로그에 9102 이벤트로 표시됩니다(이벤트 뷰어에서 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`로 이동). 

1. 특정 서버에서 최신 업로드 및 다운로드 세션이 완료되었는지 확인합니다. 이것을 확인하려면 HResult 및 PerItemErrorCount가 업로드와 다운로드 모두 0인지 확인합니다(SyncDirection 필드는 특정 세션이 업로드 세션인지 아니면 다운로드 세션인지 나타냄). 최근에 완료된 동기화 세션이 보이지 않는 경우 현재 동기화 세션이 진행 중일 가능성이 높으며, 대량의 데이터를 방금 추가 또는 수정한 경우에 예상되는 동작입니다.
2. 서버가 클라우드를 통해 완전히 최신 상태가 되고 어느 방향으로든 동기화할 변경 내용이 없으면 빈 동기화 세션이 표시됩니다. 이를 나타내는 것은 업로드 및 다운로드 이벤트로, 모든 동기화* 필드(SyncFileCount, SyncDirCount, SyncTombstoneCount 및 SyncSizeBytes)가 0이면 동기화할 내용이 하나도 없다는 뜻입니다. 변동률이 높은 서버에는 항상 동기화할 내용이 있으므로 빈 동기화 세션이 발생하지 않을 수 있습니다. 동기화 작업이 없는 경우 30분마다 발생해야 합니다. 
3. 모든 서버가 클라우드를 통해 최신 상태가 되면, 다시 말해서 최신 업로드 및 다운로드 세션이 빈 동기화 세션이면 시스템 전체가 동기화되었다고 확실하게 말할 수 있습니다. 
    
Azure 파일 공유에서 직접 변경하는 경우 Azure 파일 동기화는 24시간마다 발생하는 변경 열거형이 실행될 때까지 이 변경 내용을 감지하지 못합니다. Azure 파일 공유에서 최근에 직접 변경한 내용이 없으면 서버는 클라우드를 통해 최신 상태가 되었다고 말할 수 있습니다. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>동기화되지 않는 파일 또는 폴더가 있는지 확인하려면 어떻게 하나요?
서버의 PerItemErrorCount 또는 포털의 동기화 상태가 아닌 파일 수가 특정 동기화 세션에서 0보다 크면 일부 항목이 동기화에 실패한다는 뜻입니다. 파일 및 폴더에 동기화를 막는 특징이 있을 수 있습니다. 이러한 특징은 영구적일 수 있으며, 파일 또는 폴더 이름에서 지원되지 않는 문자를 제거하는 것처럼 동기화를 다시 시작하기 위한 명시적 조치가 필요합니다. 또한 이러한 특징이 일시적일 수 있습니다. 즉, 파일 또는 폴더가 자동으로 동기화를 다시 시작할 수 있습니다. 예를 들어 핸들이 열려 있는 파일은 파일이 닫히면 자동으로 동기화를 다시 시작합니다. Azure 파일 동기화 엔진이 이러한 문제를 발견하면 오류 로그가 생성되며, 이 오류 로그를 구문 분석하면 현재 올바르게 동기화되지 않는 항목이 나열됩니다.

이러한 오류를 살펴보려면 **FileSyncErrorsReport.ps1** PowerShell 스크립트(Azure 파일 동기화 에이전트의 에이전트 설치 디렉터리에 있음)를 실행하여 열려 있는 핸들, 지원되지 않는 문자 또는 기타 문제로 동기화에 실패한 파일을 식별합니다. ItemPath 필드는 루트 동기화 디렉터리를 기준으로 파일의 위치를 알려줍니다. 아래에서 수정 단계에 대한 일반적인 동기화 오류 목록을 참조하세요.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>파일/디렉터리별 동기화 오류 문제 해결
**ItemResults 로그 - 항목별 동기화 오류**  

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 종속 폴더가 아직 동기화되지 않아 파일 또는 디렉터리 변경 내용을 동기화할 수 없습니다. 이 항목은 종속 변경 내용이 동기화된 후 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. |
| 0x7b | 123 | ERROR_INVALID_NAME | 파일 또는 디렉터리 이름이 잘못되었습니다. | 의심스러운 파일 또는 디렉터리 이름을 변경하세요. 자세한 내용은 [지원되지 않는 문자 처리](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | 파일 또는 디렉터리 이름이 잘못되었습니다. | 의심스러운 파일 또는 디렉터리 이름을 변경하세요. 자세한 내용은 [지원되지 않는 문자 처리](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 파일이 사용 중이므로 동기화할 수 없습니다. 파일이 더 이상 사용되지 않을 때 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅숏을 만듭니다. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 파일이 변경되었지만 아직 동기화에서 변경 내용을 발견하지 못했습니다. 이 변경 내용이 발견되면 동기화가 복구됩니다. | 아무 조치도 취할 필요가 없습니다. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Azure 파일 공유 제한에 도달하여 파일을 동기화할 수 없습니다. | 이 문제를 해결하려면 문제 해결 가이드의 [Azure 파일 공유 스토리지 용량 한도에 도달했습니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) 섹션을 참조하세요. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | 이 오류는 다음과 같은 이유로 발생할 수 있습니다. (예: NTFS EFS) 지원 되지 않는 솔루션으로 파일은 암호화, 파일이 삭제 보류 중 상태 또는 파일은 Dfs-r 읽기 전용 복제 폴더에 있습니다 | 파일은 지원 되지 않는 솔루션으로 암호화 하는 경우 파일의 암호 해독 및 지원 되는 암호화 솔루션을 사용 합니다. 지원 솔루션의 목록은 계획 가이드의 [암호화 솔루션](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions)을 참조하세요. 파일이 삭제 보류 중 상태인 경우 열린 파일 핸들이 모두 닫히면 파일이 삭제됩니다. 파일을를 Dfs-r 읽기 전용 복제 폴더에 있는 경우에 Azure File Sync는 Dfs-r 읽기 전용 복제 폴더에 서버 끝점을 지원 하지 않습니다. 참조 [계획 가이드](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) 자세한 내용은 합니다.
| 0x20 | 32 | ERROR_SHARING_VIOLATION | 파일이 사용 중이므로 동기화할 수 없습니다. 파일이 더 이상 사용되지 않을 때 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 동기화 중에 파일이 변경되었으므로 다시 동기화해야 합니다. | 아무 조치도 취할 필요가 없습니다. |

#### <a name="handling-unsupported-characters"></a>지원되지 않는 처리 문자
지원되지 않는 문자로 인해 **FileSyncErrorsReport.ps1** PowerShell 스크립트에서 오류(오류 코드 0x7b 및 0x8007007b)가 표시되면 각 파일 이름에서 오류가 있는 문자를 제거하거나 이름을 변경해야 합니다. 이러한 문자는 대부분 표준 시각적 개체 인코딩이 없으므로 PowerShell이 이러한 문자를 물음표 또는 빈 사각형으로 인쇄할 수 있습니다. [평가 도구](storage-sync-files-planning.md#evaluation-tool)는 지원되지 않는 문자를 식별하는 데 사용될 수 있습니다.

아래 표에서 Azure 파일 동기화에서 지원하지 않는 모든 유니코드 문자가 포함되어 있습니다.

| 문자 집합 | 문자 수 |
|---------------|-----------------|
| <ul><li>0x0000009D(osc operating system command)</li><li>0x00000090(dcs device control string)</li><li>0x0000008F(ss3 single shift three)</li><li>0x00000081(high octet preset)</li><li>0x0000007F(del delete)</li><li>0x0000008D(ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF(아라비아식 표현꼴-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF(specials) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2(noncharacter)</li><li>0x0002FFFE - 0x0002FFFF = 2(noncharacter)</li><li>0x0003FFFE - 0x0003FFFF = 2(비문자)</li><li>0x0004FFFE - 0x0004FFFF = 2(비문자)</li><li>0x0005FFFE - 0x0005FFFF = 2(비문자)</li><li>0x0006FFFE - 0x0006FFFF = 2(비문자)</li><li>0x0007FFFE - 0x0007FFFF = 2(비문자)</li><li>0x0008FFFE - 0x0008FFFF = 2(비문자)</li><li>0x0009FFFE - 0x0009FFFF = 2(비문자)</li><li>0x000AFFFE - 0x000AFFFF = 2(비문자)</li><li>0x000BFFFE - 0x000BFFFF = 2(비문자)</li><li>0x000CFFFE - 0x000CFFFF = 2(비문자)</li><li>0x000DFFFE - 0x000DFFFF = 2(비문자)</li><li>0x000EFFFE - 0x000EFFFF = 2(정의되지 않음)</li><li>0x000FFFFE - 0x000FFFFF = 2(보조 개인 사용 영역)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>일반적인 동기화 오류
<a id="-2147023673"></a>**동기화 세션이 취소되었습니다.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT(10진)** | -2147023673 | 
| **오류 문자열** | ERROR_CANCELLED |
| **재구성 필요** | 아닙니다. |

서버 다시 시작 또는 업데이트 중, VSS 스냅숏 등의 다양한 이유로 동기화 세션이 실패할 수 있습니다. 이 오류는 후속 조치가 필요한 것처럼 보이지만, 여러 시간 동안 유지되지 않는 이상 무시해도 됩니다.

<a id="-2147012889"></a>**서비스와 연결할 수 없습니다.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT(10진)** | -2147012889 | 
| **오류 문자열** | WININET_E_NAME_NOT_RESOLVED |
| **재구성 필요** | 예 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**사용자 요청이 서비스에 의해 제한되었습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT(10진)** | -2134376372 |
| **오류 문자열** | ECS_E_USER_REQUEST_THROTTLED |
| **재구성 필요** | 아닙니다. |

아무 조치도 필요 없습니다. 서버가 다시 시도합니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364065"></a>**동기화에서 클라우드 엔드포인트에 지정된 Azure 파일 공유에 액세스할 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT(10진)** | -2134364065 |
| **오류 문자열** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화 에이전트가 Azure 파일 공유에 액세스할 수 없어서 발생하며, 액세스할 수 없는 이유는 Azure 파일 공유 또는 Azure 파일 공유를 호스팅하는 저장소 계정이 더 이상 존재하지 않기 때문입니다. 다음 단계를 진행하여 이 오류를 해결할 수 있습니다.

1. [저장소 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [저장소 계정에 네트워크 규칙이 포함되어 있지 않은지 확인합니다.](#troubleshoot-network-rules)
3. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)
4. [Azure 파일 동기화가 저장소 계정에 액세스할 수 있는지 확인합니다.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**사용된 저장소 계정 이름을 확인할 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT(10진)** | -2134364064 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **재구성 필요** | 예 |

1. 서버에서 저장소 DNS 이름을 확인할 수 있는지 확인합니다.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [저장소 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
3. [저장소 계정에 네트워크 규칙이 포함되어 있지 않은지 확인합니다.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**동기화 데이터베이스에 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT(10진)** | -1906441138 |
| **오류 문자열** | JET_errWriteConflict |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화에서 사용하는 내부 데이터베이스에 문제가 있을 때 발생합니다. 이 문제가 발생할 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2134364053"></a>**서버에 설치된 Azure 파일 동기화 에이전트 버전이 지원되지 않습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT(10진)** | -2134364053 |
| **오류 문자열** | ECS_E_AGENT_VERSION_BLOCKED |
| **재구성 필요** | 예 |

서버에 설치된 Azure 파일 동기화 에이전트 버전이 지원되지 않으면 이 오류가 발생합니다. 이 문제를 해결하려면 [지원되는 에이전트 버전]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)으로 [업그레이드]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths)합니다.

<a id="-2134351810"></a>**Azure 파일 공유 저장소 용량 한도에 도달했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT(10진)** | -2134351810 |
| **오류 문자열** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 공유 저장소 한도에 도달한 경우에 발생하며, Azure 파일 공유에 할당량이 적용되거나 사용량이 Azure 파일 공유의 한도를 초과할 때 발생할 수 있습니다. 자세한 내용은 [Azure 파일 공유의 현재 한도](storage-files-scale-targets.md)를 참조하세요.

1. 저장소 동기화 서비스 내에서 동기화 그룹으로 이동합니다.
2. 동기화 그룹 내에서 클라우드 엔드포인트를 선택합니다.
3. 열리는 창에서 Azure 파일 공유 이름을 확인합니다.
4. 연결된 저장소 계정을 선택합니다. 이 연결이 실패하면 참조되는 저장소 계정이 제거된 것입니다.

    ![저장소 계정에 연결된 클라우드 엔드포인트 세부 정보를 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. **파일**을 선택하여 파일 공유 목록을 봅니다.
6. 클라우드 엔드포인트가 참조하는 Azure 파일 공유에 대한 행의 끝에서 세 점을 클릭합니다.
7. **사용량**이 **할당량** 미만인지 확인합니다. 다른 할당량이 지정되지 않은 이상, 할당량은 [Azure 파일 공유의 최대 크기](storage-files-scale-targets.md)와 일치합니다.

    ![Azure 파일 공유 속성의 스크린샷.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

공유가 가득 차고 할당량이 설정되지 않은 경우 이 문제를 해결하는 한 가지 방법은 현재 서버 엔드포인트의 각 하위 폴더를 자체 동기화 그룹의 고유한 서버 엔드포인트에 배치하는 것입니다. 이렇게 하면 각 하위 폴더가 개별 Azure 파일 공유와 동기화됩니다.

<a id="-2134351824"></a>**Azure 파일 공유를 찾을 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT(10진)** | -2134351824 |
| **오류 문자열** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 공유에 액세스할 수 없을 때 발생합니다. 문제 해결 방법:

1. [저장소 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)

Azure 파일 공유가 삭제된 경우 새 파일 공유를 만든 후 동기화 그룹을 다시 만들어야 합니다. 

<a id="-2134364042"></a>**이 Azure 구독이 일시 중단된 동안 동기화가 일시 중단되었습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT(10진)** | -2134364042 |
| **오류 문자열** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **재구성 필요** | 예 |

이 오류는 Azure 구독이 일시 중단되면 발생합니다. Azure 구독이 복원되면 동기화가 다시 활성화됩니다. 자세한 내용은 [내 Azure 구독이 비활성화된 이유 및 다시 활성화하는 방법은 무엇인가요?](../../billing/billing-subscription-become-disable.md)를 참조하세요.

<a id="-2134364052"></a>**저장소 계정에 방화벽 또는 가상 네트워크가 구성되어 있습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT(10진)** | -2134364052 |
| **오류 문자열** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **재구성 필요** | 예 |

이 오류는 저장소 계정 방화벽 때문에 또는 저장소 계정이 가상 네트워크에 속하기 때문에 Azure 파일 공유에 액세스할 수 없을 때 발생합니다. Azure 파일 동기화는 아직 이 기능을 지원하지 않습니다. 문제 해결 방법:

1. [저장소 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [저장소 계정에 네트워크 규칙이 포함되어 있지 않은지 확인합니다.](#troubleshoot-network-rules)

이러한 규칙을 제거하면 이 문제를 해결할 수 있습니다. 

<a id="-2134375911"></a>**동기화 데이터베이스에 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT(10진)** | -2134375911 |
| **오류 문자열** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **재구성 필요** | 아닙니다. |

일반적으로 이 오류는 저절로 해결되며, 다음과 같은 경우에 발생할 수 있습니다.

* 동기화 그룹의 서버에서 다수의 파일이 변경되었습니다.
* 개별 파일 및 디렉터리에 수많은 오류가 있습니다.

이 문제가 여러 시간 동안 지속되는 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2146762487"></a>**서버가 보안 연결을 설정하지 못했습니다. 클라우드 서비스가 예기치 않은 인증서를 받았습니다.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT(10진)** | -2146762487 |
| **오류 문자열** | CERT_E_UNTRUSTEDROOT |
| **재구성 필요** | 예 |

이 오류는 조직에서 SSL 종료 프록시를 사용 중이거나 악의적인 엔터티가 서버와 Azure 파일 동기화 서비스 간 트래픽을 가로채는 경우에 발생할 수 있습니다. 이 문제가 발생할 것으로 확신한다면(조직에서 SSL 종료 프록시를 사용하므로) 레지스트리를 재정의하여 인증서 확인을 건너뛸 수 있습니다.

1. SkipVerifyingPinnedRootCertificate 레지스트리 값을 만듭니다.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 등록된 서버에서 동기화 서비스를 다시 시작합니다.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

이 레지스트리 값을 설정하면 Azure 파일 동기화 에이전트에서 서버와 클라우드 서비스 간에 데이터를 전송할 때 로컬에서 신뢰할 수 있는 모든 SSL 인증서를 수락합니다.

<a id="-2147012894"></a>**서비스와 연결할 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT(10진)** | -2147012894 |
| **오류 문자열** | WININET_E_TIMEOUT |
| **재구성 필요** | 예 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**인증에 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT(10진)** | -2134375680 |
| **오류 문자열** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **재구성 필요** | 예 |

이 오류는 다음과 같은 이유로 인해 발생할 수 있습니다.

- 서버 시간이 올바르지 않음
- 서버 엔드포인트 삭제 실패
- 인증에 사용된 인증서가 만료되었습니다. 
    인증서가 만료되었는지 확인하려면 다음 단계를 수행합니다.  
    1. 인증서 MMC 스냅인을 열고, 컴퓨터 계정을 선택하고, Certificates (Local Computer)\Personal\Certificates로 이동합니다.
    2. 클라이언트 인증 인증서가 만료되었는지 확인합니다.

서버 시간이 올바른 경우 다음 단계를 수행하여 문제를 해결합니다.

1. Azure 파일 동기화 에이전트 버전 4.0.1.0 이상이 설치되어 있는지 확인합니다.
2. 서버에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
    Login-AzStorageSync -SubscriptionID <guid> -TenantID <guid>
    Reset-AzStorageSyncServerCertificate -SubscriptionId <guid> -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**서버 엔드포인트가 있는 볼륨의 디스크 공간이 부족합니다.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT(10진)** | -1906441711 |
| **오류 문자열** | JET_errLogDiskFull |
| **재구성 필요** | 예 |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT(10진)** | -2134375654 |
| **오류 문자열** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **재구성 필요** | 예 |

이 오류는 볼륨이 꽉 찼기 때문에 발생합니다. 이 오류는 일반적으로 서버 엔드포인트 외부의 파일이 볼륨의 공간을 모두 사용할 때 발생합니다. 서버 엔드포인트를 추가하거나, 파일을 다른 볼륨으로 이동하거나, 서버 엔드포인트가 있는 볼륨의 크기를 늘려서 볼륨의 공간을 확보해야 합니다.

<a id="-2134364145"></a><a id="replica-not-ready"></a> **서비스를 이 서버 엔드포인트와 동기화할 준비가 완료되지 않았습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT(10진)** | -2134364145 |
| **오류 문자열** | ECS_E_REPLICA_NOT_READY |
| **재구성 필요** | 아닙니다. |

이 오류는 Azure 파일 공유에서 직접 변경된 내용이 있고 변경 내용 검색이 진행 중인 경우에 발생합니다. 변경 내용 검색이 완료되면 동기화가 시작됩니다.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**여러 개별 파일의 문제로 인해 동기화가 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT(10진)** | -2134364145 |
| **오류 문자열** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **재구성 필요** | 예 |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT(10진)** | -2134375908 |
| **오류 문자열** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **재구성 필요** | 예 |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT(10진)** | -2134375853 |
| **오류 문자열** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **재구성 필요** | 예 |

파일별 동기화 오류가 많은 경우 동기화 세션이 시작되지 않을 수 있습니다. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅숏을 만듭니다.

<a id="-2134376423"></a>**서버 엔드포인트 경로에 문제가 있어 동기화가 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT(10진)** | -2134376423 |
| **오류 문자열** | ECS_E_SYNC_INVALID_PATH |
| **재구성 필요** | 예 |

경로가 존재하는지, 로컬 NTFS 볼륨에 있는지, 그리고 재분석 지점이나 기존 서버 엔드포인트가 아닌지 확인하세요.

<a id="-2134375817"></a>**필터 드라이버 버전이 에이전트 버전과 호환되지 않아 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT(10진)** | -2134375817 |
| **오류 문자열** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **재구성 필요** | 예 |

로드된 클라우드 계층화 필터 드라이버(StorageSync.sys) 버전이 스토리지 동기화 에이전트(FileSyncSvc) 서비스와 호환되지 않아 이 오류가 발생합니다. Azure 파일 동기화 에이전트가 업그레이드된 경우 서버를 다시 시작하여 설치를 완료합니다. 오류가 계속 발생하면 에이전트를 제거하고 서버를 다시 시작한 후 Azure 파일 동기화 에이전트를 다시 설치합니다.

<a id="-2134376373"></a>**서비스를 현재 사용할 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT(10진)** | -2134376373 |
| **오류 문자열** | ECS_E_SERVICE_UNAVAILABLE |
| **재구성 필요** | 아닙니다. |

이 오류는 Azure 파일 동기화 서비스를 사용할 수 없기 때문에 발생합니다. 이 오류는 Azure 파일 동기화 서비스가 재개되면 자동으로 해결됩니다.

<a id="-2134375922"></a>**동기화 데이터베이스에 일시적인 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT(10진)** | -2134375922 |
| **오류 문자열** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **재구성 필요** | 아닙니다. |

이 오류는 동기화 데이터베이스에 내부 문제가 있어서 발생합니다. 이 오류는 Azure 파일 동기화가 동기화를 다시 시도하면 자동으로 해결됩니다. 이 문제가 오래 지속되는 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

### <a name="common-troubleshooting-steps"></a>일반적인 문제 해결 단계
<a id="troubleshoot-storage-account"></a>**저장소 계정이 있는지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 저장소 동기화 서비스 내에서 동기화 그룹으로 이동합니다.
2. 동기화 그룹 내에서 클라우드 엔드포인트를 선택합니다.
3. 열리는 창에서 Azure 파일 공유 이름을 확인합니다.
4. 연결된 저장소 계정을 선택합니다. 이 연결이 실패하면 참조되는 저장소 계정이 제거된 것입니다.
    ![저장소 계정에 연결된 클라우드 엔드포인트 세부 정보를 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**저장소 계정에 네트워크 규칙이 포함되어 있지 않은지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 저장소 계정에서, 저장소 계정 왼쪽에 있는 **방화벽 및 가상 네트워크**를 선택합니다.
2. 저장소 계정 내에서 **모든 네트워크의 액세스 허용** 라디오 단추를 선택합니다.
    ![저장소 계정 방화벽 및 네트워크 규칙이 해제된 것을 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Azure 파일 공유가 있는지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 왼쪽의 목차에서 **개요**를 클릭하여 기본 저장소 계정 페이지로 돌아갑니다.
2. **파일**을 선택하여 파일 공유 목록을 봅니다.
3. 클라우드 엔드포인트에서 참조하는 파일 공유가 파일 공유 목록에 나타나는지 확인합니다(위의 1단계에서 적어두었어야 합니다).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Azure 파일 동기화가 저장소 계정에 액세스할 수 있는지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 왼쪽 목차에서 **액세스 제어(IAM)** 를 클릭합니다.
1. **역할 할당** 탭을 클릭하여 스토리지 계정에 액세스할 수 있는 사용자 및 애플리케이션(‘서비스 주체’)을 나열합니다.
1. 목록에 **읽기 권한자 및 데이터 액세스** 역할과 함께 **하이브리드 파일 동기화 서비스**가 표시되는지 확인합니다. 

    ![저장소 계정의 액세스 제어 탭에서 하이브리드 File Sync 서비스 서비스 주체의 스크린 샷](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **하이브리드 파일 동기화 서비스**가 목록에 표시되지 않는 경우 다음 단계를 수행합니다.

    - **추가**를 클릭합니다.
    - **역할** 필드에서 **읽기 권한자 및 데이터 액세스**를 선택합니다.
    - **선택** 필드에서 **하이브리드 파일 동기화 서비스**를 입력하고 역할을 선택한 다음, **저장**을 클릭합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$foundSyncPrincipal = $false
Get-AzRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>사용자가 서버에서 지원되지 않는 문자가 포함된 파일을 만들지 못하게 하려면 어떻게 해야 하나요?
[FSRM(파일 서버 리소스 관리자) 파일 화면](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management)을 사용하면 이름에 지원되지 않는 문자가 들어 있는 파일을 서버에 만들 수 없도록 차단할 수 있습니다. 지원되지 않는 문자는 대부분 인쇄할 수 없고 따라서 16진수 표현을 문자로 먼저 캐스팅해야 하므로 PowerShell을 사용하여 차단해야 할 수도 있습니다.

먼저 [New-FsrmFileGroup cmdlet](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup)을 사용하여 FSRM 파일 그룹을 만듭니다. 이 예제에서는 지원되지 않는 문자가 2개 포함된 그룹을 정의하지만, 파일 그룹에 필요한 만큼 포함해도 됩니다.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

FSRM 파일 그룹을 정의한 후에는 New-FsrmFileScreen cmdlet을 사용하여 FSRM 파일 화면을 만들 수 있습니다.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> 파일 화면은 Azure 파일 동기화에서 지원되지 않는 문자를 만들지 못하게 차단하는 용도로만 사용해야 합니다. 파일 화면을 다른 시나리오에 사용할 경우 동기화 시 Azure 파일 공유에서 서버로 파일을 다운로드하려는 시도가 계속되지만 파일 화면 때문에 차단되므로 데이터 송신율이 높아집니다. 

## <a name="cloud-tiering"></a>클라우드 계층화 
클라우드 계층화에는 다음 두 가지 경로가 있습니다.

- 파일이 계층화되는 데 실패할 수 있습니다, 즉 Azure 파일 동기화에서 파일을 Azure Files로 성공적으로 계층화하지 못합니다.
- 파일을 회수하는 데 실패할 수 있습니다. 즉, 사용자가 계층화된 파일에 액세스하려고 하면 Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)에서 데이터를 다운로드하지 못합니다.

오류 경로 중 하나를 통해 발생할 수 있는 두 가지 주요 오류 클래스가 있습니다.

- 클라우드 저장소 오류
    - *일시적인 저장소 서비스 가용성 문제* - 자세한 내용은 [Azure Storage에 대한 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)를 참조하세요.
    - *액세스할 수 없는 Azure 파일 공유* - 일반적으로 Azure 파일 공유가 여전히 동기화 그룹의 클라우드 엔드포인트일 때 Azure 파일 공유를 삭제하면 이 오류가 발생합니다.
    - *액세스할 수 없는 저장소 계정* - 일반적으로 동기화 계정의 클라우드 엔드포인트인 Azure 파일 공유가 있는 동안 저장소 계정을 삭제하면 이 오류가 발생합니다. 
- 서버 오류 
  - *Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)가 로드되지 않음* - 계층화/회수 요청에 응답하려면 Azure 파일 동기화 파일 시스템 필터를 로드해야 합니다. 로드되지 않는 필터는 여러 가지 이유로 발생할 수 있지만, 가장 일반적인 이유는 관리자가 필터를 수동으로 언로드했기 때문입니다. Azure 파일 동기화가 제대로 작동하려면 Azure 파일 동기화 파일 시스템 파일 필터가 항상 로드되어야 합니다.
  - *누락되었거나 손상되었거나 끊어진 재분석 지점* - 재분석 지점은 다음 두 부분으로 구성된 파일의 특별한 데이터 구조입니다.
    1. 재분석 태그: Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)가 파일에 대한 IO 작업 일부를 수행해야 하는 운영 체제를 나타냅니다. 
    2. 재분석 데이터: 파일 시스템 필터에 연결된 클라우드 엔드포인트(Azure 파일 공유)에 있는 파일의 URI를 나타냅니다. 
        
       재분석 지점이 손상될 수 있는 가장 일반적인 방법은 관리자가 태그 또는 해당 데이터를 수정하려고 하는 경우입니다. 
  - *네트워크 연결 문제* - 파일을 계층화하거나 회수하려면 서버에 인터넷 연결이 있어야 합니다.

다음 섹션에서는 클라우드 계층화 문제를 해결하고 클라우드 저장소 문제 또는 서버 문제인지 여부를 확인하는 방법에 대해 설명합니다.

<a id="monitor-tiering-activity"></a>**서버의 계층화 작업을 모니터링하는 방법**  
서버의 계층화 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016 및 9029를 사용합니다.

- 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 총 오류 수, ErrorCode 등을 예로 들 수 있습니다. 한 이벤트는 오류 코드별로 기록됩니다.
- 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 사용 가능한 공간 비율, 세션에서 고스팅된 파일 수, 고스팅에 실패한 파일 수 등을 예로 들 수 있습니다.
- 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 세션에서 시도된 파일 수, 세션에서 계층화된 파일 수, 이미 계층화된 파일 수 등을 예로 들 수 있습니다.

<a id="monitor-recall-activity"></a>**서버의 회수 작업을 모니터링하는 방법**  
서버의 회수 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9005, 9006,9009 및 9059를 사용합니다.

- 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 액세스된 고유한 파일 수, 액세스에 실패한 고유한 파일 수 등을 예로 들 수 있습니다.
- 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 실패한 요청 수, ErrorCode 등을 예로 들 수 있습니다. 한 이벤트는 오류 코드별로 기록됩니다.
- 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed 등을 예로 들 수 있습니다.
- 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. ShareId, Application Name, TotalEgressNetworkBytes 등을 예로 들 수 있습니다.

<a id="files-fail-tiering"></a>**계층화에 실패한 파일의 문제 해결**  
파일이 Azure Files로 계층화되지 못한 경우:

1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다. 
   1. Azure 파일 공유에 파일이 있는지 확인합니다.

      > [!NOTE]
      > 계층화하려면 파일을 먼저 Azure 파일 공유에 동기화해야 합니다.

   2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
   3. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
       - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

> [!NOTE]
> 파일이 계층화에 실패하면 이벤트 ID 9003이 원격 분석 이벤트 로그에 1시간에 한 번 기록됩니다(오류 코드 하나당 이벤트 하나가 기록). 문제를 진단하기 위해 추가 정보가 필요한 경우 운영 및 진단 이벤트 로그를 사용해야 합니다.

<a id="files-fail-recall"></a>**회수에 실패한 파일 문제 해결**  
파일을 회수하지 못한 경우:
1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다.
    1. Azure 파일 공유에 파일이 있는지 확인합니다.
    2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
    3. 서비스 MMC 스냅인을 열고 저장소 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
    4. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
        - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

> [!NOTE]
> 파일이 회수에 실패하면 이벤트 ID 9006이 원격 분석 이벤트 로그에 1시간에 한 번 기록됩니다(오류 코드 하나당 이벤트 하나가 기록). 문제를 진단하기 위해 추가 정보가 필요한 경우 운영 및 진단 이벤트 로그를 사용해야 합니다.

<a id="files-unexpectedly-recalled"></a>**서버에서 예기치 않게 회수되는 파일 문제 해결**  
바이러스 백신, 백업, 그리고 대량의 파일을 읽는 기타 애플리케이션은 오프라인 특성 건너뛰기 및 해당 파일의 내용 읽기 건너뛰기를 사용하지 않을 경우 의도치 않은 회수가 발생합니다. 이 옵션을 지원하는 제품에 대한 오프라인 파일 건너뛰기는 바이러스 백신 검사 또는 백업 작업 등을 수행하는 동안 의도치 않은 회수를 피하는 데 도움이 됩니다.

오프라인 파일 읽기를 건너뛰도록 솔루션을 구성하는 방법은 소프트웨어 공급업체에 문의하세요.

파일 탐색기에서 파일을 탐색할 경우 등의 기타 시나리오에서도 의도치 않은 회수가 발생할 수 있습니다. 서버의 파일 탐색기에서 클라우드 계층화 파일이 있는 폴더를 열면 의도치 않은 회수가 발생할 수 있습니다. 서버에서 바이러스 백신 솔루션이 사용 가능하게 설정된 경우에 이러한 회수가 발생하기 더 쉽습니다.

> [!NOTE]
>애플리케이션으로 인해 회수가 발생하는지를 확인하려면 원격 분석 이벤트 로그에서 이벤트 ID 9059를 사용합니다. 서버 엔드포인트의 애플리케이션 회수 분포를 제공하는 이 이벤트는 1시간마다 기록됩니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결
서버에서 Azure 파일 동기화에 문제가 발생하는 경우 먼저 다음 단계를 완료합니다.
1. 이벤트 뷰어에서 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다.
    - 동기화, 계층화 및 회수 문제가 Applications and Services\Microsoft\FileSync\Agent의 원격 분석 진단 및 운영 이벤트 로그에 기록됩니다.
    - 서버 관리(예: 구성 설정)와 관련된 문제는 Applications and Services\Microsoft\FileSync\Management의 운영 및 진단 이벤트 로그에 기록됩니다.
2. 서버에서 Azure 파일 동기화 서비스가 실행 중인지 확인합니다.
    - 서비스 MMC 스냅인을 열고 Storage 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
3. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

문제가 해결되지 않으면 AFSDiag 도구를 실행합니다.
1. AFSDiag 출력이 저장될 디렉터리를 만듭니다(예: C:\output).
2. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 실행합니다(각 명령 후 Enter 키 누름).

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure 파일 동기화 커널 모드 추적 수준에 대해 **1**을 입력하고(더 자세한 추적을 만들기 위해 다르게 지정되지 않은 경우) Enter 키를 누릅니다.
4. Azure 파일 동기화 사용자 모드 추적 수준에 대해 **1**을 입력하고(더 자세한 추적을 만들기 위해 다르게 지정되지 않은 경우) Enter 키를 누릅니다.
5. 문제를 재현합니다. 작업을 완료하면 **D** 키를 입력합니다.
6. 로그 및 추적 파일을 포함하는 .zip 파일은 사용자가 지정한 출력 디렉터리에 저장됩니다.

## <a name="see-also"></a>참고 항목
- [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
- [Azure Files 질문과 대답](storage-files-faq.md)
- [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
