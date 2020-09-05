---
title: 파트너용 Azure Cost Management 시작
description: 이 문서에서는 파트너가 Azure Cost Management 기능을 사용하는 방법과 파트너가 자사의 고객을 위해 Cost Management 액세스를 사용하도록 설정하는 방법을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/04/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 6dfced457f6840294700fb998c93cf2ab993024c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683541"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>파트너용 Azure Cost Management 시작

Azure Cost Management는 고객을 Microsoft 고객 계약에 온보딩하고 [Azure 플랜을 구매](/partner-center/purchase-azure-plan)한 직접 파트너에게 기본적으로 제공됩니다. 이 문서에서는 파트너가 [Azure Cost Management](../index.yml) 기능을 사용하여 Azure 플랜의 구독에 대한 비용을 보는 방법을 설명합니다. 파트너가 고객을 위해 소매 요금으로 Cost Management 액세스가 가능하도록 설정하는 방법에 대해서도 설명합니다.

직접 파트너 및 간접 공급자의 경우 전역 관리자 및 관리자 에이전트는 파트너 테넌트의 Cost Management에 액세스하고 송장이 발부된 가격으로 비용을 관리할 수 있습니다.

재판매인 및 고객은 고객 테넌트의 Cost Management에 액세스하여 비용을 계산하고 소매 요금으로 표시하는 구독 비용을 볼 수 있습니다. 그러나 비용을 보려면 고객 테넌트의 구독에 대한 RBAC 액세스 권한이 있어야 합니다. 비용 표시 정책은 고객 테넌트에 대해 공급자가 사용하도록 설정해야 합니다.

고객은 CSP 파트너가 사용하도록 설정한 경우 Cost Management 기능을 사용할 수 있습니다.

CSP 파트너는 Cost Management를 사용하여:

- 청구된 비용을 파악하고 비용을 고객, 구독, 리소스 그룹 및 서비스에 연결합니다.
- 고객, 구독, 리소스 그룹, 리소스, 미터, 서비스 및 기타 여러 차원의 비용을 분석하는 기능을 통해 [비용 분석](quick-acm-cost-analysis.md)에서 Azure 비용을 직관적으로 확인합니다.
- 비용 분석에서 PEC(파트너 획득 크레딧)가 적용된 리소스 비용을 봅니다.
- 비용이 예산을 초과할 경우 프로그래밍 방식 [예산](tutorial-acm-create-budgets.md) 및 경고를 사용하여 알림 및 자동화를 설정합니다.
- Cost Management 데이터에 대한 고객 액세스를 제공하는 Azure Resource Manager 정책을 사용하도록 설정합니다. 그러면 고객이 [종량제 요금](https://azure.microsoft.com/pricing/calculator/)을 사용하여 구독에 대한 소비 비용 데이터를 볼 수 있습니다.
- 종량제 구독이 있는 스토리지 Blob으로 비용 및 사용 현황 데이터를 내보냅니다.

다음은 모든 고객의 비용을 보여주는 예입니다.

[![모든 고객의 비용을 보여주는 예](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

다음은 단일 고객의 비용을 보여주는 예입니다.

[![단일 고객의 비용을 보여주는 예](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Azure Cost Management에서 사용 가능한 모든 기능은 REST API로도 사용할 수 있습니다. API를 사용하여 비용 관리 작업을 자동화할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

파트너의 경우 Azure 플랜에 있는 구독에만 Azure Cost Management를 기본적으로 사용할 수 있습니다.

Azure Portal에서 Azure Cost Management를 사용하도록 설정하려면, Microsoft 고객 계약에 대한 고객의 동의를 확인하고(고객을 대신하여) 고객을 Azure 플랜으로 전환해야 합니다. Azure 플랜으로 전환된 구독의 비용만 Azure Cost Management에서 사용할 수 있습니다.

Azure Cost Management에는 청구 계정이나 구독에 대한 읽기 권한이 필요합니다.

청구 계정에 대해 Azure Cost Management에 대한 액세스를 설정하고 할당하는 방법에 대한 자세한 내용은 [사용자 역할 및 권한 할당](/partner-center/permissions-overview)을 참조하세요. **글로벌 관리자**와 **관리 에이전트** 역할은 청구 계정의 비용을 관리할 수 있습니다.

구독 범위에서 Azure Cost Management에 액세스하기 위해 구독에 대한 RBAC 액세스 권한이 있는 사용자는 소매(종량제) 요금으로 비용을 볼 수 있습니다. 단, [고객 테넌트에 대한 비용 표시 여부 정책](#enable-the-policy-to-view-azure-usage-charges)을 사용하도록 설정해야 합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](understand-cost-mgt-data.md)를 참조하세요.

## <a name="how-cost-management-uses-scopes"></a>Cost Management에서 범위를 사용하는 방법

범위는 청구 데이터를 관리하고, 결제 관련 역할이 있으며, 청구서를 살펴보고, 일반 계정 관리를 수행하는 곳입니다. RBAC를 사용하는 청구 및 계정 역할은 리소스 관리에 사용되는 범위와 별도로 관리됩니다. 액세스 제어의 차이점을 포함하여 별도 범위의 의도를 명확하게 구분하기 위해 이러한 범위를 각각 청구 범위 및 RBAC 범위라고 합니다.

청구 범위 및 RBAC 범위를 이해하고 비용 관리가 범위와 작동하는 방식을 이해하려면 [범위 이해 및 작업](understand-work-scopes.md)을 참조하세요.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>파트너 테넌트 청구 범위로 비용 관리

고객을 Microsoft 고객 계약에 온보딩한 후에는 테넌트에서 다음과 같은 _청구 범위_를 사용할 수 있습니다. 범위를 사용하여 Cost Management에서 비용을 관리합니다.

### <a name="billing-account-scope"></a>청구 계정 범위

청구 계정 범위를 사용하여 모든 고객 및 청구 프로필에 대한 세전 비용을 살펴볼 수 있습니다. 청구서 비용은 Microsoft 고객 계약의 고객 소비량 기반 제품에 대해서만 표시됩니다. 하지만 Microsoft 고객 계약 및 CSP 제품 모두에서 고객의 구매 기반 제품에 대한 청구서 비용이 표시됩니다. 현재, 범위에서 비용을 나타내는 기본 통화는 미국 달러입니다. 범위에 설정된 예산 역시 USD입니다.

다양한 고객 청구 통화에 관계없이, 파트너는 청구 계정 범위를 사용하여 고객, 구독, 리소스 및 리소스 그룹에서 USD 단위로 예산을 설정하고 비용을 관리합니다.

또한 파트너는 비용 분석 보기에서 고객의 특정 청구 통화로 비용을 필터링합니다. **실제 비용** 목록을 선택하면 지원되는 고객 청구 통화 단위로 비용을 볼 수 있습니다.

![통화에 대한 실제 비용 선택을 보여주는 예](./media/get-started-partners/actual-cost-selector.png)

청구 범위에서 [분할상환 비용 보기](quick-acm-cost-analysis.md#customize-cost-views)를 사용하면 예약 기간 동안 예약 인스턴스 분할상환 비용을 볼 수 있습니다.

### <a name="billing-profile-scope"></a>청구 프로필 범위

청구 프로필 범위를 사용하면 청구서에 포함된 모든 제품 및 구독에 대해 모든 고객의 청구 통화로 세전 비용을 볼 수 있습니다. **InvoiceID** 필터를 사용하여 특정 청구서의 청구 프로필에서 비용을 필터링할 수 있습니다. 필터는 특정 청구서의 소비 및 제품 구매 비용을 보여줍니다. 청구서에서 특정 고객의 비용을 필터링하여 세전 비용을 볼 수도 있습니다.

Microsoft 고객 계약에 고객을 온보딩한 후에는 Microsoft 고객 계약에 따라 해당 고객에 대한 모든 제품(소비, 구매 및 자격)의 모든 요금이 포함된 청구서를 받게 됩니다. 동일한 통화로 청구되는 경우, 해당 청구서에는 SaaS, Azure Marketplace와 같은 구매한 제품 및 자격에 대한 요금과 CSP 제품에 아직 있는 고객에 대한 예약도 포함됩니다.

고객 청구서에 대한 요금 조정이 수월하도록, 청구 프로필 범위를 사용하면 고객의 청구서에 발생하는 모든 비용을 볼 수 있습니다. 청구서와 마찬가지로 범위에는 신규 Microsoft 고객 계약의 모든 고객에 대한 비용이 표시됩니다. 범위에는 현재 CSP 제품에 아직 있는 고객 자격 제품에 대한 모든 요금도 표시됩니다.

청구 프로필 및 청구 계정 범위는 Azure Marketplace 및 예약 구매와 같은 구매 기반 제품과 자격에 대한 요금을 보여주는 유일하게 적용이 가능한 범위입니다.

청구 프로필은 청구서에 포함되는 구독을 정의합니다. 청구 프로필은 기업계약 등록과 기능적으로 동일합니다. 청구 프로필은 청구서가 생성되는 범위입니다.

현재, 고객의 청구 통화는 청구 프로필 범위에서 비용을 보는 경우 기본 통화입니다. 청구 프로필 범위에 설정된 예산은 청구 통화 단위입니다.

파트너는 범위를 사용하여 청구서를 조정할 수 있습니다. 그리고 범위를 사용하여 다음 항목에 대한 청구 통화로 예산을 설정합니다.

- 필터링된 특정 청구서
- Customer
- Subscription
- Resource group
- 리소스
- Azure 서비스
- 미터
- ResellerMPNID

### <a name="customer-scope"></a>고객 범위

파트너는 범위를 사용하여 Microsoft 고객 계약에 온보딩된 고객과 연결된 비용을 관리합니다. 범위를 통해 파트너는 특정 고객에 대한 세전 비용을 청구 통화 단위로 볼 수 있습니다. 특정 구독, 리소스 그룹 또는 리소스에 대한 세전 비용을 필터링할 수도 있습니다.

고객 범위에 현재 CSP 제품에 있는 고객은 포함되지 않습니다. 범위에는 Microsoft 고객 계약이 있는 고객만 포함됩니다. 현재 CSP 제품 고객에 대한 Azure 사용량이 아닌 자격 비용은 고객 필터를 적용할 때 청구 계정 및 청구 프로필 범위에서 사용할 수 있습니다. 이 범위에서 설정된 예산은 청구 통화 단위입니다.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Cost Management에서 청구 범위에 대한 파트너 액세스

**글로벌 관리자**와 **관리 에이전트** 역할이 있는 사용자만 파트너의 Azure 테넌트에서 직접 청구 계정, 청구 프로필 및 고객에 대한 비용을 관리하고 볼 수 있습니다. 파트너 센터 역할에 대한 자세한 내용 [사용자 역할 및 권한 할당](/partner-center/permissions-overview)을 참조하세요.

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>고객 테넌트 구독에 대한 비용 관리 사용

고객이 Microsoft 고객 계약에 온보딩된 후에 파트너가 Cost Management에 대한 액세스가 가능하도록 설정할 수 있습니다. 그런 다음, 파트너는 고객이 종량제 소매 요금으로 계산된 Azure 사용 서비스에 대한 비용을 볼 수 있는 정책을 사용하도록 설정할 수 있습니다. RBAC 구독 및 리소스 그룹 범위에서 소비된 사용량에 대한 비용이 고객의 청구 통화로 표시됩니다.

파트너가 비용 표시 여부에 대한 정책을 사용하도록 설정하면, 구독에 대해 Azure Resource Manager 액세스 권한이 있는 사용자가 종량제 요금으로 비용을 관리하고 분석할 수 있습니다. 사실상, Azure 구독에 대해 적절한 RBAC 액세스 권한이 있는 재판매인과 고객은 비용을 볼 수 있습니다.

정책에 관계없이 공급자의 전역 관리자와 관리자 에이전트는 구독 및 리소스 그룹에 대한 액세스 권한이 있는 경우 구독 비용을 볼 수 있습니다.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Azure 사용량 요금을 보는 정책 사용

정책을 보고 업데이트하려면 **관리 에이전트** 그룹의 구성원이어야 합니다. 다음 정보를 사용하여 고객이 Azure 사용량 요금을 볼 수 있도록 허용하는 정책을 사용하도록 설정합니다.

Azure Portal에서 파트너 테넌트에 로그인하고 **Cost Management + 청구**를 선택합니다. 청구 범위 영역에서 관련 청구 범위를 선택한 다음, **고객**을 선택합니다. 고객 목록은 청구 계정과 연결되어 있습니다.

고객 목록에서 비용을 보도록 허용할 고객을 선택합니다.

[![Cost Management에서 고객 선택](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

**설정**에서 **정책**을 선택합니다.

선택한 고객의 구독과 연결된 **Azure 사용량** 요금에 대한 현재 비용 표시 여부 정책이 표시됩니다.
![고객이 종량제 요금을 보도록 허용하는 정책](./media/get-started-partners/cost-management-billing-policies.png)

정책이 **아니요**로 설정되면 고객과 연결된 구독 사용자가 Azure Cost Management를 사용할 수 없습니다. 파트너가 활성화하지 않으면 모든 구독 사용자에 대해 비용 표시 여부 정책이 기본적으로 비활성화됩니다.

비용 정책이 **예**로 설정되면, 고객 테넌트에 연결된 구독 사용자가 종량제 요금으로 사용량 요금을 볼 수 있습니다.

비용 표시 여부 정책을 사용하도록 설정하면, 구독 사용량이 있는 모든 서비스는 종량제 요금으로 비용을 표시합니다. 예약 사용은 실제 및 분할상환 비용에 대한 요금이 0으로 표시됩니다. 구매 및 자격은 특정 구독에 연결되지 않습니다. 따라서 구매는 구독 범위에 표시되지 않습니다. 또한 직접 파트너 또는 간접 공급자의 글로벌 관리자/관리 에이전트는 [고객 업데이트 API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer)를 사용하여 각 고객의 비용 표시 정책을 대규모로 설정할 수 있습니다.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>고객 테넌트의 구독 비용 보기

구독에 대한 비용을 보려면 고객의 Azure 테넌트에서 **Cost Management + 청구**를 엽니다. 필요한 구독 **비용 분석**을 선택하여 비용 검토를 시작합니다. 고객 테넌트에서 각 구독에 대한 소비 비용을 개별적으로 볼 수 있습니다.

[![고객으로 비용 분석 보기](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

비용 분석, 예산 및 경고는 종량제 요금 기반 비용으로 구독 및 리소스 그룹 RBAC 범위에 대해 사용할 수 있습니다.

RBAC 범위에서 예약된 인스턴스에 대한 분할상환 보기와 실제 비용은 요금이 0으로 표시됩니다. 예약된 인스턴스 비용은 구매가 완료된 청구 범위에만 표시됩니다.

보기에 표시되는 비용을 계산하는 데 사용되는 소매 요금은 모든 고객에 대해 Azure 가격 책정 계산기에 표시된 가격과 동일합니다. 표시된 비용에는 파트너 적립 크레딧, 계층 할인 및 글로벌 서비스 할인과 같은 파트너가 가질 수 있는 할인이나 크레딧이 포함되지 않습니다.

## <a name="analyze-costs-in-cost-analysis"></a>비용 분석에서 비용 분석

파트너 테넌트의 청구 범위에 액세스할 수 있는 파트너는 특정 고객이나 청구서에 대해 고객 전체의 비용 분석에서 청구된 비용을 검색하고 분석할 수 있습니다. [비용 분석](quick-acm-cost-analysis.md) 보기에서 [보기를 저장](quick-acm-cost-analysis.md#saving-and-sharing-customized-views)하고 데이터를 [CSV 및 PNG 파일](quick-acm-cost-analysis.md#download-usage-data)로 내보낼 수도 있습니다.

고객 테넌트의 구독에 액세스할 수 있는 RBAC 사용자는 고객 테넌트의 구독에 대한 소매 비용을 분석하고, 보기를 저장하고, 데이터를 CSV 및 PNG 파일로 내보낼 수도 있습니다.

비용 분석에서 필터 및 그룹화 방법 기능을 사용하여 여러 필드를 기준으로 비용을 분석할 수 있습니다. 파트너별 필드는 다음 섹션에 표시됩니다.

## <a name="data-fields"></a>데이터 필드

다음 데이터 필드는 사용량 세부 정보 파일 및 Cost Management API에 있습니다. 가능한 경우 파트너 센터에 해당하는 정보가 표시됩니다. 다음과 같이 굵게 표시된 필드는 파트너가 비용 분석의 필터 및 그룹화 방법 기능을 사용하여 여러 필드를 기준으로 비용을 분석할 수 있습니다. 굵게 표시된 필드는 파트너가 지원하는 Microsoft 고객 계약에만 적용됩니다.

| **필드 이름** | **설명** | **파트너 센터 해당 항목** |
| --- | --- | --- |
| invoiceId | 특정 트랜잭션에 대한 청구서에 표시된 청구서 ID입니다. | 트랜잭션이 표시되는 청구서 번호입니다. |
| previousInvoiceID | 원래 청구서에 대한 참조에는 환불(음수 비용)이 있습니다. 환불이 있는 경우에만 채워집니다. | 해당 없음 |
| billingAccountName | 파트너를 나타내는 청구 계정의 이름입니다. Microsoft 고객 계약에 온보딩한 고객과 SaaS, Azure Marketplace 및 예약과 같은 자격 구매를 완료한 CSP 고객의 모든 비용이 축적됩니다. | 해당 없음 |
| billingAccountID | 파트너를 나타내는 청구 계정의 식별자입니다. | MCAPI 파트너 상거래 루트 ID입니다. 요청에 사용되지만 응답에는 포함되지 않습니다.|
| billingProfileID | Microsoft 고객 계약에 온보딩한 고객과 SaaS, Azure Marketplace 및 예약과 같은 자격 구매를 완료한 CSP 고객의 단일 청구 통화로 청구서 전체 비용을 그룹화하는 청구 프로필의 식별자입니다. | MCAPI 파트너 청구 그룹 ID입니다. 요청에 사용되지만 응답에는 포함되지 않습니다. |
| billingProfileName | Microsoft 고객 계약에 온보딩한 고객과 SaaS, Azure Marketplace 및 예약과 같은 자격 구매를 완료한 CSP 고객의 단일 청구 통화로 청구서 전체 비용을 그룹화하는 청구 프로필의 이름입니다. | 해당 없음 |
| invoiceSectionName | 청구서에 청구되는 프로젝트의 이름입니다. 파트너가 온보딩한 Microsoft 고객 계약에는 적용되지 않습니다. | 해당 없음 |
| invoiceSectionID | 청구서에 청구되는 프로젝트의 식별자입니다. 파트너가 온보딩한 Microsoft 고객 계약에는 적용되지 않습니다. | 해당 없음 |
| **CustomerTenantID** | 고객 구독의 Azure Active Directory 테넌트 식별자입니다. | 고객의 조직 ID - 고객의 Azure Active Directory TenantID입니다. |
| **CustomerName** | 고객 구독에 대한 Azure Active Directory 테넌트의 이름입니다. | 파트너 센터에 표시된 고객의 조직 이름입니다. 시스템 정보를 사용하여 청구서를 조정하는 데 중요합니다. |
| **CustomerTenantDomainName** | 고객 구독의 Azure Active Directory 테넌트의 도메인 이름입니다. | 고객 Azure Active Directory 테넌트 도메인입니다. |
| **PartnerTenantID** | 파트너의 Azure Active Directory 테넌트에 대한 식별자입니다. | 파트너 ID라고 하는 GUID 형식의 파트너 Azure Active Directory 테넌트 ID입니다. |
| **PartnerName** | 파트너 Azure Active Directory 테넌트의 이름입니다. | 파트너 이름입니다. |
| **ResellerMPNID** | 구독과 연결된 재판매인의 MPNID입니다. | 구독에 대한 레코드에서 재판매인의 MPN ID입니다. 현재 작업에 사용할 수 없습니다. |
| costCenter | 구독에 연결된 비용 센터입니다. | 해당 없음 |
| billingPeriodStartDate | 청구서에 표시된 청구 기간 시작 날짜입니다. | 해당 없음 |
| billingPeriodEndDate | 청구서에 표시된 청구 기간 종료 날짜입니다. | 해당 없음 |
| servicePeriodStartDate | 요금을 위해 서비스 사용량이 평가되는 평가 기간의 시작 날짜입니다. Azure 서비스의 가격은 평가 기간에 따라 결정됩니다. | 파트너 센터의 ChargeStartDate입니다. 이전에 청구되지 않은 잠재 사용 현황 데이터 날짜(이전 청구 주기)를 표시할 경우를 제외하고 청구 주기의 시작 날짜입니다. 시간은 항상 하루의 시작인 0:00입니다. |
| servicePeriodEndDate | 요금을 위해 서비스 사용량이 평가되는 기간의 종료 날짜입니다. Azure 서비스의 가격은 평가 기간을 기반으로 결정됩니다. | 해당 없음 |
| date | Azure 소비 데이터의 경우, 평가된 사용량의 날짜가 표시됩니다. 예약된 인스턴스의 경우, 구매한 날짜가 표시됩니다. Marketplace 및 지원과 같은 일회성 요금 및 반복 청구 요금의 경우 구매 날짜가 표시됩니다. | 해당 없음 |
| productID | 소비 또는 구매에 의해 요금이 발생한 제품의 식별자입니다. 파트너 센터에 표시된, productID와 SKuID가 연결된 키입니다. | 제품의 ID입니다. |
| product | 청구서에 표시된, 소비 또는 구매에 의해 요금이 발생한 제품의 이름입니다. | 카탈로그의 제품 이름입니다. |
| serviceFamily | 구매하거나 청구한 제품의 서비스 제품군을 표시합니다. 예를 들어 Storage 또는 Compute입니다. | 해당 없음 |
| productOrderID | 구독이 속하는 Azure 플랜 이름이나 자산의 식별자입니다. 예를 들어 Azure 플랜입니다. | CommerceSubscriptionID |
| productOrderName | 구독이 속하는 Azure 플랜의 이름입니다. 예를 들어 Azure 플랜입니다. | 해당 없음|
| consumedService | 레거시 EA 사용량 세부 정보에 사용되는 사용한 서비스(레거시 분류)입니다. | 파트너 센터에 표시되는 서비스입니다. 예를 들어 Microsoft.Storage, Microsoft.Compute, microsoft.operationalinsights입니다. |
| meterID | 측정된 소비에 대해 계량된 식별자입니다. | 사용된 미터의 ID입니다. |
| meterName | 측정된 소비에 대한 미터의 이름을 식별합니다. | 사용된 미터의 이름입니다. |
| meterCategory | 사용에 대한 최상위 서비스를 식별합니다. | 사용에 대한 최상위 서비스입니다. |
| meterSubCategory | 요율에 영향을 줄 수 있는 Azure 서비스의 유형이나 하위 범주를 정의합니다. | 요율에 영향을 줄 수 있는 Azure 서비스의 유형입니다.|
| meterRegion | 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. | 서비스를 위한 데이터 센터의 지역 위치(해당되며 입력된 경우)입니다. |
| 구독 ID | Azure 구독의 고유한 Microsoft 생성 식별자입니다. | EntitlementID |
| subscriptionName | Azure 구독의 이름입니다. | 해당 없음 |
| 용어 | 제품의 유효 기간을 표시합니다. 예를 들어 예약된 인스턴스는 예약된 인스턴스의 연간 12개월을 표시합니다. 일회성 구매 또는 되풀이 구매의 경우, 기간은 SaaS, Azure Marketplace 및 지원에 대해 한 달을 표시합니다. Azure 소비에는 적용되지 않습니다. | 해당 없음 |
| publisherType(firstParty, thirdPartyReseller, thirdPartyAgency) | 게시자의 유형이며, 게시자를 자사, 타사 재판매인 또는 타사 에이전시로 식별합니다. | 해당 없음 |
| partNumber | 사용되지 않는 예약된 인스턴스 및 Azure Marketplace 서비스의 파트 번호입니다. | 해당 없음 |
| publisherName | Microsoft나 타사 게시자를 비롯한 서비스 게시자의 이름입니다. | 제품의 게시자 이름입니다.|
| reservationId | 예약된 인스턴스 구매의 식별자입니다. | 해당 없음 |
| reservationName | 예약된 인스턴스의 이름입니다. | 해당 없음 |
| reservationOrderId | 예약된 인스턴스의 OrderID입니다. | 해당 없음 |
| frequency | 예약된 인스턴스의 지불 빈도입니다. | 해당 없음 |
| resourceGroup | 수명 주기 리소스 관리에 사용 되는 Azure 리소스 그룹의 이름입니다. | 리소스 그룹의 이름입니다. |
| instanceID (또는) ResourceID | 리소스 인스턴스의 식별자입니다. | 전체 리소스 속성을 포함하는 ResourceURI로 표시됩니다. |
| resourceLocation | 리소스 위치의 이름입니다. | 리소스의 위치입니다. |
| 위치 | 리소스의 정규화된 위치입니다. | 해당 없음 |
| effectivePrice | 서비스의 유효 단가(가격 책정 통화 단위)입니다. 제품, 서비스 제품군, 미터 및 제안에 대해 고유합니다. 청구 계정에 대한 가격표에서 가격 책정과 함께 사용됩니다. 계층화된 가격 책정이나 포함된 수량이 있으면 소비에 대한 혼합 가격이 표시됩니다. | 조정 후 단가입니다. |
| 수량 | 구매 또는 소비한 측정 수량입니다. 청구 기간 동안 사용된 미터의 양입니다. | 단위 수입니다. 조정하는 동안 청구 시스템의 정보와 일치하는지 확인하십시오. |
| unitOfMeasure | 서비스 요금이 청구되는 단위를 식별합니다. 예를 들면 GB와 시간입니다. | 서비스 요금이 청구되는 단위를 식별합니다. 예를 들면 GB, 시간, 10,000초입니다. |
| pricingCurrency | 단가를 정의하는 통화입니다. | 가격표의 통화입니다.|
| billingCurrency | 청구되 비용을 정의하는 통화입니다. | 고객 지역의 통화입니다. |
| chargeType | 구매 및 환불과 같이 Azure Cost Management에서 비용이 나타내는 요금 유형을 정의합니다. | 요금 또는 조정의 유형입니다. 현재 작업에 사용할 수 없습니다. |
| costinBillingCurrency | 청구된 통화 단위의 세전 혼합 비용 또는 ExtendedCost입니다. | 해당 없음 |
| costinPricingCurrency | 가격과의 상관 관계를 위한 가격 책정 통화 단위의 세전 혼합 비용 또는 ExtendedCost입니다. | 해당 없음 |
| **costinUSD** | USD 단위로 예상된 세전 혼합 비용 또는 ExtendedCost입니다. | 해당 없음 |
| **paygCostInBillingCurrency** | 가격이 소매 가격이면 비용을 표시합니다. 종량제 가격을 청구 통화 단위로 표시됩니다. RBAC 범위에서만 사용할 수 있습니다. | 해당 없음 |
| **paygCostInUSD** | 가격이 소매 가격이면 비용을 표시합니다. 종량제 가격을 USD로 표시합니다. RBAC 범위에서만 사용할 수 있습니다. | 해당 없음 |
| exchangeRate | 가격 책정 통화를 청구 통화로 변환하는 데 사용되는 환율입니다. | 파트너 센터에서는 PCToBCExchangeRate라고 합니다. 가격 책정 통화 대비 청구 통화의 환율입니다.|
| exchangeRateDate | 가격 책정 통화를 청구 통화로 변환하는 데 사용되는 환율의 날짜입니다. | 파트너 센터에서는 PCToBCExchangeRateDat라고 합니다. 가격 책정 통화 대비 청구 통화의 환율 날짜입니다.|
| isAzureCreditEligible | 비용을 Azure 크레딧으로 지불할 수 있는지 여부를 나타냅니다. | 해당 없음 |
| serviceInfo1 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. | 내부 Azure 서비스 메타데이터입니다. |
| serviceInfo2 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. | 서비스 정보입니다. 예를 들어, 가상 머신의 이미지 형식 및 ExpressRoute의 ISP 이름입니다.|
| additionalInfo | 서비스 특정 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다. | 다른 열에서 다루지 않은 추가 정보입니다. 서비스 관련 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다.|
| tags | 미터에 할당하는 태그입니다. 태그를 사용하여 청구 레코드를 그룹화합니다. 예를 들어 측정기를 사용하는 부서는 비용을 배분하는 데 태그를 사용할 수 있습니다. | 고객이 추가한 태그입니다.|
| **partnerEarnedCreditRate** | 파트너 관리자 링크 액세스에 기반한 PEC(파트너 획득 크레딧)가 있는 경우 적용되는 할인율입니다. | PEC(파트너 획득 크레딧)의 요율입니다. 예: 0% 또는 15%. |
| **partnerEarnedCreditApplied** | 파트너의 획득 크레딧을 적용했는지 여부를 나타냅니다. | 해당 없음 |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>PEC(파트너 획득 크레딧) 리소스 비용 보기

Azure Cost Management에서 파트너는 비용 분석을 사용하여 PEC 혜택을 받은 비용을 볼 수 있습니다.

Azure Portal에서 파트너 테넌트에 로그인하고 **Cost Management + 청구**를 선택합니다. **Cost Management**에서 **비용 분석**을 선택합니다.

비용 분석 보기에는 파트너에 대한 청구 계정의 비용이 표시됩니다. 청구서를 조정할 파트너, 특정 고객 또는 청구 프로필에 맞게 **범위**를 선택합니다.

도넛형 차트에서 드롭다운 목록을 선택하고 **PartnerEarnedCreditApplied**를 선택하여 PEC 비용을 상세히 살펴봅니다.

![파트너 획득 크레딧을 보는 방법을 보여주는 예제](./media/get-started-partners/cost-analysis-pec1.png)

**PartnerEarnedCreditApplied** 속성이 _True_인 경우, 관련 비용에 파트너 획득 관리자 액세스의 이점이 있습니다.

**PartnerEarnedCreditApplied** 속성이 _False_인 경우 관련 비용이 크레딧에 필요한 자격을 충족하지 못합니다. 또는 구매한 서비스가 파트너 획득 크레딧을 받을 자격이 없습니다.

서비스 사용 현황 데이터가 Cost Management에 표시되기까지 일반적으로 8-24시간이 걸립니다. 자세한 내용은 [비용, 사용량 데이터 업데이트 및 보존](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention)을 참조하세요. PEC 크레딧은 Azure Cost Management에 액세스한 후 48시간 이내에 표시됩니다.


**그룹화 방법** 옵션을 사용하여 **PartnerEarnedCreditApplied** 속성을 기준으로 그룹화하고 필터링할 수도 있습니다. 이 옵션을 사용하여 PEC가 있거나 없는 비용을 검사할 수 있습니다.

![파트너 획득 크레딧을 기준으로 그룹화 또는 필터링](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>비용 데이터를 Azure Storage로 내보내기

파트너 테넌트의 청구 범위에 액세스할 수 있는 파트너는 자신의 비용과 사용 현황 데이터를 Azure Storage Blob에 내보낼 수 있습니다. Blob은 공유 서비스 구독이나 고객의 구독이 아닌 파트너 테넌트의 구독에 있어야 합니다. 비용 데이터 내보내기를 사용하도록 설정하려면, 내보낸 비용 데이터를 호스팅할 파트너 테넌트에 독립적인 종량제 구독을 설정하는 것이 좋습니다. 내보내기 스토리지 계정은 종량제 구독에서 호스팅되는 Azure Storage Blob에 생성됩니다. 파트너가 내보내기를 만드는 범위를 기반으로, 연결된 데이터가 스토리지 계정에 자동으로 반복해서 내보내집니다.

구독에 대한 RBAC 액세스 권한이 있는 사용자는 고객 테넌트의 구독에서 호스팅되는 Azure Storage Blob으로 비용 데이터를 내보낼 수도 있습니다.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>파트너 테넌트 또는 고객 테넌트에 내보내기 만들기

Azure Portal에서 파트너 테넌트 또는 고객 테넌트에 로그인하고 **Cost Management + 청구**를 선택합니다. 적절한 범위(예: Microsoft 파트너 계약 청구 계정)를 선택한 다음, **비용 분석**을 선택합니다. 페이지가 로드되면 **내보내기**를 선택합니다. 내보내기 예약에서 **모든 내보내기 보기**를 선택합니다.

![내보내기 및 모든 내보내기 보기 선택](./media/get-started-partners/export01.png)

그런 다음, **추가**를 선택하고 이름을 입력하고 내보내기 유형을 선택합니다. **스토리지** 탭을 선택하고 필요한 정보를 입력합니다.

![신규 내보내기 추가 및 스토리지 탭 선택](./media/get-started-partners/export02.png)

파트너 테넌트에서 내보내기를 만드는 경우, 파트너 테넌트에서 종량제 구독을 선택합니다. 이 구독을 사용하여 Azure Storage 계정을 만듭니다.

고객 테넌트의 RBAC 사용자의 경우, 고객 테넌트에서 구독을 선택합니다. 이 구독을 사용하여 Azure Storage 계정을 만듭니다.

콘텐츠를 검토한 다음, **만들기**를 선택하여 내보내기를 예약합니다.

내보내기 목록에서 데이터를 확인하려면, 스토리지 계정 이름을 선택합니다. 스토리지 계정 페이지에서 **컨테이너**를 선택한 다음, 해당 컨테이너를 선택합니다. 해당 폴더로 이동하고 CSV 파일을 선택합니다. **다운로드**를 선택하여 CSV 파일을 가져와서 엽니다. 내보낸 데이터는 Azure Portal의 사용량 세부 정보와 유사한 비용 데이터를 닮았습니다.

![내보낸 데이터의 예](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API

파트너와 고객은 Cost Management API를 사용할 수 있으며, 일반적인 작업에 대해서는 다음 섹션에 설명되어 있습니다.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management API - 직접 및 간접 공급자

파트너 테넌트의 청구 범위에 액세스할 수 있는 파트너는 다음 API를 사용하여 청구된 비용을 볼 수 있습니다.

구독 범위의 API는 비용 정책에 관계없이 파트너(구독에 대한 액세스 권한이 있는 경우)가 호출할 수 있습니다. 구독에 액세스할 수 있는 다른 사용자(예: 고객 또는 재판매인)는 파트너가 고객 테넌트에 대한 비용 정책을 사용하도록 설정한 후에만 API를 호출할 수 있습니다.


#### <a name="to-get-a-list-of-billing-accounts"></a>청구 계정 목록을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>고객 목록을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>구독 목록을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>일정 기간에 대한 청구서 목록을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API 호출은 다음 JSON 코드와 유사한 요소가 있는 청구서 배열을 반환합니다.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

위의 반환된 ID 필드 값을 사용하고 다음 예제에서 사용량 세부 정보를 쿼리하는 범위로 바꿉니다.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

이 예에서는 특정 청구서와 관련된 사용 레코드를 반환합니다.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>고객이 비용을 볼 수 있는 정책을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>고객이 비용을 볼 수 있는 정책을 설정하려면

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>청구 계정에 대한 Azure 서비스 사용량을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>고객의 Azure 서비스 사용량을 다운로드 하려면

다음 get 호출은 비동기 작업입니다.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

응답에 반환된 `Location` URI를 호출하여 작업 상태를 확인합니다. *완료됨* 상태이면 생성된 보고서를 다운로드하는 데 사용할 수 있는 링크가 `downloadUrl` 속성에 포함됩니다.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>소비된 Azure 서비스의 가격표를 가져오거나 다운로드하려면

우선, 다음 post를 사용합니다.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

그런 다음, 비동기 작업 속성 값을 호출합니다. 예를 들면 다음과 같습니다.

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
위의 get 호출은 가격표를 포함하는 다운로드 링크를 반환합니다.


#### <a name="to-get-aggregated-costs"></a>집계된 비용을 구하려면

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>파트너를 위한 예산 만들기

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>고객을 위한 예산 만들기

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>예산 삭제

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>다음 단계
- Cost Management에서 [비용 분석 시작](quick-acm-cost-analysis.md)
- Cost Management에서 [예산 만들기 및 관리](tutorial-acm-create-budgets.md)
