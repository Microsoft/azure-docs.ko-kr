---
title: '자습서: Android 앱 마이그레이션 | Microsoft Azure Maps'
description: Android 앱을 Google Maps에서 Microsoft Azure Maps로 마이그레이션하는 방법을 설명합니다.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6e54d8ea44b6c322f311cc1baeb6ca3ab6715aee
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989963"
---
# <a name="migrate-an-android-app-from-google-maps"></a>Google Maps에서 Android 앱 마이그레이션

Azure Maps Android SDK의 API 인터페이스는 웹 SDK와 비슷합니다. 이러한 SDK 중 하나로 앱을 개발하신 분들은 개념, 모범 사례 및 아키텍처가 상당히 많이 겹친다는 것을 알 수 있습니다. 따라서 알고 계신 기존 지식을 다른 SDK에도 쉽게 적용할 수 있습니다.

Azure Maps Android SDK는 Android 버전 API 21: Android 5.0.0(Lollipop) 이상을 지원합니다.

모든 예제는 Java로 제공되지만, Kotlin를 Azure Maps Android SDK와 함께 사용해도 됩니다.

Azure Maps에서 제공하는 Android SDK로 앱을 개발하는 방법에 대한 자세한 내용은 [Azure Maps Android SDK에 대한 방법 가이드](how-to-use-android-map-control-library.md)를 참조하세요.

## <a name="load-a-map"></a>맵 로드

Google 또는 Azure Maps를 사용하여 Android 앱에서 맵을 로드하는 방법은 여러 동일한 단계로 구성됩니다. 두 SDK 중 하나를 사용하는 경우 다음을 수행해야 합니다.

- 두 플랫폼 중 하나에 액세스하기 위한 API 또는 구독 키를 가져옵니다.
- 작업에 XML을 추가하여 맵을 렌더링할 위치 및 레이아웃을 지정합니다.
- 맵 보기를 포함하고 있는 작업의 모든 수명 주기 메서드를 map 클래스의 해당 항목으로 전달합니다. 특히 다음 메서드를 재정의해야 합니다.
    - `onCreate(Bundle)`
    - `onStart()`
    - `onResume()`
    - `onPause()`
    - `onStop()`
    - `onDestroy()`
    - `onSaveInstanceState(Bundle)`
    - `onLowMemory()`
- 맵이 준비될 때까지 기다렸다가 프로그래밍 방식으로 액세스를 시도합니다.

**이전: Google Maps**

Android용 Google Maps SDK를 사용하여 맵을 표시하려면 다음 단계를 수행합니다.

1.  Google Play 서비스가 설치되어 있는지 확인합니다.
2.  모듈의 **gradle.build** 파일에 Google Maps 서비스에 대한 종속성을 추가합니다. 

    `implementation 'com.google.android.gms:play-services-maps:17.0.0'`

1.  **google\_maps\_api.xml** 파일의 애플리케이션 섹션 안에 Google Maps API 키를 추가합니다.
    
    ```xml
    <meta-data android:name="com.google.android.geo.API_KEY" android:value="YOUR_GOOGLE_MAPS_KEY"/>
    ```

1.  다음과 같이 기본 작업에 맵 조각을 추가합니다.

    ```xml
    <com.google.android.gms.maps.MapView
            android:id="@+id/myMap"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>
    ```

1.  MainActivity.java 파일에서 Google Maps SDK에 대한 가져오기를 추가해야 합니다. 맵 보기를 포함하고 있는 작업의 모든 수명 주기 메서드를 map 클래스의 해당 항목으로 전달합니다. `MapView` 인스턴스는 `getMapAsync(OnMapReadyCallback)` 메서드를 사용하여 맵 조각에서 검색할 수 있습니다. `MapView`는 맵 시스템과 보기를 자동으로 초기화합니다. **MainActivity.java** 파일을 다음과 같이 편집합니다.

    ```java
    import com.google.android.gms.maps.GoogleMap;
    import com.google.android.gms.maps.MapView;
    import com.google.android.gms.maps.OnMapReadyCallback;
    
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    
    public class MainActivity extends AppCompatActivity implements OnMapReadyCallback {
    
        MapView mapView;
    
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mapView = findViewById(R.id.myMap);
    
            mapView.onCreate(savedInstanceState);
            mapView.getMapAsync(this);
        }
    
        @Override
    
        public void onMapReady(GoogleMap map) {
            //Add your post map load code here.
        }
    
        @Override
        public void onResume() {
            super.onResume();
            mapView.onResume();
        }
    
        @Override
        protected void onStart(){
            super.onStart();
            mapView.onStart();
        }
    
        @Override
        public void onPause() {
            super.onPause();
            mapView.onPause();
        }
    
        @Override
        public void onStop() {
            super.onStop();
            mapView.onStop();
        }
    
        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapView.onLowMemory();
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapView.onDestroy();
        }
    
        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapView.onSaveInstanceState(outState);
        }
    }
    ```

