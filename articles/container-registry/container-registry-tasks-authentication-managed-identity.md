---
title: ACR 작업에서 관리 되는 id
description: 작업에서 다른 개인 컨테이너 레지스트리를 비롯 한 다른 Azure 리소스에 액세스할 수 있도록 Azure Container Registry 작업에서 Azure 리소스에 대해 관리 되는 id를 사용 하도록 설정 합니다.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111772"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>ACR 작업에서 Azure 관리 id 사용 

자격 증명을 제공 하거나 관리 하지 않고도 작업에서 다른 Azure 리소스에 액세스할 수 있도록 [ACR 작업](container-registry-tasks-overview.md)에서 [azure 리소스에 대해 관리 id](../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하도록 설정 합니다. 예를 들어 관리 되는 id를 사용 하 여 작업 단계에서 컨테이너 이미지를 끌어오거나 다른 레지스트리로 푸시할 수 있습니다.

이 문서에서는 Azure CLI를 사용 하 여 ACR 작업에서 사용자 할당 또는 시스템 할당 관리 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.68 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

설명을 위해이 문서의 예제 명령은 [az acr task create][az-acr-task-create] 를 사용 하 여 관리 되는 id를 사용 하도록 설정 하는 기본 이미지 빌드 작업을 만듭니다. 관리 id를 사용 하 여 ACR 작업에서 보안 된 리소스에 액세스 하는 샘플 시나리오는 다음을 참조 하세요.

* [크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault에 저장 된 암호를 사용 하 여 외부 리소스에 액세스](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

Azure 리소스에 대 한 관리 id는 Azure Active Directory에서 자동으로 관리 되는 id를 사용 하 여 선택한 Azure 서비스를 제공 합니다. 작업 단계에서 자격 증명을 전달 하지 않고도 작업에서 다른 보안 된 Azure 리소스에 액세스할 수 있도록 ACR 작업을 관리 되는 id로 구성할 수 있습니다.

관리 ID에는 다음과 같은 두 가지 유형이 있습니다.

* *사용자 할당 id*-여러 리소스에 할당 하 고 원하는 기간 동안 유지할 수 있습니다. 사용자 할당 ID는 현재 미리 보기 중입니다.

* ACR 작업과 같이 특정 리소스에 대해 고유 하 고 해당 리소스의 수명 동안 지속 되는 *시스템 할당 id*입니다.

ACR 작업에서 두 유형의 id를 모두 사용 하도록 설정할 수 있습니다. 모든 보안 주체와 마찬가지로 id에 다른 리소스에 대 한 액세스 권한을 부여 합니다. 태스크가 실행 될 때 id를 사용 하 여 액세스 해야 하는 작업 단계에서 리소스에 액세스 합니다.

## <a name="steps-to-use-a-managed-identity"></a>관리 id를 사용 하는 단계

이러한 개략적인 단계에 따라 ACR 작업에서 관리 되는 id를 사용 합니다.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (선택 사항) 사용자 할당 id 만들기

사용자 할당 id를 사용 하려는 경우 기존 id를 사용 하거나 Azure CLI 또는 다른 Azure 도구를 사용 하 여 id를 만듭니다. 예를 들어 [az identity create][az-identity-create] 명령을 사용 합니다. 

시스템 할당 id만 사용 하려는 경우이 단계를 건너뜁니다. ACR 작업을 만들 때 시스템 할당 id를 만듭니다.

### <a name="2-enable-identity-on-an-acr-task"></a>2. ACR 작업에서 id 사용

ACR 작업을 만들 때 필요에 따라 사용자 할당 id, 시스템 할당 id 또는 둘 다를 사용 하도록 설정 합니다. 예를 들어 Azure CLI에서 [az acr task create][az-acr-task-create] 명령을 실행할 때 `--assign-identity` 매개 변수를 전달 합니다.

시스템이 할당 한 id를 사용 하도록 설정 하려면 값 없이 `--assign-identity` 전달 하거나 `assign-identity [system]`합니다. 다음 예제 명령은 `hello-world` 이미지를 빌드하고 시스템 할당 관리 id를 사용 하도록 설정 하는 공용 GitHub 리포지토리에서 Linux 작업을 만듭니다.

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

사용자 할당 id를 사용 하도록 설정 하려면 id의 *리소스 ID* 값으로 `--assign-identity`를 전달 합니다. 다음 예제 명령은 `hello-world` 이미지를 빌드하고 사용자 할당 관리 id를 사용 하도록 설정 하는 공용 GitHub 리포지토리에서 Linux 작업을 만듭니다.

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

[Az identity show][az-identity-show] 명령을 실행 하 여 id의 리소스 id를 가져올 수 있습니다. 리소스 그룹 *Myresourcegroup* 의 ID *myUserAssignedIdentity* 에 대 한 리소스 id 형식은 다음과 같습니다. 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. id에 다른 Azure 리소스에 대 한 액세스 권한을 부여 합니다.

작업 요구 사항에 따라 id 권한을 부여 하 여 다른 Azure 리소스에 액세스할 수 있습니다. 다음은 이러한 템플릿의 예입니다.

* Azure의 대상 컨테이너 레지스트리에 끌어오기, 푸시 및 끌어오기 또는 기타 권한을 사용 하 여 관리 되는 id 역할을 할당 합니다. 레지스트리 역할의 전체 목록은 [Azure Container Registry 역할 및 사용 권한](container-registry-roles.md)을 참조 하세요. 
* Azure 주요 자격 증명 모음에서 암호를 읽도록 관리 되는 id를 할당 합니다.

[Azure CLI](../role-based-access-control/role-assignments-cli.md) 또는 다른 Azure 도구를 사용 하 여 리소스에 대 한 역할 기반 액세스를 관리 합니다. 예를 들어 [az role assign create][az-role-assignment-create] 명령을 실행 하 여 리소스에 id 역할을 할당 합니다. 

다음 예제에서는 관리 되는 id를 컨테이너 레지스트리에서 끌어올 수 있는 권한으로 할당 합니다. 이 명령은 작업 id의 *보안 주체 id* 와 대상 레지스트리의 *리소스 id* 를 지정 합니다.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (선택 사항) 작업에 자격 증명 추가

작업에 다른 사용자 지정 레지스트리로 이미지를 끌어오거나 푸시하는 자격 증명이 필요 하거나 다른 리소스에 액세스 하려면 작업에 자격 증명을 추가 합니다. [Az acr task credential add][az-acr-task-credential-add] 명령을 실행 하 여 자격 증명을 추가 하 고, `--use-identity` 매개 변수를 전달 하 여 id가 자격 증명에 액세스할 수 있음을 표시 합니다. 

예를 들어, 시스템 할당 id에 대 한 자격 증명을 추가 하 여 Azure container registry *targetregistry*로 인증 하려면 `use-identity [system]`를 전달 합니다.

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

사용자 할당 id에 대 한 자격 증명을 추가 하 여 레지스트리 *targetregistry*로 인증 하려면 id의 *클라이언트 id* 값으로 `use-identity`를 전달 합니다. 다음은 그 예입니다.

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

[Az identity show][az-identity-show] 명령을 실행 하 여 id의 클라이언트 id를 가져올 수 있습니다. 클라이언트 ID는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`형식의 GUID입니다.

### <a name="5-run-the-task"></a>5. 작업 실행

관리 id를 사용 하 여 작업을 구성한 후 작업을 실행 합니다. 예를 들어이 문서에서 만든 작업 중 하나를 테스트 하려면 [az acr task run][az-acr-task-run] 명령을 사용 하 여 수동으로 트리거합니다. 자동화 된 작업 트리거를 추가로 구성한 경우 태스크가 자동으로 트리거될 때 실행 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 ACR 작업에서 사용자 할당 또는 시스템 할당 관리 id를 사용 하도록 설정 하 고 사용 하는 방법을 배웠습니다. 관리 id를 사용 하 여 ACR 작업에서 보안 된 리소스에 액세스 하는 시나리오는 다음을 참조 하세요.

* [크로스 레지스트리 인증](container-registry-tasks-cross-registry-authentication.md)
* [Azure Key Vault에 저장 된 암호를 사용 하 여 외부 리소스에 액세스](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
