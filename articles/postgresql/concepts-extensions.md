---
title: 확장-Azure Database for PostgreSQL-단일 서버
description: Azure Database for PostgreSQL 단일 서버에서 사용 가능한 Postgres 확장에 대해 알아봅니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201274"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL의 PostgreSQL 확장-단일 서버
PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장은 단일 명령을 사용 하 여 데이터베이스에서 로드 하거나 제거할 수 있는 단일 패키지에서 여러 관련 SQL 개체를 함께 번들로 묶습니다. 데이터베이스에 로드 된 후 확장은 기본 제공 기능 처럼 작동 합니다.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 확장을 사용하는 방법
PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 psql 도구에서  [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html)  명령을 실행하여 패키지 개체를 데이터베이스에 로드합니다.

Azure Database for PostgreSQL은 아래에 나열 된 대로 키 확장의 하위 집합을 지원 합니다. 이 정보를 `SELECT * FROM pg_available_extensions;`를 실행하여 얻을 수도 있습니다. 나열 된 확장 이외의 확장은 지원 되지 않습니다. Azure Database for PostgreSQL에서 사용자 고유의 확장을 만들 수 없습니다.

## <a name="postgres-11-extensions"></a>Postgres 11 확장

다음 확장은 Postgres 버전 11이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다. 

> [!div class="mx-tableFixed"]
> | **확장명**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 데이터 집합 예제|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | 요점의 공통 데이터 형식 인덱싱 지원|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | 대/소문자를 구분 하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | 다차원 큐브에 대 한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | 정수에 대 한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | 지구 표면에서 큰 원 거리를 계산 합니다.|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | (키, 값) 쌍 집합을 저장 하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | PostgreSQL에 대 한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | 정수의 1 차원 배열에 대 한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | 국제 제품 번호 매기기 표준에 대 한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | 계층적 트리 구조에 대 한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상용 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2 critical           | pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사 합니다.|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | 시간 또는 ID로 분할 된 테이블을 관리 하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | 사전 웜 관계 데이터|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | 실행 된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | trigrams을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) 신뢰할 수 있는 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | PostGIS geometry, geography 및 래스터 공간 형식 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tiger geocoder 및 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | remote PostgreSQL servers에 대 한 외부 데이터 래퍼|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | 크로스탭을 포함 하 여 전체 테이블을 조작 하는 함수|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | 시계열 데이터에 대 한 확장 가능한 삽입 및 복잡 한 쿼리를 사용 합니다.|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | 강조를 제거 하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | Uuid (범용 고유 식별자)를 생성 합니다.|

## <a name="postgres-10-extensions"></a>Postgres 10 확장 

다음 확장은 Postgres 버전 10이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | **확장명**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Address Standardizer US 데이터 집합 예제|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | 요점의 공통 데이터 형식 인덱싱 지원|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | 자동 암호화 된 암호에 대 한 데이터 형식|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | 대/소문자를 구분 하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | 다차원 큐브에 대 한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | 정수에 대 한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | 지구 표면에서 큰 원 거리를 계산 합니다.|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | (키, 값) 쌍 집합을 저장 하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL에 대 한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | 정수의 1 차원 배열에 대 한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | 국제 제품 번호 매기기 표준에 대 한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | 계층적 트리 구조에 대 한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상용 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | 공유 버퍼 캐시를 검사 합니다.|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 시간 또는 ID로 분할 된 테이블을 관리 하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | 사전 웜 관계 데이터|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | 실행 된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | trigrams을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) 신뢰할 수 있는 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.4.3           | PostGIS geometry, geography 및 래스터 공간 형식 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tiger geocoder 및 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | remote PostgreSQL servers에 대 한 외부 데이터 래퍼|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | 크로스탭을 포함 하 여 전체 테이블을 조작 하는 함수|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 시계열 데이터에 대 한 확장 가능한 삽입 및 복잡 한 쿼리를 사용 합니다.|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | 강조를 제거 하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | Uuid (범용 고유 식별자)를 생성 합니다.|

## <a name="postgres-96-extensions"></a>Postgres 9.6 확장 

다음 확장은 Postgres 버전 9.6이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | **확장명**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Address Standardizer US 데이터 집합 예제|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | 요점의 공통 데이터 형식 인덱싱 지원|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | 자동 암호화 된 암호에 대 한 데이터 형식|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | 대/소문자를 구분 하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | 다차원 큐브에 대 한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | 정수에 대 한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | 지구 표면에서 큰 원 거리를 계산 합니다.|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | (키, 값) 쌍 집합을 저장 하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL에 대 한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | 정수의 1 차원 배열에 대 한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | 국제 제품 번호 매기기 표준에 대 한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | 계층적 트리 구조에 대 한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상용 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | 공유 버퍼 캐시를 검사 합니다.|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 시간 또는 ID로 분할 된 테이블을 관리 하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | 사전 웜 관계 데이터|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | 실행 된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | trigrams을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) 신뢰할 수 있는 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.3.2           | PostGIS geometry, geography 및 래스터 공간 형식 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tiger geocoder 및 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | remote PostgreSQL servers에 대 한 외부 데이터 래퍼|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | 크로스탭을 포함 하 여 전체 테이블을 조작 하는 함수|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | 시계열 데이터에 대 한 확장 가능한 삽입 및 복잡 한 쿼리를 사용 합니다.|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | 강조를 제거 하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | Uuid (범용 고유 식별자)를 생성 합니다.|

