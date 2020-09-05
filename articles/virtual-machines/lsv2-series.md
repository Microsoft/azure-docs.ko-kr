---
title: Lsv2 시리즈-Azure Virtual Machines
description: Lsv2 시리즈 Vm에 대 한 사양입니다.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 78d707c0b5afd745ae805c9513243f3791d47c60
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654755"
---
# <a name="lsv2-series"></a>Lsv2 시리즈

Lsv2 시리즈는 처리량이 많고, 대기 시간이 짧으며, 모든 코어 부스트가 2.55GHz이고 최대 부스트가 3.0GHz인 [AMD EPYC<sup>TM</sup> 7551 프로세서](https://www.amd.com/en/products/epyc-7000-series)에서 실행되는 직접 매핑된 로컬 NVMe 스토리지가 특징입니다. Lsv2 시리즈 VM은 동시 다중 스레딩 구성을 통해 8~80vCPU 크기로 제공됩니다.  vCPU당 8GiB 메모리, 8vCPU당 1.92TB NVMe SSD M.2 디바이스 한 대가 있으며, L80s v2에서 최대 19.2TB(10x1.92TB)를 사용할 수 있습니다.

> [!NOTE]
> Lsv2 시리즈 Vm은 내구성이 있는 데이터 디스크를 사용 하는 대신 VM에 직접 연결 된 노드에서 로컬 디스크를 사용 하도록 최적화 되어 있습니다. 이렇게 하면 워크로드에 대한 IOPS/처리량이 향상됩니다. Lsv2 및 Ls 시리즈는 영구 데이터 디스크에서 달성할 수 있는 IOPs를 늘리기 위해 로컬 캐시 생성을 지원 하지 않습니다.
>
> 로컬 디스크의 높은 처리량 및 IOPs를 통해 Lsv2 시리즈 Vm은 단일 VM의 장애가 발생 하는 경우 지 속성을 얻기 위해 여러 Vm 간에 데이터를 복제 하는 Apache Cassandra 및 MongoDB와 같은 NoSQL 저장소에 적합 합니다.
>
> 자세히 알아보려면 [Windows](../virtual-machines/windows/storage-performance.md) 또는 [Linux](../virtual-machines/linux/storage-performance.md)용 Lsv2 시리즈 가상 머신에서 성능 최적화를 참조 하세요.  

ACU: 150-175

버스트: 지원 됨

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 되지 않음

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

| 크기 | vCPU | 메모리(GiB) | 임시 디스크<sup>1</sup>(GiB) | NVMe 디스크<sup>2</sup> | NVMe 디스크 처리량<sup>3</sup> (읽기 IOPS/MBps) | 캐시 되지 않은 데이터 디스크 처리량 (IOPs/MBps)<sup>4</sup> | 최대 버스트 캐시 되지 않은 데이터 디스크 처리량 (IOPs/MBps)<sup>5</sup>| 최대 데이터 디스크 수 | 최대 NIC 수 | 예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92TB  | 1.5 m/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2.2 m/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92TB  | 2.9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10x1.92TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> Lsv2 시리즈 VM에는 OS 페이징/스왑 파일용 표준 SCSI 기반 임시 리소스 디스크가 있습니다(Windows의 D:, Linux의 /dev/sdb). 이 디스크는 모든 8 vCPU에 대해 80GiB 스토리지, 4,000 IOPS 및 80MBps 전송 속도를 제공합니다(예: Standard_L80s_v2는 40,000 IOPS 및 800MBPS에서 800GiB 제공). 이 경우 NVMe 드라이브는 완전히 애플리케이션 전용일 수 있습니다. 이는 임시 디스크이며 중지/할당 취소 시 모든 데이터가 손실됩니다.

<sup>2</sup> 로컬 NVMe 디스크는 임시 디스크이며 VM을 중지/할당 취소할 경우 이러한 디스크의 데이터가 손실됩니다.

<sup>3</sup> Hyper-V NVMe Direct 기술은 게스트 VM 공간에 안전하게 매핑된 로컬 NVMe 드라이브에 대한 무제한 액세스를 제공합니다.  최상의 성능을 위해 Azure Marketplace에서 최신 WS2019 빌드나 Ubuntu 18.04 또는 16.04를 사용해야 합니다.  쓰기 성능은 IO 크기, 드라이브 로드 및 용량 사용률에 따라 달라집니다.

<sup>4</sup> Lsv2 시리즈 VM은 Lsv2 워크로드에 도움이 되지 않으므로 데이터 디스크에 대한 호스트 캐시를 제공하지 않습니다.

<sup>5</sup> Lsv2 시리즈 vm은 한 번에 최대 30 분 동안 디스크 성능을 [버스트](linux/disk-bursting.md) 할 수 있습니다. 

<sup>6</sup> 64 개 이상의 vcpus가 포함 된 vm에는 다음과 같은 지원 되는 게스트 운영 체제 중 하나가 필요 합니다.

- Windows Server 2016 이상
- Ubuntu 16.04 LTS 이상, Azure에서 조정 된 커널 (4.15 kernel 이상)
- SLES 12 SP2 이상
- Microsoft에서 제공 하는 LIS package 4.3.1 이상이 설치 된 RHEL 또는 CentOS 버전 6.7 ~ 6.10
- RHEL 또는 CentOS 버전 7.3, Microsoft에서 제공 하는 LIS 패키지 4.2.1 이상 설치 됨
- RHEL 또는 CentOS 버전 7.6 이상
- UEK4 이상을 사용 하 여 Oracle Linux
- Debian 9 (backports 커널, Debian 10 이상)
- 4.14 커널을 CoreOS 이상

## <a name="size-table-definitions"></a>크기 테이블 정의

- 스토리지 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB(1000^3바이트) 단위로 측정된 디스크를 GiB(1024^3바이트) 단위로 측정된 디스크와 비교할 때는 GiB 단위의 용량 수치가 더 작게 표시될 수 있음에 유의해야 합니다. 예를 들어 1023GiB = 1098.4GB입니다.
- 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
- VM에서 최상의 성능을 얻으려면 데이터 디스크 수를 vCPU 당 2개의 디스크로 제한해야 합니다.
- **예상 네트워크 대역폭**은 모든 NIC에서 모든 대상에 대해 [VM 유형별로 할당된 최대 집계 대역폭](../virtual-network/virtual-machine-network-throughput.md)입니다. 상한값은 보장되지 않지만 원하는 애플리케이션에 적합한 VM 유형을 선택하기 위한 지침을 제공하기 위한 것입니다. 실제 네트워크 성능은 네트워크 정체, 애플리케이션 부하 및 네트워크 설정을 포함한 다양한 요인에 따라 달라집니다. 네트워크 처리량 최적화에 대한 내용은 [Windows 및 Linux에 대한 네트워크 처리량 최적화](../virtual-network/virtual-network-optimize-network-bandwidth.md)를 참조하세요. Linux 또는 Windows에 대한 예상 네트워크 성능을 달성하려면 특정 버전을 선택하거나 VM을 최적화해야 할 수도 있습니다. 자세한 내용은 [가상 머신 처리량을 안정적으로 테스트하는 방법](../virtual-network/virtual-network-bandwidth-testing.md)을 참조하세요.


## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대 한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.