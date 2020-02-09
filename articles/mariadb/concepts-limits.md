---
title: 제한 사항-Azure Database for MariaDB
description: 이 문서에서는 연결 수 및 저장소 엔진 옵션과 같은 Azure Database for MariaDB의 제한 사항을 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 1f5824f349650e340e395221785266096da16d6f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969550"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 제한 사항
다음 섹션에서는 데이터베이스 서비스의 용량, 저장소 엔진 지원, 권한 지원, 데이터 조작 문 지원 및 기능 제한에 대해 설명 합니다.

## <a name="maximum-connections"></a>Kapcsolatok maximális száma
가격 책정 계층 및 vCore당 최대 연결 수는 다음과 같습니다.

|**Tarifacsomag**|**vCore**| **최대 연결**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Általános rendeltetés| 2| 600|
|Általános rendeltetés| 4| 1250|
|Általános rendeltetés| 8| 2500|
|Általános rendeltetés| 16| 5000|
|Általános rendeltetés| 32| 10000|
|Általános rendeltetés| 64| 20000|
|Memóriára optimalizált| 2| 800|
|Memóriára optimalizált| 4| 2500|
|Memóriára optimalizált| 8| 5000|
|Memóriára optimalizált| 16| 10000|
|Memóriára optimalizált| 32| 20000|

연결이 제한을 초과 하는 경우 다음과 같은 오류가 표시 될 수 있습니다.
> 오류 1040 (08004): 연결이 너무 많음

## <a name="storage-engine-support"></a>저장소 엔진 지원

### <a name="supported"></a>Támogatott
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [RAM](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nem támogatott
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [블랙 홀](https://mariadb.com/kb/en/library/blackhole/)
- [보관](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>권한 지원

### <a name="unsupported"></a>Nem támogatott
- DBA 역할: 많은 서버 매개 변수 및 설정으로 인해 실수로 서버 성능이 저하 되거나 DBMS의 ACID 속성이 무효화 될 수 있습니다. 따라서 제품 수준에서 서비스 무결성 및 SLA를 유지 하기 위해이 서비스는 DBA 역할을 노출 하지 않습니다. 새 데이터베이스 인스턴스를 만들 때 생성 되는 기본 사용자 계정을 사용 하 여 관리 되는 데이터베이스 인스턴스에서 대부분의 DDL 및 DML 문을 수행할 수 있습니다.
- SUPER 권한: 비슷하게 [슈퍼 권한도](https://mariadb.com/kb/en/library/grant/#global-privileges) 제한 됩니다.
- DEFINER:를 만들고 제한 되는 슈퍼 권한이 필요 합니다. 백업을 사용 하 여 데이터를 가져오는 경우 `CREATE DEFINER` 명령을 수동으로 제거 하거나 mysqldump를 수행할 때 `--skip-definer` 명령을 사용 하 여 제거 합니다.

## <a name="data-manipulation-statement-support"></a>데이터 조작 문 지원

### <a name="supported"></a>Támogatott
- `LOAD DATA INFILE` 지원 되지만 `[LOCAL]` 매개 변수를 지정 하 고 UNC 경로 (SMB를 통해 탑재 된 Azure storage)로 전달 해야 합니다.

### <a name="unsupported"></a>Nem támogatott
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>기능 제한 사항

### <a name="scale-operations"></a>크기 조정 작업
- 기본 가격 책정 계층에 대 한 동적 크기 조정은 현재 지원 되지 않습니다.
- 서버 저장소 크기를 줄이는 것은 지원 되지 않습니다.

### <a name="server-version-upgrades"></a>서버 버전 업그레이드
- 주 데이터베이스 엔진 버전 간의 자동화 된 마이그레이션은 현재 지원 되지 않습니다.

### <a name="point-in-time-restore"></a>Időponthoz kötött visszaállítás
- PITR 기능을 사용 하는 경우 새 서버는 기반으로 하는 서버와 동일한 구성으로 만들어집니다.
- 삭제 된 서버 복원은 지원 되지 않습니다.

### <a name="subscription-management"></a>Előfizetés-kezelés
- 구독 및 리소스 그룹에서 미리 만들어진 서버를 동적으로 이동 하는 기능은 현재 지원 되지 않습니다.

### <a name="vnet-service-endpoints"></a>VNet 서비스 엔드포인트
- VNet 서비스 엔드포인트에 대 한 지원은 범용 및 메모리 액세스에 최적화 된 서버에만 해당 됩니다.

### <a name="storage-size"></a>저장소 크기
- 가격 계층별 저장소 크기 제한에 대해서는 [가격 책정 계층](concepts-pricing-tiers.md)을 참조하세요.

## <a name="current-known-issues"></a>현재 알려진 문제
- MariaDB 서버 인스턴스는 연결이 설정 된 후 잘못 된 서버 버전을 표시 합니다. 올바른 서버 인스턴스 엔진 버전을 가져오려면 `select version();` 명령을 사용 합니다.

## <a name="next-steps"></a>Következő lépések
- [각 서비스 계층에서 사용할 수 있는 기능](concepts-pricing-tiers.md)
- [지원 되는 Aadb 데이터베이스 버전](concepts-supported-versions.md)
