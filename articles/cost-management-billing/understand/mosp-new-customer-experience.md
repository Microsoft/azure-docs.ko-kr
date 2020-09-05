---
title: 업데이트된 Azure 청구 계정 시작
description: 업데이트된 Azure 청구 계정을 시작하여 새로운 청구 및 비용 관리 환경의 변화를 파악하세요.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: banders
ms.openlocfilehash: 27a6f8f0ed0d58deb64f768d2e47bafebc910edb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690428"
---
# <a name="get-started-with-your-updated-azure-billing-account"></a>업데이트된 Azure 청구 계정 시작

비용 및 청구서 관리는 클라우드 환경의 주요 구성 요소 중 하나로써 클라우드 비용을 제어하고 이해하는 데 도움이 됩니다. 비용 및 청구서를 더 쉽게 관리할 수 있도록 Microsoft에서는 향상된 비용 관리 및 청구 기능을 포함하도록 Azure 청구 계정을 업데이트합니다. 이 문서에서는 청구 계정 업데이트에 대해 설명하고 새로운 기능을 안내합니다.

> [!IMPORTANT]
> 계정 업데이트 소식을 알려주는 Microsoft의 이메일을 받으면 계정이 업데이트됩니다. 알림은 계정 업데이트 60일 전에 전송됩니다.

## <a name="more-flexibility-with-your-new-billing-account"></a>더욱 유연해진 새 청구 계정

다음 다이어그램은 이전 청구 계정과 새 청구 계정을 비교합니다.

![이전 계정과 새 계정의 청구 계층 구조를 비교하는 다이어그램](./media/mosp-new-customer-experience/comparison-old-new-account.png)

새 청구 계정에는 청구서 및 결제 방법을 관리하는 데 사용되는 하나 이상의 청구 프로필이 포함되어 있습니다. 각 청구 프로필에는 청구 프로필의 청구에 명시된 비용을 정리할 수 있는 하나 이상의 청구서 섹션이 있습니다.

![새 청구 계층 구조를 보여주는 다이어그램](./media/mosp-new-customer-experience/new-billing-account-hierarchy.png)

청구 계정의 역할에는 가장 높은 수준의 사용 권한이 있습니다. 이러한 역할은 청구서를 확인하고, 전체 조직의 비용을 추적해야 하는 재무 또는 IT 관리자와 같은 사용자 또는 계정에 서명한 개인에게 할당되어야 합니다. 자세한 내용은 [청구 계정 역할 및 작업](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)을 참조하세요. 계정이 업데이트되면 이전 청구 계정에서 계정 관리자 역할이 할당된 사용자에게 새 계정의 소유자 역할이 부여됩니다.

## <a name="billing-profiles"></a>청구 프로필

청구 프로필은 청구서 및 결제 방법을 관리하는 데 사용됩니다. 월초에 사용자 계정에서 각 청구 프로필의 월별 청구서가 생성됩니다. 청구서에는 청구 프로필과 연결된 모든 구독의 이전 달 요금이 포함됩니다.

계정이 업데이트되면 각 구독에 대한 청구 프로필이 자동으로 생성됩니다. 구독 요금은 해당 청구 프로필에 청구되고 해당 청구서에 표시됩니다.

청구 프로필의 역할에는 청구서 및 결제 방법을 보고 관리할 수 있는 권한이 있습니다. 조직의 회계 팀원처럼 청구서 대금을 결제하는 사용자에게 이러한 역할을 할당해야 합니다. 자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요. 

