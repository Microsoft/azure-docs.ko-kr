---
title: Azure DevTest Labs에 대 한 Azure 보안 기준
description: Azure DevTest Labs에 대 한 Azure 보안 기준
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: dc53eacf456d1e7c1926c6d7f20e343b3a84340d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078047"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs에 대 한 Azure 보안 기준

Azure DevTest Labs에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 azure [보안 벤치 마크 버전 1.0](../security/benchmarks/overview.md)에서 가져온 것으로, azure에서 클라우드 솔루션을 보호할 수 있는 방법에 대 한 권장 사항을 제공 합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용
**지침:** Microsoft는 Azure 리소스에 대 한 시간 소스를 유지 관리 합니다. 그러나 계산 리소스에 대 한 시간 동기화 설정을 관리할 수 있습니다.

Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법에 대 한 자세한 내용은 [azure에서 Windows vm에 대 한 시간 동기화](../virtual-machines/windows/time-sync.md)문서를 참조 하세요. 

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성
**지침:** Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 로그를 보냅니다. 활동 로그는 관리 평면 수준에서 Azure DevTest Labs 인스턴스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 DevTest Labs 인스턴스에 대 한 관리 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 확인할 수 있습니다.

자세한 내용은 [진단 설정 만들기를 참조 하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보냅니다](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용
**지침:** Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 로그를 보냅니다. 활동 로그는 관리 평면 수준에서 Azure DevTest Labs 인스턴스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 DevTest Labs 인스턴스에 대 한 관리 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

자세한 내용은 [진단 설정 만들기를 참조 하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보냅니다](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집
**지침:** 고객이 Vm (가상 머신)을 만들고 소유 하는 Azure DevTest Labs. 따라서이를 모니터링 하는 것은 조직의 책임입니다. Azure Security Center를 사용 하 여 계산 OS를 모니터링할 수 있습니다. 운영 체제에서 Security Center에 의해 수집 되는 데이터에는 OS 유형 및 버전, OS (Windows 이벤트 로그), 실행 중인 프로세스, 컴퓨터 이름, IP 주소 및 로그인 한 사용자가 포함 됩니다. 또한 Log Analytics 에이전트는 크래시 덤프 파일을 수집 합니다.

자세한 내용은 다음 아티클을 참조하세요. 

- [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](../azure-monitor/learn/quick-collect-azurevm.md)
- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성
***지침:** Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure DevTest Labs 인스턴스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

자세한 내용은 [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 문서를 참조 하세요.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토
**지침:** Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics에서 쿼리를 실행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, Azure DevTest Labs에 대해 수집 되었을 수 있는 활동 로그 데이터를 기반으로 다양 한 통찰력을 제공 합니다.

자세한 내용은 다음 아티클을 참조하세요.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/diagnostic-settings.md)
- [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 하는 방법](../azure-monitor/platform/activity-log.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용
**지침:** Azure Log Analytics 작업 영역을 사용 하 여 Azure DevTest Labs와 관련 된 보안 로그 및 이벤트의 비정상적인 활동을 모니터링 하 고 경고 합니다.

자세한 내용은 [log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md) 문서를 참조 하세요.

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화
**지침:** 해당 없음. Azure DevTest Labs는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용
**지침:** 해당 없음. Azure DevTest Labs는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용
**지침:** Azure DevTest Labs는 고객이 소유 하 고 관리 하는 Azure Compute 컴퓨터를 만듭니다. 지원 되는 모든 Azure Windows Vm의 Microsoft Monitoring Agent를 사용 하 여 프로세스 만들기 이벤트와 필드를 로깅합니다 `CommandLine` . 지원 되는 Azure Linux Vm의 경우 노드 단위로 콘솔 로깅을 수동으로 구성 하 고 Syslog를 사용 하 여 데이터를 저장할 수 있습니다. 또한 Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 Azure Vm에서 로그를 검토 하 고 기록 되는 데이터에 대해 쿼리를 실행 합니다.

- [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Azure Monitor에서 사용자 지정 쿼리를 실행 하는 방법](../azure-monitor/log-query/get-started-queries.md)
- [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

## <a name="identity-and-access-control"></a>ID 및 Access Control
*자세한 내용은 [보안 그룹: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리
**지침:** Azure Active Directory (Azure AD)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 실행 하 고 관리 그룹의 구성원 인 계정을 검색 합니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Azure DevTest Labs 역할](devtest-lab-add-devtest-user.md)  

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)
**지침:** Azure Active Directory (Azure AD)에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 만들어야 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 Marketplace 서비스를 담당 하 고 있습니다.

DevTest Labs에는 기본 암호 개념이 없습니다. 

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용
**지침:** 전용 관리 계정 사용과 관련 하 여 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](../security-center/security-center-identity-access.md)  
- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [Azure DevTest Labs 역할](devtest-lab-add-devtest-user.md)  

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용
**지침:** DevTest Labs는 id 관리에 Azure AD 서비스를 사용 합니다. DevTest Labs를 기반으로 환경에 대 한 액세스 권한을 사용자에 게 부여 하는 경우 다음 두 가지 주요 측면을 고려 하세요.

- **리소스 관리:** 리소스를 관리 하는 Azure Portal에 대 한 액세스를 제공 합니다 (Vm 만들기, 환경 만들기, 시작, 중지, 다시 시작, 삭제 및 아티팩트 적용 등). 리소스 관리는 azure에서 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 수행 됩니다. 사용자에 게 역할을 할당 하 고 리소스 및 액세스 수준 사용 권한을 설정 합니다.
- **가상 컴퓨터 (네트워크 수준)**: 기본 구성에서 vm은 로컬 관리자 계정을 사용 합니다. 사용 가능한 도메인 (Azure AD Domain Services, 온-프레미스 도메인 또는 클라우드 기반 도메인)이 있으면 컴퓨터를 도메인에 조인할 수 있습니다. 그런 다음 사용자는 도메인 가입 아티팩트를 사용 하 여 도메인 기반 id를 사용 하 여 컴퓨터에 연결할 수 있습니다. 

- [DevTest Labs에 대 한 참조 아키텍처](devtest-lab-reference-architecture.md#architecture)
- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용
**지침:** Azure Active Directory (AD) Multi-Factor Authentication (MFA)를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)  
- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링:*** 예

**책임:** Customer


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용
**지침:** MFA가 구성 된 Paw (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다.

- [Privileged Access Workstation에 대한 자세한 정보](/windows-server/identity/securing-privileged-access/privileged-access-workstations)  
- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)  

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고
**지침:** 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure AD (Azure Active Directory) 보안 보고서를 사용 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)  
- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)  

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리
**지침:** 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.

