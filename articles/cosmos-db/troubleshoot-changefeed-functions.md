---
title: Cosmos DB에 대해 Azure Functions 트리거를 사용할 때 발생 하는 문제 해결
description: Cosmos DB에 대한 Azure Functions 트리거를 사용 하는 경우 일반적인 문제, 해결 방법 및 진단 단계
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f3af350c96d1dd9eaf4773db503acb10d8a08a8f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441114"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB에 대해 Azure Functions 트리거를 사용 하는 경우 문제 진단 및 해결

이 문서에서는 [Cosmos DB에 대한 Azure Functions 트리거](change-feed-functions.md)를 사용 하는 경우 일반적인 문제, 해결 방법 및 진단 단계를 다룹니다.

## <a name="dependencies"></a>종속성

Cosmos DB에 대한 Azure Functions 트리거와 바인딩은 기본 Azure Functions 런타임에 대한 확장 패키지에 종속 됩니다. 발생할 수 있는 잠재적인 문제를 해결할 수 있는 수정 사항 및 새로운 기능을 포함할 수 있으므로 항상 이러한 패키지를 업데이트 된 상태로 유지 합니다.

* Azure Functions v 2의 경우 [WebJobs. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)를 참조 하세요.
* Azure Functions V1은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)를 참조 하세요.

이 문서는 명시적으로 지정 되지 않은 경우 런타임이 언급 될 때마다 항상 Azure Functions V2를 참조 합니다.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>독립적으로 Azure Cosmos DB SDK 사용

확장 패키지의 핵심 기능은 Cosmos DB에 대한 Azure Functions 트리거와 바인딩을 지원 하기 위한 것입니다. 여기에는 트리거와 바인딩을 사용 하지 않고 프로그래밍 방식으로 Azure Cosmos DB와 상호 작용 하려는 경우에 유용한 [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)도 포함 되어 있습니다.

Azure Cosmos DB SDK를 사용 하려면 다른 NuGet 패키지 참조를 프로젝트에 추가 하지 않아야 합니다. 대신 **Azure Functions의 확장 패키지를 통해 SDK 참조를 확인 하도록**합니다. 트리거 및 바인딩과 별도로 Azure Cosmos DB SDK 사용

