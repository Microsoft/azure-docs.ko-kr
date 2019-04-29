---
title: Azure Site Recovery를 사용한 Hyper-V와 보조 온-프레미스 사이트 간 재해 복구를 위한 아키텍처 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 온-프레미스 Hyper-V VM과 보조 System Center VMM 사이트 간 재해 복구를 위한 아키텍처를 간략하게 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: 553ecefc9f7588de0ee9056f2b2385f9a0ede50f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772118"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>아키텍처 - 보조 사이트로 Hyper-V 복제

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 System Center VMM(Virtual Machine Manager) 클라우드의 온-프레미스 Hyper-V VM(가상 머신)을 보조 VMM 사이트로 복제할 때 사용되는 구성 요소 및 프로세스를 설명합니다.


## <a name="architectural-components"></a>아키텍처 구성 요소

다음 표와 그림은 보조 사이트로 Hyper-V 복제에 사용되는 구성 요소를 개략적으로 보여 줍니다.

**구성 요소** | **요구 사항** | **세부 정보**
--- | --- | ---
**Azure** | Azure 구독 | VMM 위치 간에 복제를 오케스트레이션 및 관리할 수 있도록 Azure 구독에서 Recovery Services 자격 증명 모음을 만듭니다.
**VMM 서버** | VMM 기본 및 보조 위치가 필요합니다. | VMM 서버는 기본 사이트에서 1개, 보조 사이트에서 1개를 사용하는 것이 좋습니다.
**Hyper-V 서버** |  기본 및 보조 VMM 클라우드에 있는 하나 이상의 Hyper-V 호스트 서버. | Kerberos 또는 인증서 인증을 사용하여 LAN 또는 VPN을 통해 기본 및 보조 Hyper-V 호스트 서버 간에 데이터가 복제됩니다.  
**Hyper-V VM** | Hyper-V 호스트 서버에 있습니다. | 원본 호스트 서버에는 복제하려는 VM이 하나 이상 있어야 합니다.

**온-프레미스 간 아키텍처**

![온-프레미스 간](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>복제 프로세스

1. 초기 복제가 트리거되면 [Hyper-V VM 스냅숏](https://technet.microsoft.com/library/dd560637.aspx)이 생성됩니다.
2. VM의 가상 하드 디스크는 보조 위치에 하나씩 복제됩니다.
3. 초기 복제 진행 중에 디스크가 변경될 경우, Hyper-V 복제 로그(.hrl)로 Hyper-V 복제본 복제 추적자가 이러한 변경 내용을 추적합니다. 이러한 로그 파일은 디스크와 동일한 폴더에 있습니다. 각 디스크에는 보조 위치로 전송되는 .hrl 파일이 연결되어 있습니다. 초기 복제 진행 중에는 스냅숏과 로그 파일이 디스크 리소스를 사용합니다.
4. 초기 복제가 완료되면 VM 스냅숏은 삭제되고 델타 복제가 시작됩니다.
5. 로그의 델타 디스크 변경 내용이 동기화되고 부모 디스크로 병합합니다.


## <a name="failover-and-failback-process"></a>장애 조치 및 장애 복구 프로세스

- 단일 컴퓨터에 장애 조치를 수행하거나 복구 계획을 만들어서 여러 컴퓨터의 장애 조치를 오케스트레이션할 수 있습니다.
- 온-프레미스 사이트 간에 계획된 또는 계획되지 않은 장애 조치(failover)를 실행할 수 있습니다. 계획된 장애 조치를 실행할 경우 데이터 손실을 방지하기 위해 원본 VM이 종료됩니다.
    - 보조 사이트로 계획되지 않은 장애 조치(failover)를 수행하는 경우 장애 조치 후에 보조 위치의 컴퓨터가 보호되지 않습니다.
    - 계획된 장애 조치를 실행했으면 장애 조치 후에 보조 위치에 있는 컴퓨터가 보호됩니다.
- 초기 장애 조치(failover)가 실행된 후 장애 조치(failover)를 커밋하여 복제본 VM에서 워크로드 액세스를 시작합니다.
- 주 위치를 다시 사용할 수 있게 되면 장애 복구(failback)할 수 있습니다.
    - 역방향 복제를 시작하여 보조 사이트에서 주 사이트로의 복제를 시작합니다. 역방향 복제는 가상 머신을 보호된 상태로 가져오지만 보조 데이터 센터는 여전히 활성 위치입니다.
    - 기본 사이트를 활성 위치로 다시 만들려면 다른 역방향 복제 후에 보조 사이트에서 기본 사이트로 계획된 장애 조치를 시작합니다.



## <a name="next-steps"></a>다음 단계


[이 자습서](hyper-v-vmm-disaster-recovery.md)를 따라 VMM 클라우드 간에 Hyper-V 복제를 사용하도록 설정합니다.
