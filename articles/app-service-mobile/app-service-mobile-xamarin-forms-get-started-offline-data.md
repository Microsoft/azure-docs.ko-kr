---
title: Azure Mobile App(Xamarin.Forms)에 대해 오프라인 동기화 사용 | Microsoft Docs
description: App Service 모바일 앱을 사용하여 Xamarin.Forms 애플리케이션에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다.
documentationcenter: xamarin
author: conceptdev
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: crdun
ms.openlocfilehash: 506c59ca24aeafbac59b1508bb78142051302765
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127882"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Xamarin.Forms 모바일 앱에 대해 오프라인 동기화 사용
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>개요
이 자습서에서는 Xamarin.Forms용 Azure Mobile Apps의 오프라인 동기화 기능을 소개합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 디바이스가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 서비스와 동기화됩니다.

이 자습서는 [Xamarin iOS 앱 만들기] 자습서를 완료할 때 만든 Mobile Apps에 대한 Xamarin.Forms 빠른 시작 솔루션을 기반으로 합니다. Xamarin.Forms에 대한 빠른 시작 솔루션에는 사용하도록 설정해야 하는 오프라인 동기화를 지원하는 코드가 포함되어 있습니다. 이 자습서에서는 빠른 시작 솔루션을 업데이트하여 Azure Mobile Apps의 오프라인 기능을 사용하도록 설정합니다. 또한 앱에서 오프라인 관련 코드를 중점적으로 다루겠습니다. 다운로드한 빠른 시작 솔루션을 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용][1]을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화][2] 항목을 참조하세요.

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>빠른 시작 솔루션에서 오프라인 동기화 기능 사용
오프라인 동기화 코드는 C# 전처리기 지시문을 사용하여 프로젝트에 포함됩니다. **OFFLINE\_SYNC\_ENABLED** 기호가 정의된 경우 이러한 코드 경로는 빌드에 포함됩니다. Windows 앱의 경우 SQLite 플랫폼도 설치해야 합니다.

1. Visual Studio에서 솔루션 > **솔루션에 대한 NuGet 패키지 관리...** 를 마우스 오른쪽 단추로 클릭한 후 솔루션의 모든 프로젝트에서 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 패키지를 검색하고 설치합니다.
2. 솔루션 탐색기에서 이름에 **이식 가능** 이 있는 프로젝트(이식 가능한 클래스 라이브러리 프로젝트)에서 TodoItemManager.cs 파일을 연 후 다음 전처리기 지시문의 주석 처리를 제거합니다.

        #define OFFLINE_SYNC_ENABLED
3. (선택 사항) Windows 디바이스를 지원하려면 다음 SQLite 런타임 패키지 중 하나를 설치합니다.

   * **Windows 8.1 런타임:** [Windows 8.1용 SQLite][3]를 설치합니다.
   * **Windows Phone 8.1:** [Windows Phone 8.1용 SQLite][4]를 설치합니다.
   * **유니버설 Windows 플랫폼:** [유니버설 Windows 플랫폼용 SQLite][5]를 설치합니다.

     빠른 시작이 유니버설 Windows 프로젝트를 포함하지 않으면 유니버설 Windows 플랫폼은 Xamarin Forms으로 지원됩니다.
4. (선택 사항) 각 Windows 앱 프로젝트에서 마우스 오른쪽 단추로 **참조** > **참조 추가...** 를 클릭하고 **Windows** 폴더 > **확장**을 확장합니다.
    **Windows용 Visual C++ 2013 런타임** SDK와 함께 적절한 **Windows용 SQLite**를 사용하도록 설정합니다.
    SQLite SDK 이름은 Windows 플랫폼마다 약간 다릅니다.

## <a name="review-the-client-sync-code"></a>클라이언트 동기화 코드 검토
`#if OFFLINE_SYNC_ENABLED` 지시문 내의 자습서 코드에 이미 포함된 내용에 대한 간략한 개요입니다. 오프라인 동기화 기능은 이식 가능한 클래스 라이브러리 프로젝트의 TodoItemManager.cs 프로젝트 파일에 있습니다. 기능의 개념적 개요는 [Azure Mobile Apps에서 오프라인 데이터 동기화][2]를 참조하세요.

* 모든 테이블 작업을 수행하려면 먼저 로컬 저장소를 초기화해야 합니다. 로컬 저장소 데이터베이스는 다음 코드를 사용하여 **TodoItemManager** 클래스 생성자에서 초기화됩니다.

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    이 코드는 **MobileServiceSQLiteStore** 클래스를 사용하여 새로운 로컬 SQLite 데이터베이스를 만듭니다.

    **DefineTable** 메서드는 제공된 형식의 필드와 일치하는 테이블을 로컬 저장소에 만듭니다.  이 형식은 원격 데이터베이스에 있는 열을 모두 포함하지 않아도 됩니다. 열의 하위 집합 저장은 불가능합니다.
* **TodoItemManager**에서 **todoTable** 필드는 **IMobileServiceTable** 대신 **IMobileServiceSyncTable** 형식입니다. 이 클래스는 모든 만들기, 읽기, 업데이트 및 삭제(CRUD) 테이블 작업에 대해 로컬 저장소 데이터베이스를 사용합니다. **IMobileServiceSyncContext**에서 **PushAsync**를 호출하여 Mobile App 백 엔드에 해당 변경 내용을 푸시합니다. 동기화 컨텍스트를 사용하면 모든 테이블의 변경 내용을 추적하고 밀어넣어서 테이블 관계를 보존할 수 있습니다. **PushAsync**를 호출하는 경우 클라이언트 앱을 수정합니다.

    다음 **SyncAsync** 메서드는 모바일 앱 백 엔드와 동기화하기 위해 호출됩니다.

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    이 샘플에서는 기본 동기화 처리기와 함께 간단한 오류 처리를 사용합니다. 실제 애플리케이션에서는 사용자 지정 **IMobileServiceSyncHandler** 구현을 사용하여 네트워크 상태 및 서버 충돌과 같은 다양한 오류를 처리합니다.

