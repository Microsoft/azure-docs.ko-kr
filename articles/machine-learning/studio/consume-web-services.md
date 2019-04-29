---
title: 웹 서비스 사용
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio에서 기계 학습 서비스가 배포된 후에는 RESTFul 웹 서비스를 실시간 요청-응답 서비스 또는 일괄 처리 실행 서비스로 사용할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: a537227a7003391122e10f7f39233040cef49db3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751300"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-web-service"></a>Azure Machine Learning Studio 웹 서비스 사용 방법

Azure Machine Learning Studio 예측 모델을 웹 서비스로 배포하고 나면 REST API를 사용하여 데이터를 전송하고 예측을 얻을 수 있습니다. 데이터를 실시간으로 또는 일괄 처리 모드로 보낼 수 있습니다.

다음 Machine Learning 스튜디오를 사용하여 Machine Learning 웹 서비스를 만들고 배포하는 방법에 대한 자세한 내용을 알아볼 수 있습니다.

* Machine Learning Studio에서 실험을 만드는 방법에 대한 자습서는 [첫 번째 실험 만들기](create-experiment.md)를 참조하세요.
* 웹 서비스를 배포하는 방법에 대한 자세한 내용은 [Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.
* Machine Learning에 대한 자세한 내용은 [Machine Learning 설명서 센터](https://azure.microsoft.com/documentation/services/machine-learning/)를 참조하세요.



## <a name="overview"></a>개요
Azure Machine Learning 웹 서비스를 통해 외부 애플리케이션에서 Machine Learning 워크플로 점수 매기기 모델과 실시간으로 통신할 수 있습니다. Machine Learning 웹 서비스 호출은 외부 애플리케이션에 예측 결과를 반환합니다. Machine Learning 웹 서비스를 호출하려면 예측을 배포할 때 만들어진 API 키를 전달합니다. Machine Learning 웹 서비스는 웹 프로그래밍 프로젝트에 일반적으로 사용되는 아키텍처인 REST를 기반으로 합니다.

Azure Machine Learning Studio에는 다음 두 가지 유형의 서비스가 있습니다.

* RRS(요청-응답 서비스) - 대기 시간이 짧고 확장성이 높은 서비스로, Machine Learning Studio에서 생성 및 배포되는 상태 비저장 모델에 대한 인터페이스를 제공합니다.
* BES(일괄 처리 실행 서비스) – 데이터 레코드의 점수를 일괄적으로 매기는 비동기 서비스입니다.

Machine Learning 웹 서비스에 대한 자세한 내용은 [Machine Learning 웹 서비스 배포](publish-a-machine-learning-web-service.md)를 참조하세요.

## <a name="get-an-azure-machine-learning-studio-authorization-key"></a>Azure Machine Learning Studio 인증 키 가져오기
실험을 배포할 때 웹 서비스에 API 키가 생성됩니다. 여러 위치에서 키를 검색할 수 있습니다.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Microsoft Azure Machine Learning 웹 서비스 포털에서
[Microsoft Azure Machine Learning 웹 서비스](https://services.azureml.net) 포털에 로그인합니다.

새 Machine Learning 웹 서비스에 대한 API 키를 가져오려면 다음을 수행합니다.

1. Azure Machine Learning 웹 서비스 포털의 최상위 메뉴에서 **웹 서비스**를 클릭합니다.
2. 키를 검색하려는 웹 서비스를 클릭합니다.
3. 위쪽 메뉴에서 **사용**을 클릭합니다.
4. **기본 키**를 복사하고 저장합니다.

클래식 Machine Learning 웹 서비스에 대한 API 키를 가져오려면:

1. Azure Machine Learning 웹 서비스 포털의 최상위 메뉴에서 **클래식 웹 서비스**를 클릭합니다.
2. 사용하고 있는 웹 서비스를 클릭합니다.
3. 키를 검색하려는 엔드포인트를 클릭합니다.
4. 위쪽 메뉴에서 **사용**을 클릭합니다.
5. **기본 키**를 복사하고 저장합니다.

### <a name="classic-web-service"></a>기존 웹 서비스
 Machine Learning Studio에서 클래식 웹 서비스에 대한 키를 검색할 수도 있습니다.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. Machine Learning Studio의 왼쪽에서 **웹 서비스**를 클릭합니다.
2. 웹 서비스를 클릭합니다. **API 키**는 **대시보드** 탭에 있습니다.

## <a id="connect"></a>Machine Learning 웹 서비스에 연결
HTTP 요청 및 응답을 지원하는 모든 프로그래밍 언어를 사용하여 Machine Learning 웹 서비스에 연결할 수 있습니다. Machine Learning 웹 서비스 도움말 페이지에서 C#, Python 및 R로 작성된 예제를 볼 수 있습니다.

**Machine Learning API 도움말** 웹 서비스를 배포하면 Machine Learning API 도움말이 생성됩니다. [자습서 3: 신용 위험 모델 배포](tutorial-part3-credit-risk-deploy.md)를 참조하세요.
Machine Learning API 도움말에는 예측 웹 서비스에 대한 세부 정보가 포함되어 있습니다.

1. 사용하고 있는 웹 서비스를 클릭합니다.
2. API 도움말 페이지를 보려는 엔드포인트를 클릭합니다.
3. 위쪽 메뉴에서 **사용**을 클릭합니다.
4. 응답 간 또는 Batch 실행 엔드포인트에서 **API 도움말 페이지**를 클릭합니다.

**새 웹 서비스에 대한 Machine Learning API 도움말을 보려면**

[Azure Machine Learning 웹 서비스 포털에서](https://services.azureml.net/):

1. 최상위 메뉴에서 **웹 서비스**를 클릭합니다.
2. 키를 검색하려는 웹 서비스를 클릭합니다.

**웹 서비스 사용**을 클릭하여 C#, R 및 Python으로 요청-응답 및 Batch 실행 서비스와 샘플 코드에 대한 URI를 가져옵니다.

**Swagger API**를 클릭하여 제공된 URI에서 호출된 API에 대한 Swagger 기반 설명서를 가져옵니다.

### <a name="c-sample"></a>C# 샘플
Machine Learning 웹 서비스에 연결하려면 ScoreData를 전달하는 **HttpClient**를 사용합니다. ScoreData는 ScoreData를 나타내는 수치의 n 차원 벡터인 FeatureVector를 포함합니다. API 키로 Machine Learning 서비스를 인증합니다.

Machine Learning 웹 서비스에 연결하려면 **Microsoft.AspNet.WebApi.Client** NuGet 패키지를 설치해야 합니다.

**Visual Studio에서 Microsoft.AspNet.WebApi.Client NuGet 설치**

1. UCI: 성인 2 클래스 데이터 세트에서 데이터 세트 다운로드 웹 서비스를 게시합니다.
2. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭합니다.
3. **Install-Package Microsoft.AspNet.WebApi.Client**를 선택합니다.

**코드 샘플을 실행하려면**

1. Machine Learning 샘플 컬렉션의 일부인 “샘플 1: UCI: 성인 2 클래스 데이터 세트에서 데이터 세트 다운로드” 실험을 배포합니다.
2. 웹 서비스에서 가져온 키로 apiKey를 할당합니다. 위의 **Azure Machine Learning Studio 인증 키 가져오기**를 참조하세요.
3. 요청 URI로 serviceUri를 할당합니다.

**완료된 요청은 다음과 같습니다.**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python 샘플
Machine Learning 웹 서비스에 연결하려면 Python 2.X의 경우 **urllib2** 라이브러리를 사용하고, Python 3.X의 경우 **urllib.request** 라이브러리를 사용합니다. ScoreData를 나타내는 수치의 n 차원 벡터인 FeatureVector를 포함하는 ScoreData를 제공합니다. API 키로 Machine Learning 서비스를 인증합니다.

**코드 샘플을 실행하려면**

1. Machine Learning 샘플 컬렉션의 일부인 “샘플 1: UCI: 성인 2 클래스 데이터 세트에서 데이터 세트 다운로드” 실험을 배포합니다.
2. 웹 서비스에서 가져온 키로 apiKey를 할당합니다. 이 문서의 시작 부분에 있는 **Azure Machine Learning Studio 인증 키 가져오기** 섹션을 참조하세요.
3. 요청 URI로 serviceUri를 할당합니다.

**완료된 요청은 다음과 같습니다.**
```python
import urllib2 # urllib.request for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(uri, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R 샘플

Machine Learning 웹 서비스에 연결하려면 **RCurl** 및 **rjson** 라이브러리를 사용하여 요청을 수행하고 반환된 JSON 응답을 처리합니다. ScoreData를 나타내는 수치의 n 차원 벡터인 FeatureVector를 포함하는 ScoreData를 제공합니다. API 키로 Machine Learning 서비스를 인증합니다.

**완료된 요청은 다음과 같습니다.**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript 샘플

Machine Learning 웹 서비스에 연결하려면 프로젝트에 **request** npm 패키지를 사용합니다. 또한 `JSON` 개체를 사용하여 입력 형식을 지정하고 결과를 구문 분석합니다. `npm install request --save`를 사용하여 설치하거나 `"request": "*"`를 `dependencies` 아래의 package.json에 추가하고 `npm install`을 설치합니다.

**완료된 요청은 다음과 같습니다.**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
