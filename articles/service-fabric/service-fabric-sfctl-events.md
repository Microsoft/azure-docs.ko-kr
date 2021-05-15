---
title: Azure Service Fabric CLI - sfctl 이벤트
description: Service Fabric CLI sfctl 이벤트 명령에 대해 설명합니다.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76906098"
---
# <a name="sfctl-events"></a>sfctl 이벤트
이벤트 저장소에서 이벤트를 검색합니다(EventStore 서비스가 이미 설치된 경우).

6\.4 이상을 실행하는 임의의 SFRP 클러스터로 구성을 업그레이드하여 EventStore 시스템 서비스를 추가할 수 있습니다. 다음 URL\: https\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore를 확인하세요.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| all-applications-list | 모든 애플리케이션 관련 이벤트를 가져옵니다. |
| all-nodes-list | 모든 노드 관련 이벤트를 가져옵니다. |
| all-partitions-list | 모든 파티션 관련 이벤트를 가져옵니다. |
| all-services-list | 모든 서비스 관련 이벤트를 가져옵니다. |
| application-list | 애플리케이션 관련 이벤트를 가져옵니다. |
| cluster-list | 모든 클러스터 관련 이벤트를 가져옵니다. |
| node-list | 노드 관련 이벤트를 가져옵니다. |
| partition-all-replicas-list | 파티션에 대한 모든 복제본 관련 이벤트를 가져옵니다. |
| partition-list | 파티션 관련 이벤트를 가져옵니다. |
| partition-replica-list | 파티션 복제본 관련 이벤트를 가져옵니다. |
| service-list | 서비스 관련 이벤트를 가져옵니다. |

## <a name="sfctl-events-all-applications-list"></a>sfctl 이벤트 all-applications-list
모든 애플리케이션 관련 이벤트를 가져옵니다.

응답은 ApplicationEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-all-nodes-list"></a>sfctl 이벤트 all-nodes-list
모든 노드 관련 이벤트를 가져옵니다.

응답은 NodeEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl 이벤트 all-partitions-list
모든 파티션 관련 이벤트를 가져옵니다.

응답은 파티션 이벤트 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-all-services-list"></a>sfctl 이벤트 all-services-list
모든 서비스 관련 이벤트를 가져옵니다.

응답은 ServiceEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-application-list"></a>sfctl 이벤트 application-list
애플리케이션 관련 이벤트를 가져옵니다.

응답은 ApplicationEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-cluster-list"></a>sfctl 이벤트 cluster-list
모든 클러스터 관련 이벤트를 가져옵니다.

응답은 ClusterEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-node-list"></a>sfctl 이벤트 node-list
노드 관련 이벤트를 가져옵니다.

응답은 NodeEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl 이벤트 partition-all-replicas-list
파티션에 대한 모든 복제본 관련 이벤트를 가져옵니다.

응답은 ReplicaEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --partition-id   [필수] | 파티션의 id입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-partition-list"></a>sfctl 이벤트 partition-list
파티션 관련 이벤트를 가져옵니다.

응답은 파티션 이벤트 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --partition-id   [필수] | 파티션의 id입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-partition-replica-list"></a>sfctl 이벤트 partition-replica-list
파티션 복제본 관련 이벤트를 가져옵니다.

응답은 ReplicaEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --partition-id   [필수] | 파티션의 id입니다. |
| --replica-id[필수] | 복제본의 식별자입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

## <a name="sfctl-events-service-list"></a>sfctl 이벤트 service-list
서비스 관련 이벤트를 가져옵니다.

응답은 ServiceEvent 개체의 목록입니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --end-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 종료 시간입니다. |
| --service-id     [필수] | 서비스 id입니다. 이 ID는 일반적으로 'fabric\:' URI 스키마가 없는 서비스의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 서비스 이름이 "fabric\:/myapp/app1/svc1"이면 서비스 ID는 6.0 이상에서는 "myapp\~app1\~svc1"이고 이전 버전에서는 "myapp/app1/svc1"입니다. |
| --start-time-utc[필수] | ISO UTC yyyy-MM-ddTHH\:mm\:ssZ의 조회 쿼리 시작 시간입니다. |
| --events-types-filter | 응답에만 포함되어야 하는 FabricEvents 유형을 지정하는 쉼표로 구분된 문자열입니다. |
| --exclude-analysis-events | True가 전달되면 해당 매개 변수는 AnalysisEvents 검색을 사용하지 않도록 설정합니다. |
| --skip-correlation-lookup | True가 전달되면 해당 매개 변수는 CorrelatedEvents 정보 검색을 사용하지 않도록 설정합니다. 그러지 않으면 CorrelationEvents가 처리되고 모든 FabricEvent의 HasCorrelatedEvents 필드가 채워집니다. |
| --timeout -t | 작업을 수행하기 위한 서버 제한 시간(초)입니다. 이 시간 제한은 요청된 작업이 완료될 때까지 클라이언트가 기다릴 시간을 지정합니다. 이 매개 변수의 기본값은 60초입니다.  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 로깅의 자세한 정도를 늘려 모든 디버그 로그를 표시합니다. |
| --help -h | 이 도움말 메시지를 표시하고 종료합니다. |
| --output -o | 출력 형식입니다.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열입니다. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그를 표시하려면 --debug를 사용합니다. |

