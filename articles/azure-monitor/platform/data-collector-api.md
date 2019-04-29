---
title: Azure Monitor HTTP 데이터 수집기 API | Microsoft Docs
description: REST API를 호출할 수 있는 모든 클라이언트에서 Azure Monitor HTTP 데이터 수집기 API를 사용하여 POST JSON 데이터를 Log Analytics 작업 영역에 추가할 수 있습니다. 이 문서는 API를 사용하는 방법을 설명하며, 다양한 프로그래밍 언어를 사용하여 데이터를 게시하는 방법을 예제로 제시합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: bwren
ms.openlocfilehash: 9fd65dc0a6d2a5756acd2de7cb46fbf7943a8758
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931791"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>HTTP 데이터 수집기 API로 Azure Monitor에 로그 데이터 전송(공개 미리 보기)
이 문서에서는 HTTP 데이터 수집기 API를 사용하여 REST API 클라이언트에서 Azure Monitor로 로그 데이터를 전송하는 방법을 보여 줍니다.  스크립트 또는 애플리케이션에서 수집한 데이터의 서식을 지정하고, 요청에 포함하며, 해당 요청에 대한 Azure Monitor의 인증을 받는 방법을 설명합니다.  PowerShell, C# 및 Python에 예가 제공됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Azure Monitor HTTP 데이터 수집기 API는 공개 미리 보기로 제공되고 있습니다.

## <a name="concepts"></a>개념
HTTP 데이터 수집기 API를 사용하여 REST API를 호출할 수 있는 클라이언트에서 Azure Monitor의 Log Analytics 작업 영역으로 로그 데이터를 전송할 수 있습니다.  Azure 또는 다른 클라우드에서 관리 데이터를 수집하는 Azure Automation의 Runbook이거나 Azure Monitor를 사용하여 로그 데이터를 통합하고 분석하는 대체 관리 시스템일 수 있습니다.

Log Analytics 작업 영역의 모든 데이터는 특정 레코드 종류의 레코드로 저장됩니다.  JSON에서 여러 레코드로 HTTP 데이터 수집기 API에 보낼 데이터의 서식을 지정합니다.  데이터를 제출하면 개별 레코드가 요청 페이로드의 각 레코드에 대한 저장소에 만들어집니다.


