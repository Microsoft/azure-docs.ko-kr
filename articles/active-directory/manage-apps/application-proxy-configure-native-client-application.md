---
title: 네이티브 클라이언트 앱 게시 - Azure AD | Microsoft Docs
description: Azure AD 애플리케이션 프록시 커넥터와 통신하는 네이티브 클라이언트 앱을 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2a2bb9dd543da7455a276075a829ef06032edb
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159287"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>네이티브 클라이언트 응용 프로그램을 사용 하 여 프록시 응용 프로그램과 상호 작용 하는 방법

Azure Active Directory (Azure AD) 응용 프로그램 프록시를 사용 하 여 웹 앱을 게시할 수 있지만 ADAL (Azure AD 인증 라이브러리)을 사용 하 여 구성 된 네이티브 클라이언트 응용 프로그램을 게시 하는 데에도 사용할 수 있습니다. 네이티브 클라이언트 애플리케이션은 디바이스에 설치되는 반면 웹앱은 브라우저를 통해 액세스되므로 웹앱과 다릅니다.

네이티브 클라이언트 응용 프로그램을 지원 하기 위해 응용 프로그램 프록시는 헤더에 전송 되는 Azure AD 발급 토큰을 허용 합니다. 응용 프로그램 프록시 서비스는 사용자에 대 한 인증을 수행 합니다. 이 솔루션은 인증에 응용 프로그램 토큰을 사용 하지 않습니다.

![최종 사용자, Azure AD 및 게시 된 응용 프로그램 간의 관계](./media/application-proxy-configure-native-client-application/richclientflow.png)

네이티브 응용 프로그램을 게시 하려면 인증을 처리 하 고 많은 클라이언트 환경을 지 원하는 Azure AD 인증 라이브러리를 사용 합니다. 애플리케이션 프록시는 [Web API 시나리오에 대한 네이티브 애플리케이션](../azuread-dev/native-app.md)에 맞습니다.

이 문서에서는 애플리케이션 프록시 및 Azure AD 인증 라이브러리를 사용하여 네이티브 애플리케이션을 게시하는 네 가지 단계를 안내합니다.

## <a name="step-1-publish-your-proxy-application"></a>1 단계: 프록시 응용 프로그램 게시

다른 애플리케이션과 마찬가지로 프록시 애플리케이션을 게시하고 애플리케이션에 액세스하도록 사용자를 할당합니다. 자세한 내용은 [애플리케이션 프록시를 사용하여 애플리케이션 게시](application-proxy-add-on-premises-application.md)를 참조하세요.

## <a name="step-2-register-your-native-application"></a>2 단계: 네이티브 응용 프로그램 등록

이제 다음과 같이 Azure AD에서 응용 프로그램을 등록 해야 합니다.

1. [Azure Active Directory 포털](https://aad.portal.azure.com/)에 로그인 합니다. **Azure Active Directory 관리 센터** 에 대 한 **대시보드가** 나타납니다.
1. 사이드바에서 **Azure Active Directory**를 선택 합니다. **Azure Active Directory** 개요 페이지가 나타납니다.
1. Azure AD 개요 사이드바에서 **앱 등록**를 선택 합니다. 모든 앱 등록 목록이 표시 됩니다.
1. **새 등록**을 선택합니다. **응용 프로그램 등록** 페이지가 나타납니다.

   ![Azure Portal에서 새 앱 등록을 만듭니다.](./media/application-proxy-configure-native-client-application/create.png)

1. **이름** 제목에서 응용 프로그램에 대 한 사용자 연결 표시 이름을 지정 합니다.
1. **지원 되는 계정 유형** 제목에서 다음 지침을 사용 하 여 액세스 수준을 선택 합니다.

   - 조직의 내부 계정만을 대상으로 하려면 **이 조직 디렉터리 에서만 계정을**선택 합니다.
   - 비즈니스 또는 교육용 고객만 대상으로 하려면 **모든 조직 디렉터리에서 계정**을 선택 합니다.
   - 가장 넓은 Microsoft id 집합을 대상으로 하려면 **모든 조직 디렉터리와 개인 Microsoft 계정에서 계정을**선택 합니다.

1. **URI 리디렉션** 제목에서 **공용 클라이언트 (모바일 & 데스크톱)** 를 선택 하 고 응용 프로그램에 대 한 리디렉션 URI를 입력 합니다.
1. **Microsoft Platform 정책을**선택 하 고 읽은 다음 **등록**을 선택 합니다. 새 응용 프로그램 등록에 대 한 개요 페이지가 생성 되 고 표시 됩니다.

새 응용 프로그램 등록을 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory와 응용 프로그램 통합](../develop/quickstart-register-app.md)을 참조 하세요.

