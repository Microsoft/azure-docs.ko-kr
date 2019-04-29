---
title: Azure Stream Analytics for Visual Studio를 사용하여 라이브 데이터 테스트
description: 라이브 스트리밍 데이터를 사용하여 Azure Stream Analytics 작업을 로컬로 테스트하는 방법을 알아봅니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479833"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트(미리 보기)

Azure Stream Analytics Tools for Visual Studio를 사용하면 Azure Event Hub, IoT Hub 및 Blob Storage의 라이브 이벤트 스트림을 사용하여 IDE에서 작업을 로컬로 테스트할 수 있습니다. 라이브 데이터의 로컬 테스트는 클라우드에서 수행할 수 있는 [성능 및 확장성 테스트](stream-analytics-streaming-unit-consumption.md)를 대체할 수 없지만, 스트림Stream Analytics 작업을 테스트할 때마다 클라우드에 제출할 필요가 없으므로 기능 테스트를 수행하는 시간을 절약할 수 있습니다. 이 기능은 미리 보기로 있으며, 프로덕션 워크로드에 사용할 수 없습니다.

## <a name="testing-options"></a>테스트 옵션

지원되는 로컬 테스트 옵션은 다음과 같습니다.

|**입력**  |**출력**  |**작업 유형**  |
|---------|---------|---------|
|정적 로컬 데이터   |  정적 로컬 데이터   |   클라우드/에지 |
|라이브 입력 데이터   |  정적 로컬 데이터   |   클라우드 |
|라이브 입력 데이터   |  라이브 출력 데이터   |   클라우드 |

## <a name="local-testing-with-live-data"></a>라이브 데이터를 사용하여 로컬 테스트

1. [Visual Studio에서 Azure Stream Analytics 클라우드 프로젝트](stream-analytics-quick-create-vs.md)를 만든 후에 **script.asaql**을 엽니다. 로컬 테스트는 기본적으로 로컬 입력 및 로컬 출력을 사용합니다.

   ![Azure Stream Analytics Visual Studio 로컬 입력 및 로컬 출력](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. 라이브 데이터를 테스트하려면 드롭다운 상자에서 **클라우드 입력 사용**을 선택합니다.

   ![Azure Stream Analytics Visual Studio 라이브 클라우드 입력](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. **시작 시간**을 설정하여 작업에서 입력 데이터 처리를 시작할 시기를 정의합니다. 정확한 결과를 보장하려면 작업이 입력 데이터를 미리 읽어야 할 수도 있습니다. 기본 시간은 현재 시간에서 30분 전으로 설정됩니다.

   ![Azure Stream Analytics Visual Studio 라이브 데이터 시작 시간](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. **로컬에서 실행**을 클릭합니다. 실행 진행률과 작업 메트릭이 있는 콘솔 창이 나타납니다. 프로세스를지 중지하려면 수동으로 중지할 수 있습니다. 

   ![Azure Stream Analytics Visual Studio 라이브 데이터 프로세스 창](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   출력 결과는 처음 500개의 출력 행이 있는 로컬 실행 결과 창에서 3초마다 새로 고쳐지며, 출력 파일은 프로젝트 경로의 **ASALocalRun** 폴더에 저장됩니다. 로컬 실행 결과 창에서 **결과 폴더 열기** 단추를 클릭하여 출력 파일을 열 수도 있습니다.

   ![Azure Stream Analytics Visual Studio 라이브 데이터 결과 폴더 열기](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. 결과를 클라우드 출력 싱크에 출력하려면 두 번째 드롭다운 상자에서 **클라우드에 출력**을 선택합니다. Power BI 및 Azure Data Lake Storage는 지원되지 않는 출력 싱크입니다.

   ![Azure Stream Analytics Visual Studio 라이브 데이터 클라우드로 출력](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>제한 사항

* Power BI 및 Azure Data Lake Storage는 인증 모델 제한으로 인해 지원되지 않는 출력 싱크입니다.

* 클라우드 입력 옵션만 [시간 정책](stream-analytics-out-of-order-and-late-events.md)을 지원하고, 로컬 입력 옵션은 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics Tools for Visual Studio를 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [Visual Studio용 Azure Stream Analytics 도구 설치](stream-analytics-tools-for-visual-studio-install.md)
* [Visual Studio를 사용하여 로컬로 Stream Analytics 쿼리 테스트](stream-analytics-vs-tools-local-run.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)