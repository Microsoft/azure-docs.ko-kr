---
title: Azure SignalR Service에서 Event Grid로 이벤트를 보내는 방법
description: SignalR Service에 대한 Event Grid 이벤트를 사용하도록 설정한 다음 클라이언트 연결의 연결/연결 끊김 이벤트를 샘플 애플리케이션으로 보내는 방법을 보여 주는 가이드입니다.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562647"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Azure SignalR Service에서 Event Grid로 이벤트를 보내는 방법

Azure Event Grid는 게시-구독 모델을 사용하여 균일한 이벤트 소비를 제공하는 완전 관리형 이벤트 라우팅 서비스입니다. 이 가이드에서는 Azure CLI를 사용하여 Azure SignalR Service를 만들고, 연결 이벤트를 구독하고, 이벤트를 수신하는 샘플 웹 애플리케이션을 배포합니다. 마지막으로 샘플 애플리케이션에서 연결하고 연결을 끊고 이벤트 페이로드를 볼 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - 이 문서에서 Azure CLI 명령은 **Bash** 셸에서 실행하도록 형식이 지정됩니다. PowerShell 또는 명령 프롬프트와 같은 다른 셸을 사용하는 경우 줄 연속 문자 또는 변수 할당 줄을 적절히 조정해야 합니다. 이 문서에서는 변수를 사용하여 필요한 명령 편집 작업을 최소화합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스를 배포하고 관리하는 논리 컨테이너입니다. 다음 [az group create][az-group-create] 명령에서는 *eastus* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 리소스 그룹에 다른 이름을 사용하려면 `RESOURCE_GROUP_NAME`을 다른 값으로 설정합니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR Service 만들기

다음으로, 다음 명령을 사용하여 리소스 그룹에 Azure SignalR Service를 배포합니다.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

SignalR Service가 만들어지면 Azure CLI에서 다음과 유사한 출력을 반환합니다.

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>이벤트 엔드포인트 만들기

이 섹션에서는 GitHub 리포지토리에 있는 Resource Manager 템플릿을 사용하여 Azure App Service에 미리 작성된 샘플 웹 애플리케이션을 배포합니다. 나중에 레지스트리의 Event Grid 이벤트를 구독하고 이벤트가 전송되는 엔드포인트로 이 앱을 지정합니다.

샘플 앱을 배포하려면 `SITE_NAME`을 웹앱의 고유한 이름으로 설정하고 다음 명령을 실행합니다. 사이트 이름은 웹앱의 FQDN(정규화된 도메인 이름)의 일부를 형성하기 때문에 Azure 내에서 고유해야 합니다. 이후 섹션에서 레지스트리의 이벤트를 보려면 웹 브라우저에서 앱의 FQDN으로 이동합니다.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

배포에 성공하면(몇 분 정도 걸릴 수 있음) 브라우저를 열고 웹앱으로 이동하여 실행 중인지 확인합니다.

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>레지스트리 이벤트 구독

Event Grid에서 *항목* 을 구독하여 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다. 다음 [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] 명령은 사용자가 생성한 Azure SignalR Service를 구독하고, 이벤트를 보내야 하는 엔드포인트로 사용자의 웹앱 URL을 지정합니다. 이전 섹션에서 채워진 환경 변수가 여기에 재사용되므로 편집이 필요 없습니다.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

구독이 완료되면 다음과 비슷한 출력이 표시되어야 합니다.

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>레지스트리 이벤트 트리거

서비스 모드를 `Serverless Mode` 모드로 전환하고 SignalR Service에 대한 클라이언트 연결을 설정합니다. [서버리스 샘플](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)을 참조로 사용할 수 있습니다.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>레지스트리 이벤트 보기

이제 클라이언트가 SignalR Service에 연결되었습니다. Event Grid Viewer 웹앱으로 이동하면 `ClientConnectionConnected` 이벤트가 표시되어야 합니다. 클라이언트를 종료하면 `ClientConnectionDisconnected` 이벤트도 표시됩니다.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
