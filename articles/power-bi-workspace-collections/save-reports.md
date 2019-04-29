---
title: Power BI 작업 영역 컬렉션에서 보고서 저장 | Microsoft Docs
description: Power BI 작업 영역 컬렉션 내에서 보고서를 저장하는 방법을 알아봅니다. 성공적인 작업을 위해서는 적절한 사용 권한이 필요합니다.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: ''
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
origin.date: 09/26/2018
ms.date: 03/05/2019
ms.author: v-junlch
ms.openlocfilehash: b61abee3382697d50b9a18de763c8a4d01e1ccba
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103885"
---
# <a name="save-reports-in-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션에서 보고서 저장

Power BI 작업 영역 컬렉션 내에서 보고서를 저장하는 방법을 알아봅니다. 보고서를 저장하려면 적절한 사용 권한이 필요합니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

Power BI 작업 영역 컬렉션 내에서 기존 보고서를 편집하고 저장할 수 있습니다. 새 보고서를 만들고 새 보고서로 저장하여 보고서를 만들 수도 있습니다.

보고서를 저장하려면 먼저 올바른 범위를 지정하여 특정 보고서에 대한 토큰을 만들어야 합니다.

- 저장을 사용하려면 Report.ReadWrite 범위가 필요합니다.
- 다른 이름으로 저장을 사용하려면 Report.Read 및 Workspace.Report.Copy 범위가 필요합니다.
- 저장 및 다른 이름으로 저장을 사용하려면 Report.ReadWrite 및 Workspace.Report.Copy가 필요합니다.

파일 메뉴에서 적절한 저장/다른 이름으로 저장 단추를 각각 사용하기 위해서는 보고서를 포함할 때 Embed 구성에서 적절한 사용 권한을 제공해야 합니다.

- models.Permissions.ReadWrite
- models.Permissions.Copy
- models.Permissions.All

> [!NOTE]
> 액세스 토큰에도 적절한 범위가 필요합니다. 자세한 내용은 [범위](app-token-flow.md#scopes)를 참조하세요.

## <a name="embed-report-in-edit-mode"></a>편집 모드에서 보고서 포함

앱 내에서 편집 모드로 보고서를 포함하려고 하므로 Embed 구성에서 올바른 속성을 전달하고 powerbi.embed()를 호출합니다. 편집 모드에 있을 때 저장 및 다른 이름으로 저장 단추를 보려면 사용 권한과 viewMode를 제공해야 합니다. 자세한 내용은 [Embed 구성 정보](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embed-Configuration-Details)를 참조하세요.

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
        embedUrl: 'https://embedded.powerbi.cn/appTokenReportEmbed',
        id:  '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
        permissions: models.Permissions.All /*both save & save as buttons will be visible*/,
        viewMode: models.ViewMode.Edit,
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

이제 보고서가 편집 모드에서 앱에 포함됩니다.

## <a name="save-report"></a>보고서 저장

적절한 토큰 및 사용 권한을 사용하여 편집 모드에서 보고서를 포함한 후에는 파일 메뉴 또는 javascript에서 보고서를 저장할 수 있습니다.

```javascript
 // Get a reference to the embedded report.
    report = powerbi.get(reportContainer);

 // Save report
    report.save();
```

## <a name="save-as"></a>다른 이름으로 저장

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
> *다른 이름으로 저장* 이후에만 새 보고서가 만들어집니다. 저장 후에는 여전히 캔버스에서 새 보고서가 아닌 이전 보고서가 편집 모드로 표시됩니다. 만들어진 새 보고서를 포함합니다. 새 보고서를 포함하려면 보고서가 만들어질 때마다 새 액세스 토큰이 필요합니다.

그런 다음 *다른 이름으로 저장* 후에 새 보고서를 로드해야 합니다. 새 보고서를 로드하는 것은 보고서를 포함하는 것과 비슷합니다.

```html
<div id="reportContainer"></div>
<script>  
var embedConfiguration = {
        accessToken: 'eyJ0eXAiO...Qron7qYpY9MJ',
        embedUrl: 'https://embedded.powerbi.cn/appTokenReportEmbed',
        reportId: '5dac7a4a-4452-46b3-99f6-a25915e0fe54',
    };
    
    // Grab the reference to the div HTML element that will host the report
    var reportContainer = $('#reportContainer')[0];

    // Embed report
    var report = powerbi.embed(reportContainer, embedConfiguration);
</script>    
```

## <a name="see-also"></a>참고 항목

[샘플 시작](get-started-sample.md)  
[보고서 포함](embed-report.md)  
[데이터 세트에서 새 보고서 만들기](create-report-from-dataset.md)  
[Power BI 작업 영역 컬렉션에서 인증 및 권한 부여](app-token-flow.md)  
[Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)  
[JavaScript Embed 샘플](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)


<!-- Update_Description: update metedata properties -->