## <a name="postgres-95-extensions"></a>Postgres 9.5 확장 

다음 확장은 Postgres 버전 9.5이 있는 Azure Database for PostgreSQL 서버에서 사용할 수 있습니다.

> [!div class="mx-tableFixed"]
> | **확장명**| **확장 버전** | **설명** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Address Standardizer US 데이터 집합 예제|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | GIN의 공통 데이터 형식 인덱싱 지원|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | 요점의 공통 데이터 형식 인덱싱 지원|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | 자동 암호화 된 암호에 대 한 데이터 형식|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | 대/소문자를 구분 하지 않는 문자열의 데이터 형식|
> |[cube](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | 다차원 큐브에 대 한 데이터 형식|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | 데이터베이스 내에서 다른 PostgreSQL 데이터베이스에 연결|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | 정수에 대 한 텍스트 검색 사전 템플릿|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | 지구 표면에서 큰 원 거리를 계산 합니다.|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | 문자열 간의 유사성 및 거리 결정|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | (키, 값) 쌍 집합을 저장 하기 위한 데이터 형식|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | PostgreSQL에 대 한 가상 인덱스|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | 정수의 1 차원 배열에 대 한 함수, 연산자 및 인덱스 지원|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | 국제 제품 번호 매기기 표준에 대 한 데이터 형식|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | 계층적 트리 구조에 대 한 데이터 형식|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | 상용 RDBMS에서 함수 및 패키지의 하위 집합을 에뮬레이트하는 함수 및 연산자|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | 감사 기능 제공|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | 암호화 함수|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | 행 수준 잠금 정보 표시|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | 튜플 수준 통계 표시|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | 공유 버퍼 캐시를 검사 합니다.|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | 시간 또는 ID로 분할 된 테이블을 관리 하기 위한 확장|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | 사전 웜 관계 데이터|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | 실행 된 모든 SQL 문의 실행 통계 추적|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | trigrams을 기반으로 하는 텍스트 유사성 측정 및 인덱스 검색|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL 절차적 언어|
> |[postgis](https://www.postgis.net/)                      | 2.3.0           | PostGIS geometry, geography 및 래스터 공간 형식 및 함수|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL 함수|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tiger geocoder 및 reverse geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS 토폴로지 공간 형식 및 함수|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | remote PostgreSQL servers에 대 한 외부 데이터 래퍼|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | 크로스탭을 포함 하 여 전체 테이블을 조작 하는 함수|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | 강조를 제거 하는 텍스트 검색 사전|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | Uuid (범용 고유 식별자)를 생성 합니다.|


## <a name="pg_stat_statements"></a>pg_stat_statements
[Pg_stat_statements 확장](https://www.postgresql.org/docs/current/pgstatstatements.html) 은 SQL 문의 실행 통계를 추적 하는 방법을 제공 하기 위해 모든 Azure Database for PostgreSQL 서버에 미리 로드 됩니다.
확장을 통해 어떤 명령문을 계산할지 제어하는 `pg_stat_statements.track` 설정의 기본값은 `top`이며 이것은 클라이언트가 직접 실행하는 모든 명령문이 추적된다는 의미입니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)이나 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)를 통해 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements를 통해 제공되는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 상충 관계가 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우에는 `pg_stat_statements.track`을 `none`으로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스가 pg_stat_statements에 기반하여 쿼리 성능 정보를 제공할 수 있으므로 여기에 해당하는 경우인지 여부를 확인하십시오.

## <a name="dblink-and-postgres_fdw"></a>ablink 및 postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) PostgreSQL 서버에서 다른 서버에 연결 하거나 동일한 서버의 다른 데이터베이스에 연결할 수 있습니다. [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) 받는 서버는 해당 방화벽을 통한 보내는 서버의 연결을 허용해야 합니다. 이러한 확장을 사용하여 Azure Database for PostgreSQL 서버 간을 연결하는 경우 "Azure 서비스 방문 허용"을 켜기로 설정하여 이 작업을 수행할 수 있습니다. 확장을 사용하여 동일한 서버로 루프백하는 데도 이 작업이 필요합니다. "Azure 서비스 방문 허용" 설정은 Postgres 서버의 Azure Portal 페이지에서 연결 보안 아래에 있습니다. "Azure 서비스에 대 한 액세스 허용"을 설정 하면 모든 Azure Ip가 허용 목록에 배치 됩니다.

다른 Azure Database for PostgreSQL 서버에 대한 연결을 제외하고, 현재 Azure Database for PostgreSQL에서 아웃바운드 연결은 지원되지 않습니다.

## <a name="uuid"></a>uuid
[Uuid-ossp 확장](https://www.postgresql.org/docs/current/uuid-ossp.html)의 `uuid_generate_v4()`를 사용 하려는 경우 성능 이점을 위해 [pgcrypto 확장](https://www.postgresql.org/docs/current/pgcrypto.html) 의 `gen_random_uuid()`와 비교 하는 것이 좋습니다.

## <a name="pgaudit"></a>pgAudit
[Pgaudit 확장](https://github.com/pgaudit/pgaudit/blob/master/README.md) 은 세션 및 개체 감사 로깅을 제공 합니다. Azure Database for PostgreSQL에서이 확장을 사용 하는 방법을 알아보려면 [감사 개념 문서](concepts-audit.md)를 참조 하세요. 

## <a name="pg_prewarm"></a>pg_prewarm
Pg_prewarm 확장은 관계형 데이터를 캐시로 로드 합니다. 캐시를 미리 준비 하는 것은 다시 시작 후 쿼리를 처음 실행 하는 경우 응답 시간이 더 나은 것을 의미 합니다. Postgres 10이 하에서는 prewarming [함수](https://www.postgresql.org/docs/10/pgprewarm.html)를 사용 하 여 사전 준비를 수동으로 수행 합니다.

Postgres 11 이상에서 사전 준비를 [자동으로](https://www.postgresql.org/docs/current/pgprewarm.html)수행 하도록 구성할 수 있습니다. `shared_preload_libraries` 매개 변수의 목록에 pg_prewarm를 포함 하 고 서버를 다시 시작 하 여 변경 내용을 적용 해야 합니다. [Azure Portal](howto-configure-server-parameters-using-portal.md), [CLI](howto-configure-server-parameters-using-cli.md), REST API 또는 ARM 템플릿에서 매개 변수를 설정할 수 있습니다. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB는 PostgreSQL에 대한 확장으로 패키지되는 시계열 데이터베이스입니다. TimescaleDB는 시간 지향적인 분석 함수, 최적화를 제공하고 시계열 작업용 Postgres를 확장합니다.

[TimescaleDB에 대 한 자세한](https://docs.timescale.com/latest)정보, 시간 간격의 등록 상표 [, inc.](https://www.timescale.com/). Azure Database for PostgreSQL는 날짜 표시줄의 오픈 소스 버전을 제공 합니다. 이 버전에서 사용할 수 있는 날짜/시간 기능에 대 한 자세한 내용은 날짜/시간 [제품 비교](https://www.timescale.com/products/)를 참조 하세요.

### <a name="installing-timescaledb"></a>TimescaleDB 설치
TimescaleDB를 설치하려면 서버의 공유 미리 로드 라이브러리에 포함해야 합니다. Postgres의 `shared_preload_libraries` 매개 변수를 변경 하려면 **서버를 다시 시작** 해야 적용 됩니다. [Azure Portal](howto-configure-server-parameters-using-portal.md) 또는 [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용 하 여 매개 변수를 변경할 수 있습니다.

[Azure portal](https://portal.azure.com/) 사용:

1. Azure Database for PostgreSQL 서버를 선택합니다.

2. 사이드바에서 **서버 매개 변수**를 선택합니다.

3. `shared_preload_libraries` 매개 변수를 검색합니다.

4. **TimescaleDB**를 선택 합니다.

5. 변경 내용을 유지하기 위해 **저장**을 선택합니다. 변경 내용이 저장되면 알림을 받게 됩니다. 

6. 알림 후 이러한 변경 내용을 적용하기 위해 서버를 **다시 시작**합니다. 서버를 다시 시작하는 방법을 알아보려면 [Azure Database for PostgreSQL 서버 다시 시작](howto-restart-server-portal.md)을 참조하세요.


이제 Postgres 데이터베이스에서 TimescaleDB를 사용 설정할 수 있습니다. 데이터베이스에 연결하고 다음 명령을 실행합니다.
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 오류가 발생하는 경우 shared_preload_libraries를 저장한 후 [서버를 다시 시작](howto-restart-server-portal.md)했는지 확인합니다. 

이제 TimescaleDB hypertable을 [처음부터](https://docs.timescale.com/getting-started/creating-hypertables) 만들거나  [PostgreSQL에서 기존 시계열 데이터](https://docs.timescale.com/getting-started/migrating-data)를 마이그레이션할 수 있습니다.

### <a name="restoring-a-timescale-database"></a>시간 간격 데이터베이스 복원
Pg_dump 및 pg_restore를 사용 하 여 시간 간격 데이터베이스를 복원 하려면 대상 데이터베이스에서 두 개의 도우미 프로시저 (`timescaledb_pre_restore()` 및 `timescaledb_post restore()`)를 실행 해야 합니다.

먼저 대상 데이터베이스를 준비 합니다.

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

이제 원본 데이터베이스에서 pg_dump를 실행 하 고 pg_restore 수 있습니다. 복원 후 복원 된 데이터베이스에서 다음 명령을 실행 해야 합니다.

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>다음 단계
사용하려는 확장이 표시되지 않으면 알려주세요. [피드백 포럼](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)에서 기존 요청에 투표 하거나 새 피드백 요청을 만듭니다.
