---
title: Stretch Database TSQL에 대해 투명한 데이터 암호화를 사용하도록 설정 - Azure | Microsoft Docs
description: Azure TSQL에서 SQL Server Stretch Database에 대해 TDE(투명한 데이터 암호화)를 사용하도록 설정
services: sql-server-stretch-database
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 27753d91-9ca2-4d47-b34d-b5e2c2f029bb
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
ms.openlocfilehash: 634e1b097f2ca16a8c52da02445b3f5f2af7ff3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708824"
---
# <a name="enable-transparent-data-encryption-tde-for-stretch-database-on-azure-transact-sql"></a>Azure에서 Stretch Database에 대해 TDE(투명한 데이터 암호화)를 사용하도록 설정(Transact-SQL)
> [!div class="op_single_selector"]
> * [Azure Portal](sql-server-stretch-database-encryption-tde.md)
> * [TSQL](sql-server-stretch-database-tde-tsql.md)
>
>

TDE(투명한 데이터 암호화)는 애플리케이션에 대한 변경 요구 없이 데이터베이스, 연결된 백업 및 저장된 트랜잭션 로그 파일에 대한 실시간 암호화 및 암호 해독을 수행하여 악의적인 활동의 위협으로부터 보호합니다.

TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 저장소를 암호화합니다. 데이터베이스 암호화 키는 기본 제공 서버 인증서에 의해 보호됩니다. 기본 제공 서버 인증서는 각 Azure 서버에 대해 고유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전합니다. TDE에 대한 일반적인 설명은 [TDE(투명한 데이터 암호화)]를 참조하세요.

## <a name="enabling-encryption"></a>암호화 설정
스트레치 사용 SQL Server 데이터베이스에서 마이그레이션된 데이터를 저장하는 Azure 데이터베이스에 대해 TDE를 사용하도록 설정하려면 다음을 수행합니다.

1. master 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 데이터베이스를 호스트하는 서버의 **master** 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>암호화 비활성화
스트레치 사용 SQL Server 데이터베이스에서 마이그레이션된 데이터를 저장하는 Azure 데이터베이스에 대해 TDE를 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 마스터 데이터베이스에서 *dbmanager* 역할의 관리자 또는 멤버인 로그인을 사용하여 **마스터** 데이터베이스에 연결
2. 다음 문을 실행하여 데이터베이스를 암호화합니다.

```sql
ALTER DATABASE [database_name] SET ENCRYPTION OFF;
```

## <a name="verifying-encryption"></a>암호화 확인
스트레치 사용 SQL Server 데이터베이스에서 마이그레이션된 데이터를 저장하는 Azure 데이터베이스에 대한 암호화 상태를 확인하려면 다음을 수행합니다.

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

<!--Anchors-->
[TDE(투명한 데이터 암호화)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->
