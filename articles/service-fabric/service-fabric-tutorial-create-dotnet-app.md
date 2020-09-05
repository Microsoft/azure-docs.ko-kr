---
title: Azure의 Service Fabric에서 .NET 앱 만들기
description: 이 자습서에서는 ASP.NET Core 프런트 엔드 및 신뢰할 수 있는 서비스 상태 저장 백 엔드를 사용하여 애플리케이션을 만들고 클러스터에 배포하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 847bd2e30977c5a14f8294f6709cbd6d376a8211
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016277"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>자습서: ASP.NET Core Web API 프런트 엔드 서비스 및 상태 저장 백 엔드 서비스로 애플리케이션 만들기 및 배포

이 자습서는 시리즈의 1부입니다.  ASP.NET Core Web API 프런트 엔드 및 상태 저장 백 엔드 서비스에서 Azure Service Fabric 애플리케이션을 만들어 데이터를 저장하는 방법을 알아봅니다. 완료하면 투표 결과를 클러스터의 상태 저장 백 엔드 서비스에 저장하는 ASP.NET Core 웹 프런트 엔드가 있는 투표 애플리케이션이 생깁니다. 이 자습서 시리즈에는 Windows 개발자 머신이 필요합니다. 수동으로 투표 애플리케이션을 만들지 않으려면 완성된 애플리케이션에서 [소스 코드를 다운로드](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)하고 [투표 샘플 애플리케이션을 설명](#walkthrough_anchor)하기 위해 바로 건너뛸 수 있습니다.  원하는 경우 이 자습서의 [비디오 연습](https://channel9.msdn.com/Events/Connect/2017/E100)도 시청할 수 있습니다.

![AngularJS+ASP.NET API 프런트 엔드, Service Fabric에서 상태 저장 백 엔드 서비스에 연결](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET Core Web API 서비스를 상태 저장 Reliable Service로 만들기
> * ASP.NET Core Web 애플리케이션 서비스를 상태 비저장 웹 서비스로 만들기
> * 역방향 프록시를 사용하여 상태 저장 서비스와 통신

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * .NET Service Fabric 애플리케이션 빌드
> * [애플리케이션을 원격 클러스터에 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [ASP.NET Core 프런트 엔드 서비스에 HTTPS 엔드포인트 추가](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Azure Pipelines를 사용하여 CI/CD 구성](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [애플리케이션에 대한 모니터링 및 진단 설정](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에:
* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* **Azure 개발**과 **ASP.NET 및 웹 개발** 워크로드가 포함된 [Visual Studio 2019 버전 15.5 이상을 설치](https://www.visualstudio.com/)합니다.
* [Service Fabric SDK를 설치](service-fabric-get-started.md)합니다.

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>ASP.NET Web API 서비스를 신뢰할 수 있는 서비스로 만들기

먼저 ASP.NET Core를 사용하여 투표 애플리케이션의 웹 프런트 엔드를 만듭니다. ASP.NET Core는 최신 웹 UI 및 Web API를 만드는 데 사용할 수 있는 가벼운 크로스 플랫폼 웹 개발 프레임워크입니다. ASP.NET Core가 Service Fabric과 통합되는 방식을 완전히 이해하려면 [Service Fabric Reliable Services의 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 문서를 읽어보는 것이 좋습니다. 지금은 이 가이드를 따라 작업하여 빠르게 시작할 수 있습니다. ASP.NET Core에 대한 자세한 내용은 [ASP.NET Core 설명서](/aspnet/core/)를 참조하세요.

1. **관리자** 권한으로 Visual Studio를 시작합니다.

2. **파일**->**새로 만들기**->**프로젝트**로 프로젝트를 만듭니다.

3. **새 프로젝트** 대화 상자에서 **클라우드 &gt; Service Fabric 애플리케이션**을 선택합니다.

4. 애플리케이션의 이름을 **Voting**으로 지정하고 **확인**을 클릭합니다.

   ![Visual Studio의 새 프로젝트 대화 상자](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. **새 Service Fabric 서비스** 페이지에서 **상태 비저장 ASP.NET Core**를 선택하고, 서비스 이름을 **VotingWeb**으로 지정한 다음, **확인**을 클릭합니다.
   
   ![새 서비스 대화 상자에서 ASP.NET 웹 서비스 선택](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 다음 페이지에서는 ASP.NET Core 프로젝트 템플릿 집합을 제공합니다. 이 자습서에서는 **웹 애플리케이션(모델-뷰-컨트롤러)** 을 선택한 다음, **확인**을 클릭합니다.
   
   ![ASP.NET 프로젝트 형식 선택](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio는 애플리케이션 및 서비스 프로젝트를 만들고 솔루션 탐색기에 표시합니다.

   ![ASP.NET core Web API 서비스를 사용하여 애플리케이션 만들기를 수행하는 솔루션 탐색기]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>site.js 파일 업데이트
**wwwroot/js/site.js**를 엽니다.  해당 콘텐츠를 홈 보기에서 사용한 다음 JavaScript로 바꾼 다음, 변경 내용을 저장합니다.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Index.cshtml 파일 업데이트

홈 컨트롤러와 관련된 보기인 **Views/Home/Index.cshtml**을 엽니다.  해당 내용을 다음으로 바꾸고 변경 내용을 저장합니다.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>_Layout.cshtml 파일 업데이트

ASP.NET 앱의 기본 레이아웃인 **Views/Shared/_Layout.cshtml**을 엽니다.  해당 내용을 다음으로 바꾸고 변경 내용을 저장합니다.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>VotingWeb.cs 파일 업데이트

WebListener 웹 서버를 사용하여 상태 비저장 서비스 내에 ASP.NET Core 웹 호스트를 만드는 *VotingWeb.cs* 파일을 엽니다.

파일 맨 위에 `using System.Net.Http;` 지시문을 추가합니다.

`CreateServiceInstanceListeners()` 함수를 다음 코드로 바꾸고 변경 내용을 저장합니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

또한 `GetVotingDataServiceName` 메서드를 `CreateServiceInstanceListeners()` 아래에 추가하고 변경 내용을 저장합니다. `GetVotingDataServiceName`은 폴링될 때 서비스 이름을 반환합니다.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>VotesController.cs 파일 추가

투표 작업을 정의하는 컨트롤러를 추가합니다. **Controllers** 폴더를 마우스 오른쪽 단추로 클릭하고, **추가->새 항목->Visual C#->ASP.NET Core->클래스**를 선택합니다. 파일 이름을 **VotesController.cs**로 지정한 다음, **추가**를 클릭합니다.  

*VotesController.cs* 파일 콘텐츠를 다음으로 바꾸고 변경 내용을 저장합니다.  이 파일은 뒷부분의 [VotesController.cs 파일 업데이트](#updatevotecontroller_anchor)에서 백 엔드 서비스의 투표 데이터를 읽고 쓰도록 수정됩니다.  지금은 컨트롤러가 보기에 고정 문자열 데이터를 반환합니다.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>수신 대기 포트 구성

VotingWeb 프런트 엔드 서비스를 만들면 Visual Studio에서는 수신할 서비스에 대한 포트를 임의로 선택합니다.  VotingWeb 서비스는 이 애플리케이션에 대한 프런트 엔드로 작동하고 외부 트래픽을 허용하므로 이 서비스를 고정된 잘 알려진 포트에 바인딩하겠습니다.  [서비스 매니페스트](service-fabric-application-and-service-manifests.md)가 서비스 엔드포인트를 선언합니다.

솔루션 탐색기에서 *VotingWeb/PackageRoot/ServiceManifest.xml*을 엽니다.  **리소스** 섹션에서 **Endpoint** 요소를 찾아서 **포트** 값을 **8080**으로 변경합니다. 애플리케이션을 로컬로 배포하고 실행하려면 애플리케이션 수신 대기 포트가 열려 있고 컴퓨터에서 사용할 수 있어야 합니다.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

애플리케이션을 디버그할 때 올바른 포트에 웹 브라우저가 열리도록 투표 프로젝트에서 애플리케이션 URL 속성 값도 업데이트합니다.  솔루션 탐색기에서 **투표** 프로젝트를 선택하고 **애플리케이션 URL** 속성을 **8080**으로 업데이트합니다.

### <a name="deploy-and-run-the-voting-application-locally"></a>Voting 애플리케이션을 로컬로 배포하고 실행
이제 Voting 애플리케이션을 실행하고 디버깅할 수 있습니다. Visual Studio에서 **F5** 키를 눌러 디버그 모드에서 애플리케이션을 로컬 Service Fabric 클러스터에 배포합니다. 이전에 **관리자** 권한으로 Visual Studio를 열지 않은 경우 애플리케이션이 실패합니다.

> [!NOTE]
> 로컬에서 처음으로 애플리케이션을 배포하고 실행할 때 Visual Studio는 디버깅을 위한 로컬 Service Fabric 클러스터를 만듭니다.  클러스터 생성에는 다소 시간이 걸릴 수 있습니다. Visual Studio 출력 창에 클러스터 생성 상태가 표시됩니다.

로컬 Service Fabric 클러스터에 Voting 애플리케이션을 배포한 후에는 웹앱이 자동으로 브라우저 탭에서 열리고 다음과 비슷하게 보입니다.

![ASP.NET Core 프런트 엔드](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

애플리케이션 디버깅을 중지하려면 Visual Studio로 돌아가 **Shift+F5** 키를 누릅니다.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>상태 저장 백 엔드 서비스를 애플리케이션에 추가

이제 애플리케이션에서 ASP.NET Web API 서비스가 실행되므로, 계속해서 애플리케이션에 일부 데이터를 저장하는 상태 저장 신뢰할 수 있는 서비스를 추가하겠습니다.

Service Fabric을 통해 신뢰할 수 있는 컬렉션을 사용하여 일관되고 안정적으로 서비스 내에 데이터를 바로 저장할 수 있습니다. 신뢰할 수 있는 컬렉션은 C# 컬렉션을 사용해본 적이 있는 사람에게 친숙한 신뢰할 수 있는 고가용성 컬렉션 클래스의 집합입니다.

이 자습서에서는 신뢰할 수 있는 컬렉션에 카운터 값을 저장하는 서비스를 만듭니다.

1. 솔루션 탐색기에서 Voting 애플리케이션 프로젝트 내부의 **서비스**를 마우스 오른쪽 단추로 클릭하고 **추가 -&gt; 새 Service Fabric 서비스...** 를 선택합니다.
    
2. **새 Service Fabric 서비스** 대화 상자에서 **상태 저장 ASP.NET Core**를 선택하고, 서비스 이름을 **VotingData**로 지정한 다음, **확인**을 누릅니다.

    서비스 프로젝트를 만들었으면 애플리케이션에 두 개의 서비스가 있을 것입니다. 계속해서 애플리케이션을 빌드하는 동안 같은 방법으로 더 많은 서비스를 추가할 수 있습니다. 각 서비스를 독립적으로 버전 지정 및 업그레이드할 수 있습니다.

3. 다음 페이지에서는 ASP.NET Core 프로젝트 템플릿 집합을 제공합니다. 이 자습서에서는 **API**를 선택합니다.

    Visual Studio는 VotingData 서비스 프로젝트를 만들고 솔루션 탐색기에 표시합니다.

    ![솔루션 탐색기](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>VoteDataController.cs 파일 추가

**VotingData** 프로젝트에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가->새 항목->클래스**를 선택합니다. 파일 이름을 **VoteDataController.cs**로 지정하고 **추가**를 클릭합니다. 파일 내용을 다음으로 바꾸고 변경 내용을 저장합니다.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>서비스 연결

다음 단계에서는 두 서비스를 연결하고 프런트 엔드 웹 애플리케이션이 백 엔드 서비스에서 투표 정보를 가져와 설정하도록 합니다.

서비스 패브릭은 신뢰할 수 있는 서비스와 유연하게 통신할 수 있는 방법을 제공합니다. 단일 애플리케이션 내에는 TCP를 통해 액세스할 수 있는 서비스가 있을 수 있습니다. HTTP REST API 및 다른 서비스를 통해 액세스할 수 있는 기타 서비스는 웹 소켓을 통해 액세스할 수 있습니다. 제공되는 옵션 및 관련 장단점에 대한 배경 정보는 [서비스와의 통신](service-fabric-connect-and-communicate-with-services.md)을 참조하세요.

VotingWeb 프런트 엔드 웹 서비스가 VotingData 백 엔드 데이터 서비스와 통신할 수 있도록 이 자습서에서는 [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md) 및 [Service Fabric 역방향 프록시](service-fabric-reverseproxy.md)를 사용합니다. 역방향 프록시는 기본적으로 19081 포트를 사용하도록 구성되며 이 자습서에서 정상적으로 작동합니다. 역방향 프록시 포트는 클러스터를 설정하는 데 사용되는 Azure Resource Manager 템플릿에서 설정됩니다. 어떤 포트가 사용되는지 찾아보려면 **Microsoft.ServiceFabric/clusters** 리소스에서 클러스터 템플릿을 살펴보세요. 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
로컬 개발 클러스터에서 사용되는 역방향 프록시 포트를 찾아보려면 로컬 Service Fabric 클러스터 매니페스트에서 **HttpApplicationGatewayEndpoint** 요소를 확인하세요.
1. 브라우저 창을 열고 http:\//localhost:19080으로 이동하여 Service Fabric Explorer 도구를 엽니다.
2. **클러스터 -> 매니페스트**를 선택합니다.
3. HttpApplicationGatewayEndpoint 요소 포트를 기록해 둡니다. 기본적으로 19081이어야 합니다. 19081이 아닌 경우 다음 VotesController.cs 코드의 GetProxyAddress 메서드에서 포트를 변경해야 합니다.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>VotesController.cs 파일 업데이트

**VotingWeb** 프로젝트에서 *Controllers/VotesController.cs* 파일을 엽니다.  `VotesController` 클래스 정의 내용을 다음으로 바꾸고 변경 내용을 저장합니다. 이전 단계에서 검색한 역방향 프록시 포트가 19081이 아닌 경우 GetProxyAddress 메서드에 사용되는 포트를 19081에서 검색된 포트로 변경합니다.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>투표 애플리케이션 예제 연습

투표 애플리케이션은 두 가지 서비스로 구성됩니다.

* 웹 프런트 엔드 서비스(VotingWeb) - ASP.NET Core 웹 프런트 엔드 서비스로, 웹 페이지를 제공하며 백 엔드 서비스와 통신하기 위한 Web API를 공개합니다.
* 백 엔드 서비스(VotingData) - ASP.NET Core 웹 서비스로, 투표 결과를 디스크에 보관된 신뢰할 수 있는 사전에 저장하기 위한 API를 공개합니다.

![애플리케이션 다이어그램](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

애플리케이션에 투표하는 경우 다음 이벤트가 발생합니다.

1. JavaScript가 투표 요청을 웹 프런트 엔드 서비스의 Web API에 HTTP PUT 요청으로 보냅니다.

2. 웹 프런트 엔드 서비스는 프록시를 사용하여 HTTP PUT 요청을 찾아 백 엔드 서비스에 전달합니다.

3. 백 엔드 서비스는 들어오는 요청을 받고 업데이트된 결과를, 클러스터 내의 여러 노드에 복제되고 디스크에 보관된 신뢰할 수 있는 사전에 저장합니다. 애플리케이션의 모든 데이터가 클러스터에 저장되므로 데이터베이스가 필요하지 않습니다.

## <a name="debug-in-visual-studio"></a>Visual Studio에서 디버그

Visual Studio에서 애플리케이션을 디버깅할 때 로컬 Service Fabric 개발 클러스터를 사용합니다. 사용자 시나리오에 대해 디버깅 환경을 조정하는 옵션이 있습니다. 이 애플리케이션에서는 신뢰할 수 있는 사전을 사용하여 데이터를 백 엔드 서비스에 저장합니다. Visual Studio는 디버거를 중지하는 경우 기본값에 대해 애플리케이션을 제거합니다. 애플리케이션을 제거하면 백 엔드 서비스의 데이터도 제거됩니다. 디버깅 세션 간에 데이터를 유지하려면 **애플리케이션 디버그 모드**를 Visual Studio에서 **Voting** 프로젝트의 속성으로 변경할 수 있습니다.

코드에서 수행되는 작업을 살펴보려면 다음 단계를 완료합니다.

1. **VotingWeb\VotesController.cs** 파일을 열고 Web API의 **Put** 메서드(72줄)에서 중단점을 설정합니다.

2. **VotingData\VoteDataController.cs** 파일을 열고 Web API의 **Put** 메서드(54줄)에서 중단점을 설정합니다.

3. **F5** 키를 눌러 디버그 모드에서 애플리케이션을 시작합니다.

4. 브라우저로 돌아가서 투표 옵션을 클릭하거나 새 투표 옵션을 추가합니다. 웹 프런트 엔드의 api 컨트롤러에서 첫 번째 중단점에 도달합니다.
    

   1. 여기서 브라우저의 JavaScript가 프런트 엔드 서비스의 Web API 컨트롤러에 요청을 보냅니다.

      ![투표 프런트 엔드 서비스 추가](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. 먼저 백 엔드 서비스 **(1)** 에 대해 ReverseProxy에 대한 URL을 구성합니다.
   3. 그런 다음, ReverseProxy **(2)** 에 HTTP PUT 요청을 보냅니다.
   4. 마지막으로 백 엔드 서비스에서 **(3)** 클라이언트로 응답을 반환합니다.

5. 계속하려면 **F5** 키를 누릅니다.
   1. 이제 백 엔드 서비스의 중단점에 있습니다.

      ![투표 백 엔드 서비스 추가](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. **(1)** 메서드의 첫 번째 줄에서는 `StateManager`를 사용하여 신뢰할 수 있는 사전 `counts`를 가져오거나 추가합니다.
   3. 신뢰할 수 있는 사전에 있는 값과의 모든 상호 작용에는 트랜잭션이 필요하며 using 문 **(2)** 으로 트랜잭션이 만들어집니다.
   4. 트랜잭션에서는 투표 옵션에 대한 관련 키 값을 업데이트하고 **(3)** 작업을 커밋합니다. 커밋 메서드가 반환되면 사전에 데이터가 업데이트되고 클러스터의 다른 노드에 복제됩니다. 이제 데이터는 클러스터에 안전하게 저장되며 백 엔드 서비스는 데이터를 계속 제공하면서 다른 노드로 장애 조치할 수 있습니다.
6. 계속하려면 **F5** 키를 누릅니다.

디버깅 세션을 중지하려면 **Shift+F5** 키를 누릅니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET Core Web API 서비스를 상태 저장 Reliable Service로 만들기
> * ASP.NET Core Web 애플리케이션 서비스를 상태 비저장 웹 서비스로 만들기
> * 역방향 프록시를 사용하여 상태 저장 서비스와 통신

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure에 애플리케이션 배포](service-fabric-tutorial-deploy-app-to-party-cluster.md)
