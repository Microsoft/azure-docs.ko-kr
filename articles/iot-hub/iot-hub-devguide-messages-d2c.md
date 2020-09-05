---
title: Azure IoT Hub 메시지 라우팅 이해하기 | Microsoft Docs
description: 개발자 가이드 - 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 보내는 방법입니다. 원격 분석 데이터 및 비원격 분석 데이터를 전송하는 방법에 대한 정보가 포함됩니다.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: a451e13b39aea27b4f1e23f9faa30f4b11c1cff1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021241"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub 메시지 라우팅을 사용 하 여 다른 끝점으로 장치-클라우드 메시지 보내기

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

메시지 라우팅을 사용하면 확장 가능하고 신뢰할 수 있는 자동화된 방식으로 디바이스에서 클라우드 서비스로 메시지를 보낼 수 있습니다. 메시지 라우팅은 다음 용도로 사용할 수 있습니다. 

* **디바이스 원격 분석 메시지는 물론 이벤트** 즉, 디바이스 수명 주기 이벤트 및 디바이스 쌍 변경 이벤트를 기본 제공 엔드포인트 및 사용자 지정 엔드포인트로 보냅니다. [라우팅 끝점](#routing-endpoints)에 대해 알아봅니다.

* 다양한 쿼리를 적용하여 **다양한 엔드포인트로 데이터를 라우팅하기 전에 데이터를 필터링**합니다. 메시지 라우팅을 사용하면 메시지 속성 및 메시지 본문뿐만 아니라 디바이스 쌍 태그 및 디바이스 쌍 속성에 대해서도 쿼리할 수 있습니다. [메시지 라우팅에서 쿼리를](iot-hub-devguide-routing-query-syntax.md)사용 하는 방법에 대해 자세히 알아보세요.

IoT Hub는 메시지 라우팅을 작동하기 위해 이러한 서비스 엔드포인트에 대한 쓰기 액세스가 필요합니다. Azure Portal을 통해 엔드포인트를 구성하는 경우 필요한 권한이 추가됩니다. 예상된 처리량을 지원하도록 서비스를 구성해야 합니다. 예를 들어 Event Hubs를 사용자 지정 끝점으로 사용 하는 경우 IoT Hub 메시지 라우팅을 통해 전송 하려는 이벤트의 수신을 처리할 수 있도록 해당 이벤트 허브에 대 한 **처리량 단위** 를 구성 해야 합니다. 마찬가지로 Service Bus 큐를 끝점으로 사용 하는 경우에는 소비자가 egressed 때까지 큐가 모든 데이터 수신를 보유할 수 있도록 **최대 크기** 를 구성 해야 합니다. IoT 솔루션을 처음 구성하는 경우 추가 엔드포인트를 모니터링하고 실제 부하에 맞게 조정해야 할 수 있습니다.

IoT Hub는 프로토콜 전체에서의 상호 운용성을 위해 모든 디바이스-클라우드 메시징에 대해 [일반적인 형식](iot-hub-devguide-messages-construct.md)을 정의합니다. 메시지가 동일한 엔드포인트를 가리키는 여러 경로와 일치하는 경우 IoT Hub는 메시지를 해당 엔드포인트에 한 번만 전달합니다. 따라서 Service Bus 큐 또는 항목에 대해 중복 제거를 구성할 필요가 없습니다. 분할된 큐에서 파티션 선호도는 메시지 순서를 보장합니다. [메시지 라우팅을 구성](tutorial-routing.md)하는 방법을 알아보려면 이 자습서를 사용하세요.

## <a name="routing-endpoints"></a>라우팅 엔드포인트

IoT Hub에는 Event Hubs와 호환되는 기본 제공 엔드포인트(**메시지/이벤트**)가 있습니다. 구독의 다른 서비스를 IoT Hub에 연결하여 메시지를 라우팅할 [사용자 지정 엔드포인트](iot-hub-devguide-endpoints.md#custom-endpoints)를 만들 수 있습니다. 

각 메시지는 일치 하는 라우팅 쿼리가 있는 모든 끝점으로 라우팅됩니다. 즉, 메시지를 여러 끝점으로 라우팅할 수 있습니다.

사용자 지정 끝점에 방화벽 구성이 있는 경우 Microsoft trusted first 파티 예외를 사용 하 여 IoT Hub 특정 끝점 [Azure Storage](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing), [Azure Event Hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) 및 [Azure Service Bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing)에 대 한 액세스 권한을 부여 하는 것이 좋습니다. [관리 서비스 id](./virtual-network-support.md)를 사용 하 여 IoT hub의 선택 지역에서 사용할 수 있습니다.

IoT Hub 현재 다음 끝점을 지원 합니다.

 - 기본 제공 엔드포인트
 - Azure Storage
 - Service Bus 큐 및 Service Bus 토픽
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>기본 제공 끝점 (라우팅 끝점)

표준 [Event Hubs 통합 및 SDK](iot-hub-devguide-messages-read-builtin.md)를 사용하여 기본 제공 엔드포인트(**메시지/이벤트**)에서 디바이스-클라우드 메시지를 수신할 수 있습니다. 경로를 만든 후에는 해당 끝점에 대 한 경로를 만들지 않는 한 데이터는 기본 끝점으로의 이동이 중지 됩니다.

## <a name="azure-storage-as-a-routing-endpoint"></a>라우팅 끝점으로 Azure Storage

메시지를 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 및 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) 계정으로 라우팅할 수 IoT Hub 두 개의 저장소 서비스가 있습니다. Azure Data Lake Storage 계정은 blob storage를 기반으로 구축 된 [계층적 네임 스페이스](../storage/blobs/data-lake-storage-namespace.md)사용 저장소 계정입니다. 둘 다 저장소에 blob을 사용 합니다.

IoT Hub에서는 데이터를 JSON 형식 뿐만 아니라 [Apache Avro](https://avro.apache.org/) 형식으로 Azure Storage에 쓸 수 있습니다. 기본값은 AVRO입니다. JSON 인코딩을 사용 하는 경우 메시지 [시스템 속성](iot-hub-devguide-routing-query-syntax.md#system-properties)에서 contentType을 **application/JSON** 으로, Contentencoding을 **u t f-8** 로 설정 해야 합니다. 이러한 두 값은 대/소문자를 구분 하지 않습니다. 콘텐츠 인코딩이 설정 되지 않은 경우 IoT Hub는 메시지를 base 64 인코딩 형식으로 씁니다.

인코딩 형식은 blob storage 끝점이 구성 된 경우에만 설정할 수 있습니다. 기존 끝점에 대해서는 편집할 수 없습니다. 기존 끝점에 대 한 인코딩 형식을 전환 하려면 원하는 형식으로 사용자 지정 끝점을 삭제 하 고 다시 만들어야 합니다. 유용한 전략 중 하나는 원하는 인코딩 형식을 사용 하 여 새 사용자 지정 끝점을 만들고이 끝점에 병렬 경로를 추가 하는 것입니다. 이러한 방식으로 기존 끝점을 삭제 하기 전에 데이터를 확인할 수 있습니다.

IoT Hub 만들거나 업데이트 REST API, 특히 [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), Azure Portal, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)또는 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint)를 사용 하 여 인코딩 형식을 선택할 수 있습니다. 다음 이미지는 Azure Portal에서 인코딩 형식을 선택 하는 방법을 보여 줍니다.

