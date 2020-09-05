---
title: '자습서: Synapse Studio에서 Apache Spark 작업 정의 만들기'
description: 자습서 - Azure Synapse Analytics를 사용하여 Spark 작업 정의를 만들어서 Azure Synapse Analytics용 Apache Spark 풀에 제출합니다.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 38678c795b0ce7534de0ca8602c1198bc35f0e05
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206193"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>자습서: Synapse Studio에서 Apache Spark 작업 정의 만들기

이 자습서에서는 Azure Synapse Studio를 사용하여 Apache Spark 작업 정의를 만든 다음, Apache Spark 풀에 제출하는 방법을 보여 줍니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

* PySpark(Python)에 대한 Apache Spark 작업 정의 만들기
* Spark(Scala)에 대한 Apache Spark 작업 정의 만들기
* .NET Spark(C#/F#)에 대한 Apache Spark 작업 정의 만들기
* 일괄 작업으로 Apache Spark 작업 정의 제출
* 파이프라인에 Apache Spark 작업 정의 추가

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에 다음 요구 사항이 충족되는지 확인합니다.

* Azure Synapse Analytics 작업 영역. 지침은 [Azure Synapse Analytics 작업 영역 만들기](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)를 참조하세요.
* Apache Spark 풀
* ADLS Gen2 스토리지 계정 작업하려는 ADLS Gen2 파일 시스템의 Storage Blob 데이터 소유자여야 합니다. 그렇지 않으면, 권한을 수동으로 추가해야 합니다.

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>PySpark(Python)에 대한 Apache Spark 작업 정의 만들기

이 섹션에서는 PySpark(Python)에 대한 Apache Spark 작업 정의를 만듭니다.

1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.

2. [Apache Spark 작업 정의 만들기를 위한 샘플 파일](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)로 이동하면 **wordcount.py** 및 **shakespear.txt**를 다운로드할 수 있습니다. 그런 다음, 이러한 파일을 Azure Storage에 업로드합니다. **데이터**를 클릭하고 **스토리지 계정**을 선택하여 ADLS Gen2 파일 시스템에 관련 파일을 업로드합니다. 파일이 이미 Azure 스토리지에 있는 경우 이 단계를 건너뜁니다. 

     ![Python 파일 업로드](./media/apache-spark-job-definitions/upload-python-file.png)

3. **개발** 허브를 클릭하고 왼쪽 창에서 **Spark 작업 정의**를 선택한 후, **Spark 작업 정의** 옆에 있는 ‘...’ 작업 노드를 클릭를 클릭한 다음, 바로 가기 메뉴에서 **새 Spark 작업 정의**를 선택합니다.

     ![Python에 대한 새 정의 만들기](./media/apache-spark-job-definitions/create-new-definition.png)

4. Apache Spark 작업 정의 주 창의 언어 드롭다운 목록에서 **PySpark(Python)** 를 선택합니다.

5. Apache Spark 작업 정의에 대한 정보를 입력합니다. 샘플 정보를 복사할 수 있습니다.

     |  속성   | Description   |  
     | ----- | ----- |  
     |작업 정의 이름| Apache Spark 작업 정의의 이름을 입력합니다. 이 이름은 게시되기 전까지 언제든지 업데이트할 수 있습니다. 예제: `job definition sample`|
     |주 정의 파일| 작업에 사용되는 주 파일입니다. 스토리지에서 PY 파일을 선택합니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다. 예제: `abfss://…/path/to/wordcount.py`|
     |명령줄 인수| 작업에 대한 선택적 인수입니다. 예제: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |참조 파일| 주 정의 파일에서 참조용으로 사용되는 추가 파일입니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다. |
     |Spark 풀| 선택한 Apache Spark 풀에 작업이 제출됩니다.|
     |Spark 버전| Apache Spark 풀에서 실행 중인 Apache Spark 버전입니다.|
     |실행자| 작업에 대해 지정된 Apache Spark 풀에 제공할 실행기의 수입니다.|
     |실행기 크기| 작업에 대해 지정된 Apache Spark 풀에 제공된 실행기에 사용할 코어 및 메모리의 수입니다.|  
     |드라이버 크기| 작업에 대해 지정된 Apache Spark 풀에 제공된 드라이버에 사용할 코어 및 메모리의 수입니다.|

     ![Python에 대한 Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/create-py-definition.png)

6. **게시**를 선택하여 Apache Spark 작업 정의를 저장합니다.

     ![py 정의 게시](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Apache Spark(Scala)에 대한 Apache Spark 작업 정의 만들기

이 섹션에서는 Apache Spark(Scala)에 대한 Apache Spark 작업 정의를 만듭니다.

 1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.

 2. [Apache Spark 작업 정의 만들기를 위한 샘플 파일](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)로 이동하면 **wordcount.jar** 및 **shakespear.txt**를 다운로드할 수 있습니다. 그런 다음, 이러한 파일을 Azure Storage에 업로드합니다. **데이터**를 클릭하고 **스토리지 계정**을 선택하여 ADLS Gen2 파일 시스템에 관련 파일을 업로드합니다. 파일이 이미 Azure 스토리지에 있는 경우 이 단계를 건너뜁니다. 
 
     ![scala 구조체 준비](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. **개발** 허브를 클릭하고 왼쪽 창에서 **Spark 작업 정의**를 선택한 후, **Spark 작업 정의** 옆에 있는 ‘...’ 작업 노드를 클릭를 클릭한 다음, 바로 가기 메뉴에서 **새 Spark 작업 정의**를 선택합니다.
     ![Scala에 대한 새 정의 만들기](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Apache Spark 작업 정의 주 창의 언어 드롭다운 목록에서 **Spark(Scala)** 를 선택합니다.

 5. Apache Spark 작업 정의에 대한 정보를 입력합니다. 샘플 정보를 복사할 수 있습니다.

     |  속성   | Description   |  
     | ----- | ----- |  
     |작업 정의 이름| Apache Spark 작업 정의의 이름을 입력합니다. 이 이름은 게시되기 전까지 언제든지 업데이트할 수 있습니다. 예제: `job definition sample`|
     |주 정의 파일| 작업에 사용되는 주 파일입니다. 스토리지에서 JAR 파일을 선택합니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다. 예제: `abfss://…/path/to/wordcount.jar`|
     |주 클래스 이름| 주 정의 파일에 있는 주 클래스 또는 정규화된 식별자입니다. 예제: `WordCount`|
     |명령줄 인수| 작업에 대한 선택적 인수입니다. 예제: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |참조 파일| 주 정의 파일에서 참조용으로 사용되는 추가 파일입니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
     |Spark 풀| 선택한 Apache Spark 풀에 작업이 제출됩니다.|
     |Spark 버전| Apache Spark 풀에서 실행 중인 Apache Spark 버전입니다.|
     |실행자| 작업에 대해 지정된 Apache Spark 풀에 제공할 실행기의 수입니다.|  
     |실행기 크기| 작업에 대해 지정된 Apache Spark 풀에 제공된 실행기에 사용할 코어 및 메모리의 수입니다.|
     |드라이버 크기| 작업에 대해 지정된 Apache Spark 풀에 제공된 드라이버에 사용할 코어 및 메모리의 수입니다.|

     ![Scala에 대한 Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. **게시**를 선택하여 Apache Spark 작업 정의를 저장합니다.

     ![scala 정의 게시](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>.NET Spark(C#/F#)에 대한 Apache Spark 작업 정의 만들기

이 섹션에서는 .NET Spark(C#/F#)에 대한 Apache Spark 작업 정의를 만듭니다.
 1. [Azure Synapse Studio](https://web.azuresynapse.net/)를 엽니다.

 2. [Apache Spark 작업 정의 만들기를 위한 샘플 파일](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)로 이동하면 **wordcount.zip** 및 **shakespear.txt**를 다운로드할 수 있습니다. 그런 다음, 이러한 파일을 Azure Storage에 업로드합니다. **데이터**를 클릭하고 **스토리지 계정**을 선택하여 ADLS Gen2 파일 시스템에 관련 파일을 업로드합니다. 파일이 이미 Azure 스토리지에 있는 경우 이 단계를 건너뜁니다. 

     ![dotnet 구조체 준비](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. **개발** 허브를 클릭하고 왼쪽 창에서 **Spark 작업 정의**를 선택한 후, **Spark 작업 정의** 옆에 있는 ‘...’ 작업 노드를 클릭를 클릭한 다음, 바로 가기 메뉴에서 **새 Spark 작업 정의**를 선택합니다.

     ![dotnet에 대한 새 정의 만들기](./media/apache-spark-job-definitions/create-new-definition.png)

 4. Apache Spark 작업 정의 주 창의 언어 드롭다운 목록에서 **.NET Spark(C#/F#)** 를 선택합니다.

 5. Apache Spark 작업 정의에 대한 정보를 입력합니다. 샘플 정보를 복사할 수 있습니다.
    
     |  속성   | Description   |  
     | ----- | ----- |  
     |작업 정의 이름| Apache Spark 작업 정의의 이름을 입력합니다. 이 이름은 게시되기 전까지 언제든지 업데이트할 수 있습니다. 예제: `job definition sample`|
     |주 정의 파일| 작업에 사용되는 주 파일입니다. .NET for Apache Spark 애플리케이션(즉, 주 실행 파일, 사용자 정의 함수를 포함하는 DLL 및 기타 필수 파일)이 포함된 ZIP 파일을 스토리지에서 선택합니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다. 예제: `abfss://…/path/to/wordcount.zip`|
     |주 실행 파일| 주 정의 ZIP 파일의 주 실행 파일입니다. 예제: `WordCount`|
     |명령줄 인수| 작업에 대한 선택적 인수입니다. 예제: `abfss://…/path/to/shakespeare.txt abfss://…/path/to/result`|
     |참조 파일| 주 정의 ZIP 파일에 포함되지 않은 .NET for Apache Spark 애플리케이션을 실행하기 위해 작업자 노드에 필요한 추가 파일(즉, 종속 jar, 추가 사용자 정의 함수 DLL 및 기타 구성 파일)입니다. **파일 업로드**를 선택하여 스토리지 계정에 파일을 업로드할 수 있습니다.|
     |Spark 풀| 선택한 Apache Spark 풀에 작업이 제출됩니다.|
     |Spark 버전| Apache Spark 풀에서 실행 중인 Apache Spark 버전입니다.|
     |실행자| 작업에 대해 지정된 Apache Spark 풀에 제공할 실행기의 수입니다.|  
     |실행기 크기| 작업에 대해 지정된 Apache Spark 풀에 제공된 실행기에 사용할 코어 및 메모리의 수입니다.|
     |드라이버 크기| 작업에 대해 지정된 Apache Spark 풀에 제공된 드라이버에 사용할 코어 및 메모리의 수입니다.|

     ![dotnet에 대한 Spark 작업 정의의 값 설정](./media/apache-spark-job-definitions/create-net-definition.png)

 6. **게시**를 선택하여 Apache Spark 작업 정의를 저장합니다.

      ![dotnet 정의 게시](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>일괄 작업으로 Apache Spark 작업 정의 제출

Apache Spark 작업 정의를 만든 후에 Apache Spark 풀에 제출할 수 있습니다. 작업하려는 ADLS Gen2 파일 시스템의 Storage Blob 데이터 소유자여야 합니다. 그렇지 않으면, 권한을 수동으로 추가해야 합니다.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>시나리오 1: Apache Spark 작업 정의 제출
 1. Apache Spark 작업 정의 창을 클릭하여 엽니다.

      ![제출할 Spark 작업 정의 열기 ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. **제출** 아이콘을 클릭하여 선택한 Apache Spark 풀에 프로젝트를 제출합니다. **Spark 모니터링 URL** 탭을 클릭하여 Apache Spark 애플리케이션의 LogQuery를 볼 수 있습니다.

    ![제출 단추를 클릭하여 Spark 작업 정의를 제출합니다.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Spark 제출 대화 상자](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>시나리오 2: Apache Spark 작업 실행 진행률 보기

 1. **모니터**를 클릭한 다음, **Spark 애플리케이션** 옵션을 선택합니다. 제출된 Apache Spark 애플리케이션을 찾을 수 있습니다.

     ![Spark 애플리케이션 보기](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 그런 다음, Apache Spark 애플리케이션을 클릭하면 **LogQuery** 창이 표시됩니다. **LogQuery**에서 작업 실행 진행률을 볼 수 있습니다.
     
     ![Spark 애플리케이션 LogQuery 보기](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>시나리오 3: 출력 파일 확인

 1. **데이터**를 클릭한 후 **스토리지 계정**을 선택합니다. 실행에 성공한 후, ADLS Gen2 스토리지로 이동하여 출력이 생성되었는지 확인할 수 있습니다.

     ![출력 파일 보기](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>파이프라인에 Apache Spark 작업 정의 추가

이 섹션에서는 파이프라인에 Apache Spark 작업 정의를 추가합니다.

 1. 기존의 Apache Spark 작업 정의를 엽니다.

 2. Apache Spark 작업 정의의 오른쪽 위에 있는 아이콘을 클릭하거나, **기존 파이프라인**을 선택하거나, **새 파이프라인**을 선택합니다. 자세한 내용은 파이프라인 페이지를 참조하세요.

     ![파이프라인에 추가](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![파이프라인에 추가](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Synapse Studio를 사용하여 Apache Spark 작업 정의를 만든 다음, Apache Spark 풀에 제출하는 방법을 보여 줍니다. 이제 Azure Synapse Studio를 사용하여 Power BI 데이터 세트를 만들고 Power BI 데이터를 관리할 수 있습니다.

