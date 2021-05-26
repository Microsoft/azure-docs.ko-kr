---
title: Azure Automation State Configuration 문제 해결
description: 이 문서에서는 Azure Automation State Configuration 문제를 해결하는 방법을 설명합니다.
services: automation
ms.subservice: ''
ms.date: 04/16/2019
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5fe977851011bdfa4f7bbf2bde24e5e4b6fd480d
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110457860"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Azure Automation State Configuration 문제 해결

이 문서에서는 Azure Automation State Configuration에서 구성을 컴파일하거나 배포할 때 발생하는 문제를 해결하는 방법에 대한 정보를 제공합니다. State Configuration 기능에 대한 일반 정보는 [Azure Automation State Configuration 개요](../automation-dsc-overview.md)를 참조하세요.

## <a name="diagnose-an-issue"></a>문제 진단

다음은 구성에 대한 컴파일 또는 배포 오류가 발생하는 경우 문제를 진단하는 데 도움이 되는 몇 가지 단계입니다.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 로컬 컴퓨터에서 구성이 성공적으로 컴파일되는지 확인

Azure Automation State Configuration은 PowerShell DSC(Desired State Configuration)를 기반으로 합니다. [PowerShell DSC 문서](/powershell/scripting/overview)에서 DSC 언어 및 구문에 대한 설명서를 찾을 수 있습니다.

로컬 컴퓨터에서 DSC 구성을 컴파일하면 다음과 같은 일반적인 오류를 검색하고 해결할 수 있습니다.

   - 모듈 없음
   - 구문 오류
   - 논리 오류

### <a name="2-view-dsc-logs-on-your-node"></a>2. 노드의 DSC 로그 보기

구성이 성공적으로 컴파일되지만 노드에 적용될 때 실패하는 경우 DSC 로그에서 자세한 정보를 찾을 수 있습니다. 이러한 로그가 있는 위치에 대한 자세한 내용은 [DSC 이벤트 로그의 위치](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)를 참조하세요.

[xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) 모듈은 DSC 로그에서 자세한 정보를 구문 분석하는 데 도움이 될 수 있습니다. 고객 지원팀에 문의하는 경우 문제를 진단하려면 이러한 로그가 필요합니다.

[안정적인 버전 모듈 설치](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module) 지침에 따라 로컬 컴퓨터에 `xDscDiagnostics` 모듈을 설치할 수 있습니다.

Azure 컴퓨터에 `xDscDiagnostics` 모듈을 설치하려면 [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand)를 사용합니다. [실행 명령을 사용하여 Windows VM에서 PowerShell 스크립트 실행](../../virtual-machines/windows/run-command.md) 단계에 따라 Azure Portal에서 **명령 실행** 옵션을 사용할 수도 있습니다.

**xDscDiagnostics** 를 사용하는 방법에 대한 자세한 내용은 [xDscDiagnostics를 사용하여 DSC 로그 분석](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)을 참조하세요. [xDscDiagnostics Cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)도 참조하세요.

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 노드 및 Automation 작업 영역에 필수 모듈이 있는지 확인

