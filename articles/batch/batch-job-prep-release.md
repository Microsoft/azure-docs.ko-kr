---
title: 계산 노드에서 작업을 준비하고 완료하는 태스크 만들기 - Azure Batch | Microsoft Docs
description: 작업 수준 준비 태스크를 사용하여 Azure Batch 계산 노드로의 데이터 전송을 최소화하고 작업 완료 시 태스크를 해제하여 노드를 정리합니다.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 02/27/2017
ms.date: 06/29/2018
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776211"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Batch 계산 노드에서 작업 준비 및 작업 릴리스 태스크 실행

 가끔씩 Azure Batch 작업에는 실행되기 전에 특정 형식으로 구성된 설정과 해당 작업이 완료된 이후의 사후 작업 유지 관리가 필요합니다. 즉 계산 노드에 공통 작업 입력 데이터를 다운로드하거나 해당 작업이 완료된 후 작업 출력 데이터를 Azure Storage에 업로드해야 할 수도 있습니다. **작업 준비** 및 **작업 해제**를 사용하면 이러한 작업을 수행할 수 있습니다.

## <a name="what-are-job-preparation-and-release-tasks"></a>작업 준비 및 해제 태스크에 대한 정의
작업 태스크가 실행되기 전에 하나 이상의 태스크를 실행하도록 예약된 모든 계산 노드에서 작업 준비 태스크가 실행됩니다. 작업이 완료되면 하나 이상의 태스크를 실행한 풀의 각 노드에서 작업 릴리스 태스크가 실행됩니다. 일반 Batch 태스크처럼 작업 준비 또는 해제 태스크가 실행될 때 호출되는 명령줄을 지정할 수 있습니다.

작업 준비 및 해제 태스크는 파일([리소스 파일][net_job_prep_resourcefiles]) 다운로드, 관리자 권한 실행, 사용자 지정 환경 변수, 최대 실행 기간, 재시도 횟수 및 파일 보존 시간과 같은 익숙한 Batch 태스크 기능을 제공합니다.

다음 섹션에서는 [Batch .NET][api_net] 라이브러리에 있는 [JobPreparationTask][net_job_prep]와 [JobReleaseTask][net_job_release] 클래스를 사용하는 방법을 알아봅니다.

> [!TIP]
> 계산 노드 풀이 작업 실행 간에 지속되고 여러 작업에서 사용되는 "공유 풀" 환경에서는 작업 준비 및 해제 태스크가 특히 유용합니다.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>작업 준비 및 해제 태스크를 사용하는 시점
작업 준비 및 작업 해제 태스크가 적합한 경우는 다음과 같습니다.

**공통 태스크 데이터 다운로드**

Batch 작업은 종종 작업의 태스크에 대한 입력으로 데이터의 공통 집합이 필요합니다. 예를 들어 일별 위험 분석 계산에서 시장 데이터는 작업 특정적이지만 작업의 모든 태스크에는 공통적입니다. 종종 수 기가바이트 크기인 시장 데이터는 각각의 계산 노드에 한 번만 다운로드되어 노드에서 실행하는 모든 태스크에서 사용할 수 있어야 합니다. **작업 준비 태스크** 를 사용하여 작업의 다른 태스크를 실행하기 전에 각 노드에 이 데이터를 다운로드합니다.

**작업 및 태스크 출력 삭제**

작업 간에 풀의 계산 노드 서비스를 해제하지 않는 "공유 풀" 환경에서 실행 간의 작업 데이터를 삭제해야 합니다. 노드의 디스크 공간을 절약하거나 조직의 보안 정책을 충족시킬 필요도 있습니다. **작업 해제 태스크** 를 사용하여 작업 준비 태스크에서 다운로드했거나 태스크를 실행하는 동안 생성한 데이터를 삭제합니다.

**로그 보존**

태스크가 생성한 로그 파일의 복사본을 유지하거나 실패한 애플리케이션에서 생성될 수 있는 덤프 파일의 작동을 중단할 수 있습니다. 이런 경우 **작업 릴리스 태스크**를 사용하여 [Azure Storage][azure_storage] 계정에 이 데이터를 압축하고 업로드합니다.

