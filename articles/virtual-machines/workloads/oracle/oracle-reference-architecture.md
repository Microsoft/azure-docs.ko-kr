---
title: Azure의 Oracle 데이터베이스에 대 한 참조 아키텍처 | Microsoft Docs
description: Microsoft Azure Virtual Machines에서 Oracle Database Enterprise Edition 데이터베이스를 실행 하기 위한 아키텍처를 참조 합니다.
services: virtual-machines-linux
author: rgardler
manager: ''
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: rogardle
ms.custom: ''
ms.openlocfilehash: 8feede515cf7ed861f3219fdf5f4642a33c9e83e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690360"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Azure의 Oracle Database Enterprise Edition에 대 한 참조 아키텍처

이 가이드에서는 Azure에서 항상 사용 가능한 Oracle 데이터베이스를 배포 하는 방법에 대해 자세히 설명 합니다. 또한이 가이드에서는 재해 복구 고려 사항도 다이브. 이러한 아키텍처는 고객 배포를 기반으로 작성 되었습니다. 이 가이드는 Oracle Database Enterprise Edition에만 적용 됩니다.

Oracle 데이터베이스의 성능을 최대화 하는 방법에 대 한 자세한 내용은 [Oracle DB 설계](oracle-design.md)를 참조 하세요.

## <a name="assumptions"></a>가정

- [가용성 영역](../../../availability-zones/az-overview.md) 등 Azure의 다양 한 개념을 이해 하 고 있습니다.
- Oracle Database Enterprise Edition 12c 이상을 실행 하 고 있습니다.
- 이 문서에서 솔루션을 사용 하는 경우 라이선스의 영향을 인식 하 고 승인 합니다.

## <a name="high-availability-for-oracle-databases"></a>Oracle 데이터베이스에 대 한 고가용성

클라우드에서 고가용성을 달성 하는 것은 모든 조직의 계획 및 설계에서 중요 한 부분입니다. Microsoft Azure 가용성 [영역 및 가용성](../../../availability-zones/az-overview.md) 집합을 제공 합니다. 가용성 영역을 사용할 수 없는 지역에서 사용할 수 있습니다. 클라우드를 설계할 수 있도록 [가상 머신의 가용성 관리](../../../virtual-machines/linux/manage-availability.md) 에 대해 자세히 알아보세요.

Oracle은 클라우드 네이티브 도구 및 제품 외에도 [Oracle Data guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)와 같은 고가용성을 위한 솔루션과 Azure에서 설정할 수 있는 fsfo, [분할](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)및 [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) 를 제공 하는 [데이터 보호](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html)를 제공 합니다. 이 가이드에서는 이러한 각 솔루션에 대 한 참조 아키텍처를 설명 합니다.

마지막으로 클라우드의 응용 프로그램을 마이그레이션하거나 만들 때 [재시도 패턴](/azure/architecture/patterns/retry) 및 [회로 차단기 패턴과](/azure/architecture/patterns/circuit-breaker)같은 클라우드 네이티브 패턴을 추가 하도록 응용 프로그램 코드를 조정 하는 것이 중요 합니다. [클라우드 디자인 패턴 가이드](/azure/architecture/patterns/) 에 정의 된 추가 패턴은 응용 프로그램의 복원 력을 향상 시킬 수 있습니다.

### <a name="oracle-rac-in-the-cloud"></a>클라우드의 Oracle RAC

Oracle RAC (실제 응용 프로그램 클러스터)는 단일 데이터베이스 저장소 (공유-전체 아키텍처 패턴)에 액세스 하는 많은 인스턴스를 포함 하 여 고객이 높은 처리량을 달성할 수 있도록 하는 Oracle의 솔루션입니다. Oracle RAC는 온-프레미스의 고가용성에도 사용할 수 있지만, Oracle RAC 단독은 랙 수준 또는 데이터 센터 수준 오류를 방지 하는 것이 아니라 인스턴스 수준 오류에 대해서만 보호 하므로 클라우드의 고가용성에는 사용할 수 없습니다. 이러한 이유로 Oracle은 고가용성을 위해 데이터베이스 (단일 인스턴스 또는 RAC)와 함께 Oracle Data Guard를 사용 하는 것을 권장 합니다. 일반적으로 고객은 업무에 중요 한 응용 프로그램을 실행 하는 데 높은 SLA를 요구 합니다. Oracle RAC는 현재 Azure의 Oracle에서 인증 또는 지원 되지 않습니다. 그러나 Azure는 인스턴스 수준 오류 로부터 보호 하는 데 도움이 되는 Azure 제품 가용성 영역 및 계획 된 유지 관리 기간 등의 기능을 제공 합니다. 이 외에도 고객은 Oracle Data Guard, Oracle GoldenGate 및 Oracle 분할와 같은 기술을 사용 하 여 성능 및 복원 력을 랙 수준에서 보호 하 고 데이터 센터 수준 및 지리적 정치적 오류를 방지할 수 있습니다.

Oracle Data Guard 또는 GoldenGate와 함께 여러 [가용성 영역](../../../availability-zones/az-overview.md) 에서 oracle 데이터베이스를 실행 하는 경우 고객은 99.99%의 작동 시간 SLA를 얻을 수 있습니다. 가용성 영역이 아직 없는 Azure 지역에서 고객은 [가용성 집합](../../linux/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) 을 사용 하 고 99.95%의 작동 시간 SLA를 달성할 수 있습니다.

>참고: 가동 시간 목표는 Microsoft에서 제공 하는 작동 시간 SLA 보다 훨씬 더 높을 수 있습니다.

## <a name="disaster-recovery-for-oracle-databases"></a>Oracle 데이터베이스에 대 한 재해 복구

클라우드에서 중요 업무용 응용 프로그램을 호스트 하는 경우 고가용성 및 재해 복구를 설계 하는 것이 중요 합니다.

