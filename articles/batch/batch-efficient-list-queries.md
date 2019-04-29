---
title: 효율적인 목록 쿼리 디자인 - Azure Batch | Microsoft Docs
description: 풀, 작업, 태스크 및 계산 노드와 같은 Batch 리소스에 대한 정보를 요청할 때 쿼리를 필터링하여 성능을 향상시킵니다.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 031fefeb-248e-4d5a-9bc2-f07e46ddd30d
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: ff3e95a603b8f9a188c7839578cd12287935de90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60778257"
---
# <a name="create-queries-to-list-batch-resources-efficiently"></a>쿼리를 만들어서 효율적으로 Batch 리소스 나열

여기서는 [Batch .NET][api_net] 라이브러리를 사용하여 작업, 태스크, 계산 노드 및 기타 리소스를 쿼리할 때 서비스에서 반환되는 데이터의 양을 줄여 Azure Batch 애플리케이션의 성능을 향상시키는 방법을 알아봅니다.

거의 모든 Batch 애플리케이션은 정기적으로 Batch 서비스를 쿼리하는 특정 형식의 모니터링 또는 다른 작업을 수행해야 합니다. 예를 들어 대기 중인 태스크가 작업에 남아 있는지 여부를 확인하려면 작업의 모든 태스크에 대한 데이터를 가져와야 합니다. 풀의 노드 상태를 확인하려면 풀의 모든 노드에서 데이터를 가져와야 합니다. 이 문서에서는 가장 효율적인 방법으로 이러한 쿼리를 실행하는 방법에 대해 설명합니다.

> [!NOTE]
> Batch 서비스는 작업 내 태스크를 카운팅하고 Batch 풀의 계산 노드를 카운팅하는 일반적인 시나리오에 대한 특수 API 지원을 제공합니다. 목록 쿼리를 사용하는 대신, [Get Task Counts][rest_get_task_counts] 및 [List Pool Node Counts][rest_get_node_counts] 연산을 호출할 수 있습니다. 이러한 연산은 목록 쿼리보다 효율적이지만 제한된 정보를 반환합니다. [상태별 태스크 및 계산 노드 카운팅](batch-get-resource-counts.md)을 참조하세요. 


## <a name="meet-the-detaillevel"></a>DetailLevel 충족
프로덕션 Batch 애플리케이션에서 작업, 태스크 및 계산 노드 같은 엔터티가 수천 개 있을 수 있습니다. 이러한 리소스에 대한 정보를 요청할 때는 잠재적으로 각 쿼리에서 대량의 데이터를 Batch 서비스에서 애플리케이션으로 "아슬아슬하게 전달"해야 합니다. 쿼리에서 반환하는 항목의 수와 정보의 형식을 제한하여 쿼리의 속도를 높이고, 그 결과로 애플리케이션의 성능도 향상시킬 수 있습니다.

이 [Batch .NET][api_net] API 코드 조각은 각 태스크의 속성 *모두*와 함께 작업과 관련된 *모든* 태스크를 나열합니다.

```csharp
// Get a collection of all of the tasks and all of their properties for job-001
IPagedEnumerable<CloudTask> allTasks =
    batchClient.JobOperations.ListTasks("job-001");
```

그러나 쿼리에 "세부 수준"을 적용하여 훨씬 더 효율적인 목록 쿼리를 수행할 수 있습니다. [JobOperations.ListTasks][net_list_tasks] 메서드에 [ODATADetailLevel][odata] 개체를 제공하여 이를 수행합니다. 이 코드 조각은 완료된 태스크의 ID, 명령줄 및 계산 노드 정보 속성만 반환합니다.

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
> 애플리케이션의 최고 효율성과 성능을 위해 *항상* .NET API 목록 호출에 ODATADetailLevel 개체를 제공하는 것이 좋습니다. 세부 정보 수준을 지정하여 Batch 서비스 응답 시간을 낮추고, 네트워크 사용률을 개선하며 클라이언트 애플리케이션의 메모리 사용을 최소화하는 데 도움이 될 수 있습니다.
> 
> 

## <a name="filter-select-and-expand"></a>Filter, select 및 expand
[Batch .NET][api_net] 및 [Batch REST][api_rest] API는 목록에 반환되는 항목 수와 각각에 대해 반환되는 정보의 크기를 줄일 수 있습니다. 이렇게 하려면 목록 쿼리를 수행할 때 **filter**, **select** 및 **expand 문자열**을 지정합니다.

