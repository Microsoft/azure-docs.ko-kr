---
title: 컨테이너 이미지를 Azure Container Registry로 가져오기
description: Docker 명령을 실행하지 않고도 Azure API를 사용하여 컨테이너 이미지를 Azure Container Registry로 가져옵니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827292"
---
# <a name="import-container-images-to-a-container-registry"></a>컨테이너 이미지를 컨테이너 레지스트리로 가져오기

Docker 명령을 사용하지 않고 컨테이너 이미지를 Azure Container Registry로 쉽게 가져올(복사) 수 있습니다. 예를 들어 개발 레지스트리에서 프로덕션 레지스트리로 이미지를 가져오거나 공개 레지스트리에서 기본 이미지를 복사합니다.

Azure Container Registry는 기존 레지스트리에서 이미지를 복사하는 많은 일반적인 시나리오를 처리합니다.

* 공개 레지스트리에서 가져오기

* 동일한 Azure 구독이나 다른 Azure 구독의 다른 Azure Container Registry에서 가져오기

* 비 Azure 개인 컨테이너 레지스트리에서 가져오기

Docker CLI 명령을 사용하는 대신 Azure Container Registry로 이미지를 가져오는 경우 다음과 같은 이점이 있습니다.

* 클라이언트 환경에는 로컬 Docker 설치가 필요하지 않으므로 지원되는 OS 유형과 관계없이 컨테이너 이미지를 가져옵니다.

* 다중 아키텍처 이미지(예: 공식 Docker 이미지)를 가져오는 경우 매니페스트 목록에 지정된 모든 아키텍처 및 플랫폼의 이미지가 복사됩니다.

컨테이너 이미지를 가져오기 위해 이 문서에서는 Azure Cloud Shell이나 로컬로 Azure CLI를 실행하도록 요구합니다(버전 2.0.55 이상 권장). `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

> [!NOTE]
> 여러 Azure 지역에 동일한 컨테이너 이미지를 분산해야 하는 경우 Azure Container Registry에서 [지역 복제](container-registry-geo-replication.md)도 지원합니다. 레지스트리를 지역 복제하면(프리미엄 SKU 필요), 단일 레지스트리의 동일한 이미지와 태그 이름으로 여러 지역에 서비스를 제공할 수 있습니다.
>

## <a name="prerequisites"></a>필수 조건

Azure Container Registry가 아직 없는 경우 레지스트리를 만듭니다. 자세한 단계는 [빠른 시작: Azure CLI를 사용하여 개인 컨테이너 레지스트리 만들기](container-registry-get-started-azure-cli.md)를 참조하세요.

이미지를 Azure Container Registry로 가져오려면 ID에 대상 레지스트리에 대한 contributor 역할 이상의 쓰기 권한이 있어야 합니다. [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요. 

## <a name="import-from-a-public-registry"></a>공개 레지스트리에서 가져오기

### <a name="import-from-docker-hub"></a>Docker 허브에서 가져오기

예를 들어 [az acr import][az-acr-import] 명령을 사용하여 Docker 허브에서 *myregistry* 레지스트리로 다중 아키텍처 `hello-world:latest` 이미지를 가져옵니다. `hello-world`는 Docker 허브의 공식 이미지이므로 이 이미지는 기본 `library` 리포지토리에 있습니다. `--source` 이미지 매개 변수의 값에 리포지토리 이름 및 선택적으로 태그를 포함합니다. 태그 대신 해당 매니페스트 다이제스트를 통해 이미지를 선택적으로 식별할 수 있으며, 이 경우 이미지의 특정 버전이 보장됩니다.
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

`az acr repository show-manifests` 명령을 실행하여 이 이미지에 여러 개의 매니페스트가 연결된 것을 확인할 수 있습니다.

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

다음 예제에서는 Docker 허브의 `tensorflow` 리포지토리에서 공용 이미지를 가져옵니다.

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Microsoft Container Registry에서 가져오기

예를 들어 Microsoft Container Registry의 `windows` 리포지토리에서 최신 Windows Server Core 이미지를 가져옵니다.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>다른 Azure Container Registry에서 가져오기

통합 Azure Active Directory 권한을 사용하여 다른 Azure Container Registry에서 이미지를 가져올 수 있습니다.

* 원본 레지스트리에서 읽고(reader 역할) 대상 레지스트리에 쓸 수 있는(contributor 역할) Azure Active Directory 권한이 ID에 있어야 합니다.

* 레지스트리는 동일한 Active Directory 테넌트의 다른 Azure 구독이나 동일한 Azure 구독에 있을 수 있습니다.

### <a name="import-from-a-registry-in-the-same-subscription"></a>동일한 구독의 레지스트리에서 가져오기

예를 들어 원본 레지스트리 *mysourceregistry*에서 동일한 Azure 구독의 *myregistry*로 `aci-helloworld:latest` 이미지를 가져옵니다.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

다음 예제에서는 태그 대신 매니페스트 다이제스트(`sha256:...`으로 표시되는 SHA-256 해시)를 통해 이미지를 가져옵니다.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>다른 구독의 레지스트리에서 가져오기

다음 예제에서 *mysourceregistry*는 동일한 Active Directory 테넌트의 *myregistry*에서 다른 구독에 있습니다. `--registry` 매개 변수를 사용하여 원본 레지스트리의 리소스 ID를 제공합니다. `--source` 매개 변수는 원본 리포지토리 및 이미지 이름만 지정하고, 레지스트리 로그인 서버 이름은 지정하지 않습니다.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>서비스 주체 자격 증명을 사용하여 레지스트리에서 가져오기

Active Directory 권한을 사용하여 액세스할 수 없는 레지스트리에서 가져오려면 서비스 주체 자격 증명을 사용할 수 있습니다(사용 가능한 경우). 원본 레지스트리에 대한 ACRPull 액세스 권한이 있는 Active Directory [서비스 주체](container-registry-auth-service-principal.md)의 appID 및 암호를 제공합니다. 서비스 주체를 사용하면 이미지를 레지스트리로 가져와야 하는 빌드 시스템 및 기타 무인 시스템에 도움이 됩니다.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>비 Azure 개인 컨테이너 레지스트리에서 가져오기

레지스트리에 대한 끌어오기 액세스를 허용하는 자격 증명을 지정하여 개인 레지스트리에서 이미지를 가져옵니다. 예를 들어 개인 Docker 레지스트리에서 이미지를 끌어옵니다. 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 공용 레지스트리 또는 다른 개인 레지스트리에서 Azure Container Registry로 컨테이너 이미지를 가져오는 방법을 알아보았습니다. 추가 이미지 가져오기 옵션은 [az acr import][az-acr-import] 명령 참조를 참조하세요. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
