---
title: 리포지토리에 대한 사용 권한
description: 레지스트리의 특정 리포지토리로 범위가 지정 된 토큰을 만들어 이미지를 끌어오거나 푸시합니다.
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454920"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Azure Container Registry의 리포지토리 범위 권한 

Azure Container Registry는 전체 레지스트리에 대한 [역할 기반 액세스](container-registry-roles.md) 권한이 있는 id를 사용 하 여 여러 [인증 옵션](container-registry-authentication.md) 을 지원 합니다. 그러나 특정 시나리오의 경우 레지스트리의 특정 *리포지토리에* 만 액세스를 제공 해야 할 수 있습니다. 

이 문서에서는 레지스트리의 특정 리포지토리에 대해서만 작업을 수행할 수 있는 권한이 있는 액세스 토큰을 만들고 사용 하는 방법을 보여 줍니다. 액세스 토큰을 사용 하 여 사용자 또는 서비스에 저장소에 대한 제한 된 시간 제한 액세스를 제공 하 여 이미지를 끌어오거나 푸시 하거나 다른 작업을 수행할 수 있습니다. 

토큰 개념 및 시나리오에 대한 배경 정보는이 문서의 뒷부분에 나오는 [리포지토리 범위 사용 권한 정보](#about-repository-scoped-permissions)를 참조 하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 이 기능은 **프리미엄** 컨테이너 레지스트리에서만 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry sku](container-registry-skus.md)를 참조 하세요.
* 현재 서비스 주체 또는 관리 id와 같은 Azure Active Directory 개체에 리포지토리 범위 사용 권한을 할당할 수 없습니다.

## <a name="prerequisites"></a>선행 조건

* **Azure CLI** -이 문서에는 Azure CLI (버전 2.0.76 이상)의 로컬 설치가 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* **Docker** -레지스트리를 인증 하려면 로컬 Docker 설치도 필요 합니다. Docker는 [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) 및 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 시스템에 대한 설치 지침을 제공합니다.
* **리포지토리를 사용 하는 컨테이너 레지스트리** -없는 경우 Azure 구독에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다. 

  테스트를 위해 하나 이상의 샘플 이미지를 레지스트리로 [푸시](container-registry-get-started-docker-cli.md) 하거나 [가져옵니다](container-registry-import-images.md) . 이 문서의 예에서는 `samples/hello-world:v1` 및 `samples/nginx:v1`의 두 리포지토리에서 다음 이미지를 참조 합니다. 

## <a name="create-an-access-token"></a>액세스 토큰 만들기

[Az acr token create][az-acr-token-create] 명령을 사용 하 여 토큰을 만듭니다. 토큰을 만들 때 각 리포지토리에서 리포지토리 및 관련 된 작업을 하나 이상 지정 하거나 해당 설정으로 기존 범위 맵을 지정 합니다.

### <a name="create-access-token-and-specify-repositories"></a>액세스 토큰 만들기 및 리포지토리 지정

다음 예에서는 `samples/hello-world` 리포지토리에서 `content/write` 및 `content/read` 동작을 수행 하 고 `samples/nginx` 리포지토리에 대해 `content/read` 동작을 수행할 수 있는 권한이 있는 액세스 토큰을 만듭니다. 기본적으로이 명령은 두 개의 암호를 생성 합니다. 

이 예제에서는 토큰 상태를 `enabled` (기본 설정)로 설정 하지만 언제 든 지 토큰을 업데이트 하 고 상태를 `disabled`로 설정할 수 있습니다.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

출력은 생성 된 암호 및 범위 맵을 포함 하 여 토큰에 대한 세부 정보를 표시 합니다. 나중에 `docker login`사용할 수 있도록 암호를 안전한 장소에 저장 하는 것이 좋습니다. 암호는 다시 검색할 수 없지만 새 암호는 생성할 수 있습니다.

출력에는 `MyToken-scope-map`이라는 범위 맵이 자동으로 생성 되는 것도 표시 됩니다. 범위 맵을 사용 하 여 다른 토큰에 동일한 리포지토리 작업을 적용할 수 있습니다. 또는 나중에 범위 맵을 업데이트 하 여 토큰 사용 권한을 변경 합니다.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>범위 맵 및 연결 된 토큰 만들기

또는 토큰을 만들 때 리포지토리 및 연결 된 작업이 포함 된 범위 맵을 지정 합니다. 범위 맵을 만들려면 [az acr scope-map create][az-acr-scope-map-create] 명령을 사용 합니다.

다음 예제 명령은 이전 예제에서 사용 된 것과 동일한 권한으로 범위 맵을 만듭니다. 이 기능을 사용 하면 `samples/hello-world` 리포지토리의 작업을 `content/write` 하 고 `content/read` 하 고 `samples/nginx` 리포지토리에서 `content/read` 작업을 수행할 수 있습니다.

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

다음과 유사하게 출력됩니다.

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

[Az acr token create][az-acr-token-create] 를 실행 하 여 *MyScopeMap* 범위 맵에 연결 된 토큰을 만듭니다. 기본적으로이 명령은 두 개의 암호를 생성 합니다. 이 예제에서는 토큰 상태를 `enabled` (기본 설정)로 설정 하지만 언제 든 지 토큰을 업데이트 하 고 상태를 `disabled`로 설정할 수 있습니다.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

출력에는 생성 된 암호와 적용 한 범위 맵을 포함 하 여 토큰에 대한 세부 정보가 표시 됩니다. 나중에 `docker login`사용할 수 있도록 암호를 안전한 장소에 저장 하는 것이 좋습니다. 암호는 다시 검색할 수 없지만 새 암호는 생성할 수 있습니다.

## <a name="generate-passwords-for-token"></a>토큰에 대한 암호 생성

토큰을 만들 때 암호가 생성 된 경우에는 [레지스트리 인증](#authenticate-using-token)을 계속 진행 합니다.

토큰 암호가 없거나 새 암호를 생성 하려는 경우 [az acr token credential generate][az-acr-token-credential-generate] 명령을 실행 합니다.

다음 예에서는 만료 기간 30 일을 사용 하 여 만든 토큰에 대해 새 암호를 생성 합니다. TOKEN_PWD 환경 변수에 암호를 저장 합니다. 이 예제는 bash 셸에 대해 형식이 지정 됩니다.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>토큰을 사용 하 여 인증

토큰 자격 증명을 사용 하 여 레지스트리를 인증 하려면 `docker login`를 실행 합니다. 토큰 이름을 사용자 이름으로 입력 하 고 암호 중 하나를 제공 합니다. 다음 예에서는 bash 셸에 대해 형식이 지정 되 고 환경 변수를 사용 하 여 값을 제공 합니다.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

출력은 성공적인 인증을 표시 해야 합니다.

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>범위 지정 액세스 확인

토큰이 레지스트리에 있는 리포지토리에 범위 사용 권한을 제공 하는지 확인할 수 있습니다. 이 예에서는 `samples/hello-world` 및 `samples/nginx` 리포지토리에서 사용할 수 있는 이미지를 가져오기 위해 다음 `docker pull` 명령이 성공적으로 완료 되었습니다.

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

예제 토큰은 `samples/hello-world` 리포지토리에서만 `content/write` 작업 `docker push`을 허용 하므로 해당 리포지토리에 성공 하지만 `samples/nginx`에는 실패 합니다.

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>범위 맵 및 토큰 업데이트

토큰 권한을 업데이트 하려면 [az acr scope-map update][az-acr-scope-map-update]를 사용 하 여 연결 된 범위 맵에서 사용 권한을 업데이트 합니다. 예를 들어 *MyScopeMap* 를 업데이트 하 여 `samples/hello-world` 리포지토리에서 `content/write` 작업을 제거 하려면 다음을 수행 합니다.

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

범위 맵이 둘 이상의 토큰과 연결 된 경우이 명령은 연결 된 모든 토큰의 사용 권한을 업데이트 합니다.

다른 범위 맵으로 토큰을 업데이트 하려면 [az acr token update][az-acr-token-update]를 실행 합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

토큰 또는 토큰과 연결 된 범위 맵을 업데이트 한 후에는 다음 `docker login` 또는 토큰을 사용 하는 다른 인증에 사용 권한 변경 내용이 적용 됩니다.

토큰을 업데이트 한 후에는 레지스트리에 액세스 하기 위해 새 암호를 생성 하는 것이 좋습니다. [Az acr token credential generate][az-acr-token-credential-generate]를 실행 합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>리포지토리 범위 권한 정보

### <a name="concepts"></a>개념

리포지토리의 범위 사용 권한을 구성 하려면 Azure CLI의 명령을 사용 하 여 *액세스 토큰과* 연결 된 *범위 맵을* 만듭니다.

* **액세스 토큰** 은 레지스트리를 인증 하는 데 암호와 함께 사용 되는 자격 증명입니다. 각 토큰과 연결 된 *작업* 범위는 하나 이상의 리포지토리로 허용 됩니다. 각 토큰에 대해 만료 시간을 설정할 수 있습니다. 

* 지정 된 각 리포지토리에 대한 **작업** 에는 다음 중 하나 이상이 포함 됩니다.

  |작업  |설명  |
  |---------|---------|
  |`content/read`     |  리포지토리에서 데이터를 읽습니다. 예를 들어 아티팩트를 끌어옵니다.  |
  |`metadata/read`    | 리포지토리에서 메타 데이터를 읽습니다. 예를 들어 태그를 나열 하거나 매니페스트 메타 데이터를 표시 합니다.   |
  |`content/write`     |  리포지토리에 데이터를 씁니다. `content/read`와 함께 사용 하 여 아티팩트를 푸시합니다.    |
  |`metadata/write`     |  리포지토리에 메타 데이터를 씁니다. 예를 들어 매니페스트 특성을 업데이트 합니다.  |
  |`content/delete`    | 리포지토리에서 데이터를 제거 합니다. 예를 들어 리포지토리 또는 매니페스트를 삭제 합니다. |

* **범위 맵은** 토큰에 적용 하는 리포지토리 권한을 그룹화 하거나 다른 토큰에 다시 적용할 수 있는 레지스트리 개체입니다. 토큰을 만들 때 범위 맵을 적용 하지 않는 경우 권한 설정을 저장 하기 위해 범위 맵이 자동으로 생성 됩니다. 

  범위 맵은 리포지토리 집합에 대해 동일한 액세스 권한을 가진 여러 사용자를 구성 하는 데 도움이 됩니다. 또한 Azure Container Registry는 액세스 토큰을 만들 때 적용할 수 있는 시스템 정의 범위 맵을 제공 합니다.

다음 이미지는 토큰과 범위 맵 간의 관계를 요약 합니다. 

![레지스트리 범위 맵 및 토큰](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>시나리오

액세스 토큰을 사용 하는 시나리오는 다음과 같습니다.

* 저장소에서 이미지를 끌어오는 개별 토큰을 IoT 장치에 제공
* 외부 조직을 특정 리포지토리에 대한 사용 권한 제공 
* 조직의 특정 사용자 그룹에 대한 리포지토리 액세스를 제한 합니다. 예를 들어 특정 리포지토리를 대상으로 하는 이미지를 작성 하는 개발자에 게 쓰기 및 읽기 액세스를 제공 하 고 해당 리포지토리에서 배포 하는 팀에 대한 읽기 권한을 제공 합니다.

### <a name="authentication-using-token"></a>토큰을 사용 하 여 인증

토큰 이름을 사용자 이름으로 사용 하 고 연결 된 암호 중 하나를 사용 하 여 대상 레지스트리로 인증 합니다. 인증 방법은 구성 된 작업에 따라 달라 집니다.

### <a name="contentread-or-contentwrite"></a>콘텐츠/읽기/쓰기/쓰기

토큰이 `content/read` 또는 `content/write` 작업만 허용 하는 경우 다음 인증 흐름 중 하나에서 토큰 자격 증명을 제공 합니다.

* `docker login`를 사용 하 여 Docker 인증
* Azure CLI에서 [az acr login][az-acr-login] 명령을 사용 하 여 레지스트리를 인증 합니다.

인증 후에 토큰은 범위가 지정 된 리포지토리 또는 리포지토리에 대해 구성 된 작업을 허용 합니다. 예를 들어 토큰이 리포지토리에서 `content/read` 작업을 허용 하는 경우 해당 리포지토리의 이미지에서 `docker pull` 작업이 허용 됩니다.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>메타 데이터/읽기, 메타 데이터/쓰기 또는 내용/삭제

토큰이 리포지토리에서 `metadata/read`, `metadata/write`또는 `content/delete` 작업을 허용 하는 경우 토큰 자격 증명을 Azure CLI의 관련 [az acr repository][az-acr-repository] 명령과 함께 매개 변수로 제공 해야 합니다.

예를 들어 리포지토리에 대해 `metadata/read` 작업이 허용 되는 경우 [az acr repository show 태그][az-acr-repository-show-tags] 명령을 실행 하 여 태그를 나열할 때 토큰 자격 증명을 전달 합니다.

## <a name="next-steps"></a>다음 단계

* 범위 맵과 액세스 토큰을 관리 하려면 [az acr scope-map][az-acr-scope-map] 및 [az acr token][az-acr-token] 명령 그룹에서 추가 명령을 사용 합니다.
* 관리자 계정 또는 Azure Active Directory id를 사용 하 여 Azure container registry로 인증 하는 시나리오는 [인증 개요](container-registry-authentication.md) 를 참조 하세요.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
