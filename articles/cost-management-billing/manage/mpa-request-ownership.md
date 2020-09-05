---
title: MPA(Microsoft 파트너 계약)에 대한 Azure 구독의 청구 소유권 가져오기
description: 다른 사용자에게 Azure 구독의 청구 소유권을 요청하는 방법을 알아봅니다.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: eef36726db735cfdd42f5a103073303cf3b3a632
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684952"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>MPA 계정에 대한 Azure 구독의 청구 소유권 가져오기

관리되는 서비스 및 Azure 사용에 대해 통합된 단일 청구서를 제공하기 위해 CSP(클라우드 솔루션 공급자)는 Direct EA(기업계약)를 사용하여 고객의 Azure 구독에 대한 청구 소유권을 얻을 수 있습니다.

이 기능은 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp)로 인증된 CSP 직접 청구 파트너에 대해서만 사용할 수 있습니다. Microsoft 거버넌스 및 정책의 영향을 받을 수 있으며 특정 고객에 대한 검토 및 승인이 필요할 수 있습니다.

청구 소유권을 요청하려면 **글로벌 관리** 또는 **관리 에이전트** 역할이 있어야 합니다. 자세히 알아보려면 [파트너 센터 - 사용자 역할 및 사용 권한 할당](https://docs.microsoft.com/partner-center/permissions-overview)을 참조하세요.

이 문서는 Microsoft 파트너 계약에 대한 청구 계정에 적용됩니다. 이러한 계정은 새 상거래 환경에서 고객에 대한 청구를 관리하기 위해 CSP(클라우드 솔루션 공급자)에 대해 만들어집니다. 새 환경은 MCA(Microsoft 고객 계약)를 수락하고 Azure 플랜이 있는 고객을 한 명 이상 보유한 파트너에만 사용할 수 있습니다. [Microsoft 파트너 계약에 액세스할 수 있는지 확인하세요](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>필수 구성 요소

1. 고객과 [재판매인 관계](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer)를 설정합니다. [CSP 지역 권한 부여 개요](https://docs.microsoft.com/partner-center/regional-authorization-overview)를 확인하여 고객과 파트너 테넌트가 동일한 권한 있는 지역 내에 있는지 확인합니다.
1. [고객이 Microsoft 고객 계약에 동의했는지 확인합니다](https://docs.microsoft.com/partner-center/confirm-customer-agreement).
1. 고객에 대한 [Azure 플랜](https://docs.microsoft.com/partner-center/purchase-azure-plan)을 설정합니다. 고객이 여러 재판매인을 통해 구매하는 경우 고객과 재판매인의 각 조합에 대해 Azure 플랜을 설정해야 합니다.

## <a name="request-billing-ownership"></a>청구 소유권 요청

1. CSP 테넌트에서 CSP 관리 에이전트 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.  
    ![비용 관리 + 청구에 대한 Azure Portal 검색을 보여 주는 스크린샷](./media/mpa-request-ownership/search-cmb.png)
1. 왼쪽에서 **고객**을 선택한 다음, 목록에서 고객을 선택합니다.  
    [![고객 선택을 보여 주는 스크린샷](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. 왼쪽 아래에서 **전송 요청**을 선택한 다음, **새 요청 추가**를 선택합니다.  
    [![전송 요청 선택을 보여 주는 스크린샷](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. 고객 조직에서 전송 요청을 수락하는 사용자의 이메일 주소를 입력합니다. 사용자는 기업계약의 계정 소유자여야 합니다. **이전 요청 보내기**를 선택합니다.  
    [![전송 요청 전송을 보여주는 스크린샷](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. 사용자는 이전 요청을 검토하는 지침이 포함된 이메일을 받습니다.  
    ![전송 요청 이메일 검토를 보여 주는 스크린샷](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. 전송 요청을 승인하려면 사용자는 이메일의 링크를 선택하고 지침을 따릅니다.  
    [![검토 전송 요청을 보여 주는 스크린샷](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) 사용자가 Azure 제품을 전송하려는 청구 계정을 선택할 수 있습니다. 이를 선택하면 전송할 수 있는 적격 제품이 표시됩니다. **참고:** 사용하지 않도록 설정된 구독은 전송할 수 없으며, 해당하는 경우 "이전할 수 없는 Azure 제품" 목록에 표시됩니다. 전송될 Azure 제품이 선택되면 **유효성 검사**를 선택합니다.
1. **전송 유효성 검사 결과** 영역에서는 전송할 Azure 제품의 영향을 보여 줍니다. 가능한 상태는 다음과 같습니다.
    * **통과** - 이 Azure 제품에 대한 유효성 검사가 통과되었으며 전송될 수 있습니다.
    * **경고** - 선택한 Azure 제품에 대한 경고가 있습니다. 제품을 계속 전송할 수 있지만 이렇게 하면 사용자가 완화 작업을 수행하려는 경우 사용자가 알고 있어야 하는 영향을 받을 수 있습니다. 예를 들어 전송되는 Azure 구독은 RI에서 혜택을 받고 있습니다. 전송 후에는 구독이 더 이상 해당 혜택을 받지 않습니다. 절감액을 최대화하려면 RI를 해당 혜택을 사용할 수 있는 다른 구독과 연결해야 합니다. 대신 사용자는 선택 페이지로 돌아가서 이 Azure 구독을 선택 취소하도록 선택할 수도 있습니다.
    * **실패** - 오류가 발생하여 선택한 Azure 제품을 전송할 수 없습니다. 사용자가 선택한 다른 Azure 제품을 전송하려면 선택 페이지로 돌아가서 이 제품을 선택 취소해야 합니다.  
    ![유효성 검사 환경을 보여 주는 스크린샷](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>전송 요청 상태 확인

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.  
    ![비용 관리 + 청구에 대한 Azure Portal 검색을 보여 주는 스크린샷](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. 왼쪽에서 **고객**을 선택합니다.  
    [![고객 선택을 보여 주는 스크린샷](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. 목록에서 전송 요청을 보낸 고객을 선택합니다.
1. 왼쪽 아래에서 **전송 요청**을 선택합니다. 전송 요청 페이지에는 다음 정보가 표시됩니다. [![전송 요청 목록을 보여주는 스크린샷](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

   |열|정의|
   |---------|---------|
   |요청 날짜|전송 요청이 전송된 날짜입니다.|
   |받는 사람|청구 소유권 전송 요청을 보낸 사용자의 이메일 주소입니다.|
   |만료 날짜|요청이 만료되는 날짜|
   |상태|전송 요청의 상태|

    전송 요청은 다음 상태 중 하나를 가질 수 있습니다.

   |상태|정의|
   |---------|---------|
   |진행 중|사용자가 전송 요청을 수락하지 않았습니다.|
   |처리 중|사용자가 전송 요청을 승인했습니다. 사용자가 선택한 구독에 대한 청구가 계정으로 이전되고 있습니다.|
   |Completed| 사용자가 선택한 구독에 대한 청구가 계정으로 이전되었습니다.|
   |완료되었지만 오류 발생|요청을 완료했지만 사용자가 선택한 일부 구독에 대한 청구를 이전하지 못했습니다.|
   |만료됨|사용자가 제때에 요청을 수락하지 않아서 만료되었습니다.|
   |취소됨|전송 요청에 대한 액세스 권한이 있는 다른 사용자가 요청을 취소했습니다.|
   |거부함|사용자가 전송 요청을 거부했습니다.|

1. 세부 정보를 볼 전송 요청을 선택합니다. 전송 세부 정보 페이지에는 다음 정보가 표시됩니다. [![전송된 구독 목록을 보여주는 스크린샷](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

   |열  |정의|
   |---------|---------|
   |전송 요청 ID|전송 요청에 대한 고유 ID입니다. 지원 요청을 제출하는 경우 지원 요청을 신속하게 처리할 수 있도록 Azure 지원에 ID를 공유합니다.|
   |전송 요청한 날짜|전송 요청이 전송된 날짜입니다.|
   |전송 요청한 사람|전송 요청을 보낸 사용자의 이메일 주소입니다.|
   |전송 요청 만료 날짜| 전송 요청이 만료되는 날짜입니다.|
   |받는 사람의 이메일 주소|청구 소유권 전송 요청을 보낸 사용자의 이메일 주소입니다.|
   |받는 사람에게 보낸 전송 링크|전송 요청을 검토하도록 사용자에게 보낸 URL입니다.|

## <a name="supported-subscription-types"></a>지원되는 구독 유형

아래에 나열된 구독 유형의 청구 소유권을 요청할 수 있습니다.

* [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Microsoft 기업계약](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* 개발/테스트 구독을 지원 티켓을 통해 EA Enterprise 제품으로 변환해야 합니다. Enterprise 개발/테스트 구독은 전송된 후 종량제 요금으로 청구됩니다. 고객의 EA를 통한 Enterprise 개발/테스트 제품을 통해 제공되는 모든 할인은 CSP 파트너에게 제공되지 않습니다.

## <a name="additional-information"></a>추가 정보

다음 섹션에서는 구독 이전에 대한 추가 정보를 제공합니다.

### <a name="no-service-downtime"></a>서비스 가동 중지 시간이 없습니다.

구독에 포함된 Azure 서비스는 계속해서 중단 없이 실행됩니다. 사용자가 이전하기 위해 선택하는 Azure 구독에 대한 청구 관계만 전환됩니다.

### <a name="disabled-subscriptions"></a>사용하지 않도록 설정된 구독

사용하지 않도록 설정된 구독은 이전할 수 없습니다. 청구 소유권을 이전하려면 구독이 활성 상태여야 합니다.

### <a name="azure-resources-transfer"></a>Azure 리소스 이전

VM, 디스크 및 웹 사이트와 같은 구독의 모든 리소스가 이전됩니다.

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 제품 이전

CSP(클라우드 솔루션 공급자)가 관리하는 구독에 사용할 수 있는 Azure Marketplace 제품은 해당 구독과 함께 전송됩니다. CSP를 사용할 수 없는 Azure Marketplace 제품을 포함하는 구독은 전송할 수 없습니다.

### <a name="azure-reservations-transfer"></a>Azure Reservations 이전

Azure Reservations는 구독과 함께 자동으로 이동되지 않습니다. 다른 구독의 EA에서 예약을 유지하거나 [예약을 취소하고](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations) CSP에서 예약을 다시 구매할 수 있습니다.

### <a name="access-to-azure-services"></a>Azure 서비스에 대한 액세스

[Azure RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 사용하여 할당된 기존 사용자, 그룹 또는 서비스 사용자에 대한 액세스는 전환 중에 영향을 받지 않습니다. 파트너는 구독에 대한 새 RBAC 액세스 권한을 얻지 못합니다.

파트너는 고객과 협력하여 구독에 대한 액세스 권한을 얻어야 합니다. 파트너는 [Admin on Behalf Of - AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 또는 [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) 액세스 오픈 지원 티켓을 가져와야 합니다.

### <a name="azure-support-plan"></a>Azure 지원 플랜

Azure 지원은 구독과 함께 이전되지 않습니다. 사용자가 모든 Azure 구독을 이전하는 경우 지원 계획을 취소하도록 요청하세요. 전송 후 CSP 파트너는 지원을 담당합니다. 고객은 모든 지원 요청에 대해 CSP 파트너와 함께 작업해야 합니다.  

### <a name="charges-for-transferred-subscription"></a>이전된 구독의 요금

구독의 원래 청구 소유자는 이전이 완료된 시점까지 보고된 모든 요금에 대한 책임이 있습니다. 이전 시점부터 보고된 요금에 대해 지불 책임이 있습니다. 이전하기 전에 발생하지만 나중에 보고되는 일부 요금이 있을 수 있습니다. 이러한 요금은 청구서에 표시됩니다.

### <a name="cancel-a-transfer-request"></a>전송 요청 취소

요청이 승인되거나 거부될 때까지는 전송 요청을 취소할 수 있습니다. 전송 요청을 취소하려면 [전송 세부 정보 페이지](#check-the-transfer-request-status)로 이동하여 페이지 아래쪽에서 취소를 선택합니다.

### <a name="software-as-a-service-saas-transfer"></a>SaaS(Software as a Service) 전송

SaaS 제품은 구독과 함께 전송되지 않습니다. [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 SaaS 제품의 청구 소유권을 이전하도록 사용자에게 요청하세요. 사용자는 청구 소유권과 함께 리소스 소유권도 이전할 수 있습니다. 리소스 소유권을 사용하면 제품의 세부 정보 삭제 및 보기와 같은 관리 작업을 수행할 수 있습니다. 리소스 소유권을 이전하기 위해서는 사용자가 해당 SaaS 제품에 대한 리소스 소유자여야 합니다.

### <a name="additional-approval-for-certain-customers"></a>특정 고객에 대한 추가 승인

고객 전환 요청 중 일부에는 고객의 현재 엔터프라이즈 등록 구조의 특성으로 인해 Microsoft와의 추가 검토 프로세스가 필요할 수 있습니다. 고객에게 초대를 보내려고 할 때 파트너에게 이러한 요구 사항을 알려 줍니다. 파트너는 파트너 개발 관리자 및 고객의 계정 팀과 협력하여 이 검토 프로세스를 완료하도록 요청됩니다.

### <a name="azure-subscription-directory"></a>Azure 구독 디렉터리

전송되는 Azure 구독의 디렉터리는 CSP 관계를 설정하는 동안 선택한 고객의 디렉터리와 일치해야 합니다.

이러한 두 디렉터리가 일치하지 않으면 구독을 전송할 수 없습니다. Azure 구독의 디렉터리를 선택하여 고객과 새 CSP 재판매인 관계를 설정하거나 고객 CSP 관계 디렉터리와 일치하도록 Azure 구독의 디렉터리를 변경해야 합니다. 자세한 내용은 [Azure AD 디렉터리에 기존 구독 연결](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory)을 참조하세요.

### <a name="ea-subscription-in-the-non-orgnization-directory"></a>비조직 디렉터리의 EA 구독

비조직 디렉터리의 EA 구독은 해당 디렉터리에 CSP와의 재판매인 관계가 있는 한 전송될 수 있습니다. 디렉터리에 재판매인 관계가 없는 경우 디렉터리에 파트너 관계를 수락할 수 있는 *전역 관리자*로서의 조직 사용자가 있어야 합니다. 사용자 이름의 도메인 이름 부분은 초기 기본 도메인 이름 "[도메인 이름]이어야 합니다. onmicrosoft.com" 또는 "contoso.com"과 같이 확인되고 페더레이션되지 않은 사용자 지정 도메인 이름  

디렉터리에 새 사용자를 추가하려면 [빠른 시작: Azure Active Directory에 새 사용자를 추가하여 AAD 디렉터리에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조하세요.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft 파트너 계약에 대한 액세스 확인

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

* Azure 구독의 청구 소유권이 사용자에게 이전됩니다. [Azure Portal](https://portal.azure.com)에서 이러한 구독에 대한 요금을 추적합니다.
* 전송된 Azure 구독에 대한 액세스 권한을 얻으려면 고객에게 문의하세요. [RBAC를 사용하여 Azure 리소스에 대한 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
