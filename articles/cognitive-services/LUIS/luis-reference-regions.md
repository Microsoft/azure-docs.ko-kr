---
title: 영역 & 엔드포인트 게시-LUIS
titleSuffix: Azure Cognitive Services
description: Azure Portal에 지정 된 지역은 LUIS 앱을 게시할 때와 동일한 지역에 대해 엔드포인트 URL이 생성 됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 4f8e5d1c5ac3b0383fee5a901d2bb34edab8dae2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225487"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>작성 및 게시 지역과 관련 키

해당 LUIS 포털에서 3 가지 제작 지역을 지원 합니다. LUIS 앱을 둘 이상의 지역에 게시하려면 지역당 하나 이상의 키가 필요합니다. 

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS 제작 지역
지역을 기반으로 하는 세 가지 LUIS 제작 포털이 있습니다. 동일한 지역에서 작성하고 게시해야 합니다. 

|LUIS|작성 지역|Azure 지역 이름|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[preview.luis.ai](https://preview.luis.ai)|FTI(연방세 정보)에<br>유럽 이외<br>오스트레일리아 이외| `westus`|
|[au.luis.ai][au.luis.ai] <br>[preview.au.luis.ai](https://preview.au.luis.ai)|오스트레일리아| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] <br>[preview.eu.luis.ai](https://preview.eu.luis.ai)|유럽|`westeurope`|

제작 지역에는 [페어링된 장애 조치 지역이](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)있습니다. 

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>지역 및 Azure 리소스 게시
앱은 LUIS 포털에 추가된 LUIS 리소스와 관련된 모든 지역에 게시됩니다. 예를 들어 [www.luis.ai][www.luis.ai]에서 만든 앱의 경우 **westus** 에서 luis 또는 인식 서비스 리소스를 만들고 [리소스로 앱에 추가](luis-how-to-azure-subscription.md)하는 경우 해당 지역에 앱이 게시 됩니다. 

## <a name="public-apps"></a>공용 앱
공용 앱은 모든 지역에 게시되므로 지역 기반 LUIS 리소스 키를 가진 사용자가 해당 리소스 키와 연결된 지역에서 앱에 액세스할 수 있습니다.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>게시 지역은 지역 제작에 연결 됩니다.

작성 지역 앱은 해당 게시 지역에만 게시할 수 있습니다. 현재 앱이 잘못된 작성 지역에 있는 경우 앱을 내보내고 게시 지역의 올바른 작성 지역으로 가져옵니다. 

https://www.luis.ai 에서 만들어진 LUIS 앱은 [유럽](#publishing-to-europe) 및 [오스트레일리아](#publishing-to-australia) 지역을 제외한 모든 엔드포인트에 게시될 수 있습니다. 

## <a name="publishing-to-europe"></a>유럽에 게시

유럽 지역에 게시하려면 https://eu.luis.ai 에만 LUIS 앱을 만듭니다. 유럽 지역의 키를 사용하여 다른 곳에 게시하려고 하면 LUIS가 경고 메시지를 표시합니다. 대신 https://eu.luis.ai 을(를) 사용하세요. [https://eu.luis.ai][eu.luis.ai] 에서 만든 LUIS apps는 다른 지역으로 자동으로 마이그레이션되지 않습니다. LUIS 앱을 마이그레이션하려면 내보낸 다음, 가져옵니다.

## <a name="europe-publishing-regions"></a>유럽 게시 지역

 글로벌 지역 | API 지역 제작 및 웹 사이트 작성| 게시 및 쿼리 지역<br>`API region name`   |  엔드포인트 URL 형식   |
|-----|------|------|------|
| [유럽](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 프랑스 중부<br>`francecentral`     | https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [유럽](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 북유럽<br>`northeurope`     | https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [유럽](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 서유럽<br>`westeurope`    |  https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| [유럽](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| 영국 남부<br>`uksouth`    |  https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-australia"></a>오스트레일리아에 게시

오스트레일리아 지역에 게시하려면 https://au.luis.ai 에만 LUIS 앱을 만듭니다. 오스트레일리아 지역의 키를 사용하여 다른 곳에 게시하려고 하면 LUIS가 경고 메시지를 표시합니다. 대신 https://au.luis.ai 을(를) 사용하세요. [https://au.luis.ai][au.luis.ai] 에서 만든 LUIS apps는 다른 지역으로 자동으로 마이그레이션되지 않습니다. LUIS 앱을 마이그레이션하려면 내보낸 다음, 가져옵니다.

## <a name="australia-publishing-regions"></a>오스트레일리아 발행 지역

 글로벌 지역 | API 지역 제작 및 웹 사이트 작성| 게시 및 쿼리 지역<br>`API region name`   |  엔드포인트 URL 형식   |
|-----|------|------|------|
| [오스트레일리아](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| 오스트레일리아 동부<br>`australiaeast`     |  https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="publishing-to-other-regions"></a>다른 지역에 게시

다른 지역에 게시 하려면 [https://www.luis.ai](https://www.luis.ai) 에서만 LUIS apps를 만듭니다. 

## <a name="other-publishing-regions"></a>기타 게시 영역

 글로벌 지역 | API 지역 제작 및 웹 사이트 작성| 게시 및 쿼리 지역<br>`API region name`   |  엔드포인트 URL 형식   |
|-----|------|------|------|
| 아프리카 | `westus`<br>[www.luis.ai][www.luis.ai]| 남아프리카 북부<br>`southafricanorth` |  https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 인도 중부<br>`centralindia` |  https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 동아시아<br>`eastasia`     |  https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 일본 동부<br>`japaneast`     |   https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 일본 서부<br>`japanwest`     |   https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 한국 중부<br>`koreacentral`     |   https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 아시아 | `westus`<br>[www.luis.ai][www.luis.ai]| 동남아시아<br>`southeastasia`     |   https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 캐나다 중부<br>`canadacentral`     |   https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 중부<br>`centralus`     |   https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 동부<br>`eastus`      |  https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 동부 2<br>`eastus2`     |  https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 중북부<br>`northcentralus`  |  https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 중남부<br>`southcentralus`  |  https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   | 
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 중서부<br>`westcentralus`    |  https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |
| 북아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 미국 서부<br>`westus`  |   https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 북아메리카 |`westus`<br>[www.luis.ai][www.luis.ai] | 미국 서부 2<br>`westus2`    |  https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY  |
| 남아메리카 | `westus`<br>[www.luis.ai][www.luis.ai] | 브라질 남부<br>`brazilsouth`    |  https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY   |

## <a name="endpoints"></a>엔드포인트

현재 LUIS에는 두 개의 엔드포인트이 있습니다. 하나는 제작 용 이며 하나는 쿼리 예측 분석입니다.

|용도|URL|
|--|--|
|작성|`https://{region}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|텍스트 분석(쿼리 예측)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|

다음 표에서는 앞의 표에서 중괄호 `{}`로 표시된 매개 변수에 대해 설명합니다.

|매개 변수를 포함해야 합니다.|용도|
|--|--|
|region|Azure 지역 - 작성 및 게시에 서로 다른 지역 포함|
|appID|URL 경로에 사용되며 앱 대시보드에서 발견된 LUIS 앱 ID|
|q|채팅 봇과 같은 클라이언트 애플리케이션에서 보낸 발언 텍스트|

## <a name="failover-regions"></a>장애 조치 영역

각 지역에는 장애 조치할 보조 지역이 있습니다. 유럽 및 오스트레일리아 내에서 유럽 장애 조치 (failover)는 오스트레일리아 내부에서 장애 조치 합니다.

제작 지역에는 [페어링된 장애 조치 지역이](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [미리 빌드된 엔터티 참조](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
