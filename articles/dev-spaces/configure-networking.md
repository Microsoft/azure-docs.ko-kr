---
title: 다른 네트워크 토폴로지에서 Azure Dev Spaces에 대 한 네트워킹 구성
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Azure Kubernetes Services에서 Azure Dev Spaces를 실행 하기 위한 네트워킹 요구 사항을 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, CNI, kubenet, SDN, 네트워크
ms.openlocfilehash: 0d9ebbec3e3c07a466acb58e88b67e6a32a20edb
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214178"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>다른 네트워크 토폴로지에서 Azure Dev Spaces에 대 한 네트워킹 구성

Azure Dev Spaces는 기본 네트워킹 구성을 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에서 실행 됩니다. 클러스터를 방화벽 뒤에 배치 하거나 네트워크 보안 그룹을 사용 하거나 네트워크 정책을 사용 하는 것과 같이 AKS 클러스터의 네트워킹 구성을 변경 하려면 Azure Dev Spaces를 실행 하기 위한 추가 고려 사항을 통합 해야 합니다.

![가상 네트워크 구성](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>가상 네트워크 또는 서브넷 구성

AKS 클러스터에는 AKS 클러스터에 대 한 수신 또는 송신 트래픽을 제한 하는 다른 가상 네트워크 또는 서브넷 구성이 있을 수 있습니다. 예를 들어 클러스터가 Azure 방화벽과 같은 방화벽 뒤에 있거나 네트워크 보안 그룹 또는 사용자 지정 역할을 사용 하 여 네트워크 트래픽을 제한할 수 있습니다. [GitHub의 Azure Dev Spaces 샘플 리포지토리에서][sample-repo]네트워크 구성에 대 한 예제를 찾을 수 있습니다.

Azure Dev Spaces는 수신 *및 송신* 네트워크 트래픽과 *수신 전용* 트래픽만 위한 특정 요구 사항이 있습니다. AKS 클러스터에 대 한 트래픽을 제한 하는 가상 네트워크 또는 서브넷 구성을 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용 하는 경우 Azure Dev Spaces 제대로 작동 하려면 다음 수신과 수신 및 송신 트래픽 요구 사항을 따라야 합니다.

### <a name="ingress-and-egress-network-traffic-requirements"></a>수신 및 송신 네트워크 트래픽 요구 사항

다음 Fqdn에 대 한 수신 및 송신 트래픽이 Azure Dev Spaces 필요 합니다.

| FQDN                       | 포트       | 사용      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Azure Dev Spaces에 대 한 docker 이미지를 꺼내려면 |
| gcr.io                     | HTTPS: 443 | Azure Dev Spaces에 대 한 투구 이미지를 꺼내려면 |
| storage.googleapis.com     | HTTPS: 443 | Azure Dev Spaces에 대 한 투구 이미지를 꺼내려면 |

위의 Fqdn 및 [Azure Dev Spaces 인프라 서비스][service-tags]모두와의 네트워크 트래픽을 허용 하도록 방화벽 또는 보안 구성을 업데이트 합니다. 예를 들어 방화벽을 사용 하 여 네트워크를 보호 하는 경우 위의 Fqdn이 방화벽의 응용 프로그램 규칙에 추가 되 고 Azure Dev Spaces 서비스 태그도 [방화벽에 추가][firewall-service-tags]되어야 합니다. 이러한 도메인 으로부터 들어오고 나가는 트래픽을 허용 하려면 방화벽에 대 한 이러한 업데이트가 모두 필요 합니다.

### <a name="ingress-only-network-traffic-requirements"></a>수신 전용 네트워크 트래픽 요구 사항

Azure Dev Spaces는 Kubernetes 네임 스페이스 수준 라우팅 뿐만 아니라 자체 FQDN을 사용 하 여 서비스에 대 한 공용 액세스를 제공 합니다. 이러한 두 기능 모두 작동 하려면 클러스터에서 Azure Dev Spaces 수신 컨트롤러의 외부 IP 주소에 대 한 공용 수신을 허용 하도록 방화벽 또는 네트워크 구성을 업데이트 합니다. 또는 [내부 부하 분산 장치][aks-internal-lb] 를 만들고 방화벽의 공용 ip를 내부 부하 분산 장치의 ip로 변환 하는 NAT 규칙을 추가할 수 있습니다. [Traefik][traefik-ingress] 또는 [NGINX][nginx-ingress] 를 사용 하 여 사용자 지정 수신 컨트롤러를 만들 수도 있습니다.

## <a name="aks-cluster-network-requirements"></a>AKS 클러스터 네트워크 요구 사항

AKS를 사용 하면 [네트워크 정책을][aks-network-policies] 사용 하 여 pod의 송신 트래픽 뿐만 아니라 클러스터의 pod 간에 수신 및 송신 트래픽을 제어할 수 있습니다. Azure Dev Spaces는 수신 *및 송신* 네트워크 트래픽과 *수신 전용* 트래픽만 위한 특정 요구 사항이 있습니다. AKS 네트워크 정책을 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용 하는 경우 Azure Dev Spaces 제대로 작동 하려면 다음 수신과 수신 및 송신 트래픽 요구 사항을 따라야 합니다.

### <a name="ingress-and-egress-network-traffic-requirements"></a>수신 및 송신 네트워크 트래픽 요구 사항

Azure Dev Spaces를 사용 하면 디버깅을 위해 클러스터의 개발 공간에서 pod와 직접 통신할 수 있습니다. 이 기능이 작동 하려면 [지역에 따라 달라][service-tags]지는 Azure Dev Spaces 인프라의 IP 주소에 수신 및 송신 통신을 허용 하는 네트워크 정책을 추가 합니다.

### <a name="ingress-only-network-traffic-requirements"></a>수신 전용 네트워크 트래픽 요구 사항

Azure Dev Spaces는 네임 스페이스 간 pod 간의 라우팅을 제공 합니다. 예를 들어 Azure Dev Spaces 사용 하도록 설정 된 네임 스페이스는 부모/자식 관계를 가질 수 있습니다 .이 관계를 사용 하면 부모/자식 네임 스페이스를 통해 pod 간에 네트워크 트래픽을 라우팅할 수 있습니다. 또한 Azure Dev Spaces는 자체 FQDN을 사용 하 여 서비스 끝점을 노출 합니다. 서비스를 노출 하는 다양 한 방법과 네임 스페이스 수준 라우팅에 영향을 주는 방법을 구성 하려면 [다른 끝점 옵션 사용][endpoint-options]을 참조 하세요.

## <a name="using-azure-cni"></a>Azure CNI 사용

기본적으로 AKS 클러스터는 Azure Dev Spaces와 작동 하는 네트워킹에 [kubenet][aks-kubenet] 를 사용 하도록 구성 됩니다. [CNI (Azure Container 네트워킹 인터페이스)][aks-cni]를 사용 하도록 AKS 클러스터를 구성할 수도 있습니다. AKS 클러스터에서 Azure CNI를 사용 하 여 Azure Dev Spaces를 사용 하려면 Azure Dev Spaces에서 배포 된 pod에 대 한 가상 네트워크 및 서브넷 주소 공간을 최대 10 개까지 허용 합니다. 개인 IP 주소를 허용 하는 방법에 대 한 자세한 내용은 [AKS Azure cni 설명서][aks-cni-ip-planning]에서 확인할 수 있습니다.

## <a name="using-api-server-authorized-ip-ranges"></a>API 서버에 권한 있는 IP 범위 사용

AKS 클러스터를 사용 하면 사용자 지정 가상 네트워크를 사용 하거나 [권한 있는 ip 범위를 사용 하 여 API 서버에][aks-ip-auth-ranges]대 한 액세스를 보호 하는 등 클러스터와 상호 작용할 수 있는 추가 보안을 구성할 수 있습니다. 클러스터를 [만드는][aks-ip-auth-range-create] 동안이 추가 보안을 사용 하는 경우 Azure Dev Spaces를 사용 하려면 [해당 지역에 따라 추가 범위를 허용][service-tags]해야 합니다. 또한 기존 클러스터를 [업데이트][aks-ip-auth-range-update] 하 여 이러한 추가 범위를 허용할 수 있습니다. 또한 API 서버에 연결 하기 위해 AKS 클러스터에 연결 하는 모든 개발 컴퓨터의 IP 주소를 허용 해야 합니다.

## <a name="using-aks-private-clusters"></a>AKS 개인 클러스터 사용

지금은 [AKS 개인 클러스터][aks-private-clusters]에서 Azure Dev Spaces 지원 되지 않습니다.

## <a name="using-different-endpoint-options"></a>다른 끝점 옵션 사용

Azure Dev Spaces에는 AKS에서 실행 되는 서비스에 대 한 끝점을 노출 하는 옵션이 있습니다. 클러스터에서 Azure Dev Spaces를 사용 하도록 설정 하는 경우 클러스터의 끝점 유형을 구성 하는 다음과 같은 옵션을 사용할 수 있습니다.

* 기본값인 *공용* 끝점은 공용 IP 주소를 사용 하 여 수신 컨트롤러를 배포 합니다. 공용 IP 주소는 클러스터의 DNS에 등록 되므로 URL을 사용 하 여 서비스에 대 한 공용 액세스를 허용 합니다. 을 사용 하 여이 URL을 볼 수 있습니다 `azds list-uris` .
* *개인* 끝점은 개인 IP 주소를 사용 하 여 수신 컨트롤러를 배포 합니다. 개인 IP 주소를 사용 하는 경우 클러스터의 부하 분산 장치는 클러스터의 가상 네트워크 내 에서만 액세스할 수 있습니다. 부하 분산 장치의 개인 IP 주소는 클러스터의 DNS에 등록 되므로 URL을 사용 하 여 클러스터의 가상 네트워크 내 서비스에 액세스할 수 있습니다. 을 사용 하 여이 URL을 볼 수 있습니다 `azds list-uris` .
* Endpoint 옵션에 대해 *none* 을 설정 하면 수신 컨트롤러를 배포할 수 없습니다. 수신 컨트롤러를 배포 하지 않은 경우 [Azure Dev Spaces 라우팅 기능이][dev-spaces-routing] 작동 하지 않습니다. 필요에 따라 [traefik][traefik-ingress] 또는 [NGINX][nginx-ingress]를 사용 하 여 사용자 고유의 수신 컨트롤러 솔루션을 구현할 수 있습니다. 그러면 라우팅 기능이 다시 작동할 수 있습니다.

끝점 옵션을 구성 하려면 클러스터에서 Azure Dev Spaces를 사용 하도록 설정할 때 *-e* 또는 *--엔드포인트* 를 사용 합니다. 예를 들어:

> [!NOTE]
> Endpoint 옵션을 사용 하려면 Azure CLI 버전 2.2.0 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>클라이언트 요구 사항

Azure Dev Spaces는 Azure Dev Spaces CLI 확장, Visual Studio Code 확장 및 Visual Studio 확장과 같은 클라이언트 쪽 도구를 사용 하 여 디버깅을 위해 AKS 클러스터와 통신 합니다. Azure Dev Spaces 클라이언트 쪽 도구를 사용 하려면 개발 컴퓨터에서 [Azure Dev Spaces 인프라로][dev-spaces-allow-infrastructure]의 트래픽을 허용 합니다. [Api 서버에 권한 있는 ip 범위][auth-range-section]를 사용 하는 경우 api 서버에 연결 하기 위해 AKS 클러스터에 연결 하는 모든 개발 컴퓨터의 IP 주소도 허용 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags