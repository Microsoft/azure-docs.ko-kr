---
title: '자습서: Power BI를 사용하여 Azure HDInsight Apache Spark 데이터 분석'
description: 자습서 - Microsoft Power BI를 사용하여 Apache Spark 데이터 저장 HDInsight 클러스터 시각화
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 4eb10298ac683c991835f86582d82fb952b314b2
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82195114"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>자습서: HDInsight에서 Power BI를 사용하여 Apache Spark 데이터 분석

이 자습서에서는 Microsoft Power BI를 사용하여 Azure HDInsight의 Apache Spark 클러스터에서 데이터를 시각화하는 방법을 알아봅니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * Power BI를 사용하여 Spark 데이터 시각화

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* [자습서: Azure HDInsight의 Apache Spark 클러스터에서 데이터 로드 및 쿼리 실행](./apache-spark-load-data-run-query.md) 문서를 완료합니다.

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* 선택 사항: [Power BI 평가판 구독](https://app.powerbi.com/signupredirect?pbi_source=web)

## <a name="verify-the-data"></a>데이터 확인

[이전 자습서](apache-spark-load-data-run-query.md)에서 만든 [Jupyter Notebook](https://jupyter.org/)은 `hvac` 테이블을 만드는 코드를 포함합니다. 이 테이블은 `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`의 모든 HDInsight Spark 클러스터에서 사용 가능한 CSV 파일을 기준으로 합니다. 데이터를 확인하려면 다음 절차를 따릅니다.

1. Jupyter 노트북에서 다음 코드를 붙여넣은 다음 **Shift + Enter**를 누릅니다. 코드가 테이블의 존재 여부를 확인합니다.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    출력은 다음과 같이 표시됩니다.

    ![Spark에 테이블 표시](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    이 자습서를 시작하기 전에 Notebook을 닫은 경우 `hvactemptable`이 정리되므로 출력에 포함되지 않습니다.  metastore에 저장된 Hive 테이블만(**isTemporary** 열 아래에서 **False**로 표시됨) BI 도구에서 액세스할 수 있습니다. 이 자습서에서는 사용자가 만든 **hvac** 테이블에 연결합니다.

2. 빈 셀에 다음 코드를 붙여넣은 다음 **Shift + Enter**를 누릅니다. 코드가 테이블의 데이터를 확인합니다.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    출력은 다음과 같이 표시됩니다.

    ![Spark의 hvac 테이블에서 행 표시](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 선택합니다. Notebook을 종료하여 리소스를 해제합니다.

## <a name="visualize-the-data"></a>데이터 시각화

이 섹션에서는 Power BI를 사용하여 Spark 클러스터 데이터에서 시각화, 보고서 및 대시보드를 만듭니다.

### <a name="create-a-report-in-power-bi-desktop"></a>Power BI Desktop에서 보고서 만들기

Spark를 사용하는 첫 번째 단계는 Power BI Desktop에서 클러스터에 연결하고, 클러스터에서 데이터를 로드하고, 해당 데이터를 기반으로 기본 시각화를 만드는 것입니다.

1. Power BI Desktop을 엽니다. 시작 스플래시 화면이 열리면 닫습니다.

2. **홈** 탭에서 **데이터 가져오기** > **자세히...** 로 이동합니다.

    ![HDInsight Apache Spark에서 Power BI Desktop으로 데이터 가져오기](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Apache Spark BI에서 Power BI로 데이터 가져오기")

3. 검색 상자에 `Spark`를 입력하고 **Azure HDInsight Spark**를 선택한 후 **연결**을 선택합니다.

    ![Apache Spark BI에서 Power BI로 데이터 가져오기](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Apache Spark BI에서 Power BI로 데이터 가져오기")

4. **서버** 텍스트 상자에 클러스터 URL(`mysparkcluster.azurehdinsight.net` 형식)을 입력합니다.

5. **데이터 연결 모드**에서 **DirectQuery**를 선택합니다. 그런 다음, **확인**을 선택합니다.

    Spark와 함께 데이터 연결 모드 중 하나를 사용할 수 있습니다. DirectQuery를 사용하는 경우 변경 내용은 전체 데이터 세트를 새로 고치지 않고 보고서에 반영됩니다. 데이터를 가져오는 경우 변경 내용을 보려면 데이터 집합을 새로 고쳐야 합니다. DirectQuery를 사용하는 방법 및 사례에 대한 자세한 내용은 [Power BI에서 DirectQuery 사용](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/)을 참조하세요.

6. HDInsight 로그인 계정 정보를 입력한 다음 **연결**을 선택합니다. 기본 계정 이름은 *admin*입니다.

7. `hvac` 테이블을 선택하고 데이터의 미리 보기를 확인하기 위해 기다린 후 **로드**를 선택합니다.

    ![Spark 클러스터 사용자 이름 및 암호](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark 클러스터 사용자 이름 및 암호")

    Power BI Desktop에 Spark 클러스터에 연결하고 `hvac` 테이블에서 데이터를 로드하는 데 필요한 정보가 있습니다. 테이블 및 해당 열이 **필드** 창에 표시됩니다.

8. 각 건물에 대한 대상 온도와 실제 온도 간의 차이를 시각화합니다.

    1. **시각화** 창에서 **영역형 차트**를 선택합니다.

    2. **BuildingID** 필드를 **축**으로 끌어 놓고, **ActualTemp** 및 **TargetTemp** 필드를 **값**으로 끌어 놓습니다.

        ![값 열 추가](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "값 열 추가")

        다이어그램은 다음과 같이 표시됩니다.

        ![영역 그래프 합계](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "영역 그래프 합계")

        기본적으로 시각화에서는 **ActualTemp** 및 **TargetTemp**의 합계를 보여 줍니다. 시각화 창에서 **ActualTemp** 및 **TragetTemp** 옆에 있는 아래쪽 화살표를 선택하면 **Sum**이 선택된 것을 볼 수 있습니다.

    3. 시각화 창에서 **ActualTemp** 및 **TragetTemp** 옆에 있는 아래쪽 화살표를 선택하고 **Average**를 선택하여 각 건물에 대한 실제 온도와 대상 온도 간의 평균을 구합니다.

        ![평균값](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "평균값")

        데이터 시각화는 스크린샷의 데이터 시각화와 비슷해야 합니다. 커서를 시각화 위로 이동하면 관련 데이터와 함께 도구 설명이 나타납니다.

        ![영역 그래프](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "영역 그래프")

9. **파일** > **저장**으로 이동한 후 파일의 이름 `BuildingTemperature`를 입력하고 **저장**을 선택합니다.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Power BI 서비스에 보고서 게시(선택 사항)

Power BI 서비스를 사용하면 조직 전체에서 보고서 및 대시보드를 공유할 수 있습니다. 이 섹션에서는 먼저 데이터 세트와 보고서를 게시합니다. 그런 다음 보고서를 대시보드에 고정합니다. 대시보드는 일반적으로 보고서의 데이터 하위 집합에 초점을 맞추기 위해 사용됩니다. 보고서에는 시각화가 하나만 있지만 단계를 계속 진행하는 것이 좋습니다.

1. Power BI Desktop을 엽니다.

1. **홈** 탭에서 **게시**를 선택합니다.

    ![Power BI Desktop에서 게시](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Power BI Desktop에서 게시")

1. 데이터 세트 및 보고서를 게시하려는 작업 영역을 선택한 다음, **선택**을 선택합니다. 다음 이미지에서 기본 **내 작업 영역**이 선택됩니다.

    ![데이터 세트 및 보고서를 게시할 작업 영역 선택](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "데이터 세트 및 보고서를 게시할 작업 영역 선택")

1. 게시에 성공한 후 **Power BI에서 'BuildingTemperature.pbix' 열기**를 선택합니다.

    ![자격 증명 입력을 클릭하여 성공 게시](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "자격 증명 입력을 클릭하여 성공 게시")

1. Power BI 서비스에서 **자격 증명 입력**을 선택합니다.

    ![Power BI 서비스에 자격 증명 입력](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Power BI 서비스에 자격 증명 입력")

1. **자격 증명 편집**을 선택합니다.

    ![Power BI 서비스에서 자격 증명 편집](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI 서비스에서 자격 증명 편집")

1. HDInsight 로그인 계정 정보를 입력한 다음, **로그인**을 선택합니다. 기본 계정 이름은 *admin*입니다.

    ![Spark 클러스터에 로그인](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Spark 클러스터에 로그인")

1. 왼쪽 창에서 **작업 영역** > **내 작업 영역** > **보고서**로 이동한 다음, **BuildingTemperature**를 선택합니다.

    ![왼쪽 창의 보고서 아래에 나열된 보고서](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "왼쪽 창의 보고서 아래에 나열된 보고서")

    왼쪽 창의 **데이터 세트** 아래에 나열된 **BuildingTemperature**도 표시됩니다.

    이제 Power BI Desktop에서 만든 시각적 개체를 Power BI 서비스에서 사용할 수 있습니다.

1. 시각화 위로 커서를 이동한 다음, 오른쪽 위 모서리의 핀 고정 아이콘을 선택합니다.

    ![Power BI 서비스의 보고서](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Power BI 서비스의 보고서")

1. "새 대시보드"를 선택하고, 이름 `Building temperature`를 입력한 다음, **고정**을 선택합니다.

    ![새 대시보드에 고정](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "새 대시보드에 고정")

1. 보고서에서 **대시보드로 이동**을 선택합니다.

시각적 개체가 대시보드에 고정됩니다. 다른 시각적 개체를 보고서에 추가하고 동일한 대시보드에 고정할 수 있습니다. 보고서 및 대시보드에 대한 자세한 내용은 [Power BI의 보고서](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)및 [Power BI의 대시보드](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

클러스터를 삭제하려면 [브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제](../hdinsight-delete-cluster.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Microsoft Power BI를 사용하여 Azure HDInsight의 Apache Spark 클러스터에서 데이터를 시각화하는 방법을 알아보았습니다. 기계 학습 애플리케이션을 만들 수 있는지 보려면 다음 문서를 진행합니다.

> [!div class="nextstepaction"]
> [기계 학습 애플리케이션 만들기](./apache-spark-ipython-notebook-machine-learning.md)