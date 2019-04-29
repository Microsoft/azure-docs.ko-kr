---
title: Batch에서 계산 집약적인 Azure VM 사용 | Microsoft Docs
description: Azure Batch 풀에서 HPC 및 GPU VM 크기를 활용하는 방법
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/17/2018
ms.author: lahugh
ms.openlocfilehash: 3974be886b57fbf685b211369094edf844d96ab6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776527"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Batch 풀에서 RDMA 또는 GPU 인스턴스 사용

특정 Batch 작업을 실행하려면 대규모 컴퓨팅을 위해 설계된 Azure VM 크기를 활용할 수 있습니다. 예를 들면 다음과 같습니다.

* 다중 인스턴스 [MPI 워크로드](batch-mpi.md)를 실행하려면 RDMA(Remote Direct Memory Access)에 대한 네트워크 인터페이스가 있는 H 시리즈 또는 기타 크기를 선택합니다. 이러한 크기는 MPI 애플리케이션을 가속화할 수 있는 노드 간 통신에 대한 InfiniBand 네트워크에 연결합니다. 

* CUDA 애플리케이션의 경우 NVIDIA Tesla GPU(그래픽 처리 디바이스) 카드를 포함하는 N 시리즈 크기를 선택합니다.

이 문서는 Batch 풀에서 Azure의 특별한 크기 중 일부를 사용하는 지침과 예제를 제공합니다. 사양 및 백그라운드는 다음을 참조하세요.

* 고성능 계산 VM 크기([Linux](../virtual-machines/linux/sizes-hpc.md), [Windows](../virtual-machines/windows/sizes-hpc.md)) 

* GPU 가능 VM 크기([Linux](../virtual-machines/linux/sizes-gpu.md), [Windows](../virtual-machines/windows/sizes-gpu.md)) 

> [!NOTE]
> 특정 VM 크기는 배치 계정을 만든 지역에서 사용하지 못할 수도 있습니다. 크기를 사용할 수 있는지를 확인하려면 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 및 [Batch 풀에 맞는 VM 크기 선택](batch-pool-vm-sizes.md)을 참조하세요.

## <a name="dependencies"></a>종속성

Batch에서 컴퓨팅 집약적인 크기의 RDMA 또는 GPU 기능은 특정 운영 체제에서만 지원됩니다. 지원되는 운영 체제 목록은 이 크기로 생성된 가상 머신에서 지원되는 목록 중 일부입니다. Batch 풀을 만드는 방식에 따라 필요 시 추가 드라이버 또는 다른 소프트웨어를 설치하거나 구성해야 할 수 있습니다. 다음 표에서는 이러한 종속성을 요약합니다. 자세한 내용은 연결된 문서를 참조하세요. Batch 풀을 구성하는 옵션은 이 문서의 뒷부분을 참조하세요.

### <a name="linux-pools---virtual-machine-configuration"></a>Linux 풀 - 가상 머신 구성

