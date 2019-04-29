---
title: Azure Application Insights - 종속성 자동 수집 | Microsoft Docs
description: Application Insights에서 자동으로 종속성 수집 및 시각화
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/16/2018
ms.author: mbullwin
ms.openlocfilehash: 3ad2f4788a765366066023724772f5432d0d56eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699208"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet 패키지

다음은 Application Insights의 안정적 릴리스 NuGet 패키지의 현재 목록입니다.

## <a name="common-packages-for-aspnet"></a>ASP.NET용 일반 패키지

| 패키지 이름 | 안정적 버전 | 설명 | 다운로드 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.8.0 | 모든 Application Insights 원격 분석 유형에 대한 핵심 전송 기능을 제공하며, 다른 모든 Application Insights 패키지의 종속 패키지입니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | 메서드 호출 가로채기를 사용하도록 설정합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | .NET 애플리케이션의 Application Insights 종속성 수집기입니다. 이 수집기는 Application Insights 플랫폼별 패키지의 종속 패키지로, 종속성 원격 분석을 자동으로 수집합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights 성능 카운터 수집기를 사용하면 성능 카운터에서 수집한 데이터를 Application Insights로 보낼 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.8.0 | .NET 웹 애플리케이션용 Application Insights | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet 패키지는 .NET 애플리케이션의 Application Insights 원격 분석을 자동으로 수집합니다. 이 패키지는 Application Insights 플랫폼별 패키지의 종속 패키지 또는 플랫폼별 패키지(예: .NET 작업자 역할 관련)로 수집되지 않는 .NET 애플리케이션용 독립 실행형 패키지로 사용할 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | 오프라인 시나리오에서 원격 분석을 유지하는 Application Insights Windows Server SDK에 대한 원격 분석 채널을 제공합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>ASP.NET Core용 일반 패키지

| 패키지 이름 | 안정적 버전 | 설명 | 다운로드 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights for ASP.NET Core 웹 애플리케이션입니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.8.0 | 이 패키지는 모든 Application Insights 원격 분석 유형에 대한 핵심 전송 기능을 제공하며, 다른 모든 Application Insights 패키지의 종속 패키지입니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.8.0 | .NET 애플리케이션의 Application Insights 종속성 수집기입니다. 이 수집기는 Application Insights 플랫폼별 패키지의 종속 패키지로, 종속성 원격 분석을 자동으로 수집합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.8.0 | Application Insights 성능 카운터 수집기를 사용하면 성능 카운터에서 수집한 데이터를 Application Insights로 보낼 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.8.0 | Application Insights Windows Server NuGet 패키지는 .NET 애플리케이션의 Application Insights 원격 분석을 자동으로 수집합니다. 이 패키지는 Application Insights 플랫폼별 패키지의 종속 패키지 또는 플랫폼별 패키지(예: .NET 작업자 역할 관련)로 수집되지 않는 .NET 애플리케이션용 독립 실행형 패키지로 사용할 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.8.0 | 오프라인 시나리오에서 원격 분석을 유지하는 Application Insights Windows Server SDK에 대한 원격 분석 채널을 제공합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="listenerscollectorsappenders"></a>수신기/수집기/어펜더

| 패키지 이름 | 안정적 버전 | 설명 | 다운로드 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  DiagnosticSource에서 Application Insights로 이벤트를 전달할 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener를 사용하여 EventSource 이벤트에서 Application Insights로 데이터를 보낼 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector를 사용하여 ETW(Windows용 이벤트 추적)에서 Application Insights로 데이터를 보낼 수 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Application Insights로 추적 로그 메시지를 보낼 수 있도록 하는 사용자 지정 TraceListener입니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Application Insights로 Log4Net 로그 메시지를 보낼 수 있도록 하는 사용자 지정 어펜더입니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Application Insights로 NLog 로그 메시지를 보낼 수 있도록 하는 사용자 지정 대상입니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | 애플리케이션에서 예외를 모니터링하고 오프라인 분석을 위해 스냅숏을 자동으로 수집합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| 패키지 이름 | 안정적 버전 | 설명 | 다운로드 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | 이 패키지를 사용하여 애플리케이션이 실행되는 Service Fabric 컨텍스트를 통해 원격 분석을 자동으로 장식할 수 있습니다. 네이티브 Service Fabric 애플리케이션에는 이 NuGet을 사용하지 마세요. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Service fabric 애플리케이션에 대한 Application Insights 모듈입니다. 네이티브 Service Fabric 애플리케이션에만 이 NuGet을 사용합니다. 컨테이너에서 실행되는 애플리케이션의 경우 Microsoft.ApplicationInsights.ServiceFabric 패키지를 사용합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>상태 모니터

| 패키지 이름 | 안정적 버전 | 설명 | 다운로드 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  x64 애플리케이션의 런타임 데이터 수집을 사용하도록 설정합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  x86 애플리케이션의 런타임 데이터 수집을 사용하도록 설정합니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

이러한 패키지는 [상태 모니터](../../azure-monitor/app/monitor-performance-live-website-now.md)의 핵심 런타임 모니터링 기능 중 일부를 구성합니다. 이러한 패키지를 직접 다운로드할 필요는 없으며 상태 모니터 설치 관리자를 사용하기만 하면 됩니다. 이러한 패키지가 내부적으로 작동하는 방식을 알라보려면 먼저 개발자가 만든 [블로그 게시물](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/)을 참조하면 좋습니다.

## <a name="additional-packages"></a>추가 패키지

| 패키지 이름 | 안정적 버전 | 설명 | 다운로드 |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | 이 확장을 사용하여 Azure App Service에서 Application Insights 모니터링을 수행할 수 있습니다. SDK 버전 2.6.1. 지침: ikey를 사용하여 'APPINSIGHTS_INSTRUMENTATIONKEY' 애플리케이션 설정을 추가하고 웹앱을 다시 시작하여 적용합니다.| [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | 이 패키지에는 코드 없는 Application Insights 주입에 필요한 파일이 포함되어 있습니다. | [패키지 다운로드](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>다음 단계

- [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)를 모니터링합니다.
- ASP.NET Core [Azure Linux 웹앱](../../azure-monitor/app/profiler-aspnetcore-linux.md)을 프로파일링합니다.
- ASP.NET [스냅숏](../../azure-monitor/app/snapshot-debugger.md)을 디버그합니다.