![Blob storage 끝점 인코딩](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

일괄 처리가 특정 크기에 도달 하거나 특정 시간이 경과할 때마다 메시지를 일괄 처리 하 고 저장소에 데이터를 기록 IoT Hub 합니다. IoT Hub는 기본적으로 다음 파일 명명 규칙을 따릅니다.

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

원하는 파일 명명 규칙을 사용할 수 있지만, 나열된 토큰은 모두 사용해야 합니다. 쓸 데이터가 없으면 IoT Hub가 빈 Blob을 작성합니다.

Blob 또는 파일을 나열 하 고이를 반복 하 여 파티션을 가정 하지 않고 모든 blob 또는 파일을 읽을 수 있도록 하는 것이 좋습니다. 파티션 범위는 [Microsoft 시작 장애 조치(failover)](iot-hub-ha-dr.md#microsoft-initiated-failover) 또는 IoT Hub [수동 장애 조치(failover)](iot-hub-ha-dr.md#manual-failover) 중에 변경할 수 있습니다. [목록 BLOB api](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 를 사용 하 여 파일 목록에 대 한 Blob 또는 [목록 ADLS Gen2 api](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) 목록을 열거할 수 있습니다. 지침으로 다음 샘플을 참조 하세요.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Azure Data Lake Gen2 호환 저장소 계정을 만들려면 새 V2 저장소 계정을 만들고 다음 이미지와 같이 **고급** 탭의 *계층 구조 네임 스페이스* 필드에서 *사용* 을 선택 합니다.

![Azure Date Lake Gen2 storage를 선택 합니다.](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>큐 및 Service Bus 항목을 라우팅 끝점으로 Service Bus

IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색**이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 엔드포인트가 **연결할 수 없음**으로 표시됩니다.

## <a name="event-hubs-as-a-routing-endpoint"></a>라우팅 끝점으로 Event Hubs

기본 제공 Event Hubs 호환 엔드포인트 외에, Event Hubs 유형의 사용자 지정 엔드포인트로 데이터를 라우팅할 수도 있습니다. 

## <a name="reading-data-that-has-been-routed"></a>라우팅된 데이터 읽기

이 [자습서](tutorial-routing.md)에 따라 경로를 구성할 수 있습니다.

엔드포인트에서 메시지를 읽는 방법을 알아보려면 다음 자습서를 사용하세요.

* [기본 제공 엔드포인트](quickstart-send-telemetry-node.md)에서 읽기

* [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)에서 읽기

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) 에서 읽기

* [Service Bus 큐](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) 에서 읽기

* [Service Bus 토픽](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)에서 읽기


## <a name="fallback-route"></a>대체(fallback) 경로

대체(fallback) 경로는 기존 경로에서 쿼리 조건을 충족하지 않는 모든 메시지를 [Event Hubs](/azure/event-hubs/)와 호환되는 기본 제공 Event Hubs(**messages/events**)로 보냅니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 경로를 만든 후에는 해당 끝점에 대 한 경로를 만들지 않는 한 데이터를 기본 끝점으로 이동 하지 않습니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로를 사용할 수 있는 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정해야 합니다.

Azure Portal >메시지 라우팅 블레이드에서 대체 경로를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties)에 Azure Resource Manager를 사용하여 대체(fallback) 경로에 대해 사용자 지정 엔드포인트를 사용할 수도 있습니다.

## <a name="non-telemetry-events"></a>비-원격 분석 이벤트

장치 원격 분석 외에도 메시지 라우팅을 통해 장치 쌍 변경 이벤트, 장치 수명 주기 이벤트 및 디지털 쌍 변경 이벤트 (공개 미리 보기)를 보낼 수 있습니다. 예를 들어, **디바이스 쌍 변경 이벤트**로 설정된 데이터 원본으로 경로가 생성되면, IoT Hub는 디바이스 쌍의 변경 사항을 포함하는 엔드포인트로 메시지를 전송합니다. 마찬가지로 **장치 수명 주기 이벤트**로 설정 된 데이터 원본을 사용 하 여 경로를 만들 경우 IoT Hub는 장치를 삭제 하거나 만들었는지 여부를 나타내는 메시지를 보냅니다. 마지막으로, 개발자는 [IoT 플러그 앤 플레이 공개 미리 보기](../iot-pnp/overview-iot-plug-and-play.md)의 일부로 디지털 쌍 **변경 IoT Hub 이벤트** 로 설정된 데이터 원본이 있는 경로를 만들고 디지털 쌍 [속성](../iot-pnp/iot-plug-and-play-glossary.md)이 설정 되거나 변경 될 때마다 디지털 쌍으로 메시지를 보낼 수 있습니다. [digital twin](../iot-pnp/iot-plug-and-play-glossary.md)이 대체 되거나 기본 장치 쌍에 대해 변경 이벤트가 발생하는 경우

또한 IoT Hub는 이러한 이벤트를 기반으로 워크플로의 실시간 통합 및 자동화를 지 원하는 장치 이벤트를 게시 하 [는 Azure Event Grid와 통합](iot-hub-event-grid.md) 됩니다. [메시지 라우팅과 Event Grid 간의 주요 차이점](iot-hub-event-grid-routing-comparison.md)을 확인하고 내 시나리오에 무엇이 가장 적합한지 알아보세요.

## <a name="testing-routes"></a>경로 테스트

새 경로를 만들거나 기존 경로를 편집하는 경우에는 샘플 메시지로 경로 쿼리를 테스트해야 합니다. 개별 경로를 테스트하거나 모든 경로를 한 번에 테스트할 수 있으며 테스트 중에는 엔드포인트에 메시지가 라우팅되지 않습니다. Azure Portal, Azure Resource Manager, Azure PowerShell 및 Azure CLI 테스트에 사용할 수 있습니다. 결과는 샘플 메시지와 쿼리 일치 여부를 식별 하는 데 도움이 됩니다. 즉, 예제 메시지 또는 쿼리 구문이 잘못 되었으므로 테스트를 실행할 수 없습니다. 자세히 알아보려면 [경로 테스트](/rest/api/iothub/iothubresource/testroute) 및 [모든 경로 테스트](/rest/api/iothub/iothubresource/testallroutes)를 참조하세요.

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>하나 이상의 배달에 대 한 순서 보장

IoT Hub 메시지 라우팅은 순서 대로 메시지를 끝점으로 배달 하 고 한 번 이상 메시지를 배달 합니다. 즉, 중복 된 메시지가 있을 수 있으며 메시지 일련의 메시지를 다시 전송 하 여 원래 메시지 순서를 지정할 수 있습니다. 예를 들어 원래 메시지 순서가 [1, 2, 3, 4] 인 경우 [1, 2, 1, 2, 3, 1, 2, 3, 4]와 같은 메시지 시퀀스를 받을 수 있습니다. 순서를 지정 하는 것은 메시지 [1]을 받는 경우 항상 [2, 3, 4]가 뒤에 오는 것입니다.

메시지 중복을 처리 하려면 일반적으로 장치 또는 모듈인 원본 지점에 있는 메시지의 응용 프로그램 속성에서 고유한 식별자를 스탬프 하는 것이 좋습니다. 메시지를 사용 하는 서비스는이 식별자를 사용 하 여 중복 메시지를 처리할 수 있습니다.

## <a name="latency"></a>대기 시간

기본 제공 엔드포인트를 사용하여 디바이스-클라우드 간 원격 분석 메시지를 라우팅할 경우 첫 번째 경로를 만든 후 엔드투엔드 대기 시간이 약간 증가합니다.

대부분의 경우 평균 대기 시간 증가는 500 밀리초 미만입니다. **라우팅: 메시지/이벤트에 대한 메시지 대기 시간** 또는 **d2c.endpoints.latency.builtIn.events** IoT Hub 메트릭을 사용하여 대기 시간을 모니터링할 수 있습니다. 첫 번째 경로 이후에 다른 경로를 만들거나 삭제해도 엔드투엔드 대기 시간에는 영향을 주지 않습니다.

## <a name="monitoring-and-troubleshooting"></a>모니터링 및 문제 해결

IoT Hub는 허브 및 전송 메시지의 상태에 대 한 개요를 제공 하기 위해 라우팅 및 끝점과 관련 된 몇 가지 메트릭을 제공 합니다. [IoT Hub 메트릭](iot-hub-metrics.md)에는 기본적으로 IoT Hub에 대해 활성화된 모든 메트릭이 나열됩니다. Azure Monitor [진단 설정](../iot-hub/iot-hub-monitor-resource-health.md)의 **경로** 진단 로그를 사용 하 여 라우팅 쿼리 및 끝점 상태를 평가 하는 동안 발생 하는 오류를 IoT Hub에 의해 인식 되는 것으로 추적할 수 있습니다. REST API [Endpoint Health 가져오기](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 사용 하 여 Endpoint의 [Health 상태](iot-hub-devguide-endpoints.md#custom-endpoints) 를 가져올 수 있습니다. 

라우팅 문제 해결에 대 한 자세한 내용과 지원 정보는 라우팅 [에 대 한 문제 해결 가이드](troubleshoot-message-routing.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 메시지 경로를 만드는 방법에 대한 자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md)를 참조하세요.

* [디바이스-클라우드 메시지를 보내는 방법](quickstart-send-telemetry-node.md)

* 디바이스-클라우드 메시지를 보내는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.
