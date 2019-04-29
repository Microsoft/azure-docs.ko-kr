---
title: Azure Maps의 소비 모델 | Microsoft Docs
description: Azure Maps의 소비 모델에 대해 알아봅니다.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60797906"
---
# <a name="consumption-model"></a>소비 모델

온라인 라우팅은 차량 관련 소비 모델에 대한 자세한 설명을 위한 매개 변수 집합을 제공합니다.
**vehicleEngineType**의 값에 따라 다음의 두 가지 주요 소비 모델이 지원됩니다. _Combustion_ 및 _Electric_ 같은 요청에서 서로 다른 모델에 속해 있는 매개 변수를 지정하면 오류가 발생합니다.
소비 모델은 **travelMode** 값 _bicycle_ 및 _pedestrian_과 함께 사용할 수 없습니다.

## <a name="parameter-constraints-for-consumption-model"></a>소비 모델에 대한 매개 변수 제약 조건

두 소비 모델 모두에서 일부 매개 변수를 명시적으로 지정하려면 다른 매개 변수도 똑같이 지정해야 합니다. 이러한 종속성은 다음과 같습니다.

* 모든 매개 변수에서 **constantSpeedConsumption**은 사용자가 지정해야 합니다. **constantSpeedConsumption**이 지정되지 않은 경우에 **vehicleWeight**의 예외와 함께 다른 소비 모델 매개 변수를 지정하면 오류가 발생합니다.
* **accelerationEfficiency** 및 **decelerationEfficiency**는 항상 쌍으로 지정해야 합니다(즉, 둘 다 또는 없음).
* **accelerationEfficiency** 및 **decelerationEfficiency**가 지정된 경우 해당 값은 1 이하여야 합니다(영구적 동작을 방지하기 위해).
* **uphillEfficiency** 및 **downhillEfficiency**는 항상 쌍으로 지정해야 합니다(즉, 둘 다 또는 없음).
* **uphillEfficiency** 및 **downhillEfficiency**가 지정된 경우 해당 값은 1 이하여야 합니다(영구적 동작을 방지하기 위해).
* 사용자가 \*__Efficiency__ 매개 변수를 지정한 경우 **vehicleWeight**도 지정해야 합니다. **vehicleEngineType**이 _combustion_인 경우 **fuelEnergyDensityInMJoulesPerLiter**도 지정해야 합니다.
* **maxChargeInkWh** 및 **currentChargeInkWh**는 항상 쌍으로 지정해야 합니다(즉, 둘 다 또는 없음).

> [!NOTE]
> **constantSpeedConsumption**이 지정된 경우에만 경사 및 차량 가속과 같은 다른 소비 측면이 소비 계산에 대해 고려되지 않습니다.

## <a name="combustion-consumption-model"></a>연소식 소비 모델

연소식 소비 모델은 **vehicleEngineType**이 _combustion_으로 설정된 경우에 사용됩니다.
이 모델에 속해 있는 매개 변수 목록은 다음과 같습니다. 자세한 설명은 매개 변수 섹션을 참조하세요.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>일렉트릭 소비 모델

일렉트릭 소비 모델은 **vehicleEngineType**이 _electric_으로 설정된 경우에 사용됩니다.
이 모델에 속해 있는 매개 변수 목록은 다음과 같습니다. 자세한 설명은 매개 변수 섹션을 참조하세요.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>소비 매개 변수의 적절한 값

위에 지정된 모든 명시적 요구를 충족할 수 있는 경우라도 특정 소비 매개 변수 집합이 거부될 수 있습니다. 특정 매개 변수의 값 또는 여러 매개 변수의 값 조합이 소비 값의 지나친 규모를 야기하는 것으로 간주되는 경우 발생합니다. 발생하면 소비 매개 변수의 모든 적절한 값에 맞도록 적절한 주의가 요구되므로 입력 오류가 표시될 가능성이 있습니다. 특정 소비 매개 변수 집합이 거부되는 경우 함께 제공되는 오류 메시지에 이유에 관한 텍스트 설명이 포함됩니다.
자세한 매개 변수 설명에 두 모델 모두에 적절한 값의 예제가 포함되어 있습니다.
