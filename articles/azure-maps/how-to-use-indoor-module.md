---
title: Azure Maps Indoor Maps 모듈 사용
description: Microsoft Azure Maps Indoor Maps 모듈을 사용하여 모듈의 JavaScript 라이브러리를 포함하여 맵을 렌더링하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 4bfb017bb085d22c187e8074ba4f2b026d17f442
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815949"
---
# <a name="use-the-azure-maps-indoor-maps-module"></a>Azure Maps Indoor Maps 모듈 사용

Azure Maps Web SDK에는 *Azure Maps Indoor* 모듈이 포함되어 있습니다. *Azure Maps Indoor* 모듈을 사용하면 Azure Maps Creator에서 만든 실내 맵을 렌더링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Creator 리소스를 만듭니다](how-to-manage-creator.md).
3. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).
4. [실내 맵 만들기 자습서](tutorial-creator-indoor-maps.md)를 완료하여 `tilesetId`와 `statesetId`를 확보합니다.
 Azure Maps Indoor Maps 모듈을 사용하여 실내 맵을 렌더링하려면 이러한 식별자를 사용해야 합니다.

## <a name="embed-the-indoor-maps-module"></a>Indoor Maps 모듈 포함

두 가지 방법 중 하나로 *Azure Maps Indoor* 모듈을 설치하고 포함할 수 있습니다.

전역적으로 호스팅되는 Azure Content Delivery Network 버전의 *Azure Maps Indoor* 모듈을 사용하려면, HTML 파일의 `<head>` 요소에 다음 JavaScript 및 스타일시트를 참조합니다.

```html
<link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>
<script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
```

 또는 *Azure Maps Indoor* 모듈을 다운로드할 수 있습니다. *Azure Maps Indoor* 모듈은 Azure Maps 서비스에 액세스하기 위한 클라이언트 라이브러리를 포함합니다. 아래 단계에 따라 *Indoor* 모듈을 웹 애플리케이션에 설치하고 로드합니다.  
  
  1. [Azure maps-실내 패키지](https://www.npmjs.com/package/azure-maps-indoor)를 설치 합니다.
  
      ```powershell
      >npm install azure-maps-indoor
      ```

  2. HTML 파일의 `<head>` 요소에서 *Azure Maps Indoor* 모듈 JavaScript 및 스타일시트를 참조합니다.

      ```html
      <link rel="stylesheet" href="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.css" type="text/css" />
      <script src="node_modules/azure-maps-drawing-tools/dist/atlas-indoor.min.js"></script>
      ```

## <a name="instantiate-the-map-object"></a>Map 개체 인스턴스화

먼저 *Map* 개체를 만듭니다. *Map* 개체는 다음 단계에서 *Indoor Manager* 개체를 인스턴스화하는 데 사용됩니다.  아래 코드는 *Map* 개체를 인스턴스화하는 방법을 보여줍니다.

```javascript
const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";

const map = new atlas.Map("map-id", {
  //use your facility's location
  center: [-122.13315, 47.63637],
  //or, you can use bounds: [# west, # south, # east, # north] and replace # with your map's bounds
  style: "blank",
  view: 'Auto',
  authOptions: {
      authType: 'subscriptionKey',
      subscriptionKey: subscriptionKey
  },
  zoom: 19,
});
```

## <a name="instantiate-the-indoor-manager"></a>Indoor Manager 인스턴스화

실내 타일 세트와 타일의 맵 스타일을 로드하려면 *Indoor Manager*를 인스턴스화해야 합니다. *Map* 개체 및 해당 `tilesetId`를 제공하여 *Indoor Manager*를 인스턴스화합니다. [동적 맵 스타일 지정](indoor-map-dynamic-styling.md)을 지원하려면 `statesetId`를 전달해야 합니다. `statesetId` 변수 이름은 대/소문자를 구분합니다. 코드는 아래 JavaScript와 같아야 합니다.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

제공한 상태 데이터의 폴링이 가능하도록 설정하려면 `statesetId`를 제공하고 `indoorManager.setDynamicStyling(true)`을 호출해야 합니다. 폴링 상태 데이터를 사용하면 *state* 또는 동적 속성의 상태를 동적으로 업데이트할 수 있습니다. 예를 들어 room과 같은 기능에는 `occupancy`라는 동적 속성(*상태*)이 있을 수 있습니다. 애플리케이션은 시각 맵 내부의 변화를 반영하기 위해 *state* 변경 사항이 있는지 폴링할 수 있습니다. 아래 코드는 상태 폴링을 사용하도록 설정하는 방법을 보여줍니다.

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}
```

## <a name="indoor-level-picker-control"></a>Indoor Level Picker 컨트롤

 *Indoor Level Picker* 컨트롤을 사용하여 렌더링된 맵의 수준을 변경할 수 있습니다. 필요에 따라 *Indoor Manager*를 통해 *Indoor Level Picker* 컨트롤을 초기화할 수 있습니다. Level Control Picker를 초기화하는 코드는 다음과 같습니다.

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## <a name="indoor-events"></a>Indoor 이벤트

 *Azure Maps Indoor* 모듈은 *Map* 개체 이벤트를 지원합니다. *Map* 개체 이벤트 수신기는 수준이나 설비가 변경되면 호출됩니다. 수준이나 설비가 변경되었을 때 코드를 실행하려면 수신기 내부에 코드를 배치합니다. 아래 코드는 *Map* 개체에 이벤트 수신기를 추가하는 방법을 보여줍니다.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

  //code that you want to run after a level has been changed
  console.log("The level has changed: ", eventData);
});

map.events.add("facilitychanged", indoorManager, (eventData) => {

  //code that you want to run after a facility has been changed
  console.log("The facility has changed: ", eventData);
});
```

