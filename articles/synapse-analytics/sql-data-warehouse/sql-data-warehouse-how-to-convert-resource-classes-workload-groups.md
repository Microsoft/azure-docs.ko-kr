---
title: 리소스 클래스를 작업 그룹으로 변환
description: Azure SQL Data Warehouse에서 리소스 클래스와 유사한 작업 그룹을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c61e8df05c4bc199c0d91b8ed0cbd73fa6f196cf
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192324"
---
# <a name="convert-resource-classes-to-workload-groups"></a>리소스 클래스를 작업 그룹으로 변환

작업 그룹은 시스템 리소스를 격리하고 포함하는 메커니즘을 제공합니다.  또한 작업 그룹을 사용하여 실행 중인 요청에 대한 실행 규칙을 설정할 수 있습니다.  쿼리 제한 시간 실행 규칙은 사용자 개입 없이 런어웨이 쿼리를 취소할 수 있도록 허용합니다.  이 문서에서는 기존 리소스 클래스를 사용하고 유사한 구성을 사용하여 작업 그룹을 만드는 방법을 설명합니다.  또한 선택적 쿼리 제한 시간 규칙이 추가됩니다.

> [!NOTE]
> 작업 그룹 및 리소스 클래스를 동시에 사용하는 방법에 대한 지침은 [워크로드 분류](sql-data-warehouse-workload-classification.md) 개념 문서에서 [분류자와 리소스 클래스 할당 혼합](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) 섹션을 참조하세요.

## <a name="understanding-the-existing-resource-class-configuration"></a>기존 리소스 클래스 구성 이해

작업 그룹에는 요청당 할당된 전체 시스템 리소스의 백분율을 지정하는 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`라는 매개 변수가 필요합니다.  리소스 할당은 동시성 슬롯을 할당하여 [리소스 클래스](resource-classes-for-workload-management.md#what-are-resource-classes)에 대해 수행됩니다.  `REQUEST_MIN_RESOURCE_GRANT_PERCENT`에 대해 지정할 값을 확인하려면 sys.dm_workload_management_workload_groups_stats <link tbd> DMV를 사용합니다.  예를 들어 아래 쿼리는 `REQUEST_MIN_RESOURCE_GRANT_PERCENT` 매개 변수에 사용할 수 있는 값을 반환하여 staticrc40과 유사한 작업 그룹을 만듭니다.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> 작업 그룹은 전체 시스템 리소스의 백분율을 기준으로 작동합니다.  

작업 그룹은 전체 시스템 리소스의 백분율을 기준으로 작동하기 때문에 스케일 업 및 스케일 다운에 따라 전체 시스템 리소스를 기준으로 하는 정적 리소스 클래스에 할당된 리소스의 백분율이 변경됩니다.  예를 들어 DW1000c에서 staticrc40은 전체 시스템 리소스의 19.2%를 할당합니다.  DW2000c에서는 9.6%가 할당됩니다.  이 모델은 요청당 더 많은 리소스를 할당하는 것과 비교하여 동시성을 위해 확장하려는 경우에도 유사합니다.

## <a name="create-workload-group"></a>작업 그룹 만들기

알려진 `REQUEST_MIN_RESOURCE_GRANT_PERCENT`로 CREATE WORKLOAD GROUP <link> 구문을 사용하여 작업 그룹을 만들 수 있습니다.  필요에 따라 0보다 큰 `MIN_PERCENTAGE_RESOURCE`를 지정하여 작업 그룹에 대한 리소스를 격리할 수 있습니다.  또한 필요에 따라 100 미만 `CAP_PERCENTAGE_RESOURCE`를 지정하여 작업 그룹에서 사용할 수 있는 리소스의 양을 제한할 수 있습니다.  

예제에 대 한 기본으로 mediumrc를 사용 하 여, 아래 코드는를로 설정 하 여 `MIN_PERCENTAGE_RESOURCE` 시스템 리소스의 10%를 전용으로 설정 하 `wgDataLoads` 고 하나의 쿼리가 항상 실행 될 수 있도록 보장 합니다.  또한 `CAP_PERCENTAGE_RESOURCE` 가 40%로 설정 되 고이 작업 그룹을 동시 요청 4 개로 제한 합니다.  `QUERY_EXECUTION_TIMEOUT_SEC` 매개 변수를 3600으로 설정하면 1시간 넘게 실행되는 모든 쿼리가 자동으로 취소됩니다.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>분류자 만들기

이전에는 [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class)를 사용하여 리소스 클래스에 대한 쿼리 매핑을 수행했습니다.  동일한 기능을 구현하고 요청을 작업 그룹에 매핑하려면 [CREATE WORKLOAD CLASSIFIER](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 구문을 만듭니다.  sp_addrolemember를 사용하는 경우 로그인을 기반으로 하는 요청에만 리소스를 매핑할 수 있습니다.  분류자는 로그인 외에 다음과 같은 추가 옵션을 제공합니다.
    - label
    - 세션
    - 시간 아래 예제에서는 `AdfLogin` 로그인에서 쿼리를 할당합니다. 여기에는 위에서 만든 작업 그룹 `wgDataLoads`에 `factloads`로 설정된 [옵션 레이블](sql-data-warehouse-develop-label.md)도 있습니다.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>샘플 쿼리를 사용하여 테스트

다음은 작업 그룹 및 분류자가 올바르게 구성되었는지 확인하는 샘플 쿼리 및 DMV 쿼리입니다.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>다음 단계

- [워크로드 격리](sql-data-warehouse-workload-isolation.md)
- [작업 그룹을 만드는 방법](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER(Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP(Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
