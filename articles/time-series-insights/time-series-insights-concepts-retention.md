---
title: Azure Time Series Insights 환경의 데이터 보존 기간 이해 | Microsoft Docs
description: 이 아티클에서는 Azure Time Series Insights 환경의 데이터 보존 기간을 제어하는 두 가지 설정을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.custom: seodec18
ms.openlocfilehash: b230ac48cf2ca14c9ed988f869b5abba3e347215
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761565"
---
# <a name="understand-data-retention-in-time-series-insights"></a>Time Series Insights의 데이터 보존 기간 이해

이 아티클에서는 TSI(Time Series Insights) 환경의 데이터 보존 기간에 영향을 주는 두 가지 설정을 설명합니다.

## <a name="video"></a>비디오

### <a name="in-this-video-we-cover-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>이 비디오에서는 Time Series Insights 데이터 보존 및 이에 대한 계획을 세우는 방법을 설명합니다.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

각 TSI 환경에는 **데이터 보존 시간**을 제어하는 설정이 있습니다. 값의 범위는 1~400일입니다. 데이터는 환경 저장소 용량 또는 보존 기간(1-400) 중 더 빠른 시간을 기준으로 삭제됩니다.

각 TSI 환경에는 **저장 제한을 초과하는 동작**이라는 추가 설정이 있습니다. 이 설정은 환경의 최대 용량에 도달하면 수신 및 삭제 동작을 제어합니다. 두 가지 동작 중에서 선택할 수 있습니다.
- **이전 데이터 삭제**(기본값)  
- **수신 일시 중지**

> [!NOTE]
> 기본적으로 새 환경을 만들면 보존 기간이 **이전 데이터 삭제**로 구성됩니다. 이러한 설정은 생성 후 TSI 환경의 **구성** 페이지에서 Azure Portal을 사용하여 필요에 따라 전환할 수 있습니다.

보존 동작을 전환하는 방법에 대한 자세한 내용은 [Time Series Insights의 보존 기간 구성](time-series-insights-how-to-configure-retention.md)을 참조하세요.

데이터 보존 동작 비교:

## <a name="purge-old-data"></a>이전 데이터 삭제

- 이 동작은 TSI 환경의 기본 동작이며, 공개 미리 보기로 출시된 이후 TSI 환경에서 보여준 동일한 동작을 보여줍니다.  
- 이 동작은 사용자가 TSI 환경에서 항상 *가장 최근의 데이터*를 보려는 경우에 권장됩니다. 
- 이 동작은 환경의 한도(보존 시간, 크기 또는 개수 중 가장 처음으로 도달한 한도)에 도달한 경우 데이터를 *삭제*합니다. 기본적으로 보존 기간은 30일로 설정됩니다. 
- 가장 오래 전에 수집된 데이터부터 먼저 삭제됩니다(FIFO 방식).

### <a name="example-one"></a>예제 1

보존 동작 **수신을 계속하고 이전 데이터 삭제**가 지정된 예제 환경을 고려해 보세요. 이 예제에서 **데이터 보존 시간**은 400일로 설정되어 있습니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다.   인바운드 데이터가 매일 평균 500MB 누적된다고 가정하겠습니다. 인바운드 데이터의 속도에 따르면 60일째에 최대 용량에 도달하므로 이 환경은 데이터를 60일만 보존할 수 있습니다. 인바운드 데이터 누적: 매일 500MB x 60일 = 30GB

이 예제에서는 61일째에 가장 최신 데이터를 보여주지만 60일보다 오래된 데이터는 삭제됩니다. 삭제를 통해 스트리밍되는 새 데이터를 위한 공간을 확보하여 새 데이터를 계속 탐색할 수 있습니다. 

데이터를 더 오래 보존하려면 단위를 추가하여 환경의 규모를 늘리거나 푸시되는 데이터의 양을 줄여야 합니다.  

### <a name="example-two"></a>예제 2

보존 동작이 **수신을 계속하고 이전 데이터 삭제**로 구성되어 있는 환경도 생각해 보세요. 이 예제에서는 **데이터 보존 시간**이 더 낮은 값인 180일로 설정됩니다. **용량**은 30GB의 총 용량을 포함하는 S1 단위로 설정됩니다. 180일 내내 데이터를 저장하려면 일일 수신 규모가 0.166GB(166MB)를 초과하지 않아야 합니다.  

