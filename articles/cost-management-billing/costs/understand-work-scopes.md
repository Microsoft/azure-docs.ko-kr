---
title: Azure Cost Management 범위 이해 및 사용
description: 이 문서는 Azure에서 사용할 수 있는 청구 및 리소스 관리 범위를 이해 하 고 Cost Management 및 Api에서 범위를 사용 하는 방법을 설명 합니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 58bd1d3e3fb27344706b23866a68c7e1363e7ec2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990139"
---
# <a name="understand-and-work-with-scopes"></a>범위 이해 및 작업

이 문서는 Azure에서 사용할 수 있는 청구 및 리소스 관리 범위를 이해 하 고 Cost Management 및 Api에서 범위를 사용 하는 방법을 설명 합니다.

## <a name="scopes"></a>범위

_범위_ 는 azure AD 사용자가 서비스에 액세스 하 고 관리 하는 azure 리소스 계층 구조에 있는 노드입니다. 대부분의 Azure 리소스는 구독에 포함 된 리소스 그룹에 생성 되 고 배포 됩니다. Microsoft는 또한 청구 데이터를 관리 하는 특수 한 역할이 있는 Azure 구독 위의 두 계층 구조를 제공 합니다.
- 지불, 송장 등의 청구 데이터
- 비용 및 정책 관리와 같은 클라우드 서비스

범위는 청구 데이터를 관리 하 고, 지불액과 관련 된 역할이 있으며, 송장을 보고, 일반 계정 관리를 수행 하는 위치입니다. 청구 및 계정 역할은 [AZURE RBAC](../../role-based-access-control/overview.md)를 사용 하는 리소스 관리에 사용 되는 것과는 별도로 관리 됩니다. 액세스 제어의 차이점을 포함 하 여 별도의 범위를 명확 하 게 구분 하기 위해 이러한 범위를 각각 _청구 범위_ 및 _RBAC 범위_라고 합니다.

## <a name="how-cost-management-uses-scopes"></a>Cost Management 범위를 사용 하는 방법

Cost Management는 모든 리소스 범위에서 작동 하 여 조직에서 액세스 권한이 있는 수준 (전체 청구 계정 또는 단일 리소스 그룹)에 대 한 비용을 관리할 수 있도록 합니다. 청구 범위는 Microsoft 계약 (구독 유형)에 따라 다르지만 RBAC 범위는 그렇지 않습니다.

## <a name="azure-rbac-scopes"></a>Azure RBAC 범위

Azure는 리소스 관리를 위해 세 가지 범위를 지원 합니다. 각 범위는 비용 관리를 비롯 하 여 액세스 및 관리 관리를 지원 합니다.

- [**관리 그룹**](../../governance/management-groups/overview.md) -최대 8 수준의 계층 컨테이너는 Azure 구독을 구성 합니다.

    리소스 종류: [Microsoft Management/managementGroups](/rest/api/resources/managementgroups)

- **구독** -Azure 리소스에 대 한 기본 컨테이너입니다.

    리소스 종류: [Microsoft .resources/subscription](/rest/api/resources/subscriptions)

