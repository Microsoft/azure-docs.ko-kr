---
title: PowerShell을 사용하여 Azure Stream Analytics 작업 모니터링 및 관리
description: 이 아티클에서는 Azure PowerShell 및 cmdlet을 사용하여 Azure Stream Analytics 작업을 모니터링하고 관리하는 방법에 대해 설명합니다.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 52a91dcfebabf3e2d94784dedf93d54e2e748472
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110688561"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Azure PowerShell cmdlet을 사용하여 Stream Analytics 작업 모니터링 및 관리
기본 Stream Analytics 작업을 실행하는 Azure PowerShell cmdlet 및 PowerShell 스크립팅을 사용하여 Stream Analytics 리소스를 모니터링 및 관리하는 방법을 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Stream Analytics에 Azure PowerShell cmdlet을 실행하기 위한 필수 조건
* 구독에서 Azure 리소스 그룹을 만듭니다. 다음은 샘플 Azure PowerShell 스크립트입니다. Azure PowerShell 정보는 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0.

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> 프로그래밍 방식으로 만든 Stream Analytics 작업은 기본적으로 모니터링이 설정되어 있지 않습니다.  작업의 모니터 페이지로 이동하고 사용 단추를 클릭하여 Azure Portal에서 모니터링을 수동으로 설정하거나 [Azure Stream Analytics - 프로그래밍 방식으로 Stream Analytics 작업 모니터링](stream-analytics-monitor-jobs.md)의 단계에 따라 이 작업을 프로그래밍 방식으로 수행할 수 있습니다.
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Stream Analytics용 Azure PowerShell cmdlet
다음 Azure PowerShell cmdlet은 Azure Stream Analytics 작업을 모니터링하고 관리하는 데 사용할 수 있습니다. Azure PowerShell에는 여러 버전이 있습니다. 
**나열된 예제에서 첫 번째 명령은 Azure PowerShell 0.9.8에 적용되고, 두 번째 명령은 Azure PowerShell 1.0에 적용됩니다.** Azure PowerShell 1.0 명령에는 항상 “Az”가 포함되어 있습니다.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Azure 구독 또는 지정한 리소스 그룹에 정의된 모든 Stream Analytics 작업을 나열하거나 리소스 그룹 내의 특정 작업에 대한 작업 정보를 가져옵니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsJob
```

이 PowerShell 명령은 Azure 구독의 모든 Stream Analytics 작업에 대한 정보를 반환합니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

이 PowerShell 명령은 리소스 그룹 StreamAnalytics-Default-Central-US의 모든 Stream Analytics 작업에 대한 정보를 반환합니다.

**예 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

이 PowerShell 명령은 리소스 그룹 StreamAnalytics-Default-Central-US의 Stream Analytics 작업 StreamingJob에 대한 정보를 반환합니다.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
지정한 Stream Analytics 작업에 정의된 모든 입력을 나열하거나 특정 입력에 대한 정보를 가져옵니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

이 PowerShell 명령은 StreamingJob 작업에 정의된 모든 입력에 대한 정보를 반환합니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

이 PowerShell 명령은 StreamingJob 작업에 정의된 EntryStream이라는 입력에 대한 정보를 반환합니다.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
지정한 Stream Analytics 작업에 정의된 모든 출력을 나열하거나 특정 출력에 대한 정보를 가져옵니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

이 PowerShell 명령은 StreamingJob 작업에 정의된 모든 출력에 대한 정보를 반환합니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

이 PowerShell 명령은 StreamingJob 작업에 정의된 Output이라는 출력에 대한 정보를 반환합니다.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
지정한 지역의 스트리밍 단위 할당량에 대한 정보를 가져옵니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

이 PowerShell 명령은 미국 중부 지역의 스트리밍 단위 할당량 및 사용에 대한 정보를 반환합니다.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Stream Analytics 작업에 정의된 특정 변환에 대한 정보를 가져옵니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0.  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

이 PowerShell 명령은 StreamingJob 작업에 정의된 StreamingJob이라는 변환에 대한 정보를 반환합니다.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Stream Analytics 작업 내에서 새 입력을 만들거나 지정한 기존 입력을 업데이트합니다.

.json 파일 또는 명령줄에서 입력의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 입력을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 입력을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 입력 이름을 지정하면 확인 없이 입력이 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [Stream Analytics 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [입력 만들기(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] 섹션을 참조하세요.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

이 PowerShell 명령은 Input.json 파일에 새 입력을 만듭니다. 입력 정의 파일에 지정된 이름의 기존 입력이 이미 정의되어 있으면 cmdlet에서 해당 입력을 바꿀지 여부를 묻습니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

이 PowerShell 명령은 EntryStream이라는 작업에 새 입력을 만듭니다. 이 이름의 기존 입력이 이미 정의되어 있으면 cmdlet에서 해당 입력을 바꿀지 여부를 묻습니다.

**예 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

이 PowerShell 명령은 EntryStream이라는 기존 입력 소스의 정의를 파일에 있는 정의로 바꿉니다.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Microsoft Azure에 새 Stream Analytics 작업을 만들거나 지정한 기존 작업의 정의를 업데이트합니다.

.json 파일 또는 명령줄에서 작업의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 작업 이름을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 작업을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 작업 이름을 지정하면 확인 없이 작업 정의가 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [Stream Analytics 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [Stream Analytics 작업 만들기][msdn-rest-api-create-stream-analytics-job] 섹션을 참조하세요.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

이 PowerShell 명령은 JobDefinition.json의 정의에서 새 작업을 만듭니다. 작업 정의 파일에 지정된 이름의 기존 작업이 이미 정의되어 있으면 cmdlet에서 해당 작업을 바꿀지 여부를 묻습니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

이 PowerShell 명령은 StreamingJob에 대한 작업 정의를 바꿉니다.

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Stream Analytics 작업 내에서 새 출력을 만들거나 기존 출력을 업데이트합니다.  

.json 파일 또는 명령줄에서 출력의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 출력을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 출력을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 출력 이름을 지정하면 확인 없이 출력이 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [Stream Analytics 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [출력 만들기(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] 섹션을 참조하세요.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

이 PowerShell 명령은 StreamingJob 작업에 "output"이라는 새 출력을 만듭니다. 이 이름의 기존 출력이 이미 정의되어 있으면 cmdlet에서 해당 출력을 바꿀지 여부를 묻습니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

이 PowerShell 명령은 StreamingJob 작업에서 "output"의 정의를 바꿉니다.

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Stream Analytics 작업 내에서 새 변환을 만들거나 기존 변환을 업데이트합니다.

.json 파일 또는 명령줄에서 변환의 이름을 지정할 수 있습니다. 둘 다 지정하는 경우 명령줄의 이름이 파일에 있는 이름과 동일해야 합니다.

이미 존재하는 변환을 지정하고 -Force 매개 변수를 지정하지 않을 경우 cmdlet에서 기존 변환을 바꿀지 여부를 묻습니다.

–Force 매개 변수와 기존 변환 이름을 지정하면 확인 없이 변환이 대체됩니다.

JSON 파일 구조 및 내용에 대한 자세한 내용은 [Stream Analytics 관리 REST API 참조 라이브러리][stream.analytics.rest.api.reference]의 [변환 만들기(Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] 섹션을 참조하세요.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

이 PowerShell 명령은 StreamingJob 작업에 StreamingJobTransform이라는 새 변환을 만듭니다. 이 이름의 기존 변환이 이미 정의되어 있으면 cmdlet에서 해당 변환을 바꿀지 여부를 묻습니다.

**예제 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0.  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 이 PowerShell 명령은 StreamingJob 작업에서 StreamingJobTransform의 정의를 바꿉니다.

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Microsoft Azure의 Stream Analytics 작업에서 특정 입력을 비동기적으로 삭제합니다.  
–Force 매개 변수를 지정하면 확인 없이 입력이 삭제됩니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0.  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

이 PowerShell 명령은 StreamingJob 작업에서 EventStream 입력을 제거합니다.  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Microsoft Azure에서 특정 Stream Analytics 작업을 비동기적으로 삭제합니다.  
–Force 매개 변수를 지정하면 확인 없이 작업이 삭제됩니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0.  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

이 PowerShell 명령은 StreamingJob 작업을 제거합니다.  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Microsoft Azure의 Stream Analytics 작업에서 특정 출력을 비동기적으로 삭제합니다.  
–Force 매개 변수를 지정하면 확인 없이 출력이 삭제됩니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0.  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

이 PowerShell 명령은 StreamingJob 작업에서 Output 출력을 제거합니다.  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Microsoft Azure에 Stream Analytics 작업을 비동기적으로 배포하고 시작합니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0.  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

이 PowerShell 명령은 사용자 지정 출력 시작 시간이 2012년 12월 12일 12:12:12 UTC로 설정되어 StreamingJob 작업을 시작합니다.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Microsoft Azure에서 실행 중인 Stream Analytics 작업을 비동기적으로 중지하고 사용하던 리소스를 할당 취소합니다. 작업 정의와 메타데이터는 작업을 편집하고 다시 시작할 수 있도록 Azure 포털과 관리 API를 통해 구독 내에서 계속 사용할 수 있습니다. 중지됨 상태의 작업에 대해서는 요금이 부과되지 않습니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0.  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

이 PowerShell 명령은 StreamingJob 작업을 중단합니다.  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Stream Analytics이 지정한 입력에 연결할 수 있는지 테스트합니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0.  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

이 PowerShell 명령은 StreamingJob에서 EntryStream 입력의 연결 상태를 테스트합니다.  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Stream Analytics이 지정한 출력에 연결할 수 있는지 테스트합니다.

**예제 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0.  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

이 PowerShell 명령은 StreamingJob에서 Output 출력의 연결 상태를 테스트합니다.  

## <a name="get-support"></a>지원 받기
추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](/answers/topics/azure-stream-analytics.html)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
