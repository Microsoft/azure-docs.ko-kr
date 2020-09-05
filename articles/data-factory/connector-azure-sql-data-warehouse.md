---
title: Azure Synapse Analytics에서 데이터 복사 및 변환
description: Data Factory를 사용하여 Azure Synapse Analytics로/에서 데이터를 복사하고 Azure Synapse Analytics에서 데이터를 변환하는 방법을 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: cd14a183ae1434af83c96b7f8d6575186412b534
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051222"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Synapse Analytics(이전의 Azure SQL Data Warehouse)에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [현재 버전](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Synapse Analytics 간에 데이터를 복사하고 Data Flow를 사용하여 Azure Data Lake Storage Gen2에서 데이터를 변환하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Synapse Analytics 커넥터는 다음과 같은 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md) 테이블을 사용하여 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)

복사 작업의 경우 이 Azure Synapse Analytics 커넥터는 다음과 같은 기능을 지원합니다.

- 서비스 주체 또는 Azure 리소스에 대한 관리 ID를 통해 SQL 인증 및 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
- 원본으로 SQL 쿼리 또는 저장 프로시저를 사용하여 데이터를 검색합니다. Azure Synapse Analytics 원본에서 병렬 복사를 선택할 수도 있습니다. 자세한 내용은 [Synapse analytics에서 병렬 복사](#parallel-copy-from-synapse-analytics) 섹션을 참조 하세요.
- 싱크로 [PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse)나 [COPY 문](#use-copy-statement)(미리 보기) 또는 대량 삽입을 사용하여 데이터를 로드합니다. 더 나은 복사 성능을 위해 PolyBase 또는 COPY 문(미리 보기)을 권장합니다. 원본 스키마에 따라 존재 하지 않는 경우 커넥터는 대상 테이블을 자동으로 만들도록 지원 합니다.

> [!IMPORTANT]
> Azure Data Factory Integration Runtime를 사용 하 여 데이터를 복사 하는 경우 Azure 서비스에서 [논리 SQL server](../azure-sql/database/logical-servers.md)에 액세스할 수 있도록 [서버 수준 방화벽 규칙](../azure-sql/database/firewall-configure.md) 을 구성 합니다.
> 자체 호스팅 통합 런타임을 사용 하 여 데이터를 복사 하는 경우 적절 한 IP 범위를 허용 하도록 방화벽을 구성 합니다. 이 범위에는 Azure Synapse Analytics에 연결하는 데 사용되는 머신 IP가 포함됩니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> 최상의 성능을 얻으려면 PolyBase를 사용하여 Azure Synapse Analytics에 데이터를 로드합니다. 자세한 내용은 [PolyBase를 사용하여 Azure Synapse Analytics에 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요. 사용 사례가 있는 연습은 [Azure Data Factory를 통해 Azure Synapse Analytics에 15분 이내 1TB 로드](load-azure-sql-data-warehouse.md)를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Synapse Analytics 커넥터와 관련된 Data Factory 엔터티를 정의하는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Synapse Analytics 연결된 서비스에 대해 지원되는 속성은 다음과 같습니다.

| 속성            | Description                                                  | 필수                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | type 속성은 **AzureSqlDW**로 설정해야 합니다.             | 예                                                          |
| connectionString    | **connectionString** 속성에 대해 Azure Synapse Analytics 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. 암호/서비스 주체 키를 Azure Key Vault에 넣고, SQL 인증인 경우 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 표 아래의 JSON 예제 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예                                                          |
| servicePrincipalId  | 애플리케이션의 클라이언트 ID를 지정합니다.                         | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| tenant              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 서비스 주체와 함께 Azure AD 인증을 사용하는 경우 예 |
| azureCloudType | 서비스 주체 인증의 경우 Azure AD 응용 프로그램이 등록 된 Azure 클라우드 환경의 유형을 지정 합니다. <br/> 허용 되는 값은 **Azurepublic**, **azurepublic**, **azureus정부**및 **AzureGermany**입니다. 기본적으로 데이터 팩터리의 클라우드 환경이 사용 됩니다. | 예 |
| connectVia          | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 예                                                           |

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

1. Azure Portal에서 **[Azure Active Directory 애플리케이션을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** . 애플리케이션 이름 및 연결된 서비스를 정의하는 다음 값을 적어 둡니다.

   - 애플리케이션 UI
   - 애플리케이션 키
   - 테넌트 ID

2. 아직 수행 하지 않은 경우 Azure Portal에서 서버에 대 한 **[Azure Active Directory 관리자를 프로 비전](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

3. 서비스 주체에 대한 **[포함된 데이터베이스 사용자를 만듭니다](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. 일반적으로 SQL 사용자나 기타 사용자에 대해 수행하듯이 **서비스 주체에 필요한 권한을 부여**합니다. 다음 코드를 실행하거나 [여기](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)서 추가 옵션을 참조합니다. PolyBase를 사용하여 데이터를 로드하려는 경우 [필요한 데이터베이스 권한](#required-database-permission)을 알아보세요.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Azure Data Factory에서 **Azure Synapse Analytics 연결된 서비스를 구성**합니다.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Azure 리소스 인증용 관리 ID

데이터 팩터리는 특정 팩터리를 나타내는 [Azure 리소스에 대한 관리 ID](data-factory-service-identity.md)와 연결할 수 있습니다. Azure Synapse Analytics 인증에 이 관리 ID를 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터 웨어하우스의 데이터에 액세스하고 복사할 수 있습니다.

관리 ID 인증을 사용하려면 다음 단계를 수행합니다.

1. 아직 수행 하지 않은 경우 Azure Portal에서 서버에 대 한 **[Azure Active Directory 관리자를 프로 비전](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** 합니다. Azure AD 관리자는 Azure AD 사용자 또는 Azure AD 그룹이 될 수 있습니다. 관리 ID를 가진 그룹에 관리자 역할을 부여하는 경우 3단계 및 4단계를 건너뛰세요. 관리자는 데이터베이스에 대한 전체 액세스 권한을 가집니다.

2. Data Factory 관리 ID용 **[포함된 데이터베이스 사용자를 만듭니다](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** . 최소한 ALTER ANY USER 권한이 있는 Azure AD ID를 사용하여 SSMS 등의 도구를 통해 데이터를 복사하려는 데이터 웨어하우스에 연결합니다. 다음 T-SQL을 실행합니다.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL 사용자 및 다른 사용자에 대해 일반적으로 수행하는 것처럼 **Data Factory 관리 ID에 필요한 권한을 부여**합니다. 다음 코드를 실행하거나 [여기](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017)서 추가 옵션을 참조합니다. PolyBase를 사용하여 데이터를 로드하려는 경우 [필요한 데이터베이스 권한](#required-database-permission)을 알아보세요.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. Azure Data Factory에서 **Azure Synapse Analytics 연결된 서비스를 구성**합니다.

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

Azure Synapse Analytics 데이터 세트에 대해 지원되는 속성은 다음과 같습니다.

| 속성  | Description                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 세트의 **type** 속성을 **AzureSqlDWTable**로 설정해야 합니다. | 예                         |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원됩니다. 새 워크로드의 경우 `schema` 및 `table`을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

### <a name="dataset-properties-example"></a>데이터 세트 속성 예제

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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Synapse Analytics 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-synapse-analytics-as-the-source"></a>원본으로 Azure Synapse Analytics

>[!TIP]
>데이터 분할을 사용 하 여 Azure Synapse Analytics에서 데이터를 효율적으로 로드 하려면 [Synapse Analytics의 병렬 복사](#parallel-copy-from-synapse-analytics)에서 자세히 알아보세요.

Azure Synapse Analytics에서 데이터를 복사하려면 복사 작업 원본의 **type** 속성을 **SqlDWSource**로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성                     | Description                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 **type** 속성을 **SqlDWSource**로 설정해야 합니다. | 예      |
| SqlReaderQuery               | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `select * from MyTable`. | 예       |
| sqlReaderStoredProcedureName | 원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. | 예       |
| storedProcedureParameters    | 저장 프로시저에 대한 매개 변수입니다.<br/>허용되는 값은 이름 또는 값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. | 예       |
| isolationLevel | SQL 원본에 대한 트랜잭션 잠금 동작을 지정합니다. 허용 되는 값은 **ReadCommitted**, **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**입니다. 지정 하지 않으면 데이터베이스의 기본 격리 수준이 사용 됩니다. 자세한 내용은 [이 문서](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel)를 참조하세요. | 예 |
| partitionOptions | Azure Synapse Analytics에서 데이터를 로드 하는 데 사용 되는 데이터 분할 옵션을 지정 합니다. <br>허용 되는 값은 **None** (기본값), **PhysicalPartitionsOfTable**및 **dynamicrange**입니다.<br>파티션 옵션을 사용 하도록 설정 하는 경우 (즉,이 아님 `None` ) Azure Synapse Analytics에서 데이터를 동시에 로드 하는 병렬 처리 수준은 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 복사 작업의 설정에 의해 제어 됩니다. | 예 |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정합니다. <br>Partition 옵션을 사용할 수 없는 경우에 적용 `None` 됩니다. | 예 |
| ***에서 `partitionSettings` 다음을 수행 합니다.*** | | |
| partitionColumnName | 병렬 복사를 위해 범위 분할에서 사용 되는 **정수 또는 날짜/시간 형식으로** 원본 열의 이름을 지정 합니다. 지정 하지 않으면 테이블의 인덱스 또는 기본 키가 자동으로 검색 되어 파티션 열로 사용 됩니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우  `?AdfDynamicRangePartitionCondition ` WHERE 절에 후크 합니다. 예를 들어 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-synapse-analytics) 섹션을 참조 하세요. | 예 |
| partitionUpperBound | 파티션 범위 분할에 대 한 파티션 열의 최대값입니다. 이 값은 테이블의 행을 필터링 하는 것이 아니라 파티션 stride를 결정 하는 데 사용 됩니다. 테이블이 나 쿼리 결과의 모든 행이 분할 되 고 복사 됩니다. 지정 하지 않으면 복사 작업에서 값을 자동으로 검색 합니다.  <br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예를 들어 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-synapse-analytics) 섹션을 참조 하세요. | 예 |
| partitionLowerBound | 파티션 범위 분할에 대 한 파티션 열의 최소값입니다. 이 값은 테이블의 행을 필터링 하는 것이 아니라 파티션 stride를 결정 하는 데 사용 됩니다. 테이블이 나 쿼리 결과의 모든 행이 분할 되 고 복사 됩니다. 지정 하지 않으면 복사 작업에서 값을 자동으로 검색 합니다.<br>파티션 옵션이 `DynamicRange`인 경우에 적용됩니다. 예를 들어 [SQL 데이터베이스에서 병렬 복사](#parallel-copy-from-synapse-analytics) 섹션을 참조 하세요. | 예 |

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

**샘플 저장 프로시저:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> 싱크로 Azure Synapse Analytics

Azure Data Factory는 SQL Data Warehouse에 데이터를 로드하는 세 가지 방법을 지원합니다.

![SQL DW 싱크 복사 옵션](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [PolyBase 사용](#use-polybase-to-load-data-into-azure-sql-data-warehouse)
- [COPY 문 사용(미리 보기)](#use-copy-statement)
- 대량 삽입 사용

데이터를 로드하는 가장 빠르고 확장성 있는 방법은 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) 또는 [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)(미리 보기)를 통하는 것입니다.

Azure SQL Data Warehouse에 데이터를 복사하려면 복사 작업의 싱크 형식을 **SqlDWSink**로 설정합니다. 복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 복사 작업 싱크의 **type** 속성은 **SqlDWSink**로 설정해야 합니다. | 예                                           |
| allowPolyBase     | PolyBase를 사용하여 데이터를 SQL Data Warehouse에 로드할지 여부를 나타냅니다. `allowCopyCommand` 및 `allowPolyBase` 모두 true일 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [PolyBase를 사용하여 Azure SQL Data Warehouse로 데이터 로드](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 섹션을 참조하세요.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다. | 아니요.<br/>PolyBase를 사용하는 경우 적용합니다.     |
| polyBaseSettings  | `allowPolybase` 속성이 **true**로 설정된 경우 지정될 수 있는 속성의 그룹입니다. | 아니요.<br/>PolyBase를 사용하는 경우 적용합니다. |
| allowCopyCommand | [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)(미리 보기)를 사용하여 데이터를 SQL Data Warehouse에 로드할지 여부를 나타냅니다. `allowCopyCommand` 및 `allowPolyBase` 모두 true일 수 없습니다. <br/><br/>제약 조건 및 세부 정보는 [COPY 문을 사용하여 Azure SQL Data Warehouse로 데이터 로드](#use-copy-statement) 섹션을 참조하세요.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다. | 아니요.<br>COPY를 사용하는 경우 적용합니다. |
| copyCommandSettings | `allowCopyCommand` 속성이 TRUE로 설정된 경우 지정될 수 있는 속성의 그룹입니다. | 아니요.<br/>COPY를 사용하는 경우 적용합니다. |
| writeBatchSize    | **일괄 처리당** SQL 테이블에 삽입할 행 수입니다.<br/><br/>허용되는 값은 **정수**(행 수)입니다. 기본적으로 Data Factory는 행 크기에 따라 적절한 일괄 처리 크기를 동적으로 결정합니다. | 아니요.<br/>대량 삽입을 사용하는 경우 적용합니다.     |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다.<br/><br/>허용되는 값은 **시간 범위**입니다. 예제: "00:30:00"(30분). | 아니요.<br/>대량 삽입을 사용하는 경우 적용합니다.        |
| preCopyScript     | 각 실행 시 Azure SQL Data Warehouse에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예                                            |
| tableOption | 원본 스키마에 따라 존재 하지 않는 경우 [싱크 테이블을 자동으로 만들지](copy-activity-overview.md#auto-create-sink-tables) 여부를 지정 합니다. 허용되는 값은 `none`(기본값) 또는 `autoCreate`입니다. |예 |
| disableMetricsCollection | Data Factory는 복사 성능 최적화 및 권장 사항에 대한 SQL Data Warehouse DWU와 같은 메트릭을 수집합니다. 이 동작에 관심이 있는 경우 `true`를 지정하여 해제합니다. | 아니요(기본값: `false`) |

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

## <a name="parallel-copy-from-synapse-analytics"></a>Synapse Analytics의 병렬 복사

복사 작업의 Azure Synapse Analytics 커넥터는 데이터를 병렬로 복사 하기 위한 기본 제공 데이터 분할을 제공 합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-sql-server/connector-sql-partition-options.png)

분할 된 복사를 사용 하도록 설정 하면 복사 작업은 Azure Synapse Analytics 원본에 대해 병렬 쿼리를 실행 하 여 파티션으로 데이터를 로드 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 설정에 의해 제어됩니다. 예를 들어를 4로 설정 하는 경우 `parallelCopies` Data Factory는 지정 된 파티션 옵션과 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며, 각 쿼리는 Azure Synapse Analytics에서 데이터의 일부를 검색 합니다.

특히 Azure Synapse Analytics에서 대량의 데이터를 로드 하는 경우 데이터 분할으로 병렬 복사를 사용 하도록 설정 하는 것이 좋습니다. 다양한 시나리오에 대해 권장되는 구성은 다음과 같습니다. 파일 기반 데이터 저장소로 데이터를 복사할 때 폴더에 여러 파일 (폴더 이름만 지정)로 쓰는 것이 좋습니다 .이 경우에는 단일 파일에 쓰는 것 보다 성능이 좋습니다.

| 시나리오                                                     | 제안된 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션이 있는 초대형 테이블에서 전체 로드        | **파티션 옵션**: 테이블의 실제 파티션입니다. <br><br/>실행 중에는 Data Factory에서 실제 파티션을 자동으로 검색 하 고 파티션으로 데이터를 복사 합니다. |
| 데이터 분할을 위한 정수 또는 datetime 열을 포함 하는 동시에 실제 파티션이 없는 많은 테이블에서 전체 로드 | **파티션 옵션**: 동적 범위 파티션입니다.<br>**파티션 열** (선택 사항): 데이터를 분할 하는 데 사용 되는 열을 지정 합니다. 지정 하지 않으면 인덱스 또는 기본 키 열이 사용 됩니다.<br/>**파티션 상한** 및 **파티션** 하 한 (선택 사항): 파티션 스트라이드를 확인할 지 여부를 지정 합니다. 테이블의 행을 필터링 하는 것이 아니라 테이블의 모든 행이 분할 및 복사 됩니다. 지정 하지 않으면 복사 작업에서 값을 자동으로 검색 합니다.<br><br>예를 들어 파티션 열 "ID"의 값이 1에서 100 사이의 값을 가지 며, 상한 값을 20으로 설정 하 고 상한을 80로 설정 하 고, Data Factory에서 4 개의 파티션 (범위 <= 20, [21, 50], [51, 80] 및 >= 81)을 통해 데이터를 검색 합니다. |
| 실제 파티션이 없는 사용자 지정 쿼리를 사용 하 여 많은 양의 데이터를 로드 하는 동시에 데이터 분할을 위한 정수 또는 날짜/시간 열을 사용 합니다. | **파티션 옵션**: 동적 범위 파티션입니다.<br>**쿼리**:`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할하는 데 사용되는 열을 지정합니다.<br>**파티션 상한** 및 **파티션** 하 한 (선택 사항): 파티션 스트라이드를 확인할 지 여부를 지정 합니다. 테이블에서 행을 필터링 하는 것이 아니라 쿼리 결과의 모든 행이 분할 되 고 복사 됩니다. 지정 하지 않으면 복사 작업에서 값을 자동으로 검색 합니다.<br><br>실행 중 Data Factory는 `?AdfRangePartitionColumnName` 각 파티션의 실제 열 이름 및 값 범위로 바뀌고 Azure Synapse Analytics로 전송 됩니다. <br>예를 들어 파티션 열 "ID"의 값이 1에서 100 사이의 값을 가지 며, 상한 값을 20으로 설정 하 고 상한을 80로 설정 하 고, Data Factory에서 4 개의 파티션 (범위 <= 20, [21, 50], [51, 80] 및 >= 81)을 통해 데이터를 검색 합니다. |

파티션 옵션을 사용 하 여 데이터를 로드 하는 모범 사례:

1. 데이터 기울이기를 방지 하려면 기본 키 또는 고유 키와 같은 고유한 열을 분할 열로 선택 합니다. 
2. 테이블에 기본 제공 파티션이 있으면 파티션 옵션 "테이블의 실제 파티션"을 사용 하 여 성능을 향상 시킬 수 있습니다.
3. Azure Integration Runtime를 사용 하 여 데이터를 복사 하는 경우 더 많은 컴퓨팅 리소스를 활용 하기 위해 더 큰 "[DIU (데이터 통합 단위](copy-activity-performance-features.md#data-integration-units))" (>4)를 설정할 수 있습니다. 해당 하는 시나리오를 확인 합니다.
4. "[복사 병렬 처리 수준](copy-activity-performance-features.md#parallel-copy)"은 파티션 번호를 제어 하 고,이 숫자를 너무 크게 설정 하면 성능이 저하 됩니다. (diu 또는 자체 호스팅 IR 노드의 수) * (2-4)로이 숫자를 설정 하는 것이 좋습니다.
5. Azure Synapse Analytics는 한 번에 최대 32 개의 쿼리를 실행할 수 있으므로 "복사 병렬 처리 수준"을 너무 크게 설정 하면 Synapse 제한 문제가 발생할 수 있습니다.

**예: 실제 파티션이 있는 초대형 테이블에서 전체 로드**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "SqlDWSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase를 사용하여 Azure SQL Data Warehouse에 데이터 로드

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)를 사용하면 높은 처리량으로 대량 데이터를 Azure Synapse Analytics에 효율적으로 로드할 수 있습니다. 기본 BULKINSERT 메커니즘 대신 PolyBase를 사용하면 처리량이 훨씬 증가합니다. 사용 사례가 있는 연습은 [Azure Synapse Analytics에 1TB 로드](v1/data-factory-load-sql-data-warehouse.md)를 참조하세요.

- 원본 데이터가 **Azure Blob, Azure Data Lake Storage Gen1 또는 Azure Data Lake Storage Gen2**에 있고 **형식이 PolyBase 호환**이면 SQL Data Warehouse가 원본에서 데이터를 끌어오도록 복사 작업을 사용하여 PolyBase를 직접 호출할 수 있습니다. 자세한 내용은 **[PolyBase를 사용하여 직접 복사](#direct-copy-by-using-polybase)** 를 참조하세요.
- 원본 데이터 저장소와 형식이 PolyBase에서 원래 지원되지 않는 경우, 대신 **[PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)** 기능을 사용합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. 데이터를 PolyBase 호환 형식으로 자동으로 변환 하 고, Azure Blob storage에 데이터를 저장 한 다음, PolyBase를 호출 하 여 데이터를 SQL Data Warehouse으로 로드 합니다.

> [!TIP]
> [PolyBase 사용에 대한 모범 사례](#best-practices-for-using-polybase)에 대해 자세히 알아봅니다.

복사 작업의 `polyBaseSettings`에서 다음 PolyBase 설정이 지원됩니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | 쿼리가 실패하기 전에 거부될 수 있는 행의 수 또는 백분율을 지정합니다.<br/><br/>[CREATE EXTERNAL TABLE(Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx)의 인수 섹션에서 PolyBase의 거부 옵션에 대해 자세히 알아봅니다. <br/><br/>허용되는 값은 0(기본값), 1, 2 등입니다. | 예                                            |
| rejectType        | **rejectValue** 옵션이 리터럴 값인지 또는 백분율인지를 지정합니다.<br/><br/>허용되는 값은 **Value**(기본값) 및 **Percentage**입니다. | 예                                            |
| rejectSampleValue | PolyBase가 거부된 행의 백분율을 다시 계산하기 전에 검색할 행 수를 결정합니다.<br/><br/>허용되는 값은 1, 2 등입니다. | **rejectType**이 **percentage**인 경우 예 |
| useTypeDefault    | PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.<br/><br/>[외부 파일 서식 만들기(Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx)를 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다.<br/><br/>허용되는 값은 **True** 및 **False**(기본값)입니다.<br><br> | 예                                            |

### <a name="direct-copy-by-using-polybase"></a>PolyBase를 사용한 직접 복사

SQL Data Warehouse PolyBase는 Azure Blob, Azure Data Lake Storage Gen1 및 Azure Data Lake Storage Gen2를 직접 지원합니다. 원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우, PolyBase를 사용하여 원본 데이터 저장소에서 Azure Synapse Analytics로 직접 복사합니다. 조건을 충족하지 않는 경우, [PolyBase를 사용한 준비된 복사](#staged-copy-by-using-polybase)를 사용합니다.

> [!TIP]
> SQL Data Warehouse로 데이터를 효율적으로 복사하려면 [Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)(SQL Data Warehouse와 함께 Data Lake Store를 사용할 경우, Azure Data Factory에서 데이터의 인사이트를 쉽고 편리하게 얻을 수 있음)에서 자세한 내용을 참조하세요.

조건을 충족하지 않는 경우, Azure Data Factory는 설정을 확인한 후 데이터 이동을 위해 BULKINSERT 메커니즘으로 자동으로 대체됩니다.

1. **원본 연결된 서비스**는 다음과 같은 유형 및 인증 방법을 사용합니다.

    | 지원되는 원본 데이터 저장소 형식                             | 지원되는 원본 인증 유형                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | 계정 키 인증, 관리 ID 인증 |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | 서비스 주체 인증                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | 계정 키 인증, 관리 ID 인증 |

    >[!IMPORTANT]
    >Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)을 참조하세요. [Azure Blob - 관리 ID 인증](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2 - 관리 ID 인증](connector-azure-data-lake-storage.md#managed-identity) 섹션에서 각기 Data Factory에 필요한 구성을 알아봅니다.

2. **원본 데이터 형식**은 다음 구성과 함께 **Parquet**, **ORC** 또는 **구분된 텍스트**입니다.

   1. 폴더 경로는 와일드 카드 필터를 포함하지 않습니다.
   2. 파일 이름이 비어 있거나 단일 파일을 가리킵니다. 복사 작업에서 와일드 카드 파일 이름을 지정하는 경우 `*` 또는 `*.*`만 가능합니다.
   3. `rowDelimiter`는 **기본값**, **\n**, **\r\n** 또는 **\r**입니다.
   4. `nullValue`는 기본값으로 남아 있거나 **빈 문자열**("")로 설정되며, `treatEmptyAsNull`는 기본값으로 남아 있거나 true로 설정됩니다.
   5. `encodingName`는 기본값으로 남아 있거나 **utf-8**로 설정됩니다.
   6. `quoteChar`, `escapeChar` 및 `skipLineCount`는 지정되지 않습니다. PolyBase 지원은 ADF에서 `firstRowAsHeader`로 구성될 수 있는 헤더 행을 건너뜁니다.
   7. `compression`은 **no compression**, **GZip** 또는 **Deflate**일 수 있습니다.

3. 원본이 폴더인 경우 복사 작업의 `recursive`을 true로 설정해야 합니다.

4. `wildcardFolderPath` ,,,, `wildcardFilename` `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` 및 `additionalColumns` 가 지정 되지 않은 경우

>[!NOTE]
>원본이 폴더인 경우 PolyBase는 폴더와 모든 하위 폴더에서 파일을 검색하며, [여기 - LOCATION 인수](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)에 설명된 대로 파일 이름이 밑줄(_) 또는 마침표(.)로 시작하는 파일에서 데이터를 검색하지 않습니다.

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

원본 데이터가 PolyBase와 기본적으로 호환되지 않는 경우 중간 준비 Azure Blob Storage 인스턴스(Azure Premium Storage일 수 없음)를 통해 데이터 복사를 사용하도록 설정합니다. 이 경우, Azure Data Factory는 PolyBase의 데이터 형식 요구 사항을 충족하도록 데이터를 자동으로 변환합니다. 그런 다음 PolyBase를 호출하여 데이터를 SQL Data Warehouse에 로드합니다. 마지막으로, Blob Storage에서 임시 데이터를 정리합니다. 준비 Azure Blob Storage 인스턴스를 통해 데이터를 복사하는 방법에 대한 자세한 내용은 [준비된 복사](copy-activity-performance-features.md#staged-copy)를 참조하세요.

이 기능을 사용하려면 중간 Blob Storage가 있는 Azure Storage 계정을 참조하는 [Azure Blob Storage 연결된 서비스](connector-azure-blob-storage.md#linked-service-properties)를 만듭니다. 그런 다음, 복사 작업의 `enableStaging` 및 `stagingSettings` 속성을 다음 코드에 표시된 대로 지정합니다.

>[!IMPORTANT]
>준비 Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)을 참조하세요. [Azure Blob - 관리 ID 인증](connector-azure-blob-storage.md#managed-identity)에서 Data Factory에 필요한 구성을 알아봅니다.

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

다음 섹션에서는 [Azure Synapse Analytics에 대한 모범 사례](../synapse-analytics/sql/best-practices-sql-pool.md)에 설명된 모범 사례 이외의 모범 사례를 제공합니다.

#### <a name="required-database-permission"></a>필수 데이터베이스 권한

PolyBase를 사용하려면 SQL Data Warehouse에 데이터를 로드하는 사용자에게 대상 데이터베이스에 대한 [“CONTROL” 권한](https://msdn.microsoft.com/library/ms191291.aspx)이 있어야 합니다. 한 가지 방법은 해당 사용자를 **db_owner** 역할의 구성원으로 추가하는 것입니다. [SQL Data Warehouse 개요](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)에서 작업 방법을 알아보세요.

#### <a name="row-size-and-data-type-limits"></a>행 크기 및 데이터 형식 한도

PolyBase는 1MB보다 작은 행으로 제한됩니다. VARCHR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)에 로드하는 데 사용할 수 없습니다. 자세한 내용은 [SQL Data Warehouse 서비스 용량 한도](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)를 참조하세요.

원본 데이터에 1MB보다 큰 행이 있는 경우, 원본 테이블을 여러 개의 작은 테이블로 수직 분할하는 것이 좋습니다. 각 행의 최대 크기가 한도를 초과하지 않는지 확인합니다. 더 작은 테이블은 PolyBase를 사용하여 로드하고 Azure Synapse Analytics에 병합할 수 있습니다.

또는 이러한 광범위한 열이 포함된 데이터의 경우 "PolyBase 허용" 설정을 해제하여 비 PolyBase를 사용하고 ADF를 사용하여 데이터를 로드할 수 있습니다.

#### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse 리소스 클래스

가능한 최고 수준까지 처리량을 높이려면 PolyBase를 통해 SQL Data Warehouse에 데이터를 로드하는 사용자에게 더 큰 리소스 클래스를 할당합니다.

#### <a name="polybase-troubleshooting"></a>PolyBase 문제 해결

**Decimal 열에 로드**

원본 데이터가 텍스트 형식이나 기타 PolyBase 호환 저장소(준비된 복사 및 PolyBase 사용)에 있고 SQL Data Warehouse Decimal 열에 로드할 빈 값을 포함하는 경우 다음 오류가 발생할 수 있습니다.

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

이 솔루션은 복사 작업 싱크 -> PolyBase 설정에서 "**형식 기본값 사용**" 옵션(false로 설정)을 선택 취소하는 것입니다. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)"는 PolyBase 네이티브 구성으로, PolyBase가 텍스트 파일에서 데이터를 검색할 경우 구분된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.

**`tableName`Azure Synapse Analytics에서**

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

NULL 값은 특별한 형태의 기본값입니다. 열이 Null을 허용하는 경우, 해당 열에 대한 Blob의 입력 데이터가 비어 있을 수 있습니다. 그러나 입력 데이터 세트에서는 누락할 수 없습니다. PolyBase는 Azure Synapse Analytics에서 누락된 값에 대해 NULL을 삽입합니다.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a> COPY 문을 사용하여 Azure SQL Data Warehouse(미리 보기)에 데이터 로드

SQL Data Warehouse [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)(미리 보기)은 **Azure Blob 및 Azure Data Lake Storage Gen2**에서 데이터 로드를 직접 지원합니다. 원본 데이터가 이 섹션에 설명된 조건을 충족하는 경우 ADF에서 COPY 문을 사용하도록 선택하여 Azure SQL Data Warehouse로 데이터를 로드할 수 있습니다. Azure Data Factory는 설정을 확인하고 조건이 충족되지 않으면 복사 작업 실행에 실패합니다.

>[!NOTE]
>현재 Data Factory는 아래에 설명된 COPY 문 호환 원본의 복사만 지원합니다.

COPY 문을 사용하면 다음 구성이 지원됩니다.

1. **원본 연결된 서비스 및 형식**은 다음과 같은 유형 및 인증 방법을 사용합니다.

    | 지원되는 원본 데이터 저장소 형식                             | 지원되는 형식           | 지원되는 원본 인증 유형                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [구분된 텍스트](format-delimited-text.md)             | 계정 키 인증, 공유 액세스 서명 인증, 서비스 주체 인증, 관리 ID 인증 |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | 계정 키 인증, 공유 액세스 서명 인증 |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | 계정 키 인증, 공유 액세스 서명 인증 |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [구분된 텍스트](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | 계정 키 인증, 서비스 주체 인증, 관리 ID 인증 |

    >[!IMPORTANT]
    >Azure Storage가 VNet 서비스 엔드포인트로 구성된 경우 관리 ID 인증을 사용해야 합니다. [Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)을 참조하세요. [Azure Blob - 관리 ID 인증](connector-azure-blob-storage.md#managed-identity) 및 [Azure Data Lake Storage Gen2 - 관리 ID 인증](connector-azure-data-lake-storage.md#managed-identity) 섹션에서 각기 Data Factory에 필요한 구성을 알아봅니다.

2. 형식 설정에는 다음이 포함됩니다.

   1. **Parquet**의 경우: `compression`는 **압축 안 함**, **Snappy** 또는 **GZip**일 수 있습니다.
   2. **ORC**의 경우: `compression`은 **압축 안 함**, **```zlib```** 또는 **Snappy**일 수 있습니다.
   3. **구분 된 텍스트**의 경우:
      1. `rowDelimiter`는 **단일 문자** 또는 " **\r\n**"으로 명시적으로 설정됩니다. 기본값은 지원되지 않습니다.
      2. `nullValue`는 기본값으로 남아 있거나 **빈 문자열**("")로 설정됩니다.
      3. `encodingName`는 기본값으로 남아 있거나 **utf-8 또는 utf-16**으로 설정됩니다.
      4. `escapeChar`는 `quoteChar`와 동일해야 하며 비어 있지 않습니다.
      5. `skipLineCount`는 기본값으로 남아 있거나 0으로 설정됩니다.
      6. `compression`는 **압축 안 함** 또는 **GZip**일 수 있습니다.

3. 원본이 폴더인 경우 복사 작업의 `recursive`를 true로 설정해야 하며 `wildcardFilename`는 `*`이어야 합니다. 

4. `wildcardFolderPath` ,,,, `wildcardFilename` `*` `modifiedDateTimeStart` `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` 및 `additionalColumns` 는 지정 되지 않습니다.

복사 작업의 `allowCopyCommand`에서 다음 COPY 문 설정이 지원됩니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | SQL DW의 각 대상 열에 대한 기본값을 지정합니다.  속성의 기본값은 데이터 웨어하우스의 기본 제약 조건 집합을 덮어쓰고 ID 열에는 기본값을 사용할 수 없습니다. | 예 |
| additionalOptions | [COPY 문](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)의 "With" 절에서 직접 SQL DW COPY 문에 전달되는 추가 옵션입니다. COPY 문 요구 사항에 맞게 조정하는 데 필요한 값을 따옴표로 묶습니다. | 예 |

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

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환하는 경우 Azure Synapse Analytics에서 테이블에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요.

### <a name="source-transformation"></a>원본 변환

Azure Synapse Analytics에 특정한 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다.

**입력** 소스를 테이블에 표시할지 (에 해당 하는) 선택 ```Select * from <table-name>``` 하거나 사용자 지정 SQL 쿼리를 입력 합니다.

**준비 사용** Synapse DW 원본을 사용 하는 프로덕션 워크 로드에서이 옵션을 사용 하는 것이 좋습니다. 파이프라인에서 Synapase 원본을 사용 하 여 데이터 흐름 활동을 실행 하면 ADF는 스테이징 위치 저장소 계정을 입력 하 라는 메시지를 표시 하 고 준비 된 데이터 로드에이를 사용 합니다. Synapse DW에서 데이터를 로드 하는 가장 빠른 메커니즘입니다.

**쿼리**: 입력 필드에서 쿼리를 선택하는 경우에는 원본에 대한 SQL 쿼리를 입력합니다. 이렇게 설정하면 데이터 세트에서 선택한 모든 테이블이 재정의됩니다. **Order By** 절은 여기서 지원되지 않지만 전체 SELECT FROM 문을 설정할 수 있습니다. 사용자 정의 테이블 함수를 사용할 수도 있습니다. **select * from udfGetData()** 는 테이블을 반환하는 SQL의 UDF입니다. 이 쿼리는 데이터 흐름에서 사용할 수 있는 원본 테이블을 생성합니다. 쿼리를 사용하는 것은 테스트 또는 조회를 위해 행을 줄이는 좋은 방법이기도 합니다.

SQL 예제: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**일괄 처리 크기**: 일괄 처리 크기를 입력하여 대량 데이터를 읽기로 청크합니다. 데이터 흐름에서 ADF는 이 설정을 사용하여 Spark 열 형식 캐싱을 설정합니다. 이 필드는 비어 있는 경우 Spark 기본값을 사용 하는 옵션 필드입니다.

**격리 수준**: 매핑 데이터 흐름에서 SQL 원본의 기본값은 커밋되지 않은 읽기입니다. 여기에서 격리 수준을 다음 값 중 하나로 변경할 수 있습니다.

- 커밋된 읽기
- 커밋되지 않은 읽기
- 반복 가능한 읽기
- Serializable *-없음 (격리 수준 무시)

![격리 수준](media/data-flow/isolationlevel.png "격리 수준")

### <a name="sink-transformation"></a>싱크 변환

Azure Synapse Analytics에 특정한 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 메서드:** 데이터베이스 대상에서 허용되는 작업을 결정합니다. 기본값은 삽입만 허용하는 것입니다. 행을 업데이트, upsert 또는 삭제하려면 해당 작업을 위해 행에 태그를 지정하는 데 행 변경 변환이 필요합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다.

**테이블 작업:** 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지 여부를 결정합니다.

- 없음: 테이블에 대한 작업이 수행되지 않습니다.
- 다시 만들기: 테이블이 삭제되고 다시 생성됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.
- 자르기: 대상 테이블의 모든 행이 제거됩니다.

**준비 사용:** Azure Synapse Analytics에 쓸 때 [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15)를 사용할지 여부를 결정합니다.

**일괄 처리 크기**: 각 버킷에 작성되는 행 수를 제어합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상되지만 데이터를 캐시할 때 메모리 부족 예외가 발생할 위험이 있습니다.

**사전 및 사후 SQL 스크립트**: 데이터를 싱크 데이터베이스에 기록하기 전(사전 처리)과 후(사후 처리)에 실행할 여러 줄 SQL 스크립트를 입력합니다.

![사전 및 사후 SQL 처리 스크립트](media/data-flow/prepost1.png "SQL 처리 스크립트")

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요.

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics에 대한 데이터 형식 매핑

Azure Synapse Analytics에서/로 데이터를 복사하는 경우, Azure Synapse Analytics 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

>[!TIP]
>SQL DW 지원 데이터 형식 및 지원되지 않는 데이터 형식에 대한 해결 방법에 관한 자세한 내용은 [Azure Synapse Analytics의 테이블 데이터 형식](../synapse-analytics/sql/develop-tables-data-types.md) 문서를 참조하세요.

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
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
