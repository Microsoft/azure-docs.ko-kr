---
title: 서비스 주체를 사용 하 여 인증
description: Azure Active Directory 서비스 주체를 사용하여 프라이빗 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공합니다.
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455413"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>서비스 주체로 Azure Container Registry 인증

Azure AD(Azure Active Directory) 서비스 주체를 사용하여 컨테이너 레지스트리에 대해 컨테이너 이미지 `docker push` 및 `pull` 액세스 권한을 제공할 수 있습니다. 서비스 주체를 사용하면 "헤드리스" 서비스 및 애플리케이션에 대한 액세스를 제공할 수 있습니다.

## <a name="what-is-a-service-principal"></a>서비스 주체란?

Azure AD *서비스 주체*는 구독 내 Azure 리소스에 대한 액세스를 제공합니다. 서비스 사용자를 서비스의 사용자 id로 간주할 수 있습니다. 여기서 "서비스"는 리소스에 액세스 해야 하는 응용 프로그램, 서비스 또는 플랫폼입니다. 지정한 리소스에 대해서만 액세스 권한이 있는 서비스 주체를 구성할 수 있습니다. 그런 다음, 서비스 주체의 자격 증명을 사용하여 해당 리소스에 액세스하도록 애플리케이션이나 서비스를 구성합니다.

Azure Container Registry 컨텍스트에서, Azure의 프라이빗 레지스트리에 대한 풀, 푸시 및 풀 또는 기타 권한이 있는 Azure AD 서비스 주체를 만들 수 있습니다. 전체 목록에 대해서는 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.

## <a name="why-use-a-service-principal"></a>서비스 주체를 사용하는 이유

Azure AD 서비스 주체를 사용하면 프라이빗 컨테이너 레지스트리에 대해 범위가 지정된 액세스를 제공할 수 있습니다. 각 응용 프로그램 또는 서비스에 대한 다양 한 서비스 주체를 만듭니다. 각 응용 프로그램에는 레지스트리에 대한 맞춤형 액세스 권한이 있습니다. 또한 서비스와 애플리케이션 사이에 자격 증명을 공유하지 않아도 되기 때문에, 선택한 서비스 주체(및 애플리케이션)에 대해서만 자격 증명을 순환하거나 액세스 권한을 철회할 수 있습니다.

예를 들어, 빌드 시스템은 `push` 및 `pull` 액세스를 모두 제공 하는 서비스 주체를 사용 하는 반면, 이미지 `pull` 액세스만 제공 하는 서비스 주체를 사용 하도록 웹 응용 프로그램을 구성 합니다. 응용 프로그램 개발이 변경 되 면 빌드 시스템에 영향을 주지 않고 서비스 주체 자격 증명을 회전할 수 있습니다.

## <a name="when-to-use-a-service-principal"></a>서비스 주체를 사용하는 경우

**헤드리스 시나리오**에서 레지스트리 액세스를 제공하려면 서비스 주체를 사용해야 합니다. 즉, 자동 또는 무인 방식으로 컨테이너 이미지를 푸시하거나 풀해야 하는 모든 애플리케이션, 서비스 또는 스크립트입니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

  * *끌어오기*: KUBERNETES, DC/OS 및 Docker Swarm를 포함 하 여 레지스트리의 컨테이너를 오케스트레이션 시스템에 배포 합니다. 컨테이너 레지스트리에서 [AKS (Azure Kubernetes Service)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/)등의 관련 azure 서비스로 끌어올 수도 있습니다.

  * *Push*: 컨테이너 이미지를 빌드하고 Azure Pipelines 또는 Jenkins와 같은 연속 통합 및 배포 솔루션을 사용 하 여 레지스트리에 푸시합니다.

