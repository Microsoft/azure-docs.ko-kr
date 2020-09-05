---
title: Azure Machine Learning과 Azure Stream Analytics 통합
description: 이 문서에서는 사용자 정의 함수를 사용 하 여 Azure Machine Learning를 통합 하는 간단한 Azure Stream Analytics 작업을 신속 하 게 설정 하는 방법을 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 26a1208131f1d9d3df7dccd8e27bda37992f043f
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236683"
---
# <a name="do-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Azure Stream Analytics 및 Azure Machine Learning Studio를 사용 하 여 감정 분석 (클래식)

이 문서에서는 감정 분석을 위해 Azure Machine Learning Studio (클래식)를 사용 하는 간단한 Azure Stream Analytics 작업을 설정 하는 방법을 보여 줍니다. Cortana Intelligence Gallery에서 Machine Learning 감정 analytics 모델을 사용 하 여 스트리밍 텍스트 데이터를 분석 하 고 감정 점수를 확인 합니다.

> [!TIP]
> 성능 및 안정성 향상을 위해 Azure Machine Learning Studio(클래식) UDF 대신 [Azure Machine Learning UDF](machine-learning-udf.md)를 사용하는 것이 좋습니다.

문서에서 알아본 내용을 다음과 같은 시나리오에 적용할 수 있습니다.

* 스트리밍 Twitter 데이터에 대한 실시간 감정 분석
* 지원 담당자와 고객 채팅 레코드 분석
* 포럼, 블로그 및 동영상에 대한 의견 평가
* 기타 실시간 예측 점수 매기기 시나리오

사용자가 만든 Stream Analytics 작업은 감정 분석 모델을 Blob Storage의 샘플 텍스트에 UDF(사용자 정의 함수)로 적용합니다. 출력(감정 분석의 결과)은 다른 CSV 파일에 있는 동일한 Blob Storage에 기록됩니다. 

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음이 필요합니다.

* 활성화된 Azure 구독.

* 일부 Twitter 데이터가 포함 된 CSV 파일입니다. [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)에서 샘플 파일을 다운로드 하거나 파일을 직접 만들 수 있습니다. 실제 시나리오에서는 Twitter 데이터 스트림에서 직접 데이터를 가져옵니다.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>스토리지 컨테이너 만들기 및 CSV 입력 파일 업로드

이 단계에서는 저장소 컨테이너에 CSV 파일을 업로드 합니다.

1. Azure Portal에서 **리소스 만들기**  >  **저장소**  >  **저장소 계정**을 선택 합니다.

