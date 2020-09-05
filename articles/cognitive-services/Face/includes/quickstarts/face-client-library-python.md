---
title: Face Python 클라이언트 라이브러리 빠른 시작
description: 이 빠른 시작은 Python을 검색하고, 유사한 것을 찾고, 식별하고, 확인하는 데 사용되는 Face 클라이언트 라이브러리를 시작하는 데 도움이 됩니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 8ec32c7bd0d977ea18e4ff66dc616c300851a633
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505886"
---
Python용 Face 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

Python용 Face 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 이미지에서 얼굴 감지
* 유사 얼굴 찾기
* 사람 그룹 만들기 및 학습
* 얼굴 식별
* 얼굴 확인
* 데이터 마이그레이션용 스냅샷 만들기

[참조 설명서](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [패키지(PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [샘플](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>필수 구성 요소

* [Python 3.x](https://www.python.org/)
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 키와 엔드포인트를 가져온 후에는 각각 `FACE_SUBSCRIPTION_KEY` 및 `FACE_ENDPOINT`라는 키 및 엔드포인트에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="setting-up"></a>설치
 
### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

예를 들어 새 Python 스크립트(*quickstart-file.py*)를 만듭니다. 그런 다음, 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 라이브러리를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

그런 다음, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>개체 모델

Face Python 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | 이 클래스는 Face 서비스를 사용할 수 있는 권한의 부여를 나타내며 모든 Face 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|이 클래스는 사람 얼굴을 사용하여 수행할 수 있는 기본 감지 및 인식 작업을 처리합니다. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|이 클래스는 이미지의 한 얼굴에서 감지된 모든 데이터를 나타냅니다. 얼굴에 대한 자세한 정보를 검색하는 데 사용할 수 있습니다.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|이 클래스는 분류된 얼굴 세트를 저장하는 클라우드 저장 **FaceList** 구문을 관리합니다. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| 이 클래스는 한 사람에게 속한 얼굴 세트를 저장하는 클라우드 저장 **Person** 구문을 관리합니다.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| 이 클래스는 분류된 **Person** 개체 세트를 저장하는 클라우드 저장 **PersonGroup** 구문을 관리합니다. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|이 클래스는 스냅샷 기능을 관리합니다. 이를 사용하여 모든 클라우드 기반 얼굴 데이터를 임시로 저장하고 해당 데이터를 새 Azure 구독으로 마이그레이션할 수 있습니다. |

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Face 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지에서 얼굴 감지](#detect-faces-in-an-image)
* [유사 얼굴 찾기](#find-similar-faces)
* [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)
* [얼굴 식별](#identify-a-face)
* [얼굴 확인](#verify-faces)
* [데이터 마이그레이션용 스냅샷 만들기](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `FACE_SUBSCRIPTION_KEY`라는 Face 키에 대한 [환경 변수를 만들었다](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)고 가정합니다.

엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) 개체를 만들고, 엔드포인트에서 이를 사용하여 [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>이미지에서 얼굴 감지

다음 코드에서는 원격 이미지에서 얼굴을 감지합니다. 감지된 얼굴의 ID를 콘솔에 출력하고, 프로그램 메모리에 저장합니다. 그런 다음, 여러 사람이 있는 이미지에서도 얼굴을 감지하고, 해당 ID를 콘솔에 출력합니다. [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-) 메서드의 매개 변수를 변경하면 각 [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python) 개체와 관련된 다른 정보를 반환할 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

자세한 감지 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)의 샘플 코드를 참조하세요.

### <a name="display-and-frame-faces"></a>표시 및 프레임 면

다음 코드는 DetectedFace.faceRectangle 속성을 사용하여 지정된 이미지를 디스플레이에 출력하고 얼굴 주위에 사각형을 그립니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![얼굴 주위에 빨간색 사각형이 그려진 젊은 여자](../../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>유사 얼굴 찾기

다음 코드에서는 감지된 하나의 얼굴을 가져오고, 다른 얼굴 세트를 검색하여 일치 항목을 찾습니다. 일치 항목을 찾으면 일치하는 얼굴의 사각형 좌표를 콘솔에 출력합니다. 

### <a name="find-matches"></a>일치 항목 찾기

먼저 위의 섹션([이미지에서 얼굴 감지](#detect-faces-in-an-image))에서 코드를 실행하여 단일 얼굴에 대한 참조를 저장합니다. 그런 다음, 다음 코드를 실행하여 그룹 이미지에서 여러 얼굴에 대한 참조를 가져옵니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

그런 다음, 다음 코드 블록을 추가하여 그룹에서 첫 번째 얼굴의 인스턴스를 찾습니다. 이 동작을 수정하는 방법에 대한 자세한 내용은 [find_simply](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) 메서드를 참조하세요.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>일치 항목 출력

다음 코드를 사용하여 일치 항목 세부 정보를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>사람 그룹 만들기 및 학습

다음 코드에서는 서로 다른 세 개의 **Person** 개체를 사용하여 **PersonGroup**을 만듭니다. 각 **Person**을 예제 이미지 세트와 연결한 다음, 각 사용자를 인식할 수 있도록 학습시킵니다. 

### <a name="create-persongroup"></a>PersonGroup 만들기

이 시나리오를 단계별로 실행하려면 https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images 의 이미지를 프로젝트의 루트 디렉터리에 저장해야 합니다.

이 이미지 그룹에는 서로 다른 세 명의 사람에 해당하는 세 개의 얼굴 이미지 세트가 포함되어 있습니다. 코드에서는 세 개의 **Person** 개체를 정의하고, `woman`, `man` 및 `child`로 시작하는 이미지 파일에 연결합니다.

이미지가 설정되면 만들 **PersonGroup** 개체에 대한 레이블을 스크립트의 위쪽에 정의합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

그런 다음, 다음 코드를 Python 스크립트의 아래쪽에 추가합니다. 이 코드는 **PersonGroup** 및 세 개의 **Person** 개체를 만듭니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Person에 얼굴 할당

다음 코드는 접두사를 기준으로 이미지를 정렬하고, 얼굴을 감지하며, 해당 얼굴을 각 **Person** 개체에 할당합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>PersonGroup 학습

얼굴이 할당되면 각 **Person** 개체와 관련된 시각적 기능을 식별할 수 있도록 **PersonGroup**을 학습시켜야 합니다. 다음 코드는 비동기 **train** 메서드를 호출하고, 결과를 폴링하여 상태를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>얼굴 식별

다음 코드에서는 여러 얼굴이 있는 이미지를 가져오고, 해당 이미지에서 각 사람의 ID를 찾습니다. 감지된 각 얼굴을 얼굴 특징이 알려진 다른 **Person** 개체의 데이터베이스인 **PersonGroup**과 비교합니다.

> [!IMPORTANT]
> 이 예제를 실행하려면 먼저 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)에서 코드를 실행해야 합니다.

### <a name="get-a-test-image"></a>테스트 이미지 가져오기

다음 코드는 프로젝트 루트에서 _test-image-person-group.jpg_ 이미지를 살펴보고, 이미지에서 얼굴을 감지합니다. 이 이미지는 **PersonGroup** 관리에 사용된 이미지(https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images )에서 찾을 수 있습니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>얼굴 식별

**identify** 메서드는 감지된 얼굴의 배열을 사용하여 **PersonGroup**과 비교합니다. 감지된 얼굴을 **Person**과 일치시킬 수 있으면 결과가 저장됩니다. 이 코드는 자세한 일치 항목 결과를 콘솔에 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>얼굴 확인

확인 작업은 얼굴 ID와 다른 얼굴 ID 또는 **Person** 개체를 가져와서 동일한 사람에게 속하는지 여부를 확인합니다.

다음 코드는 두 개의 원본 이미지에서 얼굴을 감지한 다음, 대상 이미지에서 검색된 얼굴에 대해 확인합니다.

### <a name="get-test-images"></a>테스트 이미지 가져오기

다음 코드 블록은 확인 작업을 위한 소스 및 대상 이미지를 가리키는 변수를 선언합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>확인을 위한 얼굴 감지

다음 코드는 소스 및 대상 이미지에서 얼굴을 감지하고 변수에 저장합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>확인 결과 가져오기

다음 코드는 각 원본 이미지를 대상 이미지와 비교하고 동일한 사람에 속하는지 여부를 나타내는 메시지를 출력합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>데이터 마이그레이션용 스냅샷 만들기

스냅샷 기능을 사용하면 학습된 **PersonGroup**과 같은 저장된 얼굴 데이터를 다른 Azure Cognitive Services Face 구독으로 이동할 수 있습니다. 예를 들어 체험 구독을 사용하여 **PersonGroup** 개체를 만들었고 이제 유료 구독으로 마이그레이션하려는 경우 이 기능을 사용할 수 있습니다. 스냅샷 기능에 대한 광범위한 개요는 [얼굴 데이터 마이그레이션](../../Face-API-How-to-Topics/how-to-migrate-face-data.md)을 참조하세요.

다음 예제에서는 [사람 그룹 만들기 및 학습](#create-and-train-a-person-group)에서 만든 **PersonGroup**을 마이그레이션합니다. 해당 섹션을 먼저 완료하거나 사용자 고유의 Face 데이터 구문을 사용할 수 있습니다.

### <a name="set-up-target-subscription"></a>대상 구독 설정

먼저, Face 리소스가 포함된 두 번째 Azure 구독이 있어야 합니다. 이 작업은 [설정](#setting-up) 섹션의 단계에 따라 수행할 수 있습니다. 

그런 다음, 다음 변수를 스크립트의 위쪽 근처에 만듭니다. 또한 Azure 계정의 구독 ID와 새(대상) 계정의 키, 엔드포인트 및 구독 ID에 대한 새 환경 변수를 만들어야 합니다. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>대상 클라이언트 인증

스크립트의 뒷부분에서 현재 클라이언트 개체를 원본 클라이언트로 저장한 다음, 대상 구독에 대한 새 클라이언트 개체를 인증합니다. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>스냅샷 사용

나머지 스냅샷 작업은 비동기 함수 내에서 수행됩니다. 

1. 첫 번째 단계는 스냅샷을 **take**(수행)하여 원래 구독의 얼굴 데이터를 임시 클라우드 위치에 저장하는 것입니다. 이 메서드는 작업 상태를 쿼리하는 데 사용하는 ID를 반환합니다.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. 다음으로, 작업이 완료될 때까지 ID를 쿼리합니다.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    이 코드는 `wait_for_operation` 함수를 사용하여 별도로 정의해야 합니다.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. 비동기 함수로 돌아갑니다. **apply**(적용) 작업을 사용하여 얼굴 데이터를 대상 구독에 씁니다. 또한 이 메서드는 ID도 반환합니다.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. 다시 한 번, `wait_for_operation` 함수를 사용하여 작업이 완료될 때까지 ID를 쿼리합니다.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

이러한 단계가 완료되면 새 (대상) 구독에서 얼굴 데이터 구문에 액세스할 수 있습니다.

## <a name="run-the-application"></a>애플리케이션 실행

quickstart 파일의 `python` 명령을 사용하여 애플리케이션을 실행합니다.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

이 빠른 시작에서는 **PersonGroup**을 만들었으며, 이를 삭제하려면 스크립트에서 다음 코드를 실행합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

이 빠른 시작에서 스냅샷 기능을 사용하여 데이터를 마이그레이션한 경우 대상 구독에 저장된 **PersonGroup**도 삭제해야 합니다.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python용 Face 라이브러리를 사용하여 기본 작업을 수행하는 방법을 알아보았습니다. 다음으로 라이브러리에 대해 자세히 알아보려면 참조 설명서를 살펴보세요.

> [!div class="nextstepaction"]
> [Face API 참조(Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Face 서비스란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py)에서 확인할 수 있습니다.
