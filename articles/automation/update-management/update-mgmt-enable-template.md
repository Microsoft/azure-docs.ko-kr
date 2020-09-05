---
title: Azure Resource Manager 템플릿을 사용하여 업데이트 관리 사용 | Microsoft Docs
description: 이 문서에서는 Azure Resource Manager 템플릿을 사용하여 업데이트 관리를 사용하도록 설정하는 방법을 설명합니다.
ms.service: automation
ms.subservice: update-management
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 06/10/2020
ms.openlocfilehash: c4b29db8bbcb741116fcd425b4489973228066e6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021598"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 업데이트 관리 사용

[Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md)을 사용하여 리소스 그룹에서 Azure Automation 업데이트 관리 기능을 사용하도록 설정할 수 있습니다. 이 문서에서는 다음을 자동화하는 샘플 템플릿을 제공합니다.

* Azure Monitor Log Analytics 작업 영역 만들기
* Azure Automation 계정 만들기
* Log Analytics 작업 영역에 Automation 계정 연결(아직 연결되지 않은 경우)
* 업데이트 관리 사용

템플릿은 하나 이상의 Azure 또는 비 Azure Vm에서 업데이트 관리 사용 하도록 자동화 하지 않습니다.

구독에서 지원되는 지역에 배포된 Log Analytics 작업 영역 및 Automation 계정이 이미 있는 경우에는 연결되지 않습니다. 이 템플릿을 사용 하 여 성공적으로 링크를 만들고 업데이트 관리를 배포 합니다.

## <a name="api-versions"></a>API 버전

다음 표에는 이 템플릿에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 | 리소스 유형 | API 버전 |
|:---|:---|:---|
| 작업 영역 | workspaces | 2020-03-01-preview |
| Automation 계정 | Automation | 2018-06-30 |
| 해결 방법 | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>템플릿을 사용하기 전에

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell Az 모듈이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬에서 실행하는 경우 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.7.0)도 실행하여 Azure와의 연결을 만들어야 합니다. Azure PowerShell의 경우 배포는 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 문서에서는 Azure CLI 버전 2.1.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요. Azure CLI의 경우 이 배포는 [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)를 사용합니다. 

JSON 템플릿은 다음을 묻는 메시지를 표시하도록 구성됩니다.

* 작업 영역의 이름
* 작업 영역을 만들 지역입니다.
* 리소스 또는 작업 영역 권한을 사용 하도록 설정 합니다.
* Automation 계정의 이름
* 계정을 만들 지역입니다.

