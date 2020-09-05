---
title: '빠른 시작: Ruby 앱 만들기'
description: 첫 번째 Ruby 앱을 App Service의 Linux 컨테이너에 배포하여 Azure App Service를 시작하세요.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 07/11/2019
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 49f2100386af21cee8f76403d7a2d2e4ac6b8f63
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961339"
---
# <a name="create-a-ruby-on-rails-app-in-app-service"></a>App Service에서 Ruby on Rails 앱 만들기

[Linux의 Azure App Service](overview.md#app-service-on-linux)는 Linux 운영 체제를 사용하여 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작 자습서에서는 [Cloud Shell](../cloud-shell/overview.md)을 사용하여 Linux의 App Service에 Ruby on Rails 앱을 배포하는 방법을 보여줍니다.

> [!NOTE]
> Ruby 개발 스택은 현재 Ruby on Rails만 지원합니다. Sinatra와 같은 다른 플랫폼을 사용하거나 지원되지 않는 Ruby 버전을 사용하려는 경우 [사용자 지정 컨테이너에서 실행](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux)해야 합니다.

![Hello-world](./media/quickstart-ruby/hello-world-configured.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Ruby 2.6 이상 설치</a>
* <a href="https://git-scm.com/" target="_blank">Git 설치</a>

## <a name="download-the-sample"></a>샘플 다운로드

터미널 창에서 다음 명령을 실행하여 로컬 컴퓨터에 샘플 앱 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/ruby-docs-hello-world
```

## <a name="run-the-application-locally"></a>로컬에서 애플리케이션 실행

애플리케이션을 로컬로 실행하여 Azure에 애플리케이션을 배포할 때 표시되는 모양을 확인합니다. 터미널 창을 열고, `hello-world` 디렉터리로 변경하고, `rails server` 명령을 사용하여 서버를 시작합니다.

첫 번째 단계는 필요한 Gem을 설치하는 것입니다. 샘플에 포함된 `Gemfile`이 있으므로 다음 명령을 실행하기만 하면 됩니다.

```bash
bundle install
```

Gem이 설치되면 번들러를 사용하여 앱을 시작합니다.

```bash
bundle exec rails server
```

웹 브라우저를 사용하여 `http://localhost:3000`으로 이동한 후 앱을 로컬로 테스트합니다.

![Hello World가 구성됨](./media/quickstart-ruby/hello-world-updated.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

기본 제공 이미지를 사용하여 새로 만든 웹앱을 보려면 앱으로 이동합니다. _&lt;앱 이름>_ 을 해당하는 웹앱 이름으로 바꿉니다.

```bash
http://<app_name>.azurewebsites.net
```

새로운 웹앱은 다음과 같아야 합니다.

![시작 페이지](./media/quickstart-ruby/splash-page.png)

## <a name="deploy-your-application"></a>애플리케이션 배포

다음 명령을 실행하여 Azure 웹앱에 로컬 애플리케이션을 배포합니다.

```bash
git remote add azure <Git deployment URL from above>
git push azure master
```

원격 배포 작업이 성공을 보고하는지 확인합니다. 이 명령은 다음 텍스트와 유사한 출력을 생성합니다.

```bash
remote: Using turbolinks 5.2.0
remote: Using uglifier 4.1.20
remote: Using web-console 3.7.0
remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
remote: Bundled gems are installed into `/tmp/bundle`
remote: Zipping up bundle contents
remote: .......
remote: ~/site/repository
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git
   a6e73a2..ae34be9  master -> master
```

배포가 완료되면 웹앱이 다시 시작될 때까지 10초 정도 기다린 후 웹앱으로 이동하여 결과를 확인합니다.

```bash
http://<app-name>.azurewebsites.net
```

![업데이트된 웹앱](./media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> 앱을 다시 시작하는 동안 브라우저 또는 `Hey, Ruby developers!` 기본 페이지에서 HTTP 상태 코드 `Error 503 Server unavailable`을 확인할 수 있습니다. 앱을 완전하게 다시 시작하는 데 몇 분 정도 걸릴 수 있습니다.
>

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Postgres과 Ruby on Rails](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Ruby 앱 구성](configure-language-ruby.md)