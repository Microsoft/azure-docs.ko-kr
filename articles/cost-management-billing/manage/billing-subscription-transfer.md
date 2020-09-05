---
title: Azure 구독의 청구 소유권 양도
description: Azure 구독의 청구 소유권을 다른 계정으로 양도하는 방법을 설명하고 그 프로세스에 대한 몇 가지 질문과 대답(FAQ)을 제공합니다.
keywords: azure 구독 양도, 구독 양도 azure, azure 구독을 다른 계정에 양도, azure 구독 소유자 변경, azure 구독을 다른 계정으로 양도, azure 청구 양도
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 27ae816e3930ee0e5948c2daae40a0961a6cf474
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685037"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Azure 구독의 청구 소유권을 다른 계정에 양도

곧 퇴사하거나 구독 요금이 청구되는 계정을 다른 계정으로 변경하려는 경우 Azure 구독의 청구 소유권을 양도해야 합니다. 청구 소유권을 다른 계정으로 양도하면 청구 작업에 대한 새 계정 권한이 관리자에게 제공됩니다. 관리자는 지불 방법을 변경하고 요금을 보고 구독을 취소할 수 있습니다.

청구 소유권을 유지하면서 구독 유형만 변경하려면 [Azure 구독을 다른 제안으로 전환](switch-azure-offer.md)을 참조하세요. 구독의 리소스에 액세스할 수 있는 사용자를 제어하려면 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

