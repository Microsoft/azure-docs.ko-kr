---
title: 원격 모니터링 솔루션에 SIM 데이터 통합 - Azure| Microsoft Docs
description: 이 문서에서는 Telefónica SIM 데이터를 원격 모니터링 솔루션에 통합하는 방법을 제공합니다.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442242"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>원격 모니터링 솔루션에 SIM 데이터 통합

IoT 디바이스는 어디에서나 데이터 스트림을 보낼 수 있는 SIM 카드를 사용하여 클라우드에 연결하는 경우가 많습니다. Azure IoT 원격 모니터링 솔루션은 IoT 관리되는 연결 데이터의 통합이 가능하기 때문에 IoT SIM에서 제공되는 데이터를 통해 디바이스 상태를 추적할 수도 있습니다.

원격 모니터링은 Telefónica IoT Connectivity와의 통합을 기본 제공하기 때문에 해당사의 IoT 연결 플랫폼을 사용하는 고객은 자신의 디바이스 SIM 연결 데이터와 솔루션을 동기화할 수 있습니다. 이 솔루션은 GitHub [리포지토리](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)를 통해 다른 IoT 연결 공급자를 지원하도록 확장될 수 있습니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

* Telefónica IoT SIM 데이터를 원격 모니터링 솔루션에 통합
* 실시간 원격 분석 보기
* SIM 데이터 보기

## <a name="telefnica-iot-integration-setup"></a>Telefonica IoT 통합 설정

### <a name="prerequisites"></a>필수 조건

이 추가 원격 모니터링 기능은 현재 미리 보기로 제공됩니다. 연결 데이터를 Azure 원격 모니터링 솔루션에 동기화하려면 다음 단계를 따릅니다.

1. [Telefónica 사이트](https://iot.telefonica.com/contact)에서 요청을 입력하고 연락처 데이터를 포함하여 **Azure Remote Monitoring** 옵션을 선택합니다.
2. Telefónica에서 계정이 활성화됩니다.
3. 아직 Telefónica 고객이 아닌 경우 이 서비스나 다른 IoT Connectivity Cloud Ready 서비스를 사용하려면 [Telefónica 사이트](https://iot.telefonica.com/)에 방문하여 **Connectivity** 옵션을 선택합니다.

### <a name="telefnica-sim-setup"></a>Telefónica SIM 설정
Telefónica SIM과 Azure 쌍 디바이스 ID 연결은 Telefónica IoT SIM "alias" 속성을 기반으로 합니다. 

[Telefónica IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) &gt; SIM Inventory로 이동하여 자신의 SIM을 선택하고 각 SIM의 "alias"를 원하는 쌍 디바이스 ID로 업데이트합니다. 이 작업은 대량 모드로 수행할 수도 있습니다(Telefónica IoT Connectivity Platform 사용자 설명서 참조).

이 작업은 대량 모드로 수행할 수도 있습니다(Telefónica IoT Connectivity Platform 사용자 설명서 참조).

![Telefónica 업데이트](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

디바이스를 원격 모니터링에 연결하려면, [C](iot-accelerators-connecting-devices-linux.md) 또는 [Node](iot-accelerators-connecting-devices-node.md)를 사용하여 자습서를 수행합니다. 

## <a name="view-device-telemetry-and-sim-properties"></a>디바이스 원격 분석 및 SIM 속성 보기

Telefónica 계정이 올바르게 구성되고 디바이스가 연결되면 디바이스 정보와 SIM 데이터를 볼 수 있습니다.

다음 연결 매개 변수가 게시됩니다.

* ICCID
* IP
* 현재 네트워크 상태
* SIM 상태
* 네트워크 기반 위치
* 사용된 데이터 트래픽

![대시보드](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>다음 단계

SIM 데이터를 Azure IoT 원격 모니터링에 통합하는 방법에 대한 개요를 알아보았습니다. 아래는 솔루션 가속화를 위해 제안되는 다음 단계입니다.

* [Azure IoT 원격 모니터링 솔루션 운영](quickstart-remote-monitoring-deploy.md)
* [고급 모니터링 수행](iot-accelerators-remote-monitoring-monitor.md)
* [디바이스 관리](iot-accelerators-remote-monitoring-manage.md)
* [디바이스 문제 해결](iot-accelerators-remote-monitoring-maintain.md)

