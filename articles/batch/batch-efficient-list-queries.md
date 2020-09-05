---
title: 효율적인 목록 쿼리 설계
description: 풀, 작업, 태스크 및 컴퓨팅 노드와 같은 Batch 리소스에 대한 정보를 요청할 때 쿼리를 필터링하여 성능을 향상시킵니다.
ms.topic: how-to
ms.date: 06/18/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3a767cc8ae3c8c48e1e40e0735c33fa807ba0015
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933517"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>쿼리를 만들어서 효율적으로 Batch 리소스 나열

거의 모든 Batch 애플리케이션은 정기적으로 Batch 서비스를 쿼리하는 특정 형식의 모니터링 또는 다른 작업을 수행해야 합니다. 예를 들어 대기 중인 태스크가 작업에 남아 있는지 여부를 확인하려면 작업의 모든 태스크에 대한 데이터를 가져와야 합니다. 풀의 노드 상태를 확인하려면 풀의 모든 노드에서 데이터를 가져와야 합니다. 이 문서에서는 가장 효율적인 방법으로 이러한 쿼리를 실행하는 방법에 대해 설명합니다.

[Batch .net](/dotnet/api/microsoft.azure.batch) 라이브러리를 사용 하 여 작업, 태스크, 계산 노드 및 기타 리소스를 쿼리할 때 서비스에서 반환 되는 데이터의 양을 줄여 응용 프로그램의 성능을 Azure Batch 향상 시킬 수 있습니다.

> [!NOTE]
> Batch 서비스는 작업의 태스크 수를 계산 하 고 Batch 풀에서 계산 노드를 계산 하는 일반적인 시나리오에 대 한 API 지원을 제공 합니다. 목록 쿼리를 사용하는 대신, [Get Task Counts](/rest/api/batchservice/job/gettaskcounts) 및 [List Pool Node Counts](/rest/api/batchservice/account/listpoolnodecounts) 연산을 호출할 수 있습니다. 이러한 작업은 목록 쿼리 보다 더 효율적 이지만 항상 최신 상태가 아닐 수 있는 더 제한적인 정보를 반환 합니다. 자세한 내용은 [상태별 작업 및 계산 노드 수](batch-get-resource-counts.md)를 참조 하세요.

## <a name="specify-a-detail-level"></a>세부 정보 수준 지정

프로덕션 Batch 애플리케이션에서 작업, 태스크 및 컴퓨팅 노드 같은 엔터티가 수천 개 있을 수 있습니다. 이러한 리소스에 대한 정보를 요청할 때는 잠재적으로 각 쿼리에서 대량의 데이터를 Batch 서비스에서 애플리케이션으로 "아슬아슬하게 전달"해야 합니다. 쿼리에서 반환하는 항목의 수와 정보의 형식을 제한하여 쿼리의 속도를 높이고, 그 결과로 애플리케이션의 성능도 향상시킬 수 있습니다.

이 [Batch .NET](/dotnet/api/microsoft.azure.batch) API 코드 조각은 각 태스크의 속성 ‘모두’와 함께 작업과 관련된 ‘모든’ 태스크를 나열합니다. 

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

그러나 쿼리에 "세부 수준"을 적용하여 훨씬 더 효율적인 목록 쿼리를 수행할 수 있습니다. [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) 메서드에 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 개체를 제공하여 이를 수행합니다. 이 코드 조각은 완료된 태스크의 ID, 명령줄 및 컴퓨팅 노드 정보 속성만 반환합니다.

```csharp
// Configure an ODATADetailLevel specifying a subset of tasks and
// their properties to return
ODATADetailLevel detailLevel = new ODATADetailLevel();
detailLevel.FilterClause = "state eq 'completed'";
detailLevel.SelectClause = "id,commandLine,nodeInfo";

// Supply the ODATADetailLevel to the ListTasks method
IPagedEnumerable<CloudTask> completedTasks =
    batchClient.JobOperations.ListTasks("job-001", detailLevel);
```

