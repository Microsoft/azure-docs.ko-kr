---
title: 빠른 시작-Event Grid로 이벤트 보내기
description: 이 빠른 시작에서는 컨테이너 레지스트리에 Event Grid 이벤트를 활성화한 다음, 애플리케이션 예제에 컨테이너 이미지 push 및 delete 이벤트를 전송합니다.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18
ms.openlocfilehash: 1ff9572cf8614e3eb5d015a602ca3f878875a0a4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455354"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>빠른 시작: 개인 컨테이너 레지스트리의 이벤트를 Event Grid으로 보내기

Azure Event Grid는 게시-구독 모델을 사용하여 균일한 이벤트 소비를 제공하는 완전히 관리되는 이벤트 라우팅 서비스입니다. 이 빠른 시작에서는 Azure CLI를 사용하여 컨테이너 레지스트리를 만들고, 레지스트리 이벤트를 구독한 다음, 샘플 웹 애플리케이션을 배포하여 이벤트를 수신합니다. 마지막으로, 컨테이너 이미지 `push` 및 `delete` 이벤트를 트리거하고 애플리케이션 예제에서 이벤트 페이로드를 봅니다.

이 문서의 단계를 완료하면 컨테이너 레지스트리에서 Event Grid로 전송된 이벤트가 샘플 웹앱에 표시됩니다.

![수신된 이벤트 세 개로 샘플 웹 애플리케이션을 렌더링하는 웹 브라우저][sample-app-01]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][azure-account] 을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

이 문서에서 Azure CLI 명령은 **Bash** 셸에서 실행하도록 형식이 지정됩니다. PowerShell 또는 명령 프롬프트와 같은 다른 셸을 사용하는 경우 줄 연속 문자 또는 변수 할당 줄을 적절히 조정해야 합니다. 이 문서에서는 변수를 사용하여 필요한 명령 편집 작업을 최소화합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리 컨테이너입니다. 다음 [az group create][az-group-create] 명령은 *E미국* 지역에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다. 리소스 그룹에 다른 이름을 사용하려면 `RESOURCE_GROUP_NAME`을 다른 값으로 설정합니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>컨테이너 레지스트리 만들기

다음으로, 다음 명령을 사용하여 리소스 그룹에 컨테이너 레지스트리를 배포합니다. [Az acr create][az-acr-create] 명령을 실행 하기 전에 `ACR_NAME`를 레지스트리의 이름으로 설정 합니다. 이 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자로 제한됩니다.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

레지스트리가 만들어지면 Azure CLI에서 다음과 유사한 출력을 반환합니다.

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>이벤트 엔드포인트 만들기

이 섹션에서는 GitHub 리포지토리에 있는 Resource Manager 템플릿을 사용하여 Azure App Service에 미리 작성된 샘플 웹 애플리케이션을 배포합니다. 나중에 레지스트리의 Event Grid 이벤트를 구독하고 이벤트가 전송되는 엔드포인트로 이 앱을 지정합니다.

샘플 앱을 배포하려면 `SITE_NAME`을 웹앱의 고유한 이름으로 설정하고 다음 명령을 실행합니다. 사이트 이름은 웹앱의 FQDN(정규화된 도메인 이름)의 일부를 형성하기 때문에 Azure 내에서 고유해야 합니다. 이후 섹션에서 레지스트리의 이벤트를 보려면 웹 브라우저에서 앱의 FQDN으로 이동합니다.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

배포에 성공하면(몇 분 정도 걸릴 수 있음) 브라우저를 열고 웹앱으로 이동하여 실행 중인지 확인합니다.

`http://<your-site-name>.azurewebsites.net`

이벤트가 메시지가 표시되지 않고 렌더링된 샘플 앱이 표시되어야 합니다.

