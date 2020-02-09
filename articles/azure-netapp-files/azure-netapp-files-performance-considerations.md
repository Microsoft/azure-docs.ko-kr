---
title: Azure NetApp 파일에 대한 성능 고려 사항 | Microsoft Docs
description: Azure NetApp 파일에 대한 성능 고려 사항을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454140"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Azure NetApp Files의 성능 고려 사항

합니다 [처리량 한도](azure-netapp-files-service-levels.md) 볼륨의 볼륨에 할당 된 할당량 및 선택한 수준 서비스의 조합으로 결정 됩니다. Azure NetApp 파일에 대한 성능 계획을 만들 때 몇 가지 고려 사항을 이해 해야 합니다. 

## <a name="quota-and-throughput"></a>할당량 및 처리량  

처리량 제한은 실현할 수 있는 실제 성능을 하나만 행렬식입니다.  

일반적인 저장소 성능 고려 사항을 포함 하 여 읽고 조합 전송 크기, 임의 또는 순차 패턴 및 다른 요인 제공 된 전체 성능에 도움이 됩니다.  

테스트에서 발견 하는 최대 경험적 처리량은 4, 500 초입니다.  프리미엄 저장소 계층에 70.31 TiB의 볼륨 할당량을이 수준의 성능 달성 하기 위해 충분히 높은 처리량 제한은 프로 비전 됩니다.  

할당 볼륨 70.31 TiB 초과 할당량 크기를 고려 하는 경우 추가 데이터를 저장 하는 것에 대한 볼륨에 추가 할당량을 할당할 수 있습니다. 그러나 추가 할당량 추가 실제 처리량 증가 되지 않습니다.  

참조 [NetApp Azure Files에 대한 성능 벤치 마크](azure-netapp-files-performance-benchmarks.md) 추가 정보에 대한 합니다.

## <a name="overprovisioning-the-volume-quota"></a>볼륨 할당량을 과도 프로 비전

워크 로드의 성능 처리량 제한이 바인딩된 경우에 과도 한 프로 비전 볼륨 할당량을 더 높은 처리량 수준을 설정 하 고 더 높은 성능을 얻을 수 있습니다.  

예를 들어 경우 프리미엄 저장소 계층에 있는 볼륨 데이터의 500 개의 GiB 갖지만 128 MiB/s의 처리량 요구를 설정할 수 있습니다 할당량 2 TiB에 처리량 수준을 적절 하 게 설정 되도록 (TB 당 64 MiB/s * 2 TiB = 128 MiB/s).  

일관 되 게 높은 처리량을 달성 하기 위한 볼륨에 초과 프로비저닝, 더 높은 서비스 수준을 대신 사용 하는 것이 좋습니다.  위의 예에서 절반 볼륨 할당량을 사용 하 여 동일한 처리량 한도 대신 Ultra 저장소 계층을 사용 하 여 얻을 수 있습니다 (TiB 128 MiB/초 * 1tib = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>동적으로 늘리거나 볼륨 할당량

기본적으로 임시 성능 요구 사항이, 고정된 기간에 대한 성능 요구를 증가 시 수 동적으로 늘리거나 즉각적으로 처리량 한도 조정 하려면 볼륨 할당량입니다.  다음 고려 사항을 note 합니다. 

* 볼륨 할당량을 증가 또는 IO를 일시 중지 없이 감소 시켜야 수 및 볼륨에 대한 액세스 중단 되거나 영향을 받는 되지 합니다.  

    볼륨에 대한 활성 I/O 트랜잭션 동안 할당량을 조정할 수 있습니다.  볼륨에 저장 되는 논리 데이터의 양을 아래 해당 볼륨 할당량을 줄이면 되지 있습니다 note 합니다.

* 볼륨 할당량 변경 되 면 처리량 한도에 해당 변경 내용을 거의 즉시 됩니다. 

    변경 내용을 중단 하거나 볼륨 액세스 또는 I/O 영향을 하지 않습니다.  

* 조정 볼륨 할당량 용량 풀 크기를 변경을 해야합니다.  

    동적 및 볼륨 가용성 또는 I/O 영향을 주지 않고 용량 풀 크기를 조정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [Azure NetApp 파일에 대한 성능 벤치 마크](azure-netapp-files-performance-benchmarks.md)