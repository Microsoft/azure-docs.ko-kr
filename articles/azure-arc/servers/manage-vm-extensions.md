---
title: Azure Arc 사용 서버를 사용 하 여 VM 확장 관리 (미리 보기)
description: Azure Arc 사용 서버 (미리 보기)는 Azure Vm이 아닌 Vm을 사용 하 여 배포 후 구성 및 자동화 작업을 제공 하는 가상 머신 확장의 배포를 관리할 수 있습니다.
ms.date: 06/17/2020
ms.topic: conceptual
ms.openlocfilehash: 259a55c0e5cb4074871fd70a61e6c61d5cf3c50a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018164"
---
# <a name="virtual-machine-extension-management-with-azure-arc-enabled-servers-preview"></a>Azure Arc 사용 서버를 사용 하 여 가상 머신 확장 관리 (미리 보기)

VM (가상 컴퓨터) 확장은 Azure Vm에서 배포 후 구성 및 자동화 작업을 제공 하는 작은 응용 프로그램입니다. 예를 들어 가상 컴퓨터에 소프트웨어 설치, 바이러스 백신 보호가 필요한 경우, 또는 가상 컴퓨터 내부에서 스크립트를 실행하려면 VM 확장을 사용하면 됩니다.

Azure Arc 사용 서버 (미리 보기)를 사용 하면 azure VM 확장을 비 Azure Windows 및 Linux Vm에 배포 하 여 해당 수명 주기를 통해 온-프레미스, edge 및 기타 클라우드 환경에서 하이브리드 컴퓨터를 관리 하는 기능을 간소화할 수 있습니다.

## <a name="key-benefits"></a>주요 이점

Azure Arc 사용 서버 (미리 보기) VM 확장 지원은 다음과 같은 주요 이점을 제공 합니다.

* [Azure Automation 상태 구성을](../../automation/automation-dsc-overview.md) 사용 하 여 중앙에서 구성을 저장 하 고 DSC VM 확장을 통해 사용 하도록 설정 된 하이브리드 연결 컴퓨터의 원하는 상태를 유지 관리 합니다.

* Log Analytics 에이전트 VM 확장을 통해 사용 하도록 설정 된 [Azure Monitor의 로그](../../azure-monitor/platform/data-platform-logs.md) 를 분석 하기 위해 로그 데이터를 수집 합니다. 이는 다양 한 소스의 데이터에서 복잡 한 분석을 수행 하는 데 유용 합니다.

* [VM용 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)를 사용 하 여 Windows 및 Linux vm의 성능을 분석 하 고 다른 리소스 및 외부 프로세스에 대 한 프로세스 및 종속성을 모니터링 합니다. 이는 Log Analytics 에이전트와 종속성 에이전트 VM 확장을 모두 사용 하도록 설정 하 여 수행 됩니다.

* 사용자 지정 스크립트 확장을 사용 하 여 하이브리드 연결 컴퓨터에서 스크립트를 다운로드 하 여 실행 합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성 또는 관리 작업에 유용합니다.

## <a name="availability"></a>가용성

