---
title: Azure 청구에 대한 액세스 관리 | Microsoft Docs
description: 팀 멤버의 Azure 청구 정보에 대한 액세스를 제공하는 방법을 알아봅니다.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918693"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Azure의 청구 정보에 대한 액세스 관리

대부분의 구독은 Azure Portal의 **구독**에서 팀의 멤버에게 청구 정보에 대한 액세스 권한을 부여할 수 있습니다. Azure EA 고객(기업계약을 맺은 고객)이면서 엔터프라이즈 관리자인 경우, Enterprise 포털에서 부서 관리자 및 계정 소유자에 대한 권한을 부여할 수 있습니다.

## <a name="give-access-to-billing"></a>청구에 대한 액세스 권한 부여

EA 고객을 제외한 모든 고객은 다음 사용자 역할 중 하나를 팀의 멤버에게 할당하여 Azure 청구 정보에 대한 액세스 권한을 부여할 수 있습니다.

- 계정 관리자
- 서비스 관리자
- 공동 관리자
- 소유자
- 참가자
- 판독기
- 청구 읽기 권한자

역할을 할당하려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

이 역할은 [Azure Portal](https://portal.azure.com/)의 청구 정보에 대한 액세스 권한을 갖습니다. 이 역할이 할당된 사람은 [청구 API](billing-usage-rate-card-overview.md)를 사용하여 프로그래밍 방식으로 청구서 및 사용량 세부 정보를 확보할 수 있습니다. 자세한 내용은 [Azure RBAC의 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

### <a name="opt-in"></a> 사용자가 청구서를 다운로드하도록 허용

팀의 구성원에 게 적절 한 역할을 할당 하 고 나면 계정 관리자가 Azure portal에서 청구서 다운로드에 대 한 액세스 설정 해야 합니다. 2016년 12월보다 오래된 청구서는 계정 관리자만 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 계정 관리자는 Azure Portal의 [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.

1. **청구서**를 선택하고 **청구서 액세스**를 선택합니다.

    ![청구서에 대한 액세스를 위임하는 방법을 보여 주는 스크린샷](./media/billing-manage-access/AA-optin.png)

1. **켬**을 선택하고 저장합니다.

    ![청구서 액세스에 대한 위임을 설정/해제하는 방법을 보여 주는 스크린샷](./media/billing-manage-access/AA-optinAllow.png)

또한 계정 관리자는 전자 메일을 통해 청구서가 전송되도록 구성할 수도 있습니다. 자세한 내용은 [전자 메일로 청구서 받기](billing-download-azure-invoice-daily-usage-date.md)를 참조하세요.

## <a name="give-read-only-access-to-billing"></a>청구에 대한 읽기 전용 액세스 부여

구독 청구 정보에 대한 읽기 전용 액세스가 필요하며 Azure 서비스 관리 또는 생성 기능은 필요하지 않은 사람에게는 청구 읽기 권한자 역할을 할당합니다. 이 역할은 Azure 구독에 대해 재무 및 비용 관리를 담당하는 조직의 사용자에게 적합합니다.

EA 고객은 계정 소유자 또는 부서 관리자가 청구 읽기 권한자 역할을 팀 멤버에게 할당할 수 있습니다. 단 청구 읽기 권한자가 부서나 계정에 대한 청구 정보를 보려면, 엔터프라이즈 관리자가 Enterprise 포털에서 **AO 요금 보기** 또는 **DA 요금 보기** 정책을 사용하도록 설정해야 합니다.

청구 읽기 권한자 기능은 미리 보기로 제공되며 비전역 클라우드는 아직 지원하지 않습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure Portal의 [구독 블레이드](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 구독을 선택합니다.

1. **액세스 제어(IAM)** 를 선택합니다.
1. **역할 할당**을 선택하여 이 구독의 모든 역할 할당을 봅니다.
1. **역할 할당 추가**를 선택합니다.
1. **역할** 드롭 다운 목록에서 **청구 읽기 권한자**를 선택합니다.
1. **선택** 텍스트 상자에 추가하려는 사용자의 이름이나 이메일을 입력합니다.
1. 사용자를 선택합니다.
1. **저장**을 선택합니다.
1. 몇 분이 지나면 사용자에게는 구독 범위의 청구 읽기 권한자 역할이 할당됩니다.
1. 청구 읽기 권한자는 로그인할 링크가 포함된 이메일을 받습니다.

    ![청구 읽기 권한자가 Azure Portal에서 볼 수 있는 내용을 보여 주는 스크린샷](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>부서 관리자 또는 계정 소유자 청구 액세스 허용

엔터프라이즈 관리자는 부서 관리자와 계정 소유자가 그들이 관리하는 부서 및 계정과 관련된 비용 및 사용량 세부 정보를 보도록 허용할 수 있습니다.

1. 엔터프라이즈 관리자로 [EA 포털](https://ea.azure.com/)에 로그인합니다.
1. **관리**를 선택합니다.
1. **등록**에서 사용량 및 비용을 볼 부서관리자에 대해 **DA 요금 보기**를 **사용 가능**으로 변경합니다.
1. 사용량 및 비용을 볼 계정 소유자에 대해 **AO 요금 보기**를 **사용 가능**으로 변경합니다.


자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](billing-understand-ea-roles.md)를 참조하세요.

## <a name="only-account-admins-can-access-account-center"></a>계정 관리자만 계정 센터에 액세스할 수 있습니다.

계정 관리자는 구독의 법적 소유자입니다. 다른 사람에게 [구독 소유권이 양도](billing-subscription-transfer.md)되지 않은 한, 기본적으로 Azure 구독에 등록했거나 구입한 사람이 계정 관리자입니다. 계정 관리자는 [계정 센터](https://account.azure.com/Subscriptions)에서 구독을 만들고, 구독을 취소하고, 구독에 대한 청구 주소를 변경하고, 구독에 대한 액세스 정책을 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 소유자 또는 참가자 등의 다른 역할의 사용자는 청구 정보에 액세스할 수만 있고 Azure 서비스에는 액세스할 수 없습니다. 이러한 역할을 관리하려면 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 역할에 대한 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.
