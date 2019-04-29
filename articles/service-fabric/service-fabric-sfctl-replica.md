---
title: Azure Service Fabric CLI- sfctl 복제본 | Microsoft Docs
description: Service Fabric CLI sfctl 복제본 명령을 설명합니다.
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
ms.openlocfilehash: d0a7199ff0e9cb17c3fbc179a9b37a6620f521f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544673"
---
# <a name="sfctl-replica"></a>sfctl 복제본
서비스 파티션에 속한 복제본을 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| 배포됨 | Service Fabric 노드에서 배포된 복제본의 세부 정보를 가져옵니다. |
| deployed-list | Service Fabric 노드에서 배포된 복제본 목록을 가져옵니다. |
| health | Service Fabric 상태 저장 서비스 복제본 또는 상태 비저장 서비스 인스턴스를 가져옵니다. |
| info | Service Fabric 파티션의 복제본에 대한 정보를 가져옵니다. |
| list | Service Fabric 서비스 파티션 복제본에 대한 정보를 가져옵니다. |
| 제거 | 노드에서 실행되는 서비스 복제본을 제거합니다. |
| report-health | Service Fabric 복제본에 대한 상태 보고서를 보냅니다. |
| restart | 노드에서 실행되는 지속된 서비스의 서비스 복제본을 다시 시작합니다. |

## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Service Fabric 노드에서 배포된 복제본의 세부 정보를 가져옵니다.

Service Fabric 노드에서 배포된 복제본의 세부 정보를 가져옵니다. 정보로는 서비스 종류, 서비스 이름, 현재 서비스 작업, 현재 서비스 작업 시작 날짜 시간, 파티션 ID, 복제본/인스턴스 ID, 보고된 로드 및 기타 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수] | 노드의 이름입니다. |
| --partition-id [필수] | 파티션의 id입니다. |
| --replica-id   [필수] | 복제본의 식별자입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-replica-deployed-list"></a>sfctl replica deployed-list
Service Fabric 노드에서 배포된 복제본 목록을 가져옵니다.

Service Fabric 노드에 배포된 복제본에 대한 정보를 포함하는 목록을 가져옵니다. 정보는 파티션 ID, 복제본 ID, 복제본의 상태, 서비스의 이름, 서비스 유형의 이름 및 기타 정보를 포함합니다. PartitionId 또는 ServiceManifestName 쿼리 매개 변수를 사용하여 이러한 매개 변수에 지정된 값과 일치하는 배포된 복제본에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --partition-id | 파티션의 id입니다. |
| --service-manifest-name | Service Fabric 클러스터에 애플리케이션 유형의 일부로 등록된 서비스 매니페스트의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-replica-health"></a>sfctl 복제본 상태
Service Fabric 상태 저장 서비스 복제본 또는 상태 비저장 서비스 인스턴스를 가져옵니다.

Service Fabric 복제본의 상태를 가져옵니다. 상태에 따라 복제본에 보고된 상태 이벤트의 컬렉션을 필터링하려면 EventsHealthStateFilter를 사용합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id    [필수] | 파티션의 id입니다. |
| --replica-id      [필수] | 복제본의 식별자입니다. |
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

## <a name="sfctl-replica-info"></a>sfctl 복제본 정보
Service Fabric 파티션의 복제본에 대한 정보를 가져옵니다.

응답에는 ID, 역할, 상태, 상태, 노드 이름, 작동 시간 및 기타 복제본에 관한 세부 정보가 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수] | 파티션의 id입니다. |
| --replica-id   [필수] | 복제본의 식별자입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-replica-list"></a>sfctl 복제본 목록
Service Fabric 서비스 파티션 복제본에 대한 정보를 가져옵니다.

GetReplicas 엔드포인트는 지정된 파티션의 복제본에 관한 정보를 반환합니다. 응답에는 ID, 역할, 상태, 상태, 노드 이름, 작동 시간 및 기타 복제본에 관한 세부 정보가 들어 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --partition-id [필수] | 파티션의 id입니다. |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-replica-remove"></a>sfctl 복제본 제거
노드에서 실행되는 서비스 복제본을 제거합니다.

이 API는 Service Fabric 클러스터에서 복제본을 제거하여 Service Fabric 복제 오류를 시뮬레이션합니다. 제거는 복제본을 닫고, 복제본을 None 역할로 전환한 다음, 클러스터에서 복제본의 상태 정보 모두를 제거합니다. 이 API는 복제 상태 제거 경로를 테스트하고, 클라이언트 API를 통해 보고서 오류 영구 경로를 시뮬레이션합니다. 경고 ‑ 이 API가 사용될 때 수행되는 안전 검사가 없습니다. 이 API를 잘못 사용하면 상태 저장 서비스에 대한 데이터가 손실될 수 있습니다. 또한 forceRemove 플래그는 동일한 프로세스에서 호스팅되는 다른 모든 복제본에 영향을 줍니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수] | 노드의 이름입니다. |
| --partition-id [필수] | 파티션의 id입니다. |
| --replica-id   [필수] | 복제본의 식별자입니다. |
| --force-remove | 정상적인 종료 시퀀스를 거치지 않고 강제로 Service Fabric 애플리케이션이나 서비스를 제거합니다. 이 매개 변수는 복제본을 정상적으로 종료하지 못하게 하는 서비스 코드의 문제로 인해 삭제 시간이 초과되는 애플리케이션이나 서비스를 강제로 삭제하는 데 사용할 수 있습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
Service Fabric 복제본에 대한 상태 보고서를 보냅니다.

