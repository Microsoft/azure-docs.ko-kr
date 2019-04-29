---
title: Azure Data Box Edge 디바이스 모니터링 | Microsoft Docs
description: Azure Portal 및 로컬 웹 UI를 사용하여 Azure Data Box Edge를 모니터링하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756757"
---
# <a name="monitor-your-azure-data-box-edge"></a>Azure Data Box Edge 모니터링

이 문서에서는 Azure Data Box Edge를 모니터링하는 방법을 설명합니다. 디바이스를 모니터링하기 위해 Azure Portal 또는 로컬 웹 UI를 사용할 수 있습니다. Azure Portal을 사용하여 디바이스 이벤트를 보고 경고를 구성 및 관리하고 메트릭을 봅니다. 실제 디바이스에서 로컬 웹 UI를 사용하여 다양한 디바이스 구성 요소의 하드웨어 상태를 봅니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 디바이스 이벤트 및 해당 경고 보기
> * 디바이스 구성 요소의 하드웨어 상태 보기
> * 디바이스의 용량 및 트랜잭션 메트릭 보기
> * 경고 구성 및 관리

## <a name="view-device-events"></a>디바이스 이벤트 보기

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>하드웨어 상태 보기

디바이스 구성 요소의 하드웨어 상태를 보려면 로컬 웹 UI에서 다음 단계를 수행합니다.

1. 디바이스의 로컬 웹 UI에 연결합니다.
2. **유지 관리 > 하드웨어 상태**로 이동합니다. 다양한 디바이스 구성 요소의 상태를 볼 수 있습니다.

    ![하드웨어 상태 보기](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>메트릭 보기

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>경고 관리

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>다음 단계 

[대역폭을 관리](data-box-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.