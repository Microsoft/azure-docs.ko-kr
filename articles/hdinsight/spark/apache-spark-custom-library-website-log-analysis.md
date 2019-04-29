---
title: Spark에서 Python 라이브러리를 사용하여 웹 사이트 로그 분석 - Azure
description: 이 Notebook에서는 Azure HDInsight의 Spark와 함께 사용자 지정 라이브러리를 사용하여 로그 데이터를 분석하는 방법을 보여 줍니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: bef71f210e015dc10cd6f5c0c655d0d3beee3655
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124444"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>HDInsight에서 Apache Spark 클러스터와 함께 사용자 지정 Python 라이브러리를 사용하여 웹 사이트 로그 분석

이 노트북에서는 HDInsight의 Apache Spark와 함께 사용자 지정 라이브러리를 사용하여 로그 데이터를 분석하는 방법을 보여줍니다. 사용하는 사용자 지정 라이브러리는 **iislogparser.py**라는 Python 라이브입니다.

> [!TIP]  
> 이 자습서는 HDInsight에서 만드는 Spark(Linux) 클러스터에서 Jupyter 노트북으로 사용할 수도 있습니다. Notebook 환경을 통해 Notebook 자체에서 Python 코드 조각을 실행할 수 있습니다. Notebook 내에서 자습서를 수행하려면 Spark 클러스터를 만들고 Jupyter Notebook(`https://CLUSTERNAME.azurehdinsight.net/jupyter`)을 시작한 다음 **PySpark** 폴더 아래의 **사용자 지정 library.ipynb를 사용하여 Spark에서 로그 분석** Notebook을 실행합니다.
>
>

**필수 조건:**

다음이 있어야 합니다.

* Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

* HDInsight의 Apache Spark 클러스터입니다. 자세한 내용은 [Azure HDInsight에서 Apache Spark 클러스터 만들기](apache-spark-jupyter-spark-sql.md)를 참조하세요.

