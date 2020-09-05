---
title: 환경에 이벤트 보내기-Azure Time Series Insights | Microsoft Docs
description: 이벤트 허브를 구성 하 고, 샘플 응용 프로그램을 실행 하 고, Azure Time Series Insights 환경에 이벤트를 전송 하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 3a5ee1cc8efead7c29dadaf64adb8e2686a10621
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168085"
---
# <a name="send-events-to-an-azure-time-series-insights-gen1-environment-by-using-an-event-hub"></a>이벤트 허브를 사용 하 여 Azure Time Series Insights Gen1 환경으로 이벤트 보내기

이 문서에서는 Azure Event Hubs에서 이벤트 허브를 만들고 구성 하는 방법을 설명 합니다. 또한 Event Hubs에서 Azure Time Series Insights 하는 이벤트를 푸시하는 샘플 응용 프로그램을 실행 하는 방법을 설명 합니다. JSON 형식의 이벤트가 있는 기존 이벤트 허브가 있는 경우이 자습서를 건너뛰고 [Azure Time Series Insights](./time-series-insights-update-create-environment.md)에서 환경을 확인 합니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성

1. 이벤트 허브를 만드는 방법에 대 한 자세한 내용은 [Event Hubs 설명서](https://docs.microsoft.com/azure/event-hubs/)를 참조 하세요.
1. 검색 상자에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.
1. 이벤트 허브를 선택합니다.
1. 이벤트 허브를 만들 때 event hubs 네임 스페이스를 만듭니다. 네임 스페이스 내에서 이벤트 허브를 아직 만들지 않은 경우 메뉴의 **엔터티**아래에서 이벤트 허브를 만듭니다.  

    [![이벤트 허브의 목록](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. 이벤트 허브를 만든 후 이벤트 허브의 목록에서 선택합니다.
1. 메뉴의 **엔터티**아래에서 **Event Hubs**를 선택 합니다.
1. 구성할 이벤트 허브의 이름을 선택합니다.
1. **개요**아래에서 **소비자 그룹**을 선택 하 고 **소비자 그룹**을 선택 합니다.

    [![소비자 그룹 만들기](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. Azure Time Series Insights 이벤트 원본에 의해 독점적으로 사용 되는 소비자 그룹을 만들어야 합니다.

    > [!IMPORTANT]
    > 이 소비자 그룹이 Azure Stream Analytics 작업 또는 다른 Azure Time Series Insights 환경과 같은 다른 서비스에서 사용 되지 않는지 확인 합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스 모두에 부정적인 영향을 미칩니다. 소비자 그룹으로 **$Default**를 사용하는 경우 다른 읽기 권한자는 소비자 그룹을 잠재적으로 다시 사용할 수 있습니다.

1. 메뉴의 **설정**에서 **공유 액세스 정책**을 선택 하 고 **추가**를 선택 합니다.

    [![공유 액세스 정책을 선택한 다음, 추가 단추를 선택합니다.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. **새 공유 액세스 정책 추가** 창에서 **MySendPolicy**라는 공유 액세스를 만듭니다. 이 공유 액세스 정책을 사용 하 여이 문서의 뒷부분에 나오는 c # 예제에서 이벤트를 전송 합니다.

    [![정책 이름 상자에 MySendPolicy 입력](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. **클레임**에서 **송신** 확인란을 선택 합니다.

## <a name="add-an-azure-time-series-insights-instance"></a>Azure Time Series Insights 인스턴스 추가

Azure Time Series Insights Gen2에서 TSM (시계열 모델)을 사용 하 여 들어오는 원격 분석에 컨텍스트 데이터를 추가할 수 있습니다. TSM에서 태그나 신호를 *인스턴스* 라고 하며, *인스턴스 필드* 에 상황별 데이터를 저장할 수 있습니다. 데이터는 **시계열 ID**를 사용하여 쿼리 시에 조인됩니다. 이 문서의 뒷부분에서 사용 하는 샘플 windmills 프로젝트의 **시계열 ID** 는 `id` 입니다. 인스턴스 필드에 데이터를 저장 하는 방법에 대 한 자세한 내용은 시계열 [모델](./concepts-model-overview.md) 개요를 참조 하세요.

### <a name="create-an-azure-time-series-insights-event-source"></a>Azure Time Series Insights 이벤트 원본 만들기

1. 아직 이벤트 원본을 만들지 않은 경우 [이벤트 원본 만들기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)에 대한 단계를 완료합니다.

1. `timeSeriesId`에 대한 값을 설정합니다. 시계열 **ID**에 대 한 자세한 내용은 시계열 [모델](./concepts-model-overview.md)을 참조 하세요.

### <a name="push-events-to-windmills-sample"></a>Windmills 샘플로 이벤트 푸시

1. 검색 창에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.

1. 이벤트 허브 인스턴스를 선택 합니다.

1. **공유 액세스 정책**  >  **MySendPolicy**으로 이동 합니다. **연결 문자열-기본 키**의 값을 복사 합니다.

    [![기본 키 연결 문자열의 값 복사](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. <https://tsiclientsample.azurewebsites.net/windFarmGen.html>로 이동합니다. URL은 시뮬레이트된 풍차 장치를 만들고 실행 합니다.
1. 웹 페이지의 **이벤트 허브 연결 문자열** 상자에, [풍차 입력 필드](#push-events-to-windmills-sample)에 복사한 연결 문자열을 붙여넣습니다.
  
    [![이벤트 허브 연결 문자열 상자에 기본 키 연결 문자열 붙여넣기](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. **시작하려면 클릭**을 선택합니다.

    > [!TIP]
    > 또한 풍차 시뮬레이터는 [AZURE TIME SERIES INSIGHTS GA 쿼리 api](https://docs.microsoft.com/rest/api/time-series-insights/gen1-query)를 사용 하 여 페이로드로 사용할 수 있는 JSON을 만듭니다.

    > [!NOTE]
    > 시뮬레이터는 브라우저 탭이 닫힐 때까지 데이터를 계속 보냅니다.

1. Azure Portal에서 이벤트 허브로 돌아갑니다. **개요** 페이지에 이벤트 허브에서 받은 새 이벤트가 표시 됩니다.

    [![이벤트 허브에 대한 메트릭을 보여주는 이벤트 허브 개요 페이지](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>지원되는 JSON 셰이프

### <a name="example-one"></a>예 1

* **Input**: 간단한 JSON 개체입니다.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **출력**: 하나의 이벤트

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>예 2

* **Input**: 두 json 개체를 포함 하는 json 배열입니다. 각 JSON 개체는 이벤트로 변환됩니다.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **출력**: 두 이벤트입니다.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>예 3

* **Input**: 두 json 개체를 포함 하는 중첩 된 json 배열이 포함 된 json 개체입니다.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **출력**: 두 이벤트입니다. **location** 속성은 각 이벤트로 복사됩니다.

    |위치|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>예 4

* **Input**: 두 json 개체를 포함 하는 중첩 된 json 배열이 포함 된 json 개체입니다. 이 입력은 복합 JSON 개체로 글로벌 속성을 표시할 수 있음을 보여줍니다.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **출력**: 두 이벤트입니다.

    |위치|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.type|events.data.type|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|압력|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계

* Azure Time Series Insights 탐색기에서 [환경을 확인](https://insights.timeseries.azure.com) 합니다.

* [IoT Hub 장치 메시지](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) 에 대 한 자세한 정보
