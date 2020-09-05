---
title: Microsoft ID 플랫폼 Java 웹앱 빠른 시작 | Azure
description: OpenID Connect를 사용하여 Java 웹앱에서 Microsoft 로그인을 구현하는 방법을 알아봅니다.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 10ae1c76d48c1cedbb915fec66177ac3612feea0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115223"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>빠른 시작: Java 웹앱에 Microsoft로 로그인 추가

이 자습서에서는 Java 웹 애플리케이션을 Microsoft ID 플랫폼에 통합하는 방법을 알아봅니다. 개발자의 앱은 사용자를 로그인하고, Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오고, Microsoft Graph API를 요청합니다.

이 빠른 시작을 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등)의 로그인을 수락하게 됩니다. (자세한 내용은 [샘플 작동 방식 ](#how-the-sample-works)을 참조하세요.)

## <a name="prerequisites"></a>필수 구성 요소

이 샘플을 실행하려면 다음이 필요합니다.

- [JDK(Java Development Kit)](https://openjdk.java.net/) 8 이상 및 [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작할 수 있는 기본(옵션 1) 또는 수동(옵션 2)의 두 가지 옵션이 있습니다.
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. [Azure Portal - 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs) 빠른 시작 환경으로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
> 1. 포털의 빠른 시작 환경에 있는 지침에 따라 자동으로 구성된 애플리케이션 코드를 다운로드합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 애플리케이션을 등록하고 앱의 등록 정보를 애플리케이션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
>
> 1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
> 1. **새 등록**을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
>    - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `java-webapp`)을 입력합니다.
>    - **등록**을 선택합니다.
> 1. **개요** 페이지에서 애플리케이션의 **애플리케이션(클라이언트) ID**와 **디렉터리(테넌트) ID** 값을 찾습니다. 나중에 사용할 수 있도록 이러한 값을 복사합니다.
> 1. 메뉴에서 **인증**을 선택한 후 다음 정보를 추가합니다.
>    - **웹** 플랫폼 구성을 추가합니다.  이러한 `https://localhost:8443/msal4jsample/secure/aad` 및 `https://localhost:8443/msal4jsample/graph/me`를 **리디렉션 URI**로 추가합니다.
>    - **저장**을 선택합니다.
> 1. 메뉴에서 **인증서 및 암호**를 선택하고 **클라이언트 암호** 섹션에서 **새 클라이언트 암호**를 클릭합니다.
>
>    - 키 설명(인스턴스 앱 비밀)을 입력합니다.
>    - **1년 후**에 키 기간을 선택합니다.
>    - **추가**를 선택하면 키 값이 표시됩니다.
>    - 나중에 사용할 수 있도록 키 값을 복사합니다. 이 키 값은 다시 표시되지 않으며 다른 어떤 방법으로도 검색할 수 없으므로, Azure Portal에 표시되는 즉시 기록해 두어야 합니다.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
>
> 빠른 시작용 코드 샘플이 작동하려면 다음을 수행해야 합니다.
>
> 1. 회신 URL을 `https://localhost:8443/msal4jsample/secure/aad` 및 `https://localhost:8443/msal4jsample/graph/me`로 추가
> 1. 클라이언트 비밀을 만듭니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [이러한 변경 내용 적용]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-code-sample"></a>2단계: 코드 샘플 다운로드
> [!div renderon="docs"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> 프로젝트를 다운로드하고 zip 파일을 루트 폴더에 가까운 로컬 폴더(예제: **C:\Azure-Samples**)로 추출합니다.
>
> Localhost와 함께 https를 사용하려면 server.ssl.key 속성을 입력합니다. 자체 서명된 인증서를 생성하려면 JRE에 포함된 keytool 유틸리티를 사용합니다.
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   생성된 키 저장소 파일을 "resources" 폴더에 배치합니다.

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>3단계: 코드 샘플 구성
> 1. zip 파일을 로컬 폴더에 추출합니다.
> 1. 통합 개발 환경을 사용하는 경우 원하는 IDE에서 샘플을 엽니다(선택 사항).
> 1. src/main/resources/ 폴더에 있는 application.properties 파일을 열고 *aad.clientId*, *aad.authority* 및 *aad.secretKey* 필드의 값을 다음과 같이 **애플리케이션 ID**, **테넌트 ID** 및 **클라이언트 암호**의 해당 값으로 바꿉니다.
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> 위치:
>
> - `Enter_the_Application_Id_here` - 등록한 애플리케이션의 애플리케이션 ID입니다.
> - `Enter_the_Client_Secret_Here` - 등록한 애플리케이션의 **인증서 및 비밀**에서 만든 **클라이언트 비밀**입니다.
> - `Enter_the_Tenant_Info_Here` - 등록한 애플리케이션의 **디렉터리(테넌트 ) ID** 값입니다.
> 1. Localhost와 함께 https를 사용하려면 server.ssl.key 속성을 입력합니다. 자체 서명된 인증서를 생성하려면 JRE에 포함된 keytool 유틸리티를 사용합니다.
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   생성된 키 저장소 파일을 "resources" 폴더에 배치합니다.


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>3단계: 코드 샘플 실행
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>4단계: 코드 샘플 실행

프로젝트를 실행하려면 다음 중 하나를 수행합니다.

포함된 스프링 부트 서버를 사용하여 IDE에서 직접 실행하거나 [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html)을 사용하여 WAR 파일에 패키지하고 [Apache Tomcat](http://tomcat.apache.org/)과 같은 J2EE 컨테이너 솔루션에 배포합니다.

##### <a name="running-from-ide"></a>IDE에서 실행

IDE에서 웹 애플리케이션을 실행하는 경우 실행을 클릭한 다음, 프로젝트의 홈페이지로 이동합니다. 이 샘플의 경우 표준 홈 페이지 URL이 https://localhost:8443 입니다.

1. 전면 페이지에서 **로그인** 단추를 선택하여 Azure Active Directory으로 리디렉션하고 사용자에게 자격 증명을 입력하라는 메시지를 표시합니다.

1. 사용자가 인증된 후에는 *https://localhost:8443/msal4jsample/secure/aad* 로 리디렉션됩니다. 사용자는 이제 로그인되었으며, 페이지에는 로그인된 계정에 대한 정보가 표시됩니다. 샘플 UI에는 다음과 같은 단추가 있습니다.
    - *로그아웃*: 애플리케이션에서 현재 사용자를 로그아웃하고 홈페이지로 리디렉션합니다.
    - *사용자 정보 표시*: Microsoft Graph에 대한 토큰을 획득하고, 로그인한 사용자에 대한 기본 정보를 반환하는 토큰을 포함하는 요청을 사용하여 Microsoft Graph를 호출합니다.

##### <a name="running-from-tomcat"></a>Tomcat에서 실행

웹 샘플을 Tomcat에 배포하려면 소스 코드를 몇 가지 변경해야 합니다.

1. ms-identity-java-webapp/pom.xml 열기
    - `<name>msal-web-sample</name>`에서 `<packaging>war</packaging>` 추가

2. ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication 열기

    - 모든 소스 코드를 삭제하고 다음으로 바꿉니다.

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   Tomcat의 기본 HTTP 포트는 8080이지만 포트 8443을 통한 HTTPS 연결이 필요합니다. 이를 구성하려면 다음을 수행합니다.
        - tomcat/conf/server.xml로 이동합니다.
        - `<connector>` 태그를 검색하고 기존 커넥터를 다음으로 바꿉니다.
        ```
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ``` 
       
4. 명령 프롬프트를 열어 이 샘플의 루트 폴더(pom.xml 파일이 있는 위치)로 이동하고 `mvn package`를 실행하여 프로젝트를 빌드합니다.
    - 그러면 /대상 디렉터리에 `msal-web-sample-0.1.0.war` 파일이 생성됩니다.
    - 이 파일의 이름을 `msal4jsample.war`로 변경
    - Tomcat 또는 기타 J2EE 컨테이너 솔루션을 사용하여 이 war 파일을 배포합니다.
        - 배포하려면 msal4jsample.war 파일을 Tomcat 설치의 `/webapps/` 디렉터리에 복사한 다음, Tomcat 서버를 시작합니다.

5. 배포되면 브라우저에서 https://localhost:8443/msal4jsample 로 이동합니다.


> [!IMPORTANT]
> 이 빠른 시작 애플리케이션에서는 클라이언트 비밀을 사용하여 자체를 기밀 클라이언트로 식별합니다. 클라이언트 암호는 보안상의 이유로 프로젝트 파일에 일반 텍스트로 추가되므로, 이 애플리케이션을 프로덕션 애플리케이션으로 사용하는 방안을 고려하기 전에 클라이언트 암호 대신 인증서를 사용하는 것이 좋습니다. 인증서를 사용하는 방법에 대한 자세한 내용은 [애플리케이션 인증을 위한 인증서 자격 증명](./active-directory-certificate-credentials.md)을 참조하세요.

## <a name="more-information"></a>자세한 정보

### <a name="how-the-sample-works"></a>샘플 작동 방법
![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>MSAL 가져오기

Java용 MSAL(MSAL4J)은 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 Java 라이브러리입니다.

Maven이나 Gradle을 사용하여 MSAL4J를 애플리케이션에 추가하고 애플리케이션의 pom.xml(Maven) 또는 build.gradle(Gradle) 파일을 다음과 같이 변경하여 종속성을 관리합니다.

pom.xml에서:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

build.gradle에서:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL 초기화

MSAL4J를 사용할 파일 맨 위에 다음 코드를 추가하여 Java용 MSAL에 대한 참조를 추가합니다.

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>다음 단계

권한 및 동의에 대한 자세한 정보:

> [!div class="nextstepaction"]
> [권한 및 동의](./v2-permissions-and-consent.md)

이 시나리오의 인증 흐름에 대해 알아보려면 Oauth 2.0 인증 코드 흐름을 참조하세요.

> [!div class="nextstepaction"]
> [인증 코드 Oauth 흐름](./v2-oauth2-auth-code-flow.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]