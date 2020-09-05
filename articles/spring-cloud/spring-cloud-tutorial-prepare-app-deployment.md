---
title: Azure 스프링 클라우드에서 배포용 Java 스프링 응용 프로그램을 준비 하는 방법
description: Azure 스프링 클라우드에 배포 하기 위해 Java 스프링 응용 프로그램을 준비 하는 방법에 대해 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 79d3829eaea15c8e7909b98b83d1327cd90e4544
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260326"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비

이 항목에서는 Azure Spring Cloud에 배포하기 위해 기존 Java Spring 애플리케이션을 준비하는 방법을 보여줍니다. 제대로 구성된 경우, Azure Spring Cloud는 Java Spring Cloud 애플리케이션을 모니터링하고, 크기를 조정하고, 업데이트할 수 있는 강력한 서비스를 제공합니다.

이 예를 실행 하기 전에 [기본 빠른](spring-cloud-quickstart.md)시작을 사용해 볼 수 있습니다.

다른 예제에서는 POM 파일이 구성된 경우 Azure Spring Cloud에 애플리케이션을 배포하는 방법을 설명합니다. 
* [첫 번째 앱 시작](spring-cloud-quickstart.md)
* [마이크로서비스 빌드 및 실행](spring-cloud-quickstart-sample-app-introduction.md)

이 문서에서는 필요한 종속성과 이것을 POM 파일에 추가하는 방법을 설명합니다.

## <a name="java-runtime-version"></a>Java Runtime 버전

Spring/Java 애플리케이션만 Azure Spring Cloud에서 실행할 수 있습니다.

Azure Spring Cloud는 Java 8 및 Java 11을 모두 지원합니다. 호스팅 환경에는 Azure용 Azul Zulu OpenJDK의 최신 버전이 포함되어 있습니다. Azure용 Azul Zulu OpenJDK에 대한 자세한 내용은 [JDK 설치](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)를 참조하세요.

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot 및 Spring Cloud 버전

Azure Spring Cloud에 배포할 기존 Spring Boot 애플리케이션을 준비하려면 다음 섹션에 표시된 대로 애플리케이션 POM 파일에 Spring Boot 및 Spring Cloud 종속성을 포함합니다.

Azure Spring Cloud는 Spring Boot 버전 2.1 또는 버전 2.2의 Spring Boot 앱만 지원합니다. 아래 표에는 지원되는 Spring Boot 및 Spring Cloud 조합이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton
2.3 | Hoxton

### <a name="dependencies-for-spring-boot-version-21"></a>Spring Boot 버전 2.1에 대한 종속성

Spring Boot 버전 2.1의 경우, 애플리케이션 POM 파일에 다음 종속성을 추가합니다.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Spring Boot 버전 2.2에 대한 종속성

Spring Boot 버전 2.2의 경우, 애플리케이션 POM 파일에 다음 종속성을 추가합니다.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>스프링 부팅 버전 2.3에 대 한 종속성

스프링 부팅 버전 2.3의 경우 응용 프로그램 POM 파일에 다음 종속성을 추가 합니다.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud 클라이언트 종속성

Azure Spring Cloud는 Spring Cloud 구성 요소를 호스트하고 관리합니다. 구성 요소에는 Spring Cloud 서비스 레지스트리 및 Spring Cloud 구성 서버가 포함됩니다. 스프링 부트 2.2 또는 2.3을 사용 하는 것이 좋습니다. 스프링 부팅 2.1의 경우 Azure 스프링 클라우드 서비스 인스턴스와 통신할 수 있도록 종속성에 Azure 스프링 클라우드 클라이언트 라이브러리를 포함 해야 합니다.

다음 표에는 Spring Boot 및 Spring Cloud를 사용하는 앱의 올바른 Azure Spring Cloud 버전이 나와 있습니다.

Spring Boot 버전 | Spring Cloud 버전 | Azure 스프링 클라우드 클라이언트 스타터 버전
---|---|---
2.1 | Greenwich.RELEASE | 2.1.2
2.2 | Hoxton | 필요하지 않음
2.3 | Hoxton | 필요하지 않음

스프링 부트 2.1를 사용 하는 경우 pom.xml 파일에 다음 dependenciy를 포함 합니다.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Azure Spring Cloud 기능을 사용하도록 설정하기 위한 기타 권장 종속성

서비스 레지스트리에서 분산 추적까지 Azure Spring Cloud의 다양한 기본 제공 기능을 사용하도록 설정하려면 애플리케이션에 다음 종속성도 포함해야 합니다. 특정 앱에 해당하는 기능이 필요하지 않은 경우 이러한 종속성 중 일부를 삭제할 수 있습니다.

### <a name="service-registry"></a>서비스 레지스트리

관리형 Azure Service Registry 서비스를 사용하려면 아래와 같이 `spring-cloud-starter-netflix-eureka-client` 종속성을 pom.xml 파일에 포함합니다.

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

서비스 레지스트리 서버의 엔드포인트는 앱에 환경 변수로 자동 삽입됩니다. 애플리케이션이 서비스 레지스트리 서버에 자체적으로 등록되고, 기타 종속 마이크로서비스를 검색할 수 있습니다.

#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient 주석

애플리케이션 소스 코드에 다음 주석을 추가합니다.
```java
@EnableDiscoveryClient
```
예를 들어 이전 예제의 piggymetrics 애플리케이션을 참조하세요.
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>분산 구성

분산 구성을 사용하도록 설정하려면, pom.xml 파일의 종속성 섹션에 `spring-cloud-config-client` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> 부트스트랩 구성에 `spring.cloud.config.enabled=false`를 지정하지 마세요. 그렇지 않으면 애플리케이션이 구성 서버 작업을 중지합니다.

### <a name="metrics"></a>메트릭

다음과 같이 pom.xml 파일의 종속성 섹션에 `spring-boot-starter-actuator` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 JMX 엔드포인트에서 메트릭을 정기적으로 끌어옵니다. Azure Portal을 사용하여 메트릭을 시각화할 수 있습니다.

 > [!WARNING]
 > `spring.jmx.enabled=true`구성 속성에서를 지정 하십시오. 그렇지 않으면 Azure Portal에서 메트릭을 시각화할 수 없습니다.

### <a name="distributed-tracing"></a>분산 추적

pom.xml 파일의 종속성 섹션에 다음 `spring-cloud-starter-sleuth` 및 `spring-cloud-starter-zipkin` 종속성을 포함합니다.

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 또한, Azure Application Insights 인스턴스가 Azure Spring Cloud 서비스 인스턴스와 작동하도록 설정해야 합니다. Azure 스프링 클라우드에서 Application Insights를 사용 하는 방법에 대 한 자세한 내용은 [분산 추적에 대 한 설명서](spring-cloud-tutorial-distributed-tracing.md)를 참조 하세요.

## <a name="see-also"></a>참고 항목
* [애플리케이션 로그 및 메트릭 분석](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [구성 서버 설정](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Azure Spring Cloud에서 분산 추적 사용](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Spring 빠른 시작 가이드](https://spring.io/quickstart)
* [Spring Boot 설명서](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>다음 단계

이 항목에서는 Azure Spring Cloud에 배포하기 위해 Java Spring 애플리케이션을 구성하는 방법을 알아보았습니다. 구성 서버 인스턴스를 설정 하는 방법을 알아보려면 [구성 서버 인스턴스 설정](spring-cloud-tutorial-config-server.md)을 참조 하세요.

GitHub에서 더 많은 샘플을 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
