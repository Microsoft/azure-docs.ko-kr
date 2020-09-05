---
title: Node.js를 사용하여 데이터베이스 쿼리
description: Node.js를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스에 연결하고 T-SQL 문을 사용하여 쿼리하는 프로그램을 만드는 방법입니다.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-javascript
ms.openlocfilehash: c079d9bad15de6dac93cd9be23631786673bd009
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756387"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>빠른 시작: Node.js를 사용하여 Azure SQL Database 또는 Azure SQL Managed Instance의 데이터베이스 쿼리
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

이 빠른 시작에서는 Node.js를 사용하여 데이터베이스에 연결하고 데이터를 쿼리합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | 작업 | SQL Database | SQL Managed Instance | Azure VM의 SQL Server |
  |:--- |:--- |:---|:---|
  | 생성| [포털](single-database-create-quickstart.md) | [포털](../managed-instance/instance-create-quickstart.md) | [포털](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | 구성 | [서버 수준 IP 방화벽 규칙](firewall-create-server-level-portal-quickstart.md)| [VM에서 연결](../managed-instance/connect-vm-instance-configure.md)|
  |||[온-프레미스에서 연결](../managed-instance/point-to-site-p2s-configure.md) | [SQL Server 인스턴스에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers 복원](../managed-instance/restore-sample-database-quickstart.md) |
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기| [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||


- [Node.js](https://nodejs.org) 관련 소프트웨어

  # <a name="macos"></a>[macOS](#tab/macos)

  Homebrew 및 Node.js를 설치한 다음, [macOS에서 SQL Server를 사용하여 Node.js 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/node/mac/)의 **1.2** 및 **1.3** 단계를 사용하여 ODBC 드라이버와 SQLCMD를 설치합니다.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Node.js를 설치한 다음, [Ubuntu에서 SQL Server를 사용하여 Node.js 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/)의 **1.2** 및 **1.3** 단계를 사용하여 ODBC 드라이버와 SQLCMD를 설치합니다.

  # <a name="windows"></a>[Windows](#tab/windows)

  Chocolatey 및 Node.js를 설치한 다음, [Windows에서 SQL Server를 사용하여 Node.js 앱 만들기](https://www.microsoft.com/sql-server/developer-get-started/node/windows/)의 **1.2** 및 **1.3** 단계를 사용하여 ODBC 드라이버와 SQLCMD를 설치합니다.

  ---

> [!IMPORTANT]
> 이 문서의 스크립트는 **Adventure Works** 데이터베이스를 사용하도록 작성되었습니다.

> [!NOTE]
> 필요에 따라 Azure SQL Managed Instance를 사용하도록 선택할 수 있습니다.
>
> 만들고 구성하려면 [Azure Portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) 또는 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)를 사용한 다음, [온-프레미스](../managed-instance/point-to-site-p2s-configure.md) 또는 [VM](../managed-instance/connect-vm-instance-configure.md) 연결을 설정합니다.
>
> 데이터를 로드하려면 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) 파일을 사용하여 [BACPAC로 복원](database-import.md) 또는 [Wide World Importers 데이터베이스 복원](../managed-instance/restore-sample-database-quickstart.md)을 참조하세요.

## <a name="get-server-connection-information"></a>서버 연결 정보 가져오기

Azure SQL Database의 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL Databases** 또는 **SQL Managed Instances** 페이지로 이동합니다.

3. **개요** 페이지에서 Azure SQL Database의 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 Azure VM의 Azure SQL Managed Instance 또는 SQL Server에 대한 **호스트** 옆에 있는 정규화된 서버 이름(또는 IP 주소)을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

> [!NOTE]
> Azure VM의 SQL Server에 대한 연결 정보는 [SQL Server에 연결](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server)을 참조하세요.

## <a name="create-the-project"></a>프로젝트 만들기

명령 프롬프트를 열고 *sqltest*라는 폴더를 만듭니다. 생성한 폴더를 열고 다음 명령을 실행합니다.

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>데이터베이스를 쿼리하는 코드 추가

1. 원하는 텍스트 편집기에서 *sqltest.js* 파일을 새로 만듭니다.

1. 해당 내용을 다음 코드로 바꿉니다. 그런 다음, 서버, 데이터베이스, 사용자 및 암호에 적절한 값을 추가합니다.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });

      connection.execSql(request);
    }
    ```

> [!NOTE]
> 이 코드 예제는 Azure SQL Database에서 **AdventureWorksLT** 샘플 데이터베이스를 사용합니다.

## <a name="run-the-code"></a>코드 실행

1. 명령 프롬프트에서 프로그램을 실행합니다.

    ```bash
    node sqltest.js
    ```

1. 상위 20개 행이 반환되는지 확인한 다음, 애플리케이션 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server용 Microsoft Node.js 드라이버](/sql/connect/node-js/node-js-driver-for-sql-server)

- [.NET Core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md) 또는 [SSMS](connect-query-ssms.md)(Windows만 해당)를 사용하여 Windows/Linux/macOS에서 연결하고 쿼리 실행

- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core 시작](/dotnet/core/tutorials/using-with-xplat-cli)

- [.NET](design-first-database-csharp-tutorial.md) 또는 [SSMS](design-first-database-tutorial.md)를 사용하여 Azure SQL Database에서 첫 번째 단일 데이터베이스 디자인
