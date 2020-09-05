---
title: 비디오 분석을 배포 하는 방법-개체 및 동작 검색 Azure IoT Central 응용 프로그램 템플릿
description: 이 가이드는 비디오 분석-개체 및 동작 검색 응용 프로그램 템플릿을 사용 하 여 Azure IoT Central 응용 프로그램을 배포 하는 단계를 요약 합니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d661df57e4409c1d7fe196c7f136965191421bd4
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719712"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>비디오 분석-개체 및 동작 검색 응용 프로그램 템플릿을 사용 하 여 IoT Central 응용 프로그램을 배포 하는 방법

핵심 *비디오 분석-개체 및 동작 검색* 응용 프로그램 구성 요소에 대 한 개요는 [개체 및 동작 감지 비디오 분석 응용 프로그램 아키텍처](architecture-video-analytics.md)를 참조 하세요.

## <a name="deploy-the-application"></a>애플리케이션 배포

Video analytics 응용 프로그램 템플릿을 사용 하 여 IoT Central 응용 프로그램을 배포 하려면 다음 단계를 완료 합니다.

1. [Azure IoT Central에서 video analytics 응용 프로그램 만들기](tutorial-video-analytics-create-app.md) 자습서를 완료 하 여 다음을 수행 합니다.
    - Azure Media Services 계정을 만듭니다.
    - 비디오 분석-개체 및 동작 검색 응용 프로그램 템플릿에서 IoT Central 응용 프로그램을 만듭니다.
    - IoT Central 응용 프로그램에서 게이트웨이 장치를 구성 합니다. 게이트웨이를 사용 하면 카메라 장치를 응용 프로그램에 연결할 수 있습니다.

1. [비디오 분석을 위한 IoT Edge 인스턴스 만들기 (LINUX VM)](tutorial-video-analytics-iot-edge-vm.md) 자습서를 완료 하려면 다음을 수행 합니다.
    - Azure IoT Edge 런타임이 설치 된 Azure VM을 만듭니다.-IoT Edge 설치를 준비 하 여 비디오 분석 모듈을 호스팅합니다.
    - IoT Edge 장치를 IoT Central 응용 프로그램에 연결 합니다.

1. [비디오 분석 응용 프로그램 모니터링 및 관리](tutorial-video-analytics-manage.md) 자습서를 완료 하 여 다음을 수행 합니다.
    - IoT Central 응용 프로그램에서 게이트웨이에 개체 및 동작 검색 카메라를 추가 합니다.
    - 카메라 처리를 시작 합니다.
    - AMS에서 캡처한 비디오를 볼 수 있도록 로컬 미디어 플레이어를 설치 합니다.
    - 검색 된 개체를 표시 하는 캡처된 비디오를 봅니다.
    - 정리 합니다.

## <a name="next-steps"></a>다음 단계

이제 비디오 분석 응용 프로그램 템플릿을 배포 하 고 사용 하는 단계에 대 한 개요를 확인할 수 있습니다. 시작 하려면 [Azure IoT Central에서 비디오 분석 응용 프로그램 만들기](tutorial-video-analytics-create-app.md) 를 참조 하세요.
