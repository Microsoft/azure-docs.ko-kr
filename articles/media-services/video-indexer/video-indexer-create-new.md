---
title: 기존 비디오에서 비디오 인사이트 만들기 - Azure
titlesuffix: Azure Media Services
description: 이 항목에서는 기존 비디오 파일에 기반하여 비디오 인사이트를 만들고 게시하는 방법을 설명합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560228"
---
# <a name="create-highlights-from-existing-videos"></a>기존 비디오에서 강조 부분 만들기

이 항목에서는 다른 몇 가지 비디오에 기반하여 비디오 인사이트를 만들고 게시하는 방법에 대해 설명합니다.

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
2. 비디오 인사이트를 만들려는 비디오를 찾습니다.
3. **재생**을 누릅니다.

    페이지에는 비디오의 요약된 인사이트가 표시됩니다. 

    ![자세한 정보](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. **편집** 단추를 누릅니다.

    이 페이지에는 비디오에 대한 전체 분석이 표시됩니다. 분석은 블록으로 구분됩니다. 블록을 사용하면 데이터를 더 쉽게 살펴볼 수 있습니다. 예를 들어 블록은 화자가 변경되거나 오랫동안 일시 중지되는 경우를 기준으로 나눌 수 있습니다. 원하는 줄만 포함된 고유한 재생 목록을 만들 수 있습니다. 원본 비디오의 특정 부분만 표시하려면 주제/키워드, 감정, 사람, 화자를 기준으로 필터링할 수 있습니다. 비디오의 전사 또는 OCR만 표시하도록 선택할 수 있습니다.    

    ![자세한 정보](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. 재생 목록을 만듭니다.

    재생 목록에서 줄을 추가하거나 제거하려면 **+**/**-** 를 누릅니다.
5. 재생 목록을 미리 봅니다.

    재생 목록이 만들어지면 **미리 보기**를 누릅니다.
6. 재생 목록을 게시합니다.

    재생 목록을 미리 본 후에 게시할 수 있습니다.

    재생 목록이 게시되면 비디오 인사이트의 목록에 추가됩니다.


## <a name="next-steps"></a>다음 단계 

새 재생 목록이 만들어지면 다음 항목 중 하나에서 설명한 대로 계속 처리할 수 있습니다. 

- [Video Indexer REST API를 사용하여 콘텐츠 처리](video-indexer-use-apis.md)
- [애플리케이션에 시각적 위젯 포함](video-indexer-embed-widgets.md)

## <a name="see-also"></a>참고 항목

[Video Indexer 개요](video-indexer-overview.md) 
