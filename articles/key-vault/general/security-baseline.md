---
title: Key Vault에 대한 Azure 보안 기준
description: Key Vault에 대한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d939c03aef8d155d83d20c7d8211e04fae26c228
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230282"
---
# <a name="azure-security-baseline-for-key-vault"></a>Key Vault에 대한 Azure 보안 기준

Key Vault에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 그룹: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: Azure 개인 링크를 사용 하 여 Azure Key Vault를 통합 합니다. 

Azure 개인 링크 서비스를 사용 하면 가상 네트워크의 개인 끝점을 통해 azure 서비스 (예: Azure Key Vault) 및 Azure 호스트 된 고객/파트너 서비스에 액세스할 수 있습니다.

Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

Azure 개인 링크를 사용 하 여 Key Vault를 통합 하는 방법:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성과 트래픽에 대한 모니터링 및 기록

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 Key Vault 구성 된 리소스의 보안을 유지 합니다. 

Azure Security Center에서 제공 하는 네트워크 보안에 대 한 자세한 내용은 다음을 수행 하십시오. 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 배포 된 서비스 거부 공격 으로부터 보호 하기 위해 Key Vault 인스턴스와 연결 된 Azure 가상 네트워크에서 Azure DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

 
Azure Portal를 사용 하 여 Azure DDoS Protection Standard 관리: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center에서 Azure 서비스 계층에 대 한 위협 검색: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure Key Vault는 nsg (네트워크 보안 그룹)를 사용 하지 않으며 Azure Key Vault에 대 한 흐름 로그가 캡처되지 않습니다. 대신 Azure 개인 링크를 사용 하 여 Azure Key Vault 인스턴스를 보호 하 고 진단 설정을 사용 하 여 메트릭과 감사 이벤트를 기록 합니다.

Azure 개인 링크와 Key Vault 통합:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault 로깅: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: Azure Key Vault에 대해 ATP (advanced threat protection)를 구성 하 여이 요구 사항을 충족할 수 있습니다. ATP는 보안 인텔리전스의 추가 계층을 제공 합니다. 이 도구는 Azure Key Vault 계정에 액세스 하거나 악용 하려는 잠재적으로 유해한 시도를 검색 합니다.

Azure Security Center은 비정상적인 활동을 검색할 때 경고를 표시 합니다. 또한 구독 관리자에 게 의심 스러운 활동에 대 한 세부 정보 및 식별 된 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항을 전자 메일로 보냅니다.

Azure Key Vault에 대 한 advanced threat protection 설정:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Key Vault 인스턴스에 액세스 해야 하는 리소스의 경우 Azure Key Vault에 대해 azure 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

Azure 서비스 태그 개요: https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Key Vault 인스턴스와 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft. KeyVault" 및 "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Key Vault 인스턴스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 Azure Key Vault와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.

Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 샘플:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

빠른 시작: 포털에서 청사진을 정의 하 고 할당 합니다.

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure Key Vault 인스턴스에 대 한 네트워크 보안 및 트래픽 흐름과 관련 된 리소스에 대 한 태그를 사용 하 여 메타 데이터 및 논리 조직을 제공 합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

태그를 사용 하 여 Azure 리소스를 구성 합니다.

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Key Vault 인스턴스와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트 보기 및 검색:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor를 사용 하 여 활동 로그 경고를 만들고 확인 하 고 관리 합니다.

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: 해당 사항 없음 Microsoft는 로그의 타임 스탬프에 대해 Azure Key Vault와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 Azure Key Vault에서 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Azure Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 Azure Storage 계정을 사용 합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

Azure Key Vault 로깅:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

빠른 시작: 온-보드 Azure 센티널:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사, 보안 및 진단 로그에 액세스 하기 위해 Azure Key Vault 인스턴스에서 진단 설정을 사용 하도록 설정 합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

Azure Key Vault 로깅:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor 내에서 Azure Key Vault 로그를 저장 하는 데 사용 되는 Log Analytics 작업 영역에 대해 조직의 규정 준수 규정에 따라 보존 기간을 설정 합니다. Azure Storage 계정을 장기/보관 스토리지에 사용합니다.

데이터 보존 기간 변경: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 Azure Key Vault 보호 된 리소스에 대 한 결과를 정기적으로 검토 합니다. Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

