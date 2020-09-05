---
title: '빠른 시작: REST API 및 C#을 사용하여 이미지 인사이트 가져오기 - Bing Visual Search'
titleSuffix: Azure Cognitive Services
description: 이미지를 Bing Visual Search API에 업로드하고 이미지에 대한 인사이트를 가져오는 방법을 알아봅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-csharp
ms.openlocfilehash: e5f772fe8152f209a8188ab5bf2d0010248feabf
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934313"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>빠른 시작: Bing Visual Search REST API 및 C#을 사용하여 이미지 인사이트 가져오기

이 빠른 시작에서는 Bing Visual Search API에 이미지를 업로드하고 반환되는 인사이트를 보는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Visual Studio 2019](https://www.visualstudio.com/downloads/)의 모든 버전.
* NuGet 패키지로 사용 가능한 [Json.NET 프레임워크](https://www.newtonsoft.com/json).
* Linux/MacOS를 사용하는 경우 [Mono](https://www.mono-project.com/)를 사용하여 이 애플리케이션을 실행할 수 있습니다.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. Visual Studio에서 BingSearchApisQuickStart라는 새 콘솔 솔루션을 만듭니다. 주 코드 파일에 다음 네임스페이스를 추가합니다.

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. 구독 키, 엔드포인트 및 업로드할 이미지 경로에 대한 변수를 추가합니다. `uriBase` 값의 경우 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. `GetImageFileName()`이라는 메서드를 만들어 이미지 경로를 가져옵니다.
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. 메서드를 만들어 이미지의 이진 데이터를 가져옵니다.

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>양식 데이터 작성

1. 로컬 이미지를 업로드하려면 먼저 API에 보낼 양식 데이터를 빌드합니다. 양식 데이터는 `Content-Disposition` 헤더, "image"로 설정된 `name` 매개 변수 및 이미지의 파일 이름으로 설정된 `filename` 매개 변수를 포함합니다. 양식의 콘텐츠는 이미지의 이진 데이터를 포함합니다. 업로드할 수 있는 최대 이미지 크기는 1MB입니다.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

2. POST 양식 데이터의 형식을 지정하는 경계 문자열을 추가합니다. 경계 문자열은 데이터의 시작, 종료 및 줄 바꿈 문자를 결정합니다.

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

3. 양식 데이터에 매개 변수를 추가하려면 다음 매개 변수를 사용합니다.

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

4. 경계 문자열 및 이미지 경로를 사용하여 양식 데이터의 시작 부분을 만드는 `BuildFormDataStart()`라는 함수를 만듭니다.
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

5. 경계 문자열을 사용하여 양식 데이터의 끝 부분을 만드는 `BuildFormDataEnd()`라는 함수를 만듭니다.
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Bing Visual Search API 호출

1. Bing Visual Search 엔드포인트를 호출하는 함수를 만들고 JSON 응답을 반환합니다. 이 함수는 양식 데이터의 시작 및 끝 부분, 이미지 데이터가 포함된 바이트 배열 및 `contentType` 값을 사용합니다.

2. `WebRequest`를 사용하여 URI, contentType 값 및 헤더를 저장합니다.  

3. `request.GetRequestStream()`을 사용하여 양식 및 이미지 데이터를 기록한 다음, 응답을 받습니다. 함수는 다음 코드와 비슷해야 합니다.
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Main 메서드 만들기

1. 애플리케이션의 `Main()` 메서드에서 이미지의 파일 이름 및 이진 데이터를 가져옵니다.

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. 경계를 서식 지정하여 POST 본문을 설정합니다. 그런 다음, `BuildFormDataStart()` 및 `BuildFormDataEnd()`를 호출하여 양식 데이터를 만듭니다.

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. `CONTENT_TYPE_HEADER_PARAMS` 및 양식 데이터 경계를 서식 지정하여 `ContentType` 값을 만듭니다.

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. `BingImageSearch()`를 호출하여 API 응답을 가져온 다음, 응답을 출력합니다.

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>HttpClient 사용

`HttpClient`를 사용하는 경우 `MultipartFormDataContent` 클래스를 사용하여 양식 데이터를 빌드할 수 있습니다. 코드의 다음 섹션을 사용하여 이전 예제의 해당 메서드를 바꿉니다.

1. `Main()` 메서드를 다음 코드로 바꿉니다.

   ```csharp
           static void Main()
           {
               try
               {
                   Console.OutputEncoding = System.Text.Encoding.UTF8;

                   if (accessKey.Length == 32)
                   {
                       if (IsImagePathSet(imagePath))
                       {
                           var filename = GetImageFileName(imagePath);
                           Console.WriteLine("Getting image insights for image: " + filename);
                           var imageBinary = GetImageBinary(imagePath);

                           var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                           var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                           Console.WriteLine("\nJSON Response:\n");
                           Console.WriteLine(JsonPrettyPrint(json));
                       }
                   }
                   else
                   {
                       Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                       Console.WriteLine("Please paste yours into the source code.");
                   }

                   Console.Write("\nPress Enter to exit ");
                   Console.ReadLine();
               }
               catch (Exception e)
               {
                   Console.WriteLine(e.Message);
               }
           }
   ```

2. `BingImageSearch()` 메서드를 다음 코드로 바꿉니다.

   ```csharp
           /// <summary>
           /// Calls the Bing visual search endpoint and returns the JSON response.
           /// </summary>
           static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
           {
               var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
               requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

               var content = new MultipartFormDataContent(boundary);
               content.Add(new ByteArrayContent(image), "image", "myimage");
               requestMessage.Content = content;

               var httpClient = new HttpClient();

               Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
               HttpResponseMessage httpResponse = httpRequest.Result;
               HttpStatusCode statusCode = httpResponse.StatusCode;
               HttpContent responseContent = httpResponse.Content;

               string json = null;

               if (responseContent != null)
               {
                   Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                   json = stringContentsTask.Result;
               }

               return json;
           }
   ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Visual Search 단일 페이지 웹앱 만들기](../tutorial-bing-visual-search-single-page-app.md)
