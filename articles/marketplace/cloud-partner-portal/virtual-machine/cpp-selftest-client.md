---
title: 가상 머신의 유효성을 사전 검사하는 자체 테스트 클라이언트 - Azure Marketplace | Microsoft Docs
description: Azure Marketplace용 가상 머신 이미지의 사전 유효성 검사를 위한 자체 테스트 클라이언트를 만드는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: ae01b0fb088035240e670c16d4d457d8abda1bfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60847087"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Azure Virtual Machine 이미지의 유효성을 사전 검사하는 자체 테스트 클라이언트 만들기

이 문서는 자체 테스트 API를 사용하는 클라이언트 서비스를 만들기 위한 지침으로 사용합니다. VM(가상 머신)에서 최신 Azure Marketplace 게시 요구 사항을 충족하는지 확인하기 위해 자체 테스트 API를 사용하여 VM의 유효성을 사전 검사할 수 있습니다. 이 클라이언트 서비스를 사용하면 Microsoft 인증을 위해 제안을 제출하기 전에 VM을 테스트할 수 있습니다.

## <a name="development-and-testing-overview"></a>개발 및 테스트 개요

자체 테스트 프로세스의 일부로 Azure Marketplace에 연결하여 Azure 구독에서 실행되는 VM의 유효성을 검사하는 로컬 클라이언트를 만듭니다. VM은 Windows 또는 Linux 운영 체제를 실행할 수 있습니다.

로컬 클라이언트는 자체 테스트 API를 사용하여 인증하고, 연결 정보를 보내며, 테스트 결과를 받는 스크립트를 실행합니다.

자체 테스트 클라이언트를 만드는 대략적인 단계는 다음과 같습니다.

1. 애플리케이션에 대한 Azure AD(Active Directory) 테넌트를 선택합니다.
2. 클라이언트 앱을 등록합니다.
3. 클라이언트 Azure AD 앱에 대한 토큰을 만듭니다.
4. 토큰을 자체 테스트 API에 전달합니다.

클라이언트가 만들어지면 VM에 대해 테스트할 수 있습니다.

### <a name="self-test-client-authorization"></a>자체 테스트 클라이언트 권한 부여

다음 다이어그램에서는 클라이언트 자격 증명(공유 비밀 또는 인증서)을 사용하여 서비스 간 호출에 대한 권한 부여가 작동하는 방식을 보여 줍니다.

