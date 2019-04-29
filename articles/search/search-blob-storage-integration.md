---
title: Azure Blob Storage에 전체 텍스트 검색 추가 - Azure Search
description: HTTP REST API를 사용한 코드로 Azure Search 인덱싱용 Azure Blob Storage의 텍스트 콘텐츠를 검색합니다.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: b7e7ecd2a82a8d64967288def9c6ede7a292f72a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127590"
---
# <a name="searching-blob-storage-with-azure-search"></a>Azure Search로 Blob Storage 검색

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 Azure Search를 사용하면 단 몇 번의 클릭으로 Blob의 콘텐츠에 인덱스를 만들고 검색할 수 있습니다. Blob Storage를 통해 검색하려면 Azure Search 서비스를 프로비전해야 합니다. Azure Search의 다양한 서비스 한도 및 가격 책정 계층은 [가격 책정 페이지](https://aka.ms/azspricing)에서 확인할 수 있습니다.

## <a name="what-is-azure-search"></a>Azure Search란?
[Azure Search](https://aka.ms/whatisazsearch)는 개발자가 강력한 전체 텍스트 검색 환경을 웹 및 모바일 애플리케이션에 손쉽게 추가할 수 있도록 하는 검색 서비스입니다. 서비스 형태인 Azure Search는 [99.9% 작동 시간 SLA](https://aka.ms/azuresearchsla)를 제공하면서 검색 인프라를 관리할 필요가 없습니다.

## <a name="index-and-search-enterprise-document-formats"></a>인덱스 및 검색 엔터프라이즈 문서 형식
Azure Blob Storage에서 [문서 추출 ](https://aka.ms/azsblobindexer)을 지원하므로 다음 콘텐츠를 인덱싱할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

이러한 파일 형식에서 텍스트 및 메타데이터를 추출하여 한 번의 쿼리로 여러 파일 형식을 검색할 수 있습니다. 

## <a name="search-through-your-blob-metadata"></a>Blob 메타데이터에서 검색
모든 콘텐츠 형식의 BLOB을 손쉽게 정렬할 수 있는 일반적인 시나리오는 각 BLOB에 대해 사용자 지정 메타데이터와 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 문서 형식에 관계없이 모든 BLOB을 인덱싱할 수 있습니다. 그런 다음 모든 Blob Storage 콘텐츠에서 정렬, 필터링, 패싯을 수행할 수 있습니다.

[Blob 메타데이터 인덱싱에 관해 자세히 알아보세요.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>이미지 검색
Azure Search의 전체 텍스트 검색, 패싯 탐색 및 정렬 기능을 이제 Blob에 저장된 이미지의 메타데이터에 적용할 수 있습니다.

Cognitive Search와 같은 이미지 처리 기술에 포함 [광학 문자 인식 (OCR)](cognitive-search-skill-ocr.md) 식별 [시각적 기능](cognitive-search-skill-image-analysis.md) 시각적 콘텐츠에 인덱스를 각각에서 찾을 수 있게 하는 이미지입니다.

## <a name="index-and-search-through-json-blobs"></a>JSON Blob을 통해 인덱스 만들기 및 검색
JSON이 포함된 Blob에 있는 구조화된 콘텐츠를 추출하도록 Azure Search를 구성할 수 있습니다. Azure Search는 JSON Blob을 읽고, 구조화된 콘텐츠를 Azure Search 문서의 적절한 필드로 구문 분석합니다. Azure Search는 또한 JSON 개체의 배열을 포함하는 Blob을 처리하고 각 요소를 개별 Azure Search 문서에 매핑할 수 있습니다.

JSON 구문 분석은 현재 포털을 통해 구성할 수 없습니다. [Azure Search에서 JSON 구문 분석에 대해 자세히 알아보세요.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>빠른 시작
Azure Search는 Blob Storage 포털 블레이드에서 직접 BLOB에 추가할 수 있습니다.

![](./media/search-blob-storage-integration/blob-blade.png)

**Azure Search 추가**를 클릭하면 기존 Azure Search 서비스를 선택하거나 새 서비스를 만들 수 있는 흐름이 시작됩니다. 새 서비스를 만드는 경우 저장소 계정의 포털 환경 외부로 이동하게 됩니다. 기존 서비스를 선택할 수 있는 저장소 포털 블레이드로 다시 이동하여 **Azure Search 추가** 옵션을 다시 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계
전체 [설명서](https://aka.ms/azsblobindexer)에서 Azure Search Blob Indexer에 대해 자세히 알아봅니다.
