---
title: B2B 협업 API 및 사용자 지정 - Azure Active Directory
description: Azure Active Directory B2B 협업은 비즈니스 파트너가 선택적으로 회사 애플리케이션에 액세스할 수 있게 함으로써 회사 간 관계를 지원합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160859bb782ee8ffc4fef5ee03b61b6f54be1bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548664"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B 협업 API 및 사용자 지정

조직에 가장 잘 작동하는 방식으로 초대 프로세스를 사용자 지정하기 원하는 많은 고객이 있습니다. API를 사용하면 이 작업을 수행할 수 있습니다. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>초대 API의 기능

이 API는 다음과 같은 기능을 제공합니다.

1. *어떤* 전자 메일 주소가 있는 내부 사용자도 초대할 수 있습니다.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. 초대를 수락한 후에 사용자가 리디렉션되려는 위치를 사용자 지정합니다.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. 다음과 같이 Microsoft를 통해 표준 초대 메일을 보내려면 선택하고

    ```
    "sendInvitationMessage": true
    ```

   사용자 지정할 수 있는 받는 사람에게 보내려는 메시지를 지정합니다.

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. 또한 이 공동 작업자 초대에 대한 정보를 계속 알리려는 사람을 참조:에 추가하도록 선택합니다.

5. 또는 Azure AD 통해 알림을 보내지 않도록 선택하여 초대 및 온보딩 워크플로를 완전히 사용자 지정합니다.

    ```
    "sendInvitationMessage": false
    ```

   이 경우 전자 메일 템플릿, IM 또는 선택한 기타 배포 방법에 포함할 수 있는 API에서 상환 URL을 다시 얻을 수 있습니다.

6. 마지막으로 관리자는 사용자를 구성원으로 초대하도록 선택할 수 있습니다.

    ```
    "invitedUserType": "Member"
    ```

## <a name="determine-if-a-user-was-already-invited-to-your-directory"></a>사용자가 디렉터리에 이미 초대되었는지 확인

초대 API를 사용하여 사용자가 리소스 테넌트에 이미 존재하는지 확인할 수 있습니다. 이는 초대 API를 사용하여 사용자를 초대하는 앱을 개발하는 경우에 유용할 수 있습니다. 리소스 디렉터리에 이미 존재하는 사용자는 초대를 받지 않으므로 먼저 쿼리를 실행하여 이미 이메일이 UPN 또는 다른 로그인 속성으로 존재하는지 확인할 수 있습니다.

1. 사용자의 이메일 도메인이 리소스 테넌트의 확인된 도메인에 속해 있지 않은지 확인하세요.
2. 리소스 테넌트에서 다음 get 사용자 쿼리를 사용합니다. 여기서 {0}은 초대하는 이메일 주소입니다.

   ```
   “userPrincipalName eq '{0}' or mail eq '{0}' or proxyAddresses/any(x:x eq 'SMTP:{0}') or signInNames/any(x:x eq '{0}') or otherMails/any(x:x eq '{0}')"
   ```

## <a name="authorization-model"></a>인증 모델

API는 다음과 같은 인증 모드에서 실행될 수 있습니다.

### <a name="app--user-mode"></a>앱 + 사용자 모드

이 모드에서 API를 사용하는 모든 사용자에게 B2B 초대를 만들기 위한 권한이 있어야 합니다.

### <a name="app-only-mode"></a>앱 전용 모드

앱 전용 컨텍스트에서 초대가 성공하려면 앱에 User.Invite.All 범위가 필요합니다.

자세한 내용은 https://developer.microsoft.com/graph/docs/authorization/permission_scopes를 참조하세요.


## <a name="powershell"></a>PowerShell

PowerShell을 사용하여 외부 사용자를 조직에 쉽게 추가하고 초대할 수 있습니다. cmdlet을 사용하여 초대 만들기

```powershell
New-AzureADMSInvitation
```

사용할 수 있는 옵션은 다음과 같습니다.

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>초대 상태

외부 사용자에게 초대를 보낸 후 **Get-AzureADUser** cmdlet을 사용하여 해당 사용자가 수락했는지 확인할 수 있습니다. Get-AzureADUser의 다음 속성은 외부 사용자에게 초대를 보낼 때 채워집니다.

* **UserState** 는 초대가 **PendingAcceptance** 또는 **Accepted** 인지 여부를 나타냅니다.
* **UserStateChangedOn** 은 **UserState** 속성의 최신 변경에 대한 타임스탬프를 표시합니다.

**Filter** 옵션을 사용하여 **UserState** 별로 결과를 필터링할 수 있습니다. 아래 예제에서는 보류 중인 초대를 보유한 사용자만 표시하도록 결과를 필터링하는 방법을 보여 줍니다. 이 예제에서는 표시할 속성을 지정할 수 있는 **Format-List** 옵션도 보여 줍니다. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> AzureAD PowerShell 모듈 또는 AzureADPreview PowerShell 모듈의 최신 버전이 있는지 확인합니다. 

## <a name="see-also"></a>참고 항목

[https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)에서 초대 API 참조를 확인하세요.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [B2B 협업 초대 이메일의 요소](invitation-email-elements.md)
- [B2B 협업 초대 상환](redemption-experience.md)
- [초대 없이 B2B 협업 사용자 추가](add-user-without-invite.md)