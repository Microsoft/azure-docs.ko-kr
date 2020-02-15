---
title: 대규모로 하이브리드 컴퓨터를 Azure에 연결
description: 이 문서에서는 서비스 주체를 사용 하 여 서버 (미리 보기) 용 Azure Arc를 사용 하 여 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192273"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>대규모로 하이브리드 컴퓨터를 Azure에 연결

사용자의 요구 사항에 따라 다양 한 옵션을 사용 하 여 환경에서 여러 Windows 또는 Linux 컴퓨터에 대해 서버 (미리 보기)에 대 한 Azure Arc를 사용 하도록 설정할 수 있습니다. 제공 하는 템플릿 스크립트를 사용 하 여 Azure Arc 연결 설정 등 설치의 모든 단계를 자동화할 수 있습니다. 그러나 대상 컴퓨터와 Azure에서 높은 권한이 있는 계정을 사용 하 여이 스크립트를 대화형으로 실행 해야 합니다. 서버에 대 한 Azure Arc에 컴퓨터를 연결 하려면 권한 있는 id를 사용 하 여 [컴퓨터에 대화형으로 연결](onboard-portal.md)하는 대신 Azure Active Directory [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md) 를 사용할 수 있습니다. 서비스 주체는 `azcmagent` 명령을 사용 하 여 Azure에 컴퓨터를 연결 하는 데 필요한 최소 권한만 부여 된 특별 한 제한 된 관리 id입니다. 이는 테넌트 관리자와 같은 더 높은 권한의 계정을 사용 하는 것 보다 안전 하며, 액세스 제어 보안 모범 사례를 따릅니다. 서비스 주체는 등록 하는 동안에만 사용 되며 다른 용도에는 사용 되지 않습니다.  

연결 된 컴퓨터 에이전트를 설치 하 고 구성 하는 설치 방법에는 사용 하는 자동화 방법에 컴퓨터에 대 한 관리자 권한이 있어야 합니다. Linux에서 루트 계정 및 Windows에서 로컬 관리자 그룹의 구성원으로 사용 합니다.