이 예제 시나리오에서 수천 개의 태스크가 작업에 있다면 두 번째 쿼리의 결과는 대개 첫 번째 결과보다 더 빨리 반환됩니다. Batch .NET API를 사용하여 항목을 나열할 때 ODATADetailLevel 사용에 대한 자세한 내용은 [아래](#efficient-querying-in-batch-net)에 포함되어 있습니다.

> [!IMPORTANT]
> 애플리케이션의 최고 효율성과 성능을 위해 항상 .NET API 목록 호출에 ODATADetailLevel 개체를 제공하는 것이 좋습니다. 세부 정보 수준을 지정하여 Batch 서비스 응답 시간을 낮추고, 네트워크 사용률을 개선하며 클라이언트 애플리케이션의 메모리 사용을 최소화하는 데 도움이 될 수 있습니다.

## <a name="filter-select-and-expand"></a>Filter, select 및 expand

[Batch .NET](/dotnet/api/microsoft.azure.batch) 및 [Batch REST](/rest/api/batchservice/) API는 목록에 반환되는 항목 수와 각각에 대해 반환되는 정보의 크기를 줄일 수 있습니다. 이렇게 하려면 목록 쿼리를 수행할 때 **filter**, **select** 및 **expand 문자열**을 지정합니다.

### <a name="filter"></a>Assert

filter 문자열은 반환되는 항목 수를 줄이는 식입니다. 예를 들어 작업에 대해 실행 중인 작업만 나열 하거나 작업을 실행할 준비가 된 계산 노드만 나열할 수 있습니다.

filter 문자열은 하나 이상의 식으로 구성된 문자열로 구성됩니다. 식은 속성 이름, 연산자, 값으로 이루어져 있습니다. 각 속성에 대해 지원되는 연산자의 경우처럼 지정할 수 있는 속성은 쿼리한 항목 마다 고유합니다.  `and` 및 `or` 논리 연산자를 사용하면 여러 식을 결합할 수 있습니다.

`(state eq 'running') and startswith(id, 'renderTask')` filter 문자열 예제는 실행 중인 "렌더링" 태스크만 나열합니다.

### <a name="select"></a>선택

select 문자열은 각 항목에 대해 반환되는 속성 값을 제한합니다. 쉼표로 구분 된 속성 이름 목록을 지정 하면 쿼리 결과의 항목에 대해 해당 속성 값만 반환 됩니다. 쿼리 하는 엔터티 형식에 대 한 속성을 지정할 수 있습니다.

`id, state, stateTransitionTime` select 문자열 예제는 각 태스크에 대해 반환되어야 하는 세 가지 속성 값만 지정하고 있습니다.

### <a name="expand"></a>Expand

expand 문자열은 특정 정보를 얻는 데 필요한 API 호출 수를 줄입니다. expand 문자열을 사용하면 단일 API 호출로 각 항목에 대한 자세한 정보를 얻을 수 있습니다. 먼저 엔터티 목록을 가져온 다음 목록의 각 항목에 대 한 정보를 요청 하는 대신 확장 문자열을 사용 하 여 단일 API 호출에서 동일한 정보를 가져와 API 호출을 줄임으로써 성능을 향상 시킬 수 있습니다.

select 문자열과 마찬가지로 expand 문자열은 목록 쿼리 결과에서 특정 데이터의 포함 여부를 제어합니다. 모든 속성이 필요하며 select 문자열을 지정하지 않은 경우, 통계 정보를 가져오려면 expand 문자열을 사용 *해야 합니다* . select 문자열을 사용하여 속성 하위 집합을 가져올 경우 select 문자열에서 `stats` 를 지정할 수 있으며 expand 문자열을 지정하지 않아도 됩니다.

expand 문자열은 작업 목록, 작업 일정, 태스크 및 풀에서 사용되는 경우에만 지원됩니다. 현재 통계 정보만 지원합니다.

`stats` expand 문자열 예제는 목록에서 각 항목에 대해 반환되어야 하는 통계 정보를 지정하고 있습니다.

> [!NOTE]
> 세 가지 쿼리 문자열 형식(filter, select 및 expand) 중 하나를 구성할 때 속성 이름 및 사례가 해당 REST API 요소와 일치해야 합니다. 예를 들어 .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) 클래스를 사용하는 경우 .NET 속성이 [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask.state#Microsoft_Azure_Batch_CloudTask_State)이더라도 **State** 대신 **state**를 지정해야 합니다. .NET 및 REST API 간의 속성 매핑은 아래 표를 참조하세요.

### <a name="rules-for-filter-select-and-expand-strings"></a>filter, select, expand 문자열 규칙

- Batch [.net](/dotnet/api/microsoft.azure.batch) 또는 다른 batch sdk 중 하나를 사용 하는 경우에도 filter, select 및 expand 문자열의 속성 이름은 [batch REST](/rest/api/batchservice/) API에서와 동일 하 게 나타나야 합니다.
- 모든 속성 이름은 대/소문자를 구분하지만, 속성 값은 대/소문자를 구분하지 않습니다.
- 날짜/시간 문자열은 두 형식 중 하나가 될 수 있으며 `DateTime`으로 시작해야 합니다.
  
  - W3C-DTF 형식 예제: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  - RFC 1123 형식 예제: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
- 부울 문자열은 `true` 또는 `false`입니다.
- 잘못된 속성이나 연산자를 지정한 경우 `400 (Bad Request)` 오류가 발생합니다.

## <a name="efficient-querying-in-batch-net"></a>Batch .NET에서 효율적인 쿼리

[Batch .NET](/dotnet/api/microsoft.azure.batch) API 안에서 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 클래스를 filter, select 및 expand 문자열에 사용하여 작업을 나열합니다. ODataDetailLevel 클래스에는 생성자 내에 지정되어 있거나 개체에 직접 설정된 세 개의 공용 문자열 속성이 있습니다. ODataDetailLevel 개체를 [ListPools](/dotnet/api/microsoft.azure.batch.pooloperations), [ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) 및 [ListTasks](/dotnet/api/microsoft.azure.batch.joboperations)와 같은 다양한 목록 작업에 매개 변수로 전달합니다.

- [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause): 반환 되는 항목 수를 제한 합니다.
- [ODATADetailLevel 절](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause): 각 항목에 대해 반환 되는 속성 값을 지정 합니다.
- [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel.expandclause): 각 항목에 대 한 별도의 호출 대신 단일 API 호출의 모든 항목에 대 한 데이터를 검색 합니다.

다음 코드 조각에서는 풀의 특정 집합에 대한 통계에 대해 Batch 서비스를 효율적으로 쿼리하기 위해 Batch .NET API를 사용합니다. 이 시나리오에서 Batch 사용자는 테스트 및 프로덕션 풀을 가집니다. 테스트 풀 ID는 "test"를 접두사로 사용하고 프로덕션 풀 ID는 "prod"를 접두사로 사용합니다. 이 코드 조각에서 *myBatchClient* 는 다음과 같은 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient) 클래스의 인스턴스를 적절하게 초기화합니다.

