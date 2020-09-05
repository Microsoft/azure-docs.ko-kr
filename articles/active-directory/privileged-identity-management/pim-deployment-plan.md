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
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bfe0fee14ed463e265dc4e7e4177c702b051c81
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050202"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD PIM(Privileged Identity Management) 배포

이 문서는 Azure Active Directory (Azure AD) 조직에서 Privileged Identity Management (PIM) 배포를 계획 하는 방법을 설명 하는 단계별 가이드입니다. 가능 하면 권한이 높은 역할의 사용자를 더 강력 하지 않은 기본 제공 역할 또는 사용자 지정 역할에 재할당 하 고 가장 권한 있는 역할에 대 한 just-in-time 역할 할당을 계획 합니다. 이 문서에서는 배포 계획 및 구현에 대 한 권장 사항을 만듭니다.

> [!TIP]
> 이 문서 전체에서 다음과 같이 표시 된 항목이 표시 됩니다.
>
> : heavy_check_mark: **Microsoft 권장**
>
> 이러한 권장 사항은 일반적인 권장 사항 이며 특정 엔터프라이즈 요구에 적용 되는 경우에만 구현 해야 합니다.

## <a name="licensing-requirements"></a>라이선싱 요구 사항

Privileged Identity Management를 사용 하려면 디렉터리에 다음 유료 또는 평가판 라이선스 중 하나가 있어야 합니다. 자세한 내용은 [Privileged Identity Management 사용할 라이선스 요구 사항](subscription-requirements.md)을 참조 하세요.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>PIM 작동 방법

이 섹션에서는 Privileged Identity Management 프로세스의 관련 부분을 계획 하기 위한 검토를 제공 합니다. 자세한 내용은 [Azure AD Privileged Identity Management 란?](pim-configure.md) 을 참조 하세요.

1. 사용자가 권한 있는 역할에 적합 하도록 Privileged Identity Management를 사용 하 여 시작 합니다.
1. 적격 사용자가 권한 있는 역할을 사용 해야 하는 경우 Privileged Identity Management를 사용 하 여 역할을 활성화 합니다.
1. 사용자는 다음과 같은 설정에 필요할 수 있습니다.

    - 다단계 인증 사용
    - 활성화 승인 요청
    - 비즈니스 활성화의 비즈니스 이유 제공

1. 사용자가 자신의 역할을 성공적으로 활성화 한 후에는 설정 된 기간에 대 한 역할 권한이 부여 됩니다.
1. 관리자는 감사 로그의 모든 Privileged Identity Management 활동 기록을 볼 수 있습니다. 또한 Azure AD 조직에 대 한 보안을 강화 하 고 액세스 검토 및 경고와 같은 Privileged Identity Management 기능을 사용 하 여 규정 준수를 충족할 수 있습니다.

## <a name="roles-that-can-be-managed-by-pim"></a>PIM에 의해 관리할 수 있는 역할

**AZURE AD 역할** 은 모두 Azure Active Directory (예: 전역 관리자, Exchange 관리자 및 보안 관리자)에 있습니다. [Azure Active Directory의 관리자 역할 사용 권한](../users-groups-roles/directory-assign-admin-roles.md)에서 역할 및 해당 역할의 기능에 대해 더 자세히 읽을 수 있습니다. 관리자에게 할당하는 역할을 결정하는 데 도움이 필요한 경우 [작업별 최소 권한 있는 역할](../users-groups-roles/roles-delegate-by-task.md)을 참조하세요.

