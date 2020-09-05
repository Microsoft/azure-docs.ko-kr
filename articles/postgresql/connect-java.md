---
title: Azure Database for PostgreSQL에서 Java 및 JDBC 사용
description: Azure Database for PostgreSQL에서 Java 및 JDBC를 사용하는 방법을 알아봅니다.
author: jdubois
ms.author: judubois
ms.service: postgresql
ms.custom: mvc, devcenter
ms.topic: quickstart
ms.devlang: java
ms.date: 08/17/2020
ms.openlocfilehash: 66a3b4919903f739ed5afef0a02b501f00ff248f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545440"
---
# <a name="use-java-and-jdbc-with-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 Java 및 JDBC 사용

이 항목에서는 Java 및 [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity)를 사용하여 [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/)에 정보를 저장하고 검색하는 애플리케이션 샘플을 만드는 방법을 보여줍니다.

JDBC는 기존 관계형 데이터베이스에 연결하는 표준 Java API입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 계정. 계정이 없으면 [체험 계정을 얻습니다](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) 또는 [Azure CLI](/cli/azure/install-azure-cli). 자동으로 로그인되고 필요한 모든 도구에 액세스할 수 있는 Azure Cloud Shell을 권장합니다.
- 지원되는 [Java Development Kit](https://aka.ms/azure-jdks) 버전 8(Azure Cloud Shell에 포함됨)입니다.
- [Apache Maven](https://maven.apache.org/) 빌드 도구.

## <a name="prepare-the-working-environment"></a>작업 환경 준비

환경 변수를 사용하여 입력 실수를 제한하고 특정 요구 사항에 맞게 다음 구성을 보다 쉽게 사용자 지정할 수 있도록 합니다.

다음 명령을 사용하여 이러한 환경 변수를 설정합니다.

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=demo
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

자리 표시자를 이 문서 전체에서 사용되는 다음 값으로 바꿉니다.

- `<YOUR_DATABASE_NAME>`: PostgreSQL 서버의 이름입니다. Azure에서 고유해야 합니다.
- `<YOUR_AZURE_REGION>`: 사용할 Azure 지역. 기본적으로 `eastus`를 사용할 수 있지만 거주지와 더 가까운 지역을 구성하는 것이 좋습니다. `az account list-locations`를 입력하면 사용 가능한 지역의 전체 목록을 나열할 수 있습니다.
- `<YOUR_POSTGRESQL_PASSWORD>`: PostgreSQL 데이터베이스 서버의 암호입니다. 암호의 길이는 8자 이상이어야 합니다. 다음 범주 중 세 가지 범주의 문자여야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).
- `<YOUR_LOCAL_IP_ADDRESS>`: Java 애플리케이션을 실행할 로컬 컴퓨터의 IP 주소. 이를 확인하는 간편한 방법 중 하나는 브라우저에서 [whatismyip.akamai.com](http://whatismyip.akamai.com/)으로 이동하는 것입니다.

다음으로, 다음 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> `jq` 유틸리티를 사용하여 JSON 데이터를 표시하고 더 읽기 쉽게 만듭니다. 이 유틸리티는 [Azure Cloud Shell](https://shell.azure.com/)에 기본적으로 설치됩니다. 이 유틸리티가 마음에 들지 않을 경우 여기서 사용하게 될 모든 명령의 `| jq` 부분을 안전하게 제거하면 됩니다.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Azure Database for PostgreSQL 인스턴스 만들기

먼저 관리형 PostgreSQL 서버를 만듭니다.

> [!NOTE]
> PostgreSQL 서버 만들기에 관한 자세한 정보는 [Azure portal을 사용하여 Azure Database for PostgreSQL 서버 만들기](/azure/postgresql/quickstart-create-server-database-portal)에서 확인할 수 있습니다.

[Azure Cloud Shell](https://shell.azure.com/)에서 다음 명령을 실행합니다.

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
  	| jq
```

이 명령은 작은 PostgreSQL 서버를 만듭니다.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>PostgreSQL 서버에 대한 방화벽 규칙 구성

Azure Database for PostgreSQL 인스턴스는 기본적으로 보호됩니다. 들어오는 연결을 허용하지 않는 방화벽이 있습니다. 데이터베이스를 사용하려면 로컬 IP 주소에서 데이터베이스 서버에 액세스할 수 있도록 하는 방화벽 규칙을 추가해야 합니다.

이 문서의 시작 부분에서 로컬 IP 주소를 구성했으므로 다음 명령을 실행하여 서버의 방화벽을 열 수 있습니다.

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-postgresql-database"></a>PostgreSQL 데이터베이스 구성

이전에 만든 PostgreSQL 서버가 비어 있습니다. Java 애플리케이션에서 사용할 수 있는 데이터베이스가 없습니다. 다음 명령을 사용하여 `demo`라는 새 데이터베이스를 만듭니다.

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>새 Java 프로젝트 만들기

선호하는 IDE를 사용하여 새 Java 프로젝트를 만들고 해당 루트 디렉터리에 `pom.xml` 파일을 추가합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

이 파일은 다음을 사용하도록 프로젝트를 구성하는 [Apache Maven](https://maven.apache.org/)입니다.

- Java 8
- Java용 최신 PostgreSQL 드라이버

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 연결할 구성 파일 준비

*src/main/resources/application.properties* 파일을 만들고 다음을 추가합니다.

```properties
url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo?ssl=true&sslmode=require
user=demo@$AZ_DATABASE_NAME
password=$AZ_POSTGRESQL_PASSWORD
```

- 두 개의 `$AZ_DATABASE_NAME` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.
- `$AZ_POSTGRESQL_PASSWORD` 변수를 이 문서의 시작 부분에서 구성한 값으로 바꿉니다.

> [!NOTE]
> `?ssl=true&sslmode=require`를 `url` 구성 속성에 추가하여 데이터베이스에 연결할 때 TLS([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security))를 사용하도록 JDBC 드라이버에 지시합니다. Azure Database for PostgreSQL과 함께 TLS를 사용하는 것이 필수이며 좋은 보안 사례입니다.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>데이터베이스 스키마를 생성하는 SQL 파일 만들기

데이터베이스 스키마를 만들기 위해 *src/main/resources/`schema.sql`* 파일을 사용합니다. 다음 내용이 포함된 해당 파일을 만듭니다.

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>애플리케이션 코딩

### <a name="connect-to-the-database"></a>데이터베이스에 연결

다음으로, JDBC를 사용하여 PostgreSQL 서버에서 데이터를 저장하고 검색하는 Java 코드를 추가합니다.

다음을 포함하는 *src/main/java/DemoApplication.java* 파일을 만듭니다.

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

이 Java 코드는 PostgreSQL 서버에 연결하고 데이터를 저장하는 스키마를 만들기 위해 이전에 만든 *application.properties* 및 *schema.sql* 파일을 사용합니다.

이 파일에서는 데이터를 삽입, 읽기, 업데이트 및 삭제하는 메서드를 주석으로 처리하는 것을 확인할 수 있습니다. 이 문서의 나머지 부분에서 이러한 메서드를 코딩하고 각 메서드를 주석으로 처리하지 않을 수 있습니다.

> [!NOTE]
> 데이터베이스 자격 증명은 *application.properties* 파일의 *사용자* 및 *암호* 속성에 저장됩니다. 이러한 자격 증명은 속성 파일이 인수로 전달되므로 `DriverManager.getConnection(properties.getProperty("url"), properties);`를 실행할 때 사용됩니다.

이제 즐겨찾는 도구를 사용하여 이 기본 클래스를 실행할 수 있습니다.

- IDE를 사용하면 *DemoApplication* 클래스를 마우스 오른쪽 단추로 클릭하여 실행할 수 있어야 합니다.
- Maven을 통해 `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"`을 실행하여 애플리케이션을 실행할 수 있습니다.

애플리케이션은 Azure Database for PostgreSQL에 연결하고 데이터베이스 스키마를 만든 다음, 콘솔 로그에 표시되는 대로 연결을 닫아야 합니다.

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>도메인 클래스 만들기

`DemoApplication` 클래스 옆에 새 `Todo` Java 클래스를 만들고 다음 코드를 추가합니다.

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

이 클래스는 *schema.sql* 스크립트를 실행할 때 만든 `todo` 테이블에 매핑된 도메인 모델입니다.

### <a name="insert-data-into-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에 데이터 삽입

*src/main/java/DemoApplication.java* 파일에서 주 메서드 뒤에 다음 메서드를 추가하여 데이터를 데이터베이스에 삽입합니다.

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

이제 `main` 메서드에서 다음 두 줄의 주석 처리를 제거할 수 있습니다.

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

이제 주 클래스를 실행하면 다음과 같은 출력이 생성됩니다.

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 데이터 읽기

코드가 제대로 작동하는지 확인하기 위해 이전에 삽입한 데이터를 읽어보겠습니다.

*src/main/java/DemoApplication.java* 파일에서 `insertData` 메서드 뒤에 다음 메서드를 추가하여 데이터베이스에서 데이터를 읽습니다.

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

이제 `main` 메서드에서 다음 줄의 주석 처리를 제거할 수 있습니다.

```java
todo = readData(connection);
```

이제 주 클래스를 실행하면 다음과 같은 출력이 생성됩니다.

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 데이터 업데이트

이전에 삽입한 데이터를 업데이트해 보겠습니다.

여전히 *src/main/java/DemoApplication.java* 파일에서 `readData` 메서드 뒤에 다음 메서드를 추가하여 데이터베이스 내의 데이터를 업데이트합니다.

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

이제 `main` 메서드에서 다음 두 줄의 주석 처리를 제거할 수 있습니다.

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

이제 주 클래스를 실행하면 다음과 같은 출력이 생성됩니다.

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL에서 데이터 삭제

마지막으로 이전에 삽입한 데이터를 삭제해 보겠습니다.

여전히 *src/main/java/DemoApplication.java* 파일에서 `updateData` 메서드 뒤에 다음 메서드를 추가하여 데이터베이스 내의 데이터를 삭제합니다.

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

이제 `main` 메서드에서 다음 줄의 주석 처리를 제거할 수 있습니다.

```java
deleteData(todo, connection);
```

이제 주 클래스를 실행하면 다음과 같은 출력이 생성됩니다.

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>결론 및 리소스 정리

축하합니다! JDBC를 사용하여 Azure Database for PostgreSQL에서 데이터를 저장하고 검색하는 Java 애플리케이션을 만들었습니다.

이 빠른 시작에서 사용된 모든 리소스를 정리하려면 다음 명령을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [내보내기 및 가져오기를 사용하여 데이터베이스 마이그레이션](./howto-migrate-using-export-and-import.md)
