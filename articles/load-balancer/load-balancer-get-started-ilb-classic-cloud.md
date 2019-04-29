---
title: Azure Cloud Services에 대한 내부 Load Balancer 만들기 - 클래식 배포
titlesuffix: Azure Load Balancer
description: 클래식 배포 모델에서 PowerShell을 사용하여 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 361322ded68f7c8305c4f976847d4bcb82f7e595
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626165"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>클라우드 서비스를 위한 내부 부하 분산 장치(클래식) 만들기 시작

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloud services](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한  [Resource Manager 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 모델을 사용하여 이러한 단계를 수행하는](load-balancer-get-started-ilb-arm-ps.md) 방법을 알아봅니다.

## <a name="configure-internal-load-balancer-for-cloud-services"></a>클라우드 서비스에 대한 내부 부하 분산 장치 구성하기

내부 부하 분산 장치는 가상 머신 및 클라우드 서비스를 모두 지원합니다. 지역 가상 네트워크 외부에 있는 클라우드 서비스에 생성된 내부 부하 분산 장치 엔드포인트는 해당 클라우드 서비스 내에서만 액세스할 수 있습니다.

아래 샘플에 나와 있는 것처럼 클라우드 서비스에서 첫 번째 배포를 만드는 동안 내부 부하 분산 장치 구성을 설정해야 합니다.

> [!IMPORTANT]
> 아래 단계를 실행하려면 클라우드 배포를 위해 가상 네트워크를 반드시 미리 만들어 두어야 합니다. 내부 부하 분산을 만들려면 가상 네트워크 이름 및 서브넷 이름이 필요합니다.

### <a name="step-1"></a>1단계

Visual Studio에서 클라우드 배포용 서비스 구성 파일(.cscfg)을 열고 다음 섹션을 추가하여 네트워크 구성을 위해 마지막 "`</Role>`" 항목 아래에 내부 부하 분산을 만듭니다.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="name of the load balancer">
        <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

어떻게 표시되는지 나타내기 위해 네트워크 구성 파일에 대한 값을 추가하겠습니다. 예제에서는 test_subnet이라는 10.0.0.0/24 서브넷과 10.0.0.4 고정 IP를 사용하는 "test_vnet"이라는 VNet을 만들었다고 가정합니다. 부하 분산 장치의 이름은 testLB입니다.

```xml
<NetworkConfiguration>
    <LoadBalancers>
    <LoadBalancer name="testLB">
        <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
    </LoadBalancer>
    </LoadBalancers>
</NetworkConfiguration>
```

부하 분산 장치 스키마에 대한 자세한 내용은 [부하 분산 장치 추가](https://msdn.microsoft.com/library/azure/dn722411.aspx)를 참조하세요.

### <a name="step-2"></a>2단계

내부 부하 분산에 엔드포인트를 추가하려면 서비스 정의(.csdef) 파일을 변경합니다. 역할 인스턴스가 만들어지는 순간 서비스 정의 파일이 내부 부하 분산에 역할 인스턴스를 추가합니다.

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
    </Endpoints>
</WorkerRole>
```

위의 예제에서 나온 동일한 값을 따라 서비스 정의 파일에 값을 추가하겠습니다.

```xml
<WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
    </Endpoints>
</WorkerRole>
```

들어오는 요청의 경우 포트 80을 사용하는 testLB 부하 분산 장치를 통해 네트워크 트래픽 부하가 분산되며 포트 80의 작업자 역할 인스턴스에도 보냅니다.

## <a name="next-steps"></a>다음 단계

[원본 IP 선호도를 사용하여 부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

