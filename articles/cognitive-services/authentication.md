---
title: Authentication
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services 리소스에 대한 요청을 인증하는 세 가지 방법으로 구독 키, 전달자 토큰 또는 다중 서비스 구독이 있습니다. 이 문서에서는 각 방법과 요청을 수행하는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423935"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Azure Cognitive Services에 대한 요청 인증

Azure Cognitive Service에 대한 각 요청은 인증 헤더를 포함해야 합니다. 이 헤더는 서비스 또는 서비스 그룹에 대한 구독의 유효성을 검사하는 데 사용되는 구독 키 또는 액세스 토큰을 전달합니다. 이 문서에서는 요청을 인증하는 세 가지 방법과 각각에 대한 요구 사항에 대해 알아봅니다.

* [단일 서비스 구독 키로 인증](#authenticate-with-a-single-service-subscription-key)
* [다중 서비스 구독 키로 인증](#authenticate-with-a-multi-service-subscription-key)
* [토큰으로 인증](#authenticate-with-an-authentication-token)
* [AAD (Azure Active Directory를 사용 하 여 인증](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>전제 조건

요청을 수행하기 전에 Azure 계정과 Azure Cognitive Services 구독이 필요합니다. 계정이 이미 있는 경우 다음 섹션으로 건너뜁니다. 계정이 없는 경우 몇 분 내에 [Azure에 대한 Cognitive Services 계정 만들기](cognitive-services-apis-create-account.md)를 설정 하는 방법을 안내 합니다.

계정을 만들거나 [무료 평가판](https://azure.microsoft.com/try/cognitive-services/my-apis)을 활성화 한 후 [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 에서 구독 키를 가져올 수 있습니다.

## <a name="authentication-headers"></a>인증 헤더

Azure Cognitive Services에서 사용할 수 있는 인증 헤더를 빠르게 살펴보겠습니다.

| 헤더 | 설명 |
|--------|-------------|
| Ocp-Apim-Subscription-Key | 특정 서비스에 대한 구독 키 또는 다중 서비스 구독 키를 사용하여 인증하려면 이 헤더를 사용합니다. |
| Ocp-Apim-Subscription-Region | 이 헤더는 [Translator Text API](./Translator/reference/v3-0-reference.md)에서 다중 서비스 구독 키를 사용하는 경우에만 필요합니다. 이 헤더를 사용하여 구독 지역을 지정합니다. |
| 권한 부여 | 인증 토큰을 사용하는 경우 이 헤더를 사용합니다. 토큰 교환을 수행하는 단계는 다음 섹션에서 자세히 설명합니다. 제공되는 값은 `Bearer <TOKEN>` 형식을 따릅니다. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>단일 서비스 구독 키로 인증

첫 번째 옵션은 Translator Text와 같은 특정 서비스에 대한 구독 키를 사용하여 요청을 인증하는 것입니다. 이 키는 만든 각 리소스에 대해 Azure Portal에서 사용할 수 있습니다. 구독 키를 사용하여 요청을 인증하려면 `Ocp-Apim-Subscription-Key` 헤더로 전달해야 합니다.

이러한 요청 샘플에서는 `Ocp-Apim-Subscription-Key` 헤더를 사용하는 방법을 보여 줍니다. 이 샘플을 사용할 때는 유효한 구독 키를 포함해야 한다는 점을 명심하세요.

Bing Web Search API에 대한 호출 샘플은 다음과 같습니다.
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Translator Text API에 대한 호출 샘플은 다음과 같습니다.
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

다음 비디오는 Cognitive Services 키를 사용하는 방법을 보여줍니다.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>다중 서비스 구독 키로 인증

>[!WARNING]
> 현재 이러한 서비스는 다중 서비스 키 QnA Maker, Speech Services, Custom Vision 및 변칙 탐지기를 지원 **하지 않습니다** .

또한 이 옵션은 구독 키를 사용하여 요청을 인증합니다. 주요 차이점은 구독 키가 특정 서비스에 연결되지 않고, 오히려 단일 키를 사용하여 여러 Cognitive Services에 대한 요청을 인증할 수 있다는 것입니다. 지역별 가용성, 지원되는 기능 및 가격 책정에 대한 자세한 내용은 [Cognitive Services 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)을 참조하세요.

구독 키는 각 요청에서 `Ocp-Apim-Subscription-Key` 헤더로 제공됩니다.

[![Cognitive Services에 대한 다중 서비스 구독 키 데모](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>지원되는 지역

다중 서비스 구독 키를 사용하여 `api.cognitive.microsoft.com`에 대한 요청을 수행하는 경우 URL에 지역을 포함시켜야 합니다. 예: `westus.api.cognitive.microsoft.com`.

Translator Text API에서 다중 서비스 구독 키를 사용하는 경우 `Ocp-Apim-Subscription-Region` 헤더에서 구독 지역을 지정해야 합니다.

다중 서비스 인증이 지원되는 지역은 다음과 같습니다.

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>샘플 요청

Bing Web Search API에 대한 호출 샘플은 다음과 같습니다.

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Translator Text API에 대한 호출 샘플은 다음과 같습니다.

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>인증 토큰으로 인증

일부 Azure Cognativity Services는 인증 토큰을 수락하며, 경우에 따라 인증 토큰을 요구합니다. 현재 인증 토큰을 지원하는 서비스는 다음과 같습니다.

* Text Translation API
* Speech Services: 음성 텍스트 REST API
* 음성 서비스: 텍스트 음성 변환 REST API

>[!NOTE]
> QnA Maker도 인증 헤더를 사용하지만 엔드포인트 키가 필요합니다. 자세한 내용은 [QnA Maker: 기술 자료에서 답변 가져오기](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)를 참조 하세요.

>[!WARNING]
> 시간이 지남에 따라 인증 토큰을 지원하는 서비스가 변경될 수 있으므로 이 인증 방법을 사용하기 전에 먼저 서비스에 대한 API 참조를 확인하세요.

단일 서비스 및 다중 서비스 구독 키는 모두 인증 토큰으로 교환할 수 있습니다. 인증 토큰은 10분 동안 유효합니다.

인증 토큰은 요청에 `Authorization` 헤더로 포함됩니다. 제공되는 토큰 값은 `Bearer` 뒤에 나와야 합니다(예: `Bearer YOUR_AUTH_TOKEN`).

### <a name="sample-requests"></a>샘플 요청

인증 토큰에 대한 구독 키를 교환하려면 다음 URL을 사용합니다. `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

토큰 교환을 지원하는 다중 서비스 지역은 다음과 같습니다.

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

인증 토큰을 가져온 후에 각 요청에서 `Authorization` 헤더로 전달해야 합니다. Translator Text API에 대한 호출 샘플은 다음과 같습니다.

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>참고 항목

* [Cognitive Services란?](welcome.md)
* [Cognitive Services 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [사용자 지정 하위 도메인](cognitive-services-custom-subdomains.md)
