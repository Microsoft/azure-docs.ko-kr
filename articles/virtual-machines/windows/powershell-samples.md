---
title: Azure Virtual Machine PowerShell 샘플
description: Azure Virtual Machine PowerShell 샘플
author: cynthn
ms.service: virtual-machines-windows
ms.topic: sample
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: e8489ecb9343cf70bd8523aa45f04a6bde171b98
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321755"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure Virtual Machine PowerShell 샘플

다음 표에는 Windows VM(가상 머신)을 만들고 관리하는 PowerShell 스크립트 샘플에 대한 링크가 나와 있습니다.

| 스크립트 | Description |
|---|---|
|**가상 머신 만들기**||
| [가상 머신 빠르게 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 최소한의 프롬프트를 사용하여 리소스 그룹, 가상 머신 및 모든 관련 리소스를 만듭니다.|
| [완벽히 구성된 가상 머신 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 리소스 그룹, 가상 머신 및 모든 관련 리소스를 만듭니다.|
| [고가용성 가상 머신 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 여러 가상 머신을 부하 분산된 고가용성 구성으로 만듭니다.|
| [VM 만들기 및 구성 스크립트 실행](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 가상 머신을 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 IIS를 설치합니다. |
| [VM 만들기 및 DSC 구성 실행](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 가상 머신을 만들고 Azure DSC(필요한 상태 구성) 확장을 사용하여 IIS를 설치합니다. |
| [VHD 업로드 및 VM 만들기](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | 로컬 VHD 파일을 Azure에 업로드하고, VHD에서 이미지 만든 다음, 해당 이미지에서 VM을 만듭니다. |
| [관리되는 OS 디스크에서 VM 만들기](./../scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 기존 관리되는 디스크를 OS 디스크로 연결하여 가상 머신을 만듭니다. |
| [스냅샷에서 VM 만들기](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 먼저 스냅샷에서 관리 디스크를 만든 다음, 새 관리 디스크를 OS 디스크로 연결하여 스냅샷에서 가상 머신을 만듭니다. |
|**스토리지 관리**||
| [동일하거나 다른 구독에 VHD의 관리 디스크 만들기](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 동일하거나 다른 구독에 특수화된 VHD의 관리 디스크를 OS 디스크로 만들거나 데이터 VHD의 관리 디스크를 데이터 디스크로 만듭니다.  |
| [스냅샷에서 관리 디스크 만들기](../scripts/virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 스냅샷에서 관리 디스크를 만듭니다. |
| [동일하거나 다른 구독에 관리 디스크 복사](../scripts/virtual-machines-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | 부모 관리 디스크와 동일한 지역에 있는 동일하거나 다른 구독에 관리 디스크를 복사합니다.
| [스토리지 계정에 스냅샷을 VHD로 내보내기](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 다른 지역의 스토리지 계정에 관리 스냅샷을 VHD로 내보냅니다. |
| [관리 디스크의 VHD를 스토리지 계정으로 내보내기](../scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 관리 디스크의 기본 VHD를 다른 지역의 스토리지 계정으로 내보냅니다. |
| [VHD에서 스냅샷 만들기](../scripts/virtual-machines-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | VHD에서 스냅샷을 만든 다음, 해당 스냅샷을 사용하여 동일한 여러 관리 디스크를 빠르게 만듭니다.  |
| [동일하거나 다른 구독에 스냅샷 복사](../scripts/virtual-machines-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 부모 스냅샷과 동일한 지역에 있는 동일하거나 다른 구독에 스냅샷을 복사합니다. |
|**가상 머신 보호**||
| [VM 및 해당 데이터 디스크 암호화](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Azure 키 자격 증명 모음, 암호화 키 및 서비스 주체를 만든 다음, VM을 암호화합니다. |
|**가상 머신 모니터링**||
| [Azure Monitor를 사용하여 VM 모니터링](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 가상 머신을 만들고, Azure Log Analytics 에이전트를 설치하고, VM을 Log Analytics 작업 영역에 등록합니다.  |
| [PowerShell을 사용하여 구독의 모든 VM에 대한 세부 정보 수집](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 제공된 구독에서 VM 이름, 리소스 그룹 이름, 지역, Virtual Network, 서브넷, 개인 IP 주소, OS 유형 및 VM의 공용 IP 주소가 포함된 csv를 만듭니다.
| | |
