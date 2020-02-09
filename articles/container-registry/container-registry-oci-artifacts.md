---
title: 밀어넣기 및 끌어오기 OCI 아티팩트
description: Azure에서 개인 컨테이너 레지스트리를 사용 하 여 OCI (Open Container 이니셔티브) 아티팩트 푸시 및 끌어오기
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.openlocfilehash: cb58a7ed51ae15d33ffdbb616c9b32ef03bcbfb7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456264"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Azure container registry를 사용 하 여 OCI 아티팩트 푸시 및 끌어오기

Azure container registry를 사용 하 여 [OCI (Open Container 이니셔티브) 아티팩트](container-registry-image-formats.md#oci-artifacts) 뿐만 아니라 Docker 및 docker 호환 컨테이너 이미지를 저장 하 고 관리할 수 있습니다.

이 기능을 설명 하기 위해이 문서에서는 [OCI 레지스트리를 저장소 (ORAS)](https://github.com/deislabs/oras) 도구로 사용 하 여 샘플 아티팩트 (텍스트 파일)를 Azure container Registry로 푸시하는 방법을 보여 줍니다. 그런 다음 레지스트리에서 아티팩트를 가져옵니다. 각 아티팩트에 적합 한 다양 한 명령줄 도구를 사용 하 여 Azure container registry에서 다양 한 OCI 아티팩트를 관리할 수 있습니다.

## <a name="prerequisites"></a>선행 조건

* **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
* **Oras 도구** - [GitHub](https://github.com/deislabs/oras/releases)리포지토리에서 운영 체제에 대한 현재 oras 릴리스를 다운로드 하 여 설치 합니다. 이 도구는 압축 된 tarball (`.tar.gz` 파일)로 릴리스됩니다. 운영 체제에 대한 표준 절차를 사용 하 여 파일을 추출 하 고 설치 합니다.
* **Azure Active Directory 서비스 주체 (선택 사항)** -oras를 사용 하 여 직접 인증 하려면 레지스트리에 액세스 하는 [서비스 주체](container-registry-auth-service-principal.md) 를 만듭니다. 아티팩트를 푸시 및 끌어올 수 있는 권한이 있도록 서비스 사용자에 게 AcrPush와 같은 역할을 할당 해야 합니다.
* **Azure CLI (선택 사항)** -개별 id를 사용 하려면 Azure CLI의 로컬 설치가 필요 합니다. 2\.0.71 이상 버전을 권장 합니다. `az --version `를 실행 하 여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.
* **Docker (선택 사항)** -개별 id를 사용 하려면 로컬로 Docker를 설치 하 여 레지스트리로 인증 해야 합니다. Docker는 모든 [macOS][docker-mac], [Windows][docker-windows] 또는 [Linux][docker-linux] 시스템에서 Docker를 쉽게 구성할 수 있는 패키지를 제공합니다.


## <a name="sign-in-to-a-registry"></a>레지스트리에 로그인

이 섹션에서는 사용 되는 id에 따라 레지스트리에 로그인 하기 위한 두 가지 권장 워크플로를 보여 줍니다. 사용자 환경에 적합 한 방법을 선택 합니다.

### <a name="sign-in-with-oras"></a>ORAS를 사용 하 여 로그인

푸시 권한이 있는 [서비스 주체](container-registry-auth-service-principal.md) 를 사용 하 여 `oras login` 명령을 실행 하 여 서비스 주체 응용 프로그램 ID 및 암호를 사용 하 여 레지스트리에 로그인 합니다. 정규화 된 레지스트리 이름 (이 경우 모두 소문자)을 지정 합니다 (이 경우 *myregistry.azurecr.io*). 서비스 사용자 응용 프로그램 ID는 환경 변수 `$SP_APP_ID`에 전달 되 고 변수의 암호는 `$SP_PASSWD`.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Stdin에서 암호를 읽으려면 `--password-stdin`를 사용 합니다.

### <a name="sign-in-with-azure-cli"></a>Azure CLI로 로그인

Id로 Azure CLI에 [로그인](/cli/azure/authenticate-azure-cli) 하 여 컨테이너 레지스트리에서 아티팩트를 푸시하고 풀 합니다.

그런 다음 Azure CLI 명령 [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) 을 사용 하 여 레지스트리에 액세스 합니다. 예를 들어 *myregistry*라는 레지스트리에 인증 하려면 다음을 수행 합니다.

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`는 Docker 클라이언트를 사용 하 여 `docker.config` 파일에 Azure Active Directory 토큰을 설정 합니다. 개별 인증 흐름을 완료 하려면 Docker 클라이언트를 설치 하 고 실행 해야 합니다.

## <a name="push-an-artifact"></a>아티팩트 푸시

일부 샘플 텍스트를 사용 하 여 로컬 작업 작업 디렉터리에 텍스트 파일을 만듭니다. 예를 들어 bash 셸에서는 다음과 같습니다.

```bash
echo "Here is an artifact!" > artifact.txt
```

`oras push` 명령을 사용 하 여이 텍스트 파일을 레지스트리에 푸시합니다. 다음 예에서는 `samples/artifact` 리포지토리에 샘플 텍스트 파일을 푸시합니다. 레지스트리는 정규화 된 레지스트리 이름 *myregistry.azurecr.io* (모두 소문자)로 식별 됩니다. 아티팩트는 `1.0`태그가 지정 됩니다. 아티팩트에는 기본적으로 파일 이름 다음에 있는 *미디어 유형* 문자열로 식별 되는 정의 되지 않은 형식이 있습니다 `artifact.txt`. 추가 형식은 [OCI 아티팩트](https://github.com/opencontainers/artifacts) 를 참조 하세요. 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

성공적인 푸시에 대한 출력은 다음과 유사 합니다.

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

레지스트리에서 아티팩트를 관리 하려면 Azure CLI을 사용 하는 경우 이미지 관리를 위해 표준 `az acr` 명령을 실행 합니다. 예를 들어 [az acr repository show][az-acr-repository-show] 명령을 사용 하 여 아티팩트의 특성을 가져옵니다.

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

다음과 유사하게 출력됩니다.

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>아티팩트 풀

`oras pull` 명령을 실행 하 여 레지스트리에서 아티팩트를 가져옵니다.

먼저 로컬 작업 디렉터리에서 텍스트 파일을 제거 합니다.

```bash
rm artifact.txt
```

`oras pull`를 실행 하 여 아티팩트를 가져오고 아티팩트를 푸시하는 데 사용 되는 미디어 유형을 지정 합니다.

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

풀에 성공 했는지 확인 합니다.

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>아티팩트 제거 (선택 사항)

Azure container registry에서 아티팩트를 제거 하려면 [az acr repository delete][az-acr-repository-delete] 명령을 사용 합니다. 다음 예제에서는 여기에 저장 된 아티팩트를 제거 합니다.

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>다음 단계

* 아티팩트에 대한 매니페스트를 구성 하는 방법을 포함 하 여 [ORAS 라이브러리](https://github.com/deislabs/oras/tree/master/docs)에 대해 자세히 알아보세요.
* 새 아티팩트 형식에 대한 참조 정보를 보려면 [OCI 아티팩트](https://github.com/opencontainers/artifacts) 리포지토리를 방문 하세요.



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
