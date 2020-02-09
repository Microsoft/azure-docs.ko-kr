---
title: 반복적인 앱 디자인-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS는 모델 변경, 발화 예제, 게시 및 엔드포인트 쿼리에서 데이터 수집의 반복 주기에서 가장 좋은 사례를 학습합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422596"
---
# <a name="iterative-app-design-for-luis"></a>LUIS에 대 한 반복적인 앱 디자인

LUIS (Language Understanding) 앱은 반복을 통해 가장 효율적으로 학습 하 고 수행 합니다. 일반적인 반복 주기는 다음과 같습니다.

* 새 버전 만들기
* LUIS app 스키마를 편집 합니다. 다음 내용이 포함됩니다.
    * 예 길이 발언를 사용 하는 의도
    * 엔터티
    * 기능
* 학습, 테스트 및 게시
    * 활성 학습에 대 한 예측 엔드포인트에서 테스트
* 엔드포인트 쿼리에서 데이터 수집

![제작 주기](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>LUIS 스키마 빌드

앱의 스키마는 사용자에 게 요청 _하는 항목 (의도 또는_ _의도_ )과 답변을 확인 하는 데 사용 되는 정보 ( _엔터티_)를 제공 하는 부분을 정의 합니다. 

앱 스키마는 관련 된 단어 및 문구를 결정 하 고 일반적인 단어 순서를 결정 하려면 앱 도메인에만 적용 되어야 합니다. 

예 길이 발언는 인식 된 음성 또는 텍스트와 같이 앱이 런타임에 기대 하는 사용자 입력을 나타냅니다. 

스키마는 의도를 요구 하며 엔터티를 _포함 해야_ 합니다. 

### <a name="example-schema-of-intents"></a>의도의 스키마 예

가장 일반적인 스키마는 의도를 사용 하 여 구성 된 의도 스키마입니다. 이 스키마 유형은 LUIS를 사용 하 여 사용자의 의도를 확인 합니다. 

의도 스키마 형식은 사용자의 의도를 LUIS 결정 하는 데 도움이 되는 경우 엔터티를 가질 수 있습니다. 예를 들어, 배송 엔터티 (의도에 대 한 설명자)는 LUIS 결정 하는 데 도움이 됩니다. 

### <a name="example-schema-of-entities"></a>엔터티의 예제 스키마

엔터티 스키마는 사용자 길이 발언에서 추출 된 데이터에 대 한 엔터티를 중심으로 합니다. 예를 들어 사용자가 "3 개의 때울을 주문 하 고 싶습니다." 라고 말합니다. 추출 되는 두 개의 엔터티: _3_ 및 _때울_. 이는 주문을 수행 하는 의도를 충족 하는 데 사용 됩니다. 

엔터티 스키마의 경우 utterance의 의도는 클라이언트 응용 프로그램에 더 중요 하지 않습니다. 

엔터티 스키마를 구성 하는 일반적인 방법은 모든 예제 길이 발언를 **없음** 의도에 추가 하는 것입니다. 

### <a name="example-of-a-mixed-schema"></a>혼합 스키마의 예

가장 강력 하 고 완성도 높은 스키마는 모든 범위의 엔터티 및 기능이 포함 된 의도 스키마입니다. 클라이언트 응용 프로그램에 이러한 정보가 필요 하므로이 스키마는 의도 또는 엔터티 스키마로 시작 하 고 두 가지 개념을 포함 하도록 확장할 수 있습니다. 

## <a name="add-example-utterances-to-intents"></a>의도에 길이 발언 예제 추가

LUIS에는 각각의 **의도**에 몇 가지 예 길이 발언가 필요 합니다. 예 길이 발언에는 utterance가 의도 한 의도를 확인 하는 데 충분 한 단어 선택 및 단어 주문 변형이 필요 합니다. 

> [!CAUTION]
> 예 길이 발언를 대량으로 추가 하지 마십시오. 15 ~ 30 개의 특정 예제로 시작 합니다. 

각 예제 utterance는 **엔터티**를 사용 하 여 디자인 하 고 레이블을 지정 **하는 데 필요한 데이터** 를 포함 해야 합니다. 

|Key 요소|용도|
|--|--|
|Intent|사용자 길이 발언를 단일 의도 또는 작업으로 **분류** 합니다. 예에는 `BookFlight` 및 `GetWeather`가 포함됩니다.|
|엔터티|Utterance에서 데이터를 **추출** 하 여 의도를 완료 해야 합니다. 예를 들면 여행 날짜와 시간 및 위치를 포함 합니다.|

LUIS 앱은 utterance를 **없음** 의도에 할당 하 여 앱 도메인과 관련이 없는 길이 발언을 무시 하도록 디자인할 수 있습니다.

## <a name="test-and-train-your-app"></a>응용 프로그램 테스트 및 학습

길이 발언에 대 한 15 ~ 30 개의 다른 예제가 있는 경우, 필수 엔터티가 레이블이 지정 되 면 LUIS 앱을 테스트 하 고 [학습](luis-how-to-train.md) 해야 합니다. 

## <a name="publish-to-a-prediction-endpoint"></a>예측 엔드포인트에 게시

LUIS 앱은 목록 [예측 엔드포인트 영역](luis-reference-regions.md)에서 사용할 수 있도록 게시 해야 합니다.

## <a name="test-your-published-app"></a>게시된 앱 테스트

HTTPS 예측 엔드포인트에서 게시 된 LUIS 앱을 테스트할 수 있습니다. 예측 엔드포인트에서 테스트를 통해 LUIS는 [검토](luis-how-to-review-endpoint-utterances.md)를 위해 신뢰도가 낮은 길이 발언를 선택할 수 있습니다.  

## <a name="create-a-new-version-for-each-cycle"></a>각 주기에 대해 새 버전 만들기

각 버전은 LUIS 앱의 시간에 대 한 스냅숏입니다. 앱을 변경하기 전에 새 버전을 만듭니다. 의도를 제거 하 고 이전 상태로 길이 발언 이전 버전으로 다시 전환 하는 것이 더 쉽습니다.

버전 ID는 문자, 숫자 또는 ‘.’으로 구성되며 10자 이하여야 합니다.

초기 버전(0.1)은 기본 활성 버전입니다. 

### <a name="begin-by-cloning-an-existing-version"></a>기존 버전을 복제 하 여 시작

새 버전에 대 한 시작 지점으로 사용할 기존 버전을 복제 합니다. 버전을 복제 하 고 나면 새 버전이 **활성** 버전이 됩니다. 

### <a name="publishing-slots"></a>슬롯 게시

스테이지 및/또는 프로덕션 슬롯에 게시할 수 있습니다. 각 슬롯에는 다른 버전 또는 동일한 버전이 있을 수 있습니다. 이 기능은 봇 또는 다른 LUIS 앱 호출에서 사용할 수 있는 프로덕션에 게시 하기 전에 변경 사항을 확인 하는 데 유용 합니다. 

LUIS 앱의 [엔드포인트](luis-glossary.md#endpoint)에서는 학습 된 버전을 자동으로 사용할 수 없습니다. LUIS 앱 엔드포인트에서 사용할 수 있도록 버전을 [게시](luis-how-to-publish-app.md) 하거나 다시 게시 해야 합니다. **스테이징** 및 **프로덕션**에 게시 하 여 엔드포인트에서 사용할 수 있는 두 가지 버전의 앱을 제공할 수 있습니다. 응용 프로그램의 더 많은 버전을 엔드포인트에서 사용할 수 있어야 하는 경우 버전을 내보내고 새 앱으로 다시 가져와야 합니다. 새 앱에는 다른 앱 ID가 포함됩니다.

### <a name="import-and-export-a-version"></a>버전 가져오기 및 내보내기

앱 수준에서 버전을 가져올 수 있습니다. 해당 버전은 활성 버전이 되며 응용 프로그램 파일의 `versionId` 속성에서 버전 ID를 사용 합니다. 버전 수준에서 기존 앱으로 가져올 수도 있습니다. 새 버전이 활성 버전이 됩니다. 

버전은 앱 또는 버전 수준 에서도 내보낼 수 있습니다. 유일한 차이는 앱 수준에서 내보낸 버전이 현재 활성 버전이지만, 버전 수준에서는 **[설정](luis-how-to-manage-versions.md)** 페이지에서 내보낼 버전을 선택할 수 있다는 것입니다. 

내보낸 파일에는 다음이 포함 **되지 않습니다** .

* 가져온 후 앱을 다시 학습 때문에 컴퓨터에서 배운 정보
* 참가자 정보

LUIS 앱 스키마를 백업 하려면 [LUIS 포털](https://www.luis.ai/applications)에서 버전을 내보냅니다.

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>버전 및 참가자를 사용 하 여 참가자 변경 내용 관리

LUIS는 Azure 리소스 수준 권한을 제공 하 여 앱에 대 한 참가자 개념을 사용 합니다. 이 개념을 버전 관리와 결합 하 여 대상 공동 작업을 제공 합니다. 

다음 기술을 사용 하 여 앱에 대 한 참가자 변경 내용을 관리할 수 있습니다.

### <a name="manage-multiple-versions-inside-the-same-app"></a>동일한 앱 내부에서 여러 버전 관리

각 저자에 대 한 기본 버전에서 [복제](luis-how-to-manage-versions.md#clone-a-version) 하 여 시작 합니다. 

각 작성자는 자신의 앱 버전을 변경 합니다. 작성자가 모델에 만족 하면 새 버전을 JSON 파일로 내보냅니다.  

내보낸 앱, json 또는. n a n 파일은 변경 내용에 대해 비교할 수 있습니다. 파일을 결합 하 여 새 버전의 단일 파일을 만듭니다. 새 병합 된 버전을 나타내기 위해 `versionId` 속성을 변경 합니다. 해당 버전을 원래 앱으로 가져옵니다. 

이 방법을 사용하면 하나의 활성 버전, 하나의 스테이지 버전 및 하나의 게시된 버전을 사용할 수 있습니다. 활성 버전의 결과를 [대화형 테스트 창](luis-interactive-test.md)에서 게시 된 버전 (스테이지 또는 프로덕션)과 비교할 수 있습니다.

### <a name="manage-multiple-versions-as-apps"></a>여러 버전을 앱으로 관리

기본 버전을 [내보냅니다](luis-how-to-manage-versions.md#export-version). 각 작성자는 버전을 가져옵니다. 앱을 가져오는 사용자는 버전의 소유자입니다. 앱 수정을 완료하면 버전을 내보냅니다. 

내보낸 앱은 JSON 형식 파일이며 기본 내보내기와 변경 내용을 비교할 수 있습니다. 파일을 결합하여 새 버전의 단일 JSON 파일을 만듭니다. JSON에서 **versionId** 속성을 변경하여 새 병합 버전을 표시합니다. 해당 버전을 원래 앱으로 가져옵니다.

[공동 작업자](luis-how-to-collaborate.md)의 기여 작성에 대해 자세히 알아보세요.

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>엔드포인트 길이 발언을 검토 하 여 새 반복 주기 시작

반복 주기를 완료 하면 프로세스를 반복할 수 있습니다. 낮은 신뢰도로 표시 된 [예측 엔드포인트 길이 발언](luis-how-to-review-endpoint-utterances.md) LUIS을 검토 하는 것으로 시작 합니다. 이러한 길이 발언를 확인 하 여 올바른 예측 의도가 있고 올바른 엔터티를 추출 했습니다. 변경 내용을 검토 하 고 적용 한 후에는 검토 목록이 비어 있어야 합니다.  

## <a name="next-steps"></a>다음 단계

[협업](luis-concept-keys.md) 개념을 알아봅니다.
