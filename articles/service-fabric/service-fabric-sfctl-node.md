---
title: Azure Microsoft Azure Service Fabric CLI- sfctl node | Microsoft Docs
description: Service Fabric CLI sfctl node 명령을 설명합니다.
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
ms.openlocfilehash: 08ea0081c84ea31b2b71d03679b1b527cf94c075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556783"
---
# <a name="sfctl-node"></a>sfctl node
클러스터를 형성하는 노드를 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| disable | 지정한 비활성화 의도로 Microsoft Azure Service Fabric 클러스터 노드를 비활성화합니다. |
| enable | 현재 비활성화되어 있는 Microsoft Azure Service Fabric 클러스터 노드를 활성화합니다. |
| health | Microsoft Azure Service 패브릭 노드의 상태를 가져옵니다. |
| info | Microsoft Azure Service Fabric 클러스터에서 특정 노드에 관한 정보를 가져옵니다. |
| list | Microsoft Azure Service Fabric 클러스터에서 노드 목록을 가져옵니다. |
| load | Microsoft Azure Service 패브릭 노드의 부하 정보를 가져옵니다. |
| remove-state | 노드의 지속된 상태가 영구적으로 제거되거나 손실되었음을 Microsoft Azure Service Fabric에 알립니다. |
| report-health | Microsoft Azure Service 패브릭 노드에 관한 상태 보고서를 보냅니다. |
| restart | Microsoft Azure Service Fabric 클러스터 노드를 다시 시작합니다. |
| transition | 클러스터 노드를 시작하거나 중지합니다. |
| transition-status | StartNodeTransition을 사용하여 시작된 작업의 진행률을 가져옵니다. |

## <a name="sfctl-node-disable"></a>sfctl node disable
지정한 비활성화 의도로 Microsoft Azure Service Fabric 클러스터 노드를 비활성화합니다.

