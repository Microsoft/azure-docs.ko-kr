---
title: REST API-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid를 REST API 합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841832"
---
# <a name="rest-api"></a>REST API
이 문서에서는의 Azure Event Grid에 대 한 REST Api에 대해 설명 IoT Edge

## <a name="common-api-behavior"></a>일반적인 API 동작

### <a name="base-url"></a>기준 URL
IoT Edge Event Grid에는 HTTP (포트 5888) 및 HTTPS (포트 4438)를 통해 제공 되는 다음 Api가 있습니다.

* HTTP: http://eventgridmodule:5888 의 기준 URL
* HTTPS의 기준 URL: https://eventgridmodule:4438

### <a name="request-query-string"></a>요청 쿼리 문자열
모든 API 요청에는 다음 쿼리 문자열 매개 변수가 필요 합니다.

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>요청 내용 유형
모든 API 요청은 **콘텐츠 형식**이어야 합니다.

**Eventgridschema** 또는 **Customschema**의 경우 content-type 값은 다음 값 중 하나일 수 있습니다.

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

구조적 모드에서 **CloudEventSchemaV1_0** 하는 경우 content-type 값은 다음 값 중 하나일 수 있습니다.

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

이진 모드의 **CloudEventSchemaV1_0** 경우 자세한 내용은 [설명서](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) 를 참조 하세요.

### <a name="error-response"></a>오류 응답
모든 Api는 다음 페이로드를 사용 하 여 오류를 반환 합니다.

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>항목 관리

### <a name="put-topic-create--update"></a>항목 배치 (만들기/업데이트)

**요청**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**페이로드**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**응답**: HTTP 200

**페이로드**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>항목 가져오기

**요청**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**페이로드**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>모든 항목 가져오기

