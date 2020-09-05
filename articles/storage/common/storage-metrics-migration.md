---
title: 스토리지 분석 메트릭에서 Azure Monitor 메트릭에 이동 | Microsoft Docs
description: 스토리지 분석 메트릭 (클래식 메트릭)에서 Azure Monitor 메트릭으로 전환 하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: c8eae70b88aa454e5d712b3c5b7930b12d169912
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078251"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Azure Monitor에서 메트릭으로 전환

**2023 년 8 월 31** 일에는 스토리지 분석 메트릭을 사용 하 *는 것이 좋습니다* . 클래식 메트릭을 사용 하는 경우 해당 날짜 이전의 Azure Monitor의 메트릭으로 전환 해야 합니다. 이 문서는 전환을 수행 하는 데 도움이 됩니다.

## <a name="steps-to-complete-the-transition"></a>전환을 완료 하는 단계

Azure Monitor에서 메트릭으로 전환 하려면 다음 방법을 사용 하는 것이 좋습니다.

1. Azure Monitor에서 클래식 메트릭과 메트릭의 몇 가지 [주요 차이점](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) 에 대해 알아봅니다. 

2. 현재 사용 중인 클래식 메트릭 목록을 컴파일합니다.

3. [Azure Monitor에서](#metrics-mapping-between-old-metrics-and-new-metrics) 현재 사용 하는 메트릭과 동일한 데이터를 제공 하는 메트릭을 식별 합니다. 
   
4. [차트](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) 또는 [대시보드](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) 를 만들어 메트릭 데이터를 봅니다.

   > [!NOTE]
   > Azure Monitor 메트릭은 기본적으로 사용 하도록 설정 되어 있으므로 메트릭을 캡처하기 시작 하기 위해 수행 해야 할 작업이 없습니다. 그러나 이러한 메트릭을 보려면 차트나 대시보드를 만들어야 합니다. 
 
5. 클래식 저장소 메트릭을 기반으로 하는 경고 규칙을 만든 경우 Azure Monitor의 메트릭을 기반으로 하는 [경고 규칙을 만듭니다](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) . 

6. Azure Monitor의 모든 메트릭을 볼 수 있게 되 면 클래식 로깅을 해제할 수 있습니다. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>클래식 메트릭과 Azure Monitor의 메트릭 비교

이 섹션에서는 이러한 두 메트릭 플랫폼 간의 몇 가지 주요 차이점에 대해 설명 합니다.

주요 차이점은 메트릭을 관리 하는 방법에 있습니다. 클래식 메트릭은 Azure Storage에서 관리 하지만 Azure Monitor 메트릭은 Azure Monitor에서 관리 됩니다. Azure Storage 기본 메트릭을 사용 하 여 메트릭 값을 수집 하 고 집계 한 후 저장소 계정에 있는 테이블에 저장 합니다. Azure Monitor에서 메트릭을 사용 하 Azure Storage는 메트릭 데이터를 Azure Monitor 백 엔드에서 보냅니다. Azure Monitor는 Azure Portal 데이터 뿐만 아니라 수집 데이터를 포함 하는 통합 모니터링 환경을 제공 합니다. 

메트릭이 지원 되는 것 처럼 클래식 메트릭은 Azure Blob 저장소에 대 한 **용량** 메트릭만 제공 합니다. Azure Monitor 메트릭은 Blob, 테이블, 파일, 큐 및 premium storage에 대 한 용량 메트릭을 제공 합니다. 클래식 메트릭은 Blob, 테이블, Azure 파일 및 큐 저장소에 대 한 **트랜잭션** 메트릭을 제공 합니다. Azure Monitor에서 메트릭은 premium storage를 해당 목록에 추가 합니다.

계정의 활동이 메트릭을 트리거하지 않는 경우 클래식 메트릭은 해당 메트릭에 대해 0 값을 표시 합니다. Azure Monitor 메트릭은 데이터를 완전히 생략 하 여 클리너 보고서를 발생 시킵니다. 예를 들어 클래식 메트릭을 사용 하는 경우 서버 시간 제한 오류가 보고 되지 않으면 `ServerTimeoutError` 메트릭 테이블의 값이 0으로 설정 됩니다. 차원이와 같은 메트릭 값을 쿼리할 때 Azure Monitor는 데이터를 반환 하지 않습니다 `Transactions` `ResponseType` `ServerTimeoutError` . 

Azure Monitor의 메트릭에 대 한 자세한 내용은 [Azure Monitor의 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)을 참조 하세요.

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Azure Monitor의 메트릭에 클래식 메트릭 매핑

 이러한 표를 사용 하 여 현재 사용 중인 메트릭과 동일한 데이터를 제공 Azure Monitor의 메트릭을 식별할 수 있습니다. 

**용량 메트릭**

| 클래식 메트릭 | Azure Monitor의 메트릭 |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity`차원이 또는와 `BlobType` 같은 `BlockBlob``PageBlob` |
| `ObjectCount`        | `BlobCount`차원이 또는와 `BlobType` 같은 `BlockBlob``PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> 또한 클래식 메트릭으로 사용할 수 없는 몇 가지 새로운 용량 메트릭이 있습니다. 전체 목록을 보려면 [메트릭](../common/monitor-storage-reference.md#metrics)을 참조 하세요.

**트랜잭션 메트릭**

| 클래식 메트릭 | Azure Monitor의 메트릭 |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | 차원이이 `ResponseType` `AuthorizationError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousClientOtherError` | 차원이이 `ResponseType` `ClientOtherError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousClientTimeoutError` | 차원이이 `ResponseType` `ClientTimeoutError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousNetworkError` | 차원이이 `ResponseType` `NetworkError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousServerOtherError` | 차원이이 `ResponseType` `ServerOtherError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousServerTimeoutError` | 차원이이 `ResponseType` `ServerTimeoutError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousSuccess` | 차원이이 `ResponseType` `Success` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AnonymousThrottlingError` | 차원이 `ResponseType` `ClientThrottlingError` 또는이 `ServerBusyError` 고 차원이 `Authentication` 와 같은 트랜잭션 `Anonymous` |
| `AuthorizationError` | `ResponseType`차원이 인 트랜잭션`AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | `ResponseType`차원이 인 트랜잭션`ClientOtherError` |
| `ClientTimeoutError` | `ResponseType`차원이 인 트랜잭션`ClientTimeoutError` |
| `NetworkError` | `ResponseType`차원이 인 트랜잭션`NetworkError` |
| `PercentAuthorizationError` | `ResponseType`차원이 인 트랜잭션`AuthorizationError` |
| `PercentClientOtherError` | `ResponseType`차원이 인 트랜잭션`ClientOtherError` |
| `PercentNetworkError` | `ResponseType`차원이 인 트랜잭션`NetworkError` |
| `PercentServerOtherError` | `ResponseType`차원이 인 트랜잭션`ServerOtherError` |
| `PercentSuccess` | `ResponseType`차원이 인 트랜잭션`Success` |
| `PercentThrottlingError` | 차원이 `ResponseType` 또는와 같은 트랜잭션 `ClientThrottlingError``ServerBusyError` |
| `PercentTimeoutError` | 차원이 다음 보다 `ResponseType` 크거나 `ServerTimeoutError` `ResponseType` 같은 트랜잭션 `ClientTimeoutError` |
| `SASAuthorizationError` | 차원이이 `ResponseType` `AuthorizationError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASClientOtherError` | 차원이이 `ResponseType` `ClientOtherError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASClientTimeoutError` | 차원이이 `ResponseType` `ClientTimeoutError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASNetworkError` | 차원이이 `ResponseType` `NetworkError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASServerOtherError` | 차원이이 `ResponseType` `ServerOtherError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASServerTimeoutError` | 차원이이 `ResponseType` `ServerTimeoutError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASSuccess` | 차원이이 `ResponseType` `Success` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `SASThrottlingError` | 차원이 `ResponseType` `ClientThrottlingError` 또는이 `ServerBusyError` 고 차원이 `Authentication` 와 같은 트랜잭션 `SAS` |
| `ServerOtherError` | `ResponseType`차원이 인 트랜잭션`ServerOtherError` |
| `ServerTimeoutError` | `ResponseType`차원이 인 트랜잭션`ServerTimeoutError` |
| `Success` | `ResponseType`차원이 인 트랜잭션`Success` |
| `ThrottlingError` | `Transactions`차원이 또는와 `ResponseType` 같은 `ClientThrottlingError``ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Azure Monitor의 스토리지 메트릭](./storage-metrics-in-azure-monitor.md)
