---
title: Mobile Services에서 Azure App Service로 업그레이드 - Node.js
description: Mobile Services 애플리케이션을 App Service 모바일 앱으로 쉽게 업그레이드하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: yochayk
editor: ''
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: e54ed6c526182cea57e2d40f356ad9236510d82c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128069"
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>기존 Node.js Azure 모바일 서비스를 App Service로 업그레이드
App Service 모바일은 Microsoft Azure를 사용하여 모바일 애플리케이션을 빌드하는 새로운 방법입니다. 자세한 내용은 [Mobile Apps 정의]를 참조하세요.

이 문서에서는 기존 Node.js 백 엔드 애플리케이션을 Azure Mobile Services에서 새로운 App Service Mobile Apps로 업그레이드하는 방법을 설명합니다. 이 업그레이드를 수행하는 동안 기존 Mobile Services 애플리케이션이 계속 작동할 수 있습니다.  Node.js 백 엔드 애플리케이션을 업그레이드해야 하는 경우 [.NET Mobile Services 업그레이드](app-service-mobile-net-upgrading-from-mobile-services.md)를 참조하세요.

모바일 백 엔드가 Azure App Service로 업그레이드되면 모든 App Service 기능에 액세스할 수 있고 Mobile Services 가격 책정이 아닌 [App Service 가격 책정]에 따라 요금이 청구됩니다.

## <a name="migrate-vs-upgrade"></a>마이그레이션과 업그레이드 비교
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> 업그레이드를 진행하기 전에 [마이그레이션을 수행](app-service-mobile-migrating-from-mobile-services.md) 하는 것이 좋습니다. 이러한 방식으로 동일한 App Service 계획에 두 버전의 애플리케이션을 모두 추가 비용 없이 둘 수 있습니다.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Mobile Apps Node.js 서버 SDK에서 향상된 기능
새 [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) 업그레이드는 다음을 포함하여 다양한 향상된 기능을 제공합니다.

