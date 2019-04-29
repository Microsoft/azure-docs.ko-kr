---
title: Azure Monitor 로그 쿼리 | Microsoft Docs
description: Azure Monitor에서 로그 쿼리를 작성하는 방법을 알아보기 위한 리소스 참조입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425965"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리
Azure Monitor 로그는 Azure Data Explorer에서 빌드되고, Azure Monitor 로그 쿼리는 동일한 Kusto 쿼리 언어의 한 버전을 사용합니다. [Kusto 쿼리 언어 문서](/azure/kusto/query)에는 언어에 대한 모든 세부 정보가 나와 있으며, Azure Monitor 로그 쿼리 작성을 위한 주요 리소스로 참조해야 합니다. 이 페이지에는 쿼리를 작성하는 방법 및 Azure Monitor 언어 구현과의 차이점을 알아보기 위한 다른 리소스의 링크가 나와 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>시작

- [Azure Monitor Log Analytics를 사용 하 여 시작](get-started-portal.md) 는 쿼리를 작성 하 고 Azure portal에서 결과 사용 하 여 작업에 대 한 단원.
- [Azure Monitor 로그 쿼리 시작](get-started-queries.md)은 Azure Monitor 로그 데이터를 사용하여 쿼리를 작성하는 방법에 대한 단원입니다.

## <a name="concepts"></a>개념
- [Azure Monitor에서 로그 데이터 분석](../../azure-monitor/log-query/log-query-overview.md)에서는 로그 쿼리의 간략한 개요를 제공하고 Azure Monitor 로그 데이터가 구성된 방식을 설명합니다.
- [Azure Monitor에서 데이터 보기 및 분석](../../azure-monitor/log-query/portals.md)에서는 로그 쿼리를 만들고 실행하는 포털을 설명합니다.

## <a name="reference"></a>참조

- [쿼리 언어 참조](/azure/kusto/query)는 Kusto 쿼리 언어에 대한 전체 언어 참조입니다.
- [Azure Monitor 로그 쿼리 언어 차이](data-explorer-difference.md)에서는 Kusto 쿼리 언어 버전 간의 차이점을 설명합니다.
- [Azure Monitor 로그 레코드의 표준 속성](../../azure-monitor/platform/log-standard-properties.md)에서는 모든 Azure Monitor 로그 데이터의 표준이 되는 속성을 설명합니다.
- [Azure Monitor에서 리소스 간 로그 쿼리 수행](../../azure-monitor/log-query/cross-workspace-query.md)에서는 여러 Log Analytics 작업 영역 및 Application Insights 애플리케이션의 데이터를 사용하는 로그 쿼리를 작성하는 방법을 설명합니다.


## <a name="examples"></a>예

- [Azure Monitor 로그 쿼리 예제](examples.md)에서는 Azure Monitor 로그 데이터를 사용하는 예제 쿼리를 제공합니다.



## <a name="lessons"></a>단원

- [Azure Monitor 로그 쿼리에 문자열 사용](string-operations.md)에서는 문자열 데이터로 작업하는 방법을 설명합니다.
- [Azure Monitor 로그 쿼리에 날짜/시간 값 사용](datetime-operations.md)에서는 날짜 및 시간 데이터로 작업하는 방법을 설명합니다. 
- [Azure Monitor 로그 쿼리의 집계](aggregations.md) 및 [Azure Monitor 로그 쿼리의 고급 집계](advanced-aggregations.md)에서는 데이터를 집계하고 요약하는 방법을 설명합니다.
- [Azure Monitor 로그 쿼리의 조인](joins.md)에서는 여러 테이블의 데이터를 조인하는 방법을 설명합니다.
- [Azure Monitor 로그 쿼리에 JSON 및 데이터 구조 사용](json-data-structures.md)에서는 json 데이터를 구문 분석하는 방법을 설명합니다.
- [Azure Monitor에서 고급 로그 쿼리 작성](advanced-query-writing.md)에서는 복잡한 쿼리를 만들고 코드를 다시 사용하기 위한 전략을 설명합니다.
- [Azure Monitor 로그 쿼리에서 차트 및 다이어그램 만들기](charts.md)에서는 로그 쿼리의 데이터를 시각화하는 방법을 설명합니다.

## <a name="cheatsheets"></a>Cheatsheets

-  [SQL-Azure Monitor 로그 쿼리](sql-cheatsheet.md)에서는 SQL에 이미 익숙한 사용자를 지원합니다.
-  [Splunk-Azure Monitor 로그 쿼리](sql-cheatsheet.md)에서는 Splunk에 이미 익숙한 사용자를 지원합니다.
 
## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대한 전체 참조 문서](/azure/kusto/query/)에 액세스합니다.