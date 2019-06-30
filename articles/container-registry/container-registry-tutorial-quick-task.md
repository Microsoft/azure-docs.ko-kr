---
title: 자습서 - 클라우드에 컨테이너 이미지 빌드 - Azure Container Registry 작업
description: 이 자습서에서는 ACR 작업(Azure Container Registry 작업)을 사용하여 Azure에서 Docker 컨테이너 이미지를 빌드한 다음, Azure Container Instances에 배포하는 방법을 알아봅니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: ed5df09d492bbf6123e76f73717a1738a23a066c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "66152113"
---
# <a name="tutorial-build-and-deploy-container-images-in-the-cloud-with-azure-container-registry-tasks"></a>자습서: Azure Container Registry 작업을 사용하여 클라우드에 컨테이너 이미지 빌드 및 배포

**ACR 작업**은 Azure에서 간편하고 효율적인 Docker 컨테이너 이미지 빌드를 제공하는 Azure Container Registry 내의 기능 모음입니다. 이 문서에서는 ACR 작업의 *빠른 작업* 기능을 사용하는 방법에 대해 알아봅니다.

"내부 루프" 개발 주기는 소스 제어를 커밋하기 전에 애플리케이션 코드를 작성, 빌드 및 테스트하는 반복적인 프로세스입니다. 빠른 작업은 내부 루프를 클라우드로 확장하여 빌드 성공 유효성 검사 및 성공적으로 빌드된 이미지를 컨테이너 레지스트리에 자동으로 푸시하는 기능을 제공합니다. 사용자의 이미지는 기본적으로 레지스트리에 가까운 클라우드에 빌드되므로 더 빠르게 배포할 수 있습니다.

모든 Dockerfile 전문 지식은 ACR 작업으로 직접 전송할 수 있습니다. Dockerfile을 변경하여 ACR 작업을 통해 클라우드에 빌드할 필요 없이 실행할 명령만 변경하면 됩니다. 

이 자습서는 시리즈의 1부입니다.

> [!div class="checklist"]
> * 샘플 애플리케이션 소스 코드 가져오기
> * Azure에서 컨테이너 이미지 빌드
> * Azure Container Instances에 컨테이너 배포

후속 자습서에서는 코드 커밋 및 기본 이미지 업데이트 시 자동화된 컨테이너 이미지 빌드에 ACR 작업을 사용하는 방법에 대해 알아봅니다. 또한 ACR 작업은 [다단계 작업](container-registry-tasks-multi-step.md)을 실행하고, YAML 파일을 사용하여 여러 컨테이너를 빌드하고, 푸시하고, 필요에 따라 테스트하는 단계를 정의할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure CLI를 로컬로 사용하려면 Azure CLI 버전 **2.0.46** 이상이 설치되어 있고 [az login][az-login]을 사용하여 로그인해야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

## <a name="prerequisites"></a>필수 조건

### <a name="github-account"></a>GitHub 계정

아직 계정이 없는 경우  https://github.com 에서 계정을 만듭니다. 이 자습서 시리즈에서는 GitHub 리포지토리를 사용하여 ACR 작업에서 자동화된 이미지 빌드를 보여 줍니다.

### <a name="fork-sample-repository"></a>샘플 리포지토리 포크

다음으로, GitHub UI를 사용하여 샘플 리포지토리를 GitHub 계정에 포크합니다. 이 자습서에서는 리포지토리의 원본에서 컨테이너 이미지를 빌드하고, 다음 자습서에서는 커밋을 리포지토리의 포크에 푸시하여 자동화된 작업을 시작합니다.

