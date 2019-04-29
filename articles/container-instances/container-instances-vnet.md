---
title: Azure Virtual Network에 컨테이너 인스턴스 배포
description: 신규 또는 기존 Azure Virtual Network에 컨테이너 그룹을 배포하는 방법에 대해 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: a4da7a23d6dcb50164829507130fed145abeebbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684224"
---
# <a name="deploy-container-instances-into-an-azure-virtual-network"></a>Azure Virtual Network에 컨테이너 인스턴스 배포

[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) 온-프레미스 리소스 및 Azure에 대 한 보안, 개인 네트워킹을 제공 합니다. 컨테이너 그룹을 Azure Virtual Network에 배포하면 컨테이너가 가상 네트워크의 다른 리소스와 안전하게 통신할 수 있습니다.

Azure Virtual Network에 컨테이너 그룹을 배포하는 경우 다음과 같은 시나리오를 수행할 수 있습니다.

* 동일한 서브넷의 컨테이너 그룹 간 직접 통신
* 컨테이너 인스턴스에서 가상 네트워크의 데이터베이스로 [작업 기반](container-instances-restart-policy.md) 워크로드 전송
* 가상 네트워크의 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)에서 컨테이너 인스턴스의 콘텐츠 검색
* 가상 네트워크의 가상 머신과 컨테이너 간 통신
* [VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md)를 통한 온-프레미스 리소스와 컨테이너 간 통신

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="virtual-network-deployment-limitations"></a>가상 네트워크 배포 제한 사항

가상 네트워크에 컨테이너 그룹을 배포할 때는 특정 제한 사항이 적용됩니다.

* 컨테이너 그룹을 배포하려는 서브넷에는 다른 리소스 종류가 포함되어 있지 않아야 합니다. 컨테이너 그룹을 배포하기 전에 기존 서브넷에서 기존 리소스를 모두 제거하거나 새 서브넷을 만들어야 합니다.
* 가상 네트워크에 배포된 컨테이너 그룹의 [관리 ID](container-instances-managed-identity.md)를 사용할 수 없습니다.
* 가상 네트워크에 컨테이너 그룹을 배포할 때는 네트워킹 리소스가 추가로 사용되므로 대개 표준 컨테이너 인스턴스를 배포할 때보다 속도가 다소 느립니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

이 기능은 미리 보기 상태인 동안 가상 네트워크에 컨테이너 그룹을 배포 하는 경우 다음과 같은 제한 사항이 적용 됩니다. 

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

컨테이너 리소스 제한은 이러한 지역의 비네트워크 컨테이너 인스턴스에 대한 제한과 다를 수 있습니다. 현재 Linux 컨테이너만 이 기능이 지원됩니다. Windows 지원이 예정되어 있습니다.

### <a name="unsupported-networking-scenarios"></a>지원 되지 않는 네트워킹 시나리오 

* **Azure Load Balancer** -네트워크에 연결 된 컨테이너 그룹의 Azure Load Balancer를 사용 하 여 컨테이너 인스턴스 앞에 배치할 수 없습니다.
* **가상 네트워크 피어 링** -다른 가상 네트워크에 Azure Container Instances에 위임 하는 서브넷을 포함 하는 가상 네트워크를 피어 링 할 수 없습니다.
* **경로 테이블** -Azure Container Instances에 위임 하는 서브넷에서 사용자 정의 경로 설정할 수 없습니다
* **네트워크 보안 그룹** -Azure Container Instances에 위임 하는 서브넷에 적용 된 Nsg의 아웃 바운드 보안 규칙 현재 적용 되지 않고 
* **공용 IP 또는 DNS 레이블** -가상 네트워크에 배포 하는 컨테이너 그룹 현재 공용 IP 주소 또는 정규화 된 도메인 이름을 사용 하 여 인터넷에 직접 노출 컨테이너를 지원 하지 않습니다
* **내부 이름 확인** -내부 Azure DNS를 통해 가상 네트워크에서 Azure 리소스에 대 한 이름 확인이 지원 되지 않습니다

