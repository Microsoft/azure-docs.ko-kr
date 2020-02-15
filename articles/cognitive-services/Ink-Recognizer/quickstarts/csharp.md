---
title: '빠른 시작: Ink Recognizer REST API와 C#으로 디지털 잉크 인식'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서 Ink Recognizer API를 사용하여 디지털 잉크 스트로크 인식을 시작하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 1cf519d8d8c25877b49bf14aefd1c0be3afa8023
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137888"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>빠른 시작: Ink Recognizer REST API와 C#으로 디지털 잉크 인식

이 빠른 시작을 사용하여 디지털 잉크 스트로크를 Ink Recognizer API에 보내기 시작합니다. 이 C# 애플리케이션은 JSON 형식의 잉크 스트로크 데이터가 포함된 API 요청을 보내고 응답을 받습니다.

이 애플리케이션은 C#으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

일반적으로 API는 디지털 잉크 입력 앱에서 호출합니다. 이 빠른 시작은 JSON 파일에서 다음 필기 샘플에 대한 잉크 스트로크 데이터를 보냅니다.

![필기 문자 이미지](../media/handwriting-sample.jpg)

이 빠른 시작의 소스 코드는 [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502)에서 확인할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)의 모든 버전.
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Visual Studio에서 Newtonsoft.Json을 NuGet 패키지로 설치하려면 다음을 수행합니다.
        1. **솔루션 관리자**를 마우스 오른쪽 단추로 클릭합니다.
        2. **NuGet 패키지 관리...**
        3. `Newtonsoft.Json`을 검색하고 패키지를 설치합니다.
- Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.

- 이 빠른 시작의 잉크 스트로크 데이터 예제는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json)에서 찾을 수 있습니다.

### <a name="create-an-ink-recognizer-resource"></a>Ink Recognizer 리소스 만들기

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>새 애플리케이션 만들기

1. Visual Studio에서 새 콘솔 솔루션을 만들고 다음 패키지를 추가합니다. 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. 구독 키, 엔드포인트 및 예제 JSON 파일에 대한 변수를 만듭니다. 엔드포인트는 나중에 `inkRecognitionUrl`과 결합되어 API에 액세스합니다. 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>요청을 보내는 함수 만들기

1. 위에서 만든 변수를 사용하는 `Request`라는 새 비동기 함수를 만듭니다.

2. `HttpClient` 개체를 사용하여 클라이언트의 보안 프로토콜과 헤더 정보를 설정합니다. `Ocp-Apim-Subscription-Key` 헤더에 구독 키를 추가해야 합니다. 그런 다음, 요청에 대한 `StringContent` 개체를 만듭니다.
 
3. `PutAsync()`를 통해 요청을 보냅니다. 요청이 성공 하면 응답을 반환합니다.  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>잉크 인식 요청 보내기

1. `recognizeInk()`라는 새 함수를 만듭니다. 요청을 구성하고 엔드포인트, 구독 키, API용 URL 및 디지털 잉크 스트로크 데이터와 함께 `Request()` 함수를 호출하여 보냅니다.

2. JSON 개체를 역직렬화하여 콘솔에 작성합니다. 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>디지털 잉크 데이터 로드

`LoadJson()`이라는 함수를 만들어서 잉크 데이터 JSON 파일을 로드합니다. `StreamReader` 및 `JsonTextReader`를 사용하여 `JObject`를 만들고 반환합니다.

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>API 요청 보내기

1. 애플리케이션의 main 메서드에서 위에서 만든 함수를 사용하여 JSON 데이터를 로드합니다. 

2. 위에서 만든 `recognizeInk()` 함수를 호출합니다. `System.Console.ReadKey()`를 사용하여 애플리케이션을 실행한 후 콘솔 창을 열어 둡니다.
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>애플리케이션 실행 및 응답 보기

애플리케이션을 실행합니다. 성공 응답이 JSON 형식으로 반환됩니다. 또한 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json)에서 JSON 응답을 찾을 수 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [REST API 참조](https://go.microsoft.com/fwlink/?linkid=2089907)


Ink Recognizer API가 디지털 잉크 입력 앱에서 어떻게 작동하는지 알아보려면 GitHub에서 다음 샘플 애플리케이션을 살펴보세요.
* [C# 및 UWP(유니버설 Windows 플랫폼)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# 및 WPF(Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Javascript 웹 브라우저 앱](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java 및 Android 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift 및 iOS 모바일 앱](https://go.microsoft.com/fwlink/?linkid=2089805)
