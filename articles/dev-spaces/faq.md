---
title: Azure Dev Spaces에 대 한 질문과 대답
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Azure Dev Spaces에 대 한 일반적인 질문에 대 한 답변을 찾습니다.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
ms.openlocfilehash: 739cfdb4f930d384e180ffd0f3ce2311cd41c70a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207985"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Azure Dev Spaces에 대 한 질문과 대답

이는 Azure Dev Spaces에 대 한 질문과 대답을 다룹니다.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure Dev Spaces에 대해 지원 되는 Kubernetes 버전은 무엇 인가요?

Azure Dev Spaces [는 AKS에서 현재 지원 되는 GA (일반 공급) 버전을][aks-supported-k8s]모두 지원 합니다.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>현재 Azure Dev Spaces을 제공 하는 Azure 지역은 무엇 인가요?

사용 가능한 지역의 전체 목록은 [지원 되는 지역][supported-regions] 을 참조 하세요.

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Azure Dev Spaces를 사용 하 여 AKS 클러스터를 다른 지역으로 마이그레이션할 수 있나요?

예, Azure Dev Spaces로 AKS 클러스터를 다른 [지원 되는 지역][supported-regions]으로 이동 하려면 다른 지역에 새 클러스터를 만든 다음 Azure Dev Spaces를 설치 및 구성 하 고 리소스 및 응용 프로그램을 새 클러스터에 배포 하는 것이 좋습니다. AKS 마이그레이션에 대 한 자세한 내용은 [AKS (Azure Kubernetes Service로 마이그레이션)를][aks-migration]참조 하세요.

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>기존 Dockerfiles 또는 투구 차트와 Azure Dev Spaces를 사용할 수 있나요?

예, 프로젝트에 Dockerfile 또는 투구 차트가 이미 있는 경우 Azure Dev Spaces에서 해당 파일을 사용할 수 있습니다. 을 실행 하는 경우 `azds prep` `--chart` 매개 변수를 사용 하 고 차트의 위치를 지정 합니다. Azure Dev Spaces은 여전히 *azds* 및 *dockerfile* 파일을 생성 하지만 기존 Dockerfile 또는 투구 차트를 바꾸거나 수정 하지는 않습니다. *Azds* 및 *dockerfile* 을 수정 해야 할 수 있습니다. 모든 항목이 실행 중일 때 기존 응용 프로그램에서 제대로 작동 하도록 하려면 파일을 개발 합니다. `azds up`

사용자 고유의 Dockerfile 또는 투구 차트를 사용 하는 경우 다음과 같은 제한 사항이 있습니다.
* Dockerfile을 하나만 사용 하는 경우에는 런타임 뿐만 아니라 언어 SDK와 같은 개발 시나리오를 사용 하도록 설정 하는 데 필요한 모든 항목을 포함 해야 합니다. Dockerfile과 같은 Azure Dev Spaces에 대해 별도의 Dockerfile을 사용 하는 경우 개발 시나리오를 사용 하도록 설정 하는 데 필요한 모든 것이 Dockerfile에 포함 되어야 합니다.
* 투구 차트는 또는 전체 이미지 태그의 일부를 값의 값으로 전달 하는 것을 지원 해야 합니다 *. yaml*.
* 수신으로 모든 항목을 수정 하는 경우 Azure Dev Spaces에서 제공 하는 수신 솔루션을 사용 하도록 투구 차트를 업데이트할 수도 있습니다.
* [Azure Dev Spaces에서 제공 하는 라우팅 기능][dev-spaces-routing]을 사용 하려는 경우 개별 프로젝트에 대 한 모든 서비스는 단일 Kubernetes 네임 스페이스 내에 있어야 하며 간단한 이름 (예: *서비스 a)* 을 사용 하 여 배포 해야 합니다. 표준 투구 차트에서이 명명 업데이트는 *Fullnameoverride* 속성의 값을 지정 하 여 수행할 수 있습니다.

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Azure Dev Spaces에서 생성 된 파일을 수정할 수 있습니까?

예, [프로젝트를 준비할 때 Azure Dev Spaces에 의해 생성][dev-spaces-prep]된 *Azds* 파일, Dockerfile 및 투구 차트를 수정할 수 있습니다. 이러한 파일을 수정 하면 프로젝트가 빌드되고 실행 되는 방식이 변경 됩니다.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>공용 IP 주소 없이 Azure Dev Spaces를 사용할 수 있나요?

아니요, 공용 IP 없이 AKS 클러스터에 Azure Dev Spaces를 프로 비전 할 수 없습니다. [라우팅 Azure Dev Spaces][dev-spaces-routing]에는 공용 IP가 필요 합니다.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Azure Dev Spaces에서 자체 수신을 사용할 수 있나요?

예, 수신 Azure Dev Spaces 생성 하는 쪽을 따라 직접 수신을 구성할 수 있습니다. 예를 들어 [traefik][ingress-traefik] 또는 [NGINX][ingress-nginx]를 사용할 수 있습니다.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Azure Dev Spaces에서 HTTPS를 사용할 수 있나요?

