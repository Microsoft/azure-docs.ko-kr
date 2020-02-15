---
title: 자격 관리에서 리소스 만들기 & 관리-Azure AD
description: Azure Active Directory 자격 관리에서 리소스의 새 컨테이너를 만들고 패키지에 액세스 하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5895ed256fa5f0337b74d9dbe14c4074dad4b522
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120247"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 리소스 카탈로그 만들기 및 관리

## <a name="create-a-catalog"></a>카탈로그 만들기

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 리소스를 그룹화 하 고 패키지에 액세스 하려는 경우 카탈로그를 만듭니다. 카탈로그를 만드는 사람은 첫 번째 카탈로그 소유자가 됩니다. 카탈로그 소유자는 카탈로그 소유자를 더 추가할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 작성자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그**를 클릭 합니다.

    ![Azure Portal의 권한 관리 카탈로그](./media/entitlement-management-catalog-create/catalogs.png)

1. **새 카탈로그**를 클릭 합니다.

1. 카탈로그에 대 한 고유한 이름을 입력 하 고 설명을 입력 합니다.

    사용자는 액세스 패키지의 세부 정보에서이 정보를 볼 수 있습니다.

1. 사용자가이 카탈로그의 액세스 패키지를 만드는 즉시 요청 하 게 하려면 **사용** 을 **예**로 설정 합니다.

1. 선택한 외부 디렉터리의 사용자가이 카탈로그에서 액세스 패키지를 요청할 수 있도록 하려면 **외부 사용자에 대해 사용** 을 **예**로 설정 합니다.

    ![새 카탈로그 창](./media/entitlement-management-shared/new-catalog.png)

1. **만들기** 를 클릭 하 여 카탈로그를 만듭니다.

### <a name="creating-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그 만들기

Microsoft Graph를 사용 하 여 카탈로그를 만들 수도 있습니다.  위임 된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할의 사용자는 API를 호출 하 여 [accessPackageCatalog를 만들](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)수 있습니다.

## <a name="add-resources-to-a-catalog"></a>카탈로그에 리소스 추가

액세스 패키지에 리소스를 포함 하려면 리소스가 카탈로그에 있어야 합니다. 추가할 수 있는 리소스 유형은 그룹, 응용 프로그램 및 SharePoint Online 사이트입니다. 그룹은 클라우드 생성 Office 365 그룹 또는 클라우드 생성 Azure AD 보안 그룹이 될 수 있습니다. 응용 프로그램은 SaaS 응용 프로그램 및 Azure AD에 페더레이션된 응용 프로그램을 포함 하 여 Azure AD 엔터프라이즈 응용 프로그램 일 수 있습니다. 사이트는 SharePoint Online 사이트 또는 SharePoint Online 사이트 컬렉션 일 수 있습니다.

**필수 역할:** [카탈로그에 리소스를 추가 하려면 필수 역할을](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) 참조 하세요.

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 리소스를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스**를 클릭 합니다.

1. **리소스 추가**를 클릭 합니다.

1. 리소스 종류를 클릭 합니다 ( **그룹, 팀**, **응용 프로그램**또는 **SharePoint 사이트**).

    추가 하려는 리소스가 표시 되지 않거나 리소스를 추가할 수 없는 경우 필요한 Azure AD 디렉터리 역할 및 자격 관리 역할이 있는지 확인 합니다. 필요한 역할이 있는 사용자에 게 카탈로그에 리소스를 추가 해야 할 수도 있습니다. 자세한 내용은 [카탈로그에 리소스를 추가 하는 데 필요한 역할](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)을 참조 하세요.

1. 카탈로그에 추가할 유형의 리소스를 하나 이상 선택 합니다.

    ![카탈로그에 리소스 추가](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 완료 되 면 **추가**를 클릭 합니다.

    이러한 리소스는 이제 카탈로그 내에서 액세스 패키지에 포함 될 수 있습니다.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그에 리소스 추가

Microsoft Graph를 사용 하 여 카탈로그에 리소스를 추가할 수도 있습니다.  위임 된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할 또는 카탈로그 및 리소스 소유자의 사용자는 API를 호출 하 여 [accessPackageResourceRequest를 만들](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)수 있습니다.

## <a name="remove-resources-from-a-catalog"></a>카탈로그에서 리소스 제거

카탈로그에서 리소스를 제거할 수 있습니다. 카탈로그가 카탈로그의 액세스 패키지에서 사용 되지 않는 경우에만 카탈로그에서 리소스를 제거할 수 있습니다.

**필수 역할:** [카탈로그에 리소스를 추가 하려면 필수 역할을](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog) 참조 하세요.

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 리소스를 제거 하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스**를 클릭 합니다.

1. 제거 하려는 리소스를 선택 합니다.

1. **제거** 를 클릭 하거나 줄임표 ( **...** )를 클릭 한 다음 **리소스 제거**를 클릭 합니다.

## <a name="add-additional-catalog-owners"></a>카탈로그 소유자 추가

카탈로그를 만든 사용자가 첫 번째 카탈로그 소유자가 됩니다. 카탈로그 관리를 위임 하려면 사용자를 카탈로그 소유자 역할에 추가 합니다. 이렇게 하면 카탈로그 관리 책임을 공유 하는 데 도움이 됩니다. 

사용자를 카탈로그 소유자 역할에 할당 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 관리자를 추가 하려는 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **역할 및 관리자**를 클릭 합니다.

    ![역할 및 관리자 카탈로그](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **소유자 추가** 를 클릭 하 여 이러한 역할에 대 한 멤버를 선택 합니다.

1. **선택** 을 클릭 하 여 이러한 멤버를 추가 합니다.

## <a name="edit-a-catalog"></a>카탈로그 편집

카탈로그에 대 한 이름 및 설명을 편집할 수 있습니다. 사용자는 액세스 패키지의 세부 정보에서이 정보를 볼 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 편집 하려는 카탈로그를 엽니다.

1. 카탈로그의 **개요** 페이지에서 **편집**을 클릭 합니다.

1. 카탈로그의 이름, 설명 또는 사용 설정을 편집 합니다.

    ![카탈로그 설정 편집](./media/entitlement-management-shared/catalog-edit.png)

1. **저장**을 클릭합니다.

## <a name="delete-a-catalog"></a>카탈로그 삭제

액세스 패키지가 없는 경우에만 카탈로그를 삭제할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 카탈로그 소유자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 하 고 삭제 하려는 카탈로그를 엽니다.

1. 카탈로그의 **개요**에서 **삭제**를 클릭 합니다.

1. 표시되는 메시지 상자에서 **예**를 클릭합니다.

### <a name="deleting-a-catalog-programmatically"></a>프로그래밍 방식으로 카탈로그 삭제

Microsoft Graph를 사용 하 여 카탈로그를 삭제할 수도 있습니다.  위임 된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할의 사용자는 API를 호출 하 여 [accessPackageCatalog를 삭제할](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)수 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 관리를 위임 하 여 패키지 관리자 액세스](entitlement-management-delegate-managers.md)
