---
title: '자습서: 이벤트 데이터를 SQL Data Warehouse로 마이그레이션 - Azure Event Hubs'
description: '자습서: 이 자습서에서는 이벤트 그리드에서 트리거된 Azure 함수를 사용하여 데이터를 이벤트 허브에서 SQL 데이터 웨어하우스로 캡처하는 방법을 보여 줍니다.'
services: event-hubs
ms.date: 06/23/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b6b6466675c8fa258af8370798cadd88e3b25a83
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997832"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>자습서: Event Grid 및 Azure Functions를 사용하여 캡처된 Event Hubs 데이터를 SQL Data Warehouse로 마이그레이션

Event Hubs [캡처](./event-hubs-capture-overview.md)는 Event Hubs에 스트림된 데이터를 Azure Blob Storage 또는 Azure Data Lake 스토리지에 자동으로 전달하는 가장 쉬운 방법입니다. 이후에 데이터를 SQL Data Warehouse 또는 Cosmos DB와 같이 원하는 다른 스토리지 대상으로 처리하고 전달할 수 있습니다. 이 자습서에서는 [이벤트 그리드](../event-grid/overview.md)에서 트리거된 Azure 함수를 사용하여 데이터를 이벤트 허브에서 SQL 데이터 웨어하우스로 캡처하는 방법을 알아봅니다.

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

