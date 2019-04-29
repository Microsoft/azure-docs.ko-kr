---
title: Azure Security Center에 대 한 질문과 대답 IoT 미리 보기 | Microsoft Docs
description: 가장 자주 묻는 질문 대답 Azure Security Center에 대 한 IoT 기능 및 서비스를 찾습니다.
services: asc-for-iot
ms.service: ASCforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 97fda6c2-1ecb-491f-b48d-41788bd7e0d3
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: cd9e77e09e2a2fd5fe1108e98da028d26fc623c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60803109"
---
# <a name="azure-security-center-for-iot-frequently-asked-questions"></a>IoT 용 azure Security Center에 대 한 질문과 대답  

> [!IMPORTANT]
> IoT용 Azure Security Center는 현재 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 IoT에 대 한 자주 묻는 질문 및 답변에 대 한 Azure 보안 센터 (ASC)의 목록을 제공 합니다. 

## <a name="does-azure-provide-support-for-iot-security"></a>Azure는 IoT 보안에 대 한 지원을 제공 하나요?

Azure 모니터링 및 Azure Security Center를 통해 전체적인 보안 솔루션의 일부로 IoT 보안을 관리 하기 위한 통합 된 보기를 제공 합니다. 응용 프로그램 개발자 인 경우에 IoT 응용 프로그램 보안을 관리 하려면 IoT Hub 보기를 사용할 수 있습니다.

## <a name="what-is-azures-unique-value-proposition-for-iot-security"></a>IoT 보안에 대 한 Azure의 고유한 가치 제안을 란?

ASC IoT에 대 한 기존 사이버 보안 뷰를 통해 전체 IoT 솔루션을 확장 해 주는 합니다. Azure는 비즈니스와 관련 된 작업을 수행할 수 있도록 비즈니스 솔루션의 종단 간 보기를 제공 및 의사 결정 엔터프라이즈 보안 태세를 기반으로 하며 데이터를 수집 합니다. Azure IoT, Azure IoT Edge, Azure Sphere, 중부 Azure를 사용 하 여 보안을 결합 하 고 Azure Security Center를 사용 하 여 필요한 보안 기능을 사용 하 여 원하는 솔루션을 만들 수 있습니다.

## <a name="who-is-asc-for-iot-made-for"></a>IoT에 대 한에 대 한 ASC는 누구 인가요? 

IoT 용 ASC는가 Azure IoT Hub 보안에 통합 하 고 일상적인 비즈니스 솔루션에 대 한 관리 보안 작업을 제공 합니다. ASC IoT 용 Azure Security Center 기능에도 통합 되어 및 모니터링 하 고 전체적인 보안 솔루션의 일부로 IoT 보안 관리에 대 한 통합된 보기를 제공 합니다.

## <a name="how-does-asc-for-iot-compare-to-the-competition"></a>ASC IoT에 대 한 경쟁을 어떻게 비교 되나요?

고객은 자신의 솔루션을 만들 수 있는 기능 집합을 제공 하는 다른 솔루션, IoT에 대 한 ASC 넓은 보기에서 모든 관련된 Azure 리소스의 보안을 제공 하는 고유한 종단 간 IoT 보안 솔루션을 제공 합니다. Azure 빠른 배포 및 기존 장치 관리 도구와 쉽게 통합에 대 한 IoT Hub 모듈 쌍을 사용 하 여 완벽 한 통합을 사용합니다.

## <a name="do-i-have-to-be-an-azure-security-center-asc-customer"></a>Azure 보안 센터 (ASC) 고객 일 합니까?

아니요, 하지만 것이 좋습니다. ASC 없이 ASC IoT에 대 한 제한 된 연결 된 리소스 데이터를 수신 하 고 잠재적인 공격 노출 영역, 위협 및 잠재적 공격의 제한 된 분석을 제공 합니다. 

## <a name="do-i-have-to-be-an-azure-iot-customer"></a>Azure IoT 고객 일 합니까?

예. ASC IoT 용 Azure IoT 연결 및 인프라에 의존합니다.

## <a name="do-i-have-to-install-an-agent"></a>에이전트를 설치 해야 하나요?

IoT 용 Microsoft ASC를 사용 하도록 설정 하려면 필수 IoT 장치에 에이전트를 설치 하지 않습니다. 모니터링 및 관리 기능 선택에 따라 서로 다른 수준의 보안을 확보 하 고 다음 세 가지 옵션을 선택할 수 있습니다.

