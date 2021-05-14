---
title: 초대 링크 또는 메일 없이 B2B 게스트 추가 - Azure AD
description: 게스트 사용자가 Azure Active Directory B2B 협업에서 초대를 사용하지 않고 Azure AD에 다른 게스트 사용자를 추가하도록 할 수 있습니다.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87909803"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>초대 링크 또는 메일 없이 B2B Collaboration 게스트 사용자 추가

이제 공유 앱에 대한 [직접 링크](redemption-experience.md#redemption-through-a-direct-link)를 전송하여 게스트 사용자를 초대할 수 있습니다. 이 방법을 사용하면 게스트 사용자가 특별한 경우를 제외하고 초대 이메일을 사용하지 않아도 됩니다. 게스트 사용자는 앱 링크를 클릭하고, 개인 정보 보호 방침을 확인 및 수락한 다음, 자연스럽게 앱에 액세스합니다. 자세한 내용은 [B2B 협업 초대 상환](redemption-experience.md)을 참조하세요.

이 새로운 방법이 제공되기 전에는 UI나 PowerShell을 통해 초대자(조직 또는 파트너 조직 소속)를 **게스트 초대자** 디렉터리 역할에 추가한 다음, 게스트 사용자를 디렉터리, 그룹 또는 애플리케이션에 추가하여 초대 이메일 없이 게스트 사용자를 초대할 수 있었습니다. PowerShell을 사용하는 경우 초대 이메일을 모두 억제할 수 있습니다. 예를 들면 다음과 같습니다.

1. 호스트 조직(예: WoodGrove)의 한 사용자가 파트너 조직(예: Sam@litware.com)의 한 사용자를 게스트로 초대합니다.
2. 호스트 조직의 관리자는 Sam이 파트너 조직(Litware)의 다른 사용자를 식별하고 추가할 수 있도록 하는 [정책을 설정](delegate-invitations.md)합니다. Sam은 **게스트 초대자** 역할에 추가되어야 합니다.
3. 이제 Sam은 초대를 돌려받지 않아도 Litware의 다른 사용자를 WoodGrove 디렉터리, 그룹 또는 애플리케이션에 추가할 수 있습니다. Sam에게 Litware의 적절한 열거 권한이 있으면 이러한 작업이 자동으로 수행됩니다.
 
원래의 이 방법도 계속 가능합니다. 그러나 동작에 약간의 차이가 있습니다. PowerShell을 사용하는 경우 이제 초대 받은 게스트 계정이 즉시 **Accepted** 로 표시되는 것이 아니라 **PendingAcceptance** 상태가 됩니다. 상태는 보류 중이더라도 게스트 사용자는 이메일 초대 링크 없이 로그인하여 앱에 액세스할 수 있습니다. 보류 중 상태란 사용자가 초대하는 조직의 개인 정보 보호 방침에 동의하게 되는 [동의 환경](redemption-experience.md#consent-experience-for-the-guest)을 아직 거치지 못했다는 의미입니다. 게스트 사용자는 처음으로 로그인할 때 이 동의 화면을 보게 됩니다. 

사용자를 디렉터리에 초대한 경우 게스트 사용자는 리소스 테넌트 특정 Azure Portal URL(예: https://portal.azure.com/*resourcetenant*.onmicrosoft.com)에 직접 액세스하여 개인 정보 보호 방침에 동의해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [B2B 협업 초대 상환](redemption-experience.md)
- [Azure Active Directory B2B 협업에 대한 초대 위임](delegate-invitations.md)
- [정보 작업자가 B2B 협업 사용자를 추가하는 방법은 무엇입니까?](add-users-information-worker.md)

