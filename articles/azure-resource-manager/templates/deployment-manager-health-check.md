---
title: 상태 통합 출시-Azure 배포 관리자
description: Azure 배포 관리자를 사용하여 여러 지역에 서비스를 배포하는 방법을 설명합니다. 모든 지역에 롤아웃하기 전에 배포의 안정성을 확인하는 안전한 배포 사례를 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484806"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Azure 배포 관리자에 상태 통합 출시 소개 (공개 미리 보기)

[Azure 배포 관리자](./deployment-manager-overview.md) 를 사용 하면 Azure Resource Manager 리소스의 단계적 롤아웃을 수행할 수 있습니다. 리소스는 지역에 따라 정렬 된 방식으로 배포 됩니다. Azure 배포 관리자의 통합 된 상태 검사를 통해 롤아웃을 모니터링 하 고 문제를 해결 하 고 문제를 해결할 수 있도록 문제를 자동으로 중지할 수 있습니다. 이 기능은 업데이트의 재발으로 인해 발생 하는 서비스를 사용할 수 없게 될 수 있습니다.

## <a name="health-monitoring-providers"></a>상태 모니터링 공급자

상태를 최대한 쉽게 통합할 수 있도록, Microsoft에서는 업계 최고 수준의 여러 서비스 상태 모니터링 회사와 협력하여 고객의 시스템에 상태 확인을 통합할 수 있는 간단한 복사/붙여넣기 솔루션을 제공해 왔습니다. 상태 모니터를 아직 사용 하지 않는 경우 다음을 시작 하는 데 적합 한 솔루션입니다.

