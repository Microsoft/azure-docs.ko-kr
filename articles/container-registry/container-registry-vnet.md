---
title: 가상 네트워크에 Azure container registry를 배포 합니다.
description: Azure virtual network의 리소스에서만 또는 공용 IP 주소 범위를 Azure container registry 액세스를 허용 합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: 15b67218b129b5e017e67651587c389af412d7a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60867415"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Azure 가상 네트워크 또는 방화벽 규칙을 사용 하 여 Azure container registry에 대 한 액세스 제한

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 온-프레미스 리소스 및 Azure에 대 한 보안, 개인 네트워킹을 제공 합니다. Azure 가상 네트워크에 개인 Azure container registry를 배포 하 여 가상 네트워크의 리소스만 액세스할 레지스트리에 있는지 확인할 수 있습니다. 프레미스 간 시나리오에 대 한 특정 IP 주소 에서만에서 레지스트리 액세스를 허용 하도록 방화벽 규칙을도 구성할 수 있습니다.

이 문서에서는 Azure container registry에 대 한 액세스를 제한 하는 네트워크 액세스 규칙을 만들려면 두 가지 시나리오를 보여 줍니다: 동일한 네트워크에 배포 된 가상 컴퓨터 또는 VM의 공용 IP 주소에서 합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.
>

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 만 **Premium** 네트워크 액세스 규칙을 사용 하 여 컨테이너 레지스트리를 구성할 수 있습니다. 레지스트리 서비스 계층에 대 한 자세한 내용은 [Azure Container Registry Sku](container-registry-skus.md)합니다. 

* 만 [Azure Kubernetes Service](../aks/intro-kubernetes.md) 클러스터 또는 Azure [가상 머신](../virtual-machines/linux/overview.md) 가상 네트워크에 컨테이너 레지스트리를 액세스 하는 호스트로 사용할 수 있습니다. *Azure Container Instances를 포함 하 여 다른 Azure 서비스는 현재 지원 되지 않습니다.*

* [ACR 작업](container-registry-tasks-overview.md) 작업 가상 네트워크에 배포 된 컨테이너 레지스트리에 현재 지원 되지 않습니다.

* 각 레지스트리는 최대 100 개의 가상 네트워크 규칙을 지원합니다.

## <a name="prerequisites"></a>필수 조건

* Azure를 사용 하도록 CLI는이 문서에서는 Azure CLI 버전 2.0.58 단계 또는 이상 버전이 필요 합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

* 컨테이너 레지스트리를 아직 없는 경우 만드세요 (Premium SKU 필요) 및와 같은 샘플 이미지를 푸시 `hello-world` Docker 허브에서. 예를 들어 사용 합니다 [Azure portal] [ quickstart-portal] 또는 [Azure CLI] [ quickstart-cli] 레지스트리를 만듭니다. 

## <a name="about-network-rules-for-a-container-registry"></a>컨테이너 레지스트리에 대 한 네트워크 규칙에 대 한

기본적으로 Azure container registry는 네트워크에 있는 모든 호스트에서 인터넷을 통해 연결을 수락합니다. 가상 네트워크를 사용 하 여 AKS 클러스터 또는 Azure VM을 안전 하 게 네트워크 경계를 벗어나지 않고 레지스트리에 액세스와 같은 Azure 리소스에만 허용할 수 있습니다. 허용 목록에 추가 네트워크 방화벽 규칙을 구성할 수도 있습니다 특정 공용 인터넷 IP 주소 범위입니다. 

레지스트리에 대 한 액세스를 제한 하려면 먼저 변경 레지스트리의 기본 동작을 모든 네트워크 연결을 거부 합니다. 네트워크 액세스 규칙을 추가 합니다. 클라이언트 부여 네트워크 규칙을 통해 액세스 해야 계속 [컨테이너 레지스트리로 인증](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) 데이터 액세스 권한을 부여할 수 있습니다.

### <a name="service-endpoint-for-subnets"></a>서브넷에 대 한 서비스 끝점

가상 네트워크의 서브넷에서의 액세스를 허용 하려면 추가 해야는 [서비스 끝점](../virtual-network/virtual-network-service-endpoints-overview.md) Azure Container Registry 서비스에 대 한 합니다. 

