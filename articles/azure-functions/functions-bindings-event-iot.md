---
title: Azure Functions에 대한 Azure IoT Hub 바인딩
description: Azure Functions에서 IoT Hub 바인딩을 사용 하는 방법을 이해 합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 2940f9e2adff82c100ed347431e8c1d27c30202e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924444"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions에 대한 Azure IoT Hub 바인딩

이 문서에서는 IoT Hub에 대한 Azure Functions 바인딩을 사용 하는 방법을 설명 합니다. IoT Hub 지원은 [Azure Event Hubs 바인딩을](functions-bindings-event-hubs.md)기반으로 합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Azure Functions 버전 1.x의 경우 IoT Hub 바인딩은 [ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 패키지 (버전 2.x)에서 제공 됩니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지-함수 2.x 이상

4\.x 이상 버전의 경우 EventHubs 패키지, 버전 3.x를 사용 합니다. [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) . 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> 다음 코드 샘플에서는 이벤트 허브 API를 사용 하지만 지정 된 구문은 IoT Hub 함수에 적용 됩니다.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
