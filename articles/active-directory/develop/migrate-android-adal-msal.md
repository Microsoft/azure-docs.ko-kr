---
title: Android 용 ADAL-MSAL 마이그레이션 가이드 | Microsoft
description: ADAL (Azure Active Directory Authentication Library) Android 앱을 MSAL (Microsoft Authentication Library)로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084062"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Android 용 ADAL-MSAL 마이그레이션 가이드

이 문서에서는 MSAL (Microsoft Authentication Library)을 사용 하도록 ADAL (Azure Active Directory Authentication Library)을 사용 하는 앱을 마이그레이션하기 위해 변경 해야 하는 사항을 강조 합니다.

## <a name="difference-highlights"></a>차이점 강조

ADAL은 Azure Active Directory v 1.0 엔드포인트에서 작동 합니다. MSAL (Microsoft 인증 라이브러리)은 이전에 Azure Active Directory v2.0 엔드포인트 이라고 하는 Microsoft id 플랫폼에서 작동 합니다. Microsoft id 플랫폼은 다음과 같은 Azure Active Directory v 1.0과 다릅니다.

지원:
  - Azure Active Directory (조직 Id)
  - Outlook.com, Xbox Live 등의 조직 이외의 id
  - (B2C에만 해당) Google, Facebook, Twitter 및 Amazon를 사용 하는 페더레이션된 로그인

- 는와 호환 되는 표준입니다.
  - OAuth v2.0
  - OIDC (Openid connect Connect)

MSAL 공용 API는 다음과 같은 중요 한 변경 사항을 소개 합니다.

- 토큰 액세스를 위한 새 모델:
  - ADAL은 서버를 나타내는 `AuthenticationContext`를 통해 토큰에 대 한 액세스를 제공 합니다. MSAL은 클라이언트를 나타내는 `PublicClientApplication`를 통해 토큰에 대 한 액세스를 제공 합니다. 클라이언트 개발자는와 상호 작용 해야 하는 모든 기관에 대해 새 `PublicClientApplication` 인스턴스를 만들 필요가 없습니다. `PublicClientApplication` 구성은 하나만 필요 합니다.
  - 리소스 식별자 외에도 범위를 사용 하 여 액세스 토큰을 요청 하도록 지원 합니다.
  - 증분 동의 지원. 개발자는 앱 등록 중에 포함 되지 않은 기능을 포함 하 여 사용자가 앱의 더 많은 기능에 액세스할 때 범위를 요청할 수 있습니다.
  - 런타임에는 더 이상 인증 기관의 유효성이 검사 되지 않습니다. 대신 개발자는 개발 중에 ' 알려진 인증 기관 ' 목록을 선언 합니다.
- 토큰 API 변경:
  - ADAL에서 `AcquireToken()`는 먼저 자동 요청을 수행 합니다. 실패 하면 대화형 요청이 발생 합니다. 이 동작으로 인해 일부 개발자가 `AcquireToken`에만 의존 하 여 때때로 사용자가 예기치 않게 자격 증명을 입력 하 라는 메시지가 표시 됩니다. MSAL을 사용 하려면 사용자에 게 UI 프롬프트가 표시 될 때 개발자가 의도적인 것을 요구 합니다.
    - `AcquireTokenSilent` 항상 성공 또는 실패 하는 자동 요청이 발생 합니다.
    - `AcquireToken` 항상 UI를 통해 사용자에 게 메시지를 표시 하는 요청이 발생 합니다.
- MSAL은 기본 브라우저 또는 포함 된 웹 보기에서 로그인을 지원 합니다.
  - 기본적으로 장치의 기본 브라우저가 사용 됩니다. 이를 통해 MSAL은 하나 이상의 로그인 된 계정에 이미 있을 수 있는 인증 상태 (쿠키)를 사용할 수 있습니다. 인증 상태가 없는 경우 MSAL을 통해 권한 부여 중에 인증 하면 동일한 브라우저에서 사용 되는 다른 웹 응용 프로그램의 혜택에 대 한 인증 상태 (쿠키)가 생성 됩니다.
- 새 예외 모델:
  - 예외는 발생 한 오류의 유형과 개발자가 문제를 해결 하기 위해 수행 해야 하는 작업을 더 명확 하 게 정의 합니다.
- MSAL은 `AcquireToken` 및 `AcquireTokenSilent` 호출에 대 한 매개 변수 개체를 지원 합니다.
- MSAL은 다음에 대 한 선언적 구성을 지원 합니다.
  - 클라이언트 ID, 리디렉션 URI입니다.
  - 포함 된 vs 기본 브라우저
  - 권한
  - 읽기 및 연결 시간 제한과 같은 HTTP 설정

