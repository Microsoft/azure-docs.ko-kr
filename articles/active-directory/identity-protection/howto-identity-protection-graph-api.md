---
title: Azure Active Directory Identity Protection에 대 한 Microsoft Graph API
description: Azure Active Directory에서 Microsoft Graph 위험 검색 및 관련 정보를 쿼리 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d88f841f76b367e83f0ae6b81e604e1b7f3e4b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950122"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory ID 보호 및 Microsoft Graph 시작

Microsoft Graph는 Microsoft의 통합된 API 엔드포인트이며 [Azure Active Directory ID 보호](./overview-identity-protection.md) API의 시작점입니다. 위험한 사용자 및 로그인에 대 한 정보를 노출 하는 세 가지 Api가 있습니다. 첫 번째 API 인 **riskDetection**를 사용 하면 사용자 및 로그인 연결 된 위험 검색의 목록 및 검색에 대 한 관련 정보를 Microsoft Graph 쿼리할 수 있습니다. 두 번째 API인 **riskyUsers**를 사용하면 위험으로 검색된 사용자 ID 보호에 대한 정보에 관하여 Microsoft Graph를 쿼리할 수 있습니다. 세 번째 API인 **signIn**을 사용하면 위험 상태, 세부 정보 및 수준과 관련된 특정 속성을 사용하여 Azure AD 로그인의 정보에 대한 Microsoft Graph를 쿼리할 수 있습니다. 

