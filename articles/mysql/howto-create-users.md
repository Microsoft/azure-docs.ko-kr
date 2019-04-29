---
title: Azure Database for MySQL 서버에서 사용자 만들기
description: 이 문서에서는 Azure Database for MySQL 서버와 상호 작용할 새로운 사용자 계정을 만드는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 4cd2305ed3d7f88f6c3825d8f7cdb5d81f9a0f6b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460187"
---
# <a name="create-users-in-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버에서 사용자 만들기 
이 문서에서는 Azure Database for MySQL 서버에서 사용자를 만드는 방법을 설명합니다.

Azure Database for MySQL을 처음으로 만들면 서버 관리자 로그인 사용자 이름과 암호가 제공됩니다. 자세한 내용은 [빠른 시작](quickstart-create-mysql-server-database-using-azure-portal.md)을 참조하세요. Azure Portal에서 서버 관리자 로그인 사용자 이름을 찾을 수 있습니다.

서버 관리 사용자는 서버에 대한 다음 권한을 가집니다. SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

Azure Database for MySQL 서버가 생성되면 첫 번째 서버 관리자 계정을 사용하여 추가 사용자를 만들고 관리 사용자 권한을 부여할 수 있습니다. 또한 서버 관리자 계정은 개별 데이터베이스 스키마에 액세스할 수 있는 권한의 사용자가 만드는 데 사용할 수 있습니다.

## <a name="how-to-create-additional-admin-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 추가 관리 사용자를 만드는 방법
1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다. 

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL Workbench, mysql.exe, HeidiSQL 또는 그 외의 선호하는 클라이언트 도구를 사용하면 됩니다. 
   연결 방법을 잘 모르는 경우 [MySQL Workbench를 사용하여 연결하고 데이터 쿼리](./connect-workbench.md)를 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. `new_master_user` 자리 표시자 값을 새로운 사용자 이름으로 바꿉니다. 이 구문은 모든 데이터베이스 스키마(*.*)에 나열된 권한을 사용자 이름(이 예에서는 new_master_user)에 부여합니다. 

   ```sql
   CREATE USER 'new_master_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER ON *.* TO 'new_master_user'@'%' WITH GRANT OPTION; 
   
   FLUSH PRIVILEGES;
   ```

4. 권한 부여 확인 
   ```sql
   USE sys;
   
   SHOW GRANTS FOR 'new_master_user'@'%';
   ```

## <a name="how-to-create-database-users-in-azure-database-for-mysql"></a>Azure Database for MySQL에서 데이터베이스 사용자를 만드는 방법

1. 연결 정보 및 관리 사용자 이름을 가져옵니다.
   데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. Azure Portal의 서버 **개요** 페이지 또는 **속성** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다. 

2. 관리자 계정 및 암호를 사용하여 데이터베이스 서버에 연결합니다. MySQL Workbench, mysql.exe, HeidiSQL 또는 그 외의 선호하는 클라이언트 도구를 사용하면 됩니다. 
   연결 방법을 잘 모르는 경우 [MySQL Workbench를 사용하여 연결하고 데이터 쿼리](./connect-workbench.md)를 참조하세요.

3. 다음 SQL 코드를 편집하고 실행합니다. `db_user` 자리 표시자 값을 원하는 새 사용자 이름으로 바꾸고, `testdb` 자리 표시자 값을 자신의 데이터베이스 이름으로 바꿉니다.

   이 sql 코드 구문은 예제를 제공할 목적으로 testdb라는 새 데이터베이스를 만듭니다. 그런 다음 MySQL 서비스에 새로운 사용자를 만들고 해당 사용자에 대한 새 데이터베이스 스키마(testdb.\*)에 모든 권한을 부여합니다. 

   ```sql
   CREATE DATABASE testdb;
   
   CREATE USER 'db_user'@'%' IDENTIFIED BY 'StrongPassword!';
   
   GRANT ALL PRIVILEGES ON testdb . * TO 'db_user'@'%';
   
   FLUSH PRIVILEGES;
   ```

4. 데이터베이스 내에서 권한 부여를 확인합니다.
   ```sql
   USE testdb;
   
   SHOW GRANTS FOR 'db_user'@'%';
   ```

5. 서버에 로그인하고, 지정된 데이터베이스를 지정하고, 새로운 사용자 이름 및 암호를 사용합니다. 이 예에서는 mysql 명령줄을 보여 줍니다. 이 명령을 사용하면 사용자 이름에 대한 암호를 묻는 메시지가 표시됩니다. 사용자 고유의 서버 이름, 데이터베이스 이름 및 사용자 이름으로 바꿉니다.

   ```azurecli-interactive
   mysql --host mydemoserver.mysql.database.azure.com --database testdb --user db_user@mydemoserver -p
   ```

## <a name="next-steps"></a>다음 단계
새 사용자의 머신 IP 주소에 대한 방화벽을 열고 연결하도록 설정합니다. [Azure Portal](howto-manage-firewall-using-portal.md) 또는 [Azure CLI를 사용하여 Azure Database for MySQL 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-cli.md).

사용자 계정 관리에 대한 자세한 내용은 [사용자 계정 관리](https://dev.mysql.com/doc/refman/5.7/en/user-account-management.html), [GRANT 구문](https://dev.mysql.com/doc/refman/5.7/en/grant.html) 및 [권한](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)에 대한 MySQL 제품 설명서를 참조하세요.