- [Azure에서 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용
**지침:** Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정
**지침:** Azure AD (Azure Active Directory)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](../active-directory/reports-monitoring/overview-reports.md)  
- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)  

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 대한 액세스 시도 모니터링
**지침:** SIEM (보안 정보 및 이벤트 관리)/모니터링 도구와 통합할 수 있도록 하는 Azure Active Directory (Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고
**지침:** Azure AD (Azure Active Directory) 위험 및 Id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)  
- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공
**지침:** 고객 Lockbox 현재 Azure DevTest Labs 지원 되지 않습니다.

- [지원 되 고객 Lockbox 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

## <a name="data-protection"></a>데이터 보호
*자세한 내용은 [보안 그룹: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요 한 정보 인벤토리 유지 관리
**지침:** 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스 추적을 지원 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/resource-group-using-tags.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리
**지침:** 개발, 테스트 및 프로덕션을 위한 별도의 구독 또는 관리 그룹을 구현 합니다. Azure DevTest Labs 인스턴스는 가상 네트워크/서브넷으로 구분 하 고 적절 하 게 태그를 지정 해야 합니다. 

- [추가 Azure 구독을 만드는 방법](../billing/billing-create-subscription.md)
- [관리 그룹을 만드는 방법](../governance/management-groups/create.md)
- [DevTest Labs에 대 한 가상 네트워크를 구성 하는 방법](devtest-lab-configure-vnet.md)
- [태그를 만들고 사용하는 방법](../azure-resource-manager/resource-group-using-tags.md)
- [DevTest Labs에 대 한 태그를 만들고 사용 하는 방법](devtest-lab-add-tag.md)

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단
**지침:** 아직 사용할 수 없음 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure DevTest Labs 사용할 수 없습니다.

Microsoft는 Azure DevTest Labs에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** 공유할

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화
**지침:** Azure DevTest Labs에는 기본적으로 TLS로 암호화 된 통신이 필요 합니다. TLS 버전 1.2는 현재 지원 됩니다. 클라이언트 라이브러리 또는 도구에서 TLS를 지원 하지 않는 경우 Azure Portal 또는 관리 Api를 통해 암호화 되지 않은 연결을 사용 하도록 설정할 수 있습니다. 암호화 된 연결을 사용할 수 없는 경우에는 랩 및 클라이언트 응용 프로그램을 가상 네트워크에 배치 하는 것이 좋습니다.

[DevTest Labs에 대 한 전송 시나리오의 암호화 이해](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Azure Security Center 모니터링:** 예로

**책임:** 공유할

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별
**지침:** 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure DevTest Labs 사용할 수 없습니다. 중요 한 정보를 포함 하는 인스턴스에 태그를 적용 하 고 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어
**지침:** Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure DevTest Labs의 랩에 대 한 액세스를 제어 합니다.

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)
- [DevTest Labs의 역할 이해](devtest-lab-add-devtest-user.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용
**지침:** DevTest Labs의 일부로 생성 된 계산 리소스에 대 한 준수를 위해 필요한 경우 자동화 된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현 하 여 시스템에서 데이터를 복사 하는 경우에도 데이터에 대 한 액세스 제어를 적용 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화
**지침:** Azure DevTest Labs는 다음 고객 데이터를 저장 합니다.

- 아티팩트를 적용 하 여 생성 된 배포 및 확장 로그를 포함 하는 [아티팩트 결과](add-artifact-vm.md)
- 수식에서 가상 컴퓨터를 만드는 데 사용 되는 [수식 문서](devtest-lab-manage-formulas.md)
- 랩 가상 컴퓨터에 대 한 OS 및 데이터 디스크 

아티팩트 결과 및 수식 문서는 모든 랩 배포의 일부로 생성 된 Azure Storage 계정으로 전송 됩니다. Azure Storage의 데이터는 256 비트 AES 암호화를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다. Azure Storage 암호화를 사용 하지 않도록 설정할 수 없습니다. Microsoft에서 관리 하는 키를 사용 하 여 저장소 계정의 암호화를 사용 하거나 사용자 고유의 키로 암호화를 관리할 수 있습니다. 자세한 내용은 [lab storage 계정 암호화](encrypt-storage.md)를 참조 하세요.

기본적으로 모든 랩 OS 및 데이터 디스크는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 기존 관리 디스크에 작성 된 모든 관리 디스크, 스냅숏, 이미지 및 데이터는 플랫폼 관리 키를 사용 하 여 미사용 상태로 자동으로 암호화 됩니다. 랩 소유자는 고객 관리 키로 암호화 되도록 랩 OS 디스크를 구성할 수 있습니다. 랩 데이터 디스크에 대 한 고객 관리 키를 사용 하는 암호화는 현재 랩 자체를 통해 구성할 수 없습니다. 그러나 구독 관리자는 지금은 구독 내의 랩 디스크에 대해이 설정을 구성할 수 있습니다. 자세한 내용은 [고객 관리 키를 사용 하 여 Lab DevTest LABS OS 디스크 암호화](encrypt-disks-customer-managed-keys.md)를 참조 하세요.

**Azure Security Center 모니터링:** 해당 없음

**책임:** 공유할

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고
**지침:** DevTest Labs 인스턴스 및 기타 중요 하거나 관련 된 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만들려면 Azure 활동 로그와 함께 Azure Monitor를 사용 합니다.

- [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)
- [DevTest Labs 활동 로그 이벤트에 대 한 경고를 만드는 방법](create-alerts.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer



## <a name="vulnerability-management"></a>취약성 관리
*자세한 내용은 [보안 그룹: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행
**지침:** Azure DevTest Labs 인스턴스 및 관련 리소스를 보호 하는 Azure Security Center의 권장 사항을 따릅니다.

Microsoft는 Azure DevTest Labs을 지 원하는 기본 리소스에서 취약성 관리를 수행 합니다.

- [Azure Security Center 권장 사항 이해](../security-center/recommendations-reference.md) 

**Azure Security Center 모니터링:** 예로

**책임:** 공유할

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포
**지침:** Azure 업데이트 관리를 사용 하 여 DevTest Labs 내에서 호스트 되는 Windows 및 Linux Vm에 최신 보안 업데이트가 설치 되어 있는지 확인 합니다. Windows Vm의 경우 Windows 업데이트를 사용 하도록 설정 하 고 자동으로 업데이트 하도록 설정 되어 있는지 확인 합니다. 이 설정은 현재 DevTest Labs를 통해 구성 하는 데 사용할 수 없지만 lab admin/subscription 관리자는 구독의 기본 계산 Vm에서이 설정을 구성할 수 있습니다. 

- [Azure에서 Vm에 대 한 업데이트 관리를 구성 하는 방법](../automation/update-management/update-mgmt-overview.md)
- [Security Center에서 모니터링 하는 Azure 보안 정책 이해](../security-center/security-center-policy-definitions.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포
***지침:*** 랩 관리자는 [DevTest Labs 아티팩트](add-artifact-vm.md) 를 사용 하 여 보안 패치와 기타 업데이트를 포함 하는 랩 사용자 지정 이미지에 대 한 업데이트를 자동화할 수 있습니다. 

모든 원하는 구성을 사용 하 여 정기적으로 이미지를 자동으로 빌드 및 배포 하는 [DevTest Labs 이미지 팩터리에](image-factory-create.md)대해 자세히 알아보세요. 

구독 관리자는 Azure 업데이트 관리 솔루션을 사용 하 여 DevTest Labs Vm에 대 한 업데이트 및 패치를 관리할 수도 있습니다. 업데이트 관리는 로컬로 구성 된 업데이트 리포지토리를 사용 하 여 지원 되는 Windows 시스템을 패치 합니다. System Center Updates Publisher (Updates Publisher)와 같은 도구를 사용 하 여 WSUS (Windows Server Update Services)에 사용자 지정 업데이트를 게시할 수 있습니다. 이 시나리오를 사용 하면 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하 여 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다.

- [Azure의 업데이트 관리 솔루션](../automation/update-management/update-mgmt-overview.md)
- [Vm에 대 한 업데이트 및 패치 관리](../automation/update-management/update-mgmt-overview.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교
**지침:** 검색 결과를 일관 된 간격으로 내보내고 결과를 비교 하 여 취약점이 재구성 되었는지 확인 합니다. Azure Security Center에서 제안 하는 취약성 관리 권장 사항을 사용 하는 경우 고객은 선택한 솔루션의 포털로 피벗 하 여 기록 검색 데이터를 볼 수 있습니다.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정
**지침:** Azure Security Center에서 제공 하는 기본 위험 등급 (보안 점수)을 사용 합니다.

- [보안 점수 Azure Security Center 이해](../security-center/secure-score-security-controls.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리
*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용
**지침:** Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (DevTest Labs 리소스 포함)를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한이 있는지 확인 하 고 구독 내에서 모든 Azure 구독 및 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)
- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리
**지침:** Azure 리소스에 태그를 적용 하 여 분류에 따라 논리적으로 구성 하는 메타 데이터를 제공 합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)
- [DevTest Labs에 대 한 태그 구성](devtest-lab-add-tag.md)

**Azure Security Center 모니터링:** 사용할 수 없음

**책임:** Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제
**지침:** 태깅, 관리 그룹 및 별도의 구독을 사용 하 고, 랩 및 랩 관련 리소스를 구성 하 고 추적 하는 데 필요한 경우 별도의 랩을 사용 합니다. 정기적으로 인벤토리를 조정 하 고 권한 없는 리소스가 구독에서 빠르게 삭제 되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)
- [관리 그룹을 만드는 방법](../governance/management-groups/create.md)
- [DevTest Labs를 사용 하 여 랩을 만드는 방법](devtest-lab-create-lab.md)
- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)
- [랩에 대 한 태그를 구성 하는 방법](devtest-lab-add-tag.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리
**지침:** 조직 요구 사항에 따라 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어의 인벤토리를 만듭니다. 구독 관리자는 구성 된 랩 컴퓨터 그룹에서 실행할 수 있는 응용 프로그램 집합을 정의 하는 데 도움이 되는 적응 응용 프로그램 제어 인 Azure Security Center 기능을 사용할 수도 있습니다. 이 기능은 Azure 및 비 Azure Windows (모든 버전, 클래식 또는 Azure Resource Manager) 및 Linux 컴퓨터에서 사용할 수 있습니다.

- [적응형 응용 프로그램 제어를 사용 하도록 설정 하는 방법](../security-center/security-center-adaptive-application.md)
 
**Azure Security Center 모니터링:** 적용할 수 없는 **책임:** 고객

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링
**지침:** Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 저장소 계정을 사용 하는 환경 등의 높은 보안 기반 환경에서 유용 하 게 사용할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링
**지침:** Azure Automation는 워크 로드 및 리소스의 배포, 작업 및 서비스 해제 중에 완전 한 제어를 제공 합니다. 구독 관리자는 Azure Virtual Machine 인벤토리를 사용 하 여 구독에서 DevTest Labs Vm의 모든 소프트웨어에 대 한 정보 수집을 자동화할 수 있습니다. 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간 속성은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대 한 액세스 권한을 얻으려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

소프트웨어 응용 프로그램 모니터링을 위해 변경 내용 추적를 사용 하는 것 외에도 Azure Security Center의 적응 응용 프로그램 컨트롤은 기계 학습을 사용 하 여 컴퓨터에서 실행 되는 응용 프로그램을 분석 하 고이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성 하 고 유지 관리 하는 프로세스를 간소화 하 여 사용자 환경에서 원치 않는 소프트웨어를 사용 하지 않도록 할 수 있도록 합니다. 감사 모드를 구성 하거나 모드를 적용할 수 있습니다. 감사 모드는 보호 된 Vm의 활동만 감사 합니다. 적용 모드는 규칙을 적용 하 고 실행이 허용 되지 않은 응용 프로그램이 차단 되도록 합니다. 

- [Azure Automation 소개](../automation/automation-intro.md)
- [Azure VM 인벤토리를 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)
- [적응 응용 프로그램 컨트롤 이해](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거
**지침:** Azure Automation는 워크 로드 및 리소스의 배포, 작업 및 서비스 해제 중에 완전 한 제어를 제공 합니다. 구독 관리자는 변경 내용 추적를 사용 하 여 DevTest Labs에서 호스트 되는 Vm에 설치 된 모든 소프트웨어를 식별할 수 있습니다. 사용자 고유의 프로세스를 구현 하거나 인증 되지 않은 소프트웨어를 제거 하기 위해 Azure Automation 상태 구성을 사용할 수 있습니다.

- [Azure Automation 소개](../automation/automation-intro.md)
- [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](../automation/change-tracking.md)
- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)

**Azure Security Center 모니터링:** 사용할 수 없음

**책임:** Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용
**지침:** 구독 관리자는 Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한이 있는 소프트웨어만 실행 되도록 하 고 권한이 없는 모든 소프트웨어는 DevTest Labs에서 호스트 되는 Azure Vm에서 실행 되지 않도록 차단할 수 있습니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용
**지침:** Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다. 

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

다음 문서를 참조하세요. 
- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/not-allowed-resource-types.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리
**지침:** 적응 응용 프로그램 제어는 DevTest Labs에서 호스트 되는 Azure 및 비 Azure 컴퓨터 (Windows 및 Linux)에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Azure Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 참고 DevTest Labs에서 호스트 되는 기본 계산 리소스에 대해이 설정을 구성 하려면 구독 관리자 여야 합니다. 이 설정이 조직의 요구 사항을 충족 하지 않는 경우 타사 솔루션을 구현 합니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한
**지침:** Azure 조건부 액세스를 사용 하 여 **Microsoft Azure 관리** 앱에 대 한 **액세스 차단**"* *을 구성 하 여 사용자의 Azure Resource Manager 상호 작용 하는 기능을 제한 합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한
**지침:** 스크립트 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용 하 여 DevTest Labs에서 호스트 되는 Vm 내에서 사용자가 스크립트를 실행 하는 기능을 제한할 수 있습니다. Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한이 있는 소프트웨어만 실행 되 고 모든 권한이 없는 소프트웨어가 기본 Azure Vm에서 실행 되지 않도록 차단할 수도 있습니다.

- [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링:** 사용할 수 없음

**책임:** Customer


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리
**지침:** Azure 환경에 배포 된 위험도 높은 응용 프로그램은 가상 네트워크, 서브넷, 구독, 관리 그룹 등을 사용 하 여 격리할 수 있습니다. 그리고 Azure 방화벽, WAF (웹 응용 프로그램 방화벽) 또는 NSG (네트워크 보안 그룹)를 사용 하 여 충분히 안전 하 게 보호 합니다.

- [DevTest Labs에 대 한 가상 네트워크 구성](devtest-lab-configure-vnet.md)
- [Azure Firewall 개요](../firewall/overview.md)
- [웹 애플리케이션 방화벽 개요](../web-application-firewall/overview.md)
- [네트워크 보안 개요](../virtual-network/security-overview.md)
- [Azure Virtual Network 개요]()
- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)
- [구독 관련 결정 가이드](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center 모니터링:** 사용할 수 없음

**책임:** Customer

## <a name="secure-configuration"></a>보안 구성
**자세한 내용은 보안 제어: 보안 구성을 참조 하세요.**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정
**지침:** Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 DevTest Labs의 일부로 생성 된 Azure 리소스의 구성을 감사 하거나 적용 합니다. 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

또한 Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하거나 초과 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)
- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정
**지침:** Azure Security Center 권장 사항을 사용 하 여 DevTest Labs의 일부로 생성 된 모든 기본 계산 리소스에 대 한 보안 구성을 유지 합니다. 또한 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성 또는 DevTest Labs 아티팩트를 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure Security Center 권장 사항을 모니터링 하는 방법](../security-center/security-center-recommendations.md)
- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)
- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)
- [VHD를 업로드 하 고이를 사용 하 여 Azure에서 새 Windows Vm 만들기](../virtual-machines/windows/upload-generalized-managed.md)
- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기](../virtual-machines/linux/upload-vhd.md)
- [여러 DevTest Labs에 사용자 지정 이미지 만들기 및 배포](image-factory-save-distribute-custom-images.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리
**지침:** DevTest Labs의 일부로 생성 된 Azure 리소스 전체에서 보안 설정을 적용 하려면 Azure Policy **거부** 및 **배포** 규칙을 사용 합니다. 또한 Azure Resource Manager 템플릿을 사용 하 여 조직에서 요구 하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다.

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)
- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리
**지침:** 랩의 일부로 생성 된 기본 Azure 계산 리소스에 대 한 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다. 또한 Azure Resource Manager 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 유지할 수 있습니다. 이미지 팩터리 솔루션을 사용할 수도 있습니다. 이미지 팩터리 솔루션은 모든 desired 구성에서 정기적으로 이미지를 자동으로 빌드하고 배포 하는 코드를 작성 하는 솔루션입니다.

또한 Microsoft에서 게시 하는 가상 머신 이미지 Azure Marketplace Microsoft에서 관리 하 고 유지 관리 합니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](../security-center/security-center-vulnerability-assessment-recommendations.md)
- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)
- [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)
- [DevTest Labs에서 이미지 팩터리를 만드는 방법](image-factory-create.md)

