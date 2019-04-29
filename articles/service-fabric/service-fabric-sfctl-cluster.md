---
title: Azure Service Fabric CLI- sfctl cluster | Microsoft Docs
description: Service Fabric CLI sfctl cluster 명령을 설명합니다.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 7bb399472d7e0ab14e6399fc8652d2eb132a866a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837329"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Service Fabric 클러스터를 선택하고, 관리하고, 운영합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| code-versions | Service Fabric 클러스터에 프로비전되는 패브릭 코드 버전을 가져옵니다. |
| config-versions | Service Fabric 클러스터에 프로비전되는 패브릭 구성 버전의 목록을 가져옵니다. |
| health | Service Fabric 클러스터의 상태를 가져옵니다. |
| manifest | Service Fabric 클러스터 매니페스트를 가져옵니다. |
| operation-cancel | 사용자로 인한 오류 작업을 취소합니다. |
| operation-list | 제공된 입력으로 필터링한 사용자로 인한 오류 작업 목록을 가져옵니다. |
| provision | Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다. |
| recover-system | 현재 쿼럼 손실에 걸린 시스템 서비스를 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다. |
| report-health | Service Fabric 클러스터 대한 상태 보고서를 보냅니다. |
| 선택 | Service Fabric 클러스터 엔드포인트에 연결합니다. |
| show-connection | 이 sfctl 인스턴스가 연결된 Service Fabric 클러스터를 보여줍니다. |
| unprovision | Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다. |
| 업그레이드 | Service Fabric 클러스터의 코드 또는 구성 버전 업그레이드를 시작합니다. |
| upgrade-resume | 클러스터 업그레이드를 다음 업그레이드 도메인으로 이동합니다. |
| upgrade-rollback | Service Fabric 클러스터의 업그레이드를 롤백합니다. |
| upgrade-status | 현재 클러스터 업그레이드의 진행률을 가져옵니다. |
| upgrade-update | Service Fabric 클러스터의 업그레이드 매개 변수를 업데이트합니다. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-versions
Service Fabric 클러스터에 프로비전되는 패브릭 코드 버전을 가져옵니다.

클러스터에 프로비전되는 패브릭 코드 버전에 대한 정보의 목록을 가져옵니다. 매개 변수 CodeVersion은 필요에 따라 해당 특정 버전에만 출력을 필터링하는 데 사용될 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --code-version | Service Fabric의 제품 버전입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versions
Service Fabric 클러스터에 프로비전되는 패브릭 구성 버전의 목록을 가져옵니다.

클러스터에 프로비전되는 패브릭 구성 버전에 대한 정보의 목록을 가져옵니다. 매개 변수 ConfigVersion은 필요에 따라 해당 특정 버전에만 출력을 필터링하는 데 사용될 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --config-version | Service Fabric의 구성 버전입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-health"></a>sfctl cluster health
Service Fabric 클러스터의 상태를 가져옵니다.

상태에 따라 클러스터에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다. 마찬가지로, 집계된 상태에 따라 반환된 노드 및 애플리케이션 컬렉션을 필터링할 NodesHealthStateFilter 및 ApplicationsHealthStateFilter를 사용합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --applications-health-state-filter | 상태에 따라 클러스터 상태 쿼리의 결과로 반환된 애플리케이션 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용 가능한 값은 HealthStateFilter 열거형의 멤버 또는 멤버에 대한 비트 연산에서 가져온 정수 값입니다. 필터와 일치하는 애플리케이션만 반환됩니다. 모든 애플리케이션은 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 애플리케이션의 상태가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --events-health-state-filter | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --exclude-health-statistics | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔터티 수를 보여줍니다. |
| --include-system-application-health-statistics | 상태 통계에 fabric\:/System 애플리케이션 상태 통계를 포함할지 여부를 지정합니다. False(기본값). IncludeSystemApplicationHealthStatistics가 true로 설정된 경우 fabric\:/System 애플리케이션에 속한 엔터티를 포함합니다. 그렇지 않으면 쿼리 결과는 사용자 애플리케이션에 대해서만 상태 통계를 포함합니다. 이 매개 변수를 적용하려면 상태 통계를 쿼리 결과에 포함해야 합니다. |
| --nodes-health-state-filter | 상태에 따라 클러스터 상태 쿼리의 결과로 반환된 노드 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 노드만 반환됩니다. 모든 노드는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 노드의 상태가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-manifest"></a>sfctl 클러스터 매니페스트
Service Fabric 클러스터 매니페스트를 가져옵니다.

