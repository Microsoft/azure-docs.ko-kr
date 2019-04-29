---
title: Node.js 앱-Azure App Service 구성 | Microsoft Docs
description: Azure App Service에서 작업 하도록 Node.js 앱을 구성 하는 방법 알아보기
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850238"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux Node.js 앱 구성

모든 필수 NPM 종속성을 사용 하 여 Node.js 앱을 배포 되어야 합니다. App Service 배포 엔진 (Kudu) 자동으로 실행 `npm install --production` 배포한 경우에 [Git 리포지토리](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), 또는 [Zip 패키지](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) 켜져 빌드 프로세스를 사용 하 여. 그러나 사용 하 여 파일을 배포 하는 경우 [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), 필요한 패키지를 수동으로 업로드 해야 합니다.

이 가이드는 주요 개념 및 기본 제공 Linux 컨테이너를 사용 하 여 App Service에서 Node.js 개발자를 위한 지침을 제공 합니다. Azure App Service를 사용한 경험이 없는 경우에 따라 합니다 [Node.js 빠른 시작](quickstart-nodejs.md) 하 고 [MongoDB 자습서를 사용 하 여 Node.js](tutorial-nodejs-mongodb-app.md) 첫 번째입니다.

## <a name="show-nodejs-version"></a>Node.js 버전 표시

현재 Node.js 버전을 표시 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

모든 지원 되는 Node.js 버전을 표시 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Node.js 버전 설정

