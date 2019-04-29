---
title: Azure Database for MariaDB의 서버 로그
description: Azure Database for MariaDB에서 사용할 수 있는 로그와, 다양한 로깅 수준을 활성화하는 데 사용할 수 있는 매개 변수에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a26f61eb199d8f370e1a9dd010932dc868b74ae4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61041261"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 서버 로그
Azure Database for MariaDB에서는 사용자에게 느린 쿼리 로그를 제공합니다. 트랜잭션 로그에 대한 액세스는 지원되지 않습니다. 느린 쿼리 로그를 사용하여 문제 해결을 위한 성능 병목을 파악할 수 있습니다.

느린 쿼리 로그에 대한 자세한 내용은 MariaDB 설명서에서 [느린 쿼리 로그](https://mariadb.com/kb/en/library/slow-query-log-overview/)를 참조하세요.

## <a name="access-server-logs"></a>서버 로그 액세스
Azure Portal 및 Azure CLI를 사용하여 Azure Database for MariaDB 서버 로그를 나열하고 다운로드할 수 있습니다.

Azure Portal에서 Azure Database for MariaDB 서버를 찾습니다. **모니터링** 머리글 아레에서 **서버 로그** 페이지를 선택합니다.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>로그 보존
로그는 생성 시점에서 최대 7일까지 사용할 수 있습니다. 사용 가능한 로그의 전체 크기가 7GB를 초과하면 여유 공간이 생길 때까지 가장 오래된 파일이 삭제됩니다.

즉, 24시간이 지나거나 전체 크기가 7GB를 초과할 때마다(먼저 해당되는 쪽) 로그가 가장 오래된 파일부터 삭제됩니다.

## <a name="configure-logging"></a>로깅 구성
기본적으로 느린 쿼리 로그는 비활성화됩니다. 활성화하려면 slow_query_log를 ON으로 설정합니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- **long_query_time**: 쿼리가 기록되는 long_query_time(초)보다 쿼리가 오래 걸릴 경우 기본값은 10초입니다.
- **log_slow_admin_statements**: ON에 slow_query_log에 쓰여진 문에서 ALTER_TABLE 및 ANALYZE_TABLE 등과 같은 관리 문이 포함된 경우
- **log_queries_not_using_indexes**: 인덱스를 사용하지 않는 쿼리가 slow_query_log에 기록되는지 여부를 결정합니다.
- **log_throttle_queries_not_using_indexes**: 이 매개 변수는 느린 쿼리 로그에 쓸 수 있는 인덱스가 아닌 쿼리 수의 한도를 결정합니다. 이 매개 변수는 log_queries_not_using_indexes가 ON으로 설정된 경우 적용됩니다.

느린 쿼리 로그 매개 변수의 전체 설명은 MariaDB [느린 쿼리 로그 설명서](https://mariadb.com/kb/en/library/slow-query-log-overview/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 서버 로그를 구성하고 액세스하는 방법](howto-configure-server-logs-portal.md)