## <a name="your-app-registration-and-migration-to-msal"></a>앱 등록 및 MSAL으로 마이그레이션

MSAL을 사용 하도록 기존 앱 등록을 변경할 필요가 없습니다. 증분/프로그레시브 동의를 활용 하려면 등록을 검토 하 여 증분 방식으로 요청 하려는 특정 범위를 확인 해야 할 수 있습니다. 범위 및 증분 동의에 대 한 자세한 내용은 다음과 같습니다.

포털의 앱 등록에 **API 사용 권한** 탭이 표시 됩니다. 그러면 앱이 현재 액세스를 요청 하도록 구성 된 Api 및 사용 권한 (범위) 목록이 제공 됩니다. 또한 각 API 권한과 연결 된 범위 이름 목록을 표시 합니다.

### <a name="user-consent"></a>사용자 승인

ADAL 및 AAD v1 엔드포인트을 사용 하는 경우 자신이 소유한 리소스에 대 한 사용자 동의가 처음 사용 시 부여 되었습니다. MSAL 및 Microsoft id 플랫폼을 사용 하는 경우에는 동의가 점진적으로 요청 될 수 있습니다. 증분 동의는 사용자가 높은 권한을 고려할 수 있는 권한 또는 권한이 필요한 이유를 명확 하 게 설명 하는 경우에는 그렇지 않을 수 있는 경우에 유용 합니다. ADAL에서 이러한 사용 권한은 사용자가 앱에 로그인 하는 것을 중단 한 것일 수 있습니다.

> [!TIP]
> 앱에 권한이 필요한 이유에 대 한 추가 컨텍스트를 사용자에 게 제공 해야 하는 시나리오에서는 증분 동의를 사용 하는 것이 좋습니다.

### <a name="admin-consent"></a>관리자 동의

조직 관리자는 조직의 모든 구성원을 대신 하 여 응용 프로그램에 필요한 사용 권한에 동의할 수 있습니다. 일부 조직에서는 관리자만 응용 프로그램에 동의할 수 있습니다. 관리자 동의를 사용 하려면 앱 등록에서 응용 프로그램에 사용 되는 모든 API 권한 및 범위를 포함 해야 합니다.

> [!TIP]
> 앱 등록에 포함 되지 않은 항목에 대해 MSAL을 사용 하 여 범위를 요청 하는 경우에도 앱 등록을 업데이트 하 여 사용자가 권한을 부여할 수 있는 모든 리소스 및 범위를 포함 하는 것이 좋습니다.

## <a name="migrating-from-resource-ids-to-scopes"></a>리소스 Id에서 범위로 마이그레이션

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>처음 사용할 때 모든 권한에 대 한 권한 부여 인증 및 요청

현재 ADAL을 사용 하 고 있고 증분 동의를 사용할 필요가 없는 경우 MSAL을 사용 하 여 시작 하는 가장 간단한 방법은 새 `AcquireTokenParameter` 개체를 사용 하 여 `acquireToken` 요청 하 고 리소스 ID 값을 설정 하는 것입니다.

> [!CAUTION]
> 범위와 리소스 id를 모두 설정할 수는 없습니다. 둘 다 설정 하려고 하면 `IllegalArgumentException`발생 합니다.

 이렇게 하면 사용 되는 것과 동일한 v1 동작이 발생 합니다. 앱 등록에서 요청 된 모든 권한은 첫 번째 상호 작용 중에 사용자가 요청 합니다.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>필요한 경우에만 권한 인증 및 요청

증분 동의를 활용 하려면 앱 등록에서 앱이 사용 하는 사용 권한 (범위) 목록을 확인 하 고 다음을 기반으로 하 여 두 개의 목록으로 구성 합니다.

- 로그인 하는 동안 사용자가 앱과 상호 작용 하는 동안 요청 하려는 범위입니다.
- 사용자에 게 설명 해야 하는 앱의 중요 한 기능과 관련 된 권한입니다.

범위를 구성한 후에는 토큰을 요청 하는 데 사용할 리소스 (API) 별로 각 목록을 구성 합니다. 사용자가 동시에 권한을 부여 하려는 다른 모든 범위

MSAL에 대 한 요청을 수행 하는 데 사용 되는 parameters 개체는 다음을 지원 합니다.

