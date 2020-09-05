---
title: Azure Monitor 로그의 사용량 및 비용 관리
description: Azure Monitor에서 Log Analytics 작업 영역에 대한 가격 책정 요금제를 변경하고 데이터 볼륨 및 보존 정책을 관리하는 방법을 알아봅니다.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 84a5b1cd7b2229defd4e38a227f75cfbf9ebdd95
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933667"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 사용량 및 비용 관리    

> [!NOTE]
> 이 문서에서는 Azure Monitor 로그의 비용을 이해하고 관리하는 방법을 설명합니다. 관련 문서인 [사용량 및 예상 비용 모니터링](usage-estimated-costs.md)에서는 다른 가격 책정 모델에 대해 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다. 이 문서에 표시된 모든 가격과 비용은 예시 목적으로만 제시한 것입니다. 

Azure Monitor 로그는 Azure에 배포되거나 회사의 모든 원본에서 매일 대량의 데이터를 수집, 인덱싱, 저장할 때 크기 조정 및 지원을 할 수 있도록 설계됐습니다.  조직에 대한 주 드라이버인 반면 비용 효율성은 궁극적으로 기본 드라이버입니다. 그 목적을 위해서는 Log Analytics 작업 영역의 비용이 수집된 데이터 볼륨만을 기반으로 하지 않고 선택한 계획에 따라 달라지며, 연결된 소스에서 생성된 데이터를 저장하려고 선택한 기간을 이해하는 것이 중요합니다.  

이 문서에서는 수집된 데이터 볼륨 및 스토리지 증가를 사전에 모니터링하는 방법 및 해당 관련 비용을 제어하려면 제한을 정의하는 방법을 검토합니다. 

## <a name="pricing-model"></a>가격 책정 모델

Log Analytics에 대한 기본 가격은 수집된 데이터 볼륨을 기반으로 하며 필요에 따라 데이터를 더 오래 보존하기 위한 **종량제** 모델에 속합니다. 데이터 볼륨은 GB로 저장 되는 데이터의 크기 (10 ^ 9 바이트)로 측정 됩니다. 각 Log Analytics 작업 영역은 별도의 서비스로 요금이 부과되며 Azure 구독에 대한 청구서에 추가됩니다. 데이터 수집량은 다음의 제반 요인에 따라 상당 부분 달라질 수 있습니다. 

  - 사용하도록 설정된 관리 솔루션 수와 구성
  - 모니터링된 VM의 수
  - 모니터링된 각 VM에서 수집된 데이터 형식 
  
종량제 모델 외에도 Log Analytics에는 종량제 가격에 비해 최대 25%의 비용을 절약할 수 있는 **용량 예약** 계층이 있습니다. 용량 예약 가격 책정을 통해 일일 100GB로 시작되는 예약을 구매할 수 있습니다. 예약 수준을 초과하는 모든 사용량에 대한 요금은 종량제 요율로 청구됩니다. 용량 예약 계층은 31일의 약정 기간이 있습니다. 약정 기간 동안에는 더 높은 수준의 용량 예약 계층(31일의 약정 기간을 다시 시작함)으로 변경할 수 있으며 다만 약정 기간이 완료될 때까지 종량제 또는 더 낮은 용량 예약 계층으로 다시 이동할 수는 없습니다. 용량 예약 계층에 대한 요금은 일일 기준으로 청구됩니다. Log Analytics 종량제 및 용량 예약 가격에 대해 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/). 

