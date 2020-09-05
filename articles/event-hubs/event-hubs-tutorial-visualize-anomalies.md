---
title: Azure Event Hubs - 실시간 이벤트에서 데이터 변칙 시각화
description: '자습서: Microsoft Azure Event Hubs로 보내는 실시간 이벤트에서 데이터 변칙 시각화'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 595c0e06750171c844249a77ecef16c84dc8ada5
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85315612"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>자습서: Azure Event Hubs에 보내는 실시간 이벤트에서 데이터 이상 시각화

Azure Event Hubs에서는 Azure Stream Analytics를 사용하여 들어오는 데이터를 확인하고 변칙을 짚어낸 다음, Power BI에서 시각화할 수 있습니다. 이벤트 허브에 지속적으로 실시간 데이터를 보내 초당 최대 수백 만 건의 이벤트를 더하는 수천 대의 디바이스를 가정해 보겠습니다. 이렇게 많은 데이터에서 변칙이나 오류를 어떻게 확인할까요? 예를 들어 디바이스가 신용 카드 트랜잭션을 전송하는데, 5초 이내 간격으로 여러 국가/지역에서 발생하는 여러 트랜잭션 모두 캡처해야 한다면 어떻게 될까요? 누군가 신용 카드를 도용하여 동시에 전세계에서 물품을 구매하는 경우 이런 상황이 발생할 수 있습니다. 

이 자습서에서는 이 예제를 시뮬레이션합니다. 신용 카드 거래를 만들어 이벤트 허브로 보내는 애플리케이션을 실행합니다. 그런 다음, Azure Stream Analytics를 통해 실시간으로 데이터 스트림을 읽습니다. 여기서는 유효한 거래와 잘못된 거래를 구별한 다음, Power BI를 사용하여 잘못된 것으로 태그 지정된 거래를 시각적으로 식별합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 이벤트 허브 만들기
> * 신용 카드 거래를 전송하는 앱 실행
> * 이러한 트랜잭션을 처리하는 Stream Analytics 작업 구성
> * Power BI 시각화를 구성하여 결과 표시

이 자습서를 완료하려면 Azure 구독이 필요합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요][].

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

