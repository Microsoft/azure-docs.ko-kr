---
title: FAQ (질문과 대답)-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 LUIS(Language Understanding)에 대한 FAQ(질문과 대답)에 대해 알아봅니다.
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a2472064720af0a25568a2f173b971898b1f2e25
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123103"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Language Understanding FAQ(질문과 대답)

이 문서에서는 LUIS(Language Understanding)에 대한 FAQ(질문과 대답)에 대해 알아봅니다.

## <a name="whats-new"></a>새로운 기능

Language Understanding의 새로운 기능 (LUIS)에 [대해 자세히 알아보세요](whats-new.md) .

<a name="luis-authoring"></a>

## <a name="authoring"></a>작성

### <a name="what-are-the-luis-best-practices"></a>LUIS 모범 사례는 무엇인가요?
[작성 주기](luis-concept-app-iteration.md)부터 시작해서 [모범 사례](luis-concept-best-practices.md)를 읽어봅니다.

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>LUIS에서 내 앱 작성을 시작하는 가장 좋은 방법은 무엇인가요?

앱을 빌드하는 가장 좋은 방법은 [증분 프로세스](luis-concept-app-iteration.md)를 사용하는 것입니다.

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>내 앱의 의도를 모델링하는 적절한 방법은 무엇인가요? 보다 구체적이거나 보다 일반적인 의도를 만들어야 하나요?

너무 일반적이어서 겹치지는 않지만, LUIS가 비슷한 의도 간을 구분하는 것이 어려울 정도로 너무 구체적이 않은 의도를 선택합니다. 구분되는 특정 의도를 만드는 것이 LUIS 모델링의 모범 사례 중 하나입니다.

### <a name="is-it-important-to-train-the-none-intent"></a>없음 의도를 학습하는 것이 중요한가요?

예. 다른 의도에 더 많은 레이블을 추가할 때처럼 많은 발언으로 **없음** 의도를 학습하는 것이 좋습니다. 좋은 비율은 의도에 추가되는 10개 레이블마다 1개 또는 2개의 레이블을 **없음**에 추가하는 것입니다. 이 비율은 증폭 LUIS의 구분 능력을 높여줍니다.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>발언에서 맞춤법 오류를 수정하려면 어떻게 해야 하나요?

