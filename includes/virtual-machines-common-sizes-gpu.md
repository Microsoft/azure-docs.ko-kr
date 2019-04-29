---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: c2908ef5c67665b5ba48879626370f977634dc83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776336"
---
GPU 최적화 VM 크기는 단일 또는 여러 NVIDIA GPU에서 사용 가능한 특수한 가상 머신입니다. 이러한 크기는 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다. 이 문서에서는 GPU, vCPU, 데이터 디스크 및 NIC의 개수와 종류에 대한 정보를 제공합니다. 이 그룹화의 각 크기에 대해 저장소 처리량 및 네트워크 대역폭도 포함되어 있습니다. 

* **NC, NCv2, NCv3, ND 및 NDv2** 크기는 컴퓨팅 집약적 및 네트워크 집약적 애플리케이션 및 알고리즘에 대해 최적화되어 있습니다. CUDA 기반 및 OpenCL 기반 애플리케이션, 시뮬레이션, AI, 딥 러닝 등을 예로 들 수 있습니다. NCv3 시리즈는 NVIDIA의 Tesla V100 GPU를 지원하는 고성능 컴퓨팅 워크로드에 중점을 둡니다.  ND 시리즈는 심화 학습을 위한 교육 및 유추 시나리오에 중점을 둡니다. NVIDIA Tesla P40 GPU를 사용합니다.

* **NC 시리즈**는 Intel Xeon® E5-2690 v3 2.60GHz 프로세서를 포함합니다.

* **NCSv3**, **NCSv2** 및 **ND** 크기는 Intel Xeon® E5-2690 v4 2.60GHz 프로세서를 포함합니다.
                      
* **NV 및 NVv2** 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용하는 원격 시각화, 스트리밍, 게임, 인코딩 및 VDI 시나리오에 맞게 최적화되고 설계되었습니다.  이러한 VM은 NVIDIA Tesla M60 GPU의 지원을 받습니다.


## <a name="nc-series"></a>NC 시리즈

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

NC 시리즈 VM은 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) 카드로 구동됩니다. 사용자는 에너지 탐색 애플리케이션, 충돌 시뮬레이션, 광선 추적 렌더링, 딥러닝 등에 CUDA를 활용하여 데이터를 더 빠르게 처리할 수 있습니다. NC24r 구성은, 긴밀하게 결합된 병렬 컴퓨팅 워크로드용으로 최적화된 대기 시간이 짧고 처리량이 높은 네트워크 인터페이스를 제공합니다.


| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6 |56 | 340 | 1 | 12 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 24 | 48 | 2 |
| Standard_NC24 |24 |224 | 1,440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1,440 | 4 | 48 | 64 | 4 |

1 GPU = K80 카드의 절반.

*RDMA 지원

## <a name="ncv2-series"></a>NCv2 시리즈

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

NCv2 시리즈 VM은 [NVIDIA Tesla P100](https://www.nvidia.com/en-us/data-center/tesla-p100/) GPU로 구동됩니다. 이러한 GPU는 NC 시리즈보다 2배를 초과하는 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. NC24rs v2 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](../articles/azure-supportability/resource-manager-core-quotas-request.md)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

한 개의 GPU = 한 개의 P100 카드

*RDMA 지원

## <a name="ncv3-series"></a>NCv3 시리즈

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

NCv3 시리즈 VM은 [NVIDIA Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/) GPU로 구동됩니다. 이러한 GPU는 NCv2 시리즈보다 1.5배의 계산 성능을 제공할 수 있습니다. 고객은 저수지 모델링, DNA 배열, 단백질 분석, 몬테카를로 시뮬레이션 등 기존 HPC 워크로드에 이러한 업데이트된 GPU를 활용할 수 있습니다. NC24rs v3 구성은 긴밀하게 결합된 병렬 컴퓨팅 작업에 최적화된 짧은 대기 시간과 높은 처리량의 네트워크 인터페이스를 제공합니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 vCPU(코어) 할당량은 각 지역에서 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](../articles/azure-supportability/resource-manager-core-quotas-request.md)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6 |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

하나의 GPU = 하나의 V100 카드

*RDMA 지원

## <a name="ndv2-series-preview"></a>NDv2 시리즈(미리 보기)


Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

Infiniband: 지원되지 않음


