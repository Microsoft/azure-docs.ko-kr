---
title: 경고 설정
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 메트릭에 대해 경고를 사용 하도록 설정 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ded2f54379e60e8e3fc26d9c2166930a6f953078
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854850"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Azure Digital Twins 문제 해결: 경고

Azure Digital Twins는 리소스의 상태에 대 한 정보를 제공 하는 서비스 인스턴스에 대 한 [메트릭을](troubleshoot-metrics.md) 수집 합니다. 이러한 메트릭을 사용 하 여 Azure Digital Twins 서비스의 전반적인 상태와 연결 된 리소스를 평가할 수 있습니다.

**경고** 는 메트릭 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알려 줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대 한 자세한 내용은 [*Microsoft Azure의 경고 개요*](../azure-monitor/platform/alerts-overview.md)를 참조 하세요.

## <a name="turn-on-alerts"></a>경고 설정

Azure Digital Twins 인스턴스에 대해 경고를 사용 하도록 설정 하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 Azure Digital twins 인스턴스로 이동 합니다. 포털 검색 표시줄에 이름을 입력 하 여 찾을 수 있습니다. 

2. 메뉴에서 **경고** 를 선택 하 고 **+ 새 경고 규칙**을 선택 합니다.

3. 다음에 나오는 *경고 규칙 만들기* 페이지에서 프롬프트에 따라 조건, 트리거할 작업 및 경고 세부 정보를 정의할 수 있습니다.     
    * **범위** 세부 정보는 인스턴스에 대 한 세부 정보로 자동으로 채워집니다.
    * 경고 트리거 및 응답을 사용자 지정 하는 **조건** 및 **작업 그룹** 세부 정보를 정의 합니다.
    * **경고 규칙 세부 정보** 섹션에서 _경고 규칙 이름_, _설명 (선택 사항)_ 을 입력 합니다. 경고가 생성 되는 즉시 활성화 되도록 하려면 _만들 때 경고 규칙 사용_ 확인란을 선택할 수 있습니다.
    * 사용자가 추가 하는 일부 조건 및 작업에 따라 해당 드롭다운의 리소스 그룹 및 _심각도_ _에 경고 규칙 저장을_ 선택 해야 합니다.

4. 경고 규칙 _만들기_ 단추를 선택 하 여 경고 규칙을 만듭니다.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="범위, 조건 및 작업 그룹에 대 한 섹션을 포함 하는 경고 규칙 만들기 페이지를 보여 주는 스크린샷" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

:::image type="content" source="media/troubleshoot-alerts/alert-rule-details.png" alt-text="경고 규칙 만들기의 경고 규칙 세부 정보 섹션을 보여 주는 스크린샷" lightbox="media/troubleshoot-alerts/alert-rule-details.png":::

이러한 필드를 작성 하는 방법에 대 한 단계별 연습은 [*Microsoft Azure의 경고 개요*](../azure-monitor/platform/alerts-overview.md)를 참조 하세요. 다음은 Azure Digital Twins에 대해 단계가 표시 되는 몇 가지 예입니다.

Azure Digital Twins에 사용할 수 있는 경고 신호의 유형을 보여 주는 *Select 조건* 프로세스의 발췌 정보는 다음과 같습니다. 이 페이지에서 신호의 유형을 필터링 하 고 목록에서 원하는 신호를 선택할 수 있습니다.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="첫 번째 신호 논리 구성 페이지를 보여 주는 스크린샷 메트릭 또는 활동 로그를 선택 하는 신호 유형 상자에 강조 표시 되 고, 아래에서 선택할 수 있는 메트릭 목록이 표시 됩니다.":::

신호를 선택한 후에는 경고의 논리를 구성 하 라는 메시지가 표시 됩니다. 차원을 필터링 하 고, 경고에 대 한 임계값을 설정 하 고, 조건에 대 한 확인 빈도를 설정할 수 있습니다. 평균 라우팅 실패율 메트릭이 5%를 초과 하는 경우에 대 한 경고를 설정 하는 예는 다음과 같습니다.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="두 번째 신호 논리 구성 페이지를 보여 주는 스크린샷 이 페이지에는 메트릭 기록이 표시 되 고 Event Grid 작업과 같은 차원에서 필터링 할 영역이 있으며 ' 평균이 5 보다 큼 '과 같은 경고 논리를 정의 하는 섹션이 있습니다.":::
 
설정 경고가 표시 되 면 인스턴스의 *경고* 페이지에 다시 표시 됩니다.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="경고 페이지 및 추가할 단추를 보여 주는 스크린샷 구성 된 경고가 하나 있습니다." lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>다음 단계

* Azure Monitor 경고에 대 한 자세한 내용은 [*Microsoft Azure의 경고 개요*](../azure-monitor/platform/alerts-overview.md)를 참조 하세요.
* Azure Digital Twins 메트릭에 대 한 자세한 내용은 [*문제 해결: Azure Monitor 사용 하 여 메트릭 보기*](troubleshoot-metrics.md)를 참조 하세요.
* 메트릭에 대해 진단 로깅을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [*문제 해결: 진단 설정*](troubleshoot-diagnostics.md)을 참조 하세요.
