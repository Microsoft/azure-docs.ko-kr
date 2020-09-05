---
title: 자격 관리의 위임 및 역할-Azure AD
description: IT 관리자의 액세스 관리 권한을 부서 관리자와 프로젝트 관리자에 게 위임 하 여 액세스를 관리 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: a01f945496d2f0bc81a108c5e58c89587c1c4e38
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505481"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리의 위임 및 역할

기본적으로 전역 관리자 및 사용자 관리자는 Azure AD 자격 관리의 모든 측면을 만들고 관리할 수 있습니다. 그러나 이러한 역할의 사용자는 액세스 패키지가 필요한 모든 상황을 모를 수 있습니다. 일반적으로 해당 부서, 팀 또는 프로젝트 내에서 사용자가 공동 작업 하 고 있는 리소스를 사용 하 여 리소스와 기간을 사용 하는 사용자입니다. 관리자가 아닌 사용자에 게 무제한 권한을 부여 하는 대신 사용자에 게 작업을 수행 하는 데 필요한 최소 권한을 부여 하 고 충돌 하거나 부적절 한 액세스 권한을 만들지 않도록 할 수 있습니다.

이 비디오는 IT 관리자가 관리자가 아닌 사용자에 게 액세스 관리를 위임 하는 방법에 대 한 개요를 제공 합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Delegate 예제

권한 관리에서 액세스 관리를 위임 하는 방법을 이해 하기 위해 예제를 사용 하는 것이 좋습니다. 조직에 다음과 같은 관리자 및 관리자가 있다고 가정 합니다.

