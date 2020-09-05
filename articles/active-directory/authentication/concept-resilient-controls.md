---
title: 복원 력 있는 액세스 제어 관리 전략 만들기-Azure AD
description: 이 문서에서는 조직이 예기치 않은 중단 시 잠금의 위험을 줄이는 복원력을 제공하기 위해 채택해야 하는 전략에 대한 지침을 제공합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: f58e5a07348dfde4e4618eb58746f08016c55ed6
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049573"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립

>[!NOTE]
> 이 문서에 포함된 정보는 게시 날짜 당시 논의된 문제에 대한 Microsoft Corporation의 현재 관점을 나타냅니다. Microsoft는 변화하는 시장 상황에 대응해야 하므로 Microsoft의 약속으로 해석되지 않아야 하며, Microsoft는 게시 날짜 이후 제시된 정보의 정확성을 보증하지 않습니다.

MFA(다단계 인증) 또는 단일 네트워크 위치와 같은 단일 액세스 제어를 사용하여 IT 시스템을 보호하는 조직은 단일 액세스 제어를 사용할 수 없거나 잘못 구성하는 경우 해당 애플리케이션 및 리소스에 대한 액세스 실패에 취약합니다. 예를 들어 자연 재해로 인해 대규모 통신 인프라 또는 회사 네트워크의 대형 세그먼트를 사용할 수 없게 될 수 있습니다. 이러한 중단에 따라 최종 사용자와 관리자가 로그인하지 못할 수도 있습니다.

이 문서에서는 조직이 다음의 시나리오에서 예기치 않은 중단 시 잠금의 위험을 줄이는 복원력을 제공하기 위해 채택해야 하는 전략에 대한 지침을 제공합니다.

 1. 조직은 완화 전략 또는 대응 계획을 구현하여 **중단 전**에 잠금 위험을 줄이는 복원력을 높일 수 있습니다.
 2. 조직은 완화 전략과 대응 계획을 수립하여 **중단 중**에 선택한 애플리케이션과 리소스에 계속 액세스할 수 있습니다.
 3. 조직은 **중단 후** 및 구현된 모든 긴급 상황을 롤백하기 전에 로그와 같은 정보를 보존해야 합니다.
 4. 방지 전략 또는 대응 계획을 구현하지 않은 조직은 중단을 처리하기 위해 **응급 옵션**을 구현할 수 있습니다.

## <a name="key-guidance"></a>주요 지침

이 문서의 4가지 핵심 사항은 다음과 같습니다.

* 응급 액세스 계정을 사용하여 관리자 잠금을 방지합니다.
* 사용자 단위 MFA 대신 CA (조건부 액세스)를 사용 하 여 MFA를 구현 합니다.
* 여러 가지 CA (조건부 액세스) 컨트롤을 사용 하 여 사용자 잠금을 완화 합니다.
* 각 사용자에 대해 여러 인증 방법 또는 이와 동등한 기능을 프로비전하여 사용자 잠금을 완화합니다.

## <a name="before-a-disruption"></a>중단 전

실제 중단을 완화하는 것은 발생할 수 있는 액세스 제어 문제를 처리하는 데 있어 조직의 주요 초점이 되어야 합니다. 완화에는 실제 이벤트에 대한 계획 및 중단되는 동안 액세스 제어와 운영에 영향을 주지 않도록 하는 전략에 대한 구현이 포함됩니다.

### <a name="why-do-you-need-resilient-access-control"></a>복원력 있는 액세스 제어가 필요한 이유

 ID는 애플리케이션과 리소스에 액세스하는 사용자의 제어 평면입니다. ID 시스템은 액세스 제어 또는 인증 요구 사항과 같이 사용자가 애플리케이션에 액세스할 수 있는 사용자와 조건을 제어합니다. 예기치 않은 상황으로 인해 사용자가 인증하는 데 하나 이상의 인증 또는 액세스 제어 요구 사항을 사용할 수 없는 경우 조직에서는 다음 문제 중 하나 또는 둘 모두를 경험할 수 있습니다.

* **관리자 잠금:** 관리자는 테 넌 트 또는 서비스를 관리할 수 없습니다.
* **사용자 잠금:** 사용자는 앱 또는 리소스에 액세스할 수 없습니다.

