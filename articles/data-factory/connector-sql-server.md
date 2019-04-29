---
title: Azure Data Factory를 사용하여 SQL Server 간에 데이터 복사 | Microsoft Docs
description: Azure 데이터 팩터리를 사용하여 온-프레미스 또는 Azure VM에 있는 SQL Server 데이터베이스 간에 데이터를 이동하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: cb1b8171dc45c286d3f87a3c33e366d818cfaad9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61456821"
---
# <a name="copy-data-to-and-from-sql-server-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SQL Server 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-sqlserver-connector.md)
> * [현재 버전](connector-sql-server.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SQL Server 데이터베이스에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SQL Server 데이터베이스에서 지원되는 싱크 데이터 저장소로, 또는 지원되는 원본 데이터 저장소에서 SQL Server 데이터베이스로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SQL Server 커넥터는 다음을 지원합니다.

- SQL Server 버전 2016, 2014, 2012, 2008 R2, 2008 및 2005
- **SQL** 또는 **Windows** 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로, 대상 테이블에 데이터를 첨부하거나 복사 중에 사용자 지정 논리를 사용하여 저장 프로시저를 호출합니다.

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)는 이제 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 SQL Server 데이터베이스에서 데이터 복사를 사용하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요. 통합 런타임은 기본 제공 SQL Server 드라이버를 제공하므로 SQL Server 간에 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SQL Server 데이터베이스 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SQL Server 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **SqlServer** | 예 |
| connectionString |SQL 인증 또는 Windows 인증을 사용하여 SQL Server 데이터베이스에 연결하는 데 필요한 connectionString 정보를 지정합니다. 다음 샘플을 참조하세요.<br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| userName |Windows 인증을 사용하는 경우 사용자 이름을 지정합니다. 예: **domainname\\username**. |아닙니다. |
| 암호 |userName에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

**예 1: SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: Azure Key Vault의 암호를 사용하는 SQL 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예 3: Windows 인증 사용**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
     }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 SQL Server 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SQL Server 데이터베이스 간에 데이터를 복사하려면 데이터 세트의 type 속성을**SqlServerTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **SqlServerTable** | 예 |
| tableName |연결된 서비스가 참조하는 SQL Server 데이터베이스 인스턴스에서 테이블 또는 보기의 이름입니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예제:**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SQL Server 원본 및 싱크에서 지원되는 속성 목록을 제공합니다.

### <a name="sql-server-as-source"></a>원본으로 SQL Server

SQL Server의 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **SqlSource** | 예 |
| sqlReaderQuery |사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. |아니오 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니오 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아닙니다. |

**주의할 사항:**

- **sqlReaderQuery**가 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 SQL Server 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).
- "sqlReaderQuery" 또는 "sqlReaderStoredProcedureName" 중 하나를 지정하지 않으면 JSON 데이터 세트의 "structure" 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 생성하는 데 사용되어 SQL Server에 대해 실행합니다. 데이터 세트 정의에 "structure"가 없는 경우 모든 열은 테이블에서 선택됩니다.

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**예: 저장 프로시저 사용**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**저장 프로시저 정의:**

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sql-server-as-sink"></a>싱크로 SQL Server

SQL Server에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 type 속성을 다음으로 설정해야 합니다. **SqlSink** | 예 |
| writeBatchSize |SQL 테이블에 삽입 하는 행 수가 **일괄 처리당**합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10000) |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예제: “00:30:00”(30분) |아닙니다. |
| preCopyScript |SQL Server에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아닙니다. |
| sqlWriterStoredProcedureName |원본 데이터를 대상 테이블에 적용하는 방법(예: 사용자 고유의 비즈니스 논리를 사용하여 upsert 또는 transform 수행)을 정의하는 저장 프로시저의 이름입니다. <br/><br/>이 저장 프로시저는 **배치마다 호출**됩니다. 한 번만 실행되고 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다. |아닙니다. |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아닙니다. |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |아닙니다. |

> [!TIP]
> SQL Server로 데이터를 복사할 때 복사 작업은 기본적으로 싱크 테이블에 데이터를 추가합니다. UPSERT 또는 추가 비즈니스 논리를 수행하려면 SqlSink에서 저장 프로시저를 사용합니다. 자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