1. 수정 없이 IoT 보안 에이전트에 대 한 ASC를 설치 합니다. 이 옵션은 가장 높은 수준의 장치 동작 및 액세스에 대 한 향상 된 보안 정보를 제공 합니다.

2. 직접 에이전트를 만들고 IoT 보안 메시지 스키마에 대 한 Microsoft ASC를 구현 합니다. 이 옵션을 사용 하면 장치 보안 에이전트 기반으로 IoT 분석 도구에 대 한 Microsoft ASC를 사용 합니다.

3. IoT 장치의 보안 에이전트 설치 되지 않습니다. 이 옵션을 사용 하면 축소 된 보안 모니터링 및 관리 기능을 사용 하 여 IoT Hub 통신 모니터링 합니다. 

## <a name="what-does-the-asc-for-iot-agent-do"></a>IoT 에이전트용 ASC의 기능은 무엇입니까?

ASC IoT 에이전트에 대 한 장치 구성, 동작 및 액세스 (구성 검사), 프로세스 및 연결에 대 한 장치 수준 위협 검사를 제공 합니다. 비즈니스 관련 데이터 또는 작업에는 IoT 보안 에이전트에 대 한 ASC 검색 하지 않습니다.

## <a name="where-i-can-get-the-asc-for-iot-security-agent"></a>어디서 IoT 보안 에이전트에 대 한 ASC를 구할 수 있습니까?

IoT 보안 에이전트에 대 한 ASC는 오픈 소스 32 비트 및 64 비트 Windows 및 Linux 버전에서 GitHub에서 사용할 수 있습니다. https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="where-does-the-asc-for-iot-agent-get-installed"></a>ASC IoT 에이전트에 대 한 설치 위치는? 

자세한 설치 및 에이전트 배포 정보는 GitHub에서 찾을 수 있습니다. https://github.com/Azure/Azure-Security-IoT-Preview

## <a name="what-are-the-dependencies-and-prerequisites-of-the-agent"></a>종속성 및 에이전트의 필수 구성 요소는 무엇 인가요?

ASC IoT에 대 한 다양 한 플랫폼을 지원합니다. 참조 [지원 되는 장치 플랫폼](how-to-deploy-agent.md) 특정 장치에 대 한 지원을 확인 합니다. 

## <a name="which-data-is-collected-by-the-agent"></a>에이전트에서 데이터를 수집 하나요?

연결, 액세스, 방화벽 구성, 프로세스 목록 및 OS 기준 에이전트에 의해 수집 됩니다.

## <a name="how-much-data-will-the-agent-generate"></a>에이전트는 얼마나 많은 데이터를 생성?

에이전트 데이터 생성은 장치, 응용 프로그램, 연결 유형 및 사용자 에이전트 구성에 의해 좌우 됩니다. 장치 및 IoT 솔루션 간에 높은 가변성을 인해 첫 번째 랩 또는 테스트 설정을 확인, 배우고, 생성 된 데이터의 크기를 측정 하는 동안 필요에 맞는 특정 구성을 설정 하는 에이전트를 배포 하는 것이 좋습니다. 서비스를 시작한 후 IoT 에이전트용 ASC 구성 및 사용자 지정 프로세스를 사용 하 여 도움이 에이전트 처리량 최적화에 대 한 운영 권장 사항 제공 합니다.

## <a name="how-can-i-control-my-billing"></a>요금 청구는 어떻게 제어할 수 있나요?

ASC IoT에 대 한 검색, 데이터 버퍼 및 늘리거나 에이전트에 의해 생성 된 데이터의 양을 줄일 수 있는 사용자 지정 경고를 생성 하는 기능 구성 가능한 에이전트를 제공 합니다.

## <a name="do-agent-messages-use-up-quota-from-iot-hub"></a>에이전트 메시지를 IoT Hub에서 할당량 사용 합니까?

예. 전송 하는 에이전트 데이터는 IoT Hub 할당량에서 계산 됩니다. 

## <a name="what-next-ive-installed-an-agent-and-dont-see-any-activities-or-logs"></a>다음 단계 에이전트를 설치 했으며이 표시 되지 않는 모든 활동 로그

1. 확인 된 [에이전트 유형에 맞는 장치의 지정 된 OS 플랫폼](how-to-deploy-agent.md)

