---
title: Azure Event Grid 개념
description: Azure Event Grid 및 해당 개념을 설명합니다. Event Grid의 몇 가지 주요 구성 요소를 정의합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 1c77d0ea9e67c8d69f3f632cace164d8a0c4d921
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562358"
---
# <a name="concepts-in-azure-event-grid"></a>Azure Event Grid의 개념

이 문서에서는 Azure Event Grid의 주요 개념을 설명합니다.

## <a name="events"></a>이벤트

이벤트는 시스템에서 발생하는 무언가를 완벽히 설명하는 가장 작은 크기의 정보입니다. 모든 이벤트에는 이벤트의 원본, 이벤트가 발생한 시간 및 고유 식별자와 같은 일반적인 정보가 있습니다. 또한 모든 이벤트에는 특정 유형의 이벤트에만 관련된 특정 정보도 있습니다. 예를 들어 Azure Storage에서 만드는 새 파일에 대한 이벤트에는 `lastTimeModified` 값과 같은 파일에 대한 세부 정보가 포함되어 있습니다. 또는 Event Hubs 이벤트는 캡처 파일의 URL을 갖습니다. 

각 이벤트는 64KB의 데이터로 제한됩니다.

이벤트에 포함되어 전송되는 속성은 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

## <a name="publishers"></a>게시자

게시자는 Event Grid에 이벤트를 보내기로 결정한 사용자나 조직입니다. Microsoft는 여러 가지 Azure 서비스에 대한 이벤트를 게시합니다. 사용자의 자체 애플리케이션에서 이벤트를 게시할 수 있습니다. Azure 외부 서비스를 호스트하는 조직은 Event Grid를 통해 이벤트를 게시할 수 있습니다.

## <a name="event-sources"></a>이벤트 원본

이벤트 원본은 이벤트가 발생하는 위치입니다. 각 이벤트 원본은 하나 이상의 이벤트 유형과 관련이 있습니다. 예를 들어 Azure Storage는 이벤트가 생성된 Blob에 대한 이벤트 원본입니다. IoT Hub는 디바이스에서 만든 이벤트에 대한 이벤트 원본입니다. 애플리케이션은 사용자가 정의한 사용자 지정 이벤트에 대한 이벤트 원본입니다. 이벤트 원본은 Event Grid에 이벤트를 보내는 역할을 담당합니다.

지원되는 Event Grid 원본을 구현하는 방법에 대한 내용은 [Azure Event Grid의 이벤트 원본](event-sources.md)을 참조하세요.

## <a name="topics"></a>토픽

Event Grid 항목에는 원본이 이벤트를 보내는 엔드포인트가 제공됩니다. 게시자는 Event Grid 항목을 만들고 이벤트 원본에 항목이 하나 필요한지 또는 둘 이상 필요한지 여부를 결정합니다. 항목은 관련 이벤트의 컬렉션에 사용됩니다. 특정 이벤트 형식에 응답하려면 구독자가 구독할 토픽을 결정합니다.

시스템 토픽은 Azure 서비스에서 제공하는 기본 제공 항목입니다. 게시자가 항목을 소유하기 때문에 Azure 구독에는 시스템 항목이 표시되지 않지만 항목을 구독할 수 있습니다. 구독하려면 이벤트로부터 받으려는 리소스에 대한 정보를 제공합니다. 리소스에 액세스할 수 있는,으로 해당 이벤트를 구독할 수 있습니다.

사용자 지정 토픽은 애플리케이션 및 타사 토픽입니다. 사용자 지정 항목을 만들거나 사용자 지정 항목에 대한 액세스가 할당되면 구독에 사용자 지정 항목이 표시됩니다.

애플리케이션을 디자인할 때는 만들려는 토픽 수를 유연하게 결정할 수 있습니다. 대규모 솔루션의 경우 관련된 이벤트의 각 범주에 대한 사용자 지정 토픽을 만듭니다. 사용자 계정을 수정하고 주문을 처리하는 것과 관련된 이벤트를 보내는 애플리케이션을 예로 들 수 있습니다. 이벤트 처리기가 이벤트의 두 범주 모두를 원할 가능성은 낮습니다. 두 개의 사용자 지정 토픽을 만들고 이벤트 처리기가 관심 있는 토픽에 구독할 수 있도록 합니다. 소규모 솔루션의 경우 모든 이벤트를 단일 토픽으로 전송할 수도 있습니다. 원하는 이벤트 유형에 대한 이벤트 구독자를 필터링할 수 있습니다.

