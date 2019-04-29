---
title: Azure Functions에 대 한 azure IoT Hub 바인딩
description: Azure Functions에서 IoT 허브 바인딩을 사용 하는 방법을 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 87a99651d17e9867130024a5f721c5488cd42a8c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104479"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure Functions에 대 한 azure IoT Hub 바인딩

이 문서에서는 IoT Hub에 대 한 Azure Functions 바인딩 작업을 수행 하는 방법에 설명 합니다. IoT Hub 지원에 기반 합니다 [Azure Event Hubs 바인딩](functions-bindings-event-hubs.md)합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>패키지 - Functions 1.x

Azure Functions 버전 1.x를 IoT Hub에 제공 됩니다는 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 패키지를 버전 2.x입니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>패키지 - Functions 2.x

Functions 2.x의 경우 [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) 패키지 버전 3.x을 사용합니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 리포지토리에 있습니다.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> 다음 코드 샘플에서 이벤트 허브 API를 사용 하는 동안 지정 된 구문은 IoT Hub 기능에 적용 합니다.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
