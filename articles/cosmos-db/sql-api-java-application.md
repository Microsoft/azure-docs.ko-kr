---
title: '자습서: Azure Cosmos DB 및 SQL API를 사용하여 Java 웹 앱 빌드'
description: '자습서: 이 Java 웹 애플리케이션 자습서에서는 Azure Cosmos DB 및 SQL API를 사용하여 Azure Websites에 호스트된 Java 애플리케이션에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9daa09209818b9e01738630911a1bb926e3c257d
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079946"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>자습서: Azure Cosmos DB 및 SQL API를 사용하여 Java 웹 애플리케이션 빌드

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

이 Java 웹 애플리케이션 자습서에서는 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스를 사용하여 Azure App Service Web Apps에 호스트된 Java 애플리케이션에서 데이터를 저장하고 액세스하는 방법을 보여 줍니다. 이 문서에서는 다음에 대해 알아봅니다.

* Eclipse에서 기본 JSP(JavaServer Pages) 애플리케이션을 빌드하는 방법.
* [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java)를 사용하여 Azure Cosmos DB 서비스로 작업하는 방법입니다.

이 Java 애플리케이션 자습서에서는 다음 이미지에 표시된 것처럼 작업을 생성 및 검색하고 완료로 표시할 수 있게 해주는 웹 기반 작업 관리 애플리케이션을 만드는 방법을 보여 줍니다. 할 일 목록에 있는 각 작업은 Azure Cosmos DB에서 JSON 문서로 저장됩니다.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="My ToDo List Java 애플리케이션":::

> [!TIP]
> 이 애플리케이션 개발 자습서에서는 이전에 Java를 사용한 경험이 있다고 가정합니다. Java 또는 [필수 구성 요소 도구](#Prerequisites)를 처음 사용하는 경우 GitHub에서 전체 [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) 프로젝트를 다운로드하고 [이 문서의 끝에 있는 지침](#GetProject)을 사용하여 이 프로젝트를 빌드하는 것이 좋습니다. 프로젝트를 빌드하고 나면 이 문서를 검토하여 프로젝트의 컨텍스트에서 코드를 이해할 수 있습니다.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>이 Java 웹 애플리케이션 자습서의 필수 구성 요소

이 애플리케이션 개발 자습서를 시작하기 전에 다음이 있어야 합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [JDK(Java Development Kit) 7 이상](/java/azure/jdk/?view=azure-java-stable)
* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Java 런타임 환경(예: Tomcat 또는 Jetty)을 사용하는 Azure 웹 사이트](../app-service/quickstart-java.md)

이러한 도구를 처음 설치하는 경우 coreservlets.com에서 [자습서: TomCat7 설치 및 Eclipse에서 사용](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) 문서에 있는 [빠른 시작] 섹션의 단계별 설치 지침을 제공합니다.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Azure Cosmos DB 계정 만들기

