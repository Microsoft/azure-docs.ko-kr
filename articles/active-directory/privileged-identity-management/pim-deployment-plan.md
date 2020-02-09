---
title: 배포 Privileged Identity Management (PIM)-Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management) 배포를 계획하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 02/04/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8c77b3454026aa309d979bd938674e7c3ae7b6a
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025999"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD PIM(Privileged Identity Management) 배포

이 단계별 가이드에서는 Azure Active Directory (Azure AD) 조직에서 Privileged Identity Management (PIM) 배포를 계획 하는 방법을 설명 합니다.

> [!TIP]
> 이 문서 전체에서 다음과 같이 표시 된 항목이 표시 됩니다.
> 
> : heavy_check_mark: **Microsoft 권장**
> 
> 이러한 항목은 일반 권장 사항이며, 특정 기업 요구에 적용되는 경우에만 구현하는 것이 좋습니다.

## <a name="learn-about-privileged-identity-management"></a>Privileged Identity Management에 대해 알아보기

Azure AD Privileged Identity Management는 Azure AD, Azure 리소스 및 기타 Microsoft Online Services에서 권한 있는 관리 역할을 관리 하는 데 도움이 됩니다. 권한 있는 id가 할당 되 고 기억나지 않는 세계에서, Privileged Identity Management는 just-in-time 액세스, 요청 승인 워크플로 및 완전히 통합 된 액세스 검토와 같은 솔루션을 제공 하므로 악의적인 사용자를 식별 하 고, 파악 하 고, 차단할 수 있습니다. 실시간으로 권한 있는 역할의 활동입니다. 조직 전체에서 권한 있는 역할을 관리 하기 위해 Privileged Identity Management를 배포 하면 권한 있는 역할의 활동에 대 한 중요 한 통찰력을 얻을 수 있을 때 위험을 크게 줄일 수 있습니다.

### <a name="business-value-of-privileged-identity-management"></a>Privileged Identity Management의 비즈니스 가치

**위험 관리** - [최소 권한 액세스](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) 및 Just-In-Time 액세스 원칙을 적용하여 조직을 보호합니다. 권한 있는 역할에 영구적으로 할당하는 사용자 수를 최소화하고 권한 상승에 대한 승인 및 MFA를 적용하여 조직의 권한 있는 액세스와 관련된 보안 위험을 크게 줄일 수 있습니다. 또한 최소 권한 및 Just-In-Time 액세스를 적용하면 권한 있는 역할에 대한 액세스 기록을 보고 보안 문제가 발생할 때 해당 문제를 추적할 수 있습니다.

**주소 규정 준수 및 거 버 넌** 스-Privileged Identity Management 배포 하 여 진행 중인 id 거 버 넌 스에 대 한 환경을 만듭니다. 권한 있는 id의 just-in-time 권한 상승은 Privileged Identity Management 조직에서 권한 있는 액세스 활동을 추적 하는 방법을 제공 합니다. 또한 조직 내에서 영구적이고 자격이 있는 역할의 모든 할당에 대해 알림을 보고 수신할 수 있습니다. 액세스 검토를 통해 정기적으로 감사를 수행하여 불필요한 권한 있는 ID를 제거하고 조직이 가장 엄격한 ID, 액세스 및 보안 표준을 준수하도록 할 수 있습니다.

**비용 절감** -Privileged Identity Management를 올바르게 배포 하 여 비효율성, 인간 오류 및 보안 문제를 제거 하 여 비용을 절감 합니다. 순수한 결과는 복구하려면 비용이 많이 들고 어려운 권한 있는 ID와 연결된 사이버 범죄가 감소한다는 것입니다. 또한 Privileged Identity Management는 규정 및 표준을 준수 하는 경우 조직에서 액세스 정보 감사와 관련 된 비용을 절감할 수 있도록 지원 합니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](pim-configure.md)을 참조하세요.

### <a name="licensing-requirements"></a>라이선싱 요구 사항

Privileged Identity Management를 사용 하려면 디렉터리에 다음 유료 또는 평가판 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5
- Microsoft 365 M5

자세한 내용은 [Privileged Identity Management 사용할 라이선스 요구 사항](subscription-requirements.md)을 참조 하세요.

### <a name="key-terminology"></a>주요 용어

| 용어 또는 개념 | Description |
| --- | --- |
| 적격 | 사용자가 역할을 사용하기 위해 하나 이상의 작업을 수행해야 하는 역할 할당입니다. 사용자가 역할에 대한 자격을 얻은 경우 권한 있는 작업을 수행해야 할 때 해당 역할을 활성화할 수 있음을 의미합니다. 영구 및 적격 역할 할당을 비교했을 때 이 둘을 통해 다른 사람에게 주어진 액세스에는 차이가 없습니다. 유일한 차이는 사람들이 그 액세스를 항상 필요로 하지 않는다는 점입니다. |
| 활성화 | 사용자가 자격이 있는 역할을 사용하기 위해 하나 이상의 작업을 수행하는 프로세스입니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다. |
| JIT(Just In Time) 액세스 | 사용자가 권한 있는 작업을 수행하기 위해 임시 사용 권한을 받는 모델이며, 악의적 또는 권한이 없는 사용자가 권한이 만료된 후 액세스 권한을 획득하는 것을 방지합니다. 액세스 권한은 사용자에게 필요한 경우에만 부여됩니다. |
| 최소 권한 액세스 원칙 | 모든 사용자에게 수행할 권한이 있는 작업을 수행하는 데 필요한 최소 권한만 제공하는 권장 보안 방법입니다. 이 방법은 글로벌 관리자 수를 최소화하고, 그 대신 특정 시나리오에 대해 구체적 관리자 역할을 사용합니다. |

