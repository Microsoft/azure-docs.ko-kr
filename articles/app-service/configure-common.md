---
title: 포털에서 앱 구성
description: Azure Portal에서 App Service 앱에 대 한 일반 설정을 구성 하는 방법을 알아봅니다. 앱 설정, 연결 문자열, 플랫폼, 언어 스택, 컨테이너 등
keywords: azure app service, 웹 앱, 앱 설정, 환경 변수
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 4990862bacbf2afe2d4b5f39c2b9d31a7c48b78e
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962896"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Azure Portal에서 App Service 앱 구성

이 항목에서는 [Azure Portal]를 사용 하 여 웹 앱, 모바일 백 엔드 또는 API 앱에 대 한 일반 설정을 구성 하는 방법에 대해 설명 합니다.

## <a name="configure-app-settings"></a>앱 설정 구성

App Service에서 앱 설정은 응용 프로그램 코드에 환경 변수로 전달 되는 변수입니다. Linux 앱 및 사용자 지정 컨테이너의 경우 플래그를 사용 하 여 컨테이너에 앱 설정을 전달 `--env` 하 고 컨테이너에서 환경 변수를 설정 App Service 합니다.

[Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다. 

![App Services 검색](./media/configure-common/search-for-app-services.png)

앱의 왼쪽 메뉴에서 **구성**  >  **응용 프로그램 설정**을 선택 합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET Core 개발자를 위해 App Service에서 앱 설정을 설정 하는 것은Web.config또는appsettings.js에서 설정 하는 것과 `<appSettings>` 같지만 App Service의 값이Web.config또는 *Web.config* *appsettings.js*의 값을 재정의 합니다. *Web.config* *appsettings.json* *Web.config* 또는 *appsettings.js*에서 개발 설정 (예: 로컬 MySQL 암호)을 유지할 수 있지만 프로덕션 비밀 (예: Azure MySQL 데이터베이스 암호)은 App Service에서 안전 합니다. 로컬에서 디버그할 때 동일한 코드가 개발 설정을 사용 하 고 Azure에 배포 될 때 프로덕션 암호를 사용 합니다.

마찬가지로 다른 언어 스택은 런타임에 환경 변수로 앱 설정을 가져옵니다. 언어 스택 관련 단계는 다음을 참조 하세요.

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](configure-custom-container.md#configure-environment-variables)

앱 설정은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> [Key Vault 참조](app-service-key-vault-references.md)를 사용 하 여 [Key Vault](../key-vault/index.yml) 에서 앱 설정을 확인할 수도 있습니다.

### <a name="show-hidden-values"></a>숨겨진 값 표시

기본적으로 앱 설정의 값은 보안을 위해 포털에서 숨겨집니다. 앱 설정의 숨겨진 값을 보려면 해당 설정의 **값** 필드를 클릭 합니다. 모든 앱 설정의 값을 보려면 **값 표시** 단추를 클릭 합니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 앱 설정을 추가 하려면 **새 응용 프로그램 설정**을 클릭 합니다. 대화 상자에서 [설정을 현재 슬롯에](deploy-staging-slots.md#which-settings-are-swapped)고정 시킬 수 있습니다.

설정을 편집 하려면 오른쪽에 있는 **편집** 단추를 클릭 합니다.

완료 되 면 **업데이트**를 클릭 합니다. **구성** 페이지에서 다시 **저장** 을 클릭 해야 합니다.

> [!NOTE]
> 기본 Linux 컨테이너 또는 사용자 지정 Linux 컨테이너에서와 같은 앱 설정 이름의 중첩 된 모든 JSON 키 구조는 `ApplicationInsights:InstrumentationKey` `ApplicationInsights__InstrumentationKey` 키 이름에 대 한 App Service로 구성 해야 합니다. 즉, 모든을 `:` `__` (이중 밑줄)로 바꾸어야 합니다.
>

### <a name="edit-in-bulk"></a>대량 편집

대량으로 앱 설정을 추가 하거나 편집 하려면 **고급 편집** 단추를 클릭 합니다. 완료 되 면 **업데이트**를 클릭 합니다. **구성** 페이지에서 다시 **저장** 을 클릭 해야 합니다.

앱 설정의 JSON 형식은 다음과 같습니다.

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

## <a name="configure-connection-strings"></a>연결 문자열 구성

[Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성**  >  **응용 프로그램 설정**을 선택 합니다.

![애플리케이션 설정](./media/configure-common/open-ui.png)

ASP.NET 및 ASP.NET Core 개발자를 위해 App Service에서 연결 문자열을 설정 하는 것은Web.config에서에서 설정 하는 것과 `<connectionStrings>` 같지만 App Service에서 설정한 값은 *Web.config*의 연결 문자열을 재정의 합니다. * * App Service에서 안전 하 게 *Web.config* 및 프로덕션 비밀 (예: SQL Database 자격 증명)의 개발 설정 (예: 데이터베이스 파일)을 유지할 수 있습니다. 로컬에서 디버그할 때 동일한 코드가 개발 설정을 사용 하 고 Azure에 배포 될 때 프로덕션 암호를 사용 합니다.

다른 언어 스택에서는 값에 액세스 하기 위해 연결 문자열에 변수 키에 대 한 특수 형식이 필요 하기 때문에 [앱 설정을](#configure-app-settings) 대신 사용 하는 것이 좋습니다. 단, 응용 프로그램에서 연결 문자열을 구성 하는 경우 특정 Azure 데이터베이스 형식이 앱과 함께 백업 됩니다. 자세한 내용은 백업 되는 [항목](manage-backup.md#what-gets-backed-up)을 참조 하세요. 이 자동화 된 백업이 필요 하지 않은 경우 앱 설정을 사용 합니다.

런타임에 연결 문자열은 환경 변수로 사용할 수 있으며, 다음 연결 형식이 접두사로 사용 됩니다.

* SQLServer `SQLCONNSTR_`  
* MySQL: `MYSQLCONNSTR_` 
* SQLAzure `SQLAZURECONNSTR_` 
* 사용자 지정: `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

예를 들어 *connectionstring1* 라는 MySql 연결 문자열을 환경 변수로 액세스할 수 있습니다 `MYSQLCONNSTR_connectionString1` . 언어 스택 관련 단계는 다음을 참조 하세요.

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.JS](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [사용자 지정 컨테이너](configure-custom-container.md#configure-environment-variables)

연결 문자열은 저장될 때 항상 암호화됩니다(미사용 암호화).

> [!NOTE]
> [Key Vault 참조](app-service-key-vault-references.md)를 사용 하 여 [Key Vault](../key-vault/index.yml) 에서 연결 문자열을 확인할 수도 있습니다.

### <a name="show-hidden-values"></a>숨겨진 값 표시

기본적으로 연결 문자열의 값은 보안을 위해 포털에서 숨겨집니다. 연결 문자열의 숨겨진 값을 보려면 해당 문자열의 **값** 필드를 클릭 하면 됩니다. 모든 연결 문자열의 값을 보려면 **값 표시** 단추를 클릭 합니다.

### <a name="add-or-edit"></a>추가 또는 편집

새 연결 문자열을 추가 하려면 **새 연결 문자열**을 클릭 합니다. 대화 상자에서 [현재 슬롯에 연결 문자열을 적용할](deploy-staging-slots.md#which-settings-are-swapped)수 있습니다.

설정을 편집 하려면 오른쪽에 있는 **편집** 단추를 클릭 합니다.

완료 되 면 **업데이트**를 클릭 합니다. **구성** 페이지에서 다시 **저장** 을 클릭 해야 합니다.

### <a name="edit-in-bulk"></a>대량 편집

연결 문자열을 대량으로 추가 하거나 편집 하려면 **고급 편집** 단추를 클릭 합니다. 완료 되 면 **업데이트**를 클릭 합니다. **구성** 페이지에서 다시 **저장** 을 클릭 해야 합니다.

연결 문자열의 JSON 형식은 다음과 같습니다.

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>일반 설정 구성

[Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성**  >  **일반 설정**을 선택 합니다.

![일반 설정](./media/configure-common/open-general.png)

여기에서 앱에 대 한 몇 가지 일반 설정을 구성할 수 있습니다. 일부 설정의 경우 [더 높은 가격 책정 계층으로 확장](manage-scale-up.md)해야 합니다.

- **스택 설정**: 언어 및 SDK 버전을 포함 하 여 앱을 실행 하는 소프트웨어 스택입니다. Linux 앱 및 사용자 지정 컨테이너 앱의 경우 선택적 시작 명령 또는 파일을 설정할 수도 있습니다.
- **플랫폼 설정**: 다음을 포함 하 여 호스팅 플랫폼에 대 한 설정을 구성할 수 있습니다.
    - **비트: 32**비트 또는 64 비트.
    - **WebSocket 프로토콜**: 예를 들어 [ASP.NET SignalR] 또는 [socket.io](https://socket.io/)에 대 한입니다.
    - **Always On**: 트래픽이 없는 경우에도 앱을 로드 된 상태로 유지 합니다. CRON 식을 사용 하 여 트리거되는 연속 WebJobs 또는 WebJobs에 필요 합니다.
      > [!NOTE]
      > Always On 기능을 사용 하 여 프런트 엔드 부하 분산 장치는 응용 프로그램 루트에 요청을 보냅니다. App Service의이 응용 프로그램 끝점을 구성할 수 없습니다.
    - **관리 되는 파이프라인 버전**: IIS [파이프라인 모드]입니다. 이전 버전의 IIS가 필요한 레거시 앱이 있는 경우 **클래식** 으로 설정 합니다.
    - **HTTP 버전**: [HTTPS/2](https://wikipedia.org/wiki/HTTP/2) 프로토콜에 대 한 지원을 사용 하도록 설정 하려면 **2.0** 로 설정 합니다.
    > [!NOTE]
    > 대부분의 최신 브라우저는 TLS를 통해서만 HTTP/2 프로토콜을 지원하는 반면에 암호화되지 않은 트래픽은 HTTP/1.1을 계속 사용합니다. 클라이언트 브라우저가 HTTP/2를 사용 하 여 앱에 연결 하도록 하려면 사용자 지정 DNS 이름을 보호 합니다. 자세한 내용은 [Azure App Service에서 TLS/SSL 바인딩을 사용 하 여 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조 하세요.
    - **ARR 선호도**: 다중 인스턴스 배포에서 클라이언트가 세션 수명 동안 동일한 인스턴스로 라우팅되도록 합니다. 상태 비저장 응용 프로그램의 경우이 옵션을 **Off** 로 설정할 수 있습니다.
- **디버깅**: [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)또는 [Node.js](configure-language-nodejs.md#debug-remotely) 앱에 대해 원격 디버깅을 사용 하도록 설정 합니다. 이 옵션은 48 시간 후에 자동으로 꺼집니다.
- **들어오는 클라이언트 인증서**: [상호 인증](app-service-web-configure-tls-mutual-auth.md)에 클라이언트 인증서가 필요 합니다.

## <a name="configure-default-documents"></a>기본 문서 구성

이 설정은 Windows 앱에만 해당 됩니다.

[Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성**  >  **기본 문서**를 선택 합니다.

![기본 문서](./media/configure-common/open-documents.png)

기본 문서는 웹 사이트의 루트 URL에 표시 되는 웹 페이지입니다. 목록에서 첫 번째로 일치되는 파일이 사용됩니다. 새 기본 문서를 추가 하려면 **새 문서**를 클릭 합니다. **저장**을 클릭 하는 것을 잊지 마세요.

앱에서 정적 콘텐츠를 제공 하는 대신 URL을 기반으로 경로를 사용 하는 모듈을 사용 하는 경우 기본 문서가 필요 하지 않습니다.

## <a name="configure-path-mappings"></a>경로 매핑 구성

[Azure Portal]에서 **App Services**를 검색하여 선택한 다음, 앱을 선택합니다. 앱의 왼쪽 메뉴에서 **구성**  >  **경로 매핑**을 선택 합니다.

![경로 매핑](./media/configure-common/open-path.png)

**경로 매핑** 페이지에는 OS 유형에 따라 다양 한 항목이 표시 됩니다.

### <a name="windows-apps-uncontainerized"></a>Windows 앱 (uncontainerized)

Windows 앱의 경우 IIS 처리기 매핑과 가상 응용 프로그램 및 디렉터리를 사용자 지정할 수 있습니다.

처리기 매핑을 사용 하면 특정 파일 확장명에 대 한 요청을 처리 하는 사용자 지정 스크립트 프로세서를 추가할 수 있습니다. 사용자 지정 처리기를 추가 하려면 **새 처리기**를 클릭 합니다. 다음과 같이 처리기를 구성 합니다.

- **확장**. Php 또는 *handler*와 같이 처리 하려는 파일 확장명 * \* 입니다* .
- **스크립트 프로세서**. 스크립트 프로세서의 절대 경로입니다. 파일 확장명과 일치 하는 파일에 대 한 요청은 스크립트 프로세서에 의해 처리 됩니다. 경로 `D:\home\site\wwwroot` 를 사용하여 앱의 루트 디렉터리를 참조합니다.
- **인수**. 스크립트 프로세서에 대 한 선택적인 명령줄 인수입니다.

각 앱에는에 매핑되는 기본 루트 경로 ()가 있습니다 `/` `D:\home\site\wwwroot` . 여기서 코드는 기본적으로 배포 됩니다. 앱 루트가 다른 폴더에 있거나 리포지토리에 둘 이상의 응용 프로그램이 있는 경우 여기에서 가상 응용 프로그램 및 디렉터리를 편집 하거나 추가할 수 있습니다. **새 가상 응용 프로그램 또는 디렉터리**를 클릭 합니다.

가상 응용 프로그램 및 디렉터리를 구성 하려면 각 가상 디렉터리와 웹 사이트 루트 ()에 상대적인 해당 실제 경로를 지정 `D:\home` 합니다. 경우에 따라 **애플리케이션** 확인란을 선택하여 가상 디렉터리를 애플리케이션으로 표시할 수 있습니다.

### <a name="containerized-apps"></a>컨테이너 화 된 앱

[컨테이너 화 된 앱에 대 한 사용자 지정 저장소를 추가할](configure-connect-to-azure-storage.md)수 있습니다. 컨테이너 화 된 apps에는 모든 Linux 앱 및 App Service에서 실행 되는 Windows 및 Linux 사용자 지정 컨테이너도 포함 됩니다. **새로 만들기 Azure Storage** 클릭 하 여 다음과 같이 사용자 지정 저장소를 탑재 하 고 구성 합니다.

- **이름**: 표시 이름입니다.
- **구성 옵션**: **Basic** 또는 **Advanced**
- **Storage 계정**: 컨테이너를 포함 하는 저장소 계정입니다.
- **저장소 유형**: **Azure blob** 또는 **Azure Files**.
  > [!NOTE]
  > Windows 컨테이너 앱은 Azure Files만 지원 합니다.
- **저장소 컨테이너**: 기본 구성의 경우 원하는 컨테이너를 선택 합니다.
- **공유 이름**: 고급 구성의 경우 파일 공유 이름입니다.
- **액세스 키**: 고급 구성의 경우 액세스 키입니다.
- **탑재 경로**: 사용자 지정 저장소를 탑재할 컨테이너의 절대 경로입니다.

자세한 내용은 [App Service의 컨테이너에서 네트워크 공유로 Azure Storage 액세스](configure-connect-to-azure-storage.md)를 참조 하세요.

## <a name="configure-language-stack-settings"></a>언어 스택 설정 구성

Linux 앱의 경우 다음을 참조 하세요.

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.JS](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>사용자 지정 컨테이너 구성

[Azure App Service에 대 한 사용자 지정 Linux 컨테이너 구성을](configure-custom-container.md) 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure App Service에서 사용자 지정 도메인 이름 구성]
- [Azure App Service에서 스테이징 환경 설정]
- [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
- [진단 로그 활성화](troubleshoot-diagnostic-logs.md)
- [Azure App Service에서 앱 크기 조정]
- [Azure App Service의 기본 사항 모니터링]
- [Xdt를 사용 하 여 applicationHost.config 설정 변경](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Azure App Service에서 사용자 지정 도메인 이름 구성]: ./app-service-web-tutorial-custom-domain.md
[Azure App Service에서 스테이징 환경 설정]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: https://go.microsoft.com/fwLink/?LinkID=279906
[Azure App Service의 기본 사항 모니터링]: ./web-sites-monitor.md
[파이프라인 모드]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Azure App Service에서 앱 크기 조정]: ./manage-scale-up.md