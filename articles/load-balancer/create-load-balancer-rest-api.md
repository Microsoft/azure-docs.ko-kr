---
title: REST API를 사용하여 Azure Load Balancer 만들기
titlesuffix: Azure Load Balancer
description: REST API를 사용하여 Azure Load Balancer를 만드는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: load-balancer
ms.date: 06/06/2018
ms.author: kumud
ms.openlocfilehash: 159fe9d6a891858d8d2cc2315e9544b79eb44cff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884982"
---
# <a name="create-an-azure-basic-load-balancer-using-rest-api"></a>REST API를 사용하여 Azure 기본 Load Balancer 만들기

Azure Load Balancer는 규칙 및 상태 프로브에 따라, 부하 분산 장치의 프런트 엔드에 도착하는 새 인바운드 흐름을 백 엔드 풀 인스턴스에 분산합니다. Azure Load Balancer는 기본 및 표준이라는 두 SKU에서 사용할 수 있습니다. 두 가지 SKU 버전 간의 차이를 알아보려면 [Azure Load Balancer SKU 비교](load-balancer-overview.md#skus)를 참조하세요.
 
이 방법은 [Azure REST API](/rest/api/azure/)를 사용하여 Azure 기본 Load Balancer를 만드는 방법을 보여 주며, 이 방법을 통해 Azure 가상 네트워크 내의 여러 VM에서 들어오는 요청을 부하 분산할 수 있습니다. 참조 설명서 전문과 추가 샘플은 [Azure Load Balancer REST 참조](/rest/api/load-balancer/)에서 제공됩니다.
 
## <a name="build-the-request"></a>요청 빌드
다음 HTTP PUT 요청을 사용하여 새 Azure 기본 Load Balancer를 만듭니다.
 ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}?api-version=2018-02-01
  ```
### <a name="uri-parameters"></a>URI 매개 변수

|이름  |그런 다음  |필수 |형식 |설명 |
|---------|---------|---------|---------|--------|
|subscriptionId   |  경로       |  True        |   문자열      |  Microsoft Azure 구독을 고유하게 식별하는 구독 자격 증명입니다. 구독 ID는 모든 서비스 호출에 대한 URI의 파트를 형성합니다.      |
|resourceGroupName     |     경로    | True         |  문자열       |   리소스 그룹의 이름.     |
|loadBalancerName     |  경로       |      True    |    문자열     |    부하 분산 장치의 이름입니다.    |
|api-version    |   쿼리     |  True        |     문자열    |  클라이언트 API 버전입니다.      |



### <a name="request-body"></a>요청 본문

필수 매개변수는 `location`뿐입니다. *SKU* 버전을 정의하지 않는 경우에는 기본적으로 기본 Load Balancer가 만들어집니다.  부하 분산 장치를 사용자 지정하려면 [선택적 매개 변수](https://docs.microsoft.com/rest/api/load-balancer/loadbalancers/createorupdate#request-body)를 사용합니다.

| 이름 | 형식 | 설명 |
| :--- | :--- | :---------- |
| location | 문자열 | 리소스 위치. [위치 나열](https://docs.microsoft.com/rest/api/resources/subscriptions/listlocations) 작업을 사용하여 현재 위치 목록을 가져옵니다. |


## <a name="example-create-and-update-a-basic-load-balancer"></a>예제: 기본 Load Balancer 만들기 및 업데이트

이 예제에서는 먼저 기본 Load Balancer를 해당 리소스와 함께 만듭니다. 그런 다음, 프론트 엔드 IP 구성, 백 엔드 주소 풀, 부하 분산 규칙, 상태 프로브 및 인바운드 NAT 규칙을 포함하는 부하 분산 장치 리소스를 구성합니다.

아래 예제를 사용하여 부하 분산 장치를 만들기 전에 **East US** 위치의 *rg1*이라는 리소스 그룹에 *subnetlb* 서브넷과 함께 *vnetlb* 가상 네트워크를 만드세요.

### <a name="step-1-create-a-basic-load-balancer"></a>1단계. 기본 부하 분산 장치 만들기
이 단계에서는 *rg1* 리소스 그룹 내의 **EAST US** 위치에서 *lb*라는 기본 Load Balancer를 만듭니다.
#### <a name="sample-request"></a>샘플 요청

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>요청 본문

  ```JSON
   {
    "location": "eastus",
   }
  ```
### <a name="step-2-configure-load-balancer-resources"></a>2단계. 부하 분산 장치 리소스 구성
이 단계에서는 프런트 엔드 IP 구성(*fe-lb*), 백 엔드 주소 풀(*be-lb*), 부하 분산 규칙(*rulelb*), 상태 프로브(*probe-lb*) 및 인바운드 NAT 규칙(*in-nat-rule*)을 포함하는 부하 분산 장치 *lb* 리소스를 구성합니다.
#### <a name="sample-request"></a>샘플 요청

  ```HTTP    
  PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb?api-version=2018-02-01
  ```
#### <a name="request-body"></a>요청 본문

  ```JSON
{
  "properties": {
    "frontendIPConfigurations": [
      {
        "name": "fe-lb",
        "properties": {
          "subnet": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/vnetlb/subnets/subnetlb"
          },
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ],
          "inboundNatRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/inboundNatRules/in-nat-rule"
            }  ]  }  }  ],
    "backendAddressPools": [
      {
        "name": "be-lb",
        "properties": {
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]   }   }   ],
    "loadBalancingRules": [
      {
        "name": "rulelb",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 80,
          "backendPort": 80,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp",
          "loadDistribution": "Default",
          "backendAddressPool": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/be-lb"
          },
          "probe": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/probes/probe-lb"
          }  }  }  ],
    "probes": [
      {
        "name": "probe-lb",
        "properties": {
          "protocol": "Http",
          "port": 80,
          "requestPath": "healthcheck.aspx",
          "intervalInSeconds": 15,
          "numberOfProbes": 2,
          "loadBalancingRules": [
            {
              "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/loadBalancingRules/rulelb"
            }  ]  }  } ],
    "inboundNatRules": [
      {
        "name": "in-nat-rule",
        "properties": {
          "frontendIPConfiguration": {
            "id": "/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/lb/frontendIPConfigurations/fe-lb"
          },
          "frontendPort": 3389,
          "backendPort": 3389,
          "enableFloatingIP": true,
          "idleTimeoutInMinutes": 15,
          "protocol": "Tcp"
        } } ],
    "inboundNatPools": [],
    "outboundNatRules": []
  }  }
```
