---
title: Azure Monitor에서 활동 로그 Azure Active Directory Microsoft Docs
description: Azure Monitor의 Azure Active Directory 활동 로그 소개
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4253fe52346890eaa993a18e8e9bc9b270bffd7
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229857"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Azure Monitor의 Azure AD 활동 로그

장기 보존 및 데이터 통찰력을 위해 Azure AD (Azure Active Directory) 활동 로그를 여러 끝점으로 라우팅할 수 있습니다. 이 기능을 사용하여 다음을 할 수 있습니다.

* 데이터를 장기간 보존하기 위해 Azure 스토리지 계정에 Azure AD 활동 로그를 보관합니다.
* Splunk 및 QRadar 같은 인기 있는 SIEM(보안 정보 및 이벤트 관리) 도구를 사용하여 Azure AD 활동 로그를 Azure 이벤트 허브로 스트리밍하여 분석합니다.
* Azure AD 활동 로그를 이벤트 허브로 스트리밍하여 고유한 사용자 지정 로그 솔루션과 통합합니다.
* Azure AD 활동 로그를 Azure Monitor 로그에 보내 연결된 데이터에 대한 다양한 시각화, 모니터링 및 경고를 사용합니다.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>지원되는 보고서

이 기능을 사용하여 Azure AD 감사 로그 및 로그인 로그를 Azure Storage 계정, 이벤트 허브, Azure Monitor 로그 또는 사용자 지정 솔루션으로 라우팅할 수 있습니다. 

* **감사 로그**: [감사 로그 활동 보고서](concept-audit-logs.md)는 테넌트에서 수행된 모든 작업 기록에 대한 액세스를 제공합니다.
* **로그인 로그**: [로그인 활동 보고서](concept-sign-ins.md)를 사용하면 감사 로그에 보고된 작업을 누가 수행했는지 확인할 수 있습니다.

> [!NOTE]
> B2C 관련 감사 및 로그인 활동 로그는 현재 지원되지 않습니다.
>

## <a name="prerequisites"></a>전제 조건

