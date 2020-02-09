---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963661"
---
성능을 최대화 하려면 각 함수 앱에 대해 별도의 저장소 계정을 사용 합니다. 이는 특히 많은 양의 저장소 트랜잭션을 생성 하는 Durable Functions 또는 이벤트 허브 트리거 함수가 있는 경우에 특히 중요 합니다. 응용 프로그램 논리가 직접 (저장소 SDK를 사용 하 여) 또는 저장소 바인딩 중 하나를 통해 Azure Storage와 상호 작용 하는 경우 전용 저장소 계정을 사용 해야 합니다. 예를 들어 blob 저장소에 일부 데이터를 기록 하는 이벤트 허브 트리거 함수가 있는 경우 두 개의 저장소 계정을 사용 합니다. 하나는 함수 앱에 대한 것이 고 다른 하나는 함수에 의해 저장 되는 blob에 대한&mdash;합니다.