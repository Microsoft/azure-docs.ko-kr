---
title: Microsoft Azure Service Fabric CLI- sfctl 애플리케이션 | Microsoft Docs
description: Service Fabric CLI sfctl 애플리케이션 명령을 설명합니다.
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
ms.openlocfilehash: d4fec5d8131d269d3df229360066452c37a92430
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837490"
---
# <a name="sfctl-application"></a>sfctl application
애플리케이션 및 애플리케이션 유형을 성, 삭제 및 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| create | 지정한 설명을 사용하여 Microsoft Azure Service Fabric 애플리케이션을 만듭니다. |
| delete | 기존 Microsoft Azure Service Fabric 애플리케이션을 삭제합니다. |
| 배포됨 | Microsoft Azure Service Fabric 노드에서 배포된 애플리케이션에 대한 정보를 가져옵니다. |
| deployed-health | Service Fabric 노드에 배포된 애플리케이션의 상태에 대한 정보를 가져옵니다. |
| deployed-list | Microsoft Azure Service Fabric 노드에서 배포된 애플리케이션의 목록을 가져옵니다. |
| health | Microsoft Azure Service Fabric 애플리케이션의 상태를 가져옵니다. |
| info | Microsoft Azure Service Fabric 애플리케이션에 대한 정보를 가져옵니다. |
| list | 지정된 필터와 일치하는 Service Fabric 클러스터에서 만든 애플리케이션 목록을 가져옵니다. |
| load | Microsoft Azure Service Fabric 애플리케이션에 대한 로드 정보를 가져옵니다. |
| manifest | 애플리케이션 유형을 설명하는 매니페스트를 가져옵니다. |
| provision | 외부 저장소의 .sfpkg 패키지를 사용하거나 이미지 저장소의 애플리케이션 패키지를 사용하는 클러스터를 통해 Service Fabric 애플리케이션 유형을 프로비전하거나 등록합니다. |
| report-health | Microsoft Azure Service Fabric 애플리케이션에 대한 상태 보고서를 보냅니다. |
| 형식 | 지정된 이름과 정확히 일치하는 Microsoft Azure Service Fabric 클러스터에서 애플리케이션 종류의 목록을 가져옵니다. |
| type-list | Microsoft Azure Service Fabric 클러스터에서 애플리케이션의 유형 목록을 가져옵니다. |
| unprovision | 클러스터에서 Microsoft Azure Service Fabric 애플리케이션 유형을 제거하거나 등록 취소합니다. |
| 업그레이드 | Microsoft Azure Service Fabric 클러스터에서 애플리케이션 업그레이드를 시작합니다. |
| upgrade-resume | Microsoft Azure Service Fabric 클러스터에 애플리케이션을 다시 업그레이드하기 시작합니다. |
| upgrade-rollback | Service Fabric 클러스터에서 현재 진행 중인 애플리케이션 업그레이드의 롤백을 시작합니다. |
| upgrade-status | 이 애플리케이션에 수행된 최신 업그레이드에 대한 세부 정보를 가져옵니다. |
| upload | Microsoft Azure Service Fabric 애플리케이션 패키지를 이미지 저장소에 복사합니다. |

## <a name="sfctl-application-create"></a>sfctl application create
지정한 설명을 사용하여 Microsoft Azure Service Fabric 애플리케이션을 만듭니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-name    [필수] | '패브릭\:' URI 체계를 포함하는, 애플리케이션의 이름입니다. |
| --app-type    [필수] | 애플리케이션 매니페스트에 있는 애플리케이션 유형 이름입니다. |
| --app-version [필수] | 애플리케이션 매니페스트에서 정의된 애플리케이션 유형의 버전입니다. |
| --max-node-count | Service Fabric이 이 애플리케이션에 대해 용량을 예약하는 노드의 최대 수입니다. 이 애플리케이션의 서비스가 모든 해당 노드에 배치되지는 않습니다. |
| --metrics | JSON 인코딩된 애플리케이션 용량 메트릭 설명 목록입니다. 메트릭은 애플리케이션이 있는 각각의 노드에 대한 용량의 집합과 연결된 이름으로 정의됩니다. |
| --min-node-count | Service Fabric이 이 애플리케이션에 대해 용량을 예약하는 노드의 최소 수입니다. 이 애플리케이션의 서비스가 모든 해당 노드에 배치되지는 않습니다. |
| --parameters | JSON 인코딩된 애플리케이션 매개 변수 목록은 애플리케이션을 만들 때 적용되기 위해 재정의합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-delete"></a>sfctl application delete
기존 Microsoft Azure Service Fabric 애플리케이션을 삭제합니다.

