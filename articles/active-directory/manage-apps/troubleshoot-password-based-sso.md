---
title: Azure AD에서 암호 기반 Single Sign-On 문제 해결
description: 암호 기반 Single Sign-On에 대해 구성 된 Azure AD 앱 문제를 해결 하는 방법입니다.
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 436fbdde1283bd8406e631fd37b559bfe11e234e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051941"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Azure AD에서 암호 기반 Single Sign-On 문제 해결

내 앱에서 SSO (암호 기반 Single Sign-On)를 사용 하려면 브라우저 확장을 설치 해야 합니다. 암호 기반 SSO 용으로 구성 된 앱을 선택 하면 확장이 자동으로 다운로드 됩니다. 최종 사용자 관점에서 내 앱을 사용 하는 방법에 대해 알아보려면 [내 앱 포털 도움말](../user-help/my-apps-portal-end-user-access.md)을 참조 하세요.

## <a name="my-apps-browser-extension-not-installed"></a>내 앱 브라우저 확장이 설치 되어 있지 않음
브라우저 확장이 설치 되어 있는지 확인 합니다. 자세히 알아보려면 [내 앱 배포 Azure Active Directory 계획](access-panel-deployment-plan.md)을 참조 하세요. 

## <a name="single-sign-on-not-configured"></a>Single sign-on이 구성 되지 않음
암호 기반 Single Sign-On 구성 되어 있는지 확인 합니다. 자세히 알아보려면 [암호 기반 Single Sign-On 구성](configure-password-single-sign-on-non-gallery-applications.md)을 참조 하세요.

## <a name="users-not-assigned"></a>사용자가 할당 되지 않음
사용자가 앱에 할당 되었는지 확인 합니다. 자세히 알아보려면 [앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)을 참조 하세요.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>자격 증명은 입력했지만 확장에서 제출하지 않음

이 문제는 일반적으로 애플리케이션 공급업체에서 최근에 로그인 페이지를 변경하여 필드를 추가했거나, 사용자 이름 및 암호 필드 감지에 사용되는 식별자를 변경했거나, 애플리케이션의 로그인 경험 방식을 수정한 경우에 발생합니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 이러한 통합이 올바르게 작동하지 않는 경우에는 가능한 신속하게 해결할 수 있도록 지원 사례를 엽니다.

**이 애플리케이션의 공급업체와 연락된 경우** Microsoft에서 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위해 작업할 수 있도록 알려주세요. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>자격 증명을 입력하고 제출했지만 페이지에 자격 증명이 잘못되었다고 표시됨

이 문제를 해결하려면 먼저 다음과 같은 작업을 사용합니다.

