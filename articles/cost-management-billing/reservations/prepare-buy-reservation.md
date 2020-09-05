---
title: Azure 예약 구매
description: Azure 예약을 구매하는 데 도움이 되는 중요 사항에 대해 알아보세요.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 211e06695d3cabef8cfeb0a7bbf581835819345c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686210"
---
# <a name="buy-a-reservation"></a>예약 구입

Azure Reservations는 여러 Azure 리소스의 1년 또는 3년 플랜에 따라 비용을 절감하는 데 도움이 됩니다. 예약 구입 약정을 시작하기 전에 구매 준비를 위해 다음 섹션을 검토해야 합니다.

## <a name="who-can-buy-a-reservation"></a>예약을 구입할 수 있는 사람

플랜을 구매하려면 엔터프라이즈(MS-AZR-0017P 또는 MS-AZR-0148P), 종량제 구독(MS-AZR-0003P 또는 MS-AZR-0023P) 또는 Microsoft 고객 계약 구독의 구독 소유자 역할이 있어야 합니다. 클라우드 솔루션 공급자는 Azure Portal 또는  [파트너 센터](/partner-center/azure-reservations) 를 사용하여 Azure 예약을 구매할 수 있습니다.

EA(기업계약) 고객은 EA Portal에서 **예약 인스턴스 추가** 옵션을 해제하여 EA 관리자만 구매 가능하도록 제한할 수 있습니다. 예약을 구매하려면 EA 관리자가 하나 이상의 EA 구독에 대한 구독 소유자여야 합니다. 이 옵션은 중앙의 팀이 다양한 비용 센터의 예약을 대신 구매하도록 하려는 기업에 유용합니다. 구매 후에는 중앙의 팀이 예약에 비용 센터 소유자를 추가할 수 있습니다. 그러면 소유자는 예약의 범위를 구독으로 지정할 수 있습니다. 중앙의 팀은 예약을 구매하는 구독 소유자 액세스 권한이 필요 없습니다.

예약 할인은 엔터프라이즈, CSP(클라우드 솔루션 공급자), Microsoft 고객 계약 및 종량제 요금을 사용하는 개별 플랜을 통해 구매한 구독과 연결된 리소스에만 적용됩니다.

## <a name="scope-reservations"></a>예약 범위 지정

예약의 범위를 구독 또는 리소스 그룹으로 지정할 수 있습니다. 예약의 범위를 지정하면 예약 할인이 적용되는 위치가 선택됩니다. 예약의 범위를 리소스 그룹으로 지정하면 구독 전체가 아닌 리소스 그룹에만 예약 할인이 적용됩니다.

### <a name="reservation-scoping-options"></a>예약 범위 지정 옵션

요구 사항에 따라 예약 범위를 지정하는 세 가지 옵션이 있습니다.

- **단일 리소스 그룹 범위** — 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
- **단일 구독 범위** — 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
- **공유 범위** - 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다.
    - 기업계약 고객의 경우 청구 컨텍스트는 등록입니다. 예약 공유 범위에는 등록 시 여러 Active Directory 테넌트가 포함됩니다.
    - Microsoft 고객 계약 고객의 경우 청구 범위는 청구 프로필입니다.
    - 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

사용 요금에 예약 할인을 적용할 때 Azure는 다음과 같은 순서로 예약을 처리합니다.

1. 리소스 그룹으로 범위가 지정된 예약
2. 단일 범위 예약
3. 공유 범위 예약

