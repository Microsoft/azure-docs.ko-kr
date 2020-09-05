---
title: '빠른 시작: 연결 - MySQL Workbench - Azure Database for MySQL'
description: 이 빠른 시작에서는 MySQL용 Azure Database에서 데이터를 연결하고 쿼리하는 데 MySQL Workbench를 사용하는 단계를 제공합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 1b1249fe7159e2a3a8d2a74d22f274a9766e7922
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185828"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql"></a>빠른 시작: MySQL Workbench를 사용하여 Azure Database for MySQL에서 데이터 연결 및 쿼리

이 빠른 시작에서는 MySQL Workbench 애플리케이션을 사용하여 MySQL용 Azure Database에 연결하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> 연결하려는 IP 주소에 [Azure Portal](./howto-manage-firewall-using-portal.md) 또는 [Azure CLI](./howto-manage-firewall-using-cli.md)를 사용하여 서버의 방화벽 규칙이 추가되었는지 확인합니다.

## <a name="install-mysql-workbench"></a>MySQL Workbench 설치
[MySQL 웹 사이트](https://dev.mysql.com/downloads/workbench/)에서 컴퓨터에 MySQL Workbench를 다운로드하여 설치합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기
MySQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 방금 만든 서버를 검색합니다(예: **mydemoserver**).

3. 서버 이름을 클릭합니다.

4. 서버의 **개요** 패널에 있는 **서버 이름**과 **서버 관리자 로그인 이름**을 기록해 둡니다. 암호를 잊어버리면 이 패널에서 암호를 재설정할 수 있습니다.
 ![MySQL용 Azure Database 서버 이름](./media/connect-php/1_server-overview-name-login.png)

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>MySQL Workbench를 사용하여 서버에 연결 
GUI 도구 MySQL Workbench를 사용하여 Azure MySQL 서버에 연결하려면

1.    컴퓨터에서 MySQL Workbench 애플리케이션을 시작합니다. 

2.    **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

![새 연결 설정](./media/connect-workbench/2-setup-new-connection.png)

| **설정** | **제안 값** | **필드 설명** |
|---|---|---|
|    연결 이름 | 데모 연결 | 이 연결에 대한 레이블을 지정합니다. |
| 연결 방법 | 표준(TCP/IP) | 표준(TCP/IP)이면 충분합니다. |
| Hostname | *서버 이름* | 이전에 Azure Database for MySQL을 만들 때 사용된 서버 이름 값을 지정합니다. 표시된 예제 서버는 mydemoserver.mysql.database.azure.com입니다. 예제에서 표시된 것과 같은 정규화된 도메인 이름(\*.mysql.database.azure.com)을 사용합니다. 서버 이름을 기억하지 못하는 경우 이전 섹션으로 이동하여 연결 정보를 가져옵니다.  |
| 포트 | 3306 | Azure Database for MySQL에 연결 시 항상 포트 3306을 사용합니다. |
| 사용자 이름 |  *서버 관리자 로그인 이름* | 이전에 Azure Database for MySQL을 만들 때 제공한 서버 관리자 로그인 사용자 이름을 입력합니다. 이 예제에서는 사용자 이름이 myadmin@mydemoserver입니다. 사용자 이름을 기억하지 못하는 경우 이전 섹션의 단계에 따라 연결 정보를 가져옵니다. 형식은 *username\@servername*입니다.
| 암호 | 사용자 암호 | **Store in Vault(자격 증명 모음에 저장)...** 단추를 클릭하여 암호를 저장합니다. |

3.   **연결 테스트**를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다. 

4.   **확인**을 클릭하여 연결을 저장합니다. 

5.   **MySQL 연결** 목록에서 서버에 해당하는 타일을 클릭하고 연결이 설정될 때까지 대기합니다.

        새 SQL 탭이 쿼리를 입력할 수 있는 빈 편집기로 열립니다.
    
        > [!NOTE]
        > 기본적으로 SSL 연결 보안이 필요하며 MySQL 서버용 Azure Database에서 적용됩니다. 일반적으로 SSL 인증서가 있는 추가 구성은 MySQL Workbench에서 서버에 연결하는 데 필요하지 않지만, MySQL Workbench와 SSL CA 인증을 바인딩하는 것이 좋습니다. 인증을 다운로드하고 바인딩하는 방법에 대한 자세한 내용은 [Azure Database for MySQL에 안전하게 연결하기 위한 사용자 애플리케이션의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.  SSL을 사용하지 않도록 설정해야 하는 경우 Azure Portal을 방문하고 연결 보안 페이지를 클릭하여 SSL 연결 적용 토글 단추를 사용하지 않도록 설정합니다.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>테이블 만들기, 데이터 삽입, 데이터 읽기, 데이터 업데이트, 데이터 삭제
1. 몇 가지 샘플 데이터를 설명하기 위해 샘플 SQL 코드를 빈 SQL 탭에 복사하여 붙여넣습니다.

    이 코드는 quickstartdb라는 빈 데이터베이스를 만든 후 inventory라는 샘플 테이블을 만듭니다. 일부 행을 삽입한 후 행을 읽습니다. 데이터가 update 문으로 변경되고 다시 행을 읽습니다. 마지막으로 행을 삭제하고 다시 행을 읽습니다.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    스크린샷은 SQL Workbench에서의 SQL 코드 예제와 실행 후 출력을 보여 줍니다.
    
    ![샘플 SQL 코드를 실행하기 위한 MySQL Workbench SQL 탭](media/connect-workbench/3-workbench-sql-tab.png)

2. 샘플 SQL 코드를 실행하려면 **SQL 파일** 탭의 도구 모음에서 번갯불 아이콘을 클릭합니다.
3. 페이지 중앙의 **결과 그리드** 섹션에서 세 개의 탭 결과를 확인합니다. 
4. 페이지 하단에서 **출력** 목록을 확인합니다. 각 명령의 상태가 표시됩니다. 

이제 MySQL Workbench를 사용하여 MySQL용 Azure Database에 연결했고 SQL 언어를 사용하여 데이터를 쿼리했습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./concepts-migrate-import-export.md)
