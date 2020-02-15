---
title: 언어 지원 - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API는 NMT (신경망 변환)를 사용 하 여 텍스트를 텍스트로 변환 하기 위한 다음 언어를 지원 합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: a4f9833e8dd14dc7c8ec5849cb809bf2089a5dae
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206126"
---
# <a name="language-and-region-support-for-the-translator-text-api"></a>Translator Text API에 대한 언어 및 지역 지원

Translator Text API는 다음 언어로 텍스트를 번역하도록 지원합니다. NMT(신경 기계 번역)는 고품질의 AI 지원 기계 번역을 위한 새로운 표준으로, 신경계를 사용할 수 있을 때 Translator Text API V3를 통해 기본 방식으로 사용할 수 있습니다.

[기계 번역 작동 방식에 대해 자세히 알아보기](https://www.microsoft.com/translator/mt.aspx)

## <a name="translation"></a>Translation

**V2 Translator API**

> [!NOTE]
> V2는 2018 년 4 월 30 일부 터 더 이상 사용 되지 않습니다. V3에 독점적으로 제공 되는 새로운 기능을 활용 하려면 응용 프로그램을 V3로 마이그레이션 하세요.

* 통계 전용:이 언어에 사용할 수 있는 신경망이 없습니다.
* 사용 가능한 신경망: 신경망을 사용할 수 있습니다. `category=generalnn` 매개 변수를 사용하여 인공신경망 시스템에 액세스합니다.
* 신경망: 신경망은 기본 변환 시스템입니다. `category=smt` 매개 변수를 사용하여 Microsoft Translator Hub를 통해 통계 시스템에 액세스할 수 있습니다.
* 신경망: 신경망만 사용할 수 있습니다.

**V3 Translator API** V3 Translator API는 기본적으로 인공신경망 기반이며, 통계 시스템은 인공신경망 시스템이 없는 경우에만 사용할 수 있습니다.

> [!NOTE]
> 현재 사용자 지정 변환기에서 신경망의 하위 집합을 사용할 수 있으며,이를 점차적으로 추가 하는 중입니다. [사용자 지정 번역기에서 현재 사용할 수 있는 보기 언어](#customization)입니다.

|언어|  언어 코드|  V3 API|
|:-----|:-----:|:-----|
|아프리칸스어| `af`|   신경|
|아랍어|    `ar`    |   신경|
|벵골어|    `bn`    |   신경|
|보스니아어(라틴 문자)|   `bs`    |   신경|
|불가리아어| `bg`    |   신경|
|광둥어(번체)|   `yue`|  통계|
|카탈로니아어|   `ca`    |   통계|
|중국어 간체|    `zh-Hans`|신경|
|중국어 번체|   `zh-Hant`       |신경|
|크로아티아어|  `hr`    |신경|
|체코어| `cs`    |   신경|
|덴마크어|    `da`        |신경|
|네덜란드어| `nl`|   신경|
|영어|   `en`    |   신경|
|에스토니아어|  `et`    |   신경|
|피지어|    `fj`    |   통계|
|필리핀어|  `fil`   |   통계|
|핀란드어|   `fi`    |   신경|
|프랑스어|    `fr`    |   신경|
|독일어|    `de`    |   신경|
|그리스어| `el`    |   신경|
|아이티 크리올|    `ht`        |통계|
|히브리어 |`he`   |신경
|힌디어| `hi`    |   신경|
|몽 다오어| `mww`   |   통계|
|헝가리어| `hu`    |   신경|
|아이슬란드어| `is`    |   신경|
|인도네시아어|    `id`    |   통계|
|아일랜드어 | `ga`| 신경
|이탈리아어|   `it`    |   신경|
|일본어|  `ja`    |   신경|
|칸나다어|`kn`| 신경
|스와힐리어| `sw`    |   통계|
|클링곤어|   `tlh`   |   통계|
|클링곤어(plqaD)|   `tlh-Qaak`  |   통계|
|한국어 |`ko`   |   신경|
|라트비아어|   `lv`    |   신경|
|리투아니아어|    `lt`    |   신경|
|마다가스카르어|  `mg`    |   통계|
|말레이어| `ms`        |통계|
|말라얄람어| `ml` | 신경
|몰타어|   `mt`    |   통계|
|마오리어| `mi`  | 신경|
|노르웨이어| `nb`    |   신경|
|페르시아어|   `fa`    |   신경|
|폴란드어|    `pl`    |   신경|
|포르투갈어(브라질)|   `pt-br` |   신경|
|포르투갈어(포르투갈)| `pt-pt` | 신경
|펀잡어|`pa`|신경
|케레타로 오토미어|   `otq`   |   통계|
|루마니아어|  `ro`    |   신경|
|러시아어|   `ru`    |   신경|
|사모아어|    `sm`    |   통계|
|세르비아어(키릴 자모)|    `sr-Cyrl`|  통계|
|세르비아어(라틴 문자)|   `sr-Latn`       |통계|
|슬로바키아어|    `sk`    |   신경|
|슬로베니아어| `sl`    |   신경|
|스페인어|   `es`    |   신경|
|스웨덴어|   `sv`    |신경|
|타히티어|  `ty`    |통계|
|타밀어| `ta`    |   신경|
|텔루구어|    `te`    |   신경|
|태국어|  `th`    |   신경|
|통가어|    `to`    |   통계|
|터키어|   `tr`        |신경|
|우크라이나어| `uk`    |   신경|
|우르두어|  `ur`    |   통계|
|베트남어|    `vi`    |   신경|
|웨일스어| `cy`    |   신경|
|유카텍 마야어|  `yua`   |   통계|

> [!NOTE]
> 언어 코드 `pt`는 기본적으로 `pt-br`, 포르투갈어 (브라질)로 바뀝니다.

## <a name="transliteration"></a>음역

음역 방법은 다음과 같은 언어를 지원합니다. "대상/원본"에서 "<-->"은 언어가 나열된 스크립트에서/로 음역될 수 있음을 나타냅니다. "-->"은 언어가 한 스크립트에서 다른 스크립트로만 음역될 수 있음을 나타냅니다.

| 언어    | 언어 코드 | 스크립트 | 대상/원본 | 스크립트|
|:----------- |:-------------:|:-------------:|:-------------:|:-------------:|
| 아랍어 | `ar` | 아랍어 `Arab` | <--> | 라틴어 `Latn` |
|벵골어  | `bn` | 벵골어 `Beng` | <--> | 라틴어 `Latn` |
| 중국어 (간체) | `zh-Hans` | 중국어 간체 `Hans`| <--> | 라틴어 `Latn` |
| 중국어 (간체) | `zh-Hans` | 중국어 간체 `Hans`| <--> | 중국어 번체 `Hant`|
| 중국어 (번체) | `zh-Hant` | 중국어 번체 `Hant`| <--> | 라틴어 `Latn` |
| 중국어 (번체) | `zh-Hant` | 중국어 번체 `Hant`| <--> | 중국어 간체 `Hans` |
| 구자라트어 | `gu`  | 구자라트어 `Gujr` | --> | 라틴어 `Latn` |
| 히브리어 | `he` | 히브리어 `Hebr` | <--> | 라틴어 `Latn` |
| 힌디어 | `hi` | 데바나가리어 `Deva` | <--> | 라틴어 `Latn` |
| 일본어 | `ja` | 일본어 `Jpan` | <--> | 라틴어 `Latn` |
| 칸나다어 | `kn` | 칸나다어 `Knda` | --> | 라틴어 `Latn` |
| 말라얄람어 | `ml` | 말라얄람어 `Mlym` | --> | 라틴어 `Latn` |
| 마라티어 | `mr` | 데바나가리어 `Deva` | --> | 라틴어 `Latn` |
| 오리야어 | `or` | 오리야어 `Orya` | <--> | 라틴어 `Latn` |
| 펀잡어 | `pa` | 굴묵키어 `Guru`  | <--> | 라틴어 `Latn`  |
| 세르비아어(키릴 자모) | `sr-Cyrl` | 키릴 자모 `Cyrl`  | --> | 라틴어 `Latn` |
| 세르비아어(라틴 문자) | `sr-Latn` | 라틴어 `Latn` | --> | 키릴 자모 `Cyrl`|
| 타밀어 | `ta` | 타밀어 `Taml` | --> | 라틴어 `Latn` |
| 텔루구어 | `te` | 텔루구어 `Telu` | --> | 라틴어 `Latn` |
| 태국어 | `th` | 태국어 `Thai` | --> | 라틴어 `Latn` |

## <a name="dictionary"></a>Dictionary

사전은 조회 및 예제 방법을 사용하여 다음 언어를 영어로 또는 영어를 다음 언어로 나타내도록 지원합니다.

| 언어    | 언어 코드 |
|:----------- |:-------------:|
| 아프리칸스어      | `af`          |
| 아랍어       | `ar`          |
| 벵골어      | `bn`          |
| 보스니아어(라틴 문자)      | `bs`          |
| 불가리아어      | `bg`          |
| 카탈로니아어      | `ca`          |
| 중국어 간체      | `zh-Hans`          |
| 크로아티아어      | `hr`          |
| 체코어      | `cs`          |
| 덴마크어      | `da`          |
| 네덜란드어      | `nl`          |
| 에스토니아어      | `et`          |
| 핀란드어      | `fi`          |
| 프랑스어      | `fr`          |
| 독일어      | `de`          |
| 그리스어      | `el`          |
| 아이티 크리올      | `ht`          |
| 히브리어      | `he`          |
| 힌디어      | `hi`          |
| 몽 다오어      | `mww`          |
| 헝가리어      | `hu`          |
| 아이슬란드어    | `is`  |
| 인도네시아어      | `id`          |
| 이탈리아어      | `it`          |
| 일본어      | `ja`          |
| 스와힐리어      | `sw`          |
| 클링곤어      | `tlh`          |
| 한국어      | `ko`          |
| 라트비아어      | `lv`          |
| 리투아니아어      | `lt`          |
| 말레이어      | `ms`          |
| 몰타어      | `mt`          |
| 노르웨이어      | `nb`          |
| 페르시아어      | `fa`          |
| 폴란드어      | `pl`          |
| 포르투갈어(브라질)     | `pt-br`          |
| 루마니아어      | `ro`          |
| 러시아어      | `ru`          |
| 세르비아어(라틴 문자)      | `sr-Latn`          |
| 슬로바키아어     | `sk`          |
| 슬로베니아어      | `sl`          |
| 스페인어      | `es`          |
| 스웨덴어      | `sv`          |
| 타밀어      | `ta`          |
| 태국어      | `th`          |
| 터키어      | `tr`          |
| 우크라이나어      | `uk`          |
| 우르두어      | `ur`          |
| 베트남어      | `vi`          |
| 웨일스어      | `cy`          |

## <a name="detect"></a>Detect

Translator Text API는 번역 및 음에 사용할 수 있는 모든 언어를 검색 합니다.


## <a name="access-the-translator-text-api-language-list-programmatically"></a>Translator Text API 언어 목록에 프로그래밍 방식으로 액세스

언어 메서드를 사용하여 Translator Text API v3.0에 대해 지원되는 언어 목록을 검색할 수 있습니다. 영어 또는 지원되는 다른 언어의 언어 이름 뿐만 아니라 기능, 언어 코드별로 목록을 볼 수 있습니다. 이 목록은 새 언어를 사용할 수 있을 때 Microsoft Translator 서비스에서 자동으로 업데이트됩니다.

[언어 작업 참조 설명서 보기](reference/v3-0-languages.md)

## <a name="customization"></a>사용자 지정

[사용자 지정 변환기](https://aka.ms/CustomTranslator)를 사용 하 여 영어로 사용자 지정 하는 데 사용할 수 있는 언어는 다음과 같습니다.

| 언어    | 언어 코드 |
|:----------- |:-------------:|
| 아랍어       | `ar`          |
| 벵골어      | `bn`          |
| 보스니아어(라틴 문자)      | `bs`          |
| 불가리아어      | `bg`          |
| 중국어 간체      | `zh-Hans`          |
|중국어 번체|   `zh-Hant`   |
| 크로아티아어      | `hr`          |
| 체코어      | `cs`          |
| 덴마크어      | `da`          |
| 네덜란드어      | `nl`          |
| 영어    | `en`     |
| 에스토니아어      | `et`          |
| 핀란드어      | `fi`          |
| 프랑스어      | `fr`          |
| 독일어      | `de`          |
| 그리스어      | `el`          |
| 히브리어      | `he`          |
| 힌디어      | `hi`          |
| 헝가리어      | `hu`          |
| 아이슬란드어 | `is` |
| 인도네시아어|   `id`    |
| 아일랜드어 | `ga`  |
| 이탈리아어      | `it`          |
| 일본어      | `ja`          |
| 스와힐리어|    `sw`    |
| 한국어      | `ko`          |
| 라트비아어      | `lv`          |
| 리투아니아어      | `lt`          |
| 마다가스카르어| `mg`    |
| 마오리어| `mi`  |
| 노르웨이어      | `nb`          |
| 페르시아어      | `fa`          |
| 폴란드어      | `pl`          |
| 포르투갈어(브라질) | `pt-br` |
| 루마니아어      | `ro`          |
| 러시아어      | `ru`          |
| 사모아어|   `sm`    |
| 세르비아어(라틴 문자)      | `sr-Latn`          |
| 슬로바키아어     | `sk`          |
| 슬로베니아어      | `sl`          |
| 스페인어      | `es`          |
| 스웨덴어      | `sv`          |
| 태국어      | `th`          |
| 터키어      | `tr`          |
| 우크라이나어      | `uk`          |
| 베트남어      | `vi`          |
| 웨일스어 | `cy` |

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Microsoft Translator 웹 사이트에서 목록에 액세스

언어를 빠르게 확인하려는 경우 Microsoft Translator 웹 사이트는Translator Text 및 Speech API에서 지원하는 모든 언어를 표시합니다. 이 목록에는 언어 코드와 같은 개발자별 정보는 포함되지 않습니다.

[언어 목록을 참조하세요](https://www.microsoft.com/translator/languages.aspx).
