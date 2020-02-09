---
title: Azure 파일 동기화 문제 해결 | Microsoft Docs
description: Azure 파일 동기화와 관련된 일반적인 문제를 해결합니다.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: b5a6b62e423b982cd7a852de844cd561997ba1e7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048430"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure 파일 동기화 문제 해결
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서는 Azure 파일 동기화 배포 시 발생할 수 있는 문제를 해결하는 데 도움을 주기 위해 작성되었습니다. 또한 문제를 좀 더 자세히 조사해야 할 경우 시스템에서 중요한 로그를 수집하는 방법을 설명합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

1. [Azure Storage 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)
3. Microsoft 지원 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청**을 선택합니다.

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

<a id="parameter-is-incorrect"></a>**오류가 발생 하 여 Windows Server 2012 r 2의 볼륨에 액세스 하지 못했습니다. 매개 변수가 잘못 되었습니다.**  
Windows Server 2012 r 2에서 서버 엔드포인트을 만든 후 볼륨에 액세스할 때 다음 오류가 발생 합니다.

r: \ 에 액세스할 수 없습니다.  
매개 변수가 잘못되었습니다.

문제를 해결 하려면 Windows Server 2012 r 2에 대 한 최신 업데이트를 설치 하 고 서버를 다시 시작 합니다.

<a id="server-registration-missing-subscriptions"></a>**서버 등록에 모든 Azure 구독이 나열 되지 않음**  
ServerRegistration을 사용 하 여 서버를 등록 하는 경우 Azure 구독 드롭다운을 클릭 하면 구독이 누락 됩니다.

이 문제는 현재 ServerRegistration에서 다중 테넌트 환경을 지원 하지 않기 때문에 발생 합니다. 이 문제는 향후 Azure File Sync 에이전트 업데이트에서 수정 될 예정입니다.

이 문제를 해결 하려면 다음 PowerShell 명령을 사용 하 여 서버를 등록 합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**서버 등록에 "필수 구성 요소가 없습니다." 라는 메시지가 표시 됩니다.**  
이 메시지는 Az 또는 AzureRM PowerShell 모듈이 PowerShell 5.1에 설치 되지 않은 경우 나타납니다. 

> [!Note]  
> ServerRegistration은 PowerShell 6.x를 지원 하지 않습니다. PowerShell 6.x의 AzStorageSyncServer cmdlet을 사용 하 여 서버를 등록할 수 있습니다.

PowerShell 5.1에 Az 또는 AzureRM 모듈을 설치 하려면 다음 단계를 수행 합니다.

1. 관리자 권한 명령 프롬프트에서 **powershell** 을 입력 하 고 enter 키를 누릅니다.
2. 설명서에 따라 최신 Az 또는 AzureRM 모듈을 설치 합니다.
    - [Az module (.NET 4.7.2 필요)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [AzureRM 모듈]( https://go.microsoft.com/fwlink/?linkid=856959)
3. ServerRegistration.exe를 실행하고 마법사를 완료하여 서버를 Storage 동기화 서비스에 등록합니다.

<a id="server-already-registered"></a>**서버 등록에 "이 서버가 이미 등록 되었습니다." 라는 메시지가 표시 됩니다.** 

![“This server is already registered”(서버가 이미 등록되었습니다.) 오류 메시지가 있는 서버 등록 대화 상자의 스크린샷](media/storage-sync-files-troubleshoot/server-registration-1.png)

서버가 이전에 Storage 동기화 서비스에 등록된 경우 이 메시지가 표시됩니다. 현재 Storage 동기화 서비스에서 서버 등록을 취소하고 새 Storage 동기화 서비스에 등록하려면 [Azure 파일 동기화로 서버 등록 해제](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 완료하세요.

스토리지 동기화 서비스의 **등록된 서버** 아래에서 서버가 나열되지 않으면, 등록을 취소하려는 서버에서 다음 PowerShell 명령을 실행합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 서버가 클러스터에 속할 경우 선택적 *Reset-StorageSyncServer -CleanClusterRegistration* 매개 변수를 사용하여 클러스터 등록도 제거합니다.

<a id="web-site-not-trusted"></a>**서버를 등록할 때 "웹 사이트를 신뢰할 수 없습니다." 응답이 많이 표시 됩니다. 굳이?**  
이 문제는 서버 등록 중에 **Internet Explorer 보안 강화** 정책이 사용되도록 설정되었기 때문에 발생합니다. **Internet Explorer 보안 강화** 정책을 사용하지 않도록 적절히 설정하는 방법에 대한 자세한 내용은 [Azure 파일 동기화와 함께 사용하도록 Windows Server 준비](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

<a id="server-registration-missing"></a>**Azure Portal의 등록된 서버 아래에서 서버가 나열되지 않습니다.**  
스토리지 동기화 서비스에 대한 **등록된 서버** 아래에서 서버가 나열되지 않으면 다음을 수행합니다.
1. 등록하려는 서버에 로그인합니다.
2. 파일 탐색기를 열고 Storage 동기화 에이전트 설치 디렉터리(기본 위치 C:\Program Files\Azure\StorageSyncAgent) 로 이동합니다. 
3. ServerRegistration.exe를 실행하고 마법사를 완료하여 서버를 Storage 동기화 서비스에 등록합니다.

## <a name="sync-group-management"></a>동기화 그룹 관리
<a id="cloud-endpoint-using-share"></a> **"지정된 Azure 파일 공유가 이미 다른 클라우드 엔드포인트에서 사용되고 있습니다."라는 오류로 인해 클라우드 엔드포인트를 만드는 데 실패했습니다.**  
Azure 파일 공유가 다른 클라우드 엔드포인트에서 이미 사용되고 있으면 이 오류가 발생합니다. 

이 메시지가 표시되고 Azure 파일 공유가 현재 클라우드 엔드포인트에서 사용되고 있지 않으면 다음 단계를 수행하여 Azure 파일 공유에서 Azure 파일 동기화 메타데이터를 지웁니다.

> [!Warning]  
> 클라우드 엔드포인트에서 현재 사용 중인 Azure 파일 공유에서 메타데이터를 삭제하면 Azure 파일 동기화 작업이 실패합니다. 

1. Azure Portal에서 Azure 파일 공유로 이동합니다.  
2. Azure 파일 공유를 마우스 오른쪽 단추로 클릭하고 **메타데이터 편집**을 선택합니다.
3. **SyncService**를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다.

<a id="cloud-endpoint-authfailed"></a> **"AuthorizationFailed" 오류로 인해 클라우드 엔드포인트를 만드는 데 실패했습니다.**  
이 오류는 사용자 계정에 클라우드 엔드포인트을 만들 수 있는 충분 한 권한이 없는 경우에 발생 합니다. 

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
2. 스토리지 계정이 있는 리소스 그룹을 선택하고 **IAM(Access Control)** 를 선택합니다.
3. **역할 할당** 탭을 선택합니다.
4. 사용자 계정에 대한 **역할**(예: 소유자 또는 Contributor)을 선택합니다.
5. **리소스 공급자** 목록에서 **Microsoft 인증**을 선택합니다. 
    * **역할 할당**에 **읽기** 권한과 **쓰기** 권한이 있어야 합니다.
    * **역할 정의**에 **읽기**  권한과 **쓰기** 권한이 있어야 합니다.

<a id="-2134375898"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2134375898 또는 0x80c80226)**  
이 오류는 서버 엔드포인트 경로가 시스템 볼륨에 있고 클라우드 계층화를 사용하도록 설정한 경우 발생합니다. 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.

<a id="-2147024894"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2147024894 또는 0x80070002)**  
지정된 서버 엔드포인트 경로가 잘못된 경우 이 오류가 발생합니다. 지정된 서버 엔드포인트 경로가 로컬로 연결된 NTFS 볼륨인지 확인합니다. Azure File Sync는 매핑된 드라이브를 서버 엔드포인트 경로로 지원 하지 않습니다.

<a id="-2134375640"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2134375640 또는 0x80c80328)**  
이 오류는 지정 된 서버 엔드포인트 경로가 NTFS 볼륨이 아닌 경우에 발생 합니다. 지정된 서버 엔드포인트 경로가 로컬로 연결된 NTFS 볼륨인지 확인합니다. Azure File Sync는 매핑된 드라이브를 서버 엔드포인트 경로로 지원 하지 않습니다.

<a id="-2134347507"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2134347507 또는 0x80c8710d)**  
이 오류는 Azure 파일 동기화가 압축된 System Volume Information 폴더가 있는 볼륨의 서버 엔드포인트를 지원하지 않기 때문에 발생합니다. 이 문제를 해결하려면 System Volume Information 폴더의 압축을 해제합니다. System Volume Information 폴더가 볼륨에서 유일하게 압축된 폴더인 경우 다음 단계를 수행합니다.

