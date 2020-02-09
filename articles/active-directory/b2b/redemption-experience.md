---
title: B2B 공동 작업의 초대 상환-Azure AD
description: 개인 정보 보호 조건에 대한 규약을 비롯한 최종 사용자에 대한 Azure AD B2B 협업 초대 상환 환경을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0a71da025b8b2bb571dc9b00e23bc691ecdd44c
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758288"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B 협업 초대 상환

이 문서에서는 게스트 사용자가 리소스에 액세스 하는 방법 및 사용자가 발견할 수 있는 동의 프로세스를 설명 합니다. 게스트에 게 초대 전자 메일을 보내면 방문자가 앱 또는 포털에 대 한 액세스 권한을 얻기 위해 사용할 수 있는 링크가 초대에 포함 됩니다. 초대 전자 메일은 게스트가 리소스에 액세스할 수 있는 방법 중 하나일 뿐입니다. 또는 디렉터리에 게스트를 추가 하 고 공유 하려는 포털 또는 앱에 대 한 직접 링크를 제공할 수 있습니다. 게스트는 사용 하는 방법에 관계 없이 첫 번째 시간 승인 프로세스를 안내 합니다. 이 프로세스를 통해 게스트가 개인 정보 취급 방침에 동의 하 고 설정한 [사용 약관](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) 에 동의 하 게 됩니다.

게스트 사용자를 디렉터리에 추가 하면 게스트 사용자 계정에 초기에 **Pendingacceptance**으로 설정 된 동의 상태 (PowerShell에서 볼 수 있음)가 있습니다. 이 설정은 게스트가 초대를 수락 하 고 개인 정보 취급 방침 및 사용 약관에 동의할 때까지 유지 됩니다. 그러면 동의 상태가 **수락 됨**으로 변경 되 고 동의 페이지가 더 이상 게스트에 표시 되지 않습니다.

## <a name="redemption-through-the-invitation-email"></a>초대 이메일을 통해 상환

[Azure Portal를 사용 하](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)여 디렉터리에 게스트 사용자를 추가 하는 경우 프로세스의 게스트로 초대 이메일이 전송 됩니다. 또한 [PowerShell을 사용](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) 하 여 게스트 사용자를 디렉터리에 추가 하는 경우 초대 전자 메일을 보내도록 선택할 수 있습니다. 전자 메일의 링크를 사용할 때 게스트의 경험에 대 한 설명은 다음과 같습니다.

