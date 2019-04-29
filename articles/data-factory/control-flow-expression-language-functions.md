---
title: Azure Data Factory의 식과 함수 | Microsoft Docs
description: 이 문서에서는 데이터 팩터리 엔터티 만들기에 사용할 수 있는 식과 함수에 대한 정보를 제공합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 8a2a080ee87d48d25d7d793ca0aca463f25e52eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808832"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory의 식과 함수
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-functions-variables.md)
> * [현재 버전](control-flow-expression-language-functions.md)

이 문서에서는 Azure Data Factory에서 지원하는 식과 함수에 대한 정보를 제공합니다. 

## <a name="introduction"></a>소개
정의의 JSON 값은 리터럴일 수도 있고 정의가 런타임에 평가되는 식일 수도 있습니다. 예를 들면 다음과 같습니다.  
  
```json
"name": "value"
```

 (또는)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>식  
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
  
### <a name="examples"></a>예

#### <a name="a-dataset-with-a-parameter"></a>매개 변수가 포함된 데이터 세트
다음 예제에서는 BlobDataset은 **경로**라는 매개 변수를 사용합니다. `@{dataset().path}` 식을 사용하여 **folderPath** 속성에 대한 값을 설정하도록 해당 값을 사용합니다. 

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

#### <a name="a-pipeline-with-a-parameter"></a>매개 변수가 포함된 파이프라인
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
  
## <a name="functions"></a>Functions  
 식 내에서 함수를 호출할 수 있습니다. 다음 섹션에서는 식에서 사용할 수 있는 함수에 대한 정보를 제공합니다.  

