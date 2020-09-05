---
title: 기존 Azure 구독을 테 넌 트에 추가-Azure AD
description: Azure Active Directory 테넌트에 기존 Azure 구독을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd095939009c39c48456d330f975303f06a841a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267533"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가

Azure 구독에는 Azure Active Directory (Azure AD)와의 트러스트 관계가 있습니다. 구독은 사용자, 서비스 및 장치를 인증 하기 위해 Azure AD를 신뢰 합니다.

여러 구독은 동일한 Azure AD 디렉터리를 신뢰할 수 있습니다. 각 구독은 단일 디렉터리만 신뢰할 수 있습니다.

구독이 만료되면 구독과 연결된 기타 모든 리소스에 대한 액세스 권한도 손실됩니다. 그러나 azure AD 디렉터리는 Azure에 남아 있습니다. 다른 Azure 구독을 사용 하 여 디렉터리를 연결 하 고 관리할 수 있습니다.

기본적으로 사용자가 Microsoft 클라우드 서비스에 등록할 때 Azure AD 테 넌 트가 만들어지고 사용자는 전역 관리자 역할의 구성원이 됩니다. 기존 디렉터리에 구독을 추가 하면 전역 관리자 역할에 할당 되지 않습니다.

모든 사용자에 게는 인증을 위한 단일 *홈* 디렉터리가 있습니다. 사용자는 다른 디렉터리의 게스트가 될 수도 있습니다. Azure AD에서 각 사용자의 홈 디렉터리와 게스트 디렉터리를 모두 확인할 수 있습니다.

