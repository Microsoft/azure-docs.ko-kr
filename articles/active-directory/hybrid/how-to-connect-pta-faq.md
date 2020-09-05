---
title: 'Azure AD Connect: 통과 인증 - 질문과 대답 | Microsoft Docs'
description: Azure Active Directory 통과 인증에 대한 FAQ(질문과 대답)입니다.
services: active-directory
keywords: Azure AD Connect 통과 인증, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac36650e285c371457b89f7a362b51fa74d7d47c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071434"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory 통과 인증: 질문과 대답

이 문서에서는 Azure AD(Azure Active Directory) 통과 인증에 대한 질문과 대답을 다룹니다. 업데이트된 내용을 계속 확인해 주세요.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Azure AD에 로그인하려면 통과 인증, 암호 해시 동기화 및 AD FS(Active Directory Federation Services) 중에서 어떤 방법을 선택해야 하나요?

다양한 Azure AD 로그인 방법을 비교한 결과와 조직에 적합한 로그인 방법을 선택하는 방법을 보려면 [이 가이드](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)를 검토하세요.

## <a name="is-pass-through-authentication-a-free-feature"></a>통과 인증은 무료 기능인가요?

통과 인증은 무료 기능입니다. 이 기능을 사용하는 데 Azure AD 유료 버전이 필요하지 않습니다.

## <a name="is-pass-through-authentication-available-in-the-microsoft-azure-germany-cloud-and-the-microsoft-azure-government-cloud"></a>통과 인증은 [Microsoft Azure 독일 클라우드](https://www.microsoft.de/cloud-deutschland) 및 [Microsoft Azure Government 클라우드](https://azure.microsoft.com/features/gov/)에서 사용할 수 있나요?

아니요. 통과 인증은 Azure AD의 전 세계 인스턴스에서만 사용할 수 있습니다.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>[조건부 액세스](../active-directory-conditional-access-azure-portal.md)는 통과 인증에서 작동하나요?

예. Azure Multi-Factor Authentication을 비롯 한 모든 조건부 액세스 기능이 통과 인증을 사용 합니다.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>통과 인증은 "userPrincipalName" 대신 "Alternate ID"를 사용자 이름으로 지원하나요?
예, 대체 전자 메일과 같은 UPN이 아닌 값을 사용 하는 로그인은 PTA (통과 인증)와 PHS (암호 해시 동기화) 모두에 대해 지원 됩니다. [대체 로그인 ID](../authentication/howto-authentication-use-email-signin.md)에 대 한 자세한 내용을 보려면

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>암호 해시 동기화가 통과 인증을 대체하는 역할을 하나요?

아니요. 통과 인증은 자동으로 암호 해시 동기화로 장애 조치하지 _않습니다_. 사용자 로그인 오류를 방지하려면 [고가용성](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)에 대해 통과 인증을 구성해야 합니다.

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>암호 해시 동기화에서 통과 인증으로 전환 하면 어떻게 되나요?

Azure AD Connect를 사용 하 여 암호 해시 동기화에서 통과 인증으로 로그인 방법을 전환 하는 경우 통과 인증은 관리 되는 도메인의 사용자에 대 한 기본 로그인 방법이 됩니다. 이전에 암호 해시 동기화를 통해 동기화 된 모든 사용자의 암호 해시는 Azure AD에 저장 된 상태로 유지 됩니다.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>통과 인증 에이전트와 동일한 서버에 [Azure AD 애플리케이션 프록시](../manage-apps/application-proxy.md)를 설치할 수 있나요?

예. 다시 브랜딩된 버전의 통과 인증 에이전트(버전 1.5.193.0 이상)에서 이 구성을 지원합니다.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>필요한 Azure AD Connect 및 통과 인증 에이전트 버전은 무엇인가요?

