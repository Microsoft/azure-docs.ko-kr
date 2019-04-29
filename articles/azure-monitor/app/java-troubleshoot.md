---
title: Java 웹 프로젝트에서 Application Insights 문제 해결
description: 문제 해결 가이드 - Application Insights를 사용하여 라이브 Java 앱을 모니터링합니다.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2018
ms.author: mbullwin
ms.openlocfilehash: eaade5f9ec9db7e8d224305147dafc264916d9c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899502"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Java용 Application Insights 문제 해결과 질문 및 답변
[Java의 Azure Application Insights][java]와 관련된 질문이나 문제가 있나요? 다음은 몇 가지 팁입니다.

## <a name="build-errors"></a>빌드 오류
**Eclipse 또는 Intellij Idea에서 Maven 또는 Gradle을 통해 Application Insights SDK를 추가할 때 빌드 또는 체크섬 유효성 검사 오류가 표시됩니다.**

* 종속성 `<version>` 요소가 와일드카드 문자(예: (Maven) `<version>[2.0,)</version>` 또는 (Gradle) `version:'2.0.+'`)로 패턴을 사용하는 경우 `2.0.1`과 같은 특정 버전을 대신 지정해 봅니다. 최신 버전은 [릴리스 정보](https://github.com/Microsoft/ApplicationInsights-Java/releases) 를 참조하세요.

## <a name="no-data"></a>데이터 없음
**Application Insights를 추가하고 내 앱을 실행했는데 포털에 데이터가 표시되지 않습니다.**

* 잠시 기다린 후 새로 고침을 클릭합니다. 차트는 주기적으로 새로 고쳐지지만 수동으로 새로 고칠 수도 있습니다. 새로 고침 간격은 차트의 시간 범위에 따라 달라집니다.
* 계측 키가 ApplicationInsights.xml 파일(프로젝트의 리소스 폴더에 있음)에 정의되어 있거나 환경 변수로 구성되었는지 확인합니다.
* xml 파일에 `<DisableTelemetry>true</DisableTelemetry>` 노드가 없는지 확인합니다.
* 방화벽에서 dc.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다. 최신 버전은 [방화벽 예외의 전체 목록](../../azure-monitor/app/ip-addresses.md)
* Microsoft Azure 시작 보드에서 서비스 상태 맵을 살펴보세요. 어떤 경고 표시가 있는 경우 정상으로 돌아갈 때까지 기다린 후 Application Insights 애플리케이션 블레이드를 닫고 다시 엽니다.
* ApplicationInsights.xml 파일(프로젝트의 리소스 폴더에 있음)의 루트 노드 아래에 `<SDKLogger />` 요소를 추가하여 IDE 콘솔 창에 기록을 켜고, 의심스러운 로그에 대해 앞에 AI: INFO/WARN/ERROR가 붙은 항목을 확인합니다.
* 콘솔의 출력 메시지에서 “구성 파일을 찾았습니다”라는 문을 찾아 ApplicationInsights.xml 파일이 Java SDK에 의해 성공적으로 로드되었음을 확인합니다.
* 구성 파일이 없으면 출력 메시지를 확인하여 구성 파일이 검색되고 있는 위치를 확인하고, ApplicationInsights.xml이 그러한 검색 위치 중 한 위치에 있는지 확인합니다. 일반적으로 구성 파일을 Application Insights SDK JAR 주위에 배치할 수 있습니다. 예: Tomcat에서는 WEB-INF/classes 폴더를 의미합니다. 개발 중에 웹 프로젝트의 리소스 폴더에 ApplicationInsights.xml을 배치할 수 있습니다.
* SDK의 알려진 문제에 대해서는 [GitHub 문제 페이지](https://github.com/Microsoft/ApplicationInsights-Java/issues)를 참조하세요.
* 버전 충돌 문제를 방지하려면 동일한 버전의 Application Insights 코어, 웹, 에이전트 및 로깅 어펜더를 사용합니다.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>데이터를 보는 데 중지되었습니다.
* [상태 블로그](https://blogs.msdn.com/b/applicationinsights-status/)를 참조하세요.
* 데이터 요소의 월간 할당량에 도달했습니까? 설정/할당량 및 가격을 열어 알아봅니다. 그렇다면 계획을 업그레이드하거나 추가 용량에 대한 비용을 지불할 수 있습니다. [가격 체계](https://azure.microsoft.com/pricing/details/application-insights/)를 참조하세요.
* 최근에 SDK를 업그레이드했나요? 고유한 SDK jar만 프로젝트 디렉터리 내부에 있는지 확인합니다. 두 개의 서로 다른 SDK 버전이 있으면 안 됩니다.
* 올바른 AI 리소스를 보고 있나요? 원격 분석이 필요한 리소스에 애플리케이션의 iKey를 일치합니다. 동일해야 합니다.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>기대한 모든 데이터가 표시되지 않는 경우
* 사용량 및 예상 비용 페이지를 열고 [샘플링](../../azure-monitor/app/sampling.md)이 작동 중인지 확인합니다. (100% 전송이란 샘플링을 사용하지 않는다는 의미입니다.) Application Insights 서비스는 앱에서 도착하는 원격 분석의 일부만 허용하도록 설정할 수 있습니다. 이렇게 하면 원격 분석의 월간 할당량 내로 유지하는 데 도움이 됩니다.
* SDK 샘플링이 켜져 있나요? 켜져 있으면 적용 가능한 모든 유형에서 지정된 비율로 데이터가 샘플링됩니다.
* 이전 버전의 Java SDK를 실행 중인가요? 버전 2.0.1부터 로컬 드라이브의 데이터 지속성뿐 아니라 간헐적인 네트워크 및 백 엔드 오류를 처리하는 내결함성 메커니즘이 도입되었습니다.
* 과도한 원격 분석 때문에 제한되나요? INFO 로깅을 켜면 “앱이 제한되었습니다.”라는 로그 메시지가 표시됩니다. 현재 제한은 32K 원격 분석 항목/초입니다.

### <a name="java-agent-cannot-capture-dependency-data"></a>Java 에이전트가 종속성 데이터를 캡처할 수 없음
* [Java 에이전트 구성](java-agent.md)에 따라 Java 에이전트를 구성했나요?
* Java 에이전트 jar 및 AI-Agent.xml 파일이 동일한 폴더에 있는지 확인합니다.
* 자동 수집하려는 종속성에서 자동 수집이 지원되는지 확인합니다. 현재 MySQL, MsSQL, Oracle DB 및 Azure Cache for Redis 종속성 수집만 지원됩니다.
* JDK 1.7 또는 1.8을 사용 중인가요? 현재 JDK 9에서 종속성 수집을 지원하지 않습니다.

## <a name="no-usage-data"></a>사용 현황 데이터 없음
**요청 및 응답 시간에 대한 데이터는 표시되는데 페이지 보기, 브라우저 또는 사용자 데이터는 표시되지 않습니다.**

서버에서 원격 분석을 보내도록 앱을 설정했습니다. 이제 다음 단계로 [웹 브라우저에서 원격 분석을 보내도록 웹 페이지를 설정][usage]해야 합니다.

또는 클라이언트가 [휴대폰이나 기타 디바이스][platforms]의 앱인 경우 해당 디바이스에서 원격 분석을 보낼 수 있습니다.

동일한 계측 키를 사용하여 클라이언트 및 서버 원격 분석을 둘 다 설정합니다. 데이터가 동일한 Application Insights 리소스에 표시되며, 클라이언트와 서버의 이벤트에 상관 관계를 지정할 수 있습니다.


## <a name="disabling-telemetry"></a>원격 분석 사용 안 함
**원격 분석 수집을 사용하지 않도록 설정하려면 어떻게 해야 하나요?**

코드:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**또는**

ApplicationInsights.xml(프로젝트의 리소스 폴더에 있음)을 업데이트합니다. 루트 노드 아래에 다음을 추가합니다.

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML 메서드를 사용하여 값 변경 시 애플리케이션을 다시 시작해야 합니다.

## <a name="changing-the-target"></a>대상 변경
**내 프로젝트에서 데이터를 보내는 Azure 리소스를 변경하려면 어떻게 해야 하나요?**

* [새 리소스의 계측 키를 가져옵니다.][java]
* Azure Toolkit for Eclipse를 사용하여 프로젝트에 Application Insights를 추가한 경우 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고**Azure**, **Application Insights 구성**을 차례로 선택한 다음, 키를 변경합니다.
* 계측 키를 환경 변수로 구성한 경우, 환경 변수 값을 새 iKey로 업데이트합니다.
* 그렇지 않으면 프로젝트의 리소스 폴더에 있는 ApplicationInsights.xml에서 키를 업데이트합니다.

## <a name="debug-data-from-the-sdk"></a>SDK에서 데이터 디버깅

**SDK가 수행하는 작업을 찾는 방법**

API의 상황에 대한 자세한 정보를 가져오려면 ApplicationInsights.xml 구성 파일의 루트 노드에 `<SDKLogger/>`를 추가합니다.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

파일에 출력하도록 로거에 지시할 수도 있습니다.

```XML
  <SDKLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Spring Boot Starter

Application Insights Spring Boot Starter를 사용 하 여 Spring Boot 앱을 사용 하 여 SDK 로깅을 사용 하려면 다음을 추가 합니다 `application.properties` 파일.:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Java Agent

업데이트 JVM 에이전트 로깅을 사용 하도록 설정 합니다 [Ai-agent.xml 파일이](java-agent.md)합니다.

```xml
<AgentLogger type="FILE">
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

## <a name="the-azure-start-screen"></a>Azure 시작 화면
**[Azure Portal](https://portal.azure.com)을 보고 있습니다. 맵에 내 앱에 대한 정보가 표시되나요?**

아니요, 전 세계 Azure 서버의 상태가 표시됩니다.

*Azure 시작 보드(홈 화면)에서 내 앱에 대한 데이터를 찾으려면 어떻게 해야 하나요?*

[Application Insights에 대해 앱을 설정][java]한 경우 찾아보기를 클릭하고 Application Insights를 선택한 다음, 앱을 위해 만든 앱 리소스를 선택합니다. 이후에 더 빨리 액세스하기 위해 앱을 시작 보드에 고정할 수 있습니다.

## <a name="intranet-servers"></a>인트라넷 서버
**내 인트라넷의 서버를 모니터링할 수 있나요?**

예, 서버가 공용 인터넷을 통해 Application Insights 포털에 원격 분석을 보낼 수 있는 경우 가능합니다.

방화벽에서 dc.services.visualstudio.com 및 f5.services.visualstudio.com으로 나가는 트래픽에 대해 TCP 포트 80 및 443을 열어야 할 수 있습니다.

## <a name="data-retention"></a>데이터 보존
**데이터가 포털에 얼마나 오래 보존되나요? 안전한가요?**

[데이터 보존 및 개인 정보][data]를 참조하세요.

## <a name="debug-logging"></a>디버그 로깅
Application Insights는 `org.apache.http`를 사용합니다. 이 모듈은 Application Insights 코어 jar 내에서 `com.microsoft.applicationinsights.core.dependencies.http` 네임스페이스 아래로 위치가 변경되었습니다. 이를 통해 Application Insights는 같은 `org.apache.http`의 다른 버전이 하나의 코드 베이스에 존재하는 시나리오를 처리할 수 있습니다.

>[!NOTE]
>앱의 모든 네임스페이스에 대해 DEBUG 수준의 로깅을 사용하도록 설정하면 `org.apache.http`를 포함하여 실행 중인 모든 모듈이 `com.microsoft.applicationinsights.core.dependencies.http`로 이름이 바뀝니다. Application Insights는 로그 호출이 Apache 라이브러리에 의해 이루어지기 때문에 이러한 호출에 대해서는 필터링을 적용할 수 없습니다. DEBUG 수준 로깅은 상당한 양의 로그 데이터를 생성하므로 실제 프로덕션 인스턴스에는 권장되지 않습니다.


## <a name="next-steps"></a>다음 단계
**내 Java 서버 앱에 대해 Application Insights를 설정했습니다. 다른 어떤 작업을 할 수 있나요?**

* [웹 페이지의 가용성 모니터링][availability]
* [웹 페이지 사용 모니터링][usage]
* [디바이스 앱의 사용 추적 및 문제 진단][platforms]
* [코드를 작성하여 앱의 사용 추적][track]
* [진단 로그 캡처][javalogs]

## <a name="get-help"></a>도움말 보기
* [스택 오버플로](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [GitHub에서 문제 제출](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

