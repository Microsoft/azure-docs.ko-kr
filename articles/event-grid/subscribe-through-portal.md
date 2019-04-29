---
title: 포털을 통해 Azure Event Grid 구독
description: 포털을 통해 Event Grid 구독을 만드는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: spelluru
ms.openlocfilehash: b54bc52a2feaf4646d801265ddb273c2c86158ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823516"
---
# <a name="subscribe-to-events-through-portal"></a>포털을 통해 이벤트 구독

이 문서에서는 포털을 통해 Event Grid 구독을 만드는 방법을 설명합니다.

## <a name="create-event-subscriptions"></a>이벤트 구독 만들기

지원되는 [이벤트 원본](event-sources.md)에 대한 Event Grid 구독을 만들려면 다음 단계를 따르세요. 이 문서에서는 Azure 구독에 대한 Event Grid 구독을 만드는 방법을 설명합니다.

1. **모든 서비스**를 선택합니다.

   ![모든 서비스 선택](./media/subscribe-through-portal/select-all-services.png)

1. **Event Grid 구독**을 검색하고 사용 가능한 옵션에서 선택합니다.

   ![검색](./media/subscribe-through-portal/search.png)

1. **+ 이벤트 구독**을 선택합니다.

   ![구독 추가](./media/subscribe-through-portal/add-subscription.png)

1. 만들려는 구독 유형을 선택합니다. 예를 들어 Azure 구독에 대한 이벤트를 구독하려면 **Azure 구독** 및 대상 구독을 선택합니다.

   ![Azure 구독 선택](./media/subscribe-through-portal/azure-subscription.png)

1. 이 이벤트 원본에 대한 모든 이벤트 유형을 구독하려면 **모든 이벤트 유형 구독** 옵션을 선택한 상태로 둡니다. 그렇지 않을 경우 이 구독의 이벤트 유형을 선택합니다.

   ![이벤트 유형 선택](./media/subscribe-through-portal/select-event-types.png)

1. 이벤트 및 구독 이름을 처리하는 엔드포인트 같은 이벤트 구독에 대 한 추가 정보를 제공합니다.

   ![구독 세부 정보 제공](./media/subscribe-through-portal/provide-subscription-details.png)

1. 배달 못한 편지를 사용하도록 설정하고 재시도 정책을 사용자 지정하려면 **추가 기능**을 선택합니다.

   ![추가 기능 선택](./media/subscribe-through-portal/select-additional-features.png)

1. 제공되지 않은 이벤트를 저장하는 데 사용할 컨테이너를 선택하고 재시도를 전송하는 방법을 설정합니다.

   ![배달 못한 편지 및 재시도 사용](./media/subscribe-through-portal/set-deadletter-retry.png)

1. 완료되면 **만들기**를 선택합니다.

## <a name="create-subscription-on-resource"></a>리소스에 대한 구독 만들기

일부 이벤트 원본은 해당 리소스의 포털 인터페이스를 통해 이벤트 구독을 생성하도록 지원합니다. 이벤트 원본을 선택하고 왼쪽 창에서 **이벤트**를 찾습니다.

![구독 세부 정보 제공](./media/subscribe-through-portal/resource-events.png)

포털은 해당 원본과 관련된 이벤트 구독을 만드는 방법에 대한 옵션을 제공합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 및 다시 시도에 대한 자세한 내용은 [Event Grid 메시지 배달 및 다시 시도](delivery-and-retry.md)를 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
