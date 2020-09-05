---
title: Azure Maps와 통합
titleSuffix: Azure Digital Twins
description: 쌍으로 된 실내 지도를 Azure Maps 업데이트 하는 데 쌍을 사용 하는 Azure 함수를 만드는 방법 (쌍 그래프 및 Azure Digital Twins 알림 사용)을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: a2dff1ea9c830fa48545dc25654cc3c5318c3415
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235913"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure Digital Twins를 사용 하 여 Azure Maps 실내 맵 업데이트

이 문서에서는 [Azure Maps](../azure-maps/about-azure-maps.md)를 사용 하 여 *실내 맵에* 표시 되는 정보를 업데이트 하기 위해 Azure Digital twins 데이터를 사용 하는 데 필요한 단계를 안내 합니다. Azure Digital Twins는 IoT 장치 관계의 그래프를 저장 하 고 여러 끝점에 원격 분석을 라우팅합니다 .이를 통해 지도에서 정보 오버레이를 업데이트 하는 데 완벽 한 서비스를 사용할 수 있습니다.

이 방법에는 다음이 포함 됩니다.

1. [Azure Functions](../azure-functions/functions-overview.md)의 함수에 쌍 업데이트 이벤트를 보내도록 Azure Digital twins 인스턴스를 구성 합니다.
2. Azure Maps 실내 지도 기능 stateset을 업데이트 하는 Azure 함수를 만듭니다.
3. Azure Digital Twins 그래프에서 지도 ID와 기능 stateset ID를 저장 하는 방법입니다.

### <a name="prerequisites"></a>필수 구성 요소

* Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)을 참조 하세요.
    * 추가 끝점 및 경로를 사용 하 여이 쌍을 확장 합니다. 또한이 자습서의 함수 앱에 다른 함수를 추가 합니다. 
* Azure Maps [*자습서: Azure Maps Creator를 사용 하 여 실내 지도를 만들어*](../azure-maps/tutorial-creator-indoor-maps.md) *기능 stateset*을 사용 하 여 Azure Maps 실내 지도를 만듭니다.
    * [기능 statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) 는 방 또는 장비와 같은 데이터 집합 기능에 할당 된 동적 속성 (상태)의 컬렉션입니다. 위의 Azure Maps 자습서에서 stateset 기능은 지도에 표시 되는 대화방 상태를 저장 합니다.
    * 기능 *stateset ID* 및 AZURE MAPS *구독 id*가 필요 합니다.

### <a name="topology"></a>토폴로지

아래 이미지는이 자습서의 실내 지도 통합 요소가 더 크고 종단 간 Azure 디지털 Twins 시나리오에 적합 한 위치를 보여 줍니다.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="종단 간 시나리오에서 실내 지도 통합 피스를 강조 표시 하는 Azure 서비스의 뷰입니다." lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>쌍 업데이트 시 맵을 업데이트 하는 함수 만들기

먼저 모든 쌍 업데이트 이벤트를 event grid 토픽으로 전달 하기 위해 Azure Digital Twins에서 경로를 만듭니다. 그런 다음 Azure function을 사용 하 여 이러한 업데이트 메시지를 읽고 Azure Maps의 stateset 기능을 업데이트 합니다. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>경로 만들기 및 쌍 업데이트 알림을 위한 필터

Azure Digital Twins 인스턴스는 쌍의 상태가 업데이트 될 때마다 쌍 업데이트 이벤트를 내보낼 수 있습니다. Azure Digital Twins 자습서: 위에 연결 된 [*종단 간 솔루션 연결*](./tutorial-end-to-end.md) 은 온도계를 사용 하 여 실내 쌍에 연결 된 온도 특성을 업데이트 하는 시나리오를 안내 합니다. 쌍에 대 한 업데이트 알림을 구독 하 고 해당 정보를 사용 하 여 지도를 업데이트 하면 해당 솔루션을 확장할 수 있습니다.

이 패턴은 매핑 논리를 업데이트할 필요 없이 온도에 대 한 기본 데이터 원본을 유연 하 게 변경할 수 있도록 하는 IoT 장치가 아닌 대화방 쌍에서 직접 읽습니다. 예를 들어 지도 논리를 업데이트할 필요 없이 여러 개의 온도계 모양를 추가 하거나이 공간을 설정 하 여 다른 방에 온도계를 공유할 수 있습니다.

