---
title: 시스템 함수
description: Azure Cosmos DB의 SQL 시스템 함수에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342846"
---
# <a name="system-functions"></a>시스템 함수

 Cosmos DB는 많은 기본 제공 SQL 함수를 제공합니다. 기본 제공 함수의 범주는 다음과 같습니다.  
  
|함수|설명|  
|--------------|-----------------|  
|[수치 연산 함수](#mathematical-functions)|수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다.|  
|[형식 검사 함수](#type-checking-functions)|형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다.|  
|[문자열 함수](#string-functions)|문자열 함수는 문자열 입력 값에 대한 연산을 수행하고, 문자열, 숫자 또는 부울 값을 반환합니다.|  
|[배열 함수](#array-functions)|배열 함수는 배열 입력 값에 대한 연산을 수행하고, 숫자, 부울 또는 배열 값을 반환합니다.|
|[날짜 및 시간 함수](#date-time-functions)|날짜 및 시간 함수를 사용 하면 두 가지 형태로; 현재 UTC 날짜 및 시간을 가져오려면 Unix epoch 시간 (밀리초) 또는 ISO 8601 형식을 준수 하는 문자열 값인 숫자 타임 스탬프입니다.|
|[공간 함수](#spatial-functions)|공간 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다.|  

각 범주 내에서 함수의 목록은 다음과 같습니다.

| 함수 그룹 | 작업 |
|---------|----------|
| 수치 연산 함수 | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| 형식 검사 함수 | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| 문자열 함수 | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| 배열 함수 | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH 및 ARRAY_SLICE |
| 날짜 및 시간 함수 | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| 공간 함수 | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

사용자 정의 함수 (UDF) 기본 제공 함수를 이제 사용할 수 있는, 현재 사용 중인 경우에 해당 기본 제공 함수를 더 빨리 실행 되 고 보다 효율적인 됩니다.

Cosmos DB 함수와 ANSI SQL 간의 주요 차이점은 Cosmos DB 함수는 스키마 없는 및 혼합 된 스키마 데이터에서 잘 작동 하도록 설계 되었다는 합니다. 예를 들어 속성이 누락 되었거나 숫자가 아닌 값과 같은 경우 `unknown`, 오류를 반환 하는 대신 항목을 건너뜁니다.

##  <a name="mathematical-functions"></a> 수치 연산 함수  

수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다.

다음 예제와 같이 쿼리를 실행할 수 있습니다.

```sql
    SELECT VALUE ABS(-4)
```

결과는 다음과 같습니다.

```json
    [4]
```

다음은 지원되는 기본 제공 수치 연산 함수 표입니다.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[각도](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[로그](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[전원](#bk_power)|  
|[라디안](#bk_radians)|[반올림](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[정사각형](#bk_square)|[로그인](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 지정한 숫자 식의 절대(양수) 값을 반환합니다.  
  
 **구문**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 세 개의 다른 숫자에 ABS 함수를 사용한 결과를 보여 줍니다.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 코사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크코사인이라고도 합니다.  
  
 **구문**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 -1의 ACOS를 반환합니다.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크사인이라고도 합니다.  
  
 **구문**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 -1의 ASIN을 반환합니다.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 탄젠트 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크탄젠트라고도 합니다.  
  
 **구문**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 값의 ATAN을 반환합니다.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 y/x에 대한 아크 탄젠트의 주요 값(라디안 단위)을 반환합니다.  
  
 **구문**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 x 및 y 구성 요소에 대한 ATN2를 계산합니다.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 지정한 숫자 식보다 크거나 같은 가장 작은 정수 값을 반환합니다.  
  
 **구문**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 CEILING 함수를 사용하여 양수, 음수 및 0 값을 보여 줍니다.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 지정된 식의 라디안에서 지정된 각도의 삼각 코사인을 반환합니다.  
  
 **구문**  
  
```  
COS(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 각도의 COS를 계산합니다.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 지정된 숫자 식의 라디안에서 지정된 각도의 삼각 코탄젠트를 반환합니다.  
  
 **구문**  
  
```  
COT(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 각도의 COT를 계산합니다.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 라디안에서으로 지정 된 각도 (도) 해당 각도 반환 합니다.  
  
 **구문**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 PI/2 라디안 각도의 각도 수를 반환합니다.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 지정한 숫자 식보다 작거나 같은 가장 큰 정수 값을 반환합니다.  
  
 **구문**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 FLOOR 함수를 사용하여 양수, 음수 및 0 값을 보여 줍니다.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 지정된 숫자 식의 지수 값을 반환합니다.  
  
 **구문**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **주의**  
  
  **e**(2.718281…) 상수는 자연 로그의 밑입니다.  
  
  숫자의 지수는 **e** 상수를 거듭제곱하는 횟수입니다. 예를 들어 EXP(1.0) = e^1.0 = 2.71828182845905이며, EXP(10) = e^10 = 22026.4657948067입니다.  
  
  숫자의 자연 로그의 지수는 숫자 자체, 즉 EXP (LOG (n)) = n입니다. 그리고 숫자의 지수의 자연 로그도 숫자 자체, 즉 LOG (EXP (n)) = n입니다.  
  
  **예**  
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 지수 값을 반환합니다.  
  
```  
SELECT EXP(10) AS exp  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 다음 예제에서는 20의 자연 로그의 지수 값과 및 20의 지수의 자연 로그를 반환합니다. 이러한 함수는 서로 역함수이므로 두 경우 모두 부동 소수점 수치가 반올림된 반환 값은 20입니다.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 지정된 숫자 식의 자연 로그를 반환합니다.  
  
 **구문**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
- `base`  
  
   로그의 밑을 설정하는 선택적 숫자 인수입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **설명**  
  
  LOG()는 기본적으로 자연 로그를 반환합니다. 선택적인 base 매개 변수를 사용하여 로그의 밑을 다른 값으로 변경할 수 있습니다.  
  
  자연 로그는 **e**를 밑으로 하는 로그입니다. 여기서 **e**는 대략 2.718281828과 같은 무리 상수입니다.  
  
  그리고 숫자의 지수의 자연 로그도 숫자 자체, 즉 LOG( EXP( n ) ) = n입니다. 그리고 숫자의 자연 로그의 지수도 숫자 자체, 즉 EXP( LOG( n ) ) = n입니다.  
  
  **예**  
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 로그 값을 반환합니다.  
  
```  
SELECT LOG(10) AS log  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 다음 예제에서는 숫자의 지수에 대한 LOG를 계산합니다.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 지정한 숫자 식의 상용 로그(밑 10)를 반환합니다.  
  
 **구문**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **주의**  
  
  LOG10과 POWER 함수는 서로 역의 관계가 있습니다. 예를 들어 10 ^ LOG10(n) = n입니다.  
  
  **예**  
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (100)의 LOG10 값을 반환합니다.  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 PI의 상수 값을 반환합니다.  
  
 **구문**  
  
```  
PI ()  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 PI의 값을 반환합니다.  
  
```  
SELECT PI() AS pi 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 지정된 식의 값을 지정된 거듭제곱으로 반환합니다.  
  
 **구문**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
- `y`  
  
   `numeric_expression`이 거듭제곱되는 지수입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 숫자를 3의 지수로 거듭제곱합니다(숫자의 3제곱).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 숫자 식을 단위로 입력하면 라디안을 반환합니다.  
  
 **구문**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 몇 개의 각도를 입력으로 사용하여 해당 라디안 값을 반환합니다.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  결과 집합은 다음과 같습니다.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 가장 가까운 정수 값으로 반올림한 숫자 값을 반환합니다.  
  
 **구문**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **주의**
  
  반올림 작업을 수행할 0에서 멀어지는 쪽으로 반올림 중간점을 따릅니다. 입력이 두 정수 사이 정확 하 게 하는 숫자 식 결과 0에서 멀어지는 쪽은 가장 가까운 정수 값 수 됩니다.  
  
  |<numeric_expression>|반올림|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
  **예**  
  
  다음 예제에서는 첨부된 양수와 음수를 가장 가까운 정수로 반올림합니다.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  결과 집합은 다음과 같습니다.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 지정한 숫자 식의 양수(+1), 0(0) 또는 음수(-1) 부호를 반환합니다.  
  
 **구문**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 -2에서 2까지의 숫자의 SIGN 값을 반환합니다.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 지정된 식의 라디안에서 지정된 각도의 삼각 사인을 반환합니다.  
  
 **구문**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 각도의 SIN을 계산합니다.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 지정된 숫자 값의 제곱근을 반환합니다.  
  
 **구문**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 숫자 1-3의 제곱근을 반환합니다.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 지정한 숫자 값의 제곱을 반환합니다.  
  
 **구문**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 1에서 3까지의 숫자의 제곱을 반환합니다.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 지정된 식에서 지정된 각도(라디안 단위)의 탄젠트를 반환합니다.  
  
 **구문**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 PI()/2의 탄젠트를 계산합니다.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 가장 가까운 정수 값으로 버린 숫자 값을 반환합니다.  
  
 **구문**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 첨부된 양수와 음수를 가장 가까운 정수 값으로 자릅니다.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>형식 검사 함수

형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 확인할 수 있습니다. 변수 이거나 알 수 있을 때 즉시 항목 내의 속성 형식을 결정할 형식 검사 함수를 사용할 수 있습니다. 지원 되는 기본 제공 형식 검사 함수 표는 다음과 같습니다.

다음 함수는 입력 값에 대한 형식 검사를 지원하며, 각 함수마다 부울 값을 반환합니다.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 지정한 식의 형식이 배열인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_ARRAY 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 지정한 식의 형식이 부울인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_BOOL 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 JSON 문서 내에 속성이 있는지 여부를 확인합니다. "a"가 있으므로 첫 번째 함수는 true를 반환하지만, "b"가 없으므로 두 번째 함수는 false를 반환합니다.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 지정한 식의 형식이 널인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_NULL(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_NULL 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 지정한 식의 형식이 숫자인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_NULL 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_OBJECT 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 null)인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_PRIMITIVE 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_STRING(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_STRING 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>문자열 함수

다음 스칼라 함수는 문자열 입력된 값에 대 한 작업을 수행 하 고, 문자열, 숫자 또는 부울 값을 반환 합니다.
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[왼쪽](#bk_left)|[LENGTH](#bk_length)|  
|[낮은](#bk_lower)|[LTRIM](#bk_ltrim)|[REPLACE](#bk_replace)|  
|[복제](#bk_replicate)|[역방향](#bk_reverse)|[RIGHT](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[SUBSTRING](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[위](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환합니다.  
  
 **구문**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 값의 연결된 문자열을 반환합니다.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 첫 번째 문자열 식이 두 번째를 포함하는지를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc"에 "ab"와 "d"가 포함되어 있는지 확인합니다.  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc"가 "b"와 "bc"로 끝나는지 여부를 나타내는 부울 값을 반환합니다.  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 지정된 첫 번째 문자열 식 내의 두 번째 문자열 식에서 첫 번째로 나타나는 시작 위치를 반환하거나 문자열을 찾을 수 없는 경우 -1을 반환합니다.  
  
 **구문**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc" 안에 다양한 부분 문자열의 인덱스를 반환합니다.  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 지정된 수의 문자로 문자열의 왼쪽 부분을 반환합니다.  
  
 **구문**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   유효한 숫자 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 왼쪽 부분을 반환합니다.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 지정한 문자열 식의 문자 수를 반환합니다.  
  
 **구문**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 문자열의 길이를 반환합니다.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
LOWER(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 LOWER를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 선행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 LTRIM을 쿼리 내에서 사용하는 방법을 보여 줍니다.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 지정된 문자열 값의 모든 항목을 다른 문자열 값으로 바꿉니다.  
  
 **구문**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 REPLACE를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 문자열 값을 지정한 횟수 만큼을 반복합니다.  
  
 **구문**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   유효한 숫자 식입니다. num_expr이 음수이거나 무한 값이면 결과가 정의되지 않습니다.

  > [!NOTE]
  > 결과의 최대 길이는 10,000자입니다((length(str_expr) * num_expr) <= 10,000).
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 REPLICATE를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 문자열 값의 순서와 반대로 반환합니다.  
  
 **구문**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 REVERSE를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 지정된 수의 문자로 문자열의 오른쪽 부분을 반환합니다.  
  
 **구문**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   유효한 숫자 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 오른쪽 부분을 반환합니다.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리 내에서 RTRIM을 사용하는 방법을 보여 줍니다.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc" 문자열이 "b"와 "a"로 시작하는지 확인합니다.  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 배열로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToArray(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   JSON 배열 식으로 계산할 유효한 스칼라 식이입니다. Note는 중첩 된 문자열 값 유효 하려면 큰따옴표를 사용 하 여 작성 해야 합니다. JSON 형식에 대 한 자세한 내용은 참조 하세요. [json.org](https://json.org/)
  
  **반환 형식**  
  
  배열 식을 반환 하거나 정의 되지 않았습니다.  
  
  **예**  
  
  다음 예제에서는 StringToArray 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 
  
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

결과 집합은 다음과 같습니다.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

다음은 잘못 된 입력의 예입니다. 
   
 배열 내에서 작은따옴표는 유효한 JSON이 없습니다.
쿼리 내에서 유효한 지, 경우에 이러한 구문 분석 하지 않습니다 유효한 배열입니다. 문자열 배열의 문자열 내의 이스케이프 되어야 하거나 "[\\"\\"]" 주변 따옴표는 하나 여야 합니다 또는 ' [""]'.

```
SELECT
    StringToArray("['5','6','7']")
```

결과 집합은 다음과 같습니다.

```
[{}]
```

잘못 된 입력의 예는 다음과 같습니다.
   
 JSON 배열로 전달 되는 식 구문 분석 됩니다. 배열 형식과 따라서 undefined를 반환 하려면 다음 평가 하지 않습니다.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

결과 집합은 다음과 같습니다.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 부울으로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   부울 식으로 계산할 유효한 스칼라 식이입니다.  
  
  **반환 형식**  
  
  부울 식을 반환 하거나 정의 되지 않았습니다.  
  
  **예**  
  
  다음 예제에서는 StringToBoolean 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 
 
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

이전 또는 이후에 "true"/ "false"만 공백이 허용 됩니다.

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

잘못 된 입력의 예는 다음과 같습니다.

 부울 대/소문자 구분 되며 모든 소문자 예: "true" 및 "false"를 사용 하 여 작성 해야 합니다.

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

결과 집합은 다음과 같습니다.  
  
```  
[{}]
``` 

전달 되는 식, 부울 식으로 구문 분석 됩니다. 부울 값을 입력 하 고 따라서 undefined를 반환 하는 이러한 입력 평가 하지 않습니다.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

결과 집합은 다음과 같습니다.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Null로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToNull(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   Null 식으로 계산할 유효한 스칼라 식이입니다.
  
  **반환 형식**  
  
  정의 되지 않은 또는 null 식을 반환 합니다.  
  
  **예**  
  
  다음 예제에서는 StringToNull 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 

유효한 입력을 사용 하는 예제는 다음과 같습니다.

 이전 또는 "null" 이후에는 공백이 허용 됩니다.

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

잘못 된 입력의 예는 다음과 같습니다.

Null은 대/소문자 구분 및 "null" 즉, 모든 소문자 문자를 사용 하 여 작성 해야 합니다.

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{}]
```  

전달 되는 식, null 식으로 구문 분석 됩니다. null을 입력 하 고 따라서 undefined를 반환 하는 이러한 입력 평가 하지 않습니다.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 숫자로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToNumber(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   JSON 숫자 식으로 계산할 유효한 스칼라 식이입니다. Json에서 숫자는 정수 또는 부동 소수점 있어야 합니다. JSON 형식에 대 한 자세한 내용은 참조 하세요. [json.org](https://json.org/)  
  
  **반환 형식**  
  
  정의 되지 않은 또는 숫자 식을 반환 합니다.  
  
  **예**  
  
  다음 예제에서는 StringToNumber 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 

만 전이나 후에 공백이 허용 됩니다.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

올바른 숫자를 이어야 합니다 하는 JSON 될 정수 또는 부동 소수점 숫자입니다.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
{{}}
```  

전달 되는 식은 후행 숫자 식입니다. 이러한 입력 번호를 입력 하 고 따라서 undefined를 반환 하는 평가 하지 않습니다. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 개체로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToObject(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   JSON 개체 식으로 계산할 유효한 스칼라 식이입니다. Note는 중첩 된 문자열 값 유효 하려면 큰따옴표를 사용 하 여 작성 해야 합니다. JSON 형식에 대 한 자세한 내용은 참조 하세요. [json.org](https://json.org/)  
  
  **반환 형식**  
  
  개체 식을 반환 하거나 정의 되지 않았습니다.  
  
  **예**  
  
  다음 예제에서는 StringToObject 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 
  
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

결과 집합은 다음과 같습니다.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 잘못 된 입력의 예는 다음과 같습니다.
쿼리 내에서 유효한 지, 경우에 이러한 구문 분석 하지 않습니다 유효한 개체입니다. 문자열 개체의 문자열 내의 이스케이프 되어야 하거나 "{\\"는\\":\\" str\\"}" 주변 따옴표는 하나 여야 합니다 또는 ' {"a": "str"}'.

속성 이름을 둘러싸는 작은따옴표는 유효한 JSON이 없습니다.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```  
[{}]
```  

주변 따옴표 없이 속성 이름은 유효한 JSON 하지 않습니다.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

결과 집합은 다음과 같습니다.

```  
[{}]
``` 

잘못 된 입력의 예는 다음과 같습니다.

 JSON 개체로 전달 되는 식 구문 분석 됩니다. 개체를 입력 하 고 따라서 undefined를 반환 하는 이러한 입력 평가 하지 않습니다.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 결과 집합은 다음과 같습니다.

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 지정한 문자 0 기준 위치에서 시작하여 지정한 길이 또는 문자열의 끝까지에 이르는 문자열 식의 일부를 반환합니다.  
  
 **구문**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   시작 및 끝 문자를 나타내는 유효한 숫자 식입니다.    
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 1에서 시작하여 길이가 1인 "abc"의 부분 문자열을 반환합니다.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 스칼라 식의 문자열 표현을 반환합니다. 
  
 **구문**  
  
```  
ToString(<expr>)
```  
  
 **인수**  
  
- `expr`  
  
   유효한 스칼라 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 다른 형식에서 ToString이 동작하는 방법을 보여줍니다.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 다음 입력을 지정합니다.
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 다음 예제에서는 CONCAT와 같은 다른 문자열 함수를 사용하여 ToString을 사용할 수 있는 방법을 보여줍니다.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

결과 집합은 다음과 같습니다.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
다음 입력을 지정합니다.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
다음 예제에서는 REPLACE와 같은 다른 문자열 함수를 사용하여 ToString을 사용할 수 있는 방법을 보여줍니다.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
결과 집합은 다음과 같습니다.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 선행 및 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
TRIM(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리 내에서 TRIM을 사용하는 방법을 보여 줍니다.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 소문자 데이터를 대문자로 변환한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
UPPER(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 UPPER를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>배열 함수

다음 스칼라 함수는 배열 입력된 값 및 반환 숫자, 부울 또는 배열 값에 대 한 작업을 수행합니다.
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 둘 이상의 배열 값을 연결한 결과인 배열을 반환합니다.  
  
 **구문**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
  **반환 형식**  
  
  배열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 두 배열을 연결하는 방법을 보여 줍니다.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
지정된 값이 배열에 포함되는지를 나타내는 부울 값을 반환합니다. 명령 내에서 부울 식을 사용하여 개체의 부분 또는 전체 일치를 확인할 수 있습니다. 

**구문**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
- `expr`  
  
   유효한 식입니다.  

- `bool_expr`  
  
   부울 식입니다. 'true'로 설정되고 지정된 검색 값이 개체인 경우 명령은 부분 일치(검색 개체가 개체 중 하나의 하위 집합임)를 확인합니다. 'false'로 설정되면 명령은 배열 내의 모든 개체에 대해 전체 일치를 확인합니다. 지정하지 않으면 기본값 false입니다. 
  
  **반환 형식**  
  
  부울 값을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ARRAY_CONTAINS를 사용하여 배열의 멤버 자격을 확인하는 방법을 보여 줍니다.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"b1": true, "b2": false}]  
```  

다음 예제에서는 ARRAY_CONTAINS를 사용하여 JSON의 부분 일치를 확인하는 방법을 보여 줍니다.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 지정된 배열 식의 요소 수를 반환합니다.  
  
 **구문**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ARRAY_LENGTH를 사용하여 배열의 길이를 가져오는 방법을 보여 줍니다.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 배열 식의 일부를 반환합니다.
  
 **구문**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
- `num_expr`  
  
   배열을 시작할 0 기반 숫자 인덱스 배열의 마지막 요소에 관련된 시작 인덱스를 지정하는 데 음수 값을 사용할 수 있습니다. 즉, -1은 배열의 마지막 요소를 참조합니다.  

- `num_expr`  

   결과 배열에 있는 요소의 최대 수입니다.    

  **반환 형식**  
  
  배열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ARRAY_SLICE를 사용하여 배열의 다른 조각을 가져오는 방법을 보여줍니다.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>날짜 및 시간 함수

다음 스칼라 함수를 사용 하면 두 가지 형태로; 현재 UTC 날짜 및 시간을 가져오려면 Unix epoch 시간 (밀리초) 또는 ISO 8601 형식을 준수 하는 문자열 값인 숫자 타임 스탬프입니다. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 현재 UTC 날짜 및 시간을 ISO 8601 문자열을 반환합니다.
  
 **구문**
  
```
GETCURRENTDATETIME ()
```
  
  **반환 형식**
  
  현재 UTC 날짜 및 시간 ISO 8601 문자열 값을 반환 합니다. 

  YYYY-MM-DDThh:mm:ss.sssZ 형태로 표현 됩니다 여기서:
  
  |||
  |-|-|
  |YYYY|4 자리 연도|
  |MM|두 자리 월 (01 년 1 월, = 등.)|
  |DD|월 (01-31)의 2 자리 숫자 일|
  |T|시간 요소의 시작 부분에 대 한 signifier|
  |hh|두 자리 시간 (00-23)|
  |MM|이 분 (00-59)|
  |ss|두 자리 초 (00-59)|
  |.sss|3 초의 소수 자릿수|
  |Z|UTC (협정 세계시) 지정자||
  
  ISO 8601 형식에 대 한 자세한 내용은 참조 하세요. [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **주의**

  GETCURRENTDATETIME 비결 정적 함수입니다. 
  
  반환 된 결과 UTC (협정 세계시)입니다.

  **예**  
  
  다음 예제에는 GetCurrentDateTime 기본 제공 함수를 사용 하 여 현재 UTC 날짜 시간을 가져오는 방법을 보여 줍니다.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 결과 집합 예제는 다음과 같습니다.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 00시: 00 1970 년 1 월 1 일 목요일 이후 경과 된 시간을 밀리초 단위로 반환 합니다. 
  
 **구문**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **반환 형식**  
  
  숫자 값, 경과 된 Unix epoch 이후의 예: 00시: 00 1970 년 1 월 1 일 목요일 이후 경과 된 시간을 밀리초 단위로 시간 (밀리초)의 현재 수를 반환 합니다.

  **주의**

  GETCURRENTTIMESTAMP 비결 정적 함수입니다.
  
  반환 된 결과 UTC (협정 세계시)입니다.

  **예**  
  
  다음 예제에는 GetCurrentTimestamp 기본 제공 함수를 사용 하 여 현재 타임 스탬프를 가져오는 방법을 보여 줍니다.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 결과 집합 예제는 다음과 같습니다.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>공간 함수

Cosmos DB는 지리 공간 쿼리를 위해 다음과 같은 OGC(Open Geospatial Consortium) 기본 제공 함수를 지원합니다. 다음 스칼라 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 두 GeoJSON Point, Polygon 또는 LineString 식 사이의 거리를 반환합니다.  
  
 **구문**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
  **반환 형식**  
  
  거리가 포함된 숫자 식을 반환합니다. 기본 참조 시스템의 경우 미터 단위로 표현됩니다.  
  
  **예**  
  
  다음 예제에서는 ST_DISTANCE 기본 제공 함수를 사용하여 지정된 위치에서 30Km 이내에 있는 모든 패밀리 문서를 반환하는 방법을 보여 줍니다. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 첫 번째 인수에 지정된 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 인수의 GeoJSON(Point, Polygon 또는 LineString) 내에 있는지 여부를 나타내는 부울 식을 반환합니다.  
  
 **구문**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
 
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
  **반환 형식**  
  
  부울 값을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ST_WITHIN을 사용하여 다각형 내의 모든 패밀리 문서를 찾는 방법을 보여 줍니다.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 첫 번째 인수에 지정된 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 인수의 GeoJSON(Point, Polygon 또는 LineString)과 교차하는지 여부를 나타내는 부울 식을 반환합니다.  
  
 **구문**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
 
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
  **반환 형식**  
  
  부울 값을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 다각형과 교차하는 모든 영역을 찾는 방법을 보여줍니다.  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **인수**
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ST_VALID를 사용하여 꼭짓점이 유효한지 확인하는 방법을 보여 줍니다.  
  
  예를 들어 유효 범위[-90, 90]에 없는 위도 값이 이 꼭짓점에 있으므로 쿼리에서 false를 반환합니다.  
  
  다각형의 경우 GeoJSON 사양에서는 닫힌 도형을 만들기 위해 제공된 마지막 좌표 쌍이 첫 번째 것과 같아야 합니다. 다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.  
  
 **구문**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON 꼭짓점 또는 다각형 식입니다.  
  
  **반환 형식**  
  
  지정된 GeoJSON 점 또는 다각형 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ST_ISVALIDDETAILED를 사용하여 세부 정보로 유효성을 검사하는 방법을 보여 줍니다.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [UDFs](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)