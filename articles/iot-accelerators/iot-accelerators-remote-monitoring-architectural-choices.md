---
title: 원격 모니터링 솔루션 아키텍처 선택 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링에서 이루어지는 아키텍처 및 기술 선택을 설명합니다.
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 1bd08596a30db7322a72b4269fddfe0b9df19119
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447187"
---
# <a name="remote-monitoring-architectural-choices"></a>원격 모니터링 아키텍처 선택

Azure IoT 원격 모니터링 솔루션 가속기는 오픈 소스, MIT 사용이 허가된 솔루션 가속기입니다. 이 기능은 IoT 개발 프로세스의 속도를 높이기 위한 다음과 같은 일반적인 IoT 시나리오를 보여 줍니다.

- 디바이스 연결
- 디바이스 관리
- 스트림 처리

원격 모니터링 솔루션은 권장되는 [Azure IoT 참조 아키텍처](https://aka.ms/iotrefarchitecture)를 따릅니다.

이 문서에서는 각 원격 모니터링 하위 시스템의 핵심 아키텍처 및 기술 옵션에 대해 설명합니다. 그렇지만 원격 모니터링 솔루션에서 선택하는 Microsoft 기술은 원격 모니터링 IoT 솔루션을 구현하는 유일한 방법은 아닙니다. 이러한 기술 구현을 성공적인 애플리케이션 빌드를 위한 기준으로 사용하고 다음과 같이 수정해야 합니다.

- 조직의 사용 가능한 기술 및 환경에 맞아야 합니다.
- 수직적 애플리케이션 요구를 충족해야 합니다.

## <a name="architectural-choices"></a>아키텍처 선택

IoT 애플리케이션에 대해 Microsoft에서 권장하는 아키텍처는 클라우드 네이티브, 마이크로 서비스 및 서버리스 기반입니다. IoT 애플리케이션의 여러 다른 하위 시스템을 별도로 배포하고 크기를 조정할 수 있는 별도 서비스로 빌드해야 합니다. 이러한 특성 덕분에 더 크게 확장할 수 있고, 개별 하위 시스템 업데이트 시 유연성이 높아지며, 하위 시스템에 대해 적합한 기술을 선택할 수 있는 융통성이 제공됩니다.

둘 이상의 기술을 사용하여 마이크로 서비스를 구현할 수 있습니다. 예를 들어, 다음 옵션 중 하나를 선택하여 마이크로 서비스를 구현할 수 있습니다.

- Azure Functions와 같은 서버리스 기술과 함께 Docker와 같은 컨테이너 기술을 사용합니다.
- Azure App Services와 같은 PaaS 서비스에서 마이크로 서비스를 호스트합니다.

## <a name="technology-choices"></a>기술 선택

이 섹션에서는 각 핵심 하위 시스템에 대한 원격 모니터링 솔루션의 기술 선택 사항에 대해 자세히 설명합니다.

![코어 다이어그램](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>클라우드 게이트웨이

Azure IoT Hub는 원격 모니터링 솔루션 클라우드 게이트웨이로 사용됩니다. [IoT Hub](https://azure.microsoft.com/services/iot-hub/)는 디바이스와의 안전한 양방향 통신을 제공합니다.

IoT 디바이스 연결을 위해 다음을 사용할 수 있습니다.

- [IoT Hub 디바이스 SDK](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks): 디바이스에 대한 기본 클라이언트 애플리케이션을 구현합니다. SDK는 IoT Hub REST API에 대한 래퍼를 제공하고 다시 시도 같은 시나리오를 처리합니다.
- Azure IoT Edge와 통합하여 디바이스의 컨테이너에서 실행되는 사용자 지정 모듈을 배포 및 관리합니다.
- IoT Hub의 자동 디바이스 관리와 통합하여 연결된 디바이스를 대량으로 관리합니다.

### <a name="stream-processing"></a>스트림 처리

스트림 처리를 위해 원격 모니터링 솔루션은 Azure Stream Analytics를 사용하여 복잡한 규칙을 처리합니다. 단순한 규칙을 원하는 경우 기본 제공 배포에 속하지 않더라도 단순한 규칙 처리가 지원되는 사용자 지정 마이크로 서비스를 사용할 수 있습니다. 참조 아키텍처에서는 간단한 규칙 처리에 Azure Functions를 사용하고 복잡한 규칙 처리에는 Azure Stream Analytics를 사용하는 것을 권장합니다.

### <a name="storage"></a>Storage

저장소의 경우 원격 모니터링 솔루션 가속기는 Azure Time Series Insights 및 Azure Cosmos DB를 모두 사용합니다. Azure Time Series Insights는 연결된 디바이스에서 IoT Hub를 통해 들어오는 메시지를 저장합니다. 솔루션 가속기는 Azure Cosmos DB를 콜드 스토리지, 규칙 정의, 경고 및 구성 설정과 같은 다른 모든 스토리지에 사용합니다.

Azure Cosmos DB는 IoT 애플리케이션에 권장되는 범용 웜 스토리지 솔루션입니다. 그러나 Azure Time Series Insights 및 Azure Data Lake와 같은 솔루션이 여러 사용 사례에 적합합니다. Azure Time Series Insights를 사용하면 추세와 이상 현상을 파악하여 시계열 센서 데이터에 대한 심층적 인사이트를 얻을 수 있습니다. 이를 통해 근본 원인을 분석하고 비용이 많이 드는 가동 중지 시간을 방지할 수 있습니다.

> [!NOTE]
> Time Series Insights는 현재 Azure 중국 클라우드에서 사용할 수 없습니다. Azure China 클라우드에서 새 원격 모니터링 솔루션 가속기를 배포하는 경우 모든 저장소에 Cosmos DB를 사용합니다.

### <a name="business-integration"></a>비즈니스 통합

원격 모니터링 솔루션의 비즈니스 통합은 웜 스토리지에 배치되는 경고 생성으로 제한됩니다. 심층적인 비즈니스 통합 시나리오를 구현하려면 솔루션을 Azure Logic Apps에 연결합니다.

### <a name="user-interface"></a>사용자 인터페이스

웹 UI는 JavaScript React로 빌드됩니다. React는 자주 사용되는 업계 웹 UI 프레임워크를 제공하고 Angular와 같은 다른 인기 있는 프레임워크와 비슷합니다.

### <a name="runtime-and-orchestration"></a>런타임 및 오케스트레이션

원격 모니터링 솔루션은 Docker 컨테이너를 사용하여 Kubernetes가 있는 하위 시스템을 수평적 확장을 위한 오케스트레이터로 실행합니다. 이 아키텍처를 사용하면 각 하위 시스템에 대해 별도로 크기 조정을 정의할 수 있습니다. 그러나 이 아키텍처에서 가상 머신과 컨테이너를 최신 상태로 안전하게 유지하려면 DevOps 비용이 발생합니다.

Docker 대신, Azure App Service와 같은 PaaS 서비스에서 마이크로 서비스를 호스트할 수 있습니다. Kubernetes의 경우는 Service Fabric, DC/OS 또는 Swarm과 같은 오케스트레이터를 대신 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [여기](https://www.azureiotsolutions.com/)에서 원격 모니터링 솔루션을 배포합니다.
* [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) 및 [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/)에서 GitHub 코드를 탐색합니다.  
* [여기](https://aka.ms/iotrefarchitecture)에서 IoT 참조 아키텍처에 대해 자세히 알아봅니다.
