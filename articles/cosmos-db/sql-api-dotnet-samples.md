---
title: 'Azure Cosmos DB: SQL API에 대한 .NET 예제'
description: CRUD 작업을 포함하여 Azure Cosmos DB SQL API를 사용하는 일반적인 작업에 대한 C# .NET 예제를 GitHub에서 찾아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/23/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 6383505a7b6912e6fb673f711aac68136c6c7638
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015206"
---
# <a name="azure-cosmos-db-net-examples-for-the-sql-api"></a>Azure Cosmos DB: SQL API에 대한 .NET 예제

> [!div class="op_single_selector"]
> * [.NET V2 SDK 예제](sql-api-dotnet-samples.md)
> * [.NET V3 SDK 예제](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK 예제](sql-api-java-sdk-samples.md)
> * [Node.js 예제](sql-api-nodejs-samples.md)
> * [Python 예제](sql-api-python-samples.md)
> * [Azure 코드 샘플 갤러리](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[azure-cosmos-dotnet-v2](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/code-samples) GitHub 리포지토리에는 Azure Cosmos DB 리소스에 대한 CRUD 및 일반적인 다른 작업을 수행하는 최신 .NET 솔루션 샘플이 포함되어 있습니다. 이 문서는 다음을 제공합니다.

* 각 예제 C# 프로젝트 파일에서 작업에 연결합니다. 
* 관련된 API 참조 콘텐츠에 대한 링크입니다.

.NET SDK 버전 3.0(미리 보기) 코드 샘플의 경우 [azure cosmos-dotnet v3](https://github.com/Azure/azure-cosmos-dotnet-v3) GitHub 리포지토리의 최신 샘플을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항

Azure 개발 워크플로가 설치된 Visual Studio 2019
- **평가판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하여 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다. 

[Microsoft.Azure.DocumentDB NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) 

Azure 구독 또는 Cosmos DB 체험 계정
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)할 수 있습니다. Visual Studio 구독은 유료 Azure 서비스에 사용할 수 있는 크레딧을 매달 제공합니다.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> 샘플은 자체 포함되어 있으며, [CreateDocumentCollectionAsync()](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync)를 여러 번 호출하여 자체적으로 설정되고 정리됩니다. 각 호출이 발생할 때마다 컬렉션의 성능 계층에서 1시간 단위의 사용량에 해당하는 구독 요금이 청구됩니다. 
> 

## <a name="database-examples"></a>데이터베이스 예제
*DatabaseManagement* 프로젝트 샘플의 [RunDatabaseDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L75-L91) 메서드는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 데이터베이스에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md)을 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [데이터베이스 만들기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L77) |[DocumentClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) |
| [ID별 데이터베이스 읽기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L79) |[DocumentClient.ReadDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabaseasync) |
| [모든 데이터베이스를 읽기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L83) |[DocumentClient.ReadDatabaseFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdatabasefeedasync) |
| [데이터베이스 삭제](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DatabaseManagement/Program.cs#L89) |[DocumentClient.DeleteDatabaseAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedatabaseasync) |

## <a name="collection-examples"></a>컬렉션 예제
*CollectionManagement* 프로젝트 샘플의 [RunCollectionDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L86-L104) 메서드는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 컬렉션에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md)을 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [컬렉션 만들기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L109) |[DocumentClient.CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync) 
| [컬렉션의 구성된 성능 변경](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L190) |[DocumentClient.ReplaceOfferAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync) |
| [ID별 컬렉션 가져오기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L202) |[DocumentClient.ReadDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync) |
| [데이터베이스의 모든 컬렉션 읽기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L215) |[DocumentClient.ReadDocumentCollectionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentcollectionfeedasync) |
| [컬렉션 삭제](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L228) |[DocumentClient.DeleteDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentcollectionasync) |

