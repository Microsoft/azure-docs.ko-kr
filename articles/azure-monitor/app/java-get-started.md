---
title: Azure Application Insights로 Java 웹앱 분석 | Microsoft Docs
description: 'Application Insights를 사용하여 Java 웹앱에 대한 애플리케이션 성능 모니터링. '
services: application-insights
documentationcenter: java
author: lgayhardt
manager: carmonm
ms.assetid: 051d4285-f38a-45d8-ad8a-45c3be828d91
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: lagayhar
ms.openlocfilehash: bbf9c162cd52dc94ee820c8597f36f7cbfeace5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691304"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Java 웹 프로젝트에서 Application Insights 시작하기


[Application Insights](https://azure.microsoft.com/services/application-insights/)는 라이브 애플리케이션의 성능 및 사용을 이해하는 데 도움이 되는 확장 가능한 분석 서비스입니다. 이를 사용하여 [자동으로 요청 계측, 종속성 추적 및 성능 카운터를 수집하고](auto-collect-dependencies.md#java), [성능 문제 및 예외를 진단하며](../../azure-monitor/app/detect-triage-diagnose.md), [코드를 작성하여][api] 앱을 통해 사용자가 수행하는 작업을 추적할 수 있습니다. 

![개요 샘플 데이터 스크린샷](./media/java-get-started/overview-graphs.png)

Application Insights는 Linux, Unix 또는 Windows에서 실행되는 Java 앱을 지원합니다.

다음 작업을 수행해야 합니다.

* JRE 버전 1.7 또는 1.8
* [Microsoft Azure](https://azure.microsoft.com/)구독.

Spring 프레임워크를 선호하는 경우 [Application Insights 가이드를 사용하도록 Spring Boot 이니셜라이저 앱을 구성](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)해 보세요.

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights 계측 키 가져오기
1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인합니다.
2. Application Insights 리소스 만들기 Java 웹 애플리케이션에 대한 애플리케이션 종류를 설정합니다.

3. 새 리소스의 계측 키를 찾습니다. 코드 프로젝트에 이 키를 곧바로 붙여넣어야 합니다.

    ![새 리소스 개요에서 속성을 클릭하고 계측 키 복사](./media/java-get-started/instrumentation-key-001.png)

## <a name="2-add-the-application-insights-sdk-for-java-to-your-project"></a>2. 프로젝트에 Java용 Aplication Insights SDK 추가
*프로젝트에 적합한 방법을 선택합니다.*

#### <a name="if-youre-using-maven-a-namemaven-setup-"></a>Maven을 사용하는 경우... <a name="maven-setup" />
빌드에 Maven을 사용하도록 프로젝트가 이미 설정된 경우 pom.xml 파일에 다음 코드를 병합합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

```XML

    <repositories>
       <repository>
          <id>central</id>
          <name>Central</name>
          <url>http://repo1.maven.org/maven2</url>
       </repository>
    </repositories>

    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web</artifactId>
        <!-- or applicationinsights-core for bare API -->
        <version>[2.0,)</version>
      </dependency>
    </dependencies>
```

* *빌드 또는 체크섬 유효성 검사 오류가 있나요?* `<version>2.0.n</version>`과(와) 같은 특정 버전을 사용해 봅니다. [SDK 릴리스 정보](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) 또는 [Maven 아티팩트](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights)에서 최신 버전을 찾을 수 있습니다.
* *새 SDK로 업데이트해야 하는 경우* 프로젝트의 종속성을 새로 고칩니다.

#### <a name="if-youre-using-gradle-a-namegradle-setup-"></a>Gradle을 사용하는 경우... <a name="gradle-setup" />
빌드에 Gradle을 사용하도록 프로젝트가 이미 설정된 경우 다음 코드를 build.gradle 파일에 병합합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

```gradle

    repositories {
      mavenCentral()
    }

    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web', version: '2.+'
      // or applicationinsights-core for bare API
    }
```

#### <a name="if-youre-using-eclipse-to-create-a-dynamic-web-project-"></a>Eclipse를 사용하여 동적 웹 프로젝트를 만드는 경우...
[Java 플러그인용 Application Insights SDK][eclipse]를 사용합니다. 참고: 이 플러그 인을 사용하여 Application insights를 신속하게 실행하더라도(Maven/Gradle을 사용하지 않는다고 가정함)종속성 관리 시스템이 아닙니다. 이와 같이 플러그 인을 업데이트하더라도 프로젝트에 Application Insights 라이브러리를 자동으로 업데이트하지 않습니다.

* *빌드 또는 체크섬 유효성 검사 오류가 있나요?* `version:'2.0.n'`과(와) 같은 특정 버전을 사용해 봅니다. [SDK 릴리스 정보](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) 또는 [Maven 아티팩트](https://search.maven.org/#search%7Cga%7C1%7Capplicationinsights)에서 최신 버전을 찾을 수 있습니다.
* *새 SDK로 업데이트하려면* 프로젝트의 종속성을 새로 고칩니다.

#### <a name="otherwise-if-you-are-manually-managing-dependencies-"></a>그렇지 않으면 수동으로 종속성을 관리하는 경우...
[최신 버전](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)을 다운로드하고 필요한 파일을 프로젝트에 복사하여 이전 버전을 교체합니다.

### <a name="questions"></a>질문...
* *`-core` 및 `-web` 구성 요소 간에는 어떤 관계가 있나요?*
  * `applicationinsights-core` 는 최소한의 API를 제공합니다. 이 구성 요소는 항상 필요합니다.
  * `applicationinsights-web` 은 HTTP 요청 수와 응답 시간을 추적하는 메트릭을 제공합니다. 사용자가 원격 분석 자동 수집을 원하지 않는 경우 이 구성 요소를 생략할 수 있습니다. 예를 들어 사용자 자신의 코드를 작성하려는 경우입니다.
  
* *SDK를 최신 버전으로 업데이트하려면 어떻게 해야 하나요?*
  * Gradle 또는 Maven을 사용하는 경우...
    * 빌드 파일을 업데이트하여 최신 버전을 지정하거나 Gradle/Maven의 와일드 카드 구문을 사용하여 최신 버전을 자동으로 포함합니다. 그런 다음, 프로젝트의 종속성을 새로 고칩니다. 와일드 카드 구문은 [Gradle](#gradle-setup) 또는 [Maven](#maven-setup)에 대한 위의 예제에서 볼 수 있습니다.
  * 종속성을 수동으로 관리하는 경우...
    * 최신 버전의 [Java용 Application Insights SDK](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)를 다운로드하여 이전 버전을 대체합니다. 변경 내용은 [SDK 릴리스 정보](https://github.com/Microsoft/ApplicationInsights-Java#release-notes)에 설명되어 있습니다.

## <a name="3-add-an-applicationinsightsxml-file"></a>3. ApplicationInsights.xml 파일 추가
ApplicationInsights.xml을 프로젝트의 리소스 폴더에 추가하거나 ApplicationInsights.xml이 프로젝트의 배포 클래스 경로에 추가되었는지 확인합니다. 다음 XML을 복사합니다.

Azure 포털에서 가져온 계측 키를 대체합니다.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->
      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->
      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

필요에 따라 애플리케이션에 액세스할 수 있는 위치에 구성 파일이 있으면 됩니다.  `-Dapplicationinsights.configurationDirectory` 시스템 속성은 ApplicationInsights.xml을 포함하는 디렉터리를 지정합니다. 예를 들어 `E:\myconfigs\appinsights\ApplicationInsights.xml`에 있는 구성 파일은 `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` 속성을 사용하여 구성됩니다.

* 계측 키는 원격 분석의 모든 항목과 함께 전송되며 리소스에서 표시하도록 Application Insights에 알려줍니다.
* HTTP 요청 구성 요소는 선택 사항입니다. 자동으로 포털에 요청 및 응답 시간에 대한 원격 분석을 보냅니다.
* 이벤트 상관 관계는 HTTP 요청 구성 요소에 추가됩니다. 이는 서버가 수신하는 요청마다 식별자를 할당하며 'Operation.Id' 속성으로 원격 분석의 모든 항목에 이 식별자를 속성으로 추가합니다. [진단 검색][diagnostic]에서 필터를 설정하여 각 요청과 연결된 원격 분석의 상관 관계를 지정할 수 있습니다.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>계측 키를 설정하는 다른 방법
Application Insights SDK는 다음 순서로 키를 찾습니다.

1. 시스템 속성: -DAPPLICATION_INSIGHTS_IKEY=your_ikey
2. 환경 변수: APPLICATION_INSIGHTS_IKEY
3. 구성 파일: ApplicationInsights.xml

또한 [코드로 설정](../../azure-monitor/app/api-custom-events-metrics.md#ikey)할 수 있습니다.

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="4-add-an-http-filter"></a>4. HTTP 필터 추가
마지막 구성 단계는 HTTP 요청 구성 요소가 각 웹 요청을 로그하도록 허용합니다. (완전한 API를 원하는 경우에는 요청되지 않습니다.)

### <a name="spring-boot-applications"></a>Spring Boot 애플리케이션
구성 클래스에서 Application Insights `WebRequestTrackingFilter`를 등록합니다.

```Java
package <yourpackagename>.configurations;

import javax.servlet.Filter;

import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.core.Ordered;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import com.microsoft.applicationinsights.TelemetryConfiguration;
import com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter;

@Configuration
public class AppInsightsConfig {

    @Bean
    public String telemetryConfig() {
        String telemetryKey = System.getenv("<instrumentation key>");
        if (telemetryKey != null) {
            TelemetryConfiguration.getActive().setInstrumentationKey(telemetryKey);
        }
        return telemetryKey;
    }

    /**
     * Programmatically registers a FilterRegistrationBean to register WebRequestTrackingFilter
     * @param webRequestTrackingFilter
     * @return Bean of type {@link FilterRegistrationBean}
     */
    @Bean
    public FilterRegistrationBean webRequestTrackingFilterRegistrationBean(WebRequestTrackingFilter webRequestTrackingFilter) {
        FilterRegistrationBean registration = new FilterRegistrationBean();
        registration.setFilter(webRequestTrackingFilter);
        registration.addUrlPatterns("/*");
        registration.setOrder(Ordered.HIGHEST_PRECEDENCE + 10);
        return registration;
    }


    /**
     * Creates bean of type WebRequestTrackingFilter for request tracking
     * @param applicationName Name of the application to bind filter to
     * @return {@link Bean} of type {@link WebRequestTrackingFilter}
     */
    @Bean
    @ConditionalOnMissingBean

    public WebRequestTrackingFilter webRequestTrackingFilter(@Value("${spring.application.name:application}") String applicationName) {
        return new WebRequestTrackingFilter(applicationName);
    }


}
```

> [!NOTE]
> Spring Boot 1.3.8 이하를 사용하는 경우 FilterRegistrationBean을 아래 줄로 바꿉니다.

```Java
    import org.springframework.boot.context.embedded.FilterRegistrationBean;
```

이 클래스는 `WebRequestTrackingFilter`가 http 필터 체인에서 첫 번째 필터가 되도록 구성합니다. 또한 사용 가능한 경우 운영 체제 환경 변수에서 계측 키를 끌어옵니다.

> Spring Boot 애플리케이션이고 고유한 Spring MVC가 구성되었기 때문에 Spring MVC를 구성하지 않고 웹 http 필터 구성을 사용합니다. Spring MVC 특정 구성은 아래 섹션을 참조하세요.

### <a name="applications-using-webxml"></a>Web.xml를 사용하는 애플리케이션
프로젝트에서 web.xml 파일을 찾아 열고, 애플리케이션 필터가 구성된 웹앱 노드 아래에 다음 코드를 병합합니다.

가장 정확한 결과를 얻으려면 필터를 다른 모든 필터 전에 매핑해야 합니다.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>

   <!-- This listener handles shutting down the TelemetryClient when an application/servlet is undeployed. -->
    <listener>
      <listener-class>com.microsoft.applicationinsights.web.internal.ApplicationInsightsServletContextListener</listener-class>
    </listener>
```

#### <a name="if-youre-using-spring-web-mvc-31-or-later"></a>Spring Web MVC 3.1 이상을 사용하는 경우
Application Insights 패키지를 포함하도록 *-servlet.xml에서 이러한 요소 편집:

```XML

    <context:component-scan base-package=" com.springapp.mvc, com.microsoft.applicationinsights.web.spring"/>

    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/**"/>
            <bean class="com.microsoft.applicationinsights.web.spring.RequestNameHandlerInterceptorAdapter" />
        </mvc:interceptor>
    </mvc:interceptors>
```

#### <a name="if-youre-using-struts-2"></a>Struts 2를 사용하는 경우
Struts 구성 파일에 이 항목 추가(일반적으로 struts.xml 또는 struts-default.xml이라고 함):

```XML

     <interceptors>
       <interceptor name="ApplicationInsightsRequestNameInterceptor" class="com.microsoft.applicationinsights.web.struts.RequestNameInterceptor" />
     </interceptors>
     <default-interceptor-ref name="ApplicationInsightsRequestNameInterceptor" />
```

기본 스택에 정의된 인터셉터가 있는 경우 해당 인터셉터를 스택에 추가하면 됩니다.

## <a name="5-run-your-application"></a>5. 애플리케이션 실행
응용 프로그램을 디버그 모드로 개발 컴퓨터에서 실행하거나 서버에 게시합니다.

## <a name="6-view-your-telemetry-in-application-insights"></a>6. Application Insights에서 원격 분석 보기
[Microsoft Azure 포털](https://portal.azure.com)의 Application Insights 리소스로 돌아갑니다.

HTTP 요청 데이터가 개요 블레이드에 표시됩니다. (없는 경우 몇 초 정도 기다린 다음 새로고침을 클릭합니다.)

![개요 샘플 데이터 스크린샷](./media/java-get-started/overview-graphs.png)

[메트릭에 대해 자세히 알아봅니다.][metrics]

차트를 클릭하면 더 자세한 집계된 메트릭을 볼 수 있습니다.

![차트를 사용 하 여 application Insights 오류 창](./media/java-get-started/006-barcharts.png)

> Application Insights는 MVC 애플리케이션에 대한 HTTP 요청 형식을 다음과 같이 가정합니다. `VERB controller/action` 예를들어, `GET Home/Product/f9anuh81`, `GET Home/Product/2dffwrf5` 및 `GET Home/Product/sdf96vws`은(는) `GET Home/Product`(으)로 그룹화됩니다. 이 그룹화를 통해 요청 수와 같은 의미 있는 집계 및 요청에 대한 평균 실행 시간을 사용할 수 있습니다.
>
>

### <a name="instance-data"></a>인스턴스 데이터
특정 요청 유형을 클릭하여 개별 인스턴스를 확인합니다.

![특정 샘플 보기로 드릴](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>분석: 강력한 쿼리 언어
더 많은 데이터가 누적되면 쿼리를 실행하여 데이터를 집계하고 개별 인스턴스를 찾을 수 있습니다.  [분석](../../azure-monitor/app/analytics.md) 은 성능 및 사용 이해 및 진단 목적 모두에 강력한 도구입니다.

![분석 예제](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. 서버에 앱 설치
이제 서버에 앱을 게시하고, 사람들이 사용하게 한 다음 포털에 표시되는 원격 분석을 확인합니다.

* 방화벽에서 애플리케이션이 다음 포트에 원격 분석을 보내도록 허용하는지 확인합니다.

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* 나가는 트래픽이 방화벽을 통해 라우팅되어야 하는 경우 시스템 속성 `http.proxyHost` 및 `http.proxyPort`를 정의합니다.

* Windows 서버에 다음을 설치합니다.

  * [Microsoft Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=40784)

    (이 구성 요소를 통해 성능 카운터를 사용할 수 있게 됩니다.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service 구성 (Spring Boot)

Windows에서 실행 중인 spring Boot 앱에는 Azure App Service에서 실행 하려면 추가 구성이 필요 합니다. 수정할 **web.config** 다음 줄을 추가 합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>예외 및 요청 실패
처리 되지 않은 예외를 자동으로 수집 됩니다.

다른 예외에 대한 데이터를 수집하려면 다음 두 옵션을 사용합니다.

* [사용자 코드에 trackException()에 대한 호출을 삽입합니다][apiexceptions].
* [서버에 Java 에이전트를 설치합니다](java-agent.md). 감시 방법을 지정할 수 있습니다.

## <a name="monitor-method-calls-and-external-dependencies"></a>메서드 호출 및 외부 종속성 모니터링
[Java 에이전트를 설치](java-agent.md) 하여 지정된 내부 메서드 및 JDBC를 통해 수행한 호출을 타이밍 데이터와 함께 기록합니다.

## <a name="w3c-distributed-tracing"></a>W3C 분산 추적

Application Insights Java SDK는 이제 [W3C 분산 추적](https://w3c.github.io/trace-context/)을 지원합니다.

수신 SDK 구성은 [상관 관계](correlation.md#w3c-distributed-tracing) 관련 문서에 자세히 설명되어 있습니다.

발신 SDK 구성은 [Ai-agent.xml](java-agent.md) 파일에서 정의됩니다.

## <a name="performance-counters"></a>성능 카운터
오픈 **조사**를 **메트릭**성능 카운터의 범위를 참조 하세요.

![선택한 프로세스 전용 바이트를 사용 하 여 메트릭 창 스크린샷](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>성능 카운터 수집 사용자 지정
성능 카운터의 표준 집합 수집을 사용하지 않으려면 ApplicationInsights.xml 파일의 루트 노드 아래에 다음 코드를 추가합니다.

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>추가 성능 카운터 수집
추가 성능 카운터가 수집되도록 지정할 수 있습니다.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX 카운터(Java Virtual Machine을 통해 노출됨)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` - Application Insights 포털에서 표시되는 이름입니다.
* `objectName` – JMX 개체 이름입니다.
* `attribute` - 가져올 JMX 개체 이름의 특성입니다.
* `type` (선택 사항) - JMX 개체 특성의 유형:
  * 기본값: int 또는 long과 같은 단순 유형입니다.
  * `composite`: 성능 카운터 데이터는 'Attribute.Data' 형식입니다.
  * `tabular`: 성능 카운터 데이터는 표 행 형식입니다.

#### <a name="windows-performance-counters"></a>Windows 성능 카운터
각 [Windows 성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) 는 한 범주의 구성원입니다(필드가 클래스의 구성원인 것과 동일한 방식). 범주는 전역일 수 있으며, 번호 또는 이름이 지정된 인스턴스를 가질 수도 있습니다.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName - Application Insights 포털에서 표시되는 이름입니다.
* categoryName – 이 성능 카운터와 관련된 성능 카운터 범주(성능 개체)입니다.
* counterName – 성능 카운터의 이름입니다.
* instanceName – 성능 카운터 범주 인스턴스입니다. 또는 범주가 단일 인스턴스를 포함하는 경우 빈 문자열("")의 이름입니다. categoryName이 프로세스이며 수입하려는 성능 카운터는 앱이 실행 중인 현재 JVM 프로세스에서 오는 경우, `"__SELF__"`을(를) 지정합니다.

### <a name="unix-performance-counters"></a>Unix 성능 카운터
* [Application Insights 플러그 인과 함께 collectd를 설치](java-collectd.md) 하여 광범위한 시스템 및 네트워크 데이터를 얻을 수 있습니다.

## <a name="local-forwarder"></a>로컬 전달자

[로컬 전달자](https://docs.microsoft.com/azure/application-insights/local-forwarder)는 다양한 SDK 및 프레임워크에서 Application Insights 또는 [OpenCensus](https://opencensus.io/) 원격 분석을 수집하고 Application Insights로 경로를 지정하는 에이전트입니다. Windows 및 Linux에서 실행할 수 있습니다.

```xml
<Channel type="com.microsoft.applicationinsights.channel.concrete.localforwarder.LocalForwarderTelemetryChannel">
<DeveloperMode>false</DeveloperMode>
<EndpointAddress><!-- put the hostname:port of your LocalForwarder instance here --></EndpointAddress>
<!-- The properties below are optional. The values shown are the defaults for each property -->
<FlushIntervalInSeconds>5</FlushIntervalInSeconds><!-- must be between [1, 500]. values outside the bound will be rounded to nearest bound -->
<MaxTelemetryBufferCapacity>500</MaxTelemetryBufferCapacity><!-- units=number of telemetry items; must be between [1, 1000] -->
</Channel>
```

SpringBoot 스타터를 사용하는 경우 구성 파일(application.properties)에 다음을 추가합니다.

```yml
azure.application-insights.channel.local-forwarder.endpoint-address=<!--put the hostname:port of your LocalForwarder instance here-->
azure.application-insights.channel.local-forwarder.flush-interval-in-seconds=<!--optional-->
azure.application-insights.channel.local-forwarder.max-telemetry-buffer-capacity=<!--optional-->
```

기본값은 SpringBoot application.properties 및 applicationinsights.xml 구성에 모두 동일합니다.

## <a name="get-user-and-session-data"></a>사용자 및 세션 데이터 가져오기
이제 웹 서버에서 원격 분석을 보내려 합니다. 애플리케이션을 전체적으로 파악하기 위해 모니터링을 추가할 수 있습니다.

* [웹 페이지에 원격 분석을 추가][usage]하여 페이지 보기 및 사용자 메트릭을 모니터링합니다.
* [웹 테스트를 설정][availability]하여 애플리케이션이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.

## <a name="capture-log-traces"></a>로그 추적 캡처
Application Insights를 사용하여 Log4J, Logback 또는 다른 로깅 프레임워크에서 로그를 분리 및 분할할 수 있습니다. HTTP 요청 및 기타 원격 분석과 로그를 연관지을 수 있습니다. [방법을 알아봅니다][javalogs].

## <a name="send-your-own-telemetry"></a>사용자 고유의 원격 분석 전송
이제 SDK를 설치한 했으므로 API를 사용하여 사용자 고유의 원격 분석을 전송할 수 있습니다.

* [사용자 지정 이벤트 및 메트릭을 추적][api]하여 사용자가 애플리케이션으로 수행하는 것을 알아볼 수 있습니다.
* [이벤트 및 로그를 검색][diagnostic]하여 문제를 진단할 수 있습니다.

## <a name="availability-web-tests"></a>가용성 웹 테스트
Application Insights는 일정한 간격으로 웹 사이트를 테스트하여 잘 실행되며 제대로 응답하는지 확인할 수 있습니다.

[가용성 웹 테스트를 설정 하는 방법을 자세히 알아봅니다.][availability]

## <a name="questions-problems"></a>질문이 있으십니까? 문제가 있습니까?
[Java 문제 해결](java-troubleshoot.md)

## <a name="next-steps"></a>다음 단계
* [종속성 호출 모니터링](java-agent.md)
* [Unix 성능 카운터 모니터링](java-collectd.md)
* [웹 페이지에 모니터링](javascript.md)을 추가하여 페이지 로드 시간, AJAX 호출, 브라우저 예외를 모니터링합니다.
* [사용자 지정 원격 분석](../../azure-monitor/app/api-custom-events-metrics.md)을 작성하여 브라우저 또는 서버에서 사용량을 추적합니다.
* [대시보드](../../azure-monitor/app/app-insights-dashboards.md)를 만들어서 시스템을 모니터링하기 위한 키 차트를 가져옵니다.
* 앱의 원격 분석을 통해 강력한 쿼리를 수행하려면 [분석](../../azure-monitor/app/analytics.md)을 사용합니다.
* 자세한 내용은 [Java 개발자용 Azure](/java/azure)를 방문하세요.

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: ../../azure-monitor/learn/java-quick-start.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
