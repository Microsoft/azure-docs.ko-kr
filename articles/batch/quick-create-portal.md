---
title: Azure 빠른 시작 - Batch 작업 실행 - 포털
description: Azure Portal을 사용하여 Batch 계정, 컴퓨팅 노드 풀 및 풀에서 기본 작업을 실행하는 만드는 방법을 알아봅니다.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: labrenne
ms.custom: mvc
ms.openlocfilehash: 8d725834cb2dd86163909b2ae598e61026ae4bb9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024112"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 첫 번째 Batch 작업 실행

이 빠른 시작에서는 Azure Portal을 사용하여 배치 계정, 컴퓨팅 노드(가상 머신)의 *풀* 및 풀에서 기본 *태스크*를 실행하는 *작업*을 만드는 방법을 보여 줍니다. 이 빠른 시작을 완료하면, Batch 서비스의 주요 개념을 이해하고 더 큰 규모의 더 실제적인 작업으로 Batch를 시도할 준비가 됩니다.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인 

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-batch-account"></a>Batch 계정 만들기

다음 단계에 따라 테스트용 배치 계정 샘플을 만듭니다. 풀 및 작업을 만들려면 배치 계정이 필요합니다. 여기서 보여 주듯이 Azure Storage 계정과 배치 계정을 연결할 수 있습니다. 스토리지 계정은 이 빠른 시작에서 필요하지 않지만, 애플리케이션을 배포하고 대부분의 실제 작업에 대한 입력 및 출력 데이터를 저장하는 데 유용합니다.


1. **리소스 만들기** > **계산** > **Batch Service**를 선택합니다. 

   ![Marketplace에서 Batch][marketplace_portal]

2. **계정 이름** 및 **리소스 그룹**에 대한 값을 입력합니다. 계정 이름은 선택한 Azure **위치** 내에서 고유해야 하며, 소문자 또는 숫자만 사용하고, 3-24자를 포함해야 합니다. 

3. **스토리지 계정**에서 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만듭니다.

4. 나머지 설정에 대한 기본값은 그대로 유지하고 **만들기**를 선택하여 계정을 만듭니다.

   ![Batch 계정 만들기][account_portal]  

**배포 성공** 메시지가 표시되면 포털의 배치 계정으로 이동합니다.

## <a name="create-a-pool-of-compute-nodes"></a>컴퓨팅 노드 풀 만들기

이제 배치 계정이 있으므로 테스트를 위해 Windows 컴퓨팅 노드의 샘플 풀을 만듭니다. 이 빠른 예제의 풀은 Azure Marketplace에서 Windows Server 2012 R2 이미지를 실행하는 2개의 노드로 구성되어 있습니다.


1. 배치 계정에서 **풀** > **추가**를 차례로 선택합니다.

2. *mypool*이라는 **풀 ID**를 입력합니다. 

3. **운영 체제**에서 다음 설정을 선택합니다(다른 옵션을 탐색할 수 있음).
  
   |설정  |값  |
   |---------|---------|
   |**이미지 형식**|Marketplace(Linux/Windows)|
   |**게시자**     |MicrosoftWindowsServer|
   |**제안**     |WindowsServer|
   |**Sku**     |2012-R2-Datacenter-smalldisk|

   ![풀 운영 체제 선택][pool_os] 

4. 아래로 스크롤하여 **노드 크기** 및 **배율** 설정을 입력합니다. 제안된 노드 크기는 이 빠른 예제의 성능과 비용에 대한 적절한 균형을 제공합니다.
  
   |설정  |값  |
   |---------|---------|
   |**노드 가격 책정 계층**     |Standard_A1|
   |**대상 전용 노드**     |2|

   ![풀 크기 선택][pool_size] 

5. 나머지 설정은 기본값으로 유지하고 **확인**을 선택하여 풀을 만듭니다.

Batch는 풀을 즉시 만들지만, 컴퓨팅 노드를 할당하고 시작하는 데 몇 분이 걸립니다. 이 시간 동안 풀의 **할당 상태**는 **Resizing**(크기 조정 중)입니다. 풀 크기를 조정하는 동안 작업과 태스크를 계속 만들 수 있습니다. 

![Resizing 상태의 풀][pool_resizing]

몇 분 후에 풀의 상태는 **Steady**(안정)가 되며 노드가 시작됩니다. **노드**를 선택하여 노드의 상태를 확인합니다. 노드의 상태가 **Idle**(유휴)이면 태스크를 실행할 준비가 되었습니다. 

