---
title: Azure Data Lake Analytics 작업용 작업 브라우저 및 작업 보기 사용
description: 이 문서에서는 Azure Data Lake Analytics 작업용 작업 브라우저 및 작업 보기를 사용하는 방법을 설명합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616233"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics용 작업 브라우저 및 작업 보기 사용
Azure Data Lake Analytics 서비스는 제출된 작업을 쿼리 저장소에 보관합니다. 이 문서에서는 Azure Data Lake Tools for Visual Studio에서 작업 브라우저 및 작업 보기를 사용하여 기존 작업 정보를 찾는 방법에 대해 배웁니다. 

기본적으로 Data Lake Analytics 서비스는 작업을 30일간 보존합니다. 만료 기간은 Azure Portal에서 사용자 지정 만료 정책을 구성하여 구성할 수 있습니다. 만료 후에는 작업 정보에 액세스할 수 없습니다. 

## <a name="prerequisites"></a>필수 조건
[Data Lake Tools for Visual Studio 필수 구성 요소](data-lake-analytics-data-lake-tools-get-started.md#prerequisites)를 참조하세요.

## <a name="open-the-job-browser"></a>작업 브라우저 열기
작업 브라우저는 Visual Studio에서 **서버 탐색기>Azure>Data Lake Analytics>작업**을 통해 액세스합니다.  작업 브라우저를 사용하여 Data Lake Analytics 계정의 쿼리 저장소에 액세스할 수 있습니다. 작업 브라우저의 왼쪽 쿼리 저장소에는 기본 작업 정보가 표시되고 오른쪽 작업 보기에는 세부적인 작업 정보가 표시됩니다.

## <a name="job-view"></a>작업 보기
작업 보기에는 작업에 대한 세부 정보가 표시됩니다. 작업을 열려면 작업 브라우저에서 작업을 두 번 클릭하거나 Data Lake에서 작업 보기를 클릭하여 엽니다. 작업 URL이 표시된 대화 상자가 나타납니다.

![Data Lake Tools Visual Studio 작업 브라우저](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

작업 보기에 포함된 내용은 다음과 같습니다.

* 작업 요약
  
    작업 보기를 새로 고쳐 실행 중인 작업의 최근 정보가 표시됩니다.
  
  * 작업 그래프(그래프):
    
      작업 상태에는 작업 단계가 요약 표시됩니다.
    
      ![Azure Data Lake Analytics 작업 단계 상태](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * 준비 중: 스크립트를 클라우드로 업로드하고 컴파일 서비스를 사용하여 스크립트를 컴파일 및 최적화합니다.
    * 대기 중: 작업이 충분한 리소스를 기다리거나 계정당 최대 동시 작업 수 한도를 초과한 경우 대기합니다. 우선 순위 설정에 따라 큐 작업의 순서가 결정되며 숫자가 낮을수록 우선 순위가 높습니다.
    * 실행 중: 작업이 Data Lake Analytics 계정에서 실제로 실행 중입니다.
    * 종료하는 중: 작업이 완료되는 중입니다(예: 파일 마무리 중).
      
      작업은 모든 단계에서 실패할 수 있습니다. 예를 들어 Preparing 단계에서 컴파일 오류가 발생하거나 Queued 단계에서 시간 초과 오류, Running 단계에서 실행 오류 등이 발생할 수 있습니다.
  * 기본 정보
    
      기본 작업 정보는 Job Summary 창 아래쪽에 표시됩니다.
    
      ![Azure Data Lake Analytics 작업 단계 상태](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * 작업 결과: 성공 또는 실패입니다. 작업은 모든 단계에서 실패할 수 있습니다.
    * 총 기간: 제출 시간과 종료 시간 간 벽시계 시간(기간)입니다.
    * 총 컴퓨팅 시간: 전체 꼭짓점 실행 시간의 합계로서 작업이 한 가지 꼭짓점에서만 실행되는 시간과 같습니다. 꼭짓점에 대한 자세한 내용은 전체 꼭짓점 수를 참조하세요.
    * 제출/시작/종료 시간: Data Lake Analytics 서비스가 작업 제출을 수신하는 시간/작업 실행을 시작하는 시간/작업 종료에 성공하거나 실패하는 시간입니다.
    * 컴파일/대기 중/실행 중: 준비 중/대기 중/실행 중 단계에서 사용한 벽시계 시간입니다.
    * 계정: 작업을 실행하는 데 사용된 Data Lake Analytics 계정입니다.
    * 작성자: 작업을 제출한 사용자이며, 실제 사람의 계정 또는 시스템 계정일 수 있습니다.
    * 우선 순위: 작업 우선 순위. 번호가 낮을수록 우선 순위가 높습니다. 큐에 있는 작업의 순서에만 영향을 미칩니다. 높은 우선 순위를 설정할 경우 실행 중인 작업을 선취하는 것은 아닙니다.
    * 병렬 처리: 요청된 동시 ADLAU(Azure Data Lake Analytics Unit)의 최대 수(즉, 꼭짓점)입니다. 현재 꼭짓점 한 개는 한 개의 VM 및 두 개의 가상 코어와 6GB RAM과 동일하며, 나중에 Data Lake Analytics 업데이트에서 업그레이드할 수 있습니다.
    * 남은 바이트: 작업이 완료될 때까지 처리해야 하는 바이트입니다.
    * 읽은/쓴 바이트: 작업이 실행되기 시작한 이후 읽은/쓴 바이트입니다.
    * 총 꼭짓점 수: 작업(Job)은 여러 작업(Work)으로 나누어지며, 각 작업(Work)을 꼭짓점이라고 합니다. 이 값은 작업(Job)이 몇 개의 작업(Work)으로 구성되었는가를 나타냅니다. 꼭짓점은 기본 처리 단위, 즉, ADLAU(Azure Data Lake Analytics Unit)와 같으며 꼭짓점은 병렬로 실행할 수 있습니다. 
    * 완료됨/실행 중/실패함: 완료/실행/실패한 꼭짓점의 수. 꼭짓점은 사용자 코드 및 시스템 오류로 실패할 수 있지만 시스템은 실패한 꼭짓점을 자동으로 몇 번 더 재시도합니다. 재시도 후에도 꼭짓점이 계속 실패할 경우 전체 작업이 실패합니다.
* 작업 그래프
  
    U-SQL 스크립트는 입력 데이터를 출력 데이터로 전환하는 논리를 나타냅니다. 스크립트는 준비 중 단계에서 물리적 실행 계획으로 컴파일 및 최적화됩니다. 작업 그래프에는 물리적 실행 계획이 표시됩니다.  아래 다이어그램은 이 프로세스를 보여 줍니다.
  
    ![Azure Data Lake Analytics 작업 단계 상태](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    작업(Job)은 여러 작업(Work)으로 세분화됩니다. 각 작업(Work)을 꼭짓점이라고 합니다. 꼭짓점은 슈퍼 꼭짓점(즉, 단계)으로 그룹화되며 작업 그래프로 시각화됩니다. 작업 그래프의 녹색 단계 카드는 단계를 나타냅니다.
  
    한 단계의 모든 꼭짓점은 동일 데이터 중 다른 부분에서 동일한 종류의 작업을 수행합니다. 예를 들어 한 TB 데이터에 파일이 있고 여기에서 읽은 꼭짓점이 수백 개 있을 경우 각 꼭짓점은 청크를 읽습니다. 이러한 꼭짓점은 동일한 단계로 그룹화되며 동일한 입력 파일 중 다른 부분에 대해 동일한 작업을 수행합니다.
  
  * <a name="state-information"></a>단계 정보
    
      특정 단계에서 일부 숫자는 카드에 표시됩니다.
    
      ![Azure Data Lake Analytics 작업 그래프 단계](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract: 숫자와 작업 메서드로 이름이 지정된 단계 이름입니다.
    * 84개 꼭짓점: 이 단계의 총 꼭짓점 수. 수치는 이 단계에서 세분화된 작업(Work) 수를 나타냅니다.
    * 12.90초/꼭짓점: 이 단계의 평균 꼭짓점 실행 시간. 이 수치는 SUM(전체 꼭짓점 실행 시간)을 (전체 꼭짓점 수)로 나누어 계산합니다. 즉, 병렬 처리에서 실행된 전체 꼭짓점을 할당할 수 있을 경우 전체 단계는 12.90초 안에 완료됩니다. 또한 이 단계의 모든 작업이 연속적으로 완료되는 경우 비용은 꼭짓점 수 * AVG 시간이 됩니다.
    * 850,895개 행 기록: 이 단계에서 기록한 총 행 수입니다.
    * R/W: 이 단계에서 읽거나 쓴 총 데이터의 양입니다(바이트).
    * 색: 단계에서 색을 사용하여 각각의 꼭짓점 상태를 나타냅니다.
      
      * 녹색은 꼭짓점이 성공한 것을 나타냅니다.
      * 주황색은 꼭짓점이 재시도된 것을 나타냅니다. 재시도된 꼭짓점이 실패했지만 시스템에서 자동으로 재시도하고 성공했으며 전체 단계가 성공적으로 완료되었습니다. 꼭짓점이 재시도되었지만 여전히 실패했으며 빨간색으로 바뀌고 전체 작업이 실패했습니다.
      * 빨간색은 실패를 나타내며, 시스템에서 특정 꼭짓점을 몇 번 시도했지만 여전히 실패한 것을 의미합니다. 이 시나리오에서는 전체 작업이 실패합니다.
      * 파란색은 특정 꼭짓점이 실행 중임을 의미합니다.
      * 흰색은 꼭짓점이 대기 중임을 나타냅니다. ADLAU를 사용할 수 있게 된 후 꼭짓점 예약을 대기하거나 입력 데이터가 준비되지 않은 경우 입력을 대기할 수 있습니다.
      
      마우스 커서를 상태 옆에 올려 놓으면 단계에 대한 세부 정보를 확인할 수 있습니다.
      
      ![Azure Data Lake Analytics 작업 그래프 단계 세부 정보](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * 꼭짓점: 예를 들어 꼭짓점에 대한 세부 정보 즉, 총 꼭짓점 수, 완료된 꼭짓점 수, 실패 여부 또는 실행 중/대기 중 여부 등에 대해 설명합니다.
  * 포드 전체/내부에서 읽은 데이터: 파일과 데이터가 분산된 파일 시스템의 여러 포드에 저장됩니다. 이 값은 동일한 포드 또는 여러 포드에서 읽은 데이터의 양을 설명합니다.
  * 총 컴퓨팅 시간: 단계의 모든 꼭짓점 실행 시간의 합계로, 단계의 모든 작업을 한 개 꼭짓점에서만 실행할 경우 소요되는 시간과 같습니다.
  * 쓴/읽은 데이터 및 행: 읽거나 쓴 데이터 또는 행의 양 또는 읽어야 할 데이터 또는 행의 양을 나타냅니다.
  * 꼭짓점 읽기 실패: 데이터를 읽는 중 실패한 꼭짓점의 수에 대해 설명합니다.
  * 중복 꼭짓점 삭제: 꼭짓점이 너무 느리게 실행될 경우 시스템에서 동일한 작업을 실행하려면 복수 꼭짓점을 예약할 수 있습니다. 꼭짓점 하나가 완전히 성공하면 중복 꼭짓점이 삭제됩니다. 중복 꼭짓점 삭제는 단계에서 중복으로 삭제된 꼭짓점의 수를 기록합니다.
  * 꼭짓점 해지: 꼭짓점이 성공했지만 어떤 이유로 나중에 재실행됩니다. 예를 들어 다운스트림 꼭짓점에서 중간 입력 데이터가 없어지면 업스트림 꼭짓점 재실행을 요청합니다.
  * 꼭짓점 예약 실행: 꼭짓점이 예약된 총 시간입니다.
  * 꼭짓점에서 읽은 최소/평균/최대 데이터: 꼭짓점에서 읽은 모든 데이터의 최소/평균/최대 값입니다.
  * 기간: 단계에 소요된 벽시계 시간으로, 이 값을 보려면 프로필을 로드해야 합니다.
  * 작업 재생
    
      Data Lake Analytics는 작업을 실행하고 꼭짓점이 시작된 시간, 실패한 시간, 재시도 횟수 등 작업에 대한 꼭짓점 실행 정보를 보존합니다. 모든 정보는 쿼리 저장소에 자동으로 기록되어 작업 프로필에 저장됩니다. 작업 보기의 "프로필 로드"를 통해 작업 프로필을 다운로드할 수 있으며 작업 프로필을 다운로드한 후 작업 재생을 볼 수 있습니다.
    
      작업 재생은 클러스터에서 실행되는 작업을 시각화한 것입니다. 작업 실행 진행률을 보고 매우 짧은 시간(일반적으로 30초 미만) 동안의 성능 이상과 병목 현상을 시각적으로 감지할 수 있습니다.
  * 작업 열 지도 표시 
    
      작업 열 지도는 작업 그래프의 표시 드롭다운을 통해 선택할 수 있습니다. 
    
      ![Azure Data Lake Analytics 작업 그래프 열 지도 표시](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      여기에는 작업의 I/O, 시간 및 처리량 열 지도가 표시되며, 이를 통해 작업 시간이 가장 긴 위치 또는 작업이 I/O 경계 작업인지 여부 등이 표시됩니다.
    
      ![Azure Data Lake Analytics 작업 그래프 열 지도 예](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * 진행률: 작업 실행 진행률로, 자세한 내용은 단계 정보를 참조하세요.
    * 읽은/쓴 데이터: 각 단계에서 읽거나 쓴 총 데이터의 열 지도입니다.
    * 컴퓨팅 시간: SUM(전체 꼭짓점의 실행 시간)의 열 지도로, 단계의 모든 작업을 1개 꼭짓점에서만 실행할 경우 소요되는 시간과 같습니다.
    * 노드당 평균 실행 시간: SUM(전체 꼭짓점 실행 시간) / (꼭짓점 수)의 열 지도. 즉, 병렬 처리로 실행되는 모든 꼭짓점을 할당할 수 있을 경우 전체 단계는 이 시간 안에 완료됩니다.
    * 입력/출력 처리량: 각 단계의 입력/출력 처리량의 열 지도로, 여기에서 작업이 I/O 바운드 작업인지 여부를 확인할 수 있습니다.
* 메타데이터 작업
  
    U-SQL 스크립트에서 데이터베이스 만들기, 테이블 삭제 등의 일부 메타데이터 작업을 수행할 수 있습니다. 이러한 작업은 컴파일 후 메타데이터 작업에 표시됩니다. 여기에서 어설션을 찾고 엔터티를 만들거나 삭제할 수 있습니다.
  
    ![Azure Data Lake Analytics 작업 보기 메타데이터 작업](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* 상태 기록
  
    상태 기록도 작업 요약에서 시각화되지만 여기에서 자세한 정보를 확인할 수 있습니다. 작업이 준비, 대기, 실행 시작, 종료된 시간 등 자세한 정보를 찾을 수 있습니다. 또한 작업이 실제로 클러스터에 디스패치되는 경우(세부 정보: 클러스터에 작업 디스패치) 작업이 컴파일된 횟수를 찾을 수 있습니다(CcsAttempts: 1).
  
    ![Azure Data Lake Analytics 작업 보기 상태 기록](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* 진단
  
    이 도구는 작업 실행을 자동으로 진단합니다. 작업에 오류 또는 성능 문제가 있으면 경고가 표시됩니다. 여기에서 전체 정보를 확인하려면 프로필을 다운로드해야 합니다. 
  
    ![Azure Data Lake Analytics 작업 보기 진단](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * 경고: 여기에 컴파일러 경고와 함께 경고가 표시됩니다. 경고가 나타나면 "x개 문제" 링크를 클릭하여 추가 정보를 확인할 수 있습니다.
  * 꼭짓점이 너무 오래 실행됨: 꼭짓점 시간이 부족할 경우(예: 5시간) 여기에서 문제를 확인할 수 있습니다.
  * 리소스 사용량: 필요한 것보다 많거나 적은 병렬 처리를 할당한 경우 여기에서 문제를 확인할 수 있습니다. 또한 리소스 사용을 클릭하여 세부 정보를 확인하거나 what-if 시나리오를 수행하여 자세한 리소스 할당을 찾을 수 있습니다(자세한 내용은 이 설명서 참조).
  * 메모리 검사: 5GB를 초과하는 메모리를 사용하는 꼭짓점이 있을 경우 여기에서 문제를 확인할 수 있습니다. 작업 실행이 시스템 한계보다 많은 메모리를 사용할 경우 시스템에 의해 중단될 수 있습니다.

## <a name="job-detail"></a>작업 세부 정보
작업 세부 정보에는 스크립트, 리소스, 꼭짓점 실행 보기를 포함한 자세한 작업 정보가 표시됩니다.

![Azure Data Lake Analytics 작업 세부 정보](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* 스크립트
  
    작업의 U-SQL 스크립트는 쿼리 저장소에 저장됩니다. 원래 U-SQL 스크립트를 보고 필요할 경우 다시 제출할 수 있습니다.
* 리소스
  
    리소스를 통해 쿼리 저장소에 저장된 작업 컴파일 출력을 찾을 수 있습니다. 예를 들어 여기에서 작업 그래프, 등록한 어셈블리 등을 표시하는 데 사용하는 "algebra.xml"을 찾을 수 있습니다.
* Vertex 실행 보기
  
    꼭짓점 실행에 대한 세부 정보가 표시됩니다. 작업 프로필은 읽은/쓴 총 데이터, 런타임, 상태 등 모든 꼭짓점 실행 로그를 보존합니다. 이 보기를 통해 작업이 실행되는 방식에 대한 세부 정보를 확인할 수 있습니다. 자세한 내용은 [Data Lake Tools for Visual Studio에서 Vertex Execution View 사용](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 진단 정보를 기록하려면 [Azure Data Lake Analytics에 대한 진단 로그에 액세스](data-lake-analytics-diagnostic-logs.md)를 참조하세요.
* 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* 꼭짓점 실행 보기를 사용하려면 [Data Lake Tools for Visual Studio에서 Vertex Execution View 사용](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)을 참조하세요.

