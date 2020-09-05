---
title: 사용자 지정 Linux 컨테이너에 대 한 CI/CD
description: Azure App Service에서 사용자 지정 Linux 컨테이너에 대 한 연속 배포를 설정 하는 방법을 알아봅니다. 연속 배포는 Docker 허브 및 ACR에 대해 지원 됩니다.
keywords: Azure App Service, Linux, OSS
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 11/08/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b73c7e62c6b621d3ac86cfb99b10b567460534f1
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961859"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers를 사용한 연속 배포

이 자습서에서는 관리 되는 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 리포지토리 또는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대 한 연속 배포를 구성 합니다.

## <a name="enable-continuous-deployment-with-acr"></a>ACR을 사용하여 지속적인 배포 활성화

![ACR webhook 스크린샷](./media/deploy-ci-cd-custom-container/ci-cd-acr-02.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 페이지의 왼쪽에서 **App Service** 옵션을 선택합니다.
3. 지속적인 배포를 구성하려는 앱의 이름을 선택합니다.
4. **컨테이너 설정** 페이지에서 **단일 컨테이너**를 선택합니다.
5. **Azure Container Registry**를 선택합니다.
6. **지속적인 배포 > 켜짐**을 선택합니다.
7. **저장**을 선택하여 지속적인 배포를 활성화합니다.

## <a name="use-the-acr-webhook"></a>ACR webhook 사용

지속적인 배포가 활성화되면 Azure Container Registry webhook 페이지에서 새로 만든 webhook를 볼 수 있습니다.

![ACR webhook 스크린샷](./media/deploy-ci-cd-custom-container/ci-cd-acr-03.png)

Container Registry에서 Webhook를 클릭하여 현재 webhook를 볼 수 있습니다.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Docker 허브(선택 사항)를 사용하여 지속적인 배포 활성화

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 페이지의 왼쪽에서 **App Service** 옵션을 선택합니다.
3. 지속적인 배포를 구성하려는 앱의 이름을 선택합니다.
4. **컨테이너 설정** 페이지에서 **단일 컨테이너**를 선택합니다.
5. **Docker 허브**를 선택합니다.
6. **지속적인 배포 > 켜짐**을 선택합니다.
7. **저장**을 선택하여 지속적인 배포를 활성화합니다.

![앱 설정 스크린샷](./media/deploy-ci-cd-custom-container/ci-cd-docker-02.png)

Webhook URL을 복사합니다. Docker 허브에 대한 webhook를 추가하려면 <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker 허브에 대한 webhook</a>를 따릅니다.

## <a name="automate-with-cli"></a>CLI로 자동화

Azure CLI를 사용 하 여 CI/CD를 구성 하려면 [az webapp deployment container config](/cli/azure/webapp/deployment/container?view=azure-cli-latest#az-webapp-deployment-container-config) 명령을 실행 하 여 webhook URL을 생성 합니다. URL은 DockerHub 또는 Azure Container Registry를 구성 하는 데 사용할 수 있습니다.

```azurecli-interactive
az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true
```

## <a name="next-steps"></a>다음 단계

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Linux의 App Service에서 .NET Core 웹앱 만들기](quickstart-dotnetcore.md?pivots=platform-linux)
* [Linux의 App Service에서 Ruby 웹앱 만들기](quickstart-ruby.md)
* [빠른 시작: App Service에서 사용자 지정 컨테이너 실행](quickstart-custom-container.md?pivots=container-linux)
* [Linux의 App Service FAQ](faq-app-service-linux.md)
* [사용자 지정 Linux 컨테이너 구성](configure-custom-container.md)