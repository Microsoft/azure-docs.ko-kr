---
title: 여러 스키마를 사용하여 클라우드 데이터베이스에서 쿼리 | Microsoft Docs
description: 수직 분할을 통해 데이터베이스 간 쿼리를 설정하는 방법
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein
manager: digimobile
origin.date: 01/25/2019
ms.date: 02/25/2019
ms.openlocfilehash: e7ba8057cd22c5cc1080b4a6d95f17bf76d4acb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585430"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>여러 스키마를 사용하여 클라우드 데이터베이스에서 쿼리(미리 보기)

![다른 데이터베이스에서 테이블에 대한 쿼리][1]

수직 분할 데이터베이스는 서로 다른 데이터베이스에서 다양한 테이블 집합을 사용합니다. 즉 스키마가 데이터베이스마다 서로 다릅니다. 예를 들어, 재고의 모든 테이블은 한 데이터베이스 안에 있지만 모든 회계 관련 테이블은 보조 데이터베이스에 있습니다. 

## <a name="prerequisites"></a>필수 조건

* 사용자는 모든 외부 데이터 원본 ALTER 권한이 있어야 합니다. 이 사용 권한은 ALTER DATABASE 권한에 포함됩니다.
* 기본 데이터 원본을 참조하기 위해 ALTER ANY EXTERNAL DATA SOURCE 권한이 필요합니다.

## <a name="overview"></a>개요

> [!NOTE]
> 행 분할과 달리 이 DDL 문은 Elastic Database 클라이언트 라이브러리를 통한 분할 맵이 있는 데이터 계층 정의에 종속되지 않습니다.
>

