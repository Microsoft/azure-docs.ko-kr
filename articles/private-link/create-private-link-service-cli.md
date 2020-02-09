---
title: Azure CLI를 사용 하 여 Azure 개인 링크 서비스 만들기
description: Azure CLI를 사용 하 여 Azure 개인 링크 서비스를 만드는 방법을 알아봅니다.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 6e6148d305af26f7933567ae58023d2ba73263eb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350236"
---
# <a name="create-a-private-link-service-using-azure-cli"></a>Azure CLI를 사용 하 여 개인 링크 서비스 만들기
이 문서에서는 Azure CLI를 사용 하 여 Azure에서 개인 링크 서비스를 만드는 방법을 보여 줍니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 설치 하 고 사용 하도록 결정 한 경우이 빠른 시작을 사용 하려면 최신 Azure CLI 버전을 사용 해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기
### <a name="create-a-resource-group"></a>리소스 그룹 만들기

가상 네트워크를 만들려면 먼저 가상 네트워크를 호스트할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹을 *westcentralus* 위치에 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### <a name="create-a-virtual-network"></a>가상 네트워크 만들기
[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)를 사용하여 가상 네트워크를 만듭니다. 이 예제에서는 *Mysubnet*이라는 서브넷 하나를 사용 하 여 *myVirtualNetwork* 라는 기본 가상 네트워크를 만듭니다.

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### <a name="create-a-subnet"></a>서브넷 만들기
[Az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)를 사용 하 여 가상 네트워크에 대 한 서브넷을 만듭니다. 이 예제에서는 *myVirtualNetwork* 가상 네트워크에 *mysubnet* 이라는 서브넷을 만듭니다.

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### <a name="create-a-internal-load-balancer"></a>내부 Load Balancer 만들기 
[Az network lb create](/cli/azure/network/lb#az-network-lb-create)를 사용 하 여 내부 부하 분산 장치를 만듭니다. 이 예제에서는 *Myresourcegroup*이라는 리소스 그룹에 *myilb* 라는 내부 부하 분산 장치를 만듭니다. 

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myILB --sku standard --vnet-name MyVirtualNetwork --subnet mySubnet --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool
```

### <a name="create-a-load-balancer-health-probe"></a>부하 분산 장치 상태 프로브 만들기

상태 프로브는 모든 가상 머신 인스턴스를 검사하여 네트워크 트래픽을 받을 수 있는지 확인합니다. 프로브 검사에 실패한 가상 머신 인스턴스는 다시 온라인 상태가 되어 프로브 검사가 정상으로 나올 때까지 부하 분산 장치에서 제거됩니다. [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest)를 사용하여 가상 머신의 상태를 모니터링하는 상태 프로브를 만듭니다. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성 및 트래픽을 받을 백 엔드 IP 풀과 필요한 원본 및 대상 포트를 함께 정의합니다. [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest)를 사용하여 *myFrontEnd* 프런트 엔드 풀의 80 포트에서 수신 대기하고, 마찬가지로 80 포트를 통해 부하 분산된 네트워크 트래픽을 *myBackEndPool* 백 엔드 주소 풀에 보내는 *myHTTPRule* 부하 분산 장치 규칙을 만듭니다. 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myILB \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```
### <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 예제에서는 가상 컴퓨터 만들기에 대해 다루지 않습니다. [내부 부하 분산 장치 만들기의 단계에 따라 Azure CLI를 사용 하 여 vm 부하를 분산 하](../load-balancer/load-balancer-get-started-ilb-arm-cli.md#create-servers-for-the-backend-address-pool) 여 부하 분산 장치에 대 한 백 엔드 서버로 사용할 두 개의 가상 머신을 만들 수 있습니다. 


### <a name="disable-private-link-service-network-policies-on-subnet"></a>서브넷에서 개인 링크 서비스 네트워크 정책 사용 안 함 
개인 링크 서비스에는 가상 네트워크 내에서 선택한 서브넷의 IP가 필요 합니다. 현재는 이러한 Ip에서 네트워크 정책을 지원 하지 않습니다.  따라서 서브넷에서 네트워크 정책을 사용 하지 않도록 설정 해야 합니다. [Az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)를 사용 하 여 개인 링크 서비스 네트워크 정책을 사용 하지 않도록 서브넷을 업데이트 합니다.

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --disable-private-link-service-network-policies true 
```
 
## <a name="create-a-private-link-service"></a>Private Link 서비스 만들기  
 
[Az network Private-Link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create)를 사용 하 여 표준 Load Balancer 프런트 엔드 IP 구성을 사용 하 여 개인 링크 서비스를 만듭니다. 이 예제에서는 *Myloadbalancer*이라는 리소스 그룹에서 *myloadbalancer* 라는 표준 Load Balancer 사용 하 여 *myPLS* 이라는 개인 링크 서비스를 만듭니다. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myILB \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
일단 만들어지면 개인 링크 서비스 ID를 기록해 둡니다. 나중에이 서비스에 대 한 연결을 요청 하는 데 필요 합니다.  
 
이 단계에서는 개인 링크 서비스가 성공적으로 생성 되 고 트래픽을 받을 준비가 된 것입니다. 위의 예제에서는 Azure CLI 사용 하 여 개인 링크 서비스를 만드는 방법을 보여 줍니다.  트래픽 수신 대기를 위해 부하 분산 장치 백 엔드 풀 또는 백 엔드 풀의 응용 프로그램을 구성 하지 않았습니다. 종단 간 트래픽 흐름을 보려면 표준 Load Balancer 뒤에 응용 프로그램을 구성 하는 것이 좋습니다.  
 
다음으로 Azure CLI를 사용 하 여이 서비스를 다른 가상 네트워크의 개인 엔드포인트에 매핑하는 방법을 설명 합니다. 이 예제는 개인 엔드포인트을 만들고 Azure CLI를 사용 하 여 위에서 만든 개인 링크 서비스에 연결 하는 것으로 제한 됩니다. 또한 가상 네트워크에서 가상 컴퓨터를 만들어 개인 엔드포인트으로 트래픽을 보내고 받을 수 있습니다.        
 
## <a name="private-endpoints"></a>프라이빗 엔드포인트

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기 
 [Az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)를 사용 하 여 가상 네트워크를 만듭니다. 이 예제에서는 *Myresourcegroup*이라는 리소스 그룹에  *myPEVNet* 라는 가상 네트워크를 만듭니다. 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### <a name="create-the-subnet"></a>서브넷 만들기 
 [Az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create)를 사용 하 여 가상 네트워크에서 서브넷을 만듭니다. 이 예제에서는 *Mysubnet*이라는 리소스 그룹에서 *myPEVnet* 라는 가상 네트워크에 *mysubnet* 이라는 서브넷을 만듭니다. 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## <a name="disable-private-endpoint-network-policies-on-subnet"></a>서브넷에서 개인 엔드포인트 네트워크 정책 사용 안 함 
가상 네트워크 내에서 원하는 모든 서브넷에 개인 엔드포인트을 만들 수 있습니다. 현재 개인 엔드포인트에서는 네트워크 정책을 지원 하지 않습니다.  따라서 서브넷에서 네트워크 정책을 사용 하지 않도록 설정 해야 합니다. [Az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)를 사용 하 여 개인 엔드포인트 네트워크 정책을 사용 하지 않도록 서브넷을 업데이트 합니다. 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## <a name="create-private-endpoint-and-connect-to-private-link-service"></a>개인 엔드포인트을 만들고 개인 링크 서비스에 연결 
가상 네트워크에서 위에서 만든 개인 링크 서비스를 사용 하기 위한 개인 엔드포인트을 만듭니다.
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
개인 링크 서비스에서 `az network private-link-service show`를 사용 하 여 *개인 연결 리소스 id* 를 가져올 수 있습니다. ID는 다음과 같습니다.   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename** 
 
## <a name="show-private-link-service-connections"></a>개인 링크 서비스 연결 표시 
 
[Az network Private-link-service show](/cli/azure/network/private-link-service#az-network-private-link-service-show)를 사용 하 여 개인 링크 서비스에서 연결 요청을 참조 하세요.    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## <a name="next-steps"></a>다음 단계
- [Azure 개인 링크 서비스](private-link-service-overview.md) 에 대 한 자세한 정보
 
