---
title: Azure HDInsight에 타사 애플리케이션 설치
description: Azure HDInsight에 타사 Apache Hadoop 응용 프로그램을 설치 하는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 9643e1f77be400a89a8dfaecdc2f7e1205a1938b
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031318"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight에 타사 Apache Hadoop 애플리케이션 설치

Azure HDInsight에 타사 [Apache Hadoop](https://hadoop.apache.org/) 애플리케이션을 설치하는 방법을 알아봅니다. 사용자 고유의 애플리케이션을 설치하는 방법에 대한 지침은 [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.

HDInsight 애플리케이션은 HDInsight 클러스터에 사용자가 설치할 수 있는 애플리케이션입니다. Microsoft, ISV(독립 소프트웨어 공급 업체) 또는 사용자가 직접 이러한 애플리케이션을 개발할 수 있습니다.  

다음 목록에서는 게시된 애플리케이션을 보여 줍니다.

|애플리케이션 |클러스터 유형 | Description |
|---|---|---|
|[AtScale Intelligence 플랫폼](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop은 |AtScale은 HDInsight 클러스터를 스케일 아웃 OLAP 서버로 전환 하 여 Microsoft Excel, Power BI, Tableau Software에서 QlikView으로 이미 알고 있고 소유 하 고 있는 BI 도구를 사용 하 여 수십억 개의 데이터 행을 대화형으로 쿼리할 수 있도록 합니다. |
|[HDInsight 용 CDAP](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP는 Hadoop 값에 대 한 시간을 가속화 하 고 셀프 서비스 데이터를 제공할 수 있도록 하는 빅 데이터에 대 한 최초의 통합 플랫폼입니다. 오픈 소스 및 확장 가능, CDAP는 혁신의 장애물을 제거 합니다. 요구 사항: 4 개 지역 노드, 최소 D3 v2 |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop은 |분석을 위해 데이터를 준비 하 고, 탐색 하 고, 분석 하기 위해 데이터를 준비 하 고, 탐색 하 고, Datameer 하는 셀프 서비스 확장 가능 플랫폼은 복잡 한 다중 원본 데이터를 중요 한 비즈니스에 도움이 되는 정보로 전환 하 여 엔터프라이즈급에서 |
|[HDInsight의 Dataiku DSS](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |데이터 과학자와 데이터 분석가가 공동 작업을 수행 하 여 새 데이터 제품 및 서비스를 보다 효율적으로 디자인 하 고 실행할 수 있도록 하는 엔터프라이즈 데이터 과학 플랫폼의 Dataiku DSS는 원시 데이터를 효율적으로 사용할 수 있도록 설정 합니다. |
|[WANdisco Fusion HDI 앱](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, 폭풍, Kafka |분산 된 환경에서 데이터를 일관 되 게 유지 하는 것은 엄청난 데이터 작업 과제입니다. 엔터프라이즈급 소프트웨어 플랫폼인 WANdisco Fusion은 모든 환경에서 구조화 되지 않은 데이터 일관성을 사용 하 여이 문제를 해결 합니다. |
|[HDInsight 용 H2O SparklingWater](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling 물은 다음 분산 알고리즘을 지원 합니다. &AMP; m, Naive Bayes, Distributed 임의 포리스트, 그라데이션 향상 컴퓨터, 심층 신경망, 심층 학습, K, PCA, 일반화 된 낮은 순위 모델, 변칙 검색, Autoencoders. |
|[HDInsight에 대 한 실시간 데이터 통합을 위한 Striim](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, 스톰, Spark, Kafka |Striim ("stream"으로 발음)은 종단 간 스트리밍 데이터 통합 + 인텔리전스 플랫폼으로, 서로 다른 데이터 스트림의 연속 수집, 처리 및 분석을 가능 하 게 합니다. |
|[Jumbune Enterprise 가속화 데이터 분석](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |높은 수준에서 Jumbune는, 1을 통해 기업을 지원 합니다. Tez, MapReduce & Spark 엔진 기반 Hive, Java, Scala 워크 로드 성능을 가속화 합니다. 2. 사전 Hadoop 클러스터 모니터링, 3. 분산 파일 시스템에 데이터 품질 관리를 설정 하는 중입니다. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Apache Kylin에서 제공 하는 Kyligence Enterprise는 빅 데이터에 대해 BI를 사용 하도록 설정 합니다. Hadoop의 엔터프라이즈 OLAP 엔진인 Kyligence Enterprise는 업계 표준 데이터 웨어하우스 및 BI 방법론을 사용 하 여 Hadoop의 BI를 설계 하는 비즈니스 분석가의 역량을 강화 합니다. |
|[Azure HDInsight에 대 한 섬광 Presto](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop은 |Presto는 빠르고 확장 가능한 분산 SQL 쿼리 엔진입니다. 스토리지 및 컴퓨팅을 분리하기 위해 설계된 Presto는 Azure Data Lake Storage, Azure Blob Storage, SQL 및 NoSQL 데이터베이스와 기타 데이터 원본의 데이터를 쿼리하는 데 이상적입니다. |
|[HDInsight Cloud에 대 한 StreamSets 데이터 수집기](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets 데이터 수집기는 데이터를 실시간으로 스트리밍하는 간단 하 고 강력한 엔진입니다. 데이터 수집기를 사용 하 여 데이터 스트림에서 데이터를 라우팅하고 처리 합니다. 30 일 평가판 라이선스가 제공 됩니다. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise for HDInsight는 모든 규모의 데이터에 대해 엔터프라이즈급 데이터 랭 글 링를 지원 합니다. Azure에서 Trifacta를 실행 하는 비용은 Trifacta 구독 비용과 가상 컴퓨터에 대 한 Azure 인프라 비용의 조합입니다. |
|[가는 fi 데이터 플랫폼](https://unifisoftware.com/platform/) |Hadoop, HBase, 폭풍, Spark |전체 기능을 사용 하는 셀프 서비스 데이터 도구 제품군은 비즈니스 사용자가 증분 수익을 구동 하거나 비용 또는 운영 복잡성을 줄일 수 있는 데이터 문제를 처리할 수 있도록 설계 된 셀프 서비스 데이터 도구 제품군입니다. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |HDInsight Spark 클러스터용 하기가 Data 앱입니다. |
|[워터 라인 AI 기반 Data Catalog](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |워터 라인 카탈로그는 AI를 사용 하 여 데이터를 구성 하 고 제어 하 여 비즈니스 용어로 데이터를 자동으로 태그 합니다. Waterline의 비즈니스에 익숙한 카탈로그는 셀프 서비스 분석, 규정 준수 및 거버넌스, IT 관리 이니셔티브에 중요한 성공 구성 요소입니다. |

이 문서에서 제공하는 지침은 Azure Portal을 사용합니다. 또한 포털에서 Azure Resource Manager 템플릿을 내보내거나 공급 업체에서 Resource Manager 템플릿의 복사본을 가져오고 Azure PowerShell 및 Azure 클래식 CLI를 사용하여 템플릿을 배포할 수 있습니다.  [Resource Manager 템플릿을 사용하여 HDInsight에서 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
기존 HDInsight 클러스터에 HDInsight 애플리케이션을 설치하려면 HDInsight 클러스터가 있어야 합니다. HDInsight 클러스터를 만들려면 [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요. HDInsight 클러스터를 만들 경우 HDInsight 애플리케이션도 설치할 수 있습니다.

## <a name="install-applications-to-existing-clusters"></a>기존 클러스터에 애플리케이션 설치
다음 절차에서는 기존 HDInsight 클러스터에 HDInsight 애플리케이션을 설치하는 방법을 보여 줍니다.

**HDInsight 애플리케이션 설치**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스** > **분석** > **HDInsight 클러스터**로 이동 합니다.
3. 목록에서 HDInsight 클러스터를 선택 합니다.  HDInsight 클러스터가 없는 경우 만듭니다.  see [클러스터 만들기](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.
4. **설정** 범주 아래에서 **응용 프로그램**을 선택 합니다. 주 창에서 설치 된 응용 프로그램의 목록을 볼 수 있습니다. 
   
    ![HDInsight 애플리케이션 포털 메뉴](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. 메뉴에서 **+ 추가** 를 선택 합니다. 사용 가능한 응용 프로그램의 목록을 볼 수 있습니다.  **+ 추가** 가 회색으로 표시 되는 경우이 버전의 HDInsight 클러스터에 대 한 응용 프로그램이 없는 것입니다.
   
    ![HDInsight 애플리케이션 사용 가능한 애플리케이션](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. 사용 가능한 응용 프로그램 중 하나를 선택 하 고 지침에 따라 약관에 동의 합니다.

포털 알림에서 설치 상태를 확인할 수 있습니다 (포털 맨 위에 있는 종 모양 아이콘 선택). 애플리케이션이 설치되면 설치된 앱 목록에 표시됩니다.

## <a name="install-applications-during-cluster-creation"></a>클러스터 생성 중에 애플리케이션 설치

클러스터를 만들 때 HDInsight 애플리케이션을 설치하는 옵션이 있습니다. 클러스터가 만들어지고 실행 상태가 되면 프로세스 중에 HDInsight 애플리케이션이 설치됩니다. Azure Portal를 사용 하 여 클러스터를 만드는 동안 응용 프로그램을 설치 하려면 **구성 + 가격 책정** 탭에서 **+ 응용 프로그램 추가**를 선택 합니다.

![클러스터 구성 응용 프로그램 Azure Portal](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>설치된 HDInsight 앱 및 속성 나열
포털에서는 클러스터에 설치된 HDInsight 애플리케이션의 목록과 설치된 애플리케이션 각각의 속성을 보여 줍니다.

**HDInsight 애플리케이션 나열 및 속성 표시**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴에서 **모든 서비스** > **분석** > **HDInsight 클러스터**로 이동 합니다.
3. 목록에서 HDInsight 클러스터를 선택 합니다.
4. **설정** 범주 아래에서 **응용 프로그램**을 선택 합니다. 주 창에서 설치 된 응용 프로그램의 목록을 볼 수 있습니다. 
   
    ![HDInsight 애플리케이션 설치 앱](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. 설치 된 응용 프로그램 중 하나를 선택 하 여 속성을 표시 합니다. 속성 목록:

    |속성 | Description |
    |---|---|
    |앱 이름 |애플리케이션 이름. |
    |상태 |응용 프로그램 상태입니다. |
    |웹 페이지 |에지 노드에 배포한 웹 애플리케이션의 URL입니다. 자격 증명은 클러스터에 대해 구성한 HTTP 사용자 자격 증명과 동일합니다. |
    |SSH 엔드포인트 |SSH를 사용하여 에지 노드에 연결할 수 있습니다. SSH 자격 증명은 클러스터에 대해 구성한 SSH 사용자 자격 증명과 동일합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요. |
    |Description | 응용 프로그램 설명입니다. |

6. 애플리케이션을 삭제하려면 애플리케이션을 마우스 오른쪽 단추로 클릭한 다음, 상황에 맞는 메뉴에서 **삭제**를 클릭합니다.

## <a name="connect-to-the-edge-node"></a>에지 노드에 연결
HTTP 및 SSH를 사용하여 에지 노드에 연결할 수 있습니다. 엔드포인트 정보는 [포털](#list-installed-hdinsight-apps-and-properties)에서 찾을 수 있습니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

HTTP 엔드포인트 자격 증명은 HDInsight 클러스터에 대해 구성한 HTTP 사용자 자격 증명입니다. SSH 엔드포인트 자격 증명은 HDInsight 클러스터에 대해 구성한 SSH 자격 증명입니다.

## <a name="troubleshoot"></a>문제 해결
[설치 문제 해결](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md): HDInsight로 게시 취소된 HDInsight 애플리케이션을 배포하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 게시](hdinsight-apps-publish-applications.md): 사용자 지정 HDInsight 애플리케이션을 Azure Marketplace에 게시하는 방법을 알아봅니다.
* [MSDN: HDInsight 애플리케이션 설치](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight 애플리케이션을 정의하는 방법을 알아봅니다.
* [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md): 스크립트 작업을 사용하여 추가 애플리케이션을 설치하는 방법을 알아봅니다.
* [Resource Manager 템플릿을 사용하여 HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Azure Resource Manager 템플릿을 호출하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
* [HDInsight에서 비어 있는 에지 노드 사용](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에 액세스, HDInsight 애플리케이션 테스트 및 HDInsight 애플리케이션 호스팅하는 데 비어 있는 에지 노드를 사용하는 방법을 알아봅니다.

