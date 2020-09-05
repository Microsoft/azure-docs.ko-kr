---
title: Azure App Configuration 사용 방법을 배우기 위한 빠른 시작
description: 이 빠른 시작에서는 Azure App Configuration을 사용하여 Java Spring 앱을 만들어 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 10b38d5c5802ec4f6bc33b15b3ddef12e8256a78
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586456"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>빠른 시작: Azure App Configuration을 사용하여 Java Spring 앱 만들기

이 빠른 시작에서는 Azure App Configuration을 Java Spring 앱에 통합하여 코드와 별도로 애플리케이션 설정의 스토리지 및 관리를 중앙 집중화합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- 버전 8 이상이 설치된 지원되는 [JDK(Java Development Kit)](https://docs.microsoft.com/java/azure/jdk)
- [Apache Maven](https://maven.apache.org/download.cgi) 버전 3.0 이상

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** >  **+ 만들기** > **키-값**을 차례로 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | /application/config.message | 안녕하세요. |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

7. **적용**을 선택합니다.

## <a name="create-a-spring-boot-app"></a>Spring Boot 앱 만들기

[Spring Initializr](https://start.spring.io/)를 사용하여 새 Spring Boot 프로젝트를 만듭니다.

1. [https://www.microsoft.com]\(<https://start.spring.io/>) 로 이동합니다.

1. 다음 옵션을 지정합니다.

   - **Java**를 사용하는 **Maven** 프로젝트를 생성합니다.
   - **Spring Boot** 버전 2.0 이상을 지정합니다.
   - 애플리케이션에 대한 **그룹** 및 **아티팩트** 이름을 지정합니다.
   - **Spring Web** 종속성을 추가합니다.

1. 이전 옵션을 지정한 후 **프로젝트 생성**을 선택합니다. 메시지가 표시되면 로컬 컴퓨터의 경로에 프로젝트를 다운로드합니다.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 로컬 시스템에서 파일의 압축을 풀면 간단한 Spring Boot 애플리케이션을 편집할 수 있습니다. 앱의 루트 디렉터리에서 *pom.xml* 파일을 찾습니다.

1. 텍스트 편집기에서 *pom.xml* 파일을 열고 `<dependencies>` 목록에 Spring Cloud Azure Config starter를 추가합니다.

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. 앱의 패키지 디렉터리에 *MessageProperties.java*라는 새 Java 파일을 만듭니다. 다음 줄을 추가합니다.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. 앱의 패키지 디렉터리에 *HelloController.java*라는 새 Java 파일을 만듭니다. 다음 줄을 추가합니다.

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. 기본 애플리케이션 Java 파일을 열고 `@EnableConfigurationProperties`를 추가하여 이 기능을 활성화합니다.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. 앱의 리소스 디렉터리 아래에 `bootstrap.properties`라는 새 파일을 만들고, 다음 줄을 파일에 추가합니다. 샘플 값을 App Configuration 저장소에 대한 해당 속성으로 바꿉니다.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. **APP_CONFIGURATION_CONNECTION_STRING**이이라는 환경 변수를 설정하고, App Configuration 저장소에 대한 액세스 키로 설정합니다. 명령줄에서 다음 명령을 실행하고 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예를 들어 다음과 같습니다.

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. 애플리케이션이 실행되면 *curl*을 사용하여 애플리케이션을 테스트합니다. 예를 들어 다음과 같습니다.

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    App Configuration 저장소에 입력한 메시지가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만들고, Java Spring 앱에서 사용했습니다. 자세한 내용은 [Azure의 Spring](https://docs.microsoft.com/java/azure/spring-framework/)을 참조하세요. Java Spring 앱에서 구성 설정을 동적으로 새로 고치도록 설정하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [동적 구성을 사용하도록 설정](./enable-dynamic-configuration-java-spring-app.md)
