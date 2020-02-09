---
title: 사용 보고 API 샘플 및 정의
titleSuffix: Azure AD B2C
description: Azure AD B2C 테넌트 사용자, 인증 및 다단계 인증에 대한 보고서를 얻는 방법에 대한 가이드 및 샘플입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b4486c1459024d60b53292f070dd565d20d10c52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847733"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>보고 API를 통해 Azure AD B2C에서 사용량 보고서에 액세스

Azure AD B2C(Azure Active Directory B2C)는 사용자 로그인 및 Azure Multi-Factor Authentication 기반 인증을 제공합니다. 인증은 ID 공급자를 통해 애플리케이션 제품군의 최종 사용자에게 제공됩니다. 테넌트에 등록된 사용자의 수, 등록하는 데 사용된 공급자 및 유형별 인증 수를 알고 있으면 다음과 같은 질문에 답할 수 있습니다.
* 각 유형의 ID 공급자(예: Microsoft 또는 LinkedIn 계정)에서 지난 10일 동안 등록한 사용자는 몇 명인가요?
* 지난 달에 Multi-Factor Authentication을 통해 성공적으로 완료된 인증은 몇 번인가요?
* 이번 달에 완료된 로그인 기반 인증은 몇 번인가요? 일일? 애플리케이션당?
* 내 Azure AD B2C 테넌트 활동에 대한 월별 예상 비용은 어떻게 추정해야 하나요?

이 문서에서는 사용자 수, 청구 가능한 로그인 기반 인증 수 및 다단계 인증 수에 기반한 청구 활동에 연결된 보고서에 중점을 둡니다.


## <a name="prerequisites"></a>필수 조건
시작하기 전에 [Azure AD Reporting API에 액세스하기 위한 필수 구성 요소](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)의 단계를 완료해야 합니다. 애플리케이션을 만들고, 그 암호를 가져오고, Azure AD B2C 테넌트의 보고서에 대한 액세스 권한을 부여합니다. *Bash 스크립트* 및 *Python 스크립트* 예제도 여기에 제공됩니다.

## <a name="powershell-script"></a>PowerShell 스크립트
이 스크립트에서는 `TimeStamp` 매개 변수 및 `ApplicationId` 필터를 사용하여 4개의 사용량 보고서를 만드는 방법을 보여 줍니다.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>사용량 보고서 정의
* **tenantUserCount** - 지난 30일 이내 ID 공급자 유형별 일일 테넌트 사용자 수입니다. (선택 사항: `TimeStamp` 필터는 지정된 날짜부터 현재 날짜까지의 사용자 수를 제공합니다.) 보고서에서 다음을 제공합니다.
  * **TotalUserCount**: 모든 사용자 개체의 수
  * **OtherUserCount**: Azure Active Directory 사용자(Azure AD B2C 사용자가 아님)의 수
  * **LocalUserCount**: Azure AD B2C 테넌트에 대한 로컬 자격 증명으로 만든 Azure AD B2C 사용자 계정의 수

* **AlternateIdUserCount** - 외부 ID 공급자(예: Facebook, Microsoft 계정 또는 다른 Azure Active Directory 테넌트, `OrgId`라고도 함)에 등록된 Azure AD B2C 사용자 수입니다.

* **b2cAuthenticationCountSummary** - 지난 30일 동안 일별 및 인증 흐름 유형별 청구 가능한 일일 인증 수의 요약입니다.

* **b2cAuthenticationCount** - 한 기간 내 인증 수입니다. 기본값은 지난 30일입니다.  (선택 사항: 시작 및 종료 `TimeStamp` 매개 변수는 특정 기간을 정의 합니다.) 출력에는 `StartTimeStamp` (이 테넌트의 가장 이른 작업 날짜)와 `EndTimeStamp` (최신 업데이트)이 포함 됩니다.

* **b2cMfaRequestCountSummary** - 일별 및 유형별(SMS 또는 음성) 일일 다단계 인증 수의 요약입니다.


## <a name="limitations"></a>제한 사항
사용자 수 데이터는 24-48시간마다 새로 고쳐집니다. 인증은 하루에도 여러 번 업데이트됩니다. `ApplicationId` 필터를 사용하는 경우 다음 조건 중 하나로 인해 보고서 응답이 비어 있을 수 있습니다.
  * 애플리케이션 ID가 테넌트에 존재하지 않습니다. 정확한지 확인하세요.
  * 애플리케이션 ID가 있지만 보고 기간에 존재하는 데이터가 없습니다. 날짜/시간 매개 변수를 검토하세요.


## <a name="next-steps"></a>다음 단계
### <a name="monthly-bill-estimates-for-azure-ad"></a>Azure AD에 대한 월별 예상 청구 비용 견적
[사용 가능한 최근 Azure AD B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)과 결합한 경우 일별, 주별 및 월별 Azure 소비를 예측할 수 있습니다.  견적은 전체 비용에 영향을 줄 수 있는 테넌트 동작의 변경 내용을 계획할 때 특히 유용합니다. [연결된 Azure 구독](billing.md)에서 실제 비용을 검토할 수 있습니다.

### <a name="options-for-other-output-formats"></a>다른 출력 형식의 옵션
다음 코드에서는 JSON, 이름 값 목록 및 XML로 출력을 보내는 예제를 보여 줍니다.
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
