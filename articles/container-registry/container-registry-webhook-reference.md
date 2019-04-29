---
title: Azure Container Registry 웹후크 스키마 참조
description: Azure Container Registry에 대한 웹후크 요청 JSON 페이로드 참조입니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331344"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry 웹후크 참조

컨테이너 레지스트리에 대해 특정 작업이 수행되면 이벤트를 생성하는 [웹후크를 구성](container-registry-webhook.md)할 수 있습니다. 예를 들어, 컨테이너 이미지 또는 Helm 차트를 레지스트리에 푸시 되거나 삭제 될 때 트리거되는 웹 후크를 사용 하도록 설정 합니다. 웹후크가 트리거되면 Azure Container Registry에서 사용자가 지정하는 엔드포인트로 이벤트에 대한 정보가 포함된 HTTP 또는 HTTPS 요청을 보냅니다. 그러면 해당 엔드포인트에서 웹후크를 처리하고 적절하게 작업을 수행할 수 있습니다.

다음 섹션에서는 지원되는 이벤트에서 생성하는 웹후크 요청 스키마에 대해 자세히 설명합니다. 이벤트 섹션에는 이벤트 유형에 대한 페이로드 스키마, 요청 페이로드 예, 웹후크를 트리거하는 하나 이상 명령 예가 포함되어 있습니다.

Azure Container Registry에 대한 웹후크를 구성하는 방법에 대한 자세한 내용은 [Azure Container Registry 웹후크 사용](container-registry-webhook.md)을 참조하세요.

## <a name="webhook-requests"></a>웹후크 요청

### <a name="http-request"></a>HTTP 요청

트리거된 웹후크는 웹후크를 구성할 때 사용자가 지정한 URL 엔드포인트에 대해 HTTP `POST` 요청을 수행합니다.

### <a name="http-headers"></a>HTTP 헤더

웹후크에 대한 `Content-Type` 사용자 지정 헤더를 지정하지 않은 경우 웹후크 요청에는 `application/json`의 `Content-Type`이 포함됩니다.

웹후크에 대해 지정했을 수도 있는 이러한 사용자 지정 헤더 외의 다른 헤더는 요청에 추가되지 않습니다.

## <a name="push-event"></a>푸시 이벤트

컨테이너 이미지가 리포지토리에 푸시될 때 트리거된 웹후크입니다.

### <a name="push-event-payload"></a>푸시 이벤트 페이로드

