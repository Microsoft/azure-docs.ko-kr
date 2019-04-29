---
title: Azure Functions 바인딩 확장 수동 설치 또는 업데이트
description: 배포된 함수 앱용으로 Azure Functions 바인딩 확장을 설치하거나 업데이트하는 방법에 대해 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure 함수, 함수, 바인딩 확장, 업데이트, azure functions, functions, binding extensions, NuGet, updates
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: cda977ba59070c3ddaac05784277d6c0b5109f0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035745"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>포털에서 Azure Functions 바인딩 확장 수동 설치 또는 업데이트

Azure Functions 버전 2.x 런타임은 바인딩 확장을 사용하여 트리거 및 바인딩용 코드를 구현합니다. 바인딩 확장은 NuGet 패키지에서 제공됩니다. 확장을 등록하려면 패키지를 설치합니다. 함수를 개발할 때 바인딩 확장을 설치하는 방식은 개발 환경에 따라 달라집니다. 자세한 내용은 트리거 및 바인딩 문서의 [바인딩 확장 등록](./functions-bindings-register.md)을 참조하세요.

Azure Portal에서 바인딩 확장을 수동으로 설치하거나 업데이트해야 하는 경우도 있습니다. 예를 들어 바인딩 확장을 최신 버전으로 업데이트해야 할 수도 있고, 포털의 **통합** 탭에서 설치할 수 없는 지원되는 바인딩을 등록해야 할 수도 있습니다.

## <a name="install-a-binding-extension"></a>바인딩 확장 설치

포털에서 바인딩 확장을 수동으로 설치하거나 업데이트하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱을 찾아서 선택합니다. **개요** 탭을 선택하고 **중지**를 선택합니다.  함수 앱을 중지하면 변경 내용을 적용할 수 있도록 파일 잠금이 해제됩니다.

1. **플랫폼 기능** 탭을 선택하고 **개발 도구**에서 **고급 도구(Kudu)** 를 선택합니다. Kudu 엔드포인트(`https://<APP_NAME>.scm.azurewebsites.net/`)가 새 창에서 열립니다.

1. Kudu 창에서 **디버그 콘솔** > **CMD**를 선택합니다.  

1. 명령 창에서 `D:\home\site\wwwroot`로 이동한 다음 `bin` 옆의 삭제 아이콘을 선택하여 폴더를 삭제합니다. **확인**을 선택하여 삭제를 확인합니다.

1. 함수 앱의 바인딩 확장을 정의하는 `extensions.csproj` 파일 옆의 편집 아이콘을 선택합니다. 프로젝트 파일이 온라인 편집기에서 열립니다.

1. **ItemGroup**에서 **PackageReference** 항목을 필요한 대로 추가 및 업데이트하고 **저장**을 선택합니다. 지원되는 패키지 버전의 현재 목록은 [필요한 패키지](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki 문서에서 확인할 수 있습니다. 3개 Azure Storage 바인딩에는 모두 Microsoft.Azure.WebJobs.Extensions.Storage 패키지가 필요합니다.

1. `wwwroot` 폴더에서 다음 명령을 실행하여 `bin` 폴더의 참조된 어셈블리를 다시 빌드합니다.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. 포털의 **개요** 탭으로 돌아와서 **시작**을 선택해 함수 앱을 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