빠른 시작: 온-보드 Azure 센티널:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Monitor에서 Log Analytics를 시작 합니다.

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor에서 로그 쿼리를 시작 합니다.

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Security Center에서 Key Vault에 대해 ATP (advanced threat protection)를 사용 하도록 설정 합니다. Azure Key Vault에서 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역을 Azure Sentinel에 등록하여 SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

빠른 시작: 온-보드 Azure 센티널:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Security Center의 보안 경고를 관리 하 고 대응 합니다.

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Azure Monitor 경고를 사용 하 여 이벤트에 응답:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당 사항 없음 Azure Key Vault는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Key Vault는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 그룹: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Key Vault 키, 암호 및 인증서에 대 한 액세스 권한이 있는 사용자 계정 뿐만 아니라 Azure Active Directory 등록 된 응용 프로그램의 인벤토리를 유지 관리 합니다. Azure Portal 또는 PowerShell을 사용 하 여 Key Vault 액세스를 쿼리하고 조정할 수 있습니다. PowerShell에서 액세스를 보려면 다음 명령을 사용 합니다.

(AzResource-ResourceId [KeyVaultResourceID]). Accesspolicy

Azure Active Directory를 사용 하 여 응용 프로그램 등록:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

키 자격 증명 모음에 대 한 액세스 보호:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: 해당 사항 없음 Azure Key Vault는 Active Directory에서 제공 하는 인증 및 역할 기반 액세스 제어를 사용 하 여 보안이 설정 됨에 따라 기본 암호 개념이 없습니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Key Vault 인스턴스에 대 한 액세스 권한이 있는 전용 관리 계정의 사용에 대 한 표준 운영 절차를 만듭니다. Azure Security Center Id 및 액세스 관리 (현재 미리 보기)를 사용 하 여 활성 관리 계정 수를 모니터링 합니다.

Id 및 액세스 모니터링 (미리 보기):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: AppId, TenantID 및 ClientSecret와 함께 Azure 서비스 주체를 사용 하 여 응용 프로그램을 원활 하 게 인증 하 고 Azure Key Vault 암호에 액세스 하는 데 사용 되는 토큰을 검색 합니다.

.NET을 사용 하 여 Azure Key Vault에 대 한 서비스 간 인증:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory Multi-Factor Authentication를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 (현재 미리 보기) 권장 사항을 따라 이벤트 허브 사용 리소스를 보호 합니다.

클라우드 기반 Azure Multi-Factor Authentication 배포 계획:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Id 및 액세스 모니터링 (미리 보기):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure MULTI-FACTOR AUTHENTICATION (MFA)로 구성 된 PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 Key Vault 사용 리소스에 로그인 하 고 구성 합니다. 

권한 있는 액세스 워크스테이션: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

클라우드 기반 Azure Multi-Factor Authentication 배포 계획: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하기 위해 AZURE ACTIVE DIRECTORY (AAD) PRIVILEGED IDENTITY MANAGEMENT (PIM)를 사용 합니다. AAD 위험 검색을 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 확인 합니다. 추가 로깅을 수행 하려면 Azure Security Center 위험 검색 경고를 Azure Monitor에 보내고 작업 그룹을 사용 하 여 사용자 지정 경고/알림을 구성 합니다.

Azure Key Vault에 대해 ATP (advanced threat protection)를 사용 하 여 의심 스러운 활동에 대 한 경고를 생성 합니다.

PIM (Azure AD Privileged Identity Management 배포): https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure Key Vault에 대 한 advanced threat protection 설정 (미리 보기): https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure Key Vault에 대 한 경고 (미리 보기): https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory 위험 검색: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Azure Portal에서 작업 그룹을 만들고 관리 합니다. https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 정책의 위치 조건을 구성 하 고 명명 된 위치를 관리 합니다. 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가 및 지역의 논리적 그룹을 만들 수 있습니다. 구성 된 명명 된 위치에 대 한 Key Vault 암호와 같은 중요 한 리소스에 대 한 액세스를 제한할 수 있습니다.

Azure Active Directory 조건부 액세스의 위치 조건은 무엇입니까?: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Key Vault와 같은 Azure 리소스에 대 한 중앙 인증 및 권한 부여 시스템으로 AAD (Azure Active Directory)를 사용 합니다. 이를 통해 RBAC (역할 기반 액세스 제어)가 중요 한 리소스를 관리할 수 있습니다.

 

