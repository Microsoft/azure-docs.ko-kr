---
title: Azure Batch 풀에서 노드 엔드포인트 구성 | Microsoft Docs
description: Azure Batch 풀의 계산 노드에서 SSH 또는 RDP 포트에 대한 액세스를 구성하거나 비활성화하는 방법입니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: lahugh
ms.openlocfilehash: a6c2c343b13b77048c772cb1e5c2ba06cf8add50
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616860"
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Azure Batch 풀의 계산 노드에 대한 원격 액세스 구성 또는 비활성화

기본적으로 Batch는 [노드 사용자](/rest/api/batchservice/computenode/adduser)가 네트워크 연결을 사용하여 외부에서 Batch 풀의 계산 노드에 연결할 수 있게 해줍니다. 예를 들어 사용자는 포트 3389의 RDP(원격 데스크톱)를 통해 Windows 풀의 계산 노드에 연결할 수 있습니다. 마찬가지로, 기본적으로 사용자는 포트 22의 SSH(Secure Shell)를 통해 Linux 풀의 계산 노드에 연결할 수 있습니다. 

사용자 환경에서 이러한 기본 외부 액세스 설정을 제한하거나 비활성화해야 할 수 있습니다. Batch API로 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) 속성을 설정하여 이러한 설정을 수정할 수 있습니다. 

## <a name="about-the-pool-endpoint-configuration"></a>풀 엔드포인트 구성 정보
이 엔드포인트 구성은 하나 이상의 프런트 엔드 포트 [NAT(Network Address Translation) 풀](/rest/api/batchservice/pool/add#inboundnatpool)로 이루어집니다. (NAT 풀과 계산 노드의 Batch 풀을 혼동하지 마세요.) 각 NAT 풀을 설정하여 풀의 계산 노드에 대한 기본 연결 설정을 재정의합니다. 

각 NAT 풀 구성에는 하나 이상의 [NSG(네트워크 보안 그룹) 규칙](/rest/api/batchservice/pool/add#networksecuritygrouprule)이 포함되어 있습니다. 각 NSG 규칙은 엔드포인트에 대한 특정 네트워크 트래픽을 허용하거나 거부합니다. 모든 트래픽, [서비스 태그](../virtual-network/security-overview.md#service-tags)(예: "인터넷")로 식별된 트래픽 또는 특정 IP 주소나 서브넷에서 들어오는 트래픽을 허용하거나 거부하도록 선택할 수 있습니다.

### <a name="considerations"></a>고려 사항
* 풀 엔드포인트 구성은 풀 [네트워크 구성](/rest/api/batchservice/pool/add#NetworkConfiguration)의 일부입니다. 네트워크 구성은 풀은 [Azure 가상 네트워크](batch-virtual-network.md)에 조인하는 설정을 선택적으로 포함할 수 있습니다. 가상 네트워크에서 풀을 설정할 경우 가상 네트워크의 주소 설정을 사용하는 NSG 규칙을 만들 수 있습니다.
* NAT 풀을 구성할 경우 여러 NSG 규칙을 구성할 수 있습니다. 규칙은 우선 순위에 따라 검사됩니다. 규칙이 적용되면 일치하는 규칙은 더 이상 테스트되지 않습니다.


## <a name="example-deny-all-rdp-traffic"></a>예제: 모든 RDP 트래픽 거부

다음 C# 코드 조각에서는 모든 네트워크 트래픽을 거부하도록 Windows 풀의 계산 노드에 있는 RDP 엔드포인트를 구성하는 방법을 보여줍니다. 이 엔드포인트는 *60000 - 60099* 범위의 프런트 엔드 포트 풀을 사용합니다. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>예제: 인터넷에서 오는 모든 SSH 트래픽 거부

다음 Python 코드 조각에서는 모든 인터넷 트래픽을 거부하도록 Linux 풀의 계산 노드에 있는 SSH 엔드포인트를 구성하는 방법을 보여줍니다. 이 엔드포인트는 *4000 - 4100* 범위의 프런트 엔드 포트 풀을 사용합니다. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>예제: 특정 IP 주소의 RDP 트래픽 허용

다음 C# 코드 조각에서는 *198.51.100.7*이라는 IP 주소에서 오는 RDP 액세스만 허용하도록 Windows 풀의 계산 노드에 있는 RDP 엔드포인트를 구성하는 방법을 보여줍니다. 두 번째 NSG 규칙은 IP 주소와 일치하지 않는 트래픽을 거부합니다.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>예제: 특정 서브넷의 SSH 트래픽 허용

다음 Python 코드 조각에서는 *192.168.1.0/24*라는 서브넷에서 오는 액세스만 허용하도록 Linux 풀의 계산 노드에 있는 SSH 엔드포인트를 구성하는 방법을 보여줍니다. 두 번째 NSG 규칙은 서브넷과 일치하지 않는 트래픽을 거부합니다.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>다음 단계

- Azure의 NSG 규칙에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/security-overview.md)을 참조하세요.

- Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 계산 솔루션 개발](batch-api-basics.md)을 참조하세요.

