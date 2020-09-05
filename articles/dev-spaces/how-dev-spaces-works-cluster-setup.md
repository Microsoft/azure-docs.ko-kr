---
title: Azure Dev Spaces 작동을 위해 클러스터를 설정하는 방법
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces 작동을 위해 Azure Kubernetes Service 클러스터를 설정하는 방법을 설명
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 41b955fac7abcc700d73d8ace601f80982b2253d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207955"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Azure Dev Spaces 작동을 위해 클러스터를 설정하는 방법

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. 한 가지 방법은 AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정하는 것입니다. 그러면 [클러스터에서 직접 서비스를 실행][how-it-works-up]하고 [추가 네트워킹 및 라우팅 기능][how-it-works-routing]을 사용할 수 있습니다. 이 문서에서는 클러스터를 준비하고 Azure Dev Spaces를 사용하도록 설정할 때 어떻게 되는지 설명합니다.

## <a name="prepare-your-aks-cluster"></a>AKS 클러스터 준비

AKS 클러스터에서 Dev Spaces를 준비하려면 AKS 클러스터가 [Azure Dev Spaces에서 지원][supported-regions]하는 지역에 있는지, Kubernetes 1.10.3 이상을 실행하고 있는지 확인합니다. 을 실행 하 여 Azure CLI에서 Azure Dev Spaces을 사용 하도록 설정할 수 있습니다 `az aks use-dev-spaces` .

AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정하면 클러스터에 대한 컨트롤러를 설치합니다. 컨트롤러는 AKS 클러스터 외부에 상주합니다. 이는 클라이언트 쪽 도구와 AKS 클러스터 간의 동작 및 통신을 구동합니다. 한 번 사용하도록 설정하고 나면 클라이언트 쪽 도구를 사용하여 컨트롤러와 상호 작용할 수 있습니다.

컨트롤러는 다음 작업을 수행합니다.

* 개발 공간 만들기 및 선택 항목을 관리합니다.
* 애플리케이션의 Helm 차트를 설치하고 Kubernetes 개체를 만듭니다.
* 애플리케이션의 컨테이너 이미지를 빌드합니다.
* 애플리케이션을 AKS에 배포합니다.
* 소스 코드가 변경될 때 증분 빌드와 다시 시작을 수행합니다.
* 로그 및 HTTP 추적을 관리합니다.
* Stdout 및 Stderr을 클라이언트 쪽 도구에 전달합니다.
* 부모 및 자식 공간과 함께 공간 내의 애플리케이션에 대한 라우팅을 모두 구성합니다.

컨트롤러는 클러스터 외부의 개별 Azure 리소스이며 클러스터의 리소스에 대해 다음을 수행합니다.

* 개발 공간으로 사용할 Kubernetes 네임스페이스를 만들거나 지정합니다.
* *azds*라는 Kubernetes 네임스페이스(있는 경우)를 제거하고 새로 만듭니다.
* Kubernetes 웹후크 구성을 배포합니다.
* 웹후크 허용 서버를 배포합니다.

AKS 클러스터에서 다른 Azure Dev Spaces 구성 요소에 대한 서비스 호출을 수행하는 데 사용하는 것과 동일한 서비스 주체를 사용합니다.

![Azure Dev Spaces의 클러스터 준비](media/how-dev-spaces-works/prepare-cluster.svg)

Azure Dev Spaces를 사용하려면 개발 공간이 하나 이상 있어야 합니다. Azure Dev Spaces는 AKS 클러스터 내에서 개발 공간에 Kubernetes 네임스페이스를 사용합니다. 컨트롤러를 설치하는 경우 새 Kubernetes 네임스페이스를 만들거나 첫 번째 개발 공간으로 사용할 기존 네임스페이스를 선택하라는 메시지가 표시됩니다. 기본적으로 컨트롤러는 기존의 *기본* Kubernetes 네임스페이스를 첫 번째 개발 공간으로 업그레이드해야 합니다.

네임스페이스를 개발 공간으로 지정하면 컨트롤러는 *azds.io/space=true* 레이블을 해당 네임스페이스에 추가하여 이를 개발 공간으로 식별합니다. 사용자가 만들거나 지정하는 초기 개발 공간은 클러스터를 준비한 후 기본적으로 선택됩니다. 공간을 선택하면 새 워크로드를 만들기 위해 Azure Dev Spaces에서 사용됩니다.

클라이언트 쪽 도구를 사용하여 새 개발 공간을 만들고 기존 개발 공간을 제거할 수 있습니다. Kubernetes의 제한으로 인해 *기본* 개발 공간은 제거할 수 없습니다. 또한 컨트롤러는 *azds*라는 기존 Kubernetes 네임스페이스를 제거하여 클라이언트 쪽 도구에서 사용하는 `azds` 명령과 충돌하지 않도록 합니다.

Kubernetes 웹후크 허용 서버는 계측을 위해 배포하는 동안 devspaces-proxy 컨테이너, devspaces-proxy-init 컨테이너 및 devspaces-build 컨테이너라는 세 개의 컨테이너를 사용하여 pod를 삽입하는 데 사용됩니다. **이러한 세 컨테이너는 모두 AKS 클러스터에서 루트 액세스로 실행됩니다.** 또한 AKS 클러스터에서 다른 Azure Dev Spaces 구성 요소에 대한 서비스 호출을 수행하는 데 사용하는 것과 동일한 서비스 주체를 사용합니다.

