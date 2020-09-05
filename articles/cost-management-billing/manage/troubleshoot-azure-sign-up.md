---
title: Azure Portal 또는 Azure 계정 센터에서 새 계정을 등록할 때 발생하는 문제 해결
description: Microsoft Azure Portal 계정 센터에서 새 계정을 등록할 때 발생하는 문제를 해결합니다.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 86f9f92810cff241f816dadfc6e1e8ca120f1f4a
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686652"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Azure Portal 또는 Azure 계정 센터에서 새 계정을 등록할 때 발생하는 문제 해결

Microsoft Azure Portal 또는 Azure 계정 센터에서 새 계정을 등록할 때 문제가 발생할 수 있습니다. 이 짧은 가이드에서는 등록 프로세스를 안내하고 각 단계에서는 몇 가지 일반적인 문제에 대해 설명합니다.

> [!NOTE]
> 이미 기존 계정이 있고 로그인 문제를 해결하기 위한 지침을 찾고 있는 경우 [Azure 구독 로그인 문제 해결](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

등록을 시작하기 전에 다음 사항을 확인합니다.

- Azure 계정 프로필에 제공한 정보(연락처 이메일 주소, 주소 및 전화 번호 포함)가 정확합니다.
- 신용 카드 정보가 정확합니다.
- 동일한 정보가 포함된 Microsoft 계정이 아직 없습니다.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Azure 등록에 대한 단계별 연습

Azure 등록 환경은 다음 네 개의 섹션으로 구성됩니다.

- 사용자 정보
- 전화로 ID 검증
- 카드로 ID 검증
- 규약

이 연습에서는 Azure 계정에 등록하기 위해 필요한 올바른 정보의 예를 제공합니다. 각 섹션에는 몇 가지 일반적인 문제 및 해결 방법이 포함되어 있습니다.

## <a name="about-you"></a>사용자 정보

![사용자 정보](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>일반적인 문제 및 솔루션

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>“계정에서 발생한 문제로 인해 등록을 진행할 수 없습니다. 청구 지원 담당자에게 문의하세요.”라는 메시지가 나타납니다.

이 문제를 해결하려면 다음 단계를 따릅니다.

1.  계정 관리자 자격 증명을 사용하여 [Azure 계정 센터](https://account.azure.com/Profile)에 로그인합니다.
1.  **세부 정보 편집**을 선택합니다.
1.  모든 주소 필드가 완료되었고 유효한지 확인합니다.
1.  Azure 구독에 등록할 때 신용 카드 등록을 위한 청구 주소가 은행 기록과 일치하는지 확인합니다.

오류 메시지가 계속 표시되면 다른 브라우저를 사용하여 등록해보세요.

InPrivate 브라우징 방법

#### <a name="free-trial-is-not-available"></a>평가판은 제공되지 않습니다.

이전에 Azure 구독을 사용한 적이 있나요? Azure 사용 약관은 Azure에 새로 추가된 사용자에 대해서만 무료 평가판 활성화하도록 제한합니다. 다른 유형의 Azure 구독이 있는 경우 무료 평가판을 활성화할 수 없습니다. [종량제 구독](https://azure.microsoft.com/offers/ms-azr-0003p/) 등록을 고려합니다.

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>'Azure 구독에 적합하지 않습니다.'라는 메시지가 표시됩니다.

이 문제를 해결하려면 다음 내용이 사실인지 재차 확인하십시오.

- Azure 계정 프로필에 제공한 정보(연락처 이메일 주소, 주소, 전화 번호 포함)가 정확합니다.
- 신용 카드 정보가 정확합니다.
- 동일한 정보가 사용된 Microsoft 계정이 아직 없습니다.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>'현재 계정 유형이 지원되지 않습니다.' 메시지가 표시됩니다.

이 문제는 계정이 [관리되지 않는 Azure AD 디렉터리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)에 등록되어 있고 조직의 Azure AD 디렉터리에 없는 경우에 발생할 수 있습니다.
이 문제를 해결하려면 다른 계정을 사용하여 Azure 계정을 등록하거나 관리되지 않는 AD 디렉터리를 인수하세요. 자세한 내용은 [Azure Active Directory에서 관리자 권한으로 관리되지 않는 디렉터리 인수](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover)를 참조하세요.

## <a name="identity-verification-by-phone"></a>전화로 ID 검증

![전화로 ID 검증](./media/troubleshoot-azure-sign-up/2.png)
 
문자 메시지나 전화를 받으면 텍스트 상자에 받은 코드를 입력합니다.

### <a name="common-issues-and-solutions"></a>일반적인 문제 및 솔루션

#### <a name="no-verification-text-message-or-phone-call"></a>확인 문자 메시지 또는 전화 통화가 없습니다.

등록 확인 과정은 일반적으로 신속하지만, 확인 코드가 전송되는 데 최대 4분이 걸릴 수 있습니다.

다음은 몇 가지 추가 팁입니다.

- 요구 사항을 충족하는 한 확인을 위해 전화 번호를 사용할 수 있습니다. 확인을 위해 입력한 전화 번호는 계정의 연락처 번호로 저장되지 않습니다.
  - VoiP(Voice-over-IP) 전화 번호는 전화 인증 프로세스에 사용할 수 없습니다.
  - 전화가 미국 전화 번호로 거는 전화 또는 SMS 메시지를 수신할 수 있는지 확인합니다.
- 드롭다운 메뉴에서 선택한 국가 번호를 포함하여 입력한 전화 번호를 재차 확인하십시오.
- 전화가 문자 메시지(SMS)를 수신하지 못하는 경우 **전화 걸기** 옵션을 시도합니다.

## <a name="identity-verification-by-card"></a>카드로 ID 검증

![카드로 ID 검증](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>일반적인 문제 및 솔루션

#### <a name="credit-card-declined-or-not-accepted"></a>신용 카드가 거부되거나 승인되지 않습니다.

가상 카드, 선불 카드 또는 직불 카드는 Azure 구독의 지불 옵션으로 허용되지 않습니다. 카드가 거부될 수 있는 다른 항목을 확인 하려면 [Azure 등록에서 거부된 카드 문제 해결](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)을 참조하세요.

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>신용 카드 양식이 내 청구 주소를 지원하지 않습니다.

청구 주소는 **내 정보** 섹션에서 선택한 국가에 속해야 합니다. 올바른 국가를 선택했는지 확인합니다.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>카드로 ID 검증 섹션에서 진행 표시줄이 중단됩니다.

카드를 통해 본인 여부 확인을 완료하려면 브라우저에서 타사 쿠키를 허용해야 합니다.

다음 단계를 사용하여 브라우저의 쿠키 설정을 업데이트하세요.

1. 쿠키 설정을 업데이트하세요.
   - **Chrome**을 사용하는 경우:
     - **설정** > **고급 설정 표시** > **개인 정보** > **콘텐츠 설정**을 선택합니다. **타사 쿠키 및 사이트 데이터 차단**을 선택 해제합니다.

   - **Microsoft Edge**를 사용하는 경우:
     - **설정** > **고급 설정 보기** > **쿠키** > **쿠키를 차단하지 않음**을 선택합니다.

1. Azure 등록 페이지를 새로 고친 다음, 문제가 해결되었는지 여부를 확인합니다.
1. 새로 고쳐도 문제가 해결되지 않으면 브라우저를 종료했다가 다시 시작한 후 다시 시도하세요.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>평가판 계정에 요금이 부과되었습니다.

등록한 후에는 신용 카드 계정에 소액의 확인용 금액이 표시될 수 있습니다. 이 표시는 3~5일 내에 제거됩니다. 비용 관리가 염려되는 경우 [예상치 못한 비용 방지](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started)에 대해 자세히 알아봅니다.

## <a name="agreement"></a>규약

규약 만들기

## <a name="other-issues"></a>기타 문제

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN, BizSpark, BizSparkPlus 또는 MPN과 같은 Azure 혜택 계획을 활성화할 수 없습니다.

올바른 로그인 자격 증명을 사용하고 있는지 확인합니다. 그런 다음, 혜택 프로그램에서 자격이 있는지 확인합니다.
- MSDN
  - [MSDN 계정 페이지](https://msdn.microsoft.com/subscriptions/manage/default.aspx)에서 자격 상태를 확인합니다.
  - 자신의 상태를 확인할 수 없는 경우 [MSDN 구독 고객 서비스 센터](https://msdn.microsoft.com/library/aa493452.aspx)에 문의하세요.
- Microsoft for Startups
  - [Microsoft for Startups 포털](https://startups.microsoft.com/#start-two)에 로그인하여 Microsoft for Startups에 대한 자격 상태를 확인합니다.
  - 자신의 상태를 확인할 수 없는 경우에는 [Microsoft for Startups 포럼](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups)에서 도움을 받을 수 있습니다.
- MPN
  - [MPN 포털](https://mspartner.microsoft.com/Pages/Locale.aspx)에 로그인하여 자격 상태를 확인합니다. 적절한 [클라우드 플랫폼 역량](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)을 가지고 있는 경우 추가 혜택의 자격이 있을 수 있습니다.
  - 자신의 상태를 확인할 수 없는 경우 [MPN 지원](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx)에 문의하세요.


### <a name="cant-activate-new-azure-in-open-subscription"></a>새 Azure In Open 구독을 활성화할 수 없습니다.

Azure In Open 구독을 만들려면 하나 이상의 Azure In Open 토큰이 연결된 유효한 OSA(Online Service Activation) 키가 있어야 합니다. OSA 키가 없는 경우 [Microsoft Pinpoint](https://pinpoint.microsoft.com/)에 나열된 Microsoft 파트너 중 한 곳에 문의하세요.

## <a name="additional-help-resources"></a>추가 도움말 리소스

Azure 청구 및 구독에 대한 기타 문제 해결 문서

- [거부된 카드](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-declined-card)
- [구독 로그인 문제](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue)
- [구독을 찾을 수 없음](https://docs.microsoft.com/azure/cost-management-billing/manage/no-subscriptions-found)
- [Enterprise 비용 보기 사용 안 함](https://docs.microsoft.com/azure/cost-management-billing/manage/enterprise-mgmt-grp-troubleshoot-cost-view)

## <a name="contact-us-for-help"></a>도움이 필요한 경우 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>Azure Cost Management에 대해 자세히 알아보세요.

- [Azure Cost Management 및 청구 설명서](https://docs.microsoft.com/azure/cost-management-billing)
