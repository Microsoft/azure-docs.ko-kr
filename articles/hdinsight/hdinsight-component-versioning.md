---
title: Apache Hadoop 구성 요소 및 버전 - Azure HDInsight
description: Azure HDInsight의 Apache Hadoop 구성 요소 및 버전에 대해 자세히 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 5c3586e9ba405cb5a7338b8f7f22858a238b9271
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717254"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Azure HDInsight에서 사용할 수 있는 Apache 구성 요소 및 버전

이 문서에서는 Azure HDInsight 및 Enterprise Security Package의 [Apache Hadoop](https://hadoop.apache.org/) 환경 구성 요소 및 버전에 대해 알아봅니다. 또한 HDInsight에서 Hadoop 구성 요소 버전을 확인하는 방법도 알아봅니다.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>각 HDInsight 버전에서 제공되는 Apache 구성 요소

Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 2017년 4월 4일에 Azure HDInsight에서 사용하는 기본 클러스터 버전은 3.6이었습니다.

HDInsight 클러스터 버전과 연결된 구성 요소 버전은 다음 표에 나열되어 있습니다.

> [!NOTE]
> HDInsight 서비스의 기본 버전은 예고 없이 변경될 수 있습니다. 버전 종속성이 있는 경우 Azure PowerShell 및 Azure 클래식 CLI를 사용하여 .NET SDK에서 클러스터를 만들 때 HDInsight 버전을 지정합니다.

| 구성 요소              | HDInsight 4.0 | HDInsight 3.6(기본값)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop 및 YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1(ESP Interactive Query에서 2.1.0) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0.5           | 0.4.                        |
| Apache Kafka           | 2.1.1         | 1.1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4.0: Spark 2.4 및 Kafka 2.1은 완전히 지원됩니다. 그러나 Spark 2.3 및 Kafka 1.1 클러스터 유형은 처리 되지 않습니다. HDInsight 3.6: Spark 2.3 및 Kafka 1.1은 완전히 지원됩니다.  

## <a name="check-for-current-apache-component-version-information"></a>현재 Apache 구성 요소 버전 정보 확인

HDInsight 클러스터 버전과 연결된 Hadoop 환경 구성 요소 버전은 HDInsight에 대한 업데이트로 변경될 수 있습니다. Hadoop 구성 요소를 확인하고 클러스터에 대해 사용 중인 버전을 확인하려면 Ambari REST API를 사용합니다. **GetComponentInformation** 명령은 서비스 구성 요소에 대한 정보를 검색합니다. 자세한 내용은 [Apache Ambari 설명서](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)를 참조하세요.

### <a name="release-notes"></a>릴리스 정보

HDInsight의 최신 버전과 관련된 추가 릴리스 정보는 [HDInsight 릴리스 정보](hdinsight-release-notes.md)를 참조하세요.

## <a name="supported-hdinsight-versions"></a>지원되는 HDInsight 버전

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>HDInsight 버전에 대한 지원 만료 및 사용 중지

**지원 만료**란 Microsoft에서 더 이상 특정 HDInsight 버전에 대한 지원을 제공하지 않음을 의미합니다. 클러스터를 만들기가 Azure Portal을 통해 더 이상 제공되지 않습니다. 해당 버전은 Azure CLI 또는 다양한 SDK를 사용하여 계속 만들 수 있습니다.

**사용 중지**란 HDInsight 버전의 기존 클러스터가 계속해서 그대로 실행됨을 의미합니다. 이 버전의 새 클러스터는 CLI 및 SDK를 포함하여 어떤 방법으로도 만들 수 없습니다. 수동 스케일링 및 자동 스케일링 같은 다른 컨트롤 플레인 기능도 버전 사용 중지 후에는 작동하지 않을 수 있습니다. 사용 중지된 버전에는 지원이 제공되지 않습니다.

다음 표에서는 HDInsight 버전을 보여줍니다. 알려진 경우 지원 만료 날짜와 사용 중지 날짜도 나와 있습니다.

### <a name="available-versions"></a>사용 가능한 버전

다음 표에는 Azure Portal 및 PowerShell 및 .NET SDK와 같은 다른 배포 방법을 통해서 사용할 수 있는 HDInsight 버전이 나열되어 있습니다.

| HDInsight 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal의 가용성 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018년 9월 24일 | | |예 |예 |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017년 4월 4일      | * 6 월 31 일 2021 |2021 년 6 월 31 일 |예 |예 |

* 특정 HDInsight 3.6 클러스터 형식에 대 한 지원 기간을 연장 합니다.

| 클러스터 유형                    | 프레임 워크 버전 | 현재 지원 만료        | 새 지원 만료 날짜 |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 Spark             | 2.3               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 Spark             | 2.2               | 2020 년 6 월 30 일에 사용 중지          |                             |
| HDInsight 3.6 Spark             | 2.1               | 2020 년 6 월 30 일에 사용 중지          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 Kafka             | 1.0               | 2020 년 6 월 30 일에 사용 중지 됩니다.         |                             |
| HDInsight 3.6 HBase             | 1.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 대화형 쿼리 | 2.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 스톰             | 1.1               | 2020 년 12 월 31 일                      | 2021 년 6 월 30 일               |
| HDInsight 3.6 ML 서비스      | 9.3               | 2020 년 12 월 31 일                      | 2020 년 12 월 31 일                |

> [!NOTE]
> 클러스터 버전에 대 한 지원이 만료 되 면 Azure Portal를 통해 사용 하지 못할 수 있습니다. 경우에 따라 Windows PowerShell [AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) 명령에서 **version** 매개 변수를 사용 하 고 버전 사용 중지 날짜까지 .net SDK를 사용 하 여 클러스터 버전을 계속 사용할 수 있습니다.

### <a name="retired-versions"></a>사용 중지된 버전

다음 표에는 현재 Azure Portal에서 사용할 수 없는 HDInsight 버전이 나열되어 있습니다.

| HDInsight 버전 | HDP 버전 | VM OS | 릴리스 날짜 | 지원 만료 날짜 | 사용 중지 날짜 | 고가용성 |  Azure Portal의 가용성 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016년 9월 30일 |2017년 9월 5일 |2018년 6월 28일 |예 |예 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016년 3월 29일 |2016년 12월 29일 |2018 년 1월 9일 |예 |예 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015년 12월 2일 |2016년 6월 27일 |2018년 7월 31일 |예 |예 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015년 12월 2일 |2016년 6월 27일 |2017년 7월 31일 |예 |예 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS 또는 Windows Server 2012 R2 |2015년 2월 18일 |2016년 3월 1일 |2017년 4월 1일 |예 |예 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014년 6월 24일 |2015년 5월 18일 |2016년 6월 30일 |예 |예 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014년 2월 11일 |2014년 9월 17일 |2015년 6월 30일 |예 |예 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013년 10월 28일 |2014년 5월 12일 |2015년 5월 31일 |예 |예 |
| HDInsight 1.6 |HDP 1.1 | |2013년 10월 28일 |2014년 4월 26일 |2015년 5월 31일 |예 |예 |

> [!NOTE]
> 헤드 노드가 2개 있는 고가용성 클러스터는 기본적으로 HDInsight 버전 2.1 이상에 배포됩니다. HDInsight 버전 1.6 클러스터에서는 사용할 수 없습니다.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight 클러스터 버전의 서비스 수준 약정

서비스 수준 약정은 ‘지원 기간’으로 정의됩니다. 지원 기간은 Microsoft 고객 지원에서 HDInsight 버전을 지원하는 기간을 나타냅니다. 버전의 ‘지원 만료 날짜’가 지난 경우 HDInsight 클러스터는 지원 기간이 종료된 것입니다. 최신 X+1 버전이 사용 가능해진 후에 HDInsight 버전의 지원 만료 날짜는 다음 중 더 이후인 쪽입니다.

- **수식 1:** HDInsight 클러스터 버전 X가 릴리스된 날짜에 180일을 추가합니다.
- **수식 2:** HDInsight 클러스터 버전 X+1이 Azure Portal에 제공되는 날짜에 90일을 추가합니다.

‘사용 중지 날짜’는 HDInsight에서 해당 클러스터 버전을 만들 수 없게 되는 날짜입니다. 2017년 7월 31일 기준으로 사용 중지 날짜 이후에는 HDInsight 클러스터의 크기를 조정할 수 없습니다.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>클러스터에 대한 기본 노드 구성 및 가상 머신 크기

클러스터에 대해 선택할 가상 머신 SKU에 대한 자세한 내용은 [Azure HDInsight 클러스터 구성 세부 정보](hdinsight-supported-node-configuration.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [HDInsight의 Apache Hadoop, Spark 등에 대한 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC의 HDInsight에서 Apache Hadoop 작업](hdinsight-hadoop-windows-tools.md)
- [Azure HDInsight 버전과 관련된 Hortonworks 릴리스 정보](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)
