---
title: 매핑 데이터 흐름의 식 함수
description: 데이터 흐름 매핑의 식 함수에 대해 알아봅니다.
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: 5492850944b04ea4b4d30dd206e7b297b34c075c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062849"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>데이터 흐름 매핑의 데이터 변환 식 

## <a name="expression-functions"></a>식 함수

Data Factory에서 데이터 흐름 매핑 기능의 식 언어를 사용 하 여 데이터 변환을 구성 합니다.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자의 절대값입니다.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>add</code>* ``acos(1) -> 0.0``코사인 역 값을 계산 
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
문자열 또는 숫자의 쌍을 추가합니다. 날짜에 일 수를 추가합니다. 타임 스탬프에 기간을 추가 합니다. 유사한 형식의 한 배열을 다른 배열에 추가합니다. \+ 연산자 * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임스탬프에 일을 추가합니다. 날짜 * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>에 대한 + 연산자와 동일 
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
날짜 또는 타임 스탬프에 월을 추가 합니다. 필요에 따라 표준 시간대 * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>를 전달할 수 있습니다 
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리적 AND 연산자입니다. & & * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>atan</code>* ``asin(0) -> 0.0``역 사인 값을 계산 
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>atan2</code>* ``atan(0) -> 0.0``역 탄젠트 값을 계산 
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
평면의 양의 x 축과 좌표 * ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>지정 된 점의 각도를 라디안으로 반환 
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
스트림에서 이름을 기준으로 열 값을 선택 합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다. 일치 하는 항목이 여러 개 있는 경우 첫 번째 일치 항목이 반환 됩니다. 일치 하는 항목이 없으면 NULL 값을 반환 합니다. 반환 된 값은 형식 변환 함수 (TO_DATE, TO_STRING ...) 중 하나로 변환 되어야 합니다.  디자인 타임에 알려진 열 이름은 해당 이름 으로만 처리 되어야 합니다. 계산 된 입력은 지원 되지 않지만 매개 변수 대체 * ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>를 사용할 수 있습니다 
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
스트림에서 상대 위치 (1부터) 만큼 열 값을 선택 합니다. 위치가 범위를 벗어나면 NULL 값을 반환 합니다. 반환 된 값은 형식 변환 함수 (TO_DATE, TO_STRING ...) 중 하나로 변환 되어야 합니다. 계산 된 입력은 지원 되지 않지만 매개 변수 대체 * ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>를 사용할 수 있습니다 
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
교대로 나오는 조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 입력 수가 짝수인 경우에는 마지막 조건 * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>에 대한 기본값은 NULL입니다 
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
숫자 * ``cbrt(8) -> 2.0``
___
### <code>ceil</code>의 큐브 루트를 계산 
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>

