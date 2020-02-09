---
title: 태그가 없는 매니페스트를 유지 하는 정책
description: 정의 된 기간이 끝난 후 태그가 지정 된 매니페스트를 자동으로 삭제 하기 위해 Azure container registry에서 보존 정책을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454820"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>태그가 없는 매니페스트에 대한 보존 정책 설정

Azure Container Registry에는 연결 된 태그 (*태그가 없는 매니페스트*)가 없는 저장 된 이미지 매니페스트에 대한 *보존 정책을* 설정 하는 옵션이 제공 됩니다. 보존 정책을 사용 하도록 설정 하면 설정 된 일 수 후에 레지스트리의 태그 없는 매니페스트가 자동으로 삭제 됩니다. 이 기능을 사용 하면 레지스트리가 필요 하지 않은 아티팩트를 채우지 않고 저장소 비용을 절감할 수 있습니다. 태그가 없는 매니페스트의 `delete-enabled` 특성이 `false`로 설정 된 경우에는 매니페스트를 삭제할 수 없으며 보존 정책이 적용 되지 않습니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 문서의 명령 예제를 실행할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.74 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli]를 참조하세요.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공되며 일부 [제한 사항이 적용](#preview-limitations)됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

> [!WARNING]
> 보존 정책을 주의 하 여 설정--삭제 된 이미지 데이터는 복구할 수 없습니다. 이미지 이름과 달리 매니페스트 다이제스트로 이미지를 가져오는 시스템이 있는 경우 태그 없는 매니페스트에 대한 보존 정책을 설정 하면 안 됩니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트를 가져오는 대신 *고유한 태깅* 스키마를 채택 하는 것이 [좋습니다. 권장 되는 모범 사례](container-registry-image-tag-version.md)입니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

* 보존 정책을 사용 하 여 **프리미엄** 컨테이너 레지스트리만 구성할 수 있습니다. 레지스트리 서비스 계층에 대한 자세한 내용은 [Azure Container Registry sku](container-registry-skus.md)를 참조 하세요.
* 태그 없는 매니페스트에 대한 보존 정책만 설정할 수 있습니다.
* 보존 정책은 현재 정책 사용이 설정 된 *후* 태그가 없는 매니페스트에만 적용 됩니다. 레지스트리의 태그가 없는 기존 매니페스트는 정책이 적용 되지 않습니다. 태그가 없는 기존 매니페스트를 삭제 하려면 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)의 예제를 참조 하세요.

## <a name="about-the-retention-policy"></a>보존 정책 정보

Azure Container Registry는 레지스트리의 매니페스트 계산을 참조 합니다. 매니페스트가 태그 되지 않은 경우 보존 정책을 확인 합니다. 보존 정책을 사용 하는 경우 정책에 설정 된 기간 (일)에 따라 특정 날짜를 사용 하 여 매니페스트 삭제 작업이 큐에 대기 됩니다.

별도의 큐 관리 작업은 메시지를 지속적으로 처리 하 고 필요에 따라 크기를 조정 합니다. 예를 들어 30 일의 보존 정책을 사용 하는 레지스트리에 1 시간 떨어진 두 개의 매니페스트를 태그를 지정할 수 있습니다. 두 메시지는 큐에 대기 됩니다. 그런 다음 30 일 후에 약 1 시간 마다 메시지를 큐에서 검색 하 고 처리 하는 것으로 간주 합니다.

## <a name="set-a-retention-policy---cli"></a>보존 정책 설정-CLI

다음 예에서는 Azure CLI를 사용 하 여 레지스트리의 태그 없는 매니페스트에 대한 보존 정책을 설정 하는 방법을 보여 줍니다.

### <a name="enable-a-retention-policy"></a>보존 정책 사용

