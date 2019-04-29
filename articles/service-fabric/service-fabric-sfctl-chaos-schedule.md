---
title: Azure Service Fabric CLI- sfctl chaos schedule | Microsoft Docs
description: Service Fabric CLI sfctl schedule 명령을 설명합니다.
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
ms.openlocfilehash: dc3dd06b5feac1f66598cd65fa79f447a1bbd9be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837507"
---
# <a name="sfctl-chaos-schedule"></a>sfctl chaos schedule
chaos schedule을 가져오고 설정합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
| get | Chaos를 실행하는 시기 및 방법을 정의하는 Chaos Schedule을 가져옵니다. |
| set | Chaos에서 사용되는 일정을 설정합니다. |

## <a name="sfctl-chaos-schedule-get"></a>sfctl chaos schedule get
Chaos를 실행하는 시기 및 방법을 정의하는 Chaos Schedule을 가져옵니다.

사용 중인 Chaos Schedule과 Chaos를 실행하는 시기 및 방법을 정의하는 Chaos Schedule의 버전을 가져옵니다.

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

## <a name="sfctl-chaos-schedule-set"></a>sfctl chaos schedule set
Chaos에서 사용되는 일정을 설정합니다.

Chaos는 Chaos Schedule에 따라 실행을 자동으로 예약합니다. 제공된 입력 일정의 버전은 서버의 Chaos Schedule 버전과 일치해야 합니다. 제공된 버전이 서버의 버전과 일치하지 않는 경우 Chaos Schedule은 업데이트되지 않습니다. 제공된 버전이 서버의 버전과 일치하는 경우 Chaos Schedule이 업데이트되고 서버의 Chaos Schedule 버전은 1단위로 증분하고 2,147,483,647 다음에 0으로 다시 래핑합니다. 이 호출이 만들어질 때 Chaos가 실행되는 경우 호출에 실패합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --chaos-parameters-dictionary | 작업에서 사용할 ChaosParameters로 문자열 이름의 매핑을 나타내는 JSON 인코딩된 목록입니다. |
| --expiry-date-utc | Chaos를 예약하는 데 Schedule을 사용하여 중지할 시기에 대한 날짜 및 시간입니다.  기본값\: 9999-12-31T23\:59\:59.999Z. |
| --jobs | Chaos를 실행할 시기 및 Chaos와 함께 실행할 매개 변수를 나타내는 ChaosScheduleJobs의 JSON 인코딩된 목록입니다. |
| --start-date-utc | Chaos를 예약하는 데 Schedule을 사용하여 시작할 시기에 대한 날짜 및 시간입니다.  기본값\: 1601-01-01T00\:00\:00.000Z. |
| --timeout -t | 서버 시간 제한(초).  기본값\: 60. |
| --version | Schedule의 버전 번호입니다. |

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다. |
| --help -h | 이 도움말 메시지 및 종료를 표시합니다. |
| --output -o | 출력 형식.  허용되는 값\: json, jsonc, table, tsv.  기본값\: json. |
| --query | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http\://jmespath.org/를 참조하세요. |
| --verbose | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그의 경우 --debug를 사용합니다. |

### <a name="examples"></a>예

다음 명령은 Chaos를 1일 24시간, 주 7일 실행하는 2016-01-01에 시작하여 2038-01-01에 만료되는 일정을 설정합니다(현재 일정에 버전 0이 있다고 가정). Chaos는 해당 시간에 대해 클러스터에서 예약됩니다.

    sfctl chaos schedule set --version 0 --start-date-utc "2016-01-01T00:00:00.000Z" --expiry-date-utc "2038-01-01T00:00:00.000Z"
    --chaos-parameters-dictionary
    [
    {
        "Key":"adhoc",
        "Value":{
            "MaxConcurrentFaults":3,
            "EnableMoveReplicaFaults":true,
            "ChaosTargetFilter":{
                "NodeTypeInclusionList":[
                "N0010Ref",
                "N0020Ref",
                "N0030Ref",
                "N0040Ref",
                "N0050Ref"
                ]
            },
            "MaxClusterStabilizationTimeoutInSeconds":60,
            "WaitTimeBetweenIterationsInSeconds":15,
            "WaitTimeBetweenFaultsInSeconds":30,
            "TimeToRunInSeconds":"600",
            "Context":{
                "Map":{
                "test":"value"
                }
            },
            "ClusterHealthPolicy":{
                "MaxPercentUnhealthyNodes":0,
                "ConsiderWarningAsError":true,
                "MaxPercentUnhealthyApplications":0
            }
        }
    }
    ]
    --jobs
    [
    {
        "ChaosParameters":"adhoc",
        "Days":{
            "Sunday":true,
            "Monday":true,
            "Tuesday":true,
            "Wednesday":true,
            "Thursday":true,
            "Friday":true,
            "Saturday":true
        },
        "Times":[
            {
                "StartTime":{
                "Hour":0,
                "Minute":0
                },
                "EndTime":{
                "Hour":23,
                "Minute":59
                }
            }
        ]
    }
    ]


## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.
