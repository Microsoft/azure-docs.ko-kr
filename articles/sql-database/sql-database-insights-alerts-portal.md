---
title: Azure Portal을 사용하여 경고 및 알림 설정 | Microsoft Docs
description: Azure Portal을 사용하여 사용자가 지정한 조건에 부합하면 알림이나 자동 작업을 트리거할 수 있는 SQL Database 경고를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 11/02/2018
ms.openlocfilehash: 93337e39a117c1f8d38f24dc416ff8ae95513a34
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036056"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Database 및 Data Warehouse에 대한 경고 만들기

## <a name="overview"></a>개요
이 문서에서는 Azure Portal을 사용하여 Azure SQL Database 및 Data Warehouse 경고를 설정하는 방법을 보여 줍니다. 메트릭(예: 데이터베이스 크기 또는 CPU 사용량)이 임계값에 도달하면 경고에서 이메일을 보내거나 webhook를 호출할 수 있습니다. 이 문서는 또한 경고 기간 설정에 대한 모범 사례를 제공합니다.    

> [!IMPORTANT]
> Managed Instance에서는 아직 이 기능을 사용할 수 없습니다. 대안으로 SQL Agent를 사용하여 [동적 관리 뷰](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)를 기반으로 메트릭에 대한 이메일 경고를 보낼 수 있습니다.

Azure 서비스 또는 Azure 서비스의 이벤트에 대한 모니터링 메트릭을 기반으로 경고를 받을 수 있습니다.

* **메트릭 값** - 이 경고는 특정 메트릭의 값이 어느 방향으로든 사용자가 할당한 임계값을 초과했을 때 트리거됩니다. 즉 조건에 처음 부합했을 때와, 조건에 더 이상 부합하지 않게 되었을 때 모두 트리거됩니다.    
* **활동 로그 이벤트** - *모든* 이벤트에 대해 또는 특정 이벤트 수가 발생했을 때만 경고를 트리거할 수 있습니다

트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 서비스 관리자 및 공동 관리자에게 이메일 알림을 보냅니다.
* 사용자가 지정한 추가 이메일 주소로 이메일을 보냅니다.
* webhook 호출