### <a name="filter"></a>Filter
filter 문자열은 반환되는 항목 수를 줄이는 식입니다. 예를 들어, 한 작업에 대해 실행 중인 태스크만 나열하거나 태스크를 실행할 준비가 된 계산 노드만 나열할 수 있습니다.

* filter 문자열은 하나 이상의 식으로 구성된 문자열로 구성됩니다. 식은 속성 이름, 연산자, 값으로 이루어져 있습니다. 각 속성에 대해 지원되는 연산자의 경우처럼 지정할 수 있는 속성은 쿼리한 항목 마다 고유합니다.
* `and` 및 `or` 논리 연산자를 사용하면 여러 식을 결합할 수 있습니다.
* `(state eq 'running') and startswith(id, 'renderTask')` filter 문자열 예제는 실행 중인 "렌더링" 태스크만 나열합니다.

### <a name="select"></a>여기서
select 문자열은 각 항목에 대해 반환되는 속성 값을 제한합니다. 속성 이름의 목록을 지정하고 해당 속성 값은 쿼리 결과의 항목에 대해 반환됩니다.

* select 문자열은 속성 이름을 쉼표로 구분한 목록으로 구성됩니다. 쿼리 중인 엔터티 형식에 대한 속성을 지정할 수 있습니다.
* `id, state, stateTransitionTime` select 문자열 예제는 각 태스크에 대해 반환되어야 하는 세 가지 속성 값만 지정하고 있습니다.

### <a name="expand"></a>Expand
expand 문자열은 특정 정보를 얻는 데 필요한 API 호출 수를 줄입니다. expand 문자열을 사용하면 단일 API 호출로 각 항목에 대한 자세한 정보를 얻을 수 있습니다. 엔터티의 목록을 첫 번째로 가져오기 보다 목록에서 각 항목에 대한 정보를 요청하여 단일 API 호출에서 동일한 정보를 얻기 위해 확장 문자열을 사용할 수 있습니다. API 호출이 적어지면 성능이 향상되었음을 의미합니다.

* select 문자열과 마찬가지로 expand 문자열은 목록 쿼리 결과에서 특정 데이터의 포함 여부를 제어합니다.
* expand 문자열은 작업 목록, 작업 일정, 태스크 및 풀에서 사용되는 경우에만 지원됩니다. 현재 통계 정보만 지원합니다.
* 모든 속성이 필요하며 select 문자열을 지정하지 않은 경우, 통계 정보를 가져오려면 expand 문자열을 사용 *해야 합니다* . select 문자열을 사용하여 속성 하위 집합을 가져올 경우 select 문자열에서 `stats` 를 지정할 수 있으며 expand 문자열을 지정하지 않아도 됩니다.
* `stats` expand 문자열 예제는 목록에서 각 항목에 대해 반환되어야 하는 통계 정보를 지정하고 있습니다.