Service Fabric 클러스터 매니페스트를 가져옵니다. 클러스터 매니페스트에는 클러스터, 보안 구성, 오류 및 업그레이드 도메인 토폴로지 등의 다양한 노드 형식을 포함하는 클러스터의 속성이 들어 있습니다. 이러한 속성은 독립 실행형 클러스터를 배포하는 동안 ClusterConfig.JSON 파일의 일부로 지정됩니다. 그러나 클러스터 매니페스트에 있는 대부분 정보는 다른 배포 시나리오(예: Azure Portal 사용 시)에서 클러스터 배포할 경우 서비스 패브릭에서 내부적으로 생성됩니다. 클러스터 매니페스트의 내용이 단지 정보용일 경우 사용자는 파일 내용 및 해석 형식에 의존하지 않아야 합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operation-cancel
사용자로 인한 오류 작업을 취소합니다.

CancelOperation를 사용 하 여 취소 될 수도 있습니다는 오류 작업을 시작 하는 다음과 같은 Api\: StartDataLoss, StartQuorumLoss, StartPartitionRestart startnodetransition을 합니다. force가 false인 경우 지정된 사용자로 인한 작업이 정상적으로 중지되며 정리됩니다.  force가 true인 경우 명령이 중단되며, 일부 내부 상태가 남아 있을 수 있습니다.  force를 true로 지정하는 것은 주의해서 사용해야 합니다. force가 true로 설정된 이 API를 호출하는 것은 이 API가 force가 false로 먼저 설정된 동일한 테스트 명령에서 호출될 때까지 또는 테스트 명령에 이미 OperationState.RollingBack의 OperationState가 있는 한 허용되지 않습니다. 

설명\: OperationState.RollingBack은 시스템이 명령 실행으로 인해 발생된 내부 시스템 상태를 정리함을 의미합니다.  테스트 명령이 데이터 손실을 발생시킨 경우 데이터를 복원하지 않습니다.  예를 들어 StartDataLoss를 호출한 다음, 이 API를 호출하는 경우 시스템은 명령 실행에서의 내부 상태만을 정리합니다. 명령이 데이터 손실을 충분히 진행시킨 경우 대상 파티션의 데이터를 복원하지 않습니다. 

> [!NOTE]
> 이 API가 force==true로 호출된 경우 내부 상태는 남아 있을 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --operation-id [필수] | 이 API의 호출을 식별하는 GUID입니다.  이 값은 해당 GetProgress API에 전달됩니다. |
| --force | 사용자로 인한 작업을 실행하여 수정된 내부 시스템 상태를 정상적으로 롤백 및 정리할지 여부를 나타냅니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operation-list
제공된 입력으로 필터링한 사용자로 인한 오류 작업 목록을 가져옵니다.

제공된 입력으로 필터링한 사용자로 인한 오류 작업 목록을 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --state-filter | OperationState의 사용자로 인한 작업을 필터링하는 데 사용됩니다. <br> 65535 - 모두 선택 <br> 1 - Running 선택 <br> 2     - RollingBack 선택 <br>8     - Completed 선택 <br>16    - Faulted 선택 <br>32    - Cancelled 선택 <br>64    - ForceCancelled 선택  <br>기본값\: 65535 |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --type-filter | 사용자로 인한 작업에 대한 OperationType을 필터링하는 데 사용됩니다. <br> 65535 - 모두 선택 <br> 1     - PartitionDataLoss 선택 <br> 2     - PartitionQuorumLoss 선택 <br> 4     - PartitionRestart 선택 <br> 8     - NodeTransition 선택  <br> 기본값\: 65535 |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다.

Service Fabric 클러스터의 코드 또는 구성 패키지의 유효성을 검사하거나 프로비전합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --cluster-manifest-file-path | 클러스터 매니페스트 파일 경로입니다. |
| --code-file-path | 클러스터 코드 패키지 파일 경로입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-system
현재 쿼럼 손실에 걸린 시스템 서비스를 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다.

현재 쿼럼 손실에 걸린 시스템 서비스를 복구하고자 시도해야 함을 Service Fabric 클러스터에 나타냅니다. 이 작업은 다운된 복제본을 복구하지 못할 경우에만 수행해야 합니다. 이 API를 잘못 사용하면 잠재적인 데이터 손실이 발생할 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster report-health
Service Fabric 클러스터 대한 상태 보고서를 보냅니다.