Oracle Database Enterprise Edition의 경우 Oracle Data Guard는 재해 복구를 위한 유용한 기능입니다. [쌍을 이루는 Azure 지역](../../../best-practices-availability-paired-regions.md) 에 대기 데이터베이스 인스턴스를 설정 하 고 재해 복구를 위해 Data Guard 장애 조치 (failover)를 설정할 수 있습니다. 데이터가 손실 되지 않도록 하려면 활성 데이터 가드 외에도 Oracle Data Guard Far Sync 인스턴스를 배포 하는 것이 좋습니다. 

응용 프로그램에서 대기 시간을 허용 하는 경우 Oracle 주 데이터베이스와 다른 가용성 영역에서 Data Guard Far 동기화 인스턴스를 설정 하는 것이 좋습니다 (철저 한 테스트가 필요 함). **최대 가용성** 모드를 사용 하 여 다시 실행 파일의 동기 전송을 Far 동기화 인스턴스에 설정 합니다. 이러한 파일은 대기 데이터베이스로 비동기적으로 전송 됩니다. 

**최대 가용성** 모드 (동기)의 다른 가용성 영역에서 far sync 인스턴스를 설정할 때 응용 프로그램에서 성능 손실을 허용 하지 않는 경우 주 데이터베이스와 동일한 가용성 영역에서 far 동기화 인스턴스를 설정할 수 있습니다. 추가 가용성을 위해 주 데이터베이스에 가까운 여러 개의 Far 동기화 인스턴스를 설정 하는 것이 좋습니다. 역할을 전환 하는 경우 대기 데이터베이스에 가까운 하나 이상의 인스턴스를 설정 하는 것이 좋습니다. Oracle Data guard [far 동기화 백서](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)의 Oracle Data Guard far 동기화에 대해 자세히 알아보세요.

Oracle Standard Edition 데이터베이스를 사용 하는 경우 고가용성 및 재해 복구를 설정 하는 데 사용할 수 있는 DBVisit Standby와 같은 ISV 솔루션이 있습니다.

## <a name="reference-architectures"></a>참조 아키텍처

### <a name="oracle-data-guard"></a>Oracle 데이터 가드

Oracle Data Guard는 엔터프라이즈 데이터에 대 한 고가용성, 데이터 보호 및 재해 복구를 보장 합니다. Data Guard는 대기 데이터베이스를 트랜잭션 측면에서 일관 된 주 데이터베이스 복사본으로 유지 관리 합니다. 주 데이터베이스와 보조 데이터베이스 간의 거리와 대기 시간에 대 한 응용 프로그램 허용 범위에 따라 동기 또는 비동기 복제를 설정할 수 있습니다. 그런 다음 계획 된 중단 또는 계획 되지 않은 중단으로 인해 주 데이터베이스를 사용할 수 없는 경우 데이터 가드는 대기 데이터베이스를 주 역할로 전환 하 여 가동 중지 시간을 최소화할 수 있습니다. 

Oracle Data Guard를 사용 하는 경우 읽기 전용으로 보조 데이터베이스를 열 수도 있습니다. 이 구성을 활성 데이터 가드 라고 합니다. Oracle Database 12c는 Data Guard Far 동기화 인스턴스 라는 기능을 도입 했습니다. 이 인스턴스를 사용 하면 성능에 영향을 주지 않고 Oracle 데이터베이스의 데이터 손실 (제로) 구성을 설정할 수 있습니다.

> [!NOTE]
> 활성 데이터 가드를 사용 하려면 추가 라이선스가 필요 합니다. 이 라이선스는 또한 Far 동기화 기능을 사용 하는 데 필요 합니다. Oracle 담당자와 연결 하 여 라이선스의 영향을 설명 하세요.

#### <a name="oracle-data-guard-with-fsfo"></a>FSFO을 사용한 Oracle Data Guard
FSFO (빠른 시작 장애 조치 (Failover))을 사용 하는 Oracle Data Guard는 별도의 컴퓨터에서 broker를 설정 하 여 추가 복원 력을 제공할 수 있습니다. Data Guard broker와 보조 데이터베이스는 모두 관찰자를 실행 하 고 주 데이터베이스에서 가동 중지 시간을 관찰 합니다. 이를 통해 데이터 보호 관찰자 설정 에서도 중복성을 사용할 수 있습니다. 

Oracle Database 버전 12.2 이상에서는 단일 Oracle Data Guard broker 구성으로 여러 관찰자를 구성할 수도 있습니다. 이 설치 프로그램은 하나의 관찰자와 보조 데이터베이스에 가동 중지 시간이 발생 하는 경우에 추가 가용성을 제공 합니다. Data Guard Broker는 경량 이며 비교적 작은 가상 머신에서 호스트할 수 있습니다. Data Guard Broker 및 그 이점에 대 한 자세한 내용은이 항목의 [Oracle 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) 를 참조 하세요.

다음 다이어그램은 가용성 영역을 사용 하 여 Azure에서 Oracle Data Guard를 사용 하기 위한 권장 아키텍처입니다. 이 아키텍처를 통해 99.99%의 VM 작동 시간 SLA를 얻을 수 있습니다.

