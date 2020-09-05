---
title: 연결 문제 해결
description: Synapse SQL 풀의 연결 문제를 해결합니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 2edb3060437080e528d41d4af5f4affd4fbf3316
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010191"
---
# <a name="troubleshooting-connectivity-issues-in-synapse-sql-pool"></a>Synapse SQL 풀의 연결 문제 해결

이 문서에서는 SQL 풀 데이터베이스 연결과 관련된 일반적인 문제 해결 방법을 보여줍니다.

## <a name="check-service-availability"></a>서비스 가용성 확인

서비스를 사용할 수 있는지 확인합니다. Azure Portal에서 연결하려는 SQL 풀로 이동합니다. 왼쪽 TOC 패널에서 **문제 진단 및 해결**을 클릭합니다.

![리소스 상태 선택](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL 풀의 상태는 여기에 표시됩니다. 서비스가 **사용 가능**으로 표시되지 않는 경우 추가 단계를 확인합니다.

![서비스 사용 가능](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

리소스 상태에서 SQL 풀 인스턴스가 일시 중지 또는 크기 조정되는 것으로 표시되는 경우에는 지침에 따라 인스턴스를 다시 시작합니다.

![서비스 일시 중지](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png): Resource Health에 대한 추가 정보는 여기에서 찾을 수 있습니다.

## <a name="check-for-paused-or-scaling-operation"></a>일시 중지 또는 크기 조정 작업 확인

포털을 확인하여 SQL 풀 인스턴스가 일시 중지되었거나 크기 조정되었는지 확인합니다.

![서비스가 일시 중지됨](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

서비스를 일시 중지하거나 크기를 조정하는 것이 확인되는 경우 유지 관리 일정 중이 아닌지 확인합니다. SQL 풀 *개요*의 포털에서 선택한 유지 관리 일정이 표시됩니다.

![유지 관리 일정 개요](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

그렇지 않으면 IT 관리자에게 문의하여 이 유지 관리가 예약된 이벤트가 아닌지 확인합니다. SQL 풀 인스턴스를 다시 시작하려면 [다음 단계](pause-and-resume-compute-portal.md)를 수행합니다.

## <a name="check-your-firewall-settings"></a>방화벽 설정 확인

SQL 풀 데이터베이스는 포트 1433을 통해 통신합니다.  회사 네트워크 내에서 연결하려는 경우 1433 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 포트 1433을 열지 않는 한 [논리 서버](../../azure-sql/database/logical-servers.md) 에 연결할 수 없습니다. 방화벽 구성에 대한 추가 정보는 [여기](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules)에서 찾을 수 있습니다.

## <a name="check-your-vnetservice-endpoint-settings"></a>VNet/Service Endpoint 설정 확인

40914 및 40615 오류를 수신하는 경우 [여기에 오류 설명 및 해결 방법](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)을 참조하세요.

## <a name="check-for-the-latest-drivers"></a>최신 드라이버 확인

### <a name="software"></a>소프트웨어

최신 도구를 사용하여 SQL 풀에 연결하고 있는지 확인합니다.

- SSMS
- Azure Data Studio
- SQL Server Data Tools(Visual Studio)

### <a name="drivers"></a>드라이버

최신 드라이버 버전을 사용하고 있는지 확인합니다.  이전 버전의 드라이버를 사용하면 이전 드라이버가 새 기능을 지원하지 않을 수 있기 때문에 예기치 않은 동작이 발생할 수 있습니다.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="check-your-connection-string"></a>연결 문자열 확인

연결 문자열이 올바르게 설정되었는지 확인합니다.  다음은 몇 가지 샘플입니다.  [연결 문자열에 대한 추가 정보는 여기](sql-data-warehouse-connection-strings.md)에서 찾을 수 있습니다.

ADO.NET 연결 문자열

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC 연결 문자열

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP 연결 문자열

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC 연결 문자열

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>일시적 연결 문제

대기 중인 요청 수가 많은 서버에서 부하가 과도하게 발생하는지 확인합니다. 추가 리소스를 위해 SQL 풀을 확장해야 할 수도 있습니다.

## <a name="common-error-messages"></a>일반적인 오류 메시지

40914 및 40615 오류는 [여기에 오류 설명 및 해결 방법](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)을 참조하세요.

## <a name="still-having-connectivity-issues"></a>여전히 연결 문제가 있나요?

엔지니어링 팀이 지원할 수 있도록 [지원 티켓](sql-data-warehouse-get-started-create-support-ticket.md)를 만듭니다.
