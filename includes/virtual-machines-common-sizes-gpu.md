---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 06/11/2019
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: 8f256749c363e2900fe62bda557f7cb4caa72e3e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77179109"
---
GPU 최적화 VM 크기는 단일 또는 여러 NVIDIA GPU에서 사용 가능한 특수한 가상 머신입니다. 이러한 크기는 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다. 이 문서에서는 GPU, vCPU, 데이터 디스크 및 NIC의 개수와 종류에 대한 정보를 제공합니다. 이 그룹화의 각 크기에 대해 스토리지 처리량 및 네트워크 대역폭도 포함되어 있습니다.

* **NC, NCv2, NCv3** 크기는 계산 집약적 및 네트워크 집약적인 응용 프로그램 및 알고리즘에 최적화 되어 있습니다. CUDA 기반 및 OpenCL 기반 애플리케이션, 시뮬레이션, AI, 딥 러닝 등을 예로 들 수 있습니다. NCv3 시리즈는 NVIDIA의 Tesla V100 GPU를 지원하는 고성능 컴퓨팅 워크로드에 중점을 둡니다. NC 시리즈는 Intel Xeon E5-2690 v3 2.60 GHz v3 (Haswell) 프로세서를 사용 하 고 NCv2 시리즈 및 NCv3 시리즈 Vm은 Intel Xeon E5-2690 v4 (Broadwell) 프로세서를 사용 합니다.

* **ND 및 NDv2** ND 시리즈는 심층 학습을 위한 학습 및 유추 시나리오에 중점을 두었습니다. NVIDIA Tesla P40 GPU 및 Intel Xeon E5-2690 v4 (Broadwell) 프로세서를 사용 합니다. NDv2 시리즈는 Intel Xeon Platinum 8168 (Skylake) 프로세서를 사용 합니다.

* **NV 및 NVv3** 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하 여 원격 시각화, 스트리밍, 게임, 인코딩 및 VDI 시나리오에 맞게 최적화 되 고 설계 되었습니다.  이러한 VM은 NVIDIA Tesla M60 GPU의 지원을 받습니다.

* **NVv4** 크기는 VDI 및 원격 시각화에 맞게 최적화 되 고 설계 되었습니다. 분할 Gpu를 사용 하면 NVv4는 더 작은 GPU 리소스를 필요로 하는 워크 로드에 적합 한 크기를 제공 합니다.  이러한 Vm은 AMD Radeon 이러한 MI25 GPU에 의해 지원 됩니다.


## <a name="nc-series"></a>NC 시리즈

Premium Storage: 지원되지 않음

Premium Storage 캐싱: 지원 되지 않음

NC 시리즈 Vm은 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) 카드 및 Intel Xeon E5-2690 V3 (haswell) 프로세서를 기반으로 합니다. 사용자는 에너지 탐색 애플리케이션, 충돌 시뮬레이션, 광선 추적 렌더링, 딥러닝 등에 CUDA를 활용하여 데이터를 더 빠르게 처리할 수 있습니다. NC24r 구성은, 긴밀하게 결합된 병렬 컴퓨팅 워크로드용으로 최적화된 대기 시간이 짧고 처리량이 높은 네트워크 인터페이스를 제공합니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = K80 카드의 절반.

*RDMA 지원

## <a name="ncv2-series"></a>NCv2 시리즈

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

