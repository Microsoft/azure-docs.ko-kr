---
title: Microsoft id 플랫폼 범위, 권한 및 동의 | Microsoft Docs
description: 범위, 권한 및 동의 포함 하 여 Microsoft id 플랫폼 끝점에서 권한 부여의 설명입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: celested
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87103b1052b5d9168928193eacc78a935e68067f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112080"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>사용 권한 및 동의에 Microsoft id 플랫폼 끝점

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft ID 플랫폼과 통합되는 애플리케이션은 사용자와 관리자가 데이터 액세스 방법을 제어할 수 있는 권한 부여 모델을 따릅니다. 권한 부여 모델의 구현에 Microsoft id 플랫폼 끝점에서 업데이트 된 및 앱 Microsoft id 플랫폼과 상호 작용 해야 하는 방법을 변경 합니다. 이 문서에서는 범위, 사용 권한 및 동의를 포함하여 이 권한 부여 모델의 기본 개념에 대해 설명합니다.

> [!NOTE]
> Microsoft id 플랫폼 끝점에는 일부 시나리오 및 기능만 지원 하지 않습니다. 에 대 한 자세한 내용은 Microsoft id 플랫폼 끝점을 사용 해야 하는지 여부를 확인 합니다 [Microsoft identity 플랫폼 제한](active-directory-v2-limitations.md)합니다.

## <a name="scopes-and-permissions"></a>범위 및 사용 권한

