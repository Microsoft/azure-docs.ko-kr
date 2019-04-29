---
title: 원격 모니터링 솔루션 가속기 개요 - Azure | Microsoft Docs
description: 원격 모니터링 솔루션 가속기의 개요입니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442550"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>원격 모니터링 솔루션 가속기 개요

원격 모니터링 [솔루션 가속기](../iot-accelerators/about-iot-accelerators.md)는 원격 위치에서 여러 머신에 대한 엔드투엔드 모니터링 솔루션을 구현합니다. 솔루션은 비즈니스 시나리오의 제네릭 구현을 제공하는 핵심 Azure 서비스를 결합합니다. 솔루션을 고유한 구현을 위한 시작점으로 사용하고 사용자의 특정 비즈니스 요구 사항에 맞게 [사용자 지정](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)할 수 있습니다.

이 문서는 작동 방식을 이해할 수 있도록 원격 모니터링 솔루션의 핵심 요소 중 일부를 안내합니다. 이 정보는 다음 항목을 도울 수 있습니다.

* 솔루션의 문제를 해결합니다.
* 솔루션을 사용자 지정하여 고유한 특정 요구 사항을 충족하는 방법을 계획합니다.
* Azure 서비스를 사용하는 고유한 IoT 솔루션을 디자인합니다.

원격 모니터링 솔루션 가속기 코드는 GitHub에서 사용할 수 있습니다.

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>논리 아키텍처

다음 다이어그램은 [IoT 아키텍처](../iot-fundamentals/iot-introduction.md)에 오버레이된 원격 모니터링 솔루션 가속기의 논리적 구성 요소에 대해 설명합니다.

