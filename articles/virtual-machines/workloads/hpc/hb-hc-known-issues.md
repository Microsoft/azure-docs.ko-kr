---
title: HPC 및 GPU Vm의 알려진 문제 해결-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HPC 및 GPU VM 크기와 관련 하 여 알려진 문제를 해결 하는 방법에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d8c3a2d961cc5b6fd719b77dae07b6e46c3d8b65
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604841"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H 시리즈 및 N 시리즈 VM의 알려진 문제

이 문서에서는 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) HPC 및 GPU vm을 사용 하는 경우 최근 일반적인 문제 및 해당 솔루션을 나열 하려고 시도 합니다.

## <a name="mofed-installation-on-ubuntu"></a>Ubuntu에 설치 하는 MOFED
Ubuntu-18.04에서 Mellanox OFED는 커널 버전 및 최신 버전과의 호환성을 보여 주었습니다 `5.4.0-1039-azure #42` . 그러면 VM 부팅 시간이 약 30 분으로 증가 합니다. 이는 Mellanox OFED 버전 5.2-1.0.4.0와 5.2-2.2.0.0 모두에 대해 보고 되었습니다.
임시 솔루션은 **UbuntuServer: 18_04-gen2:18.04.202101290** marketplace 이미지를 사용 하 고 커널을 업데이트 하지 않는 것입니다.
이 문제는 최신 MOFED (TBD)로 해결 될 예정입니다.

## <a name="mpi-qp-creation-errors"></a>MPI QP 생성 오류
MPI 워크 로드를 실행 하는 경우 아래와 같은 InfiniBand QP 생성 오류가 발생 하면 VM을 다시 부팅 하 고 작업을 다시 시도 하는 것이 좋습니다. 이 문제는 나중에 수정 될 예정입니다.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

다음과 같이 문제가 관찰 될 때 최대 큐 쌍 수 값을 확인할 수 있습니다.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>HB, HC, HBv2 및 NDv2의 가속화 네트워킹

이제 RDMA 및 InfiniBand 지원 및 SR-IOV 지원 VM 크기 [Hb](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md)및 [NDv2](../../ndv2-series.md)에서 [Azure 가속화 된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) 을 사용할 수 있습니다. 이제이 기능을 통해 (최대 30gbps) Azure 이더넷 네트워크에서 대기 시간을 향상 시킬 수 있습니다. InfiniBand 네트워크를 통한 RDMA 기능과는 별개 이지만, InfiniBand를 통해 작업을 실행할 때이 기능에 대 한 일부 플랫폼 변경이 특정 MPI 구현의 동작에 영향을 줄 수 있습니다. 특히 일부 Vm의 InfiniBand 인터페이스에는 약간 다른 이름 (mlx5_1 이전 mlx5_0와 반대)이 포함 될 수 있으며,이로 인해 특히 (일반적으로 OpenMPI 및 HPC-X를 사용 하 여)를 사용 하는 경우 MPI 명령줄을 조정 해야 할 수 있습니다. 가장 간단한 솔루션은 현재 CentOS-HPC VM 이미지에서 최신 HPC-X를 사용 하거나 필요 하지 않은 경우 가속화 된 네트워킹을 사용 하지 않도록 설정할 수 있습니다.
이에 대 한 자세한 내용은이 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) 에서 관찰 된 문제를 해결 하는 방법에 대 한 지침을 제공 합니다.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>비 SR-IOV Vm에 InfiniBand 드라이버 설치

