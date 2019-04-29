---
title: 웹 애플리케이션 성능 모니터링 - Azure Application Insights | Microsoft Docs
description: devOps 주기에 Application Insights를 적용하는 방법
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: 24b0bc01b5cb4f1d2696a7c9526d586c9b42d0fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899715"
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Application Insights로 웹앱 및 서비스 심층 진단
## <a name="why-do-i-need-application-insights"></a>Application Insights가 필요한 이유는 무엇일까요?
Application Insights는 실행 중인 웹앱을 모니터링합니다. 오류와 성능 문제에 대해 알려주고 고객이 앱을 어떻게 사용하는지 분석하도록 도와줍니다. 다양한 플랫폼(예: ASP.NET, Java EE, Node.js)에서 실행되는 앱에 작동하며 클라우드 또는 온-프레미스에서 호스트됩니다. 

![Web Apps을 제공하는 방법의 복잡성 측면](./media/devops/010.png)

최신 애플리케이션은 실행하는 동안 모니터링이 필요합니다. 특히, 대부분의 고객보다 빠르게 오류를 탐지해야 합니다. 또한 치명적이진 않지만 속도를 저해하거나 사용자에게 불편을 초래할 수도 있는 성능 문제들을 검색하고 수정하려고 합니다. 시스템이 만족스럽게 작동될 때 사용자가 어떤 작업을 하는지 알고 싶을 것입니다. 최신 기능을 사용하고 있나요? 잘 사용하고 있는지 궁금할 것입니다.

최신 웹 애플리케이션은 지속적인 업데이트 주기에 맞추어 개발됩니다. 즉, 새로운 기능 및 개선 사항을 릴리스하고, 사용자의 입장에서 잘 작동하는지 관찰하고, 이러한 수집된 정보를 바탕으로 다음 점진적 개발을 계획합니다. 이 주기의 핵심 부분은 관찰 단계입니다. Application Insights는 웹 애플리케이션의 성능과 사용을 모니터링하는 도구를 제공합니다.

이 프로세스에서 가장 중요한 점은 진단입니다. 애플리케이션에 오류가 발생하면 비즈니스에 손실이 발생합니다. 그러므로 모니터링 프레임워크의 주된 역할은 오류를 안정적으로 탐지하고, 즉시 개발자에게 알리고, 문제를 진단하는 데 필요한 정보를 제시하는 것입니다. 바로 Application Insights가 이런 작업을 수행합니다.

### <a name="where-do-bugs-come-from"></a>버그는 어디에서 발생할까요?
일반적으로, 웹 시스템 오류는 구성 문제나 여러 구성 요소 간의 잘못된 상호 작용에서 발생합니다. 그러므로 라이브 사이트 문제를 해결하는 첫걸음은 문제가 발생하는 곳, 즉 어느 구성 요소 또는 관계가 원인인지 찾아내는 것입니다.

지금 지긋이 나이를 먹은 분들은 컴퓨터 한 대에 하나의 프로그램만 실행하던 단순한 시절을 기억하실 겁니다. 그때는 개발자가 출시 전에 철저히 테스트를 했고, 한 번 출시한 프로그램을 다시 들여다 보거나 그에 대해 생각할 필요가 없었습니다. 당시 사용자들은 오랫동안 남아 있는 버그를 참는 수밖에 없었습니다. 

오늘날 상황은 매우 다릅니다. 여러분의 앱은 수많은 디바이스에서 실행되고, 각 디바이스에서 정확히 똑같은 동작을 보장하기 어려워졌습니다. 클라우드에서 앱을 호스팅하면 버그를 신속히 고칠 수 있지만, 끊임없는 경쟁과 자주 새로운 기능을 출시해야 한다는 부담에 시달립니다. 