다음을 통해 경고에 대한 정보를 구성하고 가져올 수 있습니다.

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [명령줄 인터페이스(CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Azure 포털에서 메트릭에 대한 경고 규칙 만들기
1. [포털](https://portal.azure.com/)에서 모니터링하려는 리소스를 찾아 선택합니다.
2. 모니터링 섹션에서 **경고(클래식)** 를 선택합니다. 텍스트와 아이콘은 리소스마다 약간씩 다를 수 있습니다.  
   
     ![모니터링](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **SQL DW 전용**: **DWU 사용** 그래프를 클릭합니다. **클래식 경고 보기** 선택

3. **메트릭 경고 추가(클래식)** 단추를 선택하고 필드에 입력합니다.
   
    ![Add alert](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. 경고 규칙의 **이름**을 지정하고 **설명**을 선택합니다. 알림 이메일에도 표시되는 항목입니다.
5. 모니터링할 **메트릭**을 선택하고 해당 메트릭에 대한 **조건** 및 **임계값**을 선택합니다. 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간**도 선택합니다. 예를 들어, "PT5M" 기간을 사용하고 경고가 80% 이상인 CPU를 찾는 경우 **평균** CPU가 5분 동안 80%를 넘으면 이 경고가 트리거됩니다. 첫 번째 트리거가 발생한 후 평균 CPU가 5분 동안 80% 미만을 유지하면 다시 트리거됩니다. CPU 측정은 1 분마다 발생합니다. 지원되는 시간 창 그리고 모든 경고가 아닌 각각의 경고가 평균 값을 사용하는 집계 유형에 대해서는 아래의 테이블을 확인합니다.   
6. 경고가 발생했을 때 관리자 및 공동 관리자에게 이메일을 보내려면 **소유자에게 이메일 보내기...** 를 선택합니다.
7. 경고가 발생했을 때 다른 이메일 주소에서 알림을 받으려면 해당 이메일을 **추가 관리자 이메일** 필드에 추가합니다. 여러 전자 메일-세미콜론으로 구분 *전자 메일\@contoso.com;email2\@contoso.com*
8. 경고가 발생했을 때 호출하려면 **Webhook** 필드에 유효한 URI를 입력합니다.
9. 경고 만들기가 완료되면 **확인**을 선택합니다.   

앞서 설명한 대로 몇 분 안에 경고가 활성화 및 트리거됩니다.

## <a name="managing-your-alerts"></a>경고 관리
경고를 만든 후 해당 경고를 선택하여 다음을 수행할 수 있습니다.

* 전날의 메트릭 임계값 및 실제 값을 표시하는 그래프 확인
* 편집 또는 삭제
* 해당 경고에 대한 알림 수신을 일시 중지 또는 재개하려면 **사용 중지** 또는 **사용**하도록 설정합니다.


## <a name="sql-database-alert-values"></a>SQL Database 경고 값

| 리소스 종류 | 메트릭 이름 | 친숙한 이름 | 집계 형식 | 최소 경고 시간 창|
| --- | --- | --- | --- | --- |
| SQL 데이터베이스 | cpu_percent | CPU 비율 | 평균 | 5분 |
| SQL 데이터베이스 | physical_data_read_percent | 데이터 IO 비율 | 평균 | 5분 |
| SQL 데이터베이스 | log_write_percent | 로그 IO 비율 | 평균 | 5분 |
| SQL 데이터베이스 | dtu_consumption_percent | DTU 비율 | 평균 | 5분 |
| SQL 데이터베이스 | 저장소 | 총 데이터베이스 크기 | 최대 | 30분 |
| SQL 데이터베이스 | connection_successful | 성공적인 연결 | 합계 | 10분 |
| SQL 데이터베이스 | connection_failed | 실패한 연결 | 합계 | 10분 |
| SQL 데이터베이스 | blocked_by_firewall | 방화벽에 의해 차단 | 합계 | 10분 |
| SQL 데이터베이스 | 교착 상태 | 교착 상태 | 합계 | 10분 |
| SQL 데이터베이스 | storage_percent | 데이터베이스 크기 비율 | 최대 | 30분 |
| SQL 데이터베이스 | xtp_storage_percent | 메모리 내 OLTP 저장소 백분율(미리 보기) | 평균 | 5분 |
| SQL 데이터베이스 | workers_percent | 작업자 백분율 | 평균 | 5분 |
| SQL 데이터베이스 | sessions_percent | 세션 백분율 | 평균 | 5분 |
| SQL 데이터베이스 | dtu_limit | DTU 제한 | 평균 | 5분 |
| SQL 데이터베이스 | dtu_used | DTU 사용됨 | 평균 | 5분 |
||||||
| 탄력적 풀 | cpu_percent | CPU 비율 | 평균 | 10분 |
| 탄력적 풀 | physical_data_read_percent | 데이터 IO 비율 | 평균 | 10분 |
| 탄력적 풀 | log_write_percent | 로그 IO 비율 | 평균 | 10분 |
| 탄력적 풀 | dtu_consumption_percent | DTU 비율 | 평균 | 10분 |
| 탄력적 풀 | storage_percent | 저장소 비율 | 평균 | 10분 |
| 탄력적 풀 | workers_percent | 작업자 백분율 | 평균 | 10분 |
| 탄력적 풀 | eDTU_limit | eDTU 제한 | 평균 | 10분 |
| 탄력적 풀 | storage_limit | 저장소 제한 | 평균 | 10분 |
| 탄력적 풀 | eDTU_used | eDTU 사용 | 평균 | 10분 |
| 탄력적 풀 | storage_used | 저장소 사용됨 | 평균 | 10분 |
||||||               
| SQL 데이터 웨어하우스 | cpu_percent | CPU 비율 | 평균 | 10분 |
| SQL 데이터 웨어하우스 | physical_data_read_percent | 데이터 IO 비율 | 평균 | 10분 |
| SQL 데이터 웨어하우스 | connection_successful | 성공적인 연결 | 합계 | 10분 |
| SQL 데이터 웨어하우스 | connection_failed | 실패한 연결 | 합계 | 10분 |
| SQL 데이터 웨어하우스 | blocked_by_firewall | 방화벽에 의해 차단 | 합계 | 10분 |
| SQL 데이터 웨어하우스 | service_level_objective | 데이터베이스의 서비스 계층 | 합계 | 10분 |
| SQL 데이터 웨어하우스 | dwu_limit | dwu 제한 | 최대 | 10분 |
| SQL 데이터 웨어하우스 | dwu_consumption_percent | DWU 백분율 | 평균 | 10분 |
| SQL 데이터 웨어하우스 | dwu_used | DWU 사용됨 | 평균 | 10분 |
||||||


## <a name="next-steps"></a>다음 단계
* [Azure 모니터링 개요](../monitoring-and-diagnostics/monitoring-overview.md) 를 확인합니다.
* [경고에서의 webhook 구성](../azure-monitor/platform/alerts-webhooks.md)에 대해 자세히 알아봅니다.
* 서비스의 상세 고빈도 메트릭을 수집하기 위한 [진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md) 의 개요를 살펴봅니다.
* 서비스를 사용 가능하며 응답할 수 있는 상태로 유지하기 위한 [메트릭 수집](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 의 개요를 살펴봅니다.
