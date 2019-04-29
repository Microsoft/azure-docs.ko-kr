---
title: 이해 및 Azure Cost Management 범위 사용 | Microsoft Docs
description: 이 문서에서는 Azure Cost Management에 Api 범위를 사용 하는 방법에 사용할 수 있는 청구 및 리소스 관리 범위를 이해 하도록 도와줍니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 4e7956e8873b552fcd73c51a51f51d99f21af324
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61003014"
---
# <a name="understand-and-work-with-scopes"></a>범위 이해 및 작업

이 문서에서는 Azure Cost Management에 Api 범위를 사용 하는 방법에 사용할 수 있는 청구 및 리소스 관리 범위를 이해 하도록 도와줍니다.

## <a name="scopes"></a>범위

A _범위_ 는 Azure AD 사용자가 액세스 하 고 서비스를 관리 하는 여기서 Azure 리소스 계층 구조의 노드입니다. 대부분의 Azure 리소스 생성 및 구독의 일부인 리소스 그룹에 배포 됩니다. 또한 Microsoft Azure 구독 청구 데이터를 관리 하는 역할 특정 상위 두 계층을 제공 합니다.
- 지불 청구서 등의 데이터를 청구합니다.
- 비용 및 정책 거 버 넌 스와 같은 클라우드 서비스

범위는 여기서 있습니다 청구 데이터를 관리, 역할 관련 지불, 청구서를 보려면 일반 계정 관리를 수행 합니다. 청구 및 계정 역할에 사용 되는 리소스 관리를 사용 하는에서 별도로 관리 됩니다 [Azure RBAC](../role-based-access-control/overview.md)합니다. 액세스 제어 차이점을 포함 하 여 범위를 별도의 의도 명확 하 게 구분 하기 위해 이러한 이라고 _범위를 청구_ 하 고 _RBAC 범위_, 각각.

## <a name="how-cost-management-uses-scopes"></a>Cost Management 범위를 사용 하는 방법

위 리소스가 단일 리소스 그룹 또는 전체 청구 계정에 액세스할 수는 수준 비용을 관리 하는 조직이 전혀 범위로 지정 비용된 관리가 작동 합니다. 청구 범위는 Microsoft 계약 (구독 유형)에 따라 다를 있지만 RBAC 범위는 그렇지 않습니다.

## <a name="azure-rbac-scopes"></a>Azure RBAC 범위

Azure는 리소스 관리에 대 한 세 가지 범위를 지원합니다. 각 범위는 액세스 및 거 버 넌 스를 포함 하 여 관리를 지원 하지만 비용 관리에 제한 되지 않습니다.