## <a name="create-a-job"></a>작업 만들기

이제 풀이 있으므로 여기에서 실행할 작업을 만들 수 있습니다. Batch 작업은 하나 이상의 태스크에 대한 논리적 그룹입니다. 작업에는 우선 순위 및 태스크를 실행할 풀과 같은 태스크에 공통적으로 적용되는 설정이 포함됩니다. 처음에는 작업에 태스크가 없습니다. 

1. 배치 계정 보기에서 **작업** > **추가**를 선택합니다. 

2. *myjob*이라는 **작업 ID**를 입력합니다. **풀**에서 *mypool*을 선택합니다. 나머지 설정은 기본값으로 유지하고 **확인**을 선택합니다.

   ![작업 만들기][job_create]

작업이 만들어지면 **태스크** 페이지가 열립니다.

## <a name="create-tasks"></a>태스크 만들기

이제 작업에서 실행할 샘플 태스크를 만듭니다. 일반적으로 컴퓨팅 노드에서 실행하기 위해 Batch에서 큐에 넣고 배포하는 여러 태스크를 만듭니다. 이 예제에서는 두 개의 동일한 태스크를 만듭니다. 각 태스크는 명령줄을 실행하여 컴퓨팅 노드에 Batch 환경 변수를 표시한 다음, 90초 동안 기다립니다. 

Batch를 사용하면 명령줄에서 앱 또는 스크립트를 지정합니다. Batch는 컴퓨팅 노드에 앱과 스크립트를 배포하는 여러 가지 방법을 제공합니다. 

첫 번째 태스크를 만들려면 다음을 수행합니다.

1. **추가**를 선택합니다.

2. *mytask*라는 **태스크 ID**를 입력합니다. 

3. **명령줄**에 `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"`을 입력합니다. 나머지 설정은 기본값으로 유지하고 **확인**을 선택합니다.

   ![작업을 만듭니다.][task_create]

태스크가 만들어지면 Batch는 풀에서 실행되도록 해당 태스크를 큐에 넣습니다. 노드에서 실행할 수 있게 되면 태스크가 실행됩니다.

두 번째 태스크를 만들려면 1단계로 다시 이동합니다. 다른 **태스크 ID**를 입력하고 동일한 명령줄을 지정합니다. 첫 번째 태스크가 아직 실행 중이면 Batch는 풀의 다른 노드에서 두 번째 태스크를 시작합니다.

## <a name="view-task-output"></a>태스크 출력 보기

앞의 태스크 예제는 몇 분 내에 완료됩니다. 완료된 작업의 출력을 보려면 **노드에 있는 파일**을 선택하고 `stdout.txt` 파일을 선택합니다. 이 파일은 태스크의 표준 출력을 표시합니다. 내용은 다음과 비슷합니다.

![태스크 출력 보기][task_output]

내용에는 노드에 설정된 Azure Batch 환경 변수가 표시됩니다. 사용자 고유의 Batch 작업 및 태스크를 만들 때, 태스크 명령줄과 이 명령줄에서 실행되는 앱 및 스크립트에서 이러한 환경 변수를 참조할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

Batch 자습서 및 샘플을 계속 사용하려면 이 빠른 시작에서 만든 배치 계정 및 연결된 스토리지 계정을 사용합니다. 배치 계정 자체에는 요금이 부과되지 않습니다.

작업이 예약되지 않은 경우에도 노드가 실행되는 동안은 풀에 대한 요금이 부과됩니다. 풀이 더 이상 필요하지 않으면 삭제합니다. 계정 보기에서 **풀**과 해당 풀의 이름을 선택합니다. 그런 다음, **삭제**를 선택합니다.  풀을 삭제하면 노드의 모든 태스크 출력이 삭제됩니다. 

더 이상 필요하지 않으면 리소스 그룹, 배치 계정 및 관련된 모든 리소스를 삭제합니다. 이렇게 하려면 배치 계정에 대한 리소스 그룹을 선택하고 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 배치 계정, Batch 풀 및 Batch 작업을 만들었습니다. 작업에서 샘플 태스크를 실행했고, 노드 중 하나에서 만들어진 출력을 보았습니다. 이제 Batch 서비스의 핵심 개념을 이해 했으므로 더 큰 규모의 더 실제적인 작업 부하로 Batch를 시도할 준비가 되었습니다. Azure Batch에 대한 자세한 내용은 Azure Batch 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [Azure Batch 자습서](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png