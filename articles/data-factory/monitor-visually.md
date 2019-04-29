---
title: Azure Data Factory 시각적 모니터링 | Microsoft Docs
description: Azure Data Factory를 시각적으로 모니터링하는 방법에 대해 알아봅니다
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60717072"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure Data Factory를 시각적으로 모니터링하는 방법에 대해 알아봅니다
Azure Data Factory는 데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 컴퓨팅 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 애플리케이션의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다.

이 빠른 시작에서는 코드를 전혀 작성하지 않고 Data Factory 파이프라인을 시각적으로 모니터링하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="monitor-data-factory-pipelines"></a>Data Factory 파이프라인 모니터링

간단한 목록 보기 인터페이스로 파이프라인과 활동 실행을 모니터링합니다. 모든 실행이 로컬 브라우저 시간대로 표시됩니다. 시간대를 변경할 수 있으며 모든 날짜 시간 필드가 선택된 시간대로 맞춰집니다.  

1. **Microsoft Edge** 또는 **Google Chrome** 웹 브라우저를 시작합니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
3. Azure Portal에서 생성된 데이터 팩터리 블레이드로 이동한 다음 '모니터링 및 관리' 타일을 클릭하여 Data Factory 시각적 개체 모니터링 환경을 시작합니다.

## <a name="monitor-pipeline-runs"></a>파이프라인 실행 모니터링
Data Factory v2 파이프라인에 대해 각 파이프라인 실행을 보여주는 목록 보기. 포함 열:

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인의 이름입니다. |
| 작업 | 활동 실행을 보는 데 사용할 수 있는 단일 작업. |
| 실행 시작 | 파이프라인 실행 시작 날짜 시간 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| 기간 | 실행 기간 (HH:MM:SS) |
| 트리거 주체 | 수동 트리거, 트리거 예약 |
| 상태 | 실패, 성공, 진행 중 |
| 매개 변수 | 파이프라인 실행 매개 변수 (이름, 값 쌍) |
| 오류 | 파이프라인 실행 오류 (있는 경우) |
| 실행 ID | 파이프라인 실행 ID입니다. |

