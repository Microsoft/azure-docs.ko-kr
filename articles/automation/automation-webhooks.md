---
title: 웹후크를 사용하여 Azure Automation Runbook 시작
description: 클라이언트가 Azure Automation에서 HTTP 호출을 통해 runbook을 시작하는 데 사용되는 webhook입니다.  이 문서는 webhook를 만드는 방법 및 webhook를 호출하여 runbook을 시작하는 방법에 대해 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 153bb0304102906f7be64ae55dd0e0f6bb8d7146
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61305023"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>웹후크를 사용하여 Azure Automation Runbook 시작

*Webhook*를 사용하면 단일 HTTP 요청을 통해 Azure Automation에서 특정 runbook을 시작할 수 있습니다. Azure DevOps Services, GitHub, Azure Monitor 로그 또는 Azure Automation API를 사용 하 여 전체 솔루션을 구현 하지 않고 runbook을 시작 하려면 사용자 지정 응용 프로그램 같은 외부 서비스가 있습니다.  
![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

[Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)

> [!NOTE]
> Python runbook을 시작 하는 웹 후크를 사용 하 여 지원 되지 않습니다.

## <a name="details-of-a-webhook"></a>Webhook의 세부 정보

다음 표에서는 Webhook에 대해 구성해야 하는 속성을 설명합니다.

| 자산 | 설명 |
|:--- |:--- |
| 이름 |클라이언트에 노출되지 않으므로 원하는 webhook 이름을 지정할 수 있습니다. Azure Automation에서 Runbook을 식별하는 용도로만 사용됩니다. <br> 가장 좋은 방법은 webhook를 사용할 클라이언트와 관련된 이름을 지정하는 것입니다. |
| URL |webhook의 URL은 클라이언트가 webhook에 연결된 runbook을 시작하기 위해 HTTP POST로 호출하는 고유한 주소입니다. webhook를 만들 때 자동으로 생성됩니다. 사용자 지정 URL을 지정할 수 없습니다. <br> <br> URL에는 타사 시스템이 추가 인증 없이 runbook을 호출할 수 있게 해주는 보안 토큰이 포함됩니다. 따라서 암호처럼 취급해야 합니다. 보안상의 이유로 이 URL은 Azure 포털에서 webhook가 생성될 때만 볼 수 있습니다. 이 URL을 나중에 사용할 수 있도록 안전한 위치에 기록해 둡니다. |
| 만료 날짜 |각 webhook는 인증서처럼 만료 날짜가 있으며, 이 날짜가 되면 인증서를 더 이상 사용할 수 없습니다. webhook가 만료되지 않는 한 webhook를 생성한 후 이 만료 날짜를 수정할 수 있습니다. |
| Enabled |webhook는 생성될 때 기본적으로 사용하도록 설정됩니다. 사용 안함으로 설정할 경우 어떤 클라이언트도 webhook를 사용할 수 없습니다. webhook를 만들 때 또는 만든 후 언제든지 **사용** 속성으로 설정할 수 있습니다. |

### <a name="parameters"></a>매개 변수

webhook는 runbook을 시작할 때 사용되는 runbook 매개 변수 값을 정의할 수 있습니다. webhook에는 runbook의 모든 필수 매개 변수 값이 포함되어야 하고 선택적 매개 변수 값이 포함 될 수 있습니다. 웹후크에 구성된 매개 변수 값은 웹후크를 만든 후에도 수정할 수 있습니다. 단일 runbook에 연결된 여러 webhook는 각각 다른 매개 변수 값을 사용할 수 있습니다.

클라이언트는 webhook를 사용하여 Runbook을 시작할 때 webhook에 정의된 매개 변수 값을 재정의할 수 없습니다. 클라이언트에서 데이터를 수신하려면 Runbook이 **$WebhookData**라는 단일 매개 변수를 수락하면 됩니다. 이 매개 변수는 [object] 형식이며 클라이언트가 POST 요청에 포함하는 데이터를 포함하고 있습니다.

![Webhookdata 속성](media/automation-webhooks/webhook-data-properties.png)

**$WebhookData** 개체의 속성은 다음과 같습니다.

| 자산 | 설명 |
|:--- |:--- |
| WebhookName |Webhook의 이름입니다. |
| RequestHeader |들어오는 POST 요청의 헤더를 포함한 해시 테이블입니다. |
| RequestBody |들어오는 POST 요청의 본문입니다. 문자열, JSON, XML 또는 인코딩된 데이터와 같은 서식을 유지합니다. Runbook은 예상되는 데이터 형식으로 작동하도록 작성되어야 합니다. |

**$WebhookData** 매개 변수를 지원하는 데 필요한 webhook 구성은 없으며, Runbook은 이를 수락할 필요가 없습니다. Runbook이 매개 변수를 정의하지 않을 경우 클라이언트에서 전송된 요청의 모든 세부 정보가 무시됩니다.

webhook를 만들 때 $WebhookData에 값을 지정하면 클라이언트가 요청 본문에 데이터를 포함하지 않는 경우에도 webhook가 클라이언트 POST 요청의 데이터로 Runbook을 시작할 때 해당 값이 재정의됩니다. webhook 이외의 방법을 사용하는 $WebhookData가 있는 runbook을 시작하는 경우 runbook에서 인식할 $Webhookdata 값을 제공할 수 있습니다. 이 값은 runbook이 webhook에서 전달된 실제 WebhookData로 작동하는 것처럼 제대로 작동할 수 있도록 $Webhookdata와 동일한 [속성](#details-of-a-webhook)을 가진 개체여야 합니다.

예를 들어 Azure Portal에서 다음 runbook을 시작하고 테스트용으로 일부 샘플 WebhookData를 전달하려는 경우 WebhookData는 개체이기 때문에 UI에서 JSON으로 전달해야 합니다.

![UI에서의 WebhookData 매개 변수](media/automation-webhooks/WebhookData-parameter-from-UI.png)

다음 runbook의 경우 WebhookData 매개 변수에 대한 다음 속성을 사용하는 경우:

* WebhookName: *MyWebhook*
* RequestBody: *[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*

그런 다음, WebhookData 매개 변수에 대한 UI에서 다음 JSON 값을 전달합니다. 캐리지가 있는 다음 예제는 반환하고 줄 바꿈 문자는 webhook에서 전달되는 형식과 일치합니다.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![UI에서 WebhookData 매개 변수 시작](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> 모든 입력 매개 변수의 값은 runbook 작업에 기록됩니다. webhook 요청에서 클라이언트에서 제공된 입력이 기록되고 자동화 작업에 액세스할 수 있는 모든 사용자가 사용할 수 있게 된다는 것을 의미합니다.  따라서 webhook 호출에 중요한 정보를 포함할 때는 주의해야 합니다.

## <a name="security"></a>보안

webhook의 보안은 webhook의 호출에 사용되는 보안 토큰을 포함하는 URL의 개인 정보에 의존합니다. Azure Automation은 올바른 URL로 설정되어 있는 경우에만 요청에 대한 인증을 수행합니다. 따라서 대체 유효성 검사 방법을 사용하지 않고 매우 중요한 기능을 수행하는 runbook에 webhook를 사용해서는 안 됩니다.

$WebhookData 매개 변수의 **WebhookName** 속성을 확인하여 webhook에서 호출했는지 확인하는 논리를 runbook 내에 포함할 수 있습니다. Runbook은 **RequestHeader** 또는 **RequestBody** 속성에서 특정 정보를 찾아 추가로 유효성 검사를 수행할 수 있습니다.

또 다른 전략은 runbook이 webhook 요청을 받았을 때 외부 조건의 일부 유효성 검사를 수행하도록 하는 것입니다. 예를 들어 GitHub 리포지토리에 대한 새 커밋이 있을 때마다 GitHub에 의해 호출되는 Runbook을 가정해 보겠습니다. 이 Runbook은 계속하기 전에 GitHub에 연결하여 새 커밋이 발생했는지 유효성 검사를 수행할 수 있습니다.

## <a name="creating-a-webhook"></a>webhook 만들기

Azure 포털에서 runbook에 연결된 새 webhook를 만들려면 다음 절차를 수행합니다.

1. Azure Portal의 **Runbook 블레이드**에서 해당 세부 정보 블레이드를 표시하도록 webhook를 시작할 Runbook을 클릭합니다. Runbook **상태**가 **게시**됐는지 확인합니다.
2. 페이지 맨 위에서 **웹후크**를 클릭하여 **웹후크 추가** 페이지를 엽니다.
3. **새 웹후크 만들기**를 클릭하여 **웹후크 페이지 만들기**를 엽니다.
4. webhook의 **이름**, **만료 날짜**와 사용 여부를 지정합니다. 이러한 속성에 대한 자세한 내용은 [webhook 세부 정보](#details-of-a-webhook) 를 참조하십시오.
5. 복사 아이콘을 클릭하고 Ctrl+C를 눌러 webhook의 URL을 복사합니다. 그런 다음 안전한 곳에 기록합니다. **webhook를 만들고 나면 URL을 다시 검색할 수 없습니다.**

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. **매개 변수** 를 클릭하여 runbook 매개 변수의 값을 제공합니다. runbook에 필수 매개 변수가 있으면 값을 제공 하지 않는 한 webhook를 만들 수 없습니다.
1. **만들기** 를 클릭하여 webhook를 만듭니다.

## <a name="using-a-webhook"></a>webhook 사용

만들어진 후에 webhook를 사용하려면 클라이언트 애플리케이션이 webhook의 URL로 HTTP POST를 실행해야 합니다. webhook의 구문 형식은 다음과 같습니다.

```http
http://<Webhook Server>/token?=<Token Value>
```

클라이언트는 POST 요청으로부터의 다음 반환 코드 중 하나를 받게 됩니다.

| 코드 | Text | 설명 |
|:--- |:--- |:--- |
| 202 |수락됨 |요청이 수락되었고 runbook에서 대기합니다. |
| 400 |잘못된 요청 |다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>webhook가 만료되었습니다.</li> <li>webhook가 비활성화되었습니다.</li> <li>URL의 토큰이 잘못되었습니다.</li>  </ul> |
| 404 |찾을 수 없음 |다음 이유 중 하나로 인해 요청이 수락되지 않았습니다. <ul> <li>webhook를 찾을 수 없습니다.</li> <li>runbook을 찾을 수 없습니다.</li> <li>계정을 찾을 수 없습니다.</li>  </ul> |
| 500 |내부 서버 오류 |URL은 유효했지만 오류가 발생했습니다. 요청을 다시 제출하십시오. |

요청이 성공했다고 가정하면 webhook 응답은 다음과 같은 JSON 형식의 작업 ID를 포함합니다. 단일 작업 ID를 포함하지만 잠재적인 이후 향상 기능에 대해 JSON 형식이 허용됩니다.

```json
{"JobIds":["<JobId>"]}
```

클라이언트는 Runbook 작업의 완료 여부 또는 완료 상태를 webhook에서 확인할 수 없습니다. [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjob) 또는 [Azure Automation API](/rest/api/automation/job)와 같은 다른 메서드로 작업 ID를 사용하여 이 정보를 확인할 수 있습니다.

## <a name="renew-webhook"></a>webhook 갱신

webhook를 만들 때 유효 기간은 1년입니다. 해당 연도 후 webhook는 자동으로 만료됩니다. webhook가 만료되면 다시 활성화할 수 없습니다. 제거하고 다시 만들어야 합니다. webhook가 만료되기 전에 만료 시간을 연장할 수 있습니다.

webhook를 확장하려면 webhook를 포함하는 Runbook으로 이동합니다. **리소스** 아래에서 **Webhook**를 선택합니다. 연장하려는 webhook를 클릭하면 **Webhook** 페이지가 열립니다.  새 만료 날짜 및 시간을 선택하고 **저장**을 클릭합니다.

## <a name="sample-runbook"></a>샘플 Runbook

다음 샘플 Runbook은 webhook 데이터를 수락하고 요청 본문에 지정된 가상 머신을 시작합니다. 이 Runbook을 테스트하려면 Automation 계정의 **Runbook** 아래에서 **+ Runbook 추가**를 클릭합니다. Runbook을 만드는 방법을 모르는 경우 [Runbook 만들기](automation-quickstart-create-runbook.md)를 참조하세요.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)



# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>예제 테스트

다음 예제는 Windows PowerShell을 사용하여 webhook로 runbook을 시작합니다. HTTP 요청을 만들 수 있는 모든 언어는 webhook를 사용할 수 있습니다. Windows PowerShell은 예제로 여기에서 사용됩니다.

Runbook에는 요청 본문에 JSON으로 서식이 지정된 가상 머신의 목록이 있어야 합니다. Runbook은 webhook 호출자의 유효성을 검사하도록 정의된 메시지가 헤더에 포함되어 있는지 확인합니다.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

다음 예제는 **WebhookData**의 **RequestBody** 속성에서 Runbook에 사용할 수 있는 요청 본문을 보여줍니다. 이 값은 요청의 본문에 포함된 형식이기 때문에 JSON으로 지정됩니다.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

다음 이미지는 Windows PowerShell 및 결과 응답에서 전송되는 요청을 표시합니다. 작업 ID는 응답에서 추출되고 문자열로 변환됩니다.

![Webhook 단추](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>다음 단계

* Azure Automation을 사용하여 Azure 경고에 대해 조치를 취하는 방법을 알아보려면 [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조하세요.