1. [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
2. [데이터베이스 범위 자격 증명 만들기](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>데이터베이스 범위 마스터 키 및 자격 증명 만들기

자격 증명은 탄력적 쿼리에서 원격 데이터베이스 연결에 사용됩니다.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> `<username>`에 **"\@servername"** 접미사가 포함되지 않아야 합니다. 
>

## <a name="create-external-data-sources"></a>외부 데이터 원본 만들기

구문

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = '<fully_qualified_server_name>',
                DATABASE_NAME = '<remote_database_name>',  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> TYPE 매개 변수는 **RDBMS**로 설정해야 합니다. 
>

### <a name="example"></a>예

다음 예제에서는 외부 데이터 원본에 대한 CREATE 문 사용을 보여줍니다. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.chinacloudapi.cn', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

현재 외부 데이터 원본의 목록을 검색하려면: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>외부 테이블

구문

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>예

```sql
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 
```

다음 예제에서는 현재 데이터베이스에서 외부 테이블 목록을 검색 하는 방법을 보여줍니다. 

    select * from sys.external_tables; 

### <a name="remarks"></a>설명

탄력적 쿼리는 기존 외부 테이블 구문을 확장하여 RDBMS 형식의 외부 데이터 원본을 사용하는 외부 테이블을 정의합니다. 수직 분할에 대한 외부 테이블 정의에서는 다음과 같은 측면을 다룹니다. 

* **스키마**: 외부 테이블 DDL은 쿼리가 사용할 수 있는 스키마를 정의합니다. 외부 테이블 정의에서 제공한 스키마는 실제 데이터가 저장되는 원격 데이터베이스의 테이블 스키마와 일치해야 합니다. 
* **원격 데이터베이스 참조**: 외부 테이블 DDL은 외부 데이터 원본을 가리킵니다. 외부 데이터 원본은 실제 테이블 데이터가 저장된 원격 데이터베이스의 SQL Database 서버 이름과 데이터베이스 이름을 지정합니다. 

이전 섹션에서 설명한 대로 외부 데이터 소스를 사용하여 외부 테이블을 만드는 구문은 다음과 같습니다. 

DATA_SOURCE 절은 외부 테이블에 사용하는 외부 데이터 원본(즉, 수직 분할의 경우 원격 데이터베이스)을 정의합니다.  

SCHEMA_NAME 및 OBJECT_NAME 절은 각각 외부 테이블 정의를 원격 데이터베이스에 있는 다른 스키마 또는 다른 이름의 테이블에 매핑하는 기능을 제공합니다. 이 기능은 원격 데이터베이스에서 카탈로그 뷰나 DMV에 대해 외부 테이블을 정의하거나, 원격 테이블 이름을 이미 로컬로 사용하는 경우 등의 다른 상황에 유용합니다.  

다음 DDL 문은 로컬 카탈로그에서 기존 외부 테이블 정의를 삭제합니다. 원격 데이터베이스에는 영향을 주지 않습니다. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**CREATE/DROP EXTERNAL TABLE 권한**: 기본 데이터 원본을 참조하기 위해 외부 테이블 DDL에 대한 ALTER ANY EXTERNAL DATA SOURCE 권한도 필요합니다.  

## <a name="security-considerations"></a>보안 고려 사항

외부 테이블에 대한 액세스 권한이 있는 사용자는 외부 데이터 원본 정의에서 제공한 자격 증명에 따라 자동으로 기본 원격 테이블에 액세스할 수 있습니다. 외부 데이터 원본의 자격 증명을 통해 원치 않는 권한 상승을 방지하려면 외부 테이블에 대한 액세스 관리에 주의가 필요합니다. 일반 SQL 권한을 사용하여 일반 테이블에서처럼 외부 테이블에 대한 액세스를 부여하거나 취소할 수 있습니다.  

## <a name="example-querying-vertically-partitioned-databases"></a>예: 수직 분할된 데이터베이스 쿼리

다음 쿼리는 주문 및 주문 라인을 위한 2개의 로컬 테이블과 고객을 위한 원격 테이블 간의 3방향 조인을 수행합니다. 탄력적 쿼리에 대한 참조 데이터 사용 사례의 예입니다. 

```sql
    SELECT      
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline, 
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer 
    JOIN orders 
    ON c_id = o_c_id
    JOIN  order_line 
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>원격 T-SQL 실행을 위한 저장 프로시저: sp\_execute_remote

또한 탄력적 쿼리는 원격 데이터베이스에 대한 직접 액세스를 제공하기 위해 저장 프로시저를 사용합니다. 저장 프로시저는 [sp\_execute \_remote](https://msdn.microsoft.com/library/mt703714)라고 하며, 원격 데이터베이스에서 원격 저장 프로시저 또는 T-SQL 코드를 실행하는 데 사용될 수 있습니다. 사용되는 매개 변수는 다음과 같습니다. 

* 데이터 원본 이름(nvarchar): RDBMS 형식의 외부 데이터 원본 이름입니다. 
* 쿼리(nvarchar): 원격 데이터베이스에서 실행할 T-SQL 쿼리입니다. 
* 매개 변수 선언(nvarchar) - 선택 사항: 쿼리 매개 변수(예: sp_executesql)에 사용된 매개 변수에 대한 데이터 형식 정의가 있는 문자열입니다. 
* 매개 변수 값 목록 - 선택 사항: 쉼표로 구분된 매개 변수 값(예: sp_executesql) 목록입니다.

sp\_execute\_remote는 호출 매개 변수에 제공된 외부 데이터 원본을 사용하여 원격 데이터베이스에서 지정된 T-SQL 문을 실행합니다. 외부 데이터 원본의 자격 증명을 사용하여 원격 데이터베이스에 연결합니다.  

예제: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>도구에 대한 연결

일반 SQL Server 연결 문자열을 사용하여, 탄력적 쿼리를 사용하도록 설정되었고 외부 테이블이 정의된 SQL DB 서버의 데이터베이스에 BI 및 데이터 통합 도구를 연결할 수 있습니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 그런 다음 도구와 연결하려는 타 SQL Server 데이터베이스와 똑같이 탄력적 쿼리 데이터베이스와 외부 테이블을 참조합니다. 

## <a name="best-practices"></a>모범 사례

* SQL DB 방화벽 구성에서 Azure 서비스에 대한 액세스를 활성화하여 탄력적 쿼리 엔드포인트 데이터베이스에 원격 데이터베이스 액세스 권한을 부여해야 합니다. 또한 외부 데이터 원본 종의에서 제공한 자격 증명으로 원격 데이터베이스에 로그인할 수 있고 원격 테이블에 대한 액세스 권한이 있는지 확인합니다.  
* 탄력적 쿼리는 원격 데이터베이스에서 대부분의 계산을 수행할 수 있는 상황에서 가장 잘 실행됩니다. 일반적으로 원격 데이터베이스에서 평가할 수 있는 선택적 필더 조건자나, 원격 데이터베이스에서 완전히 수행 가능한 조인을 통해 최고의 쿼리 성능을 얻을 수 있습니다. 다른 쿼리 패턴은 원격 데이터베이스에서 대규모의 데이터 로드가 필요할 수도 있기 때문에 성능이 좋지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* 탄력적 쿼리의 개요는 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조하세요.
* 수직 분할 자습서는 [데이터베이스 간 쿼리 시작(수직 분할)](sql-database-elastic-query-getting-started-vertical.md)을 참조하세요.
* 행 분할(분할) 자습서는 [행 분할(분할)을 위한 탄력적 데이터베이스 쿼리 시작하기](sql-database-elastic-query-getting-started.md)를 참조하세요.
* 행 분할된 데이터에 대한 구문 및 예제 쿼리는 [행 분할된 데이터 쿼리하기](sql-database-elastic-query-horizontal-partitioning.md)를 참조하세요.
* 단일 원격 Azure SQL Database 또는 수평 분할 구성표의 분할을 제공하는 데이터베이스 세트에서 Transact-SQL 문을 실행하는 저장된 프로시저는 [sp\_실행 \_원격](https://msdn.microsoft.com/library/mt703714)을 참조하세요.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->