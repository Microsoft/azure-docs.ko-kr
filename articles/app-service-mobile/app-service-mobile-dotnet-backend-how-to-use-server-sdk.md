---
title: Mobile Apps용 .NET 백 엔드 서버 SDK를 사용하는 방법 | Microsoft Docs
description: Azure App Service Mobile Apps용 .NET 백 엔드 서버 SDK를 사용하는 방법에 대해 알아봅니다.
keywords: 앱 서비스, Azure 앱 서비스, 모바일 앱, 모바일 서비스, 규모, 확장성, 앱 배포, Azure 앱 배포
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0620554f-9590-40a8-9f47-61c48c21076b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 195a2dd88f443120f337ba441358389f0dc290f8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119525"
---
# <a name="work-with-the-net-backend-server-sdk-for-azure-mobile-apps"></a>Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

이 항목은 주요 Azure App Service Mobile Apps 시나리오에서 .NET 백 엔드 서버 SDK를 사용하는 방법을 보여줍니다. Azure Mobile Apps SDK를 사용하면 ASP.NET 애플리케이션에서 모바일 클라이언트를 사용하여 작업할 수 있습니다.

> [!TIP]
> [Azure Mobile Apps용 .NET 서버 SDK][2]는 GitHub에서 오픈 소스입니다. 리포지토리는 전체 서버 SDK 단위 테스트 도구 모음 및 일부 샘플 프로젝트를 포함하는 모든 소스 코드를 포함합니다.
>
>

## <a name="reference-documentation"></a>참조 설명서
서버 SDK에 대한 참조 설명서는 [Azure Mobile Apps .NET 참조][1]에서 찾을 수 있습니다.

## <a name="create-app"></a>방법: .NET 모바일 앱 백 엔드 만들기
새 프로젝트를 시작하는 경우 [Azure Portal] 과 Visual Studio 중 하나를 사용하여 App Service 애플리케이션을 만들 수 있습니다. App Service 애플리케이션을 로컬로 실행하거나 클라우드 기반 앱 서비스 모바일 앱에 프로젝트를 게시할 수 있습니다.

