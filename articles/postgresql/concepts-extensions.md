---
title: Azure Database for PostgreSQL에서 PostgreSQL 확장 사용
description: PostgreSQL용 Azure 데이터베이스에서 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 7d052ee2d3d3bdf6cca99dd6a91b88176983113f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559736"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스의 PostgreSQL 확장
PostgreSQL은 확장을 사용하여 데이터베이스의 기능을 확장하는 방법을 제공합니다. 확장을 통해 관련된 여러 SQL 개체를 단일 패키지에 번들로 묶을 수 있으며 단일 명령을 사용해서 데이터베이스에서 로드하거나 제거할 수 있습니다. 데이터베이스에 로드된 후에 확장은 기본 제공 기능으로 작동할 수 있습니다. PostgreSQL 확장에 대한 자세한 내용은  [관련 개체를 확장으로 패키지](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)를 참조하세요.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL 확장을 사용하는 방법
PostgreSQL 확장을 사용하려면 먼저 데이터베이스에 설치해야 합니다. 특정 확장을 설치하려면 psql 도구에서  [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  명령을 실행하여 패키지 개체를 데이터베이스에 로드합니다.

PostgreSQL용 Azure 데이터베이스는 현재 여기에 나열된 대로 주요 확장의 일부만 지원합니다. 나열되지 않은 확장은 지원되지 않습니다. PostgreSQL용 Azure 데이터베이스 서비스로 고유한 확장을 만들 수는 없습니다.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>PostgreSQL용 Azure 데이터베이스에서 지원하는 확장
다음 표에는 PostgreSQL용 Azure 데이터베이스에서 현재 지원하는 표준 PostgreSQL 확장이 나와 있습니다. 이 정보를 `SELECT * FROM pg_available_extensions;`를 실행하여 얻을 수도 있습니다.

### <a name="data-types-extensions"></a>데이터 형식 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | 자동으로 암호화된 암호에 대한 데이터 형식을 제공합니다. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 대/소문자 구분 문자 문자열 형식을 제공합니다. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | 다차원 큐브의 데이터 형식을 제공합니다. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 키/값 쌍 집합을 저장하기 위한 데이터 형식을 제공합니다. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | 국제 제품 번호 매기기 표준에 대한 데이터 형식을 제공합니다. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | 계층적 트리 구조에 대한 데이터 형식을 제공합니다. |

### <a name="functions-extensions"></a>함수 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | 지구 표면의 대원 거리를 계산하는 방법을 제공합니다. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 문자열 간 유사성 및 거리를 확인하기 위한 몇 가지 함수를 제공합니다. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | null 없는 정수 배열을 조작하기 위한 함수 및 연산자를 제공합니다. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 암호화 함수를 제공합니다. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 시간 또는 ID로 분할된 테이블을 관리합니다. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | trigram 일치를 기준으로 영숫자 텍스트의 유사성을 확인하기 위한 함수 및 연산자를 제공합니다. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | 크로스탭을 비롯하여 전체 테이블을 조작하는 함수를 제공합니다. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | UUID(범용 고유 식별자)를 생성합니다. |

### <a name="full-text-search-extensions"></a>전체 텍스트 검색 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | 정수에 대한 텍스트 검색 사전 템플릿을 제공합니다. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Lexemes에서 악센트(분음 기호)를 제거하는 텍스트 검색 사전입니다. |

### <a name="index-types-extensions"></a>인덱스 형식 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 특정 데이터 형식에 대해 B-트리 유사 동작을 구현하는 샘플 GIN 연산자 클래스를 제공합니다. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | B-트리를 구현하는 GiST 인덱스 연산자 클래스를 제공합니다. |

### <a name="language-extensions"></a>언어 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 로드 가능 절차 언어. |
> | [plv8](https://plv8.github.io/) | 저장 프로시저, 트리거 등에 사용할 수 있는 PostgreSQL용 Javascript 언어 확장입니다. |

### <a name="miscellaneous-extensions"></a>기타 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 공유 버퍼 캐시에서 일어나는 작업을 실시간으로 검사하기 위한 수단을 제공합니다. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 관계 데이터를 버퍼 캐시에 로드하는 방법을 제공합니다. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. (이 확장에 대한 설명은 아래를 참조하세요.) |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | 행 수준 잠금 정보를 표시하는 방법을 제공합니다. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | 튜플 수준 통계를 표시하는 방법을 제공합니다. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 외부 PostgreSQL 서버에 저장된 데이터에 액세스하는 데 사용되는 외부 데이터 래퍼입니다. (이 확장에 대한 설명은 아래를 참조하세요.)|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | CPU나 디스크 비용이 들지 않는 가상의 인덱스를 만드는 방법을 제공합니다. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | 데이터베이스 세션 내에서 다른 PostgreSQL 데이터베이스로의 연결을 지원하는 모듈입니다. (이 확장에 대한 설명은 아래를 참조하세요.) |


### <a name="postgis-extensions"></a>PostGIS 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [PostGIS](http://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | PostgreSQL에 대한 공간 및 지리적 개체입니다. |
> | address\_standardizer, address\_standardizer\_data\_us | 주소를 구성 요소로 구문 분석하는 데 사용됩니다. 지오코딩 주소 정규화 단계를 지원하는 데 사용됩니다. |
> | [pgrouting](https://pgrouting.org/) | 지리 공간적 라우팅 기능을 제공하기 위해 PostGIS / PostgreSQL 지리 공간적 데이터베이스를 확장합니다. |


### <a name="time-series-extensions"></a>시계열 확장

> [!div class="mx-tableFixed"]
> | **확장** | **설명** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | 지원 자동화에 대 한 분할을 더 빠르게 수집 하 고 쿼리 하는 시계열 SQL 데이터베이스입니다. 시간 기반 분석 기능을 최적화를 제공 하 고 시계열 워크 로드에 대 한 PostgreSQL을 확장 합니다. TimescaleDB에서 개발 하는 및의 등록된 상표 [시간 간격, Inc.](https://www.timescale.com/) (이 확장에 대한 설명은 아래를 참조하세요.) |


## <a name="pgstatstatements"></a>pg_stat_statements
[pg\_stat\_statements 확장](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html)은 모든 Azure Database for PostgreSQL 서버에 미리 로드되어 SQL 문의 실행 통계를 추적하는 수단을 제공합니다.
확장을 통해 어떤 명령문을 계산할지 제어하는 `pg_stat_statements.track` 설정의 기본값은 `top`이며 이것은 클라이언트가 직접 실행하는 모든 명령문이 추적된다는 의미입니다. 다른 두 가지 추적 수준은 `none`과 `all`입니다. 이 설정은 [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal)이나 [Azure CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)를 통해 서버 매개 변수로 구성할 수 있습니다.

pg_stat_statements를 통해 제공되는 쿼리 실행 정보와 각 SQL 문을 기록할 때 서버 성능에 미치는 영향 사이에는 상충 관계가 있습니다. pg_stat_statements 확장을 적극적으로 사용하지 않는 경우에는 `pg_stat_statements.track`을 `none`으로 설정하는 것이 좋습니다. 일부 타사 모니터링 서비스가 pg_stat_statements에 기반하여 쿼리 성능 정보를 제공할 수 있으므로 여기에 해당하는 경우인지 여부를 확인하십시오.

## <a name="dblink-and-postgresfdw"></a>dblink 및 postgres_fdw
dblink 및 postgres_fdw를 사용하여 한 PostgreSQL 서버에서 다른 PostgreSQL 서버로 또는 동일한 서버의 다른 데이터베이스로 연결할 수 있습니다. 받는 서버는 해당 방화벽을 통한 보내는 서버의 연결을 허용해야 합니다. 이러한 확장을 사용하여 Azure Database for PostgreSQL 서버 간을 연결하는 경우 "Azure 서비스에 대한 액세스 허용"을 켜기로 설정하여 이 작업을 수행할 수 있습니다. 확장을 사용하여 동일한 서버로 루프백하는 데도 이 작업이 필요합니다. "Azure 서비스에 대한 액세스 허용" 설정은 Postgres 서버의 Azure Portal 페이지에서 연결 보안 아래에 있습니다. "Azure 서비스에 대한 액세스 허용"을 켜기로 설정하면 모든 Azure IP가 허용 목록이 추가됩니다.

현재 PostgreSQL 용 Azure Database에서 아웃 바운드 연결은 지원 되지 않습니다, 다른 Azure Database for PostgreSQL 서버에 대 한 연결을 제외 하 고.

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB는 시계열 데이터베이스는 PostgreSQL에 대 한 확장으로 패키지입니다. TimescaleDB 시간 지향적인 분석 기능을 최적화를 제공 하 고 시계열 작업용 Postgres를 확장 합니다.

[TimescaleDB에 자세히 알아보려면](https://docs.timescale.com/latest)에서의 등록된 상표 [시간 간격, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>TimescaleDB 설치
TimescaleDB를 설치 하려면 서버의 공유 미리 로드 라이브러리에 포함 해야 합니다. 미리 로드 라이브러리를 공유 하는 Postgres의 변경이 필요는 **서버를 다시 부팅** 적용 합니다.

> [!NOTE]
> TimescaleDB은 PostgreSQL 버전 9.6 및 10에 대 한 Azure Database에서 사용할 수 있습니다.

사용 하 여 [Azure portal](https://portal.azure.com/):

1. Azure Database for PostgreSQL 서버를 선택합니다.

2. 보충 기사에서 선택 **서버 매개 변수**합니다.

3. `shared_preload_libraries` 매개 변수를 검색합니다.

4. 복사 하 고 다음 값으로 붙여 넣습니다. `shared_preload_libraries`
   ```
   timescaledb
   ```

5. 선택 **저장할** 변경 내용을 유지 합니다. 변경 내용이 저장 되 면 알림을 받게 됩니다. 

6. 알림 후 **다시 시작** 서버에 이러한 변경 내용을 적용 합니다. 서버를 다시 시작하는 방법을 알아보려면 [Azure Database for PostgreSQL 서버 다시 시작](howto-restart-server-portal.md)을 참조하세요.


Postgres 데이터베이스의 TimescaleDB를 이제 사용할 수 있습니다. 데이터베이스에 연결 하 고 다음 명령을 실행 합니다.
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> 오류가 발생 하는 경우 확인 했는지 [서버를 다시 시작](howto-restart-server-portal.md) shared_preload_libraries 저장 한 후입니다. 

이제 TimescaleDB hypertable를 만들 수 있습니다 [처음부터](https://docs.timescale.com/getting-started/creating-hypertables) 마이그레이션하거나 [PostgreSQL에서 시계열 데이터를 기존](https://docs.timescale.com/getting-started/migrating-data)합니다.


## <a name="next-steps"></a>다음 단계
사용하려는 확장이 표시되지 않으면 알려주세요. [고객 사용자 의견 포럼](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)에서 기존 요청에 투표하거나 새 사용자 의견 및 요청을 만드세요.