자세한 내용은 [용어](pim-configure.md#terminology)를 참조하세요.

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Privileged Identity Management 작동 방식에 대 한 개략적인 개요

1. 사용자가 권한 있는 역할에 적합 하도록 Privileged Identity Management 설정 됩니다.
1. 적격 사용자가 권한 있는 역할을 사용 해야 하는 경우 Privileged Identity Management에서 역할을 활성화 합니다.
1. 역할에 대해 구성 된 Privileged Identity Management 설정에 따라 사용자는 특정 단계를 완료 해야 합니다 (예: multi-factor authentication 수행, 승인 가져오기 또는 이유 지정).
1. 사용자가 해당 역할을 성공적으로 활성화하면 미리 구성된 기간 동안 해당 역할을 획득합니다.
1. 관리자는 감사 로그의 모든 Privileged Identity Management 활동 기록을 볼 수 있습니다. 또한 Azure AD 조직에 대 한 보안을 강화 하 고 액세스 검토 및 경고와 같은 Privileged Identity Management 기능을 사용 하 여 규정 준수를 충족할 수 있습니다.

자세한 내용은 [Azure AD Privileged Identity Management란?](pim-configure.md)을 참조하세요.

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Privileged Identity Management에서 관리할 수 있는 역할

**AZURE AD 역할** – 이러한 역할은 모두 Azure Active Directory (전역 관리자, Exchange 관리자 및 보안 관리자)에 있습니다. [Azure Active Directory의 관리자 역할 사용 권한](../users-groups-roles/directory-assign-admin-roles.md)에서 역할 및 해당 역할의 기능에 대해 더 자세히 읽을 수 있습니다. 관리자에게 할당하는 역할을 결정하는 데 도움이 필요한 경우 [작업별 최소 권한 있는 역할](../users-groups-roles/roles-delegate-by-task.md)을 참조하세요.

**Azure 리소스 역할** – 이 역할은 Azure 리소스, 리소스 그룹, 구독 또는 관리 그룹에 연결됩니다. Privileged Identity Management는 소유자, 사용자 액세스 관리자 및 참가자와 같은 기본 제공 역할 및 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)에 대 한 just-in-time 액세스를 제공 합니다. Azure 리소스 역할에 대한 자세한 내용은 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 참조하세요.

자세한 내용은 [Privileged Identity Management에서 관리할 수 없는 역할](pim-roles.md)을 참조 하세요.

## <a name="plan-your-deployment"></a>배포 계획

이 섹션에서는 조직에서 Privileged Identity Management를 배포 하기 전에 수행 해야 하는 작업에 대해 집중적으로 설명 합니다. 지침 및 이 섹션의 개념은 조직의 권한 있는 ID에 맞게 조정된 최선의 계획을 만들도록 안내하므로, 해당 지침을 따르고 이 섹션의 개념을 이해해야 합니다.

### <a name="identify-your-stakeholders"></a>관련자 식별

다음 섹션은 프로젝트에 참여하여 로그오프하거나 검토하거나 계속 알림을 받아야 하는 모든 관련자를 식별하는 데 도움이 됩니다. Azure AD 역할에 대 한 Privileged Identity Management 배포 및 Azure 리소스 역할에 대 한 Privileged Identity Management에 대 한 별도의 테이블이 포함 되어 있습니다. 조직을 위해 적절한 경우 관련자를 다음 테이블에 추가합니다.

- SO = 이 프로젝트에서 로그오프
- R =이 프로젝트를 검토 및 입력 제공
- I = 이 프로젝트에 대한 알림을 받음

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>관련자: Azure AD 역할에 대 한 Privileged Identity Management

| 이름 | 역할 | 실행력 |
| --- | --- | --- |
| 이름 및 이메일 | **설계자 또는 Azure 글로벌 관리자 식별**<br/>이 변경을 조직의 핵심 ID 관리 인프라와 일치시키는 방법을 정의할 책임이 있는 ID 관리 팀 출신 담당자입니다. | SO/R/I |
| 이름 및 이메일 | **서비스 소유자 / 라인 관리자**<br/>서비스 또는 서비스 그룹의 IT 소유자 출신 담당자입니다. 이는 결정을 내리는 데 중요 하며 팀에 대 한 Privileged Identity Management를 롤아웃 하는 데 도움이 됩니다. | SO/R/I |
| 이름 및 이메일 | **보안 소유자**<br/>계획이 조직의 보안 요구 사항을 만족하도록 로그오프할 수 있는 보안 팀 출신 담당자입니다. | SO/R |
| 이름 및 이메일 | **IT 지원 관리자 / 고객 지원팀**<br/>고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다. | R/I |
| 파일럿 사용자에 대한 이름 및 이메일 | **권한 있는 역할 사용자**<br/>권한 있는 ID 관리를 구현하는 사용자 그룹입니다. Privileged Identity Management 구현 될 때 역할을 활성화 하는 방법을 알고 있어야 합니다. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>관련자: Azure 리소스 역할에 대 한 Privileged Identity Management

| 이름 | 역할 | 실행력 |
| --- | --- | --- |
| 이름 및 이메일 | **구독 / 리소스 소유자**<br/>Privileged Identity Management 배포 하려는 각 구독 또는 리소스의 IT 소유자 담당자 | SO/R/I |
| 이름 및 이메일 | **보안 소유자**<br/>계획이 조직의 보안 요구 사항을 만족하도록 로그오프할 수 있는 보안 팀 출신 담당자입니다. | SO/R |
| 이름 및 이메일 | **IT 지원 관리자 / 고객 지원팀**<br/>고객 지원팀 관점에서 이 변경의 지원 가능성에 대한 입력을 제공할 수 있는 IT 지원 조직 출신 담당자입니다. | R/I |
| 파일럿 사용자에 대한 이름 및 이메일 | **RBAC 역할 사용자**<br/>권한 있는 ID 관리를 구현하는 사용자 그룹입니다. Privileged Identity Management 구현 될 때 역할을 활성화 하는 방법을 알고 있어야 합니다. | I |

