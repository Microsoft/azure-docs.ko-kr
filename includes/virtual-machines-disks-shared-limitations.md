---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/26/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edb5836ace6f7a1a0cff703b9921f6e6999de578
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944499"
---
공유 디스크를 사용 하도록 설정 하는 것은 디스크 형식의 하위 집합에만 사용할 수 있습니다. 현재는 ultra disks 및 premium Ssd만 공유 디스크를 사용 하도록 설정할 수 있습니다. 공유 디스크를 사용할 수 있는 관리 되는 각 디스크에는 디스크 유형별로 구성 된 다음과 같은 제한 사항이 적용 됩니다.

### <a name="ultra-disks"></a>Ultra disks

Ultra disks에는 공유 디스크와 상관 없이 별도의 제한 사항 목록이 있습니다. Ultra disk 제한 사항은 [Azure ultra Disks 사용](../articles/virtual-machines/disks-enable-ultra-ssd.md)을 참조 하세요.

울트라 디스크를 공유 하는 경우 다음과 같은 추가 제한 사항이 있습니다.

- 현재 Azure Resource Manager 또는 SDK 지원으로 제한 되어 있습니다. 
- 일부 버전의 Windows Server 장애 조치 (failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치 (Failover) 클러스터링 하드웨어 요구 사항 및 저장소 옵션](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)을 참조 하세요.

공유 ultra disks는 기본적으로 ultra disks를 지 원하는 모든 지역에서 사용할 수 있으며, 사용 하기 위해 액세스에 등록 하지 않아도 됩니다.

### <a name="premium-ssds"></a>프리미엄 SSD

- 현재 [지역의 하위 집합](#regional-availability)에서만 지원 됩니다.
- 현재 Azure Resource Manager 또는 SDK 지원으로 제한 되어 있습니다. 
- OS 디스크가 아닌 데이터 디스크에 대해서만 사용할 수 있습니다.
- 에서 premium Ssd에는 **읽기 전용** 호스트 캐싱을 사용할 수 없습니다 `maxShares>1` .
- 에서 premium Ssd에 대해 디스크 버스트를 사용할 수 없습니다 `maxShares>1` .
- Azure 공유 디스크를 사용 하 여 가용성 집합 및 가상 머신 확장 집합을 사용 하는 경우 공유 데이터 디스크에 대해 가상 머신 장애 도메인의 [저장소 장애 도메인 맞춤이](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) 적용 되지 않습니다.
- [PPG (근접 배치 그룹)](../articles/virtual-machines/windows/proximity-placement-groups.md)를 사용 하는 경우 디스크를 공유 하는 모든 가상 머신은 동일한 ppg의 일부 여야 합니다.
- 일부 버전의 Windows Server 장애 조치 (failover) 클러스터에서는 기본 디스크만 사용할 수 있습니다. 자세한 내용은 [장애 조치 (Failover) 클러스터링 하드웨어 요구 사항 및 저장소 옵션](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements)을 참조 하세요.
- Azure Backup 및 Azure Site Recovery 지원 기능을 아직 사용할 수 없습니다.

#### <a name="regional-availability"></a>국가별 가용성

공유 premium Ssd는 다음 지역 에서만 지원 됩니다.

- 미국 동부
- 미국 동부 2
- 미국 서부
- 미국 서부 2
- 미국 중서부
- 미국 중남부
- 미국 중북부
- 미국 중부
- 서유럽
- 북유럽
- 한국 중부
- 캐나다 중부
- 캐나다 동부
- 일본 동부
- 일본 서부
- US Gov 버지니아
- US Gov 애리조나

공유 premium Ssd를 시도 하는 경우 [액세스에 등록](https://aka.ms/AzureSharedDiskGASignUp)합니다.