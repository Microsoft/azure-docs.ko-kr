---
title: 자습서-ACR 작업 예약
description: 이 자습서에서는 하나 이상의 타이머 트리거를 설정 하 여 정의 된 일정에 Azure Container Registry 작업을 실행 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 37247289ef11873ac37dc78ad56548994220f894
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454683"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>정의 된 일정에 따라 ACR 작업 실행

이 자습서에서는 일정에 따라 [ACR 작업](container-registry-tasks-overview.md) 을 실행 하는 방법을 보여 줍니다. 하나 이상의 *타이머 트리거*를 설정 하 여 작업을 예약 합니다. 타이머 트리거는 단독으로 사용 하거나 다른 작업 트리거와 함께 사용할 수 있습니다.

이 자습서에서는 다음 작업을 예약 하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 타이머 트리거를 사용 하 여 작업 만들기
> * 타이머 트리거 관리

작업 예약은 다음과 같은 시나리오에 유용 합니다.

* 예약 된 유지 관리 작업에 대해 컨테이너 워크 로드를 실행 합니다. 예를 들어 컨테이너 화 된 앱을 실행 하 여 레지스트리에서 불필요 한 이미지를 제거 합니다.
* Workday 동안 프로덕션 이미지에 대한 테스트 집합을 라이브 사이트 모니터링의 일부로 실행 합니다.

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용 하 여이 문서의 예제를 실행할 수 있습니다. 로컬에서 사용 하려는 경우 버전 2.0.68 이상이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.


## <a name="about-scheduling-a-task"></a>작업 예약 정보

