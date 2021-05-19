---
title: 서버리스 SQL 풀에서 외부 테이블 만들기 및 사용
description: 이 섹션에서는 서버리스 SQL 풀에서 외부 테이블을 만들고 사용하는 방법을 알아봅니다.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: ea98666b38628dcf0284b4fabbf7aa239bf270df
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109751836"
---
# <a name="create-and-use-external-tables-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 외부 테이블 만들기 및 사용

이 섹션에서는 서버리스 SQL 풀에서 [외부 테이블](develop-tables-external-tables.md)을 만들고 사용하는 방법을 알아봅니다. 외부 테이블은 서버리스 SQL 풀에서 외부 데이터에 대한 액세스를 제어하고 Power BI와 같은 도구를 서버리스 SQL 풀과 함께 사용하려는 경우에 유용합니다. 외부 테이블은 두 가지 유형의 스토리지에 액세스할 수 있습니다.
- 사용자가 공용 스토리지 파일에 액세스하는 공용 스토리지입니다.
- 사용자가 SAS 자격 증명, Azure AD ID 또는 Synapse 작업 영역의 관리 ID를 사용하여 스토리지 파일에 액세스하는 보호된 스토리지입니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 테이블이 생성될 데이터베이스를 만드는 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이 샘플에서 사용되는 다음과 같은 개체를 만듭니다.
- SAS로 보호되는 `https://sqlondemandstorage.blob.core.windows.net` Azure 스토리지 계정에 액세스할 수 있도록 하는 데이터베이스 범위 자격 증명 `sqlondemand`.

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- SAS 키로 보호되는 데모 스토리지 계정을 참조하는 외부 데이터 원본 `sqlondemanddemo` 및 위치 `https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/`에서 공개적으로 사용 가능한 Azure 스토리지 계정을 참조하는 외부 데이터 원본 `YellowTaxi`.

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE YellowTaxi
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
    ```

- CSV 및 Parquet 파일 형식을 설명하는 파일 형식 `QuotedCSVWithHeaderFormat` 및 `ParquetFormat`.

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    ```

이 문서의 쿼리는 샘플 데이터베이스에서 실행되며 이러한 개체를 사용합니다. 

## <a name="create-an-external-table-on-protected-data"></a>보호된 데이터에 대한 외부 테이블 만들기

Azure AD ID 또는 SAS 키가 있는 사용자에게 액세스를 허용하는 Azure 스토리지 계정에서 데이터에 액세스하는 외부 테이블을 만들 수 있습니다. 일반 SQL Server 외부 테이블을 만드는 것과 같은 방법으로 외부 테이블을 만들 수 있습니다. 

다음 쿼리는 `sqlondemanddemo` 데이터 원본을 사용하여 참조되고 `sqlondemand`라는 데이터베이스 범위 자격 증명을 사용하여 보호되는 SynapseSQL 데모 Azure 스토리지 계정에서 *population.csv* 파일을 읽는 외부 테이블을 만듭니다. 

데이터 원본 및 데이터베이스 범위 자격 증명은 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)에 생성됩니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다. 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

## <a name="create-an-external-table-on-public-data"></a>공용 데이터에 외부 테이블 만들기

공개적으로 사용 가능한 Azure 스토리지에 저장된 파일에서 데이터를 읽는 외부 테이블을 만들 수 있습니다. 이 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)는 다음 쿼리에서 사용되는 공용 외부 데이터 원본 및 Parquet 파일 형식 정의를 만듭니다.

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat
);
```

> [!NOTE]
> 테이블은 분할된 폴더 구조에 생성되지만 일부 파티션 제거를 활용할 수 없습니다. 일부 기준(이 경우 특정 연도 또는 월)을 충족하지 않는 파일을 건너뛰어 성능을 향상하려면 [외부 데이터에 대한 뷰](create-use-views.md)를 사용합니다.

## <a name="use-an-external-table"></a>외부 테이블 사용

SQL Server 쿼리에서 사용하는 것과 동일한 방식으로 사용자 쿼리에서 [외부 테이블](develop-tables-external-tables.md)을 사용할 수 있습니다.

다음 쿼리에서는 이전 섹션에서 만든 *population* 외부 테이블을 사용하는 방법을 설명합니다. 2019년의 인구를 기준으로 내림차순으로 국가/지역 이름을 반환합니다.

> [!NOTE]
> 쿼리의 첫 번째 줄(예: [mydbname])을 변경하여 사용자가 만든 데이터베이스를 사용합니다.

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>다음 단계

스토리지에 쿼리 결과를 저장하는 방법에 대한 자세한 내용은 [쿼리 결과를 스토리지에 저장](../sql/create-external-table-as-select.md) 문서를 참조하세요.