애플리케이션을 실행하면 다음과 같이 지도 컨트롤이 로드됩니다.

<center>

![간단한 Google Maps](media/migrate-google-maps-android-app/simple-google-maps.png)</center>

**이후: Azure Maps**

Android용 Azure Maps SDK를 사용하여 맵을 표시하려면 다음 단계를 수행합니다.

1. 최상위 **build.gradle** 파일을 열고, **all projects** 블록 섹션에 다음 코드를 추가합니다.

    ```JAVA
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. **app/build.gradle**을 업데이트하고 다음 코드를 추가합니다.
    
    1. 프로젝트의 **minSdkVersion**이 API 21 이상인지 확인합니다.

    2. Android 섹션에 다음 코드를 추가합니다.

        ```JAVA
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    3. 다음과 같이 종속성 블록을 업데이트하고 최신 Azure Maps Android SDK에 대한 새 구현 종속성 줄을 추가합니다.

        ```JAVA
        implementation "com.microsoft.azure.maps:mapcontrol:0.2"
        ```

        > [!Note]
        > Azure Maps Android SDK는 정기적으로 업그레이드되고 향상됩니다. [Android 지도 컨트롤 시작](how-to-use-android-map-control-library.md) 설명서에서 최신 Azure Maps 버전 번호를 확인할 수 있습니다. 또한 버전 번호를 "0.2"에서 "0+"로 설정하여 코드가 항상 최신 버전을 가리키게 할 수 있습니다.
    
    4. 도구 모음에서 **파일**로 이동한 다음, **Gradle 파일과 프로젝트 동기화**를 클릭합니다.
