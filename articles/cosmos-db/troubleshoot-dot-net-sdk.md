---
title: Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
description: 클라이언트 쪽 로깅 및 기타 타사 도구와 같은 기능을 사용 하 여 .NET SDK를 사용 하는 경우 Azure Cosmos DB 문제를 식별, 진단 및 해결 합니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/16/2020
ms.author: anfeldma
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: bc5af781b86ef559abaf33b0cb027ef14adb4262
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021904"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

이 문서에서는 [.NET SDK](sql-api-sdk-dotnet.md) 를 사용 하 여 SQL API 계정을 Azure Cosmos DB 하는 일반적인 문제, 해결 방법, 진단 단계 및 도구에 대해 설명 합니다.
.NET SDK는 Azure Cosmos DB SQL API에 액세스 하는 클라이언트 쪽 논리적 표현을 제공 합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

## <a name="checklist-for-troubleshooting-issues"></a>문제 해결을 위한 검사 목록
응용 프로그램을 프로덕션 환경으로 이동 하기 전에 다음 검사 목록을 고려 하십시오. 검사 목록을 사용 하면 표시 되는 몇 가지 일반적인 문제를 방지할 수 있습니다. 문제가 발생 하는 경우 신속 하 게 진단할 수도 있습니다.

*    최신 [SDK](sql-api-sdk-dotnet-standard.md)를 사용 합니다. Preview Sdk는 프로덕션 환경에서 사용 하면 안 됩니다. 이렇게 하면 이미 수정 된 알려진 문제를 해결할 수 있습니다.
*    [성능 팁](performance-tips.md)을 검토하고 제안된 사례를 따릅니다. 이렇게 하면 크기 조정, 대기 시간 및 기타 성능 문제를 방지할 수 있습니다.
*    문제를 해결 하는 데 도움이 되도록 SDK 로깅을 사용 하도록 설정 합니다. 로깅을 사용 하도록 설정 하면 성능에 영향을 줄 수 있으므로 문제를 해결할 때만 사용 하도록 설정 하는 것이 좋습니다. 다음 로그를 사용 하도록 설정할 수 있습니다.
*    Azure Portal를 사용 하 여 [메트릭을 기록](monitor-accounts.md) 합니다. 포털 메트릭은 Azure Cosmos DB 원격 분석을 표시 합니다 .이는 문제가 Azure Cosmos DB에 해당 하는지 또는 클라이언트 쪽의 것인지를 확인 하는 데 도움이 됩니다.
*    V2 SDK 또는 V3 SDK의 [진단](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) 에서 시점 작업 응답의 [진단 문자열](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) 을 기록 합니다.
*    모든 쿼리 응답에서 [SQL 쿼리 메트릭](sql-api-query-metrics.md) 기록 
*    [SDK 로깅]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) 설정 따르기

