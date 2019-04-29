---
title: 지역 간 안전한 배포 사례 - Azure 배포 관리자
description: Azure 배포 관리자를 사용하여 여러 지역에 서비스를 배포하는 방법을 설명합니다. 모든 지역에 롤아웃하기 전에 배포의 안정성을 확인하는 안전한 배포 사례를 보여 줍니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: a615ab26e4ea046ced70ce2c154a0c304b741986
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550907"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-private-preview"></a>Azure 배포 관리자(개인용 미리 보기)로 안전한 배포 사례 사용

여러 지역에서 서비스를 배포하고 각 지역에서 예상대로 실행되고 있는지 확인하려면 Azure 배포 관리자를 사용하여 서비스의 단계적 롤아웃을 조정할 수 있습니다. Azure 배포의 경우와 마찬가지로 [Resource Manager 템플릿](resource-group-authoring-templates.md)에서 서비스의 리소스를 정의합니다. 템플릿을 만든 후에는 배포 관리자를 사용하여 서비스의 토폴로지와 롤아웃 방식을 설명합니다.

배포 관리자는 Resource Manager의 기능입니다. 배포 중에 기능이 확장됩니다. 여러 영역에 배포해야 하는 복합 서비스가 있는 경우 배포 관리자를 사용합니다. 서비스의 출시를 준비하여 모든 지역에 배포되기 전에 잠재적인 문제를 찾을 수 있습니다. 단계적 롤아웃에 대해 추가 주의 사항이 필요하지 않은 경우 Resource Manager의 표준 [배포 옵션](resource-group-template-deploy-portal.md)을 사용합니다. 배포 관리자는 CI/CD(지속적인 통합 및 지속적인 업데이트) 제안과 같이 Resource Manager 배포를 지원하는 기존의 모든 타사 도구와 원활하게 통합됩니다. 

