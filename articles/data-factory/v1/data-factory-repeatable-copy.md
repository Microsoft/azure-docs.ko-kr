---
title: Azure Data Factory에서 반복 가능한 복사 | Microsoft Docs
description: 데이터를 복사하는 조각이 두 번 이상 실행되더라도 중복을 방지하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 20c916275acd6bb79675c592711b17b277c9fc78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605207"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Azure Data Factory에서 반복 가능한 복사

## <a name="repeatable-read-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다.  
 
> [!NOTE]
> 다음 예제는 Azure SQL에 대한 것이지만, 직사각 데이터 세트를 지원하는 모든 데이터 저장소에 적용할 수 있습니다. 데이터 저장소에 대해 소스의 **type** 및 **query** 속성(예: sqlReaderQuery 대신 query)을 조정해야 할 수도 있습니다.   

일반적으로 관계형 저장소에서 읽어올 때는 해당 조각에 대한 데이터만 읽고자 할 것입니다. 이것은 Azure Data Factory에서 제공하는 WindowStart 및 WindowEnd 시스템 변수를 사용하면 됩니다. [Azure Data Factory - 함수 및 시스템 변수](data-factory-functions-variables.md) 문서에서 Azure Data Factory의 변수 및 함수 부분을 읽어보세요. 예제: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
이 쿼리는 MyTable 테이블에서 조각 기간 범위(WindowEnd-> WindowStart)에 속하는 데이터를 읽습니다. 이 조각을 다시 실행하면 항상 같은 데이터만 읽게 됩니다. 

다른 경우 전체 테이블을 읽고 다음과 같이 sqlReaderQuery를 정의하고자 할 수 있습니다.

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>SqlSink에 반복 가능한 쓰기
다른 데이터 저장소에서 **Azure SQL/SQL Server**로 데이터를 복사할 때 의도치 않은 결과를 방지하려면 반복성을 염두에 두어야 합니다. 

Azure SQL/SQL Server 데이터베이스로 데이터를 복사할 때 복사 작업은 기본적으로 싱크 테이블에 데이터를 추가합니다. 예를 들어 두 개의 레코드가 포함된 CSV(쉼표로 구분 된 값) 파일에서 Azure SQL/SQL Server 데이터베이스의 다음 테이블로 데이터를 복사한다고 가정해 봅시다. 조각이 실행되면 두 레코드가 SQL 테이블에 복사됩니다. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

원본 파일에서 오류를 발견하고 Down Tube의 수량을 2에서 4로 업데이트했다고 가정해 보겠습니다. 해당 기간의 데이터 조각을 수동으로 다시 실행하면 Azure/SQL Server 데이터베이스에 새 레코드가 두 개 추가된 것을 알 수 있습니다. 이 예에서는 테이블에 기본 키 제약 조건이 있는 열이 없다고 가정합니다.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

이 동작을 방지하려면 다음 두 메커니즘 중 하나를 사용하여 UPSERT 의미 체계를 지정해야 합니다.

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>메커니즘 1: sqlWriterCleanupScript 사용
**sqlWriterCleanupScript** 속성을 사용하여 싱크 테이블의 데이터를 정리한 후 조각이 실행되면 데이터를 삽입할 수 있습니다. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

조각이 실행되면 SQL 테이블의 조각에 해당하는 데이터를 삭제하는 정리 스크립트가 가장 먼저 실행됩니다. 그러면 복사 작업에서 데이터를 SQL 테이블에 삽입합니다. 조각이 다시 실행되면 필요에 따라 수량이 업데이트됩니다.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

원본 csv에서 Flat Washer 레코드가 제거되었다고 가정해 보겠습니다. 이제 조각을 다시 실행하면 다음과 같은 결과가 나옵니다. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

복사 작업에서는 정리 스크립트를 실행하여 해당 조각에 대한 데이터를 삭제했습니다. 그런 다음 csv(레코드 하나만 포함)에서 입력을 읽어서 테이블에 삽입했습니다. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>메커니즘 2: sliceIdentifierColumnName 사용
> [!IMPORTANT]
> 현재 sliceIdentifierColumnName은 Azure SQL Data Warehouse에 지원되지 않습니다. 

두 번째 메커니즘은 대상 테이블에 전용 열(sliceIdentifierColumnName)을 만들어서 반복성을 유지합니다. 이 열은 Azure 데이터 팩터리에서 원본 및 대상을 동기화 상태로 유지하도록 할 때 사용됩니다. 이 방법은 대상 SQL 테이블 스키마를 유연하게 변경하거나 정의할 수 있을 때 작동됩니다. 

이 열은 반복성을 위해 Azure Data Factory에서 사용되며 Azure Data Factory가 테이블의 어떠한 스키마도 변경하지 않는 프로세스에도 사용됩니다. 이 방식을 사용하는 방법:

1. 대상 SQL 테이블에서 **이진 형식(32)** 으로 열을 정의합니다. 이 열에는 제약 조건이 없어야 합니다. 이 예에서는 이 열의 이름을 AdfSliceIdentifier라고 하겠습니다.


    원본 테이블:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    대상 테이블: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. 복사 작업에서 다음과 같이 사용합니다.
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory는 원본과 대상의 동기화 상태를 유지하기 위해 필요한 대로 이 열을 채웁니다. 이 열의 값은 이 컨텍스트 외부에서 사용할 수 없습니다. 

메커니즘 1과 마찬가지로, 복사 작업이 대상 SQL 테이블에서 지정된 조각의 데이터를 자동으로 정리합니다. 그런 다음 원본의 데이터를 대상 테이블에 삽입합니다. 

## <a name="next-steps"></a>다음 단계
완전한 JSON 예제에 대한 다음 커넥터 문서를 검토합니다. 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
