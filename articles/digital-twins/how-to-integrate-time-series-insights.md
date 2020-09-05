---
title: Azure Time Series Insights와 통합
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에서 Azure Time Series Insights로 이벤트 경로를 설정 하는 방법을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bda07d0e14ddc630bde4fdc9c869704154c1e6cc
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236355"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Azure Time Series Insights와 Azure Digital Twins 통합

이 문서에서는 [twins (Azure Time Series Insights](../time-series-insights/overview-what-is-tsi.md)와 Azure Digital twins를 통합 하는 방법에 대해 알아봅니다.

이 문서에서 설명 하는 솔루션을 통해 IoT 솔루션에 대 한 기록 데이터를 수집 하 고 분석할 수 있습니다. Azure Digital Twins는 데이터를 Time Series Insights으로 보내기 전에 데이터를 표준화 하 고 정보를 표준화 하는 데 사용할 수 있기 때문에 데이터를 Time Series Insights으로 공급할 수 있는 좋은 방법입니다. 

## <a name="prerequisites"></a>필수 구성 요소

Time Series Insights와의 관계를 설정 하려면 먼저 **Azure Digital Twins 인스턴스가**있어야 합니다. 이 인스턴스는 데이터를 기반으로 하는 디지털 쌍 정보를 업데이트 하는 기능을 사용 하 여 설정 해야 하며, Time Series Insights에서 추적 되는 데이터를 확인 하기 위해 쌍 정보를 몇 번 업데이트 해야 합니다. 

이 설정이 아직 없는 경우 Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)을 따라 만들 수 있습니다. 이 자습서에서는 디지털 쌍 업데이트를 트리거하는 가상 IoT 장치에서 작동 하는 Azure Digital Twins 인스턴스를 설정 하는 과정을 안내 합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

아래 경로를 통해 Azure Digital Twins에 Time Series Insights을 연결 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="종단 간 시나리오에서 강조 표시 되는 Azure 서비스의 보기 Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>경로 만들기 및 쌍 업데이트 알림을 위한 필터

Azure Digital Twins 인스턴스는 쌍의 상태가 업데이트 될 때마다 쌍 [업데이트 이벤트](how-to-interpret-event-data.md) 를 내보낼 수 있습니다. 이 섹션에서는 추가 처리를 위해 이러한 업데이트 이벤트를 Azure [Event Hubs](../event-hubs/event-hubs-about.md) 로 전달 하는 Azure Digital twins [**이벤트 경로**](concepts-route-events.md) 를 만듭니다.

Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md) 은 온도계를 사용 하 여 방을 나타내는 디지털 쌍의 온도 특성을 업데이트 하는 시나리오를 안내 합니다. 이 패턴은 IoT 장치에서 원격 분석을 전달 하는 대신 쌍 업데이트를 사용 하 여 Time Series Insights 논리를 업데이트할 필요 없이 기본 데이터 원본을 변경할 수 있는 유연성을 제공 합니다.

