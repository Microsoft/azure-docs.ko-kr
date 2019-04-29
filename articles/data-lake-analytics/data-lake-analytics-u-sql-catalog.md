---
title: Azure Data Lake Analytics에서 U-SQL 카탈로그 시작
description: U-SQL 카탈로그를 사용하여 코드와 데이터를 공유하는 방법을 알아봅니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: a6faa7037ccbacc0547401dd52bb3b19abd1c474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813357"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics에서 U-SQL 카탈로그 시작

## <a name="create-a-tvf"></a>TVF 만들기

이전 U-SQL 스크립트에서는 동일한 원본 파일에서 읽어 오는 EXTRACT 사용을 반복했습니다. U-SQL TVF(테이블 반환 함수)를 사용하면 데이터를 나중에 다시 사용할 수 있게 캡슐화할 수 있습니다.  

다음 스크립트는 기본 데이터베이스 및 스키마에 `Searchlog()`라는 TVF를 만듭니다.

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

다음 스크립트는 이전 스크립트에서 정의된 TVF를 사용하는 방법을 보여줍니다.

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>뷰 만들기

단일 쿼리 식이 있는 경우 TVF 대신 U-SQL VIEW를 사용하여 해당 식을 캡슐화할 수 있습니다.

다음 스크립트는 기본 데이터베이스 및 스키마에 `SearchlogView`라는 뷰를 만듭니다.

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

다음 스크립트는 정의된 뷰를 사용함을 나타냅니다.

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>테이블 만들기
관계형 데이터베이스 테이블과 마찬가지로, U-SQL을 사용하여 미리 정의된 스키마가 포함된 테이블을 만들거나 테이블을 채운 쿼리(CREATE TABLE AS SELECT 또는 CTAS)에서 스키마를 유추하는 테이블을 만들 수 있습니다.

다음 스크립트를 사용하여 데이터베이스 하나와 테이블 두 개를 만듭니다.

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>쿼리 테이블
데이터 파일을 쿼리하는 것과 같은 방식으로 이전 스크립트에서 만든 테이블을 쿼리할 수 있습니다. EXTRACT를 사용하여 행 집합을 만드는 대신 테이블 이름을 참조할 수 있습니다.

테이블에서 읽어 오려면 이전에 사용한 변환 스크립트를 수정합니다.

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >현재는 테이블을 만든 스크립트와 같은 스크립트의 테이블에 SELECT를 실행할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Portal을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
