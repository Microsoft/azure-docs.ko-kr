---
title: Azure Data Lake Analytics에서 되풀이 작업 디버그
description: Azure Data Lake Tools for Visual Studio를 사용하여 비정상적인 되풀이 작업을 디버그하는 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 05/20/2018
ms.openlocfilehash: 5a2935d559a967151c5bdc01c4b0806fe52179b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629760"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>비정상적인 되풀이 작업 문제 해결

이 문서에서는 [Azure Data Lake Tools for Visual Studio](https://aka.ms/adltoolsvs)를 사용하여 되풀이 작업 문제를 해결하는 방법을 보여 줍니다. [Azure Data Lake 및 Azure HDInsight 블로그](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)에서 파이프라인 및 되풀이 작업에 대해 자세히 알아보세요.

되풀이 작업은 일반적으로 동일한 쿼리 논리 및 유사한 입력 데이터를 공유합니다. 예를 들어 매주 월요일 오전 8시에 지난 주의 주간 활성 사용자 수를 계산하는 되풀이 작업이 실행된다고 가정합니다. 이러한 작업에 대한 스크립트는 쿼리 논리를 포함하는 하나의 스크립트 템플릿을 공유합니다. 이러한 작업에 대한 입력은 지난 주에 대한 사용 현황 데이터입니다. 동일한 쿼리 논리 및 유사한 입력을 공유한다는 것은 일반적으로 이러한 작업의 성능이 유사하고 안정적임을 의미합니다. 되풀이 작업 중 하나가 갑자기 비정상적으로 수행되거나, 실패하거나, 크게 느려질 경우 다음 작업을 수행할 수 있습니다.

- 되풀이 작업의 이전 실행에 대한 통계 보고서를 통해 어떤 일이 발생했는지 파악합니다.
- 비정상 작업을 정상 작업과 비교하여 무엇이 변경되었는지 확인합니다.

Azure Data Lake Tools for Visual Studio의 **관련 작업 보기**는 두 사례에서 문제 해결 속도를 높이는 데 도움이 됩니다.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1단계: 되풀이 작업을 찾아서 관련 작업 보기 열기

관련 작업 보기를 사용하여 되풀이 작업 문제를 해결하려면 먼저 Visual Studio에서 되풀이 작업을 찾은 후 관련 작업 보기를 열어야 합니다.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>사례 1: 되풀이 작업에 대 한 url

**도구** > **Data Lake** > **작업 보기**를 통해 작업 URL을 붙여 넣어 Visual Studio에서 작업 보기를 열 수 있습니다. **관련 작업 보기**를 선택하여 관련 작업 보기를 엽니다.

![Data Lake Analytics 도구의 관련 작업 보기 링크](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>사례 2: 되풀이 작업에 있지만 URL이 아닌 파이프라인

Visual Studio에서 서버 탐색기 > Azure Data Lake Analytics 계정 > **파이프라인**을 통해 파이프라인 브라우저를 열 수 있습니다. (서버 탐색기에서 이 노드를 찾을 수 없으면 [최신 플러그 인을 다운로드](https://aka.ms/adltoolsvs)합니다.) 

![파이프라인 노드 선택](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

파이프라인 브라우저에서 Data Lake Analytics 계정에 대한 모든 파이프라인이 왼쪽에 나열됩니다. 파이프라인을 확장하여 모든 되풀이 작업을 찾은 후, 문제가 있는 되풀이 작업을 선택할 수 있습니다. 작업 작업 보기가 오른쪽에 열립니다.

![파이프라인 선택 및 관련 작업 보기 열기](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>2단계: 통계 보고서 분석

요약 및 통계 보고서가 관련 작업 보기의 맨 위에 표시됩니다. 여기에서 문제의 잠재적인 근본 원인을 찾을 수 있습니다. 

1.  보고서에서 X축에는 작업 제출 시간이 표시됩니다. 이를 통해 비정상 작업을 찾을 수 있습니다.
2.  다음 다이어그램의 프로세스를 사용하여 통계를 확인하고, 문제에 대한 정보 및 가능한 해결 방법을 확인할 수 있습니다.

![통계를 확인하기 위한 프로세스 다이어그램](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>3단계: 비정상 작업과 정상 작업 비교

제출된 모든 되풀이 작업은 관련 작업 보기의 맨 아래에 표시되는 작업 목록을 통해 찾을 수 있습니다. 자세한 정보 및 잠재적인 해결 방법을 찾으려면 비정상 작업을 마우스 오른쪽 단추로 클릭합니다. 작업 차이점 보기를 사용하여 비정상 작업과 이전의 정상 작업을 비교합니다.

![작업을 비교하기 위한 바로 가기 메뉴](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

이러한 두 작업 간의 큰 차이를 잘 확인합니다. 이러한 차이로 인해 성능 문제가 일어났을 것입니다. 추가로 확인하려면 다음 다이어그램의 단계를 사용합니다.

![작업 간의 차이점을 확인하기 위한 프로세스 다이어그램](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>다음 단계

* [데이터 기울이기 문제 해결](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [실패한 U-SQL 작업에 대한 사용자 정의 C# 코드 디버그](data-lake-analytics-debug-u-sql-jobs.md)
