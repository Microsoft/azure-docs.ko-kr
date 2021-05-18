---
title: Azure Dev Spaces를 사용하도록 설정하여 Kubernetes 클러스터 만들기 - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 아무것도 설치하지 않고 브라우저에서 바로, Azure Dev Spaces에 사용할 수 있는 Kubernetes 클러스터를 빠르게 만드는 방법을 알아보세요.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
ms.openlocfilehash: ce73f46a2451dfa87751d90f9cd6b31d5c80683f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91963553"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Azure Cloud Shell을 통해 Azure Dev Spaces를 사용하도록 설정하여 Kubernetes 클러스터 만들기

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

이 페이지의 **사용해 보기** 단추를 사용하면 [Azure Cloud Shell](/azure/cloud-shell)을 통해 Azure Container Service 클러스터를 만들 수 있습니다. 로그인하지 않은 경우 프롬프트에 따라 Azure 계정으로 로그인한 다음, Azure Cloud Shell 프롬프트가 표시되면 명령을 입력합니다.

## <a name="create-the-cluster"></a>클러스터 만들기

먼저 [Azure Dev Spaces를 지원하는 지역][supported-regions]에서 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

다음 명령을 사용하여 Kubernetes 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

클러스터를 만드는 데 몇 분이 걸립니다.  완료되면 출력이 JSON 형식으로 표시됩니다. `provisioningState`를 찾아 `Succeeded`인지 확인합니다.

## <a name="next-steps"></a>다음 단계

전체 자습서에 대한 링크는 [Azure Dev Spaces](../index.yml)를 참조하세요.

> [!IMPORTANT]
> 많은 Azure Dev Spaces 빠른 시작 및 자습서에서는 Azure Dev Spaces CLI를 사용하여 작업을 수행합니다. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
