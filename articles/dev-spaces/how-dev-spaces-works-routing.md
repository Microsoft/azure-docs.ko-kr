---
title: 라우팅이 Azure Dev Spaces와 작동 하는 방식
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Power Azure Dev Spaces 프로세스와 라우팅의 작동 방식에 대해 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: abdd8a573b7e120c5494b8878c2eb19813fc9507
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213399"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>라우팅이 Azure Dev Spaces와 작동 하는 방식

Azure Dev Spaces를 사용하면 여러 가지 방법을 통해 Kubernetes 애플리케이션을 신속하게 반복하고 디버그할 수 있으며, AKS(Azure Kubernetes Service) 클러스터에서 팀과 공동 작업이 가능합니다. 프로젝트를 개발 공간에서 실행 하면 Azure Dev Spaces 프로젝트에 대 한 추가 네트워킹 및 라우팅 기능을 제공 합니다.

이 문서에서는 Dev 공간으로 라우팅이 작동 하는 방법을 설명 합니다.

## <a name="how-routing-works"></a>라우팅 작동 방법

Dev 공간은 AKS을 기반으로 구축 되며 동일한 [네트워킹 개념](../aks/concepts-network.md)을 사용 합니다. 또한 Azure Dev Spaces에는 중앙 집중식 *ingressmanager* 서비스가 있으며 자체 수신 컨트롤러를 AKS 클러스터에 배포 합니다. *Ingressmanager* 서비스는 dev 공간을 사용 하 여 AKS 클러스터를 모니터링 하 고, application pod로 라우팅하는 수신 개체를 사용 하 여 클러스터의 Azure Dev Spaces 수신 컨트롤러를 보강 합니다. 각 pod의 devspaces 프록시 컨테이너는 `azds-route-as` URL을 기반으로 하는 개발 공간에 http 트래픽에 대 한 http 헤더를 추가 합니다. 예를 들어 URL에 대 한 요청은를 사용 하 여 *http://azureuser.s.default.serviceA.fedcba09...azds.io* HTTP 헤더를 가져옵니다 `azds-route-as: azureuser` . Devspaces-proxy 컨테이너는 헤더를 추가 하지 않습니다 ( `azds-route-as` 이미 있는 경우).

클러스터 외부에서 서비스에 대 한 HTTP 요청이 이루어지면 요청이 수신 컨트롤러로 이동 합니다. 수신 컨트롤러는 수신 개체 및 규칙에 따라 적절 한 pod로 직접 요청을 라우팅합니다. Pod의 devspaces-proxy 컨테이너는 요청을 수신 하 `azds-route-as` 고 URL에 따라 헤더를 추가한 다음 요청을 응용 프로그램 컨테이너로 라우팅합니다.

클러스터 내의 다른 서비스에서 서비스에 대 한 HTTP 요청이 이루어지면 요청은 먼저 호출 하는 서비스의 devspaces 프록시 컨테이너를 통해 이동 합니다. Devspaces-proxy 컨테이너는 HTTP 요청을 보고 헤더를 확인 합니다 `azds-route-as` . 헤더에 따라 devspaces-proxy 컨테이너는 헤더 값과 연결 된 서비스의 IP 주소를 조회 합니다. IP 주소가 있는 경우 devspaces 프록시 컨테이너는 해당 IP 주소에 대 한 요청을 라우팅합니다. IP 주소를 찾을 수 없는 경우 devspaces 프록시 컨테이너는 해당 요청을 부모 응용 프로그램 컨테이너로 라우팅합니다.

예를 들어 *Servicea* 및 *servicea* 응용 프로그램은 *기본값*이라는 부모 개발 공간에 배포 됩니다. *Servicea는* *servicea* 에 의존 하 고이에 대 한 HTTP 호출을 수행 합니다. Azure 사용자는 *azureuser*라는 *기본* 공간을 기반으로 하는 자식 개발 공간을 만듭니다. 또한 Azure 사용자는 자신의 고유 버전의 *Servicea* 를 해당 하위 공간에 배포 합니다. 다음에 대 한 요청이 수행 되는 경우 *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Azure Dev Spaces 라우팅](media/how-dev-spaces-works/routing.svg)