- `Scope`:에 대 한 권한 부여를 요청 하 고 액세스 토큰을 수신 하려는 범위 목록입니다.
- `ExtraScopesToConsent`: 다른 리소스에 대 한 액세스 토큰을 요청 하는 동안 권한 부여를 요청 하려는 추가 범위 목록입니다. 이 범위 목록을 사용 하면 사용자 권한 부여를 요청 해야 하는 횟수를 최소화할 수 있습니다. 즉, 사용자 권한 부여 또는 동의 프롬프트가 줄어듭니다.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>AuthenticationContext에서 PublicClientApplications로 마이그레이션

### <a name="constructing-publicclientapplication"></a>PublicClientApplication 생성

MSAL을 사용 하는 경우 `PublicClientApplication`를 인스턴스화합니다. 이 개체는 앱 id를 모델링 하 고 하나 이상의 기관에 요청을 수행 하는 데 사용 됩니다. 이 개체를 사용 하 여 클라이언트 id, 리디렉션 URI, 기본 인증, 장치 브라우저 사용 여부, 로그 수준 등을 구성 합니다.

JSON을 사용 하 여이 개체를 선언적으로 구성할 수 있습니다 .이 개체는 APK 내의 리소스로 파일 또는 저장소로 제공 합니다.

이 개체는 단일 항목이 아니지만 내부적으로 대화형 및 자동 요청에 대해 shared `Executors`를 사용 합니다.

### <a name="business-to-business"></a>비즈니스 간

ADAL에서는에서 액세스 토큰을 요청 하는 모든 조직에 `AuthenticationContext`별도의 인스턴스가 필요 합니다. MSAL에서는 더 이상 필요 하지 않습니다. 자동 또는 대화형 요청의 일부로 토큰을 요청 하려는 기관을 지정할 수 있습니다.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>기관 유효성 검사에서 알려진 권한으로 마이그레이션

MSAL은 권한 유효성 검사를 사용 하거나 사용 하지 않도록 설정 하는 플래그를 포함 하지 않습니다. 인증 기관 유효성 검사는 ADAL의 기능으로, MSAL의 초기 릴리스에서는 코드가 잠재적으로 악의적인 기관에서 토큰을 요청 하는 것을 방지 합니다. 이제 MSAL은 Microsoft에 알려진 인증 기관 목록을 검색 하 고 해당 목록을 구성에 지정 된 기관과 병합 합니다.

> [!TIP]
> Azure B2C (Business to Consumer) 사용자 인 경우 더 이상 기관 유효성 검사를 사용 하지 않도록 설정할 필요가 없습니다. 대신, 지원 되는 각 Azure AD B2C 정책을 MSAL 구성의 기관으로 포함 합니다.

Microsoft에 알려지지 않은 기관을 사용 하려고 시도 하 고 구성에 포함 되지 않은 경우 `UnknownAuthorityException`을 받게 됩니다.

### <a name="logging"></a>로깅
이제 다음과 같이 구성의 일부로 선언적으로 로깅을 구성할 수 있습니다.

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>UserInfo에서 계정으로 마이그레이션

ADAL에서 `AuthenticationResult`는 인증 된 계정에 대 한 정보를 검색 하는 데 사용 되는 `UserInfo` 개체를 제공 합니다. 사람이 나 소프트웨어 에이전트를 의미 하는 "사용자" 라는 용어는 여러 계정이 포함 된 단일 사용자 (사용자 또는 소프트웨어 에이전트의 경우)를 지원 한다는 것을 알리는 방식으로 적용 되었습니다.

은행 계좌를 고려 합니다. 둘 이상의 금융 기관에 계정이 둘 이상 있을 수 있습니다. 계정을 열면 사용자 (사용자)에 게 각 계정에 대 한 잔액, 청구서 지불 등에 액세스 하는 데 사용 되는 ATM 카드 & PIN과 같은 자격 증명이 발급 됩니다. 이러한 자격 증명은 해당 자격 증명을 발급 한 금융 기관 에서만 사용할 수 있습니다.

금융 기관의 계정 처럼 Microsoft id 플랫폼의 계정은 자격 증명을 사용 하 여 액세스 됩니다. 이러한 자격 증명은 Microsoft에서 등록 되거나 발급 됩니다. 또는 Microsoft에서 조직을 대신 하 여.

Microsoft id 플랫폼은 금융 기관과 차이가 있는 경우 Microsoft id 플랫폼은 사용자가 한 계정 및 연결 된 자격 증명을 사용 하 여에 속한 리소스에 액세스할 수 있도록 하는 프레임 워크를 제공 합니다. 여러 개인 및 조직 이는 한 은행에서 발행 한 카드를 다른 금융 기관에서 사용할 수 있는 것과 같습니다. 이는 문제의 모든 조직이 Microsoft id 플랫폼을 사용 하 여 여러 조직에서 하나의 계정을 사용할 수 있기 때문에 작동 합니다. 예를 들면 다음과 같습니다.