애플리케이션을 삭제하려면 먼저 만들어야 합니다. 애플리케이션을 삭제하면 해당 애플리케이션의 일부인 모든 서비스가 삭제됩니다. 기본적으로 Service Fabric은 서비스 복제를 정상적으로 닫은 후 다음 서비스를 삭제합니다. 하지만 서비스에서 복제본을 정상적으로 닫는 데 문제가 있으면 삭제 작업이 오래 걸리거나 중단될 수 있습니다. 선택적인 ForceRemove 플래그를 사용하여 정상적인 닫기 시퀀스를 건너 뛰고 애플리케이션 및 모든 서비스를 강제로 삭제합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
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

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Microsoft Azure Service Fabric 노드에서 배포된 애플리케이션에 대한 정보를 가져옵니다.

이 쿼리는 제공되는 애플리케이션 ID가 시스템 애플리케이션용인 경우 시스템 애플리케이션 정보를 반환합니다. 결과는 활성, 활성화 중 및 상태 다운로드 중인 배포된 애플리케이션을 포함합니다. 이 쿼리에서는 노드 이름이 클러스터의 노드에 해당해야 합니다. 제공된 노드 이름이 클러스터의 활성 Service Fabric 노드를 가리키지 않으면 쿼리가 실패합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name [필수] | 노드의 이름입니다. |
| --include-health-state | 엔터티의 상태를 포함합니다. 이 매개 변수가 false이거나 지정되지 않은 경우 반환된 상태는 "Unknown"입니다. true로 설정된 경우 쿼리는 결과가 병합되기 전에 노드 및 상태 시스템 서비스에 병렬로 들어갑니다. 결과적으로 쿼리는 비용이 더 들며 더 긴 시간이 걸릴 수 있습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-deployed-health"></a>sfctl application deployed-health
Service Fabric 노드에 배포된 애플리케이션의 상태에 대한 정보를 가져옵니다.

