---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: 1bf0ecbc996fe853a6ca1d0ed5a749c798383146
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508591"
---
이 문서에서는 Python과 함께 Custom Vision 클라이언트 라이브러리를 사용하여 이미지 분류 모델을 빌드하는 방법을 보여줍니다. 프로젝트를 만든 후에는 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 게시된 예측 엔드포인트 URL을 확보하고, 이 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. Python 애플리케이션을 빌드하기 위한 템플릿으로 이 예제를 사용하세요. 코드 _없이_ 분류 모델을 빌드하고 사용하는 방법을 알아보려면 [브라우저 기반 가이드](../../getting-started-build-a-classifier.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Python 2.7+ 또는 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/) 도구
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>Custom Vision 클라이언트 라이브러리 설치

Python용 Custom Vision Service 클라이언트 라이브러리를 설치하려면 PowerShell에서 다음 명령을 실행합니다.

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](../../includes/get-keys.md)]

[!INCLUDE [python-get-images](../../includes/python-get-images.md)]

## <a name="add-the-code"></a>코드 추가

원하는 프로젝트 디렉터리에 *sample.py*라는 새 파일을 만듭니다.

### <a name="create-the-custom-vision-service-project"></a>Custom Vision Service 프로젝트 만들기

새 Custom Vision Service 프로젝트를 만드는 다음 코드를 스크립트에 추가합니다. 구독 키를 적절한 정의에 삽입합니다. 또한 Custom Vision 웹 사이트의 설정 페이지에서 엔드포인트 URL을 가져옵니다.

프로젝트를 만들 때 다른 옵션을 지정하려면 [create_project](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) 메서드를 참조하세요([분류자 빌드](../../getting-started-build-a-classifier.md) 웹 포털 가이드에 설명되어 있음).  

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateBatch, ImageFileCreateEntry
from msrest.authentication import ApiKeyCredentials

ENDPOINT = "<your API endpoint>"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>프로젝트에서 태그 만들기

프로젝트의 분류 태그를 만들려면 *sample.py* 파일의 끝에 다음 코드를 추가합니다.

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

프로젝트에 샘플 이미지를 추가하려면 태그를 만든 후 다음 코드를 삽입합니다. 이 코드는 해당 태그를 사용하여 각 이미지를 업로드합니다. 단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있습니다.

> [!NOTE]
> 이전에 Cognitive Services Python SDK 샘플 리포지토리를 다운로드한 위치에 따라 이미지 경로를 변경해야 합니다.

```Python
base_image_url = "<path to repo directory>/cognitive-services-python-sdk-samples/samples/vision/"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, ImageFileCreateBatch(images=image_list))
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>분류자 학습 및 게시

이 코드는 예측 모델의 첫 번째 반복을 만든 다음, 해당 반복을 예측 엔드포인트에 게시합니다. 게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>게시된 반복을 예측 엔드포인트에서 가져와서 사용합니다.

예측 엔드포인트에 이미지를 보내고 예측을 검색하려면 파일의 끝에 다음 코드를 추가합니다.

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient
from msrest.authentication import ApiKeyCredentials

# Now there is a trained endpoint that can be used to make a prediction
prediction_credentials = ApiKeyCredentials(in_headers={"Prediction-key": prediction_key})
predictor = CustomVisionPredictionClient(ENDPOINT, prediction_credentials)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>애플리케이션 실행

*sample.py* 파일을 실행합니다.

```powershell
python sample.py
```

애플리케이션의 출력은 다음 텍스트와 비슷할 것입니다.

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

그런 다음, 테스트 이미지( **<base_image_url>images/Test/** 에 있음)에 태그가 적절하게 지정되는지 확인할 수 있습니다. [Custom Vision 웹 사이트](https://customvision.ai)로 돌아가서 새로 만든 프로젝트의 현재 상태를 살펴볼 수도 있습니다.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>다음 단계

지금까지 개체 검색 프로세스의 모든 단계를 코드로 수행하는 방법을 살펴보았습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)
