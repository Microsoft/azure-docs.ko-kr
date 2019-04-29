---
title: Azure Stream Analytics에 입력으로 데이터 스트리밍
description: Azure Stream Analytics에서 데이터 연결을 설정하는 방법을 알아보세요. 입력에는 이벤트의 데이터 스트림과 참조 데이터가 포함되어 있습니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 2a366a9030104c885adb1a4f773de04cdc439044
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61480496"
---
# <a name="stream-data-as-input-into-stream-analytics"></a>Stream Analytics에 입력으로 데이터 스트리밍

Stream Analytics는 세 종류 리소스의 입력으로 Azure 데이터 스트림과 최고급 통합을 수행합니다.
- [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 
- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) 

이러한 입력 리소스는 Stream Analytics 작업과 동일한 Azure 구독 또는 다른 구독에 존재할 수 있습니다.

### <a name="compression"></a>압축
Stream Analytics는 모든 데이터 스트림 입력 원본에서 압축을 지원합니다. 현재 지원되는 압축 형식은 다음과 같습니다. None, GZip 및 Deflate 압축입니다. 참조 데이터에는 압축이 지원되지 않습니다. 입력 형식이 압축된 Avro 데이터인 경우 투명하게 처리됩니다. Avro serialization에서는 압축 형식을 지정할 필요가 없습니다. 

