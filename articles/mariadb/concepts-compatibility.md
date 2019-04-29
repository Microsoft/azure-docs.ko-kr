---
title: Azure Database for MariaDB 드라이버 및 관리 도구 호환성
description: 이 문서에서는 MariaDB 드라이버 및 Azure Database for MariaDB와 호환 되는 관리 도구를 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386818"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB 드라이버 및 관리 도구를 Azure Database for MariaDB 호환

이 문서에는 드라이버 및 Azure Database for MariaDB와 호환 되는 관리 도구를 설명 합니다.

## <a name="mariadb-drivers"></a>MariaDB 드라이버

Azure Database for MariaDB MariaDB 서버가의 community edition을 사용합니다. 따라서 다양한 프로그래밍 언어 및 드라이버와 호환됩니다. MariaDB의 API 및 프로토콜에는 MySQL에서 사용 되는와 호환 됩니다. 즉, MySQL을 사용 하는 커넥터 MariaDB를 사용 하 여 작동 합니다.

목표는 세 가지 가장 최근 버전 MariaDB 드라이버를 지원 하 고 지속적으로 기능 및 MariaDB 드라이버의 유용성을 개선 하기 위해 오픈 소스 커뮤니티의 작성자를 사용 하 여 드라이브 계속 합니다. 테스트 및 Azure Database for MariaDB 10.2와 호환 되도록 발견 된 드라이버 목록은 표에 제공 됩니다.

**드라이버** | **연결** | **호환되는 버전** | **호환되지 않는 버전** | **참고 사항**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5.3 | PHP 7.0과 SSL MySQLi 연결의 경우 연결 문자열에 MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT를 추가합니다. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO 집합: ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` 옵션을 false로 설정합니다.
.NET | [GitHub의 MySqlConnector](https://github.com/mysql-net/MySqlConnector) <br> [Nuget의 설치 패키지](https://www.nuget.org/packages/MySqlConnector/) | 0.27 이상 | 0.26.5 이하 |
MySQL Connector/NET | [MySQL Connector/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | 인코딩 버그로 인해 UTF8이 아닌 Windows 시스템 중 일부에서 연결이 실패할 수 있습니다.
Node.js |  [Github의 MySQLjs](https://github.com/mysqljs/mysql/) <br> NPM의 설치 패키지:<br> NPM에서 `npm install mysql` 실행 | 2.15 | 2.14.1 이하
이동 | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 이하 | 사용 하 여 `allowNativePasswords=true` 버전 1.3에 대 한 연결 문자열에 있습니다. 버전 1.4 수정 사항을 포함 하 고 `allowNativePasswords=true` 필요 하지 않습니다.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 이하 |
자바 | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 이하 |

## <a name="management-tools"></a>관리 도구

호환성 장점은 데이터베이스 관리 도구까지 확장됩니다. 기존 도구와 데이터베이스 조작이 사용자 권한의 범위 내에서 Azure Database for MariaDB, 작업할 계속 해야 합니다. 테스트 및 Azure Database for MariaDB 10.2와 호환 되도록 발견 된 세 가지 일반 데이터베이스 관리 도구는 다음 표에 나열 됩니다.

| | **MySQL Workbench 6.x 이상** | **Navicat 12** | **PHPMyAdmin 4.x 이상**
---|---|---|---
만들기, 업데이트, 읽기, 쓰기, 삭제 | X | X | X
SSL 연결 | X | X | X
SQL 쿼리 자동 완성 | X | X |
데이터 가져오기 및 내보내기 | X | X | X
여러 형식으로 내보내기 | X | X | X
Backup 및 Restore 메서드 |  | X |
서버 매개 변수 표시 | X | X | X
클라이언트 연결 표시 | X | X | X

## <a name="next-steps"></a>다음 단계

- [Azure Database for MariaDB에 대한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)