```csharp
// First we need an ODATADetailLevel instance on which to set the filter, select,
// and expand clause strings
ODATADetailLevel detailLevel = new ODATADetailLevel();

// We want to pull only the "test" pools, so we limit the number of items returned
// by using a FilterClause and specifying that the pool IDs must start with "test"
detailLevel.FilterClause = "startswith(id, 'test')";

// To further limit the data that crosses the wire, configure the SelectClause to
// limit the properties that are returned on each CloudPool object to only
// CloudPool.Id and CloudPool.Statistics
detailLevel.SelectClause = "id, stats";

// Specify the ExpandClause so that the .NET API pulls the statistics for the
// CloudPools in a single underlying REST API call. Note that we use the pool's
// REST API element name "stats" here as opposed to "Statistics" as it appears in
// the .NET API (CloudPool.Statistics)
detailLevel.ExpandClause = "stats";

// Now get our collection of pools, minimizing the amount of data that is returned
// by specifying the detail level that we configured above
List<CloudPool> testPools =
    await myBatchClient.PoolOperations.ListPools(detailLevel).ToListAsync();
```

> [!TIP]
> Select 및 Expand 절로 구성된 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel)의 인스턴스는 반환되는 데이터의 양을 제한하기 위해 [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations.getpool#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)과 같은 적절한 Get 메서드에 전달할 수도 있습니다.

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST를 .NET API에 매핑

filter, select 및 expand 문자열의 속성 이름은 이름과 대소문자 모두 해당 REST API 항목을 반영 해야 합니다 . 다음 표는 .NET과 REST API 간의 매핑을 제공합니다.

### <a name="mappings-for-filter-strings"></a>filter 문자열 매핑

- **.NET 목록 메서드**: 이 열의 각 .NET API 메서드는 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 개체를 매개 변수로 수락합니다.
- **REST 목록 요청**: 이 열에 연결된 각 REST API 페이지에는 *filter* 문자열에서 허용되는 속성과 연산을 지정하는 테이블이 들어 있습니다. [ODATADetailLevel 절](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause) 문자열을 생성할 때 이러한 속성 이름 및 작업을 사용 합니다.

| .NET 목록 메서드 | REST 목록 요청 |
| --- | --- |
| [CertificateOperations.ListCertificates](/dotnet/api/microsoft.azure.batch.certificateoperations) |[계정에 인증서 나열](/rest/api/batchservice/certificate/list) |
| [CloudTask.ListNodeFiles](/dotnet/api/microsoft.azure.batch.cloudtask) |[작업과 연관된 파일 나열](/rest/api/batchservice/file/listfromtask) |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus](/dotnet/api/microsoft.azure.batch.joboperations) |[작업 준비 및 작업에 대한 작업 릴리스 태스크의 상태 나열](/rest/api/batchservice/job/listpreparationandreleasetaskstatus) |
| [JobOperations.ListJobs](/dotnet/api/microsoft.azure.batch.joboperations) |[계정에 작업 나열](/rest/api/batchservice/job/list) |
| [JobOperations.ListNodeFiles](/dotnet/api/microsoft.azure.batch.joboperations) |[노드에 파일 나열](/rest/api/batchservice/file/listfromcomputenode) |
| [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) |[작업과 연관된 태스크 나열](/rest/api/batchservice/task/list) |
| [JobScheduleOperations.ListJobSchedules](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[계정에 작업 일정 나열](/rest/api/batchservice/jobschedule/list) |
| [JobScheduleOperations.ListJobs](/dotnet/api/microsoft.azure.batch.jobscheduleoperations) |[작업 일정과 연관된 작업 나열](/rest/api/batchservice/job/listfromjobschedule) |
| [PoolOperations.ListComputeNodes](/dotnet/api/microsoft.azure.batch.pooloperations) |[풀에 컴퓨팅 노드 나열](/rest/api/batchservice/computenode/list) |
| [PoolOperations.ListPools](/dotnet/api/microsoft.azure.batch.pooloperations) |[계정에 풀 나열](/rest/api/batchservice/pool/list) |

### <a name="mappings-for-select-strings"></a>select 문자열 매핑

- **Batch .NET 형식**: Batch .NET API 형식입니다.
- **REST API 엔터티**: 이 열의 각 페이지에는 형식에 대한 REST API 속성 이름을 나열하는 하나 이상의 표가 들어 있습니다. 이러한 속성 이름은 *select* 문자열을 구성할 때 사용됩니다. [ODATADetailLevel 절](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause) 문자열을 생성할 때 이와 같은 속성 이름을 사용 합니다.

| Batch .NET 형식 | REST API 엔터티 |
| --- | --- |
| [MSSQLSERVER에 대한 프로토콜 속성](/dotnet/api/microsoft.azure.batch.certificate) |[인증서 정보 가져오기](/rest/api/batchservice/certificate/get) |
| [CloudJob](/dotnet/api/microsoft.azure.batch.cloudjob) |[작업 정보 가져오기](/rest/api/batchservice/job/get) |
| [CloudJobSchedule](/dotnet/api/microsoft.azure.batch.cloudjobschedule) |[작업 일정 정보 가져오기](/rest/api/batchservice/jobschedule/get) |
| [ComputeNode](/dotnet/api/microsoft.azure.batch.computenode) |[노드 정보 가져오기](/rest/api/batchservice/computenode/get) |
| [CloudPool](/dotnet/api/microsoft.azure.batch.cloudpool) |[풀 정보 가져오기](/rest/api/batchservice/pool/get) |
| [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask) |[작업 정보 가져오기](/rest/api/batchservice/task/get) |

## <a name="example-construct-a-filter-string"></a>예: filter 문자열 구성

[ODATADetailLevel.FilterClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.filterclause)에 대한 filter 문자열을 구성할 때는 “filter 문자열에 대한 매핑”에서 위의 표를 참조하여 수행하려는 목록 작업에 해당하는 REST API 설명서 페이지를 찾을 수 있습니다. 해당 페이지의 첫 번째 다중 행 표에 필터링 가능한 속성과 지원되는 연산자가 있습니다. 예를 들어, 종료 코드가 0이 아닌 모든 태스크를 검색하려는 경우 [작업과 연결된 태스크 목록](/rest/api/batchservice/task/list)의 이 행은 적용 가능한 속성 문자열과 허용 가능한 연산자를 지정합니다.

| 속성 | 허용되는 연산 | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

따라서 종료 코드가 0이 아닌 모든 작업을 나열하기 위한 filter 문자열은 다음과 같게 됩니다.

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>예: select 문자열 구성

[ODATADetailLevel.SelectClause](/dotnet/api/microsoft.azure.batch.odatadetaillevel.selectclause)를 구성하려면 “select 문자열에 대한 매핑”에서 위의 표를 참조하고 나열하는 엔터티 형식에 해당하는 REST API 페이지로 이동합니다. 해당 페이지의 첫 번째 다중 행 표에 선택 가능한 속성과 지원되는 연산자가 있습니다. 목록의 각 작업에 대해 ID와 명령줄만 검색하려면 [작업 관련 정보 가져오기](/rest/api/batchservice/task/get)의 해당하는 표에서 이 행을 찾을 수 있습니다.

| 속성 | Type | 메모 |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

목록의 각 태스크와 함께 ID와 명령줄만 포함하기 위한 select 문자열은 다음과 같게 됩니다.

`id, commandLine`

## <a name="code-samples"></a>코드 샘플

### <a name="efficient-list-queries-code-sample"></a>효율적인 목록 쿼리 코드 샘플

GitHub의 [EfficientListQueries](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries) 샘플 프로젝트에서는 효율적인 목록 쿼리가 응용 프로그램의 성능에 미치는 영향을 보여 줍니다. 이 C# 콘솔 애플리케이션은 많은 작업을 만들고 또 작업에 추가합니다. 그런 다음 [JobOperations.ListTasks](/dotnet/api/microsoft.azure.batch.joboperations) 메서드에 대한 여러 호출을 만들고 다른 속성 값으로 구성된 [ODATADetailLevel](/dotnet/api/microsoft.azure.batch.odatadetaillevel) 개체를 전달하여 반환될 데이터의 크기를 다양화합니다. 다음과 유사한 출력을 생성합니다.

```
Adding 5000 tasks to job jobEffQuery...
5000 tasks added in 00:00:47.3467587, hit ENTER to query tasks...

4943 tasks retrieved in 00:00:04.3408081 (ExpandClause:  | FilterClause: state eq 'active' | SelectClause: id,state)
0 tasks retrieved in 00:00:00.2662920 (ExpandClause:  | FilterClause: state eq 'running' | SelectClause: id,state)
59 tasks retrieved in 00:00:00.3337760 (ExpandClause:  | FilterClause: state eq 'completed' | SelectClause: id,state)
5000 tasks retrieved in 00:00:04.1429881 (ExpandClause:  | FilterClause:  | SelectClause: id,state)
5000 tasks retrieved in 00:00:15.1016127 (ExpandClause:  | FilterClause:  | SelectClause: id,state,environmentSettings)
5000 tasks retrieved in 00:00:17.0548145 (ExpandClause: stats | FilterClause:  | SelectClause: )

Sample complete, hit ENTER to continue...
```

경과된 시간에서 보는 것처럼 반환되는 항목 수와 속성을 제한하여 쿼리 응답 시간을 크게 낮출 수 있습니다. 이 샘플 프로젝트와 다른 샘플 프로젝트가 GitHub의 [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples) 리포지토리에 있습니다.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics 라이브러리 및 코드 샘플

위의 EfficientListQueries 코드 샘플 외에도 [Batchmetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) 샘플 프로젝트는 Batch API를 사용 하 여 Azure Batch 작업 진행률을 효율적으로 모니터링 하는 방법을 보여 줍니다.

[BatchMetrics](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/BatchMetrics) 샘플은 자체 프로젝트로 통합할 수 있는 .NET 클래스 라이브러리 프로젝트 및 라이브러리의 사용을 연습하고 설명하는 간단한 명령줄 프로그램을 포함합니다.

프로젝트 내의 샘플 애플리케이션은 다음 작업을 보여 줍니다.

1. 필요한 속성에 대해서만 다운로드하기 위해 특정 특성 선택
2. 마지막 쿼리 이후에 변경 내용만 다운로드하기 위해 상태 전환 시간에서 필터링

예를 들어 다음 메서드는 BatchMetrics 라이브러리에 나타납니다. 쿼리되는 엔터티에 대해 `id` 및 `state` 속성만 가져와야 한다고 지정하는 ODATADetailLevel을 반환합니다. 또한 지정된 `DateTime` 매개 변수로 인해 상태가 변경된 엔터티만을 반환해야 한다고 지정합니다.

```csharp
internal static ODATADetailLevel OnlyChangedAfter(DateTime time)
{
    return new ODATADetailLevel(
        selectClause: "id, state",
        filterClause: string.Format("stateTransitionTime gt DateTime'{0:o}'", time)
    );
}
```

## <a name="next-steps"></a>다음 단계

- [동시 노드 작업을 사용 하 여 리소스 사용을 극대화 Azure Batch](batch-parallel-node-tasks.md)하는 방법에 대해 알아봅니다. 일부 유형의 작업에서는 더 크고 더 작은 계산 노드에서 병렬 작업을 실행 하는 것이 유용할 수 있습니다. 이러한 시나리오에 대한 자세한 내용은 문서의 [예제 시나리오](batch-parallel-node-tasks.md#example-scenario) 를 확인하세요.
- [상태별로 태스크 및 노드 수를 계산 하 여 Batch 솔루션을 모니터링](batch-get-resource-counts.md) 하는 방법을 알아봅니다.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_net_listjobs]: /dotnet/api/microsoft.azure.batch.joboperations
[api_rest]: /rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_ctor]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_expand]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_filter]: /dotnet/api/microsoft.azure.batch.odatadetaillevel
[odata_select]: /dotnet/api/microsoft.azure.batch.odatadetaillevel