## <a name="offline-sync-considerations"></a>오프라인 동기화 고려 사항
이 샘플에서 **SyncAsync** 메서드는 시작 시 동기화가 요청된 경우에만 호출됩니다.  Android 또는 iOS 앱에서 동기화를 시작하려면 항목 목록을 풀다운하고 Windows의 경우 **동기화** 단추를 사용합니다. 실제 애플리케이션에서는 네트워크 상태가 변경될 때 동기화가 트리거되도록 할 수도 있습니다.

끌어오기가 컨텍스트에 의해 추적되는 로컬 업데이트를 보류 중인 테이블에 대해 실행되는 경우 끌어오기 작업은 먼저 자동으로 컨텍스트 푸시를 트리거합니다. 이 샘플에서 항목을 새로 고침, 추가 및 완료하는 경우 명시적인 **PushAsync** 호출을 생략할 수 있습니다.

제공된 코드에서 원격 TodoItem 테이블에 있는 모든 레코드를 쿼리하지만 쿼리 ID 및 쿼리를 **PushAsync**로 전달하여 레코드를 필터링할 수도 있습니다. 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화][2]에서 *증분 동기화* 섹션을 참조하세요.

## <a name="run-the-client-app"></a>클라이언트 앱을 실행합니다.
이제 오프라인 동기화를 사용하도록 설정하면 각 플랫폼에서 클라이언트 애플리케이션을 한 번 이상 실행하여 로컬 저장소 데이터베이스를 채웁니다. 나중에 앱이 오프라인인 동안 오프라인 시나리오를 시뮬레이션하고 로컬 저장소에 있는 데이터를 수정합니다.

## <a name="update-the-sync-behavior-of-the-client-app"></a>클라이언트 앱의 동기화 동작 업데이트
이 섹션에서 백엔드에 잘못된 애플리케이션 URL을 사용하여 오프라인 시나리오를 시뮬레이션하도록 클라이언트 프로젝트를 수정합니다. 또는 "비행기 모드"로 디바이스를 전환하여 네트워크 연결을 해제할 수 있습니다.  데이터 항목을 추가하거나 변경하는 경우 이러한 변경 내용은 로컬 저장소에 보관되지만 연결이 다시 설정될 때까지 백 엔드 데이터 저장소에 동기화되지 않습니다.

1. 솔루션 탐색기에서 **이식 가능** 프로젝트의 Constants.cs 프로젝트 파일을 열고 `ApplicationURL` 값이 다음과 같은 잘못된 URL을 가리키도록 변경합니다.

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. **이식 가능** 프로젝트에서 TodoItemManager.cs 파일을 연 후 기본 **Exception** 클래스의 **catch**를 **SyncAsync**의 **try...catch** 블록에 추가합니다. 이 **catch** 블록은 예외 메시지를 다음과 같이 콘솔에 씁니다.

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. 클라이언트 앱을 빌드 및 실행합니다.  새 항목을 추가합니다. 백 엔드와 동기화하려는 각 시도에 대해 콘솔에 로그인된 예외를 확인합니다. 이러한 새 항목은 모바일 백 엔드에 푸시할 수 있을 때까지 로컬 저장소에만 있습니다. 클라이언트 앱은 백 엔드에 연결된 것처럼 동작하며 모든 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 지원합니다.
4. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.
5. (선택 사항) Azure SQL Database 테이블을 보는 Visual Studio를 사용하여 백 엔드 데이터베이스에서 데이터가 변경되지 않았음을 확인합니다.

    Visual Studio에서 **서버 탐색기**를 엽니다. **Azure**->**SQL Databases**에 있는 데이터베이스로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다. 이제 SQL 데이터베이스 테이블 및 콘텐츠를 찾아볼 수 있습니다.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>모바일 백 엔드를 다시 연결하도록 클라이언트 앱 업데이트
이 섹션에서는 앱을 모바일 백 엔드에 다시 연결하여 다시 온라인 상태로 전환되는 앱을 시뮬레이트합니다. 새로 고침 제스처를 수행하면 데이터가 모바일 백 엔드에 동기화됩니다.

1. Constants.cs를 다시 엽니다. 올바른 URL을 가리키도록 `applicationURL` 를 수정합니다.
2. 클라이언트 앱을 다시 빌드하고 실행합니다. 앱을 시작한 후에 모바일 앱 백 엔드와 동기화하려고 합니다. 디버그 콘솔에 기록된 예외가 없는지 확인합니다.
3. (선택 사항) SQL Server 개체 탐색기 또는 Fiddler나 [Postman][6] 같은 REST 도구를 사용하여 업데이트된 데이터를 봅니다. 백 엔드 데이터베이스와 로컬 저장소의 데이터가 동기화된 것을 확인합니다.

    데이터베이스와 로컬 저장소 간에 데이터가 동기화되었으며 앱의 연결이 끊어진 동안 추가한 항목을 포함합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Mobile Apps에서 오프라인 데이터 동기화][2]
* [Azure Mobile Apps .NET SDK 사용 방법][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
