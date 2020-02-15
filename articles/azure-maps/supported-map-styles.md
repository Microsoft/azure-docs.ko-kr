---
title: 지원 되는 지도 스타일 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 맵에서 지 원하는 다양 한 지도 렌더링 스타일에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9cdfd0d029057e36e010203b7c35a5aafee4b574
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77208287"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps에서 지원되는 지도 스타일
Azure Maps는 아래 설명된 대로 여러 가지 기본 제공 지도 스타일을 지원합니다.

## <a name="road"></a>도로
**도로** 지도는 도로, 자연적 특징 및 인공적 특징을 해당 특징에 대한 레이블과 함께 표시하는 표준 지도입니다.

![도로 지도 스타일](./media/supported-map-styles/road.png)

**적용 가능한 API:**
* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="blank-and-blank_accessible"></a>blank 및 blank_accessible

**비어** 있는 지도와 **blank_accessible** 지도 스타일은 데이터를 시각화할 빈 캔버스를 제공 합니다. 기본 지도가 표시 되지 않더라도 **blank_accessible** 스타일은 지도 위치 세부 정보를 사용 하 여 화면 판독기 업데이트를 계속 제공 합니다.

> [!Note]
> 웹 SDK에서 지도 DIV 요소의 CSS `background-color` 스타일을 설정 하 여 지도의 배경색을 변경할 수 있습니다.

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤

## <a name="satellite"></a>위성 
**위성** 스타일은 위성 및 항공 이미지의 조합입니다.

![위성 타일 지도 스타일](./media/supported-map-styles/satellite.png)

**적용 가능한 API:**
* [위성 타일](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="satellite_road_labels"></a>satellite_road_labels
이 지도 스타일은 위성 및 항공 이미지 위에 겹쳐진 도로 및 레이블의 하이브리드입니다.

![satellite_road_labels 지도 스타일](./media/supported-map-styles/satellite_road_labels.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="grayscale_dark"></a>grayscale_dark
**짙은 회색조**는 도로 지도 스타일의 어두운 버전입니다.

![gray_scale 지도 스타일](./media/supported-map-styles/grayscale_dark.png)

**적용 가능한 API:**
* [지도 이미지](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤 
* Android 맵 컨트롤


## <a name="grayscale_light"></a>grayscale_light
**회색조 라이트** 는도로 지도 스타일의 조명 버전입니다.

![회색조 연한 지도 스타일](./media/supported-map-styles/grayscale_light.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤


## <a name="night"></a>야간
**야간**은 색이 지정된 도로 및 기호가 포함된 어두운 버전의 도로 지도 스타일입니다.

![야간 지도 스타일](./media/supported-map-styles/night.png)

**적용 가능한 API:**
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤

## <a name="road_shaded_relief"></a>road_shaded_relief
**도로 음영 입체**는 지구의 등고선으로 채워진 Azure Maps 주요 스타일입니다.

![음영 된 릴리프 맵 스타일](./media/supported-map-styles/shaded-relief.png)

**적용 가능한 API:**
* [지도 타일](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* 웹 SDK 맵 컨트롤
* Android 맵 컨트롤


## <a name="next-steps"></a>다음 단계

Azure Maps에서 지도 스타일을 설정 하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [지도 스타일 선택](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