지정된 Service Fabric 복제본의 상태를 보고합니다. 보고서는 상태 보고서의 원본 및 보고되는 속성에 대한 정보를 포함해야 합니다. 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 복제본에 전송됩니다. 보고서는 게이트웨이에 의해 허용되지만 추가 유효성 검사 후 Health 스토어에 의해 거부될 수 있습니다. 예를 들어 Health 스토어는 부실 시퀀스 번호와 같은 잘못된 매개 변수로 인해 보고서를 거부할 수도 있습니다. 보고서가 Health 스토어에서 적용되었는지 여부를 확인하려면 get replica health를 실행하고 해당 보고서가 HealthEvents 섹션에 표시되는지 확인합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --health-property [필수] | 상태 정보의 속성입니다. <br><br> 엔터티는 서로 다른 속성에 대한 상태 보고서를 가질 수 있습니다. 속성은 문자열이며 보고자 유연성에서 보고서를 트리거하는 상태 조건을 분류하도록 허용하는 고정된 열거형이 아닙니다. 예를 들어 SourceId "LocalWatchdog"가 있는 보고자는 노드에서 사용 가능한 디스크의 상태를 모니터링할 수 있으므로 해당 노드에서 "AvailableDisk" 속성을 보고할 수 있습니다. 동일한 보고자는 노드 연결을 모니터링할 수 있으므로 동일한 노드에서 "Connectivity" 속성을 보고할 수 있습니다. Health 스토어에서 이러한 보고서는 지정된 노드에 대한 별도의 상태 이벤트로 처리됩니다. SourceId와 함께 속성은 상태 정보를 고유하게 식별합니다. |
| --health-state    [필수] | 가능한 값\: 'Invalid', 'Ok', 'Warning', 'Error', 'Unknown'. |
| --partition-id    [필수] | 파티션의 id입니다. |
| --replica-id      [필수] | 파티션의 id입니다. |
| --source-id       [필수] | 상태 정보를 생성한 클라이언트/watchdog/시스템 구성 요소를 식별하는 원본 이름입니다. |
| --description | 상태 정보의 설명입니다. <br><br> 보고서에 대한 사람이 읽을 수 있는 정보를 추가하는 데 사용되는 일반 텍스트를 나타냅니다. 설명의 최대 문자열 길이는 4096자입니다. 제공된 문자열이 긴 경우 자동으로 잘립니다. 잘린 경우 설명의 마지막 문자는 "[잘림]" 표식을 포함하고, 총 문자열 크기는 4096자입니다. 표식은 사용자에게 잘림이 발생했음을 나타냅니다. 잘린 경우 설명은 원래 문자열의 4096자보다 작은 문자를 갖습니다. |
| --immediate | 보고서를 즉시 보낼지 여부를 나타내는 플래그입니다. <br><br> 상태 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 애플리케이션에 전송됩니다. Immediate가 true로 설정된 경우 보고서는 HTTP 게이트웨이 애플리케이션이 사용 중인 패브릭 클라이언트 설정에 관계 없이 HTTP 게이트웨이에서 Health 스토어로 즉시 전송됩니다. 이는 가능한 한 빨리 보내야 하는 중요한 보고서에 유용합니다. 타이밍 및 기타 조건에 따라 보고서 전송이 여전히 실패할 수 있습니다. 예를 들어, HTTP 게이트웨이가 닫혀 있거나 메시지가 게이트웨이에 도달하지 않는 경우입니다. Immediate가 false로 설정된 경우 보고서는 HTTP 게이트웨이의 상태 클라이언트 설정에 따라 전송됩니다. 따라서 HealthReportSendInterval 구성에 따라 일괄처리됩니다. 상태 클라이언트가 상태 보고서 처리뿐만 아니라 Health 스토어로 메시지를 보고하는 상태를 최적화하도록 허용하므로 이는 권장 설정입니다. 기본적으로 보고서는 즉시 전송되지 않습니다. |
| --remove-when-expired | 보고서가 만료될 때 Health 스토어에서 제거할지 여부를 나타내는 값입니다. <br><br> true로 설정된 경우 보고서는 만료된 후 Health 스토어에서 제거됩니다. false로 설정된 경우 보고서는 만료될 때 오류로 처리됩니다. 이 속성의 값은 기본적으로 false입니다. 클라이언트가 주기적으로 보고하는 경우 RemoveWhenExpired를 false(기본값)로 설정해야 합니다. 이러한 방식은 문제가 있고(예: 교착 상태) 보고할 수 없는 보고자이며, 엔터티는 상태 보고서가 만료되는 경우 오류로 평가됩니다. 엔터티를 오류 성능 상태에 있는 것으로 플래그 지정합니다. |
| --sequence-number | 숫자 문자열의 이 상태 보고서에 대한 일련 번호입니다. <br><br> 보고서 일련 번호는 Health 스토어에서 유효하지 않은 보고서를 검색하는 데 사용됩니다. 지정되지 않은 경우 일련 번호는 보고서가 추가될 때 상태 클라이언트에서 자동으로 생성됩니다. |
| --service-kind | 상태가 보고되는 서비스 복제본의 종류입니다(상태 비저장 또는 상태 저장). 다음은 가능한 값\: '상태 비저장', '상태 저장'.  기본값\: 상태 저장 |
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

## <a name="sfctl-replica-restart"></a>sfctl 복제본 다시 시작
노드에서 실행되는 지속된 서비스의 서비스 복제본을 다시 시작합니다.

노드에서 실행되는 지속된 서비스의 서비스 복제본을 다시 시작합니다. 경고 ‑ 이 API가 사용될 때 수행되는 안전 검사가 없습니다. 이 API를 잘못 사용하면 상태 저장 서비스에 대한 가용성이 손실될 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name    [필수] | 노드의 이름입니다. |
| --partition-id [필수] | 파티션의 id입니다. |
| --replica-id   [필수] | 복제본의 식별자입니다. |
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
