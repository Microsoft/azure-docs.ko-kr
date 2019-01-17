---
title: Runbook 입력 매개 변수
description: Runbook 입력 매개 변수는 Runbook이 시작될 때 Runbook에 데이터를 전달할 수 있도록 하여 Runbook의 유용성을 늘립니다. 이 문서에서는 입력 매개 변수가 사용되는 Runbook의 다양한 시나리오를 설명합니다.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 650effed388dde4419e2ff6aede2f0468551a959
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276689"
---
# <a name="runbook-input-parameters"></a>Runbook 입력 매개 변수

Runbook 입력 매개 변수는 Runbook이 시작될 때 Runbook에 데이터를 전달할 수 있도록 하여 Runbook의 유용성을 늘립니다. 매개 변수는 Runbook 동작이 특정 시나리오 및 환경에 대한 대상이 되도록 합니다. 이 문서에서는 입력 매개 변수가 Runbook에서 사용되는 다양한 시나리오를 살펴보겠습니다.

## <a name="configure-input-parameters"></a>입력 매개 변수 구성

PowerShell, PowerShell 워크플로, Python 및 그래픽 Runbook에서 입력 매개 변수를 구성할 수 있습니다. Runbook에는 여러 데이터 형식을 가진 여러 매개 변수가 있거나 매개 변수가 전혀 없을 수 있습니다. 입력 매개 변수는 필수 또는 선택일 수 있으므로 선택적 매개 변수에 대한 기본값을 할당할 수 있습니다. 사용 가능한 방법 중 하나를 통해 시작할 때 Runbook에 대한 입력 매개 변수에 값을 할당할 수 있습니다. 이러한 메서드는 포털 또는 웹 서비스에서 Runbook 시작하기를 포함합니다. 또한 다른 Runbook에서 인라인으로 호출되는 하위 Runbook으로 시작할 수 있습니다.

## <a name="configure-input-parameters-in-powershell-and-powershell-workflow-runbooks"></a>PowerShell 및 PowerShell 워크플로 Runbook에서 입력 매개 변수 구성

Azure Automation에서 PowerShell 및 [PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)은 다음과 같은 특성을 통해 정의된 입력 매개 변수를 지원합니다.  

| **속성** | **설명** |
|:--- |:--- |
| type |필수 사항입니다. 매개 변수 값에 필요한 데이터 형식입니다. 모든 .NET 형식이 유효합니다. |
| 이름 |필수 사항입니다. 매개 변수의 이름입니다. 이름은 Runbook 내에서 고유해야 하고 문자, 숫자 또는 밑줄 문자를 포함할 수 있습니다. 문자로 시작해야 합니다. |
| 필수 |선택 사항입니다. 매개 변수에 대해 값을 제공해야 하는지 여부를 지정합니다. 이 값을 **$true**로 설정한 경우 Runbook이 시작될 때 값을 지정해야 합니다. 이 값을 **$false**로 설정한 경우 값은 선택 사항입니다. |
| 기본값 |선택 사항입니다. Runbook이 시작될 때 값을 전달하지 않으면 매개 변수에 대해 사용될 값을 지정합니다. 기본값을 매개 변수에 대해 설정할 수 있으며 필수 설정에 관계 없이 자동으로 매개 변수를 선택적으로 만듭니다. |

Windows PowerShell은 유효성 검사, 별칭, 매개 변수 설정과 같이 여기에 나열된 것 보다 많은 입력 매개 변수의 특성을 지원합니다. 그러나 Azure Automation은 현재 위에 나열된 입력 매개 변수만을 지원합니다.

PowerShell 워크플로 Runbook의 매개 변수 정의에는 다음과 같은 일반 형식이 있으며 여러 매개 변수는 쉼표로 구분됩니다.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

