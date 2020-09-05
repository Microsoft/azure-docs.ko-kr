---
title: C#에서 REST 호출을 사용하여 모델 가져오기
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/03/2020
ms.author: diberry
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c03e403db7157a0b009278f0e343799e179e34c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931399"
---
[참조 설명서](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)

## <a name="prerequisites"></a>필수 구성 요소

* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>예제 발언 JSON 파일

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Pizza 앱 만들기

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>프로그래밍 방식으로 모델 변경

1. `csharp-model-with-rest`의 프로젝트 및 폴더 이름을 사용하여 C# 언어를 대상으로 하는 새 콘솔 애플리케이션을 만듭니다.

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. 생성한 `csharp-model-with-rest` 디렉터리로 변경하고 다음 명령을 사용하여 필요한 종속성을 설치합니다.

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. Program.cs를 다음 코드로 덮어씁니다.

    [!code-csharp[Code snippet](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)]

1. `YOUR-`에서 시작하는 값을 고유한 값으로 바꿉니다.

    |정보|목적|
    |--|--|
    |`YOUR-APP-ID`| LUIS 앱 ID입니다. |
    |`YOUR-AUTHORING-KEY`|32자 작성 키입니다.|
    |`YOUR-AUTHORING-ENDPOINT`| 작성 URL 엔드포인트입니다. 예들 들어 `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`입니다. 리소스를 만들 때 리소스 이름을 설정합니다.|

    할당된 키와 리소스는 **Azure Resources** 페이지의 관리 섹션에 있는 LUIS 포털에 표시됩니다. 앱 ID는 **애플리케이션 설정** 페이지의 동일한 관리 섹션에서 사용할 수 있습니다.

1. 콘솔 애플리케이션을 빌드합니다.

    ```console
    dotnet build
    ```

1. 콘솔 애플리케이션을 실행합니다.

    ```console
    dotnet run
    ```

1. 제작 응답을 검토합니다.

    ```console
    Added utterances.
    [
        {
            "value": {
                "ExampleId": 1137150691,
                "UtteranceText": "order a pizza"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 1137150692,
                "UtteranceText": "order a large pepperoni pizza"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 1137150693,
                "UtteranceText": "i want two large pepperoni pizzas on thin crust"
            },
            "hasError": false
        }
    ]
    Sent training request.
    {
        "statusId": 9,
        "status": "Queued"
    }
    Requested training status.
    [
        {
            "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        }
    ]
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작을 완료하면 파일 시스템에서 파일을 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱 모범 사례](../luis-concept-best-practices.md)
