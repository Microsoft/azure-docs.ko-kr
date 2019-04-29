---
title: 관리 ID로 Azure Container Registry 인증
description: 사용자 할당 또는 시스템 할당 관리 Azure ID를 사용하여 개인 컨테이너 레지스트리의 이미지에 액세스할 수 있습니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: 728a2f8cf61bbe0691350b9de45a5fab6b90cadb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563072"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Azure Container Registry에 인증하기 위해 Azure 관리 ID 사용 

레지스트리 자격 증명을 제공하거나 관리할 필요 없이 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 다른 Azure 리소스의 Azure Container Registry에 인증할 수 있습니다. 예를 들어 Linux VM에서 사용자 할당 또는 시스템 할당 관리 ID를 설정하면 공용 레지스트리를 사용하는 것처럼 쉽게 컨테이너 레지스트리의 컨테이너 이미지에 액세스할 수 있습니다.

이 문서에서는 관리 ID 및 다음과 같은 방법에 대해 자세히 알아봅니다.

> [!div class="checklist"]
> * Azure VM에서 사용자 할당 또는 시스템 할당 ID를 사용하도록 설정
> * Azure Container Registry에 ID 액세스 권한 부여
> * 관리 ID를 사용하여 레지스트리에 액세스하고 컨테이너 이미지 가져오기 

Azure 리소스를 만들려면 이 문서에서는 Azure CLI 버전 2.0.55 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

컨테이너 레지스트리를 설정하고 컨테이너 이미지를 푸시하려면 Docker를 로컬로 설치해야 합니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.

## <a name="why-use-a-managed-identity"></a>관리 ID를 사용하는 이유

