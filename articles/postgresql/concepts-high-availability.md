---
title: 고가용성-Azure Database for PostgreSQL-단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL 단일 서버의 고가용성에 대 한 정보를 제공 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/15/2020
ms.openlocfilehash: 16ce5b42e35ff3d650ba18aa95ab80b83fdbfdad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547684"
---
# <a name="high-availability-in-azure-database-for-postgresql--single-server"></a>Azure Database for PostgreSQL에서 고가용성 – 단일 서버
Azure Database for PostgreSQL – 단일 서버 서비스는 재정적 지원 되는 SLA (서비스 수준 계약) [99.99%](https://azure.microsoft.com/support/legal/sla/postgresql) 가동 시간을 보장 하는 높은 수준의 가용성을 제공 합니다. Azure Database for PostgreSQL은 사용자가 제공 하는 크기 조정 계산 작업 등의 계획 된 이벤트 중에 고가용성을 제공 하며, 기본 하드웨어, 소프트웨어 또는 네트워크 오류와 같은 계획 되지 않은 이벤트가 발생 하는 경우에도 제공 합니다. 이 서비스를 사용 하는 경우 응용 프로그램 작동 중단 시간이 거의 없도록 하 여 대부분의 중요 한 상황에서 신속 하 게 복구할 수 Azure Database for PostgreSQL.

Azure Database for PostgreSQL는 높은 가동 시간이 필요한 중요 업무용 데이터베이스를 실행 하는 데 적합 합니다. Azure 아키텍처를 기반으로 구축 된이 서비스에는 추가 구성 요소를 구성할 필요 없이 계획 되거나 계획 되지 않은 중단 으로부터 데이터베이스 가동 중지 시간을 완화 하기 위한 내재 된 고가용성, 중복성 및 복원 력 기능이 있습니다. 

## <a name="components-in-azure-database-for-postgresql--single-server"></a>Azure Database for PostgreSQL의 구성 요소 – 단일 서버

| **구성 요소** | **설명**|
| ------------ | ----------- |
| <b>PostgreSQL 데이터베이스 서버 | Azure Database for PostgreSQL는 데이터베이스 서버에 대 한 보안, 격리, 리소스 보호 및 빠른 다시 시작 기능을 제공 합니다. 이러한 기능은 가동 중단 후 몇 초 후에 크기 조정 및 데이터베이스 서버 복구 작업과 같은 작업을 용이 하 게 합니다. <br/> 데이터베이스 서버의 데이터 수정은 일반적으로 데이터베이스 트랜잭션 컨텍스트에서 발생 합니다. 모든 데이터베이스 변경 내용은 데이터베이스 서버에 연결 된 Azure Storage에서 WAL (write 미리 로그) 형식으로 동기적으로 기록 됩니다. 데이터베이스 [검사점](https://www.postgresql.org/docs/11/sql-checkpoint.html) 프로세스 중에 데이터베이스 서버 메모리의 데이터 페이지도 저장소로 플러시됩니다. |
| <b>원격 스토리지 | 모든 PostgreSQL 물리적 데이터 파일 및 WAL 파일은 데이터 중복성, 가용성 및 안정성을 보장 하기 위해 지역 내에 세 개의 데이터 복사본을 저장 하도록 설계 된 Azure Storage에 저장 됩니다. 저장소 계층은 데이터베이스 서버와도 독립적입니다. 실패 한 데이터베이스 서버에서 분리 하 고 몇 초 내에 새 데이터베이스 서버에 다시 연결할 수 있습니다. 또한 Azure Storage는 저장소 오류를 지속적으로 모니터링 합니다. 블록 손상이 감지 되 면 새 저장소 복사본을 인스턴스화하여 자동으로 수정 됩니다. |
| <b>관문 | 게이트웨이는 데이터베이스 프록시로 작동 하며 모든 클라이언트 연결을 데이터베이스 서버로 라우팅합니다. |

## <a name="planned-downtime-mitigation"></a>계획 된 가동 중지 시간 완화
Azure Database for PostgreSQL는 계획 된 가동 중지 시간 동안 고가용성을 제공 하도록 설계 되었습니다. 

![Azure PostgreSQL에서 탄력적 크기 조정 보기](./media/concepts-high-availability/azure-postgresql-elastic-scaling.png)

1. 몇 초 안에 PostgreSQL 데이터베이스 서버 확장 및 축소
2. 라우팅 클라이언트에서 프록시 역할을 하는 게이트웨이를 적절 한 데이터베이스 서버에 연결 합니다.
3. 저장소 확장은 가동 중지 시간 없이 수행할 수 있습니다. 원격 저장소는 장애 조치 (failover) 후 빠른 분리/다시 연결을 사용 하도록 설정 합니다.
다음은 몇 가지 계획 된 유지 관리 시나리오입니다.

| **시나리오** | **설명**|
| ------------ | ----------- |
| <b>수직 계산 확장/축소 | 사용자가 계산 확장/축소 작업을 수행 하면 크기 조정 된 계산 구성을 사용 하 여 새 데이터베이스 서버가 프로 비전 됩니다. 이전 데이터베이스 서버에서 활성 검사점을 완료할 수 있으며, 클라이언트 연결이 종료 되 고, 커밋되지 않은 트랜잭션이 취소 되 고 종료 됩니다. 그런 다음 이전 데이터베이스 서버에서 저장소를 분리 하 고 새 데이터베이스 서버에 연결 합니다. 클라이언트 응용 프로그램에서 연결을 다시 시도 하거나 새 연결을 시도 하는 경우 게이트웨이는 새 데이터베이스 서버로 연결 요청을 보냅니다.|
| <b>저장소 확장 | 저장소 확장은 온라인 작업 이며 데이터베이스 서버를 중단 하지 않습니다.|
| <b>새 소프트웨어 배포 (Azure) | 새 기능 출시 또는 버그 수정은 서비스의 계획 된 유지 관리의 일부로 자동으로 발생 합니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification)를 참조 하 고 [포털](https://aka.ms/servicehealthpm)도 확인 하세요.|
| <b>부 버전 업그레이드 | Azure에서 결정 하는 부 버전에 데이터베이스 서버를 자동으로 패치 Azure Database for PostgreSQL 합니다. 서비스의 계획 된 유지 관리의 일환으로 발생 합니다. 이로 인해 몇 초 후에 짧은 가동 중지 시간이 발생 하 고 데이터베이스 서버가 새 부 버전으로 자동으로 다시 시작 됩니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/postgresql/concepts-monitoring#planned-maintenance-notification)를 참조 하 고 [포털](https://aka.ms/servicehealthpm)도 확인 하세요.|


##  <a name="unplanned-downtime-mitigation"></a>계획 되지 않은 가동 중지 시간 완화

계획 되지 않은 가동 중지 시간은 기본 하드웨어 오류, 네트워킹 문제 및 소프트웨어 버그를 포함 하 여 예측할 수 없는 실패의 결과로 발생할 수 있습니다. 데이터베이스 서버가 예기치 않게 중단 되 면 새 데이터베이스 서버가 몇 초 안에 자동으로 프로 비전 됩니다. 원격 저장소는 새 데이터베이스 서버에 자동으로 연결 됩니다. PostgreSQL 엔진은 WAL 및 데이터베이스 파일을 사용 하 여 복구 작업을 수행 하 고, 클라이언트의 연결을 허용 하도록 데이터베이스 서버를 엽니다. 커밋되지 않은 트랜잭션은 손실 되며 응용 프로그램에서 다시 시도해 야 합니다. 계획 되지 않은 가동 중지 시간은 피할 수 없지만, Azure Database for PostgreSQL은 사용자의 개입 없이 데이터베이스 서버와 저장소 계층 모두에서 복구 작업을 자동으로 수행 하 여 가동 중지 시간을 완화 합니다. 


![Azure PostgreSQL의 고가용성 보기](./media/concepts-high-availability/azure-postgresql-built-in-high-availability.png)

1. Azure PostgreSQL 서버는 빠른 크기 조정 기능을 제공 합니다.
2. 클라이언트 연결을 적절 한 데이터베이스 서버로 라우팅하는 프록시로 역할을 하는 게이트웨이
3. 안정성, 가용성 및 중복성을 위한 세 가지 복사본을 포함 하는 Azure storage
4. 원격 저장소는 서버 장애 조치 (failover) 후에도 빠른 분리/다시 연결을 사용 하도록 설정 합니다.
   
### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>계획 되지 않은 가동 중지 시간: 오류 시나리오 및 서비스 복구
다음은 몇 가지 오류 시나리오와 Azure Database for PostgreSQL 자동으로 복구 하는 방법입니다.

| **시나리오** | **자동 복구** |
| ---------- | ---------- |
| <B>데이터베이스 서버 오류 | 일부 기본 하드웨어 오류로 인해 데이터베이스 서버 작동이 중지 되 면 활성 연결이 삭제 되 고 모든 처리 중인 트랜잭션이 중단 됩니다. 새 데이터베이스 서버는 자동으로 배포 되 고 원격 데이터 저장소는 새 데이터베이스 서버에 연결 됩니다. 데이터베이스 복구가 완료 된 후 클라이언트는 게이트웨이를 통해 새 데이터베이스 서버에 연결할 수 있습니다. <br /> <br /> RTO (복구 시간)는 오류가 발생 한 시점의 작업 (예: 대용량 트랜잭션 및 데이터베이스 서버 시작 프로세스 중에 수행할 복구 용량)을 비롯 한 다양 한 요인에 따라 달라 집니다. <br /> <br /> PostgreSQL 데이터베이스를 사용 하는 응용 프로그램은 삭제 된 연결과 실패 한 트랜잭션을 검색 하 고 다시 시도 하는 방식으로 빌드해야 합니다.  응용 프로그램을 다시 시도할 때 게이트웨이는 새로 만든 데이터베이스 서버로의 연결을 투명 하 게 리디렉션합니다. |
| <B>저장소 오류 | 응용 프로그램에는 디스크 오류 또는 물리적 블록 손상과 같은 저장소 관련 문제에 대 한 영향이 표시 되지 않습니다. 데이터는 3 개의 복사본에 저장 되므로 데이터의 복사본은 활성 저장소로 제공 됩니다. 블록 손상이 자동으로 수정 됩니다. 데이터의 복사본이 손실 되 면 데이터의 새 복사본이 자동으로 생성 됩니다. |

다음은 사용자 작업이 복구 되어야 하는 몇 가지 오류 시나리오입니다.

| **시나리오** | **복구 계획** |
| ---------- | ---------- |
| <b> 지역 오류 | 지역 오류는 드문 이벤트입니다. 그러나 지역 장애 로부터 보호 해야 하는 경우에는 재해 복구 (DR)를 위해 다른 지역에 하나 이상의 읽기 복제본을 구성할 수 있습니다. 자세한 내용은 읽기 복제본 만들기 및 관리에 대 한 [이 문서](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) 를 참조 하세요. 영역 수준 실패가 발생할 경우 다른 지역에 구성 된 읽기 복제본을 프로덕션 데이터베이스 서버로 수동으로 승격할 수 있습니다. |
| <b> 논리적/사용자 오류 | 실수로 삭제 된 테이블 또는 잘못 업데이트 된 데이터와 같은 사용자 오류 로부터 복구 하려면 오류가 발생 하기 직전까지 데이터를 복원 및 복구 하 여 PITR (지정 [시간 복구](https://docs.microsoft.com/azure/postgresql/concepts-backup) )를 수행 해야 합니다.<br> <br>  데이터베이스 서버의 모든 데이터베이스가 아니라 데이터베이스 또는 특정 테이블의 하위 집합만 복원 하려면 새 인스턴스에서 데이터베이스 서버를 복원 하 고 [pg_dump](https://www.postgresql.org/docs/11/app-pgdump.html)를 통해 테이블을 내보낸 다음 [pg_restore](https://www.postgresql.org/docs/11/app-pgrestore.html) 를 사용 하 여 해당 테이블을 데이터베이스로 복원할 수 있습니다. |



## <a name="summary"></a>요약

Azure Database for PostgreSQL는 데이터베이스 서버, 중복 저장소 및 게이트웨이에서 효율적인 라우팅의 빠른 다시 시작 기능을 제공 합니다. 추가 데이터 보호를 위해 지역에서 복제 되도록 백업을 구성 하 고 다른 지역에 하나 이상의 읽기 복제본을 배포할 수도 있습니다. 내재 된 고가용성 기능을 통해 Azure Database for PostgreSQL는 가장 일반적인 작동 중단 으로부터 데이터베이스를 보호 하 고, 업계 최고의 금융 지원 [99.99%의 가동 시간 SLA](https://azure.microsoft.com/support/legal/sla/postgresql)를 제공 합니다. 이러한 모든 가용성 및 안정성 기능을 통해 Azure는 중요 업무용 응용 프로그램을 실행 하는 데 이상적인 플랫폼이 될 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 지역](../availability-zones/az-overview.md) 에 대 한 자세한 정보
- [일시적인 연결 오류 처리](concepts-connectivity.md)에 대해 알아보기
- [읽기 복제본을 사용하여 데이터를 복제하는](howto-read-replicas-portal.md) 방법에 대해 알아보기