1. 확인 합니다 [장치에서 에이전트가 실행 되 고](how-to-agent-configuration.md)입니다.

2. 확인 합니다 [서비스가 성공적으로 활성화 되었습니다](quickstart-onboard-iot-hub.md) 하 **보안** IoT Hub에. 

3. 장치 인지 확인 [IoT 모듈에 대 한 ASC 사용 하 여 IoT Hub에 구성 된](quickstart-create-security-twin.md)합니다.  

활동이 나 로그를 계속 사용할 수 없는 경우에 ASC IoT 파트너에 대 한 추가 도움말에 게 문의 합니다.

## <a name="what-happens-when-the-internet-connection-stops-working"></a>인터넷 연결이 작동을 중지 하는 경우 어떻게 되나요?

에이전트가 계속 실행 되 고 장치가 실행 되는 데이터를 저장 합니다. 데이터 크기 구성에 따라 보안 메시지 캐시에 저장 됩니다. 장치 다시 연결, 보안 메시지 보내기를 다시 시작할 합니다. 

## <a name="if-the-device-is-restarted-will-the-security-agent-self-recover"></a>장치를 다시 보안 에이전트 자체 복구 됩니다.

보안 에이전트는 각 장치를 다시 시작을 사용 하 여 자동으로 다시 실행 하도록 설계 되었습니다.

## <a name="can-the-agent-affect-the-performance-of-the-device-or-other-installed-software"></a>에이전트는 장치 또는 다른 설치 된 소프트웨어의 성능 영향을 줄 수 있습니까?

에이전트 컴퓨터 리소스를 소비 하며 모든 다른 응용 프로그램/프로세스로 일반 장치 작업을 방해 하지 않도록 해야 합니다. 해당 설치 및 구성 에이전트를 실행 하는 장치에서 리소스 소비 결합 됩니다. 프로덕션 환경에 배포 하기 전에 다른 IoT 응용 프로그램 및 기능을 사용 하 여 상호 운용성과 함께 포함 된 환경에서 에이전트 구성을 테스트 하는 것이 좋습니다.

## <a name="im-making-some-maintenance-on-the-device-can-i-turn-off-the-agent"></a>일을 할 몇 가지 유지 관리 장치에 있습니다. 에이전트를 해제 하려면 어떻게 할까요?

에이전트를 해제할 수 없습니다.

## <a name="is-there-a-way-to-test-if-the-agent-is-working-correctly"></a>에이전트가 제대로 작동 하는 경우 테스트할 방법이 있습니까? 

에이전트 통신 중지 되거나 보안 메시지를 보내지 못할 경우에 **장치는 자동** 경고가 생성 됩니다.

## <a name="can-i-create-my-own-alerts"></a>내 경고를 만들 수 있나요?

예. IP 주소 및 포트 열기와 같은 동작의 미리 결정된 된 집합에서 사용자 지정된 경고를 설정할 수 있습니다. 참조 [사용자 지정 경고를 만들](quickstart-create-custom-alerts.md) 사용자 지정 경고 및 해당 하는 방법에 자세히 알아보려면 합니다. 

## <a name="where-can-i-see-logs-can-i-customize-logs"></a>로그는 어디서 확인할 수 있나요? 로그를 사용자 지정할 수 있습니까?

- 경고 보기와 연결 된 Log Analytics 작업 영역을 사용 하 여 추천 합니다. 작업 영역에서 저장소 크기 및 기간을 구성 합니다.

- 원시 데이터에 보안 에이전트에서 Log Analytics 계정에도 저장할 수 있습니다. 이 옵션의 구성을 변경 하기 전에 크기, 기간, 저장소 요구 사항과 관련된 비용을 고려 합니다. 

## <a name="why-should-i-add-asc-for-iot-to-the-module-identity-what-is-it-used-for"></a>추가 하는 이유 ASC IoT에 대 한 모듈 id에? 용도는?

ASC IoT 모듈에 대 한 에이전트 구성 및 관리에 사용 됩니다.


## <a name="next-steps"></a>다음 단계

IoT 용 ASC를 사용 하 여 시작 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 합니다.


- ASC IoT에 대 한 읽기 [개요](overview.md)
- 확인 된 [필수 구성 요소 서비스](service-prerequisites.md)
- 하는 방법에 자세히 알아보려면 [시작](getting-started.md)
- 이해 [ASC IoT 보안 경고에 대 한](concept-security-alerts.md)

