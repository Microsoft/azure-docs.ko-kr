---
title: Java 웹앱에서 Azure Application Insights 원격 분석 필터링 | Microsoft Docs
description: 모니터링하지 않아도 되는 이벤트를 필터링하여 원격 분석 트래픽을 줄입니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: ee50a0e9c7fca8f01f12b3508c86d901b5315120
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60897117"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Java 웹앱에서 원격 분석 필터링

필터는 [Java 웹앱이 Application Insights로 보내는](java-get-started.md) 원격 분석을 선택하는 방법을 제공합니다. 사용할 수 있는 몇 가지 기본 필터가 있으며 사용자 지정 필터를 직접 작성할 수도 있습니다.

기본 필터는 다음과 같습니다.

* 추적 심각도 수준
* 특정 URL, 키워드 또는 응답 코드
* 빠른 응답(앱이 빠르게 응답한 요청)
* 특정 이벤트 이름

> [!NOTE]
> 필터는 앱 메트릭의 타이밍 스큐를 발생합니다. 예를 들어 느린 응답을 진단하기 위해서는 빠른 응답 시간을 삭제하는 필터를 설정하도록 결정할 수 있습니다. 하지만 Application Insights에서 보고하는 평균 응답 시간이 실제 속도보다 느리고 요청 수가 실제 수보다 작을 것이라는 점을 알아야 합니다.
> 이것이 문제가 될 경우 대신 [샘플링](../../azure-monitor/app/sampling.md)을 사용합니다.

## <a name="setting-filters"></a>필터 설정

ApplicationInsights.xml에서 다음 예제와 같이 `TelemetryProcessors` 섹션을 추가합니다.


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[전체 기본 제공 프로세서 집합을 검사](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor)합니다.

## <a name="built-in-filters"></a>기본 제공 필터

### <a name="metric-telemetry-filter"></a>메트릭 원격 분석 필터

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` - 쉼표로 구분된 사용자 지정 메트릭 목록입니다.


### <a name="page-view-telemetry-filter"></a>페이지 보기 원격 분석 필터

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` - 기간은 페이지를 로드하는 데 걸린 시간을 나타냅니다. 이 값으로 설정하는 경우 이 시간보다 더 빠르게 로드된 페이지는 보고되지 않습니다.
* `NotNeededNames` - 쉼표로 구분된 페이지 이름 목록입니다.
* `NotNeededUrls` - 쉼표로 구분된 URL 조각 목록입니다. 예를 들어 `"home"`은 URL에 "home"이 포함된 모든 페이지를 필터링합니다.


### <a name="request-telemetry-filter"></a>요청 원격 분석 필터


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>가상 원본 필터

SyntheticSource 속성에 값이 있는 모든 원격 분석을 필터링합니다. 여기에는 봇, 스파이더 및 가용성 테스트에 대한 요청이 포함됩니다.

모든 가상 요청에 대한 원격 분석을 필터링합니다.


```XML

           <Processor type="SyntheticSourceFilter" />
```

특정 가상 원본에 대한 원격 분석을 필터링합니다.


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` - 쉼표로 구분된 가상 원본 이름 목록입니다.

### <a name="telemetry-event-filter"></a>원격 분석 이벤트 필터

사용자 지정 이벤트를 필터링합니다([TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)를 사용하여 로깅됨).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` - 쉼표로 구분된 이벤트 이름 목록입니다.


### <a name="trace-telemetry-filter"></a>추적 원격 분석 필터

로그 추적을 필터링합니다([TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 또는 [로깅 프레임워크 수집기](java-trace-logs.md)를 사용하여 로깅됨).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` 유효한 값은 다음과 같습니다.
  *  OFF             - 모든 추적 필터링
  *  TRACE           - 필터링하지 않음 추적 수준과 같음
  *  INFO            - TRACE 수준 필터링
  *  WARN            - TRACE 및 INFO 필터링
  *  ERROR           - WARN, INFO, TRACE 필터링
  *  CRITICAL        - CRITICAL를 제외하고 모두 필터링


## <a name="custom-filters"></a>사용자 지정 필터

### <a name="1-code-your-filter"></a>1. 필터 코드

코드에서 `TelemetryProcessor`를 구현하는 클래스를 만듭니다.

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. 구성 파일에서 필터 호출

ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. 필터 (Java Spring) 호출

Spring framework를 기반으로 하는 응용 프로그램을 bean으로 주 응용 프로그램 클래스의 사용자 지정 원격 분석 프로세서를 등록 되어야 합니다. 응용 프로그램을 시작할 때 자동으로 연결 됩니다.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

사용자 고유의 필터 매개 변수를 만들 해야 `application.properties` 및 사용자 지정 필터에 해당 매개 변수를 전달할 Spring Boot 구체화 된 구성 프레임 워크를 활용 합니다. 


## <a name="troubleshooting"></a>문제 해결

*내 필터가 작동하지 않습니다.*

* 유효한 매개 변수 값을 제공했는지 확인합니다. 예를 들어 기간은 정수여야 합니다. 잘못된 값을 설정하면 필터가 무시됩니다. 사용자 지정 필터가 생성자 또는 set 메서드에서 예외를 throw하는 경우 무시됩니다.

## <a name="next-steps"></a>다음 단계

* [샘플링](../../azure-monitor/app/sampling.md) - 메트릭을 스큐하지 않는 대안으로 샘플링을 고려합니다.
