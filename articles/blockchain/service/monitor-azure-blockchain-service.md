---
title: Azure Blockchain 서비스 모니터링 (ABS)
description: Azure Monitor를 통해 Azure Blockchain 서비스 모니터링
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: v-umha
ms.openlocfilehash: 6f2a91a8ffce67d3c4008a7587f2787f6446c341
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293252"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Azure Monitor를 통해 Azure Blockchain 서비스 모니터링  

고객이 ABS (Azure Blockchain Service)에서 프로덕션 등급 blockchain 시나리오를 실행할 때 가용성, 성능 및 작업에 대 한 리소스를 모니터링 하는 것은 중요 합니다. 이 문서에서는 Azure Blockchain 서비스에서 생성 하는 모니터링 데이터와 Azure Monitor의 다양 한 기능 및 통합을 사용 하 여 프로덕션 등급 환경을 관리 하는 방법을 설명 합니다.  

## <a name="what-is-azure-monitor"></a>Azure Monitor란?

Azure Blockchain 서비스는 azure 리소스를 모니터링 하는 전체 기능 집합을 제공 하는 Azure의 전체 stack 모니터링 서비스인 Azure Monitor를 사용 하 여 모니터링 데이터를 만듭니다. Azure Monitor에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 Azure 리소스 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)을 참조 하세요.
 

다음 섹션에서는 azure Blockchain 서비스에서 수집 된 특정 데이터를 설명 하 고 Azure tools를 사용 하 여 데이터 수집을 구성 하 고이 데이터를 분석 하기 위한 예제를 제공 하 여이 문서를 작성 합니다.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Azure Blockchain 서비스에서 수집 된 데이터 모니터링  

Azure Blockchain 서비스는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집 하며, Azure 리소스의 [데이터 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) 에 설명 되어 있습니다. Azure Blockchain 서비스에서 만든 로그 및 메트릭에 대 한 자세한 내용은 [Azure Blockchain 서비스 데이터 참조 모니터링](#monitor-azure-blockchain-service-data-reference) 을 참조 하세요.

각 Azure Blockchain 서비스 멤버 리소스에 대 한 Azure Portal의 개요 페이지에는 처리 된 요청 및 처리 된 블록을 포함 하 여 트랜잭션의 간략 한 보기가 포함 되어 있습니다. Azure Blockchain 서비스 구성원 리소스를 만든 후에는이 데이터 중 일부가 자동으로 수집 되 고 분석에 사용할 수 있으며 추가 구성으로 추가 데이터 수집을 사용 하도록 설정할 수 있습니다.

## <a name="diagnostic-settings"></a>진단 설정  

플랫폼 메트릭과 활동 로그는 자동으로 수집 되지만 리소스 로그를 수집 하거나 Azure Monitor 외부로 전달 하려면 진단 설정을 만들어야 합니다. Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기를](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 참조 하세요.

진단 설정을 만들 때 수집할 로그 범주를 지정 합니다. Azure Blockchain 서비스의 범주는 아래에 나열 되어 있습니다.

**Blockchain 프록시 로그** – NGNIX 프록시 로그를 모니터링 하려는 경우 범주를 선택 합니다. 모든 고객 트랜잭션 정보는 감사 및 디버그 목적으로 사용할 수 있습니다.  

**블록 체인 응용 프로그램 로그** – 범주를 선택 하 여 관리 되는 서비스에서 호스트 되는 블록 체인 응용 프로그램의 로그를 가져옵니다. 예를 들어 ABS 쿼럼 멤버의 경우 이러한 로그는 쿼럼 자체의 로그가 됩니다.  

**메트릭 요청**: Azure 메트릭에 자동으로 수집 되는 진단 설정의 대상 Azure Cosmos DB에서 메트릭 데이터를 수집 하는 옵션을 선택 합니다. 리소스 로그를 사용 하 여 메트릭 데이터를 수집 하 여 두 종류의 데이터를 함께 분석 하 고 Azure Monitor 외부에서 메트릭 데이터를 보냅니다.

## <a name="analyze-metric-data"></a>메트릭 데이터 분석  

메트릭 탐색기를 사용 하 여 Azure Blockchain 서비스에 대 한 메트릭을 분석 하 고 ABS 리소스 블레이드의 모니터링 섹션 아래에 있는 메트릭 탭으로 이동할 수 있습니다. 도구 사용에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) 을 참조 하세요. Azure Blockchain 서비스에 대 한 전체 메트릭은 Azure Blockchain 서비스 표준 메트릭에 네임 스페이스에 있습니다.

필터를 추가 하거나 메트릭을 분할할 때 **노드** 차원을 사용할 수 있습니다 .이는 기본적으로 트랜잭션 노드당 메트릭 값과 ABS 멤버의 유효성 검사기 노드를 제공 합니다.

## <a name="analyze-log-data"></a>로그 데이터 분석

