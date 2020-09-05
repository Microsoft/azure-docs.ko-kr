---
title: Data Lake Analytics에 대 한 Azure 보안 기준
description: Data Lake Analytics 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4a02fc8a04da658b3b6cbbb41e57114d6c4a0527
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231489"
---
# <a name="azure-security-baseline-for-data-lake-analytics"></a>Data Lake Analytics에 대 한 Azure 보안 기준

Data Lake Analytics에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Data Lake Analytics에 대 한 방화벽 설정을 사용 하 여 온-프레미스 클라이언트 및 타사 서비스에서의 액세스를 허용 하도록 외부 IP 범위를 제한 합니다. 방화벽 설정 구성은 포털, REST Api 또는 PowerShell을 통해 사용할 수 있습니다.

* [방화벽 규칙](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: 해당 사항 없음 Azure Data Lake Analytics 가상 네트워크에서 실행 되지 않으며 페더레이션된 쿼리를 사용 하는 경우 고객 가상 네트워크를 통해 라우팅하도록 나가는 호출을 구성할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 사항 없음 이 컨트롤은 Azure App Service 또는 IaaS 인스턴스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Data Lake Analytics에 대 한 방화벽 설정을 사용 하 여 온-프레미스 클라이언트 및 타사 서비스에서의 액세스를 허용 하도록 외부 IP 범위를 제한 합니다. 방화벽 설정 구성은 포털, REST Api 또는 PowerShell을 통해 사용할 수 있습니다.

* [방화벽 규칙](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: 해당 사항 없음 Data Lake Analytics는 고객 가상 네트워크 내에서 실행 되지 않으며 NSGs (네트워크 보안 그룹)를 사용 하 여 네트워크 흐름 로그를 기록할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 해당 사항 없음 Data Lake Analytics은 고객 네트워크에 배포 되지 않는 PaaS 제품입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 사항 없음 이 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 해당 사항 없음 Data Lake Analytics는 고객 가상 네트워크 내에서 실행 되지 않으며 NSGs (네트워크 보안 그룹)를 사용할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: 해당 사항 없음 Data Lake Analytics는 고객 가상 네트워크 내에서 실행 되지 않으며 NSGs (네트워크 보안 그룹)를 사용할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 해당 사항 없음 Data Lake Analytics는 고객 가상 네트워크 내에서 실행 되지 않으며 NSGs (네트워크 보안 그룹)를 사용할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: 해당 사항 없음 Data Lake Analytics는 고객 가상 네트워크 내에서 실행 되지 않으며 NSGs (네트워크 보안 그룹)를 사용할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: 해당 사항 없음 Microsoft는 Data Lake Analytics에 대 한 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 ' 감사 ' 및 ' 요청 ' 진단 Data Lake Analytics 같은 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고, 장기/보관 저장소에 대 한 Azure Storage 계정을 사용 하 고, 필요에 따라 변경 불가능 한 저장소 및 적용 된 보존 유지와 같은 보안 기능을 사용 합니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정할 수 있습니다.

* [Azure Data Lake Analytics에 대한 진단 로그에 액세스](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사 및 요청 로그에 액세스 하는 Data Lake Analytics에 대 한 진단 설정을 사용 하도록 설정 합니다. 여기에는 이벤트 원본, 날짜, 사용자, 타임 스탬프 및 기타 유용한 요소와 같은 데이터가 포함 됩니다.

* [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Azure의 로깅 및 다른 로그 유형 이해](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기 및 보관 저장소에 Azure Storage 계정을 사용 합니다.

* [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 Data Lake Analytics 리소스에 대 한 결과를 정기적으로 검토 합니다. Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Log Analytics 작업 영역에 대 한 자세한 내용](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Data Lake Analytics에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공하므로 Log Analytics 작업 영역을 Azure Sentinel에 온보딩합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Log analytics 로그 데이터를 경고 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

* [Azure Data Lake Analytics에 대한 진단 로그에 액세스](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Data Lake Analytics는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: DNS 로깅 솔루션에 대 한 Azure Marketplace에서 조직의 요구에 따라 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 사항 없음 이 컨트롤은 고객이 기본 운영 체제에 액세스할 수 있는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure AD에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: azure RBAC (역할 기반 액세스 제어)를 통해 Azure Active Directory 및 보안을 통해 인증이 제공 되므로 Data Lake Analytics는 기본 암호의 개념이 없습니다.

* [Azure Data Lake Analytics 개요](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다.

Azure AD Privileged Identity Management 및 Azure Resource Manager를 사용 하 여 Just-in-time 액세스를 사용 하도록 설정할 수도 있습니다.

* [Privileged Identity Management에 대 한 자세한 정보](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: SSO (Azure Active Directory Single Sign-On)를 사용 하십시오.

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center id 및 액세스 권장 사항을 사용 합니다.

* [Azure AD를 사용 하 여 SSO 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory multi-factor AUTHENTICATION (MFA)을 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 관리 권장 사항을 따라 Data Lake Analytics 리소스를 보호 합니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: 높은 권한이 필요한 관리 작업에 대해 Azure에서 관리 하는 안전한 워크스테이션 (권한 있는 액세스 워크스테이션 또는 PAW 라고도 함)을 사용 합니다.

* [안전 하 고 Azure로 관리 되는 워크스테이션 이해](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-managed-workstation)

* [Azure AD MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고 생성에 Azure Active Directory 보안 보고서를 사용 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

* [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: Azure AD 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.

* [Azure AD 명명 된 위치를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure RBAC (역할 기반 액세스 제어)는 클라이언트에서 Data Lake Analytics 리소스에 대 한 액세스를 세부적으로 제어할 수 있도록 합니다.

* [Azure AD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

* [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure AD id 및 액세스 검토를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Data Lake Analytics 및 Azure Active Directory에 대 한 진단 설정을 사용 하도록 설정 하 여 모든 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고 (예: 비활성화 된 암호에 대 한 액세스 시도)를 구성 합니다.

* [Azure Monitor 로그와 Azure AD 로그 통합](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory의 위험 및 id 보호 기능을 사용 하 여 Data Lake Analytics 리소스와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 조직의 보안 응답을 구현 하기 위해 Azure 센티널을 통해 자동화 된 응답을 사용 하도록 설정 해야 합니다.

* [Azure AD 위험한 로그인을 확인하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 사항 없음 Azure Data Lake Analytics에 대해 고객 Lockbox 지원 되지 않습니다.

* [일반 공급에서 지원 되는 서비스 및 시나리오](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Data Lake Analytics 리소스를 추적 하는 데 도움을 줍니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 별도의 구독, 환경, 데이터 민감도 등의 개별 보안 도메인에 대 한 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Data Lake Analytics 리소스에 대 한 액세스 수준을 제어 하도록 Data Lake Analytics를 제한할 수 있습니다. 방화벽 규칙이 구성 된 경우 지정 된 네트워크 집합을 통해 데이터를 요청 하는 응용 프로그램만 Data Lake Analytics 리소스에 액세스할 수 있습니다. Azure RBAC를 통해 Azure Data Lake Analytics에 대 한 액세스를 제어할 수 있습니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [역할 기반 Access Control 관리](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

* [방화벽 규칙](https://docs.microsoft.com/rest/api/datalakeanalytics/firewallrules)

* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-powershell)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 데이터 손실 방지 기능은 아직 Azure Data Lake Analytics 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

* [Azure Storage 계정을 보호 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-security-guide)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Microsoft Azure 리소스는 기본적으로 TLS 1.2을 협상 합니다. Data Lake Analytics에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 사용 하 여 협상할 수 있는지 확인 합니다.

* [예제 작업 목록](https://docs.microsoft.com/rest/api/datalakeanalytics/operations/list)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별 기능은 아직 Azure Data Lake Analytics 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자가 서비스와 상호 작용 하는 방식을 제어 합니다.

* [Azure RBAC 관리](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-manage-use-portal#manage-role-based-access-control)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 데이터는 기본 Data Lake Storage Gen1 계정에 저장 됩니다. Data Lake Storage Gen1은 미사용 데이터에 대해 기본적으로 투명한 암호화를 지원합니다.

* [Azure Data Lake Storage Gen1의 데이터 암호화](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 변경 내용이 Azure Data Lake Analytics 리소스의 프로덕션 인스턴스에 적용 되는 경우에 대 한 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure Data Lake Analytics 리소스를 보호 하는 Azure Security Center의 권장 사항을 따릅니다.

Microsoft는 Azure Data Lake Analytics을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

* [Azure Security Center 권장 사항 이해](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 합니다.

* [NIST 게시--일반적인 취약성 점수 매기기 시스템](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독에서 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독에 있는 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.

클래식 Azure 리소스는 Azure 리소스 그래프 탐색기를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

* [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Data Lake Analytics 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

* [ARM에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "DataLakeAnalytics" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Data Lake Analytics 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Data Lake Analytics와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.
- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: azure 정책 [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 azure 리소스에서 보안 설정을 적용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure Repos을 사용 하 여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿, 필요한 상태 구성 스크립트 등과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다.

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps의 사용 권한 및 그룹 정보](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: "DataLakeAnalytics" 네임 스페이스에 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만듭니다. Azure 정책 [감사], [거부] 및 [배포 하지 않은 경우 배포]를 사용 하 여 Azure Data Lake Analytics 리소스에 대 한 구성을 자동으로 적용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 해당 사항 없음 Data Lake Analytics는 고객이 관리 해야 하는 비밀을 노출 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 해당 사항 없음 Data Lake Analytics는 Azure 관리 id를 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Data Lake Analytics)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

App Service, Data Lake Analytics, Blob Storage 등의 Azure 리소스에 업로드 되는 콘텐츠를 사전 검사 합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 사항 없음 이 컨트롤은 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Data Lake Analytics 작업 로그 및 데이터 출력은 기본 Data Lake Storage Gen1 서비스에 저장 됩니다. ADLCopy, Azure PowerShell 또는 Azure Data Factory를 포함 하 여 데이터를 복사 하는 다양 한 방법을 사용할 수 있습니다. 또한 Azure Automation를 사용 하 여 정기적으로 데이터를 자동으로 백업할 수 있습니다.

* [Storage Explorer를 사용하여 Azure Data Lake Storage Gen1 리소스 관리](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Azure Storage Blob에서 Azure Data Lake Storage Gen1로 데이터 복사](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

* [Azure Automation 개요](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Data Lake Analytics 작업 로그 및 데이터 출력은 기본 Data Lake Storage Gen1 서비스에 저장 됩니다. ADLCopy, Azure PowerShell 또는 Azure Data Factory를 포함 하 여 데이터를 복사 하는 다양 한 방법을 사용할 수 있습니다.

* [Storage Explorer를 사용하여 Azure Data Lake Storage Gen1 리소스 관리](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)

* [Azure Storage Blob에서 Azure Data Lake Storage Gen1로 데이터 복사](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 정기적으로 백업 데이터의 데이터 복원을 수행 하 여 데이터의 무결성을 테스트 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Data Lake Storage Gen1 나 Azure Storage에 저장 된 Data Lake Analytics 백업은 기본적으로 암호화를 지원 하며 해제할 수 없습니다. 백업을 중요 한 데이터로 처리 하 고이 기준의 일부로 관련 액세스 및 데이터 보호 제어를 적용 해야 합니다.

* [Azure Data Lake Storage Gen1에 저장된 데이터 보호](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-secure-data)

* [Azure Storage 데이터에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

* [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

* [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약한 지점과 격차를 확인 한 후 필요에 따라 응답 계획을 수정 합니다.

* [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

* [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

* [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

* [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

* [워크플로 자동화 및 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

* [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
