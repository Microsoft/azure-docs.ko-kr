---
title: SQL Data Warehouse의 투명한 데이터 암호화(포털) | Microsoft Docs
description: SQL Data Warehouse의 TDE(투명한 데이터 암호화)
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: be31547e8f4063a1b1fe225420fb6c06d9a1588b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61439450"
---
# <a name="get-started-with-transparent-data-encryption-tde-in-sql-data-warehouse"></a>SQL Data Warehouse에서 투명한 데이터 암호화(TDE) 시작
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

1.  [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **켜기** 설정 선택 ![][2]
5. **저장** 선택 
   ![][3]  

## <a name="disabling-encryption"></a>암호화 비활성화
SQL Data Warehouse에 대한 TDE를 비활성화하려면 다음 단계를 따르세요.

1.  [Azure 포털](https://portal.azure.com)
2. 데이터베이스 블레이드에서 **설정** 단추 클릭
3. **투명한 데이터 암호화** 옵션 선택 ![][1]
4. **끄기** 설정 선택 ![][4]
5. **저장** 선택 
   ![][5]  

## <a name="encryption-dmvs"></a>암호화 DMV
다음 DMV로 암호화를 확인할 수 있습니다.

* [sys.databases]
* [sys.dm_pdw_nodes_database_encryption_keys]

<!--MSDN references-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->
