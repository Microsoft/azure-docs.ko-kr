---
title: Azure Event Grid의 시스템 항목
description: Azure Event Grid의 시스템 항목에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 08/27/2020
ms.openlocfilehash: f5ca472ab5141207222987d476284813c2aacf56
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019099"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid의 시스템 항목
Event Grid의 시스템 항목은 Azure Storage 및 Azure Event Hubs와 같은 Azure 서비스에서 게시 하는 하나 이상의 이벤트를 나타냅니다. 예를 들어 시스템 토픽은 **모든 blob 이벤트** 를 나타내거나 **특정 저장소 계정**에 대해 게시 된 **blob 및 blob** **삭제** 이벤트만 나타낼 수 있습니다. 이 예에서 blob을 저장소 계정에 업로드 하면 Azure Storage 서비스는 **blob created** 이벤트를 Event Grid의 system 토픽에 게시 합니다. 그러면 이벤트를 수신 하 고 처리 하는 토픽의 [구독자](event-handlers.md) 에 게 이벤트를 전달 합니다. 

> [!NOTE] 
> Azure 서비스만 시스템 항목에 이벤트를 게시할 수 있습니다. 따라서 사용자 지정 토픽 또는 도메인에 대해 수행 하는 것 처럼 이벤트를 게시 하는 데 사용할 수 있는 끝점 또는 액세스 키는 가져오지 않습니다.

## <a name="azure-services-that-support-system-topics"></a>시스템 항목을 지 원하는 Azure 서비스
다음은 시스템 항목 만들기를 지 원하는 Azure 서비스의 현재 목록입니다.

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure 리소스 그룹](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure 구독](event-schema-subscriptions.md)

## <a name="system-topics-as-azure-resources"></a>Azure 리소스로 서의 시스템 항목
이전에는 시스템 항목이 암시적이 고 간단 하 게 노출 되지 않았습니다. 이제 시스템 항목이 Azure 리소스로 표시 되 고 다음과 같은 기능을 제공 합니다.

- [Azure Portal에서 시스템 항목 보기](create-view-manage-system-topics.md#view-all-system-topics)
- Azure Portal에서 시스템 토픽 및 이벤트 구독에 대 한 리소스 관리자 템플릿을 내보냅니다.
- [시스템 항목에 대 한 진단 로그 설정](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- 게시 및 배달 오류에 대 한 경고 설정 

## <a name="lifecycle-of-system-topics"></a>시스템 항목의 수명 주기
다음과 같은 두 가지 방법으로 시스템 항목을 만들 수 있습니다. 

- [Azure 리소스에 대 한 이벤트 구독을 확장 리소스로](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)만듭니다. 그러면 이름이로 지정 된 시스템 항목이 자동으로 생성 `<Azure resource name>-<GUID>` 됩니다. 이 방법으로 만든 시스템 항목은 항목에 대 한 마지막 이벤트 구독을 삭제 하면 자동으로 삭제 됩니다. 
- Azure 리소스에 대 한 시스템 항목을 만든 다음 해당 시스템 항목에 대 한 이벤트 구독을 만듭니다. 이 방법을 사용 하는 경우 시스템 항목의 이름을 지정할 수 있습니다. 마지막 이벤트 구독이 삭제 되 면 시스템 항목이 자동으로 삭제 되지 않습니다. 수동으로 삭제 해야 합니다. 

    Azure Portal 사용 하는 경우 항상이 메서드를 사용 합니다. [Azure 리소스의 **이벤트** 페이지](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)를 사용 하 여 이벤트 구독을 만들 때 먼저 시스템 항목이 만들어지고 항목에 대 한 구독이 생성 됩니다. [ **Event Grid 시스템 항목** 페이지](create-view-manage-system-topics.md#create-a-system-topic) 를 사용 하 여 먼저 시스템 항목을 명시적으로 만든 다음 해당 항목에 대 한 구독을 만들 수 있습니다. 

[CLI](create-view-manage-system-topics-cli.md), [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)또는 [Azure Resource Manager 템플릿을](create-view-manage-system-topics-arm.md)사용 하는 경우 위의 방법 중 하나를 선택할 수 있습니다. 시스템 항목을 만드는 최신 방법 이므로 먼저 시스템 항목을 만든 다음 항목에서 구독을 만드는 것이 좋습니다.

Event Grid 서비스에서 만들 수 없는 방식으로 Azure 정책을 설정 하는 경우 시스템 항목 생성에 실패 합니다. 예를 들어 구독에 특정 유형의 리소스 (예: Azure Storage, Azure Event Hubs 등)만 만들 수 있는 정책이 있을 수 있습니다. 

## <a name="location-and-resource-group-for-a-system-topic"></a>시스템 항목에 대 한 위치 및 리소스 그룹
특정 지역/위치에 있는 Azure 이벤트 원본의 경우 system 토픽은 Azure 이벤트 원본과 동일한 위치에 만들어집니다. 예를 들어 미국 동부에서 Azure blob 저장소에 대 한 이벤트 구독을 만드는 경우 미국 동부에서 시스템 항목이 생성 됩니다. Azure 구독, 리소스 그룹, Azure Maps 등의 글로벌 Azure 이벤트 원본에 대 한 Event Grid는 **글로벌** 위치에 시스템 항목을 만듭니다. 

일반적으로 시스템 항목은 Azure 이벤트 원본이 있는 것과 동일한 리소스 그룹에 만들어집니다. Azure 구독 범위에서 만든 이벤트 구독의 경우 **미국 서 부 2** 지역의 **기본-eventgrid** 리소스 그룹에 시스템 항목이 생성 됩니다. 리소스 그룹이 없는 경우 시스템 항목을 만들기 전에 Azure Event Grid 만듭니다. 

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [Azure Portal를 사용 하 여 시스템 항목을 만들고 보고 관리](create-view-manage-system-topics.md)합니다.
- [Azure CLI를 사용 하 여 Event Grid 시스템 항목 만들기, 보기 및 관리](create-view-manage-system-topics-cli.md)
- [Azure Resource Manager 템플릿을 사용 하 여 Event Grid 시스템 항목 만들기](create-view-manage-system-topics-arm.md)
