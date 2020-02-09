---
title: Azure AD 자격 관리에서 연결 된 조직 추가-Azure Active Directory
description: 조직 외부 사용자가 프로젝트에 대 한 공동 작업을 수행할 수 있도록 액세스 패키지를 요청 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 01/22/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c1b6f5ebffa39d3b735e85df794e37329e3aa2e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548903"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 연결 된 조직 추가

Azure AD 자격 관리를 통해 조직 외부 사용자와 공동 작업을 수행할 수 있습니다. 외부 Azure AD 디렉터리 또는 도메인의 사용자와 자주 공동 작업 하는 경우 연결 된 조직으로 추가할 수 있습니다. 이 문서에서는 조직 외부 사용자가 디렉터리에서 리소스를 요청할 수 있도록 연결 된 조직을 추가 하는 방법을 설명 합니다.

## <a name="what-is-a-connected-organization"></a>연결 된 조직 이란?

연결 된 조직은 외부 Azure AD 디렉터리 또는 관계가 있는 도메인입니다.

예를 들어, Woodgrove Bank에서 작업 하 고 두 개의 외부 조직과 공동 작업 하려는 경우를 가정해 보겠습니다. 이러한 두 조직의 구성은 다음과 같이 다릅니다.

- 그래픽 디자인 협회는 Azure AD를 사용 하 고 해당 사용자에 게는 `graphicdesigninstitute.com`로 끝나는 사용자 계정 이름이 있습니다.
- Contoso는 아직 Azure AD를 사용 하지 않습니다. Contoso 사용자는 `contoso.com`로 끝나는 사용자 계정 이름을 갖습니다.

이 경우 연결 된 두 조직을 구성할 수 있습니다. 그래픽 디자인 협회와 Contoso 용으로 하나의 연결 된 조직을 만들 수 있습니다. 그런 다음 두 개의 연결 된 조직을 정책에 추가 하면 정책과 일치 하는 사용자 보안 주체 이름을 가진 각 조직의 사용자가 액세스 패키지를 요청할 수 있습니다. Graphicdesigninstitute.com 도메인을 포함 하는 사용자 계정 이름을 가진 사용자는 그래픽 디자인 협회 연결 된 조직과 일치 하 고 요청을 제출할 수 있으며, 사용자 계정 이름이 contoso.com 인 사용자는 일치 합니다. Contoso 연결 된 조직에도 패키지를 요청할 수 있습니다. 또한 그래픽 설계 협회에서는 Azure AD를 사용 하기 때문에 graphicdesigninstitute 등의 테넌트에 추가 되는 [확인 된 도메인과](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) 일치 하는 보안 주체 이름을 가진 사용자도 동일한 정책을 사용 하 여 액세스 패키지를 요청할 수 있습니다.

![연결 된 조직 예](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD 디렉터리 또는 도메인의 사용자가 인증 하는 방법은 인증 유형에 따라 달라 집니다. 연결 된 조직의 인증 유형은 다음과 같습니다.

- Azure AD
- [직접 페더레이션](../b2b/direct-federation.md)
- [일회용 암호](../b2b/one-time-passcode.md) (도메인)

연결 된 조직을 추가 하는 방법에 대 한 데모를 보려면 다음 비디오를 시청 하세요.

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>연결된 조직 추가

외부 Azure AD 디렉터리 또는 도메인을 연결 된 조직으로 추가 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 게스트 초대자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **연결 된** 조직을 클릭 한 다음 **연결 된 조직 추가**를 클릭 합니다.

    ![Id 거 버 넌 스-연결 된 조직-연결 된 조직 추가](./media/entitlement-management-organization/connected-organization.png)

1. **기본 사항** 탭에서 조직의 표시 이름 및 설명을 입력 합니다.

    ![연결 된 조직 추가-기본 탭](./media/entitlement-management-organization/organization-basics.png)

1. **디렉터리 + 도메인** 탭에서 **디렉터리 추가 + 도메인** 을 클릭 하 여 디렉터리 + 도메인 선택 창을 엽니다.

1. Azure AD 디렉터리 또는 도메인을 검색할 도메인 이름을 입력 합니다. 전체 도메인 이름을 입력 해야 합니다.

1. 제공 된 이름 및 인증 유형에 따라 올바른 조직 인지 확인 합니다. 사용자가 로그인 하는 방법은 인증 유형에 따라 달라 집니다.

    ![연결 된 조직 추가-디렉터리 및 도메인 선택](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. **추가** 를 클릭 하 여 Azure AD 디렉터리 또는 도메인을 추가 합니다. 현재는 연결 된 조직 당 하나의 Azure AD 디렉터리 또는 도메인만 추가할 수 있습니다.

    > [!NOTE]
    > Azure AD 디렉터리 또는 도메인의 모든 사용자가이 액세스 패키지를 요청할 수 있습니다. 이러한 도메인은 Azure B2B 허용 또는 거부 목록에 의해 차단 되지 않는 한, 디렉터리와 연결 된 모든 하위 도메인에서 Azure AD의 사용자를 포함 합니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](../b2b/allow-deny-list.md)을 참조하세요.

1. Azure AD 디렉터리 또는 도메인을 추가 했으면 **선택**을 클릭 합니다.

    조직이 목록에 표시 됩니다.

    ![연결 된 조직 추가-디렉터리 탭](./media/entitlement-management-organization/organization-directory-domain.png)

1. **스폰서** 탭에서이 연결 된 조직의 선택 스폰서를 추가 합니다.

    스폰서는이 연결 된 조직과의 관계에 대 한 연락처 지점인 디렉터리에 이미 있는 내부 또는 외부 사용자입니다. 내부 스폰서는 디렉터리의 멤버 사용자입니다. 외부 스폰서는 이전에 초대 되었으며 이미 디렉터리에 있는 연결 된 조직의 게스트 사용자입니다. 이 연결 된 조직의 사용자가이 액세스 패키지에 대 한 액세스를 요청 하는 경우 스폰서를 승인자로 사용할 수 있습니다. 디렉터리에 게스트 사용자를 초대 하는 방법에 대 한 자세한 내용은 [Azure Portal에서 B2B 공동 작업 사용자 추가 Azure Active Directory](../b2b/add-users-administrator.md)를 참조 하세요.

    **추가/제거**를 클릭 하면 내부 또는 외부 스폰서를 선택 하는 창이 나타납니다. 이 창에는 디렉터리의 필터링 되지 않은 사용자 및 그룹 목록이 표시 됩니다.

    ![액세스 패키지-정책-연결 된 조직 추가-스폰서 탭](./media/entitlement-management-organization/organization-sponsors.png)

1. **검토 + 만들기** 탭에서 조직 설정을 검토 한 후 **만들기**를 클릭 합니다.

    ![액세스 패키지-정책-연결 된 조직 추가-검토 + 만들기 탭](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>연결 된 조직 삭제

외부 Azure AD 디렉터리 또는 도메인과 더 이상 관계가 없으면 연결 된 조직을 삭제할 수 있습니다.

**필수 역할:** 전역 관리자, 사용자 관리자 또는 게스트 초대자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **연결 된** 조직을 클릭 한 다음 연결 된 조직을 클릭 하 여 엽니다.

1. 개요 페이지에서 **삭제** 를 클릭 하 여 연결 된 조직을 삭제 합니다.

    현재 연결 된 사용자가 없는 경우에만 연결 된 조직을 삭제할 수 있습니다.

    ![Id 거 버 넌 스-연결 된 조직-연결 된 조직 삭제](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>다음 단계

- [외부 사용자에 대 한 액세스 제어](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users)
- [디렉터리에 없는 사용자의 경우](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
