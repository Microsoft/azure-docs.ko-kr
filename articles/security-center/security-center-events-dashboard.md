---
title: Azure Security Center에서 보안 이벤트 모니터링 및 처리 | Microsoft Docs
description: Security Center의 이벤트 대시보드를 사용하여 Azure VM 및 비 Azure 컴퓨터에서 보안 이벤트를 확인하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 01f6da4f5ad6b618c444949fce8d2b7aa3367e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705176"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Azure Security Center에서 보안 이벤트 모니터링 및 처리
이벤트 대시보드에서는 시간별로 수집된 보안 이벤트 수의 개요와, 확인해야 할 수 있는 주목할 만한 이벤트의 목록이 제공됩니다.  

> [!NOTE]
> 이 기능을 사용하려면 작업 영역이 Log Analytics 버전 2를 실행해야 하며, Security Center 표준 계층이 적용되어 있어야 합니다. 표준 계층에 대한 자세한 내용은 Security Center [가격 책정 페이지](security-center-pricing.md)를 참조하세요.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="what-is-a-security-event"></a>보안 이벤트란?
Security Center에서는 Microsoft Monitoring Agent를 사용하여 컴퓨터에서 다양한 보안 관련 구성과 이벤트를 수집한 다음 작업 영역에 저장합니다. 이러한 데이터의 예로는 운영 체제 로그(Windows 이벤트 로그), 실행 중인 프로세스, Security Center와 통합된 보안 솔루션의 이벤트 등이 있습니다. 또한 Microsoft Monitoring Agent는 작업 영역에 크래시 덤프 파일을 복사합니다.

## <a name="events-processed-dashboard"></a>처리된 이벤트 대시보드
Security Center 주 메뉴 또는 Security Center **개요** 블레이드에서 **이벤트** 대시보드에 액세스합니다.  

![처리된 이벤트 대시보드][1]

**Security Center** 아래의 **이벤트** 타일에는 Azure VM 및 비 Azure 컴퓨터에서 Security Center로 들어오는 이벤트의 수가 표시됩니다.

**이벤트 대시보드**에서는 시간별로 처리된 이벤트 수의 개요와 이벤트 목록을 제공합니다.

 ![대시보드][2]

 대시보드 위쪽에는 지난주에 처리된 모든 이벤트의 추세가 표시됩니다. 그리고 대시보드 아래쪽에는 주목할 만한 이벤트 및 모든 이벤트가 유형별로 나열됩니다.

 - **주목할 만한 이벤트**에는 Security Center에서 제공하는 이벤트 쿼리와 직접 만들고 추가하는 이벤트 쿼리가 포함됩니다. 대시보드에서 각 주목할 만한 이벤트 수도 빠르게 확인할 수 있습니다.
 - **유형별 모든 이벤트**에는 수신 중인 이벤트 유형과 각 유형의 수가 표시됩니다. 이벤트 유형의 예로는 SecurityEvent, CommonSecurityLog, WindowsFirewall, W3CIISLog 등이 있습니다.

> [!NOTE]
> 주목할 만한 이벤트에는 [웹 기준 평가](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment)가 포함됩니다. 웹 기준 평가의 목표는 잠재적으로 취약한 웹 서버 설정을 찾는 것입니다.

## <a name="view-processed-event-details"></a>처리된 이벤트 세부 정보 보기
1. **Security Center** 주 메뉴에서 **이벤트**를 선택합니다.
2. **이벤트 대시보드** 작업 영역 선택기가 열릴 수 있습니다. 작업 영역이 하나뿐이면 이 작업 영역 선택기는 표시되지 않습니다. 작업 영역이 여러 개이면 처리된 이벤트 세부 정보를 확인할 작업 영역을 선택해야 합니다. 작업 영역이 여러 개인 경우 목록에서 작업 영역을 선택합니다.

   ![작업 영역 목록][3]

3. **이벤트 대시보드**가 열리고 선택한 작업 영역에 대한 이벤트 세부 정보가 표시됩니다. 주목할 만한 이벤트 및 유형별 모든 이벤트를 확인할 수 있습니다.  이 예제에서는 **주목할 만한 이벤트**를 선택했습니다.

   ![주목할 만한 이벤트][4]

4. 이벤트 유형을 선택하여 작업 영역 아래에서 더 많은 데이터를 쿼리할 수 있습니다. 이 예제에서는 **SecurityEvent**를 선택했습니다.

   ![이벤트 유형 선택][5]