* [Express 프레임워크](https://expressjs.com/en/index.html)에 기반하여 새 노드 SDK는 간단하고 새 노드 버전을 유지할 수 있도록 설계됩니다. Express 미들웨어를 통해 애플리케이션 동작을 사용자 지정할 수 있습니다.
* Mobile Services SDK에 비해 성능이 크게 개선되었습니다.
* 모바일 백 엔드를 통해 웹 사이트를 호스팅할 수 있습니다. 마찬가지로 기존 v4 애플리케이션에 Azure 모바일 SDK를 추가하기는 쉽습니다.
* 플랫폼 간 및 로컬 개발을 위해 작성된 Mobile Apps SDK는 Windows, Linux 및 OSX 플랫폼에서 개발되고 로컬로 실행될 수 있습니다. 배포하기 전에 [Mocha](https://mochajs.org/) 테스트를 실행하는 것 같은 일반적인 노드 개발 기술을 사용하는 것은 쉽습니다.

## <a name="overview"></a>기본 업그레이드 개요
Node.js 백 엔드 업그레이드를 지원하기 위해 Azure App Service는 호환성 패키지를 제공했습니다.  업그레이드 후 새 App Service 사이트에 배포할 수 있는 새 사이트를 갖습니다.

Mobile Services 클라이언트 SDK는 새 Mobile Apps 서버 SDK와 호환할 수 **없습니다** . 앱에 대한 서비스 연속성을 제공하기 위해 현재 게시된 클라이언트를 제공하는 사이트에 변경 내용을 게시하지 않아야 합니다. 대신 중복으로 제공한 새 모바일 앱을 만들어야 합니다. 이 애플리케이션을 동일한 App Service 계획에 두어 추가 비용이 발생하지 않도록 할 수 있습니다.

다음 두 가지 버전의 애플리케이션이 있습니다. 하나는 동일하게 유지되고 야생에서 게시된 앱을 제공하며 다른 하나는 업그레이드하고 새 클라이언트 릴리스를 대상으로 할 수 있습니다. 진도에 맞게 코드를 이동하고 테스트할 수 있지만 수행한 버그 수정이 둘 모두에 적용되도록 해야 합니다. 야생에서 원하는 수의 클라이언트 앱이 최신 버전으로 업데이트되면 원하는 경우 원래 마이그레이션된 앱을 삭제할 수 있습니다. 동일한 App Service 계획에서 모바일 앱으로 호스팅되는 경우 금전적인 추가 비용이 발생하지 않습니다.

업그레이드 프로세스에 대한 전체 개요는 다음과 같습니다.

1. 기존(마이그레이션된) Azure 모바일 서비스를 다운로드합니다.
2. 호환성 패키지를 사용하여 프로젝트를 Azure 모바일 앱으로 변환합니다.
3. 모든 차이(예: 인증 설정)를 수정합니다.
4. 새 App Service에 변환된 Azure 모바일 앱 프로젝트를 배포합니다.
5. 새 모바일 앱을 사용하는 새 버전의 클라이언트 애플리케이션을 릴리스합니다.
6. (선택 사항) 원래 마이그레이션된 모바일 서비스 앱 삭제

삭제는 원래 마이그레이션된 모바일 서비스에 트래픽이 표시되지 않을 때 발생할 수 있습니다.

## <a name="install-npm-package"></a> 필수 조건 설치
로컬 컴퓨터에 [노드]를 설치해야 합니다.  또한 호환성 패키지를 설치해야 합니다.  노드를 설치한 후 새 cmd 또는 PowerShell 프롬프트에서 다음 명령을 실행할 수 있습니다.

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Azure Mobile Services 스크립트 가져오기
* [Azure Portal]에 로그인합니다.
* **모든 리소스** 또는 **App Services**를 사용하여 Mobile Services 사이트를 찾습니다.
* 사이트 내에서 **도구** -> **Kudu** -> **이동**을 클릭하여 Kudu 사이트를 엽니다.
* **디버그 콘솔** -> **PowerShell**을 클릭하여 디버그 콘솔을 엽니다.
* 각 디렉터리를 차례로 클릭하여 `site/wwwroot/App_Data/config` (으)로 이동합니다.
* `scripts` 디렉터리 옆에 있는 다운로드 아이콘을 클릭합니다.

ZIP 형식의 스크립트를 다운로드합니다.  로컬 컴퓨터에 새 디렉터리를 만들고 디렉터리 내의 `scripts.ZIP` 파일의 압축을 풉니다.  `scripts` 디렉터리를 만듭니다.

## <a name="scaffold-app"></a> 새 Azure Mobile Apps 백 엔드 스캐폴드
스크립트 디렉터리를 포함하는 디렉터리에서 다음 명령을 실행합니다.

```scaffold-mobile-app scripts out```

`out` 디렉터리에 스캐폴드된 Azure Mobile Apps 백 엔드를 만듭니다.  필수는 아니지만 `out` 디렉터리를 소스 코드 리포지토리로 확인하는 것이 좋습니다.

## <a name="deploy-ama-app"></a> Azure Mobile Apps 백 엔드 배포
배포하는 동안 다음을 수행해야 합니다.

1. [Azure Portal]에서 새 모바일 앱을 만듭니다.
2. 연결된 데이터베이스에서 `createViews.sql` 스크립트를 실행합니다.
3. 모바일 서비스에 연결된 데이터베이스를 새 App Service에 연결합니다.
4. 다른 리소스(예: Notification Hubs)를 새로운 App Service에 연결합니다.
5. 새 사이트에 생성된 코드를 배포합니다.

### <a name="create-a-new-mobile-app"></a>새 모바일 앱 만들기
1. [Azure Portal]에서 로그인합니다.
2. **+새로 만들기** > **웹 + 모바일** > **모바일 앱**을 클릭한 다음 모바일 앱 백 엔드에 대한 이름을 제공합니다.
3. **리소스 그룹**에 대해 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다(앱과 같은 이름 사용).

    다른 App Service 계획을 선택하거나 새 App Service 계획을 만들 수 있습니다. App Services 계획에 대한 자세한 내용과 다른 가격 책정 계층 및 원하는 위치에서 새 계획을 만드는 방법은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.
4. **App Service 계획**에 대해서는 기본 계획( [표준 계층](https://azure.microsoft.com/pricing/details/app-service/)에)이 선택되어 있습니다. 또한 다른 계획을 선택하거나 [새 계획을 만들 수 있습니다](../app-service/app-service-plan-manage.md#create-an-app-service-plan). App Service 계획의 설정에 따라 앱과 연결된 [위치, 기능, 비용 및 계산 리소스](https://azure.microsoft.com/pricing/details/app-service/)가 결정됩니다.

    계획을 결정한 다음 **만들기**를 클릭합니다. 이렇게 모바일 앱 백 엔드를 만듭니다.

### <a name="run-createviewssql"></a>CreateViews.SQL을 실행합니다.
스캐폴드된 앱은 `createViews.sql`(이)라는 파일을 포함합니다.  이 스크립트는 대상 데이터베이스에 대해 실행되어야 합니다.  대상 데이터베이스에 대한 연결 문자열은 **연결 문자열** 아래 **설정** 페이지의 마이그레이션된 모바일 서비스에서 가져올 수 있습니다.  `MS_TableConnectionString`(이)라고 합니다.

SQL Server Management Studio 또는 Visual Studio 내에서 이 스크립트를 실행할 수 있습니다.

### <a name="link-the-database-to-your-app-service"></a>데이터베이스를 App Service에 연결
기존 데이터베이스를 App Service에 연결:

* [Azure Portal]에서 App Service를 엽니다.
* **모든 설정** -> **데이터 연결**을 선택합니다.
* **+ 추가**를 클릭합니다.
* 드롭다운 목록에서 **SQL Database**
* **SQL Database** 아래에서 기존 데이터베이스를 선택한 다음 **선택**을 클릭합니다.
* **연결 문자열** 아래에서 데이터베이스에 대한 사용자 이름 및 암호를 입력한 다음 **확인**을 클릭합니다.
* **데이터 연결 추가** 페이지에서 **확인**을 클릭합니다.

마이그레이션된 모바일 서비스에서 대상 데이터베이스에 대한 연결 문자열을 확인하여 사용자 이름 및 암호를 찾을 수 있습니다.

### <a name="set-up-authentication"></a>인증 설정
Azure Mobile Apps를 사용하면 서비스 내에서 Azure Active Directory, Facebook, Google, Microsoft 및 Twitter 인증을 구성할 수 있습니다.  사용자 지정 인증을 개별적으로 개발해야 합니다.  자세한 내용은 [인증 개념] 설명서 및 [인증 빠른 시작] 설명서를 참조하세요.  

## <a name="updating-clients"></a>모바일 클라이언트 업데이트
작동하는 모바일 앱 백 엔드가 있으면 그것을 사용하는 클라이언트 애플리케이션의 새 버전에서 작동할 수 있습니다. 또한 Mobile Apps는 클라이언트 SDK의 새 버전을 포함하고 위의 서버 업그레이드와 유사합니다. 따라서 Mobile Apps 버전을 설치하기 전에 Mobile Services SDK에 대한 모든 참조를 제거해야 합니다.

버전 간의 주요 변경 사항 중 하나는 생성자가 애플리케이션 키를 더 이상 필요로 하지 않는다는 점입니다.
이제 모바일 앱의 URL에 간단히 전달할 수 있습니다. 예를 들어 .NET 클라이언트에서 `MobileServiceClient` 생성자는 다음과 같습니다.

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

아래 링크를 통해 새 SDK를 설치하고 새 구조를 사용하는 데 대한 내용을 읽을 수 있습니다.

* [Android 버전 2.2 이상](app-service-mobile-android-how-to-use-client-library.md)
* [iOS 버전 3.0.0 이상](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET(Windows/Xamarin) 버전 2.0.0 이상](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova 버전 2.0 이상](app-service-mobile-cordova-how-to-use-client-library.md)

애플리케이션이 푸시 알림을 사용하면 일부 변경 사항이 있는 경우와 같이 각 플랫폼에 대한 특정 등록 지침을 기록합니다.

새 클라이언트 버전을 준비할 때 업그레이드된 서버 프로젝트에 사용해보세요. 작동하는지 유효성을 검사한 후에 고객에게 애플리케이션의 새 버전을 릴리스할 수 있습니다. 결국 고객이 이러한 업데이트를 받고 나면 Mobile Services 버전의 앱을 삭제할 수 있습니다. 이 시점에서 최신 모바일 앱 서버 SDK를 사용하여 App Service Mobile Apps의 업그레이드를 완전히 완료했습니다.

<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Mobile Apps 정의]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service 가격 책정]: https://azure.microsoft.com/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[인증 개념]: ../app-service/overview-authentication-authorization.md
[인증 빠른 시작]: app-service-mobile-auth.md

[Azure Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
