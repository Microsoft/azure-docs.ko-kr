---
title: Azure Functions에 대한 Java 개발자 참조
description: Java로 함수를 개발하는 방법을 이해합니다.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java
ms.openlocfilehash: 1dd98ede537321403053e2e7c8a5f4f7272665d4
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144926"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 개발자 가이드

이 가이드에는 Java를 사용 하 Azure Functions를 개발 하는 데 도움이 되는 자세한 정보가 포함 되어 있습니다.

Java 개발자로 서 Azure Functions를 처음 접하는 경우 먼저 다음 문서 중 하나를 읽어 보세요.

| 시작 | 개념| 
| -- | -- |  
| <ul><li>[Visual Studio Code를 사용 하는 Java 함수](./functions-create-first-function-vs-code.md?pivots=programming-language-java)</li><li>[터미널/명령 프롬프트를 사용 하는 Java/Maven 함수](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)</li><li>[Gradle를 사용 하는 Java 함수](functions-create-first-java-gradle.md)</li><li>[Eclipse를 사용 하는 Java 함수](functions-create-maven-eclipse.md)</li><li>[IntelliJ 아이디어를 사용 하는 Java 함수](functions-create-maven-intellij.md)</li></ul> | <ul><li>[개발자 가이드](functions-reference.md)</li><li>[호스팅 옵션](functions-scale.md)</li><li>[성능 &nbsp; 고려 사항](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Java 함수 기본 사항

Java 함수는 주석 `@FunctionName`으로 데코레이트된 `public` 메서드입니다. 이 메서드는 Java 함수의 항목을 정의하며 특정 패키지에서 고유해야 합니다. 패키지에는 여러 public 메서드가로 주석 처리 된 여러 클래스가 있을 수 있습니다 `@FunctionName` . 단일 패키지가 Azure의 함수 앱에 배포 됩니다. Azure에서 실행 하는 경우 함수 앱은 개별 Java 함수에 대 한 배포, 실행 및 관리 컨텍스트를 제공 합니다.

## <a name="programming-model"></a>프로그래밍 모델 

[트리거 및 바인딩](functions-triggers-bindings.md)의 개념은 Azure Functions의 기본입니다. 트리거는 코드 실행을 시작합니다. 바인딩을 사용하면 사용자 지정 데이터 액세스 코드를 작성하지 않고도 데이터를 함수에 전달하고 함수에서 데이터를 반환할 수 있습니다.

## <a name="create-java-functions"></a>Java 함수 만들기

Java 함수를 보다 쉽게 만들 수 있도록 특정 함수 트리거를 사용하여 프로젝트를 만드는 데 도움이 되는 미리 정의된 Java 템플릿을 사용하는 Maven 기반 도구와 아키타입이 있습니다.    

### <a name="maven-based-tooling"></a>Maven 기반 도구

다음 개발자 환경에는 Java 함수 프로젝트를 만들도록 하는 Azure Functions 도구가 있습니다. 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

위 문서 링크는 선택한 IDE를 사용하여 첫 번째 함수를 만드는 방법을 보여 줍니다. 

### <a name="project-scaffolding"></a>프로젝트 스캐폴딩

터미널에서 명령줄 개발을 선호하는 경우 Java 기반 함수 프로젝트를 스캐폴드하는 가장 간단한 방법은 `Apache Maven` 아키타입을 사용하는 것입니다. Azure Functions의 Java Maven 아키타입은 다음 _groupId_:_artifactId_로 게시됩니다. [com.microsoft.azure:azure-functions-archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

다음 명령은 이 아키타입을 사용하여 새 Java 함수 프로젝트를 생성합니다.

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

이 아키타입 사용을 시작하려면 [Java 빠른 시작](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java)을 참조하세요. 

## <a name="folder-structure"></a>폴더 구조

Azure Functions Java 프로젝트의 폴더 구조는 다음과 같습니다.

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

공유 [host.json](functions-host-json.md) 파일을 사용하여 함수 앱을 구성할 수 있습니다. 각 함수에는 자체 코드 파일(.java)과 바인딩 구성 파일(function.json)이 있습니다.

하나의 프로젝트에 둘 이상의 함수를 넣을 수 있습니다. 함수를 별도의 jar에 넣지 않도록 하세요. 대상 디렉터리의 `FunctionApp`이 Azure의 함수 앱에 배포됩니다.

## <a name="triggers-and-annotations"></a>트리거 및 주석

 함수는 HTTP 요청, 타이머 또는 데이터 업데이트와 같은 트리거에 의해 호출됩니다. 함수는 해당 트리거 및 모든 다른 입력을 처리하고 하나 이상의 출력을 생성해야 합니다.

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 패키지에 포함된 Java 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다. 자세한 내용은 [Java 참조 문서](/java/api/com.microsoft.azure.functions.annotation)를 참조하세요.

> [!IMPORTANT] 
> Azure Blob Storage, Azure Queue Storage 또는 Azure Table 스토리지 트리거를 로컬로 실행하려면 [local.settings.json](./functions-run-local.md#local-settings-file)에서 Azure Storage 계정을 구성해야 합니다.

예제:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

다음은 [azure-function-maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)에서 생성된 해당 `function.json`입니다.

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="java-versions"></a>Java 버전

_Java 11에 대 한 지원은 현재 미리 보기 상태입니다._

Azure에서 함수가 실행 되는 함수 앱을 만들 때 사용 되는 Java 버전은 pom.xml 파일에 지정 되어 있습니다. Maven 원형는 현재 Java 8에 대 한 pom.xml를 생성 합니다 .이는 게시 하기 전에 변경할 수 있습니다. pom.xml의 Java 버전은 응용 프로그램을 로컬로 개발 하 고 테스트 한 버전과 일치 해야 합니다. 

### <a name="supported-versions"></a>지원되는 버전

다음 표에서는 운영 체제별로 함수 런타임의 각 주 버전에 대해 현재 지원 되는 Java 버전을 보여 줍니다.

| Functions 버전 | Java 버전 (Windows) | Java 버전 (Linux) |
| ----- | ----- | --- |
| 3.x | 11 (미리 보기)<br/>8 | 11 (미리 보기)<br/>8 |
| 2.x | 8 | 해당 없음 |

배포에 대 한 Java 버전을 지정 하지 않는 한 Maven 원형는 Azure에 배포 하는 동안 기본적으로 Java 8로 설정 됩니다.

### <a name="specify-the-deployment-version"></a>배포 버전 지정

매개 변수를 사용 하 여 Maven 원형가 대상으로 하는 Java 버전을 제어할 수 있습니다 `-DjavaVersion` . 이 매개 변수 값은 에테르 스코프 또는 일 수 있습니다 `8` `11` . Java 11 지원은 현재 미리 보기 상태입니다. 

Maven 원형는 지정 된 Java 버전을 대상으로 하는 pom.xml을 생성 합니다. pom.xml의 다음 요소는 사용할 Java 버전을 표시 합니다.

| 요소 |  Java 8 값 | Java 11 값 | Description |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Maven-플러그 인에서 사용 하는 Java 버전입니다. |
| **`JavaVersion`** | 8 | 11 | Azure의 함수 앱에서 호스트 하는 Java 버전입니다. |

다음 예에서는 pom.xml 파일의 관련 섹션에서 Java 8의 설정을 보여 줍니다.

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Maven를 사용 하 여 코드를 컴파일하는 동안 사용 되는 JDK 디렉터리로 JAVA_HOME 환경 변수를 올바르게 설정 해야 합니다. JDK의 버전이 최소 설정 보다 높아야 합니다 `Java.version` . 

### <a name="specify-the-deployment-os"></a>배포 OS 지정

또한 Maven를 사용 하면 함수 앱이 Azure에서 실행 되는 운영 체제를 지정할 수 있습니다. 요소를 사용 `os` 하 여 운영 체제를 선택 합니다. 

| 요소 |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | Windows | 용 | docker |

다음 예에서는 `runtime` pom.xml 파일의 섹션에서 운영 체제 설정을 보여 줍니다.

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>JDK 런타임 사용 가능성 및 지원 

Java 함수 앱의 로컬 개발에 대 한 적절 한 [Azul 줄루어 Enterprise](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) 를 다운로드 하 여 [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)에서 Azure Java jdks에 사용 합니다. Azure Functions는 함수 앱을 클라우드에 배포할 때 Azul Java JDK 런타임을 사용 합니다.

JDK 및 함수 앱에 문제가 있는 경우 [정규화된 지원 플랜](https://azure.microsoft.com/support/plans/)을 통해 [Azure 지원](https://azure.microsoft.com/support/)을 사용할 수 있습니다.

## <a name="customize-jvm"></a>JVM 사용자 지정

함수를 사용하여 Java 함수를 실행하는 데 사용되는 JVM(Java Virtual Machine)을 사용자 지정할 수 있습니다. [다음 JVM 옵션](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7)은 기본적으로 사용됩니다.

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

`JAVA_OPTS`라는 앱 설정에서 추가 인수를 제공할 수 있습니다. Azure Portal 또는 Azure CLI에서 Azure에 배포된 함수 앱에 앱 설정을 추가할 수 있습니다.

> [!IMPORTANT]  
> 사용 플랜에서 사용자 지정이 작동하려면 값이 0인 WEBSITE_USE_PLACEHOLDER 설정도 추가해야 합니다. 이 설정은 Java 함수의 콜드 부팅 시간을 증가시킵니다.

### <a name="azure-portal"></a>Azure portal

[Azure Portal](https://portal.azure.com)에서 [애플리케이션 설정 탭](functions-how-to-use-azure-function-app-settings.md#settings)을 사용하여 `JAVA_OPTS` 설정을 추가합니다.

### <a name="azure-cli"></a>Azure CLI

다음 예제와 같이 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령을 사용하여 `JAVA_OPTS`를 설정할 수 있습니다.

#### <a name="consumption-plan"></a>[사용 플랜](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[전용 플랜/프리미엄 플랜](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

이 예제에서는 헤드리스 모드를 사용합니다. 또한 `<APP_NAME>`을 함수 앱의 이름으로 바꾸고 `<RESOURCE_GROUP>`을 리소스 그룹으로 바꿉니다. 

## <a name="third-party-libraries"></a>타사 라이브러리 

Azure Functions는 타사 라이브러리의 사용을 지원합니다. 기본적으로 `pom.xml` 프로젝트 파일에 지정된 모든 종속성은 [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 목표 중에 자동으로 번들로 묶입니다. `pom.xml` 파일에 종속성으로 지정되지 않은 라이브러리의 경우 함수의 루트 디렉터리에 있는 `lib` 디렉터리에 배치합니다. `lib` 디렉터리에 배치된 종속성은 런타임에 시스템 클래스 로더에 추가됩니다.

`com.microsoft.azure.functions:azure-functions-java-library` 종속성은 클래스 경로에서 기본적으로 제공되므로 `lib` 디렉터리에 포함하지 않아도 됩니다. 또한 [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker)는 [여기](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) 나열된 종속성을 클래스 경로에 추가합니다.

## <a name="data-type-support"></a>데이터 형식 지원

POJO(이전의 일반 Java 개체), `azure-functions-java-library`에 정의된 형식 또는 문자열 및 정수와 같은 기본 데이터 형식을 사용하여 입력 또는 출력 바인딩에 바인딩할 수 있습니다.

### <a name="pojos"></a>POJO

입력 데이터를 POJO로 변환하기 위해 [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker)는 [gson](https://github.com/google/gson) 라이브러리를 사용합니다. 함수에 대한 입력으로 사용되는 POJO 형식은 `public`이어야 합니다.

### <a name="binary-data"></a>이진 데이터

function.json의 `dataType` 필드를 `binary`로 설정하여 이진 입력 또는 출력을 `byte[]`에 바인딩합니다.

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Null 값이 필요한 경우 `Optional<T>`를 사용합니다.

## <a name="bindings"></a>바인딩

입력 및 출력 바인딩은 코드 내에서 데이터에 연결하는 선언적 방법을 제공합니다. 함수에는 여러 개의 입력 및 출력 바인딩이 있을 수 있습니다.

### <a name="input-binding-example"></a>입력 바인딩 예제

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

HTTP 요청을 사용하여 이 함수를 호출합니다. 
- HTTP 요청 페이로드는 `String`으로 `inputReq` 인수에 전달됩니다.
- 하나의 항목이 Table Storage에서 검색되고 `TestInputData`로 `inputData` 인수에 전달됩니다.

입력 일괄 처리를 받기 위해 `String[]`, `POJO[]`, `List<String>` 또는 `List<POJO>`에 바인딩할 수 있습니다.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

이 함수는 구성된 이벤트 허브에 새 데이터가 있을 때마다 트리거됩니다. `cardinality`가 `MANY`로 설정되므로 함수는 이벤트 허브로부터 메시지의 일괄 처리를 받습니다. 이벤트 허브의 `EventData`는 함수 실행을 위해 `TestEventData`로 변환됩니다.

### <a name="output-binding-example"></a>출력 바인딩 예제

`$return`을 사용하여 출력 바인딩을 반환 값에 바인딩할 수 있습니다. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

여러 개의 출력 바인딩이 있으면 둘 중 하나에 대한 반환 값을 사용합니다.

여러 출력 값을 보내려면 `azure-functions-java-library` 패키지에 정의된 `OutputBinding<T>`를 사용합니다. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

HttpRequest에서 이 함수를 호출합니다. Queue Storage에 여러 값을 기록합니다.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 및 HttpResponseMessage

 이 항목은 `azure-functions-java-library`에서 정의됩니다. HttpTrigger 함수를 사용하는 도우미 형식입니다.

| 특수 형식      |       대상        | 일반적인 용도                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 트리거     | 메서드, 헤더 또는 쿼리 가져오기 |
| `HttpResponseMessage` | HTTP 출력 바인딩 | 200 이외의 상태 반환   |

## <a name="metadata"></a>메타데이터

입력 데이터와 함께 [트리거 메타데이터](./functions-triggers-bindings.md)를 보내는 트리거는 거의 없습니다. `@BindingName` 주석을 사용하여 트리거 메타데이터에 바인딩할 수 있습니다.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
앞의 예제에서 `queryValue`는 HTTP 요청 URL `http://{example.host}/api/metadata?name=test`에서 `name` 쿼리 문자열 매개 변수에 바인딩됩니다. 큐 트리거 메타데이터에서 `Id`에 바인딩하는 방법을 보여 주는 다른 예제는 다음과 같습니다.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> 주석에 제공된 이름은 메타데이터 속성과 일치해야 합니다.

## <a name="execution-context"></a>실행 컨텍스트

`azure-functions-java-library`에 정의된 `ExecutionContext`에는 함수 런타임과 통신하는 도우미 메서드가 포함되어 있습니다. 자세한 내용은 [ExecutionContext 참조 문서](/java/api/com.microsoft.azure.functions.executioncontext)를 참조 하세요.

### <a name="logger"></a>로거

`ExecutionContext`에 정의된 `getLogger`를 사용하여 함수 코드의 로그를 기록합니다.

예제:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>로그 보기 및 추적

Azure CLI를 사용하여 다른 애플리케이션 로깅뿐만 아니라 Java stdout 및 stderr 로깅도 스트림할 수 있습니다. 

Azure CLI를 사용하여 애플리케이션 로깅을 기록하는 함수 앱을 구성하는 방법은 다음과 같습니다.

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI를 사용하여 함수 앱의 로깅 출력을 스트림하려면 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
[az webapp log tail](/cli/azure/webapp/log) 명령에는 `--provider` 옵션을 사용하여 출력을 필터링하는 옵션이 있습니다. 

Azure CLI를 사용하여 로그 파일을 단일 ZIP 파일로 다운로드하려면 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

이 명령을 실행하기 전에 Azure Portal 또는 Azure CLI에 로그인하는 파일 시스템이 사용하도록 설정되어 있어야 합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. `System.getenv("AzureWebJobsStorage")`를 사용하여 해당 설정에 액세스할 수 있습니다.

다음 예제에서는 `myAppSetting`이라는 키가 있는 [애플리케이션 설정](functions-how-to-use-azure-function-app-settings.md#settings)을 가져옵니다.

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> AppSetting FUNCTIONS_EXTENSION_VERSION의 값은 최적화된 콜드 부팅 환경에 대해 ~2 또는 ~3 이어야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Functions Java 개발에 관한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) 및 [Eclipse](functions-create-maven-eclipse.md)를 사용한 로컬 개발 및 디버그
* [Visual Studio Code를 사용한 원격 디버그 Java 함수](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Functions의 Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* `azure-functions:add` 목표를 통해 함수 생성을 간소화하고 [ZIP 파일 배포](deployment-zip-push.md)의 스테이징 디렉터리를 준비합니다.