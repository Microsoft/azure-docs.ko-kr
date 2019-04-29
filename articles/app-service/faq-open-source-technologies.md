---
title: 오픈 소스 기술 FAQ - Azure App Service | Microsoft Docs
description: Azure App Service Web Apps 기능의 오픈 소스 기술에 대한 질문과 대답을 확인합니다.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 7831e5e989835b2c9432dbd61a242584a7b6244d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270213"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Azure Web Apps에 대한 오픈 소스 기술 FAQ

이 문서에는 [Azure App Service의 Web Apps 기능](https://azure.microsoft.com/services/app-service/web/) 관련 오픈 소스 기술 문제에 대한 FAQ(질문과 대답)가 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>PHP 문제를 해결하기 위해 PHP 로깅을 켜려면 어떻게 하나요?

PHP 로깅을 켜려면:

1. [Kudu 웹 사이트](https://*yourwebsitename*.scm.azurewebsites.net)에 로그인합니다.
2. 맨 위 메뉴에서 **디버그 콘솔** > **CMD**를 선택합니다.
3. **Site** 폴더를 선택합니다.
4. **wwwroot** 폴더를 선택합니다.
5. **+** 아이콘을 선택하고 나서 **새 파일**을 선택합니다.
6. 파일 이름을 **.user.ini**로 설정합니다.
7. **.user.ini** 옆에 있는 연필 아이콘을 선택합니다.
8. 파일에 다음 코드를 추가합니다. `log_errors=on`
9. **저장**을 선택합니다.
10. **wp-config.php** 옆에 있는 연필 아이콘을 선택합니다.
11. 텍스트를 다음 코드로 변경합니다.
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Azure Portal의 웹앱 메뉴에서 웹앱을 다시 시작합니다.

자세한 내용은 [Enable WordPress error logs](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)(WordPress 오류 로그 사용)를 참조하세요.

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>App Service에 호스트된 앱에서 Python 애플리케이션 오류를 기록하려면 어떻게 하나요?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>App Service에 호스트된 Node.js 애플리케이션의 버전을 변경하려면 어떻게 하나요?

Node.js 애플리케이션의 버전을 변경하려면 다음 옵션 중 하나를 사용합니다.

* Azure Portal에서 **앱 설정**을 사용합니다.
  1. Azure Portal에서 웹앱으로 이동합니다.
  2. **설정** 블레이드에서 **애플리케이션 설정**을 선택합니다.
  3. **앱 설정**에서 WEBSITE_NODE_DEFAULT_VERSION을 키로 포함하고 원하는 Node.js 버전을 값으로 포함할 수 있습니다.
  4. [Kudu 콘솔](https://*yourwebsitename*.scm.azurewebsites.net)로 이동합니다.
  5. Node.js 버전을 확인하려면 다음 명령을 입력합니다.  
     ```
     node -v
     ```
* iisnode.yml 파일을 수정합니다. iisnode.yml 파일에서 Node.js 버전을 변경하면 iisnode에 사용되는 런타임 환경만 설정됩니다. Kudu cmd 등에서는 Azure Portal의 **앱 설정**에서 설정된 Node.js 버전을 사용합니다.

  iisnode.yml을 수동으로 설정하려면 앱 루트 폴더에 iisnode.yml 파일을 만듭니다. 파일에 다음 줄을 포함합니다.
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* 원본 제어 배포 중에 package.json을 사용하여 iisnode.yml 파일을 설정합니다.
  Azure 원본 제어 배포 프로세스에는 다음 단계가 포함됩니다.
  1. 콘텐츠를 Azure 웹앱으로 이동합니다.
  2. 웹앱 루트 폴더에 deploy.cmd, .deployment 파일이 없는 경우 기본 배포 스크립트를 만듭니다.
  3. package.json 파일 > 엔진에 Node.js 버전을 언급한 경우 iisnode.yml 파일을 만들 배포 스크립트를 실행합니다. `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. iisnode.yml 파일에는 다음 코드 줄이 있습니다.
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>App Service에서 호스트된 내 WordPress 앱에 “데이터베이스 연결 설정 오류” 메시지가 표시됩니다. 이 문제를 어떻게 해결해야 하나요?

Azure WordPress 앱에서 이 오류가 표시되는 경우 php_errors.log 및 debug.log를 사용하도록 설정하려면 [Enable WordPress error logs](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/)(WordPress 오류 로그 사용)에 자세히 설명된 단계를 완료합니다.

로그가 사용하도록 설정되면 오류를 재현한 다음, 로그에서 연결이 부족한지 확인합니다.
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

debug.log 또는 php_errors.log 파일에 이 오류가 표시되면 앱이 연결 수를 초과하고 있는 것입니다. ClearDB에서 호스트 중인 경우 [서비스 계획](https://www.cleardb.com/pricing.view)에서 사용 가능한 연결 수를 확인합니다.

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>App Service에 호스트된 Node.js 앱을 디버그하려면 어떻게 하나요?

1.  [Kudu 콘솔](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole)로 이동합니다.
2.  애플리케이션 로그 폴더(D:\home\LogFiles\Application)로 이동합니다.
3.  logging_errors.txt 파일에서 콘텐츠를 확인합니다.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>App Service 웹앱 또는 API 앱에서 기본 Python 모듈을 설치하려면 어떻게 하나요?

일부 패키지는 Azure에서 pip를 사용하여 설치할 수 없습니다. Python Package Index에서 패키지를 사용할 수 없거나 컴파일러가 필요할 수 있습니다(App Service의 웹앱을 실행 중인 컴퓨터에서는 컴파일러를 사용할 수 없음). App Service Web Apps 및 API Apps에서 기본 모듈을 설치하는 방법에 대한 자세한 내용은 [Install Python modules in App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/)(App Service에서 Python 모듈 설치)를 참조하세요.

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Python의 새 버전 및 Git을 사용하여 Django 앱을 App Service에 배포하려면 어떻게 하나요?

Django 설치에 대한 자세한 내용은 [Deploying a Django app to App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/)(App Service에 Django 앱 배포)를 참조하세요.

## <a name="where-are-the-tomcat-log-files-located"></a>Tomcat 로그 파일은 어디에 있나요?

Azure Marketplace 및 사용자 지정 배포의 경우:

* 폴더 위치: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* 관심 파일:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


포털 **앱 설정** 배포의 경우:

* 폴더 위치: D:\home\LogFiles
* 관심 파일:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>JDBC 드라이버 연결 오류를 해결하려면 어떻게 하나요?

Tomcat 로그에서 다음 메시지를 확인할 수 있습니다.

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

오류를 해결하려면:

1. app/lib 폴더에서 sqljdbc*.jar 파일을 제거합니다.
2. 사용자 지정 Tomcat 또는 Azure Marketplace Tomcat 웹 서버를 사용하는 경우 이 .jar 파일을 Tomcat lib 폴더로 복사합니다.
3. Azure Portal에서 Java를 사용하도록 설정한 경우(**Java 1.8** > **Tomcat 서버** 선택) sqljdbc.* jar 파일을 앱에 해당하는 폴더에 복사합니다. 그다음에 다음 클래스 경로 설정을 web.config 파일에 추가합니다.

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>실시간 로그 파일을 복사할 때 오류가 표시되는 이유는 무엇인가요?

Java 앱(예: Tomcat)에 대한 실시간 로그 파일을 복사하려는 경우 다음 FTP 오류가 표시될 수 있습니다.

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

오류 메시지는 FTP 클라이언트에 따라 달라질 수 있습니다.

모든 Java 앱에는 이 잠금 오류가 있습니다. 앱이 실행되는 동안 이 파일을 다운로드하도록 지원하는 것은 Kudu뿐니다.

앱을 중지하면 FTP가 이러한 파일에 액세스할 수 있습니다.

또 다른 해결 방법은 일정에 따라 실행되고 이러한 파일을 다른 디렉터리로 복사하는 WebJob을 작성하는 것입니다. 샘플 프로젝트에 대해서는 [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) 프로젝트를 참조하세요.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Jetty에 대한 로그 파일은 어디서 찾을 수 있나요?

Marketplace 및 사용자 지정 배포의 경우 로그 파일은 D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs 폴더에 있습니다. 폴더 위치는 사용 중인 Jetty 버전에 따라 달라집니다. 예를 들어 여기에 제공된 경로는 Jetty 9.1.2에 해당합니다. jetty_*YYYY_MM_DD*.stderrout.log를 찾습니다.

포털 앱 설정 배포의 경우 로그 파일은 D:\home\LogFiles에 있습니다. jetty_*YYYY_MM_DD*.stderrout.log 찾기

## <a name="can-i-send-email-from-my-azure-web-app"></a>내 Azure 웹앱에서 메일을 보낼 수 있나요?

App Service에는 기본 제공 메일 기능이 없습니다. 앱에서 메일을 보낼 수 있는 다른 좋은 방법은 이 [Stack Overflow discussion](https://stackoverflow.com/questions/17666161/sending-email-from-azure)(스택 오버플로 토론)을 참조하세요.

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>내 WordPress 사이트가 다른 URL로 리디렉션되는 이유는 무엇인가요?

최근에 Azure로 마이그레이션한 경우 WordPress가 이전 도메인 URL로 리디렉션될 수 있습니다. 이 문제의 원인은 MySQL 데이터베이스의 설정 때문입니다.

WordPress Buddy+는 데이터베이스에서 직접 리디렉션 URL을 업데이트하는 데 사용할 수 있는 Azure 사이트 확장입니다. WordPress Buddy+ 사용에 대한 자세한 내용은 [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)(WordPress Buddy+를 사용한 WordPress 도구 및 MySQL 마이그레이션)를 참조하세요.

또는 SQL 쿼리 또는 PHPMyAdmin을 사용하여 리디렉션 URL을 수동으로 업데이트하려면 [WordPress: Redirecting to wrong URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/)(WordPress: 잘못된 URL로 리디렉션)을 참조하세요.

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>내 WordPress 로그인 암호를 어떻게 변경할 수 있나요?

WordPress 로그인 암호를 기억하지 못하는 경우 WordPress Buddy+를 사용하여 암호를 업데이트할 수 있습니다. 암호를 다시 설정하려면 WordPress Buddy+ Azure 사이트 확장을 설치하고 [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)(WordPress Buddy+를 사용한 WordPress 도구 및 MySQL 마이그레이션)에 설명된 단계를 완료합니다.

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>WordPress에 로그인할 수 없습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

최근에 플러그 인을 설치한 후 WordPress에서 잠긴 것을 알았다면 잘못된 플러그 인이 있을 수 있습니다. WordPress Buddy+는 WordPress에서 플러그 인을 사용하지 않도록 설정할 수 있는 Azure 사이트 확장입니다. 자세한 내용은 [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)(WordPress Buddy+를 사용한 WordPress 도구 및 MySQL 마이그레이션)를 참조하세요.

## <a name="how-do-i-migrate-my-wordpress-database"></a>내 WordPress 데이터베이스를 마이그레이션하려면 어떻게 하나요?

WordPress 웹 사이트에 연결된 MySQL 데이터베이스를 마이그레이션할 수 있는 다양한 옵션이 있습니다.

* 개발자: [명령 프롬프트 또는 PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/) 사용
* 비 개발자: [WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) 사용

## <a name="how-do-i-help-make-wordpress-more-secure"></a>WordPress의 보안을 강화하려면 어떻게 해야 하나요?

WordPress에 대한 보안 모범 사례를 알아보려면 [Best practices for WordPress security in Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/)(Azure의 WordPress 보안 모범 사례)를 참조하세요.

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>PHPMyAdmin을 사용하려고 하는데 “액세스 거부됨” 메시지가 표시됩니다. 이 문제를 해결하려면 어떻게 해야 하나요?

MySQL 앱 내 기능이 이 App Service 인스턴스에서 실행되고 있지 않으면 이 문제가 발생할 수 있습니다. 문제를 해결하려면 웹 사이트에 액세스해 보세요. 그러면 MySQL 앱 내 프로세스를 포함하여 필요한 프로세스가 시작됩니다. MySQL 앱 내 기능이 실행 중인지 확인하려면 프로세스 탐색기에서 mysqld.exe가 프로세스에 나열되는지 확인합니다.

MySQL 앱 내 기능이 실행 중인지 확인한 후 PHPMyAdmin을 사용해 보세요.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>PHPMyadmin을 사용하여 내 MySQL 앱 내 데이터베이스를 가져오거나 내보낼 때 HTTP 403 오류가 발생합니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이전 버전의 Chrome을 사용하고 있으면 알려진 버그가 발생할 수 있습니다. 문제를 해결하려면 최신 버전의 Chrome으로 업그레이드합니다. 또한 Internet Explorer 또는 Microsoft Edge와 같이 문제가 발생하지 않는 다른 브라우저를 사용해 보세요.
