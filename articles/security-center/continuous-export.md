---
title: SIEMs에 대 한 Azure Security Center 경고 및 권장 사항 내보내기 | Microsoft Docs
description: 이 문서에서는 SIEMs에 대 한 보안 경고 및 권장 사항의 연속 내보내기를 설정 하는 방법을 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: eb7f642e36bd72f963481cb392d7e3a6c2555816
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612387"
---
# <a name="export-security-alerts-and-recommendations"></a>보안 경고 및 권장 사항 내보내기

Azure Security Center은 자세한 보안 경고 및 권장 사항을 생성 합니다. 포털에서 또는 프로그래밍 방식 도구를 통해 볼 수 있습니다. 이 정보를 내보내거나 사용자 환경의 다른 모니터링 도구에 보내야 할 수도 있습니다. 

이 문서에서는 경고와 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있는 도구 집합을 설명 합니다.

이러한 도구를 사용 하 여 다음을 수행할 수 있습니다.

* Log Analytics 작업 영역으로 지속적으로 내보내기
* Azure Event Hubs로 계속 내보내기 (타사 SIEMs와 통합)
* CSV로 내보내기 (한 번)



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|일반 공급|
|결정|무료 계층|
|필요한 역할 및 사용 권한:|리소스 그룹 (또는 **소유자**)에 대 한 **보안 관리자 역할**<br>또한 대상 리소스에 대 한 쓰기 권한이 있어야 합니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov<br>![예](./media/icons/no-icon.png) 중국 .Gov, 기타 .Gov|
|||



## <a name="setting-up-a-continuous-export"></a>연속 내보내기 설정

Log Analytics 작업 영역 또는 Azure Event Hubs에 대 한 연속 내보내기를 설정 하 고 있는지 여부에 따라 다음 단계가 필요 합니다.

1. Security Center의 사이드바에서 **가격 책정 & 설정**을 선택 합니다.

1. 데이터 내보내기를 구성할 특정 구독을 선택 합니다.
    
