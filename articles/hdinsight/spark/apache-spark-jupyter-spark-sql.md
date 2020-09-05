---
title: '빠른 시작: 템플릿을 사용하여 Apache Spark 클러스터 만들기 - Azure HDInsight'
description: 이 빠른 시작에서는 Resource Manager 템플릿을 사용하여 Azure HDInsight에서 Apache Spark 클러스터를 만들고 Spark SQL 쿼리를 실행하는 방법을 보여줍니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 0f6a6cbc9112959a670bdb0a9cb56ea4509413a1
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640772"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure HDInsight에서 Apache Spark 클러스터 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure HDInsight에서 [Apache Spark](./apache-spark-overview.md) 클러스터를 만듭니다. 그런 다음, Jupyter Notebook을 만들고 이를 사용하여 Apache Hive 테이블에 대해 Spark SQL 쿼리를 실행합니다. Azure HDInsight는 엔터프라이즈를 위한 관리형의 전체 스펙트럼 오픈 소스 분석 서비스입니다. HDInsight용 Apache Spark 프레임워크를 통해 메모리 내 처리 기능을 사용하여 데이터 분석 및 클러스터 컴퓨팅을 신속하게 처리합니다. Jupyter Notebook을 사용하면 데이터와 상호 작용하고, 코드를 markdown 텍스트와 결합하고, 간단한 시각화를 수행할 수 있습니다.

