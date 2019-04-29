---
title: 앱 구성 - Azure App Service
description: Azure App Services에서 앱을 구성하는 방법
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: fb8dedac8b795ec127d7b4a14728d73c9397a1dd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128435"
---
# <a name="configure-apps-in-azure-app-service"></a>Azure App Service에서 앱 구성

이 항목에서는 [Azure Portal]을 사용하여 웹앱, 모바일 백 엔드 또는 API 앱을 구성하는 방법을 설명합니다.

## <a name="application-settings"></a>애플리케이션 설정
1. [Azure Portal]에서 앱의 블레이드를 엽니다.
2. **애플리케이션 설정**을 클릭합니다.

![애플리케이션 설정][configure01]

**애플리케이션 설정** 블레이드 설정이 여러 범주 아래에 그룹화됩니다.

### <a name="general-settings"></a>일반 설정
**프레임워크 버전**. 앱에서 다음 프레임워크를 사용하는 경우 이러한 옵션을 설정합니다. 

* **.NET Framework**: .NET Framework 버전을 설정합니다. 
* **PHP**: PHP 버전을 설정하거나 PHP를 사용하지 않으려면 **끄기**를 선택합니다. 
* **Java**: Java 버전을 선택하거나 Java를 사용하지 않으려면 **끄기**를 선택합니다. **웹 컨테이너** 옵션을 사용하여 Tomcat 및 Jetty 버전 사이에서 선택합니다.
* **Python**: Python 버전을 선택하거나, Python을 사용하지 않으려면 **끄기**를 선택합니다.

기술적인 이유로, 앱에 Java를 사용하도록 설정하면 .NET, PHP 및 Python 옵션은 사용하지 않도록 설정됩니다.

