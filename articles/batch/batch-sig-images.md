---
title: 공유 이미지 갤러리를 사용 하 여 사용자 지정 풀 만들기-Azure Batch | Microsoft Docs
description: 응용 프로그램에 필요한 소프트웨어와 데이터를 포함 하는 계산 노드에 사용자 지정 이미지를 프로 비전 하기 위해 공유 이미지 갤러리를 사용 하 여 Batch 풀을 만듭니다. 사용자 지정 이미지는 Batch 워크로드를 실행하도록 컴퓨팅 노드를 구성하는 효율적인 방법입니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 08/28/2019
ms.author: labrenne
ms.openlocfilehash: 2cff6a0e48fc7bf58a642f509fcda6b114e002ef
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022939"
---
# <a name="use-the-shared-image-gallery-to-create-a-custom-pool"></a>공유 이미지 갤러리를 사용 하 여 사용자 지정 풀 만들기

Virtual Machine 구성을 사용하여 Azure Batch 풀을 만들 경우 풀에서 각 컴퓨팅 노드에 대해 운영 체제를 제공하는 VM 이미지를 지정합니다. 지원 되는 Azure Marketplace 이미지를 사용 하 여 가상 머신 풀을 만들거나 [공유 이미지 갤러리](../virtual-machines/windows/shared-image-galleries.md)를 사용 하 여 사용자 지정 이미지를 만들 수 있습니다.

## <a name="benefits-of-the-shared-image-gallery"></a>공유 이미지 갤러리의 이점

사용자 지정 이미지에 대한 공유 이미지 갤러리를 사용 하는 경우 운영 체제 유형과 구성 뿐만 아니라 데이터 디스크의 유형을 제어할 수 있습니다. 공유 이미지는 프로 비전 되는 즉시 모든 Batch 풀 노드에서 사용할 수 있게 되는 응용 프로그램 및 참조 데이터를 포함할 수 있습니다.

또한 사용자 환경에 맞게 여러 버전의 이미지를 사용할 수 있습니다. 이미지 버전을 사용 하 여 VM을 만드는 경우 이미지 버전은 VM에 대한 새 디스크를 만드는 데 사용 됩니다.

공유 이미지를 사용 하면 풀의 계산 노드를 준비 하 여 Batch 워크 로드를 실행 하는 데 걸리는 시간을 절약할 수 있습니다. 프로 비전 후에는 Azure Marketplace 이미지를 사용 하 고 각 계산 노드에 소프트웨어를 설치할 수 있지만 일반적으로 공유 이미지를 사용 하는 것이 더 효율적입니다. 또한 여러 Vm (600 개 이상)을 사용 하 여 풀을 만들 때 풀을 만들 때 시간을 절약 하기 위해 공유 이미지에 대해 여러 복제본을 지정할 수 있습니다.

시나리오에 대해 구성 된 공유 이미지를 사용 하면 다음과 같은 여러 가지 이점을 얻을 수 있습니다.

