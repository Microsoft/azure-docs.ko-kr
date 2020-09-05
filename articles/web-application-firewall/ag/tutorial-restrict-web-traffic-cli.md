---
title: Web Application Firewall 활성화 - Azure CLI
description: Azure CLI를 사용하여 애플리케이션 게이트웨이에서 Web Application Firewall로 웹 트래픽을 제한하는 방법을 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 08/31/2020
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: faa46b3ad96e9555a08a662f205a0a130c28bac2
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89225172"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Azure CLI를 사용하여 Web Application Firewall 활성화

애플리케이션 게이트웨이에서 [WAF(웹 애플리케이션 방화벽)](ag-overview.md)으로 트래픽을 제한할 수 있습니다. WAF는 [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 규칙을 사용하여 애플리케이션을 보호합니다. 이러한 규칙에는 SQL 삽입, 사이트 간 스크립팅 공격 및 세션 하이재킹과 같은 공격으로부터의 보호가 포함됩니다.

이 문서에서는 다음 방법을 설명합니다.

 * 네트워크 설정
 * WAF를 사용하는 애플리케이션 게이트웨이 만들기
 * 가상 머신 확장 집합 만들기
 * 스토리지 계정 만들기 및 진단 구성

![Web Application Firewall 예제](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

원하는 경우 [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md)을 사용하여 이 절차를 완료할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하기로 선택할 경우 이 문서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group#az-group-create)를 사용하여 *myResourceGroupAG*라는 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>네트워크 리소스 만들기

가상 네트워크 및 서브넷을 사용하여 애플리케이션 게이트웨이 및 연결된 리소스에 대한 네트워크 연결을 제공합니다. *myVNet*이라는 가상 네트워크와 *myAGSubnet*이라는 서브넷을 만듭니다. 그런 다음, *myAGPublicIPAddress*라는 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>WAF를 사용하여 애플리케이션 게이트웨이 만들기

[az network application-gateway create](/cli/azure/network/application-gateway)를 사용하여 *myAppGateway*라는 애플리케이션 게이트웨이를 만들 수 있습니다. Azure CLI를 사용하여 애플리케이션 게이트웨이를 만들 때 용량, sku, HTTP 설정 등의 구성 정보를 지정합니다. 애플리케이션 게이트웨이는 *myAGSubnet* 및 *myAGPublicIPAddress*에 할당됩니다.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

애플리케이션 게이트웨이가 생성될 때까지 몇 분 정도 걸릴 수 있습니다. 애플리케이션 게이트웨이가 생성되면 다음과 같은 새 기능을 볼 수 있습니다.

- *appGatewayBackendPool* - 애플리케이션 게이트웨이에 백 엔드 주소 풀이 하나 이상 있어야 합니다.
- *appGatewayBackendHttpSettings* - 포트 80 및 HTTP 프로토콜을 통신에 사용하도록 지정합니다.
- *appGatewayHttpListener* - *appGatewayBackendPool*에 연결되는 기본 수신기입니다.
- *appGatewayFrontendIP* - *myAGPublicIPAddress*를 *appGatewayHttpListener*에 할당합니다.
- *rule1* - *appGatewayHttpListener*에 연결되는 기본 라우팅 규칙입니다.

## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

이 예제에서는 애플리케이션 게이트웨이의 백 엔드 풀에 두 개의 서버를 제공하는 가상 머신 확장 집합을 만듭니다. 확장 집합의 가상 머신은 *myBackendSubnet* 서브넷과 연결됩니다. 확장 집합을 만들려면 [az vmss create](/cli/azure/vmss#az-vmss-create)를 사용합니다.

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>NGINX 설치

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>스토리지 계정 만들기 및 진단 구성

이 문서에서 애플리케이션 게이트웨이는 스토리지 계정을 사용하여 검색 및 방지 목적으로 데이터를 저장합니다. Azure Monitor 로그 또는 Event Hub를 사용하여 데이터를 기록할 수도 있습니다. 

### <a name="create-a-storage-account"></a>스토리지 계정 만들기

[az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 사용하여 *myagstore1*이라는 스토리지 계정을 만듭니다.

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>진단 구성

ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog 및 ApplicationGatewayFirewallLog 로그에 데이터를 기록하도록 진단을 구성합니다. `<subscriptionId>`를 구독 식별자로 바꾼 다음, [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create)를 사용하여 진단을 구성합니다.

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

애플리케이션 게이트웨이의 공용 IP 주소를 가져오려면 [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show)를 사용합니다. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![애플리케이션 게이트웨이의 기준 URL 테스트](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없는 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>다음 단계

[웹 애플리케이션 방화벽 규칙 사용자 지정](application-gateway-customize-waf-rules-portal.md)