Microsoft ID 플랫폼은 [OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 프로토콜을 구현합니다. OAuth 2.0은 사용자 대신 타사 앱에서 웹에 호스트된 리소스에 액세스할 수 있도록 하는 방법입니다. Microsoft ID 플랫폼과 통합되는 웹 호스트팅 리소스에는 리소스 식별자 또는 *애플리케이션 ID URI*가 있습니다. 예를 들어 웹에 호스트된 몇 가지 Microsoft 리소스는 다음과 같습니다.

* Microsoft Graph: `https://graph.microsoft.com`
* Office 365 메일 API: `https://outlook.office.com`
* Azure AD Graph: `https://graph.windows.net`

> [!NOTE]
> Azure AD Graph나 Office 365 메일 API 대신 Microsoft Graph를 사용할 것을 강력하게 권장합니다.

Microsoft ID 플랫폼과 통합된 타사 리소스의 경우도 마찬가지입니다. 이러한 리소스는 해당 리소스의 기능을 더 작은 청크로 나누는 데 사용할 수 있는 사용 권한 집합을 정의할 수도 있습니다. 예를 들어 [Microsoft Graph](https://graph.microsoft.com)는 특히 다음 작업을 수행할 수 있는 사용 권한을 정의했습니다.

* 사용자의 일정 읽기
* 사용자의 일정에 쓰기
* 사용자로 메일 보내기

이러한 유형의 사용 권한을 정의하면 리소스가 해당 데이터 및 API 기능이 공개되는 방식을 세밀하게 제어할 수 있습니다. 타사 앱은 사용자 및 관리자에게 이러한 사용 권한을 요청할 수 있고, 요청을 받은 사용자 또는 관리자가 요청을 승인해야만 앱이 사용자 대신 데이터에 액세스하거나 작업을 수행할 수 있습니다. 리소스 기능을 더 작은 사용 권한 집합으로 나누면 기능을 수행하는 데 필요한 특정 권한만 요청하도록 타사 앱을 빌드할 수 있습니다. 사용자와 관리자가 정확 하 게 데이터를 앱에 액세스 권한을 알 수 및 악의적인 의도로 동작 하지 않으면 해당 점을 더 확신할 수 있습니다. 개발자는 항상 최소 권한의 개념에 따라 애플리케이션이 작동하는 데 필요한 사용 권한만 요청해야 합니다.

OAuth 2.0에서는 이러한 유형의 사용 권한을 *범위*라고 합니다. 또한 라고도 *권한을*합니다. 사용 권한은 Microsoft ID 플랫폼에서 문자열 값으로 표시됩니다. Microsoft Graph 예제를 계속하는 경우 각 사용 권한의 문자열 값은 다음과 같습니다.

* `Calendars.Read`를 사용하여 사용자의 일정 읽기
* `Calendars.ReadWrite`를 사용하여 사용자의 일정 쓰기
* `Mail.Send`을 사용하여 사용자로 메일 보내기

앱은 가장 일반적으로 권한 부여 끝점을 Microsoft id 플랫폼에 대 한 요청에 범위를 지정 하 여 이러한 권한을 요청 합니다. 그러나 특정 권한이 높은 권한이 수만 관리자 동의 통해 부여 하 고 요청/사용 하 여 부여 합니다 [관리자 동의 끝점](v2-permissions-and-consent.md#admin-restricted-permissions)합니다. 더 알아보려면 계속 읽어 보세요.

## <a name="permission-types"></a>사용 권한 유형

Microsoft ID 플랫폼은 **위임된 권한** 및 **애플리케이션 권한**의 두 가지 사용 권한을 지원합니다.

* **위임된 권한**은 로그인한 사용자가 있는 앱에서 사용합니다. 이러한 앱에 대 한 사용자 또는 관리자 동의 권한으로 앱 요청 및 앱에서 로그인 한 사용자 대상 리소스에 대 한 호출을 수행할 때 역할을 위임 된 권한 인지 합니다. 일부 위임된 권한은 관리자가 아닌 사용자가 동의할 수 있지만, 일부 상위 권한은 [관리자 동의](v2-permissions-and-consent.md#admin-restricted-permissions)가 필요합니다. 위임된 권한에 동의할 수 있는 관리자 역할을 알아보려면 [Azure AD의 관리자 역할 권한](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

* **애플리케이션 권한**은 로그인한 사용자 없이 실행되는 앱(예: 백그라운드 서비스 또는 디먼으로 실행한 앱)에서 사용합니다.  애플리케이션 권한은 [관리자만 승인할 수 있습니다](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_유효 권한_ 은 앱이 대상 리소스를 요청할 때 갖게 되는 권한입니다. 대상 리소스에 대 한 호출을 만들 때 위임 및 앱 권한이 부여 된 응용 프로그램 사용 권한 및 해당 유효 사용 권한 간의 차이점을 이해 하는 것이 반드시 합니다.

- 위임된 권한의 경우 앱의 _유효 권한_ 은 (동의를 통해) 앱에 부여한 위임된 권한과 현재 로그인한 사용자가 가진 권한의 최소 권한 교집합입니다. 앱은 로그인한 사용자보다 더 많은 권한을 가질 수 없습니다. 조직 내에서 로그인한 사용자의 권한은 정책 또는 관리자 역할 하나 이상의 멤버 자격에 의해 결정될 수 있습니다. 위임된 권한에 동의할 수 있는 관리자 역할을 알아보려면 [Azure AD의 관리자 역할 권한](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

   예를 들어 앱에 _User.ReadWrite.All_ 위임된 권한이 부여되었다고 가정해 봅시다. 이 권한은 일반적으로 조직에 있는 모든 사용자의 프로필을 읽고 업데이트하는 앱 권한을 부여합니다. 로그인한 사용자가 전역 관리자인 경우 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있게 됩니다. 그러나에서 로그인 한 사용자를 관리자 역할에 없는 경우 앱에서 로그인 한 사용자의 프로필에만 업데이트할 수 됩니다. 즉, 대신 행동할 권한을 가진 사용자가 조직에 있는 다른 사용자의 프로필에 대한 권한을 가지고 있지 않으므로 해당 다른 사용자의 프로필을 업데이트할 수 없습니다.
  
- 애플리케이션 권한의 경우 앱의 _유효 권한_ 은 사용 권한이 암시하는 권한의 전체 수준입니다. 예를 들어 _User.ReadWrite.All_ 애플리케이션 권한을 가진 앱은 조직에 있는 모든 사용자의 프로필을 업데이트할 수 있습니다. 

## <a name="openid-connect-scopes"></a>OpenID Connect 범위

OpenID Connect의 Microsoft id 플랫폼 구현을 특정 리소스에 적용 되지 않는 몇 가지 잘 정의 된 범위: `openid`, `email`를 `profile`, 및 `offline_access`합니다. `address` 및 `phone` OpenID Connect 범위는 지원되지 않습니다.

### <a name="openid"></a>openid

앱이 [OpenID Connect](active-directory-v2-protocols.md)를 사용하여 로그인을 수행하는 경우 `openid` 범위를 요청해야 합니다. `openid` 범위는 작업 계정 동의 페이지에 "로그인" 권한으로 표시되고 Microsoft 계정 동의 페이지에 "Microsoft 계정을 사용하여 프로필 보기 및 앱과 서비스에 연결" 권한으로 표시됩니다. 이 사용 권한을 통해 앱은 `sub` 클레임 형식으로 사용자에 대한 고유 식별자를 받을 수 있습니다. 또한 앱이 UserInfo 엔드포인트에 액세스할 수 있도록 해줍니다. `openid` 인증 앱에서 사용할 수 있는 ID 토큰을 획득 하는 Microsoft id 플랫폼의 토큰 끝점에 범위를 사용할 수 있습니다.

### <a name="email"></a>이메일

`email` 범위는 `openid` 범위 및 다른 모든 범위와 함께 사용될 수 있습니다. 이는 앱이 `email` 클레임의 형식으로 사용자의 기본 전자 메일 주소에 액세스할 수 있도록 해줍니다. `email` 전자 메일 주소는 대/소문자 형태를 사용자 계정과 연결 된 경우에 클레임은 토큰에 포함 되어 있습니다. `email` 범위를 사용하는 경우 앱에서 `email` 클레임이 토큰에 존재하지 않는 경우를 처리할 수 있도록 준비해야 합니다.

### <a name="profile"></a>프로필

`profile` 범위는 `openid` 범위 및 다른 모든 범위와 함께 사용될 수 있습니다. 이는 앱이 사용자에 대한 상당한 양의 정보에 액세스할 수 있도록 해줍니다. 정보에 액세스할 수를 포함 하지만 사용자의 지정 된 이름, 성, 기본 사용자 이름 및 개체 ID에 국한 되지 않습니다. 특정 사용자에 대한 id_token 매개 변수에서 사용할 수 있는 프로필 클레임의 전체 목록은 [`id_tokens` 참조](id-tokens.md)를 참조하세요.

### <a name="offlineaccess"></a>offline_access

[`offline_access` 범위](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)를 사용하면 앱이 연장된 기간 동안 사용자 대신 리소스에 액세스할 수 있습니다. 동의 페이지에서 이 범위는 "액세스 권한을 부여한 데이터에 대한 액세스 권한 유지" 권한으로 나타납니다. 사용자가 승인 하는 경우는 `offline_access` 범위 앱 Microsoft id 플랫폼 토큰 끝점에서 새로 고침 토큰을 받을 수 있습니다. 새로 고침 토큰은 장기적으로 존재합니다. 오래된 액세스 토큰이 만료되면 앱에서 새 액세스 토큰을 가져올 수 있습니다.

앱이 `offline_access` 범위를 명시적으로 요청하지 않으면 새로 고침 토큰을 받을 수 없습니다. 즉, [OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols.md)에서 권한 부여 코드를 교환하는 경우 `/token` 엔드포인트에서 액세스 토큰만 받게 됩니다. 액세스 토큰은 짧은 시간 동안 유효합니다. 액세스 토큰은 일반적으로 1시간 후에 만료됩니다. 이 시점에 앱은 사용자를 `/authorize` 엔드포인트로 다시 리디렉션하여 새 권한 부여 코드를 가져와야 합니다. 이 리디렉션 중에 앱 형식에 따라 사용자가 자격 증명을 다시 입력하거나 권한에 다시 동의해야 할 수 있습니다. 하지만 `offline_access` 범위 자동으로 요청 된 서버에서 클라이언트 요청 해야 합니다도 해당 새로 고침 토큰을 받으려면 합니다.

가져오기 및 새로 고침 토큰을 사용 하는 방법에 대 한 자세한 내용은 참조는 [Microsoft id 플랫폼 프로토콜 참조](active-directory-v2-protocols.md)합니다.

## <a name="requesting-individual-user-consent"></a>개별 사용자의 동의 요청

[OpenID Connect 또는 OAuth 2.0](active-directory-v2-protocols.md) 권한 부여 요청에서 앱은 `scope` 쿼리 매개 변수를 사용하여 필요한 사용 권한을 요청할 수 있습니다. 예를 들어 사용자가 앱에 로그인하면 앱이 다음 예제와 같은 요청을 보냅니다(읽기 쉽도록 줄 바꿈 추가).

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` 매개 변수는 앱이 요청하는 공백으로 구분된 위임된 권한 범위 목록입니다. 각 권한은 리소스 식별자(애플리케이션 ID URI)에 권한 값을 추가하여 표시됩니다. 요청 예제에서 앱에는 사용자의 일정을 읽고 사용자로 메일을 보낼 수 있는 권한이 필요합니다.

사용자가 자격 증명을 입력 하면 Microsoft id 플랫폼 끝점 확인의 레코드와 일치 *사용자 동의*합니다. 과거에는 사용자 요청 된 사용 권한 중 하나에 동의 하지 않은도 전체 조직 대신 하 여 이러한 권한을 관리자 동의이 Microsoft id 플랫폼 끝점 요청된 권한을 부여 하 라는 메시지를 표시 합니다.

> [!NOTE]
> 이제, `offline_access`("액세스 권한을 부여한 데이터에 대한 액세스 권한 유지") 및 `user.read`("로그인 및 프로필 읽기") 권한이 애플리케이션에 대한 초기 동의에 자동으로 포함됩니다.  이러한 권한은 일반적으로 적절한 앱 기능에 필요합니다. `offline_access`는 기본 및 웹앱에 중요한 새로 고침 토큰에 대한 앱 액세스 권한을 제공하지만, `user.read`는 `sub` 클레임에 대한 액세스 권한을 제공하여 클라이언트나 앱이 시간이 지나도 사용자를 올바르고 식별하고 기본적인 사용자 정보에 액세스할 수 있도록 합니다.  

![작업 계정 동의](./media/v2-permissions-and-consent/work_account_consent.png)

사용자가 사용 권한 요청을 승인하면 동의가 기록되므로 후속 계정 로그인 시 다시 동의할 필요가 없습니다.

## <a name="requesting-consent-for-an-entire-tenant"></a>전체 테넌트에 대한 동의 요청

종종 조직에서는 애플리케이션의 라이선스 또는 구독을 구입할 때 조직 내 모든 구성원이 사용할 수 있도록 애플리케이션을 선제적으로 설정하려고 합니다. 이러한 프로세스의 일부로, 관리자는 테넌트의 사용자를 대신하여 애플리케이션에 대한 동의를 부여할 수 있습니다. 관리자가 전체 테넌트에 대한 동의를 부여하면 조직의 사용자에게는 애플리케이션 동의 화면이 표시되지 않습니다.

테넌트의 모든 사용자에 대한 위임된 권한을 요청하기 위해 앱에서 관리자 동의 엔드포인트를 사용할 수 있습니다.

또한 애플리케이션에서는 관리자 동의 엔드포인트를 사용하여 애플리케이션 권한을 요청해야 합니다.

## <a name="admin-restricted-permissions"></a>관리 제한 권한

Microsoft 에코시스템에서 일부 높은 수준 사용 권한을 *관리 제한*으로 설정할 수 있습니다. 이러한 종류의 권한으로는 다음과 같은 것이 있습니다.

* `User.Read.All`을 사용하여 모든 사용자의 전체 프로필 읽기
* `Directory.ReadWrite.All`를 사용하여 조직의 디렉터리에 데이터 쓰기
* `Groups.Read.All`을 사용하여 조직 디렉터리의 모든 그룹 읽기

소비자 사용자는 이러한 데이터에 대한 애플리케이션 액세스 권한을 부여할 수 있는 반면 조직 사용자는 동일한 집합인 회사의 중요한 데이터에 대한 액세스 권한을 부여하지 않도록 제한됩니다. 조직 사용자에서 이러한 사용 권한 중 하나에 대 한 액세스를 요청 하는 응용 프로그램, 사용자 하지 앱의 사용 권한에 동의할 권한이 없다는 오류 메시지를 받습니다.

또한 앱이 조직의 이러한 관리 제한 범위에 대한 액세스 권한을 필요로 하는 경우 아래에 설명한 관리 동의 엔드포인트를 사용하여 회사 관리자에게 직접 요청해야 합니다.

관리자가 상위 위임된 권한을 요청하고 관리자가 관리자 동의 엔드포인트를 통해 이러한 권한을 부여하면 테넌트의 모든 사용자에 대한 동의가 부여됩니다.

응용 프로그램 응용 프로그램 사용 권한을 요청 하 고 관리자가 동의 끝점 관리자를 통해 이러한 사용 권한을 부여 하는 경우 특정 사용자를 대신 하 여이 권한 부여 동작이 수행 되지 않습니다. 그 대신 클라이언트 애플리케이션에 *직접* 권한이 부여됩니다. 이러한 유형의 사용 권한만 디먼 서비스 및 백그라운드에서 실행 되는 다른 비 대화형 응용 프로그램에서 사용 됩니다.

## <a name="using-the-admin-consent-endpoint"></a>관리 동의 엔드포인트 사용

회사 관리자가 애플리케이션을 사용하고 권한 부여 엔드포인트로 이동되면 Microsoft ID 플랫폼은 사용자의 역할을 검색하고 사용자가 요청한 사용 권한의 전체 테넌트를 대신하여 동의할 것인지 물어봅니다. 그러나 관리자가 전체 테넌트를 대신하여 권한을 부여하도록 사전에 요청하려는 경우에 사용할 수 있는 전용 관리자 동의 엔드포인트도 있습니다. 이 끝점을 사용 하 여 (없습니다 권한 부여 끝점을 사용 하 여 요청 수)는 응용 프로그램 사용 권한을 요청 하는 데 필요한 이기도 합니다.

다음 단계를 따르는 경우 앱은 관리 제한 범위를 포함하여 테넌트의 모든 사용자에 대한 사용 권한을 요청할 수 있습니다. 이것은 상위 권한 작업이며 시나리오에 꼭 필요한 경우에만 수행해야 합니다.

단계를 구현하는 코드 샘플을 보려면 [관리 제한 범위 샘플](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2)을 참조하세요.

### <a name="request-the-permissions-in-the-app-registration-portal"></a>앱 등록 포털에서 사용 권한 요청

관리자 동의는 범위 매개 변수를 인정하지 않으므로 요청되는 사용 권한을 애플리케이션 등록에서 정적으로 정의해야 합니다. 일반적으로 것이 가장 좋은 방법은 지정된 된 응용 프로그램에 대 한 정적으로 정의 하는 권한은 동적 으로/증분 방식으로 요청 될 권한 상위 집합 인지 확인 합니다.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>응용 프로그램에 대 한 정적으로 요청 된 사용 권한 목록을 구성 하려면

1. 응용 프로그램으로 이동 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 경험, 또는 [앱 만들기](quickstart-register-app.md) 아직 없는 경우.
2. **Microsoft Graph 사용 권한** 섹션으로 이동하여 앱에 필요한 사용 권한을 추가합니다.
3. 앱 등록을 **저장**합니다.

### <a name="recommended-sign-the-user-into-your-app"></a>권장: 앱에 사용자 로그인

일반적으로 관리 동의 엔드포인트를 사용하는 애플리케이션을 빌드할 때 앱에는 관리자가 앱의 사용 권한을 승인할 수 있는 페이지 또는 보기가 필요합니다. 이 페이지는 앱 로그인 흐름의 일부, 앱 설정의 일부 또는 전용 "연결" 흐름일 수 있습니다. 대부분의 경우에 사용자가 회사 또는 학교 Microsoft 계정으로 로그인한 후에 앱은 이 "연결" 보기만을 표시하게 됩니다.

사용자로 앱에 로그인하면 사용자에게 필요한 사용 권한을 승인하도록 요청하기 전에 관리자가 속해 있는 조직을 식별할 수 있습니다. 반드시 필요하지는 않지만 조직 사용자를 위한 직관적인 환경을 만들 수 있습니다. 사용자를 로그인에 따라 우리의 [Microsoft id 플랫폼 프로토콜 자습서](active-directory-v2-protocols.md)합니다.

### <a name="request-the-permissions-from-a-directory-admin"></a>디렉터리 관리에서 사용 권한 요청

조직의 관리자의 권한을 요청 하려면 준비 된 경우 Microsoft id 플랫폼에 사용자를 리디렉션할 수 있습니다 *관리 동의 끝점*합니다.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 | 사용 권한을 요청하려는 디렉터리 테넌트입니다. GUID에서 제공한 이름이거나, 친근한 이름 형식이거나, 예제에서처럼 `common`으로 일반 참조될 수 있습니다. |
| `client_id` | 필수 | 합니다 **(클라이언트) 응용 프로그램 ID** 는 합니다 [Azure portal-앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경을 앱에 할당 합니다. |
| `redirect_uri` | 필수 |리디렉션 URI는 처리할 앱에 응답을 전송하려는 위치입니다. 앱 등록 포털에 등록한 리디렉션 URI 중 하나와 정확히 일치해야 합니다. |
| `state` | 권장 | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태를 사용하여 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩할 수 있습니다. |

이 시점에서 Azure AD는 테넌트 관리자에게 요청을 완료하기 위해 로그인하도록 요구합니다. 관리자에게는 앱 등록 포털에서 앱에 요청한 사용 권한을 모두 승인하라는 메시지가 표시됩니다.

#### <a name="successful-response"></a>성공적인 응답

관리자가 앱에 대한 사용 권한을 승인하는 경우 성공적인 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| 매개 변수 | 설명 |
| --- | --- |
| `tenant` | 디렉터리 테넌트는 GUID 형식으로 요청한 권한을 애플리케이션에 부여합니다. |
| `state` | 토큰 응답에도 반환되는 요청에 포함된 값입니다. 원하는 모든 콘텐츠의 문자열일 수 있습니다. 상태는 인증 요청이 발생하기 전에 앱에서 사용자 상태에 대한 정보(예: 사용한 페이지 또는 보기)를 인코딩하는 데 사용됩니다. |
| `admin_consent` | `True`로 설정합니다. |

#### <a name="error-response"></a>오류 응답

관리자가 앱에 대한 사용 권한을 승인하지 않는 경우 실패한 응답은 다음과 같습니다.

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| 매개 변수 | 설명 |
| --- | --- |
| `error` | 발생하는 오류 유형을 분류하는 데 사용할 수 있고 오류에 대응하는 데 사용할 수 있는 오류 코드 문자열입니다. |
| `error_description` | 개발자가 오류의 근본 원인을 식별하도록 도울 수 있는 특정 오류 메시지입니다. |

관리 동의 엔드포인트에서 성공적인 응답을 받은 후 앱은 요청한 사용 권한을 얻게 됩니다. 이제 원하는 리소스에 대한 토큰을 요청할 수 있습니다.

## <a name="using-permissions"></a>사용 권한 사용

사용자가 앱에 대한 사용 권한에 동의하면 앱이 일부 용량으로 리소스에 액세스할 수 있는 앱의 권한을 나타내는 액세스 토큰을 획득할 수 있습니다. 액세스 토큰은 단일 리소스에만 사용할 수 있지만 해당 리소스에 대해 앱에 부여된 모든 사용 권한이 액세스 토큰 안에 인코딩됩니다. 액세스 토큰을 얻으려면 앱에는 Microsoft id 플랫폼 토큰 끝점에 다음과 같은 요청을 수행 수 있습니다.

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

리소스에 대한 HTTP 요청에 결과 액세스 토큰을 사용할 수 있습니다. 이는 앱에 특정 작업을 수행할 수 있는 적절한 권한이 있음을 리소스에 나타냅니다. 

OAuth 2.0 프로토콜 및 액세스 토큰을 가져오는 방법에 대 한 자세한 내용은 참조는 [Microsoft id 플랫폼 끝점 프로토콜 참조](active-directory-v2-protocols.md)합니다.

## <a name="the-default-scope"></a>/.default 범위

사용할 수는 `/.default` Microsoft id 플랫폼 끝점에 v1.0 끝점에서 앱을 마이그레이션하도록 지원 하기 위해 범위입니다. 이것은 애플리케이션 등록 시 구성된 정적 사용 권한 목록을 참조하는 모든 애플리케이션에 대한 기본 제공 범위입니다. `scope` 값 `https://graph.microsoft.com/.default`는 기본적으로 v1.0 엔드포인트 `resource=https://graph.microsoft.com`과 같습니다. 즉, Azure Portal에서 애플리케이션이 등록된 Microsoft Graph의 범위를 사용하여 토큰을 요청합니다.

/.Default 범위 모든 OAuth 2.0 흐름에서 사용할 수 있지만에 필요 합니다 [에-대리 흐름](v2-oauth2-on-behalf-of-flow.md) 및 [클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> 클라이언트를 결합할 수 없습니다. 정적 (`/.default`) 및 단일 요청에서 동적 동의 합니다. 따라서 `scope=https://graph.microsoft.com/.default+mail.read`에서는 이러한 범위 형식의 조합으로 인해 오류가 발생합니다.

### <a name="default-and-consent"></a>/.default 및 동의

`/.default` 범위는 `prompt=consent`에 대한 v1.0 엔드포인트 동작도 트리거합니다. 리소스에 관계없이 애플리케이션이 등록하는 모든 사용 권한에 대한 동의를 요청합니다. 요청의 일부로 포함 하는 경우는 `/.default` 범위 요청 된 리소스에 대 한 범위를 포함 하는 토큰을 반환 합니다.

### <a name="default-when-the-user-has-already-given-consent"></a>사용자가 이미 동의를 제공한 경우의 /.default

`/.default`는 기능적으로 `resource` 중심 v1.0 엔드포인트의 동작과 동일하므로 v1.0 엔드포인트의 동의 동작도 함께 발생합니다. 즉, `/.default`는 사용자가 클라이언트와 리소스 간에 권한을 부여하지 않은 경우에만 동의 프롬프트를 트리거합니다. 이러한 동의가 있으면 해당 리소스에 대해 사용자가 부여한 모든 범위를 포함하는 토큰이 반환됩니다. 그러나 권한이 부여되지 않았거나 `prompt=consent` 매개 변수가 제공된 경우 클라이언트 애플리케이션이 등록한 모든 범위에 대해 동의 프롬프트가 표시됩니다.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>예제 1: 사용자 또는 테넌트 관리자가 권한을 부여함

사용자(또는 테넌트 관리자)가 클라이언트에 Microsoft Graph에 대한 권한 `mail.read` 및 `user.read`를 부여했습니다. 클라이언트가 `scope=https://graph.microsoft.com/.default`를 요청한 경우, Microsoft Graph에 대해 클라이언트 애플리케이션이 등록한 권한의 동의 여부에 관계없이 동의 프롬프트가 표시되지 않습니다. 범위 `mail.read` 및 `user.read`를 포함하는 토큰이 반환됩니다.

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>예 2: 사용자가 클라이언트와 리소스 간에 사용 권한을 부여하지 않음

클라이언트와 Microsoft Graph 간에 사용자 동의가 없습니다. 클라이언트는 `user.read` 및 `contacts.read` 권한 뿐만 아니라 Azure Key Vault 범위 `https://vault.azure.net/user_impersonation`에도 등록했습니다. 클라이언트가 `scope=https://graph.microsoft.com/.default`에 대한 토큰을 요청하면 `user.read`, `contacts.read` 및 Key Vault `user_impersonation` 범위에 대한 동의 화면이 표시됩니다. 반환된 토큰에는 `user.read` 및 `contacts.read` 범위만 있습니다.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>예 3: 사용자가 동의했으며 클라이언트가 추가 범위를 요청함

사용자는 클라이언트에 대한 `mail.read`에 이미 동의했습니다. 클라이언트는 해당 등록에서 `contacts.read` 범위에 등록했습니다. 클라이언트가 `scope=https://graph.microsoft.com/.default`를 사용하여 토큰을 요청하고 `prompt=consent`를 통해 동의를 요청하면 사용자에게 애플리케이션에서 등록한 모든 권한에 대해서만 동의 화면이 표시됩니다. 즉, `contacts.read`는 동의 화면에 표시되지만 `mail.read`는 표시되지 않습니다. 반환되는 토큰은 Microsoft Graph용이며 `mail.read` 및 `contacts.read`를 포함합니다.

### <a name="using-the-default-scope-with-the-client"></a>클라이언트에서 /.default 범위 사용

클라이언트가 고유한 `/.default` 범위를 요청하는 특수한 경우의 `/.default` 범위가 있습니다. 다음 예제에서는 이 시나리오를 보여 줍니다.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

여기서는 등록된 모든 사용 권한에 대한 동의 화면이 표시되고(동의 및 `/.default`에 대한 위의 설명에 따라 해당되는 경우) 액세스 토큰이 아닌 id_token이 반환됩니다.  이 동작은 msal을 ADAL에서 이동 하는 특정 레거시 클라이언트에 대 한 존재 하며 Microsoft id 플랫폼 끝점을 대상으로 하는 새 클라이언트에서 사용할 수 없습니다.  

## <a name="troubleshooting-permissions-and-consent"></a>권한 및 동의 문제 해결

동의 프로세스 중에 개발자나 애플리케이션 사용자에게 예기치 않은 오류가 표시되면 [애플리케이션 사용에 동의할 때 발생하는 예기치 않은 오류](../manage-apps/application-sign-in-unexpected-user-consent-error.md) 문서에서 문제 해결 단계를 참조하세요.
