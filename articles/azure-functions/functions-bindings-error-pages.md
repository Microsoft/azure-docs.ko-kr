---
title: Azure Functions 오류 처리 지침
description: 특정 바인딩 오류에 대한 링크를 사용 하 여 Azure Functions 오류를 처리 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 06dc4c5c1713ee10f263e573a698e9ea36ca2662
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227334"
---
# <a name="azure-functions-error-handling"></a>Azure Functions 오류 처리

손실 된 데이터, 누락 된 이벤트 및 응용 프로그램의 상태를 모니터링 하는 것을 방지 하기 위해 Azure Functions 오류를 처리 하는 것이 중요 합니다.

이 문서에서는 바인딩 관련 오류에 대한 링크와 함께 오류 처리에 대한 일반적인 전략을 설명 합니다.

## <a name="handling-errors"></a>오류 처리

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>바인딩 오류 코드

Azure 서비스와 통합 하는 경우 기본 서비스의 Api에서 오류가 발생할 수 있습니다. 바인딩 관련 오류와 관련 된 정보는 다음 문서의 **예외 및 반환 코드** 섹션에서 사용할 수 있습니다.

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