**Azure Security Center 모니터링:** 예로

**책임:** 공유할

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장
**지침:** Azure DevOps를 사용 하 여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리할 수 있습니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 특정 사용자, 기본 제공 보안 그룹 또는 Azure Active Directory (Azure AD)에 정의 된 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다.

- [Azure Repos Git 자습서](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow)
- [권한 및 그룹 정보](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions?view=azure-devops&tabs=preview-page)
- [Azure DevTest Labs와 Azure DevOps 워크플로 간 통합](devtest-lab-dev-ops.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장
**지침:** 사용자 지정 이미지를 사용 하는 경우 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 공유 이미지 갤러리를 사용 하 여 이미지를 필요한 특정 랩에 공유할 수 있습니다. 컨테이너 이미지의 경우 Azure Container Registry에 저장 하 고 Azure RBAC를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

- [Azure RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure RBAC를 구성 하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [DevTest Labs에 대 한 공유 이미지 갤러리 구성](configure-shared-image-gallery.md)
- [Container Registry에 대 한 Azure RBAC 이해](../container-registry/container-registry-roles.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포
**지침:** Azure Policy를 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 DevTest Labs에서 만든 Azure 리소스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다. 또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)
- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포
**지침:** Azure Automation 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대 한 구성 관리 서비스입니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. 또한 조직 정책을 따르도록 모든 랩 컴퓨터에 설치할 수 있는 사용자 지정 아티팩트를 작성할 수 있습니다. 

- [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](../automation/automation-dsc-onboarding.md)
- [DevTest Labs 가상 머신에 대 한 사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현
**지침:** Azure Security Center를 사용 하 여 DevTest Labs에서 만든 Azure 리소스에 대 한 기준 검색을 수행 합니다. 또한 Azure Policy를 사용 하 여 Azure 리소스 구성을 경고 하 고 감사 합니다.

- [Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)
 
**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현
**지침:** Azure Security Center를 사용 하 여 컨테이너에 대 한 OS 및 Docker 설정에 대 한 기준 검색을 수행 합니다.

- [Azure Security Center 컨테이너 권장 사항 이해](../security-center/security-center-container-recommendations.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리
**지침:** Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

- [DevTest Labs에서 Azure Resource Manager 환경을 배포 하도록 관리 되는 id 구성](use-managed-identities-environments.md)
- [DevTest Labs에서 가상 컴퓨터를 배포 하도록 관리 되는 id 구성](enable-managed-identities-lab-vms.md)
- [주요 자격 증명 모음을 만드는 방법](../key-vault/quick-create-portal.md)
- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](../key-vault/managed-identity.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리
**지침:** 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

- [DevTest Labs에서 Azure Resource Manager 환경을 배포 하도록 관리 되는 id 구성](use-managed-identities-environments.md)
- [DevTest Labs에서 가상 컴퓨터를 배포 하도록 관리 되는 id 구성](enable-managed-identities-lab-vms.md)
 
**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거
**지침:** 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- 자격 증명 스캐너를 설정 하는 방법

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer


## <a name="malware-defense"></a>맬웨어 방어
*자세한 내용은 보안 제어: 맬웨어 방어를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용
**지침:** Azure Cloud Services 및 Virtual Machines에 Microsoft 맬웨어 방지 프로그램을 사용 하 여 리소스를 지속적으로 모니터링 하 고 방어할 수 있습니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다. 또한 데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다.

- Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지 프로그램을 구성 하는 방법
- 위협 보호 및 Azure Security Center

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사
**지침:** Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: 랩에서 호스트 되는 Azure App Service)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 콘텐츠에서 실행 되지 않습니다.
App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 합니다.

데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다.

- Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해
- 데이터 서비스에 대 한 Azure Security Center의 위협 검색 이해

**Azure Security Center 모니터링:** 예로

**책임:** 해당 없음


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인
**지침:** 배포 되 면 Azure 용 Microsoft 맬웨어 방지 프로그램은 기본적으로 최신 서명, 플랫폼 및 엔진 업데이트를 자동으로 설치 합니다. Azure Security Center "계산 & 앱"의 권장 사항에 따라 DevTest Labs 기본 계산 리소스에 대 한 모든 끝점이 최신 서명으로 최신 상태를 유지 하는지 확인 합니다. Windows OS는 Azure Security Center와 통합 되는 Microsoft Defender Advanced Threat Protection 서비스를 사용 하 여 바이러스 또는 맬웨어 기반 공격의 위험을 제한 하는 추가 보안으로 추가로 보호할 수 있습니다.

- Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 프로그램을 배포 하는 방법
- Microsoft Defender Advanced Threat Protection

**Azure Security Center 모니터링:** 예로

**책임:** Customer

## <a name="data-recovery"></a>데이터 복구
*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인
**지침:** 현재 Azure DevTest Labs는 VM 백업 및 스냅숏을 지원 하지 않습니다. 그러나 DevTest Labs에서 호스트 되는 기본 Azure Vm에서 Azure Backup를 사용 하도록 설정 하 고 구성할 수 있습니다. 또한 기본 계산 리소스에 대 한 적절 한 액세스 권한이 있는 경우 자동 백업에 대 한 원하는 빈도 및 보존 기간을 구성할 수 있습니다. 

- [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)
- [VM 설정에서 Azure VM 백업](../backup/backup-azure-vms-first-look-arm.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.
**지침:** 현재 Azure DevTest Labs는 VM 백업 및 스냅숏을 지원 하지 않습니다. 그러나 기본 계산 리소스에 대 한 적절 한 액세스 권한이 있는 한 PowerShell 또는 REST Api를 사용 하 여 해당 인스턴스에 연결 된 관리 디스크 또는 DevTest Labs에서 호스트 되는 기본 Azure Vm의 스냅숏을 만들 수 있습니다. 또한 Azure Key Vault 내에서 고객 관리 키를 백업할 수 있습니다.

대상 Azure Vm에서 Azure Backup를 사용 하도록 설정 하 고 원하는 빈도와 보존 기간을 설정 합니다. 여기에는 전체 시스템 상태 백업이 포함 됩니다. Azure disk encryption을 사용 하는 경우 Azure VM 백업은 고객이 관리 하는 키의 백업을 자동으로 처리 합니다.

- [암호화를 사용 하는 Azure Vm에서 백업](../backup/backup-azure-vms-encryption.md)
- [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)
- [Azure VM 백업 개요](../backup/backup-azure-vms-introduction.md)
- [Azure에서 Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사
**지침:** Azure Backup 내에서 콘텐츠를 정기적으로 복원 하는 기능을 보장 합니다. 필요한 경우 격리 된 가상 네트워크 또는 구독에 대 한 콘텐츠 복원을 테스트 합니다. 또한 백업 된 고객이 관리 하는 키의 복원을 테스트 합니다.

Azure disk encryption을 사용 하는 경우 디스크 암호화 키를 사용 하 여 Azure VM을 복원할 수 있습니다. 디스크 암호화를 사용 하는 경우 디스크 암호화 키를 사용 하 여 Azure VM을 복원할 수 있습니다.

- [암호화를 사용 하는 Azure Vm에서 백업](../backup/backup-azure-vms-encryption.md)
- [Azure VM 백업에서 파일을 복구 하는 방법](../backup/backup-azure-restore-files-from-vm.md)
- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [암호화 된 VM을 백업 및 복원 하는 방법](../backup/backup-azure-vms-encryption.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.
**지침:** Azure Backup를 사용 하 여 관리 디스크를 백업 하는 경우 Vm은 저장소 서비스 암호화 (SSE)를 사용 하 여 미사용 상태에서 암호화 됩니다. Azure Backup Azure Disk Encryption를 사용 하 여 암호화 된 Azure Vm을 백업할 수도 있습니다. Azure Disk Encryption는 키 자격 증명 모음에 비밀으로 보호는 BEKs (BitLocker 암호화 키)와 통합 됩니다. Azure Disk Encryption은 KEKs (Azure Key Vault 키 암호화 키)와도 통합 됩니다. Key Vault에서 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

- [Vm에 대 한 일시 삭제](../backup/soft-delete-virtual-machines.md)
- [Azure Key Vault-일시 삭제 개요](../key-vault/general/soft-delete-overview.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

## <a name="incident-response"></a>사고 대응
*자세한 내용은 [보안 그룹: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기
**지침:** 조직에 대 한 인시던트 대응 가이드를 작성 합니다. 모든 인원의 역할 및 인시던트 처리/관리의 단계를 정의 하는 사고 대응 계획을 검색 하 여 인시던트 사후 검토에서 확인 합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기
**지침:** Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석에 사용 되는 것과 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 Security Center 따라 달라 집니다.

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)
- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트
**지침:** 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램에 대 한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성
**지침:** Microsoft 보안 대응 센터 (MSRC)에서 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 Microsoft에서 사용자에 게 연락 하는 보안 인시던트 연락처 정보를 사용 합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합
**지침:** 연속 내보내기 기능을 사용 하 여 Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되는 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)
- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

#### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화
**지침:** Azure Security Center의 워크플로 자동화 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)
 
Azure Security Center 모니터링: * * * * 해당 사항 없음

**책임:** Customer


## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습
*자세한 내용은 보안 제어: [침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 60일 이내에 수정
**지침:** Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft cloud red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링:** 해당 없음

**책임:** 공유할

## <a name="next-steps"></a>다음 단계
다음 문서를 참조 하세요.

- [환경에 대 한 보안 경고 Azure DevTest Labs](environment-security-alerts.md)