Sam은 Contoso.com에 대해 작동 하지만 Fabrikam.com에 속한 Azure virtual machines를 관리 합니다. Sam의 가상 컴퓨터를 관리 하려면 해당 컴퓨터에 액세스할 수 있는 권한이 있어야 합니다. Fabrikam.com에 Sam 계정을 추가 하 고 해당 계정에 가상 컴퓨터를 사용할 수 있도록 허용 하는 역할을 부여 하 여이 액세스 권한을 부여할 수 있습니다. 이 작업은 Azure Portal를 통해 수행 됩니다.

Sam의 Contoso.com 계정을 Fabrikam.com의 멤버로 추가 하면 Sam에 대 한 Fabrikam의 Azure Active Directory에 새 레코드가 생성 됩니다. Azure Active Directory의 Sam 레코드를 사용자 개체 라고 합니다. 이 경우 해당 사용자 개체가 Contoso.com의 Sam 사용자 개체를 다시 가리킵니다. Sam의 Fabrikam 사용자 개체는 Sam의 로컬 표현이 며 Fabrikam.com의 컨텍스트에서 Sam과 연결 된 계정에 대 한 정보를 저장 하는 데 사용 됩니다. Contoso.com의 Sam 제목은 수석 DevOps 컨설턴트입니다. Fabrikam에서 Sam 제목은 계약자-Virtual Machines입니다. Contoso.com에서 Sam은 가상 컴퓨터를 관리 하는 것을 담당 하거나 권한을 부여 받지 않습니다. Fabrikam.com에서 유일한 작업 함수입니다. 아직 Sam에는 Contoso.com에서 발급 한 자격 증명을 추적 하는 자격 증명 집합만 하나 있습니다.

`acquireToken` 호출이 성공적으로 수행 되 면 나중에 `acquireTokenSilent` 요청에서 사용할 수 있는 `IAccount` 개체에 대 한 참조가 표시 됩니다.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

계정이 표시 되는 각 테넌트의 계정에 대 한 클레임에 액세스 하는 앱이 있는 경우 `IAccount` 개체를 `IMultiTenantAccount`으로 캐스팅할 수 있습니다. 이 인터페이스는 테넌트 ID로 키가 지정 된 `ITenantProfiles`의 맵을 제공 하 여 현재 계정에 상대적인 토큰을 요청한 각 테넌트의 계정에 속하는 클레임에 액세스할 수 있습니다.

`IAccount`의 루트에 있는 클레임 및 `IMultiTenantAccount`는 항상 홈 테넌트의 클레임을 포함 합니다. 홈 테넌트 내에서 토큰에 대 한 요청을 아직 수행 하지 않은 경우이 컬렉션은 비어 있습니다.

## <a name="other-changes"></a>기타 변경 내용

### <a name="use-the-new-authenticationcallback"></a>새 AuthenticationCallback 사용

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>새 예외로 마이그레이션

ADAL에는 `ADALError` 열거형 값을 검색 하는 메서드를 포함 하는 `AuthenticationException`한 가지 예외 형식이 있습니다.
MSAL에는 예외 계층이 있으며 각 계층에는 관련 된 특정 오류 코드 집합이 있습니다.

MSAL 예외 목록

|예외  | Description  |
|---------|---------|
| `MsalException`     | MSAL에서 throw 된 기본 확인 된 예외입니다.  |
| `MsalClientException`     | 오류가 클라이언트 쪽 인 경우 throw 됩니다. |
| `MsalArgumentException`     | 하나 이상의 입력 인수가 잘못 된 경우 throw 됩니다. |
| `MsalClientException`     | 오류가 클라이언트 쪽 인 경우 throw 됩니다. |
| `MsalServiceException`     | 오류가 서버 쪽 인 경우 throw 됩니다. |
| `MsalUserCancelException`     | 사용자가 인증 흐름을 취소 한 경우 throw 됩니다.  |
| `MsalUiRequiredException`     | 토큰을 자동으로 새로 고칠 수 없는 경우 throw 됩니다.  |
| `MsalDeclinedScopeException`     | 서버에서 하나 이상의 요청 된 범위를 거부 한 경우 throw 됩니다.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | 리소스에서 MAMCA 보호 정책을 사용 하도록 설정한 경우 throw 됩니다. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError에서 MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>MSAL 로깅에 ADAL 로깅

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
