---
title: '샘플: C#에서 이미지 처리 앱 탐색'
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services의 Computer Vision API를 사용하는 기본 Windows 앱을 탐색합니다. OCR를 수행하고, 썸네일을 만들고, 이미지의 시각적 기능으로 작업합니다.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 522cf801e7c1bfdd3ed1f452c123a2db701c0c42
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936353"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>샘플: C#으로 이미지 처리 앱 탐색

Computer Vision을 사용하는 기본 Windows 애플리케이션을 탐색합니다. 이 프로그램은 OCR(광학 문자 인식)을 수행하고, 스마트하게 자른 썸네일을 만들고, 이미지에서 얼굴을 비롯한 시각적 특징을 감지하고, 분류하고, 태그를 지정하고, 설명합니다. 아래 예제에서는 이미지 URL 또는 로컬로 저장된 파일을 제출할 수 있습니다. .NET Framework의 일부인 WPF(Windows Presentation Foundation) 및 Computer Vision API를 사용하여 고유한 Windows용 앱을 빌드하기 위한 템플릿으로 이 오픈 소스 예제를 사용할 수 있습니다.

> [!div class="checklist"]
> * GitHub에서 샘플 앱 가져오기
> * Visual Studio에서 샘플 앱 열기 및 빌드
> * 샘플 앱 실행 및 다양한 시나리오 수행을 위한 상호 작용
> * 샘플 앱에 포함된 다양한 시나리오 탐색

## <a name="prerequisites"></a>필수 구성 요소

샘플 앱을 탐색하기 전에 다음 필수 조건을 충족하는지 확인합니다.

* [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) 이상이 있어야 합니다.
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="get-the-sample-app"></a>샘플 앱 가져오기

Computer Vision 샘플 앱은 `Microsoft/Cognitive-Vision-Windows` 리포지토리의 GitHub에서 제공됩니다. 또한 이 리포지토리에는 Git 하위 모듈로 `Microsoft/Cognitive-Common-Windows` 리포지토리가 포함됩니다. 하위 모듈을 비롯한 이 리포지토리는 명령줄에서 `git clone --recurse-submodules` 명령을 사용하거나 GitHub 데스크톱을 사용하여 재귀적으로 복제할 수 있습니다.

예를 들어, 명령 프롬프트에서 Computer Vision 샘플 앱에 대한 리포지토리를 재귀적으로 복제하려면 다음 명령을 실행합니다.

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> 이 리포지토리를 ZIP으로 다운로드하지 마세요. ZIP으로 리포지토리를 다운로드하는 경우 Git에 하위 모듈이 포함되지 않습니다.

### <a name="get-optional-sample-images"></a>선택적 샘플 이미지 가져오기

필요에 따라 `Microsoft/Cognitive-Face-Windows` 리포지토리의 GitHub에서 제공되는 [Face](../../Face/Overview.md) 샘플 앱에 포함된 샘플 이미지를 사용할 수 있습니다. 해당 샘플 앱에는 사람의 여러 이미지가 들어있는 `/Data` 폴더가 포함되어 있습니다. Computer Vision 샘플 앱에 대해 설명된 메서드로도 이 리포지토리를 재귀적으로 복제할 수 있습니다.

예를 들어, 명령 프롬프트에서 Face 샘플 앱에 대한 리포지토리를 재귀적으로 복제하려면 다음 명령을 실행합니다.

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Visual Studio에서 샘플 앱 열기 및 빌드

샘플 앱을 실행하거나 탐색하기 전에 Visual Studio가 종속성을 확인할 수 있도록 먼저 샘플 앱을 빌드해야 합니다. 샘플 앱을 열고 빌드하려면 다음 단계를 수행합니다.

