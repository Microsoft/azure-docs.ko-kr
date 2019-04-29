---
title: Azure(큰 인스턴스)의 SAP HANA에 대한 재해 복구 원칙 및 준비 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA에 대한 재해 복구 원칙 및 준비
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73643cd6954932f933e200baad09e4301300aac2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794706"
---
# <a name="disaster-recovery-principles"></a>재해 복구 원칙

HANA 대규모 인스턴스는 서로 다른 Azure 지역의 HANA 대규모 인스턴스 스탬프 간에 재해 복구 기능을 제공합니다. Azure의 미국 서부 지역에서 HANA 대규모 인스턴스 단위를 배포하는 경우 미국 동부 지역의 HANA 대규모 인스턴스 단위를 재해 복구 단위로 활용할 수 있습니다. 앞서 언급한 것처럼 재해 복구는 자동으로 구성되지 않습니다. DR 지역의 다른 HANA 큰 인스턴스 단위에 대한 비용이 부과되기 때문입니다. 재해 복구 설치의 경우 강화 및 규모 확장이 둘 다 가능합니다. 

지금까지 배포된 시나리오에서는 고객이 설치된 HANA 인스턴스를 사용하는 비프로덕션 시스템을 실행하기 위해 DR 지역의 단위를 사용했습니다. 이 HANA 큰 인스턴스 단위는 프로덕션 용도로 사용되는 SKU와 동일한 SKU여야 합니다. 다음 이미지에서는 Azure 프로덕션 지역의 서버 단위와 재해 복구 지역 간의 디스크 구성을 보여줍니다.

![디스크 관점의 DR 설치 구성](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

이 개요 그래픽에서 볼 수 있듯이 두 번째 디스크 볼륨 세트를 주문해야 합니다. 대상 디스크 볼륨은 재해 복구 단위의 프로덕션 인스턴스에 대한 프로덕션 볼륨과 크기가 같습니다. 이러한 디스크 볼륨은 재해 복구 사이트의 HANA 큰 인스턴스 서버 단위와 연결됩니다. 다음 볼륨은 프로덕션 지역에서 DR 사이트로 복제됩니다.

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap 포함)

/hana/log 볼륨은 복제되지 않습니다. 해당 볼륨에서 복원하는 방식으로 SAP HANA 트랜잭션 로그가 필요하지 않기 때문입니다. 

제공되는 재해 복구 기능 중 기본 기능은 HANA 대규모 인스턴스 인프라에서 제공하는 저장소 복제 기능입니다. 저장소 쪽에서 사용되는 기능은 저장소 볼륨에 변경 사항이 발생하면 비동기 방식으로 복제되는 지속적인 변경 흐름이 아닙니다. 대신, 이러한 볼륨의 스냅숏이 정기적으로 생성된다는 사실에 의존하는 메커니즘입니다. 이미 복제된 스냅숏과 아직 복제되지 않은 새 스냅숏 간의 델타는 재해 복구 사이트를 거쳐 대상 디스크 볼륨으로 전송됩니다.  이러한 스냅숏은 볼륨에 저장되며, 재해 복구 장애 조치(failover)가 있는 경우 해당 볼륨에서 복원되어야 합니다.  

볼륨의 전체 데이터를 처음 전송하는 것은 데이터 양이 스냅숏 간의 델타보다 작아지기 전이어야 합니다. 결과적으로 DR 사이트의 볼륨에는 프로덕션 사이트에서 수행되는 모든 볼륨 스냅숏이 포함됩니다. 결과적으로 프로덕션 시스템으로 롤백하지 않고도 손실된 데이터를 복구하기 위해 해당 DR 시스템을 사용하여 이전 상태로 돌아갈 수 있습니다.

하나의 HANA 큰 인스턴스 단위에 여러 개의 독립된 SAP HANA 인스턴스가 있는 MCOD 배포의 경우 모든 SAP HANA 인스턴스에서 저장소가 DR 쪽으로 복제될 것으로 예상됩니다.

프로덕션 사이트에서 HANA 시스템 복제를 고가용성 기능으로 사용하고 DR 사이트에 저장소 기반 복제를 사용하는 경우, 기본 사이트에서 DR 인스턴스로 두 노드의 볼륨이 모두 복제됩니다. 주 및 보조 사이트 모두에서 DR에 대한 복제를 수용하려면 DR 사이트에서 추가 저장소(주 노드와 동일한 크기)를 구입해야 합니다. 



>[!NOTE]
>HANA 대규모 인스턴스 저장소 복제 기능은 저장소 스냅숏을 미러링하고 복제합니다. 이 문서의 백업 및 복구 섹션에 소개한 대로 스토리지 스냅숏을 수행하지 않는 경우 재해 복구 사이트로 복제할 수 없습니다. 재해 복구 사이트로 저장소를 복제하려면 저장소 스냅숏을 반드시 실행해야 합니다.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>재해 복구 시나리오 준비
이 시나리오에서 프로덕션 Azure 지역의 HANA 대규모 인스턴스에서 프로덕션 시스템을 실행하고 있습니다. 다음 단계의 경우 해당 HANA 시스템의 SID가 "PRD"이고, DR Azure 지역의 HANA 대규모 인스턴스에서 비프로덕션 시스템을 실행하고 있다고 가정하겠습니다. 후자의 경우 해당 SID가 "TST"라고 가정하겠습니다. 다음 이미지에서는 이 구성을 보여줍니다.