1. 먼저 Azure Digital Twins 인스턴스에서 이벤트를 수신 하는 event hub 네임 스페이스를 만듭니다. 아래 Azure CLI 지침을 사용 하거나 Azure Portal: [*빠른 시작: Azure Portal를 사용 하 여 이벤트 허브 만들기*](../event-hubs/event-hubs-create.md)를 사용할 수 있습니다.

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. 네임 스페이스 내에 이벤트 허브를 만듭니다.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. 보내기 및 받기 권한을 사용 하 여 [권한 부여 규칙](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) 을 만듭니다.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Event grid 토픽을 Azure Digital Twins 인스턴스에 연결 하는 Azure Digital Twins [끝점](concepts-route-events.md#create-an-endpoint) 을 만듭니다.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. 엔드포인트 업데이트 이벤트를 끝점으로 보내기 위해 Azure Digital Twins에서 [경로](concepts-route-events.md#create-an-event-route) 를 만듭니다. 이 경로의 필터는 쌍 업데이트 메시지만 끝점에 전달 되도록 허용 합니다.

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

이동 하기 전에이 문서의 뒷부분에서 다른 이벤트 허브를 만들 때 사용 하는 것 처럼 *Event Hubs 네임 스페이스* 및 *리소스 그룹*을 기록해 둡니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기 

다음으로 함수 앱 내에서 Event Hubs 트리거 함수를 만듭니다. 종단 간 자습서에서 만든 함수 앱 ([*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)) 또는 자체를 사용할 수 있습니다. 

이 함수는 이러한 쌍 업데이트 이벤트를 원래 폼에서 json 패치 문서로 변환 하 여 업데이트 되 고 쌍의 추가 된 값만 포함 하는 JSON 개체로 변환 합니다.

Azure 함수에서 Event Hubs를 사용 하는 방법에 대 한 자세한 내용은 [*Azure Functions에 대 한 azure Event Hubs 트리거*](../azure-functions/functions-bindings-event-hubs-trigger.md)를 참조 하세요.

게시 된 함수 앱 내에서 함수 코드를 다음 코드로 바꿉니다.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

그러면 함수는 만든 JSON 개체를 두 번째 이벤트 허브에 전송 하 여 Time Series Insights에 연결 합니다.

나중에이 함수가 고유한 event hubs에 연결 하는 데 사용 하는 환경 변수를 설정할 수도 있습니다.

## <a name="send-telemetry-to-an-event-hub"></a>이벤트 허브로 원격 분석 전송

이제 두 번째 이벤트 허브를 만들고 해당 출력을 해당 이벤트 허브로 스트리밍하는 함수를 구성 합니다. 그러면이 이벤트 허브가 Time Series Insights에 연결 됩니다.

### <a name="create-an-event-hub"></a>이벤트 허브 만들기

두 번째 이벤트 허브를 만들려면 아래 Azure CLI 지침을 사용 하거나 Azure Portal: [*빠른 시작: Azure Portal를 사용 하 여 이벤트 허브 만들기*](../event-hubs/event-hubs-create.md)를 사용 합니다.

1. 이 문서의 앞 부분에서 *Event Hubs 네임 스페이스* 및 *리소스 그룹* 이름 준비

2. 새 이벤트 허브 만들기
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. 보내기 및 받기 권한을 사용 하 여 [권한 부여 규칙](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) 만들기
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>함수 구성

다음으로 이전에 만든 이벤트 허브에 대 한 연결 문자열을 포함 하는 함수 앱에서 환경 변수를 설정 해야 합니다.

### <a name="set-the-twins-event-hub-connection-string"></a>Twins 이벤트 허브 연결 문자열 설정

1. Twins 허브에 대해 위에서 만든 권한 부여 규칙을 사용 하 여 Twins [이벤트 허브 연결 문자열](../event-hubs/event-hubs-get-connection-string.md)을 가져옵니다.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. 결과로 얻을 수 있는 연결 문자열을 사용 하 여 연결 문자열을 포함 하는 함수 앱에 앱 설정을 만듭니다.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Time Series Insights 이벤트 허브 연결 문자열 설정

1. Time Series Insights 허브에 대해 위에서 만든 권한 부여 규칙을 사용 하 여 TSI [이벤트 허브 연결 문자열](../event-hubs/event-hubs-get-connection-string.md)을 가져옵니다.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. 함수 앱에서 연결 문자열을 포함 하는 앱 설정을 만듭니다.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights 인스턴스 만들기 및 연결

다음으로 두 번째 이벤트 허브에서 데이터를 받도록 Time Series Insights 인스턴스를 설정 합니다. 아래 단계를 수행 하 고이 프로세스에 대 한 자세한 내용은 [*자습서: Azure Time Series Insights GEN2 PAYG 환경 설정*](../time-series-insights/tutorials-set-up-tsi-environment.md)을 참조 하세요.

1. Azure Portal에서 Time Series Insights 리소스 만들기를 시작 합니다. 
    1. **PAYG (미리 보기)** 가격 책정 계층을 선택 합니다.
    2. 이 환경에 대 한 **시계열 ID** 를 선택 해야 합니다. 시계열 ID는 Time Series Insights에서 데이터를 검색 하는 데 사용할 수 있는 최대 3 개의 값이 될 수 있습니다. 이 자습서에서는 **$dtId**를 사용할 수 있습니다. [*시계열 id를 선택 하는 방법에 대 한 자세한 내용은 모범 사례*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid)에서 id 값 선택을 참조 하세요.
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Time Series Insights 환경의 생성 포털 UX입니다. PAYG (미리 보기) 가격 책정 계층이 선택 되 고 시계열 ID 속성 이름이 $dtId":::

2. **다음: 이벤트 원본** 을 선택 하 고 위에서 Event Hubs 정보를 선택 합니다. 새 Event Hubs 소비자 그룹을 만들어야 할 수도 있습니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Time Series Insights 환경 이벤트 원본에 대 한 생성 포털 UX입니다. 위의 이벤트 허브 정보를 사용 하 여 이벤트 원본을 만듭니다. 또한 새 소비자 그룹을 만듭니다.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>IoT 데이터를 Azure Digital Twins로 보내기 시작

Time Series Insights로 데이터를 보내기 시작 하려면 변경 데이터 값을 사용 하 여 Azure Digital Twins에서 디지털 쌍 속성 업데이트를 시작 해야 합니다. [Az dt 쌍 update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) 명령을 사용 합니다.

종단 간 자습서 ([*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md))를 사용 하 여 환경 설정에 도움이 되는 경우 샘플에서 *DeviceSimulator* 프로젝트를 실행 하 여 시뮬레이트된 IoT 데이터 보내기를 시작할 수 있습니다. 지침은 자습서의 [*시뮬레이션 구성 및 실행*](tutorial-end-to-end.md#configure-and-run-the-simulation) 섹션에 있습니다.

## <a name="visualize-your-data-in-time-series-insights"></a>Time Series Insights에서 데이터 시각화

이제 데이터를 Time Series Insights 인스턴스로 이동 하 여 분석할 준비가 되어 있어야 합니다. 다음 단계를 수행 하 여에서 제공 되는 데이터를 탐색 합니다.

1. [Azure Portal](https://portal.azure.com) 에서 Time Series Insights 인스턴스를 엽니다. 포털 검색 표시줄에서 인스턴스의 이름을 검색할 수 있습니다. 인스턴스 개요에 표시 된 *Time Series Insights 탐색기 URL* 을 방문 합니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Time Series Insights 환경의 개요 탭에서 Time Series Insights 탐색기 URL을 선택 합니다.":::

2. 탐색기의 왼쪽에 표시 되는 Azure Digital 쌍에서 3 개의 쌍이 표시 됩니다. _**Thermostat67**_ 을 선택 하 고 **온도**를 선택 하 고 **추가**를 누릅니다.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="* * Thermostat67 * *를 선택 하 고 * * 온도 * *를 선택 하 고 * * 추가 * *를 선택 합니다.":::

3. 이제 아래와 같이 자동 온도 조절기의 초기 온도 판독값이 표시 됩니다. *Room21* 및 *floor1*에 대 한 동일한 온도 읽기가 업데이트 되며 이러한 데이터 스트림을 동시에 시각화할 수 있습니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="초기 온도 데이터는 TSI 탐색기에서 그래프로 표현 됩니다. 68과 85 사이의 임의 값 줄입니다.":::

4. 시뮬레이션을 훨씬 더 오랫동안 실행 하도록 허용 하면 시각화가 다음과 같이 표시 됩니다.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="각 쌍에 대 한 온도 데이터는 서로 다른 색의 세 평행 선으로 그래프로 표현 됩니다.":::

## <a name="next-steps"></a>다음 단계

디지털 쌍은 기본적으로 Time Series Insights의 플랫 계층으로 저장 되지만 모델 정보와 조직의 다단계 계층 구조를 사용 하 여 보강 수 있습니다. 이 프로세스에 대 한 자세한 내용은 다음을 참조 하세요. 

* [*자습서: 모델 정의 및 적용*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Azure Digital Twins에 이미 저장 된 모델 및 그래프 데이터를 사용 하 여이 정보를 자동으로 제공 하는 사용자 지정 논리를 작성할 수 있습니다. 쌍 그래프에서 정보를 관리, 업그레이드 및 검색 하는 방법에 대 한 자세한 내용은 다음 참조를 참조 하세요.

* [*방법: 디지털 쌍 관리*](./how-to-manage-twin.md)
* [*방법: 쌍 그래프 쿼리*](./how-to-query-graph.md)