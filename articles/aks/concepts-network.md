---
title: 개념 - AKS(Azure Kubernetes Service)의 네트워킹
description: kubenet 및 Azure CNI 네트워킹, 수신 컨트롤러, 부하 분산 장치 및 고정 IP 주소를 포함하여 AKS(Azure Kubernetes Service)의 네트워킹에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: edb195fae2e05a1f746c10482576f7e0b1bff7c9
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243907"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>애플리케이션에 대한 AKS(Azure Kubernetes Service)의 네트워크 개념

애플리케이션 개발에 대한 컨테이너 기반 마이크로 서비스 접근 방식에서 애플리케이션 구성 요소는 함께 작동하여 해당 작업을 처리해야 합니다. Kubernetes는 이 애플리케이션 통신을 사용할 수 있게 하는 다양한 리소스를 제공합니다. 애플리케이션을 내부 또는 외부에 연결하고 공개할 수 있습니다. 고가용성 애플리케이션을 빌드하기 위해 애플리케이션의 부하를 분산시킬 수 있습니다. 더 복잡한 애플리케이션에는 SSL/TLS 종료 또는 여러 구성 요소의 라우팅에 대한 수신 트래픽 구성이 필요할 수 있습니다. 보안상의 이유로 Pod, 노드 또는 서로 간의 네트워크 트래픽 흐름을 제한해야 할 수도 있습니다.

이 문서에서는 AKS에서 네트워킹을 애플리케이션에 제공하는 핵심 개념을 소개합니다.

