---
title: 배포 문제 해결 가이드
titleSuffix: Azure Machine Learning service
description: 해결, 해결 및 Azure Machine Learning 서비스를 사용 하 여 ACI 및 AKS를 사용 하 여 일반적인 Docker 배포 오류 해결 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 815be7400e0a0560ace7e07b317aeb25c2feacd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817425"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Azure Machine Learning AKS 및 ACI 서비스 배포 문제 해결

이 문서에서는 Azure Machine Learning 서비스를 사용하여 ACI(Azure Container Instances) 및 AKS(Azure Kubernetes Service)에서 일반적인 Docker 배포 오류를 해결하는 방법을 알아봅니다.

Azure Machine Learning 서비스에서 모델을 배포할 때 시스템에서 많은 작업을 수행합니다. 이 과정은 매우 복잡한 일련의 이벤트이며 종종 문제가 발생합니다. 배포 작업은 다음과 같습니다.

1. 모델을 작업 영역 모델 레지스트리에 등록합니다.

2. 다음을 포함하여 Docker 이미지를 빌드합니다.
    1. 레지스트리에서 등록 모델을 다운로드합니다. 
    2. 환경 yaml 파일에 지정된 종속성 기반의 Python 환경을 사용하여 dockerfile을 만듭니다.
    3. dockerfile에 제공하는 모델 파일 및 채점 스크립트를 추가합니다.
    4. dockerfile을 사용하여 새 Docker 이미지를 빌드합니다.
    5. 작업 영역과 연결된 Azure Container Registry에 Docker 이미지를 등록합니다.

3. ACI(Azure Container Instance) 서비스 또는 AKS(Azure Kubernetes Service)에 Docker 이미지를 배포합니다.

4. ACI 또는 AKS에서 새 컨테이너(또는 여러 개의 새 컨테이너)를 시작합니다. 

이 프로세스에 대한 자세한 정보는 [모델 관리](concept-model-management-and-deployment.md) 소개를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

문제가 발생할 경우 가장 먼저 할 일은 배포 작업을 개별 단계로 분리하여(이전 설명 참조) 문제를 격리하는 것입니다. 

사용 하는 경우에 유용 합니다 `Webservice.deploy` API 또는 `Webservice.deploy_from_model` API, 이러한 함수는 단일 작업에 앞서 언급 한 단계를 그룹화 하므로 합니다. 일반적으로 이러한 Api는 편리 하지만 단계를 사용 하 여 대체 하 여 문제를 해결할 때 중단 하는 것은 API 호출 아래.

1. 모델을 등록합니다. 다음은 샘플 코드입니다.

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. 이미지를 빌드합니다. 다음은 샘플 코드입니다.

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. 이미지를 서비스로 배포합니다. 다음은 샘플 코드입니다.

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

배포 프로세스를 개별 작업으로 분리한 후에는 가장 일반적인 오류 중 일부를 볼 수 있습니다.