예약을 구매한 후 언제든지 범위를 업데이트할 수 있습니다. 업데이트하려면 예약으로 이동하고, **구성**을 클릭하여 예약 범위를 다시 지정합니다. 예약 범위 재지정은 상거래 트랜잭션이 아닙니다. 따라서 예약 기간이 변경되지 않았습니다. 범위 업데이트에 대한 자세한 내용은 [예약을 구매한 후 범위 업데이트](manage-reserved-vm-instance.md#change-the-reservation-scope)를 참조하세요.

![예약 범위 변경을 보여주는 예제](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>할인된 구독 및 제안 유형

예약 할인은 다음과 같은 적격 구독 및 제품 유형에 적용됩니다.

- 기업계약(제품 번호: MS-AZR-0017P 또는 MS-AZR-0148P)
- Microsoft 고객 계약 구독.
- 종량제 요금을 사용한 개별 계획(제품 번호: MS-AZR-0003P 또는 MS-AZR-0023P)
- CSP 구독

다른 유형의 구독에서 실행되는 리소스는 예약 할인을 받지 못합니다.

## <a name="purchase-reservations"></a>예약 구매

Azure Portal, API, PowerShell, CLI에서 예약을 구매할 수 있습니다. 예약 구매를 준비할 때 적용되는 다음 문서를 읽어보세요.

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disk Storage](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [소프트웨어 플랜](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [스토리지](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL 데이터베이스](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Database for PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [가상 머신](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>월별 결제로 예약 구매

월별 결제로 예약 비용을 지불할 수 있습니다. 전체 금액을 지불하는 선불 구매와 달리, 월별 결제 옵션은 예약의 총 비용을 기간에 대해 매월 균등하게 나눕니다. 선불과 월별 예약의 총 비용은 동일하며, 매달 지불하기로 선택하면 추가 요금이 청구되지 않습니다.

MCA(Microsoft 고객 계약)를 통해 예약을 구매한 경우 월별 결제 금액은 현지 통화에 대한 이번 달 시장 환율에 따라 달라질 수 있습니다.

월별 결제는 다음에 사용할 수 없습니다. Databricks, SUSE Linux 예약, Red Hat 플랜 및 Azure Red Hat OpenShift 라이선스.

### <a name="view-payments-made"></a>결제 내역 보기

지불한 금액은 API, 사용 현황 데이터를 사용하여 그리고 비용 분석에서 볼 수 있습니다. 매월 지불한 예약의 경우, 사용 현황 데이터 및 Reservation Charges API에 빈도 값이 **되풀이**로 표시됩니다. 선불 예약의 경우 이 값이 **한 번**으로 표시됩니다.

비용 분석의 기본 보기에 월별 구매가 표시됩니다. 모든 구매를 보려면 **구매** 필터를 **요금 유형**에 적용하고 **되풀이** 필터를 **빈도**에 적용합니다. 예약만 보려면 **예약**에 필터를 적용합니다.

![비용 분석에서 예약 구매 비용을 보여주는 예](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>취소 및 환불

다른 예약과 마찬가지로 월별 청구를 통해 구매한 예약을 환불하거나 교환할 수 있습니다. 

매월 지불하는 예약을 교환하는 경우에는, 새 구매의 총 수명 비용이 반환되는 예약에 대해 취소되는 남은 지불액보다 커야 합니다. 교환에 대한 다른 제한이나 수수료는 없습니다. 선불로 지불한 예약을 교환하여 매월 청구되는 새로운 예약을 구매할 수 있습니다. 단, 새 예약의 수명 값이 반환되는 예약에 대해 비례 배분된 값보다 커야 합니다.

매달 지불되는 예약을 취소하면 취소된 향후 지불금은 $50,000 USD 환불 제한에 도달하게 됩니다.

교환 및 환불에 대한 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="reservation-notifications"></a>예약 알림

Azure 구독 요금을 결제하는 방법에 따라 조직의 다음 사용자에게 이메일로 예약 알림이 전송됩니다. 다음을 비롯한 다양한 이벤트에 대한 알림이 전송됩니다. 

- Purchase
- 곧 만료되는 예약
- 만료
- 갱신
- 취소
- 범위 변경

EA 구독을 사용하는 고객의 경우:

- 알림이 EA 알림 연락처로만 전송됩니다.
- RBAC(IAM) 권한을 사용하여 예약에 추가된 사용자에게는 이메일 알림이 전달되지 않습니다.

개별 구독을 사용하는 고객의 경우:

- 구매자에게 구매 알림이 전달됩니다.
- 구매 시 구독 청구 계정 소유자에게 구매 알림이 전달됩니다.
- 그 외의 모든 알림은 계정 소유자에게 전달됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 예약 관리](manage-reserved-vm-instance.md)