Azure Cosmos DB 계정을 만들어 시작해 보겠습니다. 계정이 이미 있거나 이 자습서에서 Azure Cosmos DB 에뮬레이터를 사용하는 경우 [2단계: Java JSP 애플리케이션 만들기](#CreateJSP)로 건너뛸 수 있습니다.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Java JSP 애플리케이션 만들기

JSP 애플리케이션을 만들려면 다음을 수행합니다.

1. 먼저, Java 프로젝트를 만듭니다. Eclipse를 시작한 후 **파일**, **새로 만들기**, **동적 웹 프로젝트**를 차례로 클릭합니다. **동적 웹 프로젝트**가 사용 가능한 프로젝트로 나열되지 않았으면 다음을 수행합니다. **파일**, **새로 만들기**, **프로젝트**…를 차례로 클릭하고 **웹**을 확장하여 **동적 웹 프로젝트**를 클릭하고 **다음**을 클릭합니다.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java 애플리케이션 개발":::

1. **프로젝트 이름** 상자에 프로젝트 이름을 입력하고 **대상 런타임** 드롭다운 메뉴에서 선택적으로 값(예: Apache Tomcat v7.0)을 선택한 후 **마침**을 클릭합니다. 대상 런타임을 선택하면 Eclipse를 통해 프로젝트를 로컬로 실행할 수 있습니다.

1. Eclipse의 Project Explorer 보기에서 프로젝트를 확장합니다. **WebContent**를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **JSP File**을 클릭합니다.

1. **새 JSP 파일** 대화 상자에서 파일 이름을 **index.jsp**로 지정합니다. 다음 그림에 표시된 것처럼 상위 폴더를 **WebContent**로 유지하고 **다음**을 클릭합니다.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="새 JSP 파일 만들기 - Java 웹 애플리케이션 자습서":::

1. **JSP 템플릿 선택** 대화 상자에서 이 자습서의 목적에 따라, **새 JSP 파일(html)** 을 선택한 후 **마침**을 클릭합니다.

1. *index.jsp* 파일이 Eclipse에서 열리면 **Hello World!** 를 표시하도록 텍스트를 추가합니다. 기존 `<body>` 요소 내. 업데이트된 `<body>` 콘텐츠는 다음 코드와 같이 나타납니다.

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. *index.jsp* 파일을 저장합니다.

1. 2단계에서 대상 런타임을 설정했으면 **프로젝트**, **실행**을 차례로 클릭하여 JSP 애플리케이션을 로컬로 실행할 수 있습니다.

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World – Java 애플리케이션 자습서":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>SQL Java SDK 설치

SQL Java SDK 및 해당 종속성을 가져오는 가장 쉬운 방법은 [Apache Maven](https://maven.apache.org/)을 사용하는 것입니다. 이렇게 하려면 다음 단계를 수행해서 프로젝트를 maven 프로젝트로 변환해야 합니다.

1. 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **구성**을 클릭한 후 **Maven 프로젝트로 변환**을 클릭합니다.

1. **새 POM 만들기** 창에서 기본값을 수락하고 **마침**을 클릭합니다.

1. **프로젝트 탐색기**에서 pom.xml 파일을 엽니다.

1. **종속성** 탭의 **종속성** 창에서 **추가**를 클릭합니다.

1. **종속성 선택** 창에서 다음을 수행합니다.
   
   * **그룹 ID** 상자에 `com.azure`를 입력합니다.
   * **아티팩트 ID** 상자에 `azure-cosmos`를 입력합니다.
   * **버전** 상자에 `4.0.1-beta.1`을 입력합니다.
  
   또는 그룹 ID 및 아티팩트 ID에 대한 종속성 XML을 *pom.xml* 파일에 직접 추가합니다.

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
   </dependency>
   ```

1. **확인**을 클릭하면 Maven이 SQL Java SDK를 설치하거나 pom.xml 파일을 저장합니다.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Java 애플리케이션에서 Azure Cosmos DB 서비스 사용

이제 모델, 보기 및 컨트롤러를 웹 애플리케이션에 추가해 보겠습니다.

### <a name="add-a-model"></a>모델 추가

먼저 새 파일 *TodoItem.java*에 모델을 정의하겠습니다. `TodoItem` 클래스는 getter 및 setter 메서드와 함께 항목의 스키마를 정의합니다.

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>DAO(Data Access Object) 클래스 추가

DAO(Data Access Object)를 만들어서 영구적인 ToDo 항목을 Azure Cosmos DB로 추상화합니다. ToDo 항목을 컬렉션에 저장하려면 클라이언트는 유지할 데이터베이스 및 컬렉션(셀프 link로 참조)을 알고 있어야 합니다. 일반적으로 데이터베이스로의 추가 왕복을 방지할 수 있을 때 데이터베이스 및 컬렉션을 캐시하는 것이 좋습니다.

1. Azure Cosmos DB 서비스를 호출하려면 새 `cosmosClient` 개체를 인스턴스화해야 합니다. 일반적으로 각 후속 요청마다 새 클라이언트를 생성하는 것보다는 `cosmosClient` 개체를 다시 사용하는 것이 가장 좋습니다. `cosmosClientFactory` 클래스 내에 클라이언트를 정의하면 재사용할 수 있습니다. [1단계](#CreateDB)에서 저장한 HOST 및 MASTER_KEY 값을 업데이트합니다. HOST 변수를 URI로 바꾸고 MASTER_KEY를 기본 키로 바꿉니다. 다음 코드를 사용하여 *CosmosClientFactory.java* 파일 내에 `CosmosClientFactory` 클래스를 만듭니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/CosmosClientFactory.java":::

1. 새 *TodoDao.java* 파일을 만들고 `TodoDao` 클래스를 추가하여 todo 항목을 만들고, 업데이트하고, 읽고, 삭제합니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDao.java":::

1. 새 *MockDao* 파일을 만들고 `MockDao` 클래스를 추가합니다. 이 클래스는 항목에 대한 CRUD 작업을 수행하는 `TodoDao` 클래스를 구현합니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/MockDao.java":::

1. 새 *DocDbDao.java* 파일을 만들고 `DocDbDao` 클래스를 추가합니다. 이 클래스는 컨테이너에 TodoItem을 유지하는 코드를 정의하거나, 데이터베이스 및 컬렉션(있는 경우)을 검색하거나, 없을 경우 새로 만듭니다. 이 예제에서는 [Gson](https://code.google.com/p/google-gson/)을 사용하여 TodoItem POJO(Plain Old Java Object)를 JSON 문서에 직렬화하고 역직렬화합니다. ToDo 항목을 컬렉션에 저장하려면 클라이언트는 유지할 데이터베이스 및 컬렉션(셀프 link로 참조)을 알고 있어야 합니다. 또한 이 클래스는 자체 링크가 아닌 다른 특성(예: "ID")으로 문서를 검색하는 도우미 함수를 정의합니다. 도우미 메서드를 사용하여 ID로 TodoItem JSON 문서를 검색한 다음, POJO로 역직렬화할 수 있습니다.

   또한 `cosmosClient` 클라이언트 개체를 사용하여 SQL 쿼리를 사용하는 TodoItem의 컬렉션 또는 목록을 가져올 수 있습니다. 마지막으로, 목록에서 TodoItem을 삭제하는 delete 메서드를 정의합니다. 다음 코드는 `DocDbDao` 클래스의 콘텐츠를 보여 줍니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/DocDbDao.java":::

1. 다음으로, 새 *TodoDaoFactory.java* 파일을 만들고 새 DocDbDao 개체를 만드는 `TodoDaoFactory` 클래스를 추가합니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>컨트롤러 추가

애플리케이션에 *TodoItemController* 컨트롤러를 추가합니다. 이 프로젝트에서는 [Project Lombok](https://projectlombok.org/)을 사용해서 생성자, getter, setter 및 작성기를 생성합니다. 또는 이 코드를 수동으로 작성하거나 IDE에서 생성하도록 할 수도 있습니다.

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>서블릿 만들기

다음으로, HTTP 요청을 컨트롤러로 라우팅하는 서블릿을 만듭니다. *ApiServlet.java* 파일을 만들고 다음 코드를 정의합니다.

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Java 앱의 나머지 부분을 함께 연결

이제 재미있는 부분을 마쳤으므로 빠른 사용자 인터페이스를 빌드하고 DAO에 연결하기만 하면 됩니다.

1. 사용자에게 표시할 웹 사용자 인터페이스가 필요합니다. 다음 코드를 사용하여 앞에서 만든 *index.jsp*를 다시 작성하겠습니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. 끝으로, 웹 사용자 인터페이스와 서블릿을 연결하는 클라이언트 쪽 JavaScript를 작성합니다.

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. 이제 애플리케이션을 테스트하는 일만 남았습니다. 애플리케이션을 로컬로 실행하고 항목 이름과 범주를 입력하고 **작업 추가**를 클릭하여 Todo 항목을 추가합니다. 항목이 표시되면 확인란을 설정/해제하고 **작업 업데이트**를 클릭하여 항목의 완료 여부를 업데이트할 수 있습니다.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Azure 웹 사이트에 Java 애플리케이션 배포

Azure 웹 사이트에서는 Java 애플리케이션을 간단히 배포할 수 있습니다. 즉, 애플리케이션을 WAR 파일로 내보내고 소스 제어(예: Git) 또는 FTP를 통해 업로드하면 됩니다.

1. 애플리케이션을 WAR 파일로 내보내려면 **프로젝트 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **내보내기**를 클릭한 후 **WAR 파일**을 클릭합니다.

1. **WAR 내보내기** 창에서 다음을 수행합니다.
   
   * 웹 프로젝트 상자에 azure-documentdb-java-sample을 입력합니다.
   * 대상 상자에서 WAR 파일을 저장할 대상을 선택합니다.
   * **Finish**를 클릭합니다.

1. 이제 WAR 파일이 준비되었으므로 간단히 Azure 웹 사이트의 **webapps** 디렉터리로 업로드하면 됩니다. 파일 업로드에 대한 자세한 내용은 [Azure App Service Web Apps에 Java 애플리케이션 추가](../app-service/web-sites-java-add-app.md)를 참조하세요. WAR 파일이 webapps 디렉터리에 업로드되면 런타임 환경에서 이 파일이 추가되었음을 감지하고 자동으로 로드합니다.

1. 완성된 제품을 보려면 `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/`로 이동하고 작업 추가를 시작하세요.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>GitHub에서 프로젝트 가져오기

이 자습서의 모든 샘플은 GitHub의 [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) 프로젝트에 포함되어 있습니다. Todo 프로젝트를 Eclipse로 가져오려면 [필수 조건](#Prerequisites) 섹션에 나열된 소프트웨어 및 리소스가 있는지 확인한 후 다음을 수행합니다.

1. [Project Lombok](https://projectlombok.org/)을 설치합니다. Lombok은 프로젝트에서 생성자, getter, setter를 생성하는 데 사용됩니다. lombok.jar 파일을 다운로드한 다음에는 두 번 클릭하여 설치하거나 명령줄을 사용해서 설치합니다.

1. Eclipse가 열려 있으면 닫고 다시 시작해서 Lombok을 로드합니다.

1. Eclipse의 **파일** 메뉴에서 **가져오기**를 클릭합니다.

1. **가져오기** 창에서 **Git**, **Git의 프로젝트**, **다음**을 차례로 클릭합니다.

1. **리포지토리 원본 선택** 화면에서 **URI 복제**를 클릭합니다.

1. **원본 Git 리포지토리** 화면의 **URI** 상자에 https://github.com/Azure-Samples/documentdb-java-todo-app.git 를 입력한 다음, **다음**을 클릭합니다.

1. **분기 선택** 화면에서 **마스터**가 선택되었는지 확인하고 **다음**을 클릭합니다.

1. **로컬 대상** 화면에서 **찾아보기**를 클릭하여 리포지토리를 복사할 수 있는 폴더를 선택한 후 **다음**을 클릭합니다.

1. **프로젝트 가져오기에 사용할 마법사 선택** 화면에서 **기존 프로젝트 가져오기**가 선택되었는지 확인하고 **다음**을 클릭합니다.

1. **프로젝트 가져오기** 화면에서 **DocumentDB** 프로젝트를 선택 취소한 후 **마침**을 클릭합니다. DocumentDB 프로젝트에는 종속성으로 추가할 Azure Cosmos DB Java SDK가 포함됩니다.

1. **프로젝트 탐색기**에서 azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java로 이동하고 HOST 및 MASTER_KEY 값을 각각 해당 Azure Cosmos DB 계정의 URI 및 기본 키로 바꾸고 파일을 저장합니다. 자세한 내용은 [1단계를 참조하세요. Azure Cosmos 데이터베이스 계정 만들기](#CreateDB)를 참조하세요.

1. **프로젝트 탐색기**에서 **azure-documentdb-java-sample**을 마우스 오른쪽 단추로 클릭하고 **빌드 경로**를 클릭한 후 **빌드 경로 구성**을 클릭합니다.

1. **Java 빌드 경로** 화면의 오른쪽 창에서 **라이브러리** 탭을 선택한 후 **외부 JAR 추가**를 클릭합니다. lombok.jar 파일의 위치로 이동하고 **열기**를 클릭한 후 **확인**을 클릭합니다.

1. 12단계를 수행해서 **속성** 창을 다시 열고 왼쪽 창에서 **대상 런타임**을 클릭합니다.

1. **대상 런타임** 화면에서 **새로 만들기**를 클릭하고 **Apache Tomcat v7.0**을 선택한 후 **확인**을 클릭합니다.

1. 12단계를 수행해서 **속성** 창을 다시 열고 왼쪽 창에서 **프로젝트 패싯**을 클릭합니다.

1. **프로젝트 패싯** 화면에서 **동적 웹 모듈** 및 **Java**를 선택한 후 **확인**을 클릭합니다.

1. 화면 하단에 있는 **서버** 탭에서 **로컬 호스트의 Tomcat v7.0 서버**를 마우스 오른쪽 단추로 클릭하고 **추가 및 제거**를 클릭합니다.

1. **추가 및 제거** 창에서 **azure-documentdb-java-sample**을 **구성됨** 상자로 이동하고 **마침**을 클릭합니다.

1. **서버** 탭에서 **로컬 호스트의 Tomcat v7.0 서버**를 마우스 오른쪽 단추로 클릭하고 **다시 시작**을 클릭합니다.

1. 브라우저에서 `http://localhost:8080/azure-documentdb-java-sample/`로 이동하고 작업 목록에 추가하기 시작합니다. 기본 포트 값을 변경한 경우 8080을 선택한 값으로 변경합니다.

1. 프로젝트를 Azure 웹 사이트에 배포하려면 [6단계. Azure 웹 사이트에 애플리케이션 배포](#Deploy)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Cosmos DB를 사용하여 node.js 애플리케이션 빌드](sql-api-nodejs-application.md)
