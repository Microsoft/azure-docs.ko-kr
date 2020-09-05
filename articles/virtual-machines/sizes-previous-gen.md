---
title: Azure VM 크기-이전 세대 | Microsoft Docs
description: Azure의 가상 컴퓨터에 사용할 수 있는 이전 크기의 생성을 나열 합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 37a687a17df266fe0589c24b392c0527e001f976
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683898"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>이전 세대의 가상 머신 크기

이 섹션에서는 이전 세대의 가상 컴퓨터 크기에 대 한 정보를 제공 합니다. 이러한 크기를 계속 사용할 수 있지만 새로운 세대도 사용 가능합니다.

## <a name="f-series"></a>F 시리즈

F 시리즈는 2.4GHz Intel Xeon® E5-2673 v3(Haswell) 프로세서를 기반으로 하고, Intel Turbo Boost Technology 2.0을 채택하여 최대 3.1GHz의 클럭 속도를 달성할 수 있습니다. Dv2 시리즈의 VM과 동일한 CPU 성능입니다.  

F 시리즈 VM은 더 빠른 CPU를 요구하지만 많은 메모리나 vCPU당 임시 스토리지가 필요하지 않은 워크로드에 적합합니다.  분석, 게임 서버, 웹 서버 및 배치 처리 등의 워크로드는 F 시리즈 값을 사용하면 도움이 됩니다.

ACU: 210-250

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs 시리즈 <sup>1</sup>

Fs 시리즈는 Premium Storage 외에도 F 시리즈의 모든 기능을 제공합니다.

ACU: 210-250

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 초당 10^6바이트, GiB = 1024^3바이트

