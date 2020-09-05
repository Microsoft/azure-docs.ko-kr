---
title: Azure Stream Analytics 통합 (미리 보기)을 사용 하 여 데이터 스트리밍
description: Azure Stream Analytics 통합을 사용 하 여 데이터를 Azure SQL Database 스트림 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 945e9019d75f9597d89a63c9322cbd4a8d502f15
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869867"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Azure Stream Analytics 통합 (미리 보기)을 사용 하 여 Azure SQL Database로 데이터 스트리밍

이제 사용자는 Azure SQL Database의 데이터베이스에서 직접 실시간 스트리밍 데이터를 테이블로 수집, 처리, 확인 및 분석할 수 있습니다. Azure Portal에서 [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md)를 사용 하 여이 작업을 수행 합니다. 이러한 환경을 통해 연결 된 자동차, 원격 모니터링, 사기 감지 등과 같은 다양 한 시나리오를 사용할 수 있습니다. Azure Portal에서 이벤트 소스 (이벤트 허브/IoT Hub)를 선택 하 고, 들어오는 실시간 이벤트를 보고, 이벤트를 저장할 테이블을 선택할 수 있습니다. 포털에서 Azure Stream Analytics 쿼리 언어 쿼리를 작성 하 여 들어오는 이벤트를 변환 하 고 선택한 테이블에 저장할 수도 있습니다. 이 새로운 진입점은 Stream Analytics에 이미 존재 하는 생성 및 구성 환경에 추가 됩니다. 이 환경은 데이터베이스의 컨텍스트에서 시작 되므로 Stream Analytics 작업을 신속 하 게 설정 하 고 Azure SQL Database 및 Stream Analytics 환경에서 데이터베이스 간에 원활 하 게 이동할 수 있습니다.

![Stream Analytics 흐름](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>주요 이점

- 최소 컨텍스트 전환: 포털에서 Azure SQL Database의 데이터베이스에서 시작 하 고 다른 서비스로 전환 하지 않고 실시간 데이터를 테이블로 수집 시작할 수 있습니다.
- 단계 수 감소: 데이터베이스 및 테이블의 컨텍스트는 Stream Analytics 작업을 미리 구성 하는 데 사용 됩니다.
- 미리 보기 데이터에 대 한 추가 사용 편의성: 선택한 테이블의 컨텍스트에서 이벤트 원본 (이벤트 허브/IoT Hub)에서 들어오는 데이터 미리 보기

> [!IMPORTANT]
> Azure Stream Analytics 작업은 Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse Analytics (이전의 Azure SQL Data Warehouse)로 출력 될 수 있습니다. 자세한 내용은 [출력](../../stream-analytics/sql-database-output.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).
- Azure SQL Database의 데이터베이스입니다. 자세한 내용은 [Azure SQL Database에서 단일 데이터베이스 만들기](single-database-create-quickstart.md)를 참조 하세요.
- 컴퓨터가 서버에 연결할 수 있도록 하는 방화벽 규칙입니다. 자세한 내용은 [서버 수준 방화벽 규칙 만들기](firewall-create-server-level-portal-quickstart.md)를 참조 하세요.

## <a name="configure-stream-analytics-integration"></a>Stream analytics 통합 구성

1. Azure Portal에 로그인합니다.
2. 스트리밍 데이터를 수집 하려는 데이터베이스로 이동 합니다. **Stream analytics (미리 보기)** 를 선택 합니다.

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. 스트리밍 데이터를이 데이터베이스로 수집 시작 하려면 **만들기** 를 선택 하 고 스트리밍 작업에 이름을 지정한 후 **다음: 입력**을 선택 합니다.

    ![Stream Analytics 작업 기본 사항 구성](./media/stream-data-stream-analytics-integration/create-job.png)

4. 이벤트 원본 정보를 입력 하 고 **다음: 출력**을 선택 합니다.

   - **입력 유형**: 이벤트 허브/IoT Hub
   - **입력 별칭**: 이벤트 원본을 식별 하는 이름을 입력 합니다.
   - **구독**: Azure SQL Database 구독과 동일
   - **이벤트 허브 네임 스페이스**: 네임 스페이스 이름
   - **이벤트 허브 이름**: 선택한 네임 스페이스 내의 이벤트 허브 이름
   - **이벤트 허브 정책 이름** (기본값을 새로 만들려면 기본값): 정책 이름을 지정 합니다.
   - **이벤트 허브 소비자 그룹** (새로 만들기 위한 기본값): 소비자 그룹 이름을 지정 합니다.  

      여기에서 만든 새 Azure Stream Analytics 작업 마다 소비자 그룹과 정책을 만드는 것이 좋습니다. 소비자 그룹은 5 명의 동시 판독기만 허용 하므로 각 작업에 대 한 전용 소비자 그룹을 제공 하면 해당 제한을 초과 하 여 발생할 수 있는 오류를 방지할 수 있습니다. 전용 정책을 사용 하면 다른 리소스에 영향을 주지 않고 키를 회전 하거나 사용 권한을 해지할 수 있습니다.

     ![Stream Analytics 작업 출력 구성](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. 스트리밍 데이터를 수집 하려는 테이블을 선택 합니다. 완료 되 면 **만들기**를 선택 합니다.

   - **사용자 이름**, **암호**: SQL server 인증을 위한 자격 증명을 입력 합니다. **유효성 검사**를 선택합니다.
   - **테이블**: **새로 만들기** 를 선택 하거나 기존 테이블을 **사용**합니다. 이 흐름에서 **만들기**를 선택 합니다. 그러면 stream Analytics 작업을 시작할 때 새 테이블이 생성 됩니다.

     ![Stream Analytics 작업 만들기](./media/stream-data-stream-analytics-integration/create.png)

6. 다음과 같은 세부 정보가 포함 된 쿼리 페이지가 열립니다.

   - 데이터를 수집 하는 **입력** 이벤트 원본입니다.  
   - 변환 된 데이터를 저장할 **출력** (출력 테이블)
   - SELECT 문을 사용 하는 샘플 [대해 saql 쿼리입니다](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) .
   - **입력 미리 보기**: 입력 이벤트 원본에서 들어오는 최신 데이터의 스냅숏을 표시 합니다.
     - 데이터의 serialization 형식이 자동으로 검색 됩니다 (JSON/CSV). JSON/CSV/AVRO에도 수동으로 변경할 수 있습니다.
     - 들어오는 데이터를 테이블 형식 또는 원시 형식으로 미리 볼 수 있습니다.
     - 표시 된 데이터가 최신이 아닌 경우 **새로 고침** 을 선택 하 여 최신 이벤트를 확인 합니다.
     - 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트 하려면 **시간 범위 선택** 을 선택 합니다.
     - 샘플 JSON/CSV 파일을 업로드 하 여 쿼리를 테스트 하려면 **샘플 입력 업로드** 를 선택 합니다. 대해 SAQL 쿼리를 테스트 하는 방법에 대 한 자세한 내용은 [예제 데이터를 사용 하 여 Azure Stream Analytics 작업 테스트](../../stream-analytics/stream-analytics-test-query.md)를 참조 하십시오.

     ![쿼리 테스트](./media/stream-data-stream-analytics-integration/test-query.png)

   - **테스트 결과**: **쿼리 테스트** 를 선택 하면 스트리밍 쿼리 결과를 볼 수 있습니다.

     ![테스트 결과](./media/stream-data-stream-analytics-integration/test-results.png)

   - **테스트 결과 스키마**: 테스트 후 스트리밍 쿼리 결과의 스키마를 표시 합니다. 테스트 결과 스키마가 출력 스키마와 일치 하는지 확인 합니다.

     ![테스트 결과 스키마](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **출력 스키마**: 5 단계에서 선택한 테이블의 스키마 (신규 또는 기존)를 포함 합니다.

      - 새로 만들기: 5 단계에서이 옵션을 선택한 경우 스트리밍 작업을 시작할 때까지 아직 스키마가 표시 되지 않습니다. 새 테이블을 만들 때 적절 한 테이블 인덱스를 선택 합니다. 테이블 인덱싱에 대 한 자세한 내용은 [설명 된 클러스터형 및 비클러스터형 인덱스](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)를 참조 하십시오.
      - 기존 항목 사용: 5 단계에서이 옵션을 선택한 경우 선택한 테이블의 스키마가 표시 됩니다.

7. 쿼리를 테스트 하 & 작성을 완료 한 후 **쿼리 저장**을 선택 합니다. **시작 Stream Analytics 작업** 을 선택 하 여 변환 된 데이터를 SQL 테이블로 수집 시작 합니다. 다음 필드를 마무리 한 후 작업을 **시작** 합니다.
   - **출력 시작 시간**: 작업의 첫 번째 출력 시간을 정의 합니다.  
     - Now: 작업을 지금 시작 하 고 들어오는 새 데이터를 처리 합니다.
     - 사용자 지정: 작업은 지금 시작 되지만 특정 시점의 데이터를 처리 합니다 (이전 또는 이후 일 수 있음). 자세한 내용은 [Azure Stream Analytics 작업을 시작 하는 방법](../../stream-analytics/start-job.md)을 참조 하세요.
   - **스트리밍 단위**: Azure Stream Analytics는 서비스에 데이터를 처리 하는 데 필요한 스트리밍 단위 수를 기준으로 가격이 책정 됩니다. 자세한 내용은 [Azure Stream Analytics 가격 책정](https://azure.microsoft.com/pricing/details/stream-analytics/)을 참조 하세요.
   - **출력 데이터 오류 처리**:  
     - 다시 시도: 오류가 발생 하면 쓰기가 성공할 때까지 이벤트를 무기한으로 기록 하는 Azure Stream Analytics 다시 시도 합니다. 재시도에 대 한 제한 시간은 없습니다. 결국, 재시도 중인 이벤트에 의해 모든 후속 이벤트의 처리가 차단됩니다. 이 옵션은 기본 출력 오류 처리 정책입니다.
     - Drop: Azure Stream Analytics는 데이터 변환 오류를 발생 시키는 출력 이벤트를 삭제 합니다. 삭제 된 이벤트는 나중에 다시 처리 하기 위해 복구할 수 없습니다. 모든 일시적인 오류(예: 네트워크 오류)는 출력 오류 처리 정책 구성과 상관없이 다시 시도됩니다.
   - **SQL Database 출력 설정**: 테이블에 여러 작성기를 사용 하는 완전히 병렬 토폴로지를 사용할 수 있도록 이전 쿼리 단계의 파티션 구성표를 상속 하는 옵션입니다. 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](../../stream-analytics/stream-analytics-sql-output-perf.md)을 참조하세요.
   - **최대 일괄 처리 수**: 모든 bulk insert 트랜잭션과 함께 전송 되는 레코드 수에 대 한 권장 상한입니다.  
    출력 오류 처리에 대 한 자세한 내용은 [Azure Stream Analytics의 출력 오류 정책](../../stream-analytics/stream-analytics-output-error-policy.md)을 참조 하십시오.  

     ![작업 시작](./media/stream-data-stream-analytics-integration/start-job.png)

8. 작업을 시작 하면 실행 중인 작업이 목록에 표시 되 고 다음 작업을 수행할 수 있습니다.
   - **작업 시작/중지**: 작업이 실행 중인 경우 작업을 중지할 수 있습니다. 작업이 중지 된 경우 작업을 시작할 수 있습니다.
   - **작업 편집**: 쿼리를 편집할 수 있습니다. 예를 들어 작업에 대 한 추가 변경 작업을 수행 하려는 경우 더 많은 입/출력을 추가한 다음 Stream Analytics에서 작업을 엽니다. 작업을 실행 하는 동안에는 편집 옵션을 사용할 수 없습니다.
   - **출력 테이블 미리 보기**: SQL 쿼리 편집기에서 테이블을 미리 볼 수 있습니다.
   - **Stream Analytics에서 열기**: Stream Analytics에서 작업을 열어 모니터링, 작업의 세부 정보를 확인 합니다.

     ![stream analytics 작업](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>다음 단계

- [Azure Stream Analytics 설명서](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics 솔루션 패턴](../../stream-analytics/stream-analytics-solution-patterns.md)
 