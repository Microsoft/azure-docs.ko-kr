---
title: Azure Kubernetes Service에서 관리 되는 id 사용
description: Azure Kubernetes 서비스 (AKS)에서 관리 id를 사용 하는 방법 알아보기
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121439"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>미리 보기-Azure Kubernetes Service에서 관리 되는 id 사용

현재 azure Kubernetes 서비스 (AKS) 클러스터 (특히 Kubernetes 클라우드 공급자)에는 Azure에서 부하 분산 장치 및 관리 디스크와 같은 추가 리소스를 만들기 위한 *서비스 주체가* 필요 합니다. 서비스 주체를 제공 하거나 사용자를 대신 하 여 AKS를 만들어야 합니다. 서비스 사용자에 게는 일반적으로 만료 날짜가 있습니다. 클러스터는 궁극적으로 클러스터 작동을 유지 하기 위해 서비스 주체를 갱신 해야 하는 상태에 도달 합니다. 서비스 주체를 관리 하면 복잡성이 증가 합니다.

*관리 id* 는 기본적으로 서비스 사용자를 중심으로 하는 래퍼로, 관리를 단순화 합니다. 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 참조 하세요.

AKS은 두 개의 관리 되는 id를 만듭니다.

- **시스템 할당 관리 id**: Kubernetes 클라우드 공급자가 사용자를 대신 하 여 Azure 리소스를 만드는 데 사용 하는 id입니다. 시스템 할당 id의 수명 주기는 클러스터와 연결 됩니다. 이 id는 클러스터가 삭제 될 때 삭제 됩니다.
- **사용자 할당 관리 id**: 클러스터에서 권한 부여에 사용 되는 id입니다. 예를 들어 사용자 할당 id는 Acr (액세스 제어 레코드)를 사용 하 여 AKS에 게 권한을 부여 하거나 Azure에서 메타 데이터를 가져오도록 kubelet에 게 권한을 부여 하는 데 사용 됩니다.

이 미리 보기 기간에는 서비스 사용자가 여전히 필요 합니다. 모니터링, 가상 노드, Azure Policy 및 HTTP 응용 프로그램 라우팅과 같은 추가 기능을 권한 부여 하는 데 사용 됩니다. SPN (서비스 사용자 이름)에서 추가 기능의 종속성을 제거 하는 작업이 진행 중입니다. 결국 AKS의 SPN 요구 사항이 완전히 제거 됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인 (opt in)에서 사용할 수 있습니다. 미리 보기는 "있는 그대로", "사용 가능"으로 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력을 기반으로 고객 지원팀에서 부분적으로 검사 됩니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조 하세요.
>
> - [AKS 지원 정책](support-policies.md)
> - [Azure 지원 FAQ](faq.md)

## <a name="before-you-begin"></a>시작하기 전에

다음 리소스를 설치 해야 합니다.

- Azure CLI 버전 2.0.70 이상
- Aks-preview 0.4.14 확장

Aks-preview 0.4.14 extension 이상을 설치 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> 구독에 기능을 등록 한 후에는 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정 하면 구독에서 이후에 만들어진 모든 AKS 클러스터에 기본값이 사용 될 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 대신 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

상태를 **등록 된**것으로 표시 하는 데 몇 분 정도 걸릴 수 있습니다. [Az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

상태가 등록 됨으로 표시 되 면 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 명령을 사용 하 여 `Microsoft.ContainerService` 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>관리 id를 사용 하 여 AKS 클러스터 만들기

이제 다음 CLI 명령을 사용 하 여 관리 id를 사용 하 여 AKS 클러스터를 만들 수 있습니다.

먼저 Azure 리소스 그룹을 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

그런 다음 AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

마지막으로 클러스터에 액세스 하기 위한 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

클러스터가 몇 분 안에 생성 됩니다. 그런 다음 응용 프로그램 워크 로드를 새 클러스터에 배포 하 고 서비스 주체 기반 AKS 클러스터에서 수행한 것 처럼 상호 작용할 수 있습니다.

> [!IMPORTANT]
>
> - 관리 id를 사용 하는 AKS 클러스터는 클러스터를 만드는 동안에만 사용할 수 있습니다.
> - 관리 되는 id를 사용 하도록 기존 AKS 클러스터를 업데이트 하거나 업그레이드할 수 없습니다.
