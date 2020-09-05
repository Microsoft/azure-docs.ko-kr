---
title: Azure Portal의 계정 관리자 작업
description: Azure Portal에서 결제 작업을 수행하는 방법을 설명
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ad39b9980562c345aff87f4e0771f265011886b3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683303"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Azure Portal의 계정 관리자 작업

이 문서에서는 Azure Portal에서 다음 작업을 수행하는 방법을 설명합니다.
- 구독의 결제 방법 관리
- 구독에 대한 지출 한도 삭제
- Azure In Open 구독에 크레딧 추가

이러한 작업을 수행하려면 계정 관리자여야 합니다.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>구독의 결제 방법으로 이동

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Cost Management + 청구**를 검색합니다.

    ![비용 관리 + 청구 검색을 보여주는 스크린샷 ](./media/account-admin-tasks/search-bar.png)

1. **내 구독** 목록에서 신용 카드를 추가하려는 구독을 선택합니다.

   ![개요의 내 구독 그리드를 보여주는 스크린샷](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > 여기에 일부 구독이 표시되지 않으면 특정 시점에 구독 디렉터리를 변경했기 때문일 수 있습니다. 이러한 구독의 경우 디렉터리를 원래 디렉터리(처음 등록한 디렉터리)로 전환해야 합니다. 그런 다음, 2단계를 반복합니다.

1. **결제 방법**을 선택합니다.

    ![선택된 결제 방법 블레이드를 보여주는 스크린샷](./media/account-admin-tasks/subscription-payment-methods-blade.png)

여기서 새 신용 카드를 추가하고, 활성 결제 방법을 변경하고, 신용 카드 세부 정보를 편집하고, 신용 카드를 삭제할 수 있습니다.

### <a name="change-active-payment-method"></a>활성 결제 방법 변경

새 신용 카드를 추가하거나 이미 저장된 신용 카드를 선택하여 활성 결제 방법을 변경할 수 있습니다. 활성 결제 방법을 새 신용 카드로 변경하려면 다음을 수행합니다.

1. 왼쪽 위 모서리에서 “+”를 선택하여 신용 카드를 추가합니다.

    ![더하기 기호를 보여 주는 스크린샷](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. 신용 카드 세부 정보를 오른쪽에 있는 양식에 입력합니다.

    ![신용 카드 추가 양식을 보여주는 스크린샷](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. 이 카드를 활성 결제 방법으로 만들려면 양식 위쪽에서 **이 방법을 내 활성 결제 방법으로 설정** 옆에 있는 확인란을 선택합니다. 이 카드는 선택한 구독과 동일한 카드를 사용하는 모든 구독의 활성 결제 방법이 됩니다.

    ![카드를 활성 결제 방법으로 설정하기 위한 확인란을 보여주는 스크린샷](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. **다음**을 선택합니다.

활성 결제 방법을 이미 저장된 신용 카드로 변경하려면 다음을 수행합니다.

1. 활성 결제 방법으로 설정할 카드 옆에 있는 확인란을 선택합니다.

    ![신용 카드 옆의 확인란이 선택된 모습을 보여주는 스크린샷](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. 명령 모음에서 **활성 설정**을 클릭합니다.

    ![활성 설정 단추를 보여주는 스크린샷](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>신용 카드 세부 정보 입력

만료 날짜 또는 주소와 같은 신용 카드 세부 정보를 편집하려면 편집하려는 신용 카드를 클릭합니다. 오른쪽에 신용 카드 양식이 나타납니다.

![선택한 신용 카드를 보여주는 스크린샷](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

신용 카드 세부 정보를 업데이트하고 **저장**을 클릭합니다.

### <a name="remove-a-credit-card-from-the-account"></a>계정에서 신용 카드 제거

1. 삭제할 카드 옆에 있는 확인란을 선택합니다.

    ![신용 카드 옆의 확인란이 선택된 모습을 보여주는 스크린샷](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. 명령 모음에서 **삭제**를 클릭합니다.

    ![삭제 단추를 보여주는 스크린샷](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

신용 카드가 다른 Microsoft 구독에 대한 활성 결제 방법인 경우 Azure 계정에서 제거할 수 없습니다. 이 신용 카드에 연결된 모든 구독에 대해 활성 결제 방법을 변경한 후 다시 시도하십시오.

### <a name="switch-to-invoice-payment"></a>청구서 결제로 전환

청구서로 지불할 수 있는 경우(수표/전신 송금) Azure Portal에서 구독을 청구서 결제(수표/전신 송금)로 전환할 수 있습니다.

1. 명령 모음에서 **청구서로 지불**을 선택합니다.

    ![선택된 결제 방법 블레이드를 보여주는 스크린샷](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. 청구서 지불 방법에 사용할 주소를 입력합니다.
1. **다음**을 클릭합니다.

청구서로 지불하기 위해 승인이 필요한 경우 [청구서로 지불하는 방법 알아보기](pay-by-invoice.md)를 참조하세요.

### <a name="edit-invoice-payment-address"></a>청구서 결불 주소 편집

청구서 결제 방법의 주소를 편집하려면 구독에 대한 결제 방법 목록에서 **청구서**를 클릭합니다. 주소 양식이 오른쪽에 열립니다.

## <a name="remove-spending-limit"></a>지출 한도 제거

Azure에서 지출 한도는 크레딧 금액을 초과하여 지출하는 것을 방지하기 위함입니다. 유효한 결제 방법이 Azure 구독과 연결되어 있는 경우 언제든지 지출 한도를 제거할 수 있습니다. Visual Studio Enterprise 및 Visual Studio Professional과 같이 여러 달에 대한 크레딧이 있는 구독 유형의 경우 다음 청구 기간이 시작될 때 지출 한도를 사용하도록 선택할 수 있습니다.

약정 플랜 또는 종량제 가격 책정을 사용하는 구독에서는 지출 한도를 사용할 수 없습니다. [전체 Azure 구독 유형 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)을 참조하세요.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![비용 관리 + 청구 검색을 보여주는 스크린샷 ](./media/account-admin-tasks/search-bar.png)

1. **내 구독** 목록에서 Visual Studio Enterprise 구독을 선택합니다.

   ![개요의 내 구독 그리드를 보여주는 스크린샷](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 여기에 Visual Studio 구독 중 일부가 표시되지 않으면 특정 시점에 구독 디렉터리를 변경했기 때문일 수 있습니다. 이러한 구독의 경우 디렉터리를 원래 디렉터리(처음 등록한 디렉터리)로 전환해야 합니다. 그런 다음, 2단계를 반복합니다.

1. 구독 개요에서 주황색 배너를 클릭하여 지출 한도를 제거합니다.

    ![지출 한도 제거 배너를 보여주는 스크린샷](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. 지출 한도를 무기한으로 제거할지 또는 현재 청구 기간에 대해서만 제거할지를 선택합니다.

   ![지출 한도 제거 블레이드를 보여주는 스크린샷](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. **결제 방법 선택**을 클릭하여 구독에 대한 지불 방법을 선택합니다. 이는 구독에 대한 활성 지불 방법이 됩니다.

1. **Finish**를 클릭합니다.

## <a name="add-credits-to-azure-in-open-subscription"></a>Azure In Open 구독에 크레딧 추가

Azure in Open 라이선스 구독이 있는 경우 제품 키를 사용하거나 신용 카드로 크레딧을 구매하여 Azure Portal의 구독에 크레딧을 추가할 수 있습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![비용 관리 + 청구 검색을 보여주는 스크린샷 ](./media/account-admin-tasks/search-bar.png)

1. **내 구독** 목록에서 Azure in Open 구독을 선택합니다.

    ![개요의 내 구독 그리드를 보여주는 스크린샷](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > 여기에 구독이 표시되지 않으면 특정 시점에 구독 디렉터리를 변경했기 때문일 수 있습니다. 구독의 디렉터리를 원래 디렉터리(처음 등록한 디렉터리)로 전환해야 합니다. 그런 다음, 2단계를 반복합니다.

1. **크레딧 기록**을 선택합니다.

    ![크레딧 기록을 보여주는 스크린샷](./media/account-admin-tasks/aio-credit-history-blade.png)

1. 왼쪽 위 모서리에서 “+”를 선택하여 크레딧을 더 추가합니다.

    ![크레딧 추가 단추를 보여주는 스크린샷](./media/account-admin-tasks/aio-credit-history-plus.png)

1. 드롭다운에서 지불 방법 유형을 선택합니다. 제품 키를 추가하거나 신용 카드로 크레딧을 구매할 수 있습니다.

    ![크레딧 추가 블레이드의 지불 방법 드롭다운 스크린샷](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. 제품 키를 선택한 경우:
    - 제품 키를 입력합니다.
    - **유효성 검사**를 클릭합니다.

1. 신용 카드를 선택한 경우:
    - **지불 방법 선택**을 클릭하여 신용 카드를 추가하거나 기존 카드를 선택합니다.
    - 추가하려는 크레딧의 양을 지정합니다.

1. **적용**을 클릭합니다.

## <a name="troubleshooting"></a>문제 해결
가상 카드 또는 선불 카드는 지원하지 않습니다. 유효한 신용 카드를 추가하거나 업데이트할 때 오류가 발생하면 프라이빗 모드에서 브라우저를 열어 보세요.

## <a name="next-steps"></a>다음 단계
- [Azure Portal의 예기치 않은 비용 분석 및 방지](getting-started.md)에 대해 자세히 알아봅니다.
