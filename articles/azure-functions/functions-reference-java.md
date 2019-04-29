---
title: Azure Functions에 대한 Java 개발자 참조 | Microsoft Docs
description: Java로 함수를 개발하는 방법을 이해합니다.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 함수, 이벤트 처리, 웹후크, 동적 계산, 서버리스 아키텍처, Java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: 9258b58783d4670620a251fef866211f7634480f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020387"
---
# <a name="azure-functions-java-developer-guide"></a>Azure Functions Java 개발자 가이드

Azure Functions 런타임에서 지원 [Java SE 8 LTS (zulu8.31.0.2 jre8.0.181 win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/)합니다.

이 가이드는 Java 사용 하 여 Azure 함수를 작성 하는 복잡성에 대 한 정보를 포함 합니다.

Java 함수는 `public` 주석을 사용 하 여 데코레이팅된 메서드 `@FunctionName`합니다. 이 메서드는 java 함수에 대 한 항목을 정의 하 고 지정된 된 패키지에서 고유 해야 합니다. 

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. 첫 번째 만들려면 Functions 빠른 시작을 완료 해야 함수를 사용 하 여 [Visual Studio Code](functions-create-first-function-vs-code.md) 하거나 [maven을 사용 하 여](functions-create-first-java-maven.md)입니다.

## <a name="programming-model"></a>프로그래밍 모델 

[트리거 및 바인딩](functions-triggers-bindings.md)의 개념은 Azure Functions의 기본입니다. 트리거는 코드 실행을 시작합니다. 바인딩을 사용하면 사용자 지정 데이터 액세스 코드를 작성하지 않고도 데이터를 함수에 전달하고 함수에서 데이터를 반환할 수 있습니다.

## <a name="folder-structure"></a>폴더 구조

Azure Function Java 프로젝트의 폴더 구조는 다음과 같습니다.

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

함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일(.java)과 바인딩 구성 파일(function.json)이 있습니다.

하나의 프로젝트에 둘 이상의 함수를 넣을 수 있습니다. 함수를 별도의 jar에 넣지 않도록 하세요. 대상 디렉터리의 FunctionApp이 Azure의 함수 앱에 배포됩니다.

## <a name="triggers-and-annotations"></a>트리거 및 주석

 Azure 함수는 HTTP 요청, 타이머 또는 데이터 업데이트와 같은 트리거에 의해 호출됩니다. 함수는 해당 트리거 및 모든 다른 입력을 처리하고 하나 이상의 출력을 생성해야 합니다.

[com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) 패키지에 포함된 Java 주석을 사용하여 입력 및 출력을 메서드에 바인딩합니다. 자세한 내용은 [Java 참조 문서](/java/api/com.microsoft.azure.functions.annotation)를 참조하세요.

> [!IMPORTANT] 
> Azure Storage Blob, 큐 또는 테이블 트리거를 로컬로 실행하려면 [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file)에서 Azure Storage 계정을 구성해야 합니다.

예제:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
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

## <a name="jdk-runtime-availability-and-support"></a>JDK 런타임 사용 가능성 및 지원 

Java 함수 앱을 로컬에서 개발하는 데 사용할 수 있는 [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)의 [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK를 다운로드하여 사용합니다. Azure Functions는 클라우드에 함수 앱을 배포할 때 Azul Java 8 JDK 런타임을 사용합니다.

JDK 및 함수 앱에 문제가 있는 경우 [정격 지원 플랜](https://azure.microsoft.com/support/plans/)을 통해 [Azure 지원](https://azure.microsoft.com/en-us/support/)을 사용할 수 있습니다.

## <a name="third-party-libraries"></a>타사 라이브러리 

Azure Functions는 타사 라이브러리의 사용을 지원합니다. 기본적으로 `pom.xml` 프로젝트 파일에 지정된 모든 종속성은 [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) 목표 중에 자동으로 번들로 묶어집니다. `pom.xml` 파일에 종속성으로 지정되지 않은 라이브러리의 경우 함수의 루트 디렉터리에 있는 `lib` 디렉터리에 배치합니다. `lib` 디렉터리에 배치된 종속성은 런타임에 시스템 클래스 로더에 추가됩니다.

`com.microsoft.azure.functions:azure-functions-java-library` 종속성은 classpath에서 기본적으로 제공되므로 `lib` 디렉터리에 포함하지 않아도 됩니다. 또한 [여기](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies)에 나열된 종속성은 [azure-function-java-works-works](https://github.com/Azure/azure-functions-java-worker)를 통해 클래스 경로에 추가됩니다.

## <a name="data-type-support"></a>데이터 형식 지원

POJO(이전의 일반 Java 개체), `azure-functions-java-library`에 정의된 형식 또는 String(문자열), Integer(정수)와 같은 기본 dataType을 사용하여 입력/출력 바인딩에 바인딩할 수 있습니다.

### <a name="plain-old-java-objects-pojos"></a>이전의 일반 Java 개체(POJO)

입력 데이터를 POJO로 변환하기 위해 [azure-function-java-worker](https://github.com/Azure/azure-functions-java-worker)에서 [gson](https://github.com/google/gson) 라이브러리를 사용합니다. 함수에 대한 입력으로 사용되는 POJO 형식은 `public`이어야 합니다.

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

null 값이 필요한 경우 `Optional<T>`를 사용합니다.

## <a name="bindings"></a>바인딩

입력 및 출력 바인딩은 코드 내에서 데이터에 연결하는 선언적 방법을 제공합니다. 함수에는 여러 개의 입력 및 출력 바인딩이 있을 수 있습니다.

### <a name="example-input-binding"></a>입력 바인딩 예제

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
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

이 함수는 HTTP 요청으로 호출됩니다. 
- HTTP 요청 페이로드는 `String`으로 `inputReq` 인수에 전달됩니다.
- 하나의 항목이 Azure Table Storage에서 검색되고, `TestInputData`로 `inputData` 인수에 전달됩니다.

입력 일괄 처리를 받으려면 `String[]`, `POJO[]`, `List<String>` 또는 `List<POJO>`에 바인딩할 수 있습니다.

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

이 함수는 구성된 이벤트 허브에 새 데이터가 있을 때마다 트리거됩니다. `cardinality`가 `MANY`로 설정되면 함수는 이벤트 허브로부터 메시지의 일괄 처리를 받습니다. 이벤트 허브의 EventData는 함수 실행을 위해 `TestEventData`로 변환됩니다.

### <a name="example-output-binding"></a>출력 바인딩 예제

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

위 함수는 HttpRequest에서 호출되고, 여러 값을 Azure Queue에 씁니다.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage 및 HttpResponseMessage

 HttpRequestMessage 및 HttpResponseMessage 형식은 `azure-functions-java-library`에 정의되어 있으며, HttpTrigger 함수에서 사용할 도우미 형식입니다.

| 특수 형식      |       대상        | 일반적 용도                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP 트리거     | 메서드, 헤더 또는 쿼리 가져오기 |
| `HttpResponseMessage` | HTTP 출력 바인딩 | 200 이외의 반환 상태   |

## <a name="metadata"></a>Metadata

입력 데이터와 함께 [트리거 메타데이터](/azure/azure-functions/functions-triggers-bindings)를 보내는 트리거는 거의 없습니다. `@BindingName` 주석을 사용하여 메타데이터를 트리거하도록 바인딩할 수 있습니다.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
위의 예제에서 `queryValue`는 `http://{example.host}/api/metadata?name=test` Http 요청 URL에서 `name` 쿼리 문자열 매개 변수에 바인딩됩니다. 큐 트리거 메타데이터에서 `Id`에 바인딩하는 또 다른 예제는 다음과 같습니다.

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

`azure-functions-java-library`에 정의된 `ExecutionContext`에는 함수 런타임과 통신하는 도우미 메서드가 포함되어 있습니다.

### <a name="logger"></a>로거

`ExecutionContext`에 정의된 `getLogger`를 사용하여 함수 코드에서 로그를 기록합니다.

예제:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>로그 보기 및 추적

Azure CLI를 사용하여 다른 애플리케이션 로깅뿐만 아니라 Java stdout 및 stderr 로깅도 스트림할 수 있습니다. 

Azure CLI를 사용하여 애플리케이션 로깅을 작성하도록 Function 애플리케이션을 구성합니다.

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Azure CLI를 사용하여 함수 앱에 대한 로깅 출력을 스트리밍하려면 새 명령 프롬프트, Bash 또는 터미널 세션을 열고 다음 명령을 입력합니다.

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

Functions에서 [앱 설정](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. `System.getenv("AzureWebJobsStorage")`를 사용하여 이러한 설정에 액세스할 수 있습니다.

예제:

이름이 testAppSetting이고 값이 testAppSettingValue인 [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings)을 추가합니다.

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>다음 단계

Azure Function Java 개발에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) 및 [Eclipse](functions-create-maven-eclipse.md)를 사용하여 로컬로 개발하고 디버그합니다. 
* [Visual Studio Code를 사용하여 Java Azure Functions 원격 디버그](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Azure Functions의 Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) - `azure-functions:add` 목적을 통해 함수 생성을 간소화하고 [ZIP 파일 배포](deployment-zip-push.md)에 대한 준비 디렉터리를 준비합니다.
