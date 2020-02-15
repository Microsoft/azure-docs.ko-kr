---
title: 스트리밍 수집을 사용 하 여 Azure 데이터 탐색기로 데이터 수집
description: 스트리밍 수집을 사용 하 여 데이터를 Azure 데이터 탐색기 수집 (로드) 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: cc152460be777c30d79f783b9acfa846a4c73a72
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77188015"
---
# <a name="streaming-ingestion-preview"></a>스트리밍 수집 (미리 보기)

스트리밍 수집은 가변 볼륨 데이터의 수집 시간이 10 초 미만인 낮은 대기 시간이 필요한 시나리오를 대상으로 합니다. 각 테이블에 대 한 데이터 스트림이 상대적으로 작고 (초당 몇 개의 레코드), 전체 데이터 수집 볼륨이 많은 경우 (초당 수천 개의 레코드) 하나 이상의 데이터베이스에서 많은 테이블의 작업 처리를 최적화 하는 데 사용 됩니다.

데이터 양이 테이블당 초당 1mb 이상 증가 하는 경우 스트리밍 수집 대신 클래식 (bulk) 수집을 사용 합니다. 수집의 다양 한 방법에 대 한 자세한 내용은 [데이터 수집 개요](/azure/data-explorer/ingest-data-overview) 를 참조 하세요.

> [!NOTE]
> 스트리밍 수집은 다음 기능을 지원 하지 않습니다.
> * [데이터베이스 커서](/azure/kusto/management/databasecursor).
> * [데이터 매핑](/azure/kusto/management/mappings). [미리 만든](/azure/kusto/management/create-ingestion-mapping-command) 데이터 매핑만 지원 됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [웹 UI](https://dataexplorer.azure.com/)에 로그인 합니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md) 만들기

## <a name="enable-streaming-ingestion-on-your-cluster"></a>클러스터에서 스트리밍 수집 사용

1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 **켜기** 를 선택 하 여 **스트리밍**수집을 사용 하도록 설정 합니다.
1. **저장**을 선택합니다.
 
    ![스트리밍 수집](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. [웹 UI](https://dataexplorer.azure.com/)에서 스트리밍 데이터를 수신 하는 테이블 또는 데이터베이스에 대 한 [스트리밍 수집 정책을](/azure/kusto/concepts/streamingingestionpolicy) 정의 합니다. 

    > [!NOTE]
    > * 정책이 데이터베이스 수준에서 정의 된 경우 데이터베이스의 모든 테이블에 대해 스트리밍 수집을 사용할 수 있습니다.
    > * 적용 된 정책은 새로 수집 된 데이터만 참조할 수 있으며 데이터베이스의 다른 테이블은 참조할 수 없습니다.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>스트리밍 수집을 사용 하 여 데이터를 클러스터로 수집 합니다.

지원 되는 스트리밍 수집 형식은 다음 두 가지입니다.

* 데이터 원본으로 사용 되는 [이벤트 허브](/azure/data-explorer/ingest-data-event-hub)
* 사용자 지정 수집을 사용 하려면 Azure 데이터 탐색기 클라이언트 라이브러리 중 하나를 사용 하는 응용 프로그램을 작성 해야 합니다. 샘플 응용 프로그램에 대 한 [스트리밍 수집 샘플](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) 을 참조 하세요.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>적절 한 스트리밍 수집 유형 선택

|   |이벤트 허브  |사용자 지정 수집  |
|---------|---------|---------|
|수집 시작 및 쿼리에 사용할 수 있는 데이터 간의 데이터 지연   |    지연 시간 초과     |   짧은 지연      |
|개발 오버 헤드    |   빠르고 쉬운 설치, 개발 오버 헤드 없음    |   응용 프로그램에서 오류를 처리 하 고 데이터 일관성을 유지 하기 위한 높은 개발 오버 헤드     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>클러스터에서 스트리밍 수집 사용 안 함

> [!WARNING]
> 스트리밍 수집을 사용 하지 않도록 설정 하는 데 몇 시간이 걸릴 수 있습니다.

1. 모든 관련 테이블 및 데이터베이스에서 [스트리밍 수집 정책을](/azure/kusto/concepts/streamingingestionpolicy) 삭제 합니다. 스트리밍 수집 정책 제거는 초기 저장소에서 열 저장소 (익스텐트 또는 분할)의 영구 저장소로의 스트리밍 수집 데이터 이동을 트리거합니다. 데이터 이동은 초기 저장소의 데이터 양과 클러스터의 CPU 및 메모리 사용률에 따라 몇 초에서 몇 시간까지 지속 될 수 있습니다.
1. Azure Portal에서 Azure Data Explorer 클러스터로 이동합니다. **설정**에서 **구성**을 선택 합니다. 
1. **구성** 창에서 **끄기** 를 선택 하 여 **스트리밍**수집을 사용 하지 않도록 설정 합니다.
1. **저장**을 선택합니다.

    ![스트리밍 수집 해제](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>제한 사항

* VM 및 클러스터 크기를 늘려 스트리밍 수집 성능 및 용량을 확장 합니다. 동시 ingestions는 코어 당 6 ingestions로 제한 됩니다. 예를 들어 D14 및 L16와 같은 16 개 코어 Sku의 경우 지원 되는 최대 부하는 96 동시 ingestions입니다. 코어 2와 같은 2 개 코어 Sku의 경우 지원 되는 최대 부하는 12 개의 동시 ingestions입니다.
* 수집 요청당 데이터 크기 제한은 4mb입니다.
* 테이블 생성 및 수정과 수집 매핑 등의 스키마 업데이트는 스트리밍 수집 서비스에 대해 최대 5 분이 걸릴 수 있습니다.
* 데이터가 스트리밍을 통해 수집 않는 경우에도 클러스터에서 스트리밍 수집을 사용 하도록 설정 하면에서 클러스터 컴퓨터의 로컬 SSD 디스크의 일부를 사용 하 여 수집 데이터를 스트리밍하 고 핫 캐시에 사용할 수 있는 저장소를 줄입니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기에서 데이터 쿼리](web-query-data.md)
