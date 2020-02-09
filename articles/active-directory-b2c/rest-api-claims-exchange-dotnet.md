---
title: 사용자 경험에서 REST API 클레임 교환 통합
titleSuffix: Azure AD B2C
description: 사용자 입력의 유효성 검사로 Azure AD B2C 사용자 경험에 REST API 클레임 교환을 통합 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 780d575bd7f035673510d5b1e62cff4dfd6ede16
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848760"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력의 유효성 검사로 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)의 기반이 되는 ID 경험 프레임워크를 사용하여 사용자 경험에서 RESTful API와 통합할 수 있습니다. 이 연습에서는 Azure AD B2C가 .NET Framework RESTful 서비스(Web API)와 상호 작용하는 방법을 알아봅니다.

## <a name="introduction"></a>소개

Azure AD B2C를 사용하면 RESTful 서비스를 호출하여 사용자 경험에 고유한 비즈니스 논리를 추가할 수 있습니다. ID 경험 프레임워크는 *입력 클레임* 컬렉션의 RESTful 서비스에 데이터를 보내고 *출력 클레임* 컬렉션의 RESTful에서 다시 데이터를 수신합니다. RESTful 서비스 통합을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* **사용자 입력 데이터의 유효성 검사**: 이 작업은 Azure AD의 잘못된 데이터가 지속되지 않도록 방지합니다. 사용자의 값이 유효하지 않으면 RESTful 서비스는 사용자에게 항목을 제공하도록 지시하는 오류 메시지를 반환합니다. 예를 들어 사용자가 제공한 전자 메일 주소가 고객 데이터베이스에서 종료되었는지 확인할 수 있습니다.
* **입력 클레임 덮어쓰기**: 예를 들어 사용자가 이름을 모두 소문자 또는 대문자로 입력한 경우 이름의 첫 번째 문자만을 대문자로 시작하도록 서식을 지정할 수 있습니다.
* **회사 기간 업무 애플리케이션과 추가로 통합하여 사용자 데이터 보강**: Your RESTful 서비스는 사용자의 전자 메일 주소를 수신하고, 고객의 데이터베이스를 쿼리하고, Azure AD B2C에 사용자의 전용 번호를 반환합니다. 반환 클레임을 사용자의 Azure AD 계정에서 저장하여 다음 *오케스트레이션 단계*에서 계산하거나, 액세스 토큰에 포함할 수 있습니다.
* **사용자 지정 비즈니스 논리 실행**: 푸시 알림을 보내고, 회사 데이터베이스를 업데이트하고, 사용자 마이그레이션 프로세스를 실행하고, 사용 권한을 관리하고, 데이터베이스를 감사하고, 다른 작업을 수행할 수 있습니다.

다음과 같은 방법으로 RESTful 서비스와 통합을 디자인할 수 있습니다.

* **유효성 검사 기술 프로필**: RESTful 서비스에 대한 호출은 지정된 기술 프로필의 유효성 검사 기술 프로필 내에서 발생합니다. 유효성 검사 기술 프로필은 사용자 경험이 진행되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용하면 다음과 같은 작업을 수행할 수 있습니다.
  * 입력 클레임을 보냅니다.
  * 입력 클레임의 유효성을 검사하고 사용자 정의 오류 메시지를 throw합니다.
  * 출력 클레임을 다시 보냅니다.

* **클레임 교환**: 이 디자인은 유효성 검사 기술 프로필과 유사하지만 오케스트레이션 단계 내에서 발생합니다. 이 정의는 다음으로 제한됩니다.
  * 입력 클레임을 보냅니다.
  * 출력 클레임을 다시 보냅니다.

## <a name="restful-walkthrough"></a>RESTful 연습

이 연습에서는 사용자 입력의 유효성을 검사하고 사용자 전용 번호를 제공하는 .NET Framework Web API를 개발합니다. 예를 들어 애플리케이션은 전용 번호에 따라 *플래티넘 혜택*에 대한 액세스 권한을 부여할 수 있습니다.

개요:

* RESTful 서비스 (.NET Framework web API) 개발
* 사용자 경험에서 RESTful 서비스 사용
* 입력 클레임을 보내고 코드에서 읽습니다.
* 사용자의 이름을 확인 합니다.
* 충성도 번호를 다시 보냅니다.
* JSON Web Token (JWT)에 충성도 번호를 추가 합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](custom-policy-get-started.md) 문서의 단계를 완료합니다.

## <a name="step-1-create-an-aspnet-web-api"></a>1단계: ASP.NET Web API 만들기

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택하여 프로젝트를 만듭니다.
1. **새 프로젝트** 창에서 **Visual C#**  > **웹** > **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택합니다.
1. **이름** 상자에서 애플리케이션의 이름(예: *Contoso.AADB2C.API*)을 지정한 후 **확인**을 선택합니다.

    ![Visual Studio에서 새 Visual Studio 프로젝트 만들기](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-create-project.png)

1. **새 ASP.NET 웹 애플리케이션** 창에서 **Web API** 또는 **Azure API 앱** 템플릿을 선택합니다.

    ![Visual Studio에서 web API 템플릿 선택](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-select-web-api.png)

1. 인증을 **인증 없음**으로 설정해야 합니다.
1. **확인**을 선택하여 프로젝트를 만듭니다.

## <a name="step-2-prepare-the-rest-api-endpoint"></a>2단계: REST API 엔드포인트 준비

### <a name="step-21-add-data-models"></a>2\.1단계 데이터 모델 추가

모델은 RESTful 서비스에서 입력 클레임 및 출력 클레임 데이터를 나타냅니다. 코드에서는 입력 클레임 모델을 JSON 문자열에서 C# 개체(모델)로 역직렬화하여 입력 데이터를 읽습니다. ASP.NET Web API는 자동으로 출력 클레임 모델을 다시 JSON으로 역직렬화하고 HTTP 응답 메시지의 본문에 직렬화된 데이터를 기록합니다.

다음을 수행하여 입력 클레임을 나타내는 모델을 만듭니다.

1. 솔루션 탐색기가 열려 있지 않으면 **보기** > **솔루션 탐색기**를 선택합니다.
1. 솔루션 탐색기에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **클래스**를 차례로 선택합니다.

    ![Visual Studio에서 선택한 클래스 추가 메뉴 항목](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-model.png)

1. `InputClaimsModel` 클래스의 이름을 지정하고 `InputClaimsModel` 클래스에 다음 속성을 추가합니다.

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

1. 새 모델인 `OutputClaimsModel`을 만들고 `OutputClaimsModel` 클래스에 다음 속성을 추가합니다.

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

1. 입력 유효성 검사 오류 메시지를 throw 하는 데 사용 하는 `B2CResponseContent`모델을 하나 더 만듭니다. 다음 속성을 `B2CResponseContent` 클래스에 추가하고 누락된 참조를 제공하고 해당 파일을 저장합니다.

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>2\.2단계: 컨트롤러 추가

Web API에서 _컨트롤러_는 HTTP 요청을 처리하는 개체입니다. 컨트롤러는 출력 클레임을 반환하거나 이름이 유효하지 않은 경우 충돌 HTTP 오류 메시지를 throw합니다.

1. 솔루션 탐색기에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **컨트롤러**를 차례로 선택합니다.

    ![Visual Studio에서 새 컨트롤러 추가](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-1.png)

1. **스캐폴드 추가** 창에서 **Web API 컨트롤러 - 비어 있음**을 선택하고 **추가**를 선택합니다.

    ![Visual Studio에서 비어 있는 웹 API 2 컨트롤러 선택](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-2.png)

