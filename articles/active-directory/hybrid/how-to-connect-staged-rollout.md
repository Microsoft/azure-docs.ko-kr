---
title: 'Azure AD Connect: 준비 된 롤아웃을 통한 클라우드 인증 | Microsoft Docs'
description: 이 문서에서는 단계적 롤아웃을 사용 하 여 페더레이션된 인증에서 클라우드 인증으로 마이그레이션하는 방법을 설명 합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3044ebdd716eb85dc63d3a77089912d0d51d8b6
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915225"
---
# <a name="migrate-to-cloud-authentication-by-using-staged-rollout-preview"></a>스테이징 된 롤아웃 (미리 보기)을 사용 하 여 클라우드 인증으로 마이그레이션

스테이징 된 롤아웃 방법을 사용 하 여 페더레이션된 인증에서 클라우드 인증으로 마이그레이션할 수 있습니다. 이 문서에서는 스위치를 만드는 방법을 설명 합니다. 그러나 준비 된 롤아웃 작업을 시작 하기 전에 다음 조건 중 하나 이상이 충족 되는 경우의 영향을 고려해 야 합니다.
    
-  현재 온-프레미스 Multi-Factor Authentication 서버를 사용 하 고 있습니다. 
-  인증을 위해 스마트 카드를 사용 하 고 있습니다. 
-  현재 서버는 특정 페더레이션 전용 기능을 제공 합니다.