보고서는 상태 보고서의 원본 및 보고되는 속성에 대한 정보를 포함해야 합니다. 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 노드에 전송됩니다. 보고서는 게이트웨이에 의해 허용되지만 추가 유효성 검사 후 Health 스토어에 의해 거부될 수 있습니다. 예를 들어 Health 스토어는 부실 시퀀스 번호와 같은 잘못된 매개 변수로 인해 보고서를 거부할 수도 있습니다. 보고서가 Health 스토어에서 적용되었는지 여부를 확인하려면 해당 보고서가 클러스터의 HealthEvents에 표시되는지 확인합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --health-property [필수] | 상태 정보의 속성입니다. <br><br> 엔터티는 서로 다른 속성에 대한 상태 보고서를 가질 수 있습니다. 속성은 문자열이며 보고자 유연성에서 보고서를 트리거하는 상태 조건을 분류하도록 허용하는 고정된 열거형이 아닙니다. 예를 들어 SourceId "LocalWatchdog"가 있는 보고자는 노드에서 사용 가능한 디스크의 상태를 모니터링할 수 있으므로 해당 노드에서 "AvailableDisk" 속성을 보고할 수 있습니다. 동일한 보고자는 노드 연결을 모니터링할 수 있으므로 동일한 노드에서 "Connectivity" 속성을 보고할 수 있습니다. Health 스토어에서 이러한 보고서는 지정된 노드에 대한 별도의 상태 이벤트로 처리됩니다. SourceId와 함께 속성은 상태 정보를 고유하게 식별합니다. |
| --health-state    [필수] | 가능한 값\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --source-id       [필수] | 상태 정보를 생성한 클라이언트/watchdog/시스템 구성 요소를 식별하는 원본 이름입니다. |
| --description | 상태 정보의 설명입니다. <br><br> 보고서에 대한 사람이 읽을 수 있는 정보를 추가하는 데 사용되는 일반 텍스트를 나타냅니다. 설명의 최대 문자열 길이는 4096자입니다. 제공된 문자열이 긴 경우 자동으로 잘립니다. 잘린 경우 설명의 마지막 문자는 "[잘림]" 표식을 포함하고, 총 문자열 크기는 4096자입니다. 표식은 사용자에게 잘림이 발생했음을 나타냅니다. 잘린 경우 설명은 원래 문자열의 4096자보다 작은 문자를 갖습니다. |
| --immediate | 보고서를 즉시 보낼지 여부를 나타내는 플래그입니다. <br><br> 상태 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 애플리케이션에 전송됩니다. Immediate가 true로 설정된 경우 보고서는 HTTP 게이트웨이 애플리케이션이 사용 중인 패브릭 클라이언트 설정에 관계 없이 HTTP 게이트웨이에서 Health 스토어로 즉시 전송됩니다. 이는 가능한 한 빨리 보내야 하는 중요한 보고서에 유용합니다. 타이밍 및 기타 조건에 따라 보고서 전송이 여전히 실패할 수 있습니다. 예를 들어, HTTP 게이트웨이가 닫혀 있거나 메시지가 게이트웨이에 도달하지 않는 경우입니다. Immediate가 false로 설정된 경우 보고서는 HTTP 게이트웨이의 상태 클라이언트 설정에 따라 전송됩니다. 따라서 HealthReportSendInterval 구성에 따라 일괄처리됩니다. 상태 클라이언트가 상태 보고서 처리뿐만 아니라 Health 스토어로 메시지를 보고하는 상태를 최적화하도록 허용하므로 이는 권장 설정입니다. 기본적으로 보고서는 즉시 전송되지 않습니다. |
| --remove-when-expired | 보고서가 만료될 때 Health 스토어에서 제거할지 여부를 나타내는 값입니다. <br><br> true로 설정된 경우 보고서는 만료된 후 Health 스토어에서 제거됩니다. false로 설정된 경우 보고서는 만료될 때 오류로 처리됩니다. 이 속성의 값은 기본적으로 false입니다. 클라이언트가 주기적으로 보고하는 경우 RemoveWhenExpired를 false(기본값)로 설정해야 합니다. 이러한 방식은 문제가 있고(예: 교착 상태) 보고할 수 없는 보고자이며, 엔터티는 상태 보고서가 만료되는 경우 오류로 평가됩니다. 엔터티를 오류 성능 상태에 있는 것으로 플래그 지정합니다. |
| --sequence-number | 숫자 문자열의 이 상태 보고서에 대한 일련 번호입니다. <br><br> 보고서 일련 번호는 Health 스토어에서 유효하지 않은 보고서를 검색하는 데 사용됩니다. 지정되지 않은 경우 일련 번호는 보고서가 추가될 때 상태 클라이언트에서 자동으로 생성됩니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --ttl | 이 상태 보고서가 유효한 기간입니다. 이 필드는 기간을 지정하는 데 ISO8601 형식을 사용합니다. <br><br> 클라이언트가 주기적으로 보고하는 경우 TTL(Time to Live)보다 높은 빈도로 보고서를 보내야 합니다. 클라이언트가 전환 시 보고하는 경우 TTL(Time to live)을 무한으로 설정할 수 있습니다. TTL(Time to live)이 만료되면 상태 정보를 포함하는 상태 이벤트는 RemoveWhenExpired가 true인 경우 Health 스토어에서 제거되거나 RemoveWhenExpired가 false인 경우 오류로 평가됩니다. 지정되지 않은 경우 TTL(Time to live)은 무한 값으로 기본 설정됩니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Service Fabric 클러스터 엔드포인트에 연결합니다.

