---
title: Azure Dev Spaces의 작동 원리
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Power Azure Dev Spaces 프로세스를 설명 합니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너
ms.openlocfilehash: 48bde5f3cc6f397d51a31f80f41ab299ba8866ee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212539"
---
# <a name="how-azure-dev-spaces-works"></a>Azure Dev Spaces의 작동 원리

Kubernetes 응용 프로그램을 개발 하는 것은 어려울 수 있습니다. Docker 및 Kubernetes 구성 파일이 필요 합니다. 응용 프로그램을 로컬로 테스트 하 고 다른 종속 서비스와 상호 작용 하는 방법을 파악 해야 합니다. 한 번에 여러 서비스를 개발 하 고 테스트 하는 것을 개발자 팀과 함께 처리 해야 할 수 있습니다.

Azure Dev Spaces는 Kubernetes 응용 프로그램을 신속 하 게 반복 하 고 디버그 하며 팀과 공동 작업할 수 있는 여러 가지 방법을 제공 합니다. 이 문서에서는 수행할 수 있는 작업과 작동 방식을 Azure Dev Spaces 설명 합니다.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Kubernetes 응용 프로그램을 신속 하 게 반복 하 고 디버그

Azure Dev Spaces AKS 클러스터의 컨텍스트에서 Kubernetes 응용 프로그램을 개발, 테스트 및 반복 하는 데 드는 노력이 줄어듭니다. 이러한 노력을 줄이면 개발자는 응용 프로그램의 비즈니스 논리에 집중 하 고 Kubernetes에서 실행 되도록 서비스를 구성 하지 않을 수 있습니다.

### <a name="local-process-with-kubernetes"></a>Kubernetes를 사용한 로컬 프로세스

Kubernetes를 사용 하는 로컬 프로세스를 사용 하면 개발 컴퓨터를 Kubernetes 클러스터에 연결 하 여 클러스터에서 실행 되는 것 처럼 개발 컴퓨터에서 코드를 실행 하 고 디버그할 수 있습니다. Azure Dev Spaces은 원격 에이전트 역할을 하는 pod를 클러스터에서 실행 하 여 연결 된 클러스터 간에 트래픽을 리디렉션하여 개발 컴퓨터와 클러스터 간에 트래픽을 리디렉션합니다. 이러한 트래픽 리디렉션을 통해 개발 컴퓨터의 코드와 클러스터에서 실행 되는 서비스가 동일한 클러스터에 있는 것 처럼 통신할 수 있습니다. 개발 컴퓨터를 Kubernetes 클러스터에 연결 하는 방법에 대 한 자세한 내용은 [Kubernetes를 사용 하는 로컬 프로세스 작동 방식][how-it-works-local-process-kubernetes]을 참조 하세요.

### <a name="run-your-code-in-aks"></a>AKS에서 코드 실행

Azure Dev Spaces를 사용 하 여 개발 컴퓨터와 AKS 클러스터 간의 트래픽을 리디렉션하는 것 외에도 AKS에서 직접 코드를 구성 하 고 신속 하 게 실행할 수 있습니다. Visual Studio, Visual Studio Code 또는 Azure Dev Spaces CLI를 사용 하 여 Azure Dev Spaces는 코드를 클러스터에 업로드 한 다음 빌드하고 실행 합니다. 또한 Azure Dev 공간은 코드 변경을 지능적으로 동기화 하 고 필요에 따라 변경 내용을 반영 하도록 서비스를 다시 시작할 수도 있습니다. 코드를 실행 하는 동안 빌드 로그 및 HTTP 추적은 클라이언트에 다시 스트리밍되 므로 진행률을 모니터링 하 고 문제를 진단할 수 있습니다. 또한 Azure Dev Spaces를 사용 하 여 Visual Studio에서 디버거를 연결 하 고 Java, Node.js 및 .NET Core 서비스에 Visual Studio Code 수 있습니다. 자세한 내용은 [Azure Dev Spaces 작동을 위해 프로젝트를 준비 하는 방법][how-it-works-prep], [Azure Dev Spaces 작업을 사용 하 여 코드를 실행 하는 방법][how-it-works-up], [Azure Dev Spaces를 사용][how-it-works-remote-debugging]하 여 코드를 원격으로 디버깅 하는 방법을 참조 하세요.

## <a name="team-development"></a>팀 개발

Azure Dev Spaces는 팀이 중단 없이 동일한 AKS 클러스터에서 해당 응용 프로그램을 생산적으로 작업 하는 데 도움이 됩니다.

### <a name="intelligent-routing-between-dev-spaces"></a>Dev 공간 간의 인텔리전트 라우팅

Azure Dev Spaces를 사용 하 여 팀은 클라우드 네이티브 응용 프로그램을 실행 하는 단일 AKS 클러스터를 공유 하 고 팀에서 다른 개발 공간을 방해 하지 않고 개발, 테스트 및 디버그할 수 있는 격리 된 개발 공간을 만들 수 있습니다. 응용 프로그램의 기준선 버전은 루트 개발 공간에서 실행 됩니다. 그런 다음 팀 멤버는 응용 프로그램에 대 한 개발, 테스트 및 디버깅 변경 내용에 대 한 루트 공간에 따라 독립적인 자식 개발 공간을 만듭니다. 개발자 공간의 라우팅 기능을 통해 자식 dev 공간은 자식 개발 공간에서 실행 되는 서비스와 부모 개발 공간 간에 요청을 라우팅할 수 있습니다. 이 라우팅을 통해 개발자는 부모 공간에서 종속 서비스를 다시 사용 하면서 서비스의 고유한 버전을 실행할 수 있습니다. 각 하위 공간에는 공동 작업을 위해 다른 사용자가 공유 하 고 액세스할 수 있는 고유한 URL이 있습니다. Azure Dev Spaces에서 라우팅이 작동 하는 방법에 대 한 자세한 내용은 [라우팅을 Azure Dev Spaces와 함께 작동 하는 방법][how-it-works-routing]을 참조 하세요.

### <a name="live-testing-an-open-pull-request"></a>열린 끌어오기 요청에 대 한 라이브 테스트

Azure Dev Spaces와 함께 GitHub 작업을 사용 하 여 병합 하기 전에 클러스터에서 직접 끌어오기 요청에서 응용 프로그램에 대 한 변경 내용을 테스트할 수도 있습니다. Azure Dev Spaces는 응용 프로그램의 검토 버전을 자동으로 클러스터에 배포할 수 있으므로 작성자와 다른 팀 멤버가 전체 응용 프로그램의 컨텍스트 변경 내용을 검토할 수 있습니다. 이 검토 버전의 응용 프로그램은 Azure Dev Spaces의 라우팅 기능을 사용 하 여 다른 개발 공간에 영향을 주지 않고 클러스터에도 배포 됩니다. 이러한 모든 기능을 통해 끌어오기 요청을 안전 하 게 승인 하 고 병합할 수 있습니다. GitHub 작업 및 Azure Dev Spaces의 예제를 보려면 [Azure Kubernetes Service & Github 작업][pr-flow]을 참조 하세요.

## <a name="next-steps"></a>다음 단계

AKS 클러스터에 로컬 개발 컴퓨터를 연결 하는 작업을 시작 하려면 [개발 컴퓨터를 AKS 클러스터에 연결][connect]을 참조 하세요.


[connect]: https://code.visualstudio.com/docs/containers/local-process-kubernetes
[how-it-works-local-process-kubernetes]: /visualstudio/containers/overview-local-process-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development