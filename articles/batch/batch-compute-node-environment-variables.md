---
title: 컴퓨팅 노드 환경 변수 - Azure Batch | Microsoft Docs
description: Azure Batch 분석에 대한 Compute 노드 환경 변수 참조입니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/07/2019
ms.author: lahugh
ms.openlocfilehash: 9902f38ddfd3035adcce697c2eb5b77bdc1d8c9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782234"
---
# <a name="azure-batch-compute-node-environment-variables"></a>Azure Batch 계산 노드 환경 변수

[Azure Batch 서비스](https://azure.microsoft.com/services/batch/)는 계산 노드에 다음과 같은 환경 변수를 설정합니다. 태스크 명령줄과 명령줄로 실행되는 프로그램 및 스크립트에서 이러한 환경 변수를 참조할 수 있습니다.

Batch에 환경 변수를 사용하는 방법에 대한 자세한 내용은 [태스크에 대한 환경 설정](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks)을 참조하세요.

## <a name="environment-variable-visibility"></a>환경 변수 이름 표시

이러한 환경 변수는 **태스크 사용자**, 즉 태스크가 실행되는 노드의 사용자 계정의 컨텍스트에서만 표시됩니다. RDP(원격 데스크톱 프로토콜) 또는 SSH(Secure Shell)를 통해 계산 노드에 *원격으로 연결*하고 환경 변수를 나열하는 경우 이를 확인할 수 [없습니다](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) . 원격 연결에 사용되는 사용자 계정이 태스크에서 사용하는 계정과 동일하지 않기 때문입니다.

## <a name="command-line-expansion-of-environment-variables"></a>환경 변수의 명령줄 확장

계산 노드에서 태스크로 실행되는 명령줄은 셸에서 실행되지 않습니다. 따라서 이러한 명령줄은 기본적으로 환경 변수 확장과 같은 셸 기능을 활용할 수 없습니다(`PATH` 포함). 이러한 기능을 활용하려면 명령줄에서 **셸을 호출**해야 합니다. 예를 들어 Windows 계산 노드에서 `cmd.exe`를 실행하거나 Linux 노드에서 `/bin/sh`를 실행합니다.

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>환경 변수

| 변수 이름                     | 설명                                                              | 가용성 | 예 |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | 태스크가 속한 Batch 계정의 이름입니다.                  | 모든 태스크입니다.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | 배치 계정의 URL입니다. | 모든 태스크입니다. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | 모든 앱 패키지 환경 변수의 접두사입니다. 예를 들어 애플리케이션 “Foo” 버전 “1”이 풀에 설치된 경우 환경 변수는 AZ_BATCH_APP_PACKAGE_FOO_1입니다. AZ_BATCH_APP_PACKAGE_FOO_1은 패키지가 다운로드된 위치(폴더)를 가리킵니다. | 관련 앱 패키지가 있는 모든 작업입니다. 또한 노드 자체에 애플리케이션 패키지가 있는 경우 모든 작업에 사용할 수 있습니다. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Batch 서비스 작업의 제한된 집합에 대한 액세스를 부여하는 인증 토큰입니다. 이 환경 변수는 [작업이 추가](/rest/api/batchservice/task/add#request-body)될 때 [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings)가 설정된 경우에만 존재합니다. 토큰 값은 [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)와 같은 Batch API에서 Batch 클라이언트를 만들 때 자격 증명으로 사용됩니다. | 모든 태스크입니다. | OAuth2 액세스 토큰 |
| AZ_BATCH_CERTIFICATES_DIR       | Linux 계산 노드에 대한 인증서가 저장된 [태스크 작업 디렉터리][files_dirs] 내 디렉터리입니다. 이 환경 변수는 Windows 계산 노드에 적용되지 않습니다.                                                  | 모든 태스크입니다.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | [다중 인스턴스 태스크][multi_instance]에 할당된 노드의 목록으로, `nodeIP,nodeIP` 형식입니다. | 다중 인스턴스 기본 및 하위 태스크입니다. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | 현재 노드가 [다중 인스턴스 태스크][multi_instance]의 마스터 노드인지 여부를 지정합니다. 가능한 값은 `true` 및 `false`입니다.| 다중 인스턴스 기본 및 하위 태스크입니다. | `true` |
| AZ_BATCH_JOB_ID                 | 태스크가 속한 작업의 ID | 시작 태스크를 제외한 모든 태스크입니다. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | 노드의 작업 준비 [태스크 디렉터리][files_dirs] 전체 경로입니다. | 시작 태스크 및 작업 준비 태스크를 제외한 모든 태스크입니다. 작업에 작업 준비 태스크가 구성된 경우에만 사용할 수 있습니다. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | 노드의 작업 준비 [태스크 작업 디렉터리][files_dirs] 전체 경로입니다. | 시작 태스크 및 작업 준비 태스크를 제외한 모든 태스크입니다. 작업에 작업 준비 태스크가 구성된 경우에만 사용할 수 있습니다. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | [다중 인스턴스 태스크][multi_instance]의 기본 태스크가 실행되는 계산 노드의 IP 주소와 포트입니다. | 다중 인스턴스 기본 및 하위 태스크입니다. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | 태스크가 할당된 노드의 ID입니다. | 모든 태스크입니다. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | `true`의 경우 현재 노드는 전용된 노드입니다. `false`의 경우 [우선 순위가 낮은 노드](batch-low-pri-vms.md)입니다. | 모든 태스크입니다. | `true` |
| AZ_BATCH_NODE_LIST              | [다중 인스턴스 태스크][multi_instance]에 할당된 노드의 목록으로, `nodeIP;nodeIP` 형식입니다. | 다중 인스턴스 기본 및 하위 태스크입니다. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_ROOT_DIR          | 노드의 모든 [Batch 디렉터리][files_dirs] 루트의 전체 경로입니다. | 모든 태스크입니다. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | 노드의 [공유 디렉터리][files_dirs] 전체 경로입니다. 노드에서 실행되는 모든 태스크는 이 디렉터리에 대한 읽기/쓰기 권한이 있습니다. 다른 노드에서 실행되는 태스크는 이 디렉터리에 대한 원격 액세스 권한이 없습니다("공유" 네트워크 디렉터리가 아님). | 모든 태스크입니다. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | 노드의 [시작 태스크 디렉터리][files_dirs] 전체 경로입니다. | 모든 태스크입니다. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | 태스크가 실행되는 풀의 ID | 모든 태스크입니다. | batchpool001 |
| AZ_BATCH_TASK_DIR               | 노드의 [태스크 디렉터리][files_dirs] 전체 경로입니다. 이 디렉터리에는 태스크에 대한 `stdout.txt` 및 `stderr.txt`, 그리고 AZ_BATCH_TASK_WORKING_DIR이 포함됩니다. | 모든 태스크입니다. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | 현재 태스크의 ID | 시작 태스크를 제외한 모든 태스크입니다. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | [다중 인스턴스 태스크][multi_instance]의 기본 태스크 및 모든 하위 태스크일 경우 동일한 디렉터리 경로입니다. 경로는 다중 인스턴스 태스크가 실행되며 해당 노드에서 실행 중인 태스크 명령에 읽기/쓰기 액세스 권한을 가진 모든 노드에 존재합니다([조정 명령][coord_cmd]과 [애플리케이션 명령][app_cmd] 둘 다). 다른 노드에서 실행되는 하위 태스크 또는 기본 태스크는 이 디렉터리에 대한 원격 액세스 권한이 없습니다(“공유” 네트워크 디렉터리가 아님). | 다중 인스턴스 기본 및 하위 태스크입니다. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | 노드의 [태스크 작업 디렉터리][files_dirs] 전체 경로입니다. 현재 실행 중인 태스크는 이 디렉터리에 대한 읽기/쓰기 액세스 권한이 있습니다. | 모든 태스크입니다. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | [다중 인스턴스 태스크][multi_instance]에 할당된 노드와 노드당 코어 수의 목록입니다. 노드 및 코어는 `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...` 형식으로 나열됩니다. 여기서 노드 수 뒤에 하나 이상의 노드 IP 주소와 각 노드의 코어 수가 옵니다. |  다중 인스턴스 기본 및 하위 태스크입니다. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