1. 게스트가 **Microsoft 초대**에서 보낸 [초대 전자 메일](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) 을 받습니다.
2. 게스트가 메일에서 **시작** 을 선택 합니다.
3. 게스트에 Azure AD 계정, Microsoft 계정 (MSA) 또는 페더레이션된 조직에 전자 메일 계정이 없으면 MSA를 만들라는 메시지가 표시 됩니다 ( [일회용 암호](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) 기능을 사용 하도록 설정 하 고 msa를 요구 하지 않는 경우).
4. 게스트는 아래에 설명 된 [동의 환경을](#consent-experience-for-the-guest) 안내 합니다.

## <a name="redemption-through-a-direct-link"></a>직접 링크를 통해 상환

초대 전자 메일의 대 안으로 게스트에 앱 또는 포털에 대 한 직접 링크를 제공할 수 있습니다. 먼저 [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) 또는 [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)을 통해 게스트 사용자를 디렉터리에 추가 해야 합니다. 그런 다음 [사용자 지정 가능한 방법](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)중 하나를 사용 하 여 직접 로그온 링크를 비롯 한 응용 프로그램을 사용자에 게 배포할 수 있습니다. 게스트가 초대 전자 메일 대신 직접 링크를 사용 하는 경우 처음에는 첫 번째 시간 승인 환경에 따라 진행 됩니다.

> [!IMPORTANT]
> 직접 링크는 테넌트와 관련 되어야 합니다. 즉, 테넌트 ID 또는 확인 된 도메인을 포함 해야 합니다. 그러면 공유 앱이 있는 테넌트에서 게스트를 인증할 수 있습니다. https://myapps.microsoft.com 와 같은 공통 URL은 인증을 위해 해당 홈 테넌트로 리디렉션하는 게스트에 대해 작동 하지 않습니다. 다음은 테넌트 컨텍스트가 포함 된 직접 링크의 몇 가지 예입니다.
 > - 앱 액세스 패널: https://myapps.microsoft.com/?tenantid=&lt , 테넌트 id&gt; 
 > - 확인 된 도메인에 대 한 앱 액세스 패널: https://myapps.microsoft.com/&lt ; 확인 된 도메인&gt;
 > - Azure Portal: https://portal.azure.com/&lt , 테넌트 id&gt;
 > - 개별 앱: [직접 로그온 링크](../manage-apps/end-user-experiences.md#direct-sign-on-links) 를 사용 하는 방법을 참조 하세요.

직접 링크를 통해 초대 전자 메일을 권장 하는 경우도 있습니다. 이러한 특수 사례가 조직에 중요 한 경우 초대 전자 메일을 계속 보내는 방법을 사용 하 여 사용자를 초대 하는 것이 좋습니다.
 - 사용자에 게 페더레이션 조직의 Azure AD 계정, MSA 또는 전자 메일 계정이 없습니다. 일회용 암호 기능을 사용 하지 않는 한 게스트는 MSA를 만드는 단계를 안내 하는 초대 전자 메일을 사용 해야 합니다.
 - 경우에 따라 연락처 개체(예: Outlook 연락처 개체)와 충돌이 발생하기 때문에 초대된 사용자 개체에는 이메일 주소가 없을 수 있습니다. 이 경우에 사용자는 초대 이메일에서 상환 URL을 클릭해야 합니다.
 - 사용자는 초대된 이메일 주소의 별칭으로 로그인할 수 있습니다. 별칭은 전자 메일 계정과 연결 된 추가 전자 메일 주소입니다. 이 경우 사용자는 초대 전자 메일에서 상환 URL을 클릭 해야 합니다.

## <a name="consent-experience-for-the-guest"></a>게스트에 대 한 동의 환경

게스트가 파트너 조직의 리소스에 처음으로 액세스 하기 위해 로그인 하는 경우 다음 페이지를 안내 합니다. 

1. 게스트는 초대 조직의 개인 정보 취급 방침을 설명 하는 **권한 검토** 페이지를 검토 합니다. 사용자는 초대 하는 조직의 개인 정보 취급 방침에 따라 사용자의 정보 사용을 **수락** 해야 계속할 수 있습니다.

   ![권한 검토 페이지를 보여 주는 스크린샷](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > 테넌트 관리자 권한을 가진 사용자가 조직의 개인정보처리방침에 연결할 수 있는 방법에 대한 정보는 [방법: Azure Active Directory에 조직의 개인 정보 추가](https://aka.ms/adprivacystatement)를 참조하세요.

2. 사용 약관을 구성 하면 게스트가 열리고 사용 약관을 검토 한 다음 **동의**를 선택 합니다. 

   ![새 사용 약관을 보여 주는 스크린샷](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > 조직 관계 **관리사용** > [약관](../governance/active-directory-tou.md)에서 **사용** > **약관**보기를 구성할 수 있습니다.

3. 달리 지정 하지 않는 한 게스트는 게스트가 액세스할 수 있는 응용 프로그램을 나열 하는 앱 액세스 패널로 리디렉션됩니다.

   ![앱 액세스 패널을 보여 주는 스크린샷](media/redemption-experience/myapps.png) 

디렉터리에서 방문자의 **초대 허용** 값이 **예**로 변경 됩니다. MSA를 만든 경우 게스트의 **원본** 에 **Microsoft 계정이**표시 됩니다. 게스트 사용자 계정 속성에 대 한 자세한 내용은 [AZURE AD B2B 공동 작업 사용자의 속성](user-properties.md)을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [Azure Portal에서 Azure Active Directory B2B Collaboration 사용자 추가](add-users-administrator.md)
- [정보 근로자가 Azure Active Directory에 B2B 협업 사용자를 추가하는 방법](add-users-information-worker.md)
- [PowerShell을 사용하여 Azure Active Directory B2B 협업 사용자 추가](customize-invitation-api.md#powershell)
- [게스트 사용자로써 조직 나가기](leave-the-organization.md)
