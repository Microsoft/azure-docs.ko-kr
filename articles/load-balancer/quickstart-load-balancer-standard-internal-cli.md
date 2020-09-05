---
title: '빠른 시작: 내부 부하 분산 장치 만들기 - Azure CLI'
titleSuffix: Azure Load Balancer
description: 이 빠른 시작에서는 Azure CLI를 사용하여 내부 부하 분산 장치를 만드는 방법을 보여줍니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: allensu
ms.custom: mvc, devx-track-javascript, devx-track-azurecli
ms.openlocfilehash: 3016825648ef45f8aa64b4228bac2b1f830004c3
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88763855"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 VM 부하를 분산하는 내부 부하 분산 장치 만들기

Azure CLI에서 Azure Load Balancer를 시작하여 공용 부하 분산 장치와 세 개의 가상 머신을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 로컬로 설치된 Azure CLI 또는 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

로컬로 CLI를 설치하여 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상이 필요합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)를 사용하여 리소스 그룹을 만듭니다.

* 이름: **myResourceGroupLB** 
* 위치: **eastus**

```azurecli-interactive
  az group create \
    --name myResourceGroupLB \
    --location eastus
```
---

# <a name="standard-sku"></a>[**표준 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 배포하기 전에 지원되는 가상 네트워크 리소스부터 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.1.0.0/16**의 주소 접두사.
* 서브넷: **myBackendSubnet**
* **10.1.0.0/24**의 서브넷 접두사.
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 네트워크 보안 그룹에 속한 네트워크 인터페이스가 백 엔드 주소의 VM에 있어야 합니다. 

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)를 사용하여 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹: **myResourceGroupLB**

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

[az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* 이전 단계에서 만든 네트워크 보안 그룹의 **myNSG**
* 리소스 그룹: **myResourceGroupLB**
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 원본: **(*)** .
* 대상: **(*)** .
* 대상 포트: **포트 80**.
* 액세스: **허용**
* 우선 순위: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>가상 머신에 대한 네트워크 인터페이스 만들기

[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)를 사용하여 두 개의 네트워크 인터페이스를 만듭니다.

#### <a name="vm1"></a>VM1

* 이름: **myNicVM1**
* 리소스 그룹: **myResourceGroupLB**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* 이름: **myNicVM2**
* 리소스 그룹: **myResourceGroupLB**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 섹션에서는 다음을 만듭니다.

* 서버 구성을 위한 클라우드 구성 파일 **cloud-init.txt**
* 부하 분산 장치의 백 엔드 서버로 사용할 두개의 가상 머신.

### <a name="create-cloud-init-configuration-file"></a>cloud-init 구성 파일 만들기

cloud-init 구성 파일을 사용하여 NGINX를 설치하고 Linux 가상 머신에서 'Hello World' Node.js 앱을 실행할 수 있습니다. 

현재 셸에서 cloud-init.txt라는 파일을 만듭니다. 다음 구성을 복사하여 셸에 붙여넣습니다. 전체 cloud-init 파일, 특히 첫 번째 줄을 올바르게 복사해야 합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)를 사용하여 가상 머신을 만듭니다.

