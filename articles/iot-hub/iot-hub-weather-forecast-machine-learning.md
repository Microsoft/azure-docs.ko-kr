---
title: Azure Machine Learning을 사용하여 IoT Hub 데이터로 일기 예보 | Microsoft Docs
description: Azure Machine Learning을 사용하여 IoT Hub가 센서에서 수집한 온도 및 습도 데이터를 기반으로 하여 강우 확률을 예측합니다.
author: robinsh
manager: philmea
keywords: 일기 예보 기계 학습
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: ffc2e5fb588ce6861f5df6cefdf810c1a015c043
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61441018"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Azure Machine Learning에서 IoT Hub의 센서 데이터를 사용한 일기 예보

![종단 간 다이어그램](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

기계 학습은 컴퓨터에서 기존 데이터로부터 학습하여 미래 동작, 결과 및 추세를 예측하는 데 유용한 데이터 과학 기술입니다. Azure Machine Learning은 예측 모델을 신속하게 만들고 분석 솔루션으로 배포할 수 있게 해주는 클라우드 예측 분석 서비스입니다.

## <a name="what-you-learn"></a>학습 내용

Azure Machine Learning을 사용하여 Azure IoT Hub의 온도 및 습도 데이터를 통해 일기 예보(강우 확률)를 수행하는 방법에 대해 알아봅니다. 강우 확률은 준비된 날씨 예측 모델의 결과입니다. 모델에서는 기록 데이터를 기반으로 하여 온도 및 습도에 따라 강우 확률을 예측합니다.

## <a name="what-you-do"></a>수행할 작업

- 날씨 예측 모델을 웹 서비스로 배포합니다.
- 소비자 그룹을 추가하여 IoT Hub에서 데이터 액세스 준비
- Stream Analytics 작업을 만들고 해당 작업을 다음과 같이 구성합니다.
  - IoT Hub에서 온도 및 습도 데이터를 읽습니다.
  - 웹 서비스를 호출하여 강우 확률을 가져옵니다.
  - Azure Blob Storage에 결과를 저장합니다.
- Microsoft Azure Storage 탐색기를 사용하여 일기 예보를 살펴봅니다.

## <a name="what-you-need"></a>필요한 항목

- 완료 합니다 [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서; 중 하나 예를 들어 [node.js 사용 하 여 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 이러한 다음 요구 사항을 다룹니다.
  - 활성 Azure 구독.
  - 구독 중인 Azure IoT Hub
  - 메시지를 Azure IoT Hub로 보내는 클라이언트 애플리케이션
- Azure Machine Learning Studio 계정 [Machine Learning Studio 체험해 보기](https://studio.azureml.net/)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>날씨 예측 모델을 웹 서비스로 배포

1. [날씨 예측 모델 페이지](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1)로 이동합니다.
1. Microsoft Azure Machine Learning Studio에서 **Studio에서 열기**를 클릭합니다.
   ![Cortana Intelligence 갤러리에서 날씨 예측 모델 페이지 열기](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. **실행**을 클릭하여 모델의 단계에 대한 유효성을 검사합니다. 이 단계를 완료하는 데 2분 정도 걸릴 수 있습니다.
   ![Azure Machine Learning Studio에서 날씨 예측 모델 열기](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. **웹 서비스 설정** > **예측형 웹 서비스**를 클릭합니다.
   ![Azure Machine Learning Studio에 날씨 예측 모델 배포](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. 다이어그램에서 **웹 서비스 입력** 모듈을 **모델 점수 매기기** 모듈 근처로 끌어갑니다.
1. **웹 서비스 입력** 모듈을 **모델 점수 매기기** 모듈에 연결합니다.
   ![Azure Machine Learning Studio에서 두 모듈 연결](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. **실행**을 클릭하여 모델의 단계에 대한 유효성을 검사합니다.
1. **웹 서비스 배포**를 클릭하여 모델을 웹 서비스로 배포합니다.
1. 모델의 대시보드에서 **요청/응답**에 대한 **Excel 2010 또는 이전 통합 문서**를 다운로드합니다.

   > [!Note]
   > 컴퓨터에서 최신 버전의 Excel을 실행하더라도 **Excel 2010 또는 이전 통합 문서**를 다운로드해야 합니다.

   ![REQUEST RESPONSE 엔드포인트에 대한 Excel 다운로드](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Excel 통합 문서를 열고 **웹 서비스 URL** 및 **액세스 키**를 기록해 둡니다.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics 작업 만들기, 구성 및 실행

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **리소스 만들기** > **사물 인터넷** > **Stream Analytics 작업**을 차례로 클릭합니다.
1. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **Location**: 리소스 그룹과 동일한 위치를 사용합니다.

   **대시보드에 고정**: 대시보드에서 IoT Hub에 쉽게 액세스하려면 이 옵션을 선택합니다.

   ![Azure에서 Stream Analytics 작업 만들기](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. **만들기**를 클릭합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. Stream Analytics 작업을 엽니다.
1. **작업 토폴로지**에서 **입력**을 클릭합니다.
1. **입력** 창에서 **추가**를 클릭하고 다음 정보를 입력합니다.

   **입력 별칭**: 입력에 대 한 고유 별칭입니다.

   **원본**: **IoT Hub**를 선택합니다.

   **소비자 그룹**: 만든 소비자 그룹을 선택 합니다.

   ![Azure에서 Stream Analytics 작업에 입력 추가](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. **만들기**를 클릭합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지**에서 **출력**을 클릭합니다.
1. **출력** 창에서 **추가**를 클릭하고 다음 정보를 입력합니다.

   **출력 별칭**: 출력에 대한 고유 별칭입니다.

   **싱크**: 선택 **Blob Storage**합니다.

   **스토리지 계정**: Blob storage에 대 한 저장소 계정입니다. 저장소 계정을 만들거나 기존 계정을 사용할 수 있습니다.

   **컨테이너**: Blob 저장 된 컨테이너입니다. 컨테이너를 만들거나 기존 컨테이너를 사용할 수 있습니다.

   **이벤트 직렬화 형식**: **CSV**를 선택합니다.

   ![Azure에서 Stream Analytics 작업에 출력 추가](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. **만들기**를 클릭합니다.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Stream Analytics 작업에 배포된 웹 서비스를 호출하는 함수 추가

1. **작업 토폴로지**에서 **함수** > **추가**를 클릭합니다.
1. 다음 정보를 입력합니다.

   **함수 별칭**: [https://slack.botframework.com](`machinelearning`) 을 입력합니다.

   **함수 유형**: 선택 **Azure ML**합니다.

   **가져오기 옵션**: 선택 **다른 구독에서 가져오기**합니다.

   **URL**: Excel 통합 문서에서 기록해 둔 웹 서비스 URL을 입력 합니다.

   **키**: Excel 통합 문서에서 기록해 둔 액세스 키를 입력 합니다.

   ![Azure에서 Stream Analytics 작업에 함수 추가](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. **만들기**를 클릭합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지**에서 **쿼리**를 클릭합니다.
1. 기존 코드를 다음 코드로 바꿉니다.

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다.

   `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다.

1. **저장**을 클릭합니다.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업에서 **시작** > **지금 시작** > **시작**을 차례로 클릭합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨**에서 **실행 중**으로 변경됩니다.

![Stream Analytics 작업 실행](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure Storage 탐색기를 사용하여 일기 예보 보기

클라이언트 애플리케이션을 실행하여 온도 및 습도 데이터를 수집하여 IoT Hub로 보냅니다. Stream Analytics 작업에서는 IoT Hub에서 받은 각 메시지에 대해 일기 예보 웹 서비스를 호출하여 강우 확률을 생성합니다. 그런 다음 Azure Blob Storage에 결과를 저장합니다. Azure Storage 탐색기는 결과를 보는 데 사용할 수 있는 도구입니다.

1. [Microsoft Azure Storage 탐색기를 다운로드하고 설치합니다](https://storageexplorer.com/).
1. Azure Storage 탐색기를 엽니다.
1. Azure 계정에 로그인합니다.
1. 구독을 선택합니다.
1. 구독> **Storage 계정** > Storage 계정> **Blob 컨테이너**> 컨테이너를 차례로 클릭합니다.
1. .csv 파일을 열어 결과를 확인합니다. 마지막 열은 강우 확률을 기록하고 있습니다.

   ![Azure Machine Learning을 사용하여 일기 예보 결과 가져오기](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>요약

Azure Machine Learning을 사용하여 IoT Hub에서 받은 온도 및 습도 데이터를 기반으로 하여 강우 확률을 생성했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]