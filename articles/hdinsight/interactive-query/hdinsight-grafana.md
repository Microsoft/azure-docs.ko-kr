---
title: Azure HDInsight에서 Grafana 사용
description: Azure HDInsight에서 Apache Hadoop 클러스터로 Grafana 대시보드에 액세스하는 방법을 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 81fd3b368f9405192c164ed7a0638caad0cd75fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869755"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Azure HDInsight에서 Grafana에 액세스

[Grafana](https://grafana.com/)는 널리 사용되는 오픈 소스 그래프 및 대시보드 작성기입니다. Grafana는 다양한 기능을 수행합니다. 사용자가 사용자 지정 가능하고 공유 가능한 대시보드를 만들 뿐만 아니라 템플릿/스크립팅 대시보드, LDAP 통합, 여러 데이터 원본 등도 제공합니다.

현재, Azure HDInsight에서 Grafana는 Spark, HBase, Kafka, Interactive Query 클러스터 형식으로 지원됩니다. 엔터프라이즈 보안 팩을 사용하도록 설정한 클러스터에서는 지원되지 않습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop 클러스터 만들기

[Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요. **클러스터 유형** 에서 **Spark**, **Kafka**, **HBase** 또는 **Interactive Query** 를 선택합니다.

## <a name="access-the-grafana-dashboard"></a>Grafana 대시보드에 액세스

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/grafana/`로 이동합니다. 여기서 CLUSTERNAME은 클러스터의 이름입니다.

1. Hadoop 클러스터 사용자 자격 증명을 입력합니다.

1. 다음 예제와 같은 Grafana 대시보드가 표시됩니다.

    :::image type="content" source="./media/hdinsight-grafana/hdinsight-grafana-dashboard.png " alt-text="HDInsight Grafana 웹 대시보드" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 생성된 클러스터를 삭제합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 맨 위에 있는 **검색** 상자에 **HDInsight** 를 입력합니다.

1. **서비스** 에서 **HDInsight 클러스터** 를 선택합니다.

1. 표시되는 HDInsight 클러스터 목록에서, 만든 클러스터 옆에 있는 **...** 를 선택합니다.

1. **삭제** 를 선택합니다. **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

HDInsight를 사용하여 데이터를 분석하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hive 사용](../hadoop/hdinsight-use-hive.md).

* [HDInsight에서 MapReduce 사용](../hadoop/hdinsight-use-mapreduce.md).

* [HDInsight용 Visual Studio Hadoop 도구 사용 시작](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
