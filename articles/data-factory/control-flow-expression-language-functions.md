---
title: Azure Data Factory 식 및 함수
description: 이 문서에서는 데이터 팩터리 엔터티를 만드는 데 사용할 수 있는 식 및 함수에 대한 정보를 제공 합니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9ef4b569fd8413d2825374c963fb272dd450cf0e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533131"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory의 식과 함수

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-functions-variables.md)
> * [현재 버전](control-flow-expression-language-functions.md)

이 문서에서는 Azure Data Factory에서 지원하는 식과 함수에 대한 정보를 제공합니다. 

## <a name="expressions"></a>식

정의의 JSON 값은 리터럴일 수도 있고 정의가 런타임에 평가되는 식일 수도 있습니다. 다음은 그 예입니다.  
  
```json
"name": "value"
```

 or  
  
```json
"name": "@pipeline().parameters.password"
```

식은 JSON 문자열 값에서 어느 위치에나 나타날 수 있으며 그 결과 항상 다른 JSON 값이 발생합니다. JSON 값이 식이면 at 기호(\@)를 제거하여 식의 본문을 추출합니다. \@으로 시작되는 리터럴 문자열이 필요한 경우 해당 문자열은 \@\@을 사용하여 이스케이프 처리해야 합니다. 다음 예제는 식의 작동 방식을 보여 줍니다.  
  
|JSON 값|결과|  
|----------------|------------|  
|"parameters"|'parameters' 문자가 반환됩니다.|  
|"parameters[1]"|'parameters[1]' 문자가 반환됩니다.|  
|"\@\@"|'\@'를 포함하는 1개 문자열이 반환됩니다.|  
|“\@”|'\@'를 포함하는 2개 문자열이 반환됩니다.|  
  
 *문자열 보간*이라는 기능을 사용하면 식이 `@{ ... }`로 묶인 문자열 내부에 나타날 수도 있습니다. 예: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 문자열 보간을 사용하면 결과는 항상 문자열입니다. `myNumber`를 `42`로 정의하고 `myString`을 `foo`로 정의했다고 가정합니다.  
  
|JSON 값|결과|  
|----------------|------------|  
|“\@pipeline().parameters.myString”| `foo`을 문자열로 반환합니다.|  
|\@“@{pipeline().parameters.myString}”| `foo`을 문자열로 반환합니다.|  
|“\@pipeline().parameters.myNumber”| `42`를 *숫자*로 반환합니다.|  
|“\@{pipeline().parameters.myNumber}”| `42`를 *문자열*로 반환합니다.|  
|"Answer is: @{pipeline().parameters.myNumber}"| `Answer is: 42` 문자열을 반환합니다.|  
|“\@concat(‘Answer is: ’, string(pipeline().parameters.myNumber))”| `Answer is: 42` 문자열을 반환합니다.|  
|"대답은 다음과 같습니다. \@\@{pipeline().parameters.myNumber}"| `Answer is: @{pipeline().parameters.myNumber}` 문자열을 반환합니다.|  
  
## <a name="examples"></a>예시

### <a name="a-dataset-with-a-parameter"></a>매개 변수가 포함된 데이터 세트
다음 예제에서는 BlobDataset은 **경로**라는 매개 변수를 사용합니다. 해당 값은 `dataset().path`식을 사용 하 여 **folderPath** 속성의 값을 설정 하는 데 사용 됩니다. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>매개 변수가 포함된 파이프라인
다음 예에서 파이프라인은 **inputPath** 및 **outputPath** 매개 변수를 사용합니다. 매개 변수가 있는 Blob 데이터 세트의 **경로**는 이러한 매개 변수의 값을 사용하여 설정됩니다. 여기에 사용된 구문은 `pipeline().parameters.parametername`입니다. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>자습서
이 [자습서](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) 에서는 파이프라인 및 활동과 활동 간에 매개 변수를 전달 하는 방법을 안내 합니다.

  
## <a name="functions"></a>Functions

식 내에서 함수를 호출할 수 있습니다. 다음 섹션에서는 식에서 사용할 수 있는 함수에 대한 정보를 제공합니다.  

## <a name="string-functions"></a>문자열 함수  

