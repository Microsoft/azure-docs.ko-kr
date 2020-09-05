---
title: Azure 센티널의 권한 | Microsoft Docs
description: 이 문서에서는 Azure 센티널에서 역할 기반 액세스 제어를 사용 하 여 사용자에 게 권한을 할당 하 고 각 역할에 대해 허용 되는 작업을 식별 하는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b48ff1043ae8128a5cbfdcbba0548d89b5af2624
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565845"
---
# <a name="permissions-in-azure-sentinel"></a>Azure 센티널의 사용 권한

Azure 센티널은 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 를 사용 하 여 azure의 사용자, 그룹 및 서비스에 할당할 수 있는 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 제공   합니다.

RBAC를 사용 하 여 보안 운영 팀 내에서 역할을 만들고 할당 하 여 Azure 센티널에 적절 한 액세스 권한을 부여 합니다. 다양 한 역할을 통해 Azure 센티널의 사용자가 보고 수행할 수 있는 작업을 세밀 하 게 제어할 수 있습니다. Azure 역할은 azure 센티널 작업 영역에서 직접 할당 하거나 (아래 참고 참조) 작업 영역이 속해 있는 구독 또는 리소스 그룹에서 Azure 센티널이 상속할 수 있습니다.

## <a name="roles-for-working-in-azure-sentinel"></a>Azure 센티널에서 작업 하기 위한 역할

### <a name="azure-sentinel-specific-roles"></a>Azure 센티널 특정 역할

세 가지 전용 기본 제공 Azure 센티널 역할이 있습니다.

**모든 Azure 센티널 기본 제공 역할은 Azure 센티널 작업 영역에서 데이터에 대 한 읽기 액세스 권한을 부여 합니다.**

- [Azure 센티널 판독기](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) 는 데이터, 인시던트, 통합 문서 및 기타 azure 센티널 리소스를 볼 수 있습니다.

- [Azure 센티널 응답자](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) 는 위의 외에도 인시던트 관리 (할당, 해제 등)를 수행할 수 있습니다.

- [Azure 센티널 참여자](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) 는 위의 외에도 통합 문서, 분석 규칙 및 기타 azure 센티널 리소스를 만들고 편집할 수 있습니다.

> [!NOTE]
>
> - 최상의 결과를 위해 이러한 역할은 Azure 센티널 작업 영역을 포함 하는 **리소스 그룹** 에 할당 되어야 합니다. 이러한 방식으로 역할은 Azure 센티널을 지원 하기 위해 배포 된 모든 리소스에 적용 됩니다. 이러한 리소스는 동일한 리소스 그룹에도 배치 되어야 합니다.
>
> - 또 다른 옵션은 Azure 센티널 **작업 영역** 자체에서 직접 역할을 할당 하는 것입니다. 이 작업을 수행 하는 경우 해당 작업 영역의 SecurityInsights **솔루션 리소스** 에도 동일한 역할을 할당 해야 합니다. 다른 리소스에도 할당 해야 할 수 있으며, 리소스에 대 한 역할 할당을 지속적으로 관리 해야 합니다.

### <a name="additional-roles-and-permissions"></a>추가 역할 및 사용 권한

특정 작업 요구 사항을 가진 사용자는 작업을 수행 하기 위해 추가 역할 또는 특정 권한을 할당 해야 할 수 있습니다.

