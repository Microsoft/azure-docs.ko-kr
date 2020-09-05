---
title: Azure Reservations 관리
description: Azure Reservations를 관리하는 방법을 알아봅니다. 예약 범위를 변경하고, 예약을 분할하고, 예약 사용을 최적화하는 단계를 알아봅니다.
ms.service: cost-management-billing
ms.subservice: reservations
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: f67cbb70d7064b894b9042bba42f48e4fb6a6b5e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684782"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure 리소스에 대한 예약 관리

Azure 예약을 구입하면 다른 구독에 예약을 적용하거나, 예약을 관리할 수 있는 사용자를 변경하거나, 예약의 범위를 변경해야 할 수 있습니다. 또한 예약을 두 개로 분할하여 구입한 일부 인스턴스를 다른 구독에 적용할 수도 있습니다.

Azure Reserved Virtual Machine Instances를 구입한 경우 예약에 대한 최적화 설정을 변경할 수 있습니다. 예약 할인을 동일한 시리즈의 VM에 적용하거나, 특정 VM 크기에 대한 데이터 센터 용량을 예약할 수 있습니다. 그리고 예약이 완전히 사용되도록 예약을 최적화해야 합니다.

*예약을 관리하는 데 필요한 권한은 구독 권한과는 다릅니다.*

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>예약 주문 및 예약

예약을 구매하면, **예약 주문** 및 **예약**이라는 두 가지 개체가 생성됩니다.

구매 시 예약 주문에 하나의 예약이 포함됩니다. 분할, 병합, 부분 환불 또는 교환 등의 작업은 **예약 주문**에 새 예약을 생성합니다.

예약 주문을 보려면 **Reservations**로 이동하고 예약을 선택한 다음, **예약 주문 ID**를 클릭합니다.

![예약 주문 ID를 보여주는 예약 주문 세부 정보의 예 ](./media/manage-reserved-vm-instance/reservation-order-details.png)

예약은 예약 주문에서 권한을 상속합니다.

## <a name="change-the-reservation-scope"></a>예약 범위 변경

 예약 할인은 예약과 일치하고 예약 범위에서 실행되는 가상 머신, SQL 데이터베이스, Azure Cosmos DB 또는 기타 리소스에 적용됩니다. 청구 컨텍스트는 예약을 구입하는 데 사용되는 구독에 따라 달라집니다.

예약의 범위를 업데이트하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성**을 선택합니다.
5. 범위를 변경합니다.

공유 범위에서 단일 범위로 변경하는 경우, 자신이 소유자인 구독만 선택할 수 있습니다. 동일한 청구 컨텍스트 내에서 예약된 구독만을 선택할 수 있습니다.

범위는 종량제 요금을 사용하는 개별 구독(MS-AZR-0003P 또는 MS-AZR-0023P 제안), 엔터프라이즈 제안 MS-AZR-0017P 또는 MS-AZR-0148P 또는 CSP 구독 유형에만 적용됩니다.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>예약을 관리할 수 있는 사용자 추가 또는 변경

예약 주문 또는 예약에 대한 역할에 사용자를 추가하여 예약 관리를 위임할 수 있습니다. 기본적으로 예약 주문을 수행한 사용자 및 계정 관리자에게는 예약 주문 및 예약에 대한 소유자 역할이 있습니다.

예약 할인을 받는 *구독과 별도로* 예약 주문 및 예약에 대한 액세스를 관리할 수 있습니다. 다른 사용자에게 예약 주문 또는 예약을 관리할 수 있는 권한을 부여해도 구독을 관리할 수 있는 권한은 부여되지 않습니다. 마찬가지로, 다른 사용자에게 예약 범위 내에서 구독을 관리하는 권한을 부여하더라도 예약 주문 또는 예약을 관리하는 권한은 부여되지 않습니다.