이 문서의 [일반적인 문제 및 해결 방법](#common-issues-workarounds) 섹션을 살펴봅니다.

적극적으로 모니터링 되는 [GitHub 문제 섹션](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) 을 확인 합니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다. 솔루션을 찾지 못한 경우 GitHub 문제를 파일에 표시 합니다. 긴급 한 문제에 대 한 지원 틱을 열 수 있습니다.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>일반적인 문제 및 해결 방법

### <a name="general-suggestions"></a>일반 제안
* 가능 하면 Azure Cosmos DB 계정과 동일한 Azure 지역에서 앱을 실행 합니다. 
* 클라이언트 컴퓨터의 리소스가 부족 하 여 연결/가용성 문제가 발생할 수 있습니다. Azure Cosmos DB 클라이언트를 실행 하는 노드에서 CPU 사용률을 모니터링 하 고 높은 부하 상태에서 실행 중인 경우 확장/축소 하는 것이 좋습니다.

### <a name="check-the-portal-metrics"></a>포털 메트릭 확인
[포털 메트릭을](monitor-accounts.md) 확인 하면 클라이언트 쪽 문제 인지 또는 서비스에 문제가 있는지 확인 하는 데 도움이 됩니다. 예를 들어, 메트릭에 처리율이 제한 된 요청 (HTTP 상태 코드 429)이 포함 된 경우 요청을 제한 하는 것을 의미 하는 요청 [빈도 너무 큼](troubleshoot-request-rate-too-large.md) 섹션을 확인 합니다. 

## <a name="common-error-status-codes"></a>일반적인 오류 상태 코드 <a id="error-codes"></a>

| 상태 코드 | 설명 | 
|----------|-------------|
| 400 | 잘못 된 요청 (오류 메시지에 따라 다름)| 
| 401 | [권한 없음](troubleshoot-unauthorized.md) | 
| 404 | [리소스를 찾을 수 없습니다.](troubleshoot-not-found.md) |
| 408 | [요청 시간이 초과 되었습니다.](troubleshoot-dot-net-sdk-request-timeout.md) |
| 409 | 충돌 오류는 쓰기 작업의 리소스에 제공 된 ID를 기존 리소스에서 사용 하는 경우에 발생 합니다. ID는 동일한 파티션 키 값을 가진 모든 문서 내에서 고유 해야 하므로이 문제를 해결 하려면 리소스에 다른 ID를 사용 합니다. |
| 410 | 예외가 사라졌습니다 (SLA를 위반 하지 않아야 하는 일시적인 오류). |
| 412 | 사전 조건이 실패 했습니다. 작업에서 서버에서 사용할 수 있는 버전과 다른 eTag를 지정 했습니다. 낙관적 동시성 오류입니다. 리소스의 최신 버전을 확인하고 요청의 eTag를 업데이트한 후에 요청을 다시 시도해야 합니다.
| 413 | [요청 엔터티가 너무 큼](concepts-limits.md#per-item-limits) |
| 429 | [너무 많은 요청](troubleshoot-request-rate-too-large.md) |
| 449 | 쓰기 작업에서 발생 하는 일시적인 오류 이며 다시 시도 해도 안전 합니다. |
| 500 | 예기치 않은 서비스 오류로 인해 작업이 실패 했습니다. 지원에 문의 [Azure 지원 문제](https://aka.ms/azure-support)를 참조 하세요. |
| 503 | [서비스를 사용할 수 없음](troubleshoot-service-unavailable.md) | 

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT(PAT) 포트 고갈

앱이 [공용 IP 주소 없이 azure Virtual Machines](../load-balancer/load-balancer-outbound-connections.md)에 배포 되는 경우 기본적으로 [azure SNAT 포트](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) 는 VM 외부의 끝점에 대 한 연결을 설정 합니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)으로 제한됩니다. 이 경우 연결 제한, 연결 종료 또는 위에 언급 된 [요청 시간 초과가](troubleshoot-dot-net-sdk-request-timeout.md)발생할 수 있습니다.

 Azure SNAT 포트는 VM에서 개인 IP 주소를 공용 IP 주소에 연결 하는 경우에만 사용 됩니다. Azure SNAT 제한을 방지 하기 위한 두 가지 해결 방법이 있습니다 (전체 응용 프로그램에서 단일 클라이언트 인스턴스를 이미 사용 하 고 있는 경우).

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](../virtual-network/virtual-networks-acl.md)을 사용하여 방화벽에 서브넷을 추가합니다.
* [AZURE VM에 공용 IP](../load-balancer/troubleshoot-outbound-connection.md#assignilpip)를 할당 합니다.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>높은 네트워크 대기 시간
높은 네트워크 대기 시간은 V2 SDK의 [진단 문자열](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) 또는 V3 sdk의 [진단](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) 을 사용 하 여 식별할 수 있습니다.

[시간 초과가](troubleshoot-dot-net-sdk-request-timeout.md) 없고 진단에 단일 요청이 표시 되는 경우 `ResponseTime` `RequestStartTime` (예:이 예에서는 >300 밀리초)와 같이 대기 시간이 긴 단일 요청을 표시 합니다.

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

이 대기 시간에는 여러 원인이 있을 수 있습니다.

* 응용 프로그램이 Azure Cosmos DB 계정과 동일한 지역에서 실행 되 고 있지 않습니다.
* [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 또는 [applicationregion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 구성이 잘못 되어 응용 프로그램이 현재 실행 중인 다른 지역에 연결 하려고 합니다.
* 트래픽이 많아 네트워크 인터페이스에서 병목 상태가 발생할 수 있습니다. 응용 프로그램이 Azure Virtual Machines에서 실행 되는 경우 가능한 해결 방법이 있습니다.
    * [가속화 된 네트워킹을](../virtual-network/create-vm-accelerated-networking-powershell.md)사용 하는 가상 컴퓨터를 사용 하는 것이 좋습니다.
    * [기존 가상 머신에서 가속화 된 네트워킹을](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)사용 하도록 설정 합니다.
    * [더 높은 최종 가상 컴퓨터](../virtual-machines/windows/sizes.md)를 사용 하는 것이 좋습니다.

### <a name="slow-query-performance"></a>쿼리 성능 저하
쿼리 [메트릭은](sql-api-query-metrics.md) 쿼리가 가장 많은 시간을 소비 하는 위치를 확인 하는 데 도움이 됩니다. 쿼리 메트릭에 따라 백 엔드 및 클라이언트에서 얼마나 많은 시간이 소요 되는지 확인할 수 있습니다.
* 백 엔드 쿼리가 신속 하 게 반환 하 고 클라이언트에서 많은 시간을 소비 하는 경우 컴퓨터의 부하를 확인 합니다. 리소스가 부족 하 여 SDK에서 응답을 처리 하는 데 리소스를 사용할 수 있을 때까지 대기 하 고 있을 수 있습니다.
* 백 엔드 쿼리가 느려지는 경우 [쿼리를 최적화](optimize-cost-queries.md) 하 고 현재 [인덱싱 정책을](index-overview.md) 확인 하십시오. 

## <a name="next-steps"></a>다음 단계

* [.Net V3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net V2](performance-tips.md) 에 대 한 성능 지침 알아보기
* [Reactor 기반 Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)에 대해 알아보기

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