DSC는 노드에 설치된 모듈에 종속됩니다. Azure Automation State Configuration을 사용하는 경우 [모듈 가져오기](../shared-resources/modules.md#import-modules)의 단계를 수행하여 필요한 모듈을 Automation 계정으로 가져옵니다. 구성도 모듈의 특정 버전에 대한 종속성을 가질 수 있습니다. 자세한 내용은 [모듈 문제 해결](shared-resources.md#modules)을 참조하세요.

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>시나리오: 특수 문자가 포함된 구성을 포털에서 삭제할 수 없음

### <a name="issue"></a>문제

포털에서 DSC 구성을 삭제하려고 하면 다음과 같은 오류가 표시됩니다.

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>원인

이 오류는 일시적인 문제이므로 곧 해결됩니다.

### <a name="resolution"></a>해결 방법

[Remove-AzAutomationDscConfiguration](/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration) cmdlet을 사용하여 구성을 삭제합니다.

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>시나리오: DSC 에이전트를 등록하지 못함

### <a name="issue"></a>문제

[Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) 또는 다른 DSC cmdlet 사용 시 다음 오류가 표시됩니다.

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>원인

이 오류는 일반적으로 방화벽, 프록시 서버 뒤에 있는 컴퓨터 또는 기타 네트워크 오류로 인해 발생합니다.

### <a name="resolution"></a>해결 방법

컴퓨터에 DSC의 적절한 엔드포인트에 대한 액세스 권한이 있는지 확인하고 다시 시도합니다. 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-dsc-overview.md#network-planning)을 참조하세요.

## <a name="scenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"></a>시나리오: 상태 보고서가 권한 없음 응답 코드를 반환함

### <a name="issue"></a>문제

Azure Automation State Configuration을 사용하여 노드를 등록하면 다음 오류 메시지 중 하나가 표시됩니다.

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>원인

이 문제는 잘못되거나 만료된 인증서로 인해 발생합니다. [노드 다시 등록](../automation-dsc-onboarding.md#re-register-a-node)을 참조하세요.

이 문제는 * **.azure-automation.net** 에 대한 액세스를 허용하지 않는 프록시 구성으로 인해 발생할 수도 있습니다. 자세한 내용은 [개인 네트워크 구성](../automation-dsc-overview.md#network-planning)을 참조하세요. 

### <a name="resolution"></a>해결 방법

다음 단계를 사용하여 실패한 DSC 노드를 다시 등록합니다.

#### <a name="step-1-unregister-the-node"></a>1단계: 노드 등록 취소

1. Azure Portal에서 **홈** > **Automation 계정** > (사용자의 Automation 계정) > **State Configuration(DSC)** 으로 이동합니다.
1. **노드** 를 선택하고 문제가 있는 노드를 선택합니다.
1. **등록 취소** 를 선택하여 노드 등록을 취소합니다.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>2단계: 노드에서 DSC 확장 제거

1. Azure Portal에서 **홈** > **가상 머신** > (실패한 노드) > **확장** 으로 이동합니다.
1. PowerShell DSC 확장인 **Microsoft.Powershell.DSC** 를 선택합니다.
1. **제거** 를 선택하여 확장을 제거합니다.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>3단계: 노드에서 잘못되었거나 만료된 모든 인증서 제거

승격된 PowerShell 프롬프트에서 실패한 노드에 대해 다음 명령을 실행합니다.

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

#### <a name="step-4-reregister-the-failing-node"></a>4단계: 실패한 노드 다시 등록

1. Azure Portal에서 **홈** > **Automation 계정** > (사용자의 Automation 계정) > **State Configuration(DSC)** 으로 이동합니다.
1. **노드** 를 선택합니다.
1. **추가** 를 선택합니다.
1. 실패한 노드를 선택합니다.
1. **연결** 을 선택하고 원하는 옵션을 선택합니다.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>시나리오: 노드가 "찾을 수 없음" 오류로 실패한 상태임

### <a name="issue"></a>문제

노드에 실패 상태의 보고서가 있으며 오류가 포함되어 있습니다.

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>원인

이 오류는 일반적으로 노드가 노드 구성(MOF 파일) 이름(예: **ABC.WebServer**) 대신 구성 이름(예: **ABC**)에 할당된 경우에 발생합니다.

### <a name="resolution"></a>해결 방법

* 구성 이름이 아니라 노드 구성 이름으로 노드를 할당해야 합니다.
* Azure Portal 또는 PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당할 수 있습니다.

  * Azure Portal에서 **홈** > **Automation 계정** > (사용자의 Automation 계정) > **State Configuration(DSC)** 으로 이동합니다. 그런 다음 노드를 선택하고 **노드 구성 할당** 을 선택합니다.
  * [Set-AzAutomationDscNode](/powershell/module/Az.Automation/Set-AzAutomationDscNode) cmdlet을 사용합니다.

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>시나리오: 구성이 컴파일될 때 생성된 노드 구성(MOF 파일)이 없음

### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단됩니다.

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>원인

DSC 구성에서 `Node` 키워드 다음에 오는 식이 `$null`로 평가되면 노드 구성이 생성되지 않습니다.

### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 구성 정의에서 `Node` 키워드 옆의 식이 Null로 계산되지 않는지 확인합니다.
* 구성을 컴파일할 때 [ConfigurationData](../automation-dsc-compile.md)를 전달하는 경우 구성 데이터에서 구성에 필요한 값을 전달해야 합니다.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>시나리오: DSC 노드 보고서가 진행 중 상태로 중단됨

### <a name="issue"></a>문제

DSC 에이전트 출력:

```error
No instance found with given property values
```

### <a name="cause"></a>원인

WMF(Windows Management Framework) 버전을 업그레이드하고 WMI(Windows Management Instrumentation)가 손상되었습니다.

### <a name="resolution"></a>해결 방법

[DSC 알려진 문제 및 제한 사항](/powershell/scripting/wmf/known-issues/known-issues-dsc)의 지침을 따릅니다.

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>시나리오: DSC 구성에서 자격 증명을 사용할 수 없습니다.

### <a name="issue"></a>문제

DSC 컴파일 작업이 다음 오류로 인해 중단됨:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>원인

구성에서 자격 증명을 사용하지만 각 노드 구성에 대해 `PSDscAllowPlainTextPassword`를 true로 설정하는 데 적절한 `ConfigurationData`를 제공하지 않았습니다.

### <a name="resolution"></a>해결 방법

적절한 `ConfigurationData`를 전달하여 구성에서 언급한 각 노드의 구성에 대해 `PSDscAllowPlainTextPassword`를 true로 설정하도록 합니다. [Azure Automation State Configuration에서 DSC 구성 컴파일](../automation-dsc-compile.md)을 참조하세요.

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>시나리오: DSC 확장에서 컴퓨터를 사용하도록 설정하는 경우 "확장 프로그램 처리 실패" 오류 발생

### <a name="issue"></a>문제

DSC 확장을 사용하여 컴퓨터를 사용하도록 설정할 때 오류가 발생합니다.

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>원인

일반적으로 이 오류는 서비스에 없는 노드 구성 이름이 노드에 할당될 때 발생합니다.

### <a name="resolution"></a>해결 방법

* 서비스의 이름과 정확히 일치하는 이름을 사용하여 노드를 할당해야 합니다.
* 노드 구성 이름을 포함하지 않도록 선택할 수 있습니다. 그러면 노드가 사용하도록 설정되지만 노드 구성은 할당되지 않습니다.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>시나리오: PowerShell을 사용하여 노드를 등록할 때 "하나 이상의 오류가 발생함" 오류 발생

### <a name="issue"></a>문제

[Register-AzAutomationDSCNode](/powershell/module/az.automation/register-azautomationdscnode) 또는 [Register-AzureRMAutomationDSCNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)를 사용하여 노드를 등록하면 다음과 같은 오류가 표시됩니다.

```error
One or more errors occurred.
```

### <a name="cause"></a>원인

이 오류는 Automation 계정에서 사용하는 것과 다른 구독에서 노드를 등록하려고 할 때 발생합니다.

### <a name="resolution"></a>해결 방법

별도의 클라우드 또는 온-프레미스에 대해 정의된 것처럼 구독 간 노드를 처리합니다. 컴퓨터를 사용하도록 설정하는 다음 옵션 중 하나를 사용하여 노드를 등록합니다.

* Windows: [온-프레미스나 Azure/AWS 이외의 클라우드에 있는 실제/가상 Windows 컴퓨터](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines)
* Linux: [온-프레미스, Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines)

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>시나리오: "프로비저닝 실패" 오류 메시지

### <a name="issue"></a>문제

노드를 등록하면 다음과 같은 오류가 표시됩니다.

```error
Provisioning has failed
```

### <a name="cause"></a>원인

이 메시지는 노드와 Azure 간의 연결에 문제가 있을 때 발생합니다.

### <a name="resolution"></a>해결 방법

노드가 VPN(가상 사설망)에 있는지 또는 Azure에 연결 시 발생하는 다른 문제가 있는지 확인합니다. [기능 배포 문제 해결](onboarding.md)을 참조하세요.

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>시나리오: Linux에서 구성을 적용할 때 일반 오류로 인해 실패

### <a name="issue"></a>문제

Linux에서 구성을 적용할 때 오류가 발생합니다.

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>원인

**/tmp** 위치가 `noexec`로 설정되어 있으면 현재 버전의 DSC에서 구성을 적용하지 못합니다.

### <a name="resolution"></a>해결 방법

**/tmp** 위치에서 `noexec` 옵션을 제거합니다.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>시나리오: 노드 구성 이름이 겹치면 오류가 발생할 수 있음

### <a name="issue"></a>문제

단일 구성 스크립트를 사용하여 여러 노드 구성을 생성하고 일부 노드 구성 이름이 다른 이름의 하위 집합인 경우 컴파일 서비스에서 잘못된 구성을 할당할 수 있습니다. 이 문제는 단일 스크립트를 사용하여 노드당 구성 데이터가 있는 구성을 생성하는 경우에만 발생하며, 문자열의 시작 부분에서 이름이 겹치는 경우에만 발생합니다. 예를 들어 cmdlet을 사용하여 해시 테이블로 전달된 노드 데이터를 기반으로 구성을 생성하는 데 사용되는 단일 구성 스크립트가 있으며 노드 데이터에 **server** 및 **1server** 라는 이름의 서버가 포함되는 경우입니다.

### <a name="cause"></a>원인

컴파일 서비스의 알려진 문제입니다.

### <a name="resolution"></a>해결 방법

가장 좋은 해결 방법은 로컬로 또는 CI/CD 파이프라인에서 컴파일하고 노드 구성 MOF 파일을 서비스에 직접 업로드하는 것입니다. 서비스의 컴파일이 요구 사항인 경우 다음으로 가장 좋은 해결 방법은 이름이 겹치지 않도록 컴파일 작업을 분할하는 것입니다.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>시나리오: DSC 구성 업로드 시 게이트웨이 시간 초과 오류

#### <a name="issue"></a>문제

DSC 구성을 업로드할 때 `GatewayTimeout` 오류가 표시됩니다. 

### <a name="cause"></a>원인

컴파일하는 데 시간이 오래 걸리는 DSC 구성으로 인해 이 오류가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법

[Import-DSCResource](/powershell/scripting/dsc/configurations/import-dscresource) 호출에 `ModuleName` 매개 변수를 명시적으로 포함하여 DSC 구성을 더 빠르게 구문 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.
