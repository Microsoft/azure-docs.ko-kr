---
title: '빠른 시작: .NET용 이미지 분석 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 이미지 분석 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 03/29/2021
ms.author: pafarley
ms.openlocfilehash: 0af6c97d6179a645b078f2335ff38f48890c42a3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728197"
---
<a name="HOLTop"></a>

이미지 분석 클라이언트 라이브러리를 사용하여 태그, 텍스트 설명, 얼굴, 성인 콘텐츠 등에 대한 이미지를 분석합니다.

[참조 설명서](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Visual Studio를 사용하여 새 .NET Core 애플리케이션을 만듭니다. 

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치 

새 프로젝트를 만든 후 **솔루션 탐색기** 에서 프로젝트 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리** 를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기** 를 선택하고, **시험판 포함** 을 선택하고, `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`를 검색합니다. `7.0.0` 버전, **설치** 를 차례로 선택합니다. 

#### <a name="cli"></a>[CLI](#tab/cli)

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `computer-vision-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs* 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n computer-vision-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

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

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Computer Vision 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 다음 `using` 지시문을 추가합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

애플리케이션의 **Program** 클래스에서, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

> [!IMPORTANT]
> Azure Portal로 이동합니다. **필수 구성 요소** 섹션에서 만든 Computer Vision 리소스가 성공적으로 배포된 경우 **다음 단계** 아래에서 **리소스로 이동** 단추를 클릭합니다. **리소스 관리** 아래에 있는 리소스의 **키 및 엔드포인트** 페이지에서 키 및 엔드포인트를 찾을 수 있습니다. 
>
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 자세한 내용은 Cognitive Services [보안](../../../cognitive-services-security.md) 문서를 참조하세요.

애플리케이션의 `Main` 메서드에서 이 빠른 시작에 사용된 메서드에 대한 호출을 추가합니다. 나중에 만들 것입니다.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

> [!div class="nextstepaction"]
> [클라이언트를 설정했습니다.](?success=set-up-client#object-model) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>개체 모델

이미지 분석 .NET SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 이 클래스를 인스턴스화한 다음, 대부분의 이미지 작업에 사용합니다.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| 이 클래스는 **ComputerVisionClient** 에 대한 추가 메서드를 포함하고 있습니다.|
|[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| 이 열거형은 표준 Analyze(분석) 작업에서 수행할 수 있는 다양한 유형의 이미지 분석을 정의합니다. 필요에 따라 VisualFeatureTypes 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

이 코드 조각은 .NET용 이미지 분석 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 분석](#analyze-an-image)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 Computer Vision 키 및 엔드포인트에 대한 [환경 변수를 만들었다](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)고 가정합니다.

**프로그램** 클래스의 새 메서드에서 엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** 개체를 만들고, 엔드포인트에서 이를 사용하여 **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** 개체를 만듭니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [클라이언트를 인증했습니다.](?success=authenticate-client#analyze-an-image) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="analyze-an-image"></a>이미지 분석

다음 코드는 클라이언트 개체를 사용하여 원격 이미지를 분석하고 결과를 출력하는 `AnalyzeImageUrl` 메서드를 정의합니다. 이 메서드는 텍스트 설명, 분류, 태그 목록, 감지된 얼굴, 성인 콘텐츠 플래그, 기본 색 및 이미지 형식을 반환합니다.

> [!TIP]
> 로컬 이미지를 분석할 수도 있습니다. [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) 메서드(예: **AnalyzeImageInStreamAsync**)를 참조하세요. 또는 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)의 샘플 코드를 참조하세요.

### <a name="set-up-test-image"></a>테스트 이미지 설정

**Program** 클래스에서 분석하려는 이미지의 URL에 대한 참조를 저장합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

### <a name="specify-visual-features"></a>시각적 기능 지정

이미지 분석에 사용할 새 메서드를 정의합니다. 분석에서 추출하려는 시각적 기능을 지정하는 아래 코드를 추가합니다. 전체 목록은 **[VisualFeatureTypes](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)** 열거형을 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

### <a name="call-the-analyze-api"></a>Analyze API 호출

**AnalyzeImageAsync** 메서드는 추출된 모든 정보를 포함하고 있는 **ImageAnalysis** 개체를 반환합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

다음 섹션에서는 이 정보를 자세히 구문 분석하는 방법을 보여줍니다.

다음 코드 블록을 **AnalyzeImageUrl** 메서드에 삽입하여 위에서 요청한 시각적 기능에서 데이터를 구문 분석합니다. 끝에 닫는 대괄호를 추가해야 합니다.

```csharp
}
```

### <a name="get-image-description"></a>이미지 설명 가져오기

다음 코드는 이미지에 대해 생성된 캡션 목록을 가져옵니다. 자세한 내용은 [이미지 설명](../../concept-describing-images.md)을 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>이미지 범주 가져오기

다음 코드는 검색된 이미지 범주를 가져옵니다. 자세한 내용은 [이미지 분류](../../concept-categorizing-images.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>이미지 태그 가져오기

다음 코드는 이미지에서 검색된 범주 세트를 가져옵니다. 자세한 내용은 [콘텐츠 태그](../../concept-tagging-images.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>개체 감지

다음 코드는 이미지의 공통 개체를 검색하여 콘솔에 출력합니다. 자세한 내용은 [개체 감지](../../concept-object-detection.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>브랜드 감지

다음 코드는 이미지의 회사 브랜드 및 로고를 감지하여 콘솔에 출력합니다. 자세한 내용은 [브랜드 감지](../../concept-brand-detection.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>얼굴 감지

다음 코드는 사각형 좌표를 사용하여 이미지에서 검색된 얼굴을 반환하고 얼굴 특성을 선택합니다. 자세한 내용은 [얼굴 감지](../../concept-detecting-faces.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>성인, 외설 또는 폭력 콘텐츠 검색

다음 코드는 이미지에 있는 성인 콘텐츠의 검색된 상태를 출력합니다. 자세한 내용은 [성인, 외설, 폭력 콘텐츠](../../concept-detecting-adult-content.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>이미지 색 구성표 가져오기

다음 코드는 주조색 및 강조 색과 같이 이미지에서 검색된 색 특성을 출력합니다. 자세한 내용은 [색 구성표](../../concept-detecting-color-schemes.md)를 참조하세요.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>도메인 특정 콘텐츠 가져오기

이미지 분석은 특수 모델을 사용하여 이미지에 대한 추가 분석을 수행할 수 있습니다. 자세한 내용은 [도메인 특정 콘텐츠](../../concept-detecting-domain-content.md)를 참조하세요. 

다음 코드는 이미지에서 검색된 유명인에 대한 데이터를 구문 분석합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

다음 코드는 이미지에서 검색된 랜드마크에 대한 데이터를 구문 분석합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>이미지 형식 가져오기

다음 코드는 이미지 형식이 클립 아트인지 아니면 선 그리기인지 여부에 관계없이 이미지&mdash; 형식에 대한 정보를 출력합니다.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

> [!div class="nextstepaction"]
> [이미지를 분석했습니다.](?success=analyze-image#run-the-application) [문제가 발생했습니다.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=analyze-image)



## <a name="run-the-application"></a>애플리케이션 실행

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

IDE 창의 위쪽에서 **디버그** 단추를 클릭하여 애플리케이션을 실행합니다.

#### <a name="cli"></a>[CLI](#tab/cli)

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Image Analysis 클라이언트 라이브러리를 설치하고 기본 이미지 분석 호출을 수행하는 방법을 알아보았습니다. 다음으로, Analyze API 기능에 대해 자세히 알아보세요.


> [!div class="nextstepaction"]
>[Analyze API 호출](../../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [이미지 분석 개요](../../overview-image-analysis.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)에서 확인할 수 있습니다.
