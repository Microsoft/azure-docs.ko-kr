---
title: 포함 파일
description: 포함 파일
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: 3501ff3f92ae045019df2766bbcf7fc2c3fec5b0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768924"
---
## <a name="timeout"></a>함수 앱 시간 제한 기간 

함수 앱의 제한 시간은 [호스트 json](../articles/azure-functions/functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 속성에 의해 정의 됩니다. 다음 표에서는 계획 및 다양 한 런타임 버전에 대한 기본 및 최대 값 (분)을 보여 줍니다.

| 요금제 | 런타임 버전 | 기본값 | 최대 |
|------|---------|---------|---------|
| 소비 | 1.x | 5 | 10 |
| 소비 | 2.x | 5 | 10 |
| 소비 | 3.x | 5 | 10 |
| App Service | 1.x | 제한 없음 | 제한 없음 |
| App Service | 2.x | 30 | 제한 없음 |
| App Service | 3.x | 30 | 제한 없음 |

> [!NOTE] 
> 함수 앱 제한 시간 설정에 관계 없이, 230 초는 HTTP 트리거된 함수가 요청에 응답 하는 데 사용할 수 있는 최대 시간입니다. 이는 [Azure Load Balancer의 기본 유휴 시간 제한](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)으로 인해 발생 합니다. 처리 시간이 길면 [비동기 Durable Functions 패턴](../articles/azure-functions/durable/durable-functions-overview.md#async-http) 을 사용 하거나 [실제 작업을 지연 시키고 즉각적인 응답을 반환](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)하는 것이 좋습니다.
