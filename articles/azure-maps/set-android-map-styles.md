---
title: Azure Maps Android SDK를 사용 하 여 지도 스타일 설정 Microsoft Azure 맵
description: 이 문서에서는 Android SDK에 대 한 스타일 관련 기능 Microsoft Azure 매핑하는 방법에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 96564a89a2b64203eef913b0d8300f0dafa332c5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209582"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 지도 스타일 설정

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도 스타일을 설정 하는 두 가지 방법을 보여 줍니다. Azure Maps에는 6 가지 맵 스타일을 선택할 수 있습니다. 지원 되는 지도 스타일에 대 한 자세한 내용은 [Azure Maps에서 지원 되는 맵 스타일](./supported-map-styles.md)을 참조 하세요.


## <a name="prerequisites"></a>사전 요구 사항

이 문서의 프로세스를 완료 하려면 맵을 로드 하기 위해 [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 를 설치 해야 합니다.


## <a name="set-map-style-in-the-layout"></a>레이아웃에서 지도 스타일 설정

활동 클래스의 레이아웃 파일에서 지도 스타일을 설정할 수 있습니다. **Res > 레이아웃 > activity_main xml**을 편집 하 여 아래와 같이 표시 됩니다.

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

위의 `mapcontrol_style` 특성은 지도 스타일을 **grayscale_dark**설정 합니다. 

<center>

스타일 grayscale_dark ![](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>활동 클래스에서 지도 스타일 설정

작업 클래스에서 지도 스타일을 설정할 수 있습니다. `MainActivity.java` 클래스의 **onCreate ()** 메서드에 다음 코드 조각을 복사 합니다. 이 코드는 지도 스타일을 **satellite_road_labels**설정 합니다.

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![스타일-위성-도-레이블](./media/set-android-map-styles/satellite-road-labels.png)</center>