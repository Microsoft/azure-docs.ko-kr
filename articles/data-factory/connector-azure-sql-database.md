---
title: Azure SQL Database에서 데이터 복사 및 변환
description: Azure Data Factory를 사용 하 여 Azure SQL Database에서 데이터를 복사 하 고 Azure SQL Database 데이터를 변환 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/28/2020
ms.openlocfilehash: def57dc125a148abd330643fc5848a35cd3b52bf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76991011"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure SQL Database 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-sql-connector.md)
> * [현재 버전](connector-azure-sql-database.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Azure SQL Database 간에 데이터를 복사 하 고 데이터 흐름을 사용 하 여 Azure SQL Database 데이터를 변환 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure SQL Database 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 행렬](copy-activity-overview.md) 테이블이 포함 된 [복사 작업](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 활동의 경우이 Azure SQL Database 커넥터는 다음과 같은 기능을 지원 합니다.

- SQL 인증 Azure Active Directory 및 azure AD (azure AD) 응용 프로그램 토큰 인증을 사용 하 여 Azure 리소스에 대한 서비스 주체 또는 관리 id로 데이터를 복사 합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용 하 여 데이터를 검색 합니다.
- 싱크로 서 대상 테이블에 데이터를 추가 하거나 복사 하는 동안 사용자 지정 논리를 사용 하 여 저장 프로시저를 호출 합니다.

>[!NOTE]
>지금은이 커넥터에서 Azure SQL Database [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) 지원 되지 않습니다. 이 문제를 해결 하려면 자체 호스팅 통합 런타임을 통해 [일반 odbc 커넥터](connector-odbc.md) 와 SQL Server ODBC 드라이버를 사용할 수 있습니다. ODBC 드라이버 다운로드 및 연결 문자열 구성에서 [이 지침](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) 을 따르세요.

> [!IMPORTANT]
> Azure Data Factory integration runtime을 사용 하 여 데이터를 복사 하는 경우 azure 서비스에서 서버에 액세스할 수 있도록 [azure SQL Server 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 을 구성 합니다.
> 자체 호스팅 통합 런타임을 사용 하 여 데이터를 복사 하는 경우 적절 한 IP 범위를 허용 하도록 Azure SQL Server 방화벽을 구성 합니다. 이 범위에는 Azure SQL Database 연결 하는 데 사용 되는 컴퓨터의 IP가 포함 됩니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure SQL Database 커넥터에 한정 된 Azure Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure SQL Database 연결된 서비스에 대해 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **AzureSqlDatabase**로 설정해야 합니다. | 예 |
| connectionString | Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 **connectionString** 속성에 대해 지정합니다. <br/>Azure Key Vault에서 암호나 서비스 주체 키를 입력할 수도 있습니다. SQL 인증 인 경우 연결 문자열에서 `password` 구성을 끌어옵니다. 자세한 내용은 표 다음에 나오는 JSON 예를 참조 하 고 [Azure Key Vault에 자격 증명을 저장](store-credentials-in-key-vault.md)합니다. | 예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예, 서비스 주체와 함께 Azure AD 인증을 사용 하는 경우 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString** 으로 표시 하 여 Azure Data Factory에 안전 하 게 저장 하거나 [Azure Key Vault에 저장 된 암호를 참조](store-credentials-in-key-vault.md)합니다. | 예, 서비스 주체와 함께 Azure AD 인증을 사용 하는 경우 |
| tenant | 응용 프로그램이 상주 하는 도메인 이름 또는 테넌트 ID와 같은 테넌트 정보를 지정 합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 가져가면 검색 합니다. | 예, 서비스 주체와 함께 Azure AD 인증을 사용 하는 경우 |
| connectVia | 이 [Integration Runtime](concepts-integration-runtime.md)은 데이터 저장소에 연결하는 데 사용됩니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅 integration runtime을 사용할 수 있습니다. 지정 하지 않으면 기본 Azure 통합 런타임이 사용 됩니다. | 아닙니다. |

다른 인증 형식의 경우, 각각의 필수 조건 및 JSON 샘플에 대한 다음 섹션을 참조하세요.

- [SQL 인증](#sql-authentication)
- [Azure AD 애플리케이션 토큰 인증: 서비스 주체](#service-principal-authentication)
- [Azure AD 애플리케이션 토큰 인증: Azure 리소스용 관리 ID](#managed-identity)

>[!TIP]
>오류 코드 "UserErrorFailedToConnectToSqlServer"와 함께 오류가 발생 하 고 "데이터베이스에 대한 세션 제한 값이 XXX 이며이에 도달 했습니다" 라는 메시지가 표시 되 면 연결 문자열에 `Pooling=false`을 추가 하 고 다시 시도 합니다.

### <a name="sql-authentication"></a>SQL 인증

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

**Azure Key Vault의 암호** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

### <a name="service-principal-authentication"></a>서비스 주체 인증

서비스 주체 기반의 Azure AD 애플리케이션 토큰 인증을 사용하려면 다음 단계를 따르세요.

1. Azure Portal에서 [Azure Active Directory 응용 프로그램을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) . 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 아직 수행 하지 않은 경우 Azure Portal에서 Azure SQL Server에 대한 [Azure Active Directory 관리자를 프로 비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이어야 하지만 서비스 주체일 수는 없습니다. 이 단계가 수행되면, 이후 단계에서 Azure AD ID를 사용하여 서비스 주체에 대한 포함된 데이터베이스 사용자를 만들 수 있습니다.

3. 서비스 사용자에 대한 [포함 된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) . ALTER ANY USER 권한이 있는 Azure AD id를 사용 하 여 SQL Server Management Studio와 같은 도구를 사용 하 여 데이터를 복사 하려는 또는의 데이터베이스에 연결 합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. SQL 사용자 또는 다른 사용자에 게 일반적으로 수행 하는 대로 서비스 주체에 게 필요한 권한을 부여 합니다. 다음 코드를 실행합니다. 자세한 옵션은 [이 문서](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)를 참조하세요.

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Azure Data Factory에서 Azure SQL Database 연결 된 서비스를 구성 합니다.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>서비스 주체 인증을 사용하는 연결된 서비스 예제

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. Azure SQL Database 인증에이 관리 되는 id를 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터베이스에 액세스하고 해당 데이터베이스에 대해 데이터를 복사할 수 있습니다.

관리 id 인증을 사용 하려면 다음 단계를 수행 합니다.

1. 아직 수행 하지 않은 경우 Azure Portal에서 Azure SQL Server에 대한 [Azure Active Directory 관리자를 프로 비전](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) 합니다. Azure ad 관리자는 Azure ad 사용자 또는 Azure AD 그룹 일 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 모든 권한을 가집니다.

2. 관리 되는 Azure Data Factory id에 대한 [포함 된 데이터베이스 사용자를 만듭니다](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) . ALTER ANY USER 권한이 있는 Azure AD id를 사용 하 여 SQL Server Management Studio와 같은 도구를 사용 하 여 데이터를 복사 하려는 또는의 데이터베이스에 연결 합니다. 다음 T-SQL을 실행합니다. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL 사용자 및 다른 사용자가 일반적으로 수행 하는 대로 Data Factory 관리 id에 필요한 권한을 부여 합니다. 다음 코드를 실행합니다. 자세한 옵션은 [이 문서](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)를 참조하세요.

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Azure Data Factory에서 Azure SQL Database 연결 된 서비스를 구성 합니다.

**예제**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
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

데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services)을 참조 하세요. 

Azure SQL Database 데이터 집합에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **AzureSqlTable**로 설정해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함 된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원 됩니다. 새 워크 로드의 경우 `schema` 및 `table`를 사용 합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

#### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 Azure SQL Database 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database가 원본인 경우

Azure SQL Database에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **AzureSqlSource**로 설정 해야 합니다. "SqlSource" 형식은 이전 버전과의 호환성을 위해 계속 지원 됩니다. | 예 |
| SqlReaderQuery | 이 속성은 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `select * from MyTable`입니다. | 아닙니다. |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 아닙니다. |
| storedProcedureParameters | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대/소문자 구분은 저장 프로시저 매개 변수의 이름 및 대/소문자와 일치 해야 합니다. | 아닙니다. |

**주의할 사항:**

- **AzureSqlSource**에 대해 **sqlreaderquery** 를 지정 하는 경우 복사 작업은 데이터를 가져오기 위해 Azure SQL Database 원본에 대해이 쿼리를 실행 합니다. 저장 프로시저가 매개 변수를 사용하는 경우에는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수도 있습니다.
- **Sqlreaderquery** 또는 **sqlReaderStoredProcedureName**를 지정 하지 않으면 JSON 데이터 집합의 "structure" 섹션에 정의 된 열이 쿼리를 생성 하는 데 사용 됩니다. 쿼리 `select column1, column2 from mytable` Azure SQL Database에 대해 실행 됩니다. 데이터 세트 정의에 "structure"가 없는 경우 테이블에서 모든 열이 선택됩니다.

#### <a name="sql-query-example"></a>SQL 쿼리 예제

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>저장 프로시저 예제

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>저장 프로시저 정의

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

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database가 싱크인 경우

> [!TIP]
> [Azure SQL Database에 데이터를 로드 하는 모범](#best-practice-for-loading-data-into-azure-sql-database)사례에서 지원 되는 쓰기 동작, 구성 및 모범 사례에 대해 자세히 알아보세요.

Azure SQL Database에 데이터를 복사 하려면 복사 작업 **싱크** 섹션에서 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **AzureSqlSink**로 설정 해야 합니다. "SqlSink" 형식은 이전 버전과의 호환성을 위해 계속 지원 됩니다. | 예 |
| writeBatchSize | *일괄*처리당 SQL 테이블에 삽입할 행 수입니다.<br/> 허용되는 값은 **정수**(행 수)입니다. 기본적으로 Azure Data Factory는 행 크기에 따라 적절 한 일괄 처리 크기를 동적으로 결정 합니다. | 아닙니다. |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/> 허용되는 값은 **시간 범위**입니다. 예를 들면 "00:30:00" (30 분)입니다. | 아닙니다. |
| preCopyScript | Azure SQL Database에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정 합니다. 복사 실행당 한 번만 호출됩니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 아닙니다. |
| sqlWriterStoredProcedureName | 원본 데이터를 대상 테이블에 적용하는 방법을 정의하는 저장 프로시저의 이름입니다. <br/>이 저장 프로시저는 *배치마다 호출*됩니다. 한 번만 실행 되 고 원본 데이터 (예: 삭제 또는 자르기)와 관련이 없는 작업의 경우에는 `preCopyScript` 속성을 사용 합니다. | 아닙니다. |
| storedProcedureTableTypeParameterName |저장 프로시저에 지정 된 테이블 형식의 매개 변수 이름입니다.  |아닙니다. |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름입니다. 복사 작업에서는 이동 중인 데이터를 이 테이블 형식의 임시 테이블에서 사용할 수 있습니다. 그러면 저장 프로시저 코드가 복사 중인 데이터를 기존 데이터와 병합할 수 있습니다. |아닙니다. |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 및 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 아닙니다. |
| tableOption | 원본 스키마에 따라 존재 하지 않는 경우 싱크 테이블을 자동으로 만들지 여부를 지정 합니다. 싱크가 저장 프로시저를 지정 하거나 준비 된 복사본이 복사 작업에 구성 되어 있으면 자동 테이블 만들기가 지원 되지 않습니다. 허용 되는 값은 `none` (기본값) `autoCreate`입니다. |아닙니다. |
| disableMetricsCollection | Data Factory 복사 성능 최적화 및 권장 사항에 대한 Azure SQL Database Dtu와 같은 메트릭을 수집 합니다. 이 동작에 관심이 있는 경우 `true` 지정 하 여 해제 합니다. | 아니요(기본값: `false`) |

**예제 1: 데이터 추가**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**예 2: 복사 하는 동안 저장 프로시저 호출**

자세한 내용은 [SQL 싱크에서 저장 프로시저 호출](#invoke-a-stored-procedure-from-a-sql-sink)을 참조하세요.

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL Database로 데이터를 로드 하는 모범 사례

Azure SQL Database에 데이터를 복사 하는 경우 다른 쓰기 동작이 필요할 수 있습니다.

- [추가](#append-data): 내 원본 데이터에는 새 레코드만 있습니다.
- [Upsert](#upsert-data): 내 원본 데이터에는 삽입과 업데이트가 모두 포함 됩니다.
- [덮어쓰기](#overwrite-the-entire-table): 매번 전체 차원 테이블을 다시 로드 합니다.
- [사용자 지정 논리를 사용 하 여 작성](#write-data-with-custom-logic): 대상 테이블에 최종 삽입 하기 전에 추가 처리가 필요 합니다.

Azure Data Factory 및 모범 사례에서 구성 하는 방법에 대한 해당 섹션을 참조 하세요.

### <a name="append-data"></a>데이터 추가

이 Azure SQL Database 싱크 커넥터의 기본 동작은 데이터를 추가 하는 것입니다. Azure Data Factory는 대량 삽입을 수행 하 여 테이블에 효율적으로 씁니다. 복사 작업에 따라 원본 및 싱크를 구성 할 수 있습니다.

### <a name="upsert-data"></a>데이터 Upsert

**옵션 1:** 복사할 데이터 양이 많은 경우 다음 방법을 사용 하 여 upsert를 수행 합니다. 

- 먼저 [데이터베이스 범위 임시 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) 을 사용 하 여 복사 작업을 통해 모든 레코드를 대량 로드 합니다. 데이터베이스 범위 임시 테이블에 대한 작업은 기록 되지 않으므로 몇 초만에 수백만 개의 레코드를 로드할 수 있습니다.
- Azure Data Factory에서 저장 프로시저 작업을 실행 하 여 [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) 또는 INSERT/UPDATE 문을 적용 합니다. 임시 테이블을 원본으로 사용 하 여 모든 업데이트나 삽입을 단일 트랜잭션으로 수행 합니다. 이러한 방식으로 라운드트립 및 로그 작업의 수가 줄어듭니다. 저장 프로시저 작업의 끝에서 임시 테이블은 다음 upsert 주기에 대해 준비 되도록 잘릴 수 있습니다.

예를 들어 Azure Data Factory에서 **저장 프로시저 작업과**연결 된 **복사 작업** 을 사용 하 여 파이프라인을 만들 수 있습니다. 이전에는 데이터 집합의 테이블 이름으로 원본 저장소에서 Azure SQL Database 임시 테이블로 데이터를 복사 합니다 (예: **# #UpsertTempTable**). 그런 다음 후자는 저장 프로시저를 호출 하 여 임시 테이블의 원본 데이터를 대상 테이블에 병합 하 고 임시 테이블을 정리 합니다.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

데이터베이스에서 이전 저장 프로시저 작업에서 가리키는 다음 예와 같이 병합 논리를 사용 하 여 저장 프로시저를 정의 합니다. 대상이 **프로필 id**, **State**및 **Category**라는 세 개의 열이 있는 **마케팅** 테이블인 것으로 가정 합니다. **프로필 id** 열을 기반으로 upsert를 수행 합니다.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**옵션 2:** 또한 [복사 작업 내에서 저장 프로시저를 호출](#invoke-a-stored-procedure-from-a-sql-sink)하도록 선택할 수 있습니다. 이 방법은 대량 삽입을 복사 작업의 기본 방법으로 사용 하는 대신 원본 테이블의 각 행을 실행 합니다 .이는 대규모 upsert에 적합 하지 않습니다.

### <a name="overwrite-the-entire-table"></a>전체 테이블 덮어쓰기

복사 작업 싱크에서 **Precopyscript** 속성을 구성할 수 있습니다. 이 경우를 실행 하는 각 복사 작업에 대해 Azure Data Factory는 스크립트를 먼저 실행 합니다. 그런 다음 복사를 실행 하 여 데이터를 삽입 합니다. 예를 들어 전체 테이블을 최신 데이터로 덮어쓰려면 원본에서 새 데이터를 대량 로드 하기 전에 먼저 모든 레코드를 삭제 하는 스크립트를 지정 합니다.

### <a name="write-data-with-custom-logic"></a>사용자 지정 논리를 사용 하 여 데이터 작성

사용자 지정 논리를 사용 하 여 데이터를 작성 하는 단계는 [Upsert data](#upsert-data) 섹션에 설명 된 것과 비슷합니다. 대상 테이블에 원본 데이터를 최종 삽입 하기 전에 추가 처리를 적용 해야 하는 경우 대규모 확장의 경우 다음 두 가지 중 하나를 수행할 수 있습니다.

- 데이터베이스 범위 임시 테이블에 로드 한 다음 저장 프로시저를 호출 합니다. 
- 복사 하는 동안 저장 프로시저를 호출 합니다.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> SQL 싱크에서 저장 프로시저 호출

Azure SQL Database에 데이터를 복사 하는 경우 추가 매개 변수를 사용 하 여 사용자 지정 저장 프로시저를 구성 하 고 호출할 수도 있습니다. 저장 프로시저 기능은 [테이블 반환 매개 변수](https://msdn.microsoft.com/library/bb675163.aspx)을 활용합니다.

> [!TIP]
> 저장 프로시저를 호출 하면 대량 작업을 사용 하는 대신 행을 기준으로 데이터 행을 처리 하므로 대규모 복사본에는 권장 되지 않습니다. [Azure SQL Database에 데이터를 로드 하는 모범 사례](#best-practice-for-loading-data-into-azure-sql-database)를 자세히 알아보세요.

기본 제공 복사 메커니즘이 용도에 적합하지 않은 경우, 저장 프로시저를 사용할 수 있습니다. 원본 데이터를 대상 테이블에 마지막으로 삽입 하기 전에 추가 처리를 적용 하려는 경우를 예로 들 수 있습니다. 몇 가지 추가 처리 예는 열을 병합 하 고, 추가 값을 조회 하 고, 둘 이상의 테이블에 삽입 하려고 하는 경우입니다.

다음 샘플에서는 저장 프로시저를 사용하여 Azure SQL Database의 테이블에 upsert(업데이트/삽입)를 수행하는 방법을 보여 줍니다. 입력 데이터와 싱크 **마케팅** 테이블에 각각 **프로필 id**, **State**및 **Category**라는 세 개의 열이 있다고 가정 합니다. **프로필 id** 열을 기반으로 upsert를 수행 하 고 "ProductA" 이라는 특정 범주에 대해서만 적용 합니다.

1. 데이터베이스에서 **sqlwritertabletype과**와 동일한 이름을 사용 하 여 테이블 형식을 정의 합니다. 테이블 형식의 스키마는 입력 데이터에서 반환된 스키마와 같아야 합니다.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. 데이터베이스에서 **sqlWriterStoredProcedureName**와 동일한 이름을 사용 하 여 저장 프로시저를 정의 합니다. 지정된 원본의 입력 데이터를 처리하고 출력 테이블에 병합합니다. 저장 프로시저에서 테이블 형식의 매개 변수 이름은 데이터 집합에 정의 된 **tableName** 과 동일 합니다.

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

3. Azure Data Factory에서 복사 활동의 **SQL 싱크** 섹션을 다음과 같이 정의 합니다.

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환 하는 경우 Azure SQL Database에서 테이블을 읽고 쓸 수 있습니다. 자세한 내용은 데이터 흐름 매핑에서 [원본 변환](data-flow-source.md) 및 [싱크 변환을](data-flow-sink.md) 참조 하세요.

### <a name="source-transformation"></a>원본 변환

Azure SQL Database 관련 된 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다. 

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

Azure SQL Database 관련 된 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 방법:** 데이터베이스 대상에서 허용 되는 작업을 결정 합니다. 기본값은 삽입만 허용 하는 것입니다. 행을 업데이트, upsert 또는 삭제 하려면 해당 작업에 대한 행의 태그를 변경 하는 행을 변경 해야 합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정 하기 위해 키 열을 설정 해야 합니다.

![키 열](media/data-flow/keycolumn.png "키 열")

여기에서 키로 선택한 열 이름은 ADF에서 후속 업데이트, upsert, delete의 일부로 사용 됩니다. 따라서 싱크 매핑에 있는 열을 선택 해야 합니다. 이 키 열에 값을 쓰지 않으려면 "키 열 쓰기 생략"을 클릭 합니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지를 결정 합니다.
* 없음: 테이블에 대한 작업이 수행 되지 않습니다.
* 다시 만들기: 테이블이 삭제 되 고 다시 생성 됩니다. 동적으로 새 테이블을 만드는 경우 필요 합니다.
* Truncate: 대상 테이블의 모든 행이 제거 됩니다.

**일괄 처리 크기**: 각 버킷에 작성 되는 행 수를 제어 합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상 되지만 데이터를 캐시할 때 메모리 예외가 발생할 위험이 있습니다.

**사전 및 사후 sql 스크립트**: 데이터를 싱크 데이터베이스에 기록 하기 전 (전처리) 및 이후 (사후 처리) 데이터를 실행 하는 여러 줄의 sql 스크립트를 입력 합니다.

![사전 및 사후 SQL 처리 스크립트](media/data-flow/prepost1.png "SQL 처리 스크립트")

## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 데이터 형식 매핑

데이터를 또는에서 Azure SQL Database로 복사 하는 경우 Azure SQL Database 데이터 형식에서 중간 데이터 형식을 Azure Data Factory 하는 다음 매핑이 사용 됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Azure SQL Database 데이터 형식 | Azure Data Factory 중간 데이터 형식 |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |부울 |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM 특성(varbinary(max)) |Byte[] |
| Float |Double |
| 이미지 |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |단일 |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |개체 |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |GUID |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> 10진수 중간 형식으로 매핑되는 데이터 형식의 경우 Azure Data Factory는 현재 최대 28자리의 데이터를 지원합니다. 전체 자릿수가 28 보다 큰 데이터를 사용 하는 경우 SQL 쿼리에서 문자열로 변환 하는 것이 좋습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md) 을 확인 하세요. 

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조 하세요.
