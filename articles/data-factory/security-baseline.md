---
title: Azure Data Factory에 대 한 Azure 보안 기준
description: Azure Data Factory에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 57786aa343fd2ea863b17f65253e5d4a4a6b88ce
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226831"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Azure Data Factory에 대 한 Azure 보안 기준

Azure Data Factory에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: IR (Azure-SSIS Integration Runtime)을 만들 때 가상 네트워크와 연결 하는 옵션이 있습니다. 이렇게 하면 Azure Data Factory NSG (네트워크 보안 그룹) 및 부하 분산 장치 등의 특정 네트워크 리소스를 만들 수 있습니다. 또한 사용자 고유의 고정 공용 IP 주소를 제공 하거나 사용자에 게 해당 주소를 만들도록 Azure Data Factory 수 있습니다. Azure Data Factory에 의해 자동으로 생성 되는 NSG에서 포트 3389는 기본적으로 모든 트래픽에 대해 열려 있습니다. 관리자만 액세스할 수 있도록 하려면이 잠금을 해제 합니다.

자체 호스팅 IRs는 온-프레미스 컴퓨터 또는 가상 네트워크 내의 Azure 가상 머신에 배포할 수 있습니다. 가상 네트워크 서브넷 배포에 관리 액세스만 허용 하도록 구성 된 NSG가 있는지 확인 합니다. Azure-SSIS IR는 보호를 위해 각 IR 노드의 windows 방화벽 규칙에서 포트 3389 아웃 바운드를 기본적으로 허용 하지 않습니다. NSG를 서브넷에 연결 하 고 엄격한 규칙을 설정 하 여 가상 네트워크 구성 리소스를 보호할 수 있습니다.

개인 링크를 사용할 수 있는 경우 개인 끝점을 사용 하 여 Azure SQL Server와 같은 Azure Data Factory 파이프라인에 연결 되는 모든 리소스를 보호 합니다. 개인 링크를 사용 하면 가상 네트워크와 서비스 간의 트래픽이 Microsoft 백본 네트워크를 통해 이동 하 여 공용 인터넷에서 노출을 제거 합니다.

