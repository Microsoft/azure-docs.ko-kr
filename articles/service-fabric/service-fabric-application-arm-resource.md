---
title: Azure Resource Manager를 사용하여 애플리케이션과 서비스 배포 및 업그레이드 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터로 애플리케이션 및 서비스를 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e2e1b2ae354d26c3d9729e3a3fdf39bee43647ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621465"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>애플리케이션 및 서비스를 Azure Resource Manager 리소스로 관리

Azure Resource Manager를 통해 Service Fabric 클러스터에 애플리케이션 및 서비스를 배포할 수 있습니다. 즉, 클러스터가 준비될 때까지 기다린 후에 PowerShell 또는 CLI를 통해 애플리케이션을 배포 및 관리하는 대신, 이제 애플리케이션 및 서비스를 JSON으로 나타내고 클러스터와 동일한 Resource Manager 템플릿에 배포할 수 있습니다. 애플리케이션 등록, 프로비저닝 및 배포 프로세스는 모두 한 번에 수행됩니다.

이 방법은 클러스터에 필요한 설치, 거버넌스 또는 클러스터 관리 애플리케이션을 배포할 때 권장되는 방법입니다. 여기에는 [패치 오케스트레이션 애플리케이션](service-fabric-patch-orchestration-application.md), Watchdogs 또는 다른 서비스나 애플리케이션을 배포하기 위해 먼저 클러스터에서 실행 중이어야 하는 모든 애플리케이션이 포함됩니다. 

해당하는 경우 애플리케이션을 Resource Manager 리소스로 관리하여 다음을 향상시킬 수 있습니다.
* 감사 내역: 리소스 관리자는 모든 작업을 감사 하 고 자세한 유지 *활동 로그* 는 도움이 될 수 있습니다 이러한 응용 프로그램 및 클러스터에 대 한 변경 내용을 추적 합니다.
* 역할 기반 액세스 제어 (RBAC): 클러스터에 배포 하는 응용 프로그램 뿐만 아니라 클러스터에 대 한 액세스를 관리 하는 동일한 Resource Manager 템플릿을 통해 수행할 수 있습니다.
* Azure Resource Manager(Azure Portal을 통해)는 클러스터 및 중요한 애플리케이션 배포를 관리하기 위한 원스톱 상점이 됩니다.

다음 코드 조각에서는 한 템플릿을 통해 관리될 수 있는 여러 종류의 리소스를 보여 줍니다.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Resource Manager 템플릿에 새 애플리케이션 추가

1. 배포를 위해 클러스터의 Resource Manager 템플릿을 준비합니다. 이에 대한 자세한 내용은 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-via-arm.md)를 참조하세요.
2. 클러스터에 배포하려는 일부 애플리케이션을 고려해보세요. 다른 애플리케이션이 종속성을 가질 수 있는 애플리케이션이 항상 실행되고 있나요? 클러스터 거버넌스 또는 설치 애플리케이션을 배포할 계획인가요? 이러한 종류의 애플리케이션은 위에서 설명한 것처럼 Resource Manager 템플릿을 통해 가장 잘 관리됩니다. 
3. 이러한 방식으로 배포하려는 애플리케이션을 결정했으면 애플리케이션을 패키지하고, 압축하고, 파일 공유에 추가해야 합니다. Azure Resource Manager가 배포 중에 사용할 수 있도록 하려면 REST 엔드포인트를 통해 공유에 액세스할 수 있어야 합니다.
4. Resource Manager 템플릿에서 클러스터 선언 아래에는 각 애플리케이션의 속성이 설명되어 있습니다. 이러한 속성에는 복제본 또는 인스턴스 수와 리소스(다른 애플리케이션이나 서비스) 간의 모든 종속성 체인이 포함됩니다. 포괄적인 속성 목록을 보려면 [REST API Swagger 사양](https://aka.ms/sfrpswaggerspec)을 참조하세요. 이 목록은 애플리케이션 또는 서비스 매니페스트를 대신하지 않으며, 포함된 일부 항목을 클러스터의 Resource Manager 템플릿의 일부로 설명합니다. 다음은 상태 비저장 서비스 *Service1*과 상태 저장 서비스 *Service2*를 *Application1*의 일부로 배포하는 작업을 포함하는 샘플 템플릿입니다.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > *apiVersion*을 `"2017-07-01-preview"`로 설정해야 합니다. 클러스터가 이미 배포된 경우 이 템플릿을 클러스터와는 별도로 배포할 수도 있습니다.

5. 배포합니다. 

## <a name="manage-an-existing-application-via-resource-manager"></a>Resource Manager를 통해 기존 애플리케이션 관리

클러스터가 이미 작동 중이며 Resource Manager 리소스로 관리하려는 일부 애플리케이션이 이미 클러스터에 배포된 경우, 애플리케이션을 제거한 후 다시 배포하는 대신, 동일한 API를 통해 PUT 호출을 사용하여 애플리케이션이 Resource Manager 리소스로 승인되도록 할 수 있습니다. 

> [!NOTE]
> 고객 클러스터 업그레이드를 비정상 응용 프로그램을 무시할 수 있도록 지정할 수 있습니다 "maxPercentUnhealthyApplications: 100 "의" 업그레이드 설명 부트/healthPolicy "섹션에서 모든 설정에 대해 자세한 설명이 있는 [서비스 패브릭 REST API 클러스터 업그레이드 정책 설명서](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy)합니다.

## <a name="next-steps"></a>다음 단계

* [Service Fabric CLI](service-fabric-cli.md) 또는 [PowerShell](service-fabric-deploy-remove-applications.md)을 사용하여 클러스터에 다른 애플리케이션을 배포합니다. 
* [Service Fabric 클러스터 업그레이드](service-fabric-cluster-upgrade.md)

