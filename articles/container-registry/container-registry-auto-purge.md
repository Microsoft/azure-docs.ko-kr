---
title: 태그 및 매니페스트 제거
description: 제거 명령을 사용 하 여 연령 및 태그 필터를 기반으로 Azure container registry에서 여러 태그와 매니페스트를 삭제 하 고 선택적으로 제거 작업을 예약 합니다.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: 0ec1f5f6f5c3c572b8558c971b58e46cce36e3fd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923100"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Azure container registry에서 자동으로 이미지 제거

개발 워크플로의 일부로 Azure container registry를 사용 하는 경우 레지스트리는 짧은 기간 후 필요 하지 않은 이미지 또는 기타 아티팩트를 신속 하 게 채울 수 있습니다. 특정 기간 보다 오래 되었거나 지정 된 이름 필터와 일치 하는 모든 태그를 삭제할 수 있습니다. 여러 아티팩트를 신속 하 게 삭제 하기 위해이 문서에서는 요청 시 또는 [예약 된](container-registry-tasks-scheduled.md) ACR 작업으로 실행할 수 있는 `acr purge` 명령을 소개 합니다. 

`acr purge` 명령은 현재 GitHub에 있는 [acr cli](https://github.com/Azure/acr-cli) 리포지토리의 소스 코드에서 빌드된 공용 컨테이너 이미지 (`mcr.microsoft.com/acr/acr-cli:0.1`)에 배포 됩니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 문서의 ACR 작업 예제를 실행할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.69 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요. 

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [부속 사용 약관][terms-of-use]에 동의하면 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

> [!WARNING]
> `acr purge` 명령은 주의 해 서 사용 합니다. 삭제 된 이미지 데이터는 복구할 수 없습니다. 이미지 이름과 달리 매니페스트 다이제스트로 이미지를 가져오는 시스템이 있는 경우 태그 없는 이미지를 제거 하면 안 됩니다. 태그가 지정되지 않은 이미지를 삭제하면 해당 시스템은 레지스트리에서 이미지를 끌어올 수 없게 됩니다. 매니페스트를 가져오는 대신 *고유한 태깅* 스키마를 채택 하는 것이 [좋습니다. 권장 되는 모범 사례](container-registry-image-tag-version.md)입니다.

Azure CLI 명령을 사용 하 여 단일 이미지 태그나 매니페스트를 삭제 하려면 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조 하세요.

## <a name="use-the-purge-command"></a>제거 명령 사용

`acr purge` 컨테이너 명령은 이름 필터와 일치 하 고 지정 된 기간 보다 오래 된 리포지토리의 태그로 이미지를 삭제 합니다. 기본적으로 기본 [매니페스트와](container-registry-concepts.md#manifest) 계층 데이터가 아닌 태그 참조만 삭제 됩니다. 이 명령에는 매니페스트도 삭제할 수 있는 옵션이 있습니다. 

> [!NOTE]
> `acr purge` `write-enabled` 특성이 `false`로 설정 된 이미지 태그 또는 리포지토리를 삭제 하지 않습니다. 자세한 내용은 [Azure container registry에서 컨테이너 이미지 잠그기](container-registry-image-lock.md)를 참조 하세요.

`acr purge`은 [ACR 작업](container-registry-tasks-overview.md)에서 컨테이너 명령으로 실행 되도록 설계 되었으므로 태스크가 실행 되는 레지스트리를 사용 하 여 자동으로 인증 하 고 해당 위치에서 작업을 수행 합니다. 이 문서의 태스크 예제는 정규화 된 컨테이너 이미지 명령 대신 `acr purge` 명령 [별칭](container-registry-tasks-reference-yaml.md#aliases) 을 사용 합니다.

최소한 `acr purge`를 실행 하는 경우 다음을 지정 합니다.

* `--filter`-리포지토리의 태그를 필터링 하기 위한 리포지토리 및 *정규식* 입니다. 예: `--filter "hello-world:.*"` `hello-world` 리포지토리의 모든 태그와 일치 하 고 `--filter "hello-world:^1.*"` `1`시작 하는 태그와 일치 합니다. 여러 개의 `--filter` 매개 변수를 전달 하 여 여러 리포지토리를 제거 합니다.
* `--ago`-이미지를 삭제 하는 기간을 나타내는 이동 스타일의 [기간 문자열](https://golang.org/pkg/time/) 입니다. 기간은 하나 이상의 10 진수 시퀀스로 구성 되며 각각 단위 접미사가 있습니다. 유효한 시간 단위에는 일의 경우 "d", 시간에는 "h", 분의 경우 "m"이 포함 됩니다. 예를 들어, `--ago 2d3h6m`는 2 일, 3 시간 및 6 분 전에 마지막으로 수정 된 모든 필터링 된 이미지를 선택 하 고 `--ago 1.5h`는 1.5 시간 이전에 마지막으로 수정 된 이미지를 선택 합니다.

`acr purge`는 여러 개의 선택적 매개 변수를 지원 합니다. 다음 두 가지는이 문서의 예제에서 사용 됩니다.

* `--untagged`-연결 된 태그를 포함 하지 않는 매니페스트 (*태그가*지정 되지 않은 매니페스트)를 삭제 하도록 지정 합니다.
* `--dry-run`-데이터를 삭제 하지 않도록 지정 하지만이 플래그 없이 명령이 실행 되는 것과 같은 출력을 지정 합니다. 이 매개 변수는 보존 하려는 데이터를 실수로 삭제 하지 않도록 제거 명령을 테스트 하는 데 유용 합니다.

추가 매개 변수를 `acr purge --help`를 실행 합니다. 

`acr purge`는 스트리밍 및 나중에 검색할 수 있도록 저장 된 [변수 실행](container-registry-tasks-reference-yaml.md#run-variables) 및 [작업 실행 로그](container-registry-tasks-overview.md#view-task-logs) 를 비롯 하 여 ACR 작업 명령의 다른 기능을 지원 합니다.

### <a name="run-in-an-on-demand-task"></a>요청 시 작업에서 실행

다음 예제에서는 [az acr run][az-acr-run] 명령을 사용 하 여 요청 시 `acr purge` 명령을 실행 합니다. 이 예제에서는 1 일 이상 전에 수정 된 *myregistry* 의 `hello-world` 리포지토리에서 모든 이미지 태그 및 매니페스트를 삭제 합니다. 컨테이너 명령은 환경 변수를 사용 하 여 전달 됩니다. 소스 컨텍스트 없이 태스크가 실행 됩니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>예약 된 작업에서 실행

다음 예제에서는 [az acr task create][az-acr-task-create] 명령을 사용 하 여 매일 [예약 된 acr 작업](container-registry-tasks-scheduled.md)을 만듭니다. 작업은 `hello-world` 리포지토리에서 7 일 이상 전에 수정 된 태그를 제거 합니다. 컨테이너 명령은 환경 변수를 사용 하 여 전달 됩니다. 소스 컨텍스트 없이 태스크가 실행 됩니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

[Az acr task show][az-acr-task-show] 명령을 실행 하 여 타이머 트리거가 구성 되어 있는지 확인 합니다.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>많은 개수의 태그 및 매니페스트 제거

많은 수의 태그와 매니페스트를 제거 하는 데 몇 분 이상 걸릴 수 있습니다. 수천 개의 태그 및 매니페스트를 제거 하려면 명령이 요청 시 작업의 기본 시간 제한 시간인 600 초 또는 예약 된 작업에 대한 3600 초 보다 길게 실행 되어야 할 수 있습니다. 제한 시간을 초과 하는 경우 태그 및 매니페스트의 하위 집합만 삭제 됩니다. 대규모 제거가 완료 되었는지 확인 하려면 `--timeout` 매개 변수를 전달 하 여 값을 늘립니다. 

예를 들어 다음 주문형 태스크는 시간 제한 시간을 3600 초 (1 시간)로 설정 합니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>예: 레지스트리에서 여러 리포지토리의 예약 된 제거

이 예제에서는 `acr purge`를 사용 하 여 레지스트리의 여러 리포지토리를 정기적으로 정리 하는 과정을 안내 합니다. 예를 들어 `samples/devimage1` 및 `samples/devimage2` 리포지토리에 이미지를 푸시하는 개발 파이프라인이 있을 수 있습니다. 개발 이미지는 배포에 대한 프로덕션 리포지토리로 정기적으로 가져오기 때문에 개발 이미지가 더 이상 필요 하지 않습니다. 주간 작업에 대비 하 여 `samples/devimage1` 및 `samples/devimage2` 리포지토리를 정기적으로 제거 합니다.

### <a name="preview-the-purge"></a>제거 미리 보기

데이터를 삭제 하기 전에 `--dry-run` 매개 변수를 사용 하 여 요청 시 제거 작업을 실행 하는 것이 좋습니다. 이 옵션을 사용 하면 데이터를 제거 하지 않고 명령이 제거 하는 태그 및 매니페스트를 볼 수 있습니다. 

다음 예제에서 각 리포지토리의 필터는 모든 태그를 선택 합니다. `--ago 0d` 매개 변수는 리포지토리에서 필터와 일치 하는 모든 연령의 이미지와 일치 합니다. 시나리오에 필요한 대로 선택 조건을 수정 합니다. `--untagged` 매개 변수는 태그 외에 매니페스트를 삭제 함을 나타냅니다. 컨테이너 명령은 환경 변수를 사용 하 여 [az acr run][az-acr-run] 명령에 전달 됩니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

명령 출력을 검토 하 여 선택 매개 변수와 일치 하는 태그 및 매니페스트를 확인 합니다. 이 명령은 `--dry-run`를 사용 하 여 실행 되므로 데이터가 삭제 되지 않습니다.

샘플 출력:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>제거 예약

마른 실행을 확인 한 후에는 제거를 자동화 하는 예약 된 작업을 만듭니다. 다음 예에서는 일요일 (1:00 UTC)에 매주 작업을 예약 하 여 이전 제거 명령을 실행 합니다.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

[Az acr task show][az-acr-task-show] 명령을 실행 하 여 타이머 트리거가 구성 되어 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure Container Registry에서 [이미지 데이터를 삭제](container-registry-delete.md) 하는 다른 옵션에 대해 알아봅니다.

이미지 저장소에 대한 자세한 내용은 [Azure Container Registry 컨테이너 이미지 저장소](container-registry-storage.md)를 참조 하세요.

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show

