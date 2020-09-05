---
title: Azure Billing Enterprise API
description: Azure 기업 고객이 사용량 데이터를 프로그래밍 방식으로 끌어올 수 있게 하는 보고 API에 대해 알아봅니다.
author: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b78b046e2991fa4ebfb575e4f6a1cc900df0d298
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686907"
---
# <a name="overview-of-reporting-apis-for-enterprise-customers"></a>기업 고객을 위한 보고 API 개요

> [!Note]
> Microsoft는 더 이상 Azure Billing - Enterprise Reporting API를 업데이트하지 않습니다. 대신 [Azure Consumption](/rest/api/consumption) API를 사용해야 합니다.

Azure 기업 고객은 보고 API를 통해 사용량 및 청구 데이터를 기본 데이터 분석 도구로 프로그래밍 방식으로 끌어올 수 있습니다. 기업 고객은 Azure와 [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)를 체결하여 현금 약정 금액을 협상하고 Azure 리소스에 대한 사용자 지정 가격에 액세스할 수 있습니다.

API에 필요한 모든 날짜 및 시간 매개 변수는 결합된 UTC(Unified 협정 세계시) 값으로 표시되어야 합니다. API에서 반환된 값은 UTC 형식으로 표시됩니다.

## <a name="enabling-data-access-to-the-api"></a>API에 대한 데이터 액세스 사용
* **API 키 생성 또는 검색** - Enterprise 포털에 로그인한 다음, 보고서 > 사용량 다운로드 > API 액세스 키로 이동하여 API 키를 생성하거나 검색합니다.
* **API에서 키 전달** - API 키는 인증 및 권한 부여를 호출할 때마다 전달되어야 합니다. HTTP 헤더에 있어야 하는 속성은 다음과 같습니다.

|요청 헤더 키 | 값|
|-|-|
|권한 부여| **bearer {API_KEY}** 형식의 값을 지정합니다. <br/> 예: bearer eyr....09|

## <a name="consumption-based-apis"></a>사용량 기반 API
Swagger 엔드포인트는 [AutoRest](https://github.com/Azure/AutoRest) 또는 [Swagger CodeGen](https://swagger.io/swagger-codegen/)을 사용하여 손쉬운 API 검사와 클라이언트 SDK 생성 기능을 사용할 수 있게 하는 아래에서 설명하는 API에 대해 [여기](https://consumption.azure.com/swagger/ui/index)에서 제공됩니다. 2014년 5월 1일부터 시작하는 데이터는 이 API를 통해 사용할 수 있습니다.

* **잔액 및 요약** - [잔액 및 요약 API](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)는 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과 요금에 대한 월별 정보 요약을 제공합니다.

* **사용량 세부 정보** - [사용량 세부 정보 API](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)는 등록에 따른 사용량과 예상 요금의 일별 분석 결과를 제공합니다. 이 결과에는 인스턴스, 측정기 및 부서에 대한 정보도 포함 됩니다. API는 청구 기간 또는 지정된 시작 날짜와 종료 날짜를 기준으로 쿼리할 수 있습니다.

* **Marketplace 저장소 요금** - [Marketplace 저장소 요금 API](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)는 지정된 청구 기간 또는 시작 날짜 및 종료 날짜(1회 요금은 포함되지 않음)에 대한 일별 사용량 기반 Marketplace 요금 분석 결과를 반환합니다.

* **가격표** - [가격표 API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)는 지정된 등록 및 청구 기간에 대한 각 측정기에 적용할 수 있는 가격을 제공합니다.

* **예약 인스턴스 세부 정보** - [예약 인스턴스 사용량 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)는 예약 인스턴스 구매 사용량을 반환합니다. [예약 인스턴스 요금 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)에는 청구 트랜잭션이 표시됩니다.

## <a name="data-freshness"></a>데이터 새로 고침
Etag는 위의 모든 API의 응답에서 반환됩니다. Etag의 변경 내용은 데이터를 새로 고칠 것을 나타냅니다.  동일한 매개 변수를 사용하는 동일한 API에 대한 후속 호출에서는 http 요청의 헤더에서 "If-None-Match" 키를 가진 캡처된 Etag를 전달합니다. 데이터가 더 이상 새로 고쳐지지 않고 데이터가 반환되지 않는 경우 응답 상태 코드는 "NotModified"입니다. Etag가 변경될 때마다 API는 필수 기간 동안 전체 데이터 세트를 반환합니다.

## <a name="helper-apis"></a>도우미 API
 **청구 기간 나열** - [청구 기간 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)는 지정된 등록에 대한 사용량 데이터를 역방향 시간 순서로 표시한 청구 기간 목록을 반환합니다. 각 기간에는 4개의 데이터 집합(잔액 요약, 사용량 세부 정보, Marketplace 요금 및 가격표)에 대한 API 경로를 가리키는 속성이 포함되어 있습니다.


## <a name="api-response-codes"></a>API 응답 코드   
|응답 상태 코드|메시지|Description|
|-|-|-|
|200| 확인|오류 없음|
|400| 잘못된 요청| 잘못된 매개 변수 - 날짜 범위, EA 숫자 등|
|401| 권한 없음| API 키를 찾을 수 없음, 유효하지 않음, 만료됨 등|
|404| 사용할 수 없음| 보고서 엔드포인트를 찾을 수 없음|
|429 | TooManyRequests | 요청이 제한되었습니다. <code>x-ms-ratelimit-microsoft.consumption-retry-after</code> 헤더에 지정된 시간 동안 기다린 후 다시 시도하세요.|
|500| 서버 오류| 예기치 않은 오류 처리 요청|
| 503 | ServiceUnavailable | 서비스를 일시적으로 사용할 수 없습니다. <code>Retry-After</code> 헤더에 지정된 시간 동안 기다린 후 다시 시도하세요.|
