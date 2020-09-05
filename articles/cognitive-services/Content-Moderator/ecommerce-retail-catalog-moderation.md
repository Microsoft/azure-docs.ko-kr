---
title: '자습서: 전자 상거래 제품 이미지 조정 - Content Moderator'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 지정된 레이블을 통해 제품 이미지를 분석하고 분류하는 애플리케이션을 설정하는 방법을 보여줍니다(Azure Computer Vision 및 Custom Vision 사용). 추가 검토가 필요한 문제가 될만한 이미지에는 태그를 지정합니다(Azure Content Moderator 사용).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 017dabf16384e53d924ed69f36b64050fcacb5bf
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934789"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>자습서: Azure Content Moderator를 사용하여 전자 상거래 제품 이미지 조정

이 자습서에서는 Content Moderator를 포함한 Azure Cognitive Services를 사용하여 전자 상거래 시나리오를 위한 제품 이미지를 분류하고 조정하는 방법에 대해 알아봅니다. Computer Vision 및 Custom Vision을 사용하여 태그(레이블)를 이미지에 적용한 다음, Content Moderator의 기계 학습 기반 기술을 사용자 검토 팀과 결합하여 인텔리전트 조정 시스템을 제공하는 팀 검토를 만듭니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Content Moderator에 가입하고 검토 팀을 만듭니다.
> * Content Moderator의 이미지 API를 사용하여 잠재적인 성인 및 외설 콘텐츠를 검사합니다.
> * Computer Vision 서비스를 사용하여 유명인 콘텐츠(또는 기타 Computer-Vision 검색 가능 태그)를 검사합니다.
> * Custom Vision 서비스를 사용하여 국기, 장난감 및 펜(또는 기타 사용자 지정 태그)의 존재 여부를 검사합니다.
> * 사용자 검토 및 최종 의사 결정을 위해 결합된 검사 결과를 표시합니다.

전체 예제 코드는 GitHub의 [전자 상거래 카탈로그 조정 샘플](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) 리포지토리에서 받을 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- Content Moderator 구독 키. [Cognitive Services 계정 만들기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)의 지침에 따라 Content Moderator 서비스를 구독하고 키를 가져옵니다.
- Computer Vision 구독 키(위와 동일한 지침)
- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전.
- Custom Vision 분류자에서 사용할 각 레이블의 이미지 세트(이 경우 장난감, 펜 및 미국 국기)

## <a name="create-a-review-team"></a>검토 팀 만들기

[Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/)에 가입하고 검토 팀을 만드는 방법에 대한 지침은 [웹에서 Content Moderator 사용해 보기](quick-start.md) 빠른 시작을 참조하세요. **자격 증명** 페이지에서 **팀 ID** 값을 기록해 둡니다.

## <a name="create-custom-moderation-tags"></a>사용자 지정 조정 태그 만들기

다음으로, 검토 도구에서 사용자 지정 태그를 만듭니다. (이 과정에 대한 도움이 필요하면 [태그](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) 문서를 참조하세요.) 이 경우 **celebrity**, **USA**, **flag**, **toy** 및 **pen** 태그를 추가합니다. 모든 태그가 Computer Vision에서 검색 가능한 범주일 필요는 없습니다(예: **celebrity**). Custom Vision 분류자를 학습하기만 한다면 나중에 검색하도록 고유의 사용자 지정 태그를 추가할 수 있습니다.

