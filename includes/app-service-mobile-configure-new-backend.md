---
title: 포함 파일
description: 포함 파일
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440200"
---
1. 다음 플랫폼에 대한 SDK 퀵 스타트 클라이언트를 다운로드 합니다.
    
    [iOS (Objective-c)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS(Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > 다운로드 해야 하는 iOS 프로젝트를 사용 하는 경우 "azuresdk-ios\*.zip"에서 [최신 GitHub 릴리스](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)합니다. 압축을 풀고 추가 `MicrosoftAzureMobile.framework` 프로젝트의 루트 파일입니다.
    >

2. 데이터베이스 연결을 추가 하거나 기존 연결에 연결 해야 합니다. 먼저, 데이터 저장소를 만들 또는 기존 계정을 사용할 수 있는지 여부를 결정 합니다.

    - **새 데이터 저장소를 만들**: 데이터 저장소를 하려는 경우 다음 빠른 시작을 사용 합니다.

        [빠른 시작: Azure SQL Database에서 단일 데이터베이스를 사용 하 여 시작](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **기존 데이터 원본을**: 기존 데이터베이스 연결을 사용 하려는 경우 아래 지침을 따르세요.

        1. SQL 데이터베이스 연결 문자열 형식 `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  이름 서버를이 데이터베이스에 대한 개요 페이지에서 찾을 수 있습니다 이며 일반적으로 "server_name.database.windows.net"의 형식입니다.
            **{port}** usually 1433.
            **{your_catalogue}**  데이터베이스의 이름입니다.
            **{your_username}**  데이터베이스에 액세스할 사용자 이름입니다.
            **{your_password}**  데이터베이스에 액세스 하려면 암호입니다.

            [SQL 연결 문자열 형식에 자세히 알아보기](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 연결 문자열을 추가 하 **모바일 앱** App Service에서 관리할 수 있습니다 연결 문자열이 응용 프로그램에 대해 사용 하 여는 **구성** 메뉴의 옵션입니다.

            연결 문자열을 추가 합니다.

            1. 클릭 합니다 **응용 프로그램 설정** 탭 합니다.

            2. 클릭할 **새 연결 문자열 [+]** 합니다.

            3. 제공 해야 합니다 **이름을**, **값** 하 고 **형식** 연결 문자열에 대한 합니다.

            4. 형식 **이름을** 으로 `MS_TableConnectionString`

            5. 값은 이전 단계에서 형성 된 연결 문자열 이어야 합니다.

            6. SQL Azure 데이터베이스에 연결 문자열을 추가 하는 경우 선택할 **SQLAzure** 아래에서 **형식**합니다.

3. Azure Mobile Apps에.NET 및 Node.js 백 엔드 Sdk

   - **Node.js 백 엔드**
    
     Node.js 빠른 시작 앱을 사용 하려는 경우 아래 지침을 따르세요.

     1. Azure portal로 이동 **간편한 테이블**,이 화면이 표시 됩니다.
      
        ![간편한 테이블 노드](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. 해야 SQL 연결 문자열에 이미 추가 되는 **구성** 탭 합니다. 다음 확인란 **모든 사이트 콘텐츠를 덮어쓰게 됩니다이 인정** 을 클릭 합니다 **TodoItem 테이블 만들기** 단추.
     
        ![노드 간편한 테이블 구성](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. **간편한 테이블**를 클릭 합니다 **+ 추가** 단추입니다.
    
        ![간편한 테이블 노드 추가 단추](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 만들기는 `TodoItem` 익명 액세스를 사용 하 여 테이블입니다.
      
        ![간편한 테이블 노드 테이블 추가](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET 백 엔드**
    
        .NET 빠른 시작 앱을 사용 하려는 경우 아래 지침을 따르세요.

        1. Azure Mobile Apps.NET 서버 프로젝트를 다운로드 합니다 [azure-mobile-앱-빠른 시작 리포지토리](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)합니다.

        2. Visual Studio에서 로컬로.NET 서버 프로젝트를 빌드하십시오.

        3. Visual Studio에서 솔루션 탐색기를 열고 마우스 오른쪽 단추로 클릭 `ZUMOAPPNAMEService` 프로젝트를 클릭 **게시**에 표시 됩니다는 `Publish to App Service` 창입니다. Mac에서 작업 하는 경우 앱을 배포 하는 다른 방법을 확인해 [여기](https://docs.microsoft.com/azure/app-service/deploy-local-git)합니다.
        
           ![Visual studio 게시](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 선택 **App Service** 대상 게시를 클릭 **기존 항목 선택**를 클릭 합니다 **게시** 창의 맨 아래에 있는 단추입니다.

        5. 먼저 Azure 구독을 사용 하 여 Visual Studio에 로그인 해야 합니다. 선택 합니다 `Subscription`, `Resource Group`를 선택한 다음 앱의 이름입니다. 준비 되 면 **확인**, App Service 백 엔드에 있는.NET 서버 프로젝트를 로컬로 배포는이 합니다. 배포가 완료 되 면 있습니다 리디렉션됩니다 `http://{zumoappname}.azurewebsites.net/` 브라우저에서 합니다.                   