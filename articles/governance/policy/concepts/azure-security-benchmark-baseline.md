---
title: Azure 보안 벤치 마크에 대 한 Azure Policy 보안 기준
description: Azure Policy 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6c6bd3ab87013aba04f43c95b3a986a64f2bc394
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231472"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Azure 보안 벤치 마크에 대 한 Azure Policy 보안 기준

이 보안 기준은 [Azure 보안 벤치 마크](../../../security/benchmarks/overview.md) 에서 Azure Policy에 대 한 지침을 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에서 정의한 **규정 준수 도메인** 및 **보안 제어** 와 Azure Policy에 적용 되는 관련 지침을 기준으로 그룹화 됩니다. Azure Policy에 적용할 수 없는 **컨트롤** 은 제외 되었습니다. Azure Policy 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure Policy 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

기본 제공 이니셔티브를 통해 Azure 보안 벤치 마크 컨트롤을 기본 제공 정책 정의로 매핑하면 [규정 준수: Azure 보안 벤치 마크](../samples/azure-security-benchmark.md)를 참조 하세요.

Azure Policy는 _책임_대신 _소유권_ 이라는 용어를 사용 합니다. _소유권_에 대 한 자세한 내용은 클라우드에서 [정책 정의](./definition-structure.md#type) 및 [공유 책임](../../../security/fundamentals/shared-responsibility.md)Azure Policy을 참조 하세요.


## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../../../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Policy에서는 자동으로 사용 하도록 설정 된 활동 로그를 사용 하 여 이벤트 원본, 날짜, 사용자, 타임 스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소를 포함 합니다.

* [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../../../azure-monitor/platform/diagnostic-settings.md)

* [Azure의 로깅 및 다른 로그 유형 이해](../../../azure-monitor/platform/platform-logs-overview.md)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../../../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다. 

[Azure AD Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) 권한 있는 역할 또는 [Azure Resource Manager](../../../azure-resource-manager/management/overview.md)를 사용 하 여 just-in-time/간단 하 게 액세스 솔루션을 사용 하도록 설정할 수도 있습니다.


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(권한 있는 액세스 워크스테이션) 사용

**지침**: MFA가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure 리소스에 로그인하고 구성합니다.

* [권한 있는 액세스 워크스테이션에 대해 알아보기](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](../../../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Policy에 대 한 액세스를 제어 합니다.

* [Azure Policy의 Azure RBAC 권한](../overview.md#rbac-permissions-in-azure-policy)

* [Azure RBAC를 구성 하는 방법](../../../role-based-access-control/role-assignments-portal.md)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Policy에서 변경이 발생 하는 경우에 대 한 경고를 만들려면 활동 로그와 Azure Monitor를 사용 합니다.

* [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](../../../azure-monitor/platform/alerts-activity-log.md)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../../../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다. Azure Policy _수정_ 효과를 사용 하 여 규정 준수 및 일관적인 태그 관리를 보고 하 고 적용 합니다.

* [자습서: 정책 만들기 및 관리](../tutorials/create-and-manage.md)

* [자습서: 태그 거버넌스 관리](../tutorials/govern-tags.md)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 정책 정의 및 정책 할당의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

* [Azure Policy를 구성하고 관리하는 방법](../tutorials/create-and-manage.md)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../../../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../../../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
