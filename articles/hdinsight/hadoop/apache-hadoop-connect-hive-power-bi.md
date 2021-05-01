---
title: Power BI를 사용하여 Apache Hive 데이터 시각화 - Azure HDInsight
description: Microsoft Power BI를 사용하여 Azure HDInsight에서 처리한 Hive 데이터를 시각화하는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: bb448a4befb15618485b2b5951222761180a1f22
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866746"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Azure HDInsight의 ODBC를 사용하여 Microsoft Power BI와 Apache Hive 데이터 시각화

ODBC를 사용하여 Microsoft Power BI Desktop를 Azure HDInsight에 연결하고 Apache Hive 데이터를 시각화하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> Hive ODBC 드라이버를 활용하여 Power BI Desktop에서 제네릭 ODBC 커넥터를 통해 가져오기를 수행할 수 있습니다. 그러나 BI 워크로드의 경우 Hive 쿼리 엔진의 비대화형 특성을 지정하지 않는 것이 좋습니다. 성능을 개선하기 위해 [대화형 쿼리 HDInsight 커넥터](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) 및 [HDInsight Spark 커넥터](/power-bi/spark-on-hdinsight-with-direct-connect)를 선택할 수 있습니다.

이 문서에서는 `hivesampletable` Hive 테이블의 데이터를 Power BI로 로드합니다. Hive 테이블에는 일부 휴대폰 사용량 현황 데이터가 포함되어 있습니다. 그런 다음 전 세계 맵에 사용량 현황 데이터를 그림으로 나타냅니다.

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI 맵 보고서" border="true":::

정보는 새 [대화형 쿼리](../interactive-query/apache-interactive-query-get-started.md) 클러스터 유형에도 적용됩니다. 직접 쿼리를 사용하여 HDInsight 대화형 쿼리에 연결하는 방법은 [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)(Azure HDInsight의 직접 쿼리를 사용하여 Microsoft Power BI로 대화형 쿼리 Hive 데이터 시각화)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* HDInsight 클러스터. 클러스터는 Hive를 사용한 HDInsight 클러스터 또는 새로 릴리스된 대화형 쿼리 클러스터일 수 있습니다. 클러스터를 만드는 방법은 [클러스터 만들기](apache-hadoop-linux-tutorial-get-started.md)를 참조하세요.

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45331)에서 복사본을 다운로드할 수 있습니다.

## <a name="create-hive-odbc-data-source"></a>Hive ODBC 데이터 원본 만들기

[Hive ODBC 데이터 원본 만들기](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)를 참조하세요.

## <a name="load-data-from-hdinsight"></a>HDInsight에서 데이터 로드

**hivesampletable** Hive 테이블은 모든 HDInsight 클러스터와 함께 제공됩니다.

1. Power BI Desktop을 시작합니다.

1. 상단 메뉴에서 **홈** > **데이터 가져오기** > **자세히...** 로 이동합니다.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Excel Power BI 데이터 열기" border="true":::

1. **데이터 가져오기** 대화 상자에서 왼쪽의 **기타** 를 선택하고 오른쪽의 **ODBC** 를 선택한 다음 아래쪽의 **연결** 을 선택합니다.

1. **ODBC에서** 대화 상자에서, 드롭다운 목록의 마지막 섹션에서 만든 데이터 원본 이름을 선택합니다. 그런 다음, **확인** 을 선택합니다.

1. 처음 사용하는 경우 **ODBC 드라이버** 대화 상자가 열립니다. 왼쪽 메뉴에서 **기본 또는 사용자 지정** 을 선택합니다. **연결** 을 선택하여 **탐색기** 를 엽니다.

1. **탐색기** 대화 상자에서 **ODBC>HIVE>기본값** 으로  확장하여 **hivesampletable** 을 선택한 다음 **로드** 를 선택합니다.

## <a name="visualize-data"></a>데이터 시각화

마지막 절차에서 계속 진행합니다.

1. 시각화 창에서 **맵** 을 선택합니다. 지구본 아이콘 입니다.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png" alt-text="HDInsight Power BI 보고서 사용자 지정" border="true":::

1. **Fields** 창에서 **국가** 및 **devicemake** 를 선택합니다. 맵에 표시된 데이터를 볼 수 있습니다.

1. 맵을 확장합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Power BI를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  자세한 내용은 다음 문서를 참조하세요.

* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](./apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](apache-hadoop-connect-excel-power-query.md)
* [직접 쿼리를 사용하여 Microsoft Power BI에서 대화형 쿼리 Apache Hive 데이터 시각화](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)