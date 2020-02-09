---
title: Azure Functions를 사용 하 여 사용자 지정 가용성 테스트 만들기 및 실행
description: 이 문서에서는 동작 트리거 함수에 지정 된 구성에 따라 주기적으로 실행 되는 지 수 ()를 사용 하 여 Azure 함수를 만드는 방법을 설명 합니다. 이 테스트의 결과는 Application Insights 리소스로 전송 되며, 여기서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 사용자 지정 된 테스트를 통해 포털 UI를 사용 하 여 보다 복잡 한 가용성 테스트를 작성 하거나, Azure VNET 내부에서 앱을 모니터링 하거나, 엔드포인트 주소를 변경 하거나, 지역에서 사용할 수 없는 경우 가용성 테스트를 만들 수 있습니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: c7a8ffb9873fd70353f38bb2b2bbfdb584992377
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815726"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Azure Functions를 사용 하 여 사용자 지정 가용성 테스트 만들기 및 실행

이 문서에서는 사용자 고유의 비즈니스 논리를 사용 하 여 동작 트리거 함수에 지정 된 구성에 따라 주기적으로 실행 되는 지 수 ()를 사용 하 여 Azure 함수를 만드는 방법을 설명 합니다. 이 테스트의 결과는 Application Insights 리소스로 전송 되며, 여기서 가용성 결과 데이터를 쿼리하고 경고할 수 있습니다. 이를 통해 포털에서 [가용성 모니터링](../../azure-monitor/app/monitor-web-app-availability.md) 을 통해 수행할 수 있는 것과 유사한 사용자 지정 된 테스트를 만들 수 있습니다. 사용자 지정 된 테스트를 통해 포털 UI를 사용 하 여 보다 복잡 한 가용성 테스트를 작성 하거나, Azure VNET 내부에서 앱을 모니터링 하거나, 엔드포인트 주소를 변경 하거나, 지역에서이 기능을 사용할 수 없는 경우에도 가용성 테스트를 만들 수 있습니다.

> [!NOTE]
> 이 예제는 Azure Function 내에서 지 수 () API 호출이 작동 하는 방식에 대 한 메커니즘을 보여 주기 위해서만 설계 되었습니다. 이를 완전 한 기능을 갖춘 가용성 테스트로 전환 하는 데 필요한 기본 HTTP 테스트 코드/비즈니스 논리를 작성 하는 방법에 대해서는 설명 하지 않습니다. 기본적으로이 예제를 진행 하는 경우 항상 오류를 생성 하는 가용성 테스트를 만듭니다.

## <a name="create-timer-triggered-function"></a>타이머 트리거 함수 만들기

- Application Insights 리소스가 있는 경우:
    - 기본적으로 Azure Functions는 Application Insights 리소스를 만들지만 이미 생성 된 리소스 중 하나를 사용 하려는 경우 생성 중에 지정 해야 합니다.
    - 다음 선택 항목을 사용 하 여 [Azure Functions 리소스 및 타이머 트리거 함수](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) (정리 전에 중지)를 만드는 방법에 대 한 지침을 따르세요.
        -  위쪽 근처의 **모니터링** 탭을 선택 합니다.

            ![ 사용자 고유의 App Insights 리소스를 사용 하 여 Azure Functions 앱 만들기](media/availability-azure-functions/create-function-app.png)

        - Application Insights 드롭다운 상자를 선택 하 고 리소스 이름을 입력 하거나 선택 합니다.

            ![기존 Application Insights 리소스 선택](media/availability-azure-functions/app-insights-resource.png)

        - **검토 + 만들기** 선택
- 타이머 트리거 함수에 대해 아직 Application Insights 리소스가 생성 되지 않은 경우:
    - 기본적으로 Azure Functions 응용 프로그램을 만들 때 Application Insights 리소스를 만들게 됩니다.
    - [Azure Functions 리소스 및 타이머 트리거 함수를 만드는](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) 방법에 대 한 지침을 따르세요 (정리 전 중지).

## <a name="sample-code"></a>샘플 코드

아래 코드를 실행. csx 파일에 복사 합니다. 이렇게 하면 기존 코드가 바뀝니다. 이렇게 하려면 Azure Functions 응용 프로그램으로 이동 하 여 왼쪽에서 타이머 트리거 함수를 선택 합니다.

>[!div class="mx-imgBorder"]
>Azure Portal에서 Azure 함수를 실행 합니다. csx](media/availability-azure-functions/runcsx.png) ![

> [!NOTE]
> `EndpointAddress= https://dc.services.visualstudio.com/v2/track`사용 하는 엔드포인트 주소: 리소스가 Azure Government 또는 Azure 중국와 같은 지역에 있는 경우를 제외 하 고 [기본 엔드포인트 재정의](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) 에 대 한이 문서를 참조 하 고 해당 지역에 적절 한 원격 분석 채널 엔드포인트을 선택 합니다.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

파일 보기 오른쪽에서 **추가**를 선택 합니다. 다음 구성을 사용 하 여 새 파일 **함수 proj** 를 호출 합니다.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>오른쪽 select, add를 ![합니다. 파일의 이름을 함수 proj로](media/availability-azure-functions/addfile.png) 합니다.

파일 보기 오른쪽에서 **추가**를 선택 합니다. 다음 구성을 사용 하 여 새 파일 **runAvailabilityTest** 를 호출 합니다.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>중복 확인

모든 것이 작동 하는지 확인 하기 위해 Application Insights 리소스의 가용성 탭에서 그래프를 살펴볼 수 있습니다.

> [!NOTE]
> RunAvailabilityTest에 고유한 비즈니스 논리를 구현한 경우 아래 스크린샷 처럼 성공적인 결과가 표시 됩니다. 그렇지 않은 경우에는 실패 한 결과가 표시 됩니다.

>[!div class="mx-imgBorder"]
>성공적인 결과가 있는 ![가용성 탭](media/availability-azure-functions/availtab.png)

Azure Functions를 사용 하 여 테스트를 설정 하는 경우 가용성 탭에서 **테스트 추가** 를 사용 하는 것과 달리 테스트 이름이 나타나지 않으며 상호 작용할 수 없습니다. 결과는 시각화 되지만 포털을 통해 가용성 테스트를 만들 때 표시 되는 것과 동일한 자세한 보기 대신 요약 뷰를 얻을 수 있습니다.

종단 간 트랜잭션 세부 정보를 보려면 드릴스루에서 **성공** 또는 **실패** 를 선택 하 고 샘플을 선택 합니다. 그래프에서 데이터 요소를 선택 하 여 종단 간 트랜잭션 세부 정보를 가져올 수도 있습니다.

>[!div class="mx-imgBorder"]
>샘플 가용성 테스트 ![선택](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>종단 간 트랜잭션 세부 정보를 ![](media/availability-azure-functions/end-to-end.png)

비즈니스 논리를 추가 하지 않고 모든 항목을 그대로 실행 하는 경우 테스트가 실패 한 것을 볼 수 있습니다.

## <a name="query-in-logs-analytics"></a>로그에서 쿼리 (분석)

로그 (분석)를 사용 하 여 가용성 결과, 종속성 등을 볼 수 있습니다. 로그에 대 한 자세한 내용을 보려면 [로그 쿼리 개요](../../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

>[!div class="mx-imgBorder"]
>![가용성 결과](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![종속성](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>다음 단계

- [애플리케이션 맵](../../azure-monitor/app/app-map.md)
- [트랜잭션 진단](../../azure-monitor/app/transaction-diagnostics.md)
