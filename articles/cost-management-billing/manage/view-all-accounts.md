---
title: Azure Portal에서 청구 계정 보기
description: Azure Portal에서 청구 계정을 보는 방법을 알아봅니다. Enterprise, Microsoft 고객 및 Microsoft 파트너 계약의 범위 정보를 알아봅니다.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b18e08f93df143f9844fe774eee59452978d7437
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686397"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Azure Portal의 청구 계정 및 범위

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 여러 청구 계정에 액세스할 수 있습니다. 예를 들어 개인 프로젝트를 위해 Azure에 가입한 경우가 있을 수 있습니다. 또한 조직의 기업계약 또는 Microsoft 고객 계약을 통해 액세스할 수 있습니다. 이러한 각 시나리오의 경우 별도의 청구 계정을 가질 수 있습니다.

Azure Portal에서는 다음과 같은 유형의 청구 계정을 지원합니다.

- **Microsoft Online Services 프로그램**: Microsoft Online Services 프로그램의 청구 계정은 Azure 웹 사이트를 통해 Azure에 가입할 때 생성됩니다. 예를 들어 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입하는 경우 [종량제 요금을 사용하는 계정](https://azure.microsoft.com/offers/ms-azr-0003p/) 또는 [Visual studio 구독자](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) 자격입니다.

- **기업 계약**: 기업계약에 대한 청구 계정은 조직에서 Azure를 사용하기 위해 [EA(기업계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)에 서명할 때 생성됩니다. 기업계약에는 최대 2000개의 구독을 가질 수 있습니다.

- **Microsoft 고객 계약**: Microsoft 고객 계약에 대한 청구 계정은 조직이 Microsoft 담당자와 협력하여 Microsoft 고객 계약에 서명할 때 생성됩니다. Azure 웹 사이트를 통해 [종량제 요금을 사용한 계정](https://azure.microsoft.com/offers/ms-azr-0003p/)이나 [Azure 체험 계정](https://azure.microsoft.com/offers/ms-azr-0044p/)에 가입한 특정 지역의 일부 고객은 Microsoft 고객 계약에 대한 청구 계정도 있을 수도 있습니다. Microsoft 고객 계약에는 최대 20개의 구독을 가질 수 있습니다. 자세한 내용은 [Microsoft 고객 계약의 청구 계정 시작](../understand/mca-overview.md)을 참조하세요.

- **Microsoft 파트너 계약**: Microsoft 파트너 계약의 청구 계정은 CSP(클라우드 솔루션 공급자) 파트너가 자체 고객을 새로운 상거래 환경에서 관리할 수 있도록 생성되었습니다. Azure Portal에서 청구 계정을 관리하려면 파트너에게 [Azure 플랜](https://docs.microsoft.com/partner-center/purchase-azure-plan)을 사용하는 고객이 하나 이상 있어야 합니다. 자세한 내용은 [Microsoft 파트너 계약의 청구 계정 시작](../understand/mpa-overview.md)을 참조하세요.

청구 계정의 유형을 확인하려면 [청구 계정의 유형 확인](#check-the-type-of-your-account)을 참조하세요.

## <a name="scopes-for-billing-accounts"></a>청구 계정의 범위
범위는 청구를 보고 관리하는 데 사용하는 청구 계정 내의 노드입니다. 여기에서 청구 데이터, 지불, 청구서를 관리하고 일반 계정 관리를 수행합니다.

### <a name="microsoft-online-services-program"></a>Microsoft Online Services 프로그램

![MOSP 계층 구조를 보여주는 스크린샷](./media/view-all-accounts/mosp-hierarchy.png)

|범위  |정의  |
|---------|---------|
|청구 계정     | 고객이 Azure 사용을 수락하는 계약을 나타내며, 하나 이상의 구독이 포함되어 있습니다.  |
|Subscription     |  Azure 리소스의 그룹화를 나타냅니다. 이 범위에서 청구서가 생성됩니다. 지불 방법 및 사용 주소와 같은 기타 청구 정보가 이 범위에 연결됩니다.|

### <a name="enterprise-agreement"></a>기업 계약

![EA 계층 구조를 보여주는 스크린샷](./media/view-all-accounts/ea-hierarchy.png)

|범위  |정의  |
|---------|---------|
|청구 계정    | 기업계약 등록을 나타냅니다. 하나 이상의 부서와 계정을 포함합니다. 이 범위에서 청구서가 생성됩니다. |
|department     |  비용을 논리적 그룹으로 분류하고 예산을 설정하기 위한 선택적인 계정 그룹입니다.     |
|계정     |  단일 계정 소유자를 나타냅니다. 계정 소유자는 등록에 대한 요금이 청구되는 Azure 구독을 만들고 관리할 수 있는 권한이 있습니다. |

### <a name="microsoft-customer-agreement"></a>Microsoft 고객 계약

![MCA 계층 구조를 보여주는 스크린샷](./media/view-all-accounts/mca-hierarchy.png)

|범위  |작업  |
|---------|---------|
|청구 계정     |   고객이 Microsoft 제품 및 서비스 사용을 수락하는 계약을 나타내며, 하나 이상의 청구 프로필을 포함합니다. |
|청구 프로필     |   청구서 및 관련 청구 정보(예: 지불 방법 및 청구 주소)를 나타냅니다. 하나 이상의 청구서 섹션을 포함합니다. |
|청구서 섹션     |   청구서의 비용 그룹을 나타냅니다. Azure 구독과 Azure Marketplace 및 앱 소스 제품과 같은 기타 구매가 이 범위에 연결됩니다.    |

### <a name="microsoft-partner-agreement"></a>Microsoft 파트너 계약

![MPA 계층 구조를 보여주는 스크린샷](./media/view-all-accounts/mpa-hierarchy.png)

|범위  |작업  |
|---------|---------|
|청구 계정     |   새로운 상거래 환경에서 고객의 Microsoft 제품 및 서비스를 관리하는 파트너 계약을 나타냅니다. 하나 이상의 청구 프로필과 고객을 포함합니다.   |
|청구 프로필     |   통화에 대한 청구서를 나타냅니다.     |
|Customer    |   CSP(클라우드 솔루션 공급자) 파트너의 고객을 나타냅니다.  Azure 구독과 Azure Marketplace 및 앱 소스 제품과 같은 기타 구매가 이 범위에 연결됩니다.  |
|Reseller    |   고객에게 서비스를 제공하는 재판매인입니다. 구독에 대한 선택적 필드이며 CSP 2계층 모델에서 간접 공급자에만 적용됩니다.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Azure Portal에서 청구 범위 전환

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. 개요 페이지에서 **범위를 전환하세요**를 선택합니다.

   ![청구 범위를 보여주는 스크린샷](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > 한 범위에만 액세스할 수 있으면, 범위를 전환하세요가 표시되지 않습니다.

4. 세부 정보를 볼 범위를 선택합니다.

   ![청구 범위를 보여주는 스크린샷](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>계정 유형 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [비용 분석](../costs/quick-acm-cost-analysis.md)을 시작하는 방법을 알아봅니다.
