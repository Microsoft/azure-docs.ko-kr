---
title: 지정 된 공용 IP 주소를 사용 하 여 풀 만들기
description: 사용자 고유의 공용 IP 주소를 사용 하는 Batch 풀을 만드는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 07/20/2020
ms.openlocfilehash: 158facaf1fd5052c3626f065a69bfbd134ca4c3e
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146490"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>지정 된 공용 IP 주소를 사용 하 여 Azure Batch 풀 만들기

Azure Batch 풀을 만들 때 지정한 Azure VNet ( [가상 네트워크)의 서브넷에 풀을 프로 비전](batch-virtual-network.md) 할 수 있습니다. Batch 풀의 가상 머신은 Batch로 생성 된 공용 IP 주소를 통해 액세스 됩니다. 이러한 공용 IP 주소는 풀의 수명 동안 변경 될 수 있습니다. 즉, IP 주소를 새로 고치지 않으면 네트워크 설정이 오래 된 상태가 될 수 있습니다.

풀의 가상 컴퓨터에 사용할 고정 공용 IP 주소 목록을 만들 수 있습니다. 이렇게 하면 공용 IP 주소 목록을 제어 하 고 예기치 않게 변경 되지 않도록 할 수 있습니다. 이는 특정 IP 주소에 대 한 액세스를 제한 하는 데이터베이스와 같은 외부 서비스를 사용 하 여 작업 하는 경우 특히 유용할 수 있습니다.

공용 IP 주소 없이 풀을 만드는 방법에 대 한 자세한 내용은 [공용 ip 주소 없이 Azure Batch 풀 만들기](./batch-pool-no-public-ip-address.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **인증**. 공용 IP 주소를 사용 하려면 Batch 클라이언트 API에서 [AD (Azure Active Directory) 인증](batch-aad-auth.md)을 사용 해야 합니다.

- **Azure VNet**. 풀 및 IP 주소를 만드는 동일한 Azure 구독에서 [가상 네트워크](batch-virtual-network.md) 를 사용 해야 합니다. Azure Resource Manager 기반 Vnet만 사용할 수 있습니다. VNet이 모든 [일반 요구 사항을](batch-virtual-network.md#vnet-requirements)충족 하는지 확인 합니다.

- **하나 이상의 Azure 공용 IP 주소**입니다. 하나 이상의 공용 IP 주소를 만들려면 [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), [Azure 명령줄 인터페이스 (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)또는 [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)를 사용할 수 있습니다. 아래에 나열 된 요구 사항을 준수 해야 합니다.

> [!NOTE]
> Batch는 공용 IP 주소를 포함 하는 리소스 그룹에 추가 네트워킹 리소스를 자동으로 할당 합니다. 각 100 전용 노드에 대해 일괄 처리는 일반적으로 하나의 NSG (네트워크 보안 그룹) 및 부하 분산 장치 하나를 할당 합니다. 이러한 리소스는 구독의 리소스 할당량에 의해 제한 됩니다. 더 큰 풀을 사용 하는 경우 이러한 리소스 중 하나 이상에 대해 [할당량 증가를 요청](batch-quota-limit.md#increase-a-quota) 해야 할 수 있습니다.

## <a name="public-ip-address-requirements"></a>공용 IP 주소 요구 사항

공용 IP 주소를 만들 때 다음 요구 사항을 염두에 두어야 합니다.

- 공용 IP 주소는 풀을 만드는 데 사용 하는 Batch 계정과 동일한 구독 및 지역에 있어야 합니다.
- **IP 주소 할당** 은 **고정**으로 설정 해야 합니다.
- **SKU** 는 **Standard**로 설정 되어야 합니다.
- DNS 이름을 지정 해야 합니다.
- 공용 IP 주소는 가상 컴퓨터 구성 풀에만 사용 해야 합니다. 이러한 IP 주소를 사용 하는 다른 리소스는 없으며, 풀에서 할당 오류가 발생할 수 있습니다.
- 보안 정책 또는 리소스 잠금은 사용자의 공용 IP 주소에 대 한 액세스를 제한 하지 않아야 합니다.
- 풀에 대해 지정 된 공용 IP 주소의 수는 풀을 대상으로 하는 Vm의 수를 수용할 만큼 충분히 커야 합니다. 이 값은 적어도 **targetDedicatedNodes**   풀의 targetDedicatedNodes 및 **targetLowPriorityNodes**속성의 합계 여야 합니다   . IP 주소가 충분 하지 않으면 풀에서 계산 노드를 부분적으로 할당 하 고 크기 조정 오류가 발생 합니다. 현재 Batch는 100 Vm 마다 하나의 공용 IP 주소를 사용 합니다.
- 항상 공용 IP 주소의 추가 버퍼가 있습니다. 하나 이상의 추가 공용 ip 주소를 추가 하거나 풀에 추가 하는 총 공용 IP 주소의 약 10%를 추가 하는 것이 좋습니다. 이 추가 버퍼는 규모를 축소 하는 경우 내부 최적화를 사용 하 여 일괄 처리에 도움이 되며, 규모를 확장 하거나 축소 한 후에 더 빠르게 확장할 수 있습니다.
- 풀을 만든 후에는 풀에서 사용 하는 공용 IP 주소 목록을 추가 하거나 변경할 수 없습니다. 목록을 수정 해야 하는 경우 풀을 삭제 한 다음 다시 만들어야 합니다.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>공용 IP 주소를 사용 하 여 Batch 풀 만들기

아래 예제에서는 [Azure Batch Service REST API](/rest/api/batchservice/pool/add) 를 사용 하 여 공용 IP 주소를 사용 하는 풀을 만드는 방법을 보여 줍니다.

### <a name="batch-service-rest-api"></a>Batch 서비스 REST API

REST API URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

요청 본문

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "maxTasksPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      }, 
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure 가상 네트워크의 서브넷에서 풀을 만드는](batch-virtual-network.md)방법에 대해 알아봅니다.
- [공용 IP 주소가 없는 Azure Batch 풀을 만드는](./batch-pool-no-public-ip-address.md)방법에 대해 알아봅니다.

