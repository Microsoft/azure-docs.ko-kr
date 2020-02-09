---
title: 파트너에 대 한 Azure Cost Management 시작
description: 이 문서에서는 파트너가 Azure Cost Management 기능을 사용 하는 방법과 고객이 자신의 고객에 게 Cost Management 액세스를 사용 하도록 설정 하는 방법을 설명 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 10889f6d872510fb53e76ab3722343aa2ee6a5e8
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293915"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>파트너에 대 한 Azure Cost Management 시작

Azure Cost Management는 고객이 Microsoft 고객 계약에 등록 [Azure 요금제를 구매한](/partner-center/purchase-azure-plan)파트너에 게 기본적으로 제공 됩니다. 이 문서에서는 파트너가 [Azure Cost Management](../index.yml) 기능을 사용 하 여 Azure 계획의 구독에 대 한 비용을 확인 하는 방법을 설명 합니다. 또한 파트너가 고객에 게 Cost Management 액세스를 사용 하도록 설정 하는 방법을 설명 합니다. 고객은 CSP 파트너가 사용 하도록 설정 된 경우 Cost Management 기능을 사용할 수 있습니다.

CSP 파트너는 Cost Management을 사용 하 여 다음을 수행 합니다.

- 송장 발부 된 비용을 이해 하 고 비용을 고객, 구독, 리소스 그룹 및 서비스에 연결 합니다.
- 고객, 구독, 리소스 그룹, 리소스, 측정기, 서비스 및 기타 여러 차원 별로 비용을 분석 하는 기능을 사용 하 여 [비용 분석](quick-acm-cost-analysis.md) 의 Azure 비용에 대 한 직관적인 보기를 확인 하세요.
- 비용 분석에서 PEC (파트너 획득 신용)가 적용 된 리소스 비용을 봅니다.
- 비용이 예산을 초과 하는 경우 프로그래밍 [예산](tutorial-acm-create-budgets.md) 및 경고를 사용 하 여 알림 및 자동화를 설정 합니다.
- Cost Management 데이터에 대 한 고객 액세스를 제공 하는 Azure Resource Manager 정책을 사용 하도록 설정 합니다. 그러면 고객은 [종 량 제 요금](https://azure.microsoft.com/pricing/calculator/)을 사용 하 여 구독에 대 한 소비 비용 데이터를 볼 수 있습니다.
- 종 량 제 구독이 있는 저장소 blob에 비용 및 사용 현황 데이터를 내보냅니다.

모든 고객에 대 한 비용을 보여 주는 예제는 다음과 같습니다.
모든 고객에 대 한 비용을 보여 주는 ![예제](./media/get-started-partners/customer-costs1.png)

단일 고객에 대 한 비용을 보여 주는 예제는 다음과 같습니다.
단일 고객에 대 한 비용을 보여 주는 ![예제](./media/get-started-partners/customer-costs2.png)

Azure Cost Management에서 사용할 수 있는 모든 기능을 REST Api와 함께 사용할 수도 있습니다. Api를 사용 하 여 비용 관리 작업을 자동화 합니다.

## <a name="prerequisites"></a>필수 조건

파트너는 기본적으로 Azure 계획에 있는 구독에 대해서만 Azure Cost Management을 사용할 수 있습니다.

Azure Portal에서 Azure Cost Management를 사용 하도록 설정 하려면 고객을 대신 하 여 Microsoft 고객 계약에 대 한 고객 동의가 있는지 확인 하 고 고객을 Azure 요금제로 전환 해야 합니다. Azure 요금제로 전환 되는 구독에 대 한 비용도 Azure Cost Management에서 사용할 수 있습니다.

Azure Cost Management에는 청구 계정 또는 구독에 대 한 읽기 권한이 필요 합니다.

청구 계정의 Azure Cost Management에 대 한 액세스를 설정 하 고 할당 하는 방법에 대 한 자세한 내용은 [사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조 하세요. **전역 관리자** 및 **관리 에이전트** 역할은 청구 계정에 대 한 비용을 관리할 수 있습니다.

구독 범위에서 Azure Cost Management 액세스 하기 위해 구독에 대 한 RBAC 액세스 권한이 있는 사용자는 소매점 (종 량 제) 요금으로 비용을 볼 수 있습니다. 그러나 고객 테넌트에 대 한 비용 표시 정책은 사용 하도록 설정 해야 합니다. 지원 되는 계정 유형의 전체 목록을 보려면 [데이터 Cost Management 이해](understand-cost-mgt-data.md)를 참조 하세요.


## <a name="how-cost-management-uses-scopes"></a>Cost Management 범위를 사용 하는 방법

범위는 청구 데이터를 관리 하 고, 지불액과 관련 된 역할이 있으며, 송장을 보고, 일반 계정 관리를 수행 하는 위치입니다. 청구 및 계정 역할은 RBAC를 사용 하는 리소스 관리에 사용 되는 범위와는 별도로 관리 됩니다. 액세스 제어 차이를 비롯 하 여 별도의 범위를 명확 하 게 구분 하기 위해 각각 청구 범위 및 RBAC 범위 라고 합니다.

청구 범위 및 RBAC 범위와 비용 관리가 범위에서 작동 하는 방식을 이해 하려면 [범위 이해 및](understand-work-scopes.md)사용을 참조 하세요.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>파트너 테넌트 요금 청구 범위를 사용 하 여 비용 관리

고객을 Microsoft 고객 계약에 등록 한 후에는 다음 _청구 범위_ 를 테넌트에서 사용할 수 있습니다. 범위를 사용 하 여 Cost Management 비용을 관리 합니다.

### <a name="billing-account-scope"></a>청구 계정 범위

청구 계정 범위를 사용 하 여 모든 고객 및 청구 프로필에 대 한 세금을 확인할 수 있습니다. 청구서 비용은 Microsoft 고객 계약의 고객 소비량 기반 제품에 대해서만 표시 됩니다. 그러나 Microsoft 고객 계약과 CSP 제품 모두에서 고객을 위해 구매한 기반 제품에 대해 청구서 비용이 표시 됩니다. 현재 범위에서 비용을 볼 수 있는 기본 통화는 미국 달러입니다. 범위에 대 한 예산은 USD에도 있습니다.

고객 청구 통화에 관계 없이 파트너는 청구 계정 범위를 사용 하 여 예산을 설정 하 고 고객, 구독, 리소스 및 리소스 그룹에 대 한 비용을 USD로 관리 합니다.

또한 파트너는 비용 분석 보기에서 고객 간의 특정 청구 통화 비용을 필터링 합니다. **실제 비용** 목록을 선택 하 여 지원 되는 고객 청구 통화의 비용을 확인 합니다.

![통화에 대 한 실제 비용 선택을 보여 주는 예제](./media/get-started-partners/actual-cost-selector.png)

청구 범위에서 [분할 상환 cost 뷰](quick-acm-cost-analysis.md#customize-cost-views) 를 사용 하 여 예약 된 기간 동안 예약 인스턴스 분할 상환 비용을 볼 수 있습니다.

### <a name="billing-profile-scope"></a>청구 프로필 범위

청구 프로필 범위를 사용 하 여 청구서에 포함 된 모든 제품 및 구독에 대해 모든 고객의 청구 통화에 대 한 세금을 확인할 수 있습니다. **InvoiceID** 필터를 사용 하 여 특정 송장의 청구 프로필에 대 한 비용을 필터링 할 수 있습니다. 필터는 특정 송장의 소비 및 제품 구매 비용을 보여 줍니다. 청구서의 특정 고객에 대 한 비용을 필터링 하 여 세 전 비용을 확인할 수도 있습니다.

Microsoft 고객 계약에 고객을 등록 한 후 Microsoft 고객 계약에 따라 고객에 대 한 모든 제품 (소비, 구매 및 권리)에 대 한 모든 요금이 포함 된 송장을 받게 됩니다. 동일한 통화로 청구 될 때 이러한 청구서에는 SaaS, Azure Marketplace 및 SaaS와 같은 구매한 제품에 대 한 요금 (CSP 제안에 아직 있는 고객에 대 한 예약)도 포함 됩니다.

고객 청구서에 대 한 요금을 조정 하기 위해 청구 프로필 범위를 사용 하 여 고객의 청구서에 대해 계산 되는 모든 비용을 확인할 수 있습니다. 청구서와 마찬가지로 범위는 새 Microsoft 고객 계약의 모든 고객에 대 한 비용을 표시 합니다. 범위에는 현재 CSP 제품의 고객 자격 제품에 대 한 모든 요금이 표시 됩니다.

청구 프로필 및 청구 계정 범위는 Azure Marketplace 및 예약 구매와 같은 권한 부여 및 구매 기반 제품에 대 한 요금을 보여 주는 유일한 적용 가능한 범위입니다.

청구 프로필은 청구서에 포함 되는 구독을 정의 합니다. 청구 프로필은 기업 계약 등록에 해당 하는 기능입니다. 청구 프로필은 송장이 생성 되는 범위입니다.

현재 청구 프로필 범위에서 비용을 볼 때 고객의 청구 통화는 기본 통화입니다. 청구 프로필 범위의 예산 집합은 청구 통화입니다.

파트너는이 범위를 사용 하 여 송장을 조정할 수 있습니다. 또한 범위를 사용 하 여 다음 항목에 대 한 청구 통화의 예산을 설정 합니다.

- 필터링 된 특정 송장
- Customer
- Subscription
- 리소스 그룹
- 리소스
- Azure 서비스
- 미터
- ResellerMPNID

### <a name="customer-scope"></a>고객 범위

파트너는 범위를 사용 하 여 Microsoft 고객 계약에 등록 하는 고객과 관련 된 비용을 관리 합니다. 이 범위를 통해 파트너는 특정 고객에 대 한 세 전 비용을 볼 수 있습니다. 특정 구독, 리소스 그룹 또는 리소스에 대 한 세 전 비용을 필터링 할 수도 있습니다.

고객 범위에는 현재 CSP 제품에 있는 고객이 포함 되지 않습니다. 범위에는 Microsoft 고객 계약이 있는 고객만 포함 됩니다. Azure 사용량이 아닌 권한 부여 비용은 고객 필터를 적용할 때 고객에 게 청구 계정 및 청구 프로필 범위에서 제공 됩니다.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Cost Management에서 청구 범위에 대 한 파트너 액세스

**전역 관리자** 및 **관리 에이전트** 역할이 있는 사용자만 파트너의 Azure 테넌트에서 직접 청구 계정, 청구 프로필 및 고객에 대 한 비용을 관리 하 고 볼 수 있습니다. 파트너 센터 역할에 대 한 자세한 내용은 [사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조 하세요.

## <a name="enable-cost-management-in-the-customer-tenant"></a>고객 테넌트에서 비용 관리 사용

파트너는 고객이 Microsoft 고객 계약에 등록 한 후 Cost Management에 대 한 액세스를 사용할 수 있습니다. 그런 다음 파트너는 고객이 종 량 제 요금으로 계산 된 비용을 볼 수 있도록 하는 정책을 사용 하도록 설정할 수 있습니다. 비용은 RBAC 구독 및 리소스 그룹 범위에서 사용 되는 사용량에 대 한 고객의 청구 통화로 표시 됩니다.

파트너가 비용 표시를 위한 정책을 사용 하도록 설정 하는 경우 구독에 대 한 Azure Resource Manager 액세스 권한이 있는 사용자는 종 량 제 요금으로 비용을 관리 하 고 분석할 수 있습니다. Azure 구독에 대 한 적절 한 RBAC 액세스 권한이 있는 대리점 및 고객은 효과적으로 비용을 볼 수 있습니다.

정책에 관계 없이 파트너는 구독 및 리소스 그룹에 대 한 액세스 권한이 있는 경우에도 비용을 볼 수 있습니다.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>정책을 사용 하 여 Azure 사용 요금 보기

파트너는 다음 정보를 사용 하 여 정책에서 고객에 대 한 Azure 사용 요금을 볼 수 있도록 합니다.

Azure Portal에서 파트너 테넌트에 로그인 하 고 **Cost Management + 청구**를 선택 합니다. 관련 Microsoft 파트너 계약 청구 계정을 선택한 다음 **고객**을 선택 합니다. 고객 목록은 청구 계정과 연결 됩니다.

고객 목록에서 비용을 볼 수 있도록 허용 하려는 고객을 선택 합니다.

![Cost Management에서 고객 선택](./media/get-started-partners/customer-list.png)

**설정**아래에서 **정책**을 선택 합니다.

선택한 고객에 대 한 구독과 연결 된 **Azure 사용** 요금에 대 한 현재 비용 표시 정책이 표시 됩니다.
고객이 종 량 제 요금을 볼 수 있도록 하 ![정책](./media/get-started-partners/cost-management-billing-policies.png)

정책이 **아니요**로 설정 된 경우에는 고객과 연결 된 구독 사용자에 대해 Azure Cost Management를 사용할 수 없습니다. 파트너가 사용 하도록 설정 되지 않은 경우 모든 구독 사용자에 대해 기본적으로 비용 표시 정책이 사용 되지 않습니다.

비용 정책이 **예**로 설정 된 경우 고객 테넌트에 연결 된 구독 사용자는 종 량 제 요금으로 사용 요금을 볼 수 있습니다.

비용 표시 정책을 사용 하는 경우 구독 사용이 있는 모든 서비스는 종 량 제 요금으로 비용을 표시 합니다. 예약 사용은 실제 및 분할 상환 비용에 대 한 요금이 0으로 표시 됩니다. 구매 및 자격은 특정 구독에 연결 되지 않습니다. 따라서 구매는 구독 범위에 표시 되지 않습니다.

고객 테넌트에 대 한 비용을 보려면 **Cost Management + 청구** 를 열고 관련 Microsoft 파트너 계약 청구 계정을 선택 합니다.

![청구 계정 선택](./media/get-started-partners/select-billing-account.png)

**청구**아래에서 **Azure 구독**을 선택한 다음, 고객을 선택 합니다.

![Azure 구독 고객 선택](./media/get-started-partners/subscriptions-select-customer.png)

**비용 분석** 을 선택 하 고 비용 검토를 시작 합니다.
비용 분석, 예산 및 경고는 종 량 제 요금 기반 비용으로 구독 및 리소스 그룹 RBAC 범위에서 사용할 수 있습니다.

![고객으로 비용 분석 보기 ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

RBAC 범위에서 예약 된 인스턴스에 대 한 분할 상환 보기 및 실제 비용은 0 요금을 표시 합니다. 예약 인스턴스 비용은 구매가 수행 된 청구 범위에만 표시 됩니다.

## <a name="analyze-costs-in-cost-analysis"></a>비용 분석에서 비용 분석

파트너 테넌트의 청구 범위에 대 한 액세스 권한이 있는 파트너는 특정 고객 또는 청구서에 대해 고객 전체의 비용 분석에서 송장이 발부 된 비용을 탐색 하 고 분석할 수 있습니다. [비용 분석](quick-acm-cost-analysis.md) 보기에서는 [보기를 저장](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) 하 고 데이터를 [CSV 및 PNG 파일로](quick-acm-cost-analysis.md#automation-and-offline-analysis)내보낼 수도 있습니다.

고객 테넌트의 구독에 대 한 액세스 권한이 있는 RBAC 사용자는 고객 테넌트의 구독에 대 한 소매점 비용을 분석 하 고, 보기를 저장 하 고, 데이터를 CSV 및 PNG 파일로 내보낼 수도 있습니다.

비용 분석에서 필터 및 그룹화 기능을 사용 하 여 여러 필드를 기준으로 비용을 분석할 수 있습니다. 파트너 관련 필드는 다음 섹션에 나와 있습니다.

## <a name="data-fields"></a>데이터 필드

다음 데이터 필드는 사용 현황 정보 파일 및 Cost Management Api에 있습니다. 사용 가능한 경우 파트너 센터에 해당 하는 정보가 표시 됩니다. 다음 굵게 표시 된 필드의 경우 파트너는 비용 분석에서 필터 및 그룹화 기능을 사용 하 여 여러 필드를 기준으로 비용을 분석할 수 있습니다. 굵게 표시 된 필드는 파트너가 지 원하는 Microsoft 고객 계약에만 적용 됩니다.

| **필드 이름** | **설명** | **파트너 센터 동급** |
| --- | --- | --- |
| invoiceId | 특정 트랜잭션에 대해 청구서에 표시 된 청구서 ID입니다. | 트랜잭션이 표시 되는 청구서 번호입니다. |
| previousInvoiceID | 원래 청구서에 대 한 참조에는 환불 (음의 비용)이 있습니다. 환불 된 경우에만 채워집니다. | N/A |
| billingAccountName | 파트너를 나타내는 청구 계정의 이름입니다. Microsoft 고객 계약에 등록 하 고 SaaS, Azure Marketplace, 예약 등의 권리를 구매한 고객에 대 한 모든 비용을 발생 시킬 수 있습니다. | N/A |
| billingAccountID | 파트너를 나타내는 청구 계정의 식별자입니다. | MCAPI 파트너 상거래 루트 ID입니다. 요청에 사용 되지만 응답에는 포함 되지 않습니다.|
| billingProfileID | 청구서에 대 한 비용을 청구 하는 청구 프로필에 대 한 식별자로, Microsoft 고객 계약을 등록 하 고 SaaS, Azure Marketplace 및와 같은 권한 부여를 구매한 고객에 게 서 예약. | MCAPI 파트너 청구 그룹 ID입니다. 요청에 사용 되지만 응답에는 포함 되지 않습니다. |
| billingProfileName | 청구서에 대 한 비용을 청구 하는 청구 프로필의 이름으로, Microsoft 고객 계약을 등록 하 고, SaaS, Azure Marketplace 및와 같은 권한 부여를 만든 CSP 고객과 예약. | N/A |
| invoiceSectionName | 청구서에서 청구 되는 프로젝트의 이름입니다. 파트너의 등록 Microsoft 고객 계약에는 적용 되지 않습니다. | N/A |
| invoiceSectionID | 청구서에서 청구 되는 프로젝트의 식별자입니다. 파트너의 등록 Microsoft 고객 계약에는 적용 되지 않습니다. | N/A |
| **CustomerTenantID** | 고객 구독의 Azure Active Directory 테넌트 식별자입니다. | 고객의 조직 ID-고객의 Azure Active Directory TenantID입니다. |
| **CustomerName** | 고객 구독에 대 한 Azure Active Directory 테넌트의 이름입니다. | 파트너 센터에 표시 된 고객의 조직 이름입니다. 시스템 정보를 사용 하 여 송장을 조정 하는 데 중요 합니다. |
| **CustomerTenantDomainName** | 고객 구독의 Azure Active Directory 테넌트에 대 한 도메인 이름입니다. | 고객 Azure Active Directory 테넌트 도메인입니다. |
| **대 여** | 파트너 Azure Active Directory 테넌트의 식별자입니다. | 파트너 Azure Active Directory 테넌트 ID는 GUID 형식의 파트너 ID로 호출 됩니다. |
| **PartnerName** | 파트너 Azure Active Directory 테넌트의 이름입니다. | 파트너 이름입니다. |
| **ResellerMPNID** | 구독과 연결 된 대리점에 대 한 MPNID입니다. | 구독에 대 한 대리점의 MPN ID입니다. 현재 활동에 사용할 수 없습니다. |
| costCenter | 구독과 관련 된 비용 센터입니다. | N/A |
| billingPeriodStartDate | 청구서에 표시 된 청구 기간 시작 날짜입니다. | N/A |
| billingPeriodEndDate | 청구서에 표시 된 청구 기간 종료 날짜입니다. | N/A |
| servicePeriodStartDate | 요금 등급이 청구 된 등급 기간의 시작 날짜입니다. Azure 서비스 가격은 평가 기간에 따라 결정 됩니다. | 파트너 센터의 ChargeStartDate. 이전 청구 주기에서 이전에 청구 되지 않은 잠재 사용량 데이터의 날짜를 제시 하는 경우를 제외 하 고 청구 주기 시작 날짜 시간은 항상 해당하는 날의 시작인 0:00입니다. |
| servicePeriodEndDate | 서비스 사용량이 요금으로 평가 된 기간의 종료 날짜입니다. Azure 서비스 가격은 평가 기간에 따라 결정 됩니다. | N/A |
| date | Azure 소비 데이터의 경우 등급이 지정 된 사용량의 날짜를 표시 합니다. 예약 된 인스턴스의 경우 구매한 날짜를 표시 합니다. 반복 요금 및 Marketplace 및 지원과 같은 일회성 요금은 구매 날짜를 표시 합니다. | N/A |
| productID | 소비 또는 구매 별 요금을 계산 하는 제품의 식별자입니다. 파트너 센터에 표시 된 것 처럼 productID 및 SKuID의 연결 된 키입니다. | 제품의 ID입니다. |
| product | 청구서에 표시 된 대로 소비 또는 구매 별 요금을 계산 하는 제품의 이름입니다. | 카탈로그의 제품 이름입니다. |
| serviceFamily | 구매한 제품의 서비스 제품군을 표시 합니다. 예를 들면 저장소 또는 계산입니다. | N/A |
| 제품 Orderid | 구독이 속한 자산의 식별자 또는 Azure 계획 이름입니다. 예: Azure 요금제. | N/A |
| productOrderName | 구독이 속한 Azure 계획의 이름입니다. 예: Azure 요금제. | N/A|
| consumedService | 레거시 EA 사용 세부 정보에 사용 되는 서비스 (레거시 분류)를 사용 합니다. | 파트너 센터에 표시 되는 서비스입니다. 예: operationalinsights, Microsoft. Compute 및 microsoft. |
| meterID | 측정 된 소비에 대 한 계량 된 식별자입니다. | 사용 된 미터의 ID입니다. |
| meterName | 측정 된 사용량에 대 한 미터의 이름을 식별 합니다. | 사용 된 미터의 이름입니다. |
| meterCategory | 사용에 대 한 최상위 서비스를 식별 합니다. | 사용에 대 한 최상위 수준 서비스입니다. |
| meterSubCategory | 요금에 영향을 줄 수 있는 Azure 서비스의 유형 또는 하위 범주를 정의 합니다. | 요금에 영향을 줄 수 있는 Azure 서비스의 유형입니다.|
| meterRegion | 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. | 서비스에 대 한 데이터 센터의 지리적 위치입니다 (해당 하는 경우). |
| 구독 ID | Azure 구독에 대해 고유한 Microsoft 생성 식별자입니다. | N/A |
| subscriptionName | Azure 구독의 이름입니다. | N/A |
| 조건 | 제품의 유효 기간을 표시합니다. 예를 들어 예약 된 인스턴스는 예약 된 인스턴스의 연간 기간 12 개월을 표시 합니다. 일회성 구매 또는 되풀이 구매의 경우 기간은 SaaS, Azure Marketplace 및 지원에 대해 한 달을 표시 합니다. Azure 소비에는 적용 되지 않습니다. | N/A |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | 자사, 타사 대리점 또는 타사 에이전시로 게시자를 식별 하는 게시자의 유형입니다. | N/A |
| partNumber | 사용 하지 않는 예약 된 인스턴스와 Azure Marketplace 서비스에 대 한 부품 번호입니다. | N/A |
| publisherName | Microsoft 또는 타사 게시자를 포함 하는 서비스의 게시자 이름입니다. | 제품 게시자의 이름입니다.|
| reservationId | 예약 인스턴스 구매의 식별자입니다. | N/A |
| reservationName | 예약 된 인스턴스의 이름입니다. | N/A |
| reservationOrderId | 예약 된 인스턴스의 OrderID입니다. | N/A |
| frequency | 예약 된 인스턴스의 지불 빈도입니다. | N/A |
| resourceGroup | 수명 주기 리소스 관리에 사용 되는 Azure 리소스 그룹의 이름입니다. | 리소스 그룹의 이름입니다. |
| instanceID (또는) ResourceID | 리소스 인스턴스의 식별자입니다. | 전체 리소스 속성을 포함 하는 ResourceURI로 표시 됩니다. |
| resourceLocation | 리소스 위치의 이름입니다. | 리소스의 위치입니다. |
| 위치 | 리소스의 정규화 된 위치입니다. | N/A |
| effectivePrice | 가격 책정 통화 단위의 서비스의 유효 단가입니다. 제품, 서비스 제품군, 측정기 및 제안에 대해 고유 합니다. 청구 계정에 대 한 가격표의 가격 책정과 함께 사용 됩니다. 계층화 된 가격 책정 또는 포함 된 수량이 있으면 소비에 대 한 혼합 가격이 표시 됩니다. | 조정한 후 단가입니다. |
| 수량 | 구매 하거나 사용한 측정 된 수량입니다. 청구 기간 동안 사용 된 측정기의 양입니다. | 단위 수입니다. 조정 하는 동안 청구 시스템의 정보와 일치 하는지 확인 합니다. |
| unitOfMeasure | 서비스 요금이 청구되는 단위를 식별합니다. 예를 들면 GB와 시간입니다. | 서비스 요금이 청구되는 단위를 식별합니다. 예: GB, 시간 및 1만 s. |
| pricingCurrency | 단가를 정의 하는 통화입니다. | 가격 목록의 통화입니다.|
| billingCurrency | 청구 된 비용을 정의 하는 통화입니다. | 고객 지역의 통화입니다. |
| chargeType | 구매 및 환불과 같은 Azure Cost Management 비용을 나타내는 청구 유형을 정의 합니다. | 요금 또는 조정 유형입니다. 현재 활동에 사용할 수 없습니다. |
| costinBillingCurrency | 세금이 청구 된 통화에 대 한 ExtendedCost 또는 블렌드된 비용입니다. | N/A |
| costinPricingCurrency | 가격과의 상관 관계를 위해 가격 책정 통화의 세금에 대 한 ExtendedCost 또는 혼합 비용. | N/A |
| **costinUSD** | 세율의 세금 이전 예상 ExtendedCost 또는 혼합 비용입니다. | N/A |
| **paygCostInBillingCurrency** | 가격 책정이 소매 가격 인 경우 비용을 표시 합니다. 청구 통화로 종 량 제 가격을 표시 합니다. RBAC 범위 에서만 사용할 수 있습니다. | N/A |
| **paygCostInUSD** | 가격 책정이 소매 가격 인 경우 비용을 표시 합니다. USD로 종 량 제 가격을 표시 합니다. RBAC 범위 에서만 사용할 수 있습니다. | N/A |
| exchangeRate | 가격 책정 통화에서 청구 통화로 변환 하는 데 사용 되는 환율입니다. | 파트너 센터에서 PCToBCExchangeRate 이라고 합니다. 청구 통화 환율에 대 한 가격 책정 통화입니다.|
| exchangeRateDate | 가격 책정 통화에서 청구 통화로 변환 하는 데 사용 되는 환율 날짜입니다. | 파트너 센터에서 PCToBCExchangeRateDat 이라고 합니다. 청구 통화 환율 날짜에 대 한 가격 책정 통화입니다.|
| isAzureCreditEligible | 비용이 Azure 크레딧을 통해 지불 대상이 될 수 있는지 여부를 나타냅니다. | N/A |
| serviceInfo1 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. | 내부 Azure 서비스 메타 데이터입니다. |
| serviceInfo2 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. | 서비스 정보. 예를 들어, 가상 머신의 이미지 형식 및 ExpressRoute의 ISP 이름입니다.|
| additionalInfo | 서비스 특정 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다. | 다른 열에서 다루지 않는 추가 정보입니다. 서비스별 메타 데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다.|
| tags | 미터에 할당 하는 태그입니다. 태그를 사용하여 청구 레코드를 그룹화합니다. 예를 들어 측정기를 사용하는 부서는 비용을 배분하는 데 태그를 사용할 수 있습니다. | 고객에 의해 추가 된 태그입니다.|
| **partnerEarnedCreditRate** | 파트너 관리자 링크 액세스를 기반으로 하는 파트너 획득 신용 (PEC)가 있는 경우 적용 되는 할인 률입니다. | 파트너 획득 크레딧 (PEC)의 요금입니다. 예를 들어 0% 또는 15%입니다. |
| **partnerEarnedCreditApplied** | 파트너의 획득 크레딧을 적용 했는지 여부를 나타냅니다. | N/A |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>파트너 획득 크레딧 (PEC) 리소스 비용 보기

Azure Cost Management에서 파트너는 비용 분석을 사용 하 여 PEC 혜택을 받은 비용을 볼 수 있습니다.

Azure Portal에서 파트너 테넌트에 로그인 하 고 **Cost Management + 청구**를 선택 합니다. **Cost Management**에서 **비용 분석**을 선택 합니다.

비용 분석 보기에는 파트너에 대 한 청구 계정의 비용이 표시 됩니다. 파트너, 특정 고객 또는 청구 프로필에 대 한 필요에 따라 청구서를 조정 하는 **범위** 를 선택 합니다.

도넛형 차트에서 드롭다운 목록을 선택 하 고 **PartnerEarnedCreditApplied** 를 선택 하 여 PEC 비용을 드릴 합니다.

![파트너 획득 크레딧을 보는 방법을 보여 주는 예제](./media/get-started-partners/cost-analysis-pec1.png)

**PartnerEarnedCreditApplied** 속성이 _True_인 경우 연결 된 비용은 파트너의 획득 관리자 액세스를 제공 합니다.

**PartnerEarnedCreditApplied** 속성이 _False_인 경우 관련 비용이 크레딧에 필요한 자격을 충족 하지 않습니다. 또는 구매한 서비스는 파트너의 획득 크레딧을 받을 자격이 없습니다.

서비스 사용 데이터는 일반적으로 Cost Management에 표시 되는 데 8-24 시간이 걸립니다. 자세한 내용은 [사용 현황 데이터 업데이트 빈도](understand-cost-mgt-data.md#usage-data-update-frequency-varies)변경을 참조 하세요. PEC 크레딧을 Azure Cost Management의 액세스 시간에서 48 시간 이내에 표시 됩니다.


**그룹화 방법** 옵션을 사용 하 여 **PartnerEarnedCreditApplied** 속성을 기준으로 그룹화 하 고 필터링 할 수도 있습니다. 이러한 옵션을 사용 하 여 PEC가 없는 비용을 검사할 수 있습니다.

![파트너-획득 크레딧을 그룹화 하거나 필터링 합니다.](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>비용 데이터를 Azure Storage으로 내보내기

파트너 테넌트의 청구 범위에 대 한 액세스 권한이 있는 파트너는 비용 및 사용 현황 데이터를 Azure Storage blob으로 내보낼 수 있습니다. Blob은 공유 서비스 구독 또는 고객의 구독이 아닌 파트너 테넌트의 구독에 있어야 합니다. 비용 데이터 내보내기를 사용 하려면 내보낸 비용 데이터를 호스팅하기 위해 파트너 테넌트에 독립적인 종 량 제 구독을 설정 하는 것이 좋습니다. 저장소 저장소 계정은 종 량 제 구독에서 호스트 되는 Azure Storage blob에 생성 됩니다. 파트너가 내보내기를 만드는 범위에 따라 연결 된 데이터를 자동으로 저장소 계정으로 내보냅니다.

구독에 대 한 RBAC 액세스 권한이 있는 사용자는 고객 테넌트의 모든 구독에서 호스트 되는 Azure storage blob으로 비용 데이터를 내보낼 수도 있습니다.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>파트너 테넌트 또는 고객 테넌트에서 내보내기 만들기

Azure Portal에서 파트너 테넌트 또는 고객 테넌트에 로그인 하 고 **Cost Management + 청구**를 선택 합니다. 적절 한 범위 (예: Microsoft 파트너 계약 청구 계정)를 선택 하 고 **비용 분석**을 선택 합니다. 페이지가 로드 되 면 **내보내기**를 선택 합니다. 일정 내보내기에서 **모든 내보내기 보기** 를 선택 합니다.

![내보내기 및 모든 내보내기 보기를 선택 합니다.](./media/get-started-partners/export01.png)

그런 다음 **추가** 를 선택 하 고 이름을 입력 한 다음 내보내기 유형을 선택 합니다. **저장소** 탭을 선택 하 고 필요한 정보를 입력 합니다.

![새 내보내기 추가 및 저장소 선택 탭](./media/get-started-partners/export02.png)

파트너 테넌트에서 내보내기를 만들 때 파트너 테넌트에서 종 량 제 구독을 선택 합니다. 해당 구독을 사용 하 여 Azure Storage 계정을 만듭니다.

고객 테넌트의 RBAC 사용자의 경우 고객 테넌트에서 구독을 선택 합니다. 구독을 사용 하 여 Azure Storage 계정을 만듭니다.

콘텐츠를 검토 한 다음 **만들기** 를 선택 하 여 내보내기를 예약 합니다.

내보내기 목록에서 데이터를 확인 하려면 저장소 계정 이름을 선택 합니다. 저장소 계정 페이지 **에서 컨테이너를 선택 하** 고 컨테이너를 선택 합니다. 해당 폴더로 이동 하 고 CSV 파일을 선택 합니다. **다운로드** 를 선택 하 여 CSV 파일을 가져오고 엽니다. 내보낸 데이터는 Azure Portal의 사용량 정보와 비슷한 비용 데이터와 비슷합니다.

![내보낸 데이터의 예](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>REST Api Cost Management

파트너와 고객은 다음 섹션에서 설명 하는 Cost Management Api를 사용 하 여 일반적인 작업을 수행할 수 있습니다.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management Api-직접 및 간접 공급자

파트너 테넌트의 청구 범위에 대 한 액세스 권한이 있는 파트너는 다음 Api를 사용 하 여 송장 발부 된 비용을 볼 수 있습니다.

구독 범위의 Api는 구독에 대 한 액세스 권한이 있는 경우 비용 정책에 관계 없이 파트너에 의해 호출 될 수 있습니다. 고객 또는 재판매인과 같이 구독에 대 한 액세스 권한이 있는 다른 사용자는 파트너가 고객 테넌트에 대 한 비용 정책을 사용 하도록 설정한 후에만 Api를 호출할 수 있습니다.


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

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>일정 기간에 대 한 청구서 목록을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

API 호출은 다음 JSON 코드와 유사한 요소가 포함 된 청구서의 배열을 반환 합니다.

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

위의 반환 된 ID 필드 값을 사용 하 고 다음 예제에서 사용 세부 정보를 쿼리할 범위로 바꿉니다.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

이 예에서는 특정 송장과 연결 된 사용 레코드를 반환 합니다.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>고객이 비용을 볼 수 있는 정책을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>비용을 볼 수 있도록 고객에 대 한 정책을 설정 하려면

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>청구 계정에 대 한 Azure 서비스 사용을 가져오려면

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>고객의 Azure 서비스 사용을 다운로드 하려면

다음 get 호출은 비동기 작업입니다.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

응답에서 반환 된 `Location` URI를 호출 하 여 작업 상태를 확인 합니다. 상태가 *완료*됨 인 경우 `downloadUrl` 속성에는 생성 된 보고서를 다운로드 하는 데 사용할 수 있는 링크가 포함 되어 있습니다.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>사용 되는 Azure 서비스에 대 한 가격 책정 시트를 다운로드 하거나 다운로드 하려면

먼저 다음 게시물을 사용 합니다.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

그런 다음 비동기 작업 속성 값을 호출 합니다. 예:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
위의 get 호출은 가격표를 포함 하는 다운로드 링크를 반환 합니다.


#### <a name="to-get-aggregated-costs"></a>집계 된 비용을 얻으려면

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>파트너에 대 한 예산 만들기

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>고객에 대 한 예산 만들기

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>예산 삭제

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>다음 단계
- Cost Management [비용 분석 시작](quick-acm-cost-analysis.md)
- Cost Management에서 [예산 작성 및 관리](tutorial-acm-create-budgets.md)