기본적으로 컨테이너 레지스트리에는 보존 정책이 설정 되지 않습니다. 보존 정책을 설정 하거나 업데이트 하려면 Azure CLI에서 [az acr config 보존 업데이트][az-acr-config-retention-update] 명령을 실행 합니다. 태그가 지정 되지 않은 매니페스트를 유지 하려면 0에서 365 사이의 일 수를 지정할 수 있습니다. 일 수를 지정 하지 않으면 명령의 기본값은 7 일로 설정 됩니다. 보존 기간 후에는 레지스트리의 태그가 없는 모든 매니페스트가 자동으로 삭제 됩니다.

다음 예에서는 레지스트리에서 *myregistry*의 태그가 없는 매니페스트에 대해 30 일의 보존 정책을 설정 합니다.

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

다음 예제에서는 태그를 제거 하는 즉시 레지스트리의 매니페스트를 삭제 하도록 정책을 설정 합니다. 보존 기간을 0 일로 설정 하 여이 정책을 만듭니다. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>보존 정책 유효성 검사

보존 기간이 0 일인 이전 정책을 사용 하도록 설정 하면 태그 없는 매니페스트가 삭제 되었는지 빠르게 확인할 수 있습니다.

1. 테스트 이미지 `hello-world:latest` 이미지를 레지스트리에 푸시하고 원하는 다른 테스트 이미지를 대체 합니다.
1. 예를 들어 [az acr repository 태그 지정 해제][az-acr-repository-untag] 명령을 사용 하 여 `hello-world:latest` 이미지를 태그 지정 해제 합니다. 태그가 없는 매니페스트는 레지스트리에 남아 있습니다.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. 몇 초 안에 태그가 없는 매니페스트가 삭제 됩니다. 예를 들어 [az acr repository show-manifest][az-acr-repository-show-manifests] 명령을 사용 하 여 리포지토리에 매니페스트를 나열 하 여 삭제를 확인할 수 있습니다. 테스트 이미지가 리포지토리의 유일한 경우 리포지토리 자체가 삭제 됩니다.

### <a name="disable-a-retention-policy"></a>보존 정책 사용 안 함

레지스트리에 설정 된 보존 정책을 보려면 [az acr config 보존이 show][az-acr-config-retention-show] 명령을 실행 합니다.

```azurecli
az acr config retention show --registry myregistry
```

레지스트리에서 보존 정책을 사용 하지 않도록 설정 하려면 [az acr config 보존 업데이트][az-acr-config-retention-update] 명령을 실행 하 고 `--status disabled`를 설정 합니다.

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>보존 정책 설정-포털

[Azure Portal](https://portal.azure.com)에서 레지스트리의 보존 정책을 설정할 수도 있습니다. 다음 예제에서는 포털을 사용 하 여 레지스트리의 태그가 없는 매니페스트에 대한 보존 정책을 설정 하는 방법을 보여 줍니다.

### <a name="enable-a-retention-policy"></a>보존 정책 사용

1. Azure container registry로 이동 합니다. **정책**에서 **보존** (미리 보기)을 선택 합니다.
1. **상태**에서 **사용**을 선택 합니다.
1. 태그가 없는 매니페스트를 유지 하려면 0에서 365 사이의 일 수를 선택 합니다. **저장**을 선택합니다.

![Azure Portal에서 보존 정책 사용](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>보존 정책 사용 안 함

1. Azure container registry로 이동 합니다. **정책**에서 **보존** (미리 보기)을 선택 합니다.
1. **상태**에서 **사용 안 함**을 선택 합니다. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* 에서 [이미지 및 리포지토리를 삭제](container-registry-delete.md) 하는 옵션에 대한 자세한 정보 Azure Container Registry

* 레지스트리에서 선택한 이미지 및 매니페스트를 [자동으로 제거](container-registry-auto-purge.md) 하는 방법을 알아봅니다.

* 레지스트리에서 [이미지 및 매니페스트를 잠그는](container-registry-image-lock.md) 옵션에 대한 자세한 정보

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
