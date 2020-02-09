---
title: 비 갤러리 앱에 대해 암호 SSO를 구성 하는 문제
description: Azure AD 응용 프로그램 갤러리에 없는 사용자 지정 앱에 대해 SSO (암호 Single Sign-On)를 구성할 때 발생 하는 일반적인 문제입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed8bafe7f5bc28cf37205107f8ab6dd5cdb4907c
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274143"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>비 갤러리 응용 프로그램에 대해 암호 Single Sign-On를 구성 하는 문제

이 문서에서는 비 갤러리 앱에 대해 SSO ( *암호 Single Sign-On* )를 구성할 때 발생할 수 있는 일반적인 문제에 대해 설명 합니다.

## <a name="capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드 캡처

로그인 필드 캡처는 HTML 지원 로그인 페이지에만 지원 됩니다. Adobe Flash 또는 다른 비 HTML 지원 기술을 사용 하는 것과 같은 비표준 로그인 페이지에는 지원 되지 않습니다.

사용자 지정 앱에 대 한 로그인 필드를 캡처하는 방법에는 두 가지가 있습니다.

- **자동 로그인 필드 캡처** 는 사용자 이름 및 암호 필드에 *잘 알려진 DIV id를 사용* 하는 경우 대부분의 HTML 지원 로그인 페이지에서 잘 작동 합니다. 페이지의 HTML은 특정 조건과 일치 하는 DIV Id를 찾기 스크랩 됩니다. 메타 데이터는 나중에 앱에 재생 될 수 있도록 저장 됩니다.

- **수동 로그인 필드 캡처** 는 앱 공급 업체가 *로그인 입력 필드에 레이블을 나타내지 않는*경우에 사용 됩니다. 수동 캡처는 공급 업체가 *자동으로 검색할 수 없는 여러 필드를 렌더링*하는 경우에도 사용 됩니다. Azure Active Directory (Azure AD)는 페이지에 있는 필드에 대 한 정보를 알려 주는 경우 로그인 페이지에 있는 만큼의 필드에 대 한 데이터를 저장할 수 있습니다.

일반적으로 자동 로그인 필드 캡처가 작동 하지 않는 경우 수동 옵션을 사용해 보세요.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드를 자동으로 캡처

자동 로그인 필드 캡처를 사용 하 여 암호 기반 SSO를 구성 하려면 다음 단계를 수행 합니다.