1. Azure Digital Twins 인스턴스에서 이벤트를 수신 하는 event grid 토픽을 만듭니다.
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Event grid 토픽을 Azure Digital Twins에 연결 하는 끝점을 만듭니다.
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. 엔드포인트 업데이트 이벤트를 끝점으로 보내기 위해 Azure Digital Twins에서 경로를 만듭니다.
    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>맵을 업데이트 하는 Azure function 만들기

종단 간 자습서에서 함수 앱 내에 Event Grid 트리거 함수를 만들려고 합니다 ([*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)). 이 함수는 이러한 알림의 압축을 풀고 Azure Maps 기능으로 업데이트를 전송 하 여 한 방에 대 한 온도를 업데이트 합니다. 

참조 정보는 [*Azure Functions에 대 한 Azure Event Grid 트리거*](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger)문서를 참조 하세요.

함수 코드를 다음 코드로 바꿉니다. 이 파일은 공간 쌍에 대 한 업데이트만 필터링 하 고 업데이트 된 온도를 읽고 해당 정보를 Azure Maps으로 보냅니다.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in your map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

함수 앱에서 두 환경 변수를 설정 해야 합니다. 하나는 [Azure Maps 기본 구독 키](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)이 고 하나는 [AZURE MAPS stateset ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset)입니다.

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>지도에서 라이브 업데이트 보기

라이브 업데이트 온도를 보려면 다음 단계를 수행 합니다.

1. Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)에서 **DeviceSimulator** 프로젝트를 실행 하 여 시뮬레이션 된 IoT 데이터 보내기를 시작 합니다. 이에 대 한 지침은 [*시뮬레이션 구성 및 실행*](././tutorial-end-to-end.md#configure-and-run-the-simulation) 섹션에 나와 있습니다.
2. [ **Azure Maps 실내** 모듈](../azure-maps/how-to-use-indoor-module.md) 을 사용 하 여 Azure Maps 작성자에서 만든 실내 지도를 렌더링 합니다.
    1. [*예:*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) 실내 지도 자습서: 로컬 파일에 [*Azure Maps 실내 맵 모듈 사용*](../azure-maps/how-to-use-indoor-module.md) 에서 HTML을 복사 합니다.
    1. 로컬 HTML 파일의 *tilesetId* 및 *statesetID* 를 사용자의 값으로 바꿉니다.
    1. 브라우저에서 해당 파일을 엽니다.

두 샘플 모두 호환 범위에서 온도를 전송 하므로 30 초 마다 맵에 room 121 업데이트의 색이 표시 되어야 합니다.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="방 121 컬러 주황을 보여 주는 사무실 지도":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Azure Digital Twins에 maps 정보 저장

이제 맵 정보를 업데이트 하는 하드 코드 된 솔루션이 있으므로 Azure Digital Twins 그래프를 사용 하 여 실내 지도를 업데이트 하는 데 필요한 모든 정보를 저장할 수 있습니다. 여기에는 각 맵과 위치의 stateset ID, maps 구독 ID 및 기능 ID가 포함 됩니다. 

이 특정 예제에 대 한 솔루션에는 각 최상위 공간에서 stateset ID를 업데이트 하 고 구독 ID 특성을 매핑하고 기능 ID를 포함 하도록 각 공간을 업데이트 하는 작업이 포함 됩니다. 쌍 그래프를 초기화할 때 이러한 값을 한 번 설정 하 고 각 쌍 업데이트 이벤트에 대해 해당 값을 쿼리 해야 합니다.

토폴로지의 구성에 따라 이러한 세 가지 특성을 지도의 세분성과 상관 관계를 서로 다른 수준에서 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

쌍 그래프에서 정보를 관리, 업그레이드 및 검색 하는 방법에 대 한 자세한 내용은 다음 참조를 참조 하세요.

* [*방법: digital 쌍 관리*](./how-to-manage-twin.md)
* [*방법: 쌍 그래프 쿼리*](./how-to-query-graph.md)