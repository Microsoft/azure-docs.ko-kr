---
title: Azure IoT Hub 작업 모니터링(사용되지 않음) | Microsoft Docs
description: IoT Hub 작업 모니터링을 사용하여 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링하는 방법입니다.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: nberdy
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 9d9824be536ca657d9213a47898ad19b0c39d8b4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022023"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>IoT Hub 작업 모니터링(사용되지 않음)

IoT Hub 작업 모니터링을 사용하면 실시간으로 IoT Hub에 대한 작업의 상태를 모니터링할 수 있습니다. IoT Hub는 몇 가지 작업 범주에 걸쳐 이벤트를 추적합니다. 하나 이상의 범주에서 IoT hub의 엔드포인트로 처리할 이벤트를 보내도록 선택할 수 있습니다. 데이터에 오류가 있는지 모니터링하거나 데이터 패턴을 기반으로 좀 더 복잡한 처리를 설정할 수 있습니다.

>[!NOTE]
>IoT Hub **작업 모니터링은 더 이상 사용되지 않으며, 2019년 3월 10일에 IoT Hub에서 제거되었습니다**. IoT Hub의 작동 및 상태 모니터링은 [Azure IoT Hub의 상태를 모니터링하고 문제를 신속하게 진단](iot-hub-monitor-resource-health.md)을 참조하세요. 사용 중단 타임라인에 대한 자세한 내용은 [Azure Monitor 및 Azure Resource Health로 Azure IoT 솔루션 모니터링](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health)을 참조하세요.

IoT Hub는 다음 여섯 가지 범주의 이벤트를 모니터링합니다.

* 디바이스 ID 작업
* 디바이스 원격 분석
* 클라우드-디바이스 메시지
* Connections
* 파일 업로드
* 메시지 라우팅

> [!IMPORTANT]
> IoT Hub 작업 모니터링은 신뢰할 수 있거나 순차적인 이벤트 전달을 보장하지 않습니다. IoT Hub 기본 인프라에 따라 일부 이벤트가 손실되거나 순서대로 전달되지 않을 수 있습니다. 작업 모니터링을 사용하여 실패한 연결 시도 또는 특정 디바이스에 대한 높은 빈도 연결 해제와 같은 오류 신호를 기반으로 경고를 생성하십시오. 작업 모니터링 이벤트에 의존하여 디바이스 상태에 대한 일관된 저장소(예: 디바이스의 연결 상태 또는 연결 해제 상태를 추적하는 저장소)를 만들지 말아야 합니다. 

## <a name="how-to-enable-operations-monitoring"></a>작업 모니터링을 사용하는 방법

1. IoT Hub를 만듭니다. IoT Hub를 만드는 방법에 관한 지침은 [시작](quickstart-send-telemetry-dotnet.md) 가이드에서 확인할 수 있습니다.

