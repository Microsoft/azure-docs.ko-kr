---
title: Azure HDInsight에서 Spark 클러스터의 Jupyter 노트북에 대한 커널
description: Azure HDInsight에서 Spark 클러스터와 함께 Jupyter 노트북에 사용할 수 있는 PySpark, PySpark3 및 Spark 커널에 대해 알아봅니다.
keywords: spark의 jupyter 노트북, jupyter spark
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: fed8791fbc7cc7f049a1161fb3903c7f6d42d4e8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124223"
---
# <a name="kernels-for-jupyter-notebook-on-apache-spark-clusters-in-azure-hdinsight"></a>Azure HDInsight의 Apache Spark 클러스터에 있는 Jupyter Notebook에 대한 커널 

HDInsight Spark 클러스터는 애플리케이션 테스트를 위해 [Apache Spark](https://spark.apache.org/)에서 Jupyter Notebook과 함께 사용할 수 있는 커널을 제공합니다. 커널은 코드를 실행하고 해석하는 프로그램입니다. 세 개의 커널은 다음과 같습니다.

- **PySpark** - Python2에서 작성한 애플리케이션용
- **PySpark3** - Python3에서 작성한 애플리케이션용
- **Spark** - Scala에서 작성한 애플리케이션용

이 문서에서는 이러한 커널의 사용 방법과 사용 시의 이점에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight의 Apache Spark 클러스터. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="create-a-jupyter-notebook-on-spark-hdinsight"></a>Spark HDInsight에서 Jupyter 노트북 만들기

1. [Azure Portal](https://portal.azure.com/)에서 클러스터를 엽니다.  지침에 대해서는 [클러스터 나열 및 표시](../hdinsight-administer-use-portal-linux.md#showClusters)를 참조하세요. 클러스터는 새 포털 블레이드에서 열립니다.

2. **빠른 링크** 섹션에서 **클러스터 대시보드**를 클릭하여 **클러스터 대시보드** 블레이드를 엽니다.  **빠른 링크**가 표시되지 않으면 블레이드의 왼쪽 메뉴에서 **개요**를 클릭합니다.

    ![Spark의 Jupyter 노트북](./media/apache-spark-jupyter-notebook-kernels/hdinsight-jupyter-notebook-on-spark.png "Spark의 Jupyter 노트북") 

3. **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.
   
   > [!NOTE]  
   > 또한 브라우저에서 다음 URL을 열어 Spark 클러스터의 Jupyter 노트북에 접근할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`


3. **새로 만들기**를 클릭하고 **Pyspark**, **PySpark3** 또는 **Spark** 중 하나를 클릭하여 Notebook을 만듭니다. Scala 애플리케이션에 대해 Spark 커널을, Python2 애플리케이션에 대해 PySpark 커널을, Python3 애플리케이션에 대해 PySpark3 커널을 사용합니다.
   
    ![Spark의 Jupyter 노트북에 대한 커널](./media/apache-spark-jupyter-notebook-kernels/kernel-jupyter-notebook-on-spark.png "Spark의 Jupyter 노트북에 대한 커널") 

4. 선택한 커널로 Notebook이 열립니다.

## <a name="benefits-of-using-the-kernels"></a>커널을 사용할 경우의 이점

다음은 Spark HDInsight 클러스터에서 Jupyter 노트북과 함께 새 커널을 사용할 경우의 몇 가지 이점입니다.

- **컨텍스트를 미리 설정합니다**. **PySpark**, **PySpark3** 또는 **Spark** 커널을 사용하면 애플리케이션으로 작업을 시작하기 전에 Spark 또는 Hive 컨텍스트를 명시적으로 설정할 필요가 없습니다. 이러한 컨텍스트는 기본적으로 사용할 수 있습니다. 이러한 컨텍스트는 다음과 같습니다.
   
  * **sc** - Spark 컨텍스트용
  * **sqlContext** - Hive 컨텍스트용
   
    따라서 컨텍스트를 설정하기 위해 다음과 같은 문을 실행할 필요가 없습니다.
   
         sc = SparkContext('yarn-client')
         sqlContext = HiveContext(sc)
   
    대신 애플리케이션에서 직접 미리 설정된 컨텍스트를 사용할 수 있습니다.

- **매직 셀**입니다. PySpark 커널은 특수 명령인 일부 미리 정의된 “매직”을 제공하며 이러한 매직은 `%%`(예: `%%MAGIC` `<args>`)를 사용하여 호출할 수 있습니다. 매직 명령은 코드 셀의 첫 번째 단어여야 하고 여러 콘텐츠 줄에 허용됩니다. 매직 단어는 셀의 첫 번째 단어여야 합니다. 매직 앞에 다른 단어(주석 포함)가 있으면 오류가 발생합니다.     매직에 대한 자세한 내용은 [여기](https://ipython.readthedocs.org/en/stable/interactive/magics.html)를 참조하세요.
   
    다음 표에는 커널을 통해 사용할 수 있는 다양한 매직이 나열되어 있습니다.

   | 매직 | 예 | 설명 |
   | --- | --- | --- |
   | help |`%%help` |예제 및 설명과 함께 사용할 수 있는 모든 매직이 포함된 테이블을 생성합니다. |
   | info |`%%info` |현재 Livy 엔드포인트에 대한 출력 세션 정보 |
   | 구성 |`%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} |세션 만들기에 대한 매개 변수를 구성합니다. 이미 세션이 만들어진 경우 강제 플래그(-f)가 필수이며 이렇게 하면 세션을 삭제 후 다시 만들 수 있습니다. 유효한 매개 변수 목록은 [Livy의 POST /sessions Request Body](https://github.com/cloudera/livy#request-body) 를 참조하세요. 매개 변수는 JSON 문자열로 전달되어야 하며, 아래 예제 열과 같이 매직 뒤의 다음 줄에 있어야 합니다. |
   | sql |`%%sql -o <variable name>`<br> `SHOW TABLES` |sqlContext에 대해 Hive 쿼리를 실행합니다. `-o` 매개 변수가 전달된 경우 쿼리 결과가 %%local Python 컨텍스트에서 [Pandas](https://pandas.pydata.org/) 데이터 프레임으로 유지됩니다. |
   | local |`%%local`<br>`a=1` |다음 줄의 모든 코드는 로컬로 실행됩니다. 코드는 사용 중인 커널에 관계없이 유효한 Python2 코드여야 합니다. 따라서 Notebook을 만드는 동안 **PySpark3** 또는 **Spark** 커널을 선택하더라도 셀에서 `%%local` 매직을 사용하면 해당 셀은 유효한 Python2 코드만 포함해야 합니다. |
   | 로그 |`%%logs` |현재 Livy 세션에 대한 로그를 출력합니다. |
   | delete |`%%delete -f -s <session number>` |현재 Livy 엔드포인트의 특정 세션을 삭제합니다. 커널 자체에 대해 시작된 세션은 삭제할 수 없습니다. |
   | cleanup |`%%cleanup -f` |이 노트북의 세션을 포함하여 현재 Livy 엔드포인트에 대한 모든 세션을 삭제합니다. 강제 플래그 -f는 필수입니다. |

   > [!NOTE]  
   > PySpark 커널에서 추가한 매직 외에도 `%%sh`를 포함하여 [기본 제공 IPython 매직](https://ipython.org/ipython-doc/3/interactive/magics.html#cell-magics)도 사용할 수 있습니다. `%%sh` 매직을 사용하면 클러스터 헤드 노드에서 스크립트 및 코드 블록을 실행할 수 있습니다.

1. **자동 시각화**. **Pyspark** 커널은 Hive 및 SQL 쿼리 출력을 자동으로 시각화합니다. 테이블, 원형, 선, 영역, 막대를 포함하여 다양한 시각화 형식 중에서 선택할 수 있습니다.

## <a name="parameters-supported-with-the-sql-magic"></a>%%sql 매직에서 지원되는 매개 변수
`%%sql` 매직은 쿼리를 실행할 때 검색하는 출력 종류를 제어하는 데 사용할 수 있는 여러 매개 변수를 지원합니다. 다음 표에는 출력이 나와 있습니다.

| 매개 변수 | 예 | 설명 |
| --- | --- | --- |
| -o |`-o <VARIABLE NAME>` |이 매개 변수를 사용하여 쿼리 결과를 %%local Python 컨텍스트에서 [Pandas](https://pandas.pydata.org/) 데이터 프레임으로 유지할 수 있습니다. 데이터 프레임 변수 이름은 사용자가 지정한 변수 이름입니다. |
| -q |`-q` |이 매개 변수를 사용하여 셀에 대한 시각화를 해제할 수 있습니다. 셀 내용을 자동으로 시각화하지 않고 데이터 프레임으로 캡처하기만 하려면 `-q -o <VARIABLE>`을 사용합니다. `CREATE TABLE` 문과 같은 SQL 쿼리를 실행하려는 등의 경우 결과를 캡처하지 않고 시각화를 해제하려면 `-o` 인수를 지정하지 않고 `-q`만 사용합니다. |
| -m |`-m <METHOD>` |여기서 **METHOD**는 **take** 또는 **sample**(기본값: **take**)입니다. METHOD가 **take**인 경우 커널은 MAXROWS(이 표의 뒷부분에서 설명함)로 지정된 결과 데이터 집합의 맨 위에서부터 요소를 선택합니다. METHOD가 **sample**인 경우 커널은 `-r` 매개 변수(이 표의 다음 행에서 설명함)에 따라 데이터 집합의 요소를 무작위로 샘플링합니다. |
| -r |`-r <FRACTION>` |여기서 **FRACTION**은 0.0과 1.0 사이의 부동 소수점 숫자입니다. SQL 쿼리의 샘플 메서드가 `sample`인 경우 커널은 결과 집합 요소의 지정된 부분을 무작위로 샘플링합니다. 예를 들어 `-m sample -r 0.01` 인수를 포함하여 SQL 쿼리를 실행할 경우 결과 행의 1%가 무작위로 샘플링됩니다. |
| -n |`-n <MAXROWS>` |**MAXROWS** 는 정수 값입니다. 커널은 출력 행 수를 **MAXROWS**로 제한합니다. **MAXROWS**가 **-1**과 같은 음수인 경우에는 결과 집합의 행 수가 제한되지 않습니다. |

**예제:**

    %%sql -q -m sample -r 0.1 -n 500 -o query2
    SELECT * FROM hivesampletable

위의 문은 다음 작업을 수행합니다.

* **hivesampletable**에서 모든 레코드를 선택합니다.
* -q를 사용하기 때문에 자동 시각화를 해제합니다.
* `-m sample -r 0.1 -n 500` 을 사용하기 때문에 hivesampletable에서 행의 10%를 무작위로 샘플링하고 결과 집합의 크기를 500개 행으로 제한합니다.
* 마지막으로, `-o query2` 를 사용하기 때문에 출력을 **query2**라는 데이터 프레임에도 저장합니다.

## <a name="considerations-while-using-the-new-kernels"></a>새 커널을 사용하는 동안 고려 사항

사용하는 커널에 따라 Notebook을 실행 중인 상태로 두면 클러스터 리소스가 사용됩니다.  이러한 커널의 경우 컨텍스트가 미리 설정되어 있기 때문에 Notebook이 컨텍스트를 종료하지 않으므로 클러스터 리소스는 계속 사용 중인 것으로 됩니다. Notebook을 다 사용했으면 Notebook의 **파일** 메뉴에서 **닫기 및 중지** 옵션을 사용하는 것이 좋습니다. 이 옵션은 컨텍스트를 종료한 후 Notebook을 종료합니다.     

## <a name="show-me-some-examples"></a>몇 가지 예제 보기

Jupyter Notebook을 여는 경우 루트 수준에서 사용할 수 있는 두 개의 폴더가 표시됩니다.

* **PySpark** 폴더에는 새 **Python** 커널을 사용하는 샘플 Notebook이 있습니다.
* **Scala** 폴더에는 새 **Spark** 커널을 사용하는 샘플 Notebook이 있습니다.

**PySpark** 또는 **Spark** 폴더에서 **00 - [READ ME FIRST] Spark 매직 커널 기능** Notebook을 열어 사용 가능한 다양한 매직에 대해 살펴볼 수 있습니다. 두 폴더에서 사용할 수 있는 다른 샘플 노트북을 사용하여 Jupyter 노트북을 HDInsight Spark 클러스터와 함께 사용하는 다양한 시나리오에 대해 알아볼 수 있습니다.

## <a name="where-are-the-notebooks-stored"></a>Notebook이 저장되는 위치

클러스터에 Azure Storage를 기본 스토리지 계정으로 사용하는 경우 Jupyter 노트가 **/HdiNotebooks** 폴더 아래의 스토리지 계정에 저장됩니다.  Jupyter 내에서 만든 Notebook, 텍스트 파일 및 폴더는 저장소 계정에서 액세스할 수 있습니다.  예를 들어 Jupyter를 사용하여 **myfolder** 폴더와 **myfolder/mynotebook.ipynb** Notebook을 만든 경우 저장소 계정 내, `/HdiNotebooks/myfolder/mynotebook.ipynb`에서 이 Notebook에 액세스할 수 있습니다.  반대의 경우도 마찬가지입니다. 즉, `/HdiNotebooks/mynotebook1.ipynb`에서 저장소 계정에 직접 Notebook을 업로드한 경우 Jupyter에서도 이 Notebook을 볼 수 있습니다.  Notebook은 클러스터를 삭제한 후에도 저장소 계정에 유지됩니다.

> [!NOTE]  
> 기본 스토리지로 Azure Data Lake Storage를 사용하는 HDInsight 클러스터는 연결된 스토리지에 노트를 저장하지 않습니다.

Notebook이 스토리지 계정에 저장되는 방식은 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)와 호환됩니다. 따라서 클러스터에 SSH 연결을 설정한 경우 다음 코드 조각에 표시된 것처럼 파일 관리 명령을 사용할 수 있습니다.

    hdfs dfs -ls /HdiNotebooks                               # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
    hdfs dfs –copyToLocal /HdiNotebooks                    # Download the contents of the HdiNotebooks folder
    hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter

클러스터가 기본 스토리지 계정으로 Azure Storage를 사용하는지 또는 Azure Data Lake Storage를 사용하는지에 관계없이, 노트는 `/var/lib/jupyter`의 클러스터 헤드 노드에도 저장됩니다.

## <a name="supported-browser"></a>지원되는 브라우저

Spark HDInsight 클러스터의 Jupyter 노트북은 Google Chrome에서만 지원됩니다.

## <a name="feedback"></a>사용자 의견
새로운 커널도 현재 개발 중이며 곧 완성될 예정입니다. 이는 API가 이러한 커널의 성숙에 따라 변경될 수 있음을 의미할 수 있습니다. 이러한 새로운 커널을 사용하는 동안 가진 의견을 보내주시면 감사하겠습니다. 이러한 커널의 최종 릴리스 형성에 유용할 것입니다. 이 문서의 맨 아래 **의견** 섹션 아래에 의견/피드백을 남길 수 있습니다.

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
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Spark Scala 애플리케이션 만들기 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
