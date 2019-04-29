---
title: 자습서 - Node.js용 Azure Batch 클라이언트 라이브러리 사용 | Microsoft Docs
description: Azure Batch의 기본 개념을 알아보고 Node.js를 사용하여 간단한 솔루션을 빌드합니다.
services: batch
author: shwetams
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: nodejs
ms.topic: conceptual
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: shwetams
ms.openlocfilehash: bb0bfa5eac3dd9031718fb12f270f5fc03bbaea6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775233"
---
# <a name="get-started-with-batch-sdk-for-nodejs"></a>Node.js용 Batch SDK 시작

[Azure Batch Node.js SDK](/javascript/api/overview/azure/batch)를 사용하여 Node.js로 Batch 클라이언트를 빌드하는 기본 사항을 알아봅니다. 일괄 처리 애플리케이션에 대한 시나리오를 단계별로 이해한 다음 Node.js 클라이언트를 사용하여 설정해 보겠습니다.  

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 Node.js에 대한 작업 지식을 갖고 있으며 Linux에 익숙하다고 가정합니다. 또한 Batch 및 Storage 서비스를 만들 액세스 권한이 있는 Azure 계정을 갖고 있다고 가정합니다.

이 문서에 설명된 단계를 진행하기 전에 [Azure Batch 기술 개요](batch-technical-overview.md)를 읽어 보시기 바랍니다.

## <a name="the-tutorial-scenario"></a>자습서 시나리오
일괄 처리 워크플로 시나리오를 살펴보겠습니다. Azure Blob Storage 컨테이너에서 모든 csv 파일을 다운로드하여 JSON으로 변환하는 Python으로 작성된 간단한 스크립트가 있습니다. 여러 저장소 계정 컨테이너를 병렬로 처리하려면 스크립트를 Azure Batch 작업으로 배포하면 됩니다.

## <a name="azure-batch-architecture"></a>Azure Batch 아키텍처
다음 다이어그램은 Azure Batch 및 Node.js 클라이언트를 사용하여 Python 스크립트를 확장하는 방법을 보여 줍니다.

![Azure Batch 시나리오](./media/batch-nodejs-get-started/BatchScenario.png)

Node.js 클라이언트는 저장소 계정에 있는 컨테이너의 수에 따라 준비 작업(나중에 자세히 설명)과 작업 집합을 사용하여 일괄 처리 작업을 배포합니다. GitHub 리포지토리에서 스크립트를 다운로드할 수 있습니다.

