---
title: Microsoft Azure Maps 개요
description: Microsoft Azure Maps의 서비스 및 기능으로 무엇이 있으며 애플리케이션에서 어떻게 사용할 수 있는지 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/31/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 6422874f57b490773f70b6fb27d5b65c654ded07
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212742"
---
# <a name="what-is-azure-maps"></a>Azure Maps란?

Azure Maps는 최신 매핑 데이터를 사용하여 웹 및 모바일 애플리케이션에 지리적 컨텍스트를 제공하는 지리 공간적 서비스 및 SDK 컬렉션입니다. Azure Maps는 다음을 제공합니다.

* 벡터 및 래스터 맵을 여러 스타일과 위성 이미지로 렌더링하는 REST API
* 프라이빗 실내 맵 데이터를 기반으로 맵을 만들고 렌더링하는 작성자 서비스
* 전 세계의 주소, 장소 및 관심 지점을 찾는 검색 서비스
* 지점 간, 다중 지점, 다중 지점 최적화, 등시선, 전기차, 상용차, 교통 영향, 매트릭스 등의 다양한 라우팅 옵션
* 실시간 트래픽 정보가 필요한 애플리케이션을 위한 트래픽 흐름 보기 및 인시던트 보기
* 대중 교통 정보를 요청하고 다양한 이동 모드와 실시간 도착 정보를 혼합하여 경로를 계획하는 Mobility Service
* 표준 시간대 및 지리적 위치 서비스
* 위치 정보가 Azure에서 호스팅되는 지오펜싱 서비스 및 매핑 데이터 스토리지
* 지리 공간 분석을 통한 위치 인텔리전스.

또한 웹 SDK 및 Android SDK를 통해 Azure Maps 서비스를 사용할 수 있습니다. 이러한 도구는 개발자들이 위치 정보를 Azure 솔루션으로 통합하는 솔루션을 신속하게 개발하고 확장하는 데 유용합니다.