`eventData` 변수는 각각 `levelchanged` 또는 `facilitychanged` 이벤트를 호출한 수준이나 설비에 대한 정보를 포함합니다. 수준이 변경되면 `eventData` 개체에 `facilityId`, 새 `levelNumber` 및 기타 메타데이터가 포함됩니다. 설비가 변경되면 `eventData` 개체에 새 `facilityId`, 새 `levelNumber` 및 기타 메타데이터가 포함됩니다.

## <a name="example-use-the-indoor-maps-module"></a>예제: Indoor Maps 모듈 사용

이 예제는 웹 애플리케이션에서 *Azure Maps Indoor* 모듈을 사용하는 방법을 보여줍니다. 예제의 범위는 제한되지만 *Azure Maps Indoor* 모듈 사용을 시작하는 데 필요한 기본 사항을 설명합니다. 전체 HTML 코드는 다음 단계 아래에 있습니다.

1. Azure Content Delivery Network [옵션](#embed-the-indoor-maps-module)를 사용하여 *Azure Maps Indoor* 모듈을 설치합니다.

2. 새 HTML 파일 만들기

3. HTML 헤더에서 *Azure Maps Indoor* 모듈 JavaScript 및 스타일시트 스타일을 참조합니다.

4. *Map* 개체를 초기화합니다. *Map* 개체는 다음과 같은 옵션을 지원합니다.
    - `Subscription key`는 Azure Maps 기본 구독 키입니다.
    - `center`는 실내 맵 가운데 위치의 위도와 경도를 정의합니다. `bounds` 값을 제공하지 않으려면 `center` 값을 제공합니다. 형식은 `center`: [-122.13315, 47.63637]과 같이 표시되어야 합니다.
    - `bounds`는 타일 세트 맵 데이터가 포함된 가장 작은 직사각형 모양입니다. `center` 값을 설정하지 않으려면 `bounds` 값을 설정합니다. [Tileset List API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview)를 호출하여 맵 경계를 찾을 수 있습니다. Tileset List API는 `bbox`를 반환하며, 이것을 파싱하여 `bounds`에 할당할 수 있습니다. 형식은 `bounds` [# 서 부, # 남부, # 동부, # 북부]로 표시 되어야 합니다.
    - `style`을 사용하면 배경색을 설정할 수 있습니다. 흰색 배경을 표시하려면 `style`을 "blank"로 정의합니다.
    - `zoom`을 사용하면 맵의 최소 및 최대 확대/축소 수준을 지정할 수 있습니다.

5. 다음으로 *Indoor Manager* 모듈을 만듭니다. *Azure Maps Indoor* `tilesetId`를 할당하고 선택적으로 `statesetId`를 추가합니다.

6. *Indoor Level Picker* 컨트롤을 인스턴스화합니다.

7. *Map* 개체 이벤트 수신기를 추가합니다.  

이제 파일이 아래 HTML과 유사한 모양입니다.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
      
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css"/>

      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<Your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tilesetId>";
        const statesetId = "<your statesetId>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [# west, # south, # east, # north] and replace # with your Map bounds
          style: "blank",
          view: 'Auto',
          authOptions: { 
              authType: 'subscriptionKey',
              subscriptionKey: subscriptionKey
          },
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

실내 맵을 보려면 웹 브라우저에 로드합니다. 아래 이미지와 같은 모양입니다. 계단 공간을 클릭하면 *수준 선택기*가 오른쪽 위 모서리에 나타납니다.

  ![실내 맵 이미지](media/how-to-use-indoor-module/indoor-map-graphic.png)

## <a name="next-steps"></a>다음 단계

*Azure Maps Indoor* 모듈과 관련된 API에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [드로잉 패키지 요구 사항](drawing-requirements.md)

>[!div class="nextstepaction"]
> [실내 맵용 Creator](creator-indoor-maps.md)

맵에 데이터를 더 추가하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Indoor Maps 동적 스타일 지정](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [코드 샘플](https://docs.microsoft.com/samples/browse/?products=azure-maps)