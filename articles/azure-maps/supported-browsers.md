---
title: 웹 SDK 지원 브라우저 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK에 대해 지원 되는 브라우저와 브라우저가 지원 되는 브라우저 인지 확인 하는 방법을 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988790"
---
# <a name="web-sdk-supported-browsers"></a>웹 SDK 지원 브라우저

Azure Maps 웹 SDK는 [atlas. isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)라는 도우미 함수를 제공 합니다. 이 함수는 지도 컨트롤 로드 및 렌더링을 지 원하는 데 필요한 최소 WebGL 기능 집합이 웹 브라우저에 있는지 여부를 검색 합니다. 함수를 사용 하는 방법의 예는 다음과 같습니다.

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>데스크톱

Azure Maps 웹 SDK는 다음 데스크톱 브라우저를 지원 합니다.

- Microsoft Edge (현재 및 이전 버전)
- Google Chrome (현재 및 이전 버전)
- Mozilla Firefox (현재 및 이전 버전)
- Apple Safari (Mac OS X) (현재 버전 및 이전 버전)

또한이 문서의 뒷부분에 있는 [레거시 브라우저 대상](#Target-Legacy-Browsers) 을 참조 하세요.

## <a name="mobile"></a>모바일

Azure Maps 웹 SDK는 다음과 같은 모바일 브라우저를 지원 합니다.

- Android
  - Android 6.0 이상 버전의 Chrome 최신 버전
  - Android 6.0 이상 버전의 Chrome 웹 보기
- iOS
  - IOS의 현재 및 이전 주 버전의 모바일 Safari
  - 현재 및 이전 주 버전의 iOS에 대한 UIWebView 보기 및 WKWebView
  - IOS 용 Chrome의 현재 버전

> [!TIP]
> 웹 보기 컨트롤을 사용 하 여 모바일 응용 프로그램 내에 맵을 포함 하는 경우 Azure Content Delivery Network에서 호스트 되는 SDK 버전을 참조 하는 대신 [Azure Maps 웹 SDK의 npm 패키지](https://www.npmjs.com/package/azure-maps-control) 를 사용 하는 것이 좋습니다. 이 방법은 SDK가 이미 사용자의 장치에 있고 런타임에 다운로드할 필요가 없기 때문에 로드 시간이 줄어듭니다.

## <a name="nodejs"></a>Node.js

다음 웹 SDK 모듈은 node.js 에서도 지원 됩니다.

- 서비스 모듈 ([설명서](how-to-use-services-module.md) | [npm 모듈](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>대상 레거시 브라우저

WebGL을 지원 하지 않거나 제한적 으로만 지원 되는 이전 브라우저를 대상으로 지정할 수 있습니다. 이러한 경우 [Leaflet](https://leafletjs.com/)와 같은 오픈 소스 맵 컨트롤과 함께 Azure Maps 서비스를 사용 하는 것이 좋습니다. 예를 들면 다음과 같습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
<a href='https://codepen.io'>CodePen</a>에서 Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure Maps + Leaflet</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)를 참조 하세요.
</iframe>


## <a name="next-steps"></a>다음 단계

Azure Maps 웹 SDK에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [지도 컨트롤](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [서비스 모듈](how-to-use-services-module.md)
