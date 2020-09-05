---
title: Azure AD Connect 클라우드 프로비저닝 식 및 함수 참조
description: reference
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c14f406e5671e1eefb43f0208044f9945e446267
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226576"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory에서 특성 매핑 식 작성
클라우드 프로비저닝을 구성하는 경우 식 매핑은 지정할 수 있는 특성 매핑 유형 중 하나입니다. 

식 매핑을 사용하면 스크립트와 유사한 식을 사용하여 특성을 사용자 지정할 수 있습니다.  이렇게 하면 온-프레미스 데이터를 새 값 또는 다른 값으로 변환할 수 있습니다.  예를 들어 단일 특성은 클라우드 애플리케이션 중 하나에서 사용하므로 두 특성을 이 단일 특성으로 결합하는 것이 좋습니다.

다음 문서에서는 데이터를 변환하는 데 사용되는 스크립트와 비슷한 식을 다룹니다.  이는 프로세스의 일부일 뿐입니다.  다음으로, 이 식을 사용하여 테넌트에 대한 웹 요청에 배치해야 합니다.  자세한 내용은 [변환](how-to-transformation.md)을 참조하세요.

## <a name="syntax-overview"></a>구문 개요
특성 매핑을 위한 식의 구문은 VBA(Visual Basic Applications) 함수를 연상시킵니다.

* 전체 식은 <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 서로 함수를 중첩할 수 있습니다. 다음은 그 예입니다. <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 함수에 3가지 다른 유형의 인수를 전달할 수 있습니다.
  
  1. 특성은 대괄호로 묶어야 합니다. 예: [attributeName]
  2. 문자열 상수는 큰따옴표로 묶어야 합니다. 다음은 그 예입니다.  "미국"
  3. 기타 함수 다음은 그 예입니다.  FunctionOne(`<<argument1>>`, FunctionTwo(`<<argument2>>`))
