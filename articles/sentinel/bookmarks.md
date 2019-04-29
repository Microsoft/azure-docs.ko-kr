---
title: 찾거나 hunting 책갈피를 사용 하 여 Azure Sentinel 미리 보기로 제공 하는 동안 데이터 기록해 | Microsoft Docs
description: 이 문서에서는 데이터를 추적 하기 위해 Azure Sentinel hunting 책갈피를 사용 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 9a7ceeab6d2ad761752f778038692256bd87624b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596938"
---
# <a name="keep-track-of-data-during-hunting"></a>사냥 하는 동안 데이터 한 추적

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
 
위협 구하기는 일반적으로 방대한 악의적인 동작의 증명 정보에 대 한 로그 데이터를 검토 해야 합니다. 이 과정에서 investigators 기억 전하고 다시 수행 하 고 잠재적인 가설의 유효성을 검사 하 고 손상의 전체 스토리를 이해의 일부로 분석 하고자 하는 이벤트를 찾습니다.
Hunting 책갈피 관련 하다 고 간주 하는 쿼리 결과 함께 Log Analytics에서 실행 한 쿼리를 유지 하 여이 작업을 수행할 수 있도록 합니다. 또한 여 상황에 맞는 조사 결과 기록 하 고 메모 및 태그를 추가 하 여 결과 참조할 수 있습니다. 책갈피가 표시 된 데이터와 쉽게 공동 작업에 대 한 팀 멤버에 표시 됩니다.   

책갈피가 표시 된 데이터에 언제 든 지 다시 방문할 수는 **책갈피** 탭의 **사냥** 페이지. 필터링을 사용 하 고 데이터를 빠르게 찾을 특정 현재 조사에 대 한 옵션을 검색할 수 있습니다. 직접 책갈피가 표시 된 데이터를 볼 수는 또는 **HuntingBookmark** Log Analytics에는 테이블입니다. 이 옵션을 사용 하면 필터링, 요약 및 corroborating 증명 정보를 검색할 수 있도록 다른 데이터 소스를 사용 하 여 책갈피가 표시 된 데이터를 조인 수 있습니다.

클릭 하 여 책갈피가 표시 된 데이터를 시각화할 수도 **조사**합니다. 보기, 조사 하는 대화형 엔터티 그래프 다이어그램 및 타임 라인을 사용 하 여 결과 시각적으로 조사 환경이 시작 됩니다.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Azure Sentinel에서 Log Analytics 쿼리를 실행 합니다.

1. Sentinel Azure 포털에서 클릭 **사냥** 의심 스 럽 고 비정상적인 동작에 대 한 쿼리를 실행 하 합니다.

1. Hunting 캠페인을 실행 하려면 하나를 선택 왼쪽된 검토 및 hunting 쿼리의 결과입니다. 