문자열로 작업하려면 이러한 문자열 함수 및 일부 [컬렉션 함수](#collection-functions)도 사용할 수 있습니다.
문자열 함수는 문자열에서만 작동합니다.

| 문자열 함수 | 작업 |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | 둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | 문자열이 지정된 하위 문자열로 끝나는지 여부를 확인합니다. |
| [guid](control-flow-expression-language-functions.md#guid) | 전역적으로 고유한 식별자(GUID)를 문자열로 생성합니다. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | 하위 문자열에 대한 시작 위치를 반환합니다. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | 마지막으로 나오는 부분 문자열의 시작 위치를 반환합니다. |
| [replace](control-flow-expression-language-functions.md#replace) | 하위 문자열을 지정된 문자열로 바꾸고 업데이트된 문자열을 반환합니다. |
| [분할](control-flow-expression-language-functions.md#split) | 원래 문자열에 지정된 구분 기호 문자를 기준으로 하여 더 큰 문자열의 하위 문자열을 포함하는 배열(쉼표로 구분됨)을 반환합니다. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | 문자열이 특정 하위 문자열로 시작 하는지를 확인합니다. |
| [substring](control-flow-expression-language-functions.md#substring) | 지정된 위치에서 시작하여 문자열에서 문자를 반환합니다. |
| [toLower](control-flow-expression-language-functions.md#toLower) | 문자열을 소문자 형식으로 반환 합니다. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | 문자열을 대문자 형식으로 반환합니다. |
| [trim](control-flow-expression-language-functions.md#trim) | 문자열에서 선행 및 후행 공백을 제거하고 업데이트 된 문자열을 반환합니다. |

## <a name="collection-functions"></a>컬렉션 함수

컬렉션, 일반적으로 배열, 문자열 및 경우에 따라 사전으로 작업하려면 이러한 컬렉션 함수를 사용할 수 있습니다.

| 컬렉션 함수 | 작업 |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | 컬렉션에 특정 항목이 있는지 여부를 확인합니다. |
| [empty](control-flow-expression-language-functions.md#empty) | 컬렉션이 비어 있는지 여부를 확인합니다. |
| [first](control-flow-expression-language-functions.md#first) | 컬렉션에서 첫 번째 항목을 반환합니다. |
| [intersection](control-flow-expression-language-functions.md#intersection) | 지정된 컬렉션에서 공통 항목*만* 있는 컬렉션을 반환합니다. |
| [join](control-flow-expression-language-functions.md#join) | 지정된 문자로 구분되는 배열에서 *모든* 항목이 들어 있는 문자열을 반환합니다. |
| [last](control-flow-expression-language-functions.md#last) | 컬렉션에서 마지막 항목을 반환합니다. |
| [length](control-flow-expression-language-functions.md#length) | 문자열 또는 배열에서 항목 수를 반환합니다. |
| [skip](control-flow-expression-language-functions.md#skip) | 컬렉션 앞에서 항목을 제거하고 *다른 모든* 항목을 반환합니다. |
| [take](control-flow-expression-language-functions.md#take) | 컬렉션 앞에서 항목을 반환합니다. |
| [union](control-flow-expression-language-functions.md#union) | 지정된 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다. | 

## <a name="logical-functions"></a>논리 함수  

이 함수는 조건 내에서 유용하며 논리의 형식을 평가하는 데 사용할 수 있습니다.  
  
| 논리 비교 함수 | 작업 |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | 모든 식이 true인지 확인합니다. |
| [equals](control-flow-expression-language-functions.md#equals) | 두 값이 동일한지 확인합니다. |
| [greater](control-flow-expression-language-functions.md#greater) | 첫 번째 값이 두 번째 값보다 큰지 확인합니다. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | 첫 번째 값이 두 번째 값보다 크거나 같은지 여부를 확인합니다. |
| [if](control-flow-expression-language-functions.md#if) | 식이 true인지 또는 false인지 확인합니다. 결과에 기반해 지정된 값을 반환합니다. |
| [less](control-flow-expression-language-functions.md#less) | 첫 번째 값이 두 번째 값보다 작은지 여부를 확인합니다. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | 첫 번째 값이 두 번째 값보다 작거나 같은지 여부를 확인합니다. |
| [not](control-flow-expression-language-functions.md#not) | 식이 false인지 확인합니다. |
| [or](control-flow-expression-language-functions.md#or) | 최소 하나의 식이 true인지 확인합니다. |
  
## <a name="conversion-functions"></a>변환 함수  

 이 함수는 언어의 각 기본 형식 간에 변환하는 데 사용됩니다.  
-   문자열
-   정수
-   float
-   부울
-   arrays
-   dictionaries

| 변환 함수 | 작업 |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | 단일 지정 입력에서 배열을 반환합니다. 여러 입력의 경우 [createArray](control-flow-expression-language-functions.md#createArray)를 참조합니다. |
| [base64](control-flow-expression-language-functions.md#base64) | 문자열에 대한 base64로 인코딩된 버전을 반환합니다. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [binary](control-flow-expression-language-functions.md#binary) | 입력 값에 대한 이진 버전을 반환합니다. |
| [bool](control-flow-expression-language-functions.md#bool) | 입력 값에 대한 부울 버전을 반환합니다. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | 하나 이상의 매개 변수에서 null이 아닌 첫 번째 값을 반환합니다. |
| [createArray](control-flow-expression-language-functions.md#createArray) | 여러 입력에서 배열을 반환합니다. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | 입력 값에 대한 데이터 URI를 반환합니다. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | 데이터 URI에 대한 이진 버전을 반환합니다. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | 데이터 URI에 대한 문자열 버전을 반환합니다. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | 데이터 URI에 대한 이진 버전을 반환합니다. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | 디코딩된 버전으로 이스케이프 문자를 대체하는 문자열을 반환합니다. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | URL 안전하지 않은 문자를 이스케이프 문자로 대체하는 문자열을 반환합니다. |
| [float](control-flow-expression-language-functions.md#float) | 입력 값에 대해 부동 소수점 숫자를 반환합니다. |
| [int](control-flow-expression-language-functions.md#int) | 문자열에 대한 정수 버전을 반환합니다. |
| [json](control-flow-expression-language-functions.md#json) | JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다. |
| [string](control-flow-expression-language-functions.md#string) | 입력 값에 대한 문자열 버전을 반환합니다. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | 이스케이프 문자로 URL 안전하지 않은 문자를 대체하여 입력 값에 대한 URI로 인코딩된 버전을 반환합니다. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | URI로 인코딩된 문자열에 대한 이진 버전을 반환합니다. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | URI로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [xml](control-flow-expression-language-functions.md#xml) | 문자열에 대한 XML 버전을 반환합니다. |
| [xpath](control-flow-expression-language-functions.md#xpath) | XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. |

## <a name="math-functions"></a>수학 함수  
 이 함수는 **integers** 및 **floats**의 숫자 형식에 사용할 수 있습니다.  

| 수식 함수 | 작업 |
| ------------- | ---- |
| [추가](control-flow-expression-language-functions.md#add) | 두 숫자를 더한 결과를 반환합니다. |
| [div](control-flow-expression-language-functions.md#div) | 두 숫자를 나눈 결과를 반환합니다. |
| [max](control-flow-expression-language-functions.md#max) | 숫자 또는 배열 집합에서 가장 높은 값을 반환합니다. |
| [min](control-flow-expression-language-functions.md#min) | 숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다. |
| [mod](control-flow-expression-language-functions.md#mod) | 두 숫자를 나눈 나머지를 반환합니다. |
| [mul](control-flow-expression-language-functions.md#mul) | 두 숫자를 곱한 산출물을 반환합니다. |
| [rand](control-flow-expression-language-functions.md#rand) | 지정된 범위에서 임의의 정수를 반환합니다. |
| [range](control-flow-expression-language-functions.md#range) | 지정된 정수에서 시작하는 정수 배열을 반환합니다. |
| [sub](control-flow-expression-language-functions.md#sub) | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다. |
  
## <a name="date-functions"></a>날짜 함수  

| 날짜 또는 시간 함수 | 작업 |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | 타임스탬프에 일 수를 추가합니다. |
| [addHours](control-flow-expression-language-functions.md#addHours) | 타임스탬프에 시간 수를 추가합니다. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | 타임스탬프에 분 수를 추가합니다. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | 타임스탬프에 초 수를 추가합니다. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | 타임스탬프에 시간 단위 수를 추가합니다. [getFutureTime](control-flow-expression-language-functions.md#getFutureTime)도 참조합니다. |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | UTC(Universal Time Coordinated)의 타임 스탬프를 대상 표준 시간대로 변환합니다. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | 원본 표준 시간대의 타임 스탬프를 대상 표준 시간대로 변환합니다. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | 원본 표준 시간대의 타임 스탬프를 UTC(Universal Time Coordinated)로 변환합니다. |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | 타임 스탬프에서 월 구성 요소인 날짜를 반환합니다. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | 타임 스탬프에서 주 구성 요소인 날짜를 반환합니다. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | 타임 스탬프에서 연 구성 요소인 날짜를 반환합니다. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | 타임 스탬프에서 날짜를 반환합니다. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | 지정된 시간 단위를 더한 현재 타임 스탬프를 반환합니다. [addToTime](control-flow-expression-language-functions.md#addToTime)도 참조합니다. |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | 지정된 시간 단위을 뺀 현재 타임 스탬프를 반환합니다. [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime)도 참조합니다. |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | 타임 스탬프에 대한 날의 시작을 반환합니다. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | 타임 스탬프에 대한 시간의 시작을 반환합니다. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | 타임 스탬프에 대한 분의 시작을 반환합니다. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | 타임스탬프에서 시간 단위 수를 뺍니다. [getPastTime](control-flow-expression-language-functions.md#getPastTime)도 참조합니다. |
| [ticks](control-flow-expression-language-functions.md#ticks) | 지정된 타임 스탬프에 대한 `ticks` 속성 값을 반환합니다. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | 현재 타임스탬프를 문자열로 반환합니다. |

## <a name="function-reference"></a>함수 참조

이 섹션에는 사용 가능한 모든 함수가 알파벳 순서로 나열 됩니다.

<a name="add"></a>

### <a name="add"></a>추가

두 숫자를 더한 결과를 반환합니다.

```
add(<summand_1>, <summand_2>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | yes | 정수, 부동 소수점 수 또는 혼합 | 더할 값 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | -----| ----------- |
| <*result-sum*> | 정수 또는 부동 소수점 수 | 지정한 숫자들을 더한 결과 |
||||

*예제*

이 예제는 지정한 숫자들을 더합니다.

```
add(1, 1.5)
```

그리고 다음 결과를 반환합니다. `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

타임스탬프에 일 수를 추가합니다.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*days*> | yes | 정수 | 더할 양수 또는 음수 일 수 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 타임스탬프 + 지정한 일 수  |
||||

*예 1*

이 예제는 지정한 타임스탬프에 10일을 더합니다.

```
addDays('2018-03-15T13:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-25T00:00:0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5일을 뺍니다.

```
addDays('2018-03-15T00:00:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

타임스탬프에 시간 수를 추가합니다.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*hours*> | yes | 정수 | 더할 양수 또는 음수 시간 수 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 타임스탬프 + 지정한 시간 수  |
||||

*예 1*

이 예제는 지정한 타임스탬프에 10시간을 더합니다.

```
addHours('2018-03-15T00:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T10:00:0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5시간을 뺍니다.

```
addHours('2018-03-15T15:00:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

타임스탬프에 분 수를 추가합니다.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*minutes*> | yes | 정수 | 더할 양수 또는 음수 분 수 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 타임스탬프 + 지정한 분 수 |
||||

*예 1*

이 예제는 지정한 타임스탬프에 10분을 더합니다.

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:20:00.0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5분을 뺍니다.

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

타임스탬프에 초 수를 추가합니다.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*seconds*> | yes | 정수 | 더할 양수 또는 음수 초 수 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 타임스탬프 + 지정한 초 수  |
||||

*예 1*

이 예제는 지정한 타임스탬프에 10초를 더합니다.

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:10.0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에서 5초를 뺍니다.

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

타임스탬프에 시간 단위 수를 추가합니다.
[getFutureTime()](#getFutureTime)도 함께 참조하세요.

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*interval*> | yes | 정수 | 더할 지정한 시간 단위 수 |
| <*timeUnit*> | yes | string | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 타임스탬프 + 지정한 시간 단위 수  |
||||

*예 1*

이 예제는 지정한 타임스탬프에 1일을 더합니다.

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-02T00:00:00.0000000Z"`

*예 2*

이 예제는 지정한 타임스탬프에 1일을 더합니다.

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

그리고 선택적 "D"를 사용하여 다음 결과를 반환합니다. `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>및

모든 식이 true인지 확인합니다.
모든 식이 true이면 true를 반환하거나 식 중의 적어도 개가 false이면 false를 반환합니다.

```
and(<expression1>, <expression2>, ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | yes | Boolean | 검사할 식 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | -----| ----------- |
| true 또는 false | Boolean | 모든 식이 true이면 true를 반환합니다. 식 중의 적어도 한 개가 false이면 false를 반환합니다. |
||||

*예 1*

아래 예제는 지정한 부울 값이 모두 true인지 검사합니다.

```
and(true, true)
and(false, true)
and(false, false)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 식이 모두 true이므로 `true`를 반환합니다.
* 두 번째 예제: 식 한 개가 false이므로 `false`를 반환합니다.
* 세 번째 예제: 두 식이 모두 false이므로 `false`를 반환합니다.

*예 2*

아래 예제는 지정한 식이 모두 true인지 검사합니다.

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 식이 모두 true이므로 `true`를 반환합니다.
* 두 번째 예제: 식 한 개가 false이므로 `false`를 반환합니다.
* 세 번째 예제: 두 식이 모두 false이므로 `false`를 반환합니다.

<a name="array"></a>

### <a name="array"></a>array

단일 지정 입력에서 배열을 반환합니다.
여러 입력의 경우 [createArray()](#createArray)를 참조하세요.

```
array('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 배열을 만드는 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| [<*value*>] | 배열 | 단일 지정 입력을 포함하는 배열 |
||||

*예제*

이 예제는 "hello" 문자열에서 배열을 만듭니다.

```
array('hello')
```

그리고 다음 결과를 반환합니다. `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

문자열에 대한 base64로 인코딩된 버전을 반환합니다.

```
base64('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 입력 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*base64-string*> | string | 문자열에 대한 base64로 인코딩된 버전 |
||||

*예제*

이 예제는 "hello" 문자열을 base64로 인코딩된 문자열로 변환합니다.

```
base64('hello')
```

그리고 다음 결과를 반환합니다. `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다.

```
base64ToBinary('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 base64로 인코딩된 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | string | Base64로 인코딩된 문자열에 대한 이진 버전 |
||||

*예제*

이 예제는 base64로 인코딩된 문자열 "aGVsbG8="을 이진 문자열로 변환합니다.

```
base64ToBinary('aGVsbG8=')
```

그리고 다음 결과를 반환합니다.

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

base64로 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 base64 문자열을 디코딩합니다.
[decodeBase64()](#decodeBase64) 대신에 이 함수를 사용합니다.
두 함수 모두 같은 방법으로 작동하지만 `base64ToString()`을 사용하는 것이 좋습니다.

```
base64ToString('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 디코딩할 base64로 인코딩된 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | string | Base64로 인코딩된 문자열에 대한 문자열 버전 |
||||

*예제*

이 예제는 base64로 인코딩된 문자열 "aGVsbG8="을 단순한 문자열로 변환합니다.

```
base64ToString('aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

문자열에 대한 이진 버전을 반환합니다.

```
binary('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | string | 지정한 문자열에 대한 이진 버전 |
||||

*예제*

이 예제는 "hello" 문자열을 이진 문자열로 변환합니다.

```
binary('hello')
```

그리고 다음 결과를 반환합니다.

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

값에 대한 부울 버전을 반환합니다.

```
bool(<value>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 모두 | 변환할 값 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 지정한 값에 대한 부울 버전 |
||||

*예제*

아래 예제는 지정한 값을 부울 값으로 변환합니다.

```
bool(1)
bool(0)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

하나 이상의 매개 변수에서 null이 아닌 첫 번째 값을 반환합니다.
빈 문자열, 빈 배열 및 빈 개체는 null이 아닙니다.

```
coalesce(<object_1>, <object_2>, ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | yes | Any, 형식을 혼합할 수 있음 | Null인지 검사할 하나 이상의 항목 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | 모두 | Null이 아닌 첫 번째 항목 또는 값입니다. 모든 매개 변수가 Null이면 이 함수는 Null을 반환합니다. |
||||

*예제*

아래 예제는 지정한 값에서 Null이 아닌 첫 번째 값 또는 모든 값이 Null인 경우 Null을 반환합니다.

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `"hello"`
* 세 번째 예제: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다.

```
concat('<text1>', '<text2>', ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | yes | string | 결합할 적어도 두 개의 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*text1text2...* > | string | 결합된 입력 문자열에서 만든 문자열 |
||||

*예제*

이 예제는 문자열 "Hello"와 "World"를 결합합니다.

```
concat('Hello', 'World')
```

그리고 다음 결과를 반환합니다. `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

컬렉션에 특정 항목이 있는지 여부를 확인합니다.
항목이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다.
이 함수는 대/소문자를 구분합니다.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

특히 이 함수는 아래 컬렉션 형식에서 작동합니다.

* *부분 문자열*을 찾기 위한 *문자열*
* *값*을 찾기 위한 *배열*
* *키*를 찾기 위한 *사전*

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 문자열, 배열 또는 사전 | 검사할 컬렉션 |
| <*value*> | yes | 각각 문자열, 배열 또는 사전 | 찾을 항목 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 항목이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
||||

*예 1*

이 예제는 부분 문자열 "world"에 대한 문자열 "hello world"를 검사하고 true를 반환합니다.

```
contains('hello world', 'world')
```

*예 2*

이 예제는 부분 문자열 "universe"에 대한 문자열 "hello world"를 검사하고 false를 반환합니다.

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

UTC(Universal Time Coordinated)의 타임 스탬프를 대상 표준 시간대로 변환합니다.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*destinationTimeZone*> | yes | string | 대상 표준 시간대의 이름입니다. 표준 시간대 이름에 대해서는 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조 하세요. 표준 시간대 이름에서 모든 문장 부호를 제거 해야 할 수도 있습니다. |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | 대상 표준 시간대로 변환된 타임스탬프 |
||||

*예 1*

이 예제는 타임스탬프를 지정한 표준 시간대로 변환합니다.

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00.0000000"`

*예 2*

이 예제는 타임스탬프를 지정한 표준 시간대 및 형식으로 변환합니다.

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

원본 표준 시간대의 타임 스탬프를 대상 표준 시간대로 변환합니다.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*sourceTimeZone*> | yes | string | 원본 표준 시간대의 이름입니다. 표준 시간대 이름에 대해서는 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조 하세요. 표준 시간대 이름에서 모든 문장 부호를 제거 해야 할 수도 있습니다. |
| <*destinationTimeZone*> | yes | string | 대상 표준 시간대의 이름입니다. 표준 시간대 이름에 대해서는 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조 하세요. 표준 시간대 이름에서 모든 문장 부호를 제거 해야 할 수도 있습니다. |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | 대상 표준 시간대로 변환된 타임스탬프 |
||||

*예 1*

이 예제는 원본 표준 시간대를 대상 표준 시간대로 변환합니다.

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00.0000000"`

*예 2*

이 예제는 표준 시간대를 지정한 표준 시간대 및 형식으로 변환합니다.

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

원본 표준 시간대의 타임 스탬프를 UTC(Universal Time Coordinated)로 변환합니다.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*sourceTimeZone*> | yes | string | 원본 표준 시간대의 이름입니다. 표준 시간대 이름에 대해서는 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조 하세요. 표준 시간대 이름에서 모든 문장 부호를 제거 해야 할 수도 있습니다. |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | UTC로 변환된 타임스탬프 |
||||

*예 1*

이 예제는 타임스탬프를 UTC로 변환합니다.

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T08:00:00.0000000Z"`

*예 2*

이 예제는 타임스탬프를 UTC로 변환합니다.

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

여러 입력에서 배열을 반환합니다.
단일 입력 배열의 경우 [array()](#array)를 참조하세요.

```
createArray('<object1>', '<object2>', ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | yes | 모두, 그러나 혼합은 안 됨 | 배열을 만들 적어도 두 개의 항목 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | 배열 | 모든 입력 항목에서 만든 배열 |
||||

*예제*

이 예제는 다음 입력에서 배열을 만듭니다.

```
createArray('h', 'e', 'l', 'l', 'o')
```

그리고 다음 결과를 반환합니다. `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

문자열에 대한 데이터 URI(Uniform Resource Identifier)를 반환합니다.

```
dataUri('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*data-uri*> | string | 입력 문자열에 대한 데이터 URI |
||||

*예제*

이 예제는 "hello" 문자열에 대한 데이터 URI를 만듭니다.

```
dataUri('hello')
```

그리고 다음 결과를 반환합니다. `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

데이터 URI(Uniform Resource Identifier)에 대한 이진 버전을 반환합니다.
[decodeDataUri()](#decodeDataUri) 대신에 이 함수를 사용합니다.
두 함수 모두 같은 방법으로 작동하지만 `dataUriBinary()`을 사용하는 것이 좋습니다.

```
dataUriToBinary('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 데이터 URI |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | string | 데이터 URI에 대한 이진 버전 |
||||

*예제*

이 예제는 이 데이터 URI에 대한 이진 버전을 만듭니다.

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다.

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

데이터 URI(Uniform Resource Identifier)에 대한 문자열 버전을 반환합니다.

```
dataUriToString('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 데이터 URI |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | string | 데이터 URI에 대한 문자열 버전 |
||||

*예제*

이 예제는 이 데이터 URI에 대한 문자열을 만듭니다.

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

타임스탬프에서 월의 날짜를 반환합니다.

```
dayOfMonth('<timestamp>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*day-of-month*> | 정수 | 지정한 타임스탬프의 월의 날짜 |
||||

*예제*

이 예제는 이 타임스탬프에서 월의 날짜에 대한 숫자를 반환합니다.

```
dayOfMonth('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

타임스탬프에서 요일을 반환합니다.

```
dayOfWeek('<timestamp>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*day-of-week*> | 정수 | 일요일이 0, 월요일이 1이라는 식으로 지정한 타임스탬프의 요일 |
||||

*예제*

이 예제는 이 타임스탬프에서 요일에 대한 숫자를 반환합니다.

```
dayOfWeek('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

타임스탬프에서 년의 날짜를 반환합니다.

```
dayOfYear('<timestamp>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*day-of-year*> | 정수 | 지정한 타임스탬프의 년의 날짜 |
||||

*예제*

이 예제는 이 타임스탬프에서 년의 날짜인 숫자를 반환합니다.

```
dayOfYear('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

base64로 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 base64 문자열을 디코딩합니다.
`decodeBase64()` 대신에 [base64ToString()](#base64ToString)의 사용을 검토해 보세요.
두 함수 모두 같은 방법으로 작동하지만 `base64ToString()`을 사용하는 것이 좋습니다.

```
decodeBase64('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 디코딩할 base64로 인코딩된 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | string | Base64로 인코딩된 문자열에 대한 문자열 버전 |
||||

*예제*

이 예제는 base64로 인코딩된 문자열에 대한 문자열을 만듭니다.

```
decodeBase64('aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

데이터 URI(Uniform Resource Identifier)에 대한 이진 버전을 반환합니다.
`decodeDataUri()` 대신에 [dataUriToBinary()](#dataUriToBinary)의 사용을 검토해 보세요.
두 함수 모두 같은 방법으로 작동하지만 `dataUriToBinary()`을 사용하는 것이 좋습니다.

```
decodeDataUri('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 디코딩할 데이터 URI 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | string | 데이터 URI 문자열에 대한 이진 버전 |
||||

*예제*

이 예제는 이 데이터 URI에 대한 이진 버전을 반환합니다.

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다.

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

디코딩된 버전으로 이스케이프 문자를 대체하는 문자열을 반환합니다.

```
decodeUriComponent('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 디코딩할 이스케이프 문자를 포함한 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | string | 디코딩된 이스케이프 문자로 업데이트된 문자열 |
||||

*예제*

이 예제는 이 문자열의 이스케이프 문자를 디코딩된 버전으로 바꿉니다.

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

두 숫자를 나눈 정수 결과를 반환합니다.
나머지 결과를 얻으려면 [mod()](#mod)를 참조하세요.

```
div(<dividend>, <divisor>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | yes | 정수 또는 부동 소수점 수 | *제수*로 나눌 숫자 |
| <*divisor*> | yes | 정수 또는 부동 소수점 수 | *피제수*를 분할하는 수, 그러나 0이 올 수 없음 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*quotient-result*> | 정수 | 첫 번째 숫자를 두 번째 숫자로 나눈 정수 결과 |
||||

*예제*

두 예제 모두 첫 번째 숫자를 두 번째 숫자로 나눕니다.

```
div(10, 5)
div(11, 5)
```

그리고 다음 결과를 반환합니다. `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

URL 안전하지 않은 문자를 이스케이프 문자로 바꾸어 문자열에 대한 URI(Uniform Resource Identifier) 인코딩된 버전을 반환합니다.
`encodeUriComponent()` 대신에 [uriComponent()](#uriComponent)의 사용을 검토해 보세요.
두 함수 모두 같은 방법으로 작동하지만 `uriComponent()`을 사용하는 것이 좋습니다.

```
encodeUriComponent('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | URI 인코딩된 형식으로 변환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | 이스케이프 문자로 URI 인코딩된 문자열 |
||||

*예제*

이 예제는 이 문자열에 대한 URI 인코딩된 버전을 만듭니다.

```
encodeUriComponent('https://contoso.com')
```

그리고 다음 결과를 반환합니다. `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

컬렉션이 비어 있는지 여부를 확인합니다.
컬렉션이 비어 있으면 true를 반환하거나 비어 있지 않으면 false를 반환합니다.

```
empty('<collection>')
empty([<collection>])
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 문자열, 배열 또는 개체 | 검사할 컬렉션 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 컬렉션이 비어 있으면 true를 반환합니다. 비어 있지 않으면 false를 반환합니다. |
||||

*예제*

아래 예제는 지정한 컬렉션이 비어 있는지 검사합니다.

```
empty('')
empty('abc')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 비어 있는 문자열을 전달하므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 문자열 "abc"를 전달하므로 함수는 `false`를 반환합니다.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

문자열이 특정 하위 문자열로 종료하는지 검사합니다.
하위 문자열이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다.
이 함수는 대/소문자를 구분하지 않습니다.

```
endsWith('<text>', '<searchText>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 검사할 문자열 |
| <*searchText*> | yes | string | 찾을 끝나는 하위 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false  | Boolean | 끝나는 하위 문자열이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
||||

*예 1*

이 예제는 "hellow world" 문자열이 "world" 문자열로 끝나는지 검사합니다.

```
endsWith('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `true`

*예 2*

이 예제는 "hellow world" 문자열이 "universe" 문자열로 끝나는지 검사합니다.

```
endsWith('hello world', 'universe')
```

그리고 다음 결과를 반환합니다. `false`

<a name="equals"></a>

### <a name="equals"></a>equals

두 값, 식 또는 개체가 모두 같은지 검사합니다.
둘이 다 같으면 true를 반환하거나 같지 않으면 false를 반환합니다.

```
equals('<object1>', '<object2>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | yes | 다양 | 비교할 값, 식 또는 개체 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 둘이 같으면 true를 반환합니다. 같지 않으면 false를 반환합니다. |
||||

*예제*

아래 예제는 지정한 입력들이 같은지 검사합니다.

```
equals(true, 1)
equals('abc', 'abcd')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 값이 모두 같으므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 값이 같지 않으므로 함수는 `false`를 반환합니다.

<a name="first"></a>

### <a name="first"></a>first

문자열 또는 배열에서 첫 번째 항목을 반환합니다.

```
first('<collection>')
first([<collection>])
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 문자열 또는 배열 | 첫 번째 항목을 찾을 컬렉션 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | 모두 | 컬렉션의 첫 번째 항목 |
||||

*예제*

아래 예제는 이러한 컬렉션의 첫 번째 항목을 찾습니다.

```
first('hello')
first(createArray(0, 1, 2))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"h"`
* 두 번째 예제: `0`

<a name="float"></a>

### <a name="float"></a>float

부동 소수점 수에 대한 문자열 버전을 실제 부동 소수점 수로 변환합니다.

```
float('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 유효한 부동 소수점 수를 가진 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | 지정한 문자열에 대한 부동 소수점 수 |
||||

*예제*

이 예제는 이 부동 소수점 수에 대한 문자열 버전을 만듭니다.

```
float('10.333')
```

그리고 다음 결과를 반환합니다. `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

지정한 형식의 타임스탬프를 반환합니다.

```
formatDateTime('<timestamp>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | string | 지정한 형식으로 업데이트된 타임스탬프 |
||||

*예제*

이 예제는 타임스탬프를 지정한 형식으로 변환합니다.

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

지정된 시간 단위를 더한 현재 타임 스탬프를 반환합니다.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | yes | 정수 | 더할 지정한 시간 단위 수 |
| <*timeUnit*> | yes | string | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 현재 타임스탬프 + 지정한 시간 단위 수 |
||||

*예 1*

현재 타임스탬프가 "2018-03-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 해당 타임스탬프에 5일을 더합니다.

```
getFutureTime(5, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-03-06T00:00:00.0000000Z"`

*예 2*

현재 타임스탬프가 "2018-03-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 5일을 더하고 결과를 "D" 형식으로 변환합니다.

```
getFutureTime(5, 'Day', 'D')
```

그리고 다음 결과를 반환합니다. `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

지정된 시간 단위을 뺀 현재 타임 스탬프를 반환합니다.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | yes | 정수 | 뺄 지정한 시간 단위 수 |
| <*timeUnit*> | yes | string | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 현재 타임스탬프 - 지정한 시간 단위 수 |
||||

*예 1*

현재 타임스탬프가 "2018-02-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 해당 타임스탬프에서 5일을 뺍니다.

```
getPastTime(5, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-27T00:00:00.0000000Z"`

*예 2*

현재 타임스탬프가 "2018-02-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 5일을 빼고 결과를 "D" 형식으로 변환합니다.

```
getPastTime(5, 'Day', 'D')
```

그리고 다음 결과를 반환합니다. `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

첫 번째 값이 두 번째 값보다 큰지 확인합니다.
첫 번째 값이 더 크면 true를 반환하거나 더 작으면 false를 반환합니다.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 큰지 검사할 첫 번째 값 |
| <*compareTo*> | yes | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 값 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 첫 번째 값이 두 번째 값보다 크면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 작거나 같으면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 큰지 검사합니다.

```
greater(10, 5)
greater('apple', 'banana')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

첫 번째 값이 두 번째 값보다 크거나 같은지 여부를 확인합니다.
첫 번째 값이 두 번째 값보다 크거나 같으면 true를 반환하거나 첫 번째 값이 더 작으면 false를 반환합니다.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 크거나 같은지 검사할 첫 번째 값 |
| <*compareTo*> | yes | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 값 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 첫 번째 값이 두 번째 값보다 크거나 같으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 작으면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다.

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

GUID(전역적으로 고유한 식별자)를 문자열로 생성합니다. 예, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

또한 GUID에 대해 하이픈으로 구분된 32자리인 기본 형식 "D" 이외의 다른 형식을 지정할 수 있습니다.

```
guid('<format>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*format*> | 아닙니다. | string | 반환되는 GUID에 대한 단일 [형식 지정자](https://msdn.microsoft.com/library/97af8hh4)입니다. 기본적으로 형식은 "D"이지만 "N", "D", "B", "P" 또는 "X"를 사용할 수 있습니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*GUID-value*> | string | 임의로 생성된 GUID |
||||

*예제*

이 예제는 같은 GUID를 생성하되, 하이픈으로 구분되고 소괄호로 둘러싼 32자리로 생성합니다.

```
guid('P')
```

그리고 다음 결과를 반환합니다. `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

식이 true인지 또는 false인지 확인합니다.
결과에 기반해 지정된 값을 반환합니다.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | yes | Boolean | 검사할 식 |
| <*valueIfTrue*> | yes | 모두 | 식이 true이면 반환할 값 |
| <*valueIfFalse*> | yes | 모두 | 식이 false이면 반환할 값 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | 모두 | 식이 true인지 또는 false인지에 기반해 반환하는 지정한 값 |
||||

*예제*

이 예제는 지정한 식이 true를 반환하므로 `"yes"`를 반환합니다.
그렇지 않으면 이 예제는 `"no"`를 반환합니다.

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexof

하위 문자열에 대한 시작 위치 또는 인덱스 값을 반환합니다.
이 함수는 대/소문자를 구분하지 않으며 인덱스는 숫자 0으로 시작합니다.

```
indexOf('<text>', '<searchText>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 찾을 하위 문자열을 가진 문자열 |
| <*searchText*> | yes | string | 찾을 하위 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*index-value*>| 정수 | 지정한 하위 문자열에 대한 시작 위치 또는 인덱스 값 <p>문자열을 찾을 수 없는 경우 -1을 반환합니다. |
||||

*예제*

이 예제는 "hello world" 문자열의 "world" 하위 문자열에 대한 시작 인덱스 값을 찾습니다.

```
indexOf('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `6`

<a name="int"></a>

### <a name="int"></a>int

문자열에 대한 정수 버전을 반환합니다.

```
int('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*integer-result*> | 정수 | 지정한 문자열에 대한 정수 버전 |
||||

*예제*

이 예제는 문자열 "10"에 대한 정수 버전을 만듭니다.

```
int('10')
```

그리고 다음 결과를 반환합니다. `10`

<a name="json"></a>

### <a name="json"></a>json :

JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다.

```
json('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 문자열 또는 XML | 변환할 문자열 또는 XML |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*JSON-result*> | JSON 원시 형식 또는 개체 | JSON 고유 형식 값 또는 지정한 문자열이나 XML에 대한 개체입니다. 문자열이 Null이면, 함수는 빈 개체를 반환합니다. |
||||

*예 1*

이 예제는 이 문자열을 JSON 값으로 변환합니다.

```
json('[1, 2, 3]')
```

그리고 다음 결과를 반환합니다. `[1, 2, 3]`

*예 2*

이 예제는 이 문자열을 JSON으로 변환합니다.

```
json('{"fullName": "Sophia Owen"}')
```

그리고 다음 결과를 반환합니다.

```
{
  "fullName": "Sophia Owen"
}
```

*예 3*

이 예제는 이 XML을 JSON으로 변환합니다.

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

그리고 다음 결과를 반환합니다.

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>교집합

지정된 컬렉션에서 공통 항목*만* 있는 컬렉션을 반환합니다.
결과에 나타내려면 항목이 이 함수에 전달된 모든 컬렉션에 나타나야 합니다.
같은 이름을 가진 항목이 한 개 이상이면 해당 이름의 마지막 항목이 결과에 나타납니다.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | yes | 배열 또는 개체, 그러나 둘 다는 아님 | 공통 항목*만* 원하는 컬렉션 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*common-items*> | 각각 배열 또는 개체 | 지정한 컬렉션에 대해 공통 항목만 있는 컬렉션 |
||||

*예제*

이 예제는 이러한 배열에 대한 공통 항목을 찾습니다.

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

그리고 다음 항목*만* 가진 배열을 반환합니다. `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

배열의 모든 항목을 포함하고 각 문자가 *구분 기호*로 구분된 문자열을 반환합니다.

```
join([<collection>], '<delimiter>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 배열 | 조인할 항목이 있는 배열 |
| <*delimiter*> | yes | string | 결과 문자열의 각 문자 사이에 나타나는 구분 기호 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | string | 지정한 배열의 모든 항목에서 만든 결과 문자열 |
||||

*예제*

이 예제는 지정한 문자를 구분 기호로 하여 이 배열의 모든 항목에서 문자열을 만듭니다.

```
join(createArray('a', 'b', 'c'), '.')
```

그리고 다음 결과를 반환합니다. `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

컬렉션에서 마지막 항목을 반환합니다.

```
last('<collection>')
last([<collection>])
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 문자열 또는 배열 | 마지막 항목을 찾을 컬렉션 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | 각각 문자열 또는 배열 | 컬렉션의 마지막 항목 |
||||

*예제*

아래 예제는 이러한 컬렉션의 마지막 항목을 찾습니다.

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"d"`
* 두 번째 예제: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastindexof

마지막으로 나오는 부분 문자열의 시작 위치 또는 인덱스 값을 반환합니다.
이 함수는 대/소문자를 구분하지 않으며 인덱스는 숫자 0으로 시작합니다.

```
lastIndexOf('<text>', '<searchText>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 찾을 하위 문자열을 가진 문자열 |
| <*searchText*> | yes | string | 찾을 하위 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | 정수 | 마지막으로 나오는 지정된 부분 문자열의 시작 위치 또는 인덱스 값입니다. <p>문자열을 찾을 수 없는 경우 -1을 반환합니다. |
||||

*예제*

이 예제는 "hello world" 문자열의 "world" 하위 문자열이 마지막으로 나오는 경우의 시작 인덱스 값을 찾습니다.

```
lastIndexOf('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `6`

<a name="length"></a>

### <a name="length"></a>length

컬렉션의 항목 수를 반환합니다.

```
length('<collection>')
length([<collection>])
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 문자열 또는 배열 | 개수를 셀 항목을 포함하는 컬렉션 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*length-or-count*> | 정수 | 컬렉션의 항목 수 |
||||

*예제*

아래 예제는 이러한 컬렉션의 항목 수를 셉니다.

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

그리고 다음 결과를 반환합니다. `4`

<a name="less"></a>

### <a name="less"></a>less

첫 번째 값이 두 번째 값보다 작은지 여부를 확인합니다.
첫 번째 값이 두 번째 값보다 작으면 true를 반환하거나 첫 번째 값이 더 크면 false를 반환합니다.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 작은지 검사할 첫 번째 값 |
| <*compareTo*> | yes | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 항목 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 첫 번째 값이 두 번째 값보다 작으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 크거나 같으면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 작은지 검사합니다.

```
less(5, 10)
less('banana', 'apple')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

첫 번째 값이 두 번째 값보다 작거나 같은지 여부를 확인합니다.
첫 번째 값이 두 번째 값보다 작으면 true를 반환하거나 첫 번째 값이 더 크면 false를 반환합니다.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 작거나 같은지 검사할 첫 번째 값 |
| <*compareTo*> | yes | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 항목 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false  | Boolean | 첫 번째 값이 두 번째 값보다 작거나 같으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 크면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="max"></a>

### <a name="max"></a>max

양끝이 포함된 숫자를 가진 목록 또는 배열에서 가장 높은 값을 반환합니다.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | yes | 정수, 부동 소수점 수 또는 둘 다 | 가장 높은 값을 원하는 숫자의 집합 |
| [<*number1*>, <*number2*>, ...] | yes | 배열 - 정수, 부동 소수점 수 또는 둘 다 | 가장 높은 값을 원하는 숫자의 배열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*max-value*> | 정수 또는 부동 소수점 수 | 지정한 배열 또는 숫자 집합의 가장 높은 값 |
||||

*예제*

아래 예제는 숫자 집합 및 배열에서 가장 높은 값을 가져옵니다.

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

그리고 다음 결과를 반환합니다. `3`

<a name="min"></a>

### <a name="min"></a>분

숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | yes | 정수, 부동 소수점 수 또는 둘 다 | 가장 낮은 값을 원하는 숫자의 집합 |
| [<*number1*>, <*number2*>, ...] | yes | 배열 - 정수, 부동 소수점 수 또는 둘 다 | 가장 낮은 값을 원하는 숫자의 배열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*min-value*> | 정수 또는 부동 소수점 수 | 지정한 숫자 집합 또는 지정한 배열의 가장 낮은 값 |
||||

*예제*

아래 예제는 숫자 집합 및 배열에서 가장 낮은 값을 가져옵니다.

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

그리고 다음 결과를 반환합니다. `1`

<a name="mod"></a>

### <a name="mod"></a>mod

두 숫자를 나눈 나머지를 반환합니다.
정수 결과를 가져오는 방법은 [div()](#div)를 참조하세요.

```
mod(<dividend>, <divisor>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | yes | 정수 또는 부동 소수점 수 | *제수*로 나눌 숫자 |
| <*divisor*> | yes | 정수 또는 부동 소수점 수 | *피제수*를 분할하는 숫자, 그러나 0이 올 수 없습니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*modulo-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 나눈 나머지 |
||||

*예제*

이 예제는 첫 번째 숫자를 두 번째 숫자로 나눕니다.

```
mod(3, 2)
```

그리고 다음 결과를 반환합니다. `1`

<a name="mul"></a>

### <a name="mul"></a>mul

두 숫자를 곱한 산출물을 반환합니다.

```
mul(<multiplicand1>, <multiplicand2>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | yes | 정수 또는 부동 소수점 수 | *multiplicand2*로 곱할 숫자 |
| <*multiplicand2*> | yes | 정수 또는 부동 소수점 수 | *multiplicand1*를 곱하는 숫자 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*product-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 곱한 결과 |
||||

*예제*

아래 예제는 첫 번째 숫자를 두 번째 숫자로 곱합니다.

```
mul(1, 2)
mul(1.5, 2)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `2`
* 두 번째 예제 `3`

<a name="not"></a>

### <a name="not"></a>not

식이 false인지 확인합니다.
식이 false이면 true를 반환하거나 true이면 false를 반환합니다.

```json
not(<expression>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | yes | Boolean | 검사할 식 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 식이 false이면 true를 반환합니다. 식이 true이면 false를 반환합니다. |
||||

*예 1*

아래 예제는 지정한 식이 모두 false인지 검사합니다.

```json
not(false)
not(true)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 식이 false이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 식이 true이므로 함수는 `false`를 반환합니다.

*예 2*

아래 예제는 지정한 식이 모두 false인지 검사합니다.

```json
not(equals(1, 2))
not(equals(1, 1))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 식이 false이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 식이 true이므로 함수는 `false`를 반환합니다.

<a name="or"></a>

### <a name="or"></a>or

최소 하나의 식이 true인지 확인합니다.
최소 하나의 식이 true이면 true를 반환하거나 모든 식이 false이면 false를 반환합니다.

```
or(<expression1>, <expression2>, ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | yes | Boolean | 검사할 식 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false | Boolean | 최소 하나의 식이 true이면 true를 반환합니다. 모든 식이 false이면 false를 반환합니다. |
||||

*예 1*

아래 예제는 최소 하나의 식이 true인지 검사합니다.

```json
or(true, false)
or(false, false)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 최소 하나의 식이 true이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 식이 모두 false이므로 함수는 `false`를 반환합니다.

*예 2*

아래 예제는 최소 하나의 식이 true인지 검사합니다.

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 최소 하나의 식이 true이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 식이 모두 false이므로 함수는 `false`를 반환합니다.

<a name="rand"></a>

### <a name="rand"></a>rand

시작 끝만 포함하는 지정한 범위에서 임의의 정수를 반환합니다.

```
rand(<minValue>, <maxValue>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | yes | 정수 | 범위의 가장 작은 정수 |
| <*maxValue*> | yes | 정수 | 함수가 반환할 수 있는 범위에서 가장 높은 정수 다음에 오는 정수 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*random-result*> | 정수 | 지정한 범위에서 반환된 임의의 정수 |
||||

*예제*

이 예제는 최댓값을 제외하고 지정한 범위에서 임의의 정수를 가져옵니다.

```
rand(1, 5)
```

그리고 다음 숫자 중 하나를 결과로 반환합니다. `1`, `2`, `3` 또는 `4`

<a name="range"></a>

### <a name="range"></a>range

지정된 정수에서 시작하는 정수 배열을 반환합니다.

```
range(<startIndex>, <count>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | yes | 정수 | 첫 번째 항목으로 배열을 시작 하는 정수 값입니다. |
| <*count*> | yes | 정수 | 배열에 있는 정수의 숫자 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| [<*range-result*>] | 배열 | 지정한 인덱스에서 시작하는 정수를 포함하는 배열 |
||||

*예제*

이 예제는 지정한 인덱스에서 시작하고 지정한 수의 정수를 가진 정수 배열을 만듭니다.

```
range(1, 4)
```

그리고 다음 결과를 반환합니다. `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

하위 문자열을 지정한 문자열로 바꾸고 결과 문자열을 반환합니다. 이 함수는 대/소문자를 구분합니다.

```
replace('<text>', '<oldText>', '<newText>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 바꿀 하위 문자열을 가진 문자열 |
| <*oldText*> | yes | string | 바꿀 하위 문자열 |
| <*newText*> | yes | string | 대체 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-text*> | string | 하위 문자열을 바꾼 후 업데이트된 문자열 <p>하위 문자열이 발견되지 않으면 원래 문자열을 반환합니다. |
||||

*예제*

이 예제는 "the old string"의 "old" 하위 문자열을 찾고 "old"를 "new"로 바꿉니다.

```
replace('the old string', 'old', 'new')
```

그리고 다음 결과를 반환합니다. `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

컬렉션 앞에서 항목을 제거하고 *다른 모든* 항목을 반환합니다.

```
skip([<collection>], <count>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 배열 | 해당 항목을 제거할 컬렉션 |
| <*count*> | yes | 정수 | 앞에서 제거할 항목 수에 대한 양수 정수 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | 배열 | 지정한 항목을 제거한 후 업데이트된 컬렉션 |
||||

*예제*

이 예제는 지정한 배열 앞에서 항목 한 개, 숫자 0을 제거합니다.

```
skip(createArray(0, 1, 2, 3), 1)
```

그리고 남은 항목을 포함하는 다음 배열을 반환합니다. `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

원래 문자열의 지정 된 구분 기호 문자를 기준으로 쉼표로 구분 된 부분 문자열이 포함 된 배열을 반환 합니다.

```
split('<text>', '<delimiter>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 원래 문자열에 지정된 구분 기호를 기준으로 하여 하위 문자열로 구분할 문자열입니다. |
| <*delimiter*> | yes | string | 구분 기호로 사용할 원래 문자열의 문자입니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | 배열 | 원래 문자열의 하위 문자열이 포함된 배열(쉼표로 구분됨)입니다. |
||||

*예제*

이 예제에서는 지정된 문자(구분 기호)를 기준으로 하여 지정한 문자열의 하위 문자열이 포함된 배열을 만듭니다.

```
split('a_b_c', '_')
```

그리고 `["a","b","c"]` 배열을 결과로 반환합니다.

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

타임 스탬프에 대한 날의 시작을 반환합니다.

```
startOfDay('<timestamp>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 지정한 타임스탬프이지만 해당 날짜의 0시 마크에서 시작 |
||||

*예제*

이 예제는 이 타임스탬프에 대한 날의 시작을 찾습니다.

```
startOfDay('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

타임 스탬프에 대한 시간의 시작을 반환합니다.

```
startOfHour('<timestamp>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 지정한 타임스탬프이지만 시에 대한 0분 마크에서 시작 |
||||

*예제*

이 예제는 이 타임스탬프에 대한 시의 시작을 찾습니다.

```
startOfHour('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

타임 스탬프에 대한 분의 시작을 반환합니다.

```
startOfMonth('<timestamp>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 지정한 타임스탬프이지만 0시 마크에 있는 월의 첫 번째 날에 시작 |
||||

*예제*

이 예제는 이 타임스탬프에 대한 월의 시작을 반환합니다.

```
startOfMonth('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startswith

문자열이 특정 하위 문자열로 시작 하는지를 확인합니다.
하위 문자열이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다.
이 함수는 대/소문자를 구분하지 않습니다.

```
startsWith('<text>', '<searchText>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 검사할 문자열 |
| <*searchText*> | yes | string | 찾을 시작 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| true 또는 false  | Boolean | 시작 하위 문자열이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
||||

*예 1*

이 예제는 "hello world" 문자열이 "hello" 하위 문자열로 시작하는지 검사합니다.

```
startsWith('hello world', 'hello')
```

그리고 다음 결과를 반환합니다. `true`

*예 2*

이 예제는 "hello world" 문자열이 "greetings" 하위 문자열로 시작하는지 검사합니다.

```
startsWith('hello world', 'greetings')
```

그리고 다음 결과를 반환합니다. `false`

<a name="string"></a>

### <a name="string"></a>문자열

값에 대한 문자열 버전을 반환합니다.

```
string(<value>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | 모두 | 변환할 값 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*string-value*> | string | 지정한 값에 대한 문자열 버전 |
||||

*예 1*

이 예제는 이 숫자에 대한 문자열 버전을 만듭니다.

```
string(10)
```

그리고 다음 결과를 반환합니다. `"10"`

*예 2*

이 예제는 지정한 JSON 개체에 대한 문자열을 만들고 백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다.

```
string( { "name": "Sophie Owen" } )
```

그리고 다음 결과를 반환합니다. `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다.

```
sub(<minuend>, <subtrahend>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | yes | 정수 또는 부동 소수점 수 | *감수*를 뺄 수 |
| <*subtrahend*> | yes | 정수 또는 부동 소수점 수 | *피감수*에서 뺄 수 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과 |
||||

*예제*

이 예제는 첫 번째 숫자에서 두 번째 숫자를 뺍니다.

```
sub(10.3, .3)
```

그리고 다음 결과를 반환합니다. `10`

<a name="substring"></a>

### <a name="substring"></a>substring

지정한 위치 또는 인덱스에서 시작하여 문자열에서 문자를 반환합니다.
인덱스 값은 숫자 0으로 시작합니다.

```
substring('<text>', <startIndex>, <length>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 해당 문자를 원하는 문자열 |
| <*startIndex*> | yes | 정수 | 시작 위치 또는 인덱스 값으로 사용 하려는 0 보다 크거나 같은 양수입니다. |
| <*length*> | yes | 정수 | 하위 문자열에 원하는 문자의 양수 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*substring-result*> | string | 원본 문자열의 지정한 인덱스 위치에서 시작하는 지정한 수의 문자를 포함하는 하위 문자열 |
||||

*예제*

이 예제는 인덱스 값 6에서 시작하여 지정한 문자열에서 문자 하위 문자열 5개를 만듭니다.

```
substring('hello world', 6, 5)
```

그리고 다음 결과를 반환합니다. `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

타임스탬프에서 시간 단위 수를 뺍니다.
[getPastTime](#getPastTime)도 참조합니다.

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프를 포함하는 문자열 |
| <*interval*> | yes | 정수 | 뺄 지정한 시간 단위 수 |
| <*timeUnit*> | yes | string | *interval*과 함께 사용할 시간 단위: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | 타임스탬프 - 지정한 시간 단위 수 |
||||

*예 1*

이 예제는 이 타임스탬프에서 1일을 뺍니다.

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00:0000000Z"`

*예 2*

이 예제는 이 타임스탬프에서 1일을 뺍니다.

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

그리고 선택적 "D" 형식을 사용하여 다음 결과를 반환합니다. `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

컬렉션 앞에서 항목을 반환합니다.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | yes | 문자열 또는 배열 | 해당 항목을 원하는 컬렉션 |
| <*count*> | yes | 정수 | 앞에서 제거할 항목 수에 대한 양수 정수 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*subset*> or [<*subset*>] | 각각 문자열 또는 배열 | 원본 컬렉션의 앞에서 가져온 지정한 수의 항목을 포함하는 문자열 또는 배열 |
||||

*예제*

아래 예제는 이 컬렉션의 앞에서 지정한 수의 항목을 가져옵니다.

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"abc"`
* 두 번째 예제: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

지정된 타임 스탬프에 대한 `ticks` 속성 값을 반환합니다.
*tick*은 100나노초 간격입니다.

```
ticks('<timestamp>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | yes | string | 타임스탬프에 대한 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*ticks-number*> | 정수 | 지정한 타임스탬프 이후 틱 수 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

문자열을 소문자 형식으로 반환 합니다. 문자열의 문자에 소문자 버전이 없으면 반환된 문자열에 해당 문자가 변경되지 않고 포함됩니다.

```
toLower('<text>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 소문자 형식으로 반환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | string | 소문자 형식의 원래 문자열 |
||||

*예제*

이 예제는 이 문자열을 소문자로 변환합니다.

```
toLower('Hello World')
```

그리고 다음 결과를 반환합니다. `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

문자열을 대문자 형식으로 반환합니다. 문자열의 문자에 대문자 버전이 없으면 반환된 문자열에 해당 문자가 변경되지 않고 포함됩니다.

```
toUpper('<text>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 대문자 형식으로 반환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | string | 대문자 형식의 원래 문자열 |
||||

*예제*

이 예제는 이 문자열을 대문자로 변환합니다.

```
toUpper('Hello World')
```

그리고 다음 결과를 반환합니다. `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>trim

문자열에서 선행 및 후행 공백을 제거하고 업데이트 된 문자열을 반환합니다.

```
trim('<text>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*text*> | yes | string | 제거할 선행 및 후행 공백이 있는 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updatedText*> | string | 선행 또는 후행 공백이 없는 원래 문자열에 대한 업데이트된 버전 |
||||

*예제*

이 예제는 문자열 " Hello World  "에서 선행 및 후행 공백을 제거합니다.

```
trim(' Hello World  ')
```

그리고 다음 결과를 반환합니다. `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

지정된 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다.
결과에 나타내기 위해 항목이 이 함수에 전달된 모든 컬렉션에 나타날 수 있습니다. 같은 이름을 가진 항목이 한 개 이상이면 해당 이름의 마지막 항목이 결과에 나타납니다.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | yes | 배열 또는 개체, 그러나 둘 다는 아님 | *모든* 항목을 원하는 컬렉션 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | 각각 배열 또는 개체 | 지정한 컬렉션의 모든 항목을 포함하는 컬렉션 - 중복 없음 |
||||

*예제*

이 예제는 다음 컬렉션에서 *모든* 항목을 가져옵니다.

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

그리고 다음 결과를 반환합니다. `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

URL 안전하지 않은 문자를 이스케이프 문자로 바꾸어 문자열에 대한 URI(Uniform Resource Identifier) 인코딩된 버전을 반환합니다.
[encodeUriComponent()](#encodeUriComponent) 대신에 이 함수를 사용합니다.
두 함수 모두 같은 방법으로 작동하지만 `uriComponent()`을 사용하는 것이 좋습니다.

```
uriComponent('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | URI 인코딩된 형식으로 변환할 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | 이스케이프 문자로 URI 인코딩된 문자열 |
||||

*예제*

이 예제는 이 문자열에 대한 URI 인코딩된 버전을 만듭니다.

```
uriComponent('https://contoso.com')
```

그리고 다음 결과를 반환합니다. `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

URI(Uniform Resource Identifier) 구성 요소에 대한 이진 버전을 반환합니다.

```
uriComponentToBinary('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 URI 인코딩된 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | string | URI 인코딩된 문자열에 대한 이진 버전 이진 콘텐츠는 base64로 인코딩되며 `$content`에 의해 표시됩니다. |
||||

*예제*

이 예제는 이 URI 인코딩된 문자열에 대한 이진 버전을 만듭니다.

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다.

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

URI(Uniform Resource Identifier) 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 URI 인코딩된 문자열을 디코딩합니다.

```
uriComponentToString('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 디코딩할 URI 인코딩된 문자열 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | string | URI 인코딩된 문자열에 대한 디코딩된 버전 |
||||

*예제*

이 예제는 이 URI 인코딩된 문자열에 대한 디코딩된 버전을 만듭니다.

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

현재 타임스탬프를 반환합니다.

```
utcNow('<format>')
```

선택적으로 <*format*> 매개 변수를 사용하여 다른 형식을 지정할 수 있습니다.

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*format*> | 아닙니다. | string | [단일 형식 지정자](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | string | 현재 날짜 및 시간 |
||||

*예 1*

오늘이 2018년 4월 15일 오후 1시 00분 00초라고 가정합니다.
이 예제는 현재 타임스탬프를 가져옵니다.

```
utcNow()
```

그리고 다음 결과를 반환합니다. `"2018-04-15T13:00:00.0000000Z"`

*예 2*

오늘이 2018년 4월 15일 오후 1시 00분 00초라고 가정합니다.
이 예제는 선택적 "D" 형식을 사용하여 현재 타임스탬프를 가져옵니다.

```
utcNow('D')
```

그리고 다음 결과를 반환합니다. `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>Xml

JSON 개체를 포함하는 문자열에 대한 XML 버전을 반환합니다.

```
xml('<value>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*value*> | yes | string | 변환할 JSON 개체를 가진 문자열 <p>JSON 개체는 배열일 수 없는 루트 속성 한 개만 가져야 합니다. <br>백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다. |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | 지정한 문자열 또는 JSON 개체에 대한 인코딩된 XML |
||||

*예 1*

이 예제는 이 문자열에 대한 XML 버전을 만들며, 이 문자열은 JSON 개체를 포함합니다.

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

그리고 다음 결과 XML을 반환합니다.

```xml
<name>Sophia Owen</name>
```

*예 2*

이 JSON 개체가 있다고 가정합니다.

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

이 예제는 이 JSON 개체를 포함하는 문자열에 대한 XML을 만듭니다.

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

그리고 다음 결과 XML을 반환합니다.

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. XPath 식 또는 단순히 "XPath"는 XML 콘텐츠에서 노드를 선택하고 값을 계산할 수 있도록 XML 문서 구조를 탐색하는 데 도움이 됩니다.

```
xpath('<xml>', '<xpath>')
```

| 매개 변수를 포함해야 합니다. | 필수 | Type | 설명 |
| --------- | -------- | ---- | ----------- |
| <*xml*> | yes | 모두 | XPath 식 값과 일치하는 노드 또는 값을 검색하기 위한 XML 문자열 |
| <*xpath*> | yes | 모두 | 일치하는 XML 노드 또는 값을 찾는 데 사용한 XPath 식 |
|||||

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | 단일 노드만이 지정한 XPath 식과 일치하는 경우 XML 노드 |
| <*value*> | 모두 | 단일 값만이 지정한 XPath 식과 일치하는 경우 XML 노드의 값 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>또는 </br>[<*value1*>, <*value2*>, ...] | 배열 | 지정한 XPath 식과 일치하는 XML 노드 또는 값을 가진 배열 |
||||

*예 1*

예제 1 다음에 이 예제는 `<count></count>` 노드와 일치하는 노드를 찾고 `sum()` 함수를 사용하여 해당 노드 값을 더합니다.

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

그리고 다음 결과를 반환합니다. `30`

*예 2*

이 예제의 경우 두 식은 모두 지정한 인수의 `<location></location>` 노드와 일치하는 노드를 찾으며, 이 인수는 네임스페이스와 함께 XML을 포함합니다. 이 식은 백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다.

* *식 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *식 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

다음은 인수입니다.

* 다음 XML 문서 네임스페이스를 포함하는 이 XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* 여기서 XPath 식 중 하나:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

다음은 `<location></location>` 노드와 일치하는 결과 노드입니다.

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*예 3*

예제 3 다음에 이 예제는 `<location></location>` 노드에서 값을 찾습니다.

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

그리고 다음 결과를 반환합니다. `"Paris"`

## <a name="next-steps"></a>다음 단계
식에 사용할 수 있는 시스템 변수 목록은 [시스템 변수](control-flow-system-variables.md)를 참조하세요.
