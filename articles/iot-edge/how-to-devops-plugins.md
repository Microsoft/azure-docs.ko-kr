---
title: Jenkins 플러그 인을 통해 CI/CD 사용 - Azure IoT Edge | Microsoft Docs
description: Jenkins용 Azure IoT Edge 확장을 사용하면 IoT Edge 개발 및 배포 작업을 기존 DevOps 솔루션에 통합할 수 있습니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 173e6ff91acd2ad28d7203b2b5db65e0ee0ecc43
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126344"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Jenkins 파이프라인과 Azure IoT Edge 통합

Azure IoT Edge는 Azure DevOps 및 Azure DevOps Projects를 기본적으로 지원하지만 DevOps 기능을 Jenkins로 확장하는 플러그 인도 제공합니다. [Jenkins](https://jenkins.io/)는 플러그 인을 사용하여 IoT Edge를 비롯한 여러 유형의 개발 및 배포 프로젝트를 지원하는 오픈 소스 자동화 서버입니다. 

Jenkins용 Azure IoT Edge 플러그 인은 연속 통합 및 지속적인 배포에 중점을 둡니다. 모듈을 빌드하고 컨테이너 이미지를 컨테이너 레지스트리에 푸시하는 빌드 및 푸시 파이프라인을 만들 수 있습니다. 그런 다음, IoT Edge 디바이스에 모듈을 배포하는 릴리스 파이프라인을 만듭니다. 

Jenkins용 IoT Edge 플러그 인을 사용하기 전에 Azure에 IoT 허브가 있어야 하며 컨테이너 이미지를 보관할 컨테이너 레지스트리가 필요합니다. 플러그 인이 IoT Edge 디바이스용 배포를 만들 수 있도록 Azure 서비스 주체를 사용하여 IoT 허브에 Jenkins contributor 권한을 부여합니다. 

시작할 준비가 되었으면 [Jenkins용 Azure IoT Edge 플러그 인](https://plugins.jenkins.io/azure-iot-edge)에 대한 설치 및 사용 정보를 확인합니다.