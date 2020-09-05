---
title: Microsoft 고객 계약의 EA 작업 - Azure
description: 새 청구 계정에서 기업계약 작업을 완료하는 방법에 대해 알아봅니다.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 928800f22011f6388ec50a1ab5e906b2080a4125
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682895"
---
# <a name="complete-enterprise-agreement-tasks-in-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약의 청구 계정에서 기업계약 작업 완료

조직에서 기업계약 등록을 갱신하는 Microsoft 고객 계약에 서명한 경우 계약에 대한 새 청구 계정이 생성됩니다. 새 계정의 청구는 기업계약과 다르게 구성됩니다. 이 문서에서는 새 청구 계정을 사용하여 기업계약에서 수행한 작업을 수행하는 방법을 설명합니다.

## <a name="billing-organization-in-the-new-account"></a>새 계정의 청구 조직

다음 다이어그램은 새 청구 계정에서 청구를 구성하는 방법을 설명합니다.

![ea-mca-post-transition-hierarchy 이미지](./media/mca-enterprise-operations/mca-post-transition-hierarchy.png)

| 기업 계약   | Microsoft 고객 계약    |
|------------------------|--------------------------------------------------------|
| 등록            | 기업계약 등록과 비슷하게 청구 프로필을 사용하여 조직의 청구를 관리합니다. 엔터프라이즈 관리자는 청구 프로필의 소유자가 됩니다. 청구 프로필에 대해 자세히 알아보려면 [청구 프로필 이해](../understand/mca-overview.md#billing-profiles)를 참조하세요.
| department            | 기업계약 등록에 포함된 부서와 비슷하게 청구서 섹션을 사용하여 비용을 구성할 수 있습니다. 부서는 청구서 섹션이 되고 부서 관리자는 해당 청구서 섹션의 소유자가 됩니다. 청구서 섹션에 대한 자세한 내용은 [청구서 섹션 이해](../understand/mca-overview.md#invoice-sections)를 참조하세요. |
| 계정               | 기업계약에서 만든 계정은 새 청구 계정에서 지원되지 않습니다. 계정의 구독은 해당 부서의 각 청구서 섹션에 속합니다. 계정 소유자는 청구서 섹션에 대한 구독을 만들고 관리할 수 있습니다. |

## <a name="changes-for-enterprise-administrators"></a>엔터프라이즈 관리자를 위한 변경 내용

다음 변경 내용은 Microsoft 고객 계약으로 갱신된 기업계약의 엔터프라이즈 관리자에게 적용됩니다.

- 등록에 대한 청구 프로필이 생성됩니다. 기업계약 등록과 비슷하게 청구 프로필을 사용하여 조직의 청구를 관리합니다. 청구 프로필에 대한 자세한 내용은 [청구 프로필 이해](../understand/mca-overview.md#billing-profiles)를 참조하세요.
- 기업계약 등록에 포함된 각 부서의 청구서 섹션이 생성됩니다. 이 청구서 섹션을 사용하여 부서를 관리합니다. 새 청구서 섹션을 만들어 추가 부서를 설정할 수 있습니다. 청구서 섹션에 대한 자세한 내용은 [청구서 섹션 이해](../understand/mca-overview.md#invoice-sections)를 참조하세요.
- 기업계약 등록에서 만든 계정처럼 청구서 섹션에서 Azure 구독 작성자 역할을 사용하여 다른 사람에게 Azure 구독을 만들 수 있는 권한을 부여합니다.
- Azure EA Portal 대신 [Azure Portal](https://portal.azure.com)을 사용하여 조직의 청구를 관리합니다.

새 청구 계정에 대한 다음 역할이 제공됩니다.

**청구 프로필 소유자** - 계약에 서명할 때 생성된 청구 프로필에 대한 청구 프로필 소유자 역할이 할당됩니다. 이 역할을 통해 조직의 요금 청구를 관리할 수 있습니다. 요금 및 청구서를 보고, 청구서의 비용을 구성하고, 결제 방법을 관리하고, 조직의 요금 청구에 대한 액세스를 제어할 수 있습니다.

**청구서 섹션 소유자** - 기업계약 등록에 포함된 부서에 대해 만들어진 모든 청구서 섹션에서 청구서 섹션 소유자 역할이 할당됩니다. 이 역할을 통해 Azure 구독을 만들고 다른 제품을 구매할 수 있는 사용자를 제어할 수 있습니다.

### <a name="view-charges-and-credits-balance-for-your-organization"></a>조직의 요금 및 크레딧 잔액 보기

기업계약 등록과 비슷하게 청구 프로필을 사용하여 조직의 요금 및 크레딧 잔액을 추적합니다.

청구 프로필의 크레딧 잔액을 확인하는 방법은 [청구 프로필의 Azure 크레딧 잔액 추적](mca-check-azure-credits-balance.md)을 참조하세요.

청구 프로필의 요금을 확인하는 방법은 [Microsoft 고객 계약 청구서의 요금 이해](../understand/review-customer-agreement-bill.md)를 참조하세요.

### <a name="view-charges-for-a-department"></a>부서 요금 보기

기업계약에 포함된 각 부서에 대한 청구서 섹션이 생성됩니다. Azure Portal에서 청구서 섹션의 요금을 볼 수 있습니다. 자세한 내용은 [청구서 섹션별 트랜잭션 보기](../understand/review-customer-agreement-bill.md#view-transactions-by-invoice-sections)를 참조하세요.

### <a name="view-charges-for-an-account"></a>계정 요금 보기

기업계약 등록에서 만든 계정은 새 청구 계정에서 지원되지 않습니다. 계정의 구독은 해당 부서의 각 청구서 섹션에 속합니다. 계정 소유자는 청구서 섹션에 대한 구독을 만들고 관리할 수 있습니다.

계정에 포함된 구독의 누적 비용을 보려면 각 구독에 대한 비용 센터를 설정해야 합니다. 그러면 Azure 사용량 및 요금 csv 파일을 사용하여 구독을 비용 센터로 필터링 할 수 있습니다.

### <a name="download-usage-and-charges-csv-price-sheet-and-tax-documents"></a>사용량 및 요금 csv 파일, 가격표, 세금 문서 다운로드

청구 계정의 각 청구 프로필에 대한 월별 청구서가 생성됩니다. 청구서별로 Azure 사용량 및 요금 csv 파일, 가격표, 세금 문서(해당하는 경우)를 다운로드할 수 있습니다. 이번 달 요금에 대한 Azure 사용량 및 요금 csv 파일을 다운로드할 수도 있습니다.

Azure 사용량 및 요금 csv 파일을 다운로드하는 방법은 [Microsoft 고객 계약에 대한 사용량 다운로드](../understand/download-azure-daily-usage.md)를 참조하세요.

가격표를 다운로드하는 방법은 [Microsoft 고객 계약에 대한 가격표 다운로드](ea-pricing.md)를 참조하세요.

세금 문서를 다운로드하는 방법은 [Microsoft 고객 계약에 대한 세금 문서 보기](../understand/mca-download-tax-document.md#view-and-download-tax-documents)를 참조하세요.

### <a name="add-an-additional-enterprise-administrator"></a>엔터프라이즈 관리자 추가

조직의 요금 청구를 살펴보고 관리할 수 있도록 사용자에게 청구 프로필에 대한 액세스 권한을 부여합니다. Azure Portal에서 **액세스 제어(IAM)** 페이지를 사용하여 액세스 권한을 부여할 수 있습니다.  청구 프로필 역할에 대한 자세한 내용은 [청구 프로필 역할 및 작업](understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

청구 프로필에 대한 액세스 권한을 제공하는 방법은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

### <a name="create-a-new-department"></a>새 부서 만들기

기업계약 등록의 부서와 비슷하게 청구서 섹션을 만들어서 요구 사항에 따라 비용을 구성할 수 있습니다. Azure Portal에서 새 청구서 섹션을 만들 수 있습니다. 자세한 내용은 [청구서에 섹션을 만들어 비용 정리하기](mca-section-invoice.md)를 참조하세요.

### <a name="create-a-new-account"></a>새 계정 만들기

기업계약 등록에서 만든 계정처럼 청구서 섹션에서 사용자에게 Azure 구독 작성자 역할을 할당하여 Azure 구독을 만들 수 있는 권한을 부여합니다. 역할 할당에 대한 자세한 내용은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

## <a name="changes-for-department-administrators"></a>부서 관리자를 위한 변경 내용

다음 변경 내용은 Microsoft 고객 계약으로 갱신된 기업계약의 부서 관리자에게 적용됩니다.

- 기업계약 등록에 포함된 각 부서의 청구서 섹션이 생성됩니다. 이 청구서 섹션을 사용하여 부서를 관리합니다. 청구서 섹션에 대한 자세한 내용은 [청구서 섹션 이해](../understand/mca-overview.md#invoice-sections)를 참조하세요.
- 기업계약 등록에서 만든 계정처럼 청구서 섹션에서 Azure 구독 작성자 역할을 사용하여 다른 사람에게 Azure 구독을 만들 수 있는 권한을 부여합니다.
- Azure EA Portal 대신 Azure Portal을 사용하여 조직의 청구를 관리합니다.

새 청구 계정에 대한 다음 역할이 제공됩니다.

**청구서 섹션 소유자** - 기업계약에 포함된 부서에 대해 만들어진 청구서 섹션에서 청구서 섹션 소유자 역할이 할당됩니다. 이 역할을 통해 요금을 살펴보고 추적할 수 있으며, 청구서 섹션에 대한 Azure 구독을 만들고 다른 제품을 구매할 수 있는 사용자를 제어할 수 있습니다.

### <a name="view-charges-for-your-department"></a>부서 요금 보기

Azure Portal의 [Cost Management + 청구 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)에서 부서별로 생성된 청구서 섹션의 요금을 볼 수 있습니다.

### <a name="add-an-additional-department-administrator"></a>부서 관리자 추가

기업계약에 포함된 각 부서에 대한 청구서 섹션이 생성됩니다. Azure Portal에서 **액세스 제어(IAM)** 페이지를 사용하여 다른 사람에게 청구서 섹션을 살펴보고 관리하는 권한을 부여할 수 있습니다. 청구서 섹션 역할에 대한 자세한 내용은 [청구서 섹션 역할 및 작업](understand-mca-roles.md#invoice-section-roles-and-tasks)을 참조하세요.

청구서 섹션에 대한 액세스 권한을 제공하는 방법은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

### <a name="create-a-new-account-in-your-department"></a>부서의 새 계정 만들기

부서별로 생성된 청구서 섹션에서 사용자에게 Azure 구독 작성자 역할을 할당합니다. 역할 할당에 대한 자세한 내용은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

### <a name="view-charges-for-accounts-in-your-departments"></a>부서 내 계정의 요금 보기

기업계약 등록에서 만든 계정은 새 청구 계정에서 지원되지 않습니다. 계정의 구독은 해당 부서의 각 청구서 섹션에 속합니다. 계정 소유자는 청구서 섹션에 대한 구독을 만들고 관리할 수 있습니다.

부서 내 계정에 포함된 구독의 누적 비용을 보려면 각 구독에 대한 비용 센터를 설정해야 합니다. 그러면 Azure 사용량 및 요금 파일을 사용하여 구독을 비용 센터로 필터링 할 수 있습니다.

## <a name="changes-for-account-owners"></a>계정 소유자를 위한 변경 내용

기업계약의 계정 소유자는 새 청구 계정에서 Azure 구독을 만들 수 있는 권한을 갖게 됩니다. 기존 Azure 구독은 부서별로 생성된 청구서 섹션에 속합니다. 계정이 부서에 속해 있지 않으면 구독은 기본 청구서 섹션이라는 청구서 섹션에 소속됩니다.

추가 Azure 구독을 만들 수 있도록 새 청구 계정에서 다음 역할이 제공됩니다.

**Azure 구독 작성자** - 기업계약의 부서별로 생성된 청구서 섹션에서 Azure 구독 작성자 역할이 할당됩니다. 계정이 부서에 속해 있지 않으면 기본 청구서 섹션이라는 섹션에서 Azure 구독 작성자 역할이 할당됩니다. 이 역할을 통해 청구서 섹션에 대한 Azure 구독을 만들 수 있습니다.

### <a name="create-an-azure-subscription"></a>Azure 구독을 만듭니다.

Azure Portal에서 청구서 섹션에 대한 Azure 구독을 만들 수 있습니다. 자세한 내용은 [Microsoft 고객 계약에 대한 추가 Azure 구독 만들기](create-subscription.md)를 참조하세요.

### <a name="view-charges-for-your-account"></a>계정 요금 보기

계정에 속한 구독의 요금을 보려면 Azure Portal에서 [구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다. 구독 페이지에는 모든 구독의 요금이 표시됩니다.

### <a name="view-charges-for-a-subscription"></a>구독 요금 보기

[구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) 또는 Azure 비용 분석에서 구독 요금을 볼 수 있습니다. Azure 비용 분석에 대한 자세한 내용은 [비용 분석을 사용하여 비용 탐색 및 분석](../costs/quick-acm-cost-analysis.md)을 참조하세요.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [Microsoft 고객 계약에 대한 청구 계정 이해](../understand/mca-overview.md)
- [청구서 이해](../understand/review-individual-bill.md)
- [요금 청구 방식](../understand/understand-invoice.md)
- [다른 사용자로부터 Azure 구독의 청구 소유권 받기](mca-request-billing-ownership.md)