1. [Azure 포털](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

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

    사용자 이름 및 암호 입력 상자에 대해 페이지가 자동으로 스크랩 됩니다. 이제 액세스 패널 브라우저 확장을 사용 하 여 Azure AD를 사용 하 여 해당 앱에 안전 하 게 암호를 전송할 수 있습니다.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>앱에 대 한 로그인 필드 수동 캡처

로그인 필드를 수동으로 캡처하려면 액세스 패널 브라우저 확장이 설치 되어 있어야 합니다. 또한 브라우저를 *inPrivate*, *incognito*또는 *private* 모드에서 실행할 수 없습니다.

확장을 설치 하려면이 문서의 [액세스 패널 브라우저 확장 설치](#install-the-access-panel-browser-extension) 섹션을 참조 하세요.

수동 로그인 필드 캡처를 사용 하 여 앱에 대 한 암호 기반 SSO를 구성 하려면 다음 단계를 수행 합니다.

1. [Azure 포털](https://portal.azure.com/)을 엽니다. 전역 관리자 또는 공동 관리자 권한으로 로그인 합니다.

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

10. ***&lt;appname&gt;* 암호 Single sign-on 설정 구성**을 선택 합니다.

11. **로그인 필드 수동 검색**을 선택 합니다.

14. **확인**을 선택합니다.

15. **저장**을 선택합니다.

16. 지침에 따라 액세스 패널을 사용 합니다.

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

- 수동 캡처가 작동 하는 것 처럼 보이지만 사용자가 액세스 패널에서 앱으로 이동할 때 SSO가 발생 하지 않습니다.

이러한 문제가 발생 하는 경우 다음 작업을 수행 합니다.

- 최신 버전의 액세스 패널 브라우저 확장을 *설치 하 고 사용 하도록 설정*했는지 확인 합니다. 이 문서의 [액세스 패널 브라우저 확장 설치](#install-the-access-panel-browser-extension) 섹션을 참조 하세요.

- 캡처 프로세스 중 브라우저가 *incognito*, *inPrivate*또는 *개인* 모드에 있지 않은지 확인 합니다. 이러한 모드에서는 액세스 패널 확장이 지원 되지 않습니다.

- 사용자가 *incognito*, *inPrivate*또는 *개인 모드*에서 액세스 패널을 통해 앱에 로그인을 시도 하지 않는지 확인 합니다.

- 수동 캡처 프로세스를 다시 시도 합니다. 빨간색 표식이 올바른 필드 위에 있는지 확인 합니다.

- 수동 캡처 프로세스가 응답 하지 않거나 로그인 페이지가 응답 하지 않는 것으로 보이면 수동 캡처 프로세스를 다시 시도 하세요. 그러나 이번에는 프로세스를 완료 한 후 F12 키를 눌러 브라우저의 개발자 콘솔을 엽니다. **콘솔** 탭을 선택 하 고,  ***응용 프로그램&gt;를 구성할 때 지정한 로그인 URL&lt;*** 를 입력 한 다음 enter 키를 누릅니다. 이렇게 하면 캡처 프로세스를 종료 하 고 캡처된 필드를 저장 하는 페이지 리디렉션이 강제로 수행 됩니다.

### <a name="contact-support"></a>지원에 문의

문제가 여전히 발생 하면 Microsoft 지원를 사용 하 여 케이스를 엽니다. 시도한 작업을 설명 합니다. [포털 알림 세부 정보 보기](#view-portal-notification-details) 및 [지원 엔지니어에 게 알림 세부 정보 보내기](#send-notification-details-to-a-support-engineer-to-get-help) 에 설명 된 세부 정보를 포함 하 여이 문서의 도움말 섹션을 가져옵니다 (해당 하는 경우).

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

다음 단계를 수행하세요.

1. 지원 되는 브라우저에서 [액세스 패널](https://myapps.microsoft.com) 을 엽니다. *사용자*로 Azure AD에 로그인 합니다.

2. 액세스 패널에서 **암호-SSO 응용 프로그램** 을 선택 합니다.

3. 소프트웨어를 설치할지 묻는 메시지가 표시 되 면 **지금 설치**를 선택 합니다.

4. 브라우저에 대 한 다운로드 페이지로 이동 합니다. 확장을 **추가** 하도록 선택 합니다.

5. 메시지가 표시 되 면 **사용** 또는 **허용**을 선택 합니다.

6. 설치 후 브라우저를 다시 시작 합니다.

7. 액세스 패널에 로그인 합니다. 암호 SSO가 지원 되는 앱을 열 수 있는지 확인 합니다.

다음 링크를 통해 Chrome 및 Firefox에 대 한 브라우저 확장을 직접 다운로드할 수도 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>포털 알림 세부 정보 보기

포털 알림의 세부 정보를 보려면 다음 단계를 수행 합니다.

1. Azure Portal의 오른쪽 위 모퉁이에 있는 **알림** 아이콘 (벨)을 선택 합니다.

2. *오류* 상태를 표시 하는 알림을 선택 합니다. (빨간색 "!"이 있습니다.)

   > [!NOTE]
   > *성공* 또는 *진행 중* 상태에 있는 알림은 선택할 수 없습니다.

3. **알림 세부 정보** 창이 열립니다. 문제에 대 한 자세한 내용은이 정보를 참조 하세요.

5. 여전히 도움이 필요한 경우 정보를 지원 엔지니어 또는 제품 그룹과 공유 합니다. **복사 오류** 상자 오른쪽에 있는 **복사** 아이콘을 선택 하 여 공유에 알림 세부 정보를 복사 합니다.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>지원 엔지니어에 게 알림 정보를 보내 도움 받기

이 섹션에 나열 된 *모든* 세부 정보를 지원과 함께 제공 하 여 신속 하 게 도울 수 있도록 하는 것이 중요 합니다. 이를 기록 하려면 스크린샷을 만들거나 **복사 오류**를 선택할 수 있습니다.

다음 정보는 각 알림 항목의 의미를 설명 하 고 예제를 제공 합니다.

### <a name="essential-notification-items"></a>필수 알림 항목

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

    예: *tperkins\@f128.info*

- **테넌트 ID**: 작업을 실행 한 사용자가 구성원 인 테넌트의 고유 ID입니다.

    예: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **사용자 개체 id**: 작업을 실행 한 사용자의 고유 id입니다.

    예: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>자세한 알림 항목

- **표시 이름**: (비어 있을 수 있음) 오류에 대 한 자세한 표시 이름입니다.

    예: *응용 프로그램 프록시 설정*

- **상태**: 알림의 특정 상태입니다.

    예: *실패*

- **개체 id**: (비어 있을 수 있음) 작업이 실행 된 개체 id입니다.

   예: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **세부 정보**: 작업의 결과로 발생 한 작업에 대 한 자세한 설명입니다.

    예: *내부 url '<https://bing.com/>'은 (는) 이미 사용 중 이므로 유효 하지 않습니다.*

- **복사 오류**: **오류 복사** 텍스트 상자 오른쪽에 있는 **복사 아이콘** 을 선택 하 여 지원에 도움을 주는 알림 세부 정보를 복사할 수 있습니다.

    예: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>다음 단계
[애플리케이션 프록시를 사용하여 앱에 Single Sign-On 제공](application-proxy-configure-single-sign-on-with-kcd.md)
