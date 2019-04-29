---
title: 연결된 팩터리 솔루션 연습 - Azure | Microsoft Docs
description: Azure IoT 솔루션 가속기 연결된 팩터리 및 해당 아키텍처에 대한 설명입니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 950d248d2525f053981c8642ee2d39021b9a0494
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449921"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>연결된 팩터리 솔루션 가속기 연습

연결된 팩터리 [솔루션 가속기][lnk-preconfigured-solutions]는 다음을 수행하는 종단 간 산업 솔루션의 구현입니다.

* 시뮬레이션된 공장 생산 라인에서 OPC UA 서버를 실행하는 시뮬레이션된 산업 디바이스 및 실제 OPC UA 서버 디바이스에 연결합니다. OPC UA에 대한 자세한 내용은 [연결된 팩터리 FAQ](iot-accelerators-faq-cf.md)를 참조하세요.
* 이러한 디바이스 및 생산 라인의 운영 KPI 및 OEE를 보여 줍니다.
* OPC UA 서버 시스템과 상호 작용하는 데 클라우드 기반 애플리케이션을 사용하는 방법을 보여 줍니다.
* 고유한 OPC UA 서버 디바이스를 연결할 수 있습니다.
* OPC UA 서버 데이터를 검색하고 수정할 수 있습니다.
* Azure TSI(Time Series Insights) 서비스와 통합하여 OPC UA 서버에서 데이터의 사용자 지정된 보기를 제공합니다.

솔루션을 고유한 구현을 위한 출발점으로 사용하고 사용자의 특정 비즈니스 요구 사항을 충족하도록 [사용자 지정][lnk-customize]할 수 있습니다.

이 문서는 작동 방식을 이해할 수 있도록 연결된 팩터리 솔루션의 핵심 요소 중 일부를 안내합니다. 문서에서는 솔루션을 통해 데이터 흐름 방식도 설명합니다. 이 정보는 다음 항목을 도울 수 있습니다.

* 솔루션의 문제를 해결합니다.
* 솔루션을 사용자 지정하여 고유한 특정 요구 사항을 충족하는 방법을 계획합니다.
* Azure 서비스를 사용하는 고유한 IoT 솔루션을 디자인합니다.

OPC UA에 대한 자세한 내용은 [연결된 팩터리 FAQ](iot-accelerators-faq-cf.md)를 참조하세요.

## <a name="logical-architecture"></a>논리 아키텍처

다음 다이어그램에서는 솔루션 가속기의 논리적 구성 요소를 간략히 보여 줍니다.

![연결된 팩터리 논리 아키텍처][connected-factory-logical]

## <a name="communication-patterns"></a>통신 패턴

