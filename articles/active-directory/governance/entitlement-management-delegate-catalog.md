---
title: Azure AD 자격 관리에서 카탈로그 작성자에 게 액세스 관리 위임-Azure Active Directory
description: 액세스를 관리할 수 있도록 IT 관리자의 액세스 관리를 카탈로그 작성자 및 프로젝트 관리자에 게 위임 하는 방법을 알아봅니다.
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
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e37ad006da5042291614c773f242b5a1f3be97
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120185"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 카탈로그 작성자에 게 액세스 관리 위임

카탈로그는 리소스 및 액세스 패키지의 컨테이너입니다. 관련 리소스를 그룹화 하 고 패키지에 액세스 하려는 경우 카탈로그를 만듭니다. 기본적으로 전역 관리자 또는 사용자 관리자는 [카탈로그를 만들](entitlement-management-catalog-create.md)수 있으며 사용자를 카탈로그 소유자로 추가할 수 있습니다.

자신의 카탈로그를 만들 수 있도록 관리자가 아닌 사용자에 게 위임 하려면 해당 사용자를 Azure AD 자격 관리에 정의 된 카탈로그 작성자 역할에 추가할 수 있습니다. 개별 사용자를 추가 하거나, 해당 멤버가 카탈로그를 만들 수 있는 그룹을 추가할 수 있습니다.  카탈로그를 만든 후에는 카탈로그에 소유한 리소스를 나중에 추가할 수 있습니다.

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 관리자는 카탈로그 작성자에 게 위임

카탈로그 작성자 역할에 사용자를 할당 하려면 다음 단계를 수행 합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴의 **자격 관리** 섹션에서 **설정**을 클릭 합니다.

1. **편집**을 클릭합니다.

    ![카탈로그 작성자를 추가 하기 위한 설정](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. **위임 권한 관리** 섹션에서 **카탈로그 작성자 추가** 를 클릭 하 여이 자격 관리 역할을 위임할 사용자 또는 그룹을 선택 합니다.

1. **선택**을 클릭합니다.

1. **저장**을 클릭합니다.

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>위임 된 역할이 Azure Portal에 액세스할 수 있도록 허용

카탈로그 작성자 및 액세스 패키지 관리자와 같은 위임 된 역할이 액세스 패키지를 관리 하기 위해 Azure Portal에 액세스할 수 있도록 하려면 관리 포털 설정을 확인 해야 합니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭 한 다음 **사용자**를 클릭 합니다.

1. 왼쪽 메뉴에서 **사용자 설정**을 클릭 합니다.

1. **AZURE AD 관리 포털에 대 한 액세스 제한** 이 **아니요**로 설정 되어 있는지 확인 합니다.

    ![Azure AD 사용자 설정-관리 포털](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="next-steps"></a>다음 단계

- [리소스 카탈로그 만들기 및 관리](entitlement-management-catalog-create.md)
- [액세스 관리를 위임 하 여 패키지 관리자 액세스](entitlement-management-delegate-managers.md)

