---
title: 백업 탐색기를 사용 하 여 백업 모니터링
description: 이 문서에서는 백업 탐색기를 사용 하 여 자격 증명 모음, 구독, 지역 및 테 넌 트 간 백업에 대 한 실시간 모니터링을 수행 하는 방법을 설명 합니다.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: b65f68e33b53dff341ee72f6b9e9f42e344c49b1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149578"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>백업 탐색기를 사용 하 여 백업 모니터링

조직에서 더 많은 컴퓨터를 클라우드에 백업할 때 이러한 백업을 효율적으로 모니터링 하는 것이 점점 더 중요 해지고 있습니다. 시작 하는 가장 좋은 방법은 중앙 위치를 사용 하 여 많은 공간에서 작업 정보를 보는 것입니다.

Backup 탐색기는 고객이 중앙의 단일 위치를 Azure Backup 수 있도록 하는 기본 제공 Azure Monitor 통합 문서입니다. Backup 탐색기를 사용 하면 테 넌 트, 위치, 구독, 리소스 그룹 및 자격 증명 모음에 걸친 Azure의 전체 백업 공간에서 작업 활동을 모니터링할 수 있습니다. 광범위 한 백업 탐색기는 다음과 같은 기능을 제공 합니다.

* **크기 조정**: 전체 공간에서 백업 하도록 아직 구성 되지 않은 백업 항목, 작업, 경고, 정책 및 리소스에 대 한 집계 보기를 가져옵니다. 
* **드릴 다운 분석**: 각 작업, 경고, 정책 및 백업 항목에 대 한 자세한 정보를 한 곳에 표시 합니다.
* 실행 가능한 **인터페이스**: 문제를 식별 한 후 관련 백업 항목 또는 Azure 리소스로 원활 하 게 이동 하 여 문제를 해결할 수 있습니다.

이러한 기능은 Azure 리소스 그래프 및 Azure Monitor 통합 문서와의 네이티브 통합을 통해 기본적으로 제공 됩니다.

> [!NOTE]
> * Backup 탐색기는 현재 Azure Vm (가상 컴퓨터) 데이터에만 사용할 수 있습니다.
> * Backup 탐색기는 지난 7 일간의 백업에 대 한 정보를 보기 위한 작업 대시보드 (최대)입니다.
> * 현재 백업 탐색기 템플릿 사용자 지정은 지원 되지 않습니다. 
> * Azure 리소스 그래프 데이터에 사용자 지정 자동화을 작성 하지 않는 것이 좋습니다.

## <a name="get-started"></a>시작

Recovery Services 자격 증명 모음 중 하나로 이동 하 고 **개요** 창에서 **backup 탐색기** 링크를 선택 하 여 백업 탐색기에 액세스할 수 있습니다.

