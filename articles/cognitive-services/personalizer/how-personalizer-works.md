---
title: Personalizer 작동 방식 - Personalizer
description: Personalizer _루프_ 는 기계 학습을 사용 하 여 콘텐츠에 대 한 상위 작업을 예측 하는 모델을 작성 합니다. 모델은 Rank 및 보상 호출을 통해 전송 된 데이터에 대해서만 학습 됩니다.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303610"
---
# <a name="how-personalizer-works"></a>Personalizer의 작동 원리

Personalizer 리소스 인 _학습 루프_ 는 기계 학습을 사용 하 여 콘텐츠에 대 한 상위 작업을 예측 하는 모델을 작성 합니다. 모델은 **Rank** 및 **보상** 호출을 통해 전송 된 데이터에 대해서만 학습 됩니다. 모든 루프는 서로 독립적입니다.

## <a name="rank-and-reward-apis-impact-the-model"></a>순위 및 보상 Api가 모델에 영향을 줍니다.

기능 및 _컨텍스트 기능이_ _포함 된 작업_ 을 Rank API로 보냅니다. **Rank** API는 다음 중 하나를 사용하도록 결정합니다.

* _Exploit_: 과거 데이터를 기반으로 최상의 작업을 결정 하는 현재 모델입니다.
* _탐색_: 위쪽 동작 대신 다른 작업을 선택 합니다. Azure Portal에서 Personalizer 리소스에 대해 [이 비율을 구성](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) 합니다.

보상 점수를 결정 하 고이 점수를 보상 API로 보냅니다. **Reward** API는 다음과 같습니다.

* 각 순위 호출의 기능 및 보상 점수를 기록하여 모델을 학습시키기 위한 데이터를 수집합니다.
* 는 해당 데이터를 사용 하 여 _학습 정책_ 에 지정 된 구성을 기반으로 모델을 업데이트 합니다.

## <a name="your-system-calling-personalizer"></a>Personalizer를 호출 하는 시스템

다음 이미지에서는 Rank 호출 및 Reward 호출의 아키텍처 흐름을 보여 줍니다.

![대체 텍스트](./media/how-personalizer-works/personalization-how-it-works.png "개인 설정의 작동 방식")

1. 기능 및 _컨텍스트 기능이_ _포함 된 작업_ 을 Rank API로 보냅니다.

    * Personalizer은 현재 모델을 사용할 것인지 또는 모델에 대 한 새 선택 항목을 탐색 하는지 결정 합니다.
    * 순위 결과가 EventHub로 전송됩니다.
1. 최고 순위는 _보상 동작 ID_ 로 시스템에 반환 됩니다.
    시스템에서 해당 콘텐츠를 표시하고 고유의 비즈니스 규칙에 따라 보상 점수를 결정합니다.
1. 시스템이 보상 점수를 학습 루프로 반환 합니다.
    * Personalizer에서 보상을 받으면 해당 보상을 EventHub에 보냅니다.
    * 순위와 보상이 상호 관련됩니다.
    * AI 모델이 상관 관계 결과에 따라 업데이트됩니다.
    * 유추 엔진이 새 모델로 업데이트됩니다.

## <a name="personalizer-retrains-your-model"></a>Personalizer 다시 모델

Personalizer 다시 Azure Portal의 Personalizer 리소스에 대 한 **모델 빈도 업데이트** 설정에 따라 모델을 합니다.

Personalizer는 Azure Portal의 Personalizer 리소스에 대 한 **데이터 보존** 기간 (일 수)을 기준으로 현재 보존 된 모든 데이터를 사용 합니다.

## <a name="research-behind-personalizer"></a>Personalizer에 대한 연구

Personalizer는 Microsoft Research의 백서, 연구 활동 및 진행 중인 탐구 영역을 포함하여 [보충 학습](concepts-reinforcement-learning.md) 분야의 최첨단 과학 및 연구를 기반으로 합니다.

## <a name="next-steps"></a>다음 단계

Personalizer의 [주요 시나리오](where-can-you-use-personalizer.md) 에 대 한 자세한 정보