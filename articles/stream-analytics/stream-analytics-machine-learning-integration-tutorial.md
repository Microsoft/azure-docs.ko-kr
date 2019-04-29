---
title: Azure Machine Learning과 Azure Stream Analytics 통합
description: 이 아티클에서는 사용자 정의 함수를 사용하여 Azure Machine Learning을 통합하는 간단한 Azure Stream Analytics 작업을 신속하게 설정하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b06fec8ab726f48e1937bae4cfbdbd9842788d0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61480811"
---
# <a name="performing-sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Azure Stream Analytics 및 Azure Machine Learning을 사용한 감정 분석 수행
이 문서에서는 Azure Machine Learning을 통합하는 간단한 Azure Stream Analytics 작업을 신속하게 설정하는 방법을 설명합니다. Cortana Intelligence 갤러리의 Machine Learning 감정 분석 모델을 사용하여 실시간으로 스트리밍 텍스트 데이터를 분석하고 감정 점수를 확인합니다. Cortana Intelligence Suite를 사용하면 감정 분석 모델 빌드에 대한 걱정없이 이 작업을 수행할 수 있습니다.

문서에서 알아본 내용을 다음과 같은 시나리오에 적용할 수 있습니다.

* 스트리밍 Twitter 데이터에 대한 실시간 감정 분석
* 지원 담당자와 고객 채팅 레코드 분석
* 포럼, 블로그 및 동영상에 대한 의견 평가 
* 기타 실시간 예측 점수 매기기 시나리오

실제 시나리오에서는 Twitter 데이터 스트림에서 직접 데이터를 가져옵니다. 자습서를 간소화하기 Stream Analytics 작업이 Azure Blob Storage에서 CSV 파일의 트윗을 가져오도록 작성했습니다. 고유한 CSV 파일을 만들거나 다음 이미지에 나와 있는 것처럼 샘플 CSV 파일을 사용할 수 있습니다.

