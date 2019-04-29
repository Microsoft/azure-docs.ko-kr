---
title: Azure HDInsight의 HBase에서 Apache Phoenix 및 SQLLine 사용
description: HDInsight에서 Apache Phoenix를 사용하는 방법을 알아봅니다. 또한 컴퓨터에 SQLLine을 설치 및 설정하여 HDInsight의 HBase 클러스터에 연결하는 방법도 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: hrasheed
ms.openlocfilehash: 0bef586540635ee1bada3475f6316c84dea4308c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123164"
---
# <a name="use-apache-phoenix-with-linux-based-apache-hbase-clusters-in-hdinsight"></a>HDInsight에서 Linux 기반 Apache HBase 클러스터와 함께 Apache Phoenix 사용
Azure HDInsight에서 [Apache Phoenix](https://phoenix.apache.org/)를 사용하는 방법 및 SQLLine을 사용하는 방법에 대해 알아봅니다. Phoenix에 대한 자세한 내용은 [15분 이내의 Apache Phoenix](https://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)를 참조하세요. Phoenix 문법은 [Apache Phoenix 문법](https://phoenix.apache.org/language/index.html)을 참조하세요.

> [!NOTE]  
> HDInsight에 대한 Phoenix 버전 정보는 [HDInsight에서 제공하는 Apache Hadoop 클러스터 버전의 새로운 기능](../hdinsight-component-versioning.md)을 참조하세요.
>
>

## <a name="use-sqlline"></a>SQLLine 사용
[SQLLine](http://sqlline.sourceforge.net/)은 SQL을 실행하는 명령줄 유틸리티입니다.

### <a name="prerequisites"></a>필수 조건
SQLLine을 시작하려면 다음 항목이 있어야 합니다.

* **HDInsight의 Apache HBase 클러스터**. 만들려면 [HDInsight에서 Apache HBase 시작](./apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

HBase 클러스터에 연결할 때 [Apache ZooKeeper](https://zookeeper.apache.org/) VM 중 하나에 연결해야 합니다. 각 HDInsight 클러스터마다 세 개의 ZooKeeper VM이 있습니다.

**ZooKeeper 호스트 이름을 확인하려면**

1. **https://\<cluster name\>.azurehdinsight.net**으로 이동하여 [Apache Ambari](https://ambari.apache.org/)를 엽니다.
2. 로그인하려면 HTTP(클러스터) 사용자 이름 및 암호를 입력합니다.
3. 왼쪽 메뉴에서 **ZooKeeper**를 선택합니다. 세 개의 **ZooKeeper Server** 인스턴스가 나열됩니다.
4. **ZooKeeper Server** 인스턴스 중 하나를 선택합니다. **요약** 창에서 **호스트 이름**을 찾습니다. 이 이름은 *zk1-jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*과 비슷합니다.

**SQLLine을 사용하려면**

1. SSH를 사용하여 클러스터에 연결합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](../hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

2. SSH에서 다음 명령을 실행하여 SQLLine을 실행합니다.

        cd /usr/hdp/current/phoenix-client/bin
        ./sqlline.py <ZOOKEEPER SERVER FQDN>:2181:/hbase-unsecure
3. HBase 테이블을 만들고 일부 데이터를 삽입하려면 다음 명령을 실행합니다.

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

자세한 내용은 [SQLLine 설명서(영문)](http://sqlline.sourceforge.net/#manual) 및 [Apache Phoenix 문법(영문)](https://phoenix.apache.org/language/index.html)을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 HDInsight에서 Apache Phoenix를 사용하는 방법을 알아보았습니다. 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight HBase 개요][hdinsight-hbase-overview]
  Apache HBase는 구조화되지 않은/반구조화된 대량 데이터에 대해 임의 액세스 및 강력한 일관성을 제공하는 Apache Hadoop 기반의 Apache 오픈 소스 NoSQL 데이터베이스입니다.
* [Azure Virtual Network에 Apache HBase 클러스터 프로비전][hdinsight-hbase-provision-vnet].
  가상 네트워크 통합을 사용하면 Apache HBase 클러스터를 애플리케이션과 동일한 가상 네트워크에 배포할 수 있으므로 애플리케이션에서 HBase와 직접 통신 할 수 있습니다.
* [HDInsight에서 Apache HBase 복제 구성](apache-hbase-replication.md). 두 Azure 데이터 센터 간에 Apache HBase 복제를 설정하는 방법을 알아봅니다.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-provision-vnet]:apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:apache-hbase-overview.md


