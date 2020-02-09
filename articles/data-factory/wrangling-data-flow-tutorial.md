---
title: Azure Data Factory에서 랭 글 링 데이터 흐름 시작
description: 랭 글 링 데이터 흐름을 사용 하 여 Azure Data Factory에서 데이터를 준비 하는 방법에 대한 자습서
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682274"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>랭 글 링 데이터 흐름을 사용 하 여 데이터 준비

## <a name="create-a-wrangling-data-flow"></a>랭 글 링 데이터 흐름 만들기

Azure Data Factory에서 랭 글 링 데이터 흐름을 만드는 방법에는 두 가지가 있습니다. 한 가지 방법은 더하기 아이콘을 클릭 하 고 팩터리 리소스 창에서 **데이터 흐름** 을 선택 하는 것입니다.

![랭 글 링](media/wrangling-data-flow/tutorial7.png)

다른 메서드는 파이프라인 캔버스의 작업 창에 있습니다. **이동 및 변환** 아코디언을 열고 **데이터 흐름** 작업을 캔버스로 끌어 옵니다.

두 방법 모두 열리는 측면 창에서 **새 데이터 흐름 만들기** 를 선택 하 고 **랭 글 링 data flow**를 선택 합니다. 확인을 클릭합니다.

![랭 글 링](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>랭 글 링 데이터 흐름 작성

랭 글 링 데이터 흐름에 대한 **원본 데이터 집합** 을 추가 합니다. 기존 데이터 집합을 선택 하거나 새 데이터 집합을 만들 수 있습니다. 싱크 데이터 집합을 선택할 수도 있습니다. 하나 이상의 원본 데이터 집합을 선택할 수 있지만 지금은 싱크가 하나만 허용 됩니다. 싱크 데이터 집합을 선택 하는 것은 선택 사항 이지만 하나 이상의 원본 데이터 집합이 필요 합니다.

> [!NOTE]
> 제한 된 미리 보기에서는 ADLS Gen 2로 구분 된 텍스트만 사용할 수 있습니다. 

![랭 글 링](media/wrangling-data-flow/tutorial4.png)

**만들기** 를 클릭 하 여 파워 쿼리 온라인 매시업 편집기를 엽니다.

![랭 글 링](media/wrangling-data-flow/tutorial5.png)

코드 없는 데이터 준비를 사용 하 여 랭 글 링 데이터 흐름을 작성 합니다. 사용 가능한 함수 목록은 [변환 함수](wrangling-data-flow-functions.md) 를 참조 하십시오/

![랭 글 링](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>랭 글 링 데이터 흐름 실행 및 모니터링

랭 글 링 데이터 흐름의 파이프라인 디버그 실행을 실행 하려면 파이프라인 캔버스에서 **디버그** 를 클릭 합니다. 데이터 흐름을 게시 하면 **이제 트리거가** 마지막으로 게시 된 파이프라인의 주문형 실행을 실행 합니다. 랭 글 링 데이터 흐름은 기존 Azure Data Factory 트리거를 모두 사용 하 여 예약할 수 있습니다.

![랭 글 링](media/wrangling-data-flow/tutorial3.png)

**모니터** 탭으로 이동 하 여 트리거된 랭 글 링 데이터 흐름 작업 실행의 출력을 시각화 합니다.

![랭 글 링](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>다음 단계

[매핑 데이터 흐름을 만드는](tutorial-data-flow.md)방법에 대해 알아봅니다.