빠른 시작: Azure Active Directory에서 새 테 넌 트를 만듭니다.

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Key Vault 관리 역할이 있는 부실 계정을 검색 하는 데 도움이 되는 AAD (Azure Active Directory) 로그를 검토 합니다. 또한 AAD 액세스 검토를 사용 하 여 그룹 멤버 자격, Azure Key Vault 액세스 하는 데 사용할 수 있는 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스는 90 일 마다 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 해야 합니다.

Azure Active Directory 보고서 및 모니터링 설명서:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure AD 액세스 검토 란?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 대한 액세스 시도 모니터링

**지침**: Azure Key Vault 및 Azure Active Directory에 대 한 진단 설정을 사용 하도록 설정 하 여 모든 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고 (예: 비활성화 된 암호에 대 한 액세스 시도)를 구성 합니다.

Azure AD 로그를 Azure Monitor 로그와 통합 합니다. https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

이전 Key Vault 솔루션에서 마이그레이션: https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory의 id 보호 및 위험 검색 기능을 사용 하 여 Azure Key Vault 보호 된 리소스와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 조직의 보안 응답을 구현 하기 위해 Azure 센티널을 통해 자동화 된 응답을 사용 하도록 설정 해야 합니다. 

Azure Active Directory 포털의 위험한 로그인 보고서: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

방법: 위험 정책 구성 및 사용: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure 센티널을 등록 하는 방법:  https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 사항 없음 Azure Key Vault에 대해 고객 Lockbox 지원 되지 않습니다.

일반 공급에서 지원 되는 서비스 및 시나리오: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 그룹: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 Azure Key Vault 사용할 수 있는 리소스에 대 한 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스 추적을 지원 합니다. 

태그를 사용 하 여 Azure 리소스를 구성 합니다. https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 특정 서브넷에 대 한 액세스를 제한 하도록 구성 된 가상 네트워크 서비스 끝점을 사용 하 여 Azure Key Vault에 대 한 액세스를 보호할 수 있습니다.

방화벽 규칙이 적용 되 면 요청이 허용 된 서브넷 또는 IP 주소 범위에서 시작 되는 경우에만 Azure Key Vault 데이터 평면 작업을 수행할 수 있습니다. 이는 Azure Portal의 Azure Key Vault 액세스에도 적용 됩니다. Azure Portal에서 키 자격 증명 모음을 검색할 수 있지만 클라이언트 컴퓨터가 허용 목록에 없는 경우 키, 암호 또는 인증서를 나열 하지 못할 수 있습니다. 이는 Azure Key Vault 선택기 및 기타 Azure 서비스에도 영향을 줍니다. 키 자격 증명 모음 목록을 볼 수 있지만 방화벽 규칙으로 인해 클라이언트 컴퓨터에서 그렇게 하지 못하는 경우에는 목록 키가 표시 되지 않습니다.

Azure Key Vault 방화벽 및 가상 네트워크를 구성 합니다. https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault에 대 한 Virtual network 서비스 끝점: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Azure Key Vault 내에 저장 된 모든 데이터는 중요 한 것으로 간주 됩니다. Azure Key Vault 데이터 평면 액세스 제어를 사용 하 여 Azure Key Vault 암호에 대 한 액세스를 제어 합니다. Key Vault의 기본 제공 방화벽을 사용 하 여 네트워크 계층에서 액세스를 제어할 수도 있습니다. Azure Key Vault에 대 한 액세스를 모니터링 하려면 Key Vault 진단 설정을 사용 하도록 설정 하 고 Azure Storage 계정 또는 Log Analytics 작업 영역으로 로그를 보냅니다.

키 자격 증명 모음에 대 한 액세스 보호: https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Azure Key Vault 방화벽 및 가상 네트워크를 구성 합니다. https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault 로깅: https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 인증, 관리 및 데이터 평면 액세스를 위해 Azure Key Vault에 대 한 모든 트래픽은 암호화 되며 HTTPS: 포트 443을 통해 전달 됩니다. 그러나 CRL의 경우에 따라 HTTP [포트 80] 트래픽이 발생 합니다. 

방화벽 뒤에 Azure Key Vault 액세스: https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 해당 사항 없음 Azure Key Vault (암호, 키 및 인증서) 내의 모든 데이터는 중요 한 것으로 간주 됩니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure Key Vault 인스턴스의 관리 및 데이터 평면에 안전 하 게 액세스할 수 있습니다.

키 자격 증명 모음에 대 한 액세스 보호:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Microsoft는 Azure Key Vault에 대 한 기본 인프라를 관리 하 고, 고객 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

