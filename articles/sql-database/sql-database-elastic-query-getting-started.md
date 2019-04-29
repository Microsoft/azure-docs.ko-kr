---
title: 확장된 클라우드 데이터베이스에서 보고(수평 분할) | Microsoft Docs
description: 복수 데이터베이스에 대해 보고하려면 교차 데이터베이스 쿼리를 사용합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: a73938c98ebaea310875f0db8b665d0f1aed55e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556277"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>확장된 클라우드 데이터베이스에서 보고(미리 보기)

[탄력적 쿼리](sql-database-elastic-query-overview.md)를 사용하여 단일 연결 지점의 여러 Azure SQL 데이터베이스에서 보고서를 만들 수 있습니다. 데이터베이스를 가로로 분할해야 합니다("분할됨"이라고도 함).

기존 데이터베이스를 사용하는 경우 [확장된 데이터베이스에 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md)을 참조하세요.

쿼리에 필요한 SQL 개체를 알아보려면 [수평 분할된 데이터베이스에 쿼리](sql-database-elastic-query-horizontal-partitioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

[Elastic Database 도구 샘플 시작](sql-database-elastic-scale-get-started.md)을 다운로드하고 실행하세요.

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>샘플 응용 프로그램을 사용하여 분할된 데이터베이스 맵 관리자 만들기
분할된 데이터베이스 안의 삽입된 데이터에 따라 여느 분할된 데이터 베이스와 마찬가지로 분할된 데이터 베이스 관리자를 만들수 있습니다. 이미 분할된 데이터가 설치되어 있는 분할된 데이터베이스가 있다면, 다음 단계들을 건너뛰고 다음 섹션으로 이동합니다.

1. **Elastic Database 도구 애플리케이션**을 빌드하고 실행하세요. [샘플 앱 다운로드 및 실행](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app)섹션에서 7단계까지 수행합니다. 7단계를 끝내면 다음 명령 프롬프트를 볼 수 있습니다.

    ![명령 프롬프트][1]
2. 명령 창에 "1"을 입력하고 **Enter**키를 누릅니다. 이 명령은 분할된 데이터베이스 관리자를 생성 및 두 분할된 데이터베이스를 추가합니다. 그런 다음 "3"을 입력하고 **Enter**키를 누릅니다: 작업을 4번 반복합니다. 이 명령은 분할된 데이터베이스에 샘플 데이터행을 삽입합니다.
3. [Azure Portal](https://portal.azure.com)에서 사용자의 서버 내에 다음과 같은 3개의 새 데이터베이스가 보여야 합니다.

   ![Visual Studio 확인][2]

   이 시점에 데이터베이스 간 쿼리는 Elastic Database 클라이언트 라이브러리를 통해 지원됩니다. 예를 들면, 명령창에 있는 옵션4를 이용합니다. 다중 분할된 데이터베이스 쿼리에서 나온 결과는 항상 모든 분할된 데이터베이스의 **UNION ALL** 입니다.

   다음 섹션에서는 분할 된 데이터베이스 간 데이터의 다양한 쿼리를 지원하는 샘플 데이터베이스 엔드포인트를 만듭니다.

## <a name="create-an-elastic-query-database"></a>탄력적 쿼리 데이터베이스 만들기
1. [Azure Portal](https://portal.azure.com)을 열고 로그인합니다.
2. 분할 된 데이터베이스를 설치한 동일 서버에서 새 Azure SQL 데이터베이스를 만듭니다. 데이터베이스 이름을"ElasticDBQuery."로 합니다.

    ![Azure 포털 및 가격 책정 계층][3]

    > [!NOTE]
    > 기존 데이터베이스를 사용할 수 있습니다. 그럴 경우, 사용자가 실행하고 싶은 쿼리가 포함된 분할된 데이터베이스가 아닙니다. 이 데이터베이스는 탄력적 데이터베이스 쿼리에 대한 메타데이터 개체를 만들기 위해 사용됩니다.
    >

## <a name="create-database-objects"></a>데이터베이스 개체 만들기
### <a name="database-scoped-master-key-and-credentials"></a>데이터베이스-범위 마스터 키 및 자격 증명
분할된 데이터베이스와 분할된 데이터베이스 관리자를 연결하는데 사용됩니다.

1. SQL Server Management Studio 또는 Visual Studio의 SQL Server Data Tools를 엽니다.
2. ElasticDBQuery 데이터베이스에 연결한 다음 T-SQL 명령을 실행 합니다.

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
        WITH IDENTITY = '<username>',
        SECRET = '<password>';

    "username" 및 "password"는 [탄력적 데이터베이스 도구 시작](sql-database-elastic-scale-get-started.md)의 [샘플 응용 프로그램 다운로드 및 실행](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app)의 6단계에서 사용한 로그인 정보와 같아야 합니다.

### <a name="external-data-sources"></a>외부 데이터 원본
외부 데이터 소스를 만들려면 ElasticDBQuery 데이터베이스에서 다음 명령을 실행 합니다.

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
      CREDENTIAL = ElasticDBQueryCred,
       SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 탄력적 데이터베이스 도구 샘플로  분할된 데이터베이스와 관리자를 만든 경우, "CustomerIDShardMap"가 분할된 데이터베이스 맵의 이름이 됩니다. 그러나, 이 샘플에서 사용자 설치를 사용했다면, 애플리케이션 내에서 분할된 데이터베이스 이름을 정해야 합니다.

### <a name="external-tables"></a>외부 테이블
ElasticDBQuery database의 명령을 실행하여 분할된 데이터베이스의 사용자 테이블과 일치하는 외부테이블을 만들수 있습니다.

    CREATE EXTERNAL TABLE [dbo].[Customers]
    ( [CustomerId] [int] NOT NULL,
      [Name] [nvarchar](256) NOT NULL,
      [RegionId] [int] NOT NULL)
    WITH
    ( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
    ) ;

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>탄력적 데이터베이스 T-SQL쿼리 샘플 실행
외부 데이터 원본 및 외부 테이블을 정의한 후 외부 테이블을 통해 전체 T-SQL을 사용할 수 있습니다.

ElasticDBQuery 데이터베이스에서 다음쿼리를 실행 합니다.

    select count(CustomerId) from [dbo].[Customers]

모든 분할된 데이터베이스에서 쿼리를 집계한 결과 및 다음 출력이 지정됨을 알 수 있습니다.

![출력 세부 정보][4]

## <a name="import-elastic-database-query-results-to-excel"></a>탄력적 데이터베이스 쿼리 결과를 Excel로 가져오기
 쿼리의 결과를 엑셀파일로 가져올 수 있습니다.

1. Excel 2013을 실행 합니다.
2. **데이터** 리본을 탐색합니다.
3. **기타 원본에서**을 클릭하고 **SQL Server에서**를 클릭합니다.

   ![다른 원본에서 Excel 가져오기][5]
4. **데이터 연결 마법사** 에서 서버 이름 및 로그인 자격 증명을 입력합니다. 그런 후 **Next** 를 클릭합니다.
5. 대화 상자에서 **원하는 데이터를 포함하는 데이터베이스를 선택**하고 **ElasticDBQuery** 데이터베이스를 선택합니다.
6. 목록 보기에서 **사용자**테이블을 선택하고 **다음**을 클릭합니다. **마침**을 클릭합니다.
7. **데이터 가져오기** 양식에서, **통합 문서에서 원하는 데이터를 보는 방법을 선택**하고 **테이블**을 선택하고 **확인**을 클릭합니다.

다른 분할된 데이터베이스에 저장된 **Customers** 테이블의 모든 행으로 Excel 시트를 채웁니다.

이제 Excel의 강력한 데이터 시각화 함수를 사용할 수 있습니다. 탄력적 쿼리 데이터 베이스의 데이터 통합 도구 및 BI과 연결하기 위해 서버 이름, 데이터베이스 이름, 자격 증명과 연결 문자열을 사용할 수 있습니다. SQL Server 도구에 대한 데이터 소스로 지원 되는지 확인 합니다. 탄력적 쿼리 데이터베이스 및 기타 SQL Server 데이터베이스와 마찬가지로 외부 테이블 및 도구와 연결할 수 있는 SQL Server 테이블을 참조할 수 있습니다.

### <a name="cost"></a>비용
Elastic Database 쿼리 기능을 사용 하는 것은 무료입니다.

가격 정보는 [SQL Database 가격 정보](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 탄력적 쿼리의 개요는 [탄력적 쿼리 개요](sql-database-elastic-query-overview.md)를 참조하세요.
* 수직 분할 자습서는 [데이터베이스 간 쿼리 시작(수직 분할)](sql-database-elastic-query-getting-started-vertical.md)을 참조하세요.
* 수직 분할된 데이터에 대한 구문 및 예제 쿼리는 [수직 분할된 데이터 쿼리하기](sql-database-elastic-query-vertical-partitioning.md)를 참조하세요.
* 행 분할된 데이터에 대한 구문 및 예제 쿼리는 [행 분할된 데이터 쿼리하기](sql-database-elastic-query-horizontal-partitioning.md)를 참조하세요.
* 단일 원격 Azure SQL Database 또는 수평 분할 구성표의 분할을 제공하는 데이터베이스 세트에서 Transact-SQL 문을 실행하는 저장된 프로시저는 [sp\_실행 \_원격](https://msdn.microsoft.com/library/mt703714)을 참조하세요.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
