---
title: Azure Managed Applications에 대 한 Azure 보안 기준
description: Azure Managed Applications 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: managed-applications
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 42aea886e38622c773ac1ca0ea2533d8aa1c47bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717029"
---
# <a name="azure-security-baseline-for-azure-managed-applications"></a>Azure Managed Applications에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 2.0](../../security/benchmarks/overview.md) 의 지침을 Azure Managed Applications 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure Managed Applications에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Managed Applications에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

Azure Managed Applications 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure Managed Applications 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../../security/benchmarks/security-controls-v2-network-security.md)을 참조하세요.

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: 네트워크 보안 규칙 단순화

**지침**: Azure Virtual Network 서비스 태그를 사용 하 여 Azure 관리 되는 응용 프로그램 리소스에 대해 구성 된 네트워크 보안 그룹 또는 azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: AzureResourceManager)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

- [Azure Resource Manager 서비스 태그 사용](../../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-management"></a>ID 관리

자세한 내용은 [Azure Security Benchmark: ID 관리](../../security/benchmarks/security-controls-v2-identity-management.md)를 참조하세요.

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Azure Active Directory를 중앙 ID 및 인증 시스템으로 표준화

**지침**: Azure Managed Applications는 기본 id 및 액세스 관리 서비스로 Azure Active Directory (Azure AD)를 사용 합니다. 조직의 id 및 액세스 관리를 제어 하도록 Azure AD를 표준화 합니다.

Azure는 Azure AD 및 OAuth를 사용 하 여 관리 되는 응용 프로그램에 대 한 액세스 권한을 부여 하기 위한 다음과 같은 Azure 기본 제공 역할

- 관리 되는 응용 프로그램 참가자 역할: 관리 되는 응용 프로그램 리소스를 만들 수 있습니다.

- 관리 되는 응용 프로그램 운영자 역할: 관리 되는 응용 프로그램 리소스에 대 한 작업을 읽고 수행할 수 있습니다.

- 관리 되는 응용 프로그램 판독기: 관리 되는 응용 프로그램에서 리소스를 읽고 JIT 액세스를 요청할 수 있습니다.

자세한 내용은 다음 참조 문서를 참조하세요.

- [관리 되는 응용 프로그램 참가자 역할](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [관리 되는 응용 프로그램 운영자 역할](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [관리 되는 응용 프로그램 판독기](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Azure Active Directory의 테넌시](../../active-directory/develop/single-and-multi-tenant-apps.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: 애플리케이션 ID를 안전하게 자동으로 관리

**지침**: 관리 되는 응용 프로그램에 대 한 사용 권한을 부여 하기 위해 Azure 관리 id를 사용 합니다. 추가 자격 증명을 관리 해야 하는 필요성을 제한 하기 위해 리소스에 액세스 하거나 실행 하는 데 더 강력한 인간 계정을 만드는 대신 Azure 관리 id 기능을 사용 하는 것이 좋습니다. 또한 Azure AD 인증을 지 원하는 다른 Azure 서비스/리소스에 대해 기본적으로 인증 하도록 Azure Managed Applications 서비스에는 관리 id가 할당 될 수 있습니다. 이 기능은 비밀을 검색할 때 Azure Managed Applications Azure Key Vault에 쉽게 액세스할 수 있도록 하는 데 유용할 수 있습니다. 관리 되는 id를 사용 하는 경우 id는 Azure 플랫폼에서 관리 되며 암호를 프로 비전 하거나 회전할 필요가 없습니다.

Azure Managed Applications는 응용 프로그램에 두 가지 유형의 id를 부여 하는 것을 지원 합니다.

- 시스템 할당 id는 구성 리소스와 연결 됩니다. 구성 리소스가 삭제 되는 경우 삭제 됩니다. 구성 리소스에는 시스템 할당 id가 하나만 있을 수 있습니다.

- 사용자 할당 id는 구성 리소스에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 구성 리소스에는 사용자에 게 할당 된 id가 여러 개 있을 수 있습니다.

관리 id를 활용할 수 없는 경우 Azure 관리 되는 응용 프로그램 리소스 수준에서 제한 된 권한으로 서비스 사용자를 만듭니다. 인증서 자격 증명을 사용 하 여 이러한 서비스 사용자를 구성 하 고 클라이언트 비밀로 대체 합니다. 두 경우 모두 Azure 관리 id와 함께 Azure Key Vault를 사용 하 여 런타임 환경 (예: Azure 함수)이 키 자격 증명 모음에서 자격 증명을 검색할 수 있습니다.

- [Azure Managed Applications에 관리 id를 사용 하는 방법](publish-managed-identity.md)

- [Azure 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)

- [Azure 서비스 주체](/powershell/azure/create-azure-service-principal-azureps) 

- [보안 주체 등록에 Azure Key Vault 사용](../../key-vault/general/authentication.md#app-identity-and-security-principals)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: 애플리케이션 액세스에 Azure AD SSO(Single Sign-On) 사용

**지침**: Azure Active Directory를 사용 하 여 Azure 리소스, 클라우드 응용 프로그램 및 온-프레미스 응용 프로그램에 id 및 액세스 관리를 제공 Azure Managed Applications 합니다. 여기에는 직원과 같은 엔터프라이즈 ID 및 파트너, 공급업체, 공급자와 같은 외부 ID도 포함됩니다. 이를 통해 SSO(Single Sign-On)가 온-프레미스 및 클라우드에서 조직의 데이터와 리소스에 대한 액세스를 관리하고 보호할 수 있습니다. 모든 사용자, 애플리케이션, 디바이스를 Azure AD에 연결하여 원활하고 안전하게 액세스하고 가시성과 제어를 강화할 수 있습니다.

- [Azure AD에서의 애플리케이션 SSO 이해](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: 모든 Azure Active Directory 기반 액세스에 강력한 인증 제어 사용

**지침**: Azure Managed Applications에서는 다단계 인증을 통해 강력한 인증 제어를 지 원하는 Azure Active Directory를 사용 하 고 강력한 암호 없는 메서드를 사용 합니다.
- 다단계 인증-Azure AD 다단계 인증을 사용 하도록 설정 하 고 다단계 인증 설정에서 몇 가지 모범 사례에 대 한 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다. multi-factor authentication은 모든 사용자에 게 적용 하거나 로그인 조건 및 위험 요소에 따라 사용자 단위 수준에서 선택할 수 있습니다.
- 암호 없는 인증 - 암호 없는 인증 옵션 3가지, 즉 비즈니스용 Windows Hello, Microsoft Authenticator 앱, 스마트 카드 등의 온-프레미스 인증 방법을 사용할 수 있습니다.

관리자 및 권한 있는 사용자의 경우 가장 높은 수준의 강력한 인증이 사용 되는지 확인 합니다.

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory에 대한 암호 없는 인증 옵션 소개](../../active-directory/authentication/concept-authentication-passwordless.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: 계정 변칙 모니터링 및 경고

**지침**: Azure Managed Applications은 다음과 같은 데이터 원본을 제공 하는 Azure Active Directory와 통합 됩니다.
- 로그인 – 로그인 보고서는 관리형 애플리케이션 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.
- 감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
- 위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.
- 위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

이러한 데이터 원본은 Azure Monitor, Azure 센티널 또는 타사 SIEM 시스템과 통합할 수 있습니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다.

Azure ATP(Advanced Threat Protection)는 Active Directory 신호를 사용하여 지능형 위협, 손상된 ID 및 악의적인 참가자 작업을 식별, 탐지 및 조사할 수 있는 보안 솔루션입니다.

- [Azure Active Directory의 감사 활동 보고서](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure AD 위험한 로그인을 확인하는 방법](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../../security-center/security-center-identity-access.md)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: 조건에 따라 Azure 리소스 액세스 제한

**지침**: Azure Managed Applications는 사용자 정의 조건에 따라 보다 세부적인 액세스 제어를 위해 Azure AD 조건부 액세스를 지원 합니다. 예를 들어 특정 IP 범위의 사용자 로그인은 로그인에 다단계 인증을 사용 해야 합니다. 다양한 사용 사례에 대해 세부적인 인증 세션 관리 정책을 사용할 수도 있습니다.

- [Azure 조건부 액세스 개요](../../active-directory/conditional-access/overview.md) 

- [일반적인 조건부 액세스 정책](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [조건부 액세스를 사용하여 인증 세션 관리 구성](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="privileged-access"></a>권한 있는 액세스

자세한 내용은 [Azure Security Benchmark: 권한 있는 액세스](../../security/benchmarks/security-controls-v2-privileged-access.md)를 참조하세요.

### <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1: 권한이 높은 사용자 보호 및 제한

**지침**: Azure Managed Applications는 id 및 액세스에 Azure Active Directory (Azure AD)를 사용 합니다. 가장 중요 한 기본 제공 역할은 전역 관리자이 고, 권한 있는 역할 관리자는 이러한 두 역할에 할당 된 사용자가 관리자 역할을 위임할 수 있습니다.
- 전역 관리자:이 역할의 사용자는 azure ad id를 사용 하는 서비스 뿐만 아니라 Azure AD의 모든 관리 기능에 액세스할 수 있습니다.
- 권한 있는 역할 관리자:이 역할의 사용자는 Azure AD 뿐만 아니라 Azure AD Privileged Identity Management (PIM) 내에서 역할 할당을 관리할 수 있습니다. 또한이 역할을 통해 PIM 및 관리 단위의 모든 측면을 관리할 수 있습니다.

참고: 권한 있는 특정 권한이 할당 된 사용자 지정 역할을 사용 하는 경우 관리 해야 하는 다른 중요 한 역할이 있을 수 있습니다. 또한 중요 한 비즈니스 자산의 관리자 계정에 비슷한 컨트롤을 적용할 수 있습니다.

Azure AD PIM(Privileged Identity Management)을 사용하여 Azure 리소스 및 Azure AD에 대한 JIT(Just-In-Time) 권한 있는 액세스를 사용하도록 설정할 수 있습니다. JIT는 사용자가 필요한 경우에만 권한 있는 작업을 수행할 수 있는 임시 권한을 부여합니다. 또한 PIM은 Azure AD 조직에서 의심스럽거나 안전하지 않은 활동이 있을 때 보안 경고를 생성할 수 있습니다.

- [Azure AD의 관리자 역할 권한](../../active-directory/roles/permissions-reference.md)

- [Azure Privileged Identity Management 보안 경고 사용](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../../active-directory/roles/security-planning.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: 중요 비즈니스용 시스템에 대한 관리 액세스 제한

**지침** Azure Managed Applications: Azure RBAC를 사용 하 여 구독 및 관리 그룹에 대 한 권한 있는 액세스 권한이 부여 된 계정을 제한 함으로써 업무상 중요 한 시스템에 대 한 액세스를 격리 합니다.

또한 업무상 중요 한 시스템에 설치 된 에이전트를 사용 하 여 Dc (Active Directory 도메인 컨트롤러), 보안 도구 및 시스템 관리 도구와 같이 업무상 중요 한 액세스에 대 한 관리 권한이 있는 관리, id 및 보안 시스템에 대 한 액세스를 제한 해야 합니다. 이러한 관리 및 보안 시스템을 손상 시키는 공격자는 비즈니스에 중요 한 자산을 손상 시키기 위해 즉시 weaponize 수 있습니다.

모든 종류의 액세스 제어는 일관 된 액세스 제어를 보장 하기 위해 엔터프라이즈 조각화 전략에 맞춰야 합니다.

- [Azure 구성 요소 및 참조 모델](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [관리 그룹 액세스](../../governance/management-groups/overview.md#management-group-access)

- [Azure 구독 관리자](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: 정기적으로 사용자 액세스 권한 검토 및 조정

**지침**: 정기적으로 사용자 계정 및 액세스 할당을 검토 하 여 계정 및 해당 액세스 수준이 유효한 지 확인 합니다.

Azure Managed Applications는 AAD (Azure Active Directory) 계정을 사용 하 여 리소스를 관리 하 고, 사용자 계정 및 액세스 할당을 정기적으로 검토 하 여 계정 및 해당 액세스가 올바른지 확인 합니다. Azure AD 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 검토할 수 있습니다. Azure AD reporting은 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공할 수 있습니다. 또한 Azure AD Privileged Identity Management를 사용 하 여 검토 프로세스를 용이 하 게 하는 액세스 검토 보고서 워크플로를 만들 수 있습니다.

또한 Azure Privileged Identity Management은 과도 한 수의 관리자 계정이 생성 될 때 경고 하도록 구성 하 고, 부실 하거나 부적절 하 게 구성 된 관리자 계정을 식별할 수 있습니다.

참고: 일부 Azure 서비스는 Azure AD를 통해 관리 되지 않는 로컬 사용자 및 역할을 지원 합니다. 이러한 사용자는 별도로 관리 해야 합니다.

- [Privileged Identity Management (PIM)에서 Azure 리소스 역할에 대 한 액세스 검토 만들기](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [Azure AD ID 및 액세스 검토를 사용하는 방법](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Azure AD에서 응급 액세스 설정

**지침**: Azure Managed Applications Azure Active Directory를 사용 하 여 리소스를 관리 합니다. 실수로 Azure AD 조직에서 잠기는 것을 방지 하려면 일반 관리 계정을 사용할 수 없는 경우 액세스를 위한 응급 액세스 계정을 설정 합니다. 응급 액세스 계정은 일반적으로 권한이 높으며 특정 사용자에게 할당되면 안 됩니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다.

응급 액세스 계정의 자격 증명(예: 암호, 인증서 또는 스마트 카드)을 안전하게 유지하고 비상시에만 사용할 권한이 있는 사용자에게만 알립니다.

- [Azure AD에서 응급 액세스 계정 관리](../../active-directory/roles/security-emergency-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: 자격 관리 자동화 

**지침**: Azure Managed Applications는 Azure Active Directory와 통합 되어 해당 리소스를 관리 합니다. Azure AD 자격 관리 기능을 사용 하 여 액세스 권한 부여, 검토, 만료 등의 액세스 요청 워크플로를 자동화할 수 있습니다. 이중 또는 다단계 승인도 지원 됩니다.

- [Azure AD 액세스 검토 란?](../../active-directory/governance/access-reviews-overview.md)

- [Azure AD 자격 관리 란?](../../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: 권한 있는 액세스 워크스테이션 사용

**지침**: 안전하고 격리된 워크스테이션은 관리자, 개발자 및 중요 서비스 운영자와 같은 중요한 역할의 보안에 매우 중요합니다. 관리 되는 응용 프로그램과 관련 된 관리 작업에는 매우 안전한 사용자 워크스테이션 및/또는 Azure 방호를 사용 합니다. Azure Active Directory, Microsoft Defender ATP(Advanced Threat Protection) 및/또는 Microsoft Intune을 사용하여 관리 작업을 위한 관리형 보안 사용자 워크스테이션을 배포할 수 있습니다. 보안 워크스테이션을 중앙에서 관리하여 강력한 인증, 소프트웨어 및 하드웨어 기준, 제한된 논리적 액세스 및 네트워크 액세스를 비롯한 보안 구성을 적용할 수 있습니다.

- [권한 있는 액세스 워크스테이션 이해](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [권한 있는 액세스 워크스테이션 배포](/security/compass/privileged-access-deployment)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: 충분한 관리 수행(최소 권한 원칙) 

**지침**: Azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 리소스를 관리 하는 Azure Managed Applications. Azure RBAC를 사용하면 역할 할당을 통해 Azure 리소스 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대해 미리 정의된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal과 같은 도구를 통해 쿼리하거나 인벤토리에 포함할 수 있습니다. Azure RBAC를 통해 리소스에 할당하는 권한은 항상 역할에 필요한 권한으로 제한해야 합니다. 이는 Azure AD PIM(Privileged Identity Management)의 JIT(Just-in-Time) 접근 방식을 보완하며 정기적으로 검토해야 합니다.

가능 하면 기본 제공 역할을 사용 하 여 사용 권한을 할당 하 고 필요한 경우에만 사용자 지정 역할을 만듭니다.

Azure는 Azure AD 및 OAuth를 사용 하 여 관리 되는 응용 프로그램에 대 한 액세스 권한을 부여 하기 위한 다음과 같은 Azure 기본 제공 역할

- 관리 되는 응용 프로그램 참가자 역할: 관리 되는 응용 프로그램 리소스를 만들 수 있습니다.

- 관리 되는 응용 프로그램 운영자 역할: 관리 되는 응용 프로그램 리소스에 대 한 작업을 읽고 수행할 수 있습니다.

- 관리 되는 응용 프로그램 판독기: 관리 되는 응용 프로그램에서 리소스를 읽고 JIT 액세스를 요청할 수 있습니다.

자세한 내용은 다음 참조 문서를 참조하세요.

- [관리 되는 응용 프로그램 참가자 역할](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [관리 되는 응용 프로그램 운영자 역할](../../role-based-access-control/built-in-roles.md#managed-application-contributor-role)

- [관리 되는 응용 프로그램 판독기](../../role-based-access-control/built-in-roles.md#managed-applications-reader)

- [Azure 역할 기반 access control (Azure RBAC) 이란?](../../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft 지원에 대 한 승인 프로세스 선택  

**지침**: Microsoft에서 Azure 관리 되는 응용 프로그램 데이터에 대 한 액세스 권한이 필요할 수 있는 지원 시나리오에 대 한 조직 승인 프로세스를 구현 합니다. 고객 Lockbox는 현재 관리 되는 응용 프로그램 시나리오에 사용할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../../security/benchmarks/security-controls-v2-data-protection.md)를 참조하세요.

### <a name="dp-2-protect-sensitive-data"></a>DP-2: 중요한 데이터 보호

**지침**: 고유한 키를 사용 하 여 암호화를 사용 하려면 관리 되는 응용 프로그램 구성 파일의 저장소에 대 한 사용자 고유의 저장소 계정을 활용할 수 있습니다.

- [사용자 고유의 저장소를 사용 하 여 관리 되는 구성 파일의 데이터 보호](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5: 중요한 저장소 데이터 암호화

**지침**: 응용 프로그램 및 해당 리소스를 정의 하는 관리 되는 응용 프로그램 정의는 고객이 관리 하는 암호화 키를 사용 하 여 구성할 수 있는 고유한 저장소 계정에 저장할 수 있습니다.

관리 되는 응용 프로그램 정의에 대 한 사용자 고유의 저장소를 가져올 수 없는 시나리오의 경우 Azure는 기본적으로 미사용 암호화 데이터를 제공 합니다.

- [관리 되는 응용 프로그램 정의에 대 한 고유한 저장소 가져오기](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Azure의 저장 데이터 암호화 이해](../../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [고객이 관리 하는 암호화 키를 구성 하는 방법](../../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

## <a name="asset-management"></a>자산 관리

자세한 내용은 [Azure Security Benchmark: 자산 관리](../../security/benchmarks/security-controls-v2-asset-management.md)를 참조하세요.

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: 보안 팀에서 자산 위험에 대한 가시성을 확보하도록 보장

**지침**: Azure Security Center를 사용하여 보안 위험을 모니터링할 수 있도록 Azure 테넌트 및 구독에서 보안 읽기 권한자 권한을 보안 팀에 부여합니다. 

보안 팀의 책임이 구성된 방식에 따라 보안 위험 모니터링은 중앙 보안 팀 또는 로컬 팀의 책임이 될 수 있습니다. 즉, 보안 인사이트 및 위험이 항상 조직 내의 중앙에서 집계되어야 합니다. 

보안 읽기 권한자 권한은 전체 테넌트(루트 관리 그룹)에 광범위하게 적용하거나 범위를 관리 그룹 또는 특정 구독으로 지정할 수 있습니다. 

참고: 워크로드 및 서비스에 대한 가시성을 얻으려면 추가 권한이 필요할 수 있습니다. 

- [보안 읽기 권한자 역할 개요](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 관리 그룹 개요](../../governance/management-groups/overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: 보안 팀에 자산 인벤토리 및 메타데이터에 대한 액세스 권한이 있는지 확인

**지침**: Azure 리소스, 리소스 그룹 및 구독에 태그를 적용 하 여 논리적으로 분류로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

관리 되는 앱을 만들 때 제공 되는 태그는 관리 되는 리소스 그룹에도 적용 됩니다. 응용 프로그램의 게시자는 배포 후 관리 되는 리소스에 대 한 고유한 추가 태그를 제공할 수 있습니다.

- [리소스 관리 응용 프로그램 UI 요소 별 태그](microsoft-common-tagsbyresource.md)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md) 

- [자산에 태그를 지정 하는 방법에 대 한 자세한 내용은 리소스 명명 및 태그 지정 결정 가이드를 참조 하세요.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: 승인된 Azure 서비스만 사용

**지침**: Azure Managed Applications는 Azure Policy를 사용 하 여 Azure Resource Manager 기반 배포 및 구성 적용을 지원 합니다. Azure Policy를 사용하여 환경에서 사용자가 프로비저닝할 수 있는 서비스를 감사하고 제한합니다. Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하고 검색합니다. 또한 Azure Monitor를 사용하여 승인되지 않은 서비스가 검색되면 경고를 트리거하는 규칙을 만들 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../../governance/policy/samples/built-in-policies.md#general)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: 자산 수명 주기 관리의 보안 보장

**지침**: 관리 되는 응용 프로그램 리소스를 삭제 하 여 관리 되는 응용 프로그램 리소스 및 연결 된 관리 되는 리소스 그룹을 삭제할 수 있습니다. 관리 되는 응용 프로그램 리소스가 삭제 되 면 관리 되는 리소스 그룹 및 내용도 삭제 됩니다. 추가 수명 주기 기능은 응용 프로그램의 게시자가 지정 하며,이는 허용 되는 작업을 통해 기본 관리 되는 리소스의 수명 주기에 대 한 추가 권한을 소비자에 게 제공할 수 있습니다. 소비자가 관리 하는 리소스에 대해서는 관리 되는 응용 프로그램의 게시자에 게 문의 하세요.

- [관리 되는 응용 프로그램 리소스 정리](./tutorial-create-managed-app-with-custom-provider.md?tabs=azurecli-interactive#clean-up-resources)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>오전 5: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 합니다.

- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="logging-and-threat-detection"></a>로깅 및 위협 탐지

자세한 내용은 [Azure Security Benchmark: 로깅 및 위협 탐지](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)를 참조하세요.

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure ID 및 액세스 관리를 위한 위협 탐지 사용

**지침**: azure Ad는 azure ad reporting에서 볼 수 있거나 Azure Monitor, azure 센티널 또는 기타 siem/모니터링 도구와 통합 하 여 보다 정교한 모니터링 및 분석 사용 사례를 위한 다음과 같은 사용자 로그를 제공 합니다. 로그인-관리 되는 응용 프로그램 및 사용자 로그인 활동 사용에 대 한 정보를 제공 합니다.
감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.
위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.
위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

또한 Azure Security Center는 과도한 인증 시도 실패 횟수, 구독에서 사용되지 않는 계정과 같은 의심스러운 특정 활동에 대해 경고할 수 있습니다. 기본 보안 예방 조치 모니터링 외에도 Azure Security Center의 위협 방지 모듈은 개별 Azure 컴퓨팅 리소스(가상 머신, 컨테이너, 앱 서비스), 데이터 리소스(SQL DB 및 스토리지) 및 Azure 서비스 계층에서 더 심층적인 보안 경고를 수집할 수 있습니다. 이 기능을 사용 하면 개별 리소스 내에서 계정 비정상을 볼 수 있습니다.

- [Azure Active Directory의 감사 활동 보고서](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure ID 보호 사용](../../active-directory/identity-protection/overview-identity-protection.md)

- [위협 방지 및 Azure Security Center](../../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대한 로깅 사용

**참고**: 자동으로 사용할 수 있는 활동 로그에는 읽기 작업 (GET)을 제외 하 고 관리 되는 응용 프로그램 리소스에 대 한 모든 쓰기 작업 (PUT, POST, DELETE)이 포함 됩니다. 활동 로그를 사용 하 여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../../azure-monitor/essentials/diagnostic-settings.md) 

- [Azure의 로깅 및 다른 로그 유형 이해](../../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: 보안 로그 관리 및 분석 중앙 집중화

**지침**: 상관 관계를 설정 하기 위해 로깅, 저장소 및 분석을 중앙 집중화 합니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 저장소 위치, 데이터를 처리 하 고 액세스 하는 데 사용 되는 도구 및 데이터 보존 요구 사항을 할당 했는지 확인 합니다.
Azure 활동 로그를 중앙 로깅에 통합 하 고 있는지 확인 합니다. Azure Monitor를 통해 로그를 수집 하 여 끝점 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

또한 Azure 센티널 또는 타사 SIEM에 데이터를 사용 하도록 설정 하 고 등록 합니다.

많은 조직에서 자주 사용 되는 "핫" 데이터에 대해 Azure 센티널을 사용 하 고 덜 자주 사용 되는 "콜드" 데이터를 Azure Storage 하는 것을 선택 합니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: 로그 스토리지 보존 구성

**지침**: 관리 되는 응용 프로그램 리소스에 의해 생성 된 로그를 저장 하는 데 사용 되는 저장소 계정 또는 Log Analytics 작업 영역에 조직의 규정 준수 규정에 따라 설정 된 로그 보존 기간이 있는지 확인 합니다.
Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정할 수 있습니다. 장기 및 보관 저장소에 대 한 Azure Storage, Data Lake 또는 Log Analytics 작업 영역 계정을 사용 합니다.

- [Log Analytics 작업 영역 보존 기간을 구성 하는 방법](../../azure-monitor/logs/manage-cost-storage.md)

- [Azure Storage 계정에 리소스 로그 저장](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../../security/benchmarks/security-controls-v2-incident-response.md)을 참조하세요.

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

**지침**: 조직이 보안 인시던트에 대응하는 프로세스를 보유하고, 해당 프로세스를 Azure에 대해 업데이트했으며, 준비 상태를 확인하기 위해 프로세스를 정기적으로 수행하는지 확인합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

**지침**: Azure Security Center에서 보안 인시던트 연락처 정보를 설정합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 탐지 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

**지침**: 고품질 경고를 만들고 경고의 품질을 측정하는 프로세스가 있는지 확인합니다. 이렇게 하면 이전 인시던트에서 상황을 확인하고 분석가를 위한 경고의 우선 순위를 지정할 수 있으므로 가양성으로 인해 시간을 낭비하지 않습니다. 

고품질 경고는 이전 인시던트, 유효성이 검사된 커뮤니티 소스, 다양한 신호 원본을 결합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구 등에서 얻은 경험을 기반으로 구축할 수 있습니다. 

Azure Security Center는 여러 Azure 자산에서 고품질의 경고를 제공 합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../../sentinel/connect-azure-security-center.md)

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

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../../sentinel/tutorial-investigate-cases.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

**지침**: 경고 심각도 및 자산 민감도에 따라 먼저 집중해야 하는 인시던트에 대한 컨텍스트를 분석가에게 제공합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석의 Security Center 얼마나 확실 한지에 따라 달라 지 며, 경고를 발생 시킨 활동의 악의적인 의도를가지고 있는 신뢰도 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../management/tag-resources.md).

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

**지침**: 수동 반복 작업을 자동화하여 응답 시간을 단축하고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. 

Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="posture-and-vulnerability-management"></a>태세 및 취약성 관리

자세한 내용은 [Azure Security Benchmark: 태세 및 취약성 관리](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)를 참조하세요.

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1: Azure 서비스에 대한 보안 구성 설정 

**지침**: 사용 하는 Azure 서비스를 쉽게 안전 하 게 구성할 수 있도록 하 여 인프라 및 devops 팀에 대 한 보안 guardrails 정의 합니다.

관리 되는 응용 프로그램 배포와 관련 된 리소스의 구성을 감사 하 고 적용 하도록 Azure Policy를 구성 합니다.

Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, Azure RBAC 할당 및 Azure Policy 할당을 비롯 한 서비스 및 응용 프로그램 환경의 배포 및 구성을 자동화할 수 있습니다.

- [클라우드 채택 프레임 워크 엔터프라이즈 규모 방문 영역](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture#landing-zone-expanded-definition)

- [규정 준수를 적용 하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2: Azure 서비스에 대한 보안 구성 유지

**지침**: Azure Security Center을 사용 하 여 Azure Managed Applications와 관련 된 리소스를 모니터링 하 고 Azure Policy [거부] 및 [없는 경우 배포] 효과를 사용 하 여 보안 구성을 유지 합니다.
- [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)

- [규정 준수를 적용 하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprints](../../governance/blueprints/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: 정기적인 공격 시뮬레이션 수행

**지침**: 필요에 따라 Azure 리소스에 대한 침투 테스트 또는 레드 팀 활동을 수행하고 모든 중요한 보안 결과를 수정해야 합니다.
Microsoft Cloud 침투 테스트 시행 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [Azure의 침투 테스트](../../security/fundamentals/pen-testing.md)

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="backup-and-recovery"></a>백업 및 복구

자세한 내용은 [Azure Security Benchmark: 백업 및 복구](../../security/benchmarks/security-controls-v2-backup-recovery.md)를 참조하세요.

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: 고객 관리형 키를 비롯한 모든 백업 유효성 검사

**지침**: 관리 되는 응용 프로그램 정의를 고유한 저장소 계정에 저장 하는 경우 Azure Key Vault에 저장 된 해당 계정의 암호화에 사용 되는 모든 관련 고객 관리 키를 복원할 수 있는지 확인 합니다.

- [관리 되는 응용 프로그램 정의에 대 한 고유한 저장소 가져오기](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Azure에서 Key Vault 키를 복원 하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: 분실한 키의 위험 완화

**지침**: 관리 되는 응용 프로그램 정의에 대 한 사용자 고유의 저장소를 가져오는 경우 정의를 암호화 하는 데 사용 되는 키의 손실을 방지 하 고 복구할 수 있는 측정값이 있어야 합니다. 실수로 또는 악의적인 삭제 로부터 키를 보호 하기 위해 고객 관리 키를 저장 하는 Azure Key Vault에 대해 일시 삭제 및 제거 보호를 사용 하도록 설정 합니다.  

- [관리 되는 응용 프로그램 정의에 대 한 고유한 저장소 가져오기](./publish-service-catalog-app.md?tabs=azure-powershell#bring-your-own-storage-for-the-managed-application-definition)

- [Key Vault에서 일시 삭제 및 제거 보호를 사용하도록 설정하는 방법](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="governance-and-strategy"></a>거버넌스 및 전략

자세한 내용은 [Azure Security Benchmark: 거버넌스 및 전략](../../security/benchmarks/security-controls-v2-governance-strategy.md)을 참조하세요.

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: 자산 관리 및 데이터 보호 전략 정의 

**지침**: 시스템 및 데이터를 지속적으로 모니터링하고 보호하기 위한 명확한 전략을 문서화하고 전달해야 합니다. 중요 비즈니스용 데이터 및 시스템의 검색, 평가, 보호, 모니터링에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   비즈니스 위험에 따른 데이터 분류 표준

-   위험 및 자산 인벤토리에 대한 보안 조직의 가시성 

-   사용할 Azure 서비스에 대한 보안 조직의 승인 

-   수명 주기 전체에서 자산 보안

-   조직 데이터 분류에 따라 필요한 액세스 제어 전략

-   Azure 기본 및 타사 데이터 보호 기능 사용

-   전송 중 및 미사용 사용 사례에 대한 데이터 암호화 요구 사항

-   적절한 암호화 표준

자세한 내용은 다음 참조 문서를 참조하세요.
- [Azure 보안 아키텍처 권장 사항 - 스토리지, 데이터, 암호화](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Azure 보안 기본 사항 - Azure 데이터 보안, 암호화, 스토리지](../../security/fundamentals/encryption-overview.md)

- [클라우드 채택 프레임워크 - Azure 데이터 보안 및 암호화 모범 사례](../../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark - 자산 관리](../../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark - 데이터 보호](../../security/benchmarks/security-controls-v2-data-protection.md)

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

- [Azure Security Benchmark - 태세 및 취약성 관리](../../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

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

- [Azure Security Benchmark - 네트워크 보안](../../security/benchmarks/security-controls-v2-network-security.md)

- [Azure 네트워크 보안 개요](../../security/fundamentals/network-overview.md)

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

- [Azure Security Benchmark - ID 관리](../../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark - 권한 있는 액세스](../../security/benchmarks/security-controls-v2-privileged-access.md)

- [Azure 보안 모범 사례 11 – 아키텍처 단일 통합 보안 전략](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure ID 관리 보안 개요](../../security/fundamentals/identity-management-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: 로깅 및 위협 대응 전략 정의

**지침**: 규정 준수 요구 사항을 충족하면서 신속하게 위협을 탐지하고 수정할 수 있는 로깅 및 위협 대응 전략을 수립합니다. 통합 및 수동 단계가 아닌 위협에 집중할 수 있도록 분석가에게 고품질 경고 및 원활한 환경을 제공하는 것에 우선 순위를 지정합니다. 

이 전략에는 다음 요소에 대한 문서화된 지침, 정책, 표준이 포함되어야 합니다. 

-   보안 운영(SecOps) 조직의 역할 및 책임 

-   NIST 또는 다른 업계 프레임워크를 사용하여 잘 정의된 인시던트 응답 프로세스 

-   위협 탐지, 인시던트 응답 및 규정 준수 요구 사항을 지원하는 로그 캡처 및 보존

-   SIEM, 네이티브 Azure 기능, 기타 소스를 사용하여 위협에 대한 중앙 집중식 가시성 및 상관 관계 정보 

-   고객, 공급자 및 공공 당사자와의 통신 및 알림 계획

-   로깅 및 위협 탐지, 포렌식, 공격 수정, 제거와 같은 인시던트 처리를 위해 Azure 네이티브 및 타사 플랫폼 사용

-   확인한 상황 및 증거 보존을 포함하여 인시던트 및 인시던트 후 활동을 처리하는 프로세스

자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Security Benchmark - 로깅 및 위협 탐지](../../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - 인시던트 응답](../../security/benchmarks/security-controls-v2-incident-response.md)

- [Azure 보안 모범 사례 4 - 프로세스 클라우드에 대한 인시던트 응답 프로세스 업데이트](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 채택 프레임워크, 로깅, 보고 의사 결정 가이드](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 엔터프라이즈 규모, 관리, 모니터링](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.