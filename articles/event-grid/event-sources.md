---
title: Azure Event Grid 이벤트 원본
description: Azure Event Grid에 대한 지원되는 이벤트 원본을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: b6abb0048dcc722241a2cd87354224b0fd7fa2f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561548"
---
# <a name="event-sources-in-azure-event-grid"></a>Azure Event Grid의 이벤트 원본

이벤트 원본은 이벤트가 발생하는 위치입니다. 여러 Azure 서비스는 이벤트를 전송하도록 자동으로 구성됩니다. 또한 이벤트를 전송하는 사용자 지정 애플리케이션을 만들 수도 있습니다. 사용자 지정 애플리케이션은 이벤트 배포에서 Event Grid를 사용하기 위해 Azure에서 호스트될 필요가 없습니다.

이 문서에서는 각 이벤트 원본에 대한 콘텐츠 링크를 제공합니다.

## <a name="azure-subscriptions"></a>Azure 구독

Azure 구독 이벤트를 구독하여 Azure 구독 전반에서 리소스의 변경 내용에 응답합니다.

|제목 |설명  |
|---------|---------|
| [자습서: Event Grid 및 Microsoft Teams를 사용한 Azure Automation](ensure-tags-exists-on-new-virtual-machines.md) |이벤트를 전송 하는 가상 머신을 만듭니다. 이벤트는 가상 머신에 태그를 지정하는 Automation Runbook을 트리거하고, Microsoft 팀 채널에 전송되는 메시지를 트리거합니다. |
| [방법: 포털을 통해 이벤트 구독](subscribe-through-portal.md) | 포털을 사용하여 Azure 구독에 대한 이벤트를 구독합니다. |
| [Azure CLI: Azure 구독에 대한 이벤트 구독](./scripts/event-grid-cli-azure-subscription.md) |Azure 구독에 Event Grid 구독을 만들고 WebHook에 이벤트를 전송하는 샘플 스크립트입니다. |
| [PowerShell: Azure 구독에 대한 이벤트 구독](./scripts/event-grid-powershell-azure-subscription.md)| Azure 구독에 Event Grid 구독을 만들고 WebHook에 이벤트를 전송하는 샘플 스크립트입니다. |
| [이벤트 스키마](event-schema-subscriptions.md) | Azure 구독 이벤트의 필드를 보여 줍니다. |

## <a name="container-registry"></a>Container Registry

Container Registry 이벤트를 구독하여 이미지에서 변경 내용에 응답합니다.

