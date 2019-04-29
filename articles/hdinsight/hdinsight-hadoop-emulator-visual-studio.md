---
title: Visual Studio용 Data Lake 도구 및 Hortonworks 샌드박스 - Azure HDInsight
description: Hortonworks Sandbox(로컬 VM에서 실행됨)와 Azure Data Lake tools for Visual Studio를 사용하는 방법을 알아봅니다. 이러한 도구로 샌드박스에 대한 Hive 및 Pig 작업을 만들고 실행하며 작업 출력 및 기록을 볼 수 있습니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 8172da3f573f78365cbe76f435a4817e64bc136c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762754"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Hortonworks Sandbox와 Azure Data Lake tools for Visual Studio 사용

Azure Data Lake는 제네릭 Apache Hadoop 클러스터를 사용하기 위한 도구를 포함합니다. 이 문서에서는 로컬 가상 머신에서 실행되는 Hortonworks Sandbox와 Data Lake 도구를 사용하는 데 필요한 단계를 제공합니다.

Hortonworks Sandbox를 사용하여 개발 환경에서 로컬로 Hadoop를 사용할 수 있습니다. 솔루션을 개발하여 대규모로 배포했으므로 HDInsight 클러스터를 살펴볼 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 개발 환경의 가상 머신에서 실행되는 Hortonworks Sandbox입니다. 이 문서는 Oracle VirtualBox에서 실행 중인 샌드박스로 작성 및 테스트되었으며, 샌드박스 설정에 대한 자세한 내용은 [Hortonworks 샌드박스 시작](hadoop/apache-hadoop-emulator-get-started.md) 문서를 참조하세요.

* Visual Studio 2013, Visual Studio 2015 또는 Visual Studio 2017(모든 에디션)

* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.7.1 이상

