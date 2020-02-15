---
title: Azure AD의 OAuth2 암시적 허용 흐름 이해 | Microsoft Docs
description: Azure Active Directory의 OAuth2 암시적 허용 흐름 구현 및 이 구현이 애플리케이션에 적합한지 여부에 대해 자세히 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 90e42ff9-43b0-4b4f-a222-51df847b2a8d
ms.service: active-directory
ms.subservice: azuread-dev
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ec9d58c517cca354b00af25e1f0204460f2851bb
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164098"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Azure AD(Active Directory)의 OAuth2 암시적 허용 흐름 이해

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

OAuth2 암시적 허용은 OAuth2 사양에서 보안 문제가 가장 많은 허용으로 악명이 높습니다. 그런데도 이는 ADAL JS가 구현한 방법이며 SPA 애플리케이션을 작성할 때 권장하는 방법입니다. 무엇을 제공하나요? 모두 절충의 문제이며 이미 판명되었듯이 암시적 허용은 브라우저에서 JavaScript를 통해 웹 API를 사용하는 애플리케이션에 대해 추진할 수 있는 최선의 방법입니다.

## <a name="what-is-the-oauth2-implicit-grant"></a>OAuth2 암시적 허용이란 무엇인가요?

전형적인 [OAuth2 인증 코드 권한 부여](https://tools.ietf.org/html/rfc6749#section-1.3.1) 는 별개의 두 엔드포인트를 사용하는 권한 부여입니다. 권한 부여 엔드포인트는 권한 부여 코드가 되는 사용자 상호 작용 단계에서 사용됩니다. 토큰 엔드포인트는 액세스 토큰, 종종 새로 고침 토큰에 대한 코드를 교환하기 위해 클라이언트에서 사용됩니다. 웹 애플리케이션은 권한 부여 서버가 클라이언트를 인증할 수 있도록 자신의 고유 애플리케이션 자격 증명을 토큰 엔드포인트에 제공해야 합니다.

[OAuth2 암시적 허용](https://tools.ietf.org/html/rfc6749#section-1.3.2)은 다른 권한 부여의 변형입니다. 클라이언트가 토큰 엔드포인트에 접촉하거나 인증하지 않고 권한 부여 엔드포인트에서 직접 액세스 토큰(및 [OpenId Connect](https://openid.net/specs/openid-connect-core-1_0.html)를 사용하는 경우 id_token)을 가져올 수 있도록 합니다. 이 변형은 웹 브라우저에서 실행되는 JavaScript 기반 애플리케이션용으로 설계되었습니다. 원래의 OAuth2 사양에서는 토큰이 URI 조각으로 반환됩니다. 이 방법으로 토큰 비트를 클라이언트의 JavaScript 코드에 사용할 수 있도록 하지만 서버를 향한 리디렉션에 이 비트가 포함되지 않도록 보증합니다. OAuth2 암시적 grant에서 권한 부여 끝점은 이전에 제공 된 리디렉션 URI를 사용 하 여 클라이언트에 직접 액세스 토큰을 발급 합니다. 또한 JavaScript 애플리케이션이 토큰 엔드포인트를 문의하는 데 필수인 경우 필요한 크로스 원본 호출에 대한 요구 사항을 제거하는 이점이 있습니다.

OAuth2 암시적 허용의 중요한 특징은 그러한 흐름이 새로 고침 토큰을 클라이언트에 반환하지 않는다는 사실입니다. 다음 섹션에서는 이 작업이 필요하지 않으며 실제로 보안 문제가 되는 방법을 보여 줍니다.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>OAuth2 암시적 허용에 적합한 시나리오

OAuth2 사양에서는 사용자 에이전트 애플리케이션, 즉 JavaScript 애플리케이션을 브라우저 내에서 실행할 수 있도록 하기 위해 암시적 허용이 고안되었다고 선언하고 있습니다. 이러한 애플리케이션의 결정적인 특징은 JavaScript 코드를 사용하여 서버 리소스(대개 웹 API)에 액세스하고, 이에 따라 애플리케이션 사용자 환경을 업데이트한다는 것입니다. 받은 편지함에서 메시지를 선택할 때 메시지 시각화 패널에만 새 선택 내용이 표시되고 나머지 페이지는 수정되지 않은 채로 남아 있는 Gmail 또는 Outlook Web Access와 같은 애플리케이션을 생각해 보세요. 이 특징은 모든 사용자 상호 작용으로 인해 전체 페이지를 다시 게시하고 새 서버 응답의 전체 페이지를 렌더링하는 기존 리디렉션 기반 웹앱과는 대조적입니다.

JavaScript 기반 접근 방법을 극한까지 확장하는 애플리케이션을 단일 페이지 애플리케이션 또는 SPA라고 합니다. 이러한 애플리케이션은 초기 HTML 페이지 및 연결된 JavaScript만 제공하고, 모든 후속 상호 작용은 JavaScript를 통해 수행된 Web API 호출에 의해 구동됩니다. 그러나 애플리케이션이 대부분 포스트백을 기반으로 하지만 가끔 JS 호출을 수행하는 하이브리드 방법은 흔치 않습니다. 암시적 흐름 사용에 관한 설명은 그러한 경우에도 관련됩니다.

일반적으로 리디렉션 기반 애플리케이션은 쿠키를 통해 해당 요청을 보호하지만 해당 접근 방식은 JavaScript 애플리케이션에도 작동하지 않습니다. 쿠키는 생성된 도메인에 대해서만 작동하지만 JavaScript 호출은 다른 도메인으로 편향될 수 있습니다. 사실 이러한 경우는 아주 많습니다. 모두 애플리케이션을 제공한 도메인의 외부에 상주하는 Graph API, Office API, Azure API를 호출하는 애플리케이션을 생각해 보세요. JavaScript 애플리케이션이 증가하는 추세는 타사 웹 API에 전적으로 의존하여 비즈니스 기능을 구현하는 백 엔드가 전혀 없다는 것입니다.

현재 웹 API에 대한 호출을 보호하는 기본 방법은 모든 호출이 OAuth2 액세스 토큰과 함께 동반되는 OAuth2 전달자 토큰 접근 방법을 사용하는 것입니다. 웹 API는 들어오는 액세스 토큰을 검사하고 필요한 범위에서 찾으면 요청된 작업에 대한 액세스를 부여합니다. 암시적 흐름은 JavaScript 애플리케이션이 웹 API에 대한 액세스 토큰을 가져오기에 편리한 메커니즘을 제공하여 쿠키에 관한 다양한 장점을 제공합니다.

* 토큰을 원래 호출과 교차할 필요 없이 신뢰성 있게 가져올 수 있음 - 토큰을 반환하는 리디렉션 URI의 필수 등록을 통해 토큰이 이동되지 않도록 보증
* JavaScript 애플리케이션이 대상으로 하는 웹 API 수만큼 액세스 토큰을 필요한 대로 가져올 수 있음 - 도메인에 대한 제한 없음
* 세션 또는 로컬 스토리지와 같은 HTML5 기능이 토큰 캐싱 및 수명 관리를 완전히 제어할 수 있으면서도 쿠키 관리는 앱에서 신경쓸 필요가 없음
* 액세스 토큰이 교차 사이트 요청 위조(CSRF) 공격에 취약하지 않음

암시적 허용 흐름은 대부분 보안상의 이유로 새로 고침 토큰을 발급하지 않습니다. 새로 고침 토큰은 액세스 토큰으로 범위가 한정 되지 않고, 훨씬 더 많은 기능을 부여 하므로 누수가 발생 하는 경우 훨씬 더 손상이 좁지. 암시적 흐름에서 토큰은 URL로 전달 되므로 가로채기의 위험은 인증 코드 부여 보다 높습니다.

그러나 JavaScript 애플리케이션에서는 사용자에게 자격 증명을 반복적으로 요구하지 않고 액세스 토큰을 갱신하는 또 다른 메커니즘을 마음대로 사용할 수 있습니다. 애플리케이션은 숨겨진 iframe을 사용하여 Azure AD의 권한 부여 엔드포인트에 대해 새 토큰 요청을 수행할 수 있습니다. 즉, 브라우저가 Azure AD 도메인에 대해 활성 세션을 여전히 가지고 있으면(의미: 세션 쿠키를 가짐) 사용자 상호 작용이 필요 없이 인증 요청이 성공적으로 이루어질 수 있습니다.

이 모델은 JavaScript 애플리케이션에 액세스 토큰을 독립적으로 갱신하고 새 API에 대한 새 액세스 토큰을 획득하는 기능을 부여합니다(이전에 사용자가 동의한 경우에만). 이렇게 하면 새로 고침 토큰과 같은 높은 값의 아티팩트를 획득, 유지 관리 및 보호하는 부담을 방지합니다. 자동 갱신을 가능하게 하는 아티팩트인 Azure AD 세션 쿠키는 애플리케이션 외부에서 관리됩니다. 이 방법의 다른 장점은 사용자가 브라우저 탭 중 하나에서 실행하는 Azure AD에 로그인하는 애플리케이션을 사용하여 Azure AD에서 로그아웃할 수 있는 것입니다. 이로 인해 Azure AD 세션 쿠키가 삭제되고 JavaScript 애플리케이션은 로그아웃된 사용자에 대한 토큰을 갱신하는 기능을 자동으로 손실합니다.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>암시적 허용이 내 앱에 적합할까요?

암시적 권한 부여는 다른 권한 부여 보다 더 많은 위험을 표시 하며 주의 해야 하는 영역은 잘 문서화 되어 있습니다 (예: [암시적 흐름에서 리소스 소유자를 가장 하는 액세스 토큰 오용][OAuth2-Spec-Implicit-Misuse] 및 [OAuth 2.0 위협 모델 및 보안 고려 사항][OAuth2-Threat-Model-And-Security-Implications]). 그러나 더 높은 위험 프로필은 주로 원격 리소스에서 브라우저에 제공한 활성 코드를 실행하는 애플리케이션을 사용하도록 설정하는 것을 의미한다는 사실 때문입니다. SPA 아키텍처를 계획하는 경우 백 엔드 구성 요소가 없거나 JavaScript를 통해 웹 API를 호출하려고 하므로 토큰 획득을 위해 암시적 흐름을 사용하는 것이 좋습니다.

애플리케이션이 네이티브 클라이언트인 경우 암시적 흐름은 그다지 적합하지 않습니다. 네이티브 클라이언트 상황에서 Azure AD 세션 쿠키가 없으면 오래 지속되는 세션을 유지 관리하는 수단에서 애플리케이션을 사용하지 않게 됩니다. 즉 애플리케이션은 새 리소스에 대한 액세스 토큰을 가져올 때 사용자에게 반복해서 메시지를 표시합니다.

백 엔드가 포함된 웹 애플리케이션을 개발하고 해당 백 엔드 코드에서 API를 사용하는 경우에도 암시적 흐름은 그다지 적합하지 않습니다. 다른 권한 부여는 훨씬 더 많은 전원을 제공합니다. 예를 들어 OAuth2 클라이언트 자격 증명 부여는 사용자 위임이 아니라 애플리케이션 자체에 할당된 사용 권한을 반영하는 토큰을 가져올 수 있는 기능을 제공합니다. 즉 클라이언트는 사용자가 세션에 있지 않은 경우 등에도 리소스에 대한 프로그래밍 방식의 액세스를 유지하는 기능을 갖습니다. 뿐만 아니라 이러한 부여는 더 높은 보안성 보증을 제공합니다. 예를 들어 액세스 토큰은 사용자 브라우저를 통해 전송되지 않고 브라우저 기록 등에 저장되는 위험을 감수하지 않습니다. 또한 클라이언트 애플리케이션은 토큰을 요청할 때 강력한 인증을 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 응용 프로그램 통합 프로세스에 대 한 추가 깊이는 [응용 프로그램을 AZURE AD와 통합 하는 방법을][ACOM-How-To-Integrate] 참조 하세요.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
