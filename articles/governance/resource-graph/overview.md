---
title: Azure Resource Graph 개요
description: Azure Resource Graph 서비스를 통해 구독 및 테넌트에 걸쳐 대규모의 복잡한 리소스를 쿼리하는 방법을 이해합니다.
ms.date: 08/13/2020
ms.topic: overview
ms.openlocfilehash: 73143c13fd83c8b784679ac28bd9a14f1c6a6888
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798536"
---
# <a name="what-is-azure-resource-graph"></a>Azure Resource Graph란?

Azure Resource Graph는 제공된 구독 세트에서 대규모로 쿼리를 수행할 수 있는 효율적인 고성능 리소스 탐색 기능을 제공하여 작업 환경을 효과적으로 관리할 수 있도록 Azure Resource Management를 확장할 수 있게 디자인된 Azure의 서비스입니다. 이러한 쿼리는 다음 기능을 제공합니다.

- 리소스 속성별로 복합 필터링, 그룹화 및 정렬을 사용하여 리소스를 쿼리하는 기능
- 거버넌스 요구 사항에 따라 반복적으로 리소스를 살펴보는 기능.
- 광범위한 클라우드 환경에서 정책을 적용할 때 미치는 영향을 평가하는 기능
- [리소스 속성에 대한 세부 변경 내용](./how-to/get-resource-changes.md)(미리 보기)에 대한 기능.

이 설명서에서는 각 기능을 자세히 설명합니다.

