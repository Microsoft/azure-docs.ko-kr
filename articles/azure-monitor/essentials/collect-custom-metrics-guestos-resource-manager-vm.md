---
title: 템플릿을 사용하여 Azure Monitor에서 Windows VM 메트릭 수집
description: Windows 가상 머신에 대해 Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 데이터베이스 저장소에 게스트 OS 메트릭 보내기
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.openlocfilehash: c0e8ae9e642caad0486b862b48d94ba392256a45
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839494"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>Windows 가상 머신에 대해 Azure Resource Manager 템플릿을 사용하여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기
Azure Virtual Machine의 게스트 OS 성능 데이터는 다른 [플랫폼 메트릭](./monitor-azure-resource.md#monitoring-data)과 마찬가지로 자동으로 수집되지 않습니다. Azure Monitor [진단 확장](../agents/diagnostics-extension-overview.md)을 설치하여 게스트 OS 메트릭을 메트릭 데이터베이스로 수집합니다. 이러한 메트릭은 근실시간 경고, 차트, 라우팅 및 REST API에서의 액세스를 비롯한 Azure Monitor 메트릭의 모든 기능에서 사용할 수 있습니다. 이 문서에서는 Resource Manager 템플릿을 사용하여 Windows 가상 머신에 대한 게스트 OS 성능 메트릭을 메트릭 데이터베이스로 보내는 프로세스에 대해 설명합니다.

> [!NOTE]
> Azure Portal을 사용하여 게스트 OS 메트릭을 수집하도록 진단 확장을 구성하는 방법에 대한 자세한 내용은 [WAD(Windows Azure 진단) 확장 설치 및 구성](../agents/diagnostics-extension-windows-install.md)을 참조하세요.


Resource Manager 템플릿을 처음 사용하는 경우 [템플릿 배포](../../azure-resource-manager/management/overview.md)와 해당 구조 및 구문에 대해 알아보세요.

## <a name="prerequisites"></a>사전 요구 사항

- 구독이 [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md)에 등록되어야 합니다.

- [Azure PowerShell](/powershell/azure) 또는 [Azure Cloud Shell](../../cloud-shell/overview.md)이 설치되어 있어야 합니다.

- VM 리소스는 [사용자 지정 메트릭을 지원하는 지역](./metrics-custom-overview.md#supported-regions)에 있어야 합니다.


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Azure Monitor를 데이터 싱크로 설정
Azure Diagnostics 확장은 "데이터 싱크"라는 기능을 사용하여 메트릭과 로그를 다른 위치로 라우팅합니다. 다음 단계에서는 Resource Manager 템플릿과 PowerShell을 사용하여 새 "Azure Monitor" 데이터 싱크를 통해 VM을 배포하는 방법을 보여줍니다.

## <a name="author-resource-manager-template"></a>Resource Manager 템플릿 작성
이 예제에서는 공개적으로 사용할 수 있는 템플릿 샘플을 사용할 수 있습니다. 시작 템플릿은 https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-simple-windows 에 있습니다.

- **Azuredeploy.json** 은 가상 머신을 배포하도록 미리 구성된 Resource Manager 템플릿입니다.

- **Azuredeploy.parameters.json** 은 VM에 대해 설정하려는 사용자 이름 및 암호와 같은 정보를 저장하는 매개 변수 파일입니다. 배포하는 동안 Resource Manager 템플릿에서는 이 파일에 설정된 매개 변수를 사용합니다.

두 파일을 다운로드하고 로컬로 저장합니다.

### <a name="modify-azuredeployparametersjson"></a>azuredeploy.parameters.json을 수정합니다.
*azuredeploy.parameters.json* 파일을 엽니다.

1. VM에 대해 **adminUsername** 및 **adminPassword** 에 대한 값을 입력합니다. 이러한 매개 변수는 VM에 원격으로 액세스하는 데 사용됩니다. VM이 하이재킹되지 않도록 방지하려면 이 템플릿에서 해당 값을 사용하지 마세요. 봇은 공용 GitHub 리포지토리에서 사용자 이름 및 암호를 인터넷으로 검색합니다. 이러한 기본값을 사용하여 VM을 테스트할 수 있습니다.

1. VM에 대한 고유한 dnsname을 만듭니다.

### <a name="modify-azuredeployjson"></a>Azuredeploy.json을 수정합니다.

*azuredeploy.json* 파일을 엽니다.

템플릿의 **variables** 섹션에 있는 **storageAccountName** 항목 뒤에 스토리지 계정 ID를 추가합니다.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

템플릿의 **resources** 섹션 위쪽에 MSI(관리 서비스 ID) 확장을 추가합니다. 이 확장을 통해 Azure Monitor에서 내보내는 메트릭을 허용합니다.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Azure에서 MSI 확장에 시스템 ID를 할당하도록 VM 리소스에 **identity** 구성을 추가합니다. 이 단계는 VM에서 자체에 대한 게스트 메트릭을 Azure Monitor로 내보낼 수 있도록 합니다.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

다음 구성을 추가하여 Windows 가상 머신에서 진단 확장을 사용하도록 설정합니다. 간단한 Resource Manager 기반 가상 머신의 경우 가상 머신에 대한 리소스 배열에 확장 구성을 추가할 수 있습니다. 줄 "sinks"&mdash;"AzMonSink" 및 섹션 뒷부분에 나오는 해당 "SinksConfig"&mdash;를 사용하면 확장에서 Azure Monitor에 메트릭을 직접 내보낼 수 있습니다. 필요에 따라 성능 카운터를 자유롭게 추가 또는 제거합니다.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


두 파일을 모두 저장하고 닫습니다.


## <a name="deploy-the-resource-manager-template"></a>Resource Manager 템플릿 배포

> [!NOTE]
> Azure Diagnostics 확장 버전 1.5 이상을 실행하고 Resource Manager 템플릿에서 **autoUpgradeMinorVersion**: 속성을 ‘true’로 설정해야 합니다. 그러면 Azure에서 VM을 시작할 때 적절한 확장을 로드합니다. 템플릿에 이러한 설정이 없는 경우 해당 설정을 변경하고 템플릿을 다시 배포합니다.


Resource Manager 템플릿을 배포하기 위해 Azure PowerShell을 활용합니다.

1. PowerShell을 시작합니다.
1. `Login-AzAccount`를 사용하여 Azure에 로그인합니다.
1. `Get-AzSubscription`을 사용하여 구독 목록을 가져옵니다.
1. 다음에 가상 머신을 만들고/업데이트하는 데 사용하는 구독을 설정합니다.

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. 배포되는 VM에 대한 새 리소스 그룹을 만들려면 다음 명령을 실행합니다.

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > [사용자 지정 메트릭을 사용하도록 설정된 Azure 지역을 사용](./metrics-custom-overview.md)해야 합니다.

1. Resource Manager 템플릿을 사용하여 VM을 배포하려면 다음 명령을 실행합니다.
   > [!NOTE]
   > 기존 VM을 업데이트하려면 다음 명령의 끝에 *-Mode Incremental* 을 추가하기만 하면 됩니다.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. 배포에 성공한 후 VM은 Azure Monitor에 메트릭을 내보내는 Azure Portal에 있어야 합니다.

   > [!NOTE]
   > 선택한 vmSkuSize 관련 오류가 발생할 수 있습니다. 그렇다면 azuredeploy.json 파일로 돌아가서 vmSkuSize 매개 변수의 기본값을 업데이트하세요. 이 경우에 "Standard_DS1_v2"를 사용하는 것이 좋습니다.

## <a name="chart-your-metrics"></a>메트릭 차트 작성

1. Azure 포털에 로그인합니다.

2. 왼쪽 메뉴에서 **모니터** 를 선택합니다.

3. 모니터 페이지에서 **메트릭** 을 선택합니다.

   ![메트릭 페이지](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. 집계 기간을 **지난 30분** 으로 변경합니다.

5. 리소스 드롭다운 메뉴에서 만든 VM을 선택합니다. 템플릿의 이름을 변경하지 않은 경우 *SimpleWinVM2* 여야 합니다.

6. 네임스페이스 드롭다운 메뉴에서 **azure.vm.windows.guest** 를 선택합니다.

7. 메트릭 드롭다운 메뉴에서 **메모리\%사용 중인 커밋된 바이트** 를 선택합니다.


## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](./metrics-custom-overview.md)에 대해 자세히 알아보세요.