Azure 리소스에 대한 관리 ID는 Azure AD(Azure Active Directory)에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 관리 ID를 사용하여 가상 머신을 포함하여 [특정 Azure 리소스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 구성할 수 있습니다. 그런 다음, ID를 사용하여 코드 또는 스크립트에서 자격 증명을 전달하지 않고 다른 Azure 리소스에 액세스할 수 있습니다.

관리 ID에는 다음과 같은 두 가지 유형이 있습니다.

* *사용자 할당 ID*는 여러 리소스에 할당할 수 있으며 원하는 만큼 오랫동안 유지할 수 있습니다. 사용자 할당 ID는 현재 미리 보기 중입니다.

* *시스템 관리 ID*는 단일 가상 머신과 같은 특정 리소스에 고유하며 해당 리소스의 수명만큼 지속됩니다.

관리 ID를 사용하여 Azure 리소스를 설정한 후에 모든 보안 주체와 마찬가지로 다른 리소스에 대한 액세스 권한을 해당 ID에 제공합니다. 예를 들어 관리 ID에 Azure의 개인 레지스트리에 대한 풀, 푸시 및 풀 또는 기타 권한이 있는 역할을 할당합니다. (전체 레지스트리 역할 목록은 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.) 하나 이상의 리소스에는 ID 액세스 권한을 제공할 수 있습니다.

그런 다음, 해당 ID를 사용하면 모든 [Azure AD 인증을 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다. ID를 사용하여 가상 머신에서 Azure Container Registry에 액세스하려면 Azure Resource Manager를 사용하여 인증합니다. 시나리오에 따라 관리 ID를 사용하여 인증하는 방법을 선택합니다.

* HTTP 또는 REST 호출을 사용하여 프로그래밍 방식으로 [Azure AD 액세스 토큰 획득](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

* [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) 사용

* ID를 사용하여 [Azure CLI 또는 PowerShell에 로그인](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)합니다. 

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

Azure Container Registry가 아직 없는 경우 레지스트리를 만들어 샘플 컨테이너 이미지를 레지스트리에 푸시합니다. 자세한 단계는 [빠른 시작: Azure CLI를 사용하여 개인 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요.

이 문서에서는 레지스트리에 `aci-helloworld:v1` 컨테이너 이미지가 저장되어 있다고 가정합니다. 이 예제에서는 *myContainerRegistry*라는 레지스트리 이름을 사용합니다. 이후 단계에서 사용자 고유의 레지스트리 및 이미지 이름으로 바꿉니다.

## <a name="create-a-docker-enabled-vm"></a>Docker 사용 가능 VM 만들기

Docker 사용 가능 Ubuntu 가상 머신을 만듭니다. 또한 가상 머신에 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 설치해야 합니다. Azure 가상 머신이 이미 있는 경우 이 단계를 건너뛰고 가상 머신을 만듭니다.

[az vm create][az-vm-create]를 사용하여 기본 Ubuntu Azure 가상 머신을 배포합니다. 다음 예제에서는 기존의 *myResourceGroup* 리소스 그룹에 *myDockerVM*이라는 VM을 만듭니다.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM을 만드는 데 몇 분 정도 걸립니다. 명령이 완료되면 Azure CLI에 표시된 `publicIpAddress`를 기록해 둡니다. 이 주소를 사용하여 VM에 SSH 연결을 만듭니다.

### <a name="install-docker-on-the-vm"></a>VM에 Docker 설치

VM이 실행된 후 VM에 SSH 연결을 만듭니다. *publicIpAddress*를 VM의 공용 IP 주소로 바꿉니다.

```bash
ssh azureuser@publicIpAddress
```

다음 명령을 실행하여 VM에 Docker를 설치합니다.

```bash
sudo apt install docker.io -y
```

설치 후 다음 명령을 실행하여 VM에서 Docker가 제대로 실행되는지 확인합니다.

```bash
sudo docker run -it hello-world
```

출력:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI 설치

[apt를 사용하여 Azure CLI 설치](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)의 단계를 따라 Ubuntu 가상 머신에 Azure CLI를 설치합니다. 이 문서에서는 버전 2.0.55 이상을 설치해야 합니다.

SSH 세션을 종료합니다.

## <a name="example-1-access-with-a-user-assigned-identity"></a>예제 1: 사용자 할당 ID를 사용하여 액세스

### <a name="create-an-identity"></a>ID 만들기

[az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 명령을 사용하여 구독에서 ID를 만듭니다. 이전에 사용한 것과 동일한 리소스 그룹을 사용하여 컨테이너 레지스트리, 가상 머신 또는 다른 가상 머신을 만들 수 있습니다.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

다음 단계에서 ID를 구성하려면 [az identity show][az-identity-show] 명령을 사용하여 ID의 리소스 ID 및 서비스 주체 ID를 변수에 저장합니다.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

가상 컴퓨터에서 CLI에 로그인 할 때 이후 단계에서이 id의 ID를 필요 하기 때문에 값을 보여 줍니다.

```bash
echo $userID
```

ID의 형식은 다음과 같습니다.

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>ID를 사용하여 VM 구성

다음 [az vm identity assign][az-vm-identity-assign] 명령은 사용자 할당 ID를 사용하여 Docker VM을 구성합니다.

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>컨테이너 레지스트리에 ID 액세스 권한 부여

이제 컨테이너 레지스트리에 액세스하도록 ID를 구성합니다. 먼저 [az acr show][az-acr-show] 명령을 사용하여 레지스트리의 리소스 ID를 가져옵니다.

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] 명령을 사용하여 AcrPull 역할을 레지스트리에 할당합니다. 이 역할은 [끌어오기 권한](container-registry-roles.md)을 레지스트리에 제공합니다. 끌어오기 및 밀어넣기 권한을 모두 제공하려면 ACRPush 역할을 할당합니다.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>ID를 사용하여 레지스트리에 액세스

ID를 사용하여 구성된 Docker 가상 머신에 SSH를 수행합니다. VM에 설치된 Azure CLI를 사용하여 다음 Azure CLI 명령을 실행합니다.

먼저 Azure CLI를 사용 하 여 인증할 [az login][az-login], VM에 구성 된 id를 사용 하 여 합니다. `<userID>`의 경우 이전 단계에서 검색한 ID의 ID로 대체합니다. 

```azurecli
az login --identity --username <userID>
```

그런 다음 사용 하 여 레지스트리에 인증할 [az acr login][az-acr-login]합니다. 이 명령을 사용하는 경우 CLI는 `az login`을 실행할 때 만든 Active Directory 토큰을 사용하여 원활하게 컨테이너 레지스트리로 세션을 인증합니다. (VM의 설정에 따라 `sudo`를 사용하여 이 명령 및 docker 명령을 실행해야 합니다.)

```azurecli
az acr login --name myContainerRegistry
```

`Login succeeded` 메시지가 표시되어야 합니다. 그런 다음, 자격 증명을 제공하지 않고 `docker` 명령을 실행할 수 있습니다. 예를 들어 [docker pull][docker-pull]을 실행하여 `aci-helloworld:v1` 이미지를 가져와서 레지스트리의 로인 서버 이름을 지정합니다. 로그인 서버 이름은 컨테이너 레지스트리 이름(모두 소문자)과 뒤에 나오는 `.azurecr.io`(예: `mycontainerregistry.azurecr.io`)로 구성됩니다.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>예 2: 시스템 할당 ID를 사용하여 액세스

### <a name="configure-the-vm-with-a-system-managed-identity"></a>시스템 관리 ID를 사용하여 VM을 구성합니다.

다음 [az vm identity assign][az-vm-identity-assign] 명령은 시스템 할당 ID를 사용하여 Docker VM을 구성합니다.

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

[az vm show][az-vm-show] 명령을 사용하여 후속 단계에서 사용할 수 있도록 변수를 VM ID의 `principalId` 값(서비스 주체 ID)으로 설정합니다.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>컨테이너 레지스트리에 ID 액세스 권한 부여

이제 컨테이너 레지스트리에 액세스하도록 ID를 구성합니다. 먼저 [az acr show][az-acr-show] 명령을 사용하여 레지스트리의 리소스 ID를 가져옵니다.

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

[az role assignment create][az-role-assignment-create] 명령을 사용하여 AcrPull 역할을 ID에 할당합니다. 이 역할은 [끌어오기 권한](container-registry-roles.md)을 레지스트리에 제공합니다. 끌어오기 및 밀어넣기 권한을 모두 제공하려면 ACRPush 역할을 할당합니다.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>ID를 사용하여 레지스트리에 액세스

ID를 사용하여 구성된 Docker 가상 머신에 SSH를 수행합니다. VM에 설치된 Azure CLI를 사용하여 다음 Azure CLI 명령을 실행합니다.

먼저 Azure CLI를 사용 하 여 인증 [az login][az-login], VM에서 시스템 할당 id를 사용 하 여 합니다.

```azurecli
az login --identity
```

그런 다음 사용 하 여 레지스트리에 인증할 [az acr login][az-acr-login]합니다. 이 명령을 사용하는 경우 CLI는 `az login`을 실행할 때 만든 Active Directory 토큰을 사용하여 원활하게 컨테이너 레지스트리로 세션을 인증합니다. (VM의 설정에 따라 `sudo`를 사용하여 이 명령 및 docker 명령을 실행해야 합니다.)

```azurecli
az acr login --name myContainerRegistry
```

`Login succeeded` 메시지가 표시되어야 합니다. 그런 다음, 자격 증명을 제공하지 않고 `docker` 명령을 실행할 수 있습니다. 예를 들어 [docker pull][docker-pull]을 실행하여 `aci-helloworld:v1` 이미지를 가져와서 레지스트리의 로인 서버 이름을 지정합니다. 로그인 서버 이름은 컨테이너 레지스트리 이름(모두 소문자)과 뒤에 나오는 `.azurecr.io`(예: `mycontainerregistry.azurecr.io`)로 구성됩니다.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Container Registry에서 관리 ID 사용 및 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure VM에서 사용자 할당 또는 시스템 할당 ID를 사용하도록 설정
> * Azure Container Registry에 ID 액세스 권한 부여
> * 관리 ID를 사용하여 레지스트리에 액세스하고 컨테이너 이미지 가져오기

* [Azure 리소스에 대한 관리 ID](/azure/active-directory/managed-identities-azure-resources/)에 대해 자세히 알아보세요.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