![클라이언트 권한 부여 프로세스](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>자체 테스트 클라이언트 API

자체 테스트 API에는 POST 메서드만 지원하는 단일 엔드포인트가 포함됩니다.  API의 구조는 다음과 같습니다.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

다음 표에서는 API 필드에 대해 설명합니다.


|      필드         |    설명    |
|  ---------------   |  ---------------  |
|  권한 부여     |  "Bearer xxxx-xxxx-xxxx-xxxxx" 문자열에는 PowerShell을 사용하여 만들 수 있는 Azure AD(Active Directory) 클라이언트 토큰이 포함됩니다.          |
|  DNSName           |  테스트할 VM의 DNS 이름    |
|  사용자              |  VM에 로그인하는 데 사용할 사용자 이름         |
|  암호          |  VM에 로그인하는 데 사용할 암호          |
|  OS                |  VM의 운영 체제(`Linux` 또는 `Windows`)          |
|  PortNo            |  VM에 연결하는 데 사용할 열린 포트 번호 포트 번호는 대개 `22`(Linux), `5986`(Windows)입니다.          |
|  |  |

## <a name="consuming-the-api"></a>API 사용

PowerShell 또는 cURL을 통해 자체 테스트 API를 사용할 수 있습니다.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>PowerShell을 사용하여 Linux OS에서 API 사용

PowerShell에서 API를 호출하려면 다음 단계를 수행합니다.

1. `Invoke-WebRequest` 명령을 사용하여 API를 호출합니다.
2. 다음 코드 예제와 화면 캡처와 같이 메서드는 Post이고 콘텐츠 형식은 JSON입니다.
3. JSON 형식으로 본문 매개 변수를 지정합니다.

다음 코드 예제에서는 API에 대한 PowerShell 호출을 보여 줍니다.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```
다음 화면 캡처에는 PowerShell에서 API를 호출하는 예가 나와 있습니다.

![Linux OS용 PowerShell을 사용하여 API 호출](./media/stclient-call-api-ps-linuxvm.png)

앞의 예제를 사용하여 JSON을 검색하고 이를 구문 분석하여 다음 세부 정보를 얻습니다.

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

`$res.Content`를 보여 주는 다음 화면 캡처에서는 테스트 결과의 세부 정보를 JSON 형식으로 제공합니다.

![Linux에 대한 PowerShell 호출에 따른 JSON 결과](./media/stclient-pslinux-rescontent-json.png)

다음 화면 캡처에는 [Code Beautify](https://codebeautify.org/jsonviewer) 또는 [JSON Viewer](https://jsonformatter.org/json-viewer)와 같은 온라인 JSON 뷰어에 표시된 JSON 테스트 결과의 예가 나와 있습니다.

![Linux VM에 대한 PowerShell 호출에 따른 JSON 결과](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>PowerShell을 사용하여 Windows OS에서 API 사용

PowerShell에서 API를 호출하려면 다음 단계를 수행합니다.

1. `Invoke-WebRequest` 명령을 사용하여 API를 호출합니다.
2. 다음 코드 예제와 화면 캡처와 같이 메서드는 Post이고 콘텐츠 형식은 JSON입니다.
3. Body 매개 변수를 JSON 형식으로 만듭니다.

다음 코드 예제에서는 API에 대한 PowerShell 호출을 보여 줍니다.

```powershell
$accesstoken = “Get token for your Client AAD App”
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

다음 화면 캡처에는 PowerShell에서 API를 호출하는 예가 나와 있습니다.

![Windows VM용 PowerShell을 사용하여 API 호출](./media/stclient-call-api-ps-windowsvm.png)

앞의 예제를 사용하여 JSON을 검색하고 이를 구문 분석하여 다음 세부 정보를 얻습니다.

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

`$res.Content`를 보여 주는 다음 화면 캡처에서는 테스트 결과의 세부 정보를 JSON 형식으로 제공합니다.

![Windows에 대한 PowerShell 호출에 따른 JSON 결과](./media/stclient-pswindows-rescontent-json.png)

다음 화면 캡처에는 온라인 JSON 뷰어에 표시된 테스트 결과가 나와 있습니다.
있습니다(예: [Code Beautify](https://codebeautify.org/jsonviewer), [JSON 뷰어](https://jsonformatter.org/json-viewer)).

![Windows VM에 대한 PowerShell 호출에 따른 JSON 결과](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>cURL을 사용하여 Linux OS에서 API 사용

cURL을 사용하여 API를 호출하려면 다음 단계를 수행합니다.

1. curl 명령을 사용하여 API를 호출합니다.
2. 다음 코드 조작과 같이 메서드는 Post이고 콘텐츠 형식은 JSON입니다.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

다음 화면에는 curl을 사용하여 API를 호출하는 예가 나와 있습니다.

![curl 명령을 사용하여 API 호출](./media/stclient-consume-api-curl.png)

다음 화면 캡쳐에서는 curl 호출의 JSON 결과를 보여 줍니다.

![curl 호출에 따른 JSON 결과](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>앱에 대한 Azure AD 테넌트 선택

다음 단계에 따라 애플리케이션을 만들려는 Azure AD 테넌트를 선택합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 위쪽의 메뉴 모음에서 계정을 선택하고, [디렉터리] 목록에서 애플리케이션을 등록하려는 Active Directory 테넌트를 선택합니다. 또는 **디렉터리 + 구독** 아이콘을 선택하여 글로벌 구독 필터를 확인합니다. 다음 화면 캡처에는 이 필터의 예가 나와 있습니다.

   ![구독 필터 선택](./media/stclient-subscription-filter.png)

3. 왼쪽의 탐색 모음에서 **모든 서비스**, **Azure Active Directory**를 차례로 선택합니다.

   다음 단계에서는 테넌트 이름(또는 디렉터리 이름)이나 테넌트 ID(또는 디렉터리 ID)가 필요할 수 있습니다.

   **테넌트 정보를 얻으려면,**

   **Azure Active Directory 개요**에서 "속성"을 검색한 다음, **속성**을 선택합니다. 다음 화면 캡처를 예로 사용합니다.

   - **이름** - 테넌트 이름 또는 디렉터리 이름
   - **디렉터리 ID** - 테넌트 ID 또는 디렉터리 ID입니다. 또는 스크롤 막대를 사용하여 속성을 찾습니다.

   ![Azure Active Directory 속성 페이지](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>클라이언트 앱 등록

다음 단계에 따라 클라이언트 앱을 등록합니다.

1. 왼쪽의 탐색 모음에서 **모든 서비스**, **앱 등록**을 차례로 선택합니다.
2. **앱 등록** 아래에서 **+ 새 애플리케이션 등록**을 선택합니다.
3. **만들기** 아래에서 다음 필드에 필요한 정보를 제공합니다.

   - **이름** - 앱에 대한 이름을 입력합니다. 예를 들어 "SelfTestClient"가 있습니다.
   - **애플리케이션 유형** – **웹앱/API**를 선택합니다.
   - **로그온 URL** – 형식 "https:\//isvapp.azurewebsites.net/selftest-vm"

4. **만들기**를 선택합니다.
5. **앱 등록** 또는 **등록된 앱** 아래에서 **애플리케이션 ID**를 복사합니다.

   ![애플리케이션 ID 가져오기](./media/stclient-app-id.png)

6. 등록된 앱 도구 모음에서 **설정**을 선택합니다.
7. **필요한 권한**을 선택하여 애플리케이션에 대한 권한을 구성합니다.
8. **필요한 권한** 아래에서 **+ 추가**를 선택합니다.
9. **API 액세스 추가** 아래에서 **API 선택**을 선택합니다.
10. **API 선택** 아래에서 "Windows Azure 클래식 배포 모델"을 입력하여 API를 검색합니다.
11. 검색 결과에서 **Windows Azure 클래식 배포 모델**을 선택한 다음, **선택**을 클릭합니다.

    ![앱에 대한 다중 테넌트 구성](./media/stclient-select-api.png)

12. **API 액세스 추가** 아래에서 **권한 선택**을 선택합니다.
13. **"Windows Azure Service Management API"** 를 선택합니다.

    ![앱에 대한 API 액세스 사용](./media/stclient-enable-api-access.png)

14. **선택**을 클릭합니다.
15. **완료**를 선택합니다.
16. **설정** 아래에서 **속성**을 선택합니다.
17. **속성** 아래에서 **다중 테넌트**까지 아래로 스크롤합니다. **예**를 선택합니다.

    ![앱에 대한 다중 테넌트 구성](./media/stclient-yes-multitenant.png)

18. **저장**을 선택합니다.
19. **설정** 아래에서 **키**를 선택합니다.
20. 키 **설명** 텍스트 상자를 선택하여 비밀 키를 만듭니다. 다음 필드를 구성합니다.

    - 키 이름을 입력합니다. 예를 들어 "selftestclient"가 있습니다.
    - **만료** 드롭다운 목록에서 "1년 후"를 선택합니다.
    - **저장**을 선택하여 키를 생성합니다.
    - **값** 아래에서 키를 복사합니다.

      >[!Important]
      >**키** 양식이 종료되면 키 값을 볼 수 없습니다.

    ![키 값 양식](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>클라이언트 앱에 대한 토큰 만들기

다음 프로그램 중 하나를 사용하여 OAuth REST API를 사용하는 토큰을 만들고 가져올 수 있습니다.

- postman
- Linux의 cURL
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Postman을 사용하여 토큰 만들기 및 가져오기

 권한 있는 애플리케이션에 대한 토큰을 Auth0에 요청하려면 다음 형식의 페이로드가 있는 [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) 엔드포인트에 대한 POST 작업을 수행합니다.

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

요청 본문에 다음 매개 변수를 전달합니다.

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

요청 헤더에 다음 매개 변수를 전달합니다.

```
Content-Type: application/x-www-form-urlencoded
```

다음 화면 캡처에는 Postman을 사용하여 토큰을 가져오는 예가 나와 있습니다.

![Postman을 사용하여 토큰 가져오기](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Linux에서 cURL을 사용하여 토큰 만들기 및 가져오기

권한 있는 애플리케이션에 대한 토큰을 Auth0에 요청하려면 다음 형식의 페이로드가 있는 [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) 엔드포인트에 대한 POST 작업을 수행합니다.

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

다음 화면 캡처에는 curl 명령을 사용하여 토큰을 가져오는 예가 나와 있습니다.

![curl 명령을 사용하여 토큰 가져오기](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>C#을 사용하여 토큰 만들기 및 가져오기

나는 Auth0에 승인 된 응용 프로그램에 대 한 토큰을 요청 하려면 https POST 작업을 수행 합니다.\/형식에서 페이로드를 사용 하 여 /soamtenant.auth0.com/oauth/token 끝점:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>PowerShell을 토큰 만들기 및 가져오기

나는 Auth0에 승인 된 응용 프로그램에 대 한 토큰을 요청 하려면 https POST 작업을 수행 합니다.\/형식에서 페이로드를 사용 하 여 /soamtenant.auth0.com/oauth/token 끝점:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>API에 클라이언트 앱 토큰 전달

인증 헤더에 다음 코드를 사용하여 토큰을 자체 테스트 API에 전달합니다.

```powershell
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>자체 테스트 클라이언트 테스트

클라이언트를 테스트하려면 다음 단계를 수행합니다.

1. 테스트하려는 VM을 배포합니다.
2. 권한 부여를 위해 클라이언트 앱 토큰을 사용하여 자체 테스트 API를 호출합니다.
3. JSON 형식의 테스트 결과를 가져옵니다.

### <a name="test-result-examples"></a>테스트 결과 예제

다음 코드 조각에서는 테스트 결과를 JSON 형식으로 보여 줍니다.

**Windows VM에 대한 테스트 결과:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Linux VM에 대한 테스트 결과:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>다음 단계

Azure Virtual Machine 테스트가 정상적으로 완료되면 [제안을 게시](./cpp-publish-offer.md)할 수 있습니다.
