---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/25/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 94cac0932da5880e5e7b8a8fac3870b5bc464af9
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564822"
---
## <a name="register-extensions"></a>확장 등록

HTTP 및 타이머 트리거를 제외 하 고 런타임 버전 2.x 이상에서 함수 바인딩은 확장 패키지로 구현 됩니다. 버전 2.x 및 Azure Functions 런타임 이외에 함수에 사용 되는 바인딩 형식에 대한 확장을 명시적으로 등록 해야 합니다. 이에 대한 예외는 확장을 필요로 하지 않는 HTTP 바인딩 및 타이머 트리거입니다.

바인딩 확장을 개별적으로 설치 하도록 선택 하거나, 호스트 json 프로젝트 파일에 확장 번들 참조를 추가할 수 있습니다. 확장 번들은 여러 바인딩 유형을 사용 하는 경우 패키지 호환성 문제가 발생할 가능성을 제거 합니다. 바인딩 확장을 등록 하는 데 권장 되는 방법입니다. 또한 확장 번들은 .NET Core 2.x SDK를 설치 하는 요구 사항을 제거 합니다. 

### <a name="extension-bundles"></a>확장 번들

[!INCLUDE [Register extensions](functions-extension-bundles.md)]

자세히 알아보려면 [Azure Functions 바인딩 확장 등록](../articles/azure-functions/functions-bindings-register.md#extension-bundles)을 참조 하세요. 함수. json 파일에 바인딩을 추가 하려면 먼저 확장 번들을 호스트에 추가 해야 합니다.

### <a name="register-individual-extensions"></a>개별 확장 등록

번들에 없는 확장을 설치 해야 하는 경우 특정 바인딩에 대해 개별 확장 패키지를 수동으로 등록할 수 있습니다. 

> [!NOTE]
> `func extensions install`를 사용 하 여 확장을 수동으로 등록 하려면 .NET Core 2.x SDK가 설치 되어 있어야 합니다.

함수에 필요한 모든 바인딩을 포함하도록 *function.json* 파일을 업데이트한 후에 프로젝트 폴더에서 다음 명령을 실행합니다.

```bash
func extensions install
```

이 명령은 *function.json* 파일을 읽어 필요한 패키지를 확인하고 설치한 후 확장 프로젝트를 다시 빌드합니다. 현재 버전에서 새 바인딩을 추가하되, 기존 바인딩을 업데이트하지는 않습니다. 새 바인딩을 설치할 때 기존 바인딩을 최신 버전으로 업데이트하려면 `--force` 옵션을 사용합니다.