교환 또는 환불을 수행하려면 예약 주문에 액세스할 수 있는 사용자여야 합니다. 다른 사용자에게 권한을 부여할 때는 예약이 아닌 예약 주문에 권한을 부여하는 것이 가장 좋습니다.

예약에 대한 액세스 관리를 위임하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택하여 액세스할 수 있는 예약을 나열합니다.
3. 다른 사용자에게 액세스 권한을 위임하려는 예약을 선택합니다.
4. **액세스 제어(IAM)** 를 선택합니다.
5. **역할 할당 추가** > **역할** > **소유자**를 선택합니다. 또는 제한된 액세스 권한을 부여하려는 경우 다른 역할을 선택합니다.
6. 소유자로 추가할 사용자의 메일 주소를 입력합니다.
7. 사용자를 선택한 다음 **저장**을 선택합니다.

## <a name="split-a-single-reservation-into-two-reservations"></a>단일 예약을 두 개의 예약으로 분할

 한 예약 내에서 둘 이상의 리소스 인스턴스를 구입한 후에는 예약 내의 인스턴스를 다른 구독에 할당하는 것이 좋습니다. 기본적으로 모든 인스턴스에는 단일 구독 또는 공유 중 하나의 범위가 지정됩니다. 예를 들어 10개의 예약 인스턴스를 구입하고, 범위를 구독 A로 지정했습니다. 이제 7개의 예약 범위를 구독 A로 변경하고 남은 3개를 구독 B로 변경하려고 합니다. 예약을 분할하면 세분화된 범위 관리를 위해 인스턴스를 배포할 수 있습니다. 공유 범위를 선택하여 구독에 대한 할당을 간소화할 수 있습니다. 하지만 비용 관리 또는 예산 목적으로 인해 특정 구독에 수량을 할당할 수 있습니다.

 PowerShell, CLI 또는 API를 통해 예약을 두 개로 분할할 수 있습니다.

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell을 사용하여 예약 분할

