---
title: Azure Storage 모니터링, 진단 및 문제 해결 | Microsoft Docs
description: 스토리지 분석, 클라이언트 쪽 로깅 기타 타사 도구 등의 기능을 사용하여 Azure Storage 관련 문제를 파악, 진단 및 해결합니다.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: fhryo-msft
ms.subservice: common
ms.openlocfilehash: 6edb1abae91a675a3fe47b417a112f0951886aaf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103862"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage 모니터링, 진단 및 문제 해결
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>개요
클라우드 환경에서 호스트된 분산 애플리케이션의 문제를 진단하고 해결하는 과정이 기존 환경보다 복잡할 수 있습니다. 애플리케이션은 PaaS 또는 IaaS 인프라, 온-프레미스, 모바일 장치 또는 이들 중 일부가 조합된 환경에 배포될 수 있습니다. 일반적으로 응용 프로그램의 네트워크 트래픽은 공용 네트워크와 개인 네트워크를 트래버스할 수 있으며 응용 프로그램은 Microsoft Azure Storage 테이블, Blob, 큐 또는 파일과 같은 여러 스토리지 기술뿐 아니라 관계형/문서 데이터베이스와 같은 기타 데이터 저장소도 사용할 수 있습니다.

이러한 애플리케이션은 올바르게 관리하려면 미리 모니터링하여 애플리케이션과 종속 기술의 모든 측면을 진단하고 문제를 해결하는 방법을 파악해야 합니다. Azure Storage 서비스 사용자는 애플리케이션이 사용하는 Storage 서비스를 지속적으로 모니터링하여 정상적인 응답 시간보다 속도가 느려지는 등 예기치 않은 동작 변경을 파악해야 하며, 로깅을 사용하여 보다 자세한 데이터를 수집하고 문제를 자세하게 분석해야 합니다. 모니터링 및 로깅을 통해 얻은 진단 정로를 토대로 애플리케이션에 발생한 문제의 근본 원인을 확인할 수 있습니다. 그러면 문제를 해결하고 해당 문제를 완화하기 위해 수행할 수 있는 적절한 단계를 결정할 수 있습니다. Azure Storage는 핵심 Azure 서비스이며 고객이 Azure 인프라에 배포하는 대부분의 솔루션에서 중요한 역할을 합니다. Azure Storage에는 클라우드 기반 응용 프로그램의 스토리지 문제 모니터링, 진단 및 해결 과정을 간소화하는 기능이 포함되어 있습니다.

> [!NOTE]
> 현재 Azure 파일은 로깅을 지원하지 않습니다.
>

Azure Storage 애플리케이션에서 종단간 문제 해결 실습 가이드는, [Azure Storage 및 로깅, Azcopy, 메시지 분석기를 사용한 종단 간 문제 해결](../storage-e2e-troubleshooting.md)을 참조하세요.

* [소개]
  * [이 가이드의 구성 방식]
* [저장소 서비스 모니터링]
  * [서비스 상태 모니터링]
  * [용량 모니터링]
  * [가용성 모니터링]
  * [성능 모니터링]
* [스토리지 문제 진단]
  * [서비스 상태 문제]
  * [성능 문제]
  * [오류 진단]
  * [Storage 에뮬레이터 문제]
  * [저장소 로깅 도구]
  * [네트워크 로깅 도구 사용]
* [종단 간 추적]
  * [로그 데이터 상관 관계 설정]
  * [클라이언트 요청 ID]
  * [서버 요청 ID]
  * [타임스탬프]