수동으로 컨테이너 이미지를 개발 워크스테이션으로 끌어올 때와 같이 레지스트리에 대한 개별 액세스의 경우 레지스트리 액세스에 대신 사용자 고유의 [AZURE AD id](container-registry-authentication.md#individual-login-with-azure-ad) 를 사용 하는 것이 좋습니다 (예: [az acr login][az-acr-login]사용).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>샘플 스크립트

GitHub에서 Azure CLI에 대한 앞의 샘플 스크립트와 Azure PowerShell의 버전을 찾을 수 있습니다.

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>서비스 주체를 사용 하 여 인증

컨테이너 레지스트리에 대한 액세스 권한이 부여 된 서비스 주체가 있으면 "헤드리스" 서비스 및 응용 프로그램에 액세스 하기 위한 자격 증명을 구성 하거나 `docker login` 명령을 사용 하 여 해당 자격 증명을 입력할 수 있습니다. 다음 값을 사용합니다.

* **사용자 이름** -서비스 사용자 응용 프로그램 id ( *클라이언트 id*라고도 함)
* **암호** 서비스 주체 암호 ( *클라이언트 암호*라고도 함)

각 값은 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`형태의 GUID입니다. 

> [!TIP]
> [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 명령을 실행하여 서비스 주체의 암호를 다시 생성할 수 있습니다.
>

### <a name="use-credentials-with-azure-services"></a>Azure 서비스에서 자격 증명 사용

Azure container registry를 사용 하 여 인증 하는 모든 Azure 서비스에서 서비스 주체 자격 증명을 사용할 수 있습니다.  다양 한 시나리오에 대해 레지스트리의 관리자 자격 증명 대신 서비스 주체 자격 증명을 사용 합니다.

예를 들어 자격 증명을 사용 하 여 Azure container registry에서 [Azure Container Instances](container-registry-auth-aci.md)로 이미지를 끌어옵니다.

### <a name="use-with-docker-login"></a>Docker 로그인과 함께 사용

서비스 주체를 사용 하 여 `docker login`를 실행할 수 있습니다. 다음 예제에서는 서비스 주체 응용 프로그램 ID가 환경 변수 `$SP_APP_ID`에 전달 되 고 변수의 암호가 `$SP_PASSWD`됩니다. Docker 자격 증명을 관리 하는 모범 사례는 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령 참조를 참조 하세요.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

일단 로그인 하면 Docker는 자격 증명을 캐시 합니다.

### <a name="use-with-certificate"></a>인증서와 함께 사용

인증서를 서비스 주체에 추가한 경우 인증서 기반 인증을 사용 하 여 Azure CLI에 로그인 한 다음 [az acr login][az-acr-login] 명령을 사용 하 여 레지스트리에 액세스할 수 있습니다. 인증서를 암호 대신 암호로 사용 하면 CLI를 사용할 때 추가 보안이 제공 됩니다. 

[서비스 주체를 만들](/cli/azure/create-an-azure-service-principal-azure-cli)때 자체 서명 된 인증서를 만들 수 있습니다. 또는 기존 서비스 주체에 하나 이상의 인증서를 추가 합니다. 예를 들어이 문서의 스크립트 중 하나를 사용 하 여 레지스트리에서 이미지를 끌어오거나 푸시할 수 있는 권한이 있는 서비스 주체를 만들거나 업데이트 하는 경우 [az ad sp credential reset][az-ad-sp-credential-reset] 명령을 사용 하 여 인증서를 추가 합니다.

인증서가 있는 서비스 주체를 사용 하 여 [Azure CLI에 로그인](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal)하려면 인증서가 PEM 형식 이어야 하며 개인 키를 포함 해야 합니다. 인증서가 필요한 형식이 아닌 경우 `openssl`와 같은 도구를 사용 하 여 변환 합니다. [Az login][az-login] 을 실행 하 여 서비스 주체를 사용 하 여 CLI에 로그인 하는 경우 서비스 주체의 응용 프로그램 id와 Active Directory 테넌트 id도 제공 합니다. 다음 예제에서는 이러한 값을 환경 변수로 보여 줍니다.

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

그런 다음 [az acr login][az-acr-login] 을 실행 하 여 레지스트리를 인증 합니다.

```azurecli
az acr login --name myregistry
```

CLI는 `az login` 실행 될 때 생성 된 토큰을 사용 하 여 레지스트리를 사용 하 여 세션을 인증 합니다.

## <a name="next-steps"></a>다음 단계

* Azure container registry를 사용 하 여 인증 하는 다른 시나리오는 [인증 개요](container-registry-authentication.md) 를 참조 하세요.

* Azure key vault를 사용 하 여 컨테이너 레지스트리에 대한 서비스 주체 자격 증명을 저장 하 고 검색 하는 예제는 [ACR 작업을 사용 하 여 컨테이너 이미지를 빌드하고 배포](container-registry-tutorial-quick-task.md)하는 자습서를 참조 하세요.

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
