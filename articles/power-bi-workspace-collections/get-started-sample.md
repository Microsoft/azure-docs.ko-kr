---
title: 샘플 시작
description: 이 문서에서 Power BI 작업 영역 컬렉션 시작 샘플을 소개합니다.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: 36566d72de1505cd5689aaad737d7948b80801ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110551"
---
# <a name="get-started-with-power-bi-workspace-collections-sample"></a>Power BI 작업 영역 컬렉션 시작 샘플

**Microsoft Power BI 작업 영역 컬렉션**을 사용하면 Power BI 보고서를 웹 또는 모바일 애플리케이션에 통합할 수 있습니다. 이 문서에서는 **Power BI 작업 영역 컬렉션** 시작 샘플을 소개합니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

계속 진행하기 전에 다음 리소스를 저장하려고 합니다. 이렇게 하면 Power BI 보고서를 샘플 앱 및 사용자 고유의 앱에 통합할 때도 도움이 됩니다.

* [샘플 작업 영역 웹앱](https://go.microsoft.com/fwlink/?LinkId=761493)
* [Power BI 작업 영역 컬렉션 API 참조](https://msdn.microsoft.com/library/azure/mt711507.aspx)
* [Power BI.NET SDK](https://go.microsoft.com/fwlink/?LinkId=746472) (NuGet을 통해 사용 가능)
* [JavaScript Report Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo)

> [!NOTE]
> Power BI 작업 영역 컬렉션 시작 샘플을 구성하고 실행하려면 먼저 Azure 구독에 적어도 하나의 **작업 영역 컬렉션**을 만들어야 합니다. Azure Portal에 **작업 영역 컬렉션** 을 만드는 방법을 알아보려면 [Power BI 작업 영역 컬렉션 시작](get-started.md)을 참조하세요.

## <a name="configure-the-sample-app"></a>샘플 앱 구성

샘플 앱을 실행하는 데 필요한 구성 요소에 액세스할 Visual Studio 개발 환경을 설정하는 방법을 단계적으로 안내합니다.

1. [Power BI 작업 영역 컬렉션 - 보고서를 웹앱으로 통합](https://go.microsoft.com/fwlink/?LinkId=761493) 샘플을 GitHub에서 다운로드하여 압축을 해제합니다.
2. Visual Studio에서 **PowerBI-embedded.sln** 을 엽니다. 이 솔루션에서 사용되는 패키지를 업데이트하려면 NuGet 패키지 관리자 콘솔에서 **Update-Package** 명령을 실행해야 할 수 있습니다.
3. 솔루션을 빌드하십시오.
4. **ProvisionSample** 콘솔 앱을 실행합니다. 샘플 콘솔 앱에서 작업 영역을 프로비전하고 PBIX 파일을 가져옵니다.
5. 새 **작업 영역**을 프로비전하려면 옵션 1 **컬렉션 관리**를 선택한 다음 옵션 6 **새 작업 영역 프로비전**을 선택합니다.
6. 새 **보고서**를 가져오려면 옵션 2 **관리 보고**를 선택한 다음, 옵션 3 **PBIX 데스크톱 파일을 작업 영역에 가져오기**를 선택합니다.

7. **작업 영역 컬렉션** 이름 및 **선택키**를 입력합니다. **Azure Portal**에서 이러한 정보를 가져올 수 있습니다. **선택키**를 가져오는 방법에 대해 알아보려면 Microsoft Power BI Embedded 시작의 [Power BI API 선택키 보기](get-started.md#view-power-bi-api-access-keys) 를 참조하세요.

    ![Azure Portal 내의 액세스 키](media/get-started-sample/azure-portal.png)
8. 이 문서의 뒷부분에서 사용할 수 있도록 새로 만든 **작업 영역 ID** 를 복사하여 저장합니다. **작업 영역 ID**를 만든 후에 **Azure Portal**에서 확인할 수 있습니다.

    ![Azure Portal 내의 작업 영역 ID](media/get-started-sample/workspace-id.png)
9. PBIX 파일을 **작업 영역**으로 가져오려면 옵션 **6, 기존 작업 영역**에 PBIX Desktop 파일을 가져옵니다. 사용할 수 있는 PBIX 파일이 없는 경우 [소매점 분석 샘플 PBIX](https://go.microsoft.com/fwlink/?LinkID=780547)를 다운로드할 수 있습니다.
10. 메시지가 표시되면 **데이터 세트**의 식별 이름을 입력합니다.

다음과 같은 응답이 표시됩니다.

```
Checking import state... Publishing
Checking import state... Succeeded
```

> [!NOTE]
> PBIX 파일에 직접 쿼리 연결이 포함되어 있는 경우 연결 문자열을 업데이트하도록 옵션 7을 실행합니다.

이제 Power BI PBIX 보고서를 **작업 영역**에 가져왔습니다. 다음으로는 **Power BI 작업 영역 컬렉션** 시작 샘플 웹앱을 실행하는 방법을 살펴보겠습니다.

## <a name="run-the-sample-web-app"></a>샘플 웹앱 실행

웹앱 샘플은 **작업 영역**으로 가져온 보고서를 렌더링하는 샘플 애플리케이션입니다. 웹앱 샘플을 구성하는 방법은 다음과 같습니다.

1. **PowerBI-embedded** Visual Studio 솔루션에서 **EmbedSample** 웹 애플리케이션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
2. **EmbedSample** 웹 애플리케이션의 **web.config**에서 **appSettings**, 즉 **AccessKey**, **WorkspaceCollection** 이름 및 **WorkspaceId**를 편집합니다.

    ```xml
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. **EmbedSample** 웹 애플리케이션을 실행합니다.

**EmbedSample** 웹 애플리케이션을 실행하면 왼쪽 탐색 패널에 **보고서** 메뉴가 포함되어야 합니다. 가져온 보고서를 보려면 **보고서**를 확장하고 보고서를 클릭합니다. [소매 분석 샘플 PBIX](https://go.microsoft.com/fwlink/?LinkID=780547)를 가져온 경우 샘플 웹앱이 다음과 같이 보입니다.

![샘플 애플리케이션 내의 샘플 왼쪽 탐색 창](media/get-started-sample/sample-left-nav.png)

보고서를 클릭하면 **EmbedSample** 웹 애플리케이션이 다음과 같이 표시되어야 합니다.

![애플리케이션 내에 표시되는 샘플 보고서](media/get-started-sample/sample-web-app.png)

## <a name="explore-the-sample-code"></a>샘플 코드 탐색

**Microsoft Power BI 작업 영역 컬렉션** 샘플은 **Power BI** 보고서를 앱에 통합하는 방법을 보여주는 웹앱의 예입니다. MVC(Model-View-Controller) 디자인 패턴을 사용하여 모범 사례를 보여 줍니다. 이 섹션에서는 **PowerBI-embedded** 웹앱 솔루션 내에서 탐색할 수 있는 샘플 코드 부분을 중점적으로 살펴봅니다. MVC(Model-View-Controller) 패턴은 도메인의 모델링, 프레젠테이션 및 사용자 입력에 따른 작업을 Model, View 및 Control이라는 별도의 세 가지 클래스로 구분합니다. MVC에 대한 알아보려면 [ASP.NET에 대해 알아보기](https://www.asp.net/mvc)를 참조하세요.

**Microsoft Power BI 작업 영역 컬렉션** 샘플 코드는 다음과 같이 구분됩니다. 각 섹션에는 샘플 코드를 쉽게 찾을 수 있도록 PowerBI-embedded.sln 솔루션에 파일 이름이 포함되어 있습니다.

> [!NOTE]
> 이 섹션에서는 코드가 작성된 방법을 보여 주는 샘플 코드에 대해 요약합니다. 전체 샘플을 보려면 Visual Studio에서 PowerBI-embedded.sln을 로드하세요.

### <a name="model"></a>모델

이 샘플에는 **ReportsViewModel** 및 **ReportViewModel**이 있습니다.

**ReportsViewModel.cs**: 여러 Power BI 보고서를 나타냅니다.

```csharp
public class ReportsViewModel
{
    public List<Report> Reports { get; set; }
}
```

**ReportViewModel.cs**: 하나의 Power BI 보고서를 나타냅니다.

```csharp
public class ReportViewModel
{
    public IReport Report { get; set; }

    public string AccessToken { get; set; }
}
```

### <a name="connection-string"></a>연결 문자열

연결 문자열은 다음 형식이어야 합니다.

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

일반적인 서버 및 데이터베이스 특성을 사용할 수 없습니다. 예를 들면 다음과 같습니다. Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### <a name="view"></a>보기

**보기**를 통해 여러 Power BI **보고서** 및 하나의 Power BI **보고서** 표시를 관리합니다.

**Reports.cshtml**: **Model.Reports**를 반복하여 **ActionLink**를 만듭니다. **ActionLink**는 다음과 같이 구성됩니다.

| 부 | 설명 |
| --- | --- |
| 제목 |보고서의 이름입니다. |
| QueryString |보고서 ID에 대한 링크입니다. |
```cshtml
<div id="reports-nav" class="panel-collapse collapse">
    <div class="panel-body">
        <ul class="nav navbar-nav">
            @foreach (var report in Model.Reports)
            {
                var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                <li class="@reportClass">
                    @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                </li>
            }
        </ul>
    </div>
</div>
```
Report.cshtml: **Model.AccessToken** 및 **PowerBIReportFor**에 대한 람다 식을 설정합니다.

```cshtml
@model ReportViewModel

...

<div class="side-body padding-top">
    @Html.PowerBIAccessToken(Model.AccessToken)
    @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
</div>
```

### <a name="controller"></a>Controller

**DashboardController.cs**: **앱 토큰**을 전달하는 PowerBIClient를 만듭니다. **자격 증명**을 가져오기 위해 **서명 키**에서 JWT(JSON Web Token)가 생성됩니다. **자격 증명**은 **PowerBIClient** 인스턴스를 만드는 데 사용됩니다. **PowerBIClient** 인스턴스가 작성되면 GetReports() 및 GetReportsAsync()를 호출할 수 있습니다.


CreatePowerBIClient()

```csharp
private IPowerBIClient CreatePowerBIClient()
{
    var credentials = new TokenCredentials(accessKey, "AppKey");
    var client = new PowerBIClient(credentials)
    {
        BaseUri = new Uri(apiUrl)
    };

    return client;
}
```

ActionResult Reports()

```csharp
public ActionResult Reports()
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

        var viewModel = new ReportsViewModel
        {
            Reports = reportsResponse.Value.ToList()
        };

        return PartialView(viewModel);
    }
}
```

작업<ActionResult> 보고서(문자열 reportId)

```csharp
public async Task<ActionResult> Report(string reportId)
{
    using (var client = this.CreatePowerBIClient())
    {
        var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
        var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
        var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

        var viewModel = new ReportViewModel
        {
            Report = report,
            AccessToken = embedToken.Generate(this.accessKey)
        };

        return View(viewModel);
    }
}
```

### <a name="integrate-a-report-into-your-app"></a>앱에 보고서 통합

**보고서**를 만든 후에는 **IFrame**을 사용하여 Power BI **보고서**를 포함합니다. 다음은 **Microsoft Power BI 작업 영역 컬렉션** 샘플 내 powerbi.js의 코드 조각입니다.

```javascript
init: function() {
    var embedUrl = this.getEmbedUrl();
    var iframeHtml = '<iframe style="width:100%;height:100%;" src="' + embedUrl + 
        '" scrolling="no" allowfullscreen="true"></iframe>';
    this.element.innerHTML = iframeHtml;
    this.iframe = this.element.childNodes[0];
    this.iframe.addEventListener('load', this.load.bind(this), false);
}
```

## <a name="filter-reports-embedded-in-your-application"></a>애플리케이션에 포함된 보고서 필터링

URL 구문을 사용하여 포함된 보고서를 필터링할 수 있습니다. 이렇게 하려면 지정된 필터를 사용하여 **eq** 연산자가 포함된 **$filter** 쿼리 문자열 매개 변수를 iFrame src url에 추가합니다. 필터 쿼리 구문은 다음과 같습니다.

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [!NOTE]
> {tableName/fieldName}은(는) 공백이나 특수 문자를 포함할 수 없습니다. {fieldValue}은(는) 단일 범주 값을 허용합니다.  

## <a name="see-also"></a>참고 항목

[일반적인 Microsoft Power BI 작업 영역 컬렉션 시나리오](scenarios.md)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[보고서 포함](embed-report.md)  
[데이터 세트에서 새 보고서 만들기](create-report-from-dataset.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
