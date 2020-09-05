---
title: Security Center에 대 한 Azure 보안 기준
description: Security Center 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f7da9b1779ed2cf46cb29b21215a9415cbaef494
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231285"
---
# <a name="azure-security-baseline-for-security-center"></a>Security Center에 대 한 Azure 보안 기준

이 보안 기준은 [Azure 보안 벤치 마크](../security/benchmarks/overview.md) 에서 Azure Security Center에 대 한 지침을 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure Security Center에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Security Center에 적용할 수 없는 **컨트롤** 은 제외 되었습니다. Azure Security Center 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure Security Center 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Security Center는 핵심 Azure 제품입니다. 가상 네트워크, 서브넷 또는 네트워크 보안 그룹을 Security Center에 직접 연결할 수 없습니다. 계산 리소스에 대 한 데이터 수집을 사용 하도록 설정 하는 경우 Log Analytics 작업 영역을 통해 수집한 데이터를 Security Center 저장 하 고, 가상 네트워크의 개인 끝점을 통해 작업 영역 데이터에 액세스 하기 위해 개인 링크를 사용 하도록 해당 작업 영역을 구성할 수 있습니다. 또한 데이터 수집 Security Center 사용 하는 경우 서버에 배포 되는 Log Analytics 에이전트를 사용 하 여 보안 데이터를 수집 하 고 이러한 계산 리소스에 대 한 보호를 제공 합니다. Log Analytics 에이전트를 사용 하려면 Security Center를 사용 하 여 적절 한 작업을 위해 특정 포트 및 프로토콜을 열어야 합니다. 이러한 필수 포트 및 프로토콜만 허용 하도록 네트워크를 잠그고 응용 프로그램이 네트워크 보안 그룹을 통해 작동 하는 데 필요한 추가 규칙도 추가 합니다.

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터](../virtual-network/tutorial-filter-network-traffic.md)

- [Log Analytics 에이전트를 사용 하기 위한 방화벽 요구 사항](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Azure 개인 링크 이해](../private-link/private-link-overview.md) 

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Security Center 제공은 가상 네트워크와 직접 통합 되지 않지만 네트워크에 배포 된 Log Analytics 에이전트로 구성 된 서버에서 데이터를 수집할 수 있습니다. Security Center에 데이터를 보내도록 구성 된 서버는 특정 포트 및 프로토콜이 제대로 통신 하도록 허용 해야 합니다. Azure Policy를 사용 하 여 이러한 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 적용 합니다.