- [**관리 그룹** ](../governance/management-groups/index.md) -Azure 구독을 구성 하려면 8 개 수준까지, 계층적 컨테이너가 있습니다.

    리소스 유형: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **구독** -Azure 리소스에 대 한 기본 컨테이너입니다.

    리소스 유형: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**리소스 그룹** ](../azure-resource-manager/resource-group-overview.md#resource-groups) -동일한 수명 주기를 공유 하는 Azure 솔루션에 관련 된 리소스의 논리적 그룹화입니다. 예를 들어 리소스를 배포 하 고 함께 삭제 됩니다.

    리소스 유형: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

관리 그룹을 사용 하 여 구독 계층을 구성할 수 있습니다. 예를 들어 관리 그룹을 사용 하 여 논리 조직 계층을 만들 수 있습니다. 그런 다음 프로덕션 및 개발/테스트 워크 로드에 대 한 팀 구독을 제공 합니다. 한 다음 각 하위 시스템 또는 구성 요소를 관리에 대 한 구독에서 리소스 그룹을 만듭니다.

만들기 조직 계층을 사용 하면 비용 및 정책 준수 롤업 조직이 됩니다. 그런 다음 각 리더 볼 수 있으며 현재 비용 분석. 및 다음 예산을 지출 한도 잘못 된 패턴을 옮기고 최하위 수준에서 Advisor 권장 사항 사용 하 여 비용 최적화를 만들 수 있습니다.

비용을 볼 필요에 따라 구성 및 관리 비용, 예산 등 내보내기에 대 한 액세스 권한을 부여는 Azure RBAC를 사용 하 여 거 버 넌 스 범위에서 수행 됩니다. Azure RBAC를 사용 하 여 Azure AD 사용자에 게 부여 하 고 미리 정의 된 일련의 특정 범위에서 역할에 정의 된 작업을 수행 하려면 그룹 액세스 키를 누릅니다. 예를 들어 관리 그룹 범위에 할당 된 역할 중첩 된 구독 및 리소스 그룹에 동일한 사용 권한을 부여 합니다.

Cost Management는 다음 범위 중 각각에 대해 다음 기본 제공 역할을 지원합니다.

- [**소유자** ](../role-based-access-control/built-in-roles.md#owner) – 비용을 확인 하 고 비용 구성을 비롯 한 모든 것을 관리할 수 있습니다.
- [**참가자** ](../role-based-access-control/built-in-roles.md#contributor) – 비용을 확인 하 고 모든 사항을 액세스 제어를 제외한 비용 구성을 비롯 하 여 관리할 수 있습니다.
- [**판독기** ](../role-based-access-control/built-in-roles.md#reader) – 비용 데이터 및 구성을 포함 한 모든 것을 볼 수 있지만 변경할 수 없습니다.
- [**비용 관리 참여자** ](../role-based-access-control/built-in-roles.md#cost-management-contributor) – 비용을 볼 수 있습니다 및 비용 구성을 관리 합니다.
- [**비용 관리 판독기** ](../role-based-access-control/built-in-roles.md#cost-management-reader) – 비용 데이터 및 구성을 볼 수 있습니다.

비용된 관리 참가자는 권장 되는 최소 권한 역할입니다. 사용자 만들기 및 예산 관리에 대 한 액세스를 허용 하 고 보다 효과적으로 모니터링 하 고 비용 보고 내보냅니다. 비용 관리 참가자 엔드-투-엔드 비용 관리 시나리오를 지 원하는 역할을 추가 해야 할 수 있습니다. 다음과 같은 시나리오를 고려해 보세요.

- **예산을 초과 되 면 역할** -비용 관리 참가자 만들기 및/또는 초과분에 자동으로 대응 하는 작업 그룹 관리에 대 한 액세스를 해야 합니다. 권한을 부여 하는 것이 좋습니다 [Monitoring Contributor](../role-based-access-control/built-in-roles.md#monitoring-contributor) 예산 임계값이 초과 될 때 사용 하는 작업 그룹을 포함 하는 리소스 그룹에 있습니다. 특정 작업을 자동화에 사용 되는 Automation 및 Azure Functions와 같은 특정 서비스에 대 한 추가 역할을 필요 합니다.
- **데이터 내보내기 비용 일정** -비용 관리 참가자도 저장소 계정에 데이터를 복사 하는 내보내기 예약 하기 위한 저장소 계정 관리에 대 한 액세스를 해야 합니다. 권한을 부여 하는 것이 좋습니다 [Storage 계정 참여자](../role-based-access-control/built-in-roles.md#storage-account-contributor) 데이터 비용 계정 저장소를 포함 하는 리소스 그룹에 내보내집니다.
- **비용 절감 권장 사항 보기** -비용 관리 Readers 및 Contributors 기본적으로 권장 사항에 액세스할 수 없습니다. 권장 사항에 대 한 액세스는 개별 리소스에 대 한 읽기 액세스에 필요합니다. 권한을 부여 하는 것이 좋습니다 [판독기](../role-based-access-control/built-in-roles.md#reader) 또는 [서비스에 특정 한 역할이](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)합니다.

## <a name="enterprise-agreement-scopes"></a>기업 계약 범위

EA (기업 계약) 청구 계정에 등록의 경우 다음 범위에

- [**대금 청구 계정** ](../billing/billing-view-all-accounts.md) -EA 등록을 나타냅니다. 청구서는이 범위에 생성 됩니다. 이 범위 같은 Marketplace 및 예약을 기반으로 사용 되지 않는 구매에 사용할 수만 있습니다. 부서 또는 등록 계정에 표시 되지 않는 합니다.

    리소스 유형: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **부서** -등록 계정의 그룹화 선택 사항입니다.

    리소스 유형: `Billing/billingAccounts/departments`

- **등록 계정** -단일 계정 소유자를 나타냅니다. 여러 사용자에 게 액세스 권한 부여를 지원 하지 않습니다.

    리소스 유형: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

거 버 넌 스 범위는 단일 디렉터리에 바인딩되어 있지만 EA 청구 범위 없습니다. EA 청구 계정에는 다양 한 Azure AD 디렉터리 구독이 있을 수 있습니다.

EA 청구 범위는 다음 역할을 지원합니다.

- **엔터프라이즈 관리자** – 대금 청구 계정 설정 및 액세스를 관리할 수 있습니다, 모든 비용을 볼 수 및 비용 구성을 관리할 수 있습니다. 예를 들어, 예산 및 내보냅니다. 함수에서 범위를 청구 EA 같습니다 [비용 관리 참가자 Azure RBAC 역할](../role-based-access-control/built-in-roles.md#cost-management-contributor)입니다.
- **Enterprise 읽기 전용 사용자** – 청구 계정 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예를 들어, 예산 및 내보냅니다. 함수에서 범위를 청구 EA와 동일 합니다 [비용 관리 판독기 Azure RBAC 역할](../role-based-access-control/built-in-roles.md#cost-management-reader)입니다.
- **부서 관리자** – 수 비용 센터 같은 부서 설정을 관리 및 액세스, 모든 비용 확인 및 관리할 수 비용 구성 합니다. 예를 들어, 예산 및 내보냅니다.  합니다 **DA 보기 요금** 청구 계정 설정 하려면 사용 해야 부서 관리자 및 읽기 전용 사용자에 대 한 비용을 확인 합니다. 하는 경우 **DA 보기 요금** 은 비활성화 부서의 사용자 경우 볼 수 없습니다 모든 수준에서 비용도 계정 또는 구독 소유자입니다.
- **부서 읽기 전용 사용자** – 부서 설정과 비용 데이터를 비용 구성을 볼 수 있습니다. 예를 들어, 예산 및 내보냅니다. 하는 경우 **DA 보기 요금** 은 비활성화 부서의 사용자 경우 볼 수 없습니다 모든 수준에서 비용도 계정 또는 구독 소유자입니다.
- **계정 소유자** – 수 있습니다 (예: 비용 센터) 등록 계정 설정을 관리, 모든 비용을 볼 구성 및 관리 비용 (예: 예산 및 내보내기) 등록 계정에 대 한 합니다. 합니다 **AO 보기 요금** 청구 계정 설정 하려면 사용 해야 계정 소유자 및 RBAC 사용자에 대 한 비용을 확인 합니다.

EA 청구 계정 사용자 청구서에 직접 액세스할 수 없는 합니다. 송장 외부 볼륨 라이선스 시스템에서에서 사용할 수 있습니다.

Azure 구독 등록 계정 아래에 중첩 됩니다. 요금 청구 사용자에는 구독 및 해당 범위는 리소스 그룹에 대 한 비용 데이터에 액세스할을 수 있습니다. 이러한를 보거나 Azure portal에서 리소스를 관리할 권한이 없습니다. 요금 청구 사용자로 이동 하 여 비용을 볼 수 있습니다 **Cost Management + 청구** 서비스 목록은 Azure portal에서. 그런 다음 특정 구독 및 리소스 그룹에 대해 보고 하는 데 필요한 비용을 필터링 할 수 있습니다.

특정 청구 계정에서 명시적으로 포함 되지 때문에 청구 사용자 관리 그룹에 액세스할 수 없는 합니다. 액세스 관리 그룹에 명시적으로 부여 되어야 합니다. 관리는 중첩 된 모든 구독에서 롤업 비용을 그룹화합니다. 그러나 구매 사용량 기준만 포함 됩니다. 이러한 예약 등 제 3 자 Marketplace 제품 구매를 포함 하지 않습니다. 이러한 비용을 보려면 EA 청구 계정을 사용 합니다.

## <a name="individual-agreement-pay-as-you-go-scopes"></a>각 계약 (종 량 제) 범위

종 량 제 (PAYG) 구독을 관련된 형식을 비롯 한 체험/평가판 등의 청구 계정 명시적 범위가 없는 개발/테스트 제품. 대신, 각 구독에는 계정 소유자 또는 EA 계정 소유자와 같은 계정 관리자에 있습니다.

- [**대금 청구 계정** ](../billing/billing-view-all-accounts.md) -하나 이상의 Azure 구독에 대 한 단일 계정 소유자를 나타냅니다. 현재 액세스 여러 사용자나 집계 비용 보기에 대 한 액세스 권한을 부여 지원 하지 않습니다.

    리소스 유형: 해당 없음

종 량 제 구독 계정 관리자가 보고에서 송장 및 지불 같은 청구 데이터를 관리 하는 [Azure 계정 센터](https://account.azure.com/subscriptions)합니다. 그러나 비용 데이터를 확인 하거나 Azure portal에서 리소스를 관리할 수는 없습니다. 계정 관리자에 대 한 액세스에 권한을 부여 하려면 앞에서 언급 한 Cost Management 역할을 사용 합니다.

EA와 달리 종 량 제 구독 계정 관리자는 Azure portal에서 청구서를 볼 수 있습니다. 비용 관리 판독기 및 비용 관리 참여자 역할 송장에 대 한 액세스를 제공 하지는 것을 염두에 두십시오. 자세한 내용은 [PAYG 송장에 대 한 액세스를 부여 하는 방법을](../billing/billing-manage-access.md#give-access-to-billing)합니다.

## <a name="customer-agreement-scopes"></a>고객 계약 범위

Microsoft 고객 계약 청구 계정에 다음과 같은 범위:

- **대금 청구 계정** -여러 Microsoft 제품 및 서비스에 대 한 고객 계약을 나타냅니다. 고객 계약 청구 계정을 EA 등록 기능이 없습니다. EA 등록 청구 프로필을 더욱 긴밀 하 게 정렬 됩니다.

    리소스 유형: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **청구 프로필** -청구서에 포함 된 구독을 정의 합니다. 청구 프로필은 청구서에 생성 되는 범위 이므로 EA 등록을 기능 해당 합니다. 마찬가지로, 사용량 기반 (예: Marketplace 및 예약) 하지 않은 구매는이 범위에서에 사용할 수 있습니다. 송장 섹션에 포함 되지 않습니다.

    리소스 유형: `Microsoft.Billing/billingAccounts/billingProfiles`

- **섹션 송장** -그룹 청구서 또는 청구 프로필에서 구독을 나타냅니다. 송장 섹션은 부서와 마찬가지로-여러 사람이 송장 섹션에 액세스할 수 있습니다.

    리소스 유형: `Microsoft.Billing/billingAccounts/invoiceSections`

EA와 달리 청구 범위 고객 계약 계정 청구 _는_ 단일 디렉터리에 바인딩되고 여러 Azure AD 디렉터리에서 구독을 사용할 수 없습니다.

고객 계약 청구 범위는 다음 역할을 지원합니다.

- **소유자** – 수 청구 설정 및 액세스 관리, 보기 모든 비용 및 관리 비용 구성 합니다. 예를 들어, 예산 및 내보냅니다. 함수에서이 고객 계약 범위를 청구와 동일 합니다 [비용 관리 참가자 Azure RBAC 역할](../role-based-access-control/built-in-roles.md#cost-management-contributor)입니다.
- **참가자** – 수 액세스를 제외한 청구 설정 관리, 보기 모든 비용 및 관리 비용 구성 합니다. 예를 들어, 예산 및 내보냅니다. 함수에서이 고객 계약 범위를 청구와 동일 합니다 [비용 관리 참가자 Azure RBAC 역할](../role-based-access-control/built-in-roles.md#cost-management-contributor)입니다.
- **판독기** – 청구 설정과 비용 데이터를 비용 구성을 볼 수 있습니다. 예를 들어, 예산 및 내보냅니다. 함수에서이 고객 계약 범위를 청구와 동일 합니다 [비용 관리 판독기 Azure RBAC 역할](../role-based-access-control/built-in-roles.md#cost-management-reader)입니다.
- **송장 manager** – 보기 및 청구서를 지불 하 수 구성과 데이터 비용 보기. 예를 들어, 예산 및 내보냅니다. 함수에서이 고객 계약 범위를 청구와 동일 합니다 [비용 관리 판독기 Azure RBAC 역할](../role-based-access-control/built-in-roles.md#cost-management-reader)입니다.
- **Azure 구독 작성자** – Azure 구독, 비용을 보고 만들고 관리할 수 있습니다 비용 구성 합니다. 예를 들어, 예산 및 내보냅니다. 함수에서이 고객 계약 청구 범위 EA 등록 계정 소유자 역할와 같습니다.

Azure 구독 EA 등록 계정으로 하는 방법을 같은 청구서 섹션 아래에 중첩 됩니다. 요금 청구 사용자에는 구독 및 해당 범위 아래에 있는 리소스 그룹에 대 한 비용 데이터에 액세스할을 수 있습니다. 그러나 되지 않은 액세스를 확인 하거나 Azure portal에서 리소스를 관리 합니다. 요금 청구 사용자로 이동 하 여 비용을 볼 수 있습니다 **Cost Management + 청구** 서비스 목록은 Azure portal에서. 그런 다음 특정 구독 및 리소스 그룹에 대해 보고 하는 데 필요한 비용을 필터링 합니다.

청구 액세스할 수 없는 관리 그룹에 있으므로 대금 청구 계정에 명시적으로 해당 하지 않습니다. 그러나 조직에 대 한 관리 그룹을 사용 하는 경우 모든 구독 비용은 롤업 대금 청구 계정에 루트 관리 그룹은 둘 다 단일 디렉터리에 제한 때문입니다. 관리 그룹에는 구매는 사용량 기반에 포함 됩니다. 예약 등 제 3 자 Marketplace 제품 구매는 관리 그룹에 포함 되지 않습니다. 따라서 대금 청구 계정 및 루트 관리 그룹을 다른 합계를 보고할 수 있습니다. 이러한 비용을 보려면 대금 청구 계정 또는 해당 청구 프로필을 사용 합니다.

## <a name="cloud-solution-provider-csp-scopes"></a>클라우드 솔루션 공급자 (CSP) 범위

클라우드 솔루션 공급자 (CSP) 파트너는 Cost Management에서 현재 지원 되지 않습니다. 대신, 사용할 수 있습니다 [파트너 센터](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)합니다.

## <a name="switch-between-scopes-in-cost-management"></a>Cost Management에서 범위 사이 전환

Azure portal의 비용 관리 보기를 모두 포함 된 **범위** 필 뷰의 왼쪽 위에 있는 합니다. 신속 하 게 범위를 변경 하려면 사용 합니다. 클릭 합니다 **범위** 필을 범위 선택기를 엽니다. 청구 계정, 루트 관리 그룹 및 루트 관리 그룹에 중첩 되지 않은 모든 구독을 보여 줍니다. 범위를 선택 하려면 강조 표시 한 다음 클릭 배경을 클릭 **선택** 맨 아래에 있습니다. 구독에서 리소스 그룹과 같은 중첩 된 범위에 드릴 범위 이름 링크를 클릭 합니다. 모든 중첩된 수준에서 부모 범위를 선택 하려면 클릭 **이 선택 &lt;범위&gt;**  범위 선택의 맨 위에 있는 합니다.

## <a name="identify-the-resource-id-for-a-scope"></a>범위에 대 한 리소스 ID를 식별 합니다.

비용 관리 Api로 작업 하는 경우 범위 반드시 알아야 합니다. 다음 정보를 사용 하 여 비용 관리 Api에 대 한 적절 한 범위 URI를 작성 합니다.

### <a name="billing-accounts"></a>청구 계정

1. Azure portal을 열고 다음 이동할 **Cost Management + 청구** 서비스 목록에서.
2. 선택 **속성** 청구 계정 메뉴에서.
3. 청구 계정 ID를 복사 합니다.
4. 범위는: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>청구 프로필

1. Azure portal을 열고 다음 이동할 **Cost Management + 청구** 서비스 목록에서.
2. 선택 **청구 프로필** 청구 계정 메뉴에서.
3. 원하는 청구 프로필의 이름을 클릭 합니다.
4. 선택 **속성** 청구 프로필 메뉴에 있습니다.
5. 대금 청구 계정 및 청구 프로필 Id를 복사 합니다.
6. 범위는: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>청구서 섹션

1. Azure portal을 열고 다음 이동할 **Cost Management + 청구** 서비스 목록에서.
2. 선택 **섹션에서는 송장** 청구 계정 메뉴에서.
3. 원하는 송장 섹션의 이름을 클릭 합니다.
4. 선택 **속성** 송장 섹션 메뉴에서.
5. 대금 청구 계정을 복사 하 고 Id 섹션 청구서를 보냅니다.
6. 범위는: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 부서

1. Azure portal을 열고 다음 이동할 **Cost Management + 청구** 서비스 목록에서.
2. 선택 **부서** 청구 계정 메뉴에서.
3. 원하는 부서 이름을 클릭 합니다.
4. 선택 **속성** 부서 메뉴에서.
5. 청구 계정 및 부서 Id를 복사 합니다.
6. 범위는: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 등록 계정

1. Azure portal을 열고 이동할 **Cost Management + 청구** 서비스 목록에서.
2. 선택 **등록 계정** 청구 계정 메뉴에서.
3. 원하는 등록 계정의 이름을 클릭 합니다.
4. 선택 **속성** 등록 계정 메뉴에서.
5. 대금 청구 계정 및 등록 계정 Id 복사 합니다.
6. 범위는: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>관리 그룹

1. Azure portal을 열고 이동할 **관리 그룹** 서비스 목록에서.
2. 원하는 관리 그룹으로 이동 합니다.
3. 테이블에서 관리 그룹 ID를 복사 합니다.
4. 범위는: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>구독

1. Azure portal을 열고 이동할 **구독** 서비스 목록에서.
2. 테이블에서 구독 ID를 복사 합니다.
3. 범위는: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>리소스 그룹

1. Azure portal을 열고 이동할 **리소스 그룹** 서비스 목록에서.
2. 원하는 리소스 그룹의 이름을 클릭 합니다.
3. 선택 **속성** 리소스 그룹 메뉴에 있습니다.
4. 리소스 ID 필드 값을 복사 합니다.
5. 범위는: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management는 현재에서 지원 됩니다 [Azure Global](https://management.azure.com) 하 고 [Azure Government](https://management.usgovcloudapi.net)합니다. Azure Government에 대 한 자세한 내용은 참조 하세요. [Azure Global 및 Government API 끝점](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_합니다._

## <a name="next-steps"></a>다음 단계

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