이 기능을 사용하려면 Azure AD Connect는 버전 1.1.750.0 이상이 필요하고, 통과 인증 에이전트는 버전 1.5.193.0 이상이 필요합니다. Windows Server 2012 R2 이상이 설치된 서버에 모든 소프트웨어를 설치합니다.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>내 사용자 암호가 만료되었을 때 통과 인증을 사용하여 로그인하려고 하면 어떻게 되나요?

특정 사용자에 대해 [비밀번호 쓰기 저장](../authentication/concept-sspr-writeback.md)이 구성되었고 이 사용자가 통과 인증을 사용하여 로그인하면 암호를 변경하거나 다시 설정할 수 있습니다. 암호는 예상대로 온-프레미스 Active Directory에 쓰기 저장됩니다.

특정 사용자에 대해 비밀번호 쓰기 저장이 구성되지 않았거나 이 사용자에게 유효한 Azure AD 라이선스가 할당되어 있지 않으면 해당 사용자가 클라우드에서 암호를 업데이트할 수 없습니다. 암호가 만료된 경우에도 해당 암호를 업데이트할 수 없습니다. 대신에 다음과 같은 메시지가 사용자에게 표시됩니다. "조직이 이 사이트에서 암호 업데이트를 허용하지 않습니다. 조직에서 권장하는 방법에 따라 암호를 업데이트하거나 도움이 필요한 경우 관리자에게 문의하세요." 사용자 또는 관리자가 온-프레미스 Active Directory에서 해당 암호를 다시 설정해야 합니다.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>통과 인증을 통해 무차별 암호 대입 공격으로부터 사용자를 보호하려면 어떻게 할까요?