또한 [AZURE COSMOS DB SDK 클라이언트](./sql-api-sdk-dotnet-core.md)의 고유한 인스턴스를 수동으로 만드는 경우 [단일 패턴 접근 방법을 사용 하 여](../azure-functions/manage-connections.md#documentclient-code-example-c)클라이언트의 인스턴스를 하나만 포함 하는 패턴을 따라야 합니다. 이 프로세스를 수행 하면 작업에서 발생할 수 있는 소켓 문제가 발생 하지 않습니다.

## <a name="common-scenarios-and-workarounds"></a>일반적인 시나리오 및 해결 방법

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>오류 메시지 수집이 없어 Azure Function이 실패 함

Azure Function이 실패 하 고 "원본 컬렉션 ' 컬렉션 이름 ' (데이터베이스 ' 데이터베이스-이름 ') 또는 임대 컬렉션 ' collection2-name ' (데이터베이스 ' database2 ')이 없습니다. 두 컬렉션 모두 수신기가 시작 되기 전에 존재 해야 합니다. 임대 컬렉션을 자동으로 만들려면 ' CreateLeaseCollectionIfNotExists '를 ' t r u e '로 설정 합니다.

즉, 트리거가 작동 하는 데 필요한 Azure Cosmos 컨테이너 중 하나 또는 둘 다가 존재 하지 않거나 Azure Function에 연결할 수 없습니다. 오류 자체는 사용자의 구성에 따라 **찾고 있는 Azure Cosmos database 및 컨테이너를 알려 줍니다** .

1. `ConnectionStringSetting` 특성을 확인 하 고 **Azure 함수 앱에 있는 설정을 참조**하는지 확인 합니다. 이 특성의 값은 연결 문자열 자체가 아니라 구성 설정의 이름입니다.
2. `databaseName` 및 `collectionName`가 Azure Cosmos 계정에 있는지 확인 합니다. `%settingName%` 패턴을 사용 하 여 자동 값 대체를 사용 하는 경우 설정의 이름이 Azure 함수 앱에 있는지 확인 합니다.
3. `LeaseCollectionName/leaseCollectionName`지정 하지 않으면 기본값은 "임대"입니다. 이러한 컨테이너가 있는지 확인 합니다. 필요에 따라 `true` 트리거에서 `CreateLeaseCollectionIfNotExists` 특성을 설정 하 여 자동으로 만들 수 있습니다.
4. Azure [Cosmos 계정의 방화벽 구성을](how-to-configure-firewall.md) 확인 하 여 azure 함수를 차단 하지 않는지 확인 합니다.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 함수는 "공유 처리량 수집에 파티션 키가 있어야 합니다."로 시작 되지 않습니다.

이전 버전의 Azure Cosmos DB 확장에서는 [공유 처리량 데이터베이스](./set-throughput.md#set-throughput-on-a-database)내에서 만들어진 임대 컨테이너를 사용 하는 것을 지원 하지 않았습니다. 이 문제를 해결 하려면 WebJobs를 업데이트 하 여 최신 버전을 받으세요. [CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 확장을 업데이트 합니다.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 함수는 "분할 된 경우 임대 컬렉션에 id와 동일한 파티션 키가 있어야 합니다."로 시작 되지 않습니다.

이 오류는 현재 임대 컨테이너가 분할 되었지만 파티션 키 경로가 `/id`되지 않았음을 의미 합니다. 이 문제를 해결 하려면 `/id`를 파티션 키로 사용 하 여 임대 컨테이너를 다시 만들어야 합니다.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>"값이 null 일 수 없습니다. 트리거를 실행 하려고 할 때 Azure Functions 로그의 매개 변수 이름: o "

이 문제는 Azure Portal를 사용 하 고 있으며 트리거를 사용 하는 Azure 함수를 검사할 때 화면에서 **실행** 단추를 선택 하려고 할 때 나타납니다. 트리거를 시작 하기 위해 실행을 선택할 필요는 없으며, Azure 함수를 배포할 때 자동으로 시작 됩니다. Azure Portal에서 Azure 함수의 로그 스트림을 확인 하려면 모니터링 된 컨테이너로 이동 하 여 일부 새 항목을 삽입 하면 트리거가 자동으로 실행 되는 것을 볼 수 있습니다.

### <a name="my-changes-take-too-long-to-be-received"></a>변경 내용을 수신 하는 데 너무 오래 걸립니다.

이 시나리오에는 여러 가지 원인이 있을 수 있으며이를 모두 확인 해야 합니다.

1. Azure 함수가 Azure Cosmos 계정과 동일한 지역에 배포되나요? 네트워크 대기 시간을 최적화하려면 Azure 함수와 Azure Cosmos 계정이 동일한 Azure 지역에 공동 배치되어야 합니다.
2. Azure Cosmos 컨테이너가 연속적으로 변경되나요? 간헐적으로 변경되나요?
후자인 경우 저장되는 변경 내용과 변경 내용을 선택하는 Azure 함수 사이에 약간의 지연이 있을 수 있습니다. 이는 내부적으로 트리거가 Azure Cosmos 컨테이너에서 변경 내용을 확인하여 읽기 보류 중인 항목이 없으면 새 변경 내용을 확인하기 전에 높은 RU 사용을 방지하기 위해 구성 가능한 시간(기본값: 5초) 동안 일시 중지하기 때문입니다. 트리거의 [구성](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)에서 `FeedPollDelay/feedPollDelay` 설정을 통해 이 일시 중지 시간을 구성할 수 있습니다(값은 밀리초 단위여야 함).
3. Azure Cosmos 컨테이너는 [rate가 제한](./request-units.md)될 수 있습니다.
4. 트리거에서 `PreferredLocations` 특성을 사용 하 여 쉼표로 구분 된 Azure 지역 목록을 지정 하 여 사용자 지정 기본 연결 순서를 정의할 수 있습니다.

### <a name="some-changes-are-missing-in-my-trigger"></a>일부 변경 내용이 내 트리거에서 누락 됨

Azure Cosmos 컨테이너에서 발생 한 변경 내용 중 일부를 Azure Function에서 선택 하지 않은 경우에는 초기 조사 단계를 수행 해야 합니다.

Azure 함수는 변경 내용을 받으면 자주 처리 하 고, 선택적으로 결과를 다른 대상으로 보낼 수 있습니다. 누락 된 변경 내용을 조사 하는 경우 수집 **지점에서 수신 되는 변경 내용** (Azure 함수가 시작 될 때)을 측정 하 여 대상이 아니라는 것을 측정 해야 합니다.

대상에 일부 변경 내용이 없는 경우에는 변경 내용이 수신 된 후 Azure 함수를 실행 하는 동안 오류가 발생 하는 것을 의미할 수 있습니다.

이 시나리오에서 가장 좋은 방법은 코드에 `try/catch` 블록을 추가 하 고, 변경 내용을 처리 하는 루프 내에 항목의 특정 하위 집합에 대한 오류를 검색 하 여 그에 따라 처리 하는 것입니다. 추가 분석을 위해 다른 저장소로 전송 하거나 다시 시도 하는 것이 좋습니다. 

> [!NOTE]
> 코드를 실행 하는 동안 처리 되지 않은 예외가 발생 하면 기본적으로 Cosmos DB에 대한 Azure Functions 트리거가 변경 내용 일괄 처리를 다시 시도 하지 않습니다. 즉, 변경 내용이 대상에 도착 하지 않은 이유는 처리에 실패 하기 때문입니다.

트리거에서 일부 변경 내용이 수신 되지 않은 경우 가장 일반적인 시나리오는 **다른 Azure 함수가 실행**되 고 있다는 것입니다. Azure에 배포 된 다른 Azure 함수 또는 **정확히 동일한 구성** (동일한 모니터링 및 임대 컨테이너)이 있는 개발자 컴퓨터에서 로컬로 실행 되는 azure 함수 일 수 있으며,이 azure 함수는 azure function에서 처리할 것으로 간주 되는 변경 내용의 하위 집합을 도용 합니다.

또한 실행 중인 Azure 함수 앱 인스턴스 수를 알고 있는 경우 시나리오의 유효성을 검사할 수 있습니다. 임대 컨테이너를 검사 하 고에서 임대 항목 수를 계산 하는 경우 `Owner` 속성의 고유 값은 함수 앱 인스턴스의 수와 같아야 합니다. 알려진 Azure 함수 앱 인스턴스보다 소유자가 더 많을 경우 추가 소유자는 변경 내용을 "도용하는" 소유자인 것을 의미합니다.

이러한 상황을 해결 하는 한 가지 쉬운 방법은 새 값 또는 다른 값을 사용 하 여 함수에 `LeaseCollectionPrefix/leaseCollectionPrefix`를 적용 하거나 새 임대 컨테이너를 사용 하 여 테스트 하는 것입니다.

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>처음부터 내 컨테이너의 모든 항목을 다시 시작 하 고 다시 처리 해야 합니다. 
컨테이너의 모든 항목을 처음부터 다시 처리 하려면 다음을 수행 합니다.
1. 현재 실행 중인 Azure 함수를 중지 합니다. 
1. 임대 컬렉션의 문서를 삭제 합니다 (또는 삭제 하 고 다시 만들어 임대 컬렉션을 삭제 한 후 다시 만들기).
1. 함수에서 [Startfrombeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) CosmosDBTrigger 특성을 true로 설정 합니다. 
1. Azure 함수를 다시 시작 합니다. 이제는 처음부터 모든 변경 내용을 읽고 처리 합니다. 

[Startfrombeginning](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) true로 설정 하면 Azure 함수는 현재 시간 대신 컬렉션의 기록 시작 부분에서 변경 내용 읽기를 시작 하도록 지시 합니다. 이는 아직 생성 된 임대가 없는 경우에만 작동 합니다 (즉, 임대 컬렉션에 문서). 이미 생성 된 임대가 있는 경우이 속성을 true로 설정 해도 아무런 효과가 없습니다. 이 시나리오에서는 함수가 중지 되었다가 다시 시작 될 때 임대 컬렉션에 정의 된 대로 마지막 검사점에서 읽기를 시작 합니다. 처음부터 다시 처리 하려면 위의 1-4 단계를 수행 합니다.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Binding은 IReadOnlyList\<Document > 또는 JArray로만 수행할 수 있습니다.

이 오류는 Azure Functions 프로젝트 또는 참조 된 프로젝트에 [Azure Functions Cosmos DB 확장](./troubleshoot-changefeed-functions.md#dependencies)에서 제공 하는 버전과 다른 버전의 Azure Cosmos DB SDK에 대한 수동 NuGet 참조가 포함 된 경우에 발생 합니다.

이러한 상황을 해결 하려면 추가 된 수동 NuGet 참조를 제거 하 고 Azure Functions Cosmos DB 확장 패키지를 통해 Azure Cosmos DB SDK 참조를 확인 하도록 합니다.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>변경 내용 검색에 대한 Azure 함수의 폴링 간격 변경

이전에 변경 된 [내용을 수신 하는 데 너무 오래 걸리므로](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)Azure function은 새 변경 내용을 확인 하기 전에 구성 가능한 시간 (기본적으로 5 초) 동안 대기 합니다. 트리거의 [구성](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)에서 `FeedPollDelay/feedPollDelay` 설정을 통해 이 일시 중지 시간을 구성할 수 있습니다(값은 밀리초 단위여야 함).

## <a name="next-steps"></a>다음 단계

* [Azure Functions 모니터링 사용](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 문제 해결](./troubleshoot-dot-net-sdk.md)
