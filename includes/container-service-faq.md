---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: f903828285b0d4fdc8fbd932fa7c85056e937481
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60510555"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>(사용되지 않음) Container Service 질문과 대답

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

## <a name="orchestrators"></a>오케스트레이터

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Azure Container Service에 어떤 컨테이너 오케스트레이터가 지원되나요? 

오픈 소스 DC/OS, Docker Swarm 및 Kubernetes가 지원됩니다. 자세한 내용은 [개요](../articles/container-service/kubernetes/container-service-intro-kubernetes.md)를 참조하세요.
 
### <a name="do-you-support-docker-swarm-mode"></a>Docker Swarm 모드가 지원되나요? 

현재 Swarm 모드는 지원되지 않지만 서비스 로드맵에 포함되어 있습니다. 

### <a name="does-azure-container-service-support-windows-containers"></a>Azure Container Service에 Windows 컨테이너가 지원되나요?  

현재 Linux 컨테이너는 모든 오케스트레이터에 지원됩니다. Kubernetes를 통한 Windows 컨테이너에 대한 지원은 미리 보기 상태입니다.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Azure Container Service에 권장되는 특정 오케스트레이터가 있나요? 
일반적으로 특정 오케스트레이터가 권장되지는 않습니다. 지원되는 오케스트레이터 중 하나에 경험이 있다면 그러한 경험을 Azure Container Service에 적용할 수 있습니다. 하지만 데이터 경향에 따르면 DC/OS는 빅 데이터 및 IoT 워크로드에 대해 성능이 입증되었고 Kubernetes는 클라우드 네이티브 워크로드에 적합하며 Docker Swarm은 Docker 도구와의 통합 및 학습이 쉬운 것으로 알려져 있습니다.

