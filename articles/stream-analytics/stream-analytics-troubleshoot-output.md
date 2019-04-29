---
title: Azure Stream Analytics 출력 문제 해결
description: 이 문서에서는 Azure Stream Analytics 작업에서 출력 연결의 문제를 해결하는 기술에 대해 설명합니다.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 92cb427149e6e6cbddfb96c6e4488017641e6482
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761746"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Azure Stream Analytics 출력 문제 해결

이 페이지는 출력 연결 관련 일반 문제 및 해당 문제를 해결하는 방법을 설명합니다.

## <a name="output-not-produced-by-job"></a>작업에 의해 생성되지 않은 출력 
1.  각 출력에 대해 **테스트 연결** 단추를 사용하여 출력에 대한 연결을 확인합니다.

2.  **모니터** 탭에서 [**모니터링 메트릭**](stream-analytics-monitoring.md)을 확인합니다. 값을 집계하기 때문에 메트릭이 몇 분 동안 지연됩니다.
    - 입력 이벤트가 0보다 큰 경우, 작업은 입력 데이터를 읽을 수 있습니다. 입력 이벤트가 0보다 크지 않으면 다음을 수행합니다.
      - 데이터 원본에 유효한 데이터가 있는지 확인하려면 [Service Bus 탐색기](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)를 사용하여 확인합니다. 이 확인은 작업이 입력으로 이벤트 허브를 사용하는 경우 적용됩니다.
      - 데이터 serialization 형식과 데이터 인코딩이 예상대로인지 확인합니다.
      - 작업이 이벤트 허브를 사용하는 경우 메시지의 본문이 *Null*인지 확인합니다.
      
    - 데이터 변환 오류가 0보다 크며 증가하는 경우 다음을 의미할 수 있습니다.
      - 출력 이벤트가 대상 싱크의 스키마를 따르지 않습니다. 
      - 이벤트 스키마가 쿼리에서 이벤트의 정의 또는 예상 스키마와 일치하지 않을 수도 있습니다.
      - 이벤트에서 일부 필드의 데이터 형식이 예상과 일치하지 않을 수도 있습니다.
      
    - 런타임 오류가 0보다 큰 경우 작업이 데이터를 수신할 수 있지만 쿼리를 처리하는 동안 오류가 발생함을 의미합니다.
      - 오류를 찾으려면 [감사 로그](../azure-resource-manager/resource-group-audit.md)로 이동하여 *실패* 상태를 필터링합니다.
      
    - InputEvents가 0보다 크고 OutputEvents가 0인 경우 다음 중 하나를 의미합니다.
      - 쿼리 처리로 제로 출력 이벤트가 발생했습니다.
      - 이벤트 또는 그 필드가 잘못되어 쿼리 처리 후 제로 출력이 발생할 수 있습니다.
      - 작업이 연결 또는 인증 이유로 데이터를 출력 싱크에 푸시할 수 없습니다.
      
    - 이전에 언급한 모든 오류 사례에서 작업 로그 메시지는 쿼리 논리가 모든 이벤트를 필터링하는 경우 외에 추가 정보(발생하는 상황 포함)를 설명합니다. 여러 이벤트 처리에서 오류가 발생하면 Stream Analytics는 작업 로그에 10분 이내 동일한 형식의 첫 세 개의 오류 메시지를 기록합니다. 그런 다음, “오류가 너무 빠르게 발생하고 억제되고 있습니다.”라는 메시지로 동일한 추가 오류를 표시하지 않습니다.
    
## <a name="job-output-is-delayed"></a>작업 출력이 지연됨

### <a name="first-output-is-delayed"></a>첫 번째 출력이 지연됨
Stream Analytics 작업이 시작되면 입력 이벤트가 읽히지만 특정 상황에서 출력 생성이 지연될 수 있습니다.

temporal 쿼리 요소의 시간 값이 크면 출력이 지연의 원인이 될 수 있습니다. 큰 시간 범위에 대해 정확한 출력을 생성하기 위해, 스트리밍 작업은 가능한 한 최신 시간(최대 7일 전)의 데이터를 읽어서 시간 범위를 채웁니다. 이 시간 동안 해결되지 않은 입력 이벤트의 보완 읽기가 완료될 때까지 출력이 생성되지 않습니다. 시스템이 스트리밍 작업을 업그레이드하여 작업을 다시 시작하면 이 문제가 드러날 수 있습니다. 이러한 업그레이드는 일반적으로 몇 달에 한 번 발생합니다. 

따라서 Stream Analytics 쿼리를 디자인할 때 주의해야 합니다. 작업의 쿼리 구문에 있는 temporal 요소에 큰 시간 범위(몇 시간 이상, 최대 7일)를 사용하면 작업이 시작되거나 재시작될 때 첫 번째 출력이 지연될 수 있습니다.  

이런 종류의 첫 번째 출력 지연 문제를 완화시키는 방법은 쿼리 병렬화 기법(데이터 파티션)을 사용하거나 작업이 정상화될 때까지 처리량을 향상시키도록 스트리밍 단위를 추가하는 것입니다.  자세한 내용은 [Stream Analytics 작업을 만들 때 고려 사항](stream-analytics-concepts-checkpoint-replay.md)을 참조하세요.

