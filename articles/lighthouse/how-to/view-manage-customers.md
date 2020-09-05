---
title: 고객과 위임된 리소스 보기 및 관리
description: Azure Lighthouse를 사용 하는 서비스 공급자나 enterprise로, Azure Portal 내 고객으로 이동 하 여 위임 된 모든 리소스 및 구독을 볼 수 있습니다.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 165110556282e90bbacf4ce3b3c59e34b203a528
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167201"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>고객과 위임된 리소스 보기 및 관리

[Azure Lighthouse](../overview.md) 를 사용 하는 서비스 공급자는 [Azure Portal](https://portal.azure.com) 의 **내 고객** 페이지를 사용 하 여 위임 된 고객 리소스 및 구독을 볼 수 있습니다. 

> [!TIP]
> 여기서는 서비스 공급자 및 고객을 참조 하지만, [여러 테 넌 트를 관리](../concepts/enterprise.md) 하는 기업은 동일한 프로세스를 사용 하 여 관리 환경을 통합할 수 있습니다.

Azure Portal의 **내 고객** 페이지에 액세스하려면 **모든 서비스**를 선택한 다음, **내 고객**을 검색하여 선택합니다. Azure Portal 맨 위에 있는 검색 상자에 "내 고객"을 입력하여 찾을 수도 있습니다.

**내 고객** 페이지의 상위 **고객** 섹션에는 위임 된 구독 또는 리소스 그룹이 있는 고객에 대 한 정보만 표시 됩니다. 다른 고객과 작업 하는 경우 (예: [클라우드 솔루션 공급자 프로그램](/partner-center/csp-overview)을 통해) 해당 [리소스를 Azure Lighthouse로 등록](onboard-customer.md)하지 않는 한 **고객 섹션에서 해당 고객에** 대 한 정보를 볼 수 없습니다.

페이지의 아래쪽에서 **클라우드 솔루션 공급자 (미리 보기)** 라는 별도의 섹션에는 [Microsoft MCA (고객 계약)에 서명](/partner-center/confirm-customer-agreement) 하 고 [Azure 요금제](/partner-center/azure-plan-get-started)에 있는 CSP 고객에 대 한 청구 정보 및 리소스가 표시 됩니다. 자세한 내용은 [Microsoft 파트너 계약 청구 계정 시작](../../cost-management-billing/understand/mpa-overview.md)을 참조하세요. 이러한 CSP 고객은 Azure Lighthouse에 등록 여부에 관계 없이이 섹션에 표시 됩니다. 마찬가지로, CSP 고객은 Azure Lighthouse에 등록 하기 위해 **고객** 의 **클라우드 솔루션 공급자 (미리 보기)** 섹션에 표시 하지 않아도 됩니다.

> [!NOTE]
> 고객은 Azure Portal의 **서비스 공급자**로 이동하여 서비스 공급자에 대한 정보를 볼 수 있습니다. 자세한 내용은 [서비스 공급자 보기 및 관리](view-manage-service-providers.md)를 참조하세요.

## <a name="view-and-manage-customer-details"></a>고객 세부 정보 보기 및 관리

고객 세부 정보를 보려면 **내 고객** 페이지의 왼쪽에서 **고객**을 선택합니다.

각 고객에 대해 고객의 이름, 고객 ID(테넌트 ID) 및 참여와 연결된 제품을 볼 수 있습니다. **위임** 열에는 위임된 구독의 수 및/또는 위임된 리소스 그룹의 수가 표시됩니다.

> [!IMPORTANT]
> 위임을 보려면 온보딩 프로세스에서 사용자에게 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할(또는 읽기 권한자 액세스 권한을 포함하는 다른 기본 제공 역할)을 부여해야 합니다.

페이지 맨 위에 있는 필터를 사용하여 고객 정보를 정렬 및 그룹화하거나 특정 고객, 제품 또는 키워드를 기준으로 필터링할 수 있습니다.

이 페이지에서 다음 정보를 볼 수 있습니다.

- 고객과 연결된 모든 구독, 제품 및 위임을 보려면 고객의 이름을 선택합니다.
- 제품 및 해당 위임에 대한 자세한 내용을 보려면 제품 이름을 선택합니다.
- 위임된 구독 또는 리소스 그룹의 역할 할당에 대한 자세한 내용을 보려면 **위임** 열의 항목을 선택합니다.

## <a name="view-and-manage-delegations"></a>위임 보기 및 관리

위임에는 액세스 권한이 있는 사용자 및 권한과 함께 위임 된 구독 또는 리소스 그룹이 표시 됩니다. 이 정보를 보려면 **내 고객** 페이지의 왼쪽에서 **위임**을 선택합니다.

페이지 맨 위에 있는 필터를 사용하여 액세스 할당 정보를 정렬 및 그룹화하거나 특정 고객, 제품 또는 키워드를 기준으로 필터링할 수 있습니다.

### <a name="view-role-assignments"></a>역할 할당 보기

각 위임에 연결된 사용자 및 사용 권한은 **역할 할당** 열에 표시됩니다. 각 항목을 선택하여 구독 또는 리소스 그룹에 대한 액세스 권한이 부여된 사용자, 그룹 및 서비스 주체의 전체 목록을 볼 수 있습니다. 여기서 특정 사용자, 그룹 또는 서비스 주체 이름을 선택하여 자세한 정보를 얻을 수 있습니다.

### <a name="remove-delegations"></a>위임 제거

Azure Lighthouse에 고객을 온 보 딩 할 때 [관리 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) 을 가진 사용자를 포함 한 경우 해당 사용자는 해당 위임에 대 한 행에 표시 되는 휴지통 아이콘을 선택 하 여 위임을 제거할 수 있습니다. 이렇게 하면 서비스 공급자의 테 넌 트에 있는 사용자가 이전에 위임 된 리소스에 액세스할 수 없게 됩니다.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>위임된 구독의 컨텍스트에서 작업

작업 중인 디렉터리를 전환하지 않고 Azure Portal 내의 위임된 구독 컨텍스트에서 직접 작업할 수 있습니다. 이렇게 하려면

1. Azure Portal 위쪽에 있는 **디렉터리 + 구독** 아이콘을 선택합니다.
2. **전역구독** 필터에서 위임된 구독에 대한 확인란만 선택되어 있는지 확인합니다. **현재 + 위임된 디렉터리** 드롭다운 상자를 사용하여 특정 디렉터리 내의 구독만 표시할 수 있습니다. (디렉터리가 로그인된 디렉터리로 변경되므로 **디렉터리 전환**을 사용하지 마세요.)

그런 다음, [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)을 지원하는 서비스에 액세스하는 경우 이 서비스는 기본적으로 사용자가 선택한 위임된 구독의 컨텍스트가 됩니다. 위의 단계를 수행하고 **모두 선택** 확인란을 선택하여 변경하거나 대신 사용할 하나 이상의 구독을 선택할 수 있습니다.

> [!NOTE]
> 전체 구독에 액세스하는 대신, 하나 이상의 리소스 그룹에 대한 액세스 권한이 부여된 경우 해당 리소스 그룹이 속한 구독을 선택할 수 있습니다. 그런 다음, 해당 구독의 컨텍스트에서 작업하게 되지만 지정된 리소스 그룹에만 액세스할 수 있습니다.

또한 해당 서비스 내에서 구독 또는 리소스 그룹을 선택하여 테넌트 간 관리 환경을 지원하는 서비스 내에서 위임된 구독 또는 리소스 그룹과 관련된 기능에 액세스할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal에서 **서비스 공급자**로 이동하여 고객이 [서비스 공급자를 보고 관리](view-manage-service-providers.md)하는 방법을 알아봅니다.
