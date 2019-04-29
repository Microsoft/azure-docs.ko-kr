---
title: Windows 서버 및 작업자 역할용 Azure Application Insights | Microsoft Docs
description: ASP.NET 애플리케이션에 Application Insights SDK를 수동으로 추가하여 사용량, 가용성 및 성능을 분석합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 106ba99b-b57a-43b8-8866-e02f626c8190
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: mbullwin
ms.openlocfilehash: 85764c0ee5b8ed117fb191657d54abe5bd10a703
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784489"
---
# <a name="manually-configure-application-insights-for-net-applications"></a>.NET 애플리케이션에 대한 Application Insights를 수동으로 구성

[Application Insights](../../azure-monitor/app/app-insights-overview.md)를 구성하여 다양한 애플리케이션이나 애플리케이션 역할, 구성 요소 또는 마이크로 서비스를 모니터링할 수 있습니다. 웹앱 및 서비스의 경우 Visual Studio에서 [한 단계 구성](../../azure-monitor/app/asp-net.md)을 제공합니다. 백 엔드 서버 역할 또는 데스크톱 애플리케이션과 같은 다른 유형의 .NET 애플리케이션의 경우 Application Insights를 수동으로 구성할 수 있습니다.

![예제 성능 모니터링 차트](./media/windows-services/10-perf.png)

#### <a name="before-you-start"></a>시작하기 전에

다음 작업을 수행해야 합니다.

