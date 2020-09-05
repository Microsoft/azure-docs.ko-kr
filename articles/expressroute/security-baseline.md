---
title: Azure Express 경로에 대 한 보안 기준
description: Express 경로에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 69a47820d2afaf24f413aabf2c59a40c032070c1
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231387"
---
# <a name="azure-security-baseline-for-expressroute"></a>Express 경로에 대 한 Azure 보안 기준

Express 경로에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 해당 사항 없음 Express 경로는 고가용성을 보장 하기 위해 중복 회로 쌍으로 사용 됩니다. ExpressRoute 연결은 공용 인터넷을 통해 이동하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: 해당 사항 없음 게이트웨이 서브넷을 사용 하는 경우 게이트웨이 서브넷에 NSG (네트워크 보안 그룹)를 연결 하지 않는 것이 좋습니다. 네트워크 보안 그룹을이 서브넷에 연결 하면 Virtual Network 게이트웨이 (VPN, Express 경로 게이트웨이)가 예상 대로 작동 하지 않을 수 있습니다.

* [Azure Express 경로 게이트웨이 요구 사항 이해](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#requirements)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 해당 사항 없음 각 고객의 연결할 expressroutes 자체의 라우팅 도메인 내에 포함 되 고 자체 가상 네트워크로 터널링 됩니다. 연결할 expressroutes 격리 되는 반면 동일한 가상 네트워크를 공유 하는 다른 리소스에 대 한 추가 보호를 위해 DDoS Protection Standard를 사용 하 여 DDoS 공격 으로부터 보호할 수 있습니다.

* [Azure Express 경로에 대 한 보안 제어 이해](https://docs.microsoft.com/azure/expressroute/expressroute-security-controls)

* [DDoS 보호를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 해당 사항 없음 패키지 캡처는 IaaS 계산 리소스 (Azure virtual machines)에 대해서만 지원 됩니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 해당 사항 없음 각 고객의 연결할 expressroutes 자체의 라우팅 도메인 내에 포함 되 고 자체 가상 네트워크로 터널링 됩니다.

* [Azure Express 경로에 대 한 보안 제어 이해](https://docs.microsoft.com/azure/expressroute/expressroute-security-controls)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 해당 사항 없음 Azure Express 경로 자체는 서비스 태그나 네트워크 보안 그룹을 사용 하 여 트래픽을 필터링 하거나 허용할 수 있는 끝점이 아닙니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 Azure express 경로에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 사용자 Express의 네트워크 구성을 감사 하거나 적용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure express 경로 인스턴스에 태그를 사용 하 여 메타 데이터 및 논리 조직을 제공 합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

* [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 express 경로 연결과 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 리소스의 변경 내용이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다.

* [Azure 센티널에서 감사를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sentinel/resources)

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: 해당 사항 없음 Microsoft는 로그의 타임 스탬프에 대해 Azure 센티널와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure 활동 로그 진단 설정을 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. 활동 로그는 제어 평면 수준에서 Azure Express 경로 리소스에 대해 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 Express 경로 리소스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure 활동 로그 진단 설정을 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. 활동 로그는 제어 평면 수준에서 Azure Express 경로 리소스에 대해 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 Express 경로 리소스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure express 경로 리소스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

* [로그 보존 매개 변수를 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, Azure Express 경로에 대해 수집 되었을 수 있는 활동 로그 데이터를 기반으로 다양 한 통찰력을 제공 합니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure express 경로 리소스와 관련 된 메트릭 및 활동 로그를 기반으로 경고를 받도록를 구성할 수 있습니다. Azure Monitor를 사용 하면 전자 메일 알림을 보내거나 웹 후크를 호출 하거나 Azure 논리 앱을 호출 하도록 경고를 구성할 수 있습니다.

* [Express 경로에서 모니터링 및 경고 이해](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Azure Express 경로는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Express 경로는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure express 경로 리소스의 제어 평면 (예: Azure Portal)에 대 한 관리 권한이 있는 사용자 계정의 인벤토리를 유지 관리 합니다.

구독에 대 한 Azure Portal의 IAM (Id 및 액세스 제어) 창을 사용 하 여 azure RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다. 역할은 Active Directory의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다.

또한 Express 경로 파트너 리소스 관리자 API를 사용 하는 파트너는 역할 기반 Access Control를 expressRouteCrossConnection 리소스에 적용할 수 있습니다. 이러한 컨트롤은 사용자 계정에서 expressRouteCrossConnection 리소스를 수정 하 고 피어 링 구성을 추가/업데이트/삭제할 수 있는 권한을 정의할 수 있습니다.

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Express 경로 파트너 리소스 관리자 API에서 Azure RBAC 활용](https://docs.microsoft.com/azure/expressroute/cross-connections-api-development)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure AD에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 강제로 만들도록 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 marketplace 서비스를 담당 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

* [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy를 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 해당 사항 없음 SSO (Single Sign-On)는 사용자가 Azure Active Directory (AD)에서 사용자 지정 응용 프로그램에 로그온 할 때 보안 및 편리 함을 추가 합니다. Azure Express 경로 제어 평면 (예: Azure Portal)에 대 한 액세스는 이미 Azure Active Directory에 통합 되어 있으며 Azure Portal 및 Azure Resource Manager REST API를 통해 액세스 됩니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory Multi-Factor Authentication를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: MFA (azure Multi-Factor Authentication)를 사용 하는 PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 azure 센티널 관련 리소스에 로그인 하 고 구성 합니다.

* [권한 있는 액세스 워크스테이션](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [클라우드 기반 Azure Multi-Factor Authentication 배포 계획](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다.

또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

* [PIM(Privileged Identity Management)을 배포하는 방법](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Azure AD 위험 탐지 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure 센티널 인스턴스에 대 한 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

* [Azure AD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: AD (Azure Active Directory)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

* [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure express 경로 리소스에 대 한 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며,이를 통해 Azure 센티널 또는 타사 SIEM과 통합할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: 제어 평면의 계정 로그인 동작 편차 (예: Azure Portal)의 경우 Azure AD ID 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 사항 없음 고객 Lockbox는 Azure Express 경로에 대해 지원 되지 않습니다.

* [Customer Lockbox 지원 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Microsoft는 Azure express 경로 회로 및 관련 리소스에 대 한 기본 인프라를 관리 하 고, 고객 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: IPSEC은 IETF 표준입니다. IP (인터넷 프로토콜) 수준이 나 네트워크 계층 3에서 데이터를 암호화 합니다. IPsec을 사용 하 여 온-프레미스 네트워크와 Azure의 가상 네트워크 (VNET) 간에 종단 간 연결을 암호화할 수 있습니다.

* [Express 경로를 통해 사이트 간 IPSEC을 구성 하는 방법](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)

Express 경로를 통해 사이트 간 IPSEC을 구성 하는 방법: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 해당 사항 없음 Azure Express 경로는 고객 데이터를 저장 하지 않습니다.

* [Azure Express 경로에 대 한 보안 제어 이해](https://docs.microsoft.com/azure/expressroute/expressroute-security-controls)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: 구독에 대 한 AZURE PORTAL의 IAM (Id 및 액세스 제어) 창을 사용 하 여 azure RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다. 역할은 Active Directory의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다. 개인 및 그룹에 대 한 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다.

Azure Express 경로에는 회로 소유자 및 회로 사용자 역할도 있습니다. 회로 사용자는 ExpressRoute 회로와 동일한 구독 내에 있지 않은 가상 네트워크 게이트웨이의 소유자입니다. 회로 소유자는 언제든지 부여된 권한을 수정하고 해지할 수 있습니다. 권한 부여를 해지하면 액세스가 해지된 구독에서 모든 링크 연결이 삭제됩니다. 회로 사용자는 가상 네트워크당 하나의 권한 부여를 사용할 수 있습니다.

또한 Express 경로 파트너 리소스 관리자 API를 사용 하는 파트너는 역할 기반 Access Control를 expressRouteCrossConnection 리소스에 적용할 수 있습니다. 이러한 컨트롤은 사용자 계정에서 expressRouteCrossConnection 리소스를 수정 하 고 피어 링 구성을 추가/업데이트/삭제할 수 있는 권한을 정의할 수 있습니다.

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Express 경로 파트너 리소스 관리자 API에서 Azure RBAC 활용](https://docs.microsoft.com/azure/expressroute/cross-connections-api-development)

* [Express 경로에서 관리 역할 이해](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager#connect-a-vnet-to-a-circuit---different-subscription)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 Azure 센티널의 기본 인프라를 관리 하 고 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

* [Azure 고객 데이터 보호](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: MACSEC는 IEEE 표준입니다. MAC (미디어 액세스 제어) 수준 또는 네트워크 계층 2에서 데이터를 암호화 합니다. Express 경로 직접를 통해 Microsoft에 연결 하는 경우 MACsec를 사용 하 여 네트워크 장치와 Microsoft 네트워크 장치 간의 실제 링크를 암호화할 수 있습니다. MACsec은 기본적으로 Express 경로 직접 포트에서 사용 하지 않도록 설정 됩니다. 암호화에 대 한 사용자 고유의 MACsec 키를 가져와서 Azure Key Vault에 저장 합니다. 키를 회전 하는 시기를 결정 합니다.

* [Azure Express 경로에서 지점 간 암호화 이해](https://docs.microsoft.com/azure/expressroute/expressroute-about-encryption)

* [Express 경로 직접 포트에서 MACsec를 구성 하는 방법](https://docs.microsoft.com/azure/expressroute/expressroute-howto-macsec)

* [Key Vault에 대 한 Azure 보안 기준](https://docs.microsoft.com/azure/key-vault/general/security-baseline)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: azure 활동 로그와 함께 Azure Monitor를 사용 하 여 변경 내용이 azure express 경로 및 기타 중요 하거나 관련 된 리소스의 프로덕션 인스턴스에 적용 되는 경우에 대 한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 해당 사항 없음 Microsoft는 Azure Express 경로를 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 해당 사항 없음 Microsoft는 Azure Express 경로를 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 해당 사항 없음 Microsoft는 Azure Express 경로를 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 Asset Discovery 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure Policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스의 유형에 대 한 제한을 설정할 수 있습니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy을 사용 하 여 Azure express 경로에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 사용자 Express의 네트워크 구성을 감사 하거나 적용 합니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure express 경로에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 사용자 Express의 네트워크 구성을 감사 하거나 적용 합니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 네트워크" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure 리소스에 대 한 구성을 자동으로 적용 하려면 [감사], [deny] 및 [없는 경우 배포] Azure Policy 사용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 사용자가 Express 경로 직접를 통해 microsoft에 연결 하는 경우 macsec를 사용 하 여 네트워크 장치와 microsoft 네트워크 장치 간의 실제 링크를 암호화할 수 있습니다. MACsec은 기본적으로 Express 경로 직접 포트에서 사용 하지 않도록 설정 됩니다. 암호화에 대 한 사용자 고유의 MACsec 키를 가져와서 Azure Key Vault에 저장 합니다. 키를 회전 하는 시기를 결정 합니다.

* [새 리소스 그룹에 MACsec 암호를 저장 하는 Key Vault 인스턴스를 만드는 방법](https://docs.microsoft.com/azure/expressroute/expressroute-howto-macsec)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 해당 사항 없음 Azure Express 경로 연결 및 리소스는 관리 되는 id를 사용 하지 않습니다.

* [관리 id를 지 원하는 Azure 서비스](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스 (예: Azure App Service)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 azure 서비스 (예: azure express 경로)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

비 계산 Azure 리소스에 업로드 되는 모든 콘텐츠를 미리 검색 하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해 서 고객 콘텐츠의 맬웨어 방지 검색을 수행할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 해당 사항 없음 Azure Express 경로는 고객 데이터를 저장 하지 않습니다.

* [Azure Express 경로에 대 한 보안 제어 이해](https://docs.microsoft.com/azure/expressroute/expressroute-security-controls)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Key Vault을 사용 하 여 macsec 암호를 저장 하는 경우 키의 자동 백업을 정기적으로 수행 해야 합니다.

* [Key Vault 키를 백업 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 백업 된 고객이 관리 하는 키의 복원 테스트

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [MACsec 구성 후 Azure Express 경로 회로의 연결을 테스트 하는 방법](https://docs.microsoft.com/azure/expressroute/expressroute-howto-macsec)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호 합니다.

* [Key Vault에서 일시 삭제를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

* [Azure Security Center 내에서 워크플로 자동화를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

* [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고 Sentinel을 스트리밍할 수 있습니다.

* [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

* [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화와 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: * [Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 자세한 내용은 Microsoft에서 확인할 수 있습니다.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
