---
title: 사용자 발화 검토 - LUIS
description: 활성 학습을 사용하여 올바른 의도 및 엔터티에 대한 엔드포인트 발언을 검토하세요. LUIS는 알 수 없는 엔드포인트 발언을 선택합니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316446"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>엔드포인트 발언을 검토하여 활성 학습을 사용하도록 설정하는 데 관한 개념입니다.
활성 학습은 예측 정확도를 향상시키는 세 가지 전략 중 하나이며 구현하기에 가장 쉽습니다. 활성 학습을 사용하여 올바른 의도 및 엔터티에 대한 엔드포인트 발언을 검토하세요. LUIS는 알 수 없는 엔드포인트 발언을 선택합니다.

## <a name="what-is-active-learning"></a>활성 학습의 개념
활성 학습은 2단계 프로세스입니다. 먼저 LUIS는 유효성 검사가 필요한 앱의 엔드포인트에서 수신하는 발화를 선택합니다. 두 번째 단계는 앱 소유자 또는 협력자가 올바른 의도 및 의도 내의 모든 엔터티를 포함하여 [검토](luis-how-to-review-endpoint-utterances.md)를 위해 선택한 발화의 유효성을 검사하는 것입니다. 발화를 검토한 후 다시 앱을 학습시키고 게시합니다.

## <a name="which-utterances-are-on-the-review-list"></a>검토 목록에 있는 발화
상위 실행 의도의 점수가 낮거나 상위 두 개 의도의 점수가 너무 근접하면 LUIS는 검토 목록에 발화를 추가합니다.

## <a name="single-pool-for-utterances-per-app"></a>앱당 발언을 위한 단일 풀
**엔드포인트 발언 검토** 목록은 버전에 따라 달라지지 않습니다. 적극적으로 편집 중인 발언의 버전이나 엔드포인트에서 게시된 앱 버전에 관계없이 검토할 단일 발언 풀이 있습니다.

[REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)에서 버전 이름은 필요하며 애플리케이션에 있어야 하지만 해당 유효성 검사 이후에 사용되지 않습니다. 검토 발화는 애플리케이션 전체에 적용됩니다. 한 _버전_ 에서 발화를 제거하면 모든 버전에 영향을 미칩니다.

## <a name="where-are-the-utterances-from"></a>발화를 가져오는 위치
엔드포인트 발화는 애플리케이션 HTTP 엔드포인트의 최종 사용자 쿼리에서 가져옵니다. 앱이 게시되지 않았거나 아직 적중을 받지 않은 경우에는 검토할 발화가 없습니다. 특정 의도 또는 엔터티에 대한 엔드포인트 적중이 수신되지 않은 경우에는 검토할 해당 적중이 포함된 발화가 없습니다.

## <a name="schedule-review-periodically"></a>주기적으로 검토 예약
제안된 발화를 매일 검토할 필요는 없지만 정기적인 LUIS 유지 관리에 포함해야 합니다.

## <a name="delete-review-items-programmatically"></a>프로그래밍 방식으로 검토 항목 삭제
**[레이블이 지정되지 않은 발화 삭제](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API를 사용합니다. 삭제하기 전에 **[로그 파일을 내보내](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** 이러한 발언을 백업합니다.

## <a name="enable-active-learning"></a>활성 학습 사용

활성 학습을 사용하도록 설정하려면 사용자 쿼리를 기록해야 합니다. 이렇게 하려면 `log=true` 쿼리 문자열 매개 변수 및 값을 사용하여 [엔드포인트 쿼리](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint)를 호출합니다.

## <a name="next-steps"></a>다음 단계

* 엔드포인트 발화를 [검토](luis-how-to-review-endpoint-utterances.md)하는 방법 알아보기