![파이프라인 실행 모니터링](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>작업 실행 모니터링
각 파이프라인 실행에 해당하는 활동 실행을 보여주는 목록 보기. 각 파이프라인 실행에 대한 작업 실행을 보려면 **‘작업’** 열 아래 **‘작업 실행’** 아이콘을 클릭합니다. 포함 열:

| **열 이름** | **설명** |
| --- | --- |
| 작업 이름 | 파이프라인 내 작업의 이름입니다. |
| 작업 유형 | 작업의 유형(예: Copy, HDInsightSpark, HDInsightHive 등)입니다. |
| 실행 시작 | 작업 실행 시작 날짜 시간 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| 기간 | 실행 기간 (HH:MM:SS) |
| 상태 | 실패, 성공, 진행 중 |
| 입력 | 작업 입력에 대해 설명하는 JSON 배열 |
| 출력 | 작업 출력에 대해 설명하는 JSON 배열 |
| 오류 | 작업 실행 오류 (있는 경우) |

![작업 실행 모니터링](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> 파이프라인 및 활동 실행 목록을 새로 고치려면 맨 위의 **‘새로 고침’** 아이콘을 클릭해야 합니다. 자동 새로 고침은 현재 지원되지 않습니다.

![새로 고침](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>모니터링할 데이터 팩터리 선택
왼쪽 상단에 있는 **Data Factory** 아이콘에 커서를 올려 놓습니다. azure 구독 목록 및 모니터링하려는 Data Factory를 보려면 ‘화살표’ 아이콘을 클릭합니다.

![데이터 팩터리 선택](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>목록 보기 구성

### <a name="apply-rich-ordering-and-filtering"></a>풍부한 정렬 및 필터링 적용

파이프라인 실행을 실행 시작을 기준으로 내림차순/오름차순으로 정렬하고 파이프라인 실행을 다음 열을 기준으로 필터링합니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인의 이름입니다. 옵션에는 ‘마지막 24시간’, ‘마지막 주’, ‘마지막 30일’에 대한 빠른 필터 또는 사용자 지정 날짜 시간 선택이 포함됩니다. |
| 실행 시작 | 파이프라인 실행 시작 날짜 시간 |
| 실행 상태 | 상태(성공, 실패, 진행 중)를 기준으로 실행 필터링 |

![Filter](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>열 추가 또는 제거
마우스 오른쪽 단추로 목록 보기 헤더를 클릭하고 목록 보기에 표시하려는 열을 선택합니다

![열](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>열 너비 조정
열 헤더에 커서를 올려 놓아 목록 보기에서 열 너비를 늘리거나 줄입니다

## <a name="promote-user-properties-to-monitor"></a>모니터링하도록 사용자 속성 승격

모니터링할 수 있는 엔터티가 되도록 파이프라인 작업 속성을 사용자 속성으로 승격할 수 있습니다. 예를 들어 파이프라인에서 복사 작업의 **원본** 및 **대상** 속성을 사용자 속성으로 승격할 수 있습니다. **자동 생성**을 선택하여 복사 작업의 **원본** 및 **대상** 사용자 속성을 생성할 수도 있습니다.

![사용자 속성 만들기](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 최대 5개의 파이프라인 작업 속성만을 사용자 속성으로 승격할 수 있습니다.

사용자 속성을 만든 후에 모니터링 목록 보기에서 모니터링할 수 있습니다. 복사 작업의 원본이 테이블 이름인 경우 원본 테이블 이름을 활동 실행 목록 보기의 열로 모니터링할 수 있습니다.

![사용자 속성을 제외한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image2.png)

![활동 실행 목록에 사용자 속성에 대한 열 추가](media/monitor-visually/monitor-user-properties-image3.png)

![사용자 속성의 열을 포함한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>파이프라인 내부에서 활동 다시 실행

이제 파이프라인 내부에서 활동을 다시 실행할 수 있습니다. **활동 실행 보기**를 클릭하고 파이프라인을 다시 실행하려는 지점에서 파이프라인의 활동을 선택합니다.

![활동 실행 보기](media/monitor-visually/rerun-activities-image1.png)

![활동 실행 선택](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>다시 실행 기록 보기

목록 보기에서 모든 파이프라인 실행의 다시 실행 기록을 볼 수 있습니다.

![기록 보기](media/monitor-visually/rerun-history-image1.png)

특정 파이프라인 실행의 다시 실행 기록을 볼 수도 있습니다.

![파이프라인 실행의 기록 보기](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>둘러보기
파이프라인 및 작업 실행을 모니터링하는 방법에 대한 단계별 지침을 확인하려면 왼쪽 하단의 ‘정보 아이콘’을 클릭하고 ‘둘러보기’를 클릭합니다.

![둘러보기](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>사용자 의견
다양한 기능 또는 경험 중인 문제에 대한 의견을 보내려면 ‘사용자 의견’ 아이콘을 클릭합니다.

![사용자 의견](media/monitor-visually/feedback.png)

## <a name="alerts"></a>경고

Data Factory의 지원되는 메트릭에 대해 경고를 발생시킬 수 있습니다. 시작하려면 Data Factory 모니터링 페이지에서 **모니터링 -> 경고 및 메트릭**을 선택합니다.

![](media/monitor-visually/alerts01.png)

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>경고 만들기

1.  **새로운 경고 규칙**을 클릭하여 새 경고를 만듭니다.

    ![](media/monitor-visually/alerts02.png)

1.  규칙 이름을 지정하고 경고 **심각도**를 선택합니다.

    ![](media/monitor-visually/alerts03.png)

1.  경고 조건을 선택합니다.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  경고 논리를 구성합니다. 모든 파이프라인 및 해당 활동의 선택한 메트릭에 대해 경고를 만들 수 있습니다. 특정 활동 유형, 활동 이름, 파이프라인 이름 또는 오류 유형을 선택할 수도 있습니다.

    ![](media/monitor-visually/alerts06.png)

1.  경고에 대해 **이메일/SMS/푸시/음성** 알림을 구성합니다. 경고 알림에 대해 기존 **작업 그룹**을 만들거나 선택합니다.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  경고 규칙을 만듭니다.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) 문서를 참조하세요.
