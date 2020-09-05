---
title: PHP 앱 구성
description: 네이티브 Windows 인스턴스나 미리 작성 된 PHP 컨테이너 (Azure App Service)에서 PHP 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c510d6f1cc2aa4a7e71f64e0c296e14a9896614e
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717985"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Azure App Service에 대 한 PHP 앱 구성

이 가이드에서는 Azure App Service에서 PHP 웹 앱, 모바일 백 엔드 및 API 앱을 구성 하는 방법을 보여 줍니다.

이 가이드에서는 App Service에 앱을 배포 하는 PHP 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service를 처음 사용하는 경우 먼저 [PHP 빠른 시작](quickstart-php.md) 및 [MySQL을 사용하는 PHP 자습서](tutorial-php-mysql-app.md)를 따라하세요.

## <a name="show-php-version"></a>PHP 버전 표시

::: zone pivot="platform-windows"  

현재 PHP 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

지원되는 PHP 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

현재 PHP 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 PHP 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>PHP 버전 설정

::: zone pivot="platform-windows"  

[Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 PHP 버전을 7.4로 설정 합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

PHP 버전을 7.2로 설정하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>작성기 실행

배포 시 [작성기](https://getcomposer.org/) 를 실행 하 App Service 하려면 가장 쉬운 방법은 작성기를 리포지토리에 포함 하는 것입니다.

로컬 터미널 창에서 디렉터리를 리포지토리 루트로 변경 하 고, [작성기 다운로드](https://getcomposer.org/download/) 의 지침에 따라 작성기를 다운로드 *합니다. phar* 를 디렉터리 루트에 다운로드 합니다.

다음 명령을 실행 합니다 ( [npm](https://www.npmjs.com/get-npm) 가 설치 되어 있어야 합니다).

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

이제 리포지토리 루트에 *배포* 및 *deploy.sh*의 두 가지 추가 파일이 있습니다.

*Deploy.sh* 을 열고 `Deployment` 다음과 같은 섹션을 찾습니다.

```bash
##################################################################################################################################
# Deployment
# ----------
```

섹션의 *끝에서* 필요한 도구를 실행 하는 데 필요한 코드 섹션을 추가 합니다 `Deployment` .

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

모든 변경 내용을 커밋하고 Git 또는 빌드 자동화를 사용 하는 Zip 배포를 사용 하 여 코드를 배포 합니다. 이제 작성기는 배포 자동화의 일부로 실행 됩니다.

## <a name="run-gruntbowergulp"></a>Grunt/Bower/Gulp 실행

Grunt, Bower 또는 Gulp와 같이 배포 시 인기 있는 자동화 도구를 실행 하려면 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 제공 해야 합니다. App Service App Service는 Git를 사용 하 여 배포 하거나 빌드 자동화를 사용 하는 [Zip 배포](deploy-zip.md) 를 사용 하는 경우이 스크립트를 실행 합니다. 

이러한 도구를 실행 하기 위해 리포지토리를 사용 하도록 설정 하려면package.js의 종속성에 해당 도구를 추가 해야 * 합니다.* 예를 들면 다음과 같습니다.

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

로컬 터미널 창에서 디렉터리를 리포지토리 루트로 변경 하 고 다음 명령을 실행 합니다 ( [npm](https://www.npmjs.com/get-npm) 가 설치 되어 있어야 합니다).

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

이제 리포지토리 루트에 *배포* 및 *deploy.sh*의 두 가지 추가 파일이 있습니다.

*Deploy.sh* 을 열고 `Deployment` 다음과 같은 섹션을 찾습니다.

```bash
##################################################################################################################################
# Deployment
# ----------
```

이 섹션은를 실행 하는 것으로 끝납니다 `npm install --production` . 섹션의 *끝에서* 필요한 도구를 실행 하는 데 필요한 코드 섹션을 추가 합니다 `Deployment` .

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

배포 스크립트가 사용자 지정 명령을 실행 하는 [MEAN.js 샘플의 예제](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135)를 참조 하세요 `npm install` .

### <a name="bower"></a>Bower

이 코드 조각은 `bower install` 를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

이 코드 조각은 `gulp imagemin` 를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

이 코드 조각은 `grunt` 를 실행 합니다.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 설정된 상태에서 Git 또는 zip 패키지를 사용하여 앱을 배포하는 경우 App Service는 다음 시퀀스를 통해 자동화 단계를 빌드합니다.

1. `PRE_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.
1. `php composer.phar install`을 실행합니다.
1. `POST_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.

`PRE_BUILD_COMMAND` 및 `POST_BUILD_COMMAND`는 기본적으로 비어 있는 환경 변수입니다. 빌드 전 명령을 실행하려면 `PRE_BUILD_COMMAND`를 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`를 정의합니다.

다음 예제에서는 일련의 명령에 대한 두 변수를 쉼표로 구분하여 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조하세요.

App Service를 실행하고 Linux에서 PHP 앱을 빌드하는 방법에 대한 자세한 내용은 [Oryx 설명서: PHP 앱을 인식하고 구축하는 방법](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)을 참조하세요.

## <a name="customize-start-up"></a>시작 사용자 지정

기본적으로 내장된 PHP 컨테이너는 Apache 서버를 실행합니다. 먼저 `apache2ctl -D FOREGROUND"`이 실행됩니다. 원하는 경우 [Cloud Shell](https://shell.azure.com)에서 다음의 명령을 실행하여 시작 시 다른 명령을 실행할 수 있습니다.

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](configure-common.md#configure-app-settings)을 지정할 수 있습니다. 그런 다음, 표준적인 [getenv()](https://secure.php.net/manual/function.getenv.php) 패턴으로 액세스합니다. 예를 들어 앱 설정 `DB_HOST`에 액세스하려면 다음 코드를 사용합니다.

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>사이트 루트 변경

::: zone pivot="platform-windows"  

원하는 웹 프레임워크에서 하위 디렉터리를 사이트 루트로 사용할 수 있습니다. 예를 들어 [Laravel](https://laravel.com/)는 사이트 루트로 *public/* 하위 디렉터리를 사용 합니다.

사이트 루트를 사용자 지정 하려면 명령을 사용 하 여 앱에 대 한 가상 응용 프로그램 경로를 설정 합니다 [`az resource update`](/cli/azure/resource#az-resource-update) . 다음 예에서는 사이트 루트를 리포지토리의 *공용/* 하위 디렉터리로 설정 합니다. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

기본적으로 Azure App Service에서는 루트 가상 애플리케이션 경로( _/_ )가 배포된 애플리케이션 파일의 루트 디렉터리(_sites\wwwroot_)를 가리킵니다.

::: zone-end

::: zone pivot="platform-linux"

원하는 웹 프레임워크에서 하위 디렉터리를 사이트 루트로 사용할 수 있습니다. 예를 들어 [Laravel](https://laravel.com/)은 `public/` 하위 디렉터리를 사이트 루트로 사용합니다.

App Service용 기본 PHP 이미지는 Apache를 사용하며 앱에 맞게 사이트 루트를 사용자 지정할 수 없습니다. 이 제한을 해결하려면 다음 내용을 포함하여 *.htaccess* 파일을 리포지토리 루트에 추가하세요.

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

*.htaccess* 다시 쓰기를 사용하지 않으려면 [사용자 지정 Docker 이미지](quickstart-custom-container.md)를 대신 사용하여 Laravel 애플리케이션을 배포할 수 있습니다.

::: zone-end

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [CodeIgniter](https://codeigniter.com/)에서, [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365)는 기본적으로 `X_FORWARDED_PROTO` 값을 확인합니다.

## <a name="customize-phpini-settings"></a>php.ini 설정 사용자 지정

PHP 설치를 변경해야 할 경우 다음 단계에 따라 모든 [php.ini 지시문](https://www.php.net/manual/ini.list.php)을 변경할 수 있습니다.

> [!NOTE]
> PHP 버전과 현재의 *php.ini* 구성을 확인하는 가장 좋은 방법은 앱에서 [phpinfo()](https://php.net/manual/function.phpinfo.php)를 호출하는 것입니다.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>PHP_INI_SYSTEM 외의 지시문 사용자 지정

::: zone pivot="platform-windows"  

PHP_INI_USER, PHP_INI_PERDIR 및 PHP_INI_ALL 지시문 ( [php.ini 지시문](https://www.php.net/manual/ini.list.php)참조)을 사용자 지정 하려면 `.user.ini` 응용 프로그램의 루트 디렉터리에 파일을 추가 합니다.

`php.ini` 파일에 사용한 것과 동일한 구문을 사용하여 구성 설정을 `.user.ini` 파일에 추가합니다. 예를 들어, `display_errors` 설정을 켜고 `upload_max_filesize` 설정을 10M로 설정하려면 `.user.ini` 파일에 다음 텍스트를 포함합니다.

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

변경 내용을 적용해 앱을 다시 배포하고 재시작합니다.

파일을 사용 하는 대신 `.user.ini` 앱에서 [ini_set ()](https://www.php.net/manual/function.ini-set.php) 를 사용 하 여 이러한 비 PHP_INI_SYSTEM 지시문을 사용자 지정할 수 있습니다.

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_USER, PHP_INI_PERDIR, PHP_INI_ALL 지시문([php.ini 지시문](https://www.php.net/manual/ini.list.php) 참조)을 사용자 지정하려면 *.htaccess* 파일을 앱의 루트 디렉터리에 추가합니다.

*.htaccess* 파일에서 `php_value <directive-name> <value>` 구문을 사용하여 지시문을 추가합니다. 예를 들면 다음과 같습니다.

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

변경 내용을 적용해 앱을 다시 배포하고 재시작합니다. Kudu로 배포하는 경우(예: [Git](deploy-local-git.md) 사용) 배포 후에 자동으로 다시 시작됩니다.

*.htaccess*를 사용하는 대신 앱에서 [ini_set()](https://www.php.net/manual/function.ini-set.php)를 사용하여 이런 PHP_INI_SYSTEM 외의 지시문을 사용자 지정할 수 있습니다.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>PHP_INI_SYSTEM 지시문 사용자 지정

::: zone pivot="platform-windows"  

PHP_INI_SYSTEM 지시문([php.ini 지시문](https://www.php.net/manual/ini.list.php) 참조)을 사용자 지정할 때는 *.htaccess* 방법을 사용할 수 없습니다. App Service가 `PHP_INI_SCAN_DIR` 앱 설정을 사용하는 별도의 메커니즘을 제공합니다.

먼저 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 `PHP_INI_SCAN_DIR`이라는 앱 설정을 추가합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Kudu console ()로 이동 하 여 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 로 이동 `d:\home\site` 합니다.

`d:\home\site`에서 `ini`라는 디렉터리를 만든 다음, `d:\home\site\ini` 디렉터리에서 사용자 지정할 지시문을 포함하여 *.ini* 파일(예: *settings.ini)* 을 생성합니다. *php.ini* 파일에서와 동일한 구문을 사용합니다. 

예를 들어 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php)의 값을 변경하려면 다음의 명령을 실행하세요.

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

변경 사항을 적용하려면 앱을 다시 시작해야 합니다.

::: zone-end

::: zone pivot="platform-linux"

PHP_INI_SYSTEM 지시문([php.ini 지시문](https://www.php.net/manual/ini.list.php) 참조)을 사용자 지정할 때는 *.htaccess* 방법을 사용할 수 없습니다. App Service가 `PHP_INI_SCAN_DIR` 앱 설정을 사용하는 별도의 메커니즘을 제공합니다.

먼저 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하여 `PHP_INI_SCAN_DIR`이라는 앱 설정을 추가합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`은 *php.ini*가 있는 기본 디렉터리입니다. `/home/site/ini`은 사용자 지정 *.ini* 파일을 추가할 사용자 지정 디렉터리입니다. 값은 `:`로 구분합니다.

Linux 컨테이너(`https://<app-name>.scm.azurewebsites.net/webssh/host`)를 사용하여 웹 SSH 세션으로 이동합니다.

`/home/site`에서 `ini`라는 디렉터리를 만든 다음, `/home/site/ini` 디렉터리에서 사용자 지정할 지시문을 포함하여 *.ini* 파일(예: *settings.ini)* 을 생성합니다. *php.ini* 파일에서와 동일한 구문을 사용합니다. 

> [!TIP]
> App Service에 내장된 Linux 컨테이너에서 */home*은 영구 공유 스토리지로 사용됩니다. 
>

예를 들어 [expose_php](https://php.net/manual/ini.core.php#ini.expose-php)의 값을 변경하려면 다음의 명령을 실행하세요.

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

변경 사항을 적용하려면 앱을 다시 시작해야 합니다.

::: zone-end

## <a name="enable-php-extensions"></a>PHP 확장 활성화

::: zone pivot="platform-windows"  

내장된 PHP 설치에는 가장 일반적으로 사용하는 확장이 포함됩니다. [php.ini 지시문을 사용자 지정](#customize-php_ini_system-directives)하는 것과 동일한 방식으로 추가 확장을 활성화할 수 있습니다.

> [!NOTE]
> PHP 버전과 현재의 *php.ini* 구성을 확인하는 가장 좋은 방법은 앱에서 [phpinfo()](https://php.net/manual/function.phpinfo.php)를 호출하는 것입니다.
>

추가 확장을 사용하도록 설정하려면 다음 단계를 따릅니다.

응용 프로그램 `bin` 의 루트 디렉터리에 디렉터리를 추가 하 고 `.dll` 여기에 확장 파일을 저장 합니다 (예: *mongodb.dll*). 확장이 Azure의 PHP 버전과 호환되고 VC9 및 nts(non-thread-safe)와 호환되는지 확인하세요.

변경 내용을 배포합니다.

[PHP_INI_SYSTEM 지시문 사용자 지정](#customize-php_ini_system-directives)의 단계에 따라 [extension](https://www.php.net/manual/ini.core.php#ini.extension) 또는 [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 지시문으로 사용자 지정 *.ini* 파일에 확장을 추가합니다.

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

변경 사항을 적용하려면 앱을 다시 시작해야 합니다.

::: zone-end

::: zone pivot="platform-linux"

내장된 PHP 설치에는 가장 일반적으로 사용하는 확장이 포함됩니다. [php.ini 지시문을 사용자 지정](#customize-php_ini_system-directives)하는 것과 동일한 방식으로 추가 확장을 활성화할 수 있습니다.

> [!NOTE]
> PHP 버전과 현재의 *php.ini* 구성을 확인하는 가장 좋은 방법은 앱에서 [phpinfo()](https://php.net/manual/function.phpinfo.php)를 호출하는 것입니다.
>

추가 확장을 사용하도록 설정하려면 다음 단계를 따릅니다.

`bin` 디렉터리를 앱의 루트 디렉터리에 추가하고 `.so` 확장 파일(예: *mongodb.so*)을 넣습니다. 확장이 Azure의 PHP 버전과 호환되고 VC9 및 nts(non-thread-safe)와 호환되는지 확인하세요.

변경 내용을 배포합니다.

[PHP_INI_SYSTEM 지시문 사용자 지정](#customize-php_ini_system-directives)의 단계에 따라 [extension](https://www.php.net/manual/ini.core.php#ini.extension) 또는 [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) 지시문으로 사용자 지정 *.ini* 파일에 확장을 추가합니다.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

변경 사항을 적용하려면 앱을 다시 시작해야 합니다.

::: zone-end

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

::: zone pivot="platform-windows"  

표준 [error_log ()](https://php.net/manual/function.error-log.php) 유틸리티를 사용 하 여 진단 로그를 Azure App Service에 표시 합니다.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>문제 해결

작동하는 PHP 앱이 App Servce에서 다르게 동작하거나 오류가 발생할 경우 다음의 방법을 시도해보세요.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 로컬 상태로 앱을 테스트합니다. App Service는 프로덕션 모드에서 응용 프로그램을 실행 하므로 프로젝트가 프로덕션 모드에서 로컬에서 예상 대로 작동 하는지 확인 해야 합니다. 예를 들면 다음과 같습니다.
    - *composer.json*에 따라, 프로덕션 모드에서 다른 패키지가 설치될 수 있습니다(`require` vs. `require-dev`).
    - 특정 웹 프레임워크는 프로덕션 모드에서 다른 고정 파일을 배포할 수 있습니다.
    - 특정 웹 프레임워크는 프로덕션 모드에서 실행될 때 사용자 지정 시작 스크립트를 사용합니다.
- 디버그 모드에서 App Service의 앱을 실행합니다. 예를 들어 [Laravel](https://meanjs.org/)에서는 [앱 설정`APP_DEBUG`을 `true`](configure-common.md#configure-app-settings)으로 설정하여 프로덕션 모드에서 디버그 메시지를 출력하도록 앱을 구성할 수 있습니다.

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MySQL을 사용하는 PHP 앱](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux FAQ](faq-app-service-linux.md)

::: zone-end