1. 해당 구독에 대 한 설정 페이지의 사이드바에서 **연속 내보내기**를 선택 합니다.

    [ ![ 내보내기 옵션 Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) 내보내기 옵션을 볼 수 있습니다. 사용 가능한 각 내보내기 대상에 대 한 탭이 있습니다. 

1. 내보낼 데이터 형식을 선택 하 고 각 유형의 필터에서 선택 합니다 (예: 높은 심각도 경고만 내보내기).

1. "대상 내보내기" 영역에서 데이터를 저장 하려는 위치를 선택 합니다. 데이터는 다른 구독 (예: 중앙 이벤트 허브 인스턴스 또는 중앙 Log Analytics 작업 영역)의 대상에 저장할 수 있습니다.

1. **저장**을 선택합니다.


## <a name="setting-up-continuous-export-via-the-rest-api"></a>REST API를 통해 연속 내보내기 설정

Azure Security Center [자동화 API](https://docs.microsoft.com/rest/api/securitycenter/automations)를 통해 연속 내보내기 기능을 구성 하 고 관리할 수 있습니다. 이 API를 사용 하 여 다음 가능한 대상 중 하나로 내보내도록 자동화를 만들거나 업데이트할 수 있습니다.

- Azure Event Hub
- Log Analytics 작업 영역
- Azure Logic Apps 

API는 Azure Portal에서 사용할 수 없는 추가 기능을 제공 합니다. 예를 들면 다음과 같습니다.

* **큰 볼륨** -API를 사용 하면 단일 구독에서 여러 내보내기 구성을 만들 수 있습니다. Security Center의 포털 UI에서 **연속 내보내기** 페이지는 구독 당 하나의 내보내기 구성만 지원 합니다.

* **추가 기능** -API는 UI에 표시 되지 않는 추가 매개 변수를 제공 합니다. 예를 들어 automation 리소스에 태그를 추가 하 고, Security Center의 포털 UI에서 **연속 내보내기** 페이지에서 제공 되는 것 보다 광범위 한 경고 및 권장 사항 속성 집합을 기반으로 내보내기를 정의할 수 있습니다.

* **더 집중 된 범위** -API는 내보내기 구성의 범위에 대해 보다 세분화 된 수준을 제공 합니다. API를 사용 하 여 내보내기를 정의할 때 리소스 그룹 수준에서이 작업을 수행할 수 있습니다. Security Center의 포털 UI에서 **연속 내보내기** 페이지를 사용 하는 경우 구독 수준에서 정의 해야 합니다.

    > [!TIP]
    > API를 사용 하 여 여러 내보내기 구성을 설정 했거나 API 전용 매개 변수를 사용한 경우 해당 추가 기능은 Security Center UI에 표시 되지 않습니다. 대신 다른 구성이 존재 한다는 것을 알리는 배너가 표시 됩니다.

[REST API 설명서](https://docs.microsoft.com/rest/api/securitycenter/automations)의 자동화 API에 대해 자세히 알아보세요.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>Azure Event Hubs를 통한 SIEM 통합 구성

Azure Event Hubs는 프로그래밍 방식으로 스트리밍 데이터를 사용 하기 위한 훌륭한 솔루션입니다. Azure Security Center 경고 및 권장 사항에 대해서는 타사 SIEM과 통합 하는 데 선호 되는 방법입니다.

> [!NOTE]
> 대부분의 경우 모니터링 데이터를 외부 도구로 스트리밍하는 가장 효과적인 방법은 Azure Event Hubs를 사용 하는 것입니다. [이 문서](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) 에서는 다양 한 원본에서 이벤트 허브로 모니터링 데이터를 스트리밍하는 방법에 대 한 간략 한 설명을 제공 하 고 자세한 지침에 대 한 링크를 제공 합니다.

> [!NOTE]
> Azure 활동 로그를 사용 하 여 이전에 Security Center 경고를 SIEM으로 내보낸 경우 아래 절차가 해당 방법론을 대체 합니다.

내보낸 데이터 형식의 이벤트 스키마를 보려면 [이벤트 허브 이벤트 스키마](https://aka.ms/ASCAutomationSchemas)를 방문 하십시오.


### <a name="to-integrate-with-a-siem"></a>SIEM과 통합 하려면 

선택한 Security Center 데이터의 연속 내보내기를 Azure Event Hubs로 구성한 후 SIEM에 대 한 적절 한 커넥터를 설정할 수 있습니다.

* **Azure 센티널** -여기에 제공 된 기본 Azure Security Center 경고 [데이터 커넥터](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) 를 사용 합니다.
* **Splunk** - [Splunk에 대 한 Azure Monitor 추가 기능](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) 사용
* **IBM QRadar** - [수동으로 구성 된 로그 원본](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html) 사용
* **Arcsight** – [smartconnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) 사용

또한 지속적으로 내보낸 데이터를 구성 된 이벤트 허브에서 Azure 데이터 탐색기로 자동으로 이동 하려면 [이벤트 허브에서 azure 데이터 탐색기로 데이터 수집](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)의 지침을 사용 하세요.



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Log Analytics 작업 영역으로 연속 내보내기

Log Analytics 작업 영역 내의 Azure Security Center 데이터를 분석 하거나 Azure 경고를 Security Center와 함께 사용 하려면 Log Analytics 작업 영역으로 연속 내보내기를 설정 합니다.

Log Analytics 작업 영역으로 내보내려면 작업 영역에서 Security Center Log Analytics 솔루션을 사용 하도록 설정 해야 합니다. Azure Portal 사용 하는 경우 연속 내보내기를 사용 하도록 설정 하면 Security Center의 무료 계층 솔루션이 자동으로 사용 하도록 설정 됩니다. 그러나 연속 내보내기 설정을 프로그래밍 방식으로 구성 하는 경우 **가격 책정 & 설정**에서 필수 작업 영역에 대 한 무료 또는 표준 가격 책정 계층을 수동으로 선택 해야 합니다.  

### <a name="log-analytics-tables-and-schemas"></a>테이블 및 스키마 Log Analytics

보안 경고 및 권장 사항은 각각 *Securityalert* 및 *securityalert* 테이블에 저장 됩니다. 이러한 테이블을 포함 하는 Log Analytics 솔루션의 이름은 무료 또는 표준 계층에 있는지 ( [가격 책정](security-center-pricing.md)참조) (보안 (' 보안 및 감사 ') 또는 Securitycenter free)에 따라 달라 집니다.

![Log Analytics의 * SecurityAlert * 테이블](./media/continuous-export/log-analytics-securityalert-solution.png)

내보낸 데이터 형식의 이벤트 스키마를 보려면 [Log Analytics 테이블 스키마](https://aka.ms/ASCAutomationSchemas)를 참조 하세요.

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Azure Monitor에서 내보낸 보안 경고 및 권장 사항 보기

경우에 따라 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)에서 내보낸 보안 경고 및/또는 권장 사항을 보도록 선택할 수 있습니다. 

Azure Monitor는 진단 로그, 메트릭 경고 및 Log Analytics 작업 영역 쿼리를 기반으로 하는 사용자 지정 경고를 비롯 한 다양 한 Azure 경고에 대 한 통합 경고 환경을 제공 합니다.

Azure Monitor에서 Security Center의 경고 및 권장 사항을 보려면 Log Analytics 쿼리 (로그 경고)를 기반으로 경고 규칙을 구성 합니다.

1. Azure Monitor의 **경고** 페이지에서 **새 경고 규칙**을 선택 합니다.

    ![Azure Monitor의 경고 페이지](./media/continuous-export/azure-monitor-alerts.png)

1. 규칙 만들기 페이지에서 새 규칙을 구성 합니다 ( [Azure Monitor에서 로그 경고 규칙](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)을 구성 하는 것과 같은 방식으로).

    * **리소스**에서 보안 경고 및 권장 사항을 내보낸 Log Analytics 작업 영역을 선택 합니다.

    * **조건**에 대해 **사용자 지정 로그 검색**을 선택 합니다. 표시 되는 페이지에서 query, lookback period 및 frequency period를 구성 합니다. 검색 쿼리에서는 Log Analytics으로 연속 내보내기를 사용 하도록 설정 하는 경우 *Securityalert* 또는 *securityalert* 을 입력 하 여 Security Center 연속으로 내보낼 데이터 형식을 쿼리할 수 있습니다. 
    
    * 필요에 따라 트리거할 [작업 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 을 구성 합니다. 작업 그룹은 전자 메일 전송, ITSM 티켓, 웹 후크 등을 트리거할 수 있습니다.
    ![Azure Monitor 경고 규칙](./media/continuous-export/azure-monitor-alert-rule.png)

이제 작업 그룹 (제공 된 경우)의 자동 트리거를 사용 하 여 Azure Monitor 경고에 새 Azure Security Center 경고 또는 권장 사항 (구성에 따라)이 표시 됩니다.

## <a name="manual-one-time-export-of-security-alerts"></a>보안 경고의 수동 일회성 내보내기

경고 또는 권장 사항에 대 한 CSV 보고서를 다운로드 하려면 **보안 경고** 또는 **권장 사항** 페이지를 열고 **csv 보고서 다운로드** 단추를 선택 합니다.

[![경고 데이터를 CSV 파일로 다운로드](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> 이러한 보고서에는 현재 선택한 구독의 리소스에 대 한 경고 및 권장 사항이 포함 되어 있습니다.



## <a name="faq---continuous-export"></a>FAQ-연속 내보내기

### <a name="what-are-the-costs-involved-in-exporting-data"></a>데이터를 내보내는 데 드는 비용은 얼마 인가요?

연속 내보내기를 사용 하기 위한 비용은 없습니다. 사용자의 구성에 따라 Log Analytics 작업 영역에서 데이터를 수집 하 고 보존 하는 데 비용이 발생할 수 있습니다. 

[Log Analytics 작업 영역 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아보세요.

[Azure 이벤트 허브 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)에 대해 자세히 알아보세요.


## <a name="next-steps"></a>다음 단계

이 문서에서는 권장 사항 및 경고의 연속 내보내기를 구성 하는 방법에 대해 알아보았습니다. 또한 경고 데이터를 CSV 파일로 다운로드 하는 방법도 배웠습니다. 

관련 자료는 다음 설명서를 참조 하세요. 

- [Azure Event Hubs 설명서](https://docs.microsoft.com/azure/event-hubs/)
- [Azure 센티널 설명서](https://docs.microsoft.com/azure/sentinel/)
- [Azure Monitor 설명서](https://docs.microsoft.com/azure/azure-monitor/)
- [워크플로 자동화 및 연속 내보내기 데이터 형식 스키마](https://aka.ms/ASCAutomationSchemas)
