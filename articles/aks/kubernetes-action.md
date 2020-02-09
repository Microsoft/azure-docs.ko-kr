---
title: GitHub 작업을 사용 하 여 Azure Kubernetes Service에 컨테이너 빌드, 테스트 및 배포
description: GitHub 작업을 사용 하 여 컨테이너를 Kubernetes에 배포 하는 방법을 알아봅니다.
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: 62fcdf01250728cf84726db7e9b39452a4d4e5ff
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046355"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes service에 배포 하는 GitHub 작업

[GitHub 작업](https://help.github.com/en/articles/about-github-actions) 을 통해 자동화 된 소프트웨어 개발 수명 주기 워크플로를 유연 하 게 빌드할 수 있습니다. Kubernetes 작업 [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) Azure Kubernetes Service 클러스터에 쉽게 배포할 수 있습니다. 작업은 [azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master), [azure/k8s](https://github.com/Azure/k8s-create-secret/tree/master) 등의 다른 작업에서 사용할 수 있는 대상 AKS 클러스터 컨텍스트를 설정 하거나 kubectl 명령을 실행 합니다.

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML (.yml) 파일에 의해 정의 됩니다. 이 정의는 워크플로를 구성 하는 다양 한 단계와 매개 변수를 포함 합니다.

AKS를 대상으로 하는 워크플로의 경우 파일에는 다음 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | ACR (개인 컨테이너 레지스트리)에 로그인 합니다. |
|**빌드** | 컨테이너 이미지를 빌드 & 푸시합니다.  |
|**배포** | 1. 대상 AKS 클러스터 설정 |
| |2. Kubernetes 클러스터에서 generic/docker 레지스트리 암호 만들기  |
||3. Kubernetes 클러스터에 배포|

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](https://docs.microsoft.com/cli/azure/)에서 [az ad sp create-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) 를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/) 를 사용 하거나 **사용해 보기** 단추를 선택 하 여이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

위의 명령에서 자리 표시자를 구독 ID 및 리소스 그룹으로 바꿉니다. 출력은 리소스에 대한 액세스를 제공 하는 역할 할당 자격 증명입니다. 명령은 다음과 유사한 JSON 개체를 출력 해야 합니다.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
GitHub에서 인증 하는 데 사용할 수 있는이 JSON 개체를 복사 합니다.

## <a name="configure-the-github-secrets"></a>GitHub 암호 구성

암호를 구성 하는 단계를 수행 합니다.

1. [GitHub](https://github.com/)에서 리포지토리로 이동 하 고, **설정 > 비밀 > 선택 하 여 새 비밀을 추가**합니다.

    ![secrets](media/kubernetes-action/secrets.png)

2. 위의 `az cli` 명령의 내용을 비밀 변수 값으로 붙여넣습니다. `AZURE_CREDENTIALS`)을 입력합니다.

3. 마찬가지로 컨테이너 레지스트리 자격 증명에 대해 다음과 같은 추가 암호를 정의 하 고 Docker 로그인 작업에서 설정 합니다. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 정의 되 면 아래와 같이 비밀이 표시 됩니다.

    ![kubernetes-비밀](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>컨테이너 이미지를 빌드하고 Azure Kubernetes Service 클러스터에 배포

컨테이너 이미지의 빌드 및 푸시는 `Azure/docker-login@v1` 작업을 사용 하 여 수행 됩니다. 컨테이너 이미지를 AKS에 배포 하려면 `Azure/k8s-deploy@v1` 작업을 사용 해야 합니다. 이 작업에는 5 개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **공간** | 필드 대상 Kubernetes 네임 스페이스를 선택 합니다. 네임 스페이스를 제공 하지 않으면 명령은 기본 네임 스페이스에서 실행 됩니다. | 
| **매니페스트** |  하다 배포에 사용 되는 매니페스트 파일의 경로 |
| **images** | 필드 매니페스트 파일에서 대체 하는 데 사용할 이미지의 정규화 된 리소스 URL입니다. |
| **imagepullsecrets** | 필드 클러스터 내에서 이미 설정 된 docker 레지스트리 암호의 이름입니다. 이러한 각 암호 이름은 입력 매니페스트 파일에 있는 작업에 대한 imagePullSecrets 필드 아래에 추가 됩니다. |
| **kubectl-버전** | 필드 Kubectl binary의 특정 버전을 설치 합니다. |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터에 배포

컨테이너 이미지를 빌드하고 Azure Kubernetes Service 클러스터에 배포 하기 위한 종단 간 워크플로입니다.

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>다음 단계

GitHub에서 다양 한 리포지토리의 작업 집합을 찾을 수 있습니다. 여기에는 CI/CD 용 GitHub를 사용 하 고 Azure에 앱을 배포 하는 데 도움이 되는 설명서와 예제가 포함 되어 있습니다.

- [설정-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s](https://github.com/Azure/k8s-set-context)

- [aks](https://github.com/Azure/aks-set-context)

- [k8s-암호](https://github.com/Azure/k8s-create-secret)

- [k8s-배포](https://github.com/Azure/k8s-deploy)

- [webapps-컨테이너-배포](https://github.com/Azure/webapps-container-deploy)

- [작업-워크플로-샘플](https://github.com/Azure/actions-workflow-samples)