이 문서에서는 Microsoft Graph에 연결 및 이러한 API 쿼리를 사용하여 시작합니다. 자세한 소개, 전체 설명서 및 Graph Explorer에 대한 액세스는 [Microsoft Graph 사이트](https://graph.microsoft.io/) 또는 이러한 API에 대한 특정 참조 설명서를 참조하세요.

* [riskDetection API](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [riskyUsers API](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [signIn API](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Microsoft Graph에 연결

Microsoft Graph를 통해 ID 보호 데이터에 액세스하려면 네 가지 단계가 있습니다.

- [도메인 이름 검색](#retrieve-your-domain-name)
- [새 앱 등록 만들기](#create-a-new-app-registration)
- [API 권한 구성](#configure-api-permissions)
- [유효한 자격 증명 구성](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>도메인 이름 검색 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.  
1. **Azure Active Directory**  >  **사용자 지정 도메인 이름**으로 이동 합니다. 
1. `.onmicrosoft.com`이 정보는 이후 단계에서 필요 합니다.

### <a name="create-a-new-app-registration"></a>새 앱 등록 만들기

1. Azure Portal에서 **Azure Active Directory**  >  **앱 등록**으로 이동 합니다.
1. **새 등록**을 선택합니다.
1. **만들기** 페이지에서 다음 단계를 수행 합니다.
   1. **이름** 텍스트 상자에 응용 프로그램의 이름을 입력 합니다 (예: Azure AD 위험 검색 API).
   1. **지원 되는 계정 유형**에서 api를 사용 하는 계정 유형을 선택 합니다.
   1. **등록**을 선택합니다.
1. **응용 프로그램 ID**를 복사 합니다.

### <a name="configure-api-permissions"></a>API 권한 구성

1. 만든 **응용 프로그램** 에서 **API 권한**을 선택 합니다.
1. 구성 된 **사용 권한** 페이지의 위쪽에 있는 도구 모음에서 **사용 권한 추가**를 클릭 합니다.
1. **API 액세스 추가** 페이지에서 **API 선택**을 클릭합니다.
1. **API 선택** 페이지에서 **Microsoft Graph**를 선택한 다음, **선택**을 클릭합니다.
1. **API 권한 요청** 페이지에서 다음을 수행 합니다. 
   1. **애플리케이션 권한**을 선택합니다.
   1. 및 옆의 확인란을 `IdentityRiskEvent.Read.All` 선택 `IdentityRiskyUser.Read.All` 합니다.
   1. **권한 추가**를 선택합니다.
1. **도메인에 대 한 관리자 동의 부여를 선택 합니다** . 

### <a name="configure-a-valid-credential"></a>유효한 자격 증명 구성

1. 만든 **응용 프로그램** 에서 **인증서 & 암호**를 선택 합니다.
1. **클라이언트 암호**에서 **새 클라이언트 암호**를 선택 합니다.
   1. 클라이언트 암호에 **설명을** 지정 하 고 조직의 정책에 따라 만료 기간을 설정 합니다.
   1. **추가**를 선택합니다.

   > [!NOTE]
   > 이 키를 분실하면 이 섹션으로 돌아와서 새 키를 만들어야 합니다. 이 키의 비밀을 유지합니다. 키를 가진 사람은 누구나 데이터에 액세스할 수 있습니다.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Microsoft Graph에 인증 하 고 Id 위험 검색 API를 쿼리 합니다.

이 시점에서 다음 항목이 만들어 집니다.

- 테넌트 도메인의 이름
- 응용 프로그램 (클라이언트) ID 
- 클라이언트 암호 또는 인증서 

인증하려면 본문에서 다음 매개 변수를 ㅏ용하여 `https://login.microsoft.com` 에 게시 요청을 전송합니다.

- grant_type: “**client_credentials**”
- resource: `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

성공 하면이 요청은 인증 토큰을 반환 합니다.  
API를 호출하려면 다음 매개 변수를 사용하여 헤더를 만듭니다.

```
`Authorization`="<token_type> <access_token>"
```

인증할 경우 반환된 토큰에서 토큰 유형 및 액세스 토큰을 찾을 수 있습니다.

다음 API URL에 대한 요청으로 이 헤더를 보냅니다. `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

성공 하는 경우 응답은 id 위험 검색의 컬렉션 이며 OData JSON 형식으로 연결 된 데이터의 컬렉션입니다 .이를 구문 분석 하 고 적절 하 게 처리할 수 있습니다.

### <a name="sample"></a>예제

다음은 PowerShell을 사용하여 API를 인증하고 호출하는 데 대한 샘플 코드입니다.  
클라이언트 ID, 비밀 키 및 테넌트 도메인만 추가하면 됩니다.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>API 쿼리

이러한 세 가지 API는 조직의 위험한 사용자 및 로그인에 대한 정보를 가져오는 다양한 기회를 제공합니다. 다음은 이러한 API 및 관련된 샘플 요청에 대한 몇 가지 일반적인 사용 사례입니다. 위의 샘플 코드 또는 [Graph 탐색기](https://developer.microsoft.com/graph/graph-explorer)를 사용하여 이러한 쿼리를 실행할 수 있습니다.

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>모든 오프 라인 위험 검색 가져오기 (riskDetection API)

Id 보호 로그인 위험 정책을 사용 하면 위험이 실시간으로 감지 될 때 조건을 적용할 수 있습니다. 그러나 오프 라인에서 검색 되는 검색은 어떻습니까? 오프 라인으로 발생 한 검색을 이해 하 고 로그인 위험 정책을 트리거하지 않은 경우 riskDetection API를 쿼리할 수 있습니다.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>위험한 로그인 정책에 의해 트리거되는 MFA 챌린지를 성공적으로 통과한 모든 사용자 가져오기(riskyUsers API)

Id 보호 위험 기반 정책이 조직에 미치는 영향을 이해 하려면 위험한 로그인 정책에 의해 트리거된 MFA 챌린지를 성공적으로 통과 한 모든 사용자를 쿼리할 수 있습니다. 이 정보는 위험에서 잘못 감지했을 수 있는 사용자 ID 보호 및 AI에서 위험하다고 판단하는 작업을 수행할 수 있는 합법적인 사용자를 이해하는 데 도움이 될 수 있습니다.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>다음 단계

축하합니다! Microsoft Graph에 대한 호출을 처음으로 만들었습니다.  
이제 id 위험 검색을 쿼리하고 데이터를 사용할 수 있습니다.

Microsoft Graph 및 Graph API를 사용하여 애플리케이션을 구축하는 방법에 대한 자세한 내용은 [설명서](/graph/overview) 및 [Microsoft Graph 사이트](https://developer.microsoft.com/graph)에서 확인합니다. 

관련 정보는 다음을 참조하세요.

- [Azure Active Directory ID 보호](./overview-identity-protection.md)
- [Azure Active Directory Identity Protection에서 검색 하는 위험 검색 유형](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Microsoft Graph 개요](https://developer.microsoft.com/graph/docs)
- [Azure AD ID 보호 서비스 루트](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)