3. 다음과 같이 기본 작업에 맵 조각을 추가합니다(리소스 \> 레이아웃 \> 작업\_main.xml).
    
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
            />
    </FrameLayout>
    ```

4. **MainActivity.java** 파일에서 다음을 수행해야 합니다.
    
    * Azure Maps SDK에 대한 가져오기 추가
    * Azure Maps 인증 정보 설정
    * **onCreate** 메서드에서 지도 컨트롤 인스턴스 가져오기

    `setSubscriptionKey` 또는 `setAadProperties` 메서드를 사용하여 `AzureMaps` 클래스에 대한 인증 정보를 전역적으로 설정하면 보기마다 인증 정보를 추가할 필요가 없습니다. 

    지도 컨트롤에는 Android의 OpenGL 수명 주기를 관리하는 자체 수명 주기 메서드가 포함되어 있으며, 이러한 메서드는 포함된 작업에서 직접 호출해야 합니다. 지도 컨트롤의 수명 주기 메서드를 올바르게 호출하려면 지도 컨트롤을 포함하는 작업에서 다음 수명 주기 메서드를 재정의하고 각 지도 컨트롤 메서드를 호출해야 합니다. 

    * `onCreate(Bundle)` 
    * `onStart()` 
    * `onResume()` 
    * `onPause()` 
    * `onStop()` 
    * `onDestroy()` 
    * `onSaveInstanceState(Bundle)` 
    * `onLowMemory()`

    **MainActivity.java** 파일을 다음과 같이 편집합니다.
    
    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("<Your Azure Maps subscription key>");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);
    
            //Wait until the map resources are ready.
            mapControl.onReady(map -> {
                //Add your post map load code here.
    
            });
        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        protected void onStart(){
            super.onStart();
            mapControl.onStart();
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

애플리케이션을 실행하면 지도 컨트롤이 다음과 같이 로드됩니다.

<center>

![간단한 Azure Maps](media/migrate-google-maps-android-app/simple-azure-maps.png)</center>

Azure Maps 컨트롤은 보다 강력한 축소를 지원하고 더 많은 월드 보기를 제공합니다.

> [!TIP]
> Windows에서 Android 에뮬레이터를 사용하는 경우 OpenGL 및 소프트웨어 가속 그래픽 렌더링과의 충돌 때문에 맵이 렌더링되지 않을 수 있습니다. 다음과 같은 방법으로 이 이슈를 해결할 수 있습니다. AVD 관리자를 열고 편집할 가상 디바이스를 선택합니다. **구성 확인** 패널을 아래로 스크롤합니다. **에뮬레이트된 성능** 섹션에서 **그래픽** 옵션을 **하드웨어**로 설정합니다.

## <a name="localizing-the-map"></a>맵 지역화

대상 그룹이 여러 국가에 분산되어 있거나 여러 언어를 사용하는 경우 지역화가 중요합니다.

**이전: Google Maps**

다음 코드를 추가하여 기본 작업의 `onCreate` 메서드에서 맵의 언어를 설정할 수 있습니다. 맵의 컨텍스트 보기를 설정하기 전에 코드를 추가해야 합니다. 다음은 언어 코드 "fr"을 사용하여 언어를 프랑스어로 제한합니다.

```java
String languageToLoad = "fr";
Locale locale = new Locale(languageToLoad);
Locale.setDefault(locale);
Configuration config = new Configuration();
config.locale = locale;
getBaseContext().getResources().updateConfiguration(config,
        getBaseContext().getResources().getDisplayMetrics());
```

다음은 언어가 "fr"로 설정된 Google Maps의 예입니다.

<center>

![Google Maps 지역화](media/migrate-google-maps-android-app/google-maps-localization.png)</center>

**이후: Azure Maps**

Azure Maps는 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 첫 번째 옵션은 정적 메서드인 `setLanguage` 및 `setView` 메서드를 전역적으로 사용하여 언어 및 지역 보기 정보를 `AzureMaps` 클래스에 전달하는 것입니다. 이렇게 하면 앱에 로드된 모든 Azure Maps 컨트롤에서 기본 언어 및 지역 보기가 설정됩니다. 다음은 언어 코드 "fr-FR"을 사용하여 언어를 프랑스어로 제한합니다.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("auto");
}
```

두 번째 옵션은 언어 및 보기 정보를 지도 컨트롤 XML에 전달하는 것입니다.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="auto"
    />
```

세 번째 옵션은 맵 `setStyle` 메서드를 사용하여 맵의 언어 및 지역 보기를 프로그래밍 방식으로 설정하는 것입니다. 언제든지 이 작업을 수행하여 맵의 언어 및 지역 보기를 변경할 수 있습니다.

```java
mapControl.onReady(map -> {
    map.setStyle(StyleOptions.language("fr-FR"));
    map.setStyle(StyleOptions.view("auto"));
});
```

다음은 언어가 "fr-FR"로 설정된 Azure Maps의 예입니다.

<center>

![Azure Maps 지역화](media/migrate-google-maps-android-app/azure-maps-localization.png)</center>

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="setting-the-map-view"></a>맵 보기 설정

Azure Maps 및 Google Maps의 동적 맵은 적절한 메서드를 호출하여 프로그래밍 방식으로 새 지리적 위치로 이동할 수 있습니다. 아래 예제에서는 맵을 위성 항공 이미지에 표시하고, 좌표(위도: 35.0272, 경도: -111.0225)를 사용하여 맵 중심을 특정 위치로 이동하고, Google Maps에서 확대/축소 수준을 15로 변경하는 방법을 보여줍니다.

> [!NOTE]
> Google Maps는 크기가 256픽셀인 타일을 사용하고, Azure Maps는 이보다 큰 512픽셀 타일을 사용합니다. 따라서 Azure Maps에서 Google Maps와 동일한 맵 영역을 로드하는 데 필요한 네트워크 요청 수가 줄어듭니다. 그러나 지도 컨트롤에서 타일 피라미드가 작동하는 방식 때문에, 타일이 더 큰 Azure Maps에서는 Google Maps의 맵과 동일한 가시 영역을 얻으려면 Azure Maps를 사용할 때 Google Maps에 사용되는 확대/축소 수준에서 1을 빼야 합니다. 

**이전: Google Maps**

Google Maps 지도 컨트롤의 카메라는 맵의 중심 및 확대/축소 수준을 지정할 수 있는 `moveCamera` 메서드를 사용하여 프로그래밍 방식으로 이동할 수 있습니다. `setMapType` 메서드를 사용하여 표시되는 맵 유형을 변경할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.moveCamera(CameraUpdateFactory.newLatLngZoom(new LatLng(35.0272, -111.0225), 15));
    mapView.setMapType(GoogleMap.MAP_TYPE_SATELLITE);
}
```

