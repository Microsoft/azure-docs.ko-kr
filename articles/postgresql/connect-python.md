---
title: '빠른 시작: Python을 사용하여 - Azure Database for PostgreSQL - 단일 서버에 연결'
description: 이 빠른 시작에서는 Azure Database for PostgreSQL - 단일 서버의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Python 코드 샘플을 제공합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: a9303909eb98fc0ff2d7582fa7f5807a879e7958
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182685"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>빠른 시작: Python을 사용하여 Azure Database for PostgreSQL - 단일 서버의 데이터 연결 및 쿼리

이 빠른 시작에서는 macOS, Ubuntu Linux 또는 Windows에서 Python을 사용하여 Azure Database for PostgreSQL로 작업합니다. 이 빠른 시작에서는 데이터베이스에 연결하고 SQL 문을 사용하여 데이터를 쿼리, 삽입, 업데이트 및 삭제하는 방법을 보여 줍니다. 이 문서에서는 사용자가 Python에 익숙하지만 Azure Database for PostgreSQL 작업에 익숙하지 않다고 가정합니다.

> [!TIP]
> PostgreSQL을 사용하여 Django 애플리케이션을 빌드하려는 경우 자습서를 체크 아웃하고 [PostgreSQL 자습서를 사용하여 Django 웹앱을 배포](../app-service/containers/tutorial-python-postgresql-app.md)합니다.


## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [평가판 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- [빠른 시작: Azure Portal에서 Azure Database for PostgreSQL 서버 만들기](quickstart-create-server-database-portal.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Database for PostgreSQL 만들기](quickstart-create-server-database-azure-cli.md)의 단계를 사용하여 만든 Azure Database for PostgreSQL - 단일 서버
  
- [Python](https://www.python.org/downloads/) 2.7.9+ 또는 3.4+.
  
- 최신 [pip](https://pip.pypa.io/en/stable/installing/) 패키지 설치 관리자.

## <a name="install-the-python-libraries-for-postgresql"></a>PostgreSQL용 Python 라이브러리 설치
[psycopg2](https://pypi.python.org/pypi/psycopg2/) 모듈은 PostgreSQL 데이터베이스를 연결 및 쿼리할 수 있도록 지원하며, Linux, macOS 또는 Windows [휠](https://pythonwheels.com/) 패키지로 사용할 수 있습니다. 모든 종속 관계를 포함하는 모듈의 이진 버전을 설치합니다. `psycopg2` 설치 및 요구 사항에 대 한 자세한 내용은 [설치](http://initd.org/psycopg/docs/install.html)를 참조하세요. 

`psycopg2`를 설치하려면 터미널 또는 명령 프롬프트를 열고 `pip install psycopg2` 명령을 실행합니다.

## <a name="get-database-connection-information"></a>데이터베이스 연결 정보 가져오기
Azure Database for PostgreSQL 데이터베이스에 연결하려면 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다. Azure Portal에서 이 정보를 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Database for PostgreSQL 서버 이름을 선택합니다. 
1. 서버의 **개요** 페이지에서 정규화된 **서버 이름** 및 **관리자 사용자 이름**을 복사합니다. 정규화된 **서버 이름**은 항상 *\<my-server-name>my-server-name>.postgres.database.azure.com* 형식을 가지며, **관리 사용자 이름**은 항상 *\<my-admin-username>@\<my-server-name>* 형식을 갖습니다. 
   
   관리자 암호도 필요합니다. 이 암호를 잊어버린 경우 이 페이지에서 다시 설정할 수 있습니다. 
   
   ![Azure Database for PostgreSQL 서버 이름](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Python 예제를 실행하는 방법

이 문서의 각 코드 예제는 다음과 같습니다.

1. 텍스트 편집기에서 새 파일을 만듭니다. 
   
1. 파일에 코드 예제를 추가합니다. 코드에서 다음과 같이 바꿉니다.
   - `<server-name>` 및 `<admin-username>`을 Azure Portal에서 복사한 값으로 바꿉니다.
   - `<admin-password>`를 서버 암호로 바꿉니다.
   - `<database-name>`을 Azure Database for PostgreSQL 데이터베이스의 이름으로 바꿉니다. *postgres*라는 기본 데이터베이스는 서버를 만들 때 자동으로 생성되었습니다. SQL 명령을 사용하여 해당 데이터베이스의 이름을 바꾸거나 새 데이터베이스를 만들 수 있습니다. 
   
1. 프로젝트 폴더의 파일을 *.py* 확장명을 사용하여 저장합니다(예: *postgres-insert.py*). Windows의 경우 파일을 저장할 때 UTF-8 인코딩을 선택해야 합니다. 
   
1. 이 파일을 실행하려면 명령줄 인터페이스에서 프로젝트 폴더로 변경하고 `python` 다음에 파일 이름을 입력합니다(예: `python postgres-insert.py`).

## <a name="create-a-table-and-insert-data"></a>테이블 만들기 및 데이터 삽입
다음 코드 예제에서는 [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) 함수를 사용하여 Azure Database for PostgreSQL 데이터베이스에 연결하고 SQL **INSERT** 문을 사용하여 데이터를 로드합니다. [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) 함수는 데이터베이스에 대해 SQL 쿼리를 실행합니다. 

```Python
import psycopg2

# Update connection string information 
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

코드가 성공적으로 실행되면 다음과 같은 출력이 생성됩니다.

![명령줄 출력](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>데이터 읽기
다음 코드 예제에서는 Azure Database for PostgreSQL 데이터베이스에 연결하고 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute)와 SQL **SELECT**를 사용하여 데이터를 읽습니다. 이 함수는 쿼리를 허용하며, [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)을 사용하여 반복할 결과 세트를 반환합니다. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>데이터 업데이트
다음 코드 예제에서는 Azure Database for PostgreSQL 데이터베이스에 연결하고 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute)와 SQL **UPDATE**를 사용하여 데이터를 업데이트합니다. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>데이터 삭제
다음 코드 예제에서는 Azure Database for PostgreSQL 데이터베이스에 연결하고 [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute)와 SQL **DELETE** 문을 사용하여 이전에 삽입한 인벤토리 항목을 삭제합니다. 

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string) 
print("Connection established")

cursor = conn.cursor()

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