<a name="platform"></a>
**플랫폼**. 앱이 32비트 또는 64비트 환경에서 실행되는지 선택합니다. 64비트 환경에는 기본 또는 표준 계층이 필요합니다. 무료 및 공유 계층은 항상 32비트 환경에서 실행됩니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**웹 소켓**. WebSocket 프로토콜을 사용하도록 설정하려면 **켜기**를 설정합니다. 예를 들어 앱이 [ASP.NET SignalR] 또는 [socket.io](https://socket.io/)를 사용하는 경우가 여기에 해당합니다.

<a name="alwayson"></a>
**Always On**. 기본적으로 앱은 일정 기간 동안 유휴 상태인 경우 언로드됩니다. 이를 통해 시스템 리소스가 절약됩니다. 기본 또는 표준 모드에서는 **Always On**을 사용하도록 설정하여 앱을 항상 로드된 상태로 유지할 수 있습니다. 앱에서 연속 WebJobs를 실행하거나 CRON 식을 사용하여 트리거되는 WebJobs를 실행하는 경우 **Always On**을 사용하도록 설정해야 합니다. 그러지 않으면 웹 작업이 안정적으로 실행되지 않을 수 있습니다.

**관리되는 파이프라인 버전**. IIS [파이프라인 모드]를 설정합니다. 이전 버전의 IIS가 필요한 레거시 앱이 없으면 통합(기본값)으로 설정된 상태로 그대로 둡니다.

**HTTP 버전**. [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 프로토콜에 대한 지원을 사용하도록 **2.0**으로 설정합니다. 

> [!NOTE]
> 대부분의 최신 브라우저는 TLS를 통해서만 HTTP/2 프로토콜을 지원하는 반면에 암호화되지 않은 트래픽은 HTTP/1.1을 계속 사용합니다. 클라이언트 브라우저가 HTTP/2를 통해 앱에 연결되도록 하려면 앱의 사용자 지정 도메인에 대해 [App Service Certificate을 구매](web-sites-purchase-ssl-web-site.md)하거나 [타사 인증서를 바인딩](app-service-web-tutorial-custom-ssl.md)합니다.

**ARR 선호도**. 여러 VM 인스턴스로 확장되는 응용 프로그램에서 ARR 선호도 쿠키는 클라이언트가 세션이 끝날 때까지 동일한 인스턴스로 라우팅되도록 보장합니다. 상태 비저장 애플리케이션의 성능을 향상시키려면 이 옵션을 **꺼짐**으로 설정합니다.   

**자동 교체**. 배포 슬롯에 대한 자동 교체를 사용 하는 경우, 업데이트를 해당 슬롯에 푸시하면 App Service는 자동으로 앱을 프로덕션으로 교체합니다. 자세한 내용은 [Azure App Service에서 앱에 대한 스테이징 슬롯에 배포](deploy-staging-slots.md)를 참조하세요.

### <a name="debugging"></a>디버그
**원격 디버깅**. 원격 디버깅을 사용하도록 설정합니다. 사용하도록 설정되면 Visual Studio에서 원격 디버거를 사용하여 앱에 바로 연결할 수 있습니다. 원격 디버깅은 48시간 동안 사용 가능한 상태로 유지됩니다. 

### <a name="app-settings"></a>앱 설정
이 섹션에는 시작 시 앱이 로드하는 이름/값 쌍이 나와 있습니다. 

* .NET 앱의 경우, 이 설정은 런타임 시 .NET 구성 `AppSettings`으로 주입되어 기존 설정을 재정의합니다. 
* Linux 또는 Web App for Containers의 App Service의 경우 `ApplicationInsights:InstrumentationKey`와 같은 이름에 중첩된 json 키 구조가 있는 경우 키 이름으로 `ApplicationInsights__InstrumentationKey`가 있어야 합니다. 따라서 `:`을 `__`(즉, 밑줄 두 개)로 대체해야 합니다.
* PHP, Python, Java 및 Node 애플리케이션에서는 런타임에 환경 변수로 이러한 설정에 액세스할 수 있습니다. 각 앱 설정에 대해 두 개의 환경 변수가 만들어집니다. 하나는 앱 설정 항목에 의해 이름이 지정되고, 다른 하나는 이름에 APPSETTING_ 접두사가 붙습니다. 둘 다 같은 값을 포함합니다.

앱 설정은 저장될 때 항상 암호화됩니다(미사용 암호화).

[Key Vault 참조](app-service-key-vault-references.md)를 사용하여 Key Vault에서 앱 설정을 해결할 수 있습니다.

### <a name="connection-strings"></a>연결 문자열
연결된 리소스의 연결 문자열입니다. 

.NET 앱의 경우, 이 연결 문자열이 런타임 시 .NET 구성 `connectionStrings` 설정에 주입되어 키가 연결된 데이터베이스 이름과 같은 기존 항목을 재정의합니다. 

PHP, Python, Java 및 Node 애플리케이션에서는 런타임에 이러한 설정을 환경 변수로 사용할 수 있으며, 환경 변수 앞에는 연결 형식이 옵니다. 환경 변수 접두사는 다음과 같습니다. 

* SQL Server: `SQLCONNSTR_`
* MySQL: `MYSQLCONNSTR_`
* SQL Database: `SQLAZURECONNSTR_`
* 사용자 지정: `CUSTOMCONNSTR_`

예를 들어 MySql 연결 문자열 이름이 `connectionstring1`로 지정된 경우 환경 변수 `MYSQLCONNSTR_connectionString1`을 통해 액세스될 수 있습니다.

연결 문자열은 저장될 때 항상 암호화됩니다(미사용 암호화).

[Key Vault 참조](app-service-key-vault-references.md)를 사용하여 Key Vault에서 연결 설정을 해결할 수 있습니다.

### <a name="default-documents"></a>기본 문서
기본 문서는 웹 사이트의 루트 URL에 표시되는 웹 페이지입니다.  목록에서 첫 번째로 일치되는 파일이 사용됩니다. 

앱에서는 정적 콘텐츠를 제공하는 대신 URL을 기반으로 라우팅되는 모듈을 사용할 수 있으며, 이 경우에도 기본 문서는 없습니다.    

### <a name="handler-mappings"></a>처리기 매핑
이 영역을 사용하여 사용자 지정 스크립트 프로세서를 추가해 특정 파일 확장명에 대한 요청을 처리할 수 있습니다. 

* **확장명**. 처리할 파일 확장명입니다(예: *.php 또는 handler.fcgi). 
* **스크립트 프로세서 경로**. 스크립트 프로세서의 절대 경로입니다. 파일 확장명과 일치하는 파일에 대한 요청이 스크립트 프로세서에서 처리됩니다. 경로 `D:\home\site\wwwroot` 를 사용하여 앱의 루트 디렉터리를 참조합니다.
* **추가 인수**. 스크립트 프로세서에 대한 선택적 명령줄 인수입니다. 

### <a name="virtual-applications-and-directories"></a>가상 애플리케이션 및 디렉터리
가상 애플리케이션 및 디렉터리를 구성하려면 각 가상 디렉터리 및 웹 사이트 루트에 상대적인 실제 경로를 지정합니다. 경우에 따라 **애플리케이션** 확인란을 선택하여 가상 디렉터리를 애플리케이션으로 표시할 수 있습니다.

## <a name="enabling-diagnostic-logs"></a>진단 로그를 사용하도록 설정
진단 로그를 사용하도록 설정하려면:

1. 앱의 블레이드에서 **모든 설정**을 클릭합니다.
2. **진단 로그**를 클릭합니다. 

로깅을 지원하는 웹 애플리케이션의 진단 정보를 기록하는 옵션입니다. 

* **애플리케이션 로깅**. 파일 시스템에 애플리케이션 로그를 기록합니다. 로깅은 12시간 동안 유지됩니다. 

**수준**. 애플리케이션 로깅을 사용하도록 설정하면 이 옵션은 기록될 정보의 양(오류, 경고, 정보 또는 세부 정보 표시)을 지정합니다.

**웹 서버 로깅**. 로그는 W3C 확장 로그 파일 형식으로 저장됩니다. 

**자세한 오류 메시지**. 자세한 오류 메시지는.htm 파일로 저장됩니다. 이 파일은 /LogFiles/DetailedErrors 아래 저장됩니다. 

**실패한 요청 추적**. 로그는 XML 파일에 대해 요청하지 못했습니다. 파일은 /LogFiles/W3SVC*xxx*(여기서 xxx는 고유 식별자) 아래에 저장됩니다. 이 폴더에는 하나의 XSL 파일 및 하나 이상의 XML 파일이 포함되어 있습니다. XSL 파일은 XML 파일 내용의 서식을 지정하고 필터링하는 기능을 제공하므로 XSL 파일을 다운로드해야 합니다.

로그 파일을 보려면 다음과 같이 FTP 자격 증명을 만들어야 합니다.

1. 앱의 블레이드에서 **모든 설정**을 클릭합니다.
2. **배포 자격 증명**을 클릭합니다.
3. 사용자 이름 및 암호를 입력합니다.
4. **저장**을 클릭합니다.

![배포 자격 증명 설정][configure03]

전체 FTP 사용자 이름은 "app\username"이며, 여기서 *app*은 사용자의 앱 이름입니다. 사용자 이름은 **필수 항목**아래 앱 블레이드에 나열되어 있습니다.

![FTP 배포 자격 증명][configure02]

## <a name="other-configuration-tasks"></a>기타 구성 작업
### <a name="ssl"></a>SSL
기본 또는 표준 모드에서는 사용자 지정 도메인에 대해 SSL 인증서를 업로드할 수 있습니다. 자세한 내용은 [앱에 HTTPS 사용](app-service-web-tutorial-custom-ssl.md)을 참조하세요. 

업로드된 인증서를 보려면 **모든 설정** > **사용자 지정 도메인 및 SSL**을 사용하여 웹앱을 구성하는 방법을 설명합니다.

### <a name="domain-names"></a>도메인 이름
앱의 사용자 지정 도메인 이름을 추가합니다. 자세한 내용은 [Azure App Service에서 앱의 사용자 지정 도메인 이름 구성](app-service-web-tutorial-custom-domain.md)을 참조하세요.

도메인 이름을 보려면 **모든 설정** > **사용자 지정 도메인 및 SSL**을 사용하여 웹앱을 구성하는 방법을 설명합니다.

### <a name="deployments"></a>배포
* 연속 배포를 설정합니다. [Azure App Service에서 앱 배포를 위해 Git 사용](deploy-local-git.md)을 참조하세요.
* 배포 슬롯입니다. [Azure App Service의 스테이징 환경으로 배포]를 참조하세요.

배포 슬롯을 보려면 **모든 설정** > **배포 슬롯**을 사용하여 웹앱을 구성하는 방법을 설명합니다.

### <a name="monitoring"></a>모니터링
기본 또는 표준 모드에서는 지리적으로 분산된 최대 세 곳의 HTTP 또는 HTTPS 엔드포인트에 대한 가용성을 테스트할 수 있습니다. HTTP 응답 코드가 오류(4xx 또는 5xx)이거나 응답에 30초 넘게 걸리는 경우 모니터링 테스트가 실패합니다. 지정된 모든 위치에서 모니터링 테스트가 성공하는 경우 엔드포인트는 사용 가능한 것으로 간주됩니다. 

자세한 내용은 [방법: 웹 엔드포인트 상태 모니터링]을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure App Service에서 사용자 지정 도메인 이름 구성]
* [Azure App Service에서 앱에 대한 HTTPS를 사용하도록 설정]
* [Azure App Service에서 앱 크기 조정]
* [Azure App Service의 기본 사항 모니터링]
* [ApplicationHost.xdt 사용 하 여 applicationHost.config 설정 변경](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Azure App Service에서 사용자 지정 도메인 이름 구성]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service의 스테이징 환경으로 배포]: ./deploy-staging-slots.md
[Azure App Service에서 앱에 대한 HTTPS를 사용하도록 설정]: ./app-service-web-tutorial-custom-ssl.md
[방법: 웹 엔드포인트 상태 모니터링]: https://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service의 기본 사항 모니터링]: ./web-sites-monitor.md
[파이프라인 모드]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service에서 앱 크기 조정]: ./web-sites-scale.md

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