1. **컨트롤러 추가** 창에서 컨트롤러의 이름을 **IdentityController**로 지정한 후 **추가**를 선택합니다.

    ![Visual Studio에서 컨트롤러 이름 입력](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    스캐폴딩은 *Controllers* 폴더에서 *IdentityController.cs*라는 파일을 만듭니다.

1. *IdentityController.cs* 파일이 열려있지 않으면 두 번 클릭한 후 파일의 코드를 다음 코드로 바꿉니다.

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>3단계: Azure에 프로젝트 게시

1. 솔루션 탐색기에서 **Contoso.AADB2C.API** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시**를 선택합니다.

    ![Visual Studio를 사용 하 여 Microsoft Azure App Service에 게시](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

1. **게시** 창에서 **Microsoft Azure App Service**를 선택한 다음 **게시**를 선택합니다.

    ![Visual Studio를 사용 하 여 새 Microsoft Azure App Service 만들기](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **App Service 만들기** 창이 열립니다. 여기에서 Azure에서 ASP.NET 웹앱을 실행하는 데 필요한 모든 Azure 리소스를 만듭니다.

    > [!TIP]
    > 게시 방법에 대한 자세한 내용은 [Azure에서 ASP.NET 웹앱 만들기](../app-service/app-service-web-get-started-dotnet-framework.md)를 참조하세요.

1. **Web App 이름** 상자에서 고유한 앱 이름을 입력합니다(유효한 문자: a-z, 0-9 및 -(하이픈)). 웹앱의 URL은 http://<app_name>.azurewebsites.NET입니다. 여기서 *app_name*은 웹앱의 이름입니다. 자동으로 생성된 이름을 적용할 수 있습니다. 이 이름은 고유합니다.

    ![App Service 속성 구성](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

1. Azure 리소스 만들기를 시작하려면 **만들기**를 선택합니다.
    ASP.NET 웹앱을 만든 후에 마법사는 Azure에 게시한 다음 기본 브라우저에서 앱을 시작합니다.

1. 웹앱의 URL을 복사합니다.

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>4단계: TrustFrameworkExtensions.xml 파일의 스키마에 새 `loyaltyNumber` 클레임 추가

`loyaltyNumber` 클레임은 아직 스키마에서 정의되지 않습니다. `<BuildingBlocks>` 요소 내에서 정의를 추가합니다. *TrustFrameworkExtensions.xml* 파일의 시작 부분에서 찾을 수 있습니다.

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>5단계: 클레임 공급자 추가

모든 클레임 공급자에는 엔드포인트를 지정하는 하나 이상의 기술 프로필 및 클레임 공급자와 통신하는 데 필요한 프로토콜이 있어야 합니다.

클레임 공급자에는 다양한 이유로 여러 기술 프로필이 있을 수 있습니다. 예를 들어 클레임 공급자가 여러 프로토콜을 지원하거나, 엔드포인트가 다른 기능을 가질 수 있거나 릴리스가 다양한 보증 수준을 가진 클레임을 포함할 수 있기 때문에 여러 기술 프로필을 정의할 수 있습니다. 다른 사용자 경험이 아닌 하나의 사용자 경험에서 중요한 클레임을 해제하기 위해 허용할 수도 있습니다.

다음 XML 코드 조각에는 두 가지 기술 프로필을 포함하는 클레임 공급자 노드가 포함됩니다.

* **TechnicalProfile Id="REST-API-SignUp"** : RESTful 서비스를 정의합니다.
  * `Proprietary`은 RESTful 기반 공급자의 프로토콜로 설명되어 있습니다.
  * `InputClaims`는 Azure AD B2C에서 REST 서비스로 전송할 클레임을 정의합니다.

    이 예제에서 `givenName` 클레임의 콘텐츠는 `firstName`으로 REST 서비스에 보내고, `surname` 클레임의 콘텐츠 `lastName`으로 REST 서비스에 보내고, `email`은 그대로 보냅니다. `OutputClaims` 요소는 RESTful 서비스에서 Azure AD B2C로 다시 검색하는 클레임을 정의합니다.

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"** : 유효성 검사 기술 프로필을 기존 기술 프로필(기본 정책에 정의됨)에 추가합니다. 경험을 등록하는 동안 유효성 검사 기술 프로필은 이전 기술 프로필을 호출합니다. RESTful 서비스가 HTTP 오류 409(충돌 오류)를 반환하는 경우 사용자에게 오류 메시지가 표시됩니다.

`<ClaimsProviders>` 노드를 찾은 후 `<ClaimsProviders>` 노드 아래에서 다음 XML 코드 조각을 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>

    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

`AuthenticationType` 위의 설명 및 `AllowInsecureAuthInProduction` 프로덕션 환경으로 이동할 때 수행 해야 하는 변경 내용을 지정 합니다. 프로덕션을 위해 RESTful Api를 보호 하는 방법을 알아보려면 [기본 인증을 사용 하는 RESTful Api 보안](secure-rest-api-dotnet-basic-auth.md) 및 [인증서 인증을 사용 하는 보안 RESTful api](secure-rest-api-dotnet-certificate-auth.md)를 참조 하세요.

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>6단계: `loyaltyNumber` 클레임을 신뢰 당사자 정책 파일에 추가하여 애플리케이션에 해당 클레임을 보냄

*SignUpOrSignIn.xml* RP(신뢰 당사자) 파일을 편집하고 TechnicalProfile Id="PolicyProfile" 요소를 수정하여 `<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`를 추가합니다.

새 클레임을 추가한 후에 신뢰 당사자 코드는 다음과 같습니다.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>7단계: 테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.

1. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.

1. **ID 경험 프레임워크**를 선택합니다.

1. **모든 정책**을 엽니다.

1. **정책 업로드**를 선택합니다.

1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.

1. TrustFrameworkExtensions.xml 파일을 업로드하고 유효성 검사를 통과하는지 확인합니다.

1. SignUpOrSignIn.xml 파일을 사용하여 이전 단계를 반복합니다.

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>8단계: 지금 실행을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 선택한 다음 **ID 경험 프레임워크**로 이동합니다.

    > [!NOTE]
    > **지금 실행**을 사용하려면 하나 이상의 애플리케이션이 테넌트에 미리 등록되어 있어야 합니다. 애플리케이션을 등록하는 방법은 Azure AD B2C [시작](tutorial-create-tenant.md) 문서 또는 [애플리케이션 등록](tutorial-register-applications.md) 문서를 참조하세요.

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.

    ![Azure Portal의 B2C_1A_signup_signin 사용자 지정 정책 페이지](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-run.png)

3. **지정된 이름** 상자에 **테스트**를 입력하여 프로세스를 테스트합니다.
    Azure AD B2C는 창의 위쪽에 오류 메시지를 표시합니다.

    ![등록 로그인 페이지에서 지정 된 이름 입력 유효성 검사 테스트](./media/rest-api-claims-exchange-dotnet/aadb2c-ief-rest-api-netfw-test.png)

4. **지정된 이름** 상자에서 ("Test" 이외의) 이름을 입력합니다.
    Azure AD B2C는 사용자를 등록한 후 전용 번호를 애플리케이션에 보냅니다. 이 JWT의 번호를 적어둡니다.

```JSON
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1507125903,
  "nbf": 1507122303,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1507122303,
  "auth_time": 1507122303,
  "loyaltyNumber": "290",
  "given_name": "Emily",
  "emails": ["B2cdemo@outlook.com"]
}
```

## <a name="optional-download-the-complete-policy-files-and-code"></a>(선택 사항)완성 정책 파일 및 코드 다운로드

* [사용자 지정 정책 시작](custom-policy-get-started.md) 연습을 완료한 후에 고유한 사용자 지정 정책 파일을 사용하여 시나리오를 빌드하는 것이 좋습니다. 참조를 위한 [샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)이 제공됩니다.

* [참조를 위한 Visual Studio 솔루션 샘플](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)에서 전체 코드를 다운로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 작업은 기본 또는 클라이언트 인증서 인증을 사용 하 여 RESTful API를 보호 하는 것입니다. Api를 보호 하는 방법을 알아보려면 다음 문서를 참조 하세요.

* [기본 인증(사용자 이름 및 암호)을 사용하여 RESTful API 보호](secure-rest-api-dotnet-basic-auth.md)
* [클라이언트 인증서를 사용하여 RESTful API 보호](secure-rest-api-dotnet-certificate-auth.md)

RESTful 기술 프로필에서 사용할 수 있는 모든 요소에 대한 자세한 내용은 [참조: RESTful 기술 프로필](restful-technical-profile.md)을 참조 하세요.
