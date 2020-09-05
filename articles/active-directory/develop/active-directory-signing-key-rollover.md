---
title: Microsoft id 플랫폼에서 서명 키 롤오버
description: 이 문서에서는 Azure Active Directory에 대한 서명 키 롤오버 모범 사례를 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: b65ad1f22d20686a1ee47631f9209e1b15b0ab58
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948133"
---
# <a name="signing-key-rollover-in-microsoft-identity-platform"></a>Microsoft id 플랫폼에서 서명 키 롤오버
이 문서에서는 Microsoft id 플랫폼에서 보안 토큰에 서명 하는 데 사용 되는 공개 키에 대해 알아야 할 사항을 설명 합니다. 이러한 키는 정기적으로 롤오버 되며 응급 상황에서 즉시 롤오버 될 수 있다는 점에 유의 해야 합니다. Microsoft id 플랫폼을 사용 하는 모든 응용 프로그램은 키 롤오버 프로세스를 프로그래밍 방식으로 처리할 수 있어야 합니다. 키의 작동 방식과 롤오버가 애플리케이션에 미친 영향을 평가하는 방법, 필요한 경우 키 롤오버를 처리하도록 애플리케이션을 업데이트하거나 정기적인 수동 롤오버 프로세스를 설정하는 방법을 이해하려면 계속 읽어 보세요.

