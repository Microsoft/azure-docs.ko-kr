---
title: PHP 앱-Azure App Service 구성 | Microsoft Docs
description: Azure App Service에서 작동 하도록 PHP 앱을 구성 하는 방법 알아보기
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
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853306"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux PHP 앱 구성

이 가이드에서는 Azure App Service에서 웹 앱, 모바일 백 엔드 및 API 앱에 대 한 기본 제공 PHP 런타임을 구성 하는 방법을 보여 줍니다.

이 가이드는 주요 개념 및 기본 제공 Linux 컨테이너를 사용 하 여 App Service에서 PHP 개발자를 위한 지침을 제공 합니다. Azure App Service를 사용한 경험이 없는 경우에 따라 합니다 [PHP 빠른 시작](quickstart-php.md) 하 고 [MySQL 자습서를 사용 하 여 PHP](tutorial-php-mysql-app.md) 첫 번째입니다.

## <a name="show-php-version"></a>PHP 버전 표시

최신 PHP 버전을 표시 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

모든 지원 되는 PHP 버전을 표시 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>PHP 버전 설정

다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com) PHP 버전 7.2을로 설정 합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>작성기를 실행 합니다.

기본적으로 Kudu가 실행 되지 않습니다 [작성기](https://getcomposer.org/)합니다. Kudu 배포 하는 동안 작성기 자동화를 사용 하도록 설정 하려면 제공 된 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)합니다.

로컬 터미널 창에서 리포지토리 루트에 디렉터리를 변경 합니다. 수행 합니다 [명령줄 설치 단계](https://getcomposer.org/download/) 다운로드 하려면 *composer.phar*합니다.

다음 명령을 실행합니다.

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

리포지토리 루트 이제 두 개의 새 파일인 외에 *composer.phar*: *.deployment* 하 고 *deploy.sh*합니다. 이러한 파일 모두 App Service의 Windows 및 Linux 버전에 사용 합니다.

오픈 *deploy.sh* 찾고는 `Deployment` 섹션입니다. 전체 섹션을 다음 코드로 바꿉니다.

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

모든 변경 내용을 커밋하고 코드를 다시 배포 합니다. 이제 배포 자동화의 일환으로 작성기를 실행 합니다.

## <a name="customize-start-up"></a>시작 사용자 지정

기본적으로 기본 제공 PHP 컨테이너 Apache server를 실행 합니다. 실행 시작 시 `apache2ctl -D FOREGROUND"`합니다. 다음 명령을 실행 하 여 시작 시 다른 명령을 실행할 수는 원하는 경우는 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)을 지정할 수 있습니다. 표준을 사용 하 여 액세스할 수 있습니다 [getenv ()](https://secure.php.net/manual/function.getenv.php) 패턴입니다. 예를 들어 앱 설정 `DB_HOST`에 액세스하려면 다음 코드를 사용합니다.

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>사이트 루트 변경

사용자가 선택한 웹 프레임 워크 사이트 루트로 하위 디렉터리를 사용할 수 있습니다. 예를 들어 [Laravel](https://laravel.com/)를 사용 하는 `public/` 사이트 루트 하위 디렉터리입니다.

App Service에 대 한 기본 PHP 이미지는 Apache를 사용 하 고 앱에 대 한 사이트 루트를 사용자 지정할 수 없습니다. 이 제한을 해결 하려면 추가 된 *.htaccess* 다음과 같은 콘텐츠로 리포지토리 루트에 파일:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

*.htaccess* 다시 쓰기를 사용하지 않으려면 [사용자 지정 Docker 이미지](quickstart-docker-go.md)를 대신 사용하여 Laravel 애플리케이션을 배포할 수 있습니다.

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청의 암호화 여부를 확인해야 하는 경우 `X-Forwarded-Proto` 헤더를 검사합니다.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

인기 있는 웹 프레임워크를 사용하여 표준 앱 패턴의 `X-Forwarded-*` 정보에 액세스할 수 있습니다. [CodeIgniter](https://codeigniter.com/)에서, [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365)는 기본적으로 `X_FORWARDED_PROTO` 값을 확인합니다.

## <a name="customize-phpini-settings"></a>Php.ini 설정을 사용자 지정

PHP 설치를 변경 하는 경우 중 하나를 변경할 수 있습니다 합니다 [php.ini 지시문](http://www.php.net/manual/ini.list.php) 에서 다음 단계를 수행 합니다.

> [!NOTE]
> PHP 버전 및 현재 참조 하는 가장 좋은 방법은 *php.ini* 구성을 호출 하는 것 [phpinfo ()](https://php.net/manual/function.phpinfo.php) 앱에서.
>

### <a name="customize-non-phpinisystem-directives"></a>비 PHP_INI_SYSTEM 지시문을 사용자 지정

PHP_INI_USER, PHP_INI_PERDIR, 및 PHP_INI_ALL 지시문에 맞게 (참조 [php.ini 지시문](http://www.php.net/manual/ini.list.php)), 추가 *.htaccess* 파일을 앱의 루트 디렉터리입니다.

에 *.htaccess* 파일을 사용 하 여 지시문을 추가 합니다 `php_value <directive-name> <value>` 구문입니다. 예를 들면 다음과 같습니다.

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

변경 내용으로 앱을 다시 배포 하 고 다시 시작 합니다. Kudu를 사용 하 여 배포 하는 경우 (예를 들어,를 사용 하 여 [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json))를 자동으로 배포 후 다시 시작 합니다.

사용 하 여 대 안으로 *.htaccess*를 사용할 수 있습니다 [ini_set ()](http://www.php.net/manual/function.ini-set.php) 이러한 비 PHP_INI_SYSTEM 지시문을 사용자 지정 앱에서.

### <a name="customize-phpinisystem-directives"></a>PHP_INI_SYSTEM 지시문을 사용자 지정

PHP_INI_SYSTEM 지시문에 맞게 (참조 [php.ini 지시문](http://www.php.net/manual/ini.list.php))를 사용할 수 없습니다는 *.htaccess* 접근 방식입니다. App Service를 사용 하 여 별도 메커니즘을 제공 합니다 `PHP_INI_SCAN_DIR` 앱 설정 합니다.

첫째,에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com) 이라는 앱 설정을 추가 하려면 `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` 기본 디렉터리는 여기서 *php.ini* 존재 합니다. `/home/site/ini` 사용자 지정 하는 추가 사용자 지정 디렉터리가 *.ini* 파일입니다. 사용 하 여 값을 구분 하는 `:`합니다.

Linux 컨테이너를 사용 하 여 웹 SSH 세션으로 이동 (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

디렉터리를 만듭니다 `/home/site` 호출 `ini`, 만든 다음는 *.ini* 파일을 `/home/site/ini` 디렉터리 (예를 들어 *settings.ini)* 사용자 지정 하려는 지시문을 사용 하 여 합니다. 사용 하 여 같은 구문을 사용 하는 *php.ini* 파일입니다. 

> [!TIP]
> App Service에서 기본 제공 Linux 컨테이너에서 *home/* 지속형된 공유 저장소로 사용 됩니다. 
>

예를 들어의 값을 변경 하려면 [expose_php](http://php.net/manual/ini.core.php#ini.expose-php) 다음 명령을 실행 합니다.

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

변경 내용이 적용 되려면 앱을 다시 시작 합니다.

## <a name="enable-php-extensions"></a>PHP 확장을 사용 하도록 설정

가장 자주 사용 되는 확장을 포함 하는 기본 제공 PHP 설치 합니다. 동일한 추가 확장을 설정할 수 있습니다. 방식으로 [php.ini 지시문을 사용자 지정](#customize-php_ini_system-directives)합니다.

> [!NOTE]
> PHP 버전 및 현재 참조 하는 가장 좋은 방법은 *php.ini* 구성을 호출 하는 것 [phpinfo ()](https://php.net/manual/function.phpinfo.php) 앱에서.
>

추가 확장을 사용하도록 설정하려면 다음 단계를 따릅니다.

추가 `bin` put 앱의 루트 디렉터리로 디렉터리를 `.so` 확장 파일 (예를 들어 *mongodb.so*). 확장은 Azure 되며 VC9 및 비-스레드로부터 안전한 (nts) 호환 되는 PHP 버전과 호환 되는지 확인 합니다.

변경 내용을 배포 합니다.

단계를 따릅니다 [PHP_INI_SYSTEM 사용자 지정 지시문](#customize-php_ini_system-directives), 사용자 지정에 확장을 추가 *.ini* 파일을 [확장](https://www.php.net/manual/ini.core.php#ini.extension) 또는 [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) 지시문입니다.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

변경 내용이 적용 되려면 앱을 다시 시작 합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>문제 해결

작업 중인 PHP 앱이 App Service에서 다르게 동작 또는 오류가 하는 경우 다음과 같이 하세요.

- [로그 스트림에 액세스](#access-diagnostic-logs)합니다.
- 프로덕션 모드에서 앱을 로컬로 테스트 합니다. App Service는 프로젝트가 프로덕션 모드로 로컬로 예상 대로 작동 하는지 확인 해야 하므로 프로덕션 모드에서 Node.js 앱을 실행 합니다. 예를 들면 다음과 같습니다.
    - 에 따라 프로그램 *composer.json*, 프로덕션 모드에 대 한 서로 다른 패키지를 설치할 수 있습니다 (`require` 비교 `require-dev`).
    - 특정 웹 프레임 워크는 다르게 프로덕션 모드에서에서 정적 파일을 배포할 수 있습니다.
    - 프로덕션 모드에서 실행 하는 경우 특정 웹 프레임 워크에서 사용자 지정 시작 스크립트를 사용할 수 있습니다.
- App Service에서 앱을 디버그 모드에서 실행 합니다. 예를 들어 [Laravel](http://meanjs.org/), 하 여 프로덕션 환경에서 디버그 메시지를 출력 하도록 앱을 구성할 수 있습니다 [설정 합니다 `APP_DEBUG` 앱 설정을 `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)합니다.

### <a name="robots933456"></a>robots933456

컨테이너 로그에 다음 메시지가 표시 될 수 있습니다.

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

이 메시지를 안전 하 게 무시할 수 있습니다. `/robots933456.txt` 컨테이너의 요청 처리의 수를 확인 하려면 App Service를 사용 하는 더미 URL 경로가입니다. 404 응답 단순히 나타내지만 경로가 존재 하지 App Service 컨테이너 정상 상태이 고 요청에 응답할 준비가 임을 알 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: MySQL을 사용하는 PHP 앱](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)