[스마트 잠금에 대한 정보를 읽어보세요](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>통과 인증 에이전트가 포트 80 및 443을 통해 통신하는 것은 무엇인가요?

- 인증 에이전트는 모든 기능 작업에 대해 포트 443을 통해 HTTPS 요청을 만듭니다.
- 인증 에이전트는 포트 80을 통해 HTTP 요청을 수행 하 여 TLS/SSL Crl (인증서 해지 목록)을 다운로드 합니다.

     >[!NOTE]
     >최근 업데이트는 기능에 필요한 포트 수를 줄였습니다. 이전 버전의 Azure AD Connect 또는 인증 에이전트를 사용하는 경우 5671, 8080, 9090, 9091, 9350, 9352 및 10100-10120 포트도 열어 두세요.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>통과 인증 에이전트는 아웃바운드 웹 프록시 서버를 통해 통신할 수 있나요?

예. 온-프레미스 환경에서 WPAD(웹 프록시 자동 검색)를 사용하는 경우 인증 에이전트는 자동으로 네트워크에서 웹 프록시 서버를 찾아서 사용하려고 합니다.

사용자 환경에 WPAD가 없는 경우 프록시 정보를 추가하여(아래 그림 참조) 통과 인증 에이전트가 Azure AD와 통신할 수 있습니다.
- 서버에 통과 인증 에이전트를 설치하기 전에 Internet Explorer에서 프록시 정보를 구성합니다. 이렇게 하면 인증 에이전트 설치를 완료할 수 있지만 관리자 포털에서는 여전히 **비활성**으로 표시됩니다.
- 서버에서 "C:\Program Files\Microsoft Azure AD Connect 인증 에이전트"로 이동합니다.
- “AzureADConnectAuthenticationAgentService” 구성 파일을 편집하고 다음 줄을 추가합니다(“http\://contosoproxy.com:8080”을 실제 프록시 주소로 바꾸기).

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>동일한 서버에 둘 이상의 통과 인증 에이전트를 설치할 수 있나요?

아니요, 단일 서버에는 통과 인증 에이전트 하나만 설치할 수 있습니다. 고가용성에 대해 통과 인증을 구성하려는 경우 [여기에 있는 지침을 따르세요](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>통과 인증 에이전트에서 사용되는 인증서를 수동으로 갱신해야 하나요?

각 통과 인증 에이전트와 Azure AD 간의 통신은 인증서 기반 인증을 사용하여 보호됩니다. 이러한 [인증서는 Azure AD에서 몇 개월마다 자동으로 갱신](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents)됩니다. 따라서 이러한 인증서를 수동으로 갱신할 필요가 없습니다. 필요에 따라 만료된 이전 인증서를 정리할 수 있습니다.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>통과 인증 에이전트를 제거하려면 어떻게 해야 하나요?

통과 인증 에이전트는 실행되는 동안 활성 상태를 유지하며, 지속적으로 사용자 로그인 요청을 처리합니다. 인증 에이전트를 제거하려는 경우 **제어판 -> 프로그램 -> 프로그램 및 기능**으이로 동한 후 **Microsoft Azure AD Connect Authentication Agent** 및 **Microsoft Azure AD Connect Agent Updater** 프로그램을 둘 다 제거합니다.

위의 단계를 완료한 후 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에서 통과 인증 블레이드를 확인하는 경우 인증 에이전트가 **비활성** 상태로 표시됩니다. 이는 _예상_된 것입니다. 인증 에이전트는 10 일 후에 목록에서 자동으로 삭제 됩니다.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>이미 AD FS를 사용하여 Azure AD에 로그인하고 있습니다. 통과 인증으로 전환하려면 어떻게 하나요?

AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 경우 [여기](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)에 게시된 자세한 배포 가이드를 따르는 것이 좋습니다.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>다중 포리스트 Active Directory 환경에서 통과 인증을 사용할 수 있나요?

예. Active Directory 포리스트 간에 포리스트 트러스트 (양방향)가 있고 이름 접미사 라우팅이 제대로 구성 된 경우 다중 포리스트 환경이 지원 됩니다.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>통과 인증이 여러 인증 에이전트에 대한 부하 분산을 제공하나요?

아니요, 여러 개의 통과 인증 에이전트를 설치하면 [고가용성](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)만 보장됩니다. 인증 에이전트 간에 결정적 부하 분산을 제공하지 않습니다. (임의의) 모든 인증 에이전트는 특정 사용자 로그인 요청을 처리할 수 있습니다.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>설치해야 하는 통과 인증 에이전트 수는 몇 개인가요?

여러 개의 통과 인증 에이전트를 설치하면 [고가용성](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)이 보장됩니다. 그러나 인증 에이전트 간에 결정적 부하 분산을 제공하지는 않습니다.

테넌트에 나타날 것으로 예상되는 로그인 요청의 최대 및 평균 로드를 고려하세요. 벤치마크의 경우, 단일 인증 에이전트는 표준 4코어 CPU, 16GB RAM 서버에서 초당 300~400건의 인증을 처리할 수 있습니다.

네트워크 트래픽을 예측하려면 다음 크기 조정 지침을 사용합니다.
- 각 요청에는 크기가 (0.5K + 1K * num_of_agents)바이트인 페이로드(즉, Azure AD에서 인증 에이전트로 이동하는 데이터)가 있습니다. 여기에서 "num_of_agents"는 테넌트에 등록된 인증 에이전트 수를 나타냅니다.
- 각 응답의 페이로드(즉, 인증 에이전트에서 Azure AD로 이동하는 데이터) 크기는 1K 바이트입니다.

대부분의 고객의 경우 고가용성 및 용량을 위해 총 2~3개의 인증 에이전트로도 충분합니다. 로그인 대기 시간을 개선하려면 도메인 컨트롤러에 가까운 곳에 인증 에이전트를 설치해야 합니다.

>[!NOTE]
>테넌트당 인증 에이전트 40개라는 시스템 제한이 있습니다.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Azure AD Connect를 실행하는 서버가 아닌 다른 서버에 첫 번째 통과 인증 에이전트를 설치할 수 있나요?

아니요,이 시나리오는 지원 _되지_ 않습니다.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>통과 인증을 사용하도록 설정하기 위해 클라우드 전용 글로벌 관리자 계정이 필요한 이유는 무엇인가요?

클라우드 전용 글로벌 관리자 계정을 사용하여 통과 인증을 사용하도록 설정하거나 사용하지 않도록 설정하는 것이 좋습니다. [클라우드 전용 전역 관리자 계정을 추가 하는](../active-directory-users-create-azure-portal.md)방법에 대해 알아봅니다. 이 단계는 테넌트가 잠기지 않도록 합니다.

## <a name="how-can-i-disable-pass-through-authentication"></a>통과 인증을 사용하지 않도록 설정하려면 어떻게 하나요?

Azure AD Connect 마법사를 다시 실행하고 사용자 로그인 방법을 통과 인증에서 다른 방법으로 변경합니다. 이렇게 변경하면 테넌트에서 통과 인증이 비활성화되고 인증 에이전트가 서버에서 제거됩니다. 다른 서버에서 인증 에이전트를 수동으로 제거해야 합니다.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>통과 인증 에이전트를 제거하면 어떻게 되나요?

서버에서 통과 인증 에이전트를 제거하면 서버에서 로그인 요청 수락을 중지합니다. 테넌트에서 사용자 로그인 기능을 중단하지 않도록 방지하려면 통과 인증 에이전트를 제거하기 전에 다른 인증 에이전트가 실행되도록 합니다.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>이전 테넌트가 원래 AD FS를 사용하여 설치되었습니다.  최근에 PTA로 마이그레이션했지만 이제 Azure AD와 동기화하여 UPN 변경 내용이 표시되지 않습니다.  UPN 변경 내용이 동기화되지 않는 이유는 무엇인가요?

A: 다음과 같은 경우에 온-프레미스 UPN 변경 내용을 동기화할 수 없습니다.

- Azure AD 테넌트가 2015년 6월 15일 이전에 생성된 경우
- 처음에 인증에 AD FS를 사용하여 Azure AD 테넌트와 페더레이션된 경우
- 관리 사용자가 PTA를 인증으로 사용하도록 전환하는 경우

즉, 2015년 6월 15일 이전에 만들어진 테넌트의 기본 동작이 UPN 변경 내용을 차단하기 때문입니다.  UPN 변경 내용의 차단을 취소해야 하는 경우 다음 PowerShell cmdlt을 실행해야 합니다.  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

2015년 6월 15일 이후에 만든 테넌트에는 UPN 변경 내용을 동기화하는 기본 동작이 포함됩니다.   



## <a name="next-steps"></a>다음 단계
- [현재 제한 사항](how-to-connect-pta-current-limitations.md): 지원되는 시나리오와 지원되지 않는 시나리오를 알아봅니다.
- [빠른 시작](how-to-connect-pta-quick-start.md): Azure AD 통과 인증을 구성하고 실행합니다.
- [AD FS에서 통과 인증으로 마이그레이션](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) - AD FS(또는 기타 페더레이션 기술)에서 통과 인증으로 마이그레이션하는 방법에 대한 자세한 가이드입니다.
- [스마트 잠금](../authentication/howto-password-smart-lockout.md): 테넌트에서 스마트 잠금 기능을 구성하여 사용자 계정을 보호하는 방법을 알아봅니다.
- [기술 심층 분석](how-to-connect-pta-how-it-works.md): 통과 인증 기능이 작동하는 원리를 이해합니다.
- [문제 해결](tshoot-connect-pass-through-authentication.md): 통과 인증 기능의 일반적인 문제를 해결하는 방법을 알아봅니다.
- [보안 심층 분석](how-to-connect-pta-security-deep-dive.md) - 통과 인증 기능에 대한 자세한 기술 정보를 가져옵니다.
- [Azure AD Seamless SSO](how-to-connect-sso.md): 보완적인 Azure AD Seamless SSO 기능을 알아봅니다.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Azure Active Directory 포럼을 사용하여 새 기능 요청을 제출합니다.

