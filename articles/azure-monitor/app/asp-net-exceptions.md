---
title: Azure Application Insights를 사용하여 웹앱의 실패 및 예외 진단 | Microsoft Docs
description: 요청 원격 분석과 함께 ASP.NET 앱에서 예외를 캡처합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb32069de295b883cdc6d3a9fa495b1bea719c39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691811"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Application Insights를 사용하여 웹앱에서 예외 진단
라이브 웹앱의 예외는 [Application Insights](../../azure-monitor/app/app-insights-overview.md)에서 보고됩니다. 클라이언트와 서버에서 실패한 요청을 예외 및 다른 이벤트와 상호 연결하여 원인을 신속하게 진단할 수 있습니다.

## <a name="set-up-exception-reporting"></a>예외 보고 설정
* 서버 앱에서 예외를 보고하도록 하려면
  * 앱 코드에서 [Application Insights SDK](../../azure-monitor/app/asp-net.md)를 설치합니다.
  * IIS 웹 서버: [Application Insights 에이전트](../../azure-monitor/app/monitor-performance-live-website-now.md) 실행 또는
  * Azure 웹앱: [Application Insights 확장](../../azure-monitor/app/azure-web-apps.md) 추가
  * Java 웹앱: [Java 에이전트](../../azure-monitor/app/java-agent.md) 설치