시나리오에 따라 다른 Azure 서비스를 사용하여 사용자 지정 컨테이너 솔루션을 구축하고 관리할 수도 있습니다. 이러한 서비스에는 [Virtual Machines](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [Web Apps](../articles/app-service/overview.md) 및 [Batch](../articles/batch/batch-technical-overview.md)가 있습니다.  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Azure Container Service와 ACS Engine의 차이는 무엇인가요? 
Azure Container Service는 Azure Portal, Azure 명령줄 도구 및 Azure API의 기능이 포함된 SLA 기반 Azure 서비스입니다. 이 서비스를 사용하면 비교적 적은 수의 구성 옵션을 통해 표준 컨테이너 오케스트레이션 도구를 실행하는 클러스터를 신속하게 구현하고 관리할 수 있습니다. 

[ACS Engine](http://github.com/Azure/acs-engine)은 power user가 모든 수준의 클러스터 구성을 사용자 지정하는 데 사용할 있는 오픈 소스 프로젝트입니다. 인프라 및 소프트웨어 양쪽의 구성을 변경하는 기능이 있기 때문에 ACS Engine용 SLA는 제공되지 않습니다. 지원은 공식적인 Microsoft 채널이 아닌 GitHub의 오픈 소스 프로젝트를 통해 처리됩니다. 

자세한 내용은 [컨테이너에 대한 지원 정책](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers)을 참조하세요.

## <a name="cluster-management"></a>클러스터 관리

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>클러스터의 SSH 키는 어떻게 만드나요?

클러스터의 Linux 가상 머신에 대한 인증을 위해 운영 체제에서 표준 도구를 사용하여 SSH RSA 공용 및 개인 키 쌍을 생성할 수 있습니다. 단계는 [OS X 및 Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../articles/virtual-machines/linux/ssh-from-windows.md) 지침을 참조하세요. 

[Azure CLI 명령](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)을 사용하여 컨테이너 서비스 클러스터를 배포하는 경우 클러스터용 SSH 키가 자동으로 생성될 수 있습니다.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Kubernetes 클러스터에 대한 서비스 주체는 어떻게 만드나요?

Azure Container Service에서 Kubernetes 클러스터를 만들려면 Azure Active Directory 서비스 주체 ID 및 암호도 필요합니다. 자세한 내용은 [Kubernetes 클러스터의 서비스 주체 정보](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md)를 참조하세요.

[Azure CLI 명령](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)을 사용하여 Kubernetes 클러스터를 배포하는 경우 서비스 주체 자격 증명이 클러스터에 대해 자동으로 생성될 수 있습니다.

### <a name="how-large-a-cluster-can-i-create"></a>얼마나 큰 클러스터를 만들 수 있나요?
1, 3 또는 5개의 마스터 노드가 있는 클러스터를 만들 수 있습니다. 최대 100개의 에이전트 노드를 선택할 수 있습니다.

> [!IMPORTANT]
> 대규모 클러스터 및 노드에 선택한 VM 크기에 따라 구독에서 코어 할당량을 늘려야 합니다. 할당량 증가를 요청하려면 추가 비용 없이 [온라인 고객 지원 요청](../articles/azure-supportability/how-to-create-azure-support-request.md)을 개설합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 계산 코어만 사용할 수 있습니다.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>클러스터를 만든 후 마스터 수는 어떻게 늘리나요? 
클러스터를 만들고 나면 마스터 수가 고정되어 변경할 수 없습니다. 클러스터를 만드는 동안 고가용성을 위해 다수의 마스터를 적당히 선택해야 합니다.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>클러스터를 만든 후 에이전트 수는 어떻게 늘리나요? 
클러스터의 에이전트 수는 Azure Portal 또는 명령줄 도구를 사용하여 조정할 수 있습니다. [Azure Container Service 클러스터 규모 조정](../articles/container-service/kubernetes/container-service-scale.md)을 참조하세요.

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>마스터와 에이전트의 URL은 무언인가요? 
Azure Container Service에 있는 클러스터 리소스의 URL은 사용자가 제공한 DNS 이름 접두사 및 배포를 위해 사용자가 선택한 Azure 지역의 이름을 기반으로 합니다. 예를 들어 마스터 노드의 FQDN(정규화된 도메인 이름)은 다음과 같은 형식입니다.

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

클러스터에 일반적으로 사용되는 URL은 Azure Portal, Azure 리소스 탐색기 또는 기타 Azure 도구에서 찾을 수 있습니다.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>내 클러스터에 실행 중인 오케스트레이터 버전을 어떻게 확인하나요?

* DC/OS: [Mesosphere 설명서](https://docs.mesosphere.com/1.7/usage/cli/command-reference/) 참조
* Docker Swarm: `docker version` 실행
* Kubernetes: `kubectl version` 실행

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>배포 후 오케스트레이터를 업그레이드하려면 어떻게 하나요?

현재 Azure Container Service는 클러스터에 배포한 오케스트레이터 버전을 업그레이드하는 도구를 제공하지 않습니다. Container Service에서 새로운 버전을 지원하는 경우 새 클러스터를 배포할 수 있습니다. 또 다른 옵션은 클러스터를 바로 업그레이드할 수 있는 경우 오케스트레이터별 도구를 사용하는 것입니다. 예를 들어 [DC/OS 업그레이드](http://docs.mesosphere.com/1.12/installing/production/upgrading)를 참조하세요.
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>클러스터에 대한 SSH 연결 문자열은 어디에서 찾을 수 있나요?

연결 문자열은 Azure Portal에서 찾거나 Azure 명령줄 도구를 사용하여 찾을 수 있습니다. 

1. 포털에서 클러스터 배포를 위한 리소스 그룹으로 이동합니다.  

2. **개요**를 클릭하고 **필수** 아래에서 **배포**에 대한 링크를 클릭합니다. 

3. **배포 기록** 블레이드에서 이름이 **microsoft-acs**로 시작하고 배포 날짜가 다음에 나오는 배포를 클릭합니다. 예: microsoft-acs-201701310000.  

4. **요약** 페이지의 **출력** 아래에 여러 개의 클러스터 링크가 제공됩니다. **SSHMaster0**은 컨테이너 서비스 클러스터의 첫 번째 마스트에 SSH 연결 문자열을 제공합니다. 

이전에 언급했듯이 Azure 도구를 사용하여 마스터의 FQDN을 찾을 수도 있습니다. 마스터의 FQDN 및 클러스터를 만들 때 지정한 사용자 이름을 사용하여 마스터에 SSH 연결을 만듭니다. 예를 들면 다음과 같습니다.

```bash
ssh userName@masterFQDN –A –p 22 
```

자세한 내용은 [Azure Container Service 클러스터에 연결](../articles/container-service/kubernetes/container-service-connect.md)을 참조하세요.

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>내 DNS 이름 확인이 Windows에서 작동하지 않습니다. 어떻게 해야 하나요?

Windows에서 수정 사항이 여전히 단계적으로 제거되는 알려진 DNS 문제가 있습니다. 환경에서 혜택을 받을 수 있도록 최신 acs-engine 및 Windows 버전([KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) 및 [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848)이 설치됨)을 사용 중인지 확인하십시오. 그렇지 않은 경우, 아래 표에서 완화 단계를 참조하세요.

| DNS 증상 | 해결 방법  |
|-------------|-------------|
|워크로드 컨테이너가 불안정하고 작동이 중단되면 네트워크 네임스페이스가 정리됩니다. | 영향을 받는 모든 서비스를 재배포합니다. |
| 서비스 VIP 액세스가 손상되었습니다. | 일반(권한이 없는) Pod 하나를 항상 실행하도록 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)를 구성합니다. |
|컨테이너가 실행 중인 노드를 사용할 수 없게 되면 DNS 쿼리가 실패하여 "부정 캐시 항목"이 발생할 수 있습니다. | 영향을 받는 컨테이너 내부에서 다음을 실행합니다. <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> 그래도 문제가 해결되지 않으면 DNS 캐싱을 완전히 사용하지 않도록 설정해 봅니다. <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>다음 단계

* Azure Container Service에 대해 [자세히 알아보세요](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
* [포털](../articles/container-service/dcos-swarm/container-service-deployment.md) 또는 [Azure CLI](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)를 사용하여 컨테이너 서비스 클러스터를 배포합니다.
