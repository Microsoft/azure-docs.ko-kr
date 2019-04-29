---
title: 가상 네트워크 TAP 만들기, 변경 또는 삭제 - Azure CLI | Microsoft Docs
description: Azure CLI를 사용하여 가상 네트워크 TAP를 만들거나 변경하거나 삭제하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 3d95a9ea555cceda82530eb5c487eeb993c1a678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743193"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Azure CLI를 사용하여 가상 네트워크 TAP 작업

Azure Virtual Network TAP(터미널 액세스 지점)를 사용하면 네트워크 패킷 수집기 또는 분석 도구로 가상 머신 네트워크 트래픽을 연속 스트리밍할 수 있습니다. 수집기 또는 분석 도구는 [네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/) 파트너를 통해 제공됩니다. 가상 네트워크 TAP 사용 가능 여부의 유효성이 검사된 파트너 솔루션 목록은 [파트너 솔루션](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)을 참조하세요. 

## <a name="create-a-virtual-network-tap-resource"></a>가상 네트워크 TAP 리소스 만들기

가상 네트워크 TAP 리소스를 만들기 전에 [필수 구성 요소](virtual-network-tap-overview.md#prerequisites)를 확인하세요. 아래에 설명되어 있는 명령은 [Azure Cloud Shell](https://shell.azure.com/bash)에서 실행할 수도 있고, 컴퓨터에서 Azure CLI(명령줄 인터페이스)를 실행하여 실행할 수도 있습니다. Azure Cloud Shell은 컴퓨터에 Azure CLI를 설치하지 않아도 사용할 수 있는 무료 대화형 셸입니다. 적절한 [권한](virtual-network-tap-overview.md#permissions)이 있는 계정으로 Azure에 로그인해야 합니다. 이 문서에서 설명하는 단계를 수행하려면 Azure CLI 버전 2.0.46 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. Virtual network 탭은 현재 확장으로 사용할 수 있습니다. 실행 해야 하는 확장을 설치 하려면 `az extension add -n virtual-network-tap`합니다. 또한 Azure CLI를 로컬로 실행하는 경우 `az login`를 실행하여 Azure와 연결해야 합니다.

1. 구독 ID를 이후 단계에서 사용되는 변수로 가져옵니다.

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. 가상 네트워크 TAP 리소스를 만드는 데 사용할 구독 ID를 설정합니다.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. 가상 네트워크 TAP 리소스를 만드는 데 사용할 구독 ID를 다시 등록합니다. TAP 리소스를 만들 때 등록 오류가 발생하면 다음 명령을 실행합니다.

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. 가상 네트워크 TAP의 대상이 수집기 또는 분석 도구용 네트워크 가상 어플라이언스의 네트워크 인터페이스인 경우 다음 작업을 수행합니다.

   - 네트워크 가상 어플라이언스의 네트워크 인터페이스 IP 구성을 이후 단계에서 사용되는 변수로 가져옵니다. 이 ID는 TAP 트래픽을 집계하는 엔드포인트입니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹의 *myNetworkInterface* 네트워크 인터페이스에 대한 *ipconfig1* IP 구성의 ID를 가져옵니다.

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - IP 구성의 ID를 대상으로 사용하고 선택적 port 속성을 포함하여 westcentralus Azure 지역에 가상 네트워크 TAP를 만듭니다. 이 포트는 TAP 트래픽을 수신할 네트워크 인터페이스 ID 구성의 대상 포트를 지정합니다.  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. 가상 네트워크 TAP의 대상이 Azure 내부 부하 분산 장치인 경우 다음 작업을 수행합니다.
  
   - Azure 내부 부하 분산 장치의 프런트 엔드 IP 구성을 이후 단계에서 사용되는 변수로 가져옵니다. 이 ID는 TAP 트래픽을 집계하는 엔드포인트입니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹의 *myInternalLoadBalancer* 부하 분산 장치에 대한 *frontendipconfig1* 프런트 엔드 IP 구성의 ID를 가져옵니다.

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - 프런트 엔드 IP 구성의 ID를 대상으로 사용하고 선택적 port 속성을 포함하여 가상 네트워크 TAP를 만듭니다. 이 포트는 TAP 트래픽을 수신할 프런트 엔드 IP 구성의 대상 포트를 지정합니다.  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. 가상 네트워크 TAP가 생성되었는지 확인합니다.

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>네트워크 인터페이스에 TAP 구성 추가

1. 기존 가상 네트워크 TAP 리소스의 ID를 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 가상 네트워크 TAP *myTap*을 가져옵니다.

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. 모니터링되는 가상 머신의 네트워크 인터페이스에서 TAP 구성을 만듭니다. 다음 예제에서는 *myNetworkInterface* 네트워크 인터페이스에 대한 TAP 구성을 만듭니다.

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. TAP 구성이 생성되었는지 확인합니다.

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>네트워크 인터페이스에서 TAP 구성 삭제

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>구독의 가상 네트워크 TAP 나열

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>리소스 그룹의 가상 네트워크 TAP 삭제

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