![논리 아키텍처](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>마이크로 서비스를 사용하는 이유는 무엇인가요?

클라우드 아키텍처는 Microsoft가 솔루션 가속기를 처음 발표한 이후 발전해 왔습니다. [마이크로 서비스](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)는 개발 속도를 저하시키지 않으면서 규모와 유연성을 달성하는 입증된 방법으로 부상했습니다. 일부 Microsoft 서비스는 뛰어난 안정성과 확장성을 제공하는 아키텍처 패턴을 내부적으로 사용합니다. 업데이트된 솔루션 가속기를 통해 혜택을 볼 수 있도록 이러한 내용을 실제로 활용할 수 있습니다.

> [!TIP]
> 마이크로서비스 아키텍처에 대한 자세한 내용은 [.NET 애플리케이션 아키텍처](https://www.microsoft.com/net/learn/architecture) 및 [마이크로서비스: 클라우드가 지원하는 애플리케이션 혁명](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/)을 참조하세요.

## <a name="device-connectivity"></a>디바이스 연결

이 솔루션은 논리적 구조의 디바이스 연결 부분에 다음과 같은 구성 요소를 포함합니다.

### <a name="real-devices"></a>실제 디바이스

실제 디바이스는 솔루션에 연결할 수 있습니다. Azure IoT 디바이스 SDK를 사용하여 시뮬레이션된 디바이스의 동작을 구현할 수 있습니다.

실제 디바이스는 솔루션 포털의 대시보드에서 프로비전할 수 있습니다.

### <a name="device-simulation-microservice"></a>디바이스 시뮬레이션 마이크로 서비스

솔루션에는 솔루션 포털에서 시뮬레이션된 디바이스 풀을 관리하여 솔루션의 종단 간 흐름을 테스트할 수 있게 하는 [디바이스 시뮬레이션 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation)가 포함됩니다. 시뮬레이션된 디바이스의 특징:

* 디바이스-클라우드 원격 분석을 생성합니다.
* IoT Hub의 클라우드-장치 메서드 호출에 응답합니다.

마이크로 서비스는 시뮬레이션을 생성, 시작 및 중지할 수 있는 RESTful 엔드포인트를 제공합니다. 각 시뮬레이션은 원격 분석을 보내고 메서드 호출에 응답하는 다양한 유형의 가상 디바이스 집합으로 구성됩니다.

시뮬레이션된 디바이스를 솔루션 포털의 대시보드에서 프로비전할 수 있습니다.

### <a name="iot-hub"></a>IoT Hub

[IoT 허브](../iot-hub/index.yml)는 실제 디바이스 및 시뮬레이션된 디바이스에서 보낸 원격 분석을 수집합니다. IoT 허브를 사용하면 IoT 솔루션 백 엔드의 서비스에서 원격 분석을 사용하여 처리할 수 있습니다.

또한 솔루션에서 IoT Hub는:

* 포털에 연결하도록 허용되는 모든 디바이스의 ID 및 인증 키를 저장하는 ID 레지스트리를 유지 관리합니다.
* 솔루션 가속기를 대신하여 디바이스에서 메서드를 호출합니다.
* 등록된 모든 디바이스에 대한 디바이스 쌍을 유지 관리합니다. 디바이스 쌍은 디바이스에서 보고한 속성 값을 저장합니다. 또한 디바이스 쌍은 다음에 연결할 때 검색하는 디바이스에 대해 솔루션 포털에서 설정한 desired 속성을 저장합니다.
* 여러 디바이스에 대한 속성을 설정하거나 여러 디바이스에서 메서드를 호출하는 작업을 예약합니다.

## <a name="data-processing-and-analytics"></a>데이터 처리 및 분석

이 솔루션은 논리적 구조의 데이터 처리 및 분석 부분에 다음과 같은 구성 요소를 포함합니다.

### <a name="iot-hub-manager-microservice"></a>IoT Hub 관리자 마이크로 서비스

솔루션에는 다음과 같은 IoT Hub와의 상호 작용을 처리하는 [IoT Hub 관리자 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)가 포함됩니다.

* IoT 디바이스 만들기 및 관리.
* 디바이스 쌍 관리.
* 디바이스에 대한 메서드 호출.
* IoT 자격 증명 관리.

또한 이 서비스는 IoT Hub 쿼리를 실행하여 사용자 정의 그룹에 속하는 디바이스를 검색합니다.

마이크로 서비스는 디바이스와 디바이스 쌍을 관리하고 메서드를 호출하며 IoT Hub 쿼리를 실행하는 RESTful 엔드포인트를 제공합니다.

### <a name="device-telemetry-microservice"></a>디바이스 원격 분석 마이크로 서비스

[디바이스 원격 분석 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry)는 Time Series Insights에 저장된 디바이스 원격 분석에 대한 읽기 액세스를 위한 RESTful 엔드포인트를 제공합니다. 또한 RESTful 엔드포인트를 사용하면 규칙의 CRUD 작업 및 저장소의 경보 정의에 대한 읽기/쓰기 액세스를 수행할 수 있습니다.

### <a name="storage-adapter-microservice"></a>저장소 어댑터 마이크로 서비스

[저장소 어댑터 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter)는 키-값 쌍을 관리하고, 저장소 서비스 의미 체계를 추상화하며, Azure Cosmos DB를 사용하여 모든 형식의 데이터를 저장하는 간단한 인터페이스를 제공합니다.

값은 컬렉션으로 구성됩니다. 개별 값에서 작업하거나 전체 컬렉션을 가져올 수 있습니다. 복잡한 데이터 구조는 클라이언트에서 직렬화되고, 간단한 텍스트 페이로드로 관리됩니다.

이 서비스는 키-값 쌍의 CRUD 작업에 대한 RESTful 엔드포인트를 제공합니다. 값

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

솔루션 가속기 배포에서는 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)를 사용하여 규칙, 경고, 구성 설정 및 기타 모든 콜드 스토리지를 저장합니다.

### <a name="azure-stream-analytics-manager-microservice"></a>Azure Stream Analytics 관리자 마이크로 서비스

[Azure Stream Analytics 관리자 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager)는 구성 설정, 시작 및 중지, 상태 모니터링을 포함한 ASA(Azure Stream Analytics) 작업을 관리합니다.

ASA 작업은 두 개의 참조 데이터 집합에서 지원됩니다. 한 데이터 집합은 규칙을 정의하고, 다른 하나는 디바이스 그룹을 정의합니다. 규칙 참조 데이터는 디바이스 원격 분석 마이크로 서비스에서 관리되는 정보로부터 생성됩니다. Azure Stream Analytics 관리자 마이크로 서비스는 원격 분석 규칙을 스트림 처리 논리로 변환합니다.

디바이스 그룹 참조 데이터는 들어오는 원격 분석 메시지에 적용할 규칙 그룹을 식별하는 데 사용됩니다. 디바이스 그룹은 구성 마이크로 서비스에서 관리되며, Azure IoT Hub 디바이스 쌍 쿼리를 사용합니다.

ASA 작업은 저장소 및 분석을 위해 원격 분석을 연결된 디바이스에서 Time Series Insight로 전달합니다.

### <a name="azure-stream-analytics"></a>Azure Stream Analytics

[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)는 디바이스에서 대량으로 스트림되는 데이터를 검사할 수 있는 이벤트 처리 엔진입니다.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/)는 솔루션 가속기에 연결된 디바이스의 원격 분석을 저장합니다. 또한 솔루션 웹 UI에서 디바이스 원격 분석을 시각화하고 쿼리할 수 있습니다.