5. 해당 이벤트 유형에 대한 추가 세부 정보가 포함된 **로그 검색**이 열립니다.

   ![로그 검색][6]

## <a name="add-a-notable-event"></a>주목할 만한 이벤트 추가
Security Center에서는 몇 가지 주목할 만한 이벤트가 기본적으로 제공됩니다. 사용 하 여 사용자 고유의 쿼리를 기반으로 하는 주목할 만한 이벤트를 추가할 수 있습니다 합니다 [Kusto 쿼리 언어](../log-analytics/log-analytics-search-reference.md)합니다. 여기서는 **이벤트 대시보드**로 돌아가 주목할 만한 이벤트를 추가하겠습니다.

1. **주목할 만한 이벤트 추가**를 선택합니다.

   ![주목할 만한 이벤트 추가][7]

2. **사용자 지정 주목할 만한 이벤트 추가**가 열립니다.  **표시 이름** 아래에 주목할 만한 이벤트의 이름을 입력합니다. **검색 쿼리** 아래에 이벤트에 대한 쿼리를 입력합니다.

   ![쿼리 입력][8]

4. **확인**을 선택합니다.

## <a name="update-your-workspace-for-events-processing"></a>이벤트 처리를 위해 작업 영역 업데이트
Security Center에서 이벤트 처리 기능을 사용하려면 작업 영역에서 Log Analytics 버전 2를 실행 중이어야 하며, Security Center의 표준 계층이 적용된 상태여야 합니다. **이벤트 대시보드** 작업 영역 선택기는 이러한 요구 사항을 충족하지 않는 작업 영역을 식별합니다.

![요구 사항을 충족하지 않는 작업 영역][9]

작업 영역 행에 표시되는 정보와 해당 의미는 다음과 같습니다.

- **업데이트 필요**가 포함되어 있음 - 작업 영역을 Log Analytics 버전 2로 업데이트해야 합니다.
- **업그레이드 계획**이 포함되어 있음 - 작업 영역이 Security Center의 표준 계층을 사용하도록 업그레이드해야 합니다.
- 비어 있음 - 작업 영역이 요구 사항을 충족하며, 작업 영역을 선택하면 대시보드로 이동합니다.

> [!NOTE]
> **이벤트 대시보드** 아래의 **이벤트** 열에는 각 작업 영역의 이벤트 수가 표시됩니다.  Security Center의 무료 계층이 적용되는 작업 영역의 경우에는 이 열이 비어 있습니다. 무료 계층에서 Security Center가 이벤트를 수집 하지만 이벤트를 Azure Monitor 로그에 저장 되지 않으며 대시보드에서 사용할 수 없는 합니다.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Log Analytics 버전 2로 작업 영역 업데이트
1. **업데이트 필요**가 표시된 작업 영역을 선택합니다.
2. **업그레이드 검색**이 열립니다. **지금 업그레이드**를 선택합니다.

   ![지금 업그레이드][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Security Center의 표준 계층으로 업그레이드
1. **업그레이드 계획**이 표시된 작업 영역을 선택합니다.
2. **이벤트 대시보드**가 열립니다. **이벤트 대시보드 체험**을 선택합니다.

   ![대시보드 사용해 보기][11]

3. **고급 보안으로 온보딩** 아래에서 업그레이드할 작업 영역을 선택합니다.
4. **가격 책정** 아래에서 **표준**을 선택합니다.
5. **저장**을 선택합니다.

   ![표준 계층으로 업그레이드][12]

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center의 이벤트 대시보드를 사용하는 방법에 대해 알아보았습니다. 대시보드의 작동 방식에 대해 자세히 알아보고 이벤트 쿼리를 직접 작성하려면 다음 항목을 참조하세요.

- [Azure Monitor 로그 란?](../log-analytics/log-analytics-overview.md) – Azure Monitor 로그에 대 한 개요
- [Kusto의 로그 검색 이해](../log-analytics/log-analytics-log-search-new.md) -Azure Monitor 로그에서 로그 검색 사용 하는 방법에 대해 설명 하 고 로그 검색을 만들기 전에 이해 해야 하는 개념을 제공
- [Kusto 검색 참조](../log-analytics/log-analytics-search-reference.md) – 로그의 쿼리 언어를 사용 하 여 사용자 고유의 이벤트 쿼리를 작성 하는 방법 알아보기

Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [Security Center 개요](security-center-intro.md) – Security Center의 주요 기능에 대해 설명합니다.

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
