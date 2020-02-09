---
title: 구성 가능한 Azure AD 토큰 수명
titleSuffix: Microsoft identity platform
description: Azure AD에서 발급한 토큰의 수명을 설정하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: ryanwi
ms.custom: aaddev, annaba, identityplatformtop40
ms.reviewer: hirsin
ms.openlocfilehash: 55c7ee6711c6001745053b850c1b4e1859af5dbe
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76699022"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Azure Active Directory에서 구성 가능한 토큰 수명(미리 보기)

Azure AD(Azure Active Directory)에서 발급한 토큰의 수명을 지정할 수 있습니다. 조직의 모든 앱, 다중 테넌트(다중 조직) 애플리케이션 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성할 수 있습니다.

> [!IMPORTANT]
> 미리 보기 중에 고객의 의견을 확인 한 후 Azure AD 조건부 액세스에서 [인증 세션 관리 기능](https://go.microsoft.com/fwlink/?linkid=2083106) 을 구현 했습니다. 이 새로운 기능을 사용 하 여 로그인 빈도를 설정 하 여 새로 고침 토큰 수명을 구성할 수 있습니다. 5 월 1 2020 일 이후에는 구성 가능한 토큰 수명 정책을 사용 하 여 세션 및 새로 고침 토큰을 구성할 수 없습니다. 사용 중단 후에도 액세스 토큰 수명을 구성할 수 있습니다.

Azure AD에서 정책 개체는 개별 애플리케이션 또는 조직의 모든 애플리케이션에 적용되는 규칙 집합을 나타냅니다. 각 정책 유형에는 할당된 개체에 적용되는 속성 집합이 포함된 고유의 구조가 있습니다.

조직의 기본 정책이 될 정책을 지정할 수 있습니다. 해당 정책은 더 높은 우선 순위의 정책으로 재정의되지 않는 한 조직 내의 모든 애플리케이션에 적용됩니다. 특정 애플리케이션에 정책을 할당할 수도 있습니다. 우선 순위는 정책 유형에 따라 다릅니다.

> [!NOTE]
> 구성 가능한 토큰 수명 정책은 SharePoint Online에서 지원되지 않습니다.  PowerShell을 통해 이 정책을 만들 수 있지만, SharePoint Online에서는 이 정책을 승인하지 않습니다. 유휴 세션 제한 시간을 구성하는 방법에 대한 자세한 내용은 [SharePoint Online 블로그](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)를 참조하세요.
>* SharePoint Online 액세스 토큰의 기본 수명은 1시간입니다. 
>* SharePoint Online 새로 고침 토큰의 기본 최대 비활성 시간은 90일입니다.

## <a name="token-types"></a>토큰 형식

새로 고침 토큰, 액세스 토큰, SAML 토큰, 세션 토큰 및 ID 토큰에 대 한 토큰 수명 정책을 설정할 수 있습니다.

### <a name="access-tokens"></a>액세스 토큰

클라이언트는 액세스 토큰을 사용하여 보호된 리소스에 액세스합니다. 액세스 토큰은 사용자, 클라이언트 및 리소스의 특정 조합에만 사용할 수 있습니다. 액세스 토큰은 해지될 수 없으며 만료될 때까지 유효합니다. 액세스 토큰을 획득한 악의적인 행위자는 수명 범위 동안 이를 사용할 수 있습니다. 액세스 토큰의 수명을 조정하는 것은 시스템 성능을 개선하는 것과 사용자 계정이 비활성화된 후 클라이언트에서 액세스를 유지하는 기간을 늘리는 것 간의 절충 작업입니다. 시스템 성능을 개선하려면 클라이언트에서 새 액세스 토큰을 획득해야 하는 횟수를 줄이면 됩니다.  기본값은 1시간입니다. 1시간 후 클라이언트는 새로 고침 토큰을 사용하여 새로운 새로 고침 토큰을 획득(보통 자동으로)하고 토큰에 액세스해야 합니다. 

### <a name="saml-tokens"></a>SAML 토큰

SAML 토큰은 많은 웹 기반 SAAS 응용 프로그램에서 사용 되며 Azure Active Directory의 SAML2 프로토콜 엔드포인트을 사용 하 여 가져옵니다. WS-FEDERATION을 사용 하는 응용 프로그램 에서도 사용 됩니다. 토큰의 기본 수명은 1 시간입니다. 응용 프로그램의 관점에서 토큰의 유효 기간은 토큰의 `<conditions …>` 요소에 대 한 NotOnOrAfter 값으로 지정 됩니다. 토큰의 유효 기간이 끝난 후 클라이언트는 새 인증 요청을 시작 해야 합니다 .이 요청은 SSO (Single Sign On) 세션 토큰의 결과로 대화형 로그인 없이 자주 충족 됩니다.

NotOnOrAfter의 값은 `TokenLifetimePolicy`의 `AccessTokenLifetime` 매개 변수를 사용 하 여 변경할 수 있습니다. 정책에 구성 된 수명 및 5 분의 클록 오차 계수로 설정 됩니다.

`<SubjectConfirmationData>` 요소에 지정 된 주체 확인 NotOnOrAfter는 토큰 수명 구성의 영향을 받지 않습니다. 

### <a name="refresh-tokens"></a>새로 고침 토큰

클라이언트가 보호된 리소스에 액세스하기 위해 액세스 토큰을 획득할 때 새로 고침 토큰도 받습니다. 새로 고침 토큰은 현재 액세스 토큰이 만료된 경우 새 액세스/새로 고침 토큰 쌍을 얻는 데 사용됩니다. 새로 고침 토큰은 사용자와 클라이언트 조합에 바인딩됩니다. 새로 고침 토큰은 [언제든지 해지 가능](access-tokens.md#token-revocation)하며 사용될 때마다 토큰의 유효성이 검사됩니다.  새로 고침 토큰이 새 액세스 토큰을 인출하는 데 사용된 경우 해지하지 못합니다. 따라서 새 토큰을 가져올 때 이전 토큰을 안전하게 삭제하는 것이 가장 좋습니다. 

새로 고침 토큰을 사용할 수 있는 기간에 영향을 미치기 때문에 비밀 클라이언트와 공용 클라이언트를 구분하는 것이 중요합니다. 다양한 유형의 클라이언트에 대한 자세한 내용은 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1)를 참조하세요.

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>비밀 클라이언트 새로 고침 토큰의 토큰 수명
비밀 클라이언트는 클라이언트 암호(비밀)를 안전하게 저장할 수 있는 애플리케이션입니다. 비밀 클라이언트는 요청이 악의적인 작업자가 아닌 안전한 클라이언트 애플리케이션에서 오는 것을 증명할 수 있습니다. 예를 들어 웹앱은 웹 서버에 클라이언트 비밀을 저장할 수 있으므로 비밀 클라이언트입니다. 따라서 노출되지 않습니다. 이러한 흐름은 보다 안전하기 때문에 이러한 흐름에 발급된 새로 고침 토큰의 기본 수명은 `until-revoked`이며 정책을 사용하여 변경할 수 없고 자발적인 암호 재설정에서 취소되지 않습니다.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>공용 클라이언트 새로 고침 토큰의 토큰 수명

공용 클라이언트는 클라이언트 암호(비밀)를 안전하게 저장할 수 없습니다. 예를 들어 iOS/Android 앱은 리소스 소유자의 비밀을 난독 처리할 수 없으므로 공용 클라이언트로 간주됩니다. 리소스에 정책을 설정하여 지정된 기간보다 오래된 공용 클라이언트의 새로 고침 토큰이 새 액세스/새로 고침 토큰 쌍을 얻지 못하게 할 수 있습니다. 이렇게 하려면 새로 고침 토큰 최대 비활성 시간 속성 (`MaxInactiveTime`)을 사용 합니다. 정책을 사용 하 여 새로 고침 토큰이 더 이상 허용 되지 않는 기간을 설정할 수도 있습니다. 이렇게 하려면 새로 고침 토큰 최대 기간 속성을 사용 합니다. 공용 클라이언트 응용 프로그램을 사용 하는 경우 자동으로 다시 인증 하는 대신 사용자가 자격 증명을 다시 입력 해야 하는 시기와 빈도를 제어 하도록 새로 고침 토큰의 수명을 조정할 수 있습니다.

> [!NOTE]
> 최대 기간 속성은 단일 토큰을 사용할 수 있는 시간입니다. 

### <a name="id-tokens"></a>ID 토큰
ID 토큰은 웹 사이트 및 기본 클라이언트에 전달됩니다. ID 토큰은 사용자에 대한 프로필 정보를 포함합니다. ID 토큰은 사용자와 클라이언트의 특정 조합에 바인딩되며, ID 토큰은 만료될 때까지 유효한 것으로 간주됩니다. 일반적으로 웹 애플리케이션은 애플리케이션의 사용자 세션 수명을 해당 사용자에 대해 발급된 ID 토큰의 수명과 일치시킵니다. ID 토큰의 수명을 조정하여 웹 애플리케이션이 애플리케이션 세션을 만료하는 빈도 및 사용자에게 Azure AD에 다시 인증하도록(자동으로 또는 대화형으로) 요구하는 빈도를 제어할 수 있습니다.

### <a name="single-sign-on-session-tokens"></a>Single Sign-On 세션 토큰
사용자가 Azure AD로 인증하면 사용자 브라우저와 Azure AD를 사용하여 SSO(Single Sign-On) 세션이 설정됩니다. 쿠키 형식의 SSO 토큰은 이 세션을 나타냅니다. SSO 세션 토큰은 특정 리소스/클라이언트 응용 프로그램에 바인딩되지 않습니다. SSO 세션 토큰은 해지 가능하며 사용될 때마다 토큰의 유효성이 검사됩니다.

Azure AD는 두 종류의 SSO 세션 토큰을 사용합니다. 하나는 영구 세션 토큰이고 다른 하나는 비영구 세션 토큰입니다. 영구 세션 토큰은 브라우저에 영구 쿠키로 저장됩니다. 비영구 세션 토큰은 세션 쿠키로 저장됩니다. 세션 쿠키는 브라우저를 닫을 때 삭제 됩니다. 일반적으로 비영구 세션 토큰이 저장 됩니다. 그러나 사용자가 인증 중에 **로그인 유지** 확인란을 선택하면 영구 세션 토큰이 저장됩니다.

비영구 세션 토큰의 수명은 24시간입니다. 영구 토큰의 수명은 180일입니다. 유효 기간 내에 SSO 세션 토큰을 사용 하는 경우에는 유효 기간이 토큰 유형에 따라 24 시간 또는 180 일로 연장 됩니다. 유효 기간 내에 SSO 세션 토큰을 사용하지 않으면 만료된 것으로 간주하여 더 이상 허용되지 않습니다.

첫 번째 세션 토큰이 발급된 후 특정 시간이 지나면 더 이상 세션 토큰이 허용되지 않도록 정책을 사용하여 시간을 설정할 수 있습니다. 이렇게 하려면 세션 토큰 최대 기간 속성을 사용 합니다. 세션 토큰의 수명을 조정 하 여 웹 응용 프로그램을 사용할 때 자동으로 인증 되는 대신 사용자가 자격 증명을 다시 입력 해야 하는 시기와 빈도를 제어할 수 있습니다.

### <a name="token-lifetime-policy-properties"></a>토큰 수명 정책 속성
토큰 수명 정책은 토큰 수명 규칙을 포함하는 정책 개체의 형식입니다. 정책의 속성을 사용하여 지정된 토큰 수명을 제어할 수 있습니다. 설정된 정책이 없는 경우 시스템에서 기본 수명값을 적용합니다.

### <a name="configurable-token-lifetime-properties"></a>구성 가능한 토큰 수명 속성
| 속성 | 정책 속성 문자열 | 영향 | 기본값 | 최소 | 최대 |
| --- | --- | --- | --- | --- | --- |
| 액세스 토큰 수명 |AccessTokenLifetime<sup>2</sup> |액세스 토큰, ID 토큰, SAML2 토큰 |1시간 |10분 |1일 |
| 새로 고침 토큰 최대 비활성 시간 |MaxInactiveTime |새로 고침 토큰 |90일 |10분 |90일 |
| 단일 단계 새로 고침 토큰 최대 기간 |MaxAgeSingleFactor |새로 고침 토큰(모든 사용자) |Until-revoked |10분 |Until-revoked<sup>1</sup> |
| 다단계 새로 고침 토큰 최대 기간 |MaxAgeMultiFactor |새로 고침 토큰(모든 사용자) |Until-revoked |10분 |Until-revoked<sup>1</sup> |
| 단일 단계 세션 토큰 최대 기간 |MaxAgeSessionSingleFactor |세션 토큰(영구 및 비영구) |Until-revoked |10분 |Until-revoked<sup>1</sup> |
| 다단계 세션 토큰 최대 기간 |MaxAgeSessionMultiFactor |세션 토큰(영구 및 비영구) |Until-revoked |10분 |Until-revoked<sup>1</sup> |

* <sup>1</sup>이러한 특성에 대해 설정할 수 있는 명시적인 최대 기간은 365일입니다.
* <sup>2</sup> Microsoft 팀 웹 클라이언트의 작동을 보장 하려면 Microsoft 팀에 대해 15 분 이상 AccessTokenLifetime를 유지 하는 것이 좋습니다.

### <a name="exceptions"></a>예외
| 속성 | 영향 | 기본값 |
| --- | --- | --- |
| 새로 고침 토큰 최대 기간(해지 정보가 부족한 페더레이션된 사용자에 대해 발급됨<sup>1</sup>) |새로 고침 토큰(해지 정보가 부족한 페더레이션된 사용자에 대해 발급됨<sup>1</sup>) |12시간 |
| 새로 고침 토큰 최대 비활성 시간(비밀 클라이언트에 대해 발급됨) |새로 고침 토큰(비밀 클라이언트에 대해 발급됨) |90일 |
| 새로 고침 토큰 최대 기간(비밀 클라이언트에 대해 발급됨) |새로 고침 토큰(비밀 클라이언트에 대해 발급됨) |Until-revoked |

* <sup>1</sup>해지 정보가 부족한 페더레이션된 사용자에는 "LastPasswordChangeTimestamp" 특성이 동기화되지 않은 모든 사용자가 포함됩니다. 이러한 사용자의 경우 AAD가 이전 자격 증명(예: 변경된 암호)에 연결된 토큰을 해지할 시기를 확인할 수 없어 이 짧은 최대 사용 기간이 제공되며, 사용자 및 연결된 토큰이 여전히 양호한 상태인지 자주 확인해야 합니다. 이 환경을 개선하기 위해 테넌트 관리자는 "LastPasswordChangeTimestamp" 특성을 동기화해야 합니다. 이 특성은 Powershell을 사용하거나 AADSync를 통해 사용자 개체에 설정할 수 있습니다.

### <a name="policy-evaluation-and-prioritization"></a>정책 평가 및 우선 순위 지정
토큰 수명 정책을 만들어서 특정 애플리케이션, 조직 및 서비스 주체에 할당할 수 있습니다. 특정 애플리케이션에 여러 정책이 적용될 수 있습니다. 적용되는 토큰 수명 정책은 다음 규칙을 따릅니다.

* 정책이 서비스 주체에 명시적으로 할당된 경우 해당 정책이 적용됩니다.
* 서비스 주체에 명시적으로 할당된 정책이 없는 경우 서비스 주체의 상위 조직에 명시적으로 할당된 정책이 적용됩니다.
* 서비스 주체 또는 조직에 명시적으로 할당된 정책이 없는 경우 애플리케이션에 할당된 정책이 적용됩니다.
* 서비스 주체, 조직 또는 응용 프로그램 개체에 할당 된 정책이 없는 경우 기본값이 적용 됩니다. [구성 가능한 토큰 수명 속성](#configurable-token-lifetime-properties)의 테이블을 참조하세요.

애플리케이션 개체와 서비스 주체 간의 관계에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

토큰의 유효성은 토큰이 사용되는 시점에 평가됩니다. 액세스하는 애플리케이션에 대한 우선 순위가 가장 높은 정책이 적용됩니다.

여기에 사용되는 시간 범위는 C# [TimeSpan](/dotnet/api/system.timespan) 개체 - D.HH:MM:SS에 따라 서식이 지정됩니다.  따라서 80일 및 30분은 `80.00:30:00`입니다.  앞에 오는 D는 0인 경우 삭제할 수 있으므로 90분은 `00:90:00`입니다.  

> [!NOTE]
> 다음은 예제 시나리오입니다.
>
> 한 사용자가 두 개의 웹 애플리케이션에 액세스하려고 하며 하나는 웹 애플리케이션 A이고 다른 하나는 웹 애플리케이션 B입니다.
> 
> 요소:
> * 두 웹 애플리케이션은 모두 동일한 상위 조직에 있습니다.
> * 세션 토큰 최대 기간이 8시간인 토큰 수명 정책 1은 상위 조직의 기본값으로 설정됩니다.
> * 웹 애플리케이션 A는 어떤 정책에도 연결되지 않은 일반적인 용도의 웹 애플리케이션입니다.
> * 웹 애플리케이션 B는 매우 중요한 프로세스에 사용됩니다. 서비스 주체는 세션 토큰 최대 기간이 30분인 토큰 수명 정책 2에 연결됩니다.
>
> 오후 12시에 사용자가 새 브라우저 세션을 시작하고 웹 애플리케이션 A에 액세스하려고 시도합니다. 이 사용자는 Azure AD로 리디렉션되며 로그인하라는 메시지가 표시됩니다. 그러면 세션 토큰이 있는 쿠키가 브라우저에 생성됩니다. 사용자는 애플리케이션에 액세스할 수 있는 ID 토큰으로 웹 애플리케이션 A로 다시 리디렉션됩니다.
>
> 오후 12시 15분에 사용자가 웹 애플리케이션 B에 액세스하려고 시도합니다. 브라우저는 Azure AD로 리디렉션되고 세션 쿠키를 검색합니다. 웹 애플리케이션 B의 서비스 주체는 토큰 수명 정책 2에 연결되지만 기본 토큰 수명 정책 1이 적용되는 상위 조직의 일부이기도 합니다. 서비스 주체에 연결된 정책이 조직 기본 정책보다 우선 순위가 높기 때문에 토큰 수명 정책 2가 적용됩니다. 세션 토큰은 지난 30분 이내에 처음 발급되었기 때문에 유효한 것으로 간주됩니다. 사용자는 액세스 권한을 부여하는 ID 토큰으로 웹 애플리케이션 B로 리디렉션됩니다.
>
> 오후 1시에 사용자가 웹 애플리케이션 A에 액세스하려고 시도합니다. 사용자는 Azure AD로 리디렉션됩니다. 웹 애플리케이션 A는 어떤 정책에도 연결되어 있지 않지만 기본 토큰 수명 정책 1을 사용하는 조직에 있기 때문에 해당 정책이 적용됩니다. 지난 8시간 이내에 발급된 세션 쿠키가 검색됩니다. 사용자가 새 ID 토큰을 사용하여 자동으로 웹 애플리케이션 A로 다시 리디렉션됩니다. 사용자가 인증할 필요가 없습니다.
>
> 그 후 사용자는 웹 애플리케이션 B에 즉시 액세스하려고 시도합니다. 사용자는 Azure AD로 리디렉션됩니다. 이전과 마찬가지로 토큰 수명 정책 2가 적용됩니다. 토큰이 발급된 시간이 30분을 넘었기 때문에 사용자에게 로그인 자격 증명을 다시 입력하라는 메시지가 표시됩니다. 완전히 새로운 세션 토큰 및 ID 토큰이 발급됩니다. 이제 사용자는 웹 애플리케이션 B에 액세스할 수 있습니다.
>
>

## <a name="configurable-policy-property-details"></a>구성 가능한 정책 속성 세부 정보
### <a name="access-token-lifetime"></a>액세스 토큰 수명
**문자열:** AccessTokenLifetime

**영향을 줍니다.** 액세스 토큰, ID 토큰, SAML 토큰

**요약:** 이 정책은 이 리소스에 대한 액세스 및 ID 토큰이 유효한 것으로 간주되는 기간을 제어합니다. 액세스 토큰 수명 속성을 줄이면 악의적인 행위자가 액세스 토큰 또는 ID 토큰을 장시간 사용할 위험을 완화할 수 있습니다. 이러한 토큰은 취소할 수 없습니다. 즉, 토큰을 더 자주 바꾸어야 하므로 성능이 저하 됩니다.

### <a name="refresh-token-max-inactive-time"></a>새로 고침 토큰 최대 비활성 시간
**문자열:** MaxInactiveTime

**영향:** 새로 고침 토큰

**요약:** 이 정책은 클라이언트에서 이 리소스에 액세스하려고 할 때 새 액세스/새로 고침 토큰을 검색하는 데 새로 고침 토큰을 더 이상 사용할 수 없을 때까지의 기간을 제어합니다. 새로 고침 토큰이 사용될 때 일반적으로 새로운 새로 고침 토큰이 반환되기 때문에 클라이언트가 지정된 기간 동안 현재 새로 고침 토큰을 사용하여 리소스에 액세스하려고 시도하면 이 정책에 따라 액세스가 차단됩니다.

이 정책은 클라이언트에서 활성화되지 않은 사용자가 새로운 새로 고침 토큰을 검색하려면 다시 인증하도록 합니다.

새로 고침 토큰 최대 비활성 시간 속성은 단일 단계 토큰 최대 기간 및 다단계 새로 고침 토큰 최대 기간 속성보다 낮은 값으로 설정해야 합니다.

### <a name="single-factor-refresh-token-max-age"></a>단일 단계 새로 고침 토큰 최대 기간
**문자열:** MaxAgeSingleFactor

**영향:** 새로 고침 토큰

**요약:** 이 정책은 사용자가 단일 단계만 사용하여 마지막으로 인증에 성공한 후 새로 고침 토큰을 사용하여 새로운 액세스/새로 고침 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 새로 고침 토큰을 얻은 후 지정된 기간 동안 새로 고침 토큰 흐름을 사용할 수 있습니다. 이는 현재 새로 고침 토큰이 해지 되지 않고 비활성 시간 보다 오랫동안 사용 하지 않도록 남아 있는 경우에만 적용 됩니다. 이 시점에서 사용자는 다시 인증 하 여 새로운 새로 고침 토큰을 받아야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 다단계 새로 고침 토큰 최대 기간 속성보다 작거나 같은 값으로 설정하는 것이 좋습니다.

### <a name="multi-factor-refresh-token-max-age"></a>다단계 새로 고침 토큰 최대 기간
**문자열:** MaxAgeMultiFactor

**영향:** 새로 고침 토큰

**요약:** 이 정책은 사용자가 다단계를 사용하여 마지막으로 인증에 성공한 후 새로 고침 토큰을 사용하여 새로운 액세스/새로 고침 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 새로 고침 토큰을 얻은 후 지정된 기간 동안 새로 고침 토큰 흐름을 사용할 수 있습니다. 이는 현재 새로 고침 토큰이 해지 되지 않고, 비활성 시간 보다 오랫동안 사용 되지 않는 경우에만 적용 됩니다. 이 시점에서 사용자는 다시 인증 하 여 새로운 새로 고침 토큰을 받아야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 단일 단계 새로 고침 토큰 최대 기간 속성보다 크거나 같은 값으로 설정하는 것이 좋습니다.

### <a name="single-factor-session-token-max-age"></a>단일 단계 세션 토큰 최대 기간
**문자열:** MaxAgeSessionSingleFactor

**영향:** 세션 토큰(영구 및 비영구)

**요약:** 이 정책은 사용자가 단일 단계만 사용하여 마지막으로 인증에 성공한 후 세션 토큰을 사용하여 새로운 ID 및 세션 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 세션 토큰을 얻은 후 지정된 기간 동안 세션 토큰 흐름을 사용할 수 있습니다. 이는 현재 세션 토큰이 해지 되지 않고 만료 되지 않은 경우에만 적용 됩니다. 지정 된 시간 후에는 사용자가 새 세션 토큰을 받기 위해 다시 인증 해야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 다단계 세션 토큰 최대 기간 속성보다 작거나 같은 값으로 설정하는 것이 좋습니다.

### <a name="multi-factor-session-token-max-age"></a>다단계 세션 토큰 최대 기간
**문자열:** MaxAgeSessionMultiFactor

**영향:** 세션 토큰(영구 및 비영구)

**요약:** 이 정책은 사용자가 다단계를 사용하여 마지막으로 인증에 성공한 후 세션 토큰을 사용하여 새로운 ID 및 세션 토큰을 얻을 수 있는 기간을 제어합니다. 사용자는 인증하여 새로운 세션 토큰을 얻은 후 지정된 기간 동안 세션 토큰 흐름을 사용할 수 있습니다. 이는 현재 세션 토큰이 해지 되지 않고 만료 되지 않은 경우에만 적용 됩니다. 지정 된 시간 후에는 사용자가 새 세션 토큰을 받기 위해 다시 인증 해야 합니다.

최대 기간을 줄이면 사용자가 좀 더 자주 인증해야 합니다. 단일 단계 인증은 Multi-Factor Authentication보다 안전하지 않으므로 이 속성을 단일 단계 세션 토큰 최대 기간 속성보다 크거나 같은 값으로 설정하는 것이 좋습니다.

## <a name="example-token-lifetime-policies"></a>토큰 수명 정책 예제
앱, 서비스 주체 및 전체 조직의 토큰 수명을 만들고 관리할 수 있다면 Azure AD에서 다양한 시나리오가 가능합니다. 이 섹션에서는 새 규칙을 적용하는 데 도움이 되는 몇 가지 일반적인 정책 시나리오를 살펴보겠습니다.

* 토큰 수명
* 토큰 최대 비활성 시간
* 토큰 최대 기간

이 예제에서는 다음 작업을 수행하는 방법을 배울 수 있습니다.

* 조직의 기본 정책 관리
* 웹 로그인에 대한 정책 만들기
* web API를 호출하는 네이티브 앱에 대한 정책 만들기
* 고급 정책 관리

### <a name="prerequisites"></a>필수 조건
다음 예제에서는 앱, 서비스 주체 및 조직 전체에 대한 정책을 만들고, 업데이트하고, 연결하고, 삭제해 보겠습니다. Azure AD을 처음 접하는 분들은 [Azure AD 테넌트를 가져오는 방법](quickstart-create-new-tenant.md)을 살펴본 후 예제를 진행하는 것이 좋습니다.  

시작하려면 다음 단계 중 하나를 수행합니다.

1. 최신 [Azure AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드합니다.
2. `Connect` 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 조직에서 만든 모든 정책을 확인하려면 다음 명령을 실행합니다. 다음 시나리오에서 대부분의 작업 후에 이 명령을 실행합니다. 이 명령을 실행하면 정책의 ** **을(를) 가져오는 데도 도움이 됩니다.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>예: 조직의 기본 정책 관리
이 예제에서는 사용자가 전체 조직에서 더 덜 자주 로그인 할 수 있도록 하는 정책을 만듭니다. 이렇게 하기 위해 전체 조직에 적용되는 단일 단계 새로 고침 토큰에 대한 토큰 수명 정책을 만듭니다. 이 정책은 조직의 모든 애플리케이션과 아직 정책이 설정되지 않은 각 서비스 주체에 적용됩니다.

1. 토큰 수명 정책을 만듭니다.

    1. 단일 단계 새로 고침 토큰을 "until-revoked"로 설정합니다. 이 토큰은 액세스가 해지될 때까지 만료되지 않습니다. 다음 정책 정의를 만듭니다.

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. 정책을 만들려면 다음 명령을 실행합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. 새 정책을 보고 정책의 **ObjectId**를 가져오려면 다음 명령을 실행합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 정책을 업데이트합니다.

    이 예에서 설정한 첫 번째 정책이 서비스에 필요한 만큼 엄격한지 확인할 수 있습니다. 단일 단계 새로 고침 토큰이 이틀 후에 만료되도록 설정하려면 다음 명령을 실행합니다.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>예: 웹 로그인에 대한 정책 만들기

이 예에서는 사용자가 웹앱에 보다 자주 인증하도록 요구하는 정책을 만듭니다. 이 정책은 액세스/ID 토큰의 수명 및 다단계 세션 토큰의 최대 기간을 웹앱의 서비스 주체로 설정합니다.

1. 토큰 수명 정책을 만듭니다.

    웹 로그인에 대한 이 정책은 액세스/ID 토큰 수명 및 최대 단일 단계 세션 토큰 기간을 2시간으로 설정합니다.

    1. 정책을 만들려면 이 명령을 실행합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. 새 정책을 보고 정책 **ObjectId**를 가져오려면 다음 명령을 실행합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 **ObjectId**도 가져와야 합니다.

    1. [Get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet을 사용 하 여 조직의 모든 서비스 주체 또는 단일 서비스 주체를 확인 합니다.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. 서비스 사용자가 있는 경우 다음 명령을 실행 합니다.
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>예: web API를 호출하는 네이티브 앱에 대한 정책 만들기
이 예에서는 사용자가 보다 적게 인증하도록 요구하는 정책을 만듭니다. 또한 이 정책은 사용자가 다시 인증해야 할 때까지 걸리는 비활성 시간을 연장합니다. 이 정책은 web API에 적용됩니다. 네이티브 앱이 리소스로 web API를 요청하면 이 정책이 적용됩니다.

1. 토큰 수명 정책을 만듭니다.

    1. Web API에 대한 엄격한 정책을 만들려면 다음 명령을 실행합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. 새 정책을 보려면 다음 명령을 실행 합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. web API에 정책을 할당합니다. 애플리케이션의 **ObjectId**도 가져와야 합니다. [Azureadapplication.applicationid](/powershell/module/azuread/get-azureadapplication) cmdlet을 사용 하 여 앱의 **ObjectId**를 찾거나 [Azure Portal](https://portal.azure.com/)를 사용 합니다.

    앱의 **ObjectId** 를 가져오고 정책을 할당 합니다.

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>예: 고급 정책 관리
이 예에서는 몇 가지 정책을 만들어 우선 순위 시스템의 작동 방식을 알아봅니다. 여러 개체에 적용 되는 여러 정책을 관리 하는 방법에 대해서도 알아봅니다.

1. 토큰 수명 정책을 만듭니다.

    1. 단일 단계 새로 고침 토큰 수명을 30일로 설정하는 조직 기본 정책을 만들려면 다음 명령을 실행합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. 새 정책을 보려면 다음 명령을 실행 합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. 서비스 주체에게 정책을 할당합니다.

    이제 조직 전체에 적용되는 정책이 생겼습니다. 특정 서비스 주체에 대해 이 30일 정책을 유지하지만 조직 기본 정책을 "until-revoked"의 상한으로 변경하고 싶은 경우가 있습니다.

    1. 조직의 모든 서비스 주체를 보려면 [get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet을 사용 합니다.

    2. 서비스 사용자가 있는 경우 다음 명령을 실행 합니다.

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. `IsOrganizationDefault` 플래그를 false로 설정합니다.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. 새로운 조직 기본 정책을 만듭니다.

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    이제 서비스 주체에 연결된 원래 정책과 조직 기본 정책으로 설정된 새 정책이 있습니다. 서비스 주체에 적용되는 정책은 조직 기본 정책보다 우선한다는 점을 기억해야 합니다.

## <a name="cmdlet-reference"></a>Cmdlet 참조

### <a name="manage-policies"></a>정책 관리

다음 cmdlet을 사용하여 정책을 관리할 수 있습니다.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

새 정책을 만듭니다.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Definition</code> |정책의 모든 규칙을 포함하는 문자열로 변환된 JSON 배열입니다. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |정책 이름의 문자열입니다. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |true이면 정책을 조직의 기본 정책으로 설정하고 false이면 아무 작업도 수행하지 않습니다. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |정책의 유형입니다. 토큰 수명의 경우 항상 "TokenLifetimePolicy"를 사용합니다. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[선택 사항] |정책에 대한 대체 ID를 설정합니다. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
모든 Azure AD 정책 또는 지정된 정책을 가져옵니다.

```powershell
Get-AzureADPolicy
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code>[선택 사항] |원하는 정책의 **ObjectId (ID)** 입니다. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
정책에 연결된 모든 앱 및 서비스 주체를 가져옵니다.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |원하는 정책의 **ObjectId (ID)** 입니다. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
기존 정책을 업데이트합니다.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |원하는 정책의 **ObjectId (ID)** 입니다. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |정책 이름의 문자열입니다. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[선택 사항] |정책의 모든 규칙을 포함하는 문자열로 변환된 JSON 배열입니다. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[선택 사항] |true이면 정책을 조직의 기본 정책으로 설정하고 false이면 아무 작업도 수행하지 않습니다. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[선택 사항] |정책의 유형입니다. 토큰 수명의 경우 항상 "TokenLifetimePolicy"를 사용합니다. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[선택 사항] |정책에 대한 대체 ID를 설정합니다. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
지정된 정책을 삭제합니다.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |원하는 정책의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>애플리케이션 정책
애플리케이션 정책에 다음 cmdlet을 사용할 수 있습니다.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
지정된 정책을 애플리케이션에 연결합니다.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |응용 프로그램의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |정책의 **ObjectId**입니다. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
애플리케이션에 할당된 정책을 가져옵니다.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |응용 프로그램의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
애플리케이션에서 정책을 제거합니다.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |응용 프로그램의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |정책의 **ObjectId**입니다. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>서비스 사용자 정책
서비스 주체 정책에 다음 cmdlet을 사용할 수 있습니다.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
지정된 정책을 서비스 주체에 연결합니다.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |응용 프로그램의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |정책의 **ObjectId**입니다. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
지정된 서비스 주체에 연결된 모든 정책을 가져옵니다.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |응용 프로그램의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
지정된 서비스 주체에서 정책을 제거합니다.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| 매개 변수 | Description | 예 |
| --- | --- | --- |
| <code>&#8209;Id</code> |응용 프로그램의 **ObjectId (ID)** 입니다. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |정책의 **ObjectId**입니다. | `-PolicyId <ObjectId of Policy>` |
