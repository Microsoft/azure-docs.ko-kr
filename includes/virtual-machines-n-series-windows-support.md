---
title: 포함 파일
description: 포함 파일
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 00661043d1ec9769adbf4119a2c9c1925dcd29fa
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186356"
---
## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla(CUDA) 드라이버

NC, NCv2, NCv3, NCasT4_v3, ND 및 NDv2 시리즈 Vm 용 CDA (NVIDIA Tesla) 드라이버 (NV 시리즈의 경우 선택 사항)는 다음 표에 나열 된 운영 체제 에서만 지원 됩니다. 드라이버 다운로드 링크는 발표 시점에 제공된 링크입니다. 최신 드라이버에 대해서는 [NVIDIA](https://www.nvidia.com/) 웹 사이트를 참조하세요.

> [!TIP]
> Windows Server VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Windows Server 2016용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.


| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션 또는 가상 응용 프로그램에 사용 되는 NV 및 NVv3 시리즈 Vm에 대 한 NVIDIA GRID 드라이버 설치 관리자를 재배포 합니다. 다음 표에 나열 된 운영 체제에만 Azure NV 시리즈 Vm에 이러한 그리드 드라이버를 설치 합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다. NVIDIA vGPU 소프트웨어 라이선스 서버를 설정할 필요는 없습니다.

Azure에서 재배포 하는 그리드 드라이버는 NC, NCv2, NCv3, ND 및 NDv2 시리즈 Vm과 같은 NV 이외의 시리즈 Vm에서는 작동 하지 않습니다.

Nvidia 확장은 항상 최신 드라이버를 설치 합니다. 이전 버전에 대 한 종속성이 있는 고객을 위해 여기에서 이전 버전에 대 한 링크를 제공 합니다.

Windows Server 2019, Windows Server 2016 및 Windows 10 (최대 빌드 2004):
- [표 11 (451.48)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [표 10.1 (442.06)](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) (.exe) 

Windows Server 2012 r 2의 경우: 
- [표 11 (451.48)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)
- [표 10.1 (442.66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (.exe)  