#### <a name="vm1"></a>VM1
* 이름: **myVM1**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1**에 연결됨
* 가상 머신 이미지: **UbuntuLTS**
* 위의 이전 단계에서 만든 구성 파일 **cloud-init.txt**
* 위치: **영역 1**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* 이름: **myVM2**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2**에 연결됨
* 가상 머신 이미지: **UbuntuLTS**
* 위의 이전 단계에서 만든 구성 파일 **cloud-init.txt**
* 위치: **영역 2**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-standard-load-balancer"></a>표준 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)를 사용하여 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* **myFrontEnd**라는 프런트 엔드 풀
* **myBackEndPool**이라는 백 엔드 풀
* 가상 네트워크 **myVNet**과 연결됩니다.
* 백 엔드 서브넷 **myBackendSubnet**과 연결됩니다.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)를 사용하여 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd**의 **포트 80**에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool**에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe**를 사용합니다.
* 프로토콜: **TCP**
* 프런트 엔드 IP 주소를 사용하여 아웃바운드 SNAT(Source Network Address Translation)을 사용하도록 설정합니다.

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true 
```
>[!NOTE]
>백 엔드 풀의 가상 머신은 이 구성과 아웃바운드 인터넷 연결을 사용하지 않습니다. </br> 아웃바운드 연결 제공에 대한 자세한 내용은 다음을 참조하세요. </br> **[Azure에서 아웃바운드 연결](load-balancer-outbound-connections.md)**</br> 연결 제공 옵션: </br> **[아웃바운드 전용 부하 분산 장치 구성](egress-only.md)** </br> **[Virtual Network NAT란?](https://docs.microsoft.com/azure/virtual-network/nat-overview)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀에 가상 머신 추가

[az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다.


#### <a name="vm1"></a>VM1
* 백 엔드 주소 풀 **myBackEndPool**에 있습니다.
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* 백 엔드 주소 풀 **myBackEndPool**에 있습니다.
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**기본 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>표준 SKU 부하 분산 장치는 프로덕션 워크로드에 추천됩니다. SKU에 대한 자세한 내용은 **[Azure Load Balancer SKU](skus.md)** 를 참조하세요.

## <a name="configure-virtual-network"></a>가상 네트워크 구성

VM을 배포하고 부하 분산 장치를 배포하기 전에 지원되는 가상 네트워크 리소스부터 만듭니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt)를 사용하여 가상 네트워크를 만듭니다.

* 이름: **myVNet**
* **10.1.0.0/16**의 주소 접두사.
* 서브넷: **myBackendSubnet**
* **10.1.0.0/24**의 서브넷 접두사.
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupLB \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>네트워크 보안 그룹 만들기

표준 부하 분산 장치의 경우 네트워크 보안 그룹에 속한 네트워크 인터페이스가 백 엔드 주소의 VM에 있어야 합니다. 

[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create)를 사용하여 네트워크 보안 그룹을 만듭니다.

* 이름: **myNSG**
* 리소스 그룹: **myResourceGroupLB**

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupLB \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>네트워크 보안 그룹 규칙 만들기

[az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다.

* 이름: **myNSGRuleHTTP**
* 이전 단계에서 만든 네트워크 보안 그룹의 **myNSG**
* 리소스 그룹: **myResourceGroupLB**
* 프로토콜 **(*)** .
* 방향: **인바운드**
* 원본: **(*)** .
* 대상: **(*)** .
* 대상 포트: **포트 80**.
* 액세스: **허용**
* 우선 순위: **200**

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupLB \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>가상 머신에 대한 네트워크 인터페이스 만들기

[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)를 사용하여 두 개의 네트워크 인터페이스를 만듭니다.

#### <a name="vm1"></a>VM1

* 이름: **myNicVM1**
* 리소스 그룹: **myResourceGroupLB**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* 이름: **myNicVM2**
* 리소스 그룹: **myResourceGroupLB**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>백 엔드 서버 만들기

이 섹션에서는 다음을 만듭니다.

* 서버 구성을 위한 클라우드 구성 파일 **cloud-init.txt** 
* 가상 머신에 대한 가용성 집합
* 부하 분산 장치의 백 엔드 서버로 사용할 두개의 가상 머신.

부하 분산 장치가 성공적으로 만들어졌는지 확인하려면 가상 머신에 NGINX를 설치합니다.

### <a name="create-cloud-init-configuration-file"></a>cloud-init 구성 파일 만들기

cloud-init 구성 파일을 사용하여 NGINX를 설치하고 Linux 가상 머신에서 'Hello World' Node.js 앱을 실행할 수 있습니다. 

현재 셸에서 cloud-init.txt라는 파일을 만듭니다. 다음 구성을 복사하여 셸에 붙여넣습니다. 전체 cloud-init 파일, 특히 첫 번째 줄을 올바르게 복사해야 합니다.

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-availability-set-for-virtual-machines"></a>가상 머신에 대한 가용성 집합 만들기

[az vm availability-set create](https://docs.microsoft.com/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create)를 사용하여 가용성 집합을 만듭니다.

* 이름: **myAvSet**
* 리소스 그룹: **myResourceGroupLB**
* 위치: **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group myResourceGroupLB \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>가상 머신 만들기

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)를 사용하여 가상 머신을 만듭니다.

#### <a name="vm1"></a>VM1
* 이름: **myVM1**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1**에 연결됨
* 가상 머신 이미지: **UbuntuLTS**
* 위의 이전 단계에서 만든 구성 파일 **cloud-init.txt**
* 가용성 집합: **myAvSet**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* 이름: **myVM2**
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2**에 연결됨
* 가상 머신 이미지: **UbuntuLTS**
* 위의 이전 단계에서 만든 구성 파일 **cloud-init.txt**
* 위치: **영역 2**

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

VM을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-basic-load-balancer"></a>기본 부하 분산 장치 만들기

이 섹션에서는 다음과 같은 부하 분산 장치 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.

  * 부하 분산 장치에서 들어오는 네트워크 트래픽을 수신하는 프런트 엔드 IP 풀
  * 프런트 엔드 풀에서 부하 분산된 네트워크 트래픽을 보내는 백 엔드 IP 풀
  * 백 엔드 VM 인스턴스의 상태를 확인하는 상태 프로브.
  * 트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙

### <a name="create-the-load-balancer-resource"></a>부하 분산 장치 리소스 만들기

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create)를 사용하여 공용 부하 분산 장치를 만듭니다.

* 이름: **myLoadBalancer**
* **myFrontEnd**라는 프런트 엔드 풀
* **myBackEndPool**이라는 백 엔드 풀
* 가상 네트워크 **myVNet**과 연결됩니다.
* 백 엔드 서브넷 **myBackendSubnet**과 연결됩니다.

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>상태 프로브 만들기

상태 프로브는 네트워크 트래픽을 보낼 수 있도록 모든 가상 머신 인스턴스를 검사합니다. 

프로브 확인에 실패한 가상 머신은 부하 분산 장치에서 제거됩니다. 오류가 해결되면 가상 머신이 부하 분산 장치에 다시 추가됩니다.

[az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create)를 사용하여 상태 프로브를 만듭니다.

* 가상 머신의 상태 모니터링
* 이름: **myHealthProbe**
* 프로토콜: **TCP**
* **포트 80** 모니터링

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

다음과 같은 부하 분산 장치 규칙을 정의합니다.

* 들어오는 트래픽에 대한 프런트 엔드 IP 구성
* 트래픽을 수신할 백 엔드 IP 풀
* 필요한 원본 및 대상 포트 

[az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create)를 사용하여 부하 분산 장치 규칙을 만듭니다.

* 이름: **myHTTPRule**
* 프런트 엔드 풀 **myFrontEnd**의 **포트 80**에서 수신 대기
* **포트 80**.을 사용하여 백 엔드 주소 풀 **myBackEndPool**에 부하 분산된 네트워크 트래픽을 전송합니다. 
* 상태 프로브 **myHealthProbe**를 사용합니다.
* 프로토콜: **TCP**

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>부하 분산 장치 백 엔드 풀에 가상 머신 추가

[az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add)를 사용하여 백 엔드 풀에 가상 머신을 추가합니다.


#### <a name="vm1"></a>VM1
* 백 엔드 주소 풀 **myBackEndPool**에 있습니다.
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM1** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* 백 엔드 주소 풀 **myBackEndPool**에 있습니다.
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicVM2** 및 **ipconfig1**에 연결됨
* **myLoadBalancer**를 사용하여 부하 분산 장치에 연결됨

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group myResourceGroupLB \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

### <a name="create-azure-bastion-public-ip"></a>Azure Bastion 공용 IP 만들기

[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create)를 사용하여 베스천 호스트에 대한 공용 IP 주소를 만듭니다.

* **myBastionIP**라는 표준 영역 중복 공용 IP 주소를 만듭니다.
* 위치: **myResourceGroupLB**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Azure Bastion 서브넷 만들기

[az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create)를 사용하여 서브넷을 만듭니다.

* 이름은 **AzureBastionSubnet**입니다.
* **10.1.1.0/24**의 주소 접두사.
* 가상 네트워크: **myVNet**
* 리소스 그룹: **myResourceGroupLB**

```azurecli-interactive
  az network vnet subnet create \
    --resource-group myResourceGroupLB \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Azure Bastion 호스트 만들기
[az network bastion create](https://docs.microsoft.com/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create)를 사용하여 베스천 호스트를 만듭니다.

* 이름은 **myBastionHost**입니다.
* 위치: **myResourceGroupLB**
* 공용 IP **myBastionIP**와 연결됩니다.
* 가상 네트워크 **myVNet**과 연결됩니다.
* 위치: **eastus**.

```azurecli-interactive
  az network bastion create \
    --resource-group myResourceGroupLB \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
베스천 호스트를 배포하는 데 몇 분 정도 소요됩니다.

### <a name="create-test-virtual-machine"></a>테스트 가상 머신 만들기

[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create)를 사용하여 네트워크 인터페이스를 만듭니다.

* 이름은 **myNicTestVM**입니다.
* 리소스 그룹: **myResourceGroupLB**
* 가상 네트워크: **myVNet**
* 서브넷: **myBackendSubnet**
* 네트워크 보안 그룹: **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group myResourceGroupLB \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create)를 사용하여 가상 머신을 만듭니다.

* 이름은 **myTestVM**입니다.
* 리소스 그룹: **myResourceGroupLB**
* 네트워크 인터페이스 **myNicTestVM**에 연결됩니다.
* 가상 머신 이미지 **Win2019Datacenter**.
* **\<adminpass>** 및 **\<adminuser>** 에 대한 값을 선택합니다.
  

```azurecli-interactive
  az vm create \
    --resource-group myResourceGroupLB \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
가상 머신을 배포하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="test"></a>테스트

1. Azure Portal에 [로그인](https://portal.azure.com)합니다.

1. **개요** 화면에서 부하 분산 장치의 개인 IP 주소를 찾습니다. 왼쪽 메뉴에서 **모든 서비스**를 선택하고 **모든 리소스**를 선택한 다음, **myLoadBalancer**를 선택합니다.

2. **myLoadBalancer**의 **개요**에서 **개인 IP 주소** 옆에 있는 주소를 적어 두거나 복사해 둡니다.

3. 왼쪽 메뉴에서 **모든 서비스**를 선택하고, **모든 리소스**를 선택한 다음, 리소스 목록에서 **myResourceGroupLB** 리소스 그룹에 있는 **myTestVM**을 선택합니다.

4. **개요** 페이지에서 **연결**을 선택한 다음, **Bastion**을 선택합니다.

6. VM을 만드는 동안 입력한 사용자 이름과 암호를 입력합니다.

7. **myTestVM**에서 **Internet Explorer**를 엽니다.

8. 이전 단계의 IP 주소를 브라우저의 주소 표시줄에 입력합니다. IIS 웹 서버의 기본 페이지가 브라우저에 표시됩니다.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="표준 내부 부하 분산 장치 만들기" border="true":::
   
세 개의 VM 모두에서 부하 분산 장치 분산 트래픽을 확인하려면 각 VM의 IIS 웹 서버의 기본 페이지를 사용자 지정한 다음, 클라이언트 컴퓨터에서 웹 브라우저를 강제로 새로 고칠 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용하여 리소스 그룹, 부하 분산 장치 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 다음 작업을 수행했습니다.

* 표준 또는 공용 부하 분산 장치를 만들었습니다.
* 가상 머신을 연결했습니다. 
* 부하 분산 장치 트래픽 규칙 및 상태 프로브를 구성했습니다.
* 부하 분산 장치를 테스트했습니다.

Azure Load Balancer에 대해 자세히 알아보려면 [Azure Load Balancer란?](load-balancer-overview.md) 및 [Load Balancer 질문과 대답](load-balancer-faqs.md)으로 계속 진행하세요.

[Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md)에 대해 자세히 알아봅니다.
