---
title: Azure Application Insights에서 Java 웹앱에 대한 성능 모니터링 | Microsoft Docs
description: Application Insights로 Java 웹 사이트의 확장된 성능 및 사용량 모니터링
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ce5f7ab1e6751a9ce68aa2d9c466a112c9cac182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900611"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>종속성, 예외 포착 및 Java 웹앱에서의 메서드 실행 시간 모니터링


[Application Insights로 Java 웹앱을 계측][java]한 경우, Java Agent를 사용하여 코드의 변경 없이 보다 심층적인 정보를 얻을 수 있습니다.

* **종속성:** 애플리케이션이 다음을 포함한 다른 구성 요소에 수행하는 호출에 대한 데이터:
  * HttpClient, OkHttp 및 RestTemplate(Spring)을 통해 만든 **REST 호출**이 캡처됩니다.
  * Jedis 클라이언트를 통한 **Redis** 호출이 캡처됩니다.
  * **[JDBC 호출](https://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/)** - MySQL, SQL Server 및 Oracle DB 명령을 자동으로 캡처합니다. MySQL의 경우 호출 시간이 10초보다 길면 에이전트가 쿼리 계획을 보고합니다.
* **예외 포착:** 코드에서 처리하는 예외에 대한 정보입니다.
* **메서드 실행 시간:** 특정 메서드를 실행하는 데 걸리는 시간에 대한 정보입니다.

Java 에이전트를 사용하려면 사용자의 서버에 설치합니다. [Application Insights Java SDK][java]를 사용하여 웹앱을 계측해야 합니다. 

## <a name="install-the-application-insights-agent-for-java"></a>Java용 Application Insights 에이전트 설치
1. Java 서버를 실행 중인 컴퓨터에서 [에이전트를 다운로드](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)합니다. Application Insights Java SDK 코어 및 웹 패키지와 동일한 버전의 Java Agent 다운로드를 확인하세요.
2. 애플리케이션 서버 시작 스크립트를 편집하여 다음 JVM을 추가합니다.
   
    `javaagent:`*에이전트 JAR 파일에 대한 전체 경로*
   
    예를 들어 Linux 컴퓨터의 Tomcat에서:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. 애플리케이션 서버를 다시 시작합니다.

## <a name="configure-the-agent"></a>에이전트 구성
`AI-Agent.xml` 파일을 만들어 에이전트 JAR 파일과 동일한 폴더에 배치합니다.

xml 파일의 내용을 설정합니다. 다음 예제를 편집하여 원하는 기능을 포함 또는 생략합니다.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>

        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true">
           <!-- Disable Redis or alter threshold call duration above which arguments are sent.
               Defaults: enabled, 10000 ms -->
           <Jedis enabled="true" thresholdInMS="1000"/>

           <!-- Set SQL query duration above which query plan is reported (MySQL, PostgreSQL). Default is 10000 ms. -->
           <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>
        </BuiltIn>

        <!-- Collect data about caught exceptions
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne"
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />
           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String;I)V" />
        </Class>

      </Instrumentation>
    </ApplicationInsightsAgent>

```

개별 메서드에 대한 메서드 타이밍과 예외를 보고할 수 있도록 설정해야 합니다.

기본적으로 `reportExecutionTime`은 true이고 `reportCaughtExceptions`는 false입니다.

## <a name="additional-config-spring-boot"></a>추가 구성 (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

다음을 수행 하는 Azure App Services에 대 한:

* 설정 &gt; 애플리케이션 설정 선택
* 앱 설정 아래에서 새로운 키 값 쌍을 추가합니다.

키: `JAVA_OPTS` 값: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.3.1-SNAPSHOT.jar`

Java 에이전트의 최신 버전에 대 한 확인이 릴리스에 [여기](https://github.com/Microsoft/ApplicationInsights-Java/releases
)합니다. 

에이전트는 d: / home/site/wwwroot에서 종료 되도록 프로젝트에 리소스로 패키지 해야/디렉터리입니다. 으로 이동 하 여 에이전트는 올바른 App Service 디렉터리에 있는지 확인할 수 있습니다 **개발 도구** > **고급 도구** > **디버그 콘솔**사이트 디렉터리의 내용을 검사 하 고 있습니다.    

* 설정을 저장 하 고 앱을 다시 시작 합니다. (이러한 단계에만 적용 Windows에서 실행 중인 앱 서비스입니다.)

> [!NOTE]
> AI-Agent.xml 및 에이전트 jar 파일은 동일한 폴더에 있어야 합니다. 종종 프로젝트의 `/resources` 폴더에 함께 배치됩니다.  

### <a name="spring-rest-template"></a>Spring Rest 템플릿

Application Insights가 Spring의 Rest 템플릿으로 HTTP 호출을 성공적으로 처리하도록 하려면 Apache HTTP 클라이언트를 사용해야 합니다. 기본적으로 Spring의 Rest 템플릿은 Apache HTTP 클라이언트를 사용하도록 구성되지 않습니다. Spring Rest 템플릿의 생성자에서 [HttpComponentsClientHttpRequestfactory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/http/client/HttpComponentsClientHttpRequestFactory.html)를 지정하면 Apache HTTP를 사용합니다.

Spring Beans로 이 작업을 수행하는 방법의 예는 다음과 같습니다. 팩터리 클래스의 기본 설정을 사용하는 매우 간단한 예제입니다.

```java
@bean
public ClientHttpRequestFactory httpRequestFactory() {
return new HttpComponentsClientHttpRequestFactory()
}
@Bean(name = 'myRestTemplate')
public RestTemplate dcrAccessRestTemplate() {
    return new RestTemplate(httpRequestFactory())
}
```

#### <a name="enable-w3c-distributed-tracing"></a>W3C 분산 추적 사용

AI-Agent.xml에 다음을 추가합니다.

```xml
<Instrumentation>
        <BuiltIn enabled="true">
            <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
        </BuiltIn>
    </Instrumentation>
```

> [!NOTE]
> 이전 버전과의 호환성 모드는 기본적으로 사용하도록 설정되어 있으며 enableW3CBackCompat 매개 변수는 선택 사항이므로, 해제하려는 경우에만 사용해야 합니다. 

이상적으로는 모든 서비스 W3C 프로토콜을 지원하는 최신 버전의 SDK로 업데이트된 경우입니다. 가능한 한 빨리 W3C를 지원하는 최신 버전의 SDK로 전환하는 것이 좋습니다.

**[수신](correlation.md#w3c-distributed-tracing) 및 발신(에이전트) 구성이 둘 다 정확히 동일**한지 확인합니다.

## <a name="view-the-data"></a>데이터 보기
Application Insights 리소스에서 원격 종속성과 메서드 실행 시간의 합계는 [성능 타일 아래][metrics]에 나타납니다.

종속성의 개별 인스턴스, 예외 및 메서드 보고서를 찾으려면 [검색][diagnostic]을 엽니다.

[종속성 문제 진단 - 자세한 내용](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>질문이 있으십니까? 문제가 있습니까?
* 데이터가 없나요? [방화벽 예외 설정](../../azure-monitor/app/ip-addresses.md)
* [Java 문제 해결](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
