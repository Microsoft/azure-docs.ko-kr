---
title: 스크립트 작업 - Azure HDInsight에서 Jupyter를 사용해 Python 패키지 설치
description: 스크립트 작업을 사용하여 HDInsight Spark 클러스터와 함께 제공되는 Jupyter 노트북에서 외부 python 패키지를 사용하도록 구성하는 방법에 대한 단계별 지침입니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: hrasheed
ms.openlocfilehash: 876037e0e8e795ca98ddd3adf74dd0a304192a35
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097915"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight의 Apache Spark 클러스터에서 스크립트 작업을 사용하여 Jupyter Notebook용 외부 python 패키지 설치
> [!div class="op_single_selector"]
> * [셀 매직 사용](apache-spark-jupyter-notebook-use-external-packages.md)
> * [스크립트 작업 사용](apache-spark-python-package-installation.md)

스크립트 동작을 사용 하 여 구성 하는 방법을 알아봅니다는 [Apache Spark](https://spark.apache.org/) 외부의 커뮤니티 제공를 사용 하는 HDInsight 클러스터 **python** 하지 않은 패키지를 클러스터의 기본 제공을 포함 합니다.

> [!NOTE]  
> `%%configure` 매직을 사용하여 외부 패키지를 사용하도록 Jupyter Notebook을 구성할 수도 있습니다. 지침에 대해서는 [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)을 참조하세요.

사용할 수 있는 패키지의 전체 목록은 [패키지 인덱스](https://pypi.python.org/pypi)를 검색할 수 있습니다. 다른 소스에서 사용 가능한 패키지 목록을 가져올 수도 있습니다. 예를 들어 [conda-forge](https://conda-forge.org/feedstocks/)를 통해 제공되는 패키지를 설치할 수 있습니다.

이 문서에서는 클러스터에서 스크립트 작업을 사용하여 [TensorFlow](https://www.tensorflow.org/) 패키지를 설치하고 Jupyter 노트북을 통해 예제로 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 조건
다음이 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

   > [!NOTE]  
   > Hdinsight Linux에 Spark 클러스터가 아직 없는 경우 클러스터를 만드는 동안 스크립트 작업을 실행할 수 있습니다. [사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)에 대한 설명서를 참조하세요.
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight 클러스터에서 사용하는 오픈 소스 소프트웨어 지원

Microsoft Azure HDInsight 서비스는 Apache Hadoop으로 형성된 오픈 소스 기술의 에코시스템을 사용합니다. Microsoft Azure는 오픈 소스 기술에 대한 일반 수준의 지원을 제공합니다. 자세한 내용은 [Azure Support FAQ 웹 사이트](https://azure.microsoft.com/support/faq/)의 **지원 범위** 섹션을 참조하세요. HDInsight 서비스는 기본 제공 구성 요소에 대해 추가 수준의 지원을 제공합니다.

HDInsight 서비스에서 사용할 수 있는 오픈 소스 구성 요소에는 두 가지 유형이 있습니다.

* **기본 제공 구성 요소** - 이러한 구성 요소는 HDInsight 클러스터에 미리 설치 되어 있으며 클러스터의 핵심 기능을 제공합니다. 예를 들어 Apache Hadoop YARN ResourceManager, Apache Hive 쿼리 언어(HiveQL) 및 Mahout 라이브러리는 이 범주에 속합니다. 클러스터 구성 요소의 전체 목록은 [HDInsight에서 제공하는 Apache Hadoop 클러스터 버전의 새로운 기능](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)에 있습니다.
* **사용자 지정 구성 요소** - 클러스터의 사용자로서 사용자는 커뮤니티에서 사용 가능한 모든 구성 요소 또는 사용자가 만든 구성 요소를 작업에 설치하거나 사용할 수 있습니다.

> [!IMPORTANT]   
> HDInsight 클러스터에 제공되는 구성 요소는 완벽히 지원됩니다. Microsoft 지원은 이러한 구성 요소와 관련된 문제를 격리하고 해결하도록 도와줍니다.
>
> 사용자 지정 구성 요소는 문제 해결에 도움이 되는 합리적인 지원을 받습니다. Microsoft 지원을 통해 문제를 해결할 수 있습니다. 또는 해당 기술에 대한 전문 지식이 있는 오픈 소스 기술에 대해 사용 가능한 채널에 참여하도록 요청할 수 있습니다. 예를 들어 [HDInsight에 대한 MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com)과 같은 여러 커뮤니티 사이트를 사용할 수 있습니다. Apache 프로젝트는 [https://apache.org](https://apache.org)에 프로젝트 사이트가 있습니다(예: [Hadoop](https://hadoop.apache.org/)).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter 노트북에서 외부 패키지 사용

1. [Azure portal](https://portal.azure.com/)를 클러스터로 이동 합니다.  

2. 선택한 왼쪽된 창에서 클러스터를 사용 하 여 **설정을**를 선택 **스크립트 작업**합니다.

3. 선택 **+ 새로운 항목 제출**합니다.

4. 다음 값을 입력 합니다 **스크립트 동작 제출** 창:  


    |매개 변수 | 값 |
    |---|---|
    |스크립트 유형 | 드롭다운 목록에서 **- 사용자 지정**을 선택합니다.|
    |이름 |텍스트 상자에 `tensorflow`를 입력합니다.|
    |Bash 스크립트 URI |텍스트 상자에 `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh`를 입력합니다. |
    |노드 유형 | 선택 된 **Head**, 및 **작업자** 확인란 합니다. |

    `tensorflowinstall.sh` 다음 명령을 포함 되어 있습니다.

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install -c conda-forge tensorflow
    ```

5. **만들기**를 선택합니다.  [사용자 지정 스크립트 작업을 사용하는 방법](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)에 대한 설명서를 참조하세요.

6. 스크립트가 완료 될 때까지 기다립니다.  합니다 **스크립트 동작** 창의 상태는 **현재 클러스터 작업이 완료 된 후 새 스크립트 작업을 제출할 수 있습니다** 스크립트를 실행 하는 동안.  Ambari UI에서 진행률 표시줄을 볼 수 있습니다 **백그라운드 작업** 창입니다.

7. PySpark Jupyter notebook을 엽니다.  참조 [HDInsight Spark에서 Jupyter notebook 만들기](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight) 단계에 대 한 합니다.

    ![새 Jupyter 노트북 만들기](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "새 Jupyter 노트북 만들기")

8. 이제 `import tensorflow` 및 hello world 예제를 실행합니다. 다음 코드를 입력합니다.

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    결과는 다음과 유사하게 표시됩니다.
    
    ![TensorFlow 코드 실행](./media/apache-spark-python-package-installation/execution.png "TensorFlow 코드 실행")

> [!NOTE]  
> 클러스터에 두 개의 python 설치 프로그램이 있습니다. Spark는 `/usr/bin/anaconda/bin`에 있는 Anaconda python 설치를 사용하며, 기본적으로 Python 2.7 환경에 해당합니다. Python 3.x을 사용하고 PySpark3 커널에서 패키지를 설치하려면 해당 환경의 `conda` 실행 경로를 사용하고 `-n` 매개 변수를 사용하여 환경을 지정합니다. 예를 들어, 명령 `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35`는 `conda-forge` 채널을 사용하여 Python 3.5 환경에 `ggplot` 패키지를 설치합니다.

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [HDInsight의 Apache Spark 클러스터에서 Jupyter 노트북과 함께 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
