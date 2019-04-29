---
title: Azure Functions의 트리거 및 바인딩
description: Azure Function 온라인 이벤트 및 클라우드 기반 서비스에 연결할 트리거 및 바인딩을 사용 하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
origin.date: 02/18/2019
ms.date: 03/04/2019
ms.author: v-junlch
ms.openlocfilehash: 3865f748a9ca2fe09660d6454542d64f73a8e3c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020965"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념

이 문서의 functions 트리거 및 바인딩 관련 고급 개념을 알아봅니다.

트리거는 원인을 실행할 함수입니다. 트리거 함수를 호출 하는 방법을 정의 하 고 함수는 정확히 하나의 트리거만 있어야 합니다. 키를 누릅니다. 트리거 함수의 페이로드로 자주 제공 되는 데이터를 연결 합니다. 

함수 바인딩에 선언적으로 함수를 다른 리소스를 연결 하는 방법이 바인딩으로 연결 될 수 있습니다 *입력 바인딩*를 *출력 바인딩을*, 또는 둘 다. 데이터 바인딩에서 매개 변수로 함수에 제공 됩니다.

혼합할 수 있으며 필요에 맞게 다른 바인딩과 일치 수 있습니다. 바인딩 선택 사항이 며 함수 수 있는 하나 이상의 입력 및/또는 출력 바인딩을 합니다.

트리거 및 바인딩을 통해 다른 서비스에 대 한 하드 코딩 액세스를 방지할 수 있습니다. 함수는 함수 매개 변수에서 데이터를 수신합니다(예: 큐 메시지의 콘텐츠). 함수의 반환 값을 사용하여 데이터를 보냅니다(예를 들어 큐 메시지를 만들기 위해). 

다음 예제와 다른 함수를 구현 하는 방법을 고려해 야 합니다.

| 예제 시나리오  | 트리거 | 입력 바인딩 | 출력 바인딩 |
|-------------|---------|---------------|----------------|
| 새 큐 메시지가 다른 큐로 작성 하는 함수를 실행 하는 도착 합니다. | 큐<sup>*</sup> | *없음* | 큐<sup>*</sup> |
|예약 된 작업을 Blob Storage 콘텐츠를 읽고 새 Cosmos DB 문서를 만듭니다. | 타이머 | Blob Storage | Cosmos DB |
|Event Grid Blob 저장소 및 Cosmos DB에서 문서를 전자 메일을 보내는에서 이미지를 읽는 데 사용 됩니다. | Event Grid | Blob 저장소 및 Cosmos DB | SendGrid |
| Microsoft Graph를 사용 하 여 Excel 시트를 업데이트 하는 웹 후크입니다. | HTTP | *없음* | Microsoft Graph |

<sup>\*</sup> 다른 큐를 나타냅니다.

이러한 예제를 완전 하 게 작성 된 것은 아닙니다 하지만 사용 하는 방법을 트리거 및 바인딩을 모두 보여 주기 위해 제공 됩니다.

###  <a name="trigger-and-binding-definitions"></a>트리거 및 바인딩 정의

트리거 및 바인딩 개발 접근 방식에 따라 다르게 정의 됩니다.

| 플랫폼 | 여 트리거 및 바인딩을 구성 하는 중... |
|-------------|--------------------------------------------|
| C#클래스 라이브러리 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;메서드 및 매개 변수를 지정 하는 C# 특성 |
| 다른 모든 사용자 (Azure 포털 포함) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;업데이트 [function.json](./functions-reference.md) ([스키마](http://json.schemastore.org/function)) |

포털에서는이 구성에 대 한 UI를 제공 하지만 파일을 열어 직접 편집할 수 있습니다는 **고급 편집기** 를 통해 사용할 수는 **통합** 함수의 탭 합니다.

.NET에서는 매개 변수 형식은 입력된 데이터에 대 한 데이터 형식을 정의합니다. 예를 들어 사용 하 여 `string` 이진 개체로 역직렬화 할 사용자 지정 형식으로 읽을 바이트 배열 큐 트리거의 텍스트에 바인딩할 합니다.

JavaScript와 같은 동적으로 형식화되는 언어의 경우 *function.json* 파일의 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `dataType`을 `binary`로 설정합니다.

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

`dataType`에 대한 다른 옵션은 `stream` 및 `string`입니다.

## <a name="binding-direction"></a>바인딩 방향

모든 트리거와 바인딩은 [function.json](./functions-reference.md) 파일에 `direction` 속성이 있습니다.

- 트리거의 경우 방향은 언제나 `in`입니다
- 입력 및 출력 바인딩은 `in`과 `out`을 사용합니다
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. 사용 하는 경우 `inout`만 합니다 **고급 편집기** 를 통해 사용할 수는 **통합** 포털의 탭 합니다.

[클래스 라이브러리의 특성](functions-dotnet-class-library.md)을 사용하여 트리거 및 바인딩을 구성하는 경우 방향은 특성 생성자에서 제공되거나 매개 변수 형식에서 유추됩니다.

## <a name="supported-bindings"></a>지원되는 바인딩

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

미리 보기 상태 바인딩 또는 프로덕션 용도로 승인된 바인딩에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="resources"></a>리소스
- [바인딩 식 및 패턴](./functions-bindings-expressions-patterns.md)
- [Azure 함수 반환 값을 사용 하 여](./functions-bindings-return-value.md)
- [바인딩 식을 등록 하는 방법](./functions-bindings-register.md)
- 테스트:
  - [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)
  - [HTTP 이외 트리거 함수를 수동으로 실행](functions-manually-run-non-http.md)
- [바인딩 오류 처리](./functions-bindings-errors.md)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Functions 바인딩 확장 등록](./functions-bindings-register.md)

<!-- Update_Description: wording update -->
