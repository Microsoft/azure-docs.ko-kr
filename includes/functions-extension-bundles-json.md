---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689564"
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

| 속성 | Description |
| -------- | ----------- |
| id | Microsoft Azure Functions 확장 번들에 대한 네임스페이스입니다. |
| 버전 | 설치할 번들의 버전입니다. Functions 런타임은 항상 버전 범위 또는 간격으로 정의된 허용 가능한 최대 버전을 선택합니다. 위의 version 값은 1.0.0에서 2.0.0 사이의 모든 번들 버전을 허용하지만 2.0.0은 이 범위에 포함되지 않습니다. 자세한 내용은 [버전 범위를 지정하기 위한 간격 표기법](/nuget/reference/package-versioning#version-ranges)을 참조하세요. |