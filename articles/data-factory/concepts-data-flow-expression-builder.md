---
title: Azure Data Factory Mapping Data Flow 식 작성기
description: Azure Data Factory Mapping Data Flow용 식 작성기
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269167"
---
# <a name="mapping-data-flow-expression-builder"></a>Mapping Data Flow 식 작성기

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory Mapping Data Flow에는 데이터 변환 식을 입력할 수 있는 식 상자가 있습니다. 해당 상자에 데이터 흐름의 열, 필드, 변수, 매개 변수, 함수를 사용합니다. 식을 작성하려면 변환 내의 식 텍스트 상자를 클릭할 때 시작되는 식 작성기를 사용합니다. 변환에 사용할 열을 선택할 때 “계산 열” 옵션이 표시되는 경우도 있습니다. 이 옵션을 클릭해도 식 작성기가 시작됩니다.

![식 작성기](media/data-flow/expression.png "식 작성기")

식 작성기 도구는 기본적으로 텍스트 편집기 옵션으로 설정되어 있습니다. 자동 완성 기능은 구문 검사 및 강조 표시를 사용하여 전체 Azure Data Factory Data Flow 개체 모델에서 읽어옵니다.

![식 작성기 자동 완성](media/data-flow/expb1.png "식 작성기 자동 완성")

## <a name="currently-working-on-field"></a>현재 작업 중 필드

![식 작성기](media/data-flow/exp3.png "현재 작업 중")

식 작성기 UI의 왼쪽 위에는 “현재 작업 중” 필드가 현재 작업 중인 필드의 이름과 함께 표시됩니다. UI에서 빌드하는 식이 현재 작업 중인 필드에만 적용됩니다. 다른 필드를 변환하려면 현재 작업을 저장하고 이 드롭다운을 사용하여 다른 필드를 선택한 다음, 다른 필드에 대한 식을 작성합니다.

## <a name="data-preview-in-debug-mode"></a>디버그 모드의 데이터 미리 보기

![식 작성기](media/data-flow/exp4b.png "식 데이터 미리 보기")

식에서 작업하는 경우 Azure Data Factory Data Flow 디자인 화면에서 디버그 모드를 선택적으로 켜면 작성하는 식에서 생성된 데이터 결과의 진행 중인 라이브 미리 보기를 사용할 수 있습니다. 식에 대해 실시간 라이브 디버깅을 사용할 수 있습니다.

![디버그 모드](media/data-flow/debugbutton.png "디버그 단추")


![식 작성기](media/data-flow/exp5.png "식 데이터 미리 보기")

## <a name="comments"></a>설명

한 줄 및 여러 줄 주석 구문을 사용하여 식에 주석을 추가합니다.

![주석](media/data-flow/comments.png "주석")

## <a name="regular-expressions"></a>정규식

Azure Data Factory Data Flow 식 언어([여기서 전체 참조 문서](https://aka.ms/dataflowexpressions) 참조)를 통해 정규식 구문을 포함하는 함수를 사용할 수 있습니다. 식 작성기는 백슬래시를 해석 하려고 정규식 함수를 사용할 때는 (\\) 이스케이프 문자 시퀀스로 합니다. 정규식의 백슬래시를 사용 하는 경우 전체 정규식 틱을 단위로 묶습니다 하나 (\`) 하거나 이중 백슬래시를 사용 합니다.

예제: 틱 사용

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

또는 이중 슬래시 사용

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>배열 인덱스를 주소 지정

배열을 반환하는 식 함수를 사용하는 경우 대괄호 []를 사용하여 내부에서 배열 개체를 반환하는 특정 인덱스를 처리합니다. 배열은 1부터 시작합니다.

![식 작성기 배열](media/data-flow/expb2.png "식 데이터 미리 보기")

## <a name="handling-names-with-special-characters"></a>특수 문자를 사용 하 여 이름을 처리합니다.

열 이름은 특수 문자 또는 공백을 포함 하는 경우에 중괄호를 사용 하 여 이름을 묶습니다.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>다음 단계

[데이터 변환 식 작성 시작](data-flow-expression-functions.md)
