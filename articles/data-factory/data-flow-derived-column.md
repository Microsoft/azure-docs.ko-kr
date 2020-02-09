---
title: 매핑 데이터 흐름의 파생 열 변환
description: 데이터 흐름 파생 열 변환을 사용 하 여 Azure Data Factory에서 대규모로 데이터를 변환 하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 66396de52b3709c1d9357f32a375a29a8dcdbd1d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048746"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 파생 열 변환

파생 열 변환을 사용 하 여 데이터 흐름에 새 열을 생성 하거나 기존 필드를 수정할 수 있습니다.

## <a name="derived-column-settings"></a>파생 열 설정

기존 열을 재정의 하려면 열 드롭다운을 통해 선택 합니다. 그렇지 않으면 열 선택 필드를 textbox로 사용 하 고 새 열의 이름을 입력 합니다. 파생 열의 식을 작성 하려면 ' 식 입력 ' 상자를 클릭 하 여 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)를 엽니다.

![파생 열 설정](media/data-flow/dc1.png "파생 열 설정")

파생 열을 더 추가 하려면 기존 파생 열 위로 마우스를 이동 하 고 더하기 아이콘을 클릭 합니다. **열 추가** 또는 **열 패턴 추가**중 하나를 선택 합니다. 열 이름이 원본의 변수인 경우 열 패턴을 유용 하 게 사용할 수 있습니다. 자세한 내용은 [열 패턴](concepts-data-flow-column-pattern.md)을 참조 하세요.

![새 파생 열 선택](media/data-flow/columnpattern.png "새 파생 열 선택")

## <a name="build-schemas-in-output-schema-pane"></a>출력 스키마 창에서 스키마 빌드

스키마에 수정 하 고 추가 하는 열이 출력 스키마 창에 나열 됩니다. 여기에서 간단 하 고 복잡 한 데이터 구조를 대화형으로 작성할 수 있습니다. 필드를 더 추가 하려면 **열 추가**를 선택 합니다. 계층 구조를 작성 하려면 하위 **열 추가**를 선택 합니다.

![하위 열 추가](media/data-flow/addsubcolumn.png "하위 열 추가")

데이터 흐름의 복합 형식을 처리 하는 방법에 대한 자세한 내용은 [데이터 흐름 매핑의 JSON 처리](format-json.md#mapping-data-flow-properties)를 참조 하세요.

![복합 열 추가](media/data-flow/complexcolumn.png "열 추가")

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림을 `MoviesYear` 하 고 두 개의 파생 열을 만드는 `CleanData` 라는 파생 열입니다. 첫 번째 파생 열 `Rating` 등급 값을 사용 하 여 열을 정수 형식으로 바꿉니다. 두 번째 파생 열은 이름이 ' 영화 '로 시작 하는 각 열과 일치 하는 패턴입니다. 일치 하는 각 열에 대해 일치 하는 열의 값과 같은 `movie` 열을 만듭니다 .이 열은 ' movie_ ' 접두사가 붙습니다. 

Data Factory UX에서이 변환은 아래 이미지와 같습니다.

![파생 예제](media/data-flow/derive-script1.png "파생 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>다음 단계

- [매핑 데이터 흐름 식 언어](data-flow-expression-functions.md)에 대해 자세히 알아보세요.
