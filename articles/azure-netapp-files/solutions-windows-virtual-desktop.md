---
title: Azure NetApp Files에서 Windows 가상 데스크톱 사용 Microsoft Docs
description: Azure NetApp Files를 사용 하 여 Windows 가상 데스크톱 배포에 대 한 모범 사례 지침과 샘플 청사진을 제공 합니다.
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
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: a003090fd610f2ac75895cccbf97750adbd4cfcd
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258322"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Windows 가상 데스크톱에서 Azure NetApp Files 사용의 이점 

이 문서에서는 Azure NetApp Files으로 WVD (Windows 가상 데스크톱)를 배포 하는 방법에 대 한 모범 사례 지침을 제공 합니다.

Azure NetApp Files은 Azure에서 고성능 파일 저장소 서비스입니다. 매우 큰 규모의 Windows 가상 데스크톱 배포를 지원할 수 있는 최대 45만 IOPS 및 밀리초의 대기 시간을 제공할 수 있습니다. 데이터 평면 액세스를 유지 하면서 IO를 일시 중지 하지 않고 거의 즉시 요청 시 Azure NetApp Files 볼륨의 서비스 수준을 조정 하 고 대역폭을 조정할 수 있습니다. 이 기능을 사용 하면 비용을 위해 WVD 배포 규모를 쉽게 최적화할 수 있습니다. 볼륨 성능에 영향을 주지 않고 공간 효율적이 고 특정 시점 볼륨 스냅숏을 만들 수도 있습니다. 이 기능을 사용 하면 디렉터리에서 복사본을 통해 개별 [Fslogix 사용자 프로필 컨테이너](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile) 를 롤백하거나 `~snapshot` 볼륨 되돌리기 기능을 통해 한 번에 전체 볼륨을 즉시 롤백할 수 있습니다.  데이터 손실이 나 손상 으로부터 볼륨을 보호 하기 위해 최대 255 (회전) 스냅숏을 사용할 경우 관리자는 수행 된 작업을 실행 취소할 가능성이 많습니다.

## <a name="sample-blueprints"></a>샘플 청사진

다음 샘플 청사진에서는 Azure NetApp Files와 Windows 가상 데스크톱을 통합 하는 방법을 보여 줍니다. 풀링된 데스크톱 시나리오에서 사용자는 [다중 세션 가상 컴퓨터](https://docs.microsoft.com/azure/virtual-desktop/windows-10-multisession-faq#what-is-windows-10-enterprise-multi-session)를 사용 하 여 풀의 가장 적합 한 세션 ( [너비 우선 모드](https://docs.microsoft.com/azure/virtual-desktop/host-pool-load-balancing#breadth-first-load-balancing-method)) 호스트로 이동 됩니다. 반면에 개인 데스크톱은 각 사용자가 자신의 가상 컴퓨터를 보유 하는 시나리오용으로 예약 되어 있습니다.

### <a name="pooled-desktop-scenario"></a>풀링된 데스크톱 시나리오

풀링된 시나리오의 경우 Windows 가상 데스크톱 팀은 사용자 수를 vCPU로 사용 하 여 다음 지침을 [권장](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) 합니다. 이 권장 사항에는 가상 머신 크기가 지정 되지 않습니다.

|     워크로드 유형     |     밝음    |     보통    |     Heavy    |
|-----------------------|--------------|---------------|--------------|
|     VCPU 당 사용자 수    |     6        |     4         |     2        |


이 권장 사항은 500-사용자 Loginvsi 등과 테스트에 의해 확인 되며, 각 D16as_V4 가상 컴퓨터에 약 62 "knowledge/medium 사용자"가 기록 됩니다. 

예를 들어 D16as_V4 가상 머신 당 62 명의 사용자는 Azure NetApp Files 환경 당 6만 사용자를 쉽게 지원할 수 있습니다. D32as_v4 가상 컴퓨터의 상한을 평가 하는 테스트는 진행 중입니다. VCPU 권장 구성 당 WVD 사용자가 D32as_v4에 대해 true로 설정 된 경우 다음 그림에 표시 된 것 처럼 [1000 IP VNet 제한을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)broaching 하기 전에 12만 명 이상의 사용자가 1000 가상 컴퓨터 내에 적합 합니다.  

![Windows 가상 데스크톱 풀링된 데스크톱 시나리오](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>개인용 데스크톱 시나리오 

개인용 데스크톱 시나리오에서 다음 그림은 범용 아키텍처 권장 사항을 보여 줍니다. 사용자는 특정 데스크톱 pod에 매핑되고 각 pod는 1000 가상 컴퓨터에만 적용 되며, IP 주소에 대 한 공간은 관리 VNet에서 전파 됩니다. Azure NetApp Files는 단일 세션 호스트 풀 VNet 당 900 + 개인 데스크톱을 쉽게 처리할 수 있으며, 1000와 동일한 가상 컴퓨터의 실제 수는 허브 VNet에서 발견 된 관리 호스트 수를 뺀 값입니다. 더 많은 개인 데스크톱이 필요한 경우 다음 그림과 같이 추가 pod (호스트 풀 및 가상 네트워크)를 추가 하는 것이 쉽습니다. 

![Windows 가상 데스크톱 개인 데스크톱 시나리오](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

이와 같은 POD 기반 아키텍처를 빌드하는 경우 로그인 시 올바른 POD에 사용자를 할당 하는 것은 사용자가 항상 사용자 프로필을 찾을 수 있다는 것입니다. 

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)
