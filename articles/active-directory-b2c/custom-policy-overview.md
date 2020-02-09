---
title: Azure Active Directory B2C 사용자 지정 정책 | Microsoft Docs
description: Azure Active Directory B2C 사용자 지정 정책에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1f3bbcba562d126d503920cb7b6787e2ca0025c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847044"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 지정 정책

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자 지정 정책은 Azure Active Directory B2C (Azure AD B2C) 테넌트의 동작을 정의 하는 구성 파일입니다. 사용자 흐름은 가장 일반적인 ID 태스크에 대해 Azure AD B2C 포털에서 미리 정의됩니다. 사용자 지정 정책은 많은 다른 태스크를 완료하기 위해 ID 개발자가 완전히 편집할 수 있습니다.

## <a name="comparing-user-flows-and-custom-policies"></a>사용자 흐름 및 사용자 지정 정책 비교

| | 사용자 흐름 | 사용자 지정 정책 |
|-|-------------------|-----------------|
| 대상 사용자 | ID 전문 지식이 있거나 없는 모든 애플리케이션 개발자 | ID 전문가, 시스템 통합 서비스, 컨설턴트 및 사내 ID 팀. Openid connect Connect 흐름에 익숙해지면 id 공급자 및 클레임 기반 인증을 이해 하 고 있습니다. |
| 구성 방법 | 사용자에게 친숙한 UI(사용자 인터페이스)를 사용하는 Azure Portal | XML 파일 직접 편집 및 Azure Portal에 업로드 |
| UI 사용자 지정 | HTML, CSS 및 JavaScript를 비롯 한 전체 UI 사용자 지정<br><br>사용자 지정 문자열을 사용하는 다국어 지원 | 동일 |
| 특성 사용자 지정 | 표준 및 사용자 지정 특성 | 동일 |
| 토큰 및 세션 관리 | 사용자 지정 토큰 및 다중 세션 옵션 | 동일 |
| ID 공급자 | 미리 정의 된 로컬 또는 소셜 공급자와 대부분의 OIDC id 공급자 (예: Azure Active Directory 테넌트를 사용 하는 페더레이션) | 표준 기반 OIDC, OAUTH 및 SAML  REST Api와의 통합을 사용 하 여 인증을 수행할 수도 있습니다. |
| ID 작업 | 로컬 또는 많은 소셜 계정을 사용하여 등록 또는 로그인<br><br>셀프 서비스 암호 재설정<br><br>프로필 편집<br><br>Multi-Factor Authentication.<br><br>토큰 및 세션을 사용자 지정합니다.<br><br>액세스 토큰 흐름. | 사용자 지정 ID 공급자를 사용하는 사용자 흐름과 동일한 작업을 완료하거나 사용자 지정 범위를 사용합니다.<br><br>등록 시 다른 시스템에서 사용자를 프로비전합니다.<br><br>고유한 이메일 서비스 공급자를 사용하여 환영 이메일을 보냅니다.<br><br>Azure AD B2C 외부의 사용자 저장소를 사용합니다.<br><br>API를 사용하여 신뢰할 수 있는 시스템으로 사용자 제공 정보의 유효성을 검사합니다. |

## <a name="policy-files"></a>정책 파일

다음 세 가지 유형의 정책 파일이 사용됩니다.

- **기본 파일** - 대부분의 정의를 포함합니다. 문제 해결 및 정책을 장기적인 유지 관리에 도움이 되도록 이 파일을 최소한으로 변경하는 것이 좋습니다.
- **확장 파일** - 테넌트에 대한 고유한 구성 변경 내용을 보유합니다.
- **RP(신뢰 당사자) 파일** - 애플리케이션 또는 서비스(신뢰 당사자라고도 함)에 의해 직접 호출되는 단일 작업 중심 파일입니다. 각 고유 작업에는 고유한 RP이 필요하고 브랜딩 요구 사항에 따라 "전체 애플리케이션 x 전체 사용 사례 수"일 수 있습니다.

Azure AD B2C의 사용자 흐름은 위에 표시된 3개의 파일 패턴을 따르지만 개발자는 Azure Portal이 확장 파일에 대한 변경 내용을 백그라운드에서 수행하는 동안 개발자는 RP 파일만을 확인할 수 있습니다.

## <a name="custom-policy-core-concepts"></a>사용자 지정 정책 핵심 개념

Azure의 CIAM(고객 ID 및 액세스 관리) 서비스에는 다음이 포함됩니다.

- Microsoft Graph를 통해 액세스할 수 있고 로컬 계정 및 페더레이션된 계정 모두에 대한 사용자 데이터를 보유하는 사용자 디렉터리
- 사용자와 엔터티 간의 신뢰를 조정하고, ID 또는 액세스 관리 작업을 완료하기 위해 이들 간에 클레임을 전달하는 **ID 경험 프레임워크**에 대한 액세스
- ID 토큰, 새로 고침 토큰 및 액세스 토큰(및 해당하는 SAML 어설션)을 발급하고 유효성을 검사하여 리소스를 보호는 STS(보안 토큰 서비스)

Azure AD B2C는 순서대로 ID 공급자, 사용자, 고객, 기타 시스템 및 로컬 사용자 디렉터리와 상호 작용하여 ID 작업을 완료합니다. 예를 들어 사용자를 로그인하거나, 새 사용자를 등록하거나, 암호를 다시 설정합니다. ID 경험 프레임워크 및 정책(사용자 경험 또는 보안 프레임워크 정책이라고도 함)은 다자간 신뢰를 설정하고 행위자, 작업, 프로토콜 및 완료할 단계 시퀀스를 명시적으로 정의합니다.

Id 경험 프레임 워크는 Openid connect Connect, OAuth, SAML, 몇 가지 비표준와 같은 표준 프로토콜 형식의 엔터티 사이에 신뢰를 오케스트레이션 하는 완전히 구성 가능한 정책 중심의 클라우드 기반 Azure 플랫폼입니다 (예: REST). API 기반 시스템 간 클레임 교환. 이 프레임워크는 HTML 및 CSS를 지원하는 사용자에게 친숙한 화이트 레이블 환경을 만듭니다.

사용자 지정 정책은 계층 구조 체인에서 서로를 참조하는 하나 또는 여러 XML 형식 파일로 표시됩니다. XML 요소는 다른 요소 간에 클레임 스키마, 클레임 변환, 콘텐츠 정의, 클레임 공급자, 기술 프로필 및 사용자 경험 오케스트레이션 단계를 정의합니다. 사용자 지정 정책은 신뢰 당사자에서 호출할 때 ID 경험 프레임워크에서 실행하는 하나 이상의 XML 파일에 액세스할 수 있습니다. 사용자 지정 정책을 구성하는 개발자는 신뢰할 수 있는 관계를 세부적으로 신중하게 정의하여 메타데이터 엔드포인트, 정확한 클레임 정의 교환을 포함하고 ID 공급자 각각의 필요에 따라 암호, 키 및 인증서를 구성해야 합니다.

### <a name="inheritance-model"></a>상속 모델

애플리케이션에서 RP 정책 파일을 호출하면 Azure AD B2C의 ID 경험 프레임워크에서 기본 파일, 확장 파일 및 RP 정책 파일의 모든 요소를 차례로 추가하여 현재 적용 중인 정책을 효과적으로 조합합니다.  RP 파일에서 동일한 형식 및 이름이라는 요소는 확장에 있는 동일한 요소를 재정의하고 확장의 요소는 기본 요소를 재정의합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 정책 시작](custom-policy-get-started.md)