* **지역에서 동일한 이미지를 사용 합니다.** 모든 풀에서 동일한 이미지를 사용 하도록 여러 지역에 걸쳐 공유 이미지 복제본을 만들 수 있습니다.
* **OS (운영 체제)를 구성 합니다.** 이미지의 운영 체제 디스크 구성을 사용자 지정할 수 있습니다.
* **애플리케이션 미리 설치**. 시작 태스크를 사용 하 여 계산 노드를 프로 비전 한 후 응용 프로그램을 설치 하는 것 보다 더 효율적이 고 오류가 발생 하기 쉬우므로 OS 디스크에 응용 프로그램을 사전 설치할 수 있습니다.
* **많은 양의 데이터를 한 번 복사 합니다.** 관리 되는 공유 이미지를 관리 되는 이미지의 데이터 디스크로 복사 하 여 정적 데이터를 관리 되는 공유 이미지의 일부로 만듭니다. 이 작업은 한 번만 수행하며 풀의 각 노드에서 데이터를 사용할 수 있게 됩니다.
* **풀을 더 큰 크기로 확장 합니다.** 공유 이미지 갤러리를 사용 하면 더 많은 공유 이미지 복제본과 함께 사용자 지정 된 이미지를 사용 하 여 더 큰 풀을 만들 수 있습니다.
* **사용자 지정 이미지 보다 성능이 우수 합니다.** 공유 이미지를 사용 하는 경우 풀이 안정 된 상태에 도달 하는 데 걸리는 시간은 최대 25% 더 빠르며 VM 유휴 대기 시간은 최대 30% 더 짧습니다.
* **보다 쉽게 관리할 수 있도록 이미지 버전 관리 및 그룹화** 이미지 그룹화 정의에는 이미지를 만든 이유에 대한 정보, 사용 중인 OS 및 이미지 사용에 대한 정보가 포함 되어 있습니다. 이미지를 그룹화 하면 이미지를 쉽게 관리할 수 있습니다. 자세한 내용은 [이미지 정의](../virtual-machines/windows/shared-image-galleries.md#image-definitions)를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure Batch 계정.** Batch 계정을 만들려면 [Azure Portal](quick-create-portal.md) 또는 [Azure CLI](quick-create-cli.md)를 사용 하 여 일괄 처리 퀵 스타트를 참조 하세요.

* **공유 이미지 갤러리 이미지**입니다. 공유 이미지를 만들려면 관리 되는 이미지 리소스를 만들거나 만들어야 합니다. VM OS 디스크 및 연결된 데이터 디스크(선택 사항)의 스냅샷에서 이미지를 만들어야 합니다. 자세한 내용은 [관리 되는 이미지 준비](#prepare-a-managed-image)를 참조 하세요.

> [!NOTE]
> 공유 이미지는 Batch 계정과 동일한 구독에 있어야 합니다. 공유 이미지는 Batch 계정과 동일한 지역에 복제본이 있는 경우 다른 지역에 있을 수 있습니다.

## <a name="prepare-a-managed-image"></a>관리 되는 이미지 준비

Azure에서 관리 되는 이미지를 준비 하려면 다음을 수행 합니다.

* Azure VM의 OS 및 데이터 디스크의 스냅숏
* 관리 디스크를 사용 하는 일반화 된 Azure VM
* 클라우드로 업로드 된 일반화 된 온-프레미스 VHD

사용자 지정 이미지를 사용하여 안정적으로 Batch 풀 크기를 조정하려는 경우 VM 디스크의 스냅샷을 사용하는 *첫 번째 방법만* 사용하여 관리되는 이미지를 만드는 것이 좋습니다. VM을 준비하고 스냅샷을 생성하고 스냅샷에서 이미지를 만들려면 다음 단계를 참조하세요.

### <a name="prepare-a-vm"></a>VM 준비

이미지에 대한 새 VM을 만드는 경우 Batch에서 지원 되는 첫 번째 파티 Azure Marketplace 이미지를 관리 되는 이미지의 기본 이미지로 사용 합니다. 첫 번째 파티 이미지만 기본 이미지로 사용할 수 있습니다. Azure Batch에서 지 원하는 Azure Marketplace 이미지 참조의 전체 목록을 얻으려면 [노드 에이전트 Sku 나열](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) 작업을 참조 하세요.

> [!NOTE]
> 추가 라이선스 및 구매 약관이 있는 타사 이미지는 기본 이미지로 사용할 수 없습니다. Marketplace 이미지에 대한 자세한 내용은 [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) 또는 [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM을 참조하세요.

* VM이 관리 디스크를 사용 하 여 만들어졌는지 확인 합니다. VM을 만들 때 기본 스토리지 설정입니다.
* 사용자 지정 스크립트 확장 등의 Azure 확장을 VM에 설치해서는 안 됩니다. 이미지에 미리 설치된 확장이포함되어 있으면 Batch 풀을 배포할 때 Azure에서 문제가 발생할 수 있습니다.
* 연결 된 데이터 디스크를 사용 하는 경우 VM 내에서 디스크를 탑재 하 고 포맷 하 여 사용 해야 합니다.
* 제공하는 기본 OS 이미지가 기본 임시 드라이브를 사용하도록 해야 합니다. Batch 노드 에이전트는 현재 기본 임시 드라이브를 예상합니다.
* VM이 실행되면 RDP(Windows용) 또는 SSH(Linux용)를 통해 연결합니다. 필요한 소프트웨어를 설치하거나 원하는 데이터를 복사합니다.  

### <a name="create-a-vm-snapshot"></a>VM 스냅샷 만들기

스냅샷은 VHD의 전체 읽기 전용 복사본입니다. VM OS 또는 데이터 디스크의 스냅샷을 만들려는 경우 Azure Portal 또는 명령줄 도구를 사용할 수 있습니다. 스냅샷을 만드는 단계와 옵션은 [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) 또는 [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM용 지침을 참조하세요.

### <a name="create-an-image-from-one-or-more-snapshots"></a>스냅샷 하나 이상에서 이미지 만들기

스냅샷에서 관리되는 이미지를 만들려면 [az image create](/cli/azure/image) 명령과 같은 Azure 명령줄 도구를 사용합니다. OS 디스크 스냅숏과 선택적으로 하나 이상의 데이터 디스크 스냅숏을 지정 하 여 이미지를 만듭니다.

### <a name="create-a-shared-image-gallery"></a>Shared Image Gallery 만들기

관리 이미지를 성공적으로 만들었으면 공유 이미지 갤러리를 만들어 사용자 지정 이미지를 사용할 수 있도록 해야 합니다. 이미지에 대한 공유 이미지 갤러리를 만드는 방법을 알아보려면 [Azure CLI를 사용 하 여 공유 이미지 갤러리 만들기](../virtual-machines/linux/shared-images.md) 또는 [Azure Portal를 사용 하 여 공유 이미지 갤러리 만들기](../virtual-machines/linux/shared-images-portal.md)를 참조 하세요.

## <a name="create-a-pool-from-a-shared-image-using-the-azure-cli"></a>Azure CLI를 사용 하 여 공유 이미지에서 풀 만들기

Azure CLI를 사용 하 여 공유 이미지에서 풀을 만들려면 `az batch pool create` 명령을 사용 합니다. `--image` 필드에서 공유 이미지 ID를 지정 합니다. OS 유형 및 SKU가 `--node-agent-sku-id`에 지정 된 버전과 일치 하는지 확인 합니다.

```azurecli
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}" \
    --node-agent-sku-id "batch.node.ubuntu 16.04"
```

## <a name="create-a-pool-from-a-shared-image-using-c"></a>을 사용 하 여 공유 이미지에서 풀 만들기C#

또는 C# SDK를 사용 하 여 공유 이미지에서 풀을 만들 수 있습니다.

```csharp
private static VirtualMachineConfiguration CreateVirtualMachineConfiguration(ImageReference imageReference)
{
    return new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: "batch.node.windows amd64");
}

private static ImageReference CreateImageReference()
{
    return new ImageReference(
        virtualMachineImageId: "/subscriptions/{sub id}/resourceGroups/{resource group name}/providers/Microsoft.Compute/galleries/{gallery name}/images/{image definition name}/versions/{version id}");
}

private static void CreateBatchPool(BatchClient batchClient, VirtualMachineConfiguration vmConfiguration)
{
    try
    {
        CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: PoolId,
            targetDedicatedComputeNodes: PoolNodeCount,
            virtualMachineSize: PoolVMSize,
            virtualMachineConfiguration: vmConfiguration);

        pool.Commit();
    }
    ...
}
```

## <a name="create-a-pool-from-a-shared-image-using-the-azure-portal"></a>Azure Portal를 사용 하 여 공유 이미지에서 풀 만들기

Azure Portal에서 공유 이미지를 사용 하 여 풀을 만들려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. **Batch 계정** 으로 이동 하 여 계정을 선택 합니다.
1. **풀** 을 선택한 다음 **추가** 를 클릭 하 여 새 풀을 만듭니다.
1. **이미지 형식** 섹션에서 **공유 이미지 갤러리**를 선택 합니다.
1. 관리 되는 이미지에 대한 정보를 사용 하 여 나머지 섹션을 완료 합니다.
1. **확인**을 선택합니다.

![포털을 사용 하 여 공유 이미지에서 풀을 만듭니다.](media/batch-sig-images/create-custom-pool.png)

## <a name="considerations-for-large-pools"></a>대형 풀 관련 고려 사항

공유 이미지를 사용 하 여 수백 또는 수천 개의 Vm이 포함 된 풀을 만들려는 경우 다음 지침을 따르십시오.

* **공유 이미지 갤러리 복제본 번호입니다.**  최대 600 인스턴스를 포함 하는 모든 풀에 대해 하나 이상의 복제본을 유지 하는 것이 좋습니다. 예를 들어 3000 Vm을 사용 하 여 풀을 만드는 경우 이미지의 복제본을 5 개 이상 유지 해야 합니다. 성능 향상을 위해 항상 최소 요구 사항 보다 더 많은 복제본을 유지 하는 것이 좋습니다.

* **크기 조정 시간 제한입니다.** 풀이 고정 된 수의 노드를 포함 하는 경우 (자동 크기 조정 하지 않는 경우) 풀 크기에 따라 풀의 `resizeTimeout` 속성을 늘립니다. 1000 Vm 마다 권장 크기 조정 시간 제한이 15 분 이상입니다. 예를 들어 2000 Vm이 있는 풀에 권장 되는 크기 조정 제한 시간은 30 분 이상입니다.

## <a name="next-steps"></a>다음 단계

* Batch에 대한 심층적인 개요는 [Batch를 사용하여 대규모 병렬 컴퓨팅 솔루션 개발](batch-api-basics.md)을 참조하세요.
