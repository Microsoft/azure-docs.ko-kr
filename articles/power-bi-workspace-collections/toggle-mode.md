---
title: Power BI 작업 영역 컬렉션에서 보고서의 보기 및 편집 모드 간 전환 | Microsoft Docs
description: Power BI 작업 영역 컬렉션 내에서 보고서의 보기 및 편집 모드 간을 전환하는 방법을 알아봅니다.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: cab5d067387fcffe4f62b94e06960afe47d5f00c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123902"
---
# <a name="toggle-between-view-and-edit-mode-for-reports-in-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션에서 보고서의 보기 및 편집 모드 간 전환

Power BI 작업 영역 컬렉션 내에서 보고서의 보기 및 편집 모드 간을 전환하는 방법을 알아봅니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

## <a name="creating-an-access-token"></a>액세스 토큰 만들기

보고서를 보고 편집할 수 있는 기능을 제공하는 액세스 토큰을 만들어야 합니다. 보고서를 편집하고 저장하려면 **Report.ReadWrite** 토큰 권한이 필요합니다. 자세한 내용은 [Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)를 참조하세요.

> [!NOTE]
> 이러한 조건이 충족될 경우 기존 보고서를 편집하고 변경 내용을 저장할 수 있습니다. **다른 이름으로 저장**을 지원하는 기능을 원할 경우 추가 권한을 제공해야 합니다. 자세한 내용은 [범위](app-token-flow.md#scopes)를 참조하세요.

```csharp
using Microsoft.PowerBI.Security;

// rlsUsername and roles are optional
string scopes = "Report.ReadWrite";
PowerBIToken embedToken = PowerBIToken.CreateReportEmbedTokenForCreation(workspaceCollectionName, workspaceId, datasetId, null, null, scopes);

var token = embedToken.Generate("{access key}");
```

## <a name="embed-configuration"></a>Embed 구성

편집 모드에 있을 때 저장 단추를 보려면 사용 권한과 viewMode를 제공해야 합니다. 자세한 내용은 [Embed 구성 정보](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details)를 참조하세요.

예를 들어 JavaScript에서 다음을 수행합니다.

```html
   <div id="reportContainer"></div>

    <script>
    // Get models. Models, it contains enums that can be used.
    var models = window['powerbi-client'].models;

    // Embed configuration used to describe the what and how to embed.
    // This object is used when calling powerbi.embed.
    // This also includes settings and options such as filters.
    // You can find more information at https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details.
    var config= {
        type: 'report',
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
        embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.ReadWrite /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.View,
        settings: {
            filterPaneEnabled: true,
            navContentPaneEnabled: true
        }
    };

    // Get a reference to the embedded report HTML element
    var reportContainer = $('#reportContainer')[0];

    // Embed the report and display it within the div container.
    var report = powerbi.embed(reportContainer, config);
    </script>
```

여기서는 **models.ViewMode.View**로 설정된 **viewMode**를 기준으로 보기 모드에서 보고서를 포함합니다.

## <a name="view-mode"></a>보기 모드

편집 모드인 경우 다음 JavaScript를 사용하여 보기 모드로 전환할 수 있습니다.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to view mode.
report.switchMode("view");

```

## <a name="edit-mode"></a>편집 모드

보기 모드인 경우 다음 JavaScript를 사용하여 편집 모드로 전환할 수 있습니다.

```javascript
// Get a reference to the embedded report HTML element
var reportContainer = $('#reportContainer')[0];

// Get a reference to the embedded report.
report = powerbi.get(reportContainer);

// Switch to edit mode.
report.switchMode("edit");

```

## <a name="see-also"></a>참고 항목

[샘플 시작](get-started-sample.md)  
[보고서 포함](embed-report.md)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[PowerBI-CSharp Git 리포지토리](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git 리포지토리](https://github.com/Microsoft/PowerBI-Node)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
