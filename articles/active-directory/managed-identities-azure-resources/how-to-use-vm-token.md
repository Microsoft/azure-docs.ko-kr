---
title: 가상 머신에서 관리 되는 id를 사용 하 여 액세스 토큰 가져오기-Azure AD
description: 가상 머신에서 Azure 리소스에 대한 관리 ID를 사용하여 액세스 토큰을 획득하기 위한 단계별 지침과 예제입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4683a77b9467775fbe368e2017416e0fbff9718c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266292"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하여 액세스 토큰을 획득하는 방법 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 토큰 획득을 위한 다양한 코드 및 스크립트 예제뿐만 아니라 토큰 만료 및 HTTP 오류를 처리하는 등 중요한 항목에 대한 지침을 제공합니다. 

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

이 문서에서 Azure PowerShell 예제를 사용하려는 경우 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps)을 설치해야 합니다.


> [!IMPORTANT]
> - 이 문서의 모든 샘플 코드/스크립트는 Azure 리소스에 대한 관리 ID를 사용하는 가상 머신에서 클라이언트가 실행되고 있다고 가정합니다. Azure Portal에서 가상 머신 "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 Azure 리소스에 대한 관리 ID 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 

> [!IMPORTANT]
> - Azure 리소스에 대한 관리 ID의 보안 경계는 이를 사용하고 있는 리소스입니다. 가상 머신에서 실행되는 모든 코드/스크립트는 사용할 수 있는 관리 ID에 대한 토큰을 요청하고 검색할 수 있습니다. 

## <a name="overview"></a>개요

