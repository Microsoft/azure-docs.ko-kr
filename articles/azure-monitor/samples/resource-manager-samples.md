---
title: Azure Monitor에 대한 Resource Manager 템플릿 샘플
description: Resource Manager 템플릿을 사용하여 Azure Monitor 기능 배포 및 구성
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: ab869fc8577d4d1934be96404ded5a2051237bbf
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922731"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Azure Monitor에 대한 Resource Manager 템플릿 샘플

[Azure Resource Manager 템플릿](../../azure-resource-manager/templates/template-syntax.md)을 사용하여 대규모로 Azure Monitor를 배포하고 구성할 수 있습니다. 다음 문서에서는 다양한 Azure Monitor 기능에 대한 샘플 템플릿을 제공합니다. 이러한 샘플을 특정 요구 사항에 맞게 수정하고 Resource Manager 템플릿 배포를 위한 표준 방법을 사용하여 배포할 수 있습니다. 

## <a name="deploying-the-sample-templates"></a>샘플 템플릿 배포
샘플을 사용하는 기본 단계는 다음과 같습니다.

1. 템플릿을 복사하고 JSON 파일로 저장합니다.
2. 사용자 환경에 대한 매개 변수를 수정하고 JSON 파일로 저장합니다.
4. [Resource Manager 템플릿에 대한 배포 방법](../../azure-resource-manager/templates/deploy-powershell.md)을 사용하여 템플릿을 배포합니다. 

예를 들어 다음 명령을 통해 PowerShell 또는 Azure CLI를 사용하여 리소스 그룹에 템플릿 및 매개 변수 파일을 배포합니다.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>샘플 템플릿의 목록

- [에이전트](resource-manager-agent.md) - Log Analytics 에이전트와 진단 확장을 배포하고 구성합니다.
- 경고
  - [로그 경고 규칙](resource-manager-alerts-log.md) - 로그 쿼리 및 Azure 활동 로그의 경고입니다.
  - [메트릭 경고 규칙](resource-manager-alerts-metric.md) - 다른 종류의 논리를 사용하는 메트릭의 경고입니다.
- [Application Insights](resource-manager-app-resource.md)
- [진단 설정](resource-manager-diagnostic-settings.md) - 다른 리소스 유형에서 로그 및 메트릭을 전달하는 진단 설정을 만듭니다.
- [로그 쿼리](resource-manager-log-queries.md) - Log Analytics 작업 영역에서 저장된 로그 쿼리를 만듭니다.
- [Log Analytics 작업 영역](resource-manager-workspace.md) - Log Analytics 작업 영역을 만들고 Log Analytics 에이전트에서 다른 데이터 원본의 컬렉션을 구성합니다.
- [통합 문서](resource-manager-workbooks.md) - 통합 문서를 만듭니다.
- [컨테이너용 Azure Monitor](resource-manager-container-insights.md) - 컨테이너용 Azure Monitor에 대한 온보드 클러스터입니다.
- [VM용 Azure Monitor](resource-manager-vminsights.md) - VM용 Azure Monitor에 대한 온보드 가상 머신입니다.



## <a name="next-steps"></a>다음 단계

- [Resource Manager 템플릿](../../azure-resource-manager/templates/overview.md)에 대한 자세한 정보