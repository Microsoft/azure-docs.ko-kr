---
title: Azure Container Registry에서 Azure Container Instances에 배포
description: Azure Container Registry의 컨테이너 이미지를 사용하여 Azure Container Instances에 컨테이너를 배포하는 방법을 알아봅니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: b596af8ae9fbbaee6964622df44d316a11582cb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686153"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Registry에서 Azure Container Instances에 배포

[Azure Container Registry](../container-registry/container-registry-intro.md)는 개인 Docker 컨테이너 이미지를 저장하는 데 사용되는 Azure 기반의 관리형 컨테이너 레지스트리 서비스입니다. 이 문서에서는 Azure Container Registry에 저장된 컨테이너 이미지를 Azure Container Instances에 배포하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

**Azure Container Registry**: 이 문서의 단계를 완료하려면 Azure Container Registry가 필요하고 해당 레지스트리에 하나 이상의 컨테이너 이미지가 있어야 합니다. 레지스트리가 필요한 경우 [Azure CLI를 사용하여 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-azure-cli.md)를 참조하세요.

**Azure CLI**: 이 문서의 명령줄 예제는 [Azure CLI](/cli/azure/)를 사용하며 Bash 셸용으로 형식이 지정됩니다. 로컬로 [Azure CLI를 설치](/cli/azure/install-azure-cli)하거나 [Azure Cloud Shell][cloud-shell-bash]을 사용할 수 있습니다.

## <a name="configure-registry-authentication"></a>레지스트리 인증 구성

프로덕션 시나리오에서 Azure Container Registry에 대한 액세스는 [서비스 주체](../container-registry/container-registry-auth-service-principal.md)를 사용하여 제공되어야 합니다. 서비스 주체를 통해 컨테이너 이미지에 대해 [역할 기반 액세스 제어](../container-registry/container-registry-roles.md)를 제공할 수 있습니다. 예를 들어, 레지스트리에 대해 끌어오기 전용 액세스 권한을 가지는 서비스 주체를 구성할 수 있습니다.

다음 섹션에서는 Azure Key Vault 및 서비스 주체를 만들고, 자격 증명 모음에 서비스 주체의 자격 증명을 저장합니다. 

### <a name="create-key-vault"></a>주요 자격 증명 모음 만들기

[Azure Key Vault](../key-vault/key-vault-overview.md)에 자격 증명 모음이 아직 없는 경우 Azure CLI에서 다음 명령을 사용하여 만듭니다.

`RES_GROUP` 변수를 키 자격 증명 모음을 만들 기존 리소스 그룹의 이름으로 업데이트하고 `ACR_NAME` 변수를 컨테이너 레지스트리의 이름으로 바꿉니다. `AKV_NAME`에 새 주요 자격 증명 모음의 이름을 지정합니다. 자격 증명 모음 이름은 Azure 내에서 고유해야 하고, 영숫자 3~24자 사이여야 하고, 문자로 시작하고 문자 또는 숫자로 끝나야 하며 연속 하이픈은 포함하면 안 됩니다.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>서비스 주체 만들기 및 자격 증명 저장

이제 서비스 주체를 만들고 해당 자격 증명을 주요 자격 증명 모음에 저장해야 합니다.

다음 명령은 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]를 사용하여 서비스 주체를 만들고, [az keyvault secret set][az-keyvault-secret-set]을 사용하여 서비스 주체의 **암호**를 자격 증명 모음에 저장합니다.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

이전 명령의 `--role` 인수는 *acrpull* 역할을 사용하여 서비스 주체를 구성하고, 레지스트리에 대해 끌어오기 전용 액세스 권한을 부여합니다. 밀어넣기 및 끌어오기 액세스 권한을 모두 부여하려면 `--role` 인수를 *acrpush*로 변경합니다.

다음으로, 인증을 위해 Azure Container Registry에 전달하는 **username**에 해당하는 서비스 주체의 *appId*를 자격 증명 모음에 저장합니다.