* [Azure Data Lake tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>샌드박스의 암호 구성

Hortonworks Sandbox가 실행 중인지 확인합니다. [Hortonworks 샌드박스에서 시작](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) 문서에 나온 단계에 따릅니다. 이러한 단계에서 SSH `root` 계정 및 Apache Ambari `admin` 계정의 암호를 구성합니다. Visual Studio에서 샌드박스에 연결할 때 이러한 암호를 사용합니다.

## <a name="connect-the-tools-to-the-sandbox"></a>샌드박스에 도구 연결

1. Visual Studio를 열고 **보기** 및 **서버 탐색기**를 차례로 선택합니다.

2. **서버 탐색기**에서 **HDInsight** 항목을 마우스 오른쪽 단추로 클릭한 다음 **HDInsight Emulator에 연결**을 선택합니다.

    ![HDInsight Emulator에 연결이 강조 표시된 서버 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. **HDInsight Emulator에 연결** 대화 상자에서 Ambari에 대해 구성한 암호를 입력합니다.

    ![암호 텍스트 상자가 강조 표시된 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    **다음**을 선택하여 계속합니다.

4. **암호** 필드를 사용하여 `root` 계정에 대해 구성한 암호를 입력합니다. 다른 필드는 기본값으로 둡니다.

    ![암호 텍스트 상자가 강조 표시된 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    **다음**을 선택하여 계속합니다.

5. 서비스의 유효성 검사가 완료되기를 기다립니다. 경우에 따라 유효성 검사가 실패하고 구성을 업데이트하라는 메시지가 표시될 수 있습니다. 유효성 검사에 실패할 경우 **업데이트**를 선택하고 서비스의 구성 및 확인이 완료되기를 기다립니다.

    ![업데이트 단추가 강조 표시된 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > 업데이트 프로세스는 Ambari를 사용하여 Visual Studio용 Data Lake 도구에서 예상한 Hortonworks Sandbox 구성을 수정합니다.

6. 유효성 검사가 완료되면 **마침**을 선택하여 구성을 완료합니다.
    ![마침 단추가 강조 표시된 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > 개발 환경의 속도 및 가상 머신에 할당된 메모리 양에 따라 서비스를 구성하고 유효성을 검사하는 데 몇 분 정도 걸릴 수 있습니다.

이러한 단계를 따르면 **HDInsight** 섹션의 서버 탐색기에서 **HDInsight 로컬 클러스터** 항목이 생성됩니다.

## <a name="write-an-apache-hive-query"></a>Apache Hive 쿼리 작성

Hive에서는 구조화된 데이터로 작업하기 위한 SQL과 유사한 쿼리 언어(HiveQL)를 제공합니다. 다음 단계를 사용하여 로컬 클러스터에 대해 주문형 쿼리를 실행하는 방법을 알아봅니다.

1. **서버 탐색기**에서 이전에 추가한 로컬 클러스터에 대한 항목을 마우스 오른쪽 단추로 클릭한 다음 **Hive 쿼리 작성**을 선택합니다.

    ![Hive 쿼리 작성이 강조 표시된 서버 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    새 쿼리 창이 표시됩니다. 쿼리를 신속하게 쓰고 로컬 클러스터에 제출할 수 있습니다.

2. 새 쿼리 창에서 다음 명령을 입력합니다.

        select count(*) from sample_08;

    쿼리를 실행하려면 창의 위쪽에 있는 **제출**을 선택합니다. 다른 값(**Batch** 및 서버 이름)을 기본값으로 그대로 둡니다.

    ![제출 단추가 강조 표시된 쿼리 창의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    **제출** 옆의 드롭다운 메뉴를 사용하여 **고급**을 선택할 수도 있습니다. 고급 옵션을 사용하여 작업을 제출할 때 추가 옵션을 제공할 수 있습니다.

    ![스크립트 제출 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. 쿼리를 제출하면 작업 상태가 표시됩니다. 작업 상태에는 Hadoop에서 처리되는 작업에 대한 정보가 표시됩니다. **작업 상태**는 작업의 상태를 제공합니다. 상태는 정기적으로 업데이트됩니다. 또는 새로 고침 아이콘을 사용하여 상태를 수동으로 새로 고칠 수 있습니다.

    ![작업 상태가 강조 표시된 작업 보기 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    **작업 상태**가 **완료됨**으로 변경되면 DAG(방향성 비순환 그래프)가 표시됩니다. 이 다이어그램은 Hive 쿼리 처리 시 Tez에 의해 결정된 실행 경로를 설명합니다. Tez는 로컬 클러스터 기반 Hive의 기본 실행 엔진입니다.

    > [!NOTE]  
    > 또한 Linux 기반 HDInsight 클러스터를 사용하는 경우 Apache Tez는 기본값입니다. Windows 기반 HDInsight에서 기본값이 아닙니다. 사용하려면 Hive 쿼리의 시작 부분에 `set hive.execution.engine = tez;` 라인을 추가해야 합니다.

    출력을 보려면 **작업 출력** 링크를 사용합니다. 이 경우 823이며, 이는 sample_08 테이블의 행 수입니다. **작업 로그** 및 **YARN 로그 다운로드** 링크를 사용하여 작업에 대한 진단 정보를 볼 수 있습니다.

4. **Batch** 필드를 **대화형**으로 변경하여 Hive 작업을 대화형으로 실행할 수도 있습니다. **실행**을 선택합니다.

    ![대화형 및 실행 단추가 강조 표시된 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    대화형 쿼리는 처리 중에 생성된 출력 로그를 **HiveServer2 출력** 창으로 스트리밍합니다.

    > [!NOTE]  
    > 이 정보는 작업 완료 후 **작업 로그** 링크에서 사용할 수 있는 것과 동일한 정보입니다.

    ![출력 로그의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Hive 프로젝트 만들기

또한 여러 Hive 스크립트를 포함하는 프로젝트를 만들 수 있습니다. 스크립트와 관련되거나 스크립트를 버전 제어 시스템에 저장하려는 경우 프로젝트를 사용합니다.

1. Visual Studio에서 **파일**, **새로 만들기** 및 **프로젝트**를 차례로 선택합니다.

2. 프로젝트 목록에서 **템플릿**, **Azure Data Lake**를 차례로 확장한 다음 **HIVE(HDInsight)** 를 선택합니다. 템플릿 목록에서 **Hive 샘플**을 선택합니다. 이름과 위치를 입력한 다음 **확인**을 선택합니다.

    ![Azure Data Lake, HIVE, Hive 예제 및 확인이 강조 표시된 새 프로젝트 창의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive 샘플** 프로젝트에는 **WebLogAnalysis.hql** 및 **SensorDataAnalysis.hql**이라는 두 개의 스크립트가 포함되어 있습니다. 창 위쪽의 동일한 **제출** 단추를 사용하여 이러한 스크립트를 제출할 수 있습니다.

## <a name="create-an-apache-pig-project"></a>Apache Pig 프로젝트 만들기

Hive에서는 구조화된 데이터로 작업하기 위한 SQL과 유사한 쿼리 언어(HiveQL)를 제공하지만 Pig는 데이터에 대한 변환을 수행하는 방식으로 작동합니다. Pig는 변환의 파이프라인을 개발할 수 있는 언어(Pig Latin)를 제공합니다. 로컬 클러스터에서 Pig를 사용하려면 다음 단계를 따르세요.

1. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 차례로 선택합니다. 프로젝트 목록에서 **템플릿**, **Azure Data Lake**를 차례로 확장한 다음 **Pig(HDInsight)** 를 선택합니다. 템플릿 목록에서 **Pig 애플리케이션**을 선택합니다. 이름과 위치를 입력한 다음 **확인**을 선택합니다.

    ![Azure Data Lake, Pig, Pig 애플리케이션 및 확인이 강조 표시된 새 프로젝트 창의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. 이 프로젝트로 만든 **script.pig** 파일의 콘텐츠로 다음 텍스트를 입력합니다.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Pig에서 Hive와 다른 언어를 사용하는 동안 **제출** 단추를 통해 두 언어 간에 작업을 실행하는 방법은 일치합니다. **제출** 옆에 있는 드롭다운을 선택하면 Pig의 고급 제출 대화 상자가 표시됩니다.

    ![스크립트 제출 대화 상자의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. 또한 작업 상태 및 출력은 Hive 쿼리와 동일하게 표시됩니다.

    ![완료된 Pig 작업의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>작업 보기

또한 Data Lake 도구를 사용하면 Hadoop에서 실행된 작업에 대한 정보를 쉽게 볼 수 있습니다. 다음 단계를 사용하여 로컬 클러스터에서 실행된 작업을 확인합니다.

1. **서버 탐색기**에서 로컬 클러스터를 마우스 오른쪽 단추로 클릭한 다음 **작업 보기**를 선택합니다. 클러스터에 제출된 작업 목록이 표시됩니다.

    ![작업 보기가 강조 표시된 서버 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. 작업 목록에서 작업 세부 정보를 볼 작업을 선택합니다.

    ![작업 중 하나가 강조 표시된 작업 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    표시되는 정보는 출력 및 로그 정보를 보는 링크를 포함하여 Hive 또는 Pig 쿼리를 실행한 후에 표시되는 정보와 비슷합니다.

3. 또한 여기에서 작업을 수정하고 다시 제출할 수 있습니다.

## <a name="view-hive-databases"></a>Hive 데이터베이스 보기

1. **서버 탐색기**에서 **HDInsight 로컬 클러스터** 항목, **Hive 데이터베이스**를 차례로 확장합니다. 로컬 클러스터에 있는 **Default** 및 **xademo** 데이터베이스가 표시됩니다. 데이터베이스 확장하면 데이터베이스 내의 테이블이 표시됩니다.

    ![데이터베이스가 확장된 서버 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. 테이블을 확장하면 해당 테이블에 대한 열을 표시합니다. 데이터를 신속하게 보려면 테이블을 마우스 오른쪽 단추로 클릭하고 **상위 100개 행 보기**를 선택합니다.

    ![테이블이 확장되고 상위 100개 행 보기가 선택된 서버 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>데이터베이스 및 테이블 속성

데이터베이스 또는 테이블의 속성을 볼 수 있습니다. **속성**을 선택하면 [속성] 창에서 선택한 항목에 대한 세부 정보가 표시됩니다. 예를 들어, 다음 스크린샷에 표시된 정보를 참조하세요.

![속성 창의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>테이블 만들기

테이블을 만들려면 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **테이블 만들기**를 선택합니다.

![테이블 만들기가 강조 표시된 서버 탐색기의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

그런 다음 형식을 사용하여 테이블을 만들 수 있습니다. 다음 스크린샷 맨 아래에서 테이블을 만드는 데 사용할 원시 HiveQL를 볼 수 있습니다.

![테이블을 생성하기 위해 사용한 양식의 스크린샷](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>다음 단계

* [Hortonworks Sandbox의 방법 알아보기](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop 자습서 - HDP 시작](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
