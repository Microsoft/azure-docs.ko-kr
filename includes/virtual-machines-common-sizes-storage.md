---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 6c83298b102d6782647f3baebf6f98e43cb3ad7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778232"
---
스토리지 최적화 VM 크기는 높은 디스크 처리량 및 IO를 제공하며 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징 및 대형 트랜잭션 데이터베이스에 이상적입니다.  예를 들어 Cassandra, MongoDB, Cloudera 및 Redis가 있습니다. 이 문서에서는 각 최적화 크기에 대한 로컬 스토리지 처리량 및 네트워크 대역폭뿐 아니라 vCPU, 데이터 디스크 및 NIC 수에 대한 정보를 제공합니다.

Lsv2 시리즈는 처리량이 많고, 대기 시간이 짧으며, 모든 코어 부스트가 2.55GHz이고 최대 부스트가 3.0GHz인 [AMD EPYC&trade; 7551 프로세서](https://www.amd.com/en/products/epyc-7000-series)에서 실행되는 직접 매핑된 로컬 NVMe 스토리지가 특징입니다. Lsv2 시리즈 VM은 동시 다중 스레딩 구성을 통해 8~80vCPU 크기로 제공됩니다.  vCPU당 8GiB 메모리, 8vCPU당 1.92TB NVMe SSD M.2 디바이스 한 대가 있으며, L80s v2에서 최대 19.2TB(10x1.92TB)를 사용할 수 있습니다.

> [!NOTE]
> Lsv2 시리즈 Vm은 영구 데이터 디스크를 사용 하는 것이 아니라 VM에 직접 연결 된 노드에서 로컬 디스크를 사용 하도록 최적화 됩니다. 이렇게 하면 워크로드에 대한 IOPS/처리량이 향상됩니다. Ls 시리즈 고 Lsv2 영구 데이터 디스크에서 달성할 수 있는 IOPs를 높이기 위해 로컬 캐시의 생성을 지원 하지 않습니다.
>
> 높은 처리량 및 로컬 디스크의 IOPs Lsv2 및 Ls 시리즈 Vm을 하기에 이상적인 단일 VM의 오류가 발생할 경우 지 속성을 달성 하기 위해 여러 Vm 간에 데이터를 복제 하는 Apache Cassandra 및 MongoDB와 같은 NoSQL 저장소입니다.
>
> 자세한 내용은 참조 하세요 [Lsv2-시리즈 virtual machines에서 성능을 최적화](../articles/virtual-machines/linux/storage-performance.md)합니다.  


## <a name="lsv2-series"></a>Lsv2 시리즈

ACU: 150-175

Premium Storage: 지원됨

Premium Storage 캐싱: 지원되지 않음

| 크기          | vCPU | 메모리(GiB) | 임시 디스크<sup>1</sup>(GiB) | NVMe 디스크<sup>2</sup> | NVMe 디스크 처리량<sup>3</sup>(IOPS/MBps 읽기) | 최대 데이터 디스크 처리량 (IOPs/MBps) 캐시 되지 않은<sup>4</sup> | 최대 데이터 디스크 수 | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|---------------|-----------|-------------|--------------------------|----------------|---------------------------------------------------|-------------------------------------------|------------------------------|------------------------------| 
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1.92TB  | 400,000 / 2,000 | 8,000/160 | 16 | 2/3,200  | 
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1.92TB  | 800,000 / 4,000 | 16,000/320 | 32 | 4/6,400  | 
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1.92TB  | 1.5 M / 8,000    | 32,000/640 | 32 | 8/12,800 | 
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1.92TB  | 2.9 M 16,000 /   | 64,000/1,280 | 32 | 8/25,600 |
| Standard_L80s_v2  | 80 | 640 | 800 | 10x1.92TB   | 3.8 M / 20,000   | 80,000/1,400 | 32 | 8 / 32,000 |

<sup>1</sup> Lsv2 시리즈 VM에는 OS 페이징/스왑 파일용 표준 SCSI 기반 임시 리소스 디스크가 있습니다(Windows의 D:, Linux의 /dev/sdb). 이 디스크는 모든 8 vCPU에 대해 80GiB 스토리지, 4,000 IOPS 및 80MBps 전송 속도를 제공합니다(예: Standard_L80s_v2는 40,000 IOPS 및 800MBPS에서 800GiB 제공). 이 경우 NVMe 드라이브는 완전히 애플리케이션 전용일 수 있습니다. 이는 임시 디스크이며 중지/할당 취소 시 모든 데이터가 손실됩니다.

<sup>2</sup> 로컬 NVMe 디스크는 임시 디스크이며 VM을 중지/할당 취소할 경우 이러한 디스크의 데이터가 손실됩니다.

<sup>3</sup> Hyper-V NVMe Direct 기술은 게스트 VM 공간에 안전하게 매핑된 로컬 NVMe 드라이브에 대한 무제한 액세스를 제공합니다.  최상의 성능을 위해 Azure Marketplace에서 최신 WS2019 빌드나 Ubuntu 18.04 또는 16.04를 사용해야 합니다.  쓰기 성능은 IO 크기, 드라이브 로드 및 용량 사용률에 따라 달라집니다.

<sup>4</sup> Lsv2 시리즈 VM은 Lsv2 워크로드에 도움이 되지 않으므로 데이터 디스크에 대한 호스트 캐시를 제공하지 않습니다.  그러나 Lsv2 VM은 Azure의 임시 VM OS 디스크 옵션(최대 30GiB)을 수용할 수 있습니다.

## <a name="size-table-definitions"></a>크기 테이블 정의

- 저장소 용량 단위는 GiB(1024^3바이트) 단위로 표시됩니다. GB(1000^3바이트) 단위로 측정된 디스크를 GiB(1024^3바이트) 단위로 측정된 디스크와 비교할 때는 GiB 단위의 용량 수치가 더 작게 표시될 수 있음에 유의해야 합니다. 예를 들어 1023GiB = 1098.4GB입니다.
- 디스크 처리량은 IOPS(초당 입/출력 작업 수) 및 MBps로 측정되며, MBps = 10^6바이트/초입니다.
- VM에서 최상의 성능을 얻으려면 데이터 디스크 수를 vCPU 당 2개의 디스크로 제한해야 합니다.
- **예상 네트워크 대역폭**은 모든 NIC에서 모든 대상에 대해 [VM 유형별로 할당된 최대 집계 대역폭](../articles/virtual-network/virtual-machine-network-throughput.md)입니다. 상한값은 보장되지 않지만 원하는 애플리케이션에 적합한 VM 유형을 선택하기 위한 지침을 제공하기 위한 것입니다. 실제 네트워크 성능은 네트워크 정체, 애플리케이션 부하 및 네트워크 설정을 포함한 다양한 요인에 따라 달라집니다. 네트워크 처리량 최적화에 대한 내용은 [Windows 및 Linux에 대한 네트워크 처리량 최적화](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)를 참조하세요. Linux 또는 Windows에 대한 예상 네트워크 성능을 달성하려면 특정 버전을 선택하거나 VM을 최적화해야 할 수도 있습니다. 자세한 내용은 [가상 머신 처리량을 안정적으로 테스트하는 방법](../articles/virtual-network/virtual-network-bandwidth-testing.md)을 참조하세요.