<center>

![Google Maps 보기 설정](media/migrate-google-maps-android-app/google-maps-set-view.png)</center>

**이후: Azure Maps**

앞에서 설명했듯이, Azure Maps에서 동일한 가시 영역을 얻으려면 Google Maps에 사용되는 확대/축소 수준에서 1을 빼야 합니다. 이 예제에서는 확대/축소 수준 14를 사용합니다.

초기 맵 보기는 지도 컨트롤의 XML 특성에서 설정할 수 있습니다.

```xml
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_cameraLat="35.0272"
    app:mapcontrol_cameraLng="-111.0225"
    app:mapcontrol_zoom="14"
    app:mapcontrol_style="satellite"
    />
```

맵 보기는 맵 `setCamera` 및 `setStyle` 메서드를 사용하여 프로그래밍 방식으로 업데이트할 수 있습니다.

```java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(35.0272, -111.0225), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![Azure Maps 보기 설정](media/migrate-google-maps-android-app/azure-maps-set-view.png)</center>

**추가 리소스:**

- [지원되는 맵 스타일](supported-map-styles.md)

## <a name="adding-a-marker"></a>표식 추가

지점 데이터는 맵의 이미지를 사용하여 맵에 렌더링되는 경우가 많습니다. 이러한 이미지를 종종 표식, 압정, 핀 또는 기호라고 부릅니다. 다음 예제에서는 지점 데이터를 맵에 표식으로 렌더링합니다(위도: 51.5, 경도: -0.2).

**이전: Google Maps**

Google Maps를 사용하면 맵 `addMarker` 메서드를 사용하여 표식이 추가됩니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33)));
}
```

<center>

![Google Maps 표식](media/migrate-google-maps-android-app/google-maps-marker.png)</center>

**이후: Azure Maps**

Azure Maps에서, 먼저 데이터 원본에 데이터를 추가하여 지점 데이터를 맵에 렌더링할 수 있습니다. 그런 다음, 해당 데이터 원본을 기호 레이어에 연결합니다. 데이터 원본은 지도 컨트롤의 공간 데이터 관리를 최적화합니다. 기호 레이어는 이미지 및/또는 텍스트를 사용하여 지점 데이터를 렌더링하는 방법을 지정합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Create a symbol layer and add it to the map.
    map.layers.add(new SymbolLayer(dataSource));
});
```

<center>

![Azure Maps 표식](media/migrate-google-maps-android-app/azure-maps-marker.png)</center>

## <a name="adding-a-custom-marker"></a>사용자 지정 표식 추가

사용자 지정 이미지를 사용하여 맵에 지점을 나타낼 수 있습니다. 아래 예제의 맵은 사용자 지정 이미지를 사용하여 맵에 지점을 표시합니다. 지점은 위도 51.5 및 경도 -0.2입니다. 압정 아이콘의 지점이 맵의 올바른 위치와 일치하도록 맵이 표식의 위치를 오프셋합니다.

<center>

![노란색 압정 이미지](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

위의 두 예제에서 이미지는 앱 리소스의 drawable 폴더에 추가됩니다.

**이전: Google Maps**

Google Maps를 사용하면 표식에 사용자 지정 이미지를 사용할 수 있습니다. 표식의 `icon` 옵션을 사용하여 사용자 지정 이미지를 로드합니다. 이미지의 지점을 좌표에 맞추려면 `anchor` 옵션을 사용합니다. 앵커는 이미지 크기에 비례하며, 이 예에서는 너비 0.2단위, 높이 1단위입니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.addMarker(new MarkerOptions().position(new LatLng(47.64, -122.33))
    .icon(BitmapDescriptorFactory.fromResource(R.drawable.ylw_pushpin))
    .anchor(0.2f, 1f));
}
```

