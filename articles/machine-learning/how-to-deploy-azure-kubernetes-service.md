---
title: Azure Kubernetes Service에 모델을 배포 하는 방법
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service를 사용 하 여 Azure Machine Learning 모델을 웹 서비스로 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: db2e80ebb6cbe5f31f2d99a1403a15daf38fd877
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722410"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터에 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning를 사용 하 여 AKS (Azure Kubernetes Service)에서 웹 서비스로 모델을 배포 하는 방법에 대해 알아봅니다. Azure Kubernetes 서비스는 대규모 프로덕션 배포에 적합 합니다. 다음 기능이 하나 이상 필요한 경우 Azure Kubernetes service를 사용 합니다.

- __빠른 응답 시간__.
- 배포 __된 서비스의 자동__ 크기 조정.
- GPU 및 필드 프로그래밍 가능 게이트 배열 (FPGA)과 같은 __하드웨어 가속__ 옵션입니다.

> [!IMPORTANT]
> 클러스터 크기 조정은 Azure Machine Learning SDK를 통해 제공 되지 않습니다. AKS 클러스터의 노드 크기를 조정 하는 방법에 대 한 자세한 내용은 [AKS 클러스터의 노드 수 크기 조정](../aks/scale-cluster.md)을 참조 하세요.

Azure Kubernetes Service에 배포 하는 경우 __작업 영역에 연결__된 AKS 클러스터에 배포 합니다. AKS 클러스터를 작업 영역에 연결 하는 방법에는 두 가지가 있습니다.