> [!NOTE]
> 매개 변수를 정의할 때 **필수** 특성을 지정하지 않으면 기본적으로 매개 변수는 선택 사항으로 간주됩니다. 또한 PowerShell 워크플로 Runbook의 매개 변수에 대한 기본값을 설정하는 경우 **필수** 특성 값과 관계 없이 PowerShell에서 선택적 매개 변수로 처리됩니다.
> 
> 

예를 들어 가상 머신(단일 VM 또는 리소스 그룹 내의 모든 VM)에 대한 세부 정보를 출력하는 PowerShell 워크플로 Runbook에 대한 입력 매개 변수를 구성해보겠습니다. 다음 스크린샷에 보여준 대로 이 Runbook에는 가상 머신의 이름 및 리소스 그룹 이름이라는 두 가지 매개 변수가 있습니다.

![Automation PowerShell 워크플로](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

이 매개 변수 정의에서 매개 변수 **$VMName** 및 **$resourceGroupName**은 형식 문자열의 간단한 매개 변수입니다. 그러나 PowerShell 및 PowerShell 워크플로 Runbook은 입력 매개 변수에 대해 **개체** 또는 **PSCredential**과 같은 단순 형식 및 복합 형식을 모두 지원합니다.

Runbook에 object 형식 입력 매개 변수가 있는 경우 값에 전달하려면 (이름, 값) 쌍이 있는 PowerShell 해시 테이블을 사용합니다. 예를 들어 Runbook에 다음 매개 변수가 있는 경우

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

매개 변수에 다음 값을 전달할 수 있습니다.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```
> [!NOTE]
> `$null` _기본값_ 을 가진 선택적인 `[String]` 유형 매개 변수에 값을 전달하지 않으면 이 매개 면수의 값이 `$null`이 **아니라** _빈 문자열_ 이 됩니다.
> 
> 

## <a name="configure-input-parameters-in-graphical-runbooks"></a>그래픽 Runbook에서 입력 매개 변수 구성

입력 매개 변수를 사용하여 그래픽 Runbook을 구성하려면 가상 머신(단일 VM 또는 리소스 그룹 내의 모든 VM)에 대한 세부 정보를 출력하는 [그래픽 Runbook](automation-first-runbook-graphical.md)을 만들겠습니다. 아래 설명한 대로 Runbook이 두 가지 주요 작업으로 이루어지도록 구성합니다.

Azure로 인증하는 [**Azure 실행 계정으로 Runbook 인증**](automation-sec-configure-azure-runas-account.md).

가상 머신의 속성을 가져오는 [**Get-AzureRmVm**](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm).

You can use the [**Write-Output**](https://technet.microsoft.com/library/hh849921.aspx) activity to output the names of virtual machines. **Get-AzureRmVm** 활동은 **가상 머신 이름** 및 **리소스 그룹 이름**과 같은 두 개의 매개 변수를 수락합니다. 이러한 매개 변수가 Runbook을 시작할 때마다 다른 값을 필요할 수 있기 때문에 Runbook에 입력 매개 변수를 추가할 수 있습니다. 입력 매개 변수를 추가하는 단계는 다음과 같습니다.

1. **Runbook** 블레이드에서 그래픽 Runbook을 선택한 다음 [**편집**](automation-graphical-authoring-intro.md)을 클릭합니다.
2. Runbook 편집기에서 **입력 및 출력**을 클릭하여 **입력 및 출력** 블레이드를 엽니다.
   
    ![자동화 그래픽 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)
3. **입력 및 출력** 블레이드는 Runbook에 대해 정의된 입력 매개 변수 목록을 표시합니다. 이 블레이드에서 새 입력 매개 변수를 추가하거나 기존의 입력 매개 변수의 구성을 편집합니다. Runbook에 새 매개 변수를 추가하려면 **입력 추가**를 클릭하여 **Runbook 입력 매개 변수** 블레이드를 엽니다. 거기서 다음 매개 변수를 구성할 수 있습니다.
   
   | **속성** | **설명** |
   |:--- |:--- |
   | 이름 |필수 사항입니다. 매개 변수의 이름입니다. 이름은 Runbook 내에서 고유해야 하고 문자, 숫자 또는 밑줄 문자를 포함할 수 있습니다. 문자로 시작해야 합니다. |
   | 설명 |선택 사항입니다. 입력 매개 변수의 목적에 대한 설명입니다. |
   | type |선택 사항입니다. 매개 변수 값에 필요한 데이터 형식입니다. 지원되는 매개 변수 형식은 **문자열**, **Int32**, **Int64**, **Decimal**, **Boolean**, **DateTime** 및 **개체**입니다. 데이터 형식이 선택되어 있지 않으면 **문자열**에 대한 기본값으로 지정됩니다. |
   | 필수 |선택 사항입니다. 매개 변수에 대해 값을 제공해야 하는지 여부를 지정합니다. **예**를 선택한 경우 Runbook이 시작될 때 값을 지정해야 합니다. **아니오**를 선택한 경우 Runbook이 시작될 때 값이 필요하지 않고 기본값이 설정됩니다. |
   | 기본값 |선택 사항입니다. Runbook이 시작될 때 값을 전달하지 않으면 매개 변수에 대해 사용될 값을 지정합니다. 필수가 아닌 매개 변수에 기본값을 설정할 수 있습니다. 기본값을 설정하려면 **사용자 지정**을 선택합니다. Runbook이 시작될 때 다른 값을 지정하지 않으면 이 값을 사용합니다. 기본값을 제공하지 않으려는 경우 **없음** 을 선택합니다. |
   
    ![새 입력 추가](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. **Get-AzureRmVm** 활동에서 사용될 다음 속성을 가진 두 개의 매개 변수를 만듭니다.
   
   * **매개 변수 1:**
     
     * 이름 - VMName
     * 형식 - String
     * 필수 - 없음
   * **매개 변수 2:**
     
     * 이름 - resourceGroupName
     * 형식 - String
     * 필수 - 없음
     * 기본값 - 사용자 지정
     * 사용자 지정 기본값 - \<가상 머신을 포함하는 리소스 그룹의 이름&gt;
5. 매개 변수를 추가하면 **확인**을 클릭합니다. 이제 **입력 및 출력 블레이드**에서 볼 수 있습니다. 다시 **확인**을 클릭한 다음 Runbook을 **저장**하고 **게시**하도록 클릭합니다.

## <a name="configure-input-parameters-in-python-runbooks"></a>Python Runbook에서 입력 매개 변수 구성

PowerShell, PowerShell 워크플로 및 그래픽 Runbook과 달리 Python Runbook에서는 명명된 매개 변수를 사용하지 않습니다.
모든 입력 매개 변수는 인수 값의 배열로 구문 분석됩니다.
`sys` 모듈을 Python 스크립트에 가져온 다음 `sys.argv` 배열을 사용하여 배열에 액세스합니다.
배열의 첫 번째 요소인 `sys.argv[0]`는 스크립트의 이름이므로 실제 첫 번째 입력 매개 변수는 `sys.argv[1]`입니다.

Python Runbook에서 입력 매개 변수를 사용하는 방법의 예제를 보려면 [Azure Automation의 내 첫 번째 Python Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Runbook의 입력 매개 변수에 값 할당

다음과 같은 시나리오에서 Runbook의 매개 변수에 값을 전달할 수 있습니다.

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook 시작 및 매개 변수 할당

Runbook은 Azure 포털, webhook, PowerShell cmdlet, REST API 또는 SDK 등 여러 가지 방법을 통해 시작할 수 있습니다. 아래에서는 Runbook을 시작하고 매개 변수를 할당하는 여러 가지 방법을 설명합니다.

#### <a name="start-a-published-runbook-by-using-the-azure-portal-and-assign-parameters"></a>Azure 포털을 사용하여 게시된 Runbook 시작 및 매개 변수 할당

[Runbook을 시작](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)하면 **Runbook 시작** 블레이드가 열리며, 만든 매개 변수의 값을 입력할 수 있습니다.

![포털을 사용하여 시작하기](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

입력된 상자 아래에 있는 레이블에서 매개 변수에 대해 설정된 특성을 볼 수 있습니다. 특성은 필수 또는 선택적, 형식 및 기본값을 포함합니다. 매개 변수 이름 옆에 있는 도움말 풍선에서 매개 변수 입력 값에 대한 결정을 해야 할 모든 핵심 정보를 볼 수 있습니다. 이 정보는 매개 변수가 필수 또는 선택인지를 포함합니다. 형식 및 기본값(있는 경우) 및 기타 유용한 참고 사항도 포함됩니다.

> [!NOTE]
> 문자열 형식 매개 변수는 **빈** 문자열 값을 지원합니다.  입력 매개 변수 상자에 **[EmptyString]** 을 입력하면 빈 문자열을 매개 변수에 전달합니다. 또한 문자열 형식 매개 변수는 전달되는 **Null** 값을 지원하지 않습니다. 문자열 매개 변수에 값을 전달하지 않으면 PowerShell이 null로 해석합니다.
> 
> 

#### <a name="start-a-published-runbook-by-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell cmdlet을 사용하여 게시된 Runbook 시작 및 매개 변수 할당

* **Azure Resource Manager cmdlet:**[Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook)을 사용하여 리소스 그룹에 생성된 자동화 Runbook을 시작할 수 있습니다
  
  **예제:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”;”resourceGroupeName”=”WSVMClassicSG”}
  
  Start-AzureRmAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” –ResourceGroupName $resourceGroupName -Parameters $params
  ```
* **Azure 클래식 배포 모델 cmdlet:** [Start-AzureAutomationRunbook](https://docs.microsoft.com/powershell/module/servicemanagement/azure/start-azureautomationrunbook)을 사용하여 기본 리소스 그룹에 생성된 자동화 Runbook을 시작할 수 있습니다
  
  **예제:**
  
  ```powershell
  $params = @{“VMName”=”WSVMClassic”; ”ServiceName”=”WSVMClassicSG”}
  
  Start-AzureAutomationRunbook -AutomationAccountName “TestAutomation” -Name “Get-AzureVMGraphical” -Parameters $params
  ```

> [!NOTE]
> PowerShell cmdlet을 사용하여 Runbook을 시작할 때 기본 매개 변수인 **MicrosoftApplicationManagementStartedBy**는 **PowerShell** 값을 사용하여 만들어집니다. **작업 세부 정보** 블레이드에서 이 매개 변수를 볼 수 있습니다.  
> 
> 

#### <a name="start-a-runbook-by-using-an-sdk-and-assign-parameters"></a>SDK를 사용하여 Runbook 시작 및 매개 변수 할당

* **Azure Resource Manager 방법:** 프로그래밍 언어의 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.  
  
  ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
  ```
* **Azure 클래식 배포 모델 방법:** 프로그래밍 언어의 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.
  
  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
  ```
  
  이 메서드를 시작하려면 사전을 만들어 Runbook 매개 변수, **VMName**, **resourceGroupName** 그리고 해당 값을 저장합니다. 그런 다음 Runbook을 시작합니다. 다음은 위에 정의된 메서드를 호출하기 위한 C# 코드 조각입니다.
  
  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook(“Get-AzureVMGraphical”, RunbookParameters);
  ```

#### <a name="start-a-runbook-by-using-the-rest-api-and-assign-parameters"></a>REST API를 사용하여 Runbook 시작 및 매개 변수 할당
다음 요청 URI가 있는 **PUT** 메서드를 사용하여 Azure Automation REST API를 통해 Runbook 작업을 만들고 시작할 수 있습니다. https://management.core.windows.net/<subscription-id>/cloudServices/<cloud-service-name>/resources/automation/~/automationAccounts/<automation-account-name>/jobs/<job-id>?api-version=2014-12-08`


요청 URI에서 다음 매개 변수를 바꿉니다.

* **subscription-id:** Azure 구독 ID입니다.  
* **cloud-service-name:** 요청을 보낼 클라우드 서비스의 이름입니다.  
* **automation-account-name:** 지정된 클라우드 서비스 내에 호스트되는 자동화 계정의 이름입니다.  
* **job-id:** 작업에 대한 GUID입니다. **[GUID]::NewGuid().ToString()** 명령을 사용하여 PowerShell에서 GUID를 만들 수 있습니다.

Runbook 작업에 매개 변수를 전달하기 위해 요청 본문을 사용합니다. JSON 형식으로 제공되는 다음 두 가지 속성을 사용합니다.

* **Runbook 이름:** 필수입니다. 시작할 작업 시작에 대한 Runbook의 이름입니다.  
* **Runbook 매개 변수:** 선택 사항입니다. 이름이 문자열 형식이어야 하고 값은 유효한 JSON 값일 수 있는 (이름, 값) 형식인 매개 변수 목록의 사전입니다.

이전에 **VMName** 및 **resourceGroupName**을 매개 변수로 만든 **Get-AzureVMTextual** Runbook을 시작하려는 경우 요청 본문에 다음 JSON 형식을 사용합니다.

   ```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WSVMClassic",
         "resourceGroupName":”WSCS1”}
        }
    }
   ```

작업이 성공적으로 만들어진 경우 HTTP 상태 코드 201이 반환됩니다. 응답 헤더 및 응답 본문에 대한 자세한 내용은 [REST API를 사용하여 Runbook 작업을 만드는 방법](https://msdn.microsoft.com/library/azure/mt163849.aspx)에 관한 문서를 참조하세요.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook 테스트 및 매개 변수 할당
테스트 옵션을 사용하여 [Runbook의 초안 버전을 테스트](automation-testing-runbook.md) 할 때 **테스트** 페이지가 열리고, 만든 매개 변수에 대한 값을 구성할 수 있습니다.

![매개 변수 테스트 및 할당](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Runbook에 일정 연결 및 매개 변수 할당
Runbook이 특정 시간에 시작하도록 Runbook에 [일정을 연결](automation-schedules.md)할 수 있습니다. 일정을 만드는 동안 입력 매개 변수를 할당하고 Runbook이 일정에 따라 시작될 때 이러한 값을 사용합니다. 모든 필수 매개 변수 값이 제공될 때까지 일정을 저장할 수 없습니다.

![매개 변수 예약 및 할당](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook에 대한 webhook 만들기 및 매개 변수 할당
Runbook에 대한 [webhook](automation-webhooks.md) 을 만들고 Runbook 입력 매개 변수를 구성할 수 있습니다. 모든 필수 매개 변수 값이 제공될 때까지 webhook을 저장할 수 없습니다.

![Webhook 만들기 및 매개 변수 할당](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

웹후크를 사용하여 Runbook을 실행할 때 미리 정의된 입력 매개 변수 **[Webhookdata](automation-webhooks.md#details-of-a-webhook)** 가 사용자가 정의한 입력 매개 변수와 함께 전송됩니다. **WebhookData** 매개 변수를 클릭하여 확장하면 자세한 내용을 볼 수 있습니다.

![WebhookData 매개 변수](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="next-steps"></a>다음 단계
* Runbook 입력 및 출력에 대한 자세한 내용은 [Azure Automation: Runbook 입력, 출력 및 중첩된 Runbook](https://azure.microsoft.com/blog/azure-automation-runbook-input-output-and-nested-runbooks/)을 참조하세요.
* Runbook을 시작하는 다양한 방법에 대한 자세한 내용은 [Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.
* 텍스트 Runbook을 편집하려면 [텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조하세요.
* 그래픽 Runbook을 편집하려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.