Azure Key Vault란?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 고객 데이터 보호:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: 모든 관리 되는 개체 (키, 인증서 및 비밀)는 Azure Key Vault에서 미사용으로 암호화 됩니다.

지원 설명서:

- [암호화 모델 및 키 관리 테이블](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Monitor의 Azure Key Vault Analytics 솔루션을 사용 하 여 Azure Key Vault 감사 이벤트 로그를 검토 합니다.

Azure Monitor의 Azure Key Vault 분석 솔루션:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 그룹: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Microsoft는 Azure Key Vault을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음 Microsoft는 Key Vault을 지 원하는 기본 시스템에서 패치 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: Microsoft는 Key Vault을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Security Center에서 제공 하는 기본 위험 등급 (보안 점수)을 사용 합니다.

Azure Security Center에서 보안 점수를 향상 시킵니다.

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 그룹: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (Azure Key Vault 인스턴스 포함)를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리를 실행 합니다.

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

현재 계정에서 액세스할 수 있는 구독을 가져옵니다.

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC(Azure 역할 기반 액세스 제어)란?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타 데이터를 제공 하 Azure Key Vault 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 합니다.

태그를 만들고 사용 하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 적절 한 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure Key Vault 인스턴스 및 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

추가 Azure 구독을 만듭니다.

https://docs.microsoft.com/azure/billing/billing-create-subscription

리소스 구성 및 관리를 위한 관리 그룹 만들기:

https://docs.microsoft.com/azure/governance/management-groups/create

태그를 사용 하 여 Azure 리소스를 구성 합니다. https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어 목록을 정의 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

빠른 시작: Azure 리소스 그래프 탐색기를 사용 하 여 첫 번째 리소스 그래프 쿼리를 실행 합니다. https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 해당 사항 없음 이 권장 사항은 Azure에서 계산 리소스 뿐만 아니라 전체적으로 사용 됩니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure 정책을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정 합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy 샘플: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 AzureResources Manager와 상호 작용 하는 기능을 제한 합니다.

**지침**: Azure 조건부 액세스를 사용 하 여 사용자가 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 하 여 AZURE RESOURCE MANAGER (ARM)과 상호 작용 하는 기능을 제한 합니다. 이로 인해 Key Vault 구성을 사용 하는 것과 같이 보안 수준이 높은 환경에서 리소스를 만들고 변경할 수 없습니다.

조건부 액세스를 사용 하 여 Azure 관리에 대 한 액세스 관리:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


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

*자세한 내용은 [보안 그룹: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft. keyvault" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Key Vault 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만들 수 있습니다. 다음과 같은 Azure Key Vault에 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

Key Vault 개체를 복구할 수 있어야 합니다.

Key Vault의 진단 설정을 Log Analytics 작업 영역에 배포

Key Vault의 진단 로그를 사용하도록 설정해야 합니다.

Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

Key Vault의 진단 설정을 Event Hub에 배포

Azure Key Vault 인스턴스에 대 한 보안 구성 기준으로 Azure Security Center의 권장 사항을 사용 합니다.

사용 가능한 Azure 정책 별칭을 확인하는 방법: 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 Azure Key Vault 사용 가능한 리소스에서 보안 설정을 적용 합니다. 

자습서: 규정 준수를 적용 하는 정책 만들기 및 관리:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Azure Policy 효과 이해: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure Key Vault 사용 리소스에 대해 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

코드를 Azure DevOps에 저장하는 방법:  

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos 설명서:  

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: "Microsoft. keyvault" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

Azure Policy를 구성하고 관리하는 방법: 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 Azure Key Vault 보호 된 리소스에 대 한 기준 검색 수행 

  

Azure Security Center에서 권장 사항을 수정 하는 방법: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: 해당 사항 없음 이 벤치 마크는 계산 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다. Azure Key Vault 일시 삭제를 사용 하도록 설정 했는지 확인 합니다.

Azure 관리 ID와 통합하는 방법: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault를 만드는 방법: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리 ID를 사용하여 Key Vault 인증을 제공하는 방법:  

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다. 

  

Azure 관리 ID와 통합하는 방법:  

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Key Vault를 만드는 방법:  

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

관리 ID를 사용하여 Key Vault 인증을 제공하는 방법:   
https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.  
  
 자격 증명 스캐너를 설정하는 방법: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 그룹: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리형 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Key Vault)를 지 원하는 기본 호스트에서 사용 하도록 설정 되지만 고객 콘텐츠에서 실행 되지 않습니다.

Azure Key Vault와 같은 비 계산 Azure 리소스로 업로드 되거나 전송 되는 콘텐츠를 미리 검색 합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지를 이해 합니다. https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 그룹: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: 다음 PowerShell 명령을 사용 하 여 Key Vault 인증서, 키, 관리 되는 저장소 계정 및 암호의 자동 백업을 정기적으로 수행 하는지 확인 합니다.

- 백업-AzKeyVaultCertificate

- 백업-AzKeyVaultKey

- 백업-AzKeyVaultManagedStorageAccount

- 백업-AzKeyVaultSecret

필요에 따라 Azure Backup 내에 Key Vault 백업을 저장할 수 있습니다.

Key Vault 인증서를 백업 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Key Vault 키를 백업하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

관리 저장소 계정 Key Vault 백업 하는 방법: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Key Vault 비밀을 백업 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/backup



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: 다음 PowerShell 명령을 사용 하 여 Key Vault 인증서, 키, 관리 되는 저장소 계정 및 암호의 백업을 수행 합니다.

- 백업-AzKeyVaultCertificate

- 백업-AzKeyVaultKey

- 백업-AzKeyVaultManagedStorageAccount

- 백업-AzKeyVaultSecret

필요에 따라 Azure Backup 내에 Key Vault 백업을 저장할 수 있습니다.

Key Vault 인증서를 백업 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Key Vault 키를 백업하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

관리 저장소 계정 Key Vault 백업 하는 방법: https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Key Vault 비밀을 백업 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup를 사용 하도록 설정 하는 방법: https://docs.microsoft.com/azure/backup



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 다음 PowerShell 명령을 사용 하 여 Key Vault 인증서, 키, 관리 되는 저장소 계정 및 암호의 데이터 복원을 정기적으로 수행 합니다.

- 복원-AzKeyVaultCertificate

- 복원-AzKeyVaultKey

- 복원-AzKeyVaultManagedStorageAccount

- 복원-AzKeyVaultSecret

Key Vault 인증서를 복원 하는 방법:  https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Key Vault 키를 복원 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

관리 저장소 계정을 Key Vault 복원 하는 방법: https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Key Vault 비밀을 복원 하는 방법: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: Azure Key Vault에 대해 일시 삭제를 사용 하도록 설정 했는지 확인 합니다. 일시 삭제는 키, 암호 및 인증서와 같은 삭제 된 key vault 및 자격 증명 모음 개체를 복구할 수 있습니다. 

Azure Key Vault의 일시 삭제를 사용 하는 방법: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 그룹: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다. 이러한 프로세스는 Key Vault 암호를 사용 하는 것과 같은 중요 한 시스템을 보호 하는 데 중점을 두어야 합니다.

Azure Security Center 내에서 워크플로 자동화를 구성하는 방법:  

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 인시던트 분석:    

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

고객이 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립을 지원할 수도 있음:  

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 또한 구독(예: 프로덕션, 비-프로덕션) 및 Azure 리소스를 명확 하 게 식별 하 고 범주화 하기 위한 이름 지정 시스템을 만듭니다. 특히 Azure Key Vault 암호와 같은 중요 한 데이터를 처리 합니다.


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure Key Vault 인스턴스 및 관련 리소스를 보호 하는 연습을 수행 합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

NIST 게시물: IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드 참조:  

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

Azure Security Center 보안 연락처를 설정하는 방법: 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보내 Azure Key Vault 사용 가능한 리소스의 위험을 식별할 수 있습니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다.  Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. 

 

연속 내보내기를 구성 하는 방법: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

경고를 Azure Sentinel로 스트림하는 방법:  

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용 하 여 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 자동으로 응답을 트리거하여 Azure Key Vault 보호 된 리소스를 보호 합니다. 

 

워크플로 자동화 및 Logic Apps를 구성하는 방법:  

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 그룹: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 60일 이내에 수정

**지침**: Azure Key Vault 서비스에서 직접 펜 테스트를 수행 하지는 않지만 Key Vault를 사용 하는 Azure 리소스를 테스트 하 여 비밀의 보안을 유지 하는 것이 좋습니다.

침투 테스트가 Microsoft 정책을 위반 하지 않도록 하려면 Microsoft Engagement 참여 규칙을 따라야 합니다.

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

다음 백서에서는 Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략 및 실행에 대한 자세한 내용을 확인할 수 있습니다.  

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