|제목 |설명  |
|---------|---------|
| [빠른 시작: 컨테이너 레지스트리 이벤트 전송](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Container Registry 이벤트를 전송하는 방법을 보여줍니다. |
| [이벤트 스키마](event-schema-container-registry.md) | Container Registry 이벤트에서 필드를 보여줍니다. |

## <a name="custom-topics"></a>사용자 지정 항목

사용자 지정 토픽을 구독하여 애플리케이션 이벤트에 응답합니다.

|제목  |설명  |
|---------|---------|
| [빠른 시작: Azure CLI를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md) | Azure CLI를 사용하여 사용자 지정 이벤트를 전송하는 방법을 보여 줍니다. |
| [빠른 시작: Azure PowerShell을 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart-powershell.md) | Azure PowerShell을 사용하여 사용자 지정 이벤트를 전송하는 방법을 보여 줍니다. |
| [빠른 시작: Azure Portal을 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart-portal.md) | 포털을 사용하여 사용자 지정 이벤트를 전송하는 방법을 보여 줍니다. |
| [빠른 시작: Azure Queue Storage로 사용자 지정 이벤트 라우팅](custom-event-to-queue-storage.md) | Queue Storage에 사용자 지정 이벤트를 보내는 방법을 설명합니다. |
| [방법: 사용자 지정 토픽에 게시](post-to-custom-topic.md) | 사용자 지정 토픽에 이벤트를 게시하는 방법을 설명합니다. |
| [Azure CLI: Event Grid 사용자 지정 토픽 만들기](./scripts/event-grid-cli-create-custom-topic.md)|사용자 지정 토픽을 만드는 샘플 스크립트입니다. 스크립트는 엔드포인트 및 키를 검색합니다.|
| [Azure CLI: 사용자 지정 토픽에 대한 이벤트 구독](./scripts/event-grid-cli-subscribe-custom-topic.md)|사용자 지정 토픽에 대한 구독을 만드는 샘플 스크립트입니다. WebHook에 이벤트를 보냅니다.|
| [PowerShell: Event Grid 사용자 지정 토픽 만들기](./scripts/event-grid-powershell-create-custom-topic.md)|사용자 지정 토픽을 만드는 샘플 스크립트입니다. 스크립트는 엔드포인트 및 키를 검색합니다.|
| [PowerShell: 사용자 지정 토픽에 대한 이벤트 구독](./scripts/event-grid-powershell-subscribe-custom-topic.md)|사용자 지정 토픽에 대한 구독을 만드는 샘플 스크립트입니다. WebHook에 이벤트를 보냅니다.|
| [Resource Manager 템플릿: 토픽 및 WebHook 엔드포인트 사용자 지정](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | 사용자 지정 토픽 및 해당 사용자 지정 토픽에 대한 구독을 만드는 Resource Manager 템플릿입니다. WebHook에 이벤트를 보냅니다. |
|
| [Resource Manager 템플릿: 토픽 및 Event Hubs 엔드포인트 사용자 지정](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 사용자 지정 토픽에 대한 구독을 만드는 Resource Manager 템플릿입니다. Azure Event Hubs에 이벤트를 전송합니다. |
| [이벤트 스키마](event-schema.md) | 사용자 지정 이벤트의 필드를 보여 줍니다. |

## <a name="event-hubs"></a>Event Hubs

Event Hubs 이벤트를 구독하여 캡처 파일 이벤트에 응답합니다. Event Hubs는 이벤트 원본 또는 이벤트 처리기로 작동할 수 있습니다. 다음 아티클에서는 원본으로 Event Hubs를 사용하는 방법을 보여줍니다.

|제목  |설명  |
|---------|---------|
| [자습서: 데이터 웨어하우스로 빅 데이터 스트림](event-grid-event-hubs-integration.md) | Event Hubs가 캡처 파일을 만들 때 Event Grid는 함수 앱에 이벤트를 보냅니다. 앱은 캡처 파일을 검색하고 데이터를 데이터 웨어하우스에 마이그레이션합니다. |
| [이벤트 스키마](event-schema-event-hubs.md) | Event Hubs 이벤트의 필드를 보여 줍니다. |

처리기인 Event Hubs의 예제는 [Event Hubs 처리기](event-handlers.md#event-hubs)를 참조하세요.

## <a name="iot-hub"></a>IoT Hub

IoT Hub 이벤트를 구독하여 디바이스 생성, 삭제, 연결 및 연결 해제 이벤트에 응답합니다.

|제목  |설명  |
|---------|---------|
| [Logic Apps를 사용하여 Azure IoT Hub 이벤트에 관한 이메일 알림 보내기](publish-iot-hub-events-to-logic-apps.md) | 논리 앱은 사용자의 IoT Hub에 디바이스가 추가될 때마다 알림 이메일을 보냅니다. |
| [작업을 트리거하기 위해 Event Grid를 사용하여 IoT Hub 이벤트에 대응](../iot-hub/iot-hub-event-grid.md) | Event Grid와 IoT Hub 통합의 개요입니다. |
| [이벤트 스키마](event-schema-iot-hub.md) | IoT Hub 이벤트의 필드를 보여 줍니다. |
| [디바이스 연결 및 디바이스 연결 해제 이벤트 정렬](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | 디바이스 연결 상태 이벤트 정렬하는 방법을 보여 줍니다. |

## <a name="media-services"></a>Media Services

Media Services 이벤트를 구독하여 작업 상태 이벤트에 응답합니다.

|제목  |설명  |
|---------|---------|
| [개요: Media Services 이벤트에 대응](../media-services/latest/reacting-to-media-services-events.md) | Event Grid와 Media Services 통합의 개요입니다. |
| [자습서: CLI를 사용하여 Azure Media Services 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services에서 이벤트를 전송하는 방법을 보여 줍니다. |
| [이벤트 스키마](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services 이벤트의 필드를 표시합니다. |

## <a name="resource-groups"></a>리소스 그룹

리소스 그룹 이벤트를 구독하여 리소스 그룹 전반에서 리소스의 변경 내용에 응답합니다.

|제목  |설명  |
|---------|---------|
| [자습서: Azure Event Grid 및 Logic Apps를 사용하여 가상 머신 변경 내용 모니터링](monitor-virtual-machine-changes-event-grid-logic-app.md) | 논리 앱은 가상 머신의 변경 내용을 모니터링하고 이러한 변경에 대한 이메일을 보냅니다. |
| [Azure CLI: 리소스 그룹에 대한 이벤트 구독](./scripts/event-grid-cli-resource-group.md)| 리소스 그룹에 대한 이벤트를 구독하는 샘플 스크립트입니다. WebHook에 이벤트를 보냅니다. |
| [Azure CLI: 리소스 그룹에 대한 이벤트 구독 및 리소스에 대한 필터링](./scripts/event-grid-cli-resource-group-filter.md) | 리소스 그룹에 대한 이벤트를 구독하고 하나의 리소스에 대한 이벤트를 필터링하는 샘플 스크립트입니다. |
| [PowerShell: 리소스 그룹에 대한 이벤트 구독](./scripts/event-grid-powershell-resource-group.md) | 리소스 그룹에 대한 이벤트를 구독하는 샘플 스크립트입니다. WebHook에 이벤트를 보냅니다. |
| [PowerShell: 리소스 그룹에 대한 이벤트 구독 및 리소스에 대한 필터링](./scripts/event-grid-powershell-resource-group-filter.md) | 리소스 그룹에 대한 이벤트를 구독하고 하나의 리소스에 대한 이벤트를 필터링하는 샘플 스크립트입니다. |
| [Resource Manager 템플릿: 리소스 구독](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Azure 구독 또는 리소스 그룹에 대한 이벤트를 구독합니다. WebHook에 이벤트를 보냅니다. |
| [이벤트 스키마](event-schema-resource-groups.md) | 리소스 그룹 이벤트의 필드를 표시합니다. |

## <a name="service-bus"></a>Service Bus

Service Bus 이벤트를 구독하여 활성 수신기 없이 메시지에 응답합니다.

|제목  |설명  |
|---------|---------|
| [자습서: Azure Service Bus-Azure Event Grid 통합 예제](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid는 Service Bus 토픽의 메시지를 함수 앱 및 논리 앱에 전송합니다. |
| [개요: Azure Service Bus와 Event Grid 통합](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Event Grid와 Service Bus 통합의 개요입니다. |
| [이벤트 스키마](event-schema-service-bus.md) | Service Bus 이벤트의 필드를 표시합니다. |

## <a name="storage"></a>Storage

Blob Storage 이벤트를 구독하여 만든 blob 및 삭제된 이벤트에 대해 응답합니다.

|제목  |설명  |
|---------|---------|
| [빠른 시작: Azure CLI를 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI를 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: PowerShell을 사용하여 Blob Storage 이벤트를 사용자 지정 웹 엔드포인트로 라우팅](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure PowerShell을 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [빠른 시작: Azure Portal을 사용하여 Blob Storage 이벤트 만들기 및 라우팅](blob-event-quickstart-portal.md) | 포털를 사용하여 Blob Storage 이벤트를 WebHook로 전송하는 방법을 보여줍니다. |
| [Azure CLI: Blob Storage 계정에 대한 이벤트 구독](./scripts/event-grid-cli-blob.md) | Blob Storage 계정에 대한 이벤트를 구독하는 샘플 스크립트입니다. 이벤트를 WebHook로 전송합니다. |
| [PowerShell: Blob Storage 계정에 대한 이벤트 구독](./scripts/event-grid-powershell-blob.md) | Blob Storage 계정에 대한 이벤트를 구독하는 샘플 스크립트입니다. 이벤트를 WebHook로 전송합니다. |
| [Resource Manager 템플릿: Blob 스토리지 및 구독 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Azure Blob Storage 계정을 배포하고 해당 스토리지 계정에 대한 이벤트를 구독합니다. WebHook에 이벤트를 보냅니다. |
| [개요: Blob Storage 이벤트에 대응](../storage/blobs/storage-blob-event-overview.md) | Event Grid와 Blob Storage 통합의 개요입니다. |
| [이벤트 스키마](event-schema-blob-storage.md) | Blob Storage 이벤트의 필드를 표시합니다. |

## <a name="maps"></a>지도
Azure Maps 이벤트를 구독하여 지오펜스 이벤트에 응답합니다. 예를 들어, 애플리케이션은 디바이스가 지오펜스에 들어오거나 나갈 때마다 이메일 알림을 제공할 수 있습니다.

|제목  |설명  |
|---------|---------|
| [Event Grid를 사용하여 Azure Maps 이벤트에 대응](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid와 Azure Maps 통합에 관한 개요입니다. |
| [자습서: 지오펜스 설정](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 이 자습서에서는 Azure Maps를 사용하여 지오펜스를 설정하는 기본 단계를 안내합니다. Azure Event Grid를 사용하여 지오펜스 결과를 스트림하고, 지오펜스 결과를 기반으로 하는 알림을 설정합니다. |
| [이벤트 스키마](event-schema-azure-maps.md) | Azure Maps 이벤트의 필드를 표시합니다. |


## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
