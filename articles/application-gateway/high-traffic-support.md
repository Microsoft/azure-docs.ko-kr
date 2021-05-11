---
title: Application Gateway 높은 트래픽 볼륨 지원
description: 이 문서에서는 높은 네트워크 트래픽 볼륨 시나리오를 지원하도록 Azure Application Gateway를 구성하기 위한 지침을 제공합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586113"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway 높은 트래픽 지원

>[!NOTE]
> 해당 문서에서는 높은 트래픽 볼륨으로 인해 발생한 추가 트래픽을 처리하도록 Application Gateway를 설정하는 데 도움이 되는 몇 가지 권장 지침을 설명합니다. 경고 임계값은 전적으로 제안 사항이며 본질적으로 범용입니다. 사용자는 워크로드 및 사용률 기대치에 따라 경고 임계값을 결정할 수 있습니다.

웹 애플리케이션에 대한 트래픽을 관리하는 확장 가능하고 안전한 방법으로 WAF(웹 애플리케이션 방화벽)에서 Application Gateway를 사용할 수 있습니다.

트래픽 급증에 대비하고 QoS에 대한 잠재적 영향을 최소화할 수 있도록 트래픽에 따라 약간의 버퍼를 사용하여 Application Gateway 크기를 조정하는 것이 중요합니다. 다음 제안 사항은 추가 트래픽을 처리하도록 WAF를 사용하여 Application Gateway를 설정하는 데 유용합니다.

