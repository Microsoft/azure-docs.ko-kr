---
title: 그룹 명명 정책 빠른 시작 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 새 사용자를 추가하거나 기존 사용자를 삭제하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b54af39401ba8c3fede4c9ad80ec8edc963b1d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213673"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>빠른 시작: Azure Active Directory의 그룹에 대한 명명 정책

이 빠른 시작에서는 사용자가 만든 Microsoft 365 그룹의 Azure AD(Azure Active Directory) 조직에서 명명 정책을 설정하여 조직의 그룹을 정렬하고 검색하는 데 도움을 줍니다. 예를 들어 명명 정책을 사용하여 다음과 같은 일을 할 수 있습니다.

* 그룹의 기능, 멤버 자격, 지리적 영역 또는 그룹을 만든 사람 정보를 전달합니다.
* 주소록의 그룹을 범주화합니다.
* 그룹 이름 및 별칭에 특정 단어를 사용하지 않도록 차단합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="configure-the-group-naming-policy-in-the-azure-portal"></a>Azure Portal에서 그룹 명명 정책 구성

1. 사용자 관리자 계정을 사용하여 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
1. **그룹**을 선택한 다음, **명명 정책**을 선택하여 명명 정책 페이지를 엽니다.

    ![관리 센터에서 명명 정책 페이지 열기](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>접두사-접미사 명명 정책 보기 또는 편집

1. **명명 정책** 페이지에서 **그룹 명명 정책**을 선택합니다.
1. 명명 정책의 일부로 적용하려는 특성 또는 문자열을 선택하여 현재 접두사 또는 접미사 명명 정책을 개별적으로 보거나 편집할 수 있습니다.
1. 목록에서 접두사 또는 접미사를 제거하려면 접두사 또는 접미사를 선택한 다음, **삭제**를 선택합니다. 동시에 여러 항목을 삭제할 수 있습니다.
1. 정책에 대한 변경 사항이 효력을 발휘하도록 **저장**을 선택합니다.

### <a name="view-or-edit-the-custom-blocked-words"></a>사용자 지정 차단 단어 보기 또는 편집

1. **명명 정책** 페이지에서 **차단된 단어**를 선택합니다.

    ![명명 정책에서 차단된 단어 목록 편집 및 업로드](./media/groups-naming-policy/blockedwords.png)

1. **다운로드**를 선택하여 사용자 지정 차단 단어의 현재 목록을 보거나 편집합니다.
1. 파일 아이콘을 선택하여 사용자 지정 차단 단어의 새로운 목록을 업로드합니다.
1. 정책에 대한 변경 사항이 효력을 발휘하도록 **저장**을 선택합니다.

이것으로 끝입니다. 명명 정책을 설정하고 사용자 지정 차단 단어를 추가했습니다.

## <a name="clean-up-resources"></a>리소스 정리

### <a name="remove-the-naming-policy-using-azure-portal"></a>Azure Portal을 사용하여 명명 정책 제거

1. **명명 정책** 페이지에서 **정책 삭제**를 선택합니다.
1. 삭제를 확인한 후 모든 접두사-접미사 명명 정책 및 모든 사용자 지정 차단 단어를 비롯한 명명 정책이 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 통해 Azure AD 조직에 대한 명명 정책을 설정하는 방법을 알아보았습니다.

명명 정책에 대한 PowerShell cmdlet, 기술 제약 조건, 사용자 지정 차단 단어의 목록 추가 및 Office 365 앱의 최종 사용자 환경에 대해 자세히 알아보는 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [명명 정책 PowerShell](groups-naming-policy.md)