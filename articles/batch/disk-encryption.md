---
title: 디스크 암호화가 사용된 풀 생성
description: 디스크 암호화 구성을 사용 하 여 플랫폼 관리 키를 사용 하 여 노드를 암호화 하는 방법을 알아봅니다.
author: pkshultz
ms.topic: how-to
ms.date: 08/25/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 9b0f7f9963ee0edd3986f7ec808a8a4060d857f8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267050"
---
# <a name="create-a-pool-with-disk-encryption-enabled"></a>디스크 암호화가 사용된 풀 생성

가상 컴퓨터 구성을 사용 하 여 Azure Batch 풀을 만들 때 디스크 암호화 구성을 지정 하 여 플랫폼 관리 키를 사용 하 여 풀의 계산 노드를 암호화할 수 있습니다.

이 문서에서는 디스크 암호화를 사용 하 여 Batch 풀을 만드는 방법을 설명 합니다.

## <a name="why-use-a-pool-with-disk-encryption-configuration"></a>디스크 암호화 구성에서 풀을 사용 하는 이유

Batch 풀을 사용 하면 OS 및 계산 노드의 임시 디스크에 데이터를 액세스 하 고 저장할 수 있습니다. 플랫폼 관리 키를 사용 하 여 서버 쪽 디스크를 암호화 하면 오버 헤드가 적고 편리 하 게이 데이터를 보호할 수 있습니다.  

Batch는 풀 구성 및 지역별 지원 가능성을 기준으로 계산 노드에 이러한 디스크 암호화 기술 중 하나를 적용 합니다.

- [플랫폼 관리 키를 사용 하 여 미사용 관리 디스크 암호화](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [플랫폼 관리 키를 사용 하 여 호스트에서 암호화](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 디스크 암호화](../security/fundamentals/azure-disk-encryption-vms-vmss.md)

> [!IMPORTANT]
> Azure Batch의 플랫폼 관리 키를 사용 하는 호스트의 암호화 지원은 현재 미국 동부, 미국 서 부 2, 미국 중부, US Gov 버지니아 및 US Gov 애리조나 지역에 대 한 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

풀의 노드에 적용 되는 암호화 방법을 지정할 수 없습니다. 대신, 노드에서 암호화 하려는 대상 디스크를 제공 하 고 Batch에서 적절 한 암호화 방법을 선택 하 여 지정 된 디스크가 계산 노드에서 암호화 되도록 할 수 있습니다.
 
## <a name="azure-portal"></a>Azure portal 

Azure Portal에서 Batch 풀을 만들 때 **디스크 암호화 구성**에서 **TemporaryDisk** 또는 **OsAndTemporaryDisk** 를 선택 합니다.

:::image type="content" source="media/disk-encryption/portal-view.png" alt-text="Azure Portal에서 디스크 암호화 구성 옵션의 스크린샷":::

풀을 만든 후에는 풀의 **속성** 섹션에서 디스크 암호화 구성 대상을 확인할 수 있습니다.

:::image type="content" source="media/disk-encryption/configuration-target.png" alt-text="Azure Portal의 디스크 암호화 구성 대상을 보여 주는 스크린샷":::

## <a name="examples"></a>예

다음 예에서는 batch .NET SDK, Batch REST API 및 Azure CLI를 사용 하 여 Batch 풀에서 OS 및 임시 디스크를 암호화 하는 방법을 보여 줍니다.

### <a name="batch-net-sdk"></a>Batch .NET SDK

```csharp
pool.VirtualMachineConfiguration.DiskEncryptionConfiguration = new DiskEncryptionConfiguration(
    targets: new List<DiskEncryptionTarget> { DiskEncryptionTarget.OsDisk, DiskEncryptionTarget.TemporaryDisk }
    );
```

### <a name="batch-rest-api"></a>Batch REST API

REST API URL:
```
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```
본문 요청:
```
"pool": {
    "id": "pool2",
    "vmSize": "standard_a1",
    "virtualMachineConfiguration": {
        "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "18.04-LTS"
        },
        "diskEncryptionConfiguration": {
            "targets": [
                "OsDisk",
                "TemporaryDisk"
            ]
        }
        "nodeAgentSKUId": "batch.node.ubuntu 18.04"
    },
    "resizeTimeout": "PT15M",
    "targetDedicatedNodes": 5,
    "targetLowPriorityNodes": 0,
    "maxTasksPerNode": 3,
    "enableAutoScale": false,
    "enableInterNodeCommunication": false
}
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az batch pool create \
    --id diskencryptionPool \
    --vm-size Standard_DS1_V2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:18.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 18.04" \
    --disk-encryption-targets OsDisk TemporaryDisk
```

## <a name="next-steps"></a>다음 단계

- [Azure 디스크 저장소의 서버 쪽 암호화](../virtual-machines/windows/disk-encryption.md)에 대해 자세히 알아보세요.
- Batch에 대한 자세한 개요는 [Batch 서비스 워크플로 및 리소스](batch-service-workflow-features.md)를 참조하세요.
