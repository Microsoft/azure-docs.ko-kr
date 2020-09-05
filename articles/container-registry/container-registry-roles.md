---
title: Azure 역할 및 사용 권한
description: Azure RBAC (역할 기반 액세스 제어) 및 IAM (id 및 액세스 관리)을 사용 하 여 Azure container registry의 리소스에 대 한 세분화 된 사용 권한을 제공 합니다.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: b8562d3e33cd49082d4ba4d8567d5f0c816070b0
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661387"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry 역할 및 권한

Azure Container Registry 서비스는 Azure Container Registry에 대해 서로 다른 수준의 사용 권한을 제공 하는 [기본 제공 azure 역할](../role-based-access-control/built-in-roles.md) 집합을 지원 합니다. Azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/index.yml) 를 사용 하 여 사용자, 서비스 주체 또는 레지스트리와 상호 작용 해야 하는 다른 id에 특정 권한을 할당 합니다. 또한 서로 다른 작업을 위해 레지스트리를 세분화 된 권한으로 사용 하 여 [사용자 지정 역할](#custom-roles) 을 정의할 수 있습니다.

| 역할/권한       | [Resource Manager 액세스](#access-resource-manager) | [레지스트리 만들기/삭제](#create-and-delete-registry) | [이미지 푸시](#push-image) | [이미지 풀](#pull-image) | [이미지 데이터 삭제](#delete-image-data) | [정책 변경](#change-policies) |   [이미지 서명](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| 소유자 | X | X | X | X | X | X |  |  
| 참가자 | X | X | X |  X | X | X |  |  
| 판독기 | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>사용자 및 서비스 구별

권한을 적용할 때마다 작업을 수행하는 데 가장 제한적인 권한 집합을 사용자 또는 서비스에 대해 제공하는 것이 좋습니다. 다음 권한 집합은 사용자 및 헤드리스 서비스에서 사용할 수 있는 기능 세트를 나타냅니다.

### <a name="cicd-solutions"></a>CI/CD 솔루션

CI/CD 솔루션에서 `docker build` 명령을 자동화하는 경우 `docker push` 기능이 필요합니다. 이러한 헤드리스 서비스 시나리오의 경우 **AcrPush** 역할을 할당하는 것이 좋습니다. 이 역할은 보다 광범위한 **기여자** 역할과 달리 계정이 다른 레지스트리 작업을 수행하거나 Azure Resource Manager에 액세스하지 못하도록 합니다.

### <a name="container-host-nodes"></a>컨테이너 호스트 노드

마찬가지로, 컨테이너를 실행하는 노드에는 **AcrPull** 역할이 필요하지만 **읽기 권한자** 기능은 필요하지 않아야 합니다.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 확장

Visual Studio Code [Docker 확장](https://code.visualstudio.com/docs/azure/docker)과 같은 도구의 경우, 사용 가능한 Azure Container Registry를 나열하려면 추가 리소스 공급자에 액세스해야 합니다. 이 경우 **reader** 또는 **contributor** 역할에 대한 액세스 권한을 사용자에게 제공합니다. 이러한 역할은 `docker pull`, `docker push`, `az acr list`, `az acr build` 및 기타 기능을 허용합니다. 

## <a name="access-resource-manager"></a>Resource Manager 액세스

Azure Portal 및 [Azure CLI](/cli/azure/)를 통한 레지스트리 관리에는 Azure Resource Manager 액세스가 필요합니다. 예를 들어, `az acr list` 명령을 사용하여 레지스트리 목록을 가져오려면 이 권한 집합이 필요합니다. 

## <a name="create-and-delete-registry"></a>레지스트리 만들기 및 삭제

Azure Container Registry를 만들고 삭제하는 기능입니다.

## <a name="push-image"></a>푸시 이미지

이미지를 `docker push`하거나, Helm 차트와 같은 [지원되는 다른 아티팩트](container-registry-image-formats.md)를 레지스트리에 푸시하는 기능입니다. 인증된 ID를 사용하여 레지스트리에 [인증](container-registry-authentication.md)해야 합니다. 

## <a name="pull-image"></a>이미지 풀

보장되지 않은 이미지를 `docker pull`하거나, Helm 차트와 같은 [지원되는 다른 아티팩트](container-registry-image-formats.md)를 레지스트리에 풀하는 기능입니다. 인증된 ID를 사용하여 레지스트리에 [인증](container-registry-authentication.md)해야 합니다.

## <a name="delete-image-data"></a>이미지 데이터 삭제

레지스트리에서 [컨테이너 이미지를 삭제](container-registry-delete.md)하거나 [지원 되](container-registry-image-formats.md) 는 다른 아티팩트 (예: 투구 차트)를 삭제할 수 있습니다.

## <a name="change-policies"></a>정책 변경

레지스트리에 정책을 구성하는 기능입니다. 정책에는 이미지 제거, 격리 사용 및 이미지 서명이 포함됩니다.

## <a name="sign-images"></a>이미지 서명

일반적으로 자동화된 프로세스에 할당되는, 서비스 주체를 사용하는 이미지 서명 기능입니다. 일반적으로 이 권한은 [이미지 푸시](#push-image)와 결합되어 신뢰할 수 있는 이미지를 레지스트리에 푸시할 수 있도록 합니다. 자세한 내용은 [Azure Container Registry의 콘텐츠 신뢰](container-registry-content-trust.md)를 참조 하세요.

## <a name="custom-roles"></a>사용자 지정 역할

다른 Azure 리소스와 마찬가지로 Azure Container Registry 하는 세분화 된 권한으로 [사용자 지정 역할](../role-based-access-control/custom-roles.md) 을 만들 수 있습니다. 그런 다음 사용자, 서비스 주체 또는 레지스트리와 상호 작용 해야 하는 다른 id에 사용자 지정 역할을 할당 합니다. 

사용자 지정 역할에 적용할 권한을 결정 하려면 Microsoft.containerregistry [작업](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)목록을 참조 하거나, [기본 제공 ACR 역할](../role-based-access-control/built-in-roles.md)의 허용 된 작업을 검토 하거나, 다음 명령을 실행 합니다.

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

사용자 지정 역할을 정의 하려면 [사용자 지정 역할을 만드는 단계](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)를 참조 하세요.

> [!IMPORTANT]
> 사용자 지정 역할에서 Azure Container Registry는 현재와 같은 와일드 카드를 지원 하지 않으며, `Microsoft.ContainerRegistry/*` `Microsoft.ContainerRegistry/registries/*` 일치 하는 모든 동작에 대 한 액세스 권한을 부여 합니다. 역할에서 필수 작업을 개별적으로 지정 합니다.

### <a name="example-custom-role-to-import-images"></a>예제: 이미지를 가져오기 위한 사용자 지정 역할

예를 들어 다음 JSON은 레지스트리에 [이미지를 가져올](container-registry-import-images.md) 수 있도록 하는 사용자 지정 역할에 대 한 최소 작업을 정의 합니다.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

JSON 설명을 사용 하 여 사용자 지정 역할을 만들거나 업데이트 하려면 [Azure CLI](../role-based-access-control/custom-roles-cli.md), [Azure Resource Manager 템플릿](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)또는 기타 Azure 도구를 사용 합니다. 기본 제공 Azure 역할에 대 한 역할 할당을 관리 하는 것과 동일한 방식으로 사용자 지정 역할에 대 한 역할 할당을 추가 하거나 제거 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md)또는 기타 azure 도구를 사용 하 여 azure id에 azure 역할을 할당 하는 방법에 대해 자세히 알아보세요.

* Azure Container Registry의 [인증 옵션](container-registry-authentication.md)에 대해 알아봅니다.

* 컨테이너 레지스트리에서 [리포지토리 범위 권한](container-registry-repository-scoped-permissions.md) (미리 보기)을 사용 하도록 설정 하는 방법에 대해 알아봅니다.