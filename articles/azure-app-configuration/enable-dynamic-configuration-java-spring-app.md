---
title: Spring Boot 앱에서 동적 구성 사용
titleSuffix: Azure App Configuration
description: Spring Boot 앱에 대한 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.custom: devx-track-java
ms.author: lcozzens
ms.openlocfilehash: 653fcb6f6590fd503a97800ec8196025cf14a3b9
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121564"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>자습서: Java Spring 앱에서 동적 구성 사용

App Configuration Spring Boot 클라이언트 라이브러리는 애플리케이션을 다시 시작하지 않고도 주문형 구성 설정 세트의 업데이트를 지원합니다. 클라이언트 라이브러리는 각 설정을 캐시하여 구성 저장소에 대한 너무 많은 호출을 방지합니다. 캐시된 값이 만료될 때까지 새로 고침 작업은 구성 저장소에서 값이 변경된 경우에도 값을 업데이트하지 않습니다. 각 요청에 대한 기본 만료 시간은 30초입니다. 이는 필요한 경우 재정의할 수 있습니다.

`AppConfigurationRefresh`의 `refreshConfigurations()` 메서드를 호출하여 요청 시 업데이트된 설정을 확인할 수 있습니다.

또는 `spring-web`에 대한 종속성을 사용하여 자동화된 새로 고침을 처리하는 `spring-cloud-azure-appconfiguration-config-web` 패키지를 사용할 수 있습니다.

## <a name="use-automated-refresh"></a>자동화된 새로 고침 사용

자동화된 새로 고침을 사용하려면 [App Configuration에 대한 Spring Boot 빠른 시작](quickstart-java-spring-app.md)을 따라 만든 앱과 같은 App Configuration을 사용하는 Spring Boot 앱부터 시작합니다.

그런 다음, 텍스트 편집기에서 *pom.xml* 파일을 열고 `spring-cloud-azure-appconfiguration-config-web`에 대해 `<dependency>`를 추가합니다.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>로컬로 앱 실행 및 테스트

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 새 브라우저 창을 열고 URL(`http://localhost:8080`)로 이동합니다.  키와 연결된 메시지가 표시됩니다. 

    *curl*을 사용하여 애플리케이션을 테스트할 수도 있습니다. 예를 들면 다음과 같습니다. 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 동적 구성을 테스트하려면 애플리케이션과 연결된 Azure App Configuration 포털을 엽니다. **구성 탐색기**를 선택하고 표시되는 키의 값을 업데이트합니다. 예를 들면 다음과 같습니다.
    | 키 | 값 |
    |---|---|
    | application/config.message | Hello - 업데이트됨 |

1. 표시되는 새 메시지를 확인하려면 브라우저 페이지를 새로 고칩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Spring Boot 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