![사용자 지정 태그 구성](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio에서 [새 프로젝트] 대화 상자를 엽니다. **설치된 항목**, **Visual C#** 을 차례로 확장한 다음, **콘솔 앱(.NET Framework)** 를 선택합니다.
1. 애플리케이션 이름을 **EcommerceModeration**으로 지정하고 **확인**을 클릭합니다.
1. 이 프로젝트를 기존 솔루션에 추가하는 경우 이 프로젝트를 단일 시작 프로젝트로 선택합니다.

이 자습서에서는 프로젝트의 핵심 코드만 강조하며 모든 코드 행을 다루지는 않습니다. 샘플 프로젝트([전자 상거래 카탈로그 조정 샘플](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration))에서 _Program.cs_의 전체 내용을 새 프로젝트의 _Program.cs_ 파일에 복사합니다. 그런 다음, 다음 섹션을 단계별로 실행하여 프로젝트가 작동하는 방법과 직접 사용하는 방법을 알아봅니다.

## <a name="define-api-keys-and-endpoints"></a>API 키 및 엔드포인트 정의

이 자습서에서는 세 가지 Cognitive Services를 사용합니다. 따라서 해당하는 세 개의 키와 API 엔드포인트가 필요합니다. **Program** 클래스에 다음 필드를 확인합니다.

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

`___Key` 필드를 구독 키의 값으로 업데이트해야 하고 `___Uri` 필드를 올바른 엔드포인트 URL로 변경해야 합니다(나중에 Custom Vision 키 및 엔드포인트를 가져옴). 각 Azure 리소스의 **빠른 시작** 탭에서 이러한 값을 찾을 수 있습니다. `ReviewUri` 필드의 `YOURTEAMID` 부분에 이전에 작성한 검토 팀의 ID를 입력합니다. `CustomVisionUri` 필드의 마지막 부분은 나중에 입력합니다.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>기본 메서드 호출

**Main** 메서드에서 이미지 URL 목록을 반복하는 다음 코드를 확인합니다. 세 가지 서로 다른 서비스로 각 이미지를 분석하고, 적용된 태그를 **ReviewTags** 배열에 기록한 다음, Content Moderator 검토 도구에 이미지를 전송하여 조정자 역할을 맡은 사람에 대한 검토를 만듭니다. 이러한 메서드에 대해서는 다음 섹션에서 살펴봅니다. 원하는 경우, 적용된 태그를 확인하는 조건문에 **ReviewTags** 배열을 사용하여 검토를 위해 전송할 이미지를 제어할 수 있습니다.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy 메서드

**Program** 클래스의 **EvaluateAdultRacy** 메서드를 살펴봅니다. 이 메서드는 이미지 URL 및 키-값 쌍의 배열을 매개 변수로 사용합니다. Content Moderator의 이미지 API를 호출(REST 사용)하여 이미지의 성인 및 외설 점수를 가져옵니다. 점수가 0.4보다 큰 경우(범위는 0에서 1 사이임) **ReviewTags** 배열의 해당 값을 **True**로 설정합니다.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>EvaluateComputerVisionTags 메서드

다음 메서드는 이미지 URL 및 Computer Vision 구독 정보를 사용하고 이미지에서 유명인 존재 여부를 분석합니다. 유명인이 한 명 이상 발견되면 **ReviewTags** 배열의 해당 값을 **True**로 설정합니다.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags 메서드

다음으로, 실제 제품(이 경우 국기, 장난감 및 펜)을 분류하는 **EvaluateCustomVisionTags** 메서드를 살펴봅니다.&mdash; [분류자를 작성하는 방법](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) 가이드의 지침에 따라 고유한 사용자 지정 이미지 분류자를 작성하여 국기, 장난감 및 펜(또는 사용자 지정 태그로 선택한 항목)을 이미지에서 감지할 수 있습니다. [GitHub 리포지토리](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)의 **sample-images** 폴더에 있는 이미지를 사용하여 이 예제의 일부 범주를 신속하게 학습할 수 있습니다.

![펜, 장난감 및 국기 학습 이미지가 있는 Custom Vision 웹 페이지](images/tutorial-ecommerce-custom-vision.PNG)

분류자를 학습했으면 예측 키와 예측 엔드포인트 URL(검색하는 데 도움이 필요한 경우 [URL 및 예측 키 가져오기](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key) 참조)을 가져오고 이러한 값을 `CustomVisionKey` 및 `CustomVisionUri`에 각각 할당합니다. 이 메서드는 이러한 값을 사용하여 분류자를 쿼리합니다. 분류자가 이미지에서 하나 이상의 사용자 지정 태그를 찾는 경우 이 메서드는 **ReviewTags** 배열의 해당 값을 **True**로 설정합니다.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>검토 도구용 검토 만들기

이전 섹션에서 앱이 성인 및 외설 콘텐츠(Content Moderator), 유명인(Computer Vision) 및 다양한 기타 개체(Computer Vision)에 대해 들어오는 이미지를 검사하는 메서드를 살펴보았습니다. 다음으로, 적용된 모든 태그(_Metadata_로 전달됨)와 함께 이미지를 Content Moderator 검토 도구에 업로드하는 **CreateReview** 메서드를 살펴봅니다.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

이미지가 [Content Moderator 검토 도구](https://contentmoderator.cognitive.microsoft.com/)의 [검토] 탭에 표시됩니다.

![여러 이미지와 강조 표시된 태그가 있는 Content Moderator 검토 도구 스크린샷](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>테스트 이미지 목록 제출

**Main** 메서드에서 볼 수 있듯이 이 프로그램은 “C:Test” 디렉터리에서 이미지 Url 목록을 포함하는 _Urls.txt_ 파일을 검색합니다. 이 파일과 디렉터리를 만들거나 텍스트 파일을 가리키도록 경로를 변경합니다. 그런 다음, 테스트할 이미지의 URL로 이 파일을 채웁니다.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>프로그램 실행

위의 모든 단계를 수행했으면 프로그램이 각 이미지를 처리(관련 태그에 대해 세 가지 서비스를 모두 쿼리)한 다음, 태그 정보가 있는 이미지를 Content Moderator 검토 도구에 업로드합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 제품 이미지를 분석하고 제품 유형별로 태그를 지정하여 이러한 정보를 근거로 검토 팀이 콘텐츠 조정에 대한 합리적인 의사 결정을 내릴 수 있는 프로그램을 설정합니다. 다음으로, 이미지 조정에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [조정된 이미지 검토](./review-tool-user-guide/review-moderated-images.md)
