---
title: AKS(Azure Kubernetes Service)에서 GPU 사용
description: AKS(Azure Kubernetes Service)에서 고성능 컴퓨팅 또는 그래픽 집약적 워크로드에 GPU를 사용하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 27c284ff7e806c9f194005ed26c05e99c4697083
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88757645"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 계산 집약적 워크로드에 GPU 사용

GPU(그래픽 처리 장치)는 그래픽 및 시각화 워크로드 같은 계산 집약적 워크로드에 자주 사용됩니다. AKS는 Kubernetes에서 이러한 계산 집약적 워크로드를 실행할 수 있도록 GPU 지원 노드 풀 만들기를 지원합니다. 사용 가능한 GPU 지원 VM에 대한 자세한 내용은 [Azure의 GPU 최적화 VM 크기][gpu-skus]를 참조하세요. AKS 노드의 경우 권장하는 최소 크기는 *Standard_NC6*입니다.

> [!NOTE]
> GPU 지원 VM에는 더 높은 가격 및 지역 가용성에 맞는 특별한 하드웨어가 포함되어 있습니다. 자세한 내용은 [가격 책정][azure-pricing] 도구 및 [지역 가용성][azure-availability]을 참조하세요.

현재 GPU 사용 노드 풀을 사용 하는 것은 Linux 노드 풀에만 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 GPU를 지원하는 노드가 포함된 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터에서 Kubernetes 1.10 이상을 실행해야 합니다. 이러한 요구 사항을 충족하는 AKS 클러스터가 필요한 경우 이 문서의 첫 번째 섹션인 [AKS 클러스터 만들기](#create-an-aks-cluster)를 참조하세요.

또한 Azure CLI 버전 2.0.64 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

최소 요구 사항(GPU 지원 노드 및 Kubernetes 버전 1.10 이상)을 충족하는 AKS 클러스터가 필요한 경우 다음 단계를 완료합니다. 이러한 요구 사항을 충족 하는 AKS 클러스터가 이미 있는 경우 [다음 섹션으로 건너뜁니다](#confirm-that-gpus-are-schedulable).

먼저, [az group create][az-group-create] 명령을 사용하여 클러스터에 대한 리소스 그룹을 만듭니다. 다음 예제는 *eastus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

이제 [az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예에서는 크기의 단일 노드를 사용 하 여 클러스터를 만듭니다 `Standard_NC6` .

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터의 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>NVIDIA 장치 플러그 인 설치

노드의 Gpu를 사용 하려면 먼저 NVIDIA 장치 플러그 인에 대 한 DaemonSet를 배포 해야 합니다. 이 DaemonSet는 각 노드에서 Pod를 실행하여 GPU에 필요한 드라이버를 제공합니다.

먼저, *gpu-resources* 같은 [kubectl create namespace][kubectl-create] 명령을 사용하여 네임스페이스를 만듭니다.

```console
kubectl create namespace gpu-resources
```

*nvidia-device-plugin-ds.yaml*이라는 파일을 만들고 다음 YAML 매니페스트를 붙여넣습니다. 이 매니페스트는 [Kubernetes 프로젝트에 대 한 NVIDIA 장치 플러그 인의][nvidia-github]일부로 제공 됩니다.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

이제 다음 예제 출력과 같이 [kubectl apply][kubectl-apply] 명령을 사용 하 여 DaemonSet를 만들고 NVIDIA 장치 플러그 인이 성공적으로 만들어졌는지 확인 합니다.

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>AKS 특수 GPU 이미지 (미리 보기) 사용

이러한 단계를 수행 하는 대신, AKS는 [Kubernetes에 대 한 NVIDIA 장치 플러그 인][nvidia-github]을 이미 포함 하 고 있는 완전히 구성 된 AKS 이미지를 제공 합니다.

> [!WARNING]
> 새 AKS 특수 GPU 이미지를 사용 하 여 클러스터에 대 한 NVIDIA 장치 플러그 인 데몬 집합을 수동으로 설치 해서는 안 됩니다.


`GPUDedicatedVHDPreview` 기능을 등록합니다.

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

상태가 등록됨으로 표시되면 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Aks-preview CLI 확장을 설치 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli
az extension add --name aks-preview
```

Aks-preview CLI 확장을 업데이트 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>새 클러스터에서 AKS 특수 GPU 이미지 사용 (미리 보기)    

클러스터를 만들 때 AKS 특수 GPU 이미지를 사용 하도록 클러스터를 구성 합니다. `--aks-custom-headers`새 클러스터의 gpu 에이전트 노드에 대 한 플래그를 사용 하 여 AKS 특수 gpu 이미지를 사용 합니다.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

일반 AKS 이미지를 사용 하 여 클러스터를 만들려는 경우 사용자 지정 태그를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` . 아래와 같이 특수화 된 GPU 노드 풀을 추가 하도록 선택할 수도 있습니다.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>기존 클러스터에서 AKS 특수 GPU 이미지 사용 (미리 보기)

AKS 특수 GPU 이미지를 사용 하도록 새 노드 풀을 구성 합니다. `--aks-custom-headers`새 노드 풀의 gpu 에이전트 노드에 대 한 플래그 플래그를 사용 하 여 AKS 특수 gpu 이미지를 사용 합니다.

```azure-cli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

일반 AKS 이미지를 사용 하 여 노드 풀을 만들려면 사용자 지정 태그를 생략 하 여이 작업을 수행할 수 있습니다 `--aks-custom-headers` . 

> [!NOTE]
> GPU sku가 2 세대 가상 컴퓨터를 필요로 하는 경우 다음을 만들 수 있습니다.
> ```azure-cli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>GPU의 예약 가능 여부 확인

AKS 클러스터를 만들었으면, Kubernetes에서 GPU를 예약할 수 있는지 확인합니다. 먼저, [kubectl get nodes][kubectl-get] 명령을 사용하여 클러스터의 노드를 나열합니다.

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

[kubectl describe node][kubectl-describe] 명령을 사용하여 GPU를 예약할 수 있는지 확인합니다. *용량* 섹션에 GPU가 `nvidia.com/gpu:  1`으로 나열됩니다

압축된 다음 예제는 *aks-nodepool1-18821093-0* 노드에서 GPU를 사용할 수 있음을 보여줍니다.

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU 지원 워크로드 실행

GPU가 실제로 작동하는 모습을 보려면 적절한 리소스 요청을 사용하여 GPU 사용 워크로드를 예약하세요. 이 예제에서는 [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/)에 대해 [Tensorflow](https://www.tensorflow.org/) 작업을 실행하겠습니다.

*samples-tf-mnist-demo.yaml*이라는 파일을 만들고 다음 YAML 매니페스트를 붙여넣습니다. 다음 작업 매니페스트에는 `nvidia.com/gpu: 1`의 리소스 제한이 포함되어 있습니다.

> [!NOTE]
> 드라이버를 호출할 때 버전 불일치 오류가 발생 하는 경우, 예를 들어, verda 드라이버 버전은 verda 런타임 버전용으로 충분 하지 않은 경우 NVIDIA 드라이버 매트릭스 호환성 차트를 검토 합니다. [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

작업을 실행하려면 [kubectl apply][kubectl-apply] 명령을 사용합니다. 이 명령은 매니페스트 파일을 구문 분석하고 정의된 Kubernetes 개체를 만듭니다.

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>GPU 지원 워크로드의 상태 및 출력 보기

[kubectl get jobs][kubectl-get] 명령과 `--watch` 인수를 사용하여 작업 진행 상태를 모니터링합니다. 처음으로 이미지를 끌어와서 데이터 세트를 처리하는 경우 몇 분 정도 걸릴 수 있습니다. *완료* 열에 *1/1*이 표시 되 면 작업이 성공적으로 완료 된 것입니다. Ctrl + `kubetctl --watch` *C*를 눌러 명령을 종료 합니다.

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

GPU 사용 워크 로드의 출력을 보려면 먼저 [kubectl get pod][kubectl-get] 명령을 사용 하 여 pod의 이름을 가져옵니다.

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

이제 [kubectl logs][kubectl-logs] 명령을 사용하여 Pod 로그를 봅니다. 다음 Pod 로그 예제에서 적절한 GPU 디바이스 `Tesla K80`이 발견되었음을 확인합니다. 자체 Pod의 이름을 입력합니다.

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 관련 Kubernetes 개체를 제거하려면 다음과 같이 [kubectl delete job][kubectl delete] 명령을 사용합니다.

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>다음 단계

Apache Spark 작업을 실행하려면 [AKS에서 Apache Spark 작업 실행][aks-spark]을 참조하세요.

Kubernetes에서 ML(머신 러닝) 워크로드를 실행하는 방법에 대한 자세한 내용은 [Kubeflow 랩][kubeflow-labs]을 참조하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