![Azure Dev Spaces Kubernetes 웹후크 허용 서버](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

devspaces-proxy 컨테이너는 애플리케이션 컨테이너에서 들어오고 나가는 모든 TCP 트래픽을 처리하고 라우팅 수행에 도움이 되는 사이드카 컨테이너입니다. 특정 공간이 사용되는 경우 devspaces-proxy 컨테이너는 HTTP 메시지를 라우팅합니다. 예를 들어 부모 및 자식 공간의 애플리케이션 간에 HTTP 메시지를 라우팅하는 데 도움이 될 수 있습니다. 모든 비 HTTP 트래픽은 수정되지 않은 devspaces-proxy를 통해 전달됩니다. 또한 devspaces-proxy 컨테이너는 모든 인바운드 및 아웃바운드 HTTP 메시지를 기록하고 이를 클라이언트 쪽 도구에 추적으로 보냅니다. 개발자는 이러한 추적을 보고 애플리케이션의 동작을 검사할 수 있습니다.

devspaces-proxy-init 컨테이너는 애플리케이션 컨테이너에 공간 계층 구조를 기반으로 하는 추가 라우팅 규칙을 추가하는 [init 컨테이너](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)입니다. 시작하기 전에 애플리케이션 컨테이너의 */etc/resolv.conf* 파일 및 iptables 구성을 업데이트하여 라우팅 규칙을 추가합니다. */etc/resolv.conf* 업데이트는 부모 공간에서 서비스의 DNS 확인을 허용합니다. iptables 구성 업데이트는 애플리케이션의 컨테이너에서 들어오고 나가는 모든 TCP 트래픽이 devspaces-proxy를 통해 라우팅되는지 확인합니다. Kubernetes에서 추가하는 규칙 외에도 devspaces-proxy-init의 모든 업데이트가 발생합니다.

devspaces-build 컨테이너는 init 컨테이너이며 프로젝트 소스 코드와 Docker 소켓이 탑재되어 있습니다. 프로젝트 소스 코드 및 Docker에 대한 액세스를 통해 pod에서 직접 애플리케이션 컨테이너를 빌드할 수 있습니다.

> [!NOTE]
> Azure Dev Spaces는 동일한 노드를 사용하여 애플리케이션의 컨테이너를 빌드하고 실행합니다. 따라서 애플리케이션을 빌드 및 실행하기 위해 Azure Dev Spaces에는 외부 컨테이너 레지스트리가 필요하지 않습니다.

Kubernetes 웹후크 허용 서버는 AKS 클러스터에서 만들어진 새로운 pod를 수신 대기합니다. 해당 pod가 *azds.io/space=true* 레이블을 사용하여 네임스페이스에 배포되는 경우 추가 컨테이너를 사용하여 해당 pod를 삽입합니다. devspaces-build 컨테이너는 애플리케이션의 컨테이너가 클라이언트 쪽 도구를 사용하여 실행되는 경우에만 삽입됩니다.

AKS 클러스터를 준비한 후에는 클라이언트 쪽 도구를 사용하여 개발 공간에서 코드를 준비하고 실행할 수 있습니다.

## <a name="client-side-tooling"></a>클라이언트 쪽 도구

클라이언트 쪽 도구를 사용하여 다음을 수행할 수 있습니다.
* 애플리케이션에 대한 Dockerfile, Helm 차트 및 Azure Dev Spaces 구성 파일을 생성합니다.
* 부모 및 자식 개발 공간을 만듭니다.
* 애플리케이션을 빌드하고 시작하도록 컨트롤러에 지시합니다.

애플리케이션이 실행되는 동안 클라이언트 쪽 도구는 다음을 수행합니다.
* AKS에서 실행되는 애플리케이션에서 stdout 및 stderr을 받아서 표시합니다.
* [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)를 통해 http:\//localhost를 사용하는 애플리케이션에 대한 웹 액세스를 허용합니다.
* AKS에서 실행 중인 애플리케이션에 디버거를 연결합니다.
* 증분 빌드에 대한 변경 내용이 검색되면 개발 공간에 소스 코드를 동기화하여 빠른 반복을 허용합니다.
* 개발자 컴퓨터를 AKS 클러스터에 직접 연결할 수 있습니다.

`azds` 명령의 일부로 명령줄에서 클라이언트 쪽 도구를 사용할 수 있습니다. 클라이언트 쪽 도구를 다음과 함께 사용할 수도 있습니다.

* [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 확장을 사용하는 Visual Studio Code.
* Azure 개발 워크로드를 사용하는 Visual Studio.

## <a name="next-steps"></a>다음 단계

클라이언트 쪽 도구를 사용하여 개발 공간에서 코드를 준비하고 실행하는 방법에 대한 자세한 내용은 [Azure Dev Spaces 작동을 위한 프로젝트 준비 방법][how-it-works-prep]을 참조하세요.


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service