> [!TIP]
> 로그 및 기타 작업과 태스크 출력 데이터를 유지하는 다른 방법은 [Azure Batch File Conventions](batch-task-output.md) 라이브러리를 사용하는 것입니다.
> 
> 

## <a name="job-preparation-task"></a>작업 준비 태스크
작업의 태스크를 실행하기 전에 Batch에서는 작업을 실행하도록 예약된 각 계산 노드에서 작업 준비 태스크가 실행됩니다. 기본적으로 Batch 서비스는 노드에서 실행하도록 예약된 태스크를 실행하기 전에 작업 준비 태스크가 완료되기를 대기합니다. 그러나 서비스를 대기하지 않도록 구성할 수 있습니다. 노드가 다시 시작되면 작업 준비 태스크도 다시 실행되지만, 이 동작을 사용하지 않도록 설정할 수도 있습니다.

작업 준비 태스크는 태스크를 실행하도록 예약된 노드에서만 실행됩니다. 노드에 태스크를 할당하지 않은 경우 준비 태스크가 불필요하게 실행되지 않도록 방지합니다. 이는 작업에 대한 태스크 수가 풀의 노드 수보다 작은 경우에 발생할 수 있습니다. 이 방식은 [동시 태스크 실행](batch-parallel-node-tasks.md) 을 활성화할 때도 적용되며, 이는 태스크 개수가 가능한 총 동시 태스크 개수보다 작으면 노드 일부를 유휴 상태로 남겨둡니다. 유휴 노드에서 작업 준비 태스크를 실행하지 않으면 데이터 전송 요금에 적은 비용을 투자할 수 있습니다.

> [!NOTE]
> JobPreparationTask는 각 작업을 시작할 때 실행되지만 StartTask는 먼저 계산 노드를 풀과 조인하거나 다시 시작할 때만 실행된다는 점에서 [JobPreparationTask][net_job_prep_cloudjob]은 [CloudPool.StartTask][pool_starttask]와 다릅니다.
> 
> 

## <a name="job-release-task"></a>작업 해제 태스크
작업이 완료로 표시되면 하나 이상의 태스크를 실행한 풀의 각 노드에서 작업 릴리스 태스크가 실행됩니다. 종료 요청을 발행하여 작업을 완료로 표시합니다. 그런 다음 Batch 서비스는 작업 상태를 *terminating*으로 설정하며, 작업과 연관된 모든 활성 또는 실행 중인 태스크를 종료하고, 작업 해제 태스크를 실행합니다. 작업은 *완료* 상태로 이동합니다.

> [!NOTE]
> 또한 작업 삭제는 작업 릴리스 태스크를 실행합니다. 그러나 작업이 이미 종료되었고 나중에 삭제되는 경우에는 해제 태스크가 다시 실행되지 않습니다.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Batch .NET을 사용한 작업 준비 및 릴리스 태스크
작업 준비 태스크를 사용하려면 [JobPreparationTask][net_job_prep] 개체를 작업의 [CloudJob.JobPreparationTask][net_job_prep_cloudjob] 속성에 할당합니다. 마찬가지로 [JobReleaseTask][net_job_release]를 초기화하고 작업의 [CloudJob.JobReleaseTask][net_job_prep_cloudjob] 속성을 할당하여 작업의 릴리스 태스크를 설정합니다.

이 코드 조각에서 `myBatchClient`는 완전히 [BatchClient][net_batch_client]의 인스턴스이고 `myPool`은 Batch 계정에 있는 기존 풀입니다.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