지금 바로 [Azure Maps 계정](https://azure.microsoft.com/services/azure-maps/) 체험판에 가입하여 개발을 시작할 수 있습니다.

다음 비디오는 Azure Maps를 자세히 설명합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>맵 컨트롤

### <a name="web-sdk"></a>웹 SDK

Azure Maps 웹 SDK를 사용하면 고유한 콘텐츠와 이미지를 사용하여 대화형 맵을 사용자 지정할 수 있습니다. 웹 또는 모바일 애플리케이션 모두에 대해 이 대화형 맵을 사용할 수 있습니다. 맵 컨트롤을 통해 WebGL을 사용하여 고성능의 큰 데이터 세트를 렌더링할 수 있습니다. JavaScript 또는 TypeScript를 사용하여 SDK로 개발할 수 있습니다.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Azure Maps 웹 SDK를 사용하여 만든 채우기 변경 맵 예제":::

### <a name="android-sdk"></a>Android SDK

Azure Maps Android SDK를 사용하여 모바일 매핑 애플리케이션을 만듭니다.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="모바일 디바이스의 맵 예제":::

## <a name="services-in-azure-maps"></a>Azure Maps의 서비스

Azure Maps는 Azure 애플리케이션에 지리적 컨텍스트를 제공할 수 있는 다음과 같은 서비스로 구성됩니다.

### <a name="data-service"></a>데이터 서비스

데이터는 맵에 필수적입니다. Data Service를 사용하여 공간 작업 또는 이미지 구성에 사용할 지리 공간 데이터를 업로드하고 저장합니다.  고객 데이터를 Azure Maps 서비스에 더 가까이 가져오면 대기 시간이 줄어들고 생산성이 향상되며 애플리케이션에서 새 시나리오를 만들 수 있습니다. 이 서비스에 대해 자세히 알아보려면 [Data Service 설명서](https://docs.microsoft.com/rest/api/maps/data)를 참조하세요.

### <a name="geolocation-service"></a>지리적 위치 서비스

Geolocation Service를 사용하여 IP 주소에 대해 검색된 2자 길이의 국가/지역 코드를 미리 볼 수 있습니다. 이 서비스는 지리적 위치를 기반으로 사용자 지정된 애플리케이션 콘텐츠를 제공하여 사용자 환경을 개선하는 데 도움을 줄 수 있습니다.

자세한 내용은 [Geolocation Service 설명서](https://docs.microsoft.com/rest/api/maps/geolocation)를 참조하세요.

### <a name="mobility-service"></a>모바일 서비스

Azure Maps Mobility Service는 교통 라우팅, 가까운 대중 교통 정류장 등의 대중 교통 기능을 사용하여 애플리케이션 개발 시간을 단축합니다. 사용자는 대중 교통 정류장, 노선 및 일정에 대한 세부 정보를 검색할 수 있습니다. 또한 사용자는 Mobility Service를 통해 정류장 및 노선 기하학, 정류장/노선/서비스 영역 알림, 실시간 대중 교통 도착 및 서비스 알림을 검색할 수 있습니다. 뿐만 아니라 Mobility Service는 다중 모달 이동 계획 옵션이 있는 라우팅 기능을 제공합니다. 다중 모달 이동 계획은 1회 이동에 도보, 자전거 및 대중 교통 옵션을 모두 통합합니다. 사용자는 자세한 다중 모달 단계별 여정에 액세스할 수도 있습니다.

이 서비스에 대한 자세한 내용은 [Mobility Service 설명서](https://docs.microsoft.com/rest/api/maps/mobility)를 참조하세요.

### <a name="render-service"></a>Render Service

현재 미리 보기로 제공되는 [Render Service V2](https://docs.microsoft.com/rest/api/maps/renderv2)에는 새 버전의 [Get Map Tile V2 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)가 도입되었습니다. 이제 고객은 Get Map Tile V2 API에서 Azure Maps Creator를 사용하여 만들어진 Azure Maps 도로 타일, 날씨 타일 또는 맵 타일을 요청할 수 있습니다. 새 Get Map Tile V2 API를 사용하는 것이 좋습니다.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Render Service V2의 맵 예제":::

자세한 내용은 [Render Service V2 설명서](https://docs.microsoft.com/rest/api/maps/renderv2)를 참조하세요.

현재 GA(일반 공급) 상태인 Render Service V1에 대한 자세한 내용은 [Render Service V1 설명서](https://docs.microsoft.com/rest/api/maps/render)를 참조하세요.  

### <a name="route-service"></a>Route Service

경로 서비스를 사용하여 요청된 각 경로의 ETA(예상 도착 시간)를 계산할 수 있습니다. Route API는 요청된 요일 및 시간의 일반적인 도로 통행 속도와 같은 실시간 교통 정보, 과거의 교통 데이터 같은 요소를 고려합니다. 이 API는 시간 또는 거리를 기준으로 차례대로 또는 최적화된 순서대로 한 번에 여러 대상에서 사용할 수 있는 최단 경로 또는 가장 빠른 경로를 반환합니다. 이 서비스를 통해 개발자는 자동차, 트럭, 자전거, 도보, 전기차 등의 다양한 이동 모드로 방향을 계산할 수 있습니다. 이 서비스는 출발 시간, 무게 제한, 위해 물질 운송 등의 입력도 고려합니다.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Route Service의 맵 예제":::

Route Service는 다음과 같은 고급 기능 세트를 제공합니다.

* 여러 경로 요청의 일괄 처리.
* 원본과 대상 세트 간 여행 시간 및 거리의 매트릭스.
* 시간 또는 연료 요구 사항에 기반한 사용자가 여행할 수 있는 경로 또는 거리 찾기.

라우팅 기능에 대한 자세한 내용은 [Route Service 설명서](https://docs.microsoft.com/rest/api/maps/route)를 참조하세요.

### <a name="search-service"></a>Search 서비스

Search Service는 개발자가 주소, 장소, 비즈니스 목록을 이름이나 범주 및 기타 지리 정보 기준으로 검색하는 데 유용합니다. 또한 Search Service는 위도 및 경도를 기반으로 주소 및 교차로를 [역방향으로 지오코딩](https://en.wikipedia.org/wiki/Reverse_geocoding)할 수 있습니다.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="맵에서 검색 예제":::

또한 Search Service는 다음과 같은 고급 기능을 제공합니다.

* 경로에 따른 검색.
* 더 넓은 영역 내에서 검색.
* 검색 요청의 그룹 일괄 처리.
* 브랜드 이름별로 전기차 충전소 및 POI(관심 지점) 데이터 검색

검색 기능에 대한 자세한 내용은 [Search Service 설명서](https://docs.microsoft.com/rest/api/maps/search)를 참조하세요.

### <a name="spatial-service"></a>공간 서비스

Spatial Service는 위치 정보를 신속하게 분석하여 고객에게 시간 및 공간에서 발생하고 있는 실시간 이벤트를 알려줍니다. 이벤트를 거의 실시간으로 분석하고 예측 모델링을 수행할 수 있습니다.

이 서비스를 통해 고객은 일반적인 지리 공간적 수학 계산 라이브러리를 사용하여 위치 인텔리전스를 개선할 수 있습니다. 일반적인 계산에는 가장 가까운 지점, 대원 거리 및 버퍼가 포함됩니다. 이 서비스와 다양한 기능에 대한 자세한 내용은 [Spatial Service 설명서](https://docs.microsoft.com/rest/api/maps/spatial)를 참조하세요.

### <a name="timezone-service"></a>Timezone Service

Time Zone Service를 사용하면 현재, 과거 및 미래의 표준 시간대 정보를 쿼리할 수 있습니다. 위도 및 경도 쌍 또는 [IANA ID](https://www.iana.org/)를 입력으로 사용할 수 있습니다. Time Zone Service를 통해 다음을 수행할 수 있습니다.

* Microsoft Windows 표준 시간대 ID를 IANA 표준 시간대로 변환.
* 표준 시간대 오프셋을 UTC로 페치.
* 현재 시간을 선택한 표준 시간대로 가져오기.

Time Zone Service에 대한 쿼리의 일반적인 JSON 응답은 다음 샘플과 같습니다.

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

이 서비스에 대한 자세한 내용은 [Time Zone Service 설명서](https://docs.microsoft.com/rest/api/maps/timezone)를 참조하세요.

### <a name="traffic-service"></a>Traffic Service

Traffic Service는 교통 정보가 필요한 웹 및 모바일 애플리케이션에 사용할 수 있는 개발자용 웹 서비스 제품군입니다. 이 서비스는 다음 두 가지 데이터 형식을 제공합니다.

* 교통 흐름: 네트워크의 모든 주요 도로에 대해 실시간으로 관찰된 속도와 여행 시간.
* 교통 사고: 도로 네트워크와 관련된 교통 혼잡 및 사고에 대한 최신 보기.

![교통 정보를 사용한 맵의 예제](media/about-azure-maps/intro_traffic.png)

자세한 내용은 [Traffic Service 설명서](https://docs.microsoft.com/rest/api/maps/traffic)를 참조하세요.

### <a name="weather-service"></a>Weather Service

Weather Service는 개발자가 특정 위치의 날씨 정보를 검색하는 데 사용할 수 있는 API를 제공합니다. 이 정보에는 관측 날짜 및 시간, 기후 조건에 대한 간단한 설명, 날씨 아이콘, 강수량 지표 플래그, 온도, 바람 속도 정보 등의 세부 정보가 포함됩니다. RealFeel™ 온도 및 자외선 지수 같은 추가 세부 정보도 반환됩니다.

개발자는 [Get Weather along route API](https://docs.microsoft.com/rest/api/maps/weather/getweatheralongroutepreview)를 사용하여 특정 경로를 따라 날씨 정보를 검색할 수 있습니다. 또한 이 서비스는 홍수나 장마 같은 기후 재난의 영향을 받는 중간 지점에 대한 날씨 알림 생성을 지원합니다.

[Get Map Tile V2 API](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)를 사용하여 과거, 현재, 미래의 레이더 및 위성 타일을 요청할 수 있습니다.

![실시간 날씨 레이더 타일이 포함된 맵의 예](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service"></a>Maps Creator 서비스

Maps Creator Service는 개발자가 실내 맵 데이터 기반의 맵 기능을 통해 애플리케이션을 만드는 데 사용할 수 있는 웹 서비스 모음입니다.

Maps Creator Service는 다음과 같은 세 가지 주요 서비스를 제공합니다.

* [데이터 세트 서비스](https://docs.microsoft.com/rest/api/maps/dataset). 데이터 세트 서비스를 사용하여 변환된 그리기 패키지 데이터에서 데이터 세트를 만듭니다. 그리기 패키지 요구 사항에 대한 자세한 내용은 그리기 패키지 요구 사항을 참조하세요.

* [변환 서비스](https://docs.microsoft.com/rest/api/maps/dataset). 변환 서비스를 사용하여 DWG 디자인 파일을 실내 맵에 대한 그리기 패키지 데이터로 변환할 수 있습니다.

* [타일 세트 서비스](https://docs.microsoft.com/rest/api/maps/tileset). 타일 세트 서비스를 사용하여 데이터 세트의 벡터 기반 표현을 만듭니다. 애플리케이션에서 타일 세트를 사용하여 데이터 세트의 시각적 타일 기반 보기를 표시할 수 있습니다.

* [기능 상태 서비스](https://docs.microsoft.com/rest/api/maps/featurestate). 기능 상태 서비스를 사용하여 동적 맵 스타일 지정을 지원할 수 있습니다. 동적 맵 스타일 지정을 통해 실시간 이벤트를 IoT 시스템에서 제공하는 공간에 반영할 수 있습니다.

* [WFS 서비스](https://docs.microsoft.com/rest/api/maps/featurestate) WFS 서비스를 사용하여 실내 맵 데이터를 쿼리할 수 있습니다. WFS 서비스는 단일 데이터 세트 쿼리에 대한 [Open Geospatial Consortium API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) 표준을 따릅니다.

## <a name="programming-model"></a>프로그래밍 모델

Azure Maps는 이동성을 위해 빌드되었으며 플랫폼 간 애플리케이션을 개발하는 데 유용할 수 있습니다. 언어에 구애 받지 않는 프로그래밍 모델을 사용하며 [REST API](https://docs.microsoft.com/rest/api/maps/)를 통해 JSON 출력을 지원합니다.

또한 Azure Maps는 간단한 프로그래밍 모델을 사용하는 간편한 [JavaScript 맵 컨트롤](https://docs.microsoft.com/javascript/api/azure-maps-control)을 제공합니다. 웹 및 모바일 애플리케이션을 쉽고 빠르게 개발할 수 있습니다.

## <a name="power-bi-visual"></a>Power BI 시각적 개체

Power BI용 Azure Maps 시각적 개체는 지도 위에 공간 데이터에 대한 다양한 데이터 시각화 세트를 제공합니다. 비즈니스 데이터의 80% 이상이 위치 컨텍스트를 가지고 있는 것으로 추정됩니다. Azure Maps 시각적 개체는 이 위치 컨텍스트가 비즈니스 데이터와 어떻게 관련되고 영향을 미치는지에 대한 인사이트를 얻을 수 있는 코드 없는 솔루션을 제공합니다.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="비즈니스 데이터를 표시하는 Azure Maps 시각적 개체가 포함된 Power BI 데스크톱":::

자세한 내용은 [Azure Maps Power BI 시각적 개체](power-bi-visual-getting-started.md) 시작 설명서를 참조하세요.

## <a name="usage"></a>사용

Azure Maps 서비스에 액세스하려면 [Azure Portal](https://portal.azure.com)로 이동하여 Azure Maps 계정을 만듭니다.

Azure Maps는 키 기반 인증 체계를 사용합니다. 계정을 만들 때 두 개의 키가 생성됩니다. Azure Maps 서비스에 인증하려면 두 키 중 하나를 사용하면 됩니다.

참고 - Azure Maps는 기능을 매핑하기 위해 고객이 제공한 주소/위치 쿼리("Queries")를 타사 TomTom과 공유합니다. TomTom과 공유하는 경우 쿼리는 고객 또는 최종 사용자에게 연결되지 않으며 개인을 식별하는 데 사용할 수 없습니다. Moovit 및 AccuWeather와의 통합을 포함하는 Mobility 및 Weather Service는 현재 [미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

Microsoft는 현재 Online Services 하도급업자 목록에 TomTom, Moovit 및 AccuWeather를 추가하는 과정을 진행 중입니다.

## <a name="supported-regions"></a>지원되는 지역

Azure Maps 서비스는 현재 다음 국가/지역을 제외한 모든 지역에서 사용할 수 있습니다.

* 중국
* 대한민국

현재 IP 주소의 위치가 지원되는 국가/지역에 포함되어 있는지 확인하세요.

## <a name="next-steps"></a>다음 단계

Azure Maps를 보여주는 샘플 앱을 사용해보세요.

> [!div class="nextstepaction"]
> [빠른 시작: 웹앱 만들기](quick-demo-map-app.md)

Azure Maps에서 데이터를 최신 상태로 유지하세요.

> [!div class="nextstepaction"]
> [Azure Maps 블로그](https://azure.microsoft.com/blog/topics/azure-maps/)
