---
title: SQL 주문형(미리 보기)의 스토리지에서 파일에 액세스
description: Synapse SQL 내에서 SQL 주문형(미리 보기) 리소스를 사용하여 스토리지 파일을 쿼리하는 방법을 설명합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2a0751f12f33a36d9e0003977bcf40b66d715615
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986953"
---
# <a name="access-external-storage-in-synapse-sql-on-demand"></a>Synapse SQL(주문형)에서 외부 스토리지에 액세스

이 문서에서는 사용자가 Synapse SQL(주문형)의 Azure Storage에 저장된 파일의 데이터를 읽는 방법에 대해 설명합니다. 사용자는 다음과 같은 옵션을 사용하여 스토리지에 액세스할 수 있습니다.

- [OPENROWSET](develop-openrowset.md) 함수 - Azure Storage의 파일에 대한 임시 쿼리를 사용할 수 있습니다.
- [외부 테이블](develop-tables-external-tables.md) - 외부 파일 세트를 기반으로 미리 정의된 데이터 구조입니다.

사용자는 Azure AD 통과 인증(Azure AD 보안 주체의 기본값)이나 SAS 인증(SQL 보안 주체의 기본값)과 같은 [다른 인증 방법](develop-storage-files-storage-access-control.md)을 사용할 수 있습니다.

## <a name="query-files-using-openrowset"></a>OPENROWSET를 사용하여 파일 쿼리

OPENROWSET를 사용하면 스토리지에 대한 액세스 권한이 있는 사용자는 Azure 스토리지의 외부 파일을 쿼리할 수 있습니다. Synapse SQL 주문형 엔드포인트에 연결된 사용자는 다음 쿼리를 사용하여 Azure 스토리지의 파일 콘텐츠를 읽어야 합니다.

```sql
SELECT * FROM
 OPENROWSET(BULK 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/*.parquet', format= 'parquet') as rows
```

사용자는 다음과 같은 액세스 규칙을 사용하여 스토리지에 액세스할 수 있습니다.

- Azure AD 사용자 - `OPENROWSET`는 호출자의 Azure AD ID를 사용하여 Azure Storage에 액세스하거나 익명 액세스를 사용하여 스토리지에 액세스합니다.
- SQL 사용자 – `OPENROWSET`는 익명 액세스를 통해 스토리지에 액세스하거나 SAS 토큰 또는 작업 영역의 관리 ID를 사용하여 가장할 수 있습니다.

### <a name="impersonation"></a>[가장](#tab/impersonation)

