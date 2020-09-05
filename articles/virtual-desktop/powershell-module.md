---
title: PowerShell 모듈 Windows Virtual Desktop - Azure
description: Windows Virtual Desktop용 PowerShell 모듈을 설치하고 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c99aa1bdb246d242c4c1cbd33672367b66f923ea
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229975"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Windows Virtual Desktop에 대한 PowerShell 모듈 설정

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager 통합을 포함 하는 Windows 가상 데스크톱에 적용 됩니다.

Windows Virtual Desktop PowerShell 모듈은 Azure PowerShell 모듈에 통합되어 있습니다. 이 문서에서는 Windows Virtual Desktop에 대한 cmdlet을 실행할 수 있도록 PowerShell 모듈을 설정하는 방법을 설명합니다.

## <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

모듈 사용을 시작하려면 먼저 [최신 버전의 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)를 설치합니다. Windows Virtual Desktop cmdlet은 현재 PowerShell Core에서만 작동합니다.

다음으로 PowerShell 세션에서 사용할 DesktopVirtualization 모듈을 설치해야 합니다.

관리자 모드에서 다음 PowerShell cmdlet을 실행하여 모듈을 설치합니다.

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> 이 cmdlet이 작동하지 않는 경우 상승된 권한으로 다시 실행해 보세요.

그런 다음, 다음 cmdlet을 실행하여 Azure에 연결합니다.

```powershell
Connect-AzAccount
```

>! 중요 한 US Gov 포털에 연결 하는 경우 대신이 cmdlet을 실행 합니다.
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Azure 계정에 로그인하려면 Connect cmdlet을 실행할 때 생성되는 코드가 필요합니다. 로그인하려면 <https://microsoft.com/devicelogin>으로 이동하여 코드를 입력한 다음, Azure 관리자 자격 증명을 사용하여 로그인합니다.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

그러면 관리자 자격 증명에 대한 기본값인 구독에 직접 로그인됩니다.

## <a name="change-the-default-subscription"></a>기본 구독 변경

로그인한 후에 기본 구독을 변경하려면 이 cmdlet을 실행합니다.

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

또한 Out-GridView cmdlet을 사용 하 여 목록에서 하나를 선택할 수 있습니다.

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

사용할 새 구독을 선택하는 경우 나중에 실행하는 cmdlet에 해당 구독의 ID를 지정할 필요가 없습니다. 예를 들어 다음 cmdlet은 구독 ID를 필요로 하지 않고 특정 세션 호스트를 검색합니다.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

원하는 구독 이름을 매개 변수로 추가하여 cmdlet별로 구독을 변경할 수도 있습니다. 다음 cmdlet은 cmdlet에서 사용하는 구독을 변경하는 매개 변수로 추가된 구독 ID를 제외하고 이전 예제와 동일합니다.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>위치 가져오기

위치 매개 변수는 새 개체를 만드는 모든 **New-AzWVD** cmdlet에 대해 필수입니다.

다음 cmdlet을 실행하여 구독에서 지원하는 위치 목록을 가져옵니다.

```powershell
Get-AzLocation
```

**Get-AzLocation**에 대한 출력은 다음과 같습니다.

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

계정 위치를 확인한 후에는 cmdlet에서 사용할 수 있습니다. 예를 들어 "southeastasia" 위치에 호스트 풀을 만드는 cmdlet은 다음과 같습니다.

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>다음 단계

이제 PowerShell 모듈을 설정했으므로 Windows Virtual Desktop에서 모든 종류의 작업을 수행하는 cmdlet을 실행할 수 있습니다. 모듈을 사용할 수 있는 몇 가지 위치는 다음과 같습니다.

- [Windows Virtual Desktop 자습서]()를 실행하여 자신의 Windows Virtual Desktop 환경을 설정합니다.
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell.md)
- [Windows Virtual Desktop 부하 분산 방법 구성](configure-host-pool-load-balancing.md)
- [개인 데스크톱 호스트 풀 할당 유형 구성](configure-host-pool-personal-desktop-assignment-type.md)
- 이 외에도 많은 기능이 있습니다.

문제가 발생하는 경우 도움이 필요하면 [PowerShell 문제 해결 문서](troubleshoot-powershell.md)를 확인하세요.

