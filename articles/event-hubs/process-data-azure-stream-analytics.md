---
title: Stream Analytics를 사용 하 여 Event Hubs Azure에서 데이터 처리 Microsoft Docs
description: 이 문서에서는 Azure Stream Analytics 작업을 사용 하 여 Azure 이벤트 허브에서 데이터를 처리 하는 방법을 보여 줍니다.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991939"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Azure Stream Analytics를 사용 하 여 이벤트 허브에서 데이터 처리 
Azure Stream Analytics 서비스를 사용 하면 Azure Event Hubs에서 스트리밍 데이터를 쉽게 수집, 처리 및 분석할 수 있으므로 강력한 통찰력을 통해 실시간 작업을 지원할 수 있습니다. 이러한 통합을 통해 핫 경로 분석 파이프라인을 신속 하 게 만들 수 있습니다. Azure Portal를 사용 하 여 들어오는 데이터를 시각화 하 고 Stream Analytics 쿼리를 작성할 수 있습니다. 쿼리가 준비 되 면 몇 번의 클릭 만으로 프로덕션으로 이동할 수 있습니다. 

## <a name="key-benefits"></a>주요 이점
Azure Event Hubs 및 Azure Stream Analytics 통합의 주요 이점은 다음과 같습니다. 
- **데이터 미리 보기** – Azure Portal의 이벤트 허브에서 들어오는 데이터를 미리 볼 수 있습니다.
- **쿼리 테스트** – 변환 쿼리를 준비 하 고 Azure Portal에서 직접 테스트 합니다. 쿼리 언어 구문은 [Stream Analytics 쿼리 언어](/stream-analytics-query/built-in-functions-azure-stream-analytics) 설명서를 참조 하세요.
- **프로덕션에 쿼리 배포** – Azure Stream Analytics 작업을 만들고 시작 하 여 쿼리를 프로덕션에 배포할 수 있습니다.

## <a name="end-to-end-flow"></a>종단 간 흐름

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. **Event Hubs 네임 스페이스로** 이동한 다음 들어오는 데이터가 있는 **Event hub**로 이동 합니다. 
1. 이벤트 허브 페이지에서 **데이터 처리** 를 선택 합니다.  

    ![데이터 처리 타일](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. **이벤트에서 실시간 정보 사용** 타일에서 **탐색** 을 선택 합니다. 

    ![Stream Analytics 선택](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. 다음 필드에 대해 값이 이미 설정 된 쿼리 페이지가 표시 됩니다.
    1. 쿼리의 입력으로 사용할 **이벤트 허브** 입니다.
    1. SELECT 문을 사용 하는 샘플 **SQL 쿼리입니다** . 
    1. 쿼리 테스트 결과를 참조 하는 **출력** 별칭입니다. 

        ![쿼리 편집기](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  이 기능을 처음 사용 하는 경우이 페이지에는 들어오는 데이터를 미리 볼 수 있는 이벤트 허브에 대한 소비자 그룹 및 정책을 만드는 권한을 요청 하는 메시지가 표시 됩니다.
1. 위의 그림에 표시 된 것 처럼 **입력 미리 보기** 창에서 **만들기** 를 선택 합니다. 
1. 이 탭에서 들어오는 최신 데이터의 스냅숏이 즉시 표시 됩니다.
    - 데이터의 serialization 형식이 자동으로 검색 됩니다 (JSON/CSV). JSON/CSV/AVRO에도 수동으로 변경할 수 있습니다.
    - 들어오는 데이터를 테이블 형식 또는 원시 형식으로 미리 볼 수 있습니다. 
    - 표시 된 데이터가 최신이 아닌 경우 **새로 고침** 을 선택 하 여 최신 이벤트를 확인 합니다. 

        **테이블 형식의**데이터 예는 다음과 같습니다.   ![테이블 형식의 결과](./media/process-data-azure-stream-analytics/snapshot-results.png)

        **원시 형식의**데이터 예제는 다음과 같습니다. 

        ![원시 형식의 결과](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. 쿼리 **테스트** 를 선택 하 여 **테스트 결과** 탭에서 쿼리 테스트 결과의 스냅숏을 확인 합니다. 또한 결과를 다운로드할 수 있습니다.

    ![쿼리 결과 테스트](./media/process-data-azure-stream-analytics/test-results.png)
1. 사용자 고유의 쿼리를 작성 하 여 데이터를 변환 합니다. [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference)를 참조 하세요.
1. 쿼리를 테스트 하 고 프로덕션으로 이동 하려면 **쿼리 배포**를 선택 합니다. 쿼리를 배포 하려면 작업에 대한 출력을 설정 하 고 작업을 시작할 수 있는 Azure Stream Analytics 작업을 만듭니다. Stream Analytics 작업을 만들려면 작업의 이름을 지정 하 고 **만들기**를 선택 합니다.

      ![Azure Stream Analytics 작업 만들기](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Event Hubs 페이지에서 만드는 새 Azure Stream Analytics 작업 마다 소비자 그룹과 정책을 만드는 것이 좋습니다. 소비자 그룹은 5 명의 동시 판독기만 허용 하므로 각 작업에 대한 전용 소비자 그룹을 제공 하면 해당 제한을 초과 하 여 발생할 수 있는 오류를 방지할 수 있습니다. 전용 정책을 사용 하면 다른 리소스에 영향을 주지 않고 키를 회전 하거나 사용 권한을 해지할 수 있습니다. 
1. 이제 쿼리가 테스트 한 것과 동일한 Stream Analytics 작업이 만들어지고, 입력은 이벤트 허브입니다. 

9.  파이프라인을 완료 하려면 쿼리의 **출력** 을 설정 하 고 **시작** 을 선택 하 여 작업을 시작 합니다.

    > [!NOTE]
    > 작업을 시작 하기 전에 outputalias를 Azure Stream Analytics에서 만든 출력 이름으로 바꾸어야 합니다.

      ![출력 설정 및 작업 시작](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>알려진 제한 사항
쿼리를 테스트 하는 동안 테스트 결과를 로드 하는 데 6 초 정도 걸립니다. 테스트 성능을 향상 시키기 위해 노력 하 고 있습니다. 그러나 프로덕션 환경에 배포 된 경우 Azure Stream Analytics는 초이 대기 시간이 있습니다.

## <a name="streaming-units"></a>스트리밍 단위
Azure Stream Analytics 작업은 기본적으로 3 개의 스트리밍 단위 (SUs)로 설정 됩니다. 이 설정을 조정 하려면 Azure Portal에서 **Stream Analytics 작업** 페이지의 왼쪽 메뉴에서 **크기 조정** 을 선택 합니다. 스트리밍 단위에 대해 자세히 알아보려면 [스트리밍 단위 이해 및 조정](../stream-analytics/stream-analytics-streaming-unit-consumption.md)을 참조 하세요.

![스트리밍 단위 크기 조정](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>다음 단계
Stream Analytics 쿼리에 대한 자세한 내용은 [쿼리 언어 Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics) 를 참조 하세요.