[net_list_certs]: /dotnet/api/microsoft.azure.batch.certificateoperations
[net_list_compute_nodes]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_job_schedules]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_jobprep_status]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_jobs]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_nodefiles]: /dotnet/api/microsoft.azure.batch.joboperations
[net_list_pools]: /dotnet/api/microsoft.azure.batch.pooloperations
[net_list_schedule_jobs]: /dotnet/api/microsoft.azure.batch.jobscheduleoperations
[net_list_task_files]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_list_tasks]: /dotnet/api/microsoft.azure.batch.joboperations

[rest_list_certs]: /rest/api/batchservice/certificate/list
[rest_list_compute_nodes]: /rest/api/batchservice/computenode/list
[rest_list_job_schedules]: /rest/api/batchservice/jobschedule/list
[rest_list_jobprep_status]: /rest/api/batchservice/job/listpreparationandreleasetaskstatus
[rest_list_jobs]: /rest/api/batchservice/job/list
[rest_list_nodefiles]: /rest/api/batchservice/file/listfromcomputenode
[rest_list_pools]: /rest/api/batchservice/pool/list
[rest_list_schedule_jobs]: /rest/api/batchservice/job/listfromjobschedule
[rest_list_task_files]: /rest/api/batchservice/file/listfromtask
[rest_list_tasks]: /rest/api/batchservice/task/list

[rest_get_cert]: /rest/api/batchservice/certificate/get
[rest_get_job]: /rest/api/batchservice/job/get
[rest_get_node]: /rest/api/batchservice/computenode/get
[rest_get_pool]: /rest/api/batchservice/pool/get
[rest_get_schedule]: /rest/api/batchservice/jobschedule/get
[rest_get_task]: /rest/api/batchservice/task/get

[net_cert]: /dotnet/api/microsoft.azure.batch.certificate
[net_job]: /dotnet/api/microsoft.azure.batch.cloudjob
[net_node]: /dotnet/api/microsoft.azure.batch.computenode
[net_pool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_schedule]: /dotnet/api/microsoft.azure.batch.cloudjobschedule
[net_task]: /dotnet/api/microsoft.azure.batch.cloudtask

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
