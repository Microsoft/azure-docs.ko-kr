---
title: 구독자와 CSP 간에 Azure 구독 전송
description: 구독자와 CSP 간에 Azure 구독을 전송하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ffb72b4b06b8925cae57989a171d7efbd88c3866
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686609"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>구독자와 CSP 간에 Azure 구독 전송

이 문서에서는 CSP(클라우드 솔루션 공급자) 파트너와 고객 간에 Azure 구독을 전송하는 데 사용되는 고급 단계를 제공합니다.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>CSP 파트너에게 EA 구독 전송

[Azure Expert MSP(관리형 서비스 공급자)](https://partner.microsoft.com/membership/azure-expert-msp)로 인증된 CSP 직접 청구 파트너는 직접 EA(기업계약)가 있는 고객에 대한 Azure 구독 전송을 요청할 수 있습니다. MCA(Microsoft 고객 계약)를 수락하고 Azure 요금제를 구매한 고객에게만 구독 전송이 허용됩니다.

요청이 승인되면 CSP는 고객에게 결합된 청구서를 제공할 수 있습니다. 구독을 전송하는 CSP에 대한 자세한 내용은 [MPA 계정에 대한 Azure 구독의 청구 소유권 얻기](mpa-request-ownership.md)를 참조하세요.

## <a name="other-subscription-transfers-to-a-csp-partner"></a>CSP 파트너에 대한 기타 구독 전송

다른 Azure 구독을 CSP 파트너에게 전송하려면 구독자가 원본 구독에서 CSP 구독으로 리소스를 이동해야 합니다. 다음 지침을 사용하여 구독 간에 리소스를 이동합니다.

1. CSP 파트너와 협력하여 대상 Azure CSP 구독을 만듭니다.
1. 원본 및 대상 CSP 구독이 동일한 Azure AD(Azure Active Directory) 테넌트에 있는지 확인합니다.  
    Azure CSP 구독에 대한 Azure AD 테넌트는 변경할 수 없습니다. 대신 원본 구독을 CSP Azure AD 테넌트에 추가하거나 연결해야 합니다. 더 자세한 내용은 [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조합니다.
    > [!IMPORTANT]
    > - 구독을 다른 Azure AD 디렉터리에 연결하는 경우 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)를 사용하여 역할이 할당된 사용자는 액세스 권한을 잃게 됩니다. 서비스 관리자 및 공동 관리자를 비롯한 클래식 구독 관리자도 액세스 권한을 잃게 됩니다.
    > - 구독을 다른 디렉터리와 연결하는 경우에도 구독에서 정책 할당이 제거됩니다.
1. 전송하는 데 사용하는 사용자 계정에는 두 구독 모두에 대한 [RBAC](add-change-subscription-administrator.md) 소유자 액세스 권한이 있어야 합니다.
1. 시작하기 전에 모든 Azure 리소스를 원본 구독에서 대상 구독으로 이동할 수 있는지 [유효성을 검사](/rest/api/resources/resources/validatemoveresources)합니다.  
    일부 Azure 리소스는 구독 간에 이동할 수 없습니다. 이동할 수 있는 Azure 리소스의 전체 목록을 보려면 [리소스에 대한 이동 작업 지원](../../azure-resource-manager/management/move-support-resources.md)을 참조하세요.
    > [!IMPORTANT]
    >  - Azure CSP는 Azure Resource Manager 서비스만 지원합니다. 원본 구독에 Azure 클래식 배포 모델을 사용하여 만든 Azure 리소스가 있는 경우 마이그레이션하기 전에 [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm)로 마이그레이션해야 합니다. 웹 페이지를 보려면 파트너여야 합니다.

1. 모든 원본 구독 서비스에서 Azure Resource Manager 모델을 사용하는지 확인합니다. 그런 다음, [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md)를 사용하여 원본 구독에서 대상 구독으로 리소스를 전송합니다.
    > [!IMPORTANT]
    >  - 구독 간에 Azure 리소스를 이동하면 구독의 리소스에 따라 서비스 가동 중지 시간이 발생할 수 있습니다.

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP 구독을 다른 제품으로 전송

다른 구독을 CSP 파트너에서 다른 Azure 제품으로 전송하려면 구독자가 원본 CSP 구독과 대상 구독 간에 리소스를 이동해야 합니다.

1. 대상 Azure 구독을 만듭니다.
1. 원본 및 대상 구독이 동일한 Azure Active Directory(Azure AD) 테넌트에 있는지 확인합니다. Azure AD 테넌트 변경에 대한 자세한 내용은 [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.
    변경 디렉터리는 CSP 구독이 아닙니다. 예를 들어 CSP에서 종량제 구독으로 전송하고 있습니다. 디렉터리와 일치하도록 종량제 구독의 디렉터리를 변경해야 합니다.

    > [!IMPORTANT]
    >  - 구독을 다른 디렉터리에 연결하는 경우 [RBAC](../../role-based-access-control/role-assignments-portal.md)를 사용하여 역할이 할당된 사용자는 액세스 권한을 잃게 됩니다. 서비스 관리자 및 공동 관리자를 비롯한 클래식 구독 관리자도 액세스 권한을 잃게 됩니다.
    >  - 구독을 다른 디렉터리와 연결하는 경우에도 구독에서 정책 할당이 제거됩니다.

1. 전송하는 데 사용하는 사용자 계정에는 두 구독 모두에 대한 [RBAC](add-change-subscription-administrator.md) 소유자 액세스 권한이 있어야 합니다.
1. 시작하기 전에 모든 Azure 리소스를 원본 구독에서 대상 구독으로 이동할 수 있는지 [유효성을 검사](/rest/api/resources/resources/validatemoveresources)합니다.
    > [!IMPORTANT]
    >  - 일부 Azure 리소스는 구독 간에 이동할 수 없습니다. 이동할 수 있는 Azure 리소스의 전체 목록을 보려면 [리소스에 대한 이동 작업 지원](../../azure-resource-manager/management/move-support-resources.md)을 참조하세요.

1. [Azure Resource Move](../../azure-resource-manager/management/move-resource-group-and-subscription.md)를 사용하여 원본 구독에서 대상 구독으로 리소스를 전송합니다.
    > [!IMPORTANT]
    >  - 구독 간에 Azure 리소스를 이동하면 구독의 리소스에 따라 서비스 가동 중지 시간이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [MPA 계정에 대한 Azure 구독의 청구 소유권 얻기](mpa-request-ownership.md)
- [Azure Billing으로 계정 및 구독을 관리](../index.yml)하는 방법을 참조하세요.
