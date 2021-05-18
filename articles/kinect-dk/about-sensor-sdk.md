---
title: Azure Kinect 센서 SDK 정보
description: Azure Kinect 센서 SDK(소프트웨어 개발 키트), 해당 기능, 도구에 대한 개요입니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: azure, kinect, rgb, IR, 기록, 센서, sdk, 액세스, 깊이, 비디오, 카메라, imu, 동작, 센서, 오디오, 마이크, matroska, 센서 sdk, 다운로드
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "85276987"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Azure Kinect 센서 SDK 정보

이 문서에서는 Azure Kinect 센서 SDK(소프트웨어 개발 키트), 해당 기능, 도구에 대한 개요를 제공합니다.

## <a name="features"></a>기능

Azure Kinect 센서 SDK는 다음을 포함하여 Azure Kinect 디바이스 구성 및 하드웨어 센서 스트림에 대해 플랫폼 간 낮은 수준의 액세스를 제공합니다.

- 깊이 카메라 액세스 및 모드 제어(수동 IR 모드, 넓은/좁은 보기 필드 깊이 모드) 
- RGB 카메라 액세스 및 제어(예: 노출 및 화이트 균형) 
- 동작 센서(자이로스코프 및 가속도계) 액세스 
- 카메라 간에 구성 가능한 지연을 사용하는 동기화된 깊이-RGB 카메라 스트리밍 
- 디바이스 간에 구성 가능한 지연 오프셋을 사용하여 외부 디바이스 동기화 제어 
- 이미지 해상도, 타임스탬프 등에 대한 카메라 프레임 메타데이터 액세스 
- 디바이스 보정 데이터 액세스 

## <a name="tools"></a>도구

- 디바이스 데이터 스트림을 모니터링하고 여러 가지 모드를 구성할 수 있는 [Azure Kinect 뷰어](azure-kinect-viewer.md)
- [Matroska 컨테이너 형식](record-file-format.md)을 사용하는 [Azure Kinect 레코더](azure-kinect-recorder.md) 및 재생 판독기 API
- Azure Kinect DK [펌웨어 업데이트 도구](azure-kinect-firmware-tool.md)입니다.

## <a name="sensor-sdk"></a>센서 SDK

- [센서 SDK를 다운로드](sensor-sdk-download.md)합니다.
- 센서 SDK는 [GitHub의 오픈 소스](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)에서 사용 할 수 있습니다.
- 사용법에 대한 자세한 정보는 [센서 SDK API 설명서](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure Kinect 센서 SDK에 대해 배웠으므로 다음 작업을 수행할 수 있습니다.
>[!div class="nextstepaction"]
>[센서 SDK 코드 다운로드](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[디바이스 찾기 및 열기](find-then-open-device.md)
