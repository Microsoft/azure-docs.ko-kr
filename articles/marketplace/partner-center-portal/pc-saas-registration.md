---
title: SaaS 응용 프로그램 등록-Azure Marketplace
description: Azure Portal를 사용 하 여 SaaS 응용 프로그램을 등록 하 고 Azure Active Directory 보안 토큰을 받는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: dddcdd52709c634f0dcf67cb982804f5556e88b9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548338"
---
# <a name="register-a-saas-application"></a>SaaS 애플리케이션 등록

이 문서에서는 Microsoft [Azure Portal](https://portal.azure.com/) 를 사용 하 여 SaaS 응용 프로그램을 등록 하는 방법 및 게시자의 액세스 토큰 (Azure Active Directory 액세스 토큰)을 가져오는 방법을 설명 합니다. 게시자는 SaaS 처리 Api를 호출 하 여 SaaS 응용 프로그램을 인증 하는 데이 토큰을 사용 합니다.  처리 Api는 OAuth 2.0 클라이언트 자격 증명을 사용 하 여 서비스 간 액세스 토큰 요청을 수행 하는 Azure Active Directory (v 1.0) 끝점에 흐름을 부여 합니다.

Azure Marketplace는 SaaS 서비스에서 최종 사용자에 대해 사용 하는 인증 방법에 대 한 제약 조건을 적용 하지 않습니다. 아래 흐름은 Azure Marketplace에서 SaaS 서비스를 인증 하는 경우에만 필요 합니다.

Azure AD (Active Directory)에 대 한 자세한 내용은 [인증](../../active-directory/develop/authentication-scenarios.md)이란?을 참조 하세요.

## <a name="register-an-azure-ad-secured-app"></a>Azure AD 보안 앱 등록

Azure AD의 기능을 사용하려는 모든 애플리케이션이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 응용 프로그램에 대 한 몇 가지 세부 정보를 Azure AD에 제공 합니다. Azure Portal을 사용하여 새 애플리케이션을 등록하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 계정에서 둘 이상의 액세스를 제공 하는 경우 오른쪽 위 모서리에서 자신의 계정을 클릭 하 고 포털 세션을 원하는 Azure AD 테 넌 트로 설정 합니다.
3. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록** 및 **새 애플리케이션 등록**을 차례로 클릭합니다.

    ![SaaS AD 앱 등록](./media/saas-offer-app-registration-v1.png)

4. 만들기 페이지에서 애플리케이션의 등록\' 정보를 입력합니다.
    -   **이름**: 의미 있는 애플리케이션 이름을 입력합니다.
    -   **응용 프로그램 유형**:  
        
        [클라이언트 응용 프로그램](../../active-directory/develop/active-directory-dev-glossary.md#client-application)에 대 한 **웹 앱/** a p i를 선택 하 고 보안 서버에 설치 된 리소스/a p i [응용 프로그램](../../active-directory/develop/active-directory-dev-glossary.md#resource-server)을 선택 합니다. 이 설정은 OAuth 기밀 [웹 클라이언트](../../active-directory/develop/active-directory-dev-glossary.md#web-client)) 및 공용 [사용자 에이전트 기반 클라이언트](../../active-directory/develop/active-directory-dev-glossary.md#user-agent-based-client)에 사용 됩니다.
        동일한 애플리케이션이 클라이언트와 리소스/API를 모두 노출할 수도 있습니다.

        웹 응용 프로그램의 특정 예제는 [AZURE AD 개발자 가이드](../../active-directory/develop/index.yml)의 시작 섹션에서 사용할 수 있는 빠른 [시작](../../active-directory/develop/quickstart-create-new-tenant.md) 안내를 확인 하세요.

5. 완료 되 면 **등록**을 클릭 합니다.  Azure AD는 새 응용 프로그램에 고유한 *응용 프로그램 ID* 를 할당 합니다. API에만 액세스 하는 하나의 앱 및 단일 테 넌 트를 등록 하는 것이 좋습니다.

6. 클라이언트 암호를 만들려면 **인증서 & 암호 페이지로** 이동 하 고 **+ 새 클라이언트 암호**를 클릭 합니다.  코드에서 사용 하려면 비밀 값을 복사 해야 합니다.

**AZURE AD 앱 id** 는 게시자 id와 연결 되어 있으므로 모든 제품에서 동일한 *앱 id* 를 사용 해야 합니다.

>[!Note]
>파트너 센터에서 게시자의 계정이 서로 다른 경우 두 개의 다른 Azure AD 앱 Id를 사용 해야 합니다.  파트너 센터의 각 파트너 계정은이 계정을 통해 게시 되는 모든 SaaS 제품에 대해 고유한 Azure AD 앱 ID를 사용 해야 합니다.

## <a name="how-to-get-the-publishers-authorization-token"></a>게시자의 인증 토큰을 가져오는 방법

응용 프로그램을 등록 한 후에는 Azure AD V1 끝점을 사용 하 여 게시자의 권한 부여 토큰 (Azure AD 액세스 토큰)을 프로그래밍 방식으로 요청할 수 있습니다. 게시자는 다양 한 SaaS 처리 Api를 호출할 때이 토큰을 사용 해야 합니다. 이 토큰은 1 시간 동안만 유효 합니다. 

이러한 토큰에 대한 자세한 내용은 [Azure Active Directory 액세스 토큰](../../active-directory/develop/access-tokens.md)을 참조하세요.  아래에서 V1 끝점 토큰을 사용 합니다.

### <a name="get-the-token-with-an-http-post"></a>HTTP POST를 사용 하 여 토큰 가져오기

#### <a name="http-method"></a>HTTP 메서드

게시<br>

##### <a name="request-url"></a>*요청 URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI 매개 변수*

|  매개 변수 이름    |  필수         |  Description |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  등록 된 AAD 응용 프로그램의 테 넌 트 ID입니다. |

##### <a name="request-header"></a>*요청 헤더*

|  헤더 이름       |  필수         |  Description |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  요청과 연결된 콘텐츠 형식입니다. 기본값은 `application/x-www-form-urlencoded`입니다. |

##### <a name="request-body"></a>*요청 본문*

|  속성 이름     |  필수         |  Description |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  True      |  권한 부여 유형입니다. `"client_credentials"`을 사용합니다. |
|  `client_id`       |  True      |  Azure AD 앱과 연결된 클라이언트/앱 식별자입니다. |
|  `client_secret`   |  True      |  Azure AD 앱과 연결 된 암호입니다. |
|  `resource`        |  True      |  토큰이 요청된 대상 리소스입니다. `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`이 경우 Marketplace SAAS API는 항상 대상 리소스 이므로를 사용 합니다. |

##### <a name="response"></a>*Response*

|  Name     |  Type         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 정상   |  TokenResponse    |  요청이 성공했습니다. |

##### <a name="tokenresponse"></a>*TokenResponse*

샘플 응답:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`응답의 필드 값은 `<access_token>` 모든 SaaS 행 및 마켓플레이스 계량 api를 호출할 때 권한 부여 매개 변수로 전달할입니다.

## <a name="next-steps"></a>다음 단계

이제 Azure AD 보안 앱에서 [SaaS 앱 API 버전 2](./pc-saas-fulfillment-api-v2.md)를 사용할 수 있습니다.
