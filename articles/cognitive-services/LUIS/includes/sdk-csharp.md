---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/17/2021
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: 43a4cf24cd2305be560ee5f5bd4b763995e340e0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110164844"
---
.NET용 LUIS(Language Understanding) 클라이언트 라이브러리를 사용하여 다음을 수행합니다.
* 앱 만들기
* 예제 발화를 사용하여 기계 학습된 엔터티인 의도 추가
* 앱 학습 및 게시
* 예측 런타임 쿼리

[참조 설명서](/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding) | [작성](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring) 및 [예측](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) 라이브러리 소스 코드 | [작성](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/) 및 [예측](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) NuGet | [C# 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>사전 요구 사항

* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 및 [.NET Core CLI](/dotnet/core/tools/).
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* Azure 구독을 보유한 후에는 Azure Portal에서 [언어 이해 작성 리소스를 만들어](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 키와 엔드포인트를 가져옵니다. 배포될 때까지 기다렸다가 **리소스로 이동** 단추를 클릭합니다.
    * 애플리케이션을 언어 이해 작성에 연결하려면 [만든](../luis-how-to-azure-subscription.md) 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다. 무료 가격 책정 계층(`F0`)을 사용하여 서비스를 시도할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 dotnet `new` 명령을 사용하여 `language-understanding-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 `Program.cs`라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. 새로 만든 앱 폴더로 디렉터리를 변경합니다.

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

    ```dotnetcli
    dotnet build
    ```

    빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

    ```console
    ...
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ...
    ```

### <a name="install-the-nuget-libraries"></a>NuGet 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 LUIS(Language Understanding) 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>개체 모델 제작

LUIS(Language Understanding) 작성 클라이언트는 Azure를 인증하는 [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient) 개체이며, 이 개체에는 작성 키가 포함되어 있습니다.

## <a name="code-examples-for-authoring"></a>작성에 대한 코드 예제

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* 앱- [만들기](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync), [삭제](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync), [게시](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync)
* 발화 예제 - [추가](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions), [ID로 삭제](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync)
* 기능 - [구 목록](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync) 관리
* 모델 - [의도](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions) 및 엔터티 관리
* 패턴 - [패턴](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions) 관리
* 학습 - 앱 [학습](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync) 및 [학습 상태](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync) 폴링
* [버전](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions) - 복제, 내보내기 및 삭제를 사용하여 관리

## <a name="prediction-object-model"></a>예측 개체 모델

LUIS(Language Understanding) 예측 런타임 클라이언트는 Azure를 인증하는 [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient) 개체이며, 리소스 키를 포함합니다.

## <a name="code-examples-for-prediction-runtime"></a>예측 런타임에 대한 코드 예제

클라이언트가 만들어지면 이 클라이언트를 사용하여 다음과 같은 기능에 액세스합니다.

* [스테이징 또는 제품 슬롯](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync)별 예측
* [버전](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync)별 예측


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>종속성 추가

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 기존 `using` 코드를 다음 `using` 지시문으로 바꿉니다.

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>상용구 코드 추가

1. 비동기 호출을 허용하도록 `Main` 메서드의 서명을 변경합니다.

    ```csharp
    public static async Task Main()
    ```

1. 달리 지정하지 않는 한 `Program` 클래스의 `Main` 메서드에 나머지 코드를 추가합니다.

## <a name="create-variables-for-the-app"></a>앱에 대한 변수 만들기

두 개의 변수 세트를 만듭니다. 첫 번째 집합은 변경하고 두 번째 집합은 코드 샘플에 표시된 그대로 둡니다. 

1. 작성 키 및 리소스 이름을 저장할 변수를 만듭니다.

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. 엔드포인트, 앱 이름, 버전 및 의도 이름을 저장할 변수를 만듭니다.

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>클라이언트 인증

키를 사용하여 [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials) 개체를 만들고, 엔드포인트에서 이를 사용하여 [LUISAuthoringClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient) 개체를 만듭니다.

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>LUIS 앱 만들기

LUIS 앱에는 의도, 엔터티 및 발화 예제를 비롯한 NLP(자연어 처리) 모델이 포함되어 있습니다.

[ApplicationCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject)를 만듭니다. 이름과 언어 문화권은 필수 속성입니다. [Apps.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync) 메서드를 호출합니다. 응답은 앱 ID입니다.

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>앱용 의도 만들기
LUIS 앱 모델의 기본 개체는 의도입니다. 의도는 사용자 발화 _의도_ 의 그룹화를 통해 정렬됩니다. 사용자는 봇(또는 다른 클라이언트 애플리케이션)에서 _의도된_ 특정 응답을 찾는 질문을 하거나 명령문을 만들 수 있습니다. 의도의 예로 항공편 예약, 목적지 도시의 날씨 요청 및 고객 서비스에 대한 연락처 정보 요청이 있습니다.

고유한 의도 이름을 사용하여 [ModelCreateObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject)를 만든 다음, 앱 ID, 버전 ID 및 ModelCreateObject를 [Model.AddIntentAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync) 메서드에 전달합니다. 응답은 의도 ID입니다.

`intentName` 값은 [앱에 대한 변수 만들기](#create-variables-for-the-app) 섹션에 있는 변수의 일부로 `OrderPizzaIntent`에 하드 코딩됩니다.

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>앱용 엔터티 만들기

엔터티는 필수는 아니지만 대부분의 앱에서 찾을 수 있습니다. 엔터티는 사용자의 의도를 채우는 데 필요한 정보를 사용자 발화에서 추출합니다. [미리 작성된](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync) 엔터티 및 사용자 지정 엔터티의 여러 형식이 있으며, 각 엔터티는 고유한 DTO(데이터 변환 개체) 모델을 사용합니다.  앱에 추가할 수 있는 일반적인 미리 작성된 엔터티에는 [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md), [ordinal](../luis-reference-prebuilt-ordinal.md)이 있습니다.

엔터티는 의도에 표시되지 않는다는 것을 알고 있어야 합니다. 대부분의 의도에 적용할 수 있고 일반적으로 적용하고 있습니다. 사용자 발화 예제만 특정 단일 의도에 대해 표시됩니다.

엔터티를 만드는 메서드는 [Model](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions) 클래스의 일부입니다. 각 엔터티 형식에는 자체의 고유한 DTO(데이터 변환 개체) 모델이 있으며, 일반적으로 [Models](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models) 네임스페이스에서 `model` 단어가 포함됩니다.

엔터티 생성 코드는 하위 엔터티 및 `Quantity` 하위 엔터티에 적용되는 기능을 사용하여 기계 학습 엔터티를 만듭니다.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="생성된 엔터티, 하위 엔터티 및 기능이 `Quantity` 하위 엔터티에 적용되는 기계 학습 엔터티를 보여주는 포털의 부분 스크린샷.":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

해당 하위 엔터티에 기능을 할당하려면 클래스에 다음 메서드를 사용하여 Quantity 하위 엔터티의 ID를 찾습니다.

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>의도에 발화 예제 추가

발화의 의도를 파악하고 엔터티를 추출하기 위해 앱에는 발화 예제가 필요합니다. 이러한 예제는 특정 단일 의도를 대상으로 해야 하며, 모든 사용자 지정 엔터티를 표시해야 합니다. 미리 작성된 엔터티는 표시할 필요가 없습니다.

각 발화 예제에 대한 하나의 개체인 [ExampleLabelObject](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject) 개체의 목록을 만들어 발화 예제를 추가합니다. 각 예제는 엔터티 이름과 엔터티 값의 이름/값 쌍으로 구성된 사전을 통해 모든 엔터티를 표시해야 합니다. 엔터티 값은 발화 예제의 텍스트에 표시된 것과 정확히 일치해야 합니다.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="포털에서 레이블이 지정된 예제 발화를 보여주는 부분 스크린샷.":::

앱 ID, 버전 ID 및 예제를 사용하여 [Examples.AddAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions)를 호출합니다.

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>앱 학습

모델이 만들어지면 이 버전의 모델에 대해 LUIS 앱을 학습시켜야 합니다. 학습된 모델은 [컨테이너](../luis-container-howto.md)에서 사용하거나 준비 또는 제품 슬롯에 [게시](../luis-how-to-publish-app.md)할 수 있습니다.

[Train.TrainVersionAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions) 메서드에는 앱 ID와 버전 ID가 필요합니다.

이 빠른 시작에서 보여 주는 것과 같은 매우 작은 모델은 매우 빠르게 학습됩니다. 프로덕션 수준 애플리케이션의 경우 앱을 학습시키려면 학습이 성공했는지 여부를 확인하기 위해 [GetStatusAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) 메서드에 대한 폴링 호출이 포함되어야 합니다. 응답은 각 개체에 대한 개별 상태가 있는 [ModelTrainingInfo](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo) 개체의 목록입니다. 모든 개체가 성공해야 학습이 완료된 것으로 간주합니다.

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>프로덕션 슬롯에 앱 게시

[PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync) 메서드를 사용하여 LUIS 앱을 게시합니다. 그러면 현재 학습된 버전이 엔드포인트의 지정된 슬롯에 게시됩니다. 클라이언트 애플리케이션에서 이 엔드포인트를 사용하여 의도 및 엔터티 추출의 예측을 위한 사용자 발화를 보냅니다.

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>예측 런타임 클라이언트 인증

키와 함께 [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials) 개체를 사용하고, 엔드포인트에서 이를 사용하여 [LUISRuntimeClient](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient) 개체를 만듭니다.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>런타임에서 예측 가져하기

다음 코드를 추가하여 예측 런타임에 대한 요청을 만듭니다.

사용자 발화는 [PredictionRequest](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest) 개체의 일부입니다.

**GetSlotPredictionAsync** 메서드는 요청을 처리할 여러 매개 변수(예: app ID, slot name, prediction request 개체)가 필요합니다. verbose, show all intents, log와 같은 다른 옵션은 선택 사항입니다.

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```
