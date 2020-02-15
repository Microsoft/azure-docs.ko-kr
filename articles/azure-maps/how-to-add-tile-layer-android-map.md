---
title: Android maps에 타일 계층 추가 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps Android SDK를 사용 하 여 지도에서 타일 계층을 렌더링 하는 방법을 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8e1a77ae83783b2841a2600654a9775e9ceb6ada
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209939"
---
# <a name="add-a-tile-layer-to-a-map-using-the-azure-maps-android-sdk"></a>Azure Maps Android SDK를 사용 하 여 지도에 타일 계층 추가

이 문서에서는 Azure Maps Android SDK를 사용 하 여 지도에서 타일 계층을 렌더링 하는 방법을 보여 줍니다. 타일 계층을 사용하여 Azure Maps 기본 맵 타일의 위에 이미지를 겹칠 수 있습니다. Azure Maps의 타일 배치 체계에 대한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md) 설명서에서 볼 수 있습니다.

타일 계층이 서버에서 타일에 로드 됩니다. 이러한 이미지는 타일 계층에서 인식 하는 명명 규칙을 사용 하 여 미리 렌더링 하 고 서버의 다른 이미지와 마찬가지로 저장할 수 있습니다. 또는 이러한 이미지를 실시간으로 거의 이미지를 생성 하는 동적 서비스로 렌더링할 수 있습니다. Azure Maps TileLayer 클래스에서 지 원하는 세 가지 타일 서비스 명명 규칙은 다음과 같습니다.

* X, Y, 확대/축소 표기법-확대/축소 수준에 따라 타일 그리드에서 x는 열 위치이고 y는 행 위치입니다.
* Quadkey 표기법-x, y 조합은 정보를 타일의 고유 식별자인 단일 문자열 값으로 확대/축소합니다.
* 경계 상자-경계 상자 좌표를 사용하여 일반적으로 `{west},{south},{east},{north}`웹 매핑 서비스(WMS)[에 사용하는 ](https://www.opengeospatial.org/standards/wms) 형식으로 이미지를 지정할 수 있습니다.

> [!TIP]
> TileLayer은 지도에서 큰 데이터 집합을 시각화 하는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 있을 뿐 아니라 타일 계층으로 벡터 데이터도 렌더링할 수 있습니다. 맵 컨트롤은 타일 계층으로 벡터 데이터를 렌더링함으로써, 맵 컨트롤은 표현하는 벡터 데이터보다 파일 크기가 훨씬 더 작은 타일만 로드해야 합니다. 맵에서 데이터 행 수백만 개를 렌더링해야 하는 대부분의 사용자가 이 기술을 사용합니다.

타일 계층으로 전달된 타일 URL은 다음 매개 변수를 사용하는 TileJSON 리소스에 대한 http/https URL 또는 타일 URL 템플릿이어야 합니다. 

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing Maps 타일 시스템 명명 규칙에 따라 타일 Quadkey 식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.
* `{subdomain}`-하위 도메인 값이 지정 된 경우 하위 도메인 값에 대 한 자리 표시자입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 프로세스를 완료 하려면 맵을 로드 하기 위해 [Azure Maps Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) 를 설치 해야 합니다.


## <a name="add-a-tile-layer-to-the-map"></a>지도에 타일 계층 추가

 이 샘플에서는 타일 집합을 가리키는 타일 계층을 만드는 방법을 보여 줍니다. 이러한 타일에는 "x, y, zoom" 바둑판식 배열 시스템이 사용 됩니다. 이 타일 계층의 원본은 [아이오와 주립 대학교의 Iowa Environmental Mesonet](https://mesonet.agron.iastate.edu/ogc/)에서 받은 날씨 레이더 오버레이입니다. 

아래 단계를 수행 하 여 지도에 타일 계층을 추가할 수 있습니다.

1. **Res > 레이아웃 > activity_main xml** 을 편집 하 여 아래와 같이 표시 합니다.

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
            app:mapcontrol_centerLat="40.75"
            app:mapcontrol_centerLng="-99.47"
            app:mapcontrol_zoom="3"
            />
    
    </FrameLayout>
    ```

2. 다음 코드 조각을 `MainActivity.java` 클래스의 **onCreate ()** 메서드에 복사 합니다.

    ```Java
    mapControl.onReady(map -> {
        //Add a tile layer to the map, below the map labels.
        map.layers.add(new TileLayer(
            tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
            opacity(0.8f),
            tileSize(256)
        ), "labels");
    });
    ```
    
    위의 코드 조각은 먼저 **Onready ()** 콜백 메서드를 사용 하 여 Azure Maps map 컨트롤 인스턴스를 가져옵니다. 그런 다음 `TileLayer` 개체를 만들고 서식 지정 된 **xyz** 타일 URL을 `tileUrl` 옵션으로 전달 합니다. 계층의 불투명도는 `0.8`로 설정 되 고, 사용 되는 타일 서비스의 타일은 256 픽셀 타일로 설정 되므로이 정보는 `tileSize` 옵션으로 전달 됩니다. 그러면 타일 계층이 지도 계층 관리자로 전달 됩니다.

    위의 코드 조각을 추가한 후 `MainActivity.java`은 아래와 같습니다.
    
    ```Java
    package com.example.myapplication;

    import android.app.Activity;
    import android.os.Bundle;
    import android.support.v7.app.AppCompatActivity;
    import com.microsoft.azure.maps.mapcontrol.layer.TileLayer;
    import java.util.Arrays;
    import java.util.List;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileSize;
    import static com.microsoft.azure.maps.mapcontrol.options.TileLayerOptions.tileUrl;
        
    public class MainActivity extends AppCompatActivity {
    
        static{
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }
    
        MapControl mapControl;
        @Override
        protected void onCreate(Bundle savedInstanceState) {
    
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapControl = findViewById(R.id.mapcontrol);
    
            mapControl.onCreate(savedInstanceState);
    
            mapControl.onReady(map -> {

                //Add a tile layer to the map, below the map labels.
                map.layers.add(new TileLayer(
                    tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
                    opacity(0.8f),
                    tileSize(256)
                ), "labels");
            });    
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }    
    }
    ```

이제 응용 프로그램을 실행 하는 경우 아래와 같이 맵에 줄이 표시 됩니다.

<center>

Android 지도 선](./media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)</center> ![

## <a name="next-steps"></a>다음 단계

지도 스타일을 설정 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

> [!div class="nextstepaction"]
> [Android maps에서 지도 스타일 변경](https://docs.microsoft.com/azure/azure-maps/set-android-map-styles)