## <a name="string-functions"></a>문자열 함수  
 다음 함수는 문자열에만 적용됩니다. 문자열에서 여러 컬렉션 함수도 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|concat|임의 개수 문자열을 함께 결합합니다. 예를 들어, parameter1이 `foo,`이면 다음 식이 `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1 ... *n*<br /><br /> **이름**: 문자열 *n*<br /><br /> **설명**: 필수 사항입니다. 단일 문자열로 결합할 문자열입니다.|  
|substring|문자열의 하위 집합을 반환합니다. 예를 들어 다음 식은<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> 다음을 반환합니다.<br /><br /> `foo`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 부분 문자열을 가져올 원래 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Start index<br /><br /> **설명**: 필수 사항입니다. 매개 변수 1에서 하위 문자열이 시작되는 위치의 인덱스입니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: 길이<br /><br /> **설명**: 필수 사항입니다. 부분 문자열의 길이입니다.|  
|replace|문자열을 지정된 문자열로 바꿉니다. 예를 들어 다음 식은<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> 다음을 반환합니다.<br /><br /> `the new string`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: string<br /><br /> **설명**: 필수 사항입니다.  매개 변수 2가 매개 변수 1에 있으면 매개 변수 2에 대해 문자열을 검색하고 매개 변수 3으로 업데이트됩니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Old string<br /><br /> **설명**: 필수 사항입니다. 매개 변수 1에 일치 항목이 있으면 매개 변수 3으로 바꿀 문자열입니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: New string<br /><br /> **설명**: 필수 사항입니다. 매개 변수 1에 일치 항목이 있으면 매개 변수 2의 문자열을 바꾸는 데 사용할 문자열입니다.|  
|GUID| 전역적으로 고유한 문자열(GUID)을  생성합니다. 예를 들어 다음 출력을 생성할 수 있습니다.`c2ecc88d-88c8-4096-912c-d6f2e2b138ce`<br /><br /> `guid()`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. [이 Guid 값의 형식을 지정하는 방법](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx)을 나타내는 단일 형식 지정자입니다. 형식 매개 변수는 "N", "D", "B", "P" 또는 "X"일 수 있습니다. 형식이 제공되지 않으면 "D"가 사용됩니다.|  
|toLower|문자열을 소문자로 변환합니다. 예를 들어 다음은 `two by two is four`: `toLower('Two by Two is Four')`을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 소문자로 변환할 문자열입니다. 문자열에 있는 문자에 소문자로 변환할 항목이 없으면 반환된 문자열에서 해당 문자가 변경되지 않고 포함됩니다.|  
|toUpper|문자열을 대문자로 변환합니다. 예를 들어 다음 식은 `TWO BY TWO IS FOUR`:  `toUpper('Two by Two is Four')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 대문자로 변환할 문자열입니다. 문자열에 있는 문자에 대문자로 변환할 항목이 없으면 반환된 문자열에서 해당 문자가 변경되지 않고 포함됩니다.|  
|indexof|문자열 내에서 대소문자를 구분하지 않고 값의 인덱스를 찾습니다. 예를 들어 다음 식은 `7`: `indexof('hello, world.', 'world')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 문자열은 이 값을 포함할 수 있습니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 인덱스를 검색할 값입니다.|  
|lastindexof|문자열 내에서 대소문자를 구분하지 않고 값의 마지막 인덱스를 찾습니다. 예를 들어 다음 식은 `3`: `lastindexof('foofoo', 'foo')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 문자열은 이 값을 포함할 수 있습니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 인덱스를 검색할 값입니다.|  
|startswith|문자열이 대소문자를 구분하지 않고 값으로 시작하는지 확인합니다. 예를 들어 다음 식은 `true`: `startswith('hello, world', 'hello')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 문자열은 이 값을 포함할 수 있습니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 문자열은 이 값으로 시작할 수 있습니다.|  
|endswith|문자열이 대소문자를 구분하지 않고 값으로 끝나는지 확인합니다. 예를 들어 다음 식은 `true`: `endswith('hello, world', 'world')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 문자열은 이 값을 포함할 수 있습니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 문자열은 이 값으로 끝날 수 있습니다.|  
|분할|구분 기호를 사용하여 문자열을 분할합니다. 예를 들어 다음 식은 `["a", "b", "c"]`: `split('a;b;c',';')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 분할된 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 구분 기호입니다.|  
  
  
## <a name="collection-functions"></a>컬렉션 함수  
 이 함수는 배열, 문자열 및 경우에 따라 사전 등 컬렉션에 대해 작동합니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|contains|사전에 키가 포함되거나, 목록에 값이 포함되거나, 문자열에 하위 문자열이 포함된 경우 true를 반환합니다. 예를 들어 다음 식은 `true:``contains('abacaba','aca')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Within collection<br /><br /> **설명**: 필수 사항입니다. 내부를 검색할 컬렉션입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Find object<br /><br /> **설명**: 필수 사항입니다. **Within collection** 내부에서 찾을 개체입니다.|  