Application Gateway에서 제공하는 메트릭의 전체 목록은 [메트릭 설명서](./application-gateway-metrics.md)를 확인하세요. 메트릭에 대한 경고를 설정하는 방법에 대한 자세한 내용은 Azure Portal의 [메트릭 시각화](./application-gateway-metrics.md#metrics-visualization) 및 [Azure Monitor 설명서](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Application Gateway v1 SKU에 대한 스케일링(표준/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>최대 CPU 사용량을 기준으로 인스턴스 수 설정
v1 SKU 게이트웨이를 사용하는 경우 스케일링을 위해 Application Gateway를 최대 32 인스턴스까지 설정할 수 있습니다. Application Gateway의 CPU 사용률이 지난 한 달간 80% 이상 급증한 적이 있는지 확인합니다. 해당 항목을 모니터링하기 위한 메트릭으로 사용할 수 있습니다. 트래픽 급증을 고려하여 최대 사용량에 따라 10%에서 20%까지 추가 버퍼를 사용하여 인스턴스 수를 설정하는 것이 좋습니다.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="V1 CPU 사용률 메트릭" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>자동 크기 조정 기능 및 성능 혜택을 위해 v1에서 v2 SKU 사용
v2 SKU는 트래픽이 늘어남에 따라 Application Gateway 확장할 수 있도록 자동 크기 조정을 제공합니다. 또한 v1과 비교할 때 5배 더 나은 TLS 오프로드 성능, 더 빠른 배포 및 업데이트 시간, 영역 중복성 등의 기타 중요한 성능상의 이점을 제공합니다. 자세한 내용은 [v2 설명서](./application-gateway-autoscaling-zone-redundant.md)를 참조하고 v1에서 v2로 [마이그레이션 설명서](./migrate-v1-v2.md)를 참조하여 기존 v1 SKU 게이트웨이를 v2 SKU로 마이그레이션하는 방법에 대해 알아보세요. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Application Gateway v2 SKU(Standard_v2/WAF_v2 SKU)의 자동 크기 조정

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>최대 인스턴스 수를 가능한 최대 수(125)로 설정
 
Application Gateway v2 SKU의 경우 최대 인스턴스 수를 설정 가능한 최댓값인 125로 설정하면 Application Gateway에서 필요에 따라 스케일 아웃할 수 있습니다. 이렇게 하면 애플리케이션에 대한 트래픽 증가를 처리할 수 있습니다. 사용한 CU(용량 단위)에 대해서만 요금이 부과됩니다. 

서브넷의 서브넷 크기와 사용 가능한 IP 주소 수를 확인하고 이에 따라 최대 인스턴스 수를 설정해야 합니다. 서브넷에 수용할 공간이 충분하지 않은 경우에는 충분한 용량을 가진 동일한 서브넷이나 다른 서브넷에 게이트웨이를 다시 만들어야 합니다. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2 자동 스케일링 구성" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>평균 컴퓨팅 단위 사용량을 기준으로 최소 인스턴스 수 설정

Application Gateway v2 SKU의 경우 자동 스케일링을 통해 스케일 아웃하고 트래픽이 발생할 수 있는 추가 인스턴스 집합을 프로비저닝하는 데는 6~7분 가량 소요됩니다. 그 과정에서 트래픽이 급증하는 경우에는 기존 게이트웨이 인스턴스가 스트레스를 받을 수 있으며 결과적으로 예기치 않은 대기 시간 또는 트래픽 손실이 발생할 수 있습니다. 

최소 인스턴스 수는 최적 수준으로 설정하는 것이 좋습니다. 예를 들어 최대 로드에서 트래픽을 처리하는 데 50 인스턴스가 필요한 경우 트래픽 급증이 발생하더라도 Application Gateway가 해당 트래픽을 처리하고 자동 스케일링 기능이 응답하고 효과를 발휘하기에 충분한 시간을 제공하도록 최소 인스턴스를 10 이하로 설정하기보다는 25에서 30 사이로 설정하는 것이 좋습니다.

지난 한 달간 발생한 컴퓨팅 단위 메트릭을 확인합니다. 컴퓨팅 단위 메트릭은 게이트웨이의 CPU 사용률을 표시하며, 최고 사용량을 10으로 나눈 값을 기준으로 필요한 최소 인스턴스 수를 설정할 수 있습니다. 1개의 애플리케이션 게이트웨이 인스턴스는 최소 10개의 컴퓨팅 단위를 처리할 수 있습니다.

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="V2 컴퓨팅 단위 메트릭" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Application Gateway v2 SKU에 대한 수동 스케일링(표준_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>최대 컴퓨팅 단위 사용량을 기준으로 인스턴스 수 설정 

자동 스케일링과 달리 수동 스케일링에서는 트래픽 요구 사항에 따라 애플리케이션 게이트웨이의 인스턴스 수를 수동으로 설정해야 합니다. 트래픽 급증을 고려하여 최대 사용량에 따라 10%에서 20%까지 추가 버퍼를 사용하여 인스턴스 수를 설정하는 것이 좋습니다. 예를 들어 트래픽이 최대 50 인스턴스를 필요로 하는 경우 55에서 60 인스턴스를 프로비저닝하여 발생 가능한 예기치 않은 트래픽 급증을 처리할 수 있도록 합니다.

지난 한 달간 발생한 컴퓨팅 단위 메트릭을 확인합니다. 컴퓨팅 단위 메트릭은 게이트웨이의 CPU 사용률을 표시하고 최고 사용량을 10으로 나눈 값을 기반으로 하며, 1개 애플리케이션 게이트웨이 인스턴스가 최소 10개의 컴퓨팅 단위를 처리할 수 있음을 고려하여 필요한 인스턴스 수를 설정할 수 있습니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고

트래픽 또는 사용량의 변칙에 대한 알림을 받으려면 특정 메트릭에 대한 경고를 설정하면 됩니다. Application Gateway에서 제공하는 메트릭의 전체 목록은 [메트릭 설명서](./application-gateway-metrics.md)를 참조하세요. 메트릭에 대한 경고를 설정하는 방법에 대한 자세한 내용은 Azure Portal의 [메트릭 시각화](./application-gateway-metrics.md#metrics-visualization) 및 [Azure Monitor 설명서](../azure-monitor/alerts/alerts-metric.md)를 참조하세요.

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Application Gateway v1 SKU에 대한 경고(표준/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>평균 CPU 사용률이 80%를 초과하는 경우 경고

정상적인 조건에서 CPU 사용량은 Application Gateway 뒤에 호스트되는 웹 사이트에서 대기 시간이 발생하고 클라이언트 경험에 방해가 될 수 있으므로 일반적으로 90%를 초과해서는 안 됩니다. 인스턴스 수를 늘리거나, 보다 큰 SKU 크기로 이동하거나, 해당 작업을 둘 다 수행하여 Application Gateway 구성을 수정함으로써 CPU 사용률을 간접적으로 컨트롤하거나 향상시킬 수 있습니다. CPU 사용률 메트릭이 평균 80%를 초과하는 경우 경고를 설정합니다.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>비정상 호스트 수가 임계값을 초과하는 경우 경고

해당 메트릭은 애플리케이션 게이트웨이에서 제대로 프로브할 수 없는 백 엔드 서버 수를 나타냅니다. 해당 메트릭을 통해 Application Gateway 인스턴스가 백 엔드에 연결할 수 없는 문제를 파악할 수 있습니다. 해당 수가 백 엔드 용량의 20%를 초과하면 경고를 표시합니다. 예: 현재 백 엔드 풀에 30개의 백 엔드 서버가 있는 경우 비정상 호스트 수가 6개를 초과하면 경고를 설정합니다.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>응답 상태(4xx, 5xx)가 임계값을 초과하는 경우 경고 

Application Gateway 응답 상태가 4xx 또는 5xx인 경우 경고를 만듭니다. 일시적인 문제 때문에 가끔씩 4xx 또는 5xx 응답이 표시될 수 있습니다. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.

### <a name="alert-if-failed-requests-crosses-threshold"></a>실패한 요청이 임계값을 초과하는 경우 경고 

실패한 요청 메트릭이 임계값을 초과하는 경우 경고를 만듭니다. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>예: 지난 5분 동안 100개 이상의 실패한 요청에 대한 경고 설정

해당 예제에서는 Azure Portal를 사용하여 지난 5분 동안 실패한 요청 수가 100를 초과하는 경우 경고를 설정하는 방법을 보여 줍니다.
1. **Application Gateway** 로 이동합니다.
2. 왼쪽 패널의 **모니터링** 탭에서 **메트릭** 을 선택합니다. 
3. **실패한 요청** 에 대한 메트릭을 추가합니다.
4. **새 경고 규칙** 을 클릭하고 조건 및 작업을 정의합니다.
5. **경고 규칙 만들기** 를 클릭하여 경고를 만들고 사용하도록 설정합니다.

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 경고 만들기" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Application Gateway v2 SKU에 대한 경고(표준_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>컴퓨팅 단위 사용률이 평균 사용량의 75%를 초과하는 경우 경고 

컴퓨팅 단위는 Application Gateway 컴퓨팅 사용률을 측정한 것입니다. 지난 한 달간의 평균 컴퓨팅 단위 사용량을 확인하고 75%를 초과하는 경우 경고를 설정합니다. 예를 들어 평균 사용량의 컴퓨팅 단위가 10인 경우 7.5CU에 대한 경고를 설정합니다. 사용량이 늘어나면 경고하여 사용자에게 대응할 시간을 줍니다. 트래픽이 증가할 수 있음을 경고하기 위해 이 트래픽이 지속되는 것으로 판단되는 경우 최솟값을 늘릴 수 있습니다. 위의 스케일링 제안에 따라 필요에 따라 스케일 아웃합니다.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>예제: 평균 CU 사용량 75%에 대해 경고 설정

이 예제에서는 평균 CU 사용량의 75%에 도달할 때 Azure Portal을 사용하여 경고를 설정하는 방법을 보여 줍니다. 
1. **Application Gateway** 로 이동합니다.
2. 왼쪽 패널의 **모니터링** 탭에서 **메트릭** 을 선택합니다. 
3. **평균 현재 컴퓨팅 단위** 에 대한 메트릭을 추가합니다. 
4. 최소 인스턴스 수를 평균 CU 사용량으로 설정한 경우 계속 진행하여 최소 인스턴스 중 75%가 사용 중일 때 경고를 설정합니다. 예를 들어 평균 사용량이 10 CU인 경우 7.5CU에 대한 경고를 설정합니다. 사용량이 늘어나면 경고하여 사용자에게 대응할 시간을 줍니다. 트래픽이 증가할 수 있음을 경고하기 위해 이 트래픽이 지속되는 것으로 판단되는 경우 최솟값을 늘릴 수 있습니다. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 컴퓨팅 단위 경고" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> 잠재적인 트래픽 급증에 대한 심각도에 따라 더 낮거나 더 높은 CU 사용률에서 경고가 발생하도록 설정할 수 있습니다.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>용량 단위 사용률이 최고 사용량의 75%를 초과하는 경우 경고 

용량 단위는 처리량, 컴퓨팅 및 연결 수 면에서 전체 게이트웨이 사용률을 나타냅니다. 지난 한 달간의 최대 용량 단위 사용량을 확인하고 해당 사용량의 75%를 초과하는 경우 경고를 설정합니다. 예를 들어 최대 사용량이 100CU인 경우 75CU에 경고를 설정합니다. 필요한 경우 위의 두 제안에 따라 스케일 아웃합니다.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>비정상 호스트 수가 임계값을 초과하는 경우 경고 

해당 메트릭은 애플리케이션 게이트웨이에서 제대로 프로브할 수 없는 백 엔드 서버 수를 나타냅니다. 해당 메트릭을 통해 Application Gateway 인스턴스가 백 엔드에 연결할 수 없는 문제를 파악할 수 있습니다. 해당 수가 백 엔드 용량의 20%를 초과하면 경고를 표시합니다. 예: 현재 백 엔드 풀에 30개의 백 엔드 서버가 있는 경우 비정상 호스트 수가 6개를 초과하면 경고를 설정합니다.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>응답 상태(4xx, 5xx)가 임계값을 초과하는 경우 경고 

Application Gateway 응답 상태가 4xx 또는 5xx인 경우 경고를 만듭니다. 일시적인 문제 때문에 가끔씩 4xx 또는 5xx 응답이 표시될 수 있습니다. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.

### <a name="alert-if-failed-requests-crosses-threshold"></a>실패한 요청이 임계값을 초과하는 경우 경고 

실패한 요청 메트릭이 임계값을 초과하는 경우 경고를 만듭니다. 프로덕션에서 게이트웨이를 관찰하여 정적 임계값을 확인하거나 경고에 대한 동적 임계값을 사용해야 합니다.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>백 엔드의 마지막 바이트 응답 시간이 임계값을 초과하는 경우 경고 

해당 메트릭은 백 엔드 서버와 연결을 설정하기 시작한 시간과 응답 본문으로부터 마지막 바이트를 수신한 시간까지의 간격을 표시합니다. 백 엔드 응답 대기 시간이 일반적인 특정 임계값보다 더 많은 경우 경고를 만듭니다. 예를 들어 백 엔드 응답 대기 시간이 일반 값의 30% 이상 증가하는 경우 경고를 표시하도록 설정합니다.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Application Gateway 총 시간이 임계값을 초과하는 경우 경고

Application Gateway가 HTTP 요청의 첫 번째 바이트를 받은 시점부터 마지막 응답 바이트가 클라이언트로 전송된 시간까지의 간격입니다. 백 엔드 응답 대기 시간이 일반적인 특정 임계값보다 더 많은 경우 경고를 생성해야 합니다. 예를 들어 총 시간의 대기 시간이 일반 값의 30% 이상 증가하는 경우 경고가 표시되도록 설정할 수 있습니다.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>지역 필터링 및 봇 보호에서 공격을 중지하도록 WAF 설정
애플리케이션 앞에 추가 보안 계층이 필요한 경우 WAF용 Application Gateway WAF_v2 SKU 기능을 사용합니다. 지정된 국가/지역에서만 애플리케이션에 대한 액세스를 허용하도록 v2 SKU를 구성할 수 있습니다. 지리적 위치에 따라 트래픽을 명시적으로 허용하거나 차단하도록 WAF 사용자 지정 규칙을 설정합니다. 자세한 내용은 [지역 필터링 사용자 지정 규칙](../web-application-firewall/ag/geomatch-custom-rules.md) 및 [PowerShell을 통해 Application Gateway WAF_v2 SKU에서 사용자 지정 규칙을 구성하는 방법](../web-application-firewall/ag/configure-waf-custom-rules.md)을 참조하세요.

봇 방지를 사용하도록 설정하여 알려진 잘못된 봇을 차단합니다. 이렇게 하면 애플리케이션에 도달하는 트래픽 양이 줄어듭니다. 자세한 내용은 [설정에 따른 봇 보호 지침](../web-application-firewall/ag/configure-waf-custom-rules.md)을 참조하세요.

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Application Gateway 및 WAF에서 진단 켜기

진단 로그를 사용하여 방화벽 로그, 성능 로그 및 액세스 로그를 볼 수 있습니다. Azure에서 이러한 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 자세한 내용은 [진단 설명서](./application-gateway-diagnostics.md#diagnostic-logging)를 참조하세요. 

## <a name="set-up-an-tls-policy-for-extra-security"></a>추가 보안을 위해 TLS 정책 설정
최신 TLS 정책 버전([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s))을 사용하고 있는지 확인합니다. 이를 통해 TLS 1.2 이상의 강력한 암호가 적용됩니다. 자세한 내용은 [PowerShell을 통해 TLS 정책 버전 및 암호 그룹 구성](./application-gateway-configure-ssl-policy-powershell.md)을 참조하세요.