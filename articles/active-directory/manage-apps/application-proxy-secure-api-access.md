---
title: Azure AD 응용 프로그램 프록시를 사용 하 여 온-프레미스 Api 액세스
description: Azure Active Directory의 응용 프로그램 프록시를 사용 하면 네이티브 앱에서 온-프레미스 또는 클라우드 Vm에서 호스트 하는 Api 및 비즈니스 논리에 안전 하 게 액세스할 수 있습니다.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: ecd5d8bae22d67f8d9f5b99d5c94eecf54a4a1f3
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166002"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용 하 여 온-프레미스 Api에 안전 하 게 액세스

온-프레미스에서 실행 되는 비즈니스 논리 Api가 있거나 클라우드의 가상 컴퓨터에서 호스팅될 수 있습니다. 네이티브 Android, iOS, Mac 또는 Windows 앱은 데이터를 사용 하거나 사용자 조작을 제공 하기 위해 API 끝점과 상호 작용 해야 합니다. Azure AD 응용 프로그램 프록시 및 [ADAL (Azure Active Directory 인증 라이브러리)](/azure/active-directory/develop/active-directory-authentication-libraries) 을 사용 하면 네이티브 앱에서 온-프레미스 api에 안전 하 게 액세스할 수 있습니다. Azure Active Directory 응용 프로그램 프록시는 방화벽 포트를 열고 앱 계층에서 인증 및 권한 부여를 제어 하는 것 보다 더 빠르고 안전한 솔루션입니다. 

이 문서에서는 네이티브 앱이 액세스할 수 있는 web API 서비스를 호스팅하기 위해 Azure AD 응용 프로그램 프록시 솔루션을 설정 하는 과정을 안내 합니다. 

## <a name="overview"></a>개요

다음 다이어그램에서는 온-프레미스 Api를 게시 하는 일반적인 방법을 보여 줍니다. 이 방법을 사용 하려면 들어오는 포트 80 및 443를 열어야 합니다.

