---
title: 클라우드 서비스당 여러 VIP
titlesuffix: Azure Load Balancer
description: MultiVIP 및 클라우드 서비스에서 여러 VIP를 설정하는 방법에 대한 개요입니다.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bf5721e206316a4ce576253743e9ac65de47094a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591739"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>클라우드 서비스당 여러 VIP 구성

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure에서 제공하는 IP 주소를 사용하여 공용 인터넷을 통해 Azure 클라우드 서비스에 액세스할 수 있습니다. 이 공용 IP 주소는 Azure Load Balancer에 연결되며 클라우드 서비스 내의 VM(Virtual Machine) 인스턴스가 아니기 때문에 VIP(가상 IP)라고 합니다. 단일 VIP를 사용하여 클라우드 서비스 내의 모든 VM 인스턴스에 액세스할 수 있습니다.

그러나 동일한 클라우드 서비스에 대한 진입점으로 둘 이상의 VIP가 필요할 수 있는 시나리오도 있습니다. 예를 들어 각 사이트가 서로 다른 고객 또는 테넌트에 대해 호스트되므로 클라우드 서비스에서 기본 포트 443을 사용하여 SSL에 연결해야 하는 여러 웹 사이트를 호스트할 수 있습니다. 이 시나리오에서는 각 웹 사이트에 대해 다른 공용 연결 IP 주소를 사용해야 합니다. 아래 다이어그램은 동일한 공용 포트에 여러 SSL 인증서가 필요한 일반적인 다중 테넌트 웹 호스팅을 보여 줍니다.

![다중 VIP SSL 시나리오](./media/load-balancer-multivip/Figure1.png)

위 예제에서 모든 VIP는 동일한 공용 포트(443)를 사용하며, 모든 웹 사이트를 호스트하는 클라우드 서비스의 내부 IP 주소에 대한 고유한 개인 포트에서 하나 이상의 부하 분산 VM으로 트래픽이 리디렉션됩니다.

> [!NOTE]
> 여러 VIP를 사용해야 하는 또 다른 상황은 동일한 Virtual Machines 집합에서 여러 개의 SQL AlwaysOn 가용성 그룹 수신기를 호스트하는 경우입니다.

VIP는 기본적으로 동적이므로 클라우드 서비스에 할당된 실제 IP 주소가 시간에 따라 변경될 수 있습니다. 이러한 문제가 발생하지 않도록 서비스에 대해 VIP를 예약할 수 있습니다. 예약된 VIP에 대한 자세한 내용은 [예약된 공용 IP](../virtual-network/virtual-networks-reserved-public-ip.md)를 참조하세요.

> [!NOTE]
> VIP 및 예약된 IP의 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses/) 을 참조하세요.

PowerShell을 통해 클라우드 서비스에서 사용하는 VIP를 확인하고, VIP를 추가 및 제거하고, VIP를 엔드포인트에 연결하고, 특정 VIP에 부하 분산을 구성할 수 있습니다.

## <a name="limitations"></a>제한 사항

이때 다중 VIP 기능은 다음과 시나리오로 제한됩니다.

* **IaaS만**. VM을 포함하는 클라우드 서비스에 대해서 다중 VIP만 사용할 수 있습니다. PaaS 시나리오에서 역할 인스턴스와 함께 다중 VIP를 사용할 수는 없습니다.
* **PowerShell만**. PowerShell을 사용하여 다중 VIP만 관리할 수 있습니다.

이러한 제한은 일시적이며 언제든지 변경될 수 있습니다. 향후 변경 내용을 확인하려면 이 페이지를 다시 방문해야 합니다.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>클라우드 서비스에 VIP를 추가하는 방법
서비스를 VIP를 추가하려면 다음 PowerShell 명령을 실행합니다.

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

이 명령은 다음 예제와 유사한 결과를 표시합니다.

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>클라우드 서비스에서 VIP를 제거하는 방법
위 예제에서 서비스에 추가된 VIP를 제거하려면 다음 PowerShell 명령을 실행합니다.

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> 연결된 엔드포인트가 없는 경우에만 VIP를 제거할 수 있습니다.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>클라우드 서비스에서 VIP 정보를 검색하는 방법
클라우드 서비스와 연결된 VIP를 검색하려면 다음 PowerShell 스크립트를 실행합니다.

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

이 스크립트는 다음 예제와 유사한 결과를 표시합니다.

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

이 예제에서는 클라우드 서비스에 다음 3개의 VIP가 있습니다.

* **Vip1** 은 기본 VIP로, IsDnsProgrammedName의 값이 true로 설정되었기 때문에 식별됩니다.
* **Vip2** 및 **Vip3**은 IP 주소가 없으므로 사용되지 않습니다. 엔드포인트를 VIP에 연결하는 경우에만 사용됩니다.

> [!NOTE]
> 엔드포인트와 연결된 후에는 추가 VIP에 대한 요금이 구독에 부과됩니다. 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses/)을 참조하세요.

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>VIP를 엔드포인트에 연결하는 방법

클라우드 서비스의 VIP를 엔드포인트에 연결하려면 다음 PowerShell 명령을 실행합니다.

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

이 명령은 포트 *80*에서 *Vip2*라는 VIP에 연결된 엔드포인트를 만들고, 포트 *8080*에서 *TCP*를 사용하여 *myService*라는 클라우드 서비스에 있는 *myVM1*이라는 VM에 연결합니다.

구성을 확인하려면 다음 PowerShell 명령을 실행합니다.

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

출력은 다음 예제와 유사합니다.

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>특정 VIP에서 부하 분산을 사용하도록 설정하는 방법

부하 분산을 위해 단일 VIP를 여러 가상 머신에 연결할 수 있습니다. 예를 들어 *myService*라는 클라우드 서비스와 *myVM1* 및 *myVM2*라는 두 개의 가상 머신이 있다고 가정합니다. 클라우드 서비스에는 여러 VIP가 있고, 그중의 하나가 *Vip2*입니다. *Vip2*의 포트 *81*로 전송된 모든 트래픽을 포트 *8181*에서 *myVM1* 및 *myVM2* 간에 부하 분산하려는 경우 다음 PowerShell 스크립트를 실행합니다.

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

다른 VIP를 사용하도록 부하 분산 장치를 업데이트할 수도 있습니다. 예를 들어 아래 PowerShell 명령을 실행하면 부하 분산 집합이 Vip1이라는 VIP를 사용하도록 변경됩니다.

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>다음 단계

[Azure 부하 분산에 대 한 azure Monitor 로그](load-balancer-monitor-log.md)

[인터넷 연결 부하 분산 장치 개요](load-balancer-internet-overview.md)

[인터넷 연결 부하 분산 장치 시작](load-balancer-get-started-internet-arm-ps.md)

[Virtual Network 개요](../virtual-network/virtual-networks-overview.md)

[예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)