- 사용자가 먼저 저장된 자격 증명을 사용해서 **애플리케이션 웹 사이트에 직접 로그인**을 시도하도록 합니다.

  * 로그인이 작동 하는 경우 사용자가 [내 앱](https://myapps.microsoft.com/) 의 응용 프로그램 **섹션에** 있는 **응용 프로그램 타일** 에서 **자격 증명 업데이트** 단추를 클릭 하 여 알려진 최신 작업 사용자 이름 및 암호로 업데이트 하도록 합니다.

  * 자신이나 다른 관리자가 이 사용자에게 자격 증명을 할당한 경우는 애플리케이션의 **사용자 및 그룹** 탭으로 이동하여 할당을 선택하고 **자격 증명 업데이트** 단추를 클릭하여 사용자 또는 그룹의 애플리케이션 할당을 찾습니다.

- 사용자가 직접 자격 증명을 할당한 경우는 사용자에게 **애플리케이션에서 암호가 만료되지 않았는지 확인하라고 한** 다음, 만료되었으면 애플리케이션에 직접 로그인하여 **만료된 암호를 업데이트**하도록 합니다.

  * 응용 프로그램에서 암호를 업데이트 한 후에는 사용자에 게 **응용 프로그램의** 응용 프로그램 섹션에서 **응용 프로그램 타일** 에 있는 **자격 증명 업데이트** 단추를 클릭 [하 여 최신](https://myapps.microsoft.com/) 의 알려진 작업 사용자 이름 및 암호로 업데이트 하도록 요청 합니다.

  * 자신이나 다른 관리자가 이 사용자에게 자격 증명을 할당한 경우는 애플리케이션의 **사용자 및 그룹** 탭으로 이동하여 할당을 선택하고 **자격 증명 업데이트** 단추를 클릭하여 사용자 또는 그룹의 애플리케이션 할당을 찾습니다.

- My Apps 브라우저 확장이 실행 중이 고 사용자의 브라우저에서 사용 하도록 설정 되어 있는지 확인 합니다.

- **Incognito, inPrivate 또는 개인 모드**에서 사용자가 내 앱에서 응용 프로그램에 로그인을 시도 하지 않는지 확인 합니다. 이러한 모드에서는 My Apps 확장이 지원 되지 않습니다.

이전 방법이 통하지 않는 경우는 애플리케이션 쪽에서 변경이 일어나 일시적으로 애플리케이션과 Azure AD의 통합이 깨졌을 수 있습니다. 예를 들어, 애플리케이션 공급업체에서 수동 입력과 자동 입력에 대해 다르게 작동하는 스크립트를 페이지에 포함하는 바람에 자동화 통합이 깨지는 경우에 이 문제가 발생할 수 있습니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 애플리케이션 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 통합이 올바로 작동하지 않는 경우 최대한 신속하게 해결하도록 지원 사례를 열 수 있습니다. 

또한 **이 애플리케이션의 공급업체와 연락된 경우는 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위한 작업을 함께 진행할 수 있도록** **Microsoft로 보내 주세요**. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>애플리케이션의 로그인 페이지가 최근에 변경되었거나 추가 필드가 필요한지 확인

애플리케이션의 로그인 페이지가 최근에 변경된 경우는 그 때문에 통합이 깨질 수도 있습니다. 이런 예로는 애플리케이션 공급업체에서 로그인 필드, chaptcha 또는 다단계 인증을 경험에 추가하는 경우가 있습니다. 다행스럽게도 많은 경우에 Microsoft와 애플리케이션 공급업체가 협력하여 이 문제를 신속하게 해결할 수 있습니다.

Microsoft에는 애플리케이션 통합이 중단되었을 때 자동으로 검색할 기술이 있지만 문제를 즉시 발견하지 못하거나 해결할 때까지 시간이 걸릴 수 있습니다. 통합이 올바로 작동하지 않는 경우 최대한 신속하게 해결하도록 지원 사례를 열 수 있습니다. 

또한 **이 애플리케이션의 공급업체와 연락된 경우는 애플리케이션을 Azure Active Directory와 기본적으로 통합하기 위한 작업을 함께 진행할 수 있도록** **Microsoft로 보내 주세요**. 공급업체에서 작업을 시작할 수 있도록 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](../azuread-dev/howto-app-gallery-listing.md)로 보내 주시면 됩니다.

## <a name="capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드 캡처

로그인 필드 캡처는 HTML 지원 로그인 페이지에만 지원 됩니다. Adobe Flash 또는 다른 비 HTML 지원 기술을 사용 하는 것과 같은 비표준 로그인 페이지에는 지원 되지 않습니다.

사용자 지정 앱에 대 한 로그인 필드를 캡처하는 방법에는 두 가지가 있습니다.

- **자동 로그인 필드 캡처** 는 사용자 이름 및 암호 필드에 *잘 알려진 DIV id를 사용* 하는 경우 대부분의 HTML 지원 로그인 페이지에서 잘 작동 합니다. 페이지의 HTML은 특정 조건과 일치 하는 DIV Id를 찾기 스크랩 됩니다. 메타 데이터는 나중에 앱에 재생 될 수 있도록 저장 됩니다.

- **수동 로그인 필드 캡처** 는 앱 공급 업체가 *로그인 입력 필드에 레이블을 나타내지 않는*경우에 사용 됩니다. 수동 캡처는 공급 업체가 *자동으로 검색할 수 없는 여러 필드를 렌더링*하는 경우에도 사용 됩니다. Azure Active Directory (Azure AD)는 페이지에 있는 필드에 대 한 정보를 알려 주는 경우 로그인 페이지에 있는 만큼의 필드에 대 한 데이터를 저장할 수 있습니다.

일반적으로 자동 로그인 필드 캡처가 작동 하지 않는 경우 수동 옵션을 사용해 보세요.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드를 자동으로 캡처

자동 로그인 필드 캡처를 사용 하 여 암호 기반 SSO를 구성 하려면 다음 단계를 수행 합니다.
1. [Azure Portal](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.
2. 왼쪽의 탐색 창에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.
3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.
4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
5. **모든 응용 프로그램** 을 선택 하 여 앱 목록을 봅니다.
   > [!NOTE]
   > 원하는 앱이 표시 되지 않으면 **모든 응용 프로그램** 목록의 맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.
6. SSO에 대해 구성 하려는 앱을 선택 합니다.
7. 앱이 로드 된 후 왼쪽의 탐색 창에서 **Single sign-on** 을 선택 합니다.
8. **암호 기반 로그온** 모드를 선택 합니다.
9. 사용자가 로그인 할 때 사용자 이름 및 암호를 입력 하는 페이지의 URL 인 로그온 **url**을 입력 합니다. *사용자가 제공 하는 URL에 대 한 페이지에 로그인 필드가 표시 되는지 확인*합니다.
10. **저장**을 선택합니다.
    사용자 이름 및 암호 입력 상자에 대해 페이지가 자동으로 스크랩 됩니다. 이제 Azure AD를 사용 하 여 내 앱 브라우저 확장을 사용 하 여 해당 앱에 암호를 안전 하 게 전송할 수 있습니다.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드 수동 캡처

로그인 필드를 수동으로 캡처하려면 My Apps 브라우저 확장이 설치 되어 있어야 합니다. 또한 브라우저를 *inPrivate*, *incognito*또는 *private* 모드에서 실행할 수 없습니다.

수동 로그인 필드 캡처를 사용 하 여 앱에 대 한 암호 기반 SSO를 구성 하려면 다음 단계를 수행 합니다.
1. [Azure Portal](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.
2. 왼쪽의 탐색 창에서 **모든 서비스** 를 선택 하 여 Azure AD 확장을 엽니다.
3. 필터 검색 상자에 **Azure Active Directory** 를 입력 하 고 **Azure Active Directory**를 선택 합니다.
4. Azure AD 탐색 창에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
5. **모든 응용 프로그램** 을 선택 하 여 앱 목록을 봅니다.
   > [!NOTE] 
   > 원하는 앱이 표시 되지 않으면 **모든 응용 프로그램** 목록의 맨 위에 있는 **필터** 컨트롤을 사용 합니다. **표시** 옵션을 "모든 응용 프로그램"으로 설정 합니다.
6. SSO에 대해 구성 하려는 앱을 선택 합니다.
7. 앱이 로드 된 후 왼쪽의 탐색 창에서 **Single sign-on** 을 선택 합니다.
8. **암호 기반 로그온** 모드를 선택 합니다.
9. 사용자가 로그인 할 때 사용자 이름 및 암호를 입력 하는 페이지인 **로그온 URL**을 입력 합니다. *사용자가 제공 하는 URL에 대 한 페이지에 로그인 필드가 표시 되는지 확인*합니다.
10. ** * &lt; Appname &gt; * 암호 Single sign-on 설정 구성**을 선택 합니다.
11. **로그인 필드 수동 검색**을 선택 합니다.
14. **확인**을 선택합니다.
15. **저장**을 선택합니다.
16. 지침에 따라 내 앱을 사용 합니다.


## <a name="troubleshoot-problems"></a>문제 해결

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>"해당 URL에서 로그인 필드를 찾을 수 없습니다." 오류가 발생 합니다.

로그인 필드의 자동 검색에 실패 하면이 오류 메시지가 표시 됩니다. 이 문제를 해결 하려면 수동 로그인 필드 검색을 시도 합니다. 이 문서의 [응용 프로그램에 대 한 로그인 필드 수동 캡처](#manually-capture-sign-in-fields-for-an-app) 섹션을 참조 하세요.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>"Single Sign-On 구성을 저장할 수 없습니다." 오류가 표시 됩니다.

드물지만 SSO 구성 업데이트는 실패 합니다. 이 문제를 해결 하려면 구성을 다시 저장해 보세요.

오류가 계속 발생 하면 지원 사례를 엽니다. 이 문서에서 [포털 알림 세부 정보 보기](#view-portal-notification-details) 및 [지원 엔지니어에 게 알림 세부 정보 보내기](#send-notification-details-to-a-support-engineer-to-get-help) 에 설명 된 정보를 포함 합니다.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>앱에 대 한 로그인 필드를 수동으로 검색할 수 없음

수동 검색이 작동 하지 않는 경우 다음 동작을 관찰할 수 있습니다.
- 수동 캡처 프로세스가 작동 하는 것으로 보이지만 캡처된 필드가 잘못 되었습니다.
- 캡처 프로세스를 실행 하면 올바른 필드가 강조 표시 되지 않습니다.
- 캡처 프로세스는 앱의 로그인 페이지로 이동 하지만 아무 작업도 수행 하지 않습니다.
- 수동 캡처가 작동 하는 것 처럼 보이지만 사용자가 내 앱에서 앱으로 이동할 때 SSO가 발생 하지 않습니다.

이러한 문제가 발생 하는 경우 다음 작업을 수행 합니다.
- 최신 버전의 My Apps 브라우저 확장을 *설치 하 고 사용 하도록 설정*했는지 확인 합니다.
- 캡처 프로세스 중 브라우저가 *incognito*, *inPrivate*또는 *개인* 모드에 있지 않은지 확인 합니다. 이러한 모드에서는 My Apps 확장이 지원 되지 않습니다.
- *Incognito*, *inPrivate*또는 *개인 모드*에서 사용자가 내 앱에서 앱에 로그인을 시도 하지 않는지 확인 합니다.
- 수동 캡처 프로세스를 다시 시도 합니다. 빨간색 표식이 올바른 필드 위에 있는지 확인 합니다.
- 수동 캡처 프로세스가 응답 하지 않거나 로그인 페이지가 응답 하지 않는 것으로 보이면 수동 캡처 프로세스를 다시 시도 하세요. 그러나 이번에는 프로세스를 완료 한 후 F12 키를 눌러 브라우저의 개발자 콘솔을 엽니다. **콘솔** 탭을 선택 합니다. **location = "* &lt; 앱 &gt; 을 구성할 때 지정한 로그인 URL*"** 을 입력 한 다음 enter 키를 누릅니다. 이렇게 하면 캡처 프로세스를 종료 하 고 캡처된 필드를 저장 하는 페이지 리디렉션이 강제로 수행 됩니다.

## <a name="request-support"></a>지원 요청 
SSO를 설정 하 고 사용자를 할당 하는 경우 오류 메시지가 표시 되 면 지원 티켓을 엽니다. 가능한 한 많은 다음 정보를 포함 합니다.

-   상관 관계 오류 ID
-   UPN(사용자 전자 메일 주소)
-   TenantID
-   브라우저 종류
-   오류가 발생 한 표준 시간대 및 시간/시간 프레임
-   Fiddler 추적

### <a name="view-portal-notification-details"></a>포털 알림 세부 정보 보기

포털 알림의 세부 정보를 보려면 다음 단계를 수행 합니다.
1. Azure Portal의 오른쪽 위 모퉁이에 있는 **알림** 아이콘 (벨)을 선택 합니다.
2. *오류* 상태를 표시 하는 알림을 선택 합니다. (빨간색 "!"이 있습니다.)
   > [!NOTE]
   > *성공* 또는 *진행 중* 상태에 있는 알림은 선택할 수 없습니다.
3. **알림 세부 정보** 창이 열립니다. 문제에 대 한 자세한 내용은이 정보를 참조 하세요.
5. 여전히 도움이 필요한 경우 정보를 지원 엔지니어 또는 제품 그룹과 공유 합니다. **복사 오류** 상자 오른쪽에 있는 **복사** 아이콘을 선택 하 여 공유에 알림 세부 정보를 복사 합니다.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>지원 엔지니어에 게 알림 정보를 보내 도움 받기

이 섹션에 나열 된 *모든* 세부 정보를 지원과 함께 제공 하 여 신속 하 게 도울 수 있도록 하는 것이 중요 합니다. 이를 기록 하려면 스크린샷을 만들거나 **복사 오류**를 선택할 수 있습니다.

다음 정보는 각 알림 항목의 의미를 설명 하 고 예제를 제공 합니다.

#### <a name="essential-notification-items"></a>필수 알림 항목

- **제목**: 알림의 설명 제목입니다.

   예: *응용 프로그램 프록시 설정*

- **설명**: 작업의 결과로 발생 한 작업입니다.

   예: *입력 한 내부 URL은 다른 응용 프로그램에서 이미 사용 중입니다.*

- **알림 ID**: 알림의 고유 ID입니다.

    예: *Clientnotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **클라이언트 요청 id**: 브라우저에서 만든 특정 요청 id입니다.

    예: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **타임 스탬프 utc**: 알림이 발생 한 시간에 대 한 타임 스탬프 (utc)입니다.

    예: *2017-03-23T19:50:43.7583681 z*

- **내부 트랜잭션 id**: 시스템에서 오류를 조회 하는 데 사용 되는 내부 id입니다.

    예: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: 작업을 실행 한 사용자입니다.

    예: *tperkins \@ f128.info*

- **테 넌 트 ID**: 작업을 실행 한 사용자가 구성원 인 테 넌 트의 고유 ID입니다.

    예: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **사용자 개체 id**: 작업을 실행 한 사용자의 고유 id입니다.

    예: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>자세한 알림 항목

- **표시 이름**: (비어 있을 수 있음) 오류에 대 한 자세한 표시 이름입니다.

    예: *응용 프로그램 프록시 설정*

- **상태**: 알림의 특정 상태입니다.

    예: *실패*

- **개체 id**: (비어 있을 수 있음) 작업이 실행 된 개체 id입니다.

   예: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **세부 정보**: 작업의 결과로 발생 한 작업에 대 한 자세한 설명입니다.

    예: *내부 url ' <https://bing.com/> '은 (는) 이미 사용 중 이므로 유효 하지 않습니다.*

- **복사 오류**: **오류 복사** 텍스트 상자 오른쪽에 있는 **복사 아이콘** 을 선택 하 여 지원에 도움을 주는 알림 세부 정보를 복사할 수 있습니다.

    예 들어   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>다음 단계
* [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
* [내 앱 배포 계획](access-panel-deployment-plan.md)