> [!NOTE]
> Azure Resource Graph는 Azure Portal의 검색 창, 새로운 찾아보기 ‘모든 리소스’ 환경 및 Azure Policy의 [변경 내용](../policy/how-to/determine-non-compliance.md#change-history)
> _visual diff_에서 사용됩니다. 이 기능은 고객이 대규모 환경을 관리할 수 있도록 디자인되었습니다.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph가 Azure Resource Manager를 보완하는 방법

Resource Manager는 현재 기본 리소스 필드, 특히 리소스 이름, ID, 유형, 리소스 그룹, 구독 및 위치에서 쿼리를 지원합니다. 또한 Resource Manager는 한 번에 하나의 리소스에 대한 세부 속성을 위해 개별 리소스 공급 기업을 호출하는 기능을 제공합니다.

Azure Resource Graph를 사용하면 각 리소스 공급자를 별도로 호출하지 않고도 리소스 공급자가 반환하는 이러한 속성에 액세스할 수 있습니다. 지원되는 리소스 종류의 목록은 [테이블 및 리소스 종류 참조](./reference/supported-tables-resources.md)를 검토하세요. 지원되는 리소스 종류를 확인하는 다른 방법은 [Azure Graph Explorer Schema 브라우저](./first-query-portal.md#schema-browser)를 사용하는 것입니다.

Azure Resource Graph를 통해 다음을 수행할 수 있습니다.

- 각 리소스 공급자를 별도로 호출하지 않고 리소스 공급자가 반환하는 속성에 액세스합니다.
- 리소스에 대한 지난 14일 동안의 변경 기록을 보고 변경된 속성과 시기를 확인합니다. (미리 보기)

> [!NOTE]
> _미리 보기_ 기능으로, 일부 `type` 개체에는 사용 가능한 추가 비Resource Manager 속성이 있습니다. 자세한 내용은 [확장 속성(미리 보기)](./concepts/query-language.md#extended-properties)을 참조하세요.

## <a name="how-resource-graph-is-kept-current"></a>Resource Graph가 최신 상태를 유지하는 방식

Azure 리소스가 업데이트되면 Resource Graph는 Resource Manager로부터 변경 알림을 받습니다.
그런 다음, Resource Graph는 해당 데이터베이스를 업데이트합니다. Resource Graph는 또한 일반 _전체 검색_을 수행합니다. 이 검사는 놓친 알림이 있거나 Resource Manager 외부에서 리소스가 업데이트될 때 Resource Graph 데이터가 최신 상태인지 확인합니다.

> [!NOTE]
> Resource Graph는 각 리소스 공급자의 미리 보기가 아닌 최신 API에 `GET`을 사용하여 속성 및 값을 수집합니다. 따라서 필요한 속성을 사용하지 못할 수 있습니다. 경우에 따라 사용되는 API 버전이 재정의되어 결과에 더 최신 속성 또는 널리 사용되는 속성이 제공되었습니다. 사용자 환경의 전체 목록은 [각 리소스 유형에 대한 API 버전 표시](./samples/advanced.md#apiversion) 샘플을 참조하세요.

## <a name="the-query-language"></a>쿼리 언어

지금까지 Azure Resource Graph에 대해 알아보았으므로 이제 쿼리 생성 방법을 자세히 살펴보겠습니다.

Azure Resource Graph의 쿼리 언어는 Azure Data Explorer의 [Kusto 쿼리 언어](/azure/data-explorer/data-explorer-overview)에 기반을 두고 있다는 것을 이해하는 것이 중요합니다.

먼저 Azure Resource Graph에서 사용할 수 있는 작업 및 함수에 대한 자세한 내용은 [Resource Graph 쿼리 언어](./concepts/query-language.md)를 참조하세요. 리소스를 찾아보려면 [리소스 탐색](./concepts/explore-resources.md)을 참조하세요.

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph의 권한

Resource Graph를 사용하려면 쿼리하려는 리소스에 대해 적어도 읽기 액세스 권한이 있는 RBAC([역할 기반 액세스 제어](../../role-based-access-control/overview.md))에 적절한 권한이 부여되어야 합니다. Azure 개체 또는 개체 그룹에 대한 `read` 이상의 권한이 없으면 결과가 반환되지 않습니다.

> [!NOTE]
> Resource Graph는 로그인하는 동안 보안 주체에 제공되는 구독을 사용합니다. 활성 세션 중에 추가된 새 구독의 리소스를 보려면 보안 주체가 컨텍스트를 새로 고쳐야 합니다. 이 작업은 로그아웃했다가 다시 로그인하면 자동으로 수행됩니다.

Azure CLI 및 Azure PowerShell에서는 사용자가 액세스할 수 있는 구독을 사용합니다. REST API를 직접 사용하는 경우 구독 목록은 사용자가 제공합니다. 사용자가 목록에 있는 구독에 액세스할 수 있는 경우 사용자가 액세스할 수 있는 구독에 대한 쿼리 결과가 반환됩니다. 이 동작은 [리소스 그룹 - 목록](/rest/api/resources/resourcegroups/list) \-을 호출하는 경우와 동일합니다. 즉, 결과가 부분적일 수 있음을 표시하지 않고 액세스할 수 있는 리소스 그룹을 가져옵니다. 사용자에게 적절한 권한이 있는 구독 목록에서 구독이 없는 경우 응답은 _403_(사용할 수 없음)입니다.

> [!NOTE]
> **미리 보기** REST API 버전 `2020-04-01-preview`에서 구독 목록은 생략될 수 있습니다.
> `subscriptions` 및 `managementGroupId` 속성이 요청에서 둘 다 정의되지 않은 경우 _범위_는 테넌트로 설정됩니다. 자세한 내용은 [쿼리 범위](./concepts/query-language.md#query-scope)를 참조하세요.

## <a name="throttling"></a>제한

무료 서비스로서, Resource Graph에 대한 쿼리는 모든 고객에게 최상의 경험과 응답 시간을 제공하도록 제한됩니다. 자주 수행되는 대규모 쿼리에 Resource Graph API를 사용하려는 조직은 [Resource Graph 포털 페이지](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)의 ‘피드백’ 포털을 사용하세요.
비즈니스 사례를 제공하고, 연락을 받을 수 있도록 '피드백과 관련하여 Microsoft에서 이메일을 보내도 좋습니다' 확인란을 선택하세요.

Resource Graph는 사용자 수준에서 쿼리를 제한합니다. 서비스 응답은 다음 HTTP 헤더를 포함합니다.

- `x-ms-user-quota-remaining`(int): 사용자의 나머지 리소스 할당량입니다. 이 값은 쿼리 수에 매핑됩니다.
- `x-ms-user-quota-resets-after`(hh:mm:ss): 사용자의 할당량 소비가 재설정될 때까지 남은 기간

자세한 내용은 [제한된 요청에 대한 지침](./concepts/guidance-for-throttled-requests.md)을 참조하세요.

## <a name="running-your-first-query"></a>첫 번째 쿼리 실행

Azure Portal의 일부인 Azure Resource Graph Explorer를 사용하면 Azure Portal에서 직접 Resource Graph 쿼리를 실행할 수 있습니다. 결과를 동적 차트로 고정하여 포털 워크플로에 실시간 동적 정보를 제공합니다. 자세한 내용은 [Azure Resource Graph Explorer를 사용한 첫 번째 쿼리](./first-query-portal.md)를 참조하세요.

Resource Graph는 Azure CLI, Azure PowerShell, Python용 Azure SDK 등을 지원합니다. 쿼리는 각 언어에서 동일하게 구조화됩니다. 다음을 사용하여 Resource Graph를 활성화하는 방법을 알아봅니다.

- [Azure Portal 및 Resource Graph Explorer](./first-query-portal.md) 
- [Azure CLI](./first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](./first-query-powershell.md#add-the-resource-graph-module)
- [Python](./first-query-python.md#add-the-resource-graph-library)

## <a name="next-steps"></a>다음 단계

- [쿼리 언어](./concepts/query-language.md)에 대해 자세히 알아보기
- [시작 쿼리](./samples/starter.md)에 사용되는 언어를 참조하세요.
- [고급 쿼리](./samples/advanced.md)의 고급 사용법을 참조하세요.
