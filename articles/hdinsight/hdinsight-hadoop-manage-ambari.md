---
title: Ambari 웹 UI를 사용하여 Azure HDInsight 모니터링 및 관리
description: Ambari를 사용하여 Linux 기반 HDInsight 클러스터를 모니터링하고 관리하는 방법에 대해 알아봅니다. 이 문서에서는 HDInsight 클러스터에 포함된 Ambari 웹 UI를 사용하는 방법을 배웁니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 1659ab72620b6bf91eb932f8414a0f6600350e37
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761105"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Apache Ambari Web UI를 사용하여 HDInsight 클러스터 관리

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari는 손쉬운 Web UI 및 REST API 사용을 제공하여 Apache Hadoop 클러스터의 관리 및 모니터링을 간소화합니다. Ambari는 Linux 기반 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는데 사용됩니다.

이 문서에서는 HDInsight 클러스터와 Ambari 웹 UI를 사용하는 방법을 배웁니다.

## <a id="whatis"></a>Apache Ambari란?

[Apache Ambari](https://ambari.apache.org)는 사용하기 쉬운 웹 UI를 제공하여 Hadoop 관리를 단순화합니다. Ambari를 사용하여 Hadoop 클러스터를 관리 및 모니터링할 수 있습니다. 개발자는 [Ambari REST API](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 사용하여 자신의 애플리케이션에 이러한 기능을 통합할 수 있습니다.

Ambari 웹 UI는 기본적으로 Linux 운영 체제를 사용하는 HDInsight 클러스터와 함께 제공됩니다.

> [!IMPORTANT]  
> Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요. 

## <a name="connectivity"></a>연결

Ambari 웹 UI는 HTTPS://CLUSTERNAME.azurehdinsight.net의 HDInsight 클러스터에서 사용할 수 있습니다. 여기서 **CLUSTERNAME**은 클러스터 이름입니다.

> [!IMPORTANT]  
> HTTPS를 요구하는 HDInsight에서 Ambari로 연결 인증에 대한 대화 상자가 나타나면 클러스터를 만들 때 제공한 관리자 계정 이름 및 암호를 사용합니다.

## <a name="ssh-tunnel-proxy"></a>SSH 터널(프록시)

클러스터의 Ambari는 인터넷을 통해 직접 액세스할 수 있지만 Ambari 웹 UI의 일부 링크(예: JobTracker)는 인터넷에 노출되지 않습니다. 이러한 서비스에 액세스하려면 SSH 터널을 만들어야 합니다. 자세한 내용은 [HDInsight와 SSH 터널링 사용](hdinsight-linux-ambari-ssh-tunnel.md)을 참조하세요.

## <a name="ambari-web-ui"></a>Ambari 웹 UI

> [!WARNING]  
> Ambari 웹 UI의 일부 기능만 HDInsight에서 지원됩니다. 자세한 내용은 이 문서의 [지원되지 않는 작업](#unsupported-operations) 섹션을 참조하세요.

Ambari 웹 UI를 연결할 때 페이지에 인증하라는 메시지가 나옵니다. 클러스터를 만들 때 사용했던 클러스터 관리자 사용자(기본값 관리자)와 암호를 사용합니다.

페이지가 열리면 위쪽의 표시줄을 확인합니다. 이 표시줄에는 다음 정보 및 컨트롤이 포함되어 있습니다.

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Ambari 로고** - 클러스터를 모니터링하는 데 사용할 수 있는 대시보드를 엽니다.

* **클러스터 이름 # ops** - 진행 중인 Ambari 작업 수를 표시합니다. 클러스터 이름 또는 **# ops**를 선택하면 백그라운드 작업 목록이 표시됩니다.

* **# alerts** - 클러스터에 대한 경고 또는 중요한 알림(있을 경우)을 표시합니다.

* **Dashboard** - 대시보드를 표시합니다.

* **Services** - 클러스터의 서비스에 대한 정보 및 구성 설정입니다.

* **Hosts** - 클러스터의 노드에 대한 정보 및 구성 설정입니다.

* **Alerts** - 정보, 경고 및 중요한 알림에 대한 로그입니다.

* **Admin** - 클러스터에 설치된 소프트웨어 스택/서비스, 서비스 계정 정보 및 Kerberos 보안입니다.

* **Admin 단추** - Ambari 관리, 사용자 설정 및 로그 아웃입니다.

## <a name="monitoring"></a>모니터링

### <a name="alerts"></a>경고

다음 목록은 Ambari에서 사용하는 일반적인 경고 상태를 포함합니다.

* **OK**
* **Warning**
* **CRITICAL**
* **UNKNOWN**

**확인**이 아닌 다른 경고는 페이지 위쪽의 **# alerts** 항목에 경고 수를 표시합니다. 이 항목을 선택하면 경고 및 해당 상태가 표시됩니다.

경고는 여러 가지 기본 그룹으로 구성되며 **Alerts** 페이지에서 볼 수 있습니다.

![경고 페이지](./media/hdinsight-hadoop-manage-ambari/alerts.png)

**작업** 메뉴를 사용하고 **경고 그룹 관리**를 선택하여 그룹을 관리할 수 있습니다.

![경고 그룹 관리 대화 상자](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

또한 __경고 알림 관리__를 선택하여 **작업** 메뉴에서 경고 방법을 관리하고 경고 알림을 만들 수도 있습니다. 모든 현재 알림이 표시됩니다. 여기에서 알림을 만들 수도 있습니다. 특정 경고/심각도 조합이 발생하면 **전자 메일** 또는 **SNMP**를 통해 알림을 보낼 수 있습니다. 예를 들어 **YARN Default** 그룹에 **위험**으로 설정된 경고가 있으면 전자 메일 메시지를 보낼 수 있습니다.

![경고 만들기 대화 상자](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

마지막으로, __작업__ 메뉴에서 __경고 설정 관리__를 선택하면 알림을 보내기 전에 경고가 발생해야 하는 횟수를 지정할 수 있습니다. 일시적인 오류에 대한 알림을 방지하는 데 이 설정을 사용할 수 있습니다.

### <a name="cluster"></a>프로비전

대시보드의 **Metrics** 탭은 클러스터의 상태를 한 눈에 쉽게 모니터할 수 있는 일련의 위젯을 포함합니다. **CPU Usage**와 같은 여러 위젯은 클릭하면 추가 정보를 제공합니다.

![메트릭이 표시된 대시보드](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**Heatmaps** 탭은 녹색에서 빨간색으로 변하는 히트맵처럼 메트릭을 표시합니다.

![히트맵이 표시된 대시보드](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

클러스터 내의 노드에 대한 자세한 내용은 **호스트**를 선택합니다. 그런 다음 관심 있는 특정 노드를 선택합니다.

![호스트 세부 정보](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Services

대시보드의 **Services** 세로 막대는 클러스터에서 실행되는 서비스 상태에 대한 빠른 정보를 제공합니다. 다양한 아이콘은 수행해야 하는 상태 또는 작업을 나타내는 데 사용됩니다. 예를 들어 서비스가 재활용되어야 하는 경우 노란색 재활용 기호가 표시됩니다.

![서비스 세로 막대](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> 표시된 서비스는 HDInsight 클러스터 유형과 버전 간에 다릅니다. 여기에 표시된 서비스는 클러스터에 대해 표시된 서비스와 다를 수 있습니다.

서비스를 선택하면 해당 서비스에 대한 자세한 정보가 표시됩니다.

![서비스 요약 정보](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>빠른 링크

일부 서비스는 페이지의 위쪽에 **Quick Links** 링크를 표시합니다. 이를 사용하여 서비스 관련 웹 UI에 액세스할 수 있습니다.

* **Job History** - MapReduce 작업 기록
* **Resource Manager** - YARN ResourceManager UI
* **NameNode** - HDFS(Hadoop Distributed File System) NameNode UI
* **Oozie Web UI** - Oozie UI

이러한 링크 중 하나를 선택하면 브라우저에서 선택한 페이지를 표시하는 새 탭이 열립니다.

> [!NOTE]  
> 서비스에 대해 **빠른 링크** 항목을 선택하면 "서버를 찾을 수 없음" 오류를 반환할 수 있습니다. 이 오류가 발생하는 경우 이 서비스에 대해 **빠른 링크** 항목을 사용할 때 SSH 터널을 사용해야 합니다. 자세한 내용은 [HDInsight와 SSH 터널링 사용](hdinsight-linux-ambari-ssh-tunnel.md)을 참조하세요.

## <a name="management"></a>관리

### <a name="ambari-users-groups-and-permissions"></a>Ambari 사용자, 그룹 및 사용 권한

[도메인 가입](./domain-joined/apache-domain-joined-introduction.md) HDInsight 클러스터를 사용할 때 사용자, 그룹 및 권한을 사용하는 작업이 지원됩니다. 도메인 가입 클러스터에서 Ambari 관리 UI를 사용하는 방법에 대한 자세한 내용은 [도메인 가입 HDInsight 클러스터 관리](./domain-joined/apache-domain-joined-introduction.md)를 참조하세요.

> [!WARNING]  
> Linux 기반 HDInsight 클러스터에서 Ambari watchdog(hdinsightwatchdog)의 암호는 변경하지 마세요. 암호를 변경하면 스크립트 동작을 사용하거나 클러스터에서 크기 조정 작업을 수행하는 기능이 중단됩니다.

### <a name="hosts"></a>호스트

**Hosts** 페이지는 클러스터의 모든 호스트를 나열합니다. 호스트를 관리하려면 다음 단계를 따르세요.

![호스트 페이지](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> 호스트 추가, 서비스 해제 및 서비스 등록은 HDInsight 클러스터에서 사용할 수 없습니다.

1. 관리하려는 호스트를 선택합니다.

2. **Actions** 메뉴를 사용하여 수행할 작업을 선택합니다.

   * **Start all components** - 호스트에서 모든 구성 요소를 시작합니다.

   * **Stop all components** - 호스트에서 모든 구성 요소를 중지합니다.

   * **Restart all components** - 호스트에서 모든 구성 요소를 중지하고 시작합니다.

   * **Turn on maintenance mode** - 호스트에 대한 경고를 표시하지 않습니다. 경고를 생성하는 작업을 수행하는 경우 이 모드를 활성화해야 합니다. 예를 들어 서비스를 중지하고 시작합니다.

   * **Turn off maintenance mode** - 호스트를 정상 경고 상태로 되돌립니다.

   * **Stop** - 호스트에서 DataNode 또는 NodeManagers를 중지합니다.

   * **Start** - 호스트에서 DataNode 또는 NodeManagers를 시작합니다.

   * **Restart** - 호스트에서 DataNode 또는 NodeManagers를 중지하고 시작합니다.

   * **Decommission** - 호스트를 클러스터에서 제거합니다.

     > [!NOTE]  
     > HDInsight 클러스터에서는 이 작업을 사용하지 마세요.

   * **Recommission** - 이전에 서비스를 해지한 호스트를 클러스터에 추가합니다.

     > [!NOTE]  
     > HDInsight 클러스터에서는 이 작업을 사용하지 마세요.

### <a id="service"></a>Services

**대시보드** 또는 **서비스** 페이지에서 서비스 목록 아래쪽의 **작업** 단추를 사용하여 모든 서비스를 중지하고 시작합니다.

![Service Actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> 이 메뉴에 나열되어 있는 **서비스 추가**는 HDInsight 클러스터에 서비스를 추가하는 데 사용하면 안됩니다. 클러스터를 프로비전하는 동안 스크립트 작업을 사용하여 새 서비스를 추가해야 합니다. 스크립트 작업에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

**Actions** 단추는 모든 서비스를 다시 시작할 수 있는 반면, 특정 서비스를 시작하거나 중지, 다시 시작하려는 경우가 많습니다. 다음 단계를 사용하여 개별 서비스에서 작업을 수행할 수 있습니다.

1. **대시보드** 또는 **서비스** 페이지에서 서비스를 선택합니다.

2. **요약** 탭 위쪽에서 **서비스 작업** 단추를 사용하여 수행할 작업을 선택합니다. 모든 노드의 서비스를 다시 시작합니다.

    ![서비스 작업](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > 클러스터가 실행되는 동안 일부 서비스를 다시 시작하면 경고가 발생할 수 있습니다. 경고를 방지하려면 **서비스 작업** 단추를 사용하여 다시 시작하기 전에 서비스에 대한 **유지 관리 모드**를 사용하도록 설정합니다.

3. 동작이 선택되어 있고, 백그라운드 작업이 발생하면 페이지 위쪽에 있는 **# op** 항목의 숫자가 증가합니다. 백그라운드 작업 목록을 표시하도록 구성한 경우 백그라운드 작업 목록이 표시됩니다.

   > [!NOTE]  
   > 서비스에 대한 **유지 관리 모드**를 사용하도록 설정하여 작업이 완료된 후에는 **서비스 작업** 단추를 사용하여 이 모드를 해제해야 합니다.

서비스를 구성하려면 다음 단계를 사용합니다.

1. **대시보드** 또는 **서비스** 페이지에서 서비스를 선택합니다.

2. **Configs** 탭을 선택합니다. 현재 구성이 표시됩니다. 이전 구성의 목록도 표시됩니다.

    ![구성](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. 구성을 수정하려면 표시된 목록을 클릭하고 **Save**를 선택합니다. 또는 이전 구성을 선택한 다음 **Make current** 를 선택하여 이전 설정으로 롤백할 수 있습니다.

## <a name="ambari-views"></a>Ambari 뷰

Ambari Views를 사용하면 개발자가 [Apache Ambari Views 프레임워크](https://cwiki.apache.org/confluence/display/AMBARI/Views)를 사용하여 Ambari 웹 UI에 UI 요소를 삽입할 수 있습니다. HDInsight은 Hadoop 클러스터 종류를 사용하여 다음 뷰를 제공합니다.


* Hive 보기: Hive 보기를 사용하면 웹 브라우저에서 직접 Hive 쿼리를 실행할 수 있습니다. 쿼리를 저장하고 결과 확인하며 클러스터 저장소에 결과를 저장하거나 로컬 시스템에 다운로드할 수 있습니다. Hive 보기 사용에 대한 자세한 내용은 [HDInsight와 함께 Apache Hive 보기 사용](hadoop/apache-hadoop-use-hive-ambari-view.md)을 참조하세요.

* Tez 보기: Tez 보기를 사용하면 작업을 더 잘 이해하고 최적화할 수 있습니다. Tez 작업 실행 방법 및 사용되는 리소스에 대한 정보를 볼 수 있습니다.

## <a name="unsupported-operations"></a>지원되지 않는 작업

다음 Ambari 작업은 HDInsight에서 지원되지 않습니다.

* __메트릭 수집기 서비스 이동__. 메트릭 수집기 서비스에 대한 정보를 볼 경우 서비스 작업 메뉴에서 사용할 수 있는 작업 중 하나는 __메트릭 수집기 이동__입니다. 이 기능은 HDInsight에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)를 사용하는 방법을 알아봅니다.