모든 가격 책정 계층에서 이벤트의 데이터 크기는이 이벤트에 대 한 Log Analytics에 저장 된 속성의 문자열 표현에서 계산 됩니다. 데이터는 에이전트에서 전송 되거나 수집 프로세스 중에 추가 되는지 여부가 결정 됩니다. 여기에는 데이터가 수집 된 후 Log Analytics 저장 될 때 추가 되는 [사용자 지정 필드가](custom-fields.md) 포함 됩니다. 일부 [Log Analytics 표준 속성](log-standard-properties.md)을 비롯 하 여 모든 데이터 형식에 공통적인 몇 가지 속성은 이벤트 크기 계산에서 제외 됩니다. 여기에는,, `_ResourceId` 및가 포함 됩니다 `_ItemId` `_IsBillable` `_BilledSize` `Type` . Log Analytics에 저장 된 다른 모든 속성은 이벤트 크기 계산에 포함 됩니다. 일부 데이터 형식에는 AzureActivity, 하트 비트 및 사용 유형과 같은 데이터 수집 요금을 모두 사용할 수 없습니다. 데이터 수집에 대 한 청구에서 이벤트가 제외 되었는지 여부를 확인 하려면 `_IsBillable` [아래](#data-volume-for-specific-events)와 같이 속성을 사용 하면 됩니다. 사용량은 GB 단위로 보고 됩니다 (1.0 E9 바이트). 

또한 [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) 및 [구성 관리](https://azure.microsoft.com/pricing/details/automation/) 같은 일부 솔루션에는 저마다 고유한 가격 책정 모델이 있습니다. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 전용 클러스터

Log Analytics 전용 클러스터는 단일 관리형 Azure Data Explorer 클러스터로 수집된 작업 영역들의 컬렉션이며, [고객 관리형 키](customer-managed-keys.md) 등의 고급 시나리오를 지원합니다.  Log Analytics 전용 클러스터는 종량제 가격에 비해 25% 할인된 요금으로 일일 1000GB로 시작되는 용량 예약 가격 책정 모델만 지원합니다. 예약 수준을 초과하는 모든 사용량에 대한 요금은 종량제 요율로 청구됩니다. 예약 수준을 늘린 후 클러스터 용량 예약은 31일의 약정 기간이 있습니다. 약정 기간 동안에는 용량 예약 수준을 낮출 수는 없지만 언제든지 늘릴 수는 있습니다. [Log Analytics 클러스터를 만들고](customer-managed-keys.md#create-cluster-resource) [이 클러스터에 작업 영역을 연결](customer-managed-keys.md#workspace-association-to-cluster-resource)하는 방법에 대해 자세히 알아보세요.  

클러스터 용량 예약 수준은 `Sku`아래의 `Capacity` 매개 변수를 사용하여 Azure Resource Manager를 통해 프로그래밍 방식으로 구성됩니다. `Capacity`는 GB 단위로 지정되며 일일 100GB의 증분 단위로 일일 1000GB 이상의 값을 가질 수 있습니다. 이 내용은 [고객이 관리 하는 키 Azure Monitor](customer-managed-keys.md#create-cluster-resource)에 자세히 설명 되어 있습니다. 클러스터에서 일일 2000GB를 초과하는 예약이 필요한 경우, [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com)에 문의하세요.

클러스터에서 사용 하기 위한 두 가지 모드의 요금 청구 방법이 있습니다. `billingType` [클러스터를 구성할](customer-managed-keys.md#cmk-management)때 매개 변수를 통해 지정할 수 있습니다. 두 모드는 다음과 같습니다. 

1. **Cluster**:이 경우 (기본값) 수집 데이터에 대 한 청구는 클러스터 수준에서 수행 됩니다. 클러스터의 일별 청구 금액을 계산하기 위해 클러스터에 연결된 각 작업 영역의 수집된 데이터 수량이 집계됩니다. [Azure Security Center](https://docs.microsoft.com/azure/security-center/)에서 노드별 할당은 클러스터의 모든 작업 영역에서 집계된 데이터의 이러한 집계에 앞서 작업 영역 수준에서 적용됩니다. 

2. **작업 영역**: 클러스터의 용량 예약 비용은 클러스터의 작업 영역에 대 한 특성을 사용 합니다 (각 작업 영역에 대 한 노드 별 [Azure Security Center](https://docs.microsoft.com/azure/security-center/) 할당에 대 한 회계). 하루에 대 한 작업 영역에 수집 총 데이터 볼륨이 용량 예약 보다 작은 경우 각 작업 영역에는 용량 예약의 비율을 청구 하 여 해당 수집 데이터에 대 한 요금이 청구 되 고 용량 예약의 사용 되지 않는 부분은 클러스터 리소스로 청구 됩니다. 총 데이터 볼륨이 수집의 작업 영역으로 전환 되는 경우, 해당 일에 해당 하는 수집 데이터의 비율을 기준으로 각 작업 영역에 대 한 요금이 청구 되 고, 용량 예약 위에 있는 수집 데이터의 분수로 각 작업 영역이 청구 됩니다. 하루 동안 작업 영역으로 수집 총 데이터 볼륨이 용량 예약을 초과 하는 경우 클러스터 리소스에 대 한 요금이 청구 되지 않습니다.

클러스터 청구 옵션에서 데이터 보존은 작업 영역 수준에서 청구 됩니다. 클러스터 요금 청구는 작업 영역이 클러스터에 연결되었는지 여부에 관계 없이 클러스터를 만들 때 시작됩니다. 또한 클러스터에 연결 된 작업 영역에는 더 이상 가격 책정 계층이 없습니다.

## <a name="estimating-the-costs-to-manage-your-environment"></a>환경을 관리하는 데 드는 비용을 추정 

Azure Monitor 로그를 아직 사용하고 있지 않다면 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor)를 사용하여 Log Analytics를 사용하는 데 드는 비용을 추정할 수 있습니다. 먼저 검색 상자에 "Azure Monitor"를 입력한 후 화면에 표시되는 Azure Monitor 타일을 클릭합니다. Azure Monitor에 이를 때까지 페이지를 아래로 스크롤하여 유형 드롭다운에서 Log Analytics를 선택합니다.  이제 각 VM에서 수집하려는 VM 수와 데이터 GB를 여기에 입력하면 됩니다. 일반적으로 1~3GB의 데이터 월은 일반 Azure VM에서 수집됩니다. 이미 Azure Monitor 로그를 평가하고 있는 경우, 자신의 환경에서 데이터 통계를 사용할 수 있습니다. [모니터링되는 VM의 수](#understanding-nodes-sending-data)와 [작업 영역에서 수집 중인 데이터의 볼륨](#understanding-ingested-data-volume)을 확인하는 방법은 아래를 참조하세요. 

## <a name="understand-your-usage-and-estimate-costs"></a>사용량 파악 및 비용 추정

이제 Azure Monitor 로그를 사용하면 최근 사용 패턴에 기초하여 발생 가능한 비용을 쉽게 파악할 수 있습니다. 이렇게 하려면 **Log Analytics 사용량 및 예상 비용**을 사용하여 데이터 사용량을 검토하고 분석합니다. 이는 솔루션별로 수집되는 데이터양, 보유 중인 데이터양, 수집된 데이터양과 포함된 양을 초과하여 추가로 보유 중인 데이터양을 기준으로 한 추정 비용을 각각 표시합니다.

![사용량 및 예상 비용](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

데이터를 더 자세히 탐색하려면 **사용량 및 예상 비용** 페이지의 차트 중 하나의 오른쪽 상단 모서리에 있는 아이콘을 클릭합니다. 이제 이 쿼리에 대해 작업하여 사용량을 자세히 살펴볼 수 있습니다.  

![로그 보기](media/manage-cost-storage/logs.png)

**사용량 및 예상 비용** 페이지에서 해당 월의 데이터 볼륨을 검토할 수 있습니다. 여기에는 Log Analytics 작업 영역에서 받아서 보유 한 청구 가능한 데이터가 모두 포함 됩니다.  
 
Log Analytics 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Azure 청구서에서 Log Analytics 사용량 보기 

Azure는 [Azure Cost Management + 청구](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) 허브에서 많은 유용한 기능을 제공합니다. 예를 들어, "비용 분석" 기능을 사용하면 Azure 리소스에 대한 지출 내역을 확인할 수 있습니다. 먼저 "리소스 종류"에 따라 필터를 추가(Log Analytics의 경우 microsoft.operationalinsights/workspace에 추가, Log Analytics 클러스터의 경우 microsoft.operationalinsights/workspace에 추가)하면 Log Analytics 지출 내역을 추적할 수 있습니다. 그런 다음, "그룹화 방법"에서 "미터 범주" 또는 "미터"를 선택합니다.  Azure Security Center 및 Azure Sentinel과 같은 다른 서비스에서도 Log Analytics 작업 영역 리소스에 대한 사용량 요금을 청구합니다. 서비스 이름에 대한 매핑을 보기 위해 차트 대신 테이블 보기를 선택할 수 있습니다. 

사용량에 대한 자세한 내용은 [Azure Portal에서 사용량을 다운로드](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)하면 확인할 수 있습니다. 다운로드한 스프레드시트에서 Azure 리소스별(예: Log Analytics 작업 영역) 일일 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서는 먼저 "미터 범주"열에서 필터링을 진행하여 "Log Analytics", "Insight & Analytics"(일부 레거시 가격 책정 계층에서 사용됨) 및 "Azure Monitor"(용량 예약 가격 책정 계층에서 사용됨)를 각각 표시한 다음, "작업 영역 포함" 또는 "클러스터 포함"(후자의 경우, Log Analytics 클러스터 사용량을 포함)에 해당되는 "인스턴스 ID" 열에서 하나의 필터를 추가하면 Log Analytics 작업 영역의 사용량을 확인할 수 있습니다. 사용량은 "사용한 수량" 열에 표시되며 각 항목에 대한 단위는 "측정 단위" 열에 표시됩니다.  [Microsoft Azure 청구 방식을 이해](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)하는 데 도움이 되는 자세한 정보를 제공합니다. 

## <a name="changing-pricing-tier"></a>가격 책정 계층 변경

작업 영역의 Log Analytics 가격 책정 계층을 변경하려면 

1. Azure Portal에서 이 작업 영역에 사용할 수 있는 각 가격 책정 계층의 목록이 표시될 작업 영역에서 **사용량 및 예상 비용**을 엽니다.

2. 각 가격 책정 계층에 대한 예상 비용을 검토합니다. 이 예상치는 지난 31일간의 사용량을 기준으로 하므로 이 비용 예상치는 일반적인 사용량을 대표하는 지난 31일간의 사용량에 따라 달라집니다. 아래 예제에서는 지난 31일간의 데이터 패턴에 따라 이 작업 영역이 일일 100GB의 용량 예약 계층(#2)에 비해 종량제 계층(#1)에서 비용이 덜 드는 것을 알 수 있습니다.  

    ![가격 책정 계층](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 지난 31일간의 사용량을 기준으로 예상 비용을 검토한 후 가격 책정 계층을 변경하려면 **선택**을 클릭하세요.  

`sku` 매개 변수(Azure Resource Manager 템플릿의 `pricingTier`)를 사용하여 [Azure Resource Manager를 통해 가격 책정 계층을 설정](template-workspace-configuration.md#configure-a-log-analytics-workspace)할 수도 있습니다. 

## <a name="legacy-pricing-tiers"></a>레거시 가격 책정 계층

2018년 4월 2일 이전에 Log Analytics 작업 영역 또는 Application Insights 리소스가 있었거나 2019년 2월 1일 이전에 시작된 기업계약에 연결된 구독은 레거시 가격 책정 계층을 사용하기 위한 액세스 권한을 계속 갖게 됩니다. **무료**, **독립 실행형(GB당)** 및 **노드당(OMS)** .  무료 가격 책정 계층의 작업 영역에는 [Azure Security Center](https://docs.microsoft.com/azure/security-center/)에 의해 수집된 보안 데이터 형식을 제외하면 500MB로 제한된 일일 데이터 수집이 포함되며, 데이터 보존 기간은 7일로 제한됩니다. 무료 가격 책정 계층은 평가 목적으로만 사용됩니다. 독립 실행형 또는 노드당 가격 책정 계층의 작업 영역은 사용자 구성 가능 보존 기간이 30~730일입니다.

독립 실행형 가격 책정 계층에 대 한 사용량은 수집 데이터 볼륨으로 청구 됩니다. 이는 **Log Analytics** 서비스에서 보고 되 고 미터는 "데이터 분석 됨"으로 이름이 지정 됩니다. 

노드당 가격 책정 계층은 모니터링되는 VM(노드)당 1시간 단위로 요금이 청구됩니다. 모니터링되는 각 노드에 대한 작업 영역에는 일일 500MB의 데이터 용량이 할당되며 이에 대한 요금은 청구되지 않습니다. 이 할당은 작업 영역 수준에서 집계됩니다. 일일 총 데이터 할당량을 초과하는 데이터 수집량에 대해서는 GB당 데이터 초과분 요금이 청구됩니다. 작업 영역이 노드당 가격 책정 계층에 있는 경우, 청구서에 명시되는 해당 서비스는 Log Analytics 사용량에 대한 **Insight & Analytics**입니다. 사용량은 3 미터에 보고 됩니다.

1. 노드: 노드 * 개월 단위로 모니터링 되는 노드 (Vm)의 수를 사용 합니다.
2. 노드당 데이터 초과분: 집계 된 데이터 할당을 초과 하는 데이터 수집의 GB 수입니다.
3. 노드당 데이터 포함: 집계 된 데이터 할당에 포함 된 수집 데이터의 양입니다. 이 미터는 작업 영역이 모든 가격 책정 계층에 있는 경우에도 사용 되어 Azure Security Center에서 적용 되는 데이터의 양을 표시 합니다.

> [!TIP]
> 작업 영역에서 **노드당** 가격 책정 계층에 액세스할 수 있으며 다만 종량제 계층에서 비용이 더 저렴한지 여부가 궁금한 경우, [아래의 쿼리를 사용](#evaluating-the-legacy-per-node-pricing-tier)하면 권장 사항을 쉽게 확인할 수 있습니다. 

2016년 4월 이전에 만든 작업 영역은 데이터 보존 기간이 각각 30일 및 365일로 정해진 원래의 **표준** 및 **프리미엄** 가격 계층에 액세스할 수도 있습니다. **표준** 또는 **프리미엄** 가격 책정 계층에서는 새 작업 영역을 만들 수 없으며, 작업 영역을 이러한 계층 밖으로 옮기면 다시 이동할 수 없습니다. 이러한 레거시 계층에 대 한 데이터 수집 미터를 "데이터 분석" 이라고 합니다.

레거시 Log Analytics 계층의 사용과 [Azure Security Center](https://docs.microsoft.com/azure/security-center/)의 사용량에 대한 요금 청구 방식 간에도 몇 가지 동작이 있습니다. 

1. 작업 영역이 레거시 표준 또는 프리미엄 계층에 있는 경우, Azure Security Center는 노드당 아닌 Log Analytics 데이터 수집에 대해서만 요금이 청구됩니다.
2. 작업 영역이 레거시 노드당 계층에 있는 경우, Azure Security Center는 현재 [Azure Security Center 노드 기반 가격 책정 모델](https://azure.microsoft.com/pricing/details/security-center/)을 사용하여 요금이 청구됩니다. 
3. 그 외 가격 책정 계층(용량 예약을 포함)에서 Azure Security Center가 2017년 6월 19일 이전에 사용하도록 설정된 경우, Azure Security Center는 Log Analytics 데이터 수집에 대해서만 요금이 청구됩니다. 그렇지 않다면 Azure Security Center는 현재 Azure Security Center 노드 기반 가격 책정 모델을 사용하여 요금이 청구됩니다.

가격 책정 계층 제한 사항에 대 한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)에서 제공 됩니다.

모든 레거시 가격 책정 계층에는 지역 기반 가격이 없습니다.  

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

다음 단계에서는 로그 데이터가 작업 영역에 보존되는 기간을 구성하는 방법을 설명합니다. 데이터 보존 기간은 레거시 무료 가격 책정 계층을 사용하는 경우를 제외하면 모든 작업 영역에 대해 30~730일(2년)의 범위 내에서 구성할 수 있습니다. 이보다 더 긴 데이터 보존 기간의 가격 책정에 대해 [자세한 내용을 알아보세요](https://azure.microsoft.com/pricing/details/monitor/). 

### <a name="default-retention"></a>기본 보존 기간

작업 영역에 대한 기본 보존 기간을 설정하려면 
 
1. Azure Portal의 작업 영역에서 왼쪽 창에 있는 **사용량 및 예상 비용**을 선택합니다.
2. **사용량 및 예상 비용** 페이지의 상단에서 **데이터 보존**을 클릭합니다.
3. 창에서 슬라이더를 이동하여 일 수를 늘리거나 줄인 다음, **확인**을 클릭합니다.  *무료* 계층에서 작업 중인 경우 데이터 보존 기간을 수정할 수 없으며 이 설정을 제어하기 위해 유료 계층으로 업그레이드해야 합니다.

    ![작업 영역 데이터 보존 기간 설정 변경](media/manage-cost-storage/manage-cost-change-retention-01.png)

보존이 낮아질 때 새 보존 설정 보다 오래 된 데이터를 제거 하기 전에 몇 일의 유예 기간이 있습니다. 

`retentionInDays` 매개 변수를 사용하여 [Azure Resource Manager를 통해 보존 기간을 설정](template-workspace-configuration.md#configure-a-log-analytics-workspace)할 수도 있습니다. 데이터 보존 기간을 30 일로 설정 하는 경우 매개 변수를 사용 하 여 오래 된 데이터의 즉시 제거를 트리거할 수 있습니다 `immediatePurgeDataOn30Days` (몇 일 유예 기간 제거). 이는 즉각적인 데이터 제거가 필수적인 경우 준수 관련 시나리오에 유용할 수 있습니다. 이 즉시 제거 기능은 Azure Resource Manager 통해서만 노출 됩니다. 

30 일 보존이 있는 작업 영역은 실제로 31 일 동안 데이터를 보존할 수 있습니다. 데이터를 30 일 동안 유지 해야 하는 경우에는 Azure Resource Manager를 사용 하 여 보존 기간을 30 일로 설정 하 고 `immediatePurgeDataOn30Days` 매개 변수를 사용 합니다.  

`Usage` 및 `AzureActivity`의 두 데이터 형식은 기본적으로 최소 90일 동안 보존되며, 이 90일의 보존 기간에 대한 요금은 청구되지 않습니다. 작업 영역 보존 기간이 90일 넘게 증가하는 경우, 이러한 데이터 형식의 보존 기간도 증가하게 됩니다.  이러한 데이터 형식에 대해서도 데이터 수집 요금이 부과되지 않습니다. 

작업 영역을 기반으로 한 Application Insights 리소스의 데이터 형식(`AppAvailabilityResults`, `AppBrowserTimings`, `AppDependencies`, `AppExceptions`, `AppEvents`, `AppMetrics`, `AppPageViews`, `AppPerformanceCounters`, `AppRequests`, `AppSystemEvents` 및 `AppTraces`)은 기본적으로 90일 동안 보존되며 이러한 90일의 보존 기간에 대한 요금은 청구되지 않습니다. 이들 데이터 형식의 보존 기간은 데이터 형식별 보존 기간 기능을 적용하여 조정할 수 있습니다. 

Log Analytics [제거 API](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge) 는 보존 요금에 영향을 주지 않으며 매우 제한적인 사례에 사용 하기 위한 것입니다. 보존 요금을 줄이려면 작업 영역 또는 특정 데이터 형식에 대 한 보존 기간을 줄여야 합니다. 

### <a name="retention-by-data-type"></a>데이터 형식별 보존 기간

또한 30~730일의 범위 내에서 개별 데이터 형식에 대해 상이한 보존 기간 설정(레거시 무료 가격 책정 계층의 작업 영역은 제외)을 지정할 수도 있습니다. 각 데이터 형식은 작업 영역의 하위 리소스에 속합니다. 예를 들어, 다음과 같이 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에서 SecurityEvent 테이블의 주소를 지정할 수 있습니다.

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

데이터 형식(테이블)은 대/소문자를 구분합니다.  특정 데이터 형식의 현재 데이터 형식당 보존 기간 설정(이 예제에서는 SecurityEvent)을 가져오려면 다음을 사용합니다.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

작업 영역에 있는 모든 데이터 형식에 대한 현재 데이터 형식당 보존 기간 설정을 가져오려면 특정 데이터 형식을 생략합니다. 예를 들면 다음과 같습니다.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

특정 데이터 형식(이 예제에서는 SecurityEvent)의 보존 기간을 730일로 설정하려면 다음을 수행합니다.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`retentionInDays`의 유효한 값은 30에서 730까지입니다.

사용자 지정 보존 기간을 적용하여 `Usage` 및 `AzureActivity` 데이터 형식을 설정할 수 없습니다. 이들 데이터 형식은 기본 작업 영역 보존 기간의 최대값 또는 90일을 사용합니다. 

데이터 형식별 보존 기간을 설정하기 위해 Azure Resource Manager에 직접 연결할 수 있는 유용한 도구는 OSS 도구인 [ARMclient](https://github.com/projectkudu/ARMClient)입니다.  ARMclient에 대한 자세한 내용은 [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 및 [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)가 작성한 문서를 참조하세요.  ARMClient를 사용하여 SecurityEvent 데이터의 보존 기간을 730일로 설정하는 예제는 다음과 같습니다.

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 개별 데이터 형식에 대한 보존 기간 설정을 사용하면 데이터 보존에 드는 비용을 줄일 수 있습니다.  2019년 10월(이 기능이 릴리스된 시점)부터 수집된 데이터의 경우, 일부 데이터 형식에 대한 보존 기간을 줄이면 시간이 경과함에 따라 보존 비용을 줄일 수 있습니다.  이전에 수집된 데이터의 경우, 개별 형식에 대한 보존 기간을 단축하여 설정하더라도 데이터 보존 비용은 이에 영향을 받지 않습니다.  

## <a name="manage-your-maximum-daily-data-volume"></a>일일 최대 데이터 볼륨 관리

작업 영역에 대한 일일 한도를 구성하고 일일 수집량을 제한할 수 있지만 목표치가 일일 한도에 도달하지 않도록 주의하십시오.  그렇지 않으면 남은 기간 동안의 데이터가 손실됩니다. 이는 해당 기능이 작업 영역에서 사용할 수 있는 최신 데이터에 의존할 수도 있는 다른 Azure 서비스 및 솔루션에 영향을 줄 수 있습니다.  결과적으로 리소스의 상태 조건이 IT 서비스를 지원할 때 경고를 관찰하고 수신하는 기능이 영향을 받습니다.  일일 한도는 관리 되는 리소스에서 **예기치 않은** 데이터 볼륨 증가를 관리 하 고 한도 내로 유지 하거나 작업 영역에 대 한 계획 되지 않은 요금을 제한 하려는 경우에 사용 됩니다. 작업 영역에서 일일 상한에 매일 도달하도록 설정하는 것은 적절하지 않습니다.

각 작업 영역에는 하루 중 다른 시간에 적용 된 일일 상한이 있습니다. Reset 시간은 **일일 단면** 페이지 (아래 참조)에 표시 됩니다. 이 다시 설정 시간을 구성할 수 없습니다. 

일일 한도에 도달하면 하루의 나머지 시간 동안 청구 가능한 데이터 형식의 수집이 즉시 중지됩니다. 일일 한도를 적용 하는 기본적인 대기 시간은 지정 된 일일 상한 수준에서 한도를 정확 하 게 적용 하지 않음을 의미 합니다. 선택된 Log Analytics 작업 영역에 대한 페이지의 상단에 경고 배너가 표시되고 작업 이벤트가 **LogManagement** 범주 아래의 *작업* 테이블로 전송됩니다. *일일 한도 아래 정의된 재설정 시간이* 로 설정된 후 데이터 수집이 다시 시작합니다. 일일 데이터 한도에 도달했을 때 알려주도록 구성된 이 작업 이벤트를 기반으로 경고 규칙을 정의하는 것이 좋습니다. 

> [!NOTE]
> 작업 영역에서 많은 양의 데이터를 수신 하는 경우에는 지정 된 cap 수준 및 일부 초과 데이터를 정확 하 게 사용할 수 있으므로 일일 한도는 데이터 수집을 중지할 수 없습니다.  

> [!WARNING]
> 2017 년 6 월 19 일 이전에 Azure Security Center 설치 된 작업 영역을 제외 하 고, 일일 한도는 Azure Sentinal 또는 Azure Security Center에서 데이터 수집을 중지 하지 않습니다. 

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

데이터 수집 추세 및 정의할 일일 볼륨 한도를 이해하려면 [Log Analytics 사용량 및 예상 비용](usage-estimated-costs.md)을 검토합니다. 한도에 도달한 후에는 리소스를 모니터링할 수 없으므로 신중하게 고려해야 합니다. 

### <a name="set-the-daily-cap"></a>일일 상한 설정

다음 단계에서는 Log Analytics 작업 영역이 매일 수집하는 데이터의 볼륨 관리 제한을 구성하는 방법을 설명합니다.  

1. 작업 영역의 왼쪽 창에서 **사용량 및 예상 비용**을 선택합니다.
2. 선택한 작업 영역에 대 한 **사용량 및 예상 비용** 페이지에서 페이지 위쪽의 **데이터 상한** 을 클릭 합니다. 
3. 일일 상한은 **OFF**로 기본 설정되나요? 일일 상한을 사용하도록 설정하려면 **ON**을 클릭한 다음, 데이터 볼륨 한도를 GB/일 단위로 설정합니다.

    ![Log Analytics 데이터 제한 구성](media/manage-cost-storage/set-daily-volume-cap-01.png)
    
`dailyQuotaGb` `WorkspaceCapping` [작업 영역-만들기 또는 업데이트](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)에서 설명한 대로 매개 변수를 설정 하 여 ARM을 통해 일일 캡을 구성할 수 있습니다. 

### <a name="alert-when-daily-cap-reached"></a>일일 상한에 도달 시 경고

데이터 제한 임계값에 도달하는 경우 Azure Portal에 시각적 큐를 표시하는 반면, 이 동작은 즉각적인 주의가 필요한 운영 문제를 관리하는 방법에 맞출 필요는 없습니다.  경고 알림을 수신하려면 Azure Monitor에서 새 경고 규칙을 만들 수 있습니다.  자세한 내용은 [경고를 만들고 보고 관리하는 방법](alerts-metric.md)을 참조하세요.

시작하려면 경고에 대한 권장 설정은 다음과 같습니다.

- 대상: Log Analytics 리소스 선택
- 조건: 
   - 신호 이름: 사용자 지정 로그 검색
   - 검색 쿼리: 작업 | 세부 정보에 'OverQuota'가 있는 경우
   - 기준: 결과의 수
   - 조건: 초과
   - 임계값: 0
   - 기간: 5분
   - 빈도: 5분
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)

경고가 정의되고 한계에 도달하면 경고가 트리거되고 작업 그룹에서 정의된 응답을 수행합니다. 이메일 및 텍스트 메시지를 통해 팀에 알리거나 웹후크, Automation Runbook 또는 [외부 ITSM 솔루션을 사용해 통합](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)을 사용하여 작업을 자동화할 수 있습니다. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>사용량이 예상보다 더 높은 원인 해결

사용량이 높은 원인은 다음과 같습니다.
- 예상보다 많은 노드가 Log Analytics 작업 영역에 데이터를 전송
- Log Analytics 작업 영역으로 전송되는 데이터가 예상보다 많음(새 솔루션 사용 시작 또는 기존 솔루션에 대한 구성 변경이 원인일 수 있음)

## <a name="understanding-nodes-sending-data"></a>데이터를 전송하는 노드를 파악

지난 달에 매일 에이전트에서 하트비트를 보고하는 노드의 수를 파악하려면 다음을 사용합니다.

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
지난 24시간 내 데이터를 전송하는 노드의 수를 가져오려면 다음과 같이 쿼리를 사용합니다. 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

데이터를 전송하는 노드 목록(및 각 노드에서 전송된 데이터의 양)을 가져오려면 다음 쿼리를 사용하세요.

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> 여러 데이터 형식을 검색하면 실행 시 [많은 리소스가 필요](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)하기 때문에 이러한 `find` 쿼리는 자주 사용하지 않도록 합니다. **컴퓨터별** 결과가 필요하지 않은 경우, 사용량 데이터 형식(아래 참조)을 쿼리합니다.

## <a name="understanding-ingested-data-volume"></a>수집된 데이터 볼륨을 파악

**사용량 및 예상 비용** 페이지에서 *솔루션당 데이터 수집* 차트는 전송된 총 데이터 양과 각 솔루션이 전송하는 데이터 양을 보여 줍니다. 이를 통해 전체 데이터 사용량(또는 특정 솔루션에 의한 사용량)이 증가하는지, 고정적인지, 감소하는지 여부의 추세를 판단할 수 있습니다. 

### <a name="data-volume-for-specific-events"></a>특정 이벤트에 대한 데이터 볼륨

특정 이벤트 집합에 대해 수집된 데이터의 크기를 확인하려면 특정 테이블(이 예제에서는 `Event`)을 쿼리한 다음, 관련 이벤트(이 예제에서는 이벤트 ID 5145 또는 5156)로 쿼리를 제한합니다.

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

여기서 `where _IsBillable = true` 절은 수집 비용이 없는 특정 솔루션에서 데이터 형식을 필터링합니다. 에 [대해 자세히 알아보세요](log-standard-properties.md#_isbillable) `_IsBillable` .

### <a name="data-volume-by-solution"></a>솔루션별 데이터 볼륨

지난 한 달 동안(마지막 1일 미만의 시간은 제외) 솔루션별로 청구 가능한 데이터 볼륨을 보는 데 사용되는 쿼리는 다음과 같습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

`TimeGenerated`를 사용하는 절은 Azure Portal의 쿼리 환경이 기본값인 24시간을 초과한 기간을 되돌아보는지 확인하는 용도로만 사용됩니다. 사용량 데이터 형식을 사용하는 경우, `StartTime` 및 `EndTime`은 결과가 표시되는 시간 버킷을 나타냅니다. 

### <a name="data-volume-by-type"></a>형식별 데이터 볼륨

데이터 형식별 데이터 추세를 확인하기 위해 다음과 같이 더 자세한 내용을 다룰 수 있습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

또는 지난 한 달 동안 솔루션 및 형식별로 테이블을 표시하려면

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>컴퓨터별 데이터 볼륨

`Usage` 데이터 형식에는 컴퓨터 수준의 정보가 포함되지 않습니다. 컴퓨터당 수집된 데이터의 **크기**를 보려면 바이트 단위의 크기를 제공하는 `_BilledSize` [ 속성](log-standard-properties.md#_billedsize)을 사용합니다.

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [속성](log-standard-properties.md#_isbillable)은 수집된 데이터에서 요금이 발생하는지 여부를 지정합니다. 

컴퓨터당 청구 가능한 이벤트 **수**를 보려면 다음을 사용합니다. 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> 여러 데이터 형식을 검색하면 실행 시 [많은 리소스가 필요](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)하기 때문에 이러한 `find` 쿼리는 자주 사용하지 않도록 합니다. **컴퓨터별** 결과가 필요하지 않은 경우, 사용량 데이터 형식을 쿼리합니다.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure 리소스, 리소스 그룹 또는 구독별 데이터 볼륨

Azure에서 호스트되는 노드의 데이터에 대해서는 __컴퓨터별__로 수집된 데이터의 **크기**를 가져올 수 있으며 _ResourceId [속성](log-standard-properties.md#_resourceid)을 사용할 수 있는데 이는 리소스에 대한 전체 경로를 제공합니다.

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Azure에서 호스트 되는 노드의 데이터의 경우 __azure 구독 당__수집 데이터의 **크기** 를 가져올 수 있습니다. 구독 ID는 `_ResourceId` 다음과 같이 가져옵니다.

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend subscriptionId = tostring(split(_ResourceId, "/")[2]) 
| summarize BillableDataBytes = sum(BillableDataBytes) by subscriptionId | sort by BillableDataBytes nulls last
```

마찬가지로, 리소스 그룹별로 데이터 볼륨을 가져오려면 다음을 수행 합니다.

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

또한를 `_ResourceId` 사용 하 여 필요한 경우 보다 완전 한 구문을 분석할 수 있습니다.

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 여러 데이터 형식을 검색하면 실행 시 [많은 리소스가 필요](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane)하기 때문에 이러한 `find` 쿼리는 자주 사용하지 않도록 합니다. 구독별, 리소스 그룹별 또는 리소스 이름별 결과가 필요하지 않다면 사용량 데이터 형식에 대해 쿼리합니다.

> [!WARNING]
> 사용량 데이터 형식의 일부 필드가 여전히 스키마에 있지만 더 이상 사용되지 않으며 해당 값은 더 이상 채워지지 않습니다. 이는 **컴퓨터**일 뿐 아니라 수집과 관련된 필드(**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** 및 **AverageProcessingTimeMs**)이기도 합니다.


### <a name="querying-for-common-data-types"></a>공통 데이터 형식에 대한 쿼리

특정 데이터 형식의 데이터 소스를 더 자세히 알아보려면 다음 예와 같은 몇 가지 쿼리를 사용합니다.

+ **작업 영역 기반 Application Insights** 리소스
  - [Application Insights 사용 및 비용 관리](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources) 에서 자세히 알아보세요.
+ **보안** 솔루션
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **로그 관리** 솔루션
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **성능** 데이터 형식
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **이벤트** 데이터 형식
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 데이터 형식
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 데이터 형식
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>데이터 볼륨을 줄이기 위한 팁

수집된 로그 볼륨을 줄이기 위한 몇 가지 제안 사항은 다음과 같습니다.

| 높은 데이터 볼륨의 소스 | 데이터 볼륨을 줄이는 방법 |
| -------------------------- | ------------------------- |
| 컨테이너 정보         | 필요한 데이터만 수집 하도록 [컨테이너 정보를 구성](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-cost#controlling-ingestion-to-reduce-cost) 합니다. |
| 보안 이벤트            | [일반 또는 최소한의 보안 이벤트](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)를 선택합니다. <br> 보안 감사 정책을 변경하여 필요한 이벤트만을 수집합니다. 특히, 다음 항목에 대한 이벤트를 수집할 필요를 검토합니다. <br> - [감사 필터링 플랫폼](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [감사 레지스트리](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [감사 파일 시스템](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [감사 커널 개체](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [감사 핸들 조작](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 이동식 저장소 감사 |
| 성능 카운터       | [성능 카운터 구성](data-sources-performance-counters.md)을 다음과 같이 변경합니다. <br> - 컬렉션의 빈도 감소 <br> - 성능 카운터의 수 감소 |
| 이벤트 로그                 | [이벤트 로그 구성](data-sources-windows-events.md)을 다음과 같이 변경합니다. <br> - 수집된 이벤트 로그의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 수준 이벤트를 수집하지 않습니다. |
| syslog                     | [syslog 구성](data-sources-syslog.md)을 다음과 같이 변경합니다. <br> - 수집된 기능의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 및 *디버그* 수준 이벤트를 수집하지 않습니다. |
| AzureDiagnostics           | 다음 작업을 수행하도록 리소스 로그 컬렉션을 변경합니다. <br> - Log Analytics로 보내는 리소스 송신 로그의 수 축소 <br> - 필요한 로그만 수집 |
| 솔루션을 사용하지 않는 컴퓨터의 솔루션 데이터 | [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>노드당 가격 책정 계층에서 청구되는 노드를 가져오기

작업 영역이 레거시 노드당 가격 책정 계층에 있는 경우, 노드로 요금이 청구되는 컴퓨터 목록을 가져오려면 **청구된 데이터 형식**(일부 데이터 형식은 무료)을 전송하는 노드를 찾습니다. 이렇게 하려면 `_IsBillable` [속성](log-standard-properties.md#_isbillable)을 사용하고 정규화된 도메인 이름(FQDN)의 맨 왼쪽 필드를 사용합니다. 이는 (노드 수를 계산하고 그 요금이 청구되는 단위인) 시간당 데이터 요금이 청구되는 컴퓨터의 수를 다음과 같이 반환합니다.

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>보안 및 Automation 노드의 수 가져오기

“노드별 (OMS)” 가격 책정 계층에 속하는 경우 사용하는 노드 및 솔루션 수에 따라 비용이 청구됩니다. 요금이 청구되는 Insights 및 Analytics 노드의 수는 **사용량 및 예상 비용** 페이지의 표에 표시됩니다.  

고유한 보안 노드의 수를 보려면 다음 쿼리를 사용할 수 있습니다.

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

고유한 Automation 노드의 수를 보려면 다음 쿼리를 사용합니다.

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>레거시 노드당 가격 책정 계층 평가

레거시 **노드당** 가격 책정 계층에 액세스할 수 있는 작업 영역이 해당 계층, 현재 **종량제**, **용량 예약** 계층 중 어느 곳에서 더 나은지에 대한 판단은 고객들이 평가하기가 어려운 경우가 많습니다.  여기에는 노드당 가격 책정 계층에서 모니터링되는 노드당 고정 비용과 이 계층에 포함된 일일 노드당 500MB의 데이터 할당량 그리고 종량제(GB당) 계층의 수집 데이터에 대한 요금 결제 비용 간에 절충값을 파악하는 과정이 포함됩니다. 

이러한 평가를 보다 수월하게 진행하기 위해 다음 쿼리를 사용하여 작업 영역의 사용 패턴을 기반으로 최적의 가격 책정 계층을 추천할 수 있습니다.  이 쿼리는 지난 7일 동안 작업 영역에서 모니터링된 노드 및 수집된 데이터를 각각 확인하며 각 날짜별로 가장 적합한 가격 책정 계층을 평가합니다. 쿼리를 사용하려면

1. 작업 영역에서 `workspaceHasSecurityCenter`를 `true` 또는 `false`로 설정하여 Azure Security Center를 사용하는지 여부를 지정하고, 
2. 특정 할인이 있는 경우 가격 데이터를 업데이트해야 하며
3. `daysToEvaluate`를 설정하여 다시 확인 및 분석할 일수를 지정해야 합니다. 이 방법은 쿼리가 7일간의 데이터를 확인하는 데 너무 많은 시간이 걸리는 경우에 도움이 됩니다. 

가격 책정 계층 권장 사항 쿼리는 다음과 같습니다.

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

이 쿼리는 사용량이 계산되는 방식을 정확하게 복제한 것은 아니지만 대개의 경우 가격 책정 계층 권장 사항을 제공하는 데 사용됩니다.  

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="create-an-alert-when-data-collection-is-high"></a>데이터 수집량이 많을 때 경고 만들기

이 섹션에서는 Azure Monitor [로그 경고](alerts-unified-log.md)를 사용 하 여 최근 24 시간 동안의 데이터 볼륨이 지정 된 크기를 초과 하는 경고를 만드는 방법에 대해 설명 합니다. 

최근 24 시간 동안의 청구 가능 데이터 볼륨이 50 GB 보다 수집 경고 하려면 다음 단계를 수행 합니다. 

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름**은 **로그 검색 사용자 지정**을 선택합니다.
   - **쿼리** 를 검색 `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` 합니다. 
   - **경고 논리**는 결과 수에 **기반**하고 **조건**은 *0*의 **임계값**을 초과합니다.
   - 하루에 한 번 실행 **하는 데** *1440* 분 마다 *1440* 분 및 **경고 빈도** 의 기간입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - *24 시간 동안 청구 가능한 데이터 볼륨 50 GB 보다 큰* **이름**
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

경고가 표시 되 면 사용 방법이 예상 보다 높은 이유를 해결 하는 방법에 대 한 위 섹션의 단계를 사용 합니다.

## <a name="data-transfer-charges-using-log-analytics"></a>Log Analytics를 사용하여 데이터 전송 요금 청구

Log Analytics로 데이터를 전송하면 데이터 대역폭 요금이 발생할 수 있습니다. [Azure Bandwidth 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)에 설명된 바와 같이, 두 지역의 Azure 서비스 간 데이터 전송 요금은 일반 요율을 적용한 아웃바운드 데이터 전송 요금으로 청구되었습니다. 인바운드 데이터 전송은 무료입니다. 다만 이 요금은 Log Analytics 데이터 수집에 드는 비용에 비해 매우 적습니다(수% 정도에 불과). 따라서 Log Analytics에 대 한 비용을 제어 하려면 [수집 데이터 볼륨](#understanding-ingested-data-volume)에 집중 해야 합니다. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics에서 더 이상 데이터를 수집하지 않는 문제 해결

레거시 무료 가격 책정 계층을 사용 중이고 하루에 500MB 이상의 데이터를 보낸 경우 남은 날 동안 데이터 수집이 중지됩니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다.  Log Analytics는 데이터 수집을 시작하고 중지할 때 Operation 형식의 이벤트를 만듭니다. 일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

데이터 수집이 중지되는 경우 OperationStatus가 **Warning**입니다. 데이터 수집이 시작되는 경우 OperationStatus가 **Succeeded**입니다. 다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.  

|수집 중지 이유| 해결 방법| 
|-----------------------|---------|
|작업 영역의 일일 상한에 도달함|수집이 자동으로 다시 시작될 때까지 대기하거나, 최대 일일 데이터 볼륨 관리의 설명처럼 일일 데이터 볼륨 한도를 늘립니다. 일일 상한 다시 설정 시간은 **일일 상한** 페이지에 표시 됩니다. |
| 작업 영역에서 데이터 수집 [볼륨 요금](https://docs.microsoft.com/azure/azure-monitor/service-limits#log-analytics-workspaces) 에 도달 했습니다. | 작업 영역에 적용되는 기본 수집 볼륨 속도 임계값은 압축된 경우 500MB이고, 압축되지 않은 경우에는 약 **6GB/분**입니다. 실제 크기는 로그 길이와 압축률에 따라 데이터 형식별로 다를 수 있습니다. 이 임계값은 [진단 설정](diagnostic-settings.md) [데이터 수집기 API](data-collector-api.md) 또는 에이전트를 사용하여 Azure 리소스에서 전송되었는지 여부에 관계없이 모든 수집 데이터에 적용됩니다. 작업 영역에 전송되는 데이터의 볼륨 속도가 작업 영역에 구성된 임계값의 80%를 초과할 경우 임계값을 계속 초과하는 동안 6시간마다 작업 영역의 *Operation* 테이블에 이벤트가 계속 전송됩니다. 수집 볼륨 속도가 임계값을 초과할 경우 일부 데이터가 삭제되고, 임계값을 계속 초과하는 동안 6시간마다 작업 영역의 *Operation* 테이블로 이벤트가 전송됩니다. 수집 볼륨 속도가 임계값을 계속 초과하거나 곧 도달할 것으로 예상되는 경우 지원 요청을 열어 작업 영역에 대한 임계값 상향 조정을 요청할 수 있습니다. 작업 영역에서 이러한 이벤트에 대해 알리려면 0 보다 큰 결과 수, 5 분의 평가 기간 및 5 분의 빈도를 기준으로 경고 논리를 사용 하 여 [로그 경고 규칙](alerts-log.md) 을 만듭니다. 수집 볼륨 비율이 임계값의 80%에 도달 `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The data ingestion volume rate crossed 80% of the threshold"` 했습니다. 수집 볼륨 비율이 임계값에 도달 `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The data ingestion volume rate crossed the threshold"` 했습니다. |
|레거시 무료 가격 책정 계층의 일일 한도에 도달함 |수집이 다음 날에 자동으로 다시 시작될 때까지 대기 또는 유료 가격 책정 계층으로 변경합니다.|
|Azure 구독이 다음으로 인해 일시 중단된 상태:<br> 평가판 종료<br> Azure 암호 만료<br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)|유료 구독으로 전환<br> 한도 제거 또는 한도가 재설정될 때까지 대기|

데이터 수집이 중지될 때 알림을 받으려면 데이터 수집 중지 시 알림을 받을 수 있도록 일별 데이터 상한 만들기 경고에 설명된 단계를 사용합니다. [작업 그룹 만들기](action-groups.md)에 설명된 단계를 사용하여 경고 규칙에 대한 전자 메일, 웹후크 또는 Runbook 작업을 구성합니다. 

## <a name="limits-summary"></a>제한 요약

몇 가지 추가적인 Log Analytics 제한이 있으며, 그 중 일부는 Log Analytics 가격 책정 계층에 따라 달라집니다. 이러한 [내용은 Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)에 설명 되어 있습니다.


## <a name="next-steps"></a>다음 단계

- 검색 언어를 사용하는 방법을 알아보려면 [Azure Monitor 로그에서 로그 검색](../log-query/log-query-overview.md)을 참조하세요. 사용량 현황 데이터에 대한 추가 분석을 수행하려면 검색 쿼리를 사용할 수 있습니다.
- [새 로그 경고 만들기](alerts-metric.md)에 설명한 단계를 사용하여 검색 기준이 충족되는 경우 알림을 받을 수 있습니다.
- [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다.
- [Azure Security Center 필터링 정책](../../security-center/security-center-enable-data-collection.md)을 검토하여 효과적인 이벤트 컬렉션 정책을 구성합니다.
- [성능 카운터 구성](data-sources-performance-counters.md)을 변경합니다.
- 이벤트 컬렉션 설정을 수정하려면 [이벤트 로그 구성](data-sources-windows-events.md)을 검토합니다.
- syslog 컬렉션 설정을 수정하려면 [syslog 구성](data-sources-syslog.md)을 검토합니다.