Service Fabric 노드에 배포된 애플리케이션의 상태에 대한 정보를 가져옵니다. EventsHealthStateFilter를 사용하여 필요에 따라 상태에 따라 배포된 애플리케이션에 보고된 HealthEvent 개체의 컬렉션에 대해 필터링합니다. DeployedServicePackagesHealthStateFilter를 사용하여 필요에 따라 상태를 기반으로 한 DeployedServicePackageHealth 자식에 대해 필터링합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id                     [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --node-name                          [필수] | 노드의 이름입니다. |
| --deployed-service-packages-health-state-filter | 배포된 서비스 패키지 상태 개체의 필터링이 상태를 기반으로 한 배포된 애플리케이션 상태 쿼리의 결과로 반환되도록 허용합니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 배포된 서비스 패키지만 반환됩니다. 모든 배포된 서비스 패키지는 배포된 애플리케이션의 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 'OR' 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 서비스 패키지의 상태가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --events-health-state-filter | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --exclude-health-statistics | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔터티 수를 보여줍니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-deployed-list"></a>sfctl application deployed-list
Microsoft Azure Service Fabric 노드에서 배포된 애플리케이션의 목록을 가져옵니다.

Microsoft Azure Service Fabric 노드에서 배포된 애플리케이션의 목록을 가져옵니다. 결과는 ID별에 대해 명시적으로 쿼리되지 않는 한 배포된 시스템 애플리케이션에 대한 정보를 포함하지 않습니다. 결과는 활성, 활성화 중 및 상태 다운로드 중인 배포된 애플리케이션을 포함합니다. 이 쿼리에서는 노드 이름이 클러스터의 노드에 해당해야 합니다. 제공된 노드 이름이 클러스터의 활성 Service Fabric 노드를 가리키지 않으면 쿼리가 실패합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --node-name [필수] | 노드의 이름입니다. |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --include-health-state | 엔터티의 상태를 포함합니다. 이 매개 변수가 false이거나 지정되지 않은 경우 반환된 상태는 "Unknown"입니다. true로 설정된 경우 쿼리는 결과가 병합되기 전에 노드 및 상태 시스템 서비스에 병렬로 들어갑니다. 결과적으로 쿼리는 비용이 더 들며 더 긴 시간이 걸릴 수 있습니다. |
| --max-results | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-health"></a>sfctl application health
Microsoft Azure Service Fabric 애플리케이션의 상태를 가져옵니다.

Microsoft Azure Service Fabric 애플리케이션의 상태를 반환합니다. 응답은 확인, 오류 또는 상태 경고를 보고합니다. 엔터티를 Health 스토어에서 찾을 수 없다면 Error를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id                 [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --deployed-applications-health-state-filter | 배포된 애플리케이션 상태 개체의 필터링이 상태를 기반으로 한 애플리케이션 상태 쿼리의 결과값으로 반환되도록 허용합니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 배포된 애플리케이션만 반환됩니다. 모든 배포된 애플리케이션은 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 배포된 애플리케이션의 상태가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --events-health-state-filter | 상태에 따라 반환된 HealthEvent 개체의 컬렉션을 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 이벤트만 반환됩니다. 모든 이벤트는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 모든 이벤트가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --exclude-health-statistics | 상태 통계가 쿼리 결과의 일부로 반환되어야 하는지 여부를 나타냅니다. False(기본값). 통계는 Ok, Warning 및 Error 상태의 자식 엔터티 수를 보여줍니다. |
| --services-health-state-filter | 상태에 따라 서비스 상태 쿼리의 결과로 반환된 서비스 상태 개체를 필터링할 수 있습니다. 이 매개 변수에 사용할 수 있는 값은 다음 상태 중 하나의 정수 값을 포함합니다. 필터와 일치하는 서비스만 반환됩니다. 모든 서비스는 집계된 상태를 평가하는 데 사용됩니다. 지정하지 않으면 모든 항목이 반환됩니다. 상태 값은 플래그 기반 열거형이므로 값은 비트 OR 연산자를 사용하여 구한 값의 조합일 수 있습니다. 예를 들어 제공된 값이 6이면 HealthState 값이 OK(2) 및 Warning(4)인 서비스 상태가 반환됩니다.  <br> - Default - 기본값. 모든 HealthState와 일치합니다. 값은 0입니다.  <br> - None - 모든 HealthState 값과 일치하지 않는 필터입니다. 주어진 상태 컬렉션에 대해 결과를 반환하지 않기 위해 사용됩니다. 값은 1입니다.  <br> - Ok – HealthState 값이 Ok인 입력과 일치하는 필터입니다. 값은 2입니다.  <br> - Warning – HealthState 값이 Warning인 입력과 일치하는 필터입니다. 값은 4입니다.  <br> - Error - HealthState 값이 Error인 입력과 일치하는 필터입니다. 값은 8입니다.  <br> - All - 모든 HealthState 값의 입력과 일치하는 필터입니다. 값은 65535입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-info"></a>sfctl application info
Microsoft Azure Service Fabric 애플리케이션에 대한 정보를 가져옵니다.

Microsoft Azure Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 이름이 매개 변수로 지정된 것과 일치하는 애플리케이션에 대한 정보를 반환합니다. 응답에는 이름, 유형, 상태, 매개 변수 및 애플리케이션에 관한 기타 세부 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id      [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --exclude-application-parameters | 애플리케이션 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-list"></a>sfctl application list
지정된 필터와 일치하는 Service Fabric 클러스터에서 만든 애플리케이션 목록을 가져옵니다.

Microsoft Azure Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 지정된 필터와 일치하는 애플리케이션에 대한 정보를 가져옵니다. 응답에는 이름, 유형, 상태, 매개 변수 및 애플리케이션에 관한 기타 세부 정보가 포함됩니다. 애플리케이션이 한 페이지에 맞지 않는 경우, 결과 한 페이지가 연속 토큰으로 반환되어 다음 페이지를 가져오는 데 사용될 수 있습니다. 필터 ApplicationTypeName 및 ApplicationDefinitionKindFilter는 동시에 지정할 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-definition-kind-filter | Service Fabric 애플리케이션을 정의하는 데 사용되는 메커니즘인 ApplicationDefinitionKind에서 필터링하는 데 사용합니다.  <br> - 기본 - 기본값으로, “All”을 선택할 때와 동일한 함수를 수행합니다. 값은 0입니다.  <br> - All - 입력과 일치하는 모든 ApplicationDefinitionKind 값을 검색하는 필터입니다. 값은 65535입니다.  <br> - ServiceFabricApplicationDescription - 입력과 일치하는 ApplicationDefinitionKind 값 ServiceFabricApplicationDescription을 검색하는 필터입니다. 값은 1입니다.  <br> - Compose - 입력과 일치하는 ApplicationDefinitionKind 값 Compose를 검색하는 필터입니다. 값은 2입니다. |
| --application-type-name | 쿼리할 애플리케이션을 필터링하는 데 사용되는 애플리케이션 유형 이름입니다. 이 값은 애플리케이션 유형 버전을 포함할 수 없습니다. |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --exclude-application-parameters | 애플리케이션 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다. |
| --max-results | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-load"></a>sfctl application load
Microsoft Azure Service Fabric 애플리케이션에 대한 로드 정보를 가져옵니다.

Microsoft Azure Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 이름이 매개 변수로 지정된 것과 일치하는 애플리케이션에 대한 로드 정보를 반환합니다. 응답에는 이름, 최소 노드, 최대 노드, 애플리케이션이 현재 차지하는 노드 수, 애플리케이션에 대한 애플리케이션 부하 메트릭 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-manifest"></a>sfctl application manifest
애플리케이션 유형을 설명하는 매니페스트를 가져옵니다.

응답에 애플리케이션 매니페스트 XML이 문자열로 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name    [필수] | 애플리케이션 유형의 이름입니다. |
| --application-type-version [필수] | 애플리케이션 유형의 버전입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-provision"></a>sfctl application provision
외부 저장소의 .sfpkg 패키지를 사용하거나 이미지 저장소의 애플리케이션 패키지를 사용하는 클러스터를 통해 Service Fabric 애플리케이션 유형을 프로비전하거나 등록합니다.

클러스터를 통해 Service Fabric 애플리케이션 유형을 프로비전합니다. 이것이 있어야 새로운 애플리케이션을 인스턴스화할 수 있습니다. 프로비전 작업은 relativePathInImageStore 또는 외부 .sfpkg의 URI를 사용하여 지정된 애플리케이션 패키지에서 수행할 수 있습니다. 외부 프로비전이 설정되지 않은 경우 이 명령은 이미지 저장소 프로비전이 필요합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --애플리케이션-패키지-다운로드-URI | HTTP 또는 HTTPS 프로토콜을 사용하여 애플리케이션 패키지를 다운로드할 수 있는 ‘.sfpkg’ 애플리케이션 패키지의 경로입니다. <br><br> 프로비전 종류 외부 저장소에만 해당합니다. 애플리케이션 패키지는 파일을 다운로드하도록 가져오기 작업을 제공하는 외부 저장소에 저장할 수 있습니다. 지원되는 프로토콜은 HTTP 및 HTTPS이며, 경로는 읽기 권한을 허용해야 합니다. |
| --애플리케이션-형식-빌드-경로 | 프로비전 종류 이미지 저장소에만 해당합니다. 이전 업로드 작업 중 지정된 이미지 저장소에 있는 애플리케이션 패키지에 대한 상대 경로입니다. |
| --application-type-name | 프로비전 종류 외부 저장소에만 해당합니다. 애플리케이션 유형 이름은 애플리케이션 매니페스트에 있는 애플리케이션 유형의 이름을 나타냅니다. |
| --애플리케이션-유형-버전 | 프로비전 종류 외부 저장소에만 해당합니다. 애플리케이션 유형 버전은 애플리케이션 매니페스트에 있는 애플리케이션 유형의 버전을 나타냅니다. |
| --external-provision | 애플리케이션 패키지를 등록하거나 프로비전할 수 있는 위치입니다. 외부 저장소에 이전에 업로드된 애플리케이션 패키지를 위한 프로비전임을 나타냅니다. 애플리케이션 패키지는 확장명이 *.sfpkg로 끝납니다. |
| --no-wait | 프로비전을 비동기적으로 수행할지 여부를 나타냅니다. <br><br> true로 설정한 경우 요청이 시스템에 의해 수락되면 프로비전 작업이 반환되며, 프로비전 작업은 어떠한 시간 제한 없이 계속됩니다. 기본값은 False입니다. 대형 애플리케이션 패키지의 경우 값을 true로 설정하는 것이 좋습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-report-health"></a>sfctl application report-health
Microsoft Azure Service Fabric 애플리케이션에 대한 상태 보고서를 보냅니다.

지정된 Service Fabric 애플리케이션의 상태를 보고합니다. 보고서는 상태 보고서의 원본 및 보고되는 속성에 대한 정보를 포함해야 합니다. 보고서는 Health 스토어에 전달하는 Service Fabric 게이트웨이 애플리케이션에 전송됩니다. 보고서는 게이트웨이에 의해 허용되지만 추가 유효성 검사 후 Health 스토어에 의해 거부될 수 있습니다. 예를 들어 Health 스토어는 부실 시퀀스 번호와 같은 잘못된 매개 변수로 인해 보고서를 거부할 수도 있습니다. 보고서가 Health 스토어에서 적용되었는지 여부를 확인하려면 애플리케이션 상태를 가져오고 해당 보고서가 표시되는지 확인합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id  [필수] | 애플리케이션 ID입니다. <br><br> 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 '\~' 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 'fabric\:/myapp/app1'인 경우 애플리케이션 ID가 6.0 이상에서는 'myapp\~app1'이고 이전 버전에서는 'myapp/app1'입니다. |
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

## <a name="sfctl-application-type"></a>sfctl application type
지정된 이름과 정확히 일치하는 Microsoft Azure Service Fabric 클러스터에서 애플리케이션 종류의 목록을 가져옵니다.

Microsoft Azure Service Fabric 클러스터에 프로비전된 또는 프로비전되는 프로세스에 있는 애플리케이션 유형에 대한 정보를 반환합니다. 이러한 결과는 이름이 매개 변수로 지정된 것과 정확히 일치하며 주어진 쿼리 매개 변수를 준수해야 하는 애플리케이션 유형에서 나온 값입니다. 하나의 애플리케이션 형식으로 반환된 각 버전과 함께 애플리케이션 형식 이름과 일치하는 애플리케이션 형식의 모든 버전입니다. 응답에는 이름, 버전, 상태 및 애플리케이션에 관한 기타 세부 정보가 포함됩니다. 이것은 페이징된 쿼리로 애플리케이션 유형 모두가 한 페이지에 맞지 않는 경우, 결과 한 페이지가 연속 토큰으로 반환되어 다음 페이지를 가져오는 데 사용될 수 있습니다. 예를 들어 10개의 애플리케이션 형식이 있지만 페이지는 처음 3개 애플리케이션 형식에만 맞거나, 최대 결과가 3으로 설정된다면, 3이 반환됩니다. 결과의 나머지 부분에 액세스하려면 다음 쿼리에 반환된 연속 토큰을 사용하여 후속 페이지를 검색합니다. 후속 페이지가 없다면 공백인 연속 토큰이 반환됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name [필수] | 애플리케이션 유형의 이름입니다. |
| --애플리케이션-유형-버전 | 애플리케이션 유형의 버전입니다. |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --exclude-application-parameters | 애플리케이션 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다. |
| --max-results | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-type-list"></a>sfctl application type-list
Microsoft Azure Service Fabric 클러스터에서 애플리케이션의 유형 목록을 가져옵니다.

Microsoft Azure Service Fabric 클러스터에 프로비전된 또는 프로비전되는 프로세스에 있는 애플리케이션 유형에 대한 정보를 반환합니다. 애플리케이션 유형의 각 버전은 하나의 애플리케이션 유형으로 반환됩니다. 응답에는 이름, 버전, 상태 및 애플리케이션에 관한 기타 세부 정보가 포함됩니다. 이것은 페이징된 쿼리로 애플리케이션 유형 모두가 한 페이지에 맞지 않는 경우, 결과 한 페이지가 연속 토큰으로 반환되어 다음 페이지를 가져오는 데 사용될 수 있습니다. 예를 들어 10개의 애플리케이션 형식이 있지만 페이지는 처음 3개 애플리케이션 형식에만 맞거나, 최대 결과가 3으로 설정된다면, 3이 반환됩니다. 결과의 나머지 부분에 액세스하려면 다음 쿼리에 반환된 연속 토큰을 사용하여 후속 페이지를 검색합니다. 후속 페이지가 없다면 공백인 연속 토큰이 반환됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-definition-kind-filter | Service Fabric 애플리케이션 유형을 정의하는 데 사용되는 메커니즘인 ApplicationTypeDefinitionKind에서 필터링하는 데 사용합니다.  <br> - 기본 - 기본값으로, “All”을 선택할 때와 동일한 함수를 수행합니다. 값은 0입니다.  <br> - All - 입력과 일치하는 모든 ApplicationTypeDefinitionKind 값을 검색하는 필터입니다. 값은 65535입니다.  <br> - ServiceFabricApplicationPackage - 입력과 일치하는 ApplicationTypeDefinitionKind 값 ServiceFabricApplicationPackage를 검색하는 필터입니다. 값은 1입니다.  <br> - Compose - 입력과 일치하는 ApplicationTypeDefinitionKind 값 Compose를 검색하는 필터입니다. 값은 2입니다. |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
| --exclude-application-parameters | 애플리케이션 매개 변수가 결과에서 제외될지 여부를 지정하는 플래그입니다. |
| --max-results | 페이징된 쿼리의 일부로 반환될 결과의 최대 수입니다. 이 매개 변수는 반환되는 결과 수에 상한값을 정의합니다. 반환되는 결과는 구성에 정의된 최대 메시지 크기 제한에 따라 메시지에 맞지 않는 경우 지정된 최대의 결과보다 작을 수 있습니다. 이 매개 변수가 0이거나 지정되지 않은 경우 페이징된 쿼리는 반환 메시지에 맞는 가능한 많은 결과를 포함합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
클러스터에서 Microsoft Azure Service Fabric 애플리케이션 유형을 제거하거나 등록 취소합니다.

이 작업은 애플리케이션 형식의 모든 애플리케이션 인스턴스가 삭제된 경우에만 수행될 수 있습니다. 애플리케이션 형식이 등록 취소되면 이 특정 애플리케이션 형식에 대해서는 새 애플리케이션 인스턴스가 만들어질 수 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-type-name    [필수] | 애플리케이션 유형의 이름입니다. |
| --application-type-version [필수] | 애플리케이션 매니페스트에서 정의된 애플리케이션 유형의 버전입니다. |
| --async-parameter | 프로비전 해제를 비동기적으로 수행할지 여부를 나타내는 플래그입니다. true로 설정한 경우 요청이 시스템에 의해 수락되면 프로비전 해제 작업이 반환되며, 프로비전 해제 작업은 어떠한 시간 제한 없이 계속됩니다. 기본값은 False입니다. 단, 프로비전된 대형 애플리케이션 패키지의 경우 true로 설정하는 것이 좋습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Microsoft Azure Service Fabric 클러스터에서 애플리케이션 업그레이드를 시작합니다.

제공된 애플리케이션 업그레이드 매개 변수의 유효성을 검사하고 매개 변수가 유효한 경우 애플리케이션 업그레이드를 시작합니다. 업그레이드 설명이 기존의 애플리케이션 설명을 대체합니다. 즉, 매개 변수가 지정되지 않은 경우 애플리케이션의 기존 매개 변수를 빈 매개 변수 목록으로 덮어씁니다. 이 경우 애플리케이션은 애플리케이션 매니페스트에서의 매개 변수 기본값을 사용합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id      [필수] | 애플리케이션 ID입니다. <br><br> 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --application-version [필수] | 애플리케이션 업그레이드를 위한 대상 애플리케이션 유형 버전(애플리케이션 매니페스트에 있음)입니다. |
| --parameters          [필수] | JSON 인코딩된 애플리케이션 매개 변수 목록은 애플리케이션을 업그레이드할 때 적용되기 위해 재정의합니다. |
| --default-service-health-policy | 기본적으로 서비스 유형의 상태를 평가하는 데 사용되는 JSON 인코딩된 상태 정책 사양입니다. |
| --failure-action | 모니터링된 업그레이드가 모니터링 정책 또는 상태 정책 위반을 발견할 때 수행할 작업입니다. |
| --force-restart | 코드 버전이 변경되지 않은 경우에도 업그레이드 동안 프로세스를 강제로 다시 시작합니다. |
| --health-check-retry-timeout | 애플리케이션 또는 클러스터가 정상이 아닌지 상태 확인을 수행하는 시도의 시간 간격입니다.  기본값\: PT0H10M0S. |
| --health-check-stable-duration | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 애플리케이션 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다.  기본값\: PT0H2M0S. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --health-check-wait-duration | 업그레이드 도메인을 완료한 후 상태 확인 프로세스를 시작하기 전에 대기하는 시간입니다.  기본값\: 0. |
| --max-unhealthy-apps | 비정상적인 배포 애플리케이션의 최대 허용치입니다. 0과 100 사이의 숫자로 표시합니다. |
| --mode | 롤링 업그레이드 동안 상태를 모니터링하는 데 사용되는 모드입니다.  기본값\: UnmonitoredAuto. |
| --replica-set-check-timeout | 예기치 않은 문제가 있을 때 업그레이드 도메인의 처리를 차단하고 가용성의 손실을 방지하는 최대 시간입니다. 초 단위로 측정됩니다. |
| --service-health-policy | 서비스 형식 이름 단위 서비스 형식 상태 정책으로 JSON 인코딩된 맵입니다. 맵은 기본적으로 비어 있습니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --upgrade-domain-timeout | FailureAction이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다.  기본값\: P10675199DT02H48M05.4775807S. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --upgrade-timeout | FailureAction이 실행되기 전에 전체 업그레이드를 완료해야 하는 시간입니다.  기본값\: P10675199DT02H48M05.4775807S. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --warning-as-error | 경고가 오류와 동일한 심각도로 처리되는지 여부를 나타냅니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl application upgrade-resume
Microsoft Azure Service Fabric 클러스터에 애플리케이션을 다시 업그레이드하기 시작합니다.

모니터링되지 않는 수동 Service Fabric 애플리케이션 업그레이드를 다시 시작합니다. Service Fabric은 한 번에 하나의 업그레이드 도메인을 업그레이드합니다. 모니터링되지 않는 수동 업그레이드의 경우 Service Fabric에서 업그레이드 도메인을 완료한 후 다음 업그레이드 도메인을 계속 진행하기 전에 이 API를 호출하도록 기다립니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id      [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --upgrade-domain-name [필수] | 업그레이드를 다시 시작할 업그레이드 도메인의 이름입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl application upgrade-rollback
Service Fabric 클러스터에서 현재 진행 중인 애플리케이션 업그레이드의 롤백을 시작합니다.

이전 버전으로 현재 애플리케이션 업그레이드의 롤백을 시작합니다. 이 API는 새 버전으로 롤포워드되는 현재 진행 중인 업그레이드를 롤백하는 데만 사용할 수 있습니다. 애플리케이션이 현재 업그레이드 중이 아닌 경우 StartApplicationUpgrade API를 사용하여 이전 버전으로 롤백을 포함하여 원하는 버전으로 업그레이드합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-upgrade-status"></a>sfctl application upgrade-status
이 애플리케이션에 수행된 최신 업그레이드에 대한 세부 정보를 가져옵니다.

애플리케이션 상태 문제 디버깅을 지원하기 위한 세부 정보와 함께 최신 애플리케이션 업그레이드의 상태에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --application-id [필수] | 애플리케이션 ID입니다. 일반적으로 'fabric\:' URI 스키마가 없는 애플리케이션의 전체 이름입니다. 버전 6.0에서 시작하며, 계층적 이름이 "\~" 문자로 구분됩니다. 예를 들어 애플리케이션 이름이 “fabric\:/myapp/app1”인 경우 애플리케이션 ID가 6.0 이상에서는 “myapp\~app1”이고 이전 버전에서는 “myapp/app1”입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-application-upload"></a>sfctl application upload
Microsoft Azure Service Fabric 애플리케이션 패키지를 이미지 저장소에 복사합니다.

필요에 따라 패키지의 각 파일에 대한 업로드 진행률을 표시합니다. 업로드 진행률을 `stderr`로 보냅니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --path   [필수] | 로컬 애플리케이션 패키지 경로입니다. |
| --imagestore-string | 애플리케이션 패키지를 업로드할 대상 이미지 저장소입니다.  기본값\: fabric\:ImageStore. |
| --show-progress | 큰 패키지에 대한 파일 업로드 진행률을 표시합니다. |

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
