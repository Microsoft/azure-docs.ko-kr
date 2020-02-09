---
title: Azure Functions의 트리거 및 바인딩
description: 트리거 및 바인딩을 사용 하 여 Azure 함수를 온라인 이벤트 및 클라우드 기반 서비스에 연결 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226555"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념

이 문서에서는 함수 트리거 및 바인딩과 관련 된 개략적인 개념에 대해 알아봅니다.

트리거는 함수를 실행 하는 원인이 됩니다. 트리거는 함수를 호출 하는 방법을 정의 하며 함수에는 정확히 하나의 트리거를 포함 해야 합니다. 트리거에는 연결 된 데이터가 있으며,이는 종종 함수 페이로드로 제공 됩니다. 

함수에 바인딩하는 것은 다른 리소스를 함수에 선언적으로 연결 하는 방법입니다. 바인딩은 *입력 바인딩*, *출력 바인딩*또는 둘 다로 연결 될 수 있습니다. 바인딩의 데이터는 함수에 매개 변수로 제공됩니다.

요구 사항에 따라 서로 다른 바인딩을 혼합 하 고 일치 시킬 수 있습니다. 바인딩은 선택 사항이 며 함수는 하나 또는 여러 개의 입력 및/또는 출력 바인딩을 포함할 수 있습니다.

트리거 및 바인딩을 사용 하면 다른 서비스에 대한 액세스를 하드 코딩 하지 않을 수 있습니다. 함수는 함수 매개 변수에서 데이터를 수신합니다(예: 큐 메시지의 콘텐츠). 함수의 반환 값을 사용하여 데이터를 보냅니다(예를 들어 큐 메시지를 만들기 위해). 

다른 함수를 구현 하는 방법에 대한 다음 예제를 참조 하세요.

| 예제 시나리오 | 트리거 | 입력 바인딩 | 출력 바인딩 |
|-------------|---------|---------------|----------------|
| 새 큐 메시지가 도착 하면 함수를 실행 하 여 다른 큐에 기록 합니다. | 큐<sup>*</sup> | *없음* | 큐<sup>*</sup> |
|예약 된 작업은 Blob Storage 내용을 읽고 새 Cosmos DB 문서를 만듭니다. | 타이머 | Blob Storage | Cosmos DB |
|Event Grid은 Blob Storage에서 이미지를 읽고 Cosmos DB의 문서에서 전자 메일을 보내는 데 사용 됩니다. | Event Grid | Blob Storage 및 Cosmos DB | SendGrid |
| Microsoft Graph를 사용 하 여 Excel 시트를 업데이트 하는 webhook입니다. | http | *없음* | Microsoft Graph |

<sup>\*</sup> 다른 큐를 나타냄

이러한 예제는 완전 하지는 않지만 트리거와 바인딩을 함께 사용할 수 있는 방법을 설명 하기 위해 제공 됩니다.

###  <a name="trigger-and-binding-definitions"></a>트리거 및 바인딩 정의

트리거와 바인딩은 개발 방법에 따라 다르게 정의 됩니다.

| 플랫폼 | 트리거 및 바인딩을 구성 하는 방법 ... |
|-------------|--------------------------------------------|
| C#클래스 라이브러리 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;특성으로 C# 메서드 및 매개 변수의 데코레이팅 |
| 기타 모든 항목 (Azure Portal 포함) | &nbsp;&nbsp;&nbsp;&nbsp;[&nbsp;업데이트 (](./functions-reference.md) [스키마](http://json.schemastore.org/function)) |

포털은이 구성에 대한 UI를 제공 하지만 함수에서 **통합** 탭을 통해 사용할 수 있는 **고급 편집기** 를 열어 직접 파일을 편집할 수 있습니다.

.NET에서 매개 변수 형식은 입력 데이터의 데이터 형식을 정의 합니다. 예를 들어 `string`를 사용 하 여 큐 트리거의 텍스트, 이진으로 읽을 바이트 배열 및 개체에 대한 사용자 지정 형식 직렬화를 취소 합니다.

JavaScript와 같은 동적으로 형식화되는 언어의 경우 `dataType`function.json*파일의* 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `dataType`을 `binary`로 설정합니다.

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

모든 트리거와 바인딩은 `direction`function.json[ 파일에 ](./functions-reference.md) 속성이 있습니다.

- 트리거의 경우 방향은 언제나 `in`입니다
- 입력 및 출력 바인딩은 `in`과 `out`을 사용합니다
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. `inout`사용 하는 경우 포털에서 **통합** 탭을 통해 **고급 편집기** 만 사용할 수 있습니다.

[클래스 라이브러리의 특성](functions-dotnet-class-library.md)을 사용하여 트리거 및 바인딩을 구성하는 경우 방향은 특성 생성자에서 제공되거나 매개 변수 형식에서 유추됩니다.

## <a name="supported-bindings"></a>지원되는 바인딩

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

미리 보기 상태 바인딩 또는 프로덕션 용도로 승인된 바인딩에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="resources"></a>리소스
- [바인딩 식 및 패턴](./functions-bindings-expressions-patterns.md)
- [Azure Function 반환 값 사용](./functions-bindings-return-value.md)
- [바인딩 식을 등록 하는 방법](./functions-bindings-register.md)
- 테스트:
  - [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)
  - [HTTP 이외 트리거 함수를 수동으로 실행](functions-manually-run-non-http.md)
- [바인딩 오류 처리](./functions-bindings-errors.md)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Functions 바인딩 확장 등록](./functions-bindings-register.md)
