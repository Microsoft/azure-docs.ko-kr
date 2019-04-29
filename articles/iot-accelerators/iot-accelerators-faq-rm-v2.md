---
title: 원격 모니터링 솔루션 가속기 FAQ - Azure | Microsoft Docs
description: 원격 모니터링 솔루션 가속기에 대한 질문과 대답
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 746d21c7ff4d5b939eea7690193ac07425b4001c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447915"
---
# <a name="frequently-asked-questions-for-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기에 대한 질문과 대답

일반 [FAQ](iot-accelerators-faq.md)도 참조하세요.

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>새 원격 모니터링 솔루션을 프로비저닝하는 데 드는 비용은 얼마인가요?

새로운 솔루션 가속기는 두 가지 배포 옵션을 제공합니다.

* *기본* 옵션은 낮은 개발 비용을 원하는 개발자나 데모 또는 개념 증명을 빌드하려는 고객을 위해 설계되었습니다.
* *표준* 옵션은 프로덕션이 준비된 인프라를 배포하려는 기업을 위해 설계되었습니다.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>솔루션을 개발하는 동안 비용을 낮게 유지하려면 어떻게 해야 하나요?

두 가지 차별화된 배포를 제공하는 것 외에 새로운 원격 모니터링 솔루션에는 시뮬레이션된 모든 디바이스를 필요할 때 활성화 또는 비활성화할 수 있는 설정이 있습니다. 시뮬레이션을 비활성화하면 솔루션에서 수집된 데이터가 줄어들어 전체 비용이 절감됩니다.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>기본 배포와 표준 배포의 차이가 무엇인가요? 두 가지 배포 옵션 중 적합한 옵션을 어떻게 결정하나요?

각 배포 옵션은 서로 다른 요구에 대응합니다. 기본 배포는 PoC 및 소규모 파일럿을 시작하고 개발하도록 설계되었습니다. 최소한의 필요한 리소스와 보다 저렴한 비용으로 간소화된 아키텍처를 제공합니다. 표준 배포는 프로덕션이 준비된 솔루션을 빌드하고 사용자 지정하도록 설계되었으며 이를 실현하는 데 필요한 요소가 포함된 배포를 제공합니다. 안정성과 확장성을 위해 애플리케이션 마이크로 서비스는 Docker 컨테이너로 빌드되고 오케스트레이터(기본적으로 Kubernetes)를 사용하여 배포됩니다. 오케스트레이터는 애플리케이션의 배포, 확장 및 관리를 담당합니다. 현재 필요에 따라 옵션을 선택해야 합니다. 프로젝트 단계에 따라 둘 중 하나를 사용할 수도 있고, 둘 다를 조합하여 사용할 수도 있습니다.

### <a name="how-do-i-configure-a-dynamic-map-on-the-dashboard"></a>대시보드에서 동적 맵을 구성하려면 어떻게 해야 할까요?

자세한 내용은 [동적 맵에서 디바이스를 보기 위해 맵 키 업그레이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)를 참조하세요.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>원격 모니터링 솔루션의 이전 버전 관련 정보는 어디서 확인할 수 있나요?

원격 모니터링 솔루션 가속기 이전 버전의 명칭은 IoT Suite Remote Monitoring 미리 구성된 솔루션이었습니다. [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)에서 보관된 설명서를 확인할 수 있습니다.

### <a name="next-steps"></a>다음 단계

IoT 솔루션 가속기의 몇 가지 다른 기능을 탐색할 수도 있습니다.

* [원격 모니터링 솔루션 가속기의 기능 탐색](quickstart-remote-monitoring-deploy.md)
* [예측 유지 관리 솔루션 가속기 개요](iot-accelerators-predictive-overview.md)
* [연결된 팩터리 솔루션 가속기 배포](quickstart-connected-factory-deploy.md)
* [처음부터 IoT 보안을 고려](/azure/iot-fundamentals/iot-security-ground-up)