![HTTP 데이터 수집기 개요](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>요청 만들기
HTTP 데이터 수집기 API를 사용하려면 JSON(JavaScript Object Notation)에서 전송할 데이터가 포함된 POST 요청을 만듭니다.  다음 세 개 표에는 각 요청에 필요한 속성이 나와 있습니다. 이 문서의 뒷부분에서 각각의 속성에 대해 자세히 설명합니다.

### <a name="request-uri"></a>요청 URI
| 특성 | 자산 |
|:--- |:--- |
| 방법 |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| 콘텐츠 형식 |application/json |

### <a name="request-uri-parameters"></a>URI 매개 변수 요청
| 매개 변수 | 설명 |
|:--- |:--- |
| CustomerID |Log Analytics 작업 영역에 대한 고유 식별자입니다. |
| 리소스 |API 리소스 이름: /api/logs |
| API 버전 |이 요청에 사용하는 API의 버전입니다. 현재 2016-04-01입니다. |

### <a name="request-headers"></a>헤더 요청
| 헤더 | 설명 |
|:--- |:--- |
| 권한 부여 |권한 부여 서명입니다. 문서의 뒷부분에 HMAC-SHA256 헤더를 만드는 방법이 나와 있습니다. |
| Log-Type |제출 중인 데이터의 레코드 종류를 지정합니다. 이 매개 변수에 대한 크기 제한은 100자입니다. |
| x-ms-date |RFC 1123 형식의 요청이 처리된 날짜입니다. |
| x-ms-AzureResourceId | Azure 리소스 데이터의 리소스 ID와 연결 해야 합니다. 채웁니다 합니다 [_ResourceId](log-standard-properties.md#_resourceid) 속성에 포함 될 데이터를 허용 하 고 [리소스 중심](manage-access.md#access-modes) 쿼리 합니다. 이 필드를 지정 하지 않으면 리소스 중심 쿼리에서 데이터 포함 되지 않습니다. |
| time-generated-field | 데이터 항목의 타임스탬프가 포함된 데이터의 필드 이름입니다. 필드를 지정하면 그 내용이 **TimeGenerated**에 사용됩니다. 이 필드를 지정하지 않으면 **TimeGenerated**의 기본값은 메시지가 수집된 시간입니다. 메시지 필드의 내용은 ISO 8601 형식 YYYY-MM-DDThh:mm:ssZ를 따라야 합니다. |

## <a name="authorization"></a>권한 부여
Azure Monitor HTTP 데이터 수집기 API에 대한 모든 요청에는 인증 헤더가 포함되어야 합니다. 요청을 인증하려면 요청을 수행하는 작업 영역에 대한 기본 키 또는 보조 키를 통해 요청을 서명해야 합니다. 그런 다음 요청의 일부로 해당 서명을 전달합니다.   

권한 부여 헤더의 형식은 다음과 같습니다.

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID*는 Log Analytics 작업 영역에 대한 고유 식별자입니다. *서명*은 요청에서 구성되고 [SHA256 알고리즘](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx)을 사용하여 계산된 [해시 기반 메시지 인증 코드(HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx)입니다. 그런 다음 Base64 인코딩을 사용하여 인코딩합니다.

이 형식을 사용하여 **SharedKey** 서명 문자열을 인코딩합니다.

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

서명 문자열의 예는 다음과 같습니다.

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

서명 문자열이 있을 때는 UTF-8 문자열에서 HMAC-SHA256 알고리즘을 사용하여 인코딩한 다음 결과를 Base64로 인코딩합니다. 이 형식을 사용합니다.

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

다음 섹션의 샘플은 권한 부여 헤더를 만드는 데 도움이 되는 예제 코드입니다.

## <a name="request-body"></a>요청 본문
메시지의 본문은 JSON에 있어야 합니다. 다음 형식으로 속성 이름과 값 쌍을 갖는 하나 이상의 레코드를 포함해야 합니다.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

다음 형식을 사용하여 단일 요청에서 여러 레코드를 일괄 처리할 수 있습니다. 모든 레코드는 동일한 레코드 형식 이어야 합니다.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>레코드 유형 및 속성
Azure Monitor HTTP 데이터 수집기 API를 통해 데이터를 제출할 때 사용자 지정 레코드 종류를 정의합니다. 현재는 다른 데이터 형식과 솔루션으로 만든 기존 레코드 형식에 데이터를 쓸 수 없습니다. Azure Monitor는 들어오는 데이터를 읽은 다음, 입력된 값의 데이터 형식과 일치하는 속성을 만듭니다.

데이터 수집기 API에 대한 각 요청에는 레코드 종류의 이름과 함께 **Log-Type** 헤더가 포함되어야 합니다. 이 사용자 지정 로그를 다른 로그 형식과 구분하기 위해 입력한 이름에는 접미사 **_CL**이 자동으로 추가됩니다. 예를 들어 이름으로 **MyNewRecordType**을 입력할 경우 Azure Monitor는 **MyNewRecordType_CL** 형식의 레코드를 만듭니다. 이렇게 하면 사용자가 만든 형식 이름과 Microsoft가 현재 또는 향후에 포함한 이름 사이의 충돌을 방지할 수 있습니다.

속성의 데이터 형식을 식별하기 위해 Azure Monitor는 속성 이름에 접미사를 추가합니다. 속성에 null 값이 있으면 속성이 해당 레코드에 포함되지 않습니다. 이 표는 속성 데이터 형식과 해당하는 접미사를 나열합니다.

| 속성 데이터 형식 | 접미사 |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| 날짜/시간 |_t |
| GUID |_g |

Azure Monitor가 각 속성에 사용하는 데이터 형식은 새 레코드의 레코드 종류가 이미 있는지 여부에 따라 달라집니다.

* 레코드 종류가 없는 경우 Azure Monitor는 JSON 형식 유추를 통해 새 레코드 종류를 만들어 새 레코드에 포함된 각 속성의 데이터 형식을 결정합니다.
* 레코드 종류가 있는 경우 Azure Monitor는 기존 속성에 따라 새 레코드를 만들려고 합니다. 새 레코드에 포함된 속성의 데이터 형식이 일치하지 않고 기존 형식으로 변환할 수 없거나, 레코드가 존재하지 않는 속성을 포함하는 경우 Azure Monitor는 관련 접미사가 있는 새 속성을 만듭니다.

예를 들어, 이 제출 항목은 **number_d**, **boolean_b**, **string_s** 등의 세 가지 속성이 있는 레코드를 만듭니다.

![샘플 레코드 1](media/data-collector-api/record-01.png)

이후 모든 값을 문자열 형식으로 지정하여 다음 항목을 제출하면 속성이 변경되지 않습니다. 이러한 값은 기존 데이터 형식으로 변환할 수 있습니다.

![샘플 레코드 2](media/data-collector-api/record-02.png)

그러나 이 다음 제출을 실행하면 Azure Monitor가 새 속성 **boolean_d** 및 **string_d**를 만듭니다. 이 값은 변환할 수 없습니다.

![샘플 레코드 3](media/data-collector-api/record-03.png)

이후에 다음 항목을 제출한 경우 레코드 종류가 생성되기 전에 Azure Monitor는 **number_s**, **boolean_s** 및 **string_s**의 세 가지 속성을 가진 레코드를 만듭니다. 이 항목에서 각각의 초기 값은 문자열 형식이 됩니다.

![샘플 레코드 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>예약 된 속성
예약 되어 있으므로 사용자 지정 레코드 형식에서는 사용할 수 없습니다. 페이로드 이러한 속성 이름을 포함 하는 경우 오류를 받습니다.

- tenant

## <a name="data-limits"></a>데이터 제한
Azure Monitor 데이터 수집 API에 게시되는 데이터와 관련된 몇 가지 제약 조건이 있습니다.

* Azure Monitor 데이터 수집기 API에 대한 게시당 최대 30MB. 이는 단일 게시물에 대한 크기 제한입니다. 단일 게시물의 데이터가 30MB를 초과하는 경우 보다 작은 크기의 청크로 분할하여 동시에 보내야 합니다.
* 최대 32KB의 필드 값 제한. 필드 값이 32KB보다 크면 데이터가 잘립니다.
* 지정된 형식의 권장되는 최대 필드 수는 50개입니다. 이는 사용 편의성 및 검색 환경 관점에서의 실용적인 제한입니다.  
* Log Analytics 작업 영역의 테이블은 최대 500개의 열(이 문서에서는 필드라고 함)만 지원합니다. 
* 열 이름의 최대 문자 수는 500자입니다.

## <a name="return-codes"></a>반환 코드
HTTP 상태 코드 200는 처리를 위한 요청을 받았다는 것을 의미합니다. 이 항목은 작업이 성공적으로 완료되었음을 나타냅니다.

이 표는 서비스에서 반환할 수 있는 전체 상태 코드 집합을 보여 줍니다.

| 코드 | 상태 | 오류 코드 | 설명 |
|:--- |:--- |:--- |:--- |
| 200 |확인 | |요청이 성공적으로 수락되었습니다. |
| 400 |잘못된 요청 |InactiveCustomer |작업 영역이 닫혔습니다. |
| 400 |잘못된 요청 |InvalidApiVersion |지정한 API 버전이 서비스에서 인식되지 않았습니다. |
| 400 |잘못된 요청 |InvalidCustomerId |지정된 작업 영역 ID가 올바르지 않습니다. |
| 400 |잘못된 요청 |InvalidDataFormat |잘못된 JSON이 제출되었습니다. 응답 본문에 오류 해결 방법에 관한 추가 정보가 포함될 수 있습니다. |
| 400 |잘못된 요청 |InvalidLogType |지정한 로그 형식이 특수 문자나 숫자를 포함합니다. |
| 400 |잘못된 요청 |MissingApiVersion |API 버전을 지정하지 않았습니다. |
| 400 |잘못된 요청 |MissingContentType |콘텐츠 형식을 지정하지 않았습니다. |
| 400 |잘못된 요청 |MissingLogType |필요한 값 로그 형식을 지정하지 않았습니다. |
| 400 |잘못된 요청 |UnsupportedContentType |콘텐츠 형식이 **application/json**으로 설정되지 않았습니다. |
| 403 |사용할 수 없음 |InvalidAuthorization |서비스가 요청을 인증하지 못했습니다. 작업 영역 ID 및 연결 키가 올바른지 확인합니다. |
| 404 |찾을 수 없음 | | 제공된 URL이 잘못되었거나 요청이 너무 큽니다. |
| 429 |너무 많은 요청 | | 서비스 계정에서 많은 양의 데이터가 발생했습니다. 요청을 나중에 다시 시도하세요. |
| 500 |내부 서버 오류 |UnspecifiedError |서비스에 내부 오류가 발생했습니다. 요청을 다시 시도하세요. |
| 503 |서비스를 사용할 수 없음 |ServiceUnavailable |현재 서비스가 요청을 받을 수 없습니다. 요청을 다시 시도하세요. |

## <a name="query-data"></a>쿼리 데이터
Azure Monitor HTTP 데이터 수집기 API에서 제출한 데이터를 쿼리하려면 지정한 **LogType** 값에 **_CL**을 추가한 형식과 같은 **형식**의 레코드를 검색합니다. 예를 들어 **MyCustomLog**를 사용한 경우 `MyCustomLog_CL`이 포함된 레코드를 모두 반환합니다.

## <a name="sample-requests"></a>샘플 요청
다음 섹션에서는 다양한 프로그래밍 언어를 사용하여 Azure Monitor HTTP 데이터 수집기 API에 데이터를 제출하는 방법의 샘플을 제공합니다.

각각의 샘플에서 다음 절차를 통해 권한 부여 헤더에 대한 변수를 설정합니다.

1. Azure Portal에서 Log Analytics 작업 영역을 찾습니다.
2. **고급 설정** 및 **연결된 원본**을 차례로 선택합니다.
2. **작업 영역 ID** 오른쪽에서 복사 아이콘을 선택한 다음 이 ID를 **고객 ID** 변수의 값으로 붙여넣습니다.
3. **기본 키** 오른쪽에서 복사 아이콘을 선택한 다음 이 ID를 **공유 키** 변수의 값으로 붙여넣습니다.

또는 로그 형식 및 JSON 데이터에 대한 변수를 변경할 수 있습니다.

### <a name="powershell-sample"></a>PowerShell 샘플
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# 샘플
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Python 2 샘플
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```
## <a name="alternatives-and-considerations"></a>대체 및 고려 사항
데이터 수집기 API를 Azure 로그에 자유 형식 데이터를 수집 하 여 필요한 대부분의 다루어야, 하는 동안 일부의 API의 한계를 극복 하기 위해 대안을 해야 할 수 있습니다 위치 인스턴스가 있습니다. 모든 옵션은 다음과 같으며, 포함 하는 주요 고려 사항:

| 대체 | 설명 | 가장 적합 |
|---|---|---|
| [사용자 지정 이벤트](https://docs.microsoft.com/en-us/azure/azure-monitor/app/api-custom-events-metrics?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Application Insights에서 네이티브 SDK 기반 수집 | 일반적으로 응용 프로그램 내에서 SDK를 통해 계측 되는 application Insights는 사용자 지정 이벤트를 통해 사용자 지정 데이터를 보낼 수 있는 기능을 제공 합니다. | <ul><li> 기본 데이터 형식 중 하나를 통해 SDK에 의해 선택 되지 있지만 응용 프로그램 내에서 생성 된 데이터 (예: 요청, 종속성, 예외 등).</li><li> Application Insights의 다른 응용 프로그램 데이터를 상호 관련 된 대부분의 데이터 </li></ul> |
| [데이터 수집기 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) Azure Monitor 로그에서 | Azure Monitor 로그에서 데이터 수집기 API를 완전히 자유로운 방식으로 데이터를 수집 하는. 다음 JSON 개체에 서식이 지정 된 모든 데이터를 보낼 수 있습니다. 전송 된 후 처리 하는, 및 로그 수를 사용할 수 있는 다른 데이터 연관성이 로그 나 다른 Application Insights에 대 한 데이터입니다. <br/><br/> 데이터 파일로 blob에 업로드 하는 Azure Blob에서에서 이러한 파일을 처리 및 Log Analytics에 업로드 하려는 매우 쉽습니다. 참조 하세요 [이](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) 이러한 파이프라인의 샘플 구현에 대 한 문서. | <ul><li> Application Insights 내에서 계측 된 응용 프로그램 내에서 생성 하지 않은 데이터입니다.</li><li> 조회 및 팩트 테이블, 참조 데이터를 미리 집계 된 통계 등을 예로 들 수 있습니다. </li><li> 다른 Azure Monitor 데이터 (예: Application Insights, 보안 센터, Azure Monitor 컨테이너/v m 등 다른 로그 데이터 형식)에 대 한 상호 참조 되는 데이터를 위한 것입니다. </li></ul> |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview) | Azure 데이터 탐색기 (ADX)에 Application Insights Analytics와 Azure Monitor 로그를 구동 하는 데이터 플랫폼입니다. 이제 일반적으로 사용 가능 ("GA"), 데이터 플랫폼을 사용 하 여 원시 형태로 제공 완전 유연성 (하지만 관리 오버 헤드가 필요) (RBAC 보존 율, 스키마 등)으로 클러스터 합니다. 다 수 제공 ADX [수집 옵션](https://docs.microsoft.com/azure/data-explorer/ingest-data-overview#ingestion-methods) 포함 [CSV, TSV 및 JSON](https://docs.microsoft.com/azure/kusto/management/mappings?branch=master) 파일입니다. | <ul><li> 데이터는 상호 관련 Application Insights 또는 로그에서 다른 데이터입니다. </li><li> 데이터 수집을 고급 필요 하거나 Azure Monitor 로그에서 현재 사용할 수 없는 기능을 처리 합니다. </li></ul> |


## <a name="next-steps"></a>다음 단계
- [로그 검색 API](../log-query/log-query-overview.md)를 사용하여 Log Analytics 작업 영역에서 데이터를 검색합니다.

- Azure Monitor에 대한 Logic Apps 워크플로를 사용하여 [데이터 수집기 API로 데이터 파이프라인을 만드는](create-pipeline-datacollector-api.md) 방법을 알아봅니다.