### <a name="administrator-lockout-contingency"></a>관리자 잠금 긴급 상황

테넌트에 대한 관리자 액세스의 잠금을 해제하려면 응급 액세스 계정을 만들어야 합니다. *비상*(break glass) 계정이라고도 하는 이러한 응급 액세스 계정을 사용하면 권한 있는 일반 계정 액세스 절차를 사용할 수 없을 때 Azure AD 구성을 관리할 수 있습니다. [응급 액세스 계정 추천 사항]( ../users-groups-roles/directory-emergency-access.md)에 따라 둘 이상의 응급 액세스 계정을 만들어야 합니다.

### <a name="mitigating-user-lockout"></a>사용자 잠금 완화

 사용자 잠금의 위험을 완화 하려면 여러 컨트롤이 포함 된 조건부 액세스 정책을 사용 하 여 사용자에 게 앱과 리소스에 액세스 하는 방법을 선택할 수 있습니다. 예를 들어 사용자가 MFA로 로그인 **또는** 관리 디바이스에서 로그인 **또는** 회사 네트워크에서 로그인 중에서 선택할 수 있도록 하여 액세스 제어 중 하나를 사용할 수 없는 경우 계속 작업할 수 있는 다른 옵션이 사용자에게 제공됩니다.

#### <a name="microsoft-recommendations"></a>Microsoft 추천 사항

조직의 기존 조건부 액세스 정책에 다음 액세스 제어를 통합 합니다.

1. Microsoft Authenticator 앱(인터넷 기반), OATH 토큰(디바이스에서 생성) 및 SMS(전화)와 같이 다양한 통신 채널을 사용하는 각 사용자에 대해 여러 인증 방법을 프로비전합니다. 다음 PowerShell 스크립트는 사용자가 등록 해야 하는 추가 메서드인 [AZURE MFA 인증 방법 분석에 대 한 스크립트](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)를 미리 식별 하는 데 도움이 됩니다.
2. 디바이스 로그인에서 직접 MFA 요구 사항을 충족시키기 위해 Windows 10 디바이스에 비즈니스용 Windows Hello를 배포합니다.
3. [Azure AD 하이브리드 조인](../devices/overview.md) 또는 [Microsoft Intune 관리 디바이스](/intune/planning-guide)를 통해 신뢰할 수 있는 디바이스를 사용합니다. 사용자의 MFA 챌린지 없이 신뢰할 수 있는 디바이스 자체에서 정책의 강력한 인증 요구 사항을 충족할 수 있으므로 신뢰할 수 있는 디바이스는 사용자 환경을 향상시킵니다. 그러면 새 디바이스를 등록할 때와 신뢰할 수 없는 디바이스에서 애플리케이션 또는 리소스에 액세스할 때 MFA가 필요합니다.
4. 고정된 MFA 정책 대신 사용자 또는 로그인이 위험한 상태에 있을 때 액세스를 차단하는 Azure AD ID 보호 위험 기반 정책을 사용합니다.
5. Azure MFA NPS 확장을 사용 하 여 VPN 액세스를 보호 하는 경우 VPN 솔루션을 [SAML 앱](../manage-apps/view-applications-portal.md) 으로 페더레이션 하 고 아래 권장 된 대로 앱 범주를 확인 하는 것이 좋습니다. 

>[!NOTE]
> 위험 기반 정책에는 [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) 라이선스가 필요합니다.

다음 예제에서는 사용자가 자신의 애플리케이션과 리소스에 액세스할 수 있는 복원력 있는 액세스 제어를 제공하기 위해 만들어야 하는 정책을 설명합니다. 이 예제에서는 액세스 권한을 부여하려는 대상 사용자가 있는 **AppUsers** 보안 그룹, 핵심 관리자가 있는 **CoreAdmins** 그룹 및 응급 액세스 계정이 있는 **EmergencyAccess** 그룹이 필요합니다.
이 정책 세트 예제는 **AppUsers**에서 선택한 사용자에게 액세스 권한을 부여하거나, 신뢰할 수 있는 디바이스에서 연결하는 경우 선택한 애플리케이션에 액세스하거나, MFA와 같은 강력한 인증을 제공합니다. 응급 계정과 핵심 관리자는 여기서 제외됩니다.

**CA 완화 정책 세트:**

