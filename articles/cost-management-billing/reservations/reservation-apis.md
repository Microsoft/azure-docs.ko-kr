---
title: Azure 예약 자동화용 API | Microsoft Docs
description: 프로그래밍 방식으로 예약 정보를 가져오는 데 사용할 수 있는 Azure API에 대해 알아봅니다.
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: cffb753d515c4d066c8ce52e7dacebe5a8f6f8ca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682640"
---
# <a name="apis-for-azure-reservation-automation"></a>Azure 예약 자동화용 API

Azure API를 사용하여 프로그래밍 방식으로 Azure 서비스 또는 소프트웨어 예약에 대한 사용자 조직의 정보를 가져옵니다.

## <a name="find-reservation-plans-to-buy"></a>구입할 예약 플랜 찾기

예약 권장 사항 API를 사용하여 조직의 사용량을 기준으로 구입할 예약 플랜에 대한 권장 사항을 가져옵니다. 자세한 내용은 [예약 권장 사항 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)를 참조하세요.

또한 소비 API 사용량 세부 정보를 사용하여 리소스 사용량을 분석할 수도 있습니다. 자세한 내용은 [사용량 세부 정보 - 청구 계정별 청구 기간 목록](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy)을 참조하세요. 일관되게 사용하는 Azure 리소스가 일반적으로 예약에 가장 적합한 후보입니다.

## <a name="buy-a-reservation"></a>예약 구입

REST API를 사용하여 Azure 예약 및 소프트웨어 플랜을 프로그래밍 방식으로 구매할 수 있습니다. 자세히 알아보려면 [예약 주문 - 구매 API](/rest/api/reserved-vm-instances/reservationorder/purchase)를 참조하세요.

다음은 REST API를 사용하여 구매하는 샘플 요청입니다.

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

본문 요청:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Azure Portal에서 예약을 구매할 수도 있습니다. 자세한 내용은 다음 문서를 참조하세요.

서비스 플랜:
- [가상 머신](../../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL 데이터베이스](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

소프트웨어 플랜:
- [SUSE Linux 소프트웨어](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>예약 가져오기

기업계약이 있는 Azure 고객(EA 고객)인 경우 [예약 인스턴스 가져오기 트랜잭션 요금 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)를 사용하여 조직이 구입한 예약을 가져올 수 있습니다. 다른 구독의 경우, API [예약 주문 - 목록](/rest/api/reserved-vm-instances/reservationorder/list)를 사용하여 구입했으며 보기 권한이 있는 예약 목록을 가져옵니다. 기본적으로 계정 소유자 또는 예약을 구입한 사람은 예약을 볼 수 있는 권한이 있습니다.

## <a name="see-reservation-usage"></a>예약 사용량 참조

EA 고객인 경우 조직의 예약이 사용되는 방식을 프로그래밍 방식으로 볼 수 있습니다. 자세한 내용은 [기업고객을 위한 예약 인스턴스 사용량 가져오기](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)를 참조하세요. 다른 구독의 경우 API [예약 요약 - 예약 주문 및 예약별 목록](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)을 사용합니다.

조직의 예약이 사용 중인지 확인한 경우:

- 조직이 만드는 가상 머신이 예약의 VM 크기와 일치하는지 확인합니다.
- 인스턴스 크기 유연성이 켜져 있는지 확인합니다. 자세한 내용은 [예약 관리 - 예약 VM 인스턴스에 대한 최적화 설정 변경](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)을 참조하세요.
- 보다 광범위하게 적용되도록 예약의 범위를 공유로 변경합니다. 자세한 내용은 [예약 관리 - 예약 범위 변경](manage-reserved-vm-instance.md#change-the-reservation-scope)을 참조하세요.
- 사용하지 않은 수량을 교환합니다. 자세한 내용은 [예약 관리](manage-reserved-vm-instance.md)를 참조하세요.

## <a name="give-access-to-reservations"></a>예약에 대한 액세스 권한 부여

[예약 - 작업 - 목록 API](/rest/api/reserved-vm-instances/reservationorder/list)를 사용하여 사용자에게 액세스 권한이 있는 모든 예약 목록을 가져옵니다. 프로그래밍 방식으로 예약에 대해 액세스 권한을 부여하려면 다음 문서 중 하나를 참조하세요.

- [RBAC 및 REST API를 사용하여 액세스 관리](../../role-based-access-control/role-assignments-rest.md)
- [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-powershell.md)
- [RBAC 및 Azure CLI를 사용하여 액세스 관리](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>예약 분할 또는 병합

한 예약 내에서 둘 이상의 리소스 인스턴스를 구입한 후에는 예약 내의 인스턴스를 다른 구독에 할당하는 것이 좋습니다. 동일한 청구 컨텍스트 내의 모든 구독에 적용되도록 예약 범위를 변경할 수 있습니다. 하지만 비용 관리 또는 예산 목적을 위해서는 범위를 "단일 구독"으로 유지하고 예약 인스턴스를 특정 구독에 할당할 수 있습니다.

예약을 분할하려면 API [예약 - 분할](/rest/api/reserved-vm-instances/reservation/split)을 사용합니다. PowerShell을 사용하여 예약을 분할할 수도 있습니다. 자세한 내용은 [예약 관리 - 예약을 두 개로 예약으로 분할](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations)을 참조하세요.

두 예약을 하나의 예약으로 병합하려면 API [예약 - 병합](/rest/api/reserved-vm-instances/reservation/merge)을 사용합니다.

## <a name="change-scope-for-a-reservation"></a>예약 범위 변경

예약 범위는 단일 구독, 단일 리소스 그룹 또는 청구 컨텍스트의 모든 구독일 수 있습니다. 범위를 단일 구독 또는 단일 리소스 그룹으로 설정하는 경우 예약은 선택한 구독에서 실행 중인 리소스와 일치됩니다. 구독 또는 리소스 그룹을 삭제하거나 이동하는 경우 예약이 활용되지 않습니다.  범위를 공유로 설정하는 경우 Azure에서 예약을 청구 컨텍스트 내 모든 구독에서 실행하는 리소스와 일치시킵니다. 청구 컨텍스트는 예약을 구입하는 데 사용되는 구독에 따라 달라집니다. 구매 시 범위를 선택하거나 구매 후 언제든지 범위를 변경할 수 있습니다. 자세한 내용은 [예약 관리 - 범위 변경](manage-reserved-vm-instance.md#change-the-reservation-scope)을 참조하세요.

범위를 프로그래밍 방식으로 변경하려면 API [예약 - 업데이트](/rest/api/reserved-vm-instances/reservation/update)를 사용합니다.

## <a name="learn-more"></a>자세한 정보

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [VM 예약 할인이 적용되는 방식 이해](../manage/understand-vm-reservation-charges.md)
- [SUSE Linux Enterprise 소프트웨어 요금제 할인이 적용되는 방식 이해](understand-suse-reservation-charges.md)
- [예약 할인이 적용되는 방식 이해](understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)
- [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)
