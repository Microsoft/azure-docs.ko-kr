---
title: Android의 Microsoft id 플랫폼 계정 & 테넌트 프로필 | Microsoft
description: Android 용 Microsoft id 플랫폼 계정 개요
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: a0f0f3be1647c820591923a094ef7fce86ab9672
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699447"
---
# <a name="accounts--tenant-profiles-android"></a>계정 및 테넌트 프로필(Android)

이 문서에서는 Microsoft id 플랫폼에 `account`의 개요를 제공 합니다.

MSAL (Microsoft 인증 라이브러리) API는 *사용자* 라는 용어를 *계정*으로 대체 합니다. 한 가지 이유는 사용자 (사람이 나 소프트웨어 에이전트)가 여러 계정을 사용 하거나 사용할 수 있기 때문입니다. 이러한 계정은 사용자의 조직에 있거나 사용자가 구성원 인 다른 조직에 있을 수 있습니다.

Microsoft id 플랫폼의 계정은 다음으로 구성 됩니다.

- 고유 식별자입니다.  
- 계정 소유권/제어를 시연 하는 데 사용 되는 하나 이상의 자격 증명입니다.
- 다음과 같은 특성으로 구성 된 하나 이상의 프로필입니다.
  - 사진, 지정 된 이름, 제품군 이름, 제목, 사무실 위치
- 계정에는 기관 또는 레코드 시스템의 원본이 있습니다. 계정이 만들어지고 해당 계정과 연결 된 자격 증명이 저장 된 시스템입니다. Microsoft id 플랫폼과 같은 다중 테넌트 시스템에서 레코드 시스템은 계정을 만든 `tenant`입니다. 이 테넌트를 `home tenant`라고도 합니다.
- Microsoft id 플랫폼의 계정에는 다음과 같은 레코드 시스템이 있습니다.
  - Azure Active Directory B2C를 포함 하 Azure Active Directory입니다.
  - Microsoft 계정 (Live)입니다.
- Microsoft id 플랫폼 외부의 레코드 시스템의 계정은 다음을 비롯 한 Microsoft id 플랫폼 내에 표시 됩니다.
  - 연결 된 온-프레미스 디렉터리의 id (Windows Server Active Directory)
  - LinkedIn, GitHub 등의 외부 id입니다.
  이러한 경우, 계정에는 원본 시스템 레코드와 Microsoft id 플랫폼 내의 레코드 시스템이 모두 있습니다.
- Microsoft id 플랫폼을 사용 하면 한 계정을 사용 하 여 여러 조직 (Azure Active Directory 테넌트)에 속하는 리소스에 액세스할 수 있습니다.
  - 한 시스템 레코드 (AAD 테넌트 A)의 계정이 다른 레코드 시스템 (AAD 테넌트 B)의 리소스에 액세스할 수 있도록 기록 하려면 리소스가 정의 된 테넌트에서 계정이 표시 되어야 합니다. 시스템 B에서 시스템 A의 계정에 대 한 로컬 레코드를 만들어이 작업을 수행 합니다.
  - 계정에 대 한 표현인이 로컬 레코드는 원래 계정에 바인딩됩니다.
  - MSAL은이 로컬 레코드를 `Tenant Profile`으로 노출 합니다.
  - 테넌트 프로필은 직함, 사무실 위치, 연락처 정보 등 로컬 컨텍스트에 적합 한 다양 한 특성을 가질 수 있습니다.
- 하나 이상의 테넌트에 계정이 있을 수 있으므로 계정에는 프로필이 둘 이상 있을 수 있습니다.

> [!NOTE]
> MSAL은 Microsoft 계정 시스템 (Live, MSA)을 Microsoft id 플랫폼 내의 다른 테넌트로 처리 합니다. Microsoft 계정 테넌트의 테넌트 id: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>계정 개요 다이어그램

![계정 개요 다이어그램](./media/accounts-overview/accounts-overview.png)

위의 다이어그램:

- `bob@contoso.com` 계정은 온-프레미스 Windows Server Active Directory (원본 온-프레미스 시스템 레코드)에 만들어집니다.
- Microsoft 계정 테넌트에서 계정 `tom@live.com`를 만듭니다.
- `bob@contoso.com`은 다음 Azure Active Directory 테넌트의 리소스 하나 이상에 액세스할 수 있습니다.
  - contoso.com (레코드의 클라우드 시스템-온-프레미스 시스템에 연결 된 레코드의 클라우드 시스템)
  - fabrikam.com
  - woodgrovebank.com
  - `bob@contoso.com`에 대 한 테넌트 프로필은 이러한 각 테넌트에 존재 합니다.
- `tom@live.com`은 다음 Microsoft 테넌트의 리소스에 액세스할 수 있습니다.
  - contoso.com
  - fabrikam.com
  - `tom@live.com`에 대 한 테넌트 프로필은 이러한 각 테넌트에 존재 합니다.
- 다른 테넌트의 Tom 및 Bob에 대 한 정보는 레코드 시스템의 정보에 따라 다를 수 있습니다. 작업 제목, 사무실 위치 등의 특성에 따라 달라질 수 있습니다. 각 조직 (Azure Active Directory 테넌트) 내에 있는 그룹 및/또는 역할의 멤버일 수 있습니다. bob@contoso.com 테넌트 프로필로이 정보를 참조 합니다.