## <a name="image-building-fails"></a>이미지 빌드 실패
시스템에서 Docker 이미지를 빌드할 수 없는 경우 단서가 될 수 있는 오류 메시지와 함께 `image.wait_for_creation()` 호출이 실패합니다. 또한 이미지 빌드 로그에서 오류에 대한 자세한 내용을 찾을 수 있습니다. 아래는 이미지 빌드 로그 uri를 검색하는 방법을 보여주는 샘플 코드입니다.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```
이미지 로그 uri는 Azure Blob Storage에 저장된 로그 파일을 가리키는 SAS URL입니다. 간단하게 uri를 복사하여 브라우저 창에 붙여넣는 방법으로 로그 파일을 다운로드하여 볼 수 있습니다.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault 액세스 정책 및 Azure Resource Manager 템플릿

이미지 빌드도 Azure Key vault 액세스 정책 관련 문제로 인해 실패할 수 있습니다. Azure Resource Manager 템플릿을 사용 하 여 연결 된 리소스 (Azure Key Vault를 포함)을 여러 번 확인 하 고 작업 영역을 만들 때 발생할 수 있습니다. 예를 들어, 템플릿을 사용 하 여를 여러 번 연속 통합 및 배포 파이프라인의 일부로 동일한 매개 변수를 사용 하 여 합니다.

템플릿을 통해 대부분의 리소스 생성 작업은 idempotent 상태, 되지만 키 자격 증명 모음 액세스 정책 템플릿이 사용 될 때마다를 지웁니다. 액세스를 중단이 Key Vault를 사용 하는 모든 기존 작업 영역에 있습니다. 이 위치 새 이미지를 만들려고 할 때 오류가 발생 합니다. 다음은 받을 수 있는 오류의 예입니다.

__포털__:
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__SDK__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__:
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

이 문제를 방지 하려면 좋습니다 다음 방법 중 하나:

* 동일한 매개 변수에 대 한 템플릿을 두 번 이상는 배포 하지 않습니다. 또는 기존 리소스를 다시 만드는 데 템플릿을 사용 하기 전에 삭제 합니다.
* 키 자격 증명 모음 액세스 정책을 검토 하 고 설정 하는 데 사용 된 `accessPolicies` 템플릿의 속성입니다.
* Key Vault 리소스 이미 있는지 확인 합니다. 이 템플릿을 통해 다시 만들지 않습니다. 예를 들어, 이미 있는 경우 키 자격 증명 모음 리소스의 생성을 사용 하지 않도록 할 수 있도록 매개 변수를 추가 합니다.

## <a name="service-launch-fails"></a>서비스 시작 실패
이미지가 성공적으로 빌드되면 시스템이 배포 구성에 따라 ACI 또는 AKS에서 컨테이너를 시작하려고 시도합니다. 단순한 형태의 단일 컨테이너 배포 이므로 ACI 배포를 먼저 시도 하는 것이 좋습니다. 이 방법으로 모든 AKS 관련 문제를 제외할 수 있습니다.

컨테이너 시작 프로세스의 일부로, 시스템에서 채점 스크립트의 `init()` 함수를 호출합니다. `init()` 함수에 catch되지 않은 예외가 있는 경우 오류 메시지에 **CrashLoopBackOff** 오류가 표시될 수 있습니다. 아래는 문제를 해결하는 팁입니다.

### <a name="inspect-the-docker-log"></a>Docker 로그 검사
서비스 개체에서 자세한 Docker 엔진 로그 메시지를 인쇄할 수 있습니다.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Docker 이미지를 로컬로 디버그
Docker 로그에 오류에 대한 정보가 충분히 제공되지 않는 경우가 가끔 있습니다. 한 단계 더 나아가서 빌드된 Docker 이미지를 끌어오고, 로컬 컨테이너를 시작하고, 라이브 컨테이너 내부에서 대화형으로 직접 디버그할 수 있습니다. 로컬 컨테이너를 시작하려면 로컬에서 실행 중인 Docker 엔진이 있어야 하며, [azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 설치되어 있으면 훨씬 쉽습니다.

먼저 이미지 위치를 확인해야 합니다.

```python
# print image location
print(image.image_location)
```

이미지 위치는 `<acr-name>.azurecr.io/<image-name>:<version-number>` 형식입니다(예: `myworkpaceacr.azurecr.io/myimage:3`). 

이제 명령줄 창으로 이동합니다. azure-cli가 설치된 경우 다음 명령을 입력하여 이미지가 저장되는 작업 영역과 연결된 ACR(Azure Container Registry)에 로그인할 수 있습니다. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
azure-cli가 설치되지 않은 경우 `docker login` 명령을 사용하여 ACR에 로그인할 수 있습니다. 하지만 먼저 Azure Portal에서 ACR의 사용자 이름 및 암호를 검색해야 합니다.

ACR에 로그인했으면 Docker 이미지를 끌어오고 컨테이너를 로컬로 시작한 다음, `docker run` 명령을 사용하여 디버깅을 위한 bash 세션을 시작할 수 있습니다.

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

컨테이너를 실행 중인 Bash 세션을 시작하면 `/var/azureml-app` 폴더에서 채점 스크립트를 찾을 수 있습니다. 그런 다음, Python 세션을 시작하여 채점 스크립트를 디버그할 수 있습니다. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
스크립트를 수정할 텍스트 편집기가 필요한 경우 vim, nano, Emacs 또는 선호하는 다른 편집기를 설치하면 됩니다.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

로컬로 웹 서비스를 시작하고 HTTP 트래픽을 해당 서비스로 보낼 수도 있습니다. Docker 컨테이너의 Flask 서버는 포트 5001에서 실행됩니다. 호스트 머신에서 사용할 수 있는 다른 포트에 매핑할 수 있습니다.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>함수 실패: get_model_path()
종종 채점 스크립트의 `init()` 함수에서, 컨테이너의 모델 파일 또는 모델 파일의 폴더를 찾기 위해 `Model.get_model_path()` 함수가 호출됩니다. 모델 파일 또는 폴더를 찾을 수 없는 경우 이로 인해 문제가 발생합니다. 이 오류를 디버그하는 가장 쉬운 방법은 컨테이너 셸에서 아래의 Python 코드를 실행하는 것입니다.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

이렇게 하면 채점 스크립트가 모델 파일 또는 폴더를 찾을 것으로 예상되는 컨테이너의 로컬 경로(`/var/azureml-app`의 상대 경로)가 인쇄됩니다. 그러면 예상 위치에 파일 또는 폴더가 실제로 있는지 확인할 수 있습니다.

로깅 수준을 디버그로 설정하면 추가 정보가 기록되어 오류를 식별하는 데 유용할 수 있습니다.

## <a name="function-fails-runinputdata"></a>함수 실패: run(input_data)
서비스가 성공적으로 배포되었지만 채점 엔드포인트에 데이터를 게시할 때 크래시가 발생하는 경우 오류를 catch하는 명령문을 `run(input_data)` 함수에 추가하면 구체적인 오류 메시지가 반환됩니다. 예를 들면 다음과 같습니다.

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```
**참고**: `run(input_data)` 호출에서 오류 메시지를 반환하는 방법은 디버깅 용도로만 사용해야 합니다. 프로덕션 환경에서 이 방법을 사용하는 것은 보안에 좋지 않습니다.