* [Azure-SSIS IR를 만드는 방법](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

* [자체 호스팅 IR을 만들고 구성 하는 방법](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

* [Virtual Network를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [가상 네트워크에 Azure-SSIS IR 연결](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#virtual-network-configuration)

* [Azure 개인 링크 이해](https://docs.microsoft.com/azure/private-link/private-link-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 Integration Runtime 배포와 연결 된 가상 네트워크 및 네트워크 보안 그룹에 대 한 네트워크 보호 권장 사항을 수정 합니다.

또한 Integration Runtime 배포를 보호 하는 NSG에 대 한 NSG (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다.

또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 사항 없음 이 권장 사항은 Azure 앱 서비스 또는 웹 응용 프로그램을 호스트 하는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 배포 된 서비스 거부 공격 으로부터 보호 하기 위해 Integration Runtime 배포와 연결 된 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

* [DDoS 보호를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Integration Runtime 배포를 보호 하는 nsg에 대 한 nsg (네트워크 보안 그룹) 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다.

또한 NSG 흐름 로그를 Log Analytics 작업 영역에 보내고, 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 인사이트를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화하고, 핫 스폿을 식별하며, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하며, 잘못된 네트워크 구성을 파악할 수 있다는 것입니다.

* [NSG 흐름 로그를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [트래픽 분석을 사용하도록 설정하고 사용하는 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: Azure-SSIS IR의 아웃 바운드 트래픽을 검사 하려는 경우, Azure express 경로 force를 통해 Azure-SSIS IR에서 온-프레미스 방화벽 어플라이언스로 시작 된 트래픽 또는 IDS/IPS 기능을 지 원하는 Azure Marketplace에서 Nva (네트워크 가상 어플라이언스)를 통해 온-프레미스 방화벽 어플라이언스로 시작 된 트래픽을 라우팅할 수 있습니다. 페이로드 검사에 기반한 침입 탐지 및/또는 방지 기능이 요구 사항이 아니면, 위협 인텔리전스가 포함된 Azure Firewall을 사용할 수 있습니다.

* [가상 네트워크에 Azure-SSIS Integration Runtime 연결](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [Azure 방화벽을 배포 하는 방법](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 사항 없음 이 권장 사항은 Azure 앱 서비스 또는 웹 응용 프로그램을 호스트 하는 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: virtual network 서비스 태그를 사용 하 여 nsg (네트워크 보안 그룹) 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: DataFactoryManagement)을 지정 하 여 해당 서비스에 대 한 인바운드 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

* [서비스 태그 이해 및 사용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Azure Data Factory 특정 서비스 태그 이해](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy을 사용 하 여 Azure 데이터 팩터리 인스턴스와 연결 된 네트워크 설정 및 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "DataFactory" 및 "" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Data Factory 인스턴스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같이 네트워킹 또는 Azure Data factory 인스턴스와 관련 된 기본 제공 정책 정의를 사용할 수도 있습니다.
- DDoS Protection 표준을 사용하도록 설정해야 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [네트워킹에 대 한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure Data Factory 인스턴스에 대 한 네트워크 보안 및 트래픽 흐름과 관련 된 리소스에 대 한 태그를 사용 하 여 메타 데이터 및 논리 조직을 제공 합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Data Factory 인스턴스와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

* [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 로그의 타임 스탬프에 대 한 Azure Data Factory와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 Azure Data Factory에서 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Azure Data Factory 활동 로그를 수신 하도록 구성 된 Log Analytics 작업 영역을 쿼리할 수 있습니다. 장기/보관 로그 스토리지 또는 이벤트 허브에 Azure Storage 계정을 사용하면 데이터를 다른 시스템으로 내보낼 수 있습니다.

또는 온 보드 데이터를 Azure 센티널 또는 타사 SIEM (보안 인시던트 및 이벤트 관리)로 설정할 수 있습니다. 또한 Git와 Azure Data Factory를 통합 하 여 변경 내용을 추적/감사 하는 기능, 버그를 발생 시키는 변경 내용 되돌리기 기능 등 여러 가지 소스 제어 이점을 활용할 수 있습니다.

* [진단 설정을 구성하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Data Factory의 소스 제어](https://docs.microsoft.com/azure/data-factory/source-control)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, azure Event Hubs 또는 보관을 위한 Azure Storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

진단 설정을 사용 하 여 메트릭 및 파이프라인 실행 데이터와 같은 Azure Data Factory의 비 계산 리소스에 대 한 진단 로그를 구성 합니다. Azure Data Factory는 45 일간 파이프라인 실행 데이터를 저장 합니다. 더 오랜 기간 동안이 데이터를 보존 하려면 감사 또는 수동 검사를 위해 저장소 계정에 진단 로그를 저장 하 고 보존 기간 (일)을 지정 합니다. 또한 로그를 Azure Event Hubs로 스트리밍하 고 분석을 위해 로그를 Log Analytics 작업 영역으로 보낼 수 있습니다.

* [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [진단 로그 Azure Data Factory 이해](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Azure VM (가상 컴퓨터)에서 Integration Runtime를 실행 하는 경우 Azure Monitor를 사용 하 여 가상 컴퓨터에서 데이터를 수집할 수 있습니다. Log Analytics VM 확장을 설치 하면 Azure Monitor Azure Vm에서 데이터를 수집할 수 있습니다. 그런 다음 Virtual Machines에 대 한 보안 이벤트 로그 모니터링을 제공할 수 Azure Security Center. 보안 이벤트 로그가 생성 하는 데이터의 양이 지정 된 경우 기본적으로 저장 되지 않습니다.

조직에서 보안 이벤트 로그 데이터를 유지 하려는 경우 데이터 컬렉션 계층 내에 저장할 수 있으며,이 시점에서 Log Analytics 쿼리할 수 있습니다.

* [Azure Monitor의 Azure Virtual Machines에서 데이터를 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Azure Security Center에서 데이터 수집 사용](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Data Factory에 대 한 진단 설정을 사용 하도록 설정 합니다. Log Analytics 작업 영역에 로그를 저장하도록 선택한 경우 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. 장기/보관 스토리지에 Azure Storage 계정을 사용합니다.

* [Azure Data Factory에서 진단 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#set-up-diagnostic-logs)

* [Log Analytics 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Data Factory에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics를 사용 하 여 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. 또한 Azure Data Factory 배포와 관련 된 데이터 저장소에 대 한 진단 설정을 사용 하도록 설정 해야 합니다. 진단 설정을 사용 하도록 설정 하는 방법에 대 한 지침은 각 서비스의 보안 기준을 참조 하세요.

Azure VM (가상 컴퓨터)에서 Integration Runtime를 실행 하는 경우 VM에 대 한 진단 설정도 사용 하도록 설정 합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

* [Log Analytics 스키마](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)

* [Azure Monitor를 사용 하 여 Azure 가상 머신에서 데이터를 수집 하는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: &amp; Azure Monitor의 경고 메트릭 섹션으로 이동 하 여 Data Factory에서 지원 되는 메트릭에 대 한 경고를 발생 시킬 수 있습니다.

Azure Data Factory에 대 한 진단 설정을 구성 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역 내에서 미리 정의한 일련의 조건이 발생하면 경보가 발생하도록 구성합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

또한 데이터 저장소와 관련 된 서비스에 대 한 진단 설정을 사용 하도록 설정 했는지 확인 합니다. 각 서비스의 보안 기준선에서 지침을 참조할 수 있습니다.

* [Azure Data Factory의 경고](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)

* [지원 되는 모든 메트릭 페이지](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)

* [Log Analytics 작업 영역에서 경고를 구성하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 azure Cloud Services에 Microsoft 맬웨어 방지 프로그램을 사용 하 고 Virtual Machines 하 고 이벤트를 Azure Storage 계정에 기록 하도록 가상 머신을 구성할 수 있습니다. 저장소 계정에서 이벤트를 수집 하 고 해당 하는 경우 경고를 생성 하도록 Log Analytics 작업 영역을 구성 합니다. Azure Security Center "계산 앱"의 권장 사항을 따릅니다 &amp; .

* [Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지 프로그램을 구성 하는 방법](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Virtual Machines에 대 한 게스트 수준 모니터링을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Data Factory는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: Azure VM (가상 머신)에서 Integration Runtime를 실행 하는 경우 명령줄 감사 로깅을 사용 하도록 설정할 수 있습니다. Azure Security Center은 Azure Vm에 대 한 보안 이벤트 로그 모니터링을 제공 합니다. Security Center은 지원 되는 모든 Azure Vm 및 자동 프로 비전이 사용 하도록 설정 된 경우 생성 되는 새 Vm에 대 한 Microsoft Monitoring Agent를 프로 비전 하거나 에이전트를 수동으로 설치할 수 있습니다. 에이전트는 프로세스 생성 이벤트 4688 및 이벤트 4688 내의 CommandLine 필드를 활성화합니다. VM에서 생성된 새로운 프로세스는 이벤트 로그에서 기록되고 Security Center의 검색 서비스에 의해 모니터링됩니다.

* [Azure Security Center에서 데이터 수집](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Data Factory 내에서 정기적으로 사용자 액세스를 추적 하 고 조정 해야 합니다. 데이터 팩터리 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정이 참여자 또는 소유자 역할의 구성원이거나, Azure 구독의 관리자여야 합니다.

또한 테 넌 트 수준에서 AD (Azure Active Directory)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 Azure Data Factory 인스턴스의 제어 평면에 대 한 관리 권한이 있는 관리 그룹의 멤버인 계정을 검색 합니다.

Azure AD는 사용자 액세스를 관리할 수 있는 권장 되는 방법 이지만, Azure VM (가상 머신)에서 Integration Runtime를 실행 하는 경우 VM에도 로컬 계정이 있을 수 있습니다. 로컬 및 도메인 계정은 일반적으로 최소한의 공간을 사용 하 여 검토 하 고 관리 해야 합니다. 또한 Just-in-time 기능에 대해 권한 있는 Id 관리자를 검토 하 여 관리 권한의 가용성을 줄이도록 권고 합니다.

* [Azure Data Factory용 역할 및 권한](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

* [권한 있는 Id 관리자에 대 한 정보](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [로컬 계정에 대 한 정보](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Data Factory AD (Azure Active Directory)를 사용 하 여 Azure Portal 및 Azure Data Factory 콘솔에 대 한 액세스를 제공 합니다. Azure AD에는 기본 암호 개념이 없지만 사용자 지정 또는 타사 애플리케이션의 기본 암호를 변경하거나 허용하지 않을 책임이 사용자에게 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Data Factory 콘솔 뿐만 아니라 Azure 제어 평면 (Azure Portal)에 대 한 액세스에 전용 관리 계정을 사용 하는 방법에 대 한 표준 운영 절차를 만듭니다. Azure Security Center Id 및 액세스 관리를 사용 하 여 Azure AD 내의 관리 계정 수를 모니터링 합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.
- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 azure Virtual Machines의 관리자 계정도 Azure 권한 있는 Id 관리자 (PIM)를 사용 하 여 구성할 수 있습니다. Azure 권한 있는 Id 관리자는 특정 시간 프레임에 대 한 사용 권한만 사용할 수 있도록 하 고 두 번째 사용자가 승인할 수 있도록 Just-in-time 권한 상승, Multi-Factor Authentication 및 위임 옵션과 같은 몇 가지 옵션을 제공 합니다.

* [Azure Security Center Id 및 액세스 이해](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy를 사용하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [권한 있는 Id 관리자에 대 한 정보](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Azure Data Factory용 역할 및 권한](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Azure 앱 등록 (서비스 주체)을 사용 하 여 응용 프로그램 또는 함수가 Recovery Services 자격 증명 모음에 액세스 하 고 상호 작용 하는 데 사용할 수 있는 토큰을 검색 합니다.

* [Azure REST Api를 호출 하는 방법](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Azure AD를 사용 하 여 클라이언트 응용 프로그램 (서비스 사용자)을 등록 하는 방법](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Recovery Services API 정보](https://docs.microsoft.com/rest/api/recoveryservices/)

* [Azure Data Factory에 대 한 REST API 정보](https://docs.microsoft.com/rest/api/datafactory/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory MFA(Multi-Factor Authentication)를 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 머신(Privileged Access Workstation) 사용

**지침**: Azure 리소스에 로그인하여 구성하도록 구성된 MFA(Multi-Factor Authentication)를 통해 PAW(Privileged Access Workstation)를 사용합니다.

* [Privileged Access Workstation에 대한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure에서 MFA를 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고 생성에 Azure Active Directory 보안 보고서를 사용 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

Azure VM (가상 머신)에서 Integration Runtime를 실행 하는 경우 VM을 Azure 센티널에도 온보드 할 수 있습니다. Microsoft Azure Sentinel은 확장 가능한 클라우드 네이티브, SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답) 솔루션입니다. Azure Sentinel은 엔터프라이즈 전반에 지능적인 보안 분석 및 위협 인텔리전스를 제공하며, 경고 검색, 위협 가시성, 주도적 헌팅 및 위협 대응을 위한 단일 솔루션을 제공합니다.

* [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다.

* [Azure에서 명명된 위치를 구성하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: 데이터 팩터리는 특정 데이터 팩터리를 나타내는 Azure 리소스에 대 한 관리 id와 연결 될 수 있습니다. Azure SQL Database 인증에이 관리 되는 id를 사용할 수 있습니다. 지정된 팩터리는 이 ID를 사용하여 데이터베이스에 액세스하고 해당 데이터베이스에 대해 데이터를 복사할 수 있습니다.

Azure 가상 머신에서 IR (Integration Runtime)을 실행 하는 경우 관리 되는 id를 사용 하 여 코드에 자격 증명 없이 Key Vault를 비롯 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다. 가상 머신에서 실행 되는 코드는 관리 되는 id를 사용 하 여 Azure AD 인증을 지 원하는 서비스에 대 한 액세스 토큰을 요청할 수 있습니다.

* [Azure AD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

* [Azure 리소스에 대한 관리 ID란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

* [Azure Data Factory를 사용 하 여 Azure SQL Database 데이터 복사 및 변환](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#using-service-principal-authentication)

* [Azure SQL Database를 사용 하 여 Azure Active Directory 인증을 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: AD (Azure Active Directory)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

Azure 가상 머신에서 런타임 통합을 실행 하는 경우 로컬 보안 그룹 및 사용자를 검토 하 여 시스템을 손상 시킬 수 있는 예기치 않은 계정이 없는지 확인 해야 합니다.

* [Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID 액세스 검토를 사용하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있는 AD (Azure Active Directory) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다. Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

Azure VM (가상 머신)에서 Integration Runtime를 실행 하는 경우 VM에서 Azure 센티널로 VM을 실행 합니다. Microsoft Azure Sentinel은 확장 가능한 클라우드 네이티브, SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답) 솔루션입니다. Azure Sentinel은 엔터프라이즈 전반에 지능적인 보안 분석 및 위협 인텔리전스를 제공하며, 경고 검색, 위협 가시성, 주도적 헌팅 및 위협 대응을 위한 단일 솔루션을 제공합니다.

* [Azure 활동 로그를 Azure Monitor에 통합하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Data Factory 리소스 (예: Azure SQL Database 또는 Azure Virtual Machines)의 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. 제어 평면 (Azure Portal)의 계정 로그인 동작 편차에 대해 Azure AD ID 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

* [Azure AD 위험한 로그인을 확인하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel을 온보딩하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [SQL을 사용하여 Azure Active Directory 인증 구성 및 관리](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell)

* [Azure-SSIS Integration Runtime을 위한 Azure Active Directory 인증 활성화](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: Microsoft에서 고객 데이터에 액세스 해야 하는 지원 시나리오에서, Azure 고객 Lockbox는 고객이 고객 데이터 액세스 요청을 검토 하 고 승인 하거나 거부할 수 있는 인터페이스를 제공 합니다. Azure Lockbox는 Azure Data Factory 자체에 사용할 수 없지만 Azure Lockbox는 Azure SQL Database 및 Azure Virtual Machines를 지원 합니다.

* [고객 Lockbox 이해](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

Azure SQL Database 데이터 검색 및 분류 기능을 사용 합니다. 데이터 검색 및 분류는 &amp; 데이터베이스의 중요 한 데이터를 검색, 분류, 레이블 지정 하는 Azure SQL Database에 기본 제공 되는 고급 기능을 제공 합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Azure SQL Server에 대 한 데이터 검색 및 분류를 사용 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Integration runtime은 VNet (가상 네트워크)/서브넷으로 구분 하 고 적절 하 게 태그를 지정 해야 합니다.

 또한 개인 끝점을 사용 하 여 네트워크 격리를 수행할 수 있습니다. Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 프라이빗 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다.

* [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [관리 그룹을 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [개인 링크 이해](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Azure Data Factory 배포에 대 한 중요 한 정보를 저장 하거나 처리 하는 데이터 원본 (예: Azure SQL Database)의 경우 태그를 사용 하 여 관련 리소스를 중요 한 것으로 표시 합니다.

개인 링크를 사용할 수 있는 경우 개인 끝점을 사용 하 여 Azure Data Factory 파이프라인에 연결 되는 모든 리소스를 보호 합니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. 또한 nsg (네트워크 보안 그룹)에서 엄격한 아웃 바운드 규칙 집합을 구성 하 고 해당 nsg를 서브넷에 연결 하 여 데이터 반출의 위험을 줄일 수 있습니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Azure 개인 링크 이해](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 클라우드 데이터 저장소가 HTTPS 또는 tls를 지 원하는 경우 Data Factory의 데이터 이동 서비스와 클라우드 데이터 저장소 간의 모든 데이터 전송은 보안 채널 HTTPS 또는 tls를 통해 수행 됩니다. 사용된 TLS 버전은 1.2입니다.

Azure SQL Database와 Azure SQL Data Warehouse에 대한 모든 연결은 항상 데이터를 데이터베이스로/에서 전송하는 중에 암호화(SSL/TLS)가 필요합니다. JSON을 사용하여 파이프라인을 작성하는 동안 암호화 속성을 추가하고 연결 문자열에서 true로 설정합니다. Azure Storage의 경우 연결 문자열에 HTTPS를 사용할 수 있습니다.

* [Azure Data Factory에서 전송 암호화 이해](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Data Factory을 사용 하 여 Azure SQL Database 인스턴스를 복사 하 고 변환 하는 경우 Azure SQL Database 데이터 검색 및 분류 기능을 사용 합니다. 데이터 검색 및 분류는 &amp; 데이터베이스의 중요 한 데이터를 검색, 분류, 레이블 지정 하는 Azure SQL Database에 기본 제공 되는 고급 기능을 제공 합니다.

데이터 검색 및 분류 기능은 아직 다른 Azure 서비스에 사용할 수 없습니다.

* [Azure SQL Server에 대 한 데이터 검색 및 분류를 사용 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Data Factory 제어 평면 (Azure Portal)에 대 한 액세스를 제어 합니다.

데이터 팩터리 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정이 참여자 또는 소유자 역할의 구성원이거나, Azure 구독의 관리자여야 합니다.

Azure SQL Database와 같은 Data Factory 데이터 원본의 경우 Azure RBAC에 대 한 자세한 내용은 해당 서비스의 보안 기준을 참조 하세요.

* [Azure RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [Azure Data Factory용 역할 및 권한](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Data Factory 배포와 관련 된 데이터 저장소에 데이터 암호화 메커니즘을 사용 하도록 설정 하는 것이 좋습니다. 휴지 상태의 데이터 암호화에 대 한 자세한 내용은 해당 서비스의 보안 기준을 참조할 수 있습니다.

Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 서버 쪽 암호화 또는 ADE (Azure disk encryption)를 사용 하 여 VM (Windows Virtual Machines)의 가상 디스크를 미사용 상태로 암호화 합니다. Azure Disk Encryption은 Windows의 BitLocker 기능을 활용하여 게스트 VM 내에서 고객 관리 키를 사용하여 Managed Disks를 암호화합니다. 고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.

Azure Key Vault에 자격 증명 또는 암호 값을 저장 하 고 파이프라인 실행 중에 사용 하 여 작업에 전달할 수 있습니다. 데이터 저장소에 대 한 자격 증명을 저장 하 고 Azure Key Vault 계산할 수도 있습니다. Azure Data Factory에서는 데이터 저장소/계산을 사용하는 작업을 실행할 때 자격 증명을 검색합니다.

* [Azure Data Factory에서 휴지 상태의 암호화 이해](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

* [Azure managed disks의 서버 쪽 암호화](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption)

* [Windows Vm에 대 한 Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)

* [파이프라인 작업에서 Azure Key Vault 암호를 사용 하는 방법](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Azure Key Vault에서 자격 증명 하는 방법](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 Azure Monitor를 사용 하 여 변경 내용이 Azure Data Factory 및 관련 된 리소스에 대해 발생 하는 경고를 만듭니다.

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure Storage 분석 로깅](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure SQL Database를 데이터 저장소로 사용 하는 경우 Azure SQL Database에 대 한 고급 데이터 보안을 사용 하도록 설정 하 고 Azure SQL 서버에서 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다.

Azure VM (가상 컴퓨터)에서 Integration Runtime를 실행 하는 경우 Vm에서 취약성 평가를 수행할 Azure Security Center의 권장 사항을 따릅니다. Azure 보안 권장 또는 타사 솔루션을 사용 하 여 가상 컴퓨터에 대 한 취약성 평가를 수행 합니다.

* [Azure SQL Database에서 취약성 평가를 실행 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [고급 데이터 보안을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: azure Vm (가상 머신)에서 Integration Runtime를 실행 하는 경우 azure 업데이트 관리 솔루션을 사용 하 여 vm에 대 한 업데이트 및 패치를 관리 합니다. 업데이트 관리는 로컬로 구성 된 업데이트 리포지토리를 사용 하 여 지원 되는 Windows 시스템을 패치 합니다. System Center Updates Publisher (Updates Publisher)와 같은 도구를 사용 하 여 WSUS (Windows Server Update Services)에 사용자 지정 업데이트를 게시할 수 있습니다. 이 시나리오를 사용 하면 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하 여 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

* [Azure의 업데이트 관리 솔루션](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Azure VM에 대한 업데이트 및 패치 관리](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

* [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: Azure VM (가상 머신)에서 Integration Runtime를 실행 하는 경우 타사 패치 관리 솔루션을 사용할 수 있습니다. Azure 업데이트 관리 솔루션을 사용 하 여 가상 컴퓨터에 대 한 업데이트 및 패치를 관리할 수 있습니다. 업데이트 관리는 로컬로 구성 된 업데이트 리포지토리를 사용 하 여 지원 되는 Windows 시스템을 패치 합니다. System Center Updates Publisher (Updates Publisher)와 같은 도구를 사용 하 여 WSUS (Windows Server Update Services)에 사용자 지정 업데이트를 게시할 수 있습니다. 이 시나리오를 사용 하면 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하 여 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다.

* [Azure의 업데이트 관리 솔루션](https://docs.microsoft.com/azure/automation/automation-update-management)

* [Azure VM에 대한 업데이트 및 패치 관리](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 검색 결과를 일관 된 간격으로 내보내고 결과를 비교 하 여 취약성이 수정 되었는지 확인 합니다. Azure Security Center에서 제안 하는 취약성 관리 권장 사항을 사용 하는 경우 선택한 솔루션의 포털로 피벗 하 여 기록 검색 데이터를 볼 수 있습니다.

* [가상 컴퓨터에 대 한 통합 취약성 스캐너 이해](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 기본 취약성 스캐너를 사용할 수 있습니다. Azure Security Center에 포함된 취약성 검사기는 Qualys에서 제공합니다. Qualys의 검사기는 Azure Virtual Machines에서 실시간으로 취약성을 식별하기 위한 최고의 도구입니다.

Security Center는 취약성을 식별하는 경우 발견 사항 및 관련 정보를 권장 사항으로 표시합니다. 관련 정보에는 수정 단계, 관련 CVE, CVSS 점수 등이 포함됩니다. 하나 이상의 구독 또는 특정 가상 컴퓨터에 대해 식별 된 취약성을 볼 수 있습니다.

* [가상 컴퓨터용 통합 취약성 스캐너](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 Asset Discovery 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(예: 컴퓨팅, 스토리지, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다. 테넌트에서 적절한 권한(읽기)을 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거합니다.

클래식 Azure 리소스는 Resource Graph를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들어 사용하는 것이 좋습니다.

* [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure 구독을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

* [태그를 만들고 사용하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

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

**지침**: 컴퓨팅 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어를 정의합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스의 유형에 대 한 제한을 설정할 수 있습니다.

Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph를 사용하여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: azure VM (가상 머신)에서 Integration Runtime를 실행 하는 경우 Azure Virtual machine 인벤토리를 활용 하 여 Virtual Machines에서 모든 소프트웨어에 대 한 정보 수집을 자동화 합니다. Azure Automation은 작업과 리소스의 배포, 조작 및 서비스 해제를 수행하는 동안 완벽한 제어를 제공합니다.

참고: 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간은 Azure Portal에서 사용할 수 있습니다. 설치 날짜 및 기타 정보에 대 한 액세스 권한을 얻으려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 가져와야 합니다.

* [Azure Automation 소개](https://docs.microsoft.com/azure/automation/automation-intro)

* [Azure VM 인벤토리를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 Azure Automation는 워크 로드 및 리소스의 배포, 운영 및 서비스 해제 중에 완전 한 제어를 제공 합니다. 변경 내용 추적를 사용 하 여 Virtual Machines에 설치 된 모든 소프트웨어를 식별할 수 있습니다. 사용자 고유의 프로세스를 구현 하거나 인증 되지 않은 소프트웨어를 제거 하기 위해 Azure Automation 상태 구성을 사용할 수 있습니다.

* [Azure Automation 소개](https://docs.microsoft.com/azure/automation/automation-intro)

* [변경 내용 추적 솔루션으로 사용자 환경의 변경 내용 추적](https://docs.microsoft.com/azure/automation/change-tracking)

* [Azure Automation 상태 구성 개요](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Vm (가상 머신)에서 Integration Runtime를 실행 하는 경우 Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한 있는 소프트웨어만 실행 되 고 모든 권한이 없는 소프트웨어가 vm에서 실행 되지 않도록 차단 합니다.

* [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.
- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 적응 응용 프로그램 제어는 azure 및 비 azure 컴퓨터 (Windows 및 Linux)에서 실행할 수 있는 응용 프로그램을 제어 하는 데 도움이 되는 Azure Security Center의 지능적이 고 자동화 된 종단 간 솔루션입니다. 조직의 요구 사항에 맞지 않는 경우 타사 솔루션을 구현 합니다.

이는 Integration Runtime Azure 가상 머신에서 실행 되는 경우에만 적용 됩니다.

* [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

* [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 컴퓨팅 리소스 내에서 스크립트를 실행하는 기능 제한

**지침**: Azure 가상 머신에서 런타임 통합을 실행 하는 경우 스크립트 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용 하 여 사용자가 azure compute 리소스 내에서 스크립트를 실행 하는 기능을 제한할 수 있습니다. Azure Security Center 적응 응용 프로그램 제어를 활용 하 여 권한 있는 소프트웨어만 실행 되 고 권한이 없는 모든 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단할 수도 있습니다.

* [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: azure 환경에 배포 된 위험도 높은 응용 프로그램은 가상 네트워크, 서브넷, 구독, 관리 그룹 등을 사용 하 여 격리 될 수 있으며, azure 방화벽, Waf (웹 응용 프로그램 방화벽) 또는 nsg (네트워크 보안 그룹)를 사용 하 여 충분히 안전 하 게 보호할 수 있습니다.

* [Azure의 가상 네트워크 및 가상 머신](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Azure Firewall이란?](https://docs.microsoft.com/azure/firewall/overview)

* [Azure 웹 애플리케이션 방화벽이란?](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Azure Virtual Network란?](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Azure 관리 그룹으로 리소스 구성](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [구독 관련 결정 가이드](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 Azure Data Factory에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "DataFactory" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Data Factory 인스턴스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

* [사용 가능한 Azure 정책 별칭을 확인하는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Azure 가상 머신에서 런타임 통합을 실행 하는 경우 모든 계산 리소스에 대 한 보안 구성을 유지 하려면 Azure Security Center 권장 사항 [Virtual Machines 보안 구성의 취약성 수정]을 사용 하세요.

* [Azure Security Center 권장 사항을 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Azure Security Center 권장 사항을 수정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [Azure Resource Manager 템플릿 만들기에 대 한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Azure Vm (가상 머신)에서 Integration Runtime를 실행 하는 경우 배포를 위한 vm에 대 한 보안 구성을 유지 관리할 수 있는 몇 가지 옵션이 있습니다.
- Azure Resource Manager 템플릿: Azure Portal에서 VM을 배포 하는 데 사용 되는 JSON 기반 파일이 며 사용자 지정 템플릿을 유지 관리 해야 합니다. Microsoft는 기본 템플릿에 대 한 유지 관리를 수행 합니다.
- 사용자 지정 VHD (가상 하드 디스크): 다른 방법을 통해 관리할 수 없는 복잡 한 환경을 처리할 때와 같이 사용자 지정 VHD 파일을 사용 해야 하는 경우도 있습니다. -Azure Automation 상태 구성: 기본 OS가 배포 되 면이를 사용 하 여 보다 세부적인 설정을 제어 하 고 자동화 프레임 워크를 통해 적용할 수 있습니다.

대부분의 시나리오에서 Azure Automation 필요한 상태 구성과 결합 된 Microsoft 기본 VM 템플릿은 보안 요구 사항을 충족 하 고 유지 관리 하는 데 도움이 될 수 있습니다.

* [VM 템플릿을 다운로드 하는 방법에 대 한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Azure Resource Manager 템플릿 만들기에 대 한 정보](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Azure에 사용자 지정 VM VHD를 업로드 하는 방법](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure devops 또는 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

* [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 사용자 지정 이미지를 사용 하는 경우 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다. 컨테이너 이미지의 경우 Azure Container Registry에 저장 하 고 Azure RBAC를 활용 하 여 권한 있는 사용자만 이미지에 액세스할 수 있도록 합니다.

Data Factory 참여자 역할을 사용 하 여 데이터 팩터리를 만들고 관리 하는 데 사용할 수 있습니다.

* [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Container Registry에 대 한 Azure RBAC 이해](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

* [Azure RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

* [Azure Data Factory용 역할 및 권한](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy 정의 및 "DataFactory" 네임 스페이스의 별칭 Azure Policy 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**:이 권장 사항은 Integration Runtime Azure 가상 머신에서 실행 되는 경우에 적용할 수 있습니다. Azure Automation 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대 한 구성 관리 서비스입니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

* [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy 정의 및 "DataFactory" 네임 스페이스의 별칭 Azure Policy 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만듭니다. Azure 리소스에 대 한 구성을 자동으로 적용 하려면 [감사], [deny] 및 [없는 경우 배포] Azure Policy 사용 합니다.

* [Azure Policy를 구성하고 관리하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**:이 권장 사항은 Integration Runtime Azure 가상 머신에서 실행 되는 경우에 적용할 수 있습니다. Azure Automation 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대 한 구성 관리 서비스입니다. 안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.

* [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 서비스 ID를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다.

Azure Key Vault에 자격 증명 또는 암호 값을 저장 하 고 파이프라인 실행 중에 사용 하 여 작업에 전달할 수도 있습니다. 일시 삭제를 사용 하도록 설정 했는지 확인 합니다.

* [Azure 관리 Id와 통합 하는 방법](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Key Vault를 만드는 방법](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](https://docs.microsoft.com/azure/key-vault/managed-identity)

* [파이프라인 작업에서 Azure Key Vault 비밀 사용](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Azure Key Vault에서 일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: 데이터 팩터리를 만들 때 팩터리 생성과 함께 관리 되는 id를 만들 수 있습니다. 관리 id는 Azure Active Directory에 등록 된 관리 되는 응용 프로그램으로,이 특정 데이터 팩터리를 나타냅니다.

* [Azure Data Factory에 대 한 관리 id](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

* [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙 관리 맬웨어 방지 소프트웨어 사용

**지침**: Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 azure Windows Virtual Machines 용 Microsoft 맬웨어 방지 프로그램을 사용 하 여 리소스를 지속적으로 모니터링 하 고 방어할 수 있습니다.

* [Cloud Services 및 Virtual Machines에 대해 Microsoft 맬웨어 방지 프로그램을 구성 하는 방법](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft Antimalware는 Azure 서비스(예: Azure App Service)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 콘텐츠에서 실행되지 않습니다.

App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 합니다.

데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다.

* [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [데이터 서비스에 대 한 Azure Security Center의 위협 검색 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 배포할 때 Azure 용 Microsoft 맬웨어 방지 프로그램은 기본적으로 최신 서명, 플랫폼 및 엔진 업데이트를 자동으로 설치 합니다. Azure Security Center "계산 앱"의 권장 사항을 따라 &amp; 모든 끝점이 최신 서명으로 최신 상태를 유지 하도록 합니다. Windows OS는 Azure Security Center와 통합 되는 Microsoft Defender Advanced Threat Protection 서비스를 사용 하 여 바이러스 또는 맬웨어 기반 공격의 위험을 제한 하는 추가 보안으로 추가로 보호할 수 있습니다.

* [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 프로그램을 배포 하는 방법](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Vm (가상 컴퓨터)에서 Integration Runtime를 실행 하는 경우 Azure Backup를 사용 하도록 설정 하 고 vm을 구성 하 고, 원하는 빈도와 자동 백업 보존 기간을 구성 합니다.

데이터 저장소에 대 한 일반적인 자동화 된 백업을 수행 하는 방법에 대 한 권장 사항은 해당 서비스의 보안 기준을 참조 하세요.

* [Azure VM 백업 개요](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [VM 설정에서 Azure VM 백업](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: azure Vm (가상 머신)에서 Integration Runtime를 실행 하는 경우 원하는 빈도와 보존 기간 뿐만 아니라 Azure Backup 및 대상 Azure vm을 사용 하도록 설정 합니다. Azure Key Vault 내에서 고객이 관리 하는 키를 백업 합니다.

데이터 저장소에 대 한 일반적인 자동화 된 백업을 수행 하는 방법에 대 한 권장 사항은 해당 서비스의 보안 기준을 참조 하세요.

* [Azure VM 백업 개요](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Azure에서 키 자격 증명 모음 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Azure 가상 머신에서 Integration Runtime를 실행 하는 경우 Azure Backup 내에서 콘텐츠를 정기적으로 복원 하는 기능을 확인 하세요. 필요한 경우 격리 된 VLAN에 대 한 콘텐츠 복원을 테스트 합니다. 백업 된 고객이 관리 하는 키의 복원을 정기적으로 테스트 합니다.

데이터 저장소에 대 한 자세한 내용은 해당 서비스의 보안 기준을 참조 하 여 백업 유효성 검사에 대 한 지침을 참조 하세요.

* [Azure Virtual Machine 백업에서 파일을 복구 하는 방법](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

* [Azure에서 키 자격 증명 모음 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure Vm (가상 머신)에서 Integration Runtime를 실행 중이 고 Azure Backup를 사용 하 여 vm을 백업 하는 경우 VM은 SSE (저장소 서비스 암호화)를 사용 하 여 미사용 상태로 암호화 됩니다. Azure Backup Azure Disk Encryption를 사용 하 여 암호화 된 Azure Vm을 백업할 수도 있습니다. Azure Disk Encryption는 키 자격 증명 모음에 비밀으로 보호는 BEKs (BitLocker 암호화 키)와 통합 됩니다. Azure Disk Encryption은 KEKs (Azure Key Vault 키 암호화 키)와도 통합 됩니다. Key Vault에서 일시 삭제를 사용하도록 설정하여 실수로 또는 악의적으로 삭제되지 않도록 키를 보호합니다.

* [Vm에 대 한 일시 삭제](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)

* [Azure Key Vault 일시 삭제 개요](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다.

* [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

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

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

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

**지침**: 

* [Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다.](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
