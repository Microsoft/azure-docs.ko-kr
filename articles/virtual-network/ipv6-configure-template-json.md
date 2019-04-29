---
title: Azure virtual network-Resource Manger 템플릿 (미리 보기)에 IPv6 이중 스택 응용 프로그램 배포
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure Resource Manager VM 템플릿을 사용 하 여 Azure 가상 네트워크 IPv6 이중 스택 응용 프로그램을 배포 하는 방법입니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: ae90bc4a12763803f38224d917c4644a68ae7d6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62131030"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure---template-preview"></a>Azure 템플릿 (미리 보기)에서 IPv6 이중 스택 응용 프로그램 배포

이 문서에 적용 되는 Azure Resource Manager VM 템플릿의 일부를 사용 하 여 IPv6 구성 작업의 목록을 제공 합니다. 이 문서에서 설명한 템플릿을 사용 하 여 이중 스택 가상 네트워크 IPv4 및 IPv6 서브넷을 사용 하 여 이중 (i p v 4 + IPv6) 프런트 엔드 구성, 이중 IP가 있는 Nic 사용 하 여 Vm 사용 하 여 부하 분산 장치를 포함 하는 Azure에서 이중 스택 (i p v 4 + IPv6) 응용 프로그램 배포 구성, 네트워크 보안 그룹 및 공용 Ip입니다. 

## <a name="required-configurations"></a>필수 구성

참조 발생 해야 하는 템플릿에 템플릿 섹션을 검색 합니다.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>가상 네트워크에 대 한 IPv6 addressSpace

추가할 템플릿 섹션:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>IPv6 가상 네트워크 addressSpace 내에 IPv6 서브넷

추가할 템플릿 섹션:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>NIC에 대 한 IPv6 구성

추가할 템플릿 섹션:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>IPv6 네트워크 보안 그룹 (NSG) 규칙

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "ace:cab:deca:deed::/64",
              "destinationAddressPrefix": "cab:ace:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>조건부 구성

네트워크 가상 어플라이언스를 사용 하는 경우 IPv6 경로 경로 테이블에 추가 합니다. 그렇지 않은 경우이 구성은 선택 사항입니다.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "ace:cab:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "deca:cab:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>옵션 구성

### <a name="ipv6-internet-access-for-the-virtual-network"></a>가상 네트워크에 대 한 IPv6 인터넷 액세스

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>IPv6 공용 IP 주소

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Load Balancer의 IPv6 프런트 엔드

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>IPv6 부하 분산 장치 백 엔드 주소 풀

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>IPv6 부하 분산 장치 규칙 수신 및 송신 포트를 연결 하려면

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>VM 템플릿 JSON 샘플
클릭 [여기](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/) Azure Resource Manager 템플릿을 사용 하 여 Azure 가상 네트워크에서 IPv6 이중 스택 응용 프로그램을 배포 합니다.

## <a name="next-steps"></a>다음 단계

가격에 대 한 세부 정보를 찾을 수 있습니다 [공용 IP 주소](https://azure.microsoft.com/pricing/details/ip-addresses/)를 [네트워크 대역폭](https://azure.microsoft.com/pricing/details/bandwidth/), 또는 [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/)합니다.
