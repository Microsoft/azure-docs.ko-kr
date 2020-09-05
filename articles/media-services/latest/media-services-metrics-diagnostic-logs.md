---
title: Azure Monitor를 사용 하 여 메트릭 및 진단 로그 Media Services
titleSuffix: Azure Media Services
description: Azure Monitor를 통해 Azure Media Services 메트릭 및 진단 로그를 모니터링 하는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 49a237a122b97fc44df4525ca02dbb478be104ab
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267609"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Azure Monitor를 통해 Media Services 메트릭 및 진단 로그를 모니터링 합니다.

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) 를 사용 하면 앱의 작동 방식을 이해 하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. Azure Monitor에서 수집 된 모든 데이터는 메트릭 및 로그의 두 가지 기본 형식 중 하나에 적합 합니다. Media Services 진단 로그를 모니터링 하 고 수집 된 메트릭 및 로그에 대 한 경고 및 알림을 만들 수 있습니다. [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)를 사용 하 여 메트릭 데이터를 시각화 하 고 분석할 수 있습니다. [Azure Storage](https://azure.microsoft.com/services/storage/)로 로그를 전송 하 고, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)에 스트림 하거나, [Log Analytics](https://azure.microsoft.com/services/log-analytics/)로 내보내거나, 타사 서비스를 사용할 수 있습니다.

자세한 개요는 [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md) 및 [Azure Monitor 진단 로그](../../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

이 항목에서는 지원 되는 [Media Services 메트릭과](#media-services-metrics) [진단 로그 Media Services](#media-services-diagnostic-logs)에 대해 설명 합니다.

## <a name="media-services-metrics"></a>Media Services 메트릭

메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생시킬 수 있기 때문에, 메트릭은 경고에 유용합니다. 메트릭 경고를 만드는 방법에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조 하세요.

Media Services는 다음 리소스에 대 한 모니터링 메트릭을 지원 합니다.

* 계정
* 스트리밍 엔드포인트

### <a name="account"></a>계정

다음 계정 메트릭을 모니터링할 수 있습니다.

|메트릭 이름|표시 이름|설명|
|---|---|---|
|AssetCount|자산 수|계정의 자산입니다.|
|AssetQuota|자산 할당량|계정의 자산 할당량입니다.|
|AssetQuotaUsedPercentage|사용된 자산 할당량 백분율|이미 사용 된 자산 할당량의 백분율입니다.|
|ContentKeyPolicyCount|콘텐츠 키 정책 수|계정의 콘텐츠 키 정책입니다.|
|ContentKeyPolicyQuota|콘텐츠 키 정책 할당량|계정의 콘텐츠 키 정책 할당량입니다.|
|ContentKeyPolicyQuotaUsedPercentage|사용된 콘텐츠 키 정책 할당량 백분율|이미 사용 된 콘텐츠 키 정책 할당량의 백분율입니다.|
|StreamingPolicyCount|스트리밍 정책 수|계정의 스트리밍 정책.|
|StreamingPolicyQuota|스트리밍 정책 할당량|계정의 스트리밍 정책 할당량입니다.|
|StreamingPolicyQuotaUsedPercentage|사용된 스트리밍 정책 할당량 백분율|이미 사용 된 스트리밍 정책 할당량의 백분율입니다.|

[계정 할당량 및 제한](limits-quotas-constraints.md)도 검토 해야 합니다.

### <a name="streaming-endpoint"></a>스트리밍 엔드포인트

다음 Media Services [스트리밍 끝점](/rest/api/media/streamingendpoints) 메트릭이 지원 됩니다.

|메트릭 이름|표시 이름|설명|
|---|---|---|
|요청|요청|스트리밍 끝점에서 제공 하는 총 HTTP 요청 수를 제공 합니다.|
|송신|송신|스트리밍 끝점 당 분당 총 바이트 수입니다.|
|SuccessE2ELatency|성공 엔드투엔드 대기 시간|스트리밍 끝점이 응답의 마지막 바이트를 보낸 시점에서 요청을 받은 시간입니다.|

### <a name="why-would-i-want-to-use-metrics"></a>메트릭을 사용 하는 이유는 무엇 인가요?

다음은 응용 프로그램의 작동 방식을 이해 하는 데 Media Services 메트릭을 모니터링 하는 방법에 대 한 예입니다. Media Services 메트릭을 사용 하 여 해결할 수 있는 몇 가지 질문은 다음과 같습니다.

* 표준 스트리밍 끝점을 모니터링 하 여 한도를 초과 하는 경우를 확인 어떻게 할까요??
* 어떻게 할까요? 프리미엄 스트리밍 끝점 배율 단위가 충분 한지 확인
* 내 스트리밍 끝점을 확장할 시기를 알 수 있도록 경고를 설정 하려면 어떻게 해야 하나요?
* 계정에 구성 된 최대 송신에 도달 했을 때 알 수 있도록 경고를 설정 어떻게 할까요??
* 실패 한 요청에 대 한 분석 및 실패 원인을 어떻게 확인할 수 있나요?
* 패키지에서 얼마나 많은 HLS 또는 대시 요청을 가져올 수 있나요?
* 실패 한 요청 수의 임계값이 적중 된 경우를 알 수 있도록 경고를 설정 어떻게 할까요??

### <a name="example"></a>예

[Media Services 메트릭을 모니터링 하는 방법을](media-services-metrics-howto.md)참조 하세요.

## <a name="media-services-diagnostic-logs"></a>진단 로그 Media Services

진단 로그는 Azure 리소스 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 합니다. 자세한 내용은 [Azure 리소스에서 로그 데이터를 수집 하 고 사용 하는 방법](../../azure-monitor/platform/platform-logs-overview.md)을 참조 하세요.

Media Services는 다음 진단 로그를 지원 합니다.

* 키 배달

### <a name="key-delivery"></a>키 배달

|Name|설명|
|---|---|
|키 배달 서비스 요청|키 배달 서비스 요청 정보를 표시 하는 로그입니다. 자세한 내용은 [스키마](media-services-diagnostic-logs-schema.md)를 참조 하세요.|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>진단 로그를 사용 하려면 어떻게 해야 하나요?

키 배달 진단 로그를 사용 하 여 검사할 수 있는 몇 가지 작업은 다음과 같습니다.

* DRM 유형에 의해 제공 되는 라이선스 수를 참조 하세요.
* 정책에서 제공 하는 라이선스 수를 확인 합니다.
* DRM 또는 정책 유형별 오류를 참조 하세요.
* 클라이언트에서 승인 되지 않은 라이선스 요청 수를 확인 합니다.

### <a name="example"></a>예

[Media Service 진단 로그를 모니터링 하는 방법을](media-services-diagnostic-logs-howto.md)참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure 리소스에서 로그 데이터를 수집 하 고 사용 하는 방법](../../azure-monitor/platform/platform-logs-overview.md)
* [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)
* [Media Services 메트릭을 모니터링 하는 방법](media-services-metrics-howto.md)
* [Media Service 진단 로그를 모니터링 하는 방법](media-services-diagnostic-logs-howto.md)
