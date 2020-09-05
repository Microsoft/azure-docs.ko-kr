---
title: Azure Deployment Manager 상태 검사 사용
description: 상태 검사를 사용하여 Azure Deployment Manage를 통해 Azure 리소스를 안전하게 배포합니다.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3c7b74d31bc3c4e2276cd52c8e6450630dc99bcd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86058030"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>자습서: Azure Deployment Manager에서 상태 확인 사용(공개 미리 보기)

[Azure Deployment Manager](./deployment-manager-overview.md)에 상태 확인을 통합하는 방법을 알아봅니다. 이 자습서는 [Resource Manager 템플릿에서 Azure Deployment Manager 사용](./deployment-manager-tutorial.md) 자습서를 기반으로 합니다. 해당 자습서를 완료한 후 이 자습서를 진행해야 합니다.

[Resource Manager 템플릿에서 Azure Deployment Manager 사용](./deployment-manager-tutorial.md) 자습서에 사용된 롤아웃 템플릿에서는 대기 단계를 사용했습니다. 이 자습서에서는 대기 단계를 상태 확인 단계로 바꿉니다.

> [!IMPORTANT]
> 구독이 새 Azure 기능을 테스트하기 위한 카나리아로 표시된 경우 Azure Deployment Manager를 사용하여 카나리아 지역에 배포하는 것만 가능합니다. 

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 상태 확인 서비스 시뮬레이터 만들기
> * 롤아웃 템플릿 수정
> * 토폴로지 배포
> * 상태가 비정상인 롤아웃 배포
> * 롤아웃 배포 확인
> * 상태가 정상인 롤아웃 배포
> * 롤아웃 배포 확인
> * 리소스 정리

추가 리소스:

* [Azure Deployment Manager REST API 참조](/rest/api/deploymentmanager/).
* [Azure Deployment Manager 샘플](https://github.com/Azure-Samples/adm-quickstart).

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* [Azure 배포 관리자에서 Resource Manager 템플릿 사용](./deployment-manager-tutorial.md)을 완료합니다.

## <a name="install-the-artifacts"></a>아티팩트 설치

[템플릿 및 아티팩트](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip)를 다운로드하여 로컬로 압축을 풉니다(아직 수행되지 않은 경우). 그런 다음, [아티팩트 준비](./deployment-manager-tutorial.md#prepare-the-artifacts)에 있는 PowerShell 스크립트를 실행합니다. 이 스크립트는 리소스 그룹을 만들고, 스토리지 컨테이너를 만들고, Blob 컨테이너를 만들고, 다운로드한 파일을 업로드한 다음, SAS 토큰을 만듭니다.

SAS 토큰을 사용하여 URL의 복사본을 만듭니다. 이 URL은 두 매개 변수 파일(토폴로지 매개 변수 파일 및 롤아웃 매개 변수 파일)의 필드를 채우는 데 필요합니다.

CreateADMServiceTopology.Parameters.json을 열고 **projectName** 및 **artifactSourceSASLocation**의 값을 업데이트합니다.

CreateADMRollout.Parameters.json을 열고 **projectName** 및 **artifactSourceSASLocation**의 값을 업데이트합니다.

## <a name="create-a-health-check-service-simulator"></a>상태 확인 서비스 시뮬레이터 만들기

프로덕션 환경에서는 일반적으로 하나 이상의 모니터링 공급 기업을 사용합니다. 상태를 최대한 쉽게 통합할 수 있도록, Microsoft에서는 업계 최고 수준의 여러 서비스 상태 모니터링 회사와 협력하여 고객의 시스템에 상태 확인을 통합할 수 있는 간단한 복사/붙여넣기 솔루션을 제공해 왔습니다. 이러한 회사 목록은 [상태 모니터링 공급 기업](./deployment-manager-health-check.md#health-monitoring-providers)을 참조하세요. 이 자습서의 목적을 달성하기 위해, 상태 모니터링 서비스를 시뮬레이션하는 [Azure Function](../../azure-functions/index.yml)을 만듭니다. 이 함수는 상태 코드를 가져오고, 동일한 코드를 반환합니다. Azure Deployment Manager 템플릿은 상태 코드를 사용하여 배포 진행 방법을 결정합니다.

다음 두 파일은 Azure Function 배포에 사용됩니다. 자습서를 진행하기 위해 두 파일을 다운로드할 필요는 없습니다.

* [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)에 있는 Resource Manager 템플릿. Azure Function을 만들기 위해 이 템플릿을 배포합니다.
* Azure Function 소스 코드의 [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip) zip 파일. 이 zip 파일은 Resource Manager 템플릿에 의해 호출됩니다.

Azure 함수를 배포하려면 **사용해보기**를 선택하여 Azure Cloud Shell을 열고, 다음 스크립트를 셸 창에 붙여넣습니다.  코드를 붙여넣으려면 셸 창을 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Azure 함수를 확인하고 테스트하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 리소스 그룹을 엽니다.  기본 이름은 **rg**가 추가된 프로젝트 이름입니다.
1. 리소스 그룹에서 앱 서비스를 선택합니다.  앱 서비스의 기본 이름은 **webapp**이 추가된 프로젝트 이름입니다.
1. **함수**를 확장한 다음, **HttpTrigger1**을 선택합니다.

    ![Azure Deployment Manager 상태 확인 Azure Function](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. **&lt;/> Get function URL**을 선택합니다.
1. **복사**를 선택하여 URL을 클립보드에 복사합니다.  URL은 다음과 비슷합니다.

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    URL의 `{healthStatus}`를 상태 코드로 바꿉니다. 이 자습서에서는 **비정상**을 사용하여 비정상 시나리오를 테스트하고, **정상** 또는 **경고**를 사용하여 정상 시나리오를 테스트합니다. 비정상 상태인 URL과 정상 상태인 URL을 하나씩 만듭니다. 예:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    이 자습서를 완료하려면 두 URL이 모두 필요합니다.

1. 상태 모니터링 시뮬레이터를 테스트하려면 마지막 단계에서 만든 URL을 엽니다.  비정상 상태의 결과는 다음과 비슷합니다.

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>롤아웃 템플릿 수정

이 섹션의 목적은 롤아웃 템플릿에 상태 확인 단계를 포함하는 방법을 보여주는 것입니다.

1. [Resource Manager 템플릿에서 Azure Deployment Manager 사용](./deployment-manager-tutorial.md)에서 만든 **CreateADMRollout.json**을 엽니다. 이 JSON 파일은 다운로드한 파일에 포함되어 있습니다.  [필수 조건](#prerequisites)을 참조하세요.
1. 다음 두 매개 변수를 추가합니다.

    ```json
    "healthCheckUrl": {
      "type": "string",
      "metadata": {
        "description": "Specifies the health check URL."
      }
    },
    "healthCheckAuthAPIKey": {
      "type": "string",
      "metadata": {
          "description": "Specifies the health check Azure Function function authorization key."
      }
    }
    ```

1. 다음과 같이 대기 단계 리소스 정의를 상태 확인 단계 리소스 정의로 바꿉니다.

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    정의에 따라 상태가 *정상*이거나 *경고*이면 롤아웃이 진행됩니다.

1. 새로 정의한 상태 확인 단계를 포함하도록 롤아웃 정의의 **dependsON**을 업데이트합니다.

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. 상태 확인 단계를 포함하도록 **stepGroups**를 업데이트합니다. **healthCheckStep**은 **stepGroup2**의 **postDeploymentSteps**에서 호출됩니다. **stepGroup3** 및 **stepGroup4**는 상태가 *정상* 또는 *경고*인 경우에만 배포됩니다.

    ```json
    "stepGroups": [
      {
        "name": "stepGroup1",
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
          "postDeploymentSteps": []
        },
        {
          "name": "stepGroup2",
          "dependsOnStepGroups": ["stepGroup1"],
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
          "postDeploymentSteps": [
            {
              "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
            }
          ]
        },
        {
          "name": "stepGroup3",
          "dependsOnStepGroups": ["stepGroup2"],
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
           "postDeploymentSteps": []
        },
        {
          "name": "stepGroup4",
          "dependsOnStepGroups": ["stepGroup3"],
          "preDeploymentSteps": [],
          "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
          "postDeploymentSteps": []
        }
    ]
    ```

    **stepGroup3** 섹션의 변경 전과 변경 후를 비교해보면 이제 이 섹션에서는 **stepGroup2**를 사용합니다.  이는 **stepGroup3** 및 후속 단계 그룹에서 상태 모니터링 결과를 사용할 때 필요합니다.

    다음은 수정된 영역과 상태 확인 단계를 사용하는 방법을 보여주는 스크린샷입니다.

    ![Azure Deployment Manager 상태 확인 템플릿](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>토폴로지 배포

다음 PowerShell 스크립트를 실행하여 토폴로지를 배포합니다. [Resource Manager 템플릿에서 Azure Deployment Manager 사용](./deployment-manager-tutorial.md)에서 사용한 것과 동일한 **CreateADMServiceTopology.json** 및 **CreateADMServiceTopology.Parameters.json**이 필요합니다.

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Azure Portal을 사용하여 서비스 토폴로지와 밑줄 표시된 리소스가 성공적으로 만들어졌는지 확인합니다.

![Azure Deployment Manager 자습서 - 배포된 서비스 토폴로지 리소스](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

리소스를 보려면 **숨겨진 형식 표시**를 선택해야 합니다.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>상태가 비정상인 롤아웃 배포

[상태 확인 서비스 시뮬레이터 만들기](#create-a-health-check-service-simulator)에서 만든 비정상 상태 URL을 사용합니다. 수정된 **CreateADMServiceTopology.json** 및 [Resource Manager 템플릿에서 Azure Deployment Manager 사용](./deployment-manager-tutorial.md)에서 사용한 것과 동일한 **CreateADMServiceTopology.Parameters.json**이 필요합니다.

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`는 비동기 호출입니다. 성공 메시지는 성공적으로 배포가 시작되었다는 의미일 뿐입니다. 배포를 확인하려면 `Get-AZDeploymentManagerRollout`을 사용합니다.  다음 절차를 참조하세요.

롤아웃 진행률을 확인하려면 다음 PowerShell 스크립트를 사용합니다.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

다음 샘플 출력은 비정상 상태로 인해 실패한 배포를 보여줍니다.

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

롤아웃이 완료되면 미국 서부에 대해 생성된 추가 리소스 그룹 하나가 보일 것입니다.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>상태가 정상인 롤아웃 배포

이 섹션을 반복하여 정상 상태 URL로 롤아웃을 다시 배포합니다.  롤아웃이 완료되면 미국 동부에 대해 생성된 추가 리소스 그룹 하나가 보일 것입니다.

## <a name="verify-the-deployment"></a>배포 확인

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 롤아웃 배포에서 만든 새 리소스 그룹 아래에 새로 만들어진 웹 애플리케이션으로 이동합니다.
3. 웹 브라우저에서 웹 애플리케이션을 엽니다. index.html 파일에서 위치와 버전을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드를 사용하여 범위를 이 자습서에서 만든 리소스 그룹으로 좁힙니다. 다음과 같이 3-4개가 있습니다.

    * **&lt;projectName>rg**: Deployment Manager 리소스가 포함되어 있습니다.
    * **&lt;projectName>ServiceWUSrg**: ServiceWUS에서 정의한 리소스가 포함되어 있습니다.
    * **&lt;projectName>ServiceEUSrg**: ServiceEUS에서 정의한 리소스가 포함되어 있습니다.
    * 사용자 정의 관리 ID에 대한 리소스 그룹
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.
5. 마지막 두 단계를 반복하여 이 자습서에서 만든 다른 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Deployment Manager의 상태 확인 기능을 사용하는 방법을 알아보았습니다. 자세한 내용은 [Azure Resource Manager 설명서](../index.yml)를 참조하세요.