## <a name="document-examples"></a>문서 예제
*DocumentManagement* 프로젝트 샘플의 [RunDocumentsDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L109-L118) 메서드는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos 문서에 대해 알아보려면 [데이터베이스, 컨테이너 및 항목 작업](databases-containers-items.md)을 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [문서 만들기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L154) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) |
| [ID로 문서 읽기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L168) |[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync) |
| [컬렉션의 모든 문서 읽기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L190) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [문서에 대한 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L18-L222) |[DocumentClient.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [문서 바꾸기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L240-L242) |[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [문서 Upsert](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L254) |[DocumentClient.UpsertDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.upsertdocumentasync) |
| [문서 삭제](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L275-L277) |[DocumentClient.DeleteDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.deletedocumentasync) |
| [.NET 동적 개체로 작업](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L349-L397) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync)<br>[DocumentClient.ReadDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentasync)<br>[DocumentClient.ReplaceDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentasync) |
| [조건부 ETag 검사로 문서 바꾸기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L405-L501) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |
| [문서가 변경된 경우에만 문서 읽기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L454-L500) |[DocumentClient.AccessCondition](/dotnet/api/microsoft.azure.documents.client.accesscondition)<br>[Documents.Client.AccessConditionType](/dotnet/api/microsoft.azure.documents.client.accessconditiontype) |

## <a name="indexing-examples"></a>청구 예제
*IndexManagement* 프로젝트 샘플의 [RunIndexDemo](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L93-L115) 메서드는 다음 작업을 수행하는 방법을 보여 줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 인덱싱에 대해 알아보려면 [인덱스 정책](index-policy.md), [인덱스 유형](index-types.md) 및 [인덱스 경로](index-paths.md)를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [인덱스에서 문서 제외](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L123-L162) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.documents.indexingdirective) |
| [지연 인덱싱 사용](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L174-L192) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.documents.indexingpolicy.indexingmode) |
| [인덱스에서 지정된 문서 경로 제외](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L202-L263) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.excludedpaths) |
| [해시 인덱스된 경로에 범위 검색 강제](https://github.com/Azure/azure-documentdb-dotnet/blob/2e9a48b6a446b47dd6182606c8608d439b88b683/samples/code-samples/IndexManagement/Program.cs#L305-L340) |[FeedOptions.EnableScanInQuery](/dotnet/api/microsoft.azure.documents.client.feedoptions.enablescaninquery) |
| [문자열에서 범위 인덱스 사용](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L265-L316) |[IndexingPolicy.IncludedPaths](/dotnet/api/microsoft.azure.documents.indexingpolicy.includedpaths)<br>[RangeIndex](/dotnet/api/microsoft.azure.documents.rangeindex) |
| [인덱스 변환 수행](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/IndexManagement/Program.cs#L318-L370) |[ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync) |

## <a name="geospatial-examples"></a>지리 공간 예제
*Geospatial* 프로젝트 샘플의 [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L94-L139) 메서드는 다음 작업을 수행하는 방법을 보여줍니다.  다음 샘플을 실행하기 전에 GeoJSON 및 지리 공간 데이터에 대해 알아보려면 [지리 공간 및 GeoJSON 위치 데이터 사용](geospatial.md)을 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [새 컬렉션에 지리 공간 인덱싱 사용](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L48) |[IndexingPolicy](/dotnet/api/microsoft.azure.documents.indexingpolicy) <br> [IndexKind.Spatial](/dotnet/api/microsoft.azure.documents.indexkind) <br>[DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [GeoJSON 지점으로 문서 삽입](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L104-L114) |[DocumentClient.CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync) </br> [DataType.Point](/dotnet/api/microsoft.azure.documents.datatype) |
| [지정된 거리 내에서 지점 찾기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L158-L199) |[ST_DISTANCE](sql-query-st-distance.md) </br> [GeometryOperationExtensions.Distance](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) |
| [다각형 내에서 지점 찾기](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L204-L227) |[ST_WITHIN](sql-query-st-within.md) </br> [GeometryOperationExtensions.Within](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.distance) </br>[Polygon](/dotnet/api/microsoft.azure.documents.spatial.polygon) |
| [기존 컬렉션에 지리 공간 인덱싱 사용](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L385-L391) |[DocumentClient.ReplaceDocumentCollectionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.replacedocumentcollectionasync)<br>[DocumentCollection.IndexingPolicy](/dotnet/api/microsoft.azure.documents.documentcollection.indexingpolicy) |
| [지점 및 다각형 데이터 유효성 검사](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs#L290-L326) |[ST_ISVALID](sql-query-st-isvalid.md)<br>[ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md)<br>[GeometryOperationExtensions.IsValid](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvalid)<br>[GeometryOperationExtensions.IsValidDetailed](/dotnet/api/microsoft.azure.documents.spatial.geometryoperationextensions.isvaliddetailed) |

## <a name="query-examples"></a>쿼리 예제
*Queries* 프로젝트 샘플의 [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L64-L129) 메서드는 SQL 쿼리 문법, 쿼리를 사용하는 LINQ 공급 기업 및 람다를 사용하여 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 SQL 쿼리 참조에 대해 알아보려면 [Azure Cosmos DB에 대한 SQL 쿼리 예제](how-to-sql-query.md)를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [모든 문서에 대한 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L131-L147) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [==를 사용하는 균등에 대한 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L186-L198) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [!= 및 NOT을 사용하는 불균등에 대한 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L288-L332) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [>, <, >=, <=같은 범위 연산자를 사용하는 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L334-L355) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [문자열에 대해 범위 연산자를 사용하는 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L355-L370) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [ORDER BY를 사용하여 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L422-L446) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [집계 함수를 사용하여 쿼리](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L448-L496) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [하위 문서로 작업](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L498-L524) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [문서 내 조인으로 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L526-L540) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [문자열, 수치 연산 및 배열 연산자로 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L636-L673) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [SqlQuerySpec를 사용하여 매개 변수가 있는 SQL로 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L149-L184) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100))<br>[SqlQuerySpec](/dotnet/api/microsoft.azure.documents.sqlqueryspec) |
| [명시적 페이징으로 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L675-L734) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [분할된 컬렉션 병렬 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L736-L807) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |
| [ORDER BY를 사용하여 분할된 컬렉션 쿼리](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs#L809-L882) |[DocumentQueryable.CreateDocumentQuery](/previous-versions/azure/dn850285(v=azure.100)) |

## <a name="change-feed-examples"></a>변경 피드 예제 
*ChangeFeed* 프로젝트 샘플의 [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L54-L97) 메서드는 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 피드 변경에 대해 알아보려면 [Azure Cosmos DB 변경 피드 읽기](read-change-feed.md) 및 [변경 피드 프로세서](change-feed-processor.md)를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [읽기 변경 피드](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L132) |[DocumentClient.CreateDocumentChangeFeedQuery](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery) | 
| [읽기 파티션 키 범위](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ChangeFeed/Program.cs#L118) |[DocumentClient.ReadPartitionKeyRangeFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpartitionkeyrangefeedasync) | 

[ChangeFeedMigrationTool](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedMigrationTool) 변경 피드 프로세서 샘플은 변경 피드 프로세서 라이브러리를 사용하여 데이터를 다른 Cosmos 컨테이너에 복제하는 방법을 보여 줍니다.   

## <a name="server-side-programming-examples"></a>서버 쪽 프로그래밍 예제
*ServerSideScripts* 프로젝트 샘플의 [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L58-L91) 메서드는 다음 작업을 수행하는 방법을 보여줍니다. 다음 샘플을 실행하기 전에 Azure Cosmos DB의 서버 쪽 프로그래밍에 대해 알아보려면 [저장 프로시저, 트리거 및 사용자 정의 함수](stored-procedures-triggers-udfs.md)를 참조하세요. 

| Task | API 참조 |
| --- | --- |
| [저장 프로시저 만들기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L110) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [저장 프로시저 실행](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L125) |[DocumentClient.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.executestoredprocedureasync) |
| [저장 프로시저에 대한 문서 피드 읽기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L198) |[DocumentClient.ReadDocumentFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readdocumentfeedasync) |
| [ORDER BY를 사용하여 저장 프로시저 만들기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L223) |[DocumentClient.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createstoredprocedureasync) |
| [사전 트리거 만들기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L273) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [사후 트리거 만들기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L341) |[DocumentClient.CreateTriggerAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createtriggerasync) |
| [UDF(사용자 정의 함수) 만들기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/ServerSideScripts/Program.cs#L421) |[DocumentClient.CreateUserDefinedFunctionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserdefinedfunctionasync) |

## <a name="user-management-examples"></a>사용자 관리 예제
*UserManagement* 프로젝트 샘플의 [RunDemoAsync](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/UserManagement/Program.cs#L55-L129) 메서드는 다음 작업을 수행하는 방법을 보여줍니다.

| Task | API 참조 |
| --- | --- |
| [사용자 만들기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L93) |[DocumentClient.CreateUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createuserasync) |
| [컬렉션 또는 문서에 권한 설정](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L97) |[DocumentClient.CreatePermissionAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createpermissionasync) |
| [사용자의 사용 권한 목록 가져오기](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/UserManagement/Program.cs#L241) |[DocumentClient.ReadUserAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readuserasync)<br>[DocumentClient.ReadPermissionFeedAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.readpermissionfeedasync) |

