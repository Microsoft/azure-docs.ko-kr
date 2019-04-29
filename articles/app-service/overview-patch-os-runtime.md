---
title: OS 및 런타임 패치 주기 - Azure App Service | Microsoft Docs
description: Azure App Service가 OS 및 런타임을 업데이트하는 방법과 업데이트 공지를 받을 수 있는 방법을 설명합니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 576627c96b19dd3563ab21a5d478b779e4a3ed64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838997"
---
# <a name="os-and-runtime-patching-in-azure-app-service"></a>Azure App Service의 OS 및 런타임 패치

이 문서에서는 [App Service](overview.md)의 OS 또는 소프트웨어에 대한 특정 버전 정보를 가져오는 방법을 보여 줍니다. 

App Service는 Platform-as-a-Service입니다. 이것은 OS 및 애플리케이션 스택이 Azure에 의해 관리된다는 것을 의미합니다. 따라서 사용자는 애플리케이션과 해당 데이터만 관리하면 됩니다. OS 및 애플리케이션 스택에 대한 추가 제어 기능은 [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/)에서 사용할 수 있습니다. 이 사실에 유의한다고 해도, 다음과 같은 추가 정보를 알면 App Service를 사용할 때 도움이 될 수 있습니다.

-   OS 업데이트 방법 및 시기
-   App Service가 심각한 취약점(예: 제로 데이)에 대해 패치되는 방법
-   앱을 실행하는 운영 체제 및 런타임 버전

보안상의 이유로, 보안 정보의 특정 세부 사항은 게시되지 않습니다. 그러나 이 문서에서는 프로세스를 최대한 투명하게 유지하고, 보안 관련 공지 사항 또는 런타임 업데이트를 최신 상태로 유지하는 방법을 공개하여 문제를 완화시키는 것을 목적으로 합니다.

## <a name="how-and-when-are-os-updates-applied"></a>OS 업데이트 방법 및 시기