이러한 요소는 생성되는 첫 번째 출력의 적시성에 영향을 줍니다.

1. 기간 이동 집계 사용(연속, 도약 및 슬라이딩 시간 범위의 GROUP BY)
   - 연속 또는 도약 시간 범위 집계에 대한 결과는 시간 범위의 끝에 생성됩니다. 
   - 슬라이딩 시간 범위는 이벤트가 슬라이딩 시간 범위에 들어가거나 끝날 때 결과가 생성됩니다. 
   - 큰 시간 범위(1시간 초과)를 사용하려는 경우 출력을 더 자주 볼 수 있도록 도약 또는 슬라이딩 시간 범위를 선택하는 것이 가장 좋습니다.

2. temporal 조인 사용(DATEDIFF를 사용한 JOIN)
   - 일치 항목은 일치하는 이벤트의 양측이 도착하자마자 생성됩니다.
   - 일치 항목이 없는 데이터(왼쪽 우선 외부 조인)는 왼쪽의 각 이벤트와 관련하여 DATEDIFF 시간 범위의 끝에 생성됩니다.

3. temporal 분석 함수 사용(LIMIT DURATION이 있는 ISFIRST, LAST 및 LAG)
   - 분석 함수의 경우 이벤트마다 출력이 생성되며 지연은 없습니다.

### <a name="output-falls-behind"></a>출력 지연
작업이 정상적으로 작동하는 동안 작업 출력이 지연되는 경우(대기 시간이 점점 길어지면) 다음 요인을 검토하여 근본 원인을 찾아낼 수 있습니다.
- 다운스트림 싱크의 제한 여부
- 업스트림 소스의 제한 여부
- 쿼리의 처리 논리에 계산이 많이 사용되는지 여부

이러한 세부 정보를 보려면 Azure Portal에서 스트리밍 작업을 선택하고 **작업 다이어그램**을 선택합니다. 각 입력에 대해 파티션당 백로그 이벤트 메트릭이 있습니다. 백로그 이벤트 메트릭이 계속 증가하면 시스템 리소스에 제한이 있다는 표시입니다. 잠재적인 이유는 출력 싱크 제한 또는 높은 CPU입니다. 작업 다이어그램 사용에 대한 자세한 내용은 [작업 다이어그램을 사용하여 데이터 기반 디버그](stream-analytics-job-diagram-with-metrics.md)를 참조하세요.

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Azure SQL Database 출력을 사용하여 키 위반 경고

Azure SQL Database를 Stream Analytics 작업에 대한 출력으로 구성하면 대상 테이블에 레코드를 대량으로 삽입합니다. 일반적으로 Azure Stream Analytics에서는 출력 싱크에 [적어도 한 번 배달]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics)을 보장합니다. SQL 테이블에 고유한 제약 조건이 정의된 경우에도 사용자는 [정확히 한 번 배달을 설정]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/)할 수 있습니다. 

SQL 테이블에 UNIQUE KEY 제약 조건이 설정되고 SQL 테이블에 중복 레코드가 삽입되면 Azure Stream Analytics는 중복된 레코드를 제거합니다. 데이터를 일괄 처리로 분할하고 단일 중복 레코드가 발견될 때까지 일괄 처리를 반복해서 삽입합니다. 스트리밍 작업에 중복된 행의 수가 너무 많으면 분할 및 삽입 프로세스가 중복 항목을 하나씩 무시해야 해서 효율성이 떨어지고 시간이 오래 걸립니다. 최근 한 시간 내에 활동 로그에서 여러 키 위반 경고 메시지가 표시된 경우 SQL 출력이 전체 작업 성능을 저하시킬 가능성이 있습니다. 

이 문제를 해결하려면 IGNORE_DUP_KEY 옵션을 사용하여 키 위반이 발생하는 [인덱스를 구성]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)해야 합니다. 이 옵션을 사용하면 중복 값이 대량 삽입하는 동안 SQL에서 무시될 수 있고 SQL Azure에서 오류 대신 경고 메시지를 생성하게 됩니다. Azure Stream Analytics에서는 기본 키 위반 오류를 더 이상 생성하지 않습니다.

몇 가지 유형의 인덱스에서 IGNORE_DUP_KEY를 구성할 때 다음 조사 내용을 적어둡니다.

* 기본 키에서 IGNORE_DUP_KEY 또는 ALTER INDEX를 사용하는 고유한 제약 조건을 설정할 수 없습니다. 인덱스를 삭제하고 다시 만들어야 합니다.  
* 고유 인덱스에 ALTER INDEX를 사용하여 IGNORE_DUP_KEY 옵션을 설정할 수 있습니다. PRIMARY KEY/UNIQUE 제약 조건과 다르게 CREATE INDEX 또는 INDEX 정의를 사용하여 만듭니다.  
* 이러한 인덱스에 고유성을 적용할 수 없기 때문에 IGNORE_DUP_KEY는 열 저장소 인덱스에 적용되지 않습니다.  

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
