---
title: 자격 관리에서 새 액세스 패키지 만들기-Azure AD
description: Azure Active Directory 자격 관리에서 공유 하려는 리소스의 새 액세스 패키지를 만드는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80dafda39c0b37b1a7477b93d7f649fa1beeade
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783640"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 새 액세스 패키지 만들기

액세스 패키지를 사용 하면 액세스 패키지의 수명 기간 동안 자동으로 액세스를 관리 하는 리소스 및 정책의 일회성 설치를 수행할 수 있습니다. 이 문서에서는 새 액세스 패키지를 만드는 방법을 설명 합니다.

## <a name="overview"></a>개요

모든 액세스 패키지는 카탈로그 라는 컨테이너에 배치 해야 합니다. 카탈로그는 액세스 패키지에 추가할 수 있는 리소스를 정의 합니다. 카탈로그를 지정 하지 않으면 액세스 패키지가 일반 카탈로그에 추가 됩니다. 현재는 기존 액세스 패키지를 다른 카탈로그로 이동할 수 없습니다.

액세스 패키지 관리자 인 경우 소유 하 고 있는 리소스를 카탈로그에 추가할 수 없습니다. 카탈로그에서 사용 가능한 리소스를 사용 하는 것으로 제한 됩니다. 카탈로그에 리소스를 추가 해야 하는 경우 카탈로그 소유자에 게 요청할 수 있습니다.

모든 액세스 패키지에는 하나 이상의 정책이 있어야 합니다. 정책은 액세스 패키지를 요청할 수 있는 사용자와 승인 및 수명 주기 설정도 지정 합니다. 새 액세스 패키지를 만들 때 디렉터리에 없는 사용자, 관리자 직접 할당에 대해서만 또는 나중에 정책을 만들도록 선택할 수 있는 디렉터리의 사용자에 대 한 초기 정책을 만들 수 있습니다.

![액세스 패키지 만들기](./media/entitlement-management-access-package-create/access-package-create.png)

새 액세스 패키지를 만들기 위한 개략적인 단계는 다음과 같습니다.

1. Id 거 버 넌 스에서 프로세스를 시작 하 여 새 액세스 패키지를 만듭니다.

1. 액세스 패키지를 만들려는 카탈로그를 선택 합니다.

1. 카탈로그의 리소스를 액세스 패키지에 추가 합니다.

1. 각 리소스에 대 한 리소스 역할을 할당 합니다.

1. 액세스를 요청할 수 있는 사용자를 지정 합니다.

1. 승인 설정을 지정 합니다.

1. 수명 주기 설정을 지정 합니다.

## <a name="start-new-access-package"></a>새 액세스 패키지 시작

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지**를 클릭합니다.