* [Node.js 클라이언트](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/nodejs_batch_client_sample.js)
* [준비 작업 셸 스크립트](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/startup_prereq.sh)
* [Python csv-JSON 프로세서](https://github.com/Azure/azure-batch-samples/blob/master/Node.js/GettingStarted/processcsv.py)

> [!TIP]
> 지정된 링크의 Node.js 클라이언트는 Azure 함수 앱으로 배포될 특정 코드를 포함하고 있지 않습니다. 만드는 방법에 대한 지침은 다음 링크를 참조하세요.
> - [함수 앱 만들기](../azure-functions/functions-create-first-azure-function.md)
> - [타이머 트리거 함수 만들기](../azure-functions/functions-bindings-timer.md)
>
>

## <a name="build-the-application"></a>애플리케이션 빌드

이제 단계별 프로세스에 따라 Node.js 클라이언트를 빌드하겠습니다.

### <a name="step-1-install-azure-batch-sdk"></a>1단계: Azure Batch SDK 설치

npm install 명령을 사용하여 Node.js용 Azure Batch SDK를 설치할 수 있습니다.

`npm install azure-batch`

이 명령은 최신 버전의 azure-batch 노드 SDK를 설치합니다.

>[!Tip]
> Azure 함수 앱의 Azure 함수 설정 탭에서 "Kudu 콘솔"로 이동하여 npm install 명령을 실행합니다. 이 예에서는 Node.js용 Azure Batch SDK를 설치합니다.
>
>

### <a name="step-2-create-an-azure-batch-account"></a>2단계: Azure Batch 계정 만들기

[Azure Portal](batch-account-create-portal.md) 또는 명령줄([Powershell](batch-powershell-cmdlets-get-started.md) /[Azure cli](/cli/azure))에서 만들 수 있습니다.

다음은 Azure CLI를 통해 계정을 만드는 명령입니다.

Batch 계정을 만들 리소스가 이미 있는 경우 리소스 그룹을 만들고 이 단계를 건너뜁니다.

`az group create -n "<resource-group-name>" -l "<location>"`

다음으로 Azure Batch 계정을 만듭니다.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

각 Batch 계정에는 해당하는 액세스 키가 있습니다. 이러한 키는 Azure Batch 계정에 추가 리소스를 만드는 데 필요합니다. 프로덕션 환경의 경우 Azure Key Vault를 사용하여 이러한 키를 저장하는 것이 좋습니다. 그런 다음 애플리케이션의 서비스 주체를 만듭니다. 이 서비스 주체를 사용하여 애플리케이션에서 Key Vault의 키에 액세스하는 OAuth 토큰을 만들 수 있습니다.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

후속 단계에서 사용할 키를 복사하여 저장합니다.

### <a name="step-3-create-an-azure-batch-service-client"></a>3단계: Azure Batch 서비스 클라이언트 만들기
다음 코드 조각은 azure-batch Node.js 모듈을 가져온 후 Batch 서비스 클라이언트를 만듭니다. 먼저 이전 단계에서 복사한 Batch 계정 키로 SharedKeyCredentials 개체를 만들어야 합니다.

```nodejs
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Azure Batch URI는 Azure Portal의 개요 탭에서 찾을 수 있습니다. 다음과 같은 형식입니다.

`https://accountname.location.batch.azure.com`

스크린샷을 참조하세요.

![Azure batch uri](./media/batch-nodejs-get-started/azurebatchuri.png)



### <a name="step-4-create-an-azure-batch-pool"></a>4단계: Azure Batch 풀 만들기
Azure Batch 풀은 여러 VM(Batch 노드라고도 함)으로 구성됩니다. Azure Batch 서비스는 이러한 노드에 작업을 배포하고 관리합니다. 풀에 대해 다음 구성 매개 변수를 정의할 수 있습니다.

* Virtual Machine 이미지의 유형
* Virtual Machine 노드의 크기
* Virtual Machine 노드의 수

> [!Tip]
> Virtual Machine 노드의 크기와 수는 병렬로 실행하려는 작업의 수와 작업 자체에 따라 크게 달라집니다. 테스트를 통해 이상적인 수와 크기를 결정하는 것이 좋습니다.
>
>

다음은 구성 매개 변수 개체를 만드는 코드 조각입니다.

```nodejs
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!Tip]
> Azure Batch 및 SKU ID에 사용할 수 있는 Linux VM 이미지 목록은 [가상 머신 이미지 목록](batch-linux-nodes.md#list-of-virtual-machine-images)을 참조하세요.
>
>

풀 구성이 정의되면 Azure Batch 풀을 만들 수 있습니다. Batch 풀 명령은 Azure Virtual Machine 노드를 만들고, 실행할 작업을 수신할 수 있도록 노드를 준비합니다. 각 풀에는 후속 단계에서 참조할 고유 ID가 있어야 합니다.

다음은 Azure Batch 풀을 만드는 코드 조각입니다.

```nodejs
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

생성된 풀의 상태를 확인하고, 해당 풀에 작업을 제출하여 계속 진행하기 전에 상태를 "활성"으로 만들 수 있습니다.

```nodejs
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");    

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

다음은 pool.get 함수에서 반환하는 샘플 결과 개체입니다.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  maxTasksPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```


### <a name="step-4-submit-an-azure-batch-job"></a>4단계: Azure Batch 작업 제출
Azure Batch 작업은 유사한 작업의 논리적 그룹입니다. 이 시나리오에서는 "csv를 JSON으로 처리"입니다. 여기의 각 작업은 각 Azure Storage 컨테이너에 있는 csv 파일을 처리할 수 있습니다.

이러한 작업은 Azure Batch 서비스를 통해 병렬로 실행되어 여러 노드에 배포되고 조정됩니다.

> [!Tip]
> [maxTasksPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) 속성을 사용하여 단일 노드에서 동시에 실행할 수 있는 최대 작업 수를 지정할 수 있습니다.
>
>

#### <a name="preparation-task"></a>준비 작업

생성된 VM 노드는 빈 Ubuntu 노드입니다. 필수 구성 요소로 프로그램 집합을 설치해야 하는 경우가 자주 있습니다.
일반적으로 Linux 노드의 경우 실제 작업이 실행되기 전에 필수 구성 요소를 설치하는 셸 스크립트를 사용할 수 있습니다. 그러나 프로그래밍 방식의 실행 파일일 수 있습니다.
이 예제의 [셸 스크립트](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh)는 Python-pip 및 Python용 Azure Storage SDK를 설치합니다.

Azure Storage 계정에 스크립트를 업로드하고 해당 스크립트에 액세스하는 SAS URI를 생성할 수 있습니다. Azure Storage Node.js SDK를 사용하여 이 프로세스를 자동화할 수도 있습니다.

> [!Tip]
> 작업에 대한 준비 작업은 특정 작업을 실행해야 하는 VM 노드에서만 실행됩니다. 노드에서 실행되는 태스크와 관계없이 모든 노드에 필수 구성 요소를 설치하도록 하려면 풀을 추가하는 동안 [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) 속성을 사용하면 됩니다. 다음 준비 작업 정의를 참조에 사용할 수 있습니다.
>
>

준비 작업은 Azure Batch 작업이 제출되는 동안 지정됩니다. 다음은 준비 작업 구성 매개 변수입니다.

* **ID**: 준비 작업의 고유 식별자
* **commandLine**: 작업 실행 파일을 실행하는 명령줄
* **resourceFiles**: 이 작업을 실행하려면 다운로드해야 하는 파일의 세부 정보를 제공하는 개체 배열.  다음은 해당 옵션입니다.
    - blobSource: 파일의 SAS URI
    - filePath: 파일을 다운로드하여 저장할 로컬 경로
    - fileMode: Linux 노드에만 적용되는 fileMode는 8진수 형식이며 기본값은 0770
* **waitForSuccess**: true로 설정하면 준비 작업이 실패할 경우 작업이 실행되지 않음
* **runElevated**: 작업을 실행하려면 상승된 권한이 필요한 경우 true로 설정.

다음 코드 조각은 준비 작업 스크립트 구성 샘플을 보여 줍니다.

```nodejs
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

작업을 실행하기 위해 설치해야 할 필수 구성 요소가 없는 경우 준비 작업을 건너뛸 수 있습니다. 다음 코드는 표시 이름이 "csv 파일 처리"인 작업을 만듭니다.

 ```nodejs
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```


### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>5단계: 작업에 대한 Azure Batch 작업 제출

csv 처리 작업을 만들었으니, 해당 작업에 대한 작업을 만들겠습니다. 컨테이너가 4개 있다고 가정하고, 각 컨테이너에 하나씩 총 4개의 작업을 만들어야 합니다.

[Python 스크립트](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py)를 살펴보면, 두 개의 매개 변수가 허용됩니다.

* 컨테이너 이름: 파일을 다운로드할 Storage 컨테이너
* 패턴: 파일 이름 패턴의 선택적 매개 변수

"con1", "con2", "con3", "con4" 총 4개의 컨테이너가 있다고 가정할 때, 다음 코드는 앞에서 만든 Azure 일괄 처리 작업 "csv 처리"에 작업을 제출하는 방법을 보여 줍니다.

```nodejs
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){           

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);     
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

이 코드는 풀에 여러 작업을 추가합니다. 각 작업은 앞에서 만든 VM의 풀에 있는 노드에서 실행됩니다. 작업 수가 풀의 VM 수 또는 maxTasksPerNode 속성을 초과할 경우 노드를 사용할 수 있게 될 때까지 작업이 대기합니다. 이 오케스트레이션은 Azure Batch에서 자동으로 처리됩니다.

포털에 가면 구체적인 작업 상태 보기가 있습니다. Azure Node SDK에서 목록을 사용하여 함수를 가져올 수도 있습니다. 자세한 내용은 설명서 [링크](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html)에 제공됩니다.

## <a name="next-steps"></a>다음 단계

- 이 서비스를 처음 사용하는 경우 [Azure Batch 기능 개요](batch-api-basics.md) 문서를 검토하는 것이 좋습니다.
- Batch API에 대한 내용은 [Batch Node.js 참조](/javascript/api/overview/azure/batch)를 살펴보세요.

