---
title: 중요 비즈니스용 계층 - Azure SQL Database 서비스 | Microsoft Docs
description: Azure SQL Database 중요 비즈니스용 계층에 대해 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: e9f40e749642f2025c5298df74f9d8ff87aec14b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709339"
---
# <a name="business-critical-tier---azure-sql-database"></a>중요 비즈니스용 계층 - Azure SQL Database

> [!NOTE]
> 중요 비즈니스용 계층은 DTU 구매 모델에서 프리미엄이라고 합니다. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 대해 조정되는 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에 사용되는 세 가지 아키텍처 모델이 있습니다.
- 범용/표준 
- 중요 비즈니스용/프리미엄
- 하이퍼스케일

프리미엄/중요 비즈니스용 서비스 계층 모델은 데이터베이스 엔진 프로세스의 클러스터를 기반으로 합니다. 이 아키텍처 모델은 항상 사용 가능한 데이터베이스 엔진 노드의 쿼럼이 있다는 사실에 의존하며 유지 보수 작업 중에도 워크로드에 최소한의 성능 영향을 줍니다.

Azure는 사용자의 가동 중단 시간을 최소화하면서 운영 체제, 드라이버 및 SQL Server 데이터베이스 엔진을 업그레이드하고 패치를 적용합니다. 

프리미엄 가용성은 Azure SQL Database의 프리미엄 및 중요 비즈니스용 서비스 계층에서 사용할 수 있으며 지속적인 유지 관리 작업으로 인해 성능에 미치는 영향을 감당할 수 없는 집약적인 워크로드에 맞게 설계되었습니다.

프리미엄 모델에서 Azure SQL 데이터베이스는 계산 및 스토리지를 단일 노드에 통합합니다. 이 아키텍처 모델의 고가용성은 SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 네 개의 노드 클러스터에 배포된 컴퓨팅(SQL Server 데이터베이스 엔진 프로세스) 및 스토리지(로컬로 장착된 SSD)의 복제를 통해 이루어집니다.

![데이터베이스 엔진 노드의 클러스터](media/sql-database-managed-instance/business-critical-service-tier.png)

SQL 데이터베이스 엔진 프로세스와 기본 mdf/ldf 파일이 SSD 저장소가 로컬에 연결되어 있는 동일한 노드에 배치되기 때문에 워크로드에 대한 대기 시간이 짧습니다. SQL Server [Always On 가용성 그룹](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)과 유사한 기술을 사용하여 고가용성이 구현됩니다. 모든 데이터베이스는 고객 워크로드에 액세스할 수 있는 주 데이터베이스 하나와 데이터 복사본을 포함하는 보조 프로세스 세 개가 있는 데이터베이스 노드의 클러스터입니다. 주 노드는 어떤 이유로든 주 노드의 작동이 중단되는 경우 보조 복제본의 데이터를 사용할 수 있도록 하기 위해 변경 내용을 보조 노드로 지속적으로 푸시합니다 장애 조치(failover)는 SQL Server 데이터베이스 엔진에 의해 처리됩니다. 보조 복제본 하나가 주 노드가 되고 클러스터에 충분한 노드를 보장하기 위해 새로운 보조 복제본이 만들어집니다. 워크로드는 새로운 주 노드에 자동으로 리디렉션됩니다.

또한 중요 비즈니스용 클러스터에는 기본 워크로드의 성능에 영향을 주면 안 되는 읽기 전용 쿼리(예: 보고서)를 실행하는 데 사용될 수 있는 기본 읽기 전용 노드를 무료로 제공하는 [읽기 확장](sql-database-read-scale-out.md) 기능이 기본으로 제공됩니다.

## <a name="when-to-choose-this-service-tier"></a>언제 이 서비스 계층을 선택하나요?

중요 비즈니스용 서비스 계층은 기본 SSD 스토리지에서 짧은 대기 시간 응답(평균1 ~ 2밀리초), 기본 인프라가 실패하는 경우 빠른 복구, 보고서와 분석은 물론, 읽기 전용 쿼리를 무료로 읽을 수 있는 주 데이터베이스의 보조 복제본으로 오프로드할 필요성이 필요한 애플리케이션용으로 설계되었습니다.

## <a name="next-steps"></a>다음 단계

- [범용](sql-database-service-tier-general-purpose.md) 및 [하이퍼스케일](sql-database-service-tier-hyperscale.md) 계층에 대해 알아봅니다.
- [Service Fabric](../service-fabric/service-fabric-overview.md)에 대해 알아봅니다.
- 고가용성 및 재해 복구에 대한 자세한 옵션은 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.
