---
title: DSC (필요한 상태 구성) Azure Automation 문제 해결
description: 이 문서에는 DSC(Desired State Configuration) 문제 해결에 대한 정보가 제공됩니다.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3c3c9950aab9a5a422ebc9e858daded2888fd82e
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834268"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Azure Automation DSC (Desired State Configuration)의 문제 해결

이 문서에는 DSC(Desired State Configuration)에 관한 문제를 해결하는 방법에 대한 정보가 제공됩니다.

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>DSC (필요한 상태 구성) 문제를 해결 하는 단계

Azure 상태 구성에서 구성을 컴파일하거나 배포 하는 동안 오류가 발생 하는 경우 문제를 진단 하는 데 도움이 되는 몇 가지 단계가 있습니다.

1. **로컬 컴퓨터에서 구성이 성공적으로 컴파일되는지 확인 합니다.**  Azure 상태 구성은 PowerShell DSC를 기반으로 빌드됩니다. [POWERSHELL Dsc 문서](https://docs.microsoft.com/powershell/scripting/overview)에서 dsc 언어 및 구문에 대 한 설명서를 찾을 수 있습니다.

   로컬 컴퓨터에서 DSC 구성을 컴파일하면 다음과 같은 일반적인 오류를 검색 하 고 해결할 수 있습니다.

   - **누락 된 모듈**
   - **구문 오류**
   - **논리 오류**

2. **노드의 DSC 로그를 봅니다.** 구성이 성공적으로 컴파일되지만 노드에 적용 될 때 실패 하는 경우 로그에서 자세한 정보를 찾을 수 있습니다. DSC 로그를 찾을 수 있는 위치에 대 한 자세한 내용은 [Dsc 이벤트 로그 위치](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)를 참조 하세요.

   또한 [Xdscdiagnostics](https://github.com/PowerShell/xDscDiagnostics) 는 DSC 로그에서 자세한 정보를 구문 분석 하는 데 도움이 될 수 있습니다. 지원 서비스에 문의 하는 경우 문제를 진단 하려면 이러한 로그가 필요 합니다.

   [안정적인 버전 모듈 설치](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)에 나와 있는 지침을 사용 하 여 로컬 컴퓨터에 **xdscdiagnostics** 를 설치할 수 있습니다.

   Azure 컴퓨터에 **Xdscdiagnostics** 를 설치 하려면 [az vm run 명령을](/cli/azure/vm/run-command) 사용 하거나 [AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)를 사용 하면 됩니다. 실행 명령을 사용 하 여 [WINDOWS VM에서 PowerShell 스크립트 실행](../../virtual-machines/windows/run-command.md)에 있는 단계에 따라 포털에서 **실행 명령** 옵션을 사용할 수도 있습니다.

   **Xdscdiagnostics**사용에 대 한 자세한 내용은 Xdscdiagnostics를 [사용 하 여 DSC 로그 분석](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)및 [xdscdiagnostics cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)을 참조 하세요.
3. **노드 및 자동화 작업 영역에 필요한 모듈이 있는지 확인 합니다.** 필요한 상태 구성은 노드에 설치 된 모듈에 따라 달라 집니다.  Azure Automation 상태 구성을 사용 하는 경우 [모듈 가져오기](../shared-resources/modules.md#import-modules)에 나열 된 단계를 사용 하 여 필요한 모듈을 Automation 계정으로 가져옵니다. 구성도 모듈의 특정 버전에 대 한 종속성을 가질 수 있습니다.  자세한 내용은 [모듈 문제 해결](shared-resources.md#modules)을 참조 하세요.

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>DSC(필요한 상태 구성)으로 작업하는 경우 일반적인 오류 문제

### <a name="unsupported-characters"></a>시나리오: 포털에서 특수 문자를 사용 하 여 구성을 삭제할 수 없습니다.

#### <a name="issue"></a>문제

포털에서 DSC 구성을 삭제 하려고 하면 다음과 같은 오류가 표시 됩니다.

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>원인

이 오류는 해결 하기 위해 계획 된 일시적인 문제입니다.

#### <a name="resolution"></a>해상도

* Az Cmdlet "AzAutomationDscConfiguration"를 사용 하 여 구성을 삭제 합니다.
* 이 cmdlet에 대 한 설명서는 아직 업데이트 되지 않았습니다.  그때까지 AzureRM 모듈에 대 한 설명서를 참조 하세요.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>시나리오: Dsc 에이전트를 등록 하지 못했습니다.

#### <a name="issue"></a>문제

`Set-DscLocalConfigurationManager` 또는 다른 DSC cmdlet을 실행 하려고 하면 다음과 같은 오류가 표시 됩니다.

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

#### <a name="cause"></a>원인

이 오류는 일반적으로 방화벽, 컴퓨터가 프록시 서버 뒤에 있거나 기타 네트워크 오류로 인해 발생 합니다.

#### <a name="resolution"></a>해상도

컴퓨터에 DSC Azure Automation의 적절 한 엔드포인트에 대 한 액세스 권한이 있는지 확인 하 고 다시 시도 하세요. 필요한 포트 및 주소 목록은 [네트워크 계획](../automation-dsc-overview.md#network-planning) 을 참조 하세요.

### <a name="a-nameunauthorizedascenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>시나리오: 상태 보고서는 응답 코드 "권한 없음"을 반환 합니다.

#### <a name="issue"></a>문제

상태 구성 (DSC)을 사용 하 여 노드를 등록 하는 경우 다음 오류 메시지 중 하나가 표시 됩니다.

```error
The attempt to send status report to the server https://{your automation account url}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>원인

이 문제는 잘못 되거나 만료 된 인증서로 인해 발생 합니다.  자세한 내용은 [인증서 만료 및 다시 등록](../automation-dsc-onboarding.md#certificate-expiration-and-re-registration)를 참조 하세요.

### <a name="resolution"></a>해상도

실패 한 DSC 노드를 다시 등록 하려면 아래 나열 된 단계를 따르세요.

먼저 다음 단계를 사용 하 여 노드 등록을 취소 합니다.

1. Azure Portal의 **홈** -> **automation 계정**-> {AUTOMATION 계정}- **DSC (> 상태 구성** )
2. "노드"를 클릭 하 고 문제가 있는 노드를 클릭 합니다.
3. "등록 취소"를 클릭 하 여 노드 등록을 취소 합니다.

둘째, 노드에서 DSC 확장을 제거 합니다.

1. Azure Portal의 **홈** -> **가상 컴퓨터** -> {실패 노드}-> **확장**
2. "Microsoft. Powershell"을 클릭 합니다.
3. PowerShell DSC 확장을 제거 하려면 "제거"를 클릭 합니다.

셋째, 노드에서 잘못 되었거나 만료 된 모든 인증서를 제거 합니다.

관리자 권한 Powershell 프롬프트에서 노드 오류가 발생 하면 다음을 실행 합니다.

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

마지막으로 다음 단계를 사용 하 여 실패 한 노드를 다시 등록 합니다.

1. Azure Portal의 **홈** -> **automation 계정** -> {AUTOMATION 계정}- **DSC (> 상태 구성** )
2. "노드"를 클릭 합니다.
3. "추가" 단추를 클릭 합니다.
4. 오류가 발생 한 노드를 선택 합니다.
5. "연결"을 클릭 하 고 원하는 옵션을 선택 합니다.

### <a name="failed-not-found"></a>시나리오: 노드가 "찾을 수 없음" 오류로 실패한 상태임

#### <a name="issue"></a>문제

노드에 **실패** 상태의 보고서가 있으며 오류가 포함되어 있습니다.

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>원인

이 오류는 일반적으로 노드가 노드 구성 이름(예: ABC.WebServer) 대신 구성 이름(예: ABC)에 할당된 경우에 발생합니다.

#### <a name="resolution"></a>해상도

* "구성 이름"이 아니라 "노드 구성 이름"을 사용 하 여 노드를 할당 하 고 있는지 확인 합니다.
* Azure 포털 또는 PowerShell cmdlet을 사용하여 노드 구성을 노드에 할당할 수 있습니다.

  * Azure Portal를 사용 하 여 노드 구성을 노드에 할당 하려면 **DSC 노드** 페이지를 연 다음 노드를 선택 하 고 **노드 구성 할당** 단추를 클릭 합니다.
  * PowerShell cmdlet을 사용 하 여 노드 구성을 노드에 할당 하려면 **register-azurermautomationdscnode** cmdlet을 사용 합니다.

### <a name="no-mof-files"></a>시나리오: 구성이 컴파일될 때 생성된 노드 구성(mof 파일)이 없음

#### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단됩니다.

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>원인

DSC 구성에서 **Node** 키워드 다음에 오는 식이 `$null`로 평가되면 노드 구성이 생성되지 않습니다.

#### <a name="resolution"></a>해상도

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 구성 정의에서 **Node** 키워드 옆의 식이 $null를 평가 하 고 있지 않은지 확인 합니다.
* 구성을 컴파일할 때 ConfigurationData를 전달하는 경우 [ConfigurationData](../automation-dsc-compile.md)에서 구성에 필요한 값을 전달해야 합니다.

### <a name="dsc-in-progress"></a>시나리오: DSC 노드 보고서가 "진행 중" 상태로 중단됨

#### <a name="issue"></a>문제

DSC 에이전트 출력:

```error
No instance found with given property values
```

#### <a name="cause"></a>원인

WMF 버전을 업그레이드했고 WMI가 손상되었습니다.

#### <a name="resolution"></a>해상도

이 문제를 해결 하려면 [DSC의 알려진 문제 및 제한 사항](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) 문서에 있는 지침을 따르세요.

### <a name="issue-using-credential"></a>시나리오: DSC 구성에서 자격 증명을 사용할 수 없습니다.

#### <a name="issue"></a>문제

DSC 컴파일 작업이 오류로 인해 중단되었습니다.

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>원인

구성에서 자격 증명을 사용 했지만 각 노드 구성에 대해 **PSDscAllowPlainTextPassword** 를 true로 설정 하는 적절 한 **ConfigurationData** 을 제공 하지 않았습니다.

#### <a name="resolution"></a>해상도

* 구성에 설명 된 각 노드 구성에 대해 적절 한 **ConfigurationData** 를 전달 하 여 **PSDscAllowPlainTextPassword** 를 true로 설정 해야 합니다. 자세한 내용은 [Azure Automation DSC의 자산](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation)을 참조하세요.

### <a name="failure-processing-extension"></a>시나리오: dsc 확장에서 온 보 딩, "오류 처리 확장" 오류

#### <a name="issue"></a>문제

DSC 확장을 사용 하 여 온 보 딩 하는 경우 오류를 포함 하는 오류가 발생 합니다.

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>원인

일반적으로이 오류는 노드에 서비스에 없는 노드 구성 이름이 할당 될 때 발생 합니다.

#### <a name="resolution"></a>해상도

* 서비스의 이름과 정확히 일치 하는 노드 구성 이름으로 노드를 할당 하 고 있는지 확인 합니다.
* 노드 구성 이름을 포함 하지 않도록 선택할 수 있습니다. 그러면 노드를 온 보 딩 하지만 노드 구성을 할당 하지 않습니다.

### <a name="cross-subscription"></a>시나리오: PowerShell을 사용 하 여 노드를 등록 하면 "하나 이상의 오류가 발생 했습니다." 오류가 반환 됩니다.

#### <a name="issue"></a>문제

`Register-AzAutomationDSCNode` 또는 `Register-AzureRMAutomationDSCNode`를 사용 하 여 노드를 등록 하는 경우 다음과 같은 오류가 표시 됩니다.

```error
One or more errors occurred.
```

#### <a name="cause"></a>원인

이 오류는 Automation 계정이 아닌 별도의 구독에 상주 하는 노드를 등록 하려고 할 때 발생 합니다.

#### <a name="resolution"></a>해상도

별도의 클라우드 또는 온-프레미스에 있는 것 처럼 교차 구독 노드를 처리 합니다.

아래 단계에 따라 노드를 등록 합니다.

* Windows-온 [-프레미스 또는 Azure/AWS 이외의 클라우드에 있는 실제/가상 windows 컴퓨터](../automation-dsc-onboarding.md#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances)
* Linux-온 [-프레미스 또는 Azure 이외의 클라우드에서 온-프레미스의 물리적/가상 linux 컴퓨터](../automation-dsc-onboarding.md#physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)

### <a name="agent-has-a-problem"></a>시나리오: 오류 메시지-"프로 비전 하지 못했습니다."

#### <a name="issue"></a>문제

노드를 등록할 때 다음과 같은 오류가 표시 됩니다.

```error
Provisioning has failed
```

#### <a name="cause"></a>원인

이 메시지는 노드와 Azure 간에 연결 문제가 있는 경우에 발생 합니다.

#### <a name="resolution"></a>해상도

노드가 개인 가상 네트워크에 있는지 또는 Azure에 연결 하는 다른 문제가 있는지 확인 합니다.

자세한 내용은 [솔루션을 온 보 딩 할 때 오류 문제 해결](onboarding.md)을 참조 하세요.

### <a name="failure-linux-temp-noexec"></a>시나리오: Linux에서 구성을 적용 하면 일반 오류로 인해 오류가 발생 합니다.

#### <a name="issue"></a>문제

Linux에서 구성을 적용할 때 오류가 발생 하는 오류는 다음과 같습니다.

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>원인

`/tmp` 위치가 `noexec`으로 설정 된 경우 현재 버전의 DSC에서 구성을 적용 하지 못하는 것으로 확인 되었습니다.

#### <a name="resolution"></a>해상도

* `/tmp` 위치에서 `noexec` 옵션을 제거 합니다.

### <a name="compilation-node-name-overlap"></a>시나리오: 중복 되는 노드 구성 이름이 잘못 된 릴리스를 발생 시킬 수 있습니다.

#### <a name="issue"></a>문제

단일 구성 스크립트를 사용 하 여 여러 노드 구성을 생성 하 고 일부 노드 구성의 이름이 다른 하위 집합인 경우 컴파일 서비스의 문제로 인해 잘못 된 구성이 할당 될 수 있습니다.  이는 단일 스크립트를 사용 하 여 노드당 구성 데이터를 사용 하 여 구성을 생성 하는 경우에만 발생 하며, 문자열의 시작 부분에서 이름이 겹치는 경우에만 발생 합니다.

예를 들어 cmdlet을 사용 하 여 hashtable로 전달 된 노드 데이터를 기반으로 구성을 생성 하는 단일 구성 스크립트를 사용 하는 경우 노드 데이터에는 "server" 및 "1server" 라는 서버가 포함 됩니다.

#### <a name="cause"></a>원인

컴파일 서비스의 알려진 문제입니다.

#### <a name="resolution"></a>해상도

가장 좋은 해결 방법은 로컬로 또는 CI/CD 파이프라인에서 컴파일하고 MOF 파일을 서비스에 직접 업로드 하는 것입니다.  서비스의 컴파일이 요구 사항인 경우 다음으로 가장 좋은 해결 방법은 이름에 겹치지 않도록 컴파일 작업을 분할 하는 것입니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.