예, [traefik][ingress-https-traefik] 또는 [NGINX][ingress-https-nginx]를 사용 하 여 HTTPS로 직접 수신을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kubenet가 아닌 CNI를 사용 하는 클러스터에서 Azure Dev Spaces를 사용할 수 있나요? 

예, 네트워킹에 CNI를 사용 하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. 예를 들어 네트워킹에 CNI를 사용 하는 [기존 Windows 컨테이너][windows-containers]와 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure Dev Spaces에서 네트워킹에 CNI를 사용 하는 방법에 대 한 자세한 내용은 [여기](configure-networking.md#using-azure-cni)에서 제공 됩니다.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Windows 컨테이너에 Azure Dev Spaces를 사용할 수 있나요?

현재 Azure Dev Spaces는 Linux pod 및 노드에서만 실행 하기 위한 것 이지만 [기존 Windows 컨테이너][windows-containers]를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces을 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>API 서버 권한이 부여 된 IP 주소 범위를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, [API 서버 권한이 부여 된 IP 주소 범위][aks-auth-range] 를 사용 하 여 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. AKS 클러스터를 사용 하는 방법에 대 한 자세한 내용은 Azure Dev Spaces에서 사용 하도록 설정 된 API server [권한 있는 IP](configure-networking.md#using-api-server-authorized-ip-ranges)주소 범위를 참조 하세요.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>클러스터 노드에 대해 제한 된 송신 트래픽이 있는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, 올바른 Fqdn이 허용 되 면 사용 하도록 설정 된 [클러스터 노드에 대해 제한 된 송신 트래픽을][aks-restrict-egress-traffic] 사용 하는 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있습니다. Azure Dev Spaces 사용 하도록 설정 된 클러스터 노드에 대해 제한 된 송신 트래픽이 있는 AKS 클러스터를 사용 하는 방법에 대 한 자세한 내용은 [여기](configure-networking.md#ingress-and-egress-network-traffic-requirements)에서 사용할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>RBAC 지원 AKS 클러스터에서 Azure Dev Spaces를 사용할 수 있나요?

예, RBAC를 사용 하거나 사용 하지 않고 AKS 클러스터에 대 한 Azure Dev Spaces를 사용할 수 있습니다.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Visual Studio에서 프로젝트를 수신 하도록 설정 하면 어떻게 되나요?

Visual Studio를 사용 하 여 프로젝트를 준비할 때 서비스에 대 한 수신을 사용 하도록 설정 하는 옵션이 있습니다. 수신을 사용 하도록 설정 하면 AKS 클러스터에서 실행 될 때 서비스에 액세스할 수 있는 공용 끝점이 생성 됩니다 (선택 사항). 수신을 사용 하지 않는 경우 서비스는 AKS 클러스터 내 에서만 액세스할 수 있습니다.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Azure Dev Spaces에서 pod 관리 id를 사용할 수 있나요?

예, Azure Dev Spaces 사용 하도록 설정 된 상태로 AKS 클러스터에서 [pod 관리 id][aks-pod-managed-id] 를 사용할 수 있지만 pod 관리 id를 사용 하 여 클러스터에서 Azure Dev Spaces를 사용 하도록 설정한 후에는 [추가 구성 단계가][dev-spaces-pod-managed-id-steps] 있습니다. Pod 관리 id가 설치 되어 있고 제거 하려는 경우 [제거 노트][aks-pod-managed-id-uninstall]에서 자세한 내용을 확인할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>응용 프로그램에서 여러 마이크로 서비스에 Azure Dev Spaces를 사용할 수 있나요?

예, 여러 마이크로 서비스가 있는 응용 프로그램에서 Azure Dev Spaces를 사용할 수 있지만 루트에서 개별 마이크로 서비스를 준비 하 고 실행 해야 합니다. Azure Dev Spaces CLI, Azure Dev Spaces VS Code 확장 및 Visual Studio Azure 개발 워크 로드를 실행 하 고 디버그 하기 위해 *azds* 파일이 마이크로 서비스의 루트에 있을 것으로 간주 합니다. 단일 응용 프로그램의 여러 마이크로 서비스에 대 한 예제는 [자전거 공유 샘플 응용 프로그램][bike-sharing] 을 참조 하세요.

Visual Studio Code에서는 [개별 프로젝트를 단일 작업 영역에서 열고][vs-code-multi-root-workspaces] Azure Dev Spaces를 통해 개별적으로 디버그할 수 있습니다. 각 프로젝트는 Azure Dev Spaces에 대해 자체 포함 되 고 준비 되어야 합니다.

Visual Studio에서는 Azure Dev Spaces를 통해 디버깅 하는 데 필요한 .NET Core 솔루션을 구성할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>서비스 메시에 Azure Dev Spaces를 사용할 수 있나요?

지금은 [Istio][istio] 또는 [linkerd][linkerd]와 같은 서비스 메시와 Azure Dev Spaces 사용할 수 없습니다. Azure Dev Spaces 및 서비스 메시를 동일한 AKS 클러스터에서 실행할 수 있지만 Azure Dev Spaces와 서비스 메시를 동일한 네임 스페이스에서 사용할 수 없습니다.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md