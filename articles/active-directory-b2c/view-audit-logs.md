---
title: 감사 로그 액세스 및 검토
titleSuffix: Azure AD B2C
description: Azure AD B2C 감사 로그를 프로그래밍 방식으로 액세스 하는 방법 및 Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5695968973c7446220d8d77b84dfebb4a23ae8c7
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847759"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C 감사 로그 액세스

Azure Active Directory B2C (Azure AD B2C)는 B2C 리소스, 발급 된 토큰 및 관리자 액세스에 대한 활동 정보가 포함 된 감사 로그를 내보냅니다. 이 문서에서는 감사 로그에서 사용할 수 있는 정보의 간략 한 개요와 Azure AD B2C 테넌트에 대해이 데이터에 액세스 하는 방법에 대한 지침을 제공 합니다.

감사 로그 이벤트는 **7 일**동안 보존 됩니다. 보존 기간이 더 오래 필요한 경우, 아래 표시된 방법 중 하나를 사용하여 로그를 다운로드하고 저장하도록 플랜하세요.

> [!NOTE]
> Azure Portal의 **Azure Active Directory** 또는 **Azure AD B2C** 페이지의 **사용자** 섹션에는 개별 Azure AD B2C 응용 프로그램에 대한 사용자 로그인이 표시 되지 않습니다. 로그인 이벤트에는 사용자 작업이 표시 되지만 사용자가 로그인 한 B2C 응용 프로그램에는 다시 연관 될 수 없습니다. 이 문서에서 설명 하는 대로이에 대한 감사 로그를 사용 해야 합니다.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>감사 로그의 B2C 범주에서 사용 가능한 작업 개요

감사 로그의 **B2C** 범주에는 다음 유형의 작업이 포함됩니다.

|활동 유형 |Description  |
|---------|---------|
|권한 부여 |B2C 리소스에 액세스 하는 사용자의 권한 부여와 관련 된 활동 (예: B2C 정책 목록에 액세스 하는 관리자)         |
|디렉터리 |관리자가 Azure Portal를 사용 하 여 로그인 할 때 검색 되는 디렉터리 특성과 관련 된 작업입니다. |
|애플리케이션 | B2C 응용 프로그램에 대한 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업을 수행 합니다. |
|키 |B2C key 컨테이너에 저장 된 키에 대한 CRUD 작업 |
|리소스 |B2C 리소스에 대한 CRUD 작업 예를 들면 정책 및 id 공급자가 있습니다.
|인증 |사용자 자격 증명 및 토큰 발급의 유효성 검사|

사용자 개체 CRUD 활동의 경우 **핵심 디렉터리** 범주를 참조하세요.

## <a name="example-activity"></a>예제 활동

Azure Portal의이 이미지 예제에서는 사용자가 외부 id 공급자 (이 경우 Facebook)를 사용 하 여 로그인 할 때 캡처된 데이터를 보여 줍니다.

![Azure Portal에서 감사 로그 작업 세부 정보 페이지의 예](./media/view-audit-logs/audit-logs-example.png)

활동 세부 정보 패널에는 다음과 같은 관련 정보가 포함 되어 있습니다.

|섹션|필드|Description|
|-------|-----|-----------|
| 작업 | 이름 | 발생 한 작업입니다. 예를 들어 *응용 프로그램에*대한 Id_token를 실행 하 여 실제 사용자 로그인을 마칩니다. |
| 초기자(작업자) | ObjectId | 사용자가 로그인 하는 B2C 응용 프로그램의 **개체 ID** 입니다. 이 식별자는 Azure Portal에 표시 되지 않지만 Microsoft Graph API를 통해 액세스할 수 있습니다. |
| 초기자(작업자) | Spn | 사용자가 로그인 하는 B2C 응용 프로그램의 **응용 프로그램 ID** 입니다. |
| 대상 | ObjectId | 로그인 중인 사용자의 **개체 ID** 입니다. |
| 추가 세부 정보 | TenantId | Azure AD B2C 테넌트의 **테넌트 ID** 입니다. |
| 추가 세부 정보 | PolicyId | 사용자를 로그인 하는 데 사용 되는 사용자 흐름 (정책)의 **정책 ID** 입니다. |
| 추가 세부 정보 | ApplicationId | 사용자가 로그인 하는 B2C 응용 프로그램의 **응용 프로그램 ID** 입니다. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Azure Portal에서 감사 로그 보기

Azure Portal Azure AD B2C 테넌트의 감사 로그 이벤트에 대한 액세스를 제공 합니다.

