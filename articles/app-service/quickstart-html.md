---
title: '빠른 시작: 정적 HTML 웹앱 만들기'
description: 몇 분 안에 첫 번째 HTML Hello World를 Azure App Service에 배포합니다. App Service에 배포하는 여러 가지 방법 중 하나인 Git를 사용하여 배포합니다.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: d624d332fa35bf458e9158e00aaf83ce495b9061
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961346"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Azure에서 정적 HTML 웹앱 만들기

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작에서는 기본적인 HTML+CSS 사이트를 Azure App Service에 배포하는 방법을 보여줍니다. 여기에서는 [Cloud Shell](../cloud-shell/overview.md)을 사용해서 빠른 시작을 실행하지만 [Azure CLI](/cli/azure/install-azure-cli)를 사용하여 여기에서 설명하는 명령들을 로컬에서도 실행할 수도 있습니다.

![샘플 앱의 홈 페이지](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>샘플 다운로드

Cloud Shell에서 quickstart 디렉터리를 만든 다음, 해당 디렉토리로 이동합니다.

```bash
mkdir quickstart

cd $HOME/quickstart
```

이어서 다음 명령을 실행하여 quickstart 디렉터리에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>웹앱 만들기

샘플 코드가 들어있는 디렉토리로 이동한 후 `az webapp up` 명령을 실행합니다. 다음 명령에서 <app_name>을 고유한 앱 이름으로 바꿉니다. 정적 콘텐츠는 `--html` 플래그로 표시됩니다.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

`az webapp up` 명령에는 다음 작업이 포함됩니다.

- 기본 리소스 그룹 만들기

- 기본 앱 서비스 계획 만들기

- 지정된 이름의 앱 만들기

- 현재 작업 디렉터리에서 웹앱까지의 [Zip 배포](./deploy-zip.md) 파일

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.

<pre>
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
</pre>

`resourceGroup` 값을 기록해 둡니다. [리소스 정리](#clean-up-resources) 섹션에서 필요합니다.

## <a name="browse-to-the-app"></a>앱으로 이동

브라우저에서 앱 URL(`http://<app_name>.azurewebsites.net`)로 이동합니다.

이 페이지는 Azure App Service 웹앱으로 실행됩니다.

![샘플 앱 홈 페이지](media/quickstart-html/hello-world-in-browser-az.png)

**축하합니다.** App Service에 첫 번째 HTML 앱을 배포했습니다.

## <a name="update-and-redeploy-the-app"></a>앱 업데이트 및 다시 배포

Cloud Shell에서 `nano index.html`을 입력하여 Nano 텍스트 편집기를 엽니다. 아래와 같이 `<h1>` 제목 태그에서 "Azure App Service - 샘플 정적 HTML 사이트"를 "Azure App Service"로 변경합니다.

![Nano index.html](media/quickstart-html/nano-index-html.png)

변경 내용을 저장하고 Nano를 종료합니다. `^O` 명령을 사용하여 저장하고 `^X` 명령을 사용하여 종료합니다.

이제 동일한 `az webapp up` 명령을 사용하여 앱을 다시 배포합니다.

```bash
az webapp up --location westeurope --name <app_name> --html
```

배포가 완료되면 **앱으로 이동** 단계에서 열린 브라우저 창으로 다시 전환하고 페이지를 새로 고칩니다.

![업데이트된 샘플 앱 홈 페이지](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>새 Azure 앱 관리

만든 웹앱을 관리하려면 [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색하여 선택합니다. 

![Azure Portal에서 App Services 선택](./media/quickstart-html/portal0.png)

**App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/quickstart-html/portal1.png)

웹앱의 개요 페이지가 표시됩니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

![Azure Portal의 App Service 블레이드](./media/quickstart-html/portal2.png)

왼쪽 메뉴에는 앱을 구성할 수 있는 여러 페이지가 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 생가 생각되는 경우 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다. 리소스 그룹 이름은 [웹앱 만들기](#create-a-web-app) 단계에서 자동으로 생성된 것입니다.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 도메인 매핑](app-service-web-tutorial-custom-domain.md)