|length|배열 또는 문자열 내의 요소 수를 반환합니다. 예를 들어 다음 식은 `3`:  `length('abc')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 컬렉션<br /><br /> **설명**: 필수 사항입니다. 길이를 가져올 컬렉션입니다.|  
|empty|개체, 배열 또는 문자열이 비어 있으면 true를 반환합니다. 예를 들어 다음 식은 `true`를 반환합니다.<br /><br /> `empty('')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 컬렉션<br /><br /> **설명**: 필수 사항입니다. 비어 있는지 확인할 컬렉션입니다.|  
|교집합|전달된 배열 또는 개체 간에 공통의 요소가 있는 단일 배열 또는 개체를 반환합니다. 예를 들어 이 함수는 `[1, 2]`을 반환합니다.<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> 이 함수의 매개 변수는 개체 또는 배열 집합일 수 있습니다(이들의 혼합은 아님). 같은 이름의 개체가 두 개 있는 경우 해당 이름을 가진 마지막 개체가 최종 개체에 나타납니다.<br /><br /> **매개 변수 번호**: 1 ... *n*<br /><br /> **이름**: Collection *n*<br /><br /> **설명**: 필수 사항입니다. 평가할 컬렉션입니다. 개체는 결과에 표시하기 위해 전달된 모든 컬렉션에 있어야 합니다.|  
|union|여기에 전달된 배열 또는 개체에 있는 모든 요소를 포함하는 단일 배열 또는 개체를 반환합니다. 예를 들어 이 함수는 `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`를 반환합니다.<br /><br /> 이 함수의 매개 변수는 개체 또는 배열 집합일 수 있습니다(이들의 혼합은 아님). 최종 출력에 같은 이름의 개체가 두 개 있는 경우 해당 이름을 가진 마지막 개체가 최종 개체에 나타납니다.<br /><br /> **매개 변수 번호**: 1 ... *n*<br /><br /> **이름**: Collection *n*<br /><br /> **설명**: 필수 사항입니다. 평가할 컬렉션입니다. 컬렉션에 나타나는 개체가 결과에 나타납니다.|  
|first|전달된 배열 또는 문자열의 첫 번째 요소를 반환합니다. 예를 들어 이 함수는 `0`을 반환합니다.<br /><br /> `first([0,2,3])`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 컬렉션<br /><br /> **설명**: 필수 사항입니다. 첫 번째 개체를 가져올 컬렉션입니다.|  
|last|전달된 배열 또는 문자열의 마지막 요소를 반환합니다. 예를 들어 이 함수는 `3`을 반환합니다.<br /><br /> `last('0123')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 컬렉션<br /><br /> **설명**: 필수 사항입니다. 마지막 개체를 가져올 컬렉션입니다.|  
|take|전달된 배열 또는 문자열의 첫 번째 **Count** 요소를 반환합니다. 예를 들어 이 함수는 `[1, 2]`:  `take([1, 2, 3, 4], 2)`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 컬렉션<br /><br /> **설명**: 필수 사항입니다. 첫 번째 **Count** 개체를 취할 컬렉션입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 카운트<br /><br /> **설명**: 필수 사항입니다. **Collection**에서 가져올 개체 수입니다. 양의 정수여야 합니다.|  
|skip|인덱스 **Count**에서 시작하는 배열의 요소를 반환합니다. 예를 들어 이 함수는 `[3, 4]`를 반환합니다.<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 컬렉션<br /><br /> **설명**: 필수 사항입니다. 첫 번째 **Count** 개체를 건너뛸 컬렉션입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 카운트<br /><br /> **설명**: 필수 사항입니다. **Collection**의 앞에서 제거할 개체 수입니다. 양의 정수여야 합니다.|  
  
## <a name="logical-functions"></a>논리 함수  
 이 함수는 조건 내에서 유용하며 논리의 형식을 평가하는 데 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|equals|두 값이 같으면 true를 반환합니다. 예를 들어, parameter1이 foo이면 다음 식이 `true`: `equals(pipeline().parameters.parameter1), 'foo')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Object 1<br /><br /> **설명**: 필수 사항입니다. **Object 2**와 비교할 개체입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Object 2<br /><br /> **설명**: 필수 사항입니다. **Object 1**과 비교할 개체입니다.|  
