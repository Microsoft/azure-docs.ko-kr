---
title: Azure Database for PostgreSQL의 성능 권장 사항
description: 이 문서에서는 PostgreSQL 용 Azure Database에서 성능 권장 사항 기능을 설명 합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/28/2018
ms.openlocfilehash: c5324618eeda90b4ef1a512385fb2f14bf391215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564461"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL의 성능 권장 사항

**적용 대상:** Azure Database for PostgreSQL 9.6 및 10

데이터베이스 성능 향상된을 위해 사용자 지정 된 제안 만들기를 분석 하는 성능 권장 사항 기능을 합니다. 권장을 생성 하려면 분석 스키마를 포함 하 여 다양 한 데이터베이스 특성에 살펴봅니다. 사용 하도록 설정 [쿼리 저장소](concepts-query-store.md) 성능 권장 사항 기능을 완벽 하 게 활용 하 여 서버의 합니다. 성능 권장 사항을 구현한 후 이러한 변경의 영향을 평가 하는 성능을 테스트 해야 합니다. 

## <a name="permissions"></a>권한
성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한**소유자** 또는 **참가자** 권한입니다.

## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

오픈 **성능 권장 사항** 에서 합니다 **지능형 성능** PostgreSQL 서버용 Azure 포털 페이지의 메뉴 표시줄의 섹션입니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

선택 **분석** 분석 시작 된 데이터베이스를 선택 합니다. 워크 로드에 따라 번째 분석을 완료 하려면 몇 분 정도 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스의 전체 검사를 수행합니다. 사용량이 적은 기간 동안 분석을 수행 하는 것이 좋습니다. 

합니다 **권장 사항** 창 발견 된 경우 권장 사항 목록이 표시 됩니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항은 자동으로 적용 되지 않습니다. 권장 사항을 적용할 쿼리 텍스트를 복사 하 고 원하는 클라이언트에서 실행 합니다. 권장 구성 평가를 모니터링 및 테스트 해야 합니다. 

## <a name="recommendation-types"></a>권장 사항 유형

현재 두 가지 유형의 권장 사항 지원 됩니다. *인덱스를 만들* 하 고 *Drop Index*합니다.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항
*인덱스를 만들* 권장 워크 로드에 가장 자주 실행 하거나 시간이 많이 걸리는 쿼리 속도 새 인덱스를 제안 합니다. 이 권장 사항 형식에 필요 [쿼리 저장소](concepts-query-store.md) 사용 하도록 설정 합니다. 쿼리 저장소는 쿼리 정보를 수집 하 고 분석 권장 사항을 확인 하는 데 사용 하는 자세한 쿼리 빈도 및 런타임 통계를 제공 합니다.

### <a name="drop-index-recommendations"></a>인덱스 삭제 권장 사항
PostgreSQL 용 Azure Database는 누락 된 인덱스를 검색 하는 것 외에도 기존 인덱스의 성능을 분석 합니다. 인덱스 드물게 사용 하거나 중복 인 경우 분석기를 삭제 하도록 권장 합니다.


## <a name="next-steps"></a>다음 단계
- Azure Database for PostgreSQL의 [모니터링 및 튜닝](concepts-monitoring.md)에 대해 자세히 알아보세요.

