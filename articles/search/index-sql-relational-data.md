---
title: 가져오기 및 인덱싱을 위한 SQL 관계형 데이터 모델링
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 인덱싱 및 전체 텍스트 검색을 위해 관계형 데이터를 모델링 하 고 플랫 결과 집합으로 정규화 하는 방법에 대해 알아봅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924523"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Azure Cognitive Search에서 가져오기 및 인덱싱을 위한 관계형 SQL 데이터를 모델링 하는 방법

Azure Cognitive Search는 플랫 행 집합을 [인덱싱 파이프라인](search-what-is-an-index.md)에 대 한 입력으로 받아들입니다. 원본 데이터가 SQL Server 관계형 데이터베이스의 조인 된 테이블에서 생성 된 경우이 문서에서는 결과 집합을 생성 하는 방법과 Azure Cognitive Search 인덱스에서 부모-자식 관계를 모델링 하는 방법을 설명 합니다.

그림으로, [데모 데이터](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels)를 기반으로 하는 가상 호텔 데이터베이스를 참조 합니다. 데이터베이스가 50 호텔의 호텔 $ 테이블로 구성 되어 있다고 가정 하 고, 전체 750 대화방에 대해 다양 한 형식, 요율 및 편의 시설 대화방을 포함 하는 대화방 $ table로 구성 되어 있다고 가정 합니다. 테이블 간에 일 대 다 관계가 있습니다. 이 접근 방식에서 뷰는 각 행에 관련 된 대화방 세부 정보가 포함 된 50 행, 호텔 당 한 개의 행을 반환 하는 쿼리를 제공 합니다.

   ![호텔 데이터베이스의 테이블 및 뷰](media/index-sql-relational-data/hotels-database-tables-view.png "호텔 데이터베이스의 테이블 및 뷰")


## <a name="the-problem-of-denormalized-data"></a>비 정규화 된 데이터의 문제

일 대 다 관계로 작업 하는 경우에는 조인 된 테이블을 기반으로 하는 표준 쿼리가 비 정규화 된 데이터를 반환 하기 때문에 Azure Cognitive Search 시나리오에서 제대로 작동 하지 않습니다. 호텔 및 대화방을 조인 하는 다음 예제를 살펴보십시오.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
이 쿼리의 결과는 모든 호텔 필드와 모든 방 필드를 반환 하 고 각 방 값에 대해 예비 호텔 정보를 반복 해 서 반환 합니다.

   ![비 정규화 된 데이터, 방 필드가 추가 될 때 중복 된 호텔 데이터](media/index-sql-relational-data/denormalize-data-query.png "비 정규화 된 데이터, 방 필드가 추가 될 때 중복 된 호텔 데이터")


이 쿼리는 화면에서 성공 하지만 (플랫 행 집합의 모든 데이터를 제공), 예상 된 검색 환경에 적합 한 문서 구조를 제공 하지 못합니다. 인덱싱 중에 Azure Cognitive Search는 수집 각 행에 대해 하나의 검색 문서를 만듭니다. 검색 문서가 위의 결과와 같이 표시 되 면 쌍 돔 호텔에 대해서만 중복 된 7 개의 개별 문서를 인식 하 게 됩니다. "플로리다의 호텔"에 대 한 쿼리는 쌍 돔 호텔에 대해서만 7 개의 결과를 반환 하 여 검색 결과에 다른 관련 호텔을 푸시합니다.

호텔 마다 하나의 문서에 대해 예상 되는 환경을 얻으려면 올바른 세분성에서 행 집합을 제공 해야 하지만 전체 정보를 제공 해야 합니다. 다행히이 문서의 기술을 채택 하 여이 작업을 쉽게 수행할 수 있습니다.

## <a name="define-a-query-that-returns-embedded-json"></a>포함 된 JSON을 반환 하는 쿼리 정의

예상 검색 환경을 제공 하기 위해 데이터 집합은 Azure Cognitive Search의 각 검색 문서에 대해 하나의 행으로 구성 되어야 합니다. 이 예제에서는 각 호텔에 대해 하나의 행을 만들려고 하지만, 사용자가 야간 요금, 크기 및 수, 베드 등 관심 있는 다른 방 관련 필드를 검색할 수 있도록 하 고,이 모든 항목은 대화방 정보의 일부입니다.