EA(기업계약) 고객인 경우 엔터프라이즈 관리자는 계정 간에 구독의 청구 소유권을 양도할 수 있습니다. 자세한 내용은 [EA(기업계약) 구독의 청구 소유권 양도](#EA)를 참조하세요.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Azure 구독의 청구 소유권 양도

1. 양도하려는 구독이 있는 청구 계정의 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 본인이 관리자인지 확인하려면 [질문과 대답](#faq)을 참조하세요.

1. **Cost Management + 청구**에서 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)

1. 왼쪽 창에서 **구독**을 선택합니다. 액세스 권한에 따라 청구 범위를 선택하고 **구독** 또는 **Azure 구독**을 선택해야 할 수도 있습니다.

1. 양도하려는 구독의 **청구 소유권 양도**를 선택합니다.

   ![양도할 구독 선택](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)

1. 구독의 새 소유자가 될 계정의 청구 관리자인 사용자의 이메일 주소를 입력합니다.

1. 구독을 다른 Azure AD 테넌트의 계정으로 양도하는 경우 구독을 새 계정의 테넌트로 이전할지 여부를 선택합니다. 자세한 내용은 [다른 Azure AD 테넌트의 계정으로 구독 양도](#transfer-a-subscription-to-another-azure-ad-tenant-account)를 참조하세요.

    > [!IMPORTANT]
    >
    > 구독을 새 계정의 Azure AD 테넌트로 이동하기로 선택하면 구독의 리소스에 액세스하기 위한 모든 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 영구적으로 제거됩니다. 양도 요청을 수락하는 새 계정의 사용자에게만 구독의 리소스를 관리할 수 있는 권한이 제공됩니다. 자세한 내용은 [다른 Azure AD 테넌트 계정으로 구독 양도](#transfer-a-subscription-to-another-azure-ad-tenant-account) 섹션을 참조하세요. 또는 구독을 새 계정의 테넌트로 양도하지 않고도 **구독 Azure AD 테넌트**의 확인란을 선택 취소하여 청구 소유권을 양도할 수 있습니다. 이렇게 하면 Azure 리소스에 액세스하기 위한 기존 Azure 역할 할당이 유지됩니다.

    ![양도 요청 보내기 페이지](./media/billing-subscription-transfer/billing-send-transfer-request.PNG)

1. **이전 요청 보내기**를 선택합니다.

1. 사용자는 이전 요청을 검토하는 지침이 포함된 이메일을 받습니다.

   ![받는 사람에게 전송된 구독 양도 이메일](./media/billing-subscription-transfer/billing-receiver-email.png)

1. 전송 요청을 승인하려면 사용자는 이메일의 링크를 선택하고 지침을 따릅니다. 그런 다음, 사용자는 구독 요금을 결제하는 데 사용할 결제 방법을 선택합니다. 사용자는 Azure 계정이 없는 경우 새 계정에 가입해야 합니다.

   ![첫 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)

   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)

   ![두 번째 구독 양도 웹 페이지](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)

1. 성공! 구독이 이제 양도됩니다.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>다른 Azure AD 테넌트 계정으로 구독 양도

Azure에 가입하면 Azure AD(Active Directory) 테넌트가 자동으로 만들어집니다. 테넌트는 계정을 나타냅니다. 테넌트를 사용하여 구독 및 리소스에 대한 액세스를 관리합니다.

새 구독을 만들면 계정의 Azure AD 테넌트에 호스팅됩니다. 다른 사람에게 구독 또는 해당 리소스에 대한 액세스 권한을 제공하려면 테넌트에 가입하도록 초대해야 합니다. 이렇게 하면 구독과 리소스에 대한 액세스를 제어하는 데 도움이 됩니다.

구독의 청구 소유권을 다른 Azure AD 테넌트의 계정에 양도할 때 구독을 새 계정의 테넌트에 양도할 수 있습니다. 이렇게 하면 구독 및 구독의 리소스를 관리하는 [Azure 역할이 할당된](../../role-based-access-control/role-assignments-portal.md) 모든 사용자, 그룹 또는 서비스 주체의 액세스 권한이 사라집니다. 양도 요청을 수락하는 새 계정의 사용자에게만 리소스를 관리할 수 있는 액세스 권한이 제공됩니다. 권한을 상실한 사용에 대한 액세스를 제공하려면 새 소유자가 이러한 사용자를 구독에 수동으로 추가해야 합니다. 자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도(미리 보기)](../../role-based-access-control/transfer-subscription.md)를 참조하세요.


## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio 및 파트너 네트워크 구독 양도

Visual Studio 및 Microsoft 파트너 네트워크 구독에는 월간 반복 Azure 크레딧이 연결되어 있습니다. 이러한 구독을 양도하면 대상 청구 계정에서 크레딧을 사용할 수 없습니다. 구독에서는 대상 청구 계정의 크레딧을 사용합니다. 예를 들어 Bob이 9월 9일에 Visual Studio Enterprise 구독을 Jane의 계정으로 양도하고 Jane이 양도를 수락한다고 가정하겠습니다. 양도가 완료되면 구독에서는 Jane 계정의 크레딧을 사용하여 구독을 시작합니다. 크레딧은 매월 9일에 다시 설정됩니다.


<a id="EA"></a>

## <a name="transfer-ea-subscription-billing-ownership"></a>EA 구독 청구 소유권 양도

엔터프라이즈 관리자는 등록 내 계정 간에 구독 소유권을 양도할 수 있습니다. 자세한 내용은 EA 포털에서 [계정 소유자 변경](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#change-account-owner)을 참조하세요.

## <a name="next-steps-after-accepting-billing-ownership"></a>청구 소유권을 수락한 후의 다음 단계

Azure 구독의 청구 소유권을 수락한 경우 다음 단계를 검토하는 것이 좋습니다.

1. 서비스 관리자, 공동 관리자 및 Azure 역할 할당을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](add-change-subscription-administrator.md) 및 [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
1. 다음을 비롯한 이 구독의 서비스와 연결된 자격 증명을 업데이트합니다.
   1. 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../../cloud-services/cloud-services-certs-create.md)
   1. 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../../storage/common/storage-create-storage-account.md)를 참조하세요.
   1. Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.
1. 파트너와 함께 작업하는 경우 이 구독에서 파트너 ID를 업데이트하는 것이 좋습니다. [Azure Portal](https://portal.azure.com)에서 파트너 ID를 업데이트할 수 있습니다. 자세한 내용은 [Azure 계정에 파트너 ID 연결](link-partner-id.md)을 참조하세요.

<a id="supported"></a>

## <a name="supported-subscription-types"></a>지원되는 구독 유형

Azure Portal의 구독 양도 기능은 아래에 나열된 유형의 구독에서 사용할 수 있습니다. 현재 [평가판](https://azure.microsoft.com/offers/ms-azr-0044p/) 또는 [AIO(Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) 구독은 양도할 수 없습니다. 해결 방법을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. 지원 플랜과 같은 다른 구독을 양도하려면 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)\*
- [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise(MPN) 구독자](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure 플랜](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*

\* [EA Portal을 통해](#EA).

\*\* Azure 웹 사이트에서 가입하는 동안 생성된 계정에만 지원됩니다.

<a id="faq"></a>

## <a name="frequently-asked-questions-faq-for-senders"></a>보낸 사람에 대한 FAQ(질문과 대답)

다음 FAQ는 Azure 구독의 청구 소유권을 다른 계정에 양도하는 사용자에게 적용됩니다.

### <a name="who-is-a-billing-administrator-of-an-account"></a><a name="whoisaa"></a> 계정의 청구 관리자는 누구인가요?

청구 관리자는 계정의 청구를 관리하는 권한을 가진 사용자입니다. 이들에게는 [Azure Portal](https://portal.azure.com)에서 청구 정보에 액세스하고, 구독 만들기, 청구서 보기 및 결제, 결제 방법 업데이트와 같은 다양한 청구 작업을 수행할 수 있는 권한이 부여됩니다.

본인이 청구 관리자인 계정을 확인하려면 다음 단계를 수행합니다.

1. [Azure Portal의 Cost Management + 청구 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview)로 이동합니다.
1. 왼쪽 창에서 **모든 청구 범위**를 선택합니다.
1. 구독 페이지에는 본인이 청구 관리자인 구독이 모두 나열됩니다.

구독에 대한 계정 관리자를 잘 모를 경우 다음 단계를 사용하여 확인하세요.

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)를 방문하세요.
1. 확인하려는 구독을 선택한 다음 **설정**에서 확인합니다.
1. **속성**을 선택합니다. 구독의 계정 관리자는 **계정 관리자** 상자에 표시됩니다.

### <a name="does-everything-transfer-including-resource-groups-vms-disks-and-other-running-services"></a>모든 것이 양도되나요? 양도 항목에 리소스 그룹, VM, 디스크 및 기타 실행 중인 서비스가 포함되나요?

VM, 디스크, 웹 사이트 등의 모든 리소스가 새 계정에 양도됩니다. 그러나 구독을 다른 Azure AD 테넌트의 계정에 양도하는 경우 구독에 대한 [관리자 역할](add-change-subscription-administrator.md) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)은 [양도되지 않습니다](#transfer-a-subscription-to-another-azure-ad-tenant-account). 또한 [앱 등록](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md) 및 다른 테넌트 관련 서비스는 구독과 함께 양도되지 않습니다.

### <a name="can-i-transfer-ownership-to-an-account-in-another-countryregion"></a>소유권을 다른 국가/지역의 계정에 양도할 수 있나요?
안타깝지만 국가/지역 간 양도는 Azure Portal에서 수행할 수 없습니다. 국가/지역 간에 구독을 양도하려면 [고객 지원팀에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.

### <a name="i-am-an-administrator-on-two-accounts-can-i-transfer-a-subscription-from-one-of-my-accounts-to-another"></a>저는 두 계정의 관리자입니다. 한 계정에서 다른 계정으로 구독을 양도할 수 있나요?
예, 계정 간에 구독을 양도할 수 있습니다. 두 계정은 개념적으로 서로 다른 두 사용자의 계정으로 간주되므로 위의 단계에 따라 계정 간에 구독을 양도할 수 있습니다.

### <a name="does-a-subscription-transfer-result-in-any-service-downtime"></a>구독 양도로 인해 서비스 가동 중지 시간이 발생합니까?

구독을 동일한 Azure AD 테넌트의 계정으로 양도하는 경우 구독에서 실행 중인 리소스에 영향을 주지 않습니다. 그러나 PowerShell에 저장된 컨텍스트 정보는 업데이트되지 않으므로 제거하거나 설정을 변경해야 할 수 있습니다. 구독을 다른 테넌트의 계정에 양도하고 구독을 해당 테넌트로 이전하기로 결정하는 경우 구독의 리소스를 관리하기 위한 [Azure 역할이 할당된](../../role-based-access-control/role-assignments-portal.md) 모든 사용자, 그룹 및 서비스 주체의 액세스 권한이 사라집니다. 서비스 가동 중지 시간이 발생할 수 있습니다.

### <a name="can-users-in-new-account-access-usage-and-billing-history"></a>새 계정의 사용자가 사용량 및 청구 내역에 액세스할 수 있나요?

새 계정의 사용자에게 제공되는 유일한 정보는 구독에 대한 마지막 월의 비용입니다. 나머지 사용량 및 청구 내역은 구독과 함께 양도되지 않습니다.

### <a name="how-do-i-migrate-data-and-services-for-my-azure-subscription-to-new-subscription"></a>내 Azure 구독에 대한 데이터 및 서비스를 새 구독으로 마이그레이션하려면 어떻게 해야 하나요?

구독 소유권을 양도할 수 없는 경우 리소스를 수동으로 마이그레이션할 수 있습니다. [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

### <a name="if-i-transfer-a-visual-studio-or-microsoft-partner-network-subscription-does-my-credit-carry-forward-with-the-subscription-in-the-new-account"></a>Visual Studio 또는 Microsoft 파트너 네트워크 구독을 양도하는 경우 크레딧이 구독과 함께 새 계정에 양도되나요?

아니요, 새 계정에서 크레딧을 사용할 수 없습니다. 양도 요청을 수락하려는 사용자는 Visual Studio 라이선스가 있어야만 양도 요청을 수락할 수 있습니다. 구독에서는 사용자 계정에 제공되는 Visual Studio 크레딧을 사용합니다. 자세한 내용은 [Visual Studio 및 파트너 네트워크 구독 양도](#transfer-visual-studio-and-partner-network-subscriptions)를 참조하세요.


## <a name="frequently-asked-questions-faq-for-recipients"></a>수신자에 대한 FAQ(질문과 대답)

다음 FAQ는 다른 계정의 Azure 구독 청구 소유권을 수락하는 사용자에게 적용됩니다.

### <a name="if-i-take-over-billing-ownership-of-a-subscription-from-another-account-do-users-in-that-account-continue-to-have-access-to-my-resources"></a>다른 계정의 구독 청구 소유권을 양도받으면 해당 계정의 사용자가 내 리소스에 계속 액세스할 수 있나요?

예. 그러나 [관리자 역할](add-change-subscription-administrator.md) 및 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 제거될 수 있습니다. 계정이 구독의 테넌트가 아닌 Azure AD 테넌트에 있고 양도 요청을 보낸 사용자가 구독을 사용자 계정의 테넌트로 이동하는 경우 액세스가 손실됩니다. 구독의 리소스에 액세스할 수 있는 Azure 역할이 할당된 사용자를 보려면 다음 단계를 수행합니다.

1. [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다.
1. 확인하려는 구독을 선택한 다음, 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. 페이지 맨 위에서 **역할 할당**을 선택합니다. 역할 할당 페이지에는 구독에 액세스할 수 있는 모든 사용자가 나열됩니다.

양도 과정에서 [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)이 제거되더라도 원래 소유자 계정에 속한 사용자는 다음을 비롯한 다른 보안 메커니즘을 통해 구독에 계속 액세스할 수도 있습니다.

* 구독 리소스에 대한 관리자 권한을 사용자에게 부여하는 관리 인증서. 자세한 내용은 [Azure 용 관리 인증서 만들기 및 업로드](../../cloud-services/cloud-services-certs-create.md)를 참조하세요.
* 스토리지와 같은 서비스에 대한 액세스 키. 자세한 내용은 [Azure Storage 계정 정보](../../storage/common/storage-create-storage-account.md)를 참조하세요.
* Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

받는 사람은 리소스에 액세스를 제한해야 하는 경우 서비스와 연결된 암호 업데이트를 고려해야 합니다. 다음 단계를 사용하여 대부분의 리소스를 업데이트할 수 있습니다.

  1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
  2. 허브 메뉴에서 **모든 리소스**를 선택합니다.
  3. 리소스를 선택합니다.
  4. 리소스 페이지에서 **설정**을 선택합니다. 여기서 기존 암호를 보고 업데이트할 수 있습니다.

### <a name="if-i-take-over-the-billing-ownership-of-a-subscription-in-the-middle-of-the-billing-cycle-do-i-have-to-pay-for-the-entire-billing-cycle"></a>청구 주기 도중에 구독의 청구 소유권을 양도받으면 전체 청구 주기의 요금을 결제해야 하나요?

양도된 시점 이후부터 보고되는 사용량에 대한 요금을 결제할 책임은 양수인의 계정에 있습니다. 양도하기 전에 발생하지만 나중에 보고되는 일부 사용량이 있을 수 있습니다. 사용량은 양수인 계정의 청구서에 포함됩니다.

### <a name="can-i-use-a-different-payment-method"></a>다른 결제 방법을 사용할 수 있나요?

예. 양도 요청을 수락하는 과정에서 계정에 연결된 기존 결제 방법을 선택하거나 새 결제 방법을 추가할 수 있습니다.

### <a name="how-can-i-transfer-ownership-of-my-enterprise-agreement-ea-subscription-account-ownership-if-the-original-account-owner-is-no-longer-with-the-organization"></a>원래 계정 소유자가 더 이상 조직에 없는 경우 내 EA(기업계약) 구독 계정 소유권의 소유권을 양도하려면 어떻게 해야 하나요?

엔터프라이즈 관리자는 원래 계정 소유자가 더 이상 조직의 일부가 아닌 경우에도 모든 계정에 대한 계정 소유권을 업데이트할 수 있습니다. 이러한 작업은 EA 포털에서 [모든 구독에 대한 계정 소유권 전송](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)에 대한 지침에 따라 수행할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="why-dont-i-see-the-transfer-subscription-button"></a><a id="no-button"></a> "구독 양도" 단추가 보이지 않는 이유는 무엇인가요?

현재 청구 계정에는 셀프 서비스 구독 양도를 사용할 수 없습니다. 현재는 Azure Portal에서 EA(기업계약) 계정의 구독 청구 소유권을 양도할 수 없습니다. 또한 Microsoft 담당자와 함께 만든 Microsoft 고객 계약 계정은 청구 소유권 양도를 지원하지 않습니다.

### <a name="why-doesnt-my-subscription-type-support-transfer"></a><a id="no-button"></a> 내 구독 유형이 양도를 지원하지 않는 이유는 무엇인가요?

일부 구독 유형은 청구 소유권 양도를 지원하지 않습니다. 양도를 지원하는 구독 유형 목록을 보려면 [지원되는 구독 유형](#supported-subscription-types)을 참조하세요.

### <a name="why-am-i-receiving-an-access-denied-error-when-i-try-to-transfer-billing-ownership-of-a-subscription"></a><a id="no-button"></a> 구독의 청구 소유권을 양도하려고 할 때 액세스 거부 오류가 발생하는 이유는 무엇인가요?

Microsoft Azure 플랜 구독을 전송하려고 시도하는데 필요한 권한이 없는 경우 이 오류가 발생합니다. Microsoft Azure 플랜 구독을 양도하려면 구독 요금이 청구되는 청구서 섹션의 소유자 또는 기여자여야 합니다. 자세한 내용은 [청구서 섹션에 대한 구독 관리](understand-mca-roles.md#manage-subscriptions-for-invoice-section)를 참조하세요.


## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 서비스 관리자, 공동 관리자 및 Azure 역할 할당을 검토하고 업데이트합니다. 자세한 내용은 [Azure 구독 관리자 추가 또는 변경](add-change-subscription-administrator.md) 및 [Azure Portal을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
