---
title: 조직의 앱 및 그룹 액세스 관리 - Azure AD
description: 내 앱 포털에서 조직의 앱 및 그룹에 대한 보안 액세스를 관리하기 위해 액세스 검토를 수행하는 방법을 알아봅니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: dbe05f264b0fca6c1a5e8e7d944d94a6bed55392
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798026"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>내 앱 포털에서 액세스 검토를 수행

웹 기반 **내 앱** 포털에서 회사 또는 학교 계정을 사용하여 앱 및 그룹에 대한 액세스 검토를 수행할 수 있습니다. 액세스 검토는 오래된 액세스를 관리하거나 액세스 요구 사항을 변경하여 검토하고 업데이트하는 데 도움이 됩니다.

**My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의하세요.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>이 콘텐츠는 **내 앱** 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](../manage-apps/index.yml)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="manage-access-reviews"></a>액세스 검토 관리

관리자가 사용자에게 자체 액세스 검토를 수행할 수 있는 권한을 부여한 경우 **내 앱** 포털 페이지의 **액세스 검토** 타일에서 그룹 또는 앱 액세스를 관리할 수 있습니다.

>[!Note]
>**액세스 검토** 타일이 표시되지 않으면 액세스 검토를 수행할 수 있는 권한이 없거나 승인을 기다리는 보류 중인 검토가 없음을 의미합니다. 타일에 대한 액세스 권한이 필요한 경우 기술 지원팀에 도움을 요청하세요.

## <a name="to-perform-your-access-reviews"></a>액세스 검토를 수행하려면

1. 회사 또는 학교 계정에 로그인합니다.

2. 웹 브라우저를 열고 https://myapps.microsoft.com 으로 이동하거나 조직에서 제공하는 링크를 사용합니다. 예를 들어 https://myapps.microsoft.com/contoso.com 과 같이 조직의 사용자 지정된 페이지로 이동할 수 있습니다.

    조직에서 소유하고 사용자가 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

    ![내 앱 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. **액세스 검토** 타일을 선택하여 승인을 기다리는 액세스 검토의 목록을 확인합니다.

    ![조직에 대한 액세스 검토가 보류 중인 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. **검토 시작**을 선택하여 액세스 검토를 시작합니다.

5. 액세스를 검토하고 해당 액세스가 여전히 필요한지 여부를 결정합니다.

    ![검토 세부 정보를 표시하는 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >조직의 그룹 및 앱 액세스를 검토하도록 허용된 관리자인 경우 다른 페이지가 표시됩니다. 조직의 그룹 또는 앱을 검토하는 방법에 대한 자세한 내용은 [Azure AD 액세스 검토에서 그룹 또는 애플리케이션에 대한 액세스 검토](../governance/perform-access-review.md)를 참조하세요.

6. 액세스를 유지하려면 **예**를 선택하고 액세스를 제거하려면 **아니요**를 선택합니다.

    **예**를 선택하는 경우 **이유** 상자에 근거를 지정해야 할 수 있습니다.

    ![샘플 텍스트를 사용하여 이유 상자를 표시하는 액세스 검토 페이지](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. **제출**을 선택합니다.

    액세스 검토가 완료되고 **내 앱** 포털로 돌아갑니다.

    >[!Note]
    >액세스 검토 기간이 끝날 때까지는 언제든지 액세스를 변경할 수 있습니다. 앱 또는 그룹에 대한 액세스를 제거하는 경우 액세스가 즉시 제거되지 않습니다. 제거는 액세스 검토 기간이 끝나거나 관리자가 검토를 종료할 때 발생합니다.

## <a name="next-steps"></a>다음 단계

- [내 앱 포털에서 앱 액세스 및 사용](my-apps-portal-end-user-access.md)
- [프로필 정보 변경](my-apps-portal-end-user-update-profile.md)
- [그룹 관련 정보 보기 및 업데이트](my-apps-portal-end-user-groups.md)