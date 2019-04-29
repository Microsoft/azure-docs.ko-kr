---
title: Mobile Apps를 사용하여 UWP(유니버설 Windows 플랫폼) 앱에 오프라인 동기화 사용 | Microsoft Docs
description: Azure 모바일 앱을 사용하여 UWP(유니버설 Windows 플랫폼) 앱에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다.
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 69ee9e7101a2b7337e1e42ff5ae09954fbfd50b2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128052"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Windows 앱에 대해 오프라인 동기화 사용
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 UWP(유니버설 Windows 플랫폼) 앱에 오프라인 지원을 추가하는 방법을 보여 줍니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 디바이스가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 백 엔드와 동기화됩니다.

이 자습서에서는 Azure Mobile Apps의 오프라인 기능을 지원하도록 자습서 [Windows 앱 만들기]의 UWP 앱 프로젝트를 업데이트합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화]항목을 참조하세요.

## <a name="requirements"></a>요구 사항  
이 자습서를 사용하려면 다음 필수 구성 요소가 필요합니다.

* Windows 8.1 이상에서 실행 중인 Visual Studio 2013
* [Windows 앱 만들기][Windows 앱 만들기]을 참조하세요.
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [유니버설 Windows 플랫폼용 SQLite 개발](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>오프라인 기능을 지원하도록 클라이언트 앱 업데이트
Azure 모바일 앱 오프라인 기능을 사용하면 오프라인 시나리오에서 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 로컬 저장소에서 [SyncContext][synccontext]를 초기화합니다. 그런 다음 [IMobileServiceSyncTable][IMobileServiceSyncTable] 인터페이스를 통해 테이블을 참조합니다. SQLite는 디바이스의 로컬 저장소로 사용됩니다.

1. [유니버설 Windows 플랫폼용 SQLite 런타임](https://sqlite.org/2016/sqlite-uwp-3120200.vsix)을 설치합니다.
2. Visual Studio에서 [Windows 앱 만들기] 자습서에서 완료한 UWP 앱용 NuGet 패키지 관리자 프로젝트를 엽니다.
    **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 패키지를 검색하고 설치합니다.
3. 솔루션 탐색기에서 마우스 오른쪽 단추로 **참조** > **참조 추가...** > **유니버설 Windows** > **확장**을 클릭하고 **유니버설 Windows 플랫폼용 SQLite** 및 **유니버설 Windows 플랫폼 앱용 Visual C++ 2015 런타임**을 둘 다 사용하도록 설정합니다.

    ![SQLite UWP 참조 추가][1]
4. MainPage.xaml.cs 파일을 열고 `#define OFFLINE_SYNC_ENABLED` 정의의 주석 처리를 제거합니다.
5. Visual Studio에서 **F5** 키를 눌러 클라이언트 앱을 다시 빌드하고 실행합니다. 오프라인 동기화를 활성화하기 전에 수행한 것과 동일하게 앱이 작동합니다. 그러나 로컬 데이터베이스는 이제 오프라인 시나리오에서 사용할 수 있는 데이터로 채워집니다.

## <a name="update-sync"></a>앱을 업데이트하여 백 엔드에서 분리
이 섹션에서는 모바일 앱 백 엔드에 대한 연결을 끊고 오프라인 상황을 시뮬레이션합니다. 데이터 항목을 추가하면 예외 처리기는 앱이 오프라인 모드임을 사용자에게 알립니다. 이 상태에서 로컬 저장소에 추가된 새 항목은 푸시가 연결된 상태에서 다음에 실행될 경우 모바일 앱 백 엔드에 동기화됩니다.

1. 공유 프로젝트에서 App.xaml.cs를 편집합니다. **MobileServiceClient**의 초기화를 주석 처리하고 잘못된 모바일 앱 URL을 사용하는 다음 줄을 추가합니다.

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    디바이스에서 Wi-Fi 및 셀룰러 네트워크를 사용하지 않도록 설정하여 오프라인 동작을 시연하거나 비행기 모드를 사용할 수 있습니다.
2. **F5** 를 눌러 응용 프로그램을 빌드 및 실행합니다. 앱을 시작하는 경우 동기화는 새로 고침에 실패합니다.
3. 새 항목을 입력하고 [MobileServicePushFailedException] 을 클릭할 때마다 푸시가 **CancelledByNetworkError**상태로 실패하는지 확인합니다. 그러나 새 todo 항목은 모바일 앱 백 엔드에 푸시할 수 있을 때까지 로컬 저장소에 위치합니다.  프로덕션 앱에서 이러한 예외를 무시하는 경우 클라이언트 앱은 모바일 앱 백 엔드에 연결된 것처럼 작동합니다.
4. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.
5. (옵션) Visual Studio에서 **서버 탐색기**를 엽니다. **Azure**->**SQL Databases**에 있는 데이터베이스로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다. 이제 SQL 데이터베이스 테이블 및 콘텐츠를 찾아볼 수 있습니다. 백 엔드 데이터베이스의 데이터가 변경되지 않은 것을 확인합니다.
6. (옵션) Fiddler 또는 Postman과 같은 REST 도구를 사용하여 `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`형식의 GET 쿼리를 통해 모바일 백 엔드를 쿼리합니다.

## <a name="update-online-app"></a>모바일 앱 백 엔드를 다시 연결하도록 앱 업데이트
이 섹션에서는 앱을 Mobile App 백 엔드에 다시 연결합니다. 이렇게 변경하면 앱의 네트워크 다시 연결이 시뮬레이트됩니다.

애플리케이션을 처음 실행하는 경우 `OnNavigatedTo` 이벤트 처리기는 `InitLocalStoreAsync`를 호출합니다. 차례로 `SyncAsync`를 호출하여 백 엔드 데이터베이스와 로컬 저장소를 동기화합니다. 앱은 시작 시 동기화를 시도합니다.

1. 공유 프로젝트에서 App.xaml.cs를 열고 `MobileServiceClient` 의 이전 초기화에서 주석 처리를 제거하여 올바른 모바일 앱 URL을 사용합니다.
2. **F5** 를 눌러 앱을 다시 빌드하고 실행합니다. 앱은 푸시 및 끌어오기 작업을 사용하여 `OnNavigatedTo` 이벤트 처리기가 실행되면 로컬 변경 내용을 Azure Mobile App 백 엔드와 동기화합니다.
3. (선택 사항) SQL Server 개체 탐색기 또는 Fiddler와 같은 REST 도구를 사용하여 업데이트된 데이터를 봅니다. Azure 모바일 앱 백 엔드 데이터베이스와 로컬 저장소 간에 데이터는 동기화되었습니다.
4. 앱에서 로컬 저장소에서 완료할 몇 개 항목 옆의 확인란을 클릭합니다.

   `UpdateCheckedTodoItem`은 `SyncAsync`를 호출하여 Mobile App 백 엔드와 전체 항목을 동기화합니다. `SyncAsync` 는 푸시와 끌어오기를 둘 다 호출합니다. 그러나 **클라이언트가 변경한 테이블에 대해 끌어오기를 실행할 때마다 푸시도 항상 자동으로 실행됩니다**. 따라서 로컬 저장소의 모든 테이블 및 관계가 동기화된 상태로 유지될 수 있습니다. 이 동작으로 예기치 않은 푸시가 발생할 수도 있습니다.  이 동작에 대한 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화]를 참조하세요.

## <a name="api-summary"></a>API 요약
모바일 서비스의 오프라인 기능을 지원하기 위해 [IMobileServiceSyncTable] 인터페이스를 사용하고 로컬 SQLite 데이터베이스에서 [MobileServiceClient.SyncContext][synccontext]를 초기화했습니다. 오프라인의 경우 Mobile Apps에 대한 일반적인 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하는 반면 작업은 로컬 저장소에 발생합니다. 다음 메서드는 서버와 로컬 저장소를 동기화하는 데 사용됩니다.

* **[PushAsync]** 이 메서드가 [IMobileServicesSyncContext]의 멤버이기 때문에 모든 테이블의 변경 사항을 백 엔드에 푸시합니다. 로컬 변경 내용이 포함된 레코드만이 서버에 전송됩니다.
* **[PullAsync]** 끌어오기는 [IMobileServiceSyncTable]에서 시작됩니다. 테이블에 추적된 변경 내용이 있는 경우 암시적 푸시가 실행되어 관계와 함께 로컬 저장소의 모든 테이블이 일관성을 유지하는지 확인합니다. *pushOtherTables*은 다른 컨텍스트에서 테이블이 암시적 푸시에 푸시되는지를 제어합니다. *query* 매개 변수는 [IMobileServiceTableQuery<T>][IMobileServiceTableQuery] 또는 OData 쿼리 문자열을 사용하여 반환된 데이터를 필터링합니다. *queryId* 매개 변수를 사용하여 증분 동기화를 정의할 수 있습니다. 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화](app-service-mobile-offline-data-sync.md#how-sync-works)를 참조하세요.
* **[PurgeAsync]** 앱은 주기적으로 이 메서드를 호출하여 로컬 저장소에서 오래된 데이터를 삭제해야 합니다. 아직 동기화되지 않은 변경 내용을 삭제해야 하는 경우 *force* 매개 변수를 사용합니다.

이 개념에 대한 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화](app-service-mobile-offline-data-sync.md#how-sync-works)를 참조하세요.

## <a name="more-info"></a>자세한 정보
다음 항목에서는 Mobile Apps의 오프라인 동기화 기능에 추가 배경 정보를 제공합니다.

* [Azure Mobile Apps에서 오프라인 데이터 동기화]
* [Azure Mobile Apps .NET SDK 사용 방법][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Azure Mobile Apps에서 오프라인 데이터 동기화]: app-service-mobile-offline-data-sync.md
[Windows 앱 만들기]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
