---
title: 드라이버 및 도구 호환성-Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구를 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8d222eca76f020703f89dd1bdd5848cec4bc52ca
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006536"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Azure Database for MySQL과 호환되는 MySQL 드라이버 및 관리 도구
이 문서에서는 Azure Database for MySQL과 호환되는 드라이버 및 관리 도구를 설명합니다.

## <a name="mysql-drivers"></a>MySQL 드라이버
Azure Database for MySQL은 전 세계에서 가장 인기 있는 커뮤니티 버전의 MySQL 데이터베이스를 사용합니다. 따라서 다양한 프로그래밍 언어 및 드라이버와 호환됩니다. 세 가지 최신 버전의 MySQL 드라이버를 지원하는 것이 목표이며, MySQL 드라이버의 기능 및 사용 편의를 개선하기 위해 지속적으로 오픈 소스 커뮤니티의 작성자와 협력하고 있습니다. 다음 표에는 테스트를 통해 Azure Database for MySQL 5.6 및 5.7과 호환되는 것으로 확인된 드라이버 목록이 정리되어 있습니다.

| **프로그래밍 언어** | **드라이버** | **연결** | **호환 되는 버전** | **호환되지 않는 버전** | **참고** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqli | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | PHP 7.0과 SSL MySQLi 연결의 경우 연결 문자열에 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT를 추가합니다. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 집합: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 옵션을 false로 설정합니다.|
| .NET | .NET 용 Async MySQL 커넥터 | https://github.com/mysql-net/MySqlConnector <br> [Nuget의 설치 패키지](https://www.nuget.org/packages/MySqlConnector/) | 0.27 이상 | 0.26.5 이하 | |
| .NET | MySQL Connector/NET | https://github.com/mysql/mysql-connector-net | 6.6.3, 7.0, 8.0 |  | 인코딩 버그로 인해 UTF8이 아닌 Windows 시스템 중 일부에서 연결이 실패할 수 있습니다. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> NPM의 설치 패키지:<br> NPM에서 `npm install mysql` 실행 | 2.15 | 2.14.1 이하 | |
| Node.js | 노드-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Go MySQL 드라이버 | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 이하 | `allowNativePasswords=true`1.3 버전에 대 한 연결 문자열에서를 사용 합니다. 버전 1.4에는 수정 프로그램이 포함 되어 있으므로 `allowNativePasswords=true` 더 이상 필요 하지 않습니다. |
| Python | MySQL 커넥터/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2, MySQL 8.0에서 8.0.16 + 사용  | 1.2.2 이하 | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0부터, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (web2py의 회귀) | |
| Java | MariaDB 커넥터/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 이하 | | 
| Java | MySQL 커넥터/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, MySQL 8.0에 8.0.17 + 사용 | 5.1.20 및 아래 | |
| C | MySQL 커넥터/C (C #) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | MySQL 커넥터/ODBC (myodbc) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | MySQL 커넥터/c + + | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 및 아래 | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | mysql-swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | vapor/mysql | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>관리 도구
호환성 장점은 데이터베이스 관리 도구까지 확장됩니다. 데이터베이스 조작이 사용자 권한의 범위 내에서 이루어지는 한, 기존 도구가 Azure Database for MySQL과 계속 호환됩니다. 다음 표에는 테스트를 통해 Azure Database for MySQL 5.6 및 5.7과 호환되는 것으로 확인된 세 가지 일반적인 데이터베이스 관리 도구가 정리되어 있습니다.

|                                     | **MySQL Workbench 6.x 이상** | **Navicat 12** | **PHPMyAdmin 4.x 이상** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------|
| **만들기, 업데이트, 읽기, 쓰기, 삭제** | X | X | X |
| **SSL 연결** | X | X | X |
| **SQL 쿼리 자동 완성** | X | X |  |
| **데이터 가져오기 및 내보내기** | X | X | X |
| **여러 형식으로 내보내기** | X | X | X |
| **백업 및 복원** |  | X |  |
| **서버 매개 변수 표시** | X | X | X |
| **클라이언트 연결 표시** | X | X | X |

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL에 대한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)