이 기능을 사용하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
* Azure Portal에서 Azure AD 감사 로그에 액세스할 수 있는 Azure AD Free, Basic, Premium 1 또는 Premium 2 [라이선스](https://azure.microsoft.com/pricing/details/active-directory/). 
* Azure AD 테넌트.
* Azure AD 테 넌 트의 **전역 관리자** 또는 **보안 관리자** 인 사용자입니다.
* Azure Portal에서 Azure AD 로그인 로그에 액세스할 수 있는 Azure AD Premium 1 또는 Premium 2 [라이선스](https://azure.microsoft.com/pricing/details/active-directory/). 

감사 로그 데이터를 라우팅하려는 위치에 따라 다음 둘 중 하나가 필요합니다.

* *ListKeys* 권한이 있는 Azure Storage 계정. Blob Storage 계정이 아닌 일반 스토리지 계정을 사용하는 것이 좋습니다. 스토리지 가격 책정 정보는 [Azure Storage 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/?service=storage)를 참조하세요. 
* 타사 솔루션과 통합할 Azure Event Hubs 네임스페이스.
* Azure Monitor 로그에 로그를 보내는 Azure Log Analytics 작업 영역

## <a name="cost-considerations"></a>비용 고려 사항

Azure AD 라이선스가 이미 있는 경우 스토리지 계정 및 이벤트 허브를 설정하려면 Azure 구독이 필요합니다. Azure 구독은 무료로 제공되지만 보관에 사용하는 스토리지 계정과 스트리밍에 사용하는 이벤트 허브를 비롯한 Azure 리소스를 활용하려면 비용을 지불해야 합니다. 데이터의 양 그리고 그에 따라 발생하는 비용은 테넌트 크기에 따라 크게 달라질 수 있습니다. 

### <a name="storage-size-for-activity-logs"></a>활동 로그에 대한 스토리지 크기

모든 감사 로그 이벤트는 약 2KB의 데이터 스토리지를 사용합니다. 로그인 이벤트 로그는 약 4kb의 데이터 저장소입니다. 사용자가 100,000명이고 하루에 약 150만 개의 이벤트가 발생하는 테넌트의 경우 하루에 약 3GB의 데이터 스토리지가 필요합니다. 약 5분 단위로 쓰기가 발생하므로 매달 약 9000개의 쓰기 작업을 예상할 수 있습니다. 


다음 표에는 미국 서부에서 범용 v2 스토리지 계정을 1년 이상 보유할 때 발생할 것으로 예상되는 테넌트 크기에 따른 예상 비용이 정리되어 있습니다. 애플리케이션의 예상 데이터 볼륨에 해당하는 정확한 예상 가격을 계산하려면 [Azure Storage 가격 계산기](https://azure.microsoft.com/pricing/details/storage/blobs/)를 사용하세요.


| 로그 범주 | 사용자 수 | 일간 이벤트 수 | 월간 데이터 볼륨(예상치) | 월간 비용(예상치) | 연간 비용(예상치) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| 감사 | 100,000 | 150만&nbsp; | 90GB | $1.93 | $23.12 |
| 감사 | 1,000 | 15,000 | 900MB | $0.02 | $0.24 |
| 로그인 | 1,000 | 34,800 | 4GB | $0.13 | $1.56 |
| 로그인 | 100,000 | 1500만&nbsp; | 1.7TB | $35.41 | $424.92 |
 









### <a name="event-hub-messages-for-activity-logs"></a>활동 로그에 대한 이벤트 허브 메시지

이벤트는 약 5분 간격으로 일괄 처리되며 해당 시간대 내에서 모든 이벤트를 포함한 단일 메시지로 전송됩니다. 이벤트 허브에서 한 메시지의 최대 크기는 256KB이며, 해당 시간대의 총 메시지 크기가 해당 볼륨을 초과하면 여러 개의 메시지가 전송됩니다. 

예를 들어 사용자 수가 100,000명을 초과하는 대규모 테넌트에서는 평균적으로 초당 약 18개 이벤트가 발생하며, 이는 5분당 5,400개 이벤트와 동일한 속도입니다. 감사 로그는 이벤트당 약 2KB이므로 데이터 10.8MB와 동일합니다. 따라서 5분 간격으로 메시지 43개가 이벤트 허브로 전송됩니다. 

다음 표에는 사용자 로그인 동작 등과 같은 많은 요소에 따라 테 넌 트에 따라 다를 수 있는 이벤트 데이터의 양에 따라 월의 기본 이벤트 허브에 대 한 월별 예상 비용이 포함 되어 있습니다. 응용 프로그램에 예상 되는 데이터 볼륨의 정확한 예상 값을 계산 하려면 [Event Hubs 가격 계산기](https://azure.microsoft.com/pricing/details/event-hubs/)를 사용 합니다.

| 로그 범주 | 사용자 수 | 초당 이벤트 수 | 5분 간격당 이벤트 수 | 간격당 볼륨 | 간격당 메시지 수 | 월간 메시지 수 | 월간 비용(예상치) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| 감사 | 100,000 | 18 | 5,400 | 10.8MB | 43 | 371,520 | $10.83 |
| 감사 | 1,000 | 0.1 | 52 | 104KB | 1 | 8,640 | $10.80 |
| 로그인 | 100,000 | 18000 | 540만 | 10.8 GB | 42188 | 364504320 | $23.9 |  
| 로그인 | 1,000 | 178 | 53,400 | 106.8MB&nbsp; | 418 | 3,611,520 | $11.06 |  

### <a name="azure-monitor-logs-cost-considerations"></a>Azure Monitor 로그 비용 고려 사항



| 로그 범주       | 사용자 수 | 일간 이벤트 수 | 월 당 이벤트 (30 일) | USD (est)의 월간 비용 |
| :--                | ---             | ---            | ---                        | --:                          |
| 감사 및 로그인 | 100,000         | 1650만     | 4억9500만                |  $1093.00                       |
| 감사              | 100,000         | 150만      | 45,000,000                 |  $246.66                     |
| 로그인           | 100,000         | 15,000,000     | 450,000,000                |  $847.28                     |










Azure Monitor 로그 관리와 관련된 비용을 검토하려면 [Azure Monitor 로그에서 데이터 볼륨 및 보존을 제어하여 비용 관리](../../azure-monitor/platform/manage-cost-storage.md)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

이 섹션에서는 Azure Monitor의 Azure AD 로그에 대해 자주 묻는 질문에 대답하고 알려진 문제에 대해 토론합니다.

**Q: 어떤 로그가 포함됩니까?**

**A:** 현재 B2C 관련 감사 이벤트가 포함되지 않지만, 이 기능을 라우팅하는 데 로그인 활동 로그와 감사 로그를 모두 사용할 수 있습니다. 현재 지원되는 로그 유형 및 기능 기반 로그를 알아보려면 [감사 로그 스키마](reference-azure-monitor-audit-log-schema.md) 및 [로그인 로그 스키마](reference-azure-monitor-sign-ins-log-schema.md)를 참조하세요. 

---

**Q: 작업 수행 후 해당 로그가 내 이벤트 허브에 표시되려면 얼마나 걸립니까?**

**A**: 작업이 수행된 후 2~5분 내에 이벤트 허브에 로그가 나타나야 합니다. Event Hubs에 대한 자세한 내용은 [Azure Event Hubs란?](../../event-hubs/event-hubs-about.md)을 참조하세요.

---

**Q: 작업 수행 후 해당 로그가 내 스토리지 계정에 표시되려면 얼마나 걸립니까?**

**A:** Azure Storage 계정의 경우 대기 시간은 어디서나 작업 수행 후 5~15분입니다.

---

**Q: 관리자가 진단 설정의 보존 기간을 변경 하면 어떻게 되나요?**

**A**: 새 보존 정책은 변경 후 수집 된 로그에 적용 됩니다. 정책 변경 이전에 수집된 로그에는 아무런 변화가 없습니다.

---

**Q: 데이터를 저장하는 데 비용이 얼마나 들까요?**

**A:** 스토리지 비용은 로그의 크기와 사용자가 선택한 보존 기간에 따라 달라집니다. 생성된 로그 볼륨에 따라 달라지는 예상 테넌트 비용 목록을 보려면 [활동 로그에 대한 Storage 크기](#storage-size-for-activity-logs) 섹션을 참조하세요.

---

**Q: 이벤트 허브에 데이터를 스트리밍하는 데 비용이 얼마나 들까요?**

**A:** 스트리밍 비용은 분당 수신 메시지 수에 따라 다릅니다. 이 문서에서는 메시지 수를 기반으로 비용을 계산하고 예상 비용을 나열하는 방법을 설명합니다. 

---

**Q: Azure AD 활동 로그를 SIEM 시스템과 통합하려면 어떻게 할까요?**

**A**: 다음 두 가지 방법으로 수행할 수 있습니다.

- Event Hubs에서 Azure Monitor를 사용하여 SIEM 시스템에 로그를 스트리밍합니다. 먼저 [이벤트 허브로 로그를 스트림](tutorial-azure-monitor-stream-logs-to-event-hub.md)한 다음, 구성된 이벤트 허브를 사용하여 [SIEM 도구를 설정](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub)합니다. 

- [보고 Graph API](concept-reporting-api.md)를 사용하여 데이터에 액세스한 다음, 고유한 스크립트를 사용하여 SIEM 시스템에 푸시합니다.

---

**Q: 현재 지원되는 SIEM 도구는 무엇입니까?** 

**A**: **a**: 현재 Azure Monitor은 [Splunk](./howto-integrate-activity-logs-with-splunk.md), IBM qradar, [sumo 논리](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [arcsight](./howto-integrate-activity-logs-with-arcsight.md), LogRhythm 및 Logz.io에서 지원 됩니다. 커넥터의 작동 원리에 대한 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md)을 참조하세요.

---

**Q: Azure AD 활동 로그를 Splunk 인스턴스와 통합하려면 어떻게 할까요?**

**A**: 먼저 [Azure AD 활동 로그를 이벤트 허브로 라우팅](./tutorial-azure-monitor-stream-logs-to-event-hub.md)한 다음, [Splunk를 사용하여 활동 로그 통합](./howto-integrate-activity-logs-with-splunk.md) 단계를 수행합니다.

---

**Q: Azure AD 활동 로그를 Sumo Logic과 통합하려면 어떻게 할까요?** 

**A**: 먼저 [Azure AD 활동 로그를 이벤트 허브로 라우팅](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory)한 다음, [SumoLogic에서 Azure AD 애플리케이션 설치 및 대시보드 보기](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) 단계를 수행합니다.

---

**Q: 외부 SIEM 도구를 사용하지 않고 이벤트 허브에서 데이터에 액세스할 수 있나요?** 

**A**: 예. 사용자 지정 애플리케이션에서 로그에 액세스하려면 [Event Hubs API](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)를 사용합니다. 

---


## <a name="next-steps"></a>다음 단계

* [스토리지 계정에 활동 로그 보관](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [활동 로그를 이벤트 허브로 라우팅](./tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Azure Monitor와 활동 로그 통합](howto-integrate-activity-logs-with-log-analytics.md)