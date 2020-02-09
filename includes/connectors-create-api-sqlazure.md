---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789202"
---
* Azure SQL Database를 사용하는 경우 [Azure SQL Database에 연결](#connect-azure-sql-db)의 단계를 따릅니다.

* SQL Server를 사용하는 경우 [SQL Server에 연결](#connect-sql-server)의 단계를 따릅니다.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Azure SQL Database에 연결

SQL 트리거 또는 작업에서 연결 정보를 묻는 메시지가 표시 되 면 트리거와 동작 모두에 대해 작동 하는 다음 단계를 수행 합니다.

1. **연결 이름**에 연결 이름을 만듭니다.

1. **SQL Server 이름**에서 Azure SQL Server를 선택 합니다. **SQL Database 이름** 목록이 표시 되 면 데이터베이스를 선택 합니다. Azure SQL server에 대한 사용자 이름 및 암호를 제공 합니다.

   SQL database 속성의 Azure Portal 또는 연결 문자열에서이 정보를 찾을 수도 있습니다.

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Azure SQL Database에 대한 연결 만들기](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 완료되면 **만들기**를 선택합니다.

1. 연결을 만든 후 [sql 트리거 추가](#add-sql-trigger) 또는 [sql 작업 추가](#add-sql-action)를 계속 합니다.

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>SQL Server에 연결

SQL 트리거 또는 작업에서 연결 정보를 묻는 메시지가 표시 되 면 트리거와 동작 모두에 대해 작동 하는 다음 단계를 수행 합니다. 로컬 컴퓨터에 [온-프레미스 데이터 게이트웨이](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) 를 설치 하 고 [Azure 데이터 게이트웨이 리소스를 만드는](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)데 필요한 시나리오의 경우 먼저 이러한 요구 사항을 완료 해야 합니다. 그렇지 않으면 연결을 만들 때 게이트웨이 리소스가 게이트웨이 목록에 표시 되지 않습니다.

또한 [ise (integration service environment)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)에서 SQL Server 커넥터를 사용 하 여 Windows 인증을 사용 하려면 커넥터의 비 ise 버전 및 온-프레미스 데이터 게이트웨이를 사용 합니다. ISE 레이블이 지정 된 버전은 Windows 인증을 지원 하지 않습니다.

1. **연결 이름**에 연결 이름을 만듭니다.

1. 트리거 또는 작업에서 **온-프레미스 데이터 게이트웨이를 통해 연결**을 선택하여 SQL Server 옵션을 표시합니다.

1. Sql **Server 이름** 및 **sql 데이터베이스 이름**에 대해 sql server의 주소와 데이터베이스 이름을 제공 합니다. 사용자 **이름** 및 **암호**에 서버에 대한 사용자 이름 및 암호를 제공 합니다.

   연결 문자열에서이 정보를 찾을 수도 있습니다.

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Server에 대한 연결 만들기](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. SQL server에서 Windows 또는 기본 인증을 사용 하는 경우 **인증 유형을**선택 합니다.

1. **게이트웨이**아래에서 이전에 만든 온-프레미스 데이터 게이트웨이와 연결 된 Azure 구독을 선택 하 고 온-프레미스 데이터 게이트웨이의 이름을 선택 합니다.

   게이트웨이가 목록에 나타나지 않으면 올바르게 [게이트웨이를 설정](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)했는지 확인합니다.

   ![SQL Server 연결 만들기 완료](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 완료되면 **만들기**를 선택합니다.

1. 연결을 만든 후 [SQL 트리거 추가](#add-sql-trigger) 또는 [SQL 작업 추가](#add-sql-action)를 계속 진행합니다.
