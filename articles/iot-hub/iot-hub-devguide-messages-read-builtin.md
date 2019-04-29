---
title: Azure IoT Hub 기본 제공 엔드포인트 이해 | Microsoft Docs
description: 개발자 가이드에서는 기본 제공 Event Hub 호환 엔드포인트를 사용하여 디바이스-클라우드 메시지를 읽는 방법을 설명합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 827d7d9a3d584342703a84dd2a42e5cda9b3a656
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364073"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 디바이스-클라우드 메시지 읽기

기본적으로 메시지는 [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)와 호환되는 기본 제공 서비스 연결 엔드포인트(**messages/events**)로 라우팅됩니다. 이 엔드포인트는 현재 [AMQP](https://www.amqp.org/) 프로토콜을 통해서만 포트 5671에 노출됩니다. IoT Hub는 다음 속성을 노출하여 기본 제공 Event Hub와 호환되는 메시징 엔드포인트 **messages/events**를 제어할 수 있게 합니다.

| 자산            | 설명 |
| ------------------- | ----------- |
| **분할 개수** | 이 속성은 생성 시 설정하여 디바이스-클라우드 이벤트 수집에 대한 [파티션](../event-hubs/event-hubs-features.md#partitions) 수를 정의합니다. |
| **보존 시간**  | 이 속성은 IoT Hub에서 메시지를 보존할 일 수를 지정합니다. 기본값은 1일이지만 7일로 늘릴 수 있습니다. |

IoT Hub의 경우 최대 7 일에 대 한 기본 제공 Event Hubs의 데이터 보존을 허용합니다. IoT Hub를 만드는 동안 보존 기간을 설정할 수 있습니다. IoT Hub에서 데이터 보존 시간 단위 입력 하 여 IoT hub 계층에 따라 달라 집니다. 크기 측면에서 기본 제공 Event Hubs 할당량의 최소 24 시간까지 최대 메시지 크기의 메시지를 유지할 수 있습니다. 예를 들어 1 S1 단위 이상으로 유지 하려면 충분 한 저장소를 제공 하는 IoT Hub에 대 한 4k 400 K 메시지 크기가 각각. 장치는 더 작은 메시지를 보내는 경우 깁니다 (최대 7 일) 되는 저장소 크기에 따라에 대 한 유지 될 수 있습니다. 최소한 지정 된 보존 기간에 대 한 데이터 보존을 보장 합니다.

또한 IoT Hub를 사용하면 기본 제공 디바이스-클라우드 수신 엔드포인트에서 소비자 그룹을 관리할 수 있습니다.

사용 중인 경우 [메시지 라우팅을](iot-hub-devguide-messages-d2c.md) 및 [대체 경로](iot-hub-devguide-messages-d2c.md#fallback-route) 는 기본 끝점에 모든 경로 대 한 쿼리를 일치 하지 않는 모든 메시지 이동 사용 하도록 설정 합니다. 이 대체 경로 비활성화 하면 모든 쿼리를 일치 하지 않는 메시지가 삭제 됩니다.

[IoT Hub 리소스 공급자 REST API](/rest/api/iothub/iothubresource)를 사용하여 또는 [Azure Portal](https://portal.azure.com)을 사용하여 프로그래밍 방식으로 보존 시간을 수정할 수 있습니다.

IoT Hub는 허브에서 수신한 디바이스-클라우드 메시지를 읽도록 백 엔드 서비스의 기본 제공 엔드포인트인 **messages/events**를 공개합니다. 이 엔드포인트는 Event Hub와 호환되기 때문에 Event Hubs 서비스가 메시지 읽기에 대해 지원하는 모든 메커니즘을 사용할 수 있습니다.

## <a name="read-from-the-built-in-endpoint"></a>기본 제공 엔드포인트에서 읽기

일부 제품 통합 및 Event Hubs Sdk는 IoT Hub의 인식 되며 IoT hub 서비스 연결 문자열을 사용 하 여 기본 끝점에 연결할 수 있습니다.

Event Hubs Sdk 또는 IoT Hub를 인식 되지 않는 제품 통합을 사용 하는 경우는 Event Hub 호환 끝점 및 Event Hub 호환 이름이 필요 합니다. 포털에서 이러한 값을 다음과 같이 검색할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.

2. **기본 제공 엔드포인트**를 클릭합니다.

3. 합니다 **이벤트** 섹션에는 다음 값을 포함 합니다. **파티션을**, **Event Hub 호환 이름**를 **Event Hub 호환 끝점**를 **보존 시간**, 및 **소비자그룹**.

    ![디바이스-클라우드 설정](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

포털의 Event Hub 호환 끝점 필드 처럼 보이는 완료 Event Hubs 연결 문자열을 포함 합니다. **Endpoint=sb://abcd1234namespace.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=keykeykeykeykeykey=;EntityPath=iothub-ehub-abcd-1234-123456**. 사용 중인 SDK에서 다른 값에 필요한 경우 다음 됩니다.

| 이름 | 값 |
| ---- | ----- |
| 엔드포인트 | sb://abcd1234namespace.servicebus.windows.net/ |
| 호스트 이름 | abcd1234namespace.servicebus.windows.net |
| 네임스페이스 | abcd1234namespace |

지정된 Event Hubs에 연결할 수 있는 **ServiceConnect** 권한이 있는 공유 액세스 정책을 사용할 수 있습니다.

IoT Hub를 노출 하는 기본 제공 Event Hub 호환 끝점에 연결 하 여 Sdk는 다음과 같습니다.

| 언어 | SDK) | 예 | 메모 |
| -------- | --- | ------ | ----- |
| .NET | https://github.com/Azure/azure-event-hubs-dotnet | [빠른 시작](quickstart-send-telemetry-dotnet.md) | Event Hubs 호환 정보를 사용 하 여 |
 자바 | https://github.com/Azure/azure-event-hubs-java | [빠른 시작](quickstart-send-telemetry-java.md) | Event Hubs 호환 정보를 사용 하 여 |
| Node.js | https://github.com/Azure/azure-event-hubs-node | [빠른 시작](quickstart-send-telemetry-node.md) | IoT Hub 연결 문자열 사용 |
| Python | https://github.com/Azure/azure-event-hubs-python | https://github.com/Azure/azure-event-hubs-python/blob/master/examples/iothub_recv.py | IoT Hub 연결 문자열 사용 |

IoT Hub를 노출 하는 기본 제공 Event Hub 호환 끝점을 사용할 수 있는 제품 통합은 다음과 같습니다.

* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/). 참조 [Azure Functions를 사용 하 여 IoT Hub에서 데이터를 처리](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/)합니다.
* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) - 참조 [Stream Analytics에 입력으로 데이터를 Stream](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub)합니다.
* [Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/)합니다. 참조 [IoT hub 이벤트 원본을 Time Series Insights 환경에 추가](../time-series-insights/time-series-insights-how-to-add-an-event-source-iothub.md)합니다.
* [Apache Storm spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 을 볼 수 있습니다.
* [Apache Spark 통합](../hdinsight/spark/apache-spark-eventhub-streaming.md)
* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

## <a name="next-steps"></a>다음 단계

* IoT Hub 엔드포인트에 대한 자세한 내용은 [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md)를 참조하세요.

* [빠른 시작](quickstart-send-telemetry-node.md)에서는 시뮬레이트된 디바이스에서 디바이스-클라우드 메시지를 보내고 기본 제공 엔드포인트에서 메시지를 읽는 방법을 보여 줍니다. 

자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md) 자습서를 참조하세요.

* 디바이스-클라우드 메시지를 사용자 지정 엔드포인트로 라우팅하려면 [디바이스-클라우드 메시지에 대해 메시지 라우팅 및 사용자 지정 엔드포인트 사용](iot-hub-devguide-messages-read-custom.md)을 참조하세요.
