---
title: Kotlin 및 Maven을 사용하여 Azure에서 첫 번째 함수 만들기
description: Kotlin 및 Maven을 사용하여 Azure에 HTTP 트리거 함수를 만들어 게시합니다.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 526287feb93052d10428971dfd8f660d4265c831
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071264"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>빠른 시작: Kotlin 및 Maven을 사용하여 첫 번째 함수 만들기

이 문서에서는 Maven 명령줄 도구를 사용하여 Kotlin 함수 프로젝트를 빌드하고 Azure Functions에 게시하는 방법을 안내합니다. 완료되면 함수 코드가 Azure의 [사용 플랜](functions-scale.md#consumption-plan)에서 실행되고 HTTP 요청을 사용하여 트리거될 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

Kotlin을 사용하여 함수를 개발하려면 다음을 설치해야 합니다.

- [Java Developer Kit](https://aka.ms/azure-jdks), 버전 8
- [Apache Maven](https://maven.apache.org), 버전 3.0 이상
- [Azure CLI](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) 버전 2.6.666 이상

> [!IMPORTANT]
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

## <a name="generate-a-new-functions-project"></a>새 Functions 프로젝트 생성

빈 폴더에서 다음 명령을 실행하여 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)으로부터 Functions 프로젝트를 생성합니다.

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> 명령 실행에 문제가 발생하는 경우 `maven-archetype-plugin` 버전이 사용되는지 살펴보시기 바랍니다. `.pom` 파일이 없는 빈 디렉터리에서 명령을 실행하고 있으므로 이전 버전에서 Maven을 업그레이드한 경우 `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`에서 이전 버전의 플러그 인을 사용을 시도할 수도 있습니다. 그렇다면 `maven-archetype-plugin` 디렉터리를 삭제하고 명령을 다시 실행해보세요.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven은 프로젝트 생성을 완료하는 데 필요한 값을 요청합니다. _groupId_, _artifactId_ 및 _version_ 값은 [Maven 명명 규칙](https://maven.apache.org/guides/mini/guide-naming-conventions.html) 참고를 참조하세요. _appName_ 값은 Azure 전체에서 고유해야 하므로 기본적으로 Maven이 이전에 입력한 _artifactId_ 를 기준으로 앱 이름을 생성합니다. _packageName_ 값은 생성된 함수 코드에 대한 Kotlin 패키지를 결정합니다.

아래의 `com.fabrikam.functions` 및 `fabrikam-functions` 식별자는 예제로 사용되며 이 빠른 시작의 이후 단계를 좀 더 쉽게 읽는 데 도움이 됩니다. 이 단계에서는 Maven에 고유의 값을 제공하는 것이 좋습니다.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven은 이름이 _artifactId_ 인 새 폴더에 프로젝트 파일을 만드는데, 이 예제에서는 `fabrikam-functions`입니다. 이 프로젝트에서 생성된 즉시 실행 가능 코드는 요청의 본문을 에코하는 간단한 [HTTP 트리거](./functions-bindings-http-webhook.md) 함수입니다.

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

디렉터리를 새로 만든 프로젝트 폴더로 변경하고 Maven으로 함수를 실행합니다.

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Java 9에서 `javax.xml.bind.JAXBException` 예외가 발생하면 [GitHub](https://github.com/jOOQ/jOOQ/issues/6477)에서 해결 방법을 참조하세요.

함수가 시스템에서 로컬로 실행되고 HTTP 요청에 응답할 준비가 완료되면 이 출력이 보입니다.

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

새 터미널 창에서 curl을 사용하여 명령줄에서 함수를 트리거합니다.

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

터미널에서 `Ctrl-C`를 사용하여 함수 코드를 중지합니다.

## <a name="deploy-the-function-to-azure"></a>Azure에 함수 배포

Azure Functions에 대한 배포 프로세스는 Azure CLI의 계정 자격 증명을 사용합니다. 계속하려면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```

`azure-functions:deploy` Maven 대상을 사용하여 새 함수 앱에 코드를 배포합니다.

> [!NOTE]
> Visual Studio Code를 사용하여 함수 앱을 배포하는 경우 체험판이 아닌 구독을 선택해야 합니다. 그렇지 않으면 오류가 표시됩니다. IDE의 왼쪽에 구독이 표시됩니다.

```
mvn azure-functions:deploy
```

배포가 완료되면 Azure 함수 앱에 액세스하는 데 사용할 수 있는 URL이 표시됩니다.

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

`cURL`을 사용하여 Azure에서 실행 중인 함수 앱을 테스트합니다. 아래 예제에서는 이전 단계에서 만든 사용자 고유의 함수 앱에 배포한 URL과 일치하도록 URL을 변경해야 합니다.

> [!NOTE]
> **액세스 권한**을 `Anonymous`로 설정해야 합니다. 기본 수준 `Function`을 선택하면 함수 엔드포인트에 액세스하기 위해 요청에 [함수 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 제공해야 합니다.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>변경 및 재배포

생성된 프로젝트에서 `src/main.../Function.java` 원본 파일을 편집하여 함수 앱에서 반환된 텍스트를 변경합니다. 다음과 같이 변경합니다.

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

다음 코드로 이동:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

변경 내용을 저장하고 이전처럼 터미널에서 `azure-functions:deploy`를 실행하여 다시 배포합니다. 함수 앱이 업데이트되고 이 요청은 다음을 갖습니다.

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

업데이트된 출력이 표시됩니다.

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>바인딩 참조

HTTP 트리거 및 타이머 트리거 이외의 [Functions 트리거 및 바인딩을](functions-triggers-bindings.md) 사용하려면 바인딩 확장을 설치해야 합니다. 이 문서에서는 필요하지 않지만 다른 바인딩 형식으로 작업할 때 확장을 사용하도록 설정하는 방법을 알고 있어야 합니다.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>다음 단계

간단한 HTTP 트리거가 있는 Kotlin 함수 앱을 만들어 Azure Functions에 배포했습니다.

- Java 및 Kotlin 함수 개발에 대한 자세한 내용은 [Java Functions 개발자 가이드](functions-reference-java.md)를 검토하세요.
- `azure-functions:add` Maven 대상을 사용하여 프로젝트에 다른 트리거가 있는 다른 함수를 추가합니다.
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) 및 [Eclipse](functions-create-maven-eclipse.md)를 사용하여 로컬로 함수를 작성하고 디버그합니다. 
- Visual Studio Code를 사용하여 Azure에 배포된 함수를 디버그합니다. 자세한 지침은 Visual Studio Code [서버 없는 Java 애플리케이션](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) 설명서를 참조하세요.
