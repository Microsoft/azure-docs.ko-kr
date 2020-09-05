---
ms.openlocfilehash: 93c88f34e32e2057efd3eae25b1f41f58b948575
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682401"
---
1. F5 키를 선택하여 디버깅 세션을 시작합니다. **터미널** 창에 몇 가지 메시지가 출력됩니다.
1. *operations.json* 코드가 `GraphTopologyList` 및 `GraphInstanceList` 직접 메서드를 호출합니다. 이전 빠른 시작을 완료한 후에 리소스를 정리했다면 이 프로세스가 빈 목록을 반환한 다음, 일시 중지됩니다. Enter 키를 선택합니다.
    
    ```
    --------------------------------------------------------------------------
    Executing operation GraphTopologyList
    -----------------------  Request: GraphTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.0"
    }
    ---------------  Response: GraphTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
  
  **터미널** 창에 직접 메서드 호출의 다음 세트가 표시됩니다.  
  
  * `topologyUrl`을 사용하는 `GraphTopologySet` 호출 
  * 다음 본문을 사용하는 `GraphInstanceSet` 호출
  
  ```
  {
    "@apiVersion": "1.0",
    "name": "Sample-Graph",
    "properties": {
      "topologyName": "EVRToFilesOnMotionDetection",
      "description": "Sample graph description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
    }
  }
  ```
    
  * 그래프 인스턴스 및 비디오 흐름을 시작하는 `GraphInstanceActivate`에 대한 호출입니다.
  * 그래프 인스턴스가 실행 중 상태임을 나타내는 `GraphInstanceList`에 대한 두 번째 호출입니다.
1. **터미널** 창의 출력이 `Press Enter to continue`에서 일시 중지됩니다. 아직 Enter 키를 선택하지 마세요. 위로 스크롤하면 호출한 직접 메서드에 대한 JSON 응답 페이로드가 보입니다.
1. Visual Studio Code에서 **출력** 창으로 전환합니다. Live Video Analytics on IoT Edge 모듈에서 IoT 허브에 보내는 메시지가 표시됩니다. 이 빠른 시작의 다음 섹션에서는 이러한 메시지를 설명합니다.
1. 미디어 그래프가 계속 실행되어 결과를 출력합니다. RTSP 시뮬레이터가 원본 비디오를 계속 반복합니다. 미디어 그래프를 중지하려면 **터미널** 창으로 돌아가서 Enter 키를 선택합니다. 

    일련의 다음 호출은 리소스를 정리합니다.

    * `GraphInstanceDeactivate` 호출은 그래프 인스턴스를 비활성화합니다.
    * `GraphInstanceDelete`에 대한 호출은 인스턴스를 삭제합니다.
    * `GraphTopologyDelete` 호출은 토폴로지를 삭제합니다.
    * 마지막 `GraphTopologyList` 호출은 목록이 비어 있음을 보여줍니다.