NCv2 시리즈 VM은 [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU로 구동됩니다. 이러한 GPU는 NC 시리즈보다 2배를 초과하는 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. Gpu 외에도 NCv2 시리즈 Vm은 Intel Xeon E5-2690 v4 (Broadwell) Cpu로 구동 됩니다.

NC24rs v2 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](https://azure.microsoft.com/regions/services/)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소 (SSD): GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | ---  | ---| --- |
| Standard_NC6s_v2 | 6 |112 | 736 | 1 | 16 | 12 | 20000/ 200 | 4 |
| Standard_NC12s_v2 | 12 |224 | 1474 | 2 | 32 | 24 | 4만/400 | 8 |
| Standard_NC24s_v2 | 24 |448 | 2948 | 4 | 64 | 32 | 8만/800 | 8 |
| Standard_NC24rs_v2* | 24 |448 | 2948 | 4 | 64 | 32 | 8만/800 | 8 |

한 개의 GPU = 한 개의 P100 카드

*RDMA 지원

## <a name="ncv3-series"></a>NCv3 시리즈

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

NCv3 시리즈 VM은 [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU로 구동됩니다. 이러한 GPU는 NCv2 시리즈보다 1.5배의 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. NC24rs v3 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다. Gpu 외에도 NCv3 시리즈 Vm은 Intel Xeon E5-2690 v4 (Broadwell) Cpu로 구동 됩니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](https://azure.microsoft.com/regions/services/)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소 (SSD): GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 | 6 |112 | 736 | 1 | 16 | 12 | 2만/200 | 4 |
| Standard_NC12s_v3 | 12 |224 | 1474 | 2 | 32 | 24 | 4만/400 | 8 |
| Standard_NC24s_v3 | 24 |448 | 2948 | 4 | 64 | 32 | 8만/800 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8만/800 | 8 |

하나의 GPU = 하나의 V100 카드

*RDMA 지원

## <a name="updated-ndv2-series-preview"></a>업데이트 된 NDv2 시리즈 (미리 보기)

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

InfiniBand: 지원 됨

NDv2 시리즈 가상 머신은 가장 까다로운 GPU 가속 AI, 기계 학습, 시뮬레이션 및 HPC 워크 로드의 요구에 맞게 설계 된 GPU 제품군에 새로 추가 된 기능입니다. 

NDv2는 8 개의 NVIDIA Tesla V100 NVLINK에 연결 된 Gpu로, 각각 32 GB의 GPU 메모리를 제공 합니다. 또한 각 NDv2 VM에는 40 비 하이퍼 스레드 Intel Xeon 플래티넘 8168 (Skylake) 코어 및 672 GiB 시스템 메모리가 있습니다. 

NDv2 인스턴스는 TensorFlow, Pytorch, Caffe, RAPIDS 및 기타와 같은 GPU 가속을 지 원하는 다양 한 AI, ML 및 분석 도구와,,, 및 기타를 활용 하는 HPC 및 AI 워크 로드에 뛰어난 성능을 제공 합니다. framework. 

NDv2는 계산에 강한 확장 (VM 당 활용 8 Gpu) 및 스케일 아웃 (여러 Vm이 함께 작동 하는 활용)에 대해 작성 되었습니다. NDv2 시리즈는 현재 HPC VM의 HB 시리즈에서 사용할 수 있는 것과 유사한 100 기가 비트 InfiniBand EDR 백 엔드 네트워킹을 지원 하 여 AI 및 ML에 대 한 분산 교육을 비롯 한 병렬 시나리오에 대 한 고성능 클러스터링을 허용 합니다. 이 백 엔드 네트워크는 NVIDIA의 NCCL2 라이브러리에서 사용 되는 프로토콜을 포함 하 여 모든 주요 InfiniBand 프로토콜을 지원 하므로 Gpu의 원활한 클러스터링이 가능 합니다.

> ND40rs_v2 VM에서 [InfiniBand을 사용 하도록 설정](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) 하는 경우 4.7-1.0.0.1 Mellanox OFED 드라이버를 사용 하세요.

> GPU 메모리가 늘어남에 따라 새 ND40rs_v2 VM은 [2 세대 vm](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) 및 마켓플레이스 이미지를 사용 해야 합니다. 

> [NDv2 virtual machine preview에 대 한 초기 액세스를 요청 하도록 등록 합니다.](https://aka.ms/AzureNDrv2Preview)

> 참고: GPU 당 16gb의 메모리를 포함 하는 ND40s_v2는 더 이상 미리 보기에 사용할 수 없으며 업데이트 된 ND40rs_v2에 의해 대체 되었습니다.
<br>

| 크기 | vCPU | 메모리: GiB | 임시 저장소 (SSD): GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 네트워크 대역폭 | 최대 NIC 수 |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 g b (NVLink) | 16 | 32 | 8만/800 | 24000 Mbps | 8 |

## <a name="nd-series"></a>ND 시리즈

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

ND 시리즈 가상 머신은 AI 및 딥러닝 워크로드용으로 설계된 GPU 제품군에 새로 추가됩니다. 이 가상 머신은 교육 및 유추에 우수한 성능을 제공합니다. ND 인스턴스는 [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) Gpu 및 Intel Xeon E5-2690 V4 (Broadwell) cpu에 의해 구동 됩니다. 이 인스턴스는 단정밀도 부동 소수점 작업, Microsoft Cognitive Toolkit, TensorFlow, Caffe 및 기타 프레임워크를 활용하는 AI 워크로드에 우수한 성능을 제공합니다. ND 시리즈는 훨씬 큰 GPU 메모리 크기(24GB)도 제공하므로 더 큰 규모의 신경망 모델에도 적합합니다. NC 시리즈와 마찬가지로 ND 시리즈는 RDMA를 통한 대기 시간이 낮고 처리량이 높은 보조 네트워크 및 InfiniBand 연결과 함께 구성할 수 있으므로 여러 GPU를 사용한 대규모 교육 작업을 실행할 수 있습니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 지역당 vCPU(코어) 할당량은 처음에 0으로 설정됩니다. [사용 가능한 지역](../articles/azure-portal/supportability/resource-manager-core-quotas-request.md)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](https://azure.microsoft.com/regions/services/)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s | 6 |112 | 736 | 1 | 24 | 12 | 2만/200 | 4 |
| Standard_ND12s | 12 |224 | 1474 | 2 | 48 | 24 | 4만/400 | 8 | 
| Standard_ND24s | 24 |448 | 2948 | 4 | 96 | 32 | 8만/800 | 8 |
| Standard_ND24rs* | 24 |448 | 2948 | 4 | 96 | 32 | 8만/800 | 8 |

한 개의 GPU = 한 개의 P40 카드

*RDMA 지원

## <a name="nv-series"></a>NV 시리즈

Premium Storage: 지원되지 않음

Premium Storage 캐싱: 지원 되지 않음

NV 시리즈 가상 머신은 고객이 해당 데이터 또는 시뮬레이션을 시각화할 수 있는 데스크톱 가속화 애플리케이션 및 가상 데스크톱용 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술로 구동됩니다. 사용자는 NV 인스턴스에서 그래픽 집약적인 워크플로를 시각화하여 뛰어난 그래픽 기능을 가져오고 인코딩 및 렌더링 등의 단정밀도 작업을 추가적으로 실행할 수 있습니다. NV 시리즈 Vm은 Intel Xeon E5-2690 v3 (Haswell) Cpu도 지원 합니다.

NV 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = M60 카드 절반.

## <a name="nvv3-series--sup1sup"></a>NVv3 시리즈 <sup>1</sup>

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

NVv3 시리즈 가상 머신은 Intel E5-2690 v4 (Broadwell) Cpu를 사용 하 여 [Nvidia Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 nvidia GRID 기술로 구동 됩니다. 이러한 가상 머신은 고객이 데이터를 시각화하고 결과를 시뮬레이트하여 보고 CAD에서 작업하거나 콘텐츠를 렌더링 및 스트림하려고 하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다. 또한 이러한 가상 머신은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수 있습니다. NVv3 virtual machines는 Premium Storage을 지원 하 고 선행 NV 시리즈와 비교할 때 시스템 메모리 (RAM)의 두 배를 제공 합니다.  

NVv3 인스턴스의 각 GPU는 그리드 라이선스로 제공 됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 캐시되지 않은 디스크 처리량: IOPS/MBps | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV12s_v3 |12 |112 |736 | 1 | 8 | 12 | 2만/200 | 4 | 1 | 25 |
| Standard_NV24s_v3 |24 |224 |1474 | 2 | 16 | 24 | 4만/400 | 8 | 2 | 50 |
| Standard_NV48s_v3 |48 |448 |2948 | 4 | 32 | 32 | 8만/800 | 8 | 4 | 100 |

1 GPU = M60 카드 절반.

<sup>1</sup> NVv3 시리즈 Vm 기능 Intel 하이퍼 스레딩 기술

## <a name="nvv4-series-preview--sup1sup"></a>NVv4 시리즈 (미리 보기) <sup>1</sup>

Premium Storage: 지원됨

Premium Storage 캐싱: 지원 됨

NVv4 시리즈 가상 머신은 [Amd Radeon 이러한 MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 및 AMD Epyc 7V12 (로마) cpu로 구동 됩니다. NVv4 시리즈 Azure는 부분 Gpu를 사용 하는 가상 머신을 소개 합니다. Gpu 가속 그래픽 응용 프로그램 및 가상 데스크톱에 대 한 적절 한 크기의 가상 컴퓨터를 선택 합니다. gpu의 1/8에서 시작 하는 GiB 프레임 버퍼는 16 GiB 프레임 버퍼를 사용 하는 전체 GPU입니다. NVv4 가상 머신은 현재 Windows 게스트 운영 체제만 지원 합니다.

[미리 보기 기간에 이러한 머신에 등록하고 액세스하세요](https://aka.ms/nvv4signup).
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 | 
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 | 



<sup>1</sup> NVv4 시리즈 VM 기능 AMD 동시 다중 스레딩 기술

