---
title: Microsoft 고객 계약 청구 계정 시작 - Azure
description: 청구 프로필 및 청구서 결제 방법을 포함하여 Microsoft 고객 계약 청구 계정에 대해 알아봅니다.
author: bandersmsft
ms.reviewer: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 632fac953a54e661164152cda79082aed2c0bc06
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690479"
---
# <a name="get-started-with-your-microsoft-customer-agreement-billing-account"></a>Microsoft 고객 계약 청구 계정 시작

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 여러 청구 계정에 액세스할 수 있습니다. 예를 들어 개인 프로젝트를 위해 Azure에 가입한 경우가 있을 수 있습니다. 조직의 기업계약 또는 Microsoft 고객 계약을 통해 Azure에 액세스할 수도 있습니다. 이러한 각 시나리오의 경우 별도의 청구 계정을 가질 수 있습니다.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-customer-agreement).

## <a name="your-billing-account"></a>청구 계정

Microsoft 고객 계약의 청구 계정에는 청구서 및 결제 방법을 관리하는 데 사용되는 하나 이상의 청구 프로필이 포함되어 있습니다. 각 청구 프로필에는 청구 프로필의 청구에 명시된 비용을 정리할 수 있는 하나 이상의 청구서 섹션이 있습니다.

다음 다이어그램은 청구 계정, 청구 프로필 및 청구서 섹션 간의 관계를 보여줍니다.

![Microsoft 고객 계약 청구 계층을 보여주는 다이어그램](./media/mca-overview/mca-billing-hierarchy.png)