## <a name="save-raw-data-as-an-rdd"></a>RDD로 원시 데이터 저장
이 섹션에서는 HDInsight에서 Apache Spark 클러스터와 연결된 [Jupyter](https://jupyter.org) 노트북을 사용하여 원시 샘플 데이터를 처리하고 하이브 테이블로 저장하는 작업을 실행합니다. 샘플 데이터는 기본적으로 모든 클러스터에서 사용 가능한 .csv 파일(hvac.csv)입니다.

데이터를 Apache Hive 테이블로 저장한 후에 다음 섹션에서 Power BI 및 Tableau와 같은 BI 도구를 사용하여 Hive 테이블에 연결합니다.

1. [Azure Portal](https://portal.azure.com/)의 시작 보드에서 Spark 클러스터의 타일을 클릭합니다(시작 보드에 고정한 경우). **모두 찾아보기** > **HDInsight 클러스터**에서 클러스터로 이동할 수도 있습니다.   
2. Spark 클러스터 블레이드에서 **클러스터 대시보드**를 클릭하고 **Jupyter Notebook**을 클릭합니다. 메시지가 표시되면 클러스터에 대한 관리자 자격 증명을 입력합니다.

   > [!NOTE]
   > 또한 브라우저에서 다음 URL을 열어 클러스터에 대한 Jupyter Notebook에 접근할 수 있습니다. **CLUSTERNAME** 을 클러스터의 이름으로 바꿉니다.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. 새 Notebook을 만듭니다. **새로 만들기**를 클릭한 다음 **PySpark**를 클릭합니다.

    ![새 Jupyter 노트북 만들기](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "새 Jupyter 노트북 만들기")
4. 새 노트북이 만들어지고 Untitled.pynb 이름으로 열립니다. 맨 위에서 노트북 이름을 클릭하고 식별하기 쉬운 이름을 입력합니다.

    ![노트북 이름 제공](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "노트북 이름 제공")
5. PySpark 커널을 사용하여 노트북을 만들었기 때문에 컨텍스트를 명시적으로 만들 필요가 없습니다. 첫 번째 코드 셀을 실행하면 Spark 및 Hive 컨텍스트가 자동으로 만들어집니다. 이 시나리오에 필요한 형식을 가져와 시작할 수 있습니다. 빈 셀에 다음 코드 조각을 붙여넣은 다음 **SHIFT + ENTER**를 누릅니다.

        from pyspark.sql import Row
        from pyspark.sql.types import *


1. 클러스터에서 이미 사용할 수 있는 샘플 로그 데이터를 사용하는 RDD를 만듭니다. **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**에서 클러스터와 연결된 기본 저장소 계정의 데이터에 액세스할 수 있습니다.

        logs = sc.textFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


1. 샘플 로그 설정을 검색하여 이전 단계가 성공적으로 완료되었는지 확인합니다.

        logs.take(5)

    다음과 유사한 결과가 표시됩니다.

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>사용자 지정 Python 라이브러리를 사용하여 로그 데이터 분석
1. 위의 출력에서 처음 몇 줄은 헤더 정보를 포함하고 나머지 줄은 해당 헤더에 설명된 스키마와 일치합니다. 이러한 로그를 구문 분석하는 것은 복잡할 수 있습니다. 따라서 이러한 로그를 훨씬 쉽게 구문 분석하는 사용자 지정 Python 라이브러리(**iislogparser.py**)를 사용합니다. 기본적으로 이 라이브러리는 HDInsight의 Spark 클러스터( **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**)에 포함됩니다.

    그러나 이 라이브러리는 `PYTHONPATH`에 있지 않으므로 `import iislogparser`와(과) 같은 import 문을 사용하여 사용할 수 없습니다. 이 라이브러리를 사용하려면 모든 작업자 노드에 배포해야 합니다. 다음 조각을 실행합니다.

        sc.addPyFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


1. `iislogparser`은(는) 로그 줄이 머리글 행인 경우 함수 `None`을(를) 반환하고 로그 줄을 발견하는 경우 `LogLine` 클래스의 인스턴스를 반환하는 함수 `parse_log_line`을(를) 제공합니다. `LogLine` 클래스를 사용하여 RDD에서 로그 줄만을 추출합니다.

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
2. 몇 가지 추출된 로그 줄을 검색하여 단계가 성공적으로 완료되었는지 확인합니다.

       logLines.take(2)

   다음과 유사하게 출력되어야 합니다.

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
3. 그러면 `LogLine` 클래스에는 로그 항목에 오류 코드가 있는지 여부를 반환하는 `is_error()`와(과) 같은 몇 가지 유용한 메서드가 있습니다. 이를 사용하여 추출된 로그 줄의 오류의 수를 계산한 다음, 다른 파일에 모든 오류를 기록합니다.

       errors = logLines.filter(lambda p: p.is_error())
       numLines = logLines.count()
       numErrors = errors.count()
       print 'There are', numErrors, 'errors and', numLines, 'log entries'
       errors.map(lambda p: str(p)).saveAsTextFile('wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

   다음과 유사한 출력이 표시됩니다.

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       There are 30 errors and 646 log entries
4. **Matplotlib** 를 사용하여 데이터의 시각화를 만들 수도 있습니다. 예를 들어 오랜 시간 동안 실행되는 요청의 원인을 격리하려는 경우 평균적으로 제공하는 데 가장 많은 시간이 걸리는 파일을 찾을 수 있습니다.
   다음 코드 조각은 요청을 처리하는 데 가장 많은 시간이 걸리는 상위 25개의 리소스를 검색합니다.

       def avgTimeTakenByKey(rdd):
           return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                   lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                   lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                     .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

       avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

   다음과 유사한 출력이 표시됩니다.

       # -----------------
       # THIS IS AN OUTPUT
       # -----------------

       [(u'/blogposts/mvc4/step13.png', 197.5),
        (u'/blogposts/mvc2/step10.jpg', 179.5),
        (u'/blogposts/extractusercontrol/step5.png', 170.0),
        (u'/blogposts/mvc4/step8.png', 159.0),
        (u'/blogposts/mvcrouting/step22.jpg', 155.0),
        (u'/blogposts/mvcrouting/step3.jpg', 152.0),
        (u'/blogposts/linqsproc1/step16.jpg', 138.75),
        (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
        (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
        (u'/blogposts/nested/step2.jpg', 126.0),
        (u'/blogposts/adminpack/step1.png', 124.0),
        (u'/BlogPosts/datalistpaging/step2.png', 118.0),
        (u'/blogposts/mvc4/step35.png', 117.0),
        (u'/blogposts/mvcrouting/step2.jpg', 116.5),
        (u'/blogposts/aboutme/basketball.jpg', 109.0),
        (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
        (u'/blogposts/mvc4/step12.png', 106.0),
        (u'/blogposts/linq8/step0.jpg', 105.5),
        (u'/blogposts/mvc2/step18.jpg', 104.0),
        (u'/blogposts/mvc2/step11.jpg', 104.0),
        (u'/blogposts/mvcrouting/step1.jpg', 104.0),
        (u'/blogposts/extractusercontrol/step1.png', 103.0),
        (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
        (u'/blogposts/mvcrouting/step21.jpg', 101.0),
        (u'/blogposts/mvc4/step1.png', 98.0)]
5. 그림의 형식에 이 정보를 제공할 수도 있습니다. 플롯을 만드는 첫 번째 단계로, 먼저 임시 테이블 **AverageTime**을 만듭니다. 이 테이블은 특정 시간에 특수한 대기 시간 급증 현상이 없는지 확인하도록 시간으로 로그를 그룹화합니다.

       avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
       schema = StructType([StructField('Minutes', IntegerType(), True),
                            StructField('Time', FloatType(), True)])

       avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
       avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
6. 다음 SQL 쿼리를 실행하여 **AverageTime** 테이블의 모든 레코드를 가져올 수 있습니다.

       %%sql -o averagetime
       SELECT * FROM AverageTime

   `-o averagetime` 앞의 `%%sql` 매직은 쿼리 출력이 Jupyter 서버(일반적으로 클러스터의 헤드 노드)에서 로컬로 유지되도록 합니다. 출력은 [averagetime](https://pandas.pydata.org/) 이라는 이름이 지정된 **Pandas**데이터 프레임으로 유지됩니다.

   다음과 유사한 출력이 표시됩니다.

   ![SQL 쿼리 출력](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "SQL 쿼리 출력")

   `%%sql` 매직에 대한 자세한 내용은 [%%sql 매직에서 지원되는 매개 변수](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)를 참조하세요.
7. 이제 데이터 시각화를 구성하는 데 사용되는 라이브러리인 Matplotlib를 사용하여 플롯을 만들 수 있습니다. 로컬로 유지되는 **averagetime** 데이터 프레임에서 플롯을 만들어야 하므로 코드 조각은 `%%local` magic으로 시작해야 합니다. 그러면 코드가 Jupyter 서버에서 로컬로 실행됩니다.

       %%local
       %matplotlib inline
       import matplotlib.pyplot as plt

       plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
       plt.xlabel('Time (min)')
       plt.ylabel('Average time taken for request (ms)')

   다음과 유사한 출력이 표시됩니다.

   ![Matplotlib 출력](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib 출력")
8. 애플리케이션 실행을 완료한 후 리소스를 해제하도록 노트북을 종료해야 합니다. 이렇게 하기 위해 Notebook의 **파일** 메뉴에서 **닫기 및 중지**를 클릭합니다. 그러면 Notebook이 종료되고 닫힙니다.

## <a name="seealso"></a>참고 항목
* [개요: Azure HDInsight의 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: 음식 검사 결과를 예측하는 데 HDInsight의 Spark 사용](apache-spark-machine-learning-mllib-ipython.md)

### <a name="create-and-run-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>도구 및 확장
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark Scala 애플리케이션 생성 및 제출](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA용 HDInsight 도구 플러그 인을 사용하여 Apache Spark 애플리케이션을 원격으로 디버그](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](apache-spark-job-debugging.md)
