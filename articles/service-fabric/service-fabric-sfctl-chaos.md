---
title: Azure Service Fabric CLI- sfctl chaos | Microsoft Docs
description: Service Fabric CLI sfctl chaos 명령을 설명합니다.
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
ms.openlocfilehash: b584ec301f0f4841c8df8fbbafb410abf645c373
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837354"
---
# <a name="sfctl-chaos"></a>sfctl chaos
비정상 상황 테스트 서비스를 시작하고, 중지하고 보고합니다.

## <a name="subgroups"></a>하위 그룹
|하위 그룹|설명|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | chaos schedule을 가져오고 설정합니다. |
## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| events | 연속 토큰 또는 시간 범위를 기반으로 Chaos 이벤트의 다음 세그먼트를 가져옵니다. |
| get | Chaos의 상태를 가져옵니다. |
| start | 클러스터의 Chaos가 시작됩니다. |
| stop(정지) | 클러스터에서 실행되는 경우 Chaos를 중지하고 Chaos Schedule을 중지된 상태로 전환합니다. |

## <a name="sfctl-chaos-events"></a>sfctl chaos events
연속 토큰 또는 시간 범위를 기반으로 Chaos 이벤트의 다음 세그먼트를 가져옵니다.

Chaos 이벤트의 다음 세그먼트를 가져오기 위해 ContinuationToken을 지정할 수 있습니다. Chaos 이벤트의 새 세그먼트의 시작 부분을 가져오기 위해 StartTimeUtc 및 EndTimeUtc를 통해 시간 범위를 지정할 수 있습니다. 동일한 호출에서는 ContinuationToken과 시간 범위를 모두 지정할 수 없습니다. 100개 이상의 Chaos 이벤트가 있는 경우 Chaos 이벤트는 세그먼트가 100개 이하의 Chaos 이벤트를 포함하는 여러 세그먼트에서 반환되며, 다음 세그먼트를 가져오기 위해 연속 토큰을 사용하여 이 API에 대한 호출을 만듭니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --end-time-utc | Chaos 보고서가 생성될 시간 범위의 종료 시간을 나타내는 Windows 파일 시간입니다. 자세한 내용은 [DateTime.ToFileTimeUtc 메서드](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)를 참조하세요. |
| --max-results | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다. |
| --start-time-utc | Chaos 보고서가 생성될 시간 범위의 시작 시간을 나타내는 Windows 파일 시간입니다. 자세한 내용은 [DateTime.ToFileTimeUtc 메서드](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx)를 참조하세요. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Chaos의 상태를 가져옵니다.

Chaos가 실행 중인지 여부, Chaos 매개 변수가 Chaos 실행에 사용되는지 여부 및 Chaos Schedule의 상태를 나타내는 Chaos의 상태를 가져옵니다.

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

## <a name="sfctl-chaos-start"></a>sfctl chaos start
클러스터의 Chaos가 시작됩니다.