* Azure Machine Learning SDK, Machine Learning CLI 또는 [Azure Machine Learning studio](https://ml.azure.com)를 사용 하 여 AKS 클러스터를 만듭니다. 이 프로세스는 클러스터를 작업 영역에 자동으로 연결 합니다.
* 기존 AKS 클러스터를 Azure Machine Learning 작업 영역에 연결 합니다. Azure Machine Learning SDK, Machine Learning CLI 또는 Azure Machine Learning studio를 사용 하 여 클러스터를 연결할 수 있습니다.

> [!IMPORTANT]
> 생성 또는 첨부 파일 프로세스는 일회성 작업입니다. AKS 클러스터가 작업 영역에 연결 되 면 배포에 사용할 수 있습니다. 더 이상 필요 하지 않은 경우 AKS 클러스터를 분리 하거나 삭제할 수 있습니다. 분리 또는 삭제 된 후에는 더 이상 클러스터에 배포할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

- 작업 영역에 등록 된 machine learning 모델입니다. 등록 된 모델이 없는 경우 모델을 배포 하 [는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

- 이 문서의 __Python__ 코드 조각에서는 다음 변수가 설정 되었다고 가정 합니다.

    * `ws`-작업 영역으로 설정 합니다.
    * `model`-등록 된 모델로 설정 합니다.
    * `inference_config`-모델에 대 한 유추 구성으로 설정 합니다.

    이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- 이 문서의 __CLI__ 코드 조각에서는 `inferenceconfig.json` 문서를 만들었다고 가정 합니다. 이 문서를 만드는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="create-a-new-aks-cluster"></a>새 AKS 클러스터 만들기

**예상 시간**: 약 20 분.

작업 영역에 대 한 일회성 프로세스는 AKS 클러스터를 만들거나 연결 하는 것입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터 또는 클러스터를 포함 하는 리소스 그룹을 삭제 하는 경우 다음에를 배포 해야 할 때 새 클러스터를 만들어야 합니다. 여러 AKS 클러스터를 작업 영역에 연결할 수 있습니다.

> [!TIP]
> Azure Virtual Network를 사용 하 여 AKS 클러스터를 보호 하려면 먼저 가상 네트워크를 만들어야 합니다. 자세한 내용은 [Azure Virtual Network를 사용 하 여 보안 실험 및 유추](how-to-enable-virtual-network.md#aksvnet)를 참조 하세요.

프로덕션 대신 __개발__, __유효성 검사__및 __테스트__ 를 위해 AKS 클러스터를 만들려는 경우 개발 __테스트__에 대 한 __클러스터 목적__ 을 지정할 수 있습니다.

> [!WARNING]
> `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`를 설정 하는 경우 생성 되는 클러스터는 프로덕션 수준 트래픽에 적합 하지 않으며 유추 시간이 늘어날 수 있습니다. 또한 개발/테스트 클러스터는 내결함성을 보장 하지 않습니다. 개발/테스트 클러스터에 2 개 이상의 가상 Cpu를 권장 합니다.

다음 예에서는 SDK 및 CLI를 사용 하 여 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

**SDK 사용**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)의 경우 `agent_count` 및 `vm_size`에 대 한 사용자 지정 값을 선택 하 고 `cluster_purpose` `DEV_TEST`되지 않는 경우 `agent_count`에 `vm_size` 곱한 값이 12 개의 가상 cpu 보다 크거나 같은지 확인 해야 합니다. 예를 들어 가상 Cpu가 4 개 있는 `vm_size` "Standard_D3_v2"를 사용 하는 경우 3 이상의 `agent_count`를 선택 해야 합니다.
>
> Azure Machine Learning SDK는 AKS 클러스터의 크기 조정을 지원 하지 않습니다. 클러스터의 노드 크기를 조정 하려면 Azure Machine Learning studio에서 AKS 클러스터에 대 한 UI를 사용 합니다. 클러스터의 VM 크기가 아니라 노드 수만 변경할 수 있습니다.

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**CLI 사용**

```azurecli
az ml computetarget create aks -n myaks
```

자세한 내용은 [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference를 참조 하세요.

## <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터 연결

**예상 시간:** 약 5 분.

Azure 구독에 AKS 클러스터가 이미 있고 버전이 1.17 이하인 경우이를 사용 하 여 이미지를 배포할 수 있습니다.

> [!TIP]
> 기존 AKS 클러스터는 Azure Machine Learning 작업 영역 이외의 Azure 지역에 있을 수 있습니다.
>
> Azure Virtual Network를 사용 하 여 AKS 클러스터를 보호 하려면 먼저 가상 네트워크를 만들어야 합니다. 자세한 내용은 [Azure Virtual Network를 사용 하 여 보안 실험 및 유추](how-to-enable-virtual-network.md#aksvnet)를 참조 하세요.

AKS 클러스터를 작업 영역에 연결 하는 경우 `cluster_purpose` 매개 변수를 설정 하 여 클러스터를 사용 하는 방법을 정의할 수 있습니다.

`cluster_purpose` 매개 변수를 설정 하지 않거나 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`를 설정한 경우 클러스터에는 사용 가능한 가상 Cpu가 12 개 이상 있어야 합니다.

`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`를 설정 하는 경우 클러스터에는 12 개의 가상 Cpu가 필요 하지 않습니다. 개발/테스트에 2 개 이상의 가상 Cpu를 권장 합니다. 그러나 개발/테스트용으로 구성 된 클러스터는 프로덕션 수준 트래픽에 적합 하지 않으며 유추 시간이 늘어날 수 있습니다. 또한 개발/테스트 클러스터는 내결함성을 보장 하지 않습니다.

> [!WARNING]
> 작업 영역에서 동일한 AKS 클러스터에 대 한 동시 첨부 파일을 여러 개 만들지 마십시오. 예를 들어 두 개의 다른 이름을 사용 하 여 하나의 AKS 클러스터를 작업 영역에 연결 합니다. 새 첨부 파일은 이전의 기존 첨부 파일을 중단 합니다.
>
> SSL 또는 기타 클러스터 구성 설정을 변경 하는 등 AKS 클러스터를 다시 연결 하려면 먼저 [AksCompute ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)를 사용 하 여 기존 첨부 파일을 제거 해야 합니다.

Azure CLI 또는 포털을 사용 하 여 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [AKS 클러스터 만들기 (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 만들기 (포털)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

다음 예에서는 기존 AKS 클러스터를 작업 영역에 연결 하는 방법을 보여 줍니다.

**SDK 사용**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AksCompute () attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**CLI 사용**

CLI를 사용 하 여 기존 클러스터를 연결 하려면 기존 클러스터의 리소스 ID를 가져와야 합니다. 이 값을 가져오려면 다음 명령을 사용 합니다. `myexistingcluster`을 AKS 클러스터의 이름으로 바꿉니다. `myresourcegroup`를 클러스터를 포함 하는 리소스 그룹으로 바꿉니다.

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

이 명령은 다음 텍스트와 유사한 값을 반환합니다.

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

기존 클러스터를 작업 영역에 연결 하려면 다음 명령을 사용 합니다. `aksresourceid`를 이전 명령에서 반환 된 값으로 바꿉니다. `myresourcegroup`를 작업 영역을 포함 하는 리소스 그룹으로 바꿉니다. `myworkspace`를 작업 영역 이름으로 바꿉니다.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

자세한 내용은 [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference를 참조 하세요.

## <a name="deploy-to-aks"></a>AKS에 배포

Azure Kubernetes Service에 모델을 배포 하려면 필요한 계산 리소스를 설명 하는 __배포 구성을__ 만듭니다. 예를 들면 코어 수와 메모리입니다. 모델 및 웹 서비스를 호스트 하는 데 필요한 환경을 설명 하는 __유추 구성__도 필요 합니다. 유추 구성을 만드는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

### <a name="using-the-sdk"></a>SDK 사용

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [모델. 배포](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [웹 서비스. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLI 사용

CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. `myaks`을 AKS 계산 대상의 이름으로 바꿉니다. `mymodel:1`를 등록 된 모델의 이름 및 버전으로 바꿉니다. `myservice`를이 서비스에 제공 하는 이름으로 바꿉니다.

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference를 참조 하세요.

### <a name="using-vs-code"></a>VS Code 사용

VS Code 사용에 대 한 자세한 내용은 [VS Code 확장을 통해 AKS에 배포를](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)참조 하세요.

> [!IMPORTANT]
> VS Code를 통해 배포 하려면 AKS 클러스터를 미리 만들거나 작업 영역에 미리 연결 해야 합니다.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>제어 된 롤아웃 (미리 보기)을 사용 하 여 AKS에 모델 배포
엔드포인트을 사용 하 여 제어 된 방식으로 모델 버전을 분석 하 고 승격 합니다. 단일 엔드포인트 뒤에 최대 6 개의 버전을 배포 하 고 배포 된 각 버전에 대 한 점수 매기기 트래픽의%를 구성 합니다. App insights를 사용 하도록 설정 하 여 엔드포인트 및 배포 된 버전의 작업 메트릭을 볼 수 있습니다.

### <a name="create-an-endpoint"></a>엔드포인트 만들기
모델을 배포할 준비가 되 면 점수 매기기 엔드포인트을 만들고 첫 번째 버전을 배포 합니다. 아래 단계에서는 SDK를 사용 하 여 엔드포인트을 배포 하 고 만드는 방법을 보여 줍니다. 첫 번째 배포는 기본 버전으로 정의 됩니다. 즉, 모든 버전에서 지정 되지 않은 트래픽 백분위 수가 기본 버전으로 이동 합니다.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>엔드포인트에 버전 업데이트 및 추가

다른 버전을 엔드포인트에 추가 하 고 버전으로 이동 하는 점수 매기기 트래픽 백분위 수를 구성 합니다. 두 가지 유형의 버전, 즉 컨트롤과 처리 버전이 있습니다. 단일 컨트롤 버전을 비교 하는 데 도움이 되는 처리 버전이 여러 개 있을 수 있습니다.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

기존 버전을 업데이트 하거나 엔드포인트에서 삭제 합니다. 버전의 기본 형식, 컨트롤 형식 및 트래픽 백분위 수를 변경할 수 있습니다.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>웹 서비스 인증

Azure Kubernetes Service에 배포 하는 경우 __키 기반__ 인증은 기본적으로 사용 하도록 설정 됩니다. __토큰 기반__ 인증을 사용 하도록 설정할 수도 있습니다. 토큰 기반 인증을 사용 하려면 클라이언트가 Azure Active Directory 계정을 사용 하 여 배포 된 서비스에 대 한 요청을 수행 하는 데 사용 되는 인증 토큰을 요청 해야 합니다.

인증을 __사용 하지 않도록__ 설정 하려면 배포 구성을 만들 때 `auth_enabled=False` 매개 변수를 설정 합니다. 다음 예제에서는 SDK를 사용 하 여 인증을 사용 하지 않도록 설정 합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

클라이언트 응용 프로그램에서 인증 하는 방법에 대 한 자세한 내용은 [웹 서비스로 배포 된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md)을 참조 하세요.

### <a name="authentication-with-keys"></a>키를 사용 하 여 인증

키 인증을 사용 하는 경우 `get_keys` 메서드를 사용 하 여 기본 및 보조 인증 키를 검색할 수 있습니다.

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 키를 다시 생성 해야 하는 경우 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 사용

### <a name="authentication-with-tokens"></a>토큰을 사용한 인증

토큰 인증을 사용 하도록 설정 하려면 배포를 만들거나 업데이트할 때 `token_auth_enabled=True` 매개 변수를 설정 합니다. 다음 예제에서는 SDK를 사용 하 여 토큰 인증을 사용 하도록 설정 합니다.

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

토큰 인증을 사용 하는 경우 `get_token` 메서드를 사용 하 여 JWT 토큰 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 이후에 새 토큰을 요청 해야 합니다.
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다. 토큰을 사용 하 여 인증 하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성 되는 영역에 대 한 호출을 수행 합니다. 작업 영역을 사용할 수 없는 경우에는 클러스터가 작업 영역과 다른 지역에 있는 경우에도 웹 서비스에 대 한 토큰을 가져올 수 없습니다. 이로 인해 작업 영역을 다시 사용할 수 있을 때까지 토큰 기반 인증을 사용할 수 없습니다. 또한 클러스터의 지역과 작업 영역 영역 간의 거리가 클수록 토큰을 인출 하는 데 시간이 오래 걸립니다.

## <a name="update-the-web-service"></a>웹 서비스 업데이트

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>다음 단계

* [가상 네트워크의 보안 실험 및 유추](how-to-enable-virtual-network.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
