---
title: ACR 작업에서 외부 인증
description: Azure 리소스에 관리 되는 id를 사용 하 여 Azure key vault에 저장 된 Docker 허브 자격 증명을 읽도록 Azure Container Registry 작업 (ACR 작업)을 구성 합니다.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842523"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Azure에서 관리 하는 id를 사용 하 여 ACR 작업에서 외부 인증 

[ACR 작업](container-registry-tasks-overview.md)에서 [Azure 리소스에 대해 관리 되는 id를 사용 하도록 설정할](container-registry-tasks-authentication-managed-identity.md)수 있습니다. 작업은 자격 증명을 제공 하거나 관리할 필요 없이 id를 사용 하 여 다른 Azure 리소스에 액세스할 수 있습니다. 

이 문서에서는 Azure key vault에 저장 된 암호에 액세스 하는 작업에서 관리 되는 id를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 

이 문서에서는 Azure 리소스를 만들기 위해 Azure CLI 버전 2.0.68 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="scenario-overview"></a>시나리오 개요

예제 작업은 Azure key vault에 저장 된 Docker 허브 자격 증명을 읽습니다. 자격 증명은 개인 Docker 허브 리포지토리에 대한 쓰기 (푸시) 권한이 있는 Docker 허브 계정에 대한 자격 증명입니다. 자격 증명을 읽으려면 관리 되는 id를 사용 하 여 작업을 구성 하 고 적절 한 권한을 할당 합니다. Id와 연결 된 태스크는 이미지를 빌드하고 Docker 허브에 로그인 하 여 이미지를 개인 리포지토리로 푸시합니다. 

이 예에서는 사용자 할당 또는 시스템 할당 관리 id를 사용 하는 단계를 보여 줍니다. 선택한 id는 조직의 요구 사항에 따라 달라 집니다.

실제 시나리오에서 회사는 빌드 프로세스의 일부로 Docker 허브의 개인 리포지토리에 이미지를 게시할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

작업을 실행 하는 Azure container registry가 필요 합니다. 이 문서에서이 레지스트리의 이름은 *myregistry*입니다. 을 이후 단계에서 사용자 고유의 레지스트리 이름으로 바꿉니다.