다음은 Azure Blockchain 서비스 구성원을 모니터링 하는 데 도움이 되도록 로그 검색 표시줄에 입력할 수 있는 몇 가지 쿼리입니다. 이러한 쿼리는 [새 언어](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)에서 작동합니다.

Blockchain 응용 프로그램 로그의 오류 조건을 쿼리하려면 아래 쿼리를 사용 합니다.

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Blockchain 프록시 로그의 오류 조건을 쿼리하려면 아래 쿼리를 사용 합니다.  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Azure 로그에서 사용 가능한 시간 필터를 사용 하 여 특정 시간 범위에 대 한 쿼리를 필터링 할 수 있습니다.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Azure Blockchain 서비스 데이터 참조 모니터링  

이 문서에서는 Azure Blockchain 서비스의 성능 및 가용성을 분석 하기 위해 수집 된 로그 및 메트릭 데이터에 대 한 참조를 제공 합니다.  

### <a name="resource-logs"></a>리소스 로그

모든 리소스 로그는 블록 체인 서비스와 관련 된 몇 가지 고유한 속성을 포함 하는 최상위 수준의 공통 스키마를 공유 합니다. [최상위 리소스 로그 스키마](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-schema#top-level-resource-logs-schema)문서를 참조할 수 있으며, Azure blockchain 서비스별 속성의 세부 정보는 아래에서 설명 합니다.  

다음 표에서는 Azure Monitor 로그 나 Azure Storage에서 수집 되는 Azure Blockchain 프록시 로그의 속성을 나열 합니다.  


| 속성 이름  | Description |
|:---|:---|
| time | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| resourceID  | 로그가 사용 하도록 설정 된 Azure Blockchain 서비스 리소스입니다.  |
| category  |Azure Blockchain 서비스의 경우 가능한 값은 **proxylogs** 및 **applicationlogs**입니다. |
| operationName  | 이 이벤트가 나타내는 작업의 이름입니다.   |
| 로그 수준  | 기본적으로 Azure Blockchain 서비스는 **정보** 로그 수준을 사용 하도록 설정 합니다.   |
| NodeLocation  | Blockchain 멤버가 배포 되는 Azure 지역입니다.  |
| BlockchainNodeName  | 작업이 수행 되는 Azure Blockchain 서비스 멤버의 노드 이름입니다.   |
| EthMethod  | 쿼럼에서 기본 blockchain 프로토콜에 의해 호출 되는 메서드는 eth_sendTransactions, eth_getBlockByNumber 등이 될 수 있습니다.  |
| 에이전트  | 사용자를 대신 하 여 웹 브라우저 Mozilla, Edge 등의 동작을 수행 하는 사용자 에이전트입니다. 값의 예는 "Mozilla/5.0 (Linux x64) node.js/8.16.0 v8/6.2.414.77"입니다.  |
| 코드   | HTTP 오류 코드입니다. 일반적으로 4XX 및 5XX는 오류 조건입니다.  |
| NodeHost  | 노드의 DNS 이름입니다.   |
| RequestMethodName | HTTP 메서드가 호출 되 고, 여기에서 사용할 수 있는 값은 멤버 만들기, 기존 멤버의 세부 정보 가져오기, 삭제 멤버에 대 한 삭제, 멤버 업데이트에 대 한 패치에 포함 됩니다.   |
| BlockchainMemberName  | 사용자가 제공한 Azure Blockchain 서비스 멤버 이름입니다.  |
| 컨소시엄 | 사용자가 제공한 컨소시엄의 이름입니다.   |
| 원격  | 요청을 보내는 클라이언트의 IP입니다.  |
| RequestSize  | 요청 된 요청 크기 (바이트)입니다.  |
| RequestTime  | 요청의 기간 (밀리초)입니다.|




다음 표에서는 Azure Blockchain 응용 프로그램 로그에 대 한 속성을 나열 합니다.


| 속성 이름  | Description |
|:---|:---|
| time | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| resourceID  | 로그가 사용 하도록 설정 된 Azure Blockchain 서비스 리소스입니다.|
| category  |Azure Blockchain 서비스의 경우 가능한 값은 **proxylogs** 및 **applicationlogs**입니다.  |
| operationName  | 이 이벤트가 나타내는 작업의 이름입니다.   |
| 로그 수준  | 기본적으로 Azure Blockchain 서비스는 **정보** 로그 수준을 사용 하도록 설정 합니다.   |
| NodeLocation  | Blockchain 멤버가 배포 되는 Azure 지역입니다.  |
| BlockchainNodeName  | 작업이 수행 되는 Azure Blockchain 서비스 멤버의 노드 이름입니다.   |
| BlockchainMessage    | 이 필드에는 데이터 일반 로그 인 Blockchain 응용 프로그램 로그가 포함 됩니다. ABS 쿼럼의 경우 쿼럼 로그가 있습니다. 정보, 오류, 경고 및 실행 된 작업에 대 한 자세한 정보를 제공 하는 문자열에 해당 하는 로그 항목의 유형에 대 한 정보를 제공 합니다.   |
| TenantID    | Azure Blockchain 서비스의 지역별 테넌트입니다. 이 필드의 형식은 https://westlake-rp-prod. cloudapp.azure.com 여기서 region은 배포 된 멤버의 Azure 지역을 지정 합니다.<region>       |
| SourceSystem   | 시스템이 로그를 채웁니다 (이 경우에는 **Azure**).    |



### <a name="metrics"></a>메트릭

다음 표에서는 Azure Blockchain 서비스에 대해 수집 된 플랫폼 메트릭을 보여 줍니다. 모든 메트릭은 **Azure Blockchain 서비스** 표준 메트릭에 네임 스페이스에 저장 됩니다.

지원 되는 Azure Monitor 모든 메트릭 (Azure Blockchain 서비스 포함)의 목록은 [Azure Monitor 지원 되는 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)을 참조 하세요.

### <a name="blockchain-metrics"></a>블록 체인 메트릭

다음 표에서는 Azure Blockchain 서비스 멤버 리소스에 대해 수집 되는 블록 체인 메트릭 목록을 지정 합니다.


| 메트릭 이름 | 단위  |  집계 유형| Description   |
|---|---|---|---|
| 보류 중인 트랜잭션   | 카운트  |  평균 | 이를 위해 대기 중인 트랜잭션 수입니다.   |
| 처리 된 블록   | 카운트  | 합계  |  각 시간 간격에서 처리 된 블록 수입니다. 현재 블록 크기는 5 초 이므로 각 노드는 5 분 이내에 12 개의 블록과 60 블록을 처리 합니다.   |
|처리 된 트랜잭션    | 카운트  | 합계  | 블록에서 처리 된 트랜잭션 수입니다.    |
|대기 중인 트랜잭션    |  카운트 | 평균  | 즉시 검색할 수 없는 트랜잭션 수입니다. 이는 순서가 잘못 도착 했 고 앞의 트랜잭션이 도착 하기를 기다리고 있을 수 있습니다. 또는 한 번만 사용 되는 두 개의 트랜잭션 (nonce)과 동일한 가스 값이 있을 수 있으므로 두 번째 트랜잭션은 검색할 수 없습니다.   |

### <a name="connection-metrics"></a>연결 메트릭  

다음 표에서는 Azure Blockchain 서비스 멤버 리소스에 대해 수집 되는 다양 한 연결 메트릭을 보여 줍니다. NGINX 프록시 메트릭입니다.


| 메트릭 이름 | 단위  |  집계 유형| Description |
|---|---|---|---|
| 수락 된 연결   | 카운트  |  합계 | 허용 되는 클라이언트 연결의 총 수입니다.   |
| 활성 연결 수  | 카운트  | 평균  |  대기 중인 연결을 포함 하는 활성 클라이언트 연결의 현재 수입니다.    |
|처리 한 연결    | 카운트  | 합계  | 처리 된 총 연결 수입니다. 일반적으로 일부 리소스 제한에 도달 하지 않는 한 매개 변수 값은 허용 된 연결과 동일 합니다.     |
|처리 한 요청     |  카운트 | 합계  | 총 클라이언트 요청 수입니다.  |


### <a name="performance-metrics"></a>성능 메트릭

다음 표에서는 Azure Blockchain 구성원 리소스의 각 노드에 대해 수집 되는 성능 메트릭을 보여 줍니다.  


| 메트릭 이름 | 단위  |  집계 유형| Description   |
|---|---|---|---|
| CPU 사용량 비율   | 백분율  |  최대 | CPU 사용량의 백분율입니다.     |
| IO 읽기 바이트   | 킬로바이트   | 합계  |  Blockchain 멤버 리소스의 모든 노드에 걸친 IO 읽기 바이트의 합계입니다.      |
|IO 쓰기 바이트     | 킬로바이트   | 합계  | IO의 합계는 블록 체인 멤버 리소스의 모든 노드에 걸쳐 바이트를 씁니다.     |
|메모리 제한       |  7\.8gb   | 평균    | 노드당 blockchain 프로세스에 사용할 수 있는 최대 메모리입니다. |
|메모리 사용량     | 7\.8gb  |  평균 | 모든 노드에서 평균 사용 된 메모리 양입니다.  |
| 메모리 사용 비율     | 백분율   | 평균  |  모든 노드에서 평균 사용 된 메모리의 비율입니다.       |
|저장소 사용량      | 7\.8gb   | 평균  | 모든 노드에서 평균 사용 된 저장소 용량 (GB)입니다.       |


## <a name="next-steps"></a>다음 단계

Blockchain [Data Manager](https://docs.microsoft.com/azure/blockchain/service/data-manager) 에 대해 자세히 알아보고 Blockchain 데이터를 Azure Event Grid으로 캡처하고 변환 합니다.