또한 Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, 역할 할당 및 Azure Policy 할당 등의 주요 환경 아티팩트를 패키지 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 새 구독에 청사진을 적용 하 여 Security Center 구성 및 관련 네트워킹 리소스를 일관 되 고 안전 하 게 배포할 수 있습니다.

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [Log Analytics 에이전트를 사용 하기 위한 방화벽 요구 사항](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [네트워킹에 대 한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure Security Center 제공은 가상 네트워크와 직접 통합 되지 않지만 네트워크에 배포 된 Log Analytics 에이전트로 구성 된 서버에서 데이터를 수집할 수 있습니다. Security Center에 데이터를 보내도록 구성 된 서버는 특정 포트 및 프로토콜이 제대로 통신 하도록 허용 해야 합니다. Azure Policy를 사용 하 여 이러한 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 적용 합니다.

Azure Security Center에 보안 로그를 보내도록 구성 된 네트워크의 서버와 같은 네트워크 보안 그룹 및 기타 리소스에 대 한 리소스 태그를 사용 합니다. 개별 네트워크 보안 그룹 규칙에 대해 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙을 문서화 합니다. 

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다. 

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [Log Analytics 에이전트를 사용 하기 위한 방화벽 요구 사항](../azure-monitor/platform/log-analytics-agent.md#firewall-requirements)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags) 

- [Azure Virtual Network을 만드는 방법](../virtual-network/quick-create-portal.md) 

- [네트워크 보안 그룹 규칙을 사용 하 여 네트워크 트래픽을 필터링 하는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 리소스 구성을 모니터링 하 고 Azure Security Center 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 리소스의 변경 내용이 발생 하는 경우 사용자에 게 알리도록 경고를 Azure Monitor 만듭니다.

- [Azure 활동 로그 이벤트를 확인 하 고 검색 하는 방법](/azure/azure-monitor/platform/activity-log-view) 

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 중앙 Log Analytics 작업 영역을 사용 하 여 Azure Security Center에서 생성 된 보안 데이터 및 연결 된 원본을 집계 합니다. 

연결 된 Azure 계산 리소스의 보안 데이터 및 이벤트를 중앙 Log Analytics 작업 영역으로 보내도록 Security Center의 데이터 컬렉션을 구성 합니다. 데이터 수집 외에도 연속 내보내기 기능을 사용 하 여 Security Center에서 생성 된 보안 경고 및 권장 사항을 중앙 Log Analytics 작업 영역으로 스트리밍합니다. Azure Monitor에서 Security Center 및 연결 된 Azure 리소스에서 생성 된 보안 데이터에 대해 쿼리 및 분석을 수행할 수 있습니다. 

또는 Security Center에서 생성 한 데이터를 Azure 센티널 또는 타사 SIEM에 보낼 수 있습니다.

- [보안 경고 및 권장 사항 내보내기](continuous-export.md)

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](../azure-monitor/learn/quick-collect-azurevm.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Monitor 활동 로그를 자동으로 사용할 수 있으며, 이러한 로그에는 수행 된 작업, 작업을 시작한 사람 및 발생 시기를 비롯 하 여 Azure Security Center 리소스에 대 한 모든 쓰기 작업이 포함 됩니다. 로그 통합 및 보존 증가를 위해 Azure 활동 로그를 Log Analytics 작업 영역으로 보냅니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md)

- [활동 로그를 Log Analytics 작업 영역으로 보내기](../azure-monitor/platform/activity-log.md#send-to-log-analytics-workspace)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기 및 보관 저장소에 Azure Storage 계정을 사용 합니다. 

- [Log Analytics에서 데이터 보존 기간 변경](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 비정상적인 동작에 대 한 Azure Security Center 및 연결 된 원본에 의해 생성 된 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행할 수 있습니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md) 

- [Log Analytics 쿼리 시작](../azure-monitor/log-query/get-started-portal.md) 

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 활동 로그 또는 Azure Security Center에서 생성 된 데이터에 의해 기록 된 원치 않거나 비정상 활동을 쿼리하도록 Azure Monitor 로그 경고를 구성 합니다. 작업 그룹을 설정 하 여 조직이 알림을 받고 비정상적인 활동에 대해 로그 경고가 시작 되는 경우 조치를 취할 수 있도록 합니다. Security Center 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 논리 앱을 트리거합니다. Security Center 워크플로를 사용 하 여 사용자에 게 인시던트 응답을 알리거나 경고 정보에 따라 리소스를 수정 하는 작업을 수행할 수 있습니다.

또는 Azure 센티널에 Azure Security Center 하 여 관련 및 생성 된 온보드 데이터를 사용 하도록 설정할 수 있습니다. Azure 센티널은 보안 관련 문제에 대 한 자동화 된 위협 응답을 허용 하는 플레이 북을 지원 합니다.

- [Azure Security Center 워크플로 자동화](workflow-automation.md)

- [Azure Security Center에서 경고를 관리 하는 방법](security-center-managing-and-responding-alerts.md) 

- [Log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../sentinel/tutorial-respond-threats-playbook.md)

- [Azure Monitor의 로그 경고](../azure-monitor/platform/alerts-unified-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: azure RBAC (역할 기반 액세스 제어)를 통해 역할 할당을 통해 azure 리소스에 대 한 액세스를 관리할 수 있습니다. 이러한 역할은 사용자, 그룹 서비스 사용자 및 관리 되는 id에 할당할 수 있습니다. 특정 리소스에 대 한 미리 정의 된 기본 제공 역할이 있으며 이러한 역할은 Azure CLI, Azure PowerShell 또는 Azure Portal와 같은 도구를 통해 인벤토리 또는 쿼리할 수 있습니다. Azure Security Center에는 사용자가 보안 정책을 읽고 업데이트 하 고 경고 및 권장 사항을 해제할 수 있게 해 주는 ' 보안 읽기 권한자 ' 또는 ' 보안 Admin '의 기본 제공 역할이 있습니다.

- [Azure Security Center의 권한](security-center-permissions.md)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0) 

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure 플랫폼에 대 한 전용 관리자 계정 사용 또는 Azure Security Center 제공에 대 한 표준 운영 절차를 만듭니다. Azure Security Center Id 및 액세스 관리를 사용 하 여 Azure Active Directory의 관리 계정 수를 모니터링할 수 있습니다. 또한 Security Center에는 사용자가 보안 정책을 업데이트 하 고 경고 및 권장 사항을 해제 하는 데 사용할 수 있는 ' Security Admin '의 기본 제공 역할이 있습니다 .이 역할 할당이 정기적으로 발생 하는 모든 사용자를 검토 하 고 조정 해야 합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

- [Azure Security Center의 권한](security-center-permissions.md)

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center id 및 액세스 권장 사항을 사용 합니다.

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory MFA를 사용 하 여 Azure Security Center 및 Azure Portal에 액세스 하 고 Security Center id 및 액세스 권장 사항을 따릅니다. 

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](security-center-identity-access.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 높은 권한이 필요한 관리 작업에 대해 Azure에서 관리 하는 안전한 워크스테이션 (권한 있는 액세스 워크스테이션 또는 PAW 라고도 함)을 사용 합니다.

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)

- [Azure AD MFA를 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Active Directory 보안 보고서 및 모니터링을 사용 하 여 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 시기를 검색 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](/azure/active-directory/reports-monitoring/concept-user-at-risk) 

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](security-center-identity-access.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: Azure AD 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다. 

- [Azure AD 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Security Center를 사용할 때 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (Azure AD)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다. Azure Security Center에는 사용자가 보안 정책을 업데이트 하 고 경고 및 권장 사항을 해제할 수 있게 해 주는 ' Security i n t i o n '과 같이 할당 가능한 기본 제공 역할이 있습니다.

- [Azure Security Center의 권한](security-center-permissions.md)

- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. Azure Security Center와 관련 된 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/) 

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다. 

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.  

- [Azure Monitor와 Azure 활동 로그를 통합 하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고 

**지침**: Azure AD ID 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다. 

- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins) 

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: Azure Security Center의 중요 한 보안 정보를 저장 하는 Log Analytics 작업 영역 같은 Azure 리소스를 추적 하는 데 도움이 되는 태그를 사용 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Azure 리소스에 대 한 액세스 수준을 제한할 수 있습니다. Azure RBAC를 통해 Azure 리소스에 대 한 액세스를 제어할 수 있습니다.

기본적으로 Azure Security Center 데이터는 Security Center 백엔드 서비스에 저장 됩니다. 사용자의 리소스에이 데이터를 저장 하기 위한 요구 사항이 조직에 추가 된 경우 Log Analytics 작업 영역을 구성 하 여 Security Center 데이터, 경고 및 권장 사항을 저장할 수 있습니다. 사용자 고유의 작업 영역을 사용 하는 경우 데이터가 시작 된 환경에 따라 서로 다른 작업 영역을 구성 하 여 추가로 분리를 추가할 수 있습니다.

- [보안 경고 및 권장 사항 내보내기](continuous-export.md)

- [Azure Security Center에서 데이터 수집](security-center-enable-data-collection.md)

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription) 

- [관리 그룹을 만드는 방법](../governance/management-groups/create.md) 

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. Azure 리소스에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다. Log Analytics 에이전트를 사용 하 여 구성 하 고 Azure Security center로 데이터를 전송 하는 모든 가상 머신은 TLS 1.2를 사용 하도록 구성 해야 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다. 

- [데이터를 Log Analytics에 안전 하 게 보내기](../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)

- [Azure를 사용 하 여 전송 중인 암호화 이해](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어 

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Security Center 관련 된 데이터 및 리소스에 대 한 액세스를 관리 합니다. Azure Security Center에는 사용자가 보안 정책을 읽고 업데이트 하 고 경고 및 권장 사항을 해제할 수 있게 해 주는 ' 보안 읽기 권한자 ' 또는 ' 보안 Admin '의 기본 제공 역할이 있습니다. Security Center에 의해 수집 된 데이터를 저장 하는 Log Analytics 작업 영역에는 ' Log Analytics 읽기 권한자 ', ' Log Analytics 기여자 ' 등과 같은 사용자가 지정할 수 있는 기본 제공 역할도 있습니다. 사용자가 필요한 작업을 완료 하는 데 필요한 최소 허용 역할을 할당 합니다. 예를 들어, 리소스의 보안 상태에 대한 정보를 보기만 하고 권장 사항 적용이나 정책 편집 등의 조치는 취하지 않는 사용자에게 독자 역할을 할당합니다.

- [Azure Log Analytics 작업 영역에 대 한 사용 권한](../role-based-access-control/built-in-roles.md#log-analytics-reader)

- [Azure Security Center의 권한](security-center-permissions.md)

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Security Center 구성 된 Log Analytics 작업 영역을 사용 하 여 생성 된 데이터, 경고 및 권장 사항을 저장 합니다. Security Center 데이터 수집을 위해 구성한 작업 영역에 대해 고객 관리 키 (CMK)를 구성 합니다. CMK를 사용 하면 작업 영역에 저장 되거나 전송 되는 모든 데이터를 사용자가 만들고 소유한 Azure Key Vault 키로 암호화할 수 있습니다. 

- [Azure Monitor 고객 관리형 키](../azure-monitor/platform/customer-managed-keys.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Security Center와 관련 된 중요 한 Azure 리소스에 변경 내용이 적용 되는 경우 경고를 만들려면 Azure Monitor를 사용 합니다. 이러한 변경 내용에는 경고 또는 권장 사항 사용 안 함과 같은 보안 센터와 관련 된 구성을 수정 하는 작업, 데이터 저장소의 업데이트 또는 삭제 등이 포함 될 수 있습니다.

- [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조 하세요.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 점수 매기기 프로그램 (예: 일반적인 취약성 점수 매기기 시스템) 또는 타사 검사 도구에서 제공 하는 기본 위험 등급을 사용 합니다.

- [NIST 게시--일반적인 취약성 점수 매기기 시스템](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독에서 Azure Security Center 관련 된 모든 리소스를 쿼리하고 검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 모든 Azure 구독을 열거 하 여 Security Center 리소스를 검색 합니다. 

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md) 

- [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure Security Center의 중요 한 보안 정보를 저장 하는 Log Analytics 작업 영역 같은 Azure 리소스를 추적 하는 데 도움이 되는 태그를 사용 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Security Center 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

또한 Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](../governance/management-groups/create.md)

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리하고 검색 합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 조직의 인벤토리 및 검토 프로세스의 일부로 더 이상 필요 하지 않은 Azure Security Center 관련 된 Azure 리소스를 제거 합니다.

- [Azure 리소스 그룹 및 리소스 삭제](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 통해 Azure Security Center 및 연결 된 작업 영역에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "OperationalInsights" 및 "" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 Azure Policy 정의를 만들어 Security Center 및 해당 Log Analytics 작업 영역의 구성을 감사 하거나 적용 합니다.

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure 리소스에서 보안 설정을 적용 하려면 ' 거부 ' 및 ' 배포 하지 않은 경우 배포 '에 대 한 Azure Policy 효과를 사용 하세요. 또한 Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md) 

- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure devops를 사용 하 여 사용자 지정 Azure Policy 정의, Azure Resource Manager 템플릿 및 필요한 상태 구성 스크립트와 같은 코드를 안전 하 게 저장 하 고 관리할 수 있습니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다. 

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 Azure 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. Azure Policy 별칭을 사용 하 여 Azure Security Center 관련 리소스의 구성을 감사 하거나 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 Azure Automation를 사용 하 여 구성 변경 내용을 배포할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md) 

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy 정의 및 "OperationalInsights" 및 "microsoft의 Azure Policy 별칭을 사용 합니다. 보안 "Azure 리소스 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만드는 네임 스페이스 Azure 리소스에 대 한 구성을 자동으로 적용 하려면 Azure 정책 효과 "감사", "거부" 및 "배포 되지 않은 경우 배포"를 사용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Security Center 구성 된 Log Analytics 작업 영역을 사용 하 여 생성 된 데이터, 경고 및 권장 사항을 저장 합니다. Security Center 데이터 수집을 위해 구성한 작업 영역에 대해 고객 관리 키 (CMK)를 구성 합니다. CMK를 사용 하면 작업 영역에 저장 되거나 전송 되는 모든 데이터를 사용자가 만들고 소유한 Azure Key Vault 키로 암호화할 수 있습니다. 

- [Azure Monitor 고객 관리형 키](../azure-monitor/platform/customer-managed-keys.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조 하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Azure Security Center는 파일을 저장 하거나 처리 하기 위한 것이 아닙니다. Log Analytics 작업 영역을 포함 하 여 비 계산 Azure 리소스에 업로드 되는 콘텐츠를 미리 검색 하는 것은 사용자의 책임입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장 

**지침**: IAC (infrastructure as code) 방식을 따르고 Azure Resource Manager를 사용 하 여 리소스 관련 구성에 대 한 백업으로 사용할 수 있는 JAVASCRIPT OBJECT NOTATION (JSON) 템플릿에 Azure Security Center 관련 리소스를 배포 합니다.

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 리소스에 대 한 Azure Resource Manager 템플릿](/azure/templates/microsoft.security/allversions)

- [Azure Automation 소개](../automation/automation-intro.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Security Center는 Log Analytics 작업 영역을 사용 하 여 생성 되는 데이터, 경고 및 권장 사항을 저장 합니다. Azure Monitor 및 Security Center에서 고객이 관리 하는 키를 사용 하도록 설정 하는 데 사용 하는 작업 영역을 구성할 수 있습니다. Key Vault를 사용 하 여 고객이 관리 하는 키를 저장 하는 경우 키의 자동 백업을 정기적으로 수행 해야 합니다.

- [Key Vault 키를 백업 하는 방법](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Azure Resource Manager 지원 되는 템플릿 파일을 사용 하 여 정기적으로 복원을 수행할 수 있도록 합니다. 백업 된 고객이 관리 하는 키의 복원을 테스트 합니다.

- [Azure Resource Manager 템플릿을 사용 하 여 Log Analytics 작업 영역 관리](../azure-monitor/platform/template-workspace-configuration.md)

- [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure devops를 사용 하 여 사용자 지정 Azure Policy 정의 및 Azure Resource Manager 템플릿과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스를 보호 하기 위해 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다. 역할 기반 액세스 제어를 사용 하 여 고객이 관리 하는 키를 보호 합니다.

또한 Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하 여 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 합니다.  Azure Storage를 사용 하 여 Azure Resource Manager 템플릿 백업을 저장 하는 경우 blob 또는 blob 스냅숏이 삭제 될 때 일시 삭제를 사용 하 여 데이터를 저장 하 고 복구할 수 있습니다. 

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 정보](/azure/devops/organizations/security/about-permissions)

- [Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Azure Storage Blob에 대한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다.  사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다. 

- [Azure Security Center의 보안 경고](security-center-alerts-overview.md) 

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약한 지점과 격차를 확인 한 후 필요에 따라 응답 계획을 수정 합니다. 

- [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정 하는 방법](security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: 워크플로 자동화 기능 Azure Security Center 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다. 

- [Security Center에서 워크플로 자동화를 구성 하는 방법](workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다. 

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
