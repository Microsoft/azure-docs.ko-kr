---
title: Azure Active Directory의 단일 페이지 애플리케이션
description: SPA(단일 페이지 애플리케이션)를 소개하고 이 앱 유형에 대한 프로토콜 흐름, 등록 및 토큰 만료 기본 사항을 설명합니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 362de46b6b8cd70f9e4daa86637e3b4eb28ad95c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164176"
---
# <a name="single-page-applications"></a>단일 페이지 애플리케이션

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

SPA(단일 페이지 애플리케이션)는 일반적으로 브라우저에서 실행되는 JavaScript 프레젠테이션 계층(프런트 엔드), 서버에서 실행되고 애플리케이션의 비즈니스 논리를 구현하는 웹 API 백 엔드로 구성됩니다. 암시적 권한 부여에 대한 자세한 내용을 확인하고 자신의 애플리케이션 시나리오에 적절한지 판단하려면 [Azure Active Directory에서 OAuth2 암시적 권한 부여 흐름 이해](v1-oauth2-implicit-grant-flow.md)를 참조하세요.

이 시나리오에서는 사용자가 로그인하면 JavaScript 프런트 엔드에서 [JavaScript용 Active Directory 인증 라이브러리(ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) 및 암시적 권한 부여를 사용하여 Azure AD로부터 ID 토큰(id_token)을 가져옵니다. 토큰이 캐시되고, 클라이언트가 웹 API 백 엔드에 대해 호출할 때 이 토큰을 요청에 전달자 토큰으로 첨부합니다. 그러면 OWIN 미들웨어를 사용하여 보안됩니다.

## <a name="diagram"></a>다이어그램

![단일 페이지 애플리케이션 다이어그램](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>프로토콜 흐름

1. 사용자가 웹 애플리케이션으로 이동합니다.
1. 애플리케이션이 JavaScript 프런트 엔드(프레젠테이션 레이어)를 브라우저에 반환합니다.
1. 예를 들어 사용자가 로그인 링크를 클릭하여 로그인을 시작합니다. 브라우저가 Azure AD 권한 부여 엔드포인트에 GET을 전송하여 ID 토큰을 요청합니다. 이 요청에는 쿼리 매개 변수에 애플리케이션 ID 및 회신 URL이 포함됩니다.
1. Azure AD는 Azure Portal에서 구성한 등록된 회신 URL과 비교하여 회신 URL의 유효성을 검사합니다.
1. 사용자가 로그인 페이지에서 로그인합니다.
1. 인증에 성공하는 경우 Azure AD는 ID 토큰을 만들고 이 토큰을 애플리케이션의 회신 URL에 URL 조각(#)으로 반환합니다. 프로덕션 애플리케이션의 경우 이 회신 URL은 HTTPS여야 합니다. 반환된 토큰에는 애플리케이션이 토큰의 유효성을 검사하는 데 필요한, 사용자 및 Azure AD에 대한 클레임이 포함됩니다.
1. 브라우저에서 실행되는 JavaScript 클라이언트 코드가 애플리케이션 웹 API 백 엔드를 보안 호출하는 데 사용할 토큰을 응답에서 추출합니다.
1. 브라우저가 권한 부여 헤더의 ID 토큰을 사용하여 애플리케이션의 웹 API 백 엔드를 호출합니다. Azure AD 인증 서비스는 리소스가 클라이언트 ID와 동일한 경우 전달자 토큰으로 사용할 수 있는 ID 토큰을 발급합니다(이 경우 웹 API가 앱의 자체 백 엔드이므로 그러함).

## <a name="code-samples"></a>코드 샘플

[단일 페이지 애플리케이션 시나리오에 대한 코드 샘플](sample-v1-code.md#single-page-applications)을 참조하세요. 새로운 샘플이 자주 추가되므로 자주 확인해 보세요.

## <a name="app-registration"></a>앱 등록

* 단일 테넌트 - 조직 전용 애플리케이션을 빌드하는 경우 Azure Portal을 사용하여 해당 애플리케이션을 회사 디렉터리에 등록해야 합니다.
* 다중 테넌트 - 조직 외부 사용자가 사용할 수 있는 애플리케이션을 빌드하는 경우 애플리케이션을 회사 디렉터리에 등록해야 하며 동시에 애플리케이션을 사용할 각 조직의 디렉터리에도 등록해야 합니다. 해당 디렉터리에서 애플리케이션을 사용할 수 있게 만들려면 고객이 애플리케이션에 동의할 수 있게 하는 등록 프로세스를 포함하면 됩니다. 사용자가 애플리케이션에 등록할 때 애플리케이션에 필요한 권한을 보여 주는 대화 상자가 나타난 다음 동의하는 옵션이 나타납니다. 필요한 권한에 따라, 다른 조직의 관리자가 동의해야 할 수도 있습니다. 사용자 또는 관리자가 동의하면 애플리케이션이 이들의 디렉터리에 등록됩니다.

애플리케이션을 등록한 후에는 OAuth 2.0 암시적 허용 프로토콜을 사용하도록 애플리케이션을 구성해야 합니다. 기본적으로 이 프로토콜은 애플리케이션에 대해 사용하지 않도록 설정되어 있습니다. 애플리케이션에 OAuth2 암시적 허용 프로토콜을 사용하도록 설정하려면 Azure Portal에서 해당 애플리케이션 매니페스트를 편집하고 “oauth2AllowImplicitFlow” 값을 true로 설정합니다. 자세한 내용은 [애플리케이션 매니페스트](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)를 참조하세요.

## <a name="token-expiration"></a>토큰 만료

ADAL.js를 사용하면 다음과 같은 이점이 있습니다.

* 만료된 토큰 새로 고침
* 웹 API 리소스를 호출하기 위한 액세스 토큰 요청

인증이 성공하면 Azure AD에서 사용자의 브라우저에 쿠키를 작성하여 세션을 설정합니다. 세션은 사용자와 Azure AD 사이에 존재합니다(사용자와 웹 애플리케이션 사이가 아님). 토큰이 만료되면 ADAL.js가 이 세션을 사용하여 자동으로 다른 토큰을 가져옵니다. ADAL.js는 숨겨진 iFrame을 사용하여 OAuth 암시적 허용 프로토콜을 통해 요청을 보내고 받습니다. 또한 다른 웹 API 리소스가 CORS(원본 간 리소스 공유)를 지원하고 사용자의 디렉터리에 등록되고 사용자가 로그인 중에 필요한 동의를 제공한 경우에는, ADAL.js에서 동일한 이 메커니즘을 사용하여 애플리케이션이 호출하는 이러한 리소스에 대한 액세스 토큰을 자동으로 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 다른 [애플리케이션 유형 및 시나리오](app-types.md)에 대해 자세히 알아보기
* Azure AD [인증 기본 사항](v1-authentication-scenarios.md)에 대해 자세히 알아보기
