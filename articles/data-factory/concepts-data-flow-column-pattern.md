---
title: Azure Data Factory Mapping Data Flow 열 패턴
description: Azure Data Factory Mapping Data Flow 열 패턴은 기본 스키마 메타데이터와 관계없이 데이터 흐름의 필드를 변환하기 위한 일반화된 템플릿 패턴을 만드는 데 사용됩니다.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 53d3300ea11a86c34909ba6ce0fd6c8c0c38b4b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269718"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Azure Data Factory Mapping Data Flow 개념

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

하드 코드된 열 이름 대신 패턴을 기준으로 템플릿 열을 만들 수 있도록 “열 패턴” 아이디어를 지원하는 Azure Data Factory Data Flow 변환도 있습니다. 식 작성기 내에서 이 기능을 사용하여 정확한 특정 필드 이름을 요구하는 대신 변환을 위한 열 일치에 사용할 패턴을 정의할 수 있습니다. 패턴은 수신 원본 필드가 자주 변경되는 경우, 특히 텍스트 파일 또는 NoSQL 데이터베이스에서 열을 변경하는 경우에 유용합니다. 이를 “스키마 드리프트”라고도 합니다.

![열 패턴](media/data-flow/columnpattern2.png "열 패턴")

열 패턴은 스키마 드리프트 시나리오와 일반 시나리오 둘 다를 처리하는 데 유용합니다. 각 열 이름을 완전히 알 수 없는 상황에 적합합니다. 열 이름과 열 데이터 형식에 패턴 일치를 적용하고 `name` & `type` 패턴과 일치하는 데이터 스트림의 필드에 대해 해당 작업을 수행할 변환 식을 작성할 수 있습니다.

패턴을 허용하는 변환에 식을 추가하는 경우 “열 패턴 추가”를 선택합니다. 열 패턴을 사용하면 스키마 드리프트 열 일치 패턴이 허용됩니다.

템플릿 열 패턴을 빌드할 때는 식에 `$$`를 사용하여 입력 데이터 스트림에서 일치된 각 필드에 대한 참조를 나타냅니다.

식 작성기 regex 함수 중 하나를 사용하는 경우 나중에 $1, $2, $3...을 사용하여 regex 식에서 일치된 하위 패턴을 참조할 수 있습니다.

열 패턴 시나리오의 예는 일련의 수신 필드에 SUM을 사용하는 경우입니다. 집계 SUM 계산은 집계 변환에 있습니다. “정수”와 일치하는 필드 형식의 모든 일치 항목을 SUM한 다음, $$를 사용하여 각 일치 항목을 식에서 참조할 수 있습니다.