[https://github.com/Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node ) 리포지토리를 포크합니다.

![GitHub에 있는 포크 단추(강조 표시됨)의 스크린샷][quick-build-01-fork]

### <a name="clone-your-fork"></a>포크 복제

리포지토리가 포크되면 포크를 복제하고 로컬 복제가 포함된 디렉터리를 입력합니다.

`git`을 사용하여 리포지토리를 복제하고, **\<your-github-username\>** 을 GitHub 사용자 이름으로 바꿉니다.

```azurecli-interactive
git clone https://github.com/<your-github-username>/acr-build-helloworld-node
```

소스 코드가 포함된 디렉터리를 입력합니다.

```azurecli-interactive
cd acr-build-helloworld-node
```

### <a name="bash-shell"></a>Bash 셸

이 자습서 시리즈의 명령은 Bash 셸 형식으로 지정되어 있습니다. PowerShell, 명령 프롬프트 또는 다른 셸을 사용하려면 줄 연속 및 환경 변수 형식을 적절하게 조정해야 할 수도 있습니다.

## <a name="build-in-azure-with-acr-tasks"></a>Azure에서 ACR 작업을 사용하여 빌드

이제 소스 코드를 머신으로 가져왔으므로 다음 단계에 따라 컨테이너 레지스트리를 만들고, ACR 작업을 사용하여 컨테이너 이미지를 빌드합니다.

이 시리즈의 자습서에서는 샘플 명령을 더 쉽게 실행하기 위해 셸 환경 변수를 사용합니다. 다음 명령을 실행하여 `ACR_NAME` 변수를 설정합니다. **\<registry-name\>** 을 새 컨테이너 레지스트리에 대한 고유한 이름으로 바꿉니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 자습서에서 만드는 다른 리소스는 이 이름을 기반으로 하므로 이 첫 번째 변수만 수정해야 합니다.

```azurecli-interactive
ACR_NAME=<registry-name>
```

컨테이너 레지스트리 환경 변수가 채워지면 이제 값을 편집하지 않고도 자습서의 나머지 명령을 복사하여 붙여넣을 수 있습니다. 다음 명령을 실행하여 리소스 그룹 및 컨테이너 레지스트리를 만듭니다.

```azurecli-interactive
RES_GROUP=$ACR_NAME # Resource Group name

az group create --resource-group $RES_GROUP --location eastus
az acr create --resource-group $RES_GROUP --name $ACR_NAME --sku Standard --location eastus
```

이제 레지스트리가 있으므로 ACR 작업을 사용하여 코드 샘플에서 컨테이너 이미지를 빌드합니다. [az acr build][az-acr-build] 명령을 실행하여 *빠른 작업*을 수행합니다.

```azurecli-interactive
az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
```

[az acr build][az-acr-build] 명령의 출력은 다음과 비슷합니다. Azure에 대한 소스 코드("context") 업로드 및 ACR 작업이 클라우드에서 실행하는 `docker build` 작업의 세부 정보를 확인할 수 있습니다. ACR 작업에서 `docker build`를 사용하여 이미지를 빌드하므로 ACR 작업을 즉시 사용하기 위해 Dockerfile을 변경할 필요가 없습니다.

```console
$ az acr build --registry $ACR_NAME --image helloacrtasks:v1 .
Packing source code into tar file to upload...
Sending build context (4.813 KiB) to ACR...
Queued a build with build ID: da1
Waiting for build agent...
2018/08/22 18:31:42 Using acb_vol_01185991-be5f-42f0-9403-a36bb997ff35 as the home volume
2018/08/22 18:31:42 Setting up Docker configuration...
2018/08/22 18:31:43 Successfully set up Docker configuration
2018/08/22 18:31:43 Logging in to registry: myregistry.azurecr.io
2018/08/22 18:31:55 Successfully logged in
Sending build context to Docker daemon   21.5kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 88087d7e709a
Step 3/5 : RUN cd /src && npm install
 ---> Running in e80e1263ce9a
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.1s
Removing intermediate container e80e1263ce9a
 ---> 26aac291c02e
Step 4/5 : EXPOSE 80
 ---> Running in 318fb4c124ac
Removing intermediate container 318fb4c124ac
 ---> 113e157d0d5a
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in fe7027a11787
Removing intermediate container fe7027a11787
 ---> 20a27b90eb29
Successfully built 20a27b90eb29
Successfully tagged myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:11 Pushing image: myregistry.azurecr.io/helloacrtasks:v1, attempt 1
The push refers to repository [myregistry.azurecr.io/helloacrtasks]
6428a18b7034: Preparing
c44b9827df52: Preparing
172ed8ca5e43: Preparing
8c9992f4e5dd: Preparing
8dfad2055603: Preparing
c44b9827df52: Pushed
172ed8ca5e43: Pushed
8dfad2055603: Pushed
6428a18b7034: Pushed
8c9992f4e5dd: Pushed
v1: digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1 size: 1366
2018/08/22 18:32:43 Successfully pushed image: myregistry.azurecr.io/helloacrtasks:v1
2018/08/22 18:32:43 Step ID acb_step_0 marked as successful (elapsed time in seconds: 15.648945)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloacrtasks
    tag: v1
    digest: sha256:b038dcaa72b2889f56deaff7fa675f58c7c666041584f706c783a3958c4ac8d1
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git: {}

Run ID: da1 was successful after 1m9.970148252s
```

출력이 거의 완료될 때 ACR 작업에서 이미지에 대해 검색된 종속성이 표시됩니다. 이렇게 하면 기본 이미지가 OS 또는 프레임워크 패치로 업데이트되는 경우와 같이 ACR 작업에서 기본 이미지 업데이트 시 이미지 빌드를 자동화할 수 있습니다. 이 자습서 시리즈의 뒷부분에서 기본 이미지 업데이트에 대한 ACR 작업 지원에 대해 알아봅니다.

## <a name="deploy-to-azure-container-instances"></a>Azure Container Instances에 배포

ACR 작업은 기본적으로 성공적으로 빌드된 이미지를 레지스트리에 자동으로 푸시하여 레지스트리에서 즉시 배포할 수 있도록 합니다.

이 섹션에서는 Azure Key Vault와 서비스 주체를 만든 다음, 서비스 주체의 자격 증명을 사용하여 ACI(Azure Container Instance)에 컨테이너를 배포합니다.

### <a name="configure-registry-authentication"></a>레지스트리 인증 구성

모든 프로덕션 시나리오에서는 [서비스 주체][service-principal-auth]를 사용하여 Azure 컨테이너 레지스트리에 액세스해야 합니다. 서비스 주체를 통해 컨테이너 이미지에 대해 역할 기반 액세스 제어를 제공할 수 있습니다. 예를 들어, 레지스트리에 대해 끌어오기 전용 액세스 권한을 가지는 서비스 주체를 구성할 수 있습니다.

#### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[Azure Key Vault](/azure/key-vault/)에 자격 증명 모음이 아직 없는 경우 Azure CLI에서 다음 명령을 사용하여 만듭니다.

```azurecli-interactive
AKV_NAME=$ACR_NAME-vault

az keyvault create --resource-group $RES_GROUP --name $AKV_NAME
```

#### <a name="create-a-service-principal-and-store-credentials"></a>서비스 주체 만들기 및 자격 증명 저장

이제 서비스 주체를 만들고 해당 자격 증명을 주요 자격 증명 모음에 저장해야 합니다.

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 명령을 사용하여 서비스 주체를 만들고, [az keyvault secret set][az-keyvault-secret-set]를 사용하여 서비스 주체의 **암호**를 자격 증명 모음에 저장합니다.

```azurecli-interactive
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

이전 명령의 `--role` 인수는 *acrpull* 역할을 사용하여 서비스 주체를 구성하고, 레지스트리에 대해 끌어오기 전용 액세스 권한을 부여합니다. 밀어넣기 및 끌어오기 액세스 권한을 모두 부여하려면 `--role` 인수를 *acrpush*로 변경합니다.

다음으로, 인증을 위해 Azure Container Registry에 전달하는 **username**인 서비스 주체의 *appId*를 자격 증명 모음에 저장합니다.

```azurecli-interactive
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Azure Key Vault을 만들고 다음 두 암호를 저장했습니다.

* `$ACR_NAME-pull-usr`: **username** 컨테이너 레지스트리로 사용할 서비스 주체 ID입니다.
* `$ACR_NAME-pull-pwd`: **password** 컨테이너 레지스트리로 사용할 서비스 주체 암호입니다.

이제 사용자나 애플리케이션 및 서비스가 레지스트리에서 이미지를 끌어올 때 이러한 암호를 이름으로 참조할 수 있습니다.

### <a name="deploy-a-container-with-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

서비스 주체 자격 증명이 Azure Key Vault 비밀로 저장되므로, 애플리케이션 및 서비스에서 해당 자격 증명을 사용하여 프라이빗 레지스트리에 액세스할 수 있습니다.

다음 [az container create][az-container-create] 명령을 실행하여 컨테이너 인스턴스를 배포합니다. 이 명령은 Azure Key Vault에 저장된 서비스 주체의 자격 증명을 사용하여 컨테이너 레지스트리를 인증합니다.

```azurecli-interactive
az container create \
    --resource-group $RES_GROUP \
    --name acr-tasks \
    --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label acr-tasks-$ACR_NAME \
    --query "{FQDN:ipAddress.fqdn}" \
    --output table
```

`--dns-name-label` 값은 Azure 내에서 고유해야 하므로, 이전 명령은 컨테이너 레지스트리의 이름을 컨테이너의 DNS 이름 레이블에 추가합니다. 명령의 출력에는 컨테이너의 FQDN(정규화된 도메인 이름)이 표시됩니다. 예를 들면 다음과 같습니다.

```console
$ az container create \
>     --resource-group $RES_GROUP \
>     --name acr-tasks \
>     --image $ACR_NAME.azurecr.io/helloacrtasks:v1 \
>     --registry-login-server $ACR_NAME.azurecr.io \
>     --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-usr --query value -o tsv) \
>     --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name $ACR_NAME-pull-pwd --query value -o tsv) \
>     --dns-name-label acr-tasks-$ACR_NAME \
>     --query "{FQDN:ipAddress.fqdn}" \
>     --output table
FQDN
----------------------------------------------
acr-tasks-myregistry.eastus.azurecontainer.io
```

다음 섹션에서 사용하기 위해 컨테이너의 FQDN을 적어 둡니다.

### <a name="verify-the-deployment"></a>배포 확인

컨테이너의 시작 프로세스를 보려면 [az container attach][az-container-attach] 명령을 사용합니다.

```azurecli-interactive
az container attach --resource-group $RES_GROUP --name acr-tasks
```

`az container attach` 출력은 먼저 이미지를 가져와서 시작할 때 컨테이너의 상태를 표시한 다음, 로컬 콘솔의 STDOUT 및 STDERR을 컨테이너의 STDOUT 및 STDERR에 바인딩합니다.

```console
$ az container attach --resource-group $RES_GROUP --name acr-tasks
Container 'acr-tasks' is in state 'Running'...
(count: 1) (last timestamp: 2018-08-22 18:39:10+00:00) pulling image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:15+00:00) Successfully pulled image "myregistry.azurecr.io/helloacrtasks:v1"
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Created container
(count: 1) (last timestamp: 2018-08-22 18:39:17+00:00) Started container

Start streaming logs:
Server running at http://localhost:80
```

`Server running at http://localhost:80`이 표시되면 브라우저에서 컨테이너의 FQDN으로 이동하여 실행 중인 애플리케이션을 확인합니다. FQDN은 이전 섹션에서 실행한 `az container create` 명령의 출력에 표시되었어야 합니다.

![브라우저에서 렌더링된 샘플 애플리케이션의 스크린샷][quick-build-02-browser]

콘솔에서 콘솔을 분리하려면 `Control+C`를 누릅니다.

## <a name="clean-up-resources"></a>리소스 정리

[az container delete][az-container-delete] 명령을 사용하여 컨테이너 인스턴스를 중지합니다.

```azurecli-interactive
az container delete --resource-group $RES_GROUP --name acr-tasks
```

컨테이너 레지스트리, 키 자격 증명 모음 및 서비스 주체를 포함하여 이 자습서에서 만든 *모든* 리소스를 제거하려면 다음 명령을 실행합니다. 그러나 이러한 리소스는 시리즈의 [다음 자습서](container-registry-tutorial-build-task.md)에서 사용되므로 다음 자습서로 바로 이동하는 경우 유지해야 할 수도 있습니다.

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>다음 단계

이제 빠른 작업을 통해 내부 루프를 테스트했으므로 소스 코드를 Git 리포지토리에 커밋할 때 컨테이너 이미지 빌드를 트리거하도록 **빌드 작업**을 구성합니다.

> [!div class="nextstepaction"]
> [작업을 사용하여 자동 빌드 트리거](container-registry-tutorial-build-task.md)

<!-- LINKS - External -->
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-keyvault-create]: /cli/azure/keyvault/secret#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-login]: /cli/azure/reference-index#az-login
[service-principal-auth]: container-registry-auth-service-principal.md

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
