---
title: Android 응용 프로그램에서 토큰 획득
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 AppAuth를 사용 하 여 사용자 id를 관리 하 고 사용자를 인증 하는 Android 앱을 만드는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa8731329c141ab1390510180233ff93e606ef94
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848409"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 Android 애플리케이션을 사용하여 로그인

Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 이러한 표준을 사용하면 Azure Active Directory B2C와 통합하려는 라이브러리를 활용할 수 있습니다. 다른 라이브러리를 사용할 수 있으려면 타사 라이브러리를 Microsoft ID 플랫폼에 연결하도록 구성하는 방법을 설명하는 이와 같은 연습을 사용할 수 있습니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749)을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.

> [!WARNING]
> Microsoft는 타사 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 이 샘플은 기본 시나리오에서 Azure AD B2C와의 호환성이 테스트된 AppAuth라는 타사 라이브러리를 사용합니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)를 참조하세요.
>
>

OAuth2 또는 OpenID Connect를 처음 접하는 경우 이 샘플 구성 대부분이 잘 이해되지 않을 수도 있습니다. [여기서 추천한 프로토콜에 관한 개요](protocols-overview.md)를 간략히 살펴볼 것을 추천합니다.

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 계속하기 전에 [B2C 디렉터리를 만듭니다](tutorial-create-tenant.md) .

## <a name="create-an-application"></a>애플리케이션 만들기

다음으로 Azure AD B2C 테넌트에 응용 프로그램을 등록 합니다. 이를 통해 Azure AD는 앱과 안전 하 게 통신 하는 데 필요한 정보를 제공 합니다.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

또한 이후 단계에서 사용할 사용자 지정 리디렉션 URI를 기록 합니다. `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`)을 입력합니다.

## <a name="create-your-user-flows"></a>사용자 흐름 만들기

Azure AD B2C에서 모든 사용자 환경은 [사용자 흐름](user-flow-overview.md)에서 정의됩니다. 이는 Azure AD의 동작을 제어하는 정책의 세트입니다. 이 애플리케이션에는 로그인 및 등록 사용자 흐름이 필요합니다. 사용자 흐름을 만들 때 다음을 확인합니다.

* 사용자 흐름에서 **표시 이름**을 등록 특성으로 선택합니다.
* 모든 사용자 흐름에서 **표시 이름** 및 **개체 ID** 애플리케이션 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
* 각 사용자 흐름을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다.  사용자 흐름 이름이 나중에 필요합니다.

사용자 흐름을 만들었다면 앱을 빌드할 준비가 되었습니다.

## <a name="download-the-sample-code"></a>샘플 코드 다운로드

[GitHub에서](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) Azure AD B2C와 함께 AppAuth를 사용하는 작업 샘플이 제공됩니다. 코드를 다운로드하고 실행할 수 있습니다. [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)의 지침에 따라 사용자 고유의 Azure AD B2C 구성을 사용하는 자신만의 앱으로 빠르게 시작할 수 있습니다.

샘플은 [AppAuth](https://openid.github.io/AppAuth-Android/)에서 제공되는 샘플의 수정본입니다. AppAuth 및 기능에 대한 자세한 정보는 해당 페이지를 방문해 보세요.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>AppAuth와 함께 Azure AD B2C를 사용하도록 앱 수정

> [!NOTE]
> AppAuth는 Android API 16(Jellybean) 이상을 지원합니다. API 23 이상을 사용하는 것이 좋습니다.
>

### <a name="configuration"></a>구성

검색 URI를 지정하거나 권한 부여 엔드포인트와 토큰 엔드포인트 URI를 모두 지정하여 Azure AD B2C와의 통신을 구성할 수 있습니다. 두 경우 모두 다음 정보를 제공해야 합니다.

* 테넌트 ID(예: contoso.onmicrosoft.com)
* 사용자 흐름 이름(예: B2C\_1\_SignUpIn)

권한 부여 및 토큰 엔드포인트 URI를 자동으로 검색하려는 경우 검색 URI에서 정보를 가져와야 합니다. 검색 URI는 다음 URL에서 테넌트\_ID 및 정책\_이름을 바꿔서 만들 수 있습니다.

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

다음을 실행하면 권한 부여 및 토큰 엔드포인트 URI를 획득하고 AuthorizationServiceConfiguration 개체를 만들 수 있습니다.

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

권한 부여 및 토큰 엔드포인트 URI를 가져오기 위해 검색을 사용하는 대신 아래 URL에서 테넌트\_ID 및 정책\_이름을 바꾸면 명시적으로 지정할 수 있습니다.

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

다음 코드를 실행하여 AuthorizationServiceConfiguration 개체를 만듭니다.

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>권한 부여

권한 부여 서비스 구성을 구성하거나 검색하면 권한 부여 요청을 생성할 수 있습니다. 요청을 만들려면 다음 정보가 필요합니다.

* 이전에 기록한 클라이언트 ID (응용 프로그램 ID)입니다. `00000000-0000-0000-0000-000000000000`)을 입력합니다.
* 이전에 기록한 사용자 지정 리디렉션 URI입니다. `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`)을 입력합니다.

두 항목 모두 [앱을 등록](#create-an-application)할 때 저장해야 합니다.

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

프로세스의 나머지 단계를 완료하는 방법은 [AppAuth 가이드](https://openid.github.io/AppAuth-Android/)를 참조하세요. 작업 중인 앱으로 빠르게 시작해야 하는 경우에는 [샘플](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)을 확인하세요. [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md)에 나온 단계에 따라 사용자 고유의 Azure AD B2C 구성을 입력합니다.
