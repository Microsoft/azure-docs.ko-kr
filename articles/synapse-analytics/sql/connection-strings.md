---
title: Synapse SQL에 대한 연결 문자열(미리 보기)
description: Synapse SQL에 대한 연결 문자열(미리 보기)
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 42a1110b089d5edf9793bf2bb2c699a717a4b1a4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004972"
---
# <a name="connection-strings-for-synapse-sql-preview"></a>Synapse SQL에 대한 연결 문자열(미리 보기)

[ADO.NET](https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) 및 [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx)와 같은 여러 가지 다양한 애플리케이션 프로토콜을 사용하여 Synapse SQL(미리 보기)에 연결할 수 있습니다. 다음은 각 프로토콜에 대한 연결 문제열의 몇 가지 예입니다. 

또한 Azure 포털을 사용하여 연결 문자열을 빌드할 수도 있습니다.  Azure Portal을 사용하여 연결 문자열을 빌드하려면 데이터베이스 블레이드로 이동한 후 *Essentials* 링크에서 *데이터베이스 연결 문자열 표시*를 클릭합니다.

## <a name="sample-adonet-connection-string"></a>샘플 ADO.NET 연결 문자열

```csharp
Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>샘플 ODBC 연결 문자열

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.sql.azuresynapse.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>샘플 PHP 연결 문자열

```PHP
Server: {your_server}.sql.azuresynapse.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.sql.azuresynapse.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.sql.azuresynapse.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>샘플 JDBC 연결 문자열

```Java
jdbc:sqlserver://yourserver.sql.azuresynapse.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.sql.azuresynapse.net;loginTimeout=30;
```

> [!NOTE]
> 사용 불가능한 짧은 시간 동안 연결이 효력을 유지하려면 연결 제한 시간을 300초로 설정하는 것이 좋습니다.

## <a name="recommendations"></a>권장 사항

**SQL 주문형** 쿼리를 실행하기 위해 권장되는 도구는 [Azure Data Studio](get-started-azure-data-studio.md) 및 Azure Synapse Studio입니다.

## <a name="next-steps"></a>다음 단계

Visual Studio 및 기타 애플리케이션으로 분석을 쿼리하려면 [Visual Studio를 사용하여 쿼리](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)를 참조하세요.
