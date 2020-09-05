---
title: AKS(Azure Kubernetes Service)에서 UDR(사용자 정의 경로) 사용자 지정
description: AKS(Azure Kubernetes Service)에서 사용자 지정 송신 경로를 정의하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: 5095931e28438beebf3250155ede1a8af0bb5c64
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796972"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>사용자 정의 경로를 사용 하 여 클러스터 송신 사용자 지정

AKS 클러스터에서 송신은 특정 시나리오에 맞게 사용자 지정할 수 있습니다. 기본적으로 AKS는 설정 하 고 송신에 사용할 표준 SKU Load Balancer를 프로 비전 합니다. 그러나 공용 IP가 허용되지 않거나 송신에 대한 추가 홉이 필요한 경우에는 기본 설정이 모든 시나리오의 요구 사항을 충족하지 못할 수 있습니다.

이 문서에서는 공용 IP를 허용하지 않고 클러스터가 NVA(네트워크 가상 어플라이언스) 뒤에 있어야 하는 경우와 같은 사용자 지정 네트워크 시나리오를 지원하기 위해 클러스터의 송신 경로를 사용자 지정하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항
* Azure CLI 버전 2.0.81 이상
* API 버전 `2020-01-01` 이상


## <a name="limitations"></a>제한 사항
* OutboundType은 클러스터를 만들 때에만 정의할 수 있으며 나중에 업데이트할 수 없습니다.
* `outboundType`을 설정하려면 `VirtualMachineScaleSets`가 `vm-set-type` 및 `Standard`의 `load-balancer-sku`를 사용하는 AKS 클러스터가 필요합니다.
* `outboundType`을 `UDR` 값으로 설정하려면 클러스터에 대한 유효한 아웃바운드 연결을 사용하는 사용자 정의 경로가 필요합니다.
* `outboundType`을 `UDR` 값으로 설정하면 부하 분산 장치로 라우팅되는 수신 원본 IP가 클러스터의 나가는 송신 대상 주소와 **일치하지 않을** 수 있습니다.

## <a name="overview-of-outbound-types-in-aks"></a>AKS의 아웃바운드 형식 개요

AKS 클러스터는 또는 형식의 고유한를 사용 하 여 사용자 지정할 수 있습니다 `outboundType` `loadBalancer` `userDefinedRouting` .

> [!IMPORTANT]
> 아웃바운드 형식은 클러스터의 송신 트래픽에만 영향을 줍니다. 자세한 내용은 [수신 컨트롤러 설정](ingress-basic.md)을 참조 하세요.

> [!NOTE]
> UDR 및 kubenet 네트워킹에서 고유한 [경로 테이블][byo-route-table] 을 사용할 수 있습니다. 클러스터 id (서비스 사용자 또는 관리 id)에 게 사용자 지정 경로 테이블에 대 한 참가자 권한이 있는지 확인 합니다.

### <a name="outbound-type-of-loadbalancer"></a>loadBalancer의 아웃바운드 형식

`loadBalancer`가 설정 된 경우 AKS는 다음 구성을 자동으로 완료 합니다. 부하 분산 장치는 AKS 할당 공용 IP를 통해 송신하는 데 사용됩니다. 아웃바운드 형식의 `loadBalancer`는 AKS 리소스 공급자가 만든 부하 분산 장치에서 송신하는 것으로 간주되는 `loadBalancer` 형식의 Kubernetes 서비스를 지원합니다.

AKS에 의해 수행 되는 구성은 다음과 같습니다.
   * 공용 IP 주소가 클러스터 송신을 위해 프로비저닝됩니다.
   * 공용 IP 주소가 부하 분산 장치 리소스에 할당됩니다.
   * 부하 분산 장치의 백 엔드 풀은 클러스터의 에이전트 노드에 대해 설정 됩니다.

