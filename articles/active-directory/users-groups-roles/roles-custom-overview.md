---
title: Azure Active Directory의 사용자 지정 관리자 역할 | Microsoft Docs
description: ID 관리를 위임하기 위한 사용자 지정 Azure AD 역할을 미리 봅니다. Azure Portal, PowerShell 또는 Graph API에서 Azure 역할을 관리합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 915098130c7a8462bc0e009be04a61b5b0a7985d
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795595"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Azure Active Directory의 사용자 지정 관리자 역할

이 문서에서는 역할 기반 액세스 제어 및 리소스 범위를 사용하는 Azure AD(Azure Active Directory)의 Azure AD 사용자 지정 역할에 대해 알아봅니다. 사용자 지정 Azure AD 역할은 사용자 고유의 사용자 지정 역할을 만들고 구성할 수 있도록 [기본 제공 역할](directory-assign-admin-roles.md)의 기본 권한을 노출합니다. 이 접근 방식을 사용하면 필요할 때 기본 제공 역할보다 세분화된 방식으로 액세스 권한을 부여할 수 있습니다. Azure AD 사용자 지정 역할의 첫 번째 릴리스에는 앱 등록을 관리하는 권한 할당을 위한 역할을 만들 수 있는 기능이 포함되어 있습니다. 엔터프라이즈 애플리케이션, 사용자, 디바이스 등의 조직 리소스에 대한 권한이 앞으로 더 추가될 예정입니다.  

뿐만 아니라 Azure AD 사용자 지정 역할은 전통적인 조직 차원의 할당 외에도 리소스 단위 할당을 지원합니다. 이 접근 방식을 사용하면 모든 리소스(모든 앱 등록)에 대한 액세스 권한을 부여하지 않고 일부 리소스를 관리하는(예: 앱 하나만 등록) 액세스 권한을 부여할 수 있습니다.

Azure AD 역할 기반 액세스 제어는 Azure AD의 공개 미리 보기 기능이며, 모든 유료 Azure AD 라이선스 계획에서 사용할 수 있습니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="understand-azure-ad-role-based-access-control"></a>Azure AD 역할 기반 액세스 제어의 이해

사용자 지정 Azure AD 역할을 사용하여 권한을 부여하는 작업은 사용자 지정 역할 정의를 만든 후 역할 할당을 사용하여 할당하는 두 단계로 이루어진 프로세스입니다. 사용자 지정 역할 정의는 사전 설정 목록에서 추가하는 권한 컬렉션입니다. 이러한 권한은 기본 제공 역할에 사용되는 권한과 동일한 권한입니다.  

역할 정의를 만든 후에는 역할 할당을 만들어서 사용자에게 할당할 수 있습니다. 역할 할당은 지정된 범위의 역할 정의에서 사용자에게 권한을 부여합니다. 이 2단계 프로세스를 통해 단일 역할 정의를 만든 후 여러 범위에서 여러 번 할당할 수 있습니다. 범위는 역할 멤버가 액세스할 수 있는 Azure AD 리소스 세트를 정의합니다. 가장 일반적인 범위는 조직 전체 범위입니다. 조직 전체 범위에서 사용자 지정 역할을 할당할 수 있으며, 이렇게 하면 역할 멤버가 조직의 모든 리소스에 대한 역할 권한을 갖습니다. 개체 범위에서 사용자 지정 역할을 할당할 수도 있습니다. 개체 범위의 예로는 단일 애플리케이션이 있습니다. 조직 내 모든 애플리케이션에 대해 동일한 역할을 한 사용자에게 할당한 다음, 범위가 Contoso Expense Reports 앱인 다른 사용자에게 할당할 수 있습니다.  

Azure AD 기본 제공 및 사용자 지정 역할은 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)와 유사한 방식으로 작동합니다. [두 역할 기반 액세스 제어 시스템 간의 차이점](../../role-based-access-control/rbac-and-directory-admin-roles.md)은 Azure RBAC는 Azure Resource Management를 사용하는 가상 머신 또는 스토리지와 같은 Azure 리소스에 대한 액세스를 제어하고, Azure AD 사용자 지정 역할은 Graph API를 사용하여 Azure AD 리소스에 대한 액세스를 제어한다는 것입니다. 두 시스템 모두 역할 정의 및 역할 할당이라는 개념을 활용합니다.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Azure AD에서 사용자가 리소스에 대한 액세스 권한을 갖고 있는지 확인하는 방법

