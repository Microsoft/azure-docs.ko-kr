---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129077"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

`extensionBundle`에서 사용할 수 있는 속성은 다음과 같습니다.

| 속성 | 설명 |
| -------- | ----------- |
| id | Microsoft Azure 함수 확장 번들에 대한 네임 스페이스입니다. |
| 버전 | 설치할 번들의 버전입니다. 함수 런타임은 항상 버전 범위 또는 간격으로 정의 된 허용 가능한 최대 버전을 선택 합니다. 위의 version 값은 1.0.0의 모든 번들 버전을 2.0.0 포함 하는 것을 허용 하지 않습니다. 자세한 내용은 [버전 범위 지정을 위한 간격 표기법](/nuget/reference/package-versioning#version-ranges-and-wildcards)을 참조 하십시오. |

번들 버전은 번들 변경에서 패키지로 증가 합니다. 번들의 패키지가 주 버전에 따라 증가 하는 경우 주 버전 변경이 발생 합니다. 번들의 주 버전 변경 내용은 일반적으로 함수 런타임의 주 버전 변경 내용과 일치 합니다.  

기본 번들로 설치 된 확장의 현재 집합은이 [확장명 json 파일](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json)에 열거 됩니다.
