---
title: Linux 기반 HDInsight에서 Apache Hadoop YARN 애플리케이션 로그에 액세스 - Azure
description: 명령줄과 웹 브라우저를 사용하여 Linux 기반 HDInsight(Apache Hadoop) 클러스터에서 YARN 애플리케이션 로그에 액세스하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 0a3411cc4cc32c3e54583ab81ee98f2e151d4384
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098442"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Linux 기반 HDInsight에서 Apache Hadoop YARN 애플리케이션 로그에 액세스

Azure HDInsight의 [Apache Hadoop](https://hadoop.apache.org/) 클러스터에서 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)(Yet Another Resource Negotiator) 애플리케이션의 로그에 액세스하는 방법에 대해 알아봅니다.

> [!IMPORTANT]  
> 이 문서의 단계에는 Linux를 사용하는 HDInsight 클러스터가 필요합니다. Linux는 HDInsight 버전 3.6 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [HDInsight 구성 요소 버전 관리](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

## <a name="YARNTimelineServer"></a>YARN Timeline Server

[Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html)는 완료된 애플리케이션에 대한 일반 정보를 제공합니다.

YARN Timeline Server는 다음과 같은 형식의 데이터를 포함합니다.

* 애플리케이션의 고유한 식별자인 애플리케이션 ID
* 애플리케이션을 시작한 사용자
* 애플리케이션을 완료하려고 시도한 횟수
* 지정된 애플리케이션 시도에 사용된 컨테이너

## <a name="YARNAppsAndLogs"></a>YARN 애플리케이션 및 로그

YARN은 여러 프로그래밍 모델(예: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html))을 지원하여 리소스 관리를 애플리케이션 예약/모니터링과 분리합니다. YARN은 *리소스 관리자*(RM), 작업자 노드별 *노드 관리자*(NM) 및 애플리케이션별 *애플리케이션 마스터*(AM)를 사용합니다. 애플리케이션별 AM은 애플리케이션을 실행하기 위한 리소스(CPU, 메모리, 디스크, 네트워크)를 RM과 협상합니다. RM은 NM과 협력하여 이러한 리소스를 부여하며, 이 리소스는 *컨테이너는*로 부여됩니다. AM은 RM에 의해 부여받은 컨테이너의 진행률 추적합니다. 애플리케이션의 특성에 따라 애플리케이션에 여러 컨테이너가 필요할 수 있습니다.

각 애플리케이션은 여러 *애플리케이션 시도*로 구성될 수 있습니다. 애플리케이션이 실패할 경우 새 시도로 다시 시도할 수 있습니다. 각 시도는 하나의 컨테이너에서 실행됩니다. 어떤 의미에서 컨테이너는 YARN 애플리케이션에서 수행되는 작업의 기본 단위에 대한 컨텍스트를 제공합니다. 컨테이너의 컨텍스트 내에서 수행되는 모든 작업은 컨테이너가 할당되는 단일 작업자 노드에서 수행됩니다. 자세한 내용은 [Apache Hadoop YARN 개념][YARN-concepts]을 참조하세요.

애플리케이션 로그(및 연관된 컨테이너 로그)는 문제가 있는 Hadoop 애플리케이션을 디버깅하는 데 매우 중요합니다. YARN은 [로그 집계][log-aggregation] 기능을 사용하여 애플리케이션 로그를 수집, 집계 및 저장하기 위한 유용한 프레임워크를 제공합니다. 로그 집계 기능을 사용하면 애플리케이션 로그에 좀 더 확실하게 액세스할 수 있습니다. 이 기능은 작업자 노드의 모든 컨테이너에서 로그를 집계한 후 작업자 노드당 하나의 집계된 로그 파일로 저장합니다. 로그는 애플리케이션이 완료된 후 기본 파일 시스템에 저장됩니다. 애플리케이션은 수백 수천 개의 컨테이너를 사용할 수 있지만 단일 작업자 노드에서 실행되는 모든 컨테이너에 대한 로그는 항상 단일 파일로 집계됩니다. 따라서 애플리케이션에서 사용하는 작업자 노드당 하나의 로그만 있습니다. 로그 집계는 HDInsight 클러스터 버전 3.0 이상에서 기본적으로 사용됩니다. 집계된 로그는 클러스터에 대한 기본 스토리지에 있습니다. 다음 경로는 로그에 대한 HDFS 경로입니다.

    /app-logs/<user>/logs/<applicationId>

경로에서 `user`는 애플리케이션을 시작한 사용자의 이름입니다. `applicationId`는 YARN RM에서 애플리케이션에 할당한 고유 식별자입니다.

집계된 로그는 컨테이너별로 인덱싱된 [이진 형식][binary-format]인 [TFile][T-file]로 작성되므로 직접 읽을 수 없습니다. 관심 있는 애플리케이션 또는 컨테이너에 대한 이러한 로그를 일반 텍스트로 보려면 YARN ResourceManager 로그 또는 CLI 도구를 사용합니다.

## <a name="yarn-cli-tools"></a>YARN CLI 도구

YARN CLI 도구를 사용하려면 먼저 SSH를 사용하여 HDInsight 클러스터에 연결해야 합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

이러한 로그를 다음 명령 중 하나를 실행하여 일반 텍스트로 볼 수 있습니다.

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

이러한 명령을 실행할 때 &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> 및 &lt;worker-node-address> 정보를 지정합니다.

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI는 클러스터 헤드 노드에서 실행됩니다. Ambari 웹 UI를 통해 액세스됩니다. 다음 단계를 사용하여 YARN 로그를 봅니다.

1. 웹 브라우저에서 https://CLUSTERNAME.azurehdinsight.net으로 이동합니다. CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.
2. 왼쪽에 있는 서비스 목록에서 **YARN**을 선택합니다.

    ![선택한 Yarn 서비스](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. **빠른 링크** 드롭다운에서 클러스터 헤드 노드 중 하나를 선택한 다음 **ResourceManager 로그**를 선택합니다.

    ![Yarn 빠른 링크](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    YARN 로그에 대한 링크 목록이 나타납니다.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
