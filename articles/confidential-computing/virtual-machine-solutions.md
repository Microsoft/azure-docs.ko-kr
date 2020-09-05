---
title: 가상 머신의 Azure 기밀 컴퓨팅 솔루션
description: 가상 머신의 Azure 기밀 컴퓨팅 솔루션에 대해 알아봅니다.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: f9b73e0919d660947edd0417f7379b3f6e6140c0
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245855"
---
# <a name="solutions-on-azure-virtual-machines"></a>Azure 가상 머신의 솔루션

이 문서에서는 [Intel SGX(Software Guard Extension)](https://software.intel.com/sgx) 의해 지원되는 Intel 프로세서를 실행하는 Azure 기밀 컴퓨팅 VM(가상 머신) 배포에 대한 정보를 다룹니다. 

## <a name="azure-confidential-computing-vm-sizes"></a>Azure 기밀 컴퓨팅 VM 크기

Azure 기밀 컴퓨팅 가상 머신은 클라우드에서 처리되는 동안 데이터 및 코드의 기밀성과 무결성을 보호하도록 설계되었습니다. 

[DCsv2 시리즈](../virtual-machines/dcv2-series.md) VM은 최신의 최근 기밀 컴퓨팅 크기 제품군입니다. 이러한 VM은 더 큰 범위의 배포 기능을 지원하며, Enclave 페이지 캐시(EPC) 2개를 포함하며 DC 시리즈 VM과 비교하여 더 많은 크기를 선택합니다. [DC 시리즈](../virtual-machines/sizes-previous-gen.md#preview-dc-series) VM은 현재 미리 보기 상태이며 일반 공급에 포함되지 않으며 사용되지 않습니다.

[빠른 시작 자습서](quick-create-marketplace.md)를 수행하여 Microsoft 상업용 Marketplace를 통해 DCsv2 시리즈 VM 배포를 시작합니다.

### <a name="current-available-sizes-and-regions"></a>현재 사용 가능한 크기 및 지역

사용 가능한 지역 및 가용성 영역에서 일반적으로 사용할 수 있는 모든 기밀 계산 VM 크기의 목록을 얻으려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)에서 다음 명령을 실행합니다.

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

위의 크기에 대한 자세한 보기를 보려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>전용 호스트 요구 사항
DCSv2 시리즈 VM 제품군에서 **Standard_DC8_v2** 가상 머신 크기를 배포 하는 것은 전체 호스트를 차지 하 고 다른 테 넌 트 또는 구독과 공유 되지 않습니다. 이 VM SKU 제품군은 전용 호스트 서비스를 통해 일반적으로 충족 되는 규정 준수 및 보안 규정 요구 사항을 충족 하기 위해 필요할 수 있는 격리를 제공 합니다. **Standard_DC8_v2** SKU를 선택 하면 실제 호스트 서버는 사용 가능한 모든 하드웨어 리소스 (예를 들어, 가상 컴퓨터에 대 한 EPC 메모리 포함)를 할당 합니다. 이 기능은 인프라 설계에 의해 존재 하며 **Standard_DC8_v2** 의 모든 기능이 지원 될 예정입니다. 이 배포는 다른 Azure VM 제품군에서 제공 하는 [Azure 전용 호스트](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts) 서비스와 동일 하지 않습니다.


## <a name="deployment-considerations"></a>배포 고려 사항

빠른 시작 자습서에 따라 10분 이내에 DCsv2 시리즈 가상 머신을 배포합니다. 

- **Azure 구독** - 기밀 컴퓨팅 VM 인스턴스를 배포하려면 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우 적절한 Azure 계산 코어 양에 대한 할당량이 없습니다.

- **가격 책정 및 지역 가용성** - [가상 머신 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에서 DCsv2 시리즈 VM에 대한 가격 책정을 찾습니다. [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 에서 Azure 지역의 가용성을 확인하세요.


- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 구독에 따라서도 DCsv2 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) 합니다. 기본 제한은 구독 범주에 따라 달라질 수 있습니다.

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **크기 조정** - 특수한 하드웨어로 인해 동일한 크기의 제품군 내에서만 기밀 컴퓨팅 인스턴스의 크기를 조정할 수 있습니다. 예를 들어 DCsv2 시리즈 VM 크기는 한 DCsv2 시리즈에서 다른 DCsv2 시리즈로만 조정할 수 있습니다. 기밀이 아닌 컴퓨팅 크기에서 기밀 컴퓨팅 크기로 크기를 조정하는 것은 지원되지 않습니다.  

- **이미지** - 기밀 컴퓨팅 인스턴스에 Intel SGX(Software Guard Extension) 지원을 제공하기 위해 모든 배포를 2세대 이미지에서 실행해야 합니다. Azure 기밀 컴퓨팅은 Ubuntu 18.04 Gen 2, Ubuntu 16.04 Gen 2, Windows Server 2019 Gen 2 및 Windows Server 2016 Gen 2에서 실행되는 워크로드를 지원합니다. 지원되거나 지원되지 않는 시나리오에 대한 자세한 내용은 [Azure의 2세대 VM에 대한 지원](../virtual-machines/linux/generation-2.md)을 참조하세요. 

- **스토리지** - Azure 기밀 컴퓨팅 가상 머신 데이터 디스크 및 사용 후 삭제되는 OS 디스크는 NVMe 디스크에 있습니다. 인스턴스는 프리미엄 SSD 및 표준 SSD 디스크만 지원하고 울트라 SSD 또는 표준 HDD는 지원하지 않습니다. 가상 머신 크기 **DC8_v2**는 프리미엄 스토리지를 지원하지 않습니다. 

- **디스크 암호화** - 기밀 컴퓨팅 인스턴스는 현재 디스크 암호화를 지원하지 않습니다. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항

Azure에서 가상 머신을 사용하는 경우 모든 가동 중지 시간을 방지하기 위해 높은 가용성과 재해 복구 솔루션을 구현해야 합니다. 

Azure 기밀 컴퓨팅은 현재 가용성 영역을 통해 영역 중복성을 지원하지 않습니다. 기밀 컴퓨팅에 대한 최고 가용성 및 중복성을 위해 [가용성 집합](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)을 사용합니다. 하드웨어 제한으로 인해 기밀 컴퓨팅 인스턴스의 가용성 집합에는 최대 10개의 업데이트 도메인만 사용할 수 있습니다. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) 템플릿을 사용 하 여 배포

Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 구독에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 배포 이후 액세스 제어, 잠금 및 태그와 같은 관리 기능을 사용하여 리소스를 보호하고 구성할 수 있습니다.

ARM 템플릿에 대 한 자세한 내용은 [템플릿 배포 개요](../azure-resource-manager/templates/overview.md)를 참조 하세요.

ARM 템플릿에서 DCsv2 시리즈 VM을 배포 하려면 [가상 머신 리소스](../virtual-machines/windows/template-description.md)를 활용 합니다. **vmSize** 및 **imageReference**에 대한 올바른 속성을 지정해야 합니다.

### <a name="vm-size"></a>VM 크기

가상 컴퓨터 리소스의 ARM 템플릿에서 다음 크기 중 하나를 지정 합니다. 이 문자열은 **속성**에서 **vmSize**로 배치됩니다.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Gen2 OS 이미지

**속성** 아래의 **storageProfile**에서 이미지를 참조해야 합니다. **imageReference**에 대해 다음 이미지 중 하나만 사용합니다.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>다음 단계 

이 문서에서는 기밀 컴퓨팅 가상 머신을 만들 때 필요한 자격 및 구성에 대해 알아보았습니다. 이제 Microsoft Azure Marketplace로 이동하여 DCsv2 시리즈 VM을 배포할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Marketplace에 DCsv2 시리즈 가상 머신 배포](quick-create-marketplace.md)
