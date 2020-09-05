---
title: '빠른 시작: Azure CLI를 사용하여 Azure Static Web Apps를 통해 첫 번째 정적 웹앱 빌드'
description: Azure Static Web Apps CLI를 사용하여 Azure Static Web Apps 인스턴스를 빌드하는 방법을 알아봅니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 7e0fdbc50dd36e4ea23903a5929735c1c83bd394
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752872"
---
# <a name="quickstart-building-your-first-static-web-app-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 첫 번째 정적 웹앱 빌드

Azure Static Web Apps는 GitHub 리포지토리에서 앱을 빌드하여 프로덕션 환경에 웹 사이트를 게시합니다. 이 빠른 시작에서는 Azure CLI를 사용하여 웹 애플리케이션을 Azure Static 웹앱에 배포합니다.

Azure 구독이 아직 없는 경우 [평가판 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- [GitHub](https://github.com) 계정
- [GitHub 개인용 액세스 토큰](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) 계정
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 설치(버전 2.8.0 이상)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

다음으로, 다음 명령을 사용하여 새 폴더로 전환합니다.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>정적 웹앱 만들기

이제 리포지토리가 생성되었으므로 Azure CLI에서 정적 웹앱을 만들 수 있습니다.

> [!IMPORTANT]
> 터미널의 _my-first-static-web-app_ 폴더에 있는지 확인합니다.

1. 다음 명령을 사용하여 Azure CLI에 로그인합니다.

    ```bash
    az login
    ```

1. 리포지토리에서 새 정적 웹앱을 만듭니다.

    # <a name="no-framework"></a>[프레임워크 없음](#tab/vanilla-javascript)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```bash
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b master \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---

    - `<RESOURCE_GROUP_NAME>`: 이 값을 기존 Azure 리소스 그룹 이름으로 바꿉니다.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: 이 값을 GitHub 사용자 이름으로 바꿉니다.

    - `<LOCATION>`: 이 값을 가장 가까운 위치로 바꿉니다. 옵션은 다음과 같습니다. _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ 및 _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: 이 값을 이전에 생성한 [GitHub 개인용 액세스 토큰](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)으로 바꿉니다.

    이제 Azure에서 만든 앱을 볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

1. 위쪽 검색 창에서 **my-first-web-static-app**을 검색합니다.

1. **my-first-web-static-app**을 선택합니다.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않을 경우 다음 명령을 실행하여 Azure Static Web App 인스턴스를 삭제할 수 있습니다.

```bash
az staticwebapp delete \
    --name my-first-static-web-app
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [API 추가](add-api.md)
