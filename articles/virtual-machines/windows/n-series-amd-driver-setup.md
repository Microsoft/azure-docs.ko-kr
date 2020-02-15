---
title: Windows 용 Azure N 시리즈 AMD GPU 드라이버 설치
description: Azure에서 Windows Server 또는 Windows를 실행 하는 N 시리즈 Vm에 대 한 AMD GPU 드라이버를 설정 하는 방법
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: fdc6834f3fb5ee97f27a6397645b965863e90a6b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190533"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows를 실행 하는 N 시리즈 Vm에 AMD GPU 드라이버 설치

Windows를 실행 하는 새로운 Azure NVv4 시리즈 Vm의 GPU 기능을 활용 하려면 AMD GPU 드라이버를 설치 해야 합니다. AMD 드라이버 확장은 다음 주에 제공 될 예정입니다. 이 문서에서는 지원 되는 운영 체제, 드라이버 및 수동 설치 및 확인 단계를 제공 합니다.

기본 사양, 스토리지 용량 및 디스크 세부 정보는 [GPU Windows VM 크기](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.



## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

| OS | 드라이버 |
| -------- |------------- |
| Windows 10 EVD-빌드 1903 <br/><br/>Windows 10-빌드 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (.exe) |

## <a name="driver-installation"></a>드라이버 설치

1. 원격 데스크톱을 통해 각 NVv4 시리즈 VM에 연결 합니다.

1. 드라이버 설치 파일을 다운로드 하 고 압축을 풉니다. 폴더로 이동한 후 ' setup.exe '를 실행 하 여 Windows 운영 체제에 대해 지원 되는 드라이버를 설치 합니다.

## <a name="verify-driver-installation"></a>드라이버 설치 확인

디바이스 관리자에서 드라이버 설치를 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에서 Radeon 이러한 MI25 카드의 성공적인 구성을 보여 줍니다.
<br />
GPU 드라이버 속성을 ![](./media/n-series-amd-driver-setup/device-manager.png)

Dxdiag를 사용 하 여 비디오 RAM을 비롯 한 GPU 표시 속성을 확인할 수 있습니다. 다음 예제에서는 Azure NVv4 VM에 있는 Radeon 이러한 MI25 카드의 1/8 번째 파티션을 보여 줍니다.
<br />
GPU 드라이버 속성을 ![](./media/n-series-amd-driver-setup/dxdiag.png)