기존 프로젝트에 모바일 기능을 추가하는 경우 [SDK 다운로드 및 초기화](#install-sdk) 섹션을 참조하세요.

### <a name="create-a-net-backend-using-the-azure-portal"></a>Azure 포털을 사용하여 .NET 백 엔드 만들기
App Service 모바일 백 엔드를 만들려면 [빠른 시작 자습서][3]를 따르거나 다음과 같은 단계를 수행합니다.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

*시작* 블레이드로 돌아가서 **테이블 API 만들기** 아래에서 **백 엔드 언어**로 **C#** 을 선택합니다. **다운로드**를 클릭하고 로컬 컴퓨터에 압축된 프로젝트 파일을 풀고 Visual Studio에서 솔루션을 엽니다.

### <a name="create-a-net-backend-using-visual-studio-2017"></a>Visual Studio 2017을 사용하여 .NET 백 엔드 만들기

Visual Studio 설치 관리자를 통해 Azure 워크로드를 설치하여 Visual Studio에서 Azure Mobile Apps 프로젝트에 게시합니다. SDK를 설치한 후 다음 단계를 사용하여 ASP.NET 애플리케이션을 만듭니다.

1. **새 프로젝트** 대화를 엽니다(**파일** > **새로 만들기** > **프로젝트...** 에서).
2. **Visual C#** 를 확장하고 **웹**을 선택합니다.
3. **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택합니다.
4. 프로젝트 이름을 입력합니다. 그런 후 **OK**를 클릭합니다.
5. 템플릿 목록에서 **Azure 모바일 앱**을 선택합니다.
6. **확인**을 클릭하여 솔루션을 만듭니다.
7. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택한 다음, 게시 대상으로 **App Service**를 선택합니다.
8. 프롬프트를 따라 인증하고 새로운 또는 기존 Azure App Service를 선택하여 게시합니다.

### <a name="create-a-net-backend-using-visual-studio-2015"></a>Visual Studio 2015를 사용하여 .NET 백 엔드 만들기

Visual Studio에서 Azure Mobile Apps 프로젝트를 만들려면 [.NET용 Azure SDK][4](버전 2.9.0 이상)을 설치합니다. SDK를 설치한 후 다음 단계를 사용하여 ASP.NET 애플리케이션을 만듭니다.

1. **새 프로젝트** 대화를 엽니다(**파일** > **새로 만들기** > **프로젝트...** 에서).
2. **템플릿** > **Visual C#** 를 확장하고 **웹**을 선택합니다.
3. **ASP.NET 웹 애플리케이션**을 선택합니다.
4. 프로젝트 이름을 입력합니다. 그런 후 **OK**를 클릭합니다.
5. *ASP.NET 4.5.2 템플릿*아래에서 **Azure Mobile App**을 선택합니다. **클라우드에 호스트** 를 선택하여 클라우드에 이 프로젝트를 게시할 수 있는 모바일 백 엔드를 만듭니다.
6. **확인**을 클릭합니다.

## <a name="install-sdk"></a>방법: SDK 다운로드 및 설치
SDK는 [NuGet.org]에서 사용할 수 있습니다. 이 패키지는 SDK를 사용하여 시작하는 데 필요한 기본 기능을 포함합니다. SDK를 초기화하려면 **HttpConfiguration** 개체에서 작업을 수행해야 합니다.

### <a name="install-the-sdk"></a>SDK 설치
SDK를 설치하려면 Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. [Microsoft.Azure.Mobile.Server] 패키지를 검색한 다음 **설치**를 클릭합니다.

### <a name="server-project-setup"></a> 서버 프로젝트 초기화
A .NET 백 엔드 서버 프로젝트는 OWIN 시작 클래스를 포함하여 다른 ASP.NET 프로젝트와 유사하게 초기화됩니다. NuGet 패키지 `Microsoft.Owin.Host.SystemWeb`을 참조하도록 확인합니다. Visual Studio에서 이 클래스를 추가하려면 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** >
**새 항목**을 선택하고 **웹** > **일반** > **OWIN 시작 클래스**를 차례로 선택합니다.  다음과 같은 특성의 클래스가 생성됩니다.

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

OWIN 시작 클래스의 `Configuration()` 메서드에서 **HttpConfiguration** 개체를 사용하여 Azure Mobile Apps 환경을 구성합니다.
다음 예제는 추가된 기능이 없는 서버 프로젝트를 초기화합니다.

    // in OWIN startup class
    public void Configuration(IAppBuilder app)
    {
        HttpConfiguration config = new HttpConfiguration();

        new MobileAppConfiguration()
            // no added features
            .ApplyTo(config);

        app.UseWebApi(config);
    }

개별 기능을 사용하려면 **ApplyTo**를 호출하기 전에 **MobileAppConfiguration** 개체에서 확장 메서드를 호출해야 합니다. 예를 들어 다음 코드는 초기화하는 동안 `[MobileAppController]` 특성을 포함하는 모든 API 컨트롤러에 기본 경로를 추가합니다.

    new MobileAppConfiguration()
        .MapApiControllers()
        .ApplyTo(config);

Azure 포털의 빠른 시작 서버에서 **UseDefaultConfiguration()** 을 호출합니다. 이것은 다음 설정과 같습니다.

        new MobileAppConfiguration()
            .AddMobileAppHomeController()             // from the Home package
            .MapApiControllers()
            .AddTables(                               // from the Tables package
                new MobileAppTableConfiguration()
                    .MapTableControllers()
                    .AddEntityFramework()             // from the Entity package
                )
            .AddPushNotifications()                   // from the Notifications package
            .MapLegacyCrossDomainController()         // from the CrossDomain package
            .ApplyTo(config);

사용되는 확장 방법은 다음과 같습니다.

* `AddMobileAppHomeController()`은(는) 기본 Azure Mobile Apps 홈 페이지를 제공합니다.
* `MapApiControllers()`은(는) `[MobileAppController]` 특성으로 데코레이팅된 WebAPI 컨트롤러에 대한 사용자 지정 API 기능을 제공합니다.
* `AddTables()`은(는) 테이블 컨트롤러에 대한 `/tables` 엔드포인트의 매핑을 제공합니다.
* `AddTablesWithEntityFramework()`은(는) Entity Framework 기반 컨트롤러를 사용하는 `/tables` 엔드포인트 매핑에 대한 약칭입니다.
* `AddPushNotifications()`은(는) Notification Hubs에 대한 디바이스를 등록하는 간단한 방법을 제공합니다.
* `MapLegacyCrossDomainController()` 은(는) 로컬 개발을 위한 표준 CORS 헤더를 제공합니다.

### <a name="sdk-extensions"></a>SDK 확장
다음 NuGet 기반 확장 패키지는 애플리케이션에서 사용할 수 있는 다양한 모바일 기능을 제공합니다. **MobileAppConfiguration** 개체를 사용하여 초기화하는 동안 확장을 사용하도록 설정합니다.

* [Microsoft.Azure.Mobile.Server.Quickstart] 기본 Mobile Apps 설정을 지원합니다. 초기화하는 동안 **UseDefaultConfiguration** 확장 메서드를 호출하여 구성에 추가했습니다. 이 확장은 다음과 같은 확장을 포함합니다. 알림, 인증, 엔터티, 테이블, Cross-domain 및 홈 패키지 이 패키지는 Azure 포털에서 사용할 수 있는 Mobile Apps 빠른 시작에서 사용됩니다.
* [Microsoft.Azure.Mobile.Server.Home](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 웹 사이트 루트에 대해 기본 *이 모바일 앱이 실행 중인 페이지* 를 구현합니다. **AddMobileAppHomeController** 확장 메서드를 호출하여 구성에 추가합니다.
* [Microsoft.Azure.Mobile.Server.Tables](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 데이터로 작업하기 위한 클래스를 포함하고 데이터 파이프라인을 설정합니다. **AddTables** 확장 메서드를 호출하여 구성에 추가합니다.
* [Microsoft.Azure.Mobile.Server.Entity](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) SQL Database에서 데이터를 액세스하는 Entity Framework를 사용할 수 있도록 합니다. **AddTablesWithEntityFramework** 확장 메서드를 호출하여 구성에 추가합니다.
* [Microsoft.Azure.Mobile.Server.Authentication] 인증을 사용할 수 있도록 하고 토큰의 유효성을 검사하는 데 사용되는 OWIN 미들웨어를 설정합니다. **AddAppServiceAuthentication** 및 **IAppBuilder**.**UseAppServiceAuthentication** 확장 메서드를 호출하여 구성에 추가합니다.
* [Microsoft.Azure.Mobile.Server.Notifications] 푸시 알림을 사용할 수 있도록 하며 푸시 등록 엔드포인트를 정의합니다. **AddPushNotifications** 확장 메서드를 호출하여 구성에 추가합니다.
* [Microsoft.Azure.Mobile.Server.CrossDomain](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 모바일 앱에서 레거시 웹 브라우저에 데이터를 제공하는 컨트롤러를 만듭니다. **MapLegacyCrossDomainController** 확장 메서드를 호출하여 구성에 추가합니다.
* [Microsoft.Azure.Mobile.Server.Login]은 사용자 지정 인증 시나리오 동안 사용되는 정적 메서드인 AppServiceLoginHandler.CreateToken() 메서드를 제공합니다.

## <a name="publish-server-project"></a>방법: 서버 프로젝트 게시
이 섹션에서는 Visual Studio에서 .NET 백 엔드 프로젝트를 게시하는 방법을 보여줍니다. [Git](../app-service/deploy-local-git.md) 또는 사용 가능한 다른 메서드를 사용하여 백 엔드 프로젝트를 배포할 수도 있습니다.

1. Visual Studio에서 NuGet 패키지를 복원하려면 프로젝트를 빌드하십시오.
2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 처음 게시할 때는 게시 프로필을 정의해야 합니다. 이미 정의된 프로필이 있을 때 프로필을 선택하고 **게시**를 클릭합니다.
3. 게시 대상을 선택하도록 요청 받으면 **Microsoft Azure App Service** > **다음**을 클릭한 다음 (필요한 경우) Azure 자격 증명을 사용하여 로그인합니다.
   Visual Studio가 Azure에서 직접 게시 설정을 안전하게 다운로드하고 저장합니다.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)
4. **구독**을 선택하고 **보기**에서 **리소스 형식**을 선택하며 **모바일 앱**을 확장하고 모바일 앱 백 엔드를 클릭한 다음 **확인**을 클릭합니다.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)
5. 게시 프로필 정보를 확인하고 **게시**를 클릭합니다.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

    모바일 앱 백 엔드가 성공적으로 게시되면 성공했다는 것을 나타내는 방문 페이지가 표시됩니다.

    ![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## <a name="define-table-controller"></a> 방법: 테이블 컨트롤러 정의
모바일 클라이언트에 SQL 테이블을 노출하는 테이블 컨트롤러를 정의합니다.  테이블 컨트롤러를 구성하려면 세 단계가 필요합니다.

1. 데이터 전송 개체(DTO) 클래스를 만듭니다.
2. Mobile DbContext 클래스에 테이블 참조를 구성합니다.
3. 테이블 컨트롤러를 만듭니다.

데이터 전송 개체(DTO)는 `EntityData`에서 상속하는 일반 C# 개체입니다.  예를 들면 다음과 같습니다.

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

DTO는 SQL 데이터베이스 내에서 테이블을 정의하는 데 사용됩니다.  데이터베이스 항목을 만들려면 사용하는 DbContext에 `DbSet<>` 속성을 추가합니다.  Azure Mobile Apps에 대한 기본 프로젝트 템플릿에서 DbContext는 `Models\MobileServiceContext.cs`(이)라고 합니다.

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Azure SDK가 설치되어 있으면 이제 다음과 같이 템플릿 테이블 컨트롤러를 만들 수 있습니다.

1. 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **컨트롤러를 참조하세요.를 참조하세요.를 참조하세요.** 를 참조하세요.
2. **Azure Mobile Apps 테이블 컨트롤러** 옵션을 선택한 다음 **추가**를 클릭합니다.
3. **컨트롤러 추가** 대화 상자에서 다음을 수행합니다.
   * **모델 클래스** 드롭다운에서 새 DTO를 선택합니다.
   * **DbContext** 드롭다운에서 모바일 서비스 DbContext 클래스를 선택합니다.
   * 컨트롤러 이름이 생성됩니다.
4. **추가**를 클릭합니다.

빠른 시작 서버 프로젝트는 간단한 **TodoItemController**에 대한 예제를 포함합니다.

### <a name="adjust-pagesize"></a>방법: 테이블 페이징 크기 조정
기본적으로 Azure Mobile Apps는 요청당 50개의 레코드를 반환합니다.  페이징은 클라이언트가 해당 UI 스레드와도, 서버와도 너무 오랫동안 연결되지 않으므로 최적의 사용자 환경을 보장합니다. 테이블 페이징 크기를 변경하려면 서버 쪽의 "허용되는 쿼리 크기"와 클라이언트 쪽 페이지 크기를 늘립니다. 서버 쪽의 "허용되는 쿼리 크기"는 `EnableQuery` 특성을 사용하여 조정됩니다.

    [EnableQuery(PageSize = 500)]

PageSize은 클라이언트에서 요청하는 크기보다 크거나 같습니다.  클라이언트 페이지 크기 변경에 대한 내용은 특정 클라이언트 방법 설명서를 참조하세요.

## <a name="how-to-define-a-custom-api-controller"></a>방법: 사용자 지정 API 컨트롤러 정의
사용자 지정 API 컨트롤러는 엔드포인트를 노출하여 모바일 앱 백 엔드에서 가장 기본적인 기능을 제공합니다. [MobileAppController] 특성을 사용하여 모바일 전용 API 컨트롤러를 등록할 수 있습니다. `MobileAppController` 특성은 경로를 등록하고 Mobile Apps JSON 직렬 변환기를 설정한 후 [클라이언트 버전 검사](app-service-mobile-client-and-server-versioning.md)를 켭니다.

1. Visual Studio에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가** > **컨트롤러**를 클릭하고 **웹 API 2 컨트롤러&mdash;비어 있음**을 선택한 후 **추가**를 클릭합니다.
2. `CustomController`와 같은 **컨트롤러 이름**을 제공하고 **추가**를 클릭합니다.
3. 새로운 컨트롤러 클래스 파일에서 다음 using 문을 추가합니다.

        using Microsoft.Azure.Mobile.Server.Config;
4. 다음 예제와 같이 **[MobileAppController]** 특성을 API 컨트롤러 클래스 정의에 적용합니다.

        [MobileAppController]
        public class CustomController : ApiController
        {
              //...
        }
5. App_Start/Startup.MobileApp.cs 파일에서 다음 예제와 같이 **MapApiControllers** 확장 메서드에 대한 호출을 추가합니다.

        new MobileAppConfiguration()
            .MapApiControllers()
            .ApplyTo(config);

`MapApiControllers()` 대신 `UseDefaultConfiguration()` 확장 메서드를 사용할 수도 있습니다. **MobileAppControllerAttribute** 가 적용되지 않는 모든 컨트롤러는 클라이언트에서 여전히 액세스할 수 있지만 모든 모바일 앱 클라이언트 SDK로 올바르게 사용되지 않을 수 있습니다.

## <a name="how-to-work-with-authentication"></a>방법: 인증으로 작업
Azure Mobile Apps는 App Service 인증/권한 부여를 사용하여 모바일 백 엔드를 보호합니다.  이 섹션에서는 .NET 백 엔드 서버 프로젝트에서 다음과 같은 인증 관련 작업을 수행하는 방법을 보여줍니다.

* [방법: 서버 프로젝트에 인증 추가](#add-auth)
* [방법: 애플리케이션에 대한 사용자 지정 인증 사용](#custom-auth)
* [방법: 인증된 사용자 정보 검색](#user-info)
* [방법: 인증된 사용자에 대한 데이터 액세스 제한](#authorize)

### <a name="add-auth"></a>방법: 서버 프로젝트에 인증 추가
**MobileAppConfiguration** 개체를 확장하고 OWIN 미들웨어를 구성하여 서버 프로젝트에 인증을 추가할 수 있습니다. [Microsoft.Azure.Mobile.Server.Quickstart] 패키지를 설치하고 **UseDefaultConfiguration** 확장 메서드를 호출하는 경우 3단계로 건너뛸 수 있습니다.

1. Visual Studio에서 [Microsoft.Azure.Mobile.Server.Authentication] 패키지를 설치합니다.
2. Startup.cs 프로젝트 파일에서 **Configuration** 메서드의 시작 부분에 다음 코드 줄을 추가합니다.

        app.UseAppServiceAuthentication(config);

    이 OWIN 미들웨어 구성 요소는 관련 App Service 게이트웨이에서 발급된 토큰의 유효성을 검사합니다.
3. 인증을 요구하는 모든 컨트롤러 또는 메서드에 `[Authorize]` 특성을 추가합니다.

Mobile Apps 백 엔드에 클라이언트를 인증하는 방법에 대해 알아보려면 [앱에 인증 추가](app-service-mobile-ios-get-started-users.md)를 참조하세요.

### <a name="custom-auth"></a>방법: 애플리케이션에 대한 사용자 지정 인증 사용
> [!IMPORTANT]
> 사용자 지정 인증을 사용하도록 설정하려면 Azure Portal에서 App Service에 대한 공급자를 선택하지 않고 먼저 App Service 인증을 사용하도록 설정해야 합니다. 이렇게 하면 호스트될 때 WEBSITE_AUTH_SIGNING_KEY 환경 변수가 사용되도록 설정됩니다.
> 
> 
> App Service 인증/권한 부여 공급자 중 하나를 사용하지 않으려면 본인의 고유한 로그인 시스템을 구현할 수 있습니다. 인증 토큰 생성을 위한 [Microsoft.Azure.Mobile.Server.Login] 패키지를 설치합니다.  사용자 자격 증명의 유효성 검사를 위한 고유 코드를 제공합니다. 예를 들어 데이터베이스의 솔트되고 해시된 암호를 기준으로 검사할 수 있습니다. 다음 예제에서 `isValidAssertion()` 메서드(다른 곳에 정의됨)는 이러한 검사를 담당합니다.

사용자 지정 인증은 ApiController 만들기 및 `register` 및 `login` 작업 노출로 노출됩니다. 클라이언트는 사용자로부터 정보를 수집하는 데 사용자 지정 UI를 사용해야 합니다.  그러면 정보는 표준 HTTP POST 호출을 사용하여 API에 제출됩니다. 서버가 어설션의 유효성을 검사하면 `AppServiceLoginHandler.CreateToken()` 메서드를 사용하여 토큰이 발급됩니다.  ApiController는 `[MobileAppController]` 특성을 사용하면 **안 됩니다**.

예제 `login` 작업:

        public IHttpActionResult Post([FromBody] JObject assertion)
        {
            if (isValidAssertion(assertion)) // user-defined function, checks against a database
            {
                JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
                    mySigningKey,
                    myAppURL,
                    myAppURL,
                    TimeSpan.FromHours(24) );
                return Ok(new LoginResult()
                {
                    AuthenticationToken = token.RawData,
                    User = new LoginResultUser() { UserId = userName.ToString() }
                });
            }
            else // user assertion was not valid
            {
                return this.Request.CreateUnauthorizedResponse();
            }
        }

앞의 예에서 LoginResult 및 LoginResultUser는 필요한 속성을 노출하는 직렬화 가능 개체입니다. 클라이언트는 로그인 응답을 통해 다음과 같은 형식의 JSON 개체로 반환되어야 합니다.

        {
            "authenticationToken": "<token>",
            "user": {
                "userId": "<userId>"
            }
        }

`AppServiceLoginHandler.CreateToken()` 메서드는 *audience* 및 *issuer* 매개 변수를 포함합니다. 이러한 매개 변수 모두 HTTPS 체계를 사용하여 애플리케이션 루트의 URL로 설정됩니다. 마찬가지로 *secretKey*를 애플리케이션의 서명 키의 값으로 설정해야 합니다. 키를 만들고 사용자를 가장하는 데 사용될 수 있으므로 클라이언트의 서명 키를 배포하지 마십시오. App Service에서 호스트하는 동안 *WEBSITE\_AUTH\_SIGNING\_KEY* 환경 변수를 참조하여 서명 키를 가져올 수 있습니다. 로컬 디버깅 컨텍스트에서 필요한 경우 [인증을 사용하여 로컬 디버깅](#local-debug) 섹션의 지침에 따라 키를 검색하고 이 키를 애플리케이션 설정으로 저장합니다.

발급된 토큰은 다른 클레임 및 만료 날짜를 포함할 수도 있습니다.  최소한 발급된 토큰은 제목(**sub**) 클레임을 포함해야 합니다.

인증 경로를 오버로드하여 표준 클라이언트 `loginAsync()` 메서드를 지원할 수 있습니다.  클라이언트가 `client.loginAsync('custom');`을(를) 호출하여 로그인하는 경우 경로는 `/.auth/login/custom`이어야 합니다.  `MapHttpRoute()`을(를) 사용하여 사용자 지정 인증 컨트롤러에 대한 경로를 설정할 수 있습니다.

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

> [!TIP]
> `loginAsync()` 방식을 사용하여 인증 토큰이 서비스에 대한 모든 후속 호출에 연결되어 있는지 확인합니다.
>
>

### <a name="user-info"></a>방법: 인증된 사용자 정보 검색
사용자가 App Service에서 인증을 하는 경우 .NET 백 엔드 코드에서 할당된 사용자 ID와 기타 정보에 액세스할 수 있습니다. 백 엔드에서 권한 부여를 결정하는 데 사용자 정보를 사용할 수 있습니다. 다음 코드는 요청과 연결된 사용자 ID를 가져옵니다.

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID는 공급자 특정 사용자 ID에서 파생되고 지정된 사용자 및 로그인 공급자에 대해 정적입니다.  SID는 잘못된 인증 토큰에 대한 null입니다.

또한 App Service는 로그인 공급자에서 특정 클레임을 요청할 수 있습니다. 각 ID 공급자는 ID 공급자 SDK를 사용하여 자세한 정보를 제공할 수 있습니다.  예를 들어 친구 정보에 대한 Facebook Graph API를 사용할 수 있습니다.  Azure 포털의 공급자 블레이드에서 요청된 클레임을 지정할 수 있습니다. 일부 클레임은 ID 공급자를 사용하여 추가 구성이 필요합니다.

다음 코드는 **GetAppServiceIdentityAsync** 확장 메서드를 호출하여 로그인 자격 증명을 가져오며 이는 Facebook Graph API에 대한 요청에 필요한 액세스 토큰을 포함합니다.

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

**GetAppServiceIdentityAsync** 확장 메서드를 제공하는 `System.Security.Principal`에 문을 사용하여 추가합니다.

### <a name="authorize"></a>방법: 인증된 사용자에 대한 데이터 액세스 제한
이전 섹션에서는 인증된 사용자의 사용자 ID를 검색하는 방법을 살펴보았습니다. 이 값에 따라 데이터 및 다른 리소스에 대한 액세스를 제한할 수 있습니다. 예를 들어 테이블에 userId 열을 추가하고 사용자 ID를 기준으로 쿼리 결과를 필터링하면 반환된 데이터를 허가된 사용자만 액세스하도록 할 수 있습니다. 다음 코드는 SID가 TodoItem 테이블의 UserId 열 값과 일치할 때만 데이터 행을 반환합니다.

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

`Query()` 메서드는 필터링을 처리하는 LINQ에 의해 조작될 수 있는 `IQueryable`을(를) 반환합니다.

## <a name="how-to-add-push-notifications-to-a-server-project"></a>방법: 서버 프로젝트에 푸시 알림 추가
**MobileAppConfiguration** 개체를 확장하고 Notification Hubs 클라이언트를 만들어 서버 프로젝트에 푸시 알림을 추가합니다.

1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭한 후 `Microsoft.Azure.Mobile.Server.Notifications`를 검색한 다음 **설치**를 클릭합니다.
2. 이 단계를 반복하여 Notification Hubs 클라이언트 라이브러리를 포함하는 `Microsoft.Azure.NotificationHubs` 패키지를 설치합니다.
3. App_Start/Startup.MobileApp.cs에서 초기화하는 동안 **AddPushNotifications()** 확장 메서드에 대한 호출을 추가합니다.

        new MobileAppConfiguration()
            // other features...
            .AddPushNotifications()
            .ApplyTo(config);
4. Notification Hubs 클라이언트를 만드는 다음 코드를 추가합니다.

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

이제 등록된 디바이스에 푸시 알림을 보내는 데 Notification Hubs 클라이언트를 사용할 수 있습니다. 자세한 내용은 [앱에 푸시 알림 추가](app-service-mobile-ios-get-started-push.md)를 참조하세요. Notification Hubs에 대한 자세한 내용은 [Notification Hubs 개요](../notification-hubs/notification-hubs-push-notification-overview.md)를 참조하세요.

## <a name="tags"></a>방법: 태그를 사용하여 대상 지정 푸시 활성화
Notification Hubs를 사용하면 태그를 사용하여 특정 등록에 대상이 지정된 알림을 보낼 수 있습니다. 여러 태그가 자동으로 만들어집니다.

* 설치 ID는 특정 디바이스를 식별합니다.
* 인증된 SID에 따라 사용자 ID는 특정 사용자를 식별합니다.

**MobileServiceClient**의 **installationId** 속성에서 설치 ID에 액세스할 수 있습니다.  다음 예제에서는 설치 ID를 사용하여 Notification Hubs에서 특정 설치에 태그를 추가하는 방법을 보여 줍니다.

    hub.PatchInstallation("my-installation-id", new[]
    {
        new PartialUpdateOperation
        {
            Operation = UpdateOperationType.Add,
            Path = "/tags",
            Value = "{my-tag}"
        }
    });

설치를 만들 때 푸시 알림 등록을 수행하는 동안 클라이언트가 제공한 태그는 백 엔드에서 무시됩니다. 클라이언트를 사용하여 설치에 태그를 추가하려면 이전 패턴을 사용하여 태그를 추가하는 사용자 지정 API를 만들어야 합니다.

예제는 App Service Mobile Apps 완료된 빠른 시작 샘플에서 [클라이언트 추가 푸시 알림 태그][5]를 참조하세요.

## <a name="push-user"></a>방법: 인증된 사용자에게 푸시 알림 보내기
인증된 사용자가 푸시 알림에 등록하면 사용자 ID 태그가 등록에 자동으로 추가됩니다. 이 태그를 사용하여 해당 사용자로 등록된 모든 디바이스에 푸시 알림을 보낼 수 있습니다. 다음 코드는 요청을 만드는 사용자의 SID를 가져오고 해당 사용자에 대한 모든 디바이스 등록에 템플릿 푸시 알림을 보냅니다.

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

인증된 클라이언트의 푸시 알림을 등록할 때 등록을 시도하기 전에 인증이 완료되었는지 확인합니다. 자세한 내용은 .NET 백 엔드에 대한 App Service Mobile Apps 완료된 빠른 시작 샘플에서 [사용자에게 푸시 알림 보내기][6]를 참조하세요.

## <a name="how-to-debug-and-troubleshoot-the-net-server-sdk"></a>방법: .NET 서버 SDK 디버그 및 문제 해결
Azure App Service는 ASP.NET 애플리케이션에 대한 여러 디버깅 및 문제 해결 기술을 제공합니다.

* [Azure App Service 모니터링](../app-service/web-sites-monitor.md)
* [Azure App Service에 진단 로그 사용](../app-service/troubleshoot-diagnostic-logs.md)
* [Visual Studio에서 Azure App Service 문제 해결](../app-service/troubleshoot-dotnet-visual-studio.md)

### <a name="logging"></a>로깅
표준 ASP.NET 추적 작성을 사용하여 App Service 진단 로그에 쓸 수 있습니다. 로그를 작성하려면 먼저 모바일 앱 백 엔드에서 진단을 활성화해야 합니다.

진단을 사용하도록 설정하고 로그에 쓰려면:

1. [진단을 사용하는 방법](../app-service/troubleshoot-diagnostic-logs.md#enablediag)에서 단계를 수행합니다.
2. 코드 파일에 다음 using 문을 추가합니다.

        using System.Web.Http.Tracing;
3. .NET 백 엔드에서 진단 로그에 작성하려면 다음과 같이 추적 작성기를 만듭니다.

        ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
        traceWriter.Info("Hello, World");
4. 서버 프로젝트를 다시 게시하고 모바일 앱 백 엔드에 액세스하여 로깅을 통해 코드 경로를 실행합니다.
5. [방법: 로그 다운로드](../app-service/troubleshoot-diagnostic-logs.md#download)에 설명된 대로 로그를 다운로드하고 평가합니다.

### <a name="local-debug"></a>인증을 사용하여 로컬 디버깅
애플리케이션을 로컬로 실행하여 변경 내용을 클라우드에 게시하기 전에 테스트할 수 있습니다. 대부분의 Azure Mobile Apps 백 엔드의 경우 Visual Studio에 있는 동안 *F5* 를 누릅니다. 그러나 인증을 사용할 때 몇 가지 추가 고려 사항이 있습니다.

클라우드 기반 모바일 앱에서 App Service 인증/권한 부여를 구성해야 하며 클라이언트가 클라우드 엔드포인트를 대체 로그인 호스트로 지정해야 합니다. 필요한 구체적인 단계는 클라이언트 플랫폼에 대한 설명서를 참조하세요.

모바일 백 엔드에 [Microsoft.Azure.Mobile.Server.Authentication] 을 설치했는지 확인합니다. 그런 다음 `MobileAppConfiguration`를 `HttpConfiguration`에 적용한 후 애플리케이션의 OWIN 시작 클래스에 다음을 추가합니다.

        app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
        {
            SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
            ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
            ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
            TokenHandler = config.GetAppServiceTokenHandler()
        });

앞의 예제에서는 HTTPS 체계를 사용하여 Web.config 파일 내에서 *authAudience* 및 *authIssuer* 애플리케이션 설정을 애플리케이션 루트의 URL로 구성해야 합니다. 마찬가지로 *authSigningKey*를 애플리케이션의 서명 키의 값으로 설정해야 합니다.
서명 키를 가져오려면:

1.  [Azure Portal]
2. **도구**, **Kudu**, **이동**을 클릭합니다.
3. Kudu 관리 사이트에서 **환경**을 클릭합니다.
4. *WEBSITE\_AUTH\_SIGNING\_KEY*에 대한 값을 찾습니다.

로컬 애플리케이션 구성에서 *authSigningKey* 매개 변수에 대한 서명 키를 사용합니다.  로컬로 실행 중일 때 모바일 백 엔드가 이제 클라이언트가 클라우드 기반 엔드포인트에서 가져오는 토큰을 확인하게 됩니다.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[Azure Portal]: https://portal.azure.com
[NuGet.org]: https://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx
