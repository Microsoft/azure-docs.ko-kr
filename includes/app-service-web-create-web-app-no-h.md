---
title: 포함 파일
description: 포함 파일
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 9f7c82a2bd35f06da096fa0dd9d5d1fa4d08011e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838931"
---
Cloud Shell에서 `myAppServicePlan` App Service 계획에 [웹앱](../articles/app-service/overview.md)을 만듭니다. 이 작업은 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 명령을 사용하여 수행할 수 있습니다. 다음 예제에서는 바꿉니다  *\<앱 이름 >* 전역적으로 고유한 앱 이름으로 (유효한 문자는 `a-z`에 `0-9`, 및 `-`).

```azurecli-interactive
az webapp create --name <app-name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

git 배포를 활성화하여 빈 웹앱을 만들었습니다.

> [!NOTE]
> Git 원격의 URL은 `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git` 형식으로 `deploymentLocalGitUrl` 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.
>

새로 만든 웹앱으로 이동합니다.

```
http://<app-name>.azurewebsites.net
```

새로운 웹앱은 다음과 같아야 합니다.