시작 하기 전에 [필수 구성 요소](overview.md#prerequisites) 를 검토 하 고 구독과 리소스가 요구 사항을 충족 하는지 확인 해야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 프로세스가 끝나면 하이브리드 컴퓨터를 서버에 대 한 Azure Arc에 성공적으로 연결 하 게 됩니다.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>대규모로 온 보 딩을 위한 서비스 주체 만들기

[Azure PowerShell](/powershell/azure/install-az-ps) 를 사용 하 여 [AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet을 사용 하 여 서비스 주체를 만들 수 있습니다. 또는 [Azure Portal를 사용 하 여 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 에 나열 된 단계를 수행 하 여이 작업을 완료할 수 있습니다.

> [!NOTE]
> 서비스 주체를 만들 때 계정은 등록에 사용할 소유자 또는 사용자 액세스 관리자 여야 합니다. 역할 할당을 만들 수 있는 충분한 권한이 없는 경우 서비스 주체가 생성될 수 있지만 머신을 온보딩할 수 없습니다.
>

PowerShell을 사용 하 여 서비스 주체를 만들려면 다음을 수행 합니다.

1. 다음 명령을 실행합니다. [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet의 출력을 변수에 저장 해야 합니다. 그렇지 않으면 이후 단계에서 필요한 암호를 검색할 수 없습니다.

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

2. `$sp` 변수에 저장 된 암호를 검색 하려면 다음 명령을 실행 합니다.

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 출력에서 필드 **암호** 아래에 있는 암호 값을 찾아 복사 합니다. 또한 **ApplicationId** 필드에서 값을 찾아 복사 합니다. 나중에 안전한 장소에 저장 합니다. 서비스 사용자 암호를 잊거나 잃은 경우 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet을 사용 하 여 다시 설정할 수 있습니다.

다음 속성의 값은 `azcmagent`전달 되는 매개 변수와 함께 사용 됩니다.

* **ApplicationId** 속성의 값은 `--service-principal-id` 매개 변수 값에 사용 됩니다.
* **Password** 속성의 값은 에이전트를 연결 하는 데 사용 되는 `--service-principal-secret` 매개 변수에 사용 됩니다.

> [!NOTE]
> **Id** 속성이 아닌 서비스 사용자 **ApplicationId** 속성을 사용 해야 합니다.
>

**Azure 연결 된 컴퓨터 온 보 딩** 역할에는 컴퓨터를 등록 하는 데 필요한 사용 권한만 포함 됩니다. 해당 범위에서 리소스 그룹이 나 구독을 포함할 수 있도록 서비스 사용자 권한을 할당할 수 있습니다. 역할 할당을 추가 하려면 [AZURE rbac 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md) 또는 [azure rbac 및 Azure CLI를 사용 하 여 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)를 참조 하세요.

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치 하 고 Azure에 연결

다음 단계는 [Azure Portal에서 Azure에 하이브리드 컴퓨터 연결](onboard-portal.md) 문서에 설명 된 유사한 단계를 수행 하는 스크립트 템플릿을 사용 하 여 하이브리드 컴퓨터에 연결 된 컴퓨터 에이전트를 설치 하 고 구성 합니다. 차이점은 서비스 주체를 사용 하 여 `azcmagent` 명령을 사용 하 여 Azure Arc에 대 한 연결을 설정 하는 마지막 단계입니다. 

다음은 서비스 사용자에 대해 `azcmagent` 명령을 사용 하도록 구성 하는 설정입니다.

* `tenant-id`: Azure AD의 전용 인스턴스를 나타내는 고유 식별자 (GUID)입니다.
* `subscription-id`: 컴퓨터를 원하는 Azure 구독의 구독 ID (GUID)입니다.
* `resource-group`: 연결 된 컴퓨터를 포함할 리소스 그룹 이름입니다.
* `location`: [지원 되는 Azure 지역](overview.md#supported-regions)을 참조 하세요. 이 위치는 리소스 그룹의 위치와 같거나 다를 수 있습니다.
* `resource-name`: (*선택 사항*) 온-프레미스 컴퓨터의 Azure 리소스 표현에 사용 됩니다. 이 값을 지정 하지 않으면 컴퓨터 호스트 이름이 사용 됩니다.

[Azcmagent 참조](azcmagent-reference.md)를 검토 하 여 `azcmagent` 명령줄 도구에 대해 자세히 알아볼 수 있습니다.

### <a name="windows-installation-script"></a>Windows 설치 스크립트

다음은 서비스 주체를 사용 하 여 완전 자동화 되 고 비 대화형 에이전트 설치를 지원 하도록 수정 된 Windows 용 연결 된 컴퓨터 에이전트 설치 스크립트의 예입니다.

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

### <a name="linux-installation-script"></a>Linux 설치 스크립트

다음은 서비스 주체를 사용 하 여 완전 자동화 되 고 비 대화형 에이전트 설치를 지원 하도록 수정 된 Linux 용 연결 된 컴퓨터 에이전트 설치 스크립트의 예입니다.

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

에이전트를 설치 하 고 서버에 대 한 Azure Arc (미리 보기)에 연결 하도록 구성한 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 컴퓨터를 확인 합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)를 사용 하 여 컴퓨터를 관리 하는 방법, 컴퓨터에서 예상 Log Analytics 작업 영역에 보고 하는지 [확인,](../../governance/policy/concepts/guest-configuration.md)vm을 사용 하 여 [Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)모니터링 사용 등의 작업을 수행 하는 방법을 알아봅니다.

- [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. 컴퓨터에서 실행 되는 OS 및 워크 로드를 사전에 모니터링 하거나, 자동화 runbook 또는 업데이트 관리와 같은 솔루션을 사용 하 여 관리 하거나, [Azure Security Center](../../security-center/security-center-intro.md)같은 다른 Azure 서비스를 사용 하려는 경우 Windows 및 Linux 용 Log Analytics 에이전트가 필요 합니다.
