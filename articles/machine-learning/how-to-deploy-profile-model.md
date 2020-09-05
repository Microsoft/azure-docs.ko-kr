---
title: 모델 메모리 및 CPU 사용량 프로 파일링
titleSuffix: Azure Machine Learning
description: 모델 메모리와 CPU 사용량을 프로 파일링 하는 방법 알아보기
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.openlocfilehash: a3aed23441df225316f52eb3acb1387cbba6d807
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935588"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>모델을 프로 파일링 하 여 리소스 사용률 확인

이 문서에서는 모델에 기계 학습을 프로 파일링 하 여 모델을 웹 서비스로 배포할 때 모델에 할당 해야 하는 CPU 및 메모리 양을 결정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 Azure Machine Learning를 사용 하 여 모델을 학습 하 고 등록 했다고 가정 합니다. Azure Machine Learning를 사용 하 여 scikit 모델을 학습 하 고 등록 하는 방법에 대 한 예제는 [여기의 샘플 자습서](how-to-train-scikit-learn.md) 를 참조 하세요.

## <a name="run-the-profiler"></a>프로파일러 실행

모델을 등록 하 고 배포에 필요한 다른 구성 요소를 준비한 후에는 배포 된 서비스가 필요로 하는 CPU 및 메모리를 결정할 수 있습니다. 프로 파일링은 모델을 실행 하는 서비스를 테스트 하 고 CPU 사용량, 메모리 사용량, 응답 대기 시간 등의 정보를 반환 합니다. 또한 리소스 사용량에 따라 CPU 및 메모리에 대 한 권장 사항을 제공 합니다.

모델을 프로 파일링 하려면 다음이 필요 합니다.
* 등록 된 모델입니다.
* 입력 스크립트 및 유추 환경 정의에 따라 유추 구성입니다.
* 단일 열 표 형식 데이터 집합. 각 행에는 샘플 요청 데이터를 나타내는 문자열이 포함 됩니다.

> [!IMPORTANT]
> 이 시점에서 요청 데이터를 문자열로 간주 하는 서비스 프로 파일링만 지원 합니다. 예를 들어 문자열 직렬화 된 json, 텍스트, 문자열 직렬화 된 이미지 등이 있습니다. 데이터 집합 (문자열)의 각 행의 내용이 HTTP 요청의 본문에 배치 되 고 점수 매기기를 위해 모델을 캡슐화 하는 서비스로 전송 됩니다.

> [!IMPORTANT]
> ChinaEast2 및 USGovArizona 지역에서 최대 2 개의 Cpu 프로 파일링만 지원 합니다.

다음은 들어오는 요청 데이터가 serialize 된 json을 포함할 것으로 예상 하는 서비스를 프로 파일링 하기 위해 입력 데이터 집합을 생성 하는 방법의 예입니다. 이 경우 동일한 요청 데이터 콘텐츠의 100 인스턴스를 기반으로 하는 데이터 집합을 만들었습니다. 실제 시나리오에서는 다양 한 입력을 포함 하는 큰 데이터 집합을 사용 하는 것이 좋습니다. 특히 모델 리소스 사용량/동작이 입력에 따라 달라 지는 경우입니다.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

샘플 요청 데이터를 포함 하는 데이터 집합을 준비 했으면 유추 구성을 만듭니다. 유추 구성은 score.py 및 환경 정의를 기반으로 합니다. 다음 예제에서는 유추 구성을 만들고 프로 파일링을 실행 하는 방법을 보여 줍니다.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


다음 명령은 CLI를 사용 하 여 모델을 프로 파일링 하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> 프로 파일링에서 반환 되는 정보를 유지 하려면 모델에 대 한 태그나 속성을 사용 합니다. 태그 또는 속성을 사용 하 여 모델 레지스트리에서 모델에 데이터를 저장 합니다. 다음 예에서는 및 정보를 포함 하는 새 태그를 추가 하는 방법을 보여 줍니다 `requestedCpu` `requestedMemoryInGb` .
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>다음 단계

* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대 한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)

