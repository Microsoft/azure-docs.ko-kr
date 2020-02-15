---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: aeb15fbb8da44a203789e06a359cb664998602ab
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77123195"
---
Azure 파일 동기화 에이전트는 새 기능을 추가하고 문제를 해결하기 위해 주기적으로 업데이트됩니다. 사용할 수 있을 때 Microsoft 업데이트에서 Azure 파일 동기화 에이전트에 대한 업데이트를 가져오도록 구성하는 것이 좋습니다.

#### <a name="major-vs-minor-agent-versions"></a>주 및 부 에이전트 버전 비교
* 주 에이전트 버전에는 새로운 기능이 포함되는 경우가 많으며, 버전 번호의 첫 번째 부분에 증가하는 숫자가 있습니다. 예: \*2.\*.\*\*
* 부 에이전트 버전은 "패치"라고도 하며, 주 버전보다 더 자주 릴리스됩니다. 버그가 수정되고 기능이 향상되는 경우가 많지만, 새로운 기능은 없습니다. 예: \*\*.3.\*\*

#### <a name="upgrade-paths"></a>업그레이드 경로
Azure 파일 동기화 에이전트 업데이트를 설치하는 데 승인된 네 가지 테스트 방법이 있습니다. 
1. **(기본 설정) 에이전트 업데이트를 자동으로 다운로드하여 설치하도록 Microsoft 업데이트를 구성합니다.**  
    항상 모든 Azure 파일 동기화 업데이트를 수행하여 서버 에이전트에 대한 최신 수정 프로그램에 액세스할 수 있도록 하는 것이 좋습니다. Microsoft 업데이트는 업데이트를 자동으로 다운로드하고 설치하여 이 프로세스를 원활하게 합니다.
2. **AfsUpdater.exe를 사용하여 에이전트 업데이트를 다운로드하고 설치합니다.**  
    AfsUpdater.exe는 에이전트 설치 디렉터리에 있습니다. 실행 파일을 두 번 클릭하여 에이전트 업데이트를 다운로드하고 설치합니다. 
3. **Microsoft 업데이트 패치 파일 또는 .msp 실행 파일을 사용 하 여 기존 Azure File Sync 에이전트를 패치 합니다. 최신 Azure File Sync 업데이트 패키지는 [Microsoft 업데이트 카탈로그](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)에서 다운로드할 수 있습니다.**  
    .msp 실행 파일을 실행하면 Microsoft 업데이트에서 자동으로 사용하는 방식과 동일하게 Azure 파일 동기화 설치가 이전 업그레이드 경로에서 업그레이드됩니다. Microsoft 업데이트 패치를 적용하면 Azure 파일 동기화 설치의 현재 위치 업그레이드가 수행됩니다.
4. **[Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257)에서 최신 Azure File Sync 에이전트 설치 관리자를 다운로드 합니다.**  
    기존 Azure 파일 동기화 에이전트 설치를 업그레이드하려면 이전 버전을 제거한 다음, 다운로드한 설치 관리자에서 최신 버전을 설치합니다. Azure 파일 동기화 설치 관리자는 서버 등록, 동기화 그룹 및 다른 모든 설정을 관리합니다.

