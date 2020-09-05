---
title: '빠른 시작: Node.js용 Computer Vision 클라이언트 라이브러리'
description: 이 빠른 시작을 통해 Node.js용 Computer Vision 클라이언트 라이브러리 시작
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 01/22/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 71e6d159a7bb69e5aab28752f5ecf1eacaebd624
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321900"
---
<a name="HOLTop"></a>

[참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [패키지(npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir myapp && cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다.

```console
npm init
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

`ms-rest-azure` 및 `@azure/cognitiveservices-computervision` NPM 패키지를 설치합니다.

```console
npm install @azure/cognitiveservices-computervision
```

종속성이 있는 앱의 `package.json` 파일이 업데이트됩니다.

### <a name="prepare-the-nodejs-script"></a>Node.js 스크립트 준비

새 파일, *index.js*를 만들고, 텍스트 편집기에서 엽니다. 다음 import 명령문을 추가합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

그런 다음, `computerVision` 함수를 정의하고 기본 함수 및 콜백 함수를 사용하여 비동기 계열을 선언합니다. 기본 함수에 빠른 시작 코드를 추가하고, 스크립트의 맨 아래에 있는 `computerVision`을 호출합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>개체 모델

Computer Vision Node.js SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 이 클래스를 인스턴스화한 다음, 대부분의 이미지 작업에 사용합니다.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| 이 열거형은 표준 Analyze(분석) 작업에서 수행할 수 있는 다양한 유형의 이미지 분석을 정의합니다. 필요에 따라 **VisualFeatureTypes** 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

이 코드 조각은 Node.js용 Computer Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 분석](#analyze-an-image)
* [인쇄 텍스트 및 필기 텍스트 읽기](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키 및 엔드포인트를 사용하여 [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) 개체를 만든 다음, 이를 사용하여 [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) 개체를 만듭니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>이미지 분석

이 섹션의 코드는 원격 이미지를 분석하여 다양한 시각적 개체 기능을 추출합니다. 클라이언트 개체의 **analyzeImage** 메서드의 일부로 이러한 작업을 수행하거나 개별 메서드를 사용하여 호출할 수 있습니다. 자세한 내용은 [참조 설명서](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)를 참조하세요.

> [!NOTE]
> 로컬 이미지를 분석할 수도 있습니다. 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)의 샘플 코드를 참조하세요.

### <a name="get-image-description"></a>이미지 설명 가져오기

다음 코드는 이미지에 대해 생성된 캡션 목록을 가져옵니다. 자세한 내용은 [이미지 설명](../../concept-describing-images.md)을 참조하세요.

먼저 분석할 이미지의 URL을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

그런 다음, 다음 코드를 추가하여 이미지 설명을 가져오고 콘솔에 인쇄합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>이미지 범주 가져오기

다음 코드는 검색된 이미지 범주를 가져옵니다. 자세한 내용은 [이미지 분류](../../concept-categorizing-images.md)를 참조하세요.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

도우미 함수 `formatCategories`을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>이미지 태그 가져오기

다음 코드는 이미지에서 검색된 범주 세트를 가져옵니다. 자세한 내용은 [콘텐츠 태그](../../concept-tagging-images.md)를 참조하세요.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

도우미 함수 `formatTags`을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>개체 감지

다음 코드는 이미지의 공통 개체를 검색하여 콘솔에 출력합니다. 자세한 내용은 [개체 감지](../../concept-object-detection.md)를 참조하세요.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

도우미 함수 `formatRectObjects`를 정의하여 너비 및 높이와 함께 위쪽, 왼쪽, 아래쪽 및 오른쪽 좌표를 반환합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>브랜드 감지

다음 코드는 이미지의 회사 브랜드 및 로고를 감지하여 콘솔에 출력합니다. 자세한 내용은 [브랜드 감지](../../concept-brand-detection.md)를 참조하세요.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>얼굴 감지

다음 코드는 사각형 좌표를 사용하여 이미지에서 검색된 얼굴을 반환하고 얼굴 특성을 선택합니다. 자세한 내용은 [얼굴 감지](../../concept-detecting-faces.md)를 참조하세요.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

도우미 함수 `formatRectFaces`을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>성인, 외설 또는 폭력 콘텐츠 검색

다음 코드는 이미지에 있는 성인 콘텐츠의 검색된 상태를 출력합니다. 자세한 내용은 [성인, 외설, 폭력 콘텐츠](../../concept-detecting-adult-content.md)를 참조하세요.

사용할 이미지의 URL을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

그런 다음, 다음 코드를 추가하여 성인 콘텐츠를 검색하고 결과를 콘솔에 인쇄합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>이미지 색 구성표 가져오기

다음 코드는 주조색 및 강조 색과 같이 이미지에서 검색된 색 특성을 출력합니다. 자세한 내용은 [색 구성표](../../concept-detecting-color-schemes.md)를 참조하세요.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

`printColorScheme` 도우미 함수를 정의하여 색 구성표의 세부 정보를 콘솔에 인쇄합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>도메인 특정 콘텐츠 가져오기

Computer Vision은 특수 모델을 사용하여 이미지에 대한 추가 분석을 수행할 수 있습니다. 자세한 내용은 [도메인 특정 콘텐츠](../../concept-detecting-domain-content.md)를 참조하세요.

먼저 분석할 이미지의 URL을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

다음 코드는 이미지에서 검색된 랜드마크에 대한 데이터를 구문 분석합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

`formatRectDomain` 도우미 함수를 정의하여 검색된 랜드마크에 대한 위치 데이터를 구문 분석합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>이미지 형식 가져오기

다음 코드는 이미지 형식이 클립 아트인지 아니면 선 그리기인지 여부에 관계없이 이미지 형식에 대한 정보를 출력합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

도우미 함수 `describeType`을 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="extract-text-ocr-with-read"></a>읽기로 텍스트 추출(OCR)

Computer Vision은 이미지에서 보이는 텍스트를 추출하여 문자 스트림으로 변환할 수 있습니다. 이 샘플에서는 읽기 작업을 사용합니다.

> [!NOTE]
> 로컬 이미지에서 텍스트를 읽을 수도 있습니다. 로컬 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)의 샘플 코드를 참조하세요.

### <a name="set-up-test-images"></a>테스트 이미지 설정

텍스트를 추출하려는 이미지의 URL에 대한 참조를 저장합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-read-api"></a>읽기 API 호출

지정된 이미지에 대해 `readTextFromURL` 및 `readTextFromFile` 함수를 호출하는 아래 코드를 추가합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

`readTextFromURL` 및 `readTextFromFile` 함수를 정의합니다. 클라이언트 개체에서 **read** 및 **readInStream** 메서드를 호출합니다. 이는 작업 ID를 반환하고 이미지의 콘텐츠를 읽는 비동기 프로세스를 시작합니다. 그런 다음, 작업 ID를 사용하여 결과가 반환될 때까지 작업 상태를 확인합니다. 그런 다음, 추출된 결과를 반환합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

그런 다음, 읽기 작업의 결과를 콘솔에 출력하는 `printRecText` 도우미 함수를 정의합니다.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `node` 명령을 사용하여 애플리케이션을 실행합니다.

```console
node index.js
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[Computer Vision API 참조(Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Computer Vision이란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js)에서 확인할 수 있습니다.
