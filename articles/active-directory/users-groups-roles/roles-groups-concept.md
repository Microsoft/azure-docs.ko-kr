---
title: 클라우드 그룹을 사용 하 여 Azure Active Directory에서 역할 할당 관리 Microsoft Docs
description: ID 관리를 위임하기 위한 사용자 지정 Azure AD 역할을 미리 봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할을 관리합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/11/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe509879c38f979525a673890c05fcfe5c8e3880
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798315"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>클라우드 그룹을 사용 하 여 Azure Active Directory에서 역할 할당 관리 (미리 보기)

Azure ad (Azure Active Directory)는 클라우드 그룹을 Azure AD 기본 제공 역할에 할당할 수 있는 공개 미리 보기를 소개 합니다. 이 기능을 사용 하면 그룹을 사용 하 여 전역 및 권한 있는 역할 관리자가 최소한의 노력으로 Azure AD에서 관리자 액세스 권한을 부여할 수 있습니다.

이 예를 살펴보겠습니다. Contoso는 Azure AD 조직에서 직원에 대 한 암호를 관리 하 고 다시 설정 하기 위해 여러 지역에서 사용자를 고용 했습니다. 권한 있는 역할 관리자 또는 전역 관리자에 게 각 사용자에 게 개별적으로 기술 지원팀 관리자 역할을 할당 하도록 요청 하는 대신 Contoso_Helpdesk_Administrators 그룹을 만들어 역할에 할당할 수 있습니다. 사용자가 그룹에 가입 하면 역할이 간접적으로 할당 됩니다. 그러면 기존 거 버 넌 스 워크플로는 그룹 멤버 자격에 대 한 승인 프로세스 및 감사를 처리할 수 있으므로 합법적인 사용자만 그룹의 구성원이 되므로 기술 지원팀 관리자 역할에 할당 됩니다.

## <a name="how-this-feature-works"></a>이 기능의 작동 원리

' IsAssignableToRole ' 속성이 ' t r u e '로 설정 된 새 Office 365 또는 보안 그룹을 만듭니다. Azure AD 역할을 설정 하 여 **그룹에 할당할 수**있는 Azure Portal에서 그룹을 만들 때이 속성을 사용 하도록 설정할 수도 있습니다. 어떤 방법으로 사용자에 게 역할을 할당 하는 것과 동일한 방식으로 하나 이상의 Azure AD 역할에 그룹을 할당할 수 있습니다. 단일 Azure AD 조직 (테 넌 트)에서 최대 200의 역할 할당 가능 그룹을 만들 수 있습니다.

그룹 구성원에 게 역할에 대 한 액세스 권한을 부여할 수 없도록 하려면 Azure AD Privileged Identity Management를 사용할 수 있습니다. 그룹을 Azure AD 역할의 적격 멤버로 할당 합니다. 그런 다음 그룹의 각 구성원은 그룹이 할당 된 역할에 대 한 할당을 활성화할 수 있습니다. 그런 다음 고정 된 기간 동안 해당 역할 할당을 활성화할 수 있습니다.

> [!Note]
> PIM을 통해 Azure AD 역할에 그룹을 할당 하려면 Privileged Identity Management의 업데이트 된 버전 이어야 합니다. Azure AD 조직에서 Privileged Identity Management API를 활용 하기 때문에 이전 버전의 PIM을 사용할 수 있습니다. 별칭에 연결 pim_preview@microsoft.com 하 여 조직을 이동 하 고 API를 업데이트 하세요. [PIM의 AZURE AD 역할 및 기능](../privileged-identity-management/azure-ad-roles-features.md)에 대해 자세히 알아보세요.

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>특수 그룹을 역할에 할당 하기 위해 만드는 이유

그룹이 역할에 할당 된 경우 그룹 멤버 자격을 관리할 수 있는 IT 관리자도 해당 역할의 멤버 자격을 간접적으로 관리할 수 있습니다. 예를 들어 그룹 Contoso_User_Administrators 사용자 계정 관리자 역할에 할당 되어 있다고 가정 합니다. 그룹 멤버 자격을 수정할 수 있는 Exchange 관리자는 Contoso_User_Administrators 그룹에 자신을 추가할 수 있으며, 그러면 사용자 계정 관리자가 됩니다. 여기에서 볼 수 있듯이 관리자는 의도 하지 않은 방식으로 권한을 상승 시킬 수 있습니다.

Azure AD를 사용 하면 그룹에 대해 isAssignableToRole 라는 새 속성을 사용 하 여 역할에 할당 된 그룹을 보호할 수 있습니다. 만든 시간에 isAssignableToRole 속성이 ' t r u e '로 설정 된 클라우드 그룹만 역할에 할당할 수 있습니다. 이 속성은 변경할 수 없습니다. 이 속성을 ' t r u e '로 설정 하 여 그룹을 만든 후에는 변경할 수 없습니다. 기존 그룹에는 속성을 설정할 수 없습니다.
이러한 종류의 잠재적 위반이 발생 하지 않도록 하기 위해 그룹을 역할에 할당 하는 방법을 설계 했습니다.