이 솔루션은 두 번째 단계에서와 같이 대화방 정보를 중첩 된 JSON으로 캡처한 다음 JSON 구조를 뷰의 필드에 삽입 하는 것입니다. 

1. 호텔 $ 및 대화방 $ 라는 두 개의 조인 된 테이블이 있다고 가정 합니다 .이는 50 호텔 및 750 대화방에 대 한 세부 정보를 포함 하 고 HotelID 필드에 조인 됩니다. 이러한 테이블은 개별적으로 50 호텔 및 750 관련 대화방을 포함 합니다.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. `SELECT * from dbo.Hotels$`중첩 쿼리의 출력을 포함 하는 새 *방* 필드를 추가 하 여 부모 테이블 ()의 모든 필드로 구성 된 뷰를 만듭니다. 출력을 JSON으로 하는 구조체에 **대 한 FOR JSON AUTO** 절입니다 `SELECT * from dbo.Rooms$` . 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   다음 스크린샷에서는 아래쪽에 *방* nvarchar 필드가 있는 결과 보기를 보여 줍니다. *방* 필드는 hotelrooms 보기에만 있습니다.

   ![HotelRooms 보기](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms 뷰")

1. `SELECT * FROM dbo.HotelRooms`을 실행 하 여 행 집합을 검색 합니다. 이 쿼리는 관련 된 대화방 정보를 JSON 컬렉션으로 사용 하 여 호텔 마다 하나씩 50 행을 반환 합니다. 

   ![HotelRooms 보기의 행 집합](media/index-sql-relational-data/hotelrooms-rowset.png "HotelRooms 보기의 행 집합")

이제이 행 집합을 Azure Cognitive Search로 가져올 준비가 되었습니다.

> [!NOTE]
> 이 방법은 포함 된 JSON이 [SQL Server의 최대 열 크기 제한](/sql/sql-server/maximum-capacity-specifications-for-sql-server)에 포함 되어 있다고 가정 합니다. 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>일 대 다 관계의 "다" 쪽에 복잡 한 컬렉션 사용

Azure Cognitive Search 쪽에서 중첩 된 JSON을 사용 하 여 일 대 다 관계를 모델링 하는 인덱스 스키마를 만듭니다. 이전 섹션에서 만든 결과 집합은 일반적으로 아래에 제공 된 인덱스 스키마에 해당 합니다 (간단히 하기 위해 일부 필드를 잘라내기).

다음 예는 [복합 데이터 형식을 모델링 하는 방법](search-howto-complex-data-types.md#creating-complex-fields)의 예제와 비슷합니다. 이 문서에 중점을 둔 *방* 구조는 *호텔*이라는 인덱스의 fields 컬렉션에 있습니다. 또한이 예제에서는 컬렉션에서 허용 되는 여러 개의 항목이 아닌 여러 개의 항목이 있는 경우와는 달리 고정 된 항목 집합으로 구성 된다는 점에서 *대화방* 과는 다른 *Address*의 복합 형식을 보여 줍니다.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

이전 결과 집합과 위의 인덱스 스키마가 제공 되 면 성공적인 인덱싱 작업에 필요한 모든 구성 요소가 있습니다. 일반 데이터 집합이 인덱싱 요구 사항을 충족 하지만 세부 정보는 유지 됩니다. Azure Cognitive Search 인덱스에서 검색 결과는 호텔 기반 엔터티로 쉽게 이동 하는 동시에 개별 대화방 및 해당 특성의 컨텍스트를 유지 합니다.

## <a name="next-steps"></a>다음 단계

사용자 고유의 데이터 집합을 사용 하 여 [데이터 가져오기 마법사](search-import-data-portal.md) 를 사용 하 여 인덱스를 만들고 로드할 수 있습니다. 마법사는 *대화방*에 포함 된 것과 같은 포함 된 JSON 컬렉션을 검색 하 고 복합 형식 컬렉션을 포함 하는 인덱스 스키마를 유추 합니다. 

  ![데이터 가져오기 마법사에서 유추 된 인덱스](media/index-sql-relational-data/search-index-rooms-complex-collection.png "데이터 가져오기 마법사에서 유추 된 인덱스")

데이터 가져오기 마법사의 기본 단계에 대해 알아보려면 다음 빠른 시작을 시도 합니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal을 사용 하 여 검색 인덱스 만들기](search-get-started-portal.md)