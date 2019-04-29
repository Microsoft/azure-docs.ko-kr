---
title: Azure HDInsight Tools - Hive, LLAP 또는 PySpark용 Visual Studio Code 사용 | Microsoft Docs
description: Azure HDInsight Tools for Visual Studio Code를 사용하여 쿼리와 스크립트를 만들고 제출하는 방법에 대해 알아봅니다.
Keywords: Visual Studio Code, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, 대화형 Hive, 대화형 쿼리
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 04e607517bc806b60d7e76e5076f9d3518e530eb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098340"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code 사용

Azure HDInsight Tools for Visual Studio Code를 사용하여 Apache Hive 일괄 작업, Apache Spark용 대화형 Hive 쿼리 및 PySpark 스크립트를 만들고 제출하는 방법을 알아봅니다. 먼저 Visual Studio Code에서 HDInsight 도구를 설치하는 방법을 설명한 다음, Hive 및 Spark에 작업을 제출하는 방법을 살펴보겠습니다.  

Azure HDInsight Tools는 Windows, Linux 및 macOS를 포함하여 Visual Studio Code에서 지원하는 플랫폼에 설치할 수 있습니다. 아래에서 다양한 플랫폼에 대한 필수 조건을 찾을 수 있습니다.


## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- HDInsight 클러스터. 클러스터를 만들려면 [HDInsight 시작](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)을 참조하세요.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono는 Linux 및 macOS에만 필요합니다.
- Visual Studio Code용 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)
- [Visual Studio Code용 PySpark 대화형 환경 설정](set-up-pyspark-interactive-environment.md)
- **HDexample**이라는 로컬 디렉터리.  이 문서에서는 **C:\HD\HDexample**을 사용합니다.

## <a name="install-azure-hdinsight-tools"></a>Azure HDInsight Tools 설치

필수 조건을 완료한 후 Azure HDInsight Tools for Visual Studio Code를 설치할 수 있습니다.  Azure HDInsight Tools를 설치하려면 다음 단계를 완료합니다.

1. Visual Studio Code를 엽니다.

2. 메뉴 모음에서 **보기** > **확장**으로 이동합니다.

3. 검색 상자에 **HDInsight**를 입력합니다.

