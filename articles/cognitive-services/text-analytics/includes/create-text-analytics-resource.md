---
title: Cognitive Services Text Analytics 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics 리소스를 만드는 방법에 대해 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383429"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics 리소스 만들기

1. [Azure portal](https://portal.azure.com)에 로그인합니다.
1. **리소스 만들기**를 선택 하 고 **AI + Machine Learning** > **Text Analytics**로 이동 합니다.
   또는 [Create Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동 합니다.
1. 필요한 설정을 모두 입력 합니다.

    |설정|Value|
    |--|--|
    |name|이름 (2-64 자)을 입력 합니다.|
    |Subscription|적절 한 구독을 선택 합니다.|
    |위치|주변 위치를 선택 합니다.|
    |가격 책정 계층| 표준 가격 책정 계층 인 **S**를 입력 합니다.|
    |Resource group|사용 가능한 리소스 그룹을 선택 합니다.|

1. **만들기**를 선택 하 고 리소스가 생성 될 때까지 기다립니다. 브라우저가 새로 만든 리소스 페이지로 자동으로 리디렉션됩니다.
1. 구성 된 `endpoint` 및 API 키를 수집 합니다.

    |포털의 리소스 탭|설정|Value|
    |--|--|--|
    |**개요**|엔드포인트|엔드포인트을 복사 합니다. `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`와 유사 하 게 표시 됩니다.|
    |**키**|API 키|두 키 중 하나를 복사 합니다. <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>를 사용 하 여 공백이 나 대시가 없는 32 자리의 영숫자 문자열입니다.|
