---
title: Azure에서 관리 솔루션 파일 만들기 | Microsoft Docs
description: 관리 솔루션은 고객이 Azure 환경에 추가할 수 있는 패키지된 관리 시나리오를 제공합니다.  이 문서에서는 자체 환경에 사용할 관리 솔루션 또는 고객에게 제공할 관리 솔루션을 만드는 방법에 대해 자세히 설명합니다.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e5c27911fe86a6916235014f8602327df929e20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595759"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Azure에서 관리 솔루션 파일 만들기(Preview)
> [!NOTE]
> 현재 미리 보기 상태로 제공되는 Azure에서 관리 솔루션을 만드는 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.  

Azure의 관리 솔루션은 [Resource Manager 템플릿](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)으로 구현됩니다.  관리 솔루션을 작성하는 방법에서 주요 작업은 [템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)입니다.  이 문서에서는 솔루션에 사용되는 템플릿의 고유한 세부 정보와 일반적인 솔루션 리소스를 구성하는 방법을 설명합니다.


## <a name="tools"></a>도구

아무 텍스트 편집기나 사용하여 솔루션 파일로 작업할 수 있지만 다음 문서에 설명된 대로 Visual Studio 또는 Visual Studio 코드에서 제공되는 기능을 활용하는 것이 좋습니다.