<sup>1</sup> Fs 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [고성능을 위한 디자인 (./premium-storage-performance.md)을 참조 하십시오.


## <a name="nvv2-series"></a>NVv2 시리즈

**최신 크기 권장 사항**: [NVv3 시리즈](nvv3-series.md)

NVv2 시리즈 가상 머신은 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술을 기반으로 하며 Intel Broadwell CPU를 탑재하고 있습니다. 이러한 가상 머신은 고객이 데이터를 시각화하고 결과를 시뮬레이트하여 보고 CAD에서 작업하거나 콘텐츠를 렌더링 및 스트림하려고 하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다. 또한 이러한 가상 머신은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수 있습니다. NVv2 가상 머신은 Premium Storage를 지원하며 이전 NV 시리즈와 비교했을 때 두 배의 시스템 메모리(RAM)를 제공합니다.  

NVv2 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>가상 컴퓨터 크기의 이전 세대

이 섹션에서는 가상 머신 크기의 이전 세대에 대 한 정보를 제공 합니다. 이러한 크기는 계속 지원 되지만 추가 용량을 받지 않습니다. 일반적으로 사용할 수 있는 새로운 또는 대체 크기가 있습니다. 필요에 가장 적합 한 VM 크기를 선택 하려면 [Azure에서 Linux 가상 머신 크기](./sizes.md) 를 참조 하세요.  

Linux VM의 크기를 조정 하는 방법에 대 한 자세한 내용은 [LINUX Vm 크기 조정](linux/change-vm-size.md)을 참조 하세요.  

<br>

### <a name="basic-a"></a>Basic A  

**최신 크기 권장 사항**: [Av2 시리즈](av2-series.md)

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

기본 계층 크기는 부하 분산, 크기 자동 조정 또는 메모리 집약 가상 머신이 필요하지 않은 개발 워크로드 및 기타 애플리케이션에 기본적으로 사용됩니다.

| 크기–Size\Name | vCPU | 메모리|NIC(최대)| 최대 임시 디스크 크기 | 최대 데이터 디스크(각 1023GB)| 최대 IOPS(디스크당 300) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20GB  | 1  | 1x300  |
| A1\Basic_A1 | 1 | 1.75 GB | 2 | 40GB  | 2  | 2x300  |
| A2\Basic_A2 | 2 | 3.5 GB  | 2 | 60GB  | 4  | 4x300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120GB | 8  | 8x300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240GB | 16 | 16x300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>CLI 및 PowerShell을 사용하는 Standard A0-A4

클래식 배포 모델에서는 CLI와 PowerShell 간에 일부 VM 크기 이름이 약간 다릅니다.

* Standard_A0은 ExtraSmall
* Standard_A1은 Small
* Standard_A2는 Medium
* Standard_A3은 Large
* Standard_A4는 ExtraLarge

### <a name="a-series"></a>A 시리즈  

**최신 크기 권장 사항**: [Av2 시리즈](av2-series.md)

ACU: 50-100

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(HDD) GiB | 최대 데이터 디스크 수 | 최대 데이터 디스크 처리량: IOPS | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4x500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8x500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16x500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4x500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8x500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16x500 | 4/2000 |

<sup>1</sup> A0 크기는 실제 하드웨어에서 과도하게 구독됩니다. 이 특정 크기만 다른 고객 배포가 실행 중인 워크로드의 성능에 영향을 줄 수 있습니다. 상대적인 성능은 예상 기준으로 아래에 대략적으로 나와 있으며 약 15%의 변동성이 적용됩니다.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-시리즈 - 계산 집약적 인스턴스  

**최신 크기 권장 사항**: [Av2 시리즈](av2-series.md)

ACU: 225

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

A8-A11 및 H 시리즈는 *계산 집약적 인스턴스*라고도 합니다. 이러한 크기를 실행하는 하드웨어는 고성능 컴퓨팅(HPC) 클러스터 애플리케이션, 모델링 및 시뮬레이션을 포함하는 계산 집약적 및 네트워크 집약적 애플리케이션을 위해 디자인되고 최적화되었습니다. A8-A11 시리즈는 Intel Xeon E5-2670 @ 2.6 GHZ를 사용하고 H 시리즈는 Intel Xeon E5-2667 v3 @ 3.2 GHz를 사용합니다.  

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(HDD) GiB | 최대 데이터 디스크 수 | 최대 데이터 디스크 처리량: IOPS | 최대 NIC 수|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32x500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup>MPI 애플리케이션의 경우 초단기 대기 시간 및 고대역폭을 제공하는 FDR InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.  

> [!NOTE]
> A8 ~ A11 VM은 2021년 3월에 사용 중지될 예정입니다. 자세한 내용은 [HPC 마이그레이션 가이드](https://azure.microsoft.com/resources/hpc-migration-guide/)를 참조하세요.

<br>

### <a name="d-series"></a>D 시리즈  

**최신 크기 권장 사항**: [Dav4 시리즈](dav4-dasv4-series.md), [Dv4 시리즈](dv4-dsv4-series.md) 및 [Ddv4 시리즈](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM 제품군은 다음 CPU의 하나에서 실행할 수 있습니다. 2.2 Ghz intel Xeon® e5-2660 v2, 2.4 Ghz intel Xeon® e5-2673 V3 (haswell) 또는 2.3 GHZ intel Xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D 시리즈 - 메모리에 최적화  

**최신 크기 권장 사항**: [Dav4 시리즈](dav4-dasv4-series.md), [Dv4 시리즈](dv4-dsv4-series.md) 및 [Ddv4 시리즈](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM 제품군은 다음 CPU의 하나에서 실행할 수 있습니다. 2.2 Ghz intel Xeon® e5-2660 v2, 2.4 Ghz intel Xeon® e5-2673 V3 (haswell) 또는 2.3 GHZ intel Xeon® E5-2673 V4 (Broadwell)  

<br>

## <a name="preview-dc-series"></a>미리 보기: DC 시리즈

**최신 크기 권장 사항**: [DCsv2 시리즈](dcv2-series.md)

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

DC 시리즈는 SGX 기술이 포함 된 최신 버전의 3.7 g h z Intel XEON E-2176G 프로세서를 사용 하며, Intel 터보 부스트 기술을 통해 최대 4.7 g h z까지 이동할 수 있습니다. 

| 크기          | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32(43)                                                          | 3200 /48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> DC 시리즈 Vm은 [2 세대 vm](./linux/generation-2.md#creating-a-generation-2-vm) 이며 `Gen2` 이미지만 지원 합니다.


### <a name="ds-series"></a>DS 시리즈  

**최신 크기 권장 사항**: [Dasv4 시리즈](dav4-dasv4-series.md), [Dsv4 시리즈](dv4-dsv4-series.md) 및 [Ddsv4 시리즈](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM 제품군은 다음 CPU의 하나에서 실행할 수 있습니다. 2.2 Ghz intel Xeon® e5-2660 v2, 2.4 Ghz intel Xeon® e5-2673 V3 (haswell) 또는 2.3 GHZ intel Xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS 시리즈 - 메모리에 최적화  

**최신 크기 권장 사항**: [Dasv4 시리즈](dav4-dasv4-series.md), [Dsv4 시리즈](dv4-dsv4-series.md) 및 [Ddsv4 시리즈](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 저장소 처리량: IOPS/MBps (GiB의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> DS 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다.  자세한 내용은 [고성능을 위한 디자인 (./premium-storage-performance.md)을 참조 하십시오.
<sup>2</sup> VM 제품군은 다음 CPU의 하나에서 실행할 수 있습니다. 2.2 Ghz intel Xeon® e5-2660 v2, 2.4 Ghz intel Xeon® e5-2673 V3 (haswell) 또는 2.3 GHZ intel Xeon® E5-2673 V4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls 시리즈

**최신 크기 권장 사항**: [Lsv2 시리즈](lsv2-series.md)

Ls 시리즈는 [Intel® Xeon® 프로세서 E5 v3 제품군](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)을 사용하여 최대 32개의 vCPU를 제공합니다. Ls 시리즈는 G/GS 시리즈와 CPU 성능이 동일하며 vCPU당 8GiB 메모리가 제공됩니다.

Ls 시리즈는 영구 데이터 디스크에서 달성할 수 있는 IOPS를 증가시키기 위해 로컬 캐시를 생성하도록 지원하지 않습니다. 로컬 디스크의 높은 처리량과 IOPS를 사용 하면 여러 Vm에서 데이터를 복제 하는 Apache Cassandra 및 MongoDB와 같은 NoSQL 저장소에 대해 Ls 시리즈 Vm을 사용 하 여 단일 VM의 장애가 발생 하는 경우 지 속성을 달성할 수 있습니다.

ACU: 180-240

Premium Storage:  지원됨

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리(GiB) | 임시 스토리지(GiB) | 최대 데이터 디스크 수 | 최대 임시 저장소 처리량 (IOPS/MBps) | 최대 캐시 되지 않은 디스크 처리량 (IOPS/MBps) | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Ls 시리즈 VM에서 가능한 최대 디스크 처리량은 연결된 디스크 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다. 자세한 내용은 [고성능을 위한 디자인 (./premium-storage-performance.md)을 참조 하십시오.

<sup>1</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

### <a name="gs-series"></a>GS 시리즈

**최신 크기 권장 사항**: [Easv4 시리즈](eav4-easv4-series.md), [Esv4 시리즈](ev4-esv4-series.md), [Edsv4 시리즈](edv4-edsv4-series.md) 및 [M 시리즈](m-series.md)

ACU: 180 - 240 <sup>1</sup>

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기) | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> GS 시리즈 VM에서 제공 가능한 최대 디스크 처리량(IOPS 또는 MBps)은 연결된 디스크의 수, 크기 및 스트라이핑에 따라 제한될 수 있습니다. 자세한 내용은 [고성능을 위한 디자인 (./premium-storage-performance.md)을 참조 하십시오.

<sup>2</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.

<sup>3</sup> 제한 된 코어 크기를 사용할 수 있습니다.

<br>

### <a name="g-series"></a>G 시리즈

**최신 크기 권장 사항**: [Eav4 시리즈](eav4-easv4-series.md), [Ev4 시리즈](ev4-esv4-series.md) 및 [Edv4 시리즈](edv4-edsv4-series.md) 및 [M 시리즈](m-series.md)

ACU: 180-240

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 저장소 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 Nic/예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> 인스턴스는 단일 고객 전용의 하드웨어에 격리되어 있습니다.
<br>

## <a name="nv-series"></a>NV 시리즈
**최신 크기 권장 사항**: [NVv3 시리즈](nvv3-series.md) 및 [NVv4 시리즈](nvv4-series.md)

NV 시리즈 가상 머신은 고객이 해당 데이터 또는 시뮬레이션을 시각화할 수 있는 데스크톱 가속화 애플리케이션 및 가상 데스크톱용 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술로 구동됩니다. 사용자는 NV 인스턴스에서 그래픽 집약적인 워크플로를 시각화하여 뛰어난 그래픽 기능을 가져오고 인코딩 및 렌더링 등의 단정밀도 작업을 추가적으로 실행할 수 있습니다. NV 시리즈 Vm은 Intel Xeon E5-2690 v3 (Haswell) Cpu도 지원 합니다.

NV 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = M60 카드 절반.
<br>

## <a name="other-sizes"></a>기타 크기

* [범용](sizes-general.md)
* [컴퓨팅 최적화](sizes-compute.md)
* [메모리에 최적화](sizes-memory.md)
* [Storage에 최적화](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [고성능 컴퓨팅](sizes-hpc.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.