다음은 기본적으로 AKS 클러스터에 배포된 네트워크 토폴로지이며 `loadBalancer`의 `outboundType`을 사용합니다.

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>userDefinedRouting의 아웃바운드 형식

> [!NOTE]
> 아웃바운드 형식 사용은 고급 네트워킹 시나리오이며 적절한 네트워크 구성이 필요합니다.

`userDefinedRouting`가 설정 된 경우 AKS는 자동으로 송신 경로를 구성 하지 않습니다. 송신 설정은 사용자가 수행 해야 합니다.

SLB (표준 부하 분산 장치) 아키텍처를 사용 하지 않을 경우에는 명시적 송신을 설정 해야 하기 때문에 이전에 구성 된 서브넷을 사용 하 여 AKS 클러스터를 기존 가상 네트워크에 배포 해야 합니다. 따라서이 아키텍처를 사용 하려면 방화벽, 게이트웨이, 프록시 등의 어플라이언스로 송신 트래픽을 명시적으로 전송 하거나 표준 부하 분산 장치 또는 어플라이언스에 할당 된 공용 IP에서 NAT (Network Address Translation)를 수행할 수 있도록 해야 합니다.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>UserDefinedRouting를 사용 하 여 부하 분산 장치 만들기

아웃 바운드 형식이 UDR 인 AKS 클러스터는 ' loadBalancer ' 형식의 첫 번째 Kubernetes 서비스가 배포 될 때만 SLB (표준 부하 분산 장치)를 수신 합니다. 부하 분산 장치는 *인바운드 요청에 대 한* 공용 IP 주소 및 *인바운드* 요청에 대 한 백 엔드 풀을 사용 하 여 구성 됩니다. 인바운드 규칙은 Azure 클라우드 공급자가 구성 하지만 아웃 바운드 **공용 IP 주소 또는** 아웃 바운드 규칙은 udr의 아웃 바운드 유형의 결과로 구성 되지 않습니다. UDR은 여전히 송신 트래픽의 유일한 원본입니다.

Azure 부하 분산 장치는 [규칙이 배치 될 때까지 요금이 부과 되지 않습니다](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>아웃바운드 형식의 UDR 및 Azure Firewall을 사용하여 클러스터 배포

사용자 정의 경로를 사용 하 여 아웃 바운드 유형의 클러스터 응용 프로그램을 설명 하기 위해 자체 서브넷의 Azure 방화벽을 사용 하 여 가상 네트워크에서 클러스터를 구성할 수 있습니다. [Azure 방화벽을 사용 하 여 송신 트래픽 제한 예제](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall)에서이 예제를 참조 하세요.

> [!IMPORTANT]
> UDR의 아웃 바운드 형식에는 0.0.0.0/0의 경로와 NVA (네트워크 가상 어플라이언스)의 다음 홉 대상이 경로 테이블에 있어야 합니다.
> 경로 테이블에는 인터넷에 대 한 기본 0.0.0.0/0이 이미 있지만,이 경로를 추가 하는 것 처럼 SNAT에 대 한 공용 IP가 없으면 송신을 제공 하지 않습니다. AKS는 인터넷을 가리키는 0.0.0.0/0 경로를 만들지 않고 NVA 또는 게이트웨이 등으로 만들지를 확인 합니다. 아웃 바운드 형식 UDR을 사용 하는 경우 *loadbalancer* 형식의 서비스가 구성 되어 있지 않으면 **인바운드 요청** 에 대 한 부하 분산 장치 공용 IP 주소가 생성 되지 않습니다. 아웃 바운드 형식의 아웃 바운드 유형이 설정 된 경우 **아웃 바운드 요청** 에 대 한 공용 IP 주소는 AKS에서 생성 되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure 네트워킹 UDR 개요](../virtual-network/virtual-networks-udr-overview.md)를 참조하세요.

[경로 테이블을 만들거나 변경하거나 삭제하는 방법](../virtual-network/manage-route-table.md)을 참조하세요.

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
