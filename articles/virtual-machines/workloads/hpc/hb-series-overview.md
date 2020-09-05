---
title: HB-시리즈 VM 개요-Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기에 대 한 미리 보기 지원에 대해 알아봅니다.
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
ms.openlocfilehash: 7c66af5184c4a943fd4b3185a87623112fe0d954
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691244"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-시리즈 가상 머신 개요

AMD EPYC에서 HPC (high performance compute) 응용 프로그램 성능을 최대화 하려면 메모리 위치 및 프로세스 배치를 신중 하 게 설정 해야 합니다. 아래에서는 Azure에서 HPC 응용 프로그램에 대 한 AMD EPYC 아키텍처 및 구현에 대해 간략하게 설명 합니다. "PNUMA" 라는 용어를 사용 하 여 실제 NUMA 도메인을 참조 하 고 "vNUMA"를 사용 하 여 가상화 된 NUMA 도메인을 참조 합니다.

실제로 [Hb 시리즈](../../hb-series.md) 서버는 총 64 개의 실제 코어에 대 한 2 * 32-코어 epyc 7551 cpu입니다. 이러한 64 코어는 각각 4 개의 코어 이며 "CPU 복합" (또는 "CCX") 이라고 하는 16 개의 pNUMA 도메인 (소켓 당 8)로 나뉩니다. 각 CCX에는 자체 L3 캐시가 있으며,이는 OS에 pNUMA/vNUMA 경계가 표시 되는 방법입니다. 인접 한 쌍의 CCXs는 두 개의 물리적 DRAM 채널 (HB 시리즈 서버에서 32 GB의 DRAM)에 대 한 액세스를 공유 합니다.

VM을 방해 하지 않고 Azure 하이퍼바이저가 작동할 수 있는 공간을 제공 하기 위해 실제 pNUMA 도메인 0 (첫 번째 CCX)을 예약 합니다. 그런 다음 VM에 대 한 pNUMA 도메인 1-15 (나머지 CCX 단위)을 할당 합니다. VM에 다음이 표시 됩니다.

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` VM 당 코어 수

VM 자체는 pNUMA 0이 지정 되지 않았음을 인식 하지 못합니다. VM은 vNUMA 0-14로 pNUMA 1-15을 인식 하 고 vnuma 0에서 vnuma 7 개, vnuma 1에서 8 vnuma를 사용 합니다. 비대칭 이기는 하지만 OS는 정상적으로 부팅 하 고 작동 해야 합니다. 이 가이드의 뒷부분에서는이 비대칭 NUMA 레이아웃에서 MPI 응용 프로그램을 실행 하는 최선의 방법을 설명 합니다.

게스트 VM에 기본 실리콘를 그대로 노출 하기 때문에 프로세스 고정은 HB 시리즈 Vm에서 작동 합니다. 최적의 성능과 일관성을 위해이를 고정 하는 것이 좋습니다.

다음 다이어그램에서는 Azure 하이퍼바이저 및 HB 시리즈 VM 용으로 예약 된 코어의 분리 보여 줍니다.

![Azure 하이퍼바이저 및 HB 시리즈 VM 용으로 예약 된 코어의 분리](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양                | HB-시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어                            | 60 (SMT 사용 안 함)                |
| CPU                              | AMD EPYC 7551                    |
| CPU 빈도 (비 AVX)          | ~ 2.55 g h z (단일 + 모든 코어)   |
| 메모리                           | 4gb/코어 (240 총 GB)         |
| 로컬 디스크                       | 700 G B SSD                       |
| Infiniband                       | 100 g b EDR Mellanox Connectx-3-5 |
| Network (네트워크)                          | 50 g b 이더넷 (40 Gb 사용 가능) Azure second Gen SmartNIC |

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양           |HB-시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 18000 코어 (단일 가상 머신 확장 집합에서 단일 가상 머신 확장 집합의 300 Vm (Singleementgroup = true))  |
| MPI 지원                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| 추가 프레임 워크       | 통합 통신 X,을 (를) |
| 지원 Azure Storage       | Standard 및 Premium 디스크 (최대 4 개의 디스크) |
| SRIOV RDMA에 대 한 OS 지원   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Orchestrator 지원        | CycleCloud, Batch  |

## <a name="next-steps"></a>다음 단계

- [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 및 [다중 칩 아키텍처](https://bit.ly/2GpQIMb)에 대해 자세히 알아보세요. 자세한 내용은 [AMD EPYC 프로세서에 대 한 HPC 튜닝 가이드](https://bit.ly/2T3AWZ9)를 참조 하세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항과 HPC 예제 및 결과를 읽어 보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