다중 테 넌 트 서비스를 Azure Container Registry와 같은 사용 하 여 단일 IP 주소 집합을 모든 고객에 대 한 합니다. 서비스 끝점을 레지스트리에 액세스 하는 끝점을 할당 합니다. 이 끝점 제공 트래픽을 최적 경로 리소스에 Azure 백본 네트워크를 통해. 가상 네트워크 및 서브넷의 ID 또한 각 요청과 함께 전송됩니다.

### <a name="firewall-rules"></a>방화벽 규칙

IP 네트워크 규칙에 대 한 제공 허용된 인터넷 주소 범위와 같은 CIDR 표기법을 사용 하 여 *16.17.18.0/24* 와 같은 개별 IP 주소 *16.17.18.19*합니다. IP 네트워크 규칙에 대 한 개만 *공용* 인터넷 IP 주소입니다. IP 주소 범위 (RFC 1918에 정의 됨)으로 개인 네트워크에 대 한 예약 된 IP 규칙에서 허용 되지 않습니다.

## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 지원 가상 머신 만들기

이 문서에서는 Azure container registry를 액세스 하는 Docker 사용 가능 Ubuntu VM을 사용 합니다. 레지스트리를 Azure Active Directory 인증을 사용 하려면도 설치 합니다 [Azure CLI] [ azure-cli] VM에서. Azure 가상 컴퓨터에 이미 있는 경우이 생성 단계를 건너뜁니다.

가상 머신과 컨테이너 레지스트리에 대 한 동일한 리소스 그룹을 사용할 수 있습니다. 이 설치 프로그램에서 정리 끝을 간소화 하지만 필요 하지 않습니다. 가상 머신 및 가상 네트워크에 대 한 별도 리소스 그룹을 만들려면 원하는 경우 실행할 [az 그룹 만들기][az-group-create]합니다. 다음 예제에서는 명명 된 리소스 그룹을 만듭니다 *myResourceGroup* 에 *westcentralus* 위치:

```azurecli
az group create --name myResourceGroup --location westus
```

이제 기본값을 배포 된 Ubuntu Azure 가상 머신 [az vm 만들기][az-vm-create]합니다. 다음 예에서는 이라는 VM을 만듭니다 *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM을 만드는 데 몇 분 정도 걸립니다. 명령이 완료되면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소를 사용 하 여 VM을 필요에 따라 방화벽 규칙의 뒷부분에 나오는 설정에 대 한 SSH 연결을 확인 합니다.

### <a name="install-docker-on-the-vm"></a>VM에 Docker 설치

VM이 실행된 후 VM에 SSH 연결을 만듭니다. *publicIpAddress*를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@publicIpAddress
```

Ubuntu VM에서 Docker를 설치 하려면 다음 명령을 실행 합니다.

```bash
sudo apt install docker.io -y
```

설치 후 다음 명령을 실행하여 VM에서 Docker가 제대로 실행되는지 확인합니다.

```bash
sudo docker run -it hello-world
```

출력:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI 설치

[apt를 사용하여 Azure CLI 설치](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)의 단계를 따라 Ubuntu 가상 머신에 Azure CLI를 설치합니다. 이 문서에서는 버전 2.0.58 설치 하는 확인 이상.

SSH 연결을 종료 합니다.

## <a name="allow-access-from-a-virtual-network"></a>가상 네트워크에서 액세스 허용

이 섹션에서는 Azure virtual network의 서브넷에서 액세스할 수 있도록 컨테이너 레지스트리를 구성 합니다. Azure CLI 및 Azure portal을 사용 하 여 해당 단계가 제공 됩니다.

### <a name="allow-access-from-a-virtual-network---cli"></a>가상 네트워크-CLI에서에서 액세스 허용

#### <a name="add-a-service-endpoint-to-a-subnet"></a>서브넷에 서비스 끝점 추가

VM을 만들 때 기본적으로 Azure는 동일한 리소스 그룹에서 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 컴퓨터의 이름을 기반으로 합니다. 예를 들어, 가상 컴퓨터 이름을 지정 하면 *myDockerVM*, 기본 가상 네트워크 이름은 *myDockerVMVNET*, 이라는 서브넷이 있는 *myDockerVMSubnet*합니다. Azure portal에서 또는 사용 하 여이 확인 합니다 [az network vnet 목록] [ az-network-vnet-list] 명령:

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
```