클라이언트 애플리케이션은 지정된 리소스에 액세스하기 위해 Azure 리소스에 대한 관리 ID [앱 전용 액세스 토큰](../develop/developer-glossary.md#access-token)을 요청할 수 있습니다. 이 토큰은 [Azure 리소스 서비스 주체에 대한 관리 ID를 기준으로](overview.md#managed-identity-types) 합니다. 따라서 고유한 서비스 주체 하에서 액세스 토큰을 가져오기 위해 클라이언트를 등록하지 않아도 됩니다. 토큰은 [서비스 간 호출 요청 클라이언트 자격 증명](../develop/v2-oauth2-client-creds-grant-flow.md)에서 전달자 토큰으로 사용하기에 적합합니다.

| 링크 | 설명 |
| -------------- | -------------------- |
| [HTTP를 사용하여 토큰 가져오기](#get-a-token-using-http) | Azure 리소스에 대한 관리 ID 토큰 엔드포인트에 대한 프로토콜 세부 정보 |
| [.NET용 Microsoft.Azure.Services.AppAuthentication 라이브러리를 사용하여 토큰 가져오기](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | .NET 클라이언트에서 Microsoft.Azure.Services.AppAuthentication 라이브러리를 사용하는 예
| [C#을 사용하여 토큰 가져오기](#get-a-token-using-c) | C# 클라이언트에서 Azure 리소스에 대한 관리 ID REST 엔드포인트를 사용하는 예제 |
| [Java를 사용하여 토큰 가져오기](#get-a-token-using-java) | Java 클라이언트에서 Azure 리소스에 대한 관리 ID REST 엔드포인트를 사용하는 예제 |
| [Go를 사용하여 토큰 가져오기](#get-a-token-using-go) | Go 클라이언트에서 Azure 리소스에 대한 관리 ID REST 엔드포인트를 사용하는 예제 |
| [Azure PowerShell을 사용하여 토큰 가져오기](#get-a-token-using-azure-powershell) | PowerShell 클라이언트에서 Azure 리소스에 대한 관리 ID REST 엔드포인트를 사용하는 예제 |
| [CURL을 사용하여 토큰 가져오기](#get-a-token-using-curl) | Bash/CURL 클라이언트에서 Azure 리소스에 대한 관리 ID REST 엔드포인트를 사용하는 예제 |
| 토큰 캐싱 처리 | 만료된 액세스 토큰을 처리하는 지침 |
| [오류 처리](#error-handling) | Azure 리소스에 대한 관리 ID 토큰 엔드포인트에서 반환된 HTTP 오류를 처리하기 위한 지침 |
| [Azure 서비스의 리소스 ID](#resource-ids-for-azure-services) | 지원되는 Azure 서비스에 리소스 ID를 가져올 위치 |

## <a name="get-a-token-using-http"></a>HTTP를 사용하여 토큰 가져오기 

액세스 토큰을 획득할 기본 인터페이스는 REST 기반으로 하며 HTTP REST를 호출할 수 있는 VM에서 실행되는 모든 클라이언트 애플리케이션에 액세스할 수 있도록 합니다. 클라이언트가 가상 머신(및 Azure AD 끝점)에서 엔드포인트를 사용하는 점을 제외하고 Azure AD 프로그래밍 모델과 유사합니다.

IMDS(Instance Metadata Service) 엔드포인트를 사용하는 요청 샘플 *(권장됨)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| 요소 | 설명 |
| ------- | ----------- |
| `GET` | HTTP 동사는 엔드포인트에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Instance Metadata Service에 대한 Azure 리소스 관리 ID 엔드포인트입니다. |
| `api-version`  | IMDS 엔드포인트의 API 버전을 나타내는 쿼리 문자열 매개 변수입니다. API 버전 `2018-02-01` 이상을 사용하세요. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 또한 발급된 토큰의 `aud` (대상) 클레임에서 표시됩니다. 이 예제에서는 Azure Resource Manager에 액세스할 수 있는 토큰을 요청합니다. 여기에는 `https://management.azure.com/`이라는 앱 ID URI가 포함됩니다. |
| `Metadata` | SSRF(서버 쪽 요청 위조) 공격에 대한 완화 수단으로 Azure 리소스에 대한 관리 ID에서 HTTP 요청 헤더 필드가 필요합니다. 이 값은 모두 소문자이며 "true"로 설정되어야 합니다. |
| `object_id` | (선택 사항) 토큰을 원하는 관리 ID의 object_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|
| `client_id` | (선택 사항) 토큰을 원하는 관리 ID의 client_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|
| `mi_res_id` | 필드 토큰을 원하는 관리 id의 mi_res_id (Azure 리소스 ID)을 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다. |

Azure 리소스에 대한 관리 ID VM 확장 엔드포인트를 사용하는 요청 샘플 *(2019년 1월에 사용 중단될 예정)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 요소 | 설명 |
| ------- | ----------- |
| `GET` | HTTP 동사는 엔드포인트에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `http://localhost:50342/oauth2/token` | Azure 리소스에 대한 관리 ID 엔드포인트입니다. 여기서 50342는 기본 포트이며 구성 가능합니다. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 또한 발급된 토큰의 `aud` (대상) 클레임에서 표시됩니다. 이 예제에서는 Azure Resource Manager에 액세스할 수 있는 토큰을 요청합니다. 여기에는 `https://management.azure.com/`이라는 앱 ID URI가 포함됩니다. |
| `Metadata` | SSRF(서버 쪽 요청 위조) 공격에 대한 완화 수단으로 Azure 리소스에 대한 관리 ID에서 HTTP 요청 헤더 필드가 필요합니다. 이 값은 모두 소문자이며 "true"로 설정되어야 합니다.|
| `object_id` | (선택 사항) 토큰을 원하는 관리 ID의 object_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|
| `client_id` | (선택 사항) 토큰을 원하는 관리 ID의 client_id를 나타내는 쿼리 문자열 매개 변수입니다. VM에 여러 사용자 할당 관리 ID가 있는 경우 필수입니다.|

샘플 응답:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 요소 | Description |
| ------- | ----------- |
| `access_token` | 요청된 액세스 토큰입니다. 보안이 설정된 REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. | 
| `refresh_token` | Azure 리소스에 대한 관리 ID에서 사용되지 않습니다. |
| `expires_in` | 액세스 토큰의 발급 시간부터 만료 이전까지 계속 유효한 시간(초)입니다. 발급 시간은 토큰의 `iat` 클레임에서 확인할 수 있습니다. |
| `expires_on` | 액세스 토큰이 만료되는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `exp` 클레임에 해당함). |
| `not_before` | 액세스 토큰이 적용되고 허용될 수 있는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `nbf` 클레임에 해당함). |
| `resource` | 액세스 토큰이 요청되는 리소스는 요청의 `resource` 쿼리 문자열 매개 변수와 일치합니다. |
| `token_type` | 토큰의 형식은 "전달자" 액세스 토큰입니다. 즉, 리소스가 이 토큰의 전달자에 액세스 권한을 제공할 수 있습니다. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>.NET용 Microsoft.Azure.Services.AppAuthentication 라이브러리를 사용하여 토큰 가져오기

.NET 애플리케이션 및 함수의 경우 Azure 리소스에 대한 관리 ID를 사용하는 가장 간단한 방법은 Microsoft.Azure.Services.AppAuthentication 패키지입니다. 또한 이 라이브러리는 개발 머신에서 Visual Studio, [Azure CLI](/cli/azure?view=azure-cli-latest) 또는 Active Directory 통합 인증의 사용자 계정을 사용하여 로컬로 코드를 테스트할 수 있습니다. 이 라이브러를 통한 로컬 개발 옵션에 대한 자세한 내용은[Microsoft.Azure.Services.AppAuthentication 참조](../../key-vault/general/service-to-service-authentication.md)를 참조하세요. 이 섹션에서는 코드에서 이 라이브러리를 시작하는 방법을 보여 줍니다.

1. 애플리케이션에 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 패키지의 참조를 추가합니다.

2.  애플리케이션에 다음 코드를 추가합니다.

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Microsoft.Azure.Services.AppAuthentication 및 노출하는 작업에 대한 자세한 내용은 [Microsoft.Azure.Services.AppAuthentication 참조](../../key-vault/general/service-to-service-authentication.md) 및 [Azure 리소스에 대한 관리 ID .NET 샘플이 포함된 App Service 및 KeyVault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)를 참조하세요.

## <a name="get-a-token-using-c"></a>C#을 사용하여 토큰 가져오기

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Java를 사용하여 토큰 가져오기

이 [JSON 라이브러리](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4)를 사용하여 Java를 통해 토큰을 검색합니다.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Go를 사용하여 토큰 가져오기

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Azure PowerShell을 사용하여 토큰 가져오기

다음 예제에는 PowerShell 클라이언트에서 Azure 리소스에 대한 관리 ID REST 엔드포인트를 사용하는 방법을 보여줍니다.

1. 액세스 토큰 획득
2. 액세스 토큰을 사용하여 Azure Resource Manager REST API를 호출하고 VM에 대한 정보를 가져옵니다. 구독 ID, 리소스 그룹 이름 및 가상 머신 이름을 각각 `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>` 및 `<VM-NAME>`으로 대체해야 합니다.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

응답에서 액세스 토큰을 구문 분석하는 방법에 대한 예제:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>CURL을 사용하여 토큰 가져오기

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


응답에서 액세스 토큰을 구문 분석하는 방법에 대한 예제:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>토큰 캐싱

사용 중인 Azure 리소스에 대한 관리 ID 하위 시스템(IMDS/Azure 리소스에 대한 관리 ID VM 확장)에서 토큰을 캐시하지만, 코드에 토큰 캐싱도 구현하는 것이 좋습니다. 따라서 리소스에서 토큰이 만료되었음을 나타내는 시나리오를 준비해야 합니다. 

Azure AD에 대한 실시간 호출은 다음과 같은 경우에만 호출됩니다.
- Azure 리소스 하위 시스템 캐시에 대한 관리 ID에 토큰이 없기 때문에 캐시 누락이 발생하는 경우
- 캐시된 토큰이 만료된 경우

## <a name="error-handling"></a>오류 처리

Azure 리소스에 대한 관리 ID 엔드포인트는 HTTP 응답 메시지 헤더의 상태 코드 필드를 통해 4xx 또는 5xx 중 하나의 오류로 신호를 보냅니다.

| 상태 코드 | 오류 원인 | 처리 방법 |
| ----------- | ------------ | ------------- |
| 404 찾을 수 없습니다. | IMDS 엔드포인트를 업데이트하는 중입니다. | 지수 백오프를 통해 다시 시도. 아래 지침을 참조하세요. |
| 429 요청이 너무 많음. |  IMDS 스로틀 한계에 도달함. | 지수 백오프를 통해 다시 시도. 아래 지침을 참조하세요. |
| 요청의 4xx 오류입니다. | 요청 매개 변수 중 하나 이상이 올바르지 않습니다. | 다시 시도하지 마세요.  자세한 내용은 오류 세부 정보를 확인하세요.  4xx 오류는 디자인 타임 오류입니다.|
| 서비스의 5xx 일시적 오류입니다. | Azure 리소스에 대한 관리 ID 하위 시스템 또는 Azure Active Directory에서 일시적 오류를 반환했습니다. | 1초 이상 대기한 후에 다시 시도하는 것이 안전합니다.  너무 빨리 또는 너무 자주 다시 시도하는 경우 IMDS 및/또는 Azure AD는 속도 제한 오류(429)를 반환할 수 있습니다.|
| 시간 제한 | IMDS 엔드포인트를 업데이트하는 중입니다. | 지수 백오프를 통해 다시 시도. 아래 지침을 참조하세요. |

오류가 발생하면 해당하는 HTTP 응답 본문에는 다음과 같은 오류 세부 정보와 함께 JSON이 포함됩니다.

| 요소 | 설명 |
| ------- | ----------- |
| error   | 오류 식별자 |
| error_description | 오류의 자세한 설명입니다. **오류 설명은 언제 든 지 변경 될 수 있습니다. 오류 설명의 값을 기반으로 분기 하는 코드를 작성 하지 마십시오.**|

### <a name="http-response-reference"></a>HTTP 응답 참조

이 섹션에서는 가능한 오류 응답을 문서화합니다. "200 확인" 상태는 성공적인 응답이며 액세스 토큰은 access_token 요소의 JSON 응답 본문에 포함되어 있습니다.

| 상태 코드 | 오류 | 오류 설명 | 해결 방법 |
| ----------- | ----- | ----------------- | -------- |
| 400 잘못된 요청 | invalid_resource | AADSTS50001: 라는 테 넌 트에서 라는 응용 프로그램을 *\<URI\>* 찾을 수 없습니다 *\<TENANT-ID\>* . 이 오류는 테넌트의 관리자가 애플리케이션을 설치하지 않았거나 테넌트의 사용자가 동의하지 않은 경우에 발생할 수 있습니다. 잘못된 테넌트에 인증 요청을 보냈을 수도 있습니다. | (Linux만 해당) |
| 400 잘못된 요청 | bad_request_102 | 필수 메타데이터 헤더가 지정되지 않았습니다. | `Metadata` 요청 헤더 필드가 요청에서 누락되거나 형식이 잘못되었습니다. 값은 모두 소문자이며 `true`으로 지정해야 합니다. 예제는 이전 REST 섹션에서 "샘플 요청"을 참조하세요.|
| 401 권한 없음 | unknown_source | 알 수 없는 소스 *\<URI\>* | HTTP GET 요청 URI의 형식이 올바른지 확인합니다. `scheme:host/resource-path` 부분은 `http://localhost:50342/oauth2/token`으로 지정해야 합니다. 예제는 이전 REST 섹션에서 "샘플 요청"을 참조하세요.|
|           | invalid_request | 요청이 필수 매개 변수를 누락하거나, 잘못된 매개 변수 값이 포함되거나, 매개 변수를 두 번 이상 포함되거나 형식이 잘못되었습니다. |  |
|           | unauthorized_client | 클라이언트에는 이 메서드를 사용하여 액세스 토큰을 요청할 권한이 없습니다. | 로컬 루프백을 사용 하 여 확장을 호출 하지 않은 요청 또는 Azure 리소스에 대 한 관리 id가 올바르게 구성 되지 않은 VM에 의해 발생 합니다. VM을 구성하는 데 도움이 필요한 경우 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md)을 참조하세요. |
|           | access_denied | 리소스 소유자 또는 권한 부여 서버에서 요청을 거부했습니다. |  |
|           | unsupported_response_type | 권한 부여 서버는 이 메서드를 사용하여 액세스 토큰을 획득하도록 지원하지 않습니다. |  |
|           | invalid_scope | 요청된 범위가 잘못되었거나, 알려지지 않거나, 형식이 잘못되었습니다. |  |
| 500 내부 서버 오류 | 알 수 없음 | Active directory에서 토큰을 검색하지 못했습니다. 자세한 내용은 로그인 *\<file path\>* | VM에서 Azure 리소스에 대한 관리ID가 사용되도록 설정되어 있는지 확인합니다. VM을 구성하는 데 도움이 필요한 경우 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md)을 참조하세요.<br><br>또한 HTTP GET 요청 URI의 형식, 특히 쿼리 문자열에서 지정된 리소스 URI가 올바르게 지정되었는지 확인합니다. 예제는 이전 REST 섹션에서 "샘플 요청"을 참조하세요. 또는 서비스 및 각 리소스 ID의 목록은 [Azure AD 인증을 지원하는 Azure 서비스](./services-support-managed-identities.md)를 참조하세요.

## <a name="retry-guidance"></a>다시 시도 지침 

404, 429 또는 5xx 오류 코드가 표시되면 다시 시도하는 것이 좋습니다(위의 [오류 처리](#error-handling) 참조).

제한 한도는 IMDS 엔드 포인트에 호출한 횟수에 적용됩니다. 제한 임계값을 초과하면 제한이 적용되는 동안 IMDS 엔드포인트에서 추가 요청을 제한합니다. 이 기간 동안 IMDS 엔드포인트는 429 HTTP 상태 코드("요청이 너무 많음")를 반환하고 요청이 실패합니다. 

다시 시도하는 경우 다음 전략을 사용하는 것이 좋습니다. 

| **재시도 전략** | **설정** | **값** | **작동 방법** |
| --- | --- | --- | --- |
|ExponentialBackoff |재시도 횟수<br />최소 백오프<br />최대 백오프<br />델타 백오프<br />첫 번째 빠른 재시도 |5<br />0초<br />60초<br />2초<br />false |시도 1 - 0초 지연<br />시도 2 - ~2초 지연<br />시도 3 - ~6초 지연<br />시도 4 - ~14초 지연<br />시도 5 - ~30초 지연 |

## <a name="resource-ids-for-azure-services"></a>Azure 서비스의 리소스 ID

Azure AD를 지원하고 Azure 리소스의 관리 ID 및 해당하는 리소스 ID를 사용하여 테스트된 리소스의 목록은 [Azure AD 인증을 지원하는 Azure 서비스](./services-support-managed-identities.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하려면 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md)을 참조하세요.