> [!NOTE]
> 세 가지 쿼리 문자열 형식(filter, select 및 expand) 중 하나를 구성할 때 속성 이름 및 사례가 해당 REST API 요소와 일치해야 합니다. 예를 들어 .NET [CloudTask](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask) 클래스를 사용하는 경우 .NET 속성이 [CloudTask.State](/dotnet/api/microsoft.azure.batch.cloudtask#microsoft_azure_batch_cloudtask.state)이더라도 **State** 대신 **state**를 지정해야 합니다. .NET 및 REST API 간의 속성 매핑은 아래 표를 참조하세요.
> 
> 

### <a name="rules-for-filter-select-and-expand-strings"></a>filter, select, expand 문자열 규칙
* [Batch .NET][api_net] 또는 다른 Batch SDK 중 하나를 사용하는 경우에도 filter, select, expand 문자열의 속성 이름은 [Batch REST][api_rest] API에서와 동일하게 나타나야 합니다.
* 모든 속성 이름은 대/소문자를 구분하지만, 속성 값은 대/소문자를 구분하지 않습니다.
* 날짜/시간 문자열은 두 형식 중 하나가 될 수 있으며 `DateTime`으로 시작해야 합니다.
  
  * W3C-DTF 형식 예제: `creationTime gt DateTime'2011-05-08T08:49:37Z'`
  * RFC 1123 형식 예제: `creationTime gt DateTime'Sun, 08 May 2011 08:49:37 GMT'`
* 부울 문자열은 `true` 또는 `false`입니다.
* 잘못된 속성이나 연산자를 지정한 경우 `400 (Bad Request)` 오류가 발생합니다.

## <a name="efficient-querying-in-batch-net"></a>Batch .NET에서 효율적인 쿼리
[Batch .NET][api_net] API 안에서 [ODATADetailLevel][odata] 클래스를 filter, select 및 expand 문자열에 사용하여 작업을 나열합니다. ODataDetailLevel 클래스에는 생성자 내에 지정되어 있거나 개체에 직접 설정된 세 개의 공용 문자열 속성이 있습니다. ODataDetailLevel 개체를 [ListPools][net_list_pools], [ListJobs][net_list_jobs] 및 [ListTasks][net_list_tasks]와 같은 다양한 목록 작업에 매개 변수로 전달합니다.

* [ODATADetailLevel][odata].[FilterClause][odata_filter]: 반환되는 항목 수를 제한합니다.
* [ODATADetailLevel][odata].[SelectClause][odata_select]: 각 항목에 반환되는 속성 값을 지정합니다.
* [ODATADetailLevel][odata].[ExpandClause][odata_expand]: 각 항목의 별도 호출 대신 단일 API 호출의 모든 항목에 대한 데이터를 검색합니다.

다음 코드 조각에서는 풀의 특정 집합에 대한 통계에 대해 Batch 서비스를 효율적으로 쿼리하기 위해 Batch .NET API를 사용합니다. 이 시나리오에서 Batch 사용자는 테스트 및 프로덕션 풀을 가집니다. 테스트 풀 ID는 "test"를 접두사로 사용하고 프로덕션 풀 ID는 "prod"를 접두사로 사용합니다. 이 코드 조각에서 *myBatchClient* 는 다음과 같은 [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient#microsoft_azure_batch_batchclient) 클래스의 인스턴스를 적절하게 초기화합니다.

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
> Select 및 Expand 절로 구성된 [ODATADetailLevel][odata]의 인스턴스는 반환되는 데이터의 양을 제한하기 위해 [PoolOperations.GetPool](/dotnet/api/microsoft.azure.batch.pooloperations#Microsoft_Azure_Batch_PoolOperations_GetPool_System_String_Microsoft_Azure_Batch_DetailLevel_System_Collections_Generic_IEnumerable_Microsoft_Azure_Batch_BatchClientBehavior__)과 같은 적절한 Get 메서드에 전달할 수도 있습니다.
> 
> 

## <a name="batch-rest-to-net-api-mappings"></a>Batch REST를 .NET API에 매핑
filter, select 및 expand 문자열의 속성 이름은 이름과 대소문자 모두 해당 REST API 항목을 반영 *해야 합니다* . 다음 표는 .NET과 REST API 간의 매핑을 제공합니다.

### <a name="mappings-for-filter-strings"></a>filter 문자열 매핑
* **.NET 목록 메서드**: 이 열의 각 .NET API 메서드는 [ODATADetailLevel][odata] 개체를 매개 변수로 수락합니다.
* **REST 목록 요청**: 이 열에 연결된 각 REST API 페이지에는 *filter* 문자열에서 허용되는 속성과 연산을 지정하는 테이블이 들어 있습니다. [ODATADetailLevel.FilterClause][odata_filter] 문자열을 구성할 때 이 속성 이름과 작업을 사용합니다.

| .NET 목록 메서드 | REST 목록 요청 |
| --- | --- |
| [CertificateOperations.ListCertificates][net_list_certs] |[계정에 인증서 나열][rest_list_certs] |
| [CloudTask.ListNodeFiles][net_list_task_files] |[태스크와 연관된 파일 나열][rest_list_task_files] |
| [JobOperations.ListJobPreparationAndReleaseTaskStatus][net_list_jobprep_status] |[작업 준비 및 작업에 대한 작업 릴리스 태스크의 상태 나열][rest_list_jobprep_status] |
| [JobOperations.ListJobs][net_list_jobs] |[계정에 작업 나열][rest_list_jobs] |
| [JobOperations.ListNodeFiles][net_list_nodefiles] |[노드에 파일 나열][rest_list_nodefiles] |
| [JobOperations.ListTasks][net_list_tasks] |[작업과 연관된 태스크 나열][rest_list_tasks] |
| [JobScheduleOperations.ListJobSchedules][net_list_job_schedules] |[계정에 작업 일정 나열][rest_list_job_schedules] |
| [JobScheduleOperations.ListJobs][net_list_schedule_jobs] |[작업 일정과 연관된 작업 나열][rest_list_schedule_jobs] |
| [PoolOperations.ListComputeNodes][net_list_compute_nodes] |[풀에 계산 노드 나열][rest_list_compute_nodes] |
| [PoolOperations.ListPools][net_list_pools] |[계정에 풀 나열][rest_list_pools] |

### <a name="mappings-for-select-strings"></a>select 문자열 매핑
* **Batch .NET 형식**: Batch .NET API 형식입니다.
* **REST API 엔터티**: 이 열의 각 페이지에는 형식에 대한 REST API 속성 이름을 나열하는 하나 이상의 표가 들어 있습니다. 이러한 속성 이름은 *select* 문자열을 구성할 때 사용됩니다. [ODATADetailLevel.SelectClause][odata_select] 문자열을 구성할 때 이와 동일한 속성을 사용합니다.

| Batch .NET 형식 | REST API 엔터티 |
| --- | --- |
| [Certificate][net_cert] |[인증서 정보 가져오기][rest_get_cert] |
| [CloudJob][net_job] |[작업 정보 가져오기][rest_get_job] |
| [CloudJobSchedule][net_schedule] |[작업 일정 정보 가져오기][rest_get_schedule] |
| [ComputeNode][net_node] |[노드 정보 가져오기][rest_get_node] |
| [CloudPool][net_pool] |[풀 정보 가져오기][rest_get_pool] |
| [CloudTask][net_task] |[태스크 정보 가져오기][rest_get_task] |

## <a name="example-construct-a-filter-string"></a>예: filter 문자열 구성
[ODATADetailLevel.FilterClause][odata_filter]에 대한 filter 문자열을 구성할 때는 "filter 문자열에 대한 매핑"에서 위의 표를 참조하여 수행하려는 목록 작업에 해당하는 REST API 설명서 페이지를 찾을 수 있습니다. 해당 페이지의 첫 번째 다중 행 표에 필터링 가능한 속성과 지원되는 연산자가 있습니다. 예를 들어, 종료 코드가 0이 아닌 모든 태스크를 검색하려는 경우 [작업과 연결된 태스크 목록][rest_list_tasks]의 이 행은 적용 가능한 속성 문자열과 허용 가능한 연산자를 지정합니다.

| 자산 | 허용되는 연산 | Type |
|:--- |:--- |:--- |
| `executionInfo/exitCode` |`eq, ge, gt, le , lt` |`Int` |

따라서 종료 코드가 0이 아닌 모든 작업을 나열하기 위한 filter 문자열은 다음과 같게 됩니다.

`(executionInfo/exitCode lt 0) or (executionInfo/exitCode gt 0)`

## <a name="example-construct-a-select-string"></a>예: select 문자열 구성
[ODATADetailLevel.SelectClause][odata_select]를 구성하려면 "select 문자열에 대한 매핑"에서 위의 표를 참조하고 나열하는 엔터티 형식에 해당하는 REST API 페이지로 이동합니다. 해당 페이지의 첫 번째 다중 행 표에 선택 가능한 속성과 지원되는 연산자가 있습니다. 목록의 각 태스크에 대해 ID와 명령줄만 검색하려면 [태스크 관련 정보 가져오기][rest_get_task]의 해당하는 표에서 이 행을 찾을 수 있습니다.

| 자산 | Type | 메모 |
|:--- |:--- |:--- |
| `id` |`String` |`The ID of the task.` |
| `commandLine` |`String` |`The command line of the task.` |

목록의 각 태스크와 함께 ID와 명령줄만 포함하기 위한 select 문자열은 다음과 같게 됩니다.

`id, commandLine`

## <a name="code-samples"></a>코드 샘플
### <a name="efficient-list-queries-code-sample"></a>효율적인 목록 쿼리 코드 샘플
GitHub의 [EfficientListQueries][efficient_query_sample] 샘플 프로젝트를 확인하여 애플리케이션에서 효율적인 목록 쿼리가 성능에 미치는 영향을 확인할 수 있습니다. 이 C# 콘솔 애플리케이션은 많은 작업을 만들고 또 작업에 추가합니다. 그런 다음 [JobOperations.ListTasks][net_list_tasks] 메서드에 대한 여러 호출을 만들고 다른 속성 값으로 구성된 [ODATADetailLevel][odata] 개체를 전달하여 반환될 데이터의 크기를 다양화합니다. 다음과 유사한 출력을 생성합니다.

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

경과된 시간에서 보는 것처럼 반환되는 항목 수와 속성을 제한하여 쿼리 응답 시간을 크게 낮출 수 있습니다. 이 샘플 프로젝트와 다른 샘플 프로젝트가 GitHub의 [azure-batch-samples][github_samples] 리포지토리에 있습니다.

### <a name="batchmetrics-library-and-code-sample"></a>BatchMetrics 라이브러리 및 코드 샘플
위의 EfficientListQueries 코드 샘플 외에도 [azure-batch-samples][github_samples] GitHub 리포지토리에서 [BatchMetrics][batch_metrics] 프로젝트를 찾을 수 있습니다. BatchMetrics 샘플 프로젝트는 배치 API를 사용하여 Azure Batch 작업 진행률을 효율적으로 모니터링하는 방법을 보여 줍니다.

[BatchMetrics][batch_metrics] 샘플은 자체 프로젝트로 통합할 수 있는 .NET 클래스 라이브러리 프로젝트 및 라이브러리의 사용을 연습하고 설명하는 간단한 명령줄 프로그램을 포함합니다.

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
### <a name="parallel-node-tasks"></a>병렬 노드 작업
[동시 노드 작업으로 Azure Batch 계산 리소스 사용 극대화](batch-parallel-node-tasks.md)는 배치 애플리케이션 성능과 관련된 다른 문서입니다. 일부 유형의 작업은 더 크지만 더 적은 계산 노드에서의 병렬 작업 실행에서 이점을 얻을 수 있습니다. 이러한 시나리오에 대한 자세한 내용은 문서의 [예제 시나리오](batch-parallel-node-tasks.md#example-scenario) 를 확인하세요.


[api_net]: https://docs.microsoft.com/dotnet/api/microsoft.azure.batch
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_metrics]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchMetrics
[efficient_query_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/EfficientListQueries
[github_samples]: https://github.com/Azure/azure-batch-samples
[odata]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[odata_ctor]: https://msdn.microsoft.com/library/azure/dn866178.aspx
[odata_expand]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.expandclause.aspx
[odata_filter]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.filterclause.aspx
[odata_select]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.selectclause.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[rest_list_certs]: https://msdn.microsoft.com/library/azure/dn820154.aspx
[rest_list_compute_nodes]: https://msdn.microsoft.com/library/azure/dn820159.aspx
[rest_list_job_schedules]: https://msdn.microsoft.com/library/azure/mt282174.aspx
[rest_list_jobprep_status]: https://msdn.microsoft.com/library/azure/mt282170.aspx
[rest_list_jobs]: https://msdn.microsoft.com/library/azure/dn820117.aspx
[rest_list_nodefiles]: https://msdn.microsoft.com/library/azure/dn820151.aspx
[rest_list_pools]: https://msdn.microsoft.com/library/azure/dn820101.aspx
[rest_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/mt282169.aspx
[rest_list_task_files]: https://msdn.microsoft.com/library/azure/dn820142.aspx
[rest_list_tasks]: https://msdn.microsoft.com/library/azure/dn820187.aspx

[rest_get_cert]: https://msdn.microsoft.com/library/azure/dn820176.aspx
[rest_get_job]: https://msdn.microsoft.com/library/azure/dn820106.aspx
[rest_get_node]: https://msdn.microsoft.com/library/azure/dn820168.aspx
[rest_get_pool]: https://msdn.microsoft.com/library/azure/dn820165.aspx
[rest_get_schedule]: https://msdn.microsoft.com/library/azure/mt282171.aspx
[rest_get_task]: https://msdn.microsoft.com/library/azure/dn820133.aspx

[net_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificate.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_schedule]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjobschedule.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx

[rest_get_task_counts]: /rest/api/batchservice/get-the-task-counts-for-a-job
[rest_get_node_counts]: /rest/api/batchservice/account/listpoolnodecounts
