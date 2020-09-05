---
title: Microsoft 365 계정을 사용하여 Azure에 등록
description: Microsoft 365 계정을 사용하여 Azure 구독을 만드는 방법을 알아봅니다. 기존 Azure 및 Microsoft 365 계정을 서로 연결할 수도 있습니다.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5fef68c9de4dae8063799255fcc7d68ff6b1e6b8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684850"
---
# <a name="sign-up-for-an-azure-subscription-with-your-microsoft-365-account"></a>Microsoft 365 계정을 사용하여 Azure 구독에 등록

Microsoft 365 구독을 보유하는 경우 Microsoft 365 계정을 사용하여 Azure 구독을 만들 수 있습니다. Microsoft 365 사용자 이름과 암호를 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 가상 머신을 설정하거나 다른 Azure 서비스를 사용하려는 경우 Azure 구독에 가입해야 합니다. 다른 사람과 Azure 구독을 공유하고 [역할 기반 Access Control을 사용하여 Azure 구독 및 리소스에 대한 액세스를 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)할 수 있습니다.

Microsoft 365 계정과 Azure 구독이 둘 다 이미 있는 경우 [Azure 구독에 Microsoft 365 테넌트 연결](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="get-an-azure-subscription-using-your-microsoft-365-account"></a>Microsoft 365 계정을 사용하여 Azure 구독 가져오기

Microsoft 365 사용자 이름 및 암호를 사용하여 Azure에 등록함으로써 시간을 절약하고 계정 확산을 방지합니다.

1. [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs)에서 등록합니다.
2. Microsoft 365 사용자 이름과 암호를 사용하여 로그인합니다. 사용하는 계정에 관리자 권한이 필요하지는 않습니다. Microsoft 365 계정이 여러 개 있는 경우 Azure 구독과 연결하려는 Microsoft 365 계정에 대한 자격 증명을 사용해야 합니다.

   ![로그인 페이지를 보여 주는 스크린샷](./media/microsoft-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. 필요한 정보를 입력하고 등록 프로세스를 완료합니다. Microsoft 365 계정이 이미 있는 경우 일부 정보가 필요하지 않을 수 있습니다.

    ![등록 양식을 보여 주는 스크린샷](./media/microsoft-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- 조직의 다른 사람을 Azure 구독에 추가해야 하는 경우 [Azure Portal에서 액세스 관리 시작](../../role-based-access-control/overview.md)을 참조하세요.

## <a name=""></a><a id="more-about-subs">Azure 및 Microsoft 365 구독에 대한 추가 정보</a>

Microsoft 365 및 Azure는 Azure AD 서비스를 사용하여 사용자와 구독을 관리합니다. Azure 디렉터리는 사용자와 구독을 그룹화할 수 있는 컨테이너와 같습니다. Azure 및 Microsoft 365 구독에 대해 동일한 사용자 계정을 사용하려면 Azure 구독을 Microsoft 365 구독과 동일한 디렉터리 내에 만들어야 합니다. 다음 사항을 주의하세요.

* 구독은 디렉터리 아래에 생성됩니다.
* 사용자는 디렉터리에 속합니다.
* 구독은 구독을 생성하는 사용자의 디렉터리 내에 배치됩니다. 따라서 Microsoft 365 구독은 Azure 구독과 동일한 계정에 연결됩니다.
* Azure 구독은 디렉터리 내 개별 사용자가 소유합니다.
* Microsoft 365 구독은 디렉터리 자체에 의해 소유됩니다. 디렉터리 내에서 적절한 사용 권한이 있는 사용자는 이러한 구독을 관리할 수 있습니다.

![디렉터리, 사용자 및 구독의 관계를 보여 주는 스크린샷](./media/microsoft-365-account-for-azure-subscription/19-background-information.png)

자세한 내용은 [Azure 구독과 Azure Active Directory의 연관 관계](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- 다른 사람과 Azure 구독을 공유하고 [역할 기반 Access Control을 사용하여 Azure 구독 및 리소스에 대한 액세스를 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)합니다.