![이벤트가 표시되지 않고 샘플 웹앱을 보여주는 웹 브라우저][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>레지스트리 이벤트 구독

Event Grid에서 *항목*을 구독하여 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다. 다음 [az event grid 이벤트-subscription create][az-eventgrid-event-subscription-create] 명령은 사용자가 만든 컨테이너 레지스트리를 구독 하 고 웹 앱의 URL을 이벤트를 보내야 하는 엔드포인트으로 지정 합니다. 이전 섹션에서 채워진 환경 변수가 여기에 재사용되므로 편집이 필요 없습니다.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

구독이 완료되면 다음과 비슷한 출력이 표시되어야 합니다.

```JSON
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>레지스트리 이벤트 트리거

샘플 앱이 작동 중이고 Event Grid를 사용하여 레지스트리를 구독했으므로 일부 이벤트를 생성할 수 있습니다. 이 섹션에서는 ACR Tasks를 사용하여 컨테이너 이미지를 빌드하고 레지스트리에 푸시합니다. ACR Tasks는 로컬 컴퓨터에 Docker Engine을 설치하지 않고도 클라우드에서 컨테이너 이미지를 빌드할 수 있게 해주는 Azure Container Registry의 기능입니다.

### <a name="build-and-push-image"></a>이미지 빌드 및 푸시

GitHub 리포지토리의 콘텐츠에서 컨테이너 이미지를 빌드하려면 다음 Azure CLI 명령을 실행합니다. 기본적으로 ACR Tasks는 성공적으로 빌드된 이미지를 레지스트리에 자동으로 푸시하여 `ImagePushed` 이벤트를 생성합니다.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

ACR Tasks가 이미지를 빌드하고 푸시하는 동안에는 다음과 유사한 출력이 표시되어야 합니다. 다음 샘플 출력은 너무 길어서 일부분이 잘렸습니다.

```console
$ az acr build -r $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

빌드된 이미지가 레지스트리에 있는지 확인하려면 다음 명령을 실행하여 "myimage" 리포지토리에서 태그를 봅니다.

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

빌드한 이미지의 "v1" 태그가 다음과 유사한 출력으로 표시되어야 합니다.

```console
$ az acr repository show-tags --name $ACR_NAME --repository myimage
[
  "v1"
]
```

### <a name="delete-the-image"></a>이미지 삭제

이제 [az acr repository delete][az-acr-repository-delete] 명령을 사용 하 여 이미지를 삭제 하 여 `ImageDeleted` 이벤트를 생성 합니다.

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

매니페스트와 관련 이미지 삭제를 확인하는 다음과 유사한 출력이 표시되어야 합니다.

```console
$ az acr repository delete --name $ACR_NAME --image myimage:v1
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): y
```

## <a name="view-registry-events"></a>레지스트리 이벤트 보기

이제 이미지가 레지스트리에 푸시된 후 삭제되었습니다. Event Grid Viewer 웹앱으로 이동하면 `ImageDeleted` 및 `ImagePushed` 이벤트가 모두 표시되어야 합니다. 또한 [레지스트리 이벤트 구독](#subscribe-to-registry-events) 섹션에서 명령을 실행하여 생성된 구독 유효성 검사 이벤트도 볼 수 있습니다.

다음 스크린샷에서는 이벤트 세 개가 있는 샘플 앱을 보여주며, `ImageDeleted` 이벤트는 세부 정보를 표시하기 위해 확장됩니다.

![ImagePushed 및 ImageDeleted 이벤트가 있는 샘플 앱을 보여주는 웹 브라우저][sample-app-03]

축하합니다! `ImagePushed` 및 `ImageDeleted` 이벤트를 보면 레지스트리가 Event Grid로 이벤트를 보내고 있고, Event Grid가 이러한 이벤트를 웹앱 엔드포인트로 전달하고 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스를 다 사용했으면 다음 Azure CLI 명령을 사용하여 모두 삭제할 수 있습니다. 리소스 그룹을 삭제하면 해당 그룹에 포함된 모든 리소스가 영구적으로 삭제됩니다.

**경고**: 이 작업은 되돌릴 수 없습니다. 이 명령을 실행하기 전에 해당 그룹의 리소스가 더 이상 필요하지 않은지 확인하세요.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

Event Grid 설명서에서 Azure Container Registry 이벤트 메시지 스키마 참조를 확인할 수 있습니다.

[Container Registry에 대한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 컨테이너 레지스트리를 배포하고, ACR Tasks로 이미지를 빌드한 후 삭제하고, Event Grid에서 애플리케이션 예제로 레지스트리의 이벤트를 사용했습니다. 이제 ACR Tasks 자습서로 넘어가서 기본 이미지 업데이트에 대한 자동 빌드 등 클라우드에 컨테이너 이미지를 빌드하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [ACR Tasks를 사용하여 클라우드에 컨테이너 이미지 빌드](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