1. [Azure 포털](https://portal.azure.com)
1. Azure AD B2C 테넌트가 포함 된 디렉터리로 전환 하 고 **Azure AD B2C**로 이동 합니다.
1. 왼쪽 메뉴의 **활동** 에서 **감사 로그**를 선택 합니다.

지난 7 일 동안 기록 된 활동 이벤트 목록이 표시 됩니다.

![Azure Portal의 두 작업 이벤트를 사용 하는 예제 필터](./media/view-audit-logs/audit-logs-example-filter.png)

다음을 비롯 한 몇 가지 필터링 옵션을 사용할 수 있습니다.

* **활동 리소스 종류** - [사용 가능한 활동 개요](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) 섹션의 표에 나와 있는 활동 유형에 따라 필터링 합니다.
* **날짜** -표시 된 작업의 날짜 범위를 필터링 합니다.

목록에서 행을 선택 하면 해당 이벤트에 대한 작업 세부 정보가 표시 됩니다.

쉼표로 구분 된 값 (CSV) 파일에서 작업 이벤트 목록을 다운로드 하려면 **다운로드**를 선택 합니다.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Azure AD reporting API를 사용 하 여 감사 로그 가져오기

감사 로그는 Azure Active Directory에 대한 다른 활동과 동일한 파이프라인에 게시되므로 [Azure Active Directory 보고 API](https://docs.microsoft.com/graph/api/directoryaudit-list)를 통해 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory REPORTING API 시작](../active-directory/reports-monitoring/concept-reporting-api.md)을 참조 하세요.

### <a name="enable-reporting-api-access"></a>보고 API 액세스 사용

Azure AD 보고 API에 대한 스크립트 또는 응용 프로그램 기반 액세스를 허용 하려면 다음 API 권한을 사용 하 여 Azure AD B2C 테넌트에 등록 된 Azure Active Directory 응용 프로그램이 필요 합니다.

* 감사 로그 > 응용 프로그램 사용 권한을 Microsoft Graph >

B2C 테넌트 내의 기존 Azure Active Directory 응용 프로그램 등록에 대해 이러한 권한을 사용 하도록 설정 하거나 감사 로그 자동화에 사용 하기 위해 특별히 새 응용 프로그램을 만들 수 있습니다.

다음 단계를 수행 하 여 응용 프로그램을 등록 하 고 필요한 Microsoft Graph API 권한을 부여한 다음 클라이언트 암호를 만듭니다.

### <a name="register-application-in-azure-active-directory"></a>Azure Active Directory에 응용 프로그램 등록

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="assign-api-access-permissions"></a>API 액세스 권한 할당

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. 등록 된 **앱** 개요 페이지에서 **설정**을 선택 합니다.
1. **API 액세스**에서 **필요한 권한**을 선택 합니다.
1. **추가**를 선택한 다음, **API를 선택**합니다.
1. **Microsoft Graph**를 선택 하 고를 **선택**합니다.
1. **응용 프로그램 사용 권한**아래에서 **모든 감사 로그 데이터 읽기**를 선택 합니다.
1. **선택** 단추를 선택한 다음 **완료**를 선택 합니다.
1. **사용 권한 부여**를 선택한 다음, **예**를 선택합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. **관리** 아래에서 **API 권한**을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가**를 선택합니다.
1. **Microsoft api** 탭을 선택 합니다.
1. **Microsoft Graph**를 선택합니다.
1. **애플리케이션 권한**을 선택합니다.
1. **감사 로그** 를 확장 한 다음 **감사 로그** 확인란을 선택 합니다.
1. **권한 추가**를 선택합니다. 안내에 따라 몇 분 정도 기다린 후 다음 단계를 진행하세요.
1. **(테넌트 이름)에 대한 관리자 동의 허용**을 선택합니다.
1. *전역 관리자* 역할이 할당 된 경우 현재 로그인 된 계정을 선택 하거나 *전역 관리자* 역할이 할당 된 Azure AD B2C 테넌트의 계정으로 로그인 합니다.
1. **수락**을 선택합니다.
1. **새로 고침**을 선택 하 고 "다음에 대해 권한 부여 ..."를 확인 합니다. *감사 로그* 사용 권한에 대한 **상태** 아래에 나타납니다. 권한이 전파되려면 몇 분 정도 걸릴 수 있습니다.

* * *

### <a name="create-client-secret"></a>클라이언트 암호 만들기

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

이제 응용 프로그램에 필요한 API 액세스, 응용 프로그램 ID 및 자동화 스크립트에서 사용할 수 있는 키가 있습니다. 스크립트를 사용 하 여 작업 이벤트를 가져오는 방법에 대한 예제는이 문서의 뒷부분에 있는 PowerShell 스크립트 섹션을 참조 하세요.

### <a name="access-the-api"></a>API 액세스

API를 통해 Azure AD B2C 감사 로그 이벤트를 다운로드 하려면 `B2C` 범주에 대한 로그를 필터링 합니다. 범주별로 필터링 하려면 Azure AD 보고 API 엔드포인트을 호출할 때 `filter` 쿼리 문자열 매개 변수를 사용 합니다.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell 스크립트

다음 PowerShell 스크립트는 Azure AD reporting API를 쿼리 하는 방법의 예를 보여 줍니다. API를 쿼리 한 후에는 로깅된 이벤트를 표준 출력으로 출력 하 고 JSON 출력을 파일에 기록 합니다.

[Azure Cloud Shell](overview.md)에서이 스크립트를 사용해 볼 수 있습니다. 응용 프로그램 ID, 클라이언트 암호 및 Azure AD B2C 테넌트의 이름으로 업데이트 해야 합니다.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

다음은이 문서의 앞부분에 표시 된 예제 작업 이벤트의 JSON 표현입니다.

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

다른 관리 작업 (예: [.net을 사용 하 여 사용자 관리](manage-user-accounts-graph-api.md))을 자동화할 수 있습니다.
