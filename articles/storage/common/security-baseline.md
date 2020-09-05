---
title: Azure Storage에 대 한 Azure 보안 기준
description: Azure Storage에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6358f9d233b3c09dc4ae4f3ecac7c91dea8bba6e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228276"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure Storage에 대 한 Azure 보안 기준

Azure Storage에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 그룹: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure Firewall을 사용하여 리소스 보호

**지침**: 특정 공용 IP 주소 범위에서 클라이언트에 대 한 액세스를 제한 하 여 저장소 계정의 방화벽을 구성 하거나, Azure에서 vnet (가상 네트워크)를 선택 하거나, 특정 azure 리소스에 대 한 액세스를 제한 합니다. 또한 엔터프라이즈의 저장소 서비스에 대 한 트래픽이 개인 네트워크를 통해 독점적으로 이동 하도록 개인 끝점을 구성할 수 있습니다.

참고: 클래식 저장소 계정은 방화벽 및 가상 네트워크를 지원 하지 않습니다.

- [Azure Storage 방화벽을 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Azure Storage에 대 한 개인 끝점을 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2: Vnet, 서브넷, NIC 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Storage는 계층화 된 보안 모델을 제공 합니다. 스토리지 계정에 대한 액세스를 지정된 IP 주소, IP 범위 또는 Azure VNet(가상 네트워크)의 서브넷 목록에서 시작되는 요청으로 제한할 수 있습니다. Azure Security Center를 사용 하 고 네트워크 보호 권장 사항을 따라 Azure에서 네트워크 리소스를 보호할 수 있습니다. 또한 저장소 계정 방화벽을 통해 저장소 계정에 대해 구성 된 가상 네트워크/서브넷에 대해 NSG 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. 

저장소 계정에 연결 된 개인 끝점이 있는 경우 서브넷에 대 한 NSG (네트워크 보안 그룹) 규칙을 구성할 수 없습니다. 

- [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Azure Storage에 대 한 개인 끝점 이해](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요 한 웹 응용 프로그램 보호

**지침**: 해당 사항 없음 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Storage 계정에 대해 Advanced Threat Protection을 사용 하도록 설정 합니다. Azure Storage에 대 한 Advanced threat protection은 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. Azure Security Center 통합 경고는 IP 주소가 알려진 위험한 IP 주소 (예: 알려진 cryptominer) 또는 위험한 것으로 인식 되지 않는 IP 주소 인지 여부에 관계 없이 네트워크 통신이 성공적으로 확인 된 IP 주소와 연결 된 작업을 기반으로 합니다. 보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 

- [Advanced Threat Protection을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [Azure Security Center 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Network Watcher 패킷 캡처를 사용 하 여 저장소 계정과 가상 머신 간의 트래픽을 추적 하는 캡처 세션을 만들 수 있습니다. 원하는 트래픽만 캡처할 수 있도록 캡처 세션에 대 한 필터가 제공됩니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버그할 수 있습니다. 원격으로 패킷 캡처를 트리거할 수 있으면 원하는 가상 머신에서 수동으로 패킷 캡처를 실행하는 부담이 없어지고 시간이 단축됩니다. 

- [포털에서 Azure Network Watcher를 사용하여 패킷 캡처 관리](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-manage-portal)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 배포

**지침**: Azure Storage에 대 한 Advanced threat protection은 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 Azure Security Center와 통합 되며, 의심 스러운 활동 및 위협 조사 및 해결 방법에 대 한 권장 사항을 포함 하 여 구독 관리자에 게 전자 메일을 통해 전송 됩니다. 

- [Azure Storage에 대 한 advanced threat protection 구성](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: 웹 응용 프로그램에 대 한 트래픽 관리

**지침**: 해당 사항 없음 권장 사항은 Azure App Service 또는 계산 리소스에서 실행 되는 웹 응용 프로그램을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 저장소 계정에 액세스 해야 하는 가상 네트워크의 리소스에 대해, 구성 된 가상 네트워크에 대 한 Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: 저장소)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다. 

네트워크 액세스의 범위를 특정 저장소 계정으로 지정 해야 하는 경우 Virtual Network 서비스 끝점 정책을 사용 합니다.

- [서비스 태그를 사용 하는 방법에 대 한 자세한 내용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Azure Storage에 대 한 가상 네트워크 서비스 끝점 정책에 대 한 자세한 내용은](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 서비스

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대 한 표준 보안 구성 유지

**지침**: Azure Policy를 사용 하 여 Azure Storage 계정과 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft Storage" 및 "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 저장소 계정 리소스의 네트워크 구성을 감사 하거나 적용 합니다. 

저장소 계정과 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다. 저장소 계정에서 가상 네트워크 서비스 끝점을 사용 해야 합니다. 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [저장소에 대 한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [네트워크에 대 한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: nsg (네트워크 보안 그룹) 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다. 태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다. Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다. 

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [보안 구성을 사용하여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 검색 합니다.

**지침**: Azure Policy을 사용 하 여 네트워크 리소스에 대 한 구성 변경 내용을 기록 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다. 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-resource"></a>2.1: 승인 된 시간 동기화 리소스를 사용 하십시오.

**지침**: 해당 사항 없음 Microsoft는 Azure Storage 계정의 시간 소스를 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 끝점 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 하기 위해 Azure Monitor를 통해 로그를 수집 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고, 장기/보관 저장소에 대 한 Azure Storage 계정을 사용 하 고, 필요에 따라 변경 불가능 한 저장소 및 적용 된 보존 유지와 같은 보안 기능을 사용 합니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대 한 감사 로깅 사용

**지침**: Azure 스토리지 분석는 blob, 큐 및 테이블에 대 한 로그를 제공 합니다. Azure Portal를 사용 하 여 계정에 대해 기록 되는 로그를 구성할 수 있습니다. 

- [Azure Storage 계정에 대 한 모니터링을 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Storage 계정 또는 Log Analytics 작업 영역에 보안 이벤트 로그를 저장 하는 경우 조직의 요구 사항에 따라 보존 정책을 설정할 수 있습니다. 

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: Azure Storage 로그를 검토 하려면 Log Analytics 제품을 통한 쿼리와 같은 일반적인 옵션과 로그 파일을 직접 볼 수 있는 고유한 옵션을 사용할 수 있습니다. Azure Storage 로그는 $logs에서 직접 액세스 해야 하는 blob에 저장 됩니다 http://accountname.blob.core.windows.net/ . 로깅 폴더는 기본적으로 숨겨져 있으므로 직접 탐색 해야 합니다. 목록 명령에 표시 되지 않습니다.) 

또한 Azure Storage 계정에 대해 Advanced Threat Protection을 사용 하도록 설정 합니다. Azure Storage에 대 한 Advanced threat protection은 저장소 계정에 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 하는 추가 보안 인텔리전스 계층을 제공 합니다. 보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 Azure Security Center와 통합 되며, 의심 스러운 활동 및 위협 조사 및 해결 방법에 대 한 권장 사항을 포함 하 여 구독 관리자에 게 전자 메일을 통해 전송 됩니다. 

- [데이터 기록 및 검토](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [Advanced Threat Protection을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Security Center에서 저장소 계정에 대 한 Advanced Threat Protection을 사용 하도록 설정 합니다. 저장소 계정에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공하므로 Log Analytics 작업 영역을 Azure Sentinel에 온보딩합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Azure Security Center에서 경고를 관리 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Log analytics 로그 데이터를 경고 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure Storage 분석 로깅](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅을 중앙 집중화

**지침**: Azure Security center를 사용 하 고 Azure Storage에 대해 위협 방지를 사용 하도록 설정 하 여 활성 연결 종료 노드 (익명화 프록시)에서 해시 평판 분석과 의심 스러운 액세스를 사용 하 여 Azure Storage에 대 한 맬웨어 업로드를 검색 합니다. 

- [Azure Storage에 대 한 advanced threat protection 구성](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: Azure Monitor의 Azure DNS 분석 (미리 보기) 솔루션은 보안, 성능 및 운영에 대 한 정보를 DNS 인프라에 수집 합니다. 현재는 Azure Storage 계정을 지원 하지 않지만 타사 dns 로깅 솔루션을 사용할 수 있습니다. 

- [DNS 분석 미리 보기 솔루션으로 DNS 인프라에 대한 정보 수집](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅을 사용 합니다.

**지침**: 해당 사항 없음 벤치 마크는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 그룹: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure AD에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다. 

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당 하는 경우 기본 암호를 변경 합니다.

**지침**: Azure Storage 계정 또는 Azure Active Directory는 기본 또는 빈 암호의 개념이 없습니다. Azure Storage은 azure RBAC (역할 기반 액세스 제어) 뿐만 아니라 공유 키 및 SAS (공유 액세스 서명)를 지 원하는 액세스 제어 모델을 구현 합니다. 공유 키 및 SAS 인증의 특징은 호출자와 연결 된 id가 없으므로 보안 주체 권한 기반 권한 부여를 수행할 수 없다는 것입니다. 

- [Azure Storage 데이터에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [보안 주체 및 Azure Storage 계정에 대 한 액세스 제어 이해](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 저장소 계정에 대 한 액세스 권한이 있는 전용 관리 계정의 사용에 대 한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다. 

Microsoft 서비스 및 Azure ARM에 대해 Azure AD Privileged Identity Management 권한 있는 역할을 사용 하 여 Just-in-time/Just-in-time 액세스를 사용 하도록 설정할 수도 있습니다. 

- [Azure Security Center Id 및 액세스 이해](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Privileged Identity Management 개요](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO (Single Sign-on)를 사용 하십시오.

**지침**: 가능 하면 서비스 별로 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center Id 및 액세스 관리 권장 사항을 사용 합니다. 

- [Azure AD를 사용 하 여 SSO 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Storage 데이터에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증을 사용 하십시오.

**지침**: Azure Active Directory 다단계 인증을 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 관리 권장 사항에 따라 저장소 계정 리소스를 보호 합니다. 

- [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터 (권한 있는 액세스 워크스테이션)를 사용 합니다.

**지침**: MFA가 구성 된 paw (권한 있는 액세스 워크스테이션)를 사용 하 여 저장소 계정 리소스에 로그인 하 고 구성 합니다. 

- [Privileged Access Workstation에 대한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Monitor에 Azure Security Center 위험 검색 경고를 보내고 작업 그룹을 사용 하 여 사용자 지정 경고/알림을 구성 합니다. 의심 스러운 활동에 대 한 경고를 생성 하려면 Azure Storage 계정에 대해 Advanced Threat Protection을 사용 하도록 설정 합니다. 또한 Azure AD 위험 감지를 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 볼 수 있습니다. 

- [Azure Storage 계정에 대 한 Advanced Threat Protection을 설정 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [Azure AD 위험 탐지 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [사용자 지정 경고 및 알림에 대 한 작업 그룹을 구성 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다. 

- [Azure에서 명명 된 위치를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 스토리지 계정의 리소스에 대한 클라이언트 액세스를 자세히 제어하기 위해 RBAC(역할 기반 액세스 제어)를 제공합니다.  가능 하면 계정 키를 사용 하는 대신 보안 모범 사례로 Azure AD 자격 증명을 사용 하 여 더 쉽게 손상 시킬 수 있습니다. 응용 프로그램 디자인에 Blob 저장소에 대 한 액세스를 위해 공유 액세스 서명이 필요한 경우 Azure AD 자격 증명을 사용 하 여 뛰어난 보안을 위해 가능한 경우 사용자 위임 공유 액세스 서명 (SAS)을 만듭니다.

- [Azure AD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Azure Storage 리소스 공급자를 사용 하 여 관리 리소스에 액세스](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Azure Portal에서 RBAC를 사용 하 여 Azure Blob 및 큐 데이터에 대 한 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Azure Storage 데이터에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스를 검토 하 고 조정 합니다.

**지침**: 저장소 계정 관리 역할이 있는 계정을 포함할 수 있는 오래 된 계정을 검색 하는 데 도움이 되는 Azure Active Directory 로그를 검토 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격을 효율적으로 관리 하 고, 저장소 계정 리소스에 액세스 하는 데 사용할 수 있는 엔터프라이즈 응용 프로그램에 액세스 하 고, 역할 할당을 사용 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 해야 합니다.  

또한 SAS (공유 액세스 서명)를 사용 하 여 데이터의 보안을 손상 시 키 지 않고 저장소 계정의 리소스에 대 한 안전한 위임 된 액세스를 제공할 수 있습니다. 클라이언트에서 액세스할 수 있는 리소스, 해당 리소스에 대 한 사용 권한, SAS가 유효한 기간 (기타 매개 변수)을 제어할 수 있습니다.

또한 컨테이너 및 blob에 대 한 익명 읽기 액세스를 검토 합니다. 기본적으로, 컨테이너와 컨테이너 내의 모든 Blob은 적절한 권한이 부여된 사용자만 액세스할 수 있습니다. Azure Monitor를 사용 하 여 익명 인증 조건을 사용 하 여 저장소 계정에 대 한 익명 액세스에 경고할 수 있습니다.

의심 스러운 사용자 계정 액세스의 위험을 줄이기 위한 효과적인 방법 중 하나는 사용자에 게 부여 하는 액세스 기간을 제한 하는 것입니다. 시간이 제한 된 SAS Uri는 저장소 계정에 대 한 사용자 액세스를 자동으로 만료 하는 한 가지 효과적인 방법입니다. 또한 저장소 계정 키를 자주 회전 하는 것은 저장소 계정 키를 통한 예기치 않은 액세스가 제한 된 기간을 갖도록 하는 방법입니다.

- [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Azure Storage 계정 수준에서 액세스를 확인 하 고 변경 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [Azure Portal에서 스토리지 계정 모니터링](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [저장소 계정 액세스 키 관리](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화 되는 계정에 대 한 액세스 시도를 모니터링 합니다.

**지침**: 스토리지 분석을 사용 하 여 저장소 서비스에 대 한 성공 및 실패 한 요청에 대 한 자세한 정보를 기록 합니다. 모든 로그는 $logs 컨테이너의 블록 Blob에 저장됩니다. 이 컨테이너는 Storage 계정에 대해 스토리지 분석을 사용하도록 설정하면 자동으로 작성됩니다.

Azure Active Directory 사용자 계정에 대 한 진단 설정을 만들어 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다. Azure Storage 계정에 대 한 인증 오류를 모니터링 하려면 저장소 리소스 메트릭에 대 한 특정 임계값에 도달 했을 때 알리도록 경고를 만들 수 있습니다. 또한 Azure Monitor를 사용 하 여 익명 인증 조건을 사용 하 여 저장소 계정에 대 한 익명 액세스를 경고 합니다.

- [Azure Storage 분석 로깅](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure Storage 계정에 대 한 메트릭 경고를 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory의 위험 및 id 보호 기능을 사용 하 여 저장소 계정 리소스와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 조직의 보안 응답을 구현 하기 위해 Azure 센티널을 통해 자동화 된 응답을 사용 하도록 설정 해야 합니다. 

- [Azure AD 위험한 로그인을 확인하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Microsoft에서 고객 고객 Lockbox 데이터에 액세스 해야 하는 지원 시나리오에서 (저장소 계정 미리 보기) 고객이 고객 데이터 액세스 요청을 검토 하 고 승인 또는 거부할 수 있는 인터페이스를 제공 합니다. Microsoft는 저장소 계정 내에 저장 된 조직의 암호에 대 한 액세스를 요구 하거나 요청 하지 않습니다.

- [고객 Lockbox 이해](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 그룹: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 저장소 계정 리소스를 추적 하는 데 도움을 줍니다. 

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경, 데이터 민감도 등의 개별 보안 도메인에 대해 별도의 구독, 관리 그룹 및 저장소 계정을 사용 하 여 격리를 구현 합니다.  사용 되는 네트워크의 유형 및 하위 집합에 따라 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 저장소 계정에 대 한 액세스 수준을 제어 하도록 저장소 계정을 제한할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 세트를 통해 데이터를 요청하는 애플리케이션만 스토리지 계정에 액세스할 수 있습니다. Azure AD RBAC를 통해 Azure Storage에 대 한 액세스를 제어할 수 있습니다. 또한 가상 네트워크와 서비스 간의 트래픽이 Microsoft 백본 네트워크를 통해 이동 하 여 공용 인터넷에서 노출을 제거 하도록 개인 끝점을 구성 하 여 보안을 향상 시킬 수 있습니다. 

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Virtual Network 서비스 끝점](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 중요 한 정보를 저장 하거나 처리 하는 저장소 계정 리소스의 경우 태그를 사용 하 여 리소스를 중요 한 것으로 표시 합니다. Exfiltration을 통해 데이터 손실의 위험을 줄이려면 Azure 방화벽을 사용 하 여 Azure Storage 계정에 대 한 아웃 바운드 네트워크 트래픽을 제한 합니다. 

또한 가상 네트워크 서비스 끝점 정책을 사용 하 여 서비스 끝점을 통해 Azure Storage 계정에 대 한 송신 가상 네트워크 트래픽을 필터링 하 고 특정 Azure Storage 계정에만 데이터 반출을 수행할 수 있습니다.

- [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Azure Storage에 대 한 가상 네트워크 서비스 끝점 정책](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 저장소 계정에 대해 보안 전송 필요를 사용 하도록 설정 하 여 HTTPS 사용을 적용할 수 있습니다. 이 옵션을 사용하도록 설정하면 HTTP를 사용한 연결이 거부됩니다. 또한 Azure Security Center 및 Azure Policy를 사용 하 여 저장소 계정에 대 한 보안 전송을 적용 합니다.

- [Azure Storage에서 보안 전송을 요구 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [Security Center에서 모니터링 하는 Azure 보안 정책](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Storage 계정 및 관련 리소스에 대 한 데이터 식별 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다. 

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure Active Directory (Azure AD)는 RBAC (역할 기반 액세스 제어)를 통해 보안 리소스에 대 한 액세스 권한을 부여 합니다. Azure Storage는 blob 또는 큐 데이터에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 Azure 기본 제공 역할 집합을 정의 합니다. 

- [Azure Storage 계정에 대 한 Azure 역할을 할당 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Azure Storage 리소스 공급자를 사용 하 여 관리 리소스에 액세스](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Azure Portal에서 RBAC를 사용 하 여 Azure Blob 및 큐 데이터에 대 한 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [AAD 인스턴스를 만들고 구성하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Azure Storage 데이터에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 권장 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 휴지 상태의 중요 정보 암호화

**지침**: 모든 저장소 계정에 대해 Azure Storage 암호화를 사용 하도록 설정 되어 있으며 사용 하지 않도록 설정할 수 없습니다. Azure Storage는 데이터가 클라우드에 유지 될 때 자동으로 암호화 합니다. Azure Storage에서 데이터를 읽을 때 데이터가 반환되기 전에 Azure Storage에서 암호가 해독됩니다. Azure Storage 암호화를 사용 하면 코드를 수정 하거나 응용 프로그램에 코드를 추가 하지 않고도 미사용 데이터를 보호할 수 있습니다. 

- [미사용 Azure Storage 암호화 이해](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 저장소 계정 리소스에 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다. Azure Storage 로깅을 사용 하 여 Azure Storage에 대해 수행 된 각 요청에 대 한 권한이 있는지 추적할 수도 있습니다. 로그는 OAuth 2.0 토큰을 사용 하거나 공유 키를 사용 하거나 공유 액세스 서명 (SAS)을 사용 하 여 요청이 익명으로 수행 되었는지 여부를 나타냅니다. 또한 Azure Monitor를 사용 하 여 익명 인증 조건을 사용 하 여 저장소 계정에 대 한 익명 액세스를 경고 합니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Azure Storage 분석 로깅](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Azure Storage 계정에 대 한 메트릭 경고를 구성 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 그룹: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화 된 취약점 검사 도구 실행

**지침**: Azure Security Center의 권장 사항에 따라 저장소 계정의 구성을 지속적으로 감사 하 고 모니터링 합니다. 

- [보안 권장 사항 - 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화 된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화 된 Third Party Software 패치 관리 솔루션 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백엔드 취약성 검색 비교

**지침**: 해당 사항 없음 Microsoft는 저장소 계정을 지 원하는 기본 시스템에서 취약성 관리를 수행 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure Security Center에서 제공 하는 기본 위험 등급 (보안 점수)을 사용 합니다. 

- [보안 점수 Azure Security Center 이해](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 그룹: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내의 모든 리소스 (저장소 계정 포함)를 쿼리하고 검색 합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다. 

- [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타 데이터 유지 관리

**지침**: 저장소 계정 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 하는 메타 데이터를 제공 합니다. 

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한이 없는 Azure 리소스를 삭제 합니다.

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 저장소 계정 및 관련 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다. 

또한 Azure Storage에 대해 Advanced Threat Protection을 사용 하 여 권한이 없는 Azure 리소스를 검색 합니다. 

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Storage에 대 한 advanced threat protection 구성](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스의 인벤토리를 만들어야 합니다. 


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인 되지 않은 Azure 리소스에 대 한 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다. 

 - 허용되지 않는 리소스 종류 
 - 허용되는 리소스 유형 

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 이는 저장소 계정을 사용 하는 환경 등의 높은 보안 기반 환경에서 유용할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 계산 리소스 내에서 승인 되지 않은 소프트웨어 응용 프로그램 모니터링

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인 되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 고객은 고객의 회사 정책에 따라 리소스를 만들거나 사용 하는 데 필요한 Azure Policy를 방지할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인 된 Azure 서비스만 사용 합니다.

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다. 

- 허용되지 않는 리소스 종류 
- 허용되는 리소스 유형 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 애플리케이션 목록 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 저장소 계정을 사용 하는 경우와 같이 높은 수준의 보안 환경에서 리소스를 만들고 변경 하지 못할 수 있습니다. 

- [ARM에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 계산 리소스 내에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 그룹: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대 한 보안 구성을 설정 합니다.

**지침**: "Microsoft storage" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 저장소 계정 인스턴스의 구성을 감사 하거나 적용 합니다. 다음과 같은 Azure Storage 계정에 기본 제공 Azure Policy 정의를 사용할 수도 있습니다. 

스토리지 계정에 대한 무제한 네트워크 액세스 감사  
스토리지 계정에 Advanced Threat Protection 배포  
스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.  
Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.  

저장소 계정에 대 한 보안 구성 기준으로 Azure Security Center의 권장 사항을 사용 합니다. 

- [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: 운영 체제에 대 한 보안 구성을 설정 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: 모든 Azure 리소스에 대 한 보안 구성을 유지 합니다.

**지침**: Azure Policy [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 저장소 계정 리소스에서 보안 설정을 적용 합니다. 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: 운영 체제에 대 한 보안 구성 유지

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전 하 게 저장

**지침**: Azure Repos을 사용 하 여 사용자 지정 Azure 정책, Azure Resource Manager 템플릿, 필요한 상태 구성 스크립트 등과 같은 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps에서 관리 하는 리소스에 액세스 하려면 Azure DevOps와 통합 된 경우 Azure Active Directory (Azure AD)에 정의 된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대 한 권한을 부여 하거나 거부할 수 있습니다. 또는 TFS와 통합 된 경우 Active Directory 합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps의 사용 권한 및 그룹 정보](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전 하 게 저장

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure Policy 활용 하 여 저장소 계정에 대 한 시스템 구성을 경고, 감사 및 적용 합니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다. 

- [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Security Center 활용 하 여 Azure Storage 계정 리소스에 대 한 기준 검색을 수행 합니다. 

- [Azure Security Center에서 권장 사항을 수정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대 한 자동화 된 구성 모니터링 구현

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="711-securely-manage-azure-secrets"></a>7.11: Azure 비밀을 안전 하 게 관리

**지침**: Azure Storage 데이터를 클라우드에 보관 하는 경우 자동으로 암호화 합니다. Microsoft에서 관리 하는 키를 사용 하 여 저장소 계정을 암호화 하거나 자체 키로 암호화를 관리할 수 있습니다. 고객이 제공한 키를 사용 하는 경우 Azure Key Vault를 활용 하 여 키를 안전 하 게 저장할 수 있습니다. 

또한 저장소 계정 키의 손실 또는 공개의 영향을 제한 하기 위해 저장소 계정 키를 자주 회전 합니다.

- [미사용 데이터에 대한 Azure Storage 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [저장소 계정 액세스 키 관리](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: id를 안전 하 게 자동으로 관리

**지침**: Azure Active Directory 및 관리 id를 사용 하 여 Azure Storage 계정 내의 blob 및 큐에 대 한 액세스 권한을 부여 합니다. Azure Blob 및 Queue Storage는 Azure 리소스에 대한 관리 ID를 사용하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스에 대 한 관리 되는 id는 azure Vm (가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 되는 응용 프로그램의 Azure AD 자격 증명을 사용 하 여 blob 및 큐 데이터 액세스 권한을 부여할 수 있습니다. Azure 리소스에 대 한 관리 되는 id를 Azure AD 인증과 함께 사용 하 여 클라우드에서 실행 되는 응용 프로그램에 자격 증명을 저장 하지 않을 수 있습니다. 

- [관리 id를 사용 하 여 Azure blob 및 큐 데이터에 대 한 액세스 권한을 부여 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 그룹: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Azure Storage에 대해 위협 방지를 사용 하 여 활성 연결 종료 노드 (익명화 프록시)에서 해시 평판 분석 및 의심 스러운 액세스를 사용 하 여 Azure Storage에 대 한 맬웨어 업로드를 검색 합니다. 

또한 조직의 요구 사항을 충족 하기 위해 App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 하기 전에 맬웨어에 대해 모든 콘텐츠를 미리 검색할 수 있습니다.

- [Azure Storage에 대 한 advanced threat protection 구성](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: 해당 없음. 이 추천 사항은 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼용 맬웨어 방지를 처리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 그룹: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Microsoft Azure 저장소 계정의 데이터는 항상 자동으로 복제 되어 내구성 및 고가용성을 보장 합니다. Azure Storage는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되었거나 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 지리적으로 분리된 지역 간에 데이터를 복제하도록 선택할 수 있습니다. 

Azure automation을 사용 하도록 설정 하 여 blob의 정기적인 스냅숏을 만들 수도 있습니다.

- [중복성 및 서비스 수준 계약 Azure Storage 이해](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [Blob의 스냅숏 만들기](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation 개요](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 저장소 계정 지원 서비스에서 데이터를 백업 하려면 azcopy 또는 타사 도구를 사용 하는 등 여러 가지 방법을 사용할 수 있습니다. Azure Blob Storage에 대한 변경 불가능한 스토리지를 사용하면 사용자가 중요 비즈니스용 데이터 개체를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다.

- [AzCopy 시작](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [Blob Storage에 대한 불변성 정책 설정 및 관리](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

Azure CLI 또는 PowerShell을 사용 하 여 Azure Key Vault 내에서 고객 관리/제공 키를 백업할 수 있습니다. 

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함 한 모든 백업의 유효성을 검사 합니다.

**지침**: 다음 PowerShell 명령을 사용 하 여 Key Vault 인증서, 키, 관리 되는 저장소 계정 및 암호의 데이터 복원을 정기적으로 수행 합니다. 

AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Key Vault 인증서를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Key Vault 키를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [관리 되는 저장소 계정을 Key Vault 복원 하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy은 저장소 계정에서 blob, 파일 및 테이블 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다.](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

참고: Azure Table storage 서비스에서 데이터를 복사 하려면 AzCopy 버전 7.3을 설치 합니다.


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키의 보호 보장

**지침**: 저장소 계정에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure Key Vault을 사용 하 여 키를 저장 해야 합니다. 키 자격 증명 모음에서 일시 삭제 및 제거 안 함 속성을 모두 사용 하도록 설정 해야 합니다. Key Vault의 일시 삭제 기능을 사용 하면 삭제 된 자격 증명 모음 및 자격 증명 모음 개체 (예: 키, 암호 및 인증서)를 복구할 수 있습니다. 저장소 계정 데이터를 Azure Storage blob에 백업 하는 경우 blob 또는 blob 스냅숏이 삭제 될 때 일시 삭제를 사용 하 여 데이터를 저장 하 고 복구 합니다. 백업을 중요 한 데이터로 처리 하 고이 기준의 일부로 관련 액세스 및 데이터 보호 제어를 적용 해야 합니다. 또한 향상 된 보호를 위해 비즈니스에 중요 한 데이터 개체를 웜 (한 번 쓰기, 읽기 다) 상태에 저장할 수 있습니다.

- [Azure Key Vault의 일시 삭제를 사용 하는 방법](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Azure Storage Blob에 대한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 그룹: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-incident-response-guide"></a>10.1: 인시던트 응답 만들기 가이드

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [태그를 사용하여 Azure 리소스 구성](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트에 대 한 세부 정보를 제공 하 고 보안 인시던트에 대 한 경고 알림을 구성 하십시오.

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

- [경고를 Azure Sentinel로 스트림하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 그룹: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 합니다.

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