또한 SQL 보안 주체는 OPENROWSET를 사용하여 작업 영역의 관리 ID 또는 SAS 토큰으로 보호되는 파일을 직접 쿼리할 수 있습니다. SQL 사용자가 이 함수를 실행하는 경우 `ALTER ANY CREDENTIAL` 권한을 가진 고급 사용자는 다음과 같이 함수의 URL과 일치하는 서버 범위 자격 증명을 만들고(스토리지 이름 및 컨테이너 사용) 이 자격 증명에 대한 REFERENCES 권한을 OPENROWSET 함수의 호출자에게 부여해야 합니다.

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[https://<storage_account>.dfs.core.windows.net/<container>] TO sqluser
```

URL과 일치하는 서버 수준 자격 증명이 없거나 SQL 사용자에게 이 자격 증명에 대한 참조 권한이 없는 경우 오류가 반환됩니다. SQL 보안 주체는 일부 Azure AD ID를 사용하여 가장할 수 없습니다.

### <a name="direct-access"></a>[직접 액세스](#tab/direct-access)

Azure AD 사용자가 자신의 ID를 사용하여 파일에 액세스할 수 있도록 하는 추가 설정이 필요하지 않습니다.
모든 사용자는 익명 액세스를 허용하는 Azure 스토리지에 액세스할 수 있습니다(추가 설정은 필요하지 않음).

---

> [!NOTE]
> 이 버전의 OPENROWSET는 기본 인증을 사용하여 쉽고 빠르게 데이터를 탐색할 목적으로 설계되었습니다. 가장 또는 관리 ID를 활용하려면 다음 섹션에서 설명하는 DATASOURCE가 있는 OPENROWSET를 사용하세요.

## <a name="query-data-sources-using-openrowset"></a>OPENROWSET를 사용하여 데이터 원본 쿼리

OPENROWSET를 사용하면 사용자가 외부 데이터 원본에 배치된 파일을 쿼리할 수 있습니다.

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

이 쿼리를 실행하는 사용자는 파일에 액세스할 수 있어야 합니다. 사용자가 [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity) 또는 [익명 액세스](develop-storage-files-storage-access-control.md?tabs=public-access)를 사용하여 파일에 직접 액세스할 수 없는 경우 [SAS 토큰](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) 또는 [작업 영역의 관리 ID](develop-storage-files-storage-access-control.md?tabs=managed-identity)를 사용하여 가장해야 합니다.

### <a name="impersonation"></a>[가장](#tab/impersonation)

`DATABASE SCOPED CREDENTIAL`은 참조되는 데이터 원본의 파일에 액세스하는 방법을 지정합니다(현재는 SAS 및 관리 ID). `CONTROL DATABASE` 권한이 있는 고급 사용자는 스토리지에 액세스하는 데 사용되는 `DATABASE SCOPED CREDENTIAL` 및 사용할 데이터 원본 및 자격 증명의 URL을 지정하는 `EXTERNAL DATA SOURCE`를 만들어야 합니다.

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>/' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

호출자가 OPENROWSET 함수를 실행하려면 다음 권한 중 하나가 필요합니다.

- OPENROWSET를 실행하는 다음 권한 중 하나가 필요합니다.
  - `ADMINISTER BULK OPERATIONS`는 로그인을 활성화하여 OPENROWSET 함수를 실행합니다.
  - `ADMINISTER DATABASE BULK OPERATIONS`는 데이터베이스 범위 사용자가 OPENROWSET 함수를 실행할 수 있게 합니다.
- `EXTERNAL DATA SOURCE`에서 참조되는 자격 증명에 대한 `REFERENCES DATABASE SCOPED CREDENTIAL`.

### <a name="direct-access"></a>[직접 액세스](#tab/direct-access)

사용자는 공용 액세스 스토리지를 참조하거나 Azure AD 통과 인증을 사용하는 CREDENTIAL이 없는 EXTERNAL DATA SOURCE를 만들 수 있습니다.

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---
## <a name="external-table"></a>EXTERNAL TABLE

테이블 읽기 권한이 있는 사용자는 Azure Storage 폴더 및 파일 세트 위에 생성된 EXTERNAL TABLE을 사용하여 외부 파일에 액세스할 수 있습니다.

[외부 테이블을 만드는 권한](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions)(예: CREATE TABLE 및 ALTER ANY CREDENTIAL 또는 REFERENCES DATABASE SCOPED CREDENTIAL)이 있는 사용자는 다음 스크립트를 사용하여 Azure Storage 데이터 원본 위에 테이블을 만들 수 있습니다.

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

이 테이블에서 데이터를 읽는 사용자는 파일에 액세스할 수 있어야 합니다. 사용자가 [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity) 또는 [익명 액세스](develop-storage-files-storage-access-control.md?tabs=public-access)를 사용하여 파일에 직접 액세스할 수 없는 경우 [SAS 토큰](develop-storage-files-storage-access-control.md?tabs=shared-access-signature) 또는 [작업 영역의 관리 ID](develop-storage-files-storage-access-control.md?tabs=managed-identity)를 사용하여 가장해야 합니다.

### <a name="impersonation"></a>[가장](#tab/impersonation)

DATABASE SCOPED CREDENTIAL은 참조되는 데이터 원본의 파일에 액세스하는 방법을 지정합니다. CONTROL DATABASE 권한이 있는 사용자는 스토리지에 액세스하는 데 사용되는 DATABASE SCOPED CREDENTIAL 및 사용할 데이터 원본 및 자격 증명의 URL을 지정하는 EXTERNAL DATA SOURCE를 만들어야 합니다.

```sql
EXECUTE AS somepoweruser;

-- Create MASTER KEY if it doesn't exists in database
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'some very strong password';

CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>' ,
 CREDENTIAL = cred
 ) ;