4. 검색 결과에서 **Azure HDInsight Tools**를 선택한 다음, **설치**를 선택합니다.  

   ![HDInsight for Visual Studio Code Python 설치](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 설치된 후 **다시 로드**를 선택하여 **Azure HDInsight Tools** 확장을 활성화합니다.


## <a name="open-hdinsight-work-folder"></a>HDInsight 작업 폴더 열기

작업 폴더를 열고 Visual Studio Code에서 파일을 만들려면 다음 단계를 완료합니다.

1. 메뉴 모음에서 **파일** > **폴더 열기...** > **C:\HD\HDexample**로 이동한 다음, **폴더 선택** 단추를 선택합니다. 왼쪽의 **탐색기** 뷰에 폴더가 나타납니다.

2. **탐색기** 뷰에서 폴더, **HDexample**, 작업 폴더 옆의 **새 파일** 아이콘을 차례로 선택합니다.

   ![새 파일](./media/hdinsight-for-vscode/new-file.png)

3. .hql(Hive 쿼리) 또는 .py(Spark 스크립트) 파일 확장명 중 하나를 사용하여 새 파일 이름을 지정합니다.  이 예제에서는 **HelloWorld.hql**을 사용합니다.

## <a name="connect-to-hdinsight-cluster"></a>HDInsight 클러스터에 연결

Visual Studio Code에서 HDInsight 클러스터에 스크립트를 제출하려면 먼저 Azure 계정에 연결하거나 클러스터를 연결해야 합니다(Ambari 사용자 이름/암호 또는 도메인 가입 계정 사용).  Azure에 연결하려면 다음 단계를 완료합니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: Login**을 선택합니다.

    ![HDInsight Tools for Visual Studio Code 로그인](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. **출력** 창의 로그인 지침을 따릅니다.
    + Azure 전역 환경의 경우 **HDInsight: 로그인** 명령은 HDInsight 탐색기에서 **Azure에 로그인** 작업을 트리거하고 반대의 경우도 마찬가지입니다.

        ![Azure에 대한 로그인 지침](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + 기타 환경의 경우에는 로그인 지침을 따릅니다.

        ![기타 환경에 대한 로그인 지침](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

   연결되면 Visual Studio Code 창 왼쪽 아래의 상태 표시줄에 Azure 계정 이름이 표시됩니다.  
  

<h2 id="linkcluster">연결 만들기: Azure HDInsight</h2>

[Apache Ambari](https://ambari.apache.org/) 관리형 사용자 이름을 사용하여 정상적인 클러스터를 연결하거나, 도메인 사용자 이름(예: user1@contoso.com)을 사용하여 Enterprise Security Pack 보안 Hadoop 클러스터를 연결할 수 있습니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: Link a Cluster**를 입력합니다.

   ![클러스터 연결 명령](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 연결된 클러스터 유형 **Azure HDInsight**를 선택합니다.

3. HDInsight 클러스터 URL을 입력합니다.

4. Ambari 사용자 이름을 입력합니다. 기본값은 **admin**입니다.

5. Ambari 암호를 입력합니다.

6. 클러스터 유형을 선택합니다.

7. 확인을 위해 **출력** 보기를 검토합니다.

   > [!NOTE]  
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 사용자 이름 및 암호가 사용됩니다.  


## <a name="create-link-generic-livy-endpoint"></a>연결 만들기: 일반 Livy 엔드포인트

1. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: Link a Cluster**를 입력합니다.

2. 연결된 클러스터 유형 **일반 Livy 엔드포인트**를 선택합니다.

3. 일반 Livy 엔드포인트(예: http\://10.172.41.42:18080)를 입력합니다.

4. 인증 형식 **기본** 또는 **없음**을 선택합니다.  **기본**인 경우 다음을 수행합니다.  
    &emsp;a. Ambari 사용자 이름을 입력합니다. 기본값은 **admin**입니다.  
    &emsp;b. Ambari 암호를 입력합니다.

5. 확인을 위해 **출력** 보기를 검토합니다.

## <a name="list-hdinsight-clusters"></a>HDInsight 클러스터 나열

1. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: List Cluster**를 입력합니다.

2. 원하는 구독을 선택합니다.

3. **출력** 보기를 검토합니다.  연결된 클러스터와 Azure 구독 아래의 모든 클러스터가 보기에 표시됩니다.

    ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>기본 클러스터 설정

1. [이전에](#open-hdinsight-work-folder) 만든 **HDexample** 폴더가 닫힌 경우 다시 엽니다.  

2. [이전에](#open-hdinsight-work-folder) 만든 **HelloWorld.hql** 파일을 선택하면 스크립트 편집기에서 열립니다.

3. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-hdinsight-cluster)합니다.

4. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **HDInsight: Set Default Cluster**를 선택합니다.  

5. 현재 스크립트 파일에 대한 기본 클러스터로 사용할 클러스터를 선택합니다. **.VSCode\settings.json** 구성 파일이 자동으로 업데이트됩니다. 

   ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Azure 환경 설정

1. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-hdinsight-cluster)합니다.

2. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: Set Azure Environment**를 입력합니다.

3. 환경을 기본 로그인 항목으로 선택합니다.

4. 그렇지만 사용자의 기본 로그인 항목은 **.VSCode\settings.json**이미 저장되어 있습니다. 이 구성 파일에서 기본 로그인 항목을 직접 업데이트할 수도 있습니다. 

   ![기본 로그인 항목 구성 설정](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>대화형 Hive 쿼리, Hive 배치 스크립트 제출

HDInsight Tools for Visual Studio Code를 사용하면 대화형 Hive 쿼리 및 Hive 배치 스크립트를 HDInsight 클러스터에 제출할 수 있습니다.

1. [이전에](#open-hdinsight-work-folder) 만든 **HDexample** 폴더가 닫힌 경우 다시 엽니다.  

2. [이전에](#open-hdinsight-work-folder) 만든 **HelloWorld.hql** 파일을 선택하면 스크립트 편집기에서 열립니다.

3. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-hdinsight-cluster)합니다.

4. 다음 코드를 복사하여 Hive 파일에 붙여넣은 후 파일을 저장합니다.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고, **HDInsight: Hive Interactive**를 선택하여 쿼리를 제출하거나 바로 가기 **Ctrl + Alt + I**를 사용합니다.  **HDInsight: Hive Batch**를 선택하여 스크립트를 제출하거나 바로 가기 **Ctrl + Alt + H**를 사용합니다.  

6. 기본 클러스터를 지정하지 않은 경우 클러스터를 선택합니다. 이 도구에서는 상황에 맞는 메뉴를 사용하여 전체 스크립트 파일 대신 코드 블록을 제출할 수도 있습니다. 잠시 후 새 탭에 쿼리 결과가 표시됩니다.

   ![대화형 Hive 결과](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **결과** 패널: 전체 결과를 CSV, JSON 또는 Excel 파일로 로컬 경로에 저장하거나 여러 줄을 선택할 수 있습니다.

    - **메시지** 패널: **줄** 번호를 선택하면 실행 중인 스크립트의 첫 번째 줄로 이동합니다.

## <a name="submit-interactive-pyspark-queries"></a>대화형 PySpark 쿼리 제출

1. [이전에](#open-hdinsight-work-folder) 만든 **HDexample** 폴더가 닫힌 경우 다시 엽니다.  

2. [이전](#open-hdinsight-work-folder) 단계에 따라 새 파일 **HelloWorld.py**를 만듭니다.

3. 필수 조건인 Python을 설치하지 않은 경우 Python 확장 권장 사항 대화 상자가 표시됩니다.  Visual Studio Code를 설치하고 다시 로드하여 설치를 완료합니다.

    >![HDInsight for Visual Studio Code Python 설치](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-hdinsight-cluster)합니다.

5. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

6. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고, **HDInsight: PySpark Interactive**를 선택하여 쿼리를 제출하거나 바로 가기 **Ctrl+Alt+I**를 사용합니다.  

7. 기본 클러스터를 지정하지 않은 경우 클러스터를 선택합니다. 이 도구에서는 상황에 맞는 메뉴를 사용하여 전체 스크립트 파일 대신 코드 블록을 제출할 수도 있습니다. 잠시 후 새 탭에 쿼리 결과가 표시됩니다.

   ![Python 작업 결과 제출](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. 이 도구는 **SQL 절** 쿼리도 지원합니다.

   ![Python 결과 작업 제출](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) 쿼리를 실행할 때 제출 상태가 아래쪽 상태 표시줄의 왼쪽에 표시됩니다. 상태가 **PySpark 커널(작업 중)** 이면 다른 쿼리를 제출하지 마세요.  

>[!NOTE]  
>클러스터는 세션 정보를 유지할 수 있습니다. 정의된 변수, 함수 및 해당 값은 세션에 유지되므로 동일한 클러스터에 대한 여러 서비스 호출에서 참조될 수 있습니다. 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3은 Spark2.2/2.3에서 지원되지 않습니다.

PySpark3은 Spark 2.2 클러스터 및 Spark2.3 클러스터에서 더 이상 지원되지 않고, “PySpark”만 Python에서 지원됩니다. Python3에서 Spark 2.2/2.3에 대한 제출이 실패하는 것은 알려진 문제입니다.

   ![Python3에 제출 시 오류 발생](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Python2.x를 사용하려면 다음 단계를 수행합니다. 

1. Python 2.7을 로컬 컴퓨터에 설치하고 시스템 경로에 추가합니다.

2. Visual Studio Code를 다시 시작합니다.

3. 상태 표시줄에서 **Python XXX**를 클릭하여 Python 2로 전환하고 대상 Python을 선택합니다.

   ![Python 버전 선택](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>PySpark 배치 작업 제출

1. [이전에](#open-hdinsight-work-folder) 만든 **HDexample** 폴더가 닫힌 경우 다시 엽니다.  

2. [이전](#open-hdinsight-work-folder) 단계에 따라 새 파일 **BatchFile.py**를 만듭니다.

3. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-hdinsight-cluster)합니다.

4. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음, **HDInsight: PySpark Batch**를 선택하거나 바로 가기 **Ctrl + Alt + H**를 사용합니다. 

5. PySpark 작업을 제출할 클러스터를 선택합니다. 

   ![Python 작업 결과 제출](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Python 작업을 제출한 후 전송 로그가 Visual Studio Code의 **출력** 창에 나타납니다. **Spark UI URL** 및 **Yarn UI URL**도 표시됩니다. 웹 브라우저에서 URL을 열어 작업 상태를 추적할 수 있습니다.

## <a name="apache-livy-configuration"></a>Apache Livy 구성

[Apache Livy](https://livy.incubator.apache.org/) 구성은 지원되며, 작업 영역 폴더의 **.VSCode\settings.json**에서 설정할 수 있습니다. 현재 Livy 구성은 Python 스크립트만 지원합니다. 자세한 내용은 [Livy 추가 정보](https://github.com/cloudera/livy/blob/master/README.rst )를 참조하세요.

<a id="triggerlivyconf"></a>**Livy 구성을 트리거하는 방법**

방법 1  
1. 메뉴 모음에서 **파일** > **기본 설정** > **설정**으로 이동합니다.  
2. **검색 설정** 텍스트 상자에 **HDInsight Job Sumission: Livy Conf**를 입력합니다.  
3. 관련 검색 결과에 대해 **settings.json에서 편집**을 선택합니다.

방법 2   
파일을 제출합니다. .vscode 폴더가 작업 폴더에 자동으로 추가됩니다. **.vscode\settings.json**을 클릭하여 Livy 구성을 찾을 수 있습니다.

+ 프로젝트 설정:

    ![Livy 구성](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>**driverMomory** 및 **executorMomry** 설정의 경우 단위가 있는 값을 설정합니다(예: 1g 또는 1024m). 

+ 지원되는 Livy 구성:   

    **POST/일괄 처리**   
    요청 본문

    | 이름 | description | 형식 | 
    | :- | :- | :- | 
    | file | 실행할 애플리케이션을 포함하는 파일 | 경로(필수) | 
    | proxyUser | 작업을 실행할 때 가장하는 사용자 | 문자열 | 
    | className | 애플리케이션 Java/Spark 주 클래스 | 문자열 |
    | args | 애플리케이션에 대한 명령줄 인수 | 문자열 목록 | 
    | ./jars | 이 세션에 사용할 jars | 문자열 목록 | 
    | pyFiles | 이 세션에 사용할 Python 파일 | 문자열 목록 |
    | 업로드 | 이 세션에 사용할 파일 | 문자열 목록 |
    | driverMemory | 드라이버 프로세스에 사용할 메모리의 양 | 문자열 |
    | driverCores | 드라이버 프로세스에 사용할 코어 수 | int |
    | executorMemory | 실행기 프로세스당 사용할 메모리의 양 | 문자열 |
    | executorCores | 각 실행기에 사용할 코어 수 | int |
    | numExecutors | 이 세션에 대해 시작할 실행기 수 | int |
    | 아카이브 | 이 세션에 사용할 아카이브 | 문자열 목록 |
    | 큐 | 제출되는 YARN 큐의 이름 | 문자열 |
    | 이름 | 이 세션의 이름 | 문자열 |
    | conf | Spark 구성 속성 | key=val의 맵 |

    응답 본문   
    만든 일괄 처리 개체입니다.

    | 이름 | description | 형식 | 
    | :- | :- | :- | 
    | id | 세션 ID | int | 
    | appId | 이 세션의 애플리케이션 ID |  String |
    | appInfo | 자세한 애플리케이션 정보 | key=val의 맵 |
    | 로그 | 로그 줄 | 문자열 목록 |
    | state |   일괄 처리 상태 | 문자열 |

>[!NOTE]
>스크립트를 제출하면 할당된 Livy 구성이 출력 창에 표시됩니다.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>탐색기에서 Azure HDInsight와 통합

**Azure HDInsight**가 탐색기 뷰에 추가되었습니다. **Azure HDInsight**를 통해 직접 클러스터를 찾고 관리할 수 있습니다.

1. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-hdinsight-cluster)합니다.

2. 메뉴 모음에서 **보기** > **탐색기**로 이동합니다.

3. 왼쪽 창에서 **AZURE HDINSIGHT**를 펼칩니다.  사용 가능한 구독 및 클러스터(Spark, Hadoop 및 HBase가 지원됨)가 나열됩니다. 

   ![Azure HDInsight 구독](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. 클러스터를 확장하여 Hive 메타데이터 데이터베이스 및 테이블 스키마를 확인합니다.

   ![Azure HDInsight 클러스터](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>추가 기능

Visual Studio Code용 HDInsight는 다음 기능을 지원합니다.

- **IntelliSense 자동 완성**. 키워드, 메서드, 변수 등에 대한 추천 항목이 팝업됩니다. 다음과 같이 다양한 아이콘이 여러 형식의 개체를 나타냅니다.

    ![HDInsight Tools for Visual Studio Code IntelliSense 개체 형식](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 오류 표식**. 언어 서비스에서 Hive 스크립트에 대한 편집 오류에 밑줄을 표시합니다.     
- **구문 강조 표시**. 언어 서비스는 변수, 키워드, 데이터 형식, 함수 등을 구분하기 위해 서로 다른 색상을 사용합니다. 

    ![HDInsight Tools for Visual Studio Code 구문 강조 표시](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>클러스터 링크 해제

1. 메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: Unlink a Cluster**를 입력합니다.  

2. 링크 해제할 클러스터를 선택합니다.  

3. 확인을 위해 **출력** 보기를 검토합니다.  


## <a name="logout"></a>로그아웃  

메뉴 모음에서 **보기** > **명령 팔레트...** 로 이동한 다음, **HDInsight: Logout** 명령을 입력합니다.  **로그아웃되었습니다.** 라는 팝업이 오른쪽 아래에 표시됩니다.


## <a name="next-steps"></a>다음 단계
Visual Studio Code용 HDInsight 사용 방법의 데모 비디오는 [Visual Studio Code용 HDInsight](https://go.microsoft.com/fwlink/?linkid=858706)를 참조하세요.
