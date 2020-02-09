---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/06/2019
ms.author: larryfr
ms.openlocfilehash: 2124b5241015ca74ff6507767396b1a27bd1191d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935954"
---
`deploymentconfig.json` 문서의 항목은 AksWebservice에 대한 매개 변수에 매핑됩니다 [. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `computeType` | 해당 없음 | 컴퓨팅 대상. AKS의 경우에는 값이 `aks`이어야 합니다. |
| `autoScaler` | 해당 없음 | 자동 크기 조정에 대한 구성 요소를 포함 합니다. Autoscaler 표를 참조 하세요. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | 웹 서비스에 자동 크기 조정을 사용할지 여부입니다. `numReplicas` = `0``True`; 그렇지 않으면 `False`합니다. |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | 이 웹 서비스의 크기를 자동으로 조정 하는 데 사용할 최소 컨테이너 수입니다. 기본값 `1`입니다. |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | 이 웹 서비스의 자동 크기를 자동으로 조정 하는 데 사용할 최대 컨테이너 수입니다. 기본값 `10`입니다. |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Autoscaler이이 웹 서비스의 크기를 조정 하는 빈도입니다. 기본값 `1`입니다. |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Autoscaler이이 웹 서비스에 대한 유지 관리를 시도 하는 대상 사용률 (100의 백분율)입니다. 기본값 `70`입니다. |
| `dataCollection` | 해당 없음 | 데이터 컬렉션에 대한 구성 요소를 포함 합니다. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | 웹 서비스에 대해 모델 데이터 수집을 사용할지 여부입니다. 기본값 `False`입니다. |
| `authEnabled` | `auth_enabled` | 웹 서비스에 대한 키 인증을 사용할지 여부입니다. `tokenAuthEnabled`와 `authEnabled`는 모두 `True`수 없습니다. 기본값 `True`입니다. |
| `tokenAuthEnabled` | `token_auth_enabled` | 웹 서비스에 대한 토큰 인증을 사용할지 여부입니다. `tokenAuthEnabled`와 `authEnabled`는 모두 `True`수 없습니다. 기본값 `False`입니다. |
| `containerResourceRequirements` | 해당 없음 | CPU 및 메모리 엔터티의 컨테이너입니다. |
| &emsp;&emsp;`cpu` | `cpu_cores` | 이 웹 서비스에 할당할 CPU 코어 수입니다. 기본값, `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | 이 웹 서비스에 할당할 메모리 양 (GB)입니다. 기본값, `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | 웹 서비스에 대한 Application Insights 로깅을 사용할지 여부입니다. 기본값 `False`입니다. |
| `scoringTimeoutMs` | `scoring_timeout_ms` | 웹 서비스에 대한 점수 매기기 호출에 적용 되는 시간 제한입니다. 기본값 `60000`입니다. |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | 이 웹 서비스에 대한 노드당 최대 동시 요청 수입니다. 기본값 `1`입니다. |
| `maxQueueWaitMs` | `max_request_wait_time` | 503 오류가 반환 되기까지 요청을 큐에 유지 하는 최대 시간 (밀리초)입니다. 기본값 `500`입니다. |
| `numReplicas` | `num_replicas` | 이 웹 서비스에 할당할 컨테이너 수입니다. 기본값은 없습니다. 이 매개 변수를 설정 하지 않으면 기본적으로 autoscaler가 사용 됩니다. |
| `keys` | 해당 없음 | 키에 대한 구성 요소를 포함 합니다. |
| &emsp;&emsp;`primaryKey` | `primary_key` | 이 웹 서비스에 사용할 기본 인증 키 |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | 이 웹 서비스에 사용할 보조 인증 키 |
| `gpuCores` | `gpu_cores` | 이 웹 서비스에 할당할 GPU 코어의 수입니다. 기본값은 1입니다. 는 정수 값만 지원 합니다. |
| `livenessProbeRequirements` | 해당 없음 | 선거의 프로브 요구 사항에 대한 구성 요소를 포함 합니다. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | 선거의 프로브를 수행 하는 빈도 (초)입니다. 기본값은 10 초입니다. 최소값은 1입니다. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | 컨테이너를 시작한 후 선거의 프로브를 시작 하는 시간 (초)입니다. 기본값은 310입니다. |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | 선거의 프로브 시간이 초과 되는 시간 (초)입니다. 기본값은 2 초입니다. 최소값은 1입니다. |
| &emsp;&emsp;`successThreshold` | `success_threshold` | 선거의 프로브가 실패 한 후 성공으로 간주 되는 최소 연속 성공입니다. 기본값은 1입니다. 최소값은 1입니다. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Pod가 시작 되 고 선거의 프로브가 실패 하면 Kubernetes는 포기 하기 전에 카운터가 failurethreshold 시간을 시도 합니다. 기본값은 3입니다. 최소값은 1입니다. |
| `namespace` | `namespace` | 웹 서비스가 배포 되는 Kubernetes 네임 스페이스입니다. 최대 63 소문자 영숫자 (' 으로만 구성-a'-'z ', ' 0 '-' 9 ') 및 하이픈 ('-') 문자. 첫 번째 및 마지막 문자는 하이픈을 사용할 수 없습니다. |

다음 JSON은 CLI에서 사용할 수 있는 배포 구성의 예입니다.

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
