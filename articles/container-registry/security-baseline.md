---
title: Azure Container Registry에 대 한 Azure 보안 기준
description: Azure Container Registry에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ea7460b3f997e0432ea0b987849eafccbe57b3b9
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229177"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure Container Registry에 대 한 Azure 보안 기준

Azure Container Registry에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 그룹: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: azure Virtual Network는 azure 및 온-프레미스 리소스에 대 한 안전한 개인 네트워킹을 제공 합니다. Azure 가상 네트워크에서 개인 Azure container registry에 대 한 액세스를 제한 하 여 가상 네트워크의 리소스만 레지스트리에 액세스 하도록 합니다. 크로스-프레미스 시나리오의 경우 특정 IP 주소 에서만 레지스트리 액세스를 허용 하도록 방화벽 규칙을 구성할 수도 있습니다. 방화벽 뒤에서 컨테이너 레지스트리에 액세스 하도록 방화벽 액세스 규칙 및 서비스 태그를 구성 합니다.

Azure virtual network 또는 방화벽 규칙을 사용 하 여 Azure container registry에 대 한 액세스 제한: https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

방화벽 뒤에 있는 Azure container registry에 액세스 하는 규칙을 구성 합니다. https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성과 트래픽에 대한 모니터링 및 기록

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 재구성 하 여 Azure에서 네트워크 리소스를 보호 합니다. NSG 흐름 로그를 사용하도록 설정하고, 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다.

NSG 흐름 로그를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

네트워크 리소스 보호: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 사항 없음 벤치 마크는 웹 응용 프로그램을 호스트 하는 Azure App Service 또는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하기 위해 가상 네트워크에서 DDoS Standard 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.  위협 인텔리전스를 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다.

Azure Security Center Just-in-time 네트워크 액세스를 사용 하 여 제한 된 기간 동안 끝점의 노출을 승인 된 IP 주소로 제한 하는 NSGs를 구성할 수 있습니다. 또한 적응 네트워크 강화 Azure Security Center 사용 하 여 실제 트래픽 및 위협 인텔리전스에 따라 포트와 원본 Ip를 제한 하는 NSG 구성을 권장 합니다.

DDoS 보호를 구성 하는 방법:  https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 방화벽을 배포 하는 방법: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Security Center 통합 위협 인텔리전스 이해: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

적응 네트워크 강화 Azure Security Center 이해: https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Just-in-time 네트워크 Access Control를 Azure Security Center 합니다. https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure container registry를 보호 하는 데 사용 되는 서브넷에 연결 된 nsg에 대 한 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 합니다. Azure Storage 계정에 NSG 흐름 로그를 기록 하 여 흐름 레코드를 생성할 수 있습니다. 비정상적인 활동을 조사 하는 데 필요한 경우 Azure Network Watcher 패킷 캡처를 사용 하도록 설정 합니다.

NSG 흐름 로그를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 제공 하는 제품을 선택 합니다. 페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다. Azure Firewall 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과 주고받는 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

악의적인 트래픽을 감지 하거나 거부 하기 위해 각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 합니다.

Azure Marketplace:  https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Azure 방화벽을 배포 하는 방법: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽을 사용 하 여 경고를 구성 하는 방법: https://docs.microsoft.com/azure/firewall/threat-intel


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 사항 없음 벤치 마크는 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 컨테이너 레지스트리에 액세스 해야 하는 리소스의 경우 Azure Container Registry 서비스에 대 한 가상 네트워크 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 "AzureContainerRegistry"를 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

서비스 태그에의 한 액세스 허용: https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 Azure container registry와 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.containerregistry" 및 "" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 컨테이너 레지스트리의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 

Azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, Azure RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 새 구독에 청사진을 쉽게 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정 합니다.

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Blueprint를 만드는 방법: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 고객은 azure 청사진을 사용 하 여 단일 청사진 정의에서 Azure Resource Manager 템플릿, Azure RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키지 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 새 구독에 청사진을 쉽게 적용 하 고 버전 관리를 통해 제어 및 관리를 세부적으로 조정 합니다.

