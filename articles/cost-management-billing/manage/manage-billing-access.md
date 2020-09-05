---
title: Azure 청구에 대한 액세스 관리
description: 팀 멤버에게 Azure 청구 정보에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: f42c185046e6ce78edd234923da1a3390ed08608
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690853"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Azure의 청구 정보에 대한 액세스 관리

Azure Portal의 계정에 대한 청구 정보에 대한 액세스를 다른 사용자에게 제공할 수 있습니다. 청구 역할의 유형 및 청구 정보에 대한 액세스를 제공하는 지침은 청구 계정의 유형에 따라 달라집니다. 청구 계정의 유형을 확인하려면 [청구 계정의 유형 확인](#check-the-type-of-your-billing-account)을 참조하세요.

이 문서는 Microsoft Online Service 프로그램 계정을 사용하는 고객에게 적용됩니다. Azure EA(기업계약을 맺은 고객)이면서 엔터프라이즈 관리자인 경우, Enterprise Portal에서 부서 관리자 및 계정 소유자에 대한 권한을 부여할 수 있습니다. 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요. Microsoft 고객 계약 고객인 경우 [Azure의 Microsoft 고객 계약 관리 역할 이해](understand-mca-roles.md)를 참조하세요.

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Microsoft Online Service 프로그램 계정에 대한 계정 관리자

계정 관리자는 Microsoft Online Service 프로그램 청구 계정의 유일한 소유자입니다. 역할은 Azure에 등록한 사용자에게 할당됩니다. 계정 관리자는 구독 만들기, 청구서 보기 또는 구독에 대한 청구 변경 등의 다양한 청구 작업을 수행할 수 있는 권한이 있습니다.

## <a name="give-others-access-to-view-billing-information"></a>다른 사용자에게 청구 정보를 볼 수 있는 권한 부여

계정 관리자는 해당 계정에서 구독에 대해 다음 역할 중 하나를 할당하여 다른 사용자에게 Azure 청구 정보에 대한 액세스 권한을 부여할 수 있습니다.

- 서비스 관리자
- 공동 관리자
- 소유자
- 참가자
- 판독기
- 청구 읽기 권한자

이러한 역할은 [Azure Portal](https://portal.azure.com/)의 청구 정보에 대한 액세스 권한을 갖습니다. 이러한 역할이 할당된 사람은 [청구 API](usage-rate-card-overview.md)를 사용하여 프로그래밍 방식으로 청구서 및 사용량 세부 정보를 확보할 수 있습니다.

역할을 할당하려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

** EA 고객인 경우 계정 소유자는 팀의 다른 사용자에게 위의 역할을 할당할 수 있습니다. 그러나 이러한 사용자가 청구 정보를 보려면 엔터프라이즈 관리자는 Enterprise Portal에서 AO 보기 요금을 사용하도록 설정해야 합니다.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> 사용자가 청구서를 다운로드하도록 허용

계정 관리자가 다른 사용자에게 적절할 역할을 할당한 후, Azure Portal에서 청구서를 다운로드할 수 있는 액세스를 켜야 합니다. 2016년 12월보다 오래된 청구서는 계정 관리자만 사용할 수 있습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Cost Management + 청구**에서 검색합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. 왼쪽 창에서 **구독**을 선택합니다. 액세스에 따라 청구 범위를 선택한 다음, **구독**을 선택해야 할 수 있습니다.

    ![구독 선택을 보여 주는 스크린샷](./media/manage-billing-access/billing-select-subscriptions.png)

1. **청구서**를 선택한 다음, **청구서에 액세스**를 선택합니다.

    ![청구서에 대한 액세스를 위임하는 방법을 보여 주는 스크린샷](./media/manage-billing-access/aa-optin01.png)

1. **켬**을 선택하고 저장합니다.

    ![청구서 액세스에 대한 위임을 설정/해제하는 방법을 보여 주는 스크린샷](./media/manage-billing-access/aa-optinallow01.png)

또한 계정 관리자는 전자 메일을 통해 청구서가 전송되도록 구성할 수도 있습니다. 자세한 내용은 [전자 메일로 청구서 받기](download-azure-invoice-daily-usage-date.md)를 참조하세요.

## <a name="give-read-only-access-to-billing"></a>청구에 대한 읽기 전용 액세스 부여

구독 청구 정보에 대한 읽기 전용 액세스가 필요하며 Azure 서비스 관리 또는 생성 기능은 필요하지 않은 사람에게는 청구 읽기 권한자 역할을 할당합니다. 이 역할은 Azure 구독에 대해 재무 및 비용 관리를 담당하는 조직의 사용자에게 적합합니다.

청구 읽기 권한자 기능은 미리 보기로 제공되며 비전역 클라우드는 아직 지원하지 않습니다.

1. 계정 관리자 권한으로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Cost Management + 청구**에서 검색합니다.

    ![Azure Portal 검색을 보여 주는 스크린샷](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. 왼쪽 창에서 **구독**을 선택합니다. 액세스에 따라 청구 범위를 선택한 다음, **구독**을 선택해야 할 수 있습니다.

    ![구독 선택을 보여 주는 스크린샷](./media/manage-billing-access/billing-select-subscriptions.png)

1. **액세스 제어(IAM)** 를 선택합니다.
1. 페이지 맨 위에서 **추가**를 선택합니다.

    ![역할 할당 추가 클릭을 보여 주는 스크린샷](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. **역할** 드롭 다운 목록에서 **청구 읽기 권한자**를 선택합니다.
1. **선택** 텍스트 상자에 추가하려는 사용자의 이름이나 이메일을 입력합니다.
1. 사용자를 선택합니다.
1. **저장**을 선택합니다.
    ![역할 할당 추가 클릭을 보여 주는 스크린샷](./media/manage-billing-access/billing-save-role-assignment.png)

1. 몇 분이 지나면 사용자에게는 구독의 청구 읽기 권한자 역할이 할당됩니다.

** EA 고객인 경우 계정 소유자 또는 부서 관리자가 청구 읽기 권한자 역할을 팀 멤버에게 할당할 수 있습니다. 단 청구 읽기 권한자가 부서나 계정에 대한 청구 정보를 보려면, 엔터프라이즈 관리자가 Enterprise 포털에서 **AO 요금 보기** 또는 **DA 요금 보기** 정책을 사용하도록 설정해야 합니다.

## <a name="check-the-type-of-your-billing-account"></a>청구 계정의 유형 확인
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>다음 단계

- 소유자 또는 참가자 등의 다른 역할의 사용자는 청구 정보에 액세스할 수만 있고 Azure 서비스에는 액세스할 수 없습니다. 이러한 역할을 관리하려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 역할에 대한 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