![DR 설치 시작](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

서버 인스턴스가 추가 저장소 볼륨 세트와 함께 아직 주문되지 않은 경우 Azure의 SAP HANA Service Management는 TST HANA 인스턴스를 실행하는 HANA 대규모 인스턴스 단위에 프로덕션 복제본의 대상으로 추가 볼륨 집합을 연결합니다. 이를 위해 프로덕션 HANA 인스턴스의 SID를 제공해야 합니다. Azure Service Management의 SAP HANA가 해당 볼륨의 연결을 확인하면 해당 볼륨을 HANA 큰 인스턴스 단위에 탑재해야 합니다.

![DR 설치 다음 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

다음 단계는 TST HANA 인스턴스를 실행하는 DR Azure 지역의 HANA 대규모 인스턴스 단위에 두 번째 SAP HANA 인스턴스를 설치하는 것입니다. 새로 설치된 SAP HANA 인스턴스는 SID가 동일해야 합니다. 생성된 사용자는 프로덕션 인스턴스와 UID 및 그룹 ID가 동일해야 합니다. 자세한 내용은 [백업 및 복원](hana-backup-restore.md)을 참조하세요. 설치가 성공하면 다음을 수행해야 합니다.

- [백업 및 복원](hana-backup-restore.md)에 설명된 저장소 스냅숏 준비의 2단계를 실행합니다.
- 이 단계를 실행하지 않은 경우 HANA 대규모 인스턴스 단위의 DR 단위에 대한 공개 키를 만듭니다. [백업 및 복원](hana-backup-restore.md)에 설명된 저장소 스냅숏 준비의 3단계를 참조하세요.
- 새 HANA 인스턴스를 사용하여 *HANABackupCustomerDetails.txt*를 유지 관리하고 저장소에 대한 연결이 올바르게 작동하는지 테스트합니다.  
- DR Azure 지역의 HANA 대규모 인스턴스 단위에 새로 설치된 SAP HANA 인스턴스를 중지합니다.
- 이 PRD 볼륨을 분리하고 Azure Service Management의 SAP HANA에 문의하십시오. 저장소 복제 대상으로 작동하는 동안은 액세스할 수 없으므로 볼륨을 단위에 탑재된 상태로 유지할 수 없습니다.  

![복제를 설정하기 전 DR 설치 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

운영 팀에서는 프로덕션 Azure 지역의 PRD 볼륨과 DR Azure 지역의 PRD 볼륨 간에 복제 관계를 설정합니다.

>[!IMPORTANT]
>재해 복구 사이트에서 복제된 SAP HANA 데이터베이스를 일관된 상태로 복원할 필요가 없으므로 /hana/log 볼륨은 복제되지 않습니다.

다음으로, 재해 발생 시 RTO 및 RPO를 가져오도록 저장소 스냅숏 백업 일정을 설정하거나 조정합니다. 복구 지점 목표를 최소화려면 HANA 큰 인스턴스 서비스에서 다음 복제 간격을 설정합니다.
- 결합된 스냅숏(스냅숏 형식 **hana**)을 적용하는 볼륨의 경우 15분마다 재해 복구 사이트의 동일한 저장소 볼륨 대상에 복제되도록 설정합니다.
- 트랜잭션 로그 백업 볼륨(스냅숏 형식 **logs**)의 경우 3분마다 재해 복구 사이트의 동일한 저장소 볼륨 대상에 복제되도록 설정합니다.

복구 지점 목표를 최소화하려면 다음을 설정합니다.
- **hana** 형식 스토리지 스냅숏(“7단계: 스냅샷 수행 참조”)을 30분~1시간마다 수행합니다.
- SAP HANA 트랜잭션 로그 백업을 5분마다 수행합니다.
- **logs** 형식의 스토리지 스냅숏은 5~15분마다 수행됩니다. 이 간격 주기로 약 15-25분의 RPO를 설정합니다.

이 설정을 사용하면 트랜잭션 로그 백업의 시퀀스, 저장소 스냅숏 및 HANA 트랜잭션 로그 백업 볼륨 복제와 /hana/data, /hana/shared(/usr/sap 포함)가 다음 그래픽의 데이터와 같이 표시될 수 있습니다.

 ![시간 축에서 트랜잭션 로그 백업 스냅숏과 스냅 미러 간 관계](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

재해 복구 사례에서 RPO를 향상시키려면 HANA 트랜잭션 로그 백업을 Azure의 SAP HANA(대규모 인스턴스)에서 다른 Azure 지역에 복사할 수 있습니다. 이와 같이 RPO를 더 감소하기 위해서는 다음과 같은 단계를 수행합니다.

1. HANA 트랜잭션 로그를 가능한 한 자주 /hana/logbackups에 백업합니다.
1. rsync를 사용하여 트랜잭션 로그 백업을 NFS 공유 호스팅 Azure 가상 머신에 복사합니다. VM은 Azure 프로덕션 지역과 DR 지역의 Azure 가상 네트워크에 있습니다. 프로덕션 HANA 큰 인스턴스를 Azure에 연결하는 회로에 두 Azure 가상 네트워크를 연결해야 합니다. [HANA 큰 인스턴스를 사용한 재해 복구의 네트워크 고려 사항](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) 섹션의 그래픽을 참조하세요. 
1. 해당 지역의 트랜잭션 로그 백업을 VM이 연결된 NFS에서 내보낸 저장소에 유지합니다.
1. 재해 장애 조치(failover)의 경우 /hana/logbackups 볼륨에서 찾은 트랜잭션 로그 백업을 최근에 재해 복구 사이트의 NFS 공유에 생성한 트랜잭션 로그 백업으로 보완합니다. 
1. 트랜잭션 로그 백업을 시작하여 DR 영역에 저장할 수 있는 최신 백업으로 복원합니다.

HANA 큰 인스턴스 작업에서 복제 관계 설정을 확인하고 실행 스토리지 스냅숏 백업을 시작하면 데이터 복제가 시작됩니다.

![복제를 설정하기 전 DR 설치 단계](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

복제가 진행되면서 DR Azure 지역의 PRD 볼륨에 있는 스냅숏이 복원되지 않습니다. 저장만 됩니다. 볼륨이 이러한 상태로 탑재되어 있다면 DR Azure 지역의 서버 단위에 PRD SAP HANA 인스턴스를 설치한 후에 해당 볼륨을 분리한 상태를 나타냅니다. 또한 아직 복원되지 않은 저장소 백업을 나타냅니다.

장애 조치(failover)가 있는 경우 최신 저장소 스냅숏 대신 이전 저장소 스냅숏으로 복원하도록 선택할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [재해 복구 장애 조치(failover) 프로시저](hana-failover-procedure.md)를 참조하세요.
