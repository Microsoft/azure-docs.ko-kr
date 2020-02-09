---
title: MSAL (클라이언트 응용 프로그램 구성) | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL (Microsoft 인증 라이브러리)을 사용 하는 공용 클라이언트 및 기밀 클라이언트 응용 프로그램에 대 한 구성 옵션에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0262d22ae00456ce06cb8efbf995f1a093b20043
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085014"
---
# <a name="application-configuration-options"></a>응용 프로그램 구성 옵션

코드에서 토큰을 인증 하 고 획득 하기 위해 새 공용 또는 비밀 클라이언트 응용 프로그램 (또는 MSAL의 사용자 에이전트)을 초기화 합니다. MSAL (Microsoft Authentication Library)에서 클라이언트 앱을 초기화할 때 다양 한 구성 옵션을 설정할 수 있습니다. 이러한 옵션은 다음 두 그룹으로 분류 됩니다.

- 등록 옵션 (다음 포함)
    - [인증 기관](#authority) (id 공급자 [인스턴스와](#cloud-instance) 앱에 대 한 로그인 [대상 그룹](#application-audience) 및 테넌트 ID로 구성).
    - [클라이언트 ID](#client-id)입니다.
    - [리디렉션 URI](#redirect-uri)입니다.
    - [클라이언트 암호](#client-secret) (기밀 클라이언트 응용 프로그램의 경우).
- 로그 수준, 개인 데이터 제어 및 라이브러리를 사용 하는 구성 요소 이름 등의 [로깅 옵션](#logging)입니다.

## <a name="authority"></a>Authority

Authority는 MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 일반적인 기관은 다음과 같습니다.

- https\://login.microsoftonline.com/\<테넌트\>/, &lt;여기서 테넌트&gt;은이 Azure ad (Azure AD) 테넌트의 테넌트 ID 또는이 Azure AD 테넌트와 연결 된 도메인입니다. 특정 조직의 사용자를 로그인 하는 데만 사용 됩니다.
- https\://login.microsoftonline.com/common/. 회사 및 학교 계정이 나 개인 Microsoft 계정으로 사용자를 로그인 하는 데 사용 됩니다.
- https\://login.microsoftonline.com/organizations/. 회사 및 학교 계정으로 사용자를 로그인 하는 데 사용 됩니다.
- https\://login.microsoftonline.com/consumers/. 개인 Microsoft 계정 (이전의 Windows Live ID 계정)만 사용 하 여 사용자를 로그인 하는 데 사용 됩니다.

인증 기관 설정은 응용 프로그램 등록 포털에 선언 된 것과 일치 해야 합니다.

인증 기관 URL은 인스턴스 및 대상 그룹으로 구성 됩니다.

기관은 다음과 같을 수 있습니다.
- Azure AD 클라우드 기관.
- Azure AD B2C 권한입니다. [B2C 세부](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics)정보를 참조 하세요.
- Active Directory Federation Services (AD FS) 기관입니다. [AD FS 지원](https://aka.ms/msal-net-adfs-support)을 참조 하세요.

Azure AD 클라우드 기관에는 두 가지 부분이 있습니다.
- Id 공급자 *인스턴스*
- 앱에 대 한 로그인 *대상*

인스턴스 및 대상 그룹을 연결 하 여 기관 URL로 제공할 수 있습니다. MSAL 3 이전 버전의 MSAL.NET. *x*, 대상으로 하는 클라우드 및 로그인 대상 그룹을 기반으로 권한을 직접 구성 해야 했습니다.  이 다이어그램에서는 기관 URL을 구성 하는 방법을 보여 줍니다.

![기관 URL을 구성 하는 방법](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>클라우드 인스턴스

*인스턴스* 는 앱이 Azure 공용 클라우드 또는 국가별 클라우드에서 사용자를 서명 하 고 있는지 여부를 지정 하는 데 사용 됩니다. 코드에서 MSAL을 사용 하 여 열거형을 사용 하거나 URL을 [국가 클라우드 인스턴스에](authentication-national-cloud.md#azure-ad-authentication-endpoints) `Instance` 멤버로 전달 하 여 Azure 클라우드 인스턴스를 설정할 수 있습니다 (알고 있는 경우).

`Instance`와 `AzureCloudInstance`를 모두 지정 하면 MSAL.NET에서 명시적 예외를 throw 합니다.

인스턴스를 지정 하지 않으면 앱은 Azure 공용 클라우드 인스턴스 (URL `https://login.onmicrosoftonline.com`의 인스턴스)를 대상으로 합니다.

## <a name="application-audience"></a>응용 프로그램 대상

로그인 대상은 앱에 대 한 비즈니스 요구 사항에 따라 달라 집니다.
- LOB (기간 업무) 개발자 인 경우 조직 에서만 사용 되는 단일 테넌트 응용 프로그램을 생성할 것입니다. 이 경우 테넌트 ID (Azure AD 인스턴스의 ID) 또는 Azure AD 인스턴스와 연결 된 도메인 이름으로 조직을 지정 해야 합니다.
- ISV 인 경우 모든 조직 또는 일부 조직 (다중 테넌트 앱)에서 회사 및 학교 계정으로 사용자를 로그인 할 수 있습니다. 그러나 사용자에 게 개인 Microsoft 계정으로 로그인 할 수도 있습니다.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>코드/구성에서 대상 그룹을 지정 하는 방법

코드에서 MSAL을 사용 하 여 다음 값 중 하나를 사용 하 여 대상 그룹을 지정 합니다.
- Azure AD 기관 대상 그룹 열거
- 테넌트 ID는 다음과 같을 수 있습니다.
  - 단일 테넌트 응용 프로그램에 대 한 GUID (Azure AD 인스턴스의 ID)
  - Azure AD 인스턴스와 연결 된 도메인 이름 (단일 테넌트 응용 프로그램에도 해당)
- 다음 자리 표시자 중 하나는 Azure AD 기관 대상 그룹 열거 대신 테넌트 ID입니다.
    - 다중 테넌트 응용 프로그램에 대 한 `organizations`
    - 개인 계정으로 사용자 로그인 `consumers`
    - 회사 및 학교 계정이 나 개인 Microsoft 계정으로 사용자를 로그인 `common`

Azure AD 기관 대상과 테넌트 ID를 모두 지정 하면 MSAL에서 의미 있는 예외를 throw 합니다.

대상 그룹을 지정 하지 않으면 앱은 Azure AD 및 개인 Microsoft 계정을 대상으로 지정 합니다. 즉 `common`가 지정 된 것 처럼 동작 합니다.

### <a name="effective-audience"></a>유효한 대상

응용 프로그램에 대 한 효과적인 대상은 앱에서 설정 하는 대상 그룹과 앱 등록에 지정 된 대상에 대 한 최소 (교집합)가 됩니다. 실제로 [앱 등록](https://aka.ms/appregistrations) 환경에서는 앱에 대 한 대상 (지원 되는 계정 유형)을 지정할 수 있습니다. 자세한 내용은 [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](quickstart-register-app.md)을 참조 하세요.

현재 개인 Microsoft 계정으로 사용자를 로그인 하는 앱을 가져오는 유일한 방법은 다음 설정을 모두 구성 하는 것입니다.
- 앱 등록 대상 그룹을 `Work and school accounts and personal accounts`로 설정 합니다.
- 코드/구성의 대상 그룹을 `AadAuthorityAudience.PersonalMicrosoftAccount` (또는 `TenantID` = "소비자")로 설정 합니다.

## <a name="client-id"></a>클라이언트 ID

클라이언트 ID는 앱이 등록 될 때 Azure AD에서 앱에 할당 한 고유 응용 프로그램 (클라이언트) ID입니다.

## <a name="redirect-uri"></a>리디렉션 URI

리디렉션 URI는 id 공급자가 보안 토큰을 다시 보낼 URI입니다.

### <a name="redirect-uri-for-public-client-apps"></a>공용 클라이언트 앱에 대 한 리디렉션 URI

MSAL을 사용 하는 공용 클라이언트 앱 개발자 인 경우:
- 데스크톱 또는 UWP 응용 프로그램에서 `.WithDefaultRedirectUri()`를 사용 하려고 합니다 (MSAL.NET 4.1 이상). 이 메서드는 공용 클라이언트 응용 프로그램의 리디렉션 uri 속성을 공용 클라이언트 응용 프로그램에 대 한 기본 권장 리디렉션 uri로 설정 합니다. 

  플랫폼  | 리디렉션 URI  
  ---------  | --------------
  데스크톱 앱 (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`값입니다. 이렇게 하면 등록 해야 하는 WebAuthenticationBroker. GetCurrentApplicationCallbackUri ()의 결과로 값을 설정 하 여 브라우저에서 SSO를 사용할 수 있습니다.
  .NET Core | `https://localhost`입니다. 이를 통해 .NET Core는 현재 포함 된 웹 보기에 대 한 UI를 포함 하지 않으므로 대화형 인증에 시스템 브라우저를 사용할 수 있습니다.

- Broker를 지원 하지 않는 Xamarin Android 및 iOS 응용 프로그램을 빌드하는 경우 리디렉션 URI를 추가할 필요가 없습니다. (리디렉션 URI는 Xamarin Android 및 iOS에 대 한 `msal{ClientId}://auth` 자동으로 설정 됩니다.

- [앱 등록](https://aka.ms/appregistrations)에서 리디렉션 URI를 구성 해야 합니다.

   ![앱 등록의 리디렉션 URI](media/msal-client-application-configuration/redirect-uri.png)

`RedirectUri` 속성을 사용 하 여 리디렉션 URI를 재정의할 수 있습니다 (예: broker를 사용 하는 경우). 다음은 해당 시나리오에 대 한 리디렉션 Uri의 몇 가지 예입니다.

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $ "msauth. {번들 ID}:/인증 ";

IOS에 대 한 자세한 내용은 [Microsoft Authenticator를 사용 하는 ios 응용 프로그램을 ADAL.NET에서 MSAL.NET로 마이그레이션](msal-net-migration-ios-broker.md) 및 [Ios에서 broker 활용](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)을 참조 하세요.
Android에 대 한 추가 정보는 [android에서](brokered-auth.md)조정 된 인증을 참조 하세요.

### <a name="redirect-uri-for-confidential-client-apps"></a>기밀 클라이언트 앱에 대 한 리디렉션 URI

웹 앱의 경우 리디렉션 URI (또는 회신 URI)는 Azure AD가 응용 프로그램에 토큰을 다시 보내는 데 사용 하는 URI입니다. 이 URI는 기밀 앱이 다음 중 하나인 경우 웹 앱/웹 API의 URL 일 수 있습니다. 리디렉션 URI는 앱 등록에 등록 해야 합니다. 이 등록은 처음에 로컬로 테스트 한 앱을 배포할 때 특히 중요 합니다. 그런 다음 응용 프로그램 등록 포털에서 배포 된 앱의 회신 URL을 추가 해야 합니다.

디먼 앱의 경우 리디렉션 URI를 지정할 필요가 없습니다.

## <a name="client-secret"></a>클라이언트 암호

이 옵션은 기밀 클라이언트 앱에 대 한 클라이언트 암호를 지정 합니다. 이 비밀 (앱 암호)은 응용 프로그램 등록 포털에서 제공 되거나 PowerShell AzureAD, PowerShell AzureRM 또는 Azure CLI를 사용 하 여 앱을 등록 하는 동안 Azure AD에 제공 됩니다.

## <a name="logging"></a>로깅

다른 구성 옵션은 로깅 및 문제 해결을 가능 하 게 합니다. 사용 방법에 대 한 자세한 내용은 [로깅](msal-logging.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

MSAL.NET를 [사용 하 여 클라이언트 응용 프로그램을 인스턴스화하는](msal-net-initializing-client-applications.md)방법에 대해 알아봅니다.
[MSAL를 사용 하 여 클라이언트 응용 프로그램을 인스턴스화하는](msal-js-initializing-client-applications.md)방법에 대해 알아봅니다.