> [!NOTE]
> Time Series Insights는 현재 Azure 중국 클라우드에서 사용할 수 없습니다. Azure China 클라우드에서 새 원격 모니터링 솔루션 가속기를 배포하는 경우 모든 저장소에 Cosmos DB를 사용합니다.

### <a name="configuration-microservice"></a>구성 마이크로 서비스

[구성 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)는 솔루션 가속기의 디바이스 그룹, 솔루션 설정 및 사용자 설정의 CRUD 작업에 대한 RESTful 엔드포인트를 제공합니다. 저장소 어댑터 마이크로 서비스와 함께 작동하여 구성 데이터를 유지합니다.

### <a name="authentication-and-authorization-microservice"></a>인증 및 권한 부여 마이크로 서비스

[인증 및 권한 부여 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth)는 솔루션 가속기에 액세스하도록 권한이 부여된 사용자를 관리합니다. 사용자 관리는 [OpenId Connect](https://openid.net/connect/)를 지원하는 모든 ID 서비스 공급자를 사용하여 수행할 수 있습니다.

### <a name="azure-active-directory"></a>Azure Active Directory

솔루션 가속기 배포에서는 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)를 OpenID Connect 공급자로 사용합니다. Azure Active Directory는 사용자 정보를 저장하고, JWT 토큰 서명의 유효성을 검사하기 위한 인증서를 제공합니다.

## <a name="presentation"></a>프레젠테이션

이 솔루션은 논리적 구조의 프레젠테이션 부분에 다음과 같은 구성 요소를 포함합니다.

[웹 사용자 인터페이스는 React Javascript 애플리케이션입니다](https://github.com/Azure/pcs-remote-monitoring-webui). 이 애플리케이션은:

* Javascript React만 사용하고 브라우저에서 완전히 실행됩니다.
* CSS 스타일이 적용됩니다.
* AJAX 호출을 통해 공용 마이크로 서비스와 상호 작용합니다.

사용자 인터페이스는 모든 솔루션 가속기 기능을 제공하고, 다음과 같은 다른 마이크로 서비스와 상호 작용합니다.

* 사용자 데이터를 보호하는 인증 및 권한 부여 마이크로 서비스
* IoT 디바이스를 나열하고 관리하는 IoT Hub 관리자 마이크로 서비스

사용자 인터페이스는 Azure Time Series Insights 탐색기를 통합하여 디바이스 원격 분석에 대한 쿼리 및 분석을 수행할 수 있습니다.

구성 마이크로 서비스를 사용하면 사용자 인터페이스로 구성 설정을 저장하고 검색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

소스 코드와 개발자 설명서를 탐색하려면 다음 두 GitHub 리포지토리 중 하나를 시작합니다.

* [Azure IoT(.NET)로 원격 모니터링을 위한 솔루션 가속기](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Azure IoT(Java)로 원격 모니터링을 위한 솔루션 가속기](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

자세한 솔루션 아키텍처 다이어그램:
* [원격 모니터링 아키텍처를 위한 솔루션 가속기](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [솔루션 가속기 사용자 지정](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)을 참조하세요.
