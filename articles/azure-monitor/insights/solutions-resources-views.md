---
title: 관리 솔루션 보기 | Microsoft Docs
description: '관리 솔루션은 대개 데이터를 시각화하기 위한 하나 이상의 보기를 포함합니다.  이 문서에서는 뷰 디자이너에서 만들어진 보기를 내보내고 관리 솔루션에 포함하는 방법을 설명합니다. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: cefb83d5336bb99fd09001b5ea369a0b8fc4b942
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596640"
---
# <a name="views-in-management-solutions-preview"></a>관리 솔루션 보기(미리 보기)
> [!NOTE]
> 현재 Preview로 제공되는 관리 솔루션 만들기에 대한 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.    


[관리 솔루션](solutions.md)은 대개 데이터를 시각화하기 위한 하나 이상의 보기를 포함합니다.  이 문서에서는 [뷰 디자이너](../../azure-monitor/platform/view-designer.md)에서 만들어진 보기를 내보내고 관리 솔루션에 포함하는 방법을 설명합니다.  

> [!NOTE]
> 이 문서의 샘플에는 관리 솔루션에 필요하거나 공통적이며 [Azure의 관리 솔루션 디자인 및 빌드](solutions-creating.md)에서 설명한 매개 변수와 변수가 사용
>
>

## <a name="prerequisites"></a>필수 조건
이 문서에서는 [관리 솔루션을 만드는](solutions-creating.md) 방법과 솔루션 파일의 구조를 잘 알고 있다고 가정합니다.

## <a name="overview"></a>개요
관리 솔루션에 보기를 포함하려면 [솔루션 파일](solutions-creating.md)에서 이에 대한 **리소스**를 만듭니다.  보기의 세부 구성을 설명하는 JSON은 일반적으로 복잡하며 일반적인 솔루션 작성자가 수동으로 만들 수 있는 것이 아닙니다.  가장 일반적인 방법은 [뷰 디자이너](../../azure-monitor/platform/view-designer.md)를 사용하여 보기를 만들고, 내보내고 나서, 세부 구성을 솔루션에 추가하는 것입니다.

보기를 솔루션에 추가하는 기본 단계는 다음과 같습니다.  각 단계는 아래 섹션에서 자세히 설명합니다.

1. 보기를 파일로 내보냅니다.
2. 솔루션에서 보기 리소스를 만듭니다.
3. 보기 세부 정보를 추가합니다.

## <a name="export-the-view-to-a-file"></a>보기를 파일로 내보내기
[Log Analytics 뷰 디자이너](../../azure-monitor/platform/view-designer.md)의 지침에 따라 보기를 파일로 내보냅니다.  내보낸 파일의 형식은 [솔루션 파일과 같은 요소](solutions-solution-file.md)가 포함된 JSON 형식이 됩니다.  

보기 파일의 **리소스** 요소에는 Log Analytics 작업 영역을 나타내는 **Microsoft.OperationalInsights/workspaces** 형식의 리소스가 포함됩니다.  이 요소에는 보기를 나타내고 세부 구성이 들어 있는 **views** 형식의 하위 요소가 포함됩니다.  이 요소의 세부 정보를 복사하여 솔루션에 붙여넣습니다.

## <a name="create-the-view-resource-in-the-solution"></a>솔루션에서 보기 리소스 만들기
솔루션 파일의 **resources** 요소에 다음 보기 리소스를 추가합니다.  이 작업에는 아래 설명된, 추가해야 하는 변수가 사용됩니다.  **Dashboard** 및 **OverviewTile** 속성은 내보낸 보기 파일의 해당 속성으로 덮어쓸 자리 표시자입니다.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

솔루션 파일의 variables 요소에 다음 변수를 추가하고 해당 값을 솔루션의 값으로 바꿉니다.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

이제 내보낸 보기 파일의 전체 보기 리소스를 복사할 수 있지만, 해당 리소스가 솔루션에서 작동하려면 다음과 같이 변경해야 합니다.  

* 보기 리소스의 **type**을 **views**에서 **Microsoft.OperationalInsights/workspaces**로 변경해야 합니다.
* 작업 영역 이름을 포함하도록 보기 리소스의 **name** 속성을 변경해야 합니다.
* 솔루션에 작업 영역 리소스가 정의되어 있지 않으므로 작업 영역의 종속성을 제거해야 합니다.
* **DisplayName** 속성을 보기에 추가해야 합니다.  **Id**, **Name** 및 **DisplayName**이 모두 일치해야 합니다.
* 필요한 매개 변수 집합과 일치하도록 매개 변수 이름을 변경해야 합니다.
* 변수는 솔루션에서 정의되고 적절한 속성에서 사용되어야 합니다.

### <a name="log-analytics-api-version"></a>Log Analytics API 버전
Resource Manager 템플릿에 정의된 모든 Log Analytics 리소스에는 리소스가 사용해야 하는 API의 버전을 정의하는 **apiVersion** 속성이 있습니다.  이 버전은 [레거시 및 업그레이드된 쿼리 언어](../../azure-monitor/log-query/log-query-overview.md)를 사용하는 쿼리가 있는 보기와는 다릅니다.  

 다음 표에서는 레거시 및 업그레이드된 작업 영역에서 보기의 Log Analytics API 버전을 지정합니다. 

| 작업 영역 버전 | API 버전 | 쿼리 |
|:---|:---|:---|
| v1(레거시)   | 2015-11-01-preview | 레거시 형식입니다.<br> 예제: Type=Event EventLevelName = Error  |
| v2(업그레이드된 버전) | 2015-11-01-preview | 레거시 형식입니다.  설치 시 업그레이드된 형식으로 변환됩니다.<br> 예제: Type=Event EventLevelName = Error<br>변환 대상: Event &#124; where EventLevelName == "Error"  |
| v2(업그레이드된 버전) | 2017-03-03-preview | 업그레이드 형식입니다. <br>예제: Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>보기 세부 정보 추가
내보낸 보기 파일의 보기 리소스에는 보기의 세부 구성이 들어 있는 **properties** 요소의 두 가지 요소 **Dashboard** 및 **OverviewTile**이 포함됩니다.  이 두 가지 요소와 해당 내용을 솔루션 파일에 있는 보기 리소스의 **properties** 요소에 복사합니다.

## <a name="example"></a>예
예를 들어 다음 샘플에서는 보기가 있는 단순 솔루션 파일을 보여 줍니다.  공간 문제로 **Dashboard** 및 **OverviewTile** 내용에는 줄임표(...)가 표시됩니다.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>다음 단계
* [관리 솔루션](solutions-creating.md)을 만드는 방법에 대한 자세한 내용을 알아보세요.
* [관리 솔루션에 Automation runbook](solutions-resources-automation.md)을 포함합니다.
