---
title: Azure Storage 메트릭 마이그레이션 | Microsoft Docs
description: Azure Monitor에 의해 관리되는 새 메트릭으로 이전 메트릭을 마이그레이션하는 방법에 알아봅니다.
services: storage
author: WenJason
ms.service: storage
ms.topic: article
origin.date: 03/30/2018
ms.date: 02/25/2019
ms.author: v-jay
ms.subservice: common
ms.openlocfilehash: f8a73bb6e86a187a504c2d36098001cfd90015f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477289"
---
# <a name="azure-storage-metrics-migration"></a>Azure Storage 메트릭 마이그레이션

Azure에서 모니터 환경을 통합하는 전략에 맞춰 Azure Storage는 메트릭을 Azure Monitor 플랫폼에 통합합니다. 나중에 Azure 정책에 따라 조기 통지를 통해 이전 메트릭의 서비스가 종료될 예정입니다. 이전 저장소 메트릭을 사용하는 경우 메트릭 정보를 유지하기 위해 서비스 종료 날짜 이전에 마이그레이션해야 합니다.

이 문서에서는 이전 메트릭에서 새 메트릭으로 마이그레이션하는 방법을 보여 줍니다.

## <a name="understand-old-metrics-that-are-managed-by-azure-storage"></a>Azure Storage에서 관리하는 이전 메트릭 이해

Azure Storage는 이전 메트릭 값을 수집하고, 집계하며, 동일한 스토리지 계정 내의 $Metric 테이블에 저장합니다. Azure Portal을 사용하여 모니터링 차트를 설정할 수 있습니다. 또한 Azure Storage SDK를 사용하여 스키마를 기반으로 하는 $Metric 테이블에서 데이터를 읽을 수 있습니다. 자세한 정보는 [저장소 분석](./storage-analytics.md)을 참조하세요.

이전 메트릭은 Azure Blob Storage에 대한 용량 메트릭만 제공합니다. 이전 메트릭은 Blob Storage, Table Storage, Azure 파일 및 Queue Storage에 대한 트랜잭션 메트릭을 제공합니다.

이전 메트릭은 플랫 스키마로 설계되었습니다. 이 설계에서는 메트릭을 트리거하는 트래픽 패턴이 없는 경우 영(0) 메트릭 값이 생성됩니다. 예를 들어 라이브 트래픽에서 저장소 계정으로 서버 시간 제한 오류를 받지 않는 경우에도 **ServerTimeoutError** 값은 $Metric 테이블에서 0으로 설정됩니다.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Azure Monitor에서 관리하는 새 메트릭 이해

새 스토리지 메트릭의 경우, Azure Storage는 메트릭 데이터를 Azure Monitor 백 엔드에 내보냅니다. Azure Monitor는 데이터 수집뿐만 아니라 포털의 데이터를 포함하여 통합 모니터링 환경도 제공합니다. 자세한 내용은 이 [문서](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)를 참조할 수 있습니다.

새 메트릭은 Blob, 테이블, 파일, 큐 및 Premium Storage에 대한 용량 메트릭과 트랜잭션 메트릭을 제공합니다.

다차원은 Azure Monitor에서 제공하는 기능 중 하나입니다. Azure Storage는 새 메트릭 스키마를 정의할 때 디자인을 채택합니다. 메트릭에서 지원되는 차원은 [Azure Monitor의 Azure Storage 메트릭](./storage-metrics-in-azure-monitor.md)에서 세부 정보를 찾을 수 있습니다. 다차원 설계는 수집의 대역폭과 저장 메트릭의 용량 모두에 대한 비용 효율성을 제공합니다. 따라서 트래픽에서 관련 메트릭을 트리거하지 않은 경우 관련 메트릭 데이터가 생성되지 않습니다. 예를 들어, 트래픽이 서버 시간 제한 오류를 트리거하지 않은 경우 **ResponseType** 차원이 **ServerTimeoutError**와 동일한 **Transactions** 메트릭의 값을 쿼리하면 Azure Monitor에서 데이터를 반환하지 않습니다.

## <a name="metrics-mapping-between-old-metrics-and-new-metrics"></a>이전 메트릭과 새 메트릭 간의 메트릭 매핑

메트릭 데이터를 프로그래밍 방식으로 읽는 경우 프로그램에서 새 메트릭 스키마를 채택해야 합니다. 변경 내용을 더 잘 이해하기 위해 다음 표에 나열된 매핑을 참조할 수 있습니다.

**용량 메트릭**

| 이전 메트릭 | 새 메트릭 |
| ------------------- | ----------------- |
| **용량**            | **BlobType** 차원이 **BlockBlob** 또는 **PageBlob**과 동일한 **BlobCapacity** |
| **ObjectCount**        | **BlobType** 차원이 **BlockBlob** 또는 **PageBlob**과 동일한 **BlobCount** |
| **ContainerCount**      | **ContainerCount** |