|less|첫 번째 인수가 두 번째 인수보다 작으면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 다음 식은 `true`:  `less(10,100)`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Object 1<br /><br /> **설명**: 필수 사항입니다. **Object 2**보다 작은지 확인할 개체입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Object 2<br /><br /> **설명**: 필수 사항입니다. **Object 1**보다 큰지 확인할 개체입니다.|  
|lessOrEquals|첫 번째 인수가 두 번째 인수보다 작거나 같으면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 다음 식은 `true`:  `lessOrEquals(10,10)`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Object 1<br /><br /> **설명**: 필수 사항입니다. **Object 2**보다 작거나 같은지 확인할 개체입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Object 2<br /><br /> **설명**: 필수 사항입니다. **Object 1**보다 크거나 같은지 확인할 개체입니다.|  
|greater|첫 번째 인수가 두 번째 인수보다 크면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 다음 식은 `false`:  `greater(10,10)`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Object 1<br /><br /> **설명**: 필수 사항입니다. **Object 2**보다 큰지 확인할 개체입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Object 2<br /><br /> **설명**: 필수 사항입니다. **Object 1**보다 작은지 확인할 개체입니다.|  
|greaterOrEquals|첫 번째 인수가 두 번째 인수보다 크거나 같으면 true를 반환합니다. 값은 integer, float 또는 string 형식만 가능합니다. 예를 들어 다음 식은 `false`:  `greaterOrEquals(10,100)`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Object 1<br /><br /> **설명**: 필수 사항입니다. **Object 2**보다 크거나 같은지 확인할 개체입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Object 2<br /><br /> **설명**: 필수 사항입니다. **Object 1**보다 작거나 같은지 확인할 개체입니다.|  
|and|매개 변수가 모두 true이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 다음은 `false`:  `and(greater(1,10),equals(0,0))`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Boolean 1<br /><br /> **설명**: 필수 사항입니다. 첫 번째 인수는 `true`여야 합니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Boolean 2<br /><br /> **설명**: 필수 사항입니다. 두 번째 인수는 `true`여야 합니다.|  
|또는|매개 변수 중 하나가 true이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 다음은 `true`:  `or(greater(1,10),equals(0,0))`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Boolean 1<br /><br /> **설명**: 필수 사항입니다. 첫 번째 인수는 `true`일 수 있습니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Boolean 2<br /><br /> **설명**: 필수 사항입니다. 두 번째 인수는 `true`일 수 있습니다.|  
|not|매개 변수가 `false`이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 다음은 `true`:  `not(contains('200 Success','Fail'))`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Boolean<br /><br /> **설명**: 매개 변수가 `false`이면 true를 반환합니다. 두 인수 모두 부울이어야 합니다. 다음은 `true`:  `not(contains('200 Success','Fail'))`를 반환합니다.|  
|if|제공된 식이 `true` 또는 `false`인지에 따라 지정된 값을 반환합니다.  예를 들어 다음은 `"yes"`: `if(equals(1, 1), 'yes', 'no')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 식<br /><br /> **설명**: 필수 사항입니다. 식에서 어떤 값을 반환할지 결정하는 부울 값입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: True <br /><br /> **설명**: 필수 사항입니다. 식이 `true`인 경우 반환할 값입니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: 거짓<br /><br /> **설명**: 필수 사항입니다. 식이 `false`인 경우 반환할 값입니다.|  
  
## <a name="conversion-functions"></a>변환 함수  
 이 함수는 언어의 각 기본 형식 간에 변환하는 데 사용됩니다.  
  
-   문자열  
  
-   정수  
  
-   float  
  
-   부울  
  
-   arrays  
  
-   dictionaries  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|int|매개 변수를 정수로 변환합니다. 예를 들어 다음 식은 문자열 `int('100')`이 아닌 숫자로 100을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 값<br /><br /> **설명**: 필수 사항입니다. 정수로 변환할 값입니다.|  
|문자열|매개 변수를 문자열로 변환합니다. 예를 들어 다음 식은 `'10'`를 반환합니다.  `string(10)`. 개체를 문자열로 변환할 수도 있습니다. 예를 들어 **foo** 매개 변수가 한 속성 `bar : baz`가 있는 개체이면 다음은 `{"bar" : "baz"}` `string(pipeline().parameters.foo)`을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 값<br /><br /> **설명**: 필수 사항입니다. 문자열로 변환할 값입니다.|  
|json :|매개 변수를 JSON 형식 값으로 변환합니다. String()의 반대입니다. 예를 들어 다음 식은 문자열이 아닌 배열로 `[1,2,3]`를 반환합니다.<br /><br /> `json('[1,2,3]')`<br /><br /> 마찬가지로, 문자열을 개체로 변환할 수 있습니다. 예를 들어 `json('{"bar" : "baz"}')`는 다음을 반환합니다.<br /><br /> `{ "bar" : "baz" }`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 기본 형식 값으로 변환할 문자열입니다.<br /><br /> JSON 함수는 XML 입력도 지원합니다. 예를 들어 다음 매개 변수 값은<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> 다음 json으로 변환됩니다.<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|float|매개 변수 인수를 부동 소수점 숫자로 변환합니다. 예를 들어 다음 식은 `10.333`:  `float('10.333')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 값<br /><br /> **설명**: 필수 사항입니다. 부동 소수점 숫자로 변환할 값입니다.|  
|bool|매개 변수를 부울로 변환합니다. 예를 들어 다음 식은 `false`:  `bool(0)`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 값<br /><br /> **설명**: 필수 사항입니다. 부울로 변환할 값입니다.|  
|coalesce|전달된 인수에서 첫 번째 null이 아닌 개체를 반환합니다. 참고: 빈 문자열은 null이 아닙니다. 예를 들어 매개 변수 1 및 2가 정의되지 않은 경우 이 함수는 `fallback`:  `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`을 반환합니다.<br /><br /> **매개 변수 번호**: 1 ... *n*<br /><br /> **이름**: Object*n*<br /><br /> **설명**: 필수 사항입니다. `null`에 대해 검사할 개체입니다.|  
|base64|입력 문자열의 base64 표현을 반환합니다. 예를 들어 다음 식은 `c29tZSBzdHJpbmc=`:  `base64('some string')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String 1<br /><br /> **설명**: 필수 사항입니다. base64 표현으로 인코딩할 문자열입니다.|  
|base64ToBinary|base64 인코딩 문자열의 이진 표현을 반환합니다. 예를 들어 다음 식 `base64ToBinary('c29tZSBzdHJpbmc=')`은 일부 문자열의 이진 표현을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. Base64 인코딩된 문자열입니다.|  
|base64ToString|based64 인코딩 문자열의 문자열 표현을 반환합니다. 예를 들어 다음 식은 일부 문자열 `base64ToString('c29tZSBzdHJpbmc=')` 를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. Base64 인코딩된 문자열입니다.|  
|이진|값의 이진 표현을 반환합니다.  예를 들어 다음 식은 일부 문자열의 이진 표현 `binary(‘some string’).`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 값<br /><br /> **설명**: 필수 사항입니다. 이진으로 변환할 값입니다.|  
|dataUriToBinary|데이터 URI의 이진 표현을 반환합니다. 예를 들어 다음 식은 일부 문자열의 이진 표현 `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 이진 표현으로 변환할 데이터 URI입니다.|  
|dataUriToString|데이터 URI의 문자열 표현을 반환합니다. 예를 들어 다음 식은 일부 문자열 `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br />**설명**: 필수 사항입니다. 문자열 표현으로 변환할 데이터 URI입니다.|  
|dataUri|값의 데이터 URI를 반환합니다. 예를 들어 다음 `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')` 식은 데이터를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br />**이름**: 값<br /><br />**설명**: 필수 사항입니다. 데이터 URI로 변환할 값입니다.|  
|decodeBase64|입력 based64 문자열의 문자열 표현을 반환합니다. 예를 들어 다음 식은 `some string`:  `decodeBase64('c29tZSBzdHJpbmc=')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 입력 based64 문자열의 문자열 표현을 반환합니다.|  
|encodeUriComponent|전달된 문자열을 URL-이스케이프합니다. 예를 들어 다음 식은 `You+Are%3ACool%2FAwesome`:  `encodeUriComponent('You Are:Cool/Awesome')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. URL 안전하지 않은 문자 양식을 이스케이프할 문자열입니다.|  
|decodeUriComponent|전달된 문자열을 URL-이스케이프하지 않습니다. 예를 들어 다음 식은 `You Are:Cool/Awesome`:  `encodeUriComponent('You+Are%3ACool%2FAwesome')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. URL 안전하지 않은 문자를 디코딩할 문자열입니다.|  
|decodeDataUri|입력 데이터 URI 문자열의 이진 표현을 반환합니다. 예를 들어 다음 식은 `some string`:  `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`의 이진 표현을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br /> **설명**: 필수 사항입니다. 이진 표현으로 디코딩할 데이터 URI입니다.|  
|uriComponent|값의 URI 인코딩 표현을 반환합니다. 예를 들어 다음 식은 `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`를 반환합니다.<br /><br /> 매개 변수 세부 정보: 번호: 1, 이름: String, 설명: 필수 사항입니다. URI 인코딩할 문자열입니다.|  
|uriComponentToBinary|URI 인코딩 문자열의 이진 표현을 반환합니다. 예를 들어 다음 식은 `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`의 이진 표현을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: String<br /><br />**설명**: 필수 사항입니다. URI 인코딩된 문자열입니다.|  
|uriComponentToString|URI 인코딩 문자열의 문자열 표현을 반환합니다. 예를 들어 다음 식은 `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br />**이름**: String<br /><br />**설명**: 필수 사항입니다. URI 인코딩된 문자열입니다.|  
|Xml|값의 XML 표현을 반환합니다. 예를 들어 다음 식은 `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`으로 표현되는 xml 콘텐츠를 반환합니다. xml 함수는 JSON 개체 입력도 지원합니다. 예를 들어 `{ "abc": "xyz" }` 매개 변수는 XML 콘텐츠 `\<abc>xyz\</abc>`로 변환됩니다. <br /><br /> **매개 변수 번호**: 1<br /><br />**이름**: 값<br /><br />**설명**: 필수 사항입니다. XML로 변환할 값입니다.|  
|xpath|xpath 식을 평가할 값의 xpath 식과 일치하는 XML 노드 배열을 반환합니다.<br /><br />  **예 1**<br /><br /> 매개 변수 ‘p1’ 값이 다음 XML의 문자열 표현이라고 가정합니다.<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. 다음 코드는 `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> 다음을 반환합니다.<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> 반면<br /><br /> 2. 다음 코드는 `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> 다음을 반환합니다.<br /><br /> `13`<br /><br /> <br /><br /> **예 2**<br /><br /> 다음 XML 콘텐츠를 가정한다면:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  다음 코드는 `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> 또는<br /><br /> 2. 다음 코드는 `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> 다음을 반환합니다.<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> and<br /><br /> 3. 다음 코드는 `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> 다음을 반환합니다.<br /><br /> ``bar``<br /><br /> **매개 변수 번호**: 1<br /><br />**이름**: Xml<br /><br />**설명**: 필수 사항입니다. XPath 식을 평가할 XML입니다.<br /><br /> **매개 변수 번호**: 2<br /><br />**이름**: XPath<br /><br />**설명**: 필수 사항입니다. 평가할 XPath 식입니다.|  
|array|매개 변수를 배열로 변환합니다.  예를 들어 다음 식은 `["abc"]`: `array('abc')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 값<br /><br /> **설명**: 필수 사항입니다. 배열로 변환할 값입니다.|
|createArray|매개 변수에서 배열을 만듭니다.  예를 들어 다음 식은 `["a", "c"]`: `createArray('a', 'c')`를 반환합니다.<br /><br /> **매개 변수 번호**: 1 ... n<br /><br /> **이름**: Any n<br /><br /> **설명**: 필수 사항입니다. 배열로 결합할 값입니다.|