1. Visual Studio에서 Visual Studio 솔루션 파일 `/Sample-WPF/VisionAPI-WPF-Samples.sln`을 엽니다.
1. Visual Studio 솔루션에 2개의 프로젝트가 있는지 확인합니다.  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   SampleUserControlLibrary 프로젝트를 사용할 수 없는 경우 `Microsoft/Cognitive-Vision-Windows` 리포지토리를 재귀적으로 복제했는지 확인합니다.
1. Visual Studio에서 Ctrl + Shift + B 키를 누르거나 리본 메뉴에서 **빌드**를 선택한 다음, **솔루션 빌드**를 선택하여 솔루션을 빌드합니다.

## <a name="run-and-interact-with-the-sample-app"></a>샘플 앱 실행 및 상호 작용

샘플 앱을 실행하면 썸네일을 만들거나 이미지에 태그를 설정하는 등의 다양한 작업을 수행할 때 Computer Vision 클라이언트 라이브러리 및 사용자와 어떻게 상호 작용하는지 확인할 수 있습니다. 샘플 앱을 실행하고 상호 작용하려면 다음 단계를 수행합니다.

1. 빌드가 완료된 후 **F5**를 누르거나 리본 메뉴에서 **디버그**를 선택한 다음, **디버깅 시작**을 선택하여 샘플 앱을 실행합니다.
1. 샘플 앱이 표시되면 탐색 창에서 **구독 키 관리**를 선택하여 구독 키 관리 페이지를 표시합니다.
   ![구독 키 관리 페이지](../Images/Vision_UI_Subscription.PNG)  
1. **구독 키**에 구독 키를 입력합니다.
1. **엔드포인트**에 엔드포인트 URL을 입력합니다.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 다음에 샘플 앱을 실행할 때 구독 키 및 엔드포인트 URL을 입력하지 않도록 하려면 **설정 저장**을 선택하여 구독 키 및 엔드포인트 URL을 사용자 컴퓨터에 저장합니다. 이전에 저장된 구독 키 및 엔드포인트 URL을 삭제하려면 **설정 삭제**를 선택합니다.

   > [!NOTE]
   > 샘플 앱은 구독 키 및 엔드포인트 URL을 저장하는 데 격리된 스토리지 및 `System.IO.IsolatedStorage`를 사용합니다.

