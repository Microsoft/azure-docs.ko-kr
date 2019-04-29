---
title: Azure Service Fabric CLI- sfctl compose| Microsoft Docs
description: Service Fabric CLI sfctl compose 명령을 설명합니다.
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
ms.openlocfilehash: 4b5cbb4a24b61de7e64a52ef950deedab3eec263
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837269"
---
# <a name="sfctl-compose"></a>sfctl compose
Docker Compose 애플리케이션을 만들고, 삭제하고, 관리합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| create | Service Fabric 작성 배포를 만듭니다. |
| list | Service Fabric 클러스터에서 만든 작성 배포의 목록을 가져옵니다. |
| 제거 | 클러스터에서 기존 Service Fabric 작성 배포를 삭제합니다. |
| status | Service Fabric 작성 배포에 관한 정보를 가져옵니다. |
| 업그레이드 | Service Fabric 클러스터에서 작성 배포 업그레이드를 시작합니다. |
| upgrade-rollback | Service Fabric 클러스터에서 작성 배포 업그레이드 롤백을 시작합니다. |
| upgrade-status | 이 Service Fabric 작성 배포에 수행된 최신 업그레이드에 대한 세부 정보를 가져옵니다. |

## <a name="sfctl-compose-create"></a>sfctl compose create
Service Fabric 작성 배포를 만듭니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --deployment-name [필수] | 배포의 이름입니다. |
| --file-path [필수] | 대상 Docker Compose 파일의 경로입니다. |
| --encrypted-pass | 컨테이너 레지스트리 암호를 묻는 메시지를 표시하는 대신 이미 암호화된 암호를 사용합니다. |
| --has-pass | 컨테이너 레지스트리에 대한 암호를 묻는 메시지를 표시합니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --user | 컨테이너 레지스트리에 연결할 사용자 이름입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-compose-list"></a>sfctl compose list
Service Fabric 클러스터에서 만든 작성 배포의 목록을 가져옵니다.

Service Fabric 클러스터에 만들어졌거나 만들어지는 프로세스에 있는 작성 배포에 관한 상태를 반환합니다. 응답에는 이름, 상태 및 작성 배포에 관한 기타 세부 정보가 포함됩니다. 배포 목록이 한 페이지에 맞지 않는 경우, 결과의 한 페이지가 연속 토큰으로 반환되어 다음 페이지를 가져오는 데 사용될 수 있습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --continuation-token | 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다. |
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

## <a name="sfctl-compose-remove"></a>sfctl compose remove
클러스터에서 기존 Service Fabric 작성 배포를 삭제합니다.

기존 Service Fabric 작성 배포를 삭제합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --deployment-name [필수] | 배포의 id입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-compose-status"></a>sfctl compose status
Service Fabric 작성 배포에 관한 정보를 가져옵니다.

Service Fabric 클러스터에서 만들어졌거나 만들어지는 과정에 있으며 이름이 매개 변수로 지정된 것과 일치하는 작성 배포의 정보를 반환합니다. 응답에는 이름, 상태 및 배포에 관한 기타 세부 정보가 포함됩니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --deployment-name [필수] | 배포의 id입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Service Fabric 클러스터에서 작성 배포 업그레이드를 시작합니다.

매개 변수가 유효한 경우 제공된 업그레이드 매개 변수의 유효성을 검사하고 배포 업그레이드를 시작합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --deployment-name  [필수] | 배포의 이름입니다. |
| --file-path        [필수] | 대상 Docker Compose 파일의 경로입니다. |
| --default-svc-type-health-map | 서비스의 상태를 평가하는 데 사용된 상태 정책을 설명하는 JSON으로 인코딩된 사전입니다. |
| --encrypted-pass | 컨테이너 레지스트리 암호를 묻는 메시지를 표시하는 대신 이미 암호화된 암호를 사용합니다. |
| --failure-action | 사용할 수 있는 값\: 'Invalid', 'Rollback', 'Manual'. |
| --force-restart | 코드 버전이 변경되지 않은 경우에도 업그레이드 중에 프로세스를 강제로 다시 시작합니다. <br><br> 업그레이드는 구성 또는 데이터만 변경합니다. |
| --has-pass | 컨테이너 레지스트리에 대한 암호를 묻는 메시지를 표시합니다. |
| --health-check-retry | 애플리케이션 또는 클러스터가 정상이 아닌지 상태 확인을 수행하는 시도의 시간 간격입니다. |
| --health-check-stable | 업그레이드가 다음 업그레이드 도메인으로 진행되기 전에 애플리케이션 또는 클러스터가 정상 상태로 유지되어야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --health-check-wait | 업그레이드 도메인을 완료한 후 상태 확인 프로세스를 시작하기 전에 대기하는 시간입니다. |
| --replica-set-check | 예기치 않은 문제가 있을 때 업그레이드 도메인의 처리를 차단하고 가용성의 손실을 방지하는 최대 시간입니다. <br><br> 이 시간 제한이 만료되면 가용성 손실 문제와 상관없이 업그레이드 도메인 처리가 진행됩니다. 시간 제한은 각 업그레이드 도메인의 시작 시 다시 설정됩니다. 유효한 값은 0과 42949672925 사이입니다. |
| --svc-type-health-map | 서비스의 상태를 평가하는 데 사용된 상태 정책을 설명하는 JSON 인코딩된 개체 목록입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --unhealthy-app | 오류를 보고하기 전에 허용되는 비정상 애플리케이션의 최대 백분율입니다. <br><br> 예를 들어 애플리케이션의 10%를 비정상 상태가 되도록 허용하면 값은 10입니다. 비율은 클러스터에서 오류로 처리하기 전에 비정상 상태로 있을 수 있는 애플리케이션의 최대 허용 비율을 나타냅니다. 비율은 지켜지나 비정상 애플리케이션이 적어도 하나 있다면 상태는 경고로 평가됩니다. 클러스터에 있는 애플리케이션 인스턴스의 총 수를 비정상 애플리케이션의 수로 나눠 계산합니다. |
| --upgrade-domain-timeout | FailureAction이 실행되기 전에 각 업그레이드 도메인이 완료해야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --upgrade-kind | 기본값\: Rolling. |
| --upgrade-mode | 사용할 수 있는 값\: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  기본값\: UnmonitoredAuto. |
| --upgrade-timeout | FailureAction이 실행되기 전에 전체 업그레이드를 완료해야 하는 시간입니다. <br><br> 먼저 ISO 8601 기간을 나타내는 문자열로 해석됩니다. 실패하는 경우 총 시간(밀리초)을 나타내는 숫자로 해석됩니다. |
| --user | 컨테이너 레지스트리에 연결할 사용자 이름입니다. |
| --warning-as-error | 경고가 오류와 동일한 심각도로 처리되는지 여부를 나타냅니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose upgrade-rollback
Service Fabric 클러스터에서 작성 배포 업그레이드 롤백을 시작합니다.

Service Fabric 작성 배포 업그레이드를 롤백합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --deployment-name [필수] | 배포의 id입니다. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose upgrade-status
이 Service Fabric 작성 배포에 수행된 최신 업그레이드에 대한 세부 정보를 가져옵니다.

애플리케이션 상태 문제 디버깅을 지원하기 위한 세부 정보와 함께 작성 배포 업그레이드의 상태에 대한 정보를 반환합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --deployment-name [필수] | 배포의 id입니다. |
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