---
title: '빠른 시작: .NET용 Form Recognizer 클라이언트 라이브러리'
description: 이 빠른 시작을 통해 .NET용 Form Recognizer 클라이언트 라이브러리를 시작하여 구조화된 데이터 출력을 학습, 추출, 분석 및 가져올 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: b9db9daf06b59e1a26a9b03a93aff63984841862
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118355"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>빠른 시작: .NET용 Form Recognizer 클라이언트 라이브러리

.NET용 Form Recognizer 클라이언트 라이브러리를 시작합니다. Form Recognizer는 기계 학습 기술을 사용하여 양식 문서에서 키/값 쌍과 테이블 데이터를 식별하고 추출하는 Cognitive Service입니다. 그런 다음, 원본 파일의 관계가 포함된 정형 데이터를 출력합니다. 다음 단계에 따라 SDK 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 보세요.

.NET용 Form Recognizer 클라이언트 라이브러리를 사용하여 수행하는 작업은 다음과 같습니다.

* [사용자 지정 Form Recognizer 모델 학습](#train-a-custom-model)
* [추출된 키 목록 가져오기](#get-a-list-of-extracted-keys)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 목록 가져오기](#get-a-list-of-custom-models)
* [사용자 지정 모델 삭제](#delete-a-custom-model)

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/FormRecognizer) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 학습 데이터 세트가 포함된 Azure Storage Blob. 학습 데이터를 결합하는 옵션 및 팁에 대한 자세한 내용은 [사용자 지정 모델에 대한 학습 데이터 집합 빌드](../build-training-data-set.md)를 참조하세요. 이 빠른 시작에서는 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Train** 폴더에 있는 파일을 사용할 수 있습니다.
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="create-a-form-recognizer-azure-resource"></a>Form Recognizer Azure 리소스 만들기

[!INCLUDE [create resource](../includes/create-resource.md)]

평가판 구독 또는 리소스에서 키를 가져온 후에는 각각 `FORM_RECOGNIZER_KEY` 및 `FORM_RECOGNIZER_ENDPOINT`라는 키 및 엔드포인트에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `formrecognizer-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 _Program.cs_라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```console
dotnet new console -n formrecognizer-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 그러면 다음을 사용하여 애플리케이션을 빌드합니다.

```console
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

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 _Program.cs_ 파일을 엽니다. 다음 `using` 문을 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_using)]

그런 다음, 다음 코드를 애플리케이션의 **Main** 메서드에 추가합니다. 이 비동기 작업은 나중에 정의합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Form Recognizer 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

## <a name="object-model"></a>개체 모델

Form Recognizer SDK의 주요 기능을 처리하는 클래스는 다음과 같습니다.

|속성|Description|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|이 클래스는 모든 Form Recognizer 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| 이 클래스는 사용자 고유의 학습 입력 데이터를 사용하여 사용자 지정 Form Recognizer 모델을 학습시키는 데 사용합니다. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| 이 클래스는 모델 ID를 포함하여 사용자 지정 모델 학습 작업의 결과를 제공하며, 양식을 분석하는 데 사용할 수 있습니다. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| 이 클래스는 사용자 지정 모델 분석 작업의 결과를 제공합니다. **ExtractedPage** 인스턴스 목록이 포함됩니다. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| 이 클래스는 단일 양식 문서에서 추출한 모든 데이터를 나타냅니다.|

## <a name="code-examples"></a>코드 예제

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

여기에 나와 있는 코드 조각에서는 .NET용 Form Recognizer 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [사용자 지정 Form Recognizer 모델 학습](#train-a-custom-model)
* [추출된 키 목록 가져오기](#get-a-list-of-extracted-keys)
* [사용자 지정 모델을 사용하여 양식 분석](#analyze-forms-with-a-custom-model)
* [사용자 지정 모델 목록 가져오기](#get-a-list-of-custom-models)
* [사용자 지정 모델 삭제](#delete-a-custom-model)

## <a name="define-variables"></a>변수 정의

메서드를 정의하기 전에 다음 변수 정의를 **Program** 클래스의 맨 위에 추가합니다. 변수 중 일부는 직접 입력해야 합니다. 

* 학습 데이터에 대한 SAS URL를 검색하려면 Microsoft Azure Storage Explorer를 열고, 마우스 오른쪽 단추로 컨테이너를 클릭하고, **공유 액세스 서명 가져오기**를 선택합니다. **읽기** 권한과 **목록 사용** 권한이 선택되어 있는지 확인하고 **만들기**를 클릭합니다. 그런 다음 **URL** 섹션의 값을 복사합니다. `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` 형식이어야 합니다.
* 분석할 샘플 양식이 필요한 경우 [샘플 데이터 세트](https://go.microsoft.com/fwlink/?linkid=2090451)의 **Tes** 폴더에 있는 파일 중 하나를 사용할 수 있습니다. 이 가이드에서는 PDF 양식만 사용합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

## <a name="authenticate-the-client"></a>클라이언트 인증

`Main`에서 참조되는 작업을 `Main` 메서드 아래에 정의합니다. 여기서는 위에서 정의한 구독 변수를 사용하여 클라이언트 개체를 인증합니다. 다른 메서드는 나중에 정의할 수 있습니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

## <a name="train-a-custom-model"></a>사용자 지정 모델 학습

다음 메서드는 Form Recognizer 클라이언트 개체를 사용하여 Azure Blob 컨테이너에 저장된 문서에 대한 새 인식 모델을 학습시킵니다. 도우미 메서드를 사용하여 새로 학습된 모델에 대한 정보([ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview) 개체로 표시됨)를 표시하고 모델 ID를 반환합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

다음 도우미 메서드는 Form Recognizer 모델에 대한 정보를 표시합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

## <a name="get-a-list-of-extracted-keys"></a>추출된 키 목록 가져오기

학습이 완료되면 사용자 지정 모델은 학습 문서에서 추출된 키 목록을 유지합니다. 사용자 지정 모델은 이러한 키를 포함하는 이후 양식 문서를 예상하여 분석 작업에서 해당 값을 추출합니다. 다음 메서드를 사용하여 추출된 키 목록을 검색하고 콘솔에 출력하세요. 이 학습 프로세스가 효과적인지 확인하는 좋은 방법입니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getkeys)]

## <a name="analyze-forms-with-a-custom-model"></a>사용자 지정 모델을 사용하여 양식 분석

다음 메서드는 Form Recognizer 클라이언트와 모델 ID를 사용하여 PDF 양식 문서를 분석하고 키/값 데이터를 추출합니다. 도우미 메서드를 사용하여 결과([AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview) 개체로 표시됨)를 표시합니다.

> [!NOTE]
> 다음 메서드는 PDF 양식을 분석합니다. JPEG 및 PNG 양식을 분석하는 유사한 메서드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)의 전체 샘플 코드를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

다음 도우미 메서드는 분석 작업에 대한 정보를 표시합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

## <a name="get-a-list-of-custom-models"></a>사용자 지정 모델 목록 가져오기

계정에 속한 모든 학습된 모델의 목록을 반환하고 만들어진 시간에 대한 정보를 검색할 수 있습니다. 모델 목록은 [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview) 개체로 표현됩니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="delete-a-custom-model"></a>사용자 지정 모델 삭제

계정에서 사용자 지정 모델을 삭제하려는 경우 다음 메서드를 사용합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_deletemodel)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 호출하여 애플리케이션을 실행합니다.

```console
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

또한 계정에서 삭제하려는 사용자 지정 모델을 학습시킨 경우 [사용자 지정 모델 삭제](#delete-a-custom-model)의 메서드를 실행합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Form Recognizer .NET 클라이언트 라이브러리를 사용하여 사용자 지정 모델을 학습시키고 양식을 분석했습니다. 다음으로, 더 나은 학습 데이터 세트를 만들고 더 정확한 모델을 생성하기 위한 팁에 대해 알아봅니다.

> [!div class="nextstepaction"]
>[학습 데이터 세트 빌드](../build-training-data-set.md)

* [Form Recognizer란?](../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer)에서 확인할 수 있습니다.
