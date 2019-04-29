---
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598466"
---
보다 정확한 소비 비용을 계산하려면 폴링 간격만을 기반으로 계산을 하기 보다는 특정 일에 수신할 수 있는 메시지나 이벤트 수를 고려하는 것이 좋습니다. 이벤트나 메시지가 트리거 조건을 충족하면 많은 트리거가 조건을 충족하는 대기 중인 모든 이벤트나 메시지를 즉시 읽으려고 시도합니다. 이러한 동작은 폴링 간격을 길게 선택하더라도 워크플로를 시작하기에 적합한 대기 중인 이벤트나 메시지 수를 기반으로 트리거가 실행된다는 것을 의미합니다. 이러한 동작을 따르는 트리거에는 Azure Service Bus 및 Azure Event Hub가 있습니다.

예를 들어, 매일 엔드포인트를 확인하는 트리거를 설정한다고 가정하겠습니다. 트리거가 엔드포인트를 확인하여 조건을 충족하는 이벤트를 15개 찾으면, 트리거가 실행되고 해당 워크플로가 15번 실행됩니다. Logic Apps는 트리거 요청을 포함하여 15개 워크플로에서 수행하는 모든 작업을 측정합니다. 잠재적인 비용을 계산하려면 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용해보세요.