---
title: Azure Portal을 사용하여 Azure Data Lake Analytics에서 작업 모니터링
description: 이 문서에서는 Azure Portal을 사용하여 Azure Data Lake Analytics 작업의 문제를 해결하는 방법을 설명합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 40864bab068659be016161f7dc40243ebbd45174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812615"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Data Lake Analytics에서 작업 모니터링

**모든 작업을 보려면**

1. Azure 포털의 왼쪽 위 모서리에서 **Microsoft Azure** 를 클릭합니다.
2. 데이터 레이크 분석 계정 이름을 가진 타일을 클릭합니다.  **작업 관리** 타일에 작업 요약이 표시됩니다.

    ![Azure 데이터 레이크 분석 작업 관리](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    작업 관리에서 작업 상태를 한 눈에 볼 수 있습니다. 실패한 작업이 있는지 확인합니다.
3. 해당 작업을 보려면 **작업 관리** 타일을 클릭합니다. 작업은 **실행 중**, **대기**, **종료됨**으로 범주화되어 있습니다. **종료됨** 섹션에 실패한 작업이 표시됩니다. 목록에서 먼저 나옵니다. 작업이 많은 경우 **필터** 를 클릭하여 작업을 찾을 수 있습니다.

    ![Azure 데이터 레이크 분석 필터 작업](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. 작업 세부 정보를 열려면 목록에서 실패한 작업을 클릭합니다.

    ![Azure 데이터 레이크 분석 실패 작업](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    **다시 제출** 단추를 확인합니다. 문제를 해결한 후 해당 작업을 다시 제출할 수 있습니다.
5. 이전 스크린샷에서 강조 표시된 부분을 클릭하고 오류 세부 정보를 엽니다.  다음과 같이 표시됩니다.

    ![Azure 데이터 레이크 분석 실패 작업 세부 정보](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    원본 폴더를 찾을 수 없다고 나옵니다.
6. **Duplicate Script**(스크립트 복제)를 클릭합니다.
7. **FROM** 경로를 다음으로 업데이트합니다.

    "/Samples/Data/SearchLog.tsv"
8. **작업 제출**을 클릭합니다.

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 시작](data-lake-analytics-get-started-powershell.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