**예 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**예 2: upsert에 대해 복사 중 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출](#invoking-stored-procedure-for-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="identity-columns-in-the-target-database"></a>대상 데이터베이스의 ID 열

이 섹션에서는 ID 열이 없는 소스 테이블에서 ID 열이 있는 대상 테이블로 데이터를 복사하는 예제를 제공합니다.

**원본 테이블:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**대상 테이블:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

대상 테이블에 ID 열이 있는지 확인합니다.

**원본 데이터 세트 JSON 정의**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**대상 데이터 세트 JSON 정의**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

원본 테이블과 대상 테이블의 스키마가 서로 다릅니다(대상에 ID가 포함된 추가 열이 있음). 이 시나리오에서는 ID 열을 포함하지 않는 대상 데이터 세트 정의에서 **structure** 속성을 지정해야 합니다.

## <a name="invoking-stored-procedure-for-sql-sink"></a> SQL 싱크에서 저장된 프로시저 호출

SQL Server 데이터베이스로 데이터를 복사할 때 사용자 지정 저장 프로시저를 구성하고 추가 매개 변수로 호출할 수 있습니다.

기본 제공 메커니즘이 용도에 적합하지 않을 때는 저장 프로시저를 사용할 수 있습니다. 보통은 대상 테이블에서 원본 데이터의 최종 삽입 전에 upsert(insert + update, 삽입 + 업데이트) 또는 추가 처리(열 합병, 추가 값 검색, 여러 테이블에 삽입 등)를 수행해야 할 때 저장 프로시저를 사용합니다.

다음 샘플에서는 저장 프로시저를 사용하여 SQL Server 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 각기 다음과 같은 세 개 열을 갖는 입력 데이터와 싱크 **Marketing** 테이블을 가정해 보겠습니다. **ProfileID**, **State** 및 **Category**. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합:** "tableName" 저장된 프로시저 (저장된 프로시저 스크립트 아래 참조)의 동일한 테이블 형식 매개 변수 이름 이어야 합니다.

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

정의 된 **SQL 싱크** 다음과 같이 복사 활동의 섹션입니다.

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

데이터베이스에서 **SqlWriterStoredProcedureName**과 동일한 이름의 저장 프로시저를 정의합니다. 지정된 원본의 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저에서 테이블 형식의 매개 변수 이름은 데이터 세트에 정의된 **tableName**과 동일해야 합니다.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category);
END
```

데이터베이스에서 sqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마가 입력 데이터에서 반환된 스키마와 동일해야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

저장된 프로시저 기능은 [테이블 값 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 이용합니다.

## <a name="data-type-mapping-for-sql-server"></a>SQL Server에 대한 데이터 형식 매핑

SQL Server 간에 데이터를 복사하는 경우 SQL Server 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SQL Server 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| Image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| 텍스트 |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 데이터 형식이 10진수 중간 형식으로 매핑되는 경우 ADF는 현재 최대 28 자릿수의 데이터를 지원합니다. 28보다 큰 자릿수의 데이터가 있는 경우 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="troubleshooting-connection-issues"></a>연결 문제 해결

1. 원격 연결을 허용하도록 SQL Server를 구성합니다. **SQL Server Management Studio**를 시작하고 **서버**를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다. 목록에서 **연결**을 선택하고 **서버에 대한 원격 연결 허용**을 선택합니다.

    ![원격 연결 사용](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    자세한 단계를 보려면 [원격 액세스 서버 구성 옵션 구성](https://msdn.microsoft.com/library/ms191464.aspx) 을 참조하세요.

2. **SQL Server 구성 관리자**를 시작합니다. 사용하려는 인스턴스에 대한 **SQL Server 네트워크 구성**을 확장하고 **MSSQLSERVER용 프로토콜**을 선택합니다. 오른쪽 창에 프로토콜이 표시됩니다. **TCP/IP**를 마우스 오른쪽 단추로 클릭하고 **사용**을 클릭하여 TCP/IP를 사용하도록 설정합니다.

    ![TCP/IP 사용](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    TCP/IP 프로토콜을 사용하는 다른 방법 및 자세한 내용은 [서버 네트워크 프로토콜 사용 또는 사용 안 함](https://msdn.microsoft.com/library/ms191294.aspx) 을 참조하세요.

3. 같은 창에서 **TCP/IP**를 두 번 클릭하여 **TCP/IP 속성** 창을 시작합니다.
4. **IP 주소** 탭으로 전환합니다. 아래로 스크롤하여 **IPAll** 섹션을 확인합니다. **TCP 포트**(기본값은 **1433**)를 기록해 둡니다.
5. 컴퓨터에 **Windows 방화벽에 대한 규칙** 을 만들어 이 포트를 통해 들어오는 트래픽을 허용합니다.  
6. **연결 확인**: 정규화된 이름을 사용하여 SQL Server에 연결하려면 다른 머신의 SQL Server Management Studio를 사용합니다. 예: `"<machine>.<domain>.corp.<company>.com,1433"`

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
