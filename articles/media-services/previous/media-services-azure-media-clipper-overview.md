---
title: Azure Media Clipper를 사용하여 클립 만들기 | Microsoft Docs
description: 자산을 사용하여 미디어 클립을 빌드하는 도구인 Azure Media Clipper 개요
services: media-services
keywords: 클립, 하위 클립, 인코딩, 미디어
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 35f1f359b44af00000ccd9047673b80ca541d376
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61243879"
---
# <a name="create-clips-with-azure-media-clipper"></a>Azure Media Clipper를 사용하여 클립 만들기 

Azure Media Clipper는 웹 개발자가 사용자에게 미디어 클립을 만들기 위한 인터페이스를 제공할 수 있는 무료 JavaScript 라이브러리입니다. 이 도구는 모든 웹 페이지에 통합할 수 있으며 자산 로드 및 클리핑 작업 제출을 위한 API를 제공합니다.

Azure Media Clipper를 사용하면 다음을 수행할 수 있습니다.
- 라이브 보관에서 사전 슬레이트 및 사후 슬레이트 자르기 
- AMS 라이브 이벤트, 라이브 보관 또는 fMP4 VOD 파일에서 비디오 하이라이트 작성 
- 여러 원본에서 비디오 연결 
- AMS 미디어 자산에서 요약 클립 생성 
- 프레임 정확도를 사용하여 비디오 클리핑 
- GOP(Group of Pictures) 정확도를 사용하여 기존 라이브 및 VOD 자산에 동적 매니페스트 필터 생성 
- 미디어 서비스 계정의 자산에 대해 인코딩 작업 생성

새로운 기능을 요청하거나 아이디어 또는 피드백을 제공하려면 [Azure Media Services용 UserVoice](https://aka.ms/amsvoice/)에 제출합니다. 문제 또는 질문이 있거나 버그를 발견한 경우 Media Services 팀(amcinfo@microsoft.com)에 문의하세요.

다음 이미지는 Clipper 인터페이스: ![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)를 보여 줍니다.

## <a name="release-notes"></a>릴리스 정보
Clipper 블로그 게시물, 여러 알려진 문제, 최신 Clipper 릴리스의 변경 로그는 다음 목록을 참조하세요.
- [블로그 게시물](https://azure.microsoft.com/blog/azure-media-clipper/)
- [알려진 문제 목록](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [변경 로그](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>브라우저 지원
Azure Media Clipper는 최신 HTML5 기술을 사용하여 빌드되었으며 다음 브라우저를 지원합니다.

- Microsoft Edge 13+
- Internet Explorer 11+
- Chrome 54+
- Safari 10+
- Firefox 50+

> [!NOTE]
> 현재 Azure Media Services에서는 스트림의 HTML5 재생만 지원됩니다.

## <a name="language-support"></a>언어 지원
Clipper 위젯은 다음 18개 언어로 제공됩니다.
- 중국어 (간체)
- 중국어 (번체)
- 체코어
- 네덜란드어, 플라망어
- 영어
- 프랑스어
- 독일어
- 헝가리어
- 이탈리아어
- 일본어
- 한국어
- 폴란드어
- 포르투갈어(브라질)
- 포르투갈어(포르투갈)
- 러시아어
- 스페인어
- 스웨덴어
- 터키어

## <a name="next-steps"></a>다음 단계
Azure Media Clipper를 사용하려면 [시작](media-services-azure-media-clipper-getting-started.md) 문서에서 위젯을 배포하는 방법에 대해 자세히 읽어보세요.