다음은 사용자가 관리 리소스에 액세스할 수 있는지 확인하기 위해 Azure AD에서 사용하는 단계에 대한 간략한 정보입니다. 이 정보를 사용하여 액세스 문제를 해결할 수 있습니다.

1. 사용자(또는 서비스 주체)가 Microsoft Graph 또는 Azure AD Graph 엔드포인트에 대한 토큰을 획득합니다.

1. 사용자가 발급된 토큰을 사용하여 Microsoft Graph 또는 Azure AD Graph를 통해 Azure AD(Azure Active Directory)에 대한 API 호출을 수행합니다.

1. 환경에 따라 Azure AD에서 다음 작업 중 하나를 수행합니다.

    - 사용자의 액세스 토큰에서 [wids 클레임](../develop/access-tokens.md)을 기반으로 사용자의 역할 멤버 자격을 평가합니다.
    - 직접 또는 그룹 멤버 자격을 통해 작업을 수행할 리소스에 적용되는 사용자의 모든 역할 할당을 검색합니다.

1. Azure AD는 사용자가 이 리소스에 대해 갖는 역할에 API 호출의 작업이 포함되는지 여부를 결정합니다.
1. 사용자에게 요청된 범위에서 작업에 대한 역할이 없으면 액세스 권한이 부여되지 않습니다. 그렇지 않으면 액세스 권한이 부여됩니다.

### <a name="role-assignments"></a>역할 할당

역할 할당은 특정 범위의 사용자에게 역할 정의를 연결하여 Azure AD 리소스 액세스 권한을 부여하는 개체입니다. 역할 할당을 만들어서 액세스 권한을 부여하고, 역할 할당을 제거하여 액세스 권한을 취소합니다. 그 내부를 보면 역할 할당은 다음과 같은 세 가지 요소로 구성됩니다.

- 사용자(Azure Active Directory에 프로필이 있는 개인)
- 역할 정의
- 리소스 범위

Azure portal, Azure AD PowerShell 또는 Graph API를 사용하여 [역할 할당 만들기](roles-create-custom.md)를 수행할 수 있습니다. [사용자 지정 역할의 할당 보기](roles-view-assignments.md#view-the-assignments-of-a-role)도 가능합니다.

다음 다이어그램은 역할 할당의 예를 보여줍니다. 이 예제에서 Chris Green에게는 Contoso Widget Builder 앱 등록 범위에서 앱 등록 관리자 사용자 지정 역할이 할당되었습니다. 이렇게 할당하면 이 특정 앱 등록에 대해서만 Chris에게 앱 등록 관리자 역할 권한이 부여됩니다.

![역할 할당은 권한이 적용되는 방식이며 세 부분으로 구성됩니다.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>보안 주체

보안 주체는 Azure AD 리소스에 대한 액세스 권한을 할당할 사용자를 나타냅니다. *사용자*는 Azure Active Directory에 프로필이 있는 개인입니다.

### <a name="role"></a>역할

역할 정의 또는 역할은 권한 컬렉션입니다. 역할 정의는 만들기. 읽기, 업데이트 및 삭제와 같이 Azure AD 리소스에 대해 수행할 수 있는 작업을 나열합니다. Azure AD에는 다음과 같은 두 가지 유형의 역할이 있습니다.

- Microsoft에서 만들었으며 변경할 수 없는 기본 제공 역할
- 조직에서 만들고 관리하는 사용자 지정 역할

### <a name="scope"></a>범위

범위는 역할 할당의 일부로 허용되는 작업을 특정 Azure AD 리소스로 제한하는 것입니다. 역할을 할당할 때 관리자의 액세스를 특정 리소스로 제한하는 범위를 지정할 수 있습니다. 예를 들어 개발자에게 특정 애플리케이션 등록만 관리하는 사용자 지정 역할을 부여하려면 특정 애플리케이션 등록을 역할 할당의 범위로 포함하면 됩니다.

  > [!Note]
  > 사용자 지정 역할은 디렉터리 범위 및 리소스 범위에서 할당할 수 있습니다. 아직 관리 단위 범위에서는 할당할 수 없습니다.
  > 기본 제공 역할은 디렉터리 범위에서 할당할 수 있으며, 경우에 따라 관리 단위 범위에서도 할당할 수 있습니다. 아직 Azure AD 리소스 범위에서는 할당할 수 없습니다.

## <a name="required-license-plan"></a>필요한 라이선스 계획

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal, Azure AD PowerShell 및 Graph API](roles-create-custom.md)를 사용하여 사용자 지정 역할 할당 만들기
- [사용자 지정 역할의 할당 보기](roles-view-assignments.md#view-assignments-of-single-application-scope)