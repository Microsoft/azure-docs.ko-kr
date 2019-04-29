---
title: Azure Dev Spaces의 비즈니스 연속성 및 재해 복구
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s '
manager: jeconnoc
ms.openlocfilehash: 7b463be143ed3f89c1b10424dafc7a0e841ecbfc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686984"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Azure Dev Spaces의 비즈니스 연속성 및 재해 복구

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)용 재해 복구 지침 검토

Azure Dev Spaces는 AKS(Azure Kubernetes Service)의 기능입니다. AKS의 재해 복구 지침을 파악하고, Dev Spaces에 사용하는 AKS 클러스터에 이러한 지침이 적용되는지 여부를 고려해야 합니다. 자세한 내용은 [AKS(Azure Kubernetes Services)의 비즈니스 연속성 및 재해 복구 모범 사례](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)를 참조하세요.

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>여러 지역에서 AKS 클러스터에 대해 Dev Spaces를 사용하도록 설정

여러 지역에서 AKS 클러스터에 대해 Dev Spaces를 사용하도록 설정하면 Azure 지역 하나에서 오류가 발생하더라도 Dev Spaces 사용을 즉시 다시 시작할 수 있습니다.

여러 지역에 AKS를 배포하는 방법과 관련된 일반 정보는 [여러 지역 배포 계획](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multi-region-deployment)을 참조하세요.

Azure Dev Spaces와 호환되는 AKS 클러스터 배포에 대한 정보는 [Azure Cloud Shell을 사용하여 Kubernetes 클러스터 만들기](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)를 참조하세요.

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Azure Portal을 통해 Dev Spaces를 사용하도록 설정

Azure Portal에서 각 클러스터의 속성 아래에 있는 **Dev Spaces** 탐색 항목을 클릭합니다. 그런 다음 Dev Spaces를 사용하도록 설정하는 옵션을 선택합니다.

![Azure Portal을 통해 Dev Spaces를 사용하도록 설정](../media/common/enable-dev-spaces.jpg)

각 클러스터에 대해 이 프로세스를 반복합니다.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Azure CLI를 통해 Dev Spaces를 사용하도록 설정

명령줄에서 Dev Spaces를 사용하도록 설정할 수도 있습니다.

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>각 클러스터에 팀 초기 계획 배포

Dev Spaces 사용 시에는 보통 Kubernetes 클러스터의 상위 개발 공간에 전체 애플리케이션을 배포합니다. 기본적으로는 `default` 공간이 사용됩니다. 초기 배포에는 모든 서비스와 해당 서비스가 사용하는 외부 리소스(예: 데이터베이스 또는 큐)가 모두 포함됩니다. 이 초기 배포를 *초기 계획*이라고 합니다. 상위 개발 공간에서 초기 계획을 설정한 후에는 해당 과정을 반복하여 하위 개발 공간 내의 개별 서비스를 디버그합니다.

여러 지역의 클러스터에 서비스 초기 계획 세트의 최신 버전을 배포해야 합니다. 이러한 방식으로 초기 계획 서비스를 업데이트하면 Azure 지역 하나에서 오류가 발생하더라도 Dev Spaces를 계속 사용할 수 있습니다. 예를 들어 CI/CD 파이프라인을 통해 초기 계획을 배포하는 경우 각기 다른 지역의 여러 클러스터로 배포되도록 파이프라인을 수정합니다.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Dev Spaces용으로 사용할 올바른 AKS 클러스터 선택

팀의 초기 계획을 실행하는 백업 클러스터를 올바르게 구성하고 나면 언제든지 백업 클러스터로 빠르게 전환할 수 있습니다. 그런 후에는 Dev Spaces에서 작업 중인 개별 서비스를 다시 실행할 수 있습니다.

다음 CLI 명령을 사용하여 다른 클러스터를 선택합니다.

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

다음 명령을 사용하면 새 클러스터에서 사용 가능한 개발 공간 목록을 표시할 수 있습니다.

```cmd
azds space list
```

작업을 진행할 새 개발 공간을 만들 수도 있고 다음 명령을 사용해 기존 개발 공간을 선택할 수도 있습니다.

```cmd
azds space select -n <space name>
```

이러한 명령을 실행하고 나면 Azure Dev Spaces용 Visual Studio Code 확장을 사용하는 디버깅 프로젝트와 후속 CLI 작업에 선택한 클러스터와 개발 공간이 사용됩니다.

Visual Studio를 사용 중이라면 다음 단계를 수행하여 기존 프로젝트에 사용되는 클러스터를 전환할 수 있습니다.

1. Visual Studio에서 새 프로젝트를 엽니다.
1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
1. 왼쪽 창에서 **디버그**를 클릭합니다.
1. 디버그 속성 페이지에서 **프로필** 드롭다운 목록을 클릭하고 **Azure Dev Spaces**를 선택합니다.
1. **변경** 단추를 클릭합니다.
1. 대화 상자가 나타나면 사용할 AKS 클러스터를 선택합니다. 원하는 경우 **공간** 드롭다운 목록에서 적절한 옵션을 선택하여 작업할 다른 개발 공간을 선택하거나 새 개발 공간을 만듭니다.

올바른 클러스터 및 공간을 선택한 후에는 F5 키를 눌러 Dev Spaces에서 서비스를 실행할 수 있습니다.

원본 클러스터를 사용하도록 구성된 기타 모든 프로젝트에 대해 이러한 단계를 반복합니다.

## <a name="access-a-service-on-a-backup-cluster"></a>백업 클러스터에서 서비스 액세스

공용 DNS 이름을 사용하도록 서비스를 구성한 경우 백업 클러스터에서 서비스를 실행하면 서비스의 URL이 달라집니다. 공용 DNS 이름은 항상 `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` 형식입니다. 다른 클러스터로 전환하면 클러스터 GUID가 변경되며 지역도 변경될 수 있습니다.

`azds up`를 실행하면 Dev Spaces에 올바른 URL이 항상 표시됩니다. Visual Studio 출력 창의 **Azure Dev Spaces** 아래에도 올바른 URL이 표시됩니다.

`azds list-uris` 명령을 실행하여 URL을 확인할 수도 있습니다.
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

서비스에 액세스할 때는 이 URL을 사용합니다.
