---
title: Visual Studio용 Azure Stream Analytics 도구에서의 Edge 작업
description: 이 문서에서는 Visual Studio용 Stream Analytics 도구를 사용하여 Stream Analytics Edge 작업을 작성하고, 디버그하고, 만드는 방법에 대해 알아봅니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 684690baa63f5ccd65c69e3a1b7e310c2f809e59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60762715"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Visual Studio 도구를 사용하여 Stream Analytics Edge 작업 개발

이 자습서에서는 Visual Studio용 Stream Analytics 도구를 사용하여 Stream Analytics Edge 작업을 작성하고, 디버그하고, 만드는 방법에 대해 알아봅니다. 작업을 만들고 테스트한 후에는 Azure Portal로 이동하여 디바이스에 배포할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) 또는 [Visual Studio 2013 업데이트 4](https://www.microsoft.com/download/details.aspx?id=45326)를 설치합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다.  

* [설치 지침](stream-analytics-tools-for-visual-studio-edge-jobs.md)에 따라 Visual Studio용 Stream Analytics 도구를 설치합니다.
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge 프로젝트 만들기 

Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 차례로 선택합니다. 왼쪽의 **템플릿** 목록으로 이동하고, **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge 애플리케이션**을 차례로 펼칩니다. 프로젝트에 대한 이름, 위치 및 솔루션 이름을 제공하고 **확인**을 선택합니다.

![Visual Studio의 새 Edge 프로젝트](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

프로젝트가 만들어지면 **솔루션 탐색기**로 이동하여 폴더 계층 구조를 봅니다.

![Stream Analytics Edge 작업의 솔루션 탐색기 보기](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>올바른 구독 선택

1. Visual Studio **보기** 메뉴에서 **서버 탐색기**를 선택합니다.  

2. **Azure**를 마우스 오른쪽 단추로 클릭하고 > **Microsoft Azure 구독에 연결**을 선택한 다음, > Azure 계정으로 로그인합니다.

## <a name="define-inputs"></a>입력 정의

1. **솔루션 탐색기**에서 **입력** 노드를 펼치면 **EdgeInput.json**이라는 입력이 표시됩니다. 두 번 클릭하여 해당 설정을 봅니다.  

2. 원본 형식은 **데이터 스트림**으로 설정합니다. 그런 다음, 원본을 **Edge Hub**로, 이벤트 직렬화 형식을 **Json**으로, 인코딩을 **UTF8**로 설정합니다. 필요에 따라 **입력 별칭**의 이름을 바꿀 수 있으며, 이 예제에서는 그대로 두겠습니다. 입력 별칭의 이름을 바꾸는 경우 쿼리를 정의할 때 지정한 이름을 사용합니다. **저장**을 선택하여 설정을 저장합니다.  
   ![Stream Analytics 작업 입력 구성](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>출력 정의

1. **솔루션 탐색기**에서 **출력** 노드를 펼치면 **EdgeOutput.json**이라는 출력이 표시됩니다. 두 번 클릭하여 해당 설정을 봅니다.  

2. 싱크가 **Edge Hub**, 이벤트 직렬화 형식이 **Json**, 인코딩이 **UTF8**, 형식이 **배열**로 설정되어 있는지 확인합니다. 필요에 따라 **출력 별칭**의 이름을 바꿀 수 있으며, 이 예제에서는 그대로 두겠습니다. 출력 별칭의 이름을 바꾸는 경우 쿼리를 정의할 때 지정한 이름을 사용합니다. **저장**을 선택하여 설정을 저장합니다. 
   ![Stream Analytics 작업 출력 구성](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>변환 쿼리 정의

Edge 환경에 배포된 Stream Analytics 작업은 대부분의 [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)를 지원하지만, 아직 Edge 작업에 지원되지 않는 작업은 다음과 같습니다. 


|**범주**  | **명령**  |
|---------|---------|
|지리 공간적 연산자 |<ul><li>CreatePoint</li><li>CreatePolygon</li><li>CreateLineString</li><li>ST_DISTANCE</li><li>ST_WITHIN</li><li>ST_OVERLAPS</li><li>ST_INTERSECTS</li></ul> |
|기타 연산자 | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>COUNT 연산자의 식 매개 변수</li><li>DATE 및 TIME 함수의 마이크로초</li><li>JavaScript UDA(이 기능은 아직도 클라우드에 배포된 작업에 대한 미리 보기 상태로 있음)</li></ul>   |

포털에서 Edge 작업을 만들 때 지원되는 연산자를 사용하지 않는 경우 컴파일러에서 자동으로 경고합니다.

Visual Studio의 쿼리 편집기(**script.asaql 파일**)에서 다음 변환 쿼리를 정의합니다.

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>로컬로 작업 테스트

쿼리를 로컬로 테스트하려면 샘플 데이터를 업로드해야 합니다. [GitHub 리포지토리](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json)에서 등록 데이터를 다운로드하여 샘플 데이터를 가져오고 로컬 컴퓨터에 저장할 수 있습니다. 

1. 샘플 데이터를 업로드하려면 **EdgeInput.json** 파일을 마우스 오른쪽 단추로 클릭하고, **로컬 입력 추가**를 선택합니다.  

2. 팝업 창의 로컬 경로에서 샘플 데이터를 **찾아보고** **저장**을 선택합니다.
   ![Visual Studio Code에서 로컬 입력 구성](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. **local_EdgeInput.json** 파일이 입력 폴더에 자동으로 추가됩니다.  
4. 로컬로 실행하거나 Azure에 제출할 수 있습니다. 쿼리를 테스트하려면> **로컬로 실행**을 선택합니다.  
   ![Visual Studio에서 Stream Analytics 작업 실행 옵션](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. 명령 프롬프트 창에 작업의 상태가 표시됩니다. 작업이 성공적으로 실행되면 프로젝트 폴더 경로(“Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42”)에 “2018-02-23-11-31-42”와 같은 폴더가 만들어집니다. 폴더 경로로 이동하여 로컬 폴더의 결과를 봅니다.

   또한 Azure Portal에 로그인하여 작업이 만들어졌는지 확인할 수도 있습니다. 

   ![Stream Analytics 작업 결과 폴더](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Azure에 작업 제출

1. Azure에 작업을 제출하기 전에 먼저 Azure 구독에 연결해야 합니다. **서버 탐색기**를 열고, **Azure** > **Microsoft Azure 구독에 연결**을 마우스 오른쪽 단추로 클릭하고, Azure 구독에 로그인합니다.  

2. Azure에 작업을 제출하려면 쿼리 편집기로 이동하고, **Azure에 제출**을 선택합니다.  

3. 기존 Edge 작업을 업데이트하거나 새 Edge를 만들도록 선택할 수 있는 팝업 창이 열립니다. 기존 작업을 업데이트하면 모든 작업 구성이 대체되며, 이 시나리오에서는 새 작업을 게시합니다. **새 Azure Stream Analytics 작업 만들기**를 선택하고, **MyASAEdgeJob**과 같은 작업 이름을 입력하고, 필요한 **구독**, **리소스 그룹** 및 **위치**를 선택하고, **제출**을 선택합니다.

   ![Visual Studio에서 Azure에 Stream Analytics 작업 제출](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   이제 Stream Analytics Edge 작업이 만들어졌으므로 [IoT Edge에서 작업 실행 자습서](stream-analytics-edge.md)를 참조하여 디바이스에 디바이스를 배포하는 방법을 알아볼 수 있습니다. 

## <a name="manage-the-job"></a>작업 관리 

서버 탐색기에서 작업 및 작업 다이어그램의 상태를 볼 수 있습니다. **서버 탐색기**의 **Stream Analytics**에서 구독 및 Edge 작업을 배포한 리소스 그룹을 확장합니다. MyASAEdgejob의 상태가 **생성됨**인 것을 확인할 수 있습니다. 작업 노드를 펼치고, 해당 작업 노드를 두 번 클릭하여 작업 보기를 엽니다.

![서버 탐색기 작업 관리 옵션](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
작업 보기 창에서는 Azure Portal의 작업 새로 고침, 작업 삭제, 작업 열기와 같은 작업을 제공합니다.

![Visual Studio에서 작업 다이어그램 및 기타 옵션](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge에 대한 추가 정보](../iot-edge/about-iot-edge.md)
* [IoT Edge의 ASA 자습서](../iot-edge/tutorial-deploy-stream-analytics.md)
* [이 설문 조사를 사용하여 팀에 의견 보내기](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
