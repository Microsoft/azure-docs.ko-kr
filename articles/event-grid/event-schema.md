---
title: Azure Event Grid 이벤트 스키마
description: Azure Event Grid를 사용하여 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: b67d656ed6ab537a01696ec9c0c98f84b880f03b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561565"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid 이벤트 스키마

이 문서에서는 모든 이벤트에 존재하는 속성과 스키마를 설명합니다. 이벤트는 5개의 필수 문자열 속성 집합과 필수 데이터 개체로 구성됩니다. 속성은 모든 게시자에서 모든 이벤트에 공통입니다. 데이터 개체에는 각 게시자에만 적용되는 속성이 있습니다. 시스템 항목에 대한 이러한 속성은 Azure Storage 또는 Event Hubs와 같은 리소스 공급자에 따라 다릅니다.

이벤트 원본은 여러 이벤트 개체를 포함할 수 있는 배열의 Azure Event Grid에 이벤트를 전송합니다. 이벤트를 Event Grid 항목에 게시할 때 배열은 최대 1MB의 전체 크기를 가질 수 있습니다. 배열의 각 이벤트는 64KB로 제한됩니다. 이벤트 또는 배열이 크기 제한보다 클 경우 응답 **413 페이로드가 너무 큼**을 받습니다.

Event Grid는 단일 이벤트가 있는 배열의 구독자에게 이벤트를 보냅니다. 이 동작은 나중에 변경할 수 있습니다.

[이벤트 스키마 저장소](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)에서 Event Grid 이벤트에 대한 JSON 스키마 및 각 Azure 게시자의 데이터 페이로드를 찾을 수 있습니다.

## <a name="event-schema"></a>이벤트 스키마

다음 예제는 모든 이벤트 게시자가 사용하는 속성을 보여줍니다.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

예를 들어 Azure Blob Storage 이벤트용으로 게시된 스키마는 다음과 같습니다.

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>이벤트 속성

모든 이벤트에는 다음과 같은 동일한 최상위 수준 데이터가 있습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| 제목 | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | 특정 리소스 공급자에 대한 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성에 대해 자세히 알아보려면 이벤트 원본을 참조하십시오.

* [Azure 구독(관리 작업)](event-schema-subscriptions.md)
* [컨테이너 레지스트리](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [리소스 그룹(관리 작업)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)

사용자 지정 항목의 경우 이벤트 게시자가 데이터 개체를 결정합니다. 최상위 수준 데이터에는 표준 리소스 정의 이벤트와 동일한 필드가 있어야 합니다.

사용자 지정 항목에 이벤트를 게시할 때 구독자가 이벤트에 관심이 있는지 더 쉽게 알 수 있도록 사용자 이벤트에 대한 제목을 만듭니다. 구독자는 제목을 사용하여 이벤트를 필터링 및 라우팅합니다. 구독자가 해당 경로의 세그먼트를 기준으로 필터링할 수 있도록 이벤트가 발생하는 경로를 제공하는 것을 고려합니다. 구독자는 경로를 통해 이벤트를 제한적이거나 광범위하게 필터링할 수 있습니다. 예를 들어 제목에 `/A/B/C`와 같은 3개의 세그먼트 경로를 제공하는 경우 구독자는 첫 번째 세그먼트 `/A`를 기준으로 필터링하여 광범위한 이벤트 집합을 가져올 수 있습니다. 구독자는 `/A/B/C` 또는 `/A/D/E`와 같은 제목이 있는 이벤트를 가져옵니다. 다른 구독자는 `/A/B`를 기준으로 필터링하여 제한된 이벤트 집합을 얻을 수 있습니다.

경우에 따라 제목에 무엇에 관해서인지 자세한 내용이 포함되어야 합니다. 예를 들어 컨테이너에 파일을 추가할 때 **저장소 계정** 게시자가 제목 `/blobServices/default/containers/<container-name>/blobs/<file>`을 제공하는 경우, 구독자는 경로 `/blobServices/default/containers/testcontainer`를 기준으로 필터링하여 저장소 계정에 다른 컨테이너가 아닌 해당 컨테이너에 대한 모든 이벤트를 가져올 수 있습니다. 또한 구독자는 접미사 `.txt`를 기준으로 필터링 또는 라우팅하여 텍스트 파일로만 작업할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
