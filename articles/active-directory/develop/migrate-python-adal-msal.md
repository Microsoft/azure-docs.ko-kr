---
title: Python ADAL에서 MSAL 마이그레이션 가이드 | Microsoft
description: ADAL (Azure Active Directory Authentication Library) Python 앱을 Python 용 MSAL (Microsoft 인증 라이브러리)로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696561"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Python에 대 한 ADAL에서 MSAL 마이그레이션 가이드

이 문서에서는 MSAL (Microsoft Authentication Library)을 사용 하도록 ADAL (Azure Active Directory Authentication Library)을 사용 하는 앱을 마이그레이션하기 위해 변경 해야 하는 사항을 강조 합니다.

## <a name="difference-highlights"></a>차이점 강조

ADAL은 Azure Active Directory (Azure AD) v 1.0 엔드포인트에서 작동 합니다. MSAL (Microsoft 인증 라이브러리)은 이전에 Azure Active Directory v2.0 엔드포인트 이라고 하는 Microsoft id 플랫폼에서 작동 합니다. Microsoft id 플랫폼은 다음과 같은 점에서 Azure AD v 1.0과 다릅니다.

지원:
  - 회사 및 학교 계정(Azure AD로 프로비저닝한 계정)
  - 개인 계정(예: Outlook.com 또는 Hotmail.com)
  - Azure AD B2C 제품을 통해 자신의 이메일 또는 소셜 ID(예: LinkedIn, Facebook, Google)를 사용하는 고객

- 는와 호환 되는 표준입니다.
  - OAuth v2.0
  - OIDC (Openid connect Connect)

자세한 내용은 v2.0 [(Microsoft identity platform) 엔드포인트의 다른 항목](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) 을 참조 하세요.

### <a name="scopes-not-resources"></a>리소스가 아닌 범위

ADAL Python은 리소스에 대 한 토큰을 가져오지만 MSAL Python은 범위에 대 한 토큰을 획득 합니다. MSAL Python의 API 화면에 더 이상 리소스 매개 변수가 없습니다. 필요한 사용 권한 및 요청 된 리소스를 선언 하는 문자열의 목록으로 범위를 제공 해야 합니다. 범위에 대 한 몇 가지 예를 보려면 [Microsoft Graph의 범위](https://docs.microsoft.com/graph/permissions-reference)를 참조 하세요.

### <a name="error-handling"></a>오류 처리

Python 용 ADAL (Azure Active Directory 인증 라이브러리)은 예외 `AdalError`를 사용 하 여 문제가 있음을 표시 합니다. Python 용 MSAL은 일반적으로 오류 코드를 대신 사용 합니다. 자세한 내용은 [Msal For Python error 처리](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python)를 참조 하세요.

### <a name="api-changes"></a>API 변경

다음 표에서는 Python 용 ADAL의 API와 Python 용 MSAL에서 사용할 API를 나열 합니다.

| Python API 용 ADAL  | Python API 용 MSAL |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication 또는 ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) 및 [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) 및 [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | [Msal 확장](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) 에서 사용할 수 있는 지 속성이 있는 캐시 |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>MSAL Python에 대 한 기존 새로 고침 토큰 마이그레이션

MSAL (Microsoft 인증 라이브러리)은 새로 고침 토큰의 개념을 추상화 합니다. MSAL Python은 새로 고침 토큰을 저장, 조회 또는 업데이트할 필요가 없도록 기본적으로 메모리 내 토큰 캐시를 제공 합니다. 사용자 개입 없이 새로 고침 토큰을 일반적으로 업데이트할 수 있으므로 사용자에 게 더 낮은 로그인 프롬프트도 표시 됩니다. 토큰 캐시에 대 한 자세한 내용은 [Python 용 MSAL의 사용자 지정 토큰 캐시 serialization](msal-python-token-cache-serialization.md)을 참조 하세요.

다음 코드는 다른 OAuth2 라이브러리에서 관리 하는 새로 고침 토큰을 마이그레이션하는 데 도움이 됩니다 (ADAL Python을 포함 하지만이에 국한 되지 않음). 이러한 새로 고침 토큰을 마이그레이션하는 한 가지 이유는 앱을 Python 용 MSAL로 마이그레이션할 때 기존 사용자가 다시 로그인 해야 하는 것을 방지 하기 위한 것입니다.

새로 고침 토큰을 마이그레이션하는 방법은 Python 용 MSAL을 사용 하 여 이전 새로 고침 토큰을 사용 하 여 새 액세스 토큰을 획득 하는 것입니다. 새 새로 고침 토큰이 반환 되 면 Python 용 MSAL이 캐시에 저장 합니다. 다음은이 작업을 수행 하는 방법의 예입니다.

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [v1.0 및 v2.0 비교](active-directory-v2-compare.md)를 참조하세요.
