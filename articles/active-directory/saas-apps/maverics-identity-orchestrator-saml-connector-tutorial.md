---
title: '자습서: Maverics Identity Orchestrator SAML Connector와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Maverics Identity Orchestrator SAML Connector 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: jeedes
ms.openlocfilehash: ec5368427f50f548be965bb883683c859759bbf3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88518931"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maverics-identity-orchestrator-saml-connector"></a>자습서: Maverics Identity Orchestrator SAML Connector와 Azure Active Directory SSO(Single Sign-On)

## <a name="introduction"></a>소개

Strata는 인증 및 액세스 제어를 위해 온-프레미스 애플리케이션을 Azure AD와 통합하는 간단한 방법을 제공합니다.

이 가이드에서는 다음과 같은 작업을 수행하도록 Maverics Id Orchestrator&trade;를 구성하는 방법을 안내합니다.
* 레거시 온-프레미스 애플리케이션에 로그인한 동안 사용자를 온-프레미스 ID 시스템에서 Azure AD로 점진적으로 마이그레이션합니다.
* CA SiteMinder 또는 Oracle Access Manager 같은 레거시 웹 액세스 관리 제품의 로그인 요청을 Azure AD로 라우팅합니다.
* 사용자를 Azure AD에 대해 인증한 후 http 헤더 또는 독점적 세션 쿠키를 사용하여 보호되는 온-프레미스 애플리케이션에 사용자를 인증합니다.

Strata는 온-프레미스 또는 클라우드에 배포하면 ID 공급자를 검색, 연결 및 오케스트레이션하여 하이브리드 및 다중 클라우드 엔터프라이즈용 분산 ID 관리를 만드는 소프트웨어를 제공합니다.

이 자습서에서는 인증 및 액세스 제어에 Azure AD를 사용하도록 기존 웹 액세스 관리 제품(CA SiteMinder)으로 보호되는 온-프레미스 웹 애플리케이션을 마이그레이션하는 방법을 보여줍니다.
1. Maverics Identity Orchestrator 설치&trade;
2. 엔터프라이즈 애플리케이션을 Azure AD에 등록하고 Maverics Azure AD SAML Zero Code Connector&trade;를 SAML 기반 SSO에 사용하도록 구성합니다.
3. Maverics를 SiteMinder 및 LDAP 사용자 저장소와 통합합니다.
4. Azure Key Vault를 설정하고, Azure Key Vault를 비밀 관리 공급자로 사용하도록 Maverics를 구성합니다.
5. 온-프레미스 Java 웹 애플리케이션에 대한 액세스를 제공하도록 Maverics를 사용하여 사용자 마이그레이션 및 세션 추상화를 수행하는 방법을 보여줍니다.

설치 및 구성과 관련된 추가 지침은 https://strata.io/docs 에서 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- Maverics Identity Orchestrator SAML Connector SSO(Single Sign-On)가 설정된 구독. Maverics 소프트웨어를 얻으려면 sales@strata.io에 문의하세요.

## <a name="install-maverics-identity-orchestratortrade"></a>Maverics Identity Orchestrator 설치&trade;

Maverics Identity Orchestrator 설치를 시작하려면 https://strata.io/docs 의 설치 지침을 참조하세요.

## <a name="system-requirements"></a>시스템 요구 사항
### <a name="supported-operating-systems"></a>지원되는 운영 체제
* RHEL 7+
* CentOS 7+

### <a name="dependencies"></a>종속성
* systemd

## <a name="installation"></a>설치

1. 최신 Maverics RPM 패키지를 받습니다. Maverics 소프트웨어를 설치하려는 시스템에 이 패키지를 복사합니다.

2. Maverics 패키지를 설치하고, `maverics.rpm`을 해당 파일 이름으로 바꿉니다.

    `sudo rpm -Uvf maverics.rpm`

3. 설치가 완료되면 Maverics는 `systemd` 아래에서 서비스로 실행됩니다. 서비스가 실행 중인지 확인하려면 다음 명령을 실행합니다.

    `sudo systemctl status maverics`

기본적으로 Maverics는 `/usr/local/bin` 디렉터리에 설치됩니다.