- 전역 관리자 및 권한 있는 역할 관리자만 역할 할당 가능 그룹을 만들 수 있습니다 ("isAssignableToRole" 속성을 사용 하는 경우).
- Azure AD 동적 그룹이 될 수 없습니다. 즉, 멤버 자격 형식 "할당 됨"이 있어야 합니다. 동적 그룹의 자동화 된 채우기로 인해 원치 않는 계정이 그룹에 추가 되어 역할에 할당 될 수 있습니다.
- 기본적으로 전역 관리자 및 권한 있는 역할 관리자만 역할 할당 그룹의 멤버 자격을 관리할 수 있지만 그룹 소유자를 추가 하 여 역할 할당 그룹의 관리를 위임할 수 있습니다.
- 권한 상승이 발생 하지 않도록 하려면 권한 있는 인증 관리자나 전역 관리자만 역할을 할당 하는 그룹의 멤버 및 소유자 자격 증명을 변경할 수 있습니다.
- 중첩 되지 않습니다. 역할 할당 가능 그룹의 멤버로 그룹을 추가할 수 없습니다.

## <a name="limitations"></a>제한 사항

다음 시나리오는 현재 지원 되지 않습니다.  

- Azure AD 사용자 지정 역할에 클라우드 그룹 할당
- 관리 단위나 응용 프로그램 범위에 대해 Azure AD 역할 (기본 제공 또는 사용자 지정)에 클라우드 그룹을 할당 합니다.
- 온-프레미스 그룹을 Azure AD 역할에 할당 (기본 제공 또는 사용자 지정)

## <a name="known-issues"></a>알려진 문제

- 그룹을 통해 역할을 할당 하는 경우 동적 그룹을 만들거나 수정할 수 없습니다.
- **관리 되는 사용자 로그인 기능에 대해 준비 된 롤아웃 사용** 은 그룹을 통한 할당을 지원 하지 않습니다.
- *AZURE Ad P2 사용이 허가 된 고객만*해당: azure ad 및 PRIVILEGED IDENTITY MANAGEMENT (PIM)을 통해 역할에 활성으로 그룹을 할당 하지 않습니다. 특히 역할 할당을 만들 때 역할 할당 가능 그룹에 역할을 할당 *하지 않고 나중* 에 PIM을 사용 하 여 그룹에 역할을 할당 합니다. 이렇게 하면 사용자가 PIM에서 활성 역할 할당을 볼 수 없는 문제가 발생 하 고 해당 PIM 할당을 제거할 수 없습니다. 적격 할당은이 시나리오에서 영향을 받지 않습니다. 이 할당을 수행 하려고 하면 다음과 같은 예기치 않은 동작이 발생할 수 있습니다.
  - 역할 할당의 종료 시간이 잘못 표시 될 수 있습니다.
  - PIM 포털에서 **내 역할** 은 하나 이상의 그룹을 통해 할당을 허용 하는 방법에 관계 없이 역할 할당을 하나만 표시할 수 있습니다.
- *AZURE AD P2 라이선스 고객만* 그룹을 삭제 한 후에도 여전히 PIM UI에서 역할의 적격 멤버가 표시 됩니다. 기능적으로 문제가 발생 하지 않습니다. 단지 Azure Portal의 캐시 문제입니다.  
- Exchange 관리 센터는 그룹을 통해 역할 멤버 자격을 인식 하지 않지만 PowerShell cmdlet이 작동 합니다.
- Azure Information Protection 포털 (클래식 포털)은 아직 그룹을 통해 역할 멤버 자격을 인식 하지 못합니다. [통합 민감도 레이블 플랫폼으로 마이그레이션한](/azure/information-protection/configure-policy-migrate-labels) 다음, Office 365 Security & 준수 센터를 사용 하 여 그룹 할당을 통해 역할을 관리할 수 있습니다.

이러한 문제를 해결 하 고 있습니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

이 기능을 사용 하려면 Azure AD 조직에서 사용 가능한 Azure AD Premium P1 라이선스가 있어야 합니다. Just-in-time 역할 활성화를 위해 Privileged Identity Management를 사용 하려면 사용 가능한 Azure AD Premium P2 라이선스가 있어야 합니다. 요구 사항에 맞는 적절 한 라이선스를 찾으려면 [무료 및 프리미엄 계획의 일반 공급 기능 비교](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [역할 할당 가능 그룹 만들기](roles-groups-create-eligible.md)
- [역할 할당 가능 그룹에 역할 할당](roles-groups-assign-role.md)