___
### <code>coalesce</code>* ``ceil(-0.1) -> 0``수 보다 작은 정수 중 가장 작은 정수를 반환 
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
입력 집합에서 null이 아닌 첫 번째 값을 반환 합니다. 모든 입력은 
___
### <code>compare</code>
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``* ``coalesce(10, 20) -> 10``동일한 유형 이어야 
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
동일한 형식의 두 값을 비교 합니다. Value1 < value2 인 경우 음의 정수를 반환 하 고, value1 = = value2 이면 0을 반환 하 고, value1 > value2 * ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>값을 반환 
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 함께 연결합니다. 문자열이 있는 + 연산자와 같습니다. * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
가변 개수의 문자열을 구분 기호와 함께 연결합니다. 첫 번째 매개 변수는 구분 기호 * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
제공 된 배열의 요소가 제공 된 조건자에서 true로 평가 되 면 true를 반환 합니다. Contains에는 
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>* ``contains([1, 2, 3, 4], #item == 3) -> true``#item 조건자 함수의 한 요소에 대한 참조가 필요 
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>cosh</code>* ``cos(10) -> -0.8390715290764524``코사인 값을 계산 
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>crc32</code>* ``cosh(0) -> 1.0``값의 하이퍼볼릭 코사인을 계산 
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
0(256), 224, 256, 384, 512 값의 지정된 비트 길이를 갖는 다양한 기본 데이터 형식의 열 세트에서 CRC32 해시를 계산합니다. 이를 사용 하 여 행 * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>에 대한 지문을 계산할 수 있습니다 
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
이 작업 실행이 시작되는 현재 날짜를 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
로컬 표준 시간대 * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>를 사용 하 여 작업을 실행 하기 시작할 때 현재 타임 스탬프를 가져옵니다 
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
현재 타임 스탬프를 UTC로 가져옵니다. 현재 시간을 클러스터 표준 시간대와 다른 표준 시간대로 해석 하려면 선택적인 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. 기본적으로 현재 표준 시간대로 설정 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)를 참조하세요. UTC 시간을 다른 표준 시간대로 변환 하려면 fromUTC () * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>을 사용 
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜가 지정 된 날짜 * ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>의 날짜를 가져옵니다 
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
지정된 날짜의 요일을 가져옵니다. 1-일요일, 2-월요일 ..., 7-토요일 * ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜가 지정 된 날짜 * ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>날짜를 가져옵니다 
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
기간 (일)의 기간 (밀리초) * ``days(2) -> 172800000L``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>divide</code>* ``degrees(3.141592653589793) -> 180``라디안을도로 변환 
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 나눕니다. /연산자 * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>와 동일 
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 제공 된 문자열 * ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>를 사용 하 여 끝나는지 확인 
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 같음 연산자. = = 연산자 * ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
비교는 대/소문자를 무시하는 연산자와 같습니다. < = > 연산자 * ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
숫자의 계승값을 계산 하 여 
___
### <code>false</code>* ``factorial(5) -> 120``
<code><b>false() => boolean</b></code><br/><br/>
항상 false 값을 반환합니다. ' F a l s e ' 라는 열이 있는 경우 함수 구문 (false ())을 사용 하 * ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
제공 된 조건자를 충족 하지 않는 배열에서 요소를 필터링 합니다. 필터에는 조건자 함수의 한 요소에 대한 참조가 #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>으로 필요 
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>

