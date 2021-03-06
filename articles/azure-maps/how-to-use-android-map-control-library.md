---
title: Android 지도 컨트롤 시작 | Microsoft Azure Maps
description: Azure Maps Android SDK에 대해 잘 알고 있어야 합니다. Android Studio에서 프로젝트를 만들고, SDK를 설치 하 고, 대화형 지도를 만드는 방법을 참조 하세요.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: a60ee8faf8d19afba59e46c52aaba9395c3a5292
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604450"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Azure Maps Android SDK 시작

Azure Maps Android SDK는 Android용 벡터 지도 라이브러리입니다. 이 문서에서는 Azure Maps Android SDK를 설치하고 맵을 로드하는 프로세스를 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작: Android 앱 만들기](quick-android-map.md) 문서의 단계를 완료 해야 합니다.

## <a name="localizing-the-map"></a>맵 지역화

Azure Maps Android SDK는 맵의 언어 및 지역 보기를 설정하는 세 가지 방법을 제공합니다. 다음 코드에서는 언어를 프랑스어 ("fr-fr")로 설정 하 고 지역 보기를 "Auto"로 설정 하는 방법을 보여 줍니다.

첫 번째 옵션은 정적 `setLanguage` 및 `setView` 메서드를 전역적으로 사용하여 `AzureMaps` 클래스에 언어를 전달하고 지역 정보를 보는 것입니다. 이렇게 하면 앱에 로드된 모든 Azure Maps 컨트롤에서 기본 언어 및 지역 보기가 설정됩니다.

::: zone pivot="programming-language-java-android"

```java
static {
    //Alternatively use Azure Active Directory authenticate.
    AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

    //Set your Azure Maps Key.
    //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");   

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
companion object {
    init {
        //Alternatively use Azure Active Directory authenticate.
        AzureMaps.setAadProperties("<Your aad clientId>", "<Your aad AppId>", "<Your aad Tenant>");

        //Set your Azure Maps Key.
        //AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");
    
        //Set the language to be used by Azure Maps.
        AzureMaps.setLanguage("fr-FR");
    
        //Set the regional view to be used by Azure Maps.
        AzureMaps.setView("Auto");
    }
}
```

::: zone-end

두 번째 옵션은 언어 및 보기 정보를 지도 컨트롤 XML에 전달하는 것입니다.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

세 번째 옵션은 맵 `setStyle` 메서드를 사용하여 맵의 언어 및 지역 보기를 프로그래밍 방식으로 설정하는 것입니다. 언제든지 이 작업을 수행하여 맵의 언어 및 지역 보기를 변경할 수 있습니다.

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl.onReady(OnReady { map: AzureMap ->
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    )
})
```

::: zone-end

언어가 "fr-fr"로 설정 되 고 지역 보기가 "Auto"로 설정 된 Azure Maps의 예는 다음과 같습니다.

![Azure Maps, 프랑스어로 레이블을 표시하는 맵 이미지](media/how-to-use-android-map-control-library/android-localization.png)

지원되는 언어 및 지역 보기의 전체 목록은 [여기](supported-languages.md)에 설명되어 있습니다.

## <a name="navigating-the-map"></a>맵 탐색

여러 가지 방식으로 맵을 확대/축소, 이동, 회전 및 피치할 수 있습니다. 다음에서 맵을 탐색하는 다양한 방법을 자세히 설명합니다.

**맵 확대/축소**

* 두 손가락으로 맵을 터치하고 두 손가락을 모으면 축소되고 두 손가락을 넓히면 확대됩니다.
* 한 수준 확대하려면 맵을 두 번 탭합니다.
* 두 손가락을 두 번 탭 하 여 지도를 한 수준 축소 합니다.
* 두 번 탭할 때 두 번째 탭에서 손가락을 맵에 고정한 채로 위나 아래로 끌면 확대 또는 축소됩니다.

**맵 이동**

* 맵을 터치하고 원하는 방향으로 끕니다.

**맵 회전**

* 두 손가락으로 맵을 터치하고 회전합니다.

**맵 피치**

* 두 손가락으로 맵을 터치하고 위나 아래로 함께 끕니다.

## <a name="azure-government-cloud-support"></a>Azure Government 클라우드 지원

Azure Maps Android SDK는 Azure Government 클라우드를 지원 합니다. Azure Maps Android SDK는 동일한 Maven 리포지토리에서 액세스 됩니다. Azure Maps 플랫폼의 Azure Government 클라우드 버전에 연결 하려면 다음 작업을 수행 해야 합니다.

Azure Maps 인증 세부 정보가 지정 된 위치에서 다음 코드 줄을 추가 하 여 map에 Azure Maps 정부 클라우드 도메인을 사용 하도록 지시 합니다.

::: zone pivot="programming-language-java-android"

```java
AzureMaps.setDomain("atlas.azure.us");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
AzureMaps.setDomain("atlas.azure.us")
```

::: zone-end

지도와 서비스를 인증 하는 경우 Azure Government 클라우드 플랫폼에서 Azure Maps 인증 세부 정보를 사용 해야 합니다.

## <a name="next-steps"></a>다음 단계

맵에 오버레이 데이터를 추가하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps의 인증 관리](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Android 맵에서 맵 스타일 변경](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [기호 계층 추가](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [선 계층 추가](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](how-to-add-shapes-to-android-map.md)
