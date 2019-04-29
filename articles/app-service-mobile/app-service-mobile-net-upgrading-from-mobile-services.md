---
title: Mobile Services에서 Azure App Service로 업그레이드
description: Mobile Services 애플리케이션을 App Service 모바일 앱으로 쉽게 업그레이드하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f5ffc795e6469971d1eaf335d6683f94d05f0807
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122441"
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>기존 .NET Azure 모바일 서비스를 App Service로 업그레이드
App Service 모바일은 Microsoft Azure를 사용하여 모바일 애플리케이션을 빌드하는 새로운 방법입니다. 자세한 내용은 [Mobile Apps 정의]를 참조하세요.

이 항목에서는 기존 .NET 백 엔드 애플리케이션을 Azure Mobile Services에서 새로운 App Service Mobile Apps로 업그레이드하는 방법을 설명합니다. 이 업그레이드를 수행하는 동안 기존 Mobile Services 애플리케이션이 계속 작동할 수 있습니다.   Node.js 백 엔드 애플리케이션을 업그레이드해야 하는 경우 [Node.js Mobile Services 업그레이드](app-service-mobile-node-backend-upgrading-from-mobile-services.md)를 참조하세요.

모바일 백 엔드가 Azure App Service로 업그레이드되면 모든 App Service 기능에 액세스할 수 있고 Mobile Services 가격 책정이 아닌 [App Service 가격 책정]에 따라 요금이 청구됩니다.

## <a name="migrate-vs-upgrade"></a>마이그레이션과 업그레이드 비교
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> 업그레이드를 진행하기 전에 [마이그레이션을 수행](app-service-mobile-migrating-from-mobile-services.md) 하는 것이 좋습니다. 이러한 방식으로 동일한 App Service 계획에 두 버전의 애플리케이션을 모두 추가 비용 없이 둘 수 있습니다.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Mobile Apps .NET 서버 SDK에서 향상된 기능
새 [Mobile Apps SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)의 업그레이드는 다음과 같은 이점을 제공합니다.

