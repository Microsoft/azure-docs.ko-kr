---
title: 연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션
titleSuffix: Azure Network Watcher
description: 연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: vinigam
ms.openlocfilehash: ddf6e326df876229d32ef15983f76879836f1fca
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701838"
---
# <a name="migrate-to-connection-monitor-preview-from-connection-monitor"></a>연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션

한 번의 클릭으로 가동 중지 시간 없이 기존 연결 모니터를 새 연결 모니터 (미리 보기)로 마이그레이션할 수 있습니다. 이점에 대 한 자세한 내용을 보려면 [연결 모니터 (미리 보기)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview) 를 참조 하세요.

## <a name="key-points-to-note"></a>핵심 사항

* 에이전트 및 방화벽 설정이 그대로 작동 합니다 (터치 필요 없음). 
* 기존 연결 모니터는 연결 모니터 (미리 보기) > 테스트 그룹 > 테스트 형식으로 매핑됩니다. 사용자는 *편집* 을 클릭 하 여 새 연결 모니터의 속성을 확인 및 수정 하 고, 연결 모니터를 변경 하 고 Azure Resource Manager를 통해 제출할 수 있습니다. 
* Network Watcher 확장을 포함 하는 Azure virtual machines는 작업 영역 및 메트릭 모두에 데이터를 보냅니다. 연결 모니터는 이전 메트릭 중지 (ProbesFailedPercent 및 AverageRoundtripMs) 대신 새 메트릭 (ChecksFailedPercent (Preview) 및 RoundTripTimeMs (미리 보기))을 통해 데이터를 사용할 수 있도록 합니다. 
* 데이터 모니터링
    * 경고-마이그레이션의 일부로 새 메트릭으로 마이그레이션됩니다.
    * 대시보드 및 통합 – 메트릭 집합을 수동으로 편집 해야 합니다. 
    
## <a name="prerequisites"></a>필수 구성 요소

사용자 지정 작업 영역을 사용 하는 경우 Log Analytics 작업 영역의 구독 및 지역에서 Network Watcher를 사용 하도록 설정 해야 합니다. 

## <a name="steps-to-migrate-from-connection-monitor-to-connection-monitor-preview"></a>연결 모니터에서 연결 모니터로 마이그레이션하는 단계 (미리 보기)

1. "연결 모니터"를 클릭 하 고 "연결 모니터 마이그레이션"으로 이동 하 여 이전에서 최신 솔루션으로 연결 모니터를 마이그레이션합니다.

    ![연결 모니터를 연결 모니터 미리 보기로 마이그레이션하는 스크린샷](./media/connection-monitor-2-preview/migrate-cm-to-cm-preview.png)
    
1. 구독 및 연결 모니터를 선택 하 고 "선택한 마이그레이션"을 클릭 합니다. 한 번 클릭으로 기존 연결 모니터를 연결 모니터 (미리 보기)로 마이그레이션 
1. 연결 모니터 속성을 사용자 지정 하 고, 기본 작업 영역을 변경 하 고, 템플릿을 다운로드 하 고, 마이그레이션의 상태를 확인할 수 있습니다. 
1. 마이그레이션이 시작 되 면 다음과 같은 변경이 수행 됩니다. 
    1. 최신 연결 모니터에 대 한 리소스 변경 내용 Azure Resource Manager
        1. 연결 모니터의 이름, 지역 및 구독은 변경 되지 않은 상태로 유지 됩니다. 따라서 리소스 ID에는 영향을 주지 않습니다.
        1. 사용자 지정 되지 않은 경우 연결 모니터의 지역 및 구독에서 기본 Log Analytics 작업 영역이 생성 됩니다. 이 작업 영역에는 모니터링 데이터가 저장 되는 위치가 있습니다. 테스트 결과 데이터도 메트릭에 저장 됩니다.
        1. 각 테스트는 * defaultTestGroup * 이라는 테스트 그룹으로 마이그레이션됩니다.
        1.  원본 및 대상 끝점이 생성 되 고 생성 된 테스트 그룹에 사용 됩니다. 기본 이름은 *Defaultsourceendpoint* 및 *defaultsourceendpoint* 입니다.
        1. 대상 포트 및 검색 간격이 *Defaulttestconfiguration*라는 테스트 구성으로 이동 됩니다. 포트 값을 기준으로 프로토콜을 설정 합니다. 성공 임계값 및 기타 선택적 속성은 비워 둡니다.
    1. 메트릭 경고는 연결 모니터 (미리 보기) 메트릭 경고로 마이그레이션됩니다. 메트릭이 다르기 때문에 <link to metric section in the doc> 변경
    1. 마이그레이션된 연결 모니터는 이전 연결 모니터 솔루션에 표시 되지 않으며 이제는 연결 모니터 (미리 보기) 에서만 사용할 수 있습니다.
    1. Power BI, Grafana, SIEM 시스템과 통합 하는 대시보드 등의 외부 통합은 사용자가 직접 마이그레이션해야 합니다. 사용자가 설치를 마이그레이션하기 위해 수행 해야 하는 유일한 수동 단계입니다.

## <a name="next-steps"></a>다음 단계

* [네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 마이그레이션하는 방법](migrate-to-connection-monitor-preview-from-network-performance-monitor.md) 에 대해 알아봅니다.
* [Azure Portal를 사용 하 여 연결 모니터 (미리 보기)를 만드는 방법을](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal) 알아봅니다.
