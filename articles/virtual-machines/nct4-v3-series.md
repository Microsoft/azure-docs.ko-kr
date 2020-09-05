---
title: NCas T4 v3 시리즈
description: NCas T4 v3 시리즈 Vm에 대 한 사양입니다.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: 73d583cb371876cefc10c982dbe66f6e15bddf03
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932613"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 시리즈 

NCasT4_v3 시리즈 가상 머신은 [Nvidia Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 및 AMD Epyc 7V12 (로마) cpu로 구동 됩니다. Vm은 각각 16gb의 메모리를 포함 하는 최대 4 개의 NVIDIA T4 Gpu를 포함 하 고 있으며,이는 최대 64 비 다중 스레드 AMD EPYC 7V12 (로마) 프로세서 코어 및 440 GiB의 시스템 메모리입니다. 이러한 가상 머신은 TensorFlow, Pytorch, Caffe 및 기타 프레임 워크를 활용 하는 ML 및 AI 워크 로드를 실행 하는 데 적합 하며 NVIDIA GRID 기술을 사용 하는 그래픽 워크 로드입니다. NCasT4_v3 시리즈는 유추 워크 로드를 실행 하는 데 적합 합니다.

<br>

ACU: 230-260

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원되지 않음

메모리 보존 업데이트: 지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Windows 또는 Linux를 실행 하는 Azure NCasT4_v3 시리즈 Vm의 GPU 기능을 활용 하려면 Nvidia GPU 드라이버를 설치 해야 합니다.

Nvidia GPU 드라이버를 수동으로 설치 하려면 지원 되는 운영 체제, 드라이버, 설치 및 확인 단계에 대해 [Windows 용 N 시리즈 GPU 드라이버 설치](./windows/n-series-driver-setup.md) 를 참조 하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
