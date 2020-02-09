---
title: Azure Event Grid 이벤트 처리기
description: Azure Event Grid에 대해 지원 되는 이벤트 처리기에 대해 설명 합니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 3cef32cd204e8bd4b21353cf66575a721315b387
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511317"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid의 이벤트 처리기

이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. 여러 Azure 서비스는 이벤트를 처리하도록 자동으로 구성됩니다. 이벤트를 처리하기 위해 WebHook를 사용할 수도 있습니다. WebHook는 이벤트를 처리하기 위해 Azure에서 호스트될 필요가 없습니다. Event Grid는 HTTPS WebHook 엔드포인트만을 지원합니다.

이 문서에서는 각 이벤트 처리기에 대한 콘텐츠 링크를 제공합니다.

## <a name="azure-automation"></a>Azure Automation

자동화된 Runbook과 함께 Azure Automation을 사용하여 이벤트를 처리합니다.

|제목  |Description  |
|---------|---------|
|[자습서: Event Grid 및 Microsoft 팀을 사용한 Azure Automation](ensure-tags-exists-on-new-virtual-machines.md) |이벤트를 전송 하는 가상 머신을 만듭니다. 이벤트는 가상 머신에 태그를 지정하는 Automation Runbook을 트리거하고, Microsoft 팀 채널에 전송되는 메시지를 트리거합니다. |

## <a name="azure-functions"></a>Azure Function

이벤트에 대한 serverless 응답으로 Azure Functions를 사용합니다.