* 웹 페이지에 [JavaScript 조각](../../azure-monitor/app/javascript.md)을 설치하여 브라우저 예외를 catch합니다.
* 일부 애플리케이션 프레임워크 또는 일부 설정에서는 더 많은 예외를 catch하기 위해 몇 가지 추가 단계를 수행해야 합니다.
  * [웹 양식](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Visual Studio를 사용하여 예외 진단
디버깅에 도움이 되도록 Visual Studio에서 앱 솔루션을 엽니다.

F5 키를 사용하여 서버 또는 개발 컴퓨터에서 앱을 실행합니다.

Visual Studio에서 Application Insights Search 창을 열고 앱에서 이벤트를 표시하도록 설정합니다. 디버깅하는 동안 Application Insights 단추를 클릭하여 이를 수행할 수 있습니다.

![프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 및 열기를 선택합니다.](./media/asp-net-exceptions/34.png)

예외를 표시하도록 보고서를 필터링 할 수 있습니다.

*예외가 표시되지 않나요? [예외 캡처](#exceptions)를 참조하세요.*

예외 보고서를 클릭하여 해당 스택 추적을 표시합니다.
스택 추적에서 라인 참조를 클릭하여 관련 코드 파일을 엽니다.

코드를 보면 CodeLens가 예외에 대한 데이터를 표시합니다.

![CodeLens 예외 알림.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Azure 포털을 사용하여 오류 진단
Application Insights는 APM 환경과 함께 제공되어 모니터링된 애플리케이션에서 실패를 진단하는 데 도움이 됩니다. 시작하려면 조사 섹션에 있는 Application Insights 리소스 메뉴에서 오류 옵션을 클릭합니다.
사용자의 요청, 실패하는 횟수 및 사용자가 영향을 받는 횟수에 대한 오류 속도 추세를 표시하는 전체 화면 보기에 표시됩니다. 상위 3개 응답 코드, 상위 3개 예외 형식 및 상위 3개 실패 종속성 형식을 비롯한 선택한 실패 작업에 관한 몇 가지 가장 유용한 분포가 오른쪽에 표시됩니다.

![오류 심사 보기(작업 탭)](./media/asp-net-exceptions/FailuresTriageView.png)

한 번 클릭하면 작업의 이러한 각 하위 집합에 대한 대표 샘플을 검토할 수 있습니다. 특히, 예외를 진단하려면 다음과 같이 예외 세부 정보 블레이드에 표시되는 특정 예외 수를 클릭할 수 있습니다.

![예외 세부 정보 블레이드](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

**또는** 특정 실패 작업의 예외를 살펴보는 대신 예외 탭으로 전환하여 전체적인 예외 보기로 시작할 수 있습니다.

![오류 심사 보기(예외 탭)](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

여기서 모니터링한 앱에 대해 수집된 모든 예외를 확인할 수 있습니다.

*예외가 표시되지 않나요? [예외 캡처](#exceptions)를 참조하세요.*


## <a name="custom-tracing-and-log-data"></a>사용자 지정 추적 및 로그 데이터
진단 데이터 특성을 사용자 프로그램으로 불러오려면, 사용자 고유의 원격 분석 전송에 코드를 삽입합니다. 요청, 페이지 보기 및 기타 자동으로 수집된 데이터와 함께 진단 검색에 표시됩니다.

여러 옵션이 있습니다.

* [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) 는 일반적으로 사용 패턴 모니터링을 위해 사용되지만 진단 검색의 사용자 지정 이벤트에서도 전송하는 데이터를 표시합니다. 이벤트의 이름을 지정하고, [진단 검색을 필터링](../../azure-monitor/app/diagnostic-search.md)할 수 있는 문자열 속성 및 숫자 메트릭 수를 수행할 수있습니다. 
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) 를 사용하여 POST 정보와 같은 긴데이터를 보낼 수 있습니다.
* [TrackException()](#exceptions) 은 스택 추적을 보냅니다. [예외에 대해 자세히 알아보세요](#exceptions).
* 사용자가 이미 Log4Net 또는 NLog와 같은 로깅 프레임워크를 사용하는 경우, 요청과 예외 데이터와 함께 진단 검색 안에서 [이러한 로그를 캡처](asp-net-trace-logs.md)하고 볼 수 있습니다.

이러한 이벤트를 보려면, [검색](../../azure-monitor/app/diagnostic-search.md)과 필터를 차례대로 열고 사용자 지정 이벤트, 추적 또는 예외를 선택합니다.

![드릴스루](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> 앱에서 다양한 원격 분석을 생성하는 경우 적응 샘플링 모듈은 이벤트의 대표적인 분수만 전송하여 포털에 전송되는 볼륨을 자동으로 줄입니다. 동일한 작업에 속하는 이벤트를 그룹으로 선택하거나 선택 취소되므로 관련된 이벤트를 탐색할 수 있습니다. [샘플링에 대해 알아봅니다.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>요청 게시 데이터를 참조하는 방법
요청 세부 정보에는 POST 호출에서 앱으로 전송된 데이터가 포함되지 않습니다. 이 데이터에 대한 보고를 받으려면 다음을 수행합니다.

* 애플리케이션 프로젝트에 [SDK를 설치](../../azure-monitor/app/asp-net.md)합니다.
* 애플리케이션에 코드를 삽입하여 [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)를 호출합니다. 메시지 매개 변수의 POST 데이터를 보냅니다. 허용되는 크기에 제한이 있으므로 꼭 필요한 데이터만 보내야 합니다.
* 실패한 요청을 조사할 때 연결된 추적을 찾습니다.

![드릴스루](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> 예외 및 관련 진단 데이터 캡처
처음에는 앱에서 실패를 유발하는 예외가 포털에 전부 표시되지 않을 것입니다. 웹 페이지에서 [JavaScript SDK](../../azure-monitor/app/javascript.md)를 사용 중이라면 브라우저 예외가 보일 것입니다. 하지만 대부분 서버 예외는 IIS에서 catch하며 서버 예외를 보려면 약간의 코드를 작성해야 합니다.

다음을 수행할 수 있습니다.

* **예외를 명시적으로 기록** 합니다.
* **예외를 자동으로 캡처** 합니다. 프레임워크 유형에 따라 추가할 항목이 다릅니다.

## <a name="reporting-exceptions-explicitly"></a>예외를 명시적으로 보고
가장 간단한 방법은 예외 처리기에 TrackException()에 대한 호출을 삽입하는 것입니다.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

속성 및 측정 매개 변수는 선택적이지만 추가 정보를 [필터링 및 추가](../../azure-monitor/app/diagnostic-search.md)하는 데 유용합니다. 예를 들어 여러 게임을 실행할 수 있는 앱이 있는 경우 특정 게임과 관련된 모든 예외 보고서를 찾을 수 있습니다. 각 사전에 원하는 만큼 항목을 추가할 수 있습니다.

## <a name="browser-exceptions"></a>브라우저 예외
대부분의 브라우저 예외가 보고됩니다.

웹 페이지에 콘텐츠 배달 네트워크 또는 다른 도메인의 스크립트 파일이 포함되는 경우 스크립트 태그에 ```crossorigin="anonymous"``` 특성이 있고 서버에서 [CORS 헤더](https://enable-cors.org/)를 전송하는지 확인하세요. 이러한 리소스에서 처리되지 않은 JavaScript 예외에 대한 스택 추적 및 세부 정보를 가져올 수 있을 것입니다.

## <a name="web-forms"></a>웹 양식
웹 양식의 경우 CustomErrors를 사용하여 구성된 리디렉션이 없으면 HTTP 모듈에서 예외를 수집할 수 있습니다.

하지만 활성 리디렉션이 있다면 Global.asax.cs의 Application_Error 함수에 다음 줄을 추가합니다 (아직 없는 경우 Global.asax 파일 추가).

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Application Insights 웹 SDK 버전 2.6(beta3 및 이후 버전)부터 Application Insights는 MVC 5 + 컨트롤러 메서드에서 자동으로 throw된 처리되지 않은 예외를 수집합니다. (다음 예제에 설명된 대로) 이전에 사용자 지정 처리기를 추가하여 이러한 예외를 추적한 경우 예외의 이중 추적을 방지하기 위해 제거할 수 있습니다.

예외 필터에서 처리할 수 없는 다양한 경우가 있습니다. 예를 들면 다음과 같습니다.

* 컨트롤러 생성자에서 throw된 예외
* 메시지 처리기에서 throw된 예외
* 라우팅 중에 throw된 예외
* 응답 콘텐츠를 직렬화하는 동안 throw된 예외
* 애플리케이션 시작 중에 throw된 예외
* 배경 작업에서 throw된 예외

애플리케이션에 의해 *처리*된 모든 예외는 수동으로 추적되어야 합니다.
일반적으로 컨트롤러에서 발생한 처리되지 않은 예외로 인해 500 "내부 서버 오류" 응답이 발생합니다. 이러한 응답이 처리된 예외(또는 예외 없음)의 결과로 수동으로 생성된 경우 `ResultCode` 500을 사용하여 해당하는 요청 원격 분석에서 추적됩니다. 그러나 Application Insights SDK는 해당하는 예외를 추적할 수 없습니다.

### <a name="prior-versions-support"></a>이전 버전 지원
Application Insights 웹 SDK 2.5(및 이전 버전)의 MVC 4(및 이전 버전)를 사용하는 경우 예외를 추적하기 위해 다음 예제를 참조합니다.

[CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx)가 `Off`로 구성되어 있으면 [HTTP 모듈](https://msdn.microsoft.com/library/ms178468.aspx)에서 예외를 수집할 수 있습니다. 그러나 `RemoteOnly`(기본값) 또는 `On`으로 설정되어 있으면 예외가 지워지고 Application Insights에서 자동으로 수집할 수 없습니다. [System.Web.Mvc.HandleErrorAttribute 클래스](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx)를 재정의하고, 재정의된 클래스를 아래와 같이 다른 MVC 버전에 적용하여 이 문제를 해결할 수 있습니다([GitHub 자료](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)).

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
HandleError 특성을 컨트롤러의 새 특성으로 바꿉니다.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[샘플](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Global.asax.cs에서 `AiHandleErrorAttribute` 를 글로벌 필터로 등록합니다.

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[샘플](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
FilterConfig.cs에서 AiHandleErrorAttribute를 글로벌 필터로 등록합니다.

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[샘플](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web API
Application Insights 웹 SDK 버전 2.6(beta3 및 이후 버전)부터 Application Insights는 WebAPI 2+의 컨트롤러 메서드에서 자동으로 throw된 처리되지 않은 예외를 수집합니다. (다음 예제에 설명된 대로) 이전에 사용자 지정 처리기를 추가하여 이러한 예외를 추적한 경우 예외의 이중 추적을 방지하기 위해 제거할 수 있습니다.

예외 필터에서 처리할 수 없는 다양한 경우가 있습니다. 예를 들면 다음과 같습니다.

* 컨트롤러 생성자에서 throw된 예외
* 메시지 처리기에서 throw된 예외
* 라우팅 중에 throw된 예외
* 응답 콘텐츠를 직렬화하는 동안 throw된 예외
* 애플리케이션 시작 중에 throw된 예외
* 배경 작업에서 throw된 예외

애플리케이션에 의해 *처리*된 모든 예외는 수동으로 추적되어야 합니다.
일반적으로 컨트롤러에서 발생한 처리되지 않은 예외로 인해 500 "내부 서버 오류" 응답이 발생합니다. 이러한 응답이 처리된 예외(또는 예외 없음)의 결과로 수동으로 생성된 경우 `ResultCode` 500을 사용하여 해당하는 요청 원격 분석에서 추적됩니다. 그러나 Application Insights SDK는 해당하는 예외를 추적할 수 없습니다.

### <a name="prior-versions-support"></a>이전 버전 지원
Application Insights 웹 SDK 2.5(및 이전 버전)의 WebAPI 1(및 이전 버전)을 사용하는 경우 예외를 추적하기 위해 다음 예제를 참조합니다.

#### <a name="web-api-1x"></a>Web API 1.x
System.Web.Http.Filters.ExceptionFilterAttribute를 재정의합니다.

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

재정의된 특성을 특정 컨트롤러에 추가하거나 WebApiConfig 클래스에서 글로벌 필터 구성에 추가할 수 있습니다.

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[샘플](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x
IExceptionLogger를 추가로 구현합니다.

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

WebApiConfig에서 서비스에 추가합니다.

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[샘플](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

또는 다음 방법을 사용해도 됩니다.

1. 유일한 ExceptionHandler를 IExceptionHandler의 사용자 지정 구현으로 바꿉니다. 이 특성은 프레임 워크에서 보낼 응답 메시지를 선택할 수 있는 때에만 호출됩니다. 인스턴스에 대한 연결이 끊어지면 호출되지 않습니다.
2. 위의 Web API 1.x 컨트롤러 섹션에서 설명한 것처럼 예외 필터는 어떤 경우에도 호출되지 않습니다.

## <a name="wcf"></a>WCF
특성을 확장하고 IErrorHandler 및 IServiceBehavior를 구현하는 클래스를 추가합니다.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[샘플](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>예외 성능 카운터
서버에 [Application Insights 에이전트를 설치](../../azure-monitor/app/monitor-performance-live-website-now.md)한 경우 .NET에서 측정된 예외 속도 차트를 확인할 수 있습니다. 여기에는 처리된 .NET 예외와 처리되지 않은 .NET 예외가 모두 포함됩니다.

메트릭 탐색기 블레이드를 열고 새 차트를 추가한 다음 성능 카운터 아래에 나열된 **예외 속도**를 선택합니다.

.NET Framework는 간격의 예외 수를 계산하고 간격의 길이로 나누어 속도를 계산합니다.

TrackException 보고서를 계산하여 Application Insights 포털에서 계산되는 ‘예외’ 개수와는 다릅니다. 샘플링 간격이 다르며, SDK에서 처리된 예외 및 처리되지 않은 예외 둘 다에 대한 TrackException 보고서를 보내지 않습니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>다음 단계
* [REST, SQL 및 기타 종속성 호출 모니터링](../../azure-monitor/app/asp-net-dependencies.md)
* [페이지 로드 시간, 브라우저 예외 및 AJAX 호출 모니터링](../../azure-monitor/app/javascript.md)
* [성능 카운터 모니터링](../../azure-monitor/app/performance-counters.md)
