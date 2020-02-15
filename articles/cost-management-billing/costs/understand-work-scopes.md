---
title: Azure Cost Management 범위의 이해 및 사용
description: 이 문서에서는 Azure에서 사용할 수 있는 청구 및 리소스 관리 범위와 Cost Management 및 API에서 범위를 사용하는 방법을 설명합니다.
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "75990139"
---
# <a name="understand-and-work-with-scopes"></a>범위 이해 및 작업

이 문서에서는 Azure에서 사용할 수 있는 청구 및 리소스 관리 범위와 Cost Management 및 API에서 범위를 사용하는 방법을 설명합니다.

## <a name="scopes"></a>범위

_범위_는 Azure AD 사용자가 서비스에 액세스하고 관리하는 Azure 리소스 계층 구조의 노드입니다. 대부분의 Azure 리소스는 구독에 포함된 리소스 그룹에 생성되고 배포됩니다. 또한 Microsoft는 청구 데이터를 관리하는 특수 역할이 있는 Azure 구독보다 높은 다음 두 가지 계층 구조를 제공합니다.
- 결제 및 송장 같은 청구 데이터
- 비용 및 정책 거버넌스 같은 클라우드 서비스

범위는 청구 데이터를 관리하고, 결제 관련 역할을 수행하고, 청구서를 살펴보고, 일반 계정 관리를 수행하는 위치입니다. 청구 및 계정 역할은 리소스 관리에 사용되는 역할과는 별도로 관리되며, [Azure RBAC](../../role-based-access-control/overview.md)를 사용합니다. 액세스 제어의 차이점을 포함하여 별도 범위의 의도를 명확하게 구분하기 위해 이러한 범위를 각각 _요금 청구 범위_ 및 _RBAC 범위_라고 합니다.

## <a name="how-cost-management-uses-scopes"></a>Cost Management에서 범위를 사용하는 방법

Cost Management는 리소스 위의 모든 범위에서 작동하여 조직이 액세스 권한을 갖고 있는 수준(전체 청구 계정 또는 단일 리소스 그룹)에서 비용을 관리할 수 있게 해줍니다. 청구 범위는 Microsoft 계약(구독 유형)에 따라 달라지지만 RBAC 범위는 그렇지 않습니다.

## <a name="azure-rbac-scopes"></a>Azure RBAC 범위

Azure는 리소스 관리를 위해 세 가지 범위를 지원합니다. 각 범위는 비용 관리를 포함하여(이에 국한되지 않음) 액세스 및 거버넌스 관리를 지원합니다.

