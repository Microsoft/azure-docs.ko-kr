---
title: Azure AD 자격 관리에서 액세스 패키지에 대 한 리소스 역할 변경-Azure Active Directory
description: Azure Active Directory 자격 관리에서 기존 액세스 패키지에 대 한 리소스 역할을 변경 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 56948b700f816c13d35915400658136ffcf48846
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783589"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 액세스 패키지에 대 한 리소스 역할 변경

액세스 패키지 관리자는 새 리소스에 대 한 사용자의 액세스를 프로 비전 하거나 이전 리소스에서 액세스 권한을 제거 하는 것에 대해 걱정 하지 않고 언제 든 지 액세스 패키지의 리소스를 변경할 수 있습니다. 이 문서에서는 기존 액세스 패키지에 대 한 리소스 역할을 변경 하는 방법을 설명 합니다.

이 비디오는 액세스 패키지를 변경 하는 방법에 대 한 개요를 제공 합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>리소스에 대 한 카탈로그 확인

액세스 패키지에 리소스를 추가 해야 하는 경우 카탈로그에서 필요한 리소스를 사용할 수 있는지 확인 해야 합니다. 액세스 패키지 관리자 인 경우에는 사용자가 소유한 경우에도 카탈로그에 리소스를 추가할 수 없습니다. 카탈로그에서 사용 가능한 리소스를 사용 하는 것으로 제한 됩니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **카탈로그** 를 클릭 한 다음 카탈로그를 엽니다.