이 환경의 일일 수신 속도가 0.166GB를 초과할 때마다 일부 데이터가 삭제되므로 데이터가 180일 동안 보관되지 않습니다. 이와 동일한 환경을 바쁜 시간대에 사용한다고 생각해 보세요. 환경의 수신 속도가 일 평균 0.189GB로 증가할 수 있다고 가정해 보겠습니다. 이러한 바쁜 시간대에는 데이터가 약 158일 동안 보존됩니다(30GB/0.189 = 158.73일). 원하는 데이터 보존 기간보다 줄어든 기간입니다.

## <a name="pause-ingress"></a>수신 일시 중지

- 이 동작은 보존 기간 전에 크기와 개수 한도에 도달하지 않을 경우 데이터가 삭제되지 않도록 합니다.  
- 이 동작은 보존 기간 위반으로 데이터가 삭제되기 전에 사용자가 해당 환경의 용량을 늘릴 수 있는 추가 시간을 제공합니다.
- 이 동작은 데이터 손실을 방지하는 데 도움이 되지만 이벤트 원본의 보존 기간이 지나서 수신이 일시 중지될 경우 가장 최근의 데이터가 손실될 가능성이 생깁니다.
- 하지만 환경의 최대 용량에 도달하면 추가 조치가 있을 때까지 데이터 수신이 일시 중지됩니다. 
   - 환경의 최대 용량을 늘리세요. 자세한 내용은 [Time Series Insights 환경의 규모를 조정하는 방법](time-series-insights-how-to-scale-your-environment.md)을 참조하세요.
   - 데이터 보존 기간에 도달하면 데이터가 삭제되므로 환경을 최대 용량 미만으로 유지하세요.

### <a name="example-three"></a>예제 3

보존 동작이 **수신 일시 중지**로 구성된 환경을 생각해 보세요. 이 예제에서는 **데이터 보존 기간** 60일로 구성됩니다. **용량**은 S1의 3개 단위로 설정됩니다. 이 환경에 매일 2GB 데이터가 수신된다고 가정하겠습니다. 이 환경에서 최대 용량에 도달하면 수신이 일시 중지됩니다. 이때 수신이 다시 시작되거나 ‘수신 계속’이 활성화될 때까지 환경에 동일한 데이터 세트가 표시됩니다(이전 데이터를 삭제하여 새 데이터를 위한 공간 확보). 

수신이 다시 시작되면:

- 이벤트 원본이 수신한 순서대로 데이터가 흐릅니다.
- 이벤트 원본의 보존 정책을 초과하지 않은 경우, 타임스탬프에 따라 이벤트가 인덱싱됩니다. 이벤트 원본 보존 기간 구성에 대한 자세한 내용은 [Event Hubs FAQ](../event-hubs/event-hubs-faq.md)를 참조하세요.

> [!IMPORTANT]
> 수신이 일시 중지되는 것을 방지하는 데 도움이 되는 통지를 제공하도록 경고를 설정해야 합니다. Azure 이벤트 원본의 기본 보존 기간은 1일이므로 데이터가 손실될 수 있습니다. 따라서 수신이 일시 중지된 후 추가 조치를 취하지 않을 경우 가장 최근의 데이터가 손실될 수 있습니다. 데이터 손실 가능성을 피하려면 용량을 늘리거나 **이전 데이터 삭제**로 동작을 전환해야 합니다.

Time Series Insights에서 수신 일시 중지가 발생할 경우 데이터 손실을 최소화하기 위해 영향을 받는 Event Hubs에서 **메시지 보존** 속성을 조정하는 것이 좋습니다.

![이벤트 허브 메시지 보존.](media/time-series-insights-contepts-retention/event-hub-retention.png)

이벤트 원본에 구성된 속성이 없는 경우(timeStampPropertyName), TSI는 기본적으로 이벤트 허브 도착 타임스탬프를 x-축으로 설정합니다. timeStampPropertyName이 다른 값으로 구성된 경우 이벤트가 구문 분석될 때 데이터 패킷에서 구성된 timeStampPropertyName이 검색됩니다. 

추가 용량을 확보하거나 보존 기간을 늘리기 위해 환경의 규모를 확장해야 하는 경우 [Time Series Insights 환경의 규모를 조정하는 방법](time-series-insights-how-to-scale-your-environment.md)에서 자세한 내용을 참조하세요.  

## <a name="next-steps"></a>다음 단계

- 보존 동작을 전환하는 방법에 대한 자세한 내용은 [Time Series Insights의 보존 기간 구성](time-series-insights-how-to-configure-retention.md)을 참조하세요.
