---
title: Resource Manager 템플릿 함수 | Microsoft Docs
description: Azure Resource Manager 템플릿에서 값을 검색하고 문자열과 숫자로 작업하며 배포 정보를 검색하는 데 사용하는 함수를 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 0644abe1-abaa-443d-820d-1966d7d26bfd
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: b5a1f12a877008a3ce2ff7bd9635b9ed47b379f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782245"
---
# <a name="azure-resource-manager-template-functions"></a>Azure Resource Manager 템플릿 함수
이 아티클에서는 Azure Resource Manager 템플릿에서 사용할 수 있는 모든 함수에 대해 설명합니다. 서식 파일에서 함수를 사용 하는 방법에 대 한 내용은 [템플릿 구문](resource-group-authoring-templates.md#syntax)합니다.

함수를 직접 만들려면 [사용자 정의 함수](resource-group-authoring-templates.md#functions)를 참조하세요.

<a id="array" />
<a id="coalesce" />
<a id="concatarray" />
<a id="contains" />
<a id="createarray" />
<a id="empty" />
<a id="first" />
<a id="intersection" />
<a id="json" />
<a id="last" />
<a id="length" />
<a id="min" />
<a id="max" />
<a id="range" />
<a id="skip" />
<a id="take" />
<a id="union" />

## <a name="array-and-object-functions"></a>배열 및 개체 함수
Resource Manager는 배열 및 개체 작업을 위한 여러 함수를 제공합니다.

* [array](resource-group-template-functions-array.md#array)
* [coalesce](resource-group-template-functions-array.md#coalesce)
* [concat](resource-group-template-functions-array.md#concat)
* [contains](resource-group-template-functions-array.md#contains)
* [createArray](resource-group-template-functions-array.md#createarray)
* [empty](resource-group-template-functions-array.md#empty)
* [first](resource-group-template-functions-array.md#first)
* [intersection](resource-group-template-functions-array.md#intersection)
* [json](resource-group-template-functions-array.md#json)
* [last](resource-group-template-functions-array.md#last)
* [length](resource-group-template-functions-array.md#length)
* [min](resource-group-template-functions-array.md#min)
* [max](resource-group-template-functions-array.md#max)
* [range](resource-group-template-functions-array.md#range)
* [skip](resource-group-template-functions-array.md#skip)
* [take](resource-group-template-functions-array.md#take)
* [union](resource-group-template-functions-array.md#union)

<a id="equals" />
<a id="less" />
<a id="lessorequals" />
<a id="greater" />
<a id="greaterorequals" />

## <a name="comparison-functions"></a>비교 함수
Resource Manager는 템플릿에서 비교를 수행하기 위한 몇 가지 함수를 제공합니다.

* [equals](resource-group-template-functions-comparison.md#equals)
* [less](resource-group-template-functions-comparison.md#less)
* [lessOrEquals](resource-group-template-functions-comparison.md#lessorequals)
* [greater](resource-group-template-functions-comparison.md#greater)
* [greaterOrEquals](resource-group-template-functions-comparison.md#greaterorequals)

<a id="deployment" />
<a id="parameters" />
<a id="variables" />

## <a name="deployment-value-functions"></a>배포 값 함수
Resource Manager는 템플릿의 섹션에서 값을 가져오고 배포와 관련된 값을 가져오기 위한 다음 함수를 제공합니다.

* [deployment](resource-group-template-functions-deployment.md#deployment)
* [매개 변수](resource-group-template-functions-deployment.md#parameters)
* [variables](resource-group-template-functions-deployment.md#variables)

<a id="and" />
<a id="bool" />
<a id="if" />
<a id="not" />
<a id="or" />

## <a name="logical-functions"></a>논리 함수
Resource Manager는 논리 조건 사용을 위한 다음 함수를 제공합니다.

* [and](resource-group-template-functions-logical.md#and)
* [bool](resource-group-template-functions-logical.md#bool)
* [if](resource-group-template-functions-logical.md#if)
* [not](resource-group-template-functions-logical.md#not)
* [or](resource-group-template-functions-logical.md#or)

<a id="add" />
<a id="copyindex" />
<a id="div" />
<a id="float" />
<a id="int" />
<a id="minint" />
<a id="maxint" />
<a id="mod" />
<a id="mul" />
<a id="sub" />

## <a name="numeric-functions"></a>숫자 함수
Resource Manager는 정수 작업을 위한 다음 함수를 제공합니다.

* [추가](resource-group-template-functions-numeric.md#add)
* [copyIndex](resource-group-template-functions-numeric.md#copyindex)
* [div](resource-group-template-functions-numeric.md#div)
* [float](resource-group-template-functions-numeric.md#float)
* [int](resource-group-template-functions-numeric.md#int)
* [min](resource-group-template-functions-numeric.md#min)
* [max](resource-group-template-functions-numeric.md#max)
* [mod](resource-group-template-functions-numeric.md#mod)
* [mul](resource-group-template-functions-numeric.md#mul)
* [sub](resource-group-template-functions-numeric.md#sub)

<a id="listkeys" />
<a id="list" />
<a id="providers" />
<a id="reference" />
<a id="resourcegroup" />
<a id="resourceid" />
<a id="subscription" />

## <a name="resource-functions"></a>리소스 함수
Resource Manager는 리소스 값을 가져오기 위한 다음 함수를 제공합니다.

* [listAccountSas](resource-group-template-functions-resource.md#list)
* [listKeys](resource-group-template-functions-resource.md#listkeys)
* [listSecrets](resource-group-template-functions-resource.md#list)
* [list*](resource-group-template-functions-resource.md#list)
* [providers](resource-group-template-functions-resource.md#providers)
* [reference](resource-group-template-functions-resource.md#reference)
* [resourceGroup](resource-group-template-functions-resource.md#resourcegroup)
* [resourceId](resource-group-template-functions-resource.md#resourceid)
* [subscription](resource-group-template-functions-resource.md#subscription)

<a id="base64" />
<a id="base64tojson" />
<a id="base64tostring" />
<a id="concat" />
<a id="containsstring" />
<a id="datauri" />
<a id="datauritostring" />
<a id="emptystring" />
<a id="endswith" />
<a id="firststring" />
<a id="guid" />
<a id="indexof" />
<a id="laststring" />
<a id="lastindexof" />
<a id="lengthstring" />
<a id="padleft" />
<a id="replace" />
<a id="skipstring" />
<a id="split" />
<a id="startswith" />
<a id="string" />
<a id="substring" />
<a id="takestring" />
<a id="tolower" />
<a id="toupper" />
<a id="trim" />
<a id="uniquestring" />
<a id="uri" />
<a id="uricomponent" />
<a id="uricomponenttostring" />

## <a name="string-functions"></a>문자열 함수
Resource Manager는 문자열 작업을 위한 다음 함수를 제공합니다.

* [base64](resource-group-template-functions-string.md#base64)
* [base64ToJson](resource-group-template-functions-string.md#base64tojson)
* [base64ToString](resource-group-template-functions-string.md#base64tostring)
* [concat](resource-group-template-functions-string.md#concat)
* [contains](resource-group-template-functions-string.md#contains)
* [dataUri](resource-group-template-functions-string.md#datauri)
* [dataUriToString](resource-group-template-functions-string.md#datauritostring)
* [empty](resource-group-template-functions-string.md#empty)
* [endsWith](resource-group-template-functions-string.md#endswith)
* [first](resource-group-template-functions-string.md#first)
* [format](resource-group-template-functions-string.md#format)
* [guid](resource-group-template-functions-string.md#guid)
* [indexOf](resource-group-template-functions-string.md#indexof)
* [last](resource-group-template-functions-string.md#last)
* [lastIndexOf](resource-group-template-functions-string.md#lastindexof)
* [length](resource-group-template-functions-string.md#length)
* [newGuid](resource-group-template-functions-string.md#newguid)
* [padLeft](resource-group-template-functions-string.md#padleft)
* [replace](resource-group-template-functions-string.md#replace)
* [skip](resource-group-template-functions-string.md#skip)
* [split](resource-group-template-functions-string.md#split)
* [startsWith](resource-group-template-functions-string.md#startswith)
* [string](resource-group-template-functions-string.md#string)
* [substring](resource-group-template-functions-string.md#substring)
* [take](resource-group-template-functions-string.md#take)
* [toLower](resource-group-template-functions-string.md#tolower)
* [toUpper](resource-group-template-functions-string.md#toupper)
* [trim](resource-group-template-functions-string.md#trim)
* [uniqueString](resource-group-template-functions-string.md#uniquestring)
* [uri](resource-group-template-functions-string.md#uri)
* [uriComponent](resource-group-template-functions-string.md#uricomponent)
* [uriComponentToString](resource-group-template-functions-string.md#uricomponenttostring)
* [utcNow](resource-group-template-functions-string.md#utcnow)

## <a name="next-steps"></a>다음 단계
* Azure Resource Manager 템플릿의 섹션에 대한 설명은 [Azure Resource Manager 템플릿 작성](resource-group-authoring-templates.md)
* 여러 템플릿을 병합하려면 [Azure Resource Manager에서 연결된 템플릿 사용](resource-group-linked-templates.md)
* 리소스 유형을 만들 때 지정된 횟수만큼 반복하려면 [Azure Resource Manager에서 리소스의 여러 인스턴스 만들기](resource-group-create-multiple.md)
* 사용자가 만든 템플릿을 배포 하는 방법을 참조 하세요 [Azure Resource Manager 템플릿 사용 하 여 응용 프로그램 배포](resource-group-template-deploy.md)
