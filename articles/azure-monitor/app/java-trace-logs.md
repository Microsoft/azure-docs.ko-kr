---
title: Azure Application Insights에서 Java 추적 로그 탐색 | Microsoft Docs
description: Application Insights에서 검색 Log4J 또는 Logback 추적 검색
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 614f9a44f7c699be38906ac00e12f523490ce112
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900496"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Application Insights에서 Java 추적 로그 탐색
추적에 Logback 또는 Log4J(v1.2 또는 v2.0)를 사용하는 경우 추적 로그를 살펴보고 검색할 수 있는 Application Insights에 추적 로그를 자동으로 전송할 수 있습니다.

## <a name="install-the-java-sdk"></a>Java SDK 설치

아직 수행하지 않은 경우 지침을 다라 [Java용 Application Insights SDK][java]를 설치합니다.

## <a name="add-logging-libraries-to-your-project"></a>프로젝트에 로깅 라이브러리 추가
*프로젝트에 적합한 방법을 선택합니다.*

#### <a name="if-youre-using-maven"></a>Maven을 사용하는 경우...
빌드에 Maven을 사용하도록 프로젝트가 이미 설정된 경우 pom.xml 파일에 다음 코드 조각을 추가합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Gradle을 사용하는 경우...
빌드에 Gradle을 사용하도록 프로젝트가 이미 설정된 경우 다음 줄 중 하나를 build.gradle 파일의 `dependencies` 그룹에 추가합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>기타...
지침에 따라 Application Insights Java SDK를 수동으로 설치하고 적절한 어펜더에 해당하는 jar을 다운로드한 다음 프로젝트에 추가합니다. jar을 다운로드하려면 Maven Central 페이지로 이동하여 다운로드 섹션에서 'jar' 링크를 클릭합니다.

| 로거 | 다운로드 | 라이브러리 |
| --- | --- | --- |
| Logback |[Logback 어펜더 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 어펜더 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Log4J v1.2 어펜더 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>로깅 프레임워크에 어펜더 추가
추적 가져오기를 시작하려면 관련 코드 조각을 Log4J 및 Logback 구성 파일과 병합합니다. 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights 어펜더는 루트 로거만이 아니라 구성된 모든 로거에 의해 참조될 수 있습니다(위의 코드 샘플에 표시).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Application Insights 포털에서 추적 탐색
이제 Application Insights에 추적을 전송하도록 프로젝트를 구성했으며 [검색][diagnostic] 블레이드의 Application Insights 포털에서 이러한 추적을 보고 검색할 수 있습니다.

로거를 통해 제출된 예외는 포털에 예외 원격 분석으로 표시됩니다.

![Application Insights 포털에서 검색을 엽니다.](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>다음 단계
[진단 검색][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


