---
title: HB 시리즈 및 HC 시리즈 Vm의 알려진 문제-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기의 알려진 문제에 대해 알아봅니다.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660098"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H 시리즈 및 N 시리즈 VM의 알려진 문제

이 문서에서는 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) vm을 사용 하는 경우 가장 일반적인 문제 및 해결 방법을 제공 합니다.

## <a name="dram-on-hb-series"></a>HB 시리즈의 DRAM

HB-시리즈 Vm은 현재 게스트 Vm에 228 GB의 RAM만 노출할 수 있습니다. 이는 게스트 VM에 예약 된 AMD CCX의 로컬 DRAM (NUMA 도메인)에 페이지가 할당 되지 않도록 방지 하기 위해 Azure 하이퍼바이저의 알려진 제한 때문입니다.

## <a name="accelerated-networking"></a>가속 네트워킹

현재 Azure 가속화 된 네트워킹은 사용 하도록 설정 되지 않지만 미리 보기 기간을 진행 합니다. 이 기능이 지원 되 면 고객에 게 알립니다.

## <a name="qp0-access-restriction"></a>qp0 액세스 제한

보안 취약성을 유발할 수 있는 낮은 수준의 하드웨어 액세스를 방지 하기 위해 게스트 Vm에서는 큐 쌍 0을 액세스할 수 없습니다. 이는 일반적으로 Connectx-3 NIC의 관리와 관련 된 작업에만 영향을 주며 ibdiagnet와 같은 일부 InfiniBand 진단을 실행 하지만 최종 사용자 응용 프로그램 자체는 실행 하지 않습니다.

## <a name="gss-proxy"></a>GSS 프록시

GSS 프록시의 CentOS/RHEL 7.5에는 NFS와 함께 사용 될 때 심각한 성능 및 응답성 페널티로 매니페스트 될 수 있는 알려진 버그가 있습니다. 다음을 사용 하 여 완화할 수 있습니다.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>캐시 정리

HPC 시스템에서는 다음 사용자에 게 동일한 노드가 할당 되기 전에 작업이 완료 된 후 메모리를 정리 하는 것이 유용한 경우가 많습니다. Linux에서 응용 프로그램을 실행 한 후에는 응용 프로그램을 실행 하지 않더라도 버퍼 메모리가 늘어나면 사용 가능한 메모리가 감소 하는 것을 알 수 있습니다.

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-1.png)

`numactl -H`를 사용 하면 메모리가 버퍼링 되는 NUMAnode 표시 됩니다. Linux에서 사용자는 세 가지 방법으로 캐시를 정리 하 여 버퍼링 된 메모리 또는 캐시 된 메모리를 ' 무료 '로 되돌릴 수 있습니다. 루트 이거나 sudo 권한이 있어야 합니다.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>커널 경고

Linux에서 HB 시리즈 VM을 부팅할 때 다음과 같은 커널 경고 메시지가 표시 될 수 있습니다.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

이 경고는 무시 해도 됩니다. 이는 시간이 지남에 따라 주소가 지정 되는 Azure 하이퍼바이저의 알려진 제한 때문입니다.


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>InfiniBand 사용 N 시리즈 VM 크기에 InfiniBand 드라이버 설치

NC24r_v3 및 ND40r_v2는 sr-iov를 사용 하도록 설정 되어 있지만 NC24r 및 NC24r_v2는 SR-IOV를 사용 하도록 설정 되어 있지 않습니다. 분기에 대 한 자세한 내용은 [여기](../../sizes-hpc.md#rdma-capable-instances)를 참조 하세요.
InfiniBand (IB)는 OFED 드라이버를 사용 하 여 SR-IOV 지원 VM 크기에 구성할 수 있지만 SR-IOV VM이 아닌 VM 크기에는 ND 드라이버가 필요 합니다. 이 IB 지원은 [CentOS](configure.md)에서 적절 하 게 사용할 수 있습니다. Ubuntu의 경우 [문서](enable-infiniband.md#vm-images-with-infiniband-drivers)에 설명 된 대로 OFED 및 ND 드라이버를 모두 설치 하려면 [여기의 지침](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [HB 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토하여 성능 및 확장성을 높일 수 있도록 워크로드를 최적으로 구성하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항과 HPC 예제 및 결과를 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
