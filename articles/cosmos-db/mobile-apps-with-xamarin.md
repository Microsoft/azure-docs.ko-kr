---
title: '자습서: Xamarin 및 Azure Cosmos DB를 사용하여 모바일 애플리케이션 빌드'
description: '자습서: Azure Cosmos DB를 사용하여 Xamarin iOS, Android 또는 Forms 애플리케이션을 만드는 방법에 대한 자습서입니다. Azure Cosmos DB는 속도가 빠른 세계적 규모의 모바일 앱용 클라우드 데이터베이스입니다.'
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 14a004864891aad768486c3a436b4936d693715e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88999039"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>자습서: Xamarin 및 Azure Cosmos DB를 사용하여 모바일 애플리케이션 빌드

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

대부분의 모바일 앱은 클라우드에 데이터를 저장해야 하며 Azure Cosmos DB는 모바일 앱용 클라우드 데이터베이스입니다. 이 기능에는 모바일 개발자에게 필요한 모든 항목이 있습니다. 필요에 따라 규모를 조정할 수 있는 Database as a Service입니다. 사용자가 전 세계 어디에 있든 데이터를 애플리케이션으로 투명하게 가져올 수 있습니다. [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md)를 사용하면 중간 계층 없이 Azure Cosmos DB와 직접 상호 작용하도록 Xamarin 모바일 앱을 활성화할 수 있습니다.

이 문서에서는 Xamarin 및 Azure Cosmos DB를 사용하여 모바일 앱을 빌드하는 자습서를 제공합니다. [GitHub의 Xamarin 및 Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin)에서 사용자 및 사용 권한을 관리하는 방법을 비롯하여 자습서의 전체 소스 코드를 찾을 수 있습니다.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>모바일 앱용 Azure Cosmos DB 기능
Azure Cosmos DB는 모바일 앱 개발자를 위해 다음과 같은 주요 기능을 제공합니다.

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-for-mobile.png" alt-text="모바일 앱용 Azure Cosmos DB 기능":::

