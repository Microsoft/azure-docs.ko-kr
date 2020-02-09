---
title: IOS 응용 프로그램에서 AppAuth 사용
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 AppAuth를 사용 하 여 사용자 id를 관리 하 고 사용자를 인증 하는 iOS 앱을 만드는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5433867b0676ffa644160251c9aacd81ba04dda7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848357"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: iOS 애플리케이션을 사용하여 로그인

Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 개방형 표준 프로토콜을 사용하면 서비스와 통합할 라이브러리를 선택할 때 더 많은 개발자 선택 옵션이 제공됩니다. 개발자가 Microsoft ID 플랫폼에 연결되는 애플리케이션을 원활히 작성할 수 있도록 이 연습 및 다른 유사한 항목을 제공합니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749)을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.

> [!WARNING]
> Microsoft는 타사 라이브러리에 대한 수정 사항을 제공하지 않으며 이러한 라이브러리의 검토를 완료하지 않았습니다. 이 샘플은 기본 시나리오에서 Azure AD B2C와의 호환성이 테스트된 AppAuth라는 타사 라이브러리를 사용합니다. 문제 및 기능 요청은 라이브러리의 오픈 소스 프로젝트로 리디렉션되어야 합니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)(영문)를 읽어보세요.
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
Azure AD B2C에서 모든 사용자 환경은 [사용자 흐름](user-flow-overview.md)에 의해 정의됩니다. 이 애플리케이션은 결합된 로그인 및 등록의 하나의 ID 환경을 포함합니다. 사용자 흐름을 만들 때 다음을 확인합니다.

* **등록 특성** 아래에서 **표시 이름** 특성을 선택합니다.  다른 특성도 선택할 수 있습니다.
* **애플리케이션 클레임** 아래에서 **표시 이름** 및 **사용자의 개체 ID** 클레임을 선택합니다. 다른 클레임도 선택할 수 있습니다.
* 각 사용자 흐름을 만든 후에 **이름**을 복사합니다. 사용자 흐름을 저장하면 `b2c_1_`이 사용자 흐름 이름의 접두사로 지정됩니다.  사용자 흐름 이름이 나중에 필요합니다.

사용자 흐름을 만들었다면 앱을 빌드할 준비가 되었습니다.

## <a name="download-the-sample-code"></a>샘플 코드 다운로드
[GitHub에서](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) Azure AD B2C와 함께 AppAuth를 사용하는 작업 샘플이 제공됩니다. 코드를 다운로드하고 실행할 수 있습니다. 자신만의 Azure AD B2C 테넌트를 사용하려면 [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) 지침에 따르세요.

이 샘플은 [GitHub의 iOS AppAuth 프로젝트](https://github.com/openid/AppAuth-iOS)의 추가 정보 지침에 따라 만들었습니다. 샘플 및 라이브러리의 작동 방식에 대한 자세한 내용은 GitHub의 AppAuth 추가 정보를 참조하세요.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>AppAuth와 함께 Azure AD B2C를 사용하도록 앱 수정

> [!NOTE]
> AppAuth는 iOS 7 이상을 지원합니다.  그러나 Google에 소셜 로그인을 지원하려면 iOS 9 이상이 필요한 SFSafariViewController가 필요합니다.
>

### <a name="configuration"></a>구성

권한 부여 엔드포인트와 토큰 엔드포인트 URI를 모두 지정하여 Azure AD B2C와의 통신을 구성할 수 있습니다.  이러한 URI를 생성하려면 다음 정보가 필요합니다.
* 테넌트 ID(예: contoso.onmicrosoft.com)
* 사용자 흐름 이름(예: B2C\_1\_SignUpIn)

토큰 엔드포인트 URI는 다음 URL에서 테넌트\_ID 및 정책\_이름을 바꿔서 만들 수 있습니다.

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

권한 부여 엔드포인트 URI는 다음 URL에서 테넌트\_ID 및 정책\_이름을 바꿔서 만들 수 있습니다.

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

다음 코드를 실행하여 AuthorizationServiceConfiguration 개체를 만듭니다.

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>권한 부여

권한 부여 서비스 구성을 구성하거나 검색하면 권한 부여 요청을 생성할 수 있습니다. 요청을 만들려면 다음 정보가 필요합니다.

* 이전에 기록한 클라이언트 ID (응용 프로그램 ID)입니다. `00000000-0000-0000-0000-000000000000`)을 입력합니다.
* 이전에 기록한 사용자 지정 리디렉션 URI입니다. `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`)을 입력합니다.

두 항목 모두 [앱을 등록](#create-an-application)할 때 저장해야 합니다.

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

사용자 지정 스키마를 사용하는 URI에 대한 리디렉션을 처리하도록 애플리케이션을 설정하려면 사용자의 Info.pList에서 'URL 스키마' 목록을 업데이트해야 합니다.
* Info.pList를 엽니다.
* '번들 OS 유형 코드'와 같은 행 위로 마우스를 가져가서 \+ 기호를 클릭합니다.
* 새 행 'URL 형식'의 이름을 변경합니다.
* 'URL 형식'의 왼쪽에 있는 화살표를 클릭하여 트리를 엽니다.
* '항목 0'의 왼쪽에 있는 화살표를 클릭하여 트리를 엽니다.
* 항목 0 아래의 첫 번째 항목 이름을 'URL Schemes'로 바꿉니다.
* 'URL Schemes'의 왼쪽에 있는 화살표를 클릭하여 트리를 엽니다.
* '값' 열에서 'URL Schemes' 아래의 '항목 0' 왼쪽 필드가 비어 있습니다.  이 값을 애플리케이션의 고유한 스키마로 설정합니다.  OIDAuthorizationRequest 개체를 만들 때 redirectURL에 사용된 스키마와 일치해야 합니다.  이 샘플에서는 스키마 'com.onmicrosoft.fabrikamb2c.exampleapp'이 사용됩니다.

프로세스의 나머지 단계를 완료하는 방법은 [AppAuth 가이드](https://openid.github.io/AppAuth-iOS/)를 참조하세요. 작업 중인 앱으로 빠르게 시작해야 하는 경우에는 [샘플](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)을 확인하세요. [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md)에 나온 단계에 따라 사용자 고유의 Azure AD B2C 구성을 입력합니다.

Microsoft는 사용자 의견 및 제안을 항상 환영합니다! 이 문서를 완료하기가 어렵거나 이 콘텐츠를 개선할 사항이 있는 경우 페이지의 맨 아래에 의견을 보내주시면 감사하겠습니다. 기능 요청이 있는 경우 [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c)에 추가해 주세요.
