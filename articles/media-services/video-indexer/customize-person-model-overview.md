---
title: Video Indexer에서 개인 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 개인 모델의 개념과 이를 사용자 지정하는 방법을 간략하게 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 03/19/2019
ms.author: anzaman
ms.openlocfilehash: b491120639421d85d2fbb1a0efb2b6dd09ec1d4c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553728"
---
# <a name="customize-a-person-model-in-video-indexer"></a>Video Indexer에서 개인 모델 사용자 지정

Video Indexer에서 비디오의 유명인 인식을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. Video Indexer에서 인식 되지 않는 얼굴 여전히 검색 됩니다 있지만 명명 되지 않은 합니다. 고객은 사용자 지정 사용자 모델을 빌드할 수 있으며 기본적으로 인식 되지 않는 얼굴 인식 하도록 Video Indexer를 사용 하도록 설정 됩니다. 고객의 개인의 얼굴 이미지 파일을 사용 하 여 사람의 이름을 연결 하 여 이러한 사용자 모델을 빌드할 수 있습니다.  

계정에 다른 사용 사례에 맞는, 계정당 여러 개인 모델을 만들 수 없도록 얻을 수 있습니다. 예를 들어 계정에 콘텐츠를 다른 채널에 정렬 되어야 하는 경우 각 채널에 대 한 별도 사용자 모델을 만드는 것이 좋습니다. 

> [!NOTE]
> 각 사용자 모델은 최대 1 백만 사용자를 지원 하며 계정 마다 사용자 모델 50 개로 제한 합니다. 

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오에 대 한 새 얼굴 교육 비디오와 연결 된 특정 사용자 지정 모델을 업데이트 합니다. 

다중 개인 모델 지원이 필요하지 않은 경우, 업로드/인덱싱하거나 다시 인덱싱할 때 비디오에 개인 모델 ID를 할당하지 마세요. 이 경우 Video Indexer 계정에서 기본 사용자 모델을 사용 됩니다. 

에 설명 된 대로 비디오에서 검색 된 얼굴을 편집 하 고 관리 계정에 여러 사용자 지정 사용자 모델 Video Indexer 웹 사이트를 사용할 수 있습니다 합니다 [웹 사이트를 사용 하 여 사용자 모델을 사용자 지정](customize-person-model-with-website.md) 항목입니다. 에 설명 된 대로 API를 사용할 수도 있습니다 [Api를 사용 하 여 사용자 모델을 사용자 지정](customize-person-model-with-api.md)합니다.