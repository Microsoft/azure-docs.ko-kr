---
title: Azure Portal에서 청구 계정을 살펴볼 때 발생하는 문제 해결
description: 이 문서는 Azure Portal에서 청구 계정을 살펴보려고 할 때 발생하는 문제 해결에 도움이 됩니다.
author: amberbhargava
ms.reviewer: banders
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f701e41c62336bcd7638360a27a0fb4c3ce3ec7d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686669"
---
# <a name="troubleshoot-viewing-your-billing-account-in-the-azure-portal"></a>Azure Portal에서 청구 계정을 살펴볼 때 발생하는 문제 해결

청구 계정은 Azure를 사용하기 위해 가입할 때 생성됩니다. 청구 계정을 사용하여 청구서, 결제를 관리하고 비용을 추적할 수 있습니다. 액세스 가능한 청구 계정이 여러 개인 경우가 있습니다. 예를 들어 개인 용도로 Azure에 가입했을 수 있습니다. 조직의 기업계약 또는 Microsoft 고객 계약을 통해 Azure에 액세스할 수도 있습니다. 이러한 각 시나리오의 경우 별도의 청구 계정을 가질 수 있습니다. 이 문서는 Azure Portal에서 청구 계정을 살펴보려고 할 때 발생하는 문제 해결에 도움이 됩니다.

[Azure Cost Management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) 페이지에서 청구 계정을 볼 수 있습니다.

청구 계정에 대해 자세히 알아보고 청구 계정 유형을 확인하려면 [Azure Portal에서 청구 계정 보기](view-all-accounts.md)를 참조하세요.

Azure Portal에서 청구 계정을 볼 수 없는 경우 다음 옵션을 시도해 보세요.

## <a name="sign-in-to-a-different-tenant"></a>다른 테넌트에 로그인

청구 계정은 단일 Azure Active Directory 테넌트에 연결됩니다. 잘못된 테넌트에 로그인하면 Cost Management + 청구 페이지에 청구 계정이 표시되지 않습니다. 다음 단계에 따라 Azure Portal에서 다른 테넌트로 전환하고, 해당 테넌트의 청구 계정을 확인하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 페이지 오른쪽 위에서 프로필(이메일 주소)을 선택합니다.
1. **디렉터리 전환**을 선택합니다.  
    ![포털에서 디렉터리 전환을 선택하는 방법을 보여주는 스크린샷](./media/troubleshoot-account-not-found/select-switch-directory.png)
1. **모든 디렉터리** 섹션에서 디렉터리를 선택합니다.  
    ![포털에서 디렉터리를 선택하는 방법을 보여주는 스크린샷](./media/troubleshoot-account-not-found/select-directory.png)

## <a name="sign-in-with-a-different-email-address"></a>다른 이메일 주소로 로그인

여러 이메일 주소를 사용하여 [Azure Portal](https://portal.azure.com)에 로그인하는 사용자도 있습니다. 모든 이메일 주소로 청구 계정에 액세스할 수 있는 것은 아닙니다. 리소스를 관리할 수 있지만 청구 계정을 볼 수 있는 권한이 없는 이메일 주소를 사용하여 로그인하는 경우 Azure Portal의 [Cost Management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) 페이지에 청구 계정이 표시되지 않습니다.

청구 계정에 액세스할 수 있는 청구 계정 권한이 있는 이메일 주소를 사용하여 Azure Portal에 로그인합니다.

## <a name="sign-in-with-a-different-identity"></a>다른 ID로 로그인

이메일 주소가 동일한 ID(회사 또는 학교 계정과 개인 계정)를 2개 사용하는 사용자도 있습니다. 일반적으로 두 ID 중 하나에만 청구서를 볼 수 있는 권한이 부여됩니다. 이메일 주소 하나로 ID를 2개 만들 수도 있습니다. 청구 계정을 볼 수 있는 권한이 없는 ID로 로그인하면 [Cost Management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade) 페이지에 청구 계정이 표시되지 않습니다. 이 경우 다음 단계에 따라 ID를 전환합니다.

1. InPrivate/Incognito 창에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 이메일 주소의 ID가 2개인 경우 개인 계정 또는 회사/학교 계정 중에 선택하는 옵션이 표시됩니다. 계정 중 하나를 선택합니다.
1. Azure Portal의 Cost Management + 청구 페이지에 청구 계정이 표시되지 않으면 1단계와 2단계를 반복하고 다른 ID를 선택합니다.

## <a name="contact-us-for-help"></a>도움이 필요한 경우 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

문제 해결에 도움이 되는 다음 청구 및 구독 문서를 참조하세요.

- [거부된 카드](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [구독 로그인 문제](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [구독을 찾을 수 없음](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Enterprise 비용 보기 사용 안 함](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)