* 정책 1: 대상 그룹 외부 사용자에 대 한 액세스 차단
  * 사용자 및 그룹: 모든 사용자를 포함 합니다. AppUsers, CoreAdmins 및 EmergencyAccess 제외
  * 클라우드 앱: 모든 앱 포함
  * 조건: (없음)
  * Grant 컨트롤: Block
* 정책 2: MFA 또는 신뢰할 수 있는 장치를 요구 하는 AppUsers에 대 한 액세스 권한을 부여 합니다.
  * 사용자 및 그룹: AppUsers를 포함 합니다. CoreAdmins 및 EmergencyAccess 제외
  * 클라우드 앱: 모든 앱 포함
  * 조건: (없음)
  * 권한 부여: 액세스 권한 부여, 다단계 인증 필요, 장치가 규격을 준수 해야 합니다. 여러 컨트롤의 경우: 선택 된 컨트롤 중 하나가 필요 합니다.

### <a name="contingencies-for-user-lockout"></a>사용자 잠금 긴급 상황

조직에서는 대안으로 대응 정책을 만들 수도 있습니다. 대응 정책을 만들려면 비즈니스 연속성, 운영 비용, 재무 비용 및 보안 위험 간의 절충 조건을 정의해야 합니다. 예를 들어 애플리케이션의 하위 세트, 클라이언트의 하위 세트 또는 위치의 하위 세트에 속한 사용자의 하위 세트에만 대응 정책을 활성화할 수 있습니다. 완화 방법이 구현되지 않은 중단 동안 관리자와 최종 사용자는 대응 정책을 통해 애플리케이션 및 리소스에 액세스할 수 있습니다. 관리자가 설정 해야 하는 정책에 대 한 잠재적인 영향을 모니터링할 수 있도록 사용 하지 않을 때는 [보고서 전용 모드](../conditional-access/howto-conditional-access-insights-reporting.md) 에서 대체 정책을 사용 하도록 설정 하는 것이 좋습니다.

 중단 동안의 노출을 이해하는 것은 위험을 줄이는 데 도움이 되며 계획 프로세스에서 중요한 부분입니다. 대응 계획을 만들기 위해 먼저 결정해야 하는 조직의 다음 비즈니스 요구 사항은 다음과 같습니다.

1. 업무에 중요 한 앱을 미리 결정 합니다. 위험/보안 상태가 낮은 경우에도 액세스 권한을 부여 해야 하는 앱은 무엇 인가요? 이러한 애플리케이션의 목록을 작성하고, 액세스 제어가 모두 사라지더라도 이러한 애플리케이션이 계속 실행되어야 한다는 것에 대해 다른 모든 이해 관계자(비즈니스, 보안, 법률, 리더십)가 동의해야 합니다. 결과적으로 다음과 같은 범주로 분류될 수 있습니다.
   * **범주 1 중요 업무용 애플리케이션** - 몇 분 이상 사용할 수 없습니다(예: 조직의 매출에 직접 영향을 미치는 앱).
   * **범주 2 중요한 애플리케이션** - 몇 시간 내에 비즈니스에 액세스할 수 있어야 합니다.
   * **범주 3 우선 순위가 낮은 애플리케이션** - 며칠간의 중단에 견딜 수 있습니다.
2. 범주 1과 2의 애플리케이션에 대해 다음과 같이 허용하려는 액세스 수준 유형을 미리 계획하는 것이 좋습니다.
   * 다운로드 제한과 같이 전체 액세스 또는 제한된 세션을 허용하겠습니까?
   * 전체 애플리케이션이 아닌 일부 애플리케이션에만 액세스를 허용하겠습니까?
   * 정보 근로자 액세스를 허용하고 액세스 제어가 복원될 때까지 관리자 액세스를 차단하겠습니까?
3. 이러한 애플리케이션의 경우 다음과 같이 사용자가 의도적으로 열거나 닫을 수 있는 액세스 방법을 계획하는 것이 좋습니다.
   * 브라우저 전용 액세스를 허용하고 오프라인 데이터를 저장할 수 있는 리치 클라이언트를 차단하겠습니까?
   * 회사 네트워크 내의 사용자에게만 액세스를 허용하고 외부 사용자는 차단하겠습니까?
   * 중단 동안에만 특정 국가 또는 지역의 액세스를 허용하겠습니까?
   * 대체 액세스 제어를 사용할 수 없는 경우 대응 정책, 특히 중요 업무용 애플리케이션에 대한 대응 정책이 실패하거나 성공하도록 하겠습니까?

