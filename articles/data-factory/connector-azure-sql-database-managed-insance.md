---
title: Azure Data Factory를 사용하여 Azure SQL Database Managed Instance 간에 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure SQL Database Managed Instance 간에 데이터를 이동하는 방법을 알아봅니다.
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
ms.openlocfilehash: 9cb3c028c14e6c47d47eafcf6279a918c0917442
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093950"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database Managed Instance 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Database Managed Instance 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure SQL Database Managed Instance에서 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Managed Instance에 복사할 수도 있습니다. 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure SQL Database Managed Instance 커넥터는 다음을 지원합니다.

- SQL 또는 Windows 인증을 사용한 데이터 복사
- 쿼리 또는 저장 프로시저를 사용하여 데이터 검색(원본)
- 싱크로, 복사 중에 대상 테이블에 데이터를 추가하거나 사용자 지정 논리를 사용하여 저장 프로시저 호출(싱크)

SQL Server [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)는 이제 지원되지 않습니다. 

## <a name="prerequisites"></a>필수 조건

가상 네트워크에 있는 Azure SQL Database Managed Instance에서 데이터 복사를 사용하려면 데이터베이스에 액세스할 수 있는 자체 호스팅 통합 런타임을 설정합니다. 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md)을 참조하세요.

Managed Instance와 동일한 가상 네트워크에서 자체 호스팅 통합 런타임을 프로비전하는 경우 통합 런타임 컴퓨터가 Managed Instance와 다른 서브넷에 있는지 확인합니다. Managed Instance와 다른 가상 네트워크에서 자체 호스팅 통합 런타임을 프로비전하는 경우에는 가상 네트워크 피어링 또는 가상 네트워크 간 연결을 사용할 수 있습니다. 자세한 내용은 [애플리케이션을 Azure SQL Database Managed Instance에 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하세요.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database Managed Instance 커넥터 관련 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database Managed Instance 연결된 서비스에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SqlServer**로 설정해야 합니다. | 예. |
| connectionString |이 속성은 SQL 인증 또는 Windows 인증을 사용하여 Managed Instance에 연결하는 데 필요한 connectionString 정보를 지정합니다. 자세한 내용은 다음 예제를 참조하세요. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예. |
| userName |Windows 인증을 사용하는 경우 이 속성은 사용자 이름을 지정합니다. **domainname\\username**을 예로 들 수 있습니다. |아니요. |
| password |이 속성은 사용자 이름에 대해 지정한 사용자 계정의 암호를 지정합니다. connectionString 정보를 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)하려면 **SecureString**을 선택합니다. |아니요. |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. Managed Instance와 동일한 가상 네트워크에서 자체 호스팅 통합 런타임을 프로비전합니다. |예. |

>[!TIP]
>오류 코드 "UserErrorFailedToConnectToSqlServer" 및 "데이터베이스의 세션 제한(XXX)에 도달했습니다."와 같은 메시지가 표시될 수 있습니다. 이 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

**예제 1: SQL 인증 사용**

