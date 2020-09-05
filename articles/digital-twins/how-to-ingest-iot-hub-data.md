---
title: IoT Hub에서 원격 분석 수집
titleSuffix: Azure Digital Twins
description: IoT Hub에서 장치 원격 분석 메시지를 수집 하는 방법을 참조 하세요.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 47e4bb291d031c41c89c88435a795004490e20a1
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505328"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Azure Digital Twins에 IoT Hub 원격 분석 수집

Azure Digital Twins는 IoT 장치 및 기타 원본의 데이터를 기반으로 합니다. Azure Digital Twins에서 사용할 장치 데이터의 공통 소스는 [IoT Hub](../iot-hub/about-iot-hub.md)됩니다.

Azure digital 쌍로 데이터를 수집 하는 프로세스는 [azure function](../azure-functions/functions-overview.md)과 같은 외부 계산 리소스를 설정 하 여 데이터를 수신 하 고, [DigitalTwins api](how-to-use-apis-sdks.md) 를 사용 하 여 속성을 설정 하거나, 그에 따라 [디지털](concepts-twins-graph.md) 쌍에서 원격 분석 이벤트를 발생 시킵니다. 

이 방법 문서에서는 IoT Hub에서 원격 분석을 수집할 수 있는 Azure 함수를 작성 하는 프로세스를 안내 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 예를 계속 하기 전에 다음 리소스를 필수 조건으로 설정 해야 합니다.
* **IoT hub**. 지침은 [이 IoT Hub 빠른](../iot-hub/quickstart-send-telemetry-cli.md)시작의 *IoT Hub 만들기* 섹션을 참조 하세요.
* 디지털 쌍 인스턴스를 호출 하기 위한 올바른 권한이 있는 **Azure 함수** 입니다. 자세한 내용은 [*방법: 데이터 처리를 위한 Azure 함수 설정*](how-to-create-azure-function.md)을 참조 하세요. 
* 장치 원격 분석을 수신 하는 **Azure Digital Twins 인스턴스입니다** . 자세한 내용은 [*방법: Azure Digital Twins 인스턴스 및 인증 설정*](./how-to-set-up-instance-portal.md)을 참조 하세요.

### <a name="example-telemetry-scenario"></a>원격 분석 시나리오 예제

이 방법에서는 Azure function을 사용 하 여 IoT Hub에서 Azure Digital Twins로 메시지를 보내는 방법을 간략하게 설명 합니다. 이 작업에 사용할 수 있는 여러 가지 구성 및 일치 전략이 있지만이 문서의 예제에는 다음 부분이 포함 되어 있습니다.
* IoT Hub의 온도계 장치로, 알려진 장치 ID가 있습니다.
* 일치 하는 ID를 사용 하 여 장치를 나타내는 디지털 쌍

> [!NOTE]
> 이 예에서는 장치 ID와 해당 하는 디지털 쌍 ID 사이에 간단한 ID 일치를 사용 하지만, 장치에서 해당 쌍 (예: 매핑 테이블)으로 보다 정교한 매핑을 제공할 수 있습니다.

온도계 장치에서 온도 원격 분석 이벤트를 보낼 때마다 디지털 쌍의 *온도* 속성이 업데이트 되어야 합니다. 이 시나리오는 아래 다이어그램에 설명 되어 있습니다.

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="순서도를 표시 하는 다이어그램입니다. 차트에서 IoT Hub 장치는 azure 함수에 IoT Hub를 통해 온도 원격 분석을 전송 합니다 .이 함수는 Azure에서 쌍으로 된 쌍의 온도 속성을 업데이트 합니다." border="false":::

## <a name="add-a-model-and-twin"></a>모델 및 쌍 추가

IoT hub 정보로 업데이트 하려면 쌍이 필요 합니다.

모델은 다음과 같습니다.
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

**이 모델을 쌍 인스턴스에 업로드**하려면 Azure CLI을 열고 다음 명령을 실행 합니다.
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

그런 다음 **이 모델을 사용 하 여 하나의**쌍을 만들어야 합니다. 다음 명령을 사용 하 여 쌍을 만들고 0.0을 초기 온도 값으로 설정 합니다.
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