| ![azure deployment manager 상태 모니터 공급자 datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure deployment manager 상태 모니터 공급자 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure deployment manager 상태 모니터 공급자 wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|최신 클라우드 환경을 위한 최고의 모니터링 및 분석 플랫폼인 Datadog. [Datadog가 Azure 배포 관리자와 어떻게 통합](https://www.datadoghq.com/azure-deployment-manager/)되는지 확인 합니다.|Site24x7 개인 및 공용 클라우드 서비스 모니터링 솔루션인. [Site24x7가 Azure 배포 관리자와 어떻게 통합](https://www.site24x7.com/azure/adm.html)되는지 확인 합니다.| Wavefront, 다중 클라우드 응용 프로그램 환경에 대 한 모니터링 및 분석 플랫폼입니다. [Wavefront가 Azure 배포 관리자와 어떻게 통합](https://go.wavefront.com/wavefront-adm/)되는지 확인 합니다.|

## <a name="how-service-health-is-determined"></a>서비스 상태를 결정 하는 방법

[상태 모니터링 공급자](#health-monitoring-providers) 는 서비스를 모니터링 하 고 서비스 상태 문제에 대 한 경고를 제공 하는 몇 가지 메커니즘을 제공 합니다. [Azure Monitor](../../azure-monitor/overview.md) 은 이러한 제품의 한 예입니다. Azure Monitor를 사용 하 여 특정 임계값을 초과할 때 경고를 만들 수 있습니다. 예를 들어 서비스에 새 업데이트를 배포할 때 메모리와 CPU 사용률이 예상 수준 보다 크게 증가 합니다. 알림이 표시 되 면 정정 작업을 수행할 수 있습니다.

일반적으로 이러한 상태 공급자는 서비스 모니터의 상태를 프로그래밍 방식으로 검사할 수 있도록 REST Api를 제공 합니다. REST Api는 간단한 정상/비정상 신호 (HTTP 응답 코드에 의해 결정 됨)를 사용 하거나 수신 하는 신호에 대 한 자세한 정보를 사용 하 여 다시 돌아올 수 있습니다.

Azure 배포 관리자의 새로운 *healthCheck* 단계를 사용 하면 정상 서비스를 나타내는 HTTP 코드를 선언할 수 있습니다. 또는 더 복잡 한 REST 결과의 경우 정규식을 지정 하 여 일치 하는 경우 정상적인 응답을 나타낼 수도 있습니다.

Azure 배포 관리자 상태 검사를 사용 하 여 설치를 가져오는 흐름:

1. 선택한 health service 공급자를 통해 상태 모니터를 만듭니다.
1. Azure 배포 관리자 롤아웃의 일부로 하나 이상의 healthCheck 단계를 만듭니다. 다음 정보를 사용 하 여 healthCheck 단계를 작성 합니다.

    1. 상태 관리 서비스 공급자에 의해 정의 된 상태 모니터의 REST API에 대 한 URI입니다.
    1. 인증 정보입니다. 현재는 API 키 스타일 인증만 지원 됩니다.
    1. 정상 응답을 정의 하는 [HTTP 상태 코드](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) 또는 정규식 모든 응답이 정상으로 간주 되려면 일치 해야 하는 정규식을 제공 하거나, 응답이 정상으로 간주 되려면 모든가 일치 해야 하는 식을 제공할 수 있습니다. 두 가지 방법이 모두 지원 됩니다.

    다음 Json은 예입니다.

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

1. Azure 배포 관리자 롤아웃에서 적절 한 시간에 healthCheck 단계를 호출 합니다. 다음 예제에서는 **stepGroup2**의 **postdeploymentsteps** 에서 상태 검사 단계를 호출 합니다.

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

예제를 살펴보려면 [자습서: Azure 배포 관리자에서 상태 검사 사용](./deployment-manager-health-check.md)을 참조 하세요.

## <a name="phases-of-a-health-check"></a>상태 검사 단계

이 시점에서 Azure 배포 관리자는 서비스의 상태를 쿼리 하는 방법과 롤아웃의 단계를 파악 하는 방법을 알고 있습니다. 그러나 Azure 배포 관리자를 사용 하면 이러한 검사 타이밍의 심층 구성도 가능 합니다. HealthCheck 단계는 세 개의 순차적 단계로 실행 되며, 모두 구성 가능한 기간이 있습니다. 

1. Wait

    1. 배포 작업이 완료 되 면 Vm이 다시 부팅 되거나, 새 데이터를 기반으로 다시 구성 되거나, 처음으로 시작 될 수 있습니다. 또한 서비스에서 상태 모니터링 공급자에 의해 유용한 상태 신호 내보내기를 시작 하는 데 시간이 걸립니다. 이 tumultuous 프로세스가 진행 되는 동안 업데이트가 아직 안정 된 상태에 도달 하지 않았기 때문에 서비스 상태를 확인 하는 것은 적절 하지 않을 수 있습니다. 실제로 리소스를 사용할 때 정상 상태와 비정상 상태 사이에서 서비스가 진동 될 수 있습니다. 
    1. 대기 단계에서 서비스 상태는 모니터링 되지 않습니다. 상태 검사 프로세스를 시작 하기 전에 배포 된 리소스를 굽기 시간으로 허용 하는 데 사용 됩니다. 
1. 탄력성

    1. 리소스가 안정화 되기 전에 굽기 하는 데 걸리는 시간을 확인 하는 것이 불가능 하기 때문에 탄력적 단계를 사용 하면 리소스가 불안정 해질 때와 정상 상태를 유지 해야 하는 시간 사이에 유연 하 게 시간을 줄 수 있습니다. 상태일.
    1. 탄력적 단계가 시작 되 면 Azure 배포 관리자는 서비스 상태에 대해 제공 된 REST 엔드포인트의 폴링을 주기적으로 시작 합니다. 폴링 간격을 구성할 수 있습니다. 
    1. 서비스가 비정상 상태임을 나타내는 신호로 상태 모니터가 돌아오면 이러한 신호는 무시 되 고 탄력적 단계가 계속 되며 폴링이 계속 됩니다. 
    1. 상태가 정상 임을 나타내는 신호를 포함 하는 상태 모니터가 다시 시작 되 면 탄력적 단계가 종료 되 고 HealthyState 단계가 시작 됩니다. 
    1. 따라서 탄력적 단계에 대해 지정 된 기간은 정상 응답이 필수로 간주 되기 전에 서비스 상태에 대 한 폴링에 소요 될 수 있는 최대 시간입니다. 
1. HealthyState

    1. HealthyState 단계에서 서비스 상태는 탄력적 단계와 동일한 간격으로 지속적으로 폴링합니다. 
    1. 서비스는 지정 된 전체 기간 동안 상태 모니터링 공급자의 정상 신호를 유지 해야 합니다. 
    1. 어떤 시점에서 비정상 응답이 감지 되 면 Azure 배포 관리자는 전체 롤아웃을 중지 하 고 비정상 서비스 신호를 전달 하는 REST 응답을 반환 합니다.
    1. HealthyState 기간이 종료 되 면 healthCheck가 완료 되 고 배포는 다음 단계로 진행 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 배포 관리자에서 상태 모니터링을 통합 하는 방법에 대해 알아보았습니다. 다음 문서에서 계속해서 배포 관리자를 통한 배포 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자습서: Azure 배포 관리자에서 상태 검사 통합](./deployment-manager-tutorial-health-check.md)