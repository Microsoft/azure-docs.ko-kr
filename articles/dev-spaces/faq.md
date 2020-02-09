---
title: Azure Dev Spaces에 대한 질문과 대답
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Azure Dev Spaces에 대한 일반적인 질문에 대한 답변을 찾습니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: 964fa9ec4948bf178c310af8e35913fda5f70c0f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934175"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces에 대한 질문과 대답

이는 Azure Dev Spaces에 대한 질문과 대답을 다룹니다.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>현재 Azure Dev Spaces을 제공 하는 Azure 지역은 무엇 인가요?

사용 가능한 지역의 전체 목록은 [지원 되는 지역][supported-regions] 을 참조 하세요.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>기존 Dockerfiles 또는 투구 차트와 Azure Dev Spaces를 사용할 수 있나요?

예, 프로젝트에 Dockerfile 또는 투구 차트가 이미 있는 경우 Azure Dev Spaces에서 해당 파일을 사용할 수 있습니다. `azds prep`를 실행 하는 경우 `--chart` 매개 변수를 사용 하 여 차트의 위치를 지정 합니다. Azure Dev Spaces은 여전히 *azds* 및 *dockerfile* 파일을 생성 하지만 기존 Dockerfile 또는 투구 차트를 바꾸거나 수정 하지는 않습니다. *Azds* 및 Dockerfile을 수정 해야 할 수 있습니다. 모든 항목이 `azds up`실행 시 기존 응용 프로그램에서 제대로 작동 하도록 파일을 *개발 합니다.*

사용자 고유의 Dockerfile 또는 투구 차트를 사용 하는 경우 다음과 같은 제한 사항이 있습니다.
* Dockerfile을 하나만 사용 하는 경우에는 런타임 뿐만 아니라 언어 SDK와 같은 개발 시나리오를 사용 하도록 설정 하는 데 필요한 모든 항목을 포함 해야 합니다. Dockerfile과 같은 Azure Dev Spaces에 대해 별도의 Dockerfile을 사용 하는 경우 개발 시나리오를 사용 하도록 설정 하는 데 필요한 모든 것이 Dockerfile에 포함 되어야 합니다.
* 투구 차트는 또는 전체 이미지 태그의 일부를 값의 값으로 전달 하는 것을 지원 해야 합니다 *. yaml*.
* 수신으로 모든 항목을 수정 하는 경우 Azure Dev Spaces에서 제공 하는 수신 솔루션을 사용 하도록 투구 차트를 업데이트할 수도 있습니다.
* [Azure Dev Spaces에서 제공 하는 라우팅 기능][dev-spaces-routing]을 사용 하려는 경우 개별 프로젝트에 대한 모든 서비스는 단일 Kubernetes 네임 스페이스 내에 있어야 하며 간단한 이름 (예: *서비스 a)* 을 사용 하 여 배포 해야 합니다. 표준 투구 차트에서이 명명 업데이트는 *Fullnameoverride* 속성의 값을 지정 하 여 수행할 수 있습니다.

사용자 고유의 Dockerfile 또는 투구 차트를 Azure Dev Spaces와 함께 작동 하는 기존 버전과 비교 하려면 [빠른][quickstart-cli]시작에서 생성 된 파일을 검토 합니다.


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Azure Dev Spaces에서 생성 된 파일을 수정할 수 있습니까?

예, [프로젝트를 준비할 때 Azure Dev Spaces에 의해 생성][dev-spaces-prep]된 *Azds* 파일, Dockerfile 및 투구 차트를 수정할 수 있습니다. 이러한 파일을 수정 하면 프로젝트가 빌드되고 실행 되는 방식이 변경 됩니다.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>공용 IP 주소 없이 Azure Dev Spaces를 사용할 수 있나요?

아니요, 공용 IP 없이 AKS 클러스터에 Azure Dev Spaces를 프로 비전 할 수 없습니다. [라우팅 Azure Dev Spaces][dev-spaces-routing]에는 공용 IP가 필요 합니다.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces에서 자체 수신을 사용할 수 있나요?

예, 수신 Azure Dev Spaces 생성 하는 쪽을 따라 직접 수신을 구성할 수 있습니다. 예를 들어 [traefik][ingress-traefik] 또는 [NGINX][ingress-nginx]를 사용할 수 있습니다.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces에서 HTTPS를 사용할 수 있나요?

예, [traefik][ingress-https-traefik] 또는 [NGINX][ingress-https-nginx]를 사용 하 여 HTTPS로 직접 수신을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kubenet가 아닌 CNI를 사용 하는 클러스터에서 Azure Dev Spaces를 사용할 수 있나요? 

예, 네트워킹에 CNI를 사용 하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. 예를 들어 네트워킹에 CNI를 사용 하는 [기존 Windows 컨테이너][windows-containers]와 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure Dev Spaces에서 네트워킹에 CNI를 사용 하는 방법에 대한 자세한 내용은 [여기](configure-networking.md#using-azure-cni)에서 제공 됩니다.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Windows 컨테이너에 Azure Dev Spaces를 사용할 수 있나요?

현재 Azure Dev Spaces는 Linux pod 및 노드에서만 실행 하기 위한 것 이지만 [기존 Windows 컨테이너][windows-containers]를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces을 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API 서버 권한이 부여 된 IP 주소 범위를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, [API 서버 권한이 부여 된 IP 주소 범위][aks-auth-range] 를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. AKS 클러스터를 사용 하는 방법에 대한 자세한 내용은 Azure Dev Spaces에서 사용 하도록 설정 된 API server [권한 있는 IP](configure-networking.md#using-api-server-authorized-ip-ranges)주소 범위를 참조 하세요.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대해 제한 된 송신 트래픽이 있는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, 올바른 Fqdn이 허용 되 면 사용 하도록 설정 된 [클러스터 노드에 대해 제한 된 송신 트래픽을][aks-restrict-egress-traffic] 사용 하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure Dev Spaces 사용 하도록 설정 된 클러스터 노드에 대해 제한 된 송신 트래픽이 있는 AKS 클러스터를 사용 하는 방법에 대한 자세한 내용은 [여기](configure-networking.md#ingress-and-egress-network-traffic-requirements)에서 사용할 수 있습니다.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works.md#prepare-your-code
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md