---
title: Defender for IoT 자주 묻는 질문
description: Azure Defender for IoT 기능 및 서비스에 대해 가장 자주 묻는 질문에 대한 답변을 찾습니다.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 0ce8ded3eea344d72679e0f8b805f45b00279b58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778596"
---
# <a name="azure-defender-for-iot-frequently-asked-questions"></a>Azure Defender for IoT자주 묻는 질문

이 문서에서는 Defender for IoT에 대한 질문과 대답 목록을 제공합니다.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>IoT 보안을 위한 Azure의 고유한 가치 제안이란 무엇인가요?

Defender for IoT를 통해 엔터프라이즈는 기존 사이버 보안 보기를 전체 IoT 솔루션으로 확장할 수 있습니다. Azure는 비즈니스 솔루션의 종단 간 보기를 제공하여 엔터프라이즈 보안 상태 및 수집된 데이터를 기준으로 비즈니스 관련 작업 및 의사 결정을 수행할 수 있도록 합니다. Azure IoT, Azure IoT Edge 및 Azure Security Center를 사용하여 결합된 보안을 통해 필요한 보안을 제공하는 원하는 솔루션을 만들 수 있습니다.

## <a name="our-organization-uses-proprietary-non-standard-industrial-protocols-are-they-supported"></a>조직에서는 재산적 가치를 가지는 비표준 산업용 프로토콜을 사용합니다. 지원되나요? 

Azure Defender for IoT는 포괄적인 프로토콜 지원을 제공합니다. 포함된 프로토콜 지원과 더불어, 재산적 가치를 가지며 사용자 지정 프로토콜 또는 표준에서 벗어난 프로토콜을 실행하는 IoT 및 OT 디바이스를 보호할 수 있습니다. 개발자는 Horizon ODE(개방형 개발 환경) SDK를 사용하여 정의된 프로토콜을 기반으로 네트워크 트래픽을 디코딩하는 디섹터 플러그 인을 만들 수 있습니다. 서비스는 트래픽을 분석하여 완전한 모니터링, 경고, 보고 기능을 제공합니다. 다음 상황에서 Horizon을 사용합니다.
- 새 버전으로 업그레이드할 필요 없이 가시성 및 제어 능력을 확장합니다.
- 외부 플러그 인으로 사이트를 개발하여 독점 정보를 보호합니다. 
- 경고, 이벤트 및 프로토콜 매개 변수에 대한 텍스트를 지역화합니다.

프로토콜을 플러그 인으로 개발하기 위한 전용 솔루션에는 새 프로토콜을 지원하기 위해 전담 개발자 팀 또는 버전 릴리스가 필요하지 않습니다. 개발자, 파트너 및 고객은 Horizon을 사용하여 프로토콜을 안전하게 개발하고 인사이트와 지식을 공유할 수 있습니다. 

## <a name="do-i-have-to-purchase-hardware-appliances-from-microsoft-partners"></a>Microsoft 파트너로부터 하드웨어 어플라이언스를 구매해야 하나요?
Azure Defender for IoT 센서는 [하드웨어 사양 가이드](./how-to-identify-required-appliances.md)에 설명된 대로 특정 하드웨어 사양에서 실행되며, 고객이 Microsoft 파트너로부터 인증된 하드웨어를 구입하거나 제공된 자료(BOM)를 사용하여 직접 구매할 수 있습니다. 

인증된 하드웨어는 드라이버 안정성, 패킷 드롭 및 네트워크 크기 조정에 대해 연구소에서 테스트되었습니다.


## <a name="regulation-does-not-allow-us-to-connect-our-system-to-the-internet-can-we-still-utilize-defender-for-iot"></a>규정에 따라 시스템을 인터넷에 연결할 수 없습니다. Defender for IoT를 계속 사용할 수 있나요?

예, 하실 수 있습니다. Azure Defender for IoT 플랫폼 온-프레미스 솔루션은 네트워크 트래픽(SPAN, RSPAN 또는 TAP을 통해)을 수동적으로 수집하여, IT, OT 및 IoT 네트워크를 분석, 검색 및 지속적으로 모니터링하는 물리적 또는 가상 센서 어플라이언스로 배포됩니다. 대기업의 경우 여러 센서가 데이터를 온-프레미스 관리 콘솔로 집계할 수 있습니다.