계정이 업데이트되면 다른 사람에게 [청구서 보기](download-azure-invoice.md#allow-others-to-download-the-your-subscription-invoice) 권한을 부여한 각 구독에서 소유자, 기여자, 읽기 권한자 또는 청구 읽기 권한자 Azure RBAC 역할이 있는 사용자에게 해당 청구 프로필에 대한 읽기 권한자 역할이 부여됩니다.

## <a name="invoice-sections"></a>청구서 섹션

청구서 섹션은 청구서의 비용을 구성하는 데 사용됩니다. 예를 들어 단일 청구서가 필요하지만 부서, 팀 또는 프로젝트별로 비용을 구성하고 싶은 경우가 있습니다. 이 시나리오에서는 단일 청구 프로필을 사용하고, 각 부서, 팀 또는 프로젝트에 대한 청구서 섹션을 만들 수 있습니다.

계정이 업데이트되면 각 청구 프로필에 대한 청구서 섹션이 생성되고 관련 구독이 청구서 섹션에 할당됩니다. 구독을 더 추가할 때 추가 섹션을 만들고 청구서 섹션에 구독을 할당할 수 있습니다. 할당된 각 구독의 사용량을 반영하는 섹션이 청구 프로필의 청구서에 표시됩니다.

청구서 섹션의 역할에는 Azure 구독을 만드는 사용자를 제어할 수 있는 권한이 있습니다. 이러한 역할은 엔지니어링 책임자나 기술 설계자 같은 조직의 팀을 위한 Azure 환경을 설정한 사용자에게 할당해야 합니다. 자세한 내용은 [청구서 섹션 역할 및 작업](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)을 참조하세요.

## <a name="enhanced-features-in-your-new-experience"></a>새 환경의 향상된 기능

새 환경에는 비용과 청구서를 쉽게 관리할 수 있는 다음과 같은 비용 관리 및 청구 기능이 포함되어 있습니다.

#### <a name="invoice-management"></a>청구서 관리

**보다 예측 가능한 월간 청구 기간** - 새 계정의 청구 기간은 Azure 사용을 가입하는 날짜에 관계 없이 해당 월의 1일에 시작하여 마지막 날에 끝납니다. 매월 1일에 청구서가 생성되며, 이전 달의 모든 요금이 포함됩니다.

**여러 구독에 대한 단일 월별 청구서 제공** - 구독마다 월별 청구서를 받을 수도 있고 여러 구독에 대한 단일 청구서를 받을 수도 있습니다.

**Azure 구독, 지원 플랜 및 Azure Marketplace 제품에 대한 단일 월별 청구서 제공** - Azure 구독 사용 요금, 지원 플랜 및 Azure Marketplace 구매를 포함하여 모든 요금에 대한 단일 월별 청구서를 받게 됩니다.

**청구서의 비용을 필요한 대로 그룹화** - 청구서의 비용을 필요한 대로 부서별, 프로젝트별 또는 팀별로 그룹화할 수 있습니다.

**청구서에 선택적 구매 주문 번호 설정** - 청구서를 내부 재무 시스템과 연결하려면 구매 주문 번호를 설정합니다. 언제든지 Azure Portal에서 관리하고 업데이트할 수 있습니다.

#### <a name="payment-management"></a>결제 관리

**신용 카드를 사용하여 즉시 청구서 결제** - 신용 카드로 자동 결제가 청구될 때까지 기다릴 필요가 없습니다. Azure Portal에서 모든 신용 카드를 사용하여 결제일 전에 미리 결제하거나 기한이 지난 대금을 결제할 수 있습니다.

**계정에 적용되는 모든 결제 추적** - Azure Portal에서 사용되는 결제 방법, 결제 금액 및 결제 날짜를 포함하여 계정에 적용되는 모든 결제 정보를 살펴볼 수 있습니다.

#### <a name="cost-management"></a>비용 관리

**매월 사용량 데이터를 스토리지 계정으로 내보내기 예약** - 비용 및 사용량 데이터를 일, 주 또는 월 단위로 스토리지 계정에 자동으로 게시합니다.

#### <a name="account-and-subscription-management"></a>계정 및 구독 관리

**청구 작업을 수행할 여러 관리자 할당** - 계정의 대금 청구를 관리할 여러 사용자에게 청구 권한을 할당합니다. 다른 사람에게 읽기, 쓰기 또는 두 권한을 모두 제공하여 유연성을 높일 수 있습니다.

**Azure Portal에서 직접 추가 구독 만들기** - Azure Portal에서 클릭 한 번으로 모든 구독을 만들 수 있습니다.

#### <a name="api-support"></a>API 지원

**API, SDK 및 PowerShell을 통해 청구 및 비용 관리 작업 수행** - 비용 관리, 청구 및 소비 API를 사용하여 선호하는 데이터 분석 도구로 청구 및 비용 데이터를 가져옵니다.

**API, SDK 및 PowerShell을 통해 모든 구독 작업 수행** - Azure 구독 API를 사용하여 구독 만들기, 이름 바꾸기 및 취소를 포함하여 Azure 구독 관리를 자동화합니다.

## <a name="get-prepared-for-your-new-experience"></a>새 환경에 대비

다음은 새 환경에 대비하기 위한 권장 사항입니다.

**월간 청구 기간 및 다른 청구서 날짜**

새 환경에서는 매월 9일 전후로 청구서가 생성되며 이전 달의 모든 요금이 청구서에 포함됩니다. 이 날짜와 이전 계정에서 청구서가 생성되는 날짜가 서로 다를 수 있습니다. 청구서를 다른 사람과 공유하는 경우 변경된 날짜를 알려주어야 합니다.

**새로운 청구 및 비용 관리 API**

Cost Management 또는 청구 API를 사용하여 청구 또는 비용 데이터를 쿼리하고 업데이트하는 경우 새 API를 사용해야 합니다. 아래 표에는 새 청구 계정에서 작동하지 않는 API 및 새 청구 계정에서 변경해야 하는 내용이 나열되어 있습니다.

|API | 변경  |
|---------|---------|
|[청구 계정 - 나열](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/billingaccounts/list) | 청구 계정 - 나열 API에서 이전 청구 계정의 agreementType은 **MicrosoftOnlineServiceProgram**이고, 새 청구 계정의 agreementType은 **MicrosoftCustomerAgreement**입니다. agreementType을 사용하는 경우 업데이트하세요. |
|[청구서 - 청구 구독별 나열](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingsubscription)     | 이 API는 계정이 업데이트되기 전에 생성된 청구서만 반환합니다. 새 청구 계정에서 생성된 청구서를 가져오려면 [청구서 - 청구서 계정별 나열](https://docs.microsoft.com/rest/api/billing/2019-10-01-preview/invoices/listbybillingaccount) API를 사용해야 합니다. |

## <a name="additional-information"></a>추가 정보

다음 섹션에서는 새 환경에 대한 추가 정보를 제공합니다.

**서비스 가동 중지 시간 없음** 구독에 포함된 Azure 서비스는 중단 없이 계속 실행됩니다. 청구 환경만 업데이트됩니다. 기존 리소스, 리소스 그룹 또는 관리 그룹에는 영향이 없습니다.

**Azure 리소스에는 변화 없음** Azure RBAC(역할 기반 액세스 제어)를 사용하여 설정된 Azure 리소스 액세스는 업데이트의 영향을 받지 않습니다.

**새 환경에서 이전 청구서 사용 가능** 계정을 업데이트하기 전에 생성된 청구서가 Azure Portal에 계속 제공됩니다.

**월 중간에 업데이트된 계정의 청구서** 계정이 월 중간에 업데이트되는 경우 계정이 업데이트될 때까지 누적된 요금에 대한 청구서를 하나 받게 됩니다. 그리고 해당 월의 나머지 기간에 대한 또 다른 청구서를 받게 됩니다. 예를 들어 계정에 구독이 하나 있는데, 9월 15일에 업데이트되었다고 가정하겠습니다. 이 경우 9월 15일까지 누적된 요금의 청구서를 하나 받게 됩니다. 그리고 9월 15~30일 기간에 대한 청구서를 하나 더 받게 됩니다. 10월부터는 매월 하나의 청구서를 받게 됩니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 청구 계정에 대해 자세히 알아보세요.

- [새 청구 계정의 관리 역할 이해](../manage/understand-mca-roles.md)
- [새 청구 계정에 대한 추가 Azure 구독 만들기](../manage/create-subscription.md)
- [청구서에 섹션을 만들어 비용 정리하기](../manage/mca-section-invoice.md)
