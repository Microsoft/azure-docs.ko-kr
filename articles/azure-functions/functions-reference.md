---
title: Azure Functions 개발 지침 | Microsoft Docs
description: 프로그래밍 언어 및 바인딩에 관계 없이 Azure에서 함수를 개발하는 데 필요한 Azure Functions 개념 및 기술에 대해 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: 개발자 가이드, Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버리스 아키텍처
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 5b2b7f3cd6bfa219b794edc63d6bf8b2784b713c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120741"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions 개발자 가이드
Azure Functions에서 특정 함수는 사용하는 언어나 바인딩에 관계없이 몇 가지 핵심적 기술 개념과 구성 요소를 공유합니다. 특정 언어나 바인딩에 해당하는 세부 정보를 학습하기 전에, 모든 항목에 해당하는 이 개요를 꼼꼼히 읽어 보시기 바랍니다.

이 문서에서는 [Azure Functions 개요](functions-overview.md)를 이미 읽었다고 가정합니다.

## <a name="function-code"></a>함수 코드
*함수* 는 Azure Functions의 기본 개념입니다. 함수에는 다양한 언어로 작성할 수 있는 코드와 일부 구성인 function.json 파일의 두 가지 중요한 부분이 포함되어 있습니다. 컴파일된 언어의 경우, 이 구성 파일은 코드의 주석에서 자동으로 생성됩니다. 스크립팅 언어의 경우 구성 파일을 직접 제공해야 합니다.

function.json 파일은 함수의 트리거, 바인딩 및 기타 구성 설정을 정의합니다. 모든 함수에 하나의 트리거만 있습니다. 런타임은 이 구성 파일을 사용하여 모니터링할 이벤트와 함수 실행에 데이터를 전달하고 반환하는 방법을 확인합니다. 다음은 function.json 파일의 예제입니다.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

`bindings` 속성은 트리거와 바인딩을 모두 구성하는 곳에 위치합니다. 각 바인딩은 몇 가지 공통적인 설정과 특정한 바인딩 형식에 해당하는 일부 설정을 공유합니다. 모든 바인딩에는 다음 설정이 필요합니다.

| 자산 | 값/형식 | 설명 |
| --- | --- | --- |
| `type` |문자열 |바인딩 형식 예: `queueTrigger` |
| `direction` |'in', 'out' |함수 안으로 데이터를 수신할 바인딩인지 또는 함수의 데이터를 전송할 바인딩인지를 나타냅니다. |
| `name` |문자열 |함수에서 바인딩 데이터에 사용되는 이름입니다. C#의 경우 인수 이름이며, JavaScript의 경우 키/값 목록의 키입니다. |

## <a name="function-app"></a>함수 앱
함수 앱은 함수가 실행되는 Azure의 실행 컨텍스트를 제공합니다. Function App은 함께 관리, 배포 및 크기 조정되는 하나 이상의 개별 함수로 구성됩니다. 함수 앱의 모든 함수는 동일한 가격 책정 계획, 연속 배포 및 런타임 버전을 공유합니다. 함수 앱을 함수를 구성하고 전체적으로 관리하는 방법으로 생각합니다. 

> [!NOTE]
> Function App의 모든 함수는 동일한 언어로 작성되어야 합니다. [이전 버전](functions-versions.md)의 Azure Functions 런타임에서는 이러한 요구 사항이 필요하지 않았습니다.

## <a name="folder-structure"></a>폴더 구조
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

위 그림은 Function App의 기본(및 권장) 폴더 구조입니다. 함수 코드의 파일 위치를 변경하려면 _function.json_ 파일의 `scriptFile` 섹션을 수정합니다. 또한 [패키지 배포](deployment-zip-push.md)를 사용하여 Azure의 Function App에 프로젝트를 배포하는 것이 좋습니다. [연속 통합 및 지속적인 배포](functions-continuous-deployment.md), Azure DevOps 등의 기존 도구를 사용할 수도 있습니다.

> [!NOTE]
> 패키지를 수동으로 배포하는 경우 _host.json_ 파일과 함수 폴더를 `wwwroot` 폴더에 직접 배포해야 합니다. 배포에 `wwwroot` 폴더를 포함하지 마세요. 그렇지 않으면 `wwwroot\wwwroot` 폴더가 만들어집니다.

#### <a name="use-local-tools-and-publishing"></a>로컬 도구 및 게시 사용
Function App은 [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) 및 [Azure Functions Core Tools](./functions-develop-local.md)를 비롯한 다양한 도구를 사용하여 작성하고 게시할 수 있습니다. 자세한 내용은 [Azure Functions를 로컬에서 코딩 및 테스트](./functions-develop-local.md)를 참조하세요.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Azure Portal에서 함수를 편집하는 방법
Azure Portal에 기본 제공된 함수 편집기를 사용하면 코드와 *function.json* 파일을 인라인에서 직접 업데이트할 수 있습니다. 이 작업은 사소한 변경이나 개념 증명에만 권장되며, VS Code와 같은 로컬 개발 도구를 사용하는 것이 좋습니다.

## <a name="parallel-execution"></a>병렬 실행
복수의 트리거 이벤트가 단일 스레드 함수 런타임이 해당 이벤트를 처리할 수 있는 속도보다 빨리 발생하면 런타임은 병렬 모드로 함수를 여러 번 호출할 수 있습니다.  함수 앱이 [소비 호스팅 계획](functions-scale.md#how-the-consumption-and-premium-plans-work)을 사용하는 경우 함수 앱은 자동으로 확장할 수 있습니다.  앱이 소비 호스팅 계획 또는 일반 [App Service 계획](../app-service/overview-hosting-plans.md)에서 실행되는지 여부에 관계없이 함수 앱의 각 인스턴스는 여러 스레드를 사용하여 동시 함수 호출을 병렬로 처리할 수 있습니다.  각 함수 앱 인스턴스의 최대 동시 함수 호출 수는 함수 앱 내의 다른 함수에서 사용하는 리소스뿐만 아니라 사용 중인 트리거 유형에 따라 달라집니다.

## <a name="functions-runtime-versioning"></a>Functions 런타임 버전 관리

`FUNCTIONS_EXTENSION_VERSION` 앱 설정을 사용하여 Functions 런타임의 버전을 구성할 수 있습니다. 예를 들어 “~2”는 Function App이 주 버전으로 2.x를 사용한다는 것을 나타냅니다. Function App은 부 버전이 새로 릴리스될 때마다 업그레이드됩니다. 정확한 함수 앱 버전을 확인하는 방법을 비롯한 자세한 내용을 보려면 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](set-runtime-version.md)을 참조하세요.

## <a name="repositories"></a>리포지토리
Azure Functions에 대한 코드는 공개 소스이며 GitHub 리포지토리에 저장됩니다.

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions 호스트](https://github.com/Azure/azure-functions-host/)
* [Azure Functions 포털](https://github.com/azure/azure-functions-ux)
* [Azure Functions 템플릿](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 확장](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>바인딩
지원되는 모든 바인딩을 포함하는 테이블입니다.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

바인딩에서 들어오는 오류와 함께 문제가 있습니까? [Azure Functions 바인딩 오류 코드](functions-bindings-error-pages.md) 설명서를 참조하세요.

## <a name="reporting-issues"></a>문제 보고
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
* [Azure Functions를 로컬에서 코딩 및 테스트](./functions-develop-local.md)
* [Azure Functions에 대한 모범 사례](functions-best-practices.md)
* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)
