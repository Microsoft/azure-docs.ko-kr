---
title: Ambari 및 Azure Monitor 로그를 사용 하 여 클러스터 가용성을 모니터링 하는 방법
description: Azure Monitor 로그 클러스터 상태 및 가용성을 모니터링 하 고 Ambari를 사용 하는 방법을 알아봅니다.
keywords: ambari, 모니터링, 로그 분석, 경고, 가용성, 상태 모니터링
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: tyfox
ms.openlocfilehash: 459de569916af14b0efea0ff08b92e5c93ed2369
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097048"
---
# <a name="how-to-monitor-cluster-availability-with-ambari-and-azure-monitor-logs"></a>Ambari 및 Azure Monitor 로그를 사용 하 여 클러스터 가용성을 모니터링 하는 방법

HDInsight 클러스터를 한눈에 보기 및 미리 정의 된 경고에 대 한 상태 정보를 제공 하는 Apache Ambari 뿐만 아니라 쿼리 가능한 메트릭 및 로그 뿐만 아니라 구성 가능한 알림을 제공 하는 Azure Monitor 로그 통합을 모두 포함 됩니다.

이 문서는 이러한 도구를 사용 하 여 클러스터를 모니터링 하는 방법을 보여 줍니다 하 고 Ambari 경고를 구성, 노드 가용성 비율을 모니터링 및 하나 이상의 노드에서 하트 비트를 받지 못한 경우 발생 하는 Azure Monitor 경고를 만들기에 대 한 몇 가지 예제를 안내 5 시간입니다.

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>대시보드