보안 클러스터에 연결한 경우, 인증서(.crt) 및 키 파일(.key)에 대한 절대 경로를 지정하거나 단일 파일을 양쪽 모두(.pem)로 지정합니다. 둘 다 지정하지 마세요. 보안 클러스터에 연결한 경우 필요에 따라 CA 번들 파일 또는 신뢰할 수 있는 CA 인증서 디렉터리에 대한 절대 경로를 지정합니다. CA 인증서 디렉터리를 사용하는 경우 OpenSSL에서 제공하는 `c_rehash <directory>`를 먼저 실행하여 인증서 해시를 계산하고 적절한 기호 링크를 만들어야 합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --endpoint [필수] | 포트 및 HTTP 또는 HTTPS 접두사를 포함하는 클러스터 엔드포인트 URL입니다. |
| --aad | 인증에 Azure Active Directory를 사용합니다. |
| --ca | 유효로 처리될 CA 인증서 디렉터리 또는 CA 번들 파일에 대한 절대 경로입니다. |
| --cert | 클라이언트 인증서 파일에 대한 절대 경로입니다. |
| --key | 클라이언트 인증서 키 파일에 대한 절대 경로입니다. |
| --no-verify | HTTPS 사용 시 인증서에 대한 확인 비활성화, 참고\: 안전 하지 않은 옵션 이므로 프로덕션 환경에 사용해서는 안됩니다. |
| --pem | .pem 파일의 클라이언트 인증서에 대한 절대 경로입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
이 sfctl 인스턴스가 연결된 Service Fabric 클러스터를 보여줍니다.

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Service Fabric 클러스터의 코드 또는 구성 패키지를 프로 비전합니다.

코드 및 구성을 개별적으로 프로비전 해제하도록 지원합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --code-version | 클러스터 코드 패키지 버전입니다. |
| --config-version | 클러스터 매니페스트 버전입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Service Fabric 클러스터의 코드 또는 구성 버전 업그레이드를 시작합니다.