```json
{
    "name": "AzureSqlMILinkedService",
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
    "name": "AzureSqlMILinkedService",
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

**예제 3: Windows 인증 사용**

```json
{
    "name": "AzureSqlMILinkedService",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 Azure SQL Database Managed Instance 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Azure SQL Database Managed Instance 간에 데이터를 복사하려면 데이터 세트의 type 속성을 **SqlServerTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **SqlServerTable**로 설정해야 합니다. | 예. |
| tableName |이 속성은 연결된 서비스가 참조하는 데이터베이스 인스턴스의 테이블이나 뷰 이름입니다. | 값은 원본의 경우 No이고 싱크의 경우 Yes입니다. |

**예제**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure SQL Database Managed Instance 원본 및 싱크에서 지원하는 속성의 목록을 제공합니다.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Azure SQL Database Managed Instance(원본)

Azure SQL Database Managed Instance에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SqlSource**로 설정합니다. 복사 작업 source 섹션에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **SqlSource**로 설정해야 합니다. | 예. |
| sqlReaderQuery |이 속성은 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `select * from MyTable`입니다. |아니요. |
| sqlReaderStoredProcedureName |이 속성은 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니요. |
| storedProcedureParameters |저장 프로시저용 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대/소문자는 저장 프로시저 매개변수의 이름 및 대/소문자와 일치해야 합니다. |아니요. |

다음 사항에 유의하세요.

- **SqlSource**에 대해 **sqlReaderQuery**가 지정되어 있으면 복사 작업은 Managed Instance 원본에 대해 이 쿼리를 실행하여 데이터를 가져옵니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- **sqlReaderQuery** 또는 **sqlReaderStoredProcedureName** 중 하나를 지정하지 않는 경우, 데이터 세트 JSON의 "structure" 섹션에 정의된 열이 쿼리를 생성하는 데 사용됩니다. `select column1, column2 from mytable` 쿼리는 Managed Instance에 대해 실행됩니다. 데이터 세트 정의에 "structure"가 없는 경우 테이블에서 모든 열이 선택됩니다.

**예제: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**예제: 저장 프로시저 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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

**저장 프로시저 정의**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Azure SQL Database Managed Instance(싱크)

Azure SQL Database Managed Instance에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlSink**로 설정합니다. 복사 작업 sink 섹션에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 **SqlSink**로 설정해야 합니다. | 예. |
| writeBatchSize |SQL 테이블에 삽입 하는 행 수가 **일괄 처리당**합니다.<br/>허용되는 값은 행 수에 해당하는 정수입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout |이 속성은 시간이 초과되기 전에 완료하려는 배치 삽입 작업의 대기 시간을 지정합니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)을 지정할 수 있습니다. |아니요. |
| preCopyScript |이 속성은 Managed Instance에 데이터를 쓰기 전에 실행할 복사 작업용 SQL 쿼리를 지정하며 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아니요. |
| sqlWriterStoredProcedureName |원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. 고유한 비즈니스 논리를 사용하여 upsert 또는 변환을 수행하는 프로시저를 예로 들 수 있습니다. <br/><br/>이 저장 프로시저는 *배치마다 호출*됩니다. 한 번만 실행되며 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `preCopyScript` 속성을 사용합니다. |아니요. |
| storedProcedureParameters |저장 프로시저에 사용되는 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대/소문자는 저장 프로시저 매개변수의 이름 및 대/소문자와 일치해야 합니다. |아니요. |
| sqlWriterTableType |이 속성은 저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |아니요. |

> [!TIP]
> Azure SQL Database Managed Instance로 데이터를 복사할 때 복사 작업은 기본적으로 싱크 테이블에 데이터를 추가합니다. upsert 또는 추가 비즈니스 논리를 수행하려면 SqlSink에서 저장 프로시저를 사용합니다. 자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요.

**예제 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

**예제 2: upsert를 위한 복사 중에 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
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

이 예제에서는 ID 열이 없는 원본 테이블에서 ID 열이 있는 대상 테이블로 데이터를 복사합니다.

**원본 테이블**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```

**대상 테이블**

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

원본 및 대상 테이블의 스키마는 서로 다릅니다. 대상 테이블에 ID 열이 있습니다. 이 시나리오에서는 ID 열을 포함하지 않는 대상 데이터 세트 정의에서 "structure" 속성을 지정합니다.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

Azure SQL Database Managed Instance로 데이터를 복사할 때 저장 프로시저를 구성하고 지정한 추가 매개 변수를 사용하여 호출할 수 있습니다.

기본 제공 복사 메커니즘이 용도에 적합하지 않은 경우, 저장 프로시저를 사용할 수 있습니다. 저장 프로시저는 일반적으로 대상 테이블에 원본 데이터를 최종 삽입하기 전에 upsert(업데이트+삽입) 또는 추가 처리를 수행해야 할 때 사용됩니다. 추가 처리에는 열 병합, 추가 값 조회, 여러 테이블에 삽입 등이 포함될 수 있습니다.

다음 샘플에서는 저장 프로시저를 사용하여 SQL Server 데이터베이스 내 테이블에 간단한 삽입을 수행하는 방법을 보여줍니다. 각기 다음과 같은 세 개 열을 갖는 입력 데이터와 싱크 **Marketing** 테이블을 가정해 보겠습니다. **ProfileID**, **State** 및 **Category**. **ProfileID** 열을 기준으로 upsert(업데이트/삽입)를 수행하고 특정 범주에 대해서만 적용합니다.

**출력 데이터 집합:** "tableName" 저장된 프로시저 (저장된 프로시저 스크립트 아래 참조)의 동일한 테이블 형식 매개 변수 이름 이어야 합니다.

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

데이터베이스에서 sqlWriterTableType과 동일한 이름으로 테이블 형식을 정의합니다. 테이블 형식의 스키마는 입력 데이터에서 반환된 스키마와 같아야 합니다.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL
)
```

저장 프로시저 기능은 [테이블 반환 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 활용합니다.

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance에 대한 데이터 형식 매핑

Azure SQL Database Managed Instance 간에 데이터를 복사할 때는 Managed Instance 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database Managed Instance 데이터 형식 | Azure Data Factory 중간 데이터 형식 |
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
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 Azure Data Factory는 현재 최대 28자리의 데이터를 지원합니다. 자릿수가 28자리를 초과하는 데이터가 있으면 SQL 쿼리에서 문자열로 변환하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