* **Cron 식을** 사용 하는 트리거-태스크에 대한 타이머 트리거에서 *cron 식을*사용 합니다. 식은 작업을 트리거할 분, 시간, 일, 월 및 요일을 지정 하는 5 개의 필드가 포함 된 문자열입니다. 분당 최대 1 개의 주파수가 지원 됩니다.

  예를 들어 식 `"0 12 * * Mon-Fri"` 각 요일의 정오 UTC로 작업을 트리거합니다. 이 문서의 뒷부분에 있는 [세부 정보](#cron-expressions) 를 참조 하세요.
* **여러 타이머 트리거** -일정의 차이가 있는 한 작업에 여러 타이머를 추가 하는 작업을 수행할 수 있습니다.
    * 작업을 만들거나 나중에 추가할 때 여러 타이머 트리거를 지정 합니다.
    * 필요에 따라 더 쉽게 관리할 수 있도록 트리거의 이름을 지정 하거나 ACR 작업에서 기본 트리거 이름을 제공 합니다.
    * 타이머 일정이 한 번에 겹치면 ACR 작업에서 각 타이머에 대해 예약 된 시간에 작업을 트리거합니다.
* **다른 작업 트리거** -타이머 트리거 작업에서 [소스 코드 커밋](container-registry-tutorial-build-task.md) 또는 [기본 이미지 업데이트](container-registry-tutorial-base-image-update.md)를 기반으로 트리거를 사용 하도록 설정할 수도 있습니다. 다른 ACR 작업과 마찬가지로 예약 된 작업을 [수동으로 트리거할][az-acr-task-run] 수도 있습니다.

## <a name="create-a-task-with-a-timer-trigger"></a>타이머 트리거를 사용 하 여 작업 만들기

[Az acr task create][az-acr-task-create] 명령을 사용 하 여 작업을 만드는 경우 필요에 따라 타이머 트리거를 추가할 수 있습니다. `--schedule` 매개 변수를 추가 하 고 타이머에 대한 cron 식을 전달 합니다.

간단한 예제로, 다음 명령은 매일 21:00 UTC에 Docker 허브에서 `hello-world` 이미지 실행을 트리거합니다. 소스 코드 컨텍스트 없이 태스크가 실행 됩니다.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

[Az acr task show][az-acr-task-show] 명령을 실행 하 여 타이머 트리거가 구성 되어 있는지 확인 합니다. 기본적으로 기본 이미지 업데이트 트리거도 사용 하도록 설정 됩니다.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

[Az acr task 실행][az-acr-task-run] 을 사용 하 여 수동으로 작업을 트리거하여 올바르게 설정 되었는지 확인 합니다.

```azurecli
az acr task run --name mytask --registry myregistry
```

컨테이너가 성공적으로 실행 되 면 출력은 다음과 유사 합니다.

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

예약 된 시간 후에 [az acr task list-][az-acr-task-list-runs] run 명령을 실행 하 여 타이머가 예상 대로 작업을 트리거 했는지 확인 합니다.

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

타이머가 성공적으로 실행 되 면 출력은 다음과 유사 합니다.

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>타이머 트리거 관리

[Az acr task timer][az-acr-task-timer] 명령을 사용 하 여 acr 태스크에 대한 타이머 트리거를 관리 합니다.

### <a name="add-or-update-a-timer-trigger"></a>타이머 트리거 추가 또는 업데이트

작업을 만든 후 필요에 따라 [az acr task timer add][az-acr-task-timer-add] 명령을 사용 하 여 타이머 트리거를 추가 합니다. 다음 예에서는 이전에 만든 *mytask* 에 타이머 트리거 이름 *timer2* 을 추가 합니다. 이 타이머는 매일 10:30 UTC 마다 작업을 트리거합니다.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

[Az acr task timer update][az-acr-task-timer-update] 명령을 사용 하 여 기존 트리거의 일정을 업데이트 하거나 상태를 변경 합니다. 예를 들어 *timer2* 이라는 트리거를 업데이트 하 여 작업을 11:30 UTC로 트리거합니다.

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>타이머 트리거 나열

[Az acr task timer list][az-acr-task-timer-list] 명령은 작업에 대해 설정 된 타이머 트리거를 표시 합니다.

```azurecli
az acr task timer list --name mytask --registry myregistry
```

예제 출력:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>타이머 트리거 제거

[Az acr task timer remove][az-acr-task-timer-remove] 명령을 사용 하 여 작업에서 타이머 트리거를 제거 합니다. 다음 예에서는 *mytask*에서 *timer2* 트리거를 제거 합니다.

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 식

ACR 작업은 [NCronTab](https://github.com/atifaziz/NCrontab) 라이브러리를 사용 하 여 cron 식을 해석 합니다. ACR 작업에서 지원 되는 식에는 공백으로 구분 된 5 개의 필수 필드가 있습니다.

`{minute} {hour} {day} {month} {day-of-week}`

Cron 식에 사용 되는 표준 시간대는 UTC (협정 세계시)입니다. 시간은 24 시간 형식입니다.

> [!NOTE]
> ACR 작업은 cron 식의 `{second}` 또는 `{year}` 필드를 지원 하지 않습니다. 다른 시스템에 사용 되는 cron 식을 복사 하는 경우 이러한 필드를 사용 하는 경우 제거 해야 합니다.

각 필드에는 다음과 같은 형식의 값 중 하나가 포함될 수 있습니다.

|에  |예  |트리거될 때  |
|---------|---------|---------|
|특정 값 |<nobr>`"5 * * * *"`</nobr>|매시간 매 시간 5 분 지난 5 분|
|모든 값(`*`)|<nobr>`"* 5 * * *"`</nobr>|5:00 UTC부터 1 시간 마다 (60 시간)|
|범위(`-` 연산자)|<nobr>`"0 1-3 * * *"`</nobr>|하루에 3 번, 1:00, 2:00 및 3:00 UTC|
|값 집합(`,` 연산자)|<nobr>`"20,30,40 * * * *"`</nobr>|시간당 3 번, 20 분, 30 분, 40 분을 지난 시간|
|간격 값(`/` 연산자)|<nobr>`"*/10 * * * *"`</nobr>|시간당 6 번, 10 분, 20 분 등

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 예제

|예|트리거될 때  |
|---------|---------|
|`"*/5 * * * *"`|5분마다 한 번|
|`"0 * * * *"`|1시간이 시작할 때마다 한 번|
|`"0 */2 * * *"`|2시간마다 한 번|
|`"0 9-17 * * *"`|1 시간 마다 한 번씩 9:00에서 17:00 UTC로|
|`"30 9 * * *"`|매일 9:30 UTC|
|`"30 9 * * 1-5"`|9:30 UTC (평일) 마다|
|`"30 9 * Jan Mon"`|매월 월요일 9:30 UTC|


## <a name="next-steps"></a>다음 단계

이 자습서에서는 타이머에 의해 자동으로 트리거되는 Azure Container Registry 작업을 만드는 방법에 대해 알아보았습니다. 

예약 된 작업을 사용 하 여 레지스트리에서 리포지토리를 정리 하는 예제는 [Azure container registry에서 자동으로 이미지 제거](container-registry-auto-purge.md)를 참조 하세요.

소스 코드 커밋 또는 기본 이미지 업데이트에 의해 트리거되는 작업의 예는 [ACR 작업 자습서 시리즈](container-registry-tutorial-quick-task.md)의 다른 문서를 참조 하세요.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
