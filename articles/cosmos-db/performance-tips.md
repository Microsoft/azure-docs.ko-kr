---
title: .NET SDK v2에 대 한 Azure Cosmos DB 성능 팁
description: Azure Cosmos DB .NET v2 SDK 성능 향상을 위한 클라이언트 구성 옵션에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/26/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: bdf512c66958338992c5959f8e00b4589850ff33
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008372"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net-sdk-v2"></a>Azure Cosmos DB 및 .NET SDK v2의 성능 팁

> [!div class="op_single_selector"]
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)

Azure Cosmos DB는 보장된 대기 시간 및 처리량으로 매끄럽게 크기가 조정되는 빠르고 유연한 분산 데이터베이스입니다. 주 아키텍처를 변경 하거나 복잡 한 코드를 작성 하 여 Azure Cosmos DB로 데이터베이스를 확장할 필요가 없습니다. 규모를 확장 및 축소하는 방법이 API 호출을 하나 만드는 것만큼 쉽습니다. 자세한 내용은 [컨테이너 처리량을 프로비전하는 방법](how-to-provision-container-throughput.md) 또는 [데이터베이스 처리량을 프로비전하는 방법](how-to-provision-database-throughput.md)을 참조하세요. 그러나 네트워크 호출을 통해 Azure Cosmos DB에 액세스 하기 때문에 [SQL .NET SDK](sql-api-sdk-dotnet-standard.md)를 사용할 때 최대 성능을 얻기 위해 클라이언트 쪽 최적화를 수행할 수 있습니다.

따라서 데이터베이스 성능을 개선 하려는 경우 다음 옵션을 고려 하세요.

## <a name="upgrade-to-the-net-v3-sdk"></a>.NET V3 SDK로 업그레이드

[.Net V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3) 가 릴리스됩니다. .NET v3 SDK를 사용 하는 경우 다음 정보에 대 한 [.net v3 성능 가이드](performance-tips-dotnet-sdk-v3-sql.md) 를 참조 하세요.

- 기본값은 Direct TCP 모드입니다.
- Stream API 지원
- 사용에 대 한 System.Text.JS를 허용 하는 사용자 지정 serializer 지원
- 통합 일괄 처리 및 대량 지원

## <a name="hosting-recommendations"></a>호스팅 권장 사항

**쿼리를 많이 사용 하는 워크 로드의 경우 Linux 또는 Windows 32 비트 호스트 처리 대신 Windows 64 비트를 사용 합니다.**

성능 향상을 위해 Windows 64 비트 호스트를 처리 하는 것이 좋습니다. SQL SDK에는 로컬에서 쿼리를 구문 분석하고 최적화하는 네이티브 ServiceInterop.dll이 포함되어 있습니다. ServiceInterop.dll은 Windows x64 플랫폼에서만 지원됩니다. ServiceInterop.dll를 사용할 수 없는 Linux 및 기타 지원 되지 않는 플랫폼의 경우 게이트웨이에 대 한 추가 네트워크 호출을 수행 하 여 최적화 된 쿼리를 가져옵니다. 다음 유형의 응용 프로그램은 기본적으로 32 비트 호스트 처리를 사용 합니다. 호스트 처리를 64 비트 처리로 변경 하려면 응용 프로그램의 형식에 따라 다음 단계를 수행 합니다.

- 실행 응용 프로그램의 경우 **프로젝트 속성** 창의 **빌드** 탭에서 [플랫폼 대상](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) 을 x **64** 로 설정 하 여 호스트 처리를 변경할 수 있습니다.

- Vstest.console.exe 기반 테스트 프로젝트의 경우 **Test**  >  Visual Studio 테스트 메뉴에서 테스트**테스트 설정**  >  **기본 프로세서 아키텍처 x 64** **Test** 를 선택 하 여 호스트 처리를 변경할 수 있습니다.