지정한 비활성화 의도로 Microsoft Azure Service Fabric 클러스터 노드를 비활성화합니다. 비활성화가 진행 중이라면, 비활성화 의도가 증가할 수 있지만, 감소하지는 않습니다.(예를 들어 일시 중지 목적으로 비활성화된 노드는 다시 시작으로 더욱 비활성화되지만, 그 반대로는 되지 않습니다. 노드는 비활성화한 후 언제든지 활성화를 사용하여 노드 작업을 다시 활성화할 수 있습니다. 비활성화가 완료되지 않을 경우 비활성화가 취소됩니다. 비활성화하는 동안에 작동 중지되었다가 다시 작동하는 노드는 여전히 다시 활성화해야 서비스를 해당 노드에 배치할 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --deactivation-intent | 노드를 비활성화하는 의도나 이유를 설명합니다. 가능한 값은 다음과 같습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-enable"></a>sfctl node enable
현재 비활성화되어 있는 Microsoft Azure Service Fabric 클러스터 노드를 활성화합니다.

현재 비활성화되어 있는 Microsoft Azure Service Fabric 클러스터 노드를 활성화합니다. 일단 활성화되면 노드는 다시 새로운 복제본을 배치하는 것이 가능한 대상이 되고 노드에 남아 있는 모든 비활성화된 복제본은 다시 활성화됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-health"></a>sfctl node health
Microsoft Azure Service 패브릭 노드의 상태를 가져옵니다.

Microsoft Azure Service 패브릭 노드의 상태를 가져옵니다. 상태에 따라 노드에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다. 이름으로 지정한 노드가 상태 저장소에 존재하지 않을 경우 오류가 반환됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name       [필수] | 노드의 이름입니다. |
| --events-health-state-filter | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-info"></a>sfctl node info
Microsoft Azure Service Fabric 클러스터에서 특정 노드에 관한 정보를 가져옵니다.

응답에는 이름, 상태, ID, 상태, 작동 `시간 및 노드에 대한 기타 세부 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-list"></a>sfctl node list
Microsoft Azure Service Fabric 클러스터에서 노드 목록을 가져옵니다.

응답에는 이름, 상태, ID, 상태, 작동 `시간 및 노드에 대한 기타 세부 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --max-results | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다. |
| --node-status-filter | NodeStatus에 따라 노드를 필터링할 수 있습니다. 지정한 필터 값과 일치하는 노드만 반환됩니다. 필터 값은 다음 중 하나일 수 있습니다.  기본값\: 기본값입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-load"></a>sfctl node load
Microsoft Azure Service 패브릭 노드의 부하 정보를 가져옵니다.

정의된 로드 또는 용량이 있는 모든 메트릭에 대한 Service Fabric 노드의 로드 정보를 검색합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-remove-state"></a>sfctl node remove-state
노드의 지속된 상태가 영구적으로 제거되거나 손실되었음을 Microsoft Azure Service Fabric에 알립니다.

해당 노드의 지속된 상태를 복구할 수 없다는 것을 의미합니다. 일반적으로 하드 디스크가 초기화 정리되거나 하드 디스크의 작동이 중단하는 경우에 발생합니다. 이 작업을 성공시키려면 노드는 중단되어야 합니다. 이 작업을 사용하면 Service Fabric에서 해당 노드의 복제본이 더 이상 존재하지 않으며, Service Fabric에서 해당 복제본이 다시 작동하기를 기다리는 것을 중지해야 함을 알 수 있습니다. 노드의 상태가 제거되지 않고 노드가 해당 상태 그대로 다시 작동할 수 있는 경우 이 cmdlet을 실행하지 마십시오.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Microsoft Azure Service 패브릭 노드에 관한 상태 보고서를 보냅니다.

지정된 Service Fabric 노드의 상태를 보고합니다. 보고서는 상태 보고서의 원본 및 보고되는 속성에 대한 정보를 포함해야 합니다. 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 노드에 전송됩니다. 보고서는 게이트웨이에 의해 허용되지만 추가 유효성 검사 후 Health 스토어에 의해 거부될 수 있습니다. 예를 들어 Health 스토어는 부실 시퀀스 번호와 같은 잘못된 매개 변수로 인해 보고서를 거부할 수도 있습니다. 보고서가 Health 스토어에서 적용되었는지 여부를 확인하려면 해당 보고서가 HealthEvents 섹션에 표시되는지 확인합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --health-property [필수] | 상태 정보의 속성입니다. <br><br> 엔터티는 서로 다른 속성에 대한 상태 보고서를 가질 수 있습니다. 속성은 문자열이며 보고자 유연성에서 보고서를 트리거하는 상태 조건을 분류하도록 허용하는 고정된 열거형이 아닙니다. 예를 들어 SourceId "LocalWatchdog"가 있는 보고자는 노드에서 사용 가능한 디스크의 상태를 모니터링할 수 있으므로 해당 노드에서 "AvailableDisk" 속성을 보고할 수 있습니다. 동일한 보고자는 노드 연결을 모니터링할 수 있으므로 동일한 노드에서 "Connectivity" 속성을 보고할 수 있습니다. Health 스토어에서 이러한 보고서는 지정된 노드에 대한 별도의 상태 이벤트로 처리됩니다. SourceId와 함께 속성은 상태 정보를 고유하게 식별합니다. |
| --health-state    [필수] | 가능한 값\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --node-name       [필수] | 노드의 이름입니다. |
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

## <a name="sfctl-node-restart"></a>sfctl node restart
Microsoft Azure Service Fabric 클러스터 노드를 다시 시작합니다.

이미 시작된 Microsoft Azure Service Fabric 클러스터 노드를 다시 시작합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --create-fabric-dump | 패브릭 노드 프로세스의 덤프를 만들려면 True를 지정합니다. 대/소문자를 구분합니다.  기본값\: False. |
| --node-instance-id | 대상 노드의 인스턴스 ID입니다. 인스턴스 ID가 지정된 경우 노드가 노드의 현재 인스턴스와 일치하는 경우에만 다시 시작됩니다. "0"의 기본값은 모든 인스턴스 ID와 일치할 것입니다. 인스턴스 ID는 노드 가져오기 쿼리를 사용하여 구할 수 있습니다.  기본값\: 0. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-transition"></a>sfctl node transition
클러스터 노드를 시작하거나 중지합니다.

클러스터 노드를 시작하거나 중지합니다.  클러스터 노드는 프로세스이지 OS 인스턴스 그 자체가 아닙니다.  노드를 시작하려면 NodeTransitionType 매개 변수에 대한 "Start"을 전달합니다. 노드를 중지하려면 NodeTransitionType 매개 변수에 대한 "Stop"을 전달합니다. API가 아직 전환을 마치지 못한 노드를 반환할 때 이 API가 작업을 시작합니다. 작업의 진행률을 가져오려면 동일한 OperationId로 GetNodeTransitionProgress를 호출합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-instance-id         [필수] | 대상 노드의 노드 인스턴스 ID입니다. 이것은 GetNodeInfo API를 통해 확인할 수 있습니다. |
| --node-name                [필수] | 노드의 이름입니다. |
| --node-transition-type     [필수] | 수행할 전환의 유형을 나타냅니다.  NodeTransitionType.Start는 중지된 노드를 시작합니다. NodeTransitionType.Stop은 작동하는 노드를 중지합니다. |
| --operation-id             [필수] | 이 API의 호출을 식별하는 GUID입니다.  이 값은 해당 GetProgress API에 전달됩니다. |
| --stop-duration-in-seconds [필수] | 노드가 중지된 채로 유지되는 기간(초).  최솟값은 600, 최댓값은 14400입니다.  이 시간이 만료되면 노드가 자동으로 다시 작동합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
StartNodeTransition을 사용하여 시작된 작업의 진행률을 가져옵니다.

제공 된 OperationId를 사용하여 StartNodeTransition으로 시작되는 작업의 진행률을 가져옵니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수] | 노드의 이름입니다. |
| --operation-id [필수] | 이 API의 호출을 식별하는 GUID입니다.  이 값은 해당 GetProgress API에 전달됩니다. |
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