![IT 관리자에서 관리자로 위임](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

IT 관리자의 경우 Hana에는 각 부서 (마케팅의 Mamta, 재무에 표시 및 학과의 리소스 및 업무상 중요 한 콘텐츠를 담당 하는 법정)의 연락처가 있습니다.

권한 관리를 사용 하면 액세스 권한이 필요한 사용자, 기간 및 리소스를 파악 하는 관리자가 아닌 관리자에 게 액세스 관리를 위임할 수 있습니다. 이렇게 하면 적절 한 사용자가 해당 부서에 대 한 액세스를 관리 하 게 됩니다.

Hana가 마케팅, 재무 및 법률 부서에 액세스 관리를 위임할 수 있는 한 가지 방법은 다음과 같습니다.

1. Hana는 새 Azure AD 보안 그룹을 만들고 그룹의 구성원으로 Mamta, Mark 및 Joe를 추가 합니다.

1. Hana는 해당 그룹을 카탈로그 작성자 역할에 추가 합니다.

    이제 Mamta, Mark 및 Joe가 해당 부서의 카탈로그를 만들고, 해당 부서에 필요한 리소스를 추가 하 고, 카탈로그 내에서 추가 위임 작업을 수행할 수 있습니다.

    Mamta, Mark 및 Joe는 서로의 카탈로그를 볼 수 없습니다.

1. Mamta는 리소스의 컨테이너인 **마케팅** 카탈로그를 만듭니다.

1. Mamta는 마케팅 부서가 소유 하 고 있는 리소스를이 카탈로그에 추가 합니다.

1. Mamta은 해당 부서의 사용자를이 카탈로그의 카탈로그 소유자로 추가할 수 있습니다. 이렇게 하면 카탈로그 관리 책임을 공유 하는 데 도움이 됩니다.

1. Mamta는 마케팅 부서의 프로젝트 관리자에 게 마케팅 카탈로그의 액세스 패키지 만들기 및 관리를 추가로 위임할 수 있습니다. 액세스 패키지 관리자 역할에 할당 하 여이 작업을 수행할 수 있습니다. 액세스 패키지 관리자는 액세스 패키지를 만들고 관리할 수 있습니다. 

다음 다이어그램은 마케팅, 재무 및 법률 부서를 위한 리소스가 포함 된 카탈로그를 보여 줍니다. 프로젝트 관리자는 이러한 카탈로그를 사용 하 여 팀 또는 프로젝트에 대 한 액세스 패키지를 만들 수 있습니다.

![자격 관리 위임 예제](./media/entitlement-management-delegate/elm-delegate.png)

위임 후 마케팅 부서에는 다음 표와 비슷한 역할이 있을 수 있습니다.

| 사용자 | 작업 역할 | Azure AD 역할 | 자격 관리 역할 |
| --- | --- | --- | --- |
| Hana | IT 관리자 | 전역 관리자 또는 사용자 관리자 |  |
| Mamta | 마케팅 관리자 | 사용자 | 카탈로그 작성자 및 카탈로그 소유자 |
| Bob | 마케팅 리드 | 사용자 | 카탈로그 소유자 |
| Jessica | 마케팅 프로젝트 관리자 | 사용자 | 액세스 패키지 관리자 |

## <a name="entitlement-management-roles"></a>권한 관리 역할

자격 관리에는 자격 관리와 관련 된 다음과 같은 역할이 있습니다.

| 자격 관리 역할 | 설명 |
| --- | --- |
| 카탈로그 작성자 | 카탈로그를 만들고 관리 합니다. 일반적으로 전역 관리자가 아닌 IT 관리자 또는 리소스 컬렉션에 대 한 리소스 소유자입니다. 카탈로그를 만드는 사람은 자동으로 카탈로그의 첫 번째 카탈로그 소유자가 되며 카탈로그 소유자를 더 추가할 수 있습니다. 카탈로그 작성자는 소유 하지 않은 카탈로그를 관리 하거나 볼 수 없으며 소유 하지 않은 리소스를 카탈로그에 추가할 수 없습니다. 카탈로그 작성자가 다른 카탈로그를 관리 하거나 소유 하지 않은 리소스를 추가 해야 하는 경우 해당 카탈로그 또는 리소스의 공동 소유자가 되도록 요청할 수 있습니다. |
| 카탈로그 소유자 | 기존 카탈로그를 편집 하 고 관리 합니다. 일반적으로 IT 관리자 또는 리소스 소유자 이거나 카탈로그 소유자가 지정 하는 사용자입니다. |
| 액세스 패키지 관리자 | 카탈로그 내 모든 기존 액세스 패키지를 편집 하 고 관리 합니다. |

또한 액세스 패키지의 지정 된 승인자와 요청자는 역할이 아니라 권한도 갖습니다.

| 오른쪽 | 설명 |
| --- | --- |
| 승인자 | 패키지에 대 한 액세스 요청을 승인 하거나 거부 하기 위해 정책에서 권한을 부여 합니다. 단, 액세스 패키지 정의는 변경할 수 없습니다. |
| Requestor | 액세스 패키지를 요청 하는 액세스 패키지의 정책에 의해 권한이 부여 됩니다. |

다음 표에는 자격 관리 역할이 수행할 수 있는 작업이 나열 되어 있습니다.

| Task | Admin | 카탈로그 작성자 | 카탈로그 소유자 | 액세스 패키지 관리자 |
| --- | :---: | :---: | :---: | :---: |
| [카탈로그 작성자에 게 위임](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [연결된 조직 추가](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [새 카탈로그 만들기](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그 소유자 추가](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그 편집](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그 삭제](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [액세스 패키지 관리자에 위임](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [액세스 패키지 관리자 제거](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [카탈로그에 새 액세스 패키지 만들기](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [액세스 패키지에서 리소스 역할 변경](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [정책 만들기 및 편집](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [사용자를 액세스 패키지에 직접 할당](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지에 할당 된 사용자 보기](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지의 요청 보기](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [요청의 배달 오류 보기](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [요청 다시 처리](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [보류 중인 요청 취소](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지 숨기기](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [액세스 패키지 삭제](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>카탈로그에 리소스를 추가 하는 데 필요한 역할

전역 관리자는 카탈로그에서 그룹 (클라우드로 생성 된 보안 그룹 또는 클라우드 생성 Office 365 그룹), 응용 프로그램 또는 SharePoint Online 사이트를 추가 하거나 제거할 수 있습니다. 사용자 관리자는 디렉터리 역할에 할당할 수 있도록 구성 된 그룹을 제외 하 고 카탈로그에서 그룹 또는 응용 프로그램을 추가 하거나 제거할 수 있습니다.

전역 관리자 또는 사용자 관리자가 아닌 사용자의 경우 카탈로그에 그룹, 응용 프로그램 또는 SharePoint Online 사이트를 추가 하려면 해당 사용자에 게 필요한 Azure AD 디렉터리 역할과 카탈로그 소유자 자격 관리 역할이 *둘 다* 있어야 합니다. 다음 표에는 카탈로그에 리소스를 추가 하는 데 필요한 역할 조합이 나와 있습니다. 카탈로그에서 리소스를 제거 하려면 동일한 역할이 있어야 합니다.

| Azure AD 디렉터리 역할 | 자격 관리 역할 | 보안 그룹을 추가할 수 있음 | Office 365 그룹을 추가할 수 있습니다. | 앱 추가 가능 | SharePoint Online 사이트 추가 가능 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 해당 없음 |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [팀 서비스 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  |  |
| [SharePoint 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| [클라우드 애플리케이션 관리자](../users-groups-roles/directory-assign-admin-roles.md) | 카탈로그 소유자 |  |  | :heavy_check_mark: |  |
| 사용자 | 카탈로그 소유자 | 그룹 소유자 인 경우에만 | 그룹 소유자 인 경우에만 | 앱 소유자 인 경우에만 |  |

> [!NOTE]
> 사용자가 보안 그룹 또는 Office 365 그룹을 추가 하는 경우에는 해당 그룹을 역할에 할당할 수 없습니다. 사용자가 액세스 패키지를 만들 때 역할 할당을 할 수 있는 그룹을 추가 하는 경우 해당 역할 할당 가능 그룹의 소유자 이기도 해야 합니다. 자세한 내용은 [Azure Active Directory에서 역할 할당 가능 그룹 만들기](../users-groups-roles/roles-groups-create-eligible.md)를 참조 하세요.

태스크에 대 한 최소 권한 있는 역할을 확인 하려면 [Azure Active Directory에서 관리자 작업을 통해 관리자 역할](../users-groups-roles/roles-delegate-by-task.md#entitlement-management)을 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [액세스 관리를 카탈로그 작성자에 게 위임](entitlement-management-delegate-catalog.md)
- [리소스 카탈로그 만들기 및 관리](entitlement-management-catalog-create.md)
