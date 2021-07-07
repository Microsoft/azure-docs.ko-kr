---
title: Cost Management에 대한 Azure 보안 기준
description: Cost Management 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 627c6bcd01a11356d1f207aa079c75d4b6194c59
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093264"
---
# <a name="azure-security-baseline-for-cost-management"></a>Cost Management에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 2.0](../security/benchmarks/overview.md)의 지침을 Azure Cost Management에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark에서 정의한 **보안 제어** 및 Cost Management에 적용되는 관련 지침에 따라 그룹화됩니다. Cost Management에 적용되지 않는 **제어** 는 제외되었습니다.

Cost Management가 Azure Security Benchmark에 완전히 매핑되는 방법을 확인하려면 [전체 Cost Management 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Azure Cost Management는 Azure의 기본 ID 및 액세스 관리 서비스인 Azure AD(Azure Active Directory)와 통합됩니다. Azure AD를 표준화하여 다음 리소스에서 조직의 ID 및 액세스 관리를 관리해야 합니다.

- Microsoft 클라우드 리소스(예: Azure Portal, Azure Storage, Azure Virtual Machines(Linux 및 Windows), Azure Key Vault, PaaS 및 SaaS 애플리케이션)

- 조직의 리소스(예: Azure의 애플리케이션 또는 회사 네트워크 리소스)

Azure AD 보안은 조직의 클라우드 보안 사례에서 높은 우선 순위여야 합니다. Azure AD는 Microsoft의 모범 사례 추천 사항과 관련하여 ID 보안 상태를 평가하는 데 도움이 되는 ID 보안 점수를 제공합니다. 점수를 사용하여 구성이 모범 사례 추천 사항에 근접하게 일치하는 정도를 측정하고 보안 상태를 향상시킬 수 있습니다.

참고: Azure AD는 외부 ID 공급자를 지원하므로 Microsoft 계정이 없는 사용자가 외부 ID를 사용하여 애플리케이션 및 리소스에 로그인할 수 있습니다.

- [Azure AD의 테넌트](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure AD 테넌트 정의](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [애플리케이션에 외부 ID 공급자 사용](../active-directory/external-identities/identity-providers.md)

- [Azure AD의 ID 보안 점수란?](../active-directory/fundamentals/identity-secure-score.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 애플리케이션 액세스에 Azure AD SSO(Single Sign-On) 사용

**지침**: Azure Cost Management는 Azure Active Directory를 사용하여 Azure 리소스, 클라우드 애플리케이션 및 온-프레미스 애플리케이션에 대한 ID 및 액세스 관리를 제공합니다. 여기에는 엔터프라이즈 ID(예: 직원) 및 외부 ID(예: 파트너, 공급업체 및 공급자)가 포함됩니다. 이를 통해 SSO(Single Sign-On)가 온-프레미스 및 클라우드에서 조직의 데이터와 리소스에 대한 액세스를 관리하고 보호할 수 있습니다. 모든 사용자, 애플리케이션, 디바이스를 Azure AD에 연결하여 원활하고 안전하게 액세스하고 가시성과 제어를 강화할 수 있습니다.

- [Azure AD에서의 애플리케이션 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: Azure Cost Management는 MFA(다단계 인증)를 통한 강력한 인증 제어와 강력한 암호 없는 방법을 지원하는 Azure AD와 통합됩니다.

- 다단계 인증 - Azure AD MFA를 사용하도록 설정하고 MFA 설정의 몇 가지 모범 사례에 대한 Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다. MFA는 로그인 조건 및 위험 요소에 따라 모든 사용자나 일부 사용자에게 또는 사용자별 수준에서 적용할 수 있습니다.

- 암호 없는 인증 – 세 가지 암호 없는 인증 옵션을 사용할 수 있습니다. Windows Hello for Business, Microsoft Authenticator 앱 및 스마트 카드와 같은 온-프레미스 인증 방법입니다.

관리자 및 권한 있는 사용자의 경우 가장 높은 수준의 강력한 인증 방법을 사용하고 다른 사용자에게 적절한 강력한 인증 정책을 롤아웃해야 합니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory에 대한 암호 없는 인증 옵션 소개](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD 기본 암호 정책](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Azure AD 암호 보호를 사용하여 잘못된 암호 제거](../active-directory/authentication/concept-password-ban-bad.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 비정상 계정 활동 모니터링 및 경고

**지침**: Azure Cost Management는 다음과 같은 데이터 원본을 제공하는 Azure Active Directory와 통합됩니다.

- 로그인 – 로그인 보고서는 관리형 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이러한 데이터 원본은 Azure Monitor, Azure Sentinel 또는 타사 SIEM 시스템과 통합할 수 있습니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다.

Azure ATP(Advanced Threat Protection)는 Active Directory 신호를 사용하여 지능형 위협, 손상된 ID 및 악의적인 참가자 작업을 식별, 탐지 및 조사할 수 있는 보안 솔루션입니다.

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md) 

- [Azure Security Center 위협 인텔리전스 보호 모듈의 경고](../security-center/alerts-reference.md) 

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: 높은 권한이 있는 사용자 보호 및 제한

**지침**: EA(기업계약)에 대한 Cost Management에는 아래와 같은 높은 권한의 계정이 있습니다.

- 엔터프라이즈 관리자

EA(기업계약) 내의 역할에 할당된 사용자를 정기적으로 검토하는 것이 좋습니다.

또한 이 권한이 있는 사용자는 Azure 환경의 모든 리소스를 직접 또는 간접적으로 읽고 수정할 수 있으므로 높은 권한이 있는 계정 또는 역할의 수를 제한하고 이러한 계정을 관리자 권한 수준에서 보호하는 것이 좋습니다.

Azure AD PIM(Privileged Identity Management)을 사용하여 Azure 리소스 및 Azure AD에 대한 JIT(Just-In-Time) 권한 있는 액세스를 사용하도록 설정할 수 있습니다. JIT는 사용자가 필요한 경우에만 권한 있는 작업을 수행할 수 있는 임시 권한을 부여합니다. 또한 PIM은 Azure AD 조직에서 의심스럽거나 안전하지 않은 활동이 있을 때 보안 경고를 생성할 수 있습니다.

- [Azure Enterprise 역할 관리](manage/understand-ea-roles.md) 

- [Azure AD의 관리자 역할 권한](../active-directory/roles/permissions-reference.md) 

- [Azure Privileged Identity Management 보안 경고 사용](../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md) 

- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../active-directory/roles/security-planning.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Cost Management는 적절한 액세스를 사용하여 조직의 비용 데이터를 보고 관리합니다. 액세스는 구독 수준에서 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 제어되며, 범위 청구는 EA(기업계약) 또는 MCA(Microsoft 고객 계약)에 포함된 관리 역할을 통해 제어됩니다. 정기적으로 부여된 액세스를 감사하고 검토하여 사용자 또는 그룹에 필수 액세스 권한이 필요한지 확인합니다.

- [Azure의 청구 정보에 대한 액세스 관리](manage/manage-billing-access.md)

- [Cost Management 데이터에 대한 액세스 할당](costs/assign-access-acm-data.md)

- [Azure Enterprise 역할 관리](manage/understand-ea-roles.md)

- [Azure의 Microsoft 고객 계약 관리자 역할 이해](manage/understand-mca-roles.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: Azure Cost Management는 Azure RBAC(역할 기반 액세스 제어)와 통합되어 리소스(예: 예산, 저장된 보고서 등)를 관리합니다. Azure RBAC를 사용하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 및 서비스 주체에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. Azure RBAC를 통해 리소스에 할당하는 권한은 항상 역할에 필요한 권한으로 제한해야 합니다. 이는 Azure AD PIM(Privileged Identity Management)의 JIT(Just-in-Time) 접근 방식을 보완하며 정기적으로 검토해야 합니다.

기본 제공 역할을 사용하여 권한을 할당하고 필요한 경우에만 사용자 지정 역할을 만듭니다.

Azure Cost Management는 기본 제공 역할, 읽기 권한자 및 기여자를 제공합니다.

- [Azure Cost Management 읽기 권한자](../role-based-access-control/built-in-roles.md#cost-management-reader)

- [Azure Cost Management 기여자](../role-based-access-control/built-in-roles.md#cost-management-contributor)

Azure RBAC(역할 기반 액세스 제어)란? ../role-based-access-control/overview.md 

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)를 참조하세요.

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: 중요한 데이터 검색, 분류 및 레이블 지정

**지침**: 중요한 정보가 조직의 기술 시스템을 통해 안전하게 저장, 처리 및 전송되도록 적절한 제어를 설계할 수 있도록 중요한 데이터를 검색하고, 분류하고, 레이블을 지정하는 것이 좋습니다.

Azure, 온-프레미스, Office 365 및 기타 위치에 있는 Office 문서 내의 중요한 정보에 대해서는 Azure Information Protection(및 관련 검색 도구)을 사용합니다.

Azure SQL 정보 보호를 사용하여 Azure SQL Database에 저장된 정보의 분류 및 레이블 지정을 지원할 수 있습니다.

- [Azure Information Protection을 사용하여 중요한 정보 태그 지정](/azure/information-protection/what-is-information-protection) 

- [Azure SQL 데이터 검색을 구현하는 방법](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요한 데이터 보호

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어), 네트워크 기반 액세스 제어 및 Azure 서비스의 특정 제어(예: SQL 및 기타 데이터베이스의 암호화)를 통해 액세스를 제한하여 중요한 데이터를 보호하는 것이 좋습니다.

일관된 액세스 제어를 보장하려면 모든 유형의 액세스 제어를 엔터프라이즈 조각화 전략에 맞게 조정해야 합니다. 또한 엔터프라이즈 조각화 전략은 중요 비즈니스용 데이터 및 시스템의 위치를 통해 알려야 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출로부터 보호합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 몇 가지 기본 데이터 보호 제어 및 기능을 구현했습니다.

- [Cost Management 데이터에 대한 액세스 할당](costs/assign-access-acm-data.md)

- [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md) 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: 중요한 데이터의 무단 전송 모니터링

**지침**: 엔터프라이즈 가시성 및 제어를 벗어난 위치로의 무단 데이터 전송을 모니터링합니다. 여기에는 일반적으로 무단 데이터 반출을 나타낼 수 있는 비정상 활동(대량 또는 비정상 전송)에 대한 모니터링이 포함됩니다.

Azure Storage ATP(Advanced Threat Protection) 및 Azure SQL ATP는 중요한 정보의 무단 전송을 나타낼 수 있는 비정상 정보 전송에 대해 경고할 수 있습니다.

AIP(Azure Information Protection)는 분류되고 레이블이 지정된 정보에 대한 모니터링 기능을 제공합니다.

DLP(데이터 손실 방지)를 준수하는 데 필요한 경우 데이터 반출을 방지하기 위해 호스트 기반 DLP 솔루션을 사용하여 탐지 및/또는 예방 제어를 적용할 수 있습니다.

- [Azure SQL ATP 사용](../azure-sql/database/threat-detection-overview.md) 

- [Azure Storage ATP 사용](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: 중요한 저장소 데이터 암호화

**지침**: 액세스 제어를 보완하기 위해 Azure Cost Management의 내보내기 기능은 Microsoft 관리형 키 암호화를 사용하여 '대역 외' 공격(예: 기본 스토리지 액세스)으로부터 보호하는 미사용 데이터의 Azure Storage 암호화를 지원합니다. 이 기본 설정을 사용하면 공격자가 데이터를 쉽게 읽거나 수정할 수 없습니다.

자세한 내용은 다음을 참조하세요.

- [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

참고: 워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: 잠재적으로 큰 영향을 주는 수정을 위해 자산 수명 주기 관리 프로세스를 처리하는 보안 정책을 설정하거나 업데이트합니다. 이러한 수정에는 ID 공급자 및 액세스, 데이터 민감도, 네트워크 구성 및 관리 권한 할당에 대한 변경이 포함됩니다.

더 이상 필요하지 않은 Azure 리소스를 제거합니다.

- [Azure 리소스 그룹 및 리소스 삭제](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리에 위협 탐지 사용

**지침**: Azure AD는 Azure AD 보고에서 볼 수 있거나 더 정교한 모니터링 및 분석 사용 사례를 위해 Azure Monitor, Azure Sentinel 또는 기타 SIEM/모니터링 도구와 통합할 수 있는 다음과 같은 사용자 로그를 제공합니다.

- 로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.

- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다. 기본 보안 예방 조치 모니터링 외에도 Azure Security Center의 위협 방지 모듈은 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집할 수 있습니다. 이 기능을 사용하면 개별 리소스 내에서 비정상 계정 활동을 볼 수 있습니다.

또한 EA(기업계약) 내의 역할에 할당된 사용자를 정기적으로 검토하는 것이 좋습니다. 

- [Azure Enterprise 역할 관리](manage/understand-ea-roles.md)

- [Azure Active Directory의 감사 활동 보고서](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure ID 보호 사용](../active-directory/identity-protection/overview-identity-protection.md)

- [위협 방지 및 Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 검색 및 분석 – 고품질 경고에 기반한 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고 품질을 측정하는 프로세스가 있는지 확인합니다. 이를 통해 과거 인시던트로부터 교훈을 습득하고 분석가에 대한 경고의 우선 순위를 지정할 수 있으므로 가양성에 시간을 낭비하지 않습니다. 

고품질 경고는 과거 인시던트로부터의 경험, 유효성이 검사된 커뮤니티 원본 및 다양한 신호 원본을 융합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구를 기반으로 하여 작성될 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 검색 및 분석 – 인시던트 조사

**지침**: 분석가가 잠재적 인시던트를 조사할 때 다양한 데이터 원본을 쿼리하고 사용하여 발생한 상황에 대한 전체 보기를 작성할 수 있는지 확인합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다.  또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.  

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

*자세한 내용은 [Azure Security Benchmark: 보안 상태 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.*

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: 자동으로 신속하게 소프트웨어 취약성 수정

**지침**: 소프트웨어 업데이트를 신속하게 배포하여 운영 체제 및 애플리케이션의 소프트웨어 취약성을 수정합니다.

우선 순위는 일반적인 위험 점수 매기기 프로그램(예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공하는 기본 위험 등급을 사용하고 높은 보안 위험을 나타내는 애플리케이션과 높은 가동 시간이 필요한 애플리케이션에 대한 컨텍스트를 사용하여 환경에 맞게 조정합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따른 필수 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 저장 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](../security/benchmarks/security-controls-v2-data-protection.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: 엔터프라이즈 구분 전략 정의 

**지침**: ID, 네트워크, 애플리케이션, 구독, 관리 그룹 및 기타 컨트롤의 조합을 사용하여 자산에 대한 액세스를 구분하는 엔터프라이즈 전체 전략을 수립합니다.

서로 통신하고 데이터에 액세스해야 하는 시스템의 일상 작업을 사용하도록 설정해야 할 필요성과 보안 분리의 필요성을 신중하게 조정해야 합니다.

구분 전략이 네트워크 보안, ID 및 액세스 모델, 애플리케이션 권한/액세스 모델 및 사용자 프로세스 컨트롤을 비롯하여 컨트롤 형식 간에 일관되게 구현되는지 확인합니다.

- [Azure의 구분 전략에 대한 지침(동영상)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure의 구분 전략에 대한 지침(문서)](/security/compass/governance#enterprise-segmentation-strategy)

- [엔터프라이즈 구분 전략에 맞춰 네트워크 구분 조정](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: 보안 태세 관리 전략 정의

**지침**: 개별 자산과 해당 자산이 호스트되는 환경에 대한 위험을 지속적으로 측정하고 완화합니다. 게시된 애플리케이션, 네트워크 수신 및 송신 지점, 사용자 및 관리자 엔드포인트 등과 같은 고가치 자산과 노출이 많은 공격 노출 영역에 우선 순위를 지정합니다.

- [Azure Security Benchmark - 태세 및 취약성 관리](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: 조직 역할, 책임, 의무 조정

**지침**: 보안 조직의 역할 및 책임에 대한 명확한 전략을 문서화하고 전달하는지 확인합니다. 보안 의사 결정에 대한 명확한 책임을 제시하고, 공유 책임 모델을 모두에게 교육하고, 클라우드를 보호하는 기술에 관해 기술 팀을 교육하는 일에 우선 순위를 지정합니다.

- [Azure 보안 모범 사례 1 – 사용자: 클라우드 보안 경험에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 보안 모범 사례 2 – 사용자: 클라우드 보안 기술에 대한 팀 교육](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 보안 모범 사례 3 – 프로세스: 클라우드 보안 결정에 대한 책임 할당](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: 네트워크 보안 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure 네트워크 보안 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 네트워크 관리 및 보안 책임

-   엔터프라이즈 구분 전략에 맞춰 조정된 가상 네트워크 구분 모델

-   다양한 위협 및 공격 시나리오에서 수정 전략

-   인터넷 에지 및 수신/송신 전략

-   하이브리드 클라우드 및 온-프레미스 상호 연결 전략

-   최신 네트워크 보안 아티팩트(예: 네트워크 다이어그램, 참조 네트워크 아키텍처)

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark - 네트워크 보안](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../security/fundamentals/network-overview.md)

- [엔터프라이즈 네트워크 아키텍처 전략](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: ID 및 권한 있는 액세스 전략 정의

**지침**: 조직의 전반적인 보안 액세스 제어 전략의 일부로 Azure ID 및 권한 있는 액세스 방법을 설정합니다.  

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   중앙 집중식 ID 및 인증 시스템과 다른 내부 및 외부 ID 시스템 간의 상호 연결

-   다양한 사용 사례 및 조건에서 강력한 인증 방법

-   권한이 높은 사용자 보호

-   변칙 사용자 활동 모니터링 및 처리  

-   사용자 ID 및 액세스 검토와 조정 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - ID 관리](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 로그 및 위협 대응 전략을 수립하여 규정 준수 요구 사항을 충족하면서 위협을 신속하게 탐지하고 수정합니다. 분석가가 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고와 원활한 환경을 제공하는 데 적용되는 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책 및 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.