1. 클릭 **쿼리 결과 볼** 사냥 쿼리에서 **세부 정보** Log Analytics에서 쿼리를 보려면 페이지 결과입니다. 표시 되는 사용자 지정 하는 SSH bruteforce 공격 쿼리를 실행 한 경우의 예는 다음과 같습니다.
  
   ![결과 표시](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>책갈피 추가

1. Log Analytics 쿼리 결과 목록에서 흥미로운 알게 된 정보를 포함 하는 행을 확장 합니다.

4. 행의 끝에 줄임표 (...)를 선택 하 고 선택 **사냥 책갈피 추가**합니다.
5. 오른쪽에 **세부 정보** 페이지에서 이름으로 업데이트 하 고, 태그 및 항목에 대 한 흥미로운 던 식별할 수 있도록 정보를 추가 합니다.
6. 클릭 **저장할** 변경 내용을 커밋 하도록 합니다. 모든 책갈피에 추가 된 데이터 다른 investigators를 사용 하 여 공유 및 공동 작업 조사 환경 위한 첫 번째 단계는 합니다.

   ![결과 표시](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> 또한 Azure Log Analytics 로그 Sentinel 페이지 또는 Log Analytics 페이지에서 플라이 고 Hunting 페이지에서 열린에서 만든 쿼리에에서 시작 하는 임의의 Log Analytics 쿼리를 사용 하 여 책갈피를 사용할 수 있습니다. Azure Sentinel 외부에서 Log Analytics를 시작 하는 경우 책갈피를 추가할 수 없습니다. 

## <a name="view-and-update-bookmarks"></a>책갈피 보기 및 업데이트 

1. Sentinel Azure 포털에서 클릭 **사냥**합니다. 
2. 클릭 합니다 **책갈피** 페이지 중간에 책갈피의 목록을 보려면 탭 합니다.
3. 특정 책갈피를 찾으려면 검색 상자 또는 필터 옵션을 사용 합니다.
4. 아래 표의 오른쪽 세부 정보 창에서 책갈피 세부 정보를 보려면 각 책갈피를 선택 합니다.
5. 태그 및 메모를 업데이트 하려면 편집 가능한 텍스트 상자를 클릭 하 고 클릭 **저장할** 변경 내용을 유지 합니다.

   ![결과 표시](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Log Analytics에서 데이터를 책갈피 보기 

Log Analytics에서 책갈피에 추가 된 데이터를 볼 수는 방법은 여러 가지가 있습니다. 

원하는 책갈피를 선택 하 여 쉽게 책갈피에 추가 된 쿼리, 결과 나 기록을 볼 수는 합니다 **책갈피** 테이블과 세부 정보 창에 제공 된 링크를 사용 합니다. 옵션은 다음과 같습니다. 
- 클릭할 **쿼리 보기** Log Analytics의 원본 쿼리를 봅니다.  
- 클릭할 **책갈피 기록 보기** 를 비롯 한 메타 데이터를 책갈피 모든: 업데이트, 업데이트 된 값 및 업데이트가 발생 한 시간을 변경한 사용자입니다. 

- 클릭 하 여 모든 책갈피에 대 한 책갈피를 원시 데이터를 볼 수 **로그 책갈피** 책갈피 표 형태 위에 있습니다. 이 보기에는 연결 된 메타 데이터 구하기 책갈피 테이블에는 모든 책갈피 표시 됩니다. KQL 쿼리를 사용 하 여 찾으려는 특정 책갈피의 최신 버전으로 필터링 할 수 있습니다.  


> [!NOTE]
> 책갈피를 만드는 방법과에 표시 되 면 간에 심각 하 게 지연 (분 단위) 수를 **HuntingBookmark** 테이블입니다. 책갈피를 먼저 만든 다음 데이터는 수집 된 후이 분석 하는 것이 좋습니다. 

## <a name="delete-a-bookmark"></a>책갈피를 삭제 합니다.
다음을 수행 책갈피를 삭제 하려면: 
1.  열 번째 **사냥 책갈피** 탭 합니다. 
2.  대상 책갈피를 선택 합니다.
3.  선택한 행의 끝에서 줄임표 (...) **Delete 책갈피**합니다.
    
목록에서 책갈피를 제거 하는 책갈피를 삭제 합니다 **책갈피** 탭 합니다.  Log Analytics "HuntingBookmark" 이전 책갈피 항목을 포함 하도록 테이블은 계속 되지만 최신 항목 변경 됩니다 합니다 **SoftDelete** 값을 true로, 이전 책갈피를 필터링 하기 용이 합니다.  책갈피를 삭제 하면 다른 책갈피 또는 경고와 연관 된 조사 환경에서 모든 엔터티는 제거 되지 않습니다. 


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 책갈피를 사용 하 여 찾거나 조사를 실행 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.


- [사전에 위협에 대 한 hunt](hunting.md)
- [Notebook을 사용 하 여 자동화 된 hunting 캠페인 실행](notebooks.md)