Azure Blueprint를 만드는 방법: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 컨테이너 레지스트리 관련 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트를 확인 하 고 검색 하는 방법:  https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor에서 경고를 만드는 방법:  https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: Azure 리소스에 대한 시간 원본은 Microsoft에서 유지 관리되지만 컴퓨팅 리소스에 대한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다.

Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법: https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure container registry에서 생성 된 보안 데이터를 집계 하기 위해 Azure Monitor를 통해 로그를 수집 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, Azure Storage 계정을 장기/보관 스토리지에 사용할 수 있습니다.

진단 평가 및 감사에 대 한 Azure Container Registry 로그:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Monitor는 레지스트리의 사용자 기반 이벤트에 대 한 리소스 로그 (이전에는 진단 로그 라고 함)를 수집 합니다. 이 데이터를 수집 하 고 사용 하 여 레지스트리 인증 이벤트를 감사 하 고 끌어오기 및 푸시 이벤트와 같은 레지스트리 아티팩트의 전체 작업 내역을 제공 하 여 레지스트리의 보안 문제를 진단할 수 있습니다.

진단 평가 및 감사에 대 한 Azure Container Registry 로그: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

Log Analytics 작업 영역에 대한 로그 보존 기간 매개 변수를 설정하는 방법: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 Azure Container Registry 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다.

진단 평가 및 감사에 대 한 Azure Container Registry 로그:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Log Analytics 작업 영역 이해: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: azure Log Analytics 작업 영역을 사용 하 여 azure container registry와 관련 된 보안 로그 및 이벤트의 비정상적인 활동을 모니터링 하 고 경고 합니다.

진단 평가 및 감사에 대 한 Azure Container Registry 로그: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Log analytics 로그 데이터를 경고 하는 방법:  https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Azure Container Registry는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Container Registry 끝점 이며 통신을 시작 하지 않으며 서비스에서 DNS를 쿼리하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 그룹: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Active Directory (Azure AD)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

각 Azure container registry에 대해 기본 제공 관리자 계정이 사용 되는지 여부를 추적 합니다. 사용 하지 않을 때 계정을 사용 하지 않도록 설정 합니다.

PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry 관리자 계정:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**참고**: Azure Active Directory (Azure AD)에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 만들어야 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 Marketplace 서비스를 담당 합니다.

Azure container registry의 기본 관리자 계정을 사용 하는 경우 복잡 한 암호가 자동으로 만들어지고 회전 됩니다. 사용 하지 않을 때 계정을 사용 하지 않도록 설정 합니다.

Azure Container Registry 관리자 계정: https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 컨테이너 레지스트리의 기본 제공 관리자 계정을 사용 하도록 설정 하는 프로시저를 만듭니다. 사용 하지 않을 때 계정을 사용 하지 않도록 설정 합니다.

Azure Security Center Id 및 액세스 이해:  https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry 관리자 계정:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center Id 및 액세스 관리 권장 사항을 사용 합니다.

컨테이너 레지스트리에 대 한 개별 액세스의 경우 Azure Active Directory와 통합 된 개별 로그인을 사용 합니다.

Azure AD를 사용 하 여 SSO 이해:  https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

컨테이너 레지스트리에 대 한 개별 로그인:  https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory (Azure AD) multi-factor AUTHENTICATION (MFA)을 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center 내에서 id 및 액세스를 모니터링 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(권한 있는 액세스 워크스테이션) 사용

**지침**: MFA가 구성된 PAW(Privileged Access Workstation)를 사용하여 Azure 리소스에 로그인하고 구성합니다.

권한 있는 액세스 워크스테이션에 대해 알아봅니다.  https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 사용하도록 설정하는 방법: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure AD (Azure Active Directory) 보안 보고서를 사용 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

위험한 활동에 대해 플래그가 지정 된 Azure AD 사용자를 확인 하는 방법:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center에서 사용자의 id 및 액세스 활동을 모니터링 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

Azure에서 명명 된 위치를 구성 하는 방법:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure AD 인스턴스를 만들고 구성 하는 방법: https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

Azure AD 보고 이해:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure id 액세스 검토를 사용 하는 방법:  https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 대한 액세스 시도 모니터링

