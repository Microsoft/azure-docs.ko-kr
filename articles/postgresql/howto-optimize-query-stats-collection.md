---
title: 쿼리 통계 컬렉션 최적화 - Azure Database for PostgreSQL - 단일 서버
description: 이 문서에서는 Azure Database for PostgreSQL - 단일 서버에서 쿼리 통계 컬렉션을 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: bc731f6f6a5a60bce0851bf8fe5874f7149f3899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90901460"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - 단일 서버에서 쿼리 통계 컬렉션 최적화
이 문서에서는 Azure Database for PostgreSQL 서버에서 쿼리 통계 컬렉션을 최적화하는 방법을 설명합니다.

## <a name="use-pg_stats_statements"></a>pg_stats_statements 사용
**Pg_stat_statements** 는 Azure Database for PostgreSQL에서 기본적으로 사용하도록 설정된 PostgreSQL 확장입니다. 이 확장은 서버에서 실행되는 모든 SQL 문의 실행 통계를 추적하는 수단을 제공합니다. 이 모듈은 모든 쿼리 실행에 연결되며 적지 않은 성능 비용이 수반됩니다. **pg_stat_statements** 을 사용하면 디스크 파일에 쿼리 텍스트 쓰기를 해야 합니다.

장기 쿼리 텍스트가 포함된 고유한 쿼리가 있거나 적극적으로 **pg_stat_statements** 를 모니터링하지 않는 경우 최상의 성능을 위해 **pg_stat_statements** 를 사용하지 않도록 설정합니다. 그러려면 설정을 `pg_stat_statements.track = NONE`으로 변경합니다.

일부 고객 워크로드는 **pg_stat_statements** 를 사용하지 않도록 설정한 경우 최대 50퍼센트의 성능 향상을 보였습니다. pg_stat_statements를 사용하지 않도록 설정한 경우 발생하는 단점은 성능 문제를 해결할 수 없다는 것입니다.

`pg_stat_statements.track = NONE`을 설정하려면

- Azure Portal에서 [PostgreSQL 리소스 관리 페이지로 이동하고 서버 매개 변수 블레이드를 선택합니다](howto-configure-server-parameters-using-portal.md).

  :::image type="content" source="./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png" alt-text="PostgreSQL 서버 매개 변수 블레이드":::

- [Azure CLI](howto-configure-server-parameters-using-cli.md)를 사용하여 az postgres server configuration set to `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>쿼리 저장소 사용 
Azure Database for PostgreSQL의 [쿼리 저장소](concepts-query-store.md) 기능은 쿼리 성능을 추적하는 더 효과적인 방법을 제공합니다. *pg_stats_statements* 를 사용하지 않고 기능을 사용하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계
[Azure Portal](howto-configure-server-parameters-using-portal.md)에서 `pg_stat_statements.track = NONE`을 설정하거나 [Azure CLI](howto-configure-server-parameters-using-cli.md)을 사용하는 것이 좋습니다.

자세한 내용은 다음을 참조하세요. 
- [쿼리 저장소 사용 시나리오](concepts-query-store-scenarios.md) 
- [쿼리 저장소 모범 사례](concepts-query-store-best-practices.md) 