![CSV 파일에 표시된 샘플 트윗](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

사용자가 만든 Stream Analytics 작업은 감정 분석 모델을 Blob Storage의 샘플 텍스트에 UDF(사용자 정의 함수)로 적용합니다. 출력(감정 분석의 결과)은 다른 CSV 파일에 있는 동일한 Blob Storage에 기록됩니다. 

다음 그림은 이러한 구성을 보여 줍니다. 언급한 대로 좀 더 현실적인 시나리오에서는 Blob Storage를 Azure Event Hubs 입력의 스트리밍 Twitter 데이터로 바꿀 수 있습니다. 또한 종합 정서의 [Microsoft Power BI](https://powerbi.microsoft.com/) 실시간 시각화를 빌드할 수도 있습니다.    

![Stream Analytics Machine Learning 통합 개요](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>필수 조건
시작하기 전에 다음이 필요합니다.

* 활성 Azure 구독.
* 데이터가 포함된 CSV 파일. [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv)에서 이전에 표시된 파일을 다운로드하거나 고유한 파일을 직접 만들 수 있습니다. 이 문서의 경우 GitHub의 파일을 사용한다고 가정합니다.

좀 더 높은 수준에서 이 문서에 설명된 작업을 완료하려면 다음을 수행합니다.

1. Azure Storage 계정 및 Blob Storage 컨테이너를 만들고 컨테이너에 CSV로 포맷된 입력 파일을 업로드합니다.
3. Cortana Intelligence 갤러리의 감정 분석 모델을 Azure Machine Learning 작업 영역에 추가하고 이 모델을 Azure Machine Learning 작업 영역의 웹 서비스로 배포합니다.
5. 텍스트 입력의 감정을 결정하기 위해 이 웹 서비스를 함수로 호출하는 Stream Analytics 작업을 만듭니다.
6. Stream Analytics 작업을 시작하고 출력을 확인합니다.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>저장소 컨테이너 만들기 및 CSV 입력 파일 업로드
이 단계의 경우 GitHub에서 사용할 수 있는 파일과 같은 모든 CSV 파일을 사용할 수 있습니다.

1. Azure Portal에서 **리소스 만들기** > **저장소** > **저장소 계정**을 클릭합니다.

2. 이름을 입력합니다(`samldemo` 예제에서). 이름으로 소문자 및 숫자만 사용할 수 있으며 Azure 전체에서 고유해야 합니다. 

3. 기존 리소스 그룹을 지정하고 위치를 지정합니다. 위치의 경우 이 자습서에서 만든 모든 리소스가 동일한 위치를 사용하는 것이 좋습니다.

    ![저장소 계정 세부 정보 입력](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Azure Portal에서 저장소 계정을 선택합니다. 스토리지 계정 블레이드에서 **컨테이너**를 클릭하고 **+&nbsp;컨테이너**를 클릭하여 Blob Storage를 만듭니다.

    ![입력에 대한 Blob Storage 컨테이너 만들기](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. 컨테이너에 이름(이 예에서 `azuresamldemoblob`)을 입력하고 **액세스 형식**이 **Blob**으로 설정되었는지 확인합니다. 완료되면 **확인**을 클릭합니다.

    ![Blob 컨테이너 정보 지정](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. **컨테이너** 블레이드에서 새 컨테이너를 선택합니다. 그러면 해당 컨테이너의 블레이드가 열립니다.

7. **업로드**를 클릭합니다.

    ![컨테이너의 '업로드' 단추](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. **Blob 업로드** 블레이드에서 이전에 다운로드한 **sampleinput.csv** 파일을 업로드합니다. **Blob 형식**에서 **블록 Blob**을 선택하고 블록 크기를 4MB로 설정합니다. 그러면 이 자습서에 충분합니다.

9. 블레이드 맨 아래에 있는 **업로드** 단추를 클릭합니다.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Cortana Intelligence Gallery의 정서 분석 모델 추가

이제 Blob에 샘플 데이터가 있으므로 Cortana Intelligence Gallery에서 감정 분석 모델을 사용할 수 있습니다.

1. Cortana Intelligence Gallery에서 [예측 감정 분석 모델](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) 페이지로 이동합니다.  

2. **Studio에서 열기**를 클릭합니다.  
   
   ![Stream Analytics Machine Learning, Machine Learning 스튜디오 열기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. 로그인하여 작업 영역으로 이동합니다. 위치를 선택합니다.

4. 페이지 아래쪽에서 **실행** 을 클릭합니다. 프로세스가 실행되려면 약 1분이 걸립니다.

   ![Machine Learning Studio에서 실험 실행](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. 프로세스를 성공적으로 실행한 후에 페이지의 맨 아래에서 **웹 서비스 배포**를 선택합니다.

   ![Machine Learning Studio에서 실험을 웹 서비스로 배포](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. 감성 분석 모델을 사용할 수 있는지 유효성을 검사하려면 **테스트** 단추를 클릭합니다. "I love Microsoft"와 같은 텍스트를 입력합니다. 

   ![Machine Learning Studio에서 실험 테스트](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    테스트가 작동하면 다음 예제와 유사한 결과가 표시됩니다.

   ![Machine Learning Studio의 결과 테스트](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. **앱** 열에서 **Excel 2010 또는 이전 통합 문서** 링크를 클릭하여 Excel 통합 문서를 다운로드합니다. 통합 문서는 나중에 Stream Analytics 작업을 설정하는 데 필요한 API 키와 URL을 포함합니다.

    ![Stream Analytics Machine Learning, 간략 상태](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Machine Learning 모델을 사용하는 Stream Analytics 작업 만들기

이제 CSV 파일에서 Blob Storage로의 샘플 트윗을 읽을 수 있는 Stream Analytics 작업을 만들 수 있습니다. 

### <a name="create-the-job"></a>작업 만들기

1. [Azure 포털](https://portal.azure.com)로 이동합니다.  

2. **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 클릭합니다. 

3. `azure-sa-ml-demo`의 작업 이름을 지정하고, 구독을 지정하고, 기존 리소스 그룹을 지정하거나 새 리소스 그룹을 만들고, 작업의 위치를 선택합니다.

   ![새 Stream Analytics 작업의 설정 지정](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>작업 입력 구성
작업은 이전에 업로드한 CSV 파일에서 Blob Storage로 해당 입력을 가져옵니다.

1. 작업이 만들어진 후에 작업 블레이드의 **작업 토폴로지** 아래에서 **입력** 옵션을 클릭합니다.    

2. **입력** 블레이드에서 **스트림 입력 추가** >**Blob Storage**를 클릭합니다.

3. 다음과 같은 값으로 **Blob Storage** 블레이드를 채웁니다.

   
   |필드  |값  |
   |---------|---------|
   |**입력 별칭** | 이름 `datainput`을 사용하고 **구독에서 Blob Storage 선택**을 선택합니다.       |
   |**Storage 계정**  |  이전에 만든 저장소 계정을 선택합니다.  |
   |**컨테이너**  | 앞에서 만든 컨테이너를 선택합니다(`azuresamldemoblob`).        |
   |**이벤트 직렬화 형식**  |  **CSV**를 선택합니다.       |

   ![새 Stream Analytics 작업 입력에 대한 설정](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. **저장**을 클릭합니다.

### <a name="configure-the-job-output"></a>작업 출력 구성
작업은 입력을 가져오는 동일한 Blob Storage에 결과를 보냅니다. 

1. 작업 블레이드의 **작업 토폴로지** 아래에서 **출력** 옵션을 클릭합니다.  

2. **출력** 블레이드에서 **추가** >**Blob Storage**를 클릭한 다음, 별칭이 `datamloutput`인 출력을 추가합니다. 

3. 다음과 같은 값으로 **Blob Storage** 블레이드를 채웁니다.

   |필드  |값  |
   |---------|---------|
   |**출력 별칭** | 이름 `datamloutput`을 사용하고 **구독에서 Blob Storage 선택**을 선택합니다.       |
   |**Storage 계정**  |  이전에 만든 저장소 계정을 선택합니다.  |
   |**컨테이너**  | 앞에서 만든 컨테이너를 선택합니다(`azuresamldemoblob`).        |
   |**이벤트 직렬화 형식**  |  **CSV**를 선택합니다.       |

   ![새 Stream Analytics 작업 출력에 대한 설정](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. **저장**을 클릭합니다.   


### <a name="add-the-machine-learning-function"></a>Machine Learning 함수 추가 
이전에 웹 서비스에 Machine Learning 모델을 게시했습니다. 이 시나리오에서는 Stream Analysis 작업이 실행될 때 감정 분석에 대한 샘플을 입력에서 웹 서비스로 보냅니다. Machine Learning 웹 서비스는 감정(`positive`, `neutral` 또는 `negative`) 및 긍정적인 트윗의 확률을 반환합니다. 

자습서의 이 섹션에서는 Stream Analysis 작업의 함수를 정의합니다. 웹 서비스에 트윗을 보내고 응답을 다시 가져오기 위해 함수를 호출할 수 있습니다. 

1. Excel 통합 문서의 앞부분에서 다운로드한 웹 서비스 URL 및 API 키가 있는지 확인합니다.

2. 작업 블레이드 > **함수** > **+ 추가** > **AzureML**로 이동합니다.

3. 다음 값으로 **Azure Machine Learning 함수** 블레이드를 채웁니다.

   |필드  |값  |
   |---------|---------|
   | **함수 별칭** | 이름 `sentiment`를 사용하여 **Azure Machine Learning 함수 설정을 수동으로 제공합니다.** 를 선택합니다. 그러면 URL 및 키를 입력하기 위한 옵션이 제공됩니다.      |
   | **URL**| 웹 서비스 URL을 붙여넣습니다.|
   |**키** | API 키를 붙여넣습니다. |
  
   ![Stream Analytics 작업에 Machine Learning 함수를 추가하는 설정](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. **저장**을 클릭합니다.

### <a name="create-a-query-to-transform-the-data"></a>데이터를 변환하는 쿼리 만들기

Stream Analytics는 선언적인 SQL 기반 쿼리를 사용하여 입력을 검사하고 처리합니다. 이 섹션에서는 입력의 트윗을 읽고 Machine Learning 함수를 호출하여 감정 분석을 수행하는 쿼리를 만듭니다. 그런 다음 쿼리는 정의한 출력으로 결과를 보냅니다(Blob Storage).

1. 작업 개요 블레이드에 반환합니다.

2.  **작업 토폴로지**에서 **쿼리** 상자를 클릭합니다.

3. 다음 쿼리를 입력합니다.

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    쿼리는 입력의 트윗에 대한 감정 분석을 수행하기 위해 앞에서 만든 함수를 호출합니다(`sentiment`). 

4. **저장** 을 클릭하여 쿼리를 저장합니다.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics 작업을 시작하고 출력을 확인합니다.

이제 Stream Analytics 작업을 시작할 수 있습니다.

### <a name="start-the-job"></a>작업 시작
1. 작업 개요 블레이드에 반환합니다.

2. 블레이드 위쪽에서 **시작**을 클릭합니다.

3. **작업 시작**에서 **사용자 지정**을 선택한 다음 Blob Storage에 CSV 파일을 업로드하기 하루 전날을 선택합니다. 완료되면 **시작**을 클릭합니다.  


### <a name="check-the-output"></a>출력 파일 확인
1. **모니터링** 상자에서 작업을 확인할 때까지 몇 분 동안 작업을 실행하겠습니다. 

2. 일반적으로 Blob Storage의 내용을 검사하는 데 사용하는 도구가 있는 경우 해당 도구를 사용하여 `azuresamldemoblob` 컨테이너를 검사합니다. 또는 Azure Portal에서 다음 단계를 수행합니다.

    1. 포털에서 `samldemo` 저장소 계정을 찾고 계정 내에서 `azuresamldemoblob` 컨테이너를 찾습니다. 컨테이너에 두 개의 파일이 표시됩니다. 하나는 샘플 트윗을 포함하는 파일이고 다른 하나는 Stream Analytics 작업에 의해 생성된 CSV 파일입니다.
    2. 생성된 파일을 마우스 오른쪽 단추로 클릭한 다음 **다운로드**를 선택합니다. 

   ![Blob Storage에서 CSV 작업 출력 다운로드](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. 생성된 CSV 파일을 엽니다. 다음 예제와 유사한 출력이 표시됩니다.  
   
   ![Stream Analytics Machine Learning, CSV 보기](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>메트릭 보기
또한 Azure Machine Learning 함수 관련 메트릭도 확인할 수 있습니다. 다음 함수 관련 메트릭은 작업 블레이드의 **모니터링** 상자에서 표시됩니다.

* **함수 요청** 은 Machine Learning 웹 서비스로 전송되는 요청 수를 나타냅니다.  
* **함수 이벤트** 는 요청의 이벤트 수를 나타냅니다. 기본적으로 Machine Learning 웹 서비스에 대한 각 요청에는 최대 1,000개의 이벤트가 포함됩니다.  


## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [REST API 및 Machine Learning 통합](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)



