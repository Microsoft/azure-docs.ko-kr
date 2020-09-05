---
title: Azure 리소스에 대한 예약 보기 | Microsoft Docs
description: Azure Portal에서 Azure 예약을 보는 방법을 알아봅니다. API, PowerShell, CLI 및 Power BI를 사용하여 예약 및 사용률을 봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: ec91d0997717d536c3b47ce7b276f75b21d1baa1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681654"
---
# <a name="view-azure-reservations"></a>Azure 예약 보기

Azure Portal에서 구매한 예약을 보고 관리할 수 있습니다.

## <a name="permissions-to-view-a-reservation"></a>예약을 볼 수 있는 권한

예약을 보거나 관리하려면 해당 예약에 대한 읽기 권한자 또는 소유자 권한이 있어야 합니다. 기본적으로 예약을 구매하면 사용자와 계정 관리자에게 예약 주문 및 예약에 대한 소유자 역할이 자동으로 할당됩니다. 다른 사용자가 예약을 볼 수 있도록 하려면 예약 주문 또는 예약에서 **소유자** 또는 **Reader**로 추가해야 합니다. 예약 대금 청구용으로 제공된 구독에 추가된 다른 사용자는 예약에 자동으로 추가되지 않습니다. 

자세한 내용은 [예약을 관리할 수 있는 사용자 추가 또는 변경](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)을 참조하세요.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Azure Portal에서 예약 및 사용률 보기

소유자 또는 Reader로 예약을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. [예약](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)으로 이동합니다.
3. 목록에 소유자 또는 읽기 권한자 역할을 보유한 예약이 모두 표시됩니다. 각 예약에는 마지막으로 알려진 사용률이 표시됩니다.
4. 사용률을 클릭하여 사용률 기록 및 세부 정보를 봅니다. 아래 비디오에서 세부 정보를 참조하세요.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>API, PowerShell, CLI를 사용하여 예약 및 사용률 가져오기

다음 리소스를 사용하여 모든 예약 목록 가져오기
- [API: 예약 주문 - 목록](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: 예약 주문 - 목록](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: 예약 주문 - 목록](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

예약 인스턴스 사용량 API를 사용하여 [예약 사용률](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)을 가져올 수도 있습니다. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Power BI에서 예약 및 사용률 보기

Power BI 사용자에게는 두 가지 옵션이 있습니다.
- 콘텐츠 팩: 예약 구매 및 사용률 데이터는 [Consumption Insights Power BI 콘텐츠 팩](/power-bi/desktop-connect-azure-cost-management)에서 볼 수 있습니다. 이 콘텐츠 팩을 사용하여 원하는 보고서를 만드세요. 
- 비용 관리 앱: 추가로 사용자 지정할 수 있는 사전 생성 보고서에 [Cost Management 앱](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)을 사용합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 예약 관리](manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
- [CSP 구독의 예약 사용량 이해](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