성공한 쌍 만들기 명령의 출력은 다음과 같습니다.
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 섹션에서는 동일한 Visual Studio 시작 단계와 [*방법: 데이터 처리를 위한 azure 함수 설정*](how-to-create-azure-function.md)의 azure function 기초를 사용 합니다. 이 기초는 인증을 처리 하 고 서비스 클라이언트를 만들어 데이터를 처리 하 고 Azure Digital Twins Api를 응답으로 호출할 수 있도록 준비 했습니다. 

다음 단계에서는 IoT Hub에서 IoT 원격 분석 이벤트를 처리 하기 위해 특정 코드를 추가 합니다.  

### <a name="add-telemetry-processing"></a>원격 분석 처리 추가
    
원격 분석 이벤트는 장치의 메시지 형식으로 제공 됩니다. 원격 분석 처리 코드를 추가 하는 첫 번째 단계는 Event Grid 이벤트에서이 장치 메시지의 관련 부분을 추출 하는 것입니다. 

장치 마다 메시지를 다르게 구성할 수 있으므로이 단계의 코드는 **연결 된 장치에 따라 다릅니다.** 

다음 코드는 원격 분석을 JSON으로 전송 하는 간단한 장치에 대 한 예를 보여 줍니다. 이 샘플은 [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)에서 완전히 탐색 됩니다. 다음 코드는 메시지를 보낸 장치의 장치 ID 및 온도 값을 찾습니다.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

다음 코드 샘플은 ID 및 온도 값을 사용 하 여 해당 쌍을 "패치" (로 업데이트) 하는 데 사용 합니다.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Azure 함수 코드 업데이트

이전 샘플의 코드를 이해 했으므로 Visual Studio를 열고 Azure 함수 코드를이 샘플 코드로 바꿉니다.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>함수를 IoT Hub에 연결

1. 허브 데이터의 이벤트 대상을 설정 합니다. [Azure Portal](https://portal.azure.com/)에서 IoT Hub 인스턴스로 이동 합니다. **이벤트**아래에서 Azure function에 대 한 구독을 만듭니다. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="이벤트 구독을 추가 하는 것을 보여 주는 Azure Portal의 스크린샷":::

2. **이벤트 구독 만들기** 페이지에서 다음과 같이 필드를 채웁니다.
    1. **이름**아래에서 원하는 구독 이름을로 선택 합니다.
    2. **이벤트 스키마**에서 **Event Grid 스키마**를 선택 합니다.
    3. **시스템 항목 이름**에서 고유한 이름을 선택 합니다.
    4. **이벤트 유형**에서 필터링 할 이벤트 유형으로 **장치 원격 분석** 을 선택 합니다.
    5. **끝점 세부 정보**에서 끝점으로 Azure 함수를 선택 합니다.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="이벤트 구독 정보를 표시 하는 Azure Portal의 스크린샷":::

## <a name="send-simulated-iot-data"></a>시뮬레이션 된 IoT 데이터 보내기

새 수신 함수를 테스트 하려면 [*자습서: 종단 간 솔루션 연결*](./tutorial-end-to-end.md)에서 장치 시뮬레이터를 사용 합니다. 이 자습서는 c #으로 작성 된 샘플 프로젝트를 기반으로 합니다. 샘플 코드는 [Azure Digital Twins 샘플](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples)에 있습니다. 해당 리포지토리에서 **DeviceSimulator** 프로젝트를 사용 하 게 됩니다.

종단 간 자습서에서 다음 단계를 완료 합니다.
1. [*IoT Hub에 시뮬레이션된 디바이스 등록*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*시뮬레이션 구성 및 실행*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>결과 유효성 검사

위의 장치 시뮬레이터를 실행 하는 동안 디지털 쌍의 온도 값이 변경 됩니다. Azure CLI에서 다음 명령을 실행 하 여 온도 값을 확인 합니다.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

출력에는 다음과 같은 온도 값이 포함 되어야 합니다.

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

값 변경을 보려면 위의 쿼리 명령을 반복 해 서 실행 합니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins를 사용 하 여 데이터 수신 및 송신에 대해 읽기:
* [*개념: 다른 서비스와 통합*](concepts-integration.md)
