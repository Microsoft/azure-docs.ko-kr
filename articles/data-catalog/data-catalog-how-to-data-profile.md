---
title: Azure Data Catalog에서 데이터 프로파일링 데이터 원본을 사용하는 방법
description: Azure Data Catalog에서 데이터 원본을 등록할 경우 테이블 및 열 수준 데이터 프로필을 포함하는 방법 및 데이터 프로필을 사용하여 데이터 원본을 이해하는 방법을 강조한 방법 문서입니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 64185a951b25b4e04ea5fc65aeede9b0e617d0c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001722"
---
# <a name="data-profile-data-sources"></a>데이터 원본 데이터 프로파일링
## <a name="introduction"></a>소개
**Microsoft Azure 데이터 카탈로그** 는 등록 시스템 및 기업 데이터 원본을 위한 검색 시스템 역할을 하는 완전히 관리되는 클라우드 서비스입니다. 다시 말해서 **Azure 데이터 카탈로그** 는 사람들이 데이터 원본을 검색하고 이해하고 사용하도록 도우면서 조직의 기존 데이터로부터 더 많은 가치를 얻어내도록 돕는 역할을 합니다. **Azure Data Catalog**를 사용하여 데이터 원본을 등록하면 해당 메타데이터를 복사하고 서비스로 인덱싱하지만 여기서 끝이 아닙니다.

**Azure Data Catalog**의 **데이터 프로파일링** 기능은 카탈로그에서 지원되는 데이터 원본에서 데이터를 검사하고 해당 데이터에 대한 통계 및 정보를 수집합니다. 데이터 자산의 프로필을 포함하는 것은 쉽습니다. 데이터 자산을 등록하면 데이터 원본 등록 도구에서 **데이터 프로필 포함** 을 선택합니다.

## <a name="what-is-data-profiling"></a>데이터 프로파일링의 정의
데이터 프로파일링은 등록되는 데이터 원본에서 데이터를 검사하고 해당 데이터에 대한 통계와 정보를 수집합니다. 데이터 원본을 검색하는 동안 이러한 통계는 데이터의 적합성을 결정하여 비즈니스 문제를 해결하는 데 도움이 될 수 있습니다.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

다음 데이터 원본은 데이터 프로파일링을 지원합니다.

* SQL Server(Azure SQL DB 및 Azure SQL Data Warehouse 포함) 테이블 및 뷰
* Oracle 테이블 및 뷰
* Teradata 테이블 및 뷰
* Hive 테이블

데이터 자산을 등록할 때 데이터 프로필을 포함하면 사용자가 다음을 비롯한 데이터 원본에 대한 질문에 대답하도록 도움을 줍니다.

* 비즈니스 문제를 해결하는 데 사용할 수 있나요?
* 데이터가 특정 표준 또는 패턴을 확인하나요?
* 일부 데이터 원본에서 비정상은 무엇인가요?
* 내 애플리케이션에 이 데이터를 통합할 경우 발생할 수 있는 문제는 무엇인가요?

> [!NOTE]
> 애플리케이션으로 데이터를 통합하는 방법을 설명하기 위해 자산에 문서를 추가할 수도 있습니다. [데이터 원본을 문서화하는 방법](data-catalog-how-to-documentation.md)을 참조하세요.
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>데이터 원본을 등록할 때 데이터 프로필을 포함하는 방법
데이터 원본의 프로필을 포함하는 것은 쉽습니다. 데이터 원본에 등록할 때 데이터 원본 등록 도구의 **등록할 개체** 패널에서 **데이터 프로필 포함**을 선택합니다.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

데이터 원본을 등록하는 방법에 대해 자세히 알아보려면 [데이터 원본을 등록하는 방법](data-catalog-how-to-register.md) 및 [Azure Data Catalog 시작](data-catalog-get-started.md)을 참조하세요.

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>데이터 프로필을 포함하는 데이터 자산에 대한 필터링
데이터 프로필을 포함하는 데이터 자산을 검색하려면 `has:tableDataProfiles` 또는 `has:columnsDataProfiles`를 검색 단어 중 하나로 포함할 수 있습니다.

> [!NOTE]
> 데이터 원본 등록 도구에서 **데이터 프로필 포함** 을 선택하면 테이블 및 열 수준 프로필 정보가 모두 포함됩니다. 하지만 데이터 카탈로그 API를 통해 데이터 자산을 오직 하나 만의 프로필 정보 집합을 포함하여 등록할 수 있습니다.
>
>

## <a name="viewing-data-profile-information"></a>데이터 프로필 정보 보기
프로필에 적합한 데이터 원본을 찾으면 데이터 프로필 세부 정보를 볼 수 있습니다. 데이터 프로필을 보려면 데이터 자산을 선택하고 데이터 카탈로그 포털 창에서 **데이터 프로필** 을 선택합니다.

![](media/data-catalog-data-profile/data-catalog-view.png)

**Azure Data Catalog** 의 데이터 프로필은 다음을 포함하여 테이블 및 열 프로필 정보를 보여줍니다.

### <a name="object-data-profile"></a>개체 데이터 프로필
* 행 수
* 테이블 크기
* 개체가 마지막으로 업데이트된 시기

### <a name="column-data-profile"></a>열 데이터 프로필
* 열 데이터 형식
* 고유한 값 수
* NULL 값이 있는 행 수
* 열 값에 대한 최소, 최대, 평균 및 표준 편차

## <a name="summary"></a>요약
데이터 프로파일링은 등록된 데이터 자산에 대한 통계와 정보를 제공하여 비즈니스 문제를 해결하기 위해 데이터의 적합성을 결정할 수 있도록 합니다. 주석 달기 및 데이터 원본을 문서화하는 작업과 함께 데이터 프로필은 사용자가 데이터를 잘 이해할 수 있도록 합니다.

## <a name="see-also"></a>관련 항목
* [데이터 원본을 등록하는 방법](data-catalog-how-to-register.md)
* [Azure 데이터 카탈로그 시작](data-catalog-get-started.md)