Ambari 대시보드에 클릭 하 여 액세스할 수 있습니다 합니다 **Ambari 홈** 링크를 **클러스터 대시보드** HDInsight 개요 블레이드에서 표시 된 것 처럼 Azure portal에서 아래 부분입니다. 또는 브라우저에서 다음 URL을 입력 하 여 액세스할 수 있습니다 [https://\<clustername\>. azurehdinsight.net](https://clustername.azurehdinsight.net/)

![HDInsight 리소스 포털 보기](media/hdinsight-cluster-availability/portal-overview.png)

클러스터 로그인 사용자 이름 및 암호에 대 한 라는 메시지가 표시 됩니다. 자격 증명을 입력 클러스터를 만들 때 선택한 있습니다.

다음 몇 가지 HDInsight 클러스터의 상태에 대 한 빠른 개요를 제공 하는 메트릭 표시 하는 위젯을 포함 하는 Ambari 대시보드로 이동 합니다. 이러한 위젯에 표시 라이브 DataNodes (작업자 노드) 및 JournalNodes (zookeeper 노드) 수와 같은 메트릭 (헤드 노드) Namenode 가동 시간, 특정 클러스터 형식에 특정 잘 메트릭으로 같은 Spark 및 Hadoop 클러스터에 대 한 YARN ResourceManager 가동 시간입니다.

![Ambari 대시보드](media/hdinsight-cluster-availability/ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>호스트-개별 노드 상태 보기

또한 개별 노드에 대 한 상태 정보를 볼 수 있습니다. 클릭 합니다 **호스트** 클러스터에서 모든 노드의 목록을 확인 하 고 각 노드에 대 한 기본 정보를 확인 하는 탭 합니다. 각 노드 이름의 왼쪽에 녹색 확인 표시는 모든 구성 요소가 시작 되어 노드를 나타냅니다. 구성 요소 노드에 다운 된 경우 녹색 확인 하는 대신 빨간색 경고 삼각형을 표시 됩니다.

![Ambari 호스트 보기](media/hdinsight-cluster-availability/ambari-hosts.png)

클릭할 수 있습니다 합니다 **이름을** 자세한 내용을 보려면 해당 특정 노드의 용 호스트 메트릭 보기에 노드. 이 보기는 각 개별 구성 요소 상태/있는지를 보여 줍니다.

![Ambari 호스트 단일 노드 보기](media/hdinsight-cluster-availability/ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari 경고

Ambari는 또한 특정 이벤트에 대 한 알림을 제공할 수 있는 몇 가지 구성 가능한 경고를 제공 합니다. 경고가 트리거될 때 경고 수를 포함 하는 빨간색 배지에서 Ambari의 왼쪽 위 모서리에 표시 됩니다. 이 배지를 클릭 하면 현재 경고 목록을 보여 줍니다.

![Ambari 경고 개수](media/hdinsight-cluster-availability/ambari-alerts.png)

경고 정 및 해당 상태 목록을 보려면 클릭 합니다 **경고** 아래와 같이 탭 합니다.

![Ambari 경고 정의 보기](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari 제공, 가용성과 관련 된 많은 미리 정의 된 경고 포함:

| 경고 이름                        | 설명                                                                                                                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DataNode 상태 요약           | 이 서비스 수준 경고가 표시 될 경우 비정상 DataNodes                                                                                                                |
| NameNode 높은 가용성 상태 | NameNode 활성 또는 대기 NameNode 실행 하지 않는 경우이 서비스 수준 경고가 트리거됩니다.                                                                              |
| 사용 가능한 백분율 JournalNodes    | 이 경고 수가 JournalNodes 아래로 클러스터에 구성 된 위험 임계값 보다 큰 경우 트리거됩니다. JournalNode 프로세스 검사 결과를 집계 합니다. |
| 사용 가능한 백분율 DataNodes       | 이 경고 수가 DataNodes 아래로 클러스터에 구성 된 위험 임계값 보다 큰 경우 트리거됩니다. DataNode 프로세스 검사 결과를 집계 합니다.       |

Ambari의 전체 목록은 경고 도움말을 모니터링 하는 클러스터의 가용성을 확인할 수 있습니다 [여기](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui),

경고에 대 한 세부 정보를 보거나 기준을 수정 하려면 클릭 합니다 **이름을** 경고 합니다. 걸릴 **DataNode 상태 요약** 예입니다. 경고 뿐만 아니라는 조건에 대 한 확인 간격와 '경고' 또는 'critical' 경고를 트리거하는 특정 조건에 대 한 설명을 볼 수 있습니다. 구성을 편집 하려면 클릭 합니다 **편집** 구성 상자의 오른쪽 위 모서리에 있는 단추입니다.

![Ambari 경고 구성](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

여기에서는 설명 및 무엇 보다도 확인을 편집할 수 있습니다 간격 및 경고 또는 위험 경고에 대 한 임계값입니다.

![Ambari 경고 구성 편집 보기](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

이 예제에서는 1 비정상 DataNode만 트리거 경고 및 중요 한 경고를 트리거하는 2 개의 비정상 DataNodes를 만들 수 있습니다. 클릭 **저장할** 완료 되 면 편집 합니다.

### <a name="email-notifications"></a>전자 메일 알림

또한 필요에 따라 Ambari 경고에 대 한 전자 메일 알림을 구성할 수 있습니다. 이렇게 하려면에 있는 경우는 **경고** 탭을 클릭 합니다 **작업** 단추는 왼쪽 위, 다음 **알림을 관리 합니다.**

![Ambari 관리 알림 동작](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

경고 알림 관리에 대 한 대화 상자가 열립니다. 클릭 합니다 **+** 아래쪽의 대화 상자 및 서버 세부 정보를 전자 메일을 보낼 전자 메일을 사용 하 여 Ambari를 제공 하는 필수 필드를 입력 합니다.

> [!TIP]
> Ambari를 설정 하 여 전자 메일 알림을 여러 HDInsight 클러스터를 관리 하는 경우 한 곳에서 경고를 수신 하는 좋은 방법은 수 있습니다.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor 로그 통합

Azure Monitor에 데이터를 수집 하 고 통합 된 모니터링 환경을 위해 한곳에서 집계를 HDInsight 클러스터와 같은 여러 리소스에서 생성 된 기록 합니다.

필수 요소로, 수집 된 데이터를 저장할 Log Analytics 작업 영역을 사용 해야 합니다. 하지 이미 만든 경우 하나, 여기의 지침을 따를 수 있습니다. [Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)합니다.

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Monitor 로그 통합을 사용 하도록 설정

포털에서 HDInsight 클러스터 리소스 페이지를 클릭 합니다 **Operations Management Suite** 블레이드입니다. 클릭 **사용** 드롭다운 목록에서 Log Analytics 작업 영역을 선택 합니다.

![HDInsight Operations Management Suite 블레이드](media/hdinsight-cluster-availability/portal-enable-oms.png)

### <a name="query-metrics-and-logs-tables-in-the-logs-blade"></a>로그 블레이드에서 메트릭 및 로그 테이블 쿼리

Azure Monitor 로그 통합을 사용 하도록 설정 (몇 분 정도 걸릴 수 있음)을 이동할 하 **Log Analytics 작업 영역** 리소스를 클릭 합니다 **로그** 블레이드

![Log Analytics 작업 영역 로그 블레이드](media/hdinsight-cluster-availability/portal-logs.png)

합니다 **로그** 블레이드와 같은 다양 한 샘플 쿼리를 나열 합니다.

| 쿼리 이름                      | 설명                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 현재 컴퓨터 가용성    | 차트 1 시간 마다 로그를 전송 하는 컴퓨터 수                     |
| 목록 하트 비트                 | 지난 1 시간에서 모든 컴퓨터 하트 비트를 나열 합니다.                           |
| 각 컴퓨터의 마지막 하트 비트 | 각 컴퓨터에서 보낸 마지막 하트 비트를 보여 줍니다.                             |
| 사용할 수 없는 컴퓨터           | 지난 5 시간 동안에서 하트 비트를 보내지 않은 모든 알려진된 컴퓨터 목록 |
| 가용성 비율               | 연결 된 각 컴퓨터의 가용성 계산                |

예를 들어, 실행 합니다 **가용성 비율** 를 클릭 하 여 샘플 쿼리 **실행** 위의 스크린샷에 나와 있는 것 처럼 해당 쿼리를 합니다. 클러스터에서 각 노드의 가용성 속도 표시이 백분율로 표시 됩니다. 동일한 Log Analytics 작업 영역에 메트릭을 보내도록 여러 HDInsight 클러스터를 사용 하는 경우 가용성 속도 표시 하는 해당 클러스터의 모든 노드에 대해 표시 됩니다.

![로그 분석 작업 영역 로그 블레이드에서 '가용성 속도' 샘플 쿼리](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE] 
> 클러스터 정확한 공급 요금을 확인 하기 전 적어도 24 시간 동안 실행 해야 하므로 24 시간 동안 가용성 율을 측정 합니다.

클릭 하 여이 테이블에는 공유 대시보드에 고정할 수 있습니다 **Pin** 오른쪽 위 모퉁이에서. 쓰기 가능한 공유 대시보드가 만들어야 하는 방법을 볼 수 있습니다 여기서: [Azure portal에서 대시보드 만들기 및 공유](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-a-dashboard-and-manage-access-control)합니다.

### <a name="azure-monitor-alerts"></a>Azure Monitor 경고

또한 설정한 Azure Monitor 경고를 트리거하는 메트릭의 값 또는 특정 조건을 충족 하는 쿼리의 결과입니다. 예를 들어 하나 이상의 노드 5 시간에서 하트 비트가 전송 되지 않은 경우 전자 메일을 보내도록 경고를 만들어 보겠습니다 (즉,으로 간주 되어 사용할 수 없습니다).

**로그** 블레이드를 실행 합니다 **사용할 수 없는 컴퓨터** 를 클릭 하 여 샘플 쿼리 **실행** 아래와 같이 해당 쿼리를 합니다.

![로그 분석 작업 영역 로그 블레이드에서 '사용할 수 없는 컴퓨터' 샘플 쿼리](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

모든 노드를 사용할 수 있으면이 쿼리는 이제 0 개 결과 반환 해야 합니다. 클릭 **새 경고 규칙** 이 쿼리에 대 한 경고를 구성 하려면.

![Log Analytics 작업 영역 새 경고 규칙](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

경고에 대 한 구성 요소가 세 가지가: 합니다 *리소스* (Log Analytics 작업 영역이 경우에서), 규칙을 만들려는 합니다 *조건* 경고를 트리거할 수 및 *작업 그룹*  를 결정 하는 경고가 트리거될 때 발생 합니다.

클릭 합니다 **제목 조건**신호 논리 구성을 완료 하려면 아래 표시 된 것 처럼 합니다.

![경고 규칙 조건](media/hdinsight-cluster-availability/portal-condition-title.png)

열립니다는 **신호 논리 구성** 블레이드입니다.

설정 된 **경고 논리** 다음과 같은 섹션:

*기준으로 합니다. 조건 결과 수: 임계값 보다 큽니다. 0.*

이 쿼리만를 반환 하므로 사용할 수 없는 노드 결과로, 결과 수를 그 어느 때 보다 크면 0 경고 발생 해야 합니다.

에 **평가에 따라** 으로 설정 합니다 **기간** 및 **빈도** 사용할 수 없는 노드에 대해 확인 하려는 빈도에 따라 합니다.

이 경고를 위해 되도록 해야 하는 참고 **기간 = 빈도입니다.** 자세한 내용은 기간, 빈도 및 기타 경고 매개 변수를 찾을 수 있습니다 [여기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)합니다.

클릭 **수행** 신호 논리 구성 했으면 합니다.

![경고 규칙 신호 논리 구성](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

기존 작업 그룹을 아직 없는 경우 클릭 **새로 만들기** 아래의 합니다 **작업 그룹** 섹션입니다.

![경고 규칙 새 작업 그룹](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

열립니다는 **작업 그룹 추가** 블레이드입니다. 선택는 **작업 그룹 이름**를 **짧은 이름**를 **구독**, 및 **리소스 그룹입니다.** 아래는 **작업** 섹션을 선택는 **작업 이름** 선택한 **이메일/S/푸시/음성** 으로 **동작 유형입니다.**

> [!NOTE]
> 여러 가지 다른 작업을 전자 메일/S/푸시/음성, 같은 Azure 함수, 논리 앱, 웹 후크, ITSM 및 Automation Runbook 외에도 경고를 트리거할 수 있습니다. [더 알아보세요.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

열립니다는 **전자 메일/S/푸시/음성** 블레이드입니다. 선택는 **이름을** 수신자에 대 한 **확인** 는 **전자 메일** 경고 전송 하려는 전자 메일 주소를 입력 합니다. 클릭 **확인** 에 **이메일/S/푸시/음성** 블레이드에서 그런 다음 합니다 **작업 그룹 추가** 블레이드에서 작업 그룹 구성을 완료 하려면.

![경고 규칙 작업 그룹 추가](media/hdinsight-cluster-availability/portal-add-action-group.png)

이 블레이드를 닫습니다. 아래에 나열 된 작업 그룹 표시 되어야 합니다 **작업 그룹** 섹션. 마지막으로 완료 합니다 **경고 세부 정보** 섹션을 입력 하 여는 **경고 규칙 이름** 및 **설명** 선택 하 고를 **심각도**합니다.
클릭 **경고 규칙 만들기** 완료 합니다.

![마침 경고 규칙 만들기](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> 지정 하는 기능 **심각도** 는 여러 개의 경고를 만들 때 사용할 수 있는 강력한 도구입니다. 예를 들어, 아래로 단일 헤드 노드를 이동 하는 경우 (심각도 1) 경고를 발생 시키는 하나의 경고를 만들 수 있습니다 및 위험 (심각도 0) 두 헤드 노드는 예기치 않은 이벤트가 발생 하는 다른 경고 이동 합니다.

이 경고에 대 한 조건이 충족 되 면 경고 하면 발생 하 고 다음과 같은 경고 세부 정보를 사용 하 여 전자 메일을 받게 됩니다.

![Azure Monitor 경고 전자 메일](media/hdinsight-cluster-availability/alert-email.png)

발생으로 이동 하 여 심각도 별로 그룹화 하는 모든 경고를 볼 수는 **경고** 블레이드에서 하 **Log Analytics 작업 영역**합니다.

![Log Analytics 작업 영역 경고](media/hdinsight-cluster-availability/portal-alerts.png)

심각도 그룹화를 클릭 하면 (예: **심각도 1** 위에 강조 표시 된 대로) 아래와 같은 한 해당 심각도의 모든 경고에 대 한 레코드를 표시 됩니다:

![Log Analytics 작업 영역 1 심각도 경고](media/hdinsight-cluster-availability/portal-alerts-sev-1.png)

## <a name="next-steps"></a>다음 단계
- [HDInsight에서 Apache Hadoop 클러스터의 가용성 및 안정성](hdinsight-high-availability-linux.md)