___
### <code>fromBase64</code>* ``floor(-0.1) -> -1``수 보다 크지 않은 가장 큰 정수를 반환 
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Base64 * ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>에서 지정 된 문자열을 인코딩합니다 
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
UTC의 타임 스탬프로 변환 합니다. 필요에 따라 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. 사용 가능한 형식에 대한 현재 timezoneRefer Java의 SimpleDateFormat로 기본값을 사용 합니다. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 큼 연산자. > 연산자 * ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 크거나 같음 연산자. > = 연산자 * ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>와 동일 
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
값 목록 중에서 null 값을 건너뛴 입력으로 가장 큰 값을 반환 합니다. 모든 입력이 null 이면 null을 반환 하 고 
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>* ``greatest(10, 30, 15, 20) -> 30``
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
스트림에서 이름으로 열 값을 확인 합니다. 선택적 스트림 이름을 두 번째 인수로 전달할 수 있습니다.  디자인 타임에 알려진 열 이름은 해당 이름 으로만 처리 되어야 합니다. 계산 된 입력은 지원 되지 않지만 매개 변수 대체를 사용 하 여 
___
### <code>hour</code>* ``hasColumn('parent')``수 있습니다 
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
타임스탬프의 시간 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
시간 * ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code>의 시간 (밀리초)<br/><br/>
조건에 따라 하나의 값 또는 다른 값이 적용됩니다. 다른가 지정 되지 않은 경우 NULL로 간주 됩니다. 두 값 모두 호환 되어야 합니다 (numeric, string ...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
값이 NULL이 아닌지 여부를 확인 하 고 다른 값을 반환 합니다. * ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>에서 null이 아닌 첫 번째 값을 찾을 때까지 모든 입력을 테스트 
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
항목이 배열에 있는지 확인 * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
모든 단어의 첫 글자를 대문자로 변환합니다. 단어는 공백으로 구분 되는 
___
### <code>instr</code>* ``initCap('cool iceCREAM') -> 'Cool Icecream'``으로 식별 됩니다 
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. * ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>를 찾을 수 없는 경우 0이 반환 됩니다 
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삭제용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 오류로 표시되는지 여부를 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 무시되도록 표시되는지 여부를 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
조회 시 행이 일치하는지 여부를 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
값이 NULL 인지 여부를 확인 * ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 업데이트용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
행이 삽입용으로 표시되어 있는지 확인합니다. 입력 스트림을 두 개 이상 사용 하는 변환의 경우 스트림의 (1부터 기반) 인덱스를 전달할 수 있습니다. 스트림 인덱스는 1 또는 2 여야 하 고 기본값은 1 * ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
날짜 * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>지정 된 월의 마지막 날짜를 가져옵니다 
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
비교 보다 작거나 같음 연산자. < = 연산자 * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>와 동일 
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
인덱스 1에서 시작하여 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING (str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>

___
### <code>lesser</code>* ``length('dumbo') -> 5``문자열의 길이를 반환 
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 작음 연산자. < 연산자 * ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 보다 작거나 같음 연산자. < = 연산자 * ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>와 동일 
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>

___
### <code>like</code>* ``levenshtein('boys', 'girls') -> 4``두 문자열 간의 levenshtein 거리를 가져옵니다 
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
패턴은 문자 그대로 일치 하는 문자열입니다. 예외는 다음과 같은 특수 한 기호입니다. _은 입력에서 한 문자 (와 유사)를 찾습니다. .과 유사). %는 입력의 0개 이상의 문자와 일치합니다(posix 정규식의 .*와 유사).
이스케이프 문자는 ''입니다. 특수 기호 또는 다른 이스케이프 문자 앞에 이스케이프 문자가 오면 다음 문자는 글자 그대로 일치됩니다. 다른 모든 문자를 이스케이프하는 것은 유효하지 않습니다.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
특정 위치에서 시작하는 문자열 내에서 부분 문자열의 위치(1부터 시작)를 찾습니다. 이 위치를 생략하면 문자열의 시작 부분에서 찾습니다. * ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>를 찾을 수 없는 경우 0이 반환 됩니다 
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
로그 값을 계산합니다. 선택적인 base는 
___
### <code>log10</code>* ``log(100, 10) -> 2``사용 하는 경우 오일러 번호를 제공할 수 있습니다 
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
10 개의 기본 * ``log10(100) -> 2``
___
### <code>lower</code>를 기반으로 하는 로그 값을 계산 
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열 * ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>를 Lowercases 
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 왼쪽 여백을 채웁니다. 문자열이 길이 보다 크거나 같은 경우 길이 * ``lpad('dumbo', 10, '-') -> '-----dumbo'``
* ``lpad('dumbo', 4, '-') -> 'dumb'``
* ' ' lpad (' dumbo ', 8, ' < > ')-> ' < ><dumbo'``
___
### <code>ltrim</code>로 잘립니다 
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
왼쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않으면 두 번째 매개 변수 * ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>에 지정 된 모든 문자를 자릅니다 
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
제공 된 식을 사용 하 여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 식 함수에서 하나의 요소에 대한 참조가 필요한 #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
제공 된 식을 사용 하 여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 #item 식 함수의 한 요소에 대한 참조와 #index * ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>요소 인덱스에 대한 참조가 필요 
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
다양한 기본 데이터 형식의 열 세트에서 MD5 다이제스트를 계산하고 32자의 16진수 문자열을 반환합니다. 이를 사용 하 여 행 * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>에 대한 지문을 계산할 수 있습니다 
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
날짜의 밀리초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
* ``milliseconds(2) -> 2L``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code> 시간 (밀리초)<br/><br/>
숫자를 뺍니다. 날짜에서 일 수를 뺍니다. 타임 스탬프의 substract 기간 두 타임 스탬프는 차이를 얻기 위해 두 타임 스탬프를 작동 합니다 (밀리초 단위). -Operator * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
타임스탬프의 분 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code> * ``minutes(2) -> 120000L``시간 (밀리초)<br/><br/>
숫자 쌍의 모듈러스입니다. % Operator * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>와 동일 
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>

___
### <code>monthsBetween</code>* ``month(toDate('2012-8-8')) -> 8``날짜 또는 타임 스탬프의 월 값을 가져옵니다 
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
두 날짜 사이의 개월 수를 가져옵니다. 계산을 반올림할 수 있습니다. 선택적인 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍을 곱합니다. \* 연산자 * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>negate</code>와 동일 
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
숫자를 부정합니다. 양수를 음수 또는 
___
### <code>nextSequence</code>* ``negate(13) -> -13``반대로 바꿉니다 
<code><b>nextSequence() => long</b></code><br/><br/>
고유한 다음 시퀀스를 반환합니다. 이 수는 파티션 내 에서만 연속 되며 partitionId * ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>앞에 옵니다 
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
문자열 값을 정규화 하 여 악센트가 있는 유니코드 문자를 구분 하는 
___
### <code>not</code>* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리 부정 연산자 * ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
비교 같지 않음 연산자. ! = 연산자 * ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
값이 NULL이 아닌지 여부를 확인 * ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
NULL 값을 반환합니다. 'Null'라는 열이 있는 경우 함수 syntax(null())를 사용합니다. 에서 사용 하는 모든 작업은 NULL * ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>을 반환 
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리적 OR 연산자입니다. | |와 동일 * ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
숫자 쌍의 양의 모듈러스입니다.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
입력 행이 * ``partitionId()``
___
### <code>power</code>에 있는 현재 파티션 id를 반환 
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
한 숫자를 다른 * ``power(10, 2) -> 100``
___
### <code>reduce</code>의 기능으로 거듭제곱 
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
배열의 요소를 누적 합니다. 줄이기는 첫 번째 식 함수에서 누적기 및 one 요소에 대한 참조를 #acc 및 #item으로 예상 하며, 결과 값이 두 번째 식 함수에 사용 되는 #result * ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
지정된 정규식 패턴에 대해 일치하는 부분 문자열을 추출합니다. 마지막 매개 변수는 일치 그룹을 식별하고 생략하면 기본적으로 1이 지정됩니다. * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>를 이스케이프 하지 않고 문자열을 일치 시키려면 '<regex>' (큰따옴표)를 사용 
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 지정된 정규식 패턴과 일치하는지 확인합니다. * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>를 이스케이프 하지 않고 문자열을 일치 시키려면 '<regex>' (큰따옴표)를 사용 
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Regex 패턴의 모든 항목을 지정 된 문자열의 다른 부분 문자열로 바꿉니다 .이는 '<regex>' (back 따옴표)를 사용 하 * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>이스케이프 하지 않고 문자열과 일치 
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Regex에 따라 구분 기호를 기반으로 문자열을 분할 하 고 
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``문자열의 배열을 반환 
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
모든 부분 문자열을 지정 된 문자열의 다른 부분 문자열로 바꿉니다. 마지막 매개 변수를 생략 하는 경우 기본값은 빈 문자열 * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열 * ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>를 반대로 바꿉니다 
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
오른쪽부터 지정된 문자 수를 갖는 부분 문자열을 추출합니다. SUBSTRING (str, LENGTH (str)-n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 지정 된 regex 패턴과 일치 하는지 여부를 확인 * ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
선택적인 배율 및 선택적 반올림 모드를 지정 하 여 숫자를 반올림 합니다. 소수 자릿수가 생략 된 경우 기본값은 0입니다.  이 모드를 생략 하면 기본적으로 ROUND_HALF_UP (5)로 설정 됩니다. 반올림 값에는 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
특정 길이가 될 때까지 제공된 패딩으로 문자열의 오른쪽 여백을 채웁니다. 문자열이 길이 보다 크거나 같은 경우에는 
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``
___
### <code>rtrim</code>rtrim</code>* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``
* ``rpad('dumbo', 4, '-') -> 'dumb'``길이로 잘립니다 
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
오른쪽의 선행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않으면 두 번째 매개 변수 * ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>에 지정 된 모든 문자를 자릅니다 
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
날짜의 초 값을 가져옵니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 로컬 표준 시간대가 기본값으로 사용 됩니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code> * ``seconds(2) -> 2000L``시간 (밀리초)<br/><br/>
다양한 기본 데이터 형식의 열 세트에서 SHA-1 다이제스트를 계산하고 40자의 16진수 문자열을 반환합니다. 이를 사용 하 여 행 * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>에 대한 지문을 계산할 수 있습니다 
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
0 (256), 224, 256, 384, 512 값만 사용할 수 있는 비트 길이를 지정 하 여 다양 한 기본 데이터 형식의 열 집합에 대한 SHA-2 다이제스트를 계산 합니다. 이를 사용 하 여 행 * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>에 대한 지문을 계산할 수 있습니다 
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>sinh</code>* ``sin(2) -> 0.9092974268256817``사인 값을 계산 
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>slice</code>* ``sinh(0) -> 0.0``하이퍼볼릭 사인 값을 계산 
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
특정 위치에서 배열의 하위 세트를 추출합니다. 위치는 1부터 시작합니다. 길이를 생략 하면 기본적으로 문자열 * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>sort</code>의 끝이 됩니다 
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
제공 된 조건자 함수를 사용 하 여 배열을 정렬 합니다. Sort는 #item1 식 함수에서 두 개의 연속 된 요소에 대한 참조를 예상 하 고 #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열 * ``soundex('genius') -> 'G520'``
___
### <code>split</code>의 soundex 코드를 가져옵니다 
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
구분 기호를 기반으로 문자열을 분할 하 고 
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``문자열 배열을 반환 
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>startsWith</code>* ``sqrt(9) -> 3``숫자의 제곱근을 계산 
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
문자열이 제공 된 문자열 * ``startsWith('dumbo', 'du') -> true``
___
### <code>subDays</code>를 사용 하 여 시작 되는지 확인 
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임 스탬프에서 월을 뺍니다. 날짜 * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>의-연산자와 동일 
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
날짜 또는 타임 스탬프에서 월을 빼서 
___
### <code>substring</code>* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
특정 위치에서 특정 길이의 부분 문자열을 추출합니다. 위치는 1부터 시작합니다. 길이를 생략 하면 기본적으로 문자열 * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>tan</code>의 끝이 됩니다 
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>tanh</code>* ``tan(0) -> 0.0``접선 값을 계산 
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>toBase64</code>* ``tanh(0) -> 0.0``하이퍼볼릭 탄젠트 값을 계산 
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Base64 * ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>에서 지정 된 문자열을 인코딩합니다 
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
숫자/날짜/타임 스탬프/문자열을 이진 표현으로 변환 하 * ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
값 (' t ', ' true ', ' y ', ' yes ', ' 1 ')을 true 및 (' f ', ' false ', ' n ', ' no ', ' 0 ')로 변환 하 고 다른 모든 값 * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
선택적인 입력 날짜 형식을 사용 하 여 입력 날짜 문자열을 날짜로 변환 합니다. 사용 가능한 형식에 대한 자세한 내용은 Java의 SimpleDateFormat를 참조 하세요. 입력 날짜 형식이 생략 된 경우 기본 형식은 yyyy-[M] M-[d] d입니다. 허용 되는 형식은 다음과 같습니다. [yyyy, yyyy-[M] M, yyyy-[M] M-[d] d, yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
숫자 또는 문자열을 10진수 값으로 변환합니다. 자릿수와 소수 자릿수를 지정하지 않으면 기본값인 (10,2)가 사용됩니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. En-us, de, zh-cn * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>와 같은 BCP47 언어 형식의 선택적 로캘 형식 
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
숫자 또는 문자열을 배정도 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. En-us, de, zh-cn * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>와 같은 BCP47 언어 형식의 선택적 로캘 형식 
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
숫자 또는 문자열을 부동 소수점 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 
___
### <code>toInteger</code>
* ``toFloat('$123.45', '$###.00') -> 123.45f``
* ``toFloat('123.45') -> 123.45f``모든 double * ``toFloat(123.45) -> 123.45f``을 자릅니다 
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
숫자 또는 문자열을 정수 값으로 변환 합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. Long, float, double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>을 자릅니다 
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
숫자 또는 문자열을 긴 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. Float, double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>을 자릅니다 
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
숫자 또는 문자열을 짧은 값으로 변환합니다. 변환을 위해 선택적인 Java 10진수 형식을 사용할 수 있습니다. 정수, long, float, double * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>을 자릅니다 
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
기본 데이터 형식을 문자열로 변환합니다. 숫자 및 날짜의 경우 형식을 지정할 수 있습니다. 지정하지 않으면 시스템 기본값은 picked.Java입니다. 숫자에는 10진수 형식이 사용됩니다. 가능한 모든 날짜 형식에 대해서는 Java SimpleDateFormat를 참조 하세요. 기본 형식은 yyyy-MM-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
선택적 타임 스탬프 형식을 지정 하 여 문자열을 타임 스탬프로 변환 합니다. 가능한 모든 형식에 대해서는 Java SimpleDateFormat를 참조 하세요. 타임 스탬프를 생략 하면 기본 패턴이 사용 됩니다. yyyy-[M] M-[d] d hh: mm: ss [.f ...]가 사용 됩니다. 선택적인 표준 시간대를 ' GMT ', ' PST ', ' UTC ', ' 아메리카/케이맨 ' 형식으로 전달할 수 있습니다. Timestamp는 999Refer을 사용 하 여 최대 밀리초의 정확도를 지원 하며, 사용 가능한 형식에 대해 Java의 SimpleDateFormat를 참조 합니다. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
타임스탬프를 UTC로 변환합니다. 'GMT', 'PST', 'UTC', 'America/Cayman' 형태로 선택적 표준 시간대를 제공할 수 있습니다. 사용 가능한 형식에 대한 현재 timezoneRefer Java의 SimpleDateFormat로 기본값을 사용 합니다. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
하나의 문자 세트를 문자열의 다른 문자 세트로 바꿉니다. 문자에는 1 ~ 1 개의 대체 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
선행 및 후행 문자열을 삭제합니다. 두 번째 매개 변수를 지정하지 않으면 공백을 삭제합니다. 그렇지 않으면 두 번째 매개 변수 * ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>에 지정 된 모든 문자를 자릅니다 
<code><b>true() => boolean</b></code><br/><br/>
항상 true 값을 반환합니다. ' T r u e ' 라는 열이 있는 경우 함수 구문 (true ())을 사용 하 * ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
열의 형식과 일치합니다. 패턴 식 에서만 사용할 수 있습니다. number는 short, integer, long, double, float 또는 decimal과 일치 하는 정수, 정수, 정수, 소수는 double, float, decimal 및 datetime과 일치 하는 날짜 또는 타임 스탬프 형식 * ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
문자열 * ``upper('bojjus') -> 'BOJJUS'``
___
### <code>uuid</code>를 Uppercases 
<code><b>uuid() => string</b></code><br/><br/>
생성 된 UUID * ``uuid()``
___
### <code>weekOfYear</code>를 반환 
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
날짜 * ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>지정 된 연간 주를 가져옵니다 
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
주 수의 기간 (밀리초) * ``weeks(2) -> 1209600000L``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
논리 XOR 연산자입니다. ^ Operator * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
집계 함수를 
## 하 * ``year(toDate('2012-8-8')) -> 2012``날짜의 연도 값을 가져옵니다. 다음 함수는 집계, 피벗, 피벗 해제 및 창 변환 ___
### <code>avg</code>에만 사용할 수 있습니다 
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
열 * ``avg(sales)``
___
### <code>avgIf</code>값의 평균을 가져옵니다 
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
조건에 따라 열 * ``avgIf(region == 'West', sales)``
___
### <code>count</code>값의 평균을 가져옵니다 
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
값의 집계 개수를 가져옵니다. 선택적 열을 지정 하면 count * ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>에서 NULL 값이 무시 됩니다 
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>

___
### <code>countIf</code>* ``countDistinct(custId, custName)``열 집합에 대한 고유 값의 집계 수를 가져옵니다 
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
조건에 따라 값의 집계 개수를 가져옵니다. 선택적 열이 지정 된 경우 count * ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>에서 NULL 값을 무시 
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>covariancePopulationIf</code>* ``covariancePopulation(sales, profit)``두 열 간의 모집단 공 분산을 가져옵니다 
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>covarianceSample</code>* ``covariancePopulationIf(region == 'West', sales)``두 열의 인구 공분산을 가져옵니다 
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>covarianceSampleIf</code>* ``covarianceSample(sales, profit)``두 열의 표본 공 분산을 가져옵니다 
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>first</code>* ``covarianceSampleIf(region == 'West', sales, profit)``두 열의 샘플 공 분산을 가져옵니다 
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
열 그룹의 첫 번째 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls이 생략 된 경우 false * ``first(sales)``
* ``first(sales, false)``
___
### <code>kurtosis</code>으로 간주 됩니다 
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``kurtosis(sales)``
___
### <code>kurtosisIf</code>의 첨도를 가져옵니다 
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 열 * ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>의 첨도를 가져옵니다 
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
열 그룹의 마지막 값을 가져옵니다. 두 번째 매개 변수 ignoreNulls이 생략 된 경우 false * ``last(sales)``
* ``last(sales, false)``
___
### <code>max</code>으로 간주 됩니다 
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
열 * ``max(sales)``
___
### <code>maxIf</code>의 최대값을 가져옵니다 
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
조건에 따라 
___
### <code>mean</code>* ``maxIf(region == 'West', sales)``열에 대한 최대값을 가져옵니다 
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
열 값의 평균값을 가져옵니다. AVG * ``mean(sales)``
___
### <code>meanIf</code>와 동일 
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
조건에 따라 열 값의 평균값을 가져옵니다. AvgIf * ``meanIf(region == 'West', sales)``
___
### <code>min</code>와 동일 
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
열의 최소값을 가져옵니다. * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
조건에 따라 
___
### <code>skewness</code>* ``minIf(region == 'West', sales)``열의 최소값을 가져옵니다 
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``skewness(sales)``
___
### <code>skewnessIf</code>의 왜곡도를 가져옵니다 
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>stddev</code>* ``skewnessIf(region == 'West', sales)``열에 대한 왜곡도를 가져옵니다 
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``stdDev(sales)``
___
### <code>stddevIf</code>의 표준 편차를 가져옵니다 
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>stddevPopulation</code>* ``stddevIf(region == 'West', sales)``열에 대한 표준 편차를 가져옵니다 
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>의 모집단 표준 편차를 가져옵니다 
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>stddevSample</code>* ``stddevPopulationIf(region == 'West', sales)``열에 대한 모집단 표준 편차를 가져옵니다 
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>의 샘플 표준 편차를 가져옵니다 
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>sum</code>* ``stddevSampleIf(region == 'West', sales)``열에 대한 샘플 표준 편차를 가져옵니다 
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>

___
### <code>sumDistinct</code>* ``sum(col)``숫자 열의 집계 합계를 가져옵니다 
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>

___
### <code>sumDistinctIf</code>* ``sumDistinct(col)``숫자 열에 대한 고유 값의 집계 합계를 가져옵니다 
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 조건은 * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>열을 기반으로 할 수 있습니다 
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
기준에 따라 숫자 열의 집계 합계를 가져옵니다. 조건은 * ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>열을 기반으로 할 수 있습니다 
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``variance(sales)``
___
### <code>varianceIf</code>의 분산을 가져옵니다 
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>variancePopulation</code>* ``varianceIf(region == 'West', sales)``열에 대한 분산을 가져옵니다 
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>의 모집단 분산을 가져옵니다 
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
___
### <code>varianceSample</code>* ``variancePopulationIf(region == 'West', sales)``열에 대한 모집단 분산을 가져옵니다 
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
열 * ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>의 비편향 분산을 가져옵니다 
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
조건에 따라 
## 창 함수 * ``varianceSampleIf(region == 'West', sales)``열에 대한 비편향 분산을 가져옵니다. 다음 함수는 창 변환 ___
### <code>cumeDist</code>에서만 사용할 수 있습니다 
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 함수는 파티션의 모든 값을 기준으로 값의 위치를 계산합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행 수를 창 파티션의 총 행 수로 나눈 값입니다. 정렬의 모든 동률 값은 동일한 위치로 평가 됩니다.
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
창의 order by 절에 지정 된 값 그룹에 있는 값의 순위를 계산 합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성하지 않습니다. 조밀한 Rank는 데이터가 정렬 되지 않은 경우에도 작동 하 고 * ``denseRank()``
___
### <code>lag</code>값의 변경 내용을 찾습니다 
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
현재 행보다 n개 행 전에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 다시 검색할 행 수 이며 기본값은 1입니다. 기본값이 없는 경우에는 기본값을 지정 하지 않으면 null 값이 반환 되 고 * ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
현재 행보다 n개 행 뒤에 평가되는 첫 번째 매개 변수의 값을 가져옵니다. 두 번째 매개 변수는 앞으로 조회할 행의 수이 고 기본값은 1입니다. 기본값이 없는 경우에는 기본값을 지정 하지 않으면 null 값이 반환 되 고 * ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
NTile 함수는 각 창 파티션의 행을 1부터 `n`개 이내의 `n` 버킷 수로 나눕니다. 버킷 값 차이는 최대 1 이내입니다. 파티션의 행 수가 버킷 수로 균일하게 나누어 떨어지지 않으면 나머지 값은 첫 번째 버킷부터 시작해서 하나씩 분산됩니다. NTile 함수는 tertiles, 변 위치, deciles 및 기타 일반적인 요약 통계의 계산에 유용 합니다. 함수는 초기화 하는 동안 두 변수를 계산 합니다. 일반 버킷의 크기에는 하나의 추가 행이 추가 됩니다. 두 변수는 현재 파티션의 크기를 기준으로 합니다. 계산 프로세스 동안 이 함수는 현재 행 번호, 현재 버킷 수 및 버킷이 변경되는 행 번호(bucketThreshold)를 추적합니다. 현재 행 번호가 버킷 임계값에 도달하면 버킷 값이 1씩 커지고, 임계값은 버킷 크기만큼 커집니다(현재 버킷이 채워질 경우 하나씩 추가).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
창의 order by 절에 지정 된 값 그룹에 있는 값의 순위를 계산 합니다. 결과는 파티션 순서에서 현재 행보다 앞에 있거나 같은 위치에 있는 행의 수에 1을 더한 것입니다. 값은 시퀀스에 간격을 생성합니다. Rank는 데이터가 정렬 되지 않은 경우에도 작동 하며 * ``rank()``
___
### <code>rowNumber</code>값의 변경 내용을 찾습니다 
<code><b>rowNumber() => integer</b></code><br/><br/>
1부터 시작하는 창에서 행에 대해 순차적인 번호 매기기를 할당합니다. * ``rowNumber()``

## <a name="next-steps"></a>다음 단계

[식 작성기를 사용 하는 방법을 알아봅니다](concepts-data-flow-expression-builder.md).
