---
title: Azure Maps 용어집 | Microsoft Docs
description: Azure Maps, 위치 기반 서비스 및 GIS와 관련 된 일반적으로 사용 되는 용어에 대 한 용어입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: a6c1e14e332f6081111d2a58a30702093b99a876
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198244"
---
# <a name="glossary"></a>용어

다음 목록에서는 Azure Maps 서비스에 사용 되는 일반적인 단어에 대해 설명 합니다.

## <a name="a"></a>A

<a name="address-validation"></a>**주소 유효성 검사**: 주소가 있는지 확인 하는 프로세스입니다.

<a name="advanced-routing"></a>**고급 라우팅**:도로 라우팅 데이터를 사용 하 여 고급 작업을 수행 하는 서비스 모음입니다. 예를 들어, 연결 가능한 범위 (등시성), 거리 행렬 및 배치 경로 요청을 계산 합니다.

<a name="aerial-imagery"></a>**항공 이미지**: [위성 이미지](#satellite-imagery)를 참조 하세요. 

<a name="along-a-route-search"></a>**경로 검색**: 지정 된 우회 시간 내에 데이터를 찾는 공간 쿼리 또는 경로 경로에서의 거리입니다.

<a name="altitude"></a>고 **도: 참조**화면 위에 있는 점의 높이 또는 수직 상승입니다. 고도 측정은 평균 해면 등 지정된 참조 자료를 기반으로 합니다. 상승도 참조하세요.

<a name="ambiguous"></a>**모호한**: 지정 된 특성에 대 한 둘 이상의 값이 개체에 적절 하 게 할당 될 수 있는 경우에 존재 하는 데이터 분류의 불확실성 상태입니다. 예를 들어, "CA"를 지 오 코딩 때 두 개의 모호한 결과가 반환 됩니다. "캐나다" 및 "캘리포니아". "CA"는 각각 "캐나다" 및 "캘리포니아"에 대 한 국가 및 상태 코드입니다. 

<a name="annotation"></a>**주석**: 사용자에 게 정보를 제공 하기 위해 지도에 표시 되는 텍스트 또는 그래픽입니다. 주석은 특정 지도 엔터티를 식별하고 설명하거나, 지도에서 영역에 대한 일반 정보를 제공하거나, 지도 자체에 대한 정보를 제공할 수 있습니다.

<a name="antimeridian"></a>**Antimeridian**: 180<sup>th</sup> 자오선 라고도 합니다. 이는-180도와 180 각도의 경도를 충족 하는 지점입니다. 전 세계에서 프라임 자오선의 반대입니다.

<a name="application-programming-interface-api"></a>**API (응용 프로그래밍 인터페이스)** : 개발자가 응용 프로그램을 만들 수 있도록 하는 사양입니다.

<a name="api-key"></a>**API 키**: [공유 키 인증](#shared-key-authentication)을 참조 하세요.

<a name="area-of-interest-aoi"></a>**관심 영역 (AOI)** : 지도 또는 데이터베이스 생산에 대 한 포커스 영역을 정의 하는 데 사용 되는 범위입니다.

<a name="asset-tracking"></a>**자산 추적**: 사람, 차량 또는 기타 개체와 같은 자산의 위치를 추적 하는 프로세스입니다.

<a name="asynchronous-request"></a>**비동기 요청**: 연결을 열고 비동기 요청에 대 한 식별자를 반환 하는 서버에 요청을 한 다음 연결을 닫는 HTTP 요청입니다. 서버는 요청을 계속 처리하고 사용자는 식별자를 사용하여 상태를 확인할 수 있습니다. 요청 처리가 완료되면 사용자가 응답을 다운로드할 수 있습니다. 이 유형의 요청은 장기 실행 프로세스에 사용 됩니다.

<a name="autocomplete"></a>**자동 완성**: 응용 프로그램의 기능은 사용자가 입력 하는 나머지 단어를 예측 합니다. 

<a name="autosuggest"></a>**Autosuggest**: 응용 프로그램의 기능으로, 사용자가 입력 하는 내용에 대 한 논리적 가능성을 예측 합니다.

<a name="azure-location-based-services-lbs"></a>**Azure Location Based Services (이란)** : 미리 보기 상태인 Azure Maps의 이전 이름입니다.

<a name="azure-active-directory"></a>**Azure Active Directory (AZURE ad)** : azure Ad는 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다. Azure Maps Azure AD 통합은 현재 모든 Azure Maps Api에 대해 미리 보기에서 사용할 수 있습니다. Azure AD는 RBAC (역할 기반 액세스 제어)를 지원 하 여 Azure Maps 리소스에 대 한 세분화 액세스를 허용 합니다. Azure Maps Azure AD 통합에 대 한 자세한 내용은 [Azure Maps 및 AZURE ad](azure-maps-authentication.md) 및 [Azure Maps에서 인증 관리](how-to-manage-authentication.md)를 참조 하세요.

<a name="azure-maps-key"></a>**Azure Maps 키**: [공유 키 인증](#shared-key-authentication)을 참조 하세요.

## <a name="b"></a>b

<a name="base-map"></a>**기본 맵**:도로, 랜드마크 및 정치적 경계와 같은 배경 참조 정보를 표시 하는 맵 응용 프로그램의 일부입니다.

<a name="batch-request"></a>**일괄 처리 요청**: 여러 요청을 단일 요청으로 결합 하는 프로세스입니다.

<a name="bearing"></a>**베어링**: 다른 점을 기준으로 하는 점의 가로 방향입니다. 이는 시계 방향으로 0도에서 360도까지 북쪽에 상대적인 각도로 표현 됩니다. 

<a name="boundary"></a>**경계**: 국가/지역, 학구 및 속성과 같이 인접 정치적 엔터티를 구분 하는 선 또는 다각형입니다. 경계는 강, 산 또는 벽 등 물리적 물체를 따르거나 따르지 않는 선입니다.

<a name="bounds"></a>**경계:** [경계 상자](#bounding-box)를 참조 하세요.

<a name="bounding-box"></a>**경계 상자**: 지도의 사각형 영역을 나타내는 데 사용 되는 좌표 집합입니다. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadastre**: 등록 된 육지 및 속성에 대 한 레코드입니다. 또한 [구획](#parcel)을 참조하세요.

<a name="camera"></a>**카메라**: 대화형 지도 컨트롤의 컨텍스트에서 카메라는 보기의 맵 필드를 정의 합니다. 카메라의 뷰포트는 가운데, 확대/축소 수준, 피치, 베어링 등 여러 가지 맵 매개 변수를 기반으로 결정 됩니다. 

<a name="centroid"></a>**중심**: 기능의 기하학적 중심입니다. 선의 중심은 중간점인 반면 다각형의 중심은 영역의 중심입니다.

<a name="choropleth-map"></a>**Choropleth map**: 통계 변수의 측정에 비례하여 영역을 음영 처리 하는 테마 맵입니다. 이 통계 변수는 맵에 표시 됩니다. 예를 들어 다른 모든 주에 대한 상대 인구를 기준으로 각 미국 주의 경계 색을 지정합니다.

<a name="concave-hull"></a>**오목 선체**: 지정 된 데이터 집합의 모든 셰이프를 둘러싸는 가능한 오목 기 하 도형을 나타내는 도형입니다. 생성된 모양은 데이터를 랩으로 감싸고 열을 가한 것과 비슷합니다. 따라서 지점 간에 넓은 범위를 기타 데이터 요소로 감싸게 됩니다.

<a name="consumption-model"></a>**소비 모델**: 차량 연료 또는 전기를 소비 하는 속도를 정의 하는 정보입니다. 또한 [소비 모델 설명서](consumption-model.md)를 참조하세요.

<a name="control"></a>**Control**: 인터페이스에 대 한 동작 집합을 정의 하는 그래픽 사용자 인터페이스로 구성 된 자체 포함 되거나 재사용 가능한 구성 요소입니다. 예를 들어 지도 컨트롤은 일반적으로 대화형 지도를 로드하는 사용자 인터페이스의 일부입니다.

<a name="convex-hull"></a>**볼록 선체**: 볼록 선체는 지정 된 데이터 집합의 모든 셰이프를 둘러싸는 최소 볼록 기 하 도형을 나타내는 도형입니다. 생성된 모양은 데이터 집합 주위를 래핑하는 탄력적 밴드와 비슷합니다.

<a name="coordinate"></a>**좌표**: 지도의 위치를 나타내는 데 사용 되는 경도 및 위도 값으로 구성 됩니다.

<a name="coordinate-system"></a>**좌표계**: 두 개 또는 세 개의 차원에서 공간에 있는 점의 위치를 정의 하는 데 사용 되는 참조 프레임 워크입니다.

<a name="country-code"></a>**국가 코드**: ISO 표준을 기반으로 하는 국가/지역에 대 한 고유 식별자입니다. ISO2는 국가의 두자리 코드(예: US)이고, ISO3은 3자리 코드(예: USA)를 나타냅니다.

<a name="country-subdivision"></a>**국가**하위 수준: 일반적으로 시/도 라고 하는 국가/지역의 첫 번째 수준 하위 수준입니다.

<a name="country-secondary-subdivision"></a>**국가 보조**하위 수준: 일반적으로 관할지 이라고 하는 국가/지역의 두 번째 수준 하위 수준입니다.

<a name="country-tertiary-subdivision"></a>**국가 3 차**: 국가/지역의 세 번째 수준 하위 수준 (일반적으로는 같은 이름의 영역)입니다.

<a name="cross-street"></a>**교차 주소**: 두 개 이상이 교차 하는 지점입니다.

<a name="cylindrical-projection"></a>**원통형 프로젝션**: spheroid 또는 구의 요소를 탄젠트 또는 시/시 실린더로 변형 하는 프로젝션입니다. 원기둥은 위쪽에서 아래쪽으로 잘려서 평면으로 펴집니다.

## <a name="d"></a>D

<a name="datum"></a>**Datum**: 측정 시스템의 참조 사양으로, 표면 (가로 데이텀) 또는 표면 위 또는 아래 (세로 데이텀)의 좌표 위치에 대 한 시스템입니다.

<a name="dbf-file"></a>**DBF 파일**: 셰이프 파일 (.shp)와 함께 사용 되는 데이터베이스 파일 형식입니다.

<a name="degree-minutes-seconds-dms"></a>**분 초 (DMS)** : 위도 및 경도를 설명 하는 측정 단위입니다. 도는 원의 1/360<sup>번째</sup>입니다. 도는 60분으로 나눠지고, 분은 60초로 나눠집니다.

<a name="delaunay-triangulation"></a>**Delaunay 삼각 측정**: 점의 데이터 집합에서 겹치지 않는 연속 삼각형의 메시를 만드는 기술입니다. 각 삼각형의 외접구에는 내부에 있는 데이터 세트의 지점이 없습니다.

<a name="demographics"></a>**인구**통계: 인간 모집단의 통계 특성 (예: 나이, 출생 율 및 수입)입니다.

<a name="destination"></a>**Destination**: 누군가가 이동 하는 끝점 또는 위치입니다.

<a name="digital-elevation-model-dem"></a>**DEM (디지털 권한 상승 모델)** : 공통 된 일정을 사용 하 여 정기적으로 영역에 캡처한 서피스와 관련 된 상승 값의 데이터 집합입니다. DEM은 일반적으로 지형을 나타내는 데 사용됩니다.

<a name="dijkstra's-algorithm"></a>**Dijkstra의 알고리즘**: 네트워크 연결을 검사 하 여 두 지점과 가장 짧은 경로를 찾는 알고리즘입니다.

<a name="distance-matrix"></a>**거리 행렬**: 원본 및 대상 집합 간의 이동 시간 및 거리 정보를 포함 하는 행렬입니다. 

## <a name="e"></a>E

<a name="elevation"></a>**상승**: 참조 서피스나 데이텀 위 또는 아래에 있는 점 또는 개체의 세로 거리입니다. 일반적으로 참조 화면은 평균 바다 수준입니다. 일반적으로 상승은 토지의 세로 높이를 가리킵니다.

<a name="envelope"></a>**봉투 (Envelope**): [경계 상자](#bounding-box)를 참조 하세요.

<a name="extended-postal-code"></a>**확장 된 우편 번호**: 추가 정보를 포함할 수 있는 우편 번호입니다. 예를 들어 미국의 우편 번호는 5 자리 숫자입니다. 하지만 zip + 4 라고 하는 확장 된 우편 번호에는 4 개의 추가 숫자가 포함 됩니다. 이러한 추가 숫자는 도시 블록, 아파트 그룹 또는 post office 상자와 같이 5 자리 배달 영역 내에서 지리 세그먼트를 식별 하는 데 사용 됩니다. 지리적 세그먼트를 알면 효율적인 메일 정렬 및 전달이 지원 됩니다.

<a name="extent"></a>**익스텐트**: [경계 상자](#bounding-box)를 참조 하세요.

## <a name="f"></a>F

<a name="federated-authentication"></a>**페더레이션된 인증**: 여러 웹 및 모바일 앱에서 단일 로그온/인증 메커니즘을 사용할 수 있도록 하는 인증 방법입니다. 

<a name="feature"></a>**Feature**: 추가 메타 데이터 정보와 함께 기 하 도형을 결합 하는 개체입니다. 

<a name="feature-collection"></a>**기능 컬렉션**: 기능 개체의 컬렉션입니다.

<a name="find-along-route"></a>**경로를 따라 찾기**: 지정 된 우회 시간 또는 경로 경로에서의 거리 내에 있는 데이터를 찾는 공간 쿼리입니다.

<a name="find-nearby"></a>**주변 찾기**: 포인트에서 고정 된 직선 거리 (crow)를 검색 하는 공간 쿼리

<a name="fleet-management"></a>**차량 관리**: 자동차, 트럭, 선박 및 평면과 같은 상업 차량 관리 선단 관리에는 차량 비용, 유지 관리, 컴퓨터 통신(추적 및 진단)뿐만 아니라 드라이버, 속도, 연료, 상태 및 안전 관리와 같은 다양한 함수가 포함될 수 있습니다. 차량 관리는 회사에서 교통를 사용 하는 회사에서 사용 하는 프로세스입니다. 회사는 정부 법규를 준수 하는 동시에 위험을 최소화 하 고 전반적인 교통 및 직원 비용을 절감 하려고 합니다.

<a name="free-flow-speed"></a>**사용 가능한 흐름 속도**: 이상적인 조건에서는 사용 가능한 흐름 속도가 필요 합니다. 일반적으로 속도 제한이 있습니다.

<a name="free-form-address"></a>**자유 형식 주소**: 한 줄의 텍스트로 표시 되는 전체 주소입니다.

<a name="fuzzy-search"></a>**유사 항목 검색**: 주소나 관심 지점이 될 수 있는 텍스트의 자유 형식 문자열을 사용 하는 검색입니다. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode**: 지도에 해당 위치를 표시 하는 데 사용할 수 있는 좌표로 변환 된 주소 또는 위치입니다. 

<a name="geocoding"></a>**지 오 코딩**: 전방 지 오 코딩 라고도 하며, 위치 데이터의 주소를 좌표로 변환 하는 프로세스입니다. 

<a name="geodesic-path"></a>**측 지 path**: 구부러진 표면의 두 요소 사이의 최단 경로입니다. Azure Maps에서 렌더링되면 이 경로는 메르카토르 도법으로 인해 곡선으로 나타납니다.

<a name="geofence"></a>지 **오**의: 장치가 지역에 들어가거나 있을 때 이벤트를 트리거하는 데 사용할 수 있는 정의 된 지리적 영역입니다.

<a name="geojson"></a>**GeoJSON**: 점으로, 선, 다각형 등의 지리적 벡터 데이터를 저장 하는 데 사용 되는 일반적인 JSON 기반 파일 형식입니다. **참고**: Azure Maps는 [여기에서 설명한](extend-geojson.md) 대로 GeoJSON의 확장된 버전을 사용합니다.

<a name="geometry"></a>**Geometry**: 점, 선, 다각형 등의 공간 개체를 나타냅니다.

<a name="geometrycollection"></a>**GeometryCollection**: geometry 개체의 컬렉션입니다.

<a name="geopol"></a>**Geopol**: geopolitically 중요 한 데이터 (예: 벌어지고 테두리 및 장소 이름)를 나타냅니다.

<a name="georeference"></a>**Georeference**: 지리적 데이터 또는 이미지를 알려진 좌표계에 정렬 하는 프로세스입니다. 이 프로세스는 데이터 기울이기, 회전, 크기 조정 또는 이동으로 구성될 수 있습니다.

<a name="georss"></a>**GeoRSS**: RSS 피드에 공간 데이터를 추가 하는 XML 확장입니다.

<a name="gis"></a>**Gis**: "지리 정보 시스템"의 머리글자어입니다. 매핑 산업을 설명하는 데 사용되는 일반적인 용어입니다.

<a name="gml"></a>**GML**: Geography Markup Language 라고도 합니다. 공간 데이터를 저장하는 XML 파일 확장명입니다.

<a name="gps"></a>**GPS**: 전역 위치 지정 시스템이 라고도 하며, 지구에서 장치 위치를 결정 하는 데 사용 되는 위성 시스템입니다. 궤도 위성은 지구 상 어디서든 GPS 수신기가 삼변 측량을 통해 고유한 위치를 계산할 수 있는 신호를 전송합니다.

<a name="gpx"></a> **.Gpx)** : gps 교환 형식이 라고도 하는는 gps 장치에서 일반적으로 생성 되는 XML 파일 형식입니다.  

<a name="great-circle-distance"></a>**매우 원 거리**: 구의 표면에서 두 요소 사이의 최단 거리입니다.

<a name="greenwich-mean-time-gmt"></a>**GMT (그리니치 표준시): GMT**, 영국에서 선명한 해군를 통해 실행 되는 프라임 자오선의 시간입니다.

<a name="guid"></a>**GUID: guid**(globally unique identifier)입니다. 인터페이스, 클래스, 형식 라이브러리, 구성 요소 범주 또는 레코드를 고유하게 식별하는 데 사용되는 문자열입니다.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversine 수식**: 구에 있는 두 요소 사이의 매우 둥근 거리를 계산 하는 데 사용 되는 일반적인 수식입니다.

<a name="hd-maps"></a>**Hd 맵**: 높은 정의 맵이 라고도 하며, 자치 구동에 필요한 레인 표시, signage 및 방향 광원과 같은 고화질 네트워크 정보로 구성 됩니다.

<a name="heading"></a>**제목**: 점이 가리키는 방향입니다. [방위](#heading)를 참조하세요.

<a name="heatmap"></a>**열 지도**: 색 범위가 특정 영역에 있는 점의 밀도를 나타내는 데이터 시각화입니다. 주제도를 참조하세요.

<a name="hybrid-imagery"></a>**하이브리드 이미지**: 도로의 데이터와 레이블이 위에 겹쳐서 겹쳐진 위성 또는 항공 이미지입니다.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: 인터넷에 할당 된 번호 기관의 머리글자어입니다. 글로벌 IP 주소 할당을 감독하는 비영리 그룹입니다.

<a name="isochrone"></a>**Isochrone**: Isochrone는 지정 된 위치에서 임의 방향으로 전송 모드를 지정 된 시간 내에 이동할 수 있는 영역을 정의 합니다. [연결 가능 범위](#reachable-range)를 참조하세요.

<a name="isodistance"></a>**Isodistance**: 위치가 지정 된 경우 isochrone는 모든 방향에서 교통 모드에 대해 지정 된 거리 내에서 이동할 수 있는 영역을 정의 합니다. [연결 가능 범위](#reachable-range)를 참조하세요.

## <a name="k"></a>K

<a name="kml"></a>**KML**: 키, 선, 다각형 등의 지리적 벡터 데이터를 저장 하는 데 사용할 수 있는 일반적인 XML 파일 형식인 Keyhole Markup Language 라고도 합니다. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispectral, 땅의 이미지를 수집 하는 NASA에서 개발한 위성 orbiting 위성입니다. 이 이미지는 농업, 임업 및 지도 제작와 같은 여러 산업에서 사용 됩니다.

<a name="latitude"></a>**위도**: 적도에서 북쪽 또는 남 방향으로 측정 한 각도 거리 (도)입니다.

<a name="level-of-detail"></a>**세부 정보 수준**: 확대/축소 수준을 참조 하세요.

<a name="lidar"></a>**Lidar**: 밝은 검색 및 기타 범위의 머리글자어입니다. 반사 화면에 대한 거리를 측정하기 위해 레이저를 사용하는 원격 감지 기술입니다.

<a name="linear-interpolation"></a>**선형 보간**: 알려진 값 사이의 선형 거리를 사용 하 여 알 수 없는 값을 예측 합니다.

<a name="linestring"></a>**LineString**: 선을 나타내는 데 사용 되는 기 하 도형입니다. 다중선이라고도 합니다. 

<a name="localization"></a>**지역화**: 다양 한 언어와 문화권을 지원 합니다.

<a name="logistics"></a>**물류**: 사람, 차량, 물품 또는 자산을 조정 된 방식으로 이동 하는 프로세스입니다.

<a name="longitude"></a>**경도**: 동 또는 서 방향으로 프라임 자오선 측정 된 각도 거리 (도)입니다.

## <a name="m"></a>M

<a name="map-tile"></a>**지도 타일**: 지도 캔버스의 파티션을 나타내는 사각형 이미지입니다. 자세한 내용은 [확대/축소 수준 및 타일 그리드 설명서](zoom-levels-and-tile-grid.md)를 참조하세요.

<a name="marker"></a>**표식**: 핀 또는 압정이 라고도 하는는 지도의 점 위치를 나타내는 아이콘입니다.

<a name="mercator-projection"></a>**Mercator 프로젝션**: 자오선를 사용 하 여 각도를 절약 하는 직선 세그먼트로 rhumb 선 이라고 하는 상수 과정의 줄을 나타내는 기능 때문에 항해의 표준 맵 프로젝션이 되는 원통형 지도 투영입니다. 지표면의 진정한 레이아웃에 비해 모든 평면 지도 투영은 지도의 모양이나 크기를 왜곡합니다. 메르카토르 도법은 적도에서 먼 지역을 과장합니다. 즉, 더 작은 영역이 극 지방으로 가까워질 수록 지도에 크게 표시됩니다. 

<a name="multilinestring"></a>**MultiLineString**: LineString 개체의 컬렉션을 나타내는 기 하 도형입니다. 

<a name="multipoint"></a>**MultiPoint**: Point 개체의 컬렉션을 나타내는 기 하 도형입니다.

<a name="multipolygon"></a>**Multipolygon**: polygon 개체의 컬렉션을 나타내는 기 하 도형입니다. 예를 들어 하와이의 경계를 표시 하기 위해 각 섬에는 다각형이 간략하게 표시 됩니다. 따라서 하와이의 경계는 MultiPolygon입니다.

<a name="municipality"></a>**지방 자치 체**: 도시 또는 타운. 

<a name="municipality-subdivision"></a>**지방 자치 체**하위 작업: "downtown"와 같은 환경 또는 로컬 영역 이름과 같은 지방 자치 체의 하위 분류입니다.

## <a name="n"></a>N

<a name="navigation-bar"></a>**탐색 모음**: 확대/축소 수준, 피치, 회전 및 기본 지도 계층 전환을 조정 하는 데 사용 되는 지도의 컨트롤 집합입니다.

<a name="nearby-search"></a>**근접 검색**: 시점에서 고정 된 직선 거리 (crow)를 검색 하는 공간 쿼리

<a name="neutral-ground-truth"></a>**중립 접지 참**: 사용 가능한 경우 로컬 스크립트에서 나타내는 지역의 공식 언어로 레이블을 렌더링 하는 지도입니다.

## <a name="o"></a>O

<a name="origin"></a>**원본**: 사용자가 있는 시작점 또는 위치입니다.

## <a name="p"></a>P

<a name="panning"></a>**패닝**: 일정 한 확대/축소 수준을 유지 하면서 모든 방향으로 지도를 이동 하는 프로세스입니다.

<a name="parcel"></a>**Parcel**: 육지 또는 속성 경계의 플롯입니다.

<a name="pitch"></a>**피치**: 지도의 기울이기 양은 세로를 기준으로 합니다. 여기서 0은 지도에서 바로 아래로 이동 합니다.

<a name="point"></a>**Point**: 지도의 단일 위치를 나타내는 기 하 도형입니다. 

<a name="points-of-interest-poi"></a>**POI (관심 지점)** : 비즈니스, 랜드마크 또는 공통적인 관심 위치입니다.

<a name="polygon"></a>**Polygon**: 지도의 영역을 나타내는 솔리드 기 하 도형입니다. 

<a name="polyline"></a>**다중선**: 선을 나타내는 데 사용 되는 기 하 도형입니다. LineString이라고도 합니다. 

<a name="position"></a>**Position**: 점의 경도, 위도 및 고도 (x, y, z 좌표)입니다.

<a name="post-code"></a>**코드 게시**: [우편](#postal-code)번호를 참조 하세요.

<a name="postal-code"></a>**우편**번호: 특정 형식의 일련의 문자 또는 숫자입니다. 우편 번호는 메일 배달의 간소화를 위해 국가/지역의 우편 서비스에서 지리적 영역을 영역으로 나누는 데 사용 됩니다.

<a name="primary-key"></a>**기본 키**: Azure Maps 공유 키 인증을 위해 제공 되는 두 구독 키 중 첫 번째 키입니다. [공유 키 인증](#shared-key-authentication)을 참조 하세요.

<a name="prime-meridian"></a>**프라임 자오선**: 0도 경도를 나타내는 경도 선입니다. 일반적으로 경도 값은 180도까지 westerly 방향으로 이동 하는 경우 감소 하 고 easterly 방향 180으로으로 이동할 때 증가 합니다. 

<a name="prj"></a>**Prj**: 데이터 집합이 있는 예상 좌표 시스템에 대 한 정보가 포함 된 esri 파일이 자주 포함 되는 텍스트 파일입니다.

<a name="projection"></a>**프로젝션**: 가로 Mercator, Albers 같은 영역 및 Robinson과 같은 지도 프로젝션을 기반으로 하는 예상 좌표 시스템입니다. 여기에서는 2차원 데카르트 좌표 평면에 지표면의 지도를 투영하는 기능을 제공합니다. 투영된 좌표계는 지도 프로젝션이라고도 합니다.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: quadtree 바둑판식 배열 시스템 내의 타일에 대 한 기본-4 주소 인덱스입니다. 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md) 설명서를 참조하세요.

<a name="quadtree"></a>**Quadtree**: 각 노드에 정확히 네 개의 자식이 있는 데이터 구조입니다. Azure Maps에서 사용 되는 바둑판식 배열 시스템은 사용자가 한 수준으로 확대 하는 경우 각 지도 타일이 4 개의 하위 타일로 분할 되도록 quadtree 구조를 사용 합니다.  자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md) 설명서를 참조하세요.

<a name="queries-per-second-qps"></a>초당 **쿼리 수 (QPS)** : 1 초 이내에 서비스 또는 플랫폼에 대해 수행할 수 있는 쿼리 또는 요청 수입니다. 

## <a name="r"></a>R

<a name="radial-search"></a>**방사형 검색**: 요소에서 고정 된 직선 거리 (crowdidia)를 검색 하는 공간 쿼리입니다. 

<a name="raster-data"></a>**래스터 데이터**: 각 셀에 온도와 같은 정보를 나타내는 값이 포함 된 행과 열 (또는 그리드)으로 구성 된 셀의 행렬 (또는 픽셀)입니다. 래스터에는 디지털 항공 사진, 위성 이미지, 디지털 사진 및 스캔된 지도가 포함됩니다.

<a name="raster-layer"></a>**래스터 계층**: 래스터 이미지로 구성 된 타일 계층입니다.

<a name="reachable-range"></a>연결 가능한 **범위**: 연결 가능한 범위는 이동 하는 데 사용할 모드에 대 한 지정 된 시간 또는 거리 내에서 다른 사람이 이동 하는 영역을 정의 합니다. [등시선](#isochrone) 및 [등거리선](#isodistance)을 참조하세요.

<a name="remote-sensing"></a>**원격 감지**: 거리에서 센서 데이터를 수집 하 고 해석 하는 프로세스입니다.

<a name="rest-service"></a>**Rest 서비스**: rest 머리글자어는 Representational State Transfer를 나타냅니다. REST 서비스는 통신하기 위해 기본 웹 기술을 사용하는 URL 기반 웹 서비스이며 가장 일반적인 방법은 HTTP GET 및 POST 요청입니다. 이러한 형식의 서비스는 기존 SOAP 기반 서비스보다 훨씬 빠르고 작은 경향이 있습니다.

<a name="reverse-geocode"></a>**역방향 geocode**: 좌표를 가져오고가 지도에서가 나타내는 주소를 확인 하는 프로세스입니다.

<a name="reproject"></a>**Reproject**: [변환](#transformation)을 참조 하세요.

<a name="rest-service"></a>**REST 서비스**: Representational State Transfer에 대 한 머리글자어입니다. 분산되고 광범위한 환경에서 피어 간에 정보를 교환하는 아키텍처입니다. REST를 사용 하면 다른 컴퓨터의 프로그램이 운영 체제 또는 플랫폼과 독립적으로 통신할 수 있습니다. 서비스는 URL (uniform resource locator)에 대 한 HTTP (하이퍼텍스트 전송 프로토콜) 요청을 전송 하 고 데이터를 다시 가져올 수 있습니다.

<a name="route"></a>**Route**: 두 개 이상의 위치 간 경로입니다. 경로를 따라 waypoints에 대 한 지침과 같은 추가 정보를 포함할 수도 있습니다.

<a name="requests-per-second-rps"></a>초당 **요청 수 (RPS)** : [초당 쿼리 수 (QPS)](#queries-per-second-qps)를 참조 하십시오. 

<a name="rss"></a>**RSS**: 원본에 따라 매우 간단한 배포, RDF (리소스 설명 프레임 워크) 사이트 요약 또는 리치 사이트 요약의 약어입니다. 여러 웹 사이트에서 콘텐츠를 공유하는 간단하고 구조화된 XML 서식입니다. RSS 문서에는 작성자, 날짜, 제목, 간략한 설명 및 하이퍼텍스트 링크 등 키 메타데이터 요소가 포함됩니다. 이 정보는 사용자(또는 RSS 게시자 서비스)가 추가 정보가 필요한 자료를 결정하는 데 도움이 됩니다.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**위성 이미지**: 비행기와 위성에서 캡처한 이미지를 가리킵니다.

<a name="secondary-key"></a>**보조 키**: Azure Maps 공유 키 인증을 위해 제공 되는 두 가지 구독 키 중 두 번째입니다. [공유 키 인증](#shared-key-authentication)을 참조 하세요.

<a name="shapefile-shp"></a>**Esri (.shp)** : ESRI esri 라고도 하는는 지리적 기능의 위치, 모양 및 특성을 저장 하는 벡터 데이터 저장소 형식입니다. 셰이프 파일은 관련된 파일 집합에 저장됩니다.

<a name="shared-key-authentication"></a>**공유 키 인증**: 공유 키 인증은 Azure Maps에 대 한 각 요청과 함께 계정 생성 키 Azure Maps 전달에 의존 합니다. 이러한 키를 주로 구독 키 라고 합니다. 보안을 위해 키를 정기적으로 다시 생성 하는 것이 좋습니다. 다른 키를 다시 생성 하는 동안 한 키를 사용 하 여 연결을 유지할 수 있도록 두 개의 키가 제공 됩니다. 키를 다시 생성하는 경우 이 계정에 액세스하는 모든 애플리케이션이 새 키를 사용하도록 업데이트해야 합니다. Azure Maps 인증에 대 한 자세한 내용은 [Azure Maps 및 AZURE AD](azure-maps-authentication.md) 및 [Azure Maps에서 인증 관리](how-to-manage-authentication.md)를 참조 하세요.

<a name="software-development-kit-sdk"></a>**SDK (소프트웨어 개발 키트)** : 개발자가 API를 사용 하 여 앱을 빌드하는 데 도움이 되는 설명서, 샘플 코드 및 샘플 앱 모음입니다.

<a name="spherical-mercator-projection"></a>**구면 Mercator 프로젝션**: [웹 Mercator](#web-mercator)를 참조 하세요. 

<a name="spatial-query"></a>**공간 쿼리**: 공간 작업을 수행 하는 서비스에 대 한 요청입니다. 방사형 검색 또는 경로 검색 등이 있습니다.

<a name="spatial-reference"></a>**공간 참조**: 지리적 엔터티를 정확 하 게 찾는 데 사용 되는 좌표 기반 지역, 지역 또는 전역 시스템입니다. 지도 좌표를 실제 위치에 연결하는 데 사용되는 좌표계를 정의합니다. 공간 참조는 정확한 보기 또는 분석을 위해 여러 계층 또는 원본의 공간 데이터를 통합할 수 있도록 합니다. Azure Maps는 입력 기하 도형 데이터에 대해 [EPSG:3857](https://epsg.io/3857) 참조 좌표계 및 WGS 84를 사용합니다.

<a name="sql-spatial"></a>**SQL 공간**: SQL Azure에 기본 제공 되는 공간 기능 및 SQL Server 2008 이상을 참조 합니다. 이 공간 기능은 SQL Server와 독립적으로 사용할 수 있는 .NET 라이브러리로 사용할 수 있습니다. 자세한 내용은 [공간 데이터(SQL Server) 설명서](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)를 참조하세요.

<a name="subscription-key"></a>**구독 키**: [공유 키 인증](#shared-key-authentication)을 참조 하세요.

<a name="synchronous-request"></a>**동기 요청**: HTTP 요청은 연결을 열고 응답을 기다립니다. 브라우저는 페이지에서 수행할 수 있는 동시 HTTP 요청의 수를 제한합니다. 동시에 여러 개의 장기 실행 동기 요청이 수행 되는 경우이 한도에 도달할 수 있습니다. 요청은 다른 요청 중 하나가 완료 될 때까지 지연 됩니다.

## <a name="t"></a>T

<a name="telematics"></a>**텔레매틱스**: 원격 개체의 주는 제어와 함께 통신 장치를 통해 정보를 보내고 받고 저장 합니다. 

<a name="temporal-data"></a>**Temporal 데이터**: 특히 시간 또는 날짜를 참조 하는 데이터입니다. 임시 데이터는 번개와 같은 개별 이벤트, 기차와 같은 이동하는 개체 또는 트래픽 센서의 개수와 같은 반복된 관찰을 가리킬 수 있습니다.

<a name="terrain"></a>**지형**: 샌 트 지형 또는 mountainous와 같은 특정 특성을 갖는 지역입니다.

<a name="thematic-maps"></a>**테마 maps**: 테마 map은 지리적 영역에 대 한 테마를 반영 하기 위해 작성 된 간단한 맵입니다. 이러한 유형의 지도에 대 한 일반적인 시나리오는 데이터의 일부 메트릭에 따라 국가/지역과 같은 관리 영역에 색을 설정 하는 것입니다.

<a name="tile-layer"></a>**타일 계층**: 지도 타일 (사각형 섹션)을 연속 계층으로 조합 하 여 표시 되는 계층입니다. 타일은 래스터 이미지 타일 또는 벡터 타일입니다. 래스터 타일 계층은 일반적으로 미리 렌더링 되며 서버에 이미지로 저장 됩니다. 래스터 타일 계층은 많은 저장소 공간을 사용할 수 있습니다. 벡터 타일 계층은 클라이언트 응용 프로그램 내에서 실시간으로 렌더링 됩니다. 따라서 벡터 타일 계층의 경우 서버 쪽 저장소 요구 사항이 더 작습니다.

<a name="time-zone"></a>**표준 시간대**: 법률, 상용 및 소셜 목적에 대해 균일 한 표준 시간을 관찰 하는 전 세계 지역입니다. 표준 시간대는 국가/지역과 하위 범위를 따르는 경향이 있습니다.

<a name="transaction"></a>**트랜잭션**: Azure Maps는 트랜잭션 라이선스 모델을 사용 합니다.

- 요청된 모든 15개의 지도 또는 트래픽 타일에 대해 하나의 트랜잭션이 만들어집니다.
- Azure Maps 서비스 중 하나에 대 한 각 API 호출에 대해 하나의 트랜잭션이 생성 됩니다. 검색 및 라우팅은 Azure Maps 서비스의 예입니다.

<a name="transformation"></a>**변환**: 서로 다른 지리적 좌표계 간에 데이터를 변환 하는 프로세스입니다. 예를 들어 영국에서 캡처되고 OSGB 1936 지리 좌표계에 기반한 일부 데이터가 있을 수 있습니다. Azure Maps는 WGS84라는 [EPSG:3857](https://epsg.io/3857) 참조 좌표계 변수를 사용합니다. 따라서 데이터를 올바르게 표시하려면 여러 시스템 간에 좌표를 변환해야 합니다.

<a name="traveling-salesmen-problem-tsp"></a>Hamiltonian **Salesmen 문제 (TSP)** : 영업 사원이 일련의 중지를 방문 하는 가장 효율적인 방법을 찾고 시작 위치로 돌아가는 회로 문제입니다.  

<a name="trilateration"></a>**Trilateration**: 세 점 사이의 거리를 측정 하 여 두 개의 다른 점과 관련 하 여 지구 표면의 점의 위치를 확인 하는 프로세스입니다.

<a name="turn-by-turn-navigation"></a>**단계별 탐색**: 사용자가 다음 행동에 도달할 때 경로의 각 단계에 대 한 경로 지침을 제공 하는 응용 프로그램입니다.

## <a name="v"></a>V

<a name="vector-data"></a>**벡터 데이터**: 요소, 선 또는 다각형으로 표현 되는 데이터를 기반으로 하는 좌표입니다.

<a name="vector-tile"></a>**Vector 타일**: 맵 컨트롤과 동일한 타일 시스템을 사용 하 여 지리 공간적 벡터 데이터를 저장 하기 위한 개방형 데이터 사양입니다. [타일 계층](#tile-layer)을 참조하세요.

<a name="vehicle-routing-problem-vrp"></a>**차량 라우팅 문제 (VRP)** : 일련의 차량에 대 한 일련의 주문 된 경로를 계산 하는 동시에 제약 조건 집합을 고려 하는 문제를 해결 하는 클래스입니다. 이러한 제약 조건에는 배달 시간 창, 여러 경로 용량 및 여행 기간 제약 조건이 포함 될 수 있습니다.

<a name="voronoi-diagram"></a>**Voronoi 다이어그램**: 일련의 기하학적 개체 (일반적으로 point 기능)를 둘러싸는 영역 또는 셀로 이루어진 공간 파티션입니다. 이러한 셀 또는 다각형은 드로네 삼각형의 조건을 충족해야 합니다. 영역 내의 모든 위치는 집합의 다른 개체보다 둘러싸고 있는 개체에 더 가깝습니다. 보로노이 다이어그램은 지리적 기능에 영향을 주는 영역을 나타내는 데 자주 사용됩니다. 

## <a name="w"></a>W

<a name="waypoint"></a>이동 **경로**: 이동 경로는 탐색 목적으로 사용 되는 경도 및 위도로 정의 된 지정 된 지리적 위치입니다. 경로를 이동하는 사용자가 있는 지점을 나타내는 데 자주 사용됩니다.

<a name="waypoint-optimization"></a>**중간 지점 최적화**: 제공 된 모든 waypoints를 통과 하는 데 필요한 이동 시간이 나 거리를 최소화 하기 위해 waypoints 집합을 다시 정렬 하는 프로세스입니다. 최적화의 복잡성에 따라 이러한 최적화를 주로 [여행 Salesmen 문제](#traveling-salesmen-problem-tsp) 또는 [차량 라우팅 문제](#vehicle-routing-problem-vrp)라고 합니다.

<a name="web-map-service-wms"></a>**Wms (웹 맵 서비스**): wms는 이미지 기반 맵 서비스를 정의 하는 개방형 지리 컨소시엄 (OGC) 표준입니다. WMS 서비스는 주문형 지도 내에서 특정 영역에 대한 지도 이미지를 제공합니다. 이미지는 미리 렌더링된 기호를 포함하고 서비스에서 정의하는 경우 몇 가지 명명된 스타일 중 하나로 렌더링될 수 있습니다.

<a name="web-mercator"></a>**웹 Mercator**: 구면 Mercator 프로젝션이 라고도 합니다. 주로 웹 기반 매핑 프로그램에서 사용 되는 Mercator 프로젝션을 약간 변형 한 것입니다. 소규모 지도에 사용되는 표준 메르카토르 도법과 동일한 수식을 사용합니다. 그러나 웹 Mercator는 모든 눈금에서 구면 수식을 사용 하지만 대규모 Mercator 맵은 일반적으로 타원 형태의 프로젝션을 사용 합니다. 차이는 글로벌 규모에서 imperceptible 로컬 영역의 맵은 동일한 규모의 진정한 타원 Mercator 지도에서 약간의 차이가 있습니다.

<a name="wgs84"></a>**WGS84**: 공간 좌표를 지도 표면의 위치와 연결 하는 데 사용 되는 상수 집합입니다. WGS84 자료는 온라인 매핑 공급자 및 GPS 디바이스에서 사용되는 표준입니다. Azure Maps는 WGS84라는 [EPSG:3857](https://epsg.io/3857) 참조 좌표계 변수를 사용합니다.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z 좌표**: 고도 [를 참조 하세요](#altitude). 

<a name="zip-code"></a>**우편**번호: [우편](#postal-code)번호를 참조 하세요.

<a name="Zoom level"></a>**확대/축소 수준**: 세부 수준 및 표시 되는 지도의 크기를 지정 합니다. 수준 0으로 모든 방법을 확대 하면 전체 세계 지도가 표시 되는 경우가 많습니다. 그러나 지도에는 국가/지역 이름, 테두리 및 바다 이름과 같은 제한 된 정보가 표시 됩니다. 수준 17에 가깝게 확대하면 지도는 자세한 정보를 포함한 몇 가지 도시 블록 영역을 표시합니다. Azure maps에서 가장 높은 확대/축소 수준은 22입니다. 자세한 내용은 [확대/축소 수준 및 타일 그리드 설명서](zoom-levels-and-tile-grid.md)를 참조하세요.

