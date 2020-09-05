---
title: Azure에 하이브리드 머신을 대규모로 연결
description: 이 문서에서는 서비스 주체를 사용 하 여 Azure Arc 사용 서버 (미리 보기)를 사용 하 여 컴퓨터를 Azure에 연결 하는 방법을 알아봅니다.
ms.date: 07/23/2020
ms.topic: conceptual
ms.openlocfilehash: 07266ce7fb9579e1d4fb1b65394e0b7fdf7aa13d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211404"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Azure에 하이브리드 머신을 대규모로 연결

사용자의 요구 사항에 따라 몇 가지 유연한 옵션을 사용 하 여 사용자 환경에서 여러 Windows 또는 Linux 컴퓨터에 대해 Azure Arc 사용 서버 (미리 보기)를 사용 하도록 설정할 수 있습니다. 제공하는 템플릿 스크립트를 사용하여 Azure Arc 연결 설정 등 설치의 모든 단계를 자동화할 수 있습니다. 그러나 대상 머신 및 Azure에서 관리자 권한이 있는 계정을 사용하여 이 스크립트를 대화형으로 실행해야 합니다. 컴퓨터를 Azure Arc 사용 가능 서버 (미리 보기)에 연결 하려면 권한 있는 id를 사용 하 여 [컴퓨터를 대화형으로 연결](onboard-portal.md)하는 대신 Azure Active Directory [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md) 를 사용할 수 있습니다. 서비스 주체는 `azcmagent` 명령을 사용하여 Azure에 머신을 연결하는 데 필요한 최소한의 권한만 부여되는 특별히 한정된 관리 ID입니다. 이는 테넌트 관리자와 같은 더 높은 권한의 계정을 사용하는 것보다 안전하며, 액세스 제어 보안 모범 사례를 준수하는 것입니다. 서비스 주체는 온보딩하는 동안에만 사용되며 다른 용도에는 사용되지 않습니다.  

Connected Machine 에이전트를 설치하고 구성하는 설치 메서드는 사용하는 자동화 메서드에서 머신에 대한 관리자 권한을 요구합니다. Linux에서는 루트 계정을 사용하고 Windows에서는 로컬 관리자 그룹의 멤버여야 합니다.

시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 프로세스가 끝나면 하이브리드 컴퓨터가 Azure Arc 사용 서버 (미리 보기)에 성공적으로 연결 됩니다.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>대규모 온보딩을 위한 서비스 주체 만들기

[Azure PowerShell](/powershell/azure/install-az-ps)을 사용하여 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet으로 서비스 주체를 만들 수 있습니다. 또는 [Azure Portal을 사용하여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 아래에 나열된 단계를 수행하여 이 작업을 완료할 수 있습니다.

> [!NOTE]
> 서비스 주체를 만들 때는 계정이 온보딩에 사용할 구독의 소유자 또는 사용자 액세스 관리자여야 합니다. 역할 할당을 만들 수 있는 충분한 권한이 없는 경우 서비스 주체가 생성될 수 있지만 머신을 온보딩할 수 없습니다.
>

PowerShell을 사용하여 서비스 주체를 만들려면 다음을 수행합니다.

1. 다음 명령을 실행합니다. [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet의 출력을 변수에 저장해야 합니다. 그렇지 않으면 이후 단계에 필요한 암호를 검색할 수 없습니다.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. `$sp` 변수에 저장된 암호를 검색하려면 다음 명령을 실행합니다.

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 출력에서 **암호** 필드 아래에 있는 암호 값을 찾아 복사합니다. 또한 **ApplicationId** 필드에서 값을 찾아 복사합니다. 나중에 사용할 수 있도록 안전한 장소에 보관합니다. 서비스 주체 암호를 잊어버린 경우 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet을 사용하여 재설정할 수 있습니다.

다음 속성의 값은 `azcmagent`로 전달되는 매개 변수와 함께 사용됩니다.

* **ApplicationId** 속성의 값은 `--service-principal-id` 매개 변수 값에 사용됩니다.
* **암호** 속성의 값은 에이전트 연결에 사용되는 `--service-principal-secret` 매개 변수에 사용됩니다.

> [!NOTE]
> **Id** 속성이 아닌 서비스 주체 **ApplicationId** 속성을 사용해야 합니다.
>

**Azure Connected Machine 온보딩** 역할은 머신을 온보딩하는 데 필요한 사용 권한만 포함합니다. 해당 범위에 리소스 그룹 또는 구독을 포함할 수 있도록 서비스 주체 권한을 할당할 수 있습니다. 역할 할당을 추가하려면 [Azure RBAC 및 Azure Portal을 사용하여 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md) 또는 [Azure RBAC 및 Azure CLI를 사용하여 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조하세요.

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

다음 단계에서는 스크립트 템플릿을 사용하여 하이브리드 머신에 Connected Machine 에이전트를 설치하고 구성합니다. 이 템플릿은 [Azure Portal에서 Azure에 하이브리드 머신 연결](onboard-portal.md) 문서에 설명된 것과 비슷한 단계를 수행합니다. 차이점은 서비스 주체를 사용하여 `azcmagent` 명령으로 Azure Arc에 대한 연결을 설정하는 마지막 단계입니다.

다음은 `azcmagent` 명령이 서비스 주체에 사용하도록 구성하는 설정입니다.

* `tenant-id` : Azure AD의 전용 인스턴스를 나타내는 고유 식별자(GUID)입니다.
* `subscription-id` : 머신을 만들려는 Azure 구독의 구독 ID(GUID)입니다.
* `resource-group` : 연결된 머신을 배치할 리소스 그룹 이름입니다.
* `location` : [지원되는 Azure 지역](overview.md#supported-regions)을 참조하세요. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.
* `resource-name` : (*선택 사항*) 온-프레미스 머신의 Azure 리소스 표현에 사용됩니다. 이 값을 지정하지 않으면 머신 호스트 이름이 사용됩니다.

[Azcmagent 참조](./manage-agent.md)를 검토하여 `azcmagent` 명령줄 도구에 대해 자세히 알아볼 수 있습니다.

### <a name="windows-installation-script"></a>Windows 설치 스크립트

다음은 서비스 주체를 사용하여 완전 자동화된 비 대화형 에이전트 설치를 지원하도록 수정된 Windows용 Connected Machine 에이전트 설치 스크립트의 예입니다.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>이 스크립트는 64 비트 버전의 Windows PowerShell에서 실행 되는 것만 지원 합니다.
>

### <a name="linux-installation-script"></a>Linux 설치 스크립트

다음은 서비스 주체를 사용하여 완전 자동화된 비 대화형 에이전트 설치를 지원하도록 수정된 Linux용 Connected Machine 에이전트 설치 스크립트의 예입니다.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

>[!NOTE]
>**Azcmagent**를 실행 하려면 Linux 컴퓨터에 대 한 *루트* 액세스 권한이 있어야 합니다.

에이전트를 설치 하 고 Azure Arc 사용 서버 (미리 보기)에 연결 하도록 구성한 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

- [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.