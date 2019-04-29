---
title: 지원 되는 브라우저-Azure Maps 웹 SDK | Microsoft Docs
description: Azure Maps 웹 SDK에 대 한 지원 되는 브라우저에 알아봅니다
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: 84c5dbcf5073ba8c0ae662af019cde590a9adf10
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62738391"
---
# <a name="web-sdk-supported-browsers"></a>웹 SDK 지원 브라우저

라는 도우미 함수를 제공 하는 Azure Maps 웹 SDK [atlas.isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)합니다. 이 함수는 웹 브라우저를 로드 하 고 지도 컨트롤 렌더링을 지 원하는 데 필요한 WebGL 기능의 최소 집합에 있는지 여부를 검색 합니다. 함수를 사용 하는 방법의 예는 다음과 같습니다.

```
if(!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if(!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>데스크톱

Azure Maps 웹 SDK는 다음 데스크톱 브라우저를 지원합니다.

- Microsoft Edge (현재 및 이전 버전)
- Google Chrome (최신 및 이전 버전)
- Mozilla Firefox (최신 및 이전 버전)
- Apple Safari (Mac OS X) (현재 및 이전 버전)

참고 항목 [레거시 브라우저 대상](#Target-Legacy-Browsers) 이 문서의 뒷부분에 나오는.

## <a name="mobile"></a>모바일

Azure Maps 웹 SDK는 다음과 같은 모바일 브라우저를 지원합니다.

- Android
  - 현재 버전의 Chrome Android 6.0 이상
  - Android 6.0 이상 chrome 웹 보기
- iOS
  - IOS의 현재 버전과 이전 주 버전의 모바일 Safari
  - UIWebView 및 WKWebView 현재 및 이전 주요 버전의 iOS
  - IOS에 대 한 현재 버전의 Chrome

> [!TIP]
> WebView 컨트롤을 사용 하 여 모바일 응용 프로그램 내에서 지도 포함 하는 경우 사용할 수도 있습니다는 [npm 패키지는 Azure Maps 웹 SDK의](https://www.npmjs.com/package/azure-maps-control) Azure 콘텐츠 배달에서 호스트 되는 SDK의 버전을 참조 하는 대신 네트워크입니다. 이 방법은 SDK 사용자의 장치에 이미 있을 고 런타임 시 다운로드할 필요가 없습니다 때문에 로드 시간을 줄입니다.

## <a name="nodejs"></a>Node.js

다음 웹 SDK 모듈은 node.js에서 에서도 지원 됩니다.

- Services 모듈 ([설명서](how-to-use-services-module.md) | [npm 모듈](https://www.npmjs.com/package/azure-maps-rest))

## <a name="Target-Legacy-Browsers"></a>대상 레거시 브라우저

WebGL을 지원 하지 않는 또는 지원을 제한 되어 있는 오래 된 브라우저를 대상으로 지정할 수 있습니다. 이러한 경우에 Azure Maps 서비스와 같은 오픈 소스 맵 컨트롤을 함께 사용 하는 권장 [광고 지](https://leafletjs.com/)합니다. 예를 들면 다음과 같습니다.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure Maps + 광고 지" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
펜을을 참조 하세요 <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>광고 지 + Azure Maps</a> 에서 Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에서 <a href='https://codepen.io'>CodePen</a>합니다.
</iframe>


## <a name="next-steps"></a>다음 단계

Azure Maps 웹 SDK에 대 한 자세한 정보:

> [!div class="nextstepaction"]
> [지도 컨트롤](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Services 모듈](how-to-use-services-module.md)
