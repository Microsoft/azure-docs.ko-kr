---
title: Azure Kubernetes Service 소개
description: Azure에서 컨테이너 기반 애플리케이션을 배포 및 관리하는 Azure Kubernetes Service의 기능 및 이점을 알아봅니다.
services: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: f4c1e96a0603caa8e026f1968299fa24b8755a42
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88003208"
---
# <a name="azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)

AKS(Azure Kubernetes Service)를 사용하면 Azure에서 관리되는 Kubernetes 클러스터를 간단하게 배포할 수 있습니다. AKS는 대부분의 부담을 Azure에 오프로딩하여 Kubernetes를 관리하는 복잡성 및 운영 과부하를 감소시킵니다. 호스팅되는 Kubernetes 서비스인 Azure는 상태 모니터링 및 유지 관리 같은 중요 작업을 처리합니다. Kubernetes 마스터는 Azure에서 관리됩니다. 에이전트 노드만 관리하고 유지하면 됩니다. 관리되는 Kubernetes 서비스, AKS가 무료이므로 마스터가 아니라 클러스터 내의 에이전트 노드에 대해서만 지불합니다.

Azure CLI 또는 Resource Manager 템플릿 및 Terraform과 같은 템플릿 기반 배포 옵션을 사용하여 Azure Portal에서 AKS 클러스터를 만들 수 있습니다. AKS 클러스터를 배포할 때 Kubernetes 마스터 및 모든 노드가 배포되고 구성됩니다. 배포 프로세스 중에 고급 네트워킹, Azure Active Directory 통합 및 모니터링 등의 추가 기능을 구성할 수도 있습니다. Windows Server 컨테이너는 AKS에서 지원됩니다.

Kubernetes 기본 사항에 대한 자세한 내용은 [AKS의 Kubernetes 핵심 개념][concepts-clusters-workloads]을 참조하세요.

시작하려면 [Azure Portal][aks-portal]이나 [Azure CLI][aks-cli]를 사용하여 AKS 빠른 시작을 완료합니다.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>액세스, 보안 및 모니터링

향상된 보안 및 관리를 위해 AKS를 사용하면 Azure Active Directory와 통합하고 Kubernetes RBAC(역할 기반 액세스 제어)를 사용할 수 있습니다. 클러스터 및 리소스의 상태를 모니터링할 수도 있습니다.

### <a name="identity-and-security-management"></a>ID 및 보안 관리

클러스터 리소스에 대한 액세스를 제한하기 위해 AKS는 [Kubernetes RBAC(역할 기반 액세스 제어)][kubernetes-rbac]를 지원합니다. RBAC를 사용하면 Kubernetes 리소스 및 네임스페이스에 대한 액세스와 해당 리소스에 대한 권한을 제어할 수 있습니다. Azure AD(Active Directory)와 통합하도록 AKS 클러스터를 구성할 수도 있습니다. Azure AD 통합을 사용하여 Kubernetes 액세스는 기존 ID 및 그룹 멤버 자격에 따라 구성될 수 있습니다. 기존 Azure AD 사용자 및 그룹에 AKS 리소스에 대한 액세스와 통합된 로그인 환경을 제공할 수 있습니다.

ID에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션][concepts-identity]을 참조하세요.

AKS 클러스터를 보호하려면 [AKS와 Azure Active Directory 통합][aks-aad]을 참조하세요.

### <a name="integrated-logging-and-monitoring"></a>통합된 로깅 및 모니터링

AKS 클러스터 및 배포된 애플리케이션이 수행되는 방법을 이해하기 위해 컨테이너 상태에 대한 Azure Monitor는 컨테이너, 노드 및 컨트롤러에서 메모리 및 프로세서 메트릭을 수집합니다. 컨테이너 로그를 사용할 수 있으며, [Kubernetes 마스터 로그를 검토][aks-master-logs]할 수도 있습니다. 이 모니터링 데이터는 Azure Log Analytics 작업 영역에 저장되고, Azure Portal, Azure CLI 또는 REST 엔드포인트를 통해 제공됩니다.

자세한 내용은 [Azure Kubernetes Service 컨테이너 상태 모니터링][container-health]을 참조하세요.

## <a name="clusters-and-nodes"></a>클러스터 및 노드

AKS 노드는 Azure 가상 머신에서 실행됩니다. 스토리지를 노드 및 Pod에 연결하고, 클러스터 구성 요소를 업그레이드하고, GPU를 사용할 수 있습니다. AKS는 혼합된 운영 체제 및 Windows Server 컨테이너를 지원하기 위해 여러 노드 풀을 실행하는 Kubernetes 클러스터를 지원합니다. Linux 노드는 사용자 지정된 Ubuntu OS 이미지를 실행하고 Windows 서버 노드는 사용자 지정된 Windows Server 2019 OS 이미지를 실행합니다.

### <a name="cluster-node-and-pod-scaling"></a>클러스터 노드 및 Pod 크기 조정

리소스 변경에 따라 서비스를 실행하는 클러스터 노드 또는 Pod 수를 자동으로 확대 또는 축소할 수 있습니다. 수평 Pod 자동 크기 조정 또는 클러스터 자동 크기 조정을 모두 사용할 수 있습니다. 크기 조정에 대한 이와 같은 방식을 통해 AKS 클러스터는 요구에 맞게 자동으로 조정되고 필요한 리소스만 실행할 수 있습니다.

자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터의 규모 조정][aks-scale]을 참조하세요.

### <a name="cluster-node-upgrades"></a>클러스터 노드 업그레이드

