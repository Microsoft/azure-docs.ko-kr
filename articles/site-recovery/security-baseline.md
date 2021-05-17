---
title: Site Recovery에 대 한 Azure 보안 기준
description: Site Recovery 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf91c0a6e533acb326fe815c3e3c1088c959a603
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576720"
---
# <a name="azure-security-baseline-for-site-recovery"></a>Site Recovery에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Site Recovery 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Site Recovery에 적용 되는 관련 지침에 따라 그룹화 됩니다. Site Recovery에 적용할 수 없는 **컨트롤** 은 제외 되었습니다. 

Site Recovery 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Site Recovery 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**참고**: Microsoft Azure Site Recovery는 Azure Virtual Network에 대 한 배포를 지원 하지 않습니다. 네트워크를 통해 보안 통신을 적용 하려면 Azure 개인 끝점을 사용 하 여 Site Recovery 서비스를 구성 합니다.

- [Azure Site Recovery 개인 링크 지원](azure-to-azure-how-to-enable-replication-private-endpoints.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Site Recovery 서비스는 고객이 특정 서비스 및 포트에 대 한 트래픽만 열 수 있도록 하는 서비스 태그를 지원 합니다. 고객은 방화벽 또는 네트워크 보안 그룹에 대 한 "AzureSiteRecovery" 서비스 태그를 허용 하 여 Site Recovery 서비스에 대 한 아웃 바운드 액세스를 허용 해야 합니다.

- [서비스 태그를 사용 하 여 아웃 바운드 연결](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 네트워크 보안 그룹 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 리소스 태그를 사용 합니다. 개별 네트워크 보안 그룹 규칙에 대해 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙을 문서화 합니다. 

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 통합 합니다. 

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다. 

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md) 

- [Azure Virtual Network을 만드는 방법](../virtual-network/quick-create-portal.md) 

- [네트워크 보안 그룹 규칙을 사용 하 여 네트워크 트래픽을 필터링 하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 Site Recovery 서비스와 관련 된 네트워크 리소스 구성에 대 한 변경 내용을 모니터링 합니다. Azure Monitor에서 경고를 만들어 중요 한 Site Recovery 네트워크 리소스가 변경 될 때 사용자에 게 알립니다.

- [Azure 활동 로그 이벤트 보기 및 검색](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 감사 로깅에 대해 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 보관을 위해 Log Analytics 작업 영역, Azure Storage 계정 또는 Azure 이벤트 허브에 로그를 보냅니다.

Azure 활동 로그 데이터를 사용 하 여 Azure 리소스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정 합니다.

Azure Monitor에서 Site Recovery 로그를 수집 하 여 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 또는 보관 저장소에 저장소 계정을 사용 합니다. 또한 Azure 센티널 또는 타사 SIEM (보안 인시던트 및 이벤트 관리) 솔루션에 대해 및 온보드 데이터를 사용할 수 있습니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor 로그를 사용하여 Site Recovery 모니터링](monitor-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사 로깅에 대해 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, Azure Storage 계정 또는 보관을 위해 Azure Event Hub에 로그를 보냅니다. 

Azure 활동 로그 데이터를 사용 하 여 Azure 리소스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정 합니다.

Azure Monitor를 사용 하 여 Site Recovery 로그를 수집 하 여 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 저장소 계정을 사용 합니다. Azure 센티널 또는 타사 SIEM (보안 인시던트 및 이벤트 관리) 솔루션에 및 온-보드 데이터를 사용 하도록 설정 합니다.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor 로그를 사용하여 Site Recovery 모니터링](monitor-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: 조직의 규정 준수 규정에 따라 Azure Monitor을 사용 하 여 Azure Recovery Services 자격 증명 모음과 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다. 

- [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. 

Log Analytics 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, Recovery Services 자격 증명 모음에서 수집한 활동 로그 데이터에 대 한 통찰력

- [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 하는 방법](../azure-monitor/essentials/activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Monitor 로그 및 Log Analytics를 사용 하 여 Azure Site Recovery에서 복제 된 컴퓨터를 모니터링 합니다. Azure Monitor 내에서 Log Analytics를 사용 하 여 로그 쿼리를 작성 및 테스트 하 고 로그 데이터를 대화형으로 분석할 수 있습니다. Azure Monitor는 다른 모니터링 데이터와 함께 활동 및 리소스 로그를 수집 합니다. 

로그 결과를 시각화 및 쿼리하고 모니터링 된 데이터에 따라 동작을 수행 하도록 경고를 구성 합니다. 대화 충성도 (보안 오케스트레이션 자동화 된 응답) 솔루션을 제공 하므로 Log Analytics 작업 영역에 대 한 경고를 Azure 센티널에 설정 합니다. 이를 통해 플레이 북과 같은 자동화 된 솔루션을 만들고이를 사용 하 여 보안 문제를 수정할 수 있습니다. Azure Monitor를 사용 하 여 Log Analytics 작업 영역에 사용자 지정 로그 경고를 만듭니다. 

- [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용하여 로그 경고 만들기, 보기 및 관리](../azure-monitor/alerts/alerts-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**참고**: 기본적으로 역할은 할당 되지 않습니다. 비즈니스 요구에 따라 명시적으로 할당 해야 합니다. PowerShell CLI 또는 Azure Active Directory (Azure AD)를 사용 하 여 역할 할당을 확인 하 여 관리 그룹의 구성원 인 계정을 검색할 수 있습니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Security Center의 Id 및 액세스 관리 기능을 사용 하 여 관리 계정 수를 모니터링 합니다.

또한 전용 관리 계정을 추적 하는 데 도움이 되도록 다음과 같은 Security Center 또는 기본 제공 Azure 정책의 권장 사항을 사용 하세요. 

- 구독에 둘 이상의 소유자를 할당해야 합니다. 
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다. 

- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

관리 계정에 대 한 id 및 액세스 제어를 추적 하 고 주기적으로 검토 하는 프로세스를 만듭니다.

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 서비스 사용자와 함께 Azure 앱 등록을 사용 하 여 API 호출을 통해 Recovery Services 자격 증명 모음과 상호 작용 하는 데 사용할 토큰을 검색 합니다.

- [Azure REST Api를 호출 하는 방법](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Azure AD를 사용 하 여 클라이언트 응용 프로그램 (서비스 사용자)을 등록 하는 방법](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API 정보](/rest/api/recoveryservices)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD, 다단계 인증을 사용 하도록 설정 하 고 Security Center Id 및 액세스 권장 사항을 따릅니다. 
- [Azure AD Multi-Factor Authentication 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

- [ID 및 액세스 모니터링](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 관리 작업을 위한 azure 다단계 인증을 사용 하 여 PAW (권한 있는 액세스 워크스테이션) 라고도 하는 안전한 azure 관리 워크스테이션을 사용 하 고 Site Recovery 리소스에 대해 권한 있는 작업을 수행 합니다.

- [권한 있는 액세스 워크스테이션](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [클라우드 기반 Azure AD Multi-Factor Authentication 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure AD의 PIM (Privileged Identity Management) 기능을 사용 합니다.
Azure AD 위험 감지 기능을 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위, 지역 또는 국가의 특정 논리적 그룹 에서만 Azure Portal에 대 한 액세스를 허용 합니다.
- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD를 중앙 인증 및 Site Recovery 권한 부여 시스템으로 사용 합니다. Azure AD는 미사용 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고 전송 중에는 salts, 해시 및 안전 하 게 사용자 자격 증명을 저장 합니다. 

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD 로그를 사용 하 여 부실 계정을 검색 하는 데 도움을 줍니다. 

Azure AD의 Id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램 및 역할 할당에 대 한 액세스를 효율적으로 관리 합니다. 

정기적으로 사용자 액세스를 검토 하는 프로세스를 만들어, 완료 된 액세스 검토를 가진 사용자만 지속적으로 액세스할 수 있도록 합니다. 

- [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure AD를 중앙 인증 및 Site Recovery 리소스에 대 한 권한 부여 시스템으로 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 하 고 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며,이를 통해 Azure Marketplace에서 사용할 수 있는 Azure 센티널 또는 SIEM 또는 모니터링 도구와 통합할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 Log Analytics 작업 영역에 감사 및 로그인 로그를 전송 하 여이 프로세스를 더욱 간소화 합니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD를 Recovery Services 자격 증명 모음에 대 한 중앙 인증 및 권한 부여 시스템으로 사용 합니다. 

계정 로그인 동작을 검색 하 고, 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 하기 위해 Azure AD의 Id 보호 기능을 사용 합니다. 또한 추가 조사를 위해 데이터를 Azure 센티널로 수집 합니다.

- [Azure AD 위험한 로그인을 확인 하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션 Recovery Services 자격 증명 모음에 대 한 별도의 구독 또는 관리 그룹을 구현 합니다. 가상 네트워크 또는 서브넷을 사용 하 여 리소스를 분리 하 고, 적절 하 게 태그가 지정 되 고, 네트워크 보안 그룹 또는 Azure 방화벽을 통해 보안이 유지 됩니다. 

사용 하지 않는 경우 중요 한 데이터를 저장 하거나 처리 하는 가상 컴퓨터를 해제 합니다. 이를 되풀이 프로세스로 설정 하려면 정책과 프로시저를 구현 합니다. 

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [Site Recovery 개요](site-recovery-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 개인 링크 또는 개인 끝점, 네트워크 보안 그룹 및 서비스 태그를 사용 하 여 Site Recovery 활성화 된 가상 머신에서 데이터 반출을 위한 기회를 완화 합니다.

Microsoft는 Site Recovery에서 사용 하는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Microsoft는 Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

- [Azure 개인 끝점을 사용 하 여 가상 머신 복제](azure-to-azure-how-to-enable-replication-private-endpoints.md)

- [Azure Site Recovery 서비스 태그를 사용 하 여 가상 머신 복제](azure-to-azure-about-networking.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Site Recovery는 Azure 워크 로드 서버에서 AES(ADVANCED ENCRYPTION STANDARD) (AES 256)를 사용 하 여 암호화 된 보안 https 채널을 사용 하 여 Recovery Services 자격 증명 모음 뒤에 호스팅된 서비스를 Site Recovery 합니다.

Site Recovery에 대해 지원 되는 현재 TLS 버전은 TLS 1.0, TLS 1.1, TLS 1.2의 2019 끝에 살고 있는 지역에 있습니다. TLS 1.2는 모든 새 지역에서 유일 하 게 지원 되는 TLS 버전입니다.

- [Azure Site Recovery에 대 한 전송 암호화 이해](physical-azure-set-up-source.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Site Recovery 사용할 수 없습니다. 

규정 준수를 위해 필요에 따라 타사 솔루션을 구현 합니다.

Microsoft는 Site Recovery에서 사용 하는 기본 플랫폼을 관리 하 고 모든 고객 콘텐츠를 중요 한 것으로 처리 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Site Recovery 리소스와 관련 된 데이터 및 리소스에 대 한 액세스를 관리 합니다. 

작업 의무를 Azure RBAC와 구분 하 고 적절 한 액세스 권한을 부여 해야 합니다. 기본 제공 Site Recovery 역할을 사용 하 여 Site Recovery 관리 작업을 제어 합니다.

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

- [Role-Based Access Control를 사용 하 여 관리 Azure Site Recovery](site-recovery-role-based-linked-access-control.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 플랫폼과 고객이 관리 하는 키를 모두 사용 하 여 이중 암호화를 사용 하도록 설정 합니다. 이 기능은 Site Recovery에서 사용할 수 있습니다. 

Site Recovery는 데이터에 대 한 저장소 데이터 암호화를 지원 합니다. Azure IaaS 워크 로드의 경우 데이터는 SSE (저장소 서비스 암호화)를 사용 하 여 미사용으로 암호화 됩니다. 

고객이 관리 하는 키로 암호화 된 Recovery Services 자격 증명 모음을 사용 하는 동안에만 고객에 게 암호화 키에 대 한 액세스 권한이 있습니다. Microsoft는 복사본을 유지 관리 하지 않으며, 키에 대 한 액세스 권한이 없으며, 언제 든 지 기본에서 재해 복구 위치로 전송 된 데이터의 암호를 해독 하지 않습니다. 

- [Azure Site Recovery에 대 한 고객 관리 키 지원](azure-to-azure-how-to-enable-replication-cmk-disks.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: 중요 한 리소스를 변경 하는 경우 경고를 만들려면 Azure 활동 로그와 Azure Monitor를 사용 합니다. 이러한 리소스에는 Recovery Services 자격 증명 모음, Site Recovery 서비스 리소스 및 관련 리소스의 프로덕션 인스턴스가 포함 될 수 있습니다.
- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 Recovery Services 자격 증명 모음을 비롯 한 모든 리소스를 쿼리하거나 검색 합니다. 테 넌 트에서 적절 한 읽기 권한이 있는지 확인 하 고 모든 Azure 구독 및 구독 내의 리소스를 열거 합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터와 Site Recovery에서 사용 하는 Recovery Services 자격 증명 모음 및 기타 관련 리소스에 태그를 적용 하 여 데이터를 분류로 논리적으로 구성 합니다.
- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 Site Recovery (Recovery Services 자격 증명 모음) 및 기타 관련 된 리소스를 구성 하 고 추적 합니다. 

또한 Azure Policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 고객의 조직 요구 사항에 따라 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 

- 허용되지 않는 리소스 종류 
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류 
- 허용되는 리소스 유형

회사 표준 및 서비스 수준 계약의 준수를 유지하는 데 있어 Azure에서 정책을 만들고 관리하는 방법을 이해하는 것이 중요합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다. 이렇게 하면 보안 수준이 높은 환경에서 리소스를 만들고 변경 하지 못할 수 있습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 Recovery Services 자격 증명 모음에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. 

"Microsoft RecoveryServices" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 감사를 위한 사용자 지정 정책을 만들거나 Site Recovery 서비스의 Recovery Services 자격 증명 모음 리소스 구성을 적용 합니다.
- [사용 가능한 Azure 정책 별칭을 확인하는 방법](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [없는 경우 배포] 효과를 사용 하 여 Azure 리소스에서 보안 설정을 적용 합니다.
- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Recovery Services 자격 증명 모음 및 관련 리소스에 대 한 사용자 지정 Azure Policy 정의를 사용 하는 경우 코드를 안전 하 게 저장 하 고 관리 하려면 Azure Repos을 선택 합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos 설명서](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft recoveryservices" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 

또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft recoveryservices" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 

Azure 리소스에 대 한 구성을 자동으로 적용 하려면 Azure Policy [감사], [거부] 및 [없는 경우 배포] 효과를 사용 합니다.
- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 고객이 Azure Key vault와 통합 된 Site Recovery 암호를 관리 하 고 Azure Disk Encryption 사용 가능한 가상 컴퓨터에 대 한 재해 복구를 사용 하도록 설정 해야 합니다. 

- [주요 자격 증명 모음을 만드는 방법](../key-vault/general/quick-create-portal.md)

- [주요 자격 증명 모음에 인증 하는 방법](../key-vault/general/authentication.md)

- [키 자격 증명 모음 액세스 정책을 할당 하는 방법](../key-vault/general/assign-access-policy-portal.md)

- [Site Recovery를 사용 하 여 Azure Disk Encryption 지원 가상 컴퓨터에 대해 DR을 사용 하도록 설정 하는 방법](azure-to-azure-how-to-enable-replication-ade-vms.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Site Recovery는 고객이 Recovery Services 자격 증명 모음에서 시스템 관리 id를 사용 하도록 설정할 수 있는 시스템 관리 id만 지원 합니다. 액세스 경계를 정의 하기 위해 재해 복구 제공에 사용 되는 리소스에도 동일한 방법이 적용 됩니다. 

관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 

관리 id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

- [Azure 관리 Id와 통합 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md?tabs=core2x)

- [Recovery Services 자격 증명 모음에서 시스템 관리 Id를 사용 하도록 설정 하는 방법](azure-to-azure-how-to-enable-replication-private-endpoints.md#enable-the-managed-identity-for-the-vault)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Site Recovery)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 콘텐츠에서 실행 되지 않습니다. App Service, Data Lake Storage 및 Blob Storage와 같은 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 합니다.

데이터 서비스에 대 한 Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다.

- [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해](../security/fundamentals/antimalware.md)

- [데이터 서비스에 대 한 Azure Security Center의 위협 검색 이해](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Site Recovery는 고객이 작업에 대해 구성 된 대로 재해 복구 솔루션의 상태를 유지 하기 위해 내부적으로 Azure Storage 계정을 사용 합니다.

유형 구성에서 Site Recovery services 메타 데이터에 사용 되는 모든 저장소 리소스: 읽기 액세스 지역 중복 저장소 (RA-GRS). GRS (예: RAGRS, ZRS) 위의 유형 저장소 계정은 데이터를 보조 지역 (원본 데이터의 기본 위치에서 수백 마일 떨어진 곳)으로 복제 하 여 중단 시 고객에 대 한 재해 복구를 계속 제공 합니다.

이는 고객 범위를 벗어난 것 이며 Site Recovery 팀은 내부적으로이를 처리 합니다. 고객은 Azure에서 Key Vault 키를 백업할 수 있습니다.

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 백업 된 고객이 관리 하는 키의 복원을 정기적으로 테스트 합니다.

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure의 IaaS (Infrastructure As a Service)를 Virtual Machines 기반으로 하는 저장소 서비스 암호화 (SSE)를 사용 하 여 미사용 데이터를 암호화 합니다. Key Vault에서 일시 삭제를 사용 하도록 설정 하 여 실수로 또는 악의적인 삭제 로부터 키를 보호 합니다.

- [Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 

담당자의 모든 역할을 정의 하는 사고 대응 계획을 작성 하 고, 검색 후 인시던트 검토에서 인시던트 처리 또는 관리의 단계를 정의 해야 합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center의 할당 된 경고-심각도에 따라 먼저 조사 해야 하는 경고의 우선 순위를 지정 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

구독을 명확 하 게 (예: 프로덕션, 비프로덕션) 표시 하 고 Azure 리소스를 명확 하 게 식별 하 고 범주화 하는 명명 시스템을 만듭니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](../azure-resource-manager/management/tag-resources.md).

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 필요에 따라 약한 점수 및 차이를 식별 하 고 계획을 수정 합니다.

- [IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램은 NIST의 게시 가이드를 참조 하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 

문제를 해결 하기 위해 발생 한 인시던트를 검토 하는 프로세스를 만듭니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용 하 여 Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. 

필요에 따라 Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍합니다.
- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Security Center의 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.
- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

- [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.