Azure Functions를 처리기로 사용할 때는 일반 HTTP 트리거 대신 Event Grid 트리거를 사용합니다. Event Grid는 Event Grid Function 트리거의 유효성을 자동으로 검사합니다. 일반 HTTP 트리거를 사용하면 [유효성 검사 응답](security-authentication.md#webhook-event-delivery)을 구현해야 합니다.

|제목  |Description  |
|---------|---------|
| [빠른 시작: 함수를 사용 하 여 이벤트 처리](custom-event-to-function.md) | 처리를 위해 사용자 지정 이벤트를 함수로 보냅니다. |
| [Azure Functions의 Event Grid 트리거](../azure-functions/functions-bindings-event-grid.md) | Functions에서 Event Grid 트리거 사용 개요. |
| [자습서: Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](resize-images-on-storage-blob-upload-event.md) | 사용자가 웹앱을 통해 이미지를 스토리지 계정에 업로드합니다. 스토리지 BLOB이 만들어지면 Event Grid는 이벤트를 함수 앱에 보내고, 그곳에서 업로드된 이미지를 크기 조정합니다. |
| [자습서: 데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md) | Event Hubs가 캡처 파일을 만들 때 Event Grid는 함수 앱에 이벤트를 보냅니다. 앱은 캡처 파일을 검색하고 데이터를 데이터 웨어하우스에 마이그레이션합니다. |
| [자습서: Azure Service Bus-Azure Event Grid 통합 예제](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱 및 논리 앱에 전송합니다. |

## <a name="event-hubs"></a>Event Hubs

솔루션이 이벤트를 처리할 수 있는 속도보다 더 빠르게 이벤트를 가져오는 경우 Event Hubs를 사용하세요. 애플리케이션이 자체 일정에 따라 Event Hubs의 이벤트를 처리합니다. 들어오는 이벤트를 처리하도록 이벤트 처리 규모를 확장할 수 있습니다.

Event Hubs는 이벤트 원본 또는 이벤트 처리기로 작동할 수 있습니다. 다음 아티클에서는 처리기로 Event Hubs를 사용하는 방법을 보여줍니다.

|제목  |Description  |
|---------|---------|
| [빠른 시작: Azure CLI 및 Event Grid를 사용하여 Azure Event Hubs로 사용자 지정 이벤트 라우팅](custom-event-to-eventhub.md) | 애플리케이션에서 처리하도록 사용자 지정 이벤트를 이벤트 허브로 보냅니다. |
| [Resource Manager 템플릿: 토픽 및 Event Hubs 엔드포인트 사용자 지정](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 사용자 지정 토픽에 대한 구독을 만드는 Resource Manager 템플릿입니다. Azure Event Hubs에 이벤트를 전송합니다. |

원본인 Event Hubs의 예제는 [Event Hubs 원본](event-sources.md#event-hubs)을 참조하세요.

## <a name="hybrid-connections"></a>하이브리드 연결

Azure Relay 하이브리드 연결을 사용하여 엔터프라이즈 네트워크 내에 있으며 공개적으로 액세스할 수 있는 엔드포인트가 없는 애플리케이션에 이벤트를 보냅니다.

|제목  |Description  |
|---------|---------|
| [자습서: 하이브리드 연결로 이벤트 전송](custom-event-to-hybrid-connection.md) | 수신기 애플리케이션에서 프로세싱에 대한 기존 하이브리드 연결에 사용자 지정 이벤트를 보냅니다. |

## <a name="logic-apps"></a>Logic Apps

Logic Apps를 사용하여 이벤트에 응답하는 비즈니스 프로세스를 자동화합니다.

|제목  |Description  |
|---------|---------|
| [자습서: Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 내용 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md) | 논리 앱은 가상 머신의 변경 내용을 모니터링하고 이러한 변경에 대한 이메일을 보냅니다. |
| [자습서: Logic Apps를 사용하여 Azure IoT Hub 이벤트에 대한 이메일 알림 보내기](publish-iot-hub-events-to-logic-apps.md) | 논리 앱은 사용자의 IoT Hub에 디바이스가 추가될 때마다 알림 이메일을 보냅니다. |
| [자습서: Azure Service Bus-Azure Event Grid 통합 예제](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱 및 논리 앱에 전송합니다. |

## <a name="service-bus"></a>Service Bus

### <a name="service-bus-queues"></a>Service Bus 큐

엔터프라이즈 응용 프로그램에서 버퍼링 또는 명령 & 제어 시나리오에서 사용 하기 위해 Event Grid의 이벤트를 Service Bus 큐에 직접 라우팅할 수 있습니다.

Azure Portal에서 이벤트 구독을 만드는 동안 엔드포인트 유형으로 "Service Bus 큐"를 선택한 다음 "엔드포인트 선택"을 클릭 하 여 Service Bus 큐를 선택 합니다.

#### <a name="using-cli-to-add-a-service-bus-queue-handler"></a>CLI를 사용 하 여 Service Bus 큐 처리기 추가

Azure CLI의 경우 다음 예제에서는 event grid 토픽을 구독 하 고 Service Bus 큐에 연결 합니다.

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

### <a name="service-bus-topics"></a>Service Bus 항목

Service Bus 항목을 사용 하 여 Azure 시스템 이벤트를 처리 하기 위해 Event Grid의 이벤트를 Service Bus 항목으로 직접 라우트 하거나 명령 & 메시징 시나리오를 제어할 수 있습니다.

Azure Portal에서 이벤트 구독을 만드는 동안 "Service Bus 토픽"을 엔드포인트 유형으로 선택 하 고 "선택 및 엔드포인트"을 클릭 하 여 Service Bus 항목을 선택 합니다.

#### <a name="using-cli-to-add-a-service-bus-topic-handler"></a>CLI를 사용 하 여 Service Bus 토픽 처리기 추가

Azure CLI의 경우 다음 예제에서는 event grid 토픽을 구독 하 고 Service Bus 큐에 연결 합니다.

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebustopic \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/topics/topic1
```

## <a name="queue-storage"></a>Queue Storage

Queue Storage를 사용하여 끌어와야 할 이벤트를 수신합니다. 응답 시간이 너무 오래 걸리는 장기 실행 프로세스가 있는 경우 Queue Storage를 사용할 수 있습니다. 이벤트를 Queue Storage를 보내면 앱이 자체 일정에 따라 프로세스를 끌어와서 처리할 수 있습니다.

|제목  |Description  |
|---------|---------|
| [빠른 시작: Azure CLI 및 Event Grid를 사용하여 Azure Queue Storage로 사용자 지정 이벤트 라우팅](custom-event-to-queue-storage.md) | Queue Storage에 사용자 지정 이벤트를 보내는 방법을 설명합니다. |

## <a name="webhooks"></a>웹후크

이벤트에 응답하는 사용자 지정 가능한 엔드포인트에는 웹후크를 사용합니다.

|제목  |Description  |
|---------|---------|
| 빠른 시작: [Azure CLI](custom-event-quickstart.md), [PowerShell](custom-event-quickstart-powershell.md) 및 [포털](custom-event-quickstart-portal.md)을 사용하여 사용자 지정 이벤트 만들기 및 라우팅 | 사용자 지정 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| 빠른 시작: [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json), [PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) 및 [포털](blob-event-quickstart-portal.md)을 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅 | Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: 컨테이너 레지스트리 이벤트 전송](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Container Registry 이벤트를 전송하는 방법을 보여줍니다. |
| [개요: HTTP 엔드포인트에 대한 이벤트 수신](receive-events.md) | 이벤트 구독으로부터 이벤트를 수신하기 위해 HTTP 엔드포인트의 유효성을 검사하고, 이벤트를 수신하고 역직렬화하는 방법을 설명합니다. |

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
