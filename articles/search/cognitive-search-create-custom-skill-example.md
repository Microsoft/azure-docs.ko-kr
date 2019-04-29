---
title: '예제: 인식 검색 파이프라인에 사용자 지정 기술 만들기 - Azure Search'
description: Azure Search의 인식 검색 인덱싱 파이프라인에 매핑된 사용자 지정 기술의 Translator Text API를 사용하는 방법을 보여 줍니다.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: f3b4e6cd18a362775443bb296560a076aaa1497d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61344162"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>예제: Translator Text API를 사용하여 사용자 지정 기술 만들기

이 예제에서는 모든 언어의 텍스트를 수락하고 영어로 번역하는 Web API 사용자 지정 기술을 만드는 방법을 알아봅니다. 예제에서는 [Azure Function](https://azure.microsoft.com/services/functions/)을 통해 [Translator Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)를 래핑하여 사용자 지정 기술 인터페이스를 구현하도록 합니다.

## <a name="prerequisites"></a>필수 조건

+ 사용자 지정 기술이 구현해야 하는 입력/출력 인터페이스를 잘 모르겠으면 [사용자 지정 기술 인터페이스](cognitive-search-custom-skill-interface.md) 문서를 읽어 보세요.

+ [Translator Text API에 등록](../cognitive-services/translator/translator-text-how-to-signup.md)하고 API 키를 가져와서 사용합니다.

+ Azure 개발 워크로드를 포함하여 [Visual Studio 2017 버전 15.5](https://www.visualstudio.com/vs/) 이상을 설치합니다.

## <a name="create-an-azure-function"></a>Azure Function 만들기

이 예제에서는 Azure Function을 사용하여 Web API를 호스트하지만 필수는 아닙니다.  [인식 기술에 대한 인터페이스 요구 사항](cognitive-search-custom-skill-interface.md)을 충족하기만 하면, 사용하는 방식은 중요하지 않습니다. 그러나 Azure Functions를 사용하면 사용자 지정 기술을 쉽게 만들 수 있습니다.

### <a name="create-a-function-app"></a>함수 앱 만들기

1. Visual Studio의 파일 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

1. 새 프로젝트 대화 상자에서 **설치됨**을 선택하고, **Visual C#** > **클라우드**를 확장하고, **Azure Functions**를 선택하고, 프로젝트의 이름을 입력한 다음, **확인**을 선택합니다. 함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자는 사용하지 마세요.

1. 선택 **Azure Functions v2 (.NET Core)** 합니다. 버전 1에서도 수행할 수 있지만 아래에 작성된 코드는 v2 템플릿을 기반으로 합니다.

1. 형식을 **HTTP 트리거**로 선택합니다.

1. 저장소 계정의 경우, 이 함수에 대한 저장소가 필요하지 않으므로 **없음**을 선택할 수 있습니다.

1. **확인**을 선택하여 함수 프로젝트 및 HTTP 트리거 함수를 만듭니다.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Translate Cognitive Service를 호출하도록 코드 수정

Visual Studio는 프로젝트를 만들고 그 안에는 선택한 함수 형식에 대한 상용구 코드를 포함하는 클래스를 만듭니다. 메서드의 *FunctionName* 특성은 함수 이름을 설정합니다. *HttpTrigger* 특성은 함수가 HTTP 요청에 의해 트리거되도록 지정합니다.

이제 *Function1.cs* 파일의 모든 내용을 다음 코드로 바꿉니다.

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Translator Text API에 등록할 때 받은 키를 기준으로 *TranslateText* 메서드에 고유한 *key* 값을 입력해야 합니다.

이 예제는 한 번에 하나의 레코드만 처리하는 간단한 보강자입니다. 이 사실은 나중에 기능의 배치 크기를 설정할 때 중요합니다.

## <a name="test-the-function-from-visual-studio"></a>Visual Studio에서 함수 테스트

**F5** 키를 눌러 프로그램을 실행하고 함수 동작을 테스트합니다. 이 경우에 스페인어 텍스트를 영어로 번역하려면 아래 기능을 사용합니다. Postman 또는 Fiddler를 사용하여 아래와 같은 호출을 실행합니다.

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>요청 본문
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>response
다음 예제와 유사한 응답이 표시됩니다.

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Azure에 함수 게시

함수 동작이 만족스러우면 함수를 게시할 수 있습니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. **새로 만들기** > **게시**를 선택합니다.

1. Visual Studio를 Azure 계정에 아직 연결하지 않았으면 **계정 추가...** 를 선택합니다.

1. 화면에 표시되는 메시지를 따릅니다. 사용할 Azure 계정, 리소스 그룹, 호스팅 플랜 및 저장소 계정을 지정하라는 메시지가 표시됩니다. 아직 없는 경우 새 리소스 그룹, 새 호스팅 플랜 및 저장소 계정을 만들 수 있습니다. 작업을 마쳤으면 **만들기**를 선택합니다.

1. 배포가 완료된 후 사이트 URL을 적어둡니다. 이 URL은 Azure에서 해당 함수 앱의 주소입니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동한 다음, 게시한 Translate Function을 찾습니다. **관리** 섹션 아래에 호스트 키가 표시됩니다. ‘기본’ 호스트 키의 **복사** 아이콘을 선택합니다.  

## <a name="test-the-function-in-azure"></a>Azure에서 함수 테스트

이제 기본 호스트 키가 있으므로 다음과 같이 함수를 테스트합니다.

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>요청 본문
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

이전에 로컬 환경에서 함수를 실행할 때 본 것과 유사한 결과가 생성됩니다.

## <a name="connect-to-your-pipeline"></a>파이프라인에 연결
이제 새 사용자 지정 기술이 있으므로 기능에 추가할 수 있습니다. 아래 예제에서는 기술을 호출하는 방법을 보여 줍니다. 기술은 배치를 처리하지 않으므로 한 번에 하나씩 문서를 보내도록 최대 배치 크기를 ```1```로 설정하는 명령을 추가합니다.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계
축하합니다! 첫 번째 사용자 지정 보강자를 만들었습니다. 이제 동일한 패턴을 따라 고유한 사용자 지정 기능을 추가할 수 있습니다. 

+ [인식 검색 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기능 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