**네트워크 리소스 삭제** 시에는 가상 네트워크에 컨테이너 그룹을 배포한 후 [추가 단계](#delete-network-resources)를 수행해야 합니다.

## <a name="required-network-resources"></a>필요한 네트워크 리소스

가상 네트워크에 컨테이너 그룹을 배포할 때 필요한 세 가지 Azure Virtual Network 리소스는 [가상 네트워크](#virtual-network) 자체, 가상 네트워크 내의 [위임된 서브넷](#subnet-delegated) 및 [네트워크 프로필](#network-profile)입니다. 

### <a name="virtual-network"></a>가상 네트워크

가상 네트워크는 주소 공간을 정의합니다. 이 주소 공간에 서브넷을 하나 이상 만든 다음 컨테이너 그룹 등의 Azure 리소스를 가상 네트워크의 서브넷에 배포합니다.

### <a name="subnet-delegated"></a>서브넷(위임)

서브넷은 가상 네트워크를 서브넷에 배치하는 Azure 리소스가 사용할 수 있는 개별 주소 공간으로 구분합니다. 가상 네트워크 내에 하나 이상의 서브넷을 만들 수 있습니다.

컨테이너 그룹용으로 사용하는 서브넷은 컨테이너 그룹만 포함할 수 있습니다. 서브넷에 컨테이너 그룹을 처음 배포할 때 Azure는 Azure Container Instances에 해당 서브넷을 위임합니다. 위임된 서브넷은 컨테이너 그룹에만 사용할 수 있습니다. 위임된 서브넷에 컨테이너 그룹 이외의 리소스를 배포하려고 하면 작업이 실패합니다.

### <a name="network-profile"></a>네트워크 프로필

Azure 리소스용 네트워크 구성 템플릿인 네트워크 프로필은 리소스를 배포해야 하는 서브넷 등 리소스의 특정 네트워크 속성을 지정합니다. 처음 [az container create][az-container-create] 명령을 사용하여 서브넷(가상 네트워크)에 컨테이너 그룹을 배포하는 경우 Azure가 사용자를 위해 네트워크 프로필을 만듭니다. 그러면 다음에 컨테이너 그룹을 서브넷에 배포할 때 해당 네트워크 프로필을 사용할 수 있습니다. 

Resource Manager 템플릿, YAML 파일 또는 프로그래밍 방식을 사용하여 서브넷에 컨테이너 그룹을 배포하려면 네트워크 프로필의 전체 Resource Manager 리소스 ID를 제공해야 합니다. [az container create][az-container-create]를 사용하여 이전에 만든 프로필을 사용할 수도 있고 Resource Manager 템플릿을 사용하여 프로필을 만들 수도 있습니다([템플릿 예제](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) 및 [참조](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles) 참조). 이전에 만든 프로필의 ID를 가져오려면 [az network profile list][az-network-profile-list] 명령을 사용합니다. 

아래 다이어그램에서는 Azure Container Instances에 위임된 서브넷에 컨테이너 그룹이 여러 개 배포되어 있습니다. 컨테이너 그룹 하나를 서브넷에 배포한 후에는 같은 네트워크 프로필을 지정하여 추가 컨테이너 그룹을 배포할 수 있습니다.

![가상 네트워크 내의 컨테이너 그룹][aci-vnet-01]

## <a name="deployment-scenarios"></a>배포 시나리오

[az container create][az-container-create]를 사용하여 새 가상 네트워크에 컨테이너 그룹을 배포하고 Azure가 사용자에게 필요한 네트워크 리소스를 만들도록 허용하거나 기존 가상 네트워크에 배포할 수 있습니다. 

### <a name="new-virtual-network"></a>새 가상 네트워크

새 가상 네트워크에 배포하고 Azure에서 네트워크 리소스를 자동으로 생성하도록 하려는 경우 [az container create][az-container-create] 실행 시에 다음 항목을 지정합니다.

* 가상 네트워크 이름
* CIDR 형식의 가상 네트워크 주소 접두사
* 서브넷 이름
* CIDR 형식의 서브넷 주소 접두사

가상 네트워크 및 서브넷 주소 접두사는 각각 가상 네트워크와 서브넷의 주소 공간을 지정합니다. 이러한 값은 `10.0.0.0/16`과 같이 CIDR(Classless Inter-Domain Routing) 표기법으로 표시됩니다. 서브넷 사용 방법에 대한 자세한 내용은 [가상 네트워크 서브넷 추가, 변경 또는 삭제](../virtual-network/virtual-network-manage-subnet.md)를 참조하세요.

이 방법으로 첫 번째 컨테이너 그룹을 배포한 후에는 가상 네트워크 및 서브넷 이름이나 Azure에서 자동으로 생성한 네트워크 프로필을 지정하여 같은 서브넷에 컨테이너 그룹을 배포할 수 있습니다. 서브넷은 Azure Container Instances에 위임되므로 서브넷에는 컨테이너 그룹*만* 배포할 수 있습니다.

### <a name="existing-virtual-network"></a>기존 가상 네트워크

기존 가상 네트워크에 컨테이너 그룹을 배포하려면

1. 기존 가상 네트워크 내에 서브넷을 만들거나 기존 서브넷에서 기타 리소스를 *모두* 비웁니다.
1. [az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포하고 다음 중 하나를 지정합니다.
   * 가상 네트워크 이름 및 서브넷 이름
   * 다른 리소스 그룹의 가상 네트워크를 사용할 수 있는 가상 네트워크 리소스 ID 및 서브넷 리소스 ID
   * [az network profile list][az-network-profile-list]를 사용하여 가져올 수 있는 네트워크 프로필 이름 또는 ID

기존 서브넷에 첫 번째 컨테이너 그룹을 배포하면 Azure Container Instances에 해당 서브넷이 위임됩니다. 해당 서브넷에는 컨테이너 그룹 이외의 리소스를 더 이상 배포할 수 없습니다.

## <a name="deployment-examples"></a>배포 예제

다음 섹션에서는 Azure CLI를 사용하여 가상 네트워크에 컨테이너 그룹을 배포하는 방법을 설명합니다. 명령 예제는 **Bash** 셸용으로 서식이 지정되어 있습니다. PowerShell이나 명령 프롬프트 등의 다른 셸을 사용하려는 경우 해당 셸에 따라 줄 연속 문자를 조정하세요.

### <a name="deploy-to-a-new-virtual-network"></a>새 가상 네트워크에 배포

먼저 컨테이너 그룹을 배포하고 새 가상 네트워크와 서브넷의 매개 변수를 지정합니다. 이러한 매개 변수를 지정하면 가상 네트워크와 서브넷이 생성되고, Azure Container instances에 서브넷이 위임되며, 네트워크 프로필도 작성됩니다. 리소스를 만들고 나면 컨테이너 그룹이 서브넷에 배포됩니다.

새 가상 네트워크와 서브넷의 설정을 지정하는 다음 [az container create][az-container-create] 명령을 실행합니다. 가상 네트워크에서 컨테이너 그룹을 [지원](#preview-limitations)하는 지역에 만들어진 리소스 그룹의 이름을 제공해야 합니다. 이 명령은 공용 Microsoft 배포 [aci helloworld] [ aci-helloworld] 정적 웹 페이지 처리는 작은 Node.js 웹 서버를 실행 하는 컨테이너입니다. 다음 섹션에서는 같은 서브넷에 두 번째 컨테이너 그룹을 배포하고 두 컨테이너 인스턴스 간의 통신을 테스트합니다.

```azurecli
az container create \
    --name appcontainer \
    --resource-group myResourceGroup \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --vnet aci-vnet \
    --vnet-address-prefix 10.0.0.0/16 \
    --subnet aci-subnet \
    --subnet-address-prefix 10.0.0.0/24
```

이 방법을 사용하여 새 가상 네트워크에 배포할 때는 네트워크 리소스가 생성되는 과정에서 배포가 몇 분 정도 소요될 수 있습니다. 초기 배포 후의 추가 컨테이너 그룹 배포는 더 빨리 완료됩니다.

### <a name="deploy-to-existing-virtual-network"></a>기존 가상 네트워크에 배포

이제 새 가상 네트워크에 컨테이너 그룹을 배포했으므로 같은 서브넷에 두 번째 컨테이너 그룹을 배포하고 두 컨테이너 인스턴스 간의 통신을 확인합니다.

먼저 첫 번째로 배포한 컨테이너 그룹인 *appcontainer*의 IP 주소를 가져옵니다.

```azurecli
az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
```

출력에는 개인 서브넷의 컨테이너 그룹 IP 주소가 표시됩니다.

```console
$ az container show --resource-group myResourceGroup --name appcontainer --query ipAddress.ip --output tsv
10.0.0.4
```

이제 `CONTAINER_GROUP_IP`를 `az container show` 명령으로 검색한 IP로 설정하고 다음 `az container create` 명령을 실행합니다. 두 번째 컨테이너인 *commchecker*는 Alpine Linux 기반 이미지를 실행하며 첫 번째 컨테이너 그룹의 개인 서브넷 IP 주소에 대해 `wget`을 실행합니다.

```azurecli
CONTAINER_GROUP_IP=<container-group-IP-here>

az container create \
    --resource-group myResourceGroup \
    --name commchecker \
    --image alpine:3.5 \
    --command-line "wget $CONTAINER_GROUP_IP" \
    --restart-policy never \
    --vnet aci-vnet \
    --subnet aci-subnet
```

이 두 번째 컨테이너 배포가 완료되면 실행된 `wget` 명령의 출력을 확인할 수 있도록 해당 로그를 끌어옵니다.

```azurecli
az container logs --resource-group myResourceGroup --name commchecker
```

두 번째 컨테이너가 첫 번째 컨테이너와 정상적으로 통신했다면 출력은 다음과 같이 표시됩니다.

```console
$ az container logs --resource-group myResourceGroup --name commchecker
Connecting to 10.0.0.4 (10.0.0.4:80)
index.html           100% |*******************************|  1663   0:00:00 ETA
```

로그 출력에는 `wget`이 로컬 서브넷의 개인 IP 주소를 사용하여 첫 번째 컨테이너에 연결한 다음 해당 컨테이너에서 인덱스 파일을 다운로드할 수 있었다는 내용이 표시되어야 합니다. 두 컨테이너 그룹 간의 네트워크 트래픽은 가상 네트워크 내에서 그대로 유지됩니다.

### <a name="deploy-to-existing-virtual-network---yaml"></a>기존 가상 네트워크에 배포 - YAML

YAML 파일을 사용하여 기존 가상 네트워크에 컨테이너 그룹을 배포할 수도 있습니다. 가상 네트워크의 서브넷에 배포하려면 YAML에서 몇 가지 추가 속성을 지정합니다.

* `ipAddress`: 컨테이너 그룹의 IP 주소 설정입니다.
  * `ports`: 열려는 포트(있는 경우)입니다.
  * `protocol`: 열려 있는 포트의 프로토콜(TCP 또는 UDP)입니다.
* `networkProfile`: Azure 리소스에 대한 가상 네트워크 및 서브넷과 같이 네트워크 설정을 지정합니다.
  * `id`: `networkProfile`의 전체 Resource Manager 리소스 ID입니다.

YAML 파일을 사용하여 가상 네트워크에 컨테이너 그룹을 배포하려면 먼저 네트워크 프로필의 ID를 가져와야 합니다. 가상 네트워크와 위임된 서브넷을 포함하는 리소스 그룹의 이름을 지정하여 [az network profile list][az-network-profile-list] 명령을 실행합니다.

``` azurecli
az network profile list --resource-group myResourceGroup --query [0].id --output tsv
```

명령의 출력에는 네트워크 프로필의 전체 리소스 ID가 표시됩니다.

```console
$ az network profile list --resource-group myResourceGroup --query [0].id --output tsv
/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-aci-subnet
```

네트워크 프로필 ID를 가져온 후 다음 YAML을 새 파일 *vnet-deploy-aci.yaml*에 복사합니다. `networkProfile` 아래의 `id` 값을 방금 검색한 ID로 바꾸고 파일을 저장합니다. 이 YAML은 가상 네트워크에 컨테이너 그룹 *appcontaineryaml*을 만듭니다.

```YAML
apiVersion: '2018-09-01'
location: westus
name: appcontaineryaml
properties:
  containers:
  - name: appcontaineryaml
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  ipAddress:
    type: Private
    ports:
    - protocol: tcp
      port: '80'
  networkProfile:
    id: /subscriptions/<Subscription ID>/resourceGroups/container/providers/Microsoft.Network/networkProfiles/aci-network-profile-aci-vnet-subnet
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

`--file` 매개 변수로 YAML 파일 이름을 지정한 [az container create][az-container-create] 명령을 사용하여 컨테이너 그룹을 배포합니다.

```azurecli
az container create --resource-group myResourceGroup --file vnet-deploy-aci.yaml
```

배포가 완료되면 [az container show][az-container-show] 명령을 실행하여 배포 상태를 표시합니다.

```console
$ az container show --resource-group myResourceGroup --name appcontaineryaml --output table
Name              ResourceGroup    Status    Image                                       IP:ports     Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  ------------------------------------------  -----------  ---------  ---------------  --------  ----------
appcontaineryaml  myResourceGroup  Running   mcr.microsoft.com/azuredocs/aci-helloworld  10.0.0.5:80  Private    1.0 core/1.5 gb  Linux     westus
```

## <a name="clean-up-resources"></a>리소스 정리

### <a name="delete-container-instances"></a>컨테이너 인스턴스 삭제

작성한 컨테이너를 사용하는 작업이 완료되었으면 다음 명령을 사용하여 컨테이너를 삭제합니다.

```azurecli
az container delete --resource-group myResourceGroup --name appcontainer -y
az container delete --resource-group myResourceGroup --name commchecker -y
az container delete --resource-group myResourceGroup --name appcontaineryaml -y
```

### <a name="delete-network-resources"></a>네트워크 리소스 삭제

이 기능의 초기 미리 보기에서는 앞에서 만든 네트워크 리소스를 삭제하려면 여러 명령을 추가로 실행해야 합니다. 이 문서의 이전 섹션에 나왔던 예제 명령을 사용하여 가상 네트워크와 서브넷을 만들었다면 아래 스크립트를 사용하여 해당 네트워크 리소스를 삭제할 수 있습니다.

스크립트를 실행하기 전에 `RES_GROUP` 변수를 삭제해야 하는 가상 네트워크 및 서브넷이 포함된 리소스 그룹의 이름으로 설정합니다. 스크립트는 Bash 셸에 대해 서식이 지정됩니다. PowerShell이나 명령 프롬프트 등의 다른 셸을 사용하려는 경우 해당 셸에 따라 변수 할당과 접근자를 조정해야 합니다.

> [!WARNING]
> 이 스크립트는 리소스, 즉 가상 네트워크 및 해당 네트워크에 포함된 모든 서브넷을 삭제합니다. 그러므로 이 스크립트를 실행하기 전에 가상 네트워크에 포함된 서브넷을 비롯하여 가상 네트워크의 *모든* 리소스가 더 이상 필요하지 않은지 확인하세요. 삭제된 **리소스는 복구할 수 없습니다**.

```azurecli
# Replace <my-resource-group> with the name of your resource group
RES_GROUP=<my-resource-group>

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet --remove delegations 0

# Delete the subnet
az network vnet subnet delete --resource-group $RES_GROUP --vnet-name aci-vnet --name aci-subnet

# Delete virtual network
az network vnet delete --resource-group $RES_GROUP --name aci-vnet
```

## <a name="next-steps"></a>다음 단계

Resource Manager 템플릿을 사용하여 새 가상 네트워크, 서브넷, 네트워크 프로필 및 컨테이너 그룹을 배포하려면 [VNet을 사용하여 Azure 컨테이너 그룹 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
)를 참조하세요.

이 문서에서는 여러 가상 네트워크 리소스와 기능에 대해 간략하게 설명했습니다. Azure Virtual Network 설명서에서는 다음 주제에 대해 광범위하게 설명합니다.

* [가상 네트워크](../virtual-network/manage-virtual-network.md)
* [서브넷](../virtual-network/virtual-network-manage-subnet.md)
* [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)
* [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [ExpressRoute](../expressroute/expressroute-introduction.md)

<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
