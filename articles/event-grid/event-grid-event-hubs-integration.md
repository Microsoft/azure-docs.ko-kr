---
title: '자습서: 데이터 웨어하우스로 Event Hubs 데이터 보내기 - Event Grid'
description: '자습서: Azure Event Grid 및 Event Hubs를 사용하여 SQL Data Warehouse로 데이터를 마이그레이션하는 방법을 설명합니다. 캡처 파일을 검색하는 Azure 함수를 사용합니다.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c4a1943981fc3e9f1df0fafff540e24ee3631e9
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007454"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>자습서: 데이터 웨어하우스로 빅 데이터 스트림
Azure [Event Grid](overview.md)는 앱과 서비스의 알림(이벤트)에 응답하는 데 사용할 수 있는 인텔리전트 이벤트 라우팅 서비스입니다. 예를 들어 Azure Blob Storage 또는 Azure Data Lake Store로 캡처된 Event Hubs 데이터를 처리하도록 Azure Function을 트리거하고, 다른 데이터 리포지토리에 데이터를 마이그레이션할 수 있습니다. 이 [Event Hubs 및 Event Grid 통합 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)은 Event Grid와 함께 Event Hubs를 사용하여 캡처된 Event Hubs 데이터를 BLOB 스토리지에서 SQL Data Warehouse로 원활하게 마이그레이션하는 방법을 보여줍니다.

![애플리케이션 개요](media/event-grid-event-hubs-integration/overview.png)

이 다이어그램은 이 자습서에서 빌드하는 솔루션의 워크플로를 보여줍니다. 

1. Azure 이벤트 허브로 전송된 데이터는 Azure BLOB 스토리지에 캡처됩니다.
2. 데이터 캡처가 완료되면 이벤트가 생성되어 Azure Event Grid로 전송됩니다. 
3. Event Grid는 이 이벤트 데이터를 Azure 함수 앱에 전달합니다.
4. 함수 앱은 이벤트 데이터의 BLOB URL을 사용하여 스토리지에서 BLOB을 검색합니다. 
5. 함수 앱은 Azure SQL Data Warehouse로 BLOB 데이터를 마이그레이션합니다. 

