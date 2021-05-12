---
title: Azure HDInsight 도구를 사용한 PySpark 대화형 환경
description: Azure HDInsight Tools for Visual Studio Code를 사용하여 쿼리와 스크립트를 만들고 제출하는 방법에 대해 알아봅니다.
keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,대화형 Hive,대화형 쿼리
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: a6247116cdf579691e48883231f57712da36c4ad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864570"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Visual Studio Code용 PySpark 대화형 환경 설정

다음 단계는 VSCode에서 PySpark 대화형 환경을 설정하는 방법을 보여줍니다. 이 단계는 Windows 외의 사용자만을 위한 것입니다.

**python/pip** 명령을 사용하여 홈 경로에서 가상 환경을 빌드합니다. 다른 버전을 사용하려면 **python/pip** 명령의 기본 버전을 수동으로 변경해야 합니다. 자세한 내용은 [update-alternatives](https://linux.die.net/man/8/update-alternatives)를 참조하세요.

1. [Python](https://www.python.org/downloads/) 및 [pip](https://pip.pypa.io/en/stable/installing/)를 설치합니다.

   * [https://www.python.org/downloads/](https://www.python.org/downloads/)에서 Python을 설치합니다. 
   * [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/)에서 pip를 설치합니다(Python 설치 시 설치되지 않은 경우).
   * 필요에 따라 `python --version`과 `pip --version` 명령을 각각 사용하여 Python과 pip을 설치했는지 확인합니다. 

     > [!NOTE]
     > macOS 기본 버전을 사용하는 대신 Python을 수동으로 설치하는 것이 좋습니다.

2. 아래 명령을 실행하여 **virtualenv** 를 설치합니다.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>기타 패키지

Linux에서 아래 오류 메시지를 표시한 후 다음 두 명령을 실행하여 필요한 패키지를 설치합니다.

   :::image type="content" source="./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png" alt-text="Python용 libkrb5 패키지 설치" border="true":::

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

VSCode를 다시 시작한 다음 VSCode 편집기로 돌아가서 **Spark: PySPark Interactive** 명령을 실행합니다.

## <a name="next-steps"></a>다음 단계

### <a name="demo"></a>데모

* HDInsight for VS Code: [비디오](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>도구 및 확장

* [Azure HDInsight Tool for Visual Studio Code 사용](hdinsight-for-vscode.md)
* [IntelliJ용 Azure 도구 키트를 사용하여 Apache Spark Scala 애플리케이션 만들기 및 제출](spark/apache-spark-intellij-tool-plugin.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](spark/apache-spark-jupyter-notebook-install-locally.md)
