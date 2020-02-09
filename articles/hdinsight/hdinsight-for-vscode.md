---
title: Visual Studio Code 용 Azure HDInsight
description: Visual Studio Code에 대해 Spark & Hive 도구 (Azure HDInsight)를 사용 하 여 쿼리와 스크립트를 만들고 제출 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435679"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Visual Studio Code Spark & Hive 도구 사용

Visual Studio Code Spark & Hive 도구를 사용 하 여 Apache Hive batch 작업, 대화형 Hive 쿼리 및 Apache Spark에 대 한 PySpark 스크립트를 만들고 제출 하는 방법에 대해 알아봅니다. 먼저 Visual Studio Code에서 Spark & Hive 도구를 설치 하는 방법을 설명한 다음 Spark & Hive 도구에 작업을 제출 하는 방법을 안내 합니다.  

Windows, Linux 및 macOS를 포함하여 Visual Studio Code에서 지원되는 플랫폼에 Spark & Hive Tools를 설치할 수 있습니다. 다른 플랫폼에 대 한 다음 필수 구성 요소를 확인 합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- Azure HDInsight 클러스터를 만듭니다. 클러스터를 만들려면 [HDInsight 시작](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)을 참조하세요. 또는 Apache Livy 엔드포인트을 지 원하는 Spark 및 Hive 클러스터를 사용 합니다.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono는 Linux 및 macOS에만 필요 합니다.
- [Visual Studio Code에 대 한 PySpark 대화형 환경](set-up-pyspark-interactive-environment.md)입니다.
- 로컬 디렉터리입니다. 이 문서에서는 **C:\HD\HDexample**을 사용합니다.

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools 설치

필수 구성 요소를 충족 한 후 다음 단계를 수행 하 여 Visual Studio Code에 대 한 Spark & Hive 도구를 설치할 수 있습니다.

1. Visual Studio Code를 엽니다.

2. 메뉴 모음에서 **보기** > **확장**으로 이동합니다.

3. 검색 상자에 **Spark & Hive**를 입력합니다.