Azure container registry가 아직 없는 경우 [빠른 시작: Azure CLI을 사용 하 여 개인 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조 하세요. 아직 레지스트리에 이미지를 푸시할 필요는 없습니다.

또한 Docker 허브에 개인 리포지토리와 리포지토리에 쓸 수 있는 권한이 있는 Docker 허브 계정이 필요 합니다. 이 예에서는이 리포지토리의 이름이 *hubuser/hubrepo*입니다. 

## <a name="create-a-key-vault-and-store-secrets"></a>주요 자격 증명 모음을 만들고 비밀을 저장 합니다.

먼저 필요한 경우 다음 [az group create][az-group-create] 명령을 사용 하 여 *myresourcegroup* 이라는 이름의 리소스 그룹을 *e us* 위치에 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[Az keyvault create][az-keyvault-create] 명령을 사용 하 여 주요 자격 증명 모음을 만듭니다. 고유 키 자격 증명 모음 이름을 지정 해야 합니다. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

[Az keyvault secret set][az-keyvault-secret-set] 명령을 사용 하 여 key vault에 필요한 Docker 허브 자격 증명을 저장 합니다. 이러한 명령에서 값은 환경 변수로 전달 됩니다.

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

실제 시나리오에서 비밀은 별도의 프로세스로 설정 및 유지 관리 될 수 있습니다.

## <a name="define-task-steps-in-yaml-file"></a>YAML 파일에서 작업 단계를 정의 합니다.

이 예제 작업의 단계는 [Yaml 파일](container-registry-tasks-reference-yaml.md)에 정의 되어 있습니다. 로컬 작업 디렉터리에 `dockerhubtask.yaml` 라는 파일을 만들고 다음 내용을 붙여 넣습니다. 파일의 키 자격 증명 모음 이름을 키 자격 증명 모음의 이름으로 바꾸어야 합니다.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

작업 단계는 다음을 수행 합니다.

* Docker 허브를 사용 하 여 인증 하는 비밀 자격 증명을 관리 합니다.
* `docker login` 명령에 암호를 전달 하 여 Docker 허브를 통해 인증 합니다.
* [Azure 샘플/acr-작업](https://github.com/Azure-Samples/acr-tasks.git) 리포지토리에서 샘플 Dockerfile을 사용 하 여 이미지를 빌드합니다.
* 개인 Docker 허브 리포지토리에 이미지를 푸시합니다.


## <a name="option-1-create-task-with-user-assigned-identity"></a>옵션 1: 사용자 할당 id를 사용 하 여 작업 만들기

이 섹션의 단계에서는 작업을 만들고 사용자 할당 id를 사용 하도록 설정 합니다. 시스템 할당 id를 대신 사용 하도록 설정 하려면 [옵션 2: 시스템 할당 id를 사용 하 여 작업 만들기](#option-2-create-task-with-system-assigned-identity)를 참조 하세요. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행 하 여 *dockerhubtask* 작업을 만듭니다. 소스 코드 컨텍스트 없이 태스크가 실행 되 고 명령이 작업 디렉터리에서 `dockerhubtask.yaml` 파일을 참조 합니다. `--assign-identity` 매개 변수는 사용자 할당 id의 리소스 ID를 전달 합니다. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>옵션 2: 시스템 할당 id를 사용 하 여 작업 만들기

이 섹션의 단계에서는 작업을 만들고 시스템 할당 id를 사용 하도록 설정 합니다. 사용자 할당 id를 대신 사용 하려면 [옵션 1: 사용자 할당 id를 사용 하 여 작업 만들기](#option-1-create-task-with-user-assigned-identity)를 참조 하세요. 

### <a name="create-task"></a>작업 만들기

다음 [az acr task create][az-acr-task-create] 명령을 실행 하 여 *dockerhubtask* 작업을 만듭니다. 소스 코드 컨텍스트 없이 태스크가 실행 되 고 명령이 작업 디렉터리에서 `dockerhubtask.yaml` 파일을 참조 합니다. 값이 없는 `--assign-identity` 매개 변수는 작업에 대해 시스템 할당 id를 사용 하도록 설정 합니다.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Key vault에 id 액세스 권한 부여

다음 [az keyvault set-policy][az-keyvault-set-policy] 명령을 실행 하 여 key vault에 대한 액세스 정책을 설정 합니다. 다음 예에서는 id가 키 자격 증명 모음에서 암호를 읽을 수 있도록 허용 합니다. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>수동으로 작업 실행

관리 id를 사용 하도록 설정한 태스크가 성공적으로 실행 되는지 확인 하려면 [az acr task run][az-acr-task-run] 명령을 사용 하 여 수동으로 작업을 트리거합니다. `--set` 매개 변수는 개인 리포지토리 이름을 작업에 전달 하는 데 사용 됩니다. 이 예제에서 자리 표시자 리포지토리 이름은 *hubuser/hubrepo*입니다.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

작업이 성공적으로 실행 되 면 출력은 Docker 허브에 대한 성공적인 인증을 표시 하 고 이미지가 성공적으로 빌드되고 개인 리포지토리로 푸시됩니다.

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

이미지가 푸시 되었는지 확인 하려면 개인 Docker 허브 리포지토리에서 태그 (이 예제에서는`cf24`)를 확인 합니다.

## <a name="next-steps"></a>다음 단계

* [ACR 작업에서 관리 id를 사용 하도록 설정 하](container-registry-tasks-authentication-managed-identity.md)는 방법에 대해 자세히 알아보세요.
* [ACR 작업 YAML 참조](container-registry-tasks-reference-yaml.md) 를 참조 하세요.


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