Chaos가 아직 클러스터에 실행되고 있지 않다면 Chaos 매개 변수에 전달된 값으로 Chaos를 시작합니다. 이 호출을 수행할 때 Chaos가 이미 실행 중이면 호출이 FABRIC_E_CHAOS_ALREADY_RUNNING 오류 코드로 실패합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-type-health-policy-map | 특정 애플리케이션 형식에 대한 비정상 애플리케이션 최대 비율을 사용하여 JSON 인코딩된 목록입니다. 각 항목은 지정된 애플리케이션 형식의 애플리케이션을 평가하는 데 사용된 MaxPercentUnhealthyApplications 백분율을 나타내는 애플리케이션 형식 이름을 키로 그리고 정수를 값으로 지정합니다. <br><br> 특정 애플리케이션 유형에 대한 비정상 애플리케이션 최대 비율을 사용하여 맵을 정의합니다. 각 항목은 지정된 애플리케이션 유형의 애플리케이션을 평가하는 데 사용된 MaxPercentUnhealthyApplications 백분율을 나타내는 애플리케이션 유형 이름을 키로 그리고 정수를 값으로 지정합니다. 클러스터 상태를 평가하는 동안 애플리케이션 유형 상태 정책 맵을 사용하여 특수 애플리케이션 유형을 설명할 수 있습니다. 맵에 포함된 애플리케이션 유형은 클러스터 상태 정책에 정의된 전역 MaxPercentUnhealthyApplications가 아닌 맵에 지정된 백분율에 대해 평가됩니다. 맵에서 지정된 애플리케이션 유형의 애플리케이션은 애플리케이션의 전역 풀에 대해 계산되지 않습니다. 예를 들어 한 형식의 일부 애플리케이션이 중요한 경우, 클러스터 관리자는 항목을 해당 애플리케이션 유형에 대한 맵에 추가하고 0%의 값을 할당할 수 있습니다(즉, 오류를 허용하지 않음). 다른 모든 애플리케이션은 수천 개의 애플리케이션 인스턴스 중에 일부 오류를 허용하도록 20%로 설정된 MaxPercentUnhealthyApplications를 통해 평가될 수 있습니다. 애플리케이션 유형 상태 정책 맵은 클러스터 매니페스트가 HealthManager/EnableApplicationTypeHealthEvaluation에 대한 구성 항목을 사용하여 애플리케이션 유형 상태 평가를 활성화한 경우에만 사용됩니다. |
| --chaos-target-filter | 두 개의 문자열 형식 키를 사용하여 JSON으로 인코딩된 사전입니다. 두 개의 키는 NodeTypeInclusionList 및 ApplicationInclusionList입니다. 이러한 키 모두에 대한 값은 문자열 목록입니다. chaos_target_filter는 특정 노드 유형에서만 오류가 발생하거나 특정 애플리케이션에서만 오류가 발생하는 것처럼 대상이 지정된 Chaos 오류에 대한 모든 필터를 정의합니다. <br><br> chaos_target_filter를 사용하지 않으면 Chaos로 인해 모든 클러스터 엔터티에 오류가 발생합니다. chaos_target_filter를 사용하면 Chaos로 인해 chaos_target_filter 사양을 충족하는 엔터티에만 오류가 발생합니다. NodeTypeInclusionList 및 ApplicationInclusionList는 합집합 의미 체계만 허용합니다. NodeTypeInclusionList 및 ApplicationInclusionList의 교집합은 지정할 수 없습니다. 예를 들어, "해당 노드 유형에 해당할 경우에만 이 애플리케이션에 오류가 있다"고 지정할 수 없습니다. 엔터티가 NodeTypeInclusionList 또는 ApplicationInclusionList 중 하나에 포함되면 해당 엔터티는 ChaosTargetFilter를 사용하여 제외할 수 없습니다. applicationX가 ApplicationInclusionList에 표시되지 않더라도 일부 비정상 상황 반복에서 applicationX가 NodeTypeInclusionList에 포함된 nodeTypeY 노드에 있을 수 있으므로 오류가 있는 것으로 지정될 수 있습니다. NodeTypeInclusionList 및 ApplicationInclusionList 모두 비어 있는 경우 ArgumentException이 throw됩니다. 모든 유형의 오류(노드 다시 시작, 코드 패키지 다시 시작, 복제본 제거, 복제본 다시 시작, 주 복제본 이동 및 보조 복제본 이동)가 이러한 노드 유형의 노드에 대해 사용 가능으로 설정됩니다. 노드 유형(NodeTypeX)이 NodeTypeInclusionList에 표시되지 않으면 NodeTypeX의 노드에 대해 노드 수준 오류(예: NodeRestart)가 사용 가능으로 설정되지 않지만, ApplicationInclusionList의 애플리케이션이 NodeTypeX의 노드에 상주하게 될 경우 코드 패키지 및 복제본 오류는 NodeTypeX에 대해 여전히 사용될 수 있습니다. 최대 100개의 노드 유형 이름을 이 목록에 포함할 수 있으며, 이 수를 늘리려면 MaxNumberOfNodeTypesInChaosEntityFilter 구성에 대해 구성 업그레이드가 필요합니다. 이러한 애플리케이션의 서비스에 속하는 모든 복제본은 비정상 상황에 의해 복제본 오류(복제본 다시 시작, 복제본 제거, 주 복제본 이동 및 보조 복제본 이동)로 수정될 수 있습니다. 비정상 상황은 코드 패키지가 이러한 애플리케이션의 복제본만 호스트하는 경우에만 코드 패키지를 다시 시작할 수 있습니다. 애플리케이션이 이 목록에 나타나지 않을 경우, 애플리케이션이 NodeTypeInclusionList에 포함된 노드 유형의 노드에 배치되면 일부 Chaos 반복에서 여전히 오류가 있는 것으로 지정될 수 있습니다. 그러나 applicationX가 배치 제약 조건을 통해 nodeTypeY에 연결되며 applicationX가 ApplicationInclusionList에 없고, nodeTypeY가 NodeTypeInclusionList에 없으면 applicationX는 절대 오류가 있는 것으로 지정되지 않습니다. 최대 1,000개의 애플리케이션 이름을 이 목록에 포함할 수 있으며, 이 수를 늘리려면 MaxNumberOfApplicationsInChaosEntityFilter 구성에 대해 구성 업그레이드가 필요합니다. |
| --context | (string, string) 형식 키-값 쌍의 JSON으로 인코딩된 맵입니다. 비정상 상황 실행에 대한 정보를 기록하기 위해 맵이 사용될 수 있습니다. 이러한 쌍은 100개 이하로만 존재할 수 있으며 각 문자열(키 또는 값)은 4095자 이하로만 설정할 수 있습니다. 비정상 상황 실행 시작 기능이 특정 실행에 대한 컨텍스트를 선택적으로 저장할 수 있게 이러한 맵을 설정합니다. |
| --disable-move-replica-faults | 기본 이동을 사용하지 않도록 설정하고 보조 오류를 이동합니다. |
| --max-cluster-stabilization | 모든 클러스터 엔티티가 안정적이고 정상화가 될 때까지 기다리는 최대 시간입니다.  기본값\: 60. <br><br> Chaos가 반복적으로 실행되고 각 반복 시작 시 클러스터 엔터티의 상태에 대해 유효성을 검사합니다. 유효성을 검사하는 동안 클러스터 엔터티가 MaxClusterStabilizationTimeoutInSeconds 내에서 불안정하거나 정상적이지 않으면 Chaos가 유효성 검사 오류 이벤트를 생성합니다. |
| --max-concurrent-faults | 반복당 유도되는 동시 오류 최대 수 Chaos가 반복적으로 실행되고 두 번의 연속적인 반복은 유효성 검사 단계에 의해 구분됩니다. 동시성이 높을수록 오류 주입은 더 공격적입니다. 이는 버그를 파악하기에 더 복잡한 일련의 상태로 이어집니다. 2 또는 3 값으로 시작하고 이동하는 동안 주의하는 것이 좋습니다.  기본값\: 1. |
| --max-percent-unhealthy-apps | Chaos가 실행하는 동안 클러스터 상태를 평가할 때, 오류를 보고하기 전에 허용되는 비정상 애플리케이션의 최대 백분율입니다. <br><br> 오류를 보고하기 전에 허용되는 비정상 애플리케이션의 최대 백분율입니다. 예를 들어 애플리케이션의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 애플리케이션의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 애플리케이션이 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 값은 클러스터에 있는 애플리케이션 인스턴스의 총 수를 비정상 애플리케이션의 수로 나눠 계산합니다. 이 때 ApplicationTypeHealthPolicyMap에 포함된 애플리케이션 형식의 애플리케이션은 제외합니다. 계산값은 적은 수의 애플리케이션에서 오류 하나를 허용할 수 있도록 반올림됩니다. 기본 비율은 0입니다. |
| --max-percent-unhealthy-nodes | Chaos가 실행하는 동안 클러스터 상태를 평가할 때, 오류를 보고하기 전에 허용되는 비정상 노드의 최대 백분율입니다. <br><br> 오류를 보고하기 전에 허용되는 비정상 노드의 최대 백분율입니다. 예를 들어 노드의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 노드의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 노드가 적어도 하나 있다면 상태는 경고로 평가됩니다. 이 백분율은 클러스터에 있는 노드 총 수를 비정상 노드 수로 나눠 계산합니다. 계산값은 적은 수의 노드에서 오류 하나를 허용할 수 있도록 반올림됩니다. 기본 비율은 0입니다. 대형 클러스터에는 항상 복구를 위해 다운되거나 중단되는 노드가 있으므로 이를 감안하여 이 비율을 구성해야 합니다. |
| --time-to-run | Chaos가 자동으로 중지되기 전에 실행되는 총 시간(초)입니다. 최대 허용된 값은 4294967295(System.UInt32.MaxValue)입니다.  기본값\: 4294967295. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --wait-time-between-faults | 단일 반복 내에서 연속 오류 사이의 대기 시간(초)입니다.  기본값\: 20. <br><br> 값이 클수록 오류 간 겹침은 적어지며 클러스터가 통과하는 상태 전환의 시퀀스는 더 단순해집니다. 1 ~ 5 사이 값으로 시작하고 이동하는 동안 주의하는 것이 좋습니다. |
| --wait-time-between-iterations | 두 차례의 Chaos 연속 반복 간 시간-구분(초)입니다. 값이 높을수록 오류 삽입 속도는 낮아집니다.  기본값\: 30. |
| --warning-as-error | 경고가 오류와 동일한 심각도로 처리되는지 여부를 나타냅니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
클러스터에서 실행되는 경우 Chaos를 중지하고 Chaos Schedule을 중지된 상태로 전환합니다.

새로운 오류를 실행할 수 없도록 Chaos를 중지합니다. 진행 중인 오류는 완료될 때까지 계속해서 실행됩니다. 현재 Chaos Schedule은 중지된 상태로 전환됩니다. 일정이 중지되면 중지된 상태로 유지되며 Chaos의 Chaos Schedule 새 실행에 사용할 수 없습니다. 일정을 다시 시작하기 위해 새 Chaos Schedule을 설정해야 합니다.

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


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.