---
title: '이미지 분류 자습서: 모델 배포'
titleSuffix: Azure Machine Learning
description: 두 부분으로 구성된 시리즈의 두 번째인 이 자습서에서는 Azure Machine Learning을 사용하여 Python Jupyter Notebook에서 scikit-learn으로 이미지 분류 모델을 배포하는 방법을 보여줍니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 03/18/2020
ms.custom: seodec18
ms.openlocfilehash: 680b6ec17b65cd9452dd3bd5c0c470e395688cb8
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86025678"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>자습서: Azure Container Instance에서 이미지 분류 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 자습서는 **2부로 구성된 자습서 시리즈 중 제2부**입니다. [이전 자습서에서는](tutorial-train-models-with-aml.md), 기계 학습 모델을 학습한 후 클라우드에서 작업 영역에 모델을 등록합니다.  이제 웹 서비스로 배포할 준비가 되었습니다. 웹 서비스는 이미지로, 이 경우 Docker 이미지입니다. 점수 매기기 논리와 모델 자체를 캡슐화합니다. 

자습서의 이 부분에서는 Azure Machine Learning을 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * 테스트 환경 설정
> * 작업 영역에서 모델 검색
> * Container Instances에 모델 배포
> * 배포된 모델 테스트

Container Instances는 워크플로를 테스트 및 이해하기 위한 훌륭한 솔루션입니다. 확장성 있는 프로덕션 배포에는 Azure Kubernetes Service를 사용하는 것이 좋습니다. 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

>[!NOTE]
> 이 문서의 코드는 Azure Machine Learning SDK 버전 1.0.83으로 테스트되었습니다.

## <a name="prerequisites"></a>사전 요구 사항

Notebook을 실행하려면 먼저 [자습서(1부): 이미지 분류 모델 학습시키기](tutorial-train-models-with-aml.md)를 참조하세요.   그런 다음, 복제된 *tutorials/image-classification-mnist-data* 폴더에서 *img-classification-part2-deploy.ipynb* Notebook을 엽니다.

이 자습서는 고유의 [로컬 환경](how-to-configure-environment.md#local)에서 사용하려는 경우 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)에서도 사용할 수 있습니다.  사용자 환경에 `matplotlib` 및 `scikit-learn`을 설치했는지 확인합니다. 

> [!Important]
> 이 문서의 나머지 부분에는 Notebook에 표시되는 것과 동일한 콘텐츠가 포함되어 있습니다.  
>
> 코드를 실행할 때 함께 읽도록 하려면 지금 Jupyter Notebook으로 전환합니다.
> Notebook에서 단일 코드 셀을 실행하려면 코드 셀을 클릭하고 **Shift+Enter** 키를 누릅니다. 또는 상단 도구 모음에서 **모두 실행**을 선택하여 전체 Notebook을 실행합니다.

## <a name="set-up-the-environment"></a><a name="start"></a>환경 설정

테스트 환경 설정부터 시작합니다.

### <a name="import-packages"></a>패키지 가져오기

이 자습서에 필요한 Python 패키지를 가져옵니다.


```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt
 
import azureml.core

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

## <a name="deploy-as-web-service"></a>웹 서비스로 배포

모델을 ACI에서 호스팅되는 웹 서비스로 배포합니다. 

올바른 ACI용 환경을 빌드하기 위해서 다음을 제공합니다.
* 모델 사용 방법을 보여 주는 채점 스크립트
* ACI를 빌드하기 위한 구성 파일
* 이전에 학습을 진행한 모델

### <a name="create-scoring-script"></a>채점 스크립트 만들기

모델 사용법을 보여줄 수 있는 웹 서비스 호출에 사용되는 채점 스크립트(score.py)를 만듭니다.

다음 두 개의 필수 함수를 채점 스크립트에 포함해야 합니다.
* `init()` 함수는 일반적으로 모델을 전역 개체에 로드합니다. 이 함수는 Docker 컨테이너를 시작할 때 한 번만 실행됩니다. 

* `run(input_data)` 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용하지만 다른 형식도 지원됩니다.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

### <a name="create-configuration-file"></a>구성 파일 만들기

배포 구성 파일을 만들고 ACI 컨테이너에 필요한 RAM 기가바이트 및 CPU 개수를 지정합니다. 모델에 따라 다르긴 하지만 대개 다수의 모델에서 기본적으로 1기가바이트의 RAM 및 1개 코어면 충분합니다. 나중에 더 필요할 것 같면, 이미지를 다시 만들고 서비스를 다시 배포해야 합니다.


```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  "method" : "sklearn"}, 
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-aci"></a>ACI에서 배포
예상 완료 시간: **약 2~5분**

이미지를 구성하고 배포합니다. 다음 코드는 다음과 같은 단계를 거칩니다.

1. 학습 중에 저장된 환경(`tutorial-env`)을 사용하여 모델에 필요한 종속성이 포함된 환경 개체를 만듭니다.
1. 다음을 사용하여 모델을 웹 서비스로 배포하는 데 필요한 유추 구성을 만듭니다.
   * 채점 파일(`score.py`)
   * 이전 단계에서 만든 환경 개체
