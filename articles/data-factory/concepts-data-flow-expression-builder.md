---
title: 데이터 흐름 매핑의 식 작성기
description: Azure Data Factory의 데이터 흐름 매핑에서 식 작성기를 사용 하 여 식을 작성 합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f522812f762b55ec61794101e6cd1ec15fb171ca
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212100"
---
# <a name="build-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름에서 식 작성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름에서는 많은 변환 속성이 식으로 입력 됩니다. 이러한 식은 런타임에 Spark 데이터 형식으로 계산 되는 열 값, 매개 변수, 함수, 연산자 및 리터럴로 구성 됩니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>식 작성기 열기

Azure Data Factory 사용자 환경에서 식 편집 인터페이스를 식 작성기 라고 합니다. 식 논리를 입력할 때 Data Factory는 강조 표시, 구문 검사 및 자동 완료를 위해 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) 코드 완성 기능을 사용 합니다.

![식 작성기](media/data-flow/xpb1.png "식 작성기")

식이 필수인 파생 열 및 필터와 같은 변환에서 blue 식 상자를 선택 하 여 식 작성기를 엽니다.

![Blue 식 상자](media/data-flow/expressionbox.png "식 작성기")

일치 또는 그룹별 조건에서 열을 참조 하는 경우 식이 열에서 값을 추출할 수 있습니다. 식을 만들려면 **계산 열**을 선택 합니다.

![계산 열 옵션](media/data-flow/computedcolumn.png "식 작성기")

식 또는 리터럴 값이 유효한 입력 인 경우 **동적 콘텐츠 추가** 를 선택 하 여 리터럴 값으로 계산 되는 식을 작성 합니다.

![동적 콘텐츠 추가 옵션](media/data-flow/add-dynamic-content.png "식 작성기")

## <a name="expression-language-reference"></a>식 언어 참조

데이터 흐름 매핑에는 식에 사용할 수 있는 기본 제공 함수 및 연산자가 있습니다. 사용 가능한 함수 목록은 [매핑 데이터 흐름의 식 함수](data-flow-expression-functions.md)를 참조 하세요.

## <a name="column-names-with-special-characters"></a>특수 문자가 포함 된 열 이름

특수 문자 또는 공백을 포함 하는 열 이름이 있는 경우 식에서 참조할 수 있도록 이름을 중괄호로 묶습니다.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>식 결과 미리 보기

[디버그 모드](concepts-data-flow-debug-mode.md) 를 설정 하는 경우 라이브 Spark 클러스터를 사용 하 여 식이 평가 하는 결과에 대 한 진행 중인 미리 보기를 볼 수 있습니다. 논리를 작성 하는 동안 식을 실시간으로 디버그할 수 있습니다. 

![진행 중인 미리 보기](media/data-flow/exp4b.png "식 데이터 미리 보기")

원본 라이브 샘플에 대 한 식 결과를 업데이트 하려면 **새로 고침** 을 선택 합니다.

![새로 고침 단추](media/data-flow/exp5.png "식 데이터 미리 보기")

## <a name="string-interpolation"></a>문자열 보간

리터럴 문자열 텍스트를 식과 함께 묶으려면 따옴표를 사용 합니다. 식 함수, 열 및 매개 변수를 포함할 수 있습니다. 문자열 보간은 매개 변수가 쿼리 문자열에 포함 된 경우 문자열 연결의 광범위 한 사용을 방지 하는 데 유용 합니다. 식 구문을 사용 하려면 중괄호를 중괄호로 묶습니다.

문자열 보간의 몇 가지 예는 다음과 같습니다.

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>주석 식

한 줄 및 여러 줄 주석 구문을 사용 하 여 식에 주석을 추가 합니다.

다음은 유효한 설명 예입니다.

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

식의 맨 위에 주석을 입력 하면 변환 텍스트 상자에 표시 되어 변환 식을 문서화할 수 있습니다.

![변환 텍스트 상자의 주석](media/data-flow/comments2.png "주석")

## <a name="regular-expressions"></a>정규식

많은 식 언어 함수는 정규식 구문을 사용 합니다. 정규식 함수를 사용 하는 경우 식 작성기는 백슬래시 ( \\ )를 이스케이프 문자 시퀀스로 해석 하려고 합니다. 정규식에서 백슬래시를 사용 하는 경우 전체 regex를 backtick ()으로 묶거나 \` 이중 백슬래시를 사용 합니다.

백 틱을 사용 하는 예제:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

이중 슬래시를 사용 하는 예제는 다음과 같습니다.

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>주소 배열 인덱스

배열을 반환 하는 식 함수를 사용 하 여 대괄호 ([])를 사용 하 여 배열 개체를 반환 하는 내의 특정 인덱스를 처리 합니다. 배열은 배열을 기반으로 합니다.

![식 작성기 배열](media/data-flow/expb2.png "식 데이터 미리 보기")

## <a name="keyboard-shortcuts"></a>바로 가기 키

* Ctrl + K Ctrl + C: 줄 전체를 주석으로 처리 합니다.
* Ctrl + K Ctrl + U: 주석 처리를 제거 합니다.
* F1: 편집기 도움말 명령을 제공 합니다.
* Alt + 아래쪽 화살표 키: 현재 줄 아래로 이동 합니다.
* Alt + 위쪽 화살표 키: 현재 줄 위로 이동 합니다.
* Ctrl + 스페이스바: 상황에 맞는 도움말을 표시 합니다.

## <a name="convert-to-dates-or-timestamps"></a>날짜 또는 타임 스탬프로 변환

타임 스탬프 출력에 문자열 리터럴을 포함 하려면에서 변환을 래핑합니다 ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Epoch에서 날짜 또는 타임 스탬프로 밀리초를 변환 하려면를 사용 `toTimestamp(<number of milliseconds>)` 합니다. 시간을 초 단위로 가져오는 경우 1000를 곱합니다.

```toTimestamp(1574127407*1000l)```

이전 식의 끝에 있는 후행 "l"은 long 형식으로의 변환을 인라인 구문으로 나타냅니다.

## <a name="find-time-from-epoch-or-unix-time"></a>Epoch 또는 Unix 시간에서 시간 찾기

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' yyyy-MM-dd HH: MM: ss). SSS ')) * 1000l

## <a name="next-steps"></a>다음 단계

[데이터 변환 식 작성 시작](data-flow-expression-functions.md)
