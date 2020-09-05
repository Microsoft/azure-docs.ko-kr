---
ms.openlocfilehash: e46a56742ab8b98c53c1cd05e840e8ad4b8a73da
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682303"
---
[IoT Edge 배포 매니페스트 생성 및 배포](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest) 단계에서 Visual Studio Code의 **AZURE IOT HUB**(왼쪽 하단 섹션) 아래에서 **lva-sample-device** 노드를 확장합니다. 다음 모듈이 배포된 것을 볼 수 있습니다.

* `lvaEdge`라는 이름의 Live Video Analytics 모듈
* RTSP 서버를 시뮬레이션하고 라이브 비디오 피드의 원본으로 작동하는 `rtspsim` 모듈

  ![모듈](../../../media/quickstarts/lva-sample-device-node.png)

> [!NOTE]
> 설치 스크립트를 통해 프로비저닝된 에지 디바이스 대신 사용자 고유의 에지 디바이스를 사용하는 경우 에지 디바이스로 이동하고, **관리자 권한**으로 다음 명령을 실행하여 이 빠른 시작에 사용되는 샘플 비디오 파일을 가져와 저장합니다.  

```
mkdir /home/lvaadmin/samples
mkdir /home/lvaadmin/samples/input    
curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
chown -R lvaadmin /home/lvaadmin/samples/  
```
