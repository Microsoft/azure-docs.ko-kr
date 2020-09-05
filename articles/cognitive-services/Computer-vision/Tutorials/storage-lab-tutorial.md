---
title: '자습서: Azure 이미지에 대한 메타데이터 생성'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 Azure Computer Vision 서비스를 웹앱에 통합하여 이미지에 대한 메타데이터를 생성하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: f14bcc1b0576a5ad2f4dbbf30311f613607d3ee5
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322333"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>자습서: Computer Vision을 사용하여 Azure Storage에서 이미지 메타데이터 생성

이 자습서에서는 Azure Computer Vision 서비스를 웹앱에 통합하여 업로드된 이미지에 대한 메타데이터를 생성하는 방법에 대해 알아봅니다. 이 기능은 회사가 모든 이미지에 대한 설명 캡션 또는 검색 가능한 키워드를 빠르게 생성하려는 경우와 같이 [DAM(디지털 자산 관리)](../overview.md#computer-vision-for-digital-asset-management) 시나리오에 유용합니다.

전체 앱 가이드는 GitHub의 [Azure Storage 및 Cognitive Services 랩](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md)에서 찾을 수 있으며, 이 자습서에서는 기본적으로 랩의 연습 5를 다룹니다. 모든 단계를 수행하여 전체 애플리케이션을 만들 수 있지만 Computer Vision을 기존 웹앱에 통합하는 방법만 알아보려면 여기를 참조하세요.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Azure에서 Computer Vision 리소스 만들기
> * Azure Storage 이미지에서 이미지 분석 수행
> * Azure Storage 이미지에 메타데이터 연결
> * Azure Storage Explorer를 사용하여 이미지 메타데이터 확인

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2017 Community 버전](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) 이상("ASP.NET 및 웹 개발" 및 "Azure 개발" 워크로드가 설치되어 있음)
- 이미지 스토리지에 설정된 Blob 컨테이너가 있는 Azure Storage 계정(이 단계와 관련하여 도움이 필요한 경우 [Azure Storage 랩의 연습 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) 수행)
- Azure Storage Explorer 도구(이 단계에서 도움이 필요한 경우 [Azure Storage 랩의 연습 2](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) 수행)
- Azure Storage에 액세스할 수 있는 ASP.NET 웹 애플리케이션([Azure Storage 랩의 연습 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3)에 따라 앱을 빠르게 만듦)

## <a name="create-a-computer-vision-resource"></a>Computer Vision 리소스 만들기

Azure 계정에 대한 Computer Vision 리소스를 만들어야 합니다. 이 리소스는 Azure의 Computer Vision 서비스에 대한 액세스를 관리합니다. 

1. [Azure Cognitive Services 리소스 만들기](../../cognitive-services-apis-create-account.md)의 지침에 따라 Computer Vision 리소스를 만듭니다.