- [**리소스 그룹**](../../azure-resource-manager/management/overview.md#resource-groups) -동일한 수명 주기를 공유 하는 Azure 솔루션에 대 한 관련 리소스의 논리적 그룹화입니다. 배포 하 고 함께 삭제 되는 리소스를 예로 들 수 있습니다.

    리소스 종류: [Microsoft .resources/subscription/resourceGroups](/rest/api/resources/resourcegroups)

관리 그룹을 사용 하 여 구독을 계층 구조로 구성할 수 있습니다. 예를 들어 관리 그룹을 사용 하 여 논리적 조직 계층 구조를 만들 수 있습니다. 그런 다음 프로덕션 및 개발/테스트 워크 로드에 대 한 팀 구독을 제공 합니다. 그런 다음 구독에서 리소스 그룹을 만들어 각 하위 시스템 또는 구성 요소를 관리 합니다.

조직 계층 구조를 만들면 비용 및 정책 준수 롤업 조직이이 가능 합니다. 그런 다음 각 리더가 현재 비용을 보고 분석할 수 있습니다. 그런 다음, 예산을 만들어 잘못 된 지출 패턴을 연석 하 고 가장 낮은 수준에서 Advisor 권장 사항을 사용 하 여 비용을 최적화할 수 있습니다.

비용을 볼 수 있는 권한을 부여 하 고, 필요에 따라 예산 및 내보내기와 같은 비용 구성을 관리 하려면 Azure RBAC를 사용 하 여 거 버 넌 스 범위에서 수행 해야 합니다. Azure RBAC를 사용 하 여 특정 범위의 역할에 정의 된 미리 정의 된 작업 집합을 수행할 수 있는 액세스 권한을 Azure AD 사용자 및 그룹에 부여 합니다. 예를 들어 관리 그룹 범위에 할당 된 역할은 중첩 된 구독 및 리소스 그룹에도 동일한 사용 권한을 부여 합니다.

Cost Management는 다음과 같은 각 범위에 대해 다음과 같은 기본 제공 역할을 지원 합니다.

- [**소유자**](../../role-based-access-control/built-in-roles.md#owner) – 비용 구성을 포함 하 여 비용을 보고 모든 항목을 관리할 수 있습니다.
- [**참가자**](../../role-based-access-control/built-in-roles.md#contributor) – 비용 구성을 비롯 하 여 비용을 보고, 액세스 제어를 제외 하 고 모든 것을 관리할 수 있습니다.
- [**읽기 권한자**](../../role-based-access-control/built-in-roles.md#reader) – 비용 데이터 및 구성을 포함 하 여 모든 항목을 볼 수 있지만 변경할 수는 없습니다.
- [**Cost Management 참여자**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – 비용을 보고, 비용 구성을 관리 하 고, 권장 사항을 볼 수 있습니다.
- [**Cost Management 판독기**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – 비용 데이터, 비용 구성 및 보기 권장 사항을 볼 수 있습니다.

Cost Management 참가자가 권장 되는 최소 권한 역할입니다. 이를 통해 사용자는 예산 및 내보내기를 만들고 관리 하 여 비용을 보다 효과적으로 모니터링 하 고 보고할 수 있습니다. Cost Management 기여자는 종단 간 비용 관리 시나리오를 지원 하기 위해 추가 역할이 필요할 수도 있습니다. 다음과 같은 시나리오를 고려해 보세요.

- **예산을 초과 하는 경우에는** 작업을 수행 하는 데 필요한 작업 그룹을 만들거나 관리 하기 위해 작업 그룹을 만들거나 관리 하는 데 필요한 액세스 권한이 필요 Cost Management. 예산 임계값을 초과할 때 사용할 작업 그룹을 포함 하는 리소스 그룹에 [모니터링 참가자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 를 부여 하는 것이 좋습니다. 특정 작업을 자동화 하려면 자동화 및 Azure Functions와 같은 사용 되는 특정 서비스에 대 한 추가 역할이 필요 합니다.
- **비용 데이터 내보내기 일정** Cost Management – 저장소 계정에 데이터를 복사 하는 내보내기를 예약 하기 위해 저장소 계정을 관리 하기 위한 액세스 권한도 필요 합니다. 비용 데이터를 내보내는 저장소 계정이 포함 된 리소스 그룹에 [저장소 계정 참가자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 를 부여 하는 것이 좋습니다.
- **비용 절약 권장 사항 보기** – Cost Management 판독기 및 Cost Management 참가자는 기본적으로 비용 권장 사항을 *볼* 수 있는 권한이 있습니다. 그러나 비용 권장 사항에 대 한 작업에 액세스 하려면 개별 리소스에 대 한 액세스 권한이 있어야 합니다. 비용 기반 권장 사항에 대 한 작업을 수행 하려면 [서비스별 역할](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions) 을 부여 하는 것이 좋습니다.

## <a name="enterprise-agreement-scopes"></a>기업계약 범위

등록 라고도 하는 EA (기업계약) 청구 계정의 범위는 다음과 같습니다.

- [**청구 계정**](../manage/view-all-accounts.md) -EA 등록을 나타냅니다. 이 범위에서 송장이 생성 됩니다. Marketplace 및 예약과 같이 사용량을 기반으로 하지 않는 구매는이 범위 에서만 사용할 수 있습니다. 부서 또는 등록 계정에 표시 되지 않습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **부서** -등록 계정의 선택적 그룹화입니다.

    리소스 종류: `Billing/billingAccounts/departments`

- **등록 계정** -단일 계정 소유자를 나타냅니다. 는 여러 사용자에 게 액세스 권한을 부여 하는 것을 지원 하지 않습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

거 버 넌 스 범위는 단일 디렉터리에 바인딩되어 있지만 EA 청구 범위는 그렇지 않습니다. EA 청구 계정에는 여러 Azure AD 디렉터리의 구독이 있을 수 있습니다.

EA 청구 범위는 다음 역할을 지원 합니다.

- **엔터프라이즈 관리자** – 청구 계정 설정 및 액세스를 관리 하 고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서 EA 청구 범위는 [참여자 AZURE RBAC 역할 Cost Management](../../role-based-access-control/built-in-roles.md#cost-management-contributor)와 동일 합니다.
- **Enterprise 읽기 전용 사용자** – 청구 계정 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서 EA 청구 범위는 [Cost Management 판독기 AZURE RBAC 역할과](../../role-based-access-control/built-in-roles.md#cost-management-reader)동일 합니다.
- **부서 관리자** – 비용 센터와 같은 부서 설정을 관리 하 고, 액세스 하 고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예를 들어 예산 및 내보내기입니다.  부서 관리자 및 읽기 전용 사용자가 비용을 볼 수 있도록 **DA 보기 요금** 청구 계정 설정을 사용 하도록 설정 해야 합니다. **DA 보기 요금이** 사용 하지 않도록 설정 된 경우 부서 사용자는 계정이 나 구독 소유자 인 경우에도 어떤 수준 에서도 비용을 볼 수 없습니다.
- **부서 읽기 전용 사용자** – 부서 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예를 들어 예산 및 내보내기입니다. **DA 보기 요금이** 사용 하지 않도록 설정 된 경우 부서 사용자는 계정이 나 구독 소유자 인 경우에도 어떤 수준 에서도 비용을 볼 수 없습니다.
- **계정 소유자** – 등록 계정 설정 (예: 비용 센터)을 관리 하 고, 모든 비용을 보고, 등록 계정에 대 한 비용 구성 (예: 예산 및 내보내기)을 관리할 수 있습니다. 계정 소유자 및 RBAC 사용자가 비용을 볼 수 있도록 **AO 보기 요금** 청구 계정 설정을 사용 하도록 설정 해야 합니다.

EA 청구 계정 사용자는 청구서에 직접 액세스할 수 없습니다. 청구서는 외부 볼륨 라이선스 시스템에서 사용할 수 있습니다.

Azure 구독은 등록 계정 아래에 중첩 됩니다. 청구 사용자는 해당 범위에 속하는 구독 및 리소스 그룹에 대 한 비용 데이터에 액세스할 수 있습니다. Azure Portal에서 리소스를 보거나 관리할 수 있는 액세스 권한이 없습니다. 청구 사용자는 Azure Portal 서비스 목록에서 **Cost Management + 청구** 로 이동 하 여 비용을 볼 수 있습니다. 그런 다음 보고 해야 하는 특정 구독 및 리소스 그룹에 대 한 비용을 필터링 할 수 있습니다.

청구 사용자는 특정 청구 계정에 명시적으로 포함 되지 않기 때문에 관리 그룹에 대 한 액세스 권한이 없습니다. 관리 그룹에 명시적으로 액세스 권한을 부여 해야 합니다. 관리 그룹은 중첩 된 모든 구독에서 비용을 롤업 합니다. 그러나 사용량 기반 구매가 포함 됩니다. 예약 및 타사 Marketplace 제공과 같은 구매는 포함 되지 않습니다. 이러한 비용을 확인 하려면 EA 청구 계정을 사용 합니다.

## <a name="individual-agreement-scopes"></a>개별 계약 범위

종 량 제 및 무료 평가판, 개발/테스트 제품 등의 관련 유형과 같은 개별 제품에서 만든 Azure 구독은 명시적인 청구 계정 범위를 갖지 않습니다. 대신 각 구독에는 EA 계정 소유자와 같은 계정 소유자 또는 계정 관리자가 있습니다.

- [**청구 계정**](../manage/view-all-accounts.md) -하나 이상의 Azure 구독에 대 한 단일 계정 소유자를 나타냅니다. 현재는 여러 사용자에 대 한 액세스 권한 부여 또는 집계 된 비용 보기에 대 한 액세스를 지원 하지 않습니다.

    리소스 종류: 해당 사항 없음

개별 Azure 구독 계정 관리자는 [Azure 계정 센터](https://account.azure.com/subscriptions)에서 송장, 지불 등의 청구 데이터를 보고 관리할 수 있습니다. 그러나 Azure Portal에서 비용 데이터를 보거나 리소스를 관리할 수 없습니다. 계정 관리자에 게 액세스 권한을 부여 하려면 앞에서 언급 한 Cost Management 역할을 사용 합니다.

EA와 달리 개별 Azure 구독 계정 관리자는 Azure Portal에서 송장을 볼 수 있습니다. Cost Management 판독기와 Cost Management 참가자 역할은 청구서에 대 한 액세스 권한을 제공 하지 않습니다. 자세한 내용은 [청구서에 대 한 액세스 권한을 부여 하는 방법](../manage/manage-billing-access.md#give-read-only-access-to-billing)을 참조 하세요.

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft 고객 계약 범위

Microsoft 고객 계약 청구 계정의 범위는 다음과 같습니다.

- **청구 계정** -여러 Microsoft 제품 및 서비스에 대 한 고객 규약을 나타냅니다. 고객 계약 청구 계정은 EA 등록와 기능적으로 동일 하지 않습니다. EA 등록는 청구 프로필에 더 가깝게 정렬 됩니다.

    리소스 종류: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **청구 프로필** -청구서에 포함 되는 구독을 정의 합니다. 청구 프로필은에서 송장이 생성 되는 범위 이므로 EA 등록과 동등한 기능입니다. 마찬가지로 사용 하지 않는 구매 (예: 마켓플레이스 및 예약)는이 범위 에서만 사용할 수 있습니다. 송장 섹션에는 포함 되지 않습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/billingProfiles`

- **송장 섹션** -송장 또는 청구 프로필의 구독 그룹을 나타냅니다. 청구서 섹션은 부서와 유사 하며, 여러 사람이 청구서 섹션에 액세스할 수 있습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/invoiceSections`

- **고객** -파트너에 의해 Microsoft 고객 계약에 등록 특정 고객에 연결 된 구독 그룹을 나타냅니다. 이 범위는 CSP와 관련이 있습니다.

EA 청구 범위와 달리 고객 계약 청구 계정은 단일 디렉터리에 바인딩되고 여러 Azure AD 디렉터리에 _구독을 가질_ 수 없습니다.

파트너에 게는 고객 계약 청구 범위가 적용 되지 않습니다. 파트너 역할 및 사용 권한은 [사용자 할당 역할 및 사용 권한](/partner-center/permissions-overview)에 설명 되어 있습니다.

고객 계약 청구 범위는 다음 역할을 지원 합니다.

- **소유자** – 청구 설정 및 액세스를 관리 하 고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서이 고객 계약 청구 범위는 [Cost Management 참여자 AZURE RBAC 역할과](../../role-based-access-control/built-in-roles.md#cost-management-contributor)동일 합니다.
- **참여자** – 액세스를 제외한 요금 청구 설정을 관리 하 고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서이 고객 계약 청구 범위는 [Cost Management 참여자 AZURE RBAC 역할과](../../role-based-access-control/built-in-roles.md#cost-management-contributor)동일 합니다.
- **읽기 권한자** – 청구 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서이 고객 계약 청구 범위는 [Cost Management 판독기 AZURE RBAC 역할과](../../role-based-access-control/built-in-roles.md#cost-management-reader)동일 합니다.
- **송장 관리자** – 청구서를 보고 지불 하며 비용 데이터 및 구성을 볼 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서이 고객 계약 청구 범위는 [Cost Management 판독기 AZURE RBAC 역할과](../../role-based-access-control/built-in-roles.md#cost-management-reader)동일 합니다.
- **Azure 구독 작성자** – azure 구독을 만들고, 비용을 보고, 비용 구성을 관리할 수 있습니다. 예를 들어 예산 및 내보내기입니다. 함수에서이 고객 계약 청구 범위는 EA 등록 계정 소유자 역할과 동일 합니다.

Azure 구독은 EA 등록 계정에 있는 방법과 같이 청구서 섹션 아래에 중첩 됩니다. 청구 사용자는 해당 범위에 속하는 구독 및 리소스 그룹에 대 한 비용 데이터에 액세스할 수 있습니다. 그러나 Azure Portal에서 리소스를 보거나 관리할 수 있는 권한은 없습니다. 청구 사용자는 Azure Portal 서비스 목록에서 **Cost Management + 청구** 로 이동 하 여 비용을 볼 수 있습니다. 그런 다음 보고 해야 하는 특정 구독 및 리소스 그룹에 대 한 비용을 필터링 합니다.

청구 사용자는 청구 계정에 명시적으로 포함 되지 않으므로 관리 그룹에 대 한 액세스 권한이 없습니다. 그러나 조직에 대해 관리 그룹을 사용 하는 경우 모든 구독 비용은 모두 단일 디렉터리로 제한 되므로 청구 계정 및 루트 관리 그룹에 롤업 됩니다. 관리 그룹에는 사용량 기반 구매가 포함 됩니다. 예약 및 타사 Marketplace 제공과 같은 구매는 관리 그룹에 포함 되지 않습니다. 따라서 청구 계정 및 루트 관리 그룹은 다른 합계를 보고할 수 있습니다. 이러한 비용을 보려면 청구 계정 또는 해당 청구 프로필을 사용 합니다.

## <a name="aws-scopes"></a>AWS 범위

AWS 통합이 완료 된 후에는 [AWS integration 설정 및 구성](aws-integration-set-up-configure.md)을 참조 하세요. 사용할 수 있는 범위는 다음과 같습니다.

- **외부 청구 계정** -타사 공급 업체와 고객 계약을 나타냅니다. EA 청구 계정과 유사 합니다.

    리소스 종류: `Microsoft.CostManagement/externalBillingAccounts`

- **외부 구독** -타사 공급 업체를 사용 하는 고객 운영 계정을 나타냅니다. 이는 Azure 구독과 유사 합니다.

    리소스 종류: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>CSP (클라우드 솔루션 공급자) 범위

Microsoft 고객 계약을 체결 한 고객의 Csp에 대해 지원 되는 범위는 다음과 같습니다.

- **청구 계정** -여러 Microsoft 제품 및 서비스에 대 한 고객 규약을 나타냅니다. 고객 계약 청구 계정은 EA 등록와 기능적으로 동일 하지 않습니다. EA 등록는 청구 프로필에 더 가깝게 정렬 됩니다.

    리소스 종류: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **청구 프로필** -청구서에 포함 되는 구독을 정의 합니다. 청구 프로필은에서 송장이 생성 되는 범위 이므로 EA 등록과 동등한 기능입니다. 마찬가지로 사용 하지 않는 구매 (예: 마켓플레이스 및 예약)는이 범위 에서만 사용할 수 있습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/billingProfiles`

- **고객** -파트너에 의해 Microsoft 고객 계약에 등록 특정 고객에 연결 된 구독 그룹을 나타냅니다.

*전역 관리자* 및 *관리 에이전트* 역할이 있는 사용자만 파트너의 Azure 테넌트에서 직접 청구 계정, 청구 프로필 및 고객에 대 한 비용을 관리 하 고 볼 수 있습니다. 파트너 센터 역할에 대 한 자세한 내용은 [사용자 역할 및 사용 권한 할당](/partner-center/permissions-overview)을 참조 하세요.

Azure Cost Management는 고객이 Microsoft 고객 계약을 체결 한 경우에만 CSP 파트너 고객만 지원 합니다. 아직 Microsoft 고객 계약에 없는 CSP 지원 고객은 [파트너 센터](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)를 참조 하세요.

## <a name="switch-between-scopes-in-cost-management"></a>Cost Management 범위 간 전환

Azure Portal의 모든 Cost Management 보기에는 뷰의 왼쪽 위에 약 **범위** 선택이 포함 됩니다. 이를 사용 하 여 범위를 신속 하 게 변경 합니다. **범위 약를** 클릭 하 여 범위 선택기를 엽니다. 여기에는 청구 계정, 루트 관리 그룹 및 루트 관리 그룹에 중첩 되지 않은 구독이 표시 됩니다. 범위를 선택 하려면 배경을 클릭 하 여 강조 표시 하 고 맨 아래에 있는 **선택** 을 클릭 합니다. 구독에서 리소스 그룹과 같은 중첩 된 범위에 대 한 드릴을 하려면 범위 이름 링크를 클릭 합니다. 중첩 된 수준에서 부모 범위를 선택 하려면 범위 선택의 위쪽에서 **이 &lt;범위&gt;선택** 을 클릭 합니다.

## <a name="identify-the-resource-id-for-a-scope"></a>범위에 대 한 리소스 ID를 식별 합니다.

Cost Management Api를 사용 하는 경우 범위를 아는 것이 중요 합니다. 다음 정보를 사용 하 여 Cost Management Api에 대 한 적절 한 범위 URI를 빌드합니다.

### <a name="billing-accounts"></a>청구 계정

1. Azure Portal 연 다음 서비스 목록에서 **Cost Management + 청구** 로 이동 합니다.
2. 청구 계정 메뉴에서 **속성** 을 선택 합니다.
3. 청구 계정 ID를 복사 합니다.
4. 범위: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>청구 프로필

1. Azure Portal 연 다음 서비스 목록에서 **Cost Management + 청구** 로 이동 합니다.
2. 청구 계정 메뉴에서 **청구 프로필** 을 선택 합니다.
3. 원하는 청구 프로필의 이름을 클릭 합니다.
4. 청구 프로필 메뉴에서 **속성** 을 선택 합니다.
5. 청구 계정 및 청구 프로필 Id를 복사 합니다.
6. 범위: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>청구서 섹션

1. Azure Portal 연 다음 서비스 목록에서 **Cost Management + 청구** 로 이동 합니다.
2. 청구 계정 메뉴에서 **송장 섹션** 을 선택 합니다.
3. 원하는 청구서 섹션의 이름을 클릭 합니다.
4. 송장 섹션 메뉴에서 **속성** 을 선택 합니다.
5. 청구 계정 및 청구서 섹션 Id를 복사 합니다.
6. 범위: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 부서

1. Azure Portal 연 다음 서비스 목록에서 **Cost Management + 청구** 로 이동 합니다.
2. 청구 계정 메뉴에서 **부서** 를 선택 합니다.
3. 원하는 부서의 이름을 클릭 합니다.
4. 부서 메뉴에서 **속성** 을 선택 합니다.
5. 청구 계정 및 부서 Id를 복사 합니다.
6. 범위: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 등록 계정

1. Azure Portal를 열고 서비스 목록에서 **Cost Management + 청구** 로 이동 합니다.
2. 청구 계정 메뉴에서 **등록 계정** 을 선택 합니다.
3. 원하는 등록 계정의 이름을 클릭 합니다.
4. 등록 계정 메뉴에서 **속성** 을 선택 합니다.
5. 청구 계정 및 등록 계정 Id를 복사 합니다.
6. 범위: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>관리 그룹

1. Azure Portal를 열고 서비스 목록에서 **관리 그룹** 으로 이동 합니다.
2. 원하는 관리 그룹으로 이동 합니다.
3. 테이블에서 관리 그룹 ID를 복사 합니다.
4. 범위: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscription

1. Azure Portal를 열고 서비스 목록에서 **구독** 으로 이동 합니다.
2. 테이블에서 구독 ID를 복사 합니다.
3. 범위: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>리소스 그룹

1. Azure Portal를 열고 서비스 목록에서 **리소스 그룹** 으로 이동 합니다.
2. 원하는 리소스 그룹의 이름을 클릭 합니다.
3. 리소스 그룹 메뉴에서 **속성** 을 선택 합니다.
4. 리소스 ID 필드 값을 복사 합니다.
5. 범위: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management는 현재 [Azure Global](https://management.azure.com) 및 [Azure Government](https://management.usgovcloudapi.net)에서 지원 됩니다. Azure Government에 대 한 자세한 내용은 [Azure 전역 및 정부 API 엔드포인트](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