## <a name="math-functions"></a>수학 함수  
 이 함수는 **integers** 및 **floats**의 숫자 형식에 사용할 수 있습니다.  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|추가|두 숫자를 합한 결과를 반환합니다. 예를 들어 이 함수는 `20.333`:  `add(10,10.333)`을 반환합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Summand 1<br /><br /> **설명**: 필수 사항입니다. **Summand 2**에 더할 숫자입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Summand 2<br /><br /> **설명**: 필수 사항입니다. **Summand 1**에 더할 숫자입니다.|  
|sub|두 숫자를 차감한 결과를 반환합니다. 예를 들어 이 함수는 `-0.333`을 반환합니다.<br /><br /> `sub(10,10.333)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Minuend<br /><br /> **설명**: 필수 사항입니다. **Subtrahend**를 뺄 숫자입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Subtrahend<br /><br /> **설명**: 필수 사항입니다. **Minuend**에서 뺄 숫자입니다.|  
|mul|두 숫자를 곱한 결과를 반환합니다. 예를 들어 다음은 `103.33`을 반환합니다.<br /><br /> `mul(10,10.333)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Multiplicand 1<br /><br /> **설명**: 필수 사항입니다. **Multiplicand 2**를 곱할 숫자입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Multiplicand 2<br /><br /> **설명**: 필수 사항입니다. **Multiplicand 1**을 곱할 숫자입니다.|  
|div|두 숫자를 나눈 결과를 반환합니다. 예를 들어 다음은 `1.0333`을 반환합니다.<br /><br /> `div(10.333,10)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Dividend<br /><br /> **설명**: 필수 사항입니다. **Divisor**로 나눌 숫자입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Divisor<br /><br /> **설명**: 필수 사항입니다. **Dividend**로 나누어지는 숫자입니다.|  
|mod|두 숫자를 나눈 후 나머지 값을 반환합니다(modulo). 예를 들어 다음 식은 `2`를 반환합니다.<br /><br /> `mod(10,4)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Dividend<br /><br /> **설명**: 필수 사항입니다. **Divisor**로 나눌 숫자입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: Divisor<br /><br /> **설명**: 필수 사항입니다. **Dividend**로 나누어지는 숫자입니다. 나눈 후 나머지를 가져옵니다.|  
|min|이 함수를 호출하는 데는 두 가지 다른 패턴이 있습니다. `min([0,1,2])` 여기서는 min이 배열을 사용합니다. 이 식은 `0`를 반환합니다. 또는 이 함수가 쉼표로 구분된 값 목록  `min(0,1,2)`을 사용할 수도 있습니다. 또한 이 함수는 0을 반환합니다. 모든 값은 숫자여야 하므로 매개 변수가 배열이면 배열에 숫자만 포함되어야 합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Collection 또는 Value<br /><br /> **설명**: 필수 사항입니다. 최소값을 찾을 값 배열이거나 집합의 첫 번째 값입니다.<br /><br /> **매개 변수 번호**: 2 ... *n*<br /><br /> **이름**: Value *n*<br /><br /> **설명**: 선택 사항입니다. 첫 번째 매개 변수가 Value인 경우 추가 값을 전달할 수 있으며 전달된 모든 값의 최소값이 반환됩니다.|  
|max|이 함수를 호출하는 데는 두 가지 다른 패턴이 있습니다. `max([0,1,2])`:<br /><br /> 여기서는 max가 배열을 취합니다. 이 식은 `2`를 반환합니다. 또는 이 함수가 쉼표로 구분된 값 목록  `max(0,1,2)`를 사용할 수도 있습니다. 이 함수는 2를 반환합니다. 모든 값은 숫자여야 하므로 매개 변수가 배열이면 배열에 숫자만 포함되어야 합니다.<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Collection 또는 Value<br /><br /> **설명**: 필수 사항입니다. 최대값을 찾을 값 배열이거나 집합의 첫 번째 값입니다.<br /><br /> **매개 변수 번호**: 2 ... *n*<br /><br /> **이름**: Value *n*<br /><br /> **설명**: 선택 사항입니다. 첫 번째 매개 변수가 Value인 경우 추가 값을 전달할 수 있으며 전달된 모든 값의 최대값이 반환됩니다.|  
|range| 특정 번호로 시작하는 정수 배열을 생성합니다. 반환된 배열의 길이는 사용자가 정의합니다. 예를 들어 이 함수는 `[3,4,5,6]`을 반환합니다.<br /><br /> `range(3,4)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Start index<br /><br /> **설명**: 필수 사항입니다. 배열의 첫 번째 정수입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 카운트<br /><br /> **설명**: 필수 사항입니다. 배열에 있는 정수의 수입니다.|  
|rand| 지정된 범위 내에서 정수를 임의로 생성합니다(양쪽 끝 포함). 예를 들어 여기서는 `42`를 반환합니다.<br /><br /> `rand(-1000,1000)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 최소<br /><br /> **설명**: 필수 사항입니다. 반환 가능한 가장 작은 정수입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 최대<br /><br /> **설명**: 필수 사항입니다. 반환 가능한 가장 큰 정수입니다.|  
  
