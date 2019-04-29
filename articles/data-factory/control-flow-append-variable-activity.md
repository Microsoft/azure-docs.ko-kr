---
title: Azure Data Factory의 변수 추가 작업 | Microsoft Docs
description: Data Factory 파이프라인에 정의된 기존 배열 변수에 값을 추가하기 위해 변수 추가 작업을 설정하는 방법 알아보기
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/09/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: a5efe946000eb00e65d314ae53d7136761e2109d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557225"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Azure Data Factory의 변수 추가 작업

변수 추가 작업을 사용하여 Data Factory 파이프라인에 정의된 기존 배열 변수에 값을 추가할 수 있습니다.

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 필수
-------- | ----------- | --------
이름 | 파이프라인의 작업 이름 | 예
description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | no
형식 | 활동 유형은 AppendVariable입니다. | 예
값 | 지정된 변수에 추가하는 데 사용되는 문자열 리터럴 또는 표현식 개체 값 | 예
variableName | 활동별로 수정될 변수의 이름, 변수는 ‘Array’ 유형이어야 합니다. | 예

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 설정 작업](control-flow-set-variable-activity.md)
