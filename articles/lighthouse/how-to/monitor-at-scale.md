---
title: 대규모로 위임 된 리소스 모니터링
description: 관리 중인 고객 테 넌 트에서 확장 가능한 방식으로 Azure Monitor 로그를 효과적으로 사용 하는 방법을 알아봅니다.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: fdd0147737da47613d6b7ef1bf6005e4c03de0dd
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163291"
---
# <a name="monitor-delegated-resources-at-scale"></a>대규모로 위임 된 리소스 모니터링

서비스 공급자로 서 [Azure Lighthouse](../overview.md)에 여러 고객 테 넌 트를 등록 했을 수 있습니다. Azure Lighthouse를 사용하여 서비스 공급자는 여러 테넌트에 걸쳐 대규모로 작업을 한 번에 수행할 수 있으므로 관리 작업을 보다 효율적으로 수행할 수 있습니다.

이 항목에서는 관리 중인 고객 테 넌 트에서 확장 가능한 방식으로 [Azure Monitor 로그](../../azure-monitor/platform/data-platform-logs.md) 를 사용 하는 방법을 보여 줍니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만이 지침은 [Azure Lighthouse를 사용 하 여 여러 테 넌 트를 관리](../concepts/enterprise.md)하는 기업에도 적용 됩니다.

## <a name="create-log-analytics-workspaces"></a>Log Analytics 작업 영역 만들기

데이터를 수집 하려면 Log Analytics 작업 영역을 만들어야 합니다. 이러한 Log Analytics 작업 영역은 Azure Monitor에 의해 수집 되는 데이터에 대 한 고유한 환경입니다. 각 작업 영역에는 자체 데이터 리포지토리 및 구성이 있으며 데이터 원본 및 솔루션은 특정 작업 영역에 데이터를 저장하도록 구성됩니다.

이러한 작업 영역을 고객 테 넌 트에 직접 만드는 것이 좋습니다. 이러한 방식으로 데이터가 자신의 테 넌 트로 내보내지지 않고 테 넌 트에 유지 됩니다. 또한 Log Analytics에서 지 원하는 모든 리소스 또는 서비스를 중앙에서 모니터링할 수 있으므로 모니터링 하는 데이터 형식에 대 한 유연성을 높일 수 있습니다.

[Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)를 사용 하거나 [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)를 사용 하거나 [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)를 사용 하 여 Log Analytics 작업 영역을 만들 수 있습니다.

## <a name="deploy-policies-that-log-data"></a>데이터를 기록 하는 정책 배포

Log Analytics 작업 영역을 만든 후에는 각 테 넌 트의 적절 한 작업 영역에 진단 데이터를 보내도록 고객 계층 구조 전체에 [Azure Policy](../../governance/policy/index.yml) 를 배포할 수 있습니다. 배포 하는 정확한 정책은 모니터링 하려는 리소스 종류에 따라 달라질 수 있습니다.

정책을 만드는 방법에 대 한 자세한 내용은 [자습서: 규정 준수를 적용 하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)를 참조 하세요. 이 [커뮤니티 도구](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) 는 사용자가 선택 하는 특정 리소스 종류를 모니터링 하는 정책을 만드는 데 도움이 되는 스크립트를 제공 합니다.

배포할 정책을 결정 한 경우 [대규모로 위임 된 구독에 배포할](policy-at-scale.md)수 있습니다.

## <a name="analyze-the-gathered-data"></a>수집 된 데이터 분석

정책을 배포한 후에는 각 고객 테 넌 트에서 만든 Log Analytics 작업 영역에 데이터가 로깅됩니다. 모든 관리 되는 고객에 대 한 통찰력을 얻기 위해 [Azure Monitor 통합 문서](../../azure-monitor/platform/workbooks-overview.md) 와 같은 도구를 사용 하 여 여러 데이터 원본의 정보를 수집 하 고 분석할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- 여러 Log Analytics 작업 영역에서 [업데이트 관리 로그를 쿼리하여](../../automation/update-management/update-mgmt-query-logs.md) 패치 준수 보고를 추적 하는이 [MVP 제작 샘플 통합 문서](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)를 살펴보세요. 
- [Azure Monitor](../../azure-monitor/index.yml)에 대해 알아봅니다.
- [Azure Monitor 로그](../../azure-monitor/platform/data-platform-logs.md)에 대해 알아봅니다.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
