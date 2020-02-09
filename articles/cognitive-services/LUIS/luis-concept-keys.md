---
title: LUIS에서 제작 및 런타임 키를 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: LUIS는 두 가지 키 인 제작 키를 사용 하 여 모델을 만들고 사용자 길이 발언를 사용 하 여 예측 엔드포인트을 쿼리 하기 위한 런타임 키를 사용 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75969332"
---
# <a name="authoring-and-runtime-keys"></a>작성 및 런타임 키

Language Understanding (LUIS)에는 두 가지 서비스 및 API 집합이 있습니다. 

* 제작 (이전에는 _프로그래밍 방식_으로 알려짐)
* 예측 런타임

작업 하려는 서비스 및 사용 방법에 따라 몇 가지 주요 유형이 있습니다.

## <a name="non-azure-resources-for-luis"></a>LUIS에 대 한 비 Azure 리소스

### <a name="starter-key"></a>시작 키

LUIS 사용을 처음으로 시작 하면 시작 **키** 가 생성 됩니다. 이 리소스는 다음을 제공 합니다.

* LUIS 포털 또는 Api를 통해 무료 제작 서비스 요청 (Sdk 포함)
* 브라우저, API 또는 Sdk를 통해 매월 무료 1000 예측 엔드포인트 요청

## <a name="azure-resources-for-luis"></a>LUIS 용 Azure 리소스

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

LUIS는 다음과 같은 세 가지 유형의 Azure 리소스를 허용 합니다. 
 
