---
title: Azure Application Insights - Azure Functions 지원 기능 | Microsoft Docs
description: Azure Functions에 대한 Application Insights 지원 기능
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902250"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions에 대한 Application Insights 지원 기능

Azure Functions는 ILogger 인터페이스를 통해 사용할 수 있는 Application Insights와의 [기본 제공 통합](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)을 제공합니다. 다음은 현재 지원되는 기능 목록입니다. Azure Functions 가이드에서 [시작](https://github.com/Azure/Azure-Functions/wiki/App-Insights) 방법을 검토하세요.

## <a name="supported-features"></a>지원되는 기능

| Azure 기능                       | V1                | V2(Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **자동 컬렉션**        |                 |                   |               
| &bull; 요청                     | 예             | 예               | 
| &bull; 예외                   | 예             | 예               | 
| &bull; 성능 카운터         | 예             | 예               |
| &bull; 종속성                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | 예               | 
| | | | 
| **지원되는 기능**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | 예             | 예               | 
| &nbsp;&nbsp;&nbsp;&mdash; 컨트롤 채널 보호|                 | 예               | 
| &bull; 샘플링                     | 예             | 예               | 
| &bull; 하트비트                   |                 | 예               | 
| | | | 
| **상관 관계**                       |                   |                   |               
| &bull; ServiceBus                     |                   | 예               | 
| &bull; EventHub                       |                   | 예               | 
| | | | 
| **구성 가능 여부**                      |                   |                   |           
| &bull;완전히 구성 가능합니다.<br/>지침을 보려면 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)를 참조하세요.<br/>모든 옵션에 대해서는 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)를 참조하세요.               |                   | 예                   | 


## <a name="performance-counters"></a>성능 카운터

성능 카운터의 자동 수집은 Windows 머신에서만 작동합니다.


## <a name="live-metrics--secure-control-channel"></a>라이브 메트릭 및 컨트롤 채널 보호

지정한 사용자 지정 필터 조건이 Application Insights SDK의 라이브 메트릭 구성 요소에 다시 전송됩니다. 필터는 customerid와 같은 잠재적으로 중요한 정보를 포함할 수 있습니다. 비밀 API 키를 사용해 채널 보안을 유지할 수 있습니다. 해당 지침은 [컨트롤 채널 보호](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)를 참조하세요.

## <a name="sampling"></a>샘플링

Azure Functions를 사용하면 기본적으로 구성에서 샘플링을 사용하도록 설정할 수 있습니다. 자세한 내용은 [샘플링 구성](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)을 참조하세요.

프로젝트 추적 수동 원격 분석을 위해 Application Insights SDK에 종속, 하는 경우 샘플링 구성 함수를 샘플링 구성과 다른 경우 비정상적인 동작이 발생할 수 있습니다. 

함수와 동일한 구성을 사용 하는 것이 좋습니다. 사용 하 여 **Functions v2**, 생성자에서 종속성 주입을 사용 하 여 동일한 구성을 가져올 수 있습니다.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
