---
title: Translator Text API V3.0 참조
titleSuffix: Azure Cognitive Services
description: Translator Text API V3.0에 대한 참조 문서입니다. Translator Text API 버전 3은 최신 JSON 기반 Web API를 제공합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 11/14/2019
ms.author: swmachan
ms.openlocfilehash: 172bf452cc5197db95e0e1e55c7c687971194899
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123045"
---
# <a name="translator-text-api-v30"></a>Translator Text API v3.0

## <a name="whats-new"></a>새로운 기능

Translator Text API 버전 3은 최신 JSON 기반 Web API를 제공합니다. 기존 기능을 더 적은 개수의 작업으로 통합하여 유용성 및 성능을 개선하고 새로운 기능을 제공합니다.

 * 특정 언어의 텍스트를 한 스크립트에서 다른 스크립트로 음차하여 변환
 * 단일 요청에서 여러 언어로 번역
 * 단일 요청의 언어 감지, 번역, 음차
 * 사전에서 용어에 대 한 대체 번역을 조회 하 여 컨텍스트에서 사용 된 용어를 보여 주는 후방 번역 및 예제를 찾습니다.
 * 추가 정보를 제공하는 언어 감지 결과

## <a name="base-urls"></a>기준 URL

Microsoft Translator는 여러 데이터 센터 위치에서 제공됩니다. 현재는 10 개의 [Azure 지역](https://azure.microsoft.com/global-infrastructure/regions)에 있습니다.

* **미주:** 미국 동부, 미국 서 부, 미국 중부, 미국 서 부 2 
* **아시아 태평양:** 한국 남부, 일본 동부, 동남 아시아 및 오스트레일리아 동부
* **유럽:** 서유럽 및 유럽 서부

Microsoft Translator Text API에 대한 요청은 대부분 요청이 시작된 위치와 가장 가까운 데이터 센터에서 처리됩니다. 데이터 센터 오류가 발생 하는 경우 요청은 Azure 지리 외부에서 라우팅될 수 있습니다.

특정 Azure 지리에서 요청을 처리 하도록 강제 하려면 API 요청의 전역 엔드포인트을 원하는 지역 엔드포인트으로 변경 합니다.

|설명|Azure 지리|기준 URL|
|:--|:--|:--|
|Azure|Global (비 지역)|   api.cognitive.microsofttranslator.com|
|Azure|미국|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europe|  api-eur.cognitive.microsofttranslator.com|
|Azure|아시아 태평양|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>인증

Azure Cognitive Services에서 Translator Text API 또는 [Cognitive Services 다중 서비스](https://azure.microsoft.com/pricing/details/cognitive-services/) 를 구독 하 고 구독 키 (Azure Portal에서 사용 가능)를 사용 하 여 인증 합니다. 

구독을 인증하는 데 사용할 수 있는 헤더는 세 개가 있습니다. 다음 표에서는 각를 사용 하는 방법을 설명 합니다.

|헤더|설명|
|:----|:----|
|Ocp-Apim-Subscription-Key|*비밀 키를 전달하는 경우 Cognitive Services 구독에 사용합니다*.<br/>값은 Translator Text API 구독에 대한 Azure 비밀 키입니다.|
|권한 부여|*인증 토큰을 전달하는 경우 Cognitive Services 구독에 사용합니다*.<br/>값은 전달자 토큰인 `Bearer <token>`입니다.|
|Ocp-Apim-Subscription-Region|*다중 서비스 비밀 키를 전달 하는 경우 Cognitive Services 다중 서비스 구독과 함께 사용 합니다.*<br/>값은 다중 서비스 구독의 지역입니다. 다중 서비스 구독을 사용 하지 않는 경우이 값은 선택 사항입니다.|

###  <a name="secret-key"></a>비밀 키
첫 번째 옵션은 `Ocp-Apim-Subscription-Key` 헤더를 사용하여 인증하는 것입니다. 요청에 `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` 헤더를 추가 합니다.

### <a name="authorization-token"></a>권한 부여 토큰
또는 액세스 토큰에 대한 비밀 키를 교환할 수 있습니다. 이 토큰은 각 요청에 `Authorization` 헤더로 포함됩니다. 인증 토큰을 받으려면 다음 URL에 대해 `POST` 요청을 수행합니다.

| 환경     | 인증 서비스 URL                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

다음은 비밀 키가 제공된 경우 토큰을 얻기 위한 요청 예입니다.

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

요청에 성공하면 인코드된 액세스 토큰이 응답 본문에 일반 텍스트로 반환됩니다. 유효한 토큰이 인증의 전달자 토큰으로 Translator 서비스에 전달됩니다.

```http
Authorization: Bearer <Base64-access_token>
```

인증 토큰은 10분 동안 유효합니다. Translator Api를 여러 번 호출 하는 경우 토큰을 다시 사용 해야 합니다. 그러나 프로그램에서 오랜 시간 동안 변환기 API에 대 한 요청을 수행 하는 경우 프로그램은 정기적으로 새 액세스 토큰을 요청 해야 합니다 (예: 8 분 마다).

### <a name="multi-service-subscription"></a>다중 서비스 구독

마지막 인증 옵션은 인식 서비스의 다중 서비스 구독을 사용 하는 것입니다. 그러면 단일 비밀 키를 사용하여 여러 서비스에 대한 요청을 인증할 수 있습니다. 

다중 서비스 비밀 키를 사용 하는 경우 요청에 인증 헤더를 두 개 포함 해야 합니다. 첫 번째는 비밀 키를 전달하고, 두 번째는 구독과 관련된 지역을 지정합니다. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

다중 서비스 텍스트 API 구독에는 지역이 필요 합니다. 선택한 지역은 다중 서비스 구독 키를 사용 하는 경우 텍스트 번역에 사용할 수 있는 유일한 지역 이며 Azure Portal를 통해 다중 서비스 구독에 등록할 때 선택한 것과 동일한 지역 이어야 합니다.

사용 가능한 지역은 `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, `japaneast`, `japanwest`, `koreacentral`, `northcentralus`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, `westus2`, `southafricanorth`입니다.

`Subscription-Key` 매개 변수를 사용한 쿼리 문자열에 비밀 키를 전달하는 경우 `Subscription-Region` 쿼리 매개 변수를 사용하여 지역을 지정해야 합니다.

전달자 토큰을 사용하는 경우 지역 엔드포인트에서 `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` 토큰을 가져와야 합니다.


## <a name="errors"></a>오류

표준 오류 응답은 이름/값 쌍이 포함된 JSON 개체 `error`입니다. 값도 다음 속성을 가진 JSON 개체입니다.

  * `code`: 서버에서 정의된 오류 코드입니다.
  * `message`: 사용자가 읽을 수 있는 오류 표시를 제공하는 문자열입니다.

예를 들어 평가판 구독이 있는 고객은 무료 할당량이 모두 사용된 후 다음 오류를 받게 됩니다.

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
오류 코드는 오류를 더 범주화하도록 뒤에 3자리 숫자가 오는 3자리 HTTP 상태 코드로 결합된 6자리 숫자입니다. 일반적인 오류 코드는 다음과 같습니다.

| 코드 | 설명 |
|:----|:-----|
| 400000| 요청 입력 중 하나가 올바르지 않습니다.|
| 400001| "scope" 매개 변수가 올바르지 않습니다.|
| 400002| "category" 매개 변수가 올바르지 않습니다.|
| 400003| 언어 지정자 누락되었거나 올바르지 않습니다.|
| 400004| 대상 스크립트 지정자("To script")가 누락되었거나 올바르지 않습니다.|
| 400005| 입력 텍스트가 누락되었거나 올바르지 않습니다.|
| 400006| 언어 및 스크립트의 조합이 올바르지 않습니다.|
| 400018| 원본 스크립트 지정자("From script")가 누락되었거나 올바르지 않습니다.|
| 400019| 지정 된 언어 중 하나가 지원 되지 않습니다.|
| 400020| 입력 텍스트의 배열에서 요소 중 하나가 올바르지 않습니다.|
| 400021| API 버전 매개 변수가 누락되었거나 올바르지 않습니다.|
| 400023| 지정된 언어 쌍 중 하나가 올바르지 않습니다.|
| 400035| 원본 언어("From" 필드)가 올바르지 않습니다.|
| 400036| 대상 언어("To" 필드)가 누락되었거나 올바르지 않습니다.|
| 400042| 지정된 옵션("Options" 필드) 중 하나가 올바르지 않습니다.|
| 400043| 클라이언트 추적 ID(ClientTraceId 필드 또는 X-ClientTranceId 헤더)가 누락되었거나 올바르지 않습니다.|
| 400050| 입력 텍스트가 너무 깁니다. [요청 제한](../request-limits.md)보기|
| 400064| "translation" 매개 변수가 누락되었거나 올바르지 않습니다.|
| 400070| 대상 스크립트(ToScript 매개 변수)의 수가 대상 언어(To parameter)의 수와 일치하지 않습니다.|
| 400071| 값이 TextType에 적합하지 않습니다.|
| 400072| 입력 텍스트의 배열에 요소가 너무 많습니다.|
| 400073| 스크립트 매개 변수가 올바르지 않습니다.|
| 400074| 요청 분문이 유효한 JSON이 아닙니다.|
| 400075| 언어 쌍 및 범주 조합이 올바르지 않습니다.|
| 400077| 최대 요청 크기를 초과했습니다. [요청 제한](../request-limits.md)보기|
| 400079| from과 to 언어 간의 변환에 대해 요청된 사용자 지정 시스템이 존재하지 않습니다.|
| 400080| 언어 또는 스크립트에는 음이 지원 되지 않습니다.|
| 401000| 자격 증명이 누락되었거나 올바르지 않으므로 요청에 권한이 없습니다.|
| 401015| "제공된 자격 증명은 Speech API에 대한 것입니다. 이 요청에 Text API에 대한 자격 증명이 필요합니다. 구독을 사용 하 여 Translator Text API 합니다. "|
| 403000| 작업이 허용되지 않습니다.|
| 403001| 구독이 무료 할당량을 초과했기 때문에 작업이 허용되지 않습니다.|
| 405000| 요청 메서드가 요청된 리소스에 대해 지원되지 않습니다.|
| 408001| 요청한 변환 시스템을 준비 하 고 있습니다. 잠시 후에 다시 시도하세요.|
| 408002| 들어오는 스트림을 기다리는 동안 요청 시간이 초과 되었습니다. 서버가 대기 하도록 준비 된 시간 내에 클라이언트에서 요청을 생성 하지 않았습니다. 클라이언트는 나중에 수정 하지 않고 요청을 반복할 수 있습니다.|
| 415000| Content-Type 헤더가 누락되었거나 올바르지 않습니다.|
| 429000, 429001, 429002| 클라이언트에서 요청 제한을 초과 하 여 서버가 요청을 거부 했습니다.|
| 500000| 예기치 않은 오류가 발생했습니다. 이 오류가 계속 발생하는 경우 오류의 날짜/시간, 응답 헤더 X-RequestId의 요청 식별자 및 요청 헤더 X-ClientTraceId의 클라이언트 식별자를 사용하여 보고합니다.|
| 503000| 서비스를 일시적으로 사용할 수 없습니다. 다시 시도하세요. 이 오류가 계속 발생하는 경우 오류의 날짜/시간, 응답 헤더 X-RequestId의 요청 식별자 및 요청 헤더 X-ClientTraceId의 클라이언트 식별자를 사용하여 보고합니다.|

## <a name="metrics"></a>메트릭 
메트릭을 사용 하면 아래 스크린샷에 표시 된 것 처럼 메트릭 섹션의 Azure Portal에서 변환기 사용 및 가용성 정보를 볼 수 있습니다. 자세한 내용은 [데이터 및 플랫폼 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)을 참조 하세요.

![번역기 메트릭](../media/translatormetrics.png)

다음 표에서는 번역 API 호출을 모니터링 하는 데 사용 되는 방법에 대 한 설명과 함께 사용 가능한 메트릭을 보여 줍니다.

| 메트릭 | 설명 |
|:----|:-----|
| TotalCalls| 총 API 호출 수.|
| TotalTokenCalls| 인증 토큰을 사용 하 여 토큰 서비스를 통한 총 API 호출 수입니다.|
| SuccessfulCalls| 성공한 호출 수입니다.|
| TotalErrors| 오류 응답이 있는 호출 수입니다.|
| BlockedCalls| 요금 또는 할당량 한도를 초과한 호출 수입니다.|
| ServerErrors| 서버 내부 오류 (5XX)를 사용 하는 호출 수입니다.|
| ClientErrors| 클라이언트 쪽 오류 (4XX)를 사용 하는 호출 수입니다.|
| 대기 시간| 요청을 완료 하는 데 걸린 시간 (밀리초)입니다.|
| CharactersTranslated| 들어오는 텍스트 요청에 있는 문자의 총 수입니다.|