4. 검색 결과에서 **Spark & Hive 도구** 를 선택 하 고 **설치**를 선택 합니다.

   ![Python 설치 Visual Studio Code에 대 한 Spark & Hive](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 필요한 경우 **다시 로드** 를 선택 합니다.

## <a name="open-a-work-folder"></a>작업 폴더 열기

작업 폴더를 열고 Visual Studio Code 파일을 만들려면 다음 단계를 수행 합니다.

1. 메뉴 모음에서 **파일** > **폴더 열기** ... > **C:\HD\HDexample**으로 이동한 다음 **폴더 선택** 단추를 선택 합니다. 왼쪽 **탐색기** 뷰에 폴더가 표시됩니다.

2. **탐색기** 보기에서 **hdexample** 대상 폴더를 선택 하 고 작업 폴더 옆의 **새 파일** 아이콘을 선택 합니다.

   ![visual studio code 새 파일 아이콘](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. `.hql` (Hive 쿼리) 또는 `.py` (Spark 스크립트) 파일 확장명 중 하나를 사용 하 여 새 파일의 이름을로 합니다. 이 예제에서는 **HelloWorld.hql**을 사용합니다.

## <a name="set-the-azure-environment"></a>Azure 환경 설정

국가별 클라우드 사용자의 경우 다음 단계에 따라 먼저 Azure 환경을 설정 하 고 azure **: 로그인** 명령을 사용 하 여 azure에 로그인 합니다.

1. **파일** > **기본** 설정 > **설정**으로 이동 합니다.
2. 다음 문자열을 검색 합니다. **Azure: Cloud**.
3. 목록에서 국가 클라우드를 선택 합니다.

   ![기본 로그인 항목 구성 설정](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Azure 계정에 연결

Visual Studio Code에서 클러스터에 스크립트를 제출 하려면 먼저 Azure 계정에 연결 하거나 클러스터를 연결 해야 합니다 (Apache Ambari 사용자 이름 및 암호 자격 증명 또는 도메인 가입 계정 사용). Azure에 연결 하려면 다음 단계를 따르세요.

1. 메뉴 모음에서 **보기** > **명령 팔레트 ...** 로 이동 하 고 **Azure: 로그인**을 입력 합니다.

    ![Visual Studio Code 로그인에 대 한 Spark & Hive 도구](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 로그인 지침에 따라 Azure에 로그인 합니다. 연결 되 면 Azure 계정 이름이 Visual Studio Code 창의 아래쪽에 있는 상태 표시줄에 표시 됩니다.  

## <a name="link-a-cluster"></a>클러스터 연결

### <a name="link-azure-hdinsight"></a>링크: Azure HDInsight

[Apache Ambari](https://ambari.apache.org/)관리 사용자 이름을 사용 하 여 일반 클러스터를 연결 하거나, 도메인 사용자 이름 (예: `user1@contoso.com`)을 사용 하 여 엔터프라이즈 보안 팩 보안 Hadoop 클러스터를 연결할 수 있습니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트 ...** 로 이동 하 고 **Spark/Hive: 클러스터 연결**을 입력 합니다.

   ![명령 팔레트 링크 클러스터 명령](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 연결된 클러스터 유형 **Azure HDInsight**를 선택합니다.

3. HDInsight 클러스터 URL을 입력 합니다.

4. Ambari 사용자 이름을 입력 합니다. 기본값은 **admin**입니다.

5. Ambari 암호를 입력 합니다.

6. 클러스터 유형을 선택 합니다.

7. 클러스터의 표시 이름을 설정 합니다 (선택 사항).

8. 확인을 위해 **출력** 뷰를 검토합니다.

   > [!NOTE]  
   > 클러스터가 Azure 구독에 로그인 하 고 클러스터에 연결 된 경우 연결 된 사용자 이름 및 암호가 사용 됩니다.  

### <a name="link-generic-livy-endpoint"></a>링크: 제네릭 Livy 엔드포인트

1. 메뉴 모음에서 **보기** > **명령 팔레트 ...** 로 이동 하 고 **Spark/Hive: 클러스터 연결**을 입력 합니다.

2. 연결된 클러스터 유형 **일반 Livy 엔드포인트**를 선택합니다.

3. 제네릭 Livy 엔드포인트을 입력 합니다. 예: http\://10.172.41.42:18080.

4. 인증 형식 **기본** 또는 **없음**을 선택합니다.  **기본**을 선택 하는 경우:  
    &emsp;a. Ambari 사용자 이름을 입력 합니다. 기본값은 **admin**입니다.  
    &emsp;b. Ambari 암호를 입력 합니다.

5. 확인을 위해 **출력** 뷰를 검토합니다.

## <a name="list-clusters"></a>클러스터 나열

1. 메뉴 모음에서 **보기** > **명령 팔레트 ...** 로 이동 하 여 **Spark/Hive: List Cluster**를 입력 합니다.

2. 원하는 구독을 선택 합니다.

3. **출력** 뷰를 검토합니다. 이 보기에는 Azure 구독에 연결 된 클러스터 (또는 클러스터)와 모든 클러스터가 표시 됩니다.

    ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>기본 클러스터 설정

1. 종결 된 경우 [앞](#open-a-work-folder)에서 설명한 **hdexample** 를 다시 엽니다.  

2. [앞](#open-a-work-folder)에서 만든 **hql** 파일을 선택 합니다. 스크립트 편집기에서 열립니다.

3. 스크립트 편집기를 마우스 오른쪽 단추로 클릭 한 다음 **Spark/Hive: 기본 클러스터 설정**을 선택 합니다.  

4. 아직 수행 하지 않은 경우 Azure 계정에 [연결](#connect-to-an-azure-account) 하거나 클러스터를 연결 합니다.

5. 현재 스크립트 파일의 기본 클러스터로 사용할 클러스터를 선택합니다. 도구가 자동으로를 업데이트 합니다 **. Vscode\settingstt\settingsvfile** :

   ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>대화형 Hive 쿼리 및 Hive 배치 스크립트 제출

Visual Studio Code Spark & Hive 도구를 사용 하 여 대화형 Hive 쿼리 및 Hive 배치 스크립트를 클러스터에 제출할 수 있습니다.

1. 종결 된 경우 [앞](#open-a-work-folder)에서 설명한 **hdexample** 를 다시 엽니다.  

2. [앞](#open-a-work-folder)에서 만든 **hql** 파일을 선택 합니다. 스크립트 편집기에서 열립니다.

3. 다음 코드를 복사 하 여 Hive 파일에 붙여넣은 다음 저장 합니다.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. 아직 수행 하지 않은 경우 Azure 계정에 [연결](#connect-to-an-azure-account) 하거나 클러스터를 연결 합니다.

5. 스크립트 편집기를 마우스 오른쪽 단추로 클릭 하 고 **Hive: Interactive** 를 선택 하 여 쿼리를 제출 하거나 Ctrl + Alt + I 바로 가기 키를 사용 합니다.  **Hive: Batch** 를 선택 하 여 스크립트를 제출 하거나 Ctrl + Alt + H 바로 가기 키를 사용 합니다.  

6. 기본 클러스터를 지정 하지 않은 경우 클러스터를 선택 합니다. 또한이 도구를 사용 하면 상황에 맞는 메뉴를 사용 하 여 전체 스크립트 파일 대신 코드 블록을 제출할 수 있습니다. 잠시 후 쿼리 결과가 새 탭에 나타납니다.

   ![대화형 Apache Hive 쿼리 결과](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **결과** 패널: 전체 결과를 CSV, JSON 또는 Excel 파일로 로컬 경로에 저장 하거나 여러 줄을 선택할 수 있습니다.

    - **메시지** 패널: **줄** 번호를 선택 하면 실행 중인 스크립트의 첫 번째 줄로 이동 합니다.

## <a name="submit-interactive-pyspark-queries"></a>대화형 PySpark 쿼리 제출

대화형 PySpark 쿼리를 제출 하려면 다음 단계를 수행 합니다.

1. 종결 된 경우 [앞](#open-a-work-folder)에서 설명한 **hdexample** 를 다시 엽니다.  

2. [이전](#open-a-work-folder) 단계에 따라 새 **HelloWorld.py** 파일을 만듭니다.

3. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.

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

4. 아직 수행 하지 않은 경우 Azure 계정에 [연결](#connect-to-an-azure-account) 하거나 클러스터를 연결 합니다.

5. 모든 코드를 선택 하 고 스크립트 편집기를 마우스 오른쪽 단추로 클릭 한 다음 **Spark: PySpark Interactive** 를 선택 하 여 쿼리를 제출 합니다. 또는 Ctrl + Alt + I 바로 가기를 사용 합니다.

   ![pyspark 대화형 상황에 맞는 메뉴](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 기본 클러스터를 지정 하지 않은 경우 클러스터를 선택 합니다. 몇 분 후에 **Python 대화형** 결과가 새 탭에 나타납니다. 또한이 도구를 사용 하면 상황에 맞는 메뉴를 사용 하 여 전체 스크립트 파일 대신 코드 블록을 제출할 수 있습니다.

   ![pyspark 대화형 python 대화형 창](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. **%% Info**를 입력 한 다음 Shift + enter를 눌러 작업 정보를 확인 합니다 (선택 사항).

   ![pyspark 대화형 보기 작업 정보](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 이 도구는 **SPARK SQL** 쿼리도 지원 합니다.

   ![Pyspark 대화형 보기 결과](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   제출 상태는 쿼리를 실행할 때 낮은 상태 표시줄의 왼쪽에 표시 됩니다. 상태가 **PySpark 커널(작업 중)** 이면 다른 쿼리를 제출하지 마세요.  

   > [!NOTE]
   >
   > 설정에서 **Python 확장을 사용 하도록** 설정 하는 경우 (기본적으로 선택 됨) 제출 된 pyspark 상호 작용 결과에는 이전 창이 사용 됩니다.
   >
   > ![pyspark 대화형 python 확장 사용 안 함](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>PySpark 일괄 작업 제출

1. 종결 된 경우 [앞](#open-a-work-folder)에서 설명한 **hdexample** 를 다시 엽니다.  

2. [이전](#open-a-work-folder) 단계를 수행 하 여 새 **BatchFile.py** 파일을 만듭니다.

3. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.

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

4. 아직 수행 하지 않은 경우 Azure 계정에 [연결](#connect-to-an-azure-account) 하거나 클러스터를 연결 합니다.

5. 스크립트 편집기를 마우스 오른쪽 단추로 클릭 한 다음 **Spark: PySpark Batch**를 선택 하거나 Ctrl + Alt + H 바로 가기 키를 사용 합니다.

6. PySpark 작업을 제출할 클러스터를 선택 합니다.

   ![Python 작업 결과 출력 제출](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Python 작업을 제출하면 Visual Studio Code의 **출력** 창에 제출 로그가 표시됩니다. Spark UI URL 및 Yarn UI URL도 표시 됩니다. 웹 브라우저에서 URL을 열어 작업 상태를 추적할 수 있습니다.

## <a name="apache-livy-configuration"></a>Apache Livy 구성

[Apache Livy](https://livy.incubator.apache.org/) 구성이 지원 됩니다. 에서 구성할 수 있습니다 **. 작업 영역 폴더에 있는 Vscode\settingsfile** 입니다. 현재, Livy 구성은 Python 스크립트만 지원합니다. 자세한 내용은 [LIVY 추가](https://github.com/cloudera/livy/blob/master/README.rst )정보를 참조 하세요.

<a id="triggerlivyconf"></a>**Livy 구성을 트리거하는 방법**

방법 1  
1. 메뉴 모음에서 **파일** > **기본 설정** > **설정**으로 이동합니다.
2. **검색 설정** 상자에 **HDInsight 작업 제출: Livy 회의**를 입력 합니다.  
3. 관련 검색 결과에 대해 **settings.json에서 편집**을 선택합니다.

방법 2 파일을 제출 합니다. vscode 폴더가 작업 폴더에 자동으로 추가 되는 것을 볼 수 있습니다. Livy 구성은 **.vscode\settings.s**를 선택 하 여 볼 수 있습니다.

+ 프로젝트 설정:

    ![HDInsight Apache Livy 구성](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >**Drivermemory** 및 **executormemory** 설정에 대해 값과 단위를 설정 합니다. 예: 1g 또는 1024m.

+ 지원 되는 Livy 구성:

    **사후 배치/일괄 처리** 요청 본문

    | name | description | type |
    | :- | :- | :- |
    | 파일 | 실행할 애플리케이션이 포함된 파일 | Path (필수) |
    | proxyUser | 작업을 실행할 때 가장할 사용자 | String |
    | className | 애플리케이션 Java/Spark 주 클래스 | String |
    | args | 응용 프로그램에 대 한 명령줄 인수 | 문자열 목록 |
    | jars | 이 세션에서 사용할 jar | 문자열 목록 | 
    | pyFiles | 이 세션에서 사용할 Python 파일 | 문자열 목록 |
    | files | 이 세션에 사용할 파일입니다. | 문자열 목록 |
    | driverMemory | 드라이버 프로세스에 사용할 메모리 크기 | String |
    | driverCores | 드라이버 프로세스에 사용할 코어 수 | Int |
    | executorMemory | 실행기 프로세스당 사용할 메모리 크기 | String |
    | executorCores | 각 실행기에 사용할 코어 수 | Int |
    | numExecutors | 이 세션에서 시작할 실행기 수 | Int |
    | archives | 이 세션에서 사용할 보관 파일 | 문자열 목록 |
    | queue | 제출할 YARN 큐의 이름입니다.| String |
    | name | 이 세션의 이름 | String |
    | conf | Spark 구성 속성 | 키=값 맵 |

    응답 본문 생성 된 일괄 처리 개체입니다.

    | name | description | type |
    | :- | :- | :- |
    | id | 세션 ID | Int |
    | appId | 이 세션의 응용 프로그램 id | String |
    | appInfo | 자세한 응용 프로그램 정보 | 키=값 맵 |
    | log | 로그 줄 | 문자열 목록 |
    | state |일괄 처리 상태 | String |

    > [!NOTE]
    > 할당 된 Livy 구성은 스크립트를 제출할 때 출력 창에 표시 됩니다.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>탐색기에서 Azure HDInsight와 통합

**Azure HDInsight** 탐색기를 통해 클러스터의 Hive 테이블을 직접 미리 볼 수 있습니다.

1. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-an-azure-account)합니다.

2. 맨 왼쪽 열에서 **Azure** 아이콘을 선택 합니다.

3. 왼쪽 창에서 **AZURE: HDINSIGHT**를 확장 합니다. 사용 가능한 구독과 클러스터가 나열 됩니다.

4. 클러스터를 확장 하 여 Hive 메타 데이터 데이터베이스 및 테이블 스키마를 확인 합니다.

5. Hive 테이블을 마우스 오른쪽 단추로 클릭 합니다. 예: **hivesampletable**. **미리 보기**를 선택합니다.

   ![Visual Studio Code 미리 보기 hive 테이블의 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **미리 보기 결과** 창이 열립니다.

   ![Visual Studio Code 미리 보기 결과 창에 대 한 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- 결과 패널

   전체 결과를 CSV, JSON 또는 Excel 파일로 로컬 경로에 저장 하거나 여러 줄을 선택할 수 있습니다.

- 메시지 패널
   1. 테이블의 행 수가 100 보다 큰 경우 "Hive 테이블에 대해 첫 번째 100 행이 표시 됩니다." 라는 메시지가 표시 됩니다.
   2. 테이블의 행 수가 100 보다 작거나 같으면 다음과 같은 메시지가 표시 됩니다. "Hive 테이블에 대해 60 행이 표시 됩니다."
   3. 테이블에 내용이 없는 경우 다음 메시지가 표시 됩니다. "Hive 테이블에 대해 0 개의 행이 표시 됩니다."

        >[!NOTE]
        >
        >Linux에서 xclip을 설치 하 여 복사 테이블 데이터를 사용 하도록 설정 합니다.
        >
        >![Linux에서 Visual Studio code에 대 한 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>추가 기능

Visual Studio Code에 대 한 Spark & Hive는 또한 다음과 같은 기능을 지원 합니다.

- **IntelliSense 자동 완성**. 키워드, 메서드, 변수 및 기타 프로그래밍 요소에 대 한 제안이 팝업 됩니다. 다음과 같이 개체 형식마다 다른 아이콘으로 표시됩니다.

    ![IntelliSense 개체 Visual Studio Code Spark & Hive 도구](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense 오류 표식**. 언어 서비스는 Hive 스크립트의 편집 오류에 밑줄을 긋습니다.     
- **구문 강조 표시**. 언어 서비스는 변수, 키워드, 데이터 형식, 함수 및 기타 프로그래밍 요소를 구분 하는 데 서로 다른 색을 사용 합니다.

    ![Visual Studio Code용 Spark & Hive Tools 구문 강조 표시](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>독자 전용 역할

클러스터에 대 한 읽기 전용 역할이 할당 된 사용자는 더 이상 HDInsight 클러스터에 작업을 제출 하거나 Hive 데이터베이스를 볼 수 없습니다. 클러스터 관리자에 게 문의 하 여 [Azure Portal](https://ms.portal.azure.com/)에서 [**HDInsight 클러스터 운영자**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) 에 게 역할을 업그레이드 하십시오. 유효한 Ambari 자격 증명이 있는 경우 다음 지침을 사용 하 여 클러스터를 수동으로 연결할 수 있습니다.

### <a name="browse-the-hdinsight-cluster"></a>HDInsight 클러스터 찾아보기  

Azure HDInsight 탐색기를 선택 하 여 HDInsight 클러스터를 확장 하는 경우 클러스터에 대 한 읽기 전용 역할이 있는 경우 클러스터를 연결 하 라는 메시지가 표시 됩니다. Ambari 자격 증명을 사용 하 여 클러스터에 연결 하려면 다음 메서드를 사용 합니다.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>HDInsight 클러스터에 작업 제출

HDInsight 클러스터에 작업을 제출 하는 경우 클러스터에 대 한 읽기 전용 역할을 하는 경우 클러스터를 연결 하 라는 메시지가 표시 됩니다. Ambari 자격 증명을 사용 하 여 클러스터에 연결 하려면 다음 단계를 사용 합니다.

### <a name="link-to-the-cluster"></a>클러스터에 연결

1. 올바른 Ambari 사용자 이름을 입력 하세요.
2. 올바른 암호를 입력 하십시오.

   ![Visual Studio Code 사용자 이름에 대 한 Spark & Hive 도구](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Visual Studio Code 암호를 위한 Spark & Hive 도구](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >`Spark / Hive: List Cluster`를 사용 하 여 연결 된 클러스터를 확인할 수 있습니다.
  >
  >![연결 된 Visual Studio Code 판독기 용 Spark & Hive 도구](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Data Lake Storage Gen2 계정 찾아보기

Data Lake Storage Gen2 계정을 확장 하기 위해 Azure HDInsight 탐색기를 선택 하면 Azure 계정에 Gen2 저장소에 대 한 액세스 권한이 없는 경우 저장소 액세스 키를 입력 하 라는 메시지가 표시 됩니다. 액세스 키의 유효성을 검사 한 후 Data Lake Storage Gen2 계정은 자동으로 확장 됩니다.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Data Lake Storage Gen2를 사용 하 여 HDInsight 클러스터에 작업 제출

Data Lake Storage Gen2를 사용 하 여 HDInsight 클러스터에 작업을 제출 하는 경우 Azure 계정에 Gen2 저장소에 대 한 쓰기 권한이 없는 경우 저장소 액세스 키를 입력 하 라는 메시지가 표시 됩니다. 액세스 키의 유효성을 검사 한 후 작업이 성공적으로 제출 됩니다.

![Visual Studio Code AccessKey 용 Spark & Hive 도구](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Azure Portal에서 저장소 계정에 대 한 액세스 키를 가져올 수 있습니다. 자세한 내용은 [저장소 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조 하세요.

## <a name="unlink-cluster"></a>클러스터 연결 해제

1. 메뉴 모음에서 **보기** > **명령 팔레트**로 이동한 다음 **Spark/Hive: 클러스터 연결 해제**를 입력 합니다.  

2. 연결을 끊을 클러스터를 선택 합니다.  

3. 확인에 대 한 **출력** 뷰를 참조 하세요.  

## <a name="sign-out"></a>로그아웃  

메뉴 모음에서 **보기** > **명령 팔레트**로 이동한 다음 **Azure: 로그 아웃**을 입력 합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio Code에 Spark & Hive를 사용 하는 방법을 보여 주는 비디오는 [Visual Studio Code에 대 한 spark & hive](https://go.microsoft.com/fwlink/?linkid=858706)를 참조 하세요.
