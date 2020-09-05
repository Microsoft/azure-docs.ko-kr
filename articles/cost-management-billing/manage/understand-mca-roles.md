---
title: Microsoft 고객 계약에 대한 청구 역할 - Azure
description: Azure의 Microsoft 고객 계약에서 청구 계정에 대한 청구 역할에 대해 알아봅니다.
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: f0ace05d6a4df65282bd56cca8e7895b0dc24edf
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686550"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Azure의 Microsoft 고객 계약 관리자 역할 이해

Microsoft 고객 계약의 청구 계정을 관리하려면 다음 섹션에서 설명하는 역할을 사용하세요. 이러한 역할은 리소스에 대한 액세스를 제어하기 위한 Azure의 기본 제공 역할 외에 추가로 제공됩니다. 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

이 문서는 Microsoft 고객 계약에 대한 청구 계정에 적용됩니다. Microsoft 고객 계약에 액세스할 수 있는지 확인하세요.

## <a name="billing-role-definitions"></a>청구 역할 정의

다음 표에서는 청구 계정, 청구 프로필 및 청구서 섹션을 관리하는 데 사용되는 청구 역할에 대해 설명합니다.

|역할|Description|
|---|---|
|청구 계정 소유자|청구 계정에 대한 모든 항목 관리|
|청구 계정 기여자|사용 권한을 제외한 청구 계정의 모든 항목 관리|
|청구 계정 읽기 권한자|청구 계정의 모든 항목에 대한 읽기 전용 보기|
|청구 프로필 소유자|청구 프로필에 대한 모든 항목 관리|
|청구 프로필 기여자|사용 권한을 제외한 청구 프로필의 모든 항목 관리|
|청구 프로필 읽기 권한자|청구 프로필의 모든 항목에 대한 읽기 전용 보기|
|청구서 관리자|청구 프로필에 대한 청구서를 살펴보고 결제|
|청구서 섹션 소유자|청구서 섹션의 모든 항목 관리|
|청구서 섹션 기여자|사용 권한을 제외한 청구서 섹션의 모든 항목 관리|
|청구서 섹션 읽기 권한자|청구서 섹션의 모든 항목에 대한 읽기 전용 보기|
|Azure 구독 작성자|Azure 구독 만들기|

## <a name="billing-account-roles-and-tasks"></a>청구 계정 역할 및 작업