여러 클러스터를 함께 사용하는 경우 가상 네트워크를 만들고, Spark 클러스터를 사용하는 경우 Hive Warehouse Connector도 사용하는 것이 좋습니다. 자세한 내용은 [Azure HDInsight에 대한 가상 네트워크 계획](../hdinsight-plan-virtual-network-deployment.md) 및 [Hive Warehouse Connector를 사용하여 Apache Spark 및 Apache Hive 통합](../interactive-query/apache-hive-warehouse-connector.md)을 참조하세요.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다.

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): Azure Storage 계정을 만듭니다.
* [Microsoft.HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): HDInsight 클러스터를 만듭니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 아래 **Azure에 배포** 단추를 선택하여 Azure에 로그인하고 ARM 템플릿을 엽니다.

    [![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. 다음 값을 입력하거나 선택합니다.

    |속성 |Description |
    |---|---|
    |Subscription|드롭다운 목록에서 클러스터에 사용할 Azure 구독을 선택합니다.|
    |Resource group|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기**를 선택합니다.|
    |위치|이 값은 리소스 그룹에 사용되는 위치로 자동 입력됩니다.|
    |클러스터 이름|전역적으로 고유한 이름을 입력합니다. 이 템플릿의 경우 소문자와 숫자만 사용합니다.|
    |클러스터 로그인 사용자 이름|사용자 이름을 입력합니다. 기본값은 **admin**입니다.|
    |클러스터 로그인 암호|암호를 입력합니다. 암호는 10자 이상이어야 하며, 숫자, 대문자, 소문자 및 영숫자가 아닌 문자(' " ` 문자 제외)를 각각 하나 이상 포함해야 합니다. |
    |SSH 사용자 이름|사용자 이름을 입력합니다. 기본값은 **sshuser**입니다.|
    |SSH 암호|암호를 입력합니다.|

    ![Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Spark 클러스터 만들기](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Spark 클러스터를 만듭니다.")

1. **사용 약관**을 검토합니다. 그런 다음, **위에 명시된 사용 약관에 동의함**을 선택한 다음, **구매**를 선택합니다. 배포가 진행 중이라는 알림이 표시됩니다. 클러스터를 만들려면 20분 정도가 걸립니다.

HDInsight 클러스터를 만드는 데 문제가 발생하는 경우 이를 수행하기 위한 적절한 사용 권한이 없을 수 있습니다. 자세한 내용은 [액세스 제어 요구 사항](../hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

클러스터가 만들어지면 **리소스로 이동** 링크가 포함된 **배포 성공** 알림이 표시됩니다. 리소스 그룹 페이지에 새 HDInsight 클러스터와 해당 클러스터에 연결된 기본 스토리지가 나열됩니다. 각 클러스터에는 [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) 계정 또는 [Azure Data Lake Storage 계정](../hdinsight-hadoop-use-data-lake-store.md) 종속성이 있습니다. 이 스토리지 계정을 기본 스토리지 계정이라고 합니다. HDInsight 클러스터와 해당 기본 스토리지 계정은 같은 Azure 지역에 있어야 합니다. 클러스터를 삭제하더라도 스토리지 계정은 삭제되지 않습니다.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook 만들기

[Jupyter Notebook](https://jupyter.org/)은 다양한 프로그래밍 언어를 지원하는 대화형 Notebook 환경입니다. Notebook을 사용하면 데이터와 상호 작용하고, 코드를 markdown 텍스트와 결합하고, 간단한 시각화를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. **HDInsight 클러스터**를 선택한 다음, 만든 클러스터를 선택합니다.

    ![Azure Portal에서 HDInsight 클러스터 열기](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. 포털의 **클러스터 대시보드** 섹션에서 **Jupyter Notebook**을 선택합니다. 메시지가 표시되면 클러스터에 대한 클러스터 로그인 자격 증명을 입력합니다.

   ![대화형 Spark SQL 쿼리 실행을 위해 Jupyter Notebook 열기](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Jupyter Notebook을 열어 대화형 Spark SQL 쿼리 실행")

4. **새로 만들기** > **PySpark**를 선택하여 Notebook을 만듭니다.

   ![대화형 Spark SQL 쿼리 실행을 위해 Jupyter Notebook 만들기](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "대화형 Spark SQL 쿼리 실행을 위해 Jupyter Notebook 만들기")

   새 Notebook이 만들어지고 Untitled(Untitled.pynb) 이름으로 열립니다.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL 문 실행

SQL(구조적 쿼리 언어)은 데이터 쿼리 및 변환에 가장 일반적이며 널리 사용되는 언어입니다. Spark SQL은 익숙한 SQL 구문을 사용하여 구조화된 데이터를 처리하기 위한 Apache Spark에 대한 확장으로 작동합니다.

1. 커널이 준비되었는지 확인합니다. Notebook의 커널 이름 옆에 속이 빈 원이 보이면 커널이 준비된 것입니다. 속이 찬 원은 커널이 사용 중이라는 뜻입니다.

    ![커널 상태](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "커널 상태")

    Notebook을 처음으로 시작하면 커널이 백그라운드에서 몇 가지 작업을 수행합니다. 커널이 준비될 때까지 기다립니다.

1. 빈 셀에 다음 코드를 붙여 넣은 다음, **SHIFT + ENTER**를 눌러 코드를 실행합니다. 이 명령은 클러스터의 Hive 테이블을 나열합니다.

    ```sql
    %%sql
    SHOW TABLES
    ```

    Jupyter Notebook을 HDInsight 클러스터와 함께 사용하는 경우 Spark SQL을 사용하여 Hive 쿼리를 실행하는 데 사용할 수 있는 미리 설정된 `spark` 세션을 얻게 됩니다. `%%sql`은 Jupyter Notebook에 미리 설정된 `spark` 세션을 사용하여 Hive 쿼리를 실행하도록 지시합니다. 쿼리는 기본적으로 모든 HDInsight 클러스터와 함께 제공되는 Hive 테이블(**hivesampletable**)에서 상위 10개의 행을 검색합니다. 처음으로 쿼리를 제출할 때 Jupyter는 Notebook에 대한 Spark 애플리케이션을 만듭니다. 완료하는 데 약 30초가 걸립니다. Spark 애플리케이션이 준비되면 약 1초 후 쿼리가 실행되어 결과를 생성합니다. 출력은 다음과 같이 표시됩니다.

    ![HDInsight의 Apache Hive 쿼리](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "HDInsight의 Hive 쿼리")

    Jupyter에서 쿼리를 실행할 때마다, 웹 브라우저 창 제목에 Notebook 제목과 함께 **(사용 중)** 상태가 표시됩니다. 또한 오른쪽 위 모서리에 있는 **PySpark** 텍스트 옆에 단색 원이 표시됩니다.

1. 또 다른 쿼리를 실행하여 `hivesampletable`의 데이터를 봅니다.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    쿼리 출력이 표시되도록 화면이 새로 고쳐집니다.

    ![HDInsight의 Hive 쿼리 출력](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "HDInsight의 Hive 쿼리 출력")

1. Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 선택합니다. Notebook을 종료하면 Spark 애플리케이션을 비롯한 클러스터 리소스가 릴리스됩니다.

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작을 완료한 후 클러스터를 삭제하는 것이 좋습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

Azure Portal에서 클러스터로 이동하여 **삭제**를 선택합니다.

![Azure Portal에서 HDInsight 클러스터 삭제](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight 클러스터 삭제")

또한 리소스 그룹 이름을 선택하여 리소스 그룹 페이지를 연 다음, **리소스 그룹 삭제**를 선택할 수도 있습니다. 리소스 그룹을 삭제하여 HDInsight 클러스터와 기본 스토리지 계정을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 HDInsight에서 Apache Spark 클러스터를 만들고 기본 Spark SQL 쿼리를 실행하는 방법을 알아보았습니다. 다음 자습서를 진행하여 샘플 데이터에서 대화형 쿼리를 실행하는 데 HDInsight 클러스터를 사용하는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Apache Spark에서 대화형 쿼리 실행](./apache-spark-load-data-run-query.md)