현재 H16r, H16mr 및 NC24r는 SR-IOV를 사용 하도록 설정 되어 있지 않습니다. InfiniBand stack 분기에 대 한 자세한 내용은 [여기](../../sizes-hpc.md#rdma-capable-instances)를 참조 하세요.
Sr-iov를 사용 하도록 설정 된 VM 크기에서 OFED 드라이버를 사용 하 여 InfiniBand를 구성할 수 있지만 sr-iov가 아닌 VM 크기에는 ND 드라이버가 필요 합니다. 이 IB 지원은 [CentOS, RHEL 및 Ubuntu](configure.md)에서 적절 하 게 사용할 수 있습니다.

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>H 시리즈 및 N 시리즈 Vm에서 Ubuntu로 클라우드 init를 사용 하 여 MAC 복제

Ubuntu VM 이미지에서 IB 인터페이스를 표시 하려고 할 때 알려진 문제는 알려진 문제입니다. 이는 VM을 다시 부팅 하거나 일반화 후 VM 이미지를 만들려고 할 때 발생할 수 있습니다. VM 부팅 로그에 다음과 같은 오류가 표시 될 수 있습니다.
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

이 ' Ubuntu에서 클라우드 초기화를 사용 하는 MAC 중복 "은 알려진 문제입니다. 이는 최신 커널에서 해결 될 예정입니다. 문제가 발생 하는 경우 해결 방법은 다음과 같습니다.
1) (Ubuntu 18.04) marketplace VM 이미지 배포
2) IB를 사용 하도록 설정 하는 데 필요한 소프트웨어 패키지 설치 ([지침](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Waagent를 편집 하 여 EnableRDMA = y를 변경 합니다.
4) 클라우드에서 네트워킹 사용 안 함-초기화
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) 클라우드에서 생성 된 netplan의 네트워킹 구성 파일을 편집 하 여 MAC을 제거 합니다.
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="qp0-access-restriction"></a>qp0 액세스 제한

보안 취약성을 유발할 수 있는 낮은 수준의 하드웨어 액세스를 방지 하기 위해 게스트 Vm에서는 큐 쌍 0을 액세스할 수 없습니다. 이는 일반적으로 Connectx-3 NIC의 관리와 관련 된 작업에만 영향을 주며 ibdiagnet와 같은 일부 InfiniBand 진단을 실행 하지만 최종 사용자 응용 프로그램 자체는 실행 하지 않습니다.

## <a name="dram-on-hb-series-vms"></a>HB 시리즈 Vm의 DRAM

HB-시리즈 Vm은 현재 게스트 Vm에 228 GB의 RAM만 노출할 수 있습니다. 마찬가지로 HBv2의 경우 458, HBv3 Vm의 경우 448 GB입니다. 이는 게스트 VM에 예약 된 AMD CCX의 로컬 DRAM (NUMA 도메인)에 페이지가 할당 되지 않도록 방지 하기 위해 Azure 하이퍼바이저의 알려진 제한 때문입니다.

## <a name="gss-proxy"></a>GSS 프록시

GSS 프록시의 CentOS/RHEL 7.5에는 NFS와 함께 사용 될 때 심각한 성능 및 응답성 페널티로 매니페스트 될 수 있는 알려진 버그가 있습니다. 다음을 사용 하 여 완화할 수 있습니다.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>캐시 정리

HPC 시스템에서는 다음 사용자에 게 동일한 노드가 할당 되기 전에 작업이 완료 된 후 메모리를 정리 하는 것이 유용한 경우가 많습니다. Linux에서 응용 프로그램을 실행 한 후에는 응용 프로그램을 실행 하지 않더라도 버퍼 메모리가 늘어나면 사용 가능한 메모리가 감소 하는 것을 알 수 있습니다.

![청소 전 명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-1.png)

`numactl -H`를 사용 하면 메모리가 버퍼링 되는 NUMAnode 표시 됩니다. Linux에서 사용자는 세 가지 방법으로 캐시를 정리 하 여 버퍼링 된 메모리 또는 캐시 된 메모리를 ' 무료 '로 되돌릴 수 있습니다. 루트 이거나 sudo 권한이 있어야 합니다.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![청소 후 명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>커널 경고

Linux에서 HB 시리즈 VM을 부팅할 때 다음 커널 경고 메시지를 무시할 수 있습니다. 이는 시간이 지남에 따라 주소가 지정 되는 Azure 하이퍼바이저의 알려진 제한 때문입니다.

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


## <a name="next-steps"></a>다음 단계

- [HB 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토하여 성능 및 확장성을 높일 수 있도록 워크로드를 최적으로 구성하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크 로드 예제 및 성능 결과에 대해 읽어 보세요.
- 실행 중인 HPC 워크 로드에 대 한 높은 수준의 아키텍처 보기는 [Azure의 hpc (고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조 하세요.