청구 계정의 역할에는 가장 높은 수준의 사용 권한이 있습니다. 기본적으로 Azure에 가입한 사용자만 청구 계정에 액세스할 수 있습니다. 이러한 역할은 청구서를 확인하고, 전체 조직의 비용을 추적해야 하는 재무 또는 IT 관리자와 같은 사용자에게 할당되어야 합니다. 자세한 내용은 [청구 계정 역할 및 작업](../manage/understand-mca-roles.md#billing-account-roles-and-tasks)을 참조하세요.

## <a name="billing-profiles"></a>청구 프로필

청구 프로필을 사용하여 청구서 및 결제 방법을 관리할 수 있습니다. 월초에 사용자 계정에서 각 청구 프로필의 월별 청구서가 생성됩니다. 청구서에는 이전 달의 모든 Azure 구독 및 기타 구매건의 해당 요금이 포함됩니다.

청구 프로필은 청구 계정에 자동으로 생성됩니다. 기본적으로 청구서 섹션이 하나 포함되어 있습니다. 프로젝트, 부서 또는 개발 환경 중 어느 것에 대한 것이든 요구 사항에 따라 손쉽게 비용을 추적하고 정리할 수 있도록 추가 섹션을 만들 수 있습니다. 각 구독의 사용량과 각 구독에 할당한 구매를 반영하는 해당 섹션이 청구 프로필의 청구서에 표시됩니다.

청구 프로필의 역할에는 청구서 및 결제 방법을 보고 관리할 수 있는 권한이 있습니다. 조직의 회계 팀원처럼 청구서 대금을 지불하는 사용자에게 이러한 역할을 할당합니다. 자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

### <a name="each-billing-profile-gets-a-monthly-invoice"></a>각 청구 프로필은 월별 청구서를 가져옵니다.

월초에 각 청구 프로필의 월별 청구서가 생성됩니다. 청구서는 이전 달의 모든 요금을 포함합니다.

Azure Portal에서 청구서를 확인하고, 문서를 다운로드하고, 설정을 변경하여 이후의 청구서를 이메일로 받을 수 있습니다. 자세한 내용은 [Microsoft 고객 계약 청구서 다운로드](../manage/download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement)를 참조하세요.

### <a name="invoice-payment-methods"></a>청구서 결제 방법

각 청구 프로필에는 청구서를 결제하는 데 사용되는 고유한 결제 방법이 있습니다. 다음과 같은 결제 방법이 지원됩니다.

| Type             | 정의  |
|------------------|-------------|
|Azure 크레딧    |  크레딧이 청구서의 적격 요금에 자동으로 적용되므로 지불해야 하는 금액이 줄어듭니다. 자세한 내용은 [청구 프로필의 Azure 크레딧 잔액 추적](../manage/mca-check-azure-credits-balance.md)을 참조하세요. |
|수표/전신 송금 | 수표/전신 송금을 통한 결제가 승인된 계정인 경우. 수표/전신 송금을 통해 청구서 대금을 지불할 수 있습니다. 지불 지침은 청구서에 제공됩니다. |
|신용 카드 | Azure 웹 사이트를 통해 Azure에 가입한 고객은 신용 카드를 통해 요금을 지불할 수 있습니다. |

### <a name="apply-policies-to-control-purchases"></a>구매를 제어하는 정책 적용

청구 프로필을 사용하여 Azure Marketplace 및 예약 구매를 제어하는 정책을 적용합니다. Azure Reservations 및 Marketplace 제품의 구매를 비활성화하는 정책을 설정할 수 있습니다. 정책이 적용되면 청구 프로필에 청구되는 구독을 사용하여 이러한 구매를 수행할 수 없습니다.

### <a name="azure-plans-determine-pricing-and-service-level-agreement-for-subscriptions"></a>Azure 계획에 따라 구독의 가격 책정 및 서비스 수준 계약이 결정됩니다.

Azure 계획에 따라 Azure 구독의 가격 책정 및 서비스 수준 계약이 결정됩니다. 이는 청구 프로필을 만들 때 자동으로 사용하도록 설정됩니다. 청구 프로필과 연결된 모든 청구서 섹션은 이러한 계획을 사용할 수 있습니다. 청구서 섹션에 대한 액세스 권한이 있는 사용자는 계획을 사용하여 Azure 구독을 만듭니다. Microsoft 고객 계약의 청구 계정에서 지원되는 Azure 계획은 다음과 같습니다.

| 계획             | 정의  |
|------------------|-------------|
|Microsoft Azure 계획   | 사용자가 모든 워크로드를 실행할 수 있는 구독을 만들 수 있게 허용합니다.  |
|개발/테스트용 Microsoft Azure 계획 | Visual Studio 구독자가 개발 또는 테스트 워크로드용으로 제한된 구독을 만들 수 있게 허용합니다. 이러한 구독은 Azure Portal의 배타적 가상 머신 이미지에 대한 액세스 권한 및 요금 할인과 같은 혜택을 받습니다. |

## <a name="invoice-sections"></a>청구서 섹션

청구서의 비용을 정리하려면 청구서 섹션을 만드세요. 예를 들어 조직에 대한 단일 청구서가 필요하지만 부서, 팀 또는 프로젝트별로 비용을 정리하려는 경우가 있습니다. 이 시나리오에서는 단일 청구 프로필을 사용하고, 각 부서, 팀 또는 프로젝트에 대한 청구서 섹션을 만들 수 있습니다.

청구서 섹션이 생성되면 다른 사용자에게 섹션에 청구되는 Azure 구독을 만들 수 있는 권한을 부여할 수 있습니다. 그러면 구독에 대한 모든 사용량 요금 및 구매는 섹션에 청구됩니다.

청구서 섹션의 역할에는 Azure 구독을 만드는 사용자를 제어할 수 있는 권한이 있습니다. 이러한 역할은 엔지니어링 책임자 및 기술 설계자와 같이 조직의 팀을 위한 Azure 환경을 설정하는 사용자에게 할당하세요. 자세한 내용은 [청구서 섹션 역할 및 작업](../manage/understand-mca-roles.md#invoice-section-roles-and-tasks)을 참조하세요.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

청구 계정에 대해 알아보려면 다음 문서를 참조하세요.

- [Azure의 Microsoft 고객 계약 관리자 역할 이해](../manage/understand-mca-roles.md)
- [Microsoft 고객 계약에 대한 추가 Azure 구독 만들기](../manage/create-subscription.md)
- [청구서에 섹션을 만들어 비용 정리하기](../manage/mca-section-invoice.md)
