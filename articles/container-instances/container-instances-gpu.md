---
title: GPU 지원 Azure 컨테이너 인스턴스 배포
description: GPU 리소스에서 실행할 Azure 컨테이너 인스턴스를 배포하는 방법을 알아봅니다.
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 5073b68f6ef3de330671e3ea25056e0cae976360
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583826"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU 리소스를 사용하는 컨테이너 인스턴스 배포

Azure Container Instances에서 컴퓨팅 작업이 많은 특정한 워크로드를 실행하려면 *GPU 리소스*를 사용하여 [컨테이너 그룹](container-instances-container-groups.md)을 배포합니다. 그룹의 컨테이너 인스턴스는 CUDA 및 딥 러닝 애플리케이션과 같은 컨테이너 워크로드를 실행하는 동안 하나 이상의 NVIDIA Tesla GPU에 액세스할 수 있습니다.

이 문서에서는 사용 하 여 컨테이너 그룹을 배포할 때 GPU 리소스를 추가 하는 방법을 보여 줍니다.는 [YAML 파일](container-instances-multi-container-yaml.md) 하거나 [Resource Manager 템플릿을](container-instances-multi-container-group.md)합니다. Azure portal을 사용 하 여 컨테이너 인스턴스를 배포 하는 경우에 GPU 리소스를 지정할 수 있습니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

미리 보기에서는 컨테이너 그룹에 GPU 리소스를 사용할 때 다음과 같은 제한 사항이 적용됩니다. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

앞으로 더 많은 Azure 지역에 대한 지원이 추가될 것입니다.

**지원되는 OS 유형**: Linux만

**추가 제한 사항**: 컨테이너 그룹을 [가상 네트워크](container-instances-vnet.md)에 배포하는 경우에는 GPU 리소스를 사용할 수 없습니다.

## <a name="about-gpu-resources"></a>GPU 리소스 정보

### <a name="count-and-sku"></a>수 및 SKU

컨테이너 인스턴스에서 GPU를 사용하려면 다음 정보를 사용하여 *GPU 리소스*를 지정해야 합니다.

