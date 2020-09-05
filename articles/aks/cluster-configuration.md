---
title: AKS(Azure Kubernetes Service)의 클러스터 구성
description: AKS(Azure Kubernetes Service)에서 클러스터를 구성하는 방법 알아보기
services: container-service
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: daffcbf0a2ceb6f28cbb539906d4c6387840aa20
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752093"
---
# <a name="configure-an-aks-cluster"></a>AKS 클러스터 구성

AKS 클러스터를 만드는 과정에서 필요에 따라 클러스터 구성을 사용자 지정해야 할 수 있습니다. 이 문서에서는 AKS 클러스터를 사용자 지정하기 위한 몇 가지 옵션을 소개합니다.

## <a name="os-configuration-preview"></a>OS 구성(미리 보기)

이제 AKS는 미리 보기에서 노드 OS(운영 체제)로 Ubuntu 18.04를 지원합니다. 미리 보기 기간 동안 Ubuntu 16.04 및 Ubuntu 18.04를 모두 사용할 수 있습니다.

> [!IMPORTANT]
> Kubernetes v 1.18에 생성 된 노드 풀은 필수 노드 이미지에 대 한 기본값 보다 높습니다 `AKS Ubuntu 18.04` . 1.18 미만의 지원 되는 Kubernetes 버전의 노드 풀은 노드 `AKS Ubuntu 16.04` 이미지로 수신 하지만 `AKS Ubuntu 18.04` 노드 풀 Kubernetes 버전이 v 1.18 이상으로 업데이트 되 면 업데이트 됩니다.
> 
> 1.18 이상에서 클러스터를 사용 하기 전에 AKS Ubuntu 18.04 노드 풀에서 워크 로드를 테스트 하는 것이 좋습니다. [Ubuntu 18.04 노드 풀을 테스트](#use-aks-ubuntu-1804-existing-clusters-preview)하는 방법을 참조 하세요.

다음 리소스가 설치되어 있어야 합니다.

- [Azure CLI][azure-cli-install]버전 2.2.0 이상
- aks-preview 0.4.35 확장

aks-preview 0.4.35 확장 이상을 설치하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

`UseCustomizedUbuntuPreview` 기능을 등록합니다.

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

상태가 등록됨으로 표시되면 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>새 클러스터에서 AKS Ubuntu 18.04 사용 (미리 보기)

클러스터를 만들 때 Ubuntu 18.04를 사용하도록 클러스터를 구성합니다. `--aks-custom-headers` 플래그를 사용하여 Ubuntu 18.04를 기본 OS로 설정합니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

AKS Ubuntu 16.04 이미지를 사용 하 여 클러스터를 만들려는 경우 사용자 지정 태그를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` .

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>AKS Ubuntu 18.04 기존 클러스터 사용 (미리 보기)

Ubuntu 18.04를 사용하도록 새 노드 풀을 구성합니다. `--aks-custom-headers` 플래그를 사용하여 Ubuntu 18.04를 해당 노드 풀의 기본 OS로 설정합니다.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

AKS Ubuntu 16.04 이미지를 사용 하 여 노드 풀을 만들려면 사용자 지정 태그를 생략 하 여이 작업을 수행할 수 있습니다 `--aks-custom-headers` .


## <a name="container-runtime-configuration-preview"></a>컨테이너 런타임 구성 (미리 보기)

컨테이너 런타임은 컨테이너를 실행 하 고 노드의 컨테이너 이미지를 관리 하는 소프트웨어입니다. 런타임은 sys 호출 또는 OS (운영 체제) 특정 기능을 추상화 하 여 Linux 또는 Windows에서 컨테이너를 실행 하는 데 도움이 됩니다. 오늘 AKS는 [Moby](https://mobyproject.org/) (업스트림 docker)를 컨테이너 런타임으로 사용 하 고 있습니다. 
    
![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) 는 노드에서 컨테이너를 실행 하 고 이미지를 관리 하는 데 필요한 최소 기능 집합을 제공 하는 [OCI](https://opencontainers.org/) (Open Container 이니셔티브) 규격 핵심 컨테이너 런타임입니다. 2017 년 3 월의 CNCF (Cloud Native Compute Foundation)로 [기증](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) 되었습니다. AKS에서 현재 사용 하 고 있는 현재 Moby 버전은 위에 표시 된 대로 이미를 활용 하 고 `containerd` 있습니다. 

Containerd 기반 노드 및 노드 풀을 사용 하는 대신, `dockershim` kubelet는 `containerd` CRI (container runtime interface) 플러그 인을 통해 직접 통신 하 여 Docker CRI 구현과 비교할 때 흐름에서 추가 홉을 제거 합니다. 따라서 pod 시작 대기 시간 및 리소스 (CPU 및 메모리) 사용이 더 나은 것을 볼 수 있습니다.

AKS 노드에 대해를 사용 하 여 `containerd` pod 시작 대기 시간이 향상 되 고 컨테이너 런타임에의 한 노드 리소스 소비가 감소 합니다. 이러한 향상 된 기능은 kubelet이 새 아키텍처에서 사용 하도록 설정 됩니다 .이 아키텍처는 `containerd` Moby/docker 아키텍처 kubelet에서 CRI 플러그 인을 통해 직접 통신 하는 반면 `dockershim` `containerd` , 흐름에 추가 홉이 발생 하기 전에 및 docker 엔진과 통신 합니다.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` AKS에 있는 kubernetes의 모든 GA 버전 및 v 1.10 위의 모든 업스트림 kubernetes 버전에서 작동 하며 모든 kubernetes 및 AKS 기능을 지원 합니다.

> [!IMPORTANT]
> `containerd`가 AKS에서 일반 공급 되 면 새 클러스터의 컨테이너 런타임에 사용할 수 있는 유일한 옵션입니다. 지원 되지 않는 이전 버전에서는 계속 해 서 Moby nodepools 및 클러스터를 사용할 수 있습니다. 
> 
> `containerd`이 컨테이너 런타임을 사용 하 여 새 클러스터를 업그레이드 하거나 만들기 전에 노드 풀에서 작업을 테스트 하는 것이 좋습니다.

### <a name="use-containerd-as-your-container-runtime-preview"></a>`containerd`컨테이너 런타임으로 사용 (미리 보기)

다음과 같은 필수 구성 요소가 있어야 합니다.

- [Azure CLI][azure-cli-install]버전 2.8.0 이상 설치 됨
- Aks-preview 확장 버전 0.4.53 이상
- `UseCustomizedContainerRuntime`등록 된 기능 플래그
- `UseCustomizedUbuntuPreview`등록 된 기능 플래그

Aks-preview 0.4.53 extension 이상을 설치 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

및 기능을 등록 합니다 `UseCustomizedContainerRuntime` `UseCustomizedUbuntuPreview` .

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

상태가 등록됨으로 표시되면 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>`containerd`새 클러스터에 사용 (미리 보기)

클러스터를 만들 때 사용할 클러스터를 구성 `containerd` 합니다. 플래그를 사용 `--aks-custom-headers` 하 여를 `containerd` 컨테이너 런타임으로 설정 합니다.

> [!NOTE]
> `containerd`런타임은 AKS Ubuntu 18.04 이미지를 사용 하는 노드 및 노드 풀 에서만 지원 됩니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Moby (docker) 런타임을 사용 하 여 클러스터를 만들려는 경우 사용자 지정 태그를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` .

### <a name="use-containerd-on-existing-clusters-preview"></a>`containerd`기존 클러스터에 사용 (미리 보기)

사용할 새 노드 풀을 구성 `containerd` 합니다. 플래그를 사용 하 여 `--aks-custom-headers` `containerd` 해당 노드 풀의 런타임으로 설정 합니다.

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

Moby (docker) 런타임을 사용 하 여 노드 풀을 만들려면 사용자 지정 태그를 생략 하 여이 작업을 수행할 수 있습니다 `--aks-custom-headers` .


### <a name="containerd-limitationsdifferences"></a>`Containerd` 제한 사항/차이점

* 를 `containerd` 컨테이너 런타임으로 사용 하려면 기본 OS 이미지로 AKS Ubuntu 18.04을 사용 해야 합니다.
* Docker 도구 집합은 여전히 노드에 있지만 Kubernetes는 컨테이너 런타임으로를 사용 `containerd` 합니다. 따라서 Moby/Docker는 노드의 Kubernetes 생성 컨테이너를 관리 하지 않으므로 Docker 명령 (예: `docker ps` ) 또는 DOCKER API를 사용 하 여 컨테이너를 보거나 조작할 수 없습니다.
* 의 경우 `containerd` [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) Kubernetes 노드의 pod, 컨테이너 및 컨테이너 이미지 **문제를 해결** 하기 위해 DOCKER cli 대신 대체 cli로를 사용 하는 것이 좋습니다 (예: `crictl ps` ). 
   * Docker CLI의 완전 한 기능을 제공 하지 않습니다. 문제 해결을 위한 용도로만 사용 됩니다.
   * `crictl` pod 등과 같은 개념을 포함 하 여 컨테이너에 대 한 보다 kubernetes 보기를 제공 합니다.
* `Containerd` 표준화 된 `cri` 로깅 형식 (현재 docker의 json 드라이버에서 가져온 것과 다름)을 사용 하 여 로깅을 설정 합니다. 로깅 솔루션은 `cri` [컨테이너에 대 한 Azure Monitor](../azure-monitor/insights/container-insights-enable-new-cluster.md)와 같은 로깅 형식을 지원 해야 합니다.
* 더 이상 docker 엔진에 액세스 하거나 docker (docker)를 사용할 수 없습니다 `/var/run/docker.sock` .
  * 현재 Docker 엔진에서 응용 프로그램 로그 또는 모니터링 데이터를 추출 하는 경우 [컨테이너에 Azure Monitor](../azure-monitor/insights/container-insights-enable-new-cluster.md) 와 같은 항목을 대신 사용 하세요. 또한 AKS는 에이전트 노드에서 모든 대역외 명령이 실행 되는 것을 지원 하지 않습니다 .이로 인해 불안정 해질 수 있습니다.
  * Moby/docker를 사용 하는 경우에도 위의 방법으로 이미지를 빌드하고 docker 엔진을 직접 활용 하는 것은 권장 되지 않습니다. Kubernetes는 소비 된 리소스를 완전히 인식 하지 못하므로 이러한 접근 방식은 [여기](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) 및 [여기](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)에서 자세히 설명 하는 다양 한 문제를 제공 합니다.
* 이미지 빌드-이미지를 빌드하기 위한 권장 접근 방식은 [ACR 작업](../container-registry/container-registry-quickstart-task-cli.md)을 사용 하는 것입니다. 또 다른 방법은 [docker buildx](https://github.com/docker/buildx)와 같은 더 안전한 클러스터 내 옵션을 사용 하는 것입니다.

## <a name="generation-2-virtual-machines-preview"></a>2 세대 가상 컴퓨터 (미리 보기)

Azure는 [Gen2 (2 세대) vm (가상 머신)](../virtual-machines/windows/generation-2.md)을 지원 합니다. 2 세대 Vm은 1 세대 Vm (Gen1)에서 지원 되지 않는 주요 기능을 지원 합니다. 이러한 기능에는 메모리 증가, Intel SGX(Software Guard Extensions) 및 vPMEM(가상화된 영구 메모리)이 포함됩니다.

2세대 VM은 1세대 VM에서 사용되는 BIOS 기반 아키텍처 대신 새 UEFI 기반 부팅 아키텍처를 사용합니다.
특정 Sku 및 크기만 Gen2 Vm을 지원 합니다. [지원 되는 크기 목록을](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)확인 하 여 SKU가 Gen2을 지원 하거나 요구 하는지 확인 합니다.

또한 모든 VM 이미지가 Gen2을 지 원하는 것은 아닙니다. AKS Gen2 Vm은 새로운 [AKS Ubuntu 18.04 이미지](#os-configuration-preview)를 사용 합니다. 이 이미지는 모든 Gen2 Sku 및 크기를 지원 합니다.

미리 보기 중에 Gen2 Vm을 사용 하려면 다음이 필요 합니다.
- `aks-preview`CLI 확장이 설치 되었습니다.
- `Gen2VMPreview`등록 된 기능 플래그입니다.

`Gen2VMPreview` 기능을 등록합니다.

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
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

### <a name="use-gen2-vms-on-new-clusters-preview"></a>새 클러스터에서 Gen2 Vm 사용 (미리 보기)
클러스터가 생성 될 때 선택한 SKU에 대해 Gen2 Vm을 사용 하도록 클러스터를 구성 합니다. `--aks-custom-headers`Gen2를 새 클러스터의 VM 생성으로 설정 하려면 플래그를 사용 합니다.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

Gen1 (1 세대) Vm을 사용 하 여 일반 클러스터를 만들려는 경우 사용자 지정 태그를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` . 또한 아래와 같이 더 많은 Gen1 또는 Gen2 Vm을 추가 하도록 선택할 수 있습니다.

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>기존 클러스터에서 Gen2 Vm 사용 (미리 보기)
Gen2 Vm을 사용 하도록 새 노드 풀을 구성 합니다. 플래그를 사용 하 여 `--aks-custom-headers` Gen2을 해당 노드 풀의 VM 생성으로 설정 합니다.

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

일반 Gen1 노드 풀을 만들려면 사용자 지정 태그를 생략 하 여이 작업을 수행할 수 있습니다 `--aks-custom-headers` .


## <a name="ephemeral-os-preview"></a>임시 OS (미리 보기)

기본적으로 Azure 가상 컴퓨터에 대 한 운영 체제 디스크는 Azure storage에 자동으로 복제 되어 데이터 손실을 방지 하 고 VM을 다른 호스트에 재배치 해야 합니다. 그러나 컨테이너는 로컬 상태를 유지 하도록 설계 되지 않았으므로이 동작은 제한 된 값을 제공 하는 반면, 느린 노드 프로 비전 및 더 높은 읽기/쓰기 대기 시간을 비롯 한 몇 가지 단점을 제공 합니다.

이와 반대로 삭제 된 OS 디스크는 임시 디스크와 마찬가지로 호스트 컴퓨터에만 저장 됩니다. 이를 통해 더 빠른 노드 크기 조정 및 클러스터 업그레이드와 함께 읽기/쓰기 대기 시간이 줄어듭니다.

임시 디스크와 마찬가지로, 임시 OS 디스크는 가상 컴퓨터의 가격에 포함 되어 있으므로 추가 저장소 비용이 발생 하지 않습니다.

`EnableEphemeralOSDiskPreview` 기능을 등록합니다.

```azurecli
az feature register --name EnableEphemeralOSDiskPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨**으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEphemeralOSDiskPreview')].{Name:name,State:properties.state}"
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

### <a name="use-ephemeral-os-on-new-clusters-preview"></a>새 클러스터에서 사용 후 삭제 OS 사용 (미리 보기)

클러스터를 만들 때 사용 후 삭제 OS 디스크를 사용 하도록 클러스터를 구성 합니다. `--aks-custom-headers`새 클러스터에 대 한 os 디스크 유형으로 사용 후 삭제 os를 설정 하려면 플래그를 사용 합니다.

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

네트워크에 연결 된 OS 디스크를 사용 하 여 일반 클러스터를 만들려는 경우 사용자 지정 태그를 생략 하 여 수행할 수 있습니다 `--aks-custom-headers` . 또한 아래와 같이 더 많은 임시 OS 노드 풀을 추가 하도록 선택할 수 있습니다.

### <a name="use-ephemeral-os-on-existing-clusters-preview"></a>기존 클러스터에서 사용 후 삭제 OS 사용 (미리 보기)
새 노드 풀을 구성 하 여 사용 후 삭제 OS 디스크를 사용 합니다. 플래그를 사용 `--aks-custom-headers` 하 여 os 디스크 유형으로 해당 노드 풀의 os 디스크 유형으로 설정 합니다.

```azure-cli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

> [!IMPORTANT]
> 사용 후 삭제 OS를 사용 하면 vm 캐시 크기까지 VM 및 인스턴스 이미지를 배포할 수 있습니다. AKS의 경우 기본 노드 OS 디스크 구성은 100GiB을 사용 합니다. 즉, 캐시가 100 GiB 보다 큰 VM 크기가 필요 합니다. 기본 Standard_DS2_v2의 캐시 크기는 86 GiB입니다 .이는 충분히 크지 않습니다. Standard_DS3_v2의 캐시 크기가 172 GiB입니다 .이는 크기가 충분 합니다. 또한를 사용 하 여 OS 디스크의 기본 크기를 줄일 수 있습니다 `--node-osdisk-size` . AKS 이미지의 최소 크기는 30GiB입니다. 

네트워크에 연결 된 OS 디스크를 사용 하 여 노드 풀을 만들려면 사용자 지정 태그를 생략 하 여 그렇게 할 수 있습니다 `--aks-custom-headers` .

## <a name="custom-resource-group-name"></a>사용자 지정 리소스 그룹 이름

Azure에서 Azure Kubernetes Service 클러스터를 배포하면 작업자 노드에 대한 두 번째 리소스 그룹이 생성됩니다. 기본적으로 AKS는 노드 리소스 그룹 이름을 `MC_resourcegroupname_clustername_location`으로 지정하지만 사용자 고유의 이름을 제공할 수도 있습니다.

고유한 리소스 그룹 이름을 지정하려면 aks-preview Azure CLI 확장 버전 0.3.2 이상을 설치합니다. Azure CLI 사용하여 `az aks create` 명령의 `--node-resource-group` 매개 변수를 사용하여 리소스 그룹의 사용자 지정 이름을 지정합니다. Azure Resource Manager 템플릿을 사용하여 AKS 클러스터를 배포하는 경우 `nodeResourceGroup` 속성을 사용하여 리소스 그룹 이름을 정의할 수 있습니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

자체 구독에서 Azure 리소스 공급자가 자동으로 보조 리소스 그룹을 만듭니다. 클러스터가 생성될 때만 사용자 지정 리소스 그룹 이름을 지정할 수 있습니다. 

노드 리소스 그룹으로 작업할 때는 다음을 수행할 수 없습니다.

- 노드 리소스 그룹에 기존 리소스 그룹을 지정합니다.
- 노드 리소스 그룹에 다른 구독을 지정합니다.
- 클러스터를 만든 후 노드 리소스 그룹 이름을 변경합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 이름을 지정합니다.
- 노드 리소스 그룹 내에서 관리 리소스의 Azure에서 만든 태그를 수정하거나 삭제합니다.

## <a name="next-steps"></a>다음 단계

- 클러스터에서 `Kured`를 사용하여 [Linux 노드에 보안 및 커널 업데이트를 적용](node-updates-kured.md)하는 방법에 대해 알아봅니다.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아보려면 [AKS(Azure Kubernetes Service) 클러스터 업그레이드](upgrade-cluster.md)를 참조하세요.
- [ `containerd` 및 Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/) 에 대해 자세히 알아보세요.
- 몇 가지 일반적인 AKS 질문에 대한 답변을 확인하려면 [AKS에 대한 질문과 대답](faq.md) 목록을 참조하세요.
- [임시 OS 디스크](../virtual-machines/ephemeral-os-disks.md)에 대해 자세히 알아보세요.


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
