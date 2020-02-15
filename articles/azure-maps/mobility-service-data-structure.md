---
title: Azure Maps의 모바일 서비스 데이터 구조 Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 지도 모바일 서비스를 통해 반환 되는 공통 필드 및 데이터 구조에 대해 설명 합니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 685810a6efa46c8eb3ad6cee0c2424299f0347d8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209616"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps 모바일 서비스의 데이터 구조

이 문서에서는 [모바일 서비스 Azure Maps](https://aka.ms/AzureMapsMobilityService)의 Metro 영역 개념을 소개 합니다. 이 서비스가 공용 전송 중지 및 줄에 대해 쿼리 될 때 반환 되는 일반적인 필드 중 일부에 대해 설명 합니다. 모바일 서비스 Api를 사용 하 여 개발 하기 전에이 문서를 읽어 보는 것이 좋습니다.

## <a name="metro-area"></a>Metro 영역

모바일 서비스 데이터는 지원 되는 metro 영역을 기준으로 그룹화 됩니다. Metro 영역은 도시 경계를 따르지 않습니다. Metro 영역에는 여러 도시, 조밀 채워진 도시 및 주변 도시가 포함 될 수 있습니다. 실제로 국가/지역은 하나의 지하철 영역 일 수 있습니다. 

`metroID`는 [metro 영역 정보 가져오기 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)를 호출 하는 데 사용할 수 있는 METRO 영역 ID입니다. Azure Maps ' "Metro 가져오기" API를 사용 하 여 전송 유형, 전송 기관, 활성 경고 및 선택한 Metro에 대 한 추가 세부 정보를 요청 합니다. 지원 되는 metro 영역 및 metroIDs를 요청할 수도 있습니다. Metro 영역 Id는 변경 될 수 있습니다.

**metroID:** 522 **이름:** 시애틀-Tacoma-Bellevue

![시애틀-지하철-지역](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Id 중지

전송 중지는 두 가지 유형의 Id 인 [GFTS (일반 전송 피드 사양)](https://gtfs.org/) id 및 AZURE MAPS 중지 id로 참조 될 수 있습니다. GFTS ID를 stopKey 라고 하 고 Azure Maps stop ID를 Stopkey 라고 합니다. 전송 중지를 자주 참조 하는 경우 Azure Maps 중지 ID를 사용 하는 것이 좋습니다. stopID는 더 안정적 이며 실제 중지가 있는 한 동일 하 게 유지 될 가능성이 높습니다. GTFS 중지 ID는 더 자주 업데이트 됩니다. 예를 들어 gtfs 중지 ID는 GTFS 공급자 요청에 따라 업데이트 하거나 새 GTFS 버전이 릴리스될 때 업데이트할 수 있습니다. 실제 중지에는 변경 내용이 없지만 GTFS 중지 ID는 변경 될 수 있습니다.

시작 하려면 [주변 전송 API 가져오기](https://aka.ms/AzureMapsMobilityNearbyTransit)를 사용 하 여 가까운 전송 중지를 요청할 수 있습니다.

## <a name="line-groups-and-lines"></a>줄 그룹 및 선

모바일 서비스는 선 및 선 그룹에 병렬 데이터 모델을 사용 합니다. 이 모델은 [Gtfs](https://gtfs.org/) 경로 및 트립 데이터에서 상속 된 변경 사항을 보다 효율적으로 처리 하는 데 사용 됩니다.


### <a name="line-groups"></a>줄 그룹

줄 그룹은 동일한 그룹의 논리적 부분인 모든 줄을 그룹화 하는 엔터티입니다. 일반적으로 선 그룹에는 두 줄이 포함 되 고, 하나는 지점 A에서 B로, 다른 하나는 B에서로 반환 됩니다. 두 줄은 동일한 공용 전송 에이전시에 속하며 동일한 줄 번호를 갖습니다. 그러나 줄 그룹에는 줄이 세 개 이상 있거나 한 줄만 있는 경우가 있을 수 있습니다.


### <a name="lines"></a>선

위에서 설명한 것 처럼 각 줄 그룹은 일련의 선으로 구성 됩니다. 각 줄 그룹은 두 줄로 구성 되 고 각 줄은 방향을 설명 합니다.  그러나 줄 그룹을 구성 하는 줄이 더 많은 경우가 있습니다. 예를 들어 때때로 특정 환경에서 우회 하는 선이 있습니다. 두 경우 모두 동일한 줄 번호에서 작동 합니다. 또한 줄 그룹은 한 줄로 구성 될 수 있습니다. 단일 방향이 있는 원형 선은 한 줄이 있는 (ling 그룹입니다.

시작 하려면 [전송 라인 API 가져오기 API](https://aka.ms/AzureMapsMobilityTransitLine)를 사용 하 여 줄 그룹을 요청할 수 있습니다.


## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 전송 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [전송 데이터를 요청 하는 방법](how-to-request-transit-data.md)

모바일 서비스를 사용 하 여 실시간 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청 하는 방법](how-to-request-real-time-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [모바일 서비스 API 설명서](https://aka.ms/AzureMapsMobilityService)
