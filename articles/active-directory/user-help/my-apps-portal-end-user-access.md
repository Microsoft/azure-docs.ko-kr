---
title: 내 앱 포털을 찾아 앱 사용 - Azure AD
description: 내 앱 포털을 찾은 다음 조직의 클라우드 기반 앱에 액세스하는 방법을 알아봅니다.
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
ms.openlocfilehash: 78204be967bafdd7d01af4f9f286bd15347e4a9f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798043"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>내 앱 포털에서 로그인하고 앱 시작

웹 기반 **내 앱** 포털에서 회사 또는 학교 계정을 사용하여 다음을 수행할 수 있습니다.

- 다양한 조직의 클라우드 기반 앱 보기 및 시작
- 프로필 및 계정 정보 업데이트
- **그룹** 정보 보기
- 앱 및 그룹에 대한 **액세스 검토** 수행

**My Apps** 포털에 액세스할 수 없는 경우 사용 권한에 대해 기술 지원팀에 문의하세요.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

> [!Important]
> 이 콘텐츠는 내 앱 사용자를 위한 것입니다. 관리자인 경우 [애플리케이션 관리 설명서](../manage-apps/index.yml)에서 클라우드 기반 앱을 설정 및 관리하는 방법에 대한 더 자세한 정보를 확인할 수 있습니다.

## <a name="supported-browsers"></a>지원되는 브라우저

다음 웹 브라우저에서 **내 앱** 포털로 이동할 수 있습니다.

- Google Chrome

- Mozilla Firefox 버전 26.0 이상

- Microsoft Edge

- Internet Explorer 버전 11(지원이 제한됨)

## <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장 다운로드 및 설치

메시지가 표시되면 내 앱 보안 로그인 확장을 다운로드하여 설치합니다. 이 확장을 사용하면 Single Sign-On 프로세스를 사용해야 하는 조직의 클라우드 앱을 시작할 수 있습니다. 조직에서 Single Sign-On을 이미 설정한 경우에는 확장이 자동으로 설치되므로 이 섹션을 건너뛸 수 있습니다.

이 확장을 통해 다음과 같은 작업을 할 수 있습니다.

- 로그인 페이지에서 앱에 직접 로그인합니다.

- **빠른 검색** 기능을 사용하여 앱을 시작합니다.

- **최근에 사용** 섹션에서 마지막으로 사용한 앱을 확인합니다.

- [애플리케이션 프록시](../manage-apps/application-proxy.md)를 통해 원격으로 내부 회사 URL을 사용합니다.

### <a name="to-download-and-install-the-extension"></a>확장을 다운로드하여 설치하려면

다음과 같이 사용 중인 브라우저에 따라 확장을 다운로드하여 설치합니다.

- **Google Chrome.** Chrome 웹 스토어에서 [내 앱 보안 로그인 확장](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) 기능으로 이동한 다음 **Chrome에 추가**를 선택합니다.

- **Mozilla Firefox**. **Firefox 추가 기능** 페이지에서 [내 앱 보안 로그인 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/) 기능으로 이동한 다음 **Firefox에 추가**를 선택합니다.

