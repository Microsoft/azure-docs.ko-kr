---
title: 가상 머신 제품 가격 책정 | Microsoft Docs
description: 가상 머신 제품의 가격을 지정하는 세 가지 방법에 대해 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a029477dfd8046863ebfe34cd839562a0b1f3d87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094461"
---
<a name="pricing-for-virtual-machine-offers"></a>가상 머신 제품 가격 책정
==================================

가상 머신 제품의 가격은 사용자 지정 코어 가격, 코어당 가격, 스프레드시트 가격의 세 가지 방식으로 지정할 수 있습니다.


<a name="customized-core-pricing"></a>사용자 지정 코어 가격
-----------------------

각 지역과 코어의 조합별로 가격을 책정합니다. 정의의 **virtualMachinePricing**/**regionPrices** 섹션에서 모든 판매 지역을 지정해야 합니다.  각 [지역](#regions)의 현재 통화 코드를 요청에 사용합니다.  다음 예제에서는 이러한 요구 사항을 보여 줍니다.

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>코어당 가격
----------------

이 방식의 경우 게시자가 SKU용 가격 하나를 USD 단위로 지정하며, 그러면 기타 모든 가격은 자동으로 생성됩니다. 요청의 **single** 매개 변수에서 코어당 가격을 지정합니다.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>스프레드시트 가격
-------------------

게시자는 임시 저장소 위치에 가격 스프레드시트를 업로드한 후에 다른 파일 아티팩트와 같은 방식으로 요청에 URI를 포함할 수도 있습니다. 그러면 스프레드시트가 업로드된 후 변환되어 지정된 가격표를 평가하며, 마지막으로 가격 정보가 적용되어 제품이 업데이트됩니다. 제품에 대한 후속 GET 요청에서는 URI 스프레드시트와 해당 지역에 대해 평가된 가격이 반환됩니다.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>영역
-------

다음 표에는 사용자 지정 코어 가격을 지정할 수 있는 지역 및 해당하는 통화 코드가 나와 있습니다.

| **지역** | **Name**             | **통화 코드** |
|------------|----------------------|-------------------|
| DZ         | 알제리              | DZD               |
| AR         | 아르헨티나            | ARS               |
| AU         | 오스트레일리아            | AUD               |
| AT         | 오스트리아              | EUR               |
| BH         | 바레인              | BHD               |
| BY         | 벨로루시              | RUB               |
| BE         | 벨기에              | EUR               |
| BR         | 브라질               | USD               |
| BG         | 불가리아             | BGN               |
| CA         | 캐나다               | CAD               |
| CL         | 칠레                | CLP               |
| CO         | 콜롬비아             | COP               |
| CR         | 코스타리카           | CRC               |
| HR         | 크로아티아              | HRK               |
| CY         | 키프로스               | EUR               |
| CZ         | 체코       | CZK               |
| DK         | 덴마크              | DKK               |
| DO         | 도미니카 공화국   | USD               |
| EC         | 에콰도르              | USD               |
| EG         | 이집트                | EGP               |
| SV         | 엘살바도르          | USD               |
| EE         | 에스토니아              | EUR               |
| FI         | 핀란드              | EUR               |
| FR         | 프랑스               | EUR               |
| DE         | 독일              | EUR               |
| GR         | 그리스               | EUR               |
| GT         | 과테말라            | GTQ               |
| HK         | 홍콩 특별 행정구        | HKD               |
| HU         | 헝가리              | HUF               |
| IS         | 아이슬란드              | ISK               |
| IN         | 인도                | INR               |
| ID         | 인도네시아            | IDR               |
| IE         | 아일랜드              | EUR               |
| IL         | 이스라엘               | ILS               |
| IT         | 이탈리아                | EUR               |
| JP         | 일본                | JPY               |
| JO         | 요르단               | JOD               |
| KZ         | 카자흐스탄           | KZT               |
| KE         | 케냐                | KES               |
| KR         | 한국                | KRW               |
| KW         | 쿠웨이트               | KWD               |
| LV         | 라트비아               | EUR               |
| LI         | 리히텐슈타인        | CHF               |
| LT         | 리투아니아            | EUR               |
| LU         | 룩셈부르크           | EUR               |
| MK         | 북마케도니아      | MKD               |
| MY         | 말레이시아             | MYR               |
| MT         | 몰타                | EUR               |
| MX         | 멕시코               | MXN               |
| ME         | 몬테네그로           | EUR               |
| MA         | 모로코              | MAD               |
| NL         | 네덜란드          | EUR               |
| NZ         | 뉴질랜드          | NZD               |
| NG         | 나이지리아              | NGN               |
| 아니요         | 노르웨이               | NOK               |
| OM         | 오만                 | OMR               |
| PK         | 파키스탄             | PKR               |
| PA         | 파나마               | USD               |
| PY         | 파라과이             | PYG               |
| PE         | 페루                 | PEN               |
| PH         | 필리핀          | PHP               |
| PL         | 폴란드               | PLN               |
| PT         | 포르투갈             | EUR               |
| PR         | 푸에르토리코          | USD               |
| QA         | 카타르                | QAR               |
| RO         | 루마니아              | RON               |
| RU         | 러시아               | RUB               |
| SA         | 사우디아라비아         | SAR               |
| RS         | 세르비아               | RSD               |
| SG         | 싱가포르            | SGD               |
| SK         | 슬로바키아             | EUR               |
| SI         | 슬로베니아             | EUR               |
| ZA         | 남아프리카         | ZAR               |
| ES         | 스페인                | EUR               |
| LK         | 스리랑카            | USD               |
| SE         | 스웨덴               | SEK               |
| CH         | 스위스          | CHF               |
| TW         | 대만               | TWD               |
| TH         | 태국             | THB               |
| TT         | 트리니다드 토바고  | TTD               |
| TN         | 튀니지              | TND               |
| TR         | 터키               | TRY               |
| UA         | 우크라이나              | UAH               |
| AE         | 아랍에미리트 | EUR               |
| GB         | 영국       | GBP               |
| US         | 미국        | USD               |
| UY         | 우루과이              | UYU               |
| VE         | 베네수엘라            | USD               |
|  |  |  |