청구 계정을 통해 조직의 요금 청구를 관리할 수 있습니다. 청구 계정을 사용하여 비용을 구성하고, 요금 및 청구서를 모니터링하고, 조직의 청구 액세스를 제어할 수 있습니다. 자세한 내용은 [청구 계정 이해](../understand/mca-overview.md#your-billing-account)를 참조하세요.

다음 표에서는 청구 계정의 컨텍스트에서 작업을 완료하는 데 필요한 역할을 보여줍니다.

### <a name="manage-billing-account-permissions-and-properties"></a>청구 계정 권한 및 속성 관리

|Task|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자|
|---|---|---|---|
|청구 계정에 대한 기존 권한 보기|✔|✔|✔|
|다른 사용자에게 청구 계정을 보고 관리할 수 있는 권한 부여|✔|✘|✘|
|회사 이름, 주소 등과 같은 청구 계정 속성 보기|✔|✔|✔|

### <a name="manage-billing-profiles-for-billing-account"></a>청구 계정의 청구 프로필 관리

|Task|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자|
|---|---|---|---|
|계정의 모든 청구 프로필 보기|✔|✔|✔|

### <a name="manage-invoices-for-billing-account"></a>청구 계정의 청구서 관리

|Task|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자|
|---|---|---|---|
|계정의 모든 청구서 보기|✔|✔|✔|
|계정의 청구서, Azure 사용량 및 요금 파일, 가격표, 세금 문서 다운로드|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-account"></a>청구 계정의 청구서 섹션 관리

|Task|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자|
|---|---|---|---|
|계정의 모든 청구서 섹션 보기|✔|✔|✔|

### <a name="manage-transactions-for-billing-account"></a>청구 계정의 트랜잭션 관리

|Task|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자|
|---|---|---|---|
|해당 계정의 모든 청구 트랜잭션 보기|✔|✔|✔|
|해당 계정으로 구매한 모든 제품 보기|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-account"></a>청구 계정에 대한 구독 관리

|Task|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자|
|---|---|---|---|
|청구 계정의 모든 Azure 구독 보기|✔|✔|✔|

## <a name="billing-profile-roles-and-tasks"></a>청구 프로필 역할 및 작업

청구 프로필을 사용하여 청구서 및 결제 방법을 관리할 수 있습니다. 청구 프로필을 사용하여 구매한 Azure 구독 및 기타 제품에 대한 월별 청구서가 생성됩니다. 결제 방법을 사용하여 청구된 금액을 결제합니다. 자세한 내용은 [청구 프로필 이해](../understand/mca-overview.md#billing-profiles)를 참조하세요.

다음 표에서는 청구 프로필의 컨텍스트에서 작업을 완료하는 데 필요한 역할을 보여줍니다.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>청구 프로필 권한, 속성 및 정책 관리

|Task|청구 프로필 소유자|청구 프로필 기여자|청구 프로필 읽기 권한자|청구서 관리자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구 프로필에 대한 기존 권한 보기|✔|✔|✔|✔|✔|✔|✔|
|다른 사용자에게 청구 프로필을 보고 관리할 수 있는 권한 부여|✔|✘|✘|✘|✘|✘|✘|
|PO 번호, 이메일 청구서 기본 설정 등과 같은 청구 프로필 속성 보기|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필 속성 업데이트 |✔|✔|✘|✘|✘|✘|✘|
|Azure 예약 구매 사용, Azure 마켓플레이스 구매 사용 등 청구 프로필에 적용되는 정책 보기|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필에 정책 적용 |✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-invoices-for-billing-profile"></a>청구 프로필의 청구서 관리

|Task|청구 프로필 소유자|청구 프로필 기여자|청구 프로필 읽기 권한자|청구서 관리자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구 프로필의 모든 청구서 보기|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필의 청구서, Azure 사용량 및 요금 파일, 가격표, 세금 문서 다운로드|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>청구 프로필의 청구서 섹션 관리

|Task|청구 프로필 소유자|청구 프로필 기여자|청구 프로필 읽기 권한자|청구서 관리자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구 프로필의 모든 청구서 섹션 보기|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필의 새 청구서 섹션 만들기|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-transactions-for-billing-profile"></a>청구 프로필의 트랜잭션 관리

|Task|청구 프로필 소유자|청구 프로필 기여자|청구 프로필 읽기 권한자|청구서 관리자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구 프로필의 모든 청구 트랜잭션 보기|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-payment-methods-for-billing-profile"></a>청구 프로필의 결제 방법 관리

|Task|청구 프로필 소유자|청구 프로필 기여자|청구 프로필 읽기 권한자|청구서 관리자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구 프로필의 결제 방법 보기|✔|✔|✔|✔|✔|✔|✔|
|청구 프로필의 Azure 크레딧 잔액 추적|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>청구 프로필에 대한 구독 관리

|Task|청구 프로필 소유자|청구 프로필 기여자|청구 프로필 읽기 권한자|청구서 관리자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구 프로필의 모든 Azure 구독 보기|✔|✔|✔|✔|✔|✔|✔|

## <a name="invoice-section-roles-and-tasks"></a>청구서 섹션 역할 및 작업

청구서 섹션을 통해 청구서의 비용을 구성할 수 있습니다. 부서, 개발 환경 또는 조직의 요구 사항에 따라 비용을 구성하는 섹션을 만들 수 있습니다. 다른 사용자에게 섹션에 대한 Azure 구독을 만들 수 있는 권한을 부여합니다. 그러면 구독에 대한 모든 사용량 요금 및 구매 정보가 청구서 섹션에 표시됩니다. 자세한 내용은 [청구서 섹션 이해](../understand/mca-overview.md#invoice-sections)를 참조하세요.

다음 표에서는 청구서 섹션의 컨텍스트에서 작업을 완료하는 데 필요한 역할을 보여줍니다.

### <a name="manage-invoice-section-permissions-and-properties"></a>청구서 섹션 권한 및 속성 관리

|작업|청구서 섹션 소유자|청구서 섹션 기여자|청구서 섹션 읽기 권한자|Azure 구독 작성자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자 |
|---|---|---|---|---|---|---|---|
|청구서 섹션의 모든 권한 보기|✔|✔|✔|✔|✔|✔|✔|
|다른 사용자에게 청구서 섹션을 보고 관리할 수 있는 권한 부여|✔|✘|✘|✘|✘|✘|✘|
|청구서 섹션 속성 보기|✔|✔|✔|✔|✔|✔|✔|
|청구서 섹션 속성 업데이트|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-products-for-invoice-section"></a>청구서에 대한 제품 관리

|작업|청구서 섹션 소유자|청구서 섹션 기여자|청구서 섹션 읽기 권한자|Azure 구독 작성자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구서 섹션에서 구매한 모든 제품 보기|✔|✔|✔|✘|✔|✔|✔|
|취소, 자동 갱신 해제 등 청구서 섹션의 제품 요금 청구 관리|✔|✔|✘|✘|✘|✘|✘|
|제품의 청구서 섹션 변경|✔|✔|✘|✘|✘|✘|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>청구서 섹션에 대한 구독 관리

|작업|청구서 섹션 소유자|청구서 섹션 기여자|청구서 섹션 읽기 권한자|Azure 구독 작성자|청구 계정 소유자|청구 계정 기여자|청구 계정 읽기 권한자
|---|---|---|---|---|---|---|---|
|청구서 섹션에 대한 모든 Azure 구독 관리|✔|✔|✔|✘|✔|✔|✔|
|구독의 청구서 섹션 변경|✔|✔|✘|✘|✘|✘|✘|
|다른 청구 계정의 사용자에게 구독의 청구 소유권 요청|✔|✔|✘|✘|✘|✘|✘|

## <a name="subscription-billing-roles-and-tasks"></a>구독 청구 역할 및 작업

다음 표에서는 구독의 컨텍스트에서 작업을 완료하는 데 필요한 역할을 보여줍니다.

|작업|청구서 섹션 소유자|청구서 섹션 기여자|청구서 섹션 읽기 권한자|Azure 구독 작성자|
|---|---|---|---|---|
|Azure 구독 만들기|✔|✔|✘|✔|
|구독의 비용 센터 업데이트|✔|✔|✘|✘|
|구독의 청구서 섹션 변경|✔|✔|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Azure Portal에서 청구 역할 관리

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구**를 검색합니다.

   ![Azure Portal 검색을 보여 주는 스크린샷](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. 청구 계정, 청구 프로필, 송장 섹션 등 액세스 권한을 부여하려는 범위에서 **액세스 제어(IAM)** 를 선택합니다.

4. 액세스 제어(IAM) 페이지에는 해당 범위의 각 역할에 할당된 사용자 및 그룹이 나열됩니다.

   ![청구 계정의 관리자 목록을 보여주는 스크린샷](./media/understand-mca-roles/billing-list-admins.png)

5. 사용자에게 액세스 권한을 부여하려면 페이지 맨 위에서 **추가**를 선택합니다. [역할] 드롭다운 목록에서 역할을 선택합니다. 액세스 권한을 부여하려는 사용자의 이메일 주소를 입력합니다. **저장**을 선택하여 역할을 할당합니다.

   ![청구 계정에 관리자를 추가하는 방법을 보여주는 스크린샷](./media/understand-mca-roles/billing-add-admin.png)

6. 사용자에게 할당된 액세스 권한을 제거하려면 제거하려는 역할이 할당된 사용자를 선택합니다. [제거]를 선택합니다.

   ![청구 계정에서 관리자를 제거하는 방법을 보여주는 스크린샷](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대한 액세스 확인
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.

## <a name="next-steps"></a>다음 단계

청구 계정에 대해 알아보려면 다음 문서를 참조하세요.

- [Microsoft 고객 계약의 청구 계정 시작](../understand/mca-overview.md)
- [Microsoft 고객 계약의 청구 계정에 대한 Azure 구독 만들기](create-subscription.md)