1. 모델을 ACI 컨테이너에 배포합니다.
1. 웹 서비스 HTTP 엔드포인트를 가져옵니다.


```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-svc3', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

REST 클라이언트 호출을 수락하는 채점 웹 서비스의 HTTP 엔드포인트를 가져옵니다. 이 엔드포인트는 웹 서비스를 테스트하거나 애플리케이션에 통합하려는 모든 사용자와 공유할 수 있습니다.


```python
print(service.scoring_uri)
```

## <a name="test-the-model"></a>모델 테스트


### <a name="download-test-data"></a>테스트 데이터 다운로드
테스트 데이터를 **./data/** 디렉터리에 다운로드합니다.


```python
import os
from azureml.core import Dataset
from azureml.opendatasets import MNIST

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

mnist_file_dataset = MNIST.get_file_dataset()
mnist_file_dataset.download(data_folder, overwrite=True)
```

### <a name="load-test-data"></a>테스트 데이터 로드

자습서 학습 중에 만들어진 **./data/** 에서 테스트 데이터를 로드합니다.


```python
from utils import load_data
import os
import glob

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-images-idx3-ubyte.gz"), recursive=True)[0], False) / 255.0
y_test = load_data(glob.glob(os.path.join(data_folder,"**/t10k-labels-idx1-ubyte.gz"), recursive=True)[0], True).reshape(-1)
```

### <a name="predict-test-data"></a>테스트 데이터 예측

예측을 가져오기 위해 모델에 테스트 데이터 세트를 제공합니다.


다음 코드는 다음과 같은 단계를 거칩니다.
1. 데이터를 JSON 배열로 ACI에서 호스팅되는 웹 서비스에 보냅니다. 

1. SDK의 `run` API를 사용하여 서비스를 호출합니다. Curl과 같은 HTTP 도구를 사용하여 원시 호출을 수행할 수도 있습니다.


```python
import json
test = json.dumps({"data": X_test.tolist()})
test = bytes(test, encoding='utf8')
y_hat = service.run(input_data=test)
```

###  <a name="examine-the-confusion-matrix"></a>혼동 행렬을 검사합니다

테스트 집합에서 얼마나 많은 샘플 올바르게 분류되었는지를 확인하기 위해 혼동 행렬을 생성합니다. 잘못된 예측에 대해 잘못 분류된 값을 확인합니다.


```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

출력은 혼동 행렬을 보여 줍니다.

```output
[[ 960    0    1    2    1    5    6    3    1    1]
 [   0 1112    3    1    0    1    5    1   12    0]
 [   9    8  920   20   10    4   10   11   37    3]
 [   4    0   17  921    2   21    4   12   20    9]
 [   1    2    5    3  915    0   10    2    6   38]
 [  10    2    0   41   10  770   17    7   28    7]
 [   9    3    7    2    6   20  907    1    3    0]
 [   2    7   22    5    8    1    1  950    5   27]
 [  10   15    5   21   15   27    7   11  851   12]
 [   7    8    2   13   32   13    0   24   12  898]]
Overall accuracy: 0.9204
```

`matplotlib` 사용하여 혼동 행렬을 그래프로 표시합니다. 이 그래프에서 X축은 실제 값을 나타내고 Y축은 예측된 값을 나타냅니다. 각 눈금의 색은 오류 비율을 나타냅니다. 색이 밝을수록 오류 비율이 높습니다. 예를 들어 다수의 5가 3으로 잘못 분류되어 있습니다. 따라서 (5,3)에서 밝은 눈금이 표시됩니다.

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![혼동 행렬을 보여 주는 차트](./media/tutorial-deploy-models-with-aml/confusion.png)


## <a name="show-predictions"></a>예측 표시

테스트 데이터에서 무작위로 샘플링한 30개 이미지를 사용하여 배포된 모델을 테스트할 수 있습니다.  


1. 반환된 예측을 인쇄하고 입력 이미지와 함께 그립니다. 빨강 글꼴 및 역방향 이미지(검정 바탕에 흰색)는 오분류된 샘플을 강조 표시하는 데 사용됩니다. 

 모델 정확도가 높으므로 다음 코드를 몇 번 실행해야 오분류된 샘플을 볼 수 있을 수 있습니다.



```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize = (20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

원시 HTTP 요청을 보내 웹 서비스를 테스트할 수도 있습니다.


```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서 및 탐색을 위해 리소스 그룹과 작업 영역을 유지하려면 이 API 호출을 사용하여 Container Instances 배포만 삭제하면 됩니다.

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>다음 단계

+ [Azure Machine Learning에 대한 배포 옵션](how-to-deploy-and-where.md)을 모두 알아봅니다.
+ [웹 서비스에 대한 클라이언트를 만드는 방법](how-to-consume-web-service.md)에 대해 알아봅니다.
+  [많은 양의 데이터에 대해 비동기적으로 예측](how-to-use-parallel-run-step.md)합니다.
+ [Application Insights](how-to-enable-app-insights.md)를 사용하여 Azure Machine Learning 모델을 모니터링합니다.
+ [자동 알고리즘 선택](tutorial-auto-train-models.md) 자습서를 사용해보세요. 