```

### <a name="direct-access"></a>[직접 액세스](#tab/direct-access)

사용자는 공용 액세스 스토리지를 참조하거나 Azure AD 통과 인증을 사용하는 CREDENTIAL이 없는 EXTERNAL DATA SOURCE를 만들 수 있습니다.

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>') ;
```
---

### <a name="read-external-files-with-external-table"></a>EXTERNAL TABLE을 사용하여 외부 파일 읽기

EXTERNAL TABLE을 사용하면 표준 SQL SELECT 문을 사용하는 데이터 원본을 통해 참조되는 파일의 데이터를 읽을 수 있습니다.

```sql
SELECT *
FROM dbo.DimProductsExternal
```

호출자는 데이터를 읽기 위해 다음 권한이 필요합니다.
- 외부 테이블에 대한 `SELECT` 권한
- `DATA SOURCE`에 `CREDENTIAL`이 있는 경우 `REFERENCES DATABASE SCOPED CREDENTIAL` 권한

## <a name="permissions"></a>사용 권한

다음 표에서는 위에 나열된 작업에 필요한 권한을 보여줍니다.

| 쿼리 | 필요한 사용 권한|
| --- | --- |
| 데이터 원본 없는 OPENROWSET(BULK) | `ADMINISTER BULK OPERATIONS`, `ADMINISTER DATABASE BULK OPERATIONS` 또는 SQL 로그인에는 SAS로 보호되는 스토리지에 대한 REFERENCES CREDENTIAL::\<URL>이 필요합니다. |
| 자격 증명이 없고 데이터 원본이 있는 OPENROWSET(BULK) | `ADMINISTER BULK OPERATIONS` 또는 `ADMINISTER DATABASE BULK OPERATIONS` |
| 자격 증명이 있고 데이터 원본이 있는 OPENROWSET(BULK) | `REFERENCES DATABASE SCOPED CREDENTIAL` 및 `ADMINISTER BULK OPERATIONS` 또는 `ADMINISTER DATABASE BULK OPERATIONS` 중 하나 |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` 및 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY EXTERNAL FILE FORMAT` 및 `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` 및 `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | 테이블을 만들려면 `CREATE TABLE`, `ALTER ANY SCHEMA`, `ALTER ANY DATA SOURCE` 및 `ALTER ANY EXTERNAL FILE FORMAT`이 필요합니다. 데이터를 읽으려면 쿼리의 각 테이블/보기/함수에 대한 `ADMINISTER BULK OPERATIONS`, `REFERENCES CREDENTIAL` 또는 `SELECT TABLE` 권한과 스토리지에 대한 읽기/쓰기 권한이 필요합니다. |

## <a name="next-steps"></a>다음 단계

이제 다음 방법 문서를 진행할 수 있습니다.

- [스토리지에서 데이터 쿼리](query-data-storage.md)

- [CSV 파일 쿼리](query-single-csv-file.md)

- [Parquet 파일 쿼리](query-parquet-files.md)

- [JSON 파일 쿼리](query-json-files.md)

- [폴더 및 여러 파일 쿼리](query-folders-multiple-csv-files.md)

- [분할 및 메타데이터 함수 사용](query-specific-files.md)

- [중첩 형식 쿼리](query-parquet-nested-types.md)

- [보기 만들기 및 사용](create-use-views.md)