- [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Visual Studio 코드에서 Azure Resource Manager 템플릿으로 작업](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>구조
관리 솔루션 파일의 기본 구조는 다음과 같은 [Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md#template-format)과 동일합니다.  아래의 각 섹션에서는 솔루션의 최상위 수준 요소와 해당 콘텐츠에 대해 설명합니다.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>매개 변수
[매개 변수](../../azure-resource-manager/resource-group-authoring-templates.md#parameters)는 사용자가 관리 솔루션을 설치할 때 사용자에게 요구할 값입니다.  모든 솔루션에 포함될 표준 매개 변수가 있고, 특정 솔루션에 필요한 다른 매개 변수를 추가할 수 있습니다.  사용자가 솔루션을 설치할 때 매개 변수 값을 제공하는 방법은 솔루션 설치 방법과 특정 매개 변수에 따라 다릅니다.

사용자가 Azure Marketplace 또는 Azure 빠른 시작 템플릿을 통해 [관리 솔루션을 설치](solutions.md#install-a-monitoring-solution)하는 경우 [Log Analytics 작업 영역 및 Automation 계정](solutions.md#log-analytics-workspace-and-automation-account)을 선택하라는 메시지가 표시됩니다.  이러한 템플릿은 각 표준 매개 변수의 값을 채우는 데 사용됩니다.  사용자에게는 표준 매개 변수의 값을 직접 제공하라는 메시지가 표시되지 않고 추가 매개 변수의 값을 제공하라는 메시지가 표시됩니다.


샘플 매개 변수는 다음과 같습니다.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

다음 표에서는 매개 변수의 특성을 설명합니다.

| 특성 | 설명 |
|:--- |:--- |
| type |매개 변수의 데이터 형식입니다. 사용자에게 표시되는 입력 컨트롤은 데이터 형식에 따라 다릅니다.<br><br>bool - 드롭다운 상자<br>string - 텍스트 상자<br>int - 텍스트 상자<br>securestring - 암호 필드<br> |
| category |매개 변수의 선택적 범주입니다.  같은 범주의 매개 변수는 함께 그룹화됩니다. |
| control |string 매개 변수의 추가 기능입니다.<br><br>datetime - Datetime 컨트롤이 표시됩니다.<br>guid - Guid 값이 자동으로 생성되고 매개 변수가 표시되지 않습니다. |
| description |매개 변수에 대한 선택적 설명입니다.  매개 변수 옆에 정보 풍선으로 표시됩니다. |

### <a name="standard-parameters"></a>표준 매개 변수
다음 표에는 모든 관리 솔루션에 대한 표준 매개 변수가 나열됩니다.  솔루션이 Azure Marketplace 또는 빠른 시작 템플릿에서 설치될 경우 매개 변수에 대한 메시지가 표시되지 않고 이러한 값이 자동으로 채워집니다.  솔루션이 다른 방법으로 설치될 경우 사용자가 값을 입력해야 합니다.

> [!NOTE]
> Azure Marketplace 및 빠른 시작 템플릿의 사용자 인터페이스에는 테이블의 매개 변수 이름을 사용해야 합니다.  다른 매개 변수 이름을 사용하면 사용자에게 매개 변수에 대한 메시지가 표시되고 매개 변수가 자동으로 채워지지 않습니다.
>
>

| 매개 변수 | type | 설명 |
|:--- |:--- |:--- |
| accountName |string |Azure Automation 계정 이름입니다. |
| pricingTier |string |Log Analytics 작업 영역 및 Azure Automation 계정의 가격 책정 계층입니다. |
| regionId |string |Azure Automation 계정의 지역입니다. |
| solutionName |string |솔루션의 이름입니다.  빠른 시작 템플릿을 통해 솔루션을 배포하는 경우 사용자에게 지정하도록 요구하는 대신 문자열을 정의할 수 있도록 solutionName을 매개 변수로 정의해야 합니다. |
| workspaceName |string |Log Analytics 작업 영역 이름입니다. |
| workspaceRegionId |string |Log Analytics 작업 영역의 지역입니다. |


다음은 솔루션 파일에 복사하여 붙여넣을 수 있는 표준 매개 변수 구조입니다.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


**parameters('매개 변수 이름')** 구문을 사용하여 솔루션의 다른 요소에 있는 매개 변수 값을 참조할 수 있습니다.  예를 들어 작업 영역 이름에 액세스하려면 **parameters('workspaceName')** 을 사용합니다.

## <a name="variables"></a>variables
[Variables](../../azure-resource-manager/resource-group-authoring-templates.md#variables)는 관리 솔루션의 나머지 부분에 사용할 값입니다.  이러한 값은 솔루션을 설치하는 사용자에게 노출되지 않습니다.  작성자가 솔루션을 만드는 동안 여러 번 사용할지도 모르는 값을 관리할 수 있는 단일 위치를 제공하는 것이 이러한 값의 목적입니다. 솔루션 관련 값을 **resources** 요소로 하드 코드하지 않고 해당 값을 변수에 포함해야 합니다.  이렇게 하면 코드를 더 쉽게 읽을 수 있고 이후 버전에서 이들 값을 쉽게 변경할 수 있습니다.

다음은 솔루션에 일반적인 매개 변수를 사용한 **variables** 요소의 예입니다.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

**variables('변수 이름')** 구문을 사용하여 솔루션 전체의 변수 값을 참조할 수 있습니다.  예를 들어 SolutionName 변수에 액세스하려면 **variables('solutionName')** 을 사용합니다.

여러 값 집합이 있는 복잡한 변수를 정의할 수도 있습니다.  이러한 변수는 서로 다른 형식의 리소스에 대해 여러 속성을 정의하는 관리 솔루션에서 특히 유용합니다.  예를 들어 위에 표시된 솔루션 변수를 다음으로 재구성할 수 있습니다.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

이 경우 **variables('변수 이름').property** 구문을 사용하여 솔루션 전체의 변수 값을 참조할 수 있습니다.  예를 들어 SolutionName 변수에 액세스하려면 **variables('솔루션').Name**을 사용합니다.

## <a name="resources"></a>리소스
[리소스](../../azure-resource-manager/resource-group-authoring-templates.md#resources)는 관리 솔루션에서 설치 및 구성할 다양한 리소스를 정의합니다.  이번 파트가 아마도 템플릿에서 가장 크고 복잡한 내용일 것입니다.  리소스 요소의 구조와 전체 설명은 [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md#resources)에서 확인할 수 있습니다.  일반적으로 정의하는 여러 리소스는 이 설명서의 다른 문서에 자세히 설명되어 있습니다. 


### <a name="dependencies"></a>종속성
**dependsOn** 요소는 다른 리소스에 대한 [종속성](../../azure-resource-manager/resource-group-define-dependencies.md)을 지정합니다.  솔루션이 설치될 때 모든 리소스의 종속성이 만들어진 후에야 리소스가 만들어지지 않습니다.  예를 들어 솔루션이 [작업 리소스](solutions-resources-automation.md#automation-jobs)를 사용하여 설치될 경우 솔루션에서 [Runbook을 시작](solutions-resources-automation.md#runbooks)할 수 있습니다.  작업이 만들어지기 전에 runbook이 만들어지도록 작업 리소스는 runbook 리소스에 종속됩니다.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정
관리 솔루션은 뷰를 포함하는 [Log Analytics 작업 영역](../../azure-monitor/platform/manage-access.md)과 Runbook 및 관련 리소스를 포함하는 [Automation 계정](../../automation/automation-security-overview.md#automation-account-overview)이 필요합니다.  이러한 항목은 솔루션의 리소스가 만들어지기 전에 제공되어야 하며 솔루션 자체에 정의될 수 없습니다.  사용자는 솔루션을 배포할 때 [작업 영역과 계정을 지정](solutions.md#log-analytics-workspace-and-automation-account)하지만, 작성자는 다음 사항을 고려해야 합니다.


## <a name="solution-resource"></a>솔루션 리소스
각 솔루션은 솔루션 자체를 정의하는 요소인 **resources** 요소에 리소스 항목이 필요합니다.  유형은 **Microsoft.OperationsManagement/solutions**이고 구조는 다음과 같습니다. 여기에는 일반적으로 솔루션의 속성을 정의하는 데 사용되는 [표준 매개 변수](#parameters) 및 [변수](#variables)가 포함됩니다.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>종속성
솔루션이 만들어지기 전에 솔루션 리소스가 있어야 하므로 솔루션 리소스는 솔루션의 모든 다른 리소스에 대해 [종속성](../../azure-resource-manager/resource-group-define-dependencies.md)을 가져야 합니다.  **dependsOn** 요소에서 각 리소스의 항목을 추가하면 됩니다.

### <a name="properties"></a>properties
솔루션 리소스는 테이블의 속성을 가집니다.  여기에는 솔루션 설치 후 리소스 관리 방식을 정의하는 솔루션에서 참조 및 포함하는 리소스가 포함됩니다.  솔루션의 각 리소스는 **referencedResources** 또는 **containedResources** 속성에 나열되어야 합니다.

| 자산 | 설명 |
|:--- |:--- |
| workspaceResourceId |폼에서 Log Analytics 작업 영역 ID  *\<리소스 그룹 ID > /providers/Microsoft.OperationalInsights/workspaces/\<작업 영역 이름\>* 합니다. |
| referencedResources |솔루션을 제거해도 함께 제거되면 안 되는 솔루션의 리소스 목록입니다. |
| containedResources |솔루션을 제거하면 함께 제거되어야 하는 솔루션의 리소스 목록입니다. |

위의 예제는 runbook, 일정, 보기가 포함된 솔루션과 관련됩니다.  일정 및 runbook은 **properties** 요소에서 *참조*되므로 솔루션이 제거될 때 제거되지 않습니다.  보기는 *포함*되어 있으므로 솔루션을 제거하면 함께 제거됩니다.

### <a name="plan"></a>계획
솔루션 리소스의 **plan** 엔터티는 테이블의 속성을 가집니다.

| 자산 | 설명 |
|:--- |:--- |
| name |솔루션의 이름입니다. |
| version |솔루션 버전은 작성자가 결정합니다. |
| product |솔루션을 식별하는 고유 문자열입니다. |
| publisher |솔루션의 게시자입니다. |



## <a name="next-steps"></a>다음 단계
* 관리 솔루션에 [저장된 검색 및 경고를 추가](solutions-resources-searches-alerts.md)합니다.
* 관리 솔루션에 대한 [보기를 추가](solutions-resources-views.md)합니다.
* 관리 솔루션에 [Runbook 및 기타 Automation 리소스를 추가](solutions-resources-automation.md)합니다.
* [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)에 대해 자세히 알아봅니다.
* [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates)에서 다양한 Resource Manager 템플릿 샘플을 검색합니다.
