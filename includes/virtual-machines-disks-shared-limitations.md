---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4c150d874d56e3de495b0682bee979d13304a01d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202217"
---
미리 보기 중에 공유 디스크가 설정 된 managed disks에는 다음과 같은 제한 사항이 적용 됩니다.

- 현재 premium Ssd 에서만 사용할 수 있습니다.
- 현재 미국 서 부 지역 에서만 지원 됩니다.
- 디스크를 공유 하는 모든 가상 머신은 동일한 [근접 배치 그룹](../articles/virtual-machines/windows/proximity-placement-groups.md)에 배포 되어야 합니다.
- OS 디스크가 아닌 데이터 디스크에 대해서만 사용할 수 있습니다.
- 일부 버전의 Windows Server 장애 조치 (failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치 (Failover) 클러스터링 하드웨어 요구 사항 및 저장소 옵션](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)을 참조 하세요.
- `maxShares>1`를 사용 하는 premium Ssd에는 읽기 전용 호스트 캐싱을 사용할 수 없습니다.
- 가용성 집합 및 가상 머신 확장 집합은 `FaultDomainCount` 1로 설정 된 경우에만 사용할 수 있습니다.
- Azure Backup 및 Azure Site Recovery 지원 기능을 아직 사용할 수 없습니다.

공유 디스크를 사용해 보려는 경우 [미리 보기에 등록](https://aka.ms/shareddisksignup)하세요.