출력:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

사용 된 [az 네트워크 vnet 서브넷 업데이트] [ az-network-vnet-subnet-update] 추가할 명령을 **Microsoft.ContainerRegistry** 서브넷에 서비스 끝점입니다. 가상 네트워크와 다음 명령에서 서브넷의 이름을 바꿉니다.

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

사용 된 [az 네트워크 vnet 서브넷 표시] [ az-network-vnet-subnet-show] 서브넷의 리소스 ID를 검색 하는 명령입니다. 네트워크 액세스 규칙을 구성 하는 이후 단계에서 필요 합니다.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

출력:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>레지스트리에 대 한 기본 네트워크 액세스 변경

기본적으로 Azure container registry는 네트워크에 있는 모든 호스트에서 연결을 허용합니다. 선택한 네트워크에 대 한 액세스를 제한 하려면 액세스를 거부 하도록 기본 동작을 변경 합니다. 다음에서 레지스트리의 이름으로 대체 [az acr update] [ az-acr-update] 명령:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

사용 된 [az acr 네트워크 규칙 추가] [ az-acr-network-rule-add] VM의 서브넷에서 액세스할 수 있도록 레지스트리에 네트워크 규칙을 추가 하는 명령입니다. Container registry의 이름과 다음 명령에서 서브넷의 리소스 ID를 대체 합니다. 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