* NuGet 종속성에서 유연성이 증가합니다. 호스팅 환경이 고유한 버전의 NuGet 패키지를 더 이상 제공하지 않으므로 호환되는 대체 버전을 사용할 수 있습니다. 그러나 모바일 서버 SDK 또는 종속성에 대한 새 주요 오류수정 또는 보안 업데이트가 있는 경우 서비스를 수동으로 업데이트해야 합니다.
* 모바일 SDK에서 유연성이 증가합니다. 인증, 테이블 API 및 푸시 등록 엔드포인트 등 어떤 기능 및 경로를 설정할 것인지 명시적으로 제어할 수 있습니다. 자세한 내용은 [Azure Mobile Apps용 .NET 서버 SDK를 사용하는 방법](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.
* 다른 ASP.NET 프로젝트 형식 및 경로를 지원합니다. 이제 모바일 백 엔드 프로젝트와 동일한 프로젝트에서 MVC 및 Web API 컨트롤러를 호스팅할 수 있습니다.
* 새로운 App Service 인증 기능을 지원하며 이는 웹 및 모바일 앱에서 일반 인증 구성을 사용할 수 있게 합니다.

## <a name="overview"></a>기본 업그레이드 개요
대부분의 경우 새 Mobile Apps 서버 SDK로 전환한 다음 새 Mobile App 인스턴스에 코드를 다시 게시하기만 하면 간단히 업그레이드할 수 있습니다. 그러나 고급 인증 시나리오 및 예약된 작업 사용과 같이 추가 구성이 필요한 일부 시나리오도 있습니다. 각 시나리오는 이후 섹션에서 설명합니다.

> [!TIP]
> 업그레이드를 시작하기 전에 이 항목의 나머지 부분을 읽고 완전히 이해하는 것이 좋습니다. 아래 설명선에 표시된 사용하는 기능을 모두 기록해 두세요.
>
>

Mobile Services 클라이언트 SDK는 새 Mobile Apps 서버 SDK와 호환할 수 **없습니다** . 앱에 대한 서비스 연속성을 제공하기 위해 현재 게시된 클라이언트를 제공하는 사이트에 변경 내용을 게시하지 않아야 합니다. 대신 중복으로 제공한 새 모바일 앱을 만들어야 합니다. 이 애플리케이션을 동일한 App Service 계획에 두어 추가 비용이 발생하지 않도록 할 수 있습니다.

다음 두 가지 버전의 애플리케이션이 있습니다. 하나는 동일하게 유지되고 야생에서 게시된 앱을 제공하며 다른 하나는 업그레이드하고 새 클라이언트 릴리스를 대상으로 할 수 있습니다. 진도에 맞게 코드를 이동하고 테스트할 수 있지만 수행한 버그 수정이 둘 모두에 적용되도록 해야 합니다. 야생에서 원하는 수의 클라이언트 앱이 최신 버전으로 업데이트되면 원하는 경우 원래 마이그레이션된 앱을 삭제할 수 있습니다.

업그레이드 프로세스에 대한 전체 개요는 다음과 같습니다.

1. 새 모바일 앱 만들기
2. 프로젝트를 업데이트하여 새 서버 SDK 사용
3. 새 버전의 클라이언트 애플리케이션 릴리스
4. (선택 사항) 원래 마이그레이션된 인스턴스 삭제

## <a name="mobile-app-version"></a>두 번째 애플리케이션 인스턴스 만들기
업그레이드의 첫 번째 단계는 새 버전의 애플리케이션을 호스트할 모바일 앱 리소스를 만드는 것입니다. 기존 모바일 서비스를 이미 마이그레이션한 경우 동일한 호스팅 계획에 이 버전을 만들려고 합니다. [Azure Portal]을 열고 마이그레이션된 애플리케이션으로 이동합니다. App Service 계획에서 실행 중인지 확인합니다.

다음으로 [.NET 백 엔드 만들기 지침](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app)을 수행하여 두 번째 애플리케이션 인스턴스를 만듭니다. App Service 계획 또는 "호스팅 계획"을 선택하라는 메시지가 나타나면 마이그레이션된 애플리케이션의 계획을 선택합니다.

Mobile Services와 동일한 데이터베이스 및 알림 허브를 사용하려는 경우가 많습니다. [Azure Portal]을 열고 원래 애플리케이션 탐색하여 이러한 값을 복사한 다음 **설정** > **애플리케이션 설정**을 클릭할 수 있습니다. **연결 문자열**에서 `MS_NotificationHubConnectionString` 및 `MS_TableConnectionString`을 복사합니다. 새 업그레이드 사이트로 이동하고 붙여 넣어 기존 값을 덮어씁니다. 앱에 필요한 다른 애플리케이션 설정에 이 프로세스를 반복합니다.

애플리케이션에 대한 ASP.NET 프로젝트의 복사본을 만들고 새 사이트에 게시합니다. 새 URL를 통해 업데이트된 클라이언트 애플리케이션의 복사본을 사용하여 모든 작업이 예상 대로 작동하는 것을 확인합니다.

## <a name="updating-the-server-project"></a>서버 프로젝트 업데이트
Mobile Apps는 Mobile Services 런타임과 동일한 기능을 대부분 제공하는 새로운 [모바일 앱 서버 SDK]를 제공합니다. 먼저 Mobile Services 패키지에 대한 모든 참조를 제거해야 합니다. NuGet 패키지 관리자에서 `WindowsAzure.MobileServices.Backend`를 검색합니다. 대부분의 앱에서 `WindowsAzure.MobileServices.Backend.Tables` 및 `WindowsAzure.MobileServices.Backend.Entity`를 포함하여 여러 패키지가 나타납니다. 이 경우 `Entity`와 같은 종속성 트리에서 가장 낮은 패키지를 시작하고 제거합니다. 메시지가 표시되면 모든 종속 패키지를 제거하지 마십시오. `WindowsAzure.MobileServices.Backend` 자체를 제거할 때까지 이 프로세스를 반복합니다.

이 시점에서 더 이상 Mobile Services SDK를 참조하지 않는 프로젝트가 있습니다.

다음으로 Mobile Apps SDK 참조를 추가합니다. 이 업그레이드를 위해 대부분의 개발자는 `Microsoft.Azure.Mobile.Server.Quickstart` 패키지를 다운로드하고 설치할 수 있으며, 전체 필수 집합으로 가져오게 됩니다.

SDK 간의 차이로 인해 발생하는 몇 가지 컴파일러 오류가 있지만 쉽게 해결할 수 있으며 이 섹션의 나머지 부분에서 다루게 됩니다.

### <a name="base-configuration"></a>기본 구성
그런 다음 WebApiConfig.cs에서

```csharp
// Use this class to set configuration options for your mobile service
ConfigOptions options = new ConfigOptions();

// Use this class to set WebAPI configuration options
HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));
```

다음으로 바꿀 수 있습니다.

```csharp
HttpConfiguration config = new HttpConfiguration();
new MobileAppConfiguration()
    .UseDefaultConfiguration()
.ApplyTo(config);

```

> [!NOTE]
> 앱에서 기능을 추가/제거하는 방법 및 새 .NET 서버 SDK에 대한 자세한 내용을 보려면, [.NET 서버 SDK를 사용하는 방법] 항목을 참조하세요.
>
>

또한 앱이 인증 기능을 사용하는 경우 OWIN 미들웨어를 등록해야 합니다. 이 경우 새 OWIN 시작 클래스에 위의 구성 코드를 이동해야 합니다.

1. 프로젝트에 아직 포함되지 않은 경우 NuGet 패키지 `Microsoft.Owin.Host.SystemWeb` 를 추가합니다.
2. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** -> **새 항목**을 선택합니다. **웹** -> **일반** -> **OWIN 시작 클래스**를 선택합니다.
3. MobileAppConfiguration에 대한 위의 코드를 `WebApiConfig.Register()`에서 새 시작 클래스의 `Configuration()` 메서드로 이동합니다.

`Configuration()` 메서드가 다음으로 끝나도록 합니다.

```csharp
app.UseWebApi(config)
app.UseAppServiceAuthentication(config);
```

아래 전체 인증 섹션에서 다루는 인증에 관련된 추가 변경 내용이 있습니다.

### <a name="working-with-data"></a>데이터 작업
Mobile Services에서 모바일 앱 이름은 Entity Framework 설치 프로그램의 기본 스키마 이름으로 제공되었습니다.

이전에 참조된 동일한 스키마가 있는지 확인하려면 다음을 사용하여 애플리케이션에 DbContext의 스키마를 설정합니다.

```csharp
string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
```

위의 작업을 수행하는 경우 MS_MobileServiceName을 설정해야 합니다. 또한 애플리케이션이 이전에 사용자 지정한 경우 다른 스키마 이름을 제공할 수 있습니다.

### <a name="system-properties"></a>시스템 속성
#### <a name="naming"></a>이름 지정
Azure Mobile Services 서버 SDK에서 시스템 속성은 항상 속성에 대해 두 개의 밑줄(`__`) 접두사를 포함합니다.

* __createdAt
* __updatedAt
* __deleted
* __version

Mobile Services 클라이언트 SDK는 이 형식에서 시스템 속성을 구문 분석하기 위한 특수한 논리입니다.

Azure Mobile Apps에서 시스템 속성에는 더 이상 특별 한 형식 및 다음과 같은 이름이 없습니다.

* createdAt
* updatedAt
* deleted
* 버전

Mobile Apps 클라이언트 SDK는 새 시스템 속성 이름을 사용하므로 클라이언트 코드에는 변경이 필요하지 않습니다. 그러나 서비스에 직접 REST 호출을 하는 경우 쿼리를 적절하게 변경해야 합니다.

#### <a name="local-store"></a>로컬 저장소
시스템 속성의 이름에 변경 사항이 있으면 Mobile Services에 대한 오프라인 동기화 로컬 데이터베이스가 Mobile Apps와 호환되지 않습니다. 가능한 경우 보류 중인 변경 내용이 서버에 보내진 이후까지 Mobile Services에서 Mobile Apps에 클라이언트 앱 업그레이드하지 않도록 해야 합니다. 그런 다음 업그레이드된 앱은 새 데이터베이스 파일 이름을 사용해야 합니다.

작업 큐에 오프라인 변경 내용을 보류 중인 동안 클라이언트 앱이 Mobile Services에서 Mobile Apps로 업그레이드되면 시스템 데이터베이스는 새 열 이름을 사용하도록 업데이트되어야 합니다. iOS에서 열 이름을 변경하는 간단한 마이그레이션을 사용하여 수행할 수 있습니다. Android 및 .NET 관리된 클라이언트에서 사용자 지정 SQL을 작성하여 데이터 개체 테이블에 대한 열 이름을 바꿔야 합니다.

iOS에서 다음과 일치하도록 데이터 엔터티에 대한 핵심 데이터 스키마를 변경해야 합니다. 속성 `createdAt`, `updatedAt` 및 `version`에는 더 이상 `ms_` 접두사가 없습니다.

| 특성 | Type | 참고 |
| --- | --- | --- |
| id |문자열, 필수로 표시 |원격 저장소의 기본 키 |
| createdAt |Date |(옵션) createdAt 시스템 속성에 매핑됩니다. |
| updatedAt |Date |(옵션) updatedAt 시스템 속성에 매핑됩니다. |
| 버전 |String |(옵션) 충돌을 검색하는 데 사용되며 version에 매핑됩니다. |

#### <a name="querying-system-properties"></a>시스템 속성 쿼리
Azure Mobile Services에서 시스템 속성은 기본적으로 전송되지 않지만 쿼리 문자열 `__systemProperties`을 사용하여 요청된 경우에만 그렇습니다. 반면 Azure Mobile Apps 시스템에서 속성은 서버 SDK 개체 모델의 일부이기 때문에 **항상 선택** 됩니다.

이 변경 사항은 `MappedEntityDomainManager`의 확장과 같은 도메인 관리자의 사용자 지정 구현에 주로 영향을 줍니다. Mobile Services에서 클라이언트가 시스템 속성을 요구하지 않으면 모든 속성이 실제로 매핑되지 않는 `MappedEntityDomainManager`를 사용할 수 있습니다. 그러나 Azure Mobile Apps에서 이러한 매핑되지 않은 속성은 가져오기 쿼리에서 오류를 발생시킵니다.

문제를 해결하는 가장 쉬운 방법은 DTO를 수정하여 `EntityData` 대신 `ITableData`을 상속하는 것입니다. 그런 다음 `[NotMapped]` 특성을 생략해야 하는 필드에 추가합니다.

예를 들어 다음은 시스템 속성이 없는 `TodoItem` 을 정의합니다.

```csharp
using System.ComponentModel.DataAnnotations.Schema;

public class TodoItem : ITableData
{
    public string Text { get; set; }

    public bool Complete { get; set; }

    public string Id { get; set; }

    [NotMapped]
    public DateTimeOffset? CreatedAt { get; set; }

    [NotMapped]
    public DateTimeOffset? UpdatedAt { get; set; }

    [NotMapped]
    public bool Deleted { get; set; }

    [NotMapped]
    public byte[] Version { get; set; }
}
```

참고: `NotMapped`에 오류가 발생하는 경우 어셈블리 `System.ComponentModel.DataAnnotations`에 참조를 추가합니다.

### <a name="cors"></a>CORS
Mobile Services는 ASP.NET CORS 솔루션을 래핑하여 CORS에 대한 지원을 포함합니다. 래핑 계층이 개발자에게 더 많은 제어를 제공하도록 제거되었으므로 직접 [ASP.NET CORS 지원](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api)을 활용할 수 있습니다.

우려되는 부분은 CORS를 사용하는 경우 클라이언트 SDK가 제대로 작동하려면 `eTag` 및 `Location` 헤더를 허용해야 한다는 점입니다.

### <a name="push-notifications"></a>푸시 알림
푸시를 위해 서버 SDK에서 누락된 주요 항목은 PushRegistrationHandler 클래스입니다. Mobile Apps에서는 등록이 약간 다르게 처리되며, 기본적으로 태그 없는 등록이 사용됩니다. 사용자 지정 API를 통해 태그를 관리할 수 있습니다. 자세한 정보는 [태그에 대한 등록](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) 지침을 참조하세요.

### <a name="scheduled-jobs"></a>예약된 작업
예약된 작업은 Mobile Apps에 빌드되지 않으므로 .NET 백 엔드에 있는 기존 작업을 모두 개별적으로 업그레이드해야 합니다. 한 가지 옵션은 모바일 앱 코드 사이트에 예약된 [웹 작업] 을 만드는 것입니다. 작업 코드를 포함하는 컨트롤러를 설정하고 예상 일정에 따라 해당 엔드포인트에 도달하도록 [Azure Scheduler]를 구성할 수도 있습니다.

### <a name="miscellaneous-changes"></a>기타 변경 내용
모바일 클라이언트에서 사용될 수 있는 모든 ApiControllers에는 이제 `[MobileAppController]` 특성이 있어야 합니다. 더 이상 기본적으로 포함되지 않으므로 다른 ApiControllers는 모바일 포맷터에 의해 영향을 받지 않았습니다.

`ApiServices` 개체는 더이상 SDK의 일부가 아닙니다. 모바일 앱 설정에 액세스하려면 다음을 사용할 수 있습니다.

```csharp
MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
```

마찬가지로, 로깅은 이제 표준 ASP.NET 추적 쓰기를 사용하여 수행됩니다.

```csharp
ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
traceWriter.Info("Hello, World");  
```

## <a name="authentication"></a>인증 고려 사항
이제 Mobile Services의 인증 구성 요소는 App Service 인증/권한 부여 기능으로 옮겨졌습니다. [모바일 앱에 인증 추가](app-service-mobile-ios-get-started-users.md) 항목을 읽어서 사이트에 이 옵션을 사용하는 데 대해 알아볼 수 있습니다.

AAD, Facebook, Google 등의 일부 공급자의 경우 복사 애플리케이션에서 기존 등록을 활용할 수 있어야 합니다. 단순히 ID 공급자의 포털로 이동하고 새 리디렉션 URL을 등록에 추가해야 합니다. 그런 다음 클라이언트 ID 및 암호를 통해 App Service 인증/권한 부여를 구성합니다.

### <a name="controller-action-authorization"></a>컨트롤러 작업 권한 부여
`[AuthorizeLevel(AuthorizationLevel.User)]` 특성의 인스턴스는 이제 표준 ASP.NET `[Authorize]` 특성을 사용하도록 변경되어야 합니다. 또한 컨트롤러는 다른 ASP.NET 애플리케이션에서와 같이 이제 기본적으로 익명입니다.
관리자 또는 애플리케이션을 같은 다른 AuthorizeLevel 옵션 중 하나를 사용하는 경우 이러한 내용이 사라지도록 합니다. 대신 공유 암호에 AuthorizationFilters를 설정하거나 AAD 서비스 주체를 구성하여 서비스 간 호출을 안전하게 사용하도록 할 수 있습니다.

### <a name="getting-additional-user-information"></a>추가 사용자 정보 가져오기
`GetAppServiceIdentityAsync()` 메서드를 통해 액세스 토큰을 포함하는 추가 사용자 정보를 가져올 수 있습니다.

```csharp
FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
```

또한 데이터베이스에 사용자 ID를 저장하는 경우와 같이 애플리케이션이 사용자 ID에 종속된 경우 Mobile Services와 App Service Mobile Apps 간에 사용자 ID가 서로 다르다는 것에 유의해야 합니다. 그러나 Mobile Services 사용자 ID도 가져올 수 있습니다. ProviderCredentials 하위 클래스는 모두 UserId 속성을 가집니다. 따라서 앞의 예제부터 계속 실행합니다.

```csharp
string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
```

앱이 사용자 ID에 종속된 경우 가능하면 ID 공급자와 함께 동일한 등록을 활용하는 것이 중요합니다. 일반적으로 사용자 ID의 범위는 사용된 애플리케이션 등록으로 지정되므로 새 등록을 도입하면 사용자를 해당 데이터에 일치시킬 때 문제가 발생할 수 있습니다.

### <a name="custom-authentication"></a>사용자 지정 인증
앱이 사용자 지정 인증 솔루션을 사용하는 경우 업그레이드된 사이트가 시스템에 액세스하도록 하려 합니다. [.NET 서버 SDK 개요] 에서 사용자 지정 인증에 대한 새 지침을 수행하여 솔루션을 통합할 수 있습니다. 사용자 지정 인증 구성 요소가 여전히 미리 보기 상태입니다.

## <a name="updating-clients"></a>클라이언트 업데이트
작동하는 모바일 앱 백 엔드가 있으면 그것을 사용하는 클라이언트 애플리케이션의 새 버전에서 작동할 수 있습니다. 또한 Mobile Apps는 클라이언트 SDK의 새 버전을 포함하고 위의 서버 업그레이드와 유사합니다. 따라서 Mobile Apps 버전을 설치하기 전에 Mobile Services SDK에 대한 모든 참조를 제거해야 합니다.

버전 간의 주요 변경 사항 중 하나는 생성자가 애플리케이션 키를 더 이상 필요로 하지 않는다는 점입니다. 이제 모바일 앱의 URL에 간단히 전달할 수 있습니다. 예를 들어 .NET 클라이언트에서 `MobileServiceClient` 생성자는 다음과 같습니다.

```csharp
public static MobileServiceClient MobileService = new MobileServiceClient(
    "https://contoso.azurewebsites.net", // URL of the Mobile App
);
```

아래 링크를 통해 새 SDK를 설치하고 새 구조를 사용하는 데 대한 내용을 읽을 수 있습니다.

* [iOS 버전 3.0.0 이상](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET(Windows/Xamarin) 버전 2.0.0 이상](app-service-mobile-dotnet-how-to-use-client-library.md)

애플리케이션이 푸시 알림을 사용하면 일부 변경 사항이 있는 경우와 같이 각 플랫폼에 대한 특정 등록 지침을 기록합니다.

새 클라이언트 버전을 준비할 때 업그레이드된 서버 프로젝트에 사용해보세요. 작동하는지 유효성을 검사한 후에 고객에게 애플리케이션의 새 버전을 릴리스할 수 있습니다. 결국 고객이 이러한 업데이트를 받고 나면 Mobile Services 버전의 앱을 삭제할 수 있습니다. 이 시점에서 최신 모바일 앱 서버 SDK를 사용하여 App Service Mobile Apps의 업그레이드를 완전히 완료했습니다.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Mobile Apps 정의]: app-service-mobile-value-prop.md
[모바일 앱 서버 SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /azure/scheduler/
[웹 작업]: https://github.com/Azure/azure-webjobs-sdk/wiki
[.NET 서버 SDK를 사용하는 방법]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[App Service 가격 책정]: https://azure.microsoft.com/pricing/details/app-service/
[.NET 서버 SDK 개요]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
