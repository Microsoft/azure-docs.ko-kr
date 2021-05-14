---
title: Create UI definition 문자열 함수
description: Azure Managed Applications용 UI 정의를 생성하는 경우 사용할 문자열 함수에 대해 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098159"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition 문자열 함수

JSON 문자열과 함께 사용할 함수입니다.

## <a name="concat"></a>concat

하나 이상의 문자열을 연결합니다.

예를 들어 `element1`의 출력 값이 `"Contoso"`인 경우 이 예제에서는 `"Demo Contoso app"` 문자열을 반환합니다.

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

문자열이 값으로 끝나는지 여부를 결정합니다.

다음 샘플에서는 true를 반환합니다.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

전역적으로 고유한 문자열(GUID)을 생성합니다.

다음 예제에서는 `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` 같은 값을 반환합니다.

```json
"[guid()]"
```

## <a name="indexof"></a>indexof

문자열 내 값의 첫 번째 위치를 반환하거나 찾지 못하는 경우 -1을 반환합니다.

다음 샘플에서는 2를 반환합니다.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastindexof

문자열 내 값의 마지막 위치를 반환하거나 찾지 못하는 경우 -1을 반환합니다.

다음 샘플에서는 3을 반환합니다.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>replace

현재 문자열에서 지정된 문자열의 모든 항목이 다른 문자열로 바뀐 문자열을 반환합니다.

다음 예제는 `"Contoso.com web app"`을 반환합니다.

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startswith

문자열이 값으로 시작하는지 여부를 결정합니다.

다음 샘플에서는 true를 반환합니다.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

지정된 문자열의 부분 문자열을 반환합니다. 부분 문자열은 지정된 인덱스에서 시작하고 지정된 길이를 갖습니다.

다음 예제는 `"web"`을 반환합니다.

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

소문자로 변환된 문자열을 반환합니다.

다음 예제는 `"contoso"`을 반환합니다.

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

대문자로 변환된 문자열을 반환합니다.

다음 예제는 `"CONTOSO"`을 반환합니다.

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>다음 단계

* Azure Resource Manager에 대한 소개는 [Azure Resource Manager 개요](../management/overview.md)를 참조하세요.

