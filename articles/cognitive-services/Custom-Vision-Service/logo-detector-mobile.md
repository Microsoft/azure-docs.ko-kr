---
title: '자습서: 사용자 지정 로고 탐지기를 사용하여 Azure 서비스 인식 - Custom Vision'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 로고 탐지 시나리오의 일부로 Custom Vision을 사용하는 샘플 앱을 단계별로 살펴봅니다. 다른 구성 요소와 함께 Custom Vision을 사용하여 엔드투엔드 애플리케이션을 제공하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 98d94e9544e75f762d4532101a92d14106d6a575
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931797"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>자습서: 카메라 사진에서 Azure 서비스 로고 인식

이 자습서에서는 보다 큰 시나리오의 일부로 Custom Vision을 사용하는 샘플 앱을 살펴봅니다. 모바일 플랫폼용 Xamarin.Forms 앱인 AI Visual Provision 앱은 Azure 서비스 로고의 카메라 사진을 분석하여 실제 서비스를 사용자의 Azure 계정에 배포합니다. 이 문서에서는 다른 구성 요소와 함께 Custom Vision을 사용하여 유용한 엔드투엔드 애플리케이션을 제공하는 방법을 알아봅니다. 전체 앱 시나리오를 직접 실행하거나 설정의 Custom Vision 파트만 완료하고 앱에서 어떻게 사용되는지 살펴볼 수 있습니다.

이 자습서는 다음에 대한 방법을 보여 줍니다.

> [!div class="checklist"]
> - Azure 서비스 로고를 인식하는 사용자 지정 개체 감지기 만들기
> - Azure Computer Vision 및 Custom Vision에 앱 연결
> - 앱에서 Azure 서비스를 배포하는 Azure 서비스 주체 계정 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

- [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)
- Visual Studio용 Xamarin 워크로드([Xamarin 설치](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows) 참조)
- Visual Studio용 iOS 또는 Android 에뮬레이터
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)(선택 사항)

## <a name="get-the-source-code"></a>소스 코드 가져오기

제공된 웹앱을 사용하려면 GitHub의 [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) 리포지토리에서 앱의 소스 코드를 복제 또는 다운로드합니다. Visual Studio에서 *Source/VisualProvision.sln* 파일을 엽니다. 나중에 앱을 실행할 수 있도록 프로젝트 파일 중 일부를 편집합니다.

## <a name="create-an-object-detector"></a>개체 감지기 만들기

[Custom Vision 웹 사이트](https://customvision.ai/)에 로그인하여 새 프로젝트를 만듭니다. 개체 검색 프로젝트를 지정하고 로고 도메인을 사용합니다. 그러면 로고 감지에 최적화된 알고리즘이 서비스에 사용됩니다. 

![Chrome 브라우저의 Custom Vision 웹 사이트에 표시되는 새 프로젝트 창](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

다음으로, Azure 서비스 로고 이미지를 업로드하고 수동으로 태그를 지정하여 로고 감지 알고리즘을 학습합니다. AIVisualProvision 리포지토리에는 사용 가능한 학습 이미지 세트가 포함되어 있습니다. 웹 사이트의 **학습 이미지** 탭에서 **이미지 추가** 단추를 선택합니다. 그런 다음, 리포지토리의 **Documents/Images/Training_DataSet** 폴더로 이동합니다. 각 이미지의 로고에 수동으로 태그를 지정해야 하므로 이 프로젝트만 테스트하려는 경우 이미지의 일부만 업로드하면 됩니다. 사용하려는 각 태그의 인스턴스를 적어도 15개 업로드합니다.

학습 이미지를 업로드한 후에는 화면에서 첫 번째 이미지를 선택합니다. 태그 지정 창이 나타납니다. 각 이미지에서 상자를 그리고 각 로고에 대한 태그를 할당합니다. 

![Custom Vision 웹 사이트에서 로고 태그 지정](media/azure-logo-tutorial/tag-logos.png)

특정 태그 문자열을 사용하도록 앱이 구성됩니다. *Source\VisualProvision\Services\Recognition\RecognitionService.cs* 파일에서 정의를 확인합니다.

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?name=snippet_constants)]

이미지에 태그를 지정한 다음, 오른쪽으로 이동하여 다음 항목에 태그를 지정합니다. 완료했으면 태그 지정 창을 닫습니다.

## <a name="train-the-object-detector"></a>개체 감지기 학습

