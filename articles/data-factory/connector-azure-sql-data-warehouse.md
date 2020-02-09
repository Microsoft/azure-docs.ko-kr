---
title: Azure Synapse Analytics에서 데이터 복사 및 변환
description: Data Factory를 사용 하 여 azure Synapse Analytics에서 데이터를 복사 하 고 Azure Synapse Analytics에서 데이터를 변환 하는 방법을 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: f009b438cb0dc227289d65604d89c11fd382b675
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892971"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Synapse Analytics (이전의 Azure SQL Data Warehouse)에서 데이터 복사 및 변환 

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스의 버전을 선택 합니다."]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [현재 버전](connector-azure-sql-data-warehouse.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Azure Synapse Analytics에서 데이터를 복사 하 고 데이터 흐름을 사용 하 여 Azure Data Lake Storage Gen2 데이터를 변환 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Synapse Analytics 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 행렬](copy-activity-overview.md) 테이블이 포함 된 [복사 작업](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 활동의 경우이 Azure Synapse Analytics 커넥터는 다음과 같은 기능을 지원 합니다.

- 서비스 주체 또는 Azure 리소스에 대한 관리 ID를 통해 SQL 인증 및 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다.
- 싱크로는 [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 또는 [COPY 문](#use-copy-statement) (미리 보기) 또는 bulk insert를 사용 하 여 데이터를 로드 합니다. 더 나은 복사 성능을 위해 PolyBase 또는 COPY 문 (미리 보기)을 권장 합니다.

> [!IMPORTANT]
> Azure Data Factory Integration Runtime을 사용하여 데이터를 복사하는 경우, Azure 서비스가 서버에 액세스할 수 있도록 [Azure SQL Server 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성합니다.
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우, 적절한 IP 범위를 허용하도록 Azure SQL Server 방화벽을 구성합니다. 이 범위에는 Azure Synapse Analytics에 연결 하는 데 사용 되는 컴퓨터의 IP가 포함 됩니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> 최상의 성능을 얻으려면 PolyBase를 사용 하 여 Azure Synapse Analytics로 데이터를 로드 합니다. [PolyBase를 사용 하 여 Azure Synapse Analytics로 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션에서 자세히 설명 합니다. 사용 사례가 포함 된 연습은 [Azure Data Factory를 사용 하 여 15 분 이내에 Azure Synapse Analytics에 1Tb 로드](load-azure-sql-data-warehouse.md)를 참조 하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Synapse Analytics 커넥터와 관련 된 Data Factory 엔터티를 정의 하는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Synapse Analytics 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성            | Description                                                  | 필수                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | type 속성은 **AzureSqlDW**로 설정해야 합니다.             | 예                                                          |
| connectionString    | **ConnectionString** 속성에 대한 Azure Synapse Analytics 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호/서비스 주체 키를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예                                                          |
| servicePrincipalId  | 애플리케이션의 클라이언트 ID를 지정합니다.                         | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| tenant              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| connectVia          | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 아닙니다.                                                           |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- Azure AD 애플리케이션 토큰 인증: [서비스 주체](#service-principal-authentication)
- Azure AD 애플리케이션 토큰 인증: [Azure 리소스에 대한 관리 ID](#managed-identity)

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" 오류 코드 및 "데이터베이스에 대한 세션 제한이 XXX이고 이에 도달했습니다."와 같은 메시지가 있는 오류가 발생하면 연결 문자열에 `Pooling=false`를 추가하고 다시 시도하세요.

### <a name="sql-authentication"></a>SQL 인증

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault의 암호:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 기반의 Azure AD 애플리케이션 토큰 인증을 사용하려면 다음 단계를 따르세요.

1. Azure Portal에서 **[Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** . 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 아직 수행하지 않은 경우 Azure Portal에서 Azure SQL Server에 대해 **[Azure Active Directory 관리자를 프로비저닝](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

3. 서비스 주체에 대한 **[포함된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여**합니다. 다음 코드를 실행 하거나 [여기](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)에서 기타 옵션을 참조 합니다. PolyBase를 사용 하 여 데이터를 로드 하려면 [필요한 데이터베이스 사용 권한을](#required-database-permission)알아보세요.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Azure Data Factory에서 **Azure Synapse Analytics 연결 된 서비스를 구성** 합니다.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Azure 리소스 인증용 관리 ID

데이터 팩터리는 특정 팩터리를 나타내는 [Azure 리소스에 대한 관리 ID](data-factory-service-identity.md)와 연결할 수 있습니다. Azure Synapse Analytics 인증에이 관리 되는 id를 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터 웨어하우스의 데이터에 액세스하고 복사할 수 있습니다.

관리 되는 id 인증을 사용 하려면 다음 단계를 수행 합니다.

1. 아직 수행하지 않은 경우 Azure Portal에서 Azure SQL Server에 대해 **[Azure Active Directory 관리자를 프로비저닝](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

2. 관리 되는 Data Factory Id에 대한 **[포함 된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL 사용자 및 다른 사용자가 일반적으로 수행 하는 대로 **Data Factory 관리 id에 필요한 권한을 부여** 합니다. 다음 코드를 실행 하거나 [여기](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)에서 기타 옵션을 참조 합니다. PolyBase를 사용 하 여 데이터를 로드 하려면 [필요한 데이터베이스 사용 권한을](#required-database-permission)알아보세요.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. Azure Data Factory에서 **Azure Synapse Analytics 연결 된 서비스를 구성** 합니다.

**예:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

Azure Synapse Analytics 데이터 집합에 대해 지원 되는 속성은 다음과 같습니다.

| 속성  | Description                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 세트의 **type** 속성을 **AzureSqlDWTable**로 설정해야 합니다. | 예                         |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함 된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원 됩니다. 새 워크 로드의 경우 `schema` 및 `table`를 사용 합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

#### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Synapse Analytics 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics를 원본으로

Azure Synapse Analytics에서 데이터를 복사 하려면 복사 작업 원본의 **type** 속성을 **SqlDWSource**로 설정 합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성                     | Description                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 **type** 속성을 **SqlDWSource**로 설정해야 합니다. | 예      |
| SqlReaderQuery               | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. | 아닙니다.       |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 아닙니다.       |
| storedProcedureParameters    | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아닙니다.       |

**예: SQL 쿼리 사용**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**예제 저장 프로시저:**

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

### <a name="azure-sql-data-warehouse-as-sink"></a>싱크로 Azure Synapse Analytics

Azure Data Factory는 SQL Data Warehouse에 데이터를 로드 하는 세 가지 방법을 지원 합니다.

![SQL DW 싱크 복사 옵션](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [PolyBase 사용](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [COPY 문 사용 (미리 보기)](#use-copy-statement)
- Bulk insert 사용

데이터를 로드 하는 가장 빠르고 확장성 있는 방법은 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 또는 [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (미리 보기)을 통하는 것입니다.

Azure SQL Data Warehouse에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlDWSink**로 설정합니다. 복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 복사 작업 싱크의 **type** 속성은 **SqlDWSink**로 설정해야 합니다. | 예                                           |
| allowPolyBase     | PolyBase를 사용 하 여 데이터를 SQL Data Warehouse에 로드할지 여부를 나타냅니다. `allowCopyCommand`와 `allowPolyBase`는 모두 true 일 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure SQL Data Warehouse로 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다. | 아닙니다.<br/>PolyBase를 사용 하는 경우 적용 합니다.     |
| polyBaseSettings  | `allowPolybase` 속성이 **true**로 설정 된 경우 지정할 수 있는 속성 그룹입니다. | 아닙니다.<br/>PolyBase를 사용 하는 경우 적용 합니다. |
| allowCopyCommand | [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (미리 보기)을 사용 하 여 데이터를 SQL Data Warehouse에 로드할지 여부를 나타냅니다. `allowCopyCommand`와 `allowPolyBase`는 모두 true 일 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [COPY 문을 사용 하 여 Azure SQL Data Warehouse에 데이터 로드](#use-copy-statement) 섹션을 참조 하세요.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다. | 아닙니다.<br>COPY를 사용할 때 적용 됩니다. |
| copyCommandSettings | `allowCopyCommand` 속성이 TRUE로 설정 된 경우 지정할 수 있는 속성 그룹입니다. | 아닙니다.<br/>COPY를 사용할 때 적용 됩니다. |
| writeBatchSize    | **일괄**처리당 SQL 테이블에 삽입할 행 수입니다.<br/><br/>허용되는 값은 **정수**(행 수)입니다. 기본적으로 Data Factory는 행 크기에 따라 적절 한 일괄 처리 크기를 동적으로 결정 합니다. | 아닙니다.<br/>Bulk insert를 사용할 때 적용 됩니다.     |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/><br/>허용되는 값은 **시간 범위**입니다. 예: “00:30:00”(30분). | 아닙니다.<br/>Bulk insert를 사용할 때 적용 됩니다.        |
| preCopyScript     | 각 실행 시 Azure SQL Data Warehouse에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 아닙니다.                                            |
| tableOption | 원본 스키마에 따라 존재 하지 않는 경우 싱크 테이블을 자동으로 만들지 여부를 지정 합니다. 준비 된 복사본이 복사 작업에서 구성 된 경우 자동 테이블 만들기가 지원 되지 않습니다. 허용 되는 값은 `none` (기본값) `autoCreate`입니다. |아닙니다. |
| disableMetricsCollection | Data Factory 복사 성능 최적화 및 권장 사항에 대한 SQL Data Warehouse DWUs와 같은 메트릭을 수집 합니다. 이 동작에 관심이 있는 경우 `true` 지정 하 여 해제 합니다. | 아니요(기본값: `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL Data Warehouse 싱크 예제

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 를 사용 하는 것은 처리량이 높은 Azure Synapse Analytics로 많은 양의 데이터를 로드 하는 효율적인 방법입니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하면 처리량이 훨씬 증가합니다. 사용 사례에 대한 연습은 [Azure Synapse Analytics로 1Tb 로드](v1/data-factory-load-sql-data-warehouse.md)를 참조 하세요.

* 원본 데이터가 **Azure Blob, Azure Data Lake Storage Gen1 또는 Azure Data Lake Storage Gen2**에 있고 **형식이 polybase 호환**인 경우 복사 작업을 사용 하 여 polybase를 직접 호출 하 여 원본에서 데이터를 Azure SQL Data Warehouse 가져올 수 있습니다. 자세한 내용은 **[PolyBase를 사용하여 직접 복사](#direct-copy-by-using-polybase)** 를 참조하세요.
* 원본 데이터 저장소와 형식이 PolyBase에서 원래 지원되지 않는 경우, 대신 **[PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)** 기능을 사용합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 데이터를 PolyBase 호환 형식으로 자동으로 변환 하 고, 데이터를 Azure Blob 저장소에 저장 합니다. 그런 다음 PolyBase를 호출 하 여 데이터를 SQL Data Warehouse으로 로드 합니다.

>[!TIP]
>[PolyBase 사용에 대한 모범 사례](#best-practices-for-using-polybase)에 대해 자세히 알아보세요.

복사 작업의 `polyBaseSettings`에서 다음 PolyBase 설정이 지원 됩니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다.<br/><br/>[CREATE EXTERNAL TABLE(Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)의 인수 섹션에서 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. <br/><br/>허용되는 값은 0(기본값), 1, 2 등입니다. | 아닙니다.                                            |
| rejectType        | **rejectValue** 옵션이 리터럴 값인지 또는 백분율인지를 지정합니다.<br/><br/>허용되는 값은 **Value**(기본값) 및 **Percentage**입니다. | 아닙니다.                                            |
| rejectSampleValue | PolyBase가 거부된 행의 백분율을 다시 계산하기 전에 검색할 행 수를 결정합니다.<br/><br/>허용되는 값은 1, 2 등입니다. | **rejectType**이 **percentage**인 경우 예 |
| useTypeDefault    | PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다.<br><br> | 아닙니다.                                            |

### <a name="direct-copy-by-using-polybase"></a>PolyBase를 사용한 직접 복사

PolyBase SQL Data Warehouse Azure Blob, Azure Data Lake Storage Gen1 및 Azure Data Lake Storage Gen2를 직접 지원 합니다. 원본 데이터가이 섹션에 설명 된 조건을 충족 하는 경우 PolyBase를 사용 하 여 원본 데이터 저장소에서 Azure Synapse Analytics로 직접 복사 합니다. 조건을 충족하지 않는 경우, [PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)를 사용합니다.

> [!TIP]
> SQL Data Warehouse에 효율적으로 데이터를 복사 하기 위해 Azure Data Factory에서 자세한 정보를 통해 [SQL Data Warehouse에서 Data Lake Store를 사용할 때 데이터를 보다 쉽고 편리 하 게 파악할 수](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)있습니다.

조건을 충족하지 않는 경우, Azure Data Factory는 설정을 확인한 후 데이터 이동을 위해 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1. **원본 연결 된 서비스** 는 다음과 같은 형식 및 인증 방법을 사용 합니다.

    | 지원 되는 원본 데이터 저장소 유형                             | 지원 되는 원본 인증 유형                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 계정 키 인증, 관리 id 인증 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 서비스 주체 인증                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 계정 키 인증, 관리 id 인증 |

    >[!IMPORTANT]
    >Azure Storage VNet 서비스 엔드포인트을 사용 하 여 구성 된 경우 관리 되는 id 인증을 사용 해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트을 사용 하는 경우의 영향](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)을 참조 하세요. [Azure Blob 관리 id 인증](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2 관리 id 인증](connector-azure-data-lake-storage.md#managed-identity) 섹션의 Data Factory에서 필요한 구성을 알아봅니다.

2. **원본 데이터 형식은** 다음과 같은 구성 **으로 Parquet**, **ORC**또는 **구분 된 텍스트**입니다.

   1. 폴더 경로에 와일드 카드 필터가 포함 되어 있지 않습니다.
   2. 파일 이름이 비어 있거나 단일 파일을 가리킵니다. 복사 활동에서 와일드 카드 파일 이름을 지정 하는 경우 `*` 또는 `*.*`만 가능 합니다.
   3. `rowDelimiter` **기본값**, **\n**, **\r\n**또는 **\r**입니다.
   4. `nullValue`는 기본값 이거나 **빈 문자열** ("")로 설정 되 고 `treatEmptyAsNull`는 기본값으로 유지 되거나 true로 설정 됩니다.
   5. `encodingName`는 기본값으로 유지 되거나 **u t f-8**로 설정 됩니다.
   6. `quoteChar`, `escapeChar`및 `skipLineCount` 지정 되지 않았습니다. PolyBase는 ADF에서 `firstRowAsHeader`로 구성할 수 있는 skip 머리글 행을 지원 합니다.
   7. `compression`은 **no compression**, **GZip** 또는 **Deflate**일 수 있습니다.

3. 소스가 폴더 인 경우 복사 작업의 `recursive`을 true로 설정 해야 합니다.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`및 `modifiedDateTimeEnd` 지정 되지 않았습니다.

>[!NOTE]
>원본이 폴더인 경우 PolyBase는 폴더와 모든 하위 폴더에서 파일을 검색 하 고, 여기에 설명 된 [위치 인수](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)에 설명 된 대로 파일 이름이 밑줄 (_) 또는 마침표 (.)로 시작 하는 파일에서 데이터를 검색 하지 않습니다.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>PolyBase를 사용한 준비된 복사

원본 데이터가 PolyBase와 기본적으로 호환 되지 않는 경우 임시 스테이징 Azure Blob 저장소 인스턴스를 통해 데이터 복사를 사용 하도록 설정 합니다 (Azure Premium Storage 일 수 없음). 이 경우 Azure Data Factory는 PolyBase의 데이터 형식 요구 사항을 충족 하도록 데이터를 자동으로 변환 합니다. 그런 다음 PolyBase를 호출 하 여 SQL Data Warehouse에 데이터를 로드 합니다. 마지막으로, Blob Storage에서 임시 데이터를 정리합니다. 준비 Azure Blob Storage 인스턴스를 통해 데이터를 복사하는 방법에 대한 자세한 내용은 [준비된 복사](copy-activity-performance.md#staged-copy)를 참조하세요.

이 기능을 사용 하려면 임시 Blob storage를 사용 하 여 Azure storage 계정을 참조 하는 [Azure Blob Storage 연결 된 서비스](connector-azure-blob-storage.md#linked-service-properties) 를 만듭니다. 그런 다음, 다음 코드와 같이 복사 활동에 대한 `enableStaging` 및 `stagingSettings` 속성을 지정 합니다.

>[!IMPORTANT]
>스테이징 Azure Storage VNet 서비스 엔드포인트을 사용 하 여 구성 된 경우 관리 id 인증을 사용 해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트을 사용 하는 경우의 영향](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)을 참조 하세요. [Azure Blob 관리 id 인증](connector-azure-blob-storage.md#managed-identity)에서 Data Factory에 필요한 구성을 알아봅니다.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>PolyBase를 사용하는 모범 사례

다음 섹션에서는 [Azure Synapse Analytics 모범 사례](../sql-data-warehouse/sql-data-warehouse-best-practices.md)에 설명 된 모범 사례 외에도 모범 사례를 제공 합니다.

#### <a name="required-database-permission"></a>필수 데이터베이스 권한

PolyBase를 사용하려면 SQL Data Warehouse에 데이터를 로드하는 사용자에게 대상 데이터베이스에 대한 [“CONTROL” 권한](https://msdn.microsoft.com/library/ms191291.aspx)이 있어야 합니다. 한 가지 방법은 해당 사용자를 **db_owner** 역할의 구성원으로 추가하는 것입니다. [SQL Data Warehouse 개요](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)에서 작업 방법을 알아보세요.

#### <a name="row-size-and-data-type-limits"></a>행 크기 및 데이터 형식 한도

PolyBase는 1MB보다 작은 행으로 제한됩니다. 작고 VARCHR로 (MAX), NVARCHAR (MAX) 또는 VARBINARY (MAX)로 로드 하는 데 사용할 수 없습니다. 자세한 내용은 [SQL Data Warehouse 서비스 용량 한도](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)를 참조하세요.

원본 데이터에 1MB보다 큰 행이 있는 경우, 원본 테이블을 여러 개의 작은 테이블로 수직 분할하는 것이 좋습니다. 각 행의 최대 크기가 한도를 초과하지 않는지 확인합니다. 그런 다음 더 작은 테이블을 PolyBase를 사용 하 여 로드 하 고 Azure Synapse Analytics에서 함께 병합할 수 있습니다.

또는 이러한 광범위 한 열이 포함 된 데이터의 경우 PolyBase를 사용 하 여 ADF를 사용 하 여 데이터를 로드할 수 있습니다. "PolyBase 허용" 설정을 해제 합니다.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse 리소스 클래스

가능한 최고 수준까지 처리량을 높이려면 PolyBase를 통해 SQL Data Warehouse에 데이터를 로드하는 사용자에게 더 큰 리소스 클래스를 할당합니다.

#### <a name="polybase-troubleshooting"></a>PolyBase 문제 해결

**Decimal 열로 로드**

원본 데이터가 텍스트 형식이 나 기타 PolyBase 호환 저장소 (준비 된 복사 및 PolyBase 사용)에 있는 경우 SQL Data Warehouse Decimal 열에 로드할 빈 값을 포함 하는 경우 다음 오류가 발생할 수 있습니다.

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

이 솔루션은 복사 작업 싱크에서 PolyBase 설정 > "**형식 기본값 사용**" 옵션을 선택 취소 하는 것입니다 (false로 설정). "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)"은 polybase 네이티브 구성으로, polybase가 텍스트 파일에서 데이터를 검색할 때 구분 된 텍스트 파일에서 누락 값을 처리 하는 방법을 지정 합니다. 

**Azure Synapse Analytics의 `tableName`**

다음 표에서는 JSON 데이터 세트의 **tableName** 속성을 지정하는 방법에 대한 예를 제공합니다. 스키마 및 테이블 이름의 여러 조합을 보여 줍니다.

| DB 스키마 | 테이블 이름 | **tableName** JSON 속성               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable 또는 dbo.MyTable 또는 [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable 또는 [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] 또는 [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

다음 오류가 표시되는 경우, **tableName** 속성에 대해 지정한 값이 문제일 수 있습니다. **tableName** JSON 속성의 값을 지정하는 올바른 방법은 앞의 표를 참조하세요.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**기본값이 있는 열**

현재, Data Factory의 PolyBase 기능은 대상 테이블과 동일한 열 수만 허용합니다. 예를 들어, 네 개의 열이 있고 그 중 한 열이 기본값으로 정의된 테이블이 있다고 가정합니다. 이 경우, 입력 데이터에 네 개의 열이 있어야 합니다. 3열 입력 데이터 세트를 제공하면 다음 메시지와 비슷한 오류가 발생합니다.

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL 값은 특별한 형태의 기본값입니다. 열이 Null을 허용하는 경우, 해당 열에 대한 Blob의 입력 데이터가 비어 있을 수 있습니다. 그러나 입력 데이터 세트에서는 누락할 수 없습니다. PolyBase는 Azure Synapse Analytics에서 누락 값에 대해 NULL을 삽입 합니다.

## <a name="use-copy-statement"></a>COPY 문을 사용 하 여 Azure SQL Data Warehouse (미리 보기)에 데이터 로드

SQL Data Warehouse [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (미리 보기)은 **Azure Blob 및 Azure Data Lake Storage Gen2**에서 데이터 로드를 직접 지원 합니다. 원본 데이터가이 섹션에 설명 된 조건을 충족 하는 경우 ADF에서 COPY 문을 사용 하 여 데이터를 Azure SQL Data Warehouse 로드 하도록 선택할 수 있습니다. Azure Data Factory는 설정을 확인 하 고 조건이 충족 되지 않으면 복사 작업 실행에 실패 합니다.

>[!NOTE]
>현재는 아래에 설명 된 COPY 문 호환 소스의 복사만 지원 Data Factory.

COPY 문을 사용 하면 다음 구성이 지원 됩니다.

1. **원본 연결 된 서비스와 형식은** 다음과 같은 형식 및 인증 방법을 사용 합니다.

    | 지원 되는 원본 데이터 저장소 유형                             | 지원 되는 형식           | 지원 되는 원본 인증 유형                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [구분 기호로 분리 된 텍스트](format-delimited-text.md)             | 계정 키 인증, 공유 액세스 서명 인증, 서비스 주체 인증, 관리 id 인증 |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | 계정 키 인증, 공유 액세스 서명 인증 |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | 계정 키 인증, 공유 액세스 서명 인증 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [구분 기호로 분리 된 텍스트](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | 계정 키 인증, 서비스 주체 인증, 관리 id 인증 |

    >[!IMPORTANT]
    >Azure Storage VNet 서비스 엔드포인트을 사용 하 여 구성 된 경우 관리 되는 id 인증을 사용 해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트을 사용 하는 경우의 영향](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)을 참조 하세요. [Azure Blob 관리 id 인증](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2 관리 id 인증](connector-azure-data-lake-storage.md#managed-identity) 섹션의 Data Factory에서 필요한 구성을 알아봅니다.

2. 서식 설정에는 다음이 포함 됩니다.

   1. **Parquet**: `compression`에는 압축, **Snappy**또는 **GZip**이 **없을**수 있습니다.
   2. **ORC**: `compression`는 압축, **zlib**또는 **Snappy**수 **없습니다**.
   3. **구분 기호로 분리 된 텍스트**:
      1. `rowDelimiter` 명시적으로 **단일 문자** 또는 " **\r\n**"로 설정 되어 있으므로 기본값은 지원 되지 않습니다.
      2. `nullValue`는 기본값으로 유지 되거나 **빈 문자열** ("")로 설정 됩니다.
      3. `encodingName`는 기본값으로 유지 되거나 **utf-8 또는 u t f-16**으로 설정 됩니다.
      4. `escapeChar`은 `quoteChar`와 동일 해야 하며 비어 있지 않습니다.
      5. `skipLineCount`는 기본값으로 유지 되거나 0으로 설정 됩니다.
      6. `compression`은 압축과 **GZip**일 수 **없습니다** .

3. 소스가 폴더 인 경우 복사 작업의 `recursive`을 true로 설정 해야 합니다.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`및 `modifiedDateTimeEnd` 지정 되지 않았습니다.

복사 작업의 `allowCopyCommand`에서 다음 복사 문 설정이 지원 됩니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | SQL DW의 각 대상 열에 대한 기본값을 지정 합니다.  속성의 기본값은 데이터 웨어하우스의 기본 제약 조건 집합을 덮어쓰고 id 열에는 기본값을 사용할 수 없습니다. | 아닙니다. |
| additionalOptions | [Copy 문의](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)"With" 절에서 직접 SQL DW 복사 문에 전달 되는 추가 옵션입니다. COPY 문 요구 사항에 맞게 조정 하는 데 필요한 값을 따옴표로 묶습니다. | 아닙니다. |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md) 을 확인 하세요. 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 대한 데이터 형식 매핑

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환 하는 경우 Azure Synapse Analytics에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 데이터 흐름 매핑에서 [원본 변환](data-flow-source.md) 및 [싱크 변환을](data-flow-sink.md) 참조 하세요.

### <a name="source-transformation"></a>원본 변환

Azure Synapse Analytics에 특정 한 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다. 

**입력:** 원본 위치를 테이블에 표시할지 (```Select * from <table-name>```와 동일) 선택 하거나 사용자 지정 SQL 쿼리를 입력 합니다.

**쿼리**: 입력 필드에서 쿼리를 선택 하는 경우 원본에 대한 SQL 쿼리를 입력 합니다. 이 설정은 데이터 집합에서 선택한 테이블을 재정의 합니다. **Order by** 절은 여기서 지원 되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * From udfGetData ()** 는 테이블을 반환 하는 SQL의 UDF입니다. 이 쿼리는 데이터 흐름에서 사용할 수 있는 원본 테이블을 생성 합니다. 쿼리를 사용 하는 것은 테스트 또는 조회를 위해 행을 줄이는 좋은 방법 이기도 합니다. 

* SQL 예: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**일괄 처리 크기**: 대량 데이터를 읽기로 청크 하는 일괄 처리 크기를 입력 합니다.

**격리 수준**: 매핑 데이터 흐름에서 SQL 원본의 기본값은 커밋되지 않은 읽기입니다. 여기에서 격리 수준을 다음 값 중 하나로 변경할 수 있습니다.
* 커밋된 읽기
* 커밋되지 않은 읽기
* 반복 가능한 읽기
* 직렬화 가능
* 없음 (격리 수준 무시)

![격리 수준](media/data-flow/isolationlevel.png "격리 수준")

### <a name="sink-transformation"></a>싱크 변환

Azure Synapse Analytics에 특정 한 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 방법:** 데이터베이스 대상에서 허용 되는 작업을 결정 합니다. 기본값은 삽입만 허용 하는 것입니다. 행을 업데이트, upsert 또는 삭제 하려면 해당 작업에 대한 행의 태그를 변경 하는 행을 변경 해야 합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정 하기 위해 키 열을 설정 해야 합니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지를 결정 합니다.
* 없음: 테이블에 대한 작업이 수행 되지 않습니다.
* 다시 만들기: 테이블이 삭제 되 고 다시 생성 됩니다. 동적으로 새 테이블을 만드는 경우 필요 합니다.
* Truncate: 대상 테이블의 모든 행이 제거 됩니다.

**준비 사용:** Azure Synapse Analytics에 쓸 때 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) 를 사용할지 여부를 결정 합니다.

**일괄 처리 크기**: 각 버킷에 작성 되는 행 수를 제어 합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상 되지만 데이터를 캐시할 때 메모리 예외가 발생할 위험이 있습니다.

**사전 및 사후 sql 스크립트**: 데이터를 싱크 데이터베이스에 기록 하기 전 (전처리) 및 이후 (사후 처리) 데이터를 실행 하는 여러 줄의 sql 스크립트를 입력 합니다.

![사전 및 사후 SQL 처리 스크립트](media/data-flow/prepost1.png "SQL 처리 스크립트")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 대한 데이터 형식 매핑

Azure Synapse Analytics에서 데이터를 복사 하는 경우 Azure Synapse Analytics 데이터 형식에서 중간 데이터 형식을 Azure Data Factory 하는 다음과 같은 매핑이 사용 됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

>[!TIP]
>SQL DW에서 지원 되는 데이터 형식 및 지원 되지 않는 데이터 형식에 대한 자세한 내용은 [Azure Synapse Analytics의 테이블 데이터 형식](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) 문서를 참조 하세요.

| Azure Synapse Analytics 데이터 형식    | Data Factory 중간 데이터 형식 |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | 부울                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| DateTime                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM 특성(varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| 이미지                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | 단일                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