**지침**: Siem (보안 정보 및 이벤트 관리)/모니터링 도구와 통합할 수 있도록 하는 Azure Active Directory (Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure 활동 로그를 Azure Monitor에 통합 하는 방법:  https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory (Azure AD) 위험 및 id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 

Azure AD 위험한 로그인을 확인하는 방법: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Id 보호 위험 정책을 구성 하 고 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 사용할 수 없음 고객 Lockbox 현재 Azure Container Registry 지원 되지 않습니다.

고객 Lockbox가 지원하는 서비스 목록: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 그룹: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 리소스 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure container registry 추적을 지원 합니다.

중요 한 정보를 저장 하거나 처리 하는 이미지를 추적 하는 데 도움이 되도록 컨테이너 이미지 또는 리포지토리의 기타 아티팩트를 태그 및 버전으로 만들고 이미지 또는 리포지토리를 잠급니다.

태그를 만들고 사용 하는 방법:  https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

컨테이너 이미지 태그 지정 및 버전 관리에 대 한 권장 사항:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Azure container registry에서 컨테이너 이미지 잠그기:  https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 컨테이너 레지스트리, 구독 및/또는 관리 그룹을 구현 합니다. 중요 한 데이터를 저장 하거나 처리 하는 리소스는 충분히 격리 되어야 합니다.

리소스는 가상 네트워크 또는 서브넷으로 구분 되며, 적절 하 게 태그가 지정 되 고, NSG (네트워크 보안 그룹) 또는 Azure 방화벽으로 보호 됩니다.

추가 Azure 구독을 만드는 방법:  https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:  https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure virtual network 또는 방화벽 규칙을 사용 하 여 Azure container registry에 대 한 액세스 제한: https://docs.microsoft.com/azure/container-registry/container-registry-vnet

보안 구성을 사용 하 여 NSG를 만드는 방법: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure 방화벽을 배포 하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽을 사용 하 여 경고 또는 경고 및 거부를 구성 하는 방법:

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에 자동화 된 도구를 배포 하 여 중요 한 정보를 무단으로 전송 하는 것을 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송 차단

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Container Registry에 연결 하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인 합니다. Microsoft Azure 리소스는 기본적으로 TLS 1.2을 협상 합니다.

해당 하는 경우 미사용 암호화 및 전송 중인 암호화에 대 한 Azure Security Center 권장 사항을 따릅니다.

Azure를 사용 하 여 전송 중인 암호화 이해:  https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Container Registry 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 azure container registry의 데이터 및 리소스에 대 한 액세스를 제어 합니다. 

Azure RBAC를 구성 하는 방법:  https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry 역할 및 사용 권한:  https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 계산 리소스에 대 한 준수를 위해 필요한 경우 시스템에서 데이터를 복사 하는 경우에도 데이터에 대 한 액세스 제어를 적용 하기 위해 자동화 된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:  https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 모든 Azure 리소스에서 미사용 암호화를 사용 합니다. 기본적으로 Azure container registry의 모든 데이터는 Microsoft 관리 키를 사용 하 여 미사용에 암호화 됩니다.

Azure에서 미사용 암호화 이해: https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure Container Registry에서 고객이 관리 하는 키:  https://aka.ms/acr/cmk



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Monitor는 레지스트리의 사용자 기반 이벤트에 대 한 리소스 로그 (이전에는 진단 로그 라고 함)를 수집 합니다. 이 데이터를 수집 하 고 사용 하 여 레지스트리 인증 이벤트를 감사 하 고 레지스트리 아티팩트 (예: 끌어오기 및 끌어오기 이벤트)에 대 한 전체 작업 내역을 제공 하 여 레지스트리의 운영 문제를 진단할 수 있습니다.

진단 평가 및 감사에 대 한 Azure Container Registry 로그: https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 그룹: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 컨테이너 이미지에 대 한 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다. 필요에 따라 Azure Marketplace에서 타사 솔루션을 배포 하 여 이미지 취약성 평가를 수행 합니다.

Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Security Center와 Azure Container Registry 통합 (미리 보기):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Microsoft는 Azure Container Registry을 지 원하는 기본 시스템에서 패치 관리를 수행 합니다.

운영 체제 및 기타 패치의 기본 이미지에 대 한 업데이트가 검색 되 면 컨테이너 이미지 업데이트를 자동화 합니다.

Azure Container Registry 작업에 대 한 기본 이미지 업데이트 정보:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 타사 솔루션을 사용 하 여 응용 프로그램 이미지를 패치할 수 있습니다.  또한 Azure Container Registry 작업을 실행 하 여 기본 이미지의 보안 패치 또는 기타 업데이트를 기반으로 컨테이너 레지스트리에서 응용 프로그램 이미지에 대 한 업데이트를 자동화할 수 있습니다.

ACR 작업의 기본 이미지 업데이트 정보:  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: Azure Security Center와 AZURE CONTAINER REGISTRY (ACR)를 통합 하 여 취약 한 컨테이너 이미지를 정기적으로 검색할 수 있도록 합니다. 필요에 따라 Azure Marketplace에서 타사 솔루션을 배포 하 여 정기적으로 이미지 취약성 검사를 수행 합니다.

Security Center와 Azure Container Registry 통합 (미리 보기):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Security Center와 AZURE CONTAINER REGISTRY (ACR)를 통합 하 여 취약성에 대 한 컨테이너 이미지를 정기적으로 검색 하 고 위험을 분류 합니다. 필요에 따라 Azure Marketplace에서 타사 솔루션을 배포 하 여 정기적으로 이미지 취약성 검사 및 위험 분류를 수행 합니다.

Security Center와 Azure Container Registry 통합 (미리 보기):  https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 그룹: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다.  테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 확인 하는 방법:  https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Azure RBAC 이해:  https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure Container Registry는 레지스트리의 이미지에 대 한 태그 및 매니페스트를 비롯 한 메타 데이터를 유지 관리 합니다. 아티팩트에 태그를 지정 하기 위한 권장 사례를 따릅니다.

레지스트리, 리포지토리 및 이미지에 대 한 정보: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

컨테이너 이미지 태그 지정 및 버전 관리에 대 한 권장 사항: https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: Azure Container Registry는 레지스트리의 이미지에 대 한 태그 및 매니페스트를 비롯 한 메타 데이터를 유지 관리 합니다. 아티팩트에 태그를 지정 하기 위한 권장 사례를 따릅니다.

레지스트리, 리포지토리 및 이미지에 대 한 정보: https://docs.microsoft.com/azure/container-registry/container-registry-concepts

컨테이너 이미지 태그 지정 및 버전 관리에 대 한 권장 사항:  https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스의 인벤토리를 만들어야 합니다.  

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스의 유형에 대 한 제한을 설정할 수 있습니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph를 사용 하 여 쿼리를 만드는 방법:  https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 비정상적인 동작에 대 한 Azure Container Registry 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다.

진단 평가 및 감사에 대 한 Azure Container Registry 로그:  https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Log Analytics 작업 영역 이해:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor에서 사용자 지정 쿼리를 수행 하는 방법:  https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Automation는 작업 및 리소스의 배포, 작업 및 서비스 해제 중에 완전 한 제어를 제공 합니다.  권한 없는 Azure 리소스를 제거 하는 사용자 고유의 솔루션을 구현할 수 있습니다. Azure Automation 소개:  https://docs.microsoft.com/azure/automation/automation-intro


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위해 설계 되었습니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy 활용 하 여 사용자 환경에서 프로 비전 할 수 있는 서비스를 제한 합니다.

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy를 사용 하 여 특정 리소스 종류를 거부 하는 방법:  https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위해 설계 되었습니다.



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 AzureResources Manager와 상호 작용 하는 기능을 제한 합니다.

**지침**: 운영 체제별 구성 또는 타사 리소스를 사용 하 여 Azure compute 리소스 내에서 스크립트를 실행 하는 사용자의 기능을 제한 합니다.

Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법:  https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: 운영 체제별 구성 또는 타사 리소스를 사용 하 여 Azure compute 리소스 내에서 스크립트를 실행 하는 사용자의 기능을 제한 합니다.

예를 들어 Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법은 다음과 같습니다.  https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 비즈니스 운영에 필요 하지만 조직에서 더 높은 위험을 초래할 수 있는 소프트웨어, 자체 가상 머신 및/또는 가상 네트워크 내에서 격리 하 고 Azure 방화벽 또는 네트워크 보안 그룹을 사용 하 여 충분히 안전 하 게 보호 해야 합니다.

가상 네트워크를 만드는 방법:  https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용 하 여 NSG를 만드는 방법:  https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 그룹: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy 또는 Azure Security Center를 사용 하 여 모든 Azure 리소스에 대 한 보안 구성을 유지 합니다.

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 모든 계산 리소스에 대 한 보안 구성을 유지 하기 위해 "Virtual Machines 보안 구성의 취약성을 해결" Azure Security Center 권장 사항을 활용 합니다.

Azure Security Center 권장 사항을 모니터링 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure Security Center 권장 사항을 수정 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy를 구성하고 관리하는 방법: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 효과 이해:  https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

Azure DevOps에서 코드를 저장 하는 방법:  https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 설명서:  https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스에 적용 됩니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy을 사용 하 여 시스템 구성을 경고, 감사 및 적용 합니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Policy를 구성 하 고 관리 하는 방법:  https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스에 적용 됩니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 Azure 리소스에 대 한 기준 검색을 수행 합니다.

Azure Policy를 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

Azure Security Center에서 권장 사항을 수정 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Azure Policy를 사용 하 여 Azure container registry의 준수 감사:  https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스에 적용 됩니다.


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

Azure 관리 되는 Id와 통합 하는 방법:  https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault를 만드는 방법:  https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법:  https://docs.microsoft.com/azure/key-vault/managed-identity

Azure Container Registry 작업에서 Azure 관리 id를 사용 합니다.  https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 Id를 azure 서비스에 제공 합니다. 관리 ID를 사용하면 코드에 자격 증명 없이 Key Vault를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

관리 Id를 구성 하는 방법:  https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

관리 id를 사용 하 여 Azure container registry에 인증 합니다.  https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

자격 증명 스캐너를 설정 하는 방법:  https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 그룹: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: Azure Cloud Services 및 Virtual Machines에 Microsoft 맬웨어 방지 프로그램을 사용 하 여 리소스를 지속적으로 모니터링 하 고 보호 합니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다.

Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지 프로그램을 구성 하는 방법:  https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Container Registry)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 그룹: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Microsoft Azure 컨테이너 레지스트리의 데이터는 항상 자동으로 복제 되어 내구성 및 고가용성을 보장 합니다. Azure Container Registry은 계획 되거나 계획 되지 않은 이벤트에서 보호 되도록 데이터를 복사 합니다.

