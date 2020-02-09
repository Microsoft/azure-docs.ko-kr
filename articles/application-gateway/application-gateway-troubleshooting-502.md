---
title: 잘못 된 게이트웨이 오류 문제 해결-Azure 애플리케이션 게이트웨이
description: 'Application Gateway 서버 오류 문제를 해결 하는 방법 알아보기: 502-웹 서버가 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못 된 응답을 받았습니다.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130478"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Application Gateway의 잘못된 게이트웨이 오류 문제 해결

Azure 애플리케이션 Gateway를 사용 하는 경우 잘못 된 게이트웨이 (502)의 문제를 해결 하는 방법에 대해 알아봅니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

응용 프로그램 게이트웨이를 구성한 후에 표시 될 수 있는 오류 중 하나는 "서버 오류: 502-웹 서버가 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못 된 응답을 받았습니다."입니다. 이 오류는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

* NSG, UDR 또는 사용자 지정 DNS는 백엔드 풀 구성원에 대한 액세스를 차단 합니다.
* 백 엔드 Vm 또는 가상 머신 확장 집합의 인스턴스가 기본 상태 프로브에 응답 하지 않습니다.
* 사용자 지정 상태 프로브의 구성이 잘못되었거나 부적절합니다.
* Azure 애플리케이션 게이트웨이의 [백 엔드 풀이 구성 되지 않았거나 비어](#empty-backendaddresspool)있습니다.
* 가상 머신 확장 집합의 VM 또는 인스턴스가 [모두 정상이 아닙니다](#unhealthy-instances-in-backendaddresspool).
* 사용자 요청과 관련된 [요청 시간 초과 또는 연결 문제입니다](#request-time-out).

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>네트워크 보안 그룹, 사용자 정의 경로 또는 사용자 지정 DNS 문제

### <a name="cause"></a>원인

NSG, UDR 또는 사용자 지정 DNS로 인해 백 엔드에 대한 액세스가 차단 된 경우 application gateway 인스턴스는 백 엔드 풀에 연결할 수 없습니다. 이로 인해 프로브 오류가 발생 하 여 502 오류가 발생 합니다.

응용 프로그램 Vm이 배포 되는 서브넷 또는 응용 프로그램 게이트웨이 서브넷에 NSG/UDR이 있을 수 있습니다.

마찬가지로 VNet에 사용자 지정 DNS가 있으면 문제가 발생할 수도 있습니다. 백 엔드 풀 구성원에 사용 되는 FQDN은 사용자가 VNet에 대해 구성 된 DNS 서버에 의해 올바르게 확인 되지 않을 수 있습니다.

### <a name="solution"></a>해결 방법

다음 단계를 통해 NSG, UDR 및 DNS 구성을 확인합니다.

* Application gateway 서브넷과 연결 된 NSGs를 확인 합니다. 백 엔드에 대한 통신이 차단 되지 않았는지 확인 합니다.
* Application gateway 서브넷과 연결 된 UDR를 확인 합니다. UDR이 백 엔드 서브넷에서 트래픽을 외부로 전달 하지 않는지 확인 합니다. 예를 들어 Express 경로/v m을 통해 응용 프로그램 게이트웨이 서브넷에 보급 되는 네트워크 가상 어플라이언스 또는 기본 경로에 대한 라우팅을 확인 합니다.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* 백 엔드 VM과의 유효한 NSG 및 경로를 확인합니다.

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* VNet에 사용자 지정 DNS의 존재 여부를 확인합니다. 출력에서 VNet 속성의 세부 정보를 확인하여 DNS를 확인할 수 있습니다.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
있는 경우 DNS 서버에서 백 엔드 풀 멤버의 FQDN을 올바르게 확인할 수 있는지 확인 합니다.

## <a name="problems-with-default-health-probe"></a>기본 상태 검색의 문제

### <a name="cause"></a>원인

502 오류는 기본 상태 프로브에서 백 엔드 Vm에 연결할 수 없다는 것을 자주 확인할 수도 있습니다.

응용 프로그램 게이트웨이 인스턴스가 프로 비전 되 면 BackendHttpSetting의 속성을 사용 하 여 각 BackendAddressPool 기본 상태 프로브를 자동으로 구성 합니다. 이 프로브를 설정하기 위해 사용자 입력이 필요하지 않습니다. 특히 부하 분산 규칙을 구성 하는 경우 BackendHttpSetting와 BackendAddressPool 간에 연결이 생성 됩니다. 이러한 각 연결에 대해 기본 프로브가 구성 되 고, application gateway는 BackendHttpSetting 요소에 지정 된 포트에서 BackendAddressPool의 각 인스턴스에 대한 주기적인 상태 검사 연결을 시작 합니다. 

다음 표에서는 기본 상태 프로브와 관련 된 값을 나열 합니다.

| 프로브 속성 | 값 | 설명 |
| --- | --- | --- |
| 프로브 URL |`http://127.0.0.1/` |URL 경로 |
| 간격 |30 |프로브 간격(초) |
| 시간 제한 |30 |프로브 시간 제한(초) |
| 비정상 임계값 |3 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

### <a name="solution"></a>해결 방법

* 기본 사이트를 구성하고 127.0.0.1에서 수신 대기 중인지를 확인합니다.
* BackendHttpSetting이 포트 80이 아닌 다른 포트를 지정하는 경우 기본 사이트는 해당 포트에서 수신하도록 구성되어야 합니다.
* `http://127.0.0.1:port` 호출은 HTTP 결과 코드 200을 반환해야 합니다. 이는 30 초 제한 시간 내에 반환 되어야 합니다.
* 구성 된 포트가 열려 있고 구성 된 포트에서 들어오거나 나가는 트래픽을 차단 하는 방화벽 규칙 또는 Azure 네트워크 보안 그룹이 없는지 확인 합니다.
* Azure 클래식 Vm 또는 클라우드 서비스를 FQDN 또는 공용 IP와 함께 사용 하는 경우 해당 [엔드포인트이](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) 열려 있는지 확인 합니다.
* VM이 Azure Resource Manager를 통해 구성 되 고 응용 프로그램 게이트웨이가 배포 된 VNet 외부에 있는 경우, 원하는 포트에서 액세스를 허용 하도록 [네트워크 보안 그룹](../virtual-network/security-overview.md) 을 구성 해야 합니다.

## <a name="problems-with-custom-health-probe"></a>사용자 지정 상태 검색의 문제

### <a name="cause"></a>원인

사용자 지정 상태 프로브를 사용하면 기본 검사 동작에 추가적인 유연성을 제공할 수 있습니다. 사용자 지정 프로브를 사용 하는 경우 프로브 간격, URL, 테스트할 경로, 백 엔드 풀 인스턴스를 비정상으로 표시 하기 전에 허용할 실패 응답 횟수를 구성할 수 있습니다.

다음과 같은 추가 속성이 추가 됩니다.

| 프로브 속성 | 설명 |
| --- | --- |
| 이름 |프로브 이름입니다. 이 이름은 백 엔드 HTTP 설정에서 프로브를 참조하는 데 사용됩니다. |
| 프로토콜 |프로브를 보내는 데 사용하는 프로토콜입니다. 프로브는 백 엔드 HTTP 설정에 정의된 프로토콜을 사용합니다. |
| Host |프로브에 보낼 호스트 이름입니다. 응용 프로그램 게이트웨이에서 다중 사이트를 구성한 경우에만 적용할 수 있습니다. VM 호스트 이름과 다릅니다. |
| 경로 |프로브의 상대 경로입니다. 올바른 경로는 '/'부터 시작합니다. 프로브는 \<protocol\>://\<host\>:\<port\>\<path\>로 전송됩니다. |
| 간격 |프로브 간격(초). 연속된 두 프로브 사이의 시간 간격입니다. |
| 시간 제한 |프로브 시간 제한(초) 이 시간 제한 기간 내에 올바른 응답을 받지 못하면 프로브는 실패로 표시 됩니다. |
| 비정상 임계값 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

### <a name="solution"></a>해결 방법

앞의 테이블처럼 사용자 지정 상태 프로브를 올바르게 구성했는지 유효성을 검사합니다. 앞의 문제 해결 단계 외에도 다음 사항을 확인합니다.

* 프로브가 [가이드](application-gateway-create-probe-ps.md)를 기준으로 올바르게 지정되어 있는지 확인합니다.
* 응용 프로그램 게이트웨이가 단일 사이트에 대해 구성 된 경우 사용자 지정 프로브에서 달리 구성 되지 않은 한 기본적으로 호스트 이름을 `127.0.0.1`로 지정 해야 합니다.
* http://\<host\>:\<port\>\<path\>에 대한 호출은 HTTP 결과 코드 200을 반환해야 합니다.
* 간격, 시간 제한 및 Unhealtythreshold이 허용 범위 내에 있는지 확인 하십시오.
* HTTPS 프로브를 사용하는 경우 백 엔드 서버 자체에서 대체(fallback) 인증서를 구성하여 백 엔드 서버에 SNI가 필요하지 않도록 합니다.

## <a name="request-time-out"></a>요청 시간 초과

### <a name="cause"></a>원인

사용자 요청이 수신 되 면 응용 프로그램 게이트웨이에서 요청에 구성 된 규칙을 적용 하 여 백 엔드 풀 인스턴스로 라우팅합니다. 백 엔드 인스턴스의 응답에 대해 구성 가능한 시간 간격을 기다립니다. 기본적으로이 간격은 **20** 초입니다. Application gateway가이 간격의 백 엔드 응용 프로그램에서 응답을 받지 못하면 사용자 요청에서 502 오류를 가져옵니다.

### <a name="solution"></a>해결 방법

Application Gateway를 사용 하 여 BackendHttpSetting를 통해이 설정을 구성할 수 있습니다. 그런 다음 다른 풀에 적용할 수 있습니다. 다른 백 엔드 풀에는 서로 다른 BackendHttpSetting 및 다른 요청 시간 제한이 구성 되어 있을 수 있습니다.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>비어 있는 BackendAddressPool

### <a name="cause"></a>원인

응용 프로그램 게이트웨이에 백 엔드 주소 풀에 구성 된 Vm 또는 가상 머신 확장 집합이 없는 경우 고객 요청을 라우팅할 수 없고 잘못 된 게이트웨이 오류를 보낼 수 없습니다.

### <a name="solution"></a>해결 방법

백 엔드 주소 풀이 비어 있지 않은지 확인 합니다. PowerShell, CLI 또는 포털을 통해 수행할 수 있습니다.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

앞의 cmdlet에서 출력은 비어 있지 않은 백 엔드 주소 풀을 포함해야 합니다. 다음 예제에서는 백 엔드 Vm에 대한 FQDN 또는 IP 주소를 사용 하 여 구성 된 두 개의 풀을 보여 줍니다. BackendAddressPool의 상태를 프로비전하는 작업은 '성공'해야 합니다.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>BackendAddressPool의 비정상 인스턴스

### <a name="cause"></a>원인

BackendAddressPool의 모든 인스턴스가 비정상 이면 응용 프로그램 게이트웨이에 사용자 요청을 라우팅할 백 엔드가 없습니다. 이는 백 엔드 인스턴스가 정상 이지만 필수 응용 프로그램을 배포 하지 않은 경우에도 가능 합니다.

### <a name="solution"></a>해결 방법

인스턴스가 정상이고 애플리케이션이 올바르게 구성되어 있는지 확인합니다. 백 엔드 인스턴스가 동일한 VNet의 다른 VM에서 ping에 응답할 수 있는지 확인 합니다. 공용 엔드포인트을 사용 하 여 구성 하는 경우 웹 응용 프로그램에 대한 브라우저 요청을 서비스할 수 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

위의 단계를 수행 해도 문제가 해결 되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.

