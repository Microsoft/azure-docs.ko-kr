---
title: Azure Maps Android SDK에 기능 정보를 표시 합니다. Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps Android SDK를 사용 하 여 맵에 기능 정보를 표시 하는 방법에 대해 설명 합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911685"
---
# <a name="display-feature-information"></a>기능 정보 표시

공간 데이터는 종종 점으로, 선 및 다각형을 사용 하 여 표현 됩니다. 이 데이터는 연결 된 메타 데이터 정보를 포함 하는 경우가 많습니다. 예를 들어 지점은 저장소의 위치를 나타내고 식당의 메타 데이터는 해당 이름, 주소 및 사용 되는 음식 형식일 수 있습니다. `JsonObject`를 사용 하 여이 메타 데이터를 이러한 기능의 속성으로 추가할 수 있습니다. 다음 코드에서는 값이 "Hello World!" 인 `title` 속성을 사용 하 여 간단한 point 기능을 만듭니다.

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

사용자가 맵의 기능과 상호 작용 하는 경우 이벤트를 사용 하 여 해당 작업에 대응할 수 있습니다. 일반적인 시나리오는 사용자가 상호 작용 한 기능의 메타 데이터 속성에 대한 메시지를 표시 하는 것입니다. `OnFeatureClick` 이벤트는 사용자가 지도의 기능을 탭 할 때를 검색 하는 데 사용 되는 기본 이벤트입니다. `OnLongFeatureClick` 이벤트도 있습니다. 지도에 `OnFeatureClick` 이벤트를 추가 하는 경우이를 제한 하려면 계층의 ID를 전달 하 여 단일 계층으로 제한 될 수 있습니다. 계층 ID가 전달 되지 않은 경우에는 해당 계층에 관계 없이 맵의 모든 기능을 눌러이 이벤트를 발생 시킬 수 있습니다. 다음 코드에서는 지도에서 point 데이터를 렌더링할 기호 계층을 만든 다음 `OnFeatureClick` 이벤트를 추가 하 고이 기호 계층으로 제한 합니다.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>알림 메시지 표시

알림 메시지는 사용자에 게 정보를 표시 하는 가장 쉬운 방법 중 하나 이며 모든 버전의 Android에서 사용할 수 있습니다. 모든 유형의 사용자 입력을 지원 하지 않으며 짧은 시간 동안만 표시 됩니다. 사용자가 누른 항목에 대한 정보를 신속 하 게 알리려면 알림 메시지가 표시 될 수 있습니다. 다음 코드에서는 알림 메시지를 `OnFeatureClick` 이벤트와 함께 사용할 수 있는 방법을 보여 줍니다.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

탭 하는 기능의 애니메이션과 표시 되는 알림 메시지를 ![](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

알림 메시지 외에도 다음과 같은 다양 한 방법으로 기능의 메타 데이터 속성을 표시할 수 있습니다.

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) -snackbars 작업에 대한 간단한 피드백을 제공 합니다. 모바일에서 화면 아래쪽에 짧은 메시지를 표시 하 고 더 큰 장치에서 왼쪽 아래에 표시 합니다. Snackbars 화면에 있는 다른 모든 요소 위에 표시 되며 한 번에 하나만 표시할 수 있습니다.
- [대화 상자](https://developer.android.com/guide/topics/ui/dialogs)-대화 상자는 사용자에 게 결정을 하거나 추가 정보를 입력 하 라는 메시지를 표시 하는 작은 창입니다. 대화 상자는 화면을 채우지 않으며 작업을 계속 하기 전에 사용자가 작업을 수행 해야 하는 모달 이벤트에 일반적으로 사용 됩니다.
- 현재 활동에 [조각을](https://developer.android.com/guide/components/fragments) 추가 합니다.
- 다른 활동 또는 뷰로 이동 합니다.

## <a name="next-steps"></a>다음 단계

지도에 데이터를 더 추가 하려면:

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Android 맵에 셰이프 추가](how-to-add-shapes-to-android-map.md)