1. 그런 다음, 리소스 그룹의 메뉴로 이동하여 방금 만든 Computer Vision API 구독을 클릭합니다. **엔드포인트** 아래의 URL을 잠시 후에 쉽게 검색할 수 있는 위치에 복사합니다. 그런 다음, **액세스 키 표시**를 클릭합니다.

    ![설명된 엔드포인트 URL 및 액세스 키 링크가 있는 Azure Portal 페이지](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. 다음 창에서 **키 1**의 값을 클립보드에 복사합니다.

    ![키 관리 대화 상자(설명된 복사 단추 포함)](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Computer Vision 자격 증명 추가

다음으로, Computer Vision 리소스에 액세스할 수 있도록 필요한 자격 증명을 앱에 추가합니다.

Visual Studio에서 ASP.NET 웹 애플리케이션을 열고 프로젝트의 루트에 있는 **Web.config** 파일로 이동합니다. 다음 명령문을 파일의 `<appSettings>` 섹션에 추가하고, `VISION_KEY`를 이전 단계에서 복사한 키로 바꾸고, `VISION_ENDPOINT`를 이전 단계에서 저장한 URL로 바꿉니다.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

그런 다음, 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, **NuGet 패키지 관리** 명령을 사용하여 **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** 패키지를 설치합니다. 이 패키지에는 Computer Vision API를 호출하는 데 필요한 형식이 포함되어 있습니다.

## <a name="add-metadata-generation-code"></a>메타데이터 생성 코드 추가

다음으로, Computer Vision 서비스를 실제로 활용하여 이미지에 대한 메타데이터를 만드는 코드를 추가합니다. 이 단계는 랩의 ASP.NET 앱에 적용되지만 사용자 고유의 앱에도 적용할 수 있습니다. 중요한 점은 이 시점에서 이미지를 Azure Storage 컨테이너에 업로드하고, 여기에서 이미지를 읽고, 보기에 이를 표시할 수 있는 ASP.NET 웹 애플리케이션이 있다는 것입니다. 이 단계를 잘 모르는 경우 [Azure Storage 랩의 연습 3](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3)을 수행하는 것이 좋습니다. 

1. 프로젝트의 **Controllers** 폴더에서 *HomeController.cs* 파일을 열고, 파일의 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. 다음으로, **Upload** 메서드로 이동합니다. 이 메서드는 이미지를 변환하고 Blob 스토리지에 업로드합니다. `// Generate a thumbnail`로 시작하는 블록 바로 뒤(또는 image-blob-creation 프로세스의 끝)에 다음 코드를 추가합니다. 이 코드는 이미지(`photo`)가 포함된 Blob을 가져오고, Computer Vision을 사용하여 해당 이미지에 대한 설명을 생성합니다. 또한 Computer Vision API에서 이미지에 적용되는 키워드 목록도 생성합니다. 생성된 설명과 키워드는 나중에 검색할 수 있도록 Blob의 메타데이터에 저장됩니다.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. 다음으로, 동일한 파일에서 **Index** 메서드로 이동합니다. 이 메서드는 대상 Blob 컨테이너(**IListBlobItem** 인스턴스)에 저장된 이미지 Blob을 열거하고, 이를 애플리케이션 보기로 전달합니다. 이 메서드의 `foreach` 블록을 다음 코드로 바꿉니다. 이 코드는 **CloudBlockBlob.FetchAttributes**를 호출하여 각 Blob의 연결된 메타데이터를 가져옵니다. 컴퓨터에서 생성된 메타데이터의 설명(`caption`)을 추출하여 보기에 전달되는 **BlobInfo** 개체에 추가합니다.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>앱 테스트

Visual Studio에서 변경 내용을 저장하고 **Ctrl+F5**를 눌러 브라우저에서 애플리케이션을 시작합니다. 이 앱을 사용하여 랩의 리소스에 있는 "photos" 폴더 또는 사용자 고유의 폴더에서 몇 개의 이미지를 업로드합니다. 보기의 이미지 위를 커서로 가리키면 도구 설명 창이 나타나고 컴퓨터에서 생성된 이미지에 대한 캡션이 표시됩니다.

![컴퓨터에서 생성된 캡션](../Images/thumbnail-with-tooltip.png)

연결된 모든 메타데이터를 보려면 Azure Storage Explorer를 사용하여 이미지에 사용하고 있는 스토리지 컨테이너를 봅니다. 컨테이너에서 임의의 Blob을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 대화 상자에서 키-값 쌍 목록이 표시됩니다. 컴퓨터에서 생성된 이미지 설명은 "캡션" 항목에 저장되고, 검색 키워드는 "Tag0", "Tag1" 등에 저장됩니다. 완료되면 **취소**를 클릭하여 대화 상자를 닫습니다.

![메타데이터 태그가 나열된 이미지 속성 대화 상자 창](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>리소스 정리

웹앱에서 계속 작업하려면 [다음 단계](#next-steps) 섹션을 참조하세요. 이 애플리케이션을 계속 사용하지 않으려면 모든 앱 관련 리소스를 삭제해야 합니다. 리소스를 삭제하려면 단순히 Azure Storage 구독 및 Computer Vision 리소스가 포함된 리소스 그룹을 삭제할 수 있습니다. 그러면 스토리지 계정, 이 계정에 업로드된 Blob 및 ASP.NET 웹앱에 연결하는 데 필요한 App Service 리소스가 제거됩니다. 

리소스 그룹을 삭제하려면 포털에서 **리소스 그룹** 탭을 열고, 이 프로젝트에 사용한 리소스 그룹으로 이동한 다음, 보기의 위쪽에서 **리소스 그룹 삭제**를 클릭합니다. 리소스 그룹이 삭제되면 복구할 수 없으므로 삭제할 것인지를 확인하기 위해 리소스 그룹의 이름을 입력하라는 메시지가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 기존 웹앱에서 Azure의 Computer Vision 서비스를 설정하여 업로드되는 Blob 이미지에 대한 캡션과 키워드를 자동으로 생성했습니다. 다음으로, Azure Storage 랩의 연습 6을 참조하여 웹앱에 검색 기능을 추가하는 방법을 알아봅니다. 여기서는 Computer Vision 서비스에서 생성하는 검색 키워드를 활용합니다.

> [!div class="nextstepaction"]
> [앱에 검색 추가](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