2. 다음 세부 정보를 사용 *하 여 기본 탭을* 작성 하 고 나머지 필드에 대해서는 기본값을 그대로 둡니다.

   |필드  |값  |
   |---------|---------|
   |Subscription|구독을 선택합니다.|
   |Resource group|리소스 그룹을 선택 합니다.|
   |스토리지 계정 이름|스토리지 계정의 이름을 입력합니다. 이름은 Azure에서 고유해야 합니다.|
   |위치|위치를 선택합니다. 모든 리소스는 동일한 위치를 사용 해야 합니다.|
   |계정 종류|BlobStorage|

   ![스토리지 계정 세부 정보 입력](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

3. **검토 + 만들기**를 선택합니다. 그런 다음 **만들기** 를 선택 하 여 저장소 계정을 배포 합니다.

4. 배포가 완료 되 면 저장소 계정으로 이동 합니다. **Blob service** 아래에서 **컨테이너**를 선택합니다. 그런 다음, **+ 컨테이너** 를 선택 하 여 새 컨테이너를 만듭니다.

   ![입력에 대한 Blob Storage 컨테이너 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. 컨테이너의 이름을 지정 하 고 **공용 액세스 수준이** **개인**으로 설정 되어 있는지 확인 합니다. 완료되면 **만들기**를 선택합니다.

   ![Blob 컨테이너 정보 지정](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. 새로 만든 컨테이너로 이동 하 고 **업로드**를 선택 합니다. 이전에 다운로드 한 **sampleinput.csv** 파일을 업로드 합니다.

   ![컨테이너의 '업로드' 단추](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence Gallery의 정서 분석 모델 추가

이제 Blob에 샘플 데이터가 있으므로 Cortana Intelligence Gallery에서 감정 분석 모델을 사용할 수 있습니다.

1. Cortana Intelligence Gallery에서 [예측 감정 분석 모델](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) 페이지로 이동합니다.  

2. **Studio에서 열기 (클래식)를**선택 합니다.  
   
   ![Stream Analytics Machine Learning, Machine Learning 스튜디오 열기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 로그인하여 작업 영역으로 이동합니다. 위치를 선택합니다.

4. 페이지 맨 아래에서 **실행** 을 선택 합니다. 프로세스가 실행되려면 약 1분이 걸립니다.

   ![Machine Learning Studio에서 실험 실행](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. 프로세스를 성공적으로 실행한 후에 페이지의 맨 아래에서 **웹 서비스 배포**를 선택합니다.

   ![Machine Learning Studio에서 실험을 웹 서비스로 배포](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 감정 analytics 모델을 사용할 준비가 되었는지 확인 하려면 **테스트** 단추를 선택 합니다. "I love Microsoft"와 같은 텍스트를 입력합니다.

   ![Machine Learning Studio에서 실험 테스트](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

   테스트가 작동하면 다음 예제와 유사한 결과가 표시됩니다.

   ![Machine Learning Studio의 결과 테스트](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. **앱** 열에서 excel **2010 또는 이전 통합 문서** 링크를 선택 하 여 excel 통합 문서를 다운로드 합니다. 통합 문서는 나중에 Stream Analytics 작업을 설정하는 데 필요한 API 키와 URL을 포함합니다.

    ![Stream Analytics Machine Learning, 간략 상태](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Machine Learning 모델을 사용하는 Stream Analytics 작업 만들기

이제 CSV 파일에서 Blob Storage로의 샘플 트윗을 읽을 수 있는 Stream Analytics 작업을 만들 수 있습니다.

### <a name="create-the-job"></a>작업 만들기

[Azure Portal](https://portal.azure.com) 로 이동 하 여 Stream Analytics 작업을 만듭니다. 이 프로세스에 익숙하지 않은 경우 [Azure Portal를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)를 참조 하세요.

### <a name="configure-the-job-input"></a>작업 입력 구성

작업은 이전에 업로드한 CSV 파일에서 Blob Storage로 해당 입력을 가져옵니다.

1. Stream Analytics 작업으로 이동합니다. **작업 토폴로지**아래에서 **입력** 옵션을 선택 합니다. **스트림 입력**  > **Blob 저장소**추가를 선택 합니다.

2. 다음 값을 사용 하 여 **Blob Storage** 세부 정보를 입력 합니다.

   |필드  |값  |
   |---------|---------|
   |입력 별칭|입력에 이름을 지정 합니다. 쿼리를 작성 하는 경우이 별칭을 염두에 두어야 합니다.|
   |Subscription|구독을 선택합니다.|
   |스토리지 계정|이전 단계에서 만든 저장소 계정을 선택 합니다.|
   |컨테이너|이전 단계에서 만든 컨테이너를 선택 합니다.|
   |이벤트 직렬화 형식|CSV|

3. **저장**을 선택합니다.

### <a name="configure-the-job-output"></a>작업 출력 구성

작업은 입력을 가져오는 동일한 Blob Storage에 결과를 보냅니다.

1. Stream Analytics 작업으로 이동합니다. **작업 토폴로지**아래에서 **출력** 옵션을 선택 합니다. **Add**  >  **Blob 저장소**추가를 선택 합니다.

2. 다음 값으로 **Blob Storage** 폼을 채웁니다.

   |필드  |값  |
   |---------|---------|
   |입력 별칭|입력에 이름을 지정 합니다. 쿼리를 작성 하는 경우이 별칭을 염두에 두어야 합니다.|
   |Subscription|구독을 선택합니다.|
   |스토리지 계정|이전 단계에서 만든 저장소 계정을 선택 합니다.|
   |컨테이너|이전 단계에서 만든 컨테이너를 선택 합니다.|
   |이벤트 직렬화 형식|CSV|

3. **저장**을 선택합니다.

### <a name="add-the-machine-learning-function"></a>Machine Learning 함수 추가

이전에 웹 서비스에 Machine Learning 모델을 게시했습니다. 이 시나리오에서는 Stream Analysis 작업이 실행될 때 감정 분석에 대한 샘플을 입력에서 웹 서비스로 보냅니다. Machine Learning 웹 서비스는 감정(`positive`, `neutral` 또는 `negative`) 및 긍정적인 트윗의 확률을 반환합니다.

이 섹션에서는 Stream Analysis 작업에서 함수를 정의 합니다. 웹 서비스에 트윗을 보내고 응답을 다시 가져오기 위해 함수를 호출할 수 있습니다.

1. Excel 통합 문서의 앞부분에서 다운로드한 웹 서비스 URL 및 API 키가 있는지 확인합니다.

2. Stream Analytics 작업으로 이동합니다. 그런 다음 **함수**  >  **+**  >  **Azure 추가 ML Studio** 를 선택 합니다.

3. 다음 값을 사용 하 여 **Azure Machine Learning 함수** 폼을 채웁니다.

   |필드  |값  |
   |---------|---------|
   | 함수 별칭 | 이름을 사용 `sentiment` 하 여 URL 및 키를 입력할 수 있는 옵션을 제공 하는 **함수 설정 Azure Machine Learning 수동으로 제공**을 선택 합니다.      |
   | URL| 웹 서비스 URL을 붙여넣습니다.|
   |Key | API 키를 붙여넣습니다. |

4. **저장**을 선택합니다.

### <a name="create-a-query-to-transform-the-data"></a>데이터를 변환하는 쿼리 만들기

Stream Analytics는 선언적인 SQL 기반 쿼리를 사용하여 입력을 검사하고 처리합니다. 이 섹션에서는 입력의 트윗을 읽고 Machine Learning 함수를 호출하여 감정 분석을 수행하는 쿼리를 만듭니다. 그런 다음 쿼리는 정의한 출력으로 결과를 보냅니다(Blob Storage).

1. Stream Analytics 작업 개요로 돌아갑니다.

2. **작업 토폴로지**에서 **쿼리**를 선택합니다.

3. 다음 쿼리를 입력합니다.

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM <input>  
    )  

    SELECT text, result.[Score]  
    INTO <output>
    FROM sentiment  
    ```    

   이 쿼리는 앞에서 만든 함수 ()를 호출 `sentiment` 하 여 입력의 각 트 윗에 대 한 감정 분석을 수행 합니다.

4. **저장** 을 선택 하 여 쿼리를 저장 합니다.

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics 작업을 시작하고 출력을 확인합니다.

이제 Stream Analytics 작업을 시작할 수 있습니다.

### <a name="start-the-job"></a>작업 시작

1. Stream Analytics 작업 개요로 돌아갑니다.

2. 페이지 위쪽에서 **시작** 을 선택 합니다.

3. **작업 시작**에서 **사용자 지정**을 선택 하 고 blob 저장소에 CSV 파일을 업로드 한 날짜 이전을 선택 합니다. 완료되면 **시작**을 선택합니다.  

### <a name="check-the-output"></a>출력 파일 확인

1. **모니터링** 상자에서 작업을 확인할 때까지 몇 분 동안 작업을 실행하겠습니다.

2. 일반적으로 blob 저장소의 콘텐츠를 검사 하는 데 사용 하는 도구를 사용 하는 경우 해당 도구를 사용 하 여 컨테이너를 검사 합니다. 또는 Azure Portal에서 다음 단계를 수행합니다.

      1. Azure Portal에서 저장소 계정을 찾고 계정 내에서 컨테이너를 찾습니다. 컨테이너에 두 개의 파일이 표시됩니다. 하나는 샘플 트윗을 포함하는 파일이고 다른 하나는 Stream Analytics 작업에 의해 생성된 CSV 파일입니다.
      2. 생성된 파일을 마우스 오른쪽 단추로 클릭한 다음 **다운로드**를 선택합니다.

3. 생성된 CSV 파일을 엽니다. 다음 예제와 유사한 출력이 표시됩니다.  

   ![Stream Analytics Machine Learning, CSV 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

### <a name="view-metrics"></a>메트릭 보기

또한 Azure Machine Learning 함수 관련 메트릭도 확인할 수 있습니다. 다음 함수 관련 메트릭은 작업 개요의 **모니터링** 상자에 표시 됩니다.

* **함수 요청** 은 Machine Learning 웹 서비스로 전송되는 요청 수를 나타냅니다.  
* **함수 이벤트** 는 요청의 이벤트 수를 나타냅니다. 기본적으로 Machine Learning 웹 서비스에 대한 각 요청에는 최대 1,000개의 이벤트가 포함됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API 및 Machine Learning 통합](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