- 로컬로 배포 된 ASP.NET 웹 응용 프로그램의 경우 **도구**옵션 프로젝트 및 솔루션 **웹 프로젝트에서 웹 사이트 및 프로젝트에 64 비트 버전의 IIS Express 사용** 을 선택 하 여 호스트 처리를 변경할 수 있습니다  >  **Options**  >  **Projects and Solutions**  >  **Web Projects**.

- Azure에 배포 된 ASP.NET 웹 응용 프로그램의 경우 Azure Portal의 **응용 프로그램 설정** 에서 **64 비트** 플랫폼을 선택 하 여 호스트 처리를 변경할 수 있습니다.

> [!NOTE] 
> 기본적으로 새 Visual Studio 프로젝트는 **모든 CPU**로 설정 됩니다. **X 86**으로 전환 되지 않도록 프로젝트를 x **64** 로 설정 하는 것이 좋습니다. X86 전용 종속성이 추가 된 경우 **모든 CPU** 로 설정 된 프로젝트는 **x86** 으로 쉽게 전환할 수 있습니다.<br/>
> ServiceInterop.dll은 SDK DLL이 실행 되는 폴더에 있어야 합니다. Dll을 수동으로 복사 하거나 사용자 지정 빌드/배포 시스템을 포함 하는 경우에만 문제가 됩니다.
    
**서버 쪽 가비지 수집 (GC) 설정**

가비지 수집의 빈도를 줄이면 일부 경우에 도움이 될 수 있습니다. .NET에서 [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) 를로 설정 `true` 합니다.

**클라이언트 워크 로드 규모 확장**

높은 처리량 수준에서 테스트 하는 경우 (5만 이상/s 이상) 클라이언트 응용 프로그램은 CPU 또는 네트워크 사용률에 대 한 컴퓨터 50, 인해 병목 상태가 발생할 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 애플리케이션을 확장하여 Azure Cosmos DB 계정을 계속 추가할 수 있습니다.

> [!NOTE] 
> 높은 CPU 사용량으로 인해 대기 시간 및 요청 시간 제한 예외가 발생할 수 있습니다.

## <a name="networking"></a><a id="networking"></a> 네트워킹

**연결 정책: 직접 연결 모드 사용**

