---
title: 동적으로 차단 되는 암호-Azure Active Directory
description: Azure AD 동적으로 금지된 암호를 사용하여 환경에서 취약한 암호를 금지할 수 있습니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848615"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>조직에서 잘못된 암호 제거

업계 리더는 여러 위치에서 동일한 암호를 사용 하 여 복잡 하 게 하 고 "Password123" 처럼 간단 하 게 만들지 않도록 합니다. 조직에서 사용자가 모범 사례 지침을 수행 하도록 보장 하는 방법 사용자가 취약 한 암호를 사용 하지 않도록 하려면 어떻게 해야 하나요? 아니면 약한 암호에도 변화가 있나요?

암호를 더 강력 하 게 유지 하는 초기 단계는 사용자에 게 지침을 제공 하는 것입니다. 이 항목에 대한 Microsoft의 현재 지침은 다음 링크에서 찾을 수 있습니다.

[Microsoft 암호 지침](https://www.microsoft.com/research/publication/password-guidance)

유용한 지침이 중요 하지만 많은 사용자가 취약 한 암호를 선택 하는 것을 알고 있는 경우에도 마찬가지입니다. Azure AD 암호 보호는 알려진 weak 암호와 해당 변형을 검색 하 고 차단 하 고 필요에 따라 조직과 관련 된 추가 약한 약관을 차단 하 여 조직을 보호 합니다.

현재 보안 노력에 대한 자세한 내용은 [Microsoft 보안 인텔리전스 보고서](https://www.microsoft.com/security/operations/security-intelligence-report)를 참조하세요.

## <a name="global-banned-password-list"></a>전역 금지 암호 목록

Azure AD ID 보호 팀은 일반적으로 사용 되는 취약 하거나 손상 된 암호를 찾는 Azure AD 보안 원격 분석 데이터를 지속적으로 분석 하 고 약한 암호의 기반으로 자주 사용 되는 취약 한 기본 용어를 지속적으로 분석 합니다. 이러한 weak 용어는 발견 되 면 전역 금지 된 암호 목록에 추가 됩니다. 전역 금지 된 암호 목록의 콘텐츠는 외부 데이터 원본을 기반으로 하지 않습니다. 전역 금지 된 암호 목록은 전적으로 Azure AD 보안 원격 분석 및 분석의 지속적인 결과를 기반으로 합니다.

Azure AD의 테넌트에 있는 모든 사용자에 대해 새 암호를 변경 하거나 다시 설정할 때마다 암호의 강도를 확인할 때 현재 버전의 전역 차단 된 암호 목록이 키 입력으로 사용 됩니다. 이 유효성 검사를 수행 하면 모든 Azure AD 고객에 대해 훨씬 더 강력한 암호가 생성 됩니다.

> [!NOTE]
> 사이버은 또한 공격에 비슷한 전략을 사용 합니다. 따라서 Microsoft는이 목록의 콘텐츠를 공개적으로 게시 하지 않습니다.

## <a name="custom-banned-password-list"></a>사용자 지정 금지 암호 목록

일부 조직에서는 Microsoft가 사용자 지정 금지 된 암호 목록을 호출 하는 사용자 지정을 전역 금지 된 암호 목록 위에 추가 하 여 보안을 더욱 향상 시킬 수 있습니다. 이 목록에 추가 된 용어는 주로 다음과 같은 조직적 특정 조건에 초점을 맞춘 것을 권장 합니다.

- 브랜드 이름
- 제품 이름
- 위치 (예: 회사 본부)
- 회사 특정 내부 용어
- 특별 한 회사 의미가 있는 약어입니다.

사용자 지정 금지 된 암호 목록에 추가 된 용어는 암호의 유효성을 검사할 때 사용이 금지 된 글로벌 암호 목록의 용어와 결합 됩니다.

> [!NOTE]
> 사용자 지정 금지 된 암호 목록은 최대 1000 용어를 포함 하는 것으로 제한 됩니다. 매우 큰 암호 목록을 차단 하도록 설계 되지 않았습니다. 사용자 지정 금지 된 암호 목록의 이점을 완전히 활용 하려면 사용자 지정 금지 목록에 새 용어를 추가 하기 전에 먼저 암호 평가 알고리즘 (암호 [평가 방법](concept-password-ban-bad.md#how-are-passwords-evaluated)참조)을 검토 하 고 이해 하는 것이 좋습니다. 알고리즘이 작동 하는 방식을 이해 하면 엔터프라이즈에서 많은 수의 weak 암호와 해당 변형을 효율적으로 검색 하 고 차단할 수 있습니다.

예: 런던을 기반으로 하 고 "Widget" 이라는 제품을 만드는 "Contoso" 라는 고객이 있다고 가정 합니다. 이러한 고객의 경우 다음과 같이 이러한 용어의 특정 변형을 차단 하려고 하는 것은 불필요 하 고 안전 하지 않습니다.

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

대신 키 기반 용어를 차단 하는 것이 훨씬 더 효율적이 고 안전 합니다.

- "Contoso"
- 도시가
- 위젯

그런 다음 암호 유효성 검사 알고리즘은 위의 weak variant 및 조합을 자동으로 차단 합니다.

사용자 지정 금지 암호 목록 및 온-프레미스 Active Directory 통합을 사용하는 기능은 Azure Portal을 통해 관리됩니다.

![인증 방법 아래에서 사용자 지정 금지 된 암호 목록을 수정 합니다.](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>암호 스프레이 공격 및 타사 손상 된 암호 목록

Azure AD 암호 보호의 장점 중 하나는 암호 스프레이 공격 으로부터 보호 하는 데 도움을 주는 것입니다. 대부분의 암호 스프레이 공격은 계정 잠금 또는 기타 수단을 통해 검색 가능성이 크게 향상 되므로 지정 된 개별 계정을 몇 번 이상 공격 하려고 시도 하지 않습니다. 따라서 대부분의 암호 스프레이 공격은 기업의 각 계정에 대해 알려진 가장 약한 암호를 적은 수의 전송만을 사용 합니다. 이 방법을 통해 공격자는 잠재적 검색 임계값을 방지 하면서도 동시에 쉽게 손상 된 계정을 검색할 수 있습니다.

Azure AD 암호 보호는 Azure AD에서 볼 수 있는 실제 보안 원격 분석 데이터를 기반으로 하 여 암호 스프레이 공격에 사용 될 가능성이 높은 모든 알려진 약한 암호를 효율적으로 차단 하도록 설계 되었습니다.  Microsoft는 이전의 공개적으로 알려진 보안 위반에서 손상 된 수백만 개의 암호를 열거 하는 타사 웹 사이트를 인식 합니다. 일반적으로 타사 암호 유효성 검사 제품은 이러한 수백만 개의 암호에 대 한 무차별 암호 대입 비교를 기반으로 합니다. Microsoft는 이러한 기술이 암호 스프레이 공격자가 사용 하는 일반적인 전략에 따라 전체 암호 강도를 개선 하는 최선의 방법이 아닙니다.

> [!NOTE]
> Microsoft 전역 금지 된 암호 목록은 손상 된 암호 목록을 비롯 한 타사 데이터 원본을 기반으로 하지 않습니다.

일부 타사 대량 목록과 비교할 때 Microsoft 전역 차단 목록에는 문제가 없지만, 실제 암호 스프레이 공격에 대 한 실제 보안 원격 분석에서 발생 하는 것은 물론 Microsoft에서 제공 하는 보안 효과가 암호 유효성 검사 알고리즘은 스마트 유사 일치 기법을 사용 합니다. 최종 결과는 엔터프라이즈에서 가장 일반적인 약한 암호를 효율적으로 검색 하 고 차단 하는 것입니다. 사용자 지정 금지 된 암호 목록에 조직별 용어를 추가 하도록 선택 하는 고객은 동일한 알고리즘을 사용 하 여 혜택을 받을 수 있습니다.

암호 기반 보안 문제에 대 한 추가 정보는 [Pa $ $word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)에서 검토할 수 있습니다.

## <a name="on-premises-hybrid-scenarios"></a>온-프레미스 하이브리드 시나리오

클라우드 전용 계정을 보호하면 도움이 되지만, 많은 조직에서는 온-프레미스 Windows Server Active Directory를 비롯한 하이브리드 시나리오를 유지하고 있습니다. Azure AD 암호 보호의 보안 이점은 온-프레미스 에이전트 설치를 통해 Windows Server Active Directory 환경으로 확장 될 수도 있습니다. 이제 Active Directory의 암호를 변경 하거나 다시 설정 하는 사용자와 관리자는 클라우드 전용 사용자와 동일한 암호 정책을 준수 해야 합니다.

## <a name="how-are-passwords-evaluated"></a>암호 평가 방법

사용자가 암호를 변경 하거나 다시 설정할 때마다 전역 및 사용자 지정 금지 된 암호 목록 (후자가 구성 된 경우)에서 결합 된 용어 목록에 대해 유효성을 검사 하 여 새 암호의 강도와 복잡도를 확인 합니다.

사용자 암호에 금지된 암호가 포함되어 있더라도 전체 암호의 보안 수준이 충분히 높으면 해당 암호를 사용할 수 있습니다. 암호를 새로 구성하면 다음 단계가 진행되어 암호의 전반적인 보안 수준을 평가함으로써 암호를 수락할지 아니면 거부할지가 결정됩니다.

### <a name="step-1-normalization"></a>1 단계: 정규화

먼저 새 암호에 대해 정규화 프로세스가 진행됩니다. 이 기법을 사용 하면 적은 수의 금지 된 암호를 잠재적으로 취약 한 암호 집합에 매핑할 수 있습니다.

정규화는 두 부분으로 진행됩니다.  먼저 대문자가 모두 소문자로 변경됩니다.  그런 후에 다음과 같이 일반 문자 대체가 수행됩니다.  

| 원래 문자  | 대체되는 문자 |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

예: "blank"라는 암호가 금지된 상태에서 사용자가 암호를 "Bl@nK"로 변경하려 한다고 가정해 보겠습니다. "Bl@nk"는 구체적으로 금지되어 있지는 않지만 정규화 프로세스에서는 이 암호를 금지된 암호인 "blank"로 변환합니다.

### <a name="step-2-check-if-password-is-considered-banned"></a>2 단계: 암호가 금지 된 것으로 간주 되는지 확인

#### <a name="fuzzy-matching-behavior"></a>유사 일치 동작

유사 일치는 정규화된 암호가 전역 또는 사용자 지정 금지된 암호 목록에 있는 암호를 포함하는지를 확인하는 데 사용됩니다. 일치 프로세스는 편집 거리 1 비교를 기준으로 합니다.  

예: "abcdef"라는 암호가 금지된 상태에서 사용자가 암호를 다음 중 하나로 변경하려 한다고 가정해 보겠습니다.

' abcdeg ' *(마지막 문자가 ' f '에서 ' g '로 변경 됨)* ' abcdefg ' *' (g '는 끝까지 추가* 됨) ' abcde...z ' *(후행 ' f '는 끝에서 삭제 됨)*

위의 각 암호는 금지된 암호인 "abcdef"와 구체적으로 일치하지는 않습니다. 그러나 각 예제는 금지 된 용어 ' abcdef ' 중 1의 편집 거리 내에 있으므로 모두 "abcdef"와 일치 하는 것으로 간주 됩니다.

#### <a name="substring-matching-on-specific-terms"></a>부분 문자열 일치(특정 용어)

부분 문자열 일치는 정규화된 암호에서 사용자 이름/성 및 테넌트 이름을 확인하는 데 사용됩니다. Active Directory 도메인 컨트롤러에서 암호 유효성을 검사할 때는 테넌트 이름 일치가 수행되지 않습니다.

예: 사용자에 게 암호를 "P0l123fb"로 다시 설정 하려는 사용자가 있다고 가정 합니다. Registry.pol. 정규화 후이 암호는 "pol123fb"이 됩니다. 부분 문자열 일치는 암호에 사용자의 이름 "Registry.pol"가 포함 되어 있음을 찾습니다. "P0l123fb"가 특별히 금지 된 암호 목록에 포함 되지 않은 경우에도 암호에 "Registry.pol" 라는 부분 문자열이 일치 합니다. 이 암호는 거부됩니다.

#### <a name="score-calculation"></a>점수 계산

다음 단계에서는 사용자의 정규화된 새 암호에서 금지된 암호의 모든 인스턴스를 확인합니다. 그렇다면

1. 사용자의 암호에서 확인되는 금지된 각 암호가 1점으로 계산됩니다.
2. 나머지 각 고유 문자도 1점으로 계산됩니다.
3. 암호는 허용 하기 위해 5 개 이상의 지점 이어야 합니다.

다음 두 예제에서는 Contoso가 Azure AD 암호 보호를 사용 중이며 사용자 지정 목록에 "contoso"가 포함되어 있다고 가정하겠습니다. 그리고 전역 목록에는 "blank"가 포함되어 있다고 가정합니다.

예제: 사용자가 암호 "C0ntos0Blank12"로 변경합니다.

정규화 후에 이 암호는 "contosoblank12"가 됩니다. 일치 프로세스에서는 이 암호에 금지된 암호 2개(contoso, blank)가 포함되어 있음이 확인됩니다. 따라서 이 암호의 점수는

[contoso] + [blank] + [1] + [2] = 4 개 점이 있습니다 .이 암호는 5 개의 점에 있으므로 거부 됩니다.

예제: 사용자가 암호 "ContoS0Bl@nkf9!"로 변경합니다.

정규화 후에 이 암호는 "contosoblankf9!"가 됩니다. 일치 프로세스에서는 이 암호에 금지된 암호 2개(contoso, blank)가 포함되어 있음이 확인됩니다. 따라서 이 암호의 점수는

[contoso] + [blank] + [f] + [9] + [!] = 5 개의 점이 있으므로이 암호는 5 개 이상 이므로 허용 됩니다.

   > [!IMPORTANT]
   > 금지된 암호 알고리즘과 전역 목록은 지속적인 보안 분석 및 연구를 토대로 Azure에서 언제든지 변경할 수 있으며 실제로 변경됩니다. 온-프레미스 DC 에이전트 서비스의 경우 업데이트 된 알고리즘은 DC 에이전트 소프트웨어를 다시 설치한 후에만 적용 됩니다.

## <a name="license-requirements"></a>라이선스 요구 사항

|   | 전역 금지 암호 목록을 사용하여 Azure AD 암호 보호 | 사용자 지정 금지 암호 목록을 사용하여 Azure AD 암호 보호|
| --- | --- | --- |
| 클라우드 전용 사용자 | Azure AD Free | Azure AD Premium P1 또는 P2 |
| 온-프레미스 Windows Server Active Directory에서 동기화된 사용자 | Azure AD Premium P1 또는 P2 | Azure AD Premium P1 또는 P2 |

> [!NOTE]
> 온-프레미스 Windows Server Active Directory Azure Active Directory와 동기화 되지 않은 사용자는 동기화 된 사용자에 대 한 기존 라이선스를 기반으로 하는 Azure AD 암호 보호의 이점을 누릴 수 있습니다.

비용을 비롯한 추가 라이선스 정보는 [Azure Active Directory 가격 책정 사이트](https://azure.microsoft.com/pricing/details/active-directory/)에서 확인할 수 있습니다.

## <a name="what-do-users-see"></a>사용자에게 표시되는 내용

사용자가 암호를 금지된 문자열로 재설정하려고 하면 다음과 같은 오류 메시지가 표시됩니다.

암호에 쉽게 추측할 수 있는 단어, 구 또는 패턴이 포함되어 있습니다. 다른 암호로 다시 시도하세요.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 금지 암호 목록 구성](howto-password-ban-bad.md)
- [온-프레미스에서 Azure AD 암호 보호 에이전트 사용](howto-password-ban-bad-on-premises-deploy.md)