![자격 증명 모음 빠른 링크](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

이 링크를 선택 하면 액세스 권한이 있는 모든 자격 증명 모음 및 구독에 대 한 집계 보기를 제공 하는 백업 탐색기가 열립니다. Azure Lighthouse 계정을 사용 하는 경우 액세스 권한이 있는 모든 테 넌 트의 데이터를 볼 수 있습니다. 자세한 내용은이 문서의 끝에 있는 "테 넌 트 간 뷰" 섹션을 참조 하세요.

![백업 탐색기 방문 페이지](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>백업 탐색기 사용 사례

백업 탐색기에는 각각 특정 백업 아티팩트 (예: 백업 항목, 작업 또는 정책)에 대 한 자세한 정보를 제공 하는 여러 탭이 표시 됩니다. 이 섹션에서는 각 탭에 대 한 간략 한 개요를 제공 합니다. 비디오는 사용 가능한 컨트롤에 대 한 설명과 함께 각 백업 아티팩트의 샘플 사용 사례를 제공 합니다.

### <a name="the-summary-tab"></a>요약 탭

**요약** 탭에서는 백업 공간에 대 한 전반적인 상태를 신속 하 게 파악할 수가 제공 됩니다. 예를 들어 보호 되 고 있는 항목의 수, 보호를 사용 하도록 설정 되지 않은 항목 수 또는 최근 24 시간 동안 성공한 작업 수를 볼 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>백업 항목 탭

구독, 자격 증명 모음 및 기타 특성을 기준으로 각 백업 항목을 필터링 하 고 볼 수 있습니다. 백업 항목의 이름을 선택 하 여 해당 항목에 대 한 Azure 창을 열 수 있습니다. 예를 들어 테이블에서 *X*항목에 대 한 마지막 백업이 실패 한 것을 확인할 수 있습니다. *X*를 선택 하면 주문형 백업 작업을 트리거할 수 있는 항목의 **백업** 창을 열 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>작업 탭

**작업** 탭을 선택 하 여 지난 7 일 동안 트리거된 모든 작업의 세부 정보를 볼 수 있습니다. 여기에서 *작업 작업*, *작업 상태*및 실패 한 작업에 대 한 *오류 코드* 를 기준으로 필터링 할 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>경고 탭

최근 7 일 동안 자격 증명 모음에 생성 된 모든 경고의 세부 정보를 보려면 **경고** 탭을 선택 합니다. 유형 (*백업 실패* 또는 *복원 실패*), 현재 상태 (*활성* 또는 *해결 됨*) 및 심각도 (*위험*, *경고*또는 *정보*)를 기준으로 경고를 필터링 할 수 있습니다. Azure VM으로 이동 하 여 필요한 작업을 수행할 수 있는 링크를 선택할 수도 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>정책 탭

**정책** 탭을 선택 하 여 백업 공간에 대해 만들어진 모든 백업 정책에 대 한 주요 정보를 볼 수 있습니다. 정책에 지정 된 보존 범위 및 백업 빈도와 함께 각 정책과 연결 된 항목 수를 볼 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>백업 사용 안 함 탭

보호가 필요한 모든 컴퓨터에 대해 백업을 사용 하도록 설정 해야 합니다. 백업 관리자는 백업 탐색기를 사용 하 여 조직에서 아직 백업으로 보호 되지 않은 컴퓨터를 신속 하 게 식별할 수 있습니다. 이 정보를 보려면 **백업 사용 안 함** 탭을 선택 합니다.

**백업 사용 안** 함 창에는 보호 되지 않은 컴퓨터 목록이 있는 테이블이 표시 됩니다. 조직에서는 다양 한 기능을 제공 하는 컴퓨터 또는 프로덕션 컴퓨터와 테스트 컴퓨터에 다른 태그를 할당할 수 있습니다. 각 컴퓨터 클래스는 별도의 백업 정책을 필요로 하기 때문에 태그를 기준으로 필터링 하 여 각각에 해당 하는 정보를 볼 수 있습니다. 컴퓨터 이름을 선택 하면 해당 컴퓨터의 **백업 구성** 창으로 리디렉션됩니다. 여기서 적절 한 백업 정책을 적용 하도록 선택할 수 있습니다.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Excel로 내보내기

테이블이 나 차트의 내용을 Excel 스프레드시트로 내보낼 수 있습니다. 기존 필터를 적용 하 여 콘텐츠를 있는 그대로 내보냅니다. 추가 테이블 행을 내보내려면 각 탭의 위쪽에 있는 **페이지당 행** 수 드롭다운 목록을 사용 하 여 페이지에 표시할 행 수를 늘릴 수 있습니다.

## <a name="pin-to-the-dashboard"></a>대시보드에 고정

각 테이블 또는 차트의 맨 위에 있는 "고정" 아이콘을 선택 하 여 Azure Portal 대시보드에 고정할 수 있습니다. 이 정보를 고정 하면 가장 중요 한 정보를 표시 하는 맞춤형 대시보드를 만들 수 있습니다.

## <a name="cross-tenant-views"></a>다중 테 넌 트 뷰

여러 테 넌 트 환경에서 구독에 대 한 액세스를 위임 하는 Azure Lighthouse 사용자 인 경우 기본 구독 필터를 사용할 수 있습니다. Azure Portal의 오른쪽 위에 있는 "필터" 아이콘을 선택 하 여 데이터를 보려는 구독을 표시 합니다. 이 기능을 사용 하는 경우 백업 탐색기는 선택한 구독에서 모든 자격 증명 모음에 대 한 정보를 집계 합니다. 자세히 알아보려면 [Azure Lighthouse?](https://docs.microsoft.com/azure/lighthouse/overview)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure Monitor를 사용 하 여 백업 데이터에 대 한 통찰력을 얻는 방법에 대해 알아봅니다.](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
