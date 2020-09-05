---
title: Face Go 클라이언트 라이브러리 빠른 시작
description: Go용 Face 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: bb8f80baac5ce49db612a7d3a599d727b824bffc
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505885"
---
Go용 Face 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 라이브러리를 설치하고 기본 작업에 대한 예제를 사용해보십시오. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

Go용 Face 서비스 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* [이미지에서 얼굴 감지](#detect-faces-in-an-image)
* [유사 얼굴 찾기](#find-similar-faces)
* [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)
* [얼굴 식별](#identify-a-face)
* [데이터 마이그레이션용 스냅샷 만들기](#take-a-snapshot-for-data-migration)

[참조 설명서](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK 다운로드](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>필수 구성 요소

* 최신 버전의 [Go](https://golang.org/dl/)
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 키와 엔드포인트를 가져온 후에는 각각 `FACE_SUBSCRIPTION_KEY` 및 `FACE_ENDPOINT`라는 키 및 엔드포인트에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="setting-up"></a>설치

### <a name="create-a-go-project-directory"></a>Go 프로젝트 디렉터리 만들기

콘솔 창(cmd, PowerShell, 터미널, Bash)에서 Go 프로젝트에 대한 새 작업 영역 `my-app`을 만들고 해당 작업 영역으로 이동합니다.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

작업 영역에는 다음 세 개의 폴더가 있습니다.

* **src** - 이 디렉터리에는 소스 코드와 패키지가 포함됩니다. `go get` 명령으로 설치된 패키지는 이 디렉터리에 있습니다.
* **pkg** - 이 디렉터리에는 컴파일된 Go 패키지 개체가 포함됩니다. 이러한 파일의 확장명은 모두 `.a`입니다.
* **bin** - 이 디렉터리에는 `go install`을 실행할 때 만들어지는 이진 실행 파일이 포함됩니다.

> [!TIP]
> Go 작업 영역의 구조에 대한 자세한 내용은 [Go 언어 설명서](https://golang.org/doc/code.html#Workspaces)를 참조하세요. 이 가이드에는 `$GOPATH` 및 `$GOROOT` 설정에 대한 정보가 있습니다.

### <a name="install-the-client-library-for-go"></a>Go용 클라이언트 라이브러리 설치

다음으로, Go용 클라이언트 라이브러리를 설치합니다.

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

dep를 사용하는 경우에는 리포지토리 내에서 다음을 실행합니다.

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go 애플리케이션 빌드

다음으로, **src** 디렉터리에 `sample-app.go`라는 파일을 만듭니다.

```bash
cd src
touch sample-app.go
```

원하는 IDE 또는 텍스트 편집기에서 `sample-app.go`를 엽니다. 그런 다음, 패키지 이름을 추가하고 다음 라이브러리를 가져옵니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

다음으로, 다른 Face 서비스 작업을 수행하는 코드를 추가하겠습니다.

## <a name="object-model"></a>개체 모델

Face 서비스 Go 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | 이 클래스는 Face 서비스를 사용할 수 있는 권한의 부여를 나타내며 모든 Face 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. |
|[클라이언트](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|이 클래스는 사람 얼굴을 사용하여 수행할 수 있는 기본 감지 및 인식 작업을 처리합니다. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|이 클래스는 이미지의 한 얼굴에서 감지된 모든 데이터를 나타냅니다. 얼굴에 대한 자세한 정보를 검색하는 데 사용할 수 있습니다.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|이 클래스는 분류된 얼굴 세트를 저장하는 클라우드 저장 **FaceList** 구문을 관리합니다. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| 이 클래스는 한 사람에게 속한 얼굴 세트를 저장하는 클라우드 저장 **Person** 구문을 관리합니다.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| 이 클래스는 분류된 **Person** 개체 세트를 저장하는 클라우드 저장 **PersonGroup** 구문을 관리합니다. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|이 클래스는 스냅샷 기능을 관리합니다. 이를 사용하여 모든 클라우드 기반 Face 데이터를 임시로 저장하고, 해당 데이터를 새 Azure 구독으로 마이그레이션할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

다음 코드 샘플에서는 Go용 Face 서비스 클라이언트 라이브러리를 사용하여 기본 작업을 완료하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지에서 얼굴 감지](#detect-faces-in-an-image)
* [유사 얼굴 찾기](#find-similar-faces)
* [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)
* [얼굴 식별](#identify-a-face)
* [데이터 마이그레이션용 스냅샷 만들기](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE] 
> 이 빠른 시작에서는 각각 `FACE_SUBSCRIPTION_KEY` 및 `FACE_ENDPOINT`라는 Face 키 및 엔드포인트에 대한 [환경 변수를 만들었다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)고 가정합니다.

**main** 함수를 만들고, 클라이언트를 엔드포인트 및 키로 인스턴스화하는 다음 코드를 이 함수에 추가합니다. 키를 사용하여 **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** 개체를 만들고, 엔드포인트에서 이 개체를 사용하여 **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** 개체를 만듭니다. 또한 이 코드는 클라이언트 개체를 만드는 데 필요한 컨텍스트 개체를 인스턴스화합니다. 뿐만 아니라 이 빠른 시작의 샘플 이미지 중 일부가 있는 원격 위치를 정의합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

**main** 메서드에 다음 코드를 추가합니다. 이 코드는 원격 샘플 이미지를 정의하고 이미지에서 추출할 얼굴 특징을 지정합니다. 또한 감지된 얼굴에서 데이터를 추출하는 데 사용할 AI 모델을 지정합니다. 이러한 옵션에 대한 자세한 내용은 [인식 모델 지정](../../Face-API-How-to-Topics/specify-recognition-model.md)을 참조하세요. 마지막으로, **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** 메서드는 이미지에서 얼굴 검색 작업을 수행하고 그 결과를 프로그램 메모리에 저장합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>검색된 얼굴 데이터 표시

코드의 그 다음 블록은 **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** 개체 배열의 첫 번째 요소를 가져와서 해당 특성을 콘솔에 출력합니다. 여러 얼굴에 이미지를 사용한 경우 그 대신 배열을 반복해야 합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>유사 얼굴 찾기

다음 코드에서는 감지된 하나의 얼굴(원본)을 가져오고, 다른 얼굴(대상) 세트를 검색하여 일치 항목을 찾습니다. 일치 항목을 찾으면 일치하는 얼굴의 ID를 콘솔에 출력합니다.

### <a name="detect-faces-for-comparison"></a>비교할 얼굴 감지

먼저 [이미지에서 얼굴 감지](#detect-faces-in-an-image) 섹션에서 감지한 얼굴의 참조를 저장합니다. 이 얼굴이 원본입니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

그런 다음, 다른 이미지에서 얼굴 세트를 감지할 다음 코드를 입력합니다. 여기서 감지되는 얼굴이 대상입니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>일치 항목 찾기

다음 코드는 **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** 메서드를 사용하여 원본 얼굴과 일치하는 모든 대상 얼굴을 찾습니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>일치 항목 출력

다음 코드에서는 일치 항목 세부 정보를 콘솔에 출력합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>사람 그룹 만들기 및 학습

이 시나리오를 단계별로 실행하려면 https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 의 이미지를 프로젝트의 루트 디렉터리에 저장해야 합니다.

이 이미지 그룹에는 서로 다른 세 명의 사람과 일치하는 세 개의 단일 얼굴 이미지 세트가 포함되어 있습니다. 이 코드는 세 개의 **PersonGroup Person** 개체를 정의하고, `woman`, `man` 및 `child`로 시작하는 이미지 파일에 이 개체를 연결합니다.

### <a name="create-persongroup"></a>PersonGroup 만들기

이미지를 다운로드한 후에는 **main** 메서드의 맨 아래에 다음 코드를 추가합니다. 이 코드는 **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** 개체를 인증 한 다음, 이 개체를 사용하여 새 **PersonGroup**을 정의합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup Person 만들기

다음 코드 블록은 **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** 개체를 인증하고, 이 개체를 사용하여 세 개의 새 **PersonGroup Person** 개체를 정의합니다. 이러한 개체는 각각 이미지 세트의 사용자 한 명을 나타냅니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Person에 얼굴 할당

다음 코드는 접두사를 기준으로 이미지를 정렬하고, 얼굴을 감지하고, 이미지 파일 이름에 따라 얼굴을 각 **PersonGroup Person** 개체에 할당합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>PersonGroup 학습

얼굴을 할당한 후에는 각 **Person** 개체와 관련된 시각적 특징을 식별할 수 있도록 **PersonGroup**을 학습시켜야 합니다. 다음 코드는 비동기 **train** 메서드를 호출하고, 결과를 폴링하여 상태를 콘솔에 출력합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>얼굴 식별

다음 코드에서는 여러 얼굴이 있는 이미지를 가져오고, 해당 이미지에서 각 사람의 ID를 찾습니다. 감지된 각 얼굴을 얼굴 특징이 알려진 다른 **Person** 개체의 데이터베이스인 **PersonGroup**과 비교합니다.

> [!IMPORTANT]
> 이 예제를 실행하려면 먼저 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)에서 코드를 실행해야 합니다.

### <a name="get-a-test-image"></a>테스트 이미지 가져오기

다음 코드는 프로젝트의 루트에서 _test-image-person-group.jpg_ 이미지를 찾아 프로그램 메모리에 로드합니다. 이 이미지는 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 에 사용한 이미지와 동일한 리포지토리에서 찾을 수 있습니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>테스트 이미지에서 원본 얼굴 감지

다음 코드 블록은 테스트 이미지를 대상으로 일반적인 얼굴 감지를 수행하여 모든 얼굴을 감지하고 배열에 저장합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>얼굴 식별

**[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** 메서드는 감지된 얼굴 배열을 가져와서 지정된 (이전 섹션에서 정의하고 학습시킨) **PersonGroup**과 비교합니다. 감지된 얼굴을 **Person**과 매칭할 수 있으면 결과가 저장됩니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

그런 다음, 이 코드는 자세한 매칭 결과를 콘솔에 출력합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>얼굴 확인

확인 작업은 얼굴 ID와 다른 얼굴 ID 또는 **Person** 개체를 가져와서 동일한 사람에게 속하는지 여부를 확인합니다.

다음 코드는 두 개의 원본 이미지에서 얼굴을 감지한 다음, 대상 이미지에서 검색된 각 얼굴과 대조합니다.

### <a name="get-test-images"></a>테스트 이미지 가져오기

다음 코드 블록은 확인 작업을 위한 원본 및 대상 이미지를 가리키는 변수를 선언합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>확인을 위한 얼굴 감지

다음 코드는 소스 및 대상 이미지에서 얼굴을 감지하고 변수에 저장합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>확인 결과 가져오기

다음 코드는 각 원본 이미지를 대상 이미지와 비교하고 동일한 사람에 속하는지 여부를 나타내는 메시지를 출력합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>데이터 마이그레이션용 스냅샷 만들기

스냅샷 기능을 사용하면 학습된 **PersonGroup**과 같은 저장된 얼굴 데이터를 다른 Azure Cognitive Services Face 구독으로 이동할 수 있습니다. 예를 들어 체험 구독을 사용하여 **PersonGroup** 개체를 만들었고 이제 유료 구독으로 마이그레이션하려는 경우 이 기능을 사용할 수 있습니다. 스냅샷 기능에 대한 광범위한 개요는 [얼굴 데이터 마이그레이션](../../Face-API-How-to-Topics/how-to-migrate-face-data.md)을 참조하세요.

다음 예제에서는 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)에서 만든 **PersonGroup**을 마이그레이션합니다. 해당 섹션을 먼저 완료하거나 사용자 고유의 Face 데이터 구문을 사용할 수 있습니다.

### <a name="set-up-target-subscription"></a>대상 구독 설정

먼저, Face 리소스가 포함된 두 번째 Azure 구독이 있어야 합니다. 이 작업은 [설정](#setting-up) 섹션의 단계를 반복하여 수행할 수 있습니다. 

그런 다음, **main** 메서드의 위쪽 근처에 다음 변수를 만듭니다. 또한 Azure 계정의 구독 ID와 새(대상) 계정의 키, 엔드포인트 및 구독 ID에 대한 새 환경 변수를 만들어야 합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

그리고 다음 단계를 진행할 수 있도록 구독 ID 값을 배열에 배치합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>대상 클라이언트 인증

스크립트의 뒷부분에서 원래 클라이언트 개체를 원본 클라이언트로 저장한 다음, 대상 구독에 대한 새 클라이언트 개체를 인증합니다. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>스냅샷 만들기

그 다음 단계는 **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** 메서드를 사용하여 원래 구독의 얼굴 데이터를 임시 클라우드 위치에 저장하는 스냅샷을 만드는 것입니다. 이 메서드는 작업 상태를 쿼리하는 데 사용하는 ID를 반환합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

다음으로, 작업이 완료될 때까지 ID를 쿼리합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>스냅샷 적용

**[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** 작업을 사용하여 새로 업로드된 얼굴 데이터를 대상 구독에 씁니다. 또한 이 메서드는 ID도 반환합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

마찬가지로, 작업이 완료될 때까지 ID를 쿼리합니다.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

이러한 단계가 완료되면 새 (대상) 구독에서 얼굴 데이터 구문에 액세스할 수 있습니다.

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `go run [arguments]` 명령을 사용하여 Go 애플리케이션을 실행합니다.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

이 빠른 시작에서 **PersonGroup**을 만든 경우 **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** 메서드를 호출하여 삭제할 수 있습니다. 이 빠른 시작에서 스냅샷 기능을 사용하여 데이터를 마이그레이션한 경우 대상 구독에 저장된 **PersonGroup**도 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Go용 Face 라이브러리를 사용하여 기본 작업을 수행하는 방법을 알아보았습니다. 다음으로 라이브러리에 대해 자세히 알아보려면 참조 설명서를 살펴보세요.

> [!div class="nextstepaction"]
> [Face API 참조(Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Face 서비스란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go)에서 확인할 수 있습니다.
