---
title: '빠른 시작: Ruby를 사용하여 - Azure Database for PostgreSQL - 단일 서버에 연결'
description: 이 빠른 시작에서는 Azure Database for PostgreSQL - 단일 서버의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Ruby 코드 샘플을 제공합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/6/2019
ms.openlocfilehash: 98814060fb9ae669fc4e9a90fcda6319bbfaeacf
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182547"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>빠른 시작: Ruby를 사용하여 Azure Database for PostgreSQL에서 데이터 연결 및 쿼리 - 단일 서버

이 빠른 시작에서는 [Ruby](https://www.ruby-lang.org) 애플리케이션을 사용하여 Azure Database for PostgreSQL에 연결하는 방법을 보여줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서의 단계에서는 개발자가 Ruby를 사용하여 개발하는 것에 익숙하고 Azure Database for PostgreSQL 작업에 익숙하지 않다고 가정합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [DB 만들기 - 포털](quickstart-create-server-database-portal.md)
- [DB 만들기 - Azure CLI](quickstart-create-server-database-azure-cli.md)

또한 다음 모듈도 설치되어 있어야 합니다.
- [Ruby](https://www.ruby-lang.org/en/downloads/)
- Ruby pg, Ruby용 PostgreSQL 모듈

## <a name="get-connection-information"></a>연결 정보 가져오기
PostgreSQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 방금 만든 서버를 검색합니다(예: **mydemoserver**).
3. 서버 이름을 클릭합니다.
4. 서버의 **개요** 패널에 있는 **서버 이름**과 **서버 관리자 로그인 이름**을 기록해 둡니다. 암호를 잊어버리면 이 패널에서 암호를 재설정할 수 있습니다.
 ![Azure Database for PostgreSQL 서버 이름](./media/connect-ruby/1-connection-string.png)

> [!NOTE]
> Azure Postgres 사용자 이름에서 `@` 기호는 모든 연결 문자열에서 `%40`으로 url 인코딩되었습니다. 

## <a name="connect-and-create-a-table"></a>테이블 연결 및 생성
**CREATE TABLE** SQL 문 다음에 테이블에 행을 추가하는 **INSERT INTO** SQL 문을 사용하여 테이블을 연결 및 생성하려면 다음 코드를 사용합니다.

이 코드는 [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) 개체와 [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) 생성자를 사용하여 PostgreSQL용 Azure Database에 연결합니다. 그런 다음 [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) 메서드를 호출하여 DROP, CREATE TABLE 및 INSERT INTO 명령을 실행합니다. 이 코드는 [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) 클래스를 사용하여 오류를 검사합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `user` 및 `password` 문자열은 원하는 값으로 바꾸세요. 


```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database'

    # Drop previous table of same name if one exists
    connection.exec('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    connection.exec('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    connection.exec("INSERT INTO inventory VALUES(1, 'banana', 150)")
    connection.exec("INSERT INTO inventory VALUES(2, 'orange', 154)")
    connection.exec("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="read-data"></a>데이터 읽기
**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

이 코드는 [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) 개체와 [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) 생성자를 사용하여 PostgreSQL용 Azure Database에 연결합니다. 그런 다음 [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) 메서드를 호출하여 SELECT 명령을 실행하고 결과를 결과 집합에 보관합니다. 결과 집합 컬렉션이 `resultSet.each do` 루프를 사용하여 반복되고, 현재 행 값이 `row` 변수에 보관됩니다. 이 코드는 [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) 클래스를 사용하여 오류를 검사합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `user` 및 `password` 문자열은 원하는 값으로 바꾸세요. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :database => dbname, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    resultSet = connection.exec('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="update-data"></a>데이터 업데이트
**UPDATE** SQL 문을 사용하여 데이터를 연결하고 업데이트하려면 다음 코드를 사용하세요.

이 코드는 [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) 개체와 [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) 생성자를 사용하여 PostgreSQL용 Azure Database에 연결합니다. 그런 다음 [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) 메서드를 호출하여 UPDATE 명령을 실행합니다. 이 코드는 [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) 클래스를 사용하여 오류를 검사합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `user` 및 `password` 문자열은 원하는 값으로 바꾸세요. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
    puts 'Updated 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```


## <a name="delete-data"></a>데이터 삭제
**DELETE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

이 코드는 [PG::Connection](https://www.rubydoc.info/gems/pg/PG/Connection) 개체와 [new()](https://www.rubydoc.info/gems/pg/PG%2FConnection:initialize) 생성자를 사용하여 PostgreSQL용 Azure Database에 연결합니다. 그런 다음 [exec()](https://www.rubydoc.info/gems/pg/PG/Connection#exec-instance_method) 메서드를 호출하여 UPDATE 명령을 실행합니다. 이 코드는 [PG::Error](https://www.rubydoc.info/gems/pg/PG/Error) 클래스를 사용하여 오류를 검사합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/pg/PG/Connection#lo_close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `user` 및 `password` 문자열은 원하는 값으로 바꾸세요. 

```ruby
require 'pg'

begin
    # Initialize connection variables.
    host = String('mydemoserver.postgres.database.azure.com')
    database = String('postgres')
    user = String('mylogin%40mydemoserver')
    password = String('<server_admin_password>')

    # Initialize connection object.
    connection = PG::Connection.new(:host => host, :user => user, :dbname => database, :port => '5432', :password => password)
    puts 'Successfully created connection to database.'

    # Modify some data in table.
    connection.exec('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row of data.'

rescue PG::Error => e
    puts e.message 
    
ensure
    connection.close if connection
end
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