#### <a name="automatic-agent-lifecycle-management"></a>자동 에이전트 수명 주기 관리
에이전트 버전 6을 사용 하는 경우 파일 동기화 팀에 에이전트 자동 업그레이드 기능이 도입 되었습니다. 두 가지 모드 중 하나를 선택 하 고 서버에서 업그레이드를 시도 하는 유지 관리 기간을 지정할 수 있습니다. 이 기능은 에이전트가 만료 되는 것을 방지 하는 guardrail를 제공 하거나 현재 설정을 유지 하는 데 도움이 되도록 설계 되었습니다.
1. **기본 설정은** 에이전트가 만료 되는 것을 방지 하려고 시도 합니다. 에이전트에서 게시 된 만료 날짜 21 일 이내에 에이전트는 자체 업그레이드를 시도 합니다. 만료 전과 선택한 유지 관리 기간에 한 주에 한 번 업그레이드 시도를 시작 합니다. **이 옵션은 정기적인 Microsoft 업데이트 패치를 수행 하지 않아도 됩니다.**
1. 필요에 따라 새 에이전트 버전을 사용할 수 있게 되는 즉시 에이전트가 자동으로 업그레이드 되도록 선택할 수 있습니다 (현재 클러스터 된 서버에는 적용 되지 않음). 이 업데이트는 선택한 유지 관리 기간 동안 발생 하며 서버에서 일반 공급 되는 즉시 새로운 기능 및 향상 된 기능을 사용할 수 있도록 허용 합니다. 이는 주 에이전트 버전 및 서버에 대 한 일반 업데이트 패치를 제공 하는 권장 사항, 걱정 없는 설정입니다. 출시 된 모든 에이전트는 GA 품질을 갖습니다. 이 옵션을 선택 하면 Microsoft에서 최신 에이전트 버전을 비행 합니다. 클러스터 된 서버는 제외 됩니다. 플 라이팅 완료 되 면 [Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257) aka.ms/AFS/agent에서 에이전트를 사용할 수 있게 됩니다.

 ##### <a name="changing-the-auto-upgrade-setting"></a>자동 업그레이드 설정 변경

다음 지침에서는 설정을 변경 해야 하는 경우 설치 관리자를 완료 한 후 설정을 변경 하는 방법을 설명 합니다.

PowerShell 콘솔을 열고 동기화 에이전트를 설치한 디렉터리로 이동한 후 서버 cmdlet을 가져옵니다. 기본적으로 다음과 같이 표시 됩니다.
```powershell
cd 'C:\Program Files\Azure\StorageSyncAgent'
Import-Module -Name .\StorageSync.Management.ServerCmdlets.dll
```

`Get-StorageSyncAgentAutoUpdatePolicy`를 실행 하 여 현재 정책 설정을 확인 하 고 변경할 것인지 여부를 결정할 수 있습니다.

현재 정책 설정을 지연 업데이트 트랙으로 변경 하려면 다음을 사용할 수 있습니다.
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration
```

현재 정책 설정을 즉시 업데이트 트랙으로 변경 하려면 다음을 사용할 수 있습니다.
```powershell
Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest
```

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>에이전트 수명 주기 및 변경 관리 보장
Azure File Sync은 새로운 기능 및 향상 된 기능을 지속적으로 도입 하는 클라우드 서비스입니다. 즉, 특정 Azure 파일 동기화 에이전트 버전은 제한된 시간 동안만 지원될 수 있습니다. 배포를 용이 하 게 하기 위해 다음 규칙을 통해 변경 관리 프로세스에서 에이전트 업데이트/업그레이드를 수용할 수 있는 충분 한 시간과 알림이 보장 됩니다.

- 주 에이전트 버전은 최초 릴리스 날짜로부터 최소 6개월 동안 지원됩니다.
- 주 에이전트 버전의 지원 사이에는 3개월 이상 겹치도록 보장합니다. 
- 만료되기 최소 3개월 전에 만료될 에이전트를 사용하여 등록된 서버에 경고가 발급됩니다. 등록된 서버가 Storage Sync Service의 등록된 서버 섹션 아래에서 이전 버전의 에이전트를 사용하고 있는지 확인할 수 있습니다.
- 부 에이전트 버전의 수명은 연결된 주 버전에 따라 결정됩니다. 예를 들어 에이전트 버전 3.0이 릴리스되면 에이전트 버전 2.\*와 함께 모두 만료되도록 설정됩니다.

> [!Note]
> 만료 경고가 있는 에이전트 버전을 설치하면 경고가 표시되지만 성공합니다. 만료된 에이전트 버전을 설치하거나 연결하려고 하면 지원되지 않고 차단됩니다.