- [Visual Studio](https://www.visualstudio.com/)를 설치합니다. 
- Stream Analytics 작업에서 출력을 분석하려면 Power BI 계정이 필요합니다. [Power BI 평가판](https://app.powerbi.com/signupredirect?pbi_source=web)을 사용할 수 있습니다.

## <a name="set-up-resources"></a>리소스 설정

이 자습서에서는 Event Hubs 네임스페이스 및 이벤트 허브가 필요합니다. 이러한 리소스는 Azure CLI 또는 Azure PowerShell을 사용하여 만들 수 있습니다. 모든 리소스에 대해 동일한 리소스 그룹 및 위치를 사용합니다. 그런 다음, 마지막에 리소스 그룹을 삭제하면 모든 내용을 한 번에 삭제할 수 있습니다.

다음 섹션에서는 이를 위해 필요한 단계를 설명합니다. CLI *또는* PowerShell 명령에 따라 다음 단계를 수행합니다.

1. [리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 

2. Event Hubs 네임스페이스를 만듭니다. 

3. 이벤트 허브를 만듭니다.

> [!NOTE]
> 이 자습서의 뒷부분에서 필요한 각 스크립트에 변수 집합이 있습니다. 여기에는 리소스 그룹 이름($resourceGroup), 이벤트 허브 네임스페이스( **$eventHubNamespace**) 및 이벤트 허브 이름( **$eventHubName**)이 포함됩니다. 이 문서의 뒷부분에서 이러한 항목에는 달러 기호($)가 접두어로 붙어 있으므로 스크립트에 설정되어 있는 것을 알 수 있습니다.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Azure CLI를 사용하여 리소스 설정

이 스크립트를 복사하여 Cloud Shell에 붙여넣습니다. 이미 로그인했다고 가정하면 한 번에 한 줄씩 스크립트가 실행됩니다.

전역적으로 고유해야 하는 변수에는 `$RANDOM`이 연결되어 있습니다. 스크립트가 실행되고 변수가 설정되면 임의의 수치 문자열이 생성되고 고정 문자열 마지막에 연결되어 이것이 고유하게 됩니다.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Azure PowerShell을 사용하여 리소스 설정

이 스크립트를 복사하여 Cloud Shell에 붙여넣습니다. 이미 로그인했다고 가정하면 한 번에 한 줄씩 스크립트가 실행됩니다.

전역적으로 고유해야 하는 변수에는 `$(Get-Random)`이 연결되어 있습니다. 스크립트가 실행되고 변수가 설정되면 임의의 수치 문자열이 생성되고 고정 문자열 마지막에 연결되어 이것이 고유하게 됩니다.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>테스트 이벤트 데이터를 생성하는 앱 실행

GitHub의 Event Hubs [샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)에는 테스트 데이터를 생성하는 Anomaly Detector 앱이 포함되어 있습니다. 이벤트 허브에 신용 카드 거래를 작성하여 신용 카드 사용을 시뮬레이션하는데, 경우에 따라 여러 위치에서 동일한 신용 카드에 대해 여러 개의 거래를 작성하여 변칙으로 태그되게 합니다. 이 앱을 실행하려면 다음 단계를 수행합니다. 

1. GitHub에서 [Azure Event Hubs 샘플](https://github.com/Azure/azure-event-hubs/archive/master.zip)을 다운로드하여 로컬에 압축을 풉니다.
2. **\azure-event-hubs-master\samples\DotNet\\** 폴더로 이동합니다. 
3. **Azure.Messaging.EventHubs\AnomalyDetector\\** 폴더로 전환하고 **AnomalyDetector.sln**을 두 번 클릭하여 Visual Studio에서 솔루션을 엽니다. 

    이전 Microsoft.Azure.EventHubs 패키지를 사용하는 이전 버전의 샘플을 사용하려면 **Microsoft.Azure.EventHubs\AnomalyDetector** 폴더에서 솔루션을 엽니다. 
3. Program.cs를 열고 **Event Hubs connection string**을 스크립트를 실행할 때 저장한 연결 문자열로 바꿉니다. 
4. **Event Hub name**을 이벤트 허브 이름으로 바꿉니다. F5를 클릭하여 애플리케이션을 실행합니다. 이벤트 허브로 이벤트를 보내기 시작하고 1000개 이벤트를 보낼 때까지 계속합니다. 데이터를 검색하려면 앱을 실행해야 하는 경우가 있습니다. 이러한 경우는 필요하면 다음 지침에서 다룹니다.

## <a name="set-up-azure-stream-analytics"></a>Azure Stream Analytics 설정

이제 이벤트 허브로 데이터를 스트리밍할 수 있습니다. Power BI 시각화에서 데이터를 사용하려면 먼저 데이터를 검색한 다음, Power BI 시각화에 입력하는 Stream Analytics 작업을 설정합니다.

### <a name="create-the-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. Azure Portal에서 **리소스 만들기**를 클릭합니다. 검색 상자에 **stream analytics**를 입력하고 **Enter**를 누릅니다. **Stream Analytics 작업**을 선택합니다. Stream Analytics 작업 창에서 **만들기**를 클릭합니다. 

2. 작업에 대해 다음 정보를 입력합니다.

   **작업 이름**: **contosoEHjob**을 사용합니다. 이 필드는 작업의 이름이며 전역에서 고유해야 합니다.

   **구독**: 구독을 선택합니다.

   **리소스 그룹**: 이벤트 허브에서 사용하는 것과 동일한 리소스 그룹(**ContosoResourcesEH**)을 사용합니다.

   **위치**: 설치 스크립트에 사용된 것과 동일한 위치(**미국 서부**)를 사용합니다.

   ![새 Azure Stream Analytics 작업을 만드는 방법을 보여주는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    나머지 필드는 기본값을 그대로 사용합니다. **만들기**를 클릭합니다. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

**Stream Analytics 작업** 창에서 포털에 있지 않으면 포털의 **리소스 그룹**을 클릭하고 리소스 그룹(**ContosoResourcesEH**)을 선택하여 Stream Analytics 작업으로 돌아갈 수 있습니다. 이 작업은 그룹의 모든 리소스를 표시하므로 자신의 Stream Analytics 작업을 선택할 수 있습니다. 

Stream Analytics 작업 입력은 이벤트 허브로부터의 신용 카드 거래입니다.

> [!NOTE]
> 달러 기호($)로 시작되는 변수의 값은 이전 섹션의 시작 스크립트에서 설정되었습니다. Event Hubs 네임스페이스와 이벤트 허브 이름 등, 해당 필드를 지정할 때와 동일한 값을 여기서 사용해야 합니다.

1. **작업 토폴로지**에서 **입력**을 클릭합니다.

2. **입력** 창에서 **스트림 입력 추가**를 클릭하고 Event Hubs를 선택합니다. 나타난 화면에서 다음 필드를 입력합니다.

   **입력 별칭**: **contosoinputs**를 사용합니다. 이 필드는 데이터에 대한 쿼리를 정의할 때 사용한 입력 스트림의 이름입니다.

   **구독**: 구독을 선택합니다.

   **Event Hubs 네임스페이스**: Event Hub 네임스페이스($**eventHubNamespace**)를 선택합니다. 

   **Event Hub 이름**: **기존 항목 사용**을 클릭하고, 이벤트 허브($**eventHubName**)를 선택합니다.

   **Event Hubs 정책 이름**: **RootManageSharedAccessKey**를 선택합니다.

   **Event Hubs 소비자 그룹**: 기본 소비자 그룹을 사용하려면 이 필드를 비워 둡니다.

   나머지 필드는 기본값을 그대로 사용합니다.

   ![Stream Analytics 작업에 입력 스트림을 추가하는 방법을 보여주는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. **저장**을 클릭합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지**에서 **출력**을 클릭합니다. 이 필드는 데이터에 대한 쿼리를 정의할 때 사용한 출력 스트림의 이름입니다.

2. **출력** 창에서 **추가**를 클릭한 다음, **Power BI**를 선택합니다. 나타난 화면에서 다음 필드를 입력합니다.

   **출력 별칭**: **contosooutputs**를 사용합니다. 이 필드는 출력에 대한 고유 별칭입니다. 

   **데이터 세트 이름**: **contosoehdataset**를 사용합니다. 이 필드는 Power BI에 사용할 데이터 세트의 이름입니다. 

   **테이블 이름**: **contosoehtable**을 사용합니다. 이 필드는 Power BI에 사용할 테이블의 이름입니다. 

   나머지 필드는 기본값을 그대로 사용합니다.

   ![Stream Analytics 작업에 대한 출력을 설정하는 방법을 보여주는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. **권한 부여**를 클릭하고 사용자의 Power BI 계정에 로그인합니다.

4. 나머지 필드는 기본값을 그대로 사용합니다.

5. **저장**을 클릭합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

이 쿼리는 궁극적으로 Power BI 시각화로 전송되는 데이터를 검색하는 데 사용됩니다. 이전에 작업을 설정할 때 정의한 **contosoinputs** 및 **contosooutputs**를 사용합니다. 이 쿼리는 사기로 간주되는 신용카드 거래를 검색합니다. 즉 같은 5초 간격 안에 서로 다른 위치에서 동일한 신용카드 번호에 여러 거래가 발생한 경우입니다.

1. **작업 토폴로지**에서 **쿼리**를 클릭합니다.

2. 쿼리를 다움으로 바꿉니다. 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. **저장**을 클릭합니다.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Stream Analytics 작업에 대해 쿼리 테스트 

1. Anomaly Detector 앱을 실행하여 테스트를 설정 및 실행하는 동안 이벤트 허브로 데이터를 보냅니다. 

2. 쿼리 창에서 **contosoinputs** 입력 옆에 있는 점을 클릭한 다음, **입력의 샘플 데이터**를 선택합니다.

3. 3분 데이터를 지정하고 **확인**을 클릭합니다. 데이터가 샘플링되었다는 알림을 받을 때까지 기다립니다.

4. **테스트**를 클릭하고 결과를 가져오는지 확인합니다. 결과가 아래쪽 창의 **결과** 섹션에서 쿼리 아래 오른쪽에 표시됩니다.

5. 쿼리 창을 닫습니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytic 작업에서 **시작**, **지금**, **시작**을 차례로 클릭합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨**에서 **실행 중**으로 변경됩니다.

## <a name="set-up-the-power-bi-visualizations"></a>Power BI 시각화 설정

1. Anomaly Detector 앱을 실행하여 Power BI 시각화를 설정하는 동안 이벤트 허브로 데이터를 보냅니다. 실행할 때마다 1000개의 거래만 생성하므로 여러 번 실행해야 할 수 있습니다.

2. [Power BI](https://powerbi.microsoft.com/) 계정에 로그인합니다.

3. **내 작업 영역**으로 이동합니다.

4. **데이터 세트**를 클릭합니다.

   Stream Analytics 작업에 대한 출력을 만들 때 지정한 데이터 세트가 표시됩니다(**contosoehdataset**). 데이터 세트를 처음 표시할 때는 5-10분이 걸릴 수 있습니다.

5. **대시보드**를 클릭한 다음, **만들기**를 클릭하고 **대시보드**를 선택합니다.

   ![대시보드 및 만들기 단추 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. 대시보드 이름을 지정한 다음, **만들기**를 클릭합니다. **신용 카드 변칙**을 사용합니다.

   ![대시보드 이름을 지정하는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. 대시보드 페이지에서 **타일 추가**를 클릭하고 **REAL-TIME DATA**에서 **사용자 지정 스트리밍 데이터**를 선택한 후, **다음**을 클릭합니다.

   ![타일 원본을 지정하는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. 데이터 세트(**contosoehdataset**)를 선택하고 **다음**을 클릭합니다.

   ![데이터 세트를 지정하는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. 시각화 형식으로 **카드**를 선택합니다. **필드** 아래에서 **값 추가**를 선택한 다음, **fraudulentuses**를 선택합니다.

   ![시각화 형식 및 필드를 지정하는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   **다음**을 클릭합니다.

10. 제목을 **사기성 사용**으로, 부제목을 **지난 몇 분 동안의 합계**로 설정합니다. **적용**을 클릭합니다. 대시보드에 타일을 저장합니다.

    ![대시보드 타일의 제목 및 부제목을 지정하는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > 샘플 애플리케이션을 실행하고 데이터를 이벤트 허브로 스트리밍하면, 이 타일의 숫자가 빠르게(1초마다) 변경됩니다. 이것은 Stream Analytics 쿼리가 실제로 **매초마다** 값을 업데이트하기 때문입니다. 지난 수분간의 합계를 보려면 쿼리를 3분간의 연속 창으로 업데이트합니다. 
11. 다른 시각화를 추가합니다. 처음 몇 단계를 다시 반복합니다.

    * **타일 추가**를 클릭합니다.
    * **사용자 지정 스트리밍 데이터**를 선택합니다. 
    * **다음**을 클릭합니다.
    * 데이터 세트를 선택하고 **다음**을 클릭합니다. 

12. **시각화 형식**에서 **꺾은선형 차트**를 선택합니다.

13. **축**에서 **값 추가**를 클릭하고 **windowend**를 선택합니다. 

14. **축**에서 **값 추가**를 클릭하고 **fraudulentuses**를 선택합니다.

15. **표시할 시간 창**에 지난 5분을 선택합니다. **다음**을 클릭합니다.

16. 제목에 **시간 경과에 따른 사기성 사용 표시**를 입력하고 타일 부제목은 공백으로 둔 다음, **적용**을 클릭합니다. 대시보드로 돌아갑니다.

17. Anomaly Detector 앱을 다시 실행하여 일부 데이터를 이벤트 허브로 보냅니다. 데이터 분석에 따라 **사기성 사용** 타일이 변경되고 꺾은선형 차트가 데이터를 표시합니다. 

    ![Power BI 결과를 보여주는 스크린샷.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>리소스 정리

사용자가 만든 리소스를 모두 제거하려면 Power BI 시각화 데이터를 제거한 다음, 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 그룹 안에 포함된 모든 리소스가 삭제됩니다. 이 경우 이벤트 허브, 이벤트 허브 네임스페이스, 스트림 분석 작업 및 리소스 그룹 자체가 제거됩니다. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Power BI 시각화에서 리소스 정리

Power BI 계정에 로그인합니다. **내 작업 영역**으로 이동합니다. 대시보드 이름이 있는 줄에서 휴지통 아이콘을 클릭합니다. **데이터 세트**로 이동하고 휴지통 아이콘을 클릭하여 데이터 세트(**contosoehdataset**)를 삭제합니다.

### <a name="clean-up-resources-using-azure-cli"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>PowerShell을 사용하여 리소스 정리

리소스 그룹을 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 명령을 사용합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.
> [!div class="checklist"]
> * Event Hubs 네임스페이스 만들기
> * 이벤트 허브 만들기
> * 이벤트를 시뮬레이션하고 이벤트 허브로 보내는 앱 실행
> * 허브로 전송된 이벤트를 처리하는 Stream Analytics 작업 구성
> * Power BI 시각화를 구성하여 결과 표시

Azure Event Hubs에 대해 자세히 알아보려면 다음 문서를 진행하세요.

> [!div class="nextstepaction"]
> [.NET Standard를 사용하여 Azure Event Hubs로 메시지 전송 시작](get-started-dotnet-standard-send-v2.md)

[계정을 만드세요]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
