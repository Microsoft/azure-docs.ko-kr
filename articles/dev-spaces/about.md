---
title: Azure Dev Spaces란?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Azure Dev Spaces가 Azure Kubernetes Service 클러스터의 팀에게 신속하고 반복 가능한 Kubernetes 개발 환경을 제공하는 방법을 알아봅니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, kubectl, k8s
manager: gwallace
ms.openlocfilehash: ad884d5e054f23269b238b791e250c732d70a9b8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210640"
---
# <a name="what-is-azure-dev-spaces"></a>Azure Dev Spaces란?

Azure Dev Spaces는 AKS(Azure Kubernetes Service) 클러스터의 팀에게 신속하고 반복 가능한 Kubernetes 개발 환경을 제공합니다. Azure Dev Spaces에서는 종속성을 복제하거나 모의로 실행하지 않고 최소의 시스템 설정만으로 AKS의 모든 애플리케이션 구성 요소를 디버그하고 테스트할 수 있습니다.

![다이어그램은 독립적으로 개발된 두 가지 버전의 애플리케이션을 보여줍니다. 그런 다음, Azure Dev Spaces 개발 환경에서 하나로 결합됩니다.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Azure Dev 공간이 Kubernetes 개발을 단순화하는 방법

Azure Dev Spaces를 사용하면 개발 팀이 전체 마이크로서비스 아키텍처 또는 AKS에서 실행되는 애플리케이션을 직접 작업할 수 있으므로 개발 팀이 마이크로서비스 애플리케이션의 개발과 신속한 반복에 집중할 수 있습니다. 뿐만 아니라 Azure Dev Spaces는 AKS 클러스터의 나머지 부분 또는 다른 개발자에게 영향을 주지 않고 격리된 마이크로서비스 아키텍처의 일부를 독립적으로 업데이트할 수 있는 방법을 제공합니다. Azure Dev Spaces는 프로덕션 AKS 클러스터에서 실행하는 용도가 아닌 하위 수준의 개발 및 테스트 환경에서 앱을 개발하고 테스트하는 용도로 사용됩니다.

Azure Dev Spaces를 사용하면 개발 팀이 AKS에서 바로 전체 애플리케이션을 작업하고 협업할 수 있으므로 다음과 같은 장점이 있습니다.

* 로컬 머신 설치 최소화
* 개발 팀의 신규 개발자를 위한 설정 시간 감소
* 빠른 반복을 통해 개발 팀의 속도 향상
* 팀원이 클러스터를 공유할 수 있으므로 중복되는 개발 및 통합 환경 수 감소
* 종속성을 복제 또는 목업할 필요가 없음
* 개발 팀 그리고 개발 팀과 협력하는 다른 팀(예: DevOps 팀)의 협업 개선

Azure Dev Spaces는 프로젝트를 위한 Docker 및 Kubernetes 자산을 생성하는 도구를 제공합니다. 이 도구를 사용하면 개발 공간과 기타 AKS 클러스터에 신규 및 기존 애플리케이션을 쉽게 추가할 수 있습니다.

Azure Dev Spaces의 작동 방식에 대한 자세한 내용은 [Azure Dev Spaces 작동 및 구성 방법][how-dev-spaces-works]을 참조하세요.

## <a name="supported-regions-and-configurations"></a>지원되는 Azure 지역 및 구성

Azure Dev Spaces는 [일부 지역][supported-regions]의 AKS 클러스터에 의해서만 지원됩니다. Azure Dev Spaces는 Linux, macOS 또는 Windows 8 이상에 설치된 [Azure Dev Spaces 확장](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)에서 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Visual Studio Code](https://code.visualstudio.com/download)를 사용하여 AKS에서 애플리케이션을 빌드하고 실행할 수 있습니다. 또한 Azure Development 워크로드와 함께 Windows에 설치된 [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) 사용을 지원합니다.

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방식에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