### <a name="enable-privileged-identity-management"></a>Privileged Identity Management 사용

계획 프로세스의 일부로 먼저 [Privileged Identity Management 사용 시작](pim-getting-started.md) 문서에 따라 Privileged Identity Management에 동의 하 고이를 사용 하도록 설정 해야 합니다. Privileged Identity Management를 사용 하면 배포에 도움이 되도록 특별히 설계 된 일부 기능에 액세스할 수 있습니다.

Azure 리소스에 대 한 Privileged Identity Management를 배포 하는 것이 목표 인 경우 [Privileged Identity Management에서 관리할 azure 리소스 검색](pim-resource-roles-discover-resources.md) 문서를 따라야 합니다. 구독 및 관리 그룹 소유자만 이러한 리소스를 검색 하 고 Privileged Identity Management로 등록할 수 있습니다. 등록 후에는 관리 그룹, 구독, 리소스 그룹 및 리소스를 비롯 한 모든 수준의 소유자에 게 PIM 기능을 사용할 수 있습니다. 전역 관리자가 Azure 리소스에 대 한 Privileged Identity Management를 배포 하려는 경우 [모든 azure 구독을 관리 하기 위해 액세스 권한을 상승](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 시켜 검색을 위해 디렉터리의 모든 azure 리소스에 대 한 액세스 권한을 부여할 수 있습니다. 그러나 Privileged Identity Management를 사용 하 여 리소스를 관리 하기 전에 각 구독 소유자 로부터 승인을 받을 것을 권장 합니다.

### <a name="enforce-principle-of-least-privilege"></a>최소 권한 원칙 적용

조직에서 Azure AD 역할 및 Azure 리소스 역할에 대해 모두 최소 권한 원칙을 적용했는지 확인해야 합니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

Azure AD 역할의 경우 대부분의 관리자가 한두 가지 특정 관리자 역할만 필요할 때 조직이 많은 수의 관리자에게 글로벌 관리자 역할을 할당하는 것이 일반적입니다. 또한 권한 있는 역할 할당이 관리되지 않은 채로 방치되는 경향이 있습니다.

> [!NOTE]
> 역할 위임의 일반적인 문제:
>
> - Exchange 담당 관리자가 일상적인 작업을 수행하기 위해 Exchange 관리자 역할만 필요한데도 해당 관리자에게 글로벌 관리자 역할을 부여합니다.
> - Office 관리자에게 보안 및 SharePoint 관리자 역할이 모두 필요하기 때문에 해당 관리자에게 글로벌 관리자 역할을 할당하고 한 가지 역할만 위임하는 것이 더 쉽습니다.
> - 오래 전에 작업을 수행하기 위해 관리자에게 보안 관리자 역할을 할당했지만 해당 역할을 제거하지 않았습니다.

Azure AD 역할에 대해 최소 권한 원칙을 적용하려면 아래 단계를 수행합니다.

1. [사용할 수 있는 Azure AD 관리자 역할](../users-groups-roles/directory-assign-admin-roles.md#available-roles)을 읽고 이해하여 역할의 세분성을 알아 두세요. 또한 자신과 자신의 팀이 특정 작업에 대한 최소 권한 있는 역할을 설명하는 [Azure AD의 ID 작업별 관리자 역할](../users-groups-roles/roles-delegate-by-task.md)도 참조하는 것이 좋습니다.

1. 조직에서 권한 있는 역할을 가진 담당자를 나열합니다. [Privileged Identity Management 마법사](pim-security-wizard.md#run-the-wizard) 를 사용 하 여 다음과 같은 페이지를 가져올 수 있습니다.

    ![권한 있는 역할을 가진 사용자를 보여 주는 권한 있는 역할 검색 창](./media/pim-deployment-plan/discover-privileged-roles-users.png)

1. 조직의 모든 글로벌 관리자에 대해 역할이 필요한 이유를 확인합니다. 이전 설명서를 참조 하 여 하나 이상의 세부적인 관리자 역할로 사용자의 작업을 수행할 수 있는 경우 전역 관리자 역할에서 해당 사용자를 제거 하 고 Azure Active Directory 내에 적절 하 게 할당 해야 합니다. Microsoft는 현재 전역 관리자 역할이 있는 관리자가 10 명에 불과합니다. [Microsoft에서 Privileged Identity Management 사용 하는 방법](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)에 대해 자세히 알아보세요.

1. 모든 다른 Azure AD 역할에 대해 할당 목록을 검토하고 더 이상 역할이 필요 없는 관리자를 식별하여 해당 관리자의 역할을 제거합니다.

마지막 두 단계를 자동화 하려면 Privileged Identity Management에서 액세스 검토를 사용할 수 있습니다. [Privileged Identity Management에서 AZURE ad 역할에 대 한 액세스 검토 시작](pim-how-to-start-security-review.md)의 단계에 따라 하나 이상의 구성원이 있는 모든 azure ad 역할에 대 한 액세스 검토를 설정할 수 있습니다.

![Azure AD 역할에 대 한 액세스 검토 창 만들기](./media/pim-deployment-plan/create-access-review.png)

검토자를 **멤버(자신)** 로 설정하는 것이 좋습니다. 이렇게 하면 역할의 모든 멤버에게 이메일을 보내서 해당 멤버가 액세스 권한이 필요한지 여부를 확인하도록 합니다. 또한 사용자가 역할이 필요한 이유를 설명할 수 있도록 고급 설정에서 **승인 시 이유 요구**를 설정하는 것이 좋습니다. 이 정보에 따라 불필요한 역할에서 사용자를 제거하고 글로벌 관리자의 경우 더 세분된 관리자 역할을 위임할 수 있습니다.

액세스 검토는 이메일을 이용하여 해당 사용자에게 역할에 대한 자신의 액세스 권한을 검토하라고 알립니다. 이메일이 연결되지 않은 권한 있는 계정이 있는 경우 해당 계정의 보조 이메일 필드에 내용을 입력해야 합니다. 자세한 내용은 [Azure AD의 proxyAddresses 특성](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)을 참조하세요.

#### <a name="azure-resource-roles"></a>Azure 리소스 역할

Azure 구독 및 리소스에 대해 각 구독 또는 리소스의 역할을 검토하도록 유사한 액세스 리뷰 프로세스를 설정할 수 있습니다. 이 프로세스의 목적은 각 구독 또는 리소스에 연결된 소유자 및 사용자 액세스 관리자 할당을 최소화하고 불필요한 할당을 제거하는 것입니다. 그러나 조직은 흔히 각 구독 또는 리소스의 소유자가 특정 역할(특히 사용자 지정 역할)을 더 잘 알고 있다는 이유로 소유자에게 해당 작업을 위임합니다.

조직에서 Azure 리소스에 대 한 Privileged Identity Management를 배포 하려는 전역 관리자 역할이 있는 IT 관리자 인 경우 [모든 Azure 구독을 관리 하기 위해 액세스 권한을 상승](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 시켜 각 구독에 대 한 액세스 권한을 얻을 수 있습니다. 그런 다음, 각 구독 소유자를 찾고 해당 소유자와 협력하여 불필요한 할당을 제거하고 소유자 역할 할당을 최소화할 수 있습니다.

또한 Azure 구독에 대한 소유자 역할을 가진 사용자는 Azure AD 역할에 대해 앞에서 설명한 프로세스와 유사하게 [Azure 리소스에 대한 액세스 검토](pim-resource-roles-start-access-review.md)를 이용하여 감사를 수행하고 불필요한 역할 할당을 제거할 수 있습니다.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Privileged Identity Management로 보호할 역할 할당을 결정 합니다.

조직에서 권한 있는 역할 할당을 정리 하 고 나면 Privileged Identity Management를 사용 하 여 보호할 역할을 결정 해야 합니다.

역할이 Privileged Identity Management에 의해 보호 되는 경우 해당 역할에 할당 된 적격 사용자는 역할에 부여 된 권한을 사용 하도록 승격 해야 합니다. 또한 권한 상승 프로세스는 승인 획득, 다단계 인증 수행 및/또는 활성화해야 하는 이유 제공을 포함할 수도 있습니다. 알림과 Privileged Identity Management 및 Azure AD 감사 이벤트 로그를 통해 권한 상승을 추적할 수도 Privileged Identity Management.

Privileged Identity Management로 보호할 역할을 선택 하는 것은 어려울 수 있으며 각 조직 마다 다를 수 있습니다. 이 섹션에서는 Azure AD 및 Azure 리소스 역할에 대한 당사의 모범 사례 조언을 제공합니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

가장 많은 수의 사용 권한을 가진 Azure AD 역할을 보호하는 우선 순위를 지정해야 합니다. 모든 Privileged Identity Management 고객의 사용량 패턴에 따라 Privileged Identity Management에서 관리 하는 상위 10 개의 Azure AD 역할은 다음과 같습니다.

1. 전역 관리자
1. 보안 관리자
1. 사용자 관리자
1. Exchange 관리자
1. SharePoint 관리자
1. Intune 관리자
1. 보안 판독기
1. 서비스 관리자
1. 대금 청구 관리자
1. 비즈니스용 Skype 관리자

> [!TIP]
> : heavy_check_mark: Privileged Identity Management를 사용 하 여 모든 전역 관리자 및 보안 관리자를 관리 하는 것 **이 가장 좋습니다** .

조직에 가장 중요한 데이터 및 사용 권한을 고려해야 합니다. 예를 들어, 일부 조직에서는 데이터에 액세스 하 고 핵심 워크플로를 변경 하는 기능이 있기 때문에 Privileged Identity Management를 사용 하 여 Power BI 관리자 역할 또는 팀 관리자 역할을 보호 하려고 할 수 있습니다.

게스트 사용자가 할당된 역할이 있는 경우 특히 공격에 취약합니다.

> [!TIP]
> : heavy_check_mark: 손상 된 게스트 사용자 계정과 관련 된 위험을 줄이기 위해 Privileged Identity Management를 사용 하 여 게스트 사용자로 모든 역할을 관리 하는 것 **이 좋습니다** .

디렉터리 독자, 메시지 센터 독자 및 보안 독자와 같은 독자 역할은 쓰기 사용 권한이 없기 때문에 간혹 다른 역할에 비해 덜 중요한 것으로 여겨지는 경우가 있습니다. 그러나 이러한 계정에 대 한 액세스 권한을 얻은 공격자가 개인 데이터와 같은 중요 한 데이터를 읽을 수 있기 때문에 일부 고객은 이러한 역할을 보호 하기도 합니다. 조직의 독자 역할을 Privileged Identity Management를 사용 하 여 관리 해야 하는지 여부를 결정할 때이를 고려해 야 합니다.

#### <a name="azure-resource-roles"></a>Azure 리소스 역할

Azure 리소스에 대 한 Privileged Identity Management를 사용 하 여 관리 해야 하는 역할 할당을 결정할 때 먼저 조직에 가장 중요 한 구독/리소스를 식별 해야 합니다. 이러한 구독/역할의 예:

- 가장 중요한 데이터를 호스팅하는 리소스
- 핵심적이고 고객과 마주하는 애플리케이션이 의존하는 리소스

가장 중요한 구독/리소스를 결정하는 데 어려움이 있는 글로벌 관리자의 경우 조직의 구독 소유자에게 문의하여 각 구독에 의해 관리되는 리소스의 목록을 수집하는 것이 좋습니다. 그런 다음, 구독 소유자와 협력하여 손상될 경우 심각도 수준(낮음, 보통, 높음)에 따라 리소스를 그룹화하는 것이 좋습니다. 이 심각도 수준에 따라 Privileged Identity Management를 사용 하 여 리소스를 관리 하는 우선 순위를 지정 해야 합니다.

> [!TIP]
> : heavy_check_mark: 중요 한 서비스의 구독/리소스 소유자와 협력 하 여 중요 한 구독/리소스 내의 모든 역할에 대 한 Privileged Identity Management 워크플로를 설정 하는 것 **이 좋습니다** .

Azure 리소스에 대 한 Privileged Identity Management는 시간 제한 서비스 계정을 지원 합니다. 서비스 계정을 일반적인 사용자 계정을 처리하는 방법과 동일한 방법으로 처리하는 것이 좋습니다.

중요 하지 않은 구독/리소스의 경우 모든 역할에 대해 Privileged Identity Management를 설정할 필요가 없습니다. 그러나 Privileged Identity Management를 사용 하 여 소유자 및 사용자 액세스 관리자 역할을 계속 보호 해야 합니다.

> [!TIP]
> : heavy_check_mark: Privileged Identity Management를 사용 하 여 모든 구독/리소스의 소유자 역할 및 사용자 액세스 관리자 역할을 관리 하는 것 **이 좋습니다** .

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>영구적이거나 자격이 있어야 하는 역할 할당 결정

Privileged Identity Management에서 관리할 역할 목록을 결정 한 후에는 적절 한 역할 및 영구 활성 역할을 가져올 사용자를 결정 해야 합니다. 영구적으로 활성 역할은 Azure Active Directory 및 Azure 리소스를 통해 할당 된 일반 역할이 며, 적격 역할은 Privileged Identity Management 에서만 할당 될 수 있습니다.

> [!TIP]
> : heavy_check_mark: Azure AD 역할 및 Azure 리소스 역할에 대 한 영구 활성 할당을 0으로 **유지 하는** 것이 좋습니다 .이 [계정](../users-groups-roles/directory-emergency-access.md)에는 영구 전역 관리자 역할이 있어야 합니다.

현재 관리자가 없도록 하는 것이 좋지만 조직이 지금 바로 이렇게 하는 것은 어려운 경우가 있습니다. 다음은 이 결정을 내릴 때 고려해야 하는 사항입니다.

- 권한 상승 빈도 – 사용자에게 권한 있는 할당 역할이 한 번만 필요한 경우 해당 사용자에게 영구적 할당을 하지 않는 것이 좋습니다. 반면에 사용자가 일상 업무에 대 한 역할을 필요로 하 고 Privileged Identity Management를 사용 하 여 생산성을 크게 줄이는 경우 영구 역할에 대해 고려할 수 있습니다.
- 조직에 고유한 사례 - 자격이 있는 역할을 부여한 개인이 아주 먼 거리의 팀 출신이거나 통신 및 권한 상승 프로세스 적용이 어려운 지점에 대한 고위 경영진인 경우 영구적 역할의 할당을 고려할 수 있습니다.

> [!TIP]
> : heavy_check_mark: 영구 역할 할당을 사용 하는 사용자에 대 한 되풀이 액세스 검토를 설정 하는 것 **이 좋습니다** (있는 경우). 이 배포 계획의 마지막 섹션에서 정기 액세스 검토에 대해 자세히 알아보기

### <a name="draft-your-privileged-identity-management-settings"></a>Privileged Identity Management 설정 초안

Privileged Identity Management 솔루션을 구현 하기 전에 조직에서 사용 하는 모든 권한 있는 역할에 대 한 Privileged Identity Management 설정의 초안을 작성 하는 것이 좋습니다. 이 섹션에서는 특정 역할에 대 한 Privileged Identity Management 설정의 몇 가지 예를 보여 줍니다 (참조용 으로만 사용할 수 있으며 조직에 따라 다를 수 있음). 이러한 각 설정을 테이블 뒤의 Microsoft 권장 사항을 통해 자세히 설명합니다.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD 역할에 대 한 Privileged Identity Management 설정

| 역할 | MFA 요구 | 알림 | 인시던트 티켓 | 승인 필요 | 승인자 | 활성화 기간 | 영구 관리자 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 전역 관리자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 기타 글로벌 관리자 | 1시간 | 응급 액세스 계정 |
| Exchange 관리자 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | 없음 | 2시간 | 없음 |
| 기술 지원팀 관리자 | :x: | :x: | :heavy_check_mark: | :x: | 없음 | 8시간 | 없음 |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Azure 리소스 역할에 대 한 Privileged Identity Management 설정

| 역할 | MFA 요구 | 알림 | 승인 필요 | 승인자 | 활성화 기간 | 활성 관리자 | 활성화 만료 | 자격 만료 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 중요한 구독 소유자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 구독의 기타 소유자 | 1시간 | 없음 | n/a | 3개월 |
| 덜 중요한 구독의 사용자 액세스 관리자 | :heavy_check_mark: | :heavy_check_mark: | :x: | 없음 | 1시간 | 없음 | n/a | 3개월 |
| 가상 머신 참가자 | :x: | :heavy_check_mark: | :x: | 없음 | 3시간 | 없음 | n/a | 6개월 |

다음 테이블에서 각 설정을 설명합니다.

| 설정 | Description |
| --- | --- |
| 역할 | 설정을 정의하는 역할의 이름입니다. |
| MFA 요구 | 자격이 있는 사용자가 역할을 활성화하기 전에 MFA를 수행해야 하는지 여부입니다.<br/><br/> : heavy_check_mark: 특히 역할에 게스트 사용자가 있는 경우 모든 관리자 역할에 대해 MFA를 적용 하는 것 **이 좋습니다** . |
| 알림 | True로 설정하는 경우 자격이 있는 사용자가 해당 역할을 활성화하면 글로벌 관리자, 권한 있는 역할 관리자 및 조직의 보안 관리자가 이메일 알림을 받습니다.<br/><br/>**참고:** 일부 조직에는 관리자 계정에 연결 된 전자 메일 주소가 없으므로 이러한 전자 메일 알림을 받으려면 관리자가 이러한 전자 메일을 받을 수 있도록 대체 전자 메일 주소를 설정 해야 합니다. |
| 인시던트 티켓 | 자격이 있는 사용자가 자신의 역할을 활성화할 때 인시던트 티켓 번호를 기록해야 하는지 여부입니다. 이 설정은 조직이 원치 않는 활성화를 완화하기 위해 내부 문제 번호를 통해 각 활성화를 식별하는 데 도움이 됩니다.<br/><br/> : heavy_check_mark: Microsoft는 인시던트 티켓 번호를 활용 하 여 Privileged Identity Management 내부 시스템에 연결 하는 것 **이 좋습니다** . 이 방법은 활성화에 대한 컨텍스트가 필요한 승인자에게 특히 유용합니다. |
| 승인 필요 | 자격이 있는 사용자가 역할을 활성화하기 위해 승인을 받아야 하는지 여부입니다.<br/><br/> : heavy_check_mark: Microsoft는 가장 많은 권한을 가진 역할에 대 한 승인을 설정 하는 것 **이 좋습니다** . 모든 Privileged Identity Management 고객, 전역 관리자, 사용자 관리자, Exchange 관리자, 보안 관리자 및 암호 관리자의 사용 패턴에 따라 승인 설정이 가장 일반적으로 사용 되는 역할입니다. |
| 승인자 | 자격이 있는 역할을 활성화하기 위해 승인이 필요한 경우 요청을 승인해야 하는 개인을 나열합니다. 기본적으로 Privileged Identity Management는 승인자를 영구적 이거나 적격 인지 여부에 관계 없이 권한 있는 역할 관리자 인 모든 사용자로 설정 합니다.<br/><br/>**참고:** 사용자가 Azure AD 역할 및 역할 승인자 모두에 적합 한 경우 자신을 승인할 수 없습니다.<br/><br/> : heavy_check_mark: 승인자는 전역 관리자가 아닌 특정 역할 및 빈번한 사용자에 대해 가장 잘 알고 있는 사용자로 선택 하는 것 **이 좋습니다** . |
| 활성화 기간 | 역할이 만료되기 전에 사용자가 해당 역할에서 활성화되는 기간입니다. |
| 영구 관리자 | 역할에 대한 영구 관리자인(활성화할 필요가 없는) 사용자의 목록입니다.<br/><br/> : heavy_check_mark: 전역 관리자를 제외 하 고 모든 역할에 대 한 관리자가 0 인 경우를 **권장** 합니다. 자격이 있도록 만들어야 하는 개인과 이 계획의 영구 활성화 섹션이어야 하는 개인의 경우 해당 역할에 대해 더 자세히 읽으세요. |
| 활성 관리자 | Azure 리소스의 경우 활성 관리자는 역할을 사용하기 위해 활성화할 필요가 없는 사용자의 목록입니다. 이 관리자는 이 역할을 상실하는 만료 시간을 설정할 수 있으므로 Azure AD 역할과 같이 영구 관리자라고 하지 않습니다. |
| 활성화 만료 | Azure 리소스 역할에 대한 활성 역할 할당은 이 구성된 기간이 경과한 후 만료됩니다. 15일, 1개월, 3개월, 6개월, 1년 또는 영구 활성 중에서 선택할 수 있습니다. |
| 자격 만료 | Azure 리소스 역할에 대해 자격이 있는 역할 할당은 이 구성된 기간이 경과한 후 만료됩니다. 15일, 1개월, 3개월, 6개월, 1년 또는 영구적으로 자격이 있음 중에서 선택할 수 있습니다. |

## <a name="implement-your-solution"></a>솔루션 구현

적절한 계획의 기초는 Azure Active Directory를 사용하여 애플리케이션을 성공적으로 배포할 수 있는 기준입니다.  해당 기준은 온보딩을 단순화하면서도 성공적인 배포에 필요한 시간을 줄이는 지능적인 보안 및 통합을 제공합니다.  이 조합을 통해 애플리케이션을 쉽게 통합하면서도 최종 사용자에 대한 중단 시간을 완화할 수 있습니다.

### <a name="identify-test-users"></a>테스트 사용자 식별

이 섹션을 사용하여 구현 유효성을 검사할 사용자 세트 및/또는 사용자 그룹을 식별합니다. 계획 섹션에서 선택한 설정에 따라 각 역할에 대해 테스트할 사용자를 식별합니다.

> [!TIP]
> : heavy_check_mark: Microsoft에서 각 Azure AD 역할의 서비스 소유자를 테스트 사용자로 지정 하 여 프로세스를 파악 하 고 롤아웃에 대 한 내부 advocator 수 있도록 하는 것 **이 좋습니다** .

이 테이블에서 각 역할에 대한 설정이 적절한지 확인할 테스트 사용자를 식별하세요.

| 역할 이름 | 테스트 사용자 |
| --- | --- |
| &lt;역할 이름&gt; | &lt;역할을 테스트할 사용자&gt; |
| &lt;역할 이름&gt; | &lt;역할을 테스트할 사용자&gt; |

### <a name="test-implementation"></a>테스트 구현

이제 테스트 사용자를 식별 했으므로이 단계를 사용 하 여 테스트 사용자에 대 한 Privileged Identity Management를 구성 합니다. 조직에서 Azure Portal에서 Privileged Identity Management를 사용 하는 대신 사용자 고유의 내부 응용 프로그램에 Privileged Identity Management 워크플로를 통합 하려는 경우 Privileged Identity Management의 모든 작업도 [GRAPH API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root)를 통해 지원 됩니다.

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Azure AD 역할에 대 한 Privileged Identity Management 구성

1. 계획에 따라 [AZURE AD 역할 설정을 구성](pim-how-to-change-default-settings.md) 합니다.

1. **Azure AD 역할**로 이동하고 **역할**을 클릭한 다음, 방금 구성한 역할을 선택합니다.

1. 테스트 사용자 그룹의 경우 해당 사용자가 이미 영구 관리자이면 사용자를 검색하고 해당 행의 점 3개를 클릭하여 사용자를 영구에서 자격이 있음으로 변환하여 자격이 있도록 만들 수 있습니다. 아직 역할을 할당하지 않은 경우 [새 자격이 있는 할당을 만들](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role) 수 있습니다.

1. 테스트하려는 모든 역할에 대해 1-3단계를 반복합니다.

1. 테스트 사용자를 설정한 후 [자신의 Azure AD 역할을 활성화](pim-how-to-activate-role.md)하는 방법에 대한 링크를 해당 사용자에게 보내는 것이 좋습니다.

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Azure 리소스 역할에 대 한 Privileged Identity Management 구성

1. 테스트하려는 구독 또는 리소스 내의 역할에 대해 [Azure 리소스 역할 설정을 구성](pim-resource-roles-configure-role-settings.md)합니다.

1. 해당 구독에 대한 **Azure 리소스**로 이동하여 **역할**을 클릭하고 방금 구성한 역할을 선택합니다.

1. 테스트 사용자 그룹의 경우 해당 사용자가 이미 활성 관리자이면 사용자를 검색하여 자격이 있음으로 만들고 [해당 사용자의 역할 할당을 업데이트](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)할 수 있습니다. 역할이 아직 없는 경우 [새 역할을 할당](pim-resource-roles-assign-roles.md#assign-a-role)할 수 있습니다.

1. 테스트하려는 모든 역할에 대해 1-3단계를 반복합니다.

1. 테스트 사용자를 설정한 후 [자신의 Azure 리소스 역할을 활성화](pim-resource-roles-activate-your-roles.md)하는 방법에 대한 링크를 해당 사용자에게 보내는 것이 좋습니다.

이 단계를 사용하여 역할에 대해 설정한 모든 구성이 올바르게 작동하는지 여부를 확인하는 것이 좋습니다. 다음 테이블을 사용하여 테스트를 문서화하세요. 또한 이 단계를 사용하여 영향을 받는 사용자와의 통신을 최적화하는 것이 좋습니다.

| 역할 | 활성화하는 동안 예상되는 동작 | 실제 결과 |
| --- | --- | --- |
| 전역 관리자 | (1) MFA 요구<br/>(2) 승인 요구<br/>(3) 승인자가 알림을 수신하고 승인할 수 있음<br/>(4) 미리 설정된 시간이 경과한 후 역할 만료 |  |
| 구독 *X*의 소유자 | (1) MFA 요구<br/>(2) 구성된 기간이 경과한 후 자격이 있는 할당 만료 |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>영향을 받는 관련자와 Privileged Identity Management 통신

Privileged Identity Management를 배포 하면 권한 있는 역할의 사용자에 게 추가 단계가 도입 됩니다. Privileged Identity Management는 권한 있는 id와 관련 된 보안 문제를 크게 줄일 수 있지만 변경 내용은 테넌트 전체 배포 전에 효과적으로 통신 해야 합니다. 영향을 받는 관리자 수에 따라 조직은 흔히 변경 내용에 관한 내부 문서, 비디오 또는 이메일을 만듭니다. 이러한 통신에 자주 포함되는 사항:

- PIM 정의
- 조직에 대한 혜택
- 영향을 받는 개인
- PIM을 롤아웃하는 시기
- 사용자가 자신의 역할을 활성화하기 위해 필요한 추가 단계
    - 당사의 설명서에 대한 링크를 보내는 것이 좋습니다.
    - [Azure AD 역할 활성화](pim-how-to-activate-role.md)
    - [Azure 리소스 역할 활성화](pim-resource-roles-activate-your-roles.md)
- PIM과 연결된 문제에 대한 연락처 정보 또는 고객 지원팀 링크

> [!TIP]
> : heavy_check_mark: **Microsoft** 는 기술 지원팀/지원 팀과 함께 시간을 설정 하 여 Privileged Identity Management 워크플로 (조직에 내부 IT 지원 팀이 있는 경우)를 안내 하도록 권장 합니다. 해당 팀에게 적절한 설명서 및 본인의 연락처 정보를 제공합니다.

### <a name="move-to-production"></a>프로덕션 환경으로 이동

테스트가 완료 되 고 성공적으로 완료 되 면 Privileged Identity Management 구성에서 정의한 각 역할의 모든 사용자에 대 한 테스트 단계의 모든 단계를 반복 하 여 Privileged Identity Management를 프로덕션으로 이동 합니다. Azure AD 역할에 대 한 Privileged Identity Management 경우 조직에서 다른 역할에 대 한 Privileged Identity Management를 테스트 하 고 롤아웃하기 전에 전역 관리자에 대 한 Privileged Identity Management를 테스트 하 고 롤아웃 하는 경우가 많습니다. Azure 리소스의 경우 조직은 일반적으로 한 번에 하나의 Azure 구독 Privileged Identity Management 테스트 하 고 롤아웃 합니다.

### <a name="in-the-case-a-rollback-is-needed"></a>롤백이 필요한 경우

Privileged Identity Management 프로덕션 환경에서 원하는 대로 작동 하지 않으면 다음 롤백 단계를 통해 Privileged Identity Management를 설정 하기 전에 알려진 양호한 상태로 되돌릴 수 있습니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure AD 역할**을 클릭한 다음, **역할**을 클릭합니다.
1. 구성한 각 역할의 자격이 있는 할당을 가진 모든 사용자에 대해 줄임표( **...** )를 클릭합니다.
1. **영구 상태로 만들기** 옵션을 클릭하여 역할 할당을 영구 상태로 만듭니다.

#### <a name="azure-resource-roles"></a>Azure 리소스 역할

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure 리소스**를 클릭한 다음, 롤백하려는 구독 또는 리소스를 클릭합니다.
1. **역할**을 클릭합니다.
1. 구성한 각 역할의 자격이 있는 할당을 가진 모든 사용자에 대해 줄임표( **...** )를 클릭합니다.
1. **영구 상태로 만들기** 옵션을 클릭하여 역할 할당을 영구 상태로 만듭니다.

## <a name="next-steps-after-deploying"></a>배포한 후 다음 단계

프로덕션 환경에 Privileged Identity Management를 성공적으로 배포 하는 것은 조직의 권한 있는 id를 보호 하는 측면에서 매우 중요 한 단계입니다. Privileged Identity Management 배포에는 보안 및 규정 준수를 위해 사용 해야 하는 추가 Privileged Identity Management 기능이 제공 됩니다.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Privileged Identity Management 경고를 사용 하 여 권한 있는 액세스 보호

테넌트를 보다 잘 보호 하기 위해 Privileged Identity Management의 기본 제공 경고 기능을 활용 해야 합니다. 자세한 내용은 [보안 경고](pim-how-to-configure-security-alerts.md#security-alerts)를 참조하세요. 이러한 경고는 관리자가 권한 있는 역할을 사용 하지 않거나, 역할이 Privileged Identity Management 외부에서 할당 되 고, 역할이 너무 자주 활성화 되는 경우 등에 발생 합니다. 조직을 완전하게 보호하기 위해 정기적으로 경고 목록을 검토하고 문제를 해결하는 것이 좋습니다. 다음 방법으로 경고를 확인하고 해결할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure AD 역할**을 클릭한 다음, **경고**를 클릭합니다.

> [!TIP]
> : heavy_check_mark: 높은 심각도로 표시 된 모든 경고를 즉시 처리 하는 것 **이 좋습니다** . 심각도가 보통 및 낮음인 경고의 경우 계속 알리고 보안 위협이 있다고 여겨지는 경우 변경하는 것이 좋습니다.

특정 경고가 유용하지 않거나 조직에 적용되지 않는 경우 언제나 경고 페이지에서 경고를 해제할 수 있습니다. 이 해지를 나중에 언제라도 Azure AD 설정에서 되돌릴 수 있습니다.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>정기적으로 조직의 권한 있는 ID를 감사하는 정기 액세스 검토 설정

액세스 검토는 권한 있는 역할이 할당된 사용자 또는 특정 검토자에 대해 각 사용자에게 권한 있는 ID가 필요한지 여부를 묻는 최선의 방법입니다. 액세스 검토는 공격 노출을 줄이고 규정 준수를 유지하려는 경우 유용합니다. 액세스 검토 설정에 대한 자세한 내용은 [Azure AD 역할 액세스 검토](pim-how-to-start-security-review.md) 및 [Azure 리소스 역할 액세스 검토](pim-resource-roles-start-access-review.md)를 참조하세요. 일부 조직의 경우 법과 규정 준수를 유지하기 위해 정기 액세스 검토를 수행해야 하는 반면에, 액세스 검토가 조직 전체에 걸쳐 최소 권한 원칙을 준수하는 최선의 방법인 경우도 있습니다.

> [!TIP]
> : heavy_check_mark: 모든 Azure AD 및 Azure 리소스 역할에 대해 분기별 액세스 검토를 설정 하는 것 **이 좋습니다** .

대부분의 경우 Azure AD 역할에 대한 검토자는 자신이 사용자인 반면에, Azure 리소스 역할에 대한 검토자는 역할이 포함된 구독의 소유자입니다. 그러나 회사가 특정 개인의 이메일 주소와 연결되지 않은 권한 있는 계정을 가진 경우가 많습니다. 이러한 경우 아무도 액세스를 확인하거나 검토하지 않습니다.

> [!TIP]
> : heavy_check_mark: 정기적으로 확인 된 전자 메일 주소에 연결 되지 않은 권한 있는 역할 할당이 있는 모든 계정에 대해 보조 메일 주소를 추가 하는 것 **이 좋습니다** .

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>보안 및 규정 준수를 개선하기 위해 감사 로그를 최대한 이용

감사 로그를 통해 규정에 대한 최신 정보를 유지하고 규정을 준수할 수 있습니다. 현재 Privileged Identity Management은 다음을 비롯 하 여 감사 로그 내에서 조직의 모든 기록에 대 한 30 일 기록을 저장 합니다.

- 자격이 있는 역할 활성화/비활성화
- Privileged Identity Management 내부 및 외부의 역할 할당 작업
- 역할 설정 변경 내용
- 승인 설정이 있는 역할 활성화 요청/승인/거부 활동
- 경고 업데이트

글로벌 관리자 또는 권한 있는 역할 권리자가 이 감사 로그에 액세스할 수 있습니다. 자세한 내용은 [Azure AD 역할에 대한 감사 기록](pim-how-to-use-audit-log.md) 및 [Azure 리소스 역할에 대한 감사 기록](azure-pim-resource-rbac.md)을 참조하세요.

> [!TIP]
> : heavy_check_mark: **Microsoft** 는 한 명 이상의 관리자가 매주 모든 감사 이벤트를 읽고 감사 이벤트를 월별로 내보내는 것을 권장 합니다.

감사 이벤트를 오랜 기간 동안 자동으로 저장 하려는 경우 Privileged Identity Management의 감사 로그는 [AZURE AD 감사 로그](../reports-monitoring/concept-audit-logs.md)에 자동으로 동기화 됩니다.

> [!TIP]
> : heavy_check_mark: 보안 및 규정 준수를 위해 Azure 저장소 계정에 감사 이벤트를 보관 하도록 [azure 로그 모니터링](../reports-monitoring/concept-activity-logs-azure-monitor.md) 을 설정 하는 것 **이 좋습니다** .