#### <a name="microsoft-recommendations"></a>Microsoft 추천 사항

대체 조건부 액세스 정책은 Azure MFA, 타사 MFA, 위험 기반 또는 장치 기반 컨트롤을 생략 하는 **백업 정책** 입니다. 대체 정책을 사용 하는 경우 예기치 않은 중단을 최소화 하기 위해 정책은 사용 하지 않을 때 보고서 전용 모드에서 유지 되어야 합니다. 관리자는 조건부 액세스 정보 통합 문서를 사용 하 여 해당 대체 정책의 잠재적 영향을 모니터링할 수 있습니다. 조직에서 대체 계획을 활성화 하기로 결정 한 경우 관리자는 정책을 사용 하도록 설정 하 고 일반 제어 기반 정책을 사용 하지 않도록 설정할 수 있습니다.

>[!IMPORTANT]
> 사용자에게 보안을 적용하는 정책을 일시적이라도 사용하지 않도록 설정하면 대응 계획이 있는 동안 보안 상태가 저하됩니다.

* 하나의 자격 증명 유형 또는 하나의 액세스 제어 메커니즘의 중단으로 인해 앱에 대한 액세스에 영향을 주는 경우 일단의 대체 정책을 구성합니다. 도메인 가입을 제어로 사용 하는 보고서 전용 상태의 정책을 타사 MFA 공급자가 필요한 활성 정책에 대 한 백업으로 구성 합니다.
* [암호 지침](https://aka.ms/passwordguidance) 백서의 사례에 따라 MFA를 요구하지 않을 때 악의적인 행위자의 암호 추측에 대한 위험을 줄입니다.
* [Azure AD SSPR(셀프 서비스 암호 재설정)](./tutorial-enable-sspr.md) 및 [Azure AD 암호 보호](./howto-password-ban-bad-on-premises-deploy.md)를 배포하여 사용자가 금지하도록 선택한 일반적인 암호와 용어를 사용하지 못하도록 합니다.
* 특정 인증 수준에 도달하지 못하는 경우 단순히 전체 액세스로 대체하는 대신 앱 내에서 액세스를 제한하는 정책을 사용합니다. 예를 들면 다음과 같습니다.
  * 제한된 세션 클레임을 Exchange 및 SharePoint로 보내는 백업 정책을 구성합니다.
  * 조직에서 MCAS(Microsoft Cloud App Security)를 사용하는 경우 MCAS를 사용하는 정책으로 대체한 다음, MCAS에서 읽기 전용 액세스만 허용하고 업로드는 허용하지 않는 것이 좋습니다.
* 중단 시 정책을 쉽게 찾을 수 있도록 해당 정책의 이름을 지정합니다. 정책 이름에 포함되는 요소는 다음과 같습니다.
  * 정책에 대한 *레이블 번호*
  * 표시할 텍스트 - 이 정책은 응급 상황에만 적용됩니다. 예: **응급에서 사용**
  * 적용되는 *중단*. 예: **MFA 중단 중**
  * 정책을 활성화해야 하는 순서를 나타내는 *시퀀스 번호*
  * 적용되는 *앱*
  * 적용할 *제어*
  * 필요한 *조건*
  
대체 정책에 대한 이 명명 표준은 다음과 같습니다. 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

다음 예: **중요 업무용 공동 작업 앱에 대 한 액세스를 복원 하는-대체 CA 정책의 예**는 일반적인 회사의 대책입니다. 이 시나리오에서 조직은 일반적으로 모든 Exchange Online 및 SharePoint Online 액세스에서 MFA를 요구하며, 이 경우 중단되면 고객에 대한 MFA 공급자(Azure MFA, 온-프레미스 MFA 공급자 또는 타사 MFA)가 중단된 것입니다. 이 정책은 특정 대상 사용자가 신뢰할 수 있는 회사 네트워크에서 해당 애플리케이션에 액세스할 때만 신뢰할 수 있는 Windows 디바이스에서 이러한 애플리케이션에 액세스할 수 있도록 하여 이러한 중단을 완화합니다. 또한 응급 계정과 핵심 관리자도 이러한 제한에서 제외됩니다. 그러면 대상으로 지정된 사용자는 Exchange Online 및 SharePoint Online에 액세스할 수 있지만, 다른 사용자는 중단으로 인해 애플리케이션에 액세스할 수 없습니다. 이 예제에서는 대상 사용자가 있는 **CorpNetwork** 네트워크 위치와 **ContingencyAccess** 보안 그룹, 핵심 관리자가 있는 **CoreAdmins** 그룹 및 응급 액세스 계정이 있는 **EmergencyAccess** 그룹이 필요합니다. 긴급 상황에는 원하는 액세스를 제공하는 4가지 정책이 필요합니다. 

**예제 A - 중요 업무용 협업 앱에 대한 액세스를 복원하는 대응 CA 정책:**

* 정책 1: Exchange 및 SharePoint에 대해 도메인에 가입 된 장치 필요
  * 이름: EM001-응급에서 사용: MFA 중단 [1/4]-Exchange SharePoint-하이브리드 Azure AD 조인 필요
  * 사용자 및 그룹: ContingencyAccess를 포함 합니다. CoreAdmins 및 EmergencyAccess 제외
  * 클라우드 앱: Exchange Online 및 SharePoint Online
  * 조건: 모든
  * Grant Control: 도메인에 가입 되어 있어야 합니다.
  * 상태: 보고서 전용
* 정책 2: Windows 이외의 플랫폼 차단
  * 이름: EM002-응급 상황에서 사용: MFA 중단 [2/4]-Exchange SharePoint-Windows를 제외한 액세스 차단
  * 사용자 및 그룹: 모든 사용자를 포함 합니다. CoreAdmins 및 EmergencyAccess 제외
  * 클라우드 앱: Exchange Online 및 SharePoint Online
  * 조건: 모든 플랫폼을 포함 하는 장치 플랫폼, Windows 제외
  * Grant 컨트롤: Block
  * 상태: 보고서 전용
* 정책 3: CorpNetwork 이외의 네트워크 차단
  * 이름: EM003-응급에서 사용: MFA 중단 [3/4]-Exchange SharePoint-회사 네트워크를 제외한 액세스 차단
  * 사용자 및 그룹: 모든 사용자를 포함 합니다. CoreAdmins 및 EmergencyAccess 제외
  * 클라우드 앱: Exchange Online 및 SharePoint Online
  * 조건: 위치에는 모든 위치가 포함 되 고 CorpNetwork 제외 됩니다.
  * Grant 컨트롤: Block
  * 상태: 보고서 전용
* 정책 4: 명시적으로 EAS 차단
  * 이름: EM004-응급 상황에서 사용: MFA 중단 [4/4]-Exchange-모든 사용자에 대해 EAS 차단
  * 사용자 및 그룹: 모든 사용자 포함
  * 클라우드 앱: Exchange Online 포함
  * 조건: 클라이언트 앱: Exchange Active Sync
  * Grant 컨트롤: Block
  * 상태: 보고서 전용

활성화 순서:

1. 기존 MFA 정책에서 ContingencyAccess, CoreAdmins 및 EmergencyAccess를 제외합니다. ContingencyAccess의 사용자가 SharePoint Online 및 Exchange Online에 액세스할 수 있는지 확인합니다.
2. 정책 사용 1: 제외 그룹에 없는 도메인 가입 장치에 대 한 사용자가 Exchange Online 및 SharePoint Online에 액세스할 수 있는지 확인 합니다. 제외 그룹에 속한 사용자가 모든 디바이스에서 SharePoint Online 및 Exchange에 액세스할 수 있는지 확인합니다.
3. 정책 사용 2: 제외 그룹에 없는 사용자가 모바일 장치에서 SharePoint Online 및 Exchange Online에 액세스할 수 없는지 확인 합니다. 제외 그룹에 속한 사용자가 모든 디바이스(Windows/iOS/Android)에서 SharePoint 및 Exchange에 액세스할 수 있는지 확인합니다.
4. 정책 사용 3: 제외 그룹에 없는 사용자가 도메인에 가입 된 컴퓨터를 사용 하더라도 SharePoint에 액세스할 수 없고 회사 네트워크에서 Exchange에 액세스할 수 없는지 확인 합니다. 제외 그룹에 속한 사용자가 모든 네트워크에서 SharePoint 및 Exchange에 액세스할 수 있는지 확인합니다.
5. 정책 4 사용: 모든 사용자가 모바일 장치의 네이티브 메일 응용 프로그램에서 Exchange Online을 가져올 수 있는지 확인 합니다.
6. SharePoint Online 및 Exchange Online에 대한 기존 MFA 정책을 사용하지 않도록 설정합니다.

**예제 B - Salesforce에 대한 모바일 액세스를 허용하는 대응 CA 정책** 예제에서는 비즈니스 앱의 액세스가 복원됩니다. 이 시나리오에서 고객은 일반적으로 판매 직원이 모바일 디바이스에서 Salesforce(Azure AD를 통한 Single Sign-On으로 구성됨)에 액세스하여 규정 준수 디바이스에서만 허용되도록 요구합니다. 이 경우 중단이 발생하면 디바이스의 규정 준수를 평가하는 데 문제가 있으며, 판매 팀이 거래를 성사시키기 위해 Salesforce에 액세스해야 하는 중요한 시점에 중단이 발생하는 것입니다. 이러한 대응 정책을 통해 중요한 사용자는 모바일 디바이스에서 Salesforce에 액세스할 수 있으므로 거래를 계속 성사시키고 비즈니스를 중단하지 않을 수 있습니다. 이 예제에서 **SalesforceContingency**에는 액세스를 유지해야 하는 모든 판매 직원이 포함되며, **SalesAdmins**에는 Salesforce의 필수 관리자가 포함됩니다.

**예제 B - 대응 CA 정책:**

* 정책 1: SalesContingency 팀에 없는 모든 사용자 차단
  * 이름: EM001-응급에서 사용: 장치 준수 중단 [1/2]-Salesforce-SalesforceContingency를 제외한 모든 사용자 차단
  * 사용자 및 그룹: 모든 사용자를 포함 합니다. SalesAdmins 및 SalesforceContingency 제외
  * 클라우드 앱: Salesforce.
  * 조건: 없음
  * Grant 컨트롤: Block
  * 상태: 보고서 전용
* 정책 2: 모바일이 아닌 플랫폼에서 영업 팀 차단 (공격 노출 영역을 줄이기 위해)
  * 이름: EM002-응급에서 사용: 장치 준수 중단 [2/2]-Salesforce-iOS 및 Android를 제외한 모든 플랫폼 차단
  * 사용자 및 그룹: SalesforceContingency를 포함 합니다. SalesAdmins 제외
  * 클라우드 앱: Salesforce
  * 조건: 모든 플랫폼을 포함 하는 장치 플랫폼, iOS 및 Android 제외
  * Grant 컨트롤: Block
  * 상태: 보고서 전용

활성화 순서:

1. Salesforce에 대한 기존 디바이스 규정 준수 정책에서 SalesAdmins 및 SalesforceContingency를 제외합니다. SalesforceContingency 그룹에 속한 사용자가 Salesforce에 액세스할 수 있는지 확인합니다.
2. 정책 사용 1: SalesContingency 외부 사용자가 Salesforce에 액세스할 수 없는지 확인 합니다. SalesAdmins 및 SalesforceContingency에 속한 사용자가 Salesforce에 액세스할 수 있는지 확인합니다.
3. 정책 사용 2: SalesContingency 그룹의 사용자가 Windows/Mac 랩톱에서 Salesforce에 액세스할 수 없지만 모바일 장치에서 액세스할 수 있는지 확인 합니다. SalesAdmin이 모든 디바이스에서 Salesforce에 계속 액세스할 수 있는지 확인합니다.
4. Salesforce에 대한 기존 디바이스 규정 준수 정책을 사용하지 않도록 설정합니다.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>온-프레미스 리소스의 사용자 잠금에 대 한 긴급 한 (NPS 확장)

Azure MFA NPS 확장을 사용 하 여 VPN 액세스를 보호 하는 경우 VPN 솔루션을 [SAML 앱](../manage-apps/view-applications-portal.md) 으로 페더레이션 하 고 아래 권장 된 대로 앱 범주를 확인 하는 것이 좋습니다. 

Azure AD MFA NPS 확장을 배포 하 여 VPN 및 원격 데스크톱 게이트웨이 같은 온-프레미스 리소스 (예: MFA)를 보호 하는 경우 응급 상황에서 MFA를 사용 하지 않도록 설정할 준비가 되었으면 미리 고려해 야 합니다.

이 경우 nps 확장을 사용 하지 않도록 설정할 수 있습니다. 따라서 NPS 서버는 기본 인증만 확인 하 고 사용자에 게 MFA를 적용 하지 않습니다.

NPS 확장 사용 안 함: 
-   HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters 레지스트리 키를 백업으로 내보냅니다. 
-   매개 변수 키가 아닌 "AuthorizationDLLs" 및 "ExtensionDLLs"에 대 한 레지스트리 값을 삭제 합니다. 
-   네트워크 정책 서비스 (IAS) 서비스를 다시 시작 하 여 변경 내용을 적용 합니다. 
-   VPN에 대 한 기본 인증의 성공 여부를 확인 합니다.

서비스가 복구 되 고 사용자에 게 MFA를 다시 적용할 준비가 되 면 NPS 확장을 사용 하도록 설정 합니다. 
-   백업 HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters에서 레지스트리 키를 가져옵니다. 
-   네트워크 정책 서비스 (IAS) 서비스를 다시 시작 하 여 변경 내용을 적용 합니다. 
-   기본 인증 및 VPN에 대 한 보조 인증의 성공 여부를 확인 합니다.
-   NPS 서버 및 VPN 로그를 검토 하 여 응급 기간 중에 로그인 한 사용자를 확인 합니다.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>페더레이션되었거나 통과 인증을 사용하는 경우에도 암호 해시 동기화 배포

다음 조건에 해당하는 경우에도 사용자 잠금이 발생할 수 있습니다.

- 조직에서 통과 인증 또는 페더레이션을 통해 하이브리드 ID 솔루션을 사용합니다.
- 온-프레미스 ID 시스템(예: Active Directory, AD FS 또는 종속 구성 요소)을 사용할 수 없습니다. 
 
온-프레미스 ID 시스템의 가동이 중단되면 [암호 해시 동기화를 사용하도록 전환](../hybrid/plan-connect-user-signin.md)할 수 있으므로 조직에서 복원력을 높이려면 [암호 해시 동기화를 사용하도록 설정](../hybrid/choose-ad-authn.md)해야 합니다.

#### <a name="microsoft-recommendations"></a>Microsoft 추천 사항
 조직에서 페더레이션 또는 통과 인증을 사용하는지 여부에 관계없이 Azure AD Connect 마법사를 사용하여 암호 해시 동기화를 사용하도록 설정합니다.

>[!IMPORTANT]
> 암호 해시 동기화를 사용하기 위해 사용자를 페더레이션 인증에서 관리 인증으로 변환할 필요가 없습니다.

## <a name="during-a-disruption"></a>중단 중

완화 계획을 구현하도록 선택한 경우 단일 액세스 제어 중단을 자동으로 견딜 수 있습니다. 그러나 대응 계획을 만들도록 선택한 경우 액세스 제어 중단 중에 다음과 같은 대응 정책을 활성화할 수 있습니다.

1. 특정 네트워크에서 특정 애플리케이션에 대한 액세스 권한을 대상 사용자에게 부여하는 대응 정책을 사용하도록 설정합니다.
2. 일반 제어 기반 정책을 사용 하지 않도록 설정합니다.

### <a name="microsoft-recommendations"></a>Microsoft 추천 사항

중단 중에 사용되는 완화 또는 대응 정책에 따라 조직에서 암호만 사용하는 액세스 권한을 부여할 수 있습니다. 보호 수단은 신중하게 고려해야 하는 중요한 보안상의 위험이 아닙니다. 조직에서 다음과 같이 수행해야 합니다.

1. 변경 제어 전략의 일환으로 액세스 제어가 완전히 작동하는 즉시 구현한 모든 긴급 상황을 롤백할 수 있도록 모든 변경 내용과 이전 상태를 문서화합니다.
2. 악의적인 행위자가 MFA를 사용하지 않는 동안 암호 스프레이 또는 피싱 공격을 통해 암호를 수집하려고 한다고 가정합니다. 또한 이전에 이 기간 동안 시도될 수 있는 리소스에 대한 액세스 권한을 부여하지 않은 암호가 악의적인 행위자에게 이미 있을 수도 있습니다. 임원과 같은 중요한 사용자의 경우 MFA를 사용하지 않도록 설정하기 전에 암호를 다시 설정하여 이 위험을 부분적으로 완화할 수 있습니다.
3. 모든 로그인 활동을 보관하여 MFA가 사용되지 않는 동안 액세스한 사용자를 식별합니다.
4. 이 기간 동안 [보고 된 모든 위험 감지를 심사](../reports-monitoring/concept-sign-ins.md) 합니다.

## <a name="after-a-disruption"></a>중단 후

중단이 발생한 서비스가 복원되면 활성화된 대체 계획의 일환으로 변경을 실행 취소합니다. 

1. 일반 정책을 사용하도록 설정합니다.
2. 대체 정책을 보고서 전용 모드로 다시 사용 하지 않도록 설정 합니다. 
3. 중단 중에 수행되고 문서화된 다른 변경을 롤백합니다.
4. 응급 액세스 계정을 사용한 경우 응급 액세스 계정 절차의 일환으로 자격 증명을 다시 생성하고 새 자격 증명의 세부 정보를 물리적으로 보호합니다.
5. 의심 스러운 활동의 중단 이후에 [보고 된 모든 위험](../reports-monitoring/concept-sign-ins.md) 검색을 계속 심사 합니다.
6. [PowerShell을 사용](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)하여 발급된 모든 새로 고침 토큰을 철회하여 일단의 사용자를 대상으로 지정합니다. 모든 새로 고침 토큰을 철회하는 것은 중단 중에 사용되는 권한 있는 계정에 중요합니다. 이를 수행하는 경우 복원된 정책의 제어를 다시 인증하고 충족하도록 강제로 적용합니다.

## <a name="emergency-options"></a>응급 옵션

 응급 상황에서 조직이 완화 또는 대체 계획을 이전에 구현 하지 않은 경우 이미 조건부 액세스 정책을 사용 하 여 MFA를 적용 하는 경우 [사용자 잠금에 대 한 응급](#contingencies-for-user-lockout) 섹션의 권장 사항을 따릅니다.
조직에서 사용자별 MFA 레거시 정책을 사용하는 경우 고려할 수 있는 대안은 다음과 같습니다.

1. 회사 네트워크 아웃바운드 IP 주소가 있으면 회사 네트워크에만 인증할 수 있도록 이를 신뢰할 수 있는 IP로 추가합니다.
   1. 아웃바운드 IP 주소의 인벤토리가 없거나 회사 네트워크 내부 및 외부에서 액세스할 수 있어야 하는 경우 0.0.0.0/1 및 128.0.0.0/1을 지정하여 전체 IPv4 주소 공간을 신뢰할 수 있는 IP로 추가할 수 있습니다.

>[!IMPORTANT]
 > 액세스를 차단 해제 하기 위해 신뢰할 수 있는 IP 주소를 확장 한 경우에는 IP 주소 (예: 불가능 한 이동 또는 익숙하지 않은 위치)와 관련 된 위험 검색이 생성 되지 않습니다.

>[!NOTE]
 > Azure MFA에 대한 [신뢰할 수 있는 IP](./howto-mfa-mfasettings.md)는 [Azure AD Premium 라이선스](./concept-mfa-licensing.md)에서만 구성할 수 있습니다.

## <a name="learn-more"></a>자세한 정보

* [Azure AD 인증 설명서](./howto-mfaserver-iis.md)
* [Azure AD에서 응급 액세스 관리 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory에서 명명 된 위치 구성](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법](../devices/hybrid-azuread-join-plan.md)
* [비즈니스용 Windows Hello 배포 가이드](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [암호 지침 - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [조건부 액세스 Azure Active Directory의 조건은 무엇입니까?](../conditional-access/concept-conditional-access-conditions.md)
* [Azure Active Directory 조건부 액세스의 액세스 제어 정의](../conditional-access/controls.md)
* [조건부 액세스 보고서 전용 모드](../conditional-access/concept-conditional-access-report-only.md)