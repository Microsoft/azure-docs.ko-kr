---
title: Azure 구독 제안 변경 | Microsoft Docs
description: Azure 구독을 변경하고 Azure 계정 센터를 사용하여 다른 제안으로 전환하는 방법에 대해 알아봅니다.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: aae227b3-6d64-4550-a5b6-d359f53f0a59
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: banders
ms.openlocfilehash: bbdcbdc7ef288eeeb279c7e5e59baee492e1f292
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918791"
---
# <a name="change-your-azure-pay-as-you-go-subscription-to-a-different-offer"></a>다른 제안으로 Azure 종량제 구독 변경

[종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 고객인 경우 Azure 구독을 [계정 센터](https://account.windowsazure.com/Subscriptions)의 다른 제안으로 전환할 수 있습니다. 예를 들어 이 기능을 사용하여 [Visual Studio 구독자를 위한 월간 크레딧](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)을 활용할 수 있습니다. 

**평가판에서 업그레이드를 원하세요?** [종량제로 업그레이드](billing-upgrade-azure-subscription.md)를 참조하세요.

## <a name="whats-supported"></a>지원되는 내용:

| 원본 | 받는 사람 |
| --- | --- |
| Pay-As-You-Go |[종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| Pay-As-You-Go |[Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| Pay-As-You-Go |[Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| Pay-As-You-Go |[MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| Pay-As-You-Go |[Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| Pay-As-You-Go |[Visual Studio Enterprise(Bizspark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |

> [!NOTE]
> 다른 제품 변경 내용은 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
>
>

## <a name="switch-subscription-offer"></a>구독 제안 전환

> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Switch-to-a-different-Azure-offer/player]
>
>

1. [Azure 계정 센터](https://account.windowsazure.com/Subscriptions)에 로그인합니다.
1. 종량제 구독을 선택합니다.
1. **다른 제품으로 전환**을 클릭합니다. 종량제 사용자이고 최초 청구 기간에 완료한 경우에만 이 단추를 사용할 수 있습니다.

   ![페이지의 오른쪽에 있는 스위치 제품 단추를 확인합니다.](./media/billing-how-to-switch-azure-offer/switchbutton.png)
1. 구독을 전환할 수 있는 제안 목록에서 **원하는 제안**을 선택합니다. 이 목록은 계정이 연결된 멤버 자격에 따라 다릅니다. 사용 가능한 항목이 없는 경우 [전환할 수 있는 제품](#whats-supported) 목록을 확인하고 적합한 멤버 자격이 있는지 확인합니다. 

   ![전환하려는 제품을 선택합니다](./media/billing-how-to-switch-azure-offer/selectoffer.png)
1. 전환하려는 제안에 따라 전환의 영향에 대한 메모를 볼 수 있습니다. 계속하기 전에 이 목록을 꼼꼼히 살펴보고 지침을 따르세요.

   ![메모를 검토합니다.](./media/billing-how-to-switch-azure-offer/thingstonote.png)
1. 구독 이름을 바꿀 수 있습니다. 기본적으로 새 제안 이름으로 설정됩니다. 프로세스를 완료하려면 **제품 전환**을 클릭합니다.

   ![녹색 단추를 클릭합니다.](./media/billing-how-to-switch-azure-offer/confirmpage.png)
1. 성공! 이제 구독이 새 제안으로 전환되었습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-is-an-azure-offer"></a>Azure 제품이란?

Azure 제안은 사용자가 소유한 Azure 구독의 *유형*을 말합니다. 예를 들어 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/) 및 [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)는 모두 Azure 제품입니다. 각 제품은 서로 [조건](https://azure.microsoft.com/support/legal/offer-details/)이 다르며 몇 가지 특별 혜택이 있습니다. 구독의 제안은 계정 센터 구독 페이지에서 찾을 수 있습니다. 제품 이름을 클릭하면 자세한 내용을 볼 수 있습니다.

   ![자세한 내용을 보려면 계정 센터에서 제품 링크 클릭](./media/billing-how-to-switch-azure-offer/offerlink.png)

### <a name="why-dont-i-see-the-button"></a>단추가 표시되지 않는 이유는 무엇입니까?

다음과 같은 경우 **다른 제품으로 전환** 단추가 표시되지 않을 수 있습니다.

* [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 사용자가 아닙니다. 현재 종량제 구독만 다른 제안으로 전환할 수 있습니다.
  * [무료 평가판](https://azure.microsoft.com/free/)을 사용 중인 경우 [종량제로 업그레이드](billing-upgrade-azure-subscription.md)하는 방법을 알아보세요.
  * 다른 구독에서 제안을 전환하려면 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
* 아직 최초 청구 기간 상태이며 최초 청구 기간이 끝날 때까지 기다려야 제품을 전환할 수 있습니다.

### <a name="why-do-i-see-there-are-no-offers-available-in-your-region-or-country-at-this-time"></a>"귀하의 지역 또는 국가에는 현재 사용 가능한 제안이 없습니다."가 표시되는 이유는 무엇입니까?

* 제품 전환이 불가능할 수 있습니다. [전환할 수 있는 사용 가능한 제품 목록](#whats-supported)을 확인하고 Visual Studio 또는 Bizspark를 사용하여 올바른 혜택을 활성화했는지 확인합니다.
* 일부 제품의 경우 일부 국가에서만 사용 가능할 수 있습니다.

### <a name="what-does-switching-azure-offers-do-to-my-service-and-billing"></a>Azure 제품을 전환할 경우 내 서비스 및 요금 청구는 어떻게 되나요?

계정 센터에서 Azure 제품을 전환할 경우 다음과 같은 상황이 발생합니다.

#### <a name="no-service-downtime"></a>서비스 가동 중지 시간이 없습니다.

구독과 연결된 사용자에게 서비스 가동 중지 시간이 없습니다. 그러나 전환하는 제안에 제한 사항이 있을 수 있습니다. 예를 들어 일부 제안은 프로덕션 사용이 금지되므로 프로덕션 리소스를 다른 구독으로 이동해야 합니다.

#### <a name="quota-increases-are-reset"></a>할당량 증가가 다시 설정됩니다.

제품을 전환할 경우 [기본 제한을 초과하는 제한 또는 할당량 증가](../azure-supportability/resource-manager-core-quotas-request.md)는 다시 설정됩니다. 기본 제한을 초과하는 더 많은 리소스가 있더라도 서비스 작동이 중단되지 않습니다. 예를 들어 구독에서 200개 코어를 사용하고 제안을 전환하면 코어 할당량이 기본값인 20개 코어로 다시 설정됩니다. 200개 코어를 사용하는 VM은 영향을 받지 않으며 계속 실행됩니다. 그렇지만 다른 할당량 증가 요청을 지정하지 않는 경우 더 많은 코어를 프로비저전할 수 없습니다.

#### <a name="billing"></a>결제

전환한 날 모든 미결제 요금에 대한 송장이 생성됩니다. 그런 다음, 새 제안의 가격 책정 조건에 따라 구독 요금이 청구됩니다. 구독 요금 청구일은 제안을 변경한 날짜로 변경됩니다. 제품 변경 전 사용 및 요금 청구 데이터는 보존되지 않으므로 전환하기 전에 복사본을 다운로드하는 것이 좋습니다.

### <a name="can-i-migrate-from-pay-as-you-go-to-cloud-solution-providerhttpspartnermicrosoftcomsolutionscloud-reseller-overview-csp-or-enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement-ea"></a>종량제에서 [클라우드 솔루션 공급자](https://partner.microsoft.com/Solutions/cloud-reseller-overview)(CSP) 또는 [기업 계약](https://azure.microsoft.com/pricing/enterprise-agreement/)(EA)으로 마이그레이션할 수 있나요?

* CSP에 마이그레이션하려면 [CSP에 Azure 종량제 구독 마이그레이션](https://docs.microsoft.com/azure/cloud-solution-provider/migration/migration-from-payg-to-csp)을 참조하세요.
* EA로 전환하려면 등록 관리자에게 계정을 EA에 추가해 달라고 요청해야 합니다. 초대 전자 메일의 지침에 따라 구독을 EA 등록 아래로 이동하세요. 자세한 내용은 EA 포털에서 [기존 계정 연결](https://ea.azure.com/helpdocs/associateExistingAccount)을 참조하세요.

### <a name="can-i-migrate-data-and-services-to-a-new-subscription"></a>데이터 및 서비스를 새 구독으로 마이그레이션할 수 있나요?

* 리소스를 새 구독으로 바로 마이그레이션할 수 있으며 자세한 방법은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요.
* Azure 구독과 그 안에 있는 모든 것의 소유권을 다른 사람에게 양도하려면 [Azure 구독의 소유권 이전](billing-subscription-transfer.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