클라이언트에서 Azure Cosmos DB에 연결 하는 방법은 특히 관찰 되는 클라이언트 쪽 대기 시간에 대해 중요 한 성능에 영향을 미칩니다. 클라이언트 연결 정책 구성에 사용할 수 있는 두 가지 주요 구성 설정: 연결 *모드* 및 연결 *프로토콜*입니다.  두 가지 사용 가능한 모드는 같습니다.

  * 게이트웨이 모드 (기본값)
      
    게이트웨이 모드는 모든 SDK 플랫폼에서 지원 되며, [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md)에 대해 구성 된 기본값입니다. 응용 프로그램이 엄격한 방화벽 제한을 사용 하는 회사 네트워크 내에서 실행 되는 경우 표준 HTTPS 포트 및 단일 DNS 끝점을 사용 하기 때문에 게이트웨이 모드를 선택 하는 것이 가장 좋습니다. 그러나 성능 단점은 데이터를 Azure Cosmos DB에서 읽거나 쓸 때마다 게이트웨이 모드에 추가 네트워크 홉이 포함 된다는 것입니다. 따라서 직접 모드는 네트워크 홉이 적기 때문에 더 나은 성능을 제공 합니다. 또한 소켓 연결 수가 제한 된 환경에서 응용 프로그램을 실행할 때 게이트웨이 연결 모드를 권장 합니다.

    특히 [소비 계획](../azure-functions/functions-scale.md#consumption-plan)에서 Azure Functions SDK를 사용 하는 경우 [연결에 대 한 현재 제한](../azure-functions/manage-connections.md)사항을 알고 있어야 합니다. 이 경우 Azure Functions 응용 프로그램 내에서 다른 HTTP 기반 클라이언트를 사용 하는 경우에도 게이트웨이 모드가 더 좋을 수 있습니다.

  * 직접 모드

    직접 모드는 TCP 프로토콜을 통한 연결을 지원 합니다.
     
직접 모드에서 TCP를 사용 하는 경우 게이트웨이 포트 외에도 Azure Cosmos DB에서 동적 TCP 포트를 사용 하므로 1만과 2만 사이의 포트 범위가 열려 있는지 확인 해야 합니다. [전용 끝점](./how-to-configure-private-endpoints.md)에서 직접 모드를 사용 하는 경우 TCP 포트의 전체 범위 (0 ~ 65535)가 열려 있어야 합니다. 이러한 포트가 열려 있지 않은 상태에서 TCP 프로토콜을 사용 하려고 하면 503 서비스를 사용할 수 없음 오류가 표시 됩니다. 다음 표에서는 각 API에 사용 되는 다양 한 Api 및 서비스 포트에 사용할 수 있는 연결 모드를 보여 줍니다.

|연결 모드  |지원되는 프로토콜  |지원되는 SDK  |API/서비스 포트  |
|---------|---------|---------|---------|
|게이트웨이  |   HTTPS    |  모든 Sdk    |   SQL (443), MongoDB (10250, 10255, 10256), 테이블 (443), Cassandra (10350), 그래프 (443) <br> 10250 포트는 지역에서 복제를 사용 하지 않고 MongoDB 인스턴스에 대 한 기본 Azure Cosmos DB API에 매핑됩니다. 반면에 포트 10255 및 10256은 지역에서 복제를 포함 하는 인스턴스에 매핑됩니다.   |
|직접    |     TCP    |  .NET SDK    | 공개/서비스 끝점을 사용 하는 경우: 1만 ~ 2만 범위의 포트<br>개인 끝점을 사용 하는 경우: 0-65535 범위의 포트 |

Azure Cosmos DB는 HTTPS를 통해 단순한 개방형 RESTful 프로그래밍 모델을 제공 합니다. 또한 통신 모델이 RESTful이며 .NET 클라이언트 SDK를 통해 사용할 수 있는 효율적인 TCP 프로토콜도 제공합니다. TCP 프로토콜은 초기 인증 및 암호화 트래픽에 TLS를 사용 합니다. 최상의 성능을 위해 가능한 경우 TCP 프로토콜을 사용 합니다.

Microsoft.Azure.DocumentDB SDK의 경우 `DocumentClient` 매개 변수를 사용 하 여 인스턴스를 생성 하는 동안 연결 모드를 구성 합니다 `ConnectionPolicy` . 직접 모드를 사용 하는 경우 `Protocol` 매개 변수를 사용 하 여를 설정할 수도 있습니다 `ConnectionPolicy` .

```csharp
Uri serviceEndpoint = new Uri("https://contoso.documents.net");
string authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

TCP는 직접 모드 에서만 지원 되므로 게이트웨이 모드를 사용 하는 경우 항상 HTTPS 프로토콜을 사용 하 여 게이트웨이와 통신 하 고 `Protocol` 의 값은 무시 됩니다 `ConnectionPolicy` .

:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Azure Cosmos DB 연결 정책" border="false":::

**사용 후 삭제되는 포트 고갈**

인스턴스에 높은 연결 볼륨이 나 높은 포트 사용량이 표시 되는 경우 먼저 클라이언트 인스턴스가 단일 항목 인지 확인 합니다. 즉, 클라이언트 인스턴스는 응용 프로그램의 수명 동안 고유 해야 합니다.

TCP 프로토콜에서 실행 하는 경우 클라이언트는 HTTPS 프로토콜을 사용 하는 것과는 반대로 수명이 긴 연결을 사용 하 여 대기 시간을 최적화 합니다. 그러면 2 분 동안 활동이 없을 경우 연결이 종료 됩니다.

스파스 액세스가 있고 게이트웨이 모드 액세스와 비교할 때 더 높은 연결 수를 확인 하는 시나리오에서 다음을 수행할 수 있습니다.

* [Connectionpolicy ..-Ususemode](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.portreusemode) 속성을 `PrivatePortPool` (framework 버전>= 4.6.1 및 .net core 버전 >= 2.0)로 구성 합니다 .이 속성을 사용 하면 SDK에서 다른 Azure Cosmos DB 대상 끝점에 대 한 삭제 포트의 작은 풀을 사용할 수 있습니다.
* [Connectionpolicy를 구성 합니다. IdleConnectionTimeout](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.idletcpconnectiontimeout) 속성은 10 분 보다 크거나 같아야 합니다. 권장 값은 20 분에서 24 시간 사이입니다.

**첫 번째 요청 시 시작 대기 시간을 피하기 위해 OpenAsync 호출**

기본적으로 첫 번째 요청은 주소 라우팅 테이블을 인출 해야 하기 때문에 대기 시간이 더 높습니다. [SDK V2](sql-api-sdk-dotnet.md)를 사용 하는 경우 `OpenAsync()` 첫 번째 요청에서이 시작 대기 시간을 방지 하기 위해 초기화 하는 동안 한 번 호출 합니다. 호출은 다음과 같습니다. `await client.OpenAsync();`

> [!NOTE]
> `OpenAsync` 는 계정의 모든 컨테이너에 대 한 주소 라우팅 테이블을 가져오기 위해 요청을 생성 합니다. 많은 컨테이너가 있지만 응용 프로그램이 그 하위 집합에 액세스 하는 계정의 경우에는 `OpenAsync` 불필요 한 트래픽 양이 생성 되어 초기화 속도가 느려집니다. 따라서 `OpenAsync` 응용 프로그램 시작 속도가 느려지므로이 시나리오에서는를 사용 하는 것이 유용 하지 않을 수 있습니다.

**성능을 위해 동일한 Azure 지역의 배치 클라이언트**

가능 하면 Azure Cosmos DB를 호출 하는 모든 응용 프로그램을 Azure Cosmos DB 데이터베이스와 동일한 지역에 저장 합니다. 대략적인 비교는 다음과 같습니다. 동일한 지역 내 Azure Cosmos DB에 대 한 호출은 1 밀리초에서 2 밀리초 내에 완료 되지만 미국 서 부와 동부 해안 사이의 대기 시간은 50 밀리초를 초과 합니다. 이 대기 시간은 클라이언트에서 Azure 데이터 센터 경계로 전달 될 때 요청이 수행 하는 경로에 따라 요청에 따라 달라질 수 있습니다. 호출 하는 응용 프로그램이 프로 비전 된 Azure Cosmos DB 끝점과 동일한 Azure 지역 내에 있도록 하면 가능한 최저 대기 시간을 얻을 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

:::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 연결 정책" border="false":::

**스레드/작업 수 늘리기**
<a id="increase-threads"></a>

Azure Cosmos DB 호출은 네트워크를 통해 수행 되므로 클라이언트 응용 프로그램이 요청 간에 대기 하는 최소 시간을 소비 하도록 요청의 병렬 처리 수준을 변경 해야 할 수 있습니다. 예를 들어 .NET [작업 병렬 라이브러리](https://msdn.microsoft.com//library/dd460717.aspx)를 사용 하는 경우 Azure Cosmos DB에서 읽거나 쓸 수 있는 수백 개의 작업 순서를 만듭니다.

**가속 네트워킹 사용**
 
대기 시간 및 CPU 지터를 줄이려면 클라이언트 가상 머신에서 가속화 된 네트워킹을 사용 하는 것이 좋습니다. 가속화 된 [네트워킹을 사용 하 여 Windows 가상 머신 만들기](../virtual-network/create-vm-accelerated-networking-powershell.md) 또는 [가속화 된 네트워킹을 사용 하 여 Linux 가상 머신 만들기](../virtual-network/create-vm-accelerated-networking-cli.md)를 참조 하세요.

## <a name="sdk-usage"></a>SDK 사용

**최신 SDK 설치**

Azure Cosmos DB SDK는 최상의 성능을 제공하기 위해 지속적으로 향상됩니다. [Azure Cosmos DB SDK](sql-api-sdk-dotnet-standard.md) 페이지를 참조하여 최신 SDK를 확인하고 향상된 기능을 검토하세요.

**애플리케이션 수명 동안 singleton Azure Cosmos DB 클라이언트 사용**

각 `DocumentClient` 인스턴스는 스레드로부터 안전 하 고 직접 모드에서 작동 하는 경우 효율적인 연결 관리와 주소 캐싱을 수행 합니다. 효율적으로 연결을 관리 하 고 SDK 클라이언트 성능을 향상 시키려면 `AppDomain` 응용 프로그램의 수명 주기 당 단일 인스턴스를 사용 하는 것이 좋습니다.

**게이트웨이 모드를 사용 하는 경우 호스트 당 System.Net MaxConnections 증가**

Azure Cosmos DB 요청은 게이트웨이 모드를 사용 하는 경우 HTTPS/REST를 통해 수행 됩니다. 호스트 이름 또는 IP 주소에 따라 기본 연결 제한이 적용 됩니다. `MaxConnections`클라이언트 라이브러리에서 Azure Cosmos DB에 대 한 여러 동시 연결을 사용할 수 있도록 더 높은 값 (100 ~ 1000)으로 설정 해야 할 수도 있습니다. .NET SDK 1.8.0 이상에서 [Servicepointmanager](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 의 기본값은 50입니다. 값을 변경 하려면 [MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) 를 더 높은 값으로 설정 하면 됩니다.

**분할 된 컬렉션에 대 한 병렬 쿼리 조정**

SQL .NET SDK 1.9.0 이상에서는 병렬 쿼리를 지원 하므로 분할 된 컬렉션을 병렬로 쿼리할 수 있습니다. 자세한 내용은 SDK 사용과 관련된 [코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs)을 참조하세요. 병렬 쿼리는 일련의 대응 보다 더 나은 쿼리 대기 시간 및 처리량을 제공 하도록 설계 되었습니다. 병렬 쿼리는 요구 사항에 맞게 튜닝할 수 있는 두 가지 매개 변수를 제공 합니다. 
- `MaxDegreeOfParallelism` 병렬로 쿼리할 수 있는 최대 파티션 수를 제어 합니다. 
- `MaxBufferedItemCount` 미리 인출 된 결과의 수를 제어 합니다.

***병렬 처리 수준 튜닝***

병렬 쿼리는 여러 파티션을 병렬로 쿼리 하는 방식으로 작동 합니다. 하지만 개별 파티션의 데이터는 쿼리와 관련 하 여 순차적으로 인출 됩니다. `MaxDegreeOfParallelism` [SDK](sql-api-sdk-dotnet.md) v 2에서 파티션 수로 설정 하면 다른 모든 시스템 조건을 동일 하 게 유지 하는 가장 뛰어난 쿼리를 달성할 수 있습니다. 파티션 수를 모르는 경우 병렬 처리 수준을 높은 값으로 설정할 수 있습니다. 시스템은 병렬 처리 수준으로 최소 (파티션 수, 사용자가 제공한 입력)를 선택 합니다.

병렬 쿼리는 데이터를 쿼리와 관련 하 여 모든 파티션에 균등 하 게 분산 하는 경우 가장 많은 이점을 생성 합니다. 쿼리에서 반환 하는 모든 데이터 또는 대부분의 데이터가 일부 파티션에서 집중 되도록 분할 된 컬렉션이 분할 된 경우 (하나의 파티션이 최악의 경우) 이러한 파티션이 쿼리 성능에 병목 상태가 됩니다.

***튜닝 MaxBufferedItemCount***
    
병렬 쿼리는 클라이언트에서 현재 결과 일괄 처리를 처리하는 동안 결과를 프리페치하도록 설계되었습니다. 이 미리 페치를 통해 쿼리의 전체 대기 시간을 향상 시킬 수 있습니다. `MaxBufferedItemCount`매개 변수는 미리 인출 된 결과의 수를 제한 합니다. `MaxBufferedItemCount`반환 되는 결과의 예상 개수 (또는 더 높은 수)로 설정 하 여 쿼리가 사전 페치를 최대한 활용할 수 있도록 합니다.

사전 인출은 병렬 처리 수준에 관계 없이 동일한 방식으로 작동 하며 모든 파티션의 데이터에 대 한 단일 버퍼가 있습니다.  

**RetryAfter 간격으로 백오프 구현**

성능 테스트 중에는 적은 수의 요청이 제한 될 때까지 부하를 늘려야 합니다. 요청이 제한 되는 경우 클라이언트 응용 프로그램은 서버에서 지정한 재시도 간격에 대 한 제한에 따라 백오프 해야 합니다. 백오프를 사용 하면 다시 시도 사이에 대기 시간을 최소화 하는 데 도움이 됩니다. 

다시 시도 정책 지원은 다음 Sdk에 포함 되어 있습니다.
- 1.8.0 이상 버전의 [.NET sdk](sql-api-sdk-dotnet.md) 및 [SQL 용 Java sdk](sql-api-sdk-java.md)
- [Sql 용Node.js SDK](sql-api-sdk-node.md) 및 Sql [용 Python sdk](sql-api-sdk-python.md) 의 버전 1.9.0 이상
- 지원 되는 모든 버전의 [.Net Core](sql-api-sdk-dotnet-core.md) sdk 

자세한 내용은 [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)를 참조 하세요.
    
.NET SDK 버전 1.19 이상에서는 다음 샘플과 같이 추가 진단 정보를 기록 하 고 대기 시간 문제를 해결 하는 메커니즘이 있습니다. 읽기 대기 시간이 더 긴 요청에 대한 진단 문자열을 기록할 수 있습니다. 캡처된 진단 문자열은 지정 된 요청에 대해 429 오류를 받은 횟수를 이해 하는 데 도움이 됩니다.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**짧은 읽기 대기 시간 동안 문서 URI 캐시**

최상의 문서 읽기 성능이 필요할 때마다 문서 URI를 캐시합니다. 리소스를 만들 때 리소스 ID를 캐시 하는 논리를 정의 해야 합니다. 리소스 Id를 기반으로 하는 조회는 이름 기반 조회 보다 빠르기 때문에 이러한 값을 캐시 하면 성능이 향상 됩니다.

**성능 향상을 위해 쿼리/읽기 피드에 맞게 페이지 크기 조정**

읽기 피드 기능 (예:)을 사용 하 여 문서를 대량으로 읽거나 `ReadDocumentFeedAsync` SQL 쿼리를 실행할 때 결과 집합이 너무 크면 결과가 분할 된 방식으로 반환 됩니다. 기본적으로, 100개의 항목 또는 1MB 단위(둘 중 먼저 도달하는 단위)로 결과가 반환됩니다.

적용 가능한 모든 결과를 검색 하는 데 필요한 네트워크 왕복 횟수를 줄이기 위해 [x m m-최대 항목 수](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 를 사용 하 여 최대 1000 개의 헤더를 요청 하 여 페이지 크기를 늘릴 수 있습니다. 예를 들어 사용자 인터페이스 또는 응용 프로그램 API가 한 번에 10 개의 결과만 반환 하는 경우와 같이 몇 가지 결과만 표시 해야 하는 경우 읽기 및 쿼리에 사용 되는 처리량을 줄이기 위해 페이지 크기를 10으로 줄일 수도 있습니다.

> [!NOTE] 
> `maxItemCount`속성은 페이지 매김을 위해 사용 하면 안 됩니다. 가장 기본적인 용도는 단일 페이지에서 반환 되는 최대 항목 수를 줄여 쿼리 성능을 향상 시키는 것입니다.  

사용 가능한 Azure Cosmos DB Sdk를 사용 하 여 페이지 크기를 설정할 수도 있습니다. 의 [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) 속성을 `FeedOptions` 사용 하면 열거 작업에서 반환할 최대 항목 수를 설정할 수 있습니다. `maxItemCount`가-1로 설정 된 경우 SDK는 문서 크기에 따라 최적의 값을 자동으로 찾습니다. 예를 들어:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
쿼리가 실행 되 면 결과 데이터가 TCP 패킷 내에 전송 됩니다. 값을 너무 낮게 지정 하면 `maxItemCount` TCP 패킷 내에서 데이터를 전송 하는 데 필요한 왕복 횟수가 높아 성능에 영향을 줍니다. 따라서 속성에 대해 설정할 값을 잘 모를 경우 `maxItemCount` -1로 설정 하 고 SDK에서 기본값을 선택 하는 것이 가장 좋습니다.

**스레드/작업 수 늘리기**

이 문서의 네트워킹 섹션에서 [스레드/작업 수 늘리기](#increase-threads) 를 참조 하세요.

## <a name="indexing-policy"></a>인덱싱 정책
 
**더 빠른 쓰기에 대한 인덱싱에서 사용하지 않는 경로 제외**

인덱싱 경로 (Indexingpolicy.includedpaths 및 Indexingpolicy.excludedpaths)를 사용 하 여 인덱싱에 포함 하거나 인덱싱에서 제외할 문서 경로를 지정할 수도 있습니다. Azure Cosmos DB 인덱싱 경로는 쿼리 패턴을 사전에 알고 있는 시나리오에 대 한 쓰기 성능을 향상 시키고 인덱스 저장소를 줄일 수 있습니다. 이는 인덱싱 비용이 인덱싱된 고유 경로 수와 직접 연관 되기 때문입니다. 예를 들어 다음 코드는 "*" 와일드 카드를 사용 하 여 인덱싱에서 문서의 전체 섹션 (하위 트리)을 제외 하는 방법을 보여 줍니다.

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);
```

자세한 내용은 [Azure Cosmos DB 인덱싱 정책](index-policy.md)을 참조하세요.

## <a name="throughput"></a><a id="measure-rus"></a> 능력

**낮은 요청 단위/초 사용을 측정 하 고 조정 합니다.**

Azure Cosmos DB는 다양 한 데이터베이스 작업 집합을 제공 합니다. 이러한 작업에는 모두 데이터베이스 컬렉션 내의 문서에서 작동 하는 Udf, 저장 프로시저 및 트리거를 사용 하 여 관계형 및 계층 쿼리가 포함 됩니다. 이러한 각 작업과 관련 된 비용은 작업을 완료 하는 데 필요한 CPU, IO 및 메모리에 따라 달라 집니다. 하드웨어 리소스를 생각 하 고 관리 하는 대신 다양 한 데이터베이스 작업을 수행 하 고 응용 프로그램 요청을 처리 하는 데 필요한 리소스에 대 한 단일 측정으로 요청 단위 (작업)를 생각해 볼 수 있습니다.

처리량은 각 컨테이너에 설정 된 [요청 단위](request-units.md) 수를 기준으로 프로 비전 됩니다. 요청 단위 소비는 초당 비율로 평가 됩니다. 해당 컨테이너에 대해 프로 비전 된 요청 단위를 초과 하는 응용 프로그램은 해당 비율이 컨테이너에 대해 프로 비전 된 수준 아래로 떨어질 때까지 제한 됩니다. 응용 프로그램에 더 높은 수준의 처리량이 필요한 경우 추가 요청 단위를 프로 비전 하 여 처리량을 늘릴 수 있습니다.

쿼리의 복잡성은 작업에 사용 되는 요청 단위 수에 영향을 줍니다. 조건자의 수, 조건자의 특성, Udf 수 및 원본 데이터 집합의 크기는 모두 쿼리 작업의 비용에 영향을 줍니다.

모든 작업 (만들기, 업데이트 또는 삭제)의 오버 헤드를 측정 하려면 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) `RequestCharge` `ResourceResponse\<T>` `FeedResponse\<T>` 작업에서 사용 하는 요청 단위 수를 측정 하려면 X-Y 요청 (또는 .net SDK의 또는 해당 하는 속성)을 검사 합니다.

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

