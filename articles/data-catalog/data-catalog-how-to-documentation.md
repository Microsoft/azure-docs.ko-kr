---
title: Azure Data Catalog에서 데이터 원본을 문서화하는 방법
description: Azure Data Catalog에서 데이터 자산을 문서화하는 방법을 강조 표시한 방법 문서입니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 053b1701-b848-4ada-b726-6f485caa9961
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 9049e0b25db37f296e27476fbb93418d13c4dcc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001567"
---
# <a name="document-data-sources"></a>데이터 원본 문서화
## <a name="introduction"></a>소개
**Microsoft Azure 데이터 카탈로그**는 등록 시스템 및 기업 데이터 원본을 위한 검색 시스템 역할을 하는 완전히 관리되는 클라우드 서비스입니다. 다시 말해서 **Azure Data Catalog** 는 사람들이 데이터 원본을 검색하고 *이해*하고 사용하도록 하면서 조직의 기존 데이터로부터 더 많은 가치를 얻어내도록 돕는 역할을 합니다.

**Azure Data Catalog**를 사용하여 데이터 원본을 등록하면 해당 메타데이터를 복사하고 서비스로 인덱싱하지만 여기서 끝이 아닙니다. **Azure Data Catalog** 를 사용하면 사용자에게 데이터 원본에 대한 사용 현황 및 일반적인 시나리오를 설명할 수 있는 고유한 전체 설명서를 제공할 수도 있습니다.

[데이터 원본에 주석을 추가하는 방법](data-catalog-how-to-annotate.md)에서는 데이터 원본에 익숙한 전문가가 태그와 설명이 있는 주석을 추가할 수 있다는 점을 배웁니다. **Azure Data Catalog** 포털은 사용자가 데이터 자산 및 컨테이너를 완벽하게 문서화할 수 있도록 다양한 텍스트 편집기를 포함합니다. 편집기에는 제목, 텍스트 서식, 글머리 기호 목록, 번호 매기기 목록 및 테이블 등 단락 서식이 포함되어 있습니다.

태그와 설명은 간단한 주석에 유용합니다. 그러나 데이터 원본의 사용, 데이터 원본에 대한 비즈니스 시나리오를 더 잘 이해하도록 데이터 소비자를 돕기 위해 전문가는 자세한 전체 설명서를 제공할 수 있습니다. 데이터 원본을 문서화하는 것이 쉽습니다. 데이터 자산 또는 컨테이너를 선택하고 **설명서**를 선택합니다.

![](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>데이터 자산 문서화
**Azure Data Catalog** 설명서를 사용하는 장점은 데이터 카탈로그를 콘텐츠 리포지토리로 사용하여 데이터 자산의 전체 설명을 만들 수 있다는 점입니다. 컨테이너 및 테이블을 설명하는 자세한 내용을 둘러볼 수 있습니다. SharePoint 또는 파일 공유 등의 다른 콘텐츠 리포지토리의 내용이 이미 있는 경우 이 기존 콘텐츠를 참조하는 자산 설명서 링크를 추가할 수 있습니다. 이 기능을 사용해 기존 문서를 더 쉽게 검색할 수 있습니다.

> [!NOTE]
> 설명서는 검색 인덱스에 포함되지 않습니다.
>
>

![](media/data-catalog-documentation/data-catalog-documentation2.png)

설명서의 수준은 데이터 자산 컨테이너의 특성 및 값을 설명하는 정도에서 컨테이너 내의 테이블 스키마에 대한 자세한 설명의 범위까지 다양합니다. 제공된 설명서의 수준은 비즈니스 요구 사항에 따라 결정되어야 합니다. 하지만 데이터 자산을 문서화하는 일반적인 몇 가지 장단점은 다음과 같습니다.

* 컨테이너만 문서화: 모든 콘텐츠를 한 곳에 있지만 사용자가 합리적인된 결정을 내리는 데 필요한 세부 정보는 부족할 수 있습니다.
* 테이블만 문서화: 콘텐츠는 해당 개체에 지정 되지만 사용자가 문서에 대 한 여러 위치.
* 컨테이너 및 테이블 문서화: 가장 포괄적인 접근 방식 하지만 문서의 자세한 유지 관리를 제공 될 수 있습니다.

## <a name="summary"></a>요약
**Azure Data Catalog** 로 데이터 원본을 문서화하는 작업은 필요한 만큼의 세부 정보로 데이터 자산에 대한 설명을 만들 수 있습니다.  링크를 사용하여 기존 문서 및 데이터 자산을 함께 제공하는 기존 콘텐츠 리포지토리에 저장된 콘텐츠에 연결할 수 있습니다. 사용자가 적절한 데이터 자산을 검색하면 일련의 전체 설명서를 가져올 수 있습니다.