이 기능을 시도 하기 전에 적절 한 인증 방법 선택에 대 한 가이드를 검토 하는 것이 좋습니다. 자세한 내용은 [Azure Active Directory 하이브리드 id 솔루션에 대 한 올바른 인증 방법 선택](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn#comparing-methods)의 "메서드 비교" 표를 참조 하세요.

이 기능에 대 한 개요는 "Azure Active Directory 준비 된 롤아웃 이란?"를 참조 하세요. 화면이

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>전제 조건

-   페더레이션된 도메인을 포함 하는 Azure Active Directory (Azure AD) 테넌트가 있습니다.

-   다음 두 옵션 중 하나로 이동 하도록 결정 했습니다.
    - **옵션** - *암호 해시 동기화 (동기화)*  + *Single Sign-On SSO (원활한* )
    - **옵션 B** - *통과 인증* + *원활한 SSO*
    
    *원활한 SSO* 는 선택 사항 이지만 회사 네트워크 내부에서 도메인에 가입 된 컴퓨터를 실행 하는 사용자에 게 자동 로그인 환경을 구현 하는 데 사용 하는 것이 좋습니다.

-   클라우드 인증으로 마이그레이션되는 사용자에 게 필요한 모든 테넌트 브랜딩 및 조건부 액세스 정책을 구성 했습니다.

-   Azure Multi-Factor Authentication를 사용 하려는 경우 [SSPR (셀프 서비스 암호 재설정)에 수렴 형 등록](../authentication/concept-registration-mfa-sspr-combined.md) 을 사용 하 고 사용자가 인증 방법을 한 번 등록 하도록 Multi-Factor Authentication 하는 것이 좋습니다.

-   스테이징 된 롤아웃 기능을 사용 하려면 테넌트의 전역 관리자 여야 합니다.

-   특정 Active Directory 포리스트에서 *원활한 SSO* 를 사용 하도록 설정 하려면 도메인 관리자 여야 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 시나리오는 준비 된 롤아웃에 대해 지원 됩니다. 이 기능은 다음과 같은 경우에만 작동 합니다.

- Azure AD Connect를 사용 하 여 Azure AD에 프로 비전 된 사용자입니다. 클라우드 전용 사용자에 게는 적용 되지 않습니다.

- 브라우저 및 *최신 인증* 클라이언트에 대 한 사용자 로그인 트래픽 레거시 인증을 사용 하는 응용 프로그램 또는 클라우드 서비스는 페더레이션 인증 흐름으로 대체 됩니다. 최신 인증을 사용 하지 않는 Exchange online 또는 최신 인증을 지원 하지 않는 Outlook 2010을 예로 들 수 있습니다.

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

다음 시나리오는 준비 된 롤아웃에 대해 지원 되지 않습니다.

- 특정 응용 프로그램은 인증 하는 동안 Azure AD에 "domain_hint" 쿼리 매개 변수를 보냅니다. 이러한 흐름은 계속 되며, 준비 된 롤아웃을 사용 하도록 설정 된 사용자는 인증을 위해 페더레이션을 계속 사용 합니다.

<!-- -->

- 관리자는 보안 그룹을 사용 하 여 클라우드 인증을 롤아웃할 수 있습니다. 온-프레미스 Active Directory 보안 그룹을 사용 하는 경우 동기화 대기 시간을 방지 하려면 클라우드 보안 그룹을 사용 하는 것이 좋습니다. 다음 조건이 적용 됩니다.

    - 기능 당 최대 10 개의 그룹을 사용할 수 있습니다. 즉, *암호 해시 동기화*, *통과 인증*및 *원활한 SSO*에 대해 각각 10 개의 그룹을 사용할 수 있습니다.
    - 중첩 된 그룹은 *지원 되지 않습니다*. 이 범위는 공개 미리 보기에도 적용 됩니다.
    - 동적 그룹은 준비 된 롤아웃에 대해 *지원 되지 않습니다* .
    - 그룹 내의 Contact 개체는 그룹이 추가 되는 것을 차단 합니다.

- Azure AD Connect 또는 PowerShell을 사용 하 여 페더레이션된에서 클라우드 인증으로 최종 종료를 수행 해야 합니다. 단계적 롤아웃은 도메인을 페더레이션된에서 관리로 전환 하지 않습니다.

- 스테이징 된 롤아웃에 대 한 보안 그룹을 처음 추가 하는 경우 UX 시간 초과를 방지 하기 위해 200 명의 사용자로 제한 됩니다. 그룹을 추가한 후에는 필요에 따라 사용자에 게 직접 사용자를 추가할 수 있습니다.

## <a name="get-started-with-staged-rollout"></a>준비 된 롤아웃 시작

단계적 롤아웃을 사용 하 여 *암호 해시 동기화* 로그인을 테스트 하려면 다음 섹션의 사전 작업 지침을 따릅니다.

사용할 PowerShell cmdlet에 대 한 자세한 내용은 [AZURE AD 2.0 preview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)를 참조 하세요.

## <a name="pre-work-for-password-hash-sync"></a>암호 해시 동기화에 대 한 사전 작업

1. Azure AD Connect의 [선택적 기능](how-to-connect-install-custom.md#optional-features) 페이지에서 *암호 해시 동기화* 을 사용 하도록 설정 합니다. 

   ![Azure Active Directory Connect에서 "선택적 기능" 페이지의 스크린샷](media/how-to-connect-staged-rollout/sr1.png)

1. 모든 사용자의 암호 해시가 Azure AD에 동기화 되도록 전체 *암호 해시 동기화* 주기가 실행 되었는지 확인 합니다. *암호 해시 동기화*의 상태를 확인 하려면 [Azure AD Connect sync를 사용 하 여 암호 해시 동기화 문제 해결](tshoot-connect-password-hash-synchronization.md)에서 PowerShell 진단을 사용할 수 있습니다.

   ![AADConnect 문제 해결 로그의 스크린샷](./media/how-to-connect-staged-rollout/sr2.png)

단계적 롤아웃을 사용 하 여 *통과 인증* 로그인을 테스트 하려면 다음 섹션의 사전 작업 지침에 따라 로그인을 사용 하도록 설정 합니다.

## <a name="pre-work-for-pass-through-authentication"></a>통과 인증에 대 한 사전 작업

1. *통과 인증* 에이전트를 실행 하려는 Windows Server 2012 R2 이상 버전을 실행 하는 서버를 확인 합니다. 

   Azure AD Connect 서버를 선택 *하지 마십시오* . 서버가 도메인에 가입 되어 있는지 확인 하 고, Active Directory를 사용 하 여 선택한 사용자를 인증 하 고, 아웃 바운드 포트 및 Url에서 Azure AD와 통신할 수 있습니다. 자세한 내용은 [빠른 시작: AZURE AD 원활한 Single Sign-On](how-to-connect-sso-quick-start.md)의 "1 단계: 필수 구성 요소 확인" 섹션을 참조 하세요.

1. [Azure AD Connect 인증 에이전트를 다운로드](https://aka.ms/getauthagent)하 고 서버에 설치 합니다. 

1.  [고가용성을 사용 하도록 설정 하려면](how-to-connect-sso-quick-start.md)다른 서버에 추가 인증 에이전트를 설치 합니다.

1. [스마트 잠금 설정을](../authentication/howto-password-smart-lockout.md) 적절 하 게 구성 했는지 확인 합니다. 이렇게 하면 사용자의 온-프레미스 Active Directory 계정이 잘못 된 행위자에 의해 잠기지 않도록 합니다.

준비 된 롤아웃에 대해 선택 하는 로그인 방법 (*암호 해시 동기화* 또는 *통과 인증*)에 관계 없이 *원활한 SSO* 를 사용 하도록 설정 하는 것이 좋습니다. *원활한 SSO*를 사용 하도록 설정 하려면 다음 섹션의 사전 작업 지침을 따르세요.

## <a name="pre-work-for-seamless-sso"></a>원활한 SSO에 대 한 사전 작업

PowerShell을 사용 하 여 Active Directory 포리스트에서 *원활한 SSO* 를 사용 하도록 설정 합니다. Active Directory 포리스트가 둘 이상 있는 경우 각 포리스트에 대해 개별적으로 사용 하도록 설정 합니다.  *원활한 SSO* 는 준비 된 롤아웃에 대해 선택 된 사용자에 대해서만 트리거됩니다. 기존 페더레이션 설정에는 영향을 주지 않습니다.

다음을 수행 하 여 *원활한 SSO* 를 사용 하도록 설정 합니다.

1. Azure AD Connect 서버에 로그인 합니다.

2.  *% Programfiles%\\Microsoft Azure Active Directory Connect* 폴더로 이동 합니다.

3. 다음 명령을 실행 하 여 *원활한 SSO* PowerShell 모듈을 가져옵니다. 

   `Import-Module .\AzureADSSO.psd1`

4. PowerShell을 관리자 권한으로 실행합니다. PowerShell에서 `New-AzureADSSOAuthenticationContext`를 호출 합니다. 이 명령은 테넌트의 전역 관리자 자격 증명을 입력할 수 있는 창을 엽니다.

5.  `Get-AzureADSSOStatus | ConvertFrom-Json`를 호출 합니다. 이 명령은이 기능을 사용 하도록 설정 된 Active Directory 포리스트 목록 ("도메인" 목록 참조)을 표시 합니다. 기본적으로 테넌트 수준에서 false로 설정 됩니다.

   ![Windows PowerShell 출력의 예](./media/how-to-connect-staged-rollout/sr3.png)

6.  `$creds = Get-Credential`를 호출 합니다. 프롬프트에서 원하는 Active Directory 포리스트에 대 한 도메인 관리자 자격 증명을 입력 합니다.

7. `Enable-AzureADSSOForest -OnPremCredentials $creds`을 호출합니다. 이 명령은 *원활한 SSO*에 필요한 Active Directory 포리스트에 대 한 온-프레미스 도메인 컨트롤러에서 AZUREADSSOACC 컴퓨터 계정을 만듭니다.

8. *원활한 SSO* 는 url이 인트라넷 영역에 있어야 합니다. 그룹 정책을 사용 하 여 이러한 Url을 배포 하려면 [빠른 시작: AZURE AD 원활한 Single Sign-On](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)를 참조 하세요.

9. 전체 연습은 *원활한 SSO*에 대 한 [배포 계획](https://aka.ms/SeamlessSSODPDownload) 을 다운로드할 수도 있습니다.

## <a name="enable-staged-rollout"></a>스테이징 된 롤아웃 사용

특정 기능 (*통과 인증*, *암호 해시 동기화*또는 *원활한 SSO*)을 그룹의 선택 된 사용자 집합으로 롤아웃 하려면 다음 섹션의 지침을 따르세요.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>테넌트에서 특정 기능의 스테이징 된 롤아웃 사용

다음 옵션 중 하나를 롤아웃할 수 있습니다.

- **옵션** - *암호 해시 동기화* + *원활한 SSO*
- **옵션 B** - *통과 인증* + *원활한 SSO*
- *원활한 SSO* + *암호 해시 동기화* + *통과 인증* - **지원 되지 않음**

다음을 수행합니다.

1. Preview UX에 액세스 하려면 [AZURE AD 포털](https://aka.ms/stagedrolloutux)에 로그인 합니다.

2. **관리 되는 사용자 로그인에 대해 준비 된 롤아웃 사용 (미리 보기)** 링크를 선택 합니다.

   예를 들어 *옵션 A*를 사용 하도록 설정 하려면 다음 이미지에 표시 된 것 처럼 **암호 해시 동기화** 및 **원활한 Single Sign-On** 컨트롤을 **on**으로 밉니다.

   ![Azure AD Connect 페이지](./media/how-to-connect-staged-rollout/sr4.png)

   !["준비 된 롤아웃 기능 사용 (미리 보기)" 페이지](./media/how-to-connect-staged-rollout/sr5.png)

3. 기능에 그룹을 추가 하 여 *통과 인증* 및 *원활한 SSO*를 사용 하도록 설정 합니다. UX 시간 초과를 방지 하기 위해 보안 그룹에는 처음에는 200 개 미만의 멤버가 포함 되어 있는지 확인 합니다.

   !["암호 해시 동기화를 위한 그룹 관리 (미리 보기)" 페이지](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >그룹의 구성원은 준비 된 롤아웃에 대해 자동으로 사용 하도록 설정 됩니다. 중첩 및 동적 그룹은 준비 된 롤아웃에 대해 지원 되지 않습니다.

## <a name="auditing"></a>감사

준비 된 롤아웃에 대해 수행 하는 다양 한 작업에 대해 감사 이벤트를 사용 하도록 설정 했습니다.

- *암호 해시 동기화*, *통과 인증*또는 *원활한 SSO*에 대해 준비 된 롤아웃을 사용 하도록 설정한 경우의 감사 이벤트입니다.

  >[!NOTE]
  >단계적 SSO를 사용 하 여 *원활한 SSO* 를 켜면 감사 이벤트가 기록 됩니다.

  !["기능에 대 한 롤아웃 정책 만들기" 창-작업 탭](./media/how-to-connect-staged-rollout/sr7.png)

  !["기능에 대 한 롤아웃 정책 만들기" 창-수정 된 속성 탭](./media/how-to-connect-staged-rollout/sr8.png)

- *암호 해시 동기화*, *통과 인증*또는 *원활한 SSO*에 그룹이 추가 될 때의 감사 이벤트입니다.

  >[!NOTE]
  >감사 이벤트는 스테이징 롤아웃에 대 한 *암호 해시 동기화* 에 그룹이 추가 될 때 기록 됩니다.

  !["기능 롤아웃에 그룹 추가" 창-작업 탭](./media/how-to-connect-staged-rollout/sr9.png)

  !["기능 롤아웃에 그룹 추가" 창-수정 된 속성 탭](./media/how-to-connect-staged-rollout/sr10.png)

- 그룹에 추가 된 사용자가 준비 된 롤아웃에 대해 사용 하도록 설정 된 경우의 감사 이벤트입니다.

  !["기능 롤아웃에 사용자 추가" 창-작업 탭](media/how-to-connect-staged-rollout/sr11.png)

  !["기능 롤아웃에 사용자 추가" 창-대상 탭](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>유효성 검사

*암호 해시 동기화* 또는 *통과 인증* (사용자 이름 및 암호 로그인)을 사용 하 여 로그인을 테스트 하려면 다음을 수행 합니다.

1. 엑스트라넷에서 개인 브라우저 세션의 [앱 페이지로](https://myapps.microsoft.com) 이동한 후 준비 된 롤아웃에 대해 선택한 사용자 계정의 USERPRINCIPALNAME (UPN)을 입력 합니다.

   준비 된 롤아웃 대상으로 지정 된 사용자는 페더레이션된 로그인 페이지로 리디렉션되지 않습니다. 대신, Azure AD 테넌트 브랜드 로그인 페이지에 로그인 하 라는 메시지가 표시 됩니다.

1. UserPrincipalName를 사용 하 여 필터링 하 여 [AZURE AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md) 에 로그인이 성공적으로 나타나는지 확인 합니다.

*원활한 SSO*를 사용 하 여 로그인을 테스트 하려면:

1. 인트라넷에서 개인 브라우저 세션의 [앱 페이지로](https://myapps.microsoft.com) 이동한 후 준비 된 롤아웃에 대해 선택한 사용자 계정의 USERPRINCIPALNAME (UPN)을 입력 합니다.

   *원활한 SSO* 의 준비 된 롤아웃에 대 한 대상으로 지정 된 사용자는 "로그인 하는 중 ..."과 함께 제공 됩니다. 메시지가 자동으로 로그인 되기 전의 메시지입니다.

1. UserPrincipalName를 사용 하 여 필터링 하 여 [AZURE AD 로그인 활동 보고서](../reports-monitoring/concept-sign-ins.md) 에 로그인이 성공적으로 나타나는지 확인 합니다.

   선택한 준비 된 롤아웃 사용자에 대해 Active Directory Federation Services (AD FS)에서 여전히 발생 하는 사용자 로그인을 추적 하려면 [문제 해결: 이벤트 및 로깅의 AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events)지침을 따르세요. 타사 페더레이션 공급자에서이를 확인 하는 방법에 대 한 공급 업체 설명서를 확인 합니다.

## <a name="remove-a-user-from-staged-rollout"></a>단계적 롤아웃에서 사용자 제거

그룹에서 사용자를 제거 하면 해당 사용자에 대해 준비 된 롤아웃이 해제 됩니다. 준비 된 롤아웃 기능을 사용 하지 않도록 설정 하려면 컨트롤을 다시 **Off**로 밉니다.

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

**Q: 프로덕션 환경에서이 기능을 사용할 수 있나요?**

A: 예, 프로덕션 테넌트에서이 기능을 사용할 수 있지만 먼저 테스트 테넌트에서이 기능을 사용해 보는 것이 좋습니다.

**Q:이 기능을 사용 하 여 일부 사용자가 페더레이션된 인증을 사용 하 고 다른 사용자가 클라우드 인증을 사용 하는 영구 "공동 존재"를 유지 관리할 수 있나요?**

A: 아니요,이 기능은 페더레이션된에서 클라우드 인증을 단계별로 마이그레이션한 다음 결국 클라우드 인증으로 이동 하도록 설계 되었습니다. 이 방법으로 인해 예기치 않은 인증 흐름이 발생할 수 있으므로 영구 혼합 상태를 사용 하지 않는 것이 좋습니다.

**Q: PowerShell을 사용 하 여 스테이징 롤아웃을 수행할 수 있나요?**

A: 예. PowerShell을 사용 하 여 단계적 출시를 수행 하는 방법을 알아보려면 [AZURE AD 미리 보기](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [Azure AD 2.0 미리 보기](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
