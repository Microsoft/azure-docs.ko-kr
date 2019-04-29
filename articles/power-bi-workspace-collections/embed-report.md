---
title: Azure Power BI 작업 영역 컬렉션에 보고서 포함 | Microsoft Docs
description: Power BI 작업 영역 컬렉션에 있는 보고서를 애플리케이션에 포함하는 방법을 알아봅니다.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: a7d6ccc2360d63b888dc46badc742f2618a08dac
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110668"
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션에 보고서 포함

Power BI 작업 영역 컬렉션에 있는 보고서를 애플리케이션에 포함하는 방법을 알아봅니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

실제로 애플리케이션에 보고서를 포함하는 방법을 살펴보겠습니다. 여기서는 작업 영역 컬렉션의 작업 영역 내에 보고서가 이미 있다고 가정합니다. 해당 단계를 아직 완료하지 않으면 [Power BI 작업 영역 컬렉션 시작](get-started.md)을 참조하세요.

JavaScript와 함께 .NET(C#) 또는 Node.js SDK를 사용하여 Power BI 작업 영역 컬렉션으로 애플리케이션을 손쉽게 빌드할 수 있습니다.

## <a name="using-the-access-keys-to-use-rest-apis"></a>액세스 키를 사용하여 REST API 사용

REST API를 호출하려면 지정된 작업 영역 컬렉션에 대해 Azure Portal에서 가져올 수 있는 액세스 키를 전달할 수 있습니다. 자세한 내용은 [Power BI 작업 영역 컬렉션 시작](get-started.md)을 참조하세요.

## <a name="get-a-report-id"></a>보고서 ID 가져오기

모든 액세스 토큰은 보고서를 기준으로 합니다. 포함할 보고서에 대해 지정된 보고서 ID를 가져와야 합니다. 이 작업은 [보고서 가져오기](https://msdn.microsoft.com/library/azure/mt711510.aspx) REST API 호출에 따라 수행할 수 있습니다. 이렇게 하면 보고서 ID와 Embed URL이 반환됩니다. 이 작업은 Power BI .NET SDK를 사용하거나 REST API를 직접 호출하여 수행할 수 있습니다.

### <a name="using-the-power-bi-net-sdk"></a>Power BI .NET SDK 사용

.NET SDK를 사용하는 경우 Azure Portal에서 가져오는 액세스 키를 기준으로 하는 토큰 자격 증명을 만들어야 합니다. 이렇게 하려면 [Power BI API NuGet 패키지](https://www.nuget.org/profiles/powerbi)를 설치해야 합니다.

**NuGet 패키지 설치**

```powershell
Install-Package Microsoft.PowerBI.Api
```

**C# 코드**

```csharp
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>REST API 직접 호출

```csharp
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>액세스 토큰 만들기

Power BI 작업 영역 컬렉션은 HMAC 서명 JSON 웹 토큰에 해당하는 Embed 토큰을 사용합니다. 이러한 토큰은 Power BI 작업 영역 컬렉션의 액세스 키로 서명됩니다. 기본적으로 애플리케이션에 포함할 보고서에 대해 읽기 전용 액세스를 제공하기 위해 Embed 토큰이 사용됩니다. Embed 토큰은 특정 보고서에 대해 발급되며 Embed URL에 연결되어야 합니다.

서명/토큰 암호화에 액세스 키가 사용되므로 서버에서 액세스 토큰을 만들어야 합니다. 액세스 토큰을 만드는 방법에 대한 자세한 내용은 [Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)를 참조하세요. [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) 메서드를 검토할 수도 있습니다. 다음은 Power BI용 .NET SDK를 사용할 경우의 예제입니다.

이전에 검색한 보고서 ID를 사용합니다. Embed 토큰이 만들어지면 액세스 키를 사용하여 javascript 관점에서 사용할 수 있는 토큰을 생성합니다. *PowerBIToken 클래스*를 사용하려면 [Power BI Core NuGut 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)를 설치해야 합니다.

**NuGet 패키지 설치**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C# 코드**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>Embed 토큰에 사용 권한 범위 추가

Embed 토큰으로 사용하는 경우 액세스 권한을 제공하는 리소스 사용을 제한하려고 할 수 있습니다. 이러한 이유로 범위가 지정된 사용 권한으로 토큰을 생성할 수 있습니다. 자세한 내용은 [범위](app-token-flow.md#scopes)를 참조하세요.

## <a name="embed-using-javascript"></a>JavaScript를 사용하여 포함

액세스 토큰 및 보고서 ID를 설정한 후 JavaScript를 사용하여 보고서를 포함할 수 있습니다. 이렇게 하려면 NuGet [Power BI JavaScript 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)를 설치해야 합니다. embedUrl는 https://embedded.powerbi.com/appTokenReportEmbed일 뿐입니다.

> [!NOTE]
> [ 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)을 사용하여 기능을 테스트할 수 있습니다. 또한 사용할 수 있는 다양한 작업에 대한 코드 예제도 제공됩니다.

**NuGet 패키지 설치**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript 코드**

```html
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

<script>
var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
</script>
```

### <a name="set-the-size-of-embedded-elements"></a>포함된 요소의 크기 설정

보고서는 컨테이너의 크기에 따라 자동으로 포함됩니다. 포함된 항목의 기본 크기를 재정의하려면 CSS 클래스 특성 또는 너비 및 높이에 대한 인라인 스타일을 추가하기만 하면 됩니다.

## <a name="see-also"></a>참고 항목

[샘플 시작](get-started-sample.md)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI API NuGet 패키지](https://www.nuget.org/profiles/powerbi)
[Power BI Core NuGut 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI-CSharp Git 리포지토리](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git 리포지토리](https://github.com/Microsoft/PowerBI-Node)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
