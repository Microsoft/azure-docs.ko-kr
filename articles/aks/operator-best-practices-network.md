---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 네트워크 연결
description: AKS(Azure Kubernetes Services)의 가상 네트워크 리소스 및 연결에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: mlearned
ms.openlocfilehash: 5b138849538cb9bbd6af6cbcf3e7a11b0cf66395
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549124"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 네트워크 연결 및 보안에 대한 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 만들고 관리할 때 노드 및 애플리케이션에 대해 네트워크 연결을 제공합니다. 이러한 네트워크 리소스에는 IP 주소 범위, 부하 분산 장치 및 수신 컨트롤러가 포함됩니다. 애플리케이션에 대해 높은 서비스 품질을 유지하려면 이러한 리소스를 계획한 후 구성해야 합니다.

이 모범 사례 문서는 클러스터 운영자의 네트워크 연결 및 보안을 집중적으로 다룹니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * AKS에서 Azure CNI 네트워크 모드와 Kubenet 비교
> * 필수 IP 주소 및 연결 계획
> * 부하 분산 장치, 수신 컨트롤러 또는 WAF (웹 응용 프로그램 방화벽)를 사용 하 여 트래픽 분산
> * 클러스터 노드에 안전하게 연결

## <a name="choose-the-appropriate-network-model"></a>적절한 네트워크 모델 선택

**모범 사례 지침** - 기존 가상 네트워크 또는 온-프레미스 네트워크와의 통합을 위해서는 AKS의 Azure CNI 네트워킹을 사용합니다. 또한 이 네트워크 모델을 사용하면 엔터프라이즈 환경에서 리소스 관리와 제어를 보다 유용하게 구분할 수 있습니다.

가상 네트워크는 AKS 노드 및 고객이 애플리케이션에 액세스할 수 있도록 하기 위해 기본 연결을 제공합니다. AKS 클러스터를 가상 네트워크에 배포하는 방법에는 다음 두 가지가 있습니다.

* **Kubenet 네트워킹** -클러스터를 배포 하 고 [Kubenet][kubenet] Kubernetes 플러그 인을 사용 하기 때문에 Azure에서 가상 네트워크 리소스를 관리 합니다.
* **AZURE cni 네트워킹** -기존 가상 네트워크에 배포 하 고 [Cni (Azure Container 네트워킹 인터페이스)][cni-networking] Kubernetes 플러그 인을 사용 합니다. Pod는 다른 네트워크 서비스 또는 온-프레미스 리소스로 라우팅될 수 있는 개별 IP를 수신합니다.

CNI(컨테이너 네트워킹 인터페이스)는 컨테이너 런타임이 네트워크 공급자에게 요청할 수 있도록 하는 공급업체 중립 프로토콜입니다. 사용자가 기존 Azure Virtual Network에 연결할 때 Azure CNI는 pod 및 노드에 IP 주소를 할당하고, IPAM(IP 주소 관리) 기능을 제공합니다. 각 노드 및 pod 리소스는 Azure Virtual Network에서 IP 주소를 받으며, 다른 리소스 또는 서비스와 통신하기 위해 추가 라우팅이 필요하지 않습니다.

![각 노드를 단일 Azure VNet에 연결하는 브리지가 있는 두 노드를 보여주는 다이어그램](media/operator-best-practices-network/advanced-networking-diagram.png)

대부분의 프로덕션 배포에서는 Azure CNI 네트워킹을 사용해야 합니다. 이 네트워크 모델을 사용하면 리소스의 제어 및 관리를 분리할 수 있습니다. 보안 관점에서, 해당 리소스를 관리하는 팀과 보호하는 팀을 구분하려는 경우가 종종 있습니다. Azure CNI 네트워킹을 사용하면 각 pod에 할당된 IP 주소를 통해 직접 기존 Azure 리소스, 온-프레미스 리소스 또는 기타 서비스에 연결할 수 있습니다.

