---
title: '빠른 시작: Ruby를 사용하여 연결 - Azure Database for MySQL'
description: 이 빠른 시작에서는 MySQL용 Azure Database에서 데이터를 연결하고 쿼리하는 데 사용할 수 있는 몇 가지 Ruby 코드 샘플을 제공합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 79f3efad9ba5f6c0378f8b093a2f375275767659
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185845"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>빠른 시작: Ruby를 사용하여 Azure Database for MySQL에서 데이터 연결 및 쿼리

이 빠른 시작에서는 Windows, Ubuntu Linux 및 Mac 플랫폼에서 [Ruby](https://www.ruby-lang.org) 애플리케이션 및 [mysql2](https://rubygems.org/gems/mysql2) gem을 사용하여 MySQL용 Azure Database에 연결하는 방법을 보여 줍니다. SQL 문을 사용하여 데이터베이스의 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 항목에서는 Ruby를 사용하여 개발하는 데 익숙하고 MySQL용 Azure Database를 처음 사용한다고 가정합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에서는 다음과 같은 가이드 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.
- [Azure Portal을 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI를 사용한 MySQL용 Azure Database 서버 만들기](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> 연결하려는 IP 주소에 [Azure Portal](./howto-manage-firewall-using-portal.md) 또는 [Azure CLI](./howto-manage-firewall-using-cli.md)를 사용하여 서버의 방화벽 규칙이 추가되었는지 확인합니다.

## <a name="install-ruby"></a>Ruby 설치
Ruby, Gem 및 MySQL2 라이브러리를 자신의 컴퓨터에 설치합니다. 

### <a name="windows"></a>Windows
1. [Ruby](https://rubyinstaller.org/downloads/) 버전 2.3을 다운로드하고 설치합니다.
2. 시작 메뉴에서 새 명령 프롬프트(cmd)를 시작합니다.
3. 디렉터리를 Ruby 버전 2.3에 대한 디렉터리로 변경합니다. `cd c:\Ruby23-x64\bin`
4. `ruby -v` 명령을 실행하여 설치된 버전을 확인함으로써 Ruby 설치를 테스트합니다.
5. `gem -v` 명령을 실행하여 설치된 버전을 확인함으로써 Gem 설치를 테스트합니다.
6. `gem install mysql2` 명령을 실행하여 Gem을 사용하는 Ruby용 Mysql2 모듈을 빌드합니다.

### <a name="macos"></a>MacOS
1. `brew install ruby` 명령을 실행하여 Homebrew로 Ruby를 설치합니다. 자세한 설치 옵션을 보려면 Ruby [설치 설명서](https://www.ruby-lang.org/en/documentation/installation/#homebrew)를 참조하세요.
2. `ruby -v` 명령을 실행하여 설치된 버전을 확인함으로써 Ruby 설치를 테스트합니다.
3. `gem -v` 명령을 실행하여 설치된 버전을 확인함으로써 Gem 설치를 테스트합니다.
4. `gem install mysql2` 명령을 실행하여 Gem을 사용하는 Ruby용 Mysql2 모듈을 빌드합니다.

### <a name="linux-ubuntu"></a>Linux(Ubuntu)
1. `sudo apt-get install ruby-full` 명령을 실행하여 Ruby를 설치합니다. 자세한 설치 옵션을 보려면 Ruby [설치 설명서](https://www.ruby-lang.org/en/documentation/installation/)를 참조하세요.
2. `ruby -v` 명령을 실행하여 설치된 버전을 확인함으로써 Ruby 설치를 테스트합니다.
3. `sudo gem update --system` 명령을 실행하여 Gem에 대한 최신 업데이트를 설치합니다.
4. `gem -v` 명령을 실행하여 설치된 버전을 확인함으로써 Gem 설치를 테스트합니다.
5. `sudo apt-get install build-essential` 명령을 실행하여 gcc, make 및 기타 빌드 도구를 설치합니다.
6. `sudo apt-get install libmysqlclient-dev` 명령을 실행하여 MySQL 클라이언트 개발자 라이브러리를 설치합니다.
7. `sudo gem install mysql2` 명령을 실행하여 Gem을 사용하는 Ruby용 mysql2 모듈을 빌드합니다.

## <a name="get-connection-information"></a>연결 정보 가져오기
MySQL용 Azure Database에 연결하는 데 필요한 연결 정보를 가져옵니다. 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 방금 만든 서버를 검색합니다(예: **mydemoserver**).
3. 서버 이름을 클릭합니다.
4. 서버의 **개요** 패널에 있는 **서버 이름**과 **서버 관리자 로그인 이름**을 기록해 둡니다. 암호를 잊어버리면 이 패널에서 암호를 재설정할 수 있습니다.
 ![MySQL용 Azure Database 서버 이름](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>Ruby 코드 실행 
1. 아래 섹션에서 Ruby 코드를 텍스트 파일에 붙여넣고 .rb 파일 확장명이 포함된 프로젝트 폴더에 저장합니다(예: `C:\rubymysql\createtable.rb` 또는 `/home/username/rubymysql/createtable.rb`).
2. 코드를 실행하려면 명령 프롬프트 또는 Bash 셸을 시작합니다. 디렉터리를 프로젝트 폴더로 변경합니다(예: `cd rubymysql`).
3. 그런 다음, 애플리케이션을 실행하려면 Ruby 명령 다음에 파일 이름을 입력합니다(예: `ruby createtable.rb`).
4. Windows OS에서 Ruby 애플리케이션이 경로 환경 변수에 없는 경우 전체 경로를 사용하여 노드 애플리케이션을 시작해야 할 수도 있습니다(예: `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`).

## <a name="connect-and-create-a-table"></a>테이블 연결 및 생성
**CREATE TABLE** SQL 문 다음에 테이블에 행을 추가하는 **INSERT INTO** SQL 문을 사용하여 테이블을 연결 및 생성하려면 다음 코드를 사용합니다.

이 코드는 [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) 클래스 .new() 메서드를 사용하여 MySQL용 Azure 데이터베이스에 연결합니다. 그런 다음 [query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 메서드를 여러 번 호출하여 DROP, CREATE TABLE 및 INSERT INTO 명령을 실행합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `username` 및 `password` 문자열은 원하는 값으로 바꾸세요. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>데이터 읽기
**SELECT** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

이 코드는 [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) class.new() 메서드를 사용하여 MySQL용 Azure Database에 연결합니다. 그런 다음 [query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 메서드를 호출하여 SELECT 명령을 실행합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `username` 및 `password` 문자열은 원하는 값으로 바꾸세요. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>데이터 업데이트
**UPDATE** SQL 문을 사용하여 데이터를 연결하고 업데이트하려면 다음 코드를 사용하세요.

이 코드는 [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) 클래스 .new() 메서드를 사용하여 MySQL용 Azure 데이터베이스에 연결합니다. 그런 다음 [query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 메서드를 호출하여 UPDATE 명령을 실행합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `username` 및 `password` 문자열은 원하는 값으로 바꾸세요. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>데이터 삭제
**DELETE** SQL 문을 사용하여 데이터를 연결하고 읽으려면 다음 코드를 사용하세요. 

이 코드는 [mysql2::client](https://www.rubydoc.info/gems/mysql2/0.4.8) 클래스 .new() 메서드를 사용하여 MySQL용 Azure 데이터베이스에 연결합니다. 그런 다음 [query()](https://www.rubydoc.info/gems/mysql2/0.4.8#Usage) 메서드를 호출하여 DELETE 명령을 실행합니다. 그런 다음 종료하기 전에 [close()](https://www.rubydoc.info/gems/mysql2/0.4.8/Mysql2/Client#close-instance_method) 메서드를 호출하여 연결을 닫습니다.

`host`, `database`, `username` 및 `password` 문자열은 원하는 값으로 바꾸세요. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./concepts-migrate-import-export.md)