## <a name="date-functions"></a>날짜 함수  
  
|함수 이름|설명|  
|-------------------|-----------------|  
|utcnow|현재 타임스탬프를 문자열로 반환합니다. 예 `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|addseconds|전달된 문자열 타임스탬프에 시간(초)에 대한 정수를 더합니다. 시간(초) 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예 `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 타임 스탬프<br /><br /> **설명**: 필수 사항입니다. 시간을 포함하는 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 초<br /><br /> **설명**: 필수 사항입니다. 추가할 시간(초) 수입니다. 시간(초)을 빼기 위한 음수일 수 있습니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|addminutes|전달된 문자열 타임스탬프에 시간(분)에 대한 정수를 더합니다. 시간(분) 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예 `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 타임 스탬프<br /><br /> **설명**: 필수 사항입니다. 시간을 포함하는 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 분<br /><br /> **설명**: 필수 사항입니다. 더할 시간(분) 수입니다. 시간(분)을 빼기 위한 음수일 수 있습니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|addhours|전달된 문자열 타임스탬프에 시간(시)에 대한 정수를 더합니다. 시간(시) 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예 `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 타임 스탬프<br /><br /> **설명**: 필수 사항입니다. 시간을 포함하는 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 시간<br /><br /> **설명**: 필수 사항입니다. 더할 시간 수입니다. 시간(시)을 빼기 위한 음수일 수 있습니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|adddays|전달된 문자열 타임스탬프에 날 수에 대한 정수를 더합니다. 날 수는 양수 또는 음수일 수 있습니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예 `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: 타임 스탬프<br /><br /> **설명**: 필수 사항입니다. 시간을 포함하는 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 일<br /><br /> **설명**: 필수 사항입니다. 더할 날 수입니다. 날 수를 빼기 위한 음수일 수 있습니다.<br /><br /> **매개 변수 번호**: 3<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  
|formatDateTime|날짜 형식으로 문자열을 반환합니다. 기본적으로 결과는 형식 지정자를 제공하지 않은 경우 ISO 8601 형식("o")의 문자열입니다. 예 `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **매개 변수 번호**: 1<br /><br /> **이름**: Date<br /><br /> **설명**: 필수 사항입니다. 날짜를 포함하는 문자열입니다.<br /><br /> **매개 변수 번호**: 2<br /><br /> **이름**: 형식<br /><br /> **설명**: 선택 사항입니다. 이 타임스탬프 값의 형식을 지정하는 방법을 나타내는 [단일 형식 지정자 문자](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) 또는 [사용자 지정 형식 패턴](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)입니다. 형식이 제공되지 않으면 ISO 8601 형식("o")이 사용됩니다.|  

## <a name="next-steps"></a>다음 단계
식에 사용할 수 있는 시스템 변수 목록은 [시스템 변수](control-flow-system-variables.md)를 참조하세요.