Azure CNI 네트워킹을 사용하면 가상 네트워크 리소스가 AKS 클러스터와는 구분된 리소스 그룹에 있습니다. AKS 서비스 주체가 이러한 리소스를 액세스 및 관리할 수 있도록 권한을 위임합니다. AKS 클러스터에서 사용되는 서비스 주체에는 가상 네트워크 내의 서브넷에 대해 [네트워크 참가자](../role-based-access-control/built-in-roles.md#network-contributor) 이상의 권한이 있어야 합니다. 기본 제공 네트워크 참가자 역할을 사용하는 대신 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 정의하려는 경우 다음 권한이 필요합니다.
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

AKS 서비스 주체 위임에 대한 자세한 내용은 [다른 Azure 리소스에 대한 액세스 위임][sp-delegation]을 참조 하세요.

각 노드 및 pod가 자체 IP 주소를 수신하는 경우 AKS 서브넷의 주소 범위를 계획하세요. 서브넷은 배포하는 모든 노드, pod 및 네트워크 리소스에 대해 IP 주소를 제공할 만큼 충분히 커야 합니다. 각 AKS 클러스터는 자체 서브넷에 배치해야 합니다. Azure에서 온-프레미스 또는 피어링된 네트워크에 대한 연결을 허용하려면 기존 네트워크 리소스와 겹치는 IP 주소 범위를 사용하지 마세요. Kubenet 및 Azure CNI 네트워킹을 사용하여 각 노드에서 실행되는 pod 수는 기본적으로 제한되어 있습니다. 규모 확장 이벤트 또는 클러스터 업그레이드를 처리 하려면 할당 된 서브넷에 사용할 수 있는 추가 IP 주소도 필요 합니다. 이러한 추가 주소 공간은 Windows Server 컨테이너를 사용 하는 경우 특히 중요 합니다 (현재 AKS에서 미리 보기 상태). 이러한 노드 풀에는 최신 보안 패치를 적용 하기 위해 업그레이드가 필요 하기 때문입니다. Windows Server 노드에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조 하세요.

필요한 IP 주소를 계산 하려면 [AKS에서 Azure CNI 네트워킹 구성][advanced-networking]을 참조 하세요.

### <a name="kubenet-networking"></a>Kubenet 네트워킹

Kubenet에서는 클러스터를 배포하기 전에 가상 네트워크를 설정할 필요가 없지만 다음과 같은 단점이 있습니다.

* 노드 및 pod가 서로 다른 IP 서브넷에 배치됩니다. Pod와 노드 간에 트래픽을 라우팅하는 데 UDR(사용자 정의 라우팅) 및 IP 전달이 사용됩니다. 이 추가 라우팅으로 인해 네트워크 성능이 줄어듭니다.
* 기존 온-프레미스 네트워크에 연결하거나 다른 Azure Virtual Network에 피어링하는 작업이 복잡할 수 있습니다.

Kubenet은 AKS 클러스터에서 가상 네트워크 및 서브넷을 별도로 만들 필요가 없으므로 소규모 개발 또는 테스트 워크로드에 적합합니다. 트래픽이 낮은 간단한 웹 사이트를 사용하거나, 워크로드를 컨테이너로 이동 전환(lift & shift)하려는 경우 Kubenet 네트워킹을 사용하여 배포된 AKS 클러스터의 단순성을 활용할 수도 있습니다. 대부분의 프로덕션 배포에서는 Azure CNI 네트워킹을 계획하여 사용해야 합니다. [Kubenet를 사용 하 여 고유한 IP 주소 범위 및 가상 네트워크를 구성할][aks-configure-kubenet-networking]수도 있습니다.

## <a name="distribute-ingress-traffic"></a>수신 트래픽 분산

**모범 사례 지침** - HTTP 또는 HTTPS 트래픽을 애플리케이션으로 분산하려면 수신 리소스 및 컨트롤러를 사용합니다. 수신 컨트롤러는 일반 Azure Load Balancer를 능가하는 추가 기능을 제공하며 기본 Kubernetes 리소스로 관리할 수 있습니다.

Azure Load Balancer는 AKS 클러스터의 애플리케이션에 고객 트래픽을 배포할 수 있지만 해당 트래픽에 대한 정보가 제한됩니다. 부하 분산 장치 리소스는 계층 4에서 작동하며, 프로토콜 또는 포트를 기준으로 트래픽을 분산합니다. HTTP 또는 HTTPS를 사용하는 대부분의 웹 애플리케이션은 계층 7에서 작동하는 Kuberenetes 수신 리소스 및 컨트롤러를 사용해야 합니다. 수신 쪽은 애플리케이션의 URL을 기준으로 트래픽을 분산하며 TLS/SSL 종료를 처리할 수 있습니다. 또한 이 기능은 사용자가 노출 및 매핑하는 IP 주소 수를 줄입니다. 부하 분산 장치를 사용할 경우 각 애플리케이션은 일반적으로 AKS 클러스터의 서비스에 공용 IP 주소가 할당 및 매핑되어야 합니다. 수신 리소스를 사용할 경우 단일 IP 주소로 여러 애플리케이션에 트래픽을 분산할 수 있습니다.

![AKS 클러스터의 수신 트래픽 흐름을 보여 주는 다이어그램](media/operator-best-practices-network/aks-ingress.png)

 수신 쪽에는 두 가지 구성 요소가 있습니다.

 * 바로 수신 *리소스*와
 * 수신 *컨트롤러*입니다.

수신 리소스는 AKS 클러스터에서 실행되는 서비스로 트래픽을 라우팅하기 위한 호스트, 인증서 및 규칙을 정의하는 YAML 매니페스트 `kind: Ingress`입니다. 다음 예제의 YAML 매니페스트는 *myapp.com*에 대한 트래픽을 두 서비스 *blogservice* 또는 *storeservice* 중 하나로 분산합니다. 고객은 액세스하는 URL에 따라 특정 서비스로 이동됩니다.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

수신 컨트롤러는 AKS 노드에 실행되며 수신 요청을 감시하는 디먼입니다. 그런 후에 트래픽은 수신 리소스에 정의된 규칙을 기준으로 분산됩니다. 가장 일반적인 수신 컨트롤러는 [NGINX]를 기준으로 합니다. AKS은 특정 컨트롤러로 제한 하지 않으므로 [조감도][contour], [HAProxy][haproxy], [Traefik][traefik]등의 다른 컨트롤러를 사용할 수 있습니다.

수신 컨트롤러는 Linux 노드에서 예약 되어야 합니다. Windows Server 노드 (현재 AKS에서 미리 보기 상태)는 수신 컨트롤러를 실행 해서는 안 됩니다. YAML 매니페스트 또는 투구 차트 배포에서 노드 선택기를 사용 하 여 리소스를 Linux 기반 노드에서 실행 해야 함을 나타낼 수 있습니다. 자세한 내용은 [노드 선택기를 사용 하 여 AKS에서 pod이 예약 된 위치 제어를][concepts-node-selectors]참조 하세요.

다음 방법 가이드를 포함하여 수신에 대한 다양한 시나리오가 있습니다.

* [외부 네트워크 연결을 사용 하 여 기본 수신 컨트롤러 만들기][aks-ingress-basic]
* [내부, 개인 네트워크 및 IP 주소를 사용 하는 수신 컨트롤러 만들기][aks-ingress-internal]
* [사용자 고유의 TLS 인증서를 사용 하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
* 암호화를 사용 하 여 [동적 공용 ip 주소][aks-ingress-tls] 또는 [고정 공용 ip 주소][aks-ingress-static-tls] 를 사용 하는 TLS 인증서를 자동으로 생성 하는 수신 컨트롤러를 만듭니다.

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>WAF(웹 애플리케이션 방화벽)으로 트래픽 보호

**모범 사례 지침** -잠재적인 공격을 위해 들어오는 트래픽을 검사 하려면 [Barracuda WAF][barracuda-waf] 와 같은 waf (웹 응용 프로그램 방화벽)를 사용 하 여 Azure 또는 Azure 애플리케이션 게이트웨이를 사용 합니다. 이러한 추가적인 고급 네트워크 리소스로 HTTP 및 HTTPS 연결 또는 기본 SSL 종료를 능가해서 트래픽을 라우팅할 수 있습니다.

서비스 및 애플리케이션에 트래픽을 분산하는 수신 컨트롤러는 일반적으로 AKS 클러스터의 Kubernetes 리소스입니다. 이 컨트롤러는 AKS 노드에 대해 디먼으로 실행되고, CPU, 메모리 및 네트워크 대역폭과 같은 일부 노드 리소스를 사용합니다. 좀 더 규모가 큰 환경에서는 이러한 트래픽 라우팅 또는 TLS 종료 일부를 AKS 클러스터 외부의 네트워크 리소스에 오프로드할 수 있습니다. 수신 트래픽에서 잠재적인 공격을 검색할 수도 있습니다.

![Azure App Gateway와 같은 WAF(웹 애플리케이션 방화벽)로 AKS 클러스터에 대한 트래픽을 보호하고 분산할 수 있음](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

WAF(웹 애플리케이션 방화벽)는 수신 트래픽을 필터링하여 추가적인 보안 계층을 제공합니다. OWASP(Open Web Application Security Project)는 교차 사이트 스트립팅 또는 쿠키 악성 침입과 같은 공격을 감시하기 위한 규칙 세트를 제공합니다. [Azure 애플리케이션 게이트웨이][app-gateway] (현재 AKS의 미리 보기 상태)는 트래픽이 AKS 클러스터 및 응용 프로그램에 도달 하기 전에 AKS 클러스터와 통합 하 여 이러한 보안 기능을 제공할 수 있는 waf입니다. 기타 타사 솔루션도 이러한 기능을 수행하므로 지정된 제품의 기존 투자 또는 전문 지식을 계속 사용할 수 있습니다.

부하 분산 장치 또는 수신 리소스는 트래픽 분산을 미세 조정하기 위해 AKS 클러스터에서 계속 실행됩니다. App Gateway는 리소스 정의를 사용하여 중앙에서 수신 컨트롤러로 관리될 수 있습니다. 시작 하려면 [Application Gateway 수신 컨트롤러를 만듭니다][app-gateway-ingress].

## <a name="control-traffic-flow-with-network-policies"></a>네트워크 정책을 통해 트래픽 흐름을 제어

**모범 사례 지침** - 네트워크 정책을 사용하여 pod에 트래픽을 허용하거나 거부합니다. 기본적으로 모든 트래픽이 클러스터 내에서 pod 간에 허용됩니다. 보안 향상을 위해 pod 통신을 제한하는 규칙을 정의합니다.

네트워크 정책은 사용자가 pod 간 트래픽 흐름을 제어할 수 있는 Kubernetes 기능입니다. 할당된 레이블, 네임스페이스 또는 트래픽 포트와 같은 설정에 따라 트래픽을 허용하거나 거부하도록 선택할 수 있습니다. 네트워크 정책을 통해 트래픽 흐름을 제어하는 클라우드 네이티브 방법을 사용할 수 있습니다. Pod는 AKS 클러스터에서 동적으로 생성되므로 필요한 네트워크 정책을 자동으로 적용할 수 있습니다. Pod 간 트래픽을 제어하는 데 Azure 네트워크 보안 그룹을 사용하지 말고, 네트워크 정책을 사용합니다.

네트워크 정책을 사용하려면 AKS 클러스터를 만들 때 기능을 사용하도록 설정해야 합니다. 기존 AKS 클러스터에서는 네트워크 정책을 사용하도록 설정할 수 없습니다. 클러스터에서 네트워크 정책을 사용하도록 설정하고 필요에 따라 사용할 수 있도록 미리 계획합니다. 네트워크 정책은 Linux 기반 노드 및 AKS의 pod에만 사용 해야 합니다.

네트워크 정책은 YAML 매니페스트를 사용하여 Kubernetes 리소스로 생성됩니다. 정책이 정의된 pod에 적용된 다음, 수신 또는 송신 규칙이 트래픽 흐름 방식을 정의합니다. 다음 예제에서는 적용된 *app: backend* 레이블을 통해 네트워크 정책을 pod에 적용합니다. 그런 다음, 수신 규칙은 *app: frontend* 레이블을 통한 포드의 트래픽만 허용합니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

정책을 시작 하려면 [AKS (Azure Kubernetes Service)에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보호][use-network-policies]를 참조 하세요.

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>요새 호스트를 통해 노드에 안전하게 연결

**모범 사례 지침** - AKS 노드에 대한 원격 연결을 노출하지 않도록 합니다. 관리 가상 네트워크에서 요새 호스트 또는 점프 상자를 만듭니다. 요새 호스트를 사용하여 트래픽을 원격 관리 작업을 위한 AKS 클러스터로 안전하게 라우팅할 수 있습니다.

AKS의 작업 대부분은 Azure 관리 도구를 사용하거나 Kubernetes API 서버를 통해 완료할 수 있습니다. AKS 노드는 공용 인터넷에 연결되지 않으며 프라이빗 네트워크에서만 사용할 수 있습니다. 노드에 연결하고 유지 관리를 수행하거나 문제를 해결하려면 요새 호스트 또는 점프 상자를 통하도록 연결을 라우팅합니다. 이 호스트는 AKS 클러스터 가상 네트워크에 안전하게 피어링되는 별도의 관리 가상 네트워크에 있어야 합니다.

![요새 호스트 또는 점프 상자를 사용하여 AKS 노드에 연결](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

요새 호스트에 대한 관리 네트워크도 보호해야 합니다. [Azure express][expressroute] 경로 또는 [VPN gateway][vpn-gateway] 를 사용 하 여 온-프레미스 네트워크에 연결 하 고 네트워크 보안 그룹을 사용 하 여 액세스를 제어 합니다.

## <a name="next-steps"></a>다음 단계

이 문서는 네트워크 연결 및 보안에 주안점을 두었습니다. Kubernetes의 네트워크 기본 사항에 대한 자세한 내용은 [Azure Kubernetes Service의 응용 프로그램에 대한 네트워크 개념 (AKS)][aks-concepts-network] 을 참조 하세요.

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool