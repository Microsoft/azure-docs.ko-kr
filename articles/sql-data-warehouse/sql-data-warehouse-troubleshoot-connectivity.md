---
title: Azure SQL Data Warehouse 문제 해결 | Microsoft Docs
description: Azure SQL Data Warehouse 문제 해결
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 03/27/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 5115ffbc3568c87c37bae4a3e65c37f8504f1fb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475359"
---
# <a name="troubleshooting-connectivity-issues"></a>연결 문제 해결

이 문서에서는 SQL Data Warehouse에 연결 하는 관련 일반적인 문제 해결 기술을 나열 합니다.
- [서비스 가용성 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [일시 중지 또는 크기 조정 작업 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [방화벽 설정 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [VNet/서비스 끝점 설정을 확인합니다](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [최신 드라이버에 대 한 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [연결 문자열 확인](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [일시적인 연결 문제](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [일반적인 오류 메시지](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>서비스 가용성 확인

서비스를 사용할 경우 확인 합니다. Azure portal에서 연결 하려는 SQL data warehouse로 이동 합니다. 왼쪽된 목차 창에서 클릭 **진단 및 문제 해결**합니다.

![리소스 상태를 선택 합니다.](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

SQL data warehouse의 상태를 여기에 표시 됩니다. 서비스로 표시 되지 않는 경우 **사용 가능한**, 단계를 확인 합니다.

![사용할 수 있는 서비스](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

리소스 상태에 표시 하는 경우 데이터 웨어하우스가 일시 중지 또는 데이터 웨어하우스를 다시 시작 하기 위한 지침을 따라 크기 조정 합니다.

![서비스가 일시 중지](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Resource Health에 대 한 자세한 내용은 여기에서 찾을 수 있습니다.

## <a name="check-for-paused-or-scaling-operation"></a>일시 중지 또는 크기 조정 작업 확인

SQL data warehouse 일시 중지 된 경우를 확인 하려면 포털 또는 크기 조정 확인 합니다.

![서비스가 일시 중지](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

표시 하는 경우 서비스 일시 중지 또는 크기 조정 확인 하는 동안 유지 관리 일정에는 없습니다. SQL data warehouse에 대 한 포털에서 *개요*, 선택 된 유지 관리 일정이 표시 됩니다.

![개요 유지 관리 일정](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

그렇지 않으면이 유지 관리는 예정된 된 이벤트가 없습니다 확인 하려면 IT 관리자에 게 확인 합니다. SQL data warehouse를 다시 시작 하려면 나온 단계를 따릅니다 [여기](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute)합니다.

## <a name="check-your-firewall-settings"></a>방화벽 설정 확인

SQL Data Warehouse는 포트 1433을 통해 통신합니다.   회사 네트워크 내에서 연결하려는 경우 1433 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서 포트 1433을 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다. 방화벽 구성에 대 한 추가 정보를 찾을 수 있습니다 [여기](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json#manage-ip-firewall-rules-using-the-azure-portal)합니다.

## <a name="check-your-vnetservice-endpoint-settings"></a>VNet/서비스 끝점 설정을 확인합니다

오류 40914 및 40615를 수신 하는 참조 [오류 설명 및 해결 방법 다음](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)합니다.

## <a name="check-for-the-latest-drivers"></a>최신 드라이버에 대 한 확인

### <a name="software"></a>소프트웨어

최신 도구를 사용 하는 SQL data warehouse에 연결할 수 있는지 확인 합니다.

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>드라이버

최신 드라이버 버전을 사용할 수 있는지 확인 합니다.  이전 드라이버 수 새로운 기능을 지원 하지 않으므로 이전 버전의 드라이버를 사용 하 여 예기치 않은 동작이 발생할 수 있습니다.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>연결 문자열 확인

연결 문자열을 올바르게 설정 되어 있는지 확인 합니다.  다음은 몇 가지 샘플입니다.  관련 된 추가 정보를 찾을 수 있습니다 [연결 문자열 여기](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings)합니다.

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

## <a name="intermittent-connection-issues"></a>일시적인 연결 문제

많은 수의 큐에 대기 중인된 요청을 사용 하 여 서버의 부하가 발생 하는 경우를 확인 합니다. 추가 리소스에 대 한 데이터 웨어하우스 크기를 조정 해야 합니다.

## <a name="common-error-messages"></a>일반적인 오류 메시지

오류 40914 및 40615를 참조 합니다 [오류 설명 및 해결 방법 다음](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615)합니다.

## <a name="still-having-connectivity-issues"></a>여전히 연결 문제가 있습니까?
만들기는 [지원 티켓](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) 엔지니어링 팀 지원할 수 있도록 합니다.
