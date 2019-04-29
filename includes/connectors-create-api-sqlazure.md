---
title: 포함 파일
description: 포함 파일
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541146"
---
* Azure SQL Database를 사용하는 경우 [Azure SQL Database에 연결](#connect-azure-sql-db)의 단계를 따릅니다. 

* SQL Server를 사용하는 경우 [SQL Server에 연결](#connect-sql-server)의 단계를 따릅니다.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL Database에 연결

1. SQL 트리거 또는 작업이 연결 정보를 요구하는 경우 다음 단계를 따릅니다.

   1. 연결의 이름을 만듭니다.

   2. SQL Server를 선택한 다음, 데이터베이스를 선택합니다. 

      SQL Server를 선택한 후에만 데이터베이스 목록이 나타납니다.
 
   3. 서버에 대한 사용자 이름 및 암호를 제공합니다.

      Azure Portal의 SQL Database 속성 또는 연결 문자열에서 이 정보를 찾을 수 있습니다. 
      
      "사용자 ID=<*yourUserName*>"
      <br>
      "암호=<*yourPassword*>"

   이 예제에서는 트리거에 대한 연결 정보를 표시하지만, 다음 작업의 경우에도 이러한 단계가 진행됩니다.

   ![Azure SQL Database 연결 만들기](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   별표(*)는 필수 값을 나타냅니다.

   | 자산 | 값 | 세부 정보 | 
   |----------|-------|---------| 
   | 연결 이름 | <*my-sql-connection*> | 연결의 이름 | 
   | SQL Server 이름 | <*my-sql-server*> | SQL Server의 이름 |
   | SQL Database 이름 | <*my-sql-database*>  | SQL Database의 이름 | 
   | 사용자 이름 | <*my-sql-username*> | 데이터베이스에 액세스하기 위한 사용자 이름 |
   | 암호 | <*my-sql-password*> | 데이터베이스에 액세스하기 위한 암호 | 
   |||| 

2. 작업을 완료하면 **만들기**를 선택합니다.

3. 연결을 만든 후 [SQL 트리거 추가](#add-sql-trigger) 또는 [SQL 작업 추가](#add-sql-action)를 계속 진행합니다.

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server에 연결

게이트웨이를 선택하려면 먼저 [데이터 게이트웨이를 설정](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)해야 합니다. 따라서 연결을 만들 때 게이트웨이 목록에 게이트웨이가 표시됩니다.

1. SQL 트리거 또는 작업이 연결 정보를 요구하는 경우 다음 단계를 따릅니다.

   1. 트리거 또는 작업에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하여 SQL Server 옵션을 표시합니다.

   2. 연결의 이름을 만듭니다.

   3. SQL Server의 주소를 제공하고 데이터베이스의 이름을 제공합니다.
   
      연결 문자열에서 이 정보를 찾을 수 있습니다. 
      
      * "서버=<*yourServerAddress*>"
      * "데이터베이스=<*yourDatabaseName*>"

   4. 서버에 대한 사용자 이름 및 암호를 제공합니다.

      연결 문자열에서 이 정보를 찾을 수 있습니다. 
      
      * "사용자 ID=<*yourUserName*>"
      * "암호=<*yourPassword*>"

   5. SQL Server에서 Windows 또는 기본 인증을 사용하는 경우 인증 유형을 선택합니다.

   6. 이전에 만든 온-프레미스 데이터 게이트웨이의 이름을 선택합니다.
   
      게이트웨이가 목록에 나타나지 않으면 올바르게 [게이트웨이를 설정](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)했는지 확인합니다.

   이 예제에서는 트리거에 대한 연결 정보를 표시하지만, 다음 작업의 경우에도 이러한 단계가 진행됩니다.

   ![SQL Server 연결 만들기](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   별표(*)는 필수 값을 나타냅니다.

   | 자산 | 값 | 세부 정보 | 
   |----------|-------|---------| 
   | 온-프레미스 게이트웨이를 통해 연결 | 먼저 SQL Server 설정에 대해 이 옵션을 선택합니다. | | 
   | 연결 이름 | <*my-sql-connection*> | 연결의 이름 | 
   | SQL Server 이름 | <*my-sql-server*> | SQL Server의 이름 |
   | SQL Database 이름 | <*my-sql-database*>  | SQL Database의 이름 |
   | 사용자 이름 | <*my-sql-username*> | 데이터베이스에 액세스하기 위한 사용자 이름 |
   | 암호 | <*my-sql-password*> | 데이터베이스에 액세스하기 위한 암호 | 
   | 인증 유형 | Windows 또는 Basic | 선택 사항: SQL server에서 사용 하는 인증 유형 | 
   | 게이트웨이 | <*my-data-gateway*> | 온-프레미스 데이터 게이트웨이의 이름 | 
   |||| 

2. 작업을 완료하면 **만들기**를 선택합니다. 

3. 연결을 만든 후 [SQL 트리거 추가](#add-sql-trigger) 또는 [SQL 작업 추가](#add-sql-action)를 계속 진행합니다.