제공된 업그레이드 매개 변수의 유효성을 검사하고 매개 변수가 유효한 경우 Service Fabric 클러스터의 코드 또는 구성 버전 업그레이드를 시작합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-health-map | 오류를 발생시키기 전의 비정상 애플리케이션 이름 및 최대 비율 쌍의 JSON 인코딩된 사전. |
| --app-type-health-map | 오류를 발생시키기 전의 비정상 애플리케이션 형식 이름 및 최대 비율 쌍의 JSON 인코딩된 사전. |
| --code-version | 클러스터 코드 버전. |
| --config-version | 클러스터 구성 버전. |
| --delta-health-evaluation | 각 업그레이드 도메인 완료 후 절대 상태 평가가 아닌 델타 상태 평가를 사용하도록 설정합니다. |
| --delta-unhealthy-nodes | 클러스터를 업그레이드하는 동안 허용되는 노드 상태 저하의 최대 비율입니다.  기본값\: 10. <br><br> 델타는 업그레이드 시작 시 노드 상태와 상태 평가 시 노드 상태 간에 측정됩니다. 클러스터의 전역 상태를 허용 한도 내로 유지하기 위해 업그레이드 도메인 업그레이드가 완료될 때마다 검사가 수행됩니다. |
| --failure-action | 사용할 수 있는 값\: 'Invalid', 'Rollback', 'Manual'. |
| --force-restart | 코드 버전이 변경되지 않은 경우에도 업그레이드 중에 프로세스를 강제로 다시 시작합니다. <br><br> 업그레이드는 구성 또는 데이터만 변경합니다. |
| --health-check-retry | 애플리케이션 또는 클러스터가 정상이 아닌지 상태 확인을 수행하는 시도의 시간 간격입니다. |
| --health-check-stable | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 애플리케이션 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --health-check-wait | 업그레이드 도메인을 완료한 후 상태 확인 프로세스를 시작하기 전에 대기하는 시간입니다. |
| --replica-set-check-timeout | 예기치 않은 문제가 있을 때 업그레이드 도메인의 처리를 차단하고 가용성의 손실을 방지하는 최대 시간입니다. <br><br> 이 시간 제한이 만료되면 가용성 손실 문제와 상관없이 업그레이드 도메인 처리가 진행됩니다. 시간 제한은 각 업그레이드 도메인의 시작 시 다시 설정됩니다. 유효한 값은 0과 42949672925(포함) 사이입니다. |
| --rolling-upgrade-mode | 사용할 수 있는 값\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  기본값\: UnmonitoredAuto. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --unhealthy-applications | 오류를 보고하기 전에 허용되는 비정상 애플리케이션의 최대 백분율입니다. <br><br> 예를 들어 애플리케이션의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 애플리케이션의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 애플리케이션이 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 값은 클러스터에 있는 애플리케이션 인스턴스의 총 수를 비정상 애플리케이션의 수로 나눠 계산합니다. 이 때 ApplicationTypeHealthPolicyMap에 포함된 애플리케이션 형식의 애플리케이션은 제외합니다. 계산값은 적은 수의 애플리케이션에서 오류 하나를 허용할 수 있도록 반올림됩니다. |
| --unhealthy-nodes | 오류를 보고하기 전에 허용되는 비정상 노드의 최대 백분율입니다. <br><br> 예를 들어 노드의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 노드의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 노드가 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 백분율은 클러스터에 있는 노드 총 수를 비정상 노드 수로 나눠 계산합니다. 계산값은 적은 수의 노드에서 오류 하나를 허용할 수 있도록 반올림됩니다. 대형 클러스터에는 항상 복구를 위해 다운되거나 중단되는 노드가 있으므로 이를 감안하여 이 비율을 구성해야 합니다. |
| --upgrade-domain-delta-unhealthy-nodes | 클러스터를 업그레이드하는 동안 허용되는 업그레이드 도메인 노드 상태 저하의 최대 비율입니다.  기본값\: 15. <br><br> 델타는 업그레이드 시작 시 업그레이드 도메인 노드 상태와 상태 평가 시 업그레이드 도메인 노드 상태 간에 측정됩니다. 클러스터의 전역 상태를 허용 한도 내로 유지하기 위해 업그레이드 도메인 업그레이드가 완료될 때마다 완료된 업그레이드 도메인에 대한 검사가 수행됩니다. |
| --upgrade-domain-timeout | FailureAction이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --upgrade-timeout | FailureAction이 실행되기 전에 전체 업그레이드를 완료해야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --warning-as-error | 경고가 오류와 동일한 심각도로 처리되는지 여부를 나타냅니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
클러스터 업그레이드를 다음 업그레이드 도메인으로 이동합니다.

해당되는 경우 클러스터 코드 또는 구성 업그레이드를 다음 업그레이드 도메인으로 이동합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --upgrade-domain [필수] | 이 클러스터 업그레이드에 대한 다음 업그레이드 도메인입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
Service Fabric 클러스터의 업그레이드를 롤백합니다.

Service Fabric 클러스터의 코드 또는 구성 업그레이드를 롤백합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
현재 클러스터 업그레이드의 진행률을 가져옵니다.

