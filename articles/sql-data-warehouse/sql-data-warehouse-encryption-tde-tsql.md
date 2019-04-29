---
title: SQL Data Warehouse의 투명한 데이터 암호화(T-SQL) | Microsoft Docs
description: SQL Data Warehouse의 TDE(투명한 데이터 암호화)(T-SQL)
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 7908e6e0927357446ea45e16b7c44adb83ec1fd3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439063"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>투명한 데이터 암호화(TDE) 시작
> [!div class="op_single_selector"]
> * [보안 개요](sql-data-warehouse-overview-manage-security.md)
> * [인증](sql-data-warehouse-authentication.md)
> * [암호화(포털)](sql-data-warehouse-encryption-tde.md)
> * [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>필요한 권한
TDE(투명한 데이터 암호화)를 사용하려면 관리자 또는 dbmanager 역할의 멤버여야 합니다.

## <a name="enabling-encryption"></a>암호화 설정
SQL Data Warehouse에 대한 TDE를 사용하려면 다음 단계를 따르세요.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 데이터베이스를 호스팅하는 서버의 **마스터** 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>암호화 비활성화
SQL Data Warehouse에 대한 TDE를 사용하지 않으려면 다음 단계를 따르세요.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 **마스터** 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> TDE 설정을 변경하기 전에 일시 중지된 SQL Data Warehouse를 다시 시작해야 합니다.
> 
> 

## <a name="verifying-encryption"></a>암호화 확인
SQL Data Warehouse에 대한 암호화 상태를 확인하려면 다음 단계를 따르세요.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 **마스터** 또는 인스턴스 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

```1```의 결과는 암호화된 데이터베이스를 나타내고 ```0```은(는) 암호화되지 않은 데이터베이스를 나타냅니다.

## <a name="encryption-dmvs"></a>암호화 DMV
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: http://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->