* [문제 해결 지침]
  * [메트릭에서 AverageE2ELatency는 높게 표시되고 AverageServerLatency는 낮게 표시됨]
  * [메트릭에서 AverageE2ELatency 및 AverageServerLatency는 낮게 표시되는데 클라이언트의 대기 시간이 길어짐]
  * [메트릭에서 AverageServerLatency가 높게 표시됨]
  * [큐에서 메시지 배달 중에 예기치 않은 대기 시간이 발생함]
  * [메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨]
  * [메트릭에서 PercentTimeoutError가 증가하는 것으로 표시됨]
  * [메트릭에서 PercentNetworkError가 증가하는 것으로 표시됨]
  * [클라이언트에 HTTP 403(사용할 수 없음) 메시지가 표시됨]
  * [클라이언트에 HTTP 404(찾을 수 없음) 메시지가 표시됨]
  * [클라이언트에 HTTP 409(충돌) 메시지가 표시됨]
  * [메트릭에 PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음]
  * [용량 메트릭에 예기치 않은 저장소 용량 사용 증가가 표시됨]
  * [개발 또는 테스트용으로 저장소 에뮬레이터 사용 시 문제가 발생함]
  * [Azure SDK for .NET 설치 시 문제가 발생함]
  * [저장소 서비스에서 다른 문제가 발생함]
  * [Windows 가상 머신에서 VHD 문제 해결](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Linux 가상 머신에서 VHD 문제 해결](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Windows에서 Azure Files 문제 해결](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Linux에서 Azure Files 문제 해결](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [부록]
  * [부록 1: Fiddler를 사용하여 HTTP 및 HTTPS 트래픽 캡처]
  * [부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처]
  * [부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처를 참조하세요.]
  * [부록 4: Excel을 사용하여 메트릭 및 로그 데이터 보기]
  * [부록 5: Azure DevOps용 Application Insights를 사용한 모니터링]

## <a name="introduction"></a>소개
이 가이드에서는 Azure Storage 분석 등의 도구, Azure Storage 클라이언트 라이브러리의 클라이언트 쪽 로깅 그리고 기타 타사 도구를 사용하여 Azure Storage 관련 문제를 파악, 진단 및 해결하는 방법에 대해 설명합니다.

![][1]

이 가이드의 기본 대상은 Azure Storage 서비스를 사용하는 온라인 서비스의 개발자 및 이러한 온라인 서비스를 관리하는 IT 전문가입니다. 이 가이드의 목표는 다음과 같습니다.

* Azure Storage 계정의 상태와 성능을 유지 관리하는 데 도움이 되는 정보 제공
* 애플리케이션의 문제가 Azure Storage와 관련이 있는지 여부를 결정하는 데 필요한 프로세스 및 도구 제공
* Azure Storage 관련 문제 해결을 위한 실행 가능 지침 제공

### <a name="how-this-guide-is-organized"></a>이 가이드의 구성 방식
"[저장소 서비스 모니터링]" 섹션에서는 Azure Storage 분석 메트릭(Storage 메트릭)을 사용하여 Azure Storage 서비스의 상태와 성능을 모니터링하는 방법을 설명합니다.

"[스토리지 문제 진단]" 섹션에서는 Azure Storage 분석 로깅(스토리지 로깅)을 사용하여 문제를 진단하는 방법을 설명합니다. 또한 .NET용 Storage 클라이언트 라이브러리 또는 Java용 Azure SDK와 같은 클라이언트 라이브러리 중 하나의 기능을 사용하여 클라이언트 쪽 로깅을 사용하도록 설정하는 방법도 설명합니다.

"[종단 간 추적]" 섹션에서는 다양한 로그 파일 및 메트릭 데이터에 포함된 정보의 상관 관계를 지정하는 방법을 설명합니다.

"[문제 해결 지침]" 섹션에서는 발생 가능한 몇 가지 일반적인 저장소 관련 문제에 대한 문제 해결 지침을 제공합니다.

"[부록]"에는 Wireshark, Netmon 등의 기타 도구를 사용하여 네트워크 패킷 데이터를 분석하고, Fiddler를 사용하여 HTTP/HTTPS 메시징을 분석하며, .Microsoft Message Analyzer를 사용하여 로그 데이터의 상관 관계를 지정하는 방법을 설명합니다.

## <a name="monitoring-your-storage-service"></a>저장소 서비스 모니터링
Windows 성능 모니터링에 대해 잘 알고 있는 경우 Storage 메트릭은 Windows 성능 모니터 카운터의 Azure Storage 버전이라고 생각하면 됩니다. Storage 메트릭에서는 서비스 가용성, 처리할 총 요청 수, 처리할 성공한 요청의 백분율 등 포괄적인 메트릭(Windows 성능 모니터 용어로는 카운터) 집합이 제공됩니다. 사용 가능한 메트릭의 전체 목록은 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)를 참조하세요. 저장소 서비스가 메트릭을 수집 및 집계하는 간격(1시간마다/1분마다)을 지정할 수 있습니다. 메트릭을 사용하도록 설정하고 저장소 계정을 모니터링하는 방법에 대한 자세한 내용은 [저장소 메트릭 설정 및 메트릭 데이터 보기](https://go.microsoft.com/fwlink/?LinkId=510865)를 참조하세요.

[Azure Portal](https://portal.azure.com)에 표시할 시간 메트릭을 선택하고 시간 메트릭이 특정 임계값을 초과할 때마다 관리자에게 전자 메일로 알리는 규칙을 구성할 수 있습니다. 자세한 내용은 [경고 알림 받기](/azure/monitoring-and-diagnostics/monitoring-overview-alerts)를 참조하세요.

저장소 서비스는 최상의 노력을 통해 메트릭을 수집하지만 모든 저장소 작업을 기록하지는 못합니다.

Azure Portal에서 저장소 계정에 대해 가용성, 총 요청 수, 평균 대기 시간 수 등의 메트릭을 확인할 수 있습니다. 알림 규칙도 설정되어 가용성이 특정 수준 아래로 떨어지면 관리자에게 경고를 보냅니다. 이 데이터를 확인함으로써 조사할 수 있는 한 가지 영역은 Table service 성공 백분율이 100% 미만이라는 것입니다. 자세한 내용은 "[메트릭에 PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음]" 섹션을 참조하세요.

Azure 애플리케이션을 지속적으로 모니터링한 후 다음을 수행하여 애플리케이션이 정상 상태이며 성능이 적절한 수준인지를 확인해야 합니다.

* 현재 데이터를 비교하고 Azure 저장소와 애플리케이션 동작에 발생하는 중요한 변경 사항을 파악할 수 있도록 애플리케이션에 대해 몇 가지 기본 메트릭을 설정합니다. 대부분의 경우 기본 메트릭의 값은 애플리케이션별로 다르며, 애플리케이션 성능 테스트 시 해당 값을 설정해야 합니다.
* 분 메트릭을 기록한 다음 오류 수나 요청 속도 급증 등의 비정상적인 현상과 예기치 않은 오류를 적극적으로 모니터링하는 데 사용합니다.
* 시간 메트릭을 기록한 다음 평균 오류 수와 요청 속도 등의 평균 값을 모니터링하는 데 사용합니다.
* 이 문서 뒷부분의 "[스토리지 문제 진단]" 섹션에서 설명하는 진단 도구를 사용하여 잠재적 문제를 조사합니다.

다음 그림의 차트에는 시간 메트릭의 평균을 표시함으로써 활동 급증이 숨겨지는 방식이 표시되어 있습니다. 시간 메트릭에는 요청 속도가 안정적인 것으로 표시되지만 분 메트릭에는 실제로 발생하는 요청 수의 증감이 표시됩니다.

![][3]

이 섹션의 나머지 부분에서는 모니터링해야 하는 메트릭과 그 이유에 대해 설명합니다.

### <a name="monitoring-service-health"></a>서비스 상태 모니터링
[Azure Portal](https://portal.azure.com)을 사용하여 전 세계 모든 Azure 지역의 Storage 서비스와 기타 Azure 서비스 상태를 확인할 수 있습니다. 모니터링 기능을 사용하면 제어 범위를 벗어난 문제가 애플리케이션에 사용되는 지역의 Storage 서비스에 영향을 미치는지 즉시 확인할 수 있습니다.

[Azure Portal](https://portal.azure.com)에서는 여러 Azure 서비스에 영향을 주는 문제에 대한 알림도 제공합니다.
참고: 이전에는 이 정보가 [Azure 서비스 대시보드](https://status.azure.com)에서 기록 데이터와 함께 제공되었습니다.

[Azure Portal](https://portal.azure.com)은 Azure 데이터 센터 내부에서 상태 정보를 수집하는 반면(직접적인 상호 작용 모니터링) 간접적인 방식을 도입해 여러 위치에서 Azure 호스팅 웹 애플리케이션에 주기적으로 액세스하는 가상 트랜잭션을 생성할 수도 있습니다. 이러한 방식의 예로는 [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) 및 Azure DevOps용 Application Insights에서 제공하는 서비스가 있습니다. Azure DevOps용 Application Insights에 대한 자세한 내용은 “[부록 5: Azure DevOps용 Application Insights를 사용한 모니터링](#appendix-5)”을 참조하세요.

### <a name="monitoring-capacity"></a>용량 모니터링
일반적으로는 저장되는 데이터 중 Blob가 가장 큰 비율을 차지하므로, Storage 메트릭은 Blob service의 용량 메트릭만을 저장합니다. 이 문서 작성 당시에는 Storage 메트릭을 사용하여 테이블과 큐의 용량을 모니터링할 수 없었습니다. Blob service에 대해 모니터링을 사용하도록 설정한 경우 **$MetricsCapacityBlob** 테이블에서 이 데이터를 확인할 수 있습니다. Storage 메트릭은 매일 한 번씩 이 데이터를 기록하며, **RowKey**의 값을 사용하여 행에 사용자 데이터(**데이터** 값) 또는 분석 데이터(**분석** 값)와 관련된 엔터티가 포함되어 있는지 확인할 수 있습니다. 저장되는 각 엔터티에는 사용된 저장소 공간(바이트 단위로 측정되는 **용량**)과 저장소 계정에서 사용되는 현재 컨테이너 수(**ContainerCount**) 및 Blob 수(**ObjectCount**)에 대한 정보가 포함됩니다. **$MetricsCapacityBlob** 테이블에 저장되는 용량 메트릭에 대한 자세한 내용은 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)를 참조하세요.

> [!NOTE]
> 저장소 계정의 용량 제한에 도달했다는 경고를 조기에 받으려면 이러한 값을 모니터링해야 합니다. Azure Portal에서 집계 저장소 사용량이 지정한 임계값보다 크거나 작아지면 알림을 보내는 경고 규칙을 추가할 수 있습니다.
>
>

Blob 등의 다양한 스토리지 개체 크기를 예측하는 방법에 대한 도움말은 [Azure Storage 요금 청구 방식 이해 - 대역폭, 트랜잭션 및 용량](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx) 블로그 게시물을 참조하세요.

### <a name="monitoring-availability"></a>가용성 모니터링
시간 또는 분 메트릭 테이블(**$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**)의 **가용성** 열 값을 모니터링하여 저장소 계정의 저장소 서비스 가용성을 모니터링해야 합니다. **가용성** 열에는 행이 표시하는 API 작업이나 서비스의 가용성을 나타내는 백분율 값이 포함되어 있습니다. 행에 서비스 또는 특정 API 작업 전체의 메트릭이 포함되어 있으면 **RowKey**가 표시됩니다.

값이 100%보다 작으면 일부 저장소 요청이 실패함을 나타냅니다. 메트릭 데이터에서 **ServerTimeoutError**와 같이 오류 유형이 다른 요청 수를 표시하는 기타 열을 검사하여 해당 요청이 실패하는 이유를 확인할 수 있습니다. 서비스가 요청을 보다 효율적으로 부하 분산하기 위해 파티션을 이동하는 동안 일시적으로 서버 시간이 초과되는 등의 경우에는 **가용성**이 잠시 동안 100%보다 낮아질 수 있습니다. 클라이언트 애플리케이션의 다시 시도 논리가 이와 같은 일시적인 상황을 처리해야 합니다. [스토리지 분석에서 기록한 작업 및 상태 메시지](https://msdn.microsoft.com/library/azure/hh343260.aspx) 문서에는 Storage 메트릭이 해당 **가용성** 계산에 포함하는 트랜잭션 형식이 나와 있습니다.

[Azure Portal](https://portal.azure.com)에서 서비스의 **가용성**이 지정한 임계값보다 작아지면 알림을 보내는 경고 규칙을 추가할 수 있습니다.

이 가이드의 "[문제 해결 지침]" 섹션에서는 가용성과 관련된 몇 가지 일반적인 저장소 서비스 문제에 대해 설명합니다.

### <a name="monitoring-performance"></a>성능 모니터링
저장소 서비스의 성능을 모니터링하려면 시간 및 분 메트릭 테이블에서 다음 메트릭을 사용할 수 있습니다.

* **AverageE2ELatency** 및 **AverageServerLatency** 열의 값은 저장소 서비스 또는 API 작업 형식이 요청을 처리하는 데 걸리는 평균 시간을 표시합니다. **AverageE2ELatency**는 요청을 읽고 응답을 보내는 데 걸리는 시간과 요청을 처리하는 시간을 포함하는 종단 간 대기 시간을 측정한 것으로, 요청이 저장소 서비스에 도달한 후의 네트워크 대기 시간을 포함합니다. **AverageServerLatency**는 처리 시간만 측정한 것으로, 클라이언트와의 통신과 관련한 네트워크 대기 시간은 제외됩니다. 이 두 값이 크게 다를 수 있는 이유에 대한 설명은 이 가이드 뒷부분의 "[메트릭에서 AverageE2ELatency는 높게 표시되고 AverageServerLatency는 낮게 표시됨]" 섹션을 참조하세요.
* **TotalIngress** 및 **TotalEgress** 열의 값은 저장소 서비스로 들어오거나 저장소 서비스에서 나가는 총 데이터 양 또는 특정 API 작업 유형을 통과하는 총 데이터 양(바이트)을 표시합니다.
* **TotalRequests** 열의 값은 저장소 서비스 또는 API 작업이 수신하는 총 요청 수를 표시합니다. **TotalRequests** 는 저장소 서비스가 수신하는 총 요청 수입니다.

일반적으로는 이러한 값이 예기치 않게 변경되는지 여부를 모니터링하여 조사가 필요한 문제가 있는지 파악합니다.

[Azure Portal](https://portal.azure.com)에서 해당 서비스의 성능 메트릭이 지정한 임계값보다 크거나 작아지면 알림을 보내는 경고 규칙을 추가할 수 있습니다.

이 가이드의 "[문제 해결 지침]" 섹션에서는 성능과 관련된 몇 가지 일반적인 저장소 서비스 문제에 대해 설명합니다.

## <a name="diagnosing-storage-issues"></a>저장소 문제 진단
다음과 같은 여러 가지 방법으로 애플리케이션의 문제를 파악할 수 있습니다.

* 애플리케이션을 중단하거나 작동을 중지하는 중대한 오류
* 메트릭의 값이 이전 섹션("[저장소 서비스 모니터링]")에서 설명한 모니터링 중인 메트릭의 기본 값에서 크게 변경되는 현상
* 일부 특정 작업이 정상적으로 완료되지 않거나 일부 기능이 작동하지 않는다는 애플리케이션 사용자의 보고
* 애플리케이션 내에서 생성되어 로그 파일이나 일부 기타 알림 방법을 통해 표시되는 오류

일반적으로 Azure 저장소 서비스 관련 문제는 크게 다음의 네 가지 범주 중 하나에 속합니다.

* 애플리케이션에 성능 문제가 있습니다(사용자가 보고했거나 성능 메트릭의 변경으로 인해 확인됨).
* 하나 이상의 지역에서 Azure Storage 인프라에 문제가 있습니다.
* 애플리케이션에 오류가 발생합니다(사용자가 보고했거나 모니터링하는 오류 수 메트릭 중 하나의 값 증가로 인해 확인됨).
* 개발 및 테스트 중에 로컬 저장소 에뮬레이터를 사용 중일 수 있습니다. 이 경우 저장소 에뮬레이터 사용과 관련된 일부 문제가 발생할 수 있습니다.

다음 섹션에서는 이러한 각 4개 범주의 문제를 진단하고 해결하려면 따라야 하는 단계를 대략적으로 설명합니다. 이 가이드 뒷부분의 "[문제 해결 지침]" 섹션에서는 발생 가능한 몇 가지 일반적인 문제에 대해 자세히 설명합니다.

### <a name="service-health-issues"></a>서비스 상태 문제
서비스 상태 문제는 대개 직접 해결할 수가 없습니다. [Azure Portal](https://portal.azure.com)에서는 저장소 서비스를 포함한 Azure 서비스에서 지속적으로 발생하는 문제와 관련된 정보를 제공합니다. 스토리지 계정을 만들 때 Read-Access Geo Redundant Storage를 선택한 경우, 기본 위치에서 데이터를 사용할 수 없게 되면 응용 프로그램이 보조 위치의 읽기 전용 복사본으로 임시 전환될 수 있습니다. 보조 위치에서 읽으려면 애플리케이션이 기본 스토리지 위치와 보조 스토리지 위치 사이에서 전환할 수 있어야 하며 읽기 전용 데이터를 사용하여 기능이 제한된 모드에서 작업할 수 있어야 합니다. Azure Storage 클라이언트 라이브러리에서는 기본 스토리지에서 읽기가 실패하는 경우 보조 스토리지에서 읽을 수 있는 다시 시도 정책을 정의할 수 있습니다. 또한 애플리케이션은 보조 위치의 데이터가 기본 위치와 일치함을 인식할 수 있어야 합니다. 자세한 내용은 [Azure Storage 중복성 옵션 및 읽기 액세스 지역 중복 스토리지](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) 블로그 게시물을 참조하세요.

### <a name="performance-issues"></a>성능 문제
애플리케이션의 성능은 특히 사용자 측면에서는 주관적일 수 있습니다. 따라서 성능 문제가 발생할 수 있는 영역을 파악하는 데 도움이 되는 기본 메트릭을 마련해야 합니다. 클라이언트 애플리케이션 측면에서는 여러 가지 요인이 Azure 저장소 서비스의 성능에 영향을 줄 수 있습니다. 이러한 요인은 저장소 서비스, 클라이언트 또는 네트워크 인프라에 적용될 수 있으므로 성능 문제가 시작된 위치를 파악하는 전략이 있어야 합니다.

메트릭을 통해 성능 문제의 원인일 가능성이 높은 위치를 파악한 후에는 로그 파일을 사용하여 문제를 추가로 진단하고 해결하기 위한 세부 정보를 찾을 수 있습니다.

이 가이드 뒷부분의 "[문제 해결 지침]" 섹션에는 발생 가능한 몇 가지 일반적인 성능 관련 문제에 대해 자세한 내용이 제공되어 있습니다.

### <a name="diagnosing-errors"></a>오류 진단
애플리케이션 사용자가 클라이언트 애플리케이션에서 보고한 오류에 대해 알려오는 경우가 있습니다. Storage 메트릭은 **NetworkError**, **ClientTimeoutError**, **AuthorizationError** 등의 Storage 서비스에서 발생하는 여러 오류 유형의 수도 기록합니다. Storage 메트릭은 서로 다른 오류 유형의 수만 기록하지만 서버 쪽, 클라이언트 쪽 및 네트워크 로그를 검사하여 개별 요청에 대한 추가 정보를 얻을 수도 있습니다. 일반적으로는 저장소 서비스에서 반환하는 HTTP 상태 코드를 통해 요청이 실패한 이유를 파악할 수 있습니다.

> [!NOTE]
> 일부 일시적인 오류 확인을 기억하세요: 예, 일시적인 네트워크 조건으로 인한 오류나 애플리케이션 오류를 예로 들 수 있습니다.
>
>

다음 리소스에서 저장소 관련 상태 및 오류 코드를 파악할 수 있습니다.

* [공통 REST API 오류 코드](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Blob 서비스 오류 코드](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [큐 서비스 오류 코드](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [테이블 서비스 오류 코드](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [파일 서비스 오류 코드](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>저장소 에뮬레이터 문제
Azure SDK에는 개발 워크스테이션에서 실행할 수 있는 저장소 에뮬레이터가 포함되어 있습니다. Azure 저장소 서비스의 동작을 대부분 시뮬레이트하는 이 에뮬레이터는 개발 및 테스트 중에 사용하면 유용하며, Azure 구독 및 Azure 저장소 계정이 없어도 Azure 저장소 서비스를 사용하는 애플리케이션을 실행할 수 있습니다.

이 가이드의 "[문제 해결 지침]" 섹션에서는 저장소 에뮬레이터 사용 시 발생하는 몇 가지 일반적인 문제에 대해 설명합니다.

### <a name="storage-logging-tools"></a>저장소 로깅 도구
스토리지 로깅은 Azure Storage 계정의 스토리지 요청에 대한 서버 쪽 로깅 기능을 제공합니다. 서버 쪽 로깅을 사용하도록 설정하고 로그 데이터에 액세스하는 방법에 대한 자세한 내용은 [저장소 로깅 사용 및 로그 데이터 액세스](https://go.microsoft.com/fwlink/?LinkId=510867)를 참조하세요.

.NET용 Storage 클라이언트 라이브러리에서는 응용 프로그램이 수행하는 스토리지 작업과 관련된 클라이언트 쪽 로그 데이터를 수집할 수 있습니다. 자세한 내용은 [.NET Storage 클라이언트 라이브러리를 사용한 클라이언트 쪽 로깅](https://go.microsoft.com/fwlink/?LinkId=510868)을 참조하세요.

> [!NOTE]
> SAS 권한 부여 오류 등의 특정 상황에서 사용자는 서버 쪽 Storage 로그에서 요청 데이터를 찾을 수 없는 경우 오류를 보고합니다. 이러한 경우에는 Storage 클라이언트 라이브러리의 로깅 기능을 사용하여 문제의 원인이 클라이언트에 있는지 조사하거나, 네트워크 모니터링 도구를 사용하여 네트워크를 조사할 수 있습니다.
>
>

### <a name="using-network-logging-tools"></a>네트워크 로깅 도구 사용
클라이언트와 서버 간의 트래픽을 캡처하여 클라이언트와 서버가 교환하는 데이터 및 기본 네트워크 상태에 대한 상세 정보를 제공할 수 있습니다. 다음과 같은 유용한 네트워크 로깅 도구를 사용할 수 있습니다.

* [Fiddler](https://www.telerik.com/fiddler) 는 HTTP/HTTPS 요청 및 응답 메시지의 헤더 및 페이로드 데이터를 검사하는 데 사용할 수 있는 무료 웹 디버깅 프록시입니다. 자세한 내용은 [부록 1: Fiddler를 사용하여 HTTP 및 HTTPS 트래픽 캡처](#appendix-1)를 참조하세요.
* [Microsoft 네트워크 모니터(Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) 및 [Wireshark](https://www.wireshark.org/)는 광범위한 네트워크 프로토콜에 대한 상세 패킷 정보를 확인하는 데 사용할 수 있는 무료 네트워크 프로토콜 분석기입니다. Wireshark에 대한 자세한 내용은 “[부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처](#appendix-2)”를 참조하세요.
* Microsoft Message Analyzer는 Netmon을 대체하는 Microsoft의 도구로, 네트워크 패킷 데이터를 캡처할 뿐 아니라 다른 도구에서 캡처한 로그 데이터를 확인 및 분석할 수도 있습니다. 자세한 내용은 “[부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처](#appendix-3)”를 참조하세요.
* 기본 연결 테스트를 수행하여 클라이언트 컴퓨터가 네트워크를 통해 Azure 저장소 서비스에 연결할 수 있는지를 확인하려는 경우 클라이언트에서 표준 **ping** 도구를 통해서는 이 작업을 수행할 수 없습니다. 그러나 [**tcping** 도구](https://www.elifulkerson.com/projects/tcping.php)를 사용하면 연결을 확인할 수 있습니다.

대부분의 경우 스토리지 로깅 및 Storage 클라이언트 라이브러리의 로그 데이터로 문제를 충분히 진단할 수 있습니다. 그러나 이러한 네트워크 로깅 도구가 제공할 수 있는 상세 정보가 필요한 경우도 있습니다. 예를 들어 Fiddler를 통해 HTTP 및 HTTPS 메시지를 확인하면 저장소 서비스가 보내고 받는 헤더 및 페이로드 데이터를 볼 수 있으므로 클라이언트 애플리케이션이 저장소 작업을 다시 시도하는 방법을 검사할 수 있습니다. Wireshark 등의 프로토콜 분석기는 패킷 수준에서 작동하여 TCP 데이터를 확인할 수 있도록 합니다. 따라서 패킷 손실 및 연결 끊김 문제를 해결할 수 있습니다. 메시지 분석기는 HTTP 및 TCP 계층에서 모두 작동할 수 있습니다.

## <a name="end-to-end-tracing"></a>종단 간 추적
다양한 로그 파일을 사용하는 종단 간 추적은 잠재적 문제를 조사하는 데 유용한 기술입니다. 메트릭 데이터의 날짜/시간 정보를 통해 로그 파일에서 문제를 해결하는 데 도움이 되는 상세 정보 찾기를 시작할 위치를 파악할 수 있습니다.

### <a name="correlating-log-data"></a>로그 데이터 상관 관계 지정
클라이언트 애플리케이션의 로그, 네트워크 추적 및 서버 쪽 저장소 로깅을 확인할 때는 여러 로그 파일 간에 요청 상관 관계를 설정할 수 있어야 합니다. 로그 파일에는 상관 관계 식별자로 활용할 수 있는 여러 필드가 포함되어 있습니다. 다른 로그의 항목 간 상관 관계를 설정하는 데 사용할 수 있는 가장 유용한 필드는 클라이언트 요청 ID입니다. 그러나 서버 요청 ID 또는 타임스탬프를 사용하면 유용한 경우도 있습니다. 다음 섹션에서는 이러한 옵션에 대해 자세히 설명합니다.

### <a name="client-request-id"></a>클라이언트 요청 ID
저장소 클라이언트 라이브러리는 모든 요청에 대해 고유한 클라이언트 요청 ID를 자동으로 생성합니다.

* 저장소 클라이언트 라이브러리가 만드는 클라이언트 쪽 로그에서 클라이언트 요청 ID는 요청과 관련된 모든 로그 항목의 **클라이언트 요청 ID** 필드에 표시됩니다.
* Fiddler에서 캡처하는 것과 같은 네트워크 추적에서 클라이언트 요청 ID는 요청 메시지에 **x-ms-client-request-id** HTTP 헤더 값으로 표시됩니다.
* 서버 쪽 저장소 로깅 로그에서 클라이언트 요청 ID는 클라이언트 요청 ID 열에 표시됩니다.

> [!NOTE]
> 클라이언트가 클라이언트 요청 ID 값을 할당할 수 있으므로 여러 요청이 같은 클라이언트 요청 ID를 공유할 수 있습니다. 그러나 저장소 클라이언트 라이브러리가 새 값을 자동으로 할당합니다. 클라이언트에서 재시도할 때, 모든 시도는 동일한 클라이언트 요청 ID를 공유합니다. 클라이언트에서 배치를 보낸 경우, 배치는 단일 클라이언트 요청 ID를 가집니다.
>
>

### <a name="server-request-id"></a>서버 요청 ID
저장소 서비스 서버 요청 Id를 자동으로 생성합니다.

* 서버 쪽 저장소 로깅 로그에서 서버 요청 ID는 **요청 ID 헤더** 열에 표시됩니다.
* Fiddler에서 캡처하는 것과 같은 네트워크 추적에서 서버 요청 ID는 응답 메시지에 **x-ms-request-id** HTTP 헤더 값으로 표시됩니다.
* 저장소 클라이언트 라이브러리가 만드는 클라이언트 쪽 로그에서 서버 요청 ID는 서버 응답 세부 정보를 보여 주는 로그 항목의 **작업 텍스트** 필드에 표시됩니다.

> [!NOTE]
> 저장소 서비스는 수신하는 모든 요청에 항상 고유한 서버 요청 ID를 할당하므로 클라이언트의 모든 다시 시도와 일괄 처리에 포함된 모든 작업에는 고유한 서버 요청 ID가 지정됩니다.
>
>

스토리지 클라이언트 라이브러리가 클라이언트에서 **StorageException**을 throw하는 경우 **RequestInformation** 속성은 **RequestResult** 개체를 포함하며, 이 개체에는 **ServiceRequestID** 속성이 포함됩니다. **OperationContext** 인스턴스에서 **RequestResult** 개체에 액세스할 수도 있습니다.

아래의 코드 샘플은 요청의 **OperationContext** 개체를 저장소 서비스에 연결하여 사용자 지정 **ClientRequestId** 값을 설정하는 방법을 보여 줍니다. 또한 응답 메시지에서 **ServerRequestId** 값을 검색하는 방법도 보여 줍니다.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

### <a name="timestamps"></a>타임스탬프
타임스탬프를 사용하여 관련 로그 항목을 찾을 수는 있지만 이 경우 클라이언트와 서버 간의 클럭 오차 가능성에 주의해야 합니다. 일치하는 서버 측 항목을 클라이언트의 타임 스탬프를 기반으로 15분 더 검색하거나 15분 덜 검색합니다. 메트릭이 포함된 Blob의 Blob 메타데이터는 Blob에 저장된 메트릭의 시간 범위를 나타냅니다. 이 시간 범위는 동일한 분 또는 시간에 대해 메트릭 Blob이 많은 경우에 유용합니다.

## <a name="troubleshooting-guidance"></a>문제 해결 지침
이 섹션에서는 Azure 저장소 서비스를 사용할 때 애플리케이션에 발생할 수 있는 몇 가지 일반적인 문제를 진단하고 해결하는 데 도움이 되는 정보를 제공합니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

**문제 해결 결정 트리**

---
문제가 저장소 서비스 중 하나의 성능과 관련된 경우

* [메트릭에서 AverageE2ELatency는 높게 표시되고 AverageServerLatency는 낮게 표시됨]
* [메트릭에서 AverageE2ELatency 및 AverageServerLatency는 낮게 표시되는데 클라이언트의 대기 시간이 길어짐]
* [메트릭에서 AverageServerLatency가 높게 표시됨]
* [큐에서 메시지 배달 중에 예기치 않은 대기 시간이 발생함]

---
문제가 저장소 서비스 중 하나의 가용성과 관련된 경우

* [메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨]
* [메트릭에서 PercentTimeoutError가 증가하는 것으로 표시됨]
* [메트릭에서 PercentNetworkError가 증가하는 것으로 표시됨]

---
 클라이언트 애플리케이션이 저장소 서비스에서 HTTP 4XX(예: 404) 응답을 받는 경우

* [클라이언트에 HTTP 403(사용할 수 없음) 메시지가 표시됨]
* [클라이언트에 HTTP 404(찾을 수 없음) 메시지가 표시됨]
* [클라이언트에 HTTP 409(충돌) 메시지가 표시됨]

---
[메트릭에 PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음]

---
[용량 메트릭에 예기치 않은 저장소 용량 사용 증가가 표시됨]

---
[많은 수의 VHD가 연결된 Virtual Machines가 예기치 않게 다시 부팅됨]

---
[개발 또는 테스트용으로 저장소 에뮬레이터 사용 시 문제가 발생함]

---
[Azure SDK for .NET 설치 시 문제가 발생함]

---
[저장소 서비스에서 다른 문제가 발생함]

---
### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>메트릭에서 AverageE2ELatency는 높게 표시되고 AverageServerLatency는 낮게 표시됨
아래에 나와 있는 [Azure Portal](https://portal.azure.com) 모니터링 도구 그림은 **AverageE2ELatency**가 **AverageServerLatency**보다 크게 높은 경우의 예를 보여 줍니다.

![][4]

저장소 서비스는 **AverageServerLatency**와 다르게 성공한 요청에 대해서만 **AverageE2ELatency** 메트릭을 계산하며 클라이언트가 데이터를 보내고 저장소 서비스에서 승인을 받는 데 걸리는 시간도 포함합니다. 따라서 클라이언트 애플리케이션의 응답 속도가 느리거나 네트워크 상태로 인해 **AverageE2ELatency**와 **AverageServerLatency**가 서로 다를 수 있습니다.

> [!NOTE]
> 저장소 로깅 로그 데이터에서 개별 저장소 작업에 대한 **E2ELatency** 및 **ServerLatency**도 확인할 수 있습니다.
>
>

#### <a name="investigating-client-performance-issues"></a>클라이언트 성능 문제 조사
사용 가능한 연결 또는 스레드 수가 제한되거나 CPU, 메모리 또는 네트워크 대역폭과 같은 리소스가 부족한 경우 클라이언트의 응답 속도가 느려질 수 있습니다. 저장소 서비스에 대한 비동기 호출을 사용하는 등 클라이언트 코드를 보다 효율적으로 수정하거나, 코어와 메모리가 더 많은 대형 Virtual Machine을 사용하면 문제가 해결될 수도 있습니다.

테이블 및 큐 서비스의 경우 Nagle 알고리즘으로 인해 **AverageServerLatency**에 비해 **AverageE2ELatency**가 길어질 수 있습니다. 자세한 내용은 [소규모 요청에는 적합하지 않은 Nagle 알고리즘](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) 게시물을 참조하세요. **System.Net** 네임스페이스에서 **ServicePointManager** 클래스를 사용하여 코드에서 Nagle 알고리즘을 사용하지 않도록 설정할 수 있습니다. 이 작업은 이미 열려 있는 연결에는 영향을 주지 않으므로 애플리케이션에서 테이블 또는 큐 서비스를 호출하기 전에 이 작업을 수행해야 합니다. 아래에는 작업자 역할의 **Application_Start** 메서드 예제가 나와 있습니다.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

클라이언트 쪽 로그를 통해 클라이언트 애플리케이션이 제출하는 요청의 수를 확인해야 합니다. 또한 CPU, .NET 가비지 수집, 네트워크 이용률 또는 메모리와 같은 클라이언트의 일반 .NET 관련 성능 병목 현상도 확인해야 합니다. .NET 클라이언트 애플리케이션 문제 해결을 시작하려면 [디버깅, 추적 및 프로파일링](https://msdn.microsoft.com/library/7fe0dd2y)을 참조하세요.

#### <a name="investigating-network-latency-issues"></a>네트워크 대기 시간 문제 조사
일반적으로 네트워크에서 발생하는 긴 종단 간 대기 시간의 원인은 일시적인 상태 때문입니다. Wireshark 또는 Microsoft Message Analyzer와 같은 도구를 사용하여 일시적인 네트워크 문제와 영구적인 네트워크 문제(예: 패킷 삭제)를 모두 조사해야 합니다.

Wireshark를 사용하여 네트워크 문제를 해결하는 방법에 대한 자세한 내용은 “[부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처]”를 참조하세요.

Microsoft 메시지 분석기를 사용하여 네트워크 문제를 해결하는 방법에 대한 자세한 내용은 “[부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처를 참조하세요.]”를 참조하세요.

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>메트릭에서 AverageE2ELatency 및 AverageServerLatency는 낮게 표시되는데 클라이언트의 대기 시간이 길어짐
이 시나리오에서는 저장소 요청이 저장소 서비스에 도착할 때까지의 대기 시간이 길어지는 경우로 인한 가능성이 가장 높습니다. 클라이언트의 요청이 Blob 서비스에 정상적인 속도로 도착하지 않는 원인을 조사해야 합니다.

사용 가능한 연결 또는 스레드 수가 제한되는 경우 클라이언트의 요청 전송이 지연될 수 있습니다.

또한 클라이언트가 재시도를 여러 번 수행하고 있는지 확인하고 그렇다면 이유가 무엇인지 조사합니다. 다음을 수행하여 클라이언트가 여러 번의 재시도를 수행하고 있는지 확인할 수 있습니다.

* Storage 분석 로그를 검사합니다. 재시도가 여러 번 발생하는 경우 다른 서버 요청 ID의 동일한 클라이언트 요청 ID로 여러 작업이 표시됩니다.
* 클라이언트 로그를 검사합니다. 자세한 정보 로깅은 다시 시도가 발생된 것을 표시됩니다.
* 코드를 디버깅하고 요청과 관련된 **OperationContext** 개체의 속성을 확인합니다. 작업을 다시 시도하는 경우 **RequestResults** 속성은 여러 고유한 서버 요청 ID를 포함합니다. 또한 각 요청에 대한 시작 및 종료 시간을 확인할 수 있습니다. 자세한 내용은 [서버 요청 ID]섹션의 코드 샘플을 참조하세요.

클라이언트에 문제가 없으면 패킷 손실 등의 네트워크 문제 가능성을 조사해야 합니다. Wireshark 또는 Microsoft Message Analyzer와 같은 도구를 사용하여 네트워크 문제를 조사할 수 있습니다.

Wireshark를 사용하여 네트워크 문제를 해결하는 방법에 대한 자세한 내용은 “[부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처]”를 참조하세요.

Microsoft 메시지 분석기를 사용하여 네트워크 문제를 해결하는 방법에 대한 자세한 내용은 “[부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처를 참조하세요.]”를 참조하세요.

### <a name="metrics-show-high-AverageServerLatency"></a>메트릭에서 AverageServerLatency가 높게 표시됨
Blob 다운로드 요청에 대해 **AverageServerLatency**가 높게 표시되는 경우 Storage 로깅 로그를 사용하여 같은 Blob 또는 Blob 세트에 대해 요청이 반복되는지 여부를 확인해야 합니다. Blob 업로드 요청의 경우 클라이언트에서 사용 중인 블록 크기를 조사해야 합니다. 예를 들어 블록 크기가 64K보다 작은 경우 읽기도 64K 청크보다 작지 않으면 오버헤드가 발생합니다. 또한 여러 클라이언트가 같은 Blob에 병렬로 블록을 업로드하고 있는지도 조사해야 합니다. 요청 수가 급증하여 초당 확장성 목표가 초과되지 않는지 분당 메트릭도 확인해야 합니다. “[메트릭에서 PercentTimeoutError가 증가하는 것으로 표시됨]”을 참조하세요.

같은 Blob 또는 Blob 집합에 대해 요청이 반복될 때 Blob 요청에 대해 **AverageServerLatency** 가 높게 표시되는 경우에는 Azure Cache 또는 Azure CDN(Content Delivery Network)을 사용하여 이러한 Blob를 캐시해야 합니다. 업로드 요청의 경우 더 큰 블록 크기를 사용하면 처리량을 높일 수 있습니다. 테이블에 대한 쿼리의 경우에는 같은 쿼리 작업을 수행하며 데이터가 자주 변경되지 않는 클라이언트에서 클라이언트 쪽 캐싱을 구현할 수도 있습니다.

테이블이나 쿼리의 디자인이 잘못되어 검사 작업이 수행되거나 추가/앞에 추가 방지 패턴을 따르는 경우에도 **AverageServerLatency** 값이 높아지는 증상이 발생할 수 있습니다. 자세한 내용은 "[메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨]"을 참조하세요.

> [!NOTE]
> 다음에서 포괄적인 검사 목록 성능 검사를 찾을 수 있습니다. [Microsoft Azure Storage 성능 및 확장성 검사 목록](storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>큐에서 메시지 배달 중에 예기치 않은 대기 시간이 발생함
애플리케이션이 메시지를 큐에 추가하는 시간과 해당 메시지를 큐에서 읽을 수 있게 되는 시간 사이에 지연이 발생하는 경우 다음 단계를 수행하여 문제를 진단해야 합니다.

* 애플리케이션이 큐에 메시지를 정상적으로 추가할 수 있는지와, 메시지를 추가할 때까지 애플리케이션에서 **AddMessage** 메서드를 여러 번 다시 시도하지 않는지 확인합니다. Storage 클라이언트 라이브러리 로그에는 스토리지 작업의 반복적인 다시 시도가 표시됩니다.
* 메시지를 큐에 추가하는 작업자 역할과 큐에서 메시지를 읽는 작업자 역할 간의 클럭 오차로 인해 처리가 지연되는 것처럼 표시되지는 않는지 확인합니다.
* 큐에서 메시지를 읽는 작업자 역할에서 오류가 발생하는지 확인합니다. 큐 클라이언트가 **GetMessage** 메서드를 호출한 후 승인과 함께 응답하지 않으면 메시지는 **invisibilityTimeout** 기간이 만료될 때까지 큐에서 표시되지 않는 상태로 유지됩니다. 해당 기간이 만료되면 메시지를 다시 처리할 수 있게 됩니다.
* 큐 길이가 시간이 경과함에 따라 커지는지 확인합니다. 다른 작업자가 큐에 추가하는 모든 메시지를 처리하는 데 사용할 수 있는 충분한 작업자가 없는 경우 이러한 현상이 발생할 수 있습니다. 또한 삭제 요청 실패 여부와 메시지가 큐에서 제거되는 횟수를 메트릭에서 확인해야 합니다. 이러한 데이터는 메시지를 삭제하려는 시도가 반복적으로 실패했음을 나타낼 수 있습니다.
* **E2ELatency** 및 **ServerLatency** 값이 정상 상태보다 오랫동안 예상보다 높게 나타나는 큐 작업의 저장소 로깅 로그를 검사합니다.

### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨
저장소 서비스의 확장성 목표를 초과하면 제한 오류가 발생합니다. 저장소 서비스는 단일 클라이언트나 테넌트만이 서비스를 사용하는 현상을 방지하기 위해 제한 오류를 발생시킵니다. Storage 계정의 확장성 목표와 Storage 계정 내 파티션의 성능 목표에 대한 자세한 내용은 [Azure Storage 확장성 및 성능 목표](storage-scalability-targets.md)를 참조하세요.

**PercentThrottlingError** 메트릭에서 제한 오류로 인해 실패하는 요청 백분율이 증가하는 것으로 표시되면 다음의 두 시나리오 중 하나를 조사해야 합니다.

* [일시적인 PercentThrottlingError 증가]
* [영구적인 PercentThrottlingError 증가]

**PercentThrottlingError**는 저장소 요청 수가 증가할 때 함께 증가하거나, 처음으로 애플리케이션의 부하를 테스트할 때 증가하는 경우가 많습니다. 또한 이 오류는 저장소 작업에서 "503 서버 사용 중" 또는 "500 작업 시간 초과" HTTP 상태 메시지로 클라이언트에 표시될 수도 있습니다.

#### <a name="transient-increase-in-PercentThrottlingError"></a>일시적인 PercentThrottlingError 증가
애플리케이션의 작업량이 많은 기간에 **PercentThrottlingError** 값도 급증하는 경우에는 클라이언트의 다시 시도에 지수(선형이 아닌) 백오프 전략을 구현합니다. 백오프 재시도는 파티션의 순간적인 부하를 줄이고 애플리케이션에서 트래픽 급증을 완화시키는 데 도움이 됩니다. 스토리지 클라이언트 라이브러리를 사용하여 다시 시도 정책을 구현하는 방법에 대한 자세한 내용은 [Microsoft.WindowsAzure.Storage.RetryPolicies 네임스페이스](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx)를 참조하세요.

> [!NOTE]
> 애플리케이션의 작업량이 많지 않은 기간에도 **PercentThrottlingError** 값이 급증할 수 있습니다. 이러한 현상이 발생하는 경우 부하 분산을 개선하기 위해 저장소 서비스가 파티션을 이동 중일 가능성이 높습니다.
>
>

#### <a name="permanent-increase-in-PercentThrottlingError"></a>영구적인 PercentThrottlingError 증가
트랜잭션 볼륨을 영구적으로 늘린 후나 처음으로 애플리케이션 부하 테스트를 수행할 때 **PercentThrottlingError** 의 값이 계속 높게 표시되는 경우에는 애플리케이션의 저장소 파티션 사용 방법과 저장소 계정의 확장성 목표 도달 여부를 평가해야 합니다. 예를 들어 파티션 하나로 계산되는 큐에서 제한 오류가 표시되는 경우 추가 큐를 사용하여 트랜잭션을 여러 파티션으로 분산시켜야 합니다. 테이블에서 제한 오류가 표시되는 경우에는 다른 파티션 구성표를 통해 보다 광범위한 파티션 키 값을 사용하여 트랜잭션을 여러 파티션으로 분산시켜야 할 수 있습니다. 이 문제의 일반적인 원인 중 하나는 날짜를 파티션 키로 선택하면 특정일의 모든 데이터가 파티션 하나에 기록되는 앞에 추가/추가 방지 패턴입니다.: 이 경우 부하가 생성되면 쓰기 병목 현상이 발생할 수 있습니다. 다른 파티션 디자인을 사용하거나 Blob Storage를 사용하는 것이 더 효율적인 해결 방법인지 평가합니다. 또한 트래픽이 급증하여 제한이 발생하는지 확인하고 요청 패턴을 효율적으로 조정하는 방식을 조사해야 합니다.

트랜잭션을 여러 파티션으로 분산시키는 경우에도 저장소 계정에 대해 설정된 확장성 제한을 파악해야 합니다. 예를 들어 각각 초당 1KB 메시지를 2,000개까지 처리하는 큐 10개를 사용한 경우 저장소 계정의 초당 전체 메시지 제한은 20,000개입니다. 이 경우 초당 20,000개보다 많은 엔터티를 처리해야 한다면 여러 저장소 계정을 사용해야 합니다. 요청과 엔터티의 크기 역시 저장소 서비스가 클라이언트를 제한하는 시기에 영향을 준다는 점도 기억해야 합니다.: 요청과 엔터티가 큰 경우에는 제한이 더 빨리 시작될 수 있습니다.

쿼리 디자인이 비효율적인 경우에도 테이블 파티션의 확장성 제한에 도달할 수 있습니다. 예를 들어 파티션 내 엔터티 중 1%만 선택하고 모든 엔터티를 검사하는 필터가 포함된 쿼리는 각 엔터티에 액세스해야 합니다. 모든 엔터티 읽기는 해당 파티션의 총 트랜잭션 수 계산에 포함되므로 확장성 목표에 도달하기가 쉽습니다.

> [!NOTE]
> 성능 테스트를 통해 애플리케이션의 비효율적인 쿼리 디자인을 확인해야 합니다.
>
>

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>메트릭에서 PercentTimeoutError가 증가하는 것으로 표시됨
메트릭에서 저장소 서비스 중 하나의 **PercentTimeoutError**가 증가하는 것으로 표시됨과 동시에, 저장소 작업에서 많은 수의 "500 작업 시간 초과" HTTP 상태 메시지가 클라이언트에 수신되는 경우가 있습니다.

> [!NOTE]
> 저장소 서비스가 파티션을 새 서버로 이동하여 요청 부하를 분산할 때는 시간 초과 오류가 일시적으로 발생할 수 있습니다.
>
>

**PercentTimeoutError** 메트릭은 **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** 및 **SASServerTimeoutError** 메트릭을 집계한 것입니다.

서버에 오류가 발생하면 서버 시간이 초과됩니다. 서버의 작업에서 클라이언트가 지정한 제한 시간이 초과되면 클라이언트 시간이 초과됩니다. 예를 들어 Storage 클라이언트 라이브러리를 사용하는 클라이언트가 **QueueRequestOptions** 클래스의 **ServerTimeout** 속성을 사용하여 작업에 제한 시간을 설정할 수 있습니다.

서버 시간 초과는 저장소 서비스에 추가 조사가 필요한 문제가 있음을 나타냅니다. 메트릭을 사용하여 서비스의 확장성 제한에 도달했는지 확인하고 이 문제를 발생시키는 트래픽 급증 현상을 파악할 수 있습니다. 일시적인 문제는 서비스의 부하 분산 작업으로 인해 발생할 수 있습니다. 애플리케이션이 서비스의 확장성 제한에 도달하지 않았는데 영구적인 문제가 발생하는 경우에는 지원 문제에 대해 문의해야 합니다. 클라이언트 시간 초과의 경우 클라이언트에서 제한 시간이 적절한 값으로 설정되어 있는지 확인한 다음 클라이언트에 설정된 제한 시간 값을 변경하거나, 저장소 서비스의 작업 성능을 개선할 수 있는 방법을 조사해야 합니다. 예를 들어 테이블 쿼리를 최적화하거나 메시지 크기를 줄일 수 있습니다.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>메트릭에서 PercentNetworkError가 증가하는 것으로 표시됨
메트릭에서 저장소 서비스 중 하나의 **PercentNetworkError** 가 증가하는 것으로 표시됩니다. **PercentNetworkError** 메트릭은 **NetworkError**, **AnonymousNetworkError** 및 **SASNetworkError** 메트릭을 집계한 것입니다. 이러한 오류는 클라이언트가 저장소 요청을 수행할 때 저장소 서비스에서 네트워크 오류를 검색하면 발생합니다.

이 오류의 가장 일반적인 원인은 저장소 서비스에서 제한 시간이 만료되기 전에 클라이언트의 연결이 끊기는 현상입니다. 클라이언트의 코드를 조사하여 저장소 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다. Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용하여 클라이언트의 네트워크 연결 문제를 조사할 수도 있습니다. 이러한 도구에 대해서는 [부록]에서 설명합니다.

### <a name="the-client-is-receiving-403-messages"></a>클라이언트에 HTTP 403(사용할 수 없음) 메시지가 표시됨
클라이언트 애플리케이션에서 HTTP 403(사용할 수 없음) 오류가 throw되는 경우 클라이언트가 저장소 요청을 보낼 때 만료된 SAS(공유 액세스 서명)를 사용 중이어서일 가능성이 높습니다. 그러나 클럭 오차, 잘못된 키, 빈 헤더 등의 다른 원인일 수도 있습니다. 만료된 SAS가 원인인 경우에는 서버 쪽 저장소 로깅 로그 데이터에 항목이 표시되지 않습니다. 아래 표에는 이 문제가 발생함을 나타내는 Storage 클라이언트 라이브러리에서 생성된 클라이언트 쪽 로그의 샘플이 나와 있습니다.

| 원본 | 자세한 정도 | 자세한 정도 | 클라이언트 요청 ID | 작업 텍스트 |
| --- | --- | --- | --- | --- |
| Microsoft.WindowsAzure.Storage |정보 |3 |85d077ab-… |위치 모드 PrimaryOnly에 대해 위치 Primary로 작업을 시작하는 중입니다. |
| Microsoft.WindowsAzure.Storage |정보 |3 |85d077ab -… |동기 요청을 시작합니다. <https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.WindowsAzure.Storage |정보 |3 |85d077ab -… |응답을 기다리는 중입니다. |
| Microsoft.WindowsAzure.Storage |Warning |2 |85d077ab -… |응답을 기다리는 동안 예외를 throw함: 원격 서버에서 오류를 반환했습니다. (403) 사용 권한 없음 |
| Microsoft.WindowsAzure.Storage |정보 |3 |85d077ab -… |응답을 받았습니다. 상태 코드 = 403, 요청 ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.WindowsAzure.Storage |Warning |2 |85d077ab -… |작업 중에 예외를 throw함: 원격 서버에서 오류를 반환했습니다. (403) 사용 권한 없음 |
| Microsoft.WindowsAzure.Storage |정보 |3 |85d077ab -… |작업을 다시 시도해야 하는지 확인하는 중입니다. 재시도 횟수 = 0, HTTP 상태 코드 403, 예외 = = 원격 서버 오류를 반환했습니다. (403) 사용 권한 없음 |
| Microsoft.WindowsAzure.Storage |정보 |3 |85d077ab -… |위치 모드에 따라 다음 위치가 Primary로 설정되었습니다. |
| Microsoft.WindowsAzure.Storage |오류 |1 |85d077ab -… |다시 시도 정책에서 다시 시도를 허용하지 않았습니다. 작업이 실패했습니다. 원격 서버에서 오류를 반환했습니다. (403) 사용 권한 없음 |

이 시나리오에서는 클라이언트가 서버에 토큰을 보내기 전에 SAS 토큰이 만료되는 이유를 조사해야 합니다.

* 보통 클라이언트에 대해 즉시 사용할 SAS를 만들 때는 시작 시간을 설정하면 안 됩니다. 현재 시간을 사용하여 SAS를 생성하는 호스트와 저장소 서비스 간에 약간의 클럭 차이가 있는 경우에는 저장소 서비스가 아직 유효하지 않은 SAS를 받을 가능성이 있습니다.
* SAS에 대해 매우 짧은 만료 시간을 설정하지 마십시오. 마찬가지로 SAS를 생성하는 호스트와 저장소 서비스 간에 클럭 차이가 약간이라도 있으면 SAS가 예상보다 빨리 만료됩니다.
* SAS 키의 버전 매개 변수(예: **sv=2015-04-05**)가 사용 중인 Storage 클라이언트 라이브러리의 버전과 일치하는지 확인합니다. 항상 최신 버전의 [Storage 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/)를 사용하는 것이 좋습니다.
* 저장소 액세스 키를 다시 생성하면 기존 SAS 토큰이 무효화될 수 있습니다. 이 문제는 클라이언트 애플리케이션의 캐시에 대한 만료 시간이 긴 SAS 토큰을 생성하는 경우 발생할 수 있습니다.

Storage 클라이언트 라이브러리를 사용하여 SAS 토큰을 생성하는 경우에는 유효한 토큰을 쉽게 작성할 수 있습니다. 그러나 Storage REST API를 사용 중이며 수동으로 SAS 토큰을 생성하는 경우에는 [공유 액세스 서명을 사용하여 액세스 위임](https://msdn.microsoft.com/library/azure/ee395415.aspx)을 참조하세요.

### <a name="the-client-is-receiving-404-messages"></a>클라이언트에 HTTP 404(찾을 수 없음) 메시지가 표시됨
클라이언트 애플리케이션이 서버에서 HTTP 404(찾을 수 없음) 메시지를 수신하는 경우 클라이언트가 사용하려는 엔터티, 테이블, Blob, 컨테이너, 큐 등의 개체가 저장소 서비스에 없는 것입니다. 이러한 현상은 다음과 같은 여러 가지 이유로 인해 발생합니다.

* [클라이언트 또는 다른 프로세스가 이전에 개체를 삭제함]
* [SAS(공유 액세스 서명) 권한 부여 문제]
* [클라이언트 쪽 JavaScript 코드에 개체 액세스 권한이 없음]
* [네트워크 오류]

#### <a name="client-previously-deleted-the-object"></a>클라이언트 또는 다른 프로세스가 이전에 개체를 삭제함
클라이언트가 스토리지 서비스에서 데이터 읽기, 업데이트 또는 삭제를 시도하는 시나리오에서는 대개 서버 쪽 로그를 통해 스토리지 서비스에서 해당 개체를 삭제한 이전 작업을 쉽게 확인할 수 있습니다. 로그 데이터에는 다른 사용자나 프로세스가 개체를 삭제한 것이 표시되는 경우가 많습니다. 서버 쪽 저장소 로깅 로그에서 operation-type 및 requested-object-key 열에는 클라이언트가 개체를 삭제한 시기가 표시됩니다.

클라이언트가 개체 삽입을 시도하는 시나리오에서는 클라이언트가 새 개체를 만든다고 가정할 때 해당 작업으로 인해 HTTP 404(찾을 수 없음) 응답이 수신되는 이유를 즉시 확인하기가 어려울 수도 있습니다. 그러나 Blob를 만드는 클라이언트는 Blob 컨테이너를 찾을 수 있어야 하고, 메시지를 작성하는 클라이언트는 큐를 찾을 수 있어야 하며, 행을 추가하는 클라이언트는 테이블을 찾을 수 있어야 합니다.

Storage 클라이언트 라이브러리의 클라이언트 쪽 로그를 사용하여 클라이언트가 Storage 서비스에 특정 요청을 보낸 시기를 자세하게 확인할 수 있습니다.

Storage 클라이언트 라이브러리에서 생성한 다음 클라이언트 쪽 로그에는 클라이언트가 작성 중인 Blob에 대한 컨테이너를 찾을 수 없는 문제가 나와 있습니다. 이 로그에는 다음 저장소 작업에 대한 세부 정보가 포함됩니다.

| 요청 ID | 작업(Operation) |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** 메서드를 호출합니다. 이 작업에는 컨테이너 유무를 확인하는 **HEAD** 요청이 포함됩니다. |
| e2d06d78… |**CreateIfNotExists** 메서드를 호출합니다. 이 작업에는 컨테이너 유무를 확인하는 **HEAD** 요청이 포함됩니다. **HEAD** 는 404 메시지를 반환하지만 계속 실행됩니다. |
| de8b1c3c-... |**UploadFromStream** 메서드를 호출합니다. **PUT** 요청은 실패하며 404 메시지가 반환됩니다. |

로그 항목:

| 요청 ID | 작업 텍스트 |
| --- | --- |
| 07b26a5d-... |https://domemaildist.blob.core.windows.net/azuremmblobcontainer에 대한 동기 요청을 시작하는 중입니다. |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |응답을 기다리는 중입니다. |
| 07b26a5d-... |응답을 받았습니다. 상태 코드 = 200, 요청 ID = eeead849-...Content-MD5 = , ETag = &quot;0x8D14D2DC63D059B&quot; |
| 07b26a5d-... |응답 헤더가 처리되었습니다. 나머지 작업을 진행합니다. |
| 07b26a5d-... |응답 본문을 다운로드하는 중입니다. |
| 07b26a5d-... |작업이 완료되었습니다. |
| 07b26a5d-... |https://domemaildist.blob.core.windows.net/azuremmblobcontainer에 대한 동기 요청을 시작하는 중입니다. |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |응답을 기다리는 중입니다. |
| 07b26a5d-... |응답을 받았습니다. 상태 코드 = 202, 요청 ID = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |응답 헤더가 처리되었습니다. 나머지 작업을 진행합니다. |
| 07b26a5d-... |응답 본문을 다운로드하는 중입니다. |
| 07b26a5d-... |작업이 완료되었습니다. |
| e2d06d78-... |https://domemaildist.blob.core.windows.net/azuremmblobcontainer에 대한 비동기 요청을 시작하는 중입니다.</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |응답을 기다리는 중입니다. |
| de8b1c3c-... |https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt에 대한 동기 요청을 시작하는 중입니다. |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |요청 데이터 쓰기를 준비하는 중입니다. |
| e2d06d78-... |응답을 기다리는 동안 예외를 throw함: 원격 서버에서 오류를 반환했습니다. (404) 찾을 수 없음 |
| e2d06d78-... |응답을 받았습니다. 상태 코드 = 404, 요청 ID = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |응답 헤더가 처리되었습니다. 나머지 작업을 진행합니다. |
| e2d06d78-... |응답 본문을 다운로드하는 중입니다. |
| e2d06d78-... |작업이 완료되었습니다. |
| e2d06d78-... |https://domemaildist.blob.core.windows.net/azuremmblobcontainer에 대한 비동기 요청을 시작하는 중입니다. |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |응답을 기다리는 중입니다. |
| de8b1c3c-... |요청 데이터를 쓰는 중입니다. |
| de8b1c3c-... |응답을 기다리는 중입니다. |
| e2d06d78-... |응답을 기다리는 동안 예외를 throw함: 원격 서버에서 오류를 반환했습니다. (409) 충돌 |
| e2d06d78-... |응답을 받았습니다. 상태 코드 = 409, 요청 ID = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |오류 응답 본문을 다운로드하는 중입니다. |
| de8b1c3c-... |응답을 기다리는 동안 예외를 throw함: 원격 서버에서 오류를 반환했습니다. (404) 찾을 수 없음 |
| de8b1c3c-... |응답을 받았습니다. 상태 코드 = 404, 요청 ID = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |작업 중에 예외를 throw함: 원격 서버에서 오류를 반환했습니다. (404) 찾을 수 없음 |
| de8b1c3c-... |다시 시도 정책에서 다시 시도를 허용하지 않았습니다. 작업이 실패했습니다. 원격 서버에서 오류를 반환했습니다. (404) 찾을 수 없음 |
| e2d06d78-... |다시 시도 정책에서 다시 시도를 허용하지 않았습니다. 작업이 실패했습니다. 원격 서버에서 오류를 반환했습니다. (409) 충돌 |

이 예제에서 로그에는 클라이언트가 **CreateIfNotExists** 메서드(요청 ID e2d06d78…)의 요청을 **UploadFromStream** 메서드(de8b1c3c-...)의 요청과 인터리빙함이 표시됩니다. 이러한 인터리빙은 클라이언트 애플리케이션이 이러한 메서드를 비동기식으로 호출하기 때문에 발생합니다. 클라이언트가 컨테이너의 Blob에 데이터 업로드를 시도하기 전에 해당 컨테이너를 만들도록 클라이언트에서 비동기 코드를 수정합니다. 모든 컨테이너를 미리 만드는 것이 가장 좋습니다.

#### <a name="SAS-authorization-issue"></a>SAS(공유 액세스 서명) 권한 부여 문제
클라이언트 애플리케이션이 작업에 필요한 권한을 포함하지 않는 SAS 키를 사용하려고 하면 저장소 서비스는 HTTP 404(찾을 수 없음) 메시지를 클라이언트에 반환합니다. 그와 동시에 메트릭에서 **SASAuthorizationError** 에 대해 0이 아닌 값이 표시됩니다.

아래 표에는 저장소 로깅 로그 파일의 샘플 서버 쪽 로그 메시지가 나와 있습니다.

| 이름 | 값 |
| --- | --- |
| 요청 시작 시간 | 2014-05-30T06:17:48.4473697Z |
| 작업 유형     | GetBlobProperties            |
| 요청 상태     | SASAuthorizationError        |
| HTTP 상태 코드   | 404                          |
| 인증 유형| Sas                          |
| 서비스 유형       | Blob                         |
| 요청 URL        | https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| 요청 ID 헤더  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| 클라이언트 요청 ID  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


클라이언트 애플리케이션이 권한을 부여 받지 않은 작업을 시도하는 이유를 조사합니다.

#### <a name="JavaScript-code-does-not-have-permission"></a>클라이언트 쪽 JavaScript 코드에 개체 액세스 권한이 없음
JavaScript 클라이언트를 사용 중인데 저장소 서비스에서 HTTP 404 메시지를 반환하는 경우에는 브라우저에서 다음 JavaScript 오류를 확인해야 합니다.

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> 클라이언트 쪽 JavaScript 문제를 해결할 때는 Internet Explorer에서 F12 개발자 도구를 사용하여 브라우저와 저장소 서비스 간에 교환되는 메시지를 추적할 수 있습니다.
>
>

이러한 오류가 발생하는 이유는 웹 페이지가 생성된 도메인과 다른 도메인에서 API를 호출할 수 없도록 하는 [동일 원본 정책](https://www.w3.org/Security/wiki/Same_Origin_Policy) 보안 제한을 웹 브라우저가 구현하기 때문입니다.

JavaScript 문제를 해결하려면 클라이언트가 액세스하는 저장소 서비스에 대해 CORS(Cross-Origin Resource Sharing)을 구성할 수 있습니다. 자세한 내용은 [Azure Storage 서비스에 대한 CORS(크로스-원본 자원 공유) 지원](https://msdn.microsoft.com/library/azure/dn535601.aspx)을 참조하세요.

다음 코드 샘플에서는 Contoso 도메인에서 실행되는 JavaScript가 Blob Storage 서비스의 Blob에 액세스할 수 있도록 Blob 서비스를 구성하는 방법을 보여 줍니다.

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

#### <a name="network-failure"></a>네트워크 오류
네트워크 패킷 손실로 인해 저장소 서비스가 HTTP 404 메시지를 클라이언트에 반환하는 경우가 있습니다. 예를 들어 클라이언트 애플리케이션이 테이블 서비스의 엔터티를 삭제할 때 클라이언트가 저장소 예외를 throw하고 테이블 서비스에서 "HTTP 404(찾을 수 없음)" 상태 메시지를 보고할 수 있습니다. 그런데 Table Storage 서비스에서 테이블을 조사하면 서비스가 요청대로 엔터티를 삭제했음이 확인됩니다.

클라이언트의 예외 세부 정보에는 Table service가 요청에 대해 할당한 요청 ID(7e84f12d…)가 포함됩니다. 이 정보를 사용하여 로그 파일에서 **request-id-header** 열을 검색해 서버 쪽 저장소 로그에서 요청 세부 정보를 찾을 수 있습니다. 메트릭을 사용하여 이러한 오류가 발생하는 시기를 파악한 다음 메트릭에서 이 오류를 기록한 시간을 기준으로 로그 파일을 검색할 수도 있습니다. 이 로그 항목에는 "HTTP(404) 클라이언트 기타 오류" 상태 메시지를 반환하며 실패한 삭제 작업이 표시됩니다. 같은 로그 항목의 **client-request-id** 열에는 클라이언트가 생성한 요청 ID(813ea74f…)도 포함됩니다.

같은 엔터티/같은 클라이언트에서 성공한 삭제 작업에 대해 같은 **client-request-id** 값(813ea74f…)의 다른 항목도 서버 쪽 로그에 포함될 수 있습니다. 이와 같은 성공한 삭제 작업은 실패한 삭제 요청 직전에 수행된 것입니다.

이 시나리오는 클라이언트가 테이블 서비스로 엔터티에 대해 보낸 삭제 요청이 성공했는데 일시적인 네트워크 문제 등으로 인해 서버에서 승인을 받지 못한 경우 발생할 가능성이 가장 높습니다. 그 후 클라이언트는 같은 **client-request-id**를 사용하여 작업을 다시 시도했으나 엔터티가 이미 삭제되었기 때문에 다시 시도가 실패한 것입니다.

이 문제가 자주 발생하는 경우 클라이언트가 테이블 서비스에서 승인을 받지 못하는 이유를 조사해야 합니다. 문제가 일시적으로 발생하는 경우 "HTTP(404) 찾을 수 없음" 오류를 트래핑하고 클라이언트에서 기록하되 클라이언트가 작업을 계속 진행하도록 허용해야 합니다.

### <a name="the-client-is-receiving-409-messages"></a>클라이언트에 HTTP 409(충돌) 메시지가 표시됨
다음 표에서는 두 클라이언트 작업의 서버 쪽 로그에서 추출한 내용을 보여 줍니다. **DeleteIfExists**가 표시된 후 동일한 Blob 컨테이너 이름을 사용하여 **CreateIfNotExists**가 바로 나옵니다. 각 클라이언트 작업에서는 서버에 두 개의 요청을 보냅니다. 먼저 **GetContainerProperties** 요청을 보내 컨테이너가 있는지 확인한 다음, **DeleteContainer** 또는 **CreateContainer** 요청을 보냅니다.

| 타임 스탬프 | 작업(Operation) | 결과 | 컨테이너 이름 | 클라이언트 요청 ID |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

클라이언트 애플리케이션의 코드는 Blob 컨테이너를 삭제한 다음 같은 이름을 사용하여 즉시 다시 만듭니다. 최종적으로 **CreateIfNotExists** 메서드(클라이언트 요청 ID bc881924-…)는 실패하며 HTTP 409(충돌) 오류가 발생합니다. 클라이언트가 Blob 컨테이너, 테이블 또는 큐를 삭제할 때는 해당 이름을 다시 사용할 수 있을 때까지 약간 시간이 걸립니다.

삭제/다시 만들기 패턴이 공통적으로 사용되는 경우 클라이언트 애플리케이션은 새 컨테이너를 만들 때마다 고유한 컨테이너 이름을 사용해야 합니다.

### <a name="metrics-show-low-percent-success"></a>메트릭에 PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음
**PercentSuccess** 메트릭은 HTTP 상태 코드를 기준으로 성공한 작업 비율을 캡처합니다. 상태 코드가 2XX인 작업은 성공한 작업으로 계산되고 상태 코드가 3XX, 4XX, 5XX 범위에 속하는 작업은 실패한 작업으로 계산되며 낮은 **PercentSuccess** 메트릭 값이 지정됩니다. 서버 쪽 저장소 로그 파일에서 이러한 작업은 트랜잭션 상태 **ClientOtherErrors**로 기록됩니다.

이러한 작업은 정상적으로 완료되었으므로 가용성 등의 기타 메트릭에는 영향을 주지 않습니다. 정상적으로 실행되지만 작업 실패에 해당하는 HTTP 상태 코드를 표시하는 작업의 몇 가지 예는 다음과 같습니다.

* **ResourceNotFound** (찾을 수 없음 404): 존재하지 않는 Blob에 대한 GET 요청 등에서 발생합니다.
* **ResouceAlreadyExists**(충돌 409): 리소스가 이미 있는 **CreateIfNotExist** 작업 등에서 발생합니다.
* **ConditionNotMet**(수정되지 않음 304): 클라이언트가 **ETag** 값과 HTTP **If-None-Match** 헤더를 보내 마지막 작업 이후 업데이트된 경우에만 이미지를 요청하는 것과 같은 조건부 작업 등에서 발생합니다.

저장소 서비스에서 반환하는 공통 REST API 오류 코드의 목록은 [공통 REST API 오류 코드](https://msdn.microsoft.com/library/azure/dd179357.aspx) 페이지에서 확인할 수 있습니다.

### <a name="capacity-metrics-show-an-unexpected-increase"></a>용량 메트릭에 예기치 않은 저장소 용량 사용 증가가 표시됨
스토리지 계정에서 용량 사용량이 예기치 않게 갑자기 변경되는 경우 먼저 가용성 메트릭을 확인하여 원인을 조사할 수 있습니다. 예를 들어 삭제 요청 실패 수가 증가하면 Blob Storage 사용량도 증가할 수 있습니다. 공간 확보에 사용되는 SAS 토큰 만료 등의 이유로 인해 공간을 확보하기 위해 수행하는 응용 프로그램 관련 정리 작업이 예상대로 작동하지 않기 때문입니다.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>개발 또는 테스트용으로 저장소 에뮬레이터 사용 시 문제가 발생함
일반적으로는 Azure 저장소 계정을 사용하지 않기 위해 개발 및 테스트 시 저장소 에뮬레이터를 사용합니다. 저장소 에뮬레이터를 사용할 때 일반적으로 발생할 수 있는 문제는 다음과 같습니다.

* ["X" 기능이 저장소 에뮬레이터에서 작동하지 않음]
* [저장소 에뮬레이터를 사용할 때 "HTTP 헤더 중 하나의 값이 올바른 형식이 아닙니다." 오류가 발생함]
* [저장소 에뮬레이터를 실행하려면 관리 권한이 필요함]

#### <a name="feature-X-is-not-working"></a>"X" 기능이 저장소 에뮬레이터에서 작동하지 않음
저장소 에뮬레이터는 파일 서비스 등 Azure 저장소 서비스의 일부 기능을 지원하지 않습니다. 자세한 내용은 [개발 및 테스트에 Azure Storage 에뮬레이터 사용](storage-use-emulator.md)을 참조하세요.

저장소 에뮬레이터에서 지원하지 않는 기능의 경우 클라우드에서 Azure 저장소 서비스를 사용하세요.

#### <a name="error-HTTP-header-not-correct-format"></a>저장소 에뮬레이터를 사용할 때 "HTTP 헤더 중 하나의 값이 올바른 형식이 아닙니다." 오류가 발생함
로컬 스토리지 에뮬레이터에 대해 스토리지 클라이언트 라이브러리를 사용하는 애플리케이션을 테스트 중인데 **CreateIfNotExists**와 같은 메서드를 호출하면 "HTTP 헤더 중 하나의 값이 올바른 형식이 아닙니다."라는 오류 메시지와 함께 호출이 실패합니다. 이 오류는 사용 중인 저장소 에뮬레이터의 버전이 사용 중인 저장소 클라이언트 라이브러리의 버전을 지원하지 않음을 나타냅니다. Storage 클라이언트 라이브러리는 생성하는 모든 요청에 **x-ms-version** 헤더를 추가합니다. 저장소 에뮬레이터는 **x-ms-version** 헤더의 값을 인식할 수 없으면 요청을 거부합니다.

Storage 라이브러리 클라이언트 로그를 사용하여 해당 클라이언트가 전송하는 **x-ms-version header** 의 값을 확인할 수 있습니다. Fiddler를 사용하는 경우 **x-ms-version header** 의 값을 확인하여 클라이언트 애플리케이션의 요청을 추적할 수도 있습니다.

일반적으로는 스토리지 에뮬레이터를 업데이트하지 않고 Storage 클라이언트 라이브러리의 최신 버전을 설치하여 사용하는 경우 이 시나리오가 발생합니다. 저장소 에뮬레이터의 최신 버전을 설치하거나 개발 및 테스트에 에뮬레이터 대신 클라우드 저장소를 사용해야 합니다.

#### <a name="storage-emulator-requires-administrative-privileges"></a>저장소 에뮬레이터를 실행하려면 관리 권한이 필요함
저장소 에뮬레이터를 실행하면 관리자 자격 증명을 입력하라는 메시지가 표시됩니다. 이러한 메시지는 저장소 에뮬레이터를 처음 초기화할 때만 표시됩니다. 저장소 에뮬레이터를 초기화한 후에는 관리 권한이 없어도 에뮬레이터를 실행할 수 있습니다.

자세한 내용은 [개발 및 테스트에 Azure Storage 에뮬레이터 사용](storage-use-emulator.md)을 참조하세요. Visual Studio에서 저장소 에뮬레이터를 초기화할 수도 있으며 이 작업을 위해서도 관리자 권한이 필요합니다.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Azure SDK for .NET 설치 시 문제가 발생함
SDK 설치 시 로컬 컴퓨터에서 저장소 에뮬레이터 설치가 실패합니다. 이 경우 설치 로그에는 다음 메시지 중 하나가 포함됩니다.

* CAQuietExec:  오류: SQL 인스턴스에 액세스할 수 없습니다.
* CAQuietExec:  오류: 데이터베이스를 만들 수 없습니다.

이 오류의 원인은 기존 LocalDB 설치의 문제입니다. 저장소 에뮬레이터는 Azure 저장소 서비스를 시뮬레이트할 때 기본적으로 LocalDB를 사용하여 데이터를 영구적으로 저장합니다. SDK를 설치하기 전에 명령 프롬프트 창에서 다음 명령을 실행하여 LocalDB 인스턴스를 다시 설정할 수 있습니다.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**delete** 명령은 저장소 에뮬레이터의 이전 설치에서 오래된 데이터베이스 파일을 모두 제거합니다.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>저장소 서비스에서 다른 문제가 발생함
이전 문제 해결 섹션에 나와 있지 않은 문제가 저장소 서비스에서 발생한 경우에는 다음 방식을 통해 문제를 진단하고 해결해야 합니다.

* 예상한 기본 동작에서 변경된 사항이 있는지 메트릭을 확인합니다. 메트릭을 참조하여 문제가 일시적인지 영구적인지와 문제의 영향을 받는 저장소 작업을 확인할 수 있습니다.
* 메트릭 정보를 사용하면 서버 쪽 로그 데이터에서 발생하는 오류에 대한 보다 자세한 정보를 검색할 수 있습니다. 이 정보는 문제를 해결하는 데 도움이 될 수 있습니다.
* 서버 쪽 로그의 정보만으로는 문제를 올바르게 해결할 수 없는 경우에는 Storage 클라이언트 라이브러리 클라이언트 쪽 로그를 사용하여 클라이언트 애플리케이션의 동작을 조사하고 Fiddler, Wireshark, Microsoft Message Analyzer 등의 도구를 사용하여 네트워크를 조사할 수 있습니다.

Fiddler를 사용하는 방법에 대한 자세한 내용은 “[부록 1: Fiddler를 사용하여 HTTP 및 HTTPS 트래픽 캡처]”를 참조하세요.

Wireshark를 사용하는 방법에 대한 자세한 내용은 “[부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처]”를 참조하세요.

Microsoft Message Analyzer를 사용하는 방법에 대한 자세한 내용은 “[부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처를 참조하세요.]”를 참조하세요.

## <a name="appendices"></a>부록
부록에서는 Azure Storage 및 기타 서비스의 문제를 진단 및 해결할 때 유용할 수 있는 여러 가지 도구에 대해 설명합니다. 이러한 도구는 Azure Storage에 포함되지는 않으며, 타사 제품도 있습니다. 그러므로 이 부록에서 설명하는 도구에는 Microsoft Azure 또는 Azure Storage에 적용될 수 있는 지원 규약이 적용되지 않기 때문에 평가 프로세스의 일환으로 이러한 도구의 공급자가 제공하는 라이선스 및 지원 옵션을 확인해야 합니다.

### <a name="appendix-1"></a>부록 1: Fiddler를 사용하여 HTTP 및 HTTPS 트래픽 캡처
[Fiddler](https://www.telerik.com/fiddler)는 사용 중인 Azure 저장소 서비스와 클라이언트 애플리케이션 간의 HTTP 및 HTTPS 트래픽을 분석하는 유용한 도구입니다.

> [!NOTE]
> Fiddler는 HTTPS 트래픽을 디코딩할 수 있습니다. Fiddler 설명서에서 Fiddler가 HTTPS 트래픽을 디코딩하는 방식과 그에 따른 보안 관련 사항을 자세히 확인해야 합니다.
>
>

이 부록에서는 Fiddler를 설치한 로컬 컴퓨터와 Azure 저장소 서비스 간의 트래픽을 캡처하도록 Fiddler를 구성하는 방법의 간략한 연습을 제공합니다.

Fiddler를 시작하면 로컬 컴퓨터에서 HTTP 및 HTTPS 트래픽 캡처가 시작됩니다. 아래에는 Fiddler를 제어하는 몇 가지 유용한 명령이 나와 있습니다.

* 트래픽 캡처를 중지 및 시작하려면 주 메뉴에서 **파일**로 이동한 다음 **트래픽 캡처**를 클릭하여 캡처를 설정하거나 해제합니다.
* 캡처된 트래픽 데이터를 저장하려면 주 메뉴에서 **파일**로 이동한 다음 **저장**, **모든 세션**을 차례로 클릭하면 세션 보관 파일에 트래픽을 저장할 수 있습니다. 세션 보관 파일은 나중에 분석을 위해 다시 로드하거나 Microsoft 지원에서 요청하는 경우 전송할 수 있습니다.

Fiddler가 캡처하는 트래픽의 양을 제한하려면 **필터** 탭에서 구성하는 필터를 사용할 수 있습니다. 아래 스크린샷에는 **contosoemaildist.table.core.windows.net** 저장소 엔드포인트로 전송되는 트래픽만 캡처하는 필터가 나와 있습니다.

![][5]

### <a name="appendix-2"></a>부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처
[Wireshark](https://www.wireshark.org/) 는 광범위한 네트워크 프로토콜에 대한 상세 패킷 정보를 확인할 수 있는 네트워크 프로토콜 분석기입니다.

다음 절차에서는 Wireshark를 설치한 로컬 컴퓨터에서 Azure 저장소 계정의 테이블 서비스로 이동하는 트래픽에 대한 상세 패킷 정보를 캡처하는 방법을 설명합니다.

1. 로컬 컴퓨터에서 Wireshark를 시작합니다.
2. **시작** 섹션에서 로컬 네트워크 인터페이스 또는 인터넷에 연결된 인터페이스를 선택합니다.
3. **캡처 옵션**을 클릭합니다.
4. **캡처 필터** 텍스트 상자에 필터를 추가합니다. 예를 들어 **host contosoemaildist.table.core.windows.net**은 **contosoemaildist** 저장소 계정의 Table service 엔드포인트에서 보내거나 받는 패킷만 캡처하도록 Wireshark를 구성합니다. [캡처 필터의 전체 목록](https://wiki.wireshark.org/CaptureFilters)을 확인하세요.

   ![][6]
5. **시작**을 클릭합니다. 그러면 로컬 컴퓨터에서 클라이언트 애플리케이션을 사용할 때 Wireshark가 테이블 서비스 엔드포인트에서 보내거나 받는 모든 패킷을 캡처합니다.
6. 작업을 마쳤으면 주 메뉴에서 **캡처**와 **중지**를 차례로 클릭합니다.
7. 캡처된 데이터를 Wireshark 캡처 파일에 저장하려면 주 메뉴에서 **파일**과 **저장**을 차례로 클릭합니다.

WireShark의 **packetlist** 창에는 발생한 모든 오류가 강조 표시됩니다. **분석**, **전문가 정보**를 차례로 클릭하면 표시되는 **전문가 정보** 창에서 오류와 경고의 요약을 확인할 수도 있습니다.

![][7]

TCP 데이터를 마우스 오른쪽 단추로 클릭하고 **TCP 스트림 확인**을 선택하여 애플리케이션 계층에 표시되는 대로 TCP 데이터를 확인할 수도 있습니다. 캡처 필터를 사용하지 않고 덤프를 캡처한 경우 이와 같이 확인하면 유용합니다. 자세한 내용은 [Following TCP Streams](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html)(TCP 스트림 따라 이동)를 참조하세요.

![][8]

> [!NOTE]
> Wireshark를 사용하는 방법에 대한 자세한 내용은 [Wireshark 사용 설명서](https://www.wireshark.org/docs/wsug_html_chunked)를 참조하세요.
>
>

### <a name="appendix-3"></a>부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처
Microsoft Message Analyzer를 사용하여 Fiddler와 비슷한 방식으로 HTTP 및 HTTPS 트래픽을 캡처할 수 있으며 Wireshark와 비슷한 방식으로 네트워크 트래픽을 캡처할 수 있습니다.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Microsoft Message Analyzer를 사용하여 웹 추적 세션 구성
Microsoft Message Analyzer를 사용하여 HTTP 및 HTTPS 트래픽에 대해 웹 추적 세션을 구성하려면 Microsoft Message Analyzer 애플리케이션을 시작하고 **파일** 메뉴에서 **캡처/추적**을 클릭합니다. 사용 가능한 추적 시나리오 목록에서 **웹 프록시**를 선택합니다. 그런 다음 **추적 시나리오 구성** 패널의 **HostnameFilter** 텍스트 상자에 저장소 엔드포인트의 이름을 추가합니다. [Azure Portal](https://portal.azure.com)에서 이러한 이름을 조회할 수 있습니다. 예를 들어 Azure storage 계정의 이름이 **contosodata**인 경우 **HostnameFilter** 텍스트 상자에 다음을 추가해야 합니다.

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> 호스트 이름이 여러 개이면 공백 문자로 구분합니다.
>
>

추적 데이터 수집을 시작할 준비가 되면 **시작** 단추를 클릭합니다.

Microsoft 메시지 분석기 **웹 프록시** 추적에 대한 자세한 내용은 [Microsoft-PEF-WebProxy 공급자](https://technet.microsoft.com/library/jj674814.aspx)를 참조하세요.

Microsoft Message Analyzer의 기본 제공 **웹 프록시** 추적은 Fiddler를 기반으로 하며 클라이언트 쪽 HTTPS 트래픽을 캡처하고 암호화되지 않은 HTTPS 메시지를 표시할 수 있습니다. **웹 프록시** 추적은 암호화되지 않은 메시지 액세스 권한을 제공하는 모든 HTTP 및 HTTPS 트래픽에 대해 로컬 프록시를 구성하는 방식으로 작동합니다.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Microsoft Message Analyzer를 사용하여 네트워크 문제 진단
Microsoft Message Analyzer **웹 프록시** 추적을 사용하여 클라이언트 애플리케이션과 저장소 서비스 간의 HTTP/HTTPs 트래픽 세부 정보를 캡처할 수 있을 뿐 아니라, 기본 제공 **로컬 링크 계층** 추적을 사용하여 네트워크 패킷 정보를 캡처할 수도 있습니다. 그러면 Wireshark를 사용하여 캡처할 수 있는 것과 비슷한 데이터를 캡처하고 패킷 삭제 등의 네트워크 문제를 진단할 수 있습니다.

아래 스크린샷에는 **DiagnosisTypes** 열에 **정보** 메시지가 몇 개 포함된 예제 **로컬 링크 계층** 추적이 나와 있습니다. **DiagnosisTypes** 열의 아이콘을 클릭하면 메시지 세부 정보가 표시됩니다. 이 예제에서는 서버가 클라이언트에서 승인을 받지 못했으므로 #305 메시지를 다시 전송했습니다.

![][9]

Microsoft Message Analyzer에서 추적 세션을 만들 때는 추적의 노이즈 양을 줄이기 위해 필터를 지정할 수 있습니다. 이렇게 하려면 추적을 정의하는 **캡처/추적** 페이지에서 **Microsoft-Windows-NDIS-PacketCapture** 옆의 **구성** 링크를 클릭합니다. 아래 스크린샷에는 3개 저장소 서비스의 IP 주소에 대한 TCP 트래픽을 필터링하는 구성이 나와 있습니다.

![][10]

Microsoft 메시지 분석기 로컬 링크 계층 추적에 대한 자세한 내용은 [Microsoft-PEF-NDIS-PacketCapture 공급자](https://technet.microsoft.com/library/jj659264.aspx)를 참조하세요.

### <a name="appendix-4"></a>부록 4: Excel을 사용하여 메트릭 및 로그 데이터 보기
다양한 도구를 통해 Azure 테이블 스토리지에서 Storage 메트릭 데이터를 구분된 형식으로 다운로드할 수 있으며, 해당 데이터를 Excel에 로드하여 쉽게 보고 분석할 수 있습니다. Azure Blob Storage의 스토리지 로깅 데이터는 이미 Excel에 로드할 수 있는 구분된 형식으로 되어 있습니다. 그러나 [저장소 분석 로그 형식](https://msdn.microsoft.com/library/azure/hh343259.aspx) 및 [저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)의 정보를 기준으로 적절한 열 제목을 추가해야 합니다.

Blob Storage에서 다운로드한 스토리지 로깅 데이터를 Excel로 가져오려면 다음 단계를 수행합니다.

* **데이터** 메뉴에서 **텍스트에서**를 클릭합니다.
* 보려는 로그 파일을 찾은 다음 **가져오기**를 클릭합니다.
* **텍스트 가져오기 마법사**의 1단계에서 **구분 기호로 분리됨**을 선택합니다.

**텍스트 가져오기 마법사**의 1단계에서 구분 기호로 **세미콜론**만 선택하고 **텍스트 한정자**로 큰따옴표를 선택합니다. 그런 다음 **마침** 을 클릭하고 통합 문서에서 데이터를 배치할 위치를 선택합니다.

### <a name="appendix-5"></a>부록 5: Azure DevOps용 Application Insights를 사용한 모니터링
성능 및 가용성 모니터링의 일부분으로 Azure DevOps의 Application Insights 기능을 사용할 수도 있습니다. 이 도구는 다음과 같은 작업을 수행할 수 있습니다.

* 웹 서비스가 사용 가능하며 응답할 수 있는 상태인지 확인합니다. 앱이 웹 서비스를 사용하는 웹 사이트 또는 디바이스 앱인 경우에도 전 세계 여러 위치에서 몇 분마다 URL을 테스트하고 문제가 있는지 알려 줍니다.
* 웹 서비스의 성능 문제나 예외를 빠르게 진단합니다. CPU 또는 기타 리소스가 확대되는지 확인하고 예외에서 스택 추적을 가져오며 로그 추적을 쉽게 검색할 수 있습니다. 앱 성능이 허용 한도 미만으로 떨어지면 이메일이 전송될 수 있습니다. .NET 및 Java 웹 서비스를 모두 모니터링할 수 있습니다.

[Application Insights란?](../../azure-monitor/app/app-insights-overview.md)에서 추가 정보를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Storage에서 분석에 대 한 자세한 내용은 다음이 리소스를 참조 합니다.

* [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)
* [저장소 분석](storage-analytics.md)
* [저장소 분석 메트릭](storage-analytics-metrics.md)
* [저장소 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [저장소 분석 로그](storage-analytics-logging.md)
* [저장소 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[소개]: #introduction
[이 가이드의 구성 방식]: #how-this-guide-is-organized

[저장소 서비스 모니터링]: #monitoring-your-storage-service
[서비스 상태 모니터링]: #monitoring-service-health
[용량 모니터링]: #monitoring-capacity
[가용성 모니터링]: #monitoring-availability
[성능 모니터링]: #monitoring-performance

[스토리지 문제 진단]: #diagnosing-storage-issues
[서비스 상태 문제]: #service-health-issues
[성능 문제]: #performance-issues
[오류 진단]: #diagnosing-errors
[Storage 에뮬레이터 문제]: #storage-emulator-issues
[저장소 로깅 도구]: #storage-logging-tools
[네트워크 로깅 도구 사용]: #using-network-logging-tools

[종단 간 추적]: #end-to-end-tracing
[로그 데이터 상관 관계 설정]: #correlating-log-data
[클라이언트 요청 ID]: #client-request-id
[서버 요청 ID]: #server-request-id
[타임스탬프]: #timestamps

[문제 해결 지침]: #troubleshooting-guidance
[메트릭에서 AverageE2ELatency는 높게 표시되고 AverageServerLatency는 낮게 표시됨]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[메트릭에서 AverageE2ELatency 및 AverageServerLatency는 낮게 표시되는데 클라이언트의 대기 시간이 길어짐]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[메트릭에서 AverageServerLatency가 높게 표시됨]: #metrics-show-high-AverageServerLatency
[큐에서 메시지 배달 중에 예기치 않은 대기 시간이 발생함]: #you-are-experiencing-unexpected-delays-in-message-delivery

[메트릭에서 PercentThrottlingError가 증가하는 것으로 표시됨]: #metrics-show-an-increase-in-PercentThrottlingError
[일시적인 PercentThrottlingError 증가]: #transient-increase-in-PercentThrottlingError
[영구적인 PercentThrottlingError 증가]: #permanent-increase-in-PercentThrottlingError
[메트릭에서 PercentTimeoutError가 증가하는 것으로 표시됨]: #metrics-show-an-increase-in-PercentTimeoutError
[메트릭에서 PercentNetworkError가 증가하는 것으로 표시됨]: #metrics-show-an-increase-in-PercentNetworkError

[클라이언트에 HTTP 403(사용할 수 없음) 메시지가 표시됨]: #the-client-is-receiving-403-messages
[클라이언트에 HTTP 404(찾을 수 없음) 메시지가 표시됨]: #the-client-is-receiving-404-messages
[클라이언트 또는 다른 프로세스가 이전에 개체를 삭제함]: #client-previously-deleted-the-object
[SAS(공유 액세스 서명) 권한 부여 문제]: #SAS-authorization-issue
[클라이언트 쪽 JavaScript 코드에 개체 액세스 권한이 없음]: #JavaScript-code-does-not-have-permission
[네트워크 오류]: #network-failure
[클라이언트에 HTTP 409(충돌) 메시지가 표시됨]: #the-client-is-receiving-409-messages

[메트릭에 PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음]: #metrics-show-low-percent-success
[용량 메트릭에 예기치 않은 저장소 용량 사용 증가가 표시됨]: #capacity-metrics-show-an-unexpected-increase
[개발 또는 테스트용으로 저장소 에뮬레이터 사용 시 문제가 발생함]: #your-issue-arises-from-using-the-storage-emulator
["X" 기능이 저장소 에뮬레이터에서 작동하지 않음]: #feature-X-is-not-working
[저장소 에뮬레이터를 사용할 때 "HTTP 헤더 중 하나의 값이 올바른 형식이 아닙니다." 오류가 발생함]: #error-HTTP-header-not-correct-format
[저장소 에뮬레이터를 실행하려면 관리 권한이 필요함]: #storage-emulator-requires-administrative-privileges
[Azure SDK for .NET 설치 시 문제가 발생함]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[저장소 서비스에서 다른 문제가 발생함]: #you-have-a-different-issue-with-a-storage-service

[부록]: #appendices
[부록 1: Fiddler를 사용하여 HTTP 및 HTTPS 트래픽 캡처]: #appendix-1
[부록 2: Wireshark를 사용하여 네트워크 트래픽 캡처]: #appendix-2
[부록 3: Microsoft Message Analyzer를 사용하여 네트워크 트래픽 캡처를 참조하세요.]: #appendix-3
[부록 4: Excel을 사용하여 메트릭 및 로그 데이터 보기]: #appendix-4
[부록 5: Azure DevOps용 Application Insights를 사용한 모니터링]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