Maverics를 설치한 후에는 기본 `maverics.yaml` 파일이 `/etc/maverics` 디렉터리에 만들어집니다. `workflows` 및 `connectors`를 포함하도록 구성을 편집하기 전의 구성 파일은 다음과 같습니다.

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```
## <a name="config-options"></a>구성 옵션
### <a name="version"></a>Version
`version` 필드는 사용할 구성 파일의 버전을 선언합니다. 이 필드를 지정하지 않으면 가장 최근의 구성 버전이 사용됩니다.

```yaml
version: 0.1
```
### <a name="listen-address"></a>Listen Address
`listenAddress`는 Orchestrator가 수신 대기하는 주소를 선언합니다. 주소의 호스트 섹션이 비어 있으면 Orchestrator는 로컬 시스템의 모든 사용 가능한 유니캐스트 및 애니캐스트 IP 주소에서 수신 대기합니다. 주소의 포트 섹션이 비어 있으면 포트 번호가 자동으로 선택됩니다.

```yaml
listenAddress: ":453"
```
### <a name="tls"></a>TLS

`tls` 필드는 전송 계층 보안 개체의 맵을 선언합니다. TLS 개체는 커넥터 및 Orchestrator 서버에서 사용할 수 있습니다. 사용 가능한 모든 TLS 옵션은 `transport`의 패키지 설명서를 참조하세요.

SAML 기반 SSO를 사용하는 경우 Microsoft Azure는 TLS를 통한 통신이 필요합니다. 인증서 생성에 대한 자세한 내용은 [여기](https://letsencrypt.org/getting-started/)서 찾을 수 있습니다.

`maverics` 키는 Orchestrator 서버용으로 예약되어 있습니다. 그 외의 모든 키는 사용 가능하며 지정된 커넥터에 TLS 개체를 삽입하는 데 사용할 수 있습니다.

```yaml
tls:
  maverics:
    certFile: /etc/maverics/maverics.cert
    keyFile: /etc/maverics/maverics.key
```  
### <a name="include-files"></a>포함 파일

`connectors` 및 `workflows`는 다음 예제에 따라 `includeFiles`를 사용하여 개별 구성 파일에서 정의하고 `maverics.yaml`에서 참조할 수 있습니다.
```yaml
includeFiles:
  - workflow/sessionAbstraction.yaml
  - connector/AzureAD-saml.yaml
  - connector/siteminder.yaml
  ```

이 자습서에서는 단일 `maverics.yaml` 구성 파일을 사용합니다.

## <a name="using-azure-key-vault-as-your-secrets-provider"></a>비밀 공급자로 Azure Key Vault 사용

### <a name="secret-management"></a>비밀 관리

Maverics는 다양한 비밀 관리 솔루션과 통합하여 비밀을 로드할 수 있습니다. 현재 통합에는 파일, Hashicorp Vault 및 Azure Key Vault가 포함됩니다. 비밀 관리 솔루션이 지정되지 않으면 Maverics는 기본적으로 `maverics.yaml`의 일반 텍스트로 비밀을 로드합니다.
`maverics.yaml` 구성 파일에서 값을 비밀로 선언하려면 비밀을 꺾쇠 괄호로 묶습니다.

  ```yaml
  connectors:
  - name: AzureAD
    type: AzureAD
    apiToken: <AzureADAPIToken>
    oauthClientID: <AzureADOAuthClientID>
    oauthClientSecret: <AzureADOAuthClientSecret>
  ```

### <a name="file"></a>파일

파일의 비밀을 로드하려면 다음 명령을 사용하여 `/etc/maverics/maverics.env` 파일에 `MAVERICS_SECRET_PROVIDER` 환경 변수를 추가합니다.

`MAVERICS_SECRET_PROVIDER=secretfile:///<PATH TO SECRETS FILE>`

그리고 `sudo systemctl restart maverics` 명령을 사용하여 maverics 서비스를 다시 시작합니다.

`secrets.yaml` 파일 콘텐츠에 `secrets`를 원하는 만큼 채울 수 있습니다.
```yaml
secrets:
  AzureADAPIToken: aReallyGoodToken
  AzureADOAuthClientID: aReallyUniqueID
  AzureADOAuthClientSecret: aReallyGoodSecret
```
### <a name="azure-key-vault"></a>Azure Key Vault