## <a name="where-in-the-network-should-i-connect-monitoring-ports"></a>네트워크에서 모니터링 포트를 연결해야 하는 위치는 어디인가요?

Azure Defender for IoT 센서는 SPAN 포트 또는 네트워크 TAP에 연결하고, 수동(에이전트 없는) 모니터링을 통해 ICS 네트워크 트래픽 수집을 즉시 시작합니다. 데이터 경로에 배치되지 않고 OT 디바이스를 적극적으로 검사하지 않으므로 OT 네트워크에 어떠한 영향도 주지 않습니다.

예를 들면 다음과 같습니다.
- 단일 어플라이언스(물리적 가상)는 작업 현장 DMZ 레이어에 있을 수 있으며 모든 작업 현장 셀 트래픽이 이 레이어로 라우팅됩니다.
- 또는 작업 현장 DMZ 레이어에 상주할 클라우드 또는 로컬 관리를 사용하여 각 작업 현장 셀에서 소형 미니 센서를 찾습니다. 다른 어플라이언스(가상 또는 물리적)는 작업 현장 DMZ 레이어 (SCADA, Historian 또는 MES의 경우)에서 트래픽을 모니터링할 수 있습니다.

## <a name="how-does-defender-for-iot-compare-to-the-competition"></a>Defender for IoT를 경쟁 제품과 비교하면 어떤가요?

Azure Defender for IoT는 모든 IoT/OT 디바이스에서 포괄적인 보안을 제공합니다. **최종 사용자 조직** 의 경우, Azure Defender for IoT는 에이전트 없이 빠르게 배포되는 네트워크 레이어 보안을 제공하고 다양한 재산적 가치를 가지는 OT 장비 및 레거시 Windows 시스템과 함께 작동하며 Azure Sentinel 및 기타 SOC 도구와 상호 운용됩니다. 온-프레미스 또는 Azure 연결 환경에 배포할 수 있습니다. **IoT 디바이스 작성기** 의 경우, Azure Defender for IoT는 새로운 IoT/OT 이니셔티브에 디바이스 레이어 보안을 포함할 수 있는 경량 에이전트를 제공합니다.

## <a name="do-i-have-to-be-an-azure-customer"></a>Azure 고객이 되어야 하나요?

아니요, 에이전트 없는 Azure Defender for IoT 버전의 경우 Azure 고객이 아니어도 됩니다. 그러나 Azure Sentinel에 경고를 보내려는 경우, 클라우드에서 네트워크 센서를 프로비전하고 상태를 모니터링하며, 자동 소프트웨어 및 위협 인텔리전스 업데이트의 이점을 누리려면 Azure IoT Hub를 통해 센서를 Azure에 연결해야 합니다.

Azure Defender for IoT의 에이전트 기반 버전의 경우 Azure 고객이어야 합니다.

## <a name="can-i-create-my-own-alerts"></a>경고를 직접 만들 수 있나요?

예, IP/MAC 주소, 프로토콜 형식, 클래스, 서비스, 기능, 명령 등의 여러 매개 변수와 페이로드에 포함된 사용자 지정 태그의 값을 기반으로 사용자 지정 경고를 만들 수 있습니다.  사용자 지정 경고에 대한 자세한 내용과 사용자 지정 경고를 만드는 방법에 대한 자세한 내용은 [사용자 지정 경고 만들기](quickstart-create-custom-alerts.md)를 참조하세요.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>인터넷 연결 작동이 중지되면 어떻게 되나요?

센서와 에이전트는 디바이스를 실행하는 동안 계속해서 데이터를 실행하고 저장합니다. 데이터는 크기 구성에 따라 보안 메시지 캐시에 저장됩니다. 디바이스가 다시 연결되면 보안 메시지 보내기가 다시 시작됩니다.

## <a name="next-steps"></a>다음 단계

Defender for IoT를 시작하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- IoT용 Defender [개요](overview.md) 읽기
- [시스템 사전 요구 사항](quickstart-system-prerequisites.md) 확인
- [IoT용 Defender를 시작](getting-started.md)하는 방법에 대한 자세한 정보
- [IoT 보안 경고용 Defender](concept-security-alerts.md) 확인