VM 확장 기능은 지원 되는 [지역](overview.md#supported-regions)목록 에서만 사용할 수 있습니다. 이러한 지역 중 하나에서 컴퓨터를 등록 했는지 확인 합니다.

## <a name="extensions"></a>확장

이 미리 보기에서는 Windows 및 Linux 컴퓨터에서 다음 VM 확장을 지원 합니다.

|확장 |OS |Publisher |추가 정보 |
|----------|---|----------|-----------------------|
|CustomScriptExtension |Windows |Microsoft.Compute |[Windows 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-windows.md)|
|DSC |Windows |Microsoft. PowerShell|[Windows PowerShell DSC 확장](../../virtual-machines/extensions/dsc-windows.md)|
|Log Analytics 에이전트 |Windows |Microsoft.EnterpriseCloud.Monitoring |[Windows 용 Log Analytics VM 확장](../../virtual-machines/extensions/oms-windows.md)|
|Microsoft 종속성 에이전트 | Windows |Microsoft.Compute | [Windows 용 종속성 에이전트 가상 컴퓨터 확장](../../virtual-machines/extensions/agent-dependency-windows.md)|
|CustomScript|Linux |Microsoft. Azure 확장명 |[Linux 사용자 지정 스크립트 확장 버전 2](../../virtual-machines/extensions/custom-script-linux.md) |
|DSC |Linux |Microsoft.OSTCExtensions |[Linux 용 PowerShell DSC 확장](../../virtual-machines/extensions/dsc-linux.md) |
|Log Analytics 에이전트 |Linux |Microsoft.EnterpriseCloud.Monitoring |[Linux 용 Log Analytics VM 확장](../../virtual-machines/extensions/oms-linux.md) |
|Microsoft 종속성 에이전트 | Linux |Microsoft.Compute | [Linux 용 종속성 에이전트 가상 머신 확장](../../virtual-machines/extensions/agent-dependency-linux.md) |

VM 확장은 Azure Resource Manager 템플릿, Azure Portal 또는 Arc 사용 서버에서 관리 하는 하이브리드 서버 (미리 보기)에서 Azure PowerShell 실행할 수 있습니다.

Azure 연결 된 컴퓨터 에이전트 패키지 및 확장 에이전트 구성 요소에 대 한 자세한 내용은 [에이전트 개요](agent-overview.md#agent-component-details)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 기능은 구독에 있는 다음 Azure 리소스 공급자에 따라 달라 집니다.

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

아직 등록 하지 않은 경우 [Azure 리소스 공급자 등록](agent-overview.md#register-azure-resource-providers)의 단계를 따릅니다.

Linux 용 Log Analytics 에이전트 VM 확장에는 대상 컴퓨터에 Python 2.x가 설치 되어 있어야 합니다.

### <a name="connected-machine-agent"></a>Connected Machine 에이전트

컴퓨터가 Azure 연결 된 컴퓨터 에이전트에 대해 [지원 되는 버전](agent-overview.md#supported-operating-systems) 의 Windows 및 Linux 운영 체제와 일치 하는지 확인 합니다.

이 기능으로 지원 되는 연결 된 컴퓨터 에이전트의 최소 버전은 다음과 같습니다.

* Windows-이상
* Linux-0.8. x

필요한 에이전트 버전으로 컴퓨터를 업그레이드 하려면 [에이전트 업그레이드](manage-agent.md#upgrading-agent)를 참조 하세요.

## <a name="enable-extensions-from-the-portal"></a>포털에서 확장 사용

Azure Portal를 통해 서버 (미리 보기) 관리 컴퓨터에 대 한 Arc를 VM 확장에 적용할 수 있습니다.

1. 브라우저에서 [Azure Portal](https://aka.ms/arcserver-preview)로 이동합니다.

2. 포털에서 **컴퓨터-Azure Arc** 로 이동 하 고 목록에서 하이브리드 컴퓨터를 선택 합니다.

3. **확장**을 선택한 다음 **추가**를 선택 합니다. 사용 가능한 확장 목록에서 원하는 확장을 선택하고 마법사의 지시를 따릅니다. 이 예제에서는 Log Analytics VM 확장을 배포 합니다. 

    ![선택한 컴퓨터에 대 한 VM 확장 선택](./media/manage-vm-extensions/add-vm-extensions.png)

    다음 예제에서는 Azure Portal에서 Log Analytics VM 확장을 설치 하는 방법을 보여 줍니다.

    ![Log Analytics VM 확장 설치](./media/manage-vm-extensions/mma-extension-config.png)

    설치를 완료 하려면 작업 영역 ID 및 기본 키를 제공 해야 합니다. 이 정보를 찾는 방법에 익숙하지 않은 경우 [작업 영역 ID 및 키 가져오기](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)를 참조 하세요.

4. 제공 된 필수 정보를 확인 한 후 **만들기**를 선택 합니다. 배포에 대 한 요약이 표시 되 고 배포 상태를 검토할 수 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리 하 고 처리할 수 있지만 직렬로 설치 됩니다. 첫 번째 확장 설치가 완료 되 면 다음 확장을 설치 하려고 시도 합니다.

## <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure Resource Manager 템플릿에 VM 확장을 추가하고 템플릿 배포를 통해 실행할 수 있습니다. Arc 사용 서버 (미리 보기)에서 지 원하는 VM 확장을 사용 하면 Azure PowerShell를 사용 하 여 Linux 또는 Windows 컴퓨터에서 지원 되는 VM 확장을 배포할 수 있습니다. 아래 각 샘플에는 템플릿에 제공할 샘플 값이 포함 된 템플릿 파일 및 매개 변수 파일이 포함 되어 있습니다.

>[!NOTE]
>여러 확장을 함께 일괄 처리 하 고 처리할 수 있지만 직렬로 설치 됩니다. 첫 번째 확장 설치가 완료 되 면 다음 확장을 설치 하려고 시도 합니다.

### <a name="deploy-the-log-analytics-vm-extension"></a>Log Analytics VM 확장 배포

Log Analytics 에이전트를 쉽게 배포 하려면 Windows 또는 Linux에 에이전트를 설치 하기 위한 다음 샘플이 제공 됩니다.

#### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>매개 변수 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

템플릿과 매개 변수 파일을 디스크에 저장 하 고 배포에 적절 한 값을 사용 하 여 매개 변수 파일을 편집 합니다. 그런 다음, 다음 명령을 사용 하 여 리소스 그룹 내에 있는 모든 연결 된 컴퓨터에 확장을 설치할 수 있습니다. 이 명령은 템플릿 *파일* 매개 변수를 사용 하 여 템플릿을 지정 하 고, 템플릿 *parameterfile* 매개 변수를 사용 하 여 매개 변수 및 매개 변수 값을 포함 하는 파일을 지정 합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

### <a name="deploy-the-custom-script-extension"></a>사용자 지정 스크립트 확장 배포

사용자 지정 스크립트 확장을 사용 하기 위해 Windows 및 Linux에서 실행 하기 위해 다음 샘플이 제공 됩니다. 사용자 지정 스크립트 확장에 익숙하지 않은 경우 [Windows 용 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-windows.md) 또는 [Linux 용 사용자 지정 스크립트 확장](../../virtual-machines/extensions/custom-script-linux.md)을 참조 하세요. 하이브리드 컴퓨터에서이 확장을 사용 하는 경우 다음과 같은 몇 가지 특성을 이해 해야 합니다.

* Azure VM 사용자 지정 스크립트 확장을 사용 하는 지원 되는 운영 체제 목록은 Azure Arc 사용 서버에 적용 되지 않습니다. Arc 사용 서버에 대해 지원 되는 OSs 목록은 [여기](agent-overview.md#supported-operating-systems)에서 찾을 수 있습니다.

* Azure Virtual Machine Scale Sets 또는 클래식 Vm에 대 한 구성 세부 정보는 적용 되지 않습니다.

* 컴퓨터에서 외부 스크립트를 다운로드 해야 하 고 프록시 서버를 통해서만 통신할 수 있는 경우 [연결 된 컴퓨터 에이전트를 구성](manage-agent.md#update-or-remove-proxy-settings) 하 여 프록시 서버 환경 변수를 설정 해야 합니다.

사용자 지정 스크립트 확장 구성은 스크립트 위치 및 실행할 명령 등을 지정합니다. 이 구성은 Linux 및 Windows 하이브리드 컴퓨터에 대해 아래에 제공 된 Azure Resource Manager 템플릿에 지정 됩니다.

#### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

#### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

### <a name="deploy-the-powershell-dsc-extension"></a>PowerShell DSC 확장 배포

PowerShell DSC 확장을 사용 하기 위해 Windows 및 Linux에서 실행 하기 위해 다음 샘플을 제공 합니다. PowerShell DSC 확장에 익숙하지 않은 경우 [DSC 확장 처리기 개요](../../virtual-machines/extensions/dsc-overview.md)를 참조 하세요. 하이브리드 컴퓨터에서이 확장을 사용 하는 경우 다음과 같은 몇 가지 특성을 이해 해야 합니다.

* Azure VM PowerShell DSC 확장을 사용 하는 지원 되는 운영 체제 목록은 Azure Arc 사용 서버에 적용 되지 않습니다. Arc 사용 서버에 대해 지원 되는 OSs 목록은 [여기](agent-overview.md#supported-operating-systems)에서 찾을 수 있습니다.

* 컴퓨터에서 외부 스크립트를 다운로드 해야 하 고 프록시 서버를 통해서만 통신할 수 있는 경우 [연결 된 컴퓨터 에이전트를 구성](manage-agent.md#update-or-remove-proxy-settings) 하 여 프록시 서버 환경 변수를 설정 해야 합니다.

#### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

#### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

#### <a name="parameter-file"></a>매개 변수 파일

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

### <a name="dependency-agent"></a>종속성 에이전트

Azure Monitor 종속성 에이전트 확장을 사용 하려면 Windows 및 Linux에서 실행할 수 있도록 다음 샘플을 제공 합니다. 종속성 에이전트에 익숙하지 않은 경우 [Azure Monitor 에이전트 개요](../../azure-monitor/platform/agents-overview.md#dependency-agent)를 참조 하세요.

#### <a name="template-file-for-linux"></a>Linux 용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

#### <a name="template-file-for-windows"></a>Windows 용 템플릿 파일

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="troubleshooting"></a>문제 해결

확장 배포 상태에 대 한 데이터는 Azure Portal에서 검색할 수 있습니다.

다음 문제 해결 단계는 모든 VM 확장에 적용됩니다.

1. 게스트 에이전트 로그를 확인 하려면 Windows 용으로 확장이 프로 비전 되었을 때의 작업 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` 및의 Linux에 대 한 작업을 확인 하세요 `/var/lib/GuestConfig/ext_mgr_logs` .

2. Windows에 대 한 자세한 내용은 특정 확장에 대 한 확장 로그를 확인 합니다 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . 확장 출력은에서 Linux에 설치 된 각 확장에 대 한 파일에 기록 됩니다 `/var/log/GuestConfig/extension_logs` .

3. 확장 관련 설명서 문제 해결 섹션에서 오류 코드, 알려진 문제 등을 확인 하세요. 각 확장에 대 한 추가 문제 해결 정보는 확장에 대 한 개요의 **문제 해결 및 지원** 섹션에서 찾을 수 있습니다. 여기에는 로그에 기록 된 오류 코드에 대 한 설명이 포함 됩니다. 확장 문서는이 문서 앞부분에 있는 [확장 테이블](#extensions) 에 연결 되어 있습니다.

4. 시스템 로그를 확인합니다. 단독 패키지 관리자 액세스가 필요한 다른 애플리케이션의 장기 실행 설치 등 확장을 방해할 수 있는 다른 작업을 확인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

- [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.