이러한 환경에서 버그 수를 확고히 통제하는 방법은 자동화된 단위 테스트뿐입니다. 출시할 때마다 모든 것을 직접 다시 테스트할 수는 없습니다. 이제 단위 테스트는 빌드 과정에서 일반화되었습니다. Xamarin Test Cloud와 같은 도구는 여러 브라우저 버전에서 자동 UI 테스트를 제공합니다. 이러한 테스트 체계를 사용하면 앱에서 발견되는 버그 비율을 최소한으로 유지할 수 있습니다.

일반적인 웹 애플리케이션에는 여러 라이브 구성 요소가 있습니다. (브라우저 또는 디바이스 앱에서) 클라이언트와 웹 서버 외에도 상당한 백 엔드가 처리될 가능성이 있습니다. 백 엔드는 구성 요소의 파이프라인 또는 공동 작업의 느슨한 컬렉션입니다. 그 대부분은 개발자가 통제하지 못하고, 종속된 외부 서비스일 것입니다.

이러한 구성에서는 라이브 시스템 자체를 제외한 모든 가능한 오류 모드를 테스트하거나 예측하기는 어렵고 비용도 많이 듭니다. 

### <a name="questions-"></a>질문...
웹 시스템을 개발할 때 묻는 질문:

* 내 앱이 충돌하는가? 
* 정확히 무슨 일이 일어났는가? 요청이 실패했다면 그 경위를 알고 싶습니다. 이벤트 추적이 필요합니다.
* 내 앱의 속도는 충분히 빠른가? 일반적 요청에 대해 응답하는 데 얼마나 걸리는가?
* 서버가 부하를 처리할 수 있는가? 요청 속도가 증가할 경우, 응답 시간이 일정하게 유지되는가?
* 종속성(앱이 호출하는 REST API, 데이터베이스, 기타 구성 요소)의 응답 속도는 얼마나 빠른가? 특히, 시스템이 느린 경우 내 구성 요소가 원인인가, 아니면 다른 사용자가 응답하는 속도가 느려서인가?
* 앱이 확장 또는 축소가 가능한가? 전 세계에서 앱을 볼 수 있는가? 앱이 중단될 경우 원인을 알아야 합니다.
* 근본적 원인은 무엇인가? 오류의 원인은 구성 요소인가, 종속성인가? 통신 문제인가?
* 얼마나 많은 사용자가 영향을 받았는가? 해결해야 할 문제가 둘 이상이라면, 그 중에서 가장 중요한 문제는 무엇인가?

## <a name="what-is-application-insights"></a>Application Insights란?
![Application Insights의 기본 워크플로](./media/devops/020.png)

1. Application Insights는 앱이 실행되는 동안 이를 계측하고 원격 분석을 전송합니다. Application Insights SDK를 앱에 빌드하거나 런타임에 계측을 적용할 수 있습니다. Application Insights SDK를 빌드하는 방법은 일반 모듈에 원격 분석을 추가할 수 있으므로 더욱 유연합니다.
2. 원격 분석은 Application Insights 포털로 전송되고, 여기에서 원격 분석이 저장 및 처리됩니다. (Application Insights는 Microsoft Azure에서 호스팅되지만 Azure 앱뿐만 아니라 모든 웹앱을 모니터링할 수 있습니다.)
3. 원격 분석은 이벤트 차트와 표 형태로 표시됩니다.

원격 분석에는 두 가지 주요 유형(집계 및 원시 인스턴스)이 있습니다. 

* 예를 들어, 인스턴스 데이터에는 웹앱이 수신한 요청 보고서가 포함됩니다. Application Insights 포털에서 검색 도구를 사용하여 상세한 요청 정보를 찾고 검사할 수 있습니다. 인스턴스에는 앱이 요청에 응답하는 데 걸린 시간, 요청된 URL, 대략적인 클라이언트 위치 및 기타 데이터가 포함됩니다.
* 집계된 데이터에는 단위 시간당 이벤트 수가 포함되어 있으므로 응답 시간과 요청 속도를 비교할 수 있습니다. 요청 응답 시간 등의 메트릭 평균도 포함됩니다.