**Azure 역할** 은 azure 리소스, 리소스 그룹, 구독 또는 관리 그룹에 연결 된 역할입니다. PIM을 사용 하 여 소유자, 사용자 액세스 관리자 및 참가자와 같은 기본 제공 Azure 역할 뿐만 아니라 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)에 대 한 just-in-time 액세스를 제공할 수 있습니다. Azure 역할에 대 한 자세한 내용은 [azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 참조 하세요.

자세한 내용은 [Privileged Identity Management에서 관리할 수 없는 역할](pim-roles.md)을 참조 하세요.

## <a name="deployment-plan"></a>배포 계획

조직에서 Privileged Identity Management를 배포 하기 전에이 섹션의 지침을 따르고 조직의 권한 있는 id 요구 사항에 맞게 조정 된 계획을 만드는 방법을 알아봅니다.

### <a name="identify-your-stakeholders"></a>관련자 식별

다음 섹션에서는 프로젝트와 관련 된 모든 관련자를 식별 하는 데 도움이 되며, 로그 아웃 하거나 검토 하거나 정보를 유지 해야 합니다. Azure AD 역할에 대 한 PIM 및 Azure 역할에 대 한 PIM을 배포 하기 위한 별도의 테이블이 포함 되어 있습니다. 조직을 위해 적절한 경우 관련자를 다음 테이블에 추가합니다.

- SO = 이 프로젝트에서 로그오프
- R =이 프로젝트를 검토 및 입력 제공
- I = 이 프로젝트에 대한 알림을 받음

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>관련자: Azure AD 역할에 대 한 Privileged Identity Management

| Name | 역할 | 작업 |
| --- | --- | --- |
| 이름 및 이메일 | **설계자 또는 Azure 글로벌 관리자 식별**<br/>조직의 핵심 id 관리 인프라와 이러한 변경을 맞추는 방법을 정의 하는 것을 담당 하는 id 관리 팀의 담당자입니다. | SO/R/I |
| 이름 및 이메일 | **서비스 소유자 / 라인 관리자**<br/>서비스 또는 서비스 그룹의 IT 소유자 출신 담당자입니다. 이는 의사 결정을 내리는 데 중요 하며, 팀의 Privileged Identity Management을 롤아웃 하는 데 도움이 됩니다. | SO/R/I |
| 이름 및 이메일 | **보안 소유자**<br/>계획에서 조직의 보안 요구 사항을 충족 하는 데 사용할 수 있는 보안 팀의 담당자입니다. | SO/R |
| 이름 및 이메일 | **IT 지원 관리자 / 고객 지원팀**<br/>기술 지원팀 관점에서이 변경의 지원 가능성에 대 한 피드백을 제공할 수 있는 IT 지원 조직의 담당자입니다. | R/I |
| 파일럿 사용자에 대한 이름 및 이메일 | **권한 있는 역할 사용자**<br/>권한 있는 ID 관리를 구현하는 사용자 그룹입니다. Privileged Identity Management 구현 될 때 역할을 활성화 하는 방법을 알고 있어야 합니다. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>관련자: Azure 역할에 대 한 Privileged Identity Management

| Name | 역할 | 작업 |
| --- | --- | --- |
| 이름 및 이메일 | **구독 / 리소스 소유자**<br/>Privileged Identity Management 배포 하려는 각 구독 또는 리소스의 IT 소유자 담당자 | SO/R/I |
| 이름 및 이메일 | **보안 소유자**<br/>계획이 조직의 보안 요구 사항을 만족하도록 로그오프할 수 있는 보안 팀 출신 담당자입니다. | SO/R |
| 이름 및 이메일 | **IT 지원 관리자 / 고객 지원팀**<br/>기술 지원팀 관점에서이 변경의 지원 가능성에 대 한 피드백을 제공할 수 있는 IT 지원 조직의 담당자입니다. | R/I |
| 파일럿 사용자에 대한 이름 및 이메일 | **Azure 역할 사용자**<br/>권한 있는 ID 관리를 구현하는 사용자 그룹입니다. Privileged Identity Management 구현 될 때 역할을 활성화 하는 방법을 알고 있어야 합니다. | I |

### <a name="start-using-privileged-identity-management"></a>Privileged Identity Management 사용 시작

계획 프로세스의 일부로 [Privileged Identity Management 사용 시작](pim-getting-started.md) 문서에 따라 Privileged Identity Management 준비 해야 합니다. Privileged Identity Management를 사용 하면 배포에 도움이 되도록 설계 된 일부 기능에 액세스할 수 있습니다.

Azure 리소스에 대 한 Privileged Identity Management를 배포 하는 것이 목표 인 경우 [Privileged Identity Management에서 관리할 Azure 리소스 검색](pim-resource-roles-discover-resources.md) 문서를 따라야 합니다. 구독 및 관리 그룹의 소유자만 이러한 리소스를 Privileged Identity Management 하 여 관리 하는 방식으로 가져올 수 있습니다. 관리 하는 동안 관리 그룹, 구독, 리소스 그룹 및 리소스를 비롯 한 모든 수준의 소유자에 대해 PIM 기능을 사용할 수 있습니다. 전역 관리자가 Azure 리소스에 대 한 Privileged Identity Management를 배포 하려는 경우 [모든 azure 구독을 관리 하기 위해 액세스 권한을 상승](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 시켜 검색을 위해 디렉터리의 모든 azure 리소스에 대 한 액세스 권한을 부여할 수 있습니다. 그러나 Privileged Identity Management를 사용 하 여 리소스를 관리 하기 전에 각 구독 소유자 로부터 승인을 받을 것을 권장 합니다.

### <a name="enforce-principle-of-least-privilege"></a>최소 권한 원칙 적용

조직에서 Azure AD와 Azure 역할 모두에 대해 최소 권한의 원칙을 적용 했는지 확인 하는 것이 중요 합니다.

#### <a name="plan-least-privilege-delegation"></a>최소 권한 위임 계획

Azure AD 역할의 경우에는 대부분의 관리자에 게 하나 또는 두 개의 강력 하 고 강력 하지 않은 관리자 역할만 필요할 때 조직에서 많은 관리자에 게 전역 관리자 역할을 할당 하는 것이 일반적입니다. 전역 관리자 또는 기타 높은 권한 역할을 많이 사용 하는 경우 권한 있는 역할 할당을 매우 충분히 추적 하기가 어렵습니다.

Azure AD 역할에 대 한 최소 권한의 원칙을 구현 하려면 다음 단계를 수행 합니다.

1. [사용할 수 있는 Azure AD 관리자 역할](../users-groups-roles/directory-assign-admin-roles.md#available-roles)을 읽고 이해하여 역할의 세분성을 알아 두세요. 또한 자신과 자신의 팀이 특정 작업에 대한 최소 권한 있는 역할을 설명하는 [Azure AD의 ID 작업별 관리자 역할](../users-groups-roles/roles-delegate-by-task.md)도 참조하는 것이 좋습니다.

1. 조직에서 권한 있는 역할을 가진 담당자를 나열합니다. Privileged Identity Management [검색 및 정보 (미리 보기)](pim-security-wizard.md) 를 사용 하 여 노출을 줄일 수 있습니다.

    ![권한 있는 역할을 통해 노출을 줄이기 위한 검색 및 통찰력 (미리 보기) 페이지](./media/pim-deployment-plan/new-preview-page.png)

1. 조직의 모든 글로벌 관리자에 대해 역할이 필요한 이유를 확인합니다. 그런 다음 전역 관리자 역할에서이를 제거 하 고 Azure Active Directory 내에서 낮은 권한이 있는 기본 제공 역할 또는 사용자 지정 역할을 할당 합니다. 참고로, Microsoft는 현재 전역 관리자 역할이 있는 관리자가 10 명에 불과합니다. [Microsoft에서 Privileged Identity Management 사용 하는 방법](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)에 대해 자세히 알아보세요.

1. 모든 다른 Azure AD 역할에 대해 할당 목록을 검토하고 더 이상 역할이 필요 없는 관리자를 식별하여 해당 관리자의 역할을 제거합니다.

마지막 두 단계를 자동화 하려면 Privileged Identity Management에서 액세스 검토를 사용할 수 있습니다. [Privileged Identity Management에서 AZURE ad 역할에 대 한 액세스 검토 시작](pim-how-to-start-security-review.md)의 단계에 따라 하나 이상의 구성원이 있는 모든 azure ad 역할에 대 한 액세스 검토를 설정할 수 있습니다.

![Azure AD 역할에 대 한 액세스 검토 창 만들기](./media/pim-deployment-plan/create-access-review.png)

검토자를 **구성원 (self)** 으로 설정 합니다. 역할의 모든 사용자는 액세스 권한이 필요한 지 확인 하 라는 전자 메일을 받게 됩니다. 또한 고급 설정에서 **승인 사유** 를 설정 하 여 사용자가 역할이 필요한 이유를 명시 해야 합니다. 이 정보에 따라 불필요 한 역할에서 사용자를 제거 하거나 더 세부적인 관리자 역할에 위임할 수 있습니다.

액세스 검토는 이메일을 이용하여 해당 사용자에게 역할에 대한 자신의 액세스 권한을 검토하라고 알립니다. 이메일이 연결되지 않은 권한 있는 계정이 있는 경우 해당 계정의 보조 이메일 필드에 내용을 입력해야 합니다. 자세한 내용은 [Azure AD의 proxyAddresses 특성](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)을 참조하세요.

#### <a name="plan-azure-resource-role-delegation"></a>Azure 리소스 역할 위임 계획

Azure 구독 및 리소스에 대해 각 구독 또는 리소스의 역할을 검토하도록 유사한 액세스 리뷰 프로세스를 설정할 수 있습니다. 이 프로세스의 목표는 각 구독 또는 리소스에 연결 된 소유자 및 사용자 액세스 관리자 할당을 최소화 하 고 불필요 한 할당을 제거 하는 것입니다. 그러나 조직은 흔히 각 구독 또는 리소스의 소유자가 특정 역할(특히 사용자 지정 역할)을 더 잘 알고 있다는 이유로 소유자에게 해당 작업을 위임합니다.

조직에서 Azure 역할에 대 한 PIM을 배포 하려는 전역 관리자 역할을 하는 경우 [모든 azure 구독을 관리 하는 액세스 권한을 상승](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) 시켜 각 구독에 액세스할 수 있습니다. 그런 다음, 각 구독 소유자를 찾고 해당 소유자와 협력하여 불필요한 할당을 제거하고 소유자 역할 할당을 최소화할 수 있습니다.

Azure 구독에 대 한 소유자 역할을 가진 사용자는 azure [리소스에 대 한 액세스 검토](pim-resource-roles-start-access-review.md) 를 사용 하 여 azure AD 역할에 대해 앞에서 설명한 프로세스와 유사한 불필요 한 역할 할당을 감사 하 고 제거할 수도 있습니다.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Privileged Identity Management로 보호할 역할 할당을 결정 합니다.

조직에서 권한 있는 역할 할당을 정리 하 고 나면 Privileged Identity Management를 사용 하 여 보호할 역할을 결정 해야 합니다.

역할이 Privileged Identity Management에 의해 보호 되는 경우 해당 역할에 할당 된 적격 사용자는 역할에 부여 된 권한을 사용 하도록 승격 해야 합니다. 권한 상승 프로세스에는 승인 가져오기, multi-factor authentication 사용 및 활성화 하는 이유 제공이 포함 될 수도 있습니다. 알림과 Privileged Identity Management 및 Azure AD 감사 이벤트 로그를 통해 권한 상승을 추적할 수도 Privileged Identity Management.

Privileged Identity Management로 보호할 역할을 선택 하는 것은 어려울 수 있으며 각 조직 마다 다를 수 있습니다. 이 섹션에서는 Azure AD 및 Azure 역할에 대 한 모범 사례 조언을 제공 합니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

가장 많은 사용 권한이 있는 Azure AD 역할 보호의 우선 순위를 지정 하는 것이 중요 합니다. 모든 Privileged Identity Management 고객의 사용량 패턴에 따라 Privileged Identity Management에서 관리 하는 상위 10 개의 Azure AD 역할은 다음과 같습니다.

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

조직에 가장 중요 한 데이터 및 사용 권한을 고려 하는 것이 중요 합니다. 예를 들어, 일부 조직에서는 데이터에 액세스 하 고 핵심 워크플로를 변경할 수 있으므로 Privileged Identity Management를 사용 하 여 Power BI 관리자 역할 또는 팀 관리자 역할을 보호 하려고 할 수 있습니다.

게스트 사용자가 할당 된 역할이 있는 경우 공격에 취약 합니다.

> [!TIP]
> : heavy_check_mark: 손상 된 게스트 사용자 계정과 관련 된 위험을 줄이기 위해 Privileged Identity Management를 사용 하 여 게스트 사용자로 모든 역할을 관리 하는 것 **이 좋습니다** .

디렉터리 판독기, 메시지 센터 판독기 및 보안 읽기 권한자와 같은 읽기 권한자 역할은 쓰기 권한이 없기 때문에 다른 역할 보다 중요 한 것으로 간주 됩니다. 그러나 이러한 계정에 액세스할 수 있는 공격자가 개인 데이터와 같은 중요 한 데이터를 읽을 수 있기 때문에 이러한 역할을 보호 하는 고객도 있습니다. Privileged Identity Management를 사용 하 여 조직의 독자 역할을 관리할 지 여부를 결정할 때이 위험을 고려해 야 합니다.

#### <a name="azure-roles"></a>Azure 역할

Azure 리소스에 대 한 Privileged Identity Management를 사용 하 여 관리 해야 하는 역할 할당을 결정할 때 먼저 조직에 가장 중요 한 구독/리소스를 식별 해야 합니다. 이러한 구독/역할의 예:

- 가장 중요한 데이터를 호스팅하는 리소스
- 핵심적이고 고객과 마주하는 애플리케이션이 의존하는 리소스

전역 관리자가 가장 중요 한 구독과 리소스를 결정 하는 데 문제가 있는 경우 조직의 구독 소유자에 게 문의 하 여 각 구독에서 관리 하는 리소스 목록을 수집 해야 합니다. 그런 다음 심각도가 손상 된 경우 (낮음, 보통, 높음) 구독 소유자에 게 문의 하 여 리소스를 그룹화 합니다. 이 심각도 수준에 따라 Privileged Identity Management를 사용 하 여 리소스 관리의 우선 순위를 지정 합니다.

> [!TIP]
> : heavy_check_mark: 중요 한 서비스의 구독/리소스 소유자와 협력 하 여 중요 한 구독/리소스 내의 모든 역할에 대 한 Privileged Identity Management 워크플로를 설정 하는 것 **이 좋습니다** .

Azure 리소스에 대 한 Privileged Identity Management는 시간 제한 서비스 계정을 지원 합니다. 서비스 계정을 일반적인 사용자 계정을 처리하는 방법과 동일한 방법으로 처리하는 것이 좋습니다.

중요 하지 않은 구독/리소스의 경우 모든 역할에 대해 Privileged Identity Management를 설정할 필요가 없습니다. 그러나 Privileged Identity Management를 사용 하 여 소유자 및 사용자 액세스 관리자 역할을 계속 보호 해야 합니다.

> [!TIP]
> : heavy_check_mark: Privileged Identity Management를 사용 하 여 모든 구독/리소스의 소유자 역할 및 사용자 액세스 관리자 역할을 관리 하는 것 **이 좋습니다** .

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>그룹을 사용 하 여 역할을 할당할지 여부 결정

개별 사용자가 아닌 그룹에 역할을 할당할지 여부는 전략적 결정입니다. 계획할 때 다음의 경우 역할 할당을 관리 하는 역할을 그룹에 할당 하는 것이 좋습니다.

- 여러 사용자가 역할에 할당 됨
- 역할 할당을 위임 하려고 합니다.

#### <a name="many-users-are-assigned-to-a-role"></a>여러 사용자가 역할에 할당 됨

역할에 할당 된 사용자를 추적 하 고 필요한 시기에 따라 할당을 관리 하는 작업은 수동으로 수행 하는 데 시간이 걸릴 수 있습니다. 역할에 그룹을 할당 하려면 먼저 역할 할당 가능 [그룹을 만든](../users-groups-roles/roles-groups-create-eligible.md) 다음 해당 그룹을 역할에 적합 한 것으로 할당 합니다. 이 작업은 그룹의 모든 사용자에 게 역할을 상승 시킬 수 있는 개별 사용자와 동일한 정품 인증 프로세스를 주체 합니다. 그룹 멤버는 Privileged Identity Management 활성화 요청 및 승인 프로세스를 사용 하 여 그룹에 대 한 할당을 개별적으로 활성화 합니다. 그룹이 활성화 되지 않았습니다. 사용자의 그룹 구성원 자격만 있습니다.

#### <a name="you-want-to-delegate-assigning-the-role"></a>역할 할당을 위임 하려고 합니다.

그룹 소유자는 그룹의 멤버 자격을 관리할 수 있습니다. Azure AD 역할 할당 가능 그룹의 경우 권한 있는 역할 관리자, 전역 관리자 및 그룹 소유자만 그룹 멤버 자격을 관리할 수 있습니다. 멤버는 그룹에 새 멤버를 추가 하 여 할당이 적격 또는 활성 인지 여부에 관계 없이 그룹이 할당 된 역할에 대 한 액세스 권한을 얻습니다. 그룹 소유자를 사용 하 여 할당 된 역할에 대 한 그룹 멤버 관리를 위임 하 여 필요한 권한 범위를 줄입니다. 그룹을 만들 때 그룹에 소유자를 할당 하는 방법에 대 한 자세한 내용은 [AZURE AD에서 역할 할당 가능 그룹 만들기](../users-groups-roles/roles-groups-create-eligible.md)를 참조 하세요.

> [!TIP]
> : heavy_check_mark: Privileged Identity Management에서 관리 하는 Azure AD 역할 할당 가능 그룹을 가져오는 것 **이 좋습니다** . 역할 할당 가능 그룹을 PIM에서 관리 하는 경우 권한 있는 액세스 그룹 이라고 합니다. PIM을 사용 하 여 그룹 소유자가 그룹 멤버 자격을 관리 하려면 먼저 소유자 역할 할당을 활성화 해야 합니다. PIM 관리에서 그룹을 만드는 방법에 대 한 자세한 내용은 [권한 있는 액세스 그룹 가져오기 (미리 보기)를 Privileged Identity Management](groups-discover-groups.md)를 참조 하세요.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>영구적이거나 자격이 있어야 하는 역할 할당 결정

Privileged Identity Management에서 관리할 역할 목록을 결정 한 후에는 적절 한 역할 및 영구 활성 역할을 가져올 사용자를 결정 해야 합니다. 영구적으로 활성 역할은 Azure Active Directory 및 Azure 리소스를 통해 할당 된 일반 역할이 며, 적격 역할은 Privileged Identity Management 에서만 할당 될 수 있습니다.

> [!TIP]
> : heavy_check_mark: Azure AD 역할 및 Azure 역할 모두에 대해 영구적으로 영구 활성 할당을 **사용 하는** 것이 좋습니다 .이 [계정](../users-groups-roles/directory-emergency-access.md)에는 영구 전역 관리자 역할이 있어야 합니다.

현재 관리자가 없도록 하는 것이 좋지만 조직이 지금 바로 이렇게 하는 것은 어려운 경우가 있습니다. 다음은 이 결정을 내릴 때 고려해야 하는 사항입니다.

- 권한 상승 빈도 – 사용자에게 권한 있는 할당 역할이 한 번만 필요한 경우 해당 사용자에게 영구적 할당을 하지 않는 것이 좋습니다. 반면에 사용자가 일상 업무에 대 한 역할을 필요로 하 고 Privileged Identity Management를 사용 하 여 생산성을 크게 줄이는 경우 영구 역할에 대해 고려할 수 있습니다.
- 조직과 관련 된 사례 – 적격 한 역할을 하는 사람이 먼 팀에서 제공 하는 경우 또는 권한 상승 프로세스를 적용 하 고 적용 하는 것이 어려운 경우에는 영구 역할에 대해 고려할 수 있습니다.

> [!TIP]
> : heavy_check_mark: 영구 역할 할당을 사용 하는 사용자에 대 한 되풀이 액세스 검토를 설정 하는 것 **이 좋습니다** (있는 경우). 이 배포 계획의 마지막 섹션에서 정기 액세스 검토에 대해 자세히 알아보기

### <a name="draft-your-privileged-identity-management-settings"></a>Privileged Identity Management 설정 초안

Privileged Identity Management 솔루션을 구현 하기 전에 조직에서 사용 하는 모든 권한 있는 역할에 대 한 Privileged Identity Management 설정의 초안을 작성 하는 것이 좋습니다. 이 섹션에서는 특정 역할에 대 한 Privileged Identity Management 설정의 몇 가지 예를 보여 줍니다 (참조용 으로만 사용할 수 있으며 조직에 따라 다를 수 있음). 이러한 각 설정을 테이블 뒤의 Microsoft 권장 사항을 통해 자세히 설명합니다.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD 역할에 대 한 Privileged Identity Management 설정

| 역할 | MFA 요구 | 알림 | 인시던트 티켓 | 승인 필요 | 승인자 | 활성화 기간 | 영구 관리자 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 전역 관리자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 기타 글로벌 관리자 | 1시간 | 응급 액세스 계정 |
| Exchange 관리자 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | None | 2시간 | None |
| 기술 지원팀 관리자 | :x: | :x: | :heavy_check_mark: | :x: | None | 8시간 | None |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Azure 역할에 대 한 Privileged Identity Management 설정

| 역할 | MFA 요구 | 알림 | 승인 필요 | 승인자 | 활성화 기간 | 활성 관리자 | 활성화 만료 | 자격 만료 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 중요한 구독 소유자 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 구독의 기타 소유자 | 1시간 | None | 해당 없음 | 3개월 |
| 덜 중요한 구독의 사용자 액세스 관리자 | :heavy_check_mark: | :heavy_check_mark: | :x: | None | 1시간 | None | 해당 없음 | 3개월 |
| 가상 머신 참가자 | :x: | :heavy_check_mark: | :x: | None | 3시간 | None | 해당 없음 | 6개월 |

다음 테이블에서 각 설정을 설명합니다.

| 설정 | Description |
| --- | --- |
| 역할 | 설정을 정의하는 역할의 이름입니다. |
| MFA 요구 | 자격이 있는 사용자가 역할을 활성화하기 전에 MFA를 수행해야 하는지 여부입니다.<br/><br/> : heavy_check_mark: 특히 역할에 게스트 사용자가 있는 경우 모든 관리자 역할에 대해 MFA를 적용 하는 것 **이 좋습니다** . |
| 알림 | True로 설정하는 경우 자격이 있는 사용자가 해당 역할을 활성화하면 글로벌 관리자, 권한 있는 역할 관리자 및 조직의 보안 관리자가 이메일 알림을 받습니다.<br/><br/>**참고:** 일부 조직에는 관리자 계정에 연결 된 전자 메일 주소가 없으므로 이러한 전자 메일 알림을 받으려면 관리자가 이러한 전자 메일을 받을 수 있도록 대체 전자 메일 주소를 설정 해야 합니다. |
| 인시던트 티켓 | 자격이 있는 사용자가 자신의 역할을 활성화할 때 인시던트 티켓 번호를 기록해야 하는지 여부입니다. 이 설정은 조직이 원치 않는 활성화를 완화하기 위해 내부 문제 번호를 통해 각 활성화를 식별하는 데 도움이 됩니다.<br/><br/> : heavy_check_mark: Microsoft는 인시던트 티켓 번호를 활용 하 여 Privileged Identity Management 내부 시스템에 연결 하는 것 **이 좋습니다** . 이 방법은 활성화 컨텍스트가 필요한 승인자에 게 유용할 수 있습니다. |
| 승인 필요 | 자격이 있는 사용자가 역할을 활성화하기 위해 승인을 받아야 하는지 여부입니다.<br/><br/> : heavy_check_mark: Microsoft는 가장 많은 권한을 가진 역할에 대 한 승인을 설정 하는 것 **이 좋습니다** . 모든 Privileged Identity Management 고객, 전역 관리자, 사용자 관리자, Exchange 관리자, 보안 관리자 및 암호 관리자의 사용 패턴에 따라 승인 설정이 가장 일반적으로 사용 되는 역할입니다. |
| 승인자 | 자격이 있는 역할을 활성화하기 위해 승인이 필요한 경우 요청을 승인해야 하는 개인을 나열합니다. 기본적으로 Privileged Identity Management는 승인자를 영구적 이거나 적격 인지 여부에 관계 없이 권한 있는 역할 관리자 인 모든 사용자로 설정 합니다.<br/><br/>**참고:** 사용자가 Azure AD 역할 및 역할 승인자 모두에 적합 한 경우 자신을 승인할 수 없습니다.<br/><br/> : heavy_check_mark: 전역 관리자가 아닌 역할 및 빈번한 사용자에 대해 가장 잘 알고 있는 사용자가 될 승인자를 선택 하는 것 **이 좋습니다** . |
| 활성화 기간 | 역할이 만료되기 전에 사용자가 해당 역할에서 활성화되는 기간입니다. |
| 영구 관리자 | 역할에 대한 영구 관리자인(활성화할 필요가 없는) 사용자의 목록입니다.<br/><br/> : heavy_check_mark: 전역 관리자를 제외 하 고 모든 역할에 대 한 관리자가 0 인 경우를 **권장** 합니다. 자격이 있도록 만들어야 하는 개인과 이 계획의 영구 활성화 섹션이어야 하는 개인의 경우 해당 역할에 대해 더 자세히 읽으세요. |
| 활성 관리자 | Azure 리소스의 경우 활성 관리자는 역할을 사용하기 위해 활성화할 필요가 없는 사용자의 목록입니다. 사용자가이 역할을 상실 하는 만료 시간을 설정할 수 있으므로이 목록은 Azure AD 역할에서와 같이 영구 관리자가 아닙니다. |
| 활성화 만료 | Azure 역할에 대 한 활성 역할 할당은 구성 된 기간 후에 만료 됩니다. 15일, 1개월, 3개월, 6개월, 1년 또는 영구 활성 중에서 선택할 수 있습니다. |
| 자격 만료 | Azure 역할에 대 한 적격 역할 할당은이 기간 후 만료 됩니다. 15일, 1개월, 3개월, 6개월, 1년 또는 영구적으로 자격이 있음 중에서 선택할 수 있습니다. |

## <a name="implementation-plan"></a>구현 계획

적절한 계획의 기초는 Azure Active Directory를 사용하여 애플리케이션을 성공적으로 배포할 수 있는 기준입니다.  해당 기준은 온보딩을 단순화하면서도 성공적인 배포에 필요한 시간을 줄이는 지능적인 보안 및 통합을 제공합니다.  이 조합을 통해 애플리케이션을 쉽게 통합하면서도 최종 사용자에 대한 중단 시간을 완화할 수 있습니다.

### <a name="identify-test-users"></a>테스트 사용자 식별

이 섹션을 사용하여 구현 유효성을 검사할 사용자 세트 및/또는 사용자 그룹을 식별합니다. 계획 섹션에서 선택한 설정에 따라 각 역할에 대해 테스트할 사용자를 식별합니다.

> [!TIP]
> : heavy_check_mark: Microsoft에서 각 Azure AD 역할의 서비스 소유자를 테스트 사용자로 지정 하 여 프로세스를 파악 하 고 롤아웃에 대 한 내부 advocator 수 있도록 하는 것 **이 좋습니다** .

이 표에서는 역할에 대 한 설정이 작동 하는지 확인할 테스트 사용자를 식별 합니다.

| 역할 이름 | 테스트 사용자 |
| --- | --- |
| &lt;역할 이름&gt; | &lt;역할을 테스트할 사용자&gt; |
| &lt;역할 이름&gt; | &lt;역할을 테스트할 사용자&gt; |

### <a name="test-implementation"></a>테스트 구현

이제 테스트 사용자를 식별 했으므로이 단계를 사용 하 여 테스트 사용자에 대 한 Privileged Identity Management를 구성 합니다. 조직에서 Azure Portal에서 Privileged Identity Management를 사용 하는 대신 사용자 고유의 내부 응용 프로그램에 Privileged Identity Management 워크플로를 통합 하려는 경우 Privileged Identity Management의 모든 작업도 [GRAPH API](/graph/api/resources/privilegedidentitymanagement-root)를 통해 지원 됩니다.

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Azure AD 역할에 대 한 Privileged Identity Management 구성

1. 계획에 따라 [AZURE AD 역할 설정을 구성](pim-how-to-change-default-settings.md) 합니다.

1. **AZURE AD 역할**로 이동 하 고 **역할**을 선택한 다음 구성한 역할을 선택 합니다.

1. 테스트 사용자 그룹의 경우 이미 영구 관리자 인 경우 해당 사용자를 검색 하 여 해당 사용자가 해당 행에 있는 세 개의 점을 선택 하 여 해당 사용자를 검색 하 고 영구에서 적격으로 변환 합니다. 아직 역할을 할당하지 않은 경우 [새 자격이 있는 할당을 만들](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role) 수 있습니다.

1. 테스트하려는 모든 역할에 대해 1-3단계를 반복합니다.

1. 테스트 사용자를 설정한 후 [자신의 Azure AD 역할을 활성화](pim-how-to-activate-role.md)하는 방법에 대한 링크를 해당 사용자에게 보내는 것이 좋습니다.

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Azure 역할에 대 한 Privileged Identity Management 구성

1. 테스트하려는 구독 또는 리소스 내의 역할에 대해 [Azure 리소스 역할 설정을 구성](pim-resource-roles-configure-role-settings.md)합니다.

1. 해당 구독에 대 한 **Azure 리소스** 로 이동 하 여 **역할**을 선택 하 고 구성한 역할을 선택 합니다.

1. 테스트 사용자 그룹의 경우 해당 사용자가 이미 활성 관리자이면 사용자를 검색하여 자격이 있음으로 만들고 [해당 사용자의 역할 할당을 업데이트](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)할 수 있습니다. 역할이 아직 없는 경우 [새 역할을 할당](pim-resource-roles-assign-roles.md#assign-a-role)할 수 있습니다.

1. 테스트하려는 모든 역할에 대해 1-3단계를 반복합니다.

1. 테스트 사용자를 설정한 후 [자신의 Azure 리소스 역할을 활성화](pim-resource-roles-activate-your-roles.md)하는 방법에 대한 링크를 해당 사용자에게 보내는 것이 좋습니다.

이 단계를 사용하여 역할에 대해 설정한 모든 구성이 올바르게 작동하는지 여부를 확인하는 것이 좋습니다. 다음 테이블을 사용하여 테스트를 문서화하세요. 또한 이 단계를 사용하여 영향을 받는 사용자와의 통신을 최적화하는 것이 좋습니다.

| 역할 | 활성화하는 동안 예상되는 동작 | 실제 결과 |
| --- | --- | --- |
| 전역 관리자 | (1) MFA 요구<br/>(2) 승인 요구<br/>(3) 승인자가 알림을 수신하고 승인할 수 있음<br/>(4) 미리 설정된 시간이 경과한 후 역할 만료 |  |
| 구독 *X*의 소유자 | (1) MFA 요구<br/>(2) 구성된 기간이 경과한 후 자격이 있는 할당 만료 |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>영향을 받는 관련자와 Privileged Identity Management 통신

Privileged Identity Management를 배포 하면 권한 있는 역할의 사용자에 게 추가 단계가 도입 됩니다. Privileged Identity Management는 권한 있는 id와 관련 된 보안 문제를 크게 줄일 수 있지만 변경 내용은 조직 전체 배포 전에 효율적으로 통신 해야 합니다. 영향을 받는 관리자 수에 따라 조직은 흔히 변경 내용에 관한 내부 문서, 비디오 또는 이메일을 만듭니다. 이러한 통신에 자주 포함되는 사항:

- PIM 정의
- 조직에 대한 혜택
- 영향을 받는 개인
- PIM을 롤아웃하는 시기
- 사용자가 자신의 역할을 활성화하기 위해 필요한 추가 단계
    - 당사의 설명서에 대한 링크를 보내는 것이 좋습니다.
    - [Azure AD 역할 활성화](pim-how-to-activate-role.md)
    - [Azure 역할 활성화](pim-resource-roles-activate-your-roles.md)
- PIM과 연결된 문제에 대한 연락처 정보 또는 고객 지원팀 링크

> [!TIP]
> : heavy_check_mark: **Microsoft** 는 기술 지원팀/지원 팀과 함께 시간을 설정 하 여 Privileged Identity Management 워크플로 (조직에 내부 IT 지원 팀이 있는 경우)를 안내 하도록 권장 합니다. 해당 팀에게 적절한 설명서 및 본인의 연락처 정보를 제공합니다.

### <a name="move-to-production"></a>프로덕션 환경으로 이동

테스트가 완료 되 고 성공적으로 완료 되 면 Privileged Identity Management 구성에서 정의한 각 역할의 모든 사용자에 대 한 테스트 단계의 모든 단계를 반복 하 여 Privileged Identity Management를 프로덕션으로 이동 합니다. Azure AD 역할에 대 한 Privileged Identity Management 경우 조직에서 다른 역할에 대 한 Privileged Identity Management를 테스트 하 고 롤아웃하기 전에 전역 관리자에 대 한 Privileged Identity Management를 테스트 하 고 롤아웃 하는 경우가 많습니다. Azure 리소스의 경우 조직은 일반적으로 한 번에 하나의 Azure 구독 Privileged Identity Management 테스트 하 고 롤아웃 합니다.

### <a name="if-a-rollback-is-needed"></a>롤백이 필요한 경우

Privileged Identity Management 프로덕션 환경에서 원하는 대로 작동 하지 않으면 다음 롤백 단계를 통해 Privileged Identity Management를 설정 하기 전에 알려진 양호한 상태로 되돌릴 수 있습니다.

#### <a name="azure-ad-roles"></a>Azure AD 역할

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **AZURE AD 역할** 을 선택한 후 **역할**을 선택 합니다.
1. 구성 된 각 역할에 대해 적격 할당을 사용 하는 모든 사용자에 대해 줄임표 (**...**)를 선택 합니다.
1. 영구적으로 **만들기** 옵션을 선택 하 여 역할 할당을 영구적으로 만듭니다.

#### <a name="azure-roles"></a>Azure 역할

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **Azure 리소스** 를 선택 하 고 롤백하려는 구독 또는 리소스를 선택 합니다.
1. **역할**을 선택합니다.
1. 구성 된 각 역할에 대해 적격 할당을 사용 하는 모든 사용자에 대해 줄임표 (**...**)를 선택 합니다.
1. 영구적으로 **만들기** 옵션을 선택 하 여 역할 할당을 영구적으로 만듭니다.

## <a name="next-steps-after-deploying"></a>배포한 후 다음 단계

프로덕션 환경에 Privileged Identity Management를 성공적으로 배포 하는 것은 조직의 권한 있는 id를 보호 하는 측면에서 매우 중요 한 단계입니다. Privileged Identity Management 배포에는 보안 및 규정 준수를 위해 사용 해야 하는 추가 Privileged Identity Management 기능이 제공 됩니다.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Privileged Identity Management 경고를 사용 하 여 권한 있는 액세스 보호

Privileged Identity Management의 기본 제공 경고 기능을 사용 하 여 조직을 보호 하는 방법에 대 한 자세한 내용은 [보안 경고](pim-how-to-configure-security-alerts.md#security-alerts)를 참조 하세요. 이러한 경고는 관리자가 권한 있는 역할을 사용 하지 않거나, 역할이 Privileged Identity Management 외부에서 할당 되 고, 역할이 너무 자주 활성화 되는 경우 등에 발생 합니다. 조직을 완전하게 보호하기 위해 정기적으로 경고 목록을 검토하고 문제를 해결하는 것이 좋습니다. 다음 방법으로 경고를 확인하고 해결할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure AD Privileged Identity Management**를 엽니다.
1. **AZURE AD 역할** 을 선택한 후 **경고**를 선택 합니다.

> [!TIP]
> : heavy_check_mark: 높은 심각도로 표시 된 모든 경고를 즉시 처리 하는 것 **이 좋습니다** . 심각도가 보통 및 낮음인 경고의 경우 계속 알리고 보안 위협이 있다고 여겨지는 경우 변경하는 것이 좋습니다.

특정 경고가 유용하지 않거나 조직에 적용되지 않는 경우 언제나 경고 페이지에서 경고를 해제할 수 있습니다. 이 해지를 나중에 언제라도 Azure AD 설정에서 되돌릴 수 있습니다.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>정기적으로 조직의 권한 있는 ID를 감사하는 정기 액세스 검토 설정

액세스 검토는 권한 있는 역할이 할당된 사용자 또는 특정 검토자에 대해 각 사용자에게 권한 있는 ID가 필요한지 여부를 묻는 최선의 방법입니다. 액세스 검토는 공격 노출을 줄이고 규정 준수를 유지하려는 경우 유용합니다. 액세스 검토를 시작 하는 방법에 대 한 자세한 내용은 [AZURE AD 역할 액세스 검토](pim-how-to-start-security-review.md) 및 [azure 역할 액세스 검토](pim-resource-roles-start-access-review.md)를 참조 하세요. 일부 조직의 경우 법과 규정 준수를 유지하기 위해 정기 액세스 검토를 수행해야 하는 반면에, 액세스 검토가 조직 전체에 걸쳐 최소 권한 원칙을 준수하는 최선의 방법인 경우도 있습니다.

> [!TIP]
> : heavy_check_mark: 모든 Azure AD 및 Azure 역할에 대해 분기별 액세스 검토를 설정 하는 것 **이 좋습니다** .

대부분의 경우 azure AD 역할에 대 한 검토자는 사용자 자신 이며 Azure 역할의 검토자는 역할이 인 구독의 소유자입니다. 그러나 회사가 특정 개인의 이메일 주소와 연결되지 않은 권한 있는 계정을 가진 경우가 많습니다. 이러한 경우 아무도 액세스를 확인하거나 검토하지 않습니다.

> [!TIP]
> : heavy_check_mark: 정기적으로 확인 된 전자 메일 주소에 연결 되지 않은 권한 있는 역할 할당이 있는 모든 계정에 대해 보조 메일 주소를 추가 하는 것 **이 좋습니다** .

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>보안 및 규정 준수를 개선하기 위해 감사 로그를 최대한 이용

감사 로그를 통해 규정에 대한 최신 정보를 유지하고 규정을 준수할 수 있습니다. 현재 Privileged Identity Management은 다음을 비롯 하 여 감사 로그 내에서 조직의 모든 기록에 대 한 30 일 기록을 저장 합니다.

- 자격이 있는 역할 활성화/비활성화
- Privileged Identity Management 내부 및 외부의 역할 할당 작업
- 역할 설정 변경 내용
- 승인 설정이 있는 역할 활성화 요청/승인/거부 활동
- 경고 업데이트

전역 관리자 또는 권한 있는 역할 관리자 인 경우 감사 로그에 액세스할 수 있습니다. 자세한 내용은 azure [AD 역할에 대 한 감사 기록](pim-how-to-use-audit-log.md) 및 [azure 역할에 대 한 감사 기록](azure-pim-resource-rbac.md)을 참조 하세요.

> [!TIP]
> : heavy_check_mark: **Microsoft** 는 한 명 이상의 관리자가 매주 모든 감사 이벤트를 읽고 감사 이벤트를 월별로 내보내는 것을 권장 합니다.

감사 이벤트를 오랜 기간 동안 자동으로 저장 하려는 경우 Privileged Identity Management의 감사 로그는 [AZURE AD 감사 로그](../reports-monitoring/concept-audit-logs.md)에 자동으로 동기화 됩니다.

> [!TIP]
> : heavy_check_mark: 보안 및 규정 준수를 강화 하기 위해 azure 저장소 계정에 감사 이벤트를 보관 하도록 [azure 로그 모니터링](../reports-monitoring/concept-activity-logs-azure-monitor.md) 을 설정 하는 것 **이 좋습니다** .