다이어그램에서 bob@contoso.com 및 tom@live.com는 다른 Azure Active Directory 테넌트의 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 B2B 공동 작업 사용자 추가 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)를 참조 하세요.

## <a name="accounts-and-single-sign-on-sso"></a>계정 및 Single Sign-On (SSO)

MSAL 토큰 캐시는 계정 당 *단일 새로 고침 토큰* 을 저장 합니다. 이 새로 고침 토큰을 사용 하 여 여러 Microsoft id 플랫폼 테넌트의 액세스 토큰을 자동으로 요청할 수 있습니다. Broker가 장치에 설치 되 면 해당 계정은 broker에서 관리 되며 장치 차원의 Single Sign-On 가능 합니다.

> [!IMPORTANT]
> B2C (Business to Consumer) 계정 및 새로 고침 토큰 동작은 Microsoft id 플랫폼의 나머지 부분과 다릅니다. 자세한 내용은 [B2C 정책 & 계정](#b2c-policies--accounts)을 참조 하세요.

## <a name="account-identifiers"></a>계정 식별자

MSAL 계정 ID는 계정 개체 ID가 아닙니다. Microsoft id 플랫폼 내에서 고유성이 아닌 다른 항목을 전달 하기 위해 구문 분석 및/또는 의존 하지 않습니다.

ADAL (Azure AD 인증 라이브러리)과의 호환성을 위해, ADAL에서 MSAL으로 쉽게 마이그레이션할 수 있도록 MSAL은 MSAL 캐시에서 사용할 수 있는 계정에 대 한 유효한 식별자를 사용 하 여 계정을 조회할 수 있습니다.  예를 들어, 다음은 각 식별자가 유효 하기 때문에 tom@live.com에 대해 항상 동일한 계정 개체를 검색 합니다.

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>계정에 대 한 클레임 액세스

MSAL은 액세스 토큰을 요청 하는 것 외에도 항상 각 테넌트의 ID 토큰을 요청 합니다. 이를 위해 항상 다음 범위를 요청 합니다.

- openid
- 프로필

ID 토큰에는 클레임 목록이 포함 되어 있습니다. `Claims`는 계정에 대 한 이름/값 쌍 이며, 요청을 수행 하는 데 사용 됩니다.

앞에서 설명한 것 처럼 계정이 존재 하는 각 테넌트는 작업 제목, 사무실 위치 등의 특성을 포함 하 되이에 제한 되지 않고 계정에 대 한 다른 정보를 저장할 수 있습니다.

계정이 여러 조직에서 구성원 또는 게스트가 될 수 있지만, MSAL은 서비스를 쿼리하여 해당 계정이 구성원 인 테넌트 목록을 가져옵니다. 대신, MSAL은 생성 된 토큰 요청의 결과로 계정이 있는 테넌트 목록을 작성 합니다.

계정 개체에 노출 되는 클레임은 항상 계정에 대 한 ' home 테넌트 '/{authority}의 클레임입니다. 해당 계정이 홈 테넌트에 대 한 토큰을 요청 하는 데 사용 되지 않은 경우 MSAL은 계정 개체를 통해 클레임을 제공할 수 없습니다.  예:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> 계정 개체에서 사용할 수 있는 클레임 목록을 보려면 [id_token의 클레임](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token) 을 참조 하세요.

> [!TIP]
> Id_token에 추가 클레임을 포함 하려면 [방법: AZURE AD 앱에 선택적 클레임 제공](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) 의 선택적 클레임 설명서를 참조 하세요.

### <a name="access-tenant-profile-claims"></a>테넌트 프로필 클레임 액세스

다른 테넌트에 표시 되는 계정에 대 한 클레임에 액세스 하려면 먼저 계정 개체를 `IMultiTenantAccount`로 캐스팅 해야 합니다. 모든 계정은 다중 테넌트 일 수 있지만 MSAL을 통해 사용할 수 있는 테넌트 프로필 수는 현재 계정을 사용 하 여 토큰을 요청한 테넌트를 기반으로 합니다.  예:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C 정책 & 계정

계정에 대 한 새로 고침 토큰은 B2C 정책 간에 공유 되지 않습니다. 따라서 토큰을 사용 하는 Single Sign-On를 사용할 수 없습니다. 이는 Single Sign-On 가능 하지 않음을 의미 하지 않습니다. Single Sign-On을 사용 하도록 설정 하는 데 쿠키를 사용할 수 있는 대화형 환경을 사용 해야 Single Sign-On 의미 합니다.

즉, MSAL의 경우 다른 B2C 정책을 사용 하 여 토큰을 획득 하는 경우 이러한 토큰은 각각 고유한 식별자를 가진 별도의 계정으로 취급 됩니다. 계정을 사용 하 여 `acquireTokenSilent`를 사용 하 여 토큰을 요청 하려는 경우 토큰 요청에 사용 하는 정책과 일치 하는 계정 목록에서 계정을 선택 해야 합니다. 예:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