Azure는 App Service 리소스를 실행하는 2개의 수준, 즉 물리적 서버와 게스트 VM(가상 컴퓨터)의 OS 패치를 관리합니다. 둘 다 매월 업데이트되며, 월별 [화요일 패치](https://technet.microsoft.com/security/bulletins.aspx) 일정에 맞춰집니다. 이러한 업데이트는 Azure SLA의 고가용성을 보장하는 방식으로 자동으로 적용됩니다. 

업데이트가 적용되는 방식에 대한 자세한 내용은 [Demystifying the magic behind App Service OS updates](https://blogs.msdn.microsoft.com/appserviceteam/2018/01/18/demystifying-the-magic-behind-app-service-os-updates/)(App Service OS 업데이트의 마법 분석)를 참조하세요.

## <a name="how-does-azure-deal-with-significant-vulnerabilities"></a>Azure에서 중요한 취약점을 처리하는 방법

[제로 데이 취약점](https://wikipedia.org/wiki/Zero-day_(computing))과 같은 심각한 취약점 해결을 위해 즉각적인 패치가 필요할 경우 사레별로 우선 순위가 높은 업데이트가 처리됩니다.

[Azure 보안 블로그](https://azure.microsoft.com/blog/topics/security/)를 방문하여 Azure에서 중요한 최신 보안 공지를 확인합니다. 

## <a name="when-are-supported-language-runtimes-updated-added-or-deprecated"></a>지원되는 언어 런타임이 업데이트, 추가 또는 더 이상 지원되지 않는 시기

지원되는 언어 런타임의 새 안정화 버전(주, 부 또는 패치)은 App Service 인스턴스에 주기적으로 추가됩니다. 기존 버전을 덮어쓰는 업데이트도 있지만, 기존 버전과 병렬로 설치되는 업데이트도 있습니다. 덮어쓰기 설치는 앱이 업데이트된 런타임에서 자동으로 실행됨을 의미합니다. 병렬 설치는 새 런타임 버전을 활용하기 위해 앱을 수동으로 마이그레이션해야 함을 의미합니다. 자세한 내용은 다음 하위 섹션 중 하나를 참조하세요.

런타임 업데이트 및 지원 중지는 다음에 공지됩니다.

- https://azure.microsoft.com/updates/?product=app-service 
- https://github.com/Azure/app-service-announcements/issues

> [!NOTE] 
> 여기에 나오는 정보는 App Service 앱에 기본적으로 제공되는 언어 런타임에 적용됩니다. 예를 들어, App Service에 업로드하는 사용자 지정 런타임은 수동으로 업그레이드하지 않으면 변경되지 않은 상태를 유지합니다.
>
>

### <a name="new-patch-updates"></a>새 패치 업데이트

.NET, PHP, Java SDK 또는 Tomcat/Jetty 버전에 대한 패치 업데이트는 기존 설치를 새 버전으로 덮어쓰는 방법으로 자동으로 적용됩니다. Node.js 패치 업데이트는 기존 버전과 병렬로 설치됩니다(다음 섹션의 주 및 부 버전과 유사). 새 Python 패치 버전은 [사이트 확장](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)을 통해 기본 제공된 Python 설치와 함께 설치될 수 있습니다.

### <a name="new-major-and-minor-versions"></a>새 주 버전 및 부 버전

새 주 버전 또는 부 버전이 추가되면 기존 버전과 병렬로 설치됩니다. 앱을 수동으로 새 버전으로 업그레이드할 수 있습니다. 구성 파일(예: `web.config` 및 `package.json`)에서 런타임 버전을 구성한 경우 동일한 방법으로 업그레이드해야 합니다. App Service 설정을 사용하여 런타임 버전을 구성한 경우 [Azure Portal](https://portal.azure.com)에서 변경하거나 다음 예제와 같이 [Cloud Shell](../cloud-shell/overview.md)에서 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 명령을 실행하여 변경할 수 있습니다.

```azurecli-interactive
az webapp config set --net-framework-version v4.7 --resource-group <groupname> --name <appname>
az webapp config set --php-version 7.0 --resource-group <groupname> --name <appname>
az webapp config appsettings set --settings WEBSITE_NODE_DEFAULT_VERSION=8.9.3 --resource-group <groupname> --name <appname>
az webapp config set --python-version 3.4 --resource-group <groupname> --name <appname>
az webapp config set --java-version 1.8 --java-container Tomcat --java-container-version 9.0 --resource-group <groupname> --name <appname>
```

### <a name="deprecated-versions"></a>더 이상 사용되지 않는 버전  

이전 버전이 더 이상 사용되지 않을 경우 그에 따라 런타임 버전 업그레이드를 계획할 수 있도록 제거 날짜가 공지됩니다. 

## <a name="how-can-i-query-os-and-runtime-update-status-on-my-instances"></a>인스턴스 내에서 OS 및 런타임 업데이트 상태를 쿼리하는 방법  

중요한 OS 정보를 액세스하지 못하게 잠그면([Azure App Service의 운영 체제 기능](operating-system-functionality.md)) [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console)을 사용하여 App Service 인스턴스에서 OS 버전 및 런타임 버전을 쿼리할 수 있습니다. 

다음 표에서는 앱을 실행하는 언어 런타임의 Windows 버전을 확인하는 방법을 보여 줍니다.

| 정보 | 찾는 위치 | 
|-|-|
| Windows 버전 | `https://<appname>.scm.azurewebsites.net/Env.cshtml` 참조(시스템 정보 아래에 제공됨) |
| .NET 버전 | `https://<appname>.scm.azurewebsites.net/DebugConsole`의 명령 프롬프트에서 다음 명령을 실행합니다. <br>`powershell -command "gci 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Net Framework Setup\NDP\CDF'"` |
| .NET Core 버전 | `https://<appname>.scm.azurewebsites.net/DebugConsole`의 명령 프롬프트에서 다음 명령을 실행합니다. <br> `dotnet --version` |
| PHP 버전 | `https://<appname>.scm.azurewebsites.net/DebugConsole`의 명령 프롬프트에서 다음 명령을 실행합니다. <br> `php --version` |
| 기본 Node.js 버전 | [Cloud Shell](../cloud-shell/overview.md)에서 다음 명령을 실행합니다. <br> `az webapp config appsettings list --resource-group <groupname> --name <appname> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION']"` |
| Python 버전 | `https://<appname>.scm.azurewebsites.net/DebugConsole`의 명령 프롬프트에서 다음 명령을 실행합니다. <br> `python --version` |  

> [!NOTE]  
> 레지스트리 위치 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Component Based Servicing\Packages`에 액세스합니다. 여기에는 ["KB" 패치](https://docs.microsoft.com/security-updates/SecurityBulletins/securitybulletins)에 대한 정보가 저장되고 잠겨집니다.
>
>

## <a name="more-resources"></a>추가 리소스

[보안 센터: 보안](https://www.microsoft.com/en-us/trustcenter/security)  
[Azure App Service의 64비트 ASP.NET Core](https://gist.github.com/glennc/e705cd85c9680d6a8f1bdb62099c7ac7)
