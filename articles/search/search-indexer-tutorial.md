---
title: Azure SQL 데이터를 인덱싱하는 C# 자습서
titleSuffix: Azure Cognitive Search
description: 이 C# 자습서에서는 Azure SQL Database에 연결하고, 검색 가능한 데이터를 추출하고, Azure Cognitive Search 인덱스에 로드합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8647e28701316ecd7305e206918c53281deb6b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004258"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>자습서: .NET SDK를 사용하여 Azure SQL 데이터 인덱싱

Azure SQL Database에서 검색 가능한 데이터를 추출하여 Azure Cognitive Search의 검색 인덱스로 보내는 [인덱서](search-indexer-overview.md)를 구성합니다. 

이 자습서에서는 C# 및 [.NET SDK](/dotnet/api/overview/azure/search)를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure SQL Database에 연결하는 데이터 원본 만들기
> * 인덱서 만들기
> * 인덱서를 실행하여 인덱스에 데이터 로드
> * 확인 단계로 인덱스 쿼리

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 체험 검색 서비스에서는 인덱스, 인덱서 및 데이터 원본이 각각 3개로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 시작하기 전에 새 리소스를 수용할 수 있는 공간이 서비스에 있는지 확인하세요.

## <a name="download-files"></a>파일 다운로드

이 자습서의 소스 코드는 [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) GitHub 리포지토리의 [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) 폴더에 있습니다.

## <a name="1---create-services"></a>1 - 서비스 만들기

이 자습서에서는 Azure Cognitive Search를 인덱싱 및 쿼리에 사용하고 Azure SQL Database를 외부 데이터 원본으로 사용합니다. 가능한 경우 근접성과 관리 효율성을 위해 동일한 지역과 리소스 그룹에 두 서비스를 만듭니다. 실제로 Azure SQL Database는 어느 지역에나 있을 수 있습니다.

### <a name="start-with-azure-sql-database"></a>Azure SQL Database 시작

이 단계에서는 인덱서에서 크롤링할 수 있는 외부 데이터 원본을 Azure SQL Database에 만듭니다. Azure Portal 및 샘플 다운로드의 *hotels.sql* 파일을 사용하여 Azure SQL Database에서 데이터 세트를 만들 수 있습니다. Azure Cognitive Search는 뷰 또는 쿼리에서 생성된 일반 행 집합을 사용합니다. 샘플 솔루션에 있는 SQL 파일은 단일 테이블을 만들고 채웁니다.

기존 Azure SQL Database 리소스가 있는 경우 4단계부터 시작하여 hotels 테이블을 추가할 수 있습니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)합니다.

1. **SQL Database**를 찾거나 만듭니다. 기본값 및 가장 낮은 수준의 가격 책정 계층을 사용할 수 있습니다. 서버를 만드는 이점은 이후 단계에서 테이블을 만들고 로드하는 데 필요한 관리자 사용자 이름 및 암호를 지정할 수 있다는 것입니다.

   ![새 데이터베이스 페이지](./media/search-indexer-tutorial/indexer-new-sqldb.png "새 데이터베이스 페이지")

1. **검토 + 만들기**를 클릭하여 새 서버와 데이터베이스를 배포합니다. 서버 및 데이터베이스를 배포하도록 대기합니다.

1. 탐색 창에서 **쿼리 편집기(미리 보기)** 를 클릭하여 서버 관리자의 사용자 이름과 암호를 입력합니다. 

   액세스가 거부되면 오류 메시지에서 클라이언트 IP 주소를 복사한 다음, **서버 방화벽 설정** 링크를 클릭하여 클라이언트 IP를 통해 클라이언트 컴퓨터에서 액세스할 수 있도록 허용하는 규칙을 추가합니다. 규칙을 적용하는 데 몇 분 정도 걸릴 수 있습니다.

1. 쿼리 편집기에서 **쿼리 열기**를 클릭하고, 로컬 컴퓨터에서 *hotels.sql* 파일의 위치로 이동합니다. 