이 헤더에서 반환 된 요청 요금은 프로 비전 된 처리량 (즉, 2000 RUs/second)의 일부입니다. 예를 들어 위의 쿼리가 1000 1 KB 문서를 반환 하는 경우 작업 비용은 1000입니다. 따라서 1 초 이내에 서버는 이후 요청에 대해 rate를 제한 하기 전에 두 요청을 모두 인식 합니다. 자세한 내용은 [요청 단위](request-units.md) 및 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 참조 하세요.
<a id="429"></a>

**너무 큰 속도 제한/요청 속도 처리**

클라이언트가 계정에 대해 예약 된 처리량을 초과 하려고 시도 하면 서버에서 성능 저하가 발생 하지 않으며 예약 된 수준 이상의 처리량 용량이 사용 되지 않습니다. 서버는 RequestRateTooLarge (HTTP 상태 코드 429)를 사용 하 여 요청을 미리 합니다. 사용자가 요청을 다시 시도 하기 전에 대기 해야 하는 시간 (밀리초)을 나타내는 [x-m 다시 시도-ms](/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) 헤더를 반환 합니다.

```http
HTTP Status 429,
Status Line: RequestRateTooLarge
x-ms-retry-after-ms :100
```

SDK는 이 응답을 암시적으로 모두 catch하고, server-specified retry-after 헤더를 준수하고 요청을 다시 시도합니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