## <a name="http-status-code-503"></a>HTTP 상태 코드 503

Azure Kubernetes Service 배포에 복제본 추가 부하를 지원 하기 위해 추가할 수 있는 자동 크기 조정을 지원 합니다. 그러나 autoscaler 처리 하도록 설계 되었습니다 **점진적** 부하 변경을 합니다. 초당 요청에서 큰 스파이크를 수신 하는 경우 클라이언트의 HTTP 상태 코드 503 나타날 수 있습니다.

503 상태 코드를 방지 하는 데 도움이 되는 두 가지 있습니다.

* 변경의 사용률 수준은 자동 크기 조정은 새 복제본을 만듭니다.
    
    기본적으로 자동 크기 조정 목표 사용률 설정 70%, 즉, 서비스 요청 RPS (초당) 최대 30%의 급증을 처리할 수 있도록 합니다. 설정 하 여 사용률 목표를 조정할 수 있습니다는 `autoscale_target_utilization` 더 낮은 값입니다.

    > [!IMPORTANT]
    > 이 변경에 만들려는 복제본 이어지지 *빠르게*합니다. 대신, 낮은 사용률 임계값 생성 됩니다. 서비스를 활용 하는 70%가 될 때까지 대기 하는 대신 값을 30%로 변경 하면 30% 사용률 발생할 때 만들어지는 복제본입니다.
    
    웹 서비스에서 이미 현재 최대 복제본을 사용 하 고 상태 코드 503 계속 표시 되는 경우 향상 된 `autoscale_max_replicas` 복제본의 최대 수를 늘리려면 값입니다.

* 복제본의 최소 수를 변경 합니다. 최소 복제본 증가 들어오는 급증을 처리 하려면 더 큰 풀을 제공 합니다.

    복제본의 최소 수를 늘리려면 설정 `autoscale_min_replicas` 을 더 높은 값입니다. 다음 코드를 사용 하 여 프로젝트에 특정 값을 사용 하 여 값을 바꿀 필요한 복제본 계산할 수 있습니다.

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > 요청 급증을 처리할 수 있는 새 최소 복제본 보다 더 큰 경우에 따라 503s 다시 나타날 수 있습니다. 예를 들어,에 서비스 증가 트래픽으로 최소 복제본을 높이기 위해 해야 할 수 있습니다.

설정에 대 한 자세한 내용은 `autoscale_target_utilization`, `autoscale_max_replicas`, 및 `autoscale_min_replicas` 를 참조 합니다 [AksWebservice](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 모듈 참조.


## <a name="next-steps"></a>다음 단계

배포에 대해 자세히 알아보세요. 
* [배포 방법 및 위치](how-to-deploy-and-where.md)

* [자습서: 모델 학습 및 배포](tutorial-train-models-with-aml.md)