2. IoT Hub의 블레이드를 엽니다. 여기에서 **작업 모니터링**을 클릭합니다.

    ![포털의 작업 모니터링 구성 액세스](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. 모니터링하고자 하는 모니터링 범주를 선택한 다음 **저장**을 클릭합니다. 이벤트는 **모니터링 설정**에 나열된 이벤트 허브 호환 엔드포인트에서 읽어오는 데 사용할 수 있습니다. IoT Hub 엔드포인트는 `messages/operationsmonitoringevents`라고 합니다.

    ![IoT hub에서 작업 모니터링 구성](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> **연결** 범주에 대한 **자세한 정보 표시** 모니터링을 선택하면 IoT Hub가 추가 진단 메시지를 생성하게 됩니다. 다른 모든 범주의 경우 **자세한 정보 표시** 설정으로 인해 각 오류 메시지에서 IoT Hub가 포함하는 정보량이 달라집니다.

## <a name="event-categories-and-how-to-use-them"></a>이벤트 범주 및 사용 방법

각 작업 모니터링 범주는 IoT Hub와의 여러 상호 작용 유형을 추적하고 각 모니터링 범주에는 해당 범주의 이벤트가 어떻게 구성되는지를 정의하는 스키마가 있습니다.

### <a name="device-identity-operations"></a>디바이스 ID 작업

디바이스 ID 작업 범주는 IoT Hub ID 레지스트리의 항목을 만들거나, 업데이트하거나 삭제하려고 할 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 프로비전 시나리오에 유용합니다.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>디바이스 원격 분석

디바이스 원격 분석 범주는 IoT Hub에서 발생하고 원격 분석 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 원격 분석 이벤트를 보내고(예: 제한) 원격 분석 이벤트를 수신할 때(예: 무단된 판독기) 발생하는 오류가 포함됩니다. 이 범주는 디바이스 자체에서 실행되는 코드에 의해 발생한 오류를 포착할 수 없습니다.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>클라우드-디바이스 명령

클라우드-디바이스 명령 범주는 IoT Hub에서 발생하고 클라우드-디바이스 메시지 파이프라인에 관련된 오류를 추적합니다. 이 범주에는 클라우드-디바이스 메시지를 보낼 때(예: 권한이 없는 보낸 사람), 클라우드-디바이스 메시지를 받을 때(예: 전달 수 초과), 그리고 클라우드-디바이스 메시지 피드백을 받을 때(예: 피드백 만료) 발생하는 오류가 포함됩니다. 이 범주는 클라우드-디바이스 메시지가 성공적으로 전달된 경우 클라우드-디바이스 메시지가 부적절하게 처리하는 디바이스로부터 오류를 포착하지 않습니다.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Connections

연결 범주는 디바이스가 IoT Hub에 연결되거나 연결이 해제될 때 발생하는 오류를 추적합니다. 이 범주를 추적하는 것은 무단 연결 시도를 식별하고 연결 상태가 좋지 않은 영역에서 디바이스의 연결이 끊어졌을 때 추적하는 데 유용합니다.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>파일 업로드

파일 업로드 범주는 IoT Hub에서 발생하고 파일 업로드 기능과 관련된 오류를 추적합니다. 이 범주에는 다음이 포함됩니다.

* SAS URI에서 발생하는 오류(예: 디바이스가 허브에 완료된 업로드를 알리기 전에 만료되는 경우).

* 디바이스에 의해 보고된 실패한 업로드.

* IoT Hub 알림 메시지 생성 중 스토리지에서 파일을 찾을 수 없는 경우 발생하는 오류.

이 범주는 디바이스가 스토리지로 파일을 업로드하는 동안 직접 발생하는 오류를 검색할 수 없습니다.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>메시지 라우팅

메시지 라우팅 범주는 IoT Hub에서 감지하는 대로 메시지 경로 평가 및 엔드포인트 상태 중에 발생한 오류를 추적합니다. 이 범주에는 규칙이 "undefined"로 평가되는 경우, IoT Hub가 엔드포인트를 데드로 표시한 경우 및 다른 오류가 엔드포인트에서 수신되는 경우와 같은 이벤트가 포함됩니다. 이 범주는 메시지 자체에 대한 특정 오류(예: 디바이스 제한 오류)를 포함하지 않습니다. 해당 오류는 "디바이스 원격 분석" 범주 아래에서 보고됩니다.

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>모니터링 엔드포인트에 연결

IoT Hub의 모니터링 엔드포인트는 이벤트 허브와 호환되는 엔드포인트입니다. Event Hubs와 함께 작동하는 모든 메커니즘을 사용하여 이 엔드포인트에서 모니터링 메시지를 읽을 수 있습니다. 다음 샘플은 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. Event Hubs에서 메시지를 처리하는 방법에 대한 자세한 내용은 [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md) 자습서를 참조하세요.

모니터링 엔드포인트에 연결하려면 연결 문자열 및 엔드포인트 이름이 필요합니다. 다음 단계에서는 포털에서 필요한 값을 찾는 방법을 보여 줍니다.

1. 포털에서 IoT Hub 리소스 블레이드로 이동합니다.

2. **작업 모니터링**을 선택하고 **이벤트 허브 호환 이름** 및 **이벤트 허브 호환 엔드포인트** 값을 적어둡니다.

    ![이벤트 허브 호환 엔드포인트 값](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. **공유 액세스 정책**을 선택하고 **서비스**를 선택합니다. **기본 키** 값을 기록해 둡니다.

    ![서비스 공유 액세스 정책 기본 키](./media/iot-hub-operations-monitoring/service-key.png)

다음 C# 코드 샘플은 Visual Studio **Windows 클래식 데스크톱** C# 콘솔 앱에서 가져온 것입니다. 프로젝트에는 **WindowsAzure.ServiceBus** NuGet 패키지가 설치되어 있습니다.

* 다음 예제와 같이 연결 문자열 자리 표시자를 이전에 적어 둔 **이벤트 허브 호환 엔드포인트** 및 서비스 **기본 키** 값을 사용하는 연결 문자열로 바꿉니다.

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* 모니터링 엔드포인트 이름 자리 표시자를 이전에 적어 둔 **이벤트 허브 호환 이름** 값으로 바꿉니다.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();

        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }

        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }

            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));

            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)