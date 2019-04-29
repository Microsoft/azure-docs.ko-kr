---
title: Azure 모바일 앱에 대해 오프라인 동기화 사용(Cordova) | Microsoft Docs
description: App Service 모바일 앱을 사용하여 Cordova 애플리케이션에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다.
documentationcenter: cordova
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 44c54b570a38eb1a3b9ca773893599d1d497dfa2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110992"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Cordova 모바일 앱에 대해 오프라인 동기화 사용
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

이 자습서에서는 Cordova용 Azure Mobile Apps의 오프라인 동기화 기능을 소개합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다.  디바이스가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 서비스와 동기화됩니다.

이 자습서는 [Apache Cordova 빠른 시작]자습서를 완료할 때 만든 Mobile Apps에 대한 Cordova 빠른 시작 솔루션을 기반으로 합니다. 이 자습서에서는 빠른 시작 솔루션을 업데이트하여 Azure Mobile Apps의 오프라인 기능을 추가합니다.  또한 앱에서 오프라인 관련 코드를 중점적으로 다루겠습니다.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화]항목을 참조하세요. API 사용에 대한 자세한 내용은 [API 설명서](https://azure.github.io/azure-mobile-apps-js-client)를 참조하세요.

## <a name="add-offline-sync-to-the-quickstart-solution"></a>오프라인 동기화를 빠른 시작 솔루션에 추가
오프라인 동기화 코드를 앱에 추가해야 합니다. 오프라인 동기화에는 cordova-sqlite-storage 플러그 인이 필요하며 이는 Azure Mobile Apps 플러그 인이 프로젝트에 포함될 때 앱에 자동으로 추가됩니다. 빠른 시작 프로젝트에는 이러한 플러그 인이 모두 포함됩니다.

1. Visual Studio의 솔루션 탐색기에서 index.js를 열고 다음 코드를 이 코드로 

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    바꿉니다.

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. 다음으로 다음 코드를

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    바꿉니다.

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    이전 코드를 추가하면 로컬 저장소를 초기화하고 Azure 백 엔드에서 사용되는 열 값과 일치하는 로컬 테이블을 정의합니다. (이 코드에 모든 열 값을 포함할 필요가 없습니다.)  `version` 필드는 모바일 백 엔드에 의해 유지 관리되며 충돌 해결에 사용됩니다.

    **getSyncContext**를 호출하여 동기화 컨텍스트에 대한 참조를 가져옵니다. 동기화 컨텍스트를 사용하면 모든 테이블의 변경 내용을 추적하고 밀어넣어서 테이블 관계를 보존할 수 있습니다. `.push()`를 호출하는 경우 클라이언트 앱을 수정합니다.

3. 애플리케이션 URL을 모바일 앱 애플리케이션 URL로 업데이트합니다.

4. 다음으로 이 코드를

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    바꿉니다.

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    이전 코드에서 동기화 컨텍스트를 초기화하고 getSyncTable(getTable 대신)을 호출하여 로컬 테이블에 대한 참조를 가져옵니다.

    이 코드는 모든 만들기, 읽기, 업데이트 및 삭제(CRUD) 테이블 작업에 대해 로컬 저장소 데이터베이스를 사용합니다.

    이 샘플에서는 동기화 충돌의 간단한 오류를 처리합니다. 실제 애플리케이션에서는 네트워크 상태, 서버 충돌 및 기타와 같은 다양한 오류를 처리합니다. 코드 예제는 [오프라인 동기화 샘플]을 참조하세요.

5. 다음으로, 이 함수를 추가하여 실제 동기화를 수행합니다.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    **syncContext.push()** 를 호출하여 모바일 앱 백 엔드에 대한 변경 내용을 푸시할 시점을 결정합니다. 예를 들어 동기화 단추에 연결된 단추 이벤트 처리기에서 **syncBackend**를 호출할 수 있습니다.

## <a name="offline-sync-considerations"></a>오프라인 동기화 고려 사항

이 샘플에서 **syncContext**의 **푸시** 메서드는 로그인의 콜백 함수에서 앱 시작 시에만 호출됩니다.  실제 애플리케이션에서는 네트워크 상태가 변경될 때 수동으로 트리거되는 이 동기화 기능을 만들 수도 있습니다.

끌어오기가 컨텍스트에 의해 추적되는 로컬 업데이트를 보류 중인 테이블에 대해 실행되는 경우 끌어오기 작업은 자동으로 푸시를 트리거합니다. 이 샘플에서 항목을 새로 고침, 추가 및 완료하는 경우 명시적인 **push** 호출이 중복될 수 있으므로 생략할 수 있습니다.

제공된 코드에서 원격 todoItem 테이블에 있는 모든 레코드를 쿼리하지만 쿼리 ID 및 쿼리를 **푸시**로 전달하여 레코드를 필터링할 수도 있습니다. 자세한 내용은 *Azure Mobile Apps에서 오프라인 데이터 동기화* 에서 [Azure Mobile Apps에서 오프라인 데이터 동기화]섹션을 참조하세요.

## <a name="optional-disable-authentication"></a>(선택 사항)인증 사용 안 함

오프라인 동기화를 테스트하기 전에 인증을 설정하지 않으려는 경우 로그인의 호출 함수를 주석 처리하지만 호출 함수 내에 있는 코드의 주석 처리를 제거합니다.  로그인 줄을 주석 처리한 후 코드는 다음과 같습니다.

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

이제 앱을 실행하면 Azure 백 엔드와 동기화합니다.

## <a name="run-the-client-app"></a>클라이언트 앱을 실행합니다.
오프라인 동기화를 사용하도록 설정하면 각 플랫폼에서 클라이언트 애플리케이션을 한 번 이상 실행하여 로컬 저장소 데이터베이스를 채울 수 있습니다. 나중에 앱이 오프라인인 동안 오프라인 시나리오를 시뮬레이션하고 로컬 저장소에 있는 데이터를 수정합니다.

## <a name="optional-test-the-sync-behavior"></a>(선택 사항)동기화 동작 테스트
이 섹션에서 백 엔드에 잘못된 애플리케이션 URL을 사용하여 오프라인 시나리오를 시뮬레이트하도록 클라이언트 프로젝트를 수정합니다. 데이터 항목을 추가하거나 변경하는 경우 이러한 변경 내용은 로컬 저장소에 보관되지만 연결이 다시 설정될 때까지 백 엔드 데이터 저장소에 동기화되지 않습니다.

1. 솔루션 탐색기에서 index.js 프로젝트 파일을 열고 다음 코드와 같은 잘못된 URL을 가리키는 애플리케이션 URL을 변경합니다.

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. index.html에서 동일하게 잘못된 URL을 포함하는 CSP `<meta>` 요소를 업데이트합니다.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. 클라이언트 앱을 빌드 및 실행하면 앱이 로그인한 후에 백 엔드와 동기화하려는 경우 예외가 콘솔에 로그인됩니다. 추가한 새 항목은 모바일 백 엔드에 푸시할 때까지 로컬 저장소에만 있습니다. 클라이언트 앱은 백 엔드에 연결된 것처럼 동작합니다.

4. 앱을 닫았다가 다시 시작하여 만든 새 항목이 로컬 저장소에 유지되는지 확인합니다.

5. (선택 사항) Azure SQL Database 테이블을 보는 Visual Studio를 사용하여 백 엔드 데이터베이스에서 데이터가 변경되지 않았음을 확인합니다.

    Visual Studio에서 **서버 탐색기**를 엽니다. **Azure**->**SQL Databases**에 있는 데이터베이스로 이동합니다. 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **SQL Server 개체 탐색기에서 열기**를 선택합니다. 이제 SQL 데이터베이스 테이블 및 콘텐츠를 찾아볼 수 있습니다.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(선택 사항)모바일 백 엔드에 대한 다시 연결 테스트

이 섹션에서는 앱을 모바일 백 엔드에 다시 연결하여 다시 온라인 상태로 전환되는 앱을 시뮬레이트합니다. 로그인할 때 데이터가 모바일 백 엔드에 동기화됩니다.

1. index.js를 다시 열고 애플리케이션 URL을 복원합니다.
2. index.html을 다시 열고 CSP `<meta>` 요소에서 애플리케이션 URL을 수정합니다.
3. 클라이언트 앱을 다시 빌드하고 실행합니다. 로그인한 후에 앱이 모바일 앱 백 엔드와 동기화하려고 합니다. 디버그 콘솔에 기록된 예외가 없는지 확인합니다.
4. (선택 사항) SQL Server 개체 탐색기 또는 Fiddler와 같은 REST 도구를 사용하여 업데이트된 데이터를 봅니다. 백 엔드 데이터베이스와 로컬 저장소의 데이터가 동기화된 것을 확인합니다.

    데이터베이스와 로컬 저장소 간에 데이터가 동기화되었으며 앱의 연결이 끊어진 동안 추가한 항목을 포함합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Mobile Apps에서 오프라인 데이터 동기화]
* [Visual Studio Tools for Apache Cordova]

## <a name="next-steps"></a>다음 단계
* [오프라인 동기화 샘플]에서 충돌 해결과 같은 고급 오프라인 동기화 기능 검토
* [API 설명서](https://azure.github.io/azure-mobile-apps-js-client)에서 오프라인 동기화 API 참조를 검토합니다.

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Apache Cordova 빠른 시작]: app-service-mobile-cordova-get-started.md
[오프라인 동기화 샘플]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Azure Mobile Apps에서 오프라인 데이터 동기화]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
