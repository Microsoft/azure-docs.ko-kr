---
title: Microsoft 고객 계약에 대한 청구 설정 - Azure
description: Microsoft 고객 계약에 대한 청구 계정을 설정하는 방법을 알아봅니다. 설치에 필요한 필수 구성 요소를 확인하고 사용 가능한 추가 리소스를 살펴봅니다.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: afa95609e0bcb879b6ad4b3b50b5dff5feb9a9f7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682793"
---
# <a name="set-up-your-billing-account-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 청구 계정 설정

기업계약 등록이 만료되었거나 곧 만료되는 경우 Microsoft 고객 계약에 서명하여 등록을 갱신할 수 있습니다. 이 문서에서는 설정 후 기존 청구에서 변경되는 내용을 설명하고 새 청구 계정을 설정하는 과정을 안내합니다. 갱신 단계는 다음과 같습니다.

1. 새 Microsoft 고객 계약을 수락합니다. Microsoft 현장 담당자와 협력하여 세부 정보를 이해하고 새 계약에 동의합니다.
2. 새 Microsoft 고객 계약에 대해 만들어진 새 청구 계정을 설정합니다.

청구 계정을 설정하려면 Azure 구독 청구를 기업계약 등록에서 새 계정으로 전환해야 합니다. 이 설정은 구독에서 실행 중인 Azure 서비스에 영향을 주지 않습니다. 그러나 구독의 요금 청구 관리 방식이 변경됩니다.