![기존 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

다음 다이어그램에서는 들어오는 포트를 열지 않고 Azure AD 응용 프로그램 프록시를 사용 하 여 Api를 안전 하 게 게시 하는 방법을 보여 줍니다.

![Azure AD 응용 프로그램 프록시 API 액세스](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD 응용 프로그램 프록시는 솔루션의 백본을 구성 하 고, API 액세스를 위한 공용 끝점으로 작업 하 고, 인증 및 권한 부여를 제공 합니다. [ADAL](/azure/active-directory/develop/active-directory-authentication-libraries) 라이브러리를 사용 하 여 광범위 한 플랫폼에서 api에 액세스할 수 있습니다. 

Azure AD 응용 프로그램 프록시 인증 및 권한 부여는 Azure AD를 기반으로 구축 되므로 신뢰할 수 있는 장치만 응용 프로그램 프록시를 통해 게시 된 Api에 액세스할 수 있도록 Azure AD 조건부 액세스를 사용할 수 있습니다. Azure AD 조인 또는 데스크톱에 대해 Azure AD 하이브리드 조인 또는 장치에 대 한 Intune 관리를 사용 합니다. Azure Multi-Factor Authentication와 같은 Azure Active Directory Premium 기능과 [Azure Id 보호](/azure/active-directory/active-directory-identityprotection)의 기계 학습 지원 보안을 활용할 수도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 연습을 수행 하려면 다음이 필요 합니다.

- 앱을 만들고 등록할 수 있는 계정을 사용 하 여 Azure 디렉터리에 대 한 관리자 권한
- [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp) 의 샘플 web API 및 native client 앱 

## <a name="publish-the-api-through-application-proxy"></a>응용 프로그램 프록시를 통해 API 게시

응용 프로그램 프록시를 통해 인트라넷 외부에 API를 게시 하려면 웹 앱을 게시 하는 것과 동일한 패턴을 따릅니다. 자세한 내용은 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가 Azure Active Directory](application-proxy-add-on-premises-application.md)를 참조 하세요.

응용 프로그램 프록시를 통해 SecretAPI web API를 게시 하려면:

1. 샘플 SecretAPI 프로젝트를 빌드하고 로컬 컴퓨터 또는 인트라넷에 ASP.NET 웹 앱으로 게시 합니다. 웹 앱에 로컬로 액세스할 수 있는지 확인 합니다. 
   
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory**를 선택합니다. 그런 다음 **엔터프라이즈 응용 프로그램**을 선택 합니다.
   
1. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지의 맨 위에서 **새 응용 프로그램**을 선택 합니다.
   
1. **응용 프로그램 추가** 페이지에서 **온-프레미스 응용 프로그램**을 선택 합니다. **사용자 고유의 온-프레미스 응용 프로그램 추가** 페이지가 나타납니다.
   
1. 응용 프로그램 프록시 커넥터를 설치 하지 않은 경우 설치 하 라는 메시지가 표시 됩니다. **응용 프로그램 프록시 커넥터 다운로드** 를 선택 하 여 커넥터를 다운로드 하 고 설치 합니다. 
   
1. 응용 프로그램 프록시 커넥터를 설치한 후 **에는 고유한 온-프레미스 응용 프로그램 추가** 페이지에서 다음을 수행 합니다.
   
   1. **이름**옆에 *SecretAPI*을 입력 합니다.
      
   1. **내부 url**옆에 인트라넷 내에서 API에 액세스 하는 데 사용할 url을 입력 합니다.
      
   1. **사전 인증이** **Azure Active Directory**으로 설정 되어 있는지 확인 합니다. 
      
   1. 페이지 위쪽에서 **추가** 를 선택 하 고 앱이 만들어질 때까지 기다립니다.
   
   ![API 앱 추가](./media/application-proxy-secure-api-access/3-add-api-app.png)
   
1. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지에서 **SecretAPI** 앱을 선택 합니다. 
   
1. **SecretAPI-개요** 페이지의 왼쪽 탐색 영역에서 **속성** 을 선택 합니다.
   
1. **Myapps** 패널에서 최종 사용자에 게 api를 사용할 수 없게 하려면 **속성** 페이지 맨 아래에 **표시 되는 사용자** 를 **아니요** 로 설정 하 고 **저장**을 선택 합니다.
   
   ![사용자에 게 표시 되지 않음](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)
   
Azure AD 응용 프로그램 프록시를 통해 web API를 게시 했습니다. 이제 앱에 액세스할 수 있는 사용자를 추가 합니다. 

1. **SecretAPI-개요** 페이지의 왼쪽 탐색 영역에서 **사용자 및 그룹** 을 선택 합니다.
   
1. **사용자 및 그룹** 페이지에서 **사용자 추가**를 선택 합니다.  
   
1. **할당 추가** 페이지에서 **사용자 및 그룹**을 선택 합니다. 
   
1. **사용자 및 그룹** 페이지에서 사용자를 포함 하 여 앱에 액세스할 수 있는 사용자를 검색 하 고 선택 합니다. 모든 사용자를 선택한 후 **선택**을 선택 합니다. 
   
   ![사용자 선택 및 할당](./media/application-proxy-secure-api-access/7-select-admin-user.png)
   
1. **할당 추가** 페이지로 돌아가서 **할당**을 선택 합니다. 

> [!NOTE]
> Windows 통합 인증을 사용 하는 Api에는 [추가 단계가](/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd)필요할 수 있습니다.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>네이티브 앱을 등록 하 고 API에 대 한 액세스 권한 부여

네이티브 앱은 특정 플랫폼 또는 장치에서 사용 하도록 개발 된 프로그램입니다. 네이티브 앱에서 API에 연결 하 고 액세스 하려면 Azure AD에 등록 해야 합니다. 다음 단계는 네이티브 앱을 등록 하 고 응용 프로그램 프록시를 통해 게시 한 web API에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다.

AppProxyNativeAppSample 네이티브 앱을 등록 하려면:

1. Azure Active Directory **개요** 페이지에서 **앱 등록**를 선택 하 고 **앱 등록** 창 맨 위에서 **새 등록**을 선택 합니다.
   
1. **응용 프로그램 등록** 페이지에서 다음을 수행 합니다.
   
   1. **이름**아래에 *AppProxyNativeAppSample*을 입력 합니다. 
      
   1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다. 
      
   1. **URL 리디렉션**아래에서 **공용 클라이언트 (모바일 & 데스크톱)** 를 드롭다운 하 고 선택 하 고 *https:\//appproxynativeapp*를 입력 합니다. 
      
   1. **등록**을 선택 하 고 앱이 성공적으로 등록 될 때까지 기다립니다. 
      
      ![새 애플리케이션 등록](./media/application-proxy-secure-api-access/8-create-reg-ga.png)
   
이제 Azure Active Directory에 AppProxyNativeAppSample 앱을 등록 했습니다. 네이티브 앱에 SecretAPI web API에 대 한 액세스 권한을 부여 하려면 다음을 수행 합니다.

1. Azure Active Directory **개요** > **앱 등록** 페이지에서 **AppProxyNativeAppSample** 앱을 선택 합니다. 
   
1. **AppProxyNativeAppSample** 페이지의 왼쪽 탐색 영역에서 **API 사용 권한** 을 선택 합니다. 
   
1. **API 사용 권한** 페이지에서 **사용 권한 추가**를 선택 합니다.
   
1. 첫 번째 **api 권한 요청** 페이지에서 내 조직에서 탭을 **사용 하는 api** 를 선택한 다음 **SecretAPI**를 검색 하 고 선택 합니다. 
   
1. 다음 **API 권한 요청** 페이지에서 **user_impersonation**옆의 확인란을 선택 하 고 **사용 권한 추가**를 선택 합니다. 
   
    ![API 선택](./media/application-proxy-secure-api-access/10-secretapi-added.png)
   
1. **API 사용 권한** 페이지에서 다른 사용자가 앱에 개별적으로 동의할 필요가 없도록 **Contoso에 대 한 관리자 동의 부여** 를 선택할 수 있습니다. 

## <a name="configure-the-native-app-code"></a>네이티브 앱 코드 구성

마지막 단계는 네이티브 앱을 구성 하는 것입니다. NativeClient 샘플 앱에 있는 *Form1.cs* 파일의 다음 코드 조각은 ADAL 라이브러리가 API 호출을 요청 하기 위한 토큰을 획득 하 고 앱 헤더에 전달자로 연결 되도록 합니다. 
   
   ```csharp
       AuthenticationResult result = null;
       HttpClient httpClient = new HttpClient();
       authContext = new AuthenticationContext(authority);
       result = await authContext.AcquireTokenAsync(todoListResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
       
       // Append the token as bearer in the request header.
       httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
       
       // Call the API.
       HttpResponseMessage response = await httpClient.GetAsync(todoListBaseAddress + "/api/values/4");
   
       // MessageBox.Show(response.RequestMessage.ToString());
       string s = await response.Content.ReadAsStringAsync();
       MessageBox.Show(s);
   ```
   
Azure Active Directory에 연결 하 고 API 앱 프록시를 호출 하도록 네이티브 앱을 구성 하려면 NativeClient 샘플 앱의 *app.config* 파일에 있는 자리 표시자 값을 Azure AD의 값으로 업데이트 합니다. 

- `<add key="ida:Tenant" value="" />` 필드에 **디렉터리 (테 넌 트) ID** 를 붙여 넣습니다. 앱의 **개요** 페이지에서이 값 (GUID)을 찾아서 복사할 수 있습니다. 
  
- `<add key="ida:ClientId" value="" />` 필드에 AppProxyNativeAppSample **응용 프로그램 (클라이언트) ID** 를 붙여 넣습니다. AppProxyNativeAppSample **개요** 페이지에서이 값 (GUID)을 찾아서 복사할 수 있습니다.
  
- AppProxyNativeAppSample **리디렉션 URI** 를 `<add key="ida:RedirectUri" value="" />` 필드에 붙여넣습니다. AppProxyNativeAppSample **인증** 페이지에서이 값 (URI)을 찾아서 복사할 수 있습니다. 
  
- SecretAPI **응용 프로그램 ID URI** 를 `<add key="todo:TodoListResourceId" value="" />` 필드에 붙여넣습니다. 이 값 (URI)은 SecretAPI **노출 API** 페이지에서 찾아서 복사할 수 있습니다.
  
- SecretAPI **홈 페이지 URL** 을 `<add key="todo:TodoListBaseAddress" value="" />` 필드에 붙여넣습니다. SecretAPI **브랜딩** 페이지에서이 값 (URL)을 찾아서 복사할 수 있습니다.

매개 변수를 구성한 후 네이티브 앱을 빌드하고 실행 합니다. **로그인** 단추를 선택 하면 앱에서 로그인 한 다음 성공 화면을 표시 하 여 SecretAPI에 성공적으로 연결 되었는지 확인할 수 있습니다.

![Success](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Active Directory에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md)
- [빠른 시작: 웹 Api에 액세스 하도록 클라이언트 응용 프로그램 구성](../develop/quickstart-configure-app-access-web-apis.md)
- [네이티브 클라이언트 응용 프로그램을 사용 하 여 프록시 응용 프로그램과 상호 작용 하는 방법](application-proxy-configure-native-client-application.md)
