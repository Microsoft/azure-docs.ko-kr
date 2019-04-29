---
title: Azure HDInsight Tools - Visual Studio Code용 PySpark 대화형 환경 설정
description: Azure HDInsight Tools for Visual Studio Code를 사용하여 쿼리와 스크립트를 만들고 제출하는 방법에 대해 알아봅니다.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,대화형 Hive,대화형 쿼리
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 882a071731837b0f7d03ac374a7c831a20a3927e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766402"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code용 PySpark 대화형 환경 설정

다음 단계에서는 VS Code에서 PySpark 대화형 환경을 설정하는 방법을 보여 줍니다.

**python/pip** 명령을 사용하여 홈 경로에서 가상 환경을 빌드합니다. 다른 버전을 사용하려면 **python/pip** 명령의 기본 버전을 수동으로 변경해야 합니다. 자세한 내용은 [update-alternatives](https://linux.die.net/man/8/update-alternatives)를 참조하세요.

1. [Python](https://www.python.org/downloads/) 및 [pip](https://pip.pypa.io/en/stable/installing/)를 설치합니다.
   
   + [https://www.python.org/downloads/](https://www.python.org/downloads/)에서 Python을 설치합니다.
   + [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/)에서 pip를 설치합니다. (Python 설치에서 설치되지 않은 경우)
   + 다음 명령을 사용하여 Python 및 pip가 제대로 설치되었는지 유효성을 검사합니다. (선택 사항)
 
        ![Python pip 버전](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > MacOS 기본 버전을 사용하는 대신 Python을 수동으로 설치하는 것이 좋습니다.


2. 아래 명령을 실행하여 **virtualenv**를 설치합니다.
   
   ```
   pip install virtualenv
   ```

3. Linux의 경우에만 오류 메시지가 표시될 경우 아래 명령을 실행하여 필수 패키지를 설치합니다.
   
    ![Python pip 버전](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. VS Code를 다시 시작하고 **HDInsight: PySpark 대화형**을 실행하는 스크립트 편집기로 돌아옵니다.

## <a name="next-steps"></a>다음 단계

### <a name="demo"></a>데모
* HDInsight for VS Code: [비디오](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>도구 및 확장
* [Azure HDInsight Tool for Visual Studio Code 사용](hdinsight-for-vscode.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 Apache Spark Scala 애플리케이션 만들기 및 제출](spark/apache-spark-intellij-tool-plugin.md)
* [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 원격으로 Apache Spark 애플리케이션 디버그](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션 만들기](spark/apache-spark-eclipse-tool-plugin.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Apache Zeppelin을 사용 하 여 Azure HDInsight에서 Apache Hive 쿼리를 실행 하려면](hdinsight-connect-hive-zeppelin.md)