앱을 설정 하는 [지원 되는 Node.js 버전](#show-nodejs-version)에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

이 설정은 런타임 시 및 Kudu에서 자동화 된 패키지 복원 하는 동안 사용할 Node.js 버전을 지정 합니다.

> [!NOTE]
> 프로젝트의 Node.js 버전을 설정 해야 `package.json`합니다. 배포 엔진 지원 되는 모든 Node.js 버전을 포함 하는 별도 컨테이너에서 실행 됩니다.

## <a name="configure-nodejs-server"></a>Node.js 서버를 구성 합니다.

Node.js 컨테이너 수반 [PM2](http://pm2.keymetrics.io/), 프로덕션 프로세스 관리자입니다. PM2를 사용 하 여 NPM을 사용 하 여 또는 사용자 지정 명령을 사용 하 여 시작 하려면 앱을 구성할 수 있습니다.

- [사용자 지정 명령 실행](#run-custom-command)
- [Npm 시작 실행](#run-npm-start)
- [PM2를 사용 하 여 실행](#run-with-pm2)

### <a name="run-custom-command"></a>사용자 지정 명령 실행

App Service와 같은 실행 파일 등을 사용자 지정 명령을 사용 하 여 앱을 시작할 수 *run.sh*합니다. 예를 실행 하 여 `npm run start:prod`에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Npm 시작 실행

사용 하 여 앱을 시작 하려면 `npm start`, 확인을 `start` 스크립트를 *package.json* 파일. 예를 들면 다음과 같습니다.

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

사용자 지정을 사용 하도록 *package.json* 프로젝트에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>PM2를 사용 하 여 실행

컨테이너를 자동으로 앱을 시작 하 PM2를 사용 하 여 프로젝트에 있으면 일반적인 Node.js 파일 중 하나:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- 다음 중 하나 [PM2 파일](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *나오는 내용은 process.json* 고 *ecosystem.config.js*

또한 다음 확장을 사용 하 여 사용자 지정 시작 파일을 구성할 수 있습니다.

- A *.js* 파일
- A [PM2 파일](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) 확장명이 *.json*를 *. config.js*를 *.yaml*, 또는 *.yml*

사용자 지정 시작 파일을 추가 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>원격 디버깅

> [!NOTE]
> 원격 디버깅은 현재 미리 보기입니다.

Node.js 앱에서 원격으로 디버깅할 수 있습니다 [Visual Studio Code](https://code.visualstudio.com/) 되도록를 구성 하는 경우 [PM2를 사용 하 여 실행](#run-with-pm2)를 실행할 때 사용 하 여 제외 하 고는 *. config.js, *.yml, 또는 *.yaml*합니다.

대부분의 경우에서 추가 구성이 필요 하지 않습니다 앱에 대 한 합니다. 사용 하 여 앱이 실행 하는 경우는 *나오는 내용은 process.json* 파일 (기본 또는 사용자 지정) 권한이 있어야 합니다를 `script` JSON 루트에 있는 속성입니다. 예를 들면 다음과 같습니다.

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

원격 디버깅을 위해 Visual Studio Code를 설정 하려면 설치 합니다 [App Service 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)합니다. 확장 페이지의 지침을 따르고 Visual Studio Code에서 Azure에 로그인 합니다.

Azure 탐색기에서 디버깅 하 고 마우스 오른쪽 단추로 클릭 하 고 선택 하려는 앱을 찾으려는 **원격 디버깅 시작**합니다. 클릭 **예** 앱에 대 한 사용 하도록 설정 합니다. App Service 터널 프록시를 시작 하 고 디버거를 연결 합니다. 그런 다음 앱에서 요청을 하 고 디버거가 중단점에서 일시 중지를 볼 수 있습니다.

디버깅 완료 되 면 선택 하 여 디버거를 중지 **연결 끊기**합니다. 클릭 해야 대화 상자가 나타나면 **예** 원격 디버깅을 사용 하지 않도록 설정 합니다. 사용 하지 않도록 나중에 Azure 탐색기에서 다시 응용 프로그램을 마우스 오른쪽 단추로 클릭 하 고 선택 **원격 디버깅 사용 안 함**합니다.

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)을 지정할 수 있습니다. 그런 다음 표준 Node.js 패턴을 사용 하 여 액세스할 수 있습니다. 예를 들어 앱 설정 `NODE_ENV`에 액세스하려면 다음 코드를 사용합니다.

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Gulp/Grunt/Bower 실행

기본적으로 Kudu 실행 `npm install --production` 경우 것을 인식 Node.js 앱을 배포 합니다. 제공 해야 하는 앱, Grunt, Bower, Gulp 등 인기 있는 자동화 도구 중 하나를 요구 하는 경우는 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) 실행 합니다.

이러한 도구를 실행 하 여 저장소를 사용 하도록 설정 하려면 종속성에 추가할 *package.json 합니다.* 예를 들면 다음과 같습니다.

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

로컬 터미널 창에서 리포지토리 루트에 디렉터리를 변경 하 고 다음 명령을 실행 합니다.

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

리포지토리 루트에는 이제 두 개의 추가 파일에: *.deployment* 하 고 *deploy.sh*합니다.

오픈 *deploy.sh* 찾고는 `Deployment` 섹션에서는 다음과 같은:

```bash
##################################################################################################################################
# Deployment
# ----------
```

이 섹션에서는 실행 끝나는 `npm install --production`합니다. 필요한 도구를 실행 해야 하는 코드 섹션을 추가 *끝* 의 `Deployment` 섹션:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

참조를 [MEAN.js 샘플에서 예제](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)배포 스크립트를 사용자 지정도 실행 되는 위치, `npm install` 명령입니다.

### <a name="bower"></a>Bower

이 코드 조각을 실행 `bower install`합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

이 코드 조각을 실행 `gulp imagemin`합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

이 코드 조각을 실행 `grunt`합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [Express](https://expressjs.com/)를 사용할 수 있습니다 [프록시 트러스트](http://expressjs.com/guide/behind-proxies.html)합니다. 예를 들면 다음과 같습니다.

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>문제 해결

작업 중인 Node.js 앱이 App Service에서 다르게 동작 또는 오류가 하는 경우 다음과 같이 하세요.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 앱을 로컬로 테스트 합니다. App Service는 프로젝트가 프로덕션 모드로 로컬로 예상 대로 작동 하는지 확인 해야 하므로 프로덕션 모드에서 Node.js 앱을 실행 합니다. 예를 들면 다음과 같습니다.
    - 에 따라 프로그램 *package.json*, 프로덕션 모드에 대 한 서로 다른 패키지를 설치할 수 있습니다 (`dependencies` 비교 `devDependencies`).
    - 특정 웹 프레임 워크는 다르게 프로덕션 모드에서에서 정적 파일을 배포할 수 있습니다.
    - 프로덕션 모드에서 실행 하는 경우 특정 웹 프레임 워크에서 사용자 지정 시작 스크립트를 사용할 수 있습니다.
- 개발 모드에서 App Service에서 앱을 실행 합니다. 예를 들어 [MEAN.js](http://meanjs.org/), 런타임에서에서 개발 모드에 앱을 설정할 수 있습니다 [설정 합니다 `NODE_ENV` 앱 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MongoDB를 사용하는 Node.js 앱](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)