* 문자열 상수의 경우, 백슬래시 (\) 또는 따옴표(")가 문자열에 필요한 경우 백슬래시(\) 기호로 이스케이프되어야 합니다. 다음은 그 예입니다.  "회사 이름: \\"Contoso\\""

## <a name="list-of-functions"></a>함수 목록
| 함수 목록 | Description |
|-----|----|
|[Append](#append)|원본 문자열 값을 문자열의 끝에 접미사로 추가합니다.|
|[BitAnd](#bitand)|BitAnd 함수는 값에 지정된 비트를 설정합니다.|
|[CBool](#cbool)|CBool 함수는 계산된 식에 따라 부울을 반환합니다.|
|[ConvertFromBase64](#convertfrombase64)|ConvertFromBase64 함수는 지정된 base64 인코딩 값을 일반 문자열로 변환합니다.|
|[ConvertToBase64](#converttobase64)|ConvertToBase64 함수는 문자열을 유니코드 base64 문자열로 변환합니다. |
|[ConvertToUTF8Hex](#converttoutf8hex)|ConvertToUTF8Hex 함수는 문자열을 UTF8 16진수 인코딩 값으로 변환합니다.|
|[Count](#count)|Count 함수는 다중값 특성의 요소 수를 반환합니다.|
|[Cstr](#cstr)|CStr 함수는 문자열 데이터 형식으로 변환합니다.|
|[DateFromNum](#datefromnum)|DateFromNum 함수는 AD의 날짜 값 형식을 날짜/시간 형식으로 변환합니다.|
|[DNComponent](#dncomponent)|DNComponent 함수는 왼쪽부터 지정된 DN 구성 요소의 값을 반환합니다.|
|[오류](#error)|Error 함수는 사용자 지정 오류를 반환하는 데 사용됩니다.|
|[FormatDateTime](#formatdatetime) |한 형식의 날짜 문자열을 다른 형식으로 변환합니다.| 
|[GUID](#guid)|Guid 함수는 임의의 새 GUID를 생성합니다.|           
|[IIF](#iif)|IIF 함수는 지정된 조건에 따라 가능한 값 집합 중 하나를 반환합니다.|
|[InStr](#instr)|InStr 함수는 문자열에서 첫 번째 부분 문자열을 찾습니다.|
|[IsNull](#isnull)|식이 Null로 계산되면 IsNull 함수는 true를 반환합니다.|
|[IsNullOrEmpty](#isnullorempty)|식이 null 또는 빈 문자열일 경우 IsNullOrEmpty 함수는 true를 반환합니다.|         
|[IsPresent](#ispresent)|식이 Null이 아니고 비어 있지 않은 문자열로 계산되는 경우 IsPresent 함수는 true를 반환합니다.|    
|[IsString](#isstring)|식이 문자열 형식으로 계산될 수 있는 경우 IsString 함수는 True로 계산됩니다.|
|[항목](#item)|Item 함수는 다중값 문자열/특성에서 하나의 항목을 반환합니다.|
|[Join](#join) |다중 **source** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외하고 Join()은 Append()와 유사하며, 각 값은 **separator** 문자열로 구분됩니다.| 
|[Left](#left)|Left 함수는 문자열 왼쪽부터 지정된 수의 문자를 반환합니다.|
|[Mid](#mid) |원본 값의 부분 문자열을 반환합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.|
|[NormalizeDiacritics](#normalizediacritics)|하나의 문자열 인수가 필요합니다. 문자열을 반환하지만 해당하는 비분음 부호 문자로 대체된 분음 부호 문자를 포함합니다.|
|[Not](#not) |**원본**의 부울 값을 대칭 이동합니다. **원본** 값이 "*True*"인 경우 "*False*"를 반환합니다. 그렇지 않은 경우 "*True*"를 반환합니다.| 
|[RemoveDuplicates](#removeduplicates)|RemoveDuplicates 함수는 다중값 문자열을 사용하여 개별 값을 고유하게 만듭니다.| 
|[바꾸기](#replace) |문자열 내 값을 대체합니다. | 
|[SelectUniqueValue](#selectuniquevalue)|최소 두 개의 인수가 필요하며, 이는 표현식을 사용하여 정의된 고유한 값 생성 규칙입니다. 함수는 각 규칙을 평가한 후 대상 앱/디렉터리에서 생성된 값이 고유한지 확인합니다.| 
|[SingleAppRoleAssignment](#singleapproleassignment)|지정된 애플리케이션에 대해 사용자에게 할당된 모든 appRoleAssignment 목록에서 단일 appRoleAssignment를 반환합니다.| 
|[Split](#split)|지정된 구분 기호 문자를 사용하여 문자열을 다중 값 배열로 분할합니다.|
|[StringFromSID](#stringfromsid)|StringFromSid 함수는 보안 식별자를 포함한 바이트 배열을 문자열로 변환합니다.| 
|[StripSpaces](#stripspaces) |원본 문자열에서 모든 공백(" ")을 제거합니다.| 
|[스위치](#switch)|**원본** 값이 **key**와 일치하면, 해당 **key**의 **value**를 반환합니다. | 
|[ToLower](#tolower)|*원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 소문자로 변환합니다.| 
|[ToUpper](#toupper)|*원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 대문자로 변환합니다.|
|[Trim](#trim)|Trim 함수는 선행 및 후행 공백을 문자열에서 제거합니다.|
|[Word](#word)|Word 함수는 사용할 구분 기호를 설명하는 매개 변수에 따라 문자열 내에 포함된 단어와 반환할 단어 수를 반환합니다.|

---
### <a name="append"></a>추가
**함수:**<br> Append(source, suffix)

**설명:**<br> 원본 문자열 값을 문자열의 끝에 접미사로 추가합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
   | **접미사** |필수 |String |원본 값의 끝에 추가하려는 문자열입니다. |

---
### <a name="bitand"></a>BitAnd
**설명:**  
BitAnd 함수는 값에 지정된 비트를 설정합니다.

**구문:**  
`num BitAnd(num value1, num value2)`

* value1, value2: 숫자 값은 AND와 함께 사용해야 합니다.

**설명:**  
이 함수는 두 매개 변수를 전부 이진 표현으로 변환시키고 비트를 다음과 같이 설정합니다.

* 0 - *value1* 및 *value2*의 해당 비트 중 하나 또는 둘 모두가 0인 경우
* 1 - 2개 모두 해당 비트일 경우 1입니다.

즉, 두 매개 변수의 해당 비트가 1일 경우를 제외하는 모든 경우에는 0을 반환합니다.

**예:**  
 
 `BitAnd(&HF, &HF7)`</br>
 16진법 "F" AND "F7"로 이 값을 계산했기 때문에 7을 반환합니다.

---

### <a name="cbool"></a>CBool
**설명:**  
CBool 함수는 계산된 식에 따라 부울을 반환합니다.

**구문:**  
`bool CBool(exp Expression)`

**설명:**  
식에서 0이 아닌 값으로 계산하는 경우 CBool은 True를 반환하고, 그렇지 않으면 False를 반환합니다.

**예:**  
`CBool([attrib1] = [attrib2])`  

두개의 속성이 같은 동일한 값을 가지면 True로 반환합니다.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**설명:**  
ConvertFromBase64 함수는 지정된 base64 인코딩 값을 일반 문자열로 변환합니다.

**구문:**  
`str ConvertFromBase64(str source)` - 인코딩에 유니코드 가정  
`str ConvertFromBase64(str source, enum Encoding)`

* 원본: Base64 인코딩된 문자열  
* 인코딩: 유니코드, ASCII, UTF8

**예제**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

두 예제 모두 "*Hello world!* "를 반환합니다.

---
### <a name="converttobase64"></a>ConvertToBase64
**설명:**  
ConvertToBase64 함수는 문자열을 유니코드 base64 문자열로 변환합니다.  
정수 배열 값을 base 64 자릿수로 인코딩된 동등한 문자열 표현으로 변환합니다.

**구문:**  
`str ConvertToBase64(str source)`

**예:**  
`ConvertToBase64("Hello world!")`  
"SABlAGwAbABvACAAdwBvAHIAbABkACEA"를 반환합니다.

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**설명:**  
ConvertToUTF8Hex 함수는 문자열을 UTF8 16진수 인코딩 값으로 변환합니다.

**구문:**  
`str ConvertToUTF8Hex(str source)`

**설명:**  
이 함수의 출력 형식은 DN 특성 형식으로 Azure Active Directory에서 사용됩니다.

**예:**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421을 반환합니다.

---
### <a name="count"></a>개수
**설명:**  
Count 함수는 다중값 특성의 요소 수를 반환합니다.

**구문:**  
`num Count(mvstr attribute)`

---
### <a name="cstr"></a>CStr
**설명:**  
CStr 함수는 문자열 데이터 형식으로 변환합니다.

**구문:**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* 값: 숫자 값, 참조 특성 또는 부울입니다.

**예:**  
`CStr([dn])`  
"cn=Joe,dc=contoso,dc=com"을 반환할 수 있습니다.

---
### <a name="datefromnum"></a>DateFromNum
**설명:**  
DateFromNum 함수는 AD의 날짜 값 형식을 날짜/시간 형식으로 변환합니다.

**구문:**  
`dt DateFromNum(num value)`

**예:**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
2012-01-01 23:00:00을 나타내는 날짜/시간을 반환합니다.

---
### <a name="dncomponent"></a>DNComponent
**설명:**  
DNComponent 함수는 왼쪽부터 지정된 DN 구성 요소의 값을 반환합니다.

**구문:**  
`str DNComponent(ref dn, num ComponentNumber)`

* dn: 참조 특성 해석
* ComponentNumber: 반환할 DN의 구성 요소

**예:**  
`DNComponent(CRef([dn]),1)`  
dn이 "cn=Joe,ou=…"인 경우 Joe를 반환합니다.

---
### <a name="error"></a>Error
**설명:**  
Error 함수는 사용자 지정 오류를 반환하는 데 사용됩니다.

**구문:**  
`void Error(str ErrorMessage)`

**예:**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
accountName 특성이 없는 경우 개체에서 오류가 throw됩니다.

---
### <a name="formatdatetime"></a>FormatDateTime
**함수:**<br> FormatDateTime(source, inputFormat, outputFormat)

**설명:**<br> 한 형식의 날짜 문자열을 다른 형식으로 변환합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
   | **inputFormat** |필수 |String |원본 값의 예상된 형식입니다. 지원 되는 형식은 [/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings)를 참조 하세요. |
   | **outputFormat** |필수 |String |출력 날짜의 형식입니다. |

---
### <a name="guid"></a>Guid
**설명:**  
함수 Guid는 임의의 GUID를 새로 생성합니다.

**구문:**  
`str Guid()`

---
### <a name="iif"></a>IIF
**설명:**  
IIF 함수는 지정된 조건에 따라 가능한 값 집합 중 하나를 반환합니다.

**구문:**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* 조건: true 또는 false로 계산될 수 있는 임의의 값 또는 식입니다.
* valueIfTrue: 조건이 true로 평가되는 경우 반환된 값입니다.
* valueIfFalse: 조건이 false로 평가되는 경우 반환된 값입니다.

**예:**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 사용자가 인턴일 경우 사용자 별칭 앞에 “t-”를 추가하여 반환하고, 그 외의 경우에는 본래의 별칭 그대로 반환합니다.

---
### <a name="instr"></a>InStr
**설명:**  
InStr 함수는 문자열에서 부분 문자열이 처음 나오는 경우를 찾습니다.

**구문:**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start , enum compare)`

* stringcheck: 검색할 문자열
* stringmatch: 찾을 문자열
* start: 부분 문자열을 찾을 시작 위치
* compare: vbTextCompare 또는 vbBinaryCompare

**설명:**  
부분 문자열을 찾으면 위치가 반환되고, 찾지 못할 경우 0이 반환됩니다.

**예:**  
`InStr("The quick brown fox","quick")`  
5로 계산합니다.

`InStr("repEated","e",3,vbBinaryCompare)`  
7로 계산합니다.

---
### <a name="isnull"></a>IsNull
**설명:**  
식이 Null로 계산되면 IsNull 함수는 true를 반환합니다.

**구문:**  
`bool IsNull(var Expression)`

**설명:**  
특성이 없는 경우 Null로 표현됩니다.

**예:**  
`IsNull([displayName])`  
CS 또는 MV에 특성이 없을 경우 True를 반환합니다.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**설명:**  
식이 null 또는 빈 문자열일 경우 IsNullOrEmpty 함수는 true를 반환합니다.

**구문:**  
`bool IsNullOrEmpty(var Expression)`

**설명:**  
특성이 없거나, 빈 문자열로 존재하는 경우 True로 계산합니다.  
이 함수의 역원을 IsPresnt라고 합니다.

**예:**  
`IsNullOrEmpty([displayName])`  
특성이 없거나 CS 또는 MV에서 빈 문자열인 경우 True를 반환합니다.

---
### <a name="ispresent"></a>IsPresent
**설명:**  
식이 Null이 아니고 비어 있지 않은 문자열로 계산되는 경우 IsPresent 함수는 true를 반환합니다.

**구문:**  
`bool IsPresent(var expression)`

**설명:**  
이 함수의 역함수는 IsNullOrEmpty으로 지칭됩니다.

**예:**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>항목
**설명:**  
Item 함수는 다중값 문자열/특성에서 하나의 항목을 반환합니다.

**구문:**  
`var Item(mvstr attribute, num index)`

* attribute: 다중값 특성
* 인덱스: 다중값 문자열에 있는 항목에 대한 인덱스입니다.

**설명:**  
Item 함수는 다중값 특성의 항목에 대한 인덱스를 반환하는 Contains 함수와 함께 사용할 수 있습니다.

인덱스가 범위를 초과하는 경우 오류가 나타납니다.

**예:**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
기본 전자 메일 주소를 반환합니다.

---
### <a name="isstring"></a>IsString
**설명:**  
식이 문자열 형식으로 계산될 수 있는 경우 IsString 함수는 True로 계산됩니다.

**구문:**  
`bool IsString(var expression)`

**설명:**  
CStr()이 식을 구문 분석하는 데 성공할 수 있는지 여부를 결정할 때 사용됩니다.

---
### <a name="join"></a>Join
**함수:**<br> Join(separator, source1, source2, …)

**설명:**<br> 다중 **source** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외하고 Join()은 Append()와 유사하며, 각 값은 **separator** 문자열로 구분됩니다.

원본 값 중 하나가 다중 값 특성인 경우, 해당 특성의 모든 값은 함께 조인되며 구분 기호 값에 의해 구분됩니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **separator** |필수 |String |문자열이 하나의 문자열로 연결되면 원본 값을 구분하는데 문자열을 사용합니다. 구분 기호가 필요하지 않은 경우 ""일 수 있습니다. |
   | **source1  … sourceN** |필수, 시간 변수 |String |값이 함께 조인될 문자열입니다. |

---
### <a name="left"></a>Left
**설명:**  
Left 함수는 문자열 왼쪽부터 지정된 수의 문자를 반환합니다.

**구문:**  
`str Left(str string, num NumChars)`

* string: 문자로 반환될 문자열입니다.
* NumChars: 문자열의 시작(왼쪽)에서 반환될 문자의 개수를 식별하는 번호입니다.

**설명:**  
문자열의 첫 번째 numChars 문자를 포함하는 문자열입니다.

* numChars = 0 인 경우, 빈 문자열을 반환합니다.
* numCahrs < 0,인 경우, 입력된 문자열을 반환합니다.
* 문자열이 null이면 빈 문자열을 반환합니다.

문자열이 numChars 내에서 숫자가 지정한 문자보다 적은 문자를 포함하는 경우, 문자열과 동일한 문자열(즉, 매개 변수 1의 모든 문자가 포함)이 반환됩니다.

**예:**  
`Left("John Doe", 3)`  
`Joh`를 반환합니다.

---
### <a name="mid"></a>Mid
**함수:**<br> Mid(source, start, length)

**설명:**<br> 원본 값의 부분 문자열을 반환합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |일반적으로 특성 이름입니다. |
   | **start** |필수 |integer |부분 문자열이 시작될 **원본** 문자열의 인덱스입니다. 문자열의 첫번째 문자에는 인덱스 1이 있고, 두번째 문자에는 인덱스 2가 있습니다. |
   | **length** |필수 |integer |부분 문자열의 길이입니다. 길이가 **원본** 문자열 외부에서 종료되면 함수는 **시작** 인덱스부터 **원본** 문자열 끝까지의 부분 문자열을 반환합니다. |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**함수:**<br> NormalizeDiacritics(원본)

**설명:**<br> 하나의 문자열 인수가 필요합니다. 문자열을 반환하지만 해당하는 비분음 부호 문자로 대체된 분음 부호 문자를 포함합니다. 일반적으로 분음 부호 문자(악센트 부호)를 포함하는 이름 및 성을 다양한 사용자 ID(예: 사용자 계정 이름, SAM 계정 이름 및 이메일 주소)에 사용할 수 있는 올바른 값으로 변환하는 데 사용됩니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String | 일반적으로 이름 또는 성 특성입니다. |

---
### <a name="not"></a>Not
**함수:**<br> Not(source)

**설명:**<br> **원본**의 부울 값을 대칭 이동합니다. **원본** 값이 "*True*"인 경우 "*False*"를 반환합니다. 그렇지 않은 경우 "*True*"를 반환합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |부울 문자열 |예상 **원본** 값은 "True" 또는 "False"입니다. |

---
### <a name="removeduplicates"></a>RemoveDuplicates
**설명:**  
RemoveDuplicates 함수는 다중값 문자열을 사용하여 개별 값을 고유하게 만듭니다.

**구문:**  
`mvstr RemoveDuplicates(mvstr attribute)`

**예:**  
`RemoveDuplicates([proxyAddresses])`  
모든 중복 값을 제거한 삭제된 proxyAddress 특성을 반환합니다.

---
### <a name="replace"></a>Replace
**함수:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**설명:**<br>
문자열 내 값을 대체합니다. 제공된 매개 변수에 따라 다르게 작동합니다.

* **oldValue** 및 **replacementValue**가 제공되는 경우:
  
  * **source**에서 발생하는 모든 **oldValue**를 **replacementValue**로 바꿉니다.
* **oldValue** 및 **template**이 제공되는 경우:
  
  * **template**에서 **oldValue**의 모든 항목을 **원본** 값으로 바꿉니다.
* **regexPattern** 및 **replacementValue**가 제공되는 경우:

  * 함수에서 **regexPattern**을 **source** 문자열에 적용하고, regex 그룹 이름을 사용하여 **replacementValue**에 대한 문자열을 생성할 수 있습니다.
* **regexPattern**, **regexGroupName**, **replacementValue**가 제공되는 경우:
  
  * 함수에서 **regexPattern**을 **source** 문자열에 적용하고, **regexGroupName**과 일치하는 모든 값을 **replacementValue**로 바꿉니다.
* **regexPattern**, **regexGroupName**, **replacementAttributeName**이 제공되는 경우:
  
  * **source**에 값이 없는 경우 **source**가 반환됩니다.
  * **source**에 값이 있는 경우 함수에서 **regexPattern**을 **source** 문자열에 적용하고, **regexGroupName**과 일치하는 모든 값을 **replacementAttributeName**과 연결된 값으로 바꿉니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |일반적으로 **source** 개체의 특성 이름입니다. |
   | **oldValue** |옵션 |String |**원본** 또는 **템플릿**에서 대체될 값입니다. |
   | **regexPattern** |옵션 |String |**원본**에서 대체될 값에 대한 Regex 패턴입니다. 또는 **replacementPropertyName**을 사용하는 경우 **replacementPropertyName**에서 값을 추출하는 패턴입니다. |
   | **regexGroupName** |옵션 |String |**regexPattern**내 그룹의 이름입니다. **replacementPropertyName**을 사용하는 경우에만 **replacementPropertyName**에서 이 그룹의 값을 **replacementValue**로 추출합니다. |
   | **replacementValue** |옵션 |String |이전 값과 대체할 새로운 값입니다. |
   | **replacementAttributeName** |옵션 |String |대체 값에 사용할 특성의 이름입니다. |
   | **template** |옵션 |String |**template** 값이 제공되는 경우 템플릿 내에서 **oldValue**를 찾아서 **source** 값으로 바꿉니다. |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**함수:**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**설명:**<br> 최소 두 개의 인수가 필요하며, 이는 표현식을 사용하여 정의된 고유한 값 생성 규칙입니다. 함수는 각 규칙을 평가한 후 대상 앱/디렉터리에서 생성된 값이 고유한지 확인합니다. 발견된 첫 번째 고유한 값이 반환됩니다. 모든 값이 이미 대상에 있는 경우 항목은 위탁되고 감사 로그에 이유가 기록됩니다. 제공할 수 있는 인수 수에 상한은 없습니다.

> [!NOTE]
> - 최상위 레벨 함수이므로 중첩할 수 없습니다.
> - 우선 순위가 일치하는 특성에는 이 함수를 적용할 수 없습니다.  
> - 이 함수는 항목 만들기에만 사용할 수 있습니다. 특성과 함께 사용할 경우 **매핑 적용** 속성을 **개체를 만드는 동안만**으로 설정합니다.
> - 이 함수는 현재 "Workday에서 Active Directory로의 사용자 프로비저닝"에만 지원되며, 다른 프로비저닝 애플리케이션에서는 사용할 수 없습니다. 


**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **uniqueValueRule1  … uniqueValueRuleN** |2개 이상 필요, 상한 없음 |String | 평가할 고유한 값 생성 규칙 목록입니다. |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**함수:**<br> SingleAppRoleAssignment([appRoleAssignments])

**설명:**<br> 지정된 애플리케이션에 대해 사용자에게 할당된 모든 appRoleAssignment 목록에서 단일 appRoleAssignment를 반환합니다. 이 함수는 appRoleAssignments 개체를 단일 역할 이름 문자열로 변환하는 데 필요합니다. 모범 사례는 한 번에 하나의 appRoleAssignment만 하나의 사용자에게 할당되도록 하는 것이고 여러 역할이 할당된 경우에는 반환된 역할 문자열을 예측할 수 없습니다. 

**매개 변수:**<br> 

  | 속성 | 필수/ 반복 | Type | 메모 |
  |--- | --- | --- | --- |
  | **[appRoleAssignments]** |필수 |String |**[appRoleAssignments]** 개체. |

---
### <a name="split"></a>분할
**함수:**<br> Split(원본, 구분 기호)

**설명:**<br> 지정된 구분 기호 문자를 사용하여 문자열을 다중 값 배열로 분할합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |**원본** 값입니다. |
   | **delimiter** |필수 |String |문자열을 분할하는 데 사용할 문자(예: ",")를 지정 |

---
### <a name="stringfromsid"></a>StringFromSid
**설명:**  
StringFromSid 함수는 보안 식별자를 포함한 바이트 배열을 문자열로 변환합니다.

**구문:**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="stripspaces"></a>StripSpaces
**함수:**<br> StripSpaces(source)

**설명:**<br> 원본 문자열에서 모든 공백(" ")을 제거합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |**원본** 값입니다. |

---
### <a name="switch"></a>스위치
**함수:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**설명:**<br> **원본** 값이 **key**와 일치하면, 해당 **key**의 **value**를 반환합니다. **원본** 값과 일치하는 키가 없으면 **defaultValue**를 반환합니다.  **Key** 및 **value** 매개 변수는 항상 쌍으로 제공되어야 합니다. 함수는 항상 짝수 개수의 매개 변수를 예상합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |확인할 **원본** 값입니다. |
   | **defaultValue** |옵션 |String |원본이 모든 키와 일치하지 않는 경우 사용할 기본값입니다. 빈 문자열("")일 수 있습니다. |
   | **key** |필수 |String |**원본** 값과 비교할 **Key**입니다. |
   | **value** |필수 |String |키와 일치하는 **원본** 의 대체 값입니다. |

---
### <a name="tolower"></a>ToLower
**함수:**<br> ToLower(source, culture)

**설명:**<br> *원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 소문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**매개 변수:**<br> 

   | 속성 | 필수/ 반복 | Type | 메모 |
   | --- | --- | --- | --- |
   | **원본(source)** |필수 |String |대개는 원본 개체의 특성 이름 |
   | **문화권** |옵션 |String |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2*이며, 여기서 *languagecode2*는 2자 언어 코드이고 *country/regioncode2*는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

---

### <a name="toupper"></a>ToUpper
**함수:**<br> ToUpper(source, culture)

**설명:**<br> *원본* 문자열 값을 가져와서 지정된 문화권 규칙을 사용하여 대문자로 변환합니다. 지정된 *문화권* 정보가 없는 경우 고정 문화권을 사용합니다.

**매개 변수:**<br> 

  | 속성 | 필수/ 반복 | Type | 메모 |
  | --- | --- | --- | --- |
  | **원본(source)** |필수 |String |대개는 원본 개체의 특성 이름입니다. |
  | **문화권** |옵션 |String |RFC 4646 기반의 문화권 이름 형식은 *languagecode2-country/regioncode2*이며, 여기서 *languagecode2*는 2자 언어 코드이고 *country/regioncode2*는 2자 하위 문화권 코드입니다. 일본어(일본)의 ja-JP와 영어(미국)의 en-US를 예로 들 수 있습니다. 2자 언어 코드를 사용할 수 없는 경우 ISO 639-2에서 파생된 3자 코드가 사용됩니다.|

---

### <a name="trim"></a>Trim
**설명:**  
Trim 함수는 선행 및 후행 공백을 문자열에서 제거합니다.

**구문:**  
`str Trim(str value)`  

**예:**  
`Trim(" Test ")`  
"test"를 반환합니다.

`Trim([proxyAddresses])`  
proxyAddress 특성의 각 값에 대한 선행 및 후행 공백을 제거합니다.

---
### <a name="word"></a>Word
**설명:**  
Word 함수는 사용할 구분 기호를 설명하는 매개 변수에 따라 문자열 내에 포함된 단어와 반환할 단어 수를 반환합니다.

**구문:**  
`str Word(str string, num WordNumber, str delimiters)`

* string: 단어를 반환할 문자열입니다.
* WordNumber: 반환해야 하는 단어 수를 식별하는 번호입니다.
* delimiters : 단어를 식별하는데 사용될 구분 기호를 나타내는 문자열입니다.

**설명:**  
구분 기호 내의 문자 중 하나로 구분되는 전체 문자열의 각 문자열은 단어로 식별됩니다.

* 숫자가 < 1인경우 , 빈 문자열을 반환합니다.
* 문자열이 null이면, 빈 문자열을 반환합니다.

문자열이 단어 수보다 적거나, 구분 기호로 식별되는 단어를 포함할 경우, 빈 문자열이 반환됩니다.

**예:**  
`Word("The quick brown fox",3," ")`  
"brown"을 반환합니다.

`Word("This,string!has&many separators",3,",!&#")`  
"has"를 반환합니다.

## <a name="examples"></a>예
### <a name="strip-known-domain-name"></a>알려진 도메인 이름 제거
사용자 이름을 가져오려면 사용자의 전자 메일에서 알려진 도메인 이름을 제거해야 합니다. <br>
예를 들어, 도메인이 "contoso.com"인 경우 다음 식을 사용할 수 있습니다.

**식:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**샘플 입출력:** <br>

* **입력**(메일): “john.doe@contoso.com”
* **출력**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>사용자 이름에 상수 접미사 추가
Salesforce 샌드박스를 사용하는 경우 동기화하기 전에 모든 사용자 이름에 추가 접미사를 추가해야할 수 있습니다.

**식:** <br>
`Append([userPrincipalName], ".test")`

**샘플 입/출력:** <br>

* **입력**: (userPrincipalName): “John.Doe@contoso.com”
* **출력**:  “John.Doe@contoso.com.test”

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>이름과 성의 부분을 연결하여 사용자 별칭을 생성합니다.
사용자의 이름 중 처음 3개 문자 및 사용자 성의 처음 5개 문자를 사용하여 사용자 별칭을 생성해야 합니다.

**식:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**샘플 입/출력:** <br>

* **입력**(givenName): "John"
* **입력**(surname): "Doe"
* **출력**:  "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>문자열에서 분음 부호 제거
악센트 기호를 포함하지 않는 해당 문자로 악센트 기호를 포함하는 문자를 바꿔야 합니다.

**식:** <br>
NormalizeDiacritics([givenName])

**샘플 입/출력:** <br>

* **입력**(givenName): "Zoë"
* **출력**:  "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>문자열을 다중 값 배열로 분할
문자열의 쉼표로 구분된 목록을 가져와 Salesforce의 PermissionSets 특성과 같은 다중 값 특성에 연결할 수 있는 배열로 분할해야 합니다. 이 예의 경우 Azure AD에서 사용 권한 세트의 목록을 extensionAttribute5에 채웠습니다.

**식:** <br>
Split([extensionAttribute5], ",")

**샘플 입/출력:** <br>

* **INPUT** (extensionAttribute5): "PermissionSetOne, PermisionSetTwo"
* **OUTPUT**:  ["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>특정 형식에서 문자열로 출력 날짜
SaaS 애플리케이션에 특정 형식의 날짜를 전송하려고 합니다. <br>
예를 들어 ServiceNow에 대한 날짜 형식을 지정하려고 할 수 있습니다.

**식:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**샘플 입/출력:**

* **입력**(extensionAttribute1): "20150123105347.1Z"
* **출력**:  "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>미리 정의된 옵션 집합을 기반으로 값 바꾸기

Azure AD에 저장된 상태 코드를 기반으로 사용자의 시간대를 정의해야 합니다. <br>
상태 코드가 미리 정의된 옵션 중 하나와 일치하지 않으면 기본값인 "오스트레일리아/시드니"를 사용합니다.

**식:** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**샘플 입/출력:**

* **입력**(상태): "QLD"
* **출력**: "오스트레일리아/브리즈번"

### <a name="replace-characters-using-a-regular-expression"></a>정규식을 사용하여 문자를 바꿈
정규식 값과 일치하는 문자를 찾아 제거해야 합니다.

**식:** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**샘플 입/출력:**

* **INPUT** (mailNickname: "john_doe72"
* **출력**: "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>생성된 userPrincipalName(UPN) 값을 소문자로 변환
아래 예에서 UPN 값은 PreferredFirstName 및 PreferredLastName 원본 필드를 연결하여 생성되고, ToLower 함수는 생성된 문자열에서 작동하여 모든 문자를 소문자로 변환합니다. 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**샘플 입/출력:**

* **입력**(PreferredFirstName): "John"
* **입력**(PreferredLastName): "Smith"
* **출력**: "john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>UPN(userPrincipalName) 특성의 고유한 값 생성
사용자의 이름, 중간 이름, 성에 따라, UPN 특성에 값을 할당하려면 먼저 해당 UPN 특성에 대해 값을 생성하고 대상 AD 디렉터리에서 해당 값이 고유한지 확인해야 합니다.

**식:** <br>

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**샘플 입/출력:**

* **입력**(PreferredFirstName): "John"
* **입력**(PreferredLastName): "Smith"
* **출력**: John.Smith@contoso.com의 UPN 값이 디렉터리에 아직 없는 경우 “John.Smith@contoso.com”
* **출력**: John.Smith@contoso.com의 UPN 값이 디렉터리에 이미 있는 경우 “J.Smith@contoso.com”
* **출력**: 위의 두 UPN 값이 디렉터리에 이미 있는 경우 “Jo.Smith@contoso.com”


## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝이란?](what-is-cloud-provisioning.md)