![Data Guard Broker-FSFO에서 가용성 영역을 사용 하는 Oracle Database](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

위의 다이어그램에서 클라이언트 시스템은 웹을 통해 Oracle 백 엔드에서 사용자 지정 응용 프로그램에 액세스 합니다. 웹 프런트 엔드는 부하 분산 장치에 구성 됩니다. 웹 프런트 엔드는 해당 응용 프로그램 서버에 대 한 호출을 수행 하 여 작업을 처리 합니다. 응용 프로그램 서버는 주 Oracle 데이터베이스를 쿼리 합니다. Oracle 데이터베이스는 라이선스 비용을 절감 하 고 성능을 최대화 하기 위해 제약이 있는 [코어 vCPUs](../../../virtual-machines/constrained-vcpu.md) 가 포함 된 하이퍼 스레드 [메모리 최적화 가상 머신을](../../sizes-memory.md) 사용 하 여 구성 되었습니다. 여러 프리미엄 또는 ultra disks (Managed Disks)는 성능과 고가용성에 사용 됩니다.

Oracle 데이터베이스는 고가용성을 위해 여러 가용성 영역에 배치 됩니다. 각 영역은 독립 된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성 됩니다. 복원 력을 보장 하기 위해 사용 가능한 모든 지역에는 최소 3 개의 개별 영역이 설정 됩니다. 지역 내에서 가용성 영역을 물리적으로 분리 하는 것은 데이터 센터 오류 로부터 데이터를 보호 합니다. 또한 중단이 발생 한 경우 두 개의 FSFO 관찰자가 데이터베이스를 시작 하 고 장애 조치 (failover) 할 때 데이터베이스를 보조 데이터베이스로 장애 조치 (failover) 하도록 설정 됩니다. 

이전 아키텍처에 표시 된 영역 보다 다른 가용성 영역 (이 경우 AZ 1)에서 추가 관찰자 및/또는 대기 데이터베이스를 설정할 수 있습니다. 마지막으로 Oracle 데이터베이스는 Oracle OEM (Enterprise Manager)에서 작동 시간 및 성능을 모니터링 합니다. OEM을 사용 하면 다양 한 성능 및 사용 현황 보고서를 생성할 수도 있습니다.

가용성 영역이 지원 되지 않는 지역에서 가용성 집합을 사용 하 여 Oracle Database를 항상 사용 가능한 방식으로 배포할 수 있습니다. 가용성 집합을 사용 하면 VM 작동 시간을 99.95%까지 달성할 수 있습니다. 다음 다이어그램은이 사용의 참조 아키텍처입니다.

![Data Guard Broker-FSFO과 함께 가용성 집합을 사용 하는 Oracle Database](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * OEM의 인스턴스를 하나만 배포 하 고 있으므로 Oracle Enterprise Manager VM을 가용성 집합에 배치할 필요가 없습니다.
> * 현재 가용성 집합 구성에서 Ultra 디스크가 지원 되지 않습니다.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far 동기화

Oracle Data Guard Far 동기화는 Oracle 데이터베이스에 대해 제로 데이터 손실 방지 기능을 제공 합니다. 이 기능을 사용 하면 데이터베이스 컴퓨터에 오류가 발생 하는 경우의 데이터 손실을 방지할 수 있습니다. Oracle Data Guard Far 동기화는 별도의 VM에 설치 해야 합니다. Far Sync는 제어 파일, 암호 파일, spfile 및 대기 로그만 포함 하는 간단한 Oracle 인스턴스입니다. 데이터 파일이 나 rego 로그 파일이 없습니다. 

데이터 손실 방지를 위해 주 데이터베이스와 Far 동기화 인스턴스 간에 동기식 통신이 필요 합니다. Far 동기화 인스턴스는 동기 방식으로 주 복제본에서 다시 실행을 받아서 비동기 방식으로 모든 대기 데이터베이스에 즉시 전달 합니다. 또한이 설정은 주 데이터베이스의 오버 헤드를 줄여 모든 대기 데이터베이스 대신에 다시 실행을 모든 대기 데이터베이스 보다는 항상 다시 실행 해야 하기 때문에 발생 합니다. 먼 동기화 인스턴스가 실패 하는 경우 데이터 가드는 데이터 손실 방지를 유지 하기 위해 주 데이터베이스의 보조 데이터베이스에 대 한 비동기 전송을 자동으로 사용 합니다. 추가 된 복원 력을 위해 고객은 각 데이터베이스 인스턴스 (주 및 보조) 마다 여러 개의 먼 동기화 인스턴스를 배포할 수 있습니다.

다음 다이어그램은 Oracle Data Guard Far 동기화를 사용 하는 고가용성 아키텍처입니다.

![Data Guard Far Sync를 사용 하 여 가용성 영역을 사용 하는 Oracle 데이터베이스 & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

위의 아키텍처에는 둘 사이의 대기 시간을 줄이기 위해 데이터베이스 인스턴스와 동일한 가용성 영역에 배포 된 Far 동기화 인스턴스가 있습니다. 응용 프로그램의 대기 시간이 중요 한 경우에는 데이터베이스와 Far 동기화 인스턴스 또는 인스턴스를 [근접 배치 그룹](../../../virtual-machines/linux/proximity-placement-groups.md)에 배포 하는 것이 좋습니다.

다음 다이어그램은 고가용성 및 재해 복구를 위해 Oracle Data Guard FSFO 및 Far Sync를 활용 하는 아키텍처입니다.

![데이터 보호를 사용 하 여 재해 복구를 위해 가용성 영역을 사용 하는 Oracle Database & Broker-FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate를 사용 하면 엔터프라이즈 전체에서 여러 유형의 여러 플랫폼 간에 트랜잭션 수준에서 데이터를 교환 및 조작할 수 있습니다. 기존 인프라의 트랜잭션 무결성 및 최소 오버 헤드로 커밋된 트랜잭션을 이동 합니다. 모듈식 아키텍처를 사용 하면 선택한 데이터 레코드, 트랜잭션 변경 내용, 다양 한 토폴로지에서 DDL (데이터 정의 언어)에 대 한 변경 내용을 추출 하 고 복제할 수 있습니다.

Oracle GoldenGate를 사용 하면 양방향 복제를 제공 하 여 고가용성을 위해 데이터베이스를 구성할 수 있습니다. 이렇게 하면 **다중 마스터** 또는 **활성-활성 구성을**설정할 수 있습니다. 다음 다이어그램은 Azure의 Oracle GoldenGate 활성-활성 설치에 권장 되는 아키텍처입니다. 다음 아키텍처에서는 라이선스 비용을 절감 하 고 성능을 최대화 하기 위해 [제약이 있는 코어 vCPUs](../../../virtual-machines/constrained-vcpu.md) 가 포함 된 하이퍼 스레드 [메모리 최적화 가상 머신을](../../sizes-memory.md) 사용 하 여 Oracle 데이터베이스를 구성 했습니다. 성능 및 가용성을 위해 여러 프리미엄 또는 ultra 디스크 (관리 디스크)가 사용 됩니다.

![Data Guard Broker-FSFO에서 가용성 영역을 사용 하는 Oracle Database](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> 가용성 영역을 현재 사용할 수 없는 지역에서 가용성 집합을 사용 하 여 유사한 아키텍처를 설정할 수 있습니다.

Oracle GoldenGate에는 Oracle 데이터베이스 서버 간에 데이터를 비동기적으로 복제 하는 데 사용할 수 있는 Extract, 펌프, Replicat 등의 프로세스가 있습니다. 이러한 프로세스를 사용 하 여 가용성 영역 수준 가동 중지 시간이 있는 경우 데이터베이스의 고가용성을 보장 하기 위해 양방향 복제를 설정할 수 있습니다. 위의 다이어그램에서 추출 프로세스는 Oracle 데이터베이스와 동일한 서버에서 실행 되는 반면, 데이터 펌프 및 Replicat 프로세스는 동일한 가용성 영역에 있는 별도의 서버에서 실행 됩니다. Replicat 프로세스는 다른 가용성 영역에 있는 데이터베이스에서 데이터를 수신 하 고 해당 가용성 영역에서 Oracle 데이터베이스로 데이터를 커밋하는 데 사용 됩니다. 마찬가지로, 데이터 펌프 프로세스는 추출 프로세스에 의해 추출 된 데이터를 다른 가용성 영역에 있는 Replicat 프로세스로 보냅니다. 

위의 아키텍처 다이어그램은 별도의 서버에 구성 된 데이터 펌프 및 Replicat 프로세스를 보여 주지만 서버 용량 및 사용량에 따라 동일한 서버에 모든 Oracle GoldenGate 프로세스를 설정할 수 있습니다. 서버 사용 패턴을 이해 하려면 항상 Azure에서 AWR 보고서와 메트릭을 참조 하세요.

다른 가용성 영역 또는 다른 지역에서 Oracle GoldenGate 양방향 복제를 설정 하는 경우 응용 프로그램에 대해 서로 다른 구성 요소 간의 대기 시간을 허용 해야 합니다. 가용성 영역 및 지역 간의 대기 시간은 다양 한 요인에 따라 달라질 수 있습니다. 응용 프로그램 계층과 데이터베이스 계층 간에 성능 테스트를 설정 하 여 응용 프로그램 성능 요구 사항을 충족 하는지 확인 하는 것이 좋습니다.

응용 프로그램 계층은 자체 서브넷에서 설정할 수 있으며 데이터베이스 계층은 자체 서브넷으로 분리 될 수 있습니다. 가능 하면 [Azure 애플리케이션 Gateway](../../../application-gateway/overview.md) 를 사용 하 여 응용 프로그램 서버 간의 트래픽 부하를 분산 하는 것이 좋습니다. Azure 애플리케이션 게이트웨이는 강력한 웹 트래픽 부하 분산 장치입니다. 사용자 세션을 동일한 서버에 유지 하 여 데이터베이스의 충돌을 최소화 하는 쿠키 기반 세션 선호도를 제공 합니다. Application Gateway에 대 한 대안은 [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) 및 [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md)입니다.

### <a name="oracle-sharding"></a>Oracle 분할

분할는 Oracle 12.2에 도입 된 데이터 계층 패턴입니다. 이를 통해 독립 데이터베이스 간에 데이터를 수평으로 분할 하 고 확장할 수 있습니다. 이 아키텍처는 각 데이터베이스가 전용 가상 컴퓨터에서 호스트 되는 비공유 아키텍처 이며, 복원 력과 가용성 향상을 비롯 하 여 높은 읽기 및 쓰기 처리량을 가능 하 게 합니다. 이 패턴은 단일 실패 지점이 제거 되 고, 오류 격리를 제공 하 고, 가동 중지 시간 없이 롤링 업그레이드를 가능 하 게 합니다. 하나의 분할 된 데이터베이스가 나 데이터 센터 수준 오류의 가동 중지 시간은 다른 데이터 센터에 있는 다른 분할의 성능이 나 가용성에 영향을 주지 않습니다. 

분할는 가동 중지 시간을 감당할 수 없는 처리량이 높은 OLTP 응용 프로그램에 적합 합니다. 동일한 분할 키를 가진 모든 행은 항상 동일한 분할 된 데이터베이스가 되도록 보장 되므로 높은 일관성을 제공 하는 성능이 향상 됩니다. 분할를 사용 하는 응용 프로그램에는 잘 정의 된 데이터 모델 및 데이터 배포 전략 (일관성 있는 해시, 범위, 목록 또는 복합)이 있어야 합니다 .이는 주로 분할 키 (예: *customerId* 또는 *accountnum*)를 사용 하 여 데이터에 액세스 합니다. 또한 분할를 사용 하면 특정 데이터 집합을 최종 고객에 게 더 가깝게 저장 하 여 성능 및 규정 준수 요구 사항을 충족할 수 있습니다.

고가용성 및 재해 복구를 위해 분할를 복제 하는 것이 좋습니다. Oracle Data Guard 또는 oracle GoldenGate와 같은 Oracle 기술을 사용 하 여이 설치를 수행할 수 있습니다. 복제 단위는 분할 영역, 분할 된 그룹 또는 분할 그룹 일 수 있습니다. 분할 된 데이터베이스의 가용성은 하나 이상의 분할 중단 또는 속도 저하의 영향을 받지 않습니다. 고가용성을 위해 주 분할 배치 된 동일한 가용성 영역에 대기 분할을 배치할 수 있습니다. 재해 복구의 경우 대기 분할 다른 지역에 있을 수 있습니다. 또한 분할를 여러 지역에 배포 하 여 해당 지역에서 트래픽을 처리할 수 있습니다. [Oracle 분할 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)에서 분할 된 데이터베이스의 고가용성 및 복제를 구성 하는 방법에 대해 자세히 알아보세요.

Oracle 분할는 주로 다음 구성 요소로 구성 됩니다. 이러한 구성 요소에 대 한 자세한 내용은 [Oracle 분할 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)에서 찾을 수 있습니다.

- 분할 된 데이터베이스 **카탈로그** -모든 분할 된 데이터베이스 구성 데이터에 대 한 영구 저장소 인 특수 용도의 Oracle 데이터베이스입니다. 분할 된 데이터베이스에서 분할 추가 또는 제거, 데이터 매핑 및 DDLs와 같은 모든 구성 변경 내용은 분할 된 데이터베이스 카탈로그에서 시작 됩니다. 분할 된 카탈로그에는 SDB에서 중복 된 모든 테이블의 마스터 복사본도 포함 됩니다. 

  분할 카탈로그는 구체화 된 뷰를 사용 하 여 모든 분할에서 중복 된 테이블의 변경 내용을 자동으로 복제 합니다. 또한 분할 된 데이터베이스 카탈로그 데이터베이스는 분할 키를 지정 하지 않는 다중 분할 된 데이터베이스 쿼리 및 쿼리를 처리 하는 데 사용 되는 쿼리 코디네이터 역할을 합니다. 
  
  Oracle Data Guard를 가용성 영역 또는 가용성 집합을 사용 하 여 분할 된 카탈로그에 대 한 가용성 집합과 함께 사용 하는 것이 좋습니다. 분할 된 카탈로그의 가용성은 분할 된 데이터베이스의 가용성에 영향을 주지 않습니다. 분할 된 카탈로그의 가동 중지 시간은 데이터 가드 장애 조치가 완료 되는 짧은 기간 동안 유지 관리 작업 및 다중 분할 된 쿼리에만 영향을 줍니다. 온라인 트랜잭션은 SDB에서 계속 라우팅되고 실행 되며 카탈로그 중단의 영향을 받지 않습니다.

- 분할 된 **디렉터** -분할가 상주 하는 각 지역/가용성 영역에 배포 해야 하는 경량 서비스입니다. 분할 된 디렉터는 Oracle 분할의 컨텍스트에서 배포 되는 글로벌 서비스 관리자입니다. 고가용성을 위해 분할가 있는 각 가용성 영역에 하나 이상의 분할 된 사용자를 배포 하는 것이 좋습니다. 

  처음에 데이터베이스에 연결 하는 경우 라우팅 정보는 분할 된 디렉터에 의해 설정 되며 분할 된 데이터베이스 감독을 우회 하 여 후속 요청에 대해 캐시 됩니다. 분할 된 데이터베이스가 있는 세션을 설정 하면 모든 SQL 쿼리와 DMLs가 지원 되 고 지정 된 분할 된 데이터베이스가 범위에서 실행 됩니다. 이 라우팅은 속도가 빠르며 분할 되지 않은 트랜잭션을 수행 하는 모든 OLTP 워크 로드에 사용 됩니다. 최고 수준의 성능 및 가용성을 필요로 하는 모든 OLTP 워크 로드에 대해 직접 라우팅을 사용 하는 것이 좋습니다. 분할 된 데이터베이스가 사용할 수 없게 되거나 변경 되 면 분할 토폴로지에서 라우팅 캐시가 자동으로 새로 고쳐집니다. 
  
  고성능 데이터 종속 라우팅의 경우 Oracle은 분할 된 데이터베이스의 데이터에 액세스할 때 연결 풀을 사용 하는 것을 권장 합니다. Oracle 연결 풀, 언어별 라이브러리 및 드라이버는 Oracle 분할을 지원 합니다. 자세한 내용은 [Oracle 분할 설명서](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) 를 참조 하세요.

- **글로벌 서비스** -글로벌 서비스는 일반 데이터베이스 서비스와 비슷합니다. 글로벌 서비스는 데이터베이스 서비스의 모든 속성 외에도 클라이언트와 분할 된 데이터베이스 간의 지역 선호도와 같은 분할 된 데이터베이스의 속성 및 복제 지연 허용 오차를 포함 합니다. 분할 된 데이터베이스에서 데이터를 읽고 쓰기 위해 하나의 글로벌 서비스만 생성 해야 합니다. Active Data Guard를 사용 하 고 분할의 읽기 전용 복제본을 설정 하는 경우 읽기 전용 작업에 대 한 다른 gGobal 서비스를 만들 수 있습니다. 클라이언트는 이러한 글로벌 서비스를 사용 하 여 데이터베이스에 연결할 수 있습니다.

- 분할 된 **데이터베이스** -분할 된 데이터베이스는 Oracle 데이터베이스입니다. 각 데이터베이스는 FSFO (빠른 시작 장애 조치 (Failover))이 설정 된 Broker 구성의 Oracle Data Guard를 사용 하 여 복제 됩니다. 각 분할 영역에서 Data Guard 장애 조치 (failover) 및 복제를 설정할 필요가 없습니다. 공유 데이터베이스를 만들 때 자동으로 구성 되 고 배포 됩니다. 특정 분할 된 데이터베이스에 오류가 발생 하면 Oracle 공유가 자동으로 주 데이터베이스에서 대기로 데이터베이스 연결을 장애 조치 (failover) 합니다.

Oracle Enterprise Manager Cloud Control GUI 및/또는 명령줄 유틸리티의 두 가지 인터페이스를 사용 하 여 Oracle 분할 된 데이터베이스를 배포 하 고 관리할 수 있습니다. `GDSCTL` 클라우드 제어를 사용 하 여 가용성 및 성능에 대 한 다양 한 분할 모니터링할 수 있습니다. `GDSCTL DEPLOY`명령은 자동으로 분할 및 해당 수신기를 만듭니다. 또한이 명령은 관리자가 지정한 분할 수준의 고가용성에 사용 되는 복제 구성을 자동으로 배포 합니다.

데이터베이스를 분할 하는 방법에는 여러 가지가 있습니다.

* 시스템 관리 분할-분할을 사용 하 여 분할 간에 자동으로 분산
* 사용자 정의 분할-분할에 대 한 데이터 매핑을 지정할 수 있습니다 .이는 규정 또는 데이터 지역화 요구 사항이 있을 때 잘 작동 합니다.
* 복합 분할-다른 _shardspaces_ 에 대 한 시스템 관리 및 사용자 정의 분할의 조합입니다.
* 테이블 하위 파티션-일반 분할 된 테이블과 유사 합니다.

Oracle 설명서의 다른 [분할 메서드에](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) 대해 자세히 알아보세요.

분할 된 데이터베이스는 응용 프로그램 및 개발자에 게 단일 데이터베이스 처럼 보일 수 있지만 분할 된 되지 않는 데이터베이스에서 분할 된 데이터베이스로 마이그레이션할 때 중복 되는 테이블 및 분할 된를 결정 하려면 신중한 계획이 필요 합니다. 

중복 테이블은 모든 분할에 저장 되는 반면 분할 된 테이블은 여러 분할에 분산 됩니다. Small 및 차원 테이블을 복제 하 고 팩트 테이블을 배포/분할 하는 것이 좋습니다. 분할 된 카탈로그를 중앙 코디네이터로 사용 하거나 각 분할 된 데이터베이스에서 데이터 펌프를 실행 하 여 분할 된 데이터베이스에 데이터를 로드할 수 있습니다. Oracle 설명서에서 [분할 된 데이터베이스로 데이터 마이그레이션](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) 에 대해 자세히 알아보세요.

#### <a name="oracle-sharding-with-data-guard"></a>Data Guard를 사용 하는 Oracle 분할

Oracle Data Guard는 시스템 관리, 사용자 정의 및 복합 분할 메서드를 사용 하 여 분할에 사용할 수 있습니다.

다음 다이어그램은 각 분할 된 데이터의 고가용성을 위해 Oracle Data Guard를 사용 하는 Oracle 분할에 대 한 참조 아키텍처입니다. 아키텍처 다이어그램은 _복합 분할 메서드_를 보여 줍니다. 아키텍처 다이어그램은 데이터 위치, 부하 분산, 고가용성, 재해 복구 등에 대 한 요구 사항이 서로 다른 응용 프로그램에 대해 다를 수 있으며, 다른 방법을 사용 하 여 분할 수 있습니다. Oracle 분할를 통해 이러한 요구 사항을 충족 하 고 이러한 옵션을 제공 하 여 가로 및 효율적으로 확장할 수 있습니다. Oracle GoldenGate를 사용 하 여 유사한 아키텍처를 배포할 수도 있습니다.

![분할를 사용 하 여 데이터 보호 브로커-FSFO과 함께 가용성 영역 사용 Oracle Database](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

시스템 관리 분할는 구성 하 고 관리 하는 가장 쉬운 반면, 사용자 정의 분할 또는 복합 분할는 데이터 및 응용 프로그램이 지리적으로 분산 되어 있거나 각 분할 된 복제본을 제어 해야 하는 시나리오에 적합 합니다. 

위의 아키텍처에서 복합 분할는 데이터를 지리적으로 분산 하 고 응용 프로그램 계층을 수평 확장 하는 데 사용 됩니다. 복합 분할는 시스템 관리 및 사용자 정의 분할의 조합 이므로 두 방법의 이점을 제공 합니다. 위의 시나리오에서 데이터는 먼저 지역별로 구분 된 여러 shardspaces에 걸쳐 분할 된 됩니다. 그런 다음 데이터는 shardspace의 여러 분할에 걸쳐 일관 된 해시를 통해 추가로 분할 됩니다. 각 shardspace에는 여러 shardgroups 포함 됩니다. 각 shardgroup에는 여러 분할 있고이 경우 복제의 "단위"가 있습니다. 각 shardgroup에는 shardspace의 모든 데이터가 포함 됩니다. Shardgroups A1 및 B1은 기본 Shardgroups, Shardgroups A2와 B2는 그 사이에 있습니다. 개별 분할가 shardgroup 아닌 복제의 단위를 갖도록 선택할 수 있습니다.

위의 아키텍처에서 GSM/분할 된 디렉터는 고가용성을 위해 모든 가용성 영역에 배포 됩니다. 데이터 센터/지역 마다 하나 이상의 GSM/분할 된 디렉터를 배포 하는 것이 좋습니다. 또한 응용 프로그램 서버 인스턴스는 shardgroup를 포함 하는 모든 가용성 영역에 배포 됩니다. 이렇게 설정 하면 응용 프로그램에서 응용 프로그램 서버와 데이터베이스/shardgroup 낮음 사이의 대기 시간을 유지할 수 있습니다. 데이터베이스에 오류가 발생 하는 경우 데이터베이스 역할 전환이 발생 하면 대기 데이터베이스와 동일한 영역에 있는 응용 프로그램 서버가 요청을 처리할 수 있습니다. Azure 애플리케이션 게이트웨이와 분할 된 디렉터는 요청 및 응답 대기 시간을 추적 하 고 그에 따라 요청을 라우팅합니다.

응용 프로그램 관점에서 클라이언트 시스템은 클라이언트와 가장 가까운 지역으로 요청을 리디렉션하는 Azure 애플리케이션 게이트웨이 (또는 Azure의 다른 부하 분산 기술)를 요청 합니다. Azure 애플리케이션 게이트웨이는 고정 세션도 지원 하므로 동일한 클라이언트에서 들어오는 모든 요청은 동일한 응용 프로그램 서버로 라우팅됩니다. 응용 프로그램 서버는 데이터 액세스 드라이버에서 연결 풀링을 사용 합니다. 이 기능은 JDBC, ODP.NET, OCI 등의 드라이버에서 사용할 수 있습니다. 드라이버는 요청의 일부로 지정 된 분할 키를 인식할 수 있습니다. JDBC 클라이언트용 [ORACLE UCP (유니버설 연결 풀)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) 는 Apache TOMCAT 및 IIS와 같은 비 oracle 응용 프로그램 클라이언트를 사용 하도록 설정 하 여 oracle 분할 작업을 수행할 수 있습니다. 

초기 요청 중에 응용 프로그램 서버는 해당 지역의 분할 된 디렉터에 연결 하 여 요청을 라우팅해야 하는 분할 된 서버에 대 한 라우팅 정보를 가져옵니다. 전달 된 분할 키에 따라 디렉터는 응용 프로그램 서버를 해당 분할 된 서버에 라우팅합니다. 응용 프로그램 서버는 맵을 작성 하 여이 정보를 캐시 하 고, 후속 요청의 경우 분할 된 디렉터를 우회 하 고 요청을 분할 된 서버로 직접 라우팅합니다.

#### <a name="oracle-sharding-with-goldengate"></a>GoldenGate을 사용 하는 Oracle 분할

다음 다이어그램은 각 분할 된 지역에서 고가용성을 위해 Oracle 분할 Oracle GoldenGate에 대 한 참조 아키텍처입니다. 위의 아키텍처와는 달리이 아키텍처는 단일 Azure 지역 (여러 가용성 영역) 내에서 고가용성을 portrays 합니다. Oracle GoldenGate를 사용 하 여 다중 지역 고가용성 분할 된 데이터베이스 (앞의 예제와 유사)를 배포할 수 있습니다.

![GoldenGate에서 가용성 영역을 사용 하는 Oracle Database 분할](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

위의 참조 아키텍처는 _시스템 관리_ 분할 메서드를 사용 하 여 데이터를 분할 합니다. Oracle GoldenGate 복제는 청크 수준에서 수행 되므로 한 분할 된 데이터베이스로 복제 된 데이터의 절반을 다른 분할 된 데이터베이스로 복제할 수 있습니다. 다른 절반을 다른 분할 된 데이터베이스로 복제할 수 있습니다. 

데이터가 복제 되는 방식은 복제 요인에 따라 달라 집니다. 복제 요소 2를 사용 하는 경우 shardgroup의 세 분할에서 각 데이터 청크의 복사본 두 개가 만들어집니다. 마찬가지로 shardgroup에서 복제 요인이 3이 고 분할 3 개를 사용 하는 경우 각 분할 된 데이터의 모든 데이터는 shardgroup의 다른 모든 분할 된 데이터베이스로 복제 됩니다. Shardgroup의 각 분할 영역에는 서로 다른 복제 요인이 있을 수 있습니다. 이 설정은 shardgroup 및 여러 shardgroups 내에서 효율적으로 고가용성 및 재해 복구 디자인을 정의 하는 데 도움이 됩니다.

위의 아키텍처에서 shardgroup A와 shardgroup B는 모두 동일한 데이터를 포함 하지만 다른 가용성 영역에 있습니다. Shardgroup A와 shardgroup B 모두의 복제 요인이 3 인 경우 분할 된 테이블의 각 행/청크는 두 shardgroups에서 6 번 복제 됩니다. Shardgroup A의 복제 요인이 3이 고 shardgroup B의 복제 인수가 2 인 경우 각 행/청크는 두 shardgroups에서 5 번 복제 됩니다.

이 설정은 인스턴스 수준 또는 가용성 영역 수준 오류가 발생 하는 경우 데이터 손실을 방지 합니다. 응용 프로그램 계층은 각 분할 영역에서 읽고 쓸 수 있습니다. 충돌을 최소화 하기 위해 Oracle 분할는 해시 값의 각 범위에 대해 "마스터 청크"를 지정 합니다. 이 기능은 특정 청크에 대 한 쓰기 요청이 해당 청크로 전달 되도록 합니다. 또한 Oracle GoldenGate는 발생할 수 있는 모든 충돌을 처리 하기 위한 자동 충돌 감지 및 해결 기능을 제공 합니다. Oracle 분할로 GoldenGate을 구현 하는 방법에 대 한 자세한 내용 및 제한 사항에 대 한 자세한 내용은 oracle의 oracle [GoldenGate with a 분할 된 database](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)를 참조 하십시오.

위의 아키텍처에서 GSM/분할 된 디렉터는 고가용성을 위해 모든 가용성 영역에 배포 됩니다. 데이터 센터나 지역 마다 하나 이상의 GSM/분할 된 이사를 배포 하는 것이 좋습니다. 또한 응용 프로그램 서버 인스턴스는 shardgroup를 포함 하는 모든 가용성 영역에 배포 됩니다. 이렇게 설정 하면 응용 프로그램에서 응용 프로그램 서버와 데이터베이스/shardgroup 낮음 사이의 대기 시간을 유지할 수 있습니다. 데이터베이스에 오류가 발생 하는 경우 데이터베이스 역할이 전환 되 면 대기 데이터베이스와 동일한 영역에 있는 응용 프로그램 서버가 요청을 처리할 수 있습니다. Azure 애플리케이션 게이트웨이와 분할 된 디렉터는 요청 및 응답 대기 시간을 추적 하 고 그에 따라 요청을 라우팅합니다.

응용 프로그램 관점에서 클라이언트 시스템은 클라이언트와 가장 가까운 지역으로 요청을 리디렉션하는 Azure 애플리케이션 게이트웨이 (또는 Azure의 다른 부하 분산 기술)를 요청 합니다. Azure 애플리케이션 게이트웨이는 고정 세션도 지원 하므로 동일한 클라이언트에서 들어오는 모든 요청은 동일한 응용 프로그램 서버로 라우팅됩니다. 응용 프로그램 서버는 데이터 액세스 드라이버에서 연결 풀링을 사용 합니다. 이 기능은 JDBC, ODP.NET, OCI 등의 드라이버에서 사용할 수 있습니다. 드라이버는 요청의 일부로 지정 된 분할 키를 인식할 수 있습니다. JDBC 클라이언트용 [ORACLE UCP (유니버설 연결 풀)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) 는 Apache TOMCAT 및 IIS와 같은 비 oracle 응용 프로그램 클라이언트를 사용 하도록 설정 하 여 oracle 분할 작업을 수행할 수 있습니다. 

초기 요청 중에 응용 프로그램 서버는 해당 지역의 분할 된 디렉터에 연결 하 여 요청을 라우팅해야 하는 분할 된 서버에 대 한 라우팅 정보를 가져옵니다. 전달 된 분할 키에 따라 디렉터는 응용 프로그램 서버를 해당 분할 된 서버에 라우팅합니다. 응용 프로그램 서버는 맵을 작성 하 여이 정보를 캐시 하 고, 후속 요청의 경우 분할 된 디렉터를 우회 하 고 요청을 분할 된 서버로 직접 라우팅합니다.

## <a name="patching-and-maintenance"></a>패치 및 유지 관리

Oracle 워크 로드를 Azure에 배포 하는 경우 Microsoft는 모든 호스트 OS 수준 패치를 처리 합니다. 계획 된 모든 OS 수준 유지 관리는 고객이 계획 된 유지 관리를 위해 고객에 게 미리 전달 됩니다. 서로 다른 두 가용성 영역의 두 서버는 동시에 패치할 수 없습니다. VM 유지 관리 및 패치 적용에 대 한 자세한 내용은 [virtual machines의 가용성 관리](../../../virtual-machines/linux/manage-availability.md) 를 참조 하세요. 

[Azure Automation 업데이트 관리](../../../automation/update-management/update-mgmt-overview.md)를 사용 하 여 가상 머신 운영 체제 패치를 자동화할 수 있습니다. [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) 또는 [Azure Automation 업데이트 관리](../../../automation/update-management/update-mgmt-overview.md) 를 사용 하 여 Oracle 데이터베이스를 패치 및 유지 관리 하 여 가동 중지 시간을 최소화할 수 있습니다. Oracle 데이터베이스의 컨텍스트에서 사용할 수 있는 방법을 이해 하려면 [연속 배달 및 Blue/녹색 배포](/azure/devops/learn/what-is-continuous-delivery) 를 참조 하세요.

## <a name="architecture-and-design-considerations"></a>아키텍처 및 디자인 고려 사항

- 라이선스 비용을 절감 하 고 성능을 최대화 하려면 Oracle Database VM에 대해 [제약이 있는 코어 vCPUs](../../../virtual-machines/constrained-vcpu.md) 가 포함 된 하이퍼 스레드 [메모리 최적화 가상 머신을](../../sizes-memory.md) 사용 하는 것이 좋습니다. 성능 및 가용성을 위해 여러 프리미엄 또는 ultra disks (관리 디스크)를 사용 합니다.
- 관리 디스크를 사용 하는 경우 재부팅 시 디스크/장치 이름이 변경 될 수 있습니다. 다시 부팅 간에 탑재를 유지 하려면 이름 대신 장치 UUID를 사용 하는 것이 좋습니다. 자세한 내용은 [여기](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)를 참조하세요.
- 가용성 영역을 사용 하 여 지역에서 고가용성을 구현 합니다.
- Oracle 데이터베이스에 대 한 ultra disks (사용 가능한 경우) 또는 프리미엄 디스크를 사용 하는 것이 좋습니다.
- Oracle Data Guard를 사용 하 여 다른 Azure 지역에 대기 Oracle 데이터베이스를 설정 하는 것이 좋습니다.
- 응용 프로그램과 데이터베이스 계층 간의 대기 시간을 줄이려면 [근접 배치 그룹](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) 을 사용 하는 것이 좋습니다.
- 관리, 모니터링 및 로깅을 위해 [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) 를 설정 합니다.
- 데이터베이스에 대해 간소화 된 저장소 관리를 위해 Oracle ASM (자동 저장소 관리)을 사용 하는 것이 좋습니다.
- [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 를 사용 하 여 가동 중지 시간 없이 데이터베이스에 대 한 패치 및 업데이트를 관리할 수 있습니다.
- 응용 프로그램 코드를 조정 하 여 [재시도 패턴](/azure/architecture/patterns/retry), [회로 차단기 패턴](/azure/architecture/patterns/circuit-breaker)및 [클라우드 디자인 패턴](/azure/architecture/patterns/) 에 정의 된 기타 패턴과 같은 클라우드 네이티브 패턴을 추가 하 여 응용 프로그램의 복원 력을 높일 수 있습니다.

## <a name="next-steps"></a>다음 단계

시나리오에 적용 되는 다음 Oracle 참조 문서를 검토 합니다.

- [Oracle Data Guard 소개](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker 개념](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [능동-활성 고가용성을 위한 Oracle GoldenGate 구성](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Oracle 분할 개요](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [모든 거리에서 Oracle Active Data Guard Far 동기화 제로 데이터 손실](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