Azure Deployment Manager가 비공개 미리 보기로 제공됩니다. Azure 배포 관리자를 사용하려면 [등록 양식](https://aka.ms/admsignup)을 작성합니다. [사용자 의견](https://aka.ms/admfeedback)을 제공하여 기능을 개선하는 데 도움을 주세요.

배포 관리자를 사용하려면 다음 4개의 파일을 만들어야 합니다.

* 토폴로지 템플릿
* 롤아웃 템플릿
* 토폴로지용 매개 변수 파일
* 롤아웃용 매개 변수 파일

롤아웃 템플릿을 배포하기 전에 토폴로지 템플릿을 배포합니다.

Azure Deployment Manager REST API 참조는 [여기](https://docs.microsoft.com/rest/api/deploymentmanager/)서 찾을 수 있습니다.

## <a name="supported-locations"></a>지원되는 위치

미리 보기의 경우 배포 관리자 리소스는 미국 중부 및 미국 동부 2에서 지원됩니다. 토폴로지 및 롤아웃 템플릿에서 이 문서에 설명된 서비스 단위, 아티팩트 원본 및 롤아웃과 같은 리소스를 정의하는 경우 해당 위치에 대해 이러한 지역 중 하나를 지정해야 합니다. 그러나 가상 머신, 저장소 계정 및 웹앱과 같이 서비스를 만들기 위해 배포하는 리소스는 해당하는 모든 [표준 위치](https://azure.microsoft.com/global-infrastructure/services/?products=all)에서 지원됩니다.  

## <a name="identity-and-access"></a>ID 및 액세스

배포 관리자를 사용할 경우 [사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)가 배포 작업을 수행합니다. 배포를 시작하기 전에 이 ID를 만듭니다. 이 ID에는 서비스를 배포할 구독에 대한 액세스 권한이 있어야 하며 배포를 수행하기 위한 충분한 권한이 있어야 합니다. 역할을 통해 부여되는 작업에 대한 내용은 [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.

ID는 배포 관리자를 위해 지원되는 위치 중 하나에 있어야 하며 롤아웃과 동일한 위치에 있어야 합니다.

## <a name="topology-template"></a>토폴로지 템플릿

토폴로지 템플릿은 서비스를 구성하는 Azure 리소스와 이러한 리소스를 배포할 위치를 설명합니다. 다음 이미지는 예제 서비스의 토폴로지를 보여 줍니다.

![서비스 토폴로지에서 서비스 간 단위로의 계층 구조](./media/deployment-manager-overview/service-topology.png)

토폴로지 템플릿에는 다음 리소스가 포함됩니다.

* 아티팩트 원본 - Resource Manager 템플릿 및 매개 변수가 저장되는 위치입니다.
* 서비스 토폴로지 - 아티팩트 원본을 가리킵니다.
  * 서비스 - 위치 및 Azure 구독 ID를 지정합니다.
    * 서비스 단위 - 리소스 그룹, 배포 모드 및 템플릿과 매개 변수 파일의 경로를 지정합니다.

각 수준에서 수행되는 작업을 이해하기 위해서는 사용자가 제공하는 값을 확인하는 것이 좋습니다.

![각 수준의 값](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>템플릿용 아티팩트 원본

토폴로지 템플릿에서 템플릿 및 매개 변수 파일을 보유하는 아티팩트 원본을 만듭니다. 아티팩트 원본은 배포할 파일을 끌어오는 방법입니다. 이 문서 뒷부분에는 이진 파일을 위한 다른 아티팩트 원본이 설명됩니다.

다음 예제에서는 아티팩트 원본의 일반 형식을 보여 줍니다.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

자세한 내용은 [artifactSources 템플릿 참조](/azure/templates/Microsoft.DeploymentManager/artifactSources)를 참조하세요.

### <a name="service-topology"></a>서비스 토폴로지

다음 예제에서는 서비스 토폴로지 리소스의 일반 형식을 보여 줍니다. 템플릿 및 매개 변수 파일을 포함하는 아티팩트 원본의 리소스 ID를 제공합니다. 서비스 토폴로지에는 모든 서비스 리소스가 포함됩니다. 아티팩트 원본이 사용 가능하도록 하기 위해 서비스 토폴로지가 종속되어 있습니다.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

자세한 내용은 [serviceTopologies 템플릿 참조](/azure/templates/Microsoft.DeploymentManager/serviceTopologies)를 참조하세요.

### <a name="services"></a>Services

다음 예제에서는 서비스 리소스의 일반 형식을 보여 줍니다. 각 서비스에서 서비스 배포에 사용할 위치 및 Azure 구독 ID를 제공합니다. 여러 지역에 배포하려면 각 지역의 서비스를 정의합니다. 서비스는 서비스 토폴로지에 따라 다릅니다.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

자세한 내용은 [서비스 템플릿 참조](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)를 참조하세요.

### <a name="service-units"></a>서비스 단위

다음 예제에서는 서비스 단위 리소스의 일반 형식을 보여 줍니다. 각 서비스 단위에서 리소스 그룹, 배포에 사용할 [배포 모드](deployment-modes.md), 템플릿 및 매개 변수 파일의 경로를 지정합니다. 템플릿 및 매개 변수의 상대 경로를 지정하는 경우 전체 경로는 아티팩트 원본의 루트 폴더에서 구성됩니다. 템플릿 및 매개 변수의 절대 경로를 지정할 수 있지만 릴리스 버전 관리가 어려워질 수 있습니다. 서비스 단위는 서비스에 따라 다릅니다.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

각 템플릿에는 한 단계로 배포할 관련 리소스가 포함되어야 합니다. 예를 들어, 서비스 단위는 서비스의 프런트 엔드에 대한 모든 리소스를 배포하는 템플릿을 포함할 수 있습니다.

자세한 내용은 [serviceUnits 템플릿 참조](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)를 참조하세요.

## <a name="rollout-template"></a>롤아웃 템플릿

롤아웃 템플릿은 서비스를 배포할 때 수행하는 단계를 설명합니다. 사용할 서비스 토폴로지를 지정하고 서비스 단위 배포 순서를 정의합니다. 여기에는 배포용 이진 파일을 저장하기 위한 아티팩트 원본이 포함되어 있습니다. 롤아웃 템플릿에서 다음 계층 구조를 정의합니다.

* 아티팩트 원본
* 단계
* 롤아웃
  * 단계 그룹
    * 배포 작업

다음 이미지는 롤아웃 템플릿의 계층 구조를 보여 줍니다.

![롤아웃부터 단계까지의 계층 구조](./media/deployment-manager-overview/Rollout.png)

각 롤아웃은 여러 단계 그룹을 포함할 수 있습니다. 각 단계 그룹에는 서비스 토폴로지의 서비스 단위를 가리키는 하나의 배포 작업이 있습니다.

### <a name="artifact-source-for-binaries"></a>2진 파일용 아티팩트 원본

롤아웃 템플릿에서 서비스에 배포해야 하는 2진 파일용 아티팩트 원본을 만듭니다. 이 아티팩트 원본은 스크립트, 웹 페이지, 컴파일된 코드 또는 서비스에 필요한 기타 파일을 포함한다는 것을 제외하고 [템플릿용 아티팩트 원본](#artifact-source-for-templates)과 유사합니다.

### <a name="steps"></a>단계

배포 작업 전후에 수행할 단계를 정의할 수 있습니다. 현재, `wait` 단계만 사용할 수 있습니다. 대기 단계는 계속하기 전에 배포를 일시 중지합니다. 다음 서비스 단위를 배포하기 전에 서비스가 예상대로 실행되고 있는지 확인할 수 있습니다. 다음 예제에서는 대기 단계의 일반 형식을 보여 줍니다.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

지속 기간 속성은 [ISO 8601 표준](https://en.wikipedia.org/wiki/ISO_8601#Durations)을 사용합니다. 이전 예제는 1분 대기를 지정합니다.

자세한 내용은 [단계 템플릿 참조](/azure/templates/Microsoft.DeploymentManager/steps)를 참조하세요.

### <a name="rollouts"></a>출시

아티팩트 원본이 사용 가능하도록 하기 위해 롤아웃이 종속되어 있습니다. 롤아웃은 배포된 각 서비스 단위의 단계 그룹을 정의합니다. 배포 전후에 수행할 작업을 정의할 수 있습니다. 예를 들어, 서비스 단위가 배포된 후 배포 대기를 지정할 수 있습니다. 단계 그룹의 순서를 정의할 수 있습니다.

ID 개체는 배포 작업을 수행하는 [사용자가 할당한 관리 ID](#identity-and-access)입니다.

다음 예제에서는 롤아웃의 일반 형식을 보여 줍니다.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

자세한 내용은 [롤아웃 템플릿 참조](/azure/templates/Microsoft.DeploymentManager/rollouts)를 참조하세요.

## <a name="parameter-file"></a>매개 변수 파일 

두 개의 매개 변수 파일을 만듭니다. 매개 변수 파일 1개는 서비스 토폴로지를 배포할 때 사용되고, 나머지 1개는 롤아웃 배포에 사용됩니다. 두 매개 변수 파일에서 동일하게 유지해야 하는 몇 개의 값이 있습니다.  

## <a name="containerroot-variable"></a>containerRoot 변수

버전이 관리되는 배포를 사용할 경우 새 버전마다 아티팩트 경로가 변경됩니다. 배포를 처음 실행할 때 이 경로는 `https://<base-uri-blob-container>/binaries/1.0.0.0`일 수 있습니다. 두 번째 배포에서는 `https://<base-uri-blob-container>/binaries/1.0.0.1`일 수 있습니다. 배포 관리자는 `$containerRoot` 변수를 사용하여 현재 배포에 대한 올바른 루트 경로를 간편하게 가져옵니다. 이 값은 버전마다 달라지며 배포 전에는 알 수 없습니다.

Azure 리소스를 배포하려면 템플릿용 매개 변수 파일에서 `$containerRoot` 변수를 사용합니다. 배포 시, 이 변수는 롤아웃의 실제 값으로 바뀝니다. 

예를 들어, 롤아웃 중에 2진 아티팩트용 아티팩트 원본을 만듭니다.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

`artifactRoot` 및 `sasUri` 속성에 유의합니다. 아티팩트 루트를 `binaries/1.0.0.0`과 같은 값으로 설정할 수 있습니다. SAS URI는 액세스를 위한 SAS 토큰이 있는 저장소 컨테이너의 URI입니다. 배포 관리자는 `$containerRoot` 변수의 값을 자동으로 구성합니다. 해당 값을 `<container>/<artifactRoot>` 형식으로 조합합니다.

템플릿 및 매개 변수 파일은 버전이 관리되는 2진 파일을 가져오기 위해 올바른 경로를 알아야 합니다. 예를 들어, 웹앱용 파일을 배포하려면 $containerRoot 변수를 사용하여 다음 매개 변수 파일을 만듭니다. 첫 번째 백슬래시는 이스케이프 문자이므로 경로에 두 개의 백슬래시(`\\`)를 사용해야 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

그런 다음, 템플릿에서 해당 매개 변수를 사용합니다.

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

새 폴더를 만들고 롤아웃 동안 해당 루트를 전달하여 버전이 관리되는 배포를 관리합니다. 경로는 리소스를 배포하는 템플릿을 통과해서 지정됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 배포 관리자에 대해 알아보았습니다. 다음 문서에서 계속해서 배포 관리자를 통한 배포 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: Resource Manager 템플릿에서 Azure 배포 관리자 사용](./deployment-manager-tutorial.md)