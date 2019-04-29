---
title: Azure Monitor 로그 쿼리에서 문자열 작업 | Microsoft Docs
description: Azure Monitor 로그 쿼리에서 문자열을 편집, 비교, 검색하고 다양한 기타 작업을 수행하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 4b2763629a3036551cb3d362e609c72737436f4a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61424706"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리에서 문자열 작업


> [!NOTE]
> 완료 해야 [Azure Monitor Log Analytics를 사용 하 여 시작](get-started-portal.md) 하 고 [Azure Monitor 로그 쿼리를 사용 하 여 시작](get-started-queries.md) 이 자습서를 완료 하기 전에 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

이 문서에서는 문자열을 편집, 비교, 검색하고 다양한 기타 작업을 수행하는 방법을 설명합니다.

문자열의 각 문자에는 해당 위치에 따라 인덱스 번호가 있습니다. 첫 번째 문자는 인덱스 0에 있고, 다음 문자는 1에 있습니다. 이와 같이 계속 적용됩니다. 다른 문자열 함수는 다음 섹션에 나와 있는 것처럼 인덱스 번호를 사용합니다. 다음 예제 대부분이 특정 데이터 원본을 사용하지 않고 문자열 조작을 보여 주기 위해 **print** 명령을 사용합니다.


## <a name="strings-and-escaping-them"></a>문자열 및 이스케이프
문자열 값은 작은따옴표 또는 큰따옴표 문자로 래핑됩니다. 백슬래시(\)는 문자를 다음에 나오는 문자에 대해 이스케이프하는 데 사용됩니다. 예를 들어 탭의 경우 \t, 줄 바꿈에 대해 \n, 따옴표 문자에 대해 \"를 사용합니다.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

"\\"가 이스케이프 문자로 사용되지 않도록 하기 위해 문자열의 접두사로 "\@"을 추가합니다.

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>문자열 비교

연산자       |설명                         |대/소문자 구분|예제(`true` 생성)
---------------|------------------------------------|--------------|-----------------------
`==`           |같음                              |예           |`"aBc" == "aBc"`
`!=`           |같지 않음                          |예           |`"abc" != "ABC"`
`=~`           |같음                              |아닙니다.            |`"abc" =~ "ABC"`
`!~`           |같지 않음                          |아닙니다.            |`"aBc" !~ "xyz"`
`has`          |오른쪽이 왼쪽의 전체 항임 |아닙니다.|`"North America" has "america"`
`!has`         |오른쪽이 왼쪽의 전체 항이 아님       |아닙니다.            |`"North America" !has "amer"` 
`has_cs`       |오른쪽이 왼쪽의 전체 항임 |예|`"North America" has_cs "America"`
`!has_cs`      |오른쪽이 왼쪽의 전체 항이 아님       |예            |`"North America" !has_cs "amer"` 
`hasprefix`    |오른쪽이 왼쪽의 항 접두사임         |아닙니다.            |`"North America" hasprefix "ame"`
`!hasprefix`   |오른쪽이 왼쪽의 항 접두사가 아님     |아닙니다.            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |오른쪽이 왼쪽의 항 접두사임         |예            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |오른쪽이 왼쪽의 항 접두사가 아님     |예            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |오른쪽이 왼쪽의 항 접미사임         |아닙니다.            |`"North America" hassuffix "ica"`
`!hassuffix`   |오른쪽이 왼쪽의 항 접미사가 아님     |아닙니다.            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |오른쪽이 왼쪽의 항 접미사임         |예            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |오른쪽이 왼쪽의 항 접미사가 아님     |예            |`"North America" !hassuffix_cs "icA"`
`contains`     |오른쪽이 왼쪽의 하위 시퀀스로 발생함  |아닙니다.            |`"FabriKam" contains "BRik"`
`!contains`    |오른쪽이 왼쪽에 발생하지 않음           |아닙니다.            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |오른쪽이 왼쪽의 하위 시퀀스로 발생함  |예           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |오른쪽이 왼쪽에 발생하지 않음           |예           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |오른쪽이 왼쪽의 시작 하위 시퀀스임|아닙니다.            |`"Fabrikam" startswith "fab"`
`!startswith`  |오른쪽이 왼쪽의 시작 하위 시퀀스가 아님|아닙니다.        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |오른쪽이 왼쪽의 시작 하위 시퀀스임|예            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |오른쪽이 왼쪽의 시작 하위 시퀀스가 아님|예        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |오른쪽이 왼쪽의 닫는 하위 시퀀스임|아닙니다.             |`"Fabrikam" endswith "Kam"`
`!endswith`    |오른쪽이 왼쪽의 닫는 하위 시퀀스가 아님|아닙니다.         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |오른쪽이 왼쪽의 닫는 하위 시퀀스임|예             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |오른쪽이 왼쪽의 닫는 하위 시퀀스가 아님|예         |`"Fabrikam" !endswith "brik"`
`matches regex`|왼쪽에 오른쪽의 일치 항목이 포함됨        |예           |`"Fabrikam" matches regex "b.*k"`
`in`           |요소 중 하나와 같음       |예           |`"abc" in ("123", "345", "abc")`
`!in`          |어떤 요소와도 같지 않음   |예           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

문자열의 부분 문자열 발생을 계산합니다. 일반 문자열을 일치하거나 정규식을 사용할 수 있습니다. 일반 문자열 일치는 겹칠 수 있지만 정규식 일치는 겹칠 수 없습니다.

### <a name="syntax"></a>구문
```
countof(text, search [, kind])
```

### <a name="arguments"></a>인수:
- `text` - 입력 문자열입니다. 
- `search` - text 내에서 일치하는 일반 문자열 또는 정규식입니다.
- `kind` - _normal_ | _regex_(기본값: normal).