## <a name="step-3-grant-access-to-your-proxy-application"></a>3 단계: 프록시 응용 프로그램에 대 한 액세스 권한 부여

이제 네이티브 응용 프로그램을 등록 했으므로 디렉터리의 다른 응용 프로그램에 대 한 액세스 권한을 제공할 수 있습니다 .이 경우에는 프록시 응용 프로그램에 액세스할 수 있습니다. 네이티브 응용 프로그램이 프록시 응용 프로그램에 노출 될 수 있게 하려면 다음을 수행 합니다.

1. 새 응용 프로그램 등록 페이지의 사이드바에서 **API 권한**을 선택 합니다. 새 응용 프로그램 등록에 대 한 **API 권한** 페이지가 나타납니다.
1. **사용 권한 추가**를 선택합니다. **API 권한 요청** 페이지가 나타납니다.
1. **Api 선택** 설정에서 **내 조직에서 사용 하는 api**를 선택 합니다. Api를 노출 하는 디렉터리의 응용 프로그램이 포함 된 목록이 표시 됩니다.
1. 검색 상자에를 입력 하거나 스크롤하여 [1 단계: 프록시 응용 프로그램 게시](#step-1-publish-your-proxy-application)에서 게시 한 프록시 응용 프로그램을 찾은 다음 프록시 응용 프로그램을 선택 합니다.
1. **응용 프로그램에 필요한 사용 권한 유형** 제목에서 사용 권한 유형을 선택 합니다. 기본 응용 프로그램이 로그인 한 사용자로 프록시 응용 프로그램 API에 액세스 해야 하는 경우 위임 된 **권한**을 선택 합니다.
1. **권한 선택** 제목에서 원하는 사용 권한을 선택 하 고 **권한 추가**를 선택 합니다. 이제 네이티브 응용 프로그램에 대 한 **API 권한** 페이지에 추가한 프록시 응용 프로그램 및 권한 api가 표시 됩니다.

## <a name="step-4-edit-the-active-directory-authentication-library"></a>4단계: Active Directory 인증 라이브러리 편집

Active Directory 인증 라이브러리(ADAL)의 인증 컨텍스트에서 네이티브 애플리케이션 코드를 편집하여 다음 텍스트를 포함합니다.

```
// Acquire Access Token from AAD for Proxy Application
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<Tenant ID>");
AuthenticationResult result = await authContext.AcquireTokenAsync("< External Url of Proxy App >",
        "<App ID of the Native app>",
        new Uri("<Redirect Uri of the Native App>"),
        PromptBehavior.Never);

//Use the Access Token to access the Proxy Application
HttpClient httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");
```

샘플 코드의 필수 정보는 다음과 같이 Azure AD 포털에서 찾을 수 있습니다.

| 필요한 정보 | Azure AD 포털에서이를 찾는 방법 |
| --- | --- |
| \<테 넌 트 ID > | **디렉터리 ID** > **Azure Active Directory** > **속성** |
| 프록시 앱 >의 외부 Url \< | **엔터프라이즈 응용** 프로그램 > *프록시 응용* 프로그램 > **응용 프로그램 프록시** > **외부 Url** |
| 네이티브 앱의 앱 ID \<> | **엔터프라이즈 응용** 프로그램 > *네이티브 응용* 프로그램 > **속성** > **응용 프로그램 ID** |
| 네이티브 앱의 리디렉션 URI를 \<> | *기본 응용 프로그램* > **리디렉션 uri** 를 > **앱 등록** > **Azure Active Directory** |
| \<프록시 앱 API Url > | **Azure Active Directory** >  > 앱 등록 *네이티브 응용 프로그램* > api 사용 권한 > api **사용** 권한 **이름** |

이러한 매개 변수를 사용 하 여 ADAL을 편집한 후에는 사용자가 회사 네트워크 외부에 있는 경우에도 네이티브 클라이언트 응용 프로그램에 인증할 수 있습니다.

## <a name="next-steps"></a>다음 단계

네이티브 응용 프로그램 흐름에 대 한 자세한 내용은 [Azure Active Directory의 네이티브 앱](../azuread-dev/native-app.md)을 참조 하세요.

[Azure Active Directory에서 응용 프로그램에 대 한 Single sign-on을 설정 하](what-is-single-sign-on.md#choosing-a-single-sign-on-method)는 방법에 대해 알아봅니다.