* [Microsoft Azure](https://azure.com)구독. 팀 또는 조직에 Azure 구독이 있는 경우 소유자가 [Microsoft 계정](https://live.com)을 사용하여 사용자를 추가할 수 있습니다.
* Visual Studio 2013 이상.

## <a name="add"></a>1. Application Insights 리소스 선택

'리소스'는 Azure Portal에서 데이터를 수집하여 표시하는 곳입니다. 새로운 리소스를 만들지, 아니면 기존 리소스를 공유할지를 결정해야 합니다.

### <a name="part-of-a-larger-app-use-existing-resource"></a>더 큰 앱의 일부: 기존 리소스 사용

웹 애플리케이션에 프런트 엔드 웹앱과 하나 이상의 백 엔드 서비스와 같은 여러 구성 요소가 있는 경우 모든 구성 요소의 원격 분석을 동일한 리소스로 보내야 합니다. 이렇게 하면 단일 Application Map에 표시할 수 있으며 한 구성 요소에서 다른 구성 요소로의 요청을 추적할 수 있습니다.

따라서 이 앱의 다른 구성 요소를 이미 모니터링하고 있으면 동일한 리소스를 사용합니다.

[Azure Portal](https://portal.azure.com/)에서 리소스를 엽니다. 

### <a name="self-contained-app-create-a-new-resource"></a>자체 포함 앱: 새 리소스 만들기

새 앱이 다른 애플리케이션과 관련이 없으면 자체 리소스가 있어야 합니다.

[Azure Portal](https://portal.azure.com/)에 로그인하여 새 Application Insights 리소스를 만듭니다. 애플리케이션 유형으로 ASP.NET을 선택합니다.

![새로 만들기, Application Insights 클릭](./media/windows-services/01-new-asp.png)

애플리케이션 유형을 선택하면 리소스 블레이드의 기본 항목이 설정됩니다.

## <a name="2-copy-the-instrumentation-key"></a>2. 계측 키 복사
키는 리소스를 식별합니다. 데이터를 리소스로 보내기 위해 SDK에서 바로 설치합니다.

![속성 클릭, 키 선택 및 ctrl+C 누르기](./media/windows-services/02-props-asp.png)

## <a name="sdk"></a>3. Application Insights 패키지를 애플리케이션에 설치합니다.
Application Insights 패키지의 설치 및 구성은 작업하는 플랫폼에 따라 달라 집니다. 

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 선택합니다.
   
    ![마우스 오른쪽 단추로 프로젝트 클릭 및 Nuget 패키지 관리 선택](./media/windows-services/03-nuget.png)
2. Windows 서버 앱용 Application Insights 패키지인 "Microsoft.ApplicationInsights.WindowsServer"를 설치합니다.
   
    !["Application Insights" 검색](./media/windows-services/04-ai-nuget.png)
   
    *버전은?*

    최신 기능을 사용하려면 **시험판 포함**을 선택합니다. 관련 문서 또는 블로그는 시험판 버전이 필요한지 여부를 알려줍니다.
    
    *다른 패키지를 사용할 수 있나요?*
   
    예. API를 사용하여 사용자 고유의 원격 분석을 보내려는 경우에만 "Microsoft.ApplicationInsights"를 선택합니다. Windows Server 패키지에는 API 외에도 성능 카운터 수집 및 종속성 모니터링과 같은 다양한 패키지가 포함되어 있습니다. 

### <a name="to-upgrade-to-future-package-versions"></a>패키지의 나중 버전으로 업그레이드하려면
종종 새 버전의 SDK가 릴리스됩니다.

[패키지의 새 릴리스](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases/)로 업그레이드하려면, NuGet 패키지 관리자를 다시 열고 설치된 패키지를 필터링합니다. **Microsoft.ApplicationInsights.WindowsServer**을 선택하고 **업그레이드**를 선택합니다.

ApplicationInsights.config에 대한 사용자 지정을 변경한 경우, 업그레이드 전에 복사본을 저장하고 나중에 변경 내용을 새 버전에 병합합니다.

## <a name="4-send-telemetry"></a>4. 원격 분석 전송
**API 패키지에만 설치한 경우:**

* 코드(예: `main()`)에서 계측 키를 설정합니다. 
  
    `TelemetryConfiguration.Active.InstrumentationKey = "` *키* `";` 
* [API를 사용하여 고유한 원격 분석을 작성 합니다](../../azure-monitor/app/api-custom-events-metrics.md#ikey).

**다른 Application Insights 패키지를 설치한 경우** 원하는 경우 .config 파일을 사용하여 계측 키를 설정할 수 있습니다.

* ApplicationInsights.config(NuGet 설치에 의해 추가됨)를 편집합니다. 닫는 태그 바로 전에 삽입합니다.
  
    `<InstrumentationKey>` *복사한 계측 키* `</InstrumentationKey>`
* 솔루션 탐색기에서 ApplicationInsights.config라는 속성이 **빌드 작업 = 콘텐츠, 출력 디렉터리로 복사 = 복사**로 설정되도록 합니다.

[다양한 빌드 구성을 위해 키를 전환](../../azure-monitor/app/separate-resources.md)하려면 계측 키를 코드로 설정하는 것이 유용합니다. 키를 코드로 설정하면 `.config` 파일에서 설정할 필요가 없습니다.

## <a name="run"></a> 프로젝트 실행
**F5** 키를 사용하여 애플리케이션을 실행하고 여러 페이지를 열어 원격 분석을 생성해 봅니다.

Visual Studio에 전송한 이벤트 수가 표시됩니다.

![Visual Studio에서 이벤트 수](./media/windows-services/appinsights-09eventcount.png)

## <a name="monitor"></a> 원격 분석 보기
[Azure 포털](https://portal.azure.com/)로 돌아가서 Application Insights 리소스를 찾습니다.

개요 차트에서 데이터를 찾습니다. 처음에는 요소가 1~2개만 표시됩니다. 예를 들면 다음과 같습니다.

![클릭하여 추가 데이터 확인](./media/windows-services/12-first-perf.png)

차트를 클릭하면 더 자세한 메트릭을 볼 수 있습니다. [메트릭에 대해 자세히 알아봅니다.](../../azure-monitor/app/web-monitor-performance.md)

### <a name="no-data"></a>데이터가 없나요?
* 애플리케이션을 사용하여 여러 페이지를 열어 원격 분석을 생성해 봅니다.
* [검색](../../azure-monitor/app/diagnostic-search.md) 타일을 열고 개별 이벤트를 봅니다. 경우에 따라 메트릭 파이프라인을 통해 들어오려면 이벤트가 약간 더 걸립니다.
* 몇 초 정도 기다렸다가 **새로고침**을 클릭합니다. 차트는 주기적으로 새로 고쳐지지만 일부 데이터가 표시되기를 기다리는 경우에는 수동으로 새로 고칠 수 있습니다.
* [문제 해결](../../azure-monitor/app/troubleshoot-faq.md)을 참조하세요.

## <a name="publish-your-app"></a>앱 게시
이제 애플리케이션을 서버 또는 Azure에 배포하고 누적되는 데이터를 관찰합니다.

![Visual Studio를 사용하여 앱을 게시합니다.](./media/windows-services/15-publish.png)

디버그 모드에서 실행할 때는 파이프라인을 통해 원격 분석이 신속하게 수행되므로 데이터가 몇 초 내에 표시됩니다. 릴리스 구성에서 앱을 배포할 때는 데이터가 더 천천히 누적됩니다.

### <a name="no-data-after-you-publish-to-your-server"></a>서버에 게시한 후 데이터가 없나요?
서버 방화벽에서 나가는 트래픽에 대해 포트를 엽니다. 필수 주소 목록은 [이 페이지](https://docs.microsoft.com/azure/application-insights/app-insights-ip-addresses)를 참조하세요. 

### <a name="trouble-on-your-build-server"></a>빌드 서버에 문제가 있나요?
[이 문제 해결 항목](../../azure-monitor/app/asp-net-troubleshoot-no-data.md#NuGetBuild)을 참조하세요.

> [!NOTE]
> 앱에서 다양한 원격 분석을 생성하는 경우 적응 샘플링 모듈은 이벤트의 대표적인 분수만 전송하여 포털에 전송되는 볼륨을 자동으로 줄입니다. 그러나, 동일한 요청과 관련된 이벤트가 그룹으로 선택되거나 선택 취소되므로 관련 이벤트 간을 이동할 수 있습니다. 
> [샘플링에 대해 알아봅니다](../../azure-monitor/app/sampling.md).
> 
> 

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>다음 단계
* 애플리케이션의 모든 부분을 완벽하게 살펴보려면 [원격 분석을 더 추가](../../azure-monitor/app/asp-net-more.md)합니다.