다음 메트릭은 이전 메트릭에서 지원하지 않는 새로운 제안입니다.
* **TableCapacity**
* **TableCount**
* **TableEntityCount**
* **QueueCapacity**
* **QueueCount**
* **QueueMessageCount**
* **FileCapacity**
* **FileCount**
* **FileShareCount**
* **UsedCapacity**

**트랜잭션 메트릭**

| 이전 메트릭 | 새 메트릭 |
| ------------------- | ----------------- |
| **AnonymousAuthorizationError** | **ResponseType** 차원이 **AuthorizationError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousClientOtherError** | **ResponseType** 차원이 **ClientOtherError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousClientTimeoutError** | **ResponseType** 차원이 **ClientTimeoutError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousNetworkError** | **ResponseType** 차원이 **NetworkError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousServerOtherError** | **ResponseType** 차원이 **ServerOtherError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousServerTimeoutError** | **ResponseType** 차원이 **ServerTimeoutError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousSuccess** | **ResponseType** 차원이 **Success**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AnonymousThrottlingError** | **ResponseType** 차원이 **ClientThrottlingError** 또는 **ServerBusyError**와 동일하고 **Authentication** 차원이 **Anonymous**와 동일한 트랜잭션 |
| **AuthorizationError** | **ResponseType** 차원이 **AuthorizationError**와 동일한 트랜잭션 |
| **가용성** | **가용성** |
| **AverageE2ELatency** | **SuccessE2ELatency** |
| **AverageServerLatency** | **SuccessServerLatency** |
| **ClientOtherError** | **ResponseType** 차원이 **ClientOtherError**와 동일한 트랜잭션 |
| **ClientTimeoutError** | **ResponseType** 차원이 **ClientTimeoutError**와 동일한 트랜잭션 |
| **NetworkError** | **ResponseType** 차원이 **NetworkError**와 동일한 트랜잭션 |
| **PercentAuthorizationError** | **ResponseType** 차원이 **AuthorizationError**와 동일한 트랜잭션 |
| **PercentClientOtherError** | **ResponseType** 차원이 **ClientOtherError**와 동일한 트랜잭션 |
| **PercentNetworkError** | **ResponseType** 차원이 **NetworkError**와 동일한 트랜잭션 |
| **PercentServerOtherError** | **ResponseType** 차원이 **ServerOtherError**와 동일한 트랜잭션 |
| **PercentSuccess** | **ResponseType** 차원이 **Success**와 동일한 트랜잭션 |
| **PercentThrottlingError** | **ResponseType** 차원이 **ClientThrottlingError** 또는 **ServerBusyError**와 동일한 트랜잭션 |
| **PercentTimeoutError** | **ResponseType** 차원이 **ServerTimeoutError**와 동일하거나 **ResponseType**이 **ClientTimeoutError**와 동일한 트랜잭션 |
| **SASAuthorizationError** | **ResponseType** 차원이 **AuthorizationError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASClientOtherError** | **ResponseType** 차원이 **ClientOtherError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASClientTimeoutError** | **ResponseType** 차원이 **ClientTimeoutError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASNetworkError** | **ResponseType** 차원이 **NetworkError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASServerOtherError** | **ResponseType** 차원이 **ServerOtherError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASServerTimeoutError** | **ResponseType** 차원이 **ServerTimeoutError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASSuccess** | **ResponseType** 차원이 **Success**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **SASThrottlingError** | **ResponseType** 차원이 **ClientThrottlingError** 또는 **ServerBusyError**와 동일하고 **Authentication** 차원이 **SAS**와 동일한 트랜잭션 |
| **ServerOtherError** | **ResponseType** 차원이 **ServerOtherError**와 동일한 트랜잭션 |
| **ServerTimeoutError** | **ResponseType** 차원이 **ServerTimeoutError**와 동일한 트랜잭션 |
| **Success** | **ResponseType** 차원이 **Success**와 동일한 트랜잭션 |
| **ThrottlingError** | **ResponseType** 차원이 **ClientThrottlingError** 또는 **ServerBusyError**와 동일한 **트랜잭션**|
| **TotalBillableRequests** | **트랜잭션** |
| **TotalEgress** | **송신** |
| **TotalIngress** | **수신** |
| **TotalRequests** | **트랜잭션** |

## <a name="faq"></a>FAQ

### <a name="how-should-i-migrate-existing-alert-rules"></a>기존 경고 규칙을 마이그레이션하려면 어떻게 해야 할까요?

이전 저장소 메트릭을 기반으로 하는 클래식 경고 규칙을 만든 경우 새 메트릭 스키마를 기반으로 하는 새 경고 규칙을 만들어야 합니다.

### <a name="is-new-metric-data-stored-in-the-same-storage-account-by-default"></a>새 메트릭 데이터는 기본적으로 동일한 저장소 계정에 저장되나요?

번호 메트릭 데이터를 저장소 계정에 보관하려면 [Azure Monitor 진단 설정 API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Monitor의 저장소 메트릭](./storage-metrics-in-azure-monitor.md)
