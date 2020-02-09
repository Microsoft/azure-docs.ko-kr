---
title: 서명 된 이미지 관리
description: Azure container registry에 대한 콘텐츠 신뢰를 사용 하도록 설정 하 고 서명 된 이미지를 푸시하고 풀 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 09/06/2019
ms.openlocfilehash: 0418b13c352dc3b81d34501e7e76be6c54615a83
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456465"
---
# <a name="content-trust-in-azure-container-registry"></a>Azure Container Registry의 콘텐츠 신뢰

Azure Container Registry는 Docker의 [콘텐츠 신뢰][docker-content-trust] 모델을 구현하여 서명된 이미지를 푸시 및 풀하도록 설정합니다. 이 문서에서는 컨테이너 레지스트리의 콘텐츠 신뢰를 사용하도록 설정하기 시작합니다.

> [!NOTE]
> 콘텐츠 신뢰는 Azure Container Registry의 [Premium SKU](container-registry-skus.md) 기능입니다.

## <a name="how-content-trust-works"></a>콘텐츠 신뢰의 작동 원리

보안을 염두에 두고 설계된 분산 시스템에서 중요한 것은 시스템으로 들어오는 데이터의 *소스* 및 *무결성*을 확인하는 것입니다. 데이터 소비자는 데이터의 게시자(소스)를 확인할 수 있어야 할 뿐 아니라, 게시된 후 수정되지 않았음(무결성)을 확인할 수 있어야 합니다. 

이미지 게시자는 콘텐츠 신뢰를 사용하여 레지스트리로 푸시하는 이미지를 **서명**할 수 있습니다. 이미지 소비자(게시자의 레지스트리에서 이미지를 풀하는 사람 또는 시스템)는 *오직* 서명된 이미지만 끌어오도록 클라이언트를 구성할 수 있습니다. 이미지 소비자가 서명된 이미지를 풀하면 Docker 클라이언트는 이미지의 무결성을 확인합니다. 이 모델에서는 레지스트리의 서명된 이미지가 실제로 게시자에 의해 게시되었고, 게시된 후로 수정되지 않았음을 소비자가 확신할 수 있습니다.

### <a name="trusted-images"></a>신뢰할 수 있는 이미지

콘텐츠 신뢰는 리포지토리의 **태그**와 함께 작동합니다. 이미지 리포지토리는 서명된 태그와 서명되지 않은 태그를 사용하여 이미지를 포함할 수 있습니다. 예를 들어 `myimage:stable` 및 `myimage:latest` 이미지만 서명하고, `myimage:dev` 이미지는 서명하지 않을 수 있습니다.

### <a name="signing-keys"></a>서명 키