계속 해 서 [레지스트리에 액세스할 수 있는지 확인](#verify-access-to-the-registry)합니다.

### <a name="allow-access-from-a-virtual-network---portal"></a>-가상 네트워크에서 액세스할 수 있도록 포털

#### <a name="add-service-endpoint-to-subnet"></a>서브넷에 서비스 끝점 추가

VM을 만들 때 기본적으로 Azure는 동일한 리소스 그룹에서 가상 네트워크를 만듭니다. 가상 네트워크의 이름은 가상 컴퓨터의 이름을 기반으로 합니다. 예를 들어, 가상 컴퓨터 이름을 지정 하면 *myDockerVM*, 기본 가상 네트워크 이름은 *myDockerVMVNET*, 이라는 서브넷이 있는 *myDockerVMSubnet*합니다.

서브넷에 Azure Container Registry에 대 한 서비스 끝점을 추가:

1. 맨 위에 있는 검색 상자에는 [Azure portal][azure-portal]를 입력 *가상 네트워크*합니다. 검색 결과에 **가상 네트워크**가 표시되면 이를 선택합니다.
1. 가상 네트워크의 목록에서 가상 머신에 배포 되는 위치에 같은 가상 네트워크 선택 *myDockerVMVNET*합니다.
1. 아래 **설정을**를 선택 **서브넷**합니다.
1. 가상 컴퓨터를 배포할와 같은 서브넷을 선택 *myDockerVMSubnet*합니다.
1. 아래 **서비스 끝점**를 선택 **Microsoft.ContainerRegistry**합니다.
1. **저장**을 선택합니다.

![서브넷에 서비스 끝점 추가][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>레지스트리에 대 한 네트워크 액세스를 구성 합니다.

기본적으로 Azure container registry는 네트워크에 있는 모든 호스트에서 연결을 허용합니다. 가상 네트워크에 대 한 액세스를 제한 합니다.

1. 포털에서 컨테이너 레지스트리로 이동 합니다.
1. 아래 **설정을**를 선택 **방화벽 및 virtual network**합니다.
1. 기본적으로 액세스를 거부하려면 **선택한 네트워크**에서 액세스를 허용하도록 선택합니다. 
1. 선택 **기존 가상 네트워크 추가**, 가상 네트워크 및 서비스 끝점을 사용 하 여 구성 된 서브넷을 선택 합니다. **추가**를 선택합니다.
1. **저장**을 선택합니다.

![컨테이너 레지스트리에 대 한 virtual network 구성][acr-vnet-portal]

계속 해 서 [레지스트리에 액세스할 수 있는지 확인](#verify-access-to-the-registry)합니다.

## <a name="allow-access-from-an-ip-address"></a>IP 주소에서 액세스 허용

이 섹션에서는 Azure virtual network의 서브넷에서 액세스할 수 있도록 컨테이너 레지스트리를 구성 합니다. Azure CLI 및 Azure portal을 사용 하 여 해당 단계가 제공 됩니다.

### <a name="allow-access-from-an-ip-address---cli"></a>IP 주소-CLI에서에서 액세스 허용

#### <a name="change-default-network-access-to-registry"></a>레지스트리에 대 한 기본 네트워크 액세스 변경

이미 않았다면 기본적으로 액세스를 거부 하려면 레지스트리 구성을 업데이트 합니다. 다음에서 레지스트리의 이름으로 대체 [az acr update] [ az-acr-update] 명령:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>레지스트리에서 네트워크 규칙을 제거 합니다.

VM의 서브넷에서의 액세스를 허용 하는 네트워크 규칙을 이전에 추가한 경우에 서브넷의 서비스 끝점 및 네트워크 규칙을 제거 합니다. Container registry의 이름과에서 이전 단계에서 검색 하는 서브넷의 리소스 ID를 대체 합니다 [az acr 네트워크 규칙 제거] [ az-acr-network-rule-remove] 명령: 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

사용 된 [az acr 네트워크 규칙 추가] [ az-acr-network-rule-add] VM의 IP 주소에서 액세스할 수 있도록 레지스트리에 네트워크 규칙을 추가 하는 명령입니다. Container registry의 이름과 다음 명령에서 VM의 공용 IP 주소를 대체 합니다.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

계속 해 서 [레지스트리에 액세스할 수 있는지 확인](#verify-access-to-the-registry)합니다.

### <a name="allow-access-from-an-ip-address---portal"></a>IP 주소에서 액세스할 수 있도록 포털

#### <a name="remove-existing-network-rule-from-registry"></a>레지스트리에서 기존 네트워크 규칙을 제거 합니다.

VM의 서브넷에서의 액세스를 허용 하는 네트워크 규칙을 이전에 추가한 경우에 기존 규칙을 제거 합니다. 다른 VM에서 레지스트리에 액세스 하도록 하려는 경우이 섹션을 건너뜁니다.

* 서브넷 설정을 업데이트 하 여 Azure Container Registry에 대 한 서브넷의 서비스 끝점을 제거 합니다. 

  1. 에 [Azure portal][azure-portal], 가상 컴퓨터를 배포할 가상 네트워크로 이동 합니다.
  1. 아래 **설정을**를 선택 **서브넷**합니다.
  1. 가상 컴퓨터를 배포할 서브넷을 선택 합니다.
  1. 아래 **서비스 끝점**에 대 한 확인란의 선택을 해제 **Microsoft.ContainerRegistry**합니다. 
  1. **저장**을 선택합니다.

* 레지스트리에 액세스 하는 서브넷을 허용 하는 네트워크 규칙을 제거 합니다.

  1. 포털에서 컨테이너 레지스트리로 이동 합니다.
  1. 아래 **설정을**를 선택 **방화벽 및 virtual network**합니다.
  1. 아래 **가상 네트워크**가상 네트워크의 이름을 선택 하 고 선택한 **제거**합니다.
  1. **저장**을 선택합니다.

#### <a name="add-network-rule-to-registry"></a>레지스트리에 네트워크 규칙 추가

1. 포털에서 컨테이너 레지스트리로 이동 합니다.
1. 아래 **설정을**를 선택 **방화벽 및 virtual network**합니다.
1. 액세스를 허용 하도록 선택 하는 이미 않았다면 **선택한 네트워크**합니다. 
1. 아래 **가상 네트워크**, 없습니다 네트워크가 선택 되었는지 확인 합니다.
1. 아래 **방화벽**, VM의 공용 IP 주소를 입력 합니다. 또는 VM의 IP 주소를 포함 하는 CIDR 표기법의 주소 범위를 입력 합니다.
1. **저장**을 선택합니다.

![컨테이너 레지스트리에 대 한 방화벽 규칙 구성][acr-vnet-firewall-portal]

계속 해 서 [레지스트리에 액세스할 수 있는지 확인](#verify-access-to-the-registry)합니다.

## <a name="verify-access-to-the-registry"></a>레지스트리에 액세스할 수 있는지 확인

구성을 업데이트를 확인 하려면 잠시 기다린 후 VM에는 컨테이너 레지스트리에 액세스할 수 있습니다. VM에 SSH 연결을 설정 하 고 실행 합니다 [az acr login] [ az-acr-login] 레지스트리에 로그인 명령입니다. 

```bash
az acr login --name mycontainerregistry
```

실행 같은 레지스트리 작업을 수행할 수 있습니다 `docker pull` 레지스트리에서 샘플 이미지를 가져오려고 합니다. 이미지 및 태그 값을 레지스트리 로그인 서버 이름 (모두 소문자)을 접두사로 레지스트리에 대 한 적절 한 대체 합니다.

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker VM에 성공적으로 이미지를 끌어옵니다.

이 예제에서는 네트워크 액세스 규칙을 통해 개인 컨테이너 레지스트리에 액세스할 수 있는지 보여 줍니다. 그러나 레지스트리는 네트워크 액세스 규칙을 구성 하지 않은 다른 로그인 호스트에서 액세스할 수 없습니다. 사용 하 여 다른 호스트에서 로그인 하려고 하면를 `az acr login` 명령 또는 `docker login` 명령 출력은 다음과 유사 합니다.

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>기본 레지스트리 액세스를 복원 합니다.

기본적으로 액세스를 허용 하도록 레지스트리를 복원 하려면 구성 된 모든 네트워크 규칙을 제거 합니다. 액세스할 수 있도록 기본 동작을 설정 합니다. Azure CLI 및 Azure portal을 사용 하 여 해당 단계가 제공 됩니다.

### <a name="restore-default-registry-access---cli"></a>기본 레지스트리 액세스-CLI를 복원 합니다.

#### <a name="remove-network-rules"></a>네트워크 규칙 제거

레지스트리에 대 한 구성 하는 네트워크 규칙의 목록을 보려면 다음을 실행 [az acr 네트워크 규칙 목록을] [ az-acr-network-rule-list] 명령:

```azurecli
az acr network-rule list--name mycontainerregistry 
```

구성 된 각 규칙에 대해 실행 합니다 [az acr 네트워크 규칙을 제거] [ az-acr-network-rule-remove] 제거 하는 명령입니다. 예를 들면 다음과 같습니다.

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>액세스 허용

다음에서 레지스트리의 이름으로 대체 [az acr update] [ az-acr-update] 명령:
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>복원 기본 레지스트리 액세스-포털


1. 포털에서 컨테이너 레지스트리로 이동 하 고 선택 **방화벽 및 virtual network**합니다.
1. 아래 **가상 네트워크**, 각 가상 네트워크를 선택 하 고 선택한 **제거**합니다.
1. 아래 **방화벽**각 주소 범위를 선택한 다음 [삭제] 아이콘을 선택 합니다.
1. 아래 **에서 액세스할 수 있도록**를 선택 **모든 네트워크**합니다. 
1. **저장**을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

단일을 사용 하 여 리소스를 선택적으로 삭제할 수 같은 리소스에서 Azure 리소스 그룹 및 더 이상 필요 하 고 모든 작업을 만든 경우 [az 그룹 삭제](/cli/azure/group) 명령:

```azurecli
az group delete --name myResourceGroup
```

포털에서 리소스를 정리 하려면 myResourceGroup 리소스 그룹으로 이동 합니다. 리소스 그룹이 로드 되 면 클릭할 **리소스 그룹 삭제** 리소스 그룹 및 여기에 저장 된 리소스를 제거 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 여러 가상 네트워크 리소스와 기능에 대해 간략하게 설명했습니다. Azure Virtual Network 설명서에서는 다음 주제에 대해 광범위하게 설명합니다.

* [가상 네트워크](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [서브넷](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
