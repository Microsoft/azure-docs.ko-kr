---
title: 일반적인 명명 된 엔터티
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211400"
---
## <a name="general-entity-types"></a>일반 엔터티 형식:

### <a name="person"></a>Person

텍스트의 사람 이름을 인식 합니다.

언어:
* 공개 미리 보기: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian``Spanish``Swedish``Turkish`

| 하위 형식 이름 | Description                                                      | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------------------------|---------------------------------------|
| 해당 없음          | 인식할 수 있는 사용자 이름 (예: `Bill Gates``Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
작업 유형 또는 개인이 보유 한 역할입니다.

언어:
* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                                                                | 모델 버전부터 사용 가능 |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | 작업 유형 (예: `civil engineer`, `salesperson`, `chef`, `librarian`, `nursing aide` | `2020-02-01`                           |

### <a name="location"></a>위치

자연 스러운 랜드마크, 구조, 지리적 기능 및 지정 학적 엔터티.

언어:

* 공개 미리 보기: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian``Spanish``Swedish``Turkish`

| 하위 형식 이름              | Description                                                                              | 모델 버전부터 사용 가능 |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음                       | 위치 (예: `Atlantic Ocean`, `library`, `Eiffel Tower`, `Statue of Liberty`  | `2019-10-01`                           |
| 지정 학적 Entity (GPE)-영어만| 도시, 국가, 예 `Seattle`, `Pennsylvania`, `South Africa`, `Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>조직  

인식 된 조직, 회사, 기관 및 기타 사용자 그룹. 예를 들어 회사, 정치적 그룹, 음악 밴드, 스포츠 클럽, 정부 기관, 공공 단체 등이 있습니다. Nationalities 및 religions는이 엔터티 형식에 포함 되지 않습니다. 

언어: 

* 공개 미리 보기: `Arabic`, `Czech`, `Chinese-Simplified`, `Danish`, `Dutch`, `English`, `Finnish`, `French`, `German`, `Hungarian`, `Italian`, `Japanese`, `Korean`, `Norwegian (Bokmål)`, `Polish`, `Portuguese (Portugal)`, `Portuguese (Brazil)`, `Russian``Spanish``Swedish``Turkish`

| 하위 형식 이름 | Description                                                                                             | 모델 버전부터 사용 가능 |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | 조직 (예: `Microsoft`, `NASA`, `National Oceanic and Atmospheric Administration`,`VOA` | `2019-10-01`                           |

### <a name="event"></a>행사  

기록, 소셜 및 자연스럽 게 발생 하는 이벤트입니다.  

언어: 

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                                            | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | `wedding`, `hurricane`, `car accident`, `solar eclipse`, `American Revolution`와 같은 이벤트 | `2020-02-01`                           |

### <a name="product"></a>Product  

다양 한 범주의 물리적 개체  

언어: 

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                                                        | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | 예를 들어 `Microsoft Surface laptop`, `sunglasses`, `motorcycle`, `bag`, `Xbox` | `2020-02-01`                           |
| 컴퓨팅    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>기술  

기능 또는 전문 지식을 설명 하는 엔터티입니다.  

언어: 

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                                                 | 모델 버전부터 사용 가능 |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| 해당 없음          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>전화 번호

전화 번호 (미국 전화 번호만). 

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                    | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------|----------------------------------------|
| 해당 없음          | 미국 전화 번호 (예: `(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Email

메일 주소입니다. 

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                      | 모델 버전부터 사용 가능 |
|--------------|--------------------------------------------------|----------------------------------------|
| 해당 없음          | 전자 메일 주소 (예: `support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>URL

인터넷 Url.

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                                          | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------------------|----------------------------------------|
| 해당 없음          | 웹 사이트에 대 한 Url (예: `https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP 주소

인터넷 프로토콜 주소

언어:

* 공개 미리 보기: `English`

| 하위 형식 이름 | Description                              | 모델 버전부터 사용 가능 |
|--------------|------------------------------------------|----------------------------------------|
| 해당 없음          | 예 `10.0.0.101` 네트워크 주소 | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

날짜 및 시간 엔터티입니다. 

* 모델 버전 `2019-10-01`부터 사용 가능

언어:

* 공개 미리 보기: `Chinese-Simplified`, `English`, `French`, `German` 및 `Spanish`

| 하위 형식 이름    | 예                     |
|-------------|------------------------------|
| 해당 없음         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| 설정 | `every Saturday`         |

###  <a name="quantity"></a>수량

숫자 및 숫자 수량. 

* 모델 버전 `2019-10-01`부터 사용 가능

언어:

* 공개 미리 보기: `Chinese-Simplified`, `English`, `French`, `German` 및 `Spanish`

| 하위 형식 이름    | 예                     |
|-------------|------------------------------|
| Number         | `6`, `six`                   |
| 백분율  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Age         | `90 day old`, `30 years old` |
| 통화    | `$10.99`, `€30.00`           |
| 차원   | `10 miles`, `40 cm`          |
| 온도 | `32 degrees`, `10°C`         |
