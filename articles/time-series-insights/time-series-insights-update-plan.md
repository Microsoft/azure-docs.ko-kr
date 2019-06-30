---
title: Azure Time Series Insights 미리 보기 환경 계획 | Microsoft Docs
description: Azure Time Series Insights 미리 보기 환경을 계획합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 5f89105abc21f5ef6cce53ea55622a808f947e86
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357297"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights 미리 보기 환경 계획

이 문서에서는 계획 및 Azure Time Series Insights 미리 보기를 사용 하 여 빠르게 시작 하는 모범 사례를 설명 합니다.

> [!NOTE]
> 일반 공급 Time Series Insights 인스턴스를 계획 하기 위한 모범 사례를 참조 하세요 [일반 공급 Azure Time Series Insights 환경 계획](time-series-insights-environment-planning.md)합니다.

## <a name="best-practices-for-planning-and-preparation"></a>계획 및 준비 모범 사례

Time Series Insights를 시작하려면 다음을 이해하는 것이 좋습니다.

* 얻게 경우 있습니다 [Time Series Insights 미리 보기 환경 프로 비전](#the-preview-environment)합니다.
* 어떤 프로그램 [시간 시리즈 Id 및 타임 스탬프 속성은](#configure-time-series-ids-and-timestamp-properties)합니다.
* 새로운 [시계열 모델은](#understand-the-time-series-model), 및 직접 빌드하는 방법입니다.
* 하는 방법 [JSON에서 이벤트를 효율적으로 전송할](#shape-your-events)합니다.
* Time Series Insights [비즈니스 재해 복구 옵션](#business-disaster-recovery)합니다.

Azure Time Series Insights는 종 량 제 비즈니스 모델을 사용합니다. 요금 및 용량에 대한 자세한 내용은 [Time Series Insights 가격 책정](https://azure.microsoft.com/pricing/details/time-series-insights/)을 참조하세요.

## <a name="the-preview-environment"></a>미리 보기 환경

Time Series Insights 미리 보기 환경을 프로비전할 때는 다음 두 개의 Azure 리소스를 만듭니다.

* Azure Time Series Insights 미리 보기 환경
* Azure Storage 범용 V1 계정

시작하려면 다음 세 개의 추가 항목이 필요합니다.

* [시계열 모델](./time-series-insights-update-tsm.md)
* [Time Series Insights에 연결된 이벤트 원본](./time-series-insights-how-to-add-an-event-source-iothub.md)
* 모델에 매핑되고 유효한 JSON 형식인, [이벤트 원본으로 흐르는 이벤트](./time-series-insights-send-events.md)

## <a name="configure-time-series-ids-and-timestamp-properties"></a>시간 시계열 Id 및 타임 스탬프 속성 구성

새로운 Time Series Insights 환경을 만들려면 시계열 ID를 선택합니다. 이렇게 하면 데이터의 논리 파티션으로 작동합니다. 설명한 대로, 시계열 ID가 준비되었는지 확인합니다.

> [!IMPORTANT]
> 시계열 ID는 *변경 불가*이며, *나중에 변경할 수 없습니다*. 최종 선택하고 처음 사용하기 전에 각 항목을 확인합니다.

고유 하 게 리소스를 구분 하기 위해 최대 세 개의 키를 선택할 수 있습니다. 자세한 내용은 [시계열 ID 선택 모범 사례](./time-series-insights-update-how-to-id.md) 및 [스토리지 및 수신](./time-series-insights-update-storage-ingress.md)을 참조하세요.

타임스탬프 속성도 중요합니다. 이벤트 원본을 추가할 때 이 속성을 지정할 수 있습니다. 각 이벤트 원본에는 시간 경과에 따라 이벤트 원본을 추적하는 데 사용되는 선택적 타임스탬프 속성이 있습니다. 타임스탬프 값은 대/소문자를 구분하며 각 이벤트 원본의 개별 사양에 맞는 형식이어야 합니다.

> [!TIP]
> 이벤트 원본에 대한 형식 및 구문 분석 요구 사항을 확인합니다.

이 값을 비워 두면 이벤트 원본의 이벤트를 큐에 넣는 시간이 이벤트 타임스탬프로 사용됩니다. 기록 데이터 또는 일괄 처리 이벤트를 전송하는 경우 타임스탬프 속성을 사용자 지정하는 것이 기본 이벤트를 큐에 넣는 시간보다 더 유용합니다. 자세한 내용은 방법에 대해 알아보세요 [Azure IoT Hub의 이벤트 원본을 추가](./time-series-insights-how-to-add-an-event-source-iothub.md)합니다.

## <a name="understand-the-time-series-model"></a>시계열 모델 이해

이제 Time Series Insights 환경의 시계열 모델을 구성할 수 있습니다. 새 모델을 사용하면 IoT 데이터를 쉽게 찾아서 분석할 수 있습니다. 시계열 데이터의 큐레이션, 유지 관리 및 보강을 가능하게 하며, 소비자가 바로 사용할 수 있는 데이터 세트를 준비하는 데 도움이 됩니다. 모델 형식 및 계층 구조 라는 변수를 사용 하 여 고유한 리소스를 연결 하는 인스턴스에 매핑되는 시간 시리즈 Id를 사용 합니다. 새로운 [시계열 모델](./time-series-insights-update-tsm.md)에 대해 읽어 보세요.

모델은 동적이므로 언제든지 빌드할 수 있습니다. 빠르게 시작하려면 데이터를 Time Series Insights에 푸시하기 전에 빌드 및 업로드합니다. 모델을 빌드하려면 [시계열 모델 사용](./time-series-insights-update-how-to-tsm.md)을 참조하세요.

많은 고객의 경우, 시계열 모델은 이미 구현된 기존 자산 모델 또는 ERP 시스템에 매핑됩니다. 기존 모델이 없는 경우, 빠르게 시작하고 실행하도록 사전 빌드된 사용자 환경이 [제공](https://github.com/Microsoft/tsiclient)됩니다. 모델이 어떻게 도움이 되는지를 확인하려면 [샘플 데모 환경](https://insights.timeseries.azure.com/preview/demo)을 살펴보세요.

## <a name="shape-your-events"></a>이벤트 셰이핑

Time Series Insights에 이벤트를 보내는 방법을 확인할 수 있습니다. 이벤트를 효율적으로 잘 비정규화하는 것이 좋습니다.

경험상 좋은 방법:

* 시계열 모델에 메타 데이터를 저장 합니다.
* 시간 시계열 모드, 인스턴스 필드 및 이벤트 시간 계열 ID 또는 타임 스탬프와 같은 필요한 정보를 포함 합니다.

자세한 내용은 [이벤트 셰이핑](./time-series-insights-send-events.md#json)을 참조하세요.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>다음 단계

- 검토 [Azure Advisor](../advisor/advisor-overview.md) 비즈니스 복구 구성 옵션을 계획 합니다.

- 에 대해 자세히 알아보세요 [저장소 및 수신](./time-series-insights-update-storage-ingress.md) Time Series Insights 미리 보기에 있습니다.

- 에 대 한 자세한 [데이터 모델링](./time-series-insights-update-tsm.md) Time Series Insights 미리 보기에 있습니다.