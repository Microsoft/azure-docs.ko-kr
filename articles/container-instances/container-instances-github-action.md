---
title: GitHub 작업으로 컨테이너 인스턴스 배포
description: 컨테이너 이미지를 빌드, 푸시 및 배포 하기 위한 단계를 자동화 하는 GitHub 작업을 구성 하 여 Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: ''
ms.openlocfilehash: 8da72d3911797e8e3a4551f2af100afb0d7ea0fb
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755010"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>GitHub 작업을 구성하여 컨테이너 인스턴스 만들기

[Github 작업](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) 은 코드를 저장 하 고 끌어오기 요청 및 문제에 대해 공동으로 작업 하는 것과 동일한 장소에서 소프트웨어 개발 워크플로를 자동화 하는 github의 기능 모음입니다.

[Azure Container Instances에 배포를](https://github.com/azure/aci-deploy) 사용 하 여 Azure Container Instances에 대 한 단일 컨테이너의 배포를 자동화 합니다. 이 작업을 사용 하면 [az container create][az-container-create] 명령에 있는 것과 유사한 컨테이너 인스턴스의 속성을 설정할 수 있습니다.

이 문서에서는 다음 작업을 수행 하는 GitHub 리포지토리에서 워크플로를 설정 하는 방법을 보여 줍니다.

* Dockerfile에서 이미지 빌드
* Azure container registry에 이미지 푸시
* Azure container instance에 컨테이너 이미지 배포

이 문서에서는 워크플로를 설정 하는 두 가지 방법을 보여 줍니다.

* [Github 워크플로 구성](#configure-github-workflow) -배포 Azure Container Instances 작업 및 기타 작업을 사용 하 여 github 리포지토리에서 워크플로를 만듭니다.  
* [CLI 확장 사용](#use-deploy-to-azure-extension) - `az container app up` Azure CLI의 [Azure에 배포](https://github.com/Azure/deploy-to-azure-cli-extension) 확장에서 명령을 사용 합니다. 이 명령은 GitHub 워크플로 및 배포 단계 만들기를 간소화 합니다.

> [!IMPORTANT]
> Azure Container Instances에 대 한 GitHub 동작은 현재 미리 보기 상태입니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* **GitHub 계정** -아직 계정이 없는 경우에 계정을 만듭니다 https://github.com .
* **Azure CLI** -Azure CLI의 Azure Cloud Shell 또는 로컬 설치를 사용 하 여 Azure CLI 단계를 완료할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.
* **Azure container registry** -없는 경우 [Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)또는 기타 방법을 사용 하 여 기본 계층에서 azure container registry를 만듭니다. GitHub 워크플로에 사용 되는 배포에 사용 되는 리소스 그룹을 기록해 둡니다.

## <a name="set-up-repo"></a>리포지토리 설정

* 이 문서의 예제에서는 GitHub를 사용 하 여 다음 리포지토리를 포크 합니다. https://github.com/Azure-Samples/acr-build-helloworld-node

  이 리포지토리에는 작은 웹 앱의 컨테이너 이미지를 만드는 Dockerfile 및 원본 파일이 포함 되어 있습니다.

  ![GitHub에 있는 포크 단추(강조 표시됨)의 스크린샷](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* 리포지토리에 대해 작업을 사용할 수 있는지 확인 합니다. 분기 리포지토리로 이동 하 고 **설정**  >  **작업**을 선택 합니다. **작업 사용 권한**에서 **이 리포지토리에 대해 로컬 및 타사 작업 사용** 이 선택 되어 있는지 확인 합니다.

## <a name="configure-github-workflow"></a>GitHub 워크플로 구성

### <a name="create-service-principal-for-azure-authentication"></a>Azure 인증에 대 한 서비스 주체 만들기

GitHub 워크플로에서 Azure CLI에 인증 하려면 Azure 자격 증명을 제공 해야 합니다. 다음 예제에서는 컨테이너 레지스트리의 리소스 그룹으로 범위가 지정 된 참가자 역할의 서비스 주체를 만듭니다.

먼저 리소스 그룹의 리소스 ID를 가져옵니다. 다음 [az group show][az-group-show] 명령에 있는 그룹의 이름을 대체 합니다.

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

[Az ad sp create-rbac][az-ad-sp-create-for-rbac] 를 사용 하 여 서비스 주체를 만듭니다.

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

출력은 다음과 비슷합니다.

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

이후 단계에서 사용 되므로 JSON 출력을 저장 합니다. 또한 `clientId` 다음 섹션에서 서비스 주체를 업데이트 해야 하는을 기록해 둡니다.

### <a name="update-service-principal-for-registry-authentication"></a>레지스트리 인증에 대 한 서비스 주체 업데이트

컨테이너 레지스트리에 대 한 푸시 및 끌어오기 액세스를 허용 하도록 Azure 서비스 주체 자격 증명을 업데이트 합니다. 이 단계를 통해 GitHub 워크플로는 서비스 주체를 사용 하 여 [컨테이너 레지스트리를 인증](../container-registry/container-registry-auth-service-principal.md) 하 고 Docker 이미지를 푸시 및 끌어올 수 있습니다. 

컨테이너 레지스트리의 리소스 ID를 가져옵니다. 다음 [az acr show][az-acr-show] 명령에서 레지스트리 이름을 대체 합니다.

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

[Az role assign create][az-role-assignment-create] 를 사용 하 여 레지스트리에 대 한 푸시 및 끌어오기 액세스를 제공 하는 AcrPush 역할을 할당 합니다. 서비스 사용자의 클라이언트 ID로 대체 합니다.

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>GitHub 리포지토리에 자격 증명 저장

1. GitHub UI에서 분기 리포지토리로 이동 하 고 **설정**  >  **암호**를 선택 합니다. 

1. **새 비밀 추가** 를 선택 하 여 다음 비밀을 추가 합니다.

|비밀  |값  |
|---------|---------|
|`AZURE_CREDENTIALS`     | 서비스 사용자 만들기 단계의 전체 JSON 출력 |
|`REGISTRY_LOGIN_SERVER`   | 레지스트리의 로그인 서버 이름 (모두 소문자)입니다. 예: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  `clientId`서비스 사용자가 만든 JSON 출력의       |
|`REGISTRY_PASSWORD`     |  `clientSecret`서비스 사용자가 만든 JSON 출력의 |
| `RESOURCE_GROUP` | 서비스 주체의 범위를 표시 하는 데 사용한 리소스 그룹의 이름입니다. |

### <a name="create-workflow-file"></a>워크플로 파일 만들기

1. GitHub UI에서 **작업**  >  **새 워크플로**를 선택 합니다.
1. **직접 워크플로 설정**을 선택 합니다.
1. **새 파일 편집**에서 다음 yaml 콘텐츠를 붙여넣어 샘플 코드를 덮어씁니다. 기본 파일 이름을 그대로 적용 `main.yml` 하거나 선택한 파일 이름을 제공 합니다.
1. 커밋 **시작**을 선택 하 고 필요에 따라 커밋에 대 한 간단 하 고 확장 된 설명을 제공 하 고 **새 파일 커밋**을 선택 합니다.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>워크플로 유효성 검사

워크플로 파일을 커밋한 후 워크플로가 트리거됩니다. 워크플로 진행률을 검토 하려면 **작업**  >  **워크플로**로 이동 합니다. 

![워크플로 진행률 보기](./media/container-instances-github-action/github-action-progress.png)

워크플로의 각 단계에 대 한 상태 및 결과를 보는 방법에 대 한 자세한 내용은 [워크플로 실행 관리](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) 를 참조 하세요. 워크플로가 완료 되지 않으면 [로그 보기를 참조 하 여 오류를 진단](https://docs.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run#viewing-logs-to-diagnose-failures)합니다.

워크플로가 성공적으로 완료 되 면 [az container show][az-container-show] 명령을 실행 하 여 *aci-sampleapp.exe* 라는 컨테이너 인스턴스에 대 한 정보를 가져옵니다. 리소스 그룹의 이름으로 대체 합니다. 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

출력은 다음과 비슷합니다.

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

인스턴스가 프로 비전 된 후 브라우저에서 컨테이너의 FQDN으로 이동 하 여 실행 중인 웹 앱을 확인 합니다.

![브라우저에서 웹 앱 실행](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Azure 확장에 배포 사용

또는 Azure CLI에서 [Azure 확장에 배포](https://github.com/Azure/deploy-to-azure-cli-extension) 를 사용 하 여 워크플로를 구성 합니다. `az container app up`확장의 명령은 Azure Container Instances에 배포할 워크플로를 설정 하는 입력 매개 변수를 사용 합니다. 

Azure CLI에서 만든 워크플로는 [GitHub를 사용 하 여 수동으로 만들](#configure-github-workflow)수 있는 워크플로와 비슷합니다.

### <a name="additional-prerequisite"></a>추가 필수 구성 요소

이 시나리오의 [필수 구성 요소](#prerequisites) 및 [리포지토리 설치](#set-up-repo) 외에도 Azure CLI을 위해  **Azure 확장에 배포** 를 설치 해야 합니다.

[Az extension add][az-extension-add] 명령을 실행 하 여 확장을 설치 합니다.

```azurecli
az extension add \
  --name deploy-to-azure
```

확장을 찾고, 설치 하 고, 관리 하는 방법에 대 한 자세한 내용은 [Azure CLI에 확장 사용](/cli/azure/azure-cli-extensions-overview)을 참조 하세요.

### <a name="run-az-container-app-up"></a>`az container app up`을 실행합니다.

[Az container app up][az-container-app-up] 명령을 실행 하려면 최소한 다음을 제공 합니다.

* Azure container registry의 이름 (예: *myregistry* )
* GitHub 리포지토리의 URL (예:) `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

명령 샘플:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>명령 진행률

* 메시지가 표시 되 면 github 자격 증명을 제공 하거나 github 계정으로 인증 하는 데 사용 되는 *리포지토리* 및 *사용자* 범위를 포함 하는 github PAT ( [개인용 액세스 토큰](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) )를 제공 합니다. GitHub 자격 증명을 제공 하는 경우이 명령은 사용자를 위해 PAT를 만듭니다. 추가 프롬프트에 따라 워크플로를 구성 합니다.

* 이 명령은 워크플로에 대 한 리포지토리 비밀을 만듭니다.

  * Azure CLI에 대 한 서비스 주체 자격 증명
  * Azure container registry에 액세스 하기 위한 자격 증명

* 명령이 워크플로 파일을 리포지토리에 커밋한 후 워크플로가 트리거됩니다. 

출력은 다음과 비슷합니다.

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

GitHub UI의 각 단계에 대 한 워크플로 상태와 결과를 보려면 [워크플로 실행 관리](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run)를 참조 하세요.

### <a name="validate-workflow"></a>워크플로 유효성 검사

워크플로는 GitHub 리포지토리의 기본 이름 (이 경우 *acr-build-helloworld-노드*)을 사용 하 여 Azure container instance를 배포 합니다. 워크플로가 성공적으로 완료 되 면 [az container show][az-container-show] 명령을 실행 하 여 *acr-build-helloworld-node* 라는 컨테이너 인스턴스에 대 한 정보를 가져옵니다. 리소스 그룹의 이름으로 대체 합니다. 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

출력은 다음과 비슷합니다.

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

인스턴스가 프로 비전 된 후 브라우저에서 컨테이너의 FQDN으로 이동 하 여 실행 중인 웹 앱을 확인 합니다.

## <a name="clean-up-resources"></a>리소스 정리

[az container delete][az-container-delete] 명령을 사용하여 컨테이너 인스턴스를 중지합니다.

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

리소스 그룹 및 해당 리소스 그룹의 모든 리소스를 삭제 하려면 [az group delete][az-group-delete] 명령을 실행 합니다.

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>다음 단계

개발 워크플로를 자동화 하는 추가 작업은 [GitHub Marketplace](https://github.com/marketplace?type=actions) 에서 찾아볼 수 있습니다.


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