| 크기 | 기능 | 운영 체제 | 필수 소프트웨어 | 풀 설정 |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/linux/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Ubuntu 16.04 LTS 또는<br/>CentOS 기반 HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Linux RDMA 드라이버 | 노드 간 통신 사용, 동시 작업 실행 사용 안 함 |
| [NC, NCv2, NCv3, NDv2 시리즈](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla GPU(시리즈에 따라 다름) | Ubuntu 16.04 LTS 또는<br/>CentOS 7.3 또는 7.4<br/>(Azure Marketplace) | NVIDIA CUDA 또는 CUDA Toolkit 드라이버 | N/A | 
| [NV, NVv2 시리즈](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16.04 LTS 또는<br/>CentOS 7.3<br/>(Azure Marketplace) | NVIDIA GRID 드라이버 | N/A |

<sup>*</sup>RDMA 지원 N 시리즈 크기에는 NVIDIA Tesla GPU도 포함됨

### <a name="windows-pools---virtual-machine-configuration"></a>Windows 풀 - 가상 머신 구성

| 크기 | 기능 | 운영 체제 | 필수 소프트웨어 | 풀 설정 |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | RDMA | Windows Server 2016, 2012 R2 또는<br/>2012(Azure Marketplace) | Microsoft MPI 2012 R2 이상 또는<br/> Intel MPI 5<br/><br/>Windows RDMA 드라이버 | 노드 간 통신 사용, 동시 작업 실행 사용 안 함 |
| [NC, NCv2, NCv3, ND, NDv2 시리즈](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla GPU(시리즈에 따라 다름) | Windows Server 2016 또는 <br/>2012 R2(Azure Marketplace) | NVIDIA CUDA 또는 CUDA Toolkit 드라이버| N/A | 
| [NV, NVv2 시리즈](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 또는<br/>2012 R2(Azure Marketplace) | NVIDIA GRID 드라이버 | N/A |

<sup>*</sup>RDMA 지원 N 시리즈 크기에는 NVIDIA Tesla GPU도 포함됨

### <a name="windows-pools---cloud-services-configuration"></a>Windows 풀 - 클라우드 서비스 구성

> [!NOTE]
> N 시리즈 크기는 클라우드 서비스 구성을 사용하는 Batch 풀에서 지원되지 않습니다.
>

| 크기 | 기능 | 운영 체제 | 필수 소프트웨어 | 풀 설정 |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances) | RDMA | Windows Server 2016, 2012 R2, 2012 또는<br/>2008 R2(게스트 OS 제품군) | Microsoft MPI 2012 R2 이상 또는<br/>Intel MPI 5<br/><br/>Windows RDMA 드라이버 | 노드 간 통신 사용<br/> 동시 작업 실행 사용 안 함 |

## <a name="pool-configuration-options"></a>풀 구성 옵션

Batch 풀에 대해 특별한 VM 크기를 구성하려면 필수 소프트웨어 또는 드라이버를 설치하는 몇 가지 옵션이 있습니다.

* 가상 머신 구성의 풀인 경우 드라이버 및 소프트웨어가 미리 설치되어 미리 구성된 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) VM 이미지를 선택합니다. 예제: 

  * [CentOS 기반 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) - RDMA 드라이버 및 Intel MPI 5.1 포함

  * Linux 또는 Windows용 [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) - NVIDIA CUDA 드라이버 포함

  * GPU 및 RDMA 드라이버도 포함하는 Batch 컨테이너 워크로드에 대한 Linux 이미지:

    * [Azure Batch 컨테이너 풀용 CentOS(GPU 및 RDMA 드라이버 사용)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Azure Batch 컨테이너 풀용 Ubuntu Server(GPU 및 RDMA 드라이버 사용)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* 드라이버, 소프트웨어 또는 VM 크기에 필요한 기타 설정이 설치된 [사용자 지정 Windows 또는 Linux VM 이미지](batch-custom-images.md)를 만듭니다. 

* 압축된 드라이버 또는 애플리케이션 설치 관리자에서 Batch [애플리케이션 패키지](batch-application-packages.md)를 만들고 패키지를 풀 노드에 배포하고 각 노드를 만들 때 한 번 설치하도록 Batch를 구성합니다. 예를 들어 애플리케이션 패키지가 설치 관리자인 경우 자동으로 모든 풀 노드에서 앱을 설치하는 [시작 작업](batch-api-basics.md#start-task) 명령줄을 만듭니다. 워크로드가 특정 드라이버 버전에 따라 달라지는 경우 애플리케이션 패키지 및 풀 시작 작업을 사용하는 것이 좋습니다.

  > [!NOTE] 
  > 시작 태스크는 상승된(관리자) 권한으로 실행해야 하며 성공을 기다려야 합니다. 장기 실행 작업은 Batch 풀을 프로비전하는 시간이 증가합니다.
  >

* [Batch Shipyard](https://github.com/Azure/batch-shipyard)는 Azure Batch에서 컨테이너화된 워크로드를 사용하여 투명하게 작동하도록 자동으로 GPU 및 RDMA를 구성합니다. Batch Shipyard는 구성 파일에 의해 전적으로 결정됩니다. [CNTK GPU 레시피](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI)와 같은 GPU 및 RDMA의 워크로드를 사용할 수 있는 여러 가지 샘플 레시피 구성이 있습니다. 이 기능은 N 시리즈 VM에서 GPU 드라이버를 미리 구성하고 Microsoft Cognitive Toolkit 소프트웨어를 Docker 이미지로 로드합니다.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>예제: Windows NC VM 풀의 NVIDIA GPU 드라이버

Windows NC 노드의 풀에서 CUDA 애플리케이션을 실행하려면 NVDIA GPU 드라이버를 설치해야 합니다. 다음 샘플 단계에서는 애플리케이션 패키지를 사용하여 NVIDIA GPU 드라이버를 설치합니다. 워크로드가 특정 GPU 드라이버 버전에 따라 달라지는 경우 이 옵션을 선택할 수 있습니다.

1. [NVIDIA 웹 사이트](https://www.nvidia.com/Download/index.aspx)에서 Windows Server 2016에 GPU 드라이버 설치 패키지를 다운로드합니다. 예를 들면 [411.82 버전](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe)입니다. *GPUDriverSetup.exe*와 같은 짧은 이름을 사용하여 파일을 로컬에 저장합니다.
2. 패키지의 zip 파일을 만듭니다.
3. Batch 계정에 패키지를 업로드합니다. 단계는 [애플리케이션 패키지](batch-application-packages.md) 지침을 참조하세요. *GPUDriver*와 같은 애플리케이션 ID 및 *411.82*와 같은 버전을 지정합니다.
1. Batch API 또는 Azure Portal을 사용하여 원하는 수의 노드 및 규모로 가상 머신 구성에서 풀을 만듭니다. 다음 표에서는 시작 작업을 사용하여 NVIDIA GPU 드라이버를 자동으로 설치하는 샘플 설정을 보여줍니다.

| 설정 | 값 |
| ---- | ----- | 
| **이미지 형식** | Marketplace(Linux/Windows) |
| **게시자** | MicrosoftWindowsServer |
| **제안** | WindowsServer |
| **Sku** | 2016-Datacenter |
| **노드 크기** | NC6 표준 |
| **애플리케이션 패키지 참조** | GPUDriver, 버전 411.82 |
| **시작 작업 사용** | True<br>**명령 줄** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**사용자 ID** - 풀 autouser, 관리자<br/>**성공 대기** - True

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>예제: Linux NC VM 풀의 NVIDIA GPU 드라이버

Linux NC 노드의 풀에서 CUDA 애플리케이션을 실행하려면 CUDA Toolkit에서 필요한 NVIDIA Tesla GPU 드라이버를 설치해야 합니다. GPU 드라이버에서 사용자 지정 Ubuntu 16.04 LTS 이미지를 만들고 배포하는 샘플 단계는 다음과 같습니다.

1. Ubuntu 16.04 LTS를 실행하는 Azure NC 시리즈 VM을 배포합니다. 예를 들어, 미국 중남부 지역에서 VM을 만듭니다. 
2. Azure Portal, Azure 구독에 연결하는 클라이언트 컴퓨터 또는 Azure Cloud Shell을 사용하여 [NVIDIA GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-linux.md
)을 VM에 추가합니다. 또는 VM에 연결하고 [CUDA 드라이버를 수동으로 설치](../virtual-machines/linux/n-series-driver-setup.md)하는 다음 단계를 수행합니다.
3. 다음 단계에 따라 Batch에 대한 [스냅숏 및 사용자 지정 Linux VM 이미지](batch-custom-images.md)를 만듭니다.
4. NC VM을 지원하는 영역에서 Batch 계정을 만듭니다.
5. Batch API 또는 Azure Portal을 사용하여 원하는 수의 노드 및 규모로 [사용자 지정 이미지](batch-custom-images.md)를 사용하는 풀을 만듭니다. 다음 표에서는 이미지의 샘플 풀 설정을 보여줍니다.

| 설정 | 값 |
| ---- | ---- |
| **이미지 형식** | 사용자 지정 이미지 |
| **사용자 지정 이미지** | *이미지 이름* |
| **노드 에이전트 SKU** | batch.node.ubuntu 16.04 |
| **노드 크기** | NC6 표준 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>예제: Windows H16r VM 풀에서 Microsoft MPI

Azure H16r VM 노드 풀에서 Windows MPI 애플리케이션을 실행하려면 HpcVmDrivers 확장을 구성하고 [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi)를 설치해야 합니다. 필요한 드라이버와 소프트웨어와 함께 사용자 지정 Windows Server 2016 이미지를 배포하는 샘플 단계는 다음과 같습니다.

1. Windows Server 2016을 실행하는 Azure H16r VM을 배포합니다. 예를 들어, 미국 서부 지역에서 VM을 만듭니다. 
2. Azure 구독에 연결하는 클라이언트 컴퓨터에서 [Azure PowerShell 명령](../virtual-machines/windows/sizes-hpc.md#rdma-capable-instances
)을 실행하거나 Azure Cloud Shell을 사용하여 VM에 HpcVmDrivers 확장을 추가합니다. 
1. VM에 대한 원격 데스크톱 연결을 만듭니다.
1. 최신 버전의 Microsoft MPI에 대한 [설치 패키지](https://www.microsoft.com/download/details.aspx?id=57467)(MSMpiSetup.exe)를 다운로드하고 Microsoft MPI를 설치합니다.
1. 다음 단계에 따라 Batch에 대한 [스냅숏 및 사용자 지정 Windows VM 이미지](batch-custom-images.md)를 만듭니다.
1. Batch API 또는 Azure Portal을 사용하여 원하는 수의 노드 및 규모로 [사용자 지정 이미지](batch-custom-images.md)를 사용하는 풀을 만듭니다. 다음 표에서는 이미지의 샘플 풀 설정을 보여줍니다.

| 설정 | 값 |
| ---- | ---- |
| **이미지 형식** | 사용자 지정 이미지 |
| **사용자 지정 이미지** | *이미지 이름* |
| **노드 에이전트 SKU** | batch.node.windows amd64 |
| **노드 크기** | H16r Standard |
| **노드 간 통신 사용** | True |
| **노드당 최대 작업** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>예제: Linux H16r VM 풀에서 Intel MPI

Linux H 시리즈 노드 풀에서 MPI 애플리케이션을 실행하려면 한 가지 방법은 Azure Marketplace의 [CentOS 기반 7.4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74HPC?tab=Overview) 이미지를 사용하는 것입니다. Linux RDMA 드라이버 및 Intel MPI가 미리 설치됩니다. 이 이미지는 Docker 컨테이너 워크로드도 지원합니다.

Batch API 또는 Azure Portal을 사용하여 원하는 수의 노드 및 규모로 이 이미지를 사용하는 풀을 만듭니다. 다음 표에서는 샘플 풀 설정을 보여줍니다.

| 설정 | 값 |
| ---- | ---- |
| **이미지 형식** | Marketplace(Linux/Windows) |
| **게시자** | OpenLogic |
| **제안** | CentOS-HPC |
| **Sku** | 7.4 |
| **노드 크기** | H16r Standard |
| **노드 간 통신 사용** | True |
| **노드당 최대 작업** | 1 |

## <a name="next-steps"></a>다음 단계

* Azure Batch 풀에서 MPI 작업을 실행하려면 [Windows](batch-mpi.md) 또는 [Linux](https://blogs.technet.microsoft.com/windowshpc/2016/07/20/introducing-mpi-support-for-linux-on-azure-batch/) 예제를 참조하세요.

* Batch에서 GPU 워크로드의 예제는 [Batch Shipyard](https://github.com/Azure/batch-shipyard/) 레시피를 참조하세요.