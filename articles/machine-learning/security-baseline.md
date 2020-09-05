---
title: Azure Machine Learning에 대 한 Azure 보안 기준
description: Azure Machine Learning 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 168aec49dc9b14af57df98ebc4c997f8dfb27c9e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228361"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 Azure 보안 기준

Microsoft Azure Machine Learning에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다. 이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다. 자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [Azure 보안 벤치 마크: 네트워크 보안](/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Machine Learning는 계산 리소스에 대 한 다른 Azure 서비스에 의존 합니다. 컴퓨팅 리소스(컴퓨팅 대상)는 모델을 학습 및 배포하는 데 사용합니다. 가상 네트워크 컴퓨팅 대상을 만들 수 있습니다. 예를 들어 Azure Virtual Machine Learning 계산 인스턴스를 사용 하 여 모델을 학습 한 다음 AKS (Azure Kubernetes Service)에 모델을 배포할 수 있습니다. Azure 가상 네트워크 내에서 Azure Machine Learning 교육 및 유추 작업을 격리 하 여 machine learning 수명 주기를 보호할 수 있습니다.

Azure 방화벽은 Azure Machine Learning 작업 영역 및 공용 인터넷에 대 한 액세스를 제어 하는 데 사용할 수 있습니다.

- [격리 된 가상 네트워크에서 안전 하 게 실험 및 유추를 실행 하는 방법](how-to-enable-virtual-network.md)

- [Azure Machine Learning 용 Azure 방화벽 뒤에 작업 영역 사용](how-to-access-azureml-behind-firewall.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: 가상 네트워크, 서브넷 및 Nic의 구성과 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Machine Learning는 계산 리소스에 대 한 다른 Azure 서비스에 의존 합니다. Machine Learning 배포로 생성 되는 네트워크에 네트워크 보안 그룹을 할당 합니다. 

네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 감사를 위해 로그를 Azure Storage 계정으로 보냅니다. 또한 흐름 로그를 Log Analytics 작업 영역으로 보낸 다음 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 패턴에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고, 핫 스폿 및 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

- [네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석 사용 및 사용 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: Azure Machine Learning에 의해 배포 된 웹 서비스와의 통신을 보호 하기 위해 HTTPS를 사용 하도록 설정할 수 있습니다. 웹 서비스는 AKS (Azure Kubernetes Services) 또는 ACI (Azure Container Instances)에 배포 되 고 클라이언트에서 전송 하는 데이터를 보호 합니다. 또한 AKS에서 개인 IP를 사용 하 여 점수를 제한할 수 있습니다. 따라서 가상 네트워크 뒤의 클라이언트만 웹 서비스에 액세스할 수 있습니다.

- [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)

- [&amp;개인 가상 네트워크를 사용한 학습 유추 중 네트워크 격리](how-to-enable-virtual-network.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS (배포 된 서비스 거부) 공격 으로부터 보호 하기 위해 Machine Learning 인스턴스와 연결 된 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 검색을 사용 하 여 알려진 악성 또는 사용 하지 않는 인터넷 IP 주소와의 통신을 검색 합니다.

위협 인텔리전스 기반 필터링을 사용 하도록 설정 하 고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성 된 각 조직의 네트워크 경계에서 Azure 방화벽을 배포 합니다.

- [DDoS 보호를 구성 하는 방법](../virtual-network/manage-ddos-protection.md)

- [Azure Machine Learning 용 Azure 방화벽 뒤에 작업 영역 사용](how-to-access-azureml-behind-firewall.md)

- [Azure Security Center 위협 검색에 대 한 자세한 내용은](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Azure Machine Learning 서비스에 적절 한 확장이 설치 된 vm의 경우 Network Watcher 패킷 캡처를 사용 하 여 비정상적인 활동을 조사할 수 있습니다. 

- [Network Watcher 인스턴스를 만드는 방법](../network-watcher/network-watcher-create.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 악의적인 트래픽을 검색 하 고 차단 하기 위해 각 조직의 네트워크 경계에서 원하는 방화벽 솔루션을 배포 합니다.

페이로드 검사 기능을 사용 하 여 IDS/IPS 기능을 지 원하는 Azure Marketplace의 제안을 선택 합니다.  페이로드 검사가 필요 하지 않은 경우 Azure 방화벽 위협 인텔리전스를 사용할 수 있습니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과의 트래픽을 경고 및/또는 차단 하는 데 사용 됩니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

- [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure 방화벽을 사용 하 여 경고를 구성 하는 방법](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Machine Learning 계정에 액세스 해야 하는 리소스의 경우 Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: AzureMachineLearning)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

Azure Machine Learning 서비스는 복잡성을 최소화 하는 데 도움이 되는 가상 네트워크 내의 계산 대상에 대 한 서비스 태그 목록을 네트워크 관리에서 지침으로 사용할 수 있습니다.

- [서비스 태그를 사용 하는 방법에 대 한 자세한 내용](../virtual-network/service-tags-overview.md)

- [Azure Machine Learning 가상 네트워크](how-to-enable-virtual-network.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Machine Learning 네임 스페이스와 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "MachineLearning" 및 "" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Machine Learning 네임 스페이스의 네트워크 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: 분류에 따라 논리적으로 구성 하기 위해 Azure Machine Learning 배포와 연결 된 네트워크 리소스에 대 한 태그를 사용 합니다.

설명 필드를 지 원하는 Azure Machine Learning 가상 네트워크의 리소스에 대해이를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 규칙을 문서화 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Machine Learning와 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Microsoft는 로그의 타임 스탬프에 대 한 Azure Machine Learning와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Monitor을 통해 로그를 수집 하 여 Azure Machine Learning에서 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다.

- [Azure Machine Learning에 대 한 진단 로그를 구성 하는 방법](monitor-azure-machine-learning.md#configuration)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 감사, 보안 및 진단 로그에 액세스 하기 위해 Azure 리소스에 대 한 진단 설정을 사용 하도록 설정 합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

또한 보안 및 규정 준수를 위해 Machine Learning 서비스 작업 로그의 상관 관계를 지정할 수 있습니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../azure-monitor/platform/diagnostic-settings.md)

- [Azure의 로깅 및 다른 로그 유형 이해](../azure-monitor/platform/platform-logs-overview.md)

- [Azure Machine Learning에서 로깅 사용](/azure/machine-learning/how-to-enable-logging)

- [모니터링 Azure Machine Learning](monitor-azure-machine-learning.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 수집 및 모니터링을 담당 합니다. 

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 모든 계산 리소스에 대해 Azure Security Center를 사용 하 여 운영 체제를 모니터링 합니다. 

- [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](../azure-monitor/learn/quick-collect-azurevm.md)

- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Machine Learning 인스턴스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

- [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 Azure Machine Learning 결과를 정기적으로 검토 합니다. Azure Monitor 및 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행할 수 있습니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Log Analytics 작업 영역에서 Azure Machine Learning에 대 한 쿼리를 수행 하는 방법](monitor-azure-machine-learning.md#analyzing-log-data)

- [Azure Machine Learning에서 로깅 사용](/azure/machine-learning/how-to-enable-logging)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Log Analytics 쿼리 시작](../azure-monitor/log-query/get-started-portal.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure Monitor에서는 활동 로그 내에서 Azure Machine Learning 관련 된 로그를 구성 하 고, 장기 보관 저장소에 대 한 저장소 계정으로 쿼리 또는 저장소 계정에 로그를 전송 하 Log Analytics는 진단 설정을 Machine Learning 합니다. Log Analytics 작업 영역을 사용 하 여 보안 로그 및 이벤트에 있는 비정상적인 활동에 대 한 경고를 만들 수 있습니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다.

- [Azure Machine Learning 경고에 대 한 자세한 내용은](monitor-azure-machine-learning.md#alerts)

- [Log Analytics 작업 영역 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스에 대 한 맬웨어 방지 배포를 담당 합니다. 

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure Cloud Services 및 Virtual Machines 용 Microsoft 맬웨어 방지 프로그램 이벤트 수집을 사용 하도록 설정 합니다.

- [가상 컴퓨터에 대 한 Microsoft 맬웨어 방지를 구성 하는 방법](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Cloud services에 대 한 Microsoft 맬웨어 방지 확장을 구성 하는 방법](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [Microsoft 맬웨어 방지 프로그램 이해](../security/fundamentals/antimalware.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당 사항 없음 Azure Machine Learning는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 계산 리소스를 소유 하는 경우 Azure Security Center를 사용 하 여 Azure 가상 머신에 대 한 보안 이벤트 로그 모니터링을 사용 하도록 설정 합니다. Azure Security Center은 지원 되는 모든 Azure Vm에 대 한 Log Analytics 에이전트와 자동 프로비저닝을 사용 하도록 설정 된 경우 생성 되는 새 Vm을 프로 비전 합니다. 또는 에이전트를 수동으로 설치할 수 있습니다. 에이전트를 사용 하면 프로세스 만들기 이벤트 4688 및 이벤트 4688 내의 명령줄 필드를 사용할 수 있습니다. VM에서 생성 되는 새 프로세스는 이벤트 로그에 기록 되 고 Security Center의 검색 서비스에 의해 모니터링 됩니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [Azure 보안 벤치 마크: id 및 액세스 제어](/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Portal에서 리소스에 대 한 Id 및 액세스 관리 탭을 사용 하 여 RBAC (역할 기반 액세스 제어)를 구성 하 고 Azure Machine Learning 리소스에 대 한 인벤토리를 유지 관리할 수 있습니다. 역할은 Active Directory의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다. 개인 및 그룹에 대 한 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다.

Azure Machine Learning은 Azure Machine Learning의 일반적인 관리 시나리오에 대 한 기본 제공 RBAC를 제공 합니다. Azure Active Directory (Azure AD)에 프로필이 있는 개인은 이러한 RBAC 역할을 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 할당 하 여 리소스에 대 한 액세스를 부여 하거나 거부 하 Azure Machine Learning 리소스에 대 한 작업을 수행할 수 있습니다.

또한 Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색할 수 있습니다.

- [Azure Machine Learning의 역할 기반 액세스 제어 이해](how-to-assign-roles.md)

- [PowerShell을 사용 하 여 Azure Active Directory에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Machine Learning 리소스에 대 한 액세스 관리는 Azure Active Directory (Azure AD)를 통해 제어 됩니다. Azure AD에는 기본 암호 개념이 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 새 작업 영역을 만들 때 3 개의 기본 역할과 함께 제공 되며, 소유자 계정 사용과 관련 하 여 표준 운영 절차를 만들 Azure Machine Learning.

Azure AD Privileged Identity Management 및 Azure Resource Manager를 사용 하 여 관리 계정에 just-in-time 액세스를 사용 하도록 설정할 수도 있습니다. 

- [기본 역할 Machine Learning 대 한 자세한 내용은](how-to-assign-roles.md#default-roles)

- [Privileged Identity Management에 대 한 자세한 정보](/azure/active-directory/privileged-identity-management/)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 SSO(Single Sign-On) 사용

**지침**: Machine Learning는 Azure Active Directory와 통합 되며, 서비스별 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center id 및 액세스 권장 사항을 사용 합니다.  

- [Azure AD를 사용 하 여 SSO 이해](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory Multi-Factor Authentication를 사용 하도록 설정 하 고 Azure Security Center id 및 액세스 권장 사항에 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: 높은 권한이 필요한 관리 작업에 대해 Azure에서 관리 하는 안전한 워크스테이션 (권한 있는 액세스 워크스테이션 또는 PAW 라고도 함)을 사용 합니다.

- [안전 하 고 Azure로 관리 되는 워크스테이션 이해](../active-directory/devices/concept-azure-managed-workstation.md)

- [Azure AD MFA를 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Active Directory 보안 보고서 및 모니터링을 사용 하 여 환경에서 의심 스러운 활동이 나 안전 하지 않은 활동이 발생 하는 시기를 검색 합니다. Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: Azure AD 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스할 수 있도록 합니다.
 
 
 
- [Azure AD 명명 된 위치를 구성 하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory (Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.
 
Azure에서 역할 액세스의 범위를 여러 수준으로 지정할 수 있습니다. Machine Learning 작업 영역 수준에서 역할을 관리할 수 있습니다. 예를 들어 작업 영역에 대 한 소유자 액세스 권한이 있는 경우 작업 영역을 포함 하는 리소스 그룹에 대 한 소유자 액세스 권한이 없을 수 있습니다. 이를 통해 동일한 리소스 그룹 내에서 역할을 분리 하는 보다 세부적인 액세스 제어를 제공 합니다. 

- [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md) 
 
- [Azure AD 인스턴스를 만들고 구성 하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD는 부실 계정을 검색하는 데 유용한 로그를 제공합니다. 또한 Azure AD id 및 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리 합니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 
 
환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 합니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring)

- [Azure AD id 및 액세스 검토를 사용 하는 방법](../active-directory/governance/access-reviews-overview.md)

- [Azure AD PIM(Privileged Identity Management) 배포](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: siem/모니터링 도구와 통합할 수 있도록 하는 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.
 
 
- [Azure Monitor와 Azure 활동 로그를 통합 하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD ID 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.
 
- [Azure AD 위험한 로그인을 확인하는 방법](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)
 
- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 해당 사항 없음 Azure Machine Learning 서비스는 고객 lockbox를 지원 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 보호](/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.
 
- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 유형 및 데이터 민감도 수준과 같은 개별 보안 도메인에 대해 별도의 구독 및 관리 그룹을 사용 하 여 격리를 구현 합니다. 응용 프로그램 및 엔터프라이즈 환경에서 요구 하는 Azure 리소스에 대 한 액세스 수준을 제한할 수 있습니다. Azure RBAC를 통해 Azure 리소스에 대 한 액세스를 제어할 수 있습니다.
 
- [추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)

- [관리 그룹을 만드는 방법](../governance/management-groups/create.md)
 
- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: network 경계에서 Azure Marketplace의 타사 솔루션을 사용 하 여 중요 한 정보를 무단으로 전송 하는 행위를 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송을 차단 합니다. 

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다. 

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Azure Machine Learning을 통해 배포 된 웹 서비스는 전송 중인 데이터 암호화를 적용 하는 TLS 버전 1.2만 지원 합니다.

- [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Machine Learning 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현 합니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 관리

**지침**: Azure Machine Learning에서는 Azure Active Directory (Azure AD)를 사용 하 여 Machine Learning 리소스에 대 한 요청에 권한을 부여할 수 있습니다. Azure AD를 사용 하면 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다.

- [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

Microsoft는 Machine Learning에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Machine Learning은 Azure Machine Learning 작업 영역 및 구독과 연결 된 Azure Blob storage 계정에 스냅숏, 출력 및 로그를 저장 합니다. Azure Blob Storage에 저장된 모든 데이터는 Microsoft 관리형 키를 사용하여 미사용 상태에서 암호화됩니다. Machine Learning 서비스에서 사용자 고유의 키를 사용 하 여 Azure Blob 저장소에 저장 된 데이터를 암호화할 수도 있습니다. 

- [휴지 상태의 데이터 암호화 Azure Machine Learning](concept-enterprise-security.md#encryption-at-rest)

- [Azure에서 미사용 암호화 이해](../security/fundamentals/encryption-atrest.md)

- [고객 관리 암호화 키를 구성 하는 방법](../storage/common/storage-encryption-keys-portal.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 프로덕션 인스턴스 Azure Machine Learning 및 기타 중요 한 리소스 또는 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](/azure/security/benchmarks/security-control-vulnerability-management)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스의 취약성 관리를 담당 합니다. 

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure virtual machines, 컨테이너 이미지 및 SQL server에서 취약성 평가를 수행 하기 위한 Azure Security Center의 권장 사항을 따릅니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스의 패치 관리를 담당 합니다. 

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 모든 계산 리소스의 경우 Azure Automation 업데이트 관리를 사용 하 여 최신 보안 업데이트가 Windows 및 Linux Vm에 설치 되어 있는지 확인 합니다. Windows Vm의 경우 Windows 업데이트를 사용 하도록 설정 하 고 자동으로 업데이트 하도록 설정 되어 있는지 확인 합니다.

- [Azure에서 가상 머신에 대 한 업데이트 관리를 구성 하는 방법](/azure/automation/automation-update-management)

- [Security Center에서 모니터링 하는 Azure 보안 정책 이해](../security-center/security-center-policy-definitions.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 타사 패치 관리 솔루션을 사용 합니다. 환경에서 이미 Configuration Manager를 사용 하는 고객은 System Center Updates Publisher를 사용 하 여 사용자 지정 업데이트를 Windows Server Update Service에 게시할 수 있습니다. 이를 통해 Configuration Manager를 사용 하는 컴퓨터를 타사 소프트웨어를 사용 하는 업데이트 리포지토리로 패치 업데이트 관리 수 있습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure virtual machines, 컨테이너 이미지 및 SQL server에서 취약성 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다. 검색 결과는 일정 한 간격으로 내보내고 결과와 이전 검색을 비교 하 여 취약점이 재구성 되었는지 확인 합니다. Azure Security Center에서 제안 하는 취약성 관리 권장 사항을 사용 하는 경우 선택한 솔루션의 포털로 피벗 하 여 기록 검색 데이터를 볼 수 있습니다.

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 해당 없음. 이 지침은 컴퓨팅 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독에서 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다.  테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독에 있는 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.

클래식 Azure 리소스는 Azure 리소스 그래프 탐색기를 통해 검색할 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

- [Azure 리소스 그래프 탐색기를 사용 하 여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure 리소스에 태그를 적용 하 고, 분류에 따라 논리적으로 구성 하는 메타 데이터를 추가 합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 해당 하는 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.
 
 
 
- [ 추가 Azure 구독을 만드는 방법](/azure/billing/billing-create-subscription)
 
 
 
- [ 관리 그룹을 만드는 방법](../governance/management-groups/create.md)
 
 
 
- [ 태그를 만들고 사용 하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 Azure 리소스 및 계산 리소스에 대 한 승인 된 소프트웨어의 인벤토리를 만듭니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

* 허용되지 않는 리소스 종류
* 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure Automation 변경 내용 추적 및 인벤토리를 사용 하 여 Windows 및 Linux Vm에서 인벤토리 정보 수집을 자동화 합니다. Azure Portal에서 소프트웨어 이름, 버전, 게시자 및 새로 고침 시간을 사용할 수 있습니다. 소프트웨어 설치 날짜 및 기타 정보를 가져오려면 게스트 수준 진단을 사용 하도록 설정 하 고 Windows 이벤트 로그를 Log Analytics 작업 영역으로 보냅니다.

- [VM에 대 한 Azure Automation 인벤토리 수집을 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure Security Center의 FIM (파일 무결성 모니터링)을 사용 하 여 Vm에 설치 된 모든 소프트웨어를 식별 합니다. 대신 또는 FIM과 함께 사용할 수 있는 또 다른 옵션은 Linux 및 Windows Vm에서 인벤토리를 수집 하는 Azure Automation 변경 내용 추적 및 인벤토리입니다. 

권한이 없는 소프트웨어를 제거 하는 사용자 고유의 프로세스를 구현할 수 있습니다. 타사 솔루션을 사용 하 여 승인 되지 않은 소프트웨어를 식별할 수도 있습니다.

더 이상 필요 하지 않은 경우 Azure 리소스를 제거 합니다.

- [파일 무결성 모니터링을 사용 하는 방법](../security-center/security-center-file-integrity-monitoring.md#using-file-integrity-monitoring)

- [Azure Automation 변경 내용 추적 및 인벤토리 이해](../automation/change-tracking.md)

- [Azure 가상 컴퓨터 인벤토리를 사용 하도록 설정 하는 방법](../automation/automation-tutorial-installed-software.md)

- [Azure 리소스 그룹 및 리소스 삭제](../azure-resource-manager/management/delete-resource-group.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한 있는 소프트웨어만 실행 되 고 권한이 없는 모든 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단 합니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

* 허용되지 않는 리소스 종류
* 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용 하 여 구독에서 리소스를 쿼리하고 검색 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 모든 계산 리소스에 대해 Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하 여 규칙을 적용할 수 있거나 적용할 수 없는 파일 형식을 지정 합니다.

적응 응용 프로그램 제어가 요구 사항을 충족 하지 않는 경우 타사 솔루션을 구현 합니다.

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: Azure AD 조건부 액세스를 사용 하 여 "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 함으로써 사용자의 Azure 리소스 관리자와 상호 작용 하는 기능을 제한 합니다.
 
- [Azure 리소스 관리자에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: 사용자가 계산 리소스에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 스크립트 유형에 따라 운영 체제별 구성 또는 타사 리소스를 사용 하 여 사용자가 Azure compute 리소스에서 스크립트를 실행 하는 기능을 제한할 수 있습니다.  Azure Security Center 적응 응용 프로그램 제어를 사용 하 여 권한 있는 소프트웨어만 실행 되 고 권한이 없는 모든 소프트웨어가 Azure Virtual Machines에서 실행 되지 않도록 차단할 수 있습니다.

- [Windows 환경에서 PowerShell 스크립트 실행을 제어 하는 방법](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Azure Security Center 적응 응용 프로그램 컨트롤을 사용 하는 방법](../security-center/security-center-adaptive-application.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 애플리케이션을 물리적 또는 논리적으로 분리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용 하 여 Azure Machine Learning 서비스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "MachineLearning" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Machine Learning 서비스의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다.

Azure Resource Manager은 구성이 조직의 보안 요구 사항을 충족 하는지 확인 하기 위해 검토 해야 하는 JavaScript Object Notation (JSON)에서 템플릿을 내보낼 수 있습니다.

Azure Security Center의 권장 사항을 Azure 리소스에 대 한 보안 구성 기준으로 사용할 수도 있습니다.

Azure Machine Learning는 작업 추적을 위한 Git 리포지토리를 완벽 하 게 지원 합니다. 공유 작업 영역 파일 시스템에 직접 리포지토리를 복제 하 고, 로컬 워크스테이션에서 Git를 사용 하 고, 보안 구성이 Machine Learning 환경의 일부로 코드 리소스에 적용 되도록 할 수 있습니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Portal에서 템플릿에 대 한 단일 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스의 운영 체제 보안 구성을 담당 합니다.

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure Security Center 권장 사항을 사용 하 여 모든 계산 리소스에 대 한 보안 구성을 유지 합니다.  또한 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 설정할 수 있습니다.

- [Azure Security Center 권장 사항을 모니터링 하는 방법](../security-center/security-center-recommendations.md)

- [보안 권장 사항 - 참조 가이드](../security-center/recommendations-reference.md)

- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)

- [VHD를 업로드 하 고이를 사용 하 여 Azure에서 새 Windows Vm 만들기](../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLI를 사용하여 사용자 지정 디스크에서 Linux VM 만들기](../virtual-machines/linux/upload-vhd.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다. 또한 Azure Resource Manager 템플릿을 사용 하 여 조직에 필요한 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 
 
 
 
- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)
 
- [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스의 운영 체제 보안 구성을 담당 합니다.

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure compute 리소스에서 취약점 평가를 수행 하는 Azure Security Center의 권장 사항을 따릅니다.  또한 Azure Resource Manager 템플릿, 사용자 지정 운영 체제 이미지 또는 Azure Automation 상태 구성을 사용 하 여 조직에 필요한 운영 체제의 보안 구성을 유지할 수 있습니다.   Azure Automation 상태 구성과 결합 된 Microsoft 가상 머신 템플릿은 보안 요구 사항을 충족 하 고 유지 관리 하는 데 도움이 될 수 있습니다. 

Microsoft에서 게시 하는 Azure Marketplace 가상 컴퓨터 이미지는 Microsoft에서 관리 하 고 유지 관리 합니다. 

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](../security-center/security-center-vulnerability-assessment-recommendations.md)

- [ARM 템플릿에서 Azure Virtual Machine을 만드는 방법](../virtual-machines/windows/ps-template.md)

- [Azure Automation 상태 구성 개요](../automation/automation-dsc-overview.md)

- [Azure Portal에서 Windows 가상 컴퓨터 만들기 ](../virtual-machines/windows/quick-create-portal.md)

- [VM 템플릿을 다운로드 하는 방법에 대 한 정보](../virtual-machines/windows/download-template.md)

- [Azure에 VHD를 업로드하고 새 VM을 만드는 샘플 스크립트](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Machine Learning 또는 관련 리소스에 대 한 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

Azure Machine Learning는 작업 추적을 위한 Git 리포지토리를 완벽 하 게 지원 합니다. 공유 작업 영역 파일 시스템에 직접 리포지토리를 복제 하 고, 로컬 워크스테이션에서 Git를 사용 하 고, 보안 구성이 Machine Learning 환경의 일부로 코드 리소스에 적용 되도록 할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: Azure Machine Learning 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 있는 사용자만 사용자 지정 이미지에 액세스할 수 있도록 합니다. Azure 공유 이미지 갤러리를 사용 하 여 조직 내의 다른 사용자, 서비스 주체 또는 Azure AD 그룹에 이미지를 공유할 수 있습니다. 컨테이너 이미지를 Azure Container Registry에 저장 하 고 RBAC를 사용 하 여 권한 있는 사용자만 액세스할 수 있도록 합니다.

- [Azure의 RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Container Registry에 대 한 RBAC 이해](../container-registry/container-registry-roles.md)

- [Azure에서 RBAC를 구성 하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [공유 이미지 갤러리 개요](../virtual-machines/windows/shared-image-galleries.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: "MachineLearning" 네임 스페이스에 Azure Policy 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [별칭을 사용 하는 방법](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스의 보안 구성 배포를 담당 합니다.

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 클라우드 또는 온-프레미스 데이터 센터의 DSC (Desired State Configuration) 노드에 대 한 Azure Automation 상태 구성을 사용 합니다. 컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다. 

- [Azure Automation 상태 구성을 사용 하도록 설정 하는 방법](../automation/automation-dsc-onboarding.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: Azure Security Center을 사용 하 여 Azure 리소스에 대 한 기준 검색을 수행 합니다. 또한 Azure Policy를 사용 하 여 Azure 리소스 구성을 경고 하 고 감사 합니다.
 
 
 
- [ Azure Security Center에서 권장 사항을 수정 하는 방법](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: microsoft에서 계산 리소스를 소유 하는 경우 microsoft는 Azure Machine Learning 서비스에 대 한 자동화 된 보안 구성 모니터링을 담당 합니다.

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure Security Center 계산 앱을 사용 &amp; 하 고 vm 및 서버 및 컨테이너에 대 한 권장 사항을 따릅니다.

- [Azure Security Center 컨테이너 권장 사항 이해](/azure/security-center/security-center-container-recommendations)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault와 함께 관리 되는 id를 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 합니다.

사용자는 고객이 관리 하는 키를 사용 하 여 데이터 저장소 암호화를 지원 Azure Machine Learning, 조직 보안 및 규정 준수 요구 사항에 따라 키 회전 및 해지를 관리 해야 합니다. 

Azure Key Vault를 사용 하 여 학습 스크립트에서 일반 텍스트 대신 안전 하 게 원격 실행에 암호를 전달 합니다.

- [고객 관리 키 Azure Machine Learning](concept-enterprise-security.md#azure-blob-storage)

- [Azure Machine Learning 교육 실행에서 인증 자격 증명 비밀 사용](how-to-use-secrets-in-runs.md)

- [Azure 리소스에 관리 되는 id를 사용 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault를 만드는 방법](/azure/key-vault/quick-create-portal)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](/azure/key-vault/managed-identity)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Machine Learning 기본 제공 역할 및 사용자 지정 역할을 만드는 기능을 모두 지원 합니다. 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. 관리 id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

 
- [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md)

- [Azure 리소스에 대 한 관리 id를 구성 하는 방법](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](/azure/security/benchmarks/security-control-malware-defense)를 참조 하세요.*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: 중앙에서 관리 되는 맬웨어 방지 소프트웨어 사용

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Machine Learning)를 지 원하는 기본 호스트에서 사용 하도록 설정 되지만 고객 콘텐츠에서 실행 되지 않습니다.

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 계산 리소스의 경우 Azure 용 Microsoft 맬웨어 방지 프로그램을 사용 하 여 리소스를 지속적으로 모니터링 하 고 방어할 수 있습니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다. 또한 데이터 서비스에 대 한 Azure Security Center의 위협 검색을 사용 하 여 저장소 계정에 업로드 된 맬웨어를 검색 합니다.

- [Azure 용 Microsoft 맬웨어 방지를 구성 하는 방법](../security/fundamentals/antimalware.md)

- [위협 방지 및 Azure Security Center](../security-center/threat-protection.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Machine Learning)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

비 계산 Azure 리소스에 업로드 되는 모든 콘텐츠를 미리 검색 하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해 서 고객 콘텐츠의 맬웨어 방지 검색을 수행할 수 없습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: Microsoft 맬웨어 방지 프로그램을 사용 하도록 설정 하 고 azure 서비스 (예: azure Machine learning)를 지 원하는 기본 호스트에 대해 유지 관리 하지만 고객 콘텐츠에서 실행 되지 않습니다.

Azure Machine Learning은 다양 한 계산 리소스 및 사용자 고유의 계산 리소스에 대해 다양 한 지원을 제공 합니다. 조직에서 소유 하는 모든 계산 리소스에 대해 Azure Security Center, 계산 앱의 권장 사항을 따라 &amp; 모든 끝점이 최신 서명으로 최신 상태 인지 확인 합니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용 합니다.

- [Azure 용 Microsoft 맬웨어 방지 프로그램을 배포 하는 방법](../security/fundamentals/antimalware.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](/azure/security/benchmarks/security-control-data-recovery)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Machine Learning 서비스의 데이터 복구 관리는 연결 된 데이터 저장소에 대 한 데이터 관리를 통해 수행 됩니다. 고객 조직 정책에 따라 데이터를 백업 하려면 연결 된 저장소에 대 한 데이터 복구 지침을 따라야 합니다.

- [Azure Virtual Machine 백업에서 파일을 복구 하는 방법](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Machine Learning 서비스의 데이터 백업은 연결 된 데이터 저장소에 대 한 데이터 관리를 통해 수행 됩니다. Vm에 대 한 Azure Backup를 사용 하도록 설정 하 고 원하는 빈도 및 보존 기간을 구성 합니다. Azure Key Vault에서 고객이 관리 하는 키를 백업 합니다.

- [Azure Virtual Machine 백업에서 파일을 복구 하는 방법](../backup/backup-azure-restore-files-from-vm.md)
- [Azure에서 Key Vault 키를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Machine Learning 서비스의 데이터 백업 유효성 검사는 연결 된 데이터 저장소에 대 한 데이터 관리를 통해 수행 됩니다. Azure Backup에서 콘텐츠의 데이터 복원을 정기적으로 수행 합니다. 백업 된 고객 관리 키를 복원할 수 있는지 확인 합니다.

- [Azure 가상 컴퓨터 백업에서 파일을 복구 하는 방법](../backup/backup-azure-restore-files-from-vm.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: 온-프레미스 백업의 경우 Azure에 백업할 때 제공 하는 암호를 사용 하 여 미사용 암호화가 제공 됩니다. 역할 기반 액세스 제어를 사용 하 여 백업과 고객이 관리 하는 키를 보호 합니다. 

Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하 여 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 합니다. 백업을 저장 하는 데 Azure Storage를 사용 하는 경우 blob 또는 blob 스냅숏이 삭제 될 때 일시 삭제를 사용 하 여 데이터를 저장 하 고 복구 합니다.
 
 
- [Azure RBAC 이해](../role-based-access-control/overview.md)

- [Key Vault에서 일시 삭제 및 보호 제거를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Azure Blob 저장소에 대 한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [Azure 보안 벤치 마크: 인시던트 응답](/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 개발 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 해결 방법을 확인 합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 사용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 됩니다.](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 Security Center를 찾고 있는 경우와 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 따라 결정 됩니다.

또한 태그를 사용 하 여 구독을 표시 하 고, 특히 중요 한 데이터를 처리 하는 Azure 리소스를 식별 하 고 분류 하는 명명 시스템을 만듭니다. 사고가 발생 한 Azure 리소스 및 환경의 중요도에 따라 경고 수정의 우선 순위를 지정 하는 것은 사용자의 책임입니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags).

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하 여 Azure 리소스를 보호 하는 연습을 수행 합니다. 약한 지점과 격차를 확인 한 후 필요에 따라 응답 계획을 수정 합니다.

- [NIST의 게시-IT 계획 및 기능에 대 한 테스트, 학습 및 연습 프로그램 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정 하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대 한 위험을 식별 하는 데 도움이 되도록 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: 워크플로 자동화 기능 Azure Security Center 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다.

- [Security Center에서 워크플로 자동화를 구성 하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Engagement의 Microsoft 클라우드 침투 테스트 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
