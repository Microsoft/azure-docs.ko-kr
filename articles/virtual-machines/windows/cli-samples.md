---
title: Azure CLI 샘플 Windows| Microsoft Docs
description: Azure CLI 샘플 Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8bc151089f76e3f27ababb479f5e893ca9a99365
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844060"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Windows 가상 머신에 대한 Azure CLI 샘플

다음 테이블은 Windows 가상 머신을 배포하는 Azure CLI를 사용하여 빌드된 Bash 셸에 대한 링크를 포함합니다.

| | |
|---|---|
|**가상 머신 만들기**||
| [가상 머신 만들기](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 최소한의 구성으로 Windows 가상 머신을 만듭니다. |
| [완벽히 구성된 가상 머신 만들기](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 리소스 그룹, 가상 머신 및 모든 관련된 리소스를 만듭니다.|
| [고가용성 가상 머신 만들기](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 고가용성의 부하가 분산된 구성으로 여러 가상 머신을 만듭니다. |
| [VM 만들기 및 구성 스크립트 실행](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 가상 머신을 만들고 Azure 사용자 지정 스크립트 확장을 사용하여 IIS를 설치합니다. |
| [VM 만들기 및 DSC 구성 실행](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 가상 머신을 만들고 Azure DSC(필요한 상태 구성) 확장을 사용하여 IIS를 설치합니다. |
|**저장소 관리**||
| [VHD에서 관리 디스크 만들기](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 전문화 된 VHD를 OS 디스크로 또는 데이터를 데이터 디스크로 VHD에서 관리 디스크를 만듭니다.  |
| [스냅숏에서 관리 디스크 만들기](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 스냅숏에서 관리 디스크를 만듭니다. |
| [동일하거나 다른 구독으로 관리 디스크 복사](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 부모 관리 디스크와 같은 지역에 있지만 동일하거나 다른 구독으로 관리 디스크를 복사합니다. 
| [저장소 계정에 VHD로 스냅샷 내보내기](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 다른 지역의 저장소 계정에 관리 스냅숏을 VHD로 내보냅니다. |
| [관리 디스크의 VHD를 저장소 계정으로 내보내기](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 관리 디스크의 기본 VHD를 다른 지역의 저장소 계정으로 내보냅니다. |
| [동일하거나 다른 구독으로 스냅숏 복사](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 부모 스냅숏과 같은 지역에 있지만 동일하거나 다른 구독으로 스냅숏을 복사합니다. |
|**네트워크 가상 머신**||
| [가상 머신 간의 네트워크 트래픽 보안](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 두 개의 가상 머신, 모든 관련된 리소스 및 내부 및 외부 NSG(네트워크 보안 그룹)를 만듭니다. |
|**가상 머신 보호**||
| [VM 및 데이터 디스크 암호화](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Azure Key Vault, 암호화 키 및 서비스 주체를 만든 다음 VM을 암호화합니다. |
|**가상 머신 모니터링**||
| [Azure Monitor를 사용 하 여 VM 모니터링](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 가상 머신을 만들고, Log Analytics 에이전트를 설치하고, VM을 Log Analytics 작업 영역에 등록합니다.  |
| | |
