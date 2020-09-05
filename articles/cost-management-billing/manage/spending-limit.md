---
title: Azure 지출 한도
description: 이 문서에서는 Azure 지출 한도의 작동 방식 및 제거 방법을 설명합니다.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: df5c5ed74a5e0a63727962fa86d261cfb588a384
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690564"
---
# <a name="azure-spending-limit"></a>Azure 지출 한도

Azure에서 지출 한도는 크레딧 금액을 초과하여 지출하는 것을 방지하기 위함입니다. Azure 체험 계정 또는 여러 달의 크레딧을 포함하는 구독 유형에 가입한 모든 신규 고객은 기본적으로 지출 한도가 설정됩니다. 지출 한도는 크레딧 양과 같으며 변경할 수 없습니다. 예를 들어 Azure 체험 계정에 가입한 경우 지출 한도는 $200이며 $500로 변경할 수 없습니다. 단, 지출 한도를 제거할 수는 있습니다. 따라서 제한이 없거나 크레딧 양과 같은 제한이 있습니다. 이렇게 하면 대부분의 지출을 방지할 수 있습니다. 약정 플랜 또는 종량제 가격 책정을 사용하는 구독에서는 지출 한도를 사용할 수 없습니다. [전체 Azure 구독 유형 목록 및 지출 한도 가용성](https://azure.microsoft.com/support/legal/offer-details/)을 참조하세요.

## <a name="reaching-a-spending-limit"></a>지출 한도에 도달

지출 한도를 모두 사용할 경우 해당 청구 기간의 나머지 기간 동안 배포한 서비스가 비활성화됩니다.

예를 들어 Azure 체험 계정에 포함된 모든 크레딧을 소비하는 경우 배포한 Azure 리소스가 프로덕션에서 제거되고 Azure 가상 머신이 중지되고 할당이 취소됩니다. 스토리지 계정의 데이터는 읽기 전용으로 사용할 수 있습니다.

다음 청구 기간이 시작될 때 구독 유형에 여러 달에 대한 크레딧이 포함될 경우 구독이 다시 사용되도록 자동으로 설정됩니다. 그런 다음, Azure 리소스를 다시 배포하고 스토리지 계정 및 데이터베이스에 대한 모든 권한을 가질 수 있습니다.

Azure에서는 지출 한도에 도달할 경우 이메일 알림을 보냅니다. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에 로그인하면 지출 한도에 도달한 구독에 대한 알림이 표시됩니다.

Azure 체험 계정에 가입하고 지출 한도에 도달하는 경우 지출 한도를 제거하고 자동으로 구독을 활성화시키는 [종량제](upgrade-azure-subscription.md) 가격 책정으로 업그레이드할 수 있습니다.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Azure Portal에서 지출 한도 제거

유효한 결제 방법이 Azure 구독과 연결되어 있는 경우 언제든지 지출 한도를 제거할 수 있습니다. Visual Studio Enterprise 및 Visual Studio Professional과 같이 여러 달에 걸쳐 크레딧이 있는 구독 유형의 경우 지출 한도를 무기한 또는 현재 청구 기간 동안만 제거하도록 선택할 수 있습니다. 현재 청구 기간만 선택하면 다음 청구 기간이 시작될 때 지출 한도가 자동으로 활성화됩니다.

Azure 체험 계정이 있는 경우 [Azure 구독 업그레이드](upgrade-azure-subscription.md)를 참조하여 지출 한도를 제거합니다. 그렇지 않으면 다음 단계에 따라 지출 한도를 제거합니다.

<a id="remove"></a>

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![비용 관리 + 청구 검색을 보여주는 스크린샷 ](./media/spending-limit/search-bar.png)

1. **내 구독** 목록에서 구독을 선택합니다. 예를 들어 *Visual Studio Enterprise*입니다.

   ![개요의 내 구독 그리드를 보여주는 스크린샷](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 여기에 Visual Studio 구독 중 일부가 표시되지 않으면 특정 시점에 구독 디렉터리를 변경했기 때문일 수 있습니다. 이러한 구독의 경우 디렉터리를 원래 디렉터리(처음 등록한 디렉터리)로 전환해야 합니다. 그런 다음, 2단계를 반복합니다.

1. 구독 개요에서 주황색 배너를 클릭하여 지출 한도를 제거합니다.

    ![지출 한도 제거 배너를 보여주는 스크린샷](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. 지출 한도를 무기한으로 제거할지 또는 현재 청구 기간에 대해서만 제거할지를 선택합니다.

      ![지출 한도 제거 블레이드를 보여주는 스크린샷](./media/spending-limit/remove-spending-limit-blade-x.png)

      | 옵션 | 영향 |
      | --- | --- |
      | 지출 한도 무기한 제거 | 다음 청구 기간이 시작될 때 지출 한도가 자동으로 다시 설정되지 않습니다. 그러나 언제든지 다시 설정할 수 있습니다. |
      | 현재 청구 기간에 대한 지출 한도 제거 | 다음 청구 기간이 시작되면 지출 한도가 자동으로 다시 설정됩니다. |


1. **결제 방법 선택**을 클릭하여 구독에 대한 지불 방법을 선택합니다. 이는 구독에 대한 활성 지불 방법이 됩니다.

1. **Finish**를 클릭합니다.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>지출 한도를 제거하려는 이유는 무엇입니까?

지출 한도에 도달하면 특정 타사 및 Microsoft 서비스를 배포 또는 사용할 수 없게 됩니다. 다음은 구독에서 지출 한도를 제거해야 하는 상황입니다.

-  Oracle과 같은 타사 이미지 또는 Azure DevOps Services 등의 서비스를 배포하려고 합니다. 이 경우 지출 한도가 거의 순식간에 도달하게 되어 구독이 비활성화됩니다.
- 중단하면 안되는 서비스가 있습니다. 지출 한도에 도달하면 배포한 Azure 리소스가 프로덕션에서 제거되고 Azure 가상 머신이 중지되고 할당 최소됩니다. 중단하지 않으려는 서비스가 있는 경우 지출 한도를 제거해야 합니다.
- 손실되지 않아야 하는 가상 IP 주소와 같은 설정을 사용하는 서비스 및 리소스가 있는 경우. 지출 한도에 도달하고 서비스 및 리소스의 할당이 취소되면 이러한 설정이 손실됩니다.

## <a name="turn-on-the-spending-limit-after-removing"></a>제거한 후에 지출 한도 기능 켜기

이 기능은 여러 달의 크레딧을 포함하는 구독 유형에 대해 지출 한도가 무기한으로 제거된 경우에만 사용할 수 있습니다. 이 기능을 사용하여 다음 청구 기간이 시작될 때 자동으로 지출 한도를 설정할 수 있습니다.


1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Cost Management + 청구**를 검색합니다.

    ![비용 관리 + 청구 검색을 보여주는 스크린샷 ](./media/spending-limit/search-bar.png)

1. **내 구독** 목록에서 구독을 선택합니다. 예를 들어 *Visual Studio Enterprise*입니다.

   ![개요의 내 구독 그리드를 보여주는 스크린샷](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > 여기에 Visual Studio 구독 중 일부가 표시되지 않으면 특정 시점에 구독 디렉터리를 변경했기 때문일 수 있습니다. 이러한 구독의 경우 디렉터리를 원래 디렉터리(처음 등록한 디렉터리)로 전환해야 합니다. 그런 다음, 2단계를 반복합니다.

1. 구독 개요에서 페이지 맨 위에 있는 배너를 클릭하여 지출 한도를 다시 설정합니다.

## <a name="custom-spending-limit"></a>사용자 지정 지출 한도

사용자 지정 지출 한도는 사용할 수 없습니다.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>지출 한도가 모든 요금을 방지하는 것은 아닙니다.

[Azure Marketplace에 게시된 일부 외부 서비스](../understand/understand-azure-marketplace-charges.md)는 구독 크레딧을 사용할 수 없으며, 따라서 지출 한도가 설정되어 있더라도 별도의 요금이 발생할 수 있습니다. Visual Studio 라이선스, Azure Active Directory 프리미엄, 지원 플랜 및 대부분의 타사 브랜드 서비스를 예로 들 수 있습니다. 새 외부 서비스를 프로비전할 때 서비스 요금이 별도로 청구됨을 알리는 경고가 표시됩니다.

![Marketplace 구매 경고](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계
- [종량제](upgrade-azure-subscription.md) 가격 책정을 사용한 요금제로 업그레이드합니다.