1. 다음 명령을 실행하여 예약 주문 ID를 가져옵니다.

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 예약의 세부 정보를 가져옵니다.

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 예약을 두 개로 분할하고 인스턴스를 배포합니다.

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 다음 명령을 실행하여 범위를 업데이트할 수 있습니다.

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [Azure Reservations의 셀프 서비스 교환 및 환불](exchange-and-refund-azure-reservations.md)을 참조하세요.

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Reserved VM Instances에 대한 최적화 설정 변경

 Reserved VM Instance를 구매하는 경우 인스턴스 크기 유연성 또는 용량 우선 순위를 선택합니다. 인스턴스 크기 유연성은 동일한 [VM 크기 그룹](https://aka.ms/RIVMGroups)의 다른 VM에 예약 할인을 적용합니다. 용량 우선 순위는 배포를 위해 데이터 센터 용량에서 우선됩니다. 이 옵션을 사용하면 필요할 때 VM 인스턴스를 보다 확실히 시작할 수 있게 됩니다.

기본적으로 예약의 범위를 공유할 경우 인스턴스 크기 유연성이 켜집니다. 데이터 센터 용량은 VM 배포에서 우선적으로 고려되지 않습니다.

범위가 단일인 예약의 경우 VM 인스턴스 크기 유연성 대신 용량 우선 순위에 대한 예약을 최적화할 수 있습니다.

예약에 대한 최적화 설정을 업데이트하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성**을 선택합니다.  
  ![구성 항목을 보여주는 예제](./media/manage-reserved-vm-instance/add-product03.png)
5. **다음에 맞게 최적화** 설정을 변경합니다.  
  ![최적화 설정을 보여주는 예제](./media/manage-reserved-vm-instance/instance-size-flexibility-option.png)

## <a name="optimize-reservation-use"></a>예약 사용 최적화

Azure 예약 절감은 리소스를 지속적으로 사용하는 경우에만 발생합니다. 예약 구매 시, 1년 또는 3년 동안 기본적으로 100% 가능한 리소스 사용에 대한 비용을 지불합니다. 최대로 사용하고 절감할 수 있도록 예약을 최대화해보세오. 다음 섹션에서는 예약을 모니터링하고 사용을 최적화하는 방법을 설명합니다.

### <a name="view-reservation-use-in-the-azure-portal"></a>Azure Portal에서 예약 사용 보기

예약 사용량을 보는 한 가지 방법은 Azure Portal입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > [**Reservations**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)를 선택하고 예약에 대한 **사용률(%)** 을 적어둡니다.  
  ![예약 목록을 보여주는 이미지](./media/manage-reserved-vm-instance/reservation-list.png)
3. 예약을 선택합니다.
4. 시간별 예약 사용 추세를 검토합니다.  
  ![예약 사용을 보여주는 이미지 ](./media/manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>API를 사용하여 예약 사용 보기

EA(기업계약) 고객인 경우 조직에서 예약을 사용하는 방법을 프로그래밍 방식으로 볼 수 있습니다. 사용 현황 데이터를 통해 사용하지 않는 예약을 알 수 있습니다. 예약 요금을 검토할 때는 데이터가 실제 비용과 분할 상환 비용으로 구분된다는 점에 유의하세요. 실제 비용은 월별 요금 청구를 조정하는 데이터를 제공합니다. 여기에는 예약 구매 비용 및 예약 적용 세부 정보도 포함됩니다. 분할 상환 비용은 예약 사용에 대한 실효 가격이 비례 배분된 것을 제외한 실제 비용과 같습니다. 사용하지 않은 예약 시간은 분할상환 비용 데이터에 표시됩니다. EA 고객의 사용 현황 데이터에 대한 자세한 내용은 [기업 계약 예약 비용 및 사용량 가져오기](understand-reserved-instance-usage-ea.md)를 참조하세요.

다른 구독 유형의 경우 API [예약 요약 - 예약 주문 및 예약별 목록](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)을 사용하세요.

### <a name="optimize-your-reservation"></a>예약 최적화

조직의 예약이 충분히 사용되지 않는 경우:

- 조직에서 생성한 가상 머신이 예약에 해당하는 VM 크기와 일치하는지 확인합니다.
- 인스턴스 크기 유연성이 켜져 있는지 확인합니다. 자세한 내용은 [예약 관리 - 예약 VM 인스턴스에 대한 최적화 설정 변경](#change-optimize-setting-for-reserved-vm-instances)을 참조하세요.
- 예약이 보다 광범위하게 적용되도록 범위를 _공유_로 변경합니다. 자세한 내용은 [예약 범위 변경](#change-the-reservation-scope)을 참조하세요.
- 사용하지 않는 수량을 교환합니다. 자세한 내용은 [취소 및 교환](#cancel-exchange-or-refund-reservations)을 참조하세요.


## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)

서비스 계획 구입:
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 컴퓨팅 리소스 요금 선결제](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Cosmos DB 예약된 용량을 사용하여 Azure Cosmos DB 리소스 요금 선결제](../../cosmos-db/cosmos-db-reserved-capacity.md)

소프트웨어 요금제 구입:
- [Azure Reservations에서 Red Hat 소프트웨어 요금제에 대한 선불](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reservations에서 SUSE 소프트웨어 요금제에 대한 선불](../../virtual-machines/linux/prepay-suse-software-charges.md)

할인 및 사용량 이해:
- [VM 예약 할인이 적용되는 방식 이해](../manage/understand-vm-reservation-charges.md)
- [Red Hat Enterprise Linux 소프트웨어 요금제 할인이 적용되는 방식 이해](understand-rhel-reservation-charges.md)
- [SUSE Linux Enterprise 소프트웨어 요금제 할인이 적용되는 방식 이해](understand-suse-reservation-charges.md)
- [예약 할인이 적용되는 방식 이해](understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](reserved-instance-windows-software-costs.md)