왼쪽 창에서 **태그** 스위치를 **태그 있음**으로 설정하면 이미지가 표시됩니다. 그런 다음, 페이지 위쪽에서 모델을 학습하는 녹색 단추를 선택합니다. 알고리즘이 새 이미지에서 동일한 태그를 인식하도록 학습합니다. 또한 일부 기존 이미지의 모델을 테스트하여 정확도 점수를 생성합니다.

![Custom Vision 웹 사이트의 [학습 이미지] 탭. 이 스크린샷에서는 [학습] 단추에 윤곽선이 그려져 있음](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>예측 URL 가져오기

모델 학습을 마치면 모델을 앱에 통합할 수 있습니다. 엔드포인트 URL(앱에서 쿼리할 모델 주소) 및 예측 키(앱에 예측 요청 액세스를 부여하는 데 사용)를 가져와야 합니다. **성능** 탭에서, 페이지 위쪽에 있는 **예측 URL** 단추를 선택합니다.

![URL 주소 및 API 키가 있는 예측 API 창을 보여 주는 Custom Vision 웹 사이트](media/azure-logo-tutorial/cusvis-endpoint.png)

엔드포인트 URL 및 **Prediction-Key** 값을 *Source\VisualProvision\AppSettings.cs* 파일의 적절한 필드에 복사합니다.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_cusvis_keys)]

## <a name="examine-custom-vision-usage"></a>Custom Vision 사용량 검사

*Source/VisualProvision/Services/Recognition/CustomVisionService.cs* 파일을 열고 Custom Vision 키 및 엔드포인트 URL이 앱에서 어떻게 사용되는지 살펴봅니다. **PredictImageContentsAsync** 메서드는 이미지 파일의 바이트 스트림을 취소 토큰과 함께 사용하고(비동기 작업 관리 용도로), Custom Vision 예측 API를 호출하고, 예측 결과를 반환합니다. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?name=snippet_prediction)]

이 결과는 자체적으로 **예측** 인스턴스 목록을 포함하는 **PredictionResult** 인스턴스 형태입니다. **예측**에는 이미지에서 감지된 태그와 해당 경계 상자 위치가 포함됩니다.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?name=snippet_prediction_class)]

앱이 이 데이터를 어떻게 처리하는지 자세히 알아보려면 **GetResourcesAsync** 메서드를 시작합니다. 이 메서드는 *Source/VisualProvision/Services/Recognition/RecognitionService.cs* 파일에 정의되어 있습니다.  

## <a name="add-computer-vision"></a>Computer Vision 추가

자습서의 Custom Vision 부분이 완료되었습니다. 앱을 실행하려는 경우 Computer Vision 서비스도 통합해야 합니다. 이 앱은 로고 감지 프로세스를 보완하기 위해 Computer Vision의 문자 인식 기능을 사용합니다. Azure 로고는 그 모양으로 *또는* 로고 근처에 인쇄되는 텍스트를 통해 인식할 수 있습니다. Custom Vision 모델과 달리, Computer Vision은 이미지 또는 비디오에서 특정 작업을 수행하도록 미리 학습되어 있습니다.

Computer Vision 서비스를 구독하고 키 및 엔드포인트 URL을 가져옵니다. 이 단계에 대한 도움말은 [구독 키를 가져오는 방법](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe)을 참조하세요.

![[빠른 시작] 메뉴를 선택한 경우 Azure Portal의 Computer Vision 서비스. 키에 대한 링크와 API 엔드포인트 URL에 윤곽선이 표시됨](media/azure-logo-tutorial/comvis-keys.png)

다음으로, *Source\VisualProvision\AppSettings.cs* 파일을 열고 `ComputerVisionEndpoint` 및 `ComputerVisionKey` 변수에 올바른 값을 입력합니다.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_comvis_keys)]

## <a name="create-a-service-principal"></a>서비스 주체 만들기

Azure 구독에 서비스를 배포하려면 앱에 Azure 서비스 주체 계정이 필요합니다. 서비스 주체를 통해 역할 기반 액세스 제어를 사용하여 앱에 특정 권한을 위임할 수 있습니다. 자세한 내용은 [서비스 주체 가이드](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals)를 참조하세요.

다음과 같이 Azure Cloud Shell 또는 Azure CLI를 사용하여 서비스 주체를 만들 수 있습니다. 시작하려면 로그인하고 사용할 구독을 선택합니다.

