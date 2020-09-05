---
title: 관리 단위 관리(미리 보기) - Azure AD | Microsoft Docs
description: Azure Active Directory에서 보다 세부적인 권한 위임을 위해 관리 단위 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 276b6cc8038a7ac8e0ddd27a3c3eb7c87f05ed1b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795901"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Azure Active Directory의 관리 단위 관리(미리 보기)

이 문서에서는 Azure AD(Azure Active Directory)의 관리 단위에 대해 설명합니다. 관리 단위는 다른 Azure AD 리소스의 컨테이너가 될 수 있는 Azure AD 리소스입니다. 이 미리 보기 릴리스에서는 관리 단위에 사용자 및 그룹만 포함될 수 있습니다.

관리 단위를 사용하면 정의하는 조직의 부서, 지역 또는 기타 세그먼트로 제한된 관리자 권한을 부여할 수 있습니다. 관리 단위를 사용하여 지역 관리자에게 권한을 위임하거나 세부적인 수준에서 정책을 설정할 수 있습니다. 예를 들어 사용자 계정 관리자는 프로필 정보를 업데이트하고, 암호를 다시 설정하고, 자신의 관리 단위에 포함된 사용자에게만 라이선스를 할당할 수 있습니다.

 예를 들어 지원하는 지역 내의 사용자만 관리하도록 권한이 제한된 지역 지원 전문가인 [기술 지원팀 관리자](directory-assign-admin-roles.md#helpdesk-administrator) 역할을 위임할 수 있습니다.

## <a name="deployment-scenario"></a>배포 시나리오

온갖 종류의 독립 부서로 구성된 조직에서는 관리 단위를 사용하여 관리 범위를 제한하면 도움이 될 수 있습니다. 여러 단과대학(경영대학, 공과대학 등)으로 구성된 종합 대학교를 생각해 보세요. 단과대학마다 액세스를 제어하고 사용자를 관리하고 학교의 정책을 설정하는 IT 관리자 팀이 따로 있습니다. 중앙 관리자는 다음과 같은 작업을 할 수 있습니다.

- 경영대학 관리 단위에 속한 Azure AD 사용자만 관리할 수 있는 역할 만들기
- 경영대학의 관리 단위 만들기
- 관리 단위를 경영대학 학생 및 교직원으로만 채우기
- 경영대학 IT 팀을 해당 범위의 역할에 추가

## <a name="license-requirements"></a>라이선스 요구 사항

관리 단위를 사용하려면 관리 단위 관리자마다 Azure Active Directory Premium 라이선스가 필요하고, 관리 단위 멤버에게는 Azure Active Directory Free 라이선스가 있어야 합니다. 자세한 내용은 [Azure AD Premium 시작을 참조하세요](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>관리 단위 관리

이 미리 보기 릴리스에서는 Azure Portal, PowerShell cmdlet 및 스크립트, 또는 Microsoft Graph를 사용하여 관리 단위를 관리할 수 있습니다. 자세한 내용은 다음 설명서를 참조하세요.

- [역할을 만들고, 관리 단위에 추가 또는 제거하고, 정보 채우기](roles-admin-units-manage.md): 전체 방법 절차
- [관리 단위 작업](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): PowerShell을 사용하여 관리 단위를 작업하는 방법
- [관리 단위 Graph 지원](/graph/api/resources/administrativeunit?view=graph-rest-beta): 관리 단위용 Microsoft Graph에 대한 자세한 설명서입니다.

### <a name="planning-your-administrative-units"></a>관리 단위 계획 수립

관리 단위를 사용하여 Azure AD 리소스를 논리적으로 그룹화할 수 있습니다. 예를 들어 IT 부서가 전 세계에 흩어져 있는 조직의 경우 지리적 경계를 정의하는 관리 단위를 만드는 것이 적합할 수 있습니다. 하위 조직이 반자율적으로(semi-autonomous) 운영되는 다국적 조직 시나리오에서는 각 하위 조직을 관리 단위로 표시할 수 있습니다.

관리 단위를 만드는 기준은 조직의 고유한 요구 사항에 따라 결정됩니다. 관리 단위는 M365 서비스에서 구조를 정의하는 일반적인 방법입니다. M365 서비스에서 사용되는 용도를 염두에 두고 관리 단위를 준비하는 것이 좋습니다. M365 전체에서 관리 단위에 속한 공통 리소스를 연결할 수 있을 때 관리 단위를 최대로 활용할 수 있습니다.

조직에서 관리 단위를 만들 때 일반적으로 다음과 같은 단계를 거치게 됩니다.

1. 초기 채택: 조직에서 초기 조건에 따라 관리 단위 만들기를 시작하고, 조건이 점점 구체화되면서 관리 단위 수가 증가합니다.
1. 삭제: 조건이 잘 정의된 후에는 더 이상 필요 없는 관리 단위를 삭제합니다.
1. 안정화: 조직 구조가 잘 정의되었으며 관리 단위 수가 단기간에 크게 변하지 않습니다.

## <a name="currently-supported-scenarios"></a>현재 지원되는 시나리오

전역 관리자 또는 권한 있는 역할 관리자는 Azure AD 포털을 사용하여 관리 단위를 만들고, 사용자를 관리 단위의 멤버로 추가하고, IT 직원을 관리 단위 범위가 지정된 관리자 역할에 할당할 수 있습니다. 그러면 관리 단위 범위가 지정된 관리자는 Office 365 포털을 사용하여 관리 단위에 속한 사용자의 기본적인 관리 업무를 수행할 수 있습니다.

또한 그룹을 관리 단위의 멤버로 추가할 수 있으며, 관리 단위 범위가 지정된 그룹 관리자는 PowerShell, Microsoft Graph 및 Azure AD 포털을 사용하여 그룹을 관리할 수 있습니다.

아래 표에는 현재 지원되는 관리 단위 시나리오가 설명되어 있습니다.

### <a name="administrative-unit-management"></a>관리 단위 관리

사용 권한 |   MS Graph/PowerShell   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
관리 단위 만들기 및 삭제   |    지원됨    |   지원됨   |    지원되지 않음
관리 단위 멤버를 개별적으로 추가 및 제거    |   지원됨    |   지원됨   |    지원되지 않음
.csv 파일을 사용하여 관리 단위 멤버를 대량으로 추가 및 제거   |    지원되지 않음     |  지원됨   |    지원 계획 없음
관리 단위 범위가 지정된 관리자 할당  |     지원됨    |   지원됨    |   지원되지 않음
특성에 따라 동적으로 AU 멤버 추가 및 제거 | 지원되지 않음 | 지원되지 않음 | 지원되지 않음

### <a name="user-management"></a>사용자 관리

사용 권한 |   MS Graph/PowerShell   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
관리 단위 범위를 지정하여 사용자 속성, 암호, 라이선스 관리   |    지원됨     |  지원됨   |   지원됨
관리 단위 범위를 지정하여 사용자 로그인 차단 및 차단 해제    |   지원됨   |    지원됨   |    지원됨
관리 단위 범위를 지정하여 사용자 MFA 자격 증명 관리   |    지원됨   |   지원됨   |   지원되지 않음

### <a name="group-management"></a>그룹 관리

사용 권한 |   MS Graph/PowerShell   | Azure AD 포털 | Microsoft 365 관리 센터
----------- | ----------------------- | --------------- | -----------------
관리 단위 범위를 지정하여 그룹 속성 및 멤버 관리     |  지원됨   |    지원됨    |  지원되지 않음
관리 단위 범위를 지정하여 그룹 라이선스 관리   |    지원됨  |    지원됨   |   지원되지 않음

> [!NOTE]
>
> 관리 단위 범위가 지정된 관리자는 동적 그룹 멤버 관리 규칙을 관리할 수 없습니다.

관리 단위는 관리 권한에만 범위를 적용합니다. 멤버 또는 관리자가 자신의 [기본 사용자 권한](../fundamentals/users-default-permissions.md)을 사용하여 관리 단위 외부의 다른 사용자, 그룹 또는 리소스를 검색하는 것을 막을 수 없습니다. Office 365 포털에서는 범위가 지정된 관리자의 관리 단위 외부에 있는 사용자가 필터링되지만 Azure AD 포털, PowerShell 및 기타 Microsoft 서비스에서는 다른 사용자를 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [AU 관리](roles-admin-units-manage.md)
- [AU에서 사용자 관리](roles-admin-units-add-manage-users.md)
- [AU에서 그룹 관리](roles-admin-units-add-manage-groups.md)
- [범위가 지정된 역할을 AU에 할당](roles-admin-units-assign-roles.md)