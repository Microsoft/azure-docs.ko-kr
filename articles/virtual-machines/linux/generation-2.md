---
title: 2 세대 Vm에 대 한 Azure 지원
description: 2 세대 Vm에 대 한 Azure 지원 개요
services: virtual-machines-linux
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 70b13b16e6aca6b0bdb0858a32a219defef6cca3
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162041"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Azure의 2 세대 Vm에 대 한 지원

이제 Azure에서 2 세대 Vm (가상 머신)에 대 한 지원이 제공 됩니다. 가상 컴퓨터를 만든 후에는 가상 컴퓨터의 세대를 변경할 수 없으므로 세대를 선택 하기 전에이 페이지에 대 한 고려 사항을 검토 하세요.

2 세대 Vm은 1 세대 Vm에서 지원 되지 않는 주요 기능을 지원 합니다. 이러한 기능에는 메모리 증가, intel SGX (Software Guard Extensions) 및 vPMEM (가상화 된 영구 메모리)가 포함 됩니다. 온-프레미스에서 실행 되는 2 세대 Vm에는 아직 Azure에서 지원 되지 않는 몇 가지 기능이 있습니다. 자세한 내용은 [기능 및](#features-and-capabilities) 기능 섹션을 참조 하세요.

2 세대 Vm은 1 세대 Vm에서 사용 하는 BIOS 기반 아키텍처가 아닌 새로운 UEFI 기반 부팅 아키텍처를 사용 합니다. 1 세대 Vm과 비교할 때 2 세대 Vm은 부팅 및 설치 시간을 향상 시킬 수 있습니다. 2 세대 Vm의 개요와 1 세대와 2 세대 간의 차이점에 대 한 개요는 [hyper-v에서 1 세대 또는 2 세대 가상 머신을 만들어야 하나요?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)를 참조 하세요.

## <a name="generation-2-vm-sizes"></a>2 세대 VM 크기

1 세대 Vm은 Azure의 모든 VM 크기에서 지원 됩니다 (Mv2 시리즈 Vm은 제외). 이제 Azure는 다음과 같은 선택한 VM 시리즈에 대 한 2 세대 지원을 제공 합니다.

* [B 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [DC 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* [Dsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) 및 [Dsv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Esv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Fsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [GS 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [HB-시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [HC 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* [Ls 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) 및 [Lsv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Mv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* [NCv2 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) 및 [NCv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [ND 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [NVv3 시리즈](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

> [!NOTE]
> Mv2 시리즈는 2 세대 VM 이미지와 독점적으로 작동 하므로 Mv2 시리즈 Vm에 대 한 2 세대 VM 이미지 사용은 일반적으로 사용할 수 있습니다. 1 세대 VM 이미지는 Mv2 시리즈 Vm에서 지원 되지 않습니다. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Azure Marketplace의 2 세대 VM 이미지

2 세대 Vm은 다음 Marketplace 이미지를 지원 합니다.

* Windows Server 2019, 2016, 2012 R2, 2012
* 윈도우 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.0, 7.6, 7.5, 7.4, 7.0
* 운영 체제 8.0

## <a name="on-premises-vs-azure-generation-2-vms"></a>온-프레미스 및 Azure 2 세대 Vm

Azure는 현재 온-프레미스 Hyper-v에서 2 세대 Vm에 대해 지 원하는 기능 중 일부를 지원 하지 않습니다.

| 2 세대 기능                | 온-프레미스 Hyper-v | Azure |
|-------------------------------------|---------------------|-------|
| 보안 부팅                         | :heavy_check_mark:  | :x:   |
| 차폐 VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| VBS (가상화 기반 보안) | :heavy_check_mark:  | :x:   |
| VHDX 형식                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>특징과 기능

### <a name="generation-1-vs-generation-2-features"></a>1 세대와 2 세대의 기능 비교

| 기능 | 1세대 | 2세대 |
|---------|--------------|--------------|
| 부팅             | PCAT         | UEFI |
| 디스크 컨트롤러 | IDE          | SCSI |
| VM 크기         | 모든 VM 크기 | Premium storage를 지 원하는 Vm만 |

### <a name="generation-1-vs-generation-2-capabilities"></a>1 세대와 2 세대의 기능 비교

| 기능 | 1세대 | 2세대 |
|------------|--------------|--------------|
| OS 디스크 > 2tb                    | :x:                | :heavy_check_mark: |
| 사용자 지정 디스크/이미지/스왑 OS         | :heavy_check_mark: | :heavy_check_mark: |
| 가상 머신 확장 집합 지원 | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| 백업/복원                    | :heavy_check_mark: | :heavy_check_mark: |
| 공유 이미지 갤러리              | :heavy_check_mark: | :heavy_check_mark: |
| Azure Disk Encryption             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>2 세대 VM 만들기

### <a name="marketplace-image"></a>Marketplace 이미지

Azure Portal 또는 Azure CLI에서는 UEFI 부팅을 지 원하는 Marketplace 이미지에서 2 세대 Vm을 만들 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal에서 2 세대 (Gen2) VM을 만드는 단계는 다음과 같습니다.

1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
1. **리소스 만들기**를 선택합니다.
1. 왼쪽의 Azure Marketplace에서 **모두 보기** 를 클릭 합니다.
1. Gen2를 지 원하는 이미지를 선택 합니다.
1. **만들기**를 클릭합니다.
1. **고급** 탭의 **VM 생성** 섹션에서 **Gen 2** 옵션을 선택 합니다.
1. **기본** 정보 탭의 **인스턴스 정보**에서 **크기** 로 이동 하 고 **VM 크기 선택** 블레이드를 엽니다.
1. [지원 되는 2 세대 VM](#generation-2-vm-sizes)을 선택 합니다.
1. [Azure Portal 만들기 흐름](quick-create-portal.md) 을 진행 하 여 VM 만들기를 완료 합니다.

![Gen 1 또는 Gen 2 VM 선택](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

또한 PowerShell을 사용 하 여 1 세대 또는 2 세대 SKU를 직접 참조 하 여 VM을 만들 수 있습니다.

예를 들어, 다음 PowerShell cmdlet을 사용 하 여 `WindowsServer` 제품의 Sku 목록을 가져올 수 있습니다.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

또는 Azure CLI를 사용 하 여 **게시자**에 나열 된 사용 가능한 2 세대 이미지를 볼 수 있습니다.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

OS로 Windows Server 2012를 사용 하 여 VM을 만드는 경우 1 세대 (BIOS) 또는 2 세대 (UEFI) VM SKU를 선택 합니다 .이는 다음과 같습니다.

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

지원 되는 Marketplace 이미지의 최신 목록은 [기능 및 기능](#features-and-capabilities) 섹션을 참조 하세요.

### <a name="managed-image-or-managed-disk"></a>관리 되는 이미지 또는 관리 디스크

1 세대 VM을 만드는 것과 같은 방법으로 관리 되는 이미지 또는 관리 디스크에서 2 세대 VM을 만들 수 있습니다.

### <a name="virtual-machine-scale-sets"></a>가상 머신 크기 집합

또한 가상 머신 확장 집합을 사용 하 여 2 세대 Vm을 만들 수 있습니다. Azure CLI에서 Azure 확장 집합을 사용 하 여 2 세대 Vm을 만듭니다.

## <a name="frequently-asked-questions"></a>질문과 대답

* **모든 Azure 지역에서 2 세대 Vm을 사용할 수 있나요?**  
    예. 하지만 모든 지역에서 [2 세대 VM 크기](#generation-2-vm-sizes) 를 사용할 수 있는 것은 아닙니다. 2 세대 VM의 가용성은 VM 크기의 가용성에 따라 달라 집니다.

* **1 세대 및 2 세대 Vm 간에 가격 차이가 있나요?**  
    아니요.

* **온-프레미스 2 세대 VM의 .vhd 파일이 있습니다. 이 .vhd 파일을 사용 하 여 Azure에서 2 세대 VM을 만들 수 있나요?**
  예, 2 세대 .vhd 파일을 Azure로 가져와서이를 사용 하 여 2 세대 VM을 만들 수 있습니다. 이렇게 하려면 다음 단계를 사용 합니다.
    1. VM을 만들려는 동일한 지역의 저장소 계정에 .vhd를 업로드 합니다.
    1. .Vhd 파일에서 관리 디스크를 만듭니다. Hyper-v 생성 속성을 V2로 설정 합니다. 다음 PowerShell 명령은 관리 디스크를 만들 때 Hyper-v 생성 속성을 설정 합니다.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. 디스크를 사용할 수 있게 되 면이 디스크를 연결 하 여 VM을 만듭니다. 만든 VM은 2 세대 VM이 됩니다.
    2 세대 VM을 만들 때 필요에 따라이 VM의 이미지를 일반화할 수 있습니다. 이미지를 일반화 하 여 여러 Vm을 만드는 데 사용할 수 있습니다.

* **OS 디스크 크기를 늘릴 어떻게 할까요? 있나요?**  
  2tb 보다 큰 OS 디스크는 2 세대 Vm에 대 한 새로운 것입니다. 기본적으로 OS 디스크는 2 세대 Vm의 경우 2tb 보다 작습니다. 최대 4 TB까지 디스크 크기를 늘릴 수 있습니다. Azure CLI 또는 Azure Portal를 사용 하 여 OS 디스크 크기를 늘립니다. 디스크를 프로그래밍 방식으로 확장 하는 방법에 대 한 자세한 내용은 [디스크 크기 조정](expand-disks.md)을 참조 하세요.

  Azure Portal에서 OS 디스크 크기를 늘리려면 다음을 수행 하십시오.

  1. Azure Portal에서 VM 속성 페이지로 이동 합니다.
  1. VM을 종료 하 고 할당을 취소 하려면 **중지** 단추를 선택 합니다.
  1. **디스크** 섹션에서 증가 시킬 OS 디스크를 선택 합니다.
  1. **디스크** 섹션에서 **구성**을 선택 하 고 **크기** 를 원하는 값으로 업데이트 합니다.
  1. VM 속성 페이지로 돌아가서 VM을 **시작** 합니다.

  2tb 보다 큰 OS 디스크에 대 한 경고가 표시 될 수 있습니다. 이 경고는 2 세대 Vm에는 적용 되지 않습니다. 그러나 4 TB 보다 큰 OS 디스크 크기는 *권장 되지 않습니다.*

* **2 세대 Vm은 가속화 네트워킹을 지원 하나요?**  
    예. 자세한 내용은 [가속화 된 네트워킹을 사용 하 여 VM 만들기](../../virtual-network/create-vm-accelerated-networking-cli.md)를 참조 하세요.

* **VHDX는 2 세대에서 지원 되나요?**  
    아니요, 2 세대 Vm은 VHD만 지원 합니다.

* **2 세대 Vm은 Azure Ultra 디스크 저장소을 지원 하나요?**  
    예.

* **1 세대에서 2 세대로 VM을 마이그레이션할 수 있나요?**  
    아니요, VM을 만든 후에는 VM의 세대를 변경할 수 없습니다. VM 세대 간을 전환 해야 하는 경우 다른 세대의 새 VM을 만듭니다.

* **Gen2 VM을 만들려고 할 때 크기 선택기에서 내 VM 크기를 사용할 수 없는 이유는 무엇입니까?**

    이는 다음을 수행 하 여 해결할 수 있습니다.

    1. **고급** 탭에서 **VM 생성** 속성이 **Gen 2** 로 설정 되어 있는지 확인 합니다.
    1. [Gen2 vm을 지 원하는 vm 크기](#generation-2-vm-sizes)를 검색 하 고 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* [Hyper-v의 2 세대 가상 컴퓨터](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)에 대해 알아봅니다.