1. 왼쪽 메뉴에서 **리소스** 를 클릭 하 여이 카탈로그의 리소스 목록을 표시 합니다.

    ![카탈로그의 리소스 목록](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. 액세스 패키지 관리자 인 경우 카탈로그에 리소스를 추가 해야 하는 경우 카탈로그 소유자에 게 추가 하도록 요청할 수 있습니다.

## <a name="add-resource-roles"></a>리소스 역할 추가

리소스 역할은 리소스와 연결 된 사용 권한 컬렉션입니다. 사용자가 리소스를 요청할 수 있도록 하는 방법은 액세스 패키지에 리소스 역할을 추가 하는 것입니다. 그룹, 팀, 응용 프로그램 및 SharePoint 사이트에 대 한 리소스 역할을 추가할 수 있습니다.

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할**을 클릭 합니다.

1. **리소스 역할 추가** 를 클릭 하 여 패키지에 액세스할 수 있도록 리소스 역할 추가 페이지를 엽니다.

    ![액세스 패키지-리소스 역할 추가](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. 그룹, 팀, 응용 프로그램 또는 SharePoint 사이트를 추가할지 여부에 따라 다음 리소스 역할 섹션 중 하나의 단계를 수행 합니다.

## <a name="add-a-group-or-team-resource-role"></a>그룹 또는 팀 리소스 역할 추가

권한 관리를 통해 액세스 패키지가 할당 된 경우 Microsoft 팀의 그룹 또는 팀에 사용자를 자동으로 추가할 수 있습니다. 

- 그룹이 나 팀이 액세스 패키지의 일부이 고 해당 액세스 패키지에 사용자가 할당 된 경우 해당 사용자는 아직 없는 경우 해당 그룹 또는 팀에 추가 됩니다.
- 사용자의 액세스 패키지 할당은 만료 되는 경우 해당 그룹 또는 팀이 포함 된 다른 액세스 패키지에 대 한 할당을 현재 보유 하 고 있지 않은 한 그룹 또는 팀에서 제거 됩니다.

[AZURE AD 보안 그룹 또는 Office 365 그룹](../fundamentals/active-directory-groups-create-azure-portal.md)을 선택할 수 있습니다. 관리자는 카탈로그에 모든 그룹을 추가할 수 있습니다. 카탈로그 소유자는 그룹의 소유자 인 경우 카탈로그에 모든 그룹을 추가할 수 있습니다. 그룹을 선택할 때 다음 Azure AD 제약 조건을 염두에 두어야 합니다.

- 게스트를 비롯 한 사용자가 그룹 또는 팀에 멤버로 추가 되는 경우 해당 그룹 또는 팀의 다른 모든 멤버를 볼 수 있습니다.
- Azure AD는 Azure AD Connect를 사용 하 여 Windows Server Active Directory에서 동기화 되었거나 메일 그룹으로 Exchange Online에서 만든 그룹의 구성원을 변경할 수 없습니다.  
- 동적 그룹의 멤버 자격은 멤버를 추가 하거나 제거 하 여 업데이트할 수 없으므로 동적 그룹 멤버 자격은 자격 관리와 함께 사용 하기에 적합 하지 않습니다.

자세한 내용은 그룹 및 [Office 365 그룹과 Microsoft 팀](/microsoftteams/office-365-groups) [비교](/office365/admin/create-groups/compare-groups) 를 참조 하세요.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **그룹 및 팀** 을 클릭 하 여 그룹 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 그룹 및 팀을 선택 합니다.

    ![액세스 패키지-리소스 역할 추가-그룹 선택](./media/entitlement-management-access-package-resources/group-select.png)

1. **선택**을 클릭합니다.

    그룹이 나 팀을 선택 하면 **하위 유형** 열에 다음 하위 유형 중 하나가 나열 됩니다.

    | 하위 유형 | 설명 |
    | --- | --- |
    | 보안 | 리소스에 대 한 액세스 권한을 부여 하는 데 사용 됩니다. |
    | 배포 | 사용자 그룹에 알림을 보내는 데 사용 됩니다. |
    | O365 | 팀에서 사용 하지 않는 Office 365 그룹 회사 내부와 외부의 사용자 간 공동 작업에 사용 됩니다. |
    | 팀 | 팀에서 사용 하도록 설정 된 Office 365 그룹 회사 내부와 외부의 사용자 간 공동 작업에 사용 됩니다. |

1. **역할** 목록에서 **소유자** 또는 **멤버**를 선택 합니다.

    일반적으로 멤버 역할을 선택 합니다. 소유자 역할을 선택 하면 사용자가 다른 멤버나 소유자를 추가 하거나 제거할 수 있습니다.

    ![액세스 패키지-그룹 또는 팀에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-resources/group-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대 한 기존 할당을 사용 하는 모든 사용자는이 그룹 또는 팀이 추가 될 때 자동으로 구성원이 됩니다.

## <a name="add-an-application-resource-role"></a>응용 프로그램 리소스 역할 추가

사용자에 게 액세스 패키지가 할당 된 경우 azure ad가 azure ad에 페더레이션된 SaaS 응용 프로그램 및 조직의 응용 프로그램을 포함 하 여 Azure AD 엔터프라이즈 응용 프로그램에 대 한 액세스 권한을 자동으로 할당 하도록 할 수 있습니다. 페더레이션된 Single Sign-On를 통해 Azure AD와 통합 되는 응용 프로그램의 경우 Azure AD는 응용 프로그램에 할당 된 사용자에 대 한 페더레이션 토큰을 발급 합니다.

응용 프로그램에는 여러 역할이 있을 수 있습니다. 응용 프로그램을 액세스 패키지에 추가 하는 경우 해당 응용 프로그램에 둘 이상의 역할이 있는 경우 해당 사용자에 대 한 적절 한 역할을 지정 해야 합니다. 응용 프로그램을 개발 하는 경우 [방법: 엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)에서 응용 프로그램에 해당 역할을 추가 하는 방법에 대해 자세히 알아볼 수 있습니다.

응용 프로그램 역할이 액세스 패키지의 일부인 경우:

- 사용자에 게 해당 액세스 패키지를 할당 하면 해당 사용자는 아직 없는 경우 해당 응용 프로그램 역할에 추가 됩니다.
- 사용자의 액세스 패키지 할당이 만료 되 면 해당 응용 프로그램 역할을 포함 하는 다른 액세스 패키지에 할당 된 경우를 제외 하 고 응용 프로그램에서 해당 액세스 권한이 제거 됩니다.

응용 프로그램을 선택할 때 고려해 야 할 사항은 다음과 같습니다.

- 응용 프로그램에도 해당 역할에 할당 된 그룹이 있을 수 있습니다.  액세스 패키지의 응용 프로그램 역할 대신 그룹을 추가 하도록 선택할 수 있지만 응용 프로그램은 사용자에 게 내 액세스 포털에서 액세스 패키지의 일부로 표시 되지 않습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **응용** 프로그램을 클릭 하 여 응용 프로그램 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 응용 프로그램을 선택 합니다.

    ![액세스 패키지-리소스 역할 추가-응용 프로그램 선택](./media/entitlement-management-access-package-resources/application-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 응용 프로그램 역할을 선택 합니다.

    ![액세스 패키지-응용 프로그램에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-resources/application-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대 한 기존 할당을 사용 하는 모든 사용자에 게는 추가 될 때이 응용 프로그램에 대 한 액세스 권한이 자동으로 부여 됩니다.

## <a name="add-a-sharepoint-site-resource-role"></a>SharePoint 사이트 리소스 역할 추가

Azure AD는 액세스 패키지에 할당 된 경우 사용자에 게 SharePoint Online 사이트 또는 SharePoint Online 사이트 모음에 대 한 액세스 권한을 자동으로 할당할 수 있습니다.

1. **액세스 패키지에 리소스 역할 추가** 페이지에서 **sharepoint 사이트** 를 클릭 하 여 sharepoint Online 사이트 선택 창을 엽니다.

1. 액세스 패키지에 포함 하려는 SharePoint Online 사이트를 선택 합니다.

    ![액세스 패키지-리소스 역할 추가-SharePoint Online 사이트 선택](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. **선택**을 클릭합니다.

1. **역할** 목록에서 SharePoint Online 사이트 역할을 선택 합니다.

    ![액세스 패키지-SharePoint Online 사이트에 대 한 리소스 역할 추가](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. **추가**를 클릭합니다.

    액세스 패키지에 대 한 기존 할당을 사용 하는 모든 사용자에 게는이 SharePoint Online 사이트를 추가할 때 해당 사이트에 대 한 액세스 권한이 자동으로 부여 됩니다.

## <a name="remove-resource-roles"></a>리소스 역할 제거

**필수 역할:** 글로벌 관리자, 사용자 관리자, 카탈로그 소유자 또는 액세스 패키지 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭 한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **리소스 역할**을 클릭 합니다.

1. 리소스 역할 목록에서 제거 하려는 리소스 역할을 찾습니다.

1. 줄임표 (**...**)를 클릭 한 다음 **리소스 역할 제거**를 클릭 합니다.

    액세스 패키지에 대 한 기존 할당을 가진 사용자는 제거 될 때이 리소스 역할에 대 한 액세스 권한이 자동으로 취소 됩니다.

## <a name="when-changes-are-applied"></a>변경 내용이 적용 되는 경우

자격 관리에서 Azure AD는 매일 여러 번 액세스 패키지의 할당 및 리소스에 대 한 대량 변경을 처리 합니다. 따라서 할당을 수행 하거나 액세스 패키지의 리소스 역할을 변경 하는 경우 Azure AD에서 변경 내용을 적용 하는 데 최대 24 시간이 걸릴 수 있으며, 이러한 변경 내용을 다른 Microsoft Online Services 또는 연결 된 SaaS 응용 프로그램에 전파 하는 데 걸리는 시간이 길어집니다. 변경으로 인해 몇 가지 개체만 영향을 받는 경우 Azure AD에서 변경 내용을 적용 하는 데 몇 분 정도 걸릴 수 있습니다. 그 후에는 다른 Azure AD 구성 요소에서 해당 변경 내용을 검색 하 고 SaaS 응용 프로그램을 업데이트 합니다. 변경이 수천 개의 개체에 영향을 주는 경우에는 변경 내용이 더 오래 걸립니다. 예를 들어 응용 프로그램 2 개 및 100 사용자 할당을 사용 하는 액세스 패키지가 있고 액세스 패키지에 SharePoint 사이트 역할을 추가 하기로 결정 한 경우 모든 사용자가 해당 SharePoint 사이트 역할에 포함 될 때까지 지연이 있을 수 있습니다. Azure AD 감사 로그, Azure AD 프로 비전 로그 및 SharePoint 사이트 감사 로그를 통해 진행률을 모니터링할 수 있습니다.

팀 구성원을 제거하면 Office 365 그룹에서도 제거됩니다. 팀 채팅 기능을 제거하면 작업이 지연될 수 있습니다. 자세한 내용은 [그룹 멤버 자격](/microsoftteams/office-365-groups#group-membership)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
- [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에서 발행된 역할 클레임 구성](../develop/active-directory-enterprise-app-role-management.md)
- [SharePoint Online 소개](/sharepoint/introduction)