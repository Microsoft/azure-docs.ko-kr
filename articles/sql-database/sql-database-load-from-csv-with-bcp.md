---
title: CSV 파일에서 Azure SQL Database로 데이터 로드(bcp) | Microsoft Docs
description: 작은 데이터 크기의 경우 bcp를 사용하여 Azure SQL Database로 데이터를 가져옵니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: carlrab
manager: digimobile
origin.date: 01/25/2019
ms.date: 03/04/2019
ms.openlocfilehash: 8d0d81d9c140c15f2e60d3f6e3033a8b0b629228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60702472"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>CSV에서 Azure SQL Database(플랫 파일)로 데이터 로드

bcp 명령줄 유틸리티를 사용하여 CSV 파일에서 Azure SQL Database로 데이터를 가져올 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

### <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure SQL Database 서버 및 데이터베이스
* 설치된 bcp 명령줄 유틸리티
* 설치된 sqlcmd 명령줄 유틸리티

[Microsoft 다운로드 센터][Microsoft Download Center]에서 bcp 및 sqlcmd 유틸리티를 다운로드할 수 있습니다.

### <a name="data-in-ascii-or-utf-16-format"></a>ASCII 또는 UTF-16 형식 데이터

사용자의 데이터로 이 자습서를 수행하는 경우에는, bcp가 UTF-8을 지원하지 않으므로, 데이터에 ASCII 또는 UTF-16 인코딩을 사용해야 합니다. 

## <a name="1-create-a-destination-table"></a>1. 대상 테이블 만들기

SQL Database에서 테이블을 대상 테이블로 정의합니다. 테이블의 열은 데이터 파일의 각 행에 있는 데이터와 일치해야 합니다.

테이블을 만들려면, 명령 프롬프트를 열고 sqlcmd.exe를 사용하여 다음 명령을 실행합니다.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2. 원본 데이터 파일 만들기

메모장을 열고 다음 데이터 줄을 새 텍스트 파일에 복사한 다음 이 파일을 로컬 임시 디렉터리 C:\Temp\DimDate2.txt에 저장합니다. 이 데이터는 ASCII 형식입니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(선택 사항) SQL Server 데이터베이스에서 사용자의 데이터를 내보내려면, 명령 프롬프트를 열고 다음 명령을 실행합니다. TableName, ServerName, DatabaseName, Username 및 Password를 사용자의 정보로 바꿉니다.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. 데이터 로드

데이터를 로드하려면, 명령 프롬프트를 열고 Server Name, Database name, Username 및 Password 값을 사용자의 정보로 바꿔서 다음 명령을 실행합니다.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

이 명령을 사용하여 데이터가 제대로 로드되었는지 확인합니다.

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

결과는 다음과 같습니다.

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>다음 단계

SQL Server 데이터베이스를 마이그레이션하려면 [SQL Server 데이터베이스 마이그레이션](sql-database-single-database-migrate.md)을 참조하세요.

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
<!--Update_Description: update metadata-->