---
title: Azure Monitor로 Surface Hub 모니터링 | Microsoft Docs
description: Surface Hub 솔루션으로 Surface Hub 상태를 추적하여 Surface Hub가 사용되고 있는 방식을 파악합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: magoedte
ms.openlocfilehash: 902cf62e53581785caf2730f63af3633d8e1e498
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596255"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Azure Monitor로 Surface Hub를 모니터링하여 상태 추적

![Surface Hub 기호](./media/surface-hubs/surface-hub-symbol.png)

이 문서에서는 Azure Monitor의 Surface Hub 솔루션을 사용하여 Microsoft Surface Hub 디바이스를 모니터링하는 방법을 설명합니다. 이 솔루션은 Surface Hub 상태를 추적하여 Surface Hub가 사용되고 있는 방식을 파악하는 데 도움이 됩니다.

Surface Hub마다 Microsoft Monitoring Agent가 설치되어 있습니다. 에이전트를 통해야만 데이터를 Surface Hub에서 Azure Monitor의 Log Analytics 작업 영역으로 보낼 수 있습니다. 로그 파일은 먼저 Surface Hub에서 읽힌 다음, Azure Monitor로 전송됩니다. 오프라인 상태에 있는 서버, 동기화되지 않는 일정 또는 Skype에 로그인할 수 없는 디바이스 계정과 같은 문제들이 Azure Monitor의 Surface Hub 대시보드에 표시됩니다. 대시보드의 데이터를 통해 실행되지 않거나 다른 문제가 있는 디바이스를 확인하고, 잠재적으로는 발견된 문제에 대한 픽스도 적용할 수 있습니다.

## <a name="install-and-configure-the-solution"></a>솔루션 설치 및 구성
다음 정보를 사용하여 솔루션을 설치하고 구성합니다. Azure Monitor에서 Surface Hub를 관리하려면 다음이 필요합니다.

* 모니터링할 디바이스의 수를 지원하는 [Log Analytics 구독](https://azure.microsoft.com/pricing/details/log-analytics/) 수준. Log Analytics 가격 책정은 등록하는 디바이스 수와 처리할 데이터 양에 따라 달라집니다. Surface Hub 롤아웃을 계획할 때 이 점을 고려해야 합니다.

다음으로, 기존 Log Analytics 작업 영역을 추가하거나 새로 만듭니다. 두 방법 중 하나를 사용하는 방법에 대한 자세한 내용은 [Azure Portal에서 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md)를 참조하세요. Log Analytics 작업 영역이 구성되면 다음 두 가지 방법으로 Surface Hub 디바이스를 등록할 수 있습니다.

* InTune을 통해 자동으로
* Surface Hub 디바이스의 **설정**을 통한 수동 등록

## <a name="set-up-monitoring"></a>모니터링 설정
Azure Monitor를 사용하여 Surface Hub의 상태와 활동을 모니터링할 수 있습니다. Intune을 사용하거나 로컬로 Surface Hub의 **설정**을 사용하여 해당 Surface Hub를 등록할 수 있습니다.

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Intune 통해 Azure Monitor에 Surface Hub 연결
Surface Hub를 관리할 Log Analytics 작업 영역에 대한 작업 영역 ID 및 키가 필요합니다. Azure Portal의 작업 영역 설정에서 해당 항목을 가져올 수 있습니다.

Intune은 하나 이상의 디바이스에 적용되는 Log Analytics 작업 영역 구성 설정을 중앙에서 관리할 수 있게 하는 Microsoft 제품입니다. InTune 통해 디바이스를 구성하려면 다음 단계를 수행합니다.

1. InTune에 로그인합니다.
2. **설정** > **연결된 원본**으로 이동합니다.
3. Surface Hub 템플릿을 기반으로 하는 정책을 만들거나 편집합니다.
4. 정책의 Azure Operational Insights 섹션으로 이동하여 해당 정책에 Log Analytics *작업 영역 ID* 및 *작업 영역 키*를 추가합니다.
5. 해당 정책을 저장합니다.
6. 디바이스가 속한 그룹에 해당 정책을 연결합니다.

   ![InTune 정책](./media/surface-hubs/intune.png)

그런 다음, Intune에서 대상 그룹의 디바이스와 Log Analytics 설정을 동기화하여 Log Analytics 작업 영역에 해당 디바이스를 등록합니다.

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>설정 앱을 사용하여 Azure Monitor에 Surface Hub 연결
Surface Hub를 관리할 Log Analytics 작업 영역에 대한 작업 영역 ID 및 키가 필요합니다. Azure Portal에서 Log Analytics 작업 영역에 대한 설정에서 해당 항목을 가져올 수 있습니다.

사용자 환경을 관리하는 데 InTune을 사용하지 않을 경우 다음과 같이 각 Surface Hub의 **설정**을 통해 수동으로 디바이스를 등록할 수 있습니다.

1. Surface Hub에서 **설정**을 엽니다.
2. 메시지가 표시되면 디바이스 관리자 자격 증명을 입력합니다.
3. **이 디바이스**를 클릭한 다음, **모니터링**에서 **Log Analytics 설정 구성**을 클릭합니다.
4. **모니터링 사용**을 선택합니다.
5. Log Analytics 설정 대화 상자에서 Log Analytics **작업 영역 ID**, **작업 영역 키**를 차례로 입력합니다.  
   ![설정](./media/surface-hubs/settings.png)
6. **확인**을 클릭하여 구성을 완료합니다.

디바이스에 구성이 성공적으로 적용되었는지 여부를 알리는 확인 메시지가 나타납니다. 성공한 경우에는 에이전트가 Azure Monitor에 올바르게 연결되었다고 알리는 메시지가 나타납니다. 그러면 해당 디바이스에서 데이터를 확인하고 작업할 수 있는 Azure Monitor로 이 데이터를 보내기 시작합니다.

## <a name="monitor-surface-hubs"></a>Surface Hub 모니터링
Azure Monitor를 사용한 Surface Hub 모니터링은 등록된 다른 디바이스 모니터링과 매우 비슷합니다.

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Surface Hub 타일을 클릭하면 디바이스 상태가 표시됩니다.

   ![Surface Hub 대시보드](./media/surface-hubs/surface-hub-dashboard.png)

기존 또는 사용자 지정 로그 검색에 기반한 [경고](../platform/alerts-overview.md)를 만들 수 있습니다. Azure Monitor가 Surface Hub로부터 수집한 데이터를 사용하면 문제를 검색하여 디바이스에 정의하는 조건에 대해 경고할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 로그 쿼리](../log-query/log-query-overview.md)를 사용하여 자세한 Surface Hub 데이터 확인
* Surface Hub 문제 발생 시 알리는 [경고](../platform/alerts-overview.md) 만들기