|키|용도|인식 서비스 `kind`|인식 서비스 `type`|
|--|--|--|--|
|[작성 키](#programmatic-key)|작성, 학습, 게시 및 테스트를 사용 하 여 응용 프로그램의 데이터에 액세스 하 고 관리 합니다. LUIS apps를 프로그래밍 방식으로 작성 하려는 경우 LUIS authoring key를 만듭니다.<br><br>`LUIS.Authoring` 키의 목적은 다음을 수행할 수 있도록 하는 것입니다.<br>* 교육 및 게시를 포함 하 여 프로그래밍 방식으로 Language Understanding 앱 및 모델 관리<br> * [참가자 역할](#contributions-from-other-authors)에 사용자를 할당 하 여 제작 리소스에 대 한 사용 권한을 제어 합니다.|`LUIS.Authoring`|`Cognitive Services`|
|[예측 키](#prediction-endpoint-runtime-key)| 예측 엔드포인트 요청을 쿼리 합니다. 클라이언트 앱이 스타터 리소스에서 제공 하는 1000 요청 보다 많은 예측을 요청 하기 전에 LUIS 예측 키를 만듭니다. |`LUIS`|`Cognitive Services`|
|[인식 서비스 다중 서비스 리소스 키](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|LUIS 및 기타 지원 되는 Cognitive Services 공유 된 쿼리 예측 엔드포인트 요청|`CognitiveServices`|`Cognitive Services`|

리소스 만들기 프로세스가 완료 되 면 LUIS 포털에서 해당 키를 앱에 [할당](luis-how-to-azure-subscription.md) 합니다.

게시 하 고 쿼리 하려는 [지역](luis-reference-regions.md#publishing-regions) 에서 LUIS apps를 작성 하는 것이 중요 합니다.

> [!CAUTION]
> 편의를 위해 대부분의 샘플은 [할당량](luis-boundaries.md#key-limits)에 몇 가지 무료 예측 엔드포인트 호출을 제공 하므로 [스타터 키](#starter-key) 를 사용 합니다.  


### <a name="query-prediction-resources"></a>쿼리 예측 리소스

* 런타임 키는 모든 LUIS apps 또는 특정 LUIS 앱에 사용할 수 있습니다. 
* LUIS apps를 작성 하는 데 런타임 키를 사용 하지 마세요. 

LUIS runtime 엔드포인트은 두 가지 유형의 쿼리를 수락 합니다 .이 두 가지는 모두 예측 엔드포인트 런타임 키를 사용 하지만 다른 위치에서 사용 됩니다.

런타임에 액세스 하는 데 사용 되는 엔드포인트은 리소스 지역에 고유한 하위 도메인을 사용 하며 다음 표에 `{region}` 표시 됩니다. 

## <a name="assignment-of-the-key"></a>키 할당

[LUIS 포털](https://www.luis.ai) 에서 또는 해당 api를 통해 런타임 키를 [할당할](luis-how-to-azure-subscription.md) 수 있습니다. 

## <a name="key-limits"></a>키 제한

구독 당 지역 당 최대 10 개의 제작 키를 만들 수 있습니다. 

[키 제한](luis-boundaries.md#key-limits) 및 [Azure 지역](luis-reference-regions.md)을 참조 하세요. 

게시 지역은 작성 지역과 다릅니다. 클라이언트 응용 프로그램을 배치할 게시 영역에 해당 하는 제작 지역에 앱을 만들어야 합니다.

## <a name="key-limit-errors"></a>키 제한 오류
TPS (초당 트랜잭션 수) 할당량을 초과 하는 경우 HTTP 429 오류를 받게 됩니다. 월별 트랜잭션 (TPS) 할당량을 초과 하는 경우 HTTP 403 오류가 표시 됩니다. 

## <a name="contributions-from-other-authors"></a>다른 작성자의 기여

**[리소스 마이그레이션된 앱 제작](luis-migration-authoring.md)** : _참가자_ 는 **액세스 제어 (IAM)** 페이지를 사용 하 여 제작 리소스에 대 한 Azure Portal에서 관리 됩니다. 협력자의 이메일 주소 및 _참가자_ 역할을 사용 하 여 [사용자를 추가 하는 방법을](luis-how-to-collaborate.md)알아봅니다. 

**아직 마이그레이션하지 않은 앱의 경우**: 모든 _공동 작업자_ 는 LUIS 포털에서 관리 **-> 협력자** 페이지를 통해 관리 됩니다.

## <a name="move-transfer-or-change-ownership"></a>소유권 이동, 전송 또는 변경

앱은 소유자의 구독에 의해 결정 되는 Azure 리소스에 의해 정의 됩니다. 

LUIS 앱을 이동할 수 있습니다. Azure Portal 또는 Azure CLI에서 다음 설명서 리소스를 사용 합니다.

* [LUIS authoring resources 간에 앱 이동](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [동일한 구독 내 또는 구독 간에 리소스 이동](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

구독의 [소유권](../../cost-management-billing/manage/billing-subscription-transfer.md) 을 양도 하려면: 

**마이그레이션된 [리소스](luis-migration-authoring.md) 를 마이그레이션한 사용자의 경우**: 리소스 소유자로 서 `contributor`를 추가할 수 있습니다.

**아직 마이그레이션하지 않은 사용자의 경우**: JSON 파일로 앱을 내보냅니다. 다른 LUIS 사용자는 앱을 가져올 수 있으므로 앱 소유자가 됩니다. 새 앱에는 다른 앱 ID가 있습니다.  

## <a name="access-for-private-and-public-apps"></a>개인 및 공용 앱에 대 한 액세스

**개인** 앱의 경우 소유자 및 참가자에 대 한 런타임 액세스를 사용할 수 있습니다. **공용** 응용 프로그램의 경우 고유한 Azure [인식 서비스](../cognitive-services-apis-create-account.md) 또는 [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) 런타임 리소스를 포함 하 고 공용 앱 ID를 가진 모든 사용자가 런타임 액세스를 사용할 수 있습니다. 

현재는 공용 앱의 카탈로그가 없습니다.

### <a name="authoring-access"></a>작성 액세스
[LUIS](luis-reference-regions.md#luis-website) 포털에서 앱에 액세스 하거나 [제작 api](https://go.microsoft.com/fwlink/?linkid=2092087) 는 Azure authoring resource에 의해 제어 됩니다. 

소유자와 모든 참가자는 앱을 제작할 수 있는 권한이 있습니다. 

|작성 액세스에 포함된 작업|메모|
|--|--|
|엔드포인트 키 추가 또는 제거||
|버전 내보내기||
|엔드포인트 로그 내보내기||
|버전 가져오기||
|앱 공개|앱이 공용이면 작성 또는 엔드포인트 키가 있는 모든 사용자가 앱을 쿼리할 수 있습니다.|
|모델 수정|
|게시|
|[활성 학습](luis-how-to-review-endpoint-utterances.md)의 엔드포인트 발화 검토|
|교육|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>예측 엔드포인트 런타임 액세스

쿼리 액세스는 **관리** 섹션의 **응용 프로그램 정보** 페이지에 있는 설정에 의해 제어 됩니다. 

|[프라이빗 엔드포인트](#runtime-security-for-private-apps)|[공용 엔드포인트](#runtime-security-for-public-apps)|
|:--|:--|
|소유자 및 참가자가 사용할 수 있음|소유자, 참가자 및 앱 ID를 알고 있는 다른 사람이 사용할 수 있습니다.|

서버 간 환경에서 LUIS 런타임 키를 호출 하 여 해당 키를 볼 수 있는 사용자를 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API를 호출 하 여 인증 하 고 권한 부여를 확인 한 경우에는에 대 한 호출을 LUIS에 전달 합니다. 이 전략은 메시지 가로채기 (man-in-the-middle) 공격을 방지 하지는 않지만 사용자의 키 및 엔드포인트 URL을 난독 처리 액세스를 추적할 수 있으며 엔드포인트 응답 로깅 (예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가할 수 있습니다.

#### <a name="runtime-security-for-private-apps"></a>전용 앱에 대 한 런타임 보안

개인 앱의 런타임은 다음에만 사용할 수 있습니다.

|키 및 사용자|설명|
|--|--|
|소유자의 작성 키| 최대 1000회의 엔드포인트 적중|
|협력자/기여자 제작 키| 최대 1000회의 엔드포인트 적중|
|작성자 또는 협력자/기여자에 의해 LUIS에 할당 된 모든 키|키 사용량 계층 기반|

#### <a name="runtime-security-for-public-apps"></a>공용 앱에 대 한 런타임 보안

앱이 공용으로 구성되면 키에 전체 엔드포인트 할당량이 사용되지 않는 한 ‘모든’ 유효한 LUIS 작성 키 또는 LUIS 엔드포인트 키가 앱을 쿼리할 수 있습니다.

소유자 또는 참가자가 아닌 사용자는 앱 ID가 지정 된 경우에만 공용 앱의 런타임에 액세스할 수 있습니다. LUIS에는 공용 앱을 검색할 수 있는 공개 ‘시장’이나 다른 방법이 없습니다.  

공용 앱은 모든 지역에 게시되므로 지역 기반 LUIS 리소스 키를 가진 사용자가 해당 리소스 키와 연결된 지역에서 앱에 액세스할 수 있습니다.

## <a name="transfer-of-ownership"></a>소유권 이전

LUIS에는 리소스 소유권을 이전 하는 개념이 없습니다. 

## <a name="securing-the-endpoint"></a>엔드포인트 보안 

서버 간 환경에서 호출 하 여 LUIS 예측 런타임 엔드포인트 키를 볼 수 있는 사람을 제어할 수 있습니다. 봇에서 LUIS를 사용하는 경우, 봇과 LUIS 간의 연결은 이미 안전합니다. LUIS 엔드포인트를 직접 호출하는 경우에는 제어된 액세스(예: [AAD](https://azure.microsoft.com/services/active-directory/))를 사용하여 서버 쪽 API(예 : Azure [함수](https://azure.microsoft.com/services/functions/))를 만들어야 합니다. 서버 쪽 API가 호출되고 인증 및 권한 부여가 확인되면 LUIS에 해당 호출을 전달합니다. 이 전략은 가로채기 공격을 방지하지 않지만 사용자의 엔드포인트를 난독 처리하고, 액세스를 추적하도록 허용하고, 엔드포인트 응답 로깅(예: [Application Insights](https://azure.microsoft.com/services/application-insights/))을 추가하도록 허용합니다.  

## <a name="next-steps"></a>다음 단계

* [버전 관리](luis-concept-version.md) 개념을 이해합니다. 
* [키를 만드는 방법](luis-how-to-azure-subscription.md)에 대해 알아봅니다.