1. 수신 컨트롤러는 URL ( *Servicea azureuser*)과 연결 된 POD의 IP를 조회 합니다.
1. 수신 컨트롤러는 Azure 사용자의 개발 공간에서 pod의 IP를 찾아 요청을 *Servicea azureuser* pod로 라우팅합니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 요청을 수신 하 고를 `azds-route-as: azureuser` HTTP 헤더로 추가 합니다.
1. *Servicea. azureuser* pod의 devspaces-proxy 컨테이너는 요청을 *servicea. Azureuser* pod의 *servicea* 응용 프로그램 컨테이너로 라우팅합니다.
1. *Servicea. azureuser* Pod의 *servicea* 응용 프로그램은 *servicea*를 호출 합니다. *Servicea* 응용 프로그램에는 기존 헤더를 유지 하는 코드 `azds-route-as` (이 경우)도 포함 됩니다 `azds-route-as: azureuser` .
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 요청을 수신 하 고 헤더의 값을 기반으로 *SERVICEA* 의 IP를 조회 합니다 `azds-route-as` .
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 *servicea azureuser*에 대 한 IP를 찾지 않습니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 부모 *공간에서 SERVICEA의 IP* 를 찾습니다 *. 기본값은 servicea입니다.*
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 *SERVICEA* 의 IP를 찾고 요청을 *servicea. 기본* pod로 라우팅합니다.
1. Serviceb의 devspaces-proxy 컨테이너 *. 기본* pod는 요청을 수신 하 고 *serviceb. 기본* pod의 *serviceb* 응용 프로그램 컨테이너에 요청을 라우팅합니다.
1. Serviceb의 *serviceb* 응용 프로그램은 *기본* pod에 대 한 응답을 반환 *합니다.*
1. *Servicea. azureuser* pod의 devspaces-proxy 컨테이너는 응답을 수신 하 고 *servicea. Azureuser* pod의 *servicea* 응용 프로그램 컨테이너로 응답을 라우팅합니다.
1. *Servicea* 응용 프로그램은 응답을 받은 다음 자체 응답을 반환 합니다.
1. *Servicea. azureuser* pod의 devspaces 프록시 컨테이너는 *servicea* 응용 프로그램 컨테이너에서 응답을 받고 응답을 클러스터 외부의 원래 호출자에 게 라우팅합니다.

HTTP가 아닌 다른 모든 TCP 트래픽은 수신 컨트롤러 및 devspaces 프록시 컨테이너를 통해 수정 되지 않은 상태로 전달 됩니다.

## <a name="sharing-a-dev-space"></a>개발 공간 공유

팀으로 작업할 때 전체 팀에서 개발 공간을 공유 하 고 파생 된 개발 공간을 만들 수 있습니다. 개발 공간의 리소스 그룹에 대 한 참가자 액세스 권한이 있는 사용자는 dev 공간을 사용할 수 있습니다.

다른 개발 공간에서 파생 된 새 개발 공간을 만들 수도 있습니다. 파생 된 dev 공간을 만들 때 *azds.io/parent-space=PARENT-SPACE-NAME* 레이블이 파생 된 dev 공간의 네임 스페이스에 추가 됩니다. 또한 부모 개발 공간의 모든 응용 프로그램은 파생 된 개발 공간과 공유 됩니다. 파생 된 개발 공간에 업데이트 된 버전의 응용 프로그램을 배포 하는 경우 파생 된 dev 공간에만 존재 하며 부모 개발 공간은 영향을 받지 않습니다. 최대 세 수준의 파생 된 개발 공간 또는 *최상위* 공간을 사용할 수 있습니다.

또한 파생 된 개발 공간은 자체 응용 프로그램과 부모에서 공유 되는 응용 프로그램 간에 요청을 지능적으로 라우팅합니다. 라우팅은 파생 된 개발 공간의 응용 프로그램에 요청을 라우팅하고 부모 개발 공간에서 공유 응용 프로그램으로 대체 하는 방식으로 작동 합니다. 응용 프로그램이 부모 공간에 있지 않은 경우 라우팅은 최상위 공간의 공유 응용 프로그램으로 대체 됩니다.

예를 들어:
* 개발 공간 *기본값* 에는 응용 프로그램 *Servicea* 및 *servicea*가 있습니다.
* Dev space *azureuser* 는 *기본값*에서 파생 됩니다.
* *Servicea* 의 업데이트 된 버전이 *azureuser*에 배포 됩니다.

*Azureuser*를 사용 하는 경우 *servicea* 에 대 한 모든 요청은 *azureuser*의 업데이트 된 버전으로 라우팅됩니다. *Serviceb* 에 대 한 요청은 먼저 *Azureuser* 버전의 *serviceb*로 라우팅됩니다. 이 파일은 존재 하지 않으므로 *기본* 버전의 *serviceb*로 라우팅됩니다. *Servicea* 의 *azureuser* 버전이 제거 되 면 *servicea* 에 대 한 모든 요청이 *기본* 버전의 *servicea*를 사용 하는 것으로 대체 됩니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 라우팅을 사용 하 여 신속 하 게 반복 하 고 개발 하는 방법에 대 한 몇 가지 예를 보려면 [로컬 프로세스 (Kubernetes 사용][how-it-works-local-process-kubernetes]), [Azure Dev Spaces 작동 하는 원격 디버깅 방법][how-it-works-remote-debugging]및 [GitHub 작업 & Azure Kubernetes Service][pr-flow]를 참조 하세요.


[helm-upgrade]: https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[pr-flow]: how-to/github-actions.md
[troubleshooting]: troubleshooting.md