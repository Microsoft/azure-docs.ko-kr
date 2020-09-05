---
ms.openlocfilehash: 768e79c6a2471715b336f90748ad97ecfcc4bbc2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682316"
---

![개요](../../../media/quickstarts/overview-qs4.png)

위의 다이어그램에서는 이 빠른 시작의 신호 흐름을 보여줍니다. [에지 모듈](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)은 RTSP(Real-Time Streaming Protocol) 서버를 호스트하는 IP 카메라를 시뮬레이션합니다. [RTSP 원본](../../../media-graph-concept.md#rtsp-source) 노드는 이 서버에서 비디오 피드를 가져와서 비디오 프레임을 [동작 감지 프로세서](../../../media-graph-concept.md#motion-detection-processor) 노드로 보냅니다. RTSP 원본은 이벤트에 의해 트리거될 때까지 닫힌 상태로 유지되는 [신호 게이트 프로세서](../../../media-graph-concept.md#signal-gate-processor) 노드로 동일한 비디오 프레임을 보냅니다.

동작 감지 프로세서는 비디오의 동작을 감지하면 신호 게이트 프로세서 노드로 이벤트를 보내서 트리거합니다. 게이트가 구성된 시간 동안 열리고 [파일 싱크](../../../media-graph-concept.md#file-sink) 노드로 비디오 프레임을 보냅니다. 이 싱크 노드는 에지 디바이스의 로컬 파일 시스템에 MP4 파일로 비디오를 녹화합니다. 이 파일은 구성된 위치에 저장됩니다.

이 빠른 시작에서는 다음을 수행합니다.

1. 미디어 그래프를 만들고 배포합니다.
1. 결과를 해석합니다.
1. 리소스를 정리합니다.
