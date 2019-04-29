---
title: 관리 솔루션의 Azure Automation 리소스 | Microsoft Docs
description: 관리 솔루션에는 일반적으로 모니터링 데이터를 수집하고 처리하는 등의 프로세스를 자동화하기 위한 Azure Automation의 runbook이 포함됩니다.  이 문서에서는 솔루션에 runbook과 관련 리소스를 포함하는 방법을 설명합니다.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c9b13f44dae068597cb82a0aa803283ad5e67bc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110364"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>관리 솔루션에 Azure Automation 리소스 추가(미리 보기)
> [!NOTE]
> 현재 Preview로 제공되는 관리 솔루션 만들기에 대한 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.   


[관리 솔루션]( solutions.md)에는 일반적으로 모니터링 데이터를 수집하고 처리하는 등의 프로세스를 자동화하기 위한 Azure Automation의 runbook이 포함됩니다.  runbook 외에도 Automation 계정에는 솔루션에서 사용되는 runbook을 지원하는 변수, 일정 등의 자산이 포함됩니다.  이 문서에서는 솔루션에 runbook과 관련 리소스를 포함하는 방법을 설명합니다.

> [!NOTE]
> 이 문서의 샘플에는 관리 솔루션에 필요하거나 공통적이며 [Azure의 관리 솔루션 디자인 및 빌드]( solutions-creating.md)에서 설명한 매개 변수와 변수가 사용 


## <a name="prerequisites"></a>필수 조건
이 문서에서는 이미 다음 정보에 대해 잘 알고 있다고 가정합니다.

