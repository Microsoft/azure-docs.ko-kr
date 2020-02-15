---
title: 사용자 지정 Api에 대 한 보안 호출에 대 한 인증 추가
description: Azure Logic Apps에서 사용자 지정 Api 호출에 대 한 보안을 향상 시키기 위해 인증을 설정 하는 방법
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 110a684cf6ad21c13411d3bc2ada84750744f00e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191399"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Azure Logic Apps에서 사용자 지정 Api 호출에 대 한 보안 강화

Api 호출에 대 한 보안을 향상 시키려면 Azure Portal를 통해 Azure Active Directory (Azure AD) 인증을 설정할 수 있으므로 코드를 업데이트할 필요가 없습니다. 또는 API 코드를 통해 인증을 요구하고 적용할 수 있습니다.

## <a name="authentication-options-for-your-api"></a>API에 대한 인증 옵션

다음과 같은 방법으로 사용자 지정 API에 대 한 호출에 대 한 보안을 향상 시킬 수 있습니다.

* [코드 변경 없음](#no-code) - Azure Portal을 통해 [Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md)로 API를 보호하므로 코드를 업데이트하거나 API를 다시 배포할 필요가 없습니다.

  > [!NOTE]
  > 기본적으로 Azure Portal에서 설정하는 Azure AD 인증은 세분화된 권한 부여를 수행하지 않습니다. 예를 들어 이 인증을 통해 특정 사용자 또는 앱이 아니라 특정 테넌트에 대한 API를 잠급니다. 

* [API 코드 업데이트](#update-code) - 코드를 통해 [인증서 인증](#certificate), [기본 인증](#basic) 또는 [Azure AD 인증](#azure-ad-code)을 적용하여 API를 보호합니다.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>코드를 변경하지 않고 API에 대한 호출 인증

이 방법의 일반적인 단계는 다음과 같습니다.

1. 두 개의 Azure AD(Azure Active Directory) 애플리케이션 ID를 만듭니다. 하나는 논리 앱용이고 다른 하나는 웹앱용(또는 API 앱용)입니다.

2. API에 대한 호출을 인증하려면 논리 앱의 Azure AD 애플리케이션 ID와 연결된 서비스 주체의 자격 증명(클라이언트 ID 및 비밀)을 사용합니다.

3. 논리 앱 정의에 애플리케이션 ID를 포함합니다.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1부: 논리 앱의 Azure AD 애플리케이션 ID 만들기

논리 앱은 이 Azure AD 애플리케이션 ID를 사용하여 Azure AD에 대해 인증합니다. 디렉터리에 대해 이 ID를 한 번만 설정하면 됩니다. 예를 들어 논리 앱마다 고유한 ID를 만들 수 있더라도 모든 논리 앱에 대해 동일한 ID를 사용하도록 선택할 수 있습니다. Azure Portal에서 또는 [PowerShell](#powershell)을 사용하여 이러한 ID를 설정할 수 있습니다.

**Azure Portal에서 논리 앱에 대한 애플리케이션 ID 만들기**

1. [Azure Portal](https://portal.azure.com "https://portal.azure.com/")에서 **Azure Active Directory**를 선택 합니다. 

2. 웹앱 또는 API 앱과 동일한 디렉터리에 있는지 확인합니다.

   > [!TIP]
   > 디렉터리를 전환하려면 프로필을 선택하고 다른 디렉터리를 선택합니다. 또는 **개요** > **디렉터리 전환**을 차례로 선택합니다.

3. 디렉터리 메뉴의 **관리** 아래에서 **앱 등록** > **새 애플리케이션 등록**을 차례로 선택합니다.

   > [!TIP]
   > 기본적으로 앱 등록 목록에는 디렉터리의 모든 앱 등록이 표시됩니다. 앱 등록만 보려면 검색 상자 옆에 있는 **내 앱**을 선택합니다. 

   ![새 앱 등록 만들기](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. 애플리케이션 ID의 이름을 지정하고, **애플리케이션 종류**를 **웹앱/API**로 설정하며, **로그인 URL**의 도메인으로 형식이 지정된 고유 문자열을 제공하고, **만들기**를 선택합니다.

   ![애플리케이션 ID의 이름 및 로그인 URL 제공](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   논리 앱에 대해 만든 애플리케이션 ID가 이제 앱 등록 목록에 표시됩니다.

   ![논리 앱의 애플리케이션 ID](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. 앱 등록 목록에서 새 애플리케이션 ID를 선택합니다. 3부에서 논리 앱에 대한 "클라이언트 ID"로 사용할 **애플리케이션 ID**를 복사하여 저장합니다.

   ![논리 앱의 애플리케이션 ID 복사 및 저장](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. 애플리케이션 ID 설정이 표시되지 않으면 **설정** 또는 **모든 설정**을 선택합니다.

7. **API 액세스** 아래에서 **키**를 선택합니다. **설명** 아래에서 키의 이름을 입력합니다. **만료** 아래에서 키의 기간을 선택합니다.

   만드는 키는 논리 앱에 대한 애플리케이션 ID의 "비밀" 또는 암호 역할을 합니다.

   ![논리 앱 ID의 키 만들기](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. 도구 모음에서 **저장**을 선택합니다. 이제 **값** 아래에서 키가 표시됩니다. 
**키** 페이지를 나갈 때 키가 숨겨지므로 나중에 사용할 수 있도록 **키를 복사하여 저장해야 합니다**.

   3부에서 논리 앱을 구성할 때 이 키를 "비밀" 또는 암호로 지정합니다.

   ![나중을 위한 키 복사 및 저장](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**PowerShell에서 논리 앱의 애플리케이션 ID 만들기**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell과 함께 Azure Resource Manager를 통해 이 작업을 수행할 수 있습니다. PowerShell에서 다음 명령을 실행합니다.

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. 암호를 입력 하 고 enter 키를 누릅니다.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. 사용한 **테넌트 ID**(Azure AD 테넌트의 GUID), **애플리케이션 ID** 및 암호를 복사합니다.

자세한 내용은 [PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../active-directory/develop/howto-authenticate-service-principal-powershell.md)를 참조하세요.

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2부: 웹앱 또는 API 앱의 Azure AD 애플리케이션 ID 만들기

웹앱 또는 API 앱이 이미 배포된 경우 Azure Portal에서 인증을 설정하고 애플리케이션 ID를 만들 수 있습니다. 그렇지 않으면 [Azure Resource Manager 템플릿으로 배포할 때 인증을 설정](#authen-deploy)할 수 있습니다. 

**Azure Portal에서 배포된 앱의 애플리케이션 ID 만들기 및 인증 설정**

1. [Azure Portal](https://portal.azure.com "https://portal.azure.com/")에서 웹 앱 또는 API 앱을 찾아 선택 합니다. 

2. **설정**에서 **인증/권한 부여**를 선택합니다. **App Service 인증** 아래에서 인증을 **설정**으로 지정합니다. **인증 공급자** 아래에서 **Azure Active Directory**를 선택합니다.

   ![인증 설정](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. 이제 다음과 같이 웹앱 또는 API 앱의 애플리케이션 ID를 만듭니다. **Azure Active Directory 설정** 페이지에서 **관리 모드**를 **기본**으로 설정합니다. **새 AD 앱 만들기**를 선택합니다. 애플리케이션 ID의 이름을 지정하고 **확인**을 선택합니다. 

   ![웹앱 또는 API 앱의 애플리케이션 ID 만들기](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. **인증/권한 부여** 페이지에서 **저장**을 선택합니다.

이제 웹앱 또는 API 앱과 연결된 애플리케이션 ID에 대한 클라이언트 ID 및 테넌트 ID를 찾아야 합니다. 이러한 ID는 3부에서 사용됩니다. 따라서 Azure Portal에 대해 이러한 단계를 계속합니다.

**Azure Portal에서 웹앱 또는 API 앱에 대한 애플리케이션 ID의 클라이언트 ID 및 테넌트 ID 찾기**

1. **인증 공급자** 아래에서 **Azure Active Directory**를 선택합니다. 

   !["Azure Active Directory"를 선택합니다.](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. **Azure Active Directory 설정** 페이지에서 **관리 모드**를 **고급**으로 설정합니다.

3. **클라이언트 ID**를 복사하고 3부에서 사용할 해당 GUID를 저장합니다.

   > [!TIP] 
   > **클라이언트 ID** 및 **발급자 URL**이 표시되지 않으면 Azure Portal을 새로 고치고 1단계를 반복합니다.

4. **발급자 URL** 아래에서 3부에서 사용할 GUID만 복사하여 저장합니다. 필요한 경우 웹앱 또는 API 앱의 배포 템플릿에서 이 GUID를 사용할 수도 있습니다.

   이 GUID는 특정 테넌트의 GUID("테넌트 ID")이며 `https://sts.windows.net/{GUID}` URL에 표시되어야 합니다.

5. 변경 내용을 저장하지 않고 **Azure Active Directory 설정** 페이지를 닫습니다.

<a name="authen-deploy"></a>

**Azure Resource Manager 템플릿으로 배포할 때 인증 설정**

논리 앱에 대 한 앱 id와 다른 API 앱 또는 웹 앱에 대 한 Azure AD 응용 프로그램 id를 만들어야 합니다. 애플리케이션 ID를 만들려면 Azure Portal에 대해 2부의 이전 단계를 수행합니다. 

1부의 단계를 수행할 수도 있지만 `https://{URL}`로그인 URL**및**앱 ID URI**에 대한 웹앱 또는 API 앱의 실제** 을 사용해야 합니다. 이러한 단계에서 앱의 배포 템플릿 및 3부에서 사용할 클라이언트 ID와 테넌트 ID를 모두 저장해야 합니다.

> [!NOTE]
> 웹앱 또는 API 앱의 Azure AD 애플리케이션 ID를 만들 때는 PowerShell이 아닌 Azure Portal을 사용해야 합니다. PowerShell commandlet은 웹 사이트에 사용자가 로그인하는 데 필요한 권한을 설정하지 않습니다.

클라이언트 ID와 테넌트 ID를 가져온 후에는 이러한 ID를 웹앱 또는 API 앱의 하위 리소스로 배포 템플릿에 포함합니다.

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Azure Active Directory 인증과 함께 빈 웹앱과 논리 앱을 자동으로 배포하려면 [여기에서 전체 템플릿을 보거나](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json) 아래의 **Azure에 배포**를 클릭합니다.

[![Azure에 배포](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3부: 논리 앱의 권한 부여 섹션 채우기

이전 템플릿에는 이 권한 부여 섹션이 설정되어 있지만, 논리 앱을 직접 작성하는 경우 전체 권한 부여 섹션을 포함해야 합니다.

코드 보기에서 논리 앱 정의를 열고, **HTTP** 작업 정의로 이동 하 고, **권한 부여** 섹션을 찾고, 다음과 같은 속성을 포함 합니다.

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| 속성 | 필수 | Description | 
| -------- | -------- | ----------- | 
| tenant | yes | Azure AD 테넌트의 GUID | 
| 대상 | yes | 액세스하려는 대상 리소스의 GUID, 즉 웹앱 또는 API 앱에 대한 애플리케이션 ID의 클라이언트 ID | 
| clientId | yes | 액세스를 요청하는 클라이언트의 GUID, 즉 논리 앱에 대한 애플리케이션 ID의 클라이언트 ID | 
| secret | yes | 액세스 토큰을 요청하는 클라이언트에 대한 애플리케이션 ID의 키 또는 암호 | 
| type | yes | 인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. | 
|||| 

다음은 그 예입니다.

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>코드를 통한 API 호출 보호

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>인증서 인증

논리 앱에서 웹앱 또는 API 앱으로 들어오는 요청의 유효성을 검사하기 위해 클라이언트 인증서를 사용할 수 있습니다. 코드를 설정하려면 [TLS 상호 인증을 구성하는 방법](../app-service/app-service-web-configure-tls-mutual-auth.md)을 참조하세요.

**권한 부여** 섹션에서 다음 속성을 포함 합니다.

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| 속성 | 필수 | Description |
| -------- | -------- | ----------- |
| `type` | yes | 인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`여야 합니다. |
| `password` | 예 | 클라이언트 인증서(PFX 파일)에 액세스하기 위한 암호 |
| `pfx` | yes | 클라이언트 인증서(PFX 파일)의 Base64로 인코딩된 콘텐츠 |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>기본 인증

논리 앱에서 웹앱 또는 API 앱으로 들어오는 요청의 유효성을 검사하기 위해 사용자 이름 및 암호와 같은 기본 인증을 사용할 수 있습니다. 기본 인증은 일반적인 패턴이며, 웹앱 또는 API 앱을 빌드하는 데 사용되는 언어에서 이 인증을 사용할 수 있습니다.

**권한 부여** 섹션에서 다음 속성을 포함 합니다.

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| 속성 | 필수 | Description | 
| -------- | -------- | ----------- | 
| type | yes | 사용할 인증 유형입니다. 기본 인증의 경우 값은 `Basic`이어야 합니다. | 
| 사용자 이름 | yes | 인증에 사용할 사용자 이름 | 
| password | yes | 인증에 사용할 암호 | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>코드를 통한 Azure Active Directory 인증

기본적으로 Azure Portal에서 설정하는 Azure AD 인증은 세분화된 권한 부여를 수행하지 않습니다. 예를 들어 이 인증을 통해 특정 사용자 또는 앱이 아니라 특정 테넌트에 대한 API를 잠급니다. 

코드를 통해 논리 앱에 대한 API 액세스를 제한하려면 JWT(JSON Web Token)가 포함된 헤더를 추출합니다. 호출자의 ID를 확인하고 일치하지 않는 요청을 거부합니다.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>다음 단계

* [논리 앱 워크플로에서 사용자 지정 API 배포 및 호출](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