Azure Kubernetes Service는 여러 Kubernetes 버전을 제공합니다. AKS에서 새 버전을 사용할 수 있으므로 Azure Portal 또는 Azure CLI를 사용하여 클러스터를 업그레이드할 수 있습니다. 업그레이드 프로세스 중에는 노드를 신중하게 통제하고 드레이닝하여 실행 중인 애플리케이션의 중단을 최소화합니다.

수명 주기 버전에 대해 자세히 알아보려면 [AKS의 지원되는 Kubernetes 버전][aks-supported versions]을 참조하세요. 업그레이드하는 방법에 대한 단계는 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

### <a name="gpu-enabled-nodes"></a>GPU 사용 노드

AKS는 GPU 사용 노드 풀 만들기를 지원합니다. Azure는 현재, 단일 또는 여러 GPU 사용 VM을 제공합니다. GPU 사용 VM은 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다.

자세한 내용은 [AKS에서 GPU 사용][aks-gpu]을 참조하세요.

### <a name="storage-volume-support"></a>스토리지 볼륨 지원

애플리케이션 워크로드를 지원하기 위해 영구 데이터에 대한 스토리지 볼륨을 탑재할 수 있습니다. 정적 및 동적 볼륨을 모두 사용할 수 있습니다. 스토리지를 공유하기 위해 연결된 Pod 수에 따라 단일 Pod 액세스에 대한 Azure 디스크 또는 여러 동시 Pod 액세스에 대한 Azure Files 중 하나에서 지원되는 스토리지를 사용할 수 있습니다.

자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

[Azure Disks][azure-disk] 또는 [Azure Files][azure-files]를 사용하여 동적 영구 볼륨을 시작합니다.

## <a name="virtual-networks-and-ingress"></a>가상 네트워크 및 수신

AKS 클러스터는 기존 가상 네트워크로 배포될 수 있습니다. 이 구성에서 클러스터의 모든 Pod는 가상 네트워크의 IP 주소가 할당되고, 클러스터의 다른 Pod 및 가상 네트워크의 다른 노드와 직접 통신할 수 있습니다. 또한 Pod는 피어링된 가상 네트워크의 다른 서비스에 연결되고, ExpressRoute 또는 S2S(사이트 간) VPN 연결을 통해 온-프레미스 네트워크에 연결될 수 있습니다.

자세한 내용은 [애플리케이션에 대한 AKS의 네트워크 개념][aks-networking]을 참조하세요.

수신 트래픽을 시작하려면 [HTTP 애플리케이션 라우팅][aks-http-routing]을 참조하세요.

### <a name="ingress-with-http-application-routing"></a>HTTP 애플리케이션 라우팅을 사용하여 수신

HTTP 애플리케이션 라우팅 추가 기능을 사용하면 AKS 클러스터에 배포된 애플리케이션에 쉽게 액세스할 수 있습니다. HTTP 애플리케이션 라우팅 솔루션이 설정되면 AKS 클러스터에 수신 컨트롤러를 구성합니다. 애플리케이션이 배포되면 공개적으로 액세스할 수 있는 DNS 이름이 자동으로 구성됩니다. HTTP 애플리케이션 라우팅은 DNS 영역을 구성하고 AKS 클러스터와 통합합니다. 그런 다음, 정상적으로 Kubernetes 수신 리소스를 배포할 수 있습니다.

수신 트래픽을 시작하려면 [HTTP 애플리케이션 라우팅][aks-http-routing]을 참조하세요.

## <a name="development-tooling-integration"></a>개발 도구 통합

Kubernetes에는 다양한 Visual Studio Code용 개발 및 관리 도구(예: Helm 및 Kubernetes 확장)가 있습니다. 이러한 도구는 AKS를 사용하여 원활하게 작동합니다.

또한 Azure Dev Spaces는 팀에게 신속하고 반복적인 Kubernetes 개발 환경을 제공합니다. 최소한의 구성을 통해 AKS에서 바로 컨테이너를 실행하고 디버그할 수 있습니다. 시작하려면 [Azure Dev Spaces][azure-dev-spaces]를 참조하세요.

Azure DevOps 프로젝트는 기존 코드 및 Git 리포지토리를 Azure로 가져오는 간단한 솔루션을 제공합니다. DevOps 프로젝트는 자동으로 AKS, CI의 빌드 파이프라인을 포함하는 Azure DevOps Services의 릴리스 파이프라인과 같은 Azure 리소스를 만들고, CD의 릴리스 파이프라인을 설정한 다음, 모니터링을 위해 Azure Application Insights 리소스를 만듭니다.

자세한 내용은 [Azure DevOps 프로젝트][azure-devops]를 참조하세요.

## <a name="docker-image-support-and-private-container-registry"></a>Docker 이미지 지원 및 프라이빗 컨테이너 레지스트리

AKS는 Docker 이미지 형식을 지원합니다. Docker 이미지의 프라이빗 스토리지의 경우 AKS를 ACR(Azure Container Registry)과 통합할 수 있습니다.

프라이빗 이미지 저장소를 만들려면 [Azure Container Registry][acr-docs]를 참조하세요.

## <a name="kubernetes-certification"></a>Kubernetes 인증

AKS(Azure Kubernetes Service)는 Kubernetes 규칙을 따르는 CNCF로 인증되었습니다.

## <a name="regulatory-compliance"></a>규정 준수

AKS(Azure Kubernetes Service)는 SOC, ISO, PCI DSS 및 HIPAA와 호환됩니다. 자세한 내용은 [Microsoft Azure 규정 준수 개요][compliance-doc]를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure CLI 빠른 시작으로 AKS 배포 및 관리에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [AKS 빠른 시작][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/insights/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
