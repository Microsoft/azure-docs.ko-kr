---
title: 사전 구매를 사용하여 Azure Databricks 비용 최적화
description: 요금을 절감하기 위해 예약된 용량으로 Azure Databricks 요금을 선불할 수 있는 방법에 대해 알아봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 699b3d9c9799b9d3a4ec148cbf395b212715f536
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686091"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>사전 구매를 사용하여 Azure Databricks 비용 최적화

1년 또는 3년 동안 DBCU(Azure Databricks 커밋 단위)를 사전 구매하는 경우 DBU(Azure Databricks 단위) 비용을 절감할 수 있습니다. 구매 기간 중에 언제든지 미리 구매한 DBCU를 사용할 수 있습니다. VM과 달리 사전 구매한 단위는 시간 단위로 만료되지 않으며 구매 기간 동안 언제든지 사용할 수 있습니다.

모든 Azure Databricks는 사전 구매한 DBU에서 자동으로 차감됩니다. 사전 구매 할인을 받기 위해 DBU 사용량에 대해 Azure Databricks 작업 영역에 사전 구매 플랜을 다시 배포하거나 할당할 필요가 없습니다.

사전 구매 할인은 DBU 사용량에만 적용됩니다. 컴퓨팅, 스토리지 및 네트워킹 등의 기타 요금은 별도로 청구됩니다.

## <a name="determine-the-right-size-to-buy"></a>구매할 올바른 크기 결정

Databricks 사전 구매는 모든 Databricks 워크로드 및 계층에 적용됩니다. 사전 구매는 선불 Databricks 커밋 단위의 풀로 간주할 수 있습니다. 워크로드 또는 계층에 관계없이 사용량은 풀에서 차감됩니다. 사용량은 다음과 같은 비율로 차감됩니다.

| **작업** | **DBU 애플리케이션 비율 - 표준 계층** | **DBU 애플리케이션 비율 - 프리미엄 계층** |
| --- | --- | --- |
| 데이터 분석 | 0.4 | 0.55 |
| 데이터 엔지니어링 | 0.15 | 0.30 |
| 데이터 엔지니어링 라이트 | 0.07 | 0.22 |

예를 들어 데이터 분석 수량 - 표준 계층을 사용하는 경우 사전 구매한 Databricks 커밋 단위는 0.4 단위로 차감됩니다.

구매하기 전에 다른 워크로드 및 계층에 사용되는 총 DBU 수량을 계산합니다. 앞의 비율을 사용하여 DBCU로 표준화한 다음, 다음 1년 또는 3년 동안 총 사용량의 프로젝션을 실행합니다.

## <a name="purchase-databricks-commit-units"></a>Databricks 커밋 단위 구매

[Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)에서 Databricks 플랜을 구매할 수 있습니다. 예약된 용량을 구매하려면 하나 이상의 Enterprise 구독의 소유자 역할이 있어야 합니다.

- 하나 이상의 기업계약(제품 번호: MS-AZR-0017P or MS-AZR-0148P)이나 Microsoft 고객 계약 또는 종량제 요금의 개별 구독(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다.
- EA 구독의 경우 EA 포털에서 예약 인스턴스 추가 옵션을 활성화해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com/)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.

**구매하려면 다음을 수행합니다.**

1. [Azure 포털](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D)로 이동합니다.
1. 구독을 선택합니다. 예약 용량의 요금을 지불하는 데 사용되는 구독을 선택하려면 **구독** 목록을 사용합니다. 구독의 결제 방법에는 예약 용량에 대한 선불 비용이 청구됩니다. 요금은 등록의 금액 약정 잔액에서 차감되거나 초과분에 대한 요금으로 청구됩니다.
1. 범위를 선택합니다. 구독 범위를 선택하려면 **범위** 목록을 사용합니다.
    - **단일 리소스 그룹 범위** - 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **단일 구독 범위** - 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
    - **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다.
1. 구매하려는 Azure Databricks 커밋 단위 수를 선택하고 구매를 완료합니다.


![Azure Portal에서 Azure Databricks 구매를 보여주는 예제](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>범위 및 소유권 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

- 예약 범위 업데이트
- 역할 기반 액세스

Databricks 커밋 단위 사전 구매를 분할하거나 병합할 수 없습니다. 예약을 관리하는 방법에 대한 자세한 내용은 [구매 후 예약 관리](manage-reserved-vm-instance.md)를 참조하세요.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

Databricks 사전 구매 플랜에 대해서는 취소 및 교환이 지원되지 않습니다. 모든 구매는 최종입니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure Databricks 사전 구매 DBCU 할인이 적용되는 방식 이해](reservation-discount-databricks.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
