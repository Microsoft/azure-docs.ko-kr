---
title: 가상 머신 제품 가격 책정 - Azure Marketplace
description: 가상 머신 제품의 가격을 지정하는 세 가지 방법에 대해 설명합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393822"
---
# <a name="pricing-for-virtual-machine-offers"></a>가상 머신 제품 가격 책정

> [!NOTE]
> Cloud 파트너 포털 API는 파트너 센터와 통합되었으며 파트너 센터에서 계속 작동합니다. 전환 시 사소한 변경 내용이 적용됩니다. [Cloud 파트너 포털 API 참조](./cloud-partner-portal-api-overview.md)에 나열된 변경 내용을 검토하여 파트너 센터로 전환한 후에도 코드가 계속 작동하는지 확인합니다. CPP API는 파트너 센터로 전환하기 전에 이미 통합된 기존 제품에만 사용해야 합니다. 새 제품은 파트너 센터 제출 API를 사용해야 합니다.

가상 머신 제품의 가격은 사용자 지정 코어 가격, 코어당 가격, 스프레드시트 가격의 세 가지 방식으로 지정할 수 있습니다.

## <a name="customized-core-pricing"></a>사용자 지정 코어 가격

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
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```

## <a name="per-core-pricing"></a>코어당 가격

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

## <a name="spreadsheet-pricing"></a>스프레드시트 가격

게시자는 임시 스토리지 위치에 가격 스프레드시트를 업로드한 후에 다른 파일 아티팩트와 같은 방식으로 요청에 URI를 포함할 수도 있습니다. 그러면 스프레드시트가 업로드된 후 변환되어 지정된 가격표를 평가하며, 마지막으로 가격 정보가 적용되어 제품이 업데이트됩니다. 제품에 대한 후속 GET 요청에서는 URI 스프레드시트와 해당 지역에 대해 평가된 가격이 반환됩니다.

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

## <a name="new-core-sizes-added-on-722019"></a>2019년 7월 2일에 추가된 새 코어 크기

VM 게시자는 2019년 7월 2일에 새 Azure 가상 머신 크기(코어 수 기반)에 적용되는 새 가격이 추가되었다는 알림을 받았습니다.  새 가격은 코어 크기 10, 44, 48, 60, 120, 208, 416에 적용됩니다.  기존 VM 제품의 경우 현재 가격에 따라 해당 코어 크기의 새 가격이 자동으로 계산되었습니다. 게시자는 2019년 8월 1일까지 추가 가격을 검토하고 원하는 대로 변경할 수 있습니다.  이 날짜 이후에도 게시자가 다시 게시하지 않은 경우에는 새 코어 크기에 대해 자동으로 계산된 가격이 적용됩니다.

## <a name="regions"></a>영역

다음 표에는 사용자 지정 코어 가격을 지정할 수 있는 지역 및 해당하는 통화 코드가 나와 있습니다.

| **지역** | **이름**             | **통화 코드** |
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
| CA         | Canada               | CAD               |
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
| MY         | 말레이시아             | MYR               |
| MT         | 몰타                | EUR               |
| MX         | 멕시코               | MXN               |
| ME         | 몬테네그로 공화국           | EUR               |
| MA         | 모로코              | MAD               |
| NL         | 네덜란드          | EUR               |
| NZ         | 뉴질랜드          | NZD               |
| NG         | 나이지리아              | NGN               |
| MK         | 북마케도니아      | MKD               |
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
| GB         | United Kingdom       | GBP               |
| US         | 미국        | USD               |
| UY         | 우루과이              | UYU               |
| VE         | 베네수엘라            | USD               |
|  |  |  |
