---
title: Power BI 작업 영역 컬렉션의 데이터 세트에서 새 보고서 만들기 | Microsoft Docs
description: Power BI 작업 영역 컬렉션 보고서를 이제 자체 애플리케이션의 데이터 집합에서 만들 수 있습니다.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: e7499345f03e3deedb8972b0d51e8e676cb6c982
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110617"
---
# <a name="create-a-new-report-from-a-dataset-in-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션의 데이터 세트에서 새 보고서 만들기

Power BI 작업 영역 컬렉션 보고서를 이제 자체 애플리케이션의 데이터 집합에서 만들 수 있습니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

인증 방법은 보고서를 포함의 방법과 유사합니다. 이 방법은 특정 데이터 세트에만 적용되는 액세스 토큰을 기반으로 합니다. PowerBI.com에 사용되는 토큰은 AAD(Azure Active Directory)에서 발급됩니다. Power BI 작업 영역 컬렉션 토큰은 사용자의 애플리케이션에서 발급됩니다.

포함된 보고서를 만드는 경우 발급되는 토큰은 특정 데이터 세트에 대한 것입니다. 각각이 고유한 토큰을 갖도록 하기 위해 토큰을 같은 요소의 Embed URL에 연결해야 합니다. 포함된 보고서를 만들기 위해 액세스 토큰에 *Dataset.Read 및 Workspace.Report.Create* 범위를 제공해야 합니다.

## <a name="create-access-token-needed-to-create-new-report"></a>새 보고서를 만드는 데 필요한 액세스 토큰 만들기

Power BI 작업 영역 컬렉션은 HMAC 서명 JSON Web Token에 해당하는 Embed 토큰을 사용합니다. 이러한 토큰은 Power BI 작업 영역 컬렉션의 액세스 키로 서명됩니다. 기본적으로 애플리케이션에 포함할 보고서에 대해 읽기 전용 액세스를 제공하기 위해 Embed 토큰이 사용됩니다. Embed 토큰은 특정 보고서에 대해 발급되며 Embed URL에 연결되어야 합니다.

서명/토큰 암호화에 액세스 키가 사용되므로 서버에서 액세스 토큰을 만들어야 합니다. 액세스 토큰을 만드는 방법에 대한 자세한 내용은 [Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)를 참조하세요. [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) 메서드를 검토할 수도 있습니다. 다음은 Power BI용 .NET SDK를 사용할 경우의 예제입니다.

이 예제에는 새 보고서를 만들 데이터 세트 ID가 나옵니다. *Dataset.Read 및 Workspace.Report.Create*의 범위도 추가해야 합니다.

*PowerBIToken 클래스*를 사용하려면 [Power BI Core NuGut 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)를 설치해야 합니다.

**NuGet 패키지 설치**

```powershell
Install-Package Microsoft.PowerBI.Core
```

**C# 코드**

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Dataset.Read Workspace.Report.Create";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="create-a-new-blank-report"></a>비어 있는 새 보고서 만들기

새 보고서를 만들기 위해 만들기 구성이 제공됩니다. 여기에는 보고서를 만들 액세스 토큰, embedURL 및 datasetID가 포함됩니다. 이렇게 하려면 nuget [Power BI JavaScript 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)를 설치해야 합니다. embedUrl는 https://embedded.powerbi.com/appTokenReportEmbed일 뿐입니다.

> [!NOTE]
> [ 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)을 사용하여 기능을 테스트할 수 있습니다. 또한 사용할 수 있는 다양한 작업에 대한 코드 예제도 제공됩니다.

**NuGet 패키지 설치**

```powershell
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript 코드**

```html
<div id="reportContainer"></div>

<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);
</script>
```

*powerbi.createReport()* 를 호출하면 편집 모드의 빈 캔버스가 *div* 요소 내에 표시됩니다.

![새 빈 보고서](media/create-report-from-dataset/create-new-report.png)

## <a name="save-new-reports"></a>새 보고서 저장

**다른 이름으로 저장** 작업을 호출할 때까지 보고서는 생성되지 않습니다. 파일 메뉴에서 또는 JavaScript에서 이 작업을 수행할 수 있습니다.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);
    
    var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);
```

> [!IMPORTANT]
> **다른 이름으로 저장**을 호출해야만 새 보고서가 만들어집니다. 저장을 해도 캔버스에는 보고서가 아닌 편집 모드의 데이터 세트가 계속 표시됩니다. 다른 보고서의 경우처럼 새 보고서를 다시 로드해야 합니다.

![파일 메뉴 - 다른 이름으로 저장](media/create-report-from-dataset/save-new-report.png)

## <a name="load-the-new-report"></a>새 보고서 로드

애플리케이션이 일반 보고서를 포함하는 것과 같은 방식으로 포함하려는 새 보고서와 상호 작용하기 위해서는 새 보고서용으로 특수하게 새 토큰을 발급한 다음 embed 메서드를 호출해야 합니다.

```html
<div id="reportContainer"></div>
<script>
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>
```

## <a name="automate-save-and-load-of-a-new-report-using-the-saved-event"></a>"saved" 이벤트를 사용하여 새 보고서의 저장 및 로드 자동화

"다른 이름으로 저장" 프로세스를 자동화한 다음 새 보고서를 로드하기 위해 "saved" 이벤트를 사용할 수 있습니다. 이 이벤트는 save 작업이 완료되고 새 보고서 ID, 보고서 이름, 이전 보고서 ID(있는 경우)를 포함하는 Json 개체를 반환할 때 및 작업이 saveAs 또는 save일 때 발생합니다.

```json
{
  "reportObjectId": "5dac7a4a-4452-46b3-99f6-a25915e0fe54",
  "reportName": "newReport",
  "saveAs": true,
  "originalReportObjectId": null
}
```

"saved" 이벤트를 수신할 수 있는 프로세스를 자동화하려면 새 보고서 ID를 가져오고 새 토큰을 만든 다음 새 보고서를 포함합니다.

```html
<div id="reportContainer"></div>
<script>
var embedCreateConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        datasetId: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Create report
    var report = powerbi.createReport(reportContainer, embedCreateConfiguration);


   var saveAsParameters = {
        name: "newReport"
    };

    // SaveAs report
    report.saveAs(saveAsParameters);

    // report.on will add an event handler which prints to Log window.
    report.on("saved", function(event) {
        
         // get new Token
         var newReportId =  event.detail.reportObjectId;

        // create new Token. This is a function that the application should provide
        var newToken = createAccessToken(newReportId,scopes /*provide the wanted scopes*/);
        
        
    var embedConfiguration = {
        accessToken: newToken ,
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        reportId: newReportId,
    };

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
       
   // report.off removes a given event handler if it exists.
   report.off("saved");
    });
</script>
```

## <a name="see-also"></a>참고 항목

[샘플 시작](get-started-sample.md)  
[보고서 저장](save-reports.md)  
[보고서 포함](embed-report.md)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI Core NuGut 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[Power BI JavaScript 패키지](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
