---
title: Video Indexer에서 브랜드 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer 브랜드 모델의 개념과 이를 사용자 지정하는 방법을 간략하게 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 15f4b8b6d4a27e721dfcaed8b9dd700e87e3a257
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459016"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Video Indexer에서 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠에 멘션되거나, 비디오의 시각적 텍스트에 표시되는 경우 Video Indexer는 콘텐츠에서 해당 항목을 브랜드로 검색합니다. 브랜드는 컨텍스트를 사용하여 다른 용어와 구분됩니다.

브랜드 검색은 콘텐츠 보관 및 검색, 컨텍스트 광고, 소셜 미디어 분석, 소매 경쟁 분석 등 다양한 비즈니스 시나리오에서 유용합니다. Video Indexer 브랜드 검색을 사용하면 Bing 브랜드 데이터베이스뿐만 아니라 각 Video Indexer 계정의 사용자 지정 브랜드 모델 빌드를 통한 사용자 지정을 사용하여 연설 및 시각적 텍스트에서 브랜드 멘션을 인덱싱할 수 있습니다. 사용자 지정 브랜드 모델 기능을 사용하여 Video Indexer가 Bing 브랜드 데이터베이스에서 브랜드를 검색할지, 특정 브랜드를 검색되지 않도록 제외할지(브랜드의 차단 목록 생성), Bing의 브랜드 데이터베이스에 없을 수 있는 모델 부분에 해당하는 브랜드를 포함할지(브랜드의 허용 목록 생성)를 선택할 수 있습니다. 사용자가 만드는 사용자 지정 브랜드 모델은 사용자가 모델을 만든 계정에서만 사용할 수 있습니다.

## <a name="out-of-the-box-detection-example"></a>기본 제공 검색 예

[Microsoft Build 2017 2일차](https://www.videoindexer.ai/media/ed6ede78ad/) 프레젠테이션에서는 “Microsoft Windows” 브랜드가 여러 번 표시됩니다. 때로는 대본에서, 때로는 시각적 텍스트로 사용되며, 축자로는 사용되지 않습니다. Video Indexer는 컨텍스트를 기준으로 용어가 실제로 브랜드인지를 높은 정밀도로 검색하며, 기본적으로 90k개가 넘는 브랜드를 처리하고 지속적으로 업데이트됩니다. 02:25에 Video Indexer는 연설에서 브랜드를 검색한 다음, 02:40에 Windows 로고의 일부인 시각적 텍스트에서 다시 검색합니다.

![브랜드 개요](./media/content-model-customization/brands-overview.png)

생성 컨텍스트에서 Windows에 대해 말할 경우 컨텍스트에서 구분하는 방법을 알고 있는 고급 Machine Learning 알고리즘에 따라 “Windows” 단어가 브랜드로 검색되지 않으며 Box, Apple, Fox 등의 경우도 마찬가지입니다. 브랜드 검색은 지원되는 모든 언어에서 작동합니다. [전체 Microsoft Build 2017 2일차 키노트 비디오 및 인덱스](https://www.videoindexer.ai/media/ed6ede78ad/)를 보려면 여기를 클릭하세요.

사용자 고유의 브랜드를 가져오려면 다음 단계를 확인합니다.

## <a name="next-steps"></a>다음 단계

[API를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)

[웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-website.md)
