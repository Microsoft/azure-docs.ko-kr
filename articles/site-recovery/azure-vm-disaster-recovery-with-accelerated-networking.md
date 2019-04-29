---
title: Azure 가상 머신 장애 복구에서 가속화된 네트워킹 | Microsoft Docs
description: Azure 가상 머신 재해 복구에 대해 Azure Site Recovery에서 가속화된 네트워킹을 사용하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: c7edc7979636ced8697aa5ad724f9c6600d840bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772518"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Azure 가상 머신 장애 복구에서 가속화된 네트워킹

가속화된 네트워킹을 사용하면 VM에 대한 SR-IOV(단일 루트 I/O 가상화)를 구현할 수 있어 네트워킹 성능이 크게 향상됩니다. 이 고성능 경로는 데이터 경로에서 호스트를 우회함으로써 대기 시간, 지터 및 CPU 사용률을 줄이므로 지원되는 VM 유형에서 가장 까다로운 네트워크 워크로드에 사용합니다. 다음 그림에서는 가속 네트워킹을 사용할 때와 사용하지 않을 때 두 개의 VM 간의 통신을 보여줍니다.

![비교](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery를 사용하면 다른 Azure 지역에 장애 조치된 Azure 가상 머신에 대한 가속화된 네트워킹의 이점을 활용할 수 있습니다. 이 문서에서는 Azure Site Recovery를 사용하여 복제된 Azure 가상 머신에 대해 가속화된 네트워킹을 사용하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 이해해야 합니다.
-   Azure 가상 머신 [복제 아키텍처](azure-to-azure-architecture.md)
-   Azure 가상 머신의 [복제 설정](azure-to-azure-tutorial-enable-replication.md)
-   Azure 가상 머신 [장애 조치](azure-to-azure-tutorial-failover-failback.md)

## <a name="accelerated-networking-with-windows-vms"></a>Windows VM에서 가속화된 네트워킹

Azure Site Recovery는 원본 가상 머신에 가속화된 네트워킹이 활성화된 경우에만 복제된 가상 머신에 대한 가속화된 네트워킹 활성화를 지원합니다. 원본 가상 머신에 가속화된 네트워킹이 활성화되지 않은 경우 [여기](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)에서 Windows 가상 머신에 대해 가속화된 네트워킹을 활성화하는 방법을 알아볼 수 있습니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제
다음 분포는 즉시 Azure Gallery에서 기본으로 지원됩니다.
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>지원되는 VM 인스턴스
가속 네트워킹은 가장 일반적인 용도로 2개 이상의 vCPU가 포함된 계산 최적화 인스턴스 크기에서 지원됩니다.  지원되는 이러한 계열은 D/DSv2 및 F/Fs입니다.

하이퍼스레딩을 지원하는 인스턴스에서 가속 네트워킹은 4개 이상의 vCPU가 포함된 VM 인스턴스에서 지원됩니다. 지원되는 계열은 D/DSv3, E/ESv3, Fsv2 및 Ms/Mms입니다.

VM 인스턴스에 대한 자세한 내용은 [Windows VM 크기](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="accelerated-networking-with-linux-vms"></a>Linux VM에서 가속화된 네트워킹

Azure Site Recovery는 원본 가상 머신에 가속화된 네트워킹이 활성화된 경우에만 복제된 가상 머신에 대한 가속화된 네트워킹 활성화를 지원합니다. 원본 가상 머신에 가속화된 네트워킹이 활성화되지 않은 경우 [여기](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)에서 Linux 가상 머신에 대해 가속화된 네트워킹을 활성화하는 방법을 알아볼 수 있습니다.

### <a name="supported-operating-systems"></a>지원되는 운영 체제
다음 배포는 즉시 Azure Gallery에서 기본으로 지원됩니다.
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian “Stretch”(백포트 커널 포함)**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>지원되는 VM 인스턴스
가속 네트워킹은 가장 일반적인 용도로 2개 이상의 vCPU가 포함된 계산 최적화 인스턴스 크기에서 지원됩니다.  지원되는 이러한 계열은 D/DSv2 및 F/Fs입니다.

하이퍼스레딩을 지원하는 인스턴스에서 가속 네트워킹은 4개 이상의 vCPU가 포함된 VM 인스턴스에서 지원됩니다. 지원되는 계열은 D/DSv3, E/ESv3, Fsv2 및 Ms/Mms입니다.

VM 인스턴스에 대한 자세한 내용은 [Linux VM 크기](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>복제된 VM에 대해 가속화된 네트워킹 활성화

Azure 가상 머신에 대해 [복제를 활성화](azure-to-azure-tutorial-enable-replication.md)한 경우 Site Recovery에서 가상 머신 네트워크 인터페이스에 가속화된 네트워킹이 활성화되었는지 여부를 자동으로 감지합니다. 가속화된 네트워킹이 이미 활성화된 경우 Site Recovery는 복제된 가상 머신의 네트워크 인터페이스에서 가속화된 네트워킹을 자동으로 구성합니다.

가속화된 네트워킹의 상태는 복제된 가상 머신에 대한 **Compute 및 네트워크** 설정의 **네트워크 인터페이스** 섹션에서 확인할 수 있습니다.

![가속화된 네트워킹 설정](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

복제를 활성화한 후 원본 가상 머신에서 가속화된 네트워킹을 활성화한 경우 다음 프로세스로 복제된 가상 머신의 네트워크 인터페이스에 대해 가속화된 네트워킹을 활성화할 수 있습니다.
1. 복제된 가상 머신에 대한 **Compute 및 네트워크** 설정을 엽니다.
2. **네트워크 인터페이스** 섹션에서 네트워크 인터페이스의 이름을 클릭합니다.
3. **대상** 열 아래의 가속화된 네트워킹에 대한 드롭다운 목록에서 **사용**을 선택합니다.

![가속화된 네트워킹 사용](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

또한 Site Recovery에서 가속화된 네트워킹을 자동으로 활성화하지 않은 기존 복제된 가상 머신에 대해 위의 프로세스를 따라야 합니다.

## <a name="next-steps"></a>다음 단계
- [가속화된 네트워킹의 이점](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)에 대해 자세히 알아봅니다.
- [Windows 가상 머신](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) 및 [Linux 가상 머신](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)에 대한 가속화된 네트워킹의 제한 사항 및 제약 조건에 대해 자세히 알아봅니다.
- 애플리케이션 장애 조치(failover)를 자동화하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