- 방법: [관리 솔루션 만들기]( solutions-creating.md)
- 구조: [솔루션 파일]( solutions-solution-file.md)
- 방법: [Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Automation 계정
Azure Automation의 모든 리소스는 [Automation 계정](../../automation/automation-security-overview.md#automation-account-overview)에 포함됩니다.  [Log Analytics 작업 영역 및 Automation 계정]( solutions.md#log-analytics-workspace-and-automation-account)에서 설명한 대로 Automation 계정은 관리 솔루션에 포함되지 않지만, 솔루션이 설치되기 전에 존재해야 합니다.  계정을 사용할 수 없으면 솔루션 설치에 실패합니다.

각 Automation 리소스의 이름에는 해당 Automation 계정의 이름이 포함됩니다.  이 작업은 다음 runbook 리소스 예제와 같이 **accountName** 매개 변수가 포함된 솔루션에서 이루어집니다.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbook
솔루션에서 사용되는 모든 Runbook을 솔루션 파일에 포함해야 솔루션을 설치할 때 이러한 Runbook이 만들어집니다.  하지만 Runbook 본문은 템플릿에 포함할 수 없으므로 솔루션을 설치하는 모든 사용자가 액세스할 수 있는 공용 위치에 해당 Runbook을 게시해야 합니다.

[Azure Automation Runbook](../../automation/automation-runbook-types.md) 리소스의 형식은 **Microsoft.Automation/automationAccounts/runbooks**이며, 다음과 같은 구조를 가집니다. 여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Runbook의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| runbookType |runbook 유형을 지정합니다. <br><br> Script - PowerShell 스크립트 <br>PowerShell - PowerShell 워크플로 <br> GraphPowerShell - 그래픽 PowerShell 스크립트 runbook <br> GraphPowerShellWorkflow - 그래픽 PowerShell 워크플로 runbook |
| logProgress |runbook에 대한 [진행률 레코드](../../automation/automation-runbook-output-and-messages.md)를 생성해야 하는지 지정합니다. |
| logVerbose |runbook에 대한 [세부 정보 레코드](../../automation/automation-runbook-output-and-messages.md)를 생성해야 하는지 지정합니다. |
| description |runbook에 대한 선택적 설명입니다. |
| publishContentLink |runbook의 내용을 지정합니다. <br><br>uri - runbook 내용의 URL입니다.  이 URL은 PowerShell 및 Script runbook의 경우 .ps1 파일이 되고 그래프 runbook의 경우 내보낸 그래픽 runbook 파일이 됩니다.  <br> version - 자체적으로 추적하기 위한 runbook 버전입니다. |


## <a name="automation-jobs"></a>Automation 작업
Azure Automation에서 Runbook을 시작하면 자동화 작업이 만들어집니다.  솔루션에 자동화 작업 리소스를 추가하여 관리 솔루션이 설치될 때 자동으로 Runbook을 시작할 수 있습니다.  이 방법은 일반적으로 솔루션의 초기 구성에 사용되는 Runbook을 시작하는 데 사용됩니다.  일정한 간격으로 Runbook을 시작하려면 [일정](#schedules)과 [작업 일정](#job-schedules)을 만듭니다.

작업 리소스의 형식은 **Microsoft.Automation/automationAccounts/jobs**이며, 다음과 같은 구조를 가집니다.  여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

자동화 작업의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| runbook |시작할 Runbook의 이름을 포함하는 단일 이름 엔터티입니다. |
| parameters |Runbook에 필요한 각 매개 변수 값의 엔터티입니다. |

작업에는 runbook 이름과 runbook으로 전송되는 모든 매개 변수 값이 포함됩니다.  작업 전에 Runbook을 만들어야 하므로 작업은 시작하는 Runbook에 따라 [달라집니다]( solutions-solution-file.md#resources).  시작해야 하는 Runbook이 여러 개 있는 경우 작업을 먼저 실행해야 하는 다른 작업에 종속되게 하여 순서를 정의 할 수 있습니다.

작업 리소스의 이름에는 보통 매개 변수를 통해 할당되는 GUID가 포함되어야 합니다.  GUID 매개 변수에 대한 자세한 내용은 [Azure의 관리 솔루션 파일 만들기]( solutions-solution-file.md#parameters)를 참조하세요.  


## <a name="certificates"></a>인증서
[Azure Automation 인증서](../../automation/automation-certificates.md)의 형식은 **Microsoft.Automation/automationAccounts/certificates**이며, 다음과 같은 구조를 가집니다. 여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



인증서 리소스의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| base64Value |인증서에 대한 Base 64 값입니다. |
| thumbprint |인증서의 지문입니다. |



## <a name="credentials"></a>자격 증명
[Azure Automation 자격 증명](../../automation/automation-credentials.md)의 형식은 **Microsoft.Automation/automationAccounts/credentials**이며, 다음과 같은 구조를 가집니다.  여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

자격 증명 리소스의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| userName |인증서의 사용자 이름입니다. |
| password |인증서의 암호입니다. |


## <a name="schedules"></a>일정
[Azure Automation 일정](../../automation/automation-schedules.md)의 형식은 **Microsoft.Automation/automationAccounts/schedules**이며, 다음과 같은 구조를 가집니다. 여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

일정 리소스의 속성은 다음 테이블에 설명되어 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| description |일정에 대한 선택적 설명입니다. |
| startTime |일정 시작 시간을 DateTime 개체로 지정합니다. 적합한 DateTime으로 변환될 수 있는 경우 문자열을 제공할 수 있습니다. |
| isEnabled |일정 사용 여부를 지정합니다. |
| interval |일정 간격의 유형입니다.<br><br>일<br>시간 |
| frequency |특정 기간(일 또는 시간) 내에 일정이 실행되는 빈도입니다. |

일정에는 현재 시간보다 큰 값을 가진 시작 시간이 있어야 합니다.  변수를 설치할 시기를 알 수 없으므로 해당 변수에 이 값을 제공할 수 없습니다.

솔루션에서 일정 리소스를 사용하는 경우 다음 두 가지 전략 중 하나를 사용합니다.

- 일정의 시작 시간에 매개 변수를 사용합니다.  이렇게 하면 솔루션을 설치할 때 사용자에게 값을 제공하도록 요구하는 메시지가 표시됩니다.  일정이 여러 개 있는 경우 둘 이상의 일정에 단일 매개 변수 값을 사용할 수 있습니다.
- 솔루션을 설치할 때 시작되는 Runbook을 사용하여 일정을 만듭니다.  이렇게 하면 사용자가 시간을 지정하지 않아도 되지만 솔루션에 일정을 포함할 수 없으므로 솔루션을 제거할 때 일정도 제거됩니다.


### <a name="job-schedules"></a>작업 일정
작업 일정 리소스는 Runbook과 일정을 연결합니다.  작업 일정 리소스의 형식은 **Microsoft.Automation/automationAccounts/jobSchedules**이며, 다음과 같은 구조를 가집니다.  여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


작업 일정의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| schedule name |일정의 이름을 포함하는 단일 **이름** 엔터티입니다. |
| runbook name  |runbook 이름을 포함하는 단일 **name** 엔터티입니다.  |



## <a name="variables"></a>variables
[Azure Automation 변수](../../automation/automation-variables.md)의 형식은 **Microsoft.Automation/automationAccounts/variables**이며, 다음과 같은 구조를 가집니다.  여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

변수 리소스의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| description | 변수에 대한 선택적 설명입니다. |
| isEncrypted | 변수를 암호화해야 하는지 지정합니다. |
| type | 이 속성은 현재 적용되지 않습니다.  변수의 데이터 형식은 초기 값에 의해 결정됩니다. |
| 값 | 변수의 값입니다. |

> [!NOTE]
> **type** 속성은 현재 생성 중인 변수에 적용되지 않습니다.  변수의 데이터 형식은 해당 값에 의해 결정됩니다.  

변수에 대한 초기 값을 설정하는 경우 올바른 데이터 형식으로 구성해야 합니다.  다음 표에서는 허용 가능한 여러 데이터 형식과 해당 구문을 제공합니다.  JSON의 값에서는 항상 특수 문자를 인용 부호로 묶고 전체 값도 인용 부호로 묶어야 합니다.  예를 들어 문자열 값은 따옴표로 묶고(이스케이프 문자(\\) 사용), 숫자 값은 하나의 인용 부호 세트로 묶어야 합니다.

| 데이터 형식 | 설명 | 예 | 결과 값 |
|:--|:--|:--|:--|
| string   | 값을 큰따옴표로 묶습니다.  | "\"Hello world\"" | "Hello world" |
| numeric  | 작은따옴표가 있는 숫자 값| "64" | 64 |
| 부울  | 따옴표로 묶은 **true** 또는 **false**.  이 값은 소문자여야 합니다. | "true" | true |
| Datetime | 직렬화된 날짜 값.<br>PowerShell에서 ConvertTo-Json cmdlet을 사용하여 특정 날짜에 대해 이 값을 생성할 수 있습니다.<br>예: get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>모듈
관리 솔루션은 Automation 계정에서 항상 사용할 수 있으므로 Runbook에서 사용하는 [전역 모듈](../../automation/automation-integration-modules.md)을 정의할 필요가 없습니다.  Runbook에서 사용하는 다른 모듈에 대한 리소스를 포함해야 합니다.

[통합 모듈](../../automation/automation-integration-modules.md)의 형식은 **Microsoft.Automation/automationAccounts/modules**이며, 다음과 같은 구조를 가집니다.  여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


모듈 리소스의 속성은 다음 표에서 설명하고 있습니다.

| 자산 | 설명 |
|:--- |:--- |
| contentLink |모듈의 내용을 지정합니다. <br><br>uri - 모듈 내용에 대한 Uri입니다.  이 URL은 PowerShell 및 Script runbook의 경우 .ps1 파일이 되고 그래프 runbook의 경우 내보낸 그래픽 runbook 파일이 됩니다.  <br> version - 자체적으로 추적하기 위한 모듈의 버전입니다. |

모듈 리소스가 Runbook보다 먼저 만들어지도록 Runbook이 해당 리소스에 종속되어야 합니다.

### <a name="updating-modules"></a>모듈 업데이트
일정을 사용하는 runbook이 포함된 관리 솔루션을 업데이트하는데 해당 runbook에 사용되는 새 모듈이 솔루션의 새 버전에 포함된 경우 runbook은 이전 버전의 모듈을 사용할 수 있습니다.  솔루션에 다음 runbook을 포함하고 다른 runbook 전에 runbook을 실행할 작업을 만들어야 합니다.  이렇게 하면 runbook이 로드되기 전에 필요한 경우 모듈이 업데이트됩니다.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/)을 사용하면 솔루션에서 runbook에 사용된 모든 모듈이 최신 버전이 됩니다.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/)를 사용하면 일정 리소스에 연결된 runbook이 최신 모듈을 사용하도록 모든 일정 리소스가 등록됩니다.




## <a name="sample"></a>샘플
다음은 다음 리소스를 포함하는 솔루션의 샘플입니다.

- Runbook -  공용 GitHub 리포지토리에 저장된 샘플 Runbook입니다.
- Automation 작업 - 솔루션을 설치할 때 Runbook을 시작합니다.
- 일정 및 작업 일정 - 일정한 간격으로 Runbook을 시작합니다.
- 인증서
- 자격 증명
- 변수
- 모듈 -  Log Analytics에 데이터를 쓰는 [OMSIngestionAPI 모듈 ](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5)입니다. 

이 샘플에서는 리소스 정의의 값을 하드 코딩하는 대신 솔루션에 일반적으로 사용되는 [표준 솔루션 매개 변수]( solutions-solution-file.md#parameters) 변수를 사용합니다.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>다음 단계
* [솔루션에 보기를 추가]( solutions-resources-views.md)하여 수집된 데이터를 시각화합니다.