- [**관리 그룹**](../../governance/management-groups/overview.md) - 최대 8개 수준의 계층 컨테이너이며 Azure 구독을 구성합니다.

    리소스 유형: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **구독** - Azure 리소스의 기본 컨테이너입니다.

    리소스 유형: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**리소스 그룹**](../../azure-resource-manager/management/overview.md#resource-groups) - 동일한 수명 주기를 공유하는 Azure 솔루션에 대한 관련 리소스의 논리적 그룹화입니다. 함께 배포되고 삭제되는 리소스를 예로 들 수 있습니다.

    리소스 유형: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

관리 그룹을 사용하여 구독을 계층 구조로 구성할 수 있습니다. 예를 들어 관리 그룹을 사용하여 논리적 조직 계층 구조를 만들 수 있습니다. 그리고 프로덕션 및 개발/테스트 워크로드에 대한 팀 구독을 제공합니다. 그런 다음, 구독에서 각 하위 시스템 또는 구성 요소를 관리할 리소스 그룹을 만듭니다.

조직 계층 구조를 만들면 비용 및 정책 규정 준수를 조직적으로 롤업할 수 있습니다. 그런 다음, 각 책임자가 현재 비용을 살펴보고 분석할 수 있습니다. 그 후에는 예산을 작성하여 잘못된 지출 패턴을 제한하고 가장 낮은 수준에서 Advisor 추천에 따라 비용을 최적화할 수 있습니다.

비용을 살펴보고 필요에 따라 예산 및 내보내기와 같은 비용 구성을 관리할 수 있는 액세스 권한은 Azure RBAC를 사용하여 거버넌스 범위에서 부여합니다. Azure RBAC를 사용하여 특정 범위 이하의 역할에 정의되어 있는 미리 정의된 작업 세트를 수행할 수 있는 액세스 권한을 Azure AD 사용자 및 그룹에 부여합니다. 예를 들어 관리 그룹 범위에 할당된 역할은 중첩된 구독 및 리소스 그룹에도 동일한 권한을 부여합니다.

Cost Management는 아래의 각 범위에서 다음과 같은 기본 제공 역할을 지원합니다.

- [**소유자**](../../role-based-access-control/built-in-roles.md#owner) - 비용을 보고 비용 구성을 포함한 모든 항목을 관리할 수 있습니다.
- [**기여자**](../../role-based-access-control/built-in-roles.md#contributor) - 비용을 보고 비용 구성을 포함한 모든 항목(액세스 제어 제외)을 관리할 수 있습니다.
- [**읽기 권한자**](../../role-based-access-control/built-in-roles.md#reader) - 비용 데이터 및 구성을 포함하여 모든 항목을 볼 수 있지만 변경할 수는 없습니다.
- [**Cost Management 기여자**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – 비용을 보고, 비용 구성을 관리하고, 추천을 볼 수 있습니다.
- [**Cost Management 읽기 권한자**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – 비용 데이터, 비용 구성 및 추천을 볼 수 있습니다.

최소 권한 역할로 Cost Management 기여자를 사용할 것을 권장합니다. 이 역할은 사람들이 비용을 보다 효과적으로 모니터링하고 보고할 수 있도록 예산 및 내보내기를 만들고 관리하는 액세스 권한을 부여합니다. Cost Management 기여자는 엔드투엔드 비용 관리 시나리오를 지원하기 위해 추가 역할이 필요할 수도 있습니다. 다음과 같은 시나리오를 고려해 보세요.

- **예산 초과 시 조치** – Cost Management 기여자는 초과분에 자동으로 대응할 수 있도록 작업 그룹을 만들고/만들거나 관리하기 위한 액세스 권한도 필요합니다. 예산 임계값을 초과할 때 사용할 작업 그룹을 포함하고 있는 리소스 그룹에 [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor)를 부여하는 방안을 고려해 보세요. 특정 작업을 자동화하려면 Automation 및 Azure Functions처럼 사용되는 특정 서비스에 대한 추가 역할이 필요합니다.
- **비용 데이터 내보내기 예약** Cost Management 기여자는 데이터를 스토리지 계정에 복사하는 내보내기를 예약할 수 있도록 스토리지 계정을 관리하기 위한 액세스 권한도 필요합니다. 비용 데이터를 내보내는 스토리지 계정을 포함하고 있는 리소스 그룹에 [스토리지 계정 기여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor)를 부여하는 방안을 고려해 보세요.
- **비용 절감 추천 보기** – Cost Management 읽기 권한자 및 Cost Management 기여자는 기본적으로 비용 추천을 *볼 수 있습니다*. 그러나 비용 추천에 따라 조치를 취하려면 개별 리소스에 대한 액세스 권한이 필요합니다. 비용 기반 추천에 따라 조치를 취하려면 [서비스별 역할](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions)을 부여하는 방안을 고려해 보세요.

## <a name="enterprise-agreement-scopes"></a>기업계약 범위

등록이라고도 하는 EA(기업계약) 청구 계정의 범위는 다음과 같습니다.

- [**청구 계정**](../manage/view-all-accounts.md) - EA 등록을 나타냅니다. 이 범위에서 청구서가 생성됩니다. Marketplace 및 예약처럼 사용량 기반이 아닌 구매는 이 범위에서만 사용할 수 있습니다. 부서 또는 등록 계정에 표시되지 않습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **부서** - 등록 계정의 선택적 그룹화입니다.

    리소스 종류: `Billing/billingAccounts/departments`

- **등록 계정** - 단일 계정 소유자를 나타냅니다. 여러 사람에게 액세스 권한을 부여할 수 없습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

거버넌스 범위는 단일 디렉터리에 바인딩되지만, EA 청구 범위는 그렇지 않습니다. EA 청구 계정의 경우 여러 Azure AD 디렉터리에 걸쳐 여러 구독이 있을 수 있습니다.

EA 청구 범위는 다음 역할을 지원합니다.

- **엔터프라이즈 관리자** – 청구 계정 설정 및 액세스 권한을 관리하고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 EA 청구 범위는 [Cost Management 기여자 Azure RBAC 역할](../../role-based-access-control/built-in-roles.md#cost-management-contributor)과 동일합니다.
- **엔터프라이즈 읽기 전용 사용자** – 청구 계정 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 EA 청구 범위는 [Cost Management 읽기 권한자 Azure RBAC 역할](../../role-based-access-control/built-in-roles.md#cost-management-reader)과 동일합니다.
- **부서 관리자** – 비용 센터와 같은 부서 설정을 관리하고, 모든 비용에 액세스하여 살펴보고, 비용 구성을 관리할 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다.  부서 관리자 및 읽기 전용 사용자가 비용을 볼 수 있도록 **DA 요금 보기** 청구 계정 설정을 사용하도록 설정해야 합니다. **DA 요금 보기**를 사용하지 않으면 부서 사용자는 계정 또는 구독 소유자인 경우에도 어떤 수준에서도 비용을 볼 수 없습니다.
- **부서 읽기 전용 사용자** – 부서 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. **DA 요금 보기**를 사용하지 않으면 부서 사용자는 계정 또는 구독 소유자인 경우에도 어떤 수준에서도 비용을 볼 수 없습니다.
- **계정 소유자** – 등록 계정 설정(예: 비용 센터)을 관리하고, 모든 비용을 보고, 등록 계정의 비용 구성(예: 예산 및 내보내기)을 관리할 수 있습니다. 계정 소유자 및 RBAC 사용자가 비용을 볼 수 있도록 **AO 요금 보기** 청구 계정 설정을 사용하도록 설정해야 합니다.

EA 청구 계정 사용자는 청구서에 직접 액세스할 수 없습니다. 청구서는 외부 볼륨 라이선스 시스템에서 사용할 수 있습니다.

Azure 구독은 등록 계정 아래에 중첩됩니다. 청구 사용자는 각각 해당 범위에 속하는 구독 및 리소스 그룹의 비용 데이터에 액세스할 수 있습니다. Azure Portal에서 리소스를 보거나 관리할 수 있는 액세스 권한은 없습니다. 청구 사용자는 Azure Portal 서비스 목록에서 **Cost Management + 청구**로 이동하여 비용을 볼 수 있습니다. 그런 다음, 보고해야 하는 특정 구독 및 리소스 그룹으로 비용을 필터링할 수 있습니다.

청구 사용자는 특정 청구 계정에 명시적으로 포함되지 않기 때문에 관리 그룹에 대한 액세스 권한이 없습니다. 관리 그룹에 대한 액세스 권한을 명시적으로 부여해야 합니다. 관리 그룹은 중첩된 모든 구독의 비용을 롤업합니다. 그러나 여기에는 사용량 기반 구매만 포함됩니다. 예약 및 타사 Marketplace 제품과 같은 구매는 포함되지 않습니다. 이러한 비용을 확인하려면 EA 청구 계정을 사용합니다.

## <a name="individual-agreement-scopes"></a>개별 계약 범위

종량제 같은 개별 제품과 평가판 및 개발/테스트 제품 같은 관련 유형에서 만들어진 Azure 구독은 명시적인 청구 계정 범위가 없습니다. 그 대신, 각 구독에는 EA 계정 소유자와 같은 계정 소유자 또는 계정 관리자가 있습니다.

- [**청구 계정**](../manage/view-all-accounts.md) - 하나 또는 여러 Azure 구독의 단일 소유자를 나타냅니다. 현재는 여러 사람 또는 집계된 비용 보기에 대한 액세스 권한을 부여할 수 없습니다.

    리소스 유형: 해당 없음

개별 Azure 구독 계정 관리자는 [Azure 계정 센터](https://account.azure.com/subscriptions)에서 청구서나 결제 같은 청구 데이터를 보고 관리할 수 있습니다. 그러나 Azure Portal에서 비용 데이터를 보거나 리소스를 관리할 수는 없습니다. 계정 관리자에게 액세스 권한을 부여하려면 앞에서 언급한 Cost Management 역할을 사용합니다.

EA와 달리, 개별 Azure 구독 계정 관리자는 Azure Portal에서 청구서를 볼 수 있습니다. Cost Management 읽기 권한자와 Cost Management 기여자 역할은 청구서에 대한 액세스 권한을 제공하지 않습니다. 자세한 내용은 [청구서에 대한 액세스 권한을 부여하는 방법](../manage/manage-billing-access.md#give-read-only-access-to-billing)를 참조하세요.

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft 고객 계약 범위

Microsoft 고객 계약 청구 계정의 범위는 다음과 같습니다.

- **청구 계정** - 여러 Microsoft 제품 및 서비스에 대한 고객 계약을 나타냅니다. 고객 계약 청구 계정은 EA 등록과 기능적으로 동일하지 않습니다. EA 등록이 청구 프로필과 더 긴밀하게 정렬됩니다.

    리소스 종류: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **청구 프로필** - 청구서에 포함되는 구독을 정의합니다. 청구 프로필은 청구서가 생성되는 범위이므로 EA 등록과 기능적으로 동일합니다. 마찬가지로, Marketplace 및 예약처럼 사용량 기반이 아닌 구매는 이 범위에서만 사용할 수 있습니다. 청구서 섹션에 포함되지 않습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/billingProfiles`

- **청구서 섹션** - 청구서 또는 청구 프로필의 구독 그룹을 나타냅니다. 청구서 섹션은 부서와 유사하며, 여러 사람이 청구서 섹션에 액세스할 수 있습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/invoiceSections`

- **고객** - 파트너에 의해 Microsoft 고객 계약에 등록된 특정 고객과 연결된 구독 그룹을 나타냅니다. 이 범위는 CSP와 관련이 있습니다.

EA 청구 범위와 달리, 고객 계약 청구 계정은 단일 디렉터리에 바인딩_되며_ 여러 Azure AD 디렉터리에 걸쳐 구독을 가질 수 없습니다.

고객 계약 청구 범위는 파트너에게 적용되지 않습니다. 파트너 역할 및 사용 권한은 [사용자 역할 및 권한 할당](/partner-center/permissions-overview)에 설명되어 있습니다.

고객 계약 청구 범위는 다음 역할을 지원합니다.

- **소유자** – 청구 설정 및 액세스 권한을 관리하고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 이 고객 계약 청구 범위는 [Cost Management 기여자 Azure RBAC 역할](../../role-based-access-control/built-in-roles.md#cost-management-contributor)과 동일합니다.
- **기여자** – 액세스 권한을 제외한 청구 설정을 관리하고, 모든 비용을 보고, 비용 구성을 관리할 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 이 고객 계약 청구 범위는 [Cost Management 기여자 Azure RBAC 역할](../../role-based-access-control/built-in-roles.md#cost-management-contributor)과 동일합니다.
- **읽기 권한자** – 청구 설정, 비용 데이터 및 비용 구성을 볼 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 이 고객 계약 청구 범위는 [Cost Management 읽기 권한자 Azure RBAC 역할](../../role-based-access-control/built-in-roles.md#cost-management-reader)과 동일합니다.
- **청구서 관리자** – 청구서를 살펴보고 결제할 수 있으며 비용 데이터 및 구성을 볼 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 이 고객 계약 청구 범위는 [Cost Management 읽기 권한자 Azure RBAC 역할](../../role-based-access-control/built-in-roles.md#cost-management-reader)과 동일합니다.
- **Azure 구독 작성자** – Azure 구독을 만들고, 비용을 보고, 비용 구성을 관리할 수 있습니다. 예산 및 내보내기를 예로 들 수 있습니다. 기능적 측면에서 이 고객 계약 청구 범위는 EA 등록 계정 소유자 역할과 동일합니다.

Azure 구독은 EA 등록 계정 아래에 중첩되듯이 청구서 섹션 아래에 중첩됩니다. 청구 사용자는 각각 해당 범위에 속하는 구독 및 리소스 그룹의 비용 데이터에 액세스할 수 있습니다. 하지만 Azure Portal에서 리소스를 보거나 관리할 수 있는 액세스 권한은 없습니다. 청구 사용자는 Azure Portal 서비스 목록에서 **Cost Management + 청구**로 이동하여 비용을 볼 수 있습니다. 그런 다음, 보고해야 하는 특정 구독 및 리소스 그룹으로 비용을 필터링할 수 있습니다.

청구 사용자는 청구 계정에 명시적으로 포함되지 않기 때문에 관리 그룹에 대한 액세스 권한이 없습니다. 그러나 조직 전체에 관리 그룹을 사용하도록 설정하면 모든 구독 비용이 청구 계정과 루트 관리 그룹에 롤업됩니다. 둘 다 단일 디렉터리로 제한되기 때문입니다. 관리 그룹에는 사용량 기반 구매만 포함됩니다. 예약 및 타사 Marketplace 제품과 같은 구매는 관리 그룹에 포함되지 않습니다. 따라서 청구 계정과 루트 관리 그룹이 보고하는 합계가 서로 다를 수 있습니다. 이러한 비용을 보려면 청구 계정 또는 해당 청구 프로필을 사용합니다.

## <a name="aws-scopes"></a>AWS 범위

AWS 통합이 완료되면 [AWS 통합 설정 및 구성](aws-integration-set-up-configure.md)을 참조하세요. 사용 가능한 범위는 다음과 같습니다.

- **외부 청구 계정** - 타사 공급업체와 맺은 고객 계약을 나타냅니다. EA 청구 계정과 유사합니다.

    리소스 종류: `Microsoft.CostManagement/externalBillingAccounts`

- **외부 구독** - 타사 공급업체를 사용하는 고객 운영 계정을 나타냅니다. Azure 구독과 유사합니다.

    리소스 종류: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>CSP(클라우드 솔루션 공급자) 범위

Microsoft 고객 계약과 체결한 CSP에 대해 지원되는 범위는 다음과 같습니다.

- **청구 계정** - 여러 Microsoft 제품 및 서비스에 대한 고객 계약을 나타냅니다. 고객 계약 청구 계정은 EA 등록과 기능적으로 동일하지 않습니다. EA 등록이 청구 프로필과 더 긴밀하게 정렬됩니다.

    리소스 종류: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **청구 프로필** - 청구서에 포함되는 구독을 정의합니다. 청구 프로필은 청구서가 생성되는 범위이므로 EA 등록과 기능적으로 동일합니다. 마찬가지로, Marketplace 및 예약처럼 사용량 기반이 아닌 구매는 이 범위에서만 사용할 수 있습니다.

    리소스 종류: `Microsoft.Billing/billingAccounts/billingProfiles`

- **고객** - 파트너에 의해 Microsoft 고객 계약에 등록된 특정 고객과 연결된 구독 그룹을 나타냅니다.

*글로벌 관리자* 및 *관리 에이전트* 역할이 있는 사용자만 파트너의 Azure 테넌트에서 직접 청구 계정, 청구 프로필 및 고객에 대한 비용을 관리하고 볼 수 있습니다. 파트너 센터 역할에 대한 자세한 내용 [사용자 역할 및 권한 할당](/partner-center/permissions-overview)을 참조하세요.

고객이 Microsoft 고객 계약을 체결한 경우 Azure Cost Management는 CSP 파트너 고객만 지원합니다. 아직 Microsoft 고객 계약을 체결하지 않은 CSP 지원 고객은 [파트너 센터](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)를 참조하세요.

## <a name="switch-between-scopes-in-cost-management"></a>Cost Management에서 범위 전환

Azure Portal의 모든 Cost Management 보기는 왼쪽 위에 **범위** 선택 필이 있습니다. 이 필을 사용하여 범위를 신속하게 변경할 수 있습니다. **범위** 필을 클릭하면 범위 선택기가 열립니다. 여기에는 청구 계정, 루트 관리 그룹, 루트 관리 그룹에 중첩되지 않은 구독이 표시됩니다. 범위를 선택하려면 배경을 클릭하여 해당 범위를 강조 표시한 다음, 아래쪽에서 **선택**을 클릭합니다. 구독에서 리소스 그룹처럼 중첩된 범위로 이동하려면 범위 이름 링크를 클릭합니다. 중첩된 수준에서 부모 범위를 선택하려면 범위 선택기의 맨 위에서 **이 &lt;범위&gt; 선택**을 클릭합니다.

## <a name="identify-the-resource-id-for-a-scope"></a>범위의 리소스 ID 식별

Cost Management API를 사용하는 경우 반드시 범위를 알아야 합니다. 다음 정보를 사용하여 Cost Management API의 적절한 범위 URI를 빌드합니다.

### <a name="billing-accounts"></a>청구 계정

1. Azure Portal을 열고, 서비스 목록에서 **Cost Management + 청구**로 이동합니다.
2. 청구 계정 메뉴에서 **속성**을 선택합니다.
3. 청구 계정 ID를 복사합니다.
4. 범위는 `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`입니다.

### <a name="billing-profiles"></a>청구 프로필

1. Azure Portal을 열고, 서비스 목록에서 **Cost Management + 청구**로 이동합니다.
2. 청구 계정 메뉴에서 **청구 프로필**을 선택합니다.
3. 원하는 청구 프로필 이름을 클릭합니다.
4. 청구 프로필 메뉴에서 **속성**을 선택합니다.
5. 청구 계정 및 청구 프로필 ID를 복사합니다.
6. 범위는 `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`입니다.

### <a name="invoice-sections"></a>청구서 섹션

1. Azure Portal을 열고, 서비스 목록에서 **Cost Management + 청구**로 이동합니다.
2. 청구 계정 메뉴에서 **청구서 섹션**을 선택합니다.
3. 원하는 청구서 섹션 이름을 클릭합니다.
4. 청구서 섹션 메뉴에서 **속성**을 선택합니다.
5. 청구 계정 및 청구서 섹션 ID를 복사합니다.
6. 범위는 `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`입니다.

### <a name="ea-departments"></a>EA 부서

1. Azure Portal을 열고, 서비스 목록에서 **Cost Management + 청구**로 이동합니다.
2. 청구 계정 메뉴에서 **부서**를 선택합니다.
3. 원하는 부서 이름을 클릭합니다.
4. 부서 메뉴에서 **속성**을 선택합니다.
5. 청구 계정 및 부서 ID를 복사합니다.
6. 범위는 `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`입니다.

### <a name="ea-enrollment-account"></a>EA 등록 계정

1. Azure Portal을 열고, 서비스 목록에서 **Cost Management + 청구**로 이동합니다.
2. 청구 계정 메뉴에서 **등록 계정**을 선택합니다.
3. 원하는 등록 계정 이름을 클릭합니다.
4. 등록 계정 메뉴에서 **속성**을 선택합니다.
5. 청구 계정 및 등록 계정 ID를 복사합니다.
6. 범위는 `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`입니다.

### <a name="management-group"></a>관리 그룹

1. Azure Portal을 열고, 서비스 목록에서 **관리 그룹**으로 이동합니다.
2. 원하는 관리 그룹으로 이동합니다.
3. 표서 관리 그룹 ID를 복사합니다.
4. 범위는 `"/providers/Microsoft.Management/managementGroups/{id}"`입니다.

### <a name="subscription"></a>Subscription

1. Azure Portal을 열고, 서비스 목록에서 **구독**으로 이동합니다.
2. 표에서 구독 ID를 복사합니다.
3. 범위는 `"/subscriptions/{id}"`입니다.

### <a name="resource-groups"></a>리소스 그룹

1. Azure Portal을 열고, 서비스 목록에서 **리소스 그룹**으로 이동합니다.
2. 원하는 리소스 그룹 이름을 클릭합니다.
3. 리소스 그룹 메뉴에서 **속성**을 선택합니다.
4. 리소스 ID 필드 값을 복사합니다.
5. 범위는 `"/subscriptions/{id}/resourceGroups/{name}"`입니다.

Cost Management는 현재 [Azure Global](https://management.azure.com) 및 [Azure Government](https://management.usgovcloudapi.net)에서 지원됩니다. Azure Government에 대한 자세한 내용은 [Azure Global 및 Government API 엔드포인트](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