위에서 설명했듯이 작업이 종료되거나 삭제될 때 해제 태스크가 실행됩니다. [JobOperations.TerminateJobAsync][net_job_terminate]를 통해 작업을 종료합니다. [JobOperations.DeleteJobAsync][net_job_delete]를 통해 작업을 삭제합니다. 일반적으로 작업의 태스크들이 완료되거나 정의한 시간 제한에 도달했을 때 작업을 종료하거나 삭제합니다.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>GitHub의 코드 샘플
작동 중인 작업 준비 및 해제 태스크를 보려면 GitHub의 [JobPrepRelease][job_prep_release_sample] 샘플 프로젝트를 확인합니다. 이 콘솔 애플리케이션은 다음을 수행합니다.

1. 노드가 두 개인 풀을 만듭니다.
2. 작업 준비, 릴리스 및 표준 태스크를 사용하여 작업을 만듭니다.
3. 먼저 노드 "공유" 디렉터리의 텍스트 파일에 노드 ID를 기록하는 작업 준비 태스크를 실행합니다.
4. 동일한 텍스트 파일에 해당 태스크 ID를 기록하는 각 노드에서 태스크를 실행합니다.
5. 모든 태스크가 완료되면(또는 시간 제한에 도달하면) 콘솔에 각 노드의 텍스트 파일에 있는 내용을 인쇄합니다.
6. 작업이 완료되면 작업 릴리스 태스크를 실행하여 노드에서 파일을 삭제합니다.
7. 실행된 각 노드에 대한 작업 준비 및 릴리스 태스크의 종료 코드를 인쇄합니다.
8. 실행을 일시 중지하여 작업 및/또는 풀 삭제를 확인합니다.

샘플 애플리케이션에서 출력은 다음과 비슷합니다.

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> 새 풀에 있는 노드의 변수 생성 및 시작 시간으로 인해(일부 노드는 다른 노드 전에 태스크에 대해 준비됨) 다른 출력이 표시될 수 있습니다. 특히, 태스크가 신속하게 완료되기 때문에 풀의 노드 중 하나에서 작업의 태스크를 모두 실행할 수 있습니다. 이러한 경우 작업을 실행하지 않은 노드에는 작업 준비 및 릴리스 태스크가 존재하지 않습니다.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Azure 포털에서 작업 준비 및 해제 태스크 검사
샘플 애플리케이션을 실행할 때 [Azure Portal][portal]을 사용하면 작업 및 해당 태스크의 속성을 확인하거나 작업의 태스크에서 수정한 공유 텍스트 파일을 다운로드할 수도 있습니다.

다음은 샘플 애플리케이션을 실행한 후에 Azure Portal에서 **준비 태스크 블레이드**를 보여 주는 스크린샷입니다. 작업이 완료되었지만 작업과 풀을 삭제하기 전에 *JobPrepReleaseSampleJob* 속성으로 이동하여 **준비 태스크** 또는 **해제 태스크**를 클릭하여 그 속성을 확인합니다.

![Azure 포털에서 작업 준비 속성][1]

## <a name="next-steps"></a>다음 단계
### <a name="application-packages"></a>애플리케이션 패키지
작업 준비 태스크 외에도 Batch의 [애플리케이션 패키지](batch-application-packages.md) 기능을 사용하여 태스크 실행을 위한 계산 노드를 준비할 수 있습니다. 이 기능은 설치 관리자를 실행하지 않아도 되는 애플리케이션, 100개 이상의 파일을 포함하는 애플리케이션 또는 엄격한 버전 제어를 필요로 하는 애플리케이션을 배포하는 데 특히 유용합니다.

### <a name="installing-applications-and-staging-data"></a>애플리케이션 설치 및 데이터 준비
아래의 MSDN 포럼 게시물에서는 작업을 실행하기 위해 노드를 준비하는 여러 가지 방법을 간략히 제공합니다.

[Batch 계산 노드에서의 애플리케이션 설치 및 데이터 스테이징][forum_post]

Azure Batch 팀 멤버 중 하나에서 작성하고 애플리케이션과 데이터를 계산 노드에 배포하는 데 사용할 수 있는 몇 가지 방법을 설명하고 있습니다.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://www.azure.cn/home/features/storage/
[portal]: https://portal.azure.cn
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

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

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png

<!-- Update_Description: wording update -->