1. 탐색 창의 **시나리오 선택**에서 현재 샘플 앱에 포함된 시나리오 중 하나를 선택합니다.  

   | 시나리오 | Description |
   |----------|-------------|
   |이미지 분석 | [이미지 분석](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) 작업을 사용하여 로컬 또는 원격 이미지를 분석합니다. 분석에 사용할 시각적 기능 및 언어를 선택하고 이미지 및 결과를 모두 볼 수 있습니다.  |
   |도메인 모델을 사용한 이미지 분석 | [도메인 특정 모델 나열](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) 작업을 사용하여 선택할 수 있는 도메인 모델을 나열하고, [도메인 특정 콘텐츠 인식](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) 작업을 사용하여 선택한 도메인 모델을 통해 로컬 또는 원격 이미지를 분석합니다. 또한 분석에 사용할 언어를 선택할 수 있습니다. |
   |이미지 설명 | [이미지 설명](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) 작업을 사용하여 로컬 또는 원격 이미지에 대한 알기 쉬운 설명을 만듭니다. 또한 설명에 사용할 언어를 선택할 수 있습니다. |
   |태그 생성 | [이미지 태그](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) 작업을 사용하여 로컬 또는 원격 이미지의 시각적 기능에 태그를 지정합니다. 또한 태그에 사용되는 언어를 선택할 수 있습니다. |
   |텍스트 인식(OCR) | [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) 작업을 사용하여 이미지에서 인쇄된 텍스트를 인식 및 추출합니다. 사용할 언어를 사용자가 선택하거나 Computer Vision에서 자동으로 언어를 검색하도록 할 수 있습니다. |
   |텍스트 인식 V2(영어) | [텍스트 인식](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) 및 [텍스트 인식 작업 결과 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) 작업을 사용하여 이미지에서 인쇄되거나 필기한 텍스트를 비동기적으로 인식하고 추출합니다. |
   |썸네일 가져오기 | [썸네일 가져오기](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) 작업을 사용하여 로컬 또는 원격 이미지에 대한 썸네일을 생성합니다. |

   다음 스크린샷은 샘플 이미지를 분석한 후 이미지 분석 시나리오에 제공된 페이지를 보여줍니다.
   ![이미지 분석 페이지의 스크린샷](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>샘플 앱 탐색

Computer Vision 샘플 앱에 대한 Visual Studio 솔루션에는 두 개의 프로젝트가 포함됩니다.

* SampleUserControlLibrary  
  SampleUserControlLibrary 프로젝트는 여러 Cognitive Services 샘플에서 공유되는 기능을 제공합니다. 프로젝트에는 다음이 포함됩니다.
  * SampleScenarios  
    샘플에 대한 제목 표시줄, 탐색 창 및 콘텐츠 창과 같은 표준화된 프레젠테이션을 제공하는 UserControl입니다. Computer Vision 샘플 앱은 MainWindow.xaml 창에서 이 컨트롤을 사용하여 시나리오 페이지를 표시하고 구독 키 및 엔드포인트 URL과 같은 시나리오 간 공유되는 정보에 액세스합니다.
  * SubscriptionKeyPage  
    샘플 앱에 대한 구독 키 및 엔드포인트 URL을 입력하기 위해 표준화된 레이아웃을 제공하는 페이지입니다. Computer Vision 샘플 앱은 이 페이지를 사용하여 시나리오 페이지에서 사용되는 구독 키 및 엔드포인트 URL을 관리합니다.
  * VideoResultControl  
    동영상 정보에 대한 표준화된 프레젠테이션을 제공하는 UserControl입니다. Computer Vision 샘플 앱은 이 컨트롤을 사용하지 않습니다.
* VisionAPI-WPF-Samples  
  Computer Vision 샘플 앱에 대한 기본 프로젝트인 이 프로젝트에는 Computer Vision에 대한 모든 흥미로운 기능이 포함되어 있습니다. 프로젝트에는 다음이 포함됩니다.
  * AnalyzeInDomainPage.xaml  
    도메인 모델을 사용한 이미지 분석 시나리오를 위한 시나리오 페이지입니다.
  * AnalyzeImage.xaml  
    이미지 분석 시나리오를 위한 시나리오 페이지입니다.
  * DescribePage.xaml  
    이미지 설명 시나리오를 위한 시나리오 페이지입니다.
  * ImageScenarioPage.cs  
    샘플 앱의 모든 시나리오 페이지가 파생되는 ImageScenarioPage 클래스입니다. 이 클래스는 자격 증명을 제공하고 출력 서식을 지정하는 등 모든 시나리오 페이지에서 공유되는 기능을 관리합니다.
  * MainWindow.xaml  
    샘플 앱에 대한 주 창은 SubscriptionKeyPage 및 시나리오 페이지를 표시하기 위해 SampleScenarios 컨트롤을 사용합니다.
  * OCRPage.xaml  
    텍스트 인식(OCR) 시나리오에 대한 시나리오 페이지입니다.
  * RecognizeLanguage.cs  
    샘플 앱에서 다양한 메서드에 의해 지원되는 언어에 대한 정보를 제공하는 RecognizeLanguage 클래스입니다.
  * TagsPage.xaml  
    태그 생성 시나리오에 대한 시나리오 페이지입니다.
  * TextRecognitionPage.xaml  
    텍스트 인식 V2(영어) 시나리오에 대한 시나리오 페이지입니다.
  * ThumbnailPage.xaml  
    썸네일 가져오기 시나리오에 대한 시나리오 페이지입니다.

### <a name="explore-the-sample-code"></a>샘플 코드 탐색

샘플 코드의 주요 부분은 샘플 앱을 더 손쉽게 살펴볼 수 있도록 `KEY SAMPLE CODE STARTS HERE`로 시작하고 `KEY SAMPLE CODE ENDS HERE`로 끝나는 주석 블록으로 묶여 있습니다. 샘플 코드의 이러한 주요 부분에는 다양한 작업을 수행하기 위해 Computer Vision API 클라이언트 라이브러리를 사용하는 방법을 학습하는 것과 가장 관련성이 높은 코드가 포함되어 있습니다. Visual Studio에서 `KEY SAMPLE CODE STARTS HERE`를 검색하여 Computer Vision 샘플 앱에서 코드의 가장 관련성이 높은 섹션 사이를 이동할 수 있습니다. 

예를 들어 다음을 표시하고 AnalyzePage.xaml에 포함된 `UploadAndAnalyzeImageAsync` 메서드는 `ComputerVisionClient.AnalyzeImageInStreamAsync` 메서드를 호출하여 로컬 이미지를 분석하기 위해 클라이언트 라이브러리를 사용하는 방법을 설명합니다.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>클라이언트 라이브러리 탐색

이 예제 앱은 Azure Cognitive Services의 Computer Vision API용 씬 C# 클라이언트 래퍼인 Computer Vision API 클라이언트 라이브러리를 사용합니다. 클라이언트 라이브러리는 [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) 패키지의 NuGet에서 제공됩니다. Visual Studio 애플리케이션을 빌드할 때 해당하는 NuGet 패키지에서 클라이언트 라이브러리를 검색했습니다. 또한 `Microsoft/Cognitive-Vision-Windows` 리포지토리의 `/ClientLibrary` 폴더에서 클라이언트 라이브러리에 대한 소스 코드를 볼 수 있습니다.

클라이언트 라이브러리의 기능은 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` 네임스페이스의 `ComputerVisionClient` 클래스를 중심으로 하며, Computer Vision과 상호 작용할 때 `ComputerVisionClient` 클래스에서 사용되는 모델은 `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` 네임스페이스에 있습니다. 샘플 앱에 포함된 다양한 XAML 시나리오 페이지에서 해당 네임스페이스에 대한 다음 `using` 지시문을 확인할 수 있습니다.

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Computer Vision 샘플 앱에 포함된 시나리오를 살펴보면서 `ComputerVisionClient` 클래스에 포함된 다양한 메서드에 대해 자세히 알아보세요.

## <a name="explore-the-analyze-image-scenario"></a>이미지 분석 시나리오 탐색

이 시나리오는 AnalyzePage.xaml 페이지에서 관리됩니다. 분석에 사용할 시각적 기능 및 언어를 선택하고 이미지 및 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드 중 하나를 사용하여 이를 수행합니다.

* UploadAndAnalyzeImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `ComputerVisionClient.AnalyzeImageInStreamAsync` 메서드를 호출하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* AnalyzeUrlAsync  
  이 메서드는 이미지에 대한 URL을 `ComputerVisionClient.AnalyzeImageAsync` 메서드를 호출하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.

`UploadAndAnalyzeImageAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 샘플 앱은 로컬 이미지를 분석하기 때문에 Computer Vision으로 해당 이미지의 콘텐츠를 전송해야 합니다. `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 연 다음, 시나리오 페이지에서 선택한 시각적 기능 및 언어를 가져옵니다. 파일에 대한 `Stream`, 시각적 기능 및 언어를 전달하는 `ComputerVisionClient.AnalyzeImageInStreamAsync` 메서드를 호출한 다음, 결과를 `ImageAnalysis` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`AnalyzeUrlAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 시나리오 페이지에서 선택한 시각적 기능 및 언어를 가져옵니다. 이미지 URL, 시각적 기능 및 언어를 전달하는 `ComputerVisionClient.AnalyzeImageInStreamAsync` 메서드를 호출한 다음, 결과를 `ImageAnalysis` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>도메인 모델을 사용한 이미지 분석 시나리오 탐색

이 시나리오는 AnalyzeInDomainPage.xaml 페이지에서 관리됩니다. `celebrities` 또는 `landmarks`와 같은 도메인 모델 및 언어를 선택하여 이미지의 도메인 특정 분석을 수행하고 이미지 및 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드를 사용합니다.

* GetAvailableDomainModelsAsync  
  이 메서드는 Computer Vision에서 사용 가능한 도메인 모델의 목록을 가져오고, `ComputerVisionClient.ListModelsAsync` 메서드를 사용하여 페이지의 `_domainModelComboBox` ComboBox 컨트롤을 채웁니다.
* UploadAndAnalyzeInDomainImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` 메서드를 호출하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* AnalyzeInDomainUrlAsync  
  이 메서드는 이미지에 대한 URL을 `ComputerVisionClient.AnalyzeImageByDomainAsync` 메서드를 호출하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.

`UploadAndAnalyzeInDomainImageAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 샘플 앱은 로컬 이미지를 분석하기 때문에 Computer Vision으로 해당 이미지의 콘텐츠를 전송해야 합니다. `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 연 다음, 시나리오 페이지에서 선택한 언어를 가져옵니다. 파일에 대한 `Stream`, 도메인 모델의 이름 및 언어를 전달하는 `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` 메서드를 호출한 다음, 결과를 `DomainModelResults` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`AnalyzeInDomainUrlAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 시나리오 페이지에서 선택한 언어를 가져옵니다. 이미지 URL, 시각적 기능 및 언어를 전달하는 `ComputerVisionClient.AnalyzeImageByDomainAsync` 메서드를 호출한 다음, 결과를 `DomainModelResults` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

## <a name="explore-the-describe-image-scenario"></a>이미지 설명 시나리오 탐색

이 시나리오는 DescribePage.xaml 페이지에서 관리됩니다. 언어를 선택하여 알기 쉬운 이미지 설명을 만들고 이미지와 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드를 사용합니다.

* UploadAndDescribeImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `ComputerVisionClient.DescribeImageInStreamAsync` 메서드를 호출하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* DescribeUrlAsync  
  이 메서드는 이미지에 대한 URL을 `ComputerVisionClient.DescribeImageAsync` 메서드를 호출하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.

`UploadAndDescribeImageAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 샘플 앱은 로컬 이미지를 분석하기 때문에 Computer Vision으로 해당 이미지의 콘텐츠를 전송해야 합니다. `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 연 다음, 시나리오 페이지에서 선택한 언어를 가져옵니다. 파일에 대한 `Stream`, 최대 후보 수(이 경우 3) 및 언어를 전달하는 `ComputerVisionClient.DescribeImageInStreamAsync` 메서드를 호출한 다음, 결과를 `ImageDescription` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`DescribeUrlAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 시나리오 페이지에서 선택한 언어를 가져옵니다. 이미지 URL, 최대 후보 수(이 경우 3) 및 언어를 전달하는 `ComputerVisionClient.DescribeImageAsync` 메서드를 호출한 다음, 결과를 `ImageDescription` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

## <a name="explore-the-generate-tags-scenario"></a>태그 생성 시나리오 탐색

이 시나리오는 TagsPage.xaml 페이지에서 관리됩니다. 이미지의 시각적 기능에 태그를 적용할 언어를 선택하고 이미지 및 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드를 사용합니다.

* UploadAndGetTagsForImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `ComputerVisionClient.TagImageInStreamAsync` 메서드를 호출하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* GenerateTagsForUrlAsync  
  이 메서드는 이미지에 대한 URL을 `ComputerVisionClient.TagImageAsync` 메서드를 호출하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.

`UploadAndGetTagsForImageAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 샘플 앱은 로컬 이미지를 분석하기 때문에 Computer Vision으로 해당 이미지의 콘텐츠를 전송해야 합니다. `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 연 다음, 시나리오 페이지에서 선택한 언어를 가져옵니다. 파일에 대한 `Stream` 및 언어를 전달하는 `ComputerVisionClient.TagImageInStreamAsync` 메서드를 호출한 다음, 결과를 `TagResult` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`GenerateTagsForUrlAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 시나리오 페이지에서 선택한 언어를 가져옵니다. 이미지 URL 및 언어를 전달하는 `ComputerVisionClient.TagImageAsync` 메서드를 호출한 다음, 결과를 `TagResult` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

## <a name="explore-the-recognize-text-ocr-scenario"></a>텍스트 인식(OCR) 시나리오 탐색

이 시나리오는 OCRPage.xaml 페이지에서 관리됩니다. 이미지의 인쇄된 텍스트를 인식 및 추출할 언어를 선택하고 이미지 및 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드를 사용합니다.

* UploadAndRecognizeImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` 메서드를 호출하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* RecognizeUrlAsync  
  이 메서드는 이미지에 대한 URL을 `ComputerVisionClient.RecognizePrintedTextAsync` 메서드를 호출하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.

`UploadAndRecognizeImageAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 샘플 앱은 로컬 이미지를 분석하기 때문에 Computer Vision으로 해당 이미지의 콘텐츠를 전송해야 합니다. `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 연 다음, 시나리오 페이지에서 선택한 언어를 가져옵니다. 해당 방향이 감지되지 않았음을 나타내며 파일에 대한 `Stream` 및 언어를 전달하는 `ComputerVisionClient.RecognizePrintedTextInStreamAsync` 메서드를 호출한 다음, 결과를 `OcrResult` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`RecognizeUrlAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 시나리오 페이지에서 선택한 언어를 가져옵니다. 해당 방향이 감지되지 않았음을 나타내며 이미지 URL 및 언어를 전달하는 `ComputerVisionClient.RecognizePrintedTextAsync` 메서드를 호출한 다음, 결과를 `OcrResult` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>텍스트 인식 V2(영어) 시나리오 탐색

이 시나리오는 TextRecognitionPage.xaml 페이지에서 관리됩니다. 인식 모드 및 언어를 선택하여 이미지의 인쇄되거나 필기된 텍스트를 비동기적으로 인식하고 추출하며, 이미지와 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드를 사용합니다.

* UploadAndRecognizeImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `RecognizeAsync` 메서드를 호출하고 `ComputerVisionClient.RecognizeTextInStreamAsync` 메서드에 대해 매개 변수가 있는 대리자를 전달하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* RecognizeUrlAsync  
  이 메서드는 이미지에 대한 URL을 `RecognizeAsync` 메서드를 호출하고 `ComputerVisionClient.RecognizeTextAsync` 메서드에 대해 매개 변수가 있는 대리자를 전달하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.
* RecognizeAsync 이 메서드는 `UploadAndRecognizeImageAsync` 및 `RecognizeUrlAsync` 메서드 모두에 대한 비동기 호출을 처리하면서 동시에 `ComputerVisionClient.GetTextOperationResultAsync` 메서드를 호출하여 결과를 폴링합니다.

Computer Vision 샘플 앱에 포함된 다른 시나리오와 달리, 이 시나리오는 비동기로, 프로세스를 시작하기 위해 하나의 메서드가 호출되면 상태를 확인하고 해당 프로세스의 결과를 반환하는 데에는 다른 메서드가 호출됩니다. 이 시나리오의 논리 흐름은 다른 시나리오의 그것과는 약간 다릅니다.

`UploadAndRecognizeImageAsync` 메서드는 `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 연 다음, `RecognizeAsync` 메서드를 호출하여 다음을 전달합니다.

* `GetHeadersAsyncFunc`에서 매개 변수로 파일 및 인식 모드에 대한 `Stream`을 사용하는 `ComputerVisionClient.RecognizeTextInStreamAsync` 메서드의 매개 변수가 있는 비동기 대리자에 대한 람다 식.
* `GetOperationUrlFunc`에서 `Operation-Location` 응답 헤더를 가져오는 대리자에 대한 람다 식.

`RecognizeUrlAsync` 메서드는 `RecognizeAsync` 메서드를 호출하여 다음을 전달합니다.

* `GetHeadersAsyncFunc`에서 매개 변수로 원격 이미지의 URL 및 인식 모드를 사용하는 `ComputerVisionClient.RecognizeTextAsync` 메서드의 매개 변수가 있는 비동기 대리자에 대한 람다 식.
* `GetOperationUrlFunc`에서 `Operation-Location` 응답 헤더를 가져오는 대리자에 대한 람다 식.

`RecognizeAsync` 메서드가 완료되면 `UploadAndRecognizeImageAsync` 및 `RecognizeUrlAsync` 메서드 모두 결과를 `TextOperationResult` 인스턴스로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`RecognizeAsync` 메서드는 `GetHeadersAsyncFunc`에 전달된 `ComputerVisionClient.RecognizeTextInStreamAsync` 또는 `ComputerVisionClient.RecognizeTextAsync` 메서드 중 하나에 대한 매개 변수가 있는 대리자를 호출하고 응답을 기다립니다. 그런 다음, 메서드는 `GetOperationUrlFunc`에 전달된 대리자를 호출하여 응답에서 `Operation-Location` 응답 헤더 값을 가져옵니다. 이 값은 Computer Vision에서 `GetHeadersAsyncFunc`에 전달된 메서드의 결과 검색하는 데 사용되는 URL입니다.

그런 다음, `RecognizeAsync` 메서드는 `ComputerVisionClient.GetTextOperationResultAsync` 메서드를 호출하여 `Operation-Location` 응답 헤더에서 검색된 URL을 전달하며, `GetHeadersAsyncFunc`에 전달된 메서드의 결과 및 상태를 가져옵니다. 메서드의 완료, 성공 또는 실패 상태가 표시되지 않으면 `RecognizeAsync` 메서드는 `ComputerVisionClient.GetTextOperationResultAsync`를 3회 이상 호출하고 호출 간 3초 동안 대기합니다. `RecognizeAsync` 메서드를 이를 호출하는 메서드로 결과를 반환합니다.

## <a name="explore-the-get-thumbnail-scenario"></a>썸네일 가져오기 시나리오 탐색

이 시나리오는 ThumbnailPage.xaml 페이지에서 관리됩니다. 이미지에서 썸네일을 만드는 데 스마트 자르기를 사용하고 원하는 높이 및 너비를 지정할지 여부를 표시하고, 이미지와 결과를 모두 볼 수 있습니다. 시나리오 페이지는 이미지의 원본에 따라 다음 메서드를 사용합니다.

* UploadAndThumbnailImageAsync  
  이 메서드는 이미지를 `Stream`으로 인코딩해야 하며 `ComputerVisionClient.GenerateThumbnailInStreamAsync` 메서드를 호출하여 Computer Vision으로 전송하는 로컬 이미지에 사용됩니다.
* ThumbnailUrlAsync  
  이 메서드는 이미지에 대한 URL을 `ComputerVisionClient.GenerateThumbnailAsync` 메서드를 호출하여 Computer Vision으로 전송하는 원격 이미지에 사용됩니다.

`UploadAndThumbnailImageAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. 샘플 앱은 로컬 이미지를 분석하기 때문에 Computer Vision으로 해당 이미지의 콘텐츠를 전송해야 합니다. `Stream`으로 읽도록 `imageFilePath`에 지정된 로컬 파일을 엽니다. `ComputerVisionClient.GenerateThumbnailInStreamAsync` 메서드를 호출하여 너비, 높이, 파일에 대한 `Stream` 및 스마트 자르기 사용 여부를 전달한 다음, 결과를 `Stream`으로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

`RecognizeUrlAsync` 메서드는 지정된 구독 키 및 엔드포인트 URL을 사용하여 새로운 `ComputerVisionClient` 인스턴스를 만듭니다. `ComputerVisionClient.GenerateThumbnailAsync` 메서드를 호출하여 너비, 높이, 이미지에 대한 URL 및 스마트 자르기 사용 여부를 전달한 다음, 결과를 `Stream`으로 반환합니다. `ImageScenarioPage` 클래스에서 상속된 메서드는 시나리오 페이지에서 반환된 결과를 표시합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 `Microsoft/Cognitive-Vision-Windows` 리포지토리를 복제한 폴더를 삭제합니다. 샘플 이미지를 사용하기로 선택한 경우에도 `Microsoft/Cognitive-Face-Windows` 리포지토리를 복제한 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Face 서비스 시작](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