```azurecli
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

## <a name="deploy-container-with-azure-cli"></a>Azure CLI를 사용하여 컨테이너 배포

서비스 주체 자격 증명은 Azure Key Vault 암호에 저장되므로, 애플리케이션 및 서비스는 해당 자격 증명을 사용하여 개인 레지스트리에 액세스할 수 있습니다.

먼저 [az acr show][az-acr-show] 명령을 사용하여 레지스트리의 로그인 서버 이름을 가져옵니다. 로그인 서버 이름은 모두 소문자이며 `myregistry.azurecr.io`와 유사합니다.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

다음 [az container create][az-container-create] 명령을 실행하여 컨테이너 인스턴스를 배포합니다. 이 명령은 Azure Key Vault에 저장된 서비스 주체의 자격 증명을 사용하여 컨테이너 레지스트리에서 인증을 받고, 사용자가 이전에 [aci-helloworld](container-instances-quickstart.md) 이미지를 레지스트리에 밀어넣었다고 가정합니다. 레지스트리의 다른 이미지를 사용하려는 경우 `--image` 값을 업데이트합니다.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` 값은 Azure 내에서 고유해야 하므로, 이전 명령은 컨테이너의 DNS 이름 레이블에 임의 숫자를 추가합니다. 명령의 출력에는 컨테이너의 FQDN(정규화된 도메인 이름)이 표시됩니다. 예를 들면 다음과 같습니다.

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

컨테이너가 성공적으로 시작되면, 브라우저에서 해당 FQDN으로 이동하여 애플리케이션이 성공적으로 실행되고 있는지 확인할 수 있습니다.

## <a name="deploy-with-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 배포

컨테이너 그룹 정의에 `imageRegistryCredentials` 속성을 포함하여 Azure Resource Manager 템플릿에서 Azure Container Registry 속성을 지정할 수 있습니다.

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Resource Manager 템플릿에서 Azure Key Vault 암호를 참조하는 방법에 대한 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](../azure-resource-manager/resource-manager-keyvault-parameter.md)을 참조하세요.

## <a name="deploy-with-azure-portal"></a>Azure Portal을 사용하여 배포

Azure Container Registry에서 컨테이너 이미지를 유지할 경우 Azure Portal을 사용하여 Azure Container Instances에 컨테이너를 쉽게 생성할 수 있습니다. 포털을 사용하여 컨테이너 레지스트리에서 컨테이너 인스턴스를 배포하는 경우 레지스트리의 [관리자 계정](../container-registry/container-registry-authentication.md#admin-account)을 사용하도록 설정해야 합니다. 관리자 계정은 주로 테스트 용도로 단일 사용자가 레지스트리에 액세스하도록 설계되었습니다. 

1. Azure Portal에서 컨테이너 레지스트리로 이동합니다.

1. 관리자 계정이 활성화되어 있는지 확인하려면 **액세스 키**를 선택하고 **관리 사용자**에서 **사용**을 선택합니다.

1. **리포지토리**를 선택하고, 배포할 리포지토리를 선택하고, 배포하려는 컨테이너 이미지의 태그를 마우스 오른쪽 단추로 클릭하고, **인스턴스 실행**을 선택합니다.

    ![Azure Portal의 Azure Container Registry에 있는 "인스턴스 실행"][acr-runinstance-contextmenu]

1. 컨테이너에 대한 이름과 리소스 그룹에 대한 이름을 입력합니다. 원하는 경우 기본값을 변경할 수도 있습니다.

    ![Azure Container Instances의 만들기 메뉴][acr-create-deeplink]

1. 배포가 완료되면 알림 창에서 컨테이너 그룹으로 이동하여 해당 IP 주소 및 기타 속성을 찾을 수 있습니다.

    ![Azure Container Instances 컨테이너 그룹의 세부 정보 보기][aci-detailsview]

## <a name="next-steps"></a>다음 단계

Azure Container Registry 인증에 대한 자세한 내용은 [Azure Container Registry로 인증](../container-registry/container-registry-authentication.md)을 참조하세요.

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