JSON 템플릿은 환경에서 표준 구성으로 사용될 수 있는 다른 매개 변수에 대한 기본값을 지정합니다. 조직에서 공유 액세스에 대한 Azure Storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [Azure Resource Manager 템플릿과 Azure CLI를 사용하여 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

템플릿의 다음 매개 변수는 Log Analytics 작업 영역의 기본값으로 설정됩니다.

* sku - 2018년 4월 가격 책정 모델에서 배포된 새로운 GB당 가격 책정 계층이 기본값
* 데이터 보존 - 기본값 30일

>[!WARNING]
>새 2018년 4월 가격 책정 모델을 선택한 구독에서 Log Analytics 작업 영역을 만들거나 구성할 때 유효한 유일한 Log Analytics 가격 책정 계층은 **PerGB2018**입니다.
>

JSON 템플릿은 환경에서 표준 구성으로 사용될 수 있는 다른 매개 변수에 대한 기본값을 지정합니다. 조직에서 공유 액세스에 대한 Azure Storage 계정에 템플릿을 저장할 수 있습니다. 템플릿 작업에 대한 자세한 내용은 [Azure Resource Manager 템플릿과 Azure CLI를 사용하여 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

Azure Automation 및 Azure Monitor를 처음 사용하는 경우 새 Automation 계정에 연결된 Log Analytics 작업 영역을 만들고, 구성하고, 사용할 때 오류가 발생하지 않도록 하려면 다음 구성 세부 정보를 이해해야 합니다.

* 액세스 제어 모드, 가격 책정 계층, 보존 및 용량 예약 수준과 같은 작업 영역 구성 옵션을 완전히 이해하려면 [추가 세부 정보](../../azure-monitor/platform/template-workspace-configuration.md#create-a-log-analytics-workspace)를 검토하세요.

* 특정 Azure 지역에서만 구독의 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있기 때문에 [작업 영역 매핑](../how-to/region-mappings.md)을 검토하여 지원되는 영역을 인라인 또는 매개 변수 파일로 지정합니다.

* Azure Monitor 로그를 처음 사용하는 사용자는 작업 영역을 배포하지 않은 경우 [작업 영역 디자인](../../azure-monitor/platform/design-logs-deployment.md) 지침을 검토하여 액세스 제어에 대해 알아보고 조직에 권장되는 디자인 구현 전략을 이해해야 합니다.

## <a name="deploy-template"></a>템플릿 배포

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "Workspace name"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "pergb2018",
                "Free",
                "Standalone",
                "PerNode",
                "Standard",
                "Premium"
            ],
            "defaultValue": "pergb2018",
            "metadata": {
                "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium) which are not available to all customers."
            }
        },
        "dataRetention": {
            "type": "int",
            "defaultValue": 30,
            "minValue": 7,
            "maxValue": 730,
            "metadata": {
                "description": "Number of days of retention. Workspaces in the legacy Free pricing tier can only have 7 days."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Specifies the location in which to create the workspace."
            }
        },
        "resourcePermissions": {
              "type": "bool",
              "metadata": {
                "description": "true to use resource or workspace permissions. false to require workspace permissions."
              }
        },
        "automationAccountName": {
            "type": "string",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "automationAccountLocation": {
            "type": "string",
            "metadata": {
                "description": "Specify the location in which to create the Automation account."
            }
        }
    },
    "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
        }
    },
    "resources": [
        {
        "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-03-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "name": "[parameters('sku')]"
                },
                "retentionInDays": "[parameters('dataRetention')]",
                "features": {
                    "searchVersion": 1,
                    "legacy": 0,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            },
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('location')]",
                    "name": "[variables('Updates').name]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                    },
                    "plan": {
                        "name": "[variables('Updates').name]",
                        "publisher": "Microsoft",
                        "promotionCode": "",
                        "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                    }
                }
            ]
        },
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2018-06-30",
            "name": "[parameters('automationAccountName')]",
            "location": "[parameters('automationAccountLocation')]",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "sku": {
                    "name": "Basic"
                }
            }
        },
        {
            "apiVersion": "2020-03-01-preview",
            "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
            "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]",
                "[concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            ],
            "properties": {
                "resourceId": "[resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))]"
            }
        }
      ]
    }
    ```

2. 요구 사항을 충족하도록 템플릿을 편집합니다. 매개 변수를 인라인 값으로 전달하는 대신 [Resource Manager 매개 변수 파일](../../azure-resource-manager/templates/parameter-files.md)을 만드는 것이 좋습니다.

3. 이 파일을 로컬 폴더에 **deployUMSolutiontemplate.json**으로 저장합니다.

4. 이제 이 템플릿을 배포할 수 있습니다. PowerShell 또는 Azure CLI 중 하나를 사용할 수 있습니다. 작업 영역 및 Automation 계정 이름을 묻는 메시지가 표시되면 모든 Azure 구독에서 전역적으로 고유한 이름을 입력합니다.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **Azure CLI**

    ```cli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사하게 결과가 포함된 메시지가 표시됩니다.

    ![배포가 완료되었을 때 결과 예](media/update-mgmt-enable-template/template-output.png)

## <a name="next-steps"></a>다음 단계

* Vm에 대 한 업데이트 관리를 사용 하려면 [vm에 대 한 업데이트 및 패치 관리](update-mgmt-manage-updates-for-vm.md)를 참조 하세요.

* 업데이트 관리을 더 이상 사용 하지 않고 제거 하려는 경우 [업데이트 관리 기능 제거](update-mgmt-remove-feature.md)의 지침을 참조 하세요.

* 업데이트 관리에서 VM을 삭제하려면 [업데이트 관리에서 VM 제거](update-mgmt-remove-vms.md)를 참조하세요.
