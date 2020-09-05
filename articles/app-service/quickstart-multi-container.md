---
title: '빠른 시작: 다중 컨테이너 앱 만들기'
description: 첫 번째 다중 컨테이너 앱을 배포하여 Azure App Service에서 다중 컨테이너 앱을 시작하세요.
keywords: Azure App Service, 웹앱, Linux, Docker, Compose, 다중 컨테이너, 다중-컨테이너, 컨테이너용 웹앱, 다중 컨테이너, 컨테이너, wordpress, azure db for mysql, 컨테이너를 포함한 프로덕션 데이터베이스
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 562185697f8f80fc52b06be80a9f15271f16c0a3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961468"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Docker Compose 구성을 사용하여 다중 컨테이너(미리 보기) 앱 만들기

> [!NOTE]
> 다중 컨테이너는 미리 보기 상태입니다.

[Web App for Containers](overview.md#app-service-on-linux)는 Docker 이미지를 사용할 수 있는 유연한 방법을 제공합니다. 이 빠른 시작은 Docker Compose 구성을 사용하여 [Cloud Shell](../cloud-shell/overview.md)에서 Web App for Containers에 다중 컨테이너 앱(미리 보기)을 배포하는 방법을 보여줍니다.

Cloud Shell에서 이 빠른 시작을 완료하지만 [Azure CLI](/cli/azure/install-azure-cli)(2.0.32 이상)를 사용하여 이러한 명령을 로컬로 실행할 수도 있습니다. 

![Web App for Containers의 다중 컨테이너 앱 샘플][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>샘플 다운로드

이 빠른 시작에서 [Docker](https://docs.docker.com/compose/wordpress/#define-the-project)의 Compose 파일을 사용합니다. 구성 파일은 [Azure 샘플](https://github.com/Azure-Samples/multicontainerwordpress)에 있습니다.

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Cloud Shell에서 quickstart 디렉터리를 만든 다음, 해당 디렉토리로 이동합니다.

```bash
mkdir quickstart

cd $HOME/quickstart
```

이어서 다음 명령을 실행하여 quickstart 디렉터리에 샘플 앱 리포지토리를 복제합니다. 그런 다음, `multicontainerwordpress` 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

Cloud Shell에서 [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *미국 중남부* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. **표준** 계층에서 Linux의 App Service에 지원되는 모든 위치를 확인하려면 [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) 명령을 실행합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

일반적으로 사용자와 가까운 지역에서 리소스 그룹 및 리소스를 만듭니다.

명령이 완료되면 JSON 출력이 리소스 그룹 속성을 보여줍니다.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service 계획 만들기

Cloud Shell에서 [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 명령을 사용하여 리소스 그룹에 App Service 계획을 만듭니다.

다음 예제에서는 **표준** 가격 책정 계층(`--sku S1`) 및 Linux 컨테이너(`--is-linux`)에서 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Docker Compose 앱 만들기

> [!NOTE]
> 현재 Azure App Services의 Docker Compose는 4,000자로 제한되어 있습니다.

Cloud Shell 터미널에서 [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 `myAppServicePlan` App Service 계획에 다중 컨테이너 [웹앱](overview.md#app-service-on-linux)을 만듭니다. _\<app_name>_ 를 고유한 앱 이름으로 바꿔야 합니다(유효한 문자는 `a-z`, `0-9` 및 `-`임).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>앱으로 이동

(`http://<app_name>.azurewebsites.net`)에 배포된 앱으로 이동합니다. 앱을 로드하는 데 몇 분 정도 걸릴 수 있습니다. 오류가 발생하면 몇 분 후에 브라우저를 새로 고칩니다.

![Web App for Containers의 다중 컨테이너 앱 샘플][1]

**축하합니다!** Web App for Containers에 다중 컨테이너 앱을 만들었습니다.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [사용자 지정 컨테이너 구성](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png