이 솔루션은 [OPC UA Pub/Sub 사양](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/)을 사용하여 OPC UA 원격 분석 데이터를 IoT Hub에 JSON 형식으로 보냅니다. 이 솔루션은 이 목표를 달성하기 위해 [OPC 게시자](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge 모듈을 사용합니다.

또한 이 솔루션은 온-프레미스 OPC UA 서버와의 연결을 설정할 수 있는 OPC UA 클라이언트가 웹 애플리케이션과 통합되었습니다. 이 클라이언트는 [역방향 프록시](https://wikipedia.org/wiki/Reverse_proxy)를 사용하며 IoT Hub의 도움을 받아 온-프레미스 방화벽에서 포트를 열지 않고도 연결을 만들 수 있습니다. 이 통신 패턴을 서비스 지원 통신이라고 합니다. 이 솔루션은 이 목표를 달성하기 위해 [OPC 프록시](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge 모듈을 사용합니다.


## <a name="simulation"></a>시뮬레이션

시뮬레이션된 스테이션과 시뮬레이션된 MES(제조 실행 시스템)는 공장 생산 라인을 구성합니다. 시뮬레이션된 디바이스 및 OPC 게시자 모듈은 OPC Foundation에서 게시한 [OPC UA .NET 표준][lnk-OPC-UA-NET-Standard]을 기반으로 합니다.

OPC 프록시 및 OPC 게시자는 [Azure IoT Edge][lnk-Azure-IoT-Gateway]를 기반으로 하는 모듈로 구현됩니다. 각 시뮬레이션된 생산 라인에는 게이트웨이가 연결되었습니다.

모든 시뮬레이션 구성 요소는 Azure Linux VM에서 호스팅되는 Docker 컨테이너에서 실행됩니다. 시뮬레이션은 기본적으로 8개의 시뮬레이션된 생산 라인을 실행하도록 구성됩니다.

## <a name="simulated-production-line"></a>시뮬레이션된 생산 라인

생산 라인 제조 파트. 어셈블리 스테이션, 테스트 스테이션 및 패키징 스테이션과 같은 다른 스테이션으로 구성됩니다.

시뮬레이션은 OPC UA 노드를 통해 사용 가능한 데이터를 실행 및 업데이트합니다. 모든 시뮬레이션된 생산 라인 스테이션은 OPC UA를 통해 MES에 의해 오케스트레이션됩니다.

## <a name="simulated-manufacturing-execution-system"></a>시뮬레이션된 제조 실행 시스템

MES는 스테이션 상태 변경 내용을 검색하는 OPC UA를 통해 생산 라인의 각 스테이션을 모니터링합니다. 스테이션을 제어하는 OPC UA 메서드를 호출하고 완료될 때까지 한 스테이션에서 다음으로 제품을 전달합니다.

## <a name="gateway-opc-publisher-module"></a>게이트웨이 OPC 게시자 모듈

OPC 게시자 모듈은 스테이션 OPC UA 서버에 연결하고 게시될 OPC 노드를 구독합니다. 이 모듈은 다음을 수행합니다.

1. 노드 데이터를 JSON 형식으로 변환합니다.
1. JSON을 암호화합니다.
1. JSON IoT Hub로 보내는으로 OPC UA pub/sub 메시지입니다.

OPC 게시자 모듈에는 아웃바운드 https 포트(443)만 필요하며 기존 엔터프라이즈 인프라로 작업할 수 있습니다.

## <a name="gateway-opc-proxy-module"></a>게이트웨이 OPC 프록시 모듈

게이트웨이 OPC UA 프록시 모듈은 이진 OPC UA 명령 및 제어 메시지를 터널링하고 아웃바운드 https 포트(443)만을 필요로 합니다. 웹 프록시를 포함하여 기존 엔터프라이즈 인프라로 작업할 수 있습니다.

IoT Hub 디바이스 메서드를 사용하여 애플리케이션 계층에서 패킷으로 나누어진 TCP/IP 데이터를 전송하므로 엔드포인트 신뢰, 데이터 암호화 및 SSL/TLS를 사용하여 무결성을 보장합니다.

프록시 자체를 통해 릴레이된 OPC UA 이진 프로토콜은 UA 인증 및 암호화를 사용합니다.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

게이트웨이 OPC 게시자 모듈은 OPC UA 서버 노드를 구독하여 데이터 값에서 변경을 감지합니다. 노드 중 하나에서 데이터 변경이 감지되면 이 모듈은 Azure IoT Hub로 메시지를 보냅니다.

IoT Hub는 Azure TSI에 이벤트 원본을 제공합니다. TSI는 메시지에 연결된 타임스탬프에 따라 30일 동안 데이터를 저장합니다. 이 데이터에는 다음이 포함됩니다.

* OPC UA ApplicationUri
* OPC UA NodeId
* 노드 값
* 원본 타임스탬프
* OPC UA DisplayName

현재 TSI는 데이터를 유지하려는 기간에 대한 고객의 사용자 지정을 허용하지 않습니다.

TSI는 시간 기반 **SearchSpan**을 사용하여 노드 데이터에 대해 쿼리하고 **OPC UA ApplicationUri**, **OPC UA NodeId** 또는 **OPC UA DisplayName**으로 집계합니다.

OEE 및 KPI 계기 및 시간열 차트에 대한 데이터를 검색하기 위해 이 솔루션은 이벤트, **Sum**, **Avg**, **Min** 및 **Max**의 개수로 데이터를 집계합니다.

시계열은 다른 프로세스를 사용하여 빌드됩니다. 이 솔루션은 스테이션 기반 데이터의 OEE 및 KPI 값을 계산하고 프로덕션 라인, 공장 및 기업에 맞게 가치를 전달합니다.

또한 OEE 및 KPI 토폴로지에 대한 시계열은 표시된 timespan이 준비될 때마다 앱에서 계산됩니다. 예를 들어 일 보기는 1시간마다 업데이트됩니다.

노드 데이터의 시계열 보기는 timespan에 대한 집계를 사용하여 TSI에서 직접 제공됩니다.

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-IoT Hub]는 OPC 게시자 모듈에서 클라우드로 전송된 데이터를 수신하고 Azure TSI 서비스에 사용할 수 있도록 합니다. 

또한 솔루션에서 IoT Hub는:
- 모든 OPC 게시자 모듈 및 모든 OPC 프록시 모듈에 대한 ID를 저장하는 ID 레지스트리를 유지 관리합니다.
- OPC 프록시 모듈의 양방향 통신에 대한 전송 채널로 사용됩니다.

## <a name="azure-storage"></a>Azure Storage
클라이언트가 File Storage에 액세스하면 사용되는 SMB 버전은 운영 체제에서 지원하는 SMB 버전에 따라 달라집니다.

## <a name="web-app"></a>웹 앱
솔루션 가속기의 일부로 배포된 웹앱은 통합된 OPC UA 클라이언트, 경고 처리 및 원격 분석 시각화를 포함합니다.

## <a name="telemetry-data-flow"></a>원격 분석 데이터 흐름

![원격 분석 데이터 흐름](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>흐름 단계

1. OPC 게시자는 로컬 인증서 저장소에서 필수 OPC UA X509 인증서 및 IoT Hub 보안 자격 증명을 읽습니다.
    - 필요한 경우 OPC 게시자는 인증서 저장소에서 누락된 인증서 또는 자격 증명을 만들고 저장합니다.

2. OPC 게시자는 IoT Hub에 등록됩니다.
    - 구성된 프로토콜을 사용합니다. IoT Hub 클라이언트 SDK 지원 프로토콜을 사용할 수 있습니다. 기본값은 MQTT입니다.
    - 프로토콜 통신은 TLS로 보호됩니다.

3. OPC 게시자는 구성 파일을 읽습니다.

4. OPC 게시자는 구성된 각 OPC UA 서버에서 OPC 세션을 만듭니다.
    - TCP 연결을 사용합니다.
    - OPC 게시자와 OPC UA 서버는 X509 인증서를 사용하여 서로 인증합니다.
    - 모든 추가 OPC UA 트래픽은 구성된 OPC UA 암호화 메커니즘에 의해 암호화됩니다.
    - OPC 게시자는 구성된 각 게시 간격에 대한 OPC 세션에서 OPC 구독을 만듭니다.
    - OPC 구독에서 게시할 OPC 노드에 대해 OPC 모니터링 항목을 만듭니다.

5. 모니터링된 OPC 노드 값이 변경되면 OPC UA 서버는 OPC 게시자에 업데이트를 보냅니다.

6. OPC 게시자는 새 값의 코드를 변환합니다.
    - 일괄 처리를 사용하는 경우 여러 변경 내용을 일괄 처리합니다.
    - IoT Hub 메시지를 만듭니다.

7. OPC 게시자는 IoT Hub로 메시지를 보냅니다.
    - 구성된 프로토콜을 사용합니다.
    - 통신은 TLS로 보호됩니다.

8. TSI(Time Series Insights)는 IoT Hub에서 메시지를 읽습니다.
    - TCP/TLS에 AMQP를 사용합니다.
    - 이 단계는 데이터 센터의 내부 절차입니다.

9. TSI의 미사용 데이터입니다.

10. Azure App Service 쿼리에서 연결된 팩터리 WebApp에는 TSI의 데이터가 필요합니다.
    - TCP/TLS 보호 통신을 사용합니다.
    - 이 단계는 데이터 센터의 내부 절차입니다.

11. 웹 브라우저는 연결된 팩터리 WebApp에 연결됩니다.
    - 연결된 팩터리 대시보드를 표시합니다.
    - HTTPS를 통해 연결합니다.
    - 연결된 팩터리 앱에 액세스하려면 Azure Active Directory를 통한 사용자의 인증이 필요합니다.
    - 연결된 팩터리 앱에 대한 WebApi 호출은 위조 방지 토큰에 의해 보호됩니다.

12. 데이터 업데이트에서 연결된 팩터리 WebApp은 업데이트된 데이터를 웹 브라우저에 보냅니다.
    - SignalR 프로토콜을 사용합니다.
    - TCP/TLS로 보호됩니다.

## <a name="browsing-data-flow"></a>데이터 흐름 검색

![데이터 흐름 검색](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>흐름 단계

1. OPC 프록시(서버 구성 요소)를 시작합니다.
    - 로컬 저장소에서 공유 선택키를 읽습니다.
    - 필요한 경우 누락된 선택키를 저장소에 저장합니다.

2. OPC 프록시(서버 구성 요소)는 IoT Hub에 등록됩니다.
    - IoT Hub에서 알려진 디바이스를 모두 읽습니다.
    - 소켓 또는 보안 WebSocket을 통해 TLS에서 MQTT를 사용합니다.

3. 웹 브라우저는 연결된 팩터리 WebApp에 연결하고 연결된 팩터리 대시보드를 표시합니다.
    - HTTPS를 사용합니다.
    - 사용자는 연결할 OPC UA 서버를 선택합니다.

4. 연결된 팩터리 WebApp은 선택한 OPC UA 서버에 대한 OPC UA 세션을 설정합니다.
    - OPC UA 스택을 사용합니다.

5. OPC 프록시 전송은 OPC UA 서버에 대한 TCP 소켓 연결을 설정하기 위해 OPC UA 스택에서 요청을 받습니다.
    - TCP 페이로드를 검색하고 그대로 사용합니다.
    - 이 단계는 연결된 팩터리 WebApp의 내부 절차입니다.

6. OPC 프록시(클라이언트 구성 요소)는 IoT Hub 디바이스 레지스트리에서 OPC 프록시(서버 구성 요소) 디바이스를 조회합니다. 그런 다음 IoT Hub에서 OPC 프록시(서버 구성 요소) 디바이스의 디바이스 메서드를 호출합니다.
    - TCP/TLS에 HTTPS를 사용하여 OPC 프록시를 조회합니다.
    - TCP/TLS에 HTTPS를 사용하여 OPC UA 서버에 대한 TCP 소켓 연결을 설정합니다.
    - 이 단계는 데이터 센터의 내부 절차입니다.

7. IoT Hub는 OPC 프록시(서버 구성 요소) 디바이스에서 디바이스 메서드를 호출합니다.
    - 소켓 또는 보안 Websocket 연결을 통해 TLS에 설정된 MQTT를 사용하여 OPC UA 서버에 대한 TCP 소켓 연결을 설정합니다.

8. OPC 프록시(서버 구성 요소)는 작업 현장 네트워크에 TCP 페이로드를 보냅니다.

9. OPC UA 서버는 페이로드를 처리하고 응답을 다시 보냅니다.

10. OPC 프록시(서버 구성 요소)의 소켓에서 응답을 수신합니다.
    - OPC 프록시는 데이터를 디바이스 메서드의 반환 값으로 IoT Hub 및 OPC 프록시(클라이언트 구성 요소)에 보냅니다.
    - 이 데이터는 연결된 팩터리 앱에 있는 OPC UA 스택에 전달됩니다.

11. 연결된 팩터리 WebApp은 OPC UA 서버에서 받은 OPC UA 관련 정보를 포함한 OPC 브라우저 UX를 렌더링할 웹 브라우저에 반환합니다.
    - 사용자가 OPC 주소 공간을 검색하고 기능을 OPC 주소 공간의 노드에 적용하는 동안 OPC 브라우저 UX 클라이언트에서는 위조 방지 토큰으로 보호된 HTTPS를 통한 AJAX 호출을 사용하여 연결된 팩터리 WebApp에서 데이터를 가져옵니다.
    - 필요한 경우 클라이언트는 4~10단계에서 설명한 통신을 사용하여 OPC UA 서버와 정보를 교환합니다.

> [!NOTE]
> OPC 프록시(서버 구성 요소) 및 OPC 프록시(클라이언트 구성 요소)는 OPC UA 통신 관련된 모든 TCP 트래픽에 대한 #4~#10단계를 완료합니다.

> [!NOTE]
> 연결된 팩터리 WebApp 내에 있는 OPC UA 서버 및 OPC UA 스택의 경우 OPC 프록시 통신이 투명하며, 인증 및 암호화에 대한 모든 OPC UA 보안 기능이 적용됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 IoT 솔루션 가속기 시작 작업을 계속할 수 있습니다.

* [Azureiotsolutions.com 사이트에 대 한 권한][lnk-permissions]
* [연결된 팩터리 솔루션 가속기를 위해 Windows 또는 Linux에 게이트웨이 배포](iot-accelerators-connected-factory-gateway-deployment.md)
* [OPC 게시자 참조 구현](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md)

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