- **Microsoft Edge**. Microsoft Store에서 [내 앱 보안 로그인 확장](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) 기능으로 이동한 후 **가져오기**를 선택하여 Microsoft Edge 레거시 브라우저의 확장을 가져옵니다.  
새 Microsoft Edge 브라우저(Edge on Chromium)를 사용하는 경우 [Microsoft Edge 추가 기능 스토어](https://microsoftedge.microsoft.com/addons/category/EdgeExtensionsEditorsPick)에서 확장을 가져올 수 있습니다.

**주소** 표시줄의 오른쪽에 아이콘이 추가되어 로그인하고 확장을 사용자 지정할 수 있습니다.

## <a name="to-change-your-my-apps-portal-using-the-extension"></a>확장을 사용하여 내 앱 포털을 변경하려면

**최근에 사용** 섹션에서 표시할 앱 수를 선택하고 조직의 내부 URL 리디렉션 허용 여부를 결정할 수 있습니다.

1. **주소** 표시줄 오른쪽의 새 **내 앱 보안 로그인 확장** 아이콘(![확장 아이콘](media/my-apps-portal/my-apps-portal-extension-icon.png))을 선택한 다음 **로그인하여 시작**을 선택합니다.

2. **설정** 아이콘(![설정 아이콘](media/my-apps-portal/my-apps-portal-extension-settings-icon.png))을 마우스 오른쪽 단추로 클릭한 다음 **설정**을 선택합니다.

3. **설정** 상자에서 포털에 표시할 최근 앱 수를 선택하고, 원격으로 사용할 수 있도록 조직의 내부 URL 리디렉션을 허용할 것인지 여부를 선택합니다.

    ![사용 가능한 사용자 지정을 보여 주는 확장의 설정 페이지](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-by-device"></a>디바이스에서 My Apps 포털에 액세스 및 사용

컴퓨터, Intune Managed Browser, iOS 또는 Android 모바일 디바이스에서 내 앱 포털에 액세스하여 사용할 수 있습니다.

![내 앱 포털의 앱 페이지](media/my-apps-portal/my-apps-portal-apps-page.png)

### <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>컴퓨터에서 내 앱 포털에 액세스 및 사용

조직의 클라우드 기반 앱에 액세스하여 사용할 수 있는 권한이 있는 경우 **내 앱** 포털을 통해 앱을 사용할 수 있습니다.

1. 컴퓨터에서 회사 또는 학교 계정에 로그인 합니다.

2. 지원되는 웹 브라우저를 열어 https://myapps.microsoft.com 으로 이동하거나 `https://myapps.microsoft.com/contoso.com` 과 같이 사용자 지정된 페이지로 이동하는 경우 조직에서 제공하는 링크를 사용합니다.

    사용자가 사용할 수 있는 조직 소유의 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

3. **앱** 페이지에서 사용하려는 앱을 선택합니다.

    앱에 대한 새 페이지가 열리고 여기에서 로그인하거나(필요한 경우) 앱 사용을 시작할 수 있습니다.

### <a name="access-and-use-the-my-apps-portal-on-an-intune-managed-browser"></a>Intune Managed Browser에서 내 앱 포털에 액세스 및 사용

iOS 및 Android 디바이스의 Intune Managed Browser에서 조직의 앱을 보고 사용합니다.

1. 모바일 디바이스에서, Apple App Store 및 Google Play 스토어에서 Intune Managed Browser 앱을 다운로드하여 설치합니다.

2. Intune Managed Browser 앱을 열고 https://myapps.microsoft.com 으로 이동하거나, https://myapps.microsoft.com/contoso.com 과 같이 사용자 지정된 페이지로 이동하는 경우 조직에서 제공하는 링크를 사용합니다.

    조직에서 소유하고 사용자가 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

3. **앱** 페이지에서 사용하려는 앱을 선택합니다.

    앱에 대한 새 페이지가 열리고 여기에서 로그인하거나(필요한 경우) 앱 사용을 시작할 수 있습니다.

### <a name="access-and-use-the-my-apps-portal-on-an-ios-device"></a>iOS 디바이스에서 내 앱 포털에 액세스 및 사용

iOS 버전 7 이상을 실행하는 iPhone 또는 iPad 디바이스에서 **내 앱** 포털을 보고 사용합니다. iOS 디바이스에 [내 앱 모바일 앱](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)을 설치하여 조직의 앱에 액세스할 수도 있습니다.

1. 모바일 디바이스에서 Safari와 같은 웹 브라우저 앱을 시작합니다.

2. https://myapps.microsoft.com 으로 이동하거나 https://myapps.microsoft.com/contoso.com 과 같이 사용자 지정된 페이지로 이동하는 경우 조직에서 제공하는 링크를 사용합니다.

    사용자가 사용할 수 있는 조직 소유의 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

3. **앱** 페이지에서 사용하려는 앱을 선택합니다.

    앱에 대한 새 페이지가 열리고 여기에서 로그인하거나(필요한 경우) 앱 사용을 시작할 수 있습니다.

### <a name="access-and-use-the-my-apps-portal-on-an-android-device"></a>Android 디바이스에서 내 앱 포털에 액세스 및 사용

Android 디바이스에서 **내 앱** 포털을 보고 사용합니다.

1. 모바일 디바이스에서 Google Chrome 등의 웹 브라우저 앱을 시작합니다.

2. https://myapps.microsoft.com 으로 이동하거나 https://myapps.microsoft.com/contoso.com 과 같이 사용자 지정된 페이지로 이동하는 경우 조직에서 제공하는 링크를 사용합니다.

    조직에서 소유하고 사용자가 사용할 수 있는 모든 클라우드 기반 앱을 보여 주는 **앱** 페이지가 나타납니다.

3. **앱** 페이지에서 사용하려는 앱을 선택합니다.

    앱에 대한 새 페이지가 열리고 여기에서 로그인하거나(필요한 경우) 앱 사용을 시작할 수 있습니다.

## <a name="add-a-new-app-to-the-my-apps-portal"></a>내 앱 포털에 새 앱 추가

관리자가 권한을 부여한 경우 **앱** 페이지에 새 앱을 추가할 수 있습니다.

1. **앱** 페이지에서 다음 중 하나를 수행합니다.
    - 원래 내 앱 환경을 사용할 경우 표시된 대로 **앱 추가**를 선택합니다.

      ![내 앱 포털에서 앱 페이지 추가](media/my-apps-portal/my-apps-portal-add-apps-page.png)

    - 업데이트된 내 앱 환경을 사용할 경우 **셀프 서비스 앱 추가**를 선택합니다.

       ![내 앱 포털(myapplications.microsoft.com)의 앱 추가 페이지](media/my-apps-portal/my-apps-portal-add-app-link.png)

2. 제공된 목록에서 추가하려는 앱을 선택하고 **추가**를 선택합니다.

3. **앱** 페이지의 목록에 앱이 추가됩니다.

    일부 앱은 추가하기 전에 관리자 승인이 필요할 수 있습니다. 이 경우 앱은 관리자가 승인할 때까지 **앱** 페이지에 추가되지 않습니다.

## <a name="start-a-cloud-based-app"></a>클라우드 기반 앱 시작

**내 앱** 포털에서 사용 가능한 클라우드 기반 앱을 시작할 수 있습니다. 사용 권한이 있는 앱만 표시됩니다.

- **앱** 페이지에서 사용하려는 앱을 선택합니다.

    앱에 대한 새 페이지가 열리고 여기에서 로그인하거나(필요한 경우) 앱 사용을 시작할 수 있습니다.

## <a name="activities-in-the-my-apps-portal"></a>내 앱 포털에서의 작업

**내 앱** 포털로 이동한 후 다음을 수행할 수 있습니다.

- 조직에서 만들고 제공하는 다양한 범주로 앱을 구성합니다. 자세한 내용은 [내 앱 포털에서 컬렉션 액세스 및 사용](my-applications-portal-workspaces.md)을 참조하세요.

- 애플리케이션에 부여된 권한을 검토, 업데이트 또는 취소 합니다. 자세한 내용은 [내 앱 포털에서 애플리케이션 사용 권한 편집 또는 해지](my-applications-portal-permissions-saved-accounts.md)를 참조하세요.

>[!Note]
>사용 가능한 컬렉션이나 범주가 표시되지 않으면 관리자가 설정하지 않았거나 사용자와 공유하지 않은 것입니다. 공유 작업 영역을 보기 위한 추가 지원이나 권한을 조직의 기술 지원 팀에 요청하세요.

## <a name="next-steps"></a>다음 단계

**앱** 페이지로 이동한 후 다음 작업을 수행할 수 있습니다.

- [프로필 정보 변경](my-apps-portal-end-user-update-profile.md)

- [그룹 관련 정보 보기 및 업데이트](my-apps-portal-end-user-groups.md)

- [내 액세스 검토 수행](my-apps-portal-end-user-access-reviews.md)