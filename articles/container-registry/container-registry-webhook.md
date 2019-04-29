---
title: Azure Container Registry 웹후크
description: 레지스트리 리포지토리 중 하나에서 특정 작업이 수행되는 경우 웹후크를 사용하여 이벤트를 트리거하는 방법을 알아봅니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 0a3d2d0e858dc052095c0a58287970d10c06f0ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787271"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry 웹후크 사용

Azure Container Registry는 Docker 허브에서 공개 Docker 이미지를 저장하는 것과 유사한 방식으로 개인 Docker 컨테이너 이미지를 저장하고 관리합니다. 에 대 한 리포지토리를 호스트할 수도 있습니다 [Helm 차트](container-registry-helm-repos.md) (미리 보기)는 패키징 형식을 Kubernetes에 응용 프로그램을 배포 합니다. 레지스트리 리포지토리 중 하나에서 특정 작업이 수행되는 경우 웹후크를 사용하여 이벤트를 트리거할 수 있습니다. 웹후크는 레지스트리 수준에서 이벤트에 응답하거나 특정 리포지토리 태그로 범위를 줄일 수 있습니다.

Webhook 요청에 대한 세부 정보는 [Azure Container Registry 웹후크 스키마 참조](container-registry-webhook-reference.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure Container Registry - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다. 합니다 [Azure Container Registry Sku](container-registry-skus.md) 다양 한 웹 후크 할당량을 갖습니다.
* Docker CLI - 로컬 컴퓨터를 Docker 호스트로 설정하고 Docker CLI 명령에 액세스하려면 [Docker 엔진](https://docs.docker.com/engine/installation/)을 설치합니다.

## <a name="create-webhook---azure-portal"></a>웹 후크-Azure portal 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 웹후크를 만들려는 컨테이너 레지스트리로 이동합니다.
1. 아래 **Services**를 선택 **Webhook**합니다.
1. 웹후크 도구 모음에서 **추가**를 선택합니다.
1. 다음 정보로 *웹후크 만들기* 양식을 완성합니다.

| 값 | Description |
|---|---|
| 이름 | 웹후크에 지정하려는 이름입니다. 문자 및 숫자만 포함할 수 있으며 길이 5-50 자 여야 합니다. |
| 서비스 URI | 웹후크가 POST 알림을 보내야 하는 URI입니다. |
| 사용자 지정 헤더 | POST 요청과 함께 전달하려는 헤더입니다. "키: 값" 형식이어야 합니다. |
| 트리거 동작 | 웹후크를 트리거하는 동작입니다. 작업에 포함 이미지 푸시, 이미지 삭제, Helm 차트 푸시, Helm 차트 삭제 이미지 격리 됩니다. 웹 후크 트리거를 하나 이상의 작업을 선택할 수 있습니다. |
| 상태 | 웹후크가 만들어진 후의 상태입니다. 기본적으로 사용하도록 설정되어 있습니다. |
| 범위 | 웹후크가 작동하는 범위입니다. 지정 하지 않으면 범위는 레지스트리의 모든 이벤트입니다. "리포지토리: 태그" 형식을 사용 하 여 리포지토리 또는 태그를 지정할 수 있습니다 또는 "리포지토리: *" 저장소에서 모든 태그에 대 한 합니다. |

웹후크 양식 예제:

![Azure Portal의 ACR 웹후크 만들기 UI](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>웹 후크-Azure CLI 만들기

Azure CLI를 사용하여 웹후크를 만들려면 [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) 명령을 사용합니다. 다음 명령은 모든 이미지 레지스트리의 delete 이벤트에 대 한 웹 후크를 만듭니다 *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>웹후크 테스트

### <a name="azure-portal"></a>Azure portal

웹 후크를 사용 하기 전에 사용 하 여 테스트할 수 있습니다 합니다 **Ping** 단추입니다. Ping은 지정된 엔드포인트에 일반 POST 요청을 보내고 응답을 기록합니다. ping 기능을 사용하면 웹후크를 올바르게 구성했는지 확인하는 데 도움이 될 수 있습니다.

1. 테스트하려는 웹후크를 선택합니다.
2. 맨 위의 도구 모음에서 **Ping**을 선택합니다.
3. **HTTP 상태** 열에서 엔드포인트의 응답을 확인합니다.

![Azure Portal의 ACR 웹후크 만들기 UI](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 ACR 웹후크를 테스트하려면 [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) 명령을 사용합니다.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

결과를 보려면 [az acr webhook list-events](/cli/azure/acr/webhook) 명령을 사용합니다.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>웹후크 삭제

### <a name="azure-portal"></a>Azure portal

각 웹후크는 Azure Portal에서 웹후크를 선택하고 **삭제** 단추를 선택하여 삭제할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>다음 단계

### <a name="webhook-schema-reference"></a>Webhook 스키마 참조

Azure Container Registry에서 내보낸 JSON 이벤트 페이로드의 형식 및 속성에 대한 내용은 webhook 스키마 참조를 참조하세요.

[Azure Container Registry 웹후크 스키마 참조](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid 이벤트

이 문서에서 설명한 기본 레지스트리 webhook 이벤트 외에도 Azure Container Registry는 Event Grid로 이벤트를 내보낼 수 있습니다.

[빠른 시작: Event Grid로 컨테이너 레지스트리 이벤트 전송](container-registry-event-grid-quickstart.md)