다음 단계에서는 [Azure Portal](https://portal.azure.com) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 Azure Key Vault를 설정하는 방법을 보여줍니다.

1. Azure Portal 또는 다음 CLI 명령을 사용하여 [로그인](https://portal.azure.com)합니다.
    ```shell
    az login
    ```

2. [새 자격 증명 모음을 만들거나](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) 다음 CLI 명령을 사용합니다.
    ```shell
    az keyvault create --name "[VAULT_NAME]" --resource-group "[RESOURCE_GROUP]" --location "[REGION]"
    ```

3. [Key Vault에 비밀을 추가하거나](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) 다음 CLI 명령을 사용합니다.
    ```shell
    az keyvault secret set --vault-name "[VAULT_NAME]" --name "[SECRET_NAME]" --value "[SECRET_VALUE]"
    ```

4. [애플리케이션을 Azure Active Directory에 등록](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application)하거나 다음 CLI 명령을 사용합니다.
    ```shell
    az ad sp create-for-rbac -n "MavericsKeyVault" --skip-assignment > azure-credentials.json
    ```

5. [비밀을 사용할 수 있도록 애플리케이션에 권한을 부여](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault)하거나 다음 CLI 명령을 사용합니다.
    ```shell
    az keyvault set-policy --name "[VAULT_NAME]" --spn [APPID] --secret-permissions list get
    #APPID can be found in the azure-credentials.json
    generated in the previous step
    ```

Azure Key Vault에서 비밀을 로드하려면 `MAVERICS_SECRET_PROVIDER='azurekeyvault://<KEYVAULT NAME>.vault.azure.net?clientID=<APPID>&clientSecret=<PASSWORD>&tenantID=<TENANT>'` 패턴을 사용하여 azure-credentials.json 파일에 있는 자격 증명을 사용하여 `/etc/maverics/maverics.env` 파일의 `MAVERICS_SECRET_PROVIDER` 환경 변수를 설정합니다.

그리고 `sudo systemctl restart maverics` 명령을 사용하여 maverics 서비스를 다시 시작합니다.

## <a name="configure-your-application-in-azure-ad-for-saml-based-sso"></a>Azure AD에서 SAML 기반 SSO를 사용하도록 애플리케이션 구성

1. Azure Active Directory 테넌트에서 `Enterprise applications`으로 이동하여 `Maverics Identity Orchestrator SAML Connector`를 검색하고 선택합니다.

2. 'Maverics Identity Orchestrator SAML Connector' | 속성 페이지에서 `User assignment required?`를 [아니요]로 설정하여 애플리케이션이 새로 마이그레이션된 사용자에 대해 작동하도록 설정합니다.

3. 'Maverics Identity Orchestrator SAML Connector' | 개요 페이지에서 `Setup single sign-on`을 선택하고 `SAML`을 선택합니다.

4. 'Maverics Identity Orchestrator SAML Connector' | SAML 기반 로그온에서 기본 SAML 구성을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

5. `https://<SUBDOMAIN>.maverics.org` 패턴을 사용하여 `Entity ID`를 설정하고 URL을 입력합니다. `Entity ID`는 테넌트의 앱에서 고유해야 합니다. Maverics의 구성에 포함되도록 여기에 입력된 값을 저장합니다.

6. `https://<AZURECOMPANY.COM>/<MY_APP>/` 패턴을 사용하여 회신 URL을 설정합니다. 

7. `https://<AZURE-COMPANY.COM>/<MY_APP>/<LOGIN PAGE>` 패턴을 사용하여 로그온 URL을 설정하고 [저장]을 클릭합니다.

8. [SAML 서명 인증서] 섹션으로 이동한 다음, [복사] 단추를 클릭하여 [앱 페더레이션 메타데이터 URL]을 복사하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

## <a name="maverics-identity-orchestrator-azure-ad-saml-connector-configuration"></a>Maverics Identity Orchestrator Azure AD SAML Connector 구성

Maverics Identity Orchestrator Azure AD Connector는 다음을 지원합니다. 
- OpenID Connect
- SAML 연결 

1. SAML 기반 SSO를 사용하려면 `authType: saml`을 설정합니다.

1. `samlMetadataURL` 값으로 `samlMetadataURL:https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>`를 만듭니다.

1. 이제 사용자가 Azure 자격 증명을 사용하여 로그인한 후 Azure가 앱에서 다시 리디렉션될 URL을 정의합니다.
`samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>`

1. 위에서 구성한 EntityID의 값 `samlEntityID: https://<SUBDOMAIN>.maverics.org`를 복사합니다.

1. Azure AD에서 SAML 응답을 게시하는 데 사용할 회신 URL 값을 복사합니다.
`samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>`

1. [OpenSSL 도구](https://www.openssl.org/source/)를 사용하여 Maverics Identity Orchestrator&trade; 세션 정보를 보호하는 데 사용되는 JWT 서명 키를 생성합니다.

    ```shell 
    openssl rand 64 | base64
    ```
1. `jwtSigningKey` config 속성에 대한 응답 `jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==`를 복사합니다.

## <a name="attributes-and-attribute-mapping"></a>특성 및 특성 매핑
특성 매핑은 사용자가 프로비저닝될 때 원본 온-프레미스 사용자 디렉터리에서 Azure AD로 사용자 특성 매핑을 정의하는 데 사용됩니다.

특성은 클레임의 애플리케이션에 반환되거나, 세션 쿠키에 전달되거나, http 헤더 변수의 애플리케이션에 전달될 수 있는 사용자 데이터를 결정합니다.

## <a name="configure-maverics-identity-orchestrator-azure-ad-saml-connector-yaml"></a>Maverics Identity Orchestrator Azure AD SAML Connector yaml 구성

Maverics Identity Orchestrator Azure AD Connector 구성은 다음과 같습니다.
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: <SIGNING KEY>
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="migrate-users-to-azure-ad"></a>Azure AD로 사용자 마이그레이션

이 구성을 따라 CA SiteMinder, Oracle Access Manager, IBM Tivoli 등의 웹 액세스 관리 제품, LDAP 디렉터리 또는 SQL 데이터베이스의 사용자를 점진적으로 마이그레이션할 수 있습니다.

## <a name="configure-your-application-permissions-in-azure-ad-to-create-users"></a>사용자를 만들기 위해 Azure AD에서 애플리케이션 권한 구성

1. Azure Active Directory 테넌트에서 `App registrations`로 이동하여 'Maverics Identity Orchestrator SAML Connector' 애플리케이션을 선택합니다.

2. 'Maverics Identity Orchestrator SAML Connector' | 인증서 및 비밀에서 `New client secret`을 선택한 다음, 만료 옵션을 선택합니다. [복사] 단추를 클릭하여 비밀을 복사하고 컴퓨터에 저장합니다.

3. 'Maverics Identity Orchestrator SAML Connector' | API 권한에서 `Add permission`을 선택하고, [API 권한 요청]에서 `Microsoft Graph`를 선택한 다음, `Application permissions`를 선택합니다. 다음 화면에서 `User.ReadWrite.All`을 선택한 다음, `Add permissions`를 선택합니다. 그러면 다시 API 권한 화면으로 이동됩니다. 이 화면에서 `Grant admin consent`를 선택합니다.


## <a name="configure-the-maverics-identity-orchestrator-saml-connector-yaml-for-user-migration"></a>사용자 마이그레이션을 위해 Maverics Identity Orchestrator SAML Connector yaml 구성

사용자 마이그레이션 워크플로를 사용하려면 구성 파일에 다음 속성을 추가합니다.
1. Azure Graph URL `graphURL: https://graph.microsoft.com`을 설정합니다.
1. `oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>` 패턴을 사용하여 OAuth 토큰 URL을 설정합니다.
1. 위에서 생성한 클라이언트 암호 `oauthClientSecret: <CLIENT SECRET>`을 설정합니다.


최종 Maverics Identity Orchestrator Azure AD Connector 구성은 다음과 같습니다.
```yaml
- name: AzureAD
  type: azure
  authType: saml
  samlMetadataURL: https://login.microsoftonline.com/<TENANT ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<APP ID>
  samlRedirectURL: https://<AZURECOMPANY.COM>/<MY_APP>
  samlConsumerServiceURL: https://<AZURE-COMPANY.COM>/<MY_APP>
  jwtSigningKey: TBHPvTtu6NUqU84H3Q45grcv9WDJLHgTioqRhB8QGiVzghKlu1mHgP1QHVTAZZjzLlTBmQwgsSoWxGHRcT4Bcw==
  samlEntityID: https://<SUBDOMAIN>.maverics.org
  graphURL: https://graph.microsoft.com
  oauthTokenURL: https://login.microsoftonline.com/<TENANT ID>/oauth2/v2.0/token
  oauthClientID: <APP ID>
  oauthClientSecret: <NEW CLIENT SECRET>
  attributeMapping:
    displayName: username
    mailNickname: givenName
    givenName: givenName
    surname: sn
    userPrincipalName: mail
    password: password
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-siteminder"></a>SiteMinder에 대한 Maverics Zero Code Connector&trade; 구성

SiteMinder 커넥터는 사용자를 Azure AD로 마이그레이션하고, 새로 만든 Azure AD ID 및 자격 증명을 사용하여 SiteMinder로 보호되는 레거시 온-프레미스 애플리케이션에 사용자를 로그인하는 데 사용됩니다.

이 자습서에서는 양식 기반 인증과 `SMSESSION` 쿠키를 사용하여 레거시 애플리케이션을 보호하도록 SiteMinder가 구성되었습니다. http 헤더를 통해 인증 및 세션을 사용하는 앱과 통합하려면 헤더 에뮬레이션 구성을 커넥터에 추가해야 합니다.

이 예제에서는 다음과 같이 `username` 특성을 `SM_USER` http 헤더에 매핑합니다.
```yaml
  headers:
    SM_USER: username
```

`proxyPass`를 요청이 프록시되는 위치로 설정합니다. 일반적으로 보호된 애플리케이션의 호스트입니다.

`loginPage`는 인증을 위해 사용자를 리디렉션할 때 SiteMinder에서 현재 사용하는 로그인 양식의 URL과 일치해야 합니다.

```yaml
connectors:
- name: siteminder-login-form
  type: siteminder
  loginType: form
  loginPage: /siteminderagent/forms/login.fcc
  proxyPass: http://host.company.com
```

## <a name="configure-the-maverics-zero-code-connectortrade-for-ldap"></a>LDAP에 대한 Maverics Zero Code Connector&trade; 구성

애플리케이션이 SiteMinder 같은 WAM 제품으로 보호되는 경우 사용자 ID 및 특성은 일반적으로 LDAP 디렉터리에 저장됩니다.

이 커넥터 구성은 마이그레이션 워크플로 중에 올바른 사용자 프로필 정보를 수집하고 Azure AD에서 해당 사용자를 만들 수 있도록 SiteMinder에 대한 사용자 저장소로 구성된 LDAP 디렉터리에 연결하는 방법을 보여줍니다.

* `baseDN`은 LDAP 검색을 수행할 디렉터리의 위치를 지정합니다.

* `url`은 연결할 LDAP 서버의 주소 및 포트입니다.

* `serviceAccountUsername`은 LDAP 서버에 연결하는 데 사용되는 사용자 이름으로, 일반적으로 바인딩 DN으로 표현됩니다(예: `CN=Directory Manager`).

* `serviceAccountPassword`는 LDAP 서버 연결에 사용되는 암호입니다. 이 값은 이전에 구성한 Azure Key Vault 인스턴스에 저장됩니다.  

* `userAttributes`는 쿼리할 사용자 관련 특성 목록을 정의합니다. 이러한 특성은 나중에 해당하는 Azure AD 특성에 매핑됩니다.

```yaml
- name: company-ldap
  type: ldap
  url: "ldap://ldap.company.com:389"
  baseDN: ou=People,o=company,c=US
  serviceAccountUsername: uid=admin,ou=Admins,o=company,c=US
  serviceAccountPassword: <vaulted-password>
  userAttributes:
    - uid
    - cn
    - givenName
    - sn
    - mail
    - mobile
```

## <a name="configure-the-migration-workflow"></a>마이그레이션 워크플로 구성

마이그레이션 워크플로 구성은 Maverics가 SiteMinder/LDAP에서 Azure AD로 사용자를 마이그레이션하는 방법을 결정합니다.

이 워크플로는 다음과 같은 작업을 수행합니다.
- SiteMinder 커넥터를 사용하여 SiteMinder 로그인을 프록시합니다. 사용자 자격 증명은 SiteMinder 인증을 통해 유효성 검사 후 워크플로의 후속 단계에 전달됩니다.
- SiteMinder 사용자 저장소에서 사용자 프로필 특성을 검색합니다.
- Azure AD 테넌트에 사용자를 만들어 달라고 Microsoft Graph API에 요청합니다.

단계:
1. 워크플로의 이름을 지정합니다(예: SiteMinder에서 Azure AD로 마이그레이션).
2. 요청에 대한 응답으로 해당 워크플로의 `actions`를 트리거하는 워크플로가 노출되는 HTTP 경로인 `endpoint`를 지정합니다. `endpoint`는 일반적으로 프록시되는 애플리케이션(예: `/my_app`)에 해당합니다. 이 값은 선행 슬래시와 후행 슬래시를 모두 포함해야 합니다.
3. 워크플로에 적절한 `actions`를 추가합니다.
    - SiteMinder 커넥터에 대한 `login` 메서드를 정의합니다. 커넥터 값은 커넥터 구성의 이름 값과 일치해야 합니다.
     - LDAP 커넥터에 대한 `getprofile` 메서드를 정의합니다.
     - AzureAD 커넥터에 대한 `createuser`를 정의합니다.

    ```yaml
      workflows:
      - name: SiteMinder to Azure AD Migration
        endpoint: /my_app/
        actions:
        - connector: siteminder-login-form
          method: login
        - connector: company-ldap
          method: getprofile
        - connector: AzureAD
          method: createuser
    ```
### <a name="verify-the-migration-workflow"></a>마이그레이션 워크플로 확인

1. Maverics 서비스가 아직 실행되고 있지 않은 경우 `sudo systemctl start maverics` 명령을 실행하여 시작합니다.

2. 프록시된 로그인 URL `http://host.company.com/my_app`으로 이동합니다.
3. SiteMinder로 보호되는 동안 애플리케이션에 로그인하는 데 사용되는 사용자 자격 증명을 입력합니다.
4. 홈 > 사용자 | 모든 사용자로 이동하여 Azure AD 테넌트에 사용자가 만들어졌는지 확인합니다.  

## <a name="configure-the-session-abstraction-workflow"></a>세션 추상화 워크플로 구성

세션 추상화 워크플로는 레거시 온-프레미스 웹 애플리케이션에 대한 인증 및 액세스 제어를 Azure AD로 이동합니다.

Azure 커넥터는 `login` 메서드를 사용하여 사용자를 로그인 URL로 리디렉션하며, 세션이 없다고 가정합니다.

인증되면 그 결과로 생성된 세션 토큰이 Maverics로 전달되고, SiteMinder 커넥터의 `emulate` 메서드를 사용하여 쿠키 기반 세션 및/또는 헤더 기반 세션을 에뮬레이션한 다음, 애플리케이션에 필요한 추가 특성으로 요청을 데코레이트합니다.

1. 워크플로의 이름을 지정합니다(예: SiteMinder 세션 추상화).
2. 프록시되는 앱에 해당하는 `endpoint`를 지정합니다. 이 값은 선행 슬래시와 후행 슬래시를 모두 포함해야 합니다(예: `/my_app/`).
3. 워크플로에 적절한 `actions`를 추가합니다.
    - Azure 커넥터에 대한 `login` 메서드를 정의합니다. `connector` 값은 커넥터 구성의 `name` 값과 일치해야 합니다.
    - SiteMinder 커넥터에 대한 `emulate` 메서드를 정의합니다.

     ```yaml
      - name: SiteMinder Session Abstraction
        endpoint: /my_app/
        actions:
      - connector: azure
        method: login
      - connector: siteminder-login-form
        method: emulate
     ```
### <a name="verify-the-session-abstraction-workflow"></a>세션 추상화 워크플로 확인

1. 프록시된 애플리케이션 URL `https://<AZURECOMPANY.COM>/<MY_APP>`으로 이동합니다. 사용자가 프록시된 로그인 페이지로 리디렉션됩니다.
2. Azure AD 사용자 자격 증명을 입력합니다.
3. SiteMinder에서 직접 인증한 것처럼 사용자가 애플리케이션으로 리디렉션됩니다.
