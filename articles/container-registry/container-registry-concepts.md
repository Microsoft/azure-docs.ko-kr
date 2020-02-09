---
title: 리포지토리 & 이미지 정보
description: Azure 컨테이너 레지스트리, 리포지토리 및 컨테이너 이미지의 주요 개념을 소개 합니다.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: 9de0c344b226a0b13e76c7f02977ba3c91ba2d2a
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455281"
---
# <a name="about-registries-repositories-and-images"></a>레지스트리, 리포지토리 및 이미지 정보

이 문서에서는 컨테이너 레지스트리, 리포지토리 및 컨테이너 이미지와 관련 아티팩트의 주요 개념을 소개 합니다. 

## <a name="registry"></a>레지스트리

컨테이너 *레지스트리*는 컨테이너 이미지를 저장하고 배포하는 서비스입니다. Docker 허브는 오픈 소스 커뮤니티를 지 원하는 공용 컨테이너 레지스트리 이며 이미지의 일반 카탈로그로 사용 됩니다. Azure Container Registry은 사용자에 게 이미지에 대한 직접 제어를 제공 하 고, 통합 인증을 사용 하 여 네트워크에 가까운 배포, [가상 네트워크 및 방화벽 구성](container-registry-vnet.md), [태그 잠금](container-registry-image-lock.md)및 기타 다양 한 기능에 대한 글로벌 배포와 안정성을 지 원하는 [지역 복제](container-registry-geo-replication.md) 를 제공 합니다. 

Docker 컨테이너 이미지 외에도 Azure Container Registry은 OCI (Open Container 이니셔티브) 이미지 형식을 비롯 한 관련 [콘텐츠 아티팩트](container-registry-image-formats.md) 를 지원 합니다.

## <a name="content-addressable-elements-of-an-artifact"></a>아티팩트의 콘텐츠 주소 지정 가능 요소

Azure container registry의 아티팩트 주소에는 다음과 같은 요소가 포함 됩니다. 

```
[loginUrl]/[namespace]/[artifact:][tag]
```

* **loginUrl** -레지스트리 호스트의 정규화 된 이름입니다. Azure container registry의 레지스트리 호스트는 *myregistry*. azurecr.io (모두 소문자) 형식으로 되어 있습니다. Docker 또는 다른 클라이언트 도구를 사용 하 여 Azure container registry에 아티팩트를 끌어오거나 푸시할 때 loginUrl를 지정 해야 합니다. 
* 작업 그룹 또는 응용 프로그램의 경우와 같이 관련 된 이미지 또는 아티팩트의 **네임 스페이스로** 구분 된 논리적 그룹화
* **아티팩트** -특정 이미지 또는 아티팩트의 리포지토리의 이름입니다.
* **태그** -리포지토리에 저장 된 특정 버전의 이미지 또는 아티팩트


예를 들어 Azure container registry에 있는 이미지의 전체 이름은 다음과 같습니다.

```
myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2
```

이러한 요소에 대한 자세한 내용은 다음 섹션을 참조 하십시오.

## <a name="repository-name"></a>리포지토리 이름

컨테이너 레지스트리는 *리포지토리*를 관리 하 고, 동일한 이름의 컨테이너 이미지 또는 다른 아티팩트의 컬렉션을 관리 하 고, 다른 태그를 사용 합니다. 예를 들어, 다음 3개의 이미지가 "helloworld acr" 리포지토리에 있습니다.

```
acr-helloworld:latest
acr-helloworld:v1
acr-helloworld:v2
```