```azurecli
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

그런 다음, 서비스 주체를 만듭니다. (이 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.)

```azurecli
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

완료되는 즉시 필요한 자격 증명이 포함된 다음 JSON 출력이 보일 것입니다.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

`clientId` 및 `tenantId` 값을 기록해 둡니다. 이러한 값을 *Source\VisualProvision\AppSettings.cs* 파일의 적절한 필드에 추가합니다.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?name=snippet_serviceprincipal)]

## <a name="run-the-app"></a>앱 실행

이제 다음 항목에 대한 액세스 권한이 앱에 부여되었습니다.

- 학습된 Custom Vision 모델
- Computer Vision 서비스
- 서비스 주체 계정

다음 단계에 따라 앱을 실행합니다.

1. Visual Studio 솔루션 탐색기에서 **VisualProvision.Android** 프로젝트 또는 **VisualProvision.iOS** 프로젝트 중 하나를 선택합니다. 주 도구 모음의 드롭다운 메뉴에서 해당 에뮬레이터 또는 연결된 모바일 디바이스를 선택합니다. 그런 다음, 앱을 실행합니다.

    > [!NOTE]
    > iOS 에뮬레이터를 실행하려면 MacOS 디바이스가 필요합니다.

1. 첫 번째 화면에서 서비스 주체 클라이언트 ID, 테넌트 ID 및 암호를 입력합니다. **로그인** 단추를 선택합니다.

    > [!NOTE]
    > 일부 에뮬레이터는 이 단계에서 **로그인** 단추가 활성화되지 않을 수 있습니다. 이 경우 앱을 중지하고, *Source/VisualProvision/Pages/LoginPage.xaml* 파일을 열고, **LOGIN BUTTON**이라는 레이블이 지정된 `Button` 요소를 찾아서 다음 줄을 제거한 다음, 앱을 다시 실행합니다.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![서비스 주체 자격 증명에 대한 필드를 보여 주는 앱 화면](media/azure-logo-tutorial/app-credentials.png)

1. 다음 화면에서는 드롭다운 메뉴에서 Azure 구독을 선택합니다. (이 메뉴는 서비스 주체에서 액세스할 수 있는 모든 구독을 포함합니다.) **계속** 단추를 선택합니다. 이 시점에서 디바이스에 카메라 및 사진 스토리지에 대한 액세스 권한을 부여하라는 메시지가 표시될 수 있습니다. 액세스 권한을 부여합니다.

    ![대상 Azure 구독에 대한 드롭다운 필드를 보여 주는 앱 화면](media/azure-logo-tutorial/app-az-subscription.png)


1. 디바이스의 카메라가 활성화됩니다. 학습한 Azure 서비스 로고 중 하나의 사진을 찍습니다. 새 서비스의 Azure 지역 및 리소스 그룹을 선택(Azure Portal에서 배포할 때와 같은 방법으로)하라는 배포 창이 표시됩니다. 

    ![Azure 로고의 두 그림 조각에 포커스가 맞춰진 스마트폰 카메라 화면](media/azure-logo-tutorial/app-camera-capture.png)

    ![배포 지역 및 리소스 그룹에 대한 필드를 보여 주는 앱 화면](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>리소스 정리

이 시나리오의 모든 단계를 수행하고 앱을 사용하여 계정에 Azure 서비스를 배포한 경우 [Azure Portal](https://ms.portal.azure.com/)로 이동합니다. 여기서 사용하지 않을 서비스를 취소합니다.

Custom Vision을 사용하여 자체적인 개체 검색 프로젝트를 만들 계획인 경우 이 자습서에서 만든 로고 감지 프로젝트를 삭제해도 됩니다. Custom Vision 체험 구독은 두 개의 프로젝트만 허용합니다. 로고 감지 프로젝트를 삭제하려면 [Custom Vision 웹 사이트](https://customvision.ai)에서 **프로젝트**를 연 다음, **내 새 프로젝트** 아래 휴지통 아이콘을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Custom Vision 서비스를 사용하여 모바일 카메라 이미지에서 로고를 감지하는 완전한 Xamarin.Forms 앱을 설정하고 살펴보았습니다. 다음으로, 자체적으로 앱을 만들 때 성능과 정확도를 높일 수 있도록 Custom Vision 모델을 빌드하는 모범 사례를 알아보세요.

> [!div class="nextstepaction"]
> [분류자 개선 방법](getting-started-improving-your-classifier.md)