[Bing Spell Check API V7](luis-tutorial-bing-spellcheck.md) 자습서를 참조하세요. LUIS는 Bing Spell Check API v7에 따라 부여되는 제한을 적용합니다.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>프로그래밍 방식으로 LUIS 앱을 편집하려면 어떻게 해야 하나요?
프로그래밍 방식으로 LUIS 앱을 편집하려면 [작성 API](https://go.microsoft.com/fwlink/?linkid=2092087)를 사용합니다. 작성 API 호출 방법에 대한 예제를 보려면 [LUIS 작성 API 호출](./get-started-get-model-rest-apis.md) 및 [Node.js를 사용하여 프로그래밍 방식으로 LUIS 앱 빌드](./luis-tutorial-node-import-utterances-csv.md)를 참조하세요. 작성 API에서는 엔드포인트 키가 아닌 [작성 키](luis-concept-keys.md#azure-resources-for-luis)를 사용해야 합니다. 프로그래밍 방식으로 작성할 경우 매월 최대 1,000,000개 호출과 초당 5개의 트랜잭션이 허용됩니다. LUIS에서 사용하는 키에 대한 자세한 내용은 [키 관리](./luis-concept-keys.md)를 참조하세요.

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>정규식 일치를 제공하는 패턴 기능은 어디에 있나요?
이전 **패턴 기능**이 현재는 더 이상 사용되지 않으며 **[패턴](luis-concept-patterns.md)** 으로 바뀌었습니다.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>엔터티를 사용하여 올바른 데이터를 끌어오려면 어떻게 해야 하나요?
[엔터티](luis-concept-entity-types.md) 및 [데이터 추출](luis-concept-data-extraction.md)을 참조하세요.

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>예제 발언 변형에 문장 부호가 포함되어야 하나요?
의도에 다른 변형을 예제 발언으로 추가하거나 문장 부호를 [무시하는 구문](luis-concept-patterns.md#pattern-syntax)을 사용하여 예제 발언 패턴을 추가합니다.

### <a name="does-luis-currently-support-cortana"></a>LUIS는 현재 Cortana를 지원하나요?

Cortana의 미리 빌드된 앱은 2017년부터 더 이상 사용되지 않습니다. 따라서 더 이상 지원되지 않습니다.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>LUIS 앱의 소유권을 양도하려면 어떻게 해야 하나요?
LUIS 앱을 다른 Azure 구독으로 이전하려면 LUIS 앱을 내보낸 후 새 계정을 사용하여 가져옵니다. LUIS 앱을 호출하는 클라이언트 애플리케이션에서 해당 앱 ID를 업데이트합니다. 새 앱은 원래 앱과는 약간 다른 LUIS 점수를 반환할 수 있습니다.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>미리 빌드된 엔터티에는 사용자 지정 엔터티가 아닌 예제 utterance 태그가 지정 됩니다. 어떻게 할까요? 문제를 해결 하 시겠습니까? 

LUIS 포털에서 추출 하려는 정확한 엔터티의 텍스트에 레이블을 지정할 수 있습니다. LUIS 포털에 올바른 엔터티 예측이 표시 되지 않는 경우에는 길이 발언를 추가 하 고 텍스트 내에 엔터티 레이블을 추가 하거나 기능 등의 설명자를 추가 해야 할 수 있습니다. 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>앱 또는 버전 파일을 가져오려고 했지만 오류가 발생 했습니다. 문제가 발생 한 경우 

[버전 가져오기 오류](luis-how-to-manage-versions.md#import-errors)에 대해 자세히 알아보세요.

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>공동 작업 및 기여

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Azure AD(Azure Active Directory) 또는 RBAC(역할 기반 액세스 제어)를 사용하여 협력자에게 LUIS 액세스 권한을 부여하려면 어떻게 할까요?

협력자에게 액세스 권한을 부여하는 방법을 알아보려면 [Azure Active Directory 리소스](luis-how-to-collaborate.md#azure-active-directory-resources) 및 [Azure Active Directory 테넌트 사용자](luis-how-to-collaborate.md#azure-active-directory-tenant-user)를 참조하세요. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>엔드포인트

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>HTTP 403 오류 상태 코드를 받았습니다. 이 문제를 어떻게 해결하나요?

가격 책정 계층에 대 한 초당 트랜잭션 수 또는 월별 트랜잭션을 초과 하는 경우 403 및 429 오류 상태 코드를 받게 됩니다. 가격 책정 계층을 늘리거나 Language Understanding [컨테이너](luis-container-howto.md)를 사용 합니다.

이러한 무료 1000 엔드포인트 쿼리를 모두 사용 하거나 가격 책정 계층의 월간 트랜잭션 할당량을 초과 하는 경우 HTTP 403 오류 상태 코드를 받게 됩니다. 

이 오류를 해결 하려면 [가격 책정 계층](luis-how-to-azure-subscription.md#change-pricing-tier) 을 상위 계층으로 변경 하거나 [새 리소스를 만들어](get-started-portal-deploy-app.md#create-the-endpoint-resource) [앱에 할당](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)해야 합니다.

이 오류에 대 한 해결 방법은 다음과 같습니다.

* [Azure Portal](https://portal.azure.com)의 Language Understanding 리소스에서 **리소스 관리-> 가격 책정 계층**에서 가격 책정 계층을 더 높은 TPS 계층으로 변경 합니다. 리소스가 이미 Language Understanding 앱에 할당 된 경우에는 Language Understanding 포털에서 아무것도 수행할 필요가 없습니다.
*  사용량이 가장 높은 가격 책정 계층을 초과 하는 경우 부하 분산 장치 앞에 더 많은 Language Understanding 리소스를 추가 합니다. Kubernetes 또는 Docker Compose를 사용 하는 [Language Understanding 컨테이너](luis-container-howto.md) 는이를 지원할 수 있습니다.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>HTTP 429 오류 상태 코드를 받았습니다. 이 문제를 어떻게 해결하나요?

가격 책정 계층에 대 한 초당 트랜잭션 수 또는 월별 트랜잭션을 초과 하는 경우 403 및 429 오류 상태 코드를 받게 됩니다. 가격 책정 계층을 늘리거나 Language Understanding [컨테이너](luis-container-howto.md)를 사용 합니다.

초당 트랜잭션이 가격 책정 계층을 초과 하면이 상태 코드가 반환 됩니다.  

해결 방법은 다음과 같습니다.

* 가장 높은 계층에 있지 않은 경우 [가격 책정 계층을 늘릴](luis-how-to-azure-subscription.md#change-pricing-tier)수 있습니다.
* 사용량이 가장 높은 가격 책정 계층을 초과 하는 경우 부하 분산 장치 앞에 더 많은 Language Understanding 리소스를 추가 합니다. Kubernetes 또는 Docker Compose를 사용 하는 [Language Understanding 컨테이너](luis-container-howto.md) 는이를 지원할 수 있습니다.
* 이 상태 코드가 표시 될 때 사용자가 직접 구현한 [다시 시도 정책을](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) 사용 하 여 클라이언트 응용 프로그램 요청을 게이트 할 수 있습니다. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>내 엔드포인트 쿼리에서 예기치 않은 결과를 반환했습니다. 어떻게 해야 하나요?

예기치 않은 쿼리 예측 결과는 게시된 모델의 상태를 기반으로 합니다. 모델을 수정하려면 모델을 변경하고, 학습하고, 다시 게시해야 할 수도 있습니다. 

모델 수정은 [활성 학습](luis-how-to-review-endpoint-utterances.md)으로 시작합니다.

모든 학습 데이터를 사용하기 위해 [애플리케이션 버전 설정 API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings)를 업데이트하여 비결정적 학습을 제거할 수 있습니다.

다른 팁을 보려면 [모범 사례](luis-concept-best-practices.md)를 검토하세요. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>LUIS가 쿼리 주변 또는 단어 중간에 공백을 추가하는 이유는 무엇인가요?
LUIS는 [문화권](luis-glossary.md#token)에 따라 발언을 [토큰화](luis-language-support.md#tokenization)합니다. 원래 값과 토큰화된 값 둘다 [데이터 추출](luis-concept-data-extraction.md#tokenized-entity-returned)에 사용할 수 있습니다.

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>LUIS 엔드포인트 키를 만들고 할당하려면 어떻게 해야 하나요?
Azure에서 [서비스](luis-how-to-azure-subscription.md) 수준에 대해 [엔드포인트 키를 만듭니다](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). **[Azure 리소스](luis-how-to-azure-subscription.md)** 페이지에서 [키를 할당](luis-how-to-azure-subscription.md) 합니다. 이 작업에 해당하는 API는 없습니다. 그런 후 엔드포인트에 대한 HTTP 요청을 [새 엔드포인트 키를 사용하도록](luis-concept-keys.md) 변경해야 합니다.

### <a name="how-do-i-interpret-luis-scores"></a>LUIS 점수는 어떻게 해석할 수 있나요?
시스템은 해당 값에 관계 없이 점수가 가장 높은 의도를 사용해야 합니다. 예를 들어, 0.5(50%) 미만 점수라고 해서 LUIS가 반드시 낮은 신뢰도를 갖는다는 것을 의미하는 것은 아닙니다. 더 많은 학습 데이터를 제공하면 가능성이 가장 높은 의도의 [점수](luis-concept-prediction-score.md)를 높일 수 있습니다.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>내 앱의 대시보드에서 엔드포인트 적중이 표시되지 않는 이유는 무엇인가요?
앱의 대시보드에 표시되는 총 엔드포인트 적중은 주기적으로 업데이트되지만 Azure Portal의 LUIS 엔드포인트 키와 연결된 메트릭은 좀 더 자주 업데이트됩니다.

업데이트된 엔드포인트 적중이 대시보드에 보이지 않는 경우 Azure Portal에 로그인하고 LUIS 엔드포인트 키와 연결된 리소스를 찾은 다음, **메트릭**을 열어 **총 호출 수** 메트릭을 선택합니다. 엔드포인트 키가 둘 이상의 LUIS 앱에 사용되는 경우 Azure Portal의 메트릭은 해당 메트릭을 사용하는 모든 LUIS 앱의 집계된 호출 수를 나타냅니다.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>엔드포인트 할당량을 확인할 수 있는 PowerShell 명령이 있나요?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 PowerShell 명령을 사용해 엔드포인트 할당량을 확인할 수 있습니다.

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>내 LUIS 앱이 어제도 작동했으나 오늘 403 오류가 발생하고 있습니다. 앱을 변경한 적도 없었습니다. 이 문제를 어떻게 해결하나요?
다음 [지침](#how-do-i-create-and-assign-a-luis-endpoint-key) 에 따라 LUIS 엔드포인트 키를 만들고 앱에 할당 합니다. 그런 다음 [새 엔드포인트 키를 사용](luis-concept-keys.md)하도록 클라이언트 응용 프로그램의 HTTP 요청을 엔드포인트으로 변경 해야 합니다. 다른 지역에서 새 리소스를 만든 경우 HTTP 클라이언트 요청 영역도 변경 합니다.

### <a name="how-do-i-secure-my-luis-endpoint"></a>내 LUIS 엔드포인트를 어떻게 보호하나요?
[엔드포인트 보안](luis-concept-keys.md#securing-the-endpoint)을 참조하세요.

## <a name="working-within-luis-limits"></a>LUIS 제한 내에서 작업

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>LUIS 앱이 지원할 수 있는 의도 및 엔터티의 최대 수는 몇 개인가요?
[경계](luis-boundaries.md) 참조를 참조하세요.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>최대 의도보다 많은 수의 의도로 LUIS 앱을 빌드하려고 합니다. 어떻게 해야 하나요?

[의도에 대한 모범 사례](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)를 참조하세요.

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>최대 엔터티보다 많은 수의 엔터티로 LUIS에서 앱을 빌드하려고 합니다. 어떻게 해야 하나요?

[엔터티에 대한 모범 사례](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)를 참조하세요.

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>구 목록의 수 및 크기 제한은 얼마나 되나요?
[구 목록](./luis-concept-feature.md)의 최대 길이에 대해서는 [경계](luis-boundaries.md) 참조를 참조하세요.

### <a name="what-are-the-limits-on-example-utterances"></a>예제 발언에 대한 제한은 얼마나 되나요?
[경계](luis-boundaries.md) 참조를 참조하세요.

## <a name="testing-and-training"></a>학습 및 테스트

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>앱의 일부 모델에 대한 일괄 처리 테스트 창에서 오류가 발생합니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 오류는 모델의 레이블 및 예측 간에 약간의 불일치가 있음을 나타냅니다. 이 문제를 해결하려면 다음 작업 중 하나 또는 둘 다를 수행합니다.
* LUIS의 의도 간 구분 능력을 향상시키려면 더 많은 레이블을 추가합니다.
* LUIS가 더 빠르게 학습하도록 하려면 도메인별 어휘를 도입하는 구 목록 기능을 추가합니다.

[일괄 처리 테스트](luis-tutorial-batch-testing.md) 자습서를 참조하세요.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>앱을 내보낸 후 새 앱으로 다시 가져오면(새 앱 ID 사용) LUIS 예측 점수가 달라집니다. 그 이유는 무엇인가요?

[동일한 앱 사본 간 예측 차이점](luis-concept-prediction-score.md#review-intents-with-similar-scores)을 참조하세요.

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>내 앱을 변경한 후 일부 발언의 의도가 맞지 않습니다. 이 문제는 우연히 사라진 것 같습니다. 이 문제를 어떻게 해결하나요? 

[모든 데이터를 사용하여 학습](luis-how-to-train.md#train-with-all-data)을 참조하세요.

## <a name="app-publishing"></a>앱 게시

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>"앱에 키 추가" 창에서 테넌트 ID란 무엇인가요?
Azure에서 테넌트는 서비스와 연결된 클라이언트 또는 조직을 나타냅니다. Azure Portal에서 **Azure Active Directory**관리 > 속성 > 을 선택하여 **디렉터리 ID** 상자에서 테넌트 ID를 찾습니다.

![Azure Portal의 테넌트 ID](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>내가 앱에 할당한 것보다 더 많은 엔드포인트 키가 있는 이유는 무엇인가요?
각 LUIS 앱에는 편의상 엔드포인트 목록에 작성/시작 키가 있습니다. 이 키는 LUIS 테스트를 위해 몇 개의 엔드포인트 적중 항목이 있습니다.  

앱이 LUIS GA(일반 제공) 전에 생성되었다면 구독에서 LUIS 엔드포인트 키가 자동으로 할당됩니다. GA 마이그레이션을 좀 더 쉽게 진행하기 위해 이렇게 구현되었습니다. Azure Portal의 새 LUIS 엔드포인트는 LUIS에 자동으로 _할당되지 않습니다_.

## <a name="key-management"></a>키 관리

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>어떤 키가 필요한지, 어디서 얻을 수 있는지, 얻은 키로 무엇을 해야 하는지 알아보려면 어떻게 할까요?? 

제작 키와 예측 런타임 키의 차이점에 대해 알아보려면 [LUIS에서 예측 엔드포인트 키 작성 및 쿼리](luis-concept-keys.md) 를 참조 하세요. 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>할당량 초과 오류가 발생합니다. 이 문제를 어떻게 해결하나요? 

자세한 내용은 HTTP 상태 코드 [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) 및 [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) 수정을 참조 하세요.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>많은 엔드포인트 쿼리를 처리해야 합니다. 어떻게 할까요? 

자세한 내용은 HTTP 상태 코드 [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) 및 [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) 수정을 참조 하세요.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>제작 키를 만들었지만 LUIS 포털에 표시 되지 않습니다. 어떻게 된 건가요?

제작 [키 환경으로 마이그레이션한](luis-migration-authoring.md)후 LUIS 포털에서 사용할 수 있습니다.  

## <a name="app-management"></a>앱 관리

### <a name="how-do-i-download-a-log-of-user-utterances"></a>사용자 발언의 로그를 다운로드하려면 어떻게 해야 하나요?
기본적으로 LUIS 앱은 사용자의 발언을 로깅합니다. 사용자가 LUIS 앱으로 전송하는 발언 로그를 다운로드하려면 **내 앱**으로 이동한 후 앱을 선택합니다. 상황에 맞는 도구 모음에서 **엔드포인트 로그 내보내기**를 선택합니다. 로그 형식은 쉼표로 구분된 값(CSV) 파일로 지정됩니다.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>발언 로깅을 사용하지 않도록 설정하려면 어떻게 해야 하나요?
클라이언트 애플리케이션이 LUIS를 쿼리하는 사용하는 엔드포인트 URL에서 `log=false`를 설정하여 사용자 발언의 로깅을 해제할 수 있습니다. 그렇지만 로깅을 해제하면 [활성 학습](luis-concept-review-endpoint-utterances.md#what-is-active-learning)에 따라 발언을 제안하거나 성능을 향상시키는 LUIS 앱 기능을 사용할 수 없게 됩니다. 데이터 개인 정보 문제 때문에 `log=false`를 설정하는 경우 LUIS에서 해당 사용자 발언의 기록을 다운로드하거나 해당 발언을 사용하여 앱을 향상시킬 수 없습니다.

로깅은 발언의 유일한 스토리지입니다.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>모든 내 엔드포인트 발언을 로깅하지 않아도 되는 이유는 무엇인가요?
예측 분석을 위해 로그를 사용하는 경우에는 로그에 테스트 발언을 캡처하지 않도록 합니다.

## <a name="data-management"></a>데이터 관리

### <a name="can-i-delete-data-from-luis"></a>LUIS에서 데이터를 삭제할 수 있나요?

* LUIS를 학습시키는 데 사용되는 예제 발언은 항상 삭제할 수 있습니다. LUIS 앱에서 예제 발언을 삭제하면 LUIS 웹 서비스에서 제거되고 내보낼 수 없게 됩니다.
* **엔드포인트 발언 검토** 페이지에서 LUIS가 제안하는 사용자 발언 목록에 있는 발언을 삭제할 수 있습니다. 이 목록에서 발화를 삭제하면 제안되지 않지만 로그에서 삭제되지는 않습니다.
* 계정을 삭제하면 모든 앱과 예제 발화 및 로그가 함께 삭제됩니다. 데이터는 영구적으로 삭제하기 전에 60일 동안 서버에 유지됩니다.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>내가 LUIS로 보낸 데이터를 Microsoft에서 어떻게 관리하나요?

[보안 센터](https://www.microsoft.com/trustcenter)에 당사의 약속과 고객이 Azure 서비스에서 사용할 수 있는 데이터 관리 및 액세스 옵션에 대해 설명되어 있습니다.

## <a name="language-and-translation-support"></a>언어 및 번역 지원

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>앱이 한 언어로 되어 있고 다른 언어로 병렬 앱을 만들려고 합니다. 이렇게 하는 가장 쉬운 방법은 무엇인가요?
1. 앱을 내보냅니다.
2. 내보낸 앱의 JSON 파일에서 레이블이 지정된 발언을 대상 언어로 번역합니다.
3. 의도 및 엔터티의 이름을 변경하거나 원래대로 유지해야 할 수 있습니다.
4. 마지막으로, LUIS 앱을 가져와 대상 언어로 나타냅니다.

## <a name="app-notification"></a>앱 알림

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>할당량이 거의 초과되려고 한다는 전자 메일이 수신되는 이유는 무엇인가요?
작성/시작 키는 매월 1,000개의 엔드포인트 쿼리만 허용합니다. LUIS 엔드포인트 키(무료 또는 유료)를 만들고 엔드포인트 쿼리를 만들 때 해당 키를 사용합니다. 봇이나 다른 클라이언트 애플리케이션에서 엔드포인트 쿼리를 만드는 경우 해당 위치에서 LUIS 엔드포인트를 변경해야 합니다.

## <a name="bots"></a>봇

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>내 LUIS 봇이 작동하지 않습니다. 어떻게 해야 합니까?

먼저 문제가 LUIS와 관련이 있는지, 또는 LUIS 미들웨어 외부에서 발생하는지 확인해야 합니다. 

#### <a name="resolve-issue-in-luis"></a>LUIS의 문제 해결
[LUIS 엔드포인트](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint)에서 LUIS로 동일한 발화를 전달합니다. 오류가 표시되면, 오류가 더 이상 반환되지 않을 때까지 LUIS의 문제를 해결합니다. 일반적인 오류는 다음과 같습니다.

* `Out of call volume quota. Quota will be replenished in <time>.` - 이 문제는 작성 키에서 [엔드포인트 키](luis-how-to-azure-subscription.md)로 변경해야 하거나, [서비스 계층](luis-how-to-azure-subscription.md#change-pricing-tier)을 변경해야 함을 나타냅니다. 

#### <a name="resolve-issue-in-azure-bot-service"></a>Azure Bot Service의 문제 해결

Azure Bot Service를 사용 중이며, **웹 채팅에서 테스트**가 `Sorry, my bot code is having an issue`를 반환하는 문제가 있는 경우 로그를 확인합니다.

1. Azure Portal에서 사용자 봇에 대한 **봇 관리** 섹션의 **빌드**를 선택합니다.
1. 온라인 코드 편집기를 엽니다. 
1. 맨 위의 파란색 탐색 모음에서 봇 이름(오른쪽에서 두 번째 항목)을 선택합니다.
1. 결과 드롭다운 목록에서 **Kudu 콘솔 열기**를 선택합니다.
1. **LogFiles**를 선택한 다음, **애플리케이션**을 선택합니다. 모든 로그 파일을 검토합니다. 애플리케이션 폴더에 오류가 표시되지 않는 경우 **LogFiles** 아래의 모든 로그 파일을 검토합니다. 
1. C# 등의 컴파일된 언어를 사용하는 경우 프로젝트를 다시 빌드해야 합니다.

> [!Tip] 
> 콘솔에서 패키지를 설치할 수도 있습니다. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Bot Framework를 사용하여 로컬 머신에서 디버그하는 동안 문제를 해결합니다. 

봇의 로컬 디버깅에 대한 자세한 내용은 [봇 디버그](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0)를 참조하세요.

## <a name="integrating-luis"></a>LUIS 통합

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Azure 웹앱 봇 구독 프로세스 동안 내 LUIS 앱은 어디에서 만들어지나요?
LUIS 템플릿을 선택하고 템플릿 창에서 **선택** 단추를 선택하면 왼쪽 창이 해당 템플릿 유형을 포함하도록 변경되고 LUIS 템플릿을 만들 지역을 묻는 메시지가 표시됩니다. 그렇지만 웹앱 봇 프로세스는 LUIS 구독을 만들지 않습니다.

![LUIS 템플릿 웹앱 봇 지역](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>어떤 LUIS 지역에서 Bot Framework 음성 초기화를 지원하나요?
[음성 초기화](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)는 미국 중부 인스턴스의 LUIS 앱에서만 지원됩니다.

## <a name="api-programming-strategies"></a>API 프로그래밍 전략

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>리소스의 LUIS 지역을 프로그래밍 방식으로 가져오려면 어떻게 해야 하나요? 

LUIS 샘플을 사용하여 C# 또는 Node.Js를 통해 프로그래밍 방식으로 [지역을 찾을 수 있습니다](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region). 

## <a name="luis-service"></a>LUIS 서비스

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>온-프레미스 또는 프라이빗 클라우드에서 LUIS(Language Understanding)를 사용할 수 있나요?

예, 사용량을 산정하는 데 필요한 연결이 있는 경우 이러한 시나리오에 LUIS [컨테이너](luis-container-howto.md)를 사용할 수 있습니다. 

## <a name="migrating-to-the-next-version"></a>다음 버전으로 마이그레이션

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Preview V3 API로 마이그레이션할 어떻게 할까요? 있나요? 

[LUIS apps에 대 한 API v2에서 V3 마이그레이션 가이드를](luis-migration-api-v3.md) 참조 하세요.

## <a name="build-2019-conference-announcements"></a>빌드 2019 회의 알림

다음 기능은 빌드 2019 회의에서 릴리스 되었습니다.

* [V3 API 마이그레이션 가이드 미리 보기](luis-migration-api-v3.md)
* [향상 된 분석 대시보드](luis-how-to-use-dashboard.md)
* [향상 된 미리 작성 된 도메인](luis-reference-prebuilt-domains.md) 
* [동적 목록 엔터티](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [외부 엔터티](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

비디오:

* [Azure 대화형 AI를 사용 하 여 차세대 비즈니스를 위해 비즈니스를 확장 하는 방법](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>다음 단계

LUIS에 대한 자세한 내용은 다음 리소스를 참조하세요.
* [LUIS로 태그가 지정된 스택 오버플로 질문](https://stackoverflow.com/questions/tagged/luis)
* [MSDN LUIS(Language Understanding Intelligent Services) 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