데이터의 주요 범주는 다음과 같습니다.

* 앱에 대한 요청(일반적으로 HTTP 요청). URL, 응답 시간, 성공 여부 관련 데이터 포함.
* 종속성 - 앱이 실행한 REST 및 SQL 호출. URI, 응답 시간 및 성공 여부 포함
* 스택 추적을 포함한 예외.
* 사용자 브라우저에서 수집한 페이지 뷰 데이터.
* 성능 카운터 등의 메트릭과 직접 작성한 메트릭. 
* 비즈니스 이벤트를 추적하는 데 사용할 수 있는 사용자 지정 이벤트
* 디버깅에 사용하는 로그 추적.

## <a name="case-study-real-madrid-fc"></a>사례 연구: Real Madrid F.C.
[Real Madrid Football Club](https://www.realmadrid.com/) 의 웹 서비스는 전 세계 4억 5,000만 명의 팬에게 서비스를 제공합니다. 팬들은 웹 브라우저와 클럽 모바일 앱을 통해 액세스합니다. 팬은 입장권을 예매할 수 있을 뿐만 아니라 경기 결과, 선수, 예정 경기에 대한 정보와 비디오 클립에도 액세스할 수 있습니다. 골인 수 등의 필터로 검색도 가능합니다. 또한, 소셜 미디어에도 링크되어 있습니다. 사용자 경험은 매우 개인화되어 있으며, 팬의 참여를 유도하기 위해 양방향 통신으로 설계되었습니다.

솔루션은 [Microsoft Azure의 서비스 및 애플리케이션 시스템](https://www.microsoft.com/inculture/sports/real-madrid/)입니다. 확장성은 핵심 요구 사항입니다. 트래픽은 변동이 심하고 경기 무렵이나 도중에 매우 높아질 수 있습니다.

Real Madrid에게는 시스템 성능을 모니터링하는 것이 매우 중요합니다. Azure Application Insights는 시스템 전체에 대한 종합적인 개관을 제공하여 안정적이고 높은 수준의 서비스를 보장합니다. 

또한, 클럽에서 팬을 보다 심도 있게 파악할 수 있습니다. 팬들의 주거지(스페인 거주자는 3%에 불과), 선수, 과거 경기 실적, 예정된 경기에 대한 팬들의 관심 및 경기 결과에 대한 팬들의 반응을 알 수 있습니다.

이 원격 분석 데이터 대부분은 추가 코드 없이 자동 수집되므로 솔루션을 단순화하고 운영 복잡성을 낮출 수 있었습니다.  Application Insights는 Real Madrid 대신 매달 38억 개의 원격 분석 지점을 처리합니다.

Real Madrid는 Power BI 모듈을 사용하여 원격 분석을 확인합니다.

![Application Insights 원격 분석의 Power BI 보기](./media/devops/080.png)

## <a name="smart-detection"></a>스마트 감지
[사전 진단](../../azure-monitor/app/proactive-diagnostics.md)은 최신 기능입니다. 사용자가 특별한 구성을 하지 않아도 Application Insights가 앱에서 실패율이 비정상적으로 증가하는 것을 자동으로 감지하고 알립니다. 이 기능은 매우 지능적이어서 백그라운드에서 때때로 일어나거나 요청 증가에 비례하여 증가하는 오류에 대해서는 무시합니다. 예를 들어, 사용하는 서비스에 오류가 발생하거나 방금 배포한 새 빌드가 제대로 작동하지 않는다면 이메일을 보는 즉시 그 사실을 알 수 있습니다. (또한 webhook가 있어 다른 앱을 트리거할 수 있습니다.)

또한, 이 기능은 매일 심층적으로 원격 분석을 분석하여 발견하기 어려운 비정상적인 성능 패턴을 찾아냅니다. 예를 들어, 특정 지리적 영역 또는 특정 브라우저 버전과 관련된 성능 저하 문제를 찾을 수 있습니다.

이 두 경우 모두, 경고를 통해 발견한 증상뿐만 아니라 관련 예외 보고서와 같이 문제를 진단하는 데 유용한 데이터도 제공됩니다.

![사전 진단에서 전자 메일](./media/devops/030.png)

Samtec 고객은 다음과 같이 말했습니다. "최근 기능 컷오버를 하면서 리소스 한도까지 올라가서 시간 초과를 발생시키는 축소된 데이터베이스를 발견했습니다. 이 문제를 분류하는 즉시 사전 감지 경고가 나왔습니다. 제품 광고에 나왔던 대로 거의 실시간에 가까운 속도였습니다. 이 경고는 Azure 플랫폼 경고와 더불어 문제를 즉시 해결하도록 도와주었습니다. 총 가동 중지 시간은 10분 미만이었습니다."

## <a name="live-metrics-stream"></a>라이브 메트릭 스트림
최신 빌드를 배포하는 작업은 많은 걱정을 수반하는 경험일 수 있습니다. 어떤 문제가 있을 경우 즉시 알아내어 필요 시 취소할 수 있어야 할 것입니다. 라이브 메트릭 스트림은 약 1초의 대기 시간으로 주요 메트릭을 제공합니다.

![라이브 메트릭](./media/devops/0040.png)

즉시 모든 실패 또는 예외의 샘플을 검사할 수 있습니다.

![라이브 실패 이벤트](./media/devops/002-live-stream-failures.png)

## <a name="application-map"></a>애플리케이션 맵
애플리케이션 맵은 애플리케이션 토폴로지를 자동으로 검색하고 그 위에 성능 정보를 배치하여, 배포된 환경 전반에 걸쳐 성능 병목 현상 및 문제가 있는 흐름을 쉽게 파악할 수 있도록 합니다. Azure 서비스에서 애플리케이션 종속성을 검색할 수도 있습니다. 문제가 코드 관련인지 종속성 관련인지 분류하고, 한 곳에서부터 관련 진단 항목까지 파고들 수 있습니다. 예를 들어, SQL 계층에서 성능이 저하되어 애플리케이션에 오류가 발생할 수 있습니다. 애플리케이션 맵을 사용하면 SQL Index Advisor 또는 Query Insights 환경을 즉시 확인하고 자세히 살펴볼 수 있습니다.

![애플리케이션 맵](./media/devops/0050.png)

## <a name="application-insights-analytics"></a>Application Insights Analytics
[Analytics](../../azure-monitor/app/analytics.md)를 사용하면 SQL과 유사한 강력한 언어로 임의의 쿼리를 작성할 수 있습니다.  다양한 관점을 연결하고 서비스 성능과 비즈니스 메트릭, 고객 경험을 연관시킬 수 있는 올바른 질문을 던질 수 있으므로 전체 앱 스택 진단이 손쉬워집니다. 

포털에 저장된 모든 원격 분석 인스턴스와 메트릭 원시 데이터를 쿼리할 수 있습니다. 언어에는 필터, 조인, 집계 및 기타 연산자가 포함됩니다. 필드 계산과 통계 분석도 수행할 수 있습니다. 테이블 형식 및 그래픽 시각화가 모두 들어 있습니다.

![분석 쿼리 및 결과 차트](./media/devops/0025.png)

예를 들어, 다음과 같은 작업이 간편합니다.

* 고객 계층별로 애플리케이션의 요청 성능 데이터를 세그먼트로 나누고 고객 경험을 이해합니다.
* 라이브 사이트 검사 시 특정 오류 코드 또는 사용자 지정 이벤트 이름을 검색합니다.
* 특정 고객의 앱 사용을 자세히 살펴보고 어떤 식으로 기능을 습득하고 적용하는지 이해합니다.
* 특정 사용자의 세션 및 응답 시간을 추적하여 지원 팀과 운영 팀이 즉시 고객 지원을 제공하도록 합니다.
* 자주 사용되는 앱 기능을 파악하여 기능 우선순위 질문에 답합니다.

DNN 고객은 다음과 같이 말했습니다. "Application Insights는 필요에 따라 데이터를 결합, 분류, 쿼리, 필터링하는 데 빠져 있는 부분을 채워주었습니다. 우리 팀은 자신의 창의력과 경험을 활용하여 강력한 쿼리 언어로 데이터를 찾아낼 수 있었고, 그 덕분에 통찰력을 얻고 미처 알지 못했던 문제를 찾아서 해결하였습니다. *'만약...'* 이라는 질문에서 흥미로운 답이 수없이 나올 수 있습니다."

## <a name="development-tools-integration"></a>개발 도구 통합
### <a name="configuring-application-insights"></a>Application Insights 구성
Visual Studio와 Eclipse는 개발 중인 프로젝트에 알맞은 SDK 패키지를 구성하는 도구가 내장되어 있습니다. Application Insights를 추가하는 메뉴 명령이 있습니다.

Log4N, NLog, or System.Diagnostics.Trace와 같은 추적 로깅 프레임워크를 사용하는 경우 다른 원격 분석과 함께 로그를 Application Insights에 전송할 수 있는 옵션이 나타납니다. 이 기능을 통해 추적을 요청, 종속성 호출, 예외와 쉽게 연관시킬 수 있습니다.

### <a name="search-telemetry-in-visual-studio"></a>Visual Studio에서 원격 분석 검색
기능을 개발하고 디버그하는 동안 웹 포털과 똑같은 검색 기능을 사용하여 Visual Studio에서 직접 원격 분석을 조회하고 검색할 수 있습니다.

Application Insights가 예외를 기록하면 Visual Studio에서 데이터 포인트를 확인하고 바로 관련 코드로 넘어갈 수 있습니다.

![Visual Studio 검색](./media/devops/060.png)

디버그할 때는 원격 분석을 개발 컴퓨터에 보관하는 옵션이 있습니다. Visual Studio에서 볼 수는 있지만 포털에 전송하지는 않습니다. 이 로컬 옵션은 디버깅과 프로덕션 원격 분석이 섞이지 않도록 방지합니다.

### <a name="work-items"></a>작업 항목
경고가 발생한 경우 Application Insights는 자동으로 사용자의 작업 추적 시스템에서 작업 항목을 만듭니다.

## <a name="but-what-about"></a>기타 사항
* [개인 정보 보호 및 저장소](../../azure-monitor/app/data-retention-privacy.md) - 원격 분석은 Azure 보안 서버에 보관됩니다.
* 성능 - 성능에 미치는 영향이 매우 낮습니다. 원격 분석은 일괄 처리됩니다.
* [가격 책정](../../azure-monitor/app/pricing.md) - 무료로 시작할 수 있고 낮은 볼륨에서는 계속 무료로 이용할 수 있습니다.


## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>다음 단계
Application Insights로 시작하기가 쉽습니다. 기본 옵션:

* [IIS 서버](../../azure-monitor/app/monitor-performance-live-website-now.md), 및 [Azure App Service](../../azure-monitor/app/app-insights-overview.md)합니다.
* 개발 중에 프로젝트를 계측합니다. [ASP.NET](../../azure-monitor/app/asp-net.md) 또는 [Java](../../azure-monitor/app/java-get-started.md) 앱, [Node.js](../../azure-monitor/app/nodejs.md), 여러 가지 [기타 유형](../../azure-monitor/app/platforms.md) 호스트에 적용할 수 있습니다. 
* 짧은 코드 조각을 추가하여 [아무 웹 페이지](../../azure-monitor/app/javascript.md) 나 계측합니다.