진행 중인 클러스터 업그레이드의 현재 진행률을 가져옵니다. 현재 진행 중인 업그레이드가 없는 경우 이전 클러스터 업그레이드의 마지막 상태를 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-update
Service Fabric 클러스터의 업그레이드 매개 변수를 업데이트합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-health-map | 오류를 발생시키기 전의 비정상 애플리케이션 이름 및 최대 비율 쌍의 JSON 인코딩된 사전. |
| --app-type-health-map | 오류를 발생시키기 전의 비정상 애플리케이션 형식 이름 및 최대 비율 쌍의 JSON 인코딩된 사전. |
| --delta-health-evaluation | 각 업그레이드 도메인 완료 후 절대 상태 평가가 아닌 델타 상태 평가를 사용하도록 설정합니다. |
| --delta-unhealthy-nodes | 클러스터를 업그레이드하는 동안 허용되는 노드 상태 저하의 최대 비율입니다.  기본값\: 10. <br><br> 델타는 업그레이드 시작 시 노드 상태와 상태 평가 시 노드 상태 간에 측정됩니다. 클러스터의 전역 상태를 허용 한도 내로 유지하기 위해 업그레이드 도메인 업그레이드가 완료될 때마다 검사가 수행됩니다. |
| --failure-action | 사용할 수 있는 값\: 'Invalid', 'Rollback', 'Manual'. |
| --force-restart | 코드 버전이 변경되지 않은 경우에도 업그레이드 중에 프로세스를 강제로 다시 시작합니다. <br><br> 업그레이드는 구성 또는 데이터만 변경합니다. |
| --health-check-retry | 애플리케이션 또는 클러스터가 정상이 아닌지 상태 확인을 수행하는 시도의 시간 간격입니다. |
| --health-check-stable | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 애플리케이션 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --health-check-wait | 업그레이드 도메인을 완료한 후 상태 확인 프로세스를 시작하기 전에 대기하는 시간입니다. |
| --replica-set-check-timeout | 예기치 않은 문제가 있을 때 업그레이드 도메인의 처리를 차단하고 가용성의 손실을 방지하는 최대 시간입니다. <br><br> 이 시간 제한이 만료되면 가용성 손실 문제와 상관없이 업그레이드 도메인 처리가 진행됩니다. 시간 제한은 각 업그레이드 도메인의 시작 시 다시 설정됩니다. 유효한 값은 0과 42949672925(포함) 사이입니다. |
| --rolling-upgrade-mode | 사용할 수 있는 값\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  기본값\: UnmonitoredAuto. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --unhealthy-applications | 오류를 보고하기 전에 허용되는 비정상 애플리케이션의 최대 백분율입니다. <br><br> 예를 들어 애플리케이션의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 애플리케이션의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 애플리케이션이 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 값은 클러스터에 있는 애플리케이션 인스턴스의 총 수를 비정상 애플리케이션의 수로 나눠 계산합니다. 이 때 ApplicationTypeHealthPolicyMap에 포함된 애플리케이션 형식의 애플리케이션은 제외합니다. 계산값은 적은 수의 애플리케이션에서 오류 하나를 허용할 수 있도록 반올림됩니다. |
| --unhealthy-nodes | 오류를 보고하기 전에 허용되는 비정상 노드의 최대 백분율입니다. <br><br> 예를 들어 노드의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 노드의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 노드가 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 백분율은 클러스터에 있는 노드 총 수를 비정상 노드 수로 나눠 계산합니다. 계산값은 적은 수의 노드에서 오류 하나를 허용할 수 있도록 반올림됩니다. 대형 클러스터에는 항상 복구를 위해 다운되거나 중단되는 노드가 있으므로 이를 감안하여 이 비율을 구성해야 합니다. |
| --upgrade-domain-delta-unhealthy-nodes | 클러스터를 업그레이드하는 동안 허용되는 업그레이드 도메인 노드 상태 저하의 최대 비율입니다.  기본값\: 15. <br><br> 델타는 업그레이드 시작 시 업그레이드 도메인 노드 상태와 상태 평가 시 업그레이드 도메인 노드 상태 간에 측정됩니다. 클러스터의 전역 상태를 허용 한도 내로 유지하기 위해 업그레이드 도메인 업그레이드가 완료될 때마다 완료된 업그레이드 도메인에 대한 검사가 수행됩니다. |
| --upgrade-domain-timeout | FailureAction이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --upgrade-kind | 가능한 값\: 'Invalid', 'Rolling', 'Rolling_ForceRestart'.  기본값\: Rolling. |
| --upgrade-timeout | FailureAction이 실행되기 전에 전체 업그레이드를 완료해야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --warning-as-error | 경고가 오류와 동일한 심각도로 처리되는지 여부를 나타냅니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.