하나 이상의 클라이언트가 누적 작업을 요청 빈도 이상에서 일관 되 게 작동 하는 경우 현재 클라이언트에서 내부적으로 9로 설정 된 기본 재시도 횟수는 충분 하지 않을 수 있습니다. 이 경우 클라이언트는 응용 프로그램에 상태 코드 429을 포함 하는 DocumentClientException을 throw 합니다. 

인스턴스에서를 설정 하 여 기본 재시도 횟수를 변경할 수 있습니다 `RetryOptions` `ConnectionPolicy` . 기본적으로, 요청이 계속 요청 속도 이상으로 작동하는 경우 상태 코드가 429인 DocumentClientException은 누적 대기 시간 30초 후에 반환됩니다. 현재 값이 기본값 9 또는 사용자 정의 값 인지 여부에 관계 없이 현재 재시도 횟수가 최대 재시도 횟수 보다 작은 경우에도이 오류가 반환 됩니다.

자동 재시도 동작은 대부분의 응용 프로그램에 대 한 복원 력 및 유용성을 개선 하는 데 도움이 됩니다. 하지만 특히 대기 시간을 측정 하는 경우 성능 벤치 마크를 수행 하는 경우에는 최상의 동작이 아닐 수 있습니다. 실험이 서버 제한에 도달하고 클라이언트 SDK를 자동으로 재시도하는 경우 클라이언트 관찰 대기 시간이 급증합니다. 성능 실험 중 대기 시간 급증을 방지하려면, 각 작업에 의해 반환된 비용을 측정하고 요청이 예약된 요청 속도 이하로 작동하고 있는지 확인합니다. 자세한 내용은 [요청 단위](request-units.md)를 참조 하세요.

**더 높은 처리량을 위해 더 작은 문서를 디자인 합니다.**

지정 된 작업의 요청 요금 (즉, 요청 처리 비용)은 문서 크기와 직접 연관 됩니다. 규모가 많은 문서에 대 한 작업은 작은 문서에 대 한 작업 보다 더 많은 비용이 듭니다.

## <a name="next-steps"></a>다음 단계

몇 개의 클라이언트 컴퓨터에서 고성능 시나리오에 대 한 Azure Cosmos DB를 평가 하는 데 사용 되는 샘플 응용 프로그램은 [Azure Cosmos DB을 사용한 성능 및 규모 테스트](performance-testing.md)를 참조 하세요.

확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partition-data.md)을 참조하세요.
