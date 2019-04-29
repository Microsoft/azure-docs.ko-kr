---
title: Azure에서 Linux VM 최적화 | Microsoft Docs
description: Azure에서 최적의 성능을 위해 Linux VM을 설정하도록 하는 몇 가지 최적화 팁을 알아봅니다
keywords: linux 가상 컴퓨터, 가상 컴퓨터 linux, ubuntu 가상 컴퓨터
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: 30d153863a20dcdddc702ee5a37c34a2938d7446
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473912"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Azure에서 Linux VM 최적화
Linux 가상 머신(VM) 만들기는 명령줄 또는 포털에서 수행하는 것이 쉽습니다. 이 자습서에서는 Microsoft Azure Platform에서 해당 성능을 최적화하도록 설정하는 방법을 보여줍니다. 이 항목에서는 Ubuntu Server VM을 사용 하지만 [템플릿으로 사용자 고유의 이미지](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 사용하여 Linux 가상 머신을 만들 수도 있습니다.  

## <a name="prerequisites"></a>필수 조건
이 항목에서는 사용하는 Azure 구독([무료 평가판 등록](https://azure.microsoft.com/pricing/free-trial/))이 이미 있으며 Azure 구독에 VM을 이미 프로비전했다고 가정합니다. [VM을 만들기](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 전에 최신 [Azure CLI](/cli/azure/install-az-cli2)를 설치하고 [az login](/cli/azure/reference-index)을 사용하여 Azure 구독에 로그인했는지 확인합니다.

## <a name="azure-os-disk"></a>Azure OS 디스크
Azure에서 Linux VM을 만들면 이에 연결된 두 개의 디스크가 있습니다. **/dev/sda**는 OS 디스크이며 **/dev/sdb**는 임시 디스크입니다.  OS 디스크(**/dev/sda**)는 신속한 VM 부팅 시간에 최적화되고 워크로드에 좋은 성능을 제공하지 않으므로 운영 체제 이외에 사용하지 않습니다. 데이터에 대한 영구적이고 최적화된 저장소를 얻기 위해 VM에 하나 이상의 디스크를 연결하려고 합니다. 

## <a name="adding-disks-for-size-and-performance-targets"></a>크기 및 성능 대상에 디스크 추가
VM 크기에 따라 A 시리즈에 16개, D 시리즈에 32개 및 G 시리즈에 64개의 디스크를 최대로 연결할 수 있고 각각 최대 크기는 1TB입니다. 공간 및 IOps 요구 사항에 따라 필요한 만큼 디스크를 더 추가합니다. 각 디스크의 성능 목표는 Standard Storage의 경우 최대 500IOps이며 Premium Storage의 경우 디스크당 최대 5000IOps입니다.

**읽기 전용** 또는 **해당 없음**으로 캐시를 설정한 Premium Storage 디스크에서 가장 높은 IOps를 수행하기 위해 Linux에서 파일 시스템을 탑재하는 동안 **장벽**을 사용하지 않도록 설정해야 합니다. Premium Storage 백업 디스크에 쓰기는 이러한 캐시 설정에 대해 내구성이 있기 때문에 장벽이 필요하지 않습니다.

* **reiserFS**를 사용하는 경우 탑재 옵션 `barrier=none`을 사용하여 장벽을 사용하지 않도록 설정합니다(장벽 사용의 경우 `barrier=flush` 사용).
* **ext3/ext4**를 사용하는 경우 탑재 옵션 `barrier=0`을 사용하여 장벽을 사용하지 않도록 설정합니다(장벽 사용의 경우 `barrier=1` 사용).
* **XFS**를 사용하는 경우 탑재 옵션 `nobarrier`을 사용하여 장벽을 사용하지 않도록 설정합니다(장벽 사용의 경우 `barrier` 옵션 사용).

## <a name="unmanaged-storage-account-considerations"></a>관리되지 않는 저장소 계정 고려 사항
Azure CLI를 사용하여 VM을 만들 때 기본 작업은 Azure Managed Disks를 사용하는 것입니다.  이들 디스크는 Azure 플랫폼을 통해 처리되며 디스크를 저장할 위치나 준비가 필요하지 않습니다.  관리되지 않는 디스크는 저장소 계정이 필요하며 추가 성능 고려 사항이 있습니다.  관리 디스크에 대한 자세한 내용은 [Azure Managed Disks 개요](../windows/managed-disks-overview.md)를 참조하세요.  다음 섹션에서는 관리되지 않는 디스크를 사용하는 경우에만 성능 고려 사항을 설명합니다.  권장되는 기본 저장소 솔루션은 Managed Disks를 사용하는 것입니다.

관리되지 않는 디스크를 사용하여 VM을 만들 경우 가까운 근접성을 제공하고 네트워크 대기 시간을 최소화하기 위해 VM과 동일한 지역에 위치한 저장소 계정에서 디스크를 연결해야 합니다.  각 표준 저장소 계정에는 최대 20,000IOps 및 500TB 크기의 용량이 포함됩니다.  이러한 제한은 만든 OS 디스크 및 데이터 디스크를 비롯한 약 40개의 많이 사용되는 디스크에 적용됩니다. Premium Storage 계정의 경우 최대 IOps 제한이 없지만 크기는 32TB로 제한됩니다. 

높은 IOps 워크로드를 다루고 디스크에 Standard Storage를 선택한 경우 여러 스토리지 계정에 디스크를 분할하여 Standard Storage 계정에 대한 20,000IOps 제한을 넘지 않아야 합니다. VM은 다른 저장소 계정 및 저장소 계정 유형에서 혼합된 디스크를 포함하여 최적의 구성을 달성할 수 있습니다.
 

## <a name="your-vm-temporary-drive"></a>VM 임시 드라이브
기본적으로 VM을 만들 때 Azure는 OS 디스크(**/dev/sda**)와 임시 디스크(**/dev/sdb**)를 제공합니다.  추가한 모든 추가 디스크는 **/dev/sdc**, **/dev/sdd**, **/dev/sde** 등으로 나타납니다. 임시 디스크(**/dev/sdb**)의 모든 데이터는 영구적이지 않으며 VM 크기 조정, 다시 배포 또는 유지 관리와 같은 특정 이벤트가 강제로 VM을 다시 시작하는 경우 손실될 수 있습니다.  임시 디스크의 크기 및 유형은 배포 시에 선택한 VM 크기와 관련이 있습니다. 모든 프리미엄 크기 VM(DS, G 및 DS_V2 시리즈)의 경우 임시 드라이브는 최대 48,000IOps의 추가 성능을 가진 로컬 SSD에서 지원됩니다. 

## <a name="linux-swap-file"></a>Linux 스왑 파일
Azure VM을 Ubuntu 또는 CoreOS 이미지에서 가져온 경우 CustomData를 사용하여 cloud-config를 cloud-init으로 보낼 수 있습니다. cloud-init를 사용하는 [사용자 지정 Linux 이미지를 업로드](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)한 경우에도 cloud-init를 사용하여 스왑 파티션을 구성할 수 있습니다.

Ubuntu Cloud Images에서는 cloud-init를 사용하여 스왑 파티션을 구성하해야 합니다. 자세한 내용은 [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions)을 참조하세요.

cloud-init를 지원하지 않는 이미지의 경우 Azure Marketplace에서 배포된 VM 이미지에는 OS와 통합된 VM Linux 에이전트가 있습니다. 이 에이전트를 사용하면 VM에서 다양한 Azure 서비스와 상호 작용할 수 있습니다. Azure Marketplace에서 표준 이미지를 배포한 경우 다음을 수행하여 Linux 스왑 파일 설정을 올바르게 구성해야 합니다.

**/etc/waagent.conf** 파일에서 두 개의 항목을 찾아 수정합니다. 전용 스왑 파일의 존재 여부 및 스왑 파일의 크기를 제어합니다. 수정하려는 매개 변수는 `ResourceDisk.EnableSwap=N` 및 `ResourceDisk.SwapSizeMB=0`입니다. 

매개 변수를 다음 설정으로 변경합니다.

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={필요에 맞는 크기(MB)} 

변경한 후에는 변경 내용을 반영하기 위해 waagent를 다시 시작하거나 Linux VM을 다시 시작해야 합니다.  변경 내용을 구현했다면 `free` 명령을 사용하여 여유 공간을 볼 때 스왑 파일이 만들어졌습니다. 다음 예제에서는 **waagent.conf** 파일을 수정한 결과로 생성된 512MB 스왑 파일이 있습니다.

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Premium Storage에 대한 I/O 일정 알고리즘
2.6.18 Linux 커널로 기본 I/O 일정 알고리즘은 최종 기한에서 CFQ로 변경되었습니다(완전히 공정한 큐 대기 알고리즘). 임의 액세스 I/O 패턴의 경우 CFQ와 최종 기한 간의 성능 차이의 차이는 무시할 수 있는 정도입니다.  디스크 I/O 패턴이 대부분 순차적인 SSD 기반 디스크의 경우 NOOP 또는 최종 기한 알고리즘으로 다시 전환하면 I/O 성능을 높일 수 있습니다.

### <a name="view-the-current-io-scheduler"></a>현재 I/O 스케줄러 보기
다음 명령을 사용합니다.  

```bash
cat /sys/block/sda/queue/scheduler
```

현재 스케줄러를 나타내는 다음 출력이 표시됩니다.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>I/O 일정 알고리즘의 현재 디바이스(/dev/sda) 변경
다음 명령을 사용합니다.  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> **/dev/sda**에 대해서만 이 설정 적용하는 것은 유용하지 않습니다. 순차 I/O가 I/O 패턴의 우위를 차지한 모든 데이터 디스크에 설정합니다.  

**grub.cfg**가 성공적으로 다시 빌드되었으며 기본 스케줄러가 NOOP로 업데이트되었음을 나타내는 다음 출력이 표시됩니다.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Red Hat 배포 패밀리의 경우 다음 명령만 필요합니다.   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>소프트웨어 RAID를 사용하여 높은 I/Ops 달성
워크로드가 단일 디스크에서 제공하는 것보다 많은 IOps를 필요로 하는 경우 여러 디스크의 소프트웨어 RAID 구성을 사용해야 합니다. Azure는 이미 로컬 패브릭 계층에서 디스크 복구를 수행하기 때문에 가장 높은 수준의 성능 RAID-0 스트라이프 구성을 얻게 됩니다.  Azure 환경에서 디스크를 프로비전하고 만들며 드라이브를 분할하고 서식을 지정하며 탑재하기 전에 Linux VM에 연결합니다.  Azure에서 Linux VM의 소프트웨어 RAID 설정을 구성하는 방법에 대한 자세한 내용은 **[Linux에서 소프트웨어 RAID 구성](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** 문서에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계
모든 최적화에 관련된 토론 대로 변경 사항이 가져올 영향을 측정하기 위해 각 변경 사항의 전후에 테스트를 수행해야 합니다.  최적화는 환경에서 여러 컴퓨터에 다른 결과를 발생시키는 단계별 프로세스입니다.  하나의 구성에 작동한 최적화가 다른 사용자에게는 작동하지 않을 수 있습니다.

추가 리소스에 대한 몇 가지 유용한 링크:

* [Azure Linux 에이전트 사용자 가이드](../extensions/agent-linux.md)
* [Azure Linux VM에서 MySQL 성능 최적화](classic/optimize-mysql.md)
* [Linux에서 소프트웨어 RAID 구성](configure-raid.md)
