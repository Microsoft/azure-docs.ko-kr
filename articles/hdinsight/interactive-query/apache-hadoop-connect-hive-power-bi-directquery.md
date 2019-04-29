---
title: Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화
description: Microsoft Power BI를 사용하여 Azure HDInsight에서 대화형 쿼리 Hive 데이터를 시각화
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 657f8df959ccda5d51748ef5fbfc2e280f7d2c2e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126582"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Azure HDInsight에서 직접 쿼리를 사용하여 Microsoft Power BI에서 대화형 쿼리 Apache Hive 데이터 시각화

이 문서에서는 Microsoft Power BI를 Azure HDInsight 대화형 쿼리 클러스터에 연결하고 직접 쿼리를 사용하여 Apache Hive 데이터를 시각화하는 방법을 설명합니다. 제공 된 예제 데이터를 로드 한 `hivesampletable` Power BI로 Hive 테이블. `hivesampletable` Hive 테이블에 일부 휴대폰 사용 현황 데이터가 포함 되어 있습니다. 그런 다음 전 세계 맵에 사용 현황 데이터를 그림으로 나타냅니다.

![HDInsight Power BI 맵 보고서](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

[Apache Hive ODBC 드라이버](../hadoop/apache-hadoop-connect-hive-power-bi.md)를 활용하여 Power BI Desktop에서 제네릭 ODBC 커넥터를 통해 가져오기를 수행할 수 있습니다. 그러나 BI 워크로드의 경우 Hive 쿼리 엔진의 비대화형 특성을 지정하지 않는 것이 좋습니다. 성능을 개선하기 위해 [HDInsight 대화형 쿼리 커넥터](./apache-hadoop-connect-hive-power-bi-directquery.md) 및 [HDInsight Apache Spark 커넥터](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect)를 선택할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음 항목이 있어야 합니다.

* **HDInsight 클러스터**. 클러스터는 Apache Hive를 사용한 HDInsight 클러스터 또는 새로 릴리스된 대화형 쿼리 클러스터일 수 있습니다. 클러스터를 만드는 방법은 [클러스터 만들기](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)를 참조하세요.
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=45331)에서 복사본을 다운로드할 수 있습니다.

## <a name="load-data-from-hdinsight"></a>HDInsight에서 데이터 로드

`hivesampletable` Hive 테이블 모든 HDInsight 클러스터와 함께 제공 합니다.

1. Power BI Desktop을 시작 합니다.

2. 메뉴 모음에서로 이동 **Home** > **데이터 가져오기** > **더...** .

    ![HDInsight Power BI 데이터 열기](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. **데이터 가져오기** 창에서 입력 **hdinsight** 검색 상자에 있습니다.  

4. 검색 결과에서 선택 **HDInsight 대화형 쿼리**를 선택한 후 **Connect**합니다.  찾을 수 없으면 **HDInsight 대화형 쿼리**, Power BI Desktop을 최신 버전으로 업데이트 해야 합니다.

5. 선택 **계속** 닫으려면를 **타사 서비스에 연결할** 대화 합니다.

6. 에 **HDInsight 대화형 쿼리** 창에서 다음 정보를 입력 한 다음 선택 **확인**:

    |자산 | 값 |
    |---|---|
    |서버 |예를 들어 클러스터 이름을 입력 *myiqcluster.azurehdinsight.net*합니다.|
    |데이터베이스 |입력 **기본** 이 문서에 대 한 합니다.|
    |데이터 연결 모드 |선택 **DirectQuery** 이 문서에 대 한 합니다.|

    ![HDInsight 대화형 쿼리 Power BI DirectQuery 연결](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. HTTP 자격 증명을 입력 한 다음 선택 **Connect**합니다. 기본 사용자 이름은 **admin**입니다.

8. **Navigator** 의 왼쪽된 창에서 창 **hivesampletale**합니다.

9. 선택 **부하** 주 창에서.

    ![HDInsight 대화형 쿼리 Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>맵에 데이터 시각화

마지막 절차에서 계속 진행합니다.

1. 시각화 창에서 선택 **맵**, 구형 아이콘입니다. 그런 다음 일반 맵 주 창에 나타납니다.

    ![HDInsight Power BI 보고서 사용자 지정](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. 필드 창에서 **country** 및 **devicemake**를 선택합니다. 데이터 요소를 사용 하 여 전 세계 맵에 잠시 후 주 창에 나타납니다.

3. 맵을 확장합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Power BI를 사용하여 HDInsight에서 데이터를 시각화하는 방법을 알아보았습니다.  데이터 시각화에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight의 ODBC를 사용하여 Microsoft Power BI와 Apache Hive 데이터 시각화](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](./../hdinsight-connect-hive-zeppelin.md)
* [Microsoft Hive ODBC Driver로 HDInsight에 Excel 연결](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [파워 쿼리를 사용하여 Apache Hadoop에 Excel 연결](../hadoop/apache-hadoop-connect-excel-power-query.md)
* [Data Lake Tools for Visual Studio를 사용하여 Azure HDInsight에 연결 및 Apache Hive 쿼리 실행](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Azure HDInsight Tool for Visual Studio Code 사용](../hdinsight-for-vscode.md)
* [HDInsight에 데이터 업로드](./../hdinsight-upload-data.md)
