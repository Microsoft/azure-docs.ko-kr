---
title: '빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션 속성 구성'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure AD(Azure Active Directory) 테넌트에 등록된 애플리케이션을 구성합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.openlocfilehash: 1873cd14883e8b1a5174b8761e4a0bc9ba1ef872
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641860"
---
# <a name="quickstart-configure-properties-for-an-application-in-your-azure-active-directory-azure-ad-tenant"></a>빠른 시작: Azure AD(Azure Active Directory) 테넌트의 애플리케이션 속성 구성

이전 빠른 시작에서는 Azure AD(Azure Active Directory) 테넌트에 애플리케이션을 추가했습니다. 애플리케이션을 추가하면 Azure AD 테넌트가 앱의 ID 공급자임을 알 수 있습니다. 이제 앱의 몇 가지 속성을 구성하겠습니다.
 
## <a name="prerequisites"></a>필수 구성 요소

Azure AD 테넌트의 애플리케이션 속성을 구성하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.
- 선택 사항: [앱 보기](view-applications-portal.md) 완료.
- 선택 사항: [앱 추가](add-application-portal.md) 완료.

>[!IMPORTANT]
>비 프로덕션 환경을 사용하여 이 빠른 시작의 단계를 테스트합니다.

## <a name="configure-app-properties"></a>앱 속성 구성

Azure AD 테넌트에 애플리케이션 추가를 마치면 개요 페이지가 나타납니다. 이미 추가된 애플리케이션을 구성하는 경우 첫 번째 빠른 시작을 살펴보세요. 테넌트에 추가된 애플리케이션을 보는 과정을 안내합니다. 

애플리케이션 속성을 편집하려면:

1. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택합니다. 그런 다음, 구성하려는 애플리케이션을 찾아 선택합니다.
2. **관리** 섹션에서 **속성**을 선택하여 편집할 **속성** 창을 엽니다.

    ![편집 가능한 앱 속성을 표시하는 속성 화면의 스크린샷.](media/add-application-portal/edit-properties.png)

3. 구성에 사용할 수 있는 옵션을 잠시 살펴봅니다.
    - **사용자가 로그인할 수 있습니까?** 는 애플리케이션에 할당된 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자 할당이 필요하나요?** 는 애플리케이션에 할당되지 않은 사용자가 로그인할 수 있는지 여부를 결정합니다.
    - **사용자에게 표시되나요?** 는 앱에 할당된 사용자가 [내 앱](https://myapps.microsoft.com) 및 Office 365 앱 시작 관리자에서 앱을 볼 수 있는지 여부를 결정합니다. (Office 365 또는 Microsoft 365 웹 사이트의 왼쪽 위 모서리에 있는 와플 메뉴를 참조하세요.)
    
    > [!TIP]
    > 사용자 할당은 탐색의 **사용자 및 그룹** 섹션에서 이루어집니다.

    세 가지 옵션은 서로 독립적으로 전환할 수 있으며 결과 동작은 항상 명확하지는 않습니다. 다음은 도움이 될 수 있는 테이블입니다.
    
    | 사용자가 로그인할 수 있습니까? | 사용자 할당이 필요합니까? | 사용자가 볼 수 있습니까? | 앱에 할당되었거나 할당되지 않은 사용자에 대한 동작입니다. |
    |---|---|---|---|
    | 예 | 예 | 예 | 할당된 사용자는 앱을 보고 로그인할 수 있습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없고 로그인할 수 없습니다. |
    | 예 | 예 | 아니요  | 할당된 사용자는 앱을 볼 수 없지만 로그인할 수 있습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없고 로그인할 수 없습니다. |
    | 예 | 아니요  | 예 | 할당된 사용자는 앱을 보고 로그인할 수 있습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없지만 로그인할 수 있습니다. |
    | 예 | 예  | 아니요  | 할당된 사용자는 앱을 볼 수 없지만 로그인할 수 있습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없지만 로그인할 수 있습니다. |
    | 아니요  | 예 | 예 | 할당된 사용자는 앱을 볼 수 없고 로그인할 수 없습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없고 로그인할 수 없습니다. |
    | 예  | 예 | 아니요  | 할당된 사용자는 앱을 볼 수 없고 로그인할 수 없습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없고 로그인할 수 없습니다. |
    | 예  | 아니요  | 예 | 할당된 사용자는 앱을 볼 수 없고 로그인할 수 없습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없고 로그인할 수 없습니다. |
    | 예  | 예  | 아니요  | 할당된 사용자는 앱을 볼 수 없고 로그인할 수 없습니다.<br>할당되지 않은 사용자는 앱을 볼 수 없고 로그인할 수 없습니다. |

4. 완료되면 **저장**을 선택합니다.

## <a name="use-a-custom-logo"></a>사용자 지정 로고 사용

사용자 지정 로고를 사용하려면:

1. 215 x 215 픽셀 로고를 만들어 .png 형식으로 저장합니다.
2. Azure AD Portal에서 **엔터프라이즈 애플리케이션**을 선택합니다. 그런 다음, 구성하려는 애플리케이션을 찾아 선택합니다.
3. **관리** 섹션에서 **속성**을 선택하여 편집할 **속성** 창을 엽니다. 
4. 아이콘을 선택하여 로고를 업로드합니다.
5. 완료되면 **저장**을 선택합니다.

    ![로고를 변경하는 방법을 보여주는 속성 화면의 스크린샷.](media/add-application-portal/change-logo.png)

   > [!NOTE]
   > 이 **속성** 창에 표시되는 썸네일은 즉시 업데이트되지 않습니다. **속성** 창을 닫았다가 다시 열면 업데이트된 아이콘을 볼 수 있습니다.


> [!TIP]
> Graph API를 사용하여 앱 관리를 자동화할 수 있습니다. [Microsoft Graph API를 사용하여 앱 관리 자동화](https://docs.microsoft.com/graph/application-saml-sso-configure-api)를 참조하세요.


## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작 시리즈를 더 이상 사용하지 않을 생각이면 앱을 삭제하여 테스트 테넌트를 정리하는 것이 좋습니다. 앱 삭제에 대한 내용은 이 시리즈의 마지막 빠른 시작에서 다룹니다. [앱 삭제](delete-application-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

앱에 대한 Single Sign-On을 지정하는 방법에 대한 다음 문서를 진행하세요.
> [!div class="nextstepaction"]
> [Single Sign-On 설정](add-application-portal-setup-sso.md)