**요청**: ``` GET /topics?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**페이로드**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>항목 삭제

**요청**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200, 빈 페이로드

## <a name="manage-event-subscriptions"></a>이벤트 구독 관리
이 단원의 샘플에서는 `EndpointType=Webhook;`을 사용 합니다. `EndpointType=EdgeHub / EndpointType=EventGrid`에 대 한 json 샘플은 다음 섹션에 나와 있습니다. 

### <a name="put-event-subscription-create--update"></a>이벤트 구독 배치 (만들기/업데이트)

**요청**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**페이로드**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**응답**: HTTP 200

**페이로드**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>이벤트 구독 가져오기

**요청**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**페이로드**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>이벤트 구독 가져오기

**요청**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**응답**: HTTP 200

**페이로드**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>이벤트 구독 삭제

**요청**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**응답**: HTTP 200, 페이로드 없음


## <a name="publish-events-api"></a>이벤트 API 게시

### <a name="send-batch-of-events-in-event-grid-schema"></a>이벤트 일괄 처리 보내기 (Event Grid 스키마)

**요청**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**응답**: HTTP 200, 빈 페이로드


**페이로드 필드 설명**
- ```Id```은 필수입니다. 호출자가 채운 임의의 문자열 값일 수 있습니다. Event Grid은 중복 검색을 수행 하거나이 필드에 대 한 의미 체계를 적용 하지 않습니다.
- ```Topic```는 선택 사항 이지만 지정 된 경우 요청 URL의 topic_name와 일치 해야 합니다.
- ```Subject``` 필수 이며 임의의 문자열 값일 수 있습니다.
- ```EventType``` 필수 이며 임의의 문자열 값일 수 있습니다.
- ```EventTime``` 필수 이며 유효성을 검사 하지는 않지만 올바른 날짜/시간 이어야 합니다.
- ```DataVersion```는 필수입니다.
- ```MetadataVersion```은 선택적입니다. 지정 된 경우 값이 포함 된 문자열 이어야 합니다 ```"1"```
- ```Data```은 선택적 이며 모든 JSON 토큰 (number, string, boolean, array, object)이 될 수 있습니다.

### <a name="send-batch-of-events-in-custom-schema"></a>사용자 지정 스키마에서 이벤트 일괄 처리 보내기

**요청**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**응답**: HTTP 200, 빈 페이로드


**페이로드 제한**
- 는 이벤트의 배열 이어야 합니다.
- 각 배열 항목은 JSON 개체 여야 합니다.
- 다른 제약 조건 (페이로드 크기 제외)이 없습니다.

## <a name="examples"></a>예시

### <a name="set-up-topic-with-eventgrid-schema"></a>EventGrid 스키마를 사용 하 여 토픽 설정
이벤트를 **eventgridschema**에 게시 해야 하는 항목을 설정 합니다.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>사용자 지정 스키마를 사용 하 여 토픽 설정
`customschema`에서 이벤트를 게시 해야 하는 항목을 설정 합니다.

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>클라우드 이벤트 스키마를 사용 하 여 토픽 설정
`cloudeventschema`에서 이벤트를 게시 해야 하는 항목을 설정 합니다.

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>대상으로 WebHook 설정, eventgridschema에서 배달할 이벤트
이 대상 형식을 사용 하 여 HTTP 엔드포인트을 호스트 하는 다른 모듈이 나 네트워크/인터넷의 모든 HTTP 주소 지정 가능 엔드포인트으로 이벤트를 전송 합니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

`endpointUrl` 특성에 대 한 제약 조건:
- Null이 아니어야 합니다.
- 절대 URL 이어야 합니다.
- EventGridModule 설정에서 outbound__webhook__httpsOnly true로 설정 된 경우에는 HTTPS 여야 합니다.
- Outbound__webhook__httpsOnly false로 설정 하면 HTTP 또는 HTTPS가 될 수 있습니다.

`eventDeliverySchema` 속성에 대 한 제약 조건:
- 구독 항목의 입력 스키마와 일치 해야 합니다.
- Null 일 수 있습니다. 기본적으로 토픽의 입력 스키마로 설정 됩니다.

### <a name="set-up-iot-edge-as-destination"></a>IoT Edge를 대상으로 설정

이 대상을 사용 하 여 IoT Edge 허브에 이벤트를 보내고 Edge 허브의 라우팅/필터링/전달 하위 시스템에 연결할 수 있습니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Event Grid 클라우드를 대상으로 설정

이 대상을 사용 하 여 클라우드 (Azure)의 Event Grid에 이벤트를 전송 합니다. Edge에 이벤트 구독을 만들기 전에 먼저 이벤트를 보낼 클라우드에서 사용자 항목을 설정 해야 합니다.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Null이 아니어야 합니다.
- 절대 URL 이어야 합니다.
- 요청 URL 경로에 `/api/events` 경로를 정의 해야 합니다.
- 쿼리 문자열에 `api-version=2018-01-01` 있어야 합니다.
- EventGridModule 설정에서 outbound__eventgrid__httpsOnly true로 설정 되어 있으면 (기본적으로 true 임) HTTPS 여야 합니다.
- Outbound__eventgrid__httpsOnly false로 설정 된 경우 HTTP 또는 HTTPS가 될 수 있습니다.
- Outbound__eventgrid__allowInvalidHostnames false (기본값은 false)로 설정 된 경우 다음 엔드포인트 중 하나를 대상으로 해야 합니다.
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Null이 아니어야 합니다.

TopicName:
- EventDeliverySchema가 EventGridSchema로 설정 된 경우이 필드의 값은 클라우드의 Event Grid 전달 되기 전에 모든 이벤트의 토픽 필드에 배치 됩니다.
- EventDeliverySchema이 CustomEventSchema로 설정 된 경우이 속성은 무시 되 고 사용자 지정 이벤트 페이로드는 수신 된 그대로 전달 됩니다.

## <a name="set-up-event-hubs-as-a-destination"></a>Event Hubs를 대상으로 설정

이벤트 허브에 게시 하려면 `endpointType` `eventHub`으로 설정 하 고 다음을 제공 합니다.

* connectionString: 공유 액세스 정책을 통해 대상으로 생성 되는 특정 이벤트 허브에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 특정 엔터티 여야 합니다. 네임 스페이스 연결 문자열 사용은 작동 하지 않습니다. Azure Portal에서 게시 하려는 특정 이벤트 허브로 이동 하 고 **공유 액세스 정책** 을 클릭 하 여 새 엔터티 관련 connecection 문자열을 생성 하 여 엔터티 관련 연결 문자열을 생성할 수 있습니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Service Bus 큐를 대상으로 설정

Service Bus 큐에 게시 하려면 `endpointType`를 `serviceBusQueue`로 설정 하 고 다음을 제공 합니다.

* connectionString: 공유 액세스 정책을 통해 대상으로 생성 되는 특정 Service Bus 큐에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 특정 엔터티 여야 합니다. 네임 스페이스 연결 문자열 사용은 작동 하지 않습니다. Azure Portal에서 게시 하려는 특정 Service Bus 큐로 이동 하 고 **공유 액세스 정책** 을 클릭 하 여 새 엔터티 관련 connecection 문자열을 생성 하 여 엔터티 관련 연결 문자열을 생성 합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>대상으로 Service Bus 항목 설정

Service Bus 항목에 게시 하려면 `endpointType`를 `serviceBusTopic`로 설정 하 고 다음을 제공 합니다.

* connectionString: 공유 액세스 정책을 통해 생성 되는 대상으로 지정 된 특정 Service Bus 항목에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 연결 문자열은 특정 엔터티 여야 합니다. 네임 스페이스 연결 문자열 사용은 작동 하지 않습니다. Azure Portal에서 게시 하려는 특정 Service Bus 토픽으로 이동 하 여 엔터티 관련 연결 문자열을 생성 하 고 **공유 액세스 정책** 을 클릭 하 여 새 엔터티 관련 connecection 문자열을 생성 합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>저장소 큐를 대상으로 설정

저장소 큐에 게시 하려면 `endpointType` `storageQueue`으로 설정 하 고 다음을 제공 합니다.

* queueName: 게시 하는 저장소 큐의 이름입니다.
* connectionString: 저장소 큐가 있는 저장소 계정에 대 한 연결 문자열입니다.

    >[!NOTE]
    > 줄 Event Hubs, Service Bus 큐 및 Service Bus 항목은 저장소 큐에 사용 되는 연결 문자열은 엔터티만 관련 되지 않습니다. 대신 저장소 계정에 대 한 연결 문자열 이어야 합니다.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```