|요소|Type|설명|
|-------------|----------|-----------|
|`id`|String|웹후크 이벤트의 ID입니다.|
|`timestamp`|DateTime|웹후크 이벤트가 트리거된 시점의 시간입니다.|
|`action`|String|웹후크 이벤트가 트리거된 작업입니다.|
|[대상](#target)|복합 형식|웹후크 이벤트를 트리거한 이벤트의 대상입니다.|
|[요청](#request)|복합 형식|웹후크 이벤트를 생성한 요청입니다.|

### <a name="target"></a>target

|요소|Type|설명|
|------------------|----------|-----------|
|`mediaType`|String|참조된 개체의 MIME 형식입니다.|
|`size`|Int32|콘텐츠의 바이트 수입니다. 길이 필드와 동일합니다.|
|`digest`|String|콘텐츠의 다이제스트로, 레지스트리 V2 HTTP API 사양에 따라 정의됩니다.|
|`length`|Int32|콘텐츠의 바이트 수입니다. 크기 필드와 동일합니다.|
|`repository`|String|리포지토리 이름입니다.|
|`tag`|String|이미지 태그 이름입니다.|

### <a name="request"></a>request

|요소|Type|설명|
|------------------|----------|-----------|
|`id`|String|이벤트를 시작한 요청의 ID입니다.|
|`host`|String|외부에서 액세스할 수 있는 레지스트리 인스턴스의 호스트 이름으로, 들어오는 요청의 HTTP 호스트 헤더를 통해 지정됩니다.|
|`method`|String|이벤트를 생성한 요청 메서드입니다.|
|`useragent`|String|요청의 사용자 에이전트 헤더입니다.|

### <a name="payload-example-image-push-event"></a>페이로드 예: 이미지 푸시 이벤트

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

예제 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) 이미지를 트리거하는 명령은 **푸시** 이벤트 웹 후크:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>차트 푸시 이벤트

웹 후크 Helm 차트를 리포지토리에 푸시 될 때 트리거됩니다.

### <a name="chart-push-event-payload"></a>차트 푸시 이벤트 페이로드

|요소|Type|설명|
|-------------|----------|-----------|
|`id`|String|웹후크 이벤트의 ID입니다.|
|`timestamp`|DateTime|웹후크 이벤트가 트리거된 시점의 시간입니다.|
|`action`|String|웹후크 이벤트가 트리거된 작업입니다.|
|[대상](#helm_target)|복합 형식|웹후크 이벤트를 트리거한 이벤트의 대상입니다.|

### <a name="helm_target"></a>target

|요소|Type|설명|
|------------------|----------|-----------|
|`mediaType`|String|참조된 개체의 MIME 형식입니다.|
|`size`|Int32|콘텐츠의 바이트 수입니다.|
|`digest`|String|콘텐츠의 다이제스트로, 레지스트리 V2 HTTP API 사양에 따라 정의됩니다.|
|`repository`|String|리포지토리 이름입니다.|
|`tag`|String|차트 태그 이름입니다.|
|`name`|String|차트 이름입니다.|
|`version`|String|차트 버전입니다.|

### <a name="payload-example-chart-push-event"></a>페이로드 예: 차트 푸시 이벤트

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

예제 [Azure CLI](/cli/azure/acr) 트리거하는 명령은 합니다 **chart_push** 이벤트 웹 후크:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>이벤트 삭제

경우는 이미지 리포지토리에 웹 후크 트리거 또는 매니페스트 삭제 됩니다. 태그가 삭제될 때는 트리거되지 않습니다.

### <a name="delete-event-payload"></a>삭제 이벤트 페이로드

|요소|Type|설명|
|-------------|----------|-----------|
|`id`|String|웹후크 이벤트의 ID입니다.|
|`timestamp`|DateTime|웹후크 이벤트가 트리거된 시점의 시간입니다.|
|`action`|String|웹후크 이벤트가 트리거된 작업입니다.|
|[대상](#delete_target)|복합 형식|웹후크 이벤트를 트리거한 이벤트의 대상입니다.|
|[요청](#delete_request)|복합 형식|웹후크 이벤트를 생성한 요청입니다.|

### <a name="delete_target"></a> 대상

|요소|Type|설명|
|------------------|----------|-----------|
|`mediaType`|String|참조된 개체의 MIME 형식입니다.|
|`digest`|String|콘텐츠의 다이제스트로, 레지스트리 V2 HTTP API 사양에 따라 정의됩니다.|
|`repository`|String|리포지토리 이름입니다.|

### <a name="delete_request"></a> 요청

|요소|Type|설명|
|------------------|----------|-----------|
|`id`|String|이벤트를 시작한 요청의 ID입니다.|
|`host`|String|외부에서 액세스할 수 있는 레지스트리 인스턴스의 호스트 이름으로, 들어오는 요청의 HTTP 호스트 헤더를 통해 지정됩니다.|
|`method`|String|이벤트를 생성한 요청 메서드입니다.|
|`useragent`|String|요청의 사용자 에이전트 헤더입니다.|

### <a name="payload-example-image-delete-event"></a>페이로드 예: 이미지 삭제 이벤트

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

**delete** 이벤트 웹후크를 트리거하는 예제 [Azure CLI](/cli/azure/acr) 명령:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>차트 삭제 이벤트

웹 후크 Helm 차트 또는 리포지토리를 삭제 하는 경우에 트리거됩니다. 

### <a name="chart-delete-event-payload"></a>차트 삭제 이벤트 페이로드

|요소|Type|설명|
|-------------|----------|-----------|
|`id`|String|웹후크 이벤트의 ID입니다.|
|`timestamp`|DateTime|웹후크 이벤트가 트리거된 시점의 시간입니다.|
|`action`|String|웹후크 이벤트가 트리거된 작업입니다.|
|[대상](#chart_delete_target)|복합 형식|웹후크 이벤트를 트리거한 이벤트의 대상입니다.|

### <a name="chart_delete_target"></a> 대상

|요소|Type|설명|
|------------------|----------|-----------|
|`mediaType`|String|참조된 개체의 MIME 형식입니다.|
|`size`|Int32|콘텐츠의 바이트 수입니다.|
|`digest`|String|콘텐츠의 다이제스트로, 레지스트리 V2 HTTP API 사양에 따라 정의됩니다.|
|`repository`|String|리포지토리 이름입니다.|
|`tag`|String|차트 태그 이름입니다.|
|`name`|String|차트 이름입니다.|
|`version`|String|차트 버전입니다.|

### <a name="payload-example-chart-delete-event"></a>페이로드 예: 차트 삭제 이벤트

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

예제 [Azure CLI](/cli/azure/acr) 트리거하는 명령은 합니다 **chart_delete** 이벤트 웹 후크:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>다음 단계

[Azure Container Registry 웹후크 사용](container-registry-webhook.md)