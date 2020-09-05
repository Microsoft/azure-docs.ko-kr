---
title: 네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 마이그레이션
titleSuffix: Azure Network Watcher
description: 네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 마이그레이션하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 69dbb1dd4017c5acf9c195f5104741caee38c2b7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88701837"
---
# <a name="migrate-to-connection-monitor-preview-from-network-performance-monitor"></a>네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 마이그레이션

네트워크 성능 모니터의 테스트를 한 번의 클릭으로 가동 중지 시간 없이 새로운 및 향상 된 연결 모니터 (미리 보기)로 마이그레이션할 수 있습니다. 이점에 대 한 자세한 내용을 보려면 [연결 모니터 (미리 보기)](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview) 를 참조 하세요.

>[!NOTE]
> 서비스 연결 모니터의 테스트만 연결 모니터 (미리 보기)로 마이그레이션할 수 있습니다.
>

## <a name="key-points-to-note"></a>핵심 사항

* 온-프레미스 에이전트 및 방화벽 설정은 그대로 작동 합니다. 변경할 필요가 없습니다. Azure Virtual Machines에 설치 된 Log Analytics 에이전트를 Network Watcher 확장으로 바꾸어야 합니다.
* 기존 테스트는 연결 모니터 (미리 보기) > 테스트 그룹 > 테스트 형식에 매핑됩니다. 사용자는 *편집* 을 클릭 하 여 새 연결 모니터의 속성을 확인 및 수정 하 고, 연결 모니터를 변경 하 고 Azure Resource Manager를 통해 제출할 수 있습니다.
* 에이전트는 Log Analytics 작업 영역 및 메트릭에 대 한 데이터를 전송 합니다.
* 데이터 모니터링
    * Log Analytics 데이터 – 모든 데이터 사전 마이그레이션은 NetworkMonitoring 테이블에 NPM가 구성 된 작업 영역에서 계속 유지 됩니다. 마이그레이션 후에는 데이터가 NetworkMonitoring 테이블로 이동 하 고 동일한 작업 영역에 ConnectionMonitor_CL 테이블로 이동 합니다. NPM에서 테스트를 사용 하지 않도록 설정 하면 데이터는 ConnectionMonitor_CL 테이블에만 저장 됩니다.
    * 로그 기반 경고, 대시보드 및 통합-새 테이블 ConnectionMonitor_CL을 기반으로 쿼리를 수동으로 편집 해야 합니다. 이 링크를 사용 하 여 메트릭에 경고를 다시 만들 수도 있습니다. 마이그레이션의 일부로 자동으로 NetworkMonitoring 테이블의 로그 기반 경고를 메트릭 기반 경고로 마이그레이션하는 기능은 곧 제공 될 예정입니다.
    
## <a name="prerequisites"></a>필수 구성 요소

*   Log Analytics 작업 영역의 구독 및 지역에서 Network Watcher 사용 하도록 설정 되었는지 확인
*   Log analytics 에이전트가 설치 된 Azure 가상 머신은 Network Watcher 확장을 사용 하도록 설정 해야 합니다.

## <a name="steps-to-migrate-tests-from-network-performance-monitor-to-connection-monitor-preview"></a>네트워크 성능 모니터에서 연결 모니터 (미리 보기)로 테스트를 마이그레이션하는 단계

1.  "연결 모니터"를 클릭 하 고 "NPM에서 테스트 마이그레이션"으로 이동 하 여 테스트를 연결 모니터 (미리 보기)로 마이그레이션합니다.

    ![NPM에서 연결 모니터 미리 보기로 테스트 마이그레이션을 보여 주는 스크린샷](./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png)
    
1.  구독, 작업 영역 및 마이그레이션할 NPM 기능을 선택 합니다. 현재는 서비스 연결 모니터의 테스트만 마이그레이션할 수 있습니다.  
1.  "가져오기"를 클릭 하 여 테스트 마이그레이션
1.  마이그레이션이 시작 되 면 다음과 같은 변경이 수행 됩니다. 
    1. 새 연결 모니터 리소스가 생성 됩니다.
        1. 지역 및 구독 당 하나의 연결 모니터가 생성 됩니다. 온-프레미스 에이전트를 사용 하는 테스트의 경우 새 연결 모니터 이름은 <workspaceName> _"온-프레미스" 형식입니다. Azure 에이전트에 대 한 테스트의 경우 새 연결 모니터 이름은<Azure_region_name 형식 <workspaceName> _ 입니다>
        1. 이제 NPM가 사용 하도록 설정 된 동일한 Log Analytics 작업 영역에 Connectionmonitor_CL 테이블 이라는 새 테이블에 모니터링 데이터가 저장 됩니다. 
        1. 테스트 이름이 테스트 그룹 이름으로 전달 됩니다. 테스트 설명은 마이그레이션되지 않습니다.
        1. 원본 및 대상 끝점이 생성 되 고 생성 된 테스트 그룹에 사용 됩니다. 온-프레미스 에이전트의 경우 끝점은 <workspaceName> _"엔드포인트"_ 형식으로 이름이 지정 됩니다 <FQDN of on-premises machine> . Azure의 경우 마이그레이션 테스트에 에이전트가 실행 되 고 있지 않으면 에이전트를 사용 하도록 설정 하 고 다시 마이그레이션해야 합니다.
        1. 대상 포트 및 검색 간격이 테스트 구성 ("TC"_ <testname> "및" tc "_ <testname> _" appthresholds ")으로 이동 됩니다. 포트 값을 기준으로 프로토콜을 설정 합니다. 성공 임계값 및 기타 선택적 속성은 비워 둡니다.
    1. NPM를 사용할 수 없습니다. 따라서 마이그레이션된 테스트는 ConnectionMonitor_CL 테이블 뿐만 아니라 NetworkMonitoring 테이블로도 계속 데이터를 전송 합니다. 이 단계를 수행 하면 기존 로그 기반 경고 및 통합에 영향을 주지 않습니다. 마이그레이션의 일부로 자동으로 NetworkMonitoring 테이블의 로그 기반 경고를 메트릭 기반 경고로 마이그레이션하는 것이 곧 제공 될 예정입니다.
    1. 새로 만든 연결 모니터가 연결 모니터 (미리 보기)에 표시 됩니다.
1.  마이그레이션 후에는 NPM에서 테스트를 수동으로 사용 하지 않도록 설정 해야 합니다. 이 작업을 수행 하기 전에도 동일한에 대 한 요금이 계속 청구 됩니다. NPM를 사용 하지 않도록 설정 하는 동안 ConnectionMonitor_CL 테이블에 대 한 경고를 다시 만들거나 메트릭을 사용 해야 합니다. 또한 Power BI, Grafana, SIEM systems와 통합 된 대시보드 등의 외부 통합을 ConnectionMonitor_CL 테이블에 마이그레이션해야 합니다.


## <a name="next-steps"></a>다음 단계

* [연결 모니터에서 연결 모니터 (미리 보기)로 마이그레이션하는 방법](migrate-to-connection-monitor-preview-from-connection-monitor.md) 에 대해 알아봅니다.
* [Azure Portal를 사용 하 여 연결 모니터 (미리 보기)를 만드는 방법을](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview-create-using-portal) 알아봅니다.
