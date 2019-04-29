---
title: 비디오 인덱서 장면, 스크린샷, 및 키 프레임이-Azure
titlesuffix: Azure Media Services
description: 이 항목에서는 Video Indexer 장면, 스크린샷, 키 프레임의 개요를 제공 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: dfa41dc695cf6ab357a9cd4cdbd32454b6dd107d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560102"
---
# <a name="scenes-shots-and-keyframes"></a>장면, 샷 및 키 프레임

Video Indexer 구조적 및 의미적 속성을 기반으로 하는 temporal 단위로 (32–128kb 비디오를 지원 합니다. 이 기능을 사용 하면 고객을 쉽게 검색, 관리 및 다양 한 세분성에 따라 해당 비디오 콘텐츠를 편집 합니다. 예를 들어, 백그라운드에서, 스크린샷,이 항목에서 설명 하는 키 프레임에 기반 합니다. 합니다 **장면 감지** 기능은 현재 미리 보기로 제공 됩니다.   

![장면, 샷 및 키 프레임](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)

## <a name="scene-detection-preview"></a>장면 감지 (미리 보기)

Video Indexer 장면 비디오 시각 신호에 따라 변경 될 때를 결정 합니다. 장면 단일 이벤트를 보여 주며 의미 체계가 관련이 있는 연속 샷 일련의 구성 됩니다. 장면 미리 보기는 해당 기본 샷의 첫 번째 키 프레임입니다. 비디오 인덱서 연속 샷을에서 색 일관성에 따라 장면에 비디오를 분할 하 고 시작 및 종료 시간 각 장면의 검색 합니다. 장면 검색 비디오의 의미 체계 측면을 정량화 해야 하므로 작업은 매우 복잡할 간주 됩니다.

> [!NOTE]
> 3 개 이상의 장면을 포함 하는 비디오에 적용 됩니다.

## <a name="shot-detection"></a>샷 검색

Video Indexer는 인접 한 프레임의 색 구성표의 갑작스러운 및 점진적으로 전환을 추적 하 여 시각 신호에 따라 비디오의 스크린샷된 변경 될 때를 결정 합니다. 샷의 메타 데이터에는 포함 하는 키 프레임 목록 뿐만 아니라 시작 및 종료 시간을 포함 합니다. 샷을 번에 연속 프레임을 동시에 동일한 카메라에서 가져온 경우

## <a name="keyframe-detection"></a>키 프레임 감지

샷에 가장 잘 나타내는 프레임을 선택 합니다. 키 프레임은 시각적인 속성 (예: 고대비 및 stableness)을 기반으로 전체 비디오에서 선택 된 대표 프레임입니다. Video Indexer에는 고객 키 프레임 축소판 그림을 추출할 수에 따라, 샷의 메타 데이터의 일부로 키 프레임 Id 목록을 검색 합니다. 

키 프레임은 JSON 출력의 스크린샷 연관 됩니다. 

## <a name="next-steps"></a>다음 단계

[API에 의해 생성 된 비디오 인덱서 출력 검토](video-indexer-output-json-v2.md#scenes)