콘텐츠 신뢰는 암호화 서명 키 집합을 사용하여 관리됩니다. 이러한 키는 레지스트리의 특정 리포지토리와 연결됩니다. Docker 클라이언트 및 레지스트리가 리포지토리의 태그에 대한 트러스트를 관리하는 데 사용하는 여러 가지 서명 키가 있습니다. 콘텐츠 신뢰를 사용하도록 설정하고 컨테이너 게시 및 소비 파이프라인에 통합하는 경우 이러한 키를 철저하게 관리해야 합니다. 자세한 내용은 이 문서의 뒷부분에 나오는 [키 관리](#key-management) 및 Docker 설명서의 [콘텐츠 신뢰에 대한 키 관리][docker-manage-keys]를 참조하세요.

> [!TIP]
> 지금까지 Docker의 콘텐츠 신뢰 모델에 대해 매우 간략하게 알아보았습니다. 콘텐츠 신뢰에 대한 자세한 내용은 [Docker의 신뢰 콘텐츠][docker-content-trust]를 참조하세요.

## <a name="enable-registry-content-trust"></a>레지스트리 콘텐츠 신뢰 사용

첫 번째 단계는 레지스트리 수준에서 콘텐츠 신뢰를 사용하도록 설정하는 것입니다. 콘텐츠 신뢰를 사용하도록 설정하면 클라이언트(사용자 또는 서비스)가 서명된 이미지를 레지스트리에 푸시할 수 있습니다. 레지스트리에서 콘텐츠 신뢰를 사용하도록 설정해도 레지스트리 사용 범위가 콘텐츠 신뢰가 설정된 소비자로 제한되지 않습니다. 콘텐츠 신뢰가 설정되지 않은 소비자는 계속해서 정상적으로 레지스트리를 사용할 수 있습니다. 그러나 클라이언트에서 콘텐츠 신뢰를 설정한 사용자는 *오직* 레지스트리의 서명된 이미지만 볼 수 있습니다.

레지스트리에 콘텐츠 신뢰를 사용하려면 먼저 Azure Portal에서 레지스트리로 이동합니다. **정책** 아래에서 **콘텐츠 신뢰** > **사용** > **저장**을 선택합니다. Azure CLI에서 [az acr config content-trust update][az-acr-config-content-trust-update] 명령을 사용할 수도 있습니다.

![Azure Portal에서 레지스트리에 콘텐츠 신뢰를 사용하도록 설정][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>클라이언트 콘텐츠 신뢰 사용

신뢰할 수 있는 이미지를 사용하려면 이미지 게시자와 소비자 둘 다 Docker 클라이언트에 콘텐츠 트러스트를 사용하도록 설정해야 합니다. 게시자는 콘텐츠 신뢰가 설정된 레지스트리로 푸시하는 이미지를 서명할 수 있습니다. 소비자가 콘텐츠 신뢰를 사용하도록 설정하면 게시자의 레지스트리 보기가 서명된 이미지로 제한됩니다. 콘텐츠 신뢰는 Docker 클라이언트에서 기본적으로 사용되지 않지만, 셸 세션 또는 명령별로 사용하도록 설정할 수 있습니다.

셸 세션에 콘텐츠 신뢰를 사용하도록 설정하려면 `DOCKER_CONTENT_TRUST` 환경 변수를 **1**로 설정합니다. Bash 셸을 예로 들면 다음과 같습니다.

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

세션 대신 단일 명령에 콘텐츠 신뢰를 사용 또는 사용하지 않도록 설정하려는 경우 `--disable-content-trust` 인수를 지원하는 여러 Docker 명령이 있습니다. 단일 명령에 콘텐츠 신뢰를 사용하도록 설정하려면:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

셸 세션에 콘텐츠 신뢰를 사용하도록 설정했으며 단일 명령에는 사용하지 않도록 설정하려는 경우:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>이미지 서명 권한 부여

권한이 부여된 사용자 또는 시스템만 레지스트리에 신뢰할 수 있는 이미지를 푸시할 수 있습니다. 사용자(또는 서비스 주체를 사용하는 시스템)에게 신뢰할 수 있는 이미지 푸시 권한을 부여하려면 사용자의 Azure Active Directory ID에 `AcrImageSigner` 역할을 부여합니다. 이미지를 레지스트리에 푸시하는 데 필요한 `AcrPush`(또는 상응하는 것) 역할 외에도 이 역할이 필요합니다. 자세한 내용은 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.

> [!NOTE]
> Azure container registry의 [관리자 계정](container-registry-authentication.md#admin-account) 에는 신뢰할 수 있는 이미지 푸시 권한을 부여할 수 없습니다.

Azure Portal 및 Azure CLI에서 `AcrImageSigner` 역할을 부여하는 자세한 방법은 다음과 같습니다.

### <a name="azure-portal"></a>Azure 포털

Azure Portal에서 레지스트리로 이동한 다음, **액세스 제어(IAM)**  > **역할 할당 추가**를 차례로 선택합니다. **역할 할당 추가**의 `AcrImageSigner`역할**아래에서**를 선택하고, 한 명 이상의 사용자 또는 서비스 주체를 **선택**한 다음, **저장**합니다.

이 예제에서는 두 엔터티, 즉, "service-principal"이라는 서비스 사용자 이름과 "Azure User"라는 사용자에게 `AcrImageSigner` 역할을 할당했습니다.

![Azure Portal에서 레지스트리에 콘텐츠 신뢰를 사용하도록 설정][content-trust-02-portal]

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 사용자에게 서명 권한을 부여하려면 게시자의 레지스트리로 범위가 지정된 사용자에게 `AcrImageSigner` 역할을 할당합니다. 명령 형식은 다음과 같습니다.

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

예를 들어 자신에게 역할을 부여하려면 인증된 Azure CLI 세션에서 다음 명령을 실행하면 됩니다. Azure 컨테이너 레지스트리 이름을 반영하도록 `REGISTRY` 값을 수정합니다.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)

az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

[서비스 사용자](container-registry-auth-service-principal.md)에게 신뢰할 수 있는 이미지를 레지스트리에 푸시할 수 있는 권한을 부여할 수도 있습니다. 서비스 사용자를 사용하면 빌드 시스템 또는 신뢰할 수 있는 이미지를 레지스트리에 푸시해야 하는 기타 무인 시스템에 도움이 됩니다. 형식은 사용자 권한 부여와 비슷하지만, `--assignee` 값의 서비스 사용자 ID를 지정해야 합니다.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

`<service principal ID>`는 서비스 사용자의 **appId**, **objectId** 또는 **servicePrincipalNames** 중 하나입니다. 서비스 사용자 및 Azure Container Registry 사용에 대한 자세한 내용은 [서비스 사용자로 Azure Container Registry 인증](container-registry-auth-service-principal.md)을 참조하세요.

> [!IMPORTANT]
> 역할을 변경한 후, `az acr login`을 실행하여 새 역할이 영향을 받을 수 있도록 Azure CLI에 대한 로컬 ID 토큰을 새로 고칩니다. Id의 역할을 확인 하는 방법에 대한 자세한 내용은 [rbac를 사용 하 여 azure 리소스에 대한 액세스 관리 및 Azure CLI](../role-based-access-control/role-assignments-cli.md) 하 고 [azure 리소스에 대한 rbac 문제 해결](../role-based-access-control/troubleshooting.md)

## <a name="push-a-trusted-image"></a>신뢰할 수 있는 이미지 푸시

신뢰할 수 있는 이미지 태그를 컨테이너 레지스트리에 푸시하려면 콘텐츠 신뢰를 사용하도록 설정하고 `docker push`를 사용하여 이미지를 푸시해야 합니다. 서명된 태그를 처음으로 푸시하면 루트 서명 키 및 리포지토리 서명 키의 암호를 만들라는 메시지가 표시됩니다. 루트 및 리포지토리 키는 머신에 로컬로 생성 및 저장됩니다.

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

콘텐츠 신뢰를 사용하도록 설정하고 처음으로 `docker push`를 실행하면 Docker 클라이언트는 후속 푸시에 동일한 루트 키를 사용합니다. 이후 동일한 리포지토리에 푸시할 때마다 리포지토리 키만 입력하면 됩니다. 신뢰할 수 있는 이미지를 새 리포지토리를 푸시할 때마다 새 리포지토리 키의 암호를 입력해야 합니다.

## <a name="pull-a-trusted-image"></a>신뢰할 수 있는 이미지 풀

신뢰할 수 있는 이미지를 풀하려면 평소와 같이 콘텐츠 신뢰를 사용하도록 설정하고 `docker pull` 명령을 실행합니다. 신뢰할 수 있는 이미지를 풀하려면 `AcrPull` 역할은 일반 사용자에 충분합니다. `AcrImageSigner` 역할과 같은 추가 역할은 필요하지 않습니다. 콘텐츠 신뢰를 사용하도록 설정한 소비자는 서명된 태그가 있는 이미지만 풀할 수 있습니다. 다음은 서명된 태그를 풀하는 예제입니다.

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

콘텐츠 신뢰를 사용하도록 설정된 클라이언트가 서명되지 않은 태그를 풀하려고 시도하면 작업이 실패합니다.

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>배후 상황

`docker pull` 명령을 실행하면 Docker 클라이언트는 [Notary CLI][docker-notary-cli]와 동일한 라이브러리를 사용하여 게시자가 풀링하는 태그에 대한 tag-to-SHA-256 다이제스트 매핑을 요청합니다. 신뢰 데이터의 서명 유효성 검사가 끝나면 클라이언트는 Docker 엔진에 "다이제스트별로 풀"하라고 지시합니다. 풀하는 동안, 엔진은 콘텐츠 주소로 SHA-256 체크섬을 사용하여 Azure 컨테이너 레지스트리에서 이미지 매니페스트를 요청하고 유효성을 검사합니다.

## <a name="key-management"></a>키 관리

첫 번째 신뢰할 수 있는 이미지를 푸시할 때 `docker push` 출력에서 설명했듯이, 루트 키가 가장 중요합니다. 반드시 루트 키를 백업하여 안전한 위치에 저장하세요. 기본적으로 Docker 클라이언트는 서명 키를 다음 디렉터리에 저장합니다.

```sh
~/.docker/trust/private
```

루트 및 리포지토리 키를 아카이브에 압축하고 안전한 위치에 저장하여 백업해 두세요. 예를 들어 Bash에서는 다음과 같습니다.

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

신뢰할 수 있는 이미지를 푸시할 때 로컬로 생성된 루트 및 리포지토리 키 외에도 다른 여러 항목이 Azure Container Registry에서 생성 및 저장됩니다. 추가 관리 지침을 포함하여 Docker 콘텐츠 신뢰 구현의 다양한 키에 대한 자세한 내용은 Docker 설명서에서 [콘텐츠 신뢰용 키 관리][docker-manage-keys]를 참조하세요.

### <a name="lost-root-key"></a>루트 키 손실

루트 키에 대한 액세스 권한이 손실되면 해당 키를 사용하여 태그에 서명한 모든 리포지토리의 서명된 태그에 액세스할 수 없습니다. Azure Container Registry는 손실된 루트 키로 서명한 이미지 태그에 대한 액세스를 복원할 수 없습니다. 레지스트리의 모든 신뢰 데이터(서명)를 제거하려면 레지스트리에 콘텐츠 신뢰를 사용하지 않도록 설정했다가 다시 사용하도록 설정합니다.

> [!WARNING]
> 레지스트리에서 콘텐트 신뢰를 사용하지 않도록 설정했다가 다시 사용하도록 설정하면 **레지스트리에 있는 모든 리포지토리의 모든 서명된 태그에 대한 신뢰 데이터가 모두 삭제됩니다**. 이 작업은 되돌릴 수 없습니다. Azure Container Registry는 삭제된 신뢰 데이터를 복구할 수 없습니다. 콘텐츠 신뢰를 사용하지 않도록 설정해도 이미지 자체는 삭제되지 않습니다.

레지스트리에 콘텐츠 신뢰를 사용하지 않도록 설정하려면 Azure Portal에서 레지스트리로 이동합니다. **정책** 아래에서 **콘텐츠 신뢰** > **사용 안 함** > **저장**을 선택합니다. 레지스트리의 모든 서명이 손실된다는 경고 메시지가 표시됩니다. 레지스트리의 모든 서명을 영구적으로 삭제하려면 **확인**을 선택합니다.

![Azure Portal에서 레지스트리에 콘텐츠 신뢰를 사용하지 않도록 설정][content-trust-03-portal]

## <a name="next-steps"></a>다음 단계

* 콘텐츠 신뢰에 대한 추가 정보는 [Docker의 콘텐츠 신뢰][docker-content-trust]를 참조하세요. 이 문서에서 몇 가지 핵심을 살펴보았지만, 콘텐츠 신뢰는 방대한 주제이며 Docker 설명서에 자세히 설명되어 있습니다.

* Docker 이미지를 빌드하고 푸시할 때 콘텐츠 신뢰를 사용하는 예제는 [Azure Pipelines](/azure/devops/pipelines/build/content-trust) 설명서를 참조하세요.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az-acr-config-content-trust-update
