---
title: Azure Data Factory의 활동 유효성 검사 | Microsoft Docs
description: 유효성 검사 작업에서 사용자 지정 하는 특정 조건 사용 하 여 연결 된 데이터 집합의 유효성을 검사 될 때까지 파이프라인의 실행을 계속 되지 않습니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: shlo
ms.openlocfilehash: 46447bdbea93d1f99c5682cf878c2035e6f49b78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764325"
---
# <a name="validation-activity-in-azure-data-factory"></a>Azure Data Factory의 활동 유효성 검사
연결 된 유효성 검사에 실행이 계속 파이프라인 파이프라인에서 유효성 검사를 사용할 수 있는 데이터 집합 참조는 지정 된 조건을 충족 또는 시간 제한에 도달 합니다.


## <a name="syntax"></a>구문

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>형식 속성

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | '유효성 검사' 작업의 이름 | String | 예 |
형식 | 로 설정 되어야 합니다 **유효성 검사**합니다. | String | 예 |
데이터 세트 | 활동 블록 실행이 데이터 집합 참조의 유효성을 검사에 해당 될 때까지 존재 하 고는 지정된 된 조건을 충족 하는지 또는 시간 제한에 도달 합니다. 제공 된 데이터 집합 "MinimumSize" 또는 "ChildItems" 속성을 지원 해야 합니다. | 데이터 집합 참조 | 예 |
시간 제한 | 작업 실행에 대한 시간 제한을 지정합니다. 없는 값을 지정 하는 경우 기본값은 7 일 ("7.00:00:00")입니다. 형식은 d.hh:mm:ss | String | 아닙니다. |
sleep | 유효성 검사 시도 간격 (초) 지연 합니다. 없는 값을 지정 하는 경우 기본값은 10 초입니다. | 정수  | 아닙니다. |
childItems | 폴더에 자식 항목이 있는지 확인 합니다. True는를 설정할 수 있습니다. 폴더가 존재 하 고 항목이 있는지 확인 합니다. 폴더에 하나 이상의 항목이 있는지 또는 시간 제한 값에 도달할 때까지 차단 합니다.-false: 가 비어 있고 폴더가 있는지 확인 합니다. 값에 도달할 때 폴더는 비어 있거나 일까 지 시간 초과 될 때까지 차단 합니다. 값은 지정 하지 않으면 작업 폴더가 될 때까지 또는 시간 제한에 도달할 때까지 차단 됩니다. | Boolean | 아닙니다. |
minimumSize | 바이트에 있는 파일의 최소 크기입니다. 기본값은 0 바이트 없는 값을 지정 하는 경우 | 정수  | 아닙니다. |


## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