- [EA Portal](https://ea.azure.com) 대신 [Azure Portal](https://portal.azure.com)에서 Azure 서비스 및 청구를 관리하게 됩니다.
- 요금에 대한 월별 디지털 청구서를 받게 됩니다. Azure Cost Management + 청구 페이지에서 청구서를 살펴보고 분석할 수 있습니다.
- 기업계약 등록의 부서 및 계정 대신 새 계정의 청구 구조와 범위를 사용하여 청구를 관리하고 구성하게 됩니다.

설정을 시작하기 전에 다음을 수행하는 것이 좋습니다.

- **새 청구 계정 이해**
  - 새 계정은 조직의 청구 방법이 간단합니다. [새 청구 계정에 대한 간략한 개요 보기](../understand/mca-overview.md)
- **설정을 완료하는 데 필요한 액세스 권한 확인**
  - 특정 관리 권한이 있는 사용자만 설정을 완료할 수 있습니다. [설정을 완료하는 데 필요한 액세스 권한](#access-required-to-complete-the-setup)이 있는지 확인하세요.
- **달라지는 청구 계층 구조 이해**
  - 새 청구 계정은 기업계약 등록과 다르게 구성됩니다. [새 계정의 달라지는 청구 계층 구조 이해](#understand-changes-to-your-billing-hierarchy)
- **달라지는 청구 관리자의 액세스 권한 이해**
  - 기업계약 등록의 관리자는 새 계정의 청구 범위에 액세스할 수 있습니다. [이들의 액세스 권한이 어떻게 달라지는지 알아보세요](#changes-to-billing-administrator-access).
- **새 계정으로 대체되는 기업계약 기능 보기**
  - 새 계정의 기능으로 대체되는 기업계약 등록의 기능을 알아보세요.
- **일반적인 질문에 대한 답변 보기**
  - [추가 정보](#additional-information)를 살펴보고 설정에 대해 자세히 알아보세요.

## <a name="access-required-to-complete-the-setup"></a>설정을 완료하는 데 필요한 액세스 권한

설정을 완료하려면 다음 액세스 권한이 필요합니다.

- Microsoft 고객 계약에 서명할 때 만들어진 청구 프로필의 소유자. 청구 프로필에 대해 자세히 알아보려면 [청구 프로필 이해](../understand/mca-overview.md#billing-profiles)를 참조하세요.

- 갱신된 등록의 엔터프라이즈 관리자

### <a name="if-youre-not-an-enterprise-administrator-on-the-enrollment"></a>등록의 엔터프라이즈 관리자가 아닌 경우

등록의 엔터프라이즈 관리자에게 청구 계정 설정을 완료해 달라고 요청할 수 있습니다.

1. Microsoft 고객 계약에 서명할 때 전송된 이메일의 링크를 사용하여 Azure Portal에 로그인합니다.

2. 이메일이 없는 경우 다음 링크를 사용하여 로그인합니다. `<enrollmentNumber>`를 갱신된 기업계약의 등록 번호로 바꿉니다.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. 요청을 보낼 엔터프라이즈 관리자를 선택합니다.

   ![엔터프라이즈 관리자를 초대하는 방법을 보여주는 스크린샷](./media/mca-setup-account/ea-mca-invite-admins.png)

4. **요청 보내기**를 선택합니다.

   관리자는 설치 완료 지침이 포함된 이메일을 받게 됩니다.

### <a name="if-youre-not-an-owner-of-the-billing-profile"></a>청구 프로필의 소유자가 아닌 경우

Microsoft 고객 계약에 서명한 조직 내 사용자는 청구 프로필에 소유자로 추가됩니다. 이 사용자에게 설정을 완료할 수 있도록 소유자로 추가해 달라고 요청합니다.

## <a name="understand-changes-to-your-billing-hierarchy"></a>달라지는 청구 계층 구조 이해

새 청구 계정은 조직의 청구 방법이 간단하면서도 향상된 청구 및 비용 관리 기능을 제공합니다. 다음 다이어그램은 새 청구 계정에서 청구를 구성하는 방법을 설명합니다.

![ea-mca-post-transition-hierarchy 이미지](./media/mca-setup-account/mca-post-transition-hierarchy.png)

1. 청구 계정을 사용하여 Microsoft 고객 계약에 대한 청구를 관리합니다. 엔터프라이즈 관리자는 청구 계정의 소유자가 됩니다. 청구 계정에 대한 자세한 내용은 [청구 계정 이해](../understand/mca-overview.md#your-billing-account)를 참조하세요.
2. 기업계약 등록과 비슷하게 청구 프로필을 사용하여 조직의 청구를 관리합니다. 엔터프라이즈 관리자는 청구 프로필의 소유자가 됩니다. 청구 프로필에 대해 자세히 알아보려면 [청구 프로필 이해](../understand/mca-overview.md#billing-profiles)를 참조하세요.
3. 기업계약 등록의 부서와 비슷하게 청구서 섹션을 사용하여 요구 사항에 따라 비용을 구성합니다. 부서는 청구서 섹션이 되고 부서 관리자는 해당 청구서 섹션의 소유자가 됩니다. 청구서 섹션에 대한 자세한 내용은 [청구서 섹션 이해](../understand/mca-overview.md#invoice-sections)를 참조하세요.
4. 기업계약에서 만든 계정은 새 청구 계정에서 지원되지 않습니다. 계정의 구독은 해당 부서의 각 청구서 섹션에 속합니다. 계정 소유자는 청구서 섹션에 대한 구독을 만들고 관리할 수 있습니다.

## <a name="changes-to-billing-administrator-access"></a>달라지는 청구 관리자 액세스 권한

기업계약 등록의 청구 관리자는 액세스 권한에 따라 새 계정의 청구 범위에 액세스할 수 있습니다. 다음 표에서는 설정 중에 달라지는 액세스 권한에 대해 설명합니다.

| 기존 역할 | 전환 후 역할 |
| --- | --- |
| **엔터프라이즈 관리자(읽기 전용 = 아니요)** | **- 청구 계정 소유자** </br> 청구 계정에 대한 모든 항목 관리 </br> **- 청구 프로필 소유자** </br> 청구 프로필에 대한 모든 항목 관리 </br> **- 모든 청구서 섹션의 청구서 섹션 소유자** </br> 청구서 섹션의 모든 항목 관리 |
| **엔터프라이즈 관리자(읽기 전용 = 예)** | **- 청구 계정 읽기 권한자** </br> 청구 계정의 모든 항목에 대한 읽기 전용 보기</br> **- 청구 프로필 읽기 권한자** </br> 청구 프로필의 모든 항목에 대한 읽기 전용 보기</br>**- 모든 청구서 섹션의 청구서 섹션 읽기 권한자**</br> 청구서 섹션의 모든 항목에 대한 읽기 전용 보기|
| **부서 관리자(읽기 전용 = 아니요)** |**- 해당 부서에 대해 만들어진 청구서 섹션의 청구서 섹션 소유자** </br>청구서 섹션의 모든 항목 관리|
| **부서 관리자(읽기 전용 = 예)**|**- 해당 부서에 대해 만들어진 청구서 섹션의 청구서 섹션 읽기 권한자**</br> 청구서 섹션의 모든 항목에 대한 읽기 전용 보기|
| **계정 소유자** | **- 해당 부서에 대해 만들어진 청구서 섹션의 Azure 구독 작성자** </br>  청구서 섹션에 대한 Azure 구독 만들기|

Microsoft 고객 계약을 수락하는 동안 새 청구 계정에 대한 Azure AD(Active Directory) 테넌트가 선택됩니다. 조직의 테넌트가 없는 경우 새 테넌트가 만들어집니다. 테넌트는 Azure Active Directory 내에서 조직을 나타냅니다. 조직의 글로벌 테넌트 관리자는 테넌트를 사용하여 조직의 애플리케이션 및 데이터 액세스를 관리합니다.

새 계정은 Microsoft 고객 계약에 서명할 때 선택된 테넌트의 사용자만 지원합니다. 기업계약에 대한 관리 권한이 있는 사용자가 테넌트에 포함된 경우 설정 과정에서 이들에게 새 청구 계정에 대한 액세스 권한이 부여됩니다. 이들이 테넌트에 포함되지 않은 경우에는 초대를 받아야만 새 청구 계정에 액세스할 수 있습니다.

사용자를 초대하면 해당 사용자는 테넌트에 게스트 사용자로 추가되고 청구 계정에 대한 액세스 권한을 얻게 됩니다. 사용자를 초대하려면 테넌트에 대한 게스트 액세스를 켜야 합니다. 자세한 내용은 [Azure Active Directory의 게스트 액세스 제어](https://docs.microsoft.com/microsoftteams/teams-dependencies#control-guest-access-in-azure-active-directory)를 참조하세요. 게스트 액세스가 꺼져 있는 경우 테넌트의 글로벌 관리자에게 연락하여 게스트 액세스를 켜 달라고 요청합니다. <!-- Todo - How can they find their global administrator -->

## <a name="view-replaced-features"></a>바뀐 기능 보기

다음 기업계약 기능은 Microsoft 고객 계약에 대한 청구 계정의 새 기능으로 대체되었습니다.

### <a name="enterprise-agreement-accounts"></a>기업계약 계정

기업계약 등록에서 만든 계정은 새 청구 계정에서 지원되지 않습니다. 계정의 구독은 해당 부서에 대해 만들어진 청구서 섹션에 속합니다. 계정 소유자는 Azure 구독 작성자가 되어 청구서 섹션에 대한 구독을 만들고 관리할 수 있습니다.

### <a name="notification-contacts"></a>알림 연락처

알림 연락처에게는 Azure 기업계약에 대한 이메일 통신문이 전달됩니다. 알림 연락처는 새 청구 계정에서 지원되지 않습니다. Azure 크레딧 및 청구서에 대한 이메일은 청구 계정의 청구 프로필에 대한 액세스 권한이 있는 사용자에게 전달됩니다.

### <a name="spending-quotas"></a>지출 할당량

기업계약 등록에서 부서별로 설정된 지출 할당량은 새 청구 계정의 예산으로 바뀝니다. 등록에서 부서별로 설정된 각 지출 할당량에 대한 예산이 생성됩니다. 예산에 대한 자세한 내용은 [자습서: Azure 예산 만들기 및 관리](../costs/tutorial-acm-create-budgets.md)를 참조하세요.

### <a name="cost-centers"></a>비용 센터

기업계약 등록의 Azure 구독에서 설정된 비용 센터는 새 청구 계정으로 이월됩니다. 그러나 부서 및 기업계약 계정에 대한 비용 센터는 지원되지 않습니다.

## <a name="additional-information"></a>추가 정보

다음 섹션에서는 청구 계정 설정에 대한 추가 정보를 제공합니다.

### <a name="no-service-downtime"></a>서비스 가동 중지 시간이 없습니다.

구독에 포함된 Azure 서비스는 계속해서 중단 없이 실행됩니다. Azure 구독의 청구 관계만 전환됩니다. 기존 리소스, 리소스 그룹 또는 관리 그룹에는 영향이 없습니다.

### <a name="user-access-to-azure-resources"></a>Azure 리소스에 대한 사용자 액세스

Azure RBAC(역할 기반 액세스 제어)를 사용하여 설정된 Azure 리소스에 대한 액세스 권한은 전환의 영향을 받지 않습니다.

### <a name="azure-reservations"></a>Azure Reservations

기업계약 등록에 포함된 모든 Azure 예약은 새 청구 계정으로 이전됩니다. 구독에 적용되는 예약 할인은 전환 과정에서 변경되지 않습니다.

### <a name="azure-marketplace-products"></a>Azure Marketplace 제품

기업계약 등록의 모든 Azure Marketplace 제품은 구독과 함께 이전됩니다. 전환 중에 Marketplace 제품의 서비스 액세스 권한은 변경되지 않습니다.

### <a name="support-plan"></a>지원 계획

지원 혜택은 전환의 일부로 이전되지 않습니다. 새 청구 계정에서 새 지원 플랜을 구매하여 Azure 구독에 대한 혜택을 받으세요.

### <a name="past-charges-and-balance"></a>과거 요금 및 잔액

전환 전에 발생한 요금 및 크레딧 잔액은 Azure Portal을 통해 기업계약 등록에서 볼 수 있습니다. <!--Todo - Add a link for this-->

### <a name="when-should-the-setup-be-completed"></a>설정을 완료해야 하는 시기

기업계약 등록이 만료되기 전에 청구 계정 설정을 완료해야 합니다. 등록이 만료되어도 Azure 구독의 서비스는 중단 없이 계속 실행됩니다. 그러나 서비스에 대한 종량제 요금이 청구됩니다.

### <a name="changes-to-the-enterprise-agreement-enrollment-after-the-setup"></a>설정 후 기업계약 등록의 변경 내용

전환 후 기업계약 등록에 대해 만들어진 Azure 구독은 수동으로 새 청구 계정으로 이전할 수 있습니다. 자세한 내용은 [다른 사용자로부터 Azure 구독의 청구 소유권 받기](mca-request-billing-ownership.md)를 참조하세요. 전환 후 구매한 Azure 예약을 이전하려면 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요. 전환 후에도 사용자에게 청구 계정에 대한 액세스 권한을 부여할 수 있습니다. 자세한 내용은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

### <a name="revert-the-transition"></a>전환 되돌리기

전환한 후에는 되돌릴 수 없습니다. Azure 구독 청구를 새로운 청구 계정으로 전환한 후에는 기업계약 등록으로 되돌릴 수 없습니다.

### <a name="closing-your-browser-during-setup"></a>설정 중에 브라우저 종료

**전환 시작**을 클릭하기 전에는 브라우저를 닫아도 됩니다. 이메일에 포함된 링크를 사용하여 설정으로 돌아가서 전환을 시작할 수 있습니다. 전환이 시작된 후에는 브라우저를 닫아도 전환이 계속 실행됩니다. 전환 상태 페이지로 돌아가서 최신 전환 상태를 모니터링하세요. 전환이 완료되면 이메일을 받게 됩니다.

## <a name="complete-the-setup-in-the-azure-portal"></a>Azure Portal에서 설정 완료

설정을 완료하려면 새 청구 계정과 기업계약 등록에 모두 액세스해야 합니다. 자세한 내용은 [청구 계정의 설정을 완료하는 데 필요한 액세스 권한](#access-required-to-complete-the-setup)을 참조하세요.

1. Microsoft 고객 계약에 서명할 때 전송된 이메일의 링크를 사용하여 Azure Portal에 로그인합니다.

2. 이메일이 없는 경우 다음 링크를 사용하여 로그인합니다. `<enrollmentNumber>`를 갱신된 기업계약의 등록 번호로 바꿉니다.

   `https://portal.azure.com/#blade/Microsoft_Azure_EA/EATransitionToMCA/enrollmentId/<enrollmentNumber>`

3. 설정의 마지막 단계에서 **전환 시작**을 선택합니다. 전환 시작을 선택하면 다음과 같은 작업이 수행됩니다.

    ![설정 마법사를 보여주는 스크린샷](./media/mca-setup-account/ea-mca-set-up-wizard.png)

    - 기업계약 계층 구조에 해당하는 청구 계층 구조가 새 청구 계정에 생성됩니다. 자세한 내용은 [달라지는 청구 계층 구조 이해](#understand-changes-to-your-billing-hierarchy)를 참조하세요.
    - 기업계약 등록의 관리자에게는 조직의 요금 청구를 계속 관리할 수 있도록 새 청구 계정에 대한 액세스 권한이 제공됩니다.
    - Azure 구독에 대한 청구는 새 계정으로 전환됩니다. **전환 중에 Azure 서비스에 영향을 주지 않습니다. 서비스는 중단 없이 계속 실행됩니다**.
    - 기존에 보유한 Azure 예약은 혜택 또는 기간에 대한 변경 없이 새 청구 계정으로 이전됩니다.

4. **전환 상태** 페이지에서 전환 상태를 모니터링할 수 있습니다.

   ![전환 상태를 보여주는 스크린샷](./media/mca-setup-account/ea-mca-set-up-status.png)

## <a name="validate-billing-account-setup"></a>청구 계정 설정 확인

 다음 항목을 확인하여 새 청구 계정이 올바르게 설정되었는지 확인합니다.

### <a name="azure-subscriptions"></a>Azure 구독

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/mca-setup-account/search-cmb.png)

3. 청구 계정을 선택합니다. 청구 계정은 **Microsoft 고객 계약** 유형이 됩니다.

4. 왼쪽에서 **Azure 구독**을 선택합니다.

   ![구독 목록을 보여주는 스크린샷](./media/mca-setup-account/mca-subscriptions-post-transition.png)

기업계약 등록에서 새 청구 계정으로 전환된 Azure 구독은 Azure 구독 페이지에 표시됩니다. 일부 구독이 누락되었다고 생각되면 Azure Portal에서 수동으로 구독 청구를 전환합니다. 자세한 내용은 [다른 사용자로부터 Azure 구독의 청구 소유권 받기](mca-request-billing-ownership.md)를 참조하세요.

### <a name="azure-reservations"></a>Azure 예약

기업계약 등록에 포함된 Azure 예약은 혜택 또는 기간에 대한 변경 없이 새 청구 계정으로 이전됩니다. 전환 전에 완료된 트랜잭션은 새 청구 계정에 표시되지 않습니다. 그러나 [Azure 예약 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)를 방문하여 예약의 혜택이 구독에 적용되는지 확인할 수 있습니다.

### <a name="access-of-enterprise-administrators-on-the-billing-account"></a>청구 계정에 대한 엔터프라이즈 관리자의 액세스 권한

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/mca-setup-account/search-cmb.png)

3. **Microsoft 고객 계약**에 대한 청구 계정을 선택합니다.

4. 왼쪽에서 **액세스 제어(IAM)** 를 선택합니다.

   ![전환 후 엔터프라이즈 관리자의 액세스 권한을 보여주는 스크린샷](./media/mca-setup-account/mca-ea-admins-ba-access-post-transition.png)

엔터프라이즈 관리자는 청구 계정 소유자로 나열되고, 읽기 전용 권한을 가진 엔터프라이즈 관리자는 청구 계정 읽기 권한자로 나열됩니다. 엔터프라이즈 관리자의 액세스 권한이 누락되었다고 생각되면 Azure Portal에서 액세스 권한을 부여할 수 있습니다. 자세한 내용은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

### <a name="access-of-enterprise-administrators-on-the-billing-profile"></a>청구 프로필에 대한 엔터프라이즈 관리자의 액세스 권한

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/mca-setup-account/search-cmb.png)

3. 등록에 대해 생성된 청구 프로필을 선택합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 청구 프로필을 선택한 다음, 청구 프로필을 선택합니다.

4. 왼쪽에서 **액세스 제어(IAM)** 를 선택합니다.

   ![전환 후 엔터프라이즈 관리자의 액세스 권한을 보여주는 스크린샷](./media/mca-setup-account/mca-ea-admins-bp-access-post-transition.png)

엔터프라이즈 관리자는 청구 프로필 소유자로 나열되고, 읽기 전용 권한을 가진 엔터프라이즈 관리자는 청구 프로필 읽기 권한자로 나열됩니다. 엔터프라이즈 관리자의 액세스 권한이 누락되었다고 생각되면 Azure Portal에서 액세스 권한을 부여할 수 있습니다. 자세한 내용은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

### <a name="access-of-enterprise-administrators-department-administrators-and-account-owners-on-invoice-sections"></a>청구서 섹션에 대한 엔터프라이즈 관리자, 부서 관리자 및 계정 소유자의 액세스 권한

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/mca-setup-account/search-cmb.png).

3. 청구서 섹션을 선택합니다. 청구서 섹션의 이름은 기업계약 등록의 해당 부서와 동일합니다. 액세스 권한에 따라 청구 계정을 선택해야 할 수도 있습니다. 청구 계정에서 **청구 프로필**을 선택한 다음, **청구서 섹션**을 선택합니다. 청구서 섹션 목록에서 청구서 섹션을 선택합니다.

   ![전환 후 청구서 섹션 목록을 보여주는 스크린샷](./media/mca-setup-account/mca-invoice-sections-post-transition.png)

4. 왼쪽에서 **액세스 제어(IAM)** 를 선택합니다.

    ![전환 후 부서 및 계정 관리자의 액세스 권한을 보여주는 스크린샷](./media/mca-setup-account/mca-department-account-admins-access-post-transition.png)

엔터프라이즈 관리자 및 부서 관리자는 청구서 섹션 소유자 또는 청구서 섹션 읽기 권한자로 나열되고, 부서의 계정 소유자는 Azure 구독 작성자로 나열됩니다. 모든 청구서 섹션에 대해 같은 단계를 반복하여 기업계약 등록의 모든 부서에 대한 액세스 권한을 확인합니다. 부서에 속하지 않은 계정 소유자에게는 **기본 청구서 섹션**이라는 청구서 섹션에 대한 권한이 부여됩니다. 관리자의 액세스 권한이 누락되었다고 생각되면 Azure Portal에서 액세스 권한을 부여할 수 있습니다. 자세한 내용은 [Azure Portal에서 청구 역할 관리](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)를 참조하세요.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

- [새 청구 계정 시작](../understand/mca-overview.md)

- [Microsoft 고객 계약의 청구 계정에서 기업계약 작업 완료](mca-enterprise-operations.md)

- [청구 계정에 대한 액세스 관리](understand-mca-roles.md)
