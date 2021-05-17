---
title: Azure IoT Central에서 규칙에 대해 웹후크 만들기 | Microsoft Docs
description: 규칙이 실행되면 자동으로 다른 애플리케이션에 알리기 위해 Azure IoT Central에 웹 후크를 만듭니다.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87337231"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Azure IoT Central에서 규칙에 대해 웹후크 작업 만들기

‘이 항목의 내용은 빌더와 관리자에게 적용됩니다.’

웹후크를 사용하면 원격 모니터링 및 알림을 위해 IoT Central 앱을 다른 애플리케이션 및 서비스에 연결할 수 있습니다. 웹후크는 IoT Central 앱에서 규칙이 트리거될 때마다 연결하는 다른 애플리케이션 및 서비스에 자동으로 알립니다. IoT Central 앱은 규칙이 트리거될 때마다 다른 애플리케이션의 HTTP 엔드포인트에 POST 요청을 보냅니다. 페이로드는 디바이스 세부 정보 및 규칙 트리거 세부 정보를 포함합니다.

## <a name="set-up-the-webhook"></a>Webhook 설정

이 예제에서는 RequestBin에 연결하여 규칙 실행 시 webhook를 통해 알림이 표시되도록 합니다.

1. [RequestBin](https://requestbin.net/)을 엽니다.

1. 새 RequestBin을 만들고 **Bin URL** 을 복사합니다.

1. [원격 분석 규칙](tutorial-create-telemetry-rules.md)을 만듭니다. 규칙을 저장하고 새 작업을 추가합니다.

    ![웹후크 만들기 화면](media/howto-create-webhooks/webhookcreate.png)

1. 웹후크 작업을 선택하고 표시 이름을 입력한 후 Bin URL을 콜백 URL로서 붙여넣습니다.

1. 규칙을 저장합니다.

이제 규칙이 트리거되면 RequestBin에 새 요청이 표시됩니다.

## <a name="payload"></a>Payload

규칙이 트리거되면 원격 분석, 디바이스, 규칙 및 애플리케이션 세부 정보가 있는 json 페이로드를 포함하는 콜백 URL에 대해 HTTP POST 요청이 수행됩니다. 페이로드는 다음과 같아야 합니다.

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
규칙에서 일정 기간 동안 집계된 원격 분석을 모니터링하는 경우 페이로드에는 다른 원격 분석 섹션이 포함됩니다.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>데이터 형식 변경 알림

**2020년 4월 3일** 이전에 하나 이상의 webhook를 만들고 저장한 경우에는 webhook를 삭제하고 새 webhook를 만들어야 합니다. 이전 webhook는 앞으로 지원이 중단될 이전 페이로드 형식을 사용하기 때문입니다.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>webhook 페이로드(2020년 4월 3일부로 지원이 중단된 형식)

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>알려진 제한 사항

현재, API 통해 이러한 웹후크에서 구독/구독 취소하는 프로그래밍 방법은 없습니다.

이 기능을 개선하는 방법에 대한 아이디어가 있는 경우 [User voice 포럼](https://feedback.azure.com/forums/911455-azure-iot-central)에 게시해 주세요.

## <a name="next-steps"></a>다음 단계

webhook를 설정하고 사용하는 방법을 배웠으므로 이제 제안된 다음 단계는 [Azure Monitor 작업 그룹 구성 방법](howto-use-action-groups.md)을 살펴보는 것입니다.
