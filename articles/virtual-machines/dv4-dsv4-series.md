---
title: Dv4 및 Dsv4 시리즈-Azure Virtual Machines
description: Dv4 및 Dsv4 시리즈 Vm에 대 한 사양입니다.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: df78d11ce19c1d1fbbb04d2cd1718be74401cd83
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182159"
---
# <a name="dv4-and-dsv4-series"></a>Dv4 및 Dsv4 시리즈

Dv4 및 Dsv4 시리즈는 &reg; &reg; 하이퍼 스레드 구성의 Intel Xeon Platinum 8272CL Cl (캐스케이드 Lake) 프로세서에서 실행 되므로 대부분의 범용 작업에 대해 더 나은 가치를 제공 합니다. 3.4 GHz의 모든 코어 터보 클록 속도를 제공 합니다. 

> [!NOTE]
> 질문과 대답은  [로컬 임시 디스크가 없는 AZURE VM 크기](azure-vms-no-temp-disk.md)를 참조 하세요.
## <a name="dv4-series"></a>Dv4 시리즈

Dv4 시리즈 크기는 Intel &reg; Xeon &reg; Platinum 8272CL (캐스케이드 Lake)에서 실행 됩니다. Dv4 시리즈 크기는 대부분의 프로덕션 워크 로드에 대 한 vCPU, 메모리 및 원격 저장소 옵션의 조합을 제공 합니다. Dv4 시리즈 Vm은 [Intel &reg; 하이퍼 스레딩 기술을](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)특징으로 합니다.

원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다. Premium storage 디스크를 사용 하려면 Dsv4 크기를 사용 합니다. Dsv4 크기에 대 한 가격 책정 및 요금 청구 기준은 Dv4 시리즈와 동일 합니다.

ACU: 195-210

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | 원격 저장소만 | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | 원격 저장소만 | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | 원격 저장소만 | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | 원격 저장소만 | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | 원격 저장소만 | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | 원격 저장소만 | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | 원격 저장소만 | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4 시리즈

Dsv4 시리즈 크기는 Intel &reg; Xeon &reg; Platinum 8272CL (캐스케이드 Lake)에서 실행 됩니다. Dv4 시리즈 크기는 대부분의 프로덕션 워크 로드에 대 한 vCPU, 메모리 및 원격 저장소 옵션의 조합을 제공 합니다. Dsv4 시리즈 Vm은 [Intel &reg; 하이퍼 스레딩 기술을](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html)특징으로 합니다. 원격 데이터 디스크 스토리지는 가상 머신과 별도로 비용이 청구됩니다.

ACU: 195-210

Premium Storage:  지원됨

Premium Storage 캐싱:  지원됨

실시간 마이그레이션: 지원됨

메모리 보존 업데이트: 지원됨

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 데이터 디스크 수 | 캐시되지 않은 최대 디스크 처리량: IOPS/MBps | 최대 NIC 수|예상 네트워크 대역폭 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | 원격 저장소만 | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | 원격 저장소만 | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | 원격 저장소만 | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | 원격 저장소만 | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | 원격 저장소만 | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | 원격 저장소만 | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | 원격 저장소만 | 32 | 80000/1200 | 8|30000 |