1. [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) 도구를 다운로드 합니다.
2. 관리자 권한 명령 프롬프트에서 다음 명령을 실행 하 여 시스템 계정으로 실행 되는 명령 프롬프트를 시작 **합니다. PsExec-i-s-d cmd**
3. 시스템 계정으로 실행되는 명령 프롬프트에서 다음 명령을 입력하고 Enter 키를 누릅니다.   
    **cd/d "드라이브 문자: \ 시스템 볼륨 정보"**  
    **compact/u/s**

<a id="-2134376345"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2134376345 또는 0x80C80067)**  
서버당 서버 엔드포인트 수 제한에 도달한 경우에 이 오류가 발생합니다. Azure 파일 동기화는 현재 서버당 최대 30개의 서버 엔드포인트를 지원합니다. 자세한 내용은 [Azure File Sync 크기 조정 대상](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets)을 참조 하세요.

<a id="-2134376427"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2134376427 또는 0x80c80015)**  
이 오류는 다른 서버 엔드포인트에서 지정된 서버 엔드포인트 경로를 이미 동기화하고 있는 경우에 발생합니다. Azure 파일 동기화는 동일한 디렉터리 또는 볼륨을 동기화하는 여러 서버 엔드포인트를 지원하지 않습니다.

<a id="-2160590967"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 만들지 못했습니다. "MgmtServerJobFailed" (오류 코드:-2160590967 또는 0x80c80077)**  
서버 엔드포인트 경로에 분리 된 계층화 된 파일이 포함 되어 있는 경우이 오류가 발생 합니다. 서버 엔드포인트이 최근에 제거 된 경우 분리 된 계층화 된 파일 정리가 완료 될 때까지 기다립니다. 분리 된 계층화 된 파일 정리가 시작 되 면 이벤트 ID 6662가 원격 분석 이벤트 로그에 기록 됩니다. 분리 된 계층화 된 파일 정리가 완료 되 고 경로를 사용 하 여 서버 엔드포인트을 다시 만들 수 있는 경우 이벤트 ID 6661이 기록 됩니다. 이벤트 ID 6661이 기록 된 후 서버 엔드포인트 만들기가 실패 하는 경우 서버 [엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없음](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 섹션에 설명 된 단계를 수행 하 여 분리 된 계층화 된 파일을 제거 합니다.

<a id="-2134347757"></a>**다음 오류가 발생 하 여 서버 엔드포인트을 삭제 하지 못했습니다. "MgmtServerJobExpired" (오류 코드:-2134347757 또는 0x80c87013)**  
이 오류는 서버가 오프라인 상태이거나 네트워크에 연결되어 있지 않은 경우 발생합니다. 서버를 더 이상 사용할 수 없는 경우 Azure Portal에서 서버의 등록을 취소하여 서버 엔드포인트를 삭제합니다. 서버 엔드포인트를 삭제하려면 [Azure 파일 동기화에서 서버 등록 취소](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 따릅니다.

<a id="server-endpoint-provisioningfailed"></a>**서버 엔드포인트 속성 페이지를 열거나 클라우드 계층화 정책을 업데이트할 수 없습니다.**  
이 문제는 서버 엔드포인트에서 관리 작업이 실패하는 경우 발생합니다. Azure Portal에서 서버 엔드포인트 속성 페이지가 열리지 않는 경우, 서버에서 PowerShell 명령을 사용하여 서버 엔드포인트를 업데이트하면 이 문제가 해결될 수 있습니다. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**서버 엔드포인트가 "활동 없음" 또는 "보류 중" 상태이며, 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"**  

저장소 동기화 모니터 프로세스 (Azurestoragesgacutil.exe)가 실행 되 고 있지 않거나 서버에서 Azure File Sync 서비스에 액세스할 수 없는 경우이 문제가 발생할 수 있습니다.

포털에서 "오프 라인으로 표시"로 표시 되는 서버에서 원격 분석 이벤트 로그 (이벤트 뷰어의 응용 프로그램 및 Services\Microsoft\FileSync\Agent에 있음)에서 이벤트 ID 9301을 확인 하 여 서버가 Azure File Sync에 액세스할 수 없는 이유를 확인 합니다. 출력소. 

- **GetNextJob 완료 상태: 0** 이 기록 되 면 서버는 Azure File Sync 서비스와 통신할 수 있습니다. 
    - 서버에서 작업 관리자를 열고 Storage 동기화 모니터링 (AzureStorageSyncMonitor.exe) 프로세스가 실행 중인지 확인합니다. 프로세스가 실행되지 않으면 먼저 서버를 다시 시작합니다. 서버를 다시 시작해도 문제가 해결되지 않으면 최신 Azure 파일 동기화 [에이전트 버전](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes)으로 업그레이드합니다. 

- **GetNextJob이 완료 됨 상태:-2134347756** 가 기록 되는 경우 서버는 방화벽 또는 프록시로 인해 Azure File Sync 서비스와 통신할 수 없습니다. 
    - 서버가 방화벽 뒤에 있는 경우 포트 443 아웃 바운드가 허용되는지 확인합니다. 방화벽이 트래픽을 특정 도메인으로 제한하는 경우 방화벽 [설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall)에 나열된 도메인에 액세스할 수 있는지 확인합니다.
    - 서버가 프록시 뒤에 있는 경우 프록시 [설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)에 있는 단계에 따라 머신 전체 또는 앱 별 프록시 설정을 구성합니다.
    - 테스트-StorageSyncNetworkConnectivity cmdlet을 사용 하 여 서비스 엔드포인트에 대 한 네트워크 연결을 확인 합니다. 자세히 알아보려면 [서비스 엔드포인트에 대 한 네트워크 연결 테스트](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)를 참조 하세요.

- **GetNextJob이 완료 됨 상태:-2134347764** 가 기록 되는 경우 서버는 만료 되거나 삭제 된 인증서로 인해 Azure File Sync 서비스와 통신할 수 없습니다.  
    - 서버에서 다음 PowerShell 명령을 실행 하 여 인증에 사용 되는 인증서를 다시 설정 합니다.
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**서버 엔드포인트의 상태가 "활동 없음"이며, 등록된 서버 블레이드의 서버 상태가 "온라인"입니다.**  

서버 엔드포인트 상태가 "활동 없음"이면 서버 엔드포인트에서 지난 2시간 동안 동기화 활동을 기록하지 않은 것입니다.

서버의 현재 동기화 활동을 확인하려면 [현재 동기화 세션의 진행률을 모니터링 하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-progress-of-a-current-sync-session)를 참조하세요.

서버 엔드포인트은 버그로 인해 또는 시스템 리소스가 부족 하 여 동기화 작업을 몇 시간 동안 로그 하지 못할 수 있습니다. 최신 Azure File Sync [에이전트 버전이](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) 설치 되어 있는지 확인 하십시오. 문제가 지속 되 면 지원 요청을 엽니다.

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

# <a name="servertabserver"></a>[Server](#tab/server)
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

# <a name="servertabserver"></a>[Server](#tab/server)
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

# <a name="servertabserver"></a>[Server](#tab/server)
완료된 동기화 세션을 살펴보세요. 각 서버에 대한 원격 분석 이벤트 로그에 9102 이벤트로 표시됩니다(이벤트 뷰어에서 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`로 이동). 

1. 특정 서버에서 최신 업로드 및 다운로드 세션이 완료되었는지 확인합니다. 이것을 확인하려면 HResult 및 PerItemErrorCount가 업로드와 다운로드 모두 0인지 확인합니다(SyncDirection 필드는 특정 세션이 업로드 세션인지 아니면 다운로드 세션인지 나타냄). 최근에 완료된 동기화 세션이 보이지 않는 경우 현재 동기화 세션이 진행 중일 가능성이 높으며, 대량의 데이터를 방금 추가 또는 수정한 경우에 예상되는 동작입니다.
2. 서버가 클라우드를 통해 완전히 최신 상태가 되고 어느 방향으로든 동기화할 변경 내용이 없으면 빈 동기화 세션이 표시됩니다. 이러한 이벤트는 모든 Sync * 필드 (SyncFileCount, Syncfilecount, SyncTombstoneCount 및 Syncfilecount)가 0 인 업로드 및 다운로드 이벤트로 표시 됩니다. 즉, 동기화 할 항목이 없습니다. 이러한 빈 동기화 세션은 항상 동기화 할 새 항목이 있으므로 높은 변동 서버에서 발생 하지 않을 수 있습니다. 동기화 작업이 없으면 30 분 마다 발생 합니다. 
3. 모든 서버가 클라우드를 통해 최신 상태가 되면, 다시 말해서 최신 업로드 및 다운로드 세션이 빈 동기화 세션이면 시스템 전체가 동기화되었다고 확실하게 말할 수 있습니다. 
    
Azure 파일 공유에서 직접 변경하는 경우 Azure 파일 동기화는 24시간마다 발생하는 변경 열거형이 실행될 때까지 이 변경 내용을 감지하지 못합니다. Azure 파일 공유에서 최근에 직접 변경한 내용이 없으면 서버는 클라우드를 통해 최신 상태가 되었다고 말할 수 있습니다. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>동기화되지 않는 파일 또는 폴더가 있는지 확인하려면 어떻게 하나요?
지정 된 동기화 세션에 대해 PerItemErrorCount의 서버 또는 파일의 동기화 되지 않는 파일 수가 0 보다 큰 경우에는 일부 항목의 동기화가 실패 합니다. 파일 및 폴더는 동기화를 방해 하는 특성을 가질 수 있습니다. 이러한 특징은 영구적일 수 있으며, 파일 또는 폴더 이름에서 지원되지 않는 문자를 제거하는 것처럼 동기화를 다시 시작하기 위한 명시적 조치가 필요합니다. 또한 이러한 특징이 일시적일 수 있습니다. 즉, 파일 또는 폴더가 자동으로 동기화를 다시 시작할 수 있습니다. 예를 들어 핸들이 열려 있는 파일은 파일이 닫히면 자동으로 동기화를 다시 시작합니다. Azure 파일 동기화 엔진이 이러한 문제를 발견하면 오류 로그가 생성되며, 이 오류 로그를 구문 분석하면 현재 올바르게 동기화되지 않는 항목이 나열됩니다.

이러한 오류를 살펴보려면 **FileSyncErrorsReport.ps1** PowerShell 스크립트(Azure 파일 동기화 에이전트의 에이전트 설치 디렉터리에 있음)를 실행하여 열려 있는 핸들, 지원되지 않는 문자 또는 기타 문제로 동기화에 실패한 파일을 식별합니다. ItemPath 필드는 루트 동기화 디렉터리를 기준으로 파일의 위치를 알려줍니다. 아래에서 수정 단계에 대한 일반적인 동기화 오류 목록을 참조하세요.

> [!Note]  
> FileSyncErrorsReport 스크립트가 "파일 오류가 없습니다"를 반환 하거나 동기화 그룹에 대 한 항목당 오류가 나열 되지 않으면 다음 중 하나가 발생 합니다.
>
>- 원인 1: 마지막으로 완료 된 동기화 세션에 항목당 오류가 없습니다. 동기화 되지 않은 파일 0 개를 표시 하려면 포털을 곧 업데이트 해야 합니다. 
>   - 원격 분석 이벤트 로그에서 [이벤트 ID 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 을 확인 하 여 PerItemErrorCount가 0 인지 확인 합니다. 
>
>- 원인 2: 항목 당 오류가 너무 많아 이벤트 로그에이 동기화 그룹에 대 한 오류가 더 이상 포함 되어 있지 않아서 서버에서 ItemResults 이벤트 로그가 래핑 되었습니다.
>   - 이 문제를 방지 하려면 ItemResults 이벤트 로그 크기를 늘립니다. ItemResults 이벤트 로그는 이벤트 뷰어의 "응용 프로그램 및 서비스 Logs\Microsoft\FileSync\Agent"에서 찾을 수 있습니다. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>파일/디렉터리별 동기화 오류 문제 해결
**ItemResults 로그 - 항목별 동기화 오류**  

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | 서버에서 계층화 된 파일에 액세스할 수 없습니다. 이 문제는 서버 엔드포인트를 삭제하기 전에 계층화된 파일이 회수되지 않은 경우에 발생합니다. | 이 문제를 해결 하려면 [서버 엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없음](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조 하세요. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 종속 폴더가 아직 동기화 되지 않았으므로 파일 또는 디렉터리 변경을 동기화 할 수 없습니다. 이 항목은 종속 변경 내용이 동기화된 후 동기화됩니다. | 작업이 필요하지 않습니다. 오류가 며칠 동안 지속 되는 경우 FileSyncErrorsReport PowerShell 스크립트를 사용 하 여 종속 폴더가 아직 동기화 되지 않은 이유를 확인 합니다. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | 종속 폴더가 아직 동기화 되지 않았으므로 동기화 세션이 실패 했으므로 파일 또는 디렉터리 변경 내용을 동기화 할 수 없습니다. 이 항목은 종속 변경 내용이 동기화된 후 동기화됩니다. | 작업이 필요하지 않습니다. 오류가 계속 발생 하면 동기화 세션 오류를 조사 하십시오. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | 파일 또는 디렉터리 이름이 잘못되었습니다. | 의심스러운 파일 또는 디렉터리 이름을 변경하세요. 자세한 내용은 [지원되지 않는 문자 처리](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | 파일 또는 디렉터리 이름이 잘못되었습니다. | 의심스러운 파일 또는 디렉터리 이름을 변경하세요. 자세한 내용은 [지원되지 않는 문자 처리](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 파일이 사용 중이기 때문에 동기화 할 수 없습니다. 파일이 더 이상 사용되지 않을 때 동기화됩니다. | 작업이 필요하지 않습니다. Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅샷을 만듭니다. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 파일이 변경 되었지만 동기화를 통해 변경 내용이 아직 검색 되지 않았습니다. 이 변경 내용이 검색 되 면 동기화가 복구 됩니다. | 작업이 필요하지 않습니다. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | 파일이 삭제 되 고 동기화가 변경 내용을 인식 하지 못합니다. | 작업이 필요하지 않습니다. 변경 내용 검색을 통해 파일이 삭제 된 것이 감지 되 면 동기화가이 오류 기록을 중지 합니다. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | 대상에서 항목이 이미 삭제 되어 동기화가 변경 내용을 인식 하지 않으므로 파일 또는 디렉터리 삭제를 동기화 할 수 없습니다. | 작업이 필요하지 않습니다. 대상에서 변경 검색을 실행 하 고 동기화가 항목을 삭제 하 고 검색 하면 동기화가이 오류 기록을 중지 합니다. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | 파일이 나 디렉터리를 건너 뛰 었으 나 다음 동기화 세션 중에 동기화 됩니다. 항목을 다운로드할 때이 오류가 보고 되는 경우 파일 또는 디렉터리 이름이 잘못 된 것일 수 있습니다. | 파일을 업로드할 때이 오류가 보고 되는 경우에는 작업이 필요 하지 않습니다. 파일을 다운로드할 때 오류가 보고 되는 경우 해당 파일 또는 디렉터리의 이름을 바꿉니다. 자세한 내용은 [지원되지 않는 문자 처리](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | 항목이 대상에 이미 있고 동기화가 변경 내용을 인식 하지 않으므로 파일 또는 디렉터리 만들기를 동기화 할 수 없습니다. | 작업이 필요하지 않습니다. 대상에서 변경 검색을 실행 하 고 동기화가이 새 항목을 인식 하면 동기화가이 오류 기록을 중지 합니다. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Azure 파일 공유 제한에 도달하여 파일을 동기화할 수 없습니다. | 이 문제를 해결하려면 문제 해결 가이드의 [Azure 파일 공유 스토리지 용량 한도에 도달했습니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) 섹션을 참조하세요. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | 파일은 지원 되지 않는 솔루션 (예: NTFS EFS)에 의해 암호화 됩니다. | 파일의 암호를 해독 하 고 지원 되는 암호화 솔루션을 사용 합니다. 지원 솔루션의 목록은 계획 가이드의 [암호화 솔루션](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions)을 참조하세요. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | 파일은 DFS-R 읽기 전용 복제 폴더에 있습니다. | 파일은 DFS-R 읽기 전용 복제 폴더에 있습니다. Azure 파일 동기화는 DFS-R 읽기 전용 복제 폴더에서 서버 엔드포인트를 지원하지 않습니다. 자세한 내용은 [계획 가이드](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) 를 참조 하세요. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 이 파일에는 삭제 보류 중 상태가 있습니다. | 작업이 필요하지 않습니다. 열려 있는 모든 파일 핸들이 닫히면 파일이 삭제 됩니다. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | 저장소 계정에 대 한 방화벽 및 가상 네트워크 설정이 사용 하도록 설정 되어 있고 서버에 저장소 계정에 대 한 액세스 권한이 없기 때문에 파일을 동기화 할 수 없습니다. | 배포 가이드의 [방화벽 및 가상 네트워크 설정 구성](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 섹션에 설명 된 단계를 수행 하 여 서버 IP 주소 또는 가상 네트워크를 추가 합니다. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | 보안 설명자 크기가 64 KiB 제한을 초과 하므로 파일을 동기화 할 수 없습니다. | 이 문제를 해결하려면 파일의 ACE(액세스 제어 항목)를 제거하여 보안 설명자 크기를 줄입니다. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | 예기치 않은 오류로 인해 파일을 동기화 할 수 없습니다. | 오류가 며칠 동안 지속 되 면 지원 사례를 여세요. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | 파일이 사용 중이기 때문에 동기화 할 수 없습니다. 파일이 더 이상 사용되지 않을 때 동기화됩니다. | 작업이 필요하지 않습니다. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 동기화 하는 동안 파일이 변경 되었으므로 다시 동기화 해야 합니다. | 작업이 필요하지 않습니다. |
| 0x80070017 | -2147024873 | ERROR_CRC | CRC 오류로 인해 파일을 동기화 할 수 없습니다. 이 오류는 서버 엔드포인트을 삭제 하기 전에 계층화 된 파일을 회수할 수 없거나 파일이 손상 된 경우에 발생할 수 있습니다. | 이 문제를 해결 하려면 [서버 엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없어](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 분리 된 계층화 된 파일을 제거 하는 방법을 참조 하세요. 고아 계층화 된 파일을 제거한 후에도 오류가 계속 발생 하면 볼륨에서 [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) 를 실행 합니다. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | 충돌 파일의 최대 수에 도달 했으므로 파일을 동기화 할 수 없습니다. Azure File Sync는 파일당 100 충돌 파일을 지원 합니다. 파일 충돌에 대해 자세히 알아보려면 Azure File Sync [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution)를 참조 하세요. | 이 문제를 해결 하려면 충돌 파일 수를 줄입니다. 충돌 파일 수가 100 미만이 면 파일이 동기화 됩니다. |

#### <a name="handling-unsupported-characters"></a>지원되지 않는 처리 문자
**FileSyncErrorsReport** PowerShell 스크립트에 지원 되지 않는 문자 (오류 코드 0x8007007b 또는 0x80c80255)로 인 한 오류가 표시 되는 경우 해당 파일 이름에서 오류 시 문자를 제거 하거나 이름을 바꾸어야 합니다. 이러한 문자는 대부분 표준 시각적 개체 인코딩이 없으므로 PowerShell이 이러한 문자를 물음표 또는 빈 사각형으로 인쇄할 수 있습니다. [평가 도구](storage-sync-files-planning.md#evaluation-cmdlet)는 지원되지 않는 문자를 식별하는 데 사용될 수 있습니다.

아래 표에서 Azure 파일 동기화에서 지원하지 않는 모든 유니코드 문자가 포함되어 있습니다.

| 문자 집합 | 문자 수 |
|---------------|-----------------|
| <ul><li>0x0000009D(osc operating system command)</li><li>0x00000090(dcs device control string)</li><li>0x0000008F(ss3 single shift three)</li><li>0x00000081(high octet preset)</li><li>0x0000007F(del delete)</li><li>0x0000008D(ri reverse line feed)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF(아라비아식 표현꼴-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF(specials) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2(noncharacter)</li><li>0x0002FFFE - 0x0002FFFF = 2(noncharacter)</li><li>0x0003FFFE - 0x0003FFFF = 2(비문자)</li><li>0x0004FFFE - 0x0004FFFF = 2(비문자)</li><li>0x0005FFFE - 0x0005FFFF = 2(비문자)</li><li>0x0006FFFE - 0x0006FFFF = 2(비문자)</li><li>0x0007FFFE - 0x0007FFFF = 2(비문자)</li><li>0x0008FFFE - 0x0008FFFF = 2(비문자)</li><li>0x0009FFFE - 0x0009FFFF = 2(비문자)</li><li>0x000AFFFE - 0x000AFFFF = 2(비문자)</li><li>0x000BFFFE - 0x000BFFFF = 2(비문자)</li><li>0x000CFFFE - 0x000CFFFF = 2(비문자)</li><li>0x000DFFFE - 0x000DFFFF = 2(비문자)</li><li>0x000EFFFE - 0x000EFFFF = 2(정의되지 않음)</li><li>0x000FFFFE - 0x000FFFFF = 2(보조 프라이빗 사용 영역)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>일반적인 동기화 오류
<a id="-2147023673"></a>**동기화 세션이 취소 되었습니다.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT(10진)** | -2147023673 | 
| **오류 문자열** | ERROR_CANCELLED |
| **재구성 필요** | 아니요 |

서버를 다시 시작 하거나 업데이트 하는, VSS 스냅숏 등의 여러 가지 이유로 동기화 세션이 실패할 수 있습니다. 이 오류는 추가 작업이 필요한 것 처럼 보이지만 몇 시간 동안 지속 되는 경우를 제외 하 고이 오류를 무시 해도 안전 합니다.

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
| **재구성 필요** | 아니요 |

아무 조치도 필요 없습니다. 서버가 다시 시도합니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364043"></a>**변경 내용 검색 후 사후 복원이 완료 될 때까지 동기화가 차단 됩니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT(10진)** | -2134364043 |
| **오류 문자열** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **재구성 필요** | 아니요 |

사용자가 조치할 필요는 없습니다. Azure Backup를 사용 하 여 파일 또는 파일 공유 (클라우드 엔드포인트)를 복원 하는 경우 Azure 파일 공유에서 변경 검색이 완료 될 때까지 동기화가 차단 됩니다. 변경 검색은 복원이 완료되는 즉시 실행되며 기간은 파일 공유의 파일 수를 기반으로 합니다

<a id="-2147216747"></a>**동기화 데이터베이스가 언로드 되었으므로 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT(10진)** | -2147216747 |
| **오류 문자열** | SYNC_E_METADATA_INVALID_OPERATION |
| **재구성 필요** | 아니요 |

이 오류는 일반적으로 백업 애플리케이션에서 VSS 스냅샷을 만들고 동기화 데이터베이스가 언로드될 때 발생합니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364065"></a>**동기화에서 클라우드 엔드포인트에 지정된 Azure 파일 공유에 액세스할 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT(10진)** | -2134364065 |
| **오류 문자열** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화 에이전트가 Azure 파일 공유에 액세스할 수 없어서 발생하며, 액세스할 수 없는 이유는 Azure 파일 공유 또는 Azure 파일 공유를 호스팅하는 스토리지 계정이 더 이상 존재하지 않기 때문입니다. 다음 단계를 진행하여 이 오류를 해결할 수 있습니다.

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)
3. [Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](#troubleshoot-rbac)
4. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**요청에이 작업을 수행할 수 있는 권한이 없어 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT(10진)** | -2134351804 |
| **오류 문자열** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **재구성 필요** | 예 |

이 오류는 Azure File Sync 에이전트에 Azure 파일 공유에 액세스할 수 있는 권한이 없기 때문에 발생 합니다. 다음 단계를 진행하여 이 오류를 해결할 수 있습니다.

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)
3. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**사용된 스토리지 계정 이름을 확인할 수 없습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT(10진)** | -2134364064 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **재구성 필요** | 예 |

1. 서버에서 스토리지 DNS 이름을 확인할 수 있는지 확인합니다.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
3. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**저장소 계정에 액세스 하는 동안 알 수 없는 오류가 발생 했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT(10진)** | -2134364022 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **재구성 필요** | 예 |

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**저장소 계정이 잠겨 있어 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT(10진)** | -2134364014 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **재구성 필요** | 예 |

저장소 계정에 읽기 전용 [리소스 잠금이](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)있으므로이 오류가 발생 합니다. 이 문제를 해결 하려면 저장소 계정에 대 한 읽기 전용 리소스 잠금을 제거 합니다. 

<a id="-1906441138"></a>**동기화 데이터베이스에 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT(10진)** | -1906441138 |
| **오류 문자열** | JET_errWriteConflict |
| **재구성 필요** | 예 |

이 오류는 Azure File Sync에서 사용 하는 내부 데이터베이스에 문제가 있는 경우에 발생 합니다. 이 문제가 발생 하는 경우 지원 요청을 만들면이 문제를 해결 하는 데 도움이 되도록 연락 드리겠습니다.

<a id="-2134364053"></a>**서버에 설치된 Azure 파일 동기화 에이전트 버전이 지원되지 않습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT(10진)** | -2134364053 |
| **오류 문자열** | ECS_E_AGENT_VERSION_BLOCKED |
| **재구성 필요** | 예 |

서버에 설치된 Azure 파일 동기화 에이전트 버전이 지원되지 않으면 이 오류가 발생합니다. 이 문제를 해결하려면 [지원되는 에이전트 버전]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths)으로 [업그레이드]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)합니다.

<a id="-2134351810"></a>**Azure 파일 공유 스토리지 용량 한도에 도달했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT(10진)** | -2134351810 |
| **오류 문자열** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 공유 스토리지 한도에 도달한 경우에 발생하며, Azure 파일 공유에 할당량이 적용되거나 사용량이 Azure 파일 공유의 한도를 초과할 때 발생할 수 있습니다. 자세한 내용은 [Azure 파일 공유의 현재 한도](storage-files-scale-targets.md)를 참조하세요.

1. 스토리지 동기화 서비스 내에서 동기화 그룹으로 이동합니다.
2. 동기화 그룹 내에서 클라우드 엔드포인트를 선택합니다.
3. 열리는 창에서 Azure 파일 공유 이름을 확인합니다.
4. 연결된 스토리지 계정을 선택합니다. 이 연결이 실패하면 참조되는 스토리지 계정이 제거된 것입니다.

    ![스토리지 계정에 연결된 클라우드 엔드포인트 세부 정보를 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

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

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)

Azure 파일 공유가 삭제된 경우 새 파일 공유를 만든 후 동기화 그룹을 다시 만들어야 합니다. 

<a id="-2134364042"></a>**이 Azure 구독이 일시 중단된 동안 동기화가 일시 중단되었습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT(10진)** | -2134364042 |
| **오류 문자열** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **재구성 필요** | 예 |

이 오류는 Azure 구독이 일시 중단되면 발생합니다. Azure 구독이 복원되면 동기화가 다시 활성화됩니다. 자세한 내용은 [내 Azure 구독이 비활성화된 이유 및 다시 활성화하는 방법은 무엇인가요?](../../cost-management-billing/manage/subscription-disabled.md)를 참조하세요.

<a id="-2134364052"></a>**스토리지 계정에 방화벽 또는 가상 네트워크가 구성되어 있습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT(10진)** | -2134364052 |
| **오류 문자열** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **재구성 필요** | 예 |

이 오류는 스토리지 계정 방화벽 때문에 또는 스토리지 계정이 가상 네트워크에 속하기 때문에 Azure 파일 공유에 액세스할 수 없을 때 발생합니다. 저장소 계정에 대 한 방화벽 및 가상 네트워크 설정이 제대로 구성 되어 있는지 확인 합니다. 자세한 내용은 [방화벽 및 가상 네트워크 설정 구성](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)을 참조 하세요. 

<a id="-2134375911"></a>**동기화 데이터베이스에 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT(10진)** | -2134375911 |
| **오류 문자열** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **재구성 필요** | 아니요 |

일반적으로 이 오류는 저절로 해결되며, 다음과 같은 경우에 발생할 수 있습니다.

* 동기화 그룹의 서버에서 다수의 파일이 변경되었습니다.
* 개별 파일 및 디렉터리에 수많은 오류가 있습니다.

이 문제가 여러 시간 동안 지속되는 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2146762487"></a>**서버에서 보안 연결을 설정 하지 못했습니다. 클라우드 서비스에서 예기치 않은 인증서를 받았습니다.**  

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

이 오류는 일반적으로 서버 시간이 올바르지 않기 때문에 발생합니다. 서버를 가상 머신에서 실행 하는 경우 호스트의 시간이 올바른지 확인 합니다.

<a id="-2134364040"></a>**인증서 만료로 인해 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT(10진)** | -2134364040 |
| **오류 문자열** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **재구성 필요** | 예 |

이 오류는 인증에 사용되는 인증서가 만료된 경우에 발생합니다.

인증서가 만료되었는지 확인하려면 다음 단계를 수행합니다.  
1. 인증서 MMC 스냅인을 열고, 컴퓨터 계정을 선택하고, Certificates (Local Computer)\Personal\Certificates로 이동합니다.
2. 클라이언트 인증 인증서가 만료되었는지 확인합니다.

클라이언트 인증 인증서가 만료된 경우 다음 단계를 수행하여 문제를 해결합니다.

1. Azure 파일 동기화 에이전트 버전 4.0.1.0 이상이 설치되어 있는지 확인합니다.
2. 서버에서 다음 PowerShell 명령을 실행 합니다.

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**인증 인증서를 찾을 수 없어 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT(10진)** | -2134375896 |
| **오류 문자열** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 인증에 사용된 인증서를 찾을 수 없기 때문에 발생합니다.

이 문제를 해결하려면 다음 단계를 수행하십시오.

1. Azure 파일 동기화 에이전트 버전 4.0.1.0 이상이 설치되어 있는지 확인합니다.
2. 서버에서 다음 PowerShell 명령을 실행 합니다.

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**인증 id를 찾을 수 없어 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT(10진)** | -2134364039 |
| **오류 문자열** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 서버 엔드포인트 삭제가 실패하여 엔드포인트가 현재 부분적으로 삭제된 상태이기 때문에 발생합니다. 이 문제를 해결하려면 서버 엔드포인트를 다시 삭제합니다.

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
| **재구성 필요** | 아니요 |

이 오류는 Azure 파일 공유에 이미 존재 하는 콘텐츠를 사용 하 여 클라우드 엔드포인트을 만들었기 때문에 발생 합니다. 서버 엔드포인트에서 초기 동기화를 진행 하도록 허용 하기 전에 모든 콘텐츠에 대해 Azure 파일 공유를 검사 해야 Azure File Sync.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**여러 개별 파일의 문제로 인해 동기화가 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT(10진)** | -2134375877 |
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
> Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅샷을 만듭니다.

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
| **재구성 필요** | 아니요 |

이 오류는 Azure 파일 동기화 서비스를 사용할 수 없기 때문에 발생합니다. 이 오류는 Azure 파일 동기화 서비스를 다시 사용할 수 있는 경우 자동으로 해결됩니다.

<a id="-2146233088"></a>**예외로 인해 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0 x 80131500 |
| **HRESULT(10진)** | -2146233088 |
| **오류 문자열** | COR_E_EXCEPTION |
| **재구성 필요** | 아니요 |

이 오류는 예외로 인해 동기화가 실패한 경우에 발생합니다. 오류가 몇 시간 동안 지속 되 면 지원 요청을 만드세요.

<a id="-2134364045"></a>**저장소 계정이 다른 지역으로 장애 조치 (failover) 되었으므로 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT(10진)** | -2134364045 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **재구성 필요** | 예 |

스토리지 계정이 다른 지역으로 장애 조치(failover)된 경우에 이 오류가 발생합니다. Azure 파일 동기화는 스토리지 계정 장애 조치(failover) 기능을 지원하지 않습니다. Azure 파일 동기화에서 클라우드 엔드포인트로 사용되는 Azure 파일 공유를 포함하는 스토리지 계정은 장애 조치(failover)하지 않아야 합니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. 이 문제를 해결하려면 스토리지 계정을 주 지역으로 이동합니다.

<a id="-2134375922"></a>**동기화 데이터베이스에 일시적인 문제가 있어 동기화에 실패했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT(10진)** | -2134375922 |
| **오류 문자열** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **재구성 필요** | 아니요 |

이 오류는 동기화 데이터베이스에 내부 문제가 있어서 발생합니다. 이 오류는 동기화를 다시 시도하면 자동으로 해결됩니다. 이 문제가 오래 지속되는 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2134364024"></a>**Azure Active Directory 테넌트의 변경으로 인해 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT(10진)** | -2134364024 | 
| **오류 문자열** | ECS_E_INVALID_AAD_TENANT |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화가 현재 다른 Azure Active Directory 테넌트로 구독을 이동하는 작업을 지원하지 않기 때문에 발생합니다.
 
이 문제를 해결하려면 다음 옵션 중 하나를 수행합니다.

- **옵션 1 (권장)** : 구독을 원래 Azure Active Directory 테넌트로 다시 이동 합니다.
- **옵션 2**: 현재 동기화 그룹을 삭제 하 고 다시 만듭니다. 서버 엔드포인트에서 클라우드 계층화를 사용하도록 설정한 경우, 동기화 그룹을 삭제한 다음 [클라우드 계층화 섹션]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)에 설명된 단계를 수행하여 동기화 그룹을 다시 만들기 전에 분리되고 계층화된 파일을 제거합니다. 

<a id="-2134364010"></a>**방화벽 및 가상 네트워크 예외가 구성 되지 않아 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT(10진)** | -2134364010 | 
| **오류 문자열** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **재구성 필요** | 예 |

이 오류는 저장소 계정에서 방화벽 및 가상 네트워크 설정을 사용 하도록 설정 하 고 "신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용" 예외를 선택 하지 않은 경우에 발생 합니다. 이 문제를 해결하려면 배포 가이드의 [방화벽 및 가상 네트워크 설정 구성](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 섹션에 설명된 단계를 수행합니다.

<a id="-2147024891"></a>**시스템 볼륨 정보 폴더에 대 한 권한이 잘못 되어 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT(10진)** | -2147024891 |
| **오류 문자열** | ERROR_ACCESS_DENIED |
| **재구성 필요** | 예 |

이 오류는 NT AUTHORITY\SYSTEM 계정에 서버 엔드포인트가 있는 볼륨의 시스템 볼륨 정보 폴더에 대한 권한이 없는 경우에 발생할 수 있습니다. 개별 파일이 ERROR_ACCESS_DENIED와 동기화 되지 않는 경우 [파일/디렉터리 동기화 오류 당 문제 해결](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors) 섹션에 설명 된 단계를 수행 합니다.

이 문제를 해결하려면 다음 단계를 수행하십시오.

1. [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) 도구를 다운로드합니다.
2. 관리자 권한 명령 프롬프트에서 다음 명령을 실행 하 여 시스템 계정: PsExec-i-s- **d cmd** 를 사용 하 여 명령 프롬프트를 시작 합니다. 
3. 시스템 계정으로 실행되는 명령 프롬프트에서 다음 명령을 실행하여 NT AUTHORITY\SYSTEM 계정에 시스템 볼륨 정보 폴더에 대한 액세스 권한이 없는지 확인합니다. **cacls "drive letter:\system volume information" /T /C**
4. NT AUTHORITY\SYSTEM 계정에 시스템 볼륨 정보 폴더에 대한 액세스 권한이 없는 경우 다음 명령을 실행합니다. **cacls  "drive letter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - 액세스 거부로 인해 4단계가 실패한 경우 다음 명령을 실행하여 시스템 볼륨 정보 폴더의 소유권을 가져온 다음 4단계를 반복합니다. **takeown /A /R /F "drive letter:\System Volume Information"**

<a id="-2134375810"></a>**Azure 파일 공유가 삭제 되 고 다시 생성 되었기 때문에 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT(10진)** | -2134375810 |
| **오류 문자열** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화가 동일한 동기화 그룹에서 Azure 파일 공유를 삭제하고 다시 만드는 작업을 지원하지 않기 때문에 발생합니다. 

이 문제를 해결하려면 다음 단계를 수행하여 동기화 그룹을 삭제하고 다시 만듭니다.

1. 동기화 그룹의 모든 서버 엔드포인트을 삭제 합니다.
2. 클라우드 엔드포인트을 삭제 합니다. 
3. 동기화 그룹을 삭제 합니다.
4. 서버 엔드포인트에서 클라우드 계층화를 사용 하도록 설정한 경우 서버 [엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없습니다](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) . 섹션을 수행 하 여 서버에서 분리 된 계층화 된 파일을 삭제 합니다.
5. 동기화 그룹을 다시 만듭니다.

<a id="-2145844941"></a>**HTTP 요청이 리디렉션 되어 동기화 하지 못했습니다.**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT(10진)** | -2145844941 |
| **오류 문자열** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **재구성 필요** | 예 |

이 오류는 Azure File Sync에서 HTTP 리디렉션 (3xx 상태 코드)을 지원 하지 않기 때문에 발생 합니다. 이 문제를 해결 하려면 프록시 서버 또는 네트워크 장치에서 HTTP 리디렉션을 사용 하지 않도록 설정 합니다.

<a id="-2134364027"></a>**오프 라인 데이터를 전송 하는 동안 시간 초과가 발생 했지만 아직 진행 중입니다.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT(10진)** | -2134364027 |
| **오류 문자열** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **재구성 필요** | 아니요 |

이 오류는 데이터 수집 작업이 시간 제한을 초과 하는 경우에 발생 합니다. 동기화가 진행 중인 경우이 오류를 무시할 수 있습니다 (AppliedItemCount가 0 보다 큼). [현재 동기화 세션의 진행률을 모니터링 하 어떻게 할까요? 모니터링](#how-do-i-monitor-the-progress-of-a-current-sync-session)을 참조 하세요.

### <a name="common-troubleshooting-steps"></a>일반적인 문제 해결 단계
<a id="troubleshoot-storage-account"></a>**스토리지 계정이 있는지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 스토리지 동기화 서비스 내에서 동기화 그룹으로 이동합니다.
2. 동기화 그룹 내에서 클라우드 엔드포인트를 선택합니다.
3. 열리는 창에서 Azure 파일 공유 이름을 확인합니다.
4. 연결된 스토리지 계정을 선택합니다. 이 연결이 실패하면 참조되는 스토리지 계정이 제거된 것입니다.
    ![스토리지 계정에 연결된 클라우드 엔드포인트 세부 정보를 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

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

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Azure 파일 공유가 있는지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 왼쪽의 목차에서 **개요**를 클릭하여 기본 스토리지 계정 페이지로 돌아갑니다.
2. **파일**을 선택하여 파일 공유 목록을 봅니다.
3. 클라우드 엔드포인트에서 참조하는 파일 공유가 파일 공유 목록에 나타나는지 확인합니다(위의 1단계에서 적어두었어야 합니다).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.**  
# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
1. 왼쪽 목차에서 **액세스 제어(IAM)** 를 클릭합니다.
1. **역할 할당** 탭을 클릭하여 스토리지 계정에 액세스할 수 있는 사용자 및 애플리케이션(‘서비스 주체’)을 나열합니다.
1. 목록에 **읽기 권한자 및 데이터 액세스** 역할과 함께 **하이브리드 파일 동기화 서비스**가 표시되는지 확인합니다. 

    ![저장소 계정의 액세스 제어 탭에서 하이브리드 파일 동기화 서비스 서비스 주체의 스크린샷](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **하이브리드 파일 동기화 서비스**가 목록에 표시되지 않는 경우 다음 단계를 수행합니다.

    - **추가**를 클릭합니다.
    - **역할** 필드에서 **읽기 권한자 및 데이터 액세스**를 선택합니다.
    - **선택** 필드에서 **하이브리드 파일 동기화 서비스**를 입력하고 역할을 선택한 다음, **저장**을 클릭합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
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
> 파일 화면은 Azure File Sync에서 지원 하지 않는 문자 생성을 차단 하는 데만 사용 해야 합니다. 다른 시나리오에서 파일 화면을 사용 하는 경우 동기화는 계속 해 서 Azure 파일 공유에서 서버로 파일을 다운로드 하 고 파일 화면으로 인해 차단 되므로 데이터를 대량으로 송신 합니다. 

## <a name="cloud-tiering"></a>클라우드 계층화 
클라우드 계층화에는 다음 두 가지 경로가 있습니다.

- 파일이 계층화되는 데 실패할 수 있습니다, 즉 Azure 파일 동기화에서 파일을 Azure Files로 성공적으로 계층화하지 못합니다.
- 파일을 회수하는 데 실패할 수 있습니다. 즉, 사용자가 계층화된 파일에 액세스하려고 하면 Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)에서 데이터를 다운로드하지 못합니다.

오류 경로 중 하나를 통해 발생할 수 있는 두 가지 주요 오류 클래스가 있습니다.

- 클라우드 스토리지 오류
    - *일시적인 스토리지 서비스 가용성 문제* - 자세한 내용은 [Azure Storage에 대한 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)를 참조하세요.
    - *액세스할 수 없는 Azure 파일 공유* - 일반적으로 Azure 파일 공유가 여전히 동기화 그룹의 클라우드 엔드포인트일 때 Azure 파일 공유를 삭제하면 이 오류가 발생합니다.
    - *액세스할 수 없는 스토리지 계정* - 일반적으로 동기화 계정의 클라우드 엔드포인트인 Azure 파일 공유가 있는 동안 스토리지 계정을 삭제하면 이 오류가 발생합니다. 
- 서버 오류 
  - *Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)가 로드되지 않음* - 계층화/회수 요청에 응답하려면 Azure 파일 동기화 파일 시스템 필터를 로드해야 합니다. 로드되지 않는 필터는 여러 가지 이유로 발생할 수 있지만, 가장 일반적인 이유는 관리자가 필터를 수동으로 언로드했기 때문입니다. Azure 파일 동기화가 제대로 작동하려면 Azure 파일 동기화 파일 시스템 파일 필터가 항상 로드되어야 합니다.
  - *누락되었거나 손상되었거나 끊어진 재분석 지점* - 재분석 지점은 다음 두 부분으로 구성된 파일의 특별한 데이터 구조입니다.
    1. 재분석 태그: Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)가 파일에 대한 IO 작업 일부를 수행해야 하는 운영 체제를 나타냅니다. 
    2. 재분석 데이터: 파일 시스템 필터에 연결된 클라우드 엔드포인트(Azure 파일 공유)에 있는 파일의 URI를 나타냅니다. 
        
       재분석 지점이 손상될 수 있는 가장 일반적인 방법은 관리자가 태그 또는 해당 데이터를 수정하려고 하는 경우입니다. 
  - *네트워크 연결 문제* - 파일을 계층화하거나 회수하려면 서버에 인터넷 연결이 있어야 합니다.

다음 섹션에서는 클라우드 계층화 문제를 해결하고 클라우드 스토리지 문제 또는 서버 문제인지 여부를 확인하는 방법에 대해 설명합니다.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>서버에서 계층화 작업을 모니터링 하는 방법  
서버의 계층화 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016 및 9029를 사용합니다.

- 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 예를 들어 총 오류 수, ErrorCode 등이 있습니다. 오류 코드 당 하나의 이벤트가 기록 됩니다.
- 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 사용 가능한 공간 비율, 세션에서 고스팅된 파일 수, 고스팅에 실패한 파일 수 등을 예로 들 수 있습니다.
- 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 세션에서 시도된 파일 수, 세션에서 계층화된 파일 수, 이미 계층화된 파일 수 등을 예로 들 수 있습니다.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>서버에서 회수 활동을 모니터링 하는 방법
서버의 회수 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9005, 9006,9009 및 9059를 사용합니다.

- 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 액세스된 고유한 파일 수, 액세스에 실패한 고유한 파일 수 등을 예로 들 수 있습니다.
- 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 예를 들어 실패 한 총 요청, ErrorCode 등이 있습니다. 오류 코드 당 하나의 이벤트가 기록 됩니다.
- 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed 등을 예로 들 수 있습니다.
- 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. ShareId, Application Name, TotalEgressNetworkBytes 등을 예로 들 수 있습니다.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>계층화에 실패한 파일의 문제 해결 방법
파일이 Azure Files로 계층화되지 못한 경우:

1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다. 
   1. Azure 파일 공유에 파일이 있는지 확인합니다.

      > [!NOTE]
      > 계층화하려면 파일을 먼저 Azure 파일 공유에 동기화해야 합니다.

   2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
   3. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
       - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

> [!NOTE]
> 파일이 계층화에 실패하면 이벤트 ID 9003이 원격 분석 이벤트 로그에 1시간에 한 번 기록됩니다(오류 코드 하나당 이벤트 하나가 기록). [계층화 오류 및 재구성](#tiering-errors-and-remediation) 섹션을 확인 하 여 오류 코드에 대 한 수정 단계가 나열 되는지 확인 합니다.

### <a name="tiering-errors-and-remediation"></a>계층화 오류 및 수정

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | 파일이 사용 중이기 때문에 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 파일이 더 이상 사용 되지 않을 때 계층화 됩니다. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | 파일이 동기화에 의해 제외 되어 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 동기화 제외 목록의 파일은 계층화 할 수 없습니다. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | 서버에서 파일을 찾을 수 없으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 오류가 계속 발생 하면 서버에 파일이 있는지 확인 하십시오. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | 파일이 Azure 파일 공유에서 삭제 되었으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 다음 다운로드 동기화 세션이 실행 될 때 서버에서 파일을 삭제 해야 합니다. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | 네트워크 문제로 인해 파일을 계층화 하지 못했습니다. | 작업이 필요하지 않습니다. 오류가 계속 발생 하면 Azure 파일 공유에 대 한 네트워크 연결을 확인 합니다. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | 네트워크 문제로 인해 파일을 계층화 하지 못했습니다. | 작업이 필요하지 않습니다. 오류가 계속 발생 하면 Azure 파일 공유에 대 한 네트워크 연결을 확인 합니다. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 액세스 거부 오류로 인해 파일을 계층화 하지 못했습니다. 파일이 DFS-R 읽기 전용 복제 폴더에 있는 경우이 오류가 발생할 수 있습니다. | Azure 파일 동기화는 DFS-R 읽기 전용 복제 폴더에서 서버 엔드포인트를 지원하지 않습니다. 자세한 내용은 [계획 가이드](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) 를 참조 하세요. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | 네트워크 문제로 인해 파일을 계층화 하지 못했습니다. | 작업이 필요하지 않습니다. 오류가 계속 발생 하면 Azure 파일 공유에 대 한 네트워크 연결을 확인 합니다. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | 파일 크기가 지원 되는 크기 보다 작으므로 파일을 계층화 하지 못했습니다. | 에이전트 버전이 9.0 보다 작은 경우 지원 되는 최소 파일 크기는 64kb입니다. 에이전트 버전이 9.0 이상이 면 지원 되는 최소 파일 크기는 파일 시스템 클러스터 크기 (이중 파일 시스템 클러스터 크기)를 기반으로 합니다. 예를 들어 파일 시스템 클러스터 크기가 4kb 이면 최소 파일 크기는 8kb입니다. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Azure 저장소 문제로 인해 파일을 계층화 하지 못했습니다. | 오류가 지속 되 면 지원 요청을 엽니다. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | 파일이 동시에 회수 되었으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 회수가 완료 되 고 파일이 더 이상 사용 되지 않는 경우 파일이 계층화 됩니다. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | 파일이 Azure 파일 공유와 동기화 되지 않았으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 이 파일은 Azure 파일 공유와 동기화 된 후 계층에 있습니다. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | 클라우드 계층화 필터 드라이버 (microsoft.storagesync)가 실행 되 고 있지 않으므로 파일을 계층화 하지 못했습니다. | 이 문제를 해결 하려면 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행 합니다. `fltmc load storagesync`<br>Fltmc 명령을 실행할 때 microsoft.storagesync 필터 드라이버를 로드 하지 못한 경우 Azure File Sync 에이전트를 제거 하 고 서버를 다시 시작한 후 Azure File Sync 에이전트를 다시 설치 하십시오. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 서버 엔드포인트이 있는 볼륨의 디스크 공간이 부족 하 여 파일을 계층화 하지 못했습니다. | 이 문제를 해결 하려면 서버 엔드포인트이 있는 볼륨에서 100 이상의 디스크 공간을 확보 합니다. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | 파일이 Azure 파일 공유와 동기화 되지 않았으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 이 파일은 Azure 파일 공유와 동기화 된 후 계층에 있습니다. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | 지원 되지 않는 재분석 지점 이므로 파일을 계층화 하지 못했습니다. | 파일이 데이터 중복 제거 재분석 지점인 경우 [계획 가이드](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) 의 단계에 따라 데이터 중복 제거 지원을 사용 하도록 설정 합니다. 데이터 중복 제거 이외의 재분석 지점이 있는 파일은 지원 되지 않으며 계층화 되지 않습니다.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | 파일이 수정 되었으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 수정 된 파일이 Azure 파일 공유와 동기화 되 면 파일이 계층에 포함 됩니다. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | 파일이 Azure 파일 공유와 동기화 되지 않았으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 이 파일은 Azure 파일 공유와 동기화 된 후 계층에 있습니다. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | 네트워크 문제로 인해 파일을 계층화 하지 못했습니다. | 작업이 필요하지 않습니다. 오류가 계속 발생 하면 Azure 파일 공유에 대 한 네트워크 연결을 확인 합니다. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 파일이 수정 되었으므로 계층에 오류가 발생 했습니다. | 작업이 필요하지 않습니다. 수정 된 파일이 Azure 파일 공유와 동기화 되 면 파일이 계층에 포함 됩니다. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 시스템 리소스 부족으로 인해 파일을 계층화 하지 못했습니다. | 오류가 계속 발생 하면 시스템 리소스를 소모 하는 응용 프로그램 또는 커널 모드 드라이버를 조사 하십시오. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>회수에 실패한 파일의 문제 해결 방법  
파일을 회수하지 못한 경우:
1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다.
    1. Azure 파일 공유에 파일이 있는지 확인합니다.
    2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
    3. 서비스 MMC 스냅인을 열고 스토리지 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
    4. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
        - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

> [!NOTE]
> 파일이 회수에 실패하면 이벤트 ID 9006이 원격 분석 이벤트 로그에 1시간에 한 번 기록됩니다(오류 코드 하나당 이벤트 하나가 기록). [회수 오류 및](#recall-errors-and-remediation) 해결 방법 섹션을 확인 하 여 오류 코드에 대 한 수정 단계가 나열 되는지 확인 합니다.

### <a name="recall-errors-and-remediation"></a>회수 오류 및 수정

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | I/o 시간 제한으로 인해 파일을 회수 하지 못했습니다. 이 문제는 여러 가지 이유로 발생할 수 있습니다. 예를 들어 서버 리소스 제약 조건, 네트워크 연결 불량 또는 Azure storage 문제 (예: 제한)가 발생할 수 있습니다. | 작업이 필요하지 않습니다. 오류가 몇 시간 동안 지속되면 지원 사례를 열어 주세요. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | 네트워크 문제로 인해 파일을 회수 하지 못했습니다.  | 오류가 계속 발생 하면 Azure 파일 공유에 대 한 네트워크 연결을 확인 합니다. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | 서버 엔드포인트이 삭제 되어 파일을 회수 하지 못했습니다. | 이 문제를 해결 하려면 [서버 엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없음](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조 하세요. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 액세스 거부 오류로 인해 파일을 회수 하지 못했습니다. 저장소 계정에 대 한 방화벽 및 가상 네트워크 설정을 사용 하도록 설정 하 고 서버에 저장소 계정에 대 한 액세스 권한이 없는 경우이 문제가 발생할 수 있습니다. | 이 문제를 해결 하려면 배포 가이드의 [방화벽 및 가상 네트워크 설정 구성](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 섹션에 설명 된 단계를 수행 하 여 서버 IP 주소 또는 가상 네트워크를 추가 합니다. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Azure 파일 공유에서 액세스할 수 없기 때문에 파일을 회수 하지 못했습니다. | 이 문제를 해결 하려면 파일이 Azure 파일 공유에 있는지 확인 합니다. 파일이 Azure 파일 공유에 있으면 최신 Azure File Sync [에이전트 버전](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)으로 업그레이드 합니다. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | 저장소 계정에 대 한 권한 부여 실패로 인해 파일을 회수 하지 못했습니다. | 이 문제를 해결 하려면 [저장소 계정에 대 한 액세스 권한이 Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac)있는지 확인 하세요. |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Azure 파일 공유에 액세스할 수 없으므로 파일을 회수 하지 못했습니다. | 파일 공유가 있고 액세스할 수 있는지 확인 하십시오. 파일 공유를 삭제 하 고 다시 만든 경우 동기화 그룹을 삭제 하 고 다시 만들기 위해 [Azure 파일 공유가 삭제 되 고 다시 생성 되었으므로 동기화 실패](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) 에 설명 된 단계를 수행 합니다. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 시스템 리소스가 부족 하 여 파일을 회수 하지 못했습니다. | 오류가 계속 발생 하면 시스템 리소스를 소모 하는 응용 프로그램 또는 커널 모드 드라이버를 조사 하십시오. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Insuffcient 메모리로 인해 파일을 회수 하지 못했습니다. | 오류가 계속 발생 하면 메모리 부족 상태를 발생 시키는 응용 프로그램 또는 커널 모드 드라이버를 조사 하십시오. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 디스크 공간이 부족 하 여 파일을 회수 하지 못했습니다. | 이 문제를 해결 하려면 파일을 다른 볼륨으로 이동 하 여 볼륨의 공간을 확보 하거나, 볼륨의 크기를 늘리거나, 호출-StorageSyncCloudTiering cmdlet을 사용 하 여 파일을 계층으로 강제 합니다. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>서버 엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없음
서버 엔드포인트을 삭제 하기 전에 파일을 회수 하지 않으면 서버의 계층화 된 파일에 액세스할 수 없게 됩니다.

계층화 된 파일에 액세스할 수 없는 경우 기록 되는 오류
- 파일을 동기화 할 때 오류 코드-2147942467 (0x80070043-ERROR_BAD_NET_NAME)이 ItemResults 이벤트 로그에 기록 됩니다.
- 파일을 회수할 때 오류 코드-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND)이 대체 Allresults 이벤트 로그에 기록 됩니다.

다음 조건이 충족되면 계층화된 파일에 대한 액세스를 복원할 수 있습니다.
- 서버 엔드포인트가 지난 30일 이내에 삭제됨
- 클라우드 엔드포인트가 삭제되지 않음 
- 파일 공유가 삭제되지 않음
- 동기화 그룹이 삭제되지 않았습니다.

위의 조건을 충족하는 경우 30일 이내에 동일한 동기화 그룹 내의 서버에서 동일한 경로에 서버 엔드포인트를 다시 만들어 서버에 있는 파일에 대한 액세스를 복원할 수 있습니다. 

위의 조건이 충족되지 않으면 서버에서 계층화된 파일이 이제 분리되기 때문에 액세스를 복원하지 못할 수 있습니다. 분리 된 계층화 된 파일을 제거 하려면 아래 지침을 따르세요.

**참고 사항**
- 서버에서 계층화 된 파일에 액세스할 수 없는 경우 Azure 파일 공유에 직접 액세스 하는 경우에도 전체 파일에 액세스할 수 있어야 합니다.
- 나중에 분리 된 계층화 된 파일을 방지 하려면 서버 엔드포인트을 삭제할 때 [서버 엔드포인트 제거](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) 에 설명 된 단계를 따르세요.

<a id="get-orphaned"></a>**분리 된 계층화 된 파일 목록을 가져오는 방법** 

1. 에이전트 버전 v 5.1 이상이 설치 되어 Azure File Sync 확인 합니다.
2. 다음 PowerShell 명령을 실행 하 여 분리 된 계층화 된 파일을 나열 합니다.
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 파일이 삭제 된 후 백업에서 복원 해야 하는 경우에 OrphanTieredFiles 출력 파일을 저장 합니다.

<a id="remove-orphaned"></a>**분리 된 계층화 된 파일을 제거 하는 방법** 

*옵션 1: 분리 된 계층화 된 파일 삭제*

이 옵션은 Windows Server에서 분리 된 계층화 된 파일을 삭제 하지만 30 일 후에는 다시 만들기로 인해 존재 하거나 다른 동기화 그룹에 연결 된 경우 서버 엔드포인트을 제거 해야 합니다. 파일 충돌은 서버 엔드포인트을 다시 만들기 전에 Windows Server 또는 Azure 파일 공유에서 파일이 업데이트 되는 경우에 발생 합니다.

1. 에이전트 버전 v 5.1 이상이 설치 되어 Azure File Sync 확인 합니다.
2. Azure 파일 공유 및 서버 엔드포인트 위치를 백업 합니다.
3. [서버 엔드포인트 제거](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint)에 설명 된 단계를 수행 하 여 동기화 그룹에서 서버 엔드포인트을 제거 합니다 (있는 경우).

> [!Warning]  
> StorageSyncOrphanedTieredFiles cmdlet을 사용 하기 전에 서버 엔드포인트을 제거 하지 않으면 서버에서 분리 된 계층화 된 파일을 삭제 하면 Azure 파일 공유에서 전체 파일이 삭제 됩니다. 

4. 다음 PowerShell 명령을 실행 하 여 분리 된 계층화 된 파일을 나열 합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. 파일이 삭제 된 후 백업에서 복원 해야 하는 경우에 OrphanTieredFiles 출력 파일을 저장 합니다.
6. 다음 PowerShell 명령을 실행 하 여 분리 된 계층화 된 파일을 삭제 합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**참고 사항** 
- Azure 파일 공유에 동기화 되지 않은 서버에서 수정 된 계층화 된 파일은 삭제 됩니다.
- 액세스할 수 있는 (고아 아님) 계층화 된 파일은 삭제 되지 않습니다.
- 계층화 되지 않은 파일은 서버에 유지 됩니다.

7. 선택 사항: 3 단계에서 삭제 한 경우 서버 엔드포인트을 다시 만듭니다.

*옵션 2: Azure 파일 공유를 탑재 하 고 서버에서 분리 된 파일을 로컬로 복사*

이 옵션은 서버 엔드포인트을 제거 하지 않아도 되지만 전체 파일을 로컬로 복사 하는 데 충분 한 디스크 공간이 필요 합니다.

1. 분리 된 계층화 파일이 있는 Windows Server에서 Azure 파일 공유를 [탑재](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) 합니다.
2. 다음 PowerShell 명령을 실행 하 여 분리 된 계층화 된 파일을 나열 합니다.
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. OrphanTieredFiles 출력 파일을 사용 하 여 서버에서 분리 된 계층화 된 파일을 식별 합니다.
4. Azure 파일 공유에서 Windows Server로 전체 파일을 복사 하 여 분리 된 계층화 된 파일을 덮어씁니다.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>서버에서 예기치 않게 회수 되는 파일의 문제를 해결 하는 방법  
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
    > [!NOTE]
    >AFSDiag는 로그를 수집 하기 전에 출력 디렉터리의 모든 내용을 삭제 합니다. 데이터를 포함 하지 않는 출력 위치를 지정 하십시오.
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