NDv2 시리즈 가상 머신은 HPC, AI 및 기계 학습 워크로드의 요구 사항을 위해 설계된 GPU 제품군에 새롭게 추가된 제품입니다. 이 제품은 8개의 NVIDIA Tesla V100 NVLINK 상호 연결된 GPU와 40개의 Intel Skylake 코어 및 672GiB의 시스템 메모리로 구동됩니다. NDv2 인스턴스는 Cuda, TensorFlow, Pytorch, Caff 및 기타 프레임워크를 사용하는 HPC 및 AI 워크로드를 위한 우수한 FP32 및 FP64 성능을 제공합니다.

[미리 보기 기간에 이러한 머신에 등록하고 액세스하세요](https://aka.ms/ndv2signup).
<br>


| 크기              | vCPU | GPU              | 메모리  | NIC(최대) | 최대 디스크 크기           | 최대 데이터 디스크(각 1023GB) | 최대 네트워크 대역폭 | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVLink) | 672GiB | 8          | 임시 1344/2948XIO | 32                             | 24,000Mbps           |

## <a name="nd-series"></a>ND 시리즈

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

ND 시리즈 가상 머신은 AI 및 딥러닝 워크로드용으로 설계된 GPU 제품군에 새로 추가됩니다. 이 가상 머신은 교육 및 유추에 우수한 성능을 제공합니다. ND 인스턴스는 [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU에 의해 구동됩니다. 이 인스턴스는 단정밀도 부동 소수점 작업, Microsoft Cognitive Toolkit, TensorFlow, Caffe 및 기타 프레임워크를 활용하는 AI 워크로드에 우수한 성능을 제공합니다. ND 시리즈는 훨씬 큰 GPU 메모리 크기(24GB)도 제공하므로 더 큰 규모의 신경망 모델에도 적합합니다. NC 시리즈와 마찬가지로 ND 시리즈는 RDMA를 통한 대기 시간이 낮고 처리량이 높은 보조 네트워크 및 InfiniBand 연결과 함께 구성할 수 있으므로 여러 GPU를 사용한 대규모 교육 작업을 실행할 수 있습니다.

> [!IMPORTANT]
> 이 크기 제품군의 경우 구독의 지역당 vCPU(코어) 할당량은 처음에 0으로 설정됩니다. [사용 가능한 지역](https://azure.microsoft.com/regions/services/)에서 이 제품군에 대한 [vCPU 할당량 증가를 요청](../articles/azure-supportability/resource-manager-core-quotas-request.md)합니다.
>

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 96 | 32 | 8 |

한 개의 GPU = 한 개의 P40 카드

*RDMA 지원

## <a name="nv-series"></a>NV 시리즈

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

NV 시리즈 가상 머신은 고객이 해당 데이터 또는 시뮬레이션을 시각화할 수 있는 데스크톱 가속화 애플리케이션 및 가상 데스크톱용 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술로 구동됩니다. 사용자는 NV 인스턴스에서 그래픽 집약적인 워크플로를 시각화하여 뛰어난 그래픽 기능을 가져오고 인코딩 및 렌더링 등의 단정밀도 작업을 추가적으로 실행할 수 있습니다. 

NV 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6 |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1,440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = M60 카드 절반.

## <a name="nvv2-series-preview"></a>NVv2 시리즈(미리 보기)

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

NVv2 시리즈 가상 머신은 [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 NVIDIA GRID 기술을 기반으로 하며 Intel Broadwell CPU를 탑재하고 있습니다. 이러한 가상 머신은 고객이 데이터를 시각화하고 결과를 시뮬레이트하여 보고 CAD에서 작업하거나 콘텐츠를 렌더링 및 스트림하려고 하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다. 또한 이러한 가상 머신은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수 있습니다. NVv2 가상 머신은 Premium Storage를 지원하며 이전 NV 시리즈와 비교했을 때 두 배의 시스템 메모리(RAM)를 제공합니다.  

NVv2 인스턴스의 각 GPU에는 GRID 라이선스가 함께 제공됩니다. 이 라이선스가 있으면 NV 인스턴스를 단일 사용자를 위한 가상 워크스테이션으로 유연하게 사용하거나, 25명의 동시 사용자가 가상 애플리케이션 시나리오를 위해 VM에 연결할 수 있습니다.

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 | 가상 워크스테이션 | 가상 애플리케이션 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6 |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = M60 카드 절반.

 