## <a name="overview-of-signing-keys-in-microsoft-identity-platform"></a>Microsoft id 플랫폼의 서명 키 개요
Microsoft id 플랫폼은 산업 표준을 기반으로 하는 공개 키 암호화를 사용 하 여 자체와이를 사용 하는 응용 프로그램 간에 신뢰를 설정 합니다. 실제로이는 다음과 같은 방식으로 작동 합니다. Microsoft id 플랫폼은 공개 키와 개인 키 쌍으로 구성 된 서명 키를 사용 합니다. 사용자가 인증을 위해 Microsoft id 플랫폼을 사용 하는 응용 프로그램에 로그인 하는 경우 Microsoft id 플랫폼은 사용자에 대 한 정보를 포함 하는 보안 토큰을 만듭니다. 이 토큰은 응용 프로그램으로 다시 전송 되기 전에 개인 키를 사용 하 여 Microsoft id 플랫폼에서 서명 됩니다. 토큰이 유효 하 고 Microsoft id 플랫폼에서 시작 되었는지 확인 하려면 응용 프로그램은 테 넌 트의 [Openid connect Connect 검색 문서](https://openid.net/specs/openid-connect-discovery-1_0.html) 또는 SAML/WS-급지됨 [페더레이션 메타 데이터 문서](../azuread-dev/azure-ad-federation-metadata.md)에 포함 된 microsoft id 플랫폼에 의해 노출 되는 공개 키를 사용 하 여 토큰 서명의 유효성을 검사 해야 합니다.

보안을 위해 Microsoft id 플랫폼의 서명 키는 정기적으로 롤오버 되며 응급 상황에서 즉시 롤오버 될 수 있습니다. 이러한 키 롤업 간에 설정 또는 보장 된 시간은 없습니다. Microsoft id 플랫폼과 통합 되는 모든 응용 프로그램은 발생 빈도에 관계 없이 키 롤오버 이벤트를 처리 하도록 준비 해야 합니다. 없는 경우 애플리케이션은 토큰에서 서명을 확인하기 위해 만료된 키를 사용하려고 시도하며 서명 요청이 실패합니다.  업데이트를 위해 24 시간 마다 확인 하는 것이 가장 좋은 방법입니다. 알 수 없는 키 식별자를 사용 하 여 토큰을 발견 하는 경우 키 문서를 제한 (최대 5 분 마다 한 번) 합니다. 

OpenID Connect discovery 문서와 페더레이션 메타데이터 문서에는 사용 가능한 키가 항상 두 개 이상 있습니다. 응용 프로그램은 문서에 지정 된 모든 키를 사용할 수 있도록 준비 해야 합니다. 한 키가 곧 롤오버 될 수 있고 다른 키가 대체 될 수도 있기 때문입니다.  제공 되는 키 수는 새 플랫폼, 새 클라우드 또는 새 인증 프로토콜을 지원 하기 때문에 Microsoft id 플랫폼의 내부 아키텍처에 따라 시간이 지남에 따라 변경 될 수 있습니다. JSON 응답의 키 순서와 이러한 키가 노출 된 순서는 앱에 meaninful 것으로 간주 되지 않습니다. 

단일 서명 키만 지 원하는 응용 프로그램 또는 서명 키를 수동으로 업데이트 해야 하는 응용 프로그램은 본질적으로 안전 하 고 안정적이 지 않습니다.  [표준 라이브러리](reference-v2-libraries.md) 를 사용 하 여 다른 모범 사례에서 항상 최신 서명 키를 사용 하도록 업데이트 해야 합니다. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>애플리케이션이 영향을 받을지 여부와 그에 대한 대처 방안을 평가하는 방법
애플리케이션이 키 롤오버를 처리하는 방식은 애플리케이션 유형 또는 사용된 ID 프로토콜 및 라이브러리 버전과 같은 변수에 따라 달라집니다. 아래 섹션에서는 가장 일반적인 유형의 애플리케이션이 키 롤오버의 영향을 받는지 여부와 자동 롤오버를 지원하거나 수동으로 키를 업데이트하도록 애플리케이션을 업데이트하는 방법에 대한 지침을 제공하는지 여부를 평가합니다.

* [리소스에 액세스하는 네이티브 클라이언트 애플리케이션](#nativeclient)
* [리소스에 액세스하는 웹 애플리케이션/API](#webclient)
* [리소스를 보호하고 Azure App Services를 사용하여 빌드된 웹 애플리케이션/API](#appservices)
* [.NET OWIN Openid connect Connect, WS-급지됨 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용 하 여 리소스를 보호 하는 웹 응용 프로그램/a p i](#owin)
* [.NET Core OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API](#owincore)
* [Node.js passport-azure-ad 모듈을 사용하여 리소스를 보호하는 웹 애플리케이션/API](#passport)
* [리소스를 보호 하 고 Visual Studio 2015 이상으로 만든 웹 응용 프로그램/a p i](#vs2015)
* [리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 애플리케이션](#vs2013)
* 리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 API
* [리소스를 보호 하 고 Visual Studio 2012을 사용 하 여 만든 웹 응용 프로그램](#vs2012)
* [리소스를 보호하며 Visual Studio 2010, 2008 또는 Windows Identity Foundation을 사용하여 만든 웹 애플리케이션](#vs2010)
* [다른 라이브러리를 사용 하거나 지원 되는 프로토콜을 수동으로 구현 하 여 리소스를 보호 하는 웹 응용 프로그램/a p i](#other)

이 설명서는 다음에 적용할 수 **없습니다** .

* Azure AD 애플리케이션 갤러리에서 추가된 애플리케이션(사용자 지정 포함)에는 서명 키와 관련하여 별도 설명서가 있습니다. [추가 정보.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* 애플리케이션 프록시를 통해 게시된 온-프레미스 애플리케이션은 서명 키에 대해 걱정할 필요가 없습니다.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>리소스에 액세스하는 네이티브 클라이언트 애플리케이션
리소스에만 액세스하는 애플리케이션(즉, Microsoft Graph, KeyVault, Outlook API 및 기타 Microsoft Api)는 일반적으로 토큰을 가져와 리소스 소유자에 게 전달 합니다. 리소스를 보호하지 못한다면 토큰을 검사하지 않으므로 제대로 서명되었는지 확인할 필요도 없습니다.

데스크톱 또는 모바일의 네티이브 클라이언트 애플리케이션이 이 범주에 해당하므로 롤오버의 영향을 받지 않습니다.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>리소스에 액세스하는 웹 애플리케이션/API
리소스에만 액세스하는 애플리케이션(즉, Microsoft Graph, KeyVault, Outlook API 및 기타 Microsoft Api)는 일반적으로 토큰을 가져와 리소스 소유자에 게 전달 합니다. 리소스를 보호하지 못한다면 토큰을 검사하지 않으므로 제대로 서명되었는지 확인할 필요도 없습니다.

앱 전용 흐름 (클라이언트 자격 증명/클라이언트 인증서)을 사용 하 여 토큰을 요청 하는 웹 응용 프로그램 및 웹 Api는이 범주에 해당 하므로 롤오버의 영향을 받지 않습니다.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>리소스를 보호하고 Azure App Services를 사용하여 빌드된 웹 애플리케이션/API
Azure App Services의 인증/권한 부여(EasyAuth) 기능에는 이미 키 롤오버를 자동으로 처리하는 데 필요한 논리가 있습니다.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>.NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API
애플리케이션에서 .NET OWIN OpenID Connect, WS-Fed 또는 WindowsAzureActiveDirectoryBearerAuthentication 미들웨어를 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

애플리케이션의 Startup.cs 또는 Startup.Auth.cs에서 다음 코드 조각을 찾아봄으로써 애플리케이션에서 이들 미들웨어를 사용하는지 확인할 수 있습니다.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>.NET Core OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용하여 리소스를 보호하는 웹 애플리케이션/API
애플리케이션에서 .NET Core OWIN OpenID Connect 또는 JwtBearerAuthentication 미들웨어를 사용 하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

애플리케이션의 Startup.cs 또는 Startup.Auth.cs에서 다음 코드 조각을 찾아봄으로써 애플리케이션에서 이들 미들웨어를 사용하는지 확인할 수 있습니다.

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Node.js passport-azure-ad 모듈을 사용하여 리소스를 보호하는 웹 애플리케이션/API
애플리케이션에서 Node.js passport-ad module을 사용하는 경우, 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다.

애플리케이션의 app.js에서 다음 코드 조각을 찾아봄으로써 애플리케이션 passport ad를 확인할 수 있습니다.

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>리소스를 보호 하 고 Visual Studio 2015 이상으로 만든 웹 응용 프로그램/a p i
Visual Studio 2015 이상에서 웹 응용 프로그램 템플릿을 사용 하 여 응용 프로그램을 빌드 했 고 **인증 변경** 메뉴에서 **회사 또는 학교 계정** 을 선택한 경우 자동으로 키 롤오버를 처리 하는 데 필요한 논리가 이미 있는 것입니다. OWIN OpenID Connect 미들웨어에 포함된 이 논리는 OpenID Connect discovery 문서에서 키를 검색하고 캐시하며 주기적으로 새로 고칩니다.

인증을 솔루션에 수동으로 추가하면 애플리케이션에 필요한 키 롤오버 논리가 없을 수도 있습니다. 직접 작성 하거나 [다른 라이브러리를 사용 하거나 지원 되는 프로토콜을 수동으로 구현 하는 웹 응용 프로그램/api](#other)의 단계를 수행 해야 합니다.

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 애플리케이션
Visual Studio 2013에서 웹 애플리케이션 템플릿을 사용하여 애플리케이션을 빌드했고 **인증 변경** 메뉴에서 **조직 계정**을 선택한 경우 자동으로 키 롤오버를 처리하는 데 필요한 논리가 이미 있는 것입니다. 이 논리는 조직의 고유 ID 및 서명 키 정보를 프로젝트와 연결된 두 데이터베이스 테이블에 저장합니다. 프로젝트의 Web.config 파일에서 데이터베이스에 대한 연결 문자열을 찾을 수 있습니다.

인증을 솔루션에 수동으로 추가하면 애플리케이션에 필요한 키 롤오버 논리가 없을 수도 있습니다. 사용자가 직접 작성하거나 [웹 애플리케이션/다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 API](#other)의 단계를 따라야 합니다.

다음 단계는 애플리케이션에서 논리가 제대로 작동하는지 확인하는 데 도움이 됩니다.

1. Visual Studio 2013에서 솔루션을 연 다음 오른쪽 창에서 **서버 탐색기** 탭을 클릭합니다.
2. **데이터 연결**, **DefaultConnection** 및 **테이블**을 차례로 확장합니다. **IssuingAuthorityKeys** 테이블을 찾아 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시**를 클릭합니다.
3. **IssuingAuthorityKeys** 테이블에는 각 키의 지문에 해당하는 행이 하나 이상 있습니다. 테이블의 모든 행을 삭제합니다.
4. **Tenants** 테이블을 마우스 오른쪽 단추로 클릭한 다음 **테이블 데이터 표시**를 클릭합니다.
5. **Tenants** 테이블에는 고유한 디렉터리 테넌트 식별자에 해당하는 행이 하나 이상 있습니다. 테이블의 모든 행을 삭제합니다. **테넌트** 테이블 및 **IssuingAuthorityKeys** 테이블 모두에서 행을 삭제하지 않으면 런타임에 오류가 발생합니다.
6. 애플리케이션을 빌드 및 실행합니다. 계정에 로그인한 후에 애플리케이션을 중지할 수 있습니다.
7. **서버 탐색기**로 돌아와서 **IssuingAuthorityKeys** 및 **Tenants** 테이블의 값을 확인합니다. 페더레이션 메타데이터 문서의 적절한 정보로 자동으로 다시 채워진 것을 알 수 있습니다.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>리소스를 보호하며 Visual Studio 2013을 사용하여 만든 웹 API
Web API 템플릿을 사용하여 Visual Studio 2013에서 Web API 응용 프로그램을 만들고 **인증 변경** 메뉴에서 **조직 계정**을 선택한 경우 응용 프로그램에는 이미 필수 논리가 있습니다.

수동으로 인증을 구성한 경우 아래 지침에 따라 웹 API를 구성 하 여 키 정보를 자동으로 업데이트 하는 방법을 알아보세요.

다음 코드 조각은 페더레이션 메타데이터 문서에서 최신 키를 가져온 후 [JWT 토큰 처리기](https://msdn.microsoft.com/library/dn205065.aspx) 를 사용하여 토큰의 유효성을 검사하는 방법을 보여 줍니다. 이 코드 조각에서는 데이터베이스, 구성 파일 또는 다른 위치에 있든 관계 없이 Microsoft id 플랫폼에서 이후 토큰의 유효성을 검사 하기 위해 키를 유지 하는 데 사용자 고유의 캐싱 메커니즘을 사용 한다고 가정 합니다.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>리소스를 보호하며 Visual Studio 2012를 사용하여 만든 웹 애플리케이션
Visual Studio 2012에서 애플리케이션을 빌드한 경우 애플리케이션을 구성하는 데 아마도 ID 및 액세스 도구를 사용한 것입니다. 또한 [발급자 이름 레지스트리 유효성 검사(VINR)](https://msdn.microsoft.com/library/dn205067.aspx)를 사용할 가능성이 높습니다. VINR는 신뢰할 수 있는 id 공급자 (Microsoft id 플랫폼) 및 해당 토큰에서 발급 된 토큰의 유효성을 검사 하는 데 사용 되는 키에 대 한 정보를 유지 관리 합니다. 또한 VINR을 통해 해당 디렉터리에 연결된 최신 페더레이션 메타데이터 문서를 다운로드하고 구성이 최신 문서와 함께 만료되었는지 확인하며 필요에 따라 새 키를 사용하도록 애플리케이션을 업데이트하여 Web.config 파일에 저장된 키 정보를 자동으로 간편하게 업데이트할 수 있습니다.

Microsoft에서 제공하는 코드 샘플 또는 연습 문서를 사용하여 애플리케이션을 만든 경우 키 롤오버 논리가 프로젝트에 이미 포함됩니다. 아래 코드가 프로젝트에 이미 있는 것을 확인할 수 있습니다. 애플리케이션에 이 논리가 아직 없는 경우 아래 단계에 따라 추가하고 제대로 작동하는지 확인합니다.

1. **솔루션 탐색기**에서 해당 프로젝트의 **System.IdentityModel** 어셈블리에 참조를 추가합니다.
2. **Global.asax.cs** 파일을 열고 지시문을 사용하여 다음을 추가합니다.
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. 다음 메서드를 **Global.asax** 파일에 추가합니다.
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. 아래 표시된 것처럼 **Global.asax.cs**의 **Application_Start()** 메서드에서 **RefreshValidationSettings()** 메서드를 호출합니다.
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

이러한 단계를 완료하면 애플리케이션의 Web.config가 최신 키를 포함한 페더레이션 메타데이터 문서의 최신 정보로 업데이트됩니다. 이 업데이트는 애플리케이션 풀이 IIS에서 재활용될 때마다 발생하며 기본적으로 IIS는 29시간마다 애플리케이션을 재활용하도록 설정됩니다.

아래 단계에 따라 키 롤오버 논리가 제대로 작동하는지 확인합니다.

1. 애플리케이션이 위의 코드를 사용 중인지를 확인한 후 **Web.config** 파일을 열고 **\<issuerNameRegistry>** 블록으로 이동하여 구체적으로 다음 몇 줄을 확인합니다.
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. 설정에서 **\<add thumbprint="">** 모든 문자를 다른 문자로 바꿔서 지문 값을 변경 합니다. **Web.config** 파일을 저장합니다.
3. 애플리케이션을 빌드하고 실행합니다. 로그인 프로세스를 완료할 수 있으면 애플리케이션은 디렉터리의 페더레이션 메타데이터 문서에서 필요한 정보를 다운로드하여 키를 성공적으로 업데이트합니다. 로그인 하는 데 문제가 있는 경우 [Microsoft id 플랫폼을 사용 하 여 웹 응용 프로그램에 로그온 추가](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) 문서를 읽거나 응용 프로그램의 변경 내용이 올바른지 확인 하 고, [Azure Active Directory에 대 한 다중 테 넌 트 클라우드 응용 프로그램](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)코드 샘플을 다운로드 및 검사 합니다.

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>리소스를 보호하며 Visual Studio 2008 또는 2010 및 .NET 3.5용 WIF(Windows Identity Foundation) v1.0을 사용하여 만든 웹 애플리케이션
WIF v1.0에서 애플리케이션을 빌드한 경우 새 키를 사용하도록 애플리케이션의 구성을 자동으로 새로 고치는 데 제공된 메커니즘이 없습니다.

* *가장 쉬운 방법* WIF SDK에 포함된 FedUtil 도구를 사용합니다. 이 도구를 통해 최신 메타데이터 문서를 검색하고 구성을 업데이트할 수 있습니다.
* 애플리케이션을 시스템 네임스페이스에 있는 최신 버전의 WIF를 포함하는 .NET 4.5로 업데이트합니다. 그런 다음, [발급자 이름 레지스트리 유효성 검사(VINR)](https://msdn.microsoft.com/library/dn205067.aspx)를 사용하여 애플리케이션 구성의 자동 업데이트를 수행할 수 있습니다.
* 이 지침 문서의 끝에 있는 지침에 따라 수동 롤오버를 수행합니다.

구성을 업데이트하기 위해 FedUtil을 사용하는 지침:

1. Visual Studio 2008 또는 2010의 개발 컴퓨터에 WIF v1.0 SDK가 설치되어 있는지 확인합니다. 아직 설치되어 있지 않은 경우 [여기에서 다운로드](https://www.microsoft.com/en-us/download/details.aspx?id=4451) 할 수 있습니다.
2. Visual Studio에서 솔루션을 연 다음 응용 프로그램 프로젝트를 마우스 오른쪽 단추로 클릭하고 **페더레이션 메타데이터 업데이트**를 선택합니다. 이 옵션을 사용할 수 없는 경우 FedUtil 및/또는 WIF v1.0 SDK가 설치되지 않은 것입니다.
3. 표시된 메시지에서 **업데이트**를 선택하여 페더레이션 메타데이터 업데이트를 시작합니다. 애플리케이션이 호스팅되는 서버 환경에 대한 액세스 권한이 있는 경우 필요에 따라 FedUtil의 [자동 메타데이터 업데이트 스케줄러](https://msdn.microsoft.com/library/ee517272.aspx)를 사용할 수 있습니다.
4. **마침**을 클릭하여 업데이트 프로세스를 완료합니다.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하여 리소스를 보호하는 웹 애플리케이션/API
다른 라이브러리를 사용하거나 지원되는 프로토콜을 수동으로 구현하는 경우, 키가 OpenID Connect discovery 문서 또는 페더레이션 메타데이터 문서에서 검색되는지 확인하기 위해 라이브러리나 구현을 검토할 필요가 있습니다. 이를 확인하는 하나의 방법은 OpenID discovery 문서 또는 페더레이션 메타데이터 문서에 대한 모든 호출 코드 또는 라이브러리 코드를 검색하는 것입니다.

키가 어딘가에 저장되거나 애플리케이션에 하드 코딩되는 경우 이 지침 문서의 끝에 있는 지침에 따라 수동 롤오버를 수행하여 수동으로 키를 검색하고 적절하게 업데이트할 수 있습니다. Microsoft id 플랫폼이 롤오버 주기를 증가 시키거나 응급 대역 외 롤오버를 사용 하는 경우 이후 중단 및 오버 헤드를 방지 하기 위해이 문서에서 설명 하는 방법을 사용 하 여 **자동 롤오버를 지원 하도록 응용 프로그램을 개선 하는** 것이 좋습니다.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>애플리케이션을 테스트하여 영향을 받을지 확인하는 방법
스크립트를 다운로드하고 [이 GitHub 리포지토리](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>애플리케이션이 자동 롤오버를 지원하지 않는 경우 수동 롤오버를 수행하는 방법
응용 프로그램에서 자동 롤오버를 지원 **하지** 않는 경우 Microsoft id 플랫폼의 서명 키를 정기적으로 모니터링 하 고 그에 따라 수동 롤오버를 수행 하는 프로세스를 설정 해야 합니다. [이 GitHub 리포지토리](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) 는 이 작업을 수행하는 방법에 대한 스크립트 및 지침을 포함합니다.