- [Services](#services)
- [Azure 가상 네트워크](#azure-virtual-networks)
- [수신 컨트롤러](#ingress-controllers)
- [네트워크 정책](#network-policies)

## <a name="kubernetes-basics"></a>Kubernetes 기본 사항

애플리케이션에 액세스하거나 애플리케이션 구성 요소가 서로 통신할 수 있도록 Kubernetes에서 추상화 계층을 가상 네트워킹에 제공합니다. Kubernetes 노드는 가상 네트워크에 연결되고, Pod에 대한 인바운드 및 아웃바운드 연결을 제공할 수 있습니다. *kube-proxy* 구성 요소가 각 노드에서 실행되어 이러한 네트워크 기능을 제공합니다.

Kubernetes에서 *Services*는 IP 주소 또는 DNS 이름을 통해 특정 포트에서 직접 액세스할 수 있도록 Pod를 논리적으로 그룹화합니다. 트래픽은 *부하 분산 장치*를 사용하여 분산시킬 수도 있습니다. 더 복잡한 애플리케이션 트래픽 라우팅은 *수신 컨트롤러*를 사용하여 수행할 수도 있습니다. Pod에 대한 네트워크 트래픽의 보안 및 필터링은 Kubernetes *네트워크 정책*을 사용하여 수행할 수 있습니다.

또한 Azure 플랫폼은 AKS 클러스터에 대한 가상 네트워킹을 간소화하는 데에도 도움이 됩니다. Kubernetes 부하 분산 장치를 만들면 기본 Azure 부하 분산 장치 리소스가 만들어지고 구성됩니다. Pod에서 네트워크 포트를 열면 해당 Azure 네트워크 보안 그룹 규칙이 구성됩니다. HTTP 애플리케이션 라우팅의 경우 새 수신 경로가 구성될 때 Azure에서 *외부 DNS*를 구성할 수도 있습니다.

## <a name="services"></a>서비스

애플리케이션 워크로드에 대한 네트워크 구성을 간소화하기 위해 Kubernetes는 *Service*를 사용하여 일단의 Pod를 논리적으로 그룹화하고 네트워크 연결을 제공합니다. 사용할 수 있는 Service 유형은 다음과 같습니다.

- **클러스터 IP** - AKS 클러스터 내에서 사용할 내부 IP 주소를 만듭니다. 클러스터 내의 다른 워크로드를 지원하는 내부 전용 애플리케이션에 적합합니다.

    ![AKS 클러스터의 클러스터 IP 트래픽 흐름을 보여 주는 다이어그램][aks-clusterip]

- **NodePort** - 노드 IP 주소와 포트를 사용하여 애플리케이션에 직접 액세스할 수 있도록 포트 매핑을 기본 노드에 만듭니다.

    ![AKS 클러스터의 NodePort 트래픽 흐름을 보여 주는 다이어그램][aks-nodeport]

- **LoadBalancer** - Azure 부하 분산 장치 리소스를 만들고, 외부 IP 주소를 구성하고, 요청된 Pod를 부하 분산 장치 백 엔드 풀에 연결합니다. 고객의 트래픽이 응용 프로그램에 도달 하도록 허용 하기 위해 원하는 포트에서 부하 분산 규칙이 생성 됩니다. 

    ![AKS 클러스터의 Load Balancer 트래픽 흐름을 보여 주는 다이어그램][aks-loadbalancer]

    인바운드 트래픽을 추가로 제어하고 라우팅하려면 [수신 컨트롤러](#ingress-controllers)를 대신 사용할 수 있습니다.

- **ExternalName** - 애플리케이션에 쉽게 액세스하기 위한 특정 DNS 항목을 만듭니다.

부하 분산 장치 및 서비스에 대한 IP 주소는 동적으로 할당하거나 사용할 기존 고정 IP 주소를 지정할 수 있습니다. 내부 및 외부 고정 IP 주소를 모두 할당할 수 있습니다. 이 기존 고정 IP 주소는 종종 DNS 항목에 연결됩니다.

*내부* 및 *외부* 부하 분산 장치를 모두 만들 수 있습니다. 내부 부하 분산 장치는 개인 IP 주소만 할당 하므로 인터넷에서 액세스할 수 없습니다.

## <a name="azure-virtual-networks"></a>Azure 가상 네트워크

AKS에서는 다음 두 가지 네트워크 모델 중 하나를 사용하는 클러스터를 배포할 수 있습니다.

- *Kubenet* 네트워킹 - 네트워크 리소스는 일반적으로 AKS 클러스터가 배포될 때 만들어지고 구성됩니다.
- *Azure CNI(컨테이너 네트워킹 인터페이스)* 네트워킹 - AKS 클러스터가 기존 가상 네트워크 리소스 및 구성에 연결됩니다.

### <a name="kubenet-basic-networking"></a>Kubenet(기본) 네트워킹

*kubenet* 네트워킹 옵션은 AKS 클러스터 만들기에 대한 기본 구성입니다. *kubenet*을 사용하면 노드는 Azure Virtual Network 서브넷의 IP 주소를 얻습니다. Pod는 논리적으로 다른 주소 공간에서 노드의 Azure 가상 네트워크 서브넷으로 IP 주소를 수신합니다. 그러면 Pod가 Azure 가상 네트워크의 리소스에 연결할 수 있도록 NAT(Network Address Translation)가 구성됩니다. 트래픽의 원본 IP 주소는 노드의 기본 IP 주소로 NAT됩니다.

노드는 [kubenet][kubenet] Kubernetes 플러그인을 사용합니다. Azure 플랫폼에서 가상 네트워크를 만들고 구성하거나 기존 가상 네트워크 서브넷에 AKS 클러스터를 배포하도록 선택할 수 있습니다. 다시 말해, 노드만 라우팅할 수 있는 IP 주소를 받으며 pod는 NAT를 사용 하 여 AKS 클러스터 외부의 다른 리소스와 통신 합니다. 이 방법을 사용하면 네트워크 공간에서 Pod가 사용하도록 예약해야 하는 IP 주소의 수가 크게 줄어듭니다.

자세한 내용은 [AKS 클러스터에 대한 kubenet 네트워킹 구성][aks-configure-kubenet-networking]을 참조하세요.

### <a name="azure-cni-advanced-networking"></a>Azure CNI(고급) 네트워킹

Azure CNI를 사용하면 모든 Pod가 서브넷에서 IP 주소를 가져오고 직접 액세스가 가능합니다. 이러한 IP 주소는 네트워크 공간에서 고유해야 하며 미리 계획되어야 합니다. 각 노드에는 지원하는 최대 Pod 수에 대한 구성 매개 변수가 있습니다. 그러면 노드당 동일한 IP 주소 수가 해당 노드에 대해 미리 예약됩니다. 이 접근 방식에는 추가 계획이 필요 합니다. 그렇지 않으면 IP 주소 고갈 또는 응용 프로그램 요구가 증가 함에 따라 더 큰 서브넷에서 클러스터를 다시 작성 해야 합니다.

Kubenet와 달리 동일한 가상 네트워크의 끝점에 대 한 트래픽은 노드의 기본 IP에 대 한 NAT가 아닙니다. 가상 네트워크 내의 트래픽에 대 한 원본 주소는 pod IP입니다. 가상 네트워크 외부의 트래픽은 여전히 노드의 기본 IP로 Nat 합니다.

노드는 [Azure CNI(컨테이너 네트워킹 인터페이스)][cni-networking] Kubernetes 플러그인을 사용합니다.

![각 노드를 단일 Azure VNet에 연결하는 브리지가 있는 두 노드를 보여주는 다이어그램][advanced-networking-diagram]

자세한 내용은 [AKS 클러스터에 대한 Azure CNI 구성][aks-configure-advanced-networking]을 참조하세요.

### <a name="compare-network-models"></a>네트워크 모델 비교

Kubenet와 Azure CNI 모두 AKS 클러스터에 대 한 네트워크 연결을 제공 합니다. 그러나 각각에는 장점과 단점이 있습니다. 개략적인 수준에서 다음과 같은 고려 사항이 적용 됩니다.

* **kubenet**
    * IP 주소 공간을 절약 합니다.
    * Kubernetes 내부 또는 외부 부하 분산 장치를 사용 하 여 클러스터 외부에서 pod에 연결 합니다.
    * 수동으로 UDRs (사용자 정의 경로)를 관리 하 고 유지 관리 해야 합니다.
    * 클러스터 당 최대 400 노드 수
* **Azure CNI**
    * Pod는 전체 가상 네트워크 연결을 가져오고 연결 된 네트워크에서 개인 IP 주소를 통해 직접 연결할 수 있습니다.
    * 에 더 많은 IP 주소 공간이 필요 합니다.

Kubenet와 Azure CNI 간에는 다음과 같은 동작 차이가 있습니다.

| 기능                                                                                   | Kubenet   | Azure CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| 기존 또는 새 가상 네트워크에 클러스터 배포                                            | 지원 됨-UDRs 수동 적용 됨 | 지원됨 |
| Pod-pod 연결                                                                         | 지원됨 | 지원됨 |
| Pod-VM 연결 동일한 가상 네트워크의 VM                                          | Pod에 의해 시작 된 경우 작동 합니다. | 두 가지 방식으로 작동 합니다. |
| Pod-VM 연결 피어 링 가상 네트워크의 VM                                            | Pod에 의해 시작 된 경우 작동 합니다. | 두 가지 방식으로 작동 합니다. |
| VPN 또는 Express 경로를 사용 하 여 온-프레미스 액세스                                                | Pod에 의해 시작 된 경우 작동 합니다. | 두 가지 방식으로 작동 합니다. |
| 서비스 끝점으로 보호 되는 리소스에 대 한 액세스                                             | 지원됨 | 지원됨 |
| 부하 분산 장치 서비스, 앱 게이트웨이 또는 수신 컨트롤러를 사용 하 여 Kubernetes services 노출 | 지원됨 | 지원됨 |
| 기본 Azure DNS 및 개인 영역                                                          | 지원됨 | 지원됨 |

Kubenet 및 Azure CNI 플러그 인 DNS를 모두 사용 하는 DNS와 관련 하 여 AKS에서 실행 되는 배포 인 CoreDNS는 자체 autoscaler를 통해 제공 됩니다. CoreDNS on Kubernetes에 대 한 자세한 내용은 [DNS 서비스 사용자 지정](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)을 참조 하세요. CoreDNS는 AKS 클러스터가 배포 되는 Azure Virtual Network의 DNS 기능에 대 한 알 수 없는 도메인을 노드 DNS 서버로 전달 하기 위해 기본적으로 구성 됩니다. 따라서 Azure DNS 및 개인 영역은 AKS에서 실행 되는 pod에 대해 작동 합니다.

### <a name="support-scope-between-network-models"></a>네트워크 모델 간의 범위 지원

사용 하는 네트워크 모델에 관계 없이 kubenet와 Azure CNI는 다음 중 한 가지 방법으로 배포할 수 있습니다.

* Azure 플랫폼은 AKS 클러스터를 만들 때 가상 네트워크 리소스를 자동으로 만들고 구성할 수 있습니다.
* AKS 클러스터를 만들 때 가상 네트워크 리소스를 수동으로 만들고 구성 하 고 해당 리소스에 연결할 수 있습니다.

서비스 엔드포인트 나 UDRs와 같은 기능이 kubenet 및 Azure CNI 모두에서 지원 되기는 하지만 [AKS에 대 한 지원 정책은][support-policies] 어떤 변경 작업을 수행할 수 있는지를 정의 합니다. 예:

* AKS 클러스터에 대 한 가상 네트워크 리소스를 수동으로 만드는 경우 고유한 UDRs 또는 서비스 끝점을 구성할 때 지원 됩니다.
* Azure 플랫폼에서 AKS 클러스터에 대 한 가상 네트워크 리소스를 자동으로 만드는 경우 해당 AKS 관리 리소스를 수동으로 변경 하 여 사용자 고유의 UDRs 또는 서비스 끝점을 구성할 수 없습니다.

## <a name="ingress-controllers"></a>수신 컨트롤러

LoadBalancer 유형 Service를 만들면 기본 Azure 부하 분산 장치 리소스가 만들어집니다. 부하 분산 장치는 지정된 포트의 Service에 있는 Pod에 트래픽을 분산하도록 구성됩니다. LoadBalancer는 계층 4에서만 작동합니다. 즉 Service에서 실제 애플리케이션을 인식하지 못하고, 라우팅 고려 사항을 추가로 만들 수 없습니다.

*수신 컨트롤러*는 계층 7에서 작동하며, 더 지능적인 규칙을 사용하여 애플리케이션 트래픽을 분산시킬 수 있습니다. 수신 컨트롤러의 일반적인 용도는 인바운드 URL에 따라 HTTP 트래픽을 다른 애플리케이션으로 라우팅하는 것입니다.

![AKS 클러스터의 수신 트래픽 흐름을 보여 주는 다이어그램][aks-ingress]

AKS에서는 NGINX와 같은 도구를 사용하여 수신 리소스를 만들거나 AKS HTTP 애플리케이션 라우팅 기능을 사용할 수 있습니다. AKS 클러스터에 대한 HTTP 애플리케이션 라우팅을 사용하도록 설정하면 Azure 플랫폼에서 수신 컨트롤러와 *External-DNS* 컨트롤러를 만듭니다. Kubernetes에서 새 수신 리소스가 만들어지면 필요한 DNS A 레코드가 클러스터별 DNS 영역에 만들어집니다. 자세한 내용은 [HTTP 애플리케이션 라우팅 배포][aks-http-routing]를 참조하세요.

AGIC (Application Gateway 수신 컨트롤러) 추가 기능을 통해 AKS 고객은 Azure의 기본 Application Gateway 수준 7 부하 분산 장치를 활용 하 여 클라우드 소프트웨어를 인터넷에 노출할 수 있습니다. AGIC는 호스트 되는 Kubernetes 클러스터를 모니터링 하 고 Application Gateway를 지속적으로 업데이트 하 여 선택 된 서비스가 인터넷에 노출 되도록 합니다. AKS의 AGIC 추가 기능에 대 한 자세한 내용은 [Application Gateway 수신 컨트롤러 란?][agic-overview] 을 참조 하세요.

수신의 또 다른 일반적인 기능은 SSL/TLS 종료입니다. HTTPS를 통해 액세스되는 대규모 웹 애플리케이션에서 TLS 종료는 애플리케이션 자체 내에서가 아니라 수신 리소스에서 처리할 수 있습니다. 자동 TLS 인증 생성 및 구성을 제공하기 위해 Let's Encrypt와 같은 공급자를 사용하도록 수신 리소스를 구성할 수 있습니다. Let 's Encrypt를 사용하여 NGINX 수신 컨트롤러를 구성하는 방법에 대한 자세한 내용은 [수신 및 TLS][aks-ingress-tls]를 참조하세요.

AKS 클러스터의 컨테이너에 대 한 요청에 클라이언트 원본 IP를 유지 하도록 수신 컨트롤러를 구성할 수도 있습니다. 클라이언트의 요청이 수신 컨트롤러를 통해 AKS 클러스터의 컨테이너에 라우팅되는 경우 해당 요청의 원래 원본 IP를 대상 컨테이너에서 사용할 수 없습니다. *클라이언트 원본 ip 유지*를 사용 하도록 설정 하면 클라이언트에 대 한 원본 Ip가 *X로 전달 된의*요청 헤더에서 사용할 수 있습니다. 수신 컨트롤러에서 클라이언트 원본 IP 유지를 사용 하는 경우 TLS 통과를 사용할 수 없습니다. 클라이언트 원본 IP 유지 및 TLS 통과는 *LoadBalancer* 유형과 같은 다른 서비스와 함께 사용할 수 있습니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹

네트워크 보안 그룹은 AKS 노드와 같은 VM의 트래픽을 필터링합니다. LoadBalancer와 같은 Service를 만들 때 Azure 플랫폼은 필요한 모든 네트워크 보안 그룹 규칙을 자동으로 구성합니다. AKS 클러스터의 Pod에 대한 트래픽을 필터링하는 네트워크 보안 그룹 규칙은 수동으로 구성하지 않습니다. Kubernetes Service 매니페스트의 일부로 필요한 포트 및 전달을 정의하고, Azure 플랫폼에서 적절한 규칙을 만들거나 업데이트하도록 합니다. 다음 섹션에 설명 된 대로 네트워크 정책을 사용 하 여 pod에 트래픽 필터 규칙을 자동으로 적용할 수도 있습니다.

## <a name="network-policies"></a>네트워크 정책

기본적으로 AKS 클러스터의 모든 pod는 트래픽을 제한 없이 송수신할 수 있습니다. 보안 향상을 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수도 있습니다. 보통 백 엔드 애플리케이션은 필요한 프런트 엔드 서비스에만 제공되고, 데이터베이스 구성 요소는 연결된 애플리케이션 계층에서만 액세스할 수 있습니다.

네트워크 정책은 AKS에서 pod 간의 트래픽 흐름을 제어할 수 있는 Kubernetes 기능입니다. 할당된 레이블, 네임스페이스 또는 트래픽 포트와 같은 설정에 따라 트래픽을 허용하거나 거부하도록 선택할 수 있습니다. 네트워크 보안 그룹은 pod가 아닌 AKS 노드에 더 적합합니다. 네트워크 정책을 통해 트래픽 흐름을 제어하기 위한 더 적합한 클라우드 네이티브 방법을 사용할 수 있습니다. Pod는 AKS 클러스터에서 동적으로 생성되므로 필요한 네트워크 정책을 자동으로 적용할 수 있습니다.

자세한 내용은 [AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호][use-network-policies]를 참조하세요.

## <a name="next-steps"></a>다음 단계

AKS 네트워킹을 시작하려면 [kubenet][aks-configure-kubenet-networking]이나 [Azure CNI][aks-configure-advanced-networking]를 사용하여 자체 IP 주소 범위로 AKS 클러스터를 만들고 구성합니다.

관련 모범 사례는 [AKS의 네트워크 연결 및 보안에 대 한 모범 사례][operator-best-practices-network]를 참조 하세요.

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 액세스 및 ID][aks-concepts-identity]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 스토리지][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
