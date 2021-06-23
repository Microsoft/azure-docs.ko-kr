---
title: 앱 학습 - LUIS
titleSuffix: Azure Cognitive Services
description: 학습은 해당 자연어 이해를 향상시키기 위해 LUIS(Language Understanding) 앱 버전을 학습하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 0ff8376eea2eca459b93adbf46b81dbeb7639d40
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111954341"
---
# <a name="train-your-active-version-of-the-luis-app"></a>LUIS 앱의 활성 버전 학습

학습은 해당 자연어 이해를 개선하기 위해 LUIS(Language Understanding) 앱을 교육하는 과정입니다. 엔터티, 의도 또는 발언을 추가하거나, 편집하거나, 레이블 지정하거나 삭제하는 등 모델에 대한 업데이트 후에 LUIS 앱을 학습합니다.

앱 학습 및 [테스트](./luis-interactive-test.md)는 반복적인 프로세스입니다. LUIS 앱을 학습한 후에 샘플 음성으로 앱을 테스트하여 의도 및 엔티티가 올바르게 인식되는지 확인합니다. 인식되지 않으면 LUIS 앱을 업데이트하고 학습하고, 다시 테스트합니다.

학습은 LUIS 포털에서 현재 사용 중인 버전에 적용됩니다.

## <a name="how-to-train-interactively"></a>대화형으로 학습하는 방법

[LUIS 포털](https://www.luis.ai)에서 반복적인 프로세스를 시작하려면 먼저 적어도 한 번 LUIS 앱을 학습해야 합니다. 학습하기 전에 모든 의도에 하나 이상의 발언이 있는지 확인합니다.

1. **내 앱** 페이지에서 해당 이름을 선택하여 앱에 액세스합니다.

1. 앱의 위쪽 패널에서 **학습** 을 선택합니다.

1. 학습이 완료되면 브라우저 맨 위에 알림이 나타납니다.

## <a name="training-date-and-time"></a>학습 날짜 및 시간

학습 날짜 및 시간은 GMT + 2입니다.

## <a name="train-with-all-data"></a>모든 데이터를 사용하여 학습

학습은 음수 샘플링(negative sampling)의 작은 비율을 사용합니다. 포털 또는 API를 사용하여 사용 가능한 모든 데이터를 대신 사용할 수 있습니다. 

### <a name="using-the-luis-portal"></a>LUIS 포털 사용

[LUIS 포털](https://www.luis.ai/)에 로그인하고 앱을 클릭합니다. 화면 맨 위에서 **관리** 를 선택하고 **설정** 을 선택한 다음, **비결정적 학습 사용** 옵션을 사용하거나 사용하지 않도록 설정합니다. 사용하지 않도록 설정하면 학습이 사용 가능한 모든 데이터를 사용합니다.

![비결정적 학습을 사용하거나 사용하지 않도록 설정하는 단추](./media/non-determinstic-training.png)

### <a name="using-the-version-settings-api"></a>버전 설정 API 사용

`UseAllTrainingData`를 true로 설정하여 [버전 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)를 사용하면 이 기능을 해제할 수 있습니다.

## <a name="unnecessary-training"></a>불필요한 학습

변경할 때마다 학습할 필요는 없습니다. 학습은 변경 그룹을 모델에 적용한 후 수행해야 하며, 수행할 다음 단계는 테스트 또는 게시입니다. 테스트하거나 게시할 필요가 없으면 학습이 필요하지 않습니다.

## <a name="training-with-the-rest-apis"></a>REST API로 학습

LUIS 포털에서 학습은 **학습** 단추를 한 번만 누르면 됩니다. REST API를 사용한 학습은 2단계 프로세스입니다. 첫 번째 방법은 HTTP POST를 사용한 [학습 요청](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45)입니다. 그런 다음, HTTP Get을 사용하여 [학습 상태](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46)를 요청합니다.

학습이 완료될 때를 알기 위해 모든 모델이 성공적으로 학습될 때까지 상태를 폴링해야 합니다.

## <a name="next-steps"></a>다음 단계

* [대화형 테스트](luis-interactive-test.md)
* [일괄 테스트](luis-how-to-batch-test.md)