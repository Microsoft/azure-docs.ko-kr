---
title: Azure Firewall에 대한 Azure 보안 기준
description: Azure Firewall 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: firewall
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ab3f651c610127399da83addd6463ae8cb3748a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565251"
---
# <a name="azure-security-baseline-for-azure-firewall"></a>Azure Firewall에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview-v1.md)의 지침을 Azure Firewall에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
내용은 Azure Security Benchmark에서 정의된 **보안 컨트롤** 및 Azure Firewall에 적용되는 관련 지침에 따라 그룹화됩니다. Azure Firewall에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

 
Azure Firewall이 Azure Security Benchmark에 완전히 매핑되는 방식을 확인하려면 [전체 Azure Firewall 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: Azure Firewall이 방화벽에 의해 처리되는 트래픽을 로깅할 수 있도록 Azure Monitor와 통합됩니다.

또한 Azure Security Center를 사용하고 네트워크 보호 권장 사항을 따라 Azure Firewall과 관련된 네트워크 리소스를 보호합니다.

- [Azure Security Center에서 제공하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 위협 인텔리전스 기반 필터링을 사용하도록 설정하여 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부합니다. 방화벽에서 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있도록 하기 위해 위협 인텔리전스 기반 필터링을 사용하도록 설정할 수 있습니다.

- [Azure Firewall 위협 인텔리전스 기반 필터링](threat-intel.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Firewall에서 서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다.

Azure Firewall 서비스 태그는 네트워크 규칙 대상 필드에서 사용하고 Azure Firewall에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다.

또한 IP 그룹과 같은 고객 정의 태그도 지원되며 네트워크 규칙 또는 애플리케이션 규칙에 사용될 수 있습니다. 애플리케이션 규칙에 FQDN 태그가 지원되어 방화벽을 통해 필요한 아웃바운드 네트워크 트래픽을 허용할 수 있습니다.

자체 서비스 태그를 직접 만들거나 태그 내에 포함되는 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

 

- [Azure Firewall 서비스 태그](service-tags.md)

- [사용 가능한 서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)

- [Azure Firewall의 IP 그룹](ip-groups.md)

- [FQDN 태그 개요](fqdn-tags.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure 정책은 아직 Azure Firewall에 대해 완전히 지원되지 않습니다. Azure Firewall Manager를 사용하여 보안 구성의 표준화를 달성할 수 있습니다.

또한 Azure Blueprints에서 주요 환경 아티팩트(예: Azure Resource Manager 템플릿, Azure RBAC 제어 및 정책)를 단일 블루프린트 정의로 패키지하여 대규모 Azure 배포를 간소화할 수 있습니다. 블루프린트를 새로운 구독에 적용하고 버전 관리를 통해 제어 및 관리를 세부적으로 조정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용하여 리소스 구성을 모니터링하고 Azure Firewall 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure Firewall 로그 및 메트릭 모니터링](firewall-diagnostics.md)

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft가 Azure Firewall용 Azure 리소스에 대한 시간 원본을 유지 관리합니다. 고객은 이 액세스를 허용하는 네트워크 규칙 또는 해당 환경에서 사용하는 시간 서버를 만들어야 합니다.

- [NTP 서버 액세스](./protect-windows-virtual-desktop.md#additional-considerations)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 다양한 로그의 중앙 보안 로그 관리를 위해 로그 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

활동 로그를 사용하여 Azure Firewall에 대한 작업을 감사하고 리소스에 대한 작업을 모니터링할 수 있습니다. 활동 로그에는 읽기 작업(GET)을 제외하고 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 활동 로그를 사용하면 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

또한 Azure Firewall은 고객 애플리케이션 및 네트워크 규칙에 대한 정보를 제공하기 위해 다음 진단 로그를 제공합니다.

애플리케이션 규칙 로그: 새 연결이 구성된 애플리케이션 규칙 중 하나와 일치할 때마다 허용/거부된 연결에 대한 로그가 생성됩니다.

네트워크 규칙 로그: 새 연결이 구성된 네트워크 규칙 중 하나와 일치할 때마다 허용/거부된 연결에 대한 로그가 생성됩니다.

참고: 두 로그는 스토리지 계정에 저장하거나 이벤트 허브로 스트리밍할 수 있으며 환경의 각 Azure Firewall에 대해 사용하도록 설정된 경우에 한해 Azure Monitor 로그로 보낼 수 있습니다.

- [Azure Firewall 로그](logs-and-metrics.md)

활동 로그의 리소스 작업 목록: Azure Resource Manager 리소스 공급자 작업

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 활동 로그를 사용하여 Azure Firewall에 대한 작업을 감사하고 리소스에 대한 작업을 모니터링할 수 있습니다. 활동 로그에는 읽기 작업(GET)을 제외하고 Azure 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 또한 Azure Firewall은 고객 애플리케이션 및 네트워크 규칙에 대한 정보를 제공하기 위해 다음 진단 로그를 제공합니다. 

애플리케이션 규칙 로그: 새 연결이 구성된 애플리케이션 규칙 중 하나와 일치할 때마다 허용/거부된 연결에 대한 로그가 생성됩니다.

네트워크 규칙 로그: 새 연결이 구성된 네트워크 규칙 중 하나와 일치할 때마다 허용/거부된 연결에 대한 로그가 생성됩니다.

두 로그는 스토리지 계정에 저장하거나 이벤트 허브로 스트리밍할 수 있으며 환경의 각 Azure Firewall에 대해 사용하도록 설정된 경우에 한해 Azure Monitor 로그로 보낼 수 있습니다.

- [Azure Firewall 로그](logs-and-metrics.md)

- [활동 로그의 리소스 작업 목록](../role-based-access-control/resource-provider-operations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Log Analytics 작업 영역 보존 기간은 조직의 Azure Monitor 내 규정 준수 규칙에 따라 설정할 수 있습니다. 데이터 보존은 선택한 가격 책정 계층에 따라 모든 작업 영역에 대해 30~730일(2년)의 범위에서 구성할 수 있습니다.

로그 스토리지 보존을 저장하는 세 가지 옵션이 있습니다.

- 로그를 장기간 스토리지하고 필요할 때 검토하는 경우에 가장 적합합니다.

- 다른 SEIM(보안 정보 및 이벤트 관리) 도구와 통합하여 리소스에 대한 알림을 얻을 수 있는 좋은 옵션입니다.

- Azure Monitor 로그는 일반적으로 애플리케이션을 실시간으로 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

자세한 내용은 아래 참조 링크를 참조하세요.

- [Azure Firewall 로그 및 메트릭](logs-and-metrics.md)

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/logs/manage-cost-storage.md)

- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Firewall이 방화벽 로그를 살펴보고 분석할 수 있도록 Azure Monitor와 통합됩니다. 로그를 Log Analytics, Azure Storage 또는 Event Hubs로 전송할 수 있습니다. 전송된 로그를 Log Analytics 또는 Excel이나 Power BI 같은 다른 도구에서 분석할 수 있습니다. Azure Firewall 로그는 몇 가지 유형이 있습니다.

활동 로그를 사용하여 Azure Firewall에 대한 작업을 감사하고 리소스에 대한 작업을 모니터링할 수 있습니다. 활동 로그에는 읽기 작업(GET)을 제외하고 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)이 포함됩니다. 활동 로그를 사용하면 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링할 수 있습니다.

또한 Azure Firewall은 고객 애플리케이션 및 네트워크 규칙에 대한 정보를 제공하기 위해 진단 로그를 제공합니다.

애플리케이션 규칙 로그는 새 연결이 구성된 애플리케이션 규칙 중 하나와 일치할 때마다 허용/거부된 연결에 대한 로그가 생성되는 경우에 만들어집니다. 

네트워크 규칙 로그는 새 연결이 구성된 네트워크 규칙 중 하나와 일치할 때마다 허용/거부된 연결에 대한 로그가 생성되는 경우에 만들어집니다.

두 로그는 스토리지 계정에 저장하거나 이벤트 허브로 스트리밍할 수 있으며 환경의 각 Azure Firewall에 대해 사용하도록 설정된 경우에 한해 Azure Monitor 로그로 보낼 수 있습니다.

Azure Monitor 로그는 일반적으로 애플리케이션을 실시간으로 모니터링하거나 추세를 파악하는 데 사용할 수 있습니다.

- [Azure Firewall 로그 및 메트릭](logs-and-metrics.md)

- [진단 로그](./logs-and-metrics.md#diagnostic-logs)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용합니다. 

또는, 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리하는 방법](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics 로그 데이터에 관해 경고하는 방법](../azure-monitor/alerts/tutorial-response.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure AD(Azure Active Directory)에 명시적으로 할당되고 쿼리할 수 있어야 하는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행해서 관리 그룹의 구성원인 계정을 검색합니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 Azure AD(Azure Active Directory) Privileged Identity Management Privileged Roles for Microsoft Services와 Azure Resource Manager를 사용하여 Just-In-Time/Just-Enough-Access를 사용하도록 설정할 수도 있습니다.

- [Privileged Identity Management에 대해 자세히 알아보기](../active-directory/privileged-identity-management/index.yml)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: 가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure AD(Azure Active Directory) SSO를 사용합니다. Azure Security Center ID 및 액세스 관리 권장 사항을 사용합니다.

- [Azure AD를 사용한 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure Firewall 및 관련 리소스에 로그인하여 구성하도록 다단계 인증이 구성된 PAW(Privileged Access Workstation)를 사용합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](/security/compass/privileged-access-devices) 
 
- [Azure에서 다단계 인증을 사용하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure AD(Azure Active Directory) 보안 보고서를 사용하여 환경에서 의심스럽거나 안전하지 않은 활동이 발생하면 로그 및 경고를 생성합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다. 

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다. 

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory)는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 엔터프라이즈 애플리케이션에 관한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

- [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 소스에 액세스가 가능하므로 SIEM/모니터링 툴과 통합할 수 있습니다.

Azure AD 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory) 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련하여 감지된 의심스러운 동작에 대한 자동 응답을 구성합니다. 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure Firewall 및 관련 리소스를 추적할 수 있도록 지원합니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형, 데이터 민감도 수준 등의 개별 보안 도메인에 별도의 구독 및 관리 그룹을 사용하여 격리를 구현합니다. 애플리케이션 및 엔터프라이즈 환경에서 요구하는 Azure Firewall 리소스에 대한 액세스 수준을 제한할 수 있습니다. Azure 역할 기반 액세스 제어를 통해 Azure 리소스에 대한 액세스를 제어할 수 있습니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에서 중요한 정보의 무단 전송을 모니터링하고 정보 보안 전문가에게 경고하면서 해당 전송을 차단하는 Azure Marketplace 타사 솔루션을 사용합니다. 

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터가 손실되거나 노출되지 않게 보호합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요한 정보 암호화. Azure Firewall 및 관련 리소스에 연결하는 모든 클라이언트가 TLS 1.2 이상과 협상할 수 있도록 합니다. 

해당하는 경우 미사용 암호화 및 전송 중 암호화에 대한 Azure Security Center의 권장 사항을 따릅니다. 

- [Azure를 통한 전송 중 데이터 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 타사 활성 검색 도구를 사용하여 Azure Firewall 및 관련 리소스를 통해 Azure 리소스에 저장된 모든 중요한 정보를 식별하고 조직의 중요한 정보 인벤토리를 업데이트 합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어 

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure Firewall 및 관련 리소스에 대한 액세스를 제어합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Firewall 및 관련 리소스를 사용하여 모든 Azure 리소스에서 미사용 데이터 암호화를 사용합니다. Microsoft는 Azure에서 암호화 키를 관리하도록 허용하는 것을 권장하지만 일부 인스턴스에서는 사용자 고유의 키를 관리할 수 있는 옵션이 있습니다. 

- [Azure의 저장 데이터 암호화 이해](../security/fundamentals/encryption-atrest.md)

- [고객 관리형 암호화 키를 구성하는 방법](../storage/common/customer-managed-keys-configure-key-vault.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그에 Azure Monitor를 사용하여 Azure Firewall이 변경될 때 경고를 생성합니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure Firewall 및 관련 리소스에 태그를 적용하여 논리적으로 분류 체계로 구성합니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹, 별도의 구독을 사용하여 Azure Firewall 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 구성을 포함하여 승인된 Azure Firewall 리소스의 인벤토리를 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy를 사용하여 구독에서 만들 수 있는 리소스 종류를 제한합니다.

Azure Resource Graph를 사용하여 구독 내에서 Azure Firewall 리소스를 쿼리/검색합니다. 환경에 존재하는 모든 Azure Firewall 및 관련 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 권한이 없는 Azure Firewall 및 관련 리소스를 제거하는 자체 프로세스를 구현합니다. 타사 솔루션을 사용하여 승인되지 않은 Azure Firewall 및 관련 리소스를 식별할 수도 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy를 사용하여 환경에서 프로비전할 수 있는 서비스를 제한합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/concepts/effects.md#deny)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 'Microsoft Azure Management' 앱에 대한 '액세스 차단'을 구성함으로써 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다. 

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요할 수 있는 애플리케이션 또는 조직 위험 프로필이 다른 환경을 별도의 Azure Firewall 인스턴스를 사용하여 격리 및 분리해야 합니다.

- [Azure Portal을 사용하여 Azure Firewall 배포 및 구성](deploy-cli.md)

- [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

- [보안 구성을 사용하여 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Resource Manager는 구성이 조직의 보안 요구 사항을 충족하거나 초과하는지 확인하기 위해 검토해야 하는 JSON(JavaScript Object Notation)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스 관련 보안 구성 기준으로 사용할 수도 있습니다.

현재 Azure 정책은 Azure Firewall에 대해 완전히 지원되지 않습니다. 

- [Azure Portal에서 템플릿으로 단일 리소스 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure Firewall 및 관련 리소스 전체에 적용합니다.  또한 Azure Resource Manager 템플릿을 사용하여 조직에서 요구하는 Azure Firewall 및 관련 리소스의 보안 구성을 유지 관리할 수 있습니다.

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure 정책 및 Azure Resource Manager 템플릿과 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹 관련 권한을 부여하거나 거부하면 됩니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure DevOps의 그룹 및 사용 권한 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Policy를 사용하여 Azure Firewall 및 관련 리소스를 위한 표준 보안 구성을 정의하고 구현합니다. Azure Policy 별칭을 사용하여 Azure Firewall 리소스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 특정 리소스와 관련된 기본 제공 정책 정의도 사용할 수 있습니다.  

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 ID를 사용하여 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하면 Azure Resource Manager에 대한 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있고 API/Azure Portal/CLI/PowerShell에서 사용할 수 있습니다.

- [관리 ID를 구성하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Resource Manager를 사용하여 Azure Firewall 및 관련 구성을 위한 백업으로 사용할 수 있는 JSON(JavaScript Object Notation) 템플릿에서 Azure Firewall 및 관련 리소스를 내보냅니다.  Azure Portal에서 Azure Firewall의 템플릿 내보내기 기능을 사용하여 Azure Firewall 구성을 내보낼 수도 있습니다.  Azure Automation을 사용하여 자동으로 백업 스크립트를 실행합니다.

- [Azure Portal에서 템플릿으로 단일 리소스 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [템플릿을 사용하여 Azure Firewall 배포](deploy-template.md)

- [Microsoft Network Azure Firewalls 템플릿 참조](/azure/templates/microsoft.network/azurefirewalls)

- [Azure Automation 소개](../automation/automation-intro.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Resource Manager를 사용하여 Azure Firewall 및 관련 구성을 위한 백업으로 사용할 수 있는 JSON(JavaScript Object Notation) 템플릿에서 Azure Firewall 및 관련 리소스를 내보냅니다.  Azure Portal에서 Azure Firewall의 템플릿 내보내기 기능을 사용하여 Azure Firewall 구성을 내보낼 수도 있습니다.

- [템플릿을 사용하여 Azure Firewall 배포](deploy-template.md)

- [Microsoft Network Azure Firewalls 템플릿 참조](/azure/templates/microsoft.network/azurefirewalls)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure Resource Manager 템플릿 지원 파일을 사용하여 정기적으로 복원하도록 합니다.  

- [템플릿을 사용하여 Azure Firewall 배포](deploy-template.md)

- [Microsoft Network Azure Firewalls 템플릿 참조](/azure/templates/microsoft.network/azurefirewalls)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure DevOps를 사용하여 사용자 지정 Azure 정책 및 Azure Resource Manager 템플릿과 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스를 보호하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory) 또는 TFS와 통합된 경우 Active Directory에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹을 위한 권한을 부여하거나 거부하면 됩니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure DevOps의 그룹 및 사용 권한 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다. 

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다. 

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다. 

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다.

연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 'Logic Apps'를 통해 응답을 자동으로 트리거함으로써 Azure 리소스를 보호합니다. 

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft 관리형 클라우드 인프라, 서비스, 애플리케이션에 대한 Microsoft의 전략과 Red Teaming 및 라이브 사이트 침투 테스트의 실행을 사용합니다. 

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