## <a name="create-edit-or-test-inputs"></a>입력 만들기, 편집 또는 테스트
[Azure Portal](https://portal.azure.com)을 사용하여 [새 입력을 만들고](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal#configure-job-input) 스트리밍 작업에서 기존 입력을 보거나 편집할 수 있습니다. 입력 연결을 테스트하고 샘플 데이터에서 [쿼리를 테스트](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-manage-job#test-your-query)할 수도 있습니다. 쿼리를 작성할 때 FROM 절에 입력이 나열됩니다. 포털의 **쿼리** 페이지에서 사용 가능한 입력 목록을 가져올 수 있습니다. 여러 입력을 사용하려는 경우 `JOIN`하거나 여러 `SELECT` 쿼리를 작성할 수 있습니다.


## <a name="stream-data-from-event-hubs"></a>이벤트 허브에서 데이터 스트리밍

Azure Event Hubs는 확장성 있는 게시-구독 이벤트 수집기를 제공합니다. 이벤트 허브는 초당 수백만 개의 이벤트를 수집할 수 있으므로 연결된 디바이스와 애플리케이션이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. Event Hubs 및 Stream Analytics는 실시간 분석을 위한 종단간 솔루션을 함께 제공합니다. Event Hubs를 사용하면 이벤트를 실시간으로 Azure에 공급할 수 있으며 Stream Analytics 작업은 해당 이벤트를 실시간으로 처리할 수 있습니다. 예를 들어 Event Hubs에 웹 클릭, 센서 판독값 또는 온라인 로그 이벤트를 보낼 수 있습니다. 그런 다음 실시간 필터링, 집계 및 상관 관계에 대한 입력 데이터 스트림으로 Event Hubs를 사용하도록 Stream Analytics 작업을 만들 수 있습니다.

`EventEnqueuedUtcTime`은 이벤트 허브에서 이벤트 도착의 타임스탬프이며 Stream Analytics의 Event Hubs에서 오는 이벤트의 기본 타임스탬프입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

### <a name="consumer-groups"></a>소비자 그룹
각 Stream Analytics 이벤트 허브 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 셀프 조인이 포함되거나 입력이 여러 개인 경우 둘 이상의 판독기 다운스트림으로 일부 입력을 읽을 수 있습니다. 이러한 상황은 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 파티션당 소비자 그룹마다 5개 판독기의 Event Hubs 한도 초과를 방지하려면 각 Stream Analytics 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [Azure Stream Analytics 입력 문제 해결](stream-analytics-troubleshoot-input.md)을 참조하세요.

### <a name="stream-data-from-event-hubs"></a>이벤트 허브에서 데이터 스트리밍
다음 표는 이벤트 허브에서 데이터 입력을 스트리밍하는 Azure Portal의 **새 입력** 페이지의 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** |이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| **구독** | 이벤트 허브 리소스가 있는 구독을 선택합니다. | 
| **이벤트 허브 네임스페이스** | 이벤트 허브 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 네임스페이스도 만듭니다. |
| **이벤트 허브 이름** | 입력으로 사용할 이벤트 허브의 이름입니다. |
| **이벤트 허브 정책 이름** | 이벤트 허브에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. 이벤트 허브 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다.|
| **이벤트 허브 소비자 그룹**(권장) | 각 Stream Analytics 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다. 이 문자열은 이벤트 허브에서 데이터를 수집하는 데 사용할 소비자 그룹입니다. 소비자 그룹이 지정되지 않으면 Stream Analytics 작업에서 $Default 소비자 그룹을 사용합니다.  |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다.  JSON 형식이 사양을 준수하고 10진수 앞에 0이 없는지 확인하세요. |
| **Encoding** | 현재 유일하게 지원되는 인코딩 형식은 UTF-8입니다. |
| **이벤트 압축 유형** | 들어오는 데이터 스트림을 읽는 데 사용되는 압축 유형입니다(예: None(기본값), GZip 또는 Deflate). |

데이터가 이벤트 허브 스트림 입력에서 오는 경우 Stream Analytics 쿼리의 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **EventProcessedUtcTime** |이벤트가 Stream Analytics으로 처리되는 날짜 및 시간입니다. |
| **EventEnqueuedUtcTime** |Event Hubs에서 이벤트를 받은 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 이러한 필드를 사용하여 다음 예제와 같은 쿼리를 작성할 수 있습니다.

```sql
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
```

> [!NOTE]
> Event Hub를 IoT Hub Route의 엔드포인트로 사용하는 경우에는 [GetMetadataPropertyValue 함수](https://msdn.microsoft.com/library/azure/mt793845.aspx)를 사용하여 IoT Hub 메타데이터에 액세스할 수 있습니다.
> 

## <a name="stream-data-from-iot-hub"></a>IoT Hub에서 데이터 스트리밍
Azure Iot 허브는 IoT 시나리오에 최적화된, 확장성이 뛰어난 게시-구독 이벤트 처리기입니다.

Stream Analytics의 IoT Hub에서 오는 이벤트의 기본 타임스탬프는 이벤트가 IoT Hub에 도착한 타임스탬프이며, 이는 `EventEnqueuedUtcTime`입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

### <a name="consumer-groups"></a>소비자 그룹
각 Stream Analytics IoT Hub 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 셀프 조인이 포함되거나 입력이 여러 개인 경우 둘 이상의 판독기 다운스트림으로 일부 입력을 읽을 수 있습니다. 이러한 상황은 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 파티션당 소비자 그룹마다 5개 판독기의 Azure IoT Hub 한도 초과를 방지하려면 각 Stream Analytics 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>데이터 스트림 입력으로 IoT Hub 구성
다음 표에서는 스트림 입력으로 IoT Hub를 구성할 때 Azure Portal의 **새 입력** 페이지에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.|
| **구독** | IoT Hub 리소스가 있는 구독을 선택합니다. | 
| **IoT Hub** | 입력으로 사용할 IoT Hub의 이름입니다. |
| **엔드포인트** | IoT Hub에 대한 엔드포인트입니다.|
| **공유 액세스 정책 이름** | IoT Hub에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| **공유 액세스 정책 키** | IoT Hub에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다.  IoT Hub 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |
| **소비자 그룹** | 각 Stream Analytics 작업마다 서로 다른 소비자 그룹을 사용하는 것이 좋습니다. 소비자 그룹은 IoT Hub에서 데이터를 수집하는 데 사용됩니다. Stream Analytics에서는 달리 지정하지 않는 한 $Default 소비자 그룹을 사용합니다.  |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다.  JSON 형식이 사양을 준수하고 10진수 앞에 0이 없는지 확인하세요. |
| **Encoding** | 현재 유일하게 지원되는 인코딩 형식은 UTF-8입니다. |
| **이벤트 압축 유형** | 들어오는 데이터 스트림을 읽는 데 사용되는 압축 유형입니다(예: None(기본값), GZip 또는 Deflate). |


IoT Hub에서 스트림 데이터를 사용하는 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **EventProcessedUtcTime** | 이벤트가 처리되는 날짜 및 시간입니다. |
| **EventEnqueuedUtcTime** | IoT Hub에서 이벤트를 받은 날짜 및 시간입니다. |
| **PartitionId** | 입력 어댑터의 0부터 시작하는 파티션 ID입니다. |
| **IoTHub.MessageId** | IoT Hub에서 양방향 통신을 상호 연결하는 데 사용되는 ID입니다. |
| **IoTHub.CorrelationId** | IoT Hub에서 메시지 응답 및 피드백에 사용되는 ID입니다. |
| **IoTHub.ConnectionDeviceId** | 이 메시지를 보내는 데 사용된 인증 ID입니다. 이 값은 IoT Hub에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| **IoTHub.ConnectionDeviceGenerationId** | 이 메시지를 보내는 데 사용된 인증된 디바이스의 생성 ID입니다. 이 값은 IoT Hub에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| **IoTHub.EnqueuedTime** | IoT Hub에서 메시지가 수신된 시간입니다. |


## <a name="stream-data-from-blob-storage"></a>Blob Storage에서 데이터 스트리밍
클라우드에 저장할 구조화되지 않은 대량 데이터가 있는 시나리오에서 Azure Blob Storage는 비용 효과적이고 확장성 있는 솔루션을 제공합니다. Blob Storage의 데이터는 일반적으로 미사용 데이터로 간주되지만 Blob 데이터는 Stream Analytics로 데이터 스트림으로 처리될 수 있습니다. 

로그 처리는 Stream Analytics와 함께 Blob Storage 입력을 사용하기 위해 일반적으로 사용되는 시나리오입니다. 이 시나리오에서는 시스템에서 원격 분석 데이터 파일이 캡처되고 유의미한 데이터를 추출하기 위해 구문 분석 및 처리되어야 합니다.

Stream Analytics에서 Blob Storage 이벤트의 기본 타임 스탬프는 Blob이 마지막으로 수정된 타임스탬프로 `BlobLastModifiedUtcTime`입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다. Stream Analytics 작업은 Blob 파일을 사용할 수 있는 경우 1초 간격으로 Azure Blob Storage 입력에서 데이터를 가져옵니다. Blob 파일을 사용할 수 없는 경우 최대 시간 지연 시간 90초 동안 지수 백오프가 발생합니다.

CSV 형식의 입력은 데이터 집합용 필드를 정의하기 위해 헤더 행이 *필요*하며, 모든 헤드 행 필드는 고유해야 합니다.

Stream Analytics는 현재 이벤트 허브 캡처 또는 IoT Hub Azure Storage 컨테이너 사용자 지정 엔드포인트에 의해 생성된 AVRO 메시지의 비직렬화를 지원하지 않습니다.

> [!NOTE]
> Stream Analytics에서는 기존 blob 파일에 콘텐츠를 추가할 수 없습니다. Stream Analytics에서는 각 파일을 한 번만 보며 작업에서 데이터를 읽은 후 파일에서 발생한 모든 변경 내용은 처리되지 않습니다. Blob 파일에 대한 모든 데이터를 한 번에 업로드한 후 다른 새 Blob 파일에 최신 이벤트를 추가하는 것이 좋습니다.
> 

### <a name="configure-blob-storage-as-a-stream-input"></a>스트림 입력으로 Blob Storage 구성 

다음 표에서는 스트림 입력으로 Blob Storage를 구성할 때 Azure Portal의 **새 입력** 페이지에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| **구독** | IoT Hub 리소스가 있는 구독을 선택합니다. | 
| **Storage 계정** | Blob 파일이 위치한 저장소 계정의 이름입니다. |
| **Storage 계정 키** | 저장소 계정과 연결된 비밀 키입니다. Blob Storage 설정을 수동으로 제공하는 옵션을 선택하지 않으면 이 옵션이 자동으로 채워집니다. |
| **컨테이너** | Blob 입력에 대한 컨테이너입니다. 컨테이너는 Microsoft Azure Blob service에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Azure Blob Storage 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. **기존 컨테이너 사용** 또는 **새로 만들기**를 선택하여 새 컨테이너를 만들 수 있습니다.|
| **경로 패턴**(선택 사항) | 지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 파일 경로입니다. 경로 내에서 세 변수(`{date}`, `{time}`, `{partition}`)의 인스턴스 중 하나 이상을 지정할 수도 있습니다.<br/><br/>예 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>예 2: `cluster1/logs/{date}`<br/><br/>`*` 문자는 경로 접두사에 대해 허용된 값이 아닙니다. 유효한 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob 문자</a>만 허용됩니다. 컨테이너 이름 또는 파일 이름은 포함하지 않습니다. |
| **날짜 형식**(선택 사항) | 경로에서 날짜 변수를 사용하는 경우 파일이 구성된 날짜 형식입니다. 예제: `YYYY/MM/DD` |
| **시간 형식**(선택 사항) |  경로에서 시간 변수를 사용하는 경우 파일이 구성된 시간 형식입니다. 현재 지원되는 유일한 값은 몇 시간 동안 `HH`입니다. |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다.  JSON 형식이 사양을 준수하고 10진수 앞에 0이 없는지 확인하세요. |
| **Encoding** | CSV 및 JSON의 경우 UTF-8이 현재 지원되는 유일한 인코딩 형식입니다. |
| **압축** | 들어오는 데이터 스트림을 읽는 데 사용되는 압축 유형입니다(예: None(기본값), GZip 또는 Deflate). |

데이터를 Blob Storage 원본에서 가져온 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **BlobName** |이 이벤트가 발생한 입력 Blob의 이름입니다. |
| **EventProcessedUtcTime** |이벤트가 Stream Analytics으로 처리되는 날짜 및 시간입니다. |
| **BlobLastModifiedUtcTime** |Blob이 마지막으로 수정된 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 이러한 필드를 사용하여 다음 예제와 같은 쿼리를 작성할 수 있습니다.

```sql
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
