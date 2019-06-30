---
title: Azure Stream Analytics tools for Visual Studio에서에서 Stream Analytics Edge 작업
description: 이 문서 작성, 디버그 및 Visual Studio 용 Stream Analytics 도구를 사용 하 여 작업 IoT Edge의 Stream 분석을 만드는 방법을 설명 합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 1601bf6c73d9f3450959773c85385bc8ef907a66
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329955"
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Visual Studio 도구를 사용하여 Stream Analytics Edge 작업 개발

이 자습서에서는 Visual Studio 용 Stream Analytics 도구를 사용 하는 방법을 알아봅니다. 작성, 디버그 및 Stream Analytics Edge 작업을 만드는 방법에 알아봅니다. 작업을 만들고 테스트한 후에는 Azure Portal로 이동하여 디바이스에 배포할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 설치 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)하십시오 [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/), 또는 [Visual Studio 2013 업데이트 4](https://www.microsoft.com/download/details.aspx?id=45326)합니다. Enterprise(Ultimate/Premium), Professional 및 Community Edition이 지원됩니다. Express Edition은 지원되지 않습니다.  

* [설치 지침](stream-analytics-tools-for-visual-studio-edge-jobs.md)에 따라 Visual Studio용 Stream Analytics 도구를 설치합니다.
 
## <a name="create-a-stream-analytics-edge-project"></a>Stream Analytics Edge 프로젝트 만들기 

Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 차례로 선택합니다. 왼쪽의 **템플릿** 목록으로 이동하고, **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge 애플리케이션**을 차례로 펼칩니다. 프로젝트에 대한 이름, 위치 및 솔루션 이름을 제공하고 **확인**을 선택합니다.

![Visual Studio에서 새 Stream Analytics Edge 프로젝트](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-stream-analytics-edge-project.png)

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

2. 선택에 대 한 싱크를 설정 해야 **Edge Hub**, 이벤트 직렬화 형식으로 설정 **Json**인코딩을 설정 **UTF8**, 형식을 설정 하 고 **배열**합니다. 필요에 따라 **출력 별칭**의 이름을 바꿀 수 있으며, 이 예제에서는 그대로 두겠습니다. 출력 별칭의 이름을 바꾸는 경우 쿼리를 정의할 때 지정한 이름을 사용합니다. **저장**을 선택하여 설정을 저장합니다. 
   ![Stream Analytics 작업 출력 구성](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>변환 쿼리 정의

Stream Analytics IoT Edge 환경에 배포 하는 Stream Analytics 작업을 대부분 지원 [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396)합니다. 그러나 Stream Analytics Edge 작업에 대 한 다음 작업을 아직 지원 하지 않습니다. 


|**범주**  | **Command**  |
|---------|---------|
|기타 연산자 | <ul><li>PARTITION BY</li><li>TIMESTAMP BY OVER</li><li>JavaScript UDF</li><li>사용자 정의 집계 (UDA)</li><li>GetMetadataPropertyValue</li><li>집계 수가 14 개를 사용 하 여 단일 단계에서</li></ul>   |

포털에서 Stream Analytics Edge 작업을 만들 때 컴파일러는 자동으로 경고 지원 되는 연산자를 사용 하지 않는 경우.

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
4. 로컬로 실행 하거나 Azure에 제출할 수 있습니다. 선택 쿼리를 테스트 하려면 **로컬로 실행**합니다.  
   ![Visual Studio에서 Stream Analytics 작업 실행 옵션](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-visual-stuidio-run-options.png)
 
5. 명령 프롬프트 창에 작업의 상태가 표시됩니다. 작업이 성공적으로 실행되면 프로젝트 폴더 경로(“Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42”)에 “2018-02-23-11-31-42”와 같은 폴더가 만들어집니다. 폴더 경로로 이동하여 로컬 폴더의 결과를 봅니다.

   또한 Azure Portal에 로그인하여 작업이 만들어졌는지 확인할 수도 있습니다. 

   ![Stream Analytics 작업 결과 폴더](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-job-result-folder.png)

## <a name="submit-the-job-to-azure"></a>Azure에 작업 제출

1. Azure에 작업을 제출하기 전에 먼저 Azure 구독에 연결해야 합니다. **서버 탐색기**를 열고, **Azure** > **Microsoft Azure 구독에 연결**을 마우스 오른쪽 단추로 클릭하고, Azure 구독에 로그인합니다.  

2. Azure에 작업을 제출하려면 쿼리 편집기로 이동하고, **Azure에 제출**을 선택합니다.  

3. 팝업 창이 열립니다. 기존 Stream Analytics Edge 작업을 업데이트 하거나 새로 만들기를 선택 합니다. 기존 작업을 업데이트 하면이 시나리오에서는 모든 작업 구성이 대체할 경우 새 작업을 게시 해 봅니다. **새 Azure Stream Analytics 작업 만들기**를 선택하고, **MyASAEdgeJob**과 같은 작업 이름을 입력하고, 필요한 **구독**, **리소스 그룹** 및 **위치**를 선택하고, **제출**을 선택합니다.

   ![Visual Studio에서 Azure에 Stream Analytics 작업 제출](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-stream-analytics-job-to-azure.png)
 
   이제 Stream Analytics Edge 작업 작성 되었습니다. 참조할 수 있습니다 합니다 [IoT Edge 자습서에서 작업을 실행할](stream-analytics-edge.md) 장치에 배포 하는 방법을 알아보려면. 

## <a name="manage-the-job"></a>작업 관리 

서버 탐색기에서 작업 및 작업 다이어그램의 상태를 볼 수 있습니다. **Stream Analytics** 에 **서버 탐색기**, 구독 및 Stream Analytics Edge 작업을 배포할 리소스 그룹을 확장 합니다. MyASAEdgejob의 상태가 **생성됨**인 것을 확인할 수 있습니다. 작업 노드를 펼치고, 해당 작업 노드를 두 번 클릭하여 작업 보기를 엽니다.

![서버 탐색기 작업 관리 옵션](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
작업 보기 창에서는 Azure Portal의 작업 새로 고침, 작업 삭제, 작업 열기와 같은 작업을 제공합니다.

![Visual Studio에서 작업 다이어그램 및 기타 옵션](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>다음 단계

* [Azure IoT Edge에 대한 추가 정보](../iot-edge/about-iot-edge.md)
* [IoT Edge의 ASA 자습서](../iot-edge/tutorial-deploy-stream-analytics.md)
* [이 설문 조사를 사용하여 팀에 의견 보내기](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