필요에 따라 여러 Azure 지역에서 레지스트리 복제본을 유지 하기 위해 컨테이너 레지스트리를 지역에서 복제할 수 있습니다. 

Azure Container Registry에서 지역에서 복제:  https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: 필요에 따라 레지스트리 간에 가져와서 컨테이너 이미지를 백업 합니다.

Azure 명령줄 도구 또는 Sdk를 사용 하 여 Azure Key Vault에서 고객이 관리 하는 키를 백업 합니다.

컨테이너 레지스트리에 컨테이너 이미지 가져오기:  https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Azure에서 주요 자격 증명 모음 키를 백업 하는 방법:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure 명령줄 도구 또는 sdk를 사용 하 여 Azure Key Vault에서 백업 된 고객 관리 키의 복원을 테스트 합니다.

Azure에서 Azure Key Vault 키를 복원 하는 방법:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: 실수로 또는 악의적인 삭제 로부터 키를 보호 하기 위해 Azure Key Vault에서 일시 삭제를 사용 하도록 설정할 수 있습니다.

Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 그룹: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

Azure Security Center 내에서 워크플로 자동화를 구성 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

사용자 고유의 보안 인시던트 응답 프로세스를 구축 하는 방법에 대 한 지침:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 인시던트 분석:  https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만들 수 있습니다.  https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

IT 계획 및 기능에 대 한 NIST의 게시: 테스트, 학습 및 실습 프로그램 가이드를 참조 하세요.  https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

Azure Security Center 보안 담당자를 설정 하는 방법:  https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Sentinel로 스트림할 수 있습니다.

연속 내보내기를 구성 하는 방법:  https://docs.microsoft.com/azure/security-center/continuous-export

경고를 Azure 센티널로 스트리밍하는 방법:  https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 "Logic Apps"를 통해 보안 경고 및 추천 사항에 대한 대응을 자동으로 트리거합니다.

워크플로 자동화를 구성 하 고 Logic Apps 하는 방법:  https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 그룹: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 60일 이내에 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 합니다.  https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 자세한 내용은 다음에서 확인할 수 있습니다.  https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.
