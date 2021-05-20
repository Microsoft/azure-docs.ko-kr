---
title: Windows 데스크톱 앱의 사용량 및 성능 모니터링
description: Application Insights를 사용하여 Windows 데스크톱 앱의 사용량 및 성능을 분석합니다.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 15331494b40021f10c162fba75abf6fe88d2d419
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583317"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>클래식 Windows 데스크톱 앱에서 사용량 및 성능 모니터링

온-프레미스 호스팅된 애플리케이션은 Azure 및 다른 클라우드에서 Application Insights를 충분히 활용할 수 있습니다. 유일한 제한은 Application Insights 서비스와 [통신을 허용](./ip-addresses.md)해야 한다는 것입니다. UWP(유니버설 Windows 플랫폼) 애플리케이션 모니터링에는 [Visual Studio App Center](../app/mobile-center-quickstart.md)가 좋습니다.

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>클래식 Windows 애플리케이션에서 Application Insights에 원격 분석을 전송하려면
1. [Azure Portal](https://portal.azure.com)에서 [Application Insights 리소스를 만듭니다](./create-new-resource.md). 
2. 계측 키를 복사합니다.
3. Visual Studio에서 앱 프로젝트의 NuGet 패키지를 편집하고 Microsoft.ApplicationInsights.WindowsServer를 추가합니다. (또는 표준 원격 분석 수집 모듈 없이 기본 API만 사용하려는 경우 Microsoft.ApplicationInsights를 선택합니다.)
4. 코드에서 계측 키를 설정합니다.
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*키*`";`
   
    또는 ApplicationInsights.config에서(표준 원격 분석 패키지 중 하나를 설치한 경우).
   
    `<InstrumentationKey>`*키*`</InstrumentationKey>` 
   
    ApplicationInsights.config를 사용하는 경우 솔루션 탐색기에서 해당 속성이 **빌드 작업 = 콘텐츠, 출력 디렉터리로 복사 = 복사** 로 설정되도록 합니다.
5. [API를 사용](./api-custom-events-metrics.md) 하여 원격 분석을 전송합니다.
6. 앱을 실행하고 Azure Portal에서 만든 리소스의 원격 분석을 참조하세요.

## <a name="example-code"></a><a name="telemetry"></a>예제 코드

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>컴퓨터 이름의 스토리지 재정의

기본적으로 이 SDK는 원격 분석을 내보내는 시스템의 컴퓨터 이름을 수집하고 저장합니다.

컴퓨터 이름은 내부 요금 청구를 위해 Application Insights [레거시 엔터프라이즈(노드당) 가격 책정 계층](./pricing.md#legacy-enterprise-per-node-pricing-tier)에서 사용됩니다. 기본적으로 원격 분석 이니셜라이저를 사용하여 `telemetry.Context.Cloud.RoleInstance`를 재정의하면 컴퓨터 이름 값을 포함하는 별도의 속성 `ai.internal.nodeName`이 전송됩니다. 이 값은 Application Insights 원격 분석과 함께 저장되지 않지만 레거시 노드 기반 청구 모델과 이전 버전과의 호환성을 위해 수집 시 내부적으로 사용됩니다.

[레거시 엔터프라이즈(노드당) 가격 책정 계층](./pricing.md#legacy-enterprise-per-node-pricing-tier)에 있고 컴퓨터 이름의 스토리지를 재정의해야 하는 경우 원격 분석 이니셜라이저를 사용합니다.

**아래와 같이 사용자 지정 TelemetryInitializer를 작성합니다.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

계측 키를 설정하는 아래의 `Program.cs` `Main()` 메서드에서 이니셜라이저를 인스턴스화합니다.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>컴퓨터 이름 전송 재정의

[레거시 엔터프라이즈(노드당) 가격 책정 계층](./pricing.md#legacy-enterprise-per-node-pricing-tier)에 속하지 않고 컴퓨터 이름이 포함된 원격 분석이 전송되지 않도록 하려면 원격 분석 프로세서를 사용해야 합니다.

### <a name="telemetry-processor"></a>원격 분석 프로세서

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

계측 키를 설정하는 아래의 `Program.cs` `Main()` 메서드에서 원격 분석 프로세서를 인스턴스화합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> [레거시 엔터프라이즈(노드당) 가격 책정 계층](./pricing.md#legacy-enterprise-per-node-pricing-tier)을 사용하는 경우에도 위에서 설명한 대로 원격 분석 프로세서를 기술적으로 사용할 수 있지만 노드 가격 책정에 맞게 노드를 구분할 수 없기 때문에 과도한 요금이 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [대시보드 만들기](./overview-dashboard.md)
* [진단 검색](./diagnostic-search.md)
* [메트릭 탐색](../essentials/metrics-charts.md)
* [분석 쿼리 작성](../logs/log-query-overview.md)

