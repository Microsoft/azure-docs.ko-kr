---
title: 개체 탐지기 빌드 빠른 시작 - Custom Vision Service
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Custom Vision 웹 사이트를 사용하여 이미지 분류 모델을 만드는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 57fb91e9ead98f6c52fd3f659f4aa12692816297
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549550"
---
# <a name="quickstart-how-to-build-an-object-detector-with-custom-vision"></a>빠른 시작: Custom Vision을 사용하여 개체 탐지기를 빌드하는 방법

이 빠른 시작에서는 Custom Vision 웹 사이트를 통해 개체 탐지기를 빌드하는 방법에 대해 알아봅니다. 탐지기 모델이 작성되면 Custom Vision Service를 사용하여 개체를 감지할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- 탐지기 모델을 학습시키는 데 사용할 이미지 세트. [샘플 이미지](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) 세트는 GitHub에서 사용할 수 있습니다. 또는 아래 팁을 사용하여 사용자 고유의 이미지를 선택할 수 있습니다.

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>Azure Portal에서 Custom Vision 리소스 만들기

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>새 프로젝트 만들기

웹 브라우저에서 [Custom Vision 웹 페이지](https://customvision.ai)로 이동하여 __로그인__을 선택합니다. Azure Portal에 로그인하는 데 사용한 계정과 동일한 계정으로 로그인합니다.

![로그인 페이지 이미지](./media/browser-home.png)


1. 첫 번째 프로젝트를 만들려면 **새 프로젝트**를 선택합니다. **새 프로젝트 만들기** 대화 상자가 표시됩니다.

    ![새 프로젝트 대화 상자에는 이름, 설명 및 도메인 필드가 있습니다.](./media/get-started-build-detector/new-project.png)

1. 프로젝트에 대한 이름과 설명을 입력합니다. 그런 다음 리소스 그룹을 선택합니다. 로그인한 계정이 Azure 계정과 연결되어 있으면 리소스 그룹 드롭다운에는 Custom Vision Service 리소스를 포함하는 모든 Azure 리소스 그룹이 표시됩니다. 

   > [!NOTE]
   > 사용 가능한 리소스 그룹이 없는 경우 [Azure Portal](https://portal.azure.com/)에 로그인하는 데 사용한 계정과 동일한 계정으로 [customvision.ai](https://customvision.ai)에 로그인했는지 확인합니다. 또한 Custom Vision 포털에서 Custom Vision 리소스가 있는 Azure Portal의 디렉터리와 동일한 "디렉터리"를 선택했는지 확인하세요. 두 사이트 모두, 화면 오른쪽 위에 있는 드롭다운 계정 메뉴에서 디렉터리를 선택할 수 있습니다. 

1. __프로젝트 형식__ 아래에서 __개체 감지__를 선택합니다.

1. 다음으로 사용 가능한 도메인 중 하나를 선택합니다. 각 도메인은 다음 표에서 설명한 대로 특정 이미지 형식에 맞게 탐지기를 최적화합니다. 원하는 경우 나중에 도메인을 변경할 수 있습니다.

    |도메인|목적|
    |---|---|
    |__일반__| 광범위한 개체 감지 작업에 맞게 최적화됩니다. 다른 도메인이 적절하지 않거나 선택할 도메인을 잘 모르겠으면 일반 도메인을 선택합니다. |
    |__로고__|이미지에서 브랜드 로고를 찾도록 최적화됩니다.|
    |__선반 위의 제품__|선반에서 제품을 검색하고 분류하는 데 최적화되어 있습니다.|
    |__압축 도메인__| 모바일 디바이스의 실시간 개체 감지 제약 조건에 맞게 최적화됩니다. 압축 도메인에서 생성된 모델을 로컬에서 실행하기 위해 내보낼 수 있습니다.|

1. 마지막으로 __프로젝트 만들기__를 선택합니다.

## <a name="choose-training-images"></a>학습 이미지 선택

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

이 섹션에서는 탐지기를 학습시키는 데 도움이 되는 이미지를 업로드하고 수동으로 태그를 지정합니다. 

1. 이미지를 추가하려면 __이미지 추가__ 단추를 클릭하고 __로컬 파일 찾아보기__를 선택합니다. __열기__를 선택하여 이미지를 업로드합니다.

    ![이미지 추가 컨트롤은 왼쪽 위와 맨 아래 가운데 단추로 표시됩니다.](./media/get-started-build-detector/add-images.png)

1. 업로드된 이미지는 UI의 **태그 없음** 섹션에 표시됩니다. 다음 단계에서는 탐지기에서 인식할 수 있도록 학습시키려는 개체에 수동으로 태그를 지정합니다. 첫 번째 이미지를 클릭하여 태그 지정 대화 상자 창을 엽니다. 

    ![태그 없음 섹션에서 업로드된 이미지](./media/get-started-build-detector/images-untagged.png)

1. 사각형을 클릭하여 이미지의 개체 주위에 끌어 놓습니다. 그런 다음, **+** 단추를 사용하여 새 태그 이름을 입력하거나 드롭다운 목록에서 기존 태그를 선택합니다. 탐지기는 학습에서 태그가 지정되지 않은 배경 영역을 부정적인 예제로 사용하므로 감지하려는 개체의 모든 인스턴스에 태그를 지정하는 것이 매우 중요합니다. 태그 지정이 완료되면 오른쪽의 화살표를 클릭하여 태그를 저장하고 다음 이미지로 이동합니다.

    ![사각형 선택 영역을 사용하여 개체 태그 지정](./media/get-started-build-detector/image-tagging.png)

다른 이미지 세트를 업로드하려면 이 섹션의 맨 위로 돌아가서 해당 단계를 반복합니다.

## <a name="train-the-detector"></a>탐지기 학습

탐지기 모델을 학습시키려면 **학습** 단추를 선택합니다. 탐지기는 현재 이미지와 해당 태그를 모두 사용하여 태그가 지정된 각 개체를 식별하는 모델을 만듭니다.

![웹 페이지 헤더 도구 모음 오른쪽 상단의 학습 단추](./media/getting-started-build-a-classifier/train01.png)

학습 프로세스는 몇 분밖에 안 걸립니다. 이 시간 동안에는 학습 프로세스에 대한 정보가 **성능** 탭에 표시됩니다.

![주 섹션에 학습 대화 상자가 표시된 브라우저 창](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>탐지기 평가

학습이 완료되면 모델의 성능이 계산되어 표시됩니다. Custom Vision Service는 학습을 위해 제출한 이미지를 사용하여 정밀도, 재현율 및 평균 정밀도를 계산합니다. 정밀도와 재현율은 탐지기의 효율성을 나타내는 별개의 두 가지 측정값입니다.

- **정밀도**는 정확한 것으로 식별된 분류 부분을 나타냅니다. 예를 들어 모델에서 100개 이미지를 강아지로 식별했는데 그중 99개가 실제로 강아지 이미지였다면 정밀도는 99%입니다.
- **재현율**은 정확하게 식별된 실제 분류 부분을 나타냅니다. 예를 들어 실제 사과 이미지가 100개인데 모델이 80개를 사과로 식별했다면 재현율은 80%입니다.

![학습 결과는 전체적인 정밀도와 재현율 및 평균 정밀도를 보여 줍니다.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>확률 임계값

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>학습 반복 관리

탐지기를 학습시킬 때마다 업데이트된 자체 성능 메트릭을 사용하여 새 _반복_을 만듭니다. **성능** 탭의 왼쪽 창에서 모든 반복을 확인할 수 있습니다. 왼쪽 창에는 사용하지 않는 반복을 삭제하는 데 사용할 수 있는 **삭제** 단추도 있습니다. 반복을 삭제하면 해당 반복에 고유하게 연결된 이미지도 모두 삭제됩니다.

학습된 모델에 프로그래밍 방식으로 액세스하는 방법을 알아보려면 [예측 API에서 모델 사용](./use-prediction-api.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Custom Vision 웹 사이트를 사용하여 개체 탐지기 모델을 만들고 학습시키는 방법을 알아보았습니다. 다음으로는 모델을 개선하는 반복 프로세스에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](test-your-model.md)