### <a name="returns"></a>반환

검색 문자열이 컨테이너에서 일치시킬 수 있는 횟수입니다. 일반 문자열 일치는 겹칠 수 있으며, 정규식 일치는 겹칠 수 없습니다.

### <a name="examples"></a>예

#### <a name="plain-string-matches"></a>일반 문자열 일치

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>정규식 일치

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

지정된 문자열에서 정규식에 대한 일치 항목을 가져옵니다. 또한 필요에 따라 추출된 부분 문자열을 지정된 형식으로 변환합니다.

### <a name="syntax"></a>구문

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>인수

- `regex` - 정규식입니다.
- `captureGroup` - 추출할 캡처 그룹을 나타내는 양의 정수 상수입니다. 0은 전체 일치, 1은 정규식의 첫 번째 '(' 괄호')'에 의해 일치된 값, 2 이상은 이후 괄호를 나타냅니다.
- `text` - 검색할 문자열입니다.
- `typeLiteral` - 선택적 형식 리터럴(예: typeof(long))입니다. 제공된 경우 추출된 부분 문자열이 이 형식으로 변환됩니다.

### <a name="returns"></a>반환
지시된 캡처 그룹 captureGroup에 대해 일치된 부분 문자열, 선택적으로 typeLiteral로 변환됩니다.
일치 항목이 없거나 형식 변환에 실패한 경우 null을 반환합니다.

### <a name="examples"></a>예

다음 예제에서는 하트비트 레코드에서 마지막 8진수 *ComputerIP*를 추출합니다.
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

다음 예제에서는 마지막 8진수추출를 한 후 *real* 형식(number)로 캐스팅하고, 다음 IP 값을 계산합니다.
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

아래 예제에서 "Duration"의 정의로 문자열 *Trace*가 검색됩니다. 일치 항목을 *real*로 캐스팅한 후 시간 상수(1 s)를 곱합니다. 그러면 *Duration이 timespan 형식으로 캐스팅*됩니다.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty*는 인수가 빈 문자열이거나 null인 경우 true를 반환합니다(*isnull* 참조).
- *isnotempty*는 인수가 빈 문자열이거나 null이 아닌 경우 true를 반환합니다(*isnotnull* 참조). 별칭: *notempty*

### <a name="syntax"></a>구문

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>예

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

URL을 해당 부분(프로토콜, 호스트, 포트 등)을 분할하고 이러한 부분을 문자열로 포함하는 사전 개체를 반환합니다.

### <a name="syntax"></a>구문

```
parseurl(urlstring)
```

### <a name="examples"></a>예

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

결과는 다음과 같습니다.
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>replace

모든 정규식 일치 항목을 다른 문자열로 바꿉니다. 

### <a name="syntax"></a>구문

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>인수

- `regex` - 일치 항목을 찾을 정규식입니다. '('괄호')'에 캡처 그룹을 포함할 수 있습니다.
- `rewrite` - 일치하는 정규식에 의해 수행된 모든 일치에 대한 대체 정규식입니다. 전체 일치를 참조하려면 \0, 첫 번째 캡처 그룹을 참조하려면 \1, 이후 캡처 그룹을 참조하려면 \2를 사용하는 식입니다.
- `input_text` - 검색할 입력 문자열입니다.

### <a name="returns"></a>반환
regex의 모든 일치 항목을 rewrite로 바꾼 후의 text입니다. 일치 항목은 겹치지 않습니다.

### <a name="examples"></a>예

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

결과는 다음과 같을 수 있습니다.

작업                                        |대체됨
------------------------------------------------|----------------------------------------------------------
4663 - 개체에 액세스하려고 했습니다.  |활동 ID 4663: 개체에 액세스하려고 했습니다.


## <a name="split"></a>split

지정된 구분 기호에 따라 지정된 문자열을 분할하고 결과 부분 문자열의 배열을 반환합니다.

### <a name="syntax"></a>구문
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>인수:

- `source` -지정된 구분 기호에 따라 분할할 문자열입니다.
- `delimiter` - 소스 문자열을 분할하기 위해 사용될 구분 기호입니다.
- `requestedIndex` - 0부터 시작하는 선택적 인덱스입니다. 제공될 경우, 반환되는 문자열 배열에 해당 항목만 포함됩니다(있는 경우).


### <a name="examples"></a>예

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

문자열 인수를 연결합니다(1-16개 인수 지원).

### <a name="syntax"></a>구문
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>예
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

문자열의 길이를 반환합니다.

### <a name="syntax"></a>구문
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>예
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

지정된 인덱스에서 시작하여 지정된 소스 문자열의 부분 문자열을 추출합니다. 선택적으로 요청된 부분 문자열의 길이를 지정할 수 있습니다.

### <a name="syntax"></a>구문
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>인수:

- `source` - 소스 문자열을 가져올 소스 문자열입니다.
- `startingIndex` - 요청된 부분 문자열의 0부터 시작하는 시작 문자 위치입니다.
- `length` - 반환된 부분 문자열의 요청된 길이를 지정하는 데 사용할 수 있는 선택적 매개 변수입니다.

### <a name="examples"></a>예
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

지정된 문자열을 모두 소문자 또는 모두 대문자로 변환합니다.

### <a name="syntax"></a>구문
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>예
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>다음 단계
다음과 같은 고급 자습서를 계속 진행합니다.
* [집계 함수](aggregations.md)
* [고급 집계](advanced-aggregations.md)
* [차트 및 다이어그램](charts.md)
* [JSON 및 데이터 구조 사용](json-data-structures.md)
* [고급 쿼리 작성](advanced-query-writing.md)
* [조인 - 분석 간](joins.md)
