---
title: Azure Data Factory의 대기 작업 | Microsoft Docs
description: 대기 작업은 지정된 기간 동안 파이프라인의 실행을 일시 중지합니다.
services: data-factory
documentationcenter: ''
author: shlo
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/12/2018
ms.author: shlo
ms.openlocfilehash: 66d79bc1597cd8f3c7e01eb8227eb7c91ba04d1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764754"
---
# <a name="execute-wait-activity-in-azure-data-factory"></a>Azure Data Factory의 대기 작업을 실행 합니다.
파이프라인에서 대기 작업을 사용하는 경우 파이프라인은 후속 작업을 계속 실행하기 전에 지정된 기간 동안 대기합니다. 

## <a name="syntax"></a>구문

```json
{
    "name": "MyWaitActivity",
    "type": "Wait",
    "typeProperties": {
        "waitTimeInSeconds": 1
    }
}

```

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | `Wait` 작업의 이름입니다. | String | 예
형식 | **대기**로 설정해야 합니다. | String | 예
waitTimeInSeconds | 계속 처리하기 전에 파이프라인이 대기하는 시간(초)입니다. | 정수  | 예

## <a name="example"></a>예

> [!NOTE]
> 이 섹션에서는 파이프라인을 실행하는 JSON 정의 및 샘플 PowerShell 명령을 제공합니다. Azure PowerShell 및 JSON 정의를 사용하여 Data Factory 파이프라인을 만드는 단계별 지침이 포함된 연습은 [자습서: Azure PowerShell을 사용하여 Data Factory 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요.

### <a name="pipeline-with-wait-activity"></a>대기 작업을 포함하는 파이프라인
이 예제에서 파이프라인에는 두 개의 활동인 **다음까지** 및 **대기**가 있습니다. 대기 작업은 1초 동안 대기하도록 구성됩니다. 파이프라인은 각 실행 간에 1초 동안 대기하는 루프에서 웹 작업을 실행합니다. 

```json
{
    "name": "DoUntilPipeline",
    "properties": {
        "activities": [
            {
                "type": "Until",
                "typeProperties": {
                    "expression": {
                        "value": "@equals('Failed', coalesce(body('MyUnauthenticatedActivity')?.status, actions('MyUnauthenticatedActivity')?.status, 'null'))",
                        "type": "Expression"
                    },
                    "timeout": "00:00:01",
                    "activities": [
                        {
                            "name": "MyUnauthenticatedActivity",
                            "type": "WebActivity",
                            "typeProperties": {
                                "method": "get",
                                "url": "https://www.fake.com/",
                                "headers": {
                                    "Content-Type": "application/json"
                                }
                            },
                            "dependsOn": [
                                {
                                    "activity": "MyWaitActivity",
                                    "dependencyConditions": [ "Succeeded" ]
                                }
                            ]
                        },
                        {
                            "type": "Wait",
                            "typeProperties": {
                                "waitTimeInSeconds": 1
                            },
                            "name": "MyWaitActivity"
                        }
                    ]
                },
                "name": "MyUntilActivity"
            }
        ]
    }
}

```

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
