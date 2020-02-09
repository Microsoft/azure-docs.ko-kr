---
title: 미리 빌드된 모든 엔터티-LUIS
titleSuffix: Azure Cognitive Services
description: 이 문서에는 LUIS(Language Understanding)에 포함된 미리 빌드된 엔터티 목록이 포함되어 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: 254fec23ef34b936405439e0334e24e594a24dc4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890225"
---
# <a name="entities-per-culture-in-your-luis-model"></a>LUIS 모델에서 문화권별 엔터티

LUIS(Language Understanding)에서는 미리 빌드된 엔터티를 제공합니다. 미리 빌드된 엔터티가 애플리케이션에 포함되어 있으면 LUIS의 엔드포인트 응답에는 해당 엔터티 예측이 포함됩니다. 또한 모든 예제 발언은 해당 엔터티로 레이블이 지정됩니다. 미리 빌드된 엔터티의 동작은 수정할 수 **없습니다**. 다른 설명이 없는 한, 미리 빌드된 엔터티는 모든 LUIS 애플리케이션 로캘(문화권)에서 사용할 수 있습니다. 다음 표에는 각 문화권에 대해 지원되는 미리 빌드된 엔터티가 나와 있습니다.

|문화권|하위 문화권|메모|
|--|--|--|
|중국어|[zh-CN](#chinese-entity-support)||
|네덜란드어|[nl-NL](#dutch-entity-support)||
|한국어|[en-US(미국)](#english-american-entity-support)||
|프랑스어|[fr-CA(캐나다)](#french-canadian-entity-support), [fr-FR(프랑스)](#french-france-entity-support), ||
|독일어|[de-DE](#german-entity-support)||
|이탈리아어|[it-IT](#italian-entity-support)||
|일본어|[ja-JP](#japanese-entity-support)||
|한국어|[ko-KR](#korean-entity-support)||
|포르투갈어|[pt-BR(브라질)](#portuguese-brazil-entity-support)||
|스페인어|[es-ES(스페인)](#spanish-spain-entity-support), [es-MX(멕시코)](#spanish-mexico-entity-support)||
|터키어|[터키어](#turkish-entity-support)|터키어에서 지원 되는 미리 빌드된 엔터티가 없습니다.|

## <a name="prediction-endpoint-runtime"></a>예측 엔드포인트 런타임

특정 언어로 작성 된 엔터티의 가용성은 예측 엔드포인트 런타임 버전에 따라 결정 됩니다. 

## <a name="chinese-entity-support"></a>중국어 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```zh-CN``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>네덜란드어 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```nl-NL``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[날짜/시간](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>영어(미국) 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```en-US``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>프랑스어(프랑스) 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```fr-FR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>프랑스어(캐나다) 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```fr-CA``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>독일어 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```de-DE``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>이탈리아어 엔터티 지원

이탈리아어 미리 작성 된 사용 기간, 통화, 차원, 숫자, 백분율 _해상도_ 가 V2 및 V3 preview에서 변경 되었습니다.

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```it-IT``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[날짜/시간](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>중국어 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```ja-JP``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2,-   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2,-   |
[날짜/시간](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2,-   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2,-   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2,-   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2,-   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2,-   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>한국어 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```ko-KR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    -   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    -   |
[날짜/시간](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>포르투갈어(브라질) 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```pt-BR``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>스페인어(스페인) 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```es-ES``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    V2, V3   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    V2, V3   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>스페인어(멕시코) 엔터티 지원

다음과 같은 엔터티가 지원됩니다.

|미리 빌드된 엔터티|```es-MX``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>년<br>month<br>week<br>일   |    -   |
[통화(금액)](luis-reference-prebuilt-currency.md):<br>달러<br>소수부 단위(예: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>볼륨<br>area<br>weight<br>information(예: bit/byte)<br>length(예: meter)<br>speed(예: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

[사용되지 않는 미리 빌드된 엔터티](luis-reference-prebuilt-deprecated.md)에 대한 참고를 참조하세요.

KeyPhrase를 포르투갈어(브라질) ```pt-BR```의 모든 하위 문화권에서 사용할 수 없습니다.

## <a name="turkish-entity-support"></a>터키어 엔터티 지원

**터키어에서 지원 되는 미리 빌드된 엔터티가 없습니다.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>미리 빌드된 엔터티 문화권에 참여
미리 빌드된 엔터티는 Recognizers-Text open-source 프로젝트에서 개발됩니다. 이 프로젝트에 [참여](https://github.com/Microsoft/Recognizers-Text)하세요. 이 프로젝트에는 문화권별 currency 예제가 포함되어 있습니다. 

GeographyV2 및 PersonName은 인식기-텍스트 프로젝트에 포함되지 않습니다. 이러한 미리 빌드된 엔터티를 사용하는 문제는 [지원 요청](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 엽니다. 

## <a name="next-steps"></a>다음 단계

[number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) 및 [currency](luis-reference-prebuilt-currency.md) 엔터티에 대해 알아봅니다. 
