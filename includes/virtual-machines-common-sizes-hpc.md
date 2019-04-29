---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: a8f0e61a953a2e2471e49d571063f6202b7ab76d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540500"
---
Azure H 시리즈 가상 머신은 일괄 처리, 분석, 분자 모델링 및 유체 역학과 같은 워크로드 처리에 맞춘 최신 고성능 컴퓨팅 VM입니다. 이러한 8 및 16 vCPU VM은 DDR4 메모리 및 SSD 기반 임시 저장소를 제공하는 Intel Haswell E5-2667 V3 프로세서 기술을 토대로 구축되었습니다. 

H 시리즈 제품은 뛰어난 CPU 처리 능력 외에도 FDR InfiniBand 및 여러 메모리 구성을 사용하는 낮은 대기 시간 RDMA 네트워킹을 위한 다양한 옵션을 제공하여 메모리 집약적 계산 요구 사항을 지원합니다.



## <a name="h-series"></a>H 시리즈

ACU: 290-300

Premium Storage:  지원되지 않음

Premium Storage 캐싱:  지원되지 않음

| 크기 | vCPU | 메모리: GiB | 임시 저장소(SSD) GiB | 최대 데이터 디스크 수 | 최대 디스크 처리량: IOPS | 최대 NIC 수 |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> MPI 애플리케이션의 경우 초단기 대기 시간 및 고대역폭을 제공하는 FDR InfiniBand 네트워크를 통해 전용 RDMA 백 엔드 네트워크를 사용할 수 있습니다.

<br>