1. 파일을 선택하고 **열기**를 클릭합니다. 이 스크립트는 다음 스크린샷과 비슷하게 표시됩니다.

   ![SQL 스크립트](./media/search-indexer-tutorial/sql-script.png "SQL 스크립트")

1. **실행**을 클릭하여 쿼리를 실행합니다. 결과 창에서 3행에 대한 쿼리 성공 메시지가 표시됩니다.

1. 이 테이블에서 행 집합을 반환하려면 확인 단계로 다음 쿼리를 실행할 수 있습니다.

    ```sql
    SELECT * FROM Hotels
    ```

1. 데이터베이스에 대한 ADO.NET 연결 문자열을 복사합니다. **설정** > **연결 문자열** 아래에서 아래 예제와 비슷한 ADO.NET 연결 문자열을 복사합니다.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

이 연결 문자열은 다음 연습에서 환경을 설정하는 데 필요합니다.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

다음 구성 요소는 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search입니다. 이 연습은 체험 계층을 사용하여 완료할 수 있습니다. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL을 가져옵니다.

API 호출에는 서비스 URL과 액세스 키가 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   ![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

## <a name="2---set-up-your-environment"></a>2 - 환경 설정

1. Visual Studio를 시작하고 **DotNetHowToIndexers.sln**을 엽니다.

1. 솔루션 탐색기에서 **appsettings.json**을 열고 연결 정보를 제공합니다.

1. `searchServiceName`의 경우 전체 URL이 "https://my-demo-service.search.windows.net"이면 제공할 서비스 이름은 "my-demo-service"입니다.

1. `AzureSqlConnectionString`의 경우, 문자열 형식은 다음과 유사합니다. `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. 연결 문자열에서 연결 문자열에 유효한 암호가 포함되어 있는지 확인합니다. 데이터베이스와 사용자 이름은 복사되지만 암호는 수동으로 입력해야 합니다.

## <a name="3---create-the-pipeline"></a>3 - 파이프라인 만들기

인덱서에는 데이터 원본 개체와 인덱스가 필요합니다. 관련 코드는 다음 두 파일에 있습니다.

  + **hotel.cs** - 인덱스를 정의하는 스키마가 포함되어 있습니다.
  + **Program.cs** - 서비스에서 구조를 만들고 관리하는 함수가 포함되어 있습니다.

### <a name="in-hotelcs"></a>hotel.cs에서

인덱스 스키마는 다음과 같은 HotelName의 필드 정의에 표시된 대로 필드가 전체 텍스트 검색 가능, 필터링 가능, 정렬 가능한지 등 허용되는 작업을 지정하는 속성을 비롯한 필드 컬렉션을 정의합니다. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

스키마는 점수 매기기, 사용자 지정 분석기 및 다른 구문을 향상시키기 위해 점수 매기기 프로필을 비롯한 다른 요소를 포함할 수도 있습니다. 그러나 목적상 스키마는 밀도가 낮게 정의되어 샘플 데이터 세트에서 발견되는 필드를 구성합니다.

### <a name="in-programcs"></a>Program.cs에서

기본 프로그램에는 클라이언트, 인덱스, 데이터 원본 및 인덱서를 만드는 논리가 포함되어 있습니다. 코드는 이 프로그램을 여러 번 실행한다는 가정 하에서 동일한 이름의 기존 리소스를 확인하고 삭제합니다.

데이터 원본 개체는 Azure SQL의 기본 제공 [변경 내용 검색 기능](/sql/relational-databases/track-changes/about-change-tracking-sql-server)을 활용하기 위한 [부분 또는 증분 인덱싱](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)을 포함하여 Azure SQL Database 리소스에 한정된 설정을 사용하여 구성됩니다. Azure SQL의 데모 호텔 데이터베이스에는 **IsDeleted**라는 "일시 삭제" 열이 있습니다. 데이터베이스에서 이 열을 true로 설정하면 인덱서가 Azure Cognitive Search 인덱스에서 해당 문서를 제거합니다.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

인덱서 개체는 플랫폼의 제약을 받지 않으므로 원본에 관계없이 구성, 일정 예약 및 호출이 동일합니다. 이 예제 인덱서는 인덱서 기록을 지우는 초기화 옵션인 일정을 포함하고 있으며, 즉시 인덱서를 만들고 실행하는 메서드를 호출합니다.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="4---build-the-solution"></a>4 - 솔루션 빌드

F5 키를 눌러 솔루션을 빌드하고 실행합니다. 디버그 모드로 프로그램을 실행합니다. 콘솔 창에서는 각 작업의 상태를 보고합니다.

   ![콘솔 출력](./media/search-indexer-tutorial/console-output.png "콘솔 출력")

코드는 Visual Studio에서 로컬로 실행되어 Azure의 검색 서비스에 연결됩니다. 그러면 Azure SQL Database에 연결되어 데이터 세트를 검색합니다. 이러한 많은 작업을 수행하는 경우 몇 가지 잠재적인 실패 지점이 있습니다. 오류가 발생하면 먼저 다음 조건을 확인하세요.

+ 제공한 검색 서비스 연결 정보가 이 자습서에서 해당 서비스 이름으로 제한됩니다. 전체 URL을 입력한 경우 인덱스 생성 시 연결 오류와 함께 작업이 중지됩니다.

+ **appsettings.json**에 있는 데이터베이스 연결 정보입니다. 해당 항목은 포털에서 가져온 ADO.NET 연결 문자열이며 데이터베이스에 유효한 사용자 이름 및 암호를 포함하도록 수정되어야 합니다. 사용자 계정에는 데이터를 검색할 수 있는 권한이 있어야 합니다. 로컬 클라이언트 IP 주소에 액세스할 수 있어야 합니다.

+ 리소스 제한 무료 계층에는 3개의 인덱스, 인덱서 및 데이터 원본이 제한되어 있습니다. 최대 제한 시 서비스는 새 개체를 만들 수 없습니다.

## <a name="5---search"></a>5 - 검색

Azure Portal을 사용하여 개체 만들기를 확인한 다음, **Search 탐색기**를 사용하여 인덱스를 쿼리합니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 각 목록을 차례로 열어 개체가 만들어졌는지 확인합니다. **인덱스**, **인덱서** 및 **데이터 원본**에는 각각 "hotels", "azure-sql-indexer" 및 "azure-sql"이 있습니다.

   ![인덱서 및 데이터 원본 타일](./media/search-indexer-tutorial/tiles-portal.png)

1. hotels 인덱스를 선택합니다. hotels 페이지에서 첫 번째 탭은 **Search 탐색기**입니다. 

1. **검색**을 클릭하여 빈 쿼리를 실행합니다. 

   인덱스에 있는 세 개의 항목이 JSON 문서로 반환됩니다. 전체 구조를 볼 수 있도록 검색 탐색기는 JSON으로 문서를 반환합니다.

   ![인덱스 쿼리](./media/search-indexer-tutorial/portal-search.png "인덱스 쿼리")
   
1. 다음으로 검색 문자열을 입력합니다.`search=river&$count=true` 

   이 쿼리는 `river` 용어의 전체 텍스트 검색을 호출하고 결과에는 일치하는 문서 수가 포함됩니다. 일치하는 문서 수를 반환하는 작업은 수많은 문서를 포함하는 대규모 인덱스가 있을 때 시나리오를 테스트하는 경우 유용합니다. 이 경우에 하나의 문서만이 쿼리와 일치합니다.

1. 마지막으로 JSON 출력을 관심 분야로 제한하는 검색 문자열을 입력합니다.`search=river&$count=true&$select=hotelId, baseRate, description` 

   쿼리 응답이 선택한 필드로 줄어들어 더 간결한 출력이 표시됩니다.

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

이 자습서의 샘플 코드는 기존 개체를 확인하고 삭제하여 코드를 다시 실행할 수 있도록 합니다.

또한 포털을 사용하여 인덱스, 인덱서 및 데이터 원본을 삭제할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 [모든 리소스] 또는 [리소스 그룹] 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

SQL Database 인덱싱의 기본 사항을 익혔으면 인덱서 구성에 대해 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [SQL Database 인덱서 구성](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)