이 문서에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * Azure Resource Manager 템플릿을 사용하여 이벤트 허브, 스토리지 계정, 함수 앱, SQL 데이터 웨어하우스 등의 인프라를 배포합니다.
> * 데이터 웨어하우스에 테이블을 만듭니다.
> * 함수 앱에 코드를 추가합니다.
> * 이벤트를 구독합니다. 
> * 이벤트 허브로 데이터를 보내는 앱을 실행합니다.
> * 데이터 웨어하우스에서 마이그레이션된 데이터를 봅니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 완료하려면 다음 항목이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* .NET 데스크톱 개발, Azure 개발, ASP.NET 및 웹 개발, Node.js 개발, Python 개발용 작업이 포함된 [Visual Studio 2019](https://www.visualstudio.com/vs/)
* 컴퓨터에 [EventHubsCaptureEventGridDemo 샘플 프로젝트](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)를 다운로드합니다.

## <a name="deploy-the-infrastructure"></a>인프라 배포
이 단계에서는 [Resource Manager 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)을 사용하여 필요한 인프라를 배포합니다. 템플릿을 배포하면 다음 리소스가 생성됩니다.

* 캡처 기능을 사용하도록 설정된 이벤트 허브
* 캡처된 파일에 대한 스토리지 계정 
* 함수 앱 호스팅용 앱 서비스 플랜
* 이벤트 처리용 함수 앱
* 데이터 웨어하우스 호스팅용 SQL Server
* 마이그레이션된 데이터 저장용 SQL Data Warehouse

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Azure Portal에서 Azure Cloud Shell 시작

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 맨 위에서 **Cloud Shell** 단추를 선택합니다.

    ![Azure portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. 브라우저 맨 아래에 Cloud Shell이 열려 있는 것이 보입니다.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Cloud Shell에서 **Bash**와 **PowerShell** 중에 선택하는 옵션이 표시되면 **Bash**를 선택합니다. 
5. Cloud Shell을 처음으로 사용하는 경우 **스토리지 만들기**를 선택하여 스토리지 계정을 만듭니다. Azure Cloud Shell은 Azure 스토리지 계정에서 일부 파일을 저장해야 합니다. 

    ![Cloud Shell용 스토리지 만들기](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Cloud Shell이 초기화될 때까지 기다립니다. 

    ![Cloud Shell용 스토리지 만들기](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Azure CLI 사용

1. 다음 CLI 명령을 실행하여 Azure 리소스 그룹을 만듭니다. 
    1. 다음 명령을 복사하여 Cloud Shell 창에 붙여넣습니다. 원하는 경우 리소스 그룹 이름 및 위치를 변경합니다.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. **Enter**키를 누릅니다. 

        다음은 예제입니다.
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. 다음 CLI 명령을 실행하여 이전 섹션에서 언급한 모든 리소스(이벤트 허브, 스토리지 계정, 함수 앱, SQL 데이터 웨어하우스)를 배포합니다. 
    1. 명령을 복사하여 Cloud Shell 창에 붙여넣습니다. 또는 원하는 편집기에 복사/붙여넣고, 값을 설정하고, Cloud Shell에 명령을 복사합니다. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. 다음 엔터티의 값을 지정합니다.
        1. 앞에서 만든 리소스 그룹 이름
        2. 이벤트 허브 네임스페이스 이름 
        3. 이벤트 허브 이름. 현재 값(hubdatamigration)을 그대로 유지해도 됩니다.
        4. SQL 서버 이름
        5. SQL 사용자 이름 및 암호 
        6. SQL 데이터 웨어하우스 이름
        7. 스토리지 계정 이름 
        8. 함수 앱 이름 
    3.  Cloud Shell 창에서 **ENTER**를 눌러 명령을 실행합니다. 여러 리소스를 만들기 때문에 이 프로세스에 다소 시간이 걸릴 수 있습니다. 명령 결과에 오류가 없는지 확인합니다. 
    

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

1. Azure Cloud Shell에서 PowerShell 모드로 전환합니다. Azure Cloud Shell의 왼쪽 위 모서리에서 아래쪽 화살표를 선택하고, **PowerShell**을 선택합니다.

    ![PowerShell로 전환](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. 다음 명령을 실행하여 Azure 리소스 그룹을 만듭니다. 
    1. 다음 명령을 복사하여 Cloud Shell 창에 붙여넣습니다.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. **리소스 그룹**의 이름을 지정합니다.
    3. Enter 키를 누릅니다. 
3. 다음 명령을 실행하여 이전 섹션에서 언급한 모든 리소스(이벤트 허브, 스토리지 계정, 함수 앱, SQL 데이터 웨어하우스)를 배포합니다.
    1. 명령을 복사하여 Cloud Shell 창에 붙여넣습니다. 또는 원하는 편집기에 복사/붙여넣고, 값을 설정하고, Cloud Shell에 명령을 복사합니다. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. 다음 엔터티의 값을 지정합니다.
        1. 앞에서 만든 리소스 그룹 이름
        2. 이벤트 허브 네임스페이스 이름 
        3. 이벤트 허브 이름. 현재 값(hubdatamigration)을 그대로 유지해도 됩니다.
        4. SQL 서버 이름
        5. SQL 사용자 이름 및 암호 
        6. SQL 데이터 웨어하우스 이름
        7. 스토리지 계정 이름 
        8. 함수 앱 이름 
    3.  Cloud Shell 창에서 **ENTER**를 눌러 명령을 실행합니다. 여러 리소스를 만들기 때문에 이 프로세스에 다소 시간이 걸릴 수 있습니다. 명령 결과에 오류가 없는지 확인합니다. 

### <a name="close-the-cloud-shell"></a>Cloud Shell 닫기 
포털에서 **Cloud Shell** 단추를 선택하거나 Cloud Shell 창의 오른쪽 위 모서리에서 **X** 단추를 선택하여 Cloud Shell을 닫습니다. 

### <a name="verify-that-the-resources-are-created"></a>리소스가 만들어지는지 확인합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다. 
2. 검색 상자에 리소스 그룹의 이름을 입력하여 리소스 그룹 목록을 필터링합니다. 
3. 목록에서 해당 리소스 그룹을 선택합니다.

    ![리소스 그룹 선택](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 리소스 그룹에 다음 리소스가 표시되는지 확인합니다.

    ![리소스 그룹의 리소스](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse에서 테이블 만들기
[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 스크립트를 실행하여 데이터 웨어하우스에 테이블을 만듭니다. 스크립트를 실행하려면 Visual Studio 또는 포털의 쿼리 편집기를 사용합니다. 다음 단계는 쿼리 편집기 사용 방법을 보여줍니다. 

1. 리소스 그룹의 리소스 목록에서 **시냅스 SQL 풀(데이터 웨어하우스)** 를 선택합니다. 
2. SQL 데이터 웨어하우스 페이지의 왼쪽 메뉴에서 **쿼리 편집기(미리 보기)** 를 선택합니다. 

    ![SQL 데이터 웨어하우스 페이지](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. SQL 서버의 **사용자** 이름 및 **암호**를 입력하고, **확인**을 선택합니다. SQL 서버에 성공적으로 로그인하려면 방화벽에 클라이언트 IP 주소를 추가해야 할 수도 있습니다. 

    ![SQL 서버 인증](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. 쿼리 창에서 다음 SQL 스크립트를 복사하여 실행합니다. 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    ![SQL 쿼리 실행](media/event-grid-event-hubs-integration/run-sql-query.png)
5. 자습서가 끝나면 데이터가 생성되었는지는 확인할 수 있도록 이 탭 또는 창을 계속 열어 둡니다. 

### <a name="update-the-function-runtime-version"></a>함수 런타임 버전 업데이트

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. 함수 앱이 있는 리소스 그룹을 선택합니다. 
3. 리소스 그룹의 리소스 목록에서 **App Service** 형식의 함수 앱을 선택합니다.
4. 왼쪽 메뉴의 **설정**에서 **구성**을 선택합니다. 
5. 오른쪽 창에서 **함수 런타임 설정** 탭으로 전환합니다. 
5. **런타임 버전**을 **~3**으로 업데이트합니다. 

    ![함수 런타임 버전 업데이트](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Azure Functions 앱 게시

1. Visual Studio를 시작합니다.
2. [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)에서 필수 구성 요소의 일부로 다운로드한 **EventHubsCaptureEventGridDemo.sln** 솔루션을 엽니다.
3. 솔루션 탐색기에서 **FunctionEGDWDumper**를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

   ![함수 앱 게시](media/event-grid-event-hubs-integration/publish-function-app.png)
4. 다음 화면이 보이면 **시작**을 선택합니다. 

   ![게시 시작 단추](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. **게시** 대화 상자에서 **대상**에 대해 **Azure**를 선택하고 **다음**을 선택합니다. 

   ![게시 시작 단추](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. **Azure 함수 앱(Windows)** 를 선택하고 **다음**을 선택합니다. 

   ![Azure 함수 앱 - Windows 선택](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. **함수 인스턴스** 탭에서 Azure 구독을 선택하고 리소스 그룹을 확장한 다음 함수 앱을 선택하고 **마침**을 선택합니다. 아직 수행하지 않은 경우 Azure 계정에 로그인을 합니다. 

   ![함수 앱 선택](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. **서비스 종속성** 섹션에서 **구성**을 선택합니다.
9. **종속성 구성** 페이지에서 이전에 만든 스토리지 계정을 선택한 후 **다음**을 선택합니다. 
10. 연결 문자열 이름 및 값에 대한 설정을 유지하고 **다음**을 선택합니다.
11. **비밀 저장소** 옵션을 선택 취소하고 **마침**을 선택합니다.  
8. Visual Studio에서 프로필이 구성되었으면 **게시**를 선택합니다.

   ![게시 선택](media/event-grid-event-hubs-integration/select-publish.png)

함수를 게시한 후에는 이벤트를 구독할 수 있습니다.

## <a name="subscribe-to-the-event"></a>이벤트 구독

1. 웹 브라우저의 새 탭 또는 새 창에서 [Azure Portal](https://portal.azure.com)로 이동합니다.
2. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다. 
3. 검색 상자에 리소스 그룹의 이름을 입력하여 리소스 그룹 목록을 필터링합니다. 
4. 목록에서 해당 리소스 그룹을 선택합니다.

    ![리소스 그룹 선택](media/event-grid-event-hubs-integration/select-resource-group.png)
4. 리소스 그룹의 리소스 목록에서(App Service가 아니라) App Service 계획을 선택합니다. 
5. App Service 플랜 페이지의 왼쪽 메뉴에서 **앱**을 선택하고 함수 앱을 선택합니다. 

    ![함수 앱 선택](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. 함수 앱을 확장하고, 함수를 확장하고, 함수를 선택합니다. 
7. 도구 모음에서 **Event Grid 구독 추가**를 선택합니다. 

    ![Azure 함수 선택](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. **Event Grid 구독 만들기** 페이지에서 다음 작업을 수행합니다. 
    1. **이벤트 구독 세부 정보** 페이지에서 구독 이름을 입력하고(예: captureEventSub) **만들기**를 선택합니다. 
    2. **토픽 세부 정보** 섹션에서 다음 작업을 수행합니다.
        1. **항목 형식**에 대한 **Event Hubs 네임 스페이스**를 선택합니다. 
        2. Azure 구독을 선택합니다.
        2. Azure 리소스 그룹을 선택합니다.
        3. Event Hubs 네임스페이스를 선택합니다.
    3. **이벤트 유형** 섹션에서 **이벤트 유형에 대한 필터**에 대해 **캡처 파일 생성**이 선택되어 있는지 확인합니다. 
    4. **엔드포인트 세부 정보** 섹션에서 **엔드포인트 형식**이 **Azure 함수**로 설정되어 있는지 확인하고 **엔드포인트**가 Azure 함수로 설정되어 있는지 확인합니다. 
    
        ![Event Grid 구독 만들기](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>앱을 실행하여 데이터 생성
이벤트 허브, SQL 데이터 웨어하우스, Azure 함수 앱 및 이벤트 구독 설정이 완료되었습니다. 이벤트 허브에 대한 데이터를 생성하는 애플리케이션을 실행하기 전에 몇 가지 값을 구성해야 합니다.

1. Azure Portal에서 이전에 만든 리소스 그룹으로 이동합니다. 
2. Event Hubs 네임스페이스를 선택합니다.
3. **Event Hubs 네임스페이스** 페이지의 왼쪽 메뉴에서 **공유 액세스 정책**을 선택합니다.
4. 정책 목록에서 **RootManageSharedAccessKey**를 선택합니다. 
5. **연결 문자열 기본 키** 텍스트 상자 옆에 있는 복사 단추를 선택합니다. 

    ![이벤트 허브 네임스페이스에 대한 연결 문자열](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Visual Studio 솔루션으로 돌아갑니다. 
2. WindTurbineDataGenerator 프로젝트에서 **program.cs**를 엽니다.
5. 두 상수 값을 바꿉니다. **EventHubConnectionString**에는 복사한 값을 사용하고, Event Hubs 이름으로 **hubdatamigration**을 사용합니다. 이벤트 허브에 다른 이름을 사용한 경우 해당 이름을 지정합니다. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 솔루션을 빌드합니다. **WindTurbineGenerator.exe** 애플리케이션을 실행합니다. 
7. 몇 분 후에 데이터 웨어하우스에서 마이그레이션된 데이터용 테이블을 쿼리합니다.

    ![쿼리 결과](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>이벤트 허브에서 생성한 이벤트 데이터
Event Grid는 구독자에게 이벤트 데이터를 배포합니다. 다음 예제는 이벤트 허브를 통한 데이터 스트리밍을 BLOB에 캡처할 때 생성되는 이벤트 데이터를 보여줍니다. 특히 `data` 개체의 `fileUrl` 속성은 스토리지의 BLOB을 가리킵니다. 함수 앱은 이 URL을 사용하여 캡처된 데이터가 포함된 BLOB 파일을 검색합니다.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>다음 단계

* Azure 메시징 서비스의 차이점을 알아보려면 [메시지를 배달하는 Azure 서비스 중에서 선택](compare-messaging-services.md)을 참조하세요.
* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Hubs 캡처에 대한 소개는 [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](../event-hubs/event-hubs-capture-enable-through-portal.md)을 참조하세요.
* 샘플 설정 및 실행에 대한 자세한 내용은 [Event Hubs 캡처 및 Event Grid 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)을 참조하세요.