리포지토리 이름에 [네임스페이스](container-registry-best-practices.md#repository-namespaces)가 포함될 수도 있습니다. 네임 스페이스를 사용 하면 슬래시로 구분 된 리포지토리 이름을 사용 하 여 이미지를 그룹화 할 수 있습니다. 예를 들면 다음과 같습니다.

```
marketing/campaign10-18/web:v2
marketing/campaign10-18/api:v3
marketing/campaign10-18/email-sender:v2
product-returns/web-submission:20180604
product-returns/legacy-integrator:20180715
```

## <a name="image"></a>이미지

컨테이너 이미지 또는 레지스트리 내의 다른 아티팩트는 하나 이상의 태그와 연결 되 고 하나 이상의 계층이 있으며 매니페스트로 식별 됩니다. 이러한 구성 요소가 서로 어떻게 관련 되는지에 대한 이해는 레지스트리를 효과적으로 관리 하는 데 도움이 될 수 있습니다.

### <a name="tag"></a>태그

이미지 또는 다른 아티팩트의 *태그* 는 해당 버전을 지정 합니다. 리포지토리 내의 단일 아티팩트에는 하나 이상의 태그를 할당할 수 있으며 "태그가 지정 되지 않은" 경우도 있습니다. 즉, 이미지의 모든 태그를 삭제할 수 있는 반면 이미지의 데이터 (레이어)는 레지스트리에 남아 있습니다.

리포지토리(또는 리포지토리 및 네임스페이스)와 태그는 이미지의 이름을 정의합니다. 밀어넣기 및 끌어오기 작업에서 해당 이름을 지정하여 이미지를 밀어넣고 끌어올 수 있습니다.

컨테이너 이미지에 태그를 표시 하는 방법은 시나리오에 따라 개발 하거나 배포 하는 방법을 안내 합니다. 예를 들어 기본 이미지를 유지 하 고 이미지 배포를 위한 고유한 태그를 유지 하는 데 안정적인 태그를 사용할 수 있습니다. 자세한 내용은 [컨테이너 이미지 태그 지정 및 버전 관리에 대한 권장 사항](container-registry-image-tag-version.md)을 참조 하세요.

### <a name="layer"></a>계층

컨테이너 이미지는 각각 이미지를 정의 하는 Dockerfile의 줄에 해당 하는 하나 이상의 *계층*으로 구성 됩니다. 레지스트리의 이미지는 공용 계층을 공유하므로 스토리지 효율성이 높아집니다. 예를 들어, 다른 리포지토리에 있는 여러 이미지가 동일한 Alpine Linux 기본 계층을 공유할 수 있지만 해당 계층의 복사본 하나만 레지스트리에 저장됩니다.

계층 공유는 여러 개의 이미지가 공통 계층을 공유하는 방식으로 노드에 대한 계층 분산을 최적화합니다. 예를 들어, 노드에 이미 있는 이미지가 맨 아래에 Alpine Linux 계층을 포함하는 경우 이후에 같은 계층을 참조하는 다른 이미지를 끌어올 경우 해당 계층이 노드로 전송되지 않습니다. 대신, 노드에 이미 존재하는 계층을 참조합니다.

보안 격리 및 잠재적 계층 조작 으로부터 보호를 제공 하기 위해 레이어는 레지스트리를 통해 공유 되지 않습니다.

### <a name="manifest"></a>매니페스트

컨테이너 레지스트리에 푸시되는 각 컨테이너 이미지 또는 아티팩트는 *매니페스트와*연결 됩니다. 이미지를 밀어넣을 때 레지스트리에서 생성된 매니페스트는 이미지를 고유하게 식별하고 해당 계층을 지정합니다. Azure CLI 명령 [az acr repository show-manifest][az-acr-repository-show-manifests]를 사용 하 여 리포지토리의 매니페스트를 나열할 수 있습니다.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

예를 들어 "acr-helloworld" 리포지토리의 매니페스트를 나열 합니다.

```console
$ az acr repository show-manifests --name myregistry --repository acr-helloworld
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>매니페스트 다이제스트

매니페스트는 고유한 SHA-256 해시 또는 *매니페스트 다이제스트*에 의해 식별됩니다. 각 이미지 또는 아티팩트-태그가 지정 되었는지 아니면 그렇지 않은지는 다이제스트로 식별 됩니다. 다이제스트 값은 이미지의 계층 데이터가 다른 이미지의 계층 데이터와 동일하더라도 고유합니다. 이 메커니즘에 따르면 동일하게 태그가 지정된 이미지를 레지스트리에 반복적으로 밀어넣을 수 있습니다. 예를 들어, 각 이미지는 고유한 다이제스트로 식별되므로 `myimage:latest`(을)를 레지스트리에 오류 없이 반복적으로 밀어넣을 수 있습니다.

끌어오기 작업에서 해당 다이제스트를 지정하여 레지스트리에서 이미지를 끌어올 수 있습니다. 일부 시스템은 다이제스트별로 끌어오도록 구성이 가능합니다. 동일하게 태그가 지정된 이미지를 나중에 레지스트리로 밀어넣더라도 해당 이미지 버전 끌어오기가 보장되기 때문입니다.

예를 들어, 매니페스트 다이제스트를 통해 "acr-helloworld" 리포지토리에서 이미지를 끌어옵니다.

```console
$ docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108
```

> [!IMPORTANT]
> 동일한 태그가 지정된 이미지를 수정 후 반복적으로 밀어넣을 경우 태그가 지정되지 않은 이미지는 별도로 분리되어 생성될 수 있지만 이 이미지는 여전히 레지스트리에서 공간을 차지합니다. 태그가 지정되지 않은 이미지는 태그를 기준으로 이미지를 나열하거나 표시할 경우 Azure CLI 또는 Azure Portal에 표시되지 않습니다. 그러나 해당 계층은 여전히 존재하며 레지스트리에서 공간을 차지합니다. 태그가 없는 이미지를 삭제 하면 매니페스트가 유일한 경우 또는 마지막 인 경우 특정 계층을 가리키는 레지스트리 공간을 해제 합니다. 태그 없는 이미지에서 사용 되는 공간을 확보 하는 방법에 대한 자세한 내용은 [Azure Container Registry에서 컨테이너 이미지 삭제](container-registry-delete.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Container Registry에서 [이미지 저장소](container-registry-storage.md) 및 [지원 되는 콘텐츠 형식](container-registry-image-formats.md) 에 대해 자세히 알아보세요.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


