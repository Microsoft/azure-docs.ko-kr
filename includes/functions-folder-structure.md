---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
origin.date: 09/12/2018
ms.date: 10/19/2018
ms.author: v-junlch
ms.custom: include file
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731189"
---
특정 함수 앱의 모든 기능에 대한 코드는 호스트 구성 파일 및 하나 이상의 하위 폴더를 포함하는 루트 프로젝트 폴더에 있습니다. 각 하위 폴더에는 다음 예제와 같이 개별 함수에 대한 코드가 포함됩니다.

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Functions 런타임의 2.x 버전부터 함수 앱의 모든 함수는 동일한 언어 작업 프로그램을 공유해야 합니다.  

일부 런타임별 구성을 포함하는 [host.json](../articles/azure-functions/functions-host-json.md) 파일은 함수 앱의 루트 폴더에 있습니다. `bin` 폴더에는 함수 앱에 필요한 패키지 및 기타 라이브러리 파일이 포함되어 있습니다. 함수 앱 프로젝트에 대한 언어별 요구 사항을 참조하세요.

- [C# 클래스 라이브러리(.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
- [C# 스크립트(.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
- [F# 스크립트](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
- [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
- [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)


<!-- ms.date: 10/19/2018 -->