> [!Important]
> 구독을 다른 디렉터리에 연결 하는 경우 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/role-assignments-portal.md) 를 사용 하 여 역할이 할당 된 사용자는 액세스 권한을 잃게 됩니다. 서비스 관리자 및 공동 관리자를 비롯한 클래식 구독 관리자도 액세스 권한을 잃게 됩니다.
>
> 구독을 다른 디렉터리와 연결하는 경우에도 구독에서 정책 할당이 제거됩니다.
>
> AKS (Azure Kubernetes Service) 클러스터를 다른 구독으로 이동 하거나 클러스터 소유 구독을 새 테 넌 트로 이동 하면 손실 된 역할 할당 및 서비스 주체 권한으로 인해 클러스터가 기능을 잃게 됩니다. AKS에 대 한 자세한 내용은 [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

구독을 연결 하거나 추가 하려면 먼저 다음 작업을 수행 합니다.

- 구독을 연결 하거나 추가한 후에 수행 되는 변경 내용 및 영향을 받는 방법에 대 한 다음 목록을 검토 하세요.

  - Azure RBAC를 사용 하 여 역할이 할당 된 사용자는 액세스 권한을 잃게 됩니다.
  - 서비스 관리자 및 공동 관리자가 액세스할 수 없게 됩니다.
  - 키 자격 증명 모음이 있는 경우 액세스할 수 없게 되며 연결 후 수정 해야 합니다.
  - Virtual Machines 또는 Logic Apps와 같은 리소스에 대 한 관리 id가 있는 경우 연결 후 다시 사용 하도록 설정 하거나 다시 만들어야 합니다.
  - 등록 된 Azure Stack 있는 경우 연결 후 다시 등록 해야 합니다.
  - 자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도(미리 보기)](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

- 다음 조건을 충족하는 계정을 사용하여 로그인해야 합니다.

  - 구독에 대 한 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할 할당을 포함 합니다. 소유자 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조 하세요.
  - 는 현재 디렉터리와 새 디렉터리에 모두 있습니다. 현재 디렉터리는 구독과 연결 되어 있습니다. 새 디렉터리를 구독과 연결 합니다. 다른 디렉터리에 대 한 액세스 권한을 얻는 방법에 대 한 자세한 내용은 [Azure Portal에서 B2B 공동 작업 사용자 추가 Azure Active Directory](../b2b/add-users-administrator.md)를 참조 하세요.

- Azure CSP(클라우드 서비스 공급자) 구독(MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), Microsoft 내부 구독(MS-AZR-0015P) 또는 Microsoft Imagine 구독(MS-AZR-0144P)을 사용하고 있지 않은지 확인합니다.

## <a name="associate-a-subscription-to-a-directory"></a>디렉터리에 구독 연결<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

기존 구독을 Azure AD 디렉터리에 연결 하려면 다음 단계를 수행 합니다.

1. 로그인 하 고 [Azure Portal의 구독 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 사용 하려는 구독을 선택 합니다.

1. **디렉터리 변경**을 선택 합니다.

   ![디렉터리 변경 옵션이 강조 표시된 구독 페이지](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. 표시 되는 경고를 검토 하 고 **변경**을 선택 합니다.

   ![디렉터리 페이지를 변경하여 변경 대상 디렉터리 표시](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   구독에 대 한 디렉터리가 변경 된 후에는 성공 메시지가 표시 됩니다.

1. 구독 페이지에서 **디렉터리 전환** 을 선택 하 여 새 디렉터리로 이동 합니다.

   ![예제 정보가 포함 된 디렉터리 전환기 페이지](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   모든 것이 제대로 표시 되려면 몇 시간이 걸릴 수 있습니다. 너무 오래 걸리면 **전역 구독 필터**를 확인 합니다. 이동 된 구독이 숨겨지지 않았는지 확인 합니다. Azure Portal 로그 아웃 했다가 다시 로그인 하 여 새 디렉터리를 확인 해야 할 수 있습니다.

구독 디렉터리 변경은 서비스 수준 작업이므로 구독 청구 소유권에 영향을 주지 않습니다. 계정 관리자는 [계정 센터](https://account.azure.com/subscriptions)에서 서비스 관리자를 계속 변경할 수 있습니다. 원본 디렉터리를 삭제 하려면 구독 청구 소유권을 새 계정 관리자에 게 양도 해야 합니다. 청구 소유권을 전송 하는 방법에 대해 자세히 알아보려면 [Azure 구독의 소유권을 다른 계정으로 이전](../../cost-management-billing/manage/billing-subscription-transfer.md)을 참조 하세요.

## <a name="post-association-steps"></a>연결 후 단계

구독을 다른 디렉터리에 연결한 후 작업을 다시 시작 하려면 다음 작업을 수행 해야 할 수 있습니다.

- 키 자격 증명 모음이 있는 경우 주요 자격 증명 모음 테 넌 트 ID를 변경 해야 합니다. 자세한 내용은 [구독 이동 후에 주요 자격 증명 모음 테 넌 트 ID 변경](../../key-vault/general/move-subscription.md)을 참조 하세요.

- 리소스에 대해 시스템 할당 관리 Id를 사용한 경우에는 이러한 id를 다시 사용 하도록 설정 해야 합니다. 사용자 할당 관리 Id를 사용한 경우에는 이러한 id를 다시 만들어야 합니다. 관리 되는 Id를 다시 사용 하도록 설정 하거나 다시 만든 후에는 해당 id에 할당 된 사용 권한을 다시 설정 해야 합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.

- 이 구독을 사용 하 여 Azure Stack를 등록 한 경우 다시 등록 해야 합니다. 자세한 내용은 [Azure를 사용 하 여 Azure Stack 등록](/azure-stack/operator/azure-stack-registration)을 참조 하세요.

- 자세한 내용은 [다른 Azure AD 디렉터리로 Azure 구독 양도(미리 보기)](../../role-based-access-control/transfer-subscription.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 새 Azure AD 테 넌 트를 만들려면 [빠른 시작: Azure Active Directory에서 새 테 넌 트 만들기](active-directory-access-create-new-tenant.md)를 참조 하세요.

- Microsoft Azure 리소스 액세스를 제어 하는 방법에 대해 자세히 알아보려면 [클래식 구독 관리자 역할, azure 역할 및 AZURE AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조 하세요.

- Azure AD에서 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자에 게 관리자 및 비관리자 역할 할당](active-directory-users-assign-role-azure-portal.md)을 참조 하세요.