* **수** - GPU 수: **1**, **2** 또는 **4**.
* **SKU** - GPU SKU: **K80**, **P100** 또는 **V100**. 각 SKU는 다음 Azure GPU 지원 VM 제품군 중 하나의 NVIDIA Tesla GPU에 매핑됩니다.

  | SKU | VM 제품군 |
  | --- | --- |
  | K80 | [NC](../virtual-machines/linux/sizes-gpu.md#nc-series) |
  | P100 | [NCv2](../virtual-machines/linux/sizes-gpu.md#ncv2-series) |
  | V100 | [NCv3](../virtual-machines/linux/sizes-gpu.md#ncv3-series) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

GPU 리소스를 배포 하는 경우에 CPU 및 메모리 리소스를 앞의 표에 표시 된 최대 값 최대 워크 로드에 대 한 적절 한 설정 합니다. 이러한 값은 현재 GPU 리소스 없이도 컨테이너 그룹에서 사용할 수 있는 CPU 및 메모리 리소스를 초과 합니다.  

### <a name="things-to-know"></a>알아야 할 사항

* **배포 시간** - GPU 리소스가 있는 컨테이너 그룹을 만드는 데 최대 **8-10분**이 걸립니다. Azure에 GPU VM을 프로비전하고 구성하는 시간이 추가로 소요되기 때문입니다. 

* **가격 책정** - GPU 리소스가 없는 컨테이너 그룹과 마찬가지로, Azure는 GPU 리소스가 있는 컨테이너 그룹의 *기간* 동안 사용된 리소스 비용을 청구합니다. 이 기간은 컨테이너의 첫 번째 이미지를 끌어온 시점부터 컨테이너 그룹이 종료되는 시점 사이의 시간으로 계산됩니다. 컨테이너 그룹을 배포하는 시간은 포함되지 않습니다.

  [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/container-instances/)를 참조하세요.

* **CUDA 드라이버** - GPU 리소스가 있는 컨테이너 인스턴스는 NVIDIA CUDA 드라이버 및 컨테이너 런타임이 미리 프로비전되므로, CUDA 워크로드용으로 개발된 컨테이너 이미지를 사용할 수 있습니다.

  이 단계에서 CUDA 9.0 지원합니다. 예를 들어, 다음 Docker 파일에 대 한 기본 이미지를 사용할 수 있습니다.
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML 예제

GPU 리소스를 추가하는 한 가지 방법은 [YAML 파일](container-instances-multi-container-yaml.md)을 사용하여 컨테이너 그룹을 배포하는 것입니다. 다음 YAML을 *gpu-deploy-aci.yaml*이라는 새 파일에 복사하고 파일을 저장합니다. 이 YAML은 K80 GPU가 있는 컨테이너 인스턴스를 지정하는 *gpucontainergroup*이라는 컨테이너 그룹을 만듭니다. 이 인스턴스는 샘플 CUDA 벡터 추가 애플리케이션을 실행합니다. 리소스 요청은 워크로드를 실행하기에 충분합니다.

```YAML
additional_properties: {}
apiVersion: '2018-10-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

`--file` 매개 변수로 YAML 파일 이름을 지정한 [az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포합니다. GPU 리소스를 지원하는 *eastus*처럼 리소스 그룹의 이름과 컨테이너 그룹의 위치를 제공해야 합니다.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

배포를 완료하려면 몇 분이 걸립니다. 배포가 완료되면 컨테이너가 시작되어 CUDA 벡터 추가 작업을 실행합니다. 로그 출력을 보려면 [az container logs][az-container-logs] 명령을 실행합니다.

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

출력:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Resource Manager 템플릿 예제

GPU 리소스가 있는 컨테이너 그룹을 배포하는 또 다른 방법은 [Resource Manager 템플릿](container-instances-multi-container-group.md)을 사용하는 것입니다. 먼저 `gpudeploy.json` 파일을 만든 후, 다음 JSON을 이 파일에 복사합니다. 이 예제에서는 [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/)를 대상으로 [TensorFlow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) 교육 작업을 실행하는 V100 GPU가 있는 컨테이너 인스턴스를 배포합니다. 리소스 요청은 워크로드를 실행하기에 충분합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2018-10-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

[az group deployment create][az-group-deployment-create] 명령을 사용하여 템플릿을 배포합니다. *eastus*처럼 GPU 리소스를 지원하는 Azure 지역에서 만든 리소스 그룹의 이름을 제공해야 합니다.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file gpudeploy.json
```

배포를 완료하려면 몇 분이 걸립니다. 배포가 완료되면 컨테이너가 시작되어 TensorFlow 작업을 실행합니다. 로그 출력을 보려면 [az container logs][az-container-logs] 명령을 실행합니다.

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

출력:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>리소스 정리

GPU 리소스를 사용하면 많은 비용이 발생할 수 있으므로 컨테이너가 예기치 않게 오래 실행되지 않도록 주의해야 합니다. Azure Portal에서 컨테이너를 모니터링하거나 [az container show][az-container-show] 명령으로 컨테이너 그룹의 상태를 확인할 수 있습니다. 예를 들면 다음과 같습니다.

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

작성한 컨테이너를 사용하는 작업이 완료되었으면 다음 명령을 사용하여 컨테이너를 삭제합니다.

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>다음 단계

* [YAML 파일](container-instances-multi-container-yaml.md) 또는 [Resource Manager 템플릿](container-instances-multi-container-group.md)을 사용하여 컨테이너 그룹을 배포하는 방법에 대해 자세히 알아보세요.
* Azure의 [GPU 최적화 VM 크기](../virtual-machines/linux/sizes-gpu.md)에 대해 자세히 알아보세요.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
