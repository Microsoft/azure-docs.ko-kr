---
title: Microsoft 고객 계약 가격표의 용어 - Azure
description: Azure에서 Microsoft 고객 계약 청구서의 사용량 및 청구서를 읽고 이해하는 방법을 알아봅니다.
author: bandersmsft
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2020
ms.author: banders
ms.openlocfilehash: 46d0f259d30f9864c371f8bd3dd01f593004c808
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133578"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Microsoft 고객 계약 가격표의 용어

이 문서는 Microsoft 고객 계약에 대한 Azure 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

청구 프로필 소유자, 기여자, 독자 또는 청구서 관리자인 경우 Azure Portal에서 조직의 가격표를 다운로드할 수 있습니다. [조직의 가격 책정 보기 및 다운로드](ea-pricing.md)를 참조하세요.

## <a name="terms-and-descriptions-in-your-price-sheet"></a>가격표의 용어 및 설명

다음 섹션에서는 Microsoft 고객 계약 가격표에 표시되는 중요한 용어에 대해 설명합니다.

| **필드 이름**   | **설명**   |
| --- | --- |
| basePrice  | 고객이 로그인한 시기의 시장 가격 또는 로그인한 후 서비스 측정기가 시작될 때 시장 가격입니다.   |
| billingAccountId  | 청구 계정의 고유 식별자입니다.   |
| billingAccountName  | 청구 계정의 이름입니다.  |
| billingCurrency | 요금이 부과되는 통화 |
| billingProfileId  | 청구 프로필에 대한 고유 식별자입니다.   |
| billingProfileName  | 청구서를 받도록 설정된 청구 프로필의 이름입니다. 가격표의 가격은 이 청구 프로필에 연결됩니다. |
| currency | 모든 가격이 반영되는 통화입니다. |
| 할인 | 해당하는 경우 졸업 계층, 무료 계층, 포함된 수량 또는 협의된 할인에 대해 제공되는 가격 할인입니다. 백분율로 표시됩니다. |
| effectiveEndDate  | 유효 가격의 종료 날짜입니다. |
| effectiveStartDate  | 가격이 적용되는 시작 날짜입니다. |
| includedQuantity | 증분 요금 없이 고객이 사용할 수 있는 특정 서비스의 수량입니다. |
| marketPrice | 지정된 서비스에 대한 현재 일반 시장 가격입니다. |
| meterId  | 측정기의 고유 식별자입니다. |
| meterCategory  | 측정기에 대한 분류 범주의 이름입니다. 예: _클라우드 서비스_, _네트워킹_ 등. |
| meterName  | 측정기의 이름입니다. 측정기는 Azure 서비스의 배포 가능한 리소스를 나타냅니다. |
| meterSubCategory  | 측정기 하위 분류 범주의 이름입니다.  |
| meterType  |  측정기 유형의 이름입니다. |
| meterRegion  | 서비스에 대한 측정기를 사용할 수 있는 지역의 이름입니다. 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다.    |
| Product  | 요금이 발생하는 제품의 이름입니다. 예: 기본 SQL DB 또는 표준 SQL DB  |
| productId  | 측정기가 사용된 제품의 고유 식별자입니다. |
| productOrderName  | 구매한 제품 플랜의 이름입니다. |
| serviceFamily  | Azure 서비스의 특정 유형입니다. 예: 컴퓨팅, 분석, 보안 |
| tierMinimumUnits  | 가격이 정의된 계층 범위의 하한값을 정의합니다. 예를 들어 범위가 0에서 100인 경우 tierMinimumUnits는 0이 됩니다.  |
| unitOfMeasure  | 서비스 청구를 위한 측정 단위를 식별합니다. 예를 들어 컴퓨팅 서비스는 시간당 청구됩니다. |
| unitPrice  | 측정기 및 제품 주문 이름에 특정한 청구 시기의 단위당 가격입니다(유효한 혼합 가격이 아님).  참고: 단가는 계층 간 차등 가격이 있는 서비스의 경우 사용 내역 다운로드의 유효 가격과 동일하지 않습니다.  다중 계층 가격이 적용되는 서비스의 경우, 유효 가격은 계층 전체의 혼합률이며 계층별 단가를 나타내지 않습니다. 혼합 가격 또는 유효 가격은 (각 계층이 특정 단가를 가지는) 여러 계층에 걸친 소비 수량의 순 가격입니다. |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [조직의 가격 책정 보기 및 다운로드](ea-pricing.md)
