---
title: Ruby 앱 구성 - Azure App Service
description: 앱에 대해 미리 빌드된 Ruby 컨테이너를 구성하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.topic: quickstart
ms.date: 06/18/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18
ms.openlocfilehash: c822dbdf9940db7b38d354fa32906c16977df0c0
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88083769"
---
# <a name="configure-a-linux-ruby-app-for-azure-app-service"></a>Azure App Service용 Linux Ruby 앱 구성

이 문서에서는 [Azure App Service](overview.md)가 Linux 컨테이너에서 Ruby 앱을 실행하는 방법 및 필요한 경우 App Service의 동작을 사용자 지정하는 방법에 대해 설명합니다. Ruby 앱은 필요한 [gems](https://rubygems.org/gems)를 모두 사용하여 배포해야 합니다.

이 가이드에서는 App Service에 기본 제공된 Linux 컨테이너를 사용하는 Ruby 개발자를 위한 주요 개념과 지침을 제공합니다. Azure App Service를 사용한 경험이 없는 경우 먼저 [Ruby 빠른 시작](quickstart-ruby.md) 및 [PostgreSQL을 사용하는 Ruby 자습서](tutorial-ruby-postgres-app.md)를 수행해야 합니다.

## <a name="show-ruby-version"></a>Ruby 버전 표시

현재 Ruby 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 Ruby 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep RUBY
```

대신 사용자 고유의 컨테이너 이미지를 빌드하여 지원되지 않는 Ruby 버전을 실행할 수도 있습니다. 자세한 내용은 [사용자 지정 Docker 이미지 사용](tutorial-custom-container.md?pivots=container-linux)을 참조하세요

## <a name="set-ruby-version"></a>Ruby 버전 설정

Ruby 버전을 2.3으로 설정하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "RUBY|2.3"
```

> [!NOTE]
> 배포 중에 다음과 비슷한 오류가 표시되는 경우:
> ```
> Your Ruby version is 2.3.3, but your Gemfile specified 2.3.1
> ```
> 또는
> ```
> rbenv: version `2.3.1' is not installed
> ```
> 즉 프로젝트에 구성된 Ruby 버전이 실행 중인 컨테이너에 설치된 버전과 다릅니다(위의 예제에서 `2.3.3`). 위의 예제에서 *Gemfile* 및 *.ruby-version*을 모두 확인하고, Ruby 버전이 설정되어 있지 않거나 실행 중인 컨테이너에 설치된 버전으로 설정되어 있는지 확인합니다(위의 예제에서 `2.3.3`).

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](configure-common.md#configure-app-settings)을 지정할 수 있습니다. 그런 다음, 표준 [ENV['\<path-name>']](https://ruby-doc.org/core-2.3.3/ENV.html) 패턴을 사용하여 액세스할 수 있습니다. 예를 들어 앱 설정 `WEBSITE_SITE_NAME`에 액세스하려면 다음 코드를 사용합니다.

```ruby
ENV['WEBSITE_SITE_NAME']
```

## <a name="customize-deployment"></a>배포 사용자 지정

빌드 프로세스가 작동하는 상태에서 [Git 리포지토리](deploy-local-git.md) 또는 [Zip 패키지](deploy-zip.md)를 배포하면 기본적으로 배포 엔진(Kudu)에서 다음과 같은 배포 후 단계를 자동으로 실행합니다.

1. *Gemfile*이 있는지 확인합니다.
1. `bundle clean`을 실행합니다. 
1. `bundle install --path "vendor/bundle"`을 실행합니다.
1. `bundle package`를 실행하여 gem을 vendor/cache 폴더에 패키지합니다.

### <a name="use---without-flag"></a>--without 플래그 사용

[--without](https://bundler.io/man/bundle-install.1.html) 플래그를 사용하여 `bundle install`을 실행하려면 `BUNDLE_WITHOUT` [앱 설정](configure-common.md#configure-app-settings)을 쉼표로 구분된 그룹 목록으로 설정합니다. 예를 들어 다음 명령은 플래그를 `development,test`로 설정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings BUNDLE_WITHOUT="development,test"
```

이 설정이 정의되면 배포 엔진에서 `--without $BUNDLE_WITHOUT`를 사용하여 `bundle install`을 실행합니다.

### <a name="precompile-assets"></a>자산 미리 컴파일

배포 후 단계에서는 기본적으로 자산을 미리 컴파일하지 않습니다. 자산을 미리 컴파일하도록 설정하려면 `ASSETS_PRECOMPILE` [앱 설정](configure-common.md#configure-app-settings)을 `true`로 설정합니다. 그러면 배포 후 단계의 끝에서 `bundle exec rake --trace assets:precompile` 명령이 실행됩니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASSETS_PRECOMPILE=true
```

자세한 내용은 [정적 자산 제공](#serve-static-assets)을 참조하세요.

## <a name="customize-start-up"></a>시작 사용자 지정

기본적으로 Ruby 컨테이너는 다음 순서로 Rails 서버를 시작합니다(자세한 내용은 [시작 스크립트](https://github.com/Azure-App-Service/ruby/blob/master/2.3.8/startup.sh) 참조).

1. 아직 없는 경우 [secret_key_base](https://edgeguides.rubyonrails.org/security.html#environmental-security) 값을 생성합니다. 이 값은 앱을 프로덕션 모드에서 실행하는 데 필요합니다.
1. `RAILS_ENV` 환경 변수를 `production`으로 설정합니다.
1. 이전에 실행된 Rails 서버에서 남긴 *tmp/pids* 디렉터리에서 *.pid* 파일을 삭제합니다.
1. 모든 종속성이 설치되어 있는지 확인합니다. 그렇지 않은 경우 로컬 *vendor/cache* 디렉터리에서 gem을 설치합니다.
1. `rails server -e $RAILS_ENV`을 실행합니다.

시작 프로세스는 다음과 같은 방법으로 사용자 지정할 수 있습니다.

- [정적 자산 제공](#serve-static-assets)
- [비 프로덕션 모드에서 실행](#run-in-non-production-mode)
- [수동으로 secret_key_base 설정](#set-secret_key_base-manually)

### <a name="serve-static-assets"></a>정적 자산 제공

Ruby 컨테이너의 Rails 서버는 기본적으로 프로덕션 모드에서 실행되며, [자산이 미리 컴파일되어 웹 서버에서 제공된다고 가정합니다](https://guides.rubyonrails.org/asset_pipeline.html#in-production). Rails 서버에서 정적 자산을 제공하려면 다음 두 가지 작업을 수행해야 합니다.

- **자산 미리 컴파일** - [정적 자산을 로컬로 미리 컴파일](https://guides.rubyonrails.org/asset_pipeline.html#local-precompilation)하고 수동으로 배포합니다. 또는 배포 엔진이 대신 처리하도록 합니다([자산 미리 컴파일](#precompile-assets) 참조).
- **정적 파일 제공 사용** - Ruby 컨테이너에서 정적 자산을 제공하려면 [`RAILS_SERVE_STATIC_FILES` 앱 설정](configure-common.md#configure-app-settings)을 `true`로 설정합니다. 예를 들면 다음과 같습니다.

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_SERVE_STATIC_FILES=true
    ```

### <a name="run-in-non-production-mode"></a>비 프로덕션 모드에서 실행

Rails 서버는 기본적으로 프로덕션 모드에서 실행됩니다. 예를 들어 개발 모드에서 실행하려면 `RAILS_ENV` [앱 설정](configure-common.md#configure-app-settings)을 `development`로 설정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings RAILS_ENV="development"
```

그러나 이 설정만으로 인해 Rails 서버가 개발 모드로 시작되어 localhost 요청만 허용되고 컨테이너 외부에서는 액세스할 수 없습니다. 원격 클라이언트 요청을 허용하려면 `APP_COMMAND_LINE` [앱 설정](configure-common.md#configure-app-settings)을 `rails server -b 0.0.0.0`으로 설정합니다. 이 앱 설정을 사용하면 Ruby 컨테이너에서 사용자 지정 명령을 실행할 수 있습니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings APP_COMMAND_LINE="rails server -b 0.0.0.0"
```

### <a name="set-secret_key_base-manually"></a><a name="set-secret_key_base-manually"></a> 수동으로 secret_key_base 설정

App Service에서 해당 값을 생성하는 대신 사용자 고유의 `secret_key_base` 값을 사용하려면 `SECRET_KEY_BASE` [앱 설정](configure-common.md#configure-app-settings)을 원하는 값으로 설정합니다. 예를 들면 다음과 같습니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings SECRET_KEY_BASE="<key-base-value>"
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: PostgreSQL을 사용하는 Rails 앱](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](faq-app-service-linux.md)