- 플레이 북을 사용 하 여 위협에 대 한 응답 자동화

    Azure 센티널은 자동화 된 위협 대응을 위해 플레이 **북** 을 사용 합니다. Playbooks는 **Azure Logic Apps**을 기반으로 하며 별도의 Azure 리소스입니다. 보안 작업 팀의 특정 멤버에 게 보안 오케스트레이션, 자동화 및 응답 (대화 충성도) 작업에 대 한 Logic Apps를 사용 하는 기능을 할당 하는 것이 좋습니다. [논리 앱 참가자](../role-based-access-control/built-in-roles.md#logic-app-contributor) 역할 또는 [논리 앱 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator) 역할을 사용 하 여 playbooks 사용에 대 한 명시적 권한을 할당할 수 있습니다.

- Azure 센티널에 데이터 원본 연결

    사용자가 **데이터 커넥터**를 추가 하려면 Azure 센티널 작업 영역에 대 한 사용자 쓰기 권한을 할당 해야 합니다. 또한 관련 커넥터 페이지에 나열 된 대로 각 커넥터에 필요한 추가 사용 권한을 기록해 둡니다.

- 인시던트를 할당 하는 게스트 사용자

    게스트 사용자가 인시던트를 할당할 수 있어야 하는 경우 Azure 센티널 응답자 역할 외에도 사용자에 게 [디렉터리 판독기](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)의 역할을 할당 해야 합니다. 이 역할은 Azure RBAC 역할은 아니지만 **Azure Active Directory** *역할이 며 일반* (비 게스트) 사용자에 게는 기본적으로이 역할이 할당 됩니다. 

병렬 비교는 [아래 표](#roles-and-allowed-actions)를 참조 하세요.

### <a name="other-roles-you-might-see-assigned"></a>할당 된 다른 역할

Azure 센티널 특정 Azure 역할을 할당 하는 경우 다른 용도로 사용자에 게 할당 되었을 수 있는 다른 Azure 및 Log Analytics Azure 역할을 통해 제공 될 수 있습니다. 이러한 역할은 Azure 센티널 작업 영역 및 기타 리소스에 대 한 액세스를 포함 하는 보다 광범위 한 사용 권한 집합을 부여 한다는 것을 알고 있어야 합니다.

- **Azure 역할:** [소유자](../role-based-access-control/built-in-roles.md#owner), [참가자](../role-based-access-control/built-in-roles.md#contributor)및 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader)입니다. Azure 역할은 Log Analytics 작업 영역 및 Azure 센티널 리소스를 포함 하 여 모든 Azure 리소스에 대 한 액세스 권한을 부여 합니다.

- **역할 Log Analytics:** [Log Analytics 참가자](../role-based-access-control/built-in-roles.md#log-analytics-contributor) 및 [Log Analytics 판독기](../role-based-access-control/built-in-roles.md#log-analytics-reader)입니다. Log Analytics 역할은 Log Analytics 작업 영역에 대 한 액세스 권한을 부여 합니다. 

예를 들어 azure 센티널 구독자 **역할이 아닌** Azure 센티널 **구독자** 역할을 할당 받은 사용자는 azure 수준 **기여자** 역할이 할당 된 경우에도 azure 센티널의 항목을 편집할 수 있습니다. 따라서 Azure 센티널 에서만 사용자에 게 권한을 부여 하려는 경우이 사용자의 이전 권한을 신중 하 게 제거 하 여 다른 리소스에 대 한 필요한 액세스를 중단 하지 않도록 해야 합니다.

## <a name="roles-and-allowed-actions"></a>역할 및 허용되는 작업

다음 표에는 Azure 센티널의 역할 및 허용 되는 작업이 요약 되어 있습니다. 

| 역할 | 플레이 북 만들기 및 실행| 통합 문서, 분석 규칙 및 기타 Azure 센티널 리소스 만들기 및 편집 | 인시던트 관리 (해제, 할당 등) | 데이터, 인시던트, 통합 문서 및 기타 Azure 센티널 리소스 보기 |
|---|---|---|---|---|
| Azure Sentinel 읽기 권한자 | -- | -- | -- | &#10003; |
| Azure Sentinel 응답자 | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 기여자 | -- | &#10003; | &#10003; | &#10003; |
| Azure 센티널 기여자 + 논리 앱 참가자 | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>사용자 지정 역할 및 고급 RBAC

- Azure 기본 제공 역할을 사용 하는 것 외에도 azure에 대 한 azure 사용자 지정 역할을 만들 수 있습니다. Azure 센티널에 대 한 azure 사용자 지정 역할은 azure 센티널 및 [azure Log Analytics 리소스](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)에 대 한 [특정 사용 권한에](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) 따라 다른 [사용자 지정 azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) 역할을 만드는 것과 동일한 방식으로 만들어집니다.

- Azure 센티널 작업 영역의 데이터에서 Log Analytics 고급 역할 기반 액세스 제어를 사용할 수 있습니다. 여기에는 데이터 형식 기반 RBAC 및 리소스 중심 RBAC가 모두 포함 됩니다. Log Analytics 역할에 대 한 자세한 내용은 [Azure Monitor에서 로그 데이터 및 작업 영역 관리](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널 사용자를 위해 역할을 수행 하는 방법 및 사용자가 수행할 수 있는 각 역할에 대해 알아보았습니다.

* [Azure 센티널 블로그](https://aka.ms/azuresentinelblog). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