## <a name="event-subscriptions"></a>이벤트 구독

구독은 사용자가 항목의 어떤 이벤트를 받고 싶어하는지를 Event Grid에 알려줍니다. 구독을 만들 때 이벤트 처리를 위한 엔드포인트를 제공합니다. 엔드포인트에 전송되는 이벤트를 필터링할 수 있습니다. 이벤트 형식 또는 제목 패턴으로 필터링할 수 있습니다. 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.

구독을 만드는 예는 다음을 참조하세요.

* [Event Grid에 대한 Azure CLI 샘플](cli-samples.md)
* [Event Grid에 대한 Azure PowerShell 샘플](powershell-samples.md)
* [Event Grid를 위한 Azure Resource Manager 템플릿](template-samples.md)

현재 Event Grid 구독을 확보하는 방법에 대한 정보는 [쿼리 Event Grid 구독](query-event-subscriptions.md)을 참조하세요.

## <a name="event-subscription-expiration"></a>이벤트 구독 만료

Azure CLI의 [Event Grid 확장](/cli/azure/azure-cli-extensions-list)을 사용하면 이벤트 구독을 만들 때 만료 날짜를 설정할 수 있습니다. REST API를 사용 중인 경우 `api-version=2018-09-15-preview`를 사용합니다.

해당 날짜 이후 이벤트 구독이 자동으로 만료됩니다. 제한된 시간 동안만 필요한 이벤트 구독 만료를 설정하고 이러한 구독을 정리하는 것에 대해 신경 쓰고 싶지 않습니다. 예를 들어, 시나리오를 테스트하기 위해 이벤트 구독을 만들 때 만료를 설정할 수 있습니다. 

만료를 설정하는 예제는 [고급 필터가 포함된 구독](how-to-filter-events.md#subscribe-with-advanced-filters)을 참조하세요.

## <a name="event-handlers"></a>이벤트 처리기

Event Grid 측면에서 볼 때 이벤트 처리기는 이벤트가 전송된 위치입니다. 처리기는 이벤트를 처리하기 위한 추가 작업을 수행합니다. Event Grid는 여러 가지 처리기 유형을 지원합니다. 지원되는 Azure 서비스를 사용하거나 자체 웹후크를 처리기로 사용할 수 있습니다. 처리기의 형식에 따라 Event Grid는 이벤트의 배달을 보장하는 다양한 메커니즘을 따릅니다. HTTP 웹후크 이벤트 처리기의 경우 처리기가 `200 – OK`의 상태 코드를 반환할 때까지 이벤트를 다시 시도합니다. Azure Storage Queue의 경우 큐 서비스가 성공적으로 큐에 메시지 푸시를 처리할 때까지 이벤트를 다시 시도합니다.

지원되는 Event Grid 처리기를 구현하는 방법에 대한 내용은 [Azure Event Grid의 이벤트 처리기](event-handlers.md)를 참조하세요.

## <a name="security"></a>보안

Event Grid는 토픽 구독 및 게시에 대한 보안을 제공합니다. 구독할 때 리소스 또는 Event Grid 토픽에 대해 적절한 사용 권한이 있어야 합니다. 게시할 때 토픽에 대한 SAS 토큰 또는 키 인증이 있어야 합니다. 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.

## <a name="event-delivery"></a>이벤트 전달

Event Grid에서 이벤트가 구독자의 엔드포인트에서 수신되었는지 확인할 수 없는 경우 이벤트를 다시 배달합니다. 자세한 내용은 [Event Grid 메시지 배달 및 재시도](delivery-and-retry.md)를 참조하세요.

## <a name="batching"></a>일괄 처리

사용자 지정 토픽을 사용하는 경우 이벤트를 항상 배열에 게시해야 합니다. 처리량이 적은 시나리오를 위한 일괄 처리로 사용할 수 있지만, 볼륨이 큰 사용 사례인 경우 효율을 높일 수 있도록 게시마다 여러 이벤트를 일괄 처리하는 것이 좋습니다. 일괄 처리의 최대 크기는 1MB입니다. 각 이벤트도 64KB보다 크면 안 됩니다.

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
