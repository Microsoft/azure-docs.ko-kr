---
title: 환경에 Azure DevTest Labs에서 랩의 vnet에 연결 | Microsoft Docs
description: 'Azure DevTest Labs에서 랩의 가상 네트워크에서 environment (예: Service Fabric 클러스터)를 연결 하는 방법을 알아봅니다'
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: bb8b5f7d6578390fd0f48c3de154cfdb034ac6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60777171"
---
# <a name="connect-an-environment-to-your-labs-virtual-network-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩의 가상 네트워크에는 환경 연결
Azure DevTest Labs를 사용 하면 쉽게 사용 하 여 랩에서 Vm을 만들 [기본 제공 네트워킹](devtest-lab-configure-vnet.md)합니다. 상당한 수준의 유연성을 제공 하는 기능을 사용 하 여 있기 [다중 VM 환경을 만드는](devtest-lab-test-env.md)합니다. 이 문서는 랩의 가상 네트워크 환경에서 Vm을 연결 하는 방법을 보여 줍니다. 이 기능을 사용 하는 한 가지 시나리오는 랩 VNet 허용 테스트 Vm 랩 액세스에 연결 된 SQL Server 데이터 계층을 사용 하 여 N 계층 앱 설정 합니다.  

## <a name="sample-environment-that-uses-lab-vnet"></a>랩 VNet을 사용 하는 샘플 환경
랩의 서브넷에 연결 하는 단순한 환경 템플릿을 다음과 같습니다. 이 샘플에서는 `DTLSubnetId` 매개 변수는 랩이 존재 하는 서브넷의 ID를 나타냅니다. 에 할당 됩니다. `$(LabSubnetId)`, DevTest Labs 랩의 서브넷의 ID에 의해 자동으로 해결 되는 합니다. **서브넷** 의 속성을 **네트워크 인터페이스** 이 정의에서 VM 설정 되어 `DTLSubnetId` 동일한 서브넷에 가입할 수 있도록 합니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DTLEnvironVmStoretype": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_ZRS",
                "Standard_GRS",
                "Standard_RAGRS",
                "Premium_LRS"
            ]
        },
        "DTLEnvironVmName": {
            "type": "string",
            "minLength": 1
        },
        "VmAdminUserName": {
            "type": "string",
            "minLength": 1
        },
        "VmAdminUserPassword": {
            "type": "securestring"
        },
        "DTLEnvironVmOsVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1",
                "2012-Datacenter",
                "2012-R2-Datacenter",
                "Windows-Server-Technical-Preview"
            ]
        },
        "DTLSubnetId": {
            "type": "string",
            "defaultValue": "$(LabSubnetId)"
        }
    },
    "variables": {
        "DTLEnvironStoreName": "[toLower([concat(parameters('DTLEnvironVmName'), 'storename')])]",
        "DTLEnvironVmImagePublisher": "MicrosoftWindowsServer",
        "DTLEnvironVmImageOffer": "WindowsServer",
        "DTLEnvironVmOSDiskName": "[concat(parameters('DTLEnvironVmName'), 'OSDisk')]",
        "DTLEnvironVmSize": "Standard_D2_v2",
        "DTLEnvironVmStorageAccountContainerName": "vhds",
        "DTLEnvironVmNicName": "[concat(parameters('DTLEnvironVmName'), 'NetworkInterface')]"
    },
    "resources": [{
            "name": "[variables('DTLEnvironStoreName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[resourceGroup().location]",
            "apiVersion": "2016-01-01",
            "sku": {
                "name": "[parameters('DTLEnvironVmStoretype')]"
            },
            "dependsOn": [],
            "tags": {
                "displayName": "[variables('DTLEnvironStoreName')]"
            },
            "kind": "Storage"
        },
        {
            "name": "[variables('DTLEnvironVmNicName')]",
            "type": "Microsoft.Network/networkInterfaces",
            "location": "southeastasia",
            "apiVersion": "2016-03-30",
            "dependsOn": [],
            "tags": {
                "displayName": "[variables('DTLEnvironVmNicName')]"
            },
            "properties": {
                "ipConfigurations": [{
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "[parameters('DTLSubnetId')]"
                        }
                    }
                }]
            }
        },
        {
            "name": "[parameters('DTLEnvironVmName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "location": "[resourceGroup().location]",
            "apiVersion": "2015-06-15",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts', variables('DTLEnvironStoreName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces', variables('DTLEnvironVmNicName'))]"
            ],
            "tags": {
                "displayName": "[parameters('DTLEnvironVmName')]"
            },
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('DTLEnvironVmSize')]"
                },
                "osProfile": {
                    "computerName": "[parameters('DTLEnvironVmName')]",
                    "adminUsername": "[parameters('VmAdminUserName')]",
                    "adminPassword": "[parameters('VmAdminUserPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('DTLEnvironVmImagePublisher')]",
                        "offer": "[variables('DTLEnvironVmImageOffer')]",
                        "sku": "[parameters('DTLEnvironVmOsVersion')]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "name": "[variables('DTLEnvironVmOSDiskName')]",
                        "vhd": {
                            "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts', variables('DTLEnvironStoreName')), '2016-01-01').primaryEndpoints.blob, variables('DTLEnvironVmStorageAccountContainerName'), '/', variables('DTLEnvironVmOSDiskName'), '.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [{
                        "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('DTLEnvironVmNicName'))]"
                    }]
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="next-steps"></a>다음 단계
Azure portal을 사용 하 여 이러한 작업을 수행 하려면 다음 문서를 참조 하세요. [VM을 다시 시작](devtest-lab-restart-vm.md)합니다.