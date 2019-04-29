---
title: Windows용 Azure N 시리즈 GPU 드라이버 설정 | Microsoft Docs
description: Azure에서 Windows Server 또는 Windows를 실행하는 N 시리즈 VM용 NVIDIA GPU 드라이버를 설정하는 방법입니다.
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f3950c34-9406-48ae-bcd9-c0418607b37d
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b6f0d79e773ac7b79c79e4be6206fe39928cd0a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127787"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>Windows를 실행하는 N 시리즈 VM의 NVIDIA GPU 드라이버 설치 

Windows를 실행하는 Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA GPU 드라이버를 설치해야 합니다. [NVIDIA GPU 드라이버 확장](../extensions/hpccompute-gpu-windows.md)은 N 시리즈 VM에 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [NVIDIA GPU 드라이버 확장 설명서](../extensions/hpccompute-gpu-windows.md)를 참조하세요.

GPU 드라이버를 수동으로 설치하려는 경우 이 문서는 지원되는 운영 체제, 드라이버 및 설치 및 확인 단계를 제공합니다. [Linux VM](../linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에 대한 드라이버 수동 설치 정보도 제공합니다.

기본 사양, 저장소 용량 및 디스크 세부 정보는 [GPU Windows VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요. 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>드라이버 설치

1. 각 N 시리즈 VM에 원격 데스크톱으로 연결합니다.

2. Windows 운영 체제에 지원되는 드라이버를 다운로드, 추출 및 설치합니다.

VM에서는 GRID 드라이버 설치 후 다시 시작해야 합니다. CUDA 드라이버 설치 후에는 다시 시작할 필요가 없습니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인

디바이스 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NC VM에서 Tesla K80 카드의 성공적인 구성을 보여 줍니다.

![GPU 드라이버 속성](./media/n-series-driver-setup/GPU_driver_properties.png)

GPU 디바이스 상태를 쿼리하려면 드라이버와 함께 설치된 명령줄 유틸리티인 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface)를 실행합니다.

1. 명령 프롬프트를 열고 **C:\Program Files\NVIDIA Corporation\NVSMI** 디렉터리로 변경합니다.

2. `nvidia-smi`을 실행합니다. 드라이버가 설치된 경우 다음과 유사한 출력이 표시됩니다. 현재 VM에서 GPU 작업을 실행 중이지 않으면 **GPU-Util**에 **0%** 가 표시됩니다. 드라이버 버전 및 GPU 세부 정보는 표시된 것과 다를 수 있습니다.

![NVIDIA 디바이스 상태](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA 네트워크 연결

동일한 가용성 집합 또는 가상 머신 확장 집합의 단일 배치 그룹에 배포된 NC24r과 같은 RDMA 지원 N 시리즈 VM에서 RDMA 네트워크 연결을 사용할 수 있습니다. RDMA 연결에 필요한 Windows 네트워크 디바이스 드라이버를 설치하는 HpcVmDrivers 확장을 추가해야 합니다. RDMA 지원 N 시리즈에 VM 확장을 추가하려면 Azure Resource Manager용 [Azure PowerShell](/powershell/azure/overview) cmdlet을 사용합니다.

미국 서부 하위 지역에서 이름이 myVM인 기존 RDMA 지원 VM에 최신 버전 1.1 HpcVMDrivers 확장을 설치하려면
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  자세한 내용은 [Windows용 가상 머신 확장 및 기능](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)을 참조하세요.

RDMA 네트워크는 [Microsoft MPI](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) 또는 Intel MPI 5.x를 사용하여 실행되는 애플리케이션에 대한 MPI(Message Passing Interface) 트래픽을 지원합니다. 


## <a name="next-steps"></a>다음 단계

* NVIDIA Tesla GPU에 대한 GPU 가속 애플리케이션을 빌드하는 개발자는 최신 [CUDA Toolkit](https://developer.nvidia.com/cuda-downloads)을 다운로드하여 설치할 수도 있습니다. 자세한 내용은 [CUDA 설치 가이드](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi)를 참조하세요.