<center>

![Google Maps 사용자 지정 표식](media/migrate-google-maps-android-app/google-maps-custom-marker.png)</center>

**이후: Azure Maps**

Azure Maps의 기호 레이어는 사용자 지정 이미지를 지원하지만, 이미지를 맵 리소스에 로드하고 고유한 ID를 할당해야 합니다. 그러면 기호 레이어에서 이 ID를 참조할 수 있습니다. `iconOffset` 옵션을 사용하여 이미지의 올바른 지점과 일치하도록 기호를 오프셋합니다. 아이콘 오프셋은 픽셀 단위입니다. 기본적으로 오프셋은 이미지의 아래쪽 중심을 기준으로 하지만, `iconAnchor` 옵션을 사용하여 이 오프셋 값을 조정할 수 있습니다. 이 예제에서는 `iconAnchor` 옵션을 `"center"`로 설정하고 아이콘 오프셋을 사용하여 이미지를 오른쪽으로 5픽셀, 위로 15픽셀 이동하여 압정 이미지의 지점에 맞춥니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create a point feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

    //Load the custom image icon into the map resources.
    map.images.add("my-yellow-pin", R.drawable.ylw_pushpin);

    //Create a symbol that uses the custom image icon and add it to the map.
    map.layers.add(new SymbolLayer(dataSource,
        iconImage("my-yellow-pin"),
        iconAnchor(AnchorType.CENTER
        iconOffset(new Float[]{5f, -15f})));
});
```

<center>

![Azure Maps 사용자 지정 표식](media/migrate-google-maps-android-app/azure-maps-custom-marker.png)</center>

## <a name="adding-a-polyline"></a>폴리라인 추가

폴리라인은 맵에 선 또는 경로를 나타내는 데 사용됩니다. 다음 예제에서는 맵에 파선 폴리라인을 만드는 방법을 보여줍니다.

**이전: Google Maps**

Google Maps에서는 `PolylineOptions` 클래스를 사용하여 폴리라인을 만든 후 `addPolyline` 메서드를 사용하여 맵에 추가할 수 있습니다. 스트로크 색은 `color` 옵션을 사용하여 설정하고, 스트로크 너비는 width 옵션을 사용하여 설정하고, 스트로크 파선 배열은 `pattern` 옵션을 사용하여 설정할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polyline.
    PolylineOptions lineOptions = new PolylineOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .color(Color.RED)
            .width(10f)
            .pattern(Arrays.<PatternItem>asList(
                    new Dash(30f), new Gap(30f)));

    //Add the polyline to the map.
    Polyline polyline = mapView.addPolyline(lineOptions);
}
```

<center>

![Google Maps 폴리라인](media/migrate-google-maps-android-app/google-maps-polyline.png)</center>

**이후: Azure Maps**