1. **새 액세스 패키지**를 클릭합니다.
   
    ![Azure Portal에서 권한 관리](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>기본 사항

**기본 사항** 탭에서 액세스 패키지에 이름을 지정 하 고 액세스 패키지를 만들 카탈로그를 지정 합니다.

1. 액세스 패키지에 대 한 표시 이름 및 설명을 입력 합니다. 사용자는 액세스 패키지에 대 한 요청을 제출할 때이 정보를 볼 수 있습니다.

1. **카탈로그** 드롭다운 목록에서 액세스 패키지를 만들려는 카탈로그를 선택 합니다. 예를 들어 요청 될 수 있는 모든 마케팅 리소스를 관리 하는 카탈로그 소유자가 있을 수 있습니다. 이 경우 마케팅 카탈로그를 선택할 수 있습니다.

    에서 액세스 패키지를 만들 수 있는 권한이 있는 카탈로그만 표시 됩니다. 기존 카탈로그에서 액세스 패키지를 만들려면 전역 관리자 또는 사용자 관리자 여야 합니다. 또는 해당 카탈로그에서 카탈로그 소유자 또는 액세스 패키지 관리자 여야 합니다.

    ![액세스 패키지-기본 사항](./media/entitlement-management-access-package-create/basics.png)

    전역 관리자, 사용자 관리자 또는 카탈로그 작성자 이며 나열 되지 않은 새 카탈로그에서 액세스 패키지를 만들려는 경우 **새 카탈로그 만들기**를 클릭 합니다. 카탈로그 이름 및 설명을 입력 하 고 **만들기**를 클릭 합니다.

    만든 액세스 패키지와 여기에 포함 된 리소스는 새 카탈로그에 추가 됩니다. 나중에 카탈로그 소유자를 더 추가할 수도 있습니다.

1. **다음**을 클릭합니다.

## <a name="resource-roles"></a>리소스 역할

**리소스 역할** 탭에서 액세스 패키지에 포함할 리소스를 선택 합니다. 액세스 패키지를 요청 하 고 수신 하는 사용자는 액세스 패키지의 모든 리소스 역할을 받게 됩니다.

1. 추가 하려는 리소스 종류 (**그룹 및 팀**, **응용 프로그램**또는 **SharePoint 사이트**)를 클릭 합니다.

1. 표시 되는 선택 창의 목록에서 하나 이상의 리소스를 선택 합니다.

    ![액세스 패키지-리소스 역할](./media/entitlement-management-access-package-create/resource-roles.png)

    일반 카탈로그 또는 새 카탈로그에서 액세스 패키지를 만드는 경우 소유 하 고 있는 디렉터리에서 리소스를 선택할 수 있습니다. 적어도 전역 관리자, 사용자 관리자 또는 카탈로그 작성자 여야 합니다.

    기존 카탈로그에서 액세스 패키지를 만드는 경우 해당 리소스를 소유 하지 않고 카탈로그에 이미 있는 리소스를 선택할 수 있습니다.

    전역 관리자, 사용자 관리자 또는 카탈로그 소유자 인 경우에는 카탈로그에 아직 없는 리소스를 선택할 수 있는 추가 옵션이 있습니다. 선택한 카탈로그에서 현재 사용 되지 않는 리소스를 선택 하는 경우에는 다른 카탈로그 관리자가를 사용 하 여 액세스 패키지를 빌드하기 위한 카탈로그에도 이러한 리소스가 추가 됩니다. 현재 선택한 카탈로그에 있는 리소스만 선택 하려면 선택 창의 맨 위에 있는 확인란만 **표시** 확인란을 선택 합니다.

1. 리소스를 선택한 후에는 **역할** 목록에서 리소스에 대해 사용자에 게 할당할 역할을 선택 합니다.

    ![액세스 패키지-리소스 역할 선택](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. **다음**을 클릭합니다.

## <a name="requests"></a>요청

**요청** 탭에서 첫 번째 정책을 만들어 액세스 패키지 및 승인 설정을 요청할 수 있는 사용자를 지정 합니다. 나중에 추가 사용자 그룹이 자신의 승인 설정을 사용 하 여 액세스 패키지를 요청할 수 있도록 더 많은 요청 정책을 만들 수 있습니다.

![액세스 패키지-요청 탭](./media/entitlement-management-access-package-create/requests.png)

이 액세스 패키지를 요청할 수 있는 사용자에 따라 다음 섹션 중 하나에 나와 있는 단계를 수행 합니다.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>검토 + 만들기

**검토 + 만들기** 탭에서 설정을 검토 하 고 유효성 검사 오류를 확인할 수 있습니다.

1. 액세스 패키지의 설정 검토

    ![액세스 패키지-정책-정책 설정 사용](./media/entitlement-management-access-package-create/review-create.png)

1. **만들기** 를 클릭 하 여 액세스 패키지를 만듭니다.

    새 액세스 패키지는 액세스 패키지 목록에 표시 됩니다.

## <a name="creating-an-access-package-programmatically"></a>프로그래밍 방식으로 액세스 패키지 만들기

Microsoft Graph를 사용 하 여 액세스 패키지를 만들 수도 있습니다.  위임 된 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할의 사용자는 `EntitlementManagement.ReadWrite.All` API를 호출할 수 있습니다.

1. [카탈로그에 accessPackageResources를 나열](/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) 하 고 아직 카탈로그에 없는 리소스에 대 한 [accessPackageResourceRequest를 만듭니다](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) .
1. AccessPackageCatalog에 있는 각 accessPackageResource의 [accessPackageResourceRoles를 나열](/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) 합니다. 그런 다음 accessPackageResourceRoleScope를 만들 때이 역할 목록을 사용 하 여 역할을 선택 합니다.
1. [AccessPackage를 만듭니다](/graph/api/accesspackage-post?view=graph-rest-beta).
1. [AccessPackageAssignmentPolicy를 만듭니다](/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. 액세스 패키지에 필요한 각 리소스 역할에 대해 [accessPackageResourceRoleScope를 만듭니다](/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) .

## <a name="next-steps"></a>다음 단계

- [액세스 패키지를 요청 하는 공유 링크](entitlement-management-access-package-settings.md)
- [액세스 패키지에 대 한 리소스 역할 변경](entitlement-management-access-package-resources.md)
- [사용자를 액세스 패키지에 직접 할당](entitlement-management-access-package-assignments.md)