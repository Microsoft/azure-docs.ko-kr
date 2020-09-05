---
title: '자습서: Azure Cosmos DB의 Cassandra API 테이블에 샘플 데이터를 로드하는 Java 앱'
description: 이 자습서에서는 Java 애플리케이션을 사용하여 Azure Cosmos DB에서 Cassandra API 테이블로 샘플 사용자 데이터를 로드하는 방법을 보여줍니다.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6a12db8e34421dd16c12d167896ef66b3377d524
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "82853038"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>자습서: Azure Cosmos DB에서 Cassandra API 테이블에 샘플 데이터 로드

개발자는 키/값 쌍을 사용하는 애플리케이션을 가질 수도 있습니다. Azure Cosmos DB에서 Cassandra API 계정을 사용하여 키/값 데이터를 저장 및 관리할 수 있습니다. 이 자습서에서는 Java 애플리케이션을 사용하여 Azure Cosmos DB에서 Cassandra API 계정의 테이블로 샘플 사용자 데이터를 로드하는 방법을 보여줍니다. Java 애플리케이션은 [Java 드라이버](https://github.com/datastax/java-driver)를 사용하며 사용자 ID, 사용자 이름 및 사용자 도시와 같은 사용자 데이터를 로드합니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Cassandra 테이블로 데이터 로드
> * 앱 실행

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 문서는 여러 부분으로 구성된 자습서의 일부분입니다. 이 문서를 시작하기 전에 [Cassandra API 계정, 키스페이스 및 테이블을 만들어야 합니다](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>테이블에 데이터 로드

다음 단계에 따라 Cassandra API 테이블에 데이터를 로드합니다.

1. “src\main\java\com\azure\cosmosdb\cassandra” 폴더의 “UserRepository.java” 파일을 열고 코드를 추가하여 user_id, user_name 및 user_bcity 필드를 테이블에 삽입합니다.

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. “src\main\java\com\azure\cosmosdb\cassandra” 폴더의 “UserProfile.java” 파일을 엽니다. 이 클래스에는 이전에 정의한 createKeyspace 및 createTable 메서드를 호출하는 main 메서드가 들어 있습니다. 이제 다음 코드를 추가하여 일부 샘플 데이터를 Cassandra API 테이블에 삽입합니다.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>앱 실행

명령 프롬프트 또는 터미널 창을 열고 프로젝트를 만든 위치로 폴더 경로를 변경합니다. “mvn clean install” 명령을 실행하여 대상 폴더 내에 cosmosdb-cassandra-examples.jar 파일을 생성하고 애플리케이션을 실행합니다. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

이제 Azure Portal에서 데이터 탐색기를 열어 사용자 정보가 테이블에 추가되었는지 확인할 수 있습니다.
    
## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB에서 Cassandra API 계정으로 샘플 데이터를 로드하는 방법을 알아보았습니다. 이제 다음 문서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Cassandra API 계정에서 데이터 쿼리](cassandra-api-query-data.md)