- 먼저, **캡처** 기능이 사용하도록 설정된 이벤트 허브를 만들고 Azure Blob Storage를 대상으로 설정합니다. WindTurbineGenerator에서 생성된 데이터는 이벤트 허브로 스트림되며 Azure Storage에 Avro 파일로 자동으로 캡처됩니다.
- 다음으로, Event Hubs 네임스페이스를 원본으로, Azure Function 엔드포인트를 대상으로 사용하여 Azure Event Grid 구독을 만듭니다.
- Event Hubs 캡처 기능을 통해 새 Avro 파일이 Azure Storage Blob에 전달될 때마다 Event Grid에서 Blob URI를 사용하여 Azure Function에 알립니다. 그런 다음, Function은 Blob의 데이터를 SQL 데이터 웨어하우스로 마이그레이션합니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> - 인프라 배포
> - Functions 앱에 코드 게시
> - Functions 앱에서 Event Grid 구독 만들기
> - Event Hub를 통해 샘플 데이터 스트림
> - SQL Data Warehouse에서 캡처된 데이터 확인

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual Studio 2019](https://www.visualstudio.com/vs/). 설치하는 동안 .NET 데스크톱 개발, Azure 개발, ASP.NET 및 웹 개발, Node.js 개발, Python 개발 워크로드를 설치해야 합니다.
- [Git 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) 다운로드 - 샘플 솔루션에 포함된 구성 요소는 다음과 같습니다.

  - *WindTurbineDataGenerator* – 샘플 풍력 터빈 데이터를 캡처 지원 이벤트 허브로 보내는 간단한 게시자입니다.
  - *FunctionDWDumper* – Avro 파일이 Azure Storage Blob에 캡처될 때 Event Grid 알림을 받는 Azure Function입니다. Blob의 URI 경로를 받고, 해당 콘텐츠를 읽고, 이 데이터를 SQL Data Warehouse로 푸시합니다.

  이 샘플은 최신 Azure.Messaging.EventHubs 패키지를 사용합니다. [여기에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) Microsoft.Azure.EventHubs 패키지를 사용하는 이전 샘플을 찾을 수 있습니다.

### <a name="deploy-the-infrastructure"></a>인프라 배포

Azure PowerShell 또는 Azure CLI를 사용하여 이 [Azure Resource Manager 템플릿](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json)을 통해 이 자습서에 필요한 인프라를 배포합니다. 이 템플릿은 다음과 같은 리소스를 만듭니다.

- 캡처 기능을 사용하도록 설정된 Event Hub
- 캡처된 이벤트 데이터에 대한 스토리지 계정
- Functions 앱 호스팅에 대한 Azure 앱 서비스 계획
- 캡처한 이벤트 파일을 처리하는 함수 앱
- Data Warehouse 호스팅용 논리 SQL 서버
- 마이그레이션된 데이터 저장용 SQL Data Warehouse

다음 섹션에서는 이 자습서에 필요한 인프라를 배포하는 Azure CLI 및 Azure PowerShell 명령을 제공합니다. 명령을 실행하기 전에 다음 개체의 이름을 업데이트합니다. 

- Azure 리소스 그룹 
- 리소스 그룹에 대한 지역
- Event Hubs 네임스페이스
- 이벤트 허브
- 논리 SQL 서버
- SQL 사용자(및 암호)
- Azure SQL 데이터베이스
- Azure Storage 
- Azure Functions 앱

이러한 스크립트에서 모든 Azure 아티팩트를 만드는 데 약간의 시간이 걸립니다. 계속 진행하기 전에 스크립트가 완료될 때까지 기다립니다. 어떤 이유로 배포가 실패하면 리소스 그룹을 삭제하고, 보고된 문제를 해결한 다음, 명령을 다시 실행합니다. 

#### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 템플릿을 배포하려면 다음 명령을 사용합니다.

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
PowerShell을 사용하여 템플릿을 배포하려면 다음 명령을 사용합니다.

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

### <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse에서 테이블 만들기

[Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md) 또는 포털의 [쿼리 편집기]에서 [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 스크립트를 실행하여 SQL 데이터 웨어하우스에 테이블을 만듭니다. 

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

## <a name="publish-code-to-the-functions-app"></a>Functions 앱에 코드 게시

1. Visual Studio 2019에서 *EventHubsCaptureEventGridDemo.sln* 솔루션을 엽니다.

1. 솔루션 탐색기에서 *FunctionEGDWDumper*를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

   ![함수 앱 게시](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. **Azure 함수 앱**과 **기존 항목 선택**을 차례로 선택합니다. **게시**를 선택합니다.

   ![대상 함수 앱](./media/store-captured-data-data-warehouse/pick-target.png)

1. 템플릿을 통해 배포한 함수 앱을 선택합니다. **확인**을 선택합니다.

   ![함수 앱 선택](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Visual Studio에서 프로필이 구성되었으면 **게시**를 선택합니다.

   ![게시 선택](./media/store-captured-data-data-warehouse/select-publish.png)

함수가 게시되면 Event Hubs에서 캡처 이벤트를 구독할 준비가 되었습니다!


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Functions 앱에서 Event Grid 구독 만들기
 
1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 리소스 그룹 및 함수 앱을 선택합니다.

   ![함수 앱 보기](./media/store-captured-data-data-warehouse/view-function-app.png)

1. 함수를 선택합니다.

   ![함수 선택](./media/store-captured-data-data-warehouse/select-function.png)

1. **Event Grid 구독 추가**를 선택합니다.

   ![구독 추가](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Event Grid 구독의 이름을 지정합니다. 이벤트 형식으로 **Event Hubs 네임스페이스**를 사용합니다. 값을 입력하여 Event Hubs 네임스페이스의 인스턴스를 선택합니다. 구독자 엔드포인트에서는 입력된 값을 그대로 둡니다. **만들기**를 선택합니다.

   ![구독 만들기](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>샘플 데이터 생성  
이제 Event Hub, SQL 데이터 웨어하우스, Azure 함수 앱 및 Event Grid 구독이 설정되었습니다. WindTurbineDataGenerator.exe를 실행하여 소스 코드에서 이벤트 허브의 연결 문자열과 이름을 업데이트한 후에 Event Hub로의 데이터 스트림을 생성할 수 있습니다. 

1. Portal에서 이벤트 허브 네임스페이스를 선택합니다. **연결 문자열**을 선택합니다.

   ![연결 문자열 선택](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. **RootManageSharedAccessKey** 선택

   ![키 선택](./media/store-captured-data-data-warehouse/show-root-key.png)

3. **연결 문자열 - 기본 키** 복사

   ![키 복사](./media/store-captured-data-data-warehouse/copy-key.png)

4. Visual Studio 프로젝트로 돌아옵니다. *WindTurbineDataGenerator*프로젝트에서 *program.cs*를 엽니다.

5. **EventHubConnectionString** 및 **EventHubName**에 대한 값을 이벤트 허브의 연결 문자열과 이름으로 업데이트합니다. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 솔루션을 빌드한 다음, WindTurbineGenerator.exe 애플리케이션을 실행합니다. 

## <a name="verify-captured-data-in-data-warehouse"></a>데이터 웨어하우스에서 캡처된 데이터 확인
몇 분 후에 SQL 데이터 웨어하우스의 테이블을 쿼리합니다. WindTurbineDataGenerator에서 생성된 데이터가 Event Hub로 스트림되고, Azure Storage 컨테이너에 캡처된 다음, Azure Function에서 SQL Data Warehouse 테이블로 마이그레이션되었음을 알 수 있습니다.  

## <a name="next-steps"></a>다음 단계 
데이터 웨어하우스에서 강력한 데이터 시각화 도구를 사용하여 실행 가능한 인사이트를 얻을 수 있습니다.

[SQL Data Warehouse에서 Power BI를 사용하는 방법](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) 문서를 참조하세요.