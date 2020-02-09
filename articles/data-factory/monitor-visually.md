---
title: 시각적으로 Azure Data Factory 모니터링
description: Azure 데이터 팩터리를 시각적으로 모니터링 하는 방법 알아보기
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: 85b1d6b532ba11819947558226291e62af6b5119
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75690918"
---
# <a name="visually-monitor-azure-data-factory"></a>시각적으로 Azure Data Factory 모니터링

Azure Data Factory에서 파이프라인을 만들어 게시 한 후에는 트리거와 연결 하거나 임시 실행을 수동으로 시작할 수 있습니다. Azure Data Factory 사용자 환경에서 기본적으로 모든 파이프라인 실행을 모니터링할 수 있습니다. 모니터링 환경을 열려면 [Azure Portal](https://portal.azure.com/)데이터 팩터리 블레이드에서 **모니터 & 관리** 타일을 선택 합니다. ADF UX에 이미 있는 경우 왼쪽 세로 막대의 **모니터** 아이콘을 클릭 합니다.

모든 데이터 팩터리 실행은 브라우저의 현지 표준 시간대에 표시 됩니다. 표준 시간대를 변경 하는 경우 모든 날짜/시간 필드가 선택한 항목에 맞춰집니다.

## <a name="monitor-pipeline-runs"></a>파이프라인 실행 모니터링

기본 모니터링 보기는 선택한 기간의 파이프라인 실행 목록입니다. 표시되는 열은 다음과 같습니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인 이름입니다. |
| 작업 | 활동 세부 정보를 보거나, 취소 하거나, 파이프라인을 다시 실행할 수 있는 아이콘 |
| 실행 시작 | 파이프라인 실행에 대한 시작 날짜 및 시간 (MM/DD/YYYY, HH: MM: SS AM/PM) |
| 기간 | 실행 기간 (HH:MM:SS) |
| 트리거 주체 | 파이프라인을 시작한 트리거의 이름입니다. |
| 상태 | **실패**, **성공**, **진행 중**, **취소 됨**또는 **대기** 중 |
| 주석 | 파이프라인에 연결 된 필터링 가능한 태그  |
| 매개 변수 | 파이프라인 실행에 대한 매개 변수 (이름/값 쌍) |
| 오류 | 파이프라인이 실패 한 경우 실행 오류 |
| 실행 ID | 파이프라인 실행 ID입니다. |

![파이프라인 실행 모니터링에 대한 목록 보기](media/monitor-visually/pipeline-runs.png)

파이프라인 및 활동 실행 목록을 새로 고치려면 **새로 고침** 단추를 수동으로 선택 해야 합니다. Autorefresh는 현재 지원 되지 않습니다.

![새로 고침 단추](media/monitor-visually/refresh.png)

## <a name="monitor-activity-runs"></a>작업 실행 모니터링

각 파이프라인 실행에 대한 활동 실행을 보려면 **작업** 열에서 **활동 실행 보기** 아이콘을 선택 합니다. 목록 뷰에는 각 파이프라인 실행에 해당 하는 작업 실행이 표시 됩니다.

| **열 이름** | **설명** |
| --- | --- |
| 작업 이름 | 파이프라인 내의 활동 이름 |
| 작업 유형 | 작업 유형 (예: **복사**, **executedataflow 흐름**또는 **AzureMLExecutePipeline** ) |
| 작업 | JSON 입력 정보, JSON 출력 정보 또는 자세한 작업 관련 모니터링 환경을 볼 수 있는 아이콘 | 
| 실행 시작 | 작업 실행에 대한 시작 날짜 및 시간 (MM/DD/YYYY, HH: MM: SS AM/PM) |
| 기간 | 실행 기간 (HH:MM:SS) |
| 상태 | **실패**, **성공**, **진행 중**또는 **취소 됨** |
| Integration Runtime | 활동이 실행 된 Integration Runtime |
| 사용자 속성 | 활동의 사용자 정의 속성 |
| 오류 | 작업이 실패 한 경우 실행 오류 |
| 실행 ID | 활동 실행 ID입니다. |

![작업 실행 모니터링에 대한 목록 보기](media/monitor-visually/activity-runs.png)

### <a name="promote-user-properties-to-monitor"></a>모니터링하도록 사용자 속성 승격

파이프라인 활동 속성이 사용자 속성으로 승격 하 여 모니터링 하는 엔터티가 됩니다. 예를 들어 파이프라인에 있는 복사 작업의 **원본** 및 **대상** 속성을 사용자 속성으로 승격할 수 있습니다. **자동 생성** 을 선택 하 여 복사 작업의 **원본** 및 **대상** 사용자 속성을 생성 합니다.

![사용자 속성 만들기](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> 최대 5 개의 파이프라인 활동 속성만 사용자 속성으로 승격할 수 있습니다.

사용자 속성을 만든 후 모니터링 목록 보기에서 모니터링할 수 있습니다. 복사 활동에 대한 원본이 테이블 이름인 경우 활동 실행에 대해 목록 뷰의 열로 원본 테이블 이름을 모니터링할 수 있습니다.

![사용자 속성을 제외한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image2.png)

![사용자 속성 열을 활동 실행 목록에 추가](media/monitor-visually/monitor-user-properties-image3.png)

![사용자 속성의 열을 포함한 활동 실행 목록](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="configure-the-list-view"></a>목록 보기 구성

### <a name="order-and-filter"></a>순서 및 필터

실행 시작 시간에 따라 파이프라인 실행을 내림차순 또는 오름차순으로 설정/해제 합니다. 다음 열을 사용 하 여 필터 파이프라인이 실행 됩니다.

| **열 이름** | **설명** |
| --- | --- |
| 파이프라인 이름 | 파이프라인의 이름을 기준으로 필터링 합니다. |
| 실행 시작 |  표시 되는 파이프라인 실행의 시간 범위를 확인 합니다. 옵션에는 **지난 24 시간**, **지난 주**및 **지난 30 일** 에 대한 빠른 필터 또는 사용자 지정 날짜 및 시간 선택이 포함 됩니다. |
| 실행 상태 | **성공**, **실패**, **큐에 대기**, **취소 됨**또는 **진행**중 상태를 기준으로 필터를 실행 합니다. |
| 주석 | 각 파이프라인에 적용 된 태그로 필터링 |
| 실행 | 했더니 파이프라인을 표시할지 여부를 필터링 합니다. |

![필터링 옵션](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>열 추가 또는 제거
목록 뷰 헤더를 마우스 오른쪽 단추로 클릭 하 고 목록 뷰에 표시할 열을 선택 합니다.

![열 옵션](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>열 너비 조정
열 머리글을 마우스로 가리키면 목록 뷰에서 열 너비를 늘리거나 줄입니다.

## <a name="rerun-activities-inside-a-pipeline"></a>파이프라인 내부에서 활동 다시 실행

파이프라인 내에서 작업을 다시 실행할 수 있습니다. **활동 실행 보기**를 선택한 다음 파이프라인을 다시 실행 하려는 파이프라인에서 활동을 선택 합니다.

![활동 실행 보기](media/monitor-visually/rerun-activities-image1.png)

![활동 실행 선택](media/monitor-visually/rerun-activities-image2.png)

### <a name="rerun-from-failed-activity"></a>실패 한 작업에서 다시 실행

작업이 실패 하거나, 시간이 초과 되거나, 취소 되는 경우 실패 한 **작업에서 다시 실행**을 선택 하 여 실패 한 작업에서 파이프라인을 다시 실행할 수 있습니다.

![실패 한 작업 다시 실행](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>다시 실행 기록 보기

목록 보기에서 모든 파이프라인 실행의 다시 실행 기록을 볼 수 있습니다.

![기록 보기](media/monitor-visually/rerun-history-image1.png)

특정 파이프라인 실행의 다시 실행 기록을 볼 수도 있습니다.

![파이프라인 실행의 기록 보기](media/monitor-visually/rerun-history-image2.png)

## <a name="gantt-views"></a>Gantt 보기

Gantt 보기를 사용 하 여 파이프라인 및 활동 실행을 빠르게 시각화할 수 있습니다.

![Gantt 차트의 예](media/monitor-visually/gantt1.png)

파이프라인 당 Gantt 보기 또는 파이프라인에 만든 주석/태그를 기준으로 그룹을 볼 수 있습니다.

![Gantt 차트 주석](media/monitor-visually/gantt2.png)

막대의 길이는 파이프라인의 기간을 알려 줍니다. 막대를 선택 하 여 자세한 내용을 볼 수도 있습니다.

![Gantt 차트 기간](media/monitor-visually/gantt3.png)

## <a name="guided-tours"></a>둘러보기
왼쪽 아래에서 **정보** 아이콘을 선택 합니다. 그런 다음 단계별 **둘러보기** 를 선택 하 여 파이프라인 및 작업 실행을 모니터링 하는 방법에 대한 단계별 지침을 가져옵니다.

![둘러보기](media/monitor-visually/guided-tours.png)

## <a name="alerts"></a>경고

Data Factory의 지원되는 메트릭에 대해 경고를 발생시킬 수 있습니다. Data Factory 모니터링 페이지에서 **모니터링** > **경고 & 메트릭을** 선택 하 여 시작 합니다.

![데이터 팩터리 모니터 페이지](media/monitor-visually/alerts01.png)

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>경고 만들기

1.  새 경고 **규칙** 을 선택 하 여 새 경고를 만듭니다.

    ![새 경고 규칙 단추](media/monitor-visually/alerts02.png)

1.  규칙 이름을 지정 하 고 경고 심각도를 선택 합니다.

    ![규칙 이름 및 심각도 상자](media/monitor-visually/alerts03.png)

1.  경고 조건을 선택 합니다.

    ![대상 조건을 위한 상자](media/monitor-visually/alerts04.png)

    ![조건 목록](media/monitor-visually/alerts05.png)

1.  경고 논리를 구성 합니다. 모든 파이프라인 및 해당 활동의 선택한 메트릭에 대해 경고를 만들 수 있습니다. 특정 활동 유형, 활동 이름, 파이프라인 이름 또는 실패 유형을 선택할 수도 있습니다.

    ![경고 논리를 구성 하기 위한 옵션](media/monitor-visually/alerts06.png)

1.  경고에 대한 전자 메일, SMS, 푸시 및 음성 알림을 구성 합니다. 경고 알림에 대해 작업 그룹을 만들거나 기존 작업 그룹을 선택 합니다.

    ![알림 구성 옵션](media/monitor-visually/alerts07.png)

    ![알림 추가 옵션](media/monitor-visually/alerts08.png)

1.  경고 규칙을 만듭니다.

    ![경고 규칙을 만드는 옵션](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) 문서를 참조 하세요.
