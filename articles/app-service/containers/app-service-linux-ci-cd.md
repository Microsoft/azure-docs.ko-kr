---
title: Web App for Containers를 사용한 지속적인 배포 - Azure App Service | Microsoft Docs
description: Web App for Containers에서 지속적인 배포를 설정하는 방법
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2018
ms.author: yili
ms.custom: seodec18
ms.openlocfilehash: 4acadc4c08ef50e7d52303689c38c43f81187669
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852524"
---
# <a name="continuous-deployment-with-web-app-for-containers"></a>Web App for Containers를 사용한 연속 배포

이 자습서에서는 관리되는 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) 리포지토리 또는 [Docker 허브](https://hub.docker.com)에서 사용자 지정 컨테이너 이미지에 대한 연속 배포를 구성합니다.

## <a name="enable-continuous-deployment-with-acr"></a>ACR을 사용하여 지속적인 배포 활성화

![ACR webhook 스크린샷](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-02.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 페이지의 왼쪽에서 **App Service** 옵션을 선택합니다.
3. 지속적인 배포를 구성하려는 앱의 이름을 선택합니다.
4. **컨테이너 설정** 페이지에서 **단일 컨테이너**를 선택합니다.
5. **Azure Container Registry**를 선택합니다.
6. **지속적인 배포 > 켜짐**을 선택합니다.
7. **저장**을 선택하여 지속적인 배포를 활성화합니다.

## <a name="use-the-acr-webhook"></a>ACR webhook 사용

지속적인 배포가 활성화되면 Azure Container Registry webhook 페이지에서 새로 만든 webhook를 볼 수 있습니다.

![ACR webhook 스크린샷](./media/app-service-webapp-service-linux-ci-cd/ci-cd-acr-03.png)

Container Registry에서 Webhook를 클릭하여 현재 webhook를 볼 수 있습니다.

## <a name="enable-continuous-deployment-with-docker-hub-optional"></a>Docker 허브(선택 사항)를 사용하여 지속적인 배포 활성화

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 페이지의 왼쪽에서 **App Service** 옵션을 선택합니다.
3. 지속적인 배포를 구성하려는 앱의 이름을 선택합니다.
4. **컨테이너 설정** 페이지에서 **단일 컨테이너**를 선택합니다.
5. **Docker 허브**를 선택합니다.
6. **지속적인 배포 > 켜짐**을 선택합니다.
7. **저장**을 선택하여 지속적인 배포를 활성화합니다.

![앱 설정 스크린샷](./media/app-service-webapp-service-linux-ci-cd/ci-cd-docker-02.png)

Webhook URL을 복사합니다. Docker 허브에 대한 webhook를 추가하려면 <a href="https://docs.docker.com/docker-hub/webhooks/" target="_blank">Docker 허브에 대한 webhook</a>를 따릅니다.

## <a name="next-steps"></a>다음 단계

* [Linux의 Azure App Service 소개](./app-service-linux-intro.md)
* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [Linux의 App Service에서 .NET Core 웹앱 만들기](quickstart-dotnetcore.md)
* [Linux의 App Service에서 Ruby 웹앱 만들기](quickstart-ruby.md)
* [Web App for Containers에 Docker/Go 웹앱 배포](quickstart-docker-go.md)
* [Linux의 Azure App Service에 대한 FAQ](./app-service-linux-faq.md)
* [Azure CLI를 사용하여 Web App for Containers 관리](./app-service-linux-cli.md)
