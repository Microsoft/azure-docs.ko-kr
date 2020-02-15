---
title: '빠른 시작: .NET용 Face 클라이언트 라이브러리'
description: 이 빠른 시작을 통해 .NET용 Face 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a9fb77ea30aa101653d50e7833876dbec6362093
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76930141"
---
# <a name="quickstart-face-client-library-for-net"></a>빠른 시작: .NET용 Face 클라이언트 라이브러리

.NET용 Face 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

.NET용 Face 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [이미지에서 얼굴 감지](#detect-faces-in-an-image)
* [유사 얼굴 찾기](#find-similar-faces)
* [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)
* [얼굴 식별](#identify-a-face)
* [데이터 마이그레이션용 스냅샷 만들기](#take-a-snapshot-for-data-migration)

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [샘플](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>설치

### <a name="create-a-face-azure-resource"></a>Face Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Face용 리소스를 만듭니다. 다음도 가능합니다.

* 7일 동안 유효한 [평가판 키](https://azure.microsoft.com/try/cognitive-services/#decision)를 가져옵니다. 이 키는 가입 후 [Azure 웹 사이트](https://azure.microsoft.com/try/cognitive-services/my-apis/)에서 사용할 수 있습니다.  
* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

평가판 구독 또는 리소스에서 키를 가져오면 각각 `FACE_SUBSCRIPTION_KEY` 및 `FACE_ENDPOINT`라는 키 및 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다. 

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `face-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs*라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다. 

```dotnetcli
dotnet new console -n face-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

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

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *Program.cs* 파일을 엽니다. 다음 `using` 지시문을 추가합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

애플리케이션의 `Main` 메서드에서, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Face 클라이언트 라이브러리를 설치합니다.

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

## <a name="object-model"></a>개체 모델

Face .NET SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|속성|Description|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | 이 클래스는 Face 서비스를 사용할 수 있는 권한의 부여를 나타내며 모든 Face 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|이 클래스는 사람 얼굴을 사용하여 수행할 수 있는 기본 감지 및 인식 작업을 처리합니다. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|이 클래스는 이미지의 한 얼굴에서 감지된 모든 데이터를 나타냅니다. 얼굴에 대한 자세한 정보를 검색하는 데 사용할 수 있습니다.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|이 클래스는 분류된 얼굴 세트를 저장하는 클라우드 저장 **FaceList** 구문을 관리합니다. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| 이 클래스는 한 사람에게 속한 얼굴 세트를 저장하는 클라우드 저장 **Person** 구문을 관리합니다.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| 이 클래스는 분류된 **Person** 개체 세트를 저장하는 클라우드 저장 **PersonGroup** 구문을 관리합니다. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|이 클래스는 스냅샷 기능을 관리합니다. 이를 사용하여 모든 클라우드 기반 Face 데이터를 임시로 저장하고, 해당 데이터를 새 Azure 구독으로 마이그레이션할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

아래 코드 조각에서는 .NET용 Face 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지에서 얼굴 감지](#detect-faces-in-an-image)
* [유사 얼굴 찾기](#find-similar-faces)
* [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)
* [얼굴 식별](#identify-a-face)
* [데이터 마이그레이션용 스냅샷 만들기](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `FACE_SUBSCRIPTION_KEY` 및 `FACE_ENDPOINT`라는 Face 키 및 엔드포인트에 대한 [환경 변수를 만들었다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)고 가정합니다.

새 메서드에서 엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** 개체를 만들고, 엔드포인트에서 이를 사용하여 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 개체를 만듭니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

`Main` 메서드에서 다음 메서드를 호출하는 것이 좋습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

클래스의 루트에서 다음 URL 문자열을 정의합니다. 이 URL은 샘플 이미지 세트를 가리킵니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

필요에 따라 감지된 얼굴에서 데이터를 추출하는 데 사용할 AI 모델을 선택할 수 있습니다. 이러한 옵션에 대한 자세한 내용은 [인식 모델 지정](../Face-API-How-to-Topics/specify-recognition-model.md)을 참조하세요.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

최종 검색 작업에서는 **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** 개체, 이미지 URL 및 인식 모델이 사용됩니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>검색된 얼굴 개체 가져오기

다음 코드 블록에서 `DetectFaceExtract` 메서드는 지정된 URL에 있는 세 개의 이미지에서 얼굴을 감지하여 **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** 개체의 목록을 프로그램 메모리에 만듭니다. **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 값 목록은 추출할 기능을 지정합니다. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>검색된 얼굴 데이터 표시

나머지 `DetectFaceExtract` 메서드는 검색된 각 얼굴에 대한 특성 데이터를 구문 분석하고 출력합니다. 각 특성은 원래 얼굴 검색 API 호출( **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** 목록의)에서 별도로 지정해야 합니다. 다음 코드는 모든 특성을 처리하지만 하나 또는 몇 가지를 사용해야 할 가능성이 높습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>유사 얼굴 찾기

다음 코드에서는 감지된 하나의 얼굴(원본)을 가져오고, 다른 얼굴(대상) 세트를 검색하여 일치 항목을 찾습니다. 일치 항목을 찾으면 일치하는 얼굴의 ID를 콘솔에 출력합니다.

### <a name="detect-faces-for-comparison"></a>비교할 얼굴 감지

먼저 두 번째 얼굴 감지 메서드를 정의합니다. 이미지를 비교하기 전에 이미지에서 얼굴을 감지해야 하며, 이 감지 메서드는 비교 작업에 최적화되어 있습니다. 위의 섹션과 같이 자세한 얼굴 특성을 추출하지 않으며 다른 인식 모델을 사용합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>일치 항목 찾기

다음 메서드는 대상 이미지 세트와 단일 원본 이미지에서 얼굴을 감지합니다. 그런 다음, 이를 비교하여 원본 이미지와 유사한 모든 대상 이미지를 찾습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>일치 항목 출력

다음 코드에서는 일치 항목 세부 정보를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>사람 그룹 만들기 및 학습

다음 코드에서는 6개의 서로 다른 **Person** 개체를 사용하여 **PersonGroup**을 만듭니다. 각 **Person**을 예제 이미지 세트와 연결한 다음, 해당 얼굴 특성을 통해 각 사람을 인식하도록 학습시킵니다. **Person** 및 **PersonGroup** 개체는 Verify(검증), Identify(식별) 및 Group(그룹) 작업에 사용됩니다.

아직 수행하지 않은 경우 클래스의 루트에서 다음 URL 문자열을 정의합니다. 이는 샘플 이미지 세트를 가리킵니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

이 섹션의 뒷부분에 있는 코드에서는 얼굴에서 데이터를 추출할 인식 모델을 지정하며, 다음 코드 조각에서는 사용 가능한 모델에 대한 참조를 만듭니다. 인식 모델에 대한 자세한 내용은 [인식 모델 지정](../Face-API-How-to-Topics/specify-recognition-model.md)을 참조하세요.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>PersonGroup 만들기

**PersonGroup**의 ID를 나타내도록 클래스의 루트에서 문자열 변수를 선언합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

새 메서드에서 다음 코드를 추가합니다. 이 코드에서는 사람의 이름을 해당 예제 이미지와 연결합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

다음으로, 다음 코드를 추가하여 Dictionary(사전)의 각 사람에 대한 **Person** 개체를 만들고 적절한 이미지에서 얼굴 데이터를 추가합니다. 각 **Person** 개체는 고유 ID 문자열을 통해 동일한 **PersonGroup**과 연결됩니다. `client`, `url` 및 `RECOGNITION_MODEL1` 변수를 이 메서드에 전달해야 합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>PersonGroup 학습

이미지에서 얼굴 데이터를 추출하여 다른 **Person** 개체에 정렬했으면 각 **Person**  개체와 연결된 시각적 기능을 식별하도록 **PersonGroup**을 학습시켜야 합니다. 다음 코드에서는 비동기 **train** 메서드를 호출하고, 결과를 폴링하여 상태를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

이제 이 **Person** 그룹 및 연결된 해당 **Person** 개체는 Verify(검증), Identify(식별) 또는 Group(그룹) 작업에 사용할 수 있습니다.

## <a name="identify-a-face"></a>얼굴 식별

Identify(식별) 작업은 사람(또는 여러 사람)의 이미지를 가져와서 이미지에서 각 얼굴의 ID를 찾습니다. 감지된 각 얼굴을 얼굴 특징이 알려진 다른 **Person** 개체의 데이터베이스인 **PersonGroup**과 비교합니다.

> [!IMPORTANT]
> 이 예제를 실행하려면 먼저 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)에서 코드를 실행해야 합니다. 여기서는 해당 섹션(`client`, `url` 및 `RECOGNITION_MODEL1`)에 사용된 변수도 사용할 수 있어야 합니다.

### <a name="get-a-test-image"></a>테스트 이미지 가져오기

[사람 그룹 만들기 및 학습](#create-and-train-a-person-group)의 코드에서는 `sourceImageFileName` 변수를 정의합니다. 이 변수는 식별할 사람이 포함된 원본 이미지에 해당합니다.

### <a name="identify-faces"></a>얼굴 식별

다음 코드에서는 원본 이미지를 가져와서 이미지에서 감지된 모든 얼굴의 목록을 만듭니다. 이러한 얼굴은 **PersonGroup**과 비교하여 식별되는 얼굴입니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

다음 코드 조각에서는 Identify(식별) 작업을 호출하고 결과를 콘솔에 출력합니다. 여기서는 서비스에서 원본 이미지의 각 얼굴을 지정된 **PersonGroup**의 **Person**과 일치시키려고 합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>데이터 마이그레이션용 스냅샷 만들기

스냅샷 기능을 사용하면 학습된 **PersonGroup**과 같이 저장된 Face 데이터를 다른 Azure Cognitive Services Face 구독으로 이동할 수 있습니다. 예를 들어 평가판 구독을 사용하여 **PersonGroup** 개체를 만들었고 유료 구독으로 마이그레이션하려는 경우 이 기능을 사용할 수 있습니다. 스냅샷 기능에 대한 개요는 [얼굴 데이터 마이그레이션](../Face-API-How-to-Topics/how-to-migrate-face-data.md)을 참조하세요.

다음 예제에서는 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)에서 만든 **PersonGroup**을 마이그레이션합니다. 먼저 해당 섹션을 완료하거나, 마이그레이션할 사용자 고유의 Face 데이터를 생성할 수 있습니다.

### <a name="set-up-target-subscription"></a>대상 구독 설정

먼저, Face 리소스가 포함된 두 번째 Azure 구독이 있어야 합니다. 이 작업은 [설정](#setting-up) 섹션의 단계에 따라 수행할 수 있습니다. 

그런 다음, 프로그램의 `Main` 메서드에서 다음 변수를 정의합니다. Azure 계정의 구독 ID와 새(대상) 계정의 키, 엔드포인트 및 구독 ID에 대한 새 환경 변수를 만들어야 합니다. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

다음 예제에서는 데이터를 복사할 새 구독에 속하는 개체인 대상 **PersonGroup**의 ID에 대한 변수를 선언합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>대상 클라이언트 인증

다음으로, 보조 Face 구독을 인증하는 코드를 추가합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>스냅샷 사용

나머지 스냅샷 작업은 비동기 메서드 내에서 수행해야 합니다. 

1. 첫 번째 단계는 스냅샷을 **take**(수행)하여 원래 구독의 얼굴 데이터를 임시 클라우드 위치에 저장하는 것입니다. 이 메서드는 작업 상태를 쿼리하는 데 사용하는 ID를 반환합니다.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. 다음으로, 작업이 완료될 때까지 ID를 쿼리합니다.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. 그런 다음, **apply**(적용) 작업을 사용하여 얼굴 데이터를 대상 구독에 씁니다. 또한 이 메서드는 ID 값도 반환합니다.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. 다시 한 번, 작업이 완료될 때까지 새 ID를 쿼리합니다.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. 마지막으로, try/catch 블록을 완료하고 메서드를 완료합니다.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

이 시점에서 새 **PersonGroup** 개체의 데이터는 원본 데이터와 동일해야 하며, 새(대상) Azure Face 구독에서 액세스할 수 있어야 합니다.

## <a name="run-the-application"></a>애플리케이션 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

이 빠른 시작에서는 **PersonGroup**을 만들었으며, 이를 삭제하려면 프로그램에서 다음 코드를 실행합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

다음 코드를 사용하여 삭제 메서드를 정의합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

또한 이 빠른 시작에서 스냅샷 기능을 사용하여 데이터를 마이그레이션한 경우 대상 구독에 저장된 **PersonGroup**도 삭제해야 합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 .NET용 Face 라이브러리를 사용하여 기본 작업을 수행하는 방법을 알아보았습니다. 다음으로 라이브러리에 대해 자세히 알아보려면 참조 설명서를 살펴보세요.

> [!div class="nextstepaction"]
> [Face API 참조(.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Face 서비스란?](../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs)에서 확인할 수 있습니다.