Azure Maps에서 폴리라인을 `LineString` 또는 `MultiLineString` 개체라고 합니다. 이러한 개체는 선 계층을 사용하여 데이터 원본에 추가하고 렌더링할 수 있습니다. 스트로크 너비 및 파선 배열 "픽셀" 단위는 두 SDK에 같은 값을 사용하면 동일한 결과를 생성한다는 점에서 Azure Maps 웹 SDK와 동일합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of points.
    List<Point> points = Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46));

    //Create a LineString feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(LineString.fromLngLats(points)));

    //Create a line layer and add it to the map.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(4f),
        strokeDashArray(new Float[]{3f, 3f})));
});
```

<center>

![Azure Maps 폴리라인](media/migrate-google-maps-android-app/azure-maps-polyline.png)</center>

## <a name="adding-a-polygon"></a>다각형 추가

다각형은 맵의 영역을 나타내는 데 사용됩니다. 다음 예제에서는 맵의 가운데 좌표를 기준으로 삼각형을 형성하는 다각형을 만드는 방법을 보여줍니다.

**이전: Google Maps**

Google Maps에서는 `PolygonOptions` 클래스를 사용하여 다각형을 만든 후 `addPolygon` 메서드를 사용하여 맵에 추가할 수 있습니다. 채우기 및 스트로크 색은 `fillColor` 및 `strokeColor` 옵션을 사용하여 설정할 수 있고, 스트로크 너비는 `strokeWidth` 옵션을 사용하여 설정할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the polygon.
    PolygonOptions polygonOptions = new PolygonOptions()
            .add(new LatLng(46, -123))
            .add(new LatLng(49, -122))
            .add(new LatLng(46, -121))
            .add(new LatLng(46, -123))  //Close the polygon.
            .fillColor(Color.argb(128, 0, 128, 0))
            .strokeColor(Color.RED)
            .strokeWidth(5f);

    //Add the polygon to the map.
    Polygon polygon = mapView.addPolygon(polygonOptions);
}
```

<center>

![Google Maps 다각형](media/migrate-google-maps-android-app/google-maps-polygon.png)</center>

**이후: Azure Maps**

Azure Maps에서는 계층을 사용하여 `Polygon` 및 `MultiPolygon` 개체를 데이터 원본에 추가하고 이를 지도에 렌더링할 수 있습니다. 다각형 영역은 다각형 계층에 렌더링할 수 있습니다. 다각형 윤곽선은 선 계층을 사용하여 렌더링할 수 있습니다. 스트로크 너비 및 파선 배열 "픽셀" 단위는 두 SDK에 같은 값을 사용하면 동일한 결과를 생성한다는 점에서 Azure Maps 웹 SDK와 동일합니다.

```java
mapControl.onReady(map -> {
    //Create a data source and add it to the map.
    DataSource dataSource = new DataSource();
    map.sources.add(dataSource);

    //Create an array of point arrays to create polygon rings.
    List<List<Point>> rings = Arrays.asList(Arrays.asList(
        Point.fromLngLat(-123, 46),
        Point.fromLngLat(-122, 49),
        Point.fromLngLat(-121, 46),
        Point.fromLngLat(-123, 46)));

    //Create a Polygon feature and add it to the data source.
    dataSource.add(Feature.fromGeometry(Polygon.fromLngLats(rings)));

    //Add a polygon layer for rendering the polygon area.
    map.layers.add(new PolygonLayer(dataSource,
        fillColor("green"),
        fillOpacity(0.5f)));

    //Add a line layer for rendering the polygon outline.
    map.layers.add(new LineLayer(dataSource,
        strokeColor("red"),
        strokeWidth(2f)));
});
```

<center>

![Azure Maps 다각형](media/migrate-google-maps-android-app/azure-maps-polygon.png)</center>

## <a name="overlay-a-tile-layer"></a>타일 레이어 오버레이

 타일 레이어를 사용하면 작은 바둑판식 이미지로 분할된 레이어 이미지를 오버레이할 수 있으며, 지도 바둑판식 배열 시스템에 맞춰집니다. 이 방법은 레이어 이미지 또는 큰 데이터 세트를 오버레이하는 일반적인 방법입니다. Google Maps에서는 타일 레이어를 이미지 오버레이라고 합니다.

다음 예제에서는 아이오와 주립 대학교의 Iowa Environmental Mesonet에서 받은 날씨 레이더 타일 레이어를 오버레이합니다. 타일의 크기는 256픽셀입니다.

**이전: Google Maps**

Google Maps에서는 `TileOverlayOptions` 클래스를 사용하여 타일 레이어를 맵 맨 위에 오버레이하고 `addTileLauer` 메서드를 사용하여 맵에 추가할 수 있습니다. 타일을 반투명하게 만들기 위해 `transparency` 옵션이 0.2 또는 20% 투명으로 설정됩니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    //Create the options for the tile layer.
    TileOverlayOptions tileLayer = new TileOverlayOptions()
        .tileProvider(new UrlTileProvider(256, 256) {
            @Override
            public URL getTileUrl(int x, int y, int zoom) {

                try {
                    //Define the URL pattern for the tile images.
                    return new URL(String.format("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/%d/%d/%d.png", zoom, x, y));
                }catch (MalformedURLException e) {
                    throw new AssertionError(e);
                }
            }
        }).transparency(0.2f);

    //Add the tile layer to the map.
    mapView.addTileOverlay(tileLayer);
}
```

<center>

![Google Maps 타일 레이어](media/migrate-google-maps-android-app/google-maps-tile-layer.png)</center>

**이후: Azure Maps**

타일 레이어를 다른 레이어와 비슷한 방식으로 맵에 추가할 수 있습니다. x, y 및 확대/축소 자리 표시자가 있는 형식이 지정된 URL `{x}`, `{y}`, `{z}`는 각각 타일에 액세스할 위치를 레이어에 알리는 데 사용됩니다. Azure Maps의 타일 레이어는 `{quadkey}`, `{bbox-epsg-3857}` 및 `{subdomain}` 자리 표시자도 지원합니다. 타일 레이어를 반투명하게 만들기 위해 불투명도 값 0.8이 사용됩니다. 불투명도와 투명도는 비슷하지만 반전된 값을 사용합니다. 둘 사이에서 변환하려면 숫자 1에서 해당 값을 빼면 됩니다.

> [!TIP]
> Azure Maps에서는 기본 지도 계층을 비롯한 다른 계층 아래에 레이어를 쉽게 렌더링할 수 있습니다. 쉽게 읽을 수 있도록 맵 레이블 아래에 타일 레이어를 렌더링하는 것이 좋은 경우가 많습니다. `map.layers.add` 메서드는 아래에 새 레이어를 삽입할 레이어의 ID인 두 번째 매개 변수를 사용합니다. 맵 레이블 아래에 타일 레이어를 삽입하려면 `map.layers.add(myTileLayer, "labels");` 코드를 사용하면 됩니다.

```java
mapControl.onReady(map -> {
    //Add a tile layer to the map, below the map labels.
    map.layers.add(new TileLayer(
        tileUrl("https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png"),
        opacity(0.8f),
        tileSize(256)
    ), "labels");
});
```

<center>

![Azure Maps 타일 레이어](media/migrate-google-maps-android-app/azure-maps-tile-layer.png)</center>

## <a name="show-traffic"></a>트래픽 표시

트래픽 데이터는 Azure Maps와 Google 맵에 모두 오버레이할 수 있습니다.

**이전: Google Maps**

Google Maps에서는 맵의 `setTrafficEnabled` 메서드에 true를 전달하여 맵의 맨 위에 트래픽 흐름 데이터를 오버레이할 수 있습니다.

```java
@Override
public void onMapReady(GoogleMap googleMap) {
    mapView = googleMap;

    mapView.setTrafficEnabled(true);
}
```

<center>

![Google Maps 트래픽](media/migrate-google-maps-android-app/google-maps-traffic.png)</center>

**이후: Azure Maps**

Azure Maps는 트래픽을 표시하는 여러 가지 옵션을 제공합니다. 도로 폐쇄나 사고 같은 트래픽 인시던트를 맵에 아이콘으로 표시할 수 있습니다. 트래픽 흐름과 컬러 코딩된 도로를 맵에 오버레이할 수 있습니다. 게시된 속도 제한, 일반적인 예상 지연 시간 또는 절대 지연 시간을 기준으로 표시되도록 색을 수정할 수 있습니다. Azure Maps의 인시던트 데이터는 1분마다 업데이트되고 흐름 데이터는 2분마다 업데이트됩니다.

```java
mapControl.onReady(map -> {
    map.setTraffic(
        incidents(true),
        flow(TrafficFlow.RELATIVE));
});
```

<center>

![Azure Maps 트래픽](media/migrate-google-maps-android-app/azure-maps-traffic.png)</center>

## <a name="next-steps"></a>다음 단계

Azure Maps Android SDK에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Android 지도 컨트롤을 사용하는 방법](how-to-use-android-map-control-library.md)
