---
title: Azure 위임 리소스 관리
description: Azure 위임 리소스 관리는 Azure Lighthouse의 핵심 부분으로, 서비스 공급자가 민첩하고 정밀하게 위임 리소스를 대규모로 관리할 수 있도록 합니다.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767766"
---
# <a name="azure-delegated-resource-management"></a>Azure 위임 리소스 관리

Azure 위임 리소스 관리는 [Azure Lighthouse](../overview.md)의 주요 구성 요소 중 하나입니다. Azure 위임 리소스 관리를 통해 서비스 공급자는 고객 참여 및 온보딩 환경을 간소화하는 동시에, 민첩하고 정확하게 대규모의 위임된 리소스를 관리할 수 있습니다. 고객은 테넌트에 액세스할 수 있는 사용자, 액세스할 수 있는 리소스 및 수행할 수 있는 작업에 대한 제어를 유지합니다.

## <a name="what-is-azure-delegated-resource-management"></a>Azure 위임 리소스 관리란?

Azure 위임 리소스 관리를 통해 한 테넌트에서 다른 테넌트로 리소스를 논리적으로 프로젝션할 수 있습니다. 이렇게 하면 한 Azure AD(Azure Active Directory) 테넌트의 권한 있는 사용자가 해당 고객에게 속한 여러 다른 Azure AD 테넌트에서 관리 작업을 수행할 수 있습니다. 서비스 공급자는 자신의 Azure AD 테넌트에 로그인할 수 있으며 위임된 고객 구독 및 리소스 그룹에서 작업을 수행할 수 있는 권한을 받습니다. 이를 통해 각 개별 고객 테넌트에 로그인하지 않고도 고객을 대신하여 관리 작업을 수행할 수 있습니다.

> [!TIP]
> 테넌트 간 관리를 간소화하기 위해 [자체 Azure AD 테넌트가 여러 개 있는 엔터프라이즈 내에서](enterprise.md) Azure 위임 리소스 관리를 사용할 수도 있습니다.

Azure 위임 리소스 관리를 통해 권한 있는 사용자는 고객의 테넌트에 계정이 없거나 고객 테넌트의 공동 소유자가 되지 않고도 고객 구독의 컨텍스트에서 직접 작업할 수 있습니다.

[교차 테넌트 관리 환경](cross-tenant-management-experience.md)에서는 Azure Policy, Azure Security Center 등의 Azure 관리 서비스를 사용하여 보다 효율적으로 작업할 수 있습니다. 모든 서비스 공급자 활동은 고객의 테넌트에 저장되고 관리 테넌트의 사용자가 볼 수 있는 활동 로그에서 추적됩니다. 즉, 관리 테넌트와 관리형 테넌트의 사용자 모두 변경 내용과 연결된 사용자를 쉽게 식별할 수 있습니다.

Azure Lighthouse에 고객을 쉽게 온보딩할 수 있도록 [새 관리형 서비스 제안 유형을 Azure Marketplace에 게시](../how-to/publish-managed-services-offers.md)할 수 있습니다. 또는 [Azure Resource Manager 템플릿을 배포하여 온보딩 프로세스를 완료](../how-to/onboard-customer.md)할 수 있습니다.

## <a name="how-azure-delegated-resource-management-works"></a>Azure 위임 리소스 관리의 작동 원리

다음은 Azure 위임 리소스 관리의 작동 원리를 개괄적으로 설명한 것입니다.

1. 먼저 그룹, 서비스 주체 또는 사용자가 고객의 Azure 리소스를 관리하는 데 필요한 액세스 권한(역할)을 식별합니다. 액세스 정의에는 [기본 제공 **roledefinition** 값](../../role-based-access-control/built-in-roles.md)(기여자, VM 기여자, 읽기 권한자 등)에 매핑된 테넌트의 **principalId** ID와 함께 관리 테넌트 ID가 포함됩니다.
2. 다음 두 가지 방법 중 하나로 이 액세스 권한을 지정하고 Azure Lighthouse에 고객을 온보딩합니다.
   - 고객이 허용할 [Azure Marketplace 관리형 서비스 제안(프라이빗 또는 퍼블릭)을 게시](../how-to/publish-managed-services-offers.md)합니다.
   - 하나 이상의 특정 구독 또는 리소스 그룹에 대해 [고객의 테넌트에 Azure Resource Manager 템플릿 배포](../how-to/onboard-customer.md)

3. 고객이 온보딩된 후에는 권한 있는 사용자가 관리 테넌트에 로그인하고 정의된 액세스 권한에 따라 지정된 고객 범위에서 작업을 수행할 수 있습니다. 고객은 서비스 공급자 작업을 검토하고 필요한 경우 액세스 권한을 제거할 수 있습니다.

> [!NOTE]
> 여러 [지역](../../availability-zones/az-overview.md#regions)에 있는 위임된 리소스를 관리할 수 있습니다. 그러나 [국가별 클라우드](../../active-directory/develop/authentication-national-cloud.md) 및 Azure 퍼블릭 클라우드에서 구독을 위임하거나 별도의 두 국가별 클라우드 간에 구독을 위임할 수는 없습니다.

## <a name="support-for-azure-delegated-resource-management"></a>Azure 위임 리소스 관리에 대한 지원

Azure 위임 리소스 관리와 관련하여 도움이 필요한 경우 Azure Portal에서 지원 요청을 열 수 있습니다. **문제 유형** 에서 **기술** 을 선택합니다. 구독을 선택한 다음, **Lighthouse**(**모니터링 및 관리**)를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure Marketplace의 관리형 서비스 제품](managed-services-offers.md)에 대해 알아봅니다.