* 스키마 없는 데이터에 대한 풍부한 쿼리 Azure Cosmos DB는 데이터를 유형이 다른 컬렉션에 스키마 없는 JSON 문서로 저장합니다. 스키마 또는 인덱스에 대한 걱정 없이 [풍부하고 빠른 쿼리](how-to-sql-query.md)를 제공합니다.
* 빠른 처리량. Azure Cosmos DB를 사용하여 문서를 읽고 쓰는 데 몇 밀리초밖에 걸리지 않습니다. 개발자들은 필요한 처리량을 지정할 수 있고, Azure Cosmos DB는 모든 단일 지역 계정 및 평범한 일관성 수준의 모든 다중 지역 계정에 대한 99.99% 가용성 SLA 및 모든 다중 지역 데이터베이스 계정에 대한 99.999% 읽기 가용성을 제공합니다.
* 규모가 무제한입니다. Azure Cosmos 컨테이너는 [앱이 증가함에 따라 증가합니다](partition-data.md). 처음에는 소규모 데이터와 초당 요청 처리량 수백 개로 시작할 수 있습니다. 이후에 컬렉션 또는 데이터베이스가 페타바이트 단위로 증가함에 따라 초당 요청 처리량을 수억 개로 확장할 수 있습니다.
* 전역적으로 분산됩니다. 모바일 앱 사용자는 이동이 잦으며 전 세계에 퍼져 있는 경우가 자주 있습니다. Azure Cosmos DB는 [전역적으로 분산되는 데이터베이스](distribute-data-globally.md)입니다. 지도를 클릭하여 사용자에게 데이터 액세스를 제공할 수 있습니다.
* 풍부한 권한이 기본 제공됩니다. Azure Cosmos DB를 사용하면 복잡한 사용자 지정 권한 부여 코드 없이 [사용자별 데이터](https://github.com/kirillg/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems)와 같은 인기 있는 패턴 또는 다중 사용자 공유 데이터를 쉽게 구현할 수 있습니다.
* 지리 공간적 쿼리. 현재 대부분의 모바일 앱은 지역 상황별 환경을 제공합니다. Azure Cosmos DB는 [지리 공간적 유형](geospatial.md)에 대한 최고 수준의 지원을 사용하여 이러한 환경을 쉽게 구축할 수 있습니다.
* 이진 첨부 파일입니다. 앱 데이터는 종종 이진 Blob을 포함합니다. 첨부 파일이 기본적으로 지원되므로 Azure Cosmos DB를 앱 데이터의 원스톱 상점으로 쉽게 사용할 수 있습니다.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB 및 Xamarin 자습서
다음 자습서에서는 Xamarin 및 Azure Cosmos DB를 사용하여 모바일 애플리케이션을 빌드하는 방법을 보여 줍니다. [GitHub의 Xamarin 및 Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin)에서 자습서의 전체 소스 코드를 찾을 수 있습니다.

### <a name="get-started"></a>시작하기
Azure Cosmos DB로 시작하는 것이 쉽습니다. Azure Portal로 이동하여 새 Azure Cosmos DB 계정을 만듭니다. **빠른 시작** 탭을 클릭합니다. Azure Cosmos DB 계정에 연결되어 있는 Xamarin Forms 할 일 목록 샘플을 다운로드합니다. 

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-quickstart.png" alt-text="모바일 앱의 Azure Cosmos DB 빠른 시작":::

기존 Xamarin 앱이 있는 경우 [Azure Cosmos DB NuGet 패키지](sql-api-sdk-dotnet-core.md)를 추가하면 됩니다. Azure Cosmos DB는 Xamarin.IOS, Xamarin.Android 및 Xamarin Forms 공유 라이브러리를 지원합니다.

### <a name="work-with-data"></a>데이터 작업
데이터 레코드는 유형이 다른 컬렉션에서 스키마 없는 JSON 문서로 Azure Cosmos DB에 저장됩니다. 문서를 갖은 컬렉션에 다른 구조로 저장할 수 있습니다.

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Xamarin 프로젝트에서 스키마 없는 데이터에 언어가 통합된 쿼리를 사용할 수 있습니다.

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>사용자 추가
대부분의 시작 샘플과 마찬가지로 사용자가 다운로드한 Azure Cosmos DB 샘플은 앱 코드에 하드 코딩된 마스터 키를 사용하여 서비스에 인증합니다. 이 기본적인 방식은 로컬 에뮬레이터를 제외한 모든 위치에서 실행하려는 앱에는 별로 좋은 방법이 아닙니다. 인증을 받지 않은 사용자가 마스터 키를 획득하면 Azure Cosmos DB 계정에 있는 모든 데이터가 손상될 수 있습니다. 이 방법 대신, 앱이 로그인한 사용자의 레코드에만 액세스하게 하는 방법이 좋습니다. 개발자는 Azure Cosmos DB를 사용하여 컬렉션, 파티션 키로 그룹화된 일련의 문서 또는 특정 문서에 대한 애플리케이션 읽기 또는 읽기/쓰기 권한을 부여할 수 있습니다. 

다음 단계에 따라 할 일 목록 앱을 다중 사용자 할 일 목록 앱으로 수정하세요. 

  1. Facebook, Active Directory 또는 기타 공급자를 사용하여 앱에 로그인을 추가합니다.

  2. **/userId**를 파티션 키로 사용하여 Azure Cosmos DB UserItems 컬렉션을 만듭니다. 컬렉션의 파티션 키를 지정하면 빠른 쿼리를 제공하면서도 앱 사용자가 증가하는 만큼 Azure Cosmos DB를 무한히 확장할 수 있습니다.

  3. Azure Cosmos DB 리소스 토큰 브로커를 추가합니다. 이 샘플 Web API는 사용자를 인증하고, 로그인한 사용자에게 파티션 내의 문서에만 액세스할 수 있는 단기 토큰을 발급합니다. 이 예에서 리소스 토큰 broker는 App Service에 호스팅됩니다.

  4. 앱을 수정하여 Facebook을 통해 리소스 토큰 broker에 인증하고, 로그인한 Facebook 사용자에게 리소스 토큰을 요청합니다. 그런 다음 UserItems 컬렉션에서 사용자 데이터에 액세스할 수 있습니다.  

[GitHub의 리소스 토큰 broker](https://github.com/kirillg/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems)에서 이 패턴의 전체 코드 샘플을 찾을 수 있습니다. 이 다이어그램에서는 솔루션을 보여 줍니다.

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png" alt-text="Azure Cosmos DB 사용자 및 사용 권한 브로커" border="false":::

두 명의 사용자가 동일한 할 일 목록에 액세스하게 하려면 리소스 토큰 broker에서 액세스 토큰에 권한을 추가하면 됩니다.

### <a name="scale-on-demand"></a>주문형 확장
Azure Cosmos DB는 관리되는 Database as a Service입니다. 사용자 기반이 늘어나면 VM을 프로비전하거나 코어를 증가시킬 것을 걱정할 필요가 없습니다. 앱에서 필요한 초당 작업의 수(처리량)를 Azure Cosmos DB에 지시하면 됩니다. 초당 RU(요청 단위)라고 하는 처리량 측정 단위를 사용하여 **규모** 탭을 통해 처리량을 지정할 수 있습니다. 예를 들어 1KB 문서의 읽기 작업에는 1RU가 필요합니다. **처리량** 메트릭에 대한 경고를 추가하여 트래픽 증가를 모니터링하고 경고가 발생하면 처리량을 프로그래밍 방식으로 변경할 수 있습니다.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png" alt-text="주문형 Azure Cosmos DB 크기 조정 처리량":::

### <a name="go-planet-scale"></a>세계적 규모로 확장
앱이 인기를 얻게 되면 전 세계에 걸쳐 사용자가 늘어날 수 있습니다. 또는 예상치 못한 이벤트에 대비해야 할 수도 있습니다. Azure Portal로 이동하여 새 Azure Cosmos DB 계정을 엽니다. 지도를 클릭하여 전 세계 모든 지역에 데이터를 지속적으로 복제할 수 있습니다. 이 기능을 사용하면 사용자가 어디서나 데이터 사용할 수 있습니다. 또한 만약의 사태에 대비하여 장애 조치(failover) 정책을 추가할 수 있습니다.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png" alt-text="지리적 지역에 Azure Cosmos DB 크기 조정" border="false":::

축하합니다. 솔루션 완료하고 Xamarin 및 Azure Cosmos DB를 사용한 모바일 앱이 있습니다. Azure Cosmos DB JavaScript SDK 및 Azure Cosmos DB REST API를 사용하는 네이티브 iOS/Android 앱을 사용하여 비슷한 단계에 따라 Cordova 앱을 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [GitHub의 Xamarin 및 Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin)의 소스 코드를 봅니다.
* [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md)를 다운로드합니다.
* [.NET 애플리케이션](sql-api-dotnet-samples.md)에 대한 추가 코드 샘플을 찾습니다.
* [Azure Cosmos DB의 다양한 쿼리 기능](how-to-sql-query.md)에 대해 알아봅니다.
* [Azure Cosmos DB의 지리 공간적 지원](geospatial.md)에 대해 알아봅니다.



