---
title: Openid connect Connect를 사용 하 여 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IdP (Openid connect Connect identity provider)를 사용 하 여 등록 및 로그인을 설정 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a7896e0385978c4221edc233c931bad720c60f8a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847603"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 OpenID Connect를 통한 등록 및 로그인 설정

[Openid connect Connect](openid-connect.md) 는 보안 사용자 로그인에 사용할 수 있는 OAuth 2.0 위에 구축 된 인증 프로토콜입니다. 이 프로토콜을 사용 하는 대부분의 id 공급자는 Azure AD B2C에서 지원 됩니다. 이 문서에서는 사용자 지정 OpenID Connect ID 공급 기업을 사용자 흐름에 추가하는 방법을 설명합니다.

## <a name="add-the-identity-provider"></a>ID 공급자 추가

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 클릭 하 고 테넌트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **Id 공급자**를 선택한 다음 **New openid connect Connect 공급자**를 선택 합니다.

## <a name="configure-the-identity-provider"></a>ID 공급자 구성

모든 Openid connect Connect id 공급자는 로그인을 수행 하는 데 필요한 대부분의 정보를 포함 하는 메타 데이터 문서를 설명 합니다. 여기에는 사용할 URL, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함됩니다. OpenID Connect 메타데이터 문서는 항상 `.well-known\openid-configuration`으로 끝나는 엔드포인트에 있습니다. 추가하려는 OpenID Connect ID 공급자의 경우 해당 메타데이터 URL을 입력합니다.

## <a name="client-id-and-secret"></a>클라이언트 ID 및 비밀

사용자가 로그인할 수 있도록 허용하기 위해 ID 공급자는 개발자가 해당 서비스에 애플리케이션을 등록하도록 요청합니다. 이 애플리케이션에는 ID(**클라이언트 ID**라고 함) 및 **클라이언트 비밀**이 있습니다. ID 공급자에서 이러한 값을 복사하여 해당하는 필드에 입력합니다.

> [!NOTE]
> 클라이언트 비밀은 선택 사항입니다. 그러나 암호를 사용 하 여 토큰에 대한 코드를 교환 하는 [권한 부여 코드 흐름](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)을 사용 하려는 경우에는 클라이언트 암호를 입력 해야 합니다.

## <a name="scope"></a>범위

범위는 사용자 지정 ID 공급자로부터 수집하려는 정보 및 권한을 정의합니다. ID 공급자로부터 ID 토큰을 받을 수 있도록 OpenID Connect 요청에 `openid` 범위 값이 포함되어 있어야 합니다. 사용자는 ID 토큰이 없으면 사용자 지정 ID 공급자를 사용하여 Azure AD B2C에 로그인할 수 없습니다. 다른 범위를 공백으로 구분하여 추가할 수 있습니다. 사용할 수 있는 다른 범위를 확인하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

## <a name="response-type"></a>응답 형식

응답 유형은 초기 호출 시 사용자 지정 ID 공급자의 `authorization_endpoint`로 반송되는 정보의 종류를 설명합니다. 다음과 같은 응답 유형을 사용할 수 있습니다.

* `code`: [권한 부여 코드 흐름](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)에 따라 코드가 Azure AD B2C로 다시 반환됩니다. 그러면 Azure AD B2C는 `token_endpoint` 호출을 진행하여 토큰용 코드를 교환합니다.
* `id_token`: ID 토큰이 사용자 지정 ID 공급자에서 Azure AD B2C로 다시 반환됩니다.

## <a name="response-mode"></a>응답 모드

응답 모드는 사용자 지정 ID 공급자에서 Azure AD B2C로 데이터를 다시 보내는 데 사용해야 하는 메서드를 정의합니다. 다음 응답 모드를 사용할 수 있습니다.

* `form_post`: 최상의 보안을 위해 이 응답 모드를 사용하는 것이 좋습니다. 응답이 `application/x-www-form-urlencoded` 형식을 사용하여 본문에서 인코딩되는 코드 또는 토큰으로 HTTP `POST` 메서드를 통해 전송됩니다.
* `query`: 코드 또는 토큰이 쿼리 매개 변수로 반환됩니다.

## <a name="domain-hint"></a>도메인 힌트

도메인 힌트를 사용하면 사용자가 사용할 수 있는 ID 공급자의 목록 중에 선택하는 대신 지정된 ID 공급자의 로그인 페이지로 바로 건너뛸 수 있습니다. 이러한 종류의 동작을 허용하려면 도메인 힌트에 대한 값을 입력합니다. 사용자 지정 ID 공급자로 점프하려면 로그인을 위해 Azure AD B2C를 호출할 때 매개 변수 `domain_hint=<domain hint value>`를 사용자 요청의 끝에 추가합니다.

## <a name="claims-mapping"></a>클레임 매핑

사용자 지정 ID가 ID 토큰을 Azure AD B2C로 다시 보낸 후, Azure AD B2C는 수신된 토큰의 클레임을 Azure AD B2C가 인식 및 사용하는 클레임으로 매핑할 수 있어야 합니다. 아래 각 매핑에 대해 ID 공급자 토큰에서 다시 반환되는 클레임을 파악하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

* **사용자 ID**: 로그인 한 사용자의 *고유 식별자* 를 제공 하는 클레임을 입력 합니다.
* **표시 이름**: 사용자의 *표시 이름* 또는 *전체 이름을* 제공 하는 클레임을 입력 합니다.
* **지정 된 이름**: 사용자의 *이름을* 제공 하는 클레임을 입력 합니다.
* **성**: 사용자의 *성을* 제공 하는 클레임을 입력 합니다.
* **전자 메일**: 사용자의 *전자 메일 주소* 를 제공 하는 클레임을 입력 합니다.
