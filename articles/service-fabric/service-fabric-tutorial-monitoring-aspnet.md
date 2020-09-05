---
title: ASP.NET Core 서비스 모니터링 및 진단
description: 이 자습서에서는 Azure Service Fabric ASP.NET Core 애플리케이션에 대한 모니터링 및 진단을 설정하는 방법을 알아봅니다.
author: dkkapur
ms.topic: tutorial
ms.date: 07/10/2019
ms.author: dekapur
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 4c812ab4f6c8908e3cabcf2d3afcc63959073ed4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020112"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>자습서: Application Insights를 사용하여 Service Fabric에서 ASP.NET Core 애플리케이션 모니터링 및 진단

이 자습서는 시리즈의 5부입니다. Application Insights를 사용하여 Service Fabric 클러스터에서 실행되는 ASP.NET Core 애플리케이션에 대한 모니터링 및 진단을 설정하는 단계를 안내합니다. 자습서의 1부, [.NET Service Fabric 애플리케이션 빌드](service-fabric-tutorial-create-dotnet-app.md)에서 개발한 애플리케이션에서 원격 분석 데이터를 수집합니다.

자습서 시리즈의 4부에서는 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * 애플리케이션에 대한 Application Insights 구성
> * 응답 원격 분석 데이터를 수집하여 서비스 간 HTTP 기반 통신 추적
> * Application Insights에서 앱 지도 기능 사용
> * Application Insights API를 사용하여 사용자 지정 이벤트 추가

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * [.NET Service Fabric 애플리케이션 빌드](service-fabric-tutorial-create-dotnet-app.md)
> * [애플리케이션을 원격 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core 프런트 엔드 서비스에 HTTPS 엔드포인트 추가](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Azure Pipelines를 사용하여 CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * 애플리케이션에 대한 모니터링 및 진단 설정

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Visual Studio 2019를 설치](https://www.visualstudio.com/)하고 **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드를 설치합니다.
* [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.

## <a name="download-the-voting-sample-application"></a>투표 애플리케이션 예제 다운로드

[이 자습서 시리즈의 1부](service-fabric-tutorial-create-dotnet-app.md)에서 투표 예제 애플리케이션을 빌드하지 않은 경우 다운로드할 수 있습니다. 명령 창 또는 터미널에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Application Insights 리소스 설정

Application Insights는 Azure의 애플리케이션 성능 관리 플랫폼이자, 애플리케이션 모니터링 및 진단에 권장되는 Service Fabric 플랫폼입니다.

Application Insights 리소스를 만들려면 [Azure Portal](https://portal.azure.com)로 이동합니다. 왼쪽 탐색 메뉴에서 **리소스 만들기**를 클릭하여 Azure Marketplace를 엽니다. **모니터링 + 관리**, **Application Insights**를 차례로 클릭합니다.

![새 AI 리소스 만들기](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

이제 만들려는 리소스의 특성에 대한 필수 정보를 입력해야 합니다. 해당 *이름*, *리소스 그룹* 및 *구독*을 입력합니다. 나중에 Service Fabric 클러스터를 배포할 *위치*를 설정합니다. 이 자습서에서는 로컬 클러스터에 앱을 배포하므로 *위치* 필드는 관련이 없습니다. *애플리케이션 종류*를 “ASP.NET 웹 애플리케이션”으로 두어야 합니다.

![AI 리소스 특성](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

필수 정보를 입력한 후 **만들기**를 클릭하여 리소스를 프로비전합니다. 이 작업은 약 1분 정도 걸립니다.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>애플리케이션의 서비스에 Application Insights 추가

시작 메뉴에서 Visual Studio 아이콘을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택하여 관리자 권한으로 Visual Studio 2019를 시작합니다. **파일** > **열기** > **프로젝트/솔루션**을 클릭하고 자습서의 1부에서 만들었거나 git clone한 Voting 애플리케이션으로 이동합니다. *Voting.sln* 파일을 엽니다. 애플리케이션의 NuGet 패키지를 복원하라는 메시지가 표시되면 **예**를 클릭합니다.

다음 단계에 따라 VotingWeb 및 VotingData 서비스에 대해 Application Insights를 구성합니다.

1. 서비스 이름을 마우스 오른쪽 단추로 클릭하고, **추가 > 연결된 서비스 > Application Insights를 사용하여 모니터링**을 클릭합니다.

    ![AI 구성](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>프로젝트 형식에 따라 서비스 이름을 마우스 오른쪽 단추로 클릭할 때 추가-> Application Insights 원격 분석...을 클릭해야 합니다.

2. **시작**을 클릭합니다.
3. Azure 구독을 설정할 때 사용한 계정에 로그인한 다음, Application Insights 리소스를 만든 구독을 선택합니다. “리소스” 드롭다운의 *기존 Application Insights 리소스*에서 리소스를 찾습니다. **등록**을 클릭하여 Application Insights를 서비스에 추가합니다.

    ![AI 등록](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. 팝업되는 대화 상자가 작업을 완료하면 **마침**을 클릭합니다.

> [!NOTE]
> 애플리케이션에서 서비스 **둘 다**에 대해 위의 단계를 수행하여 애플리케이션에 대한 Application Insights 구성을 마쳐야 합니다.
> 들어오고 나가는 요청 및 서비스 간의 통신을 확인하기 위해 서비스 둘 다에 동일한 Application Insights 리소스가 사용됩니다.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>서비스에 Microsoft.ApplicationInsights.ServiceFabric.Native NuGet 추가

Application Insights에는 시나리오에 따라 사용할 수 있는 두 개의 Service Fabric 특정 NuGet이 있습니다. 하나는 Service Fabric의 네이티브 서비스에 사용되고, 다른 하나는 컨테이너 및 게스트 실행 파일에 사용됩니다. 이 경우 Microsoft.ApplicationInsights.ServiceFabric.Native NuGet을 사용하여 가져오는 서비스 컨텍스트 정보를 활용합니다. Application Insights SDK 및 Service Fabric 특정 NuGet에 대한 자세한 내용은 [Service Fabric용 Microsoft Application Insights](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md)를 참조하세요.

다음은 NuGet 패키지를 설정하는 단계입니다.

1. 솔루션 탐색기의 맨 위에서 **솔루션 ‘Voting’** 을 마우스 오른쪽 단추로 클릭하고, **솔루션에 대한 NuGet 패키지 관리...** 를 클릭합니다.
2. “NuGet - 솔루션” 창의 맨 위 탐색 메뉴에서 **찾아보기**를 클릭하고 검색 표시줄 옆에 있는 **시험판 포함** 상자를 선택합니다.
>[!NOTE]
>Application Insights 패키지를 설치하기 전에 사전 설치되지 않은 경우 유사한 방식으로 Microsoft.ServiceFabric.Diagnostics.Internal 패키지를 설치해야 할 수도 있습니다.

3. `Microsoft.ApplicationInsights.ServiceFabric.Native`를 검색하고 적절한 NuGet 패키지를 클릭합니다.
4. 오른쪽에서 애플리케이션의 두 서비스, 즉, **VotingWeb** 및 **VotingData** 옆에 있는 두 확인란을 클릭하고 **설치**를 클릭합니다.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. 나타나는 *변경 내용 미리 보기* 대화 상자에서 **확인**을 클릭하고, *라이선스 승인*에 동의합니다. 서비스에 NuGet 추가가 완료됩니다.
6. 이제 두 서비스에서 원격 분석 이니셜라이저를 설정해야 합니다. 이렇게 하려면 *VotingWeb.cs* 및 *VotingData.cs*를 엽니다. 둘 다에 대해 다음 두 단계를 수행합니다.
    1. 다음과 같이 각 *\<ServiceName>.cs*의 맨 위에서 기존 *using* 문 뒤에 *using* 문 두 개를 추가합니다.

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. 두 파일의 *CreateServiceInstanceListeners()* 또는 *CreateServiceReplicaListeners()* 의 중첩된 *return* 문에서, 다른 singleton 서비스가 선언된 *ConfigureServices* > *services* 아래에 다음을 추가합니다.
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    *서비스 컨텍스트*가 원격 분석에 추가되어 Application Insights에서 원격 분석의 원본을 더 자세히 이해할 수 있습니다. *VotingWeb.cs*의 중첩된 *return* 문은 다음과 같아야 합니다.

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    마찬가지로, *VotingData.cs*에는 다음 내용이 있어야 합니다.

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

위와 같이 `UseApplicationInsights()` 메서드가 *VotingWeb.cs* 및 *VotingData.cs* 파일 둘 다에서 호출되는지 다시 확인합니다.

>[!NOTE]
>이 샘플 앱은 서비스 통신을 위해 http를 사용합니다. Service Remoting V2로 앱을 개발하는 경우 위에 나온 것과 같은 위치에 다음 코드 줄을 추가해야 합니다.

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

이제 애플리케이션을 배포할 준비가 되었습니다. 맨 위에서 **시작**(또는 **F5**)을 클릭하면 Visual Studio가 애플리케이션을 빌드 및 패키지하고 로컬 클러스터를 설정한 다음, 애플리케이션을 배포합니다.

>[!NOTE]
>최신 버전의 .NET Core SDK가 설치되지 않은 경우 빌드 오류가 발생할 수 있습니다.

애플리케이션 배포가 완료되면 Voting Sample 단일 페이지 애플리케이션을 볼 수 있는 `localhost:8080`으로 이동합니다. 다른 몇 가지 선택 항목에 응답하여 일부 샘플 데이터와 원격 분석을 만듭니다.

![AI 샘플 응답](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

몇 가지 응답 추가가 완료되면 일부 응답 옵션을 자유롭게 *제거*합니다.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Application Insights에서 원격 분석 및 앱 지도 보기

Azure Portal에서 Application Insights 리소스로 이동합니다.

**개요**를 클릭하여 리소스의 방문 페이지로 돌아갑니다. 그런 다음 맨 위의 **검색**을 클릭하여 추적이 들어오는 것을 확인합니다. 추적이 Application Insights에 나타나는 데 몇 분 정도 걸립니다. 아무것도 표시되지 않는 경우 잠시 기다렸다가 맨 위의 **새로 고침** 단추를 누릅니다.
![AI see traces](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

*검색* 창에서 아래로 스크롤하면 Application Insights에서 기본적으로 제공되는 들어오는 모든 원격 분석이 표시됩니다. Voting 애플리케이션에서 수행한 각 작업에 대해 *VotingWeb*에서 나가는 PUT 요청(PUT Votes/Put [name]), *VotingData*에서 들어오는 PUT 요청(PUT VoteData/Put [name]), 표시되는 데이터 새로 고침에 대한 GET 요청 쌍이 있어야 합니다. HTTP 요청이므로 localhost에 대한 HTTP 종속성 추적도 있습니다. 응답이 어떻게 추가되었는지에 대해 표시되는 내용의 예는 다음과 같습니다.

![AI 샘플 요청 추적](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

추적 중 하나를 클릭하여 추적에 대한 자세한 정보를 볼 수 있습니다. *응답 시간* 및 *요청 URL*을 포함하여 Application Insights에서 제공하는 요청에 대한 유용한 정보가 있습니다. 또한 Service Fabric 특정 NuGet를 추가했으므로 아래 *사용자 지정 데이터* 섹션의 Service Fabric 클러스터 컨텍스트에서 애플리케이션에 대한 데이터가 제공됩니다. 여기에는 서비스 컨텍스트가 포함되므로 요청 원본의 *PartitionID* 및 *ReplicaId*를 확인하고, 애플리케이션의 오류를 진단할 때 문제를 더 효율적으로 지역화할 수 있습니다.

![AI 추적 세부 정보](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

또한 개요 페이지의 왼쪽 메뉴에서 *애플리케이션 맵*을 클릭하거나 **앱 맵** 아이콘을 클릭하여 연결된 두 서비스가 보이는 앱 맵으로 이동할 수 있습니다.

![AI 추적 세부 정보](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

앱 지도는 특히 함께 작동하는 여러 서비스를 추가할 때 애플리케이션 토폴로지를 더 잘 이해하는 데 유용할 수 있습니다. 또한 요청 성공률에 대한 기본 데이터를 제공하며, 오류가 발생할 수 있는 위치를 파악하기 위해 실패한 요청을 진단하는 데 유용할 수 있습니다. 앱 지도를 사용하는 방법에 대한 자세한 내용은 [Application Insights의 애플리케이션 맵](../azure-monitor/app/app-map.md)을 참조하세요.

## <a name="add-custom-instrumentation-to-your-application"></a>애플리케이션에 사용자 지정 계측 추가

Application Insights는 기본적으로 많은 원격 분석 데이터를 제공하지만 사용자 지정 계측을 추가하는 것이 좋습니다. 비즈니스 요구를 기반으로 하거나, 애플리케이션에서 오류가 발생할 때 진단을 개선하기 위한 것일 수 있습니다. Application Insights에는 사용자 지정 이벤트 및 메트릭을 수집하는 API가 있으며, [여기](../azure-monitor/app/api-custom-events-metrics.md)서 자세한 내용을 확인할 수 있습니다.

*VoteDataController.cs*(*VotingData* > *Controllers* 아래)에 몇 가지 사용자 지정 이벤트를 추가하여 기본 *votesDictionary*에서 응답이 추가 및 삭제되는 시기를 추적해 보겠습니다.

1. 다른 using 문의 끝에 `using Microsoft.ApplicationInsights;`를 추가합니다.
2. 클래스 시작 부분의 *IReliableStateManager* 만들기 아래에서 새 *TelemetryClient*를 선언합니다. `private TelemetryClient telemetry = new TelemetryClient();`.
3. *Put()* 함수에 응답이 추가되었음을 확인하는 이벤트를 추가합니다. 트랜잭션이 완료된 후 return *OkResult* 문 바로 앞에 `telemetry.TrackEvent($"Added a vote for {name}");`를 추가합니다.
4. *Delete()* 에는 *votesDictionary*에 지정된 응답 옵션에 대한 응답이 포함되는 조건에 따라 “if/else”가 있습니다.
    1. *if* 문의 응답 삭제를 확인하는 이벤트를 *await tx.CommitAsync()* 뒤에 추가합니다. `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. *else* 문에서 삭제가 수행되지 않았음을 보여 주는 이벤트를 return 문 앞에 추가합니다. `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

이벤트를 추가한 후 *Put()* 및 *Delete()* 함수가 표시되는 모양의 예는 다음과 같습니다.

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

이러한 변경 수행을 완료한 후 애플리케이션을 **시작**하여 최신 버전이 빌드 및 배포되도록 합니다. 애플리케이션 배포가 완료되면 `localhost:8080`으로 이동하여 몇 가지 투표 옵션을 추가 및 삭제합니다. 그런 다음, Application Insights 리소스로 돌아가서 최신 실행에 대한 추적을 확인합니다(이전처럼 추적이 Application Insights에 표시되는 데 1~2분 정도 걸릴 수 있음). 이제 추가 및 삭제한 모든 응답에 대해 “사용자 지정 이벤트”\* 와 모든 응답 원격 분석 데이터가 표시되어야 합니다.

![사용자 지정 이벤트](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.
> [!div class="checklist"]
> * 애플리케이션에 대한 Application Insights 구성
> * 응답 원격 분석 데이터를 수집하여 서비스 간 HTTP 기반 통신 추적
> * Application Insights에서 앱 지도 기능 사용
> * Application Insights API를 사용하여 사용자 지정 이벤트 추가

이제 ASP.NET 애플리케이션에 대한 모니터링 및 진단 설정을 완료했으므로 다음을 시도합니다.

* [Service Fabric에서 모니터링 및 진단 추가 탐색](service-fabric-diagnostics-overview.md)
* [Application Insights를 사용한 Service Fabric 이벤트 분석](service-fabric-diagnostics-event-analysis-appinsights.md)
* Application Insights에 대한 자세한 내용은 [Application Insights 설명서](/azure/application-insights/)를 참조하세요.
