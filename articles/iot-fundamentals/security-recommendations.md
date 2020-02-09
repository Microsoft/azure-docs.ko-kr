---
title: Azure IoT에 대 한 보안 권장 사항 | Microsoft Docs
description: 이 문서에서는 Azure IoT Hub 솔루션의 보안을 유지 하기 위한 추가 단계를 요약 합니다.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048467"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>IoT (Azure 사물 인터넷) 배포에 대 한 보안 권장 사항

이 문서에는 IoT에 대 한 보안 권장 사항이 포함 되어 있습니다. 이러한 권장 사항을 구현 하면 공유 책임 모델에 설명 된 대로 보안 의무를 달성 하는 데 도움이 됩니다. Microsoft에서 서비스 공급자 역할을 수행 하는 데 필요한 사항에 대 한 자세한 내용은 [클라우드 컴퓨팅을 위한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)을 참조 하세요.

이 문서에 포함 된 일부 권장 사항은 Azure Security Center에서 자동으로 모니터링할 수 있습니다. Azure Security Center는 Azure에서 리소스를 보호 하는 첫 번째 방어선입니다. Azure 리소스의 보안 상태를 주기적으로 분석 하 여 잠재적인 보안 취약성을 식별 합니다. 그런 다음이를 해결 하는 방법에 대 한 권장 사항을 제공 합니다.

- Azure Security Center 권장 사항에 대 한 자세한 내용은 [Azure Security Center의 보안 권장 사항](../security-center/security-center-recommendations.md)을 참조 하세요.
- Azure Security Center에 대 한 자세한 내용은 [Azure Security Center 항목](../security-center/security-center-intro.md) 을 참조 하세요.

## <a name="general"></a>일반

| 권장 사항 | 설명 | ASC에서 지원 됨 |
|-|----|--|
| 최신 상태 유지 | 지원 되는 플랫폼, 프로그래밍 언어, 프로토콜 및 프레임 워크의 최신 버전을 사용 합니다. | - |
| 인증 키를 안전 하 게 유지 | 배포 후 장치 Id와 인증 키를 물리적으로 안전 하 게 유지 합니다. 이렇게 하면 악성 장치를 등록 된 장치로 사용 하지 않습니다. | - |
| 가능 하면 장치 Sdk 사용 | 장치 Sdk는 암호화, 인증 등의 다양 한 보안 기능을 구현 하 여 강력 하 고 안전한 장치 응용 프로그램을 개발 하는 데 도움을 줍니다. 자세한 내용은 [Azure IoT Hub Sdk 이해 및 사용](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 을 참조 하세요. | - |

## <a name="identity-and-access-management"></a>ID 및 액세스 관리 

| 권장 사항 | 설명 | ASC에서 지원 됨 |
|-|----|--|
| 허브에 대 한 액세스 제어 정의 | 기능에 따라 각 구성 요소가 IoT Hub 솔루션에 포함할 [액세스 유형을 이해 하 고 정의](iot-security-deployment.md#securing-the-cloud) 합니다. 허용 되는 권한은 *Registry Read*, *RegistryReadWrite*, *ServiceConnect*및 *DeviceConnect*입니다. [IoT hub의 기본 공유 액세스 정책은](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) 해당 역할에 따라 각 구성 요소에 대 한 사용 권한을 정의 하는 데도 도움이 됩니다. | - |
| 백 엔드 서비스에 대 한 액세스 제어 정의 | IoT Hub 솔루션에 의해 수집 된 데이터는 [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)및 [Blob 저장소](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)와 같은 다른 Azure 서비스에서 사용 될 수 있습니다. 이러한 서비스에 대해 문서화 된 적절 한 액세스 권한을 이해 하 고 허용 해야 합니다. | - |

## <a name="data-protection"></a>데이터 보호

| 권장 사항 | 설명 | ASC에서 지원 됨 |
|-|----|--|
| 보안 장치 인증 | [고유한 id 키 또는 보안 토큰](iot-security-deployment.md#iot-hub-security-tokens)을 사용 하 여 장치와 IoT hub 간에 보안 통신을 보장 하거나 각 장치에 대해 [장치 x.509 인증서](iot-security-deployment.md#x509-certificate-based-device-authentication) 를 사용 합니다. 적절 한 방법을 사용 하 여 [선택 된 프로토콜 (MQTT, AMQP 또는 HTTPS)에 따라 보안 토큰을 사용](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)합니다. | - |
| 보안 장치 통신 | IoT Hub TLS (Transport Layer Security) 표준을 사용 하 여 장치에 대 한 연결을 보호 합니다 (버전 1.2 및 1.0 지원). [TLS 1.2](https://tools.ietf.org/html/rfc5246) 를 사용 하 여 보안을 최대화 합니다. | - |
| 서비스 통신 보안 유지 | IoT Hub는 TLS 프로토콜만 사용 하 여 [Azure Storage](/azure/storage/) 또는 [Event Hubs](/azure/event-hubs) 와 같은 백 엔드 서비스에 연결 하는 엔드포인트을 제공 하며 암호화 되지 않은 채널에는 엔드포인트이 노출 되지 않습니다. 이 데이터가 저장소 또는 분석을 위해 이러한 백 엔드 서비스에 도달 하면 해당 서비스에 대해 적절 한 보안 및 암호화 방법을 사용 하 고 백 엔드에서 중요 한 정보를 보호 해야 합니다. | - |

## <a name="networking"></a>네트워킹

| 권장 사항 | 설명 | ASC에서 지원 됨 |
|-|----|--|
| 장치에 대 한 액세스 보호 | 원치 않는 액세스를 방지 하기 위해 장치의 하드웨어 포트를 최소 운영으로 유지 합니다. 또한 장치의 물리적 변조를 방지 하거나 검색 하는 메커니즘을 빌드 하세요. 자세한 내용은 [IoT 보안 모범 사례](iot-security-best-practices.md) 를 참조 하세요. | - |
| 보안 하드웨어 빌드 | 장치와 인프라를 보다 안전 하 게 유지 하기 위해 암호화 된 저장소, 신뢰할 수 있는 플랫폼 모듈 (TPM) 등의 보안 기능을 통합 합니다. 장치 운영 체제 및 드라이버를 최신 버전으로 업그레이드 하 고, 공간에서 허용 하는 경우에는 바이러스 백신 및 맬웨어 방지 기능을 설치 합니다. [IoT 보안 아키텍처](iot-security-architecture.md) 를 읽고이를 통해 여러 보안 위협을 완화 하는 방법을 이해 하세요. | - |

## <a name="monitoring"></a>모니터링

| 권장 사항 | 설명 | ASC에서 지원 됨 |
|-|----|--|
| 장치에 대 한 무단 액세스 모니터링 |  장치 운영 체제의 로깅 기능을 사용 하 여 장치 또는 해당 포트의 보안 위반 또는 물리적 변조를 모니터링 합니다. | - |
| 클라우드에서 IoT 솔루션 모니터링 | [Azure Monitor 메트릭을](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics)사용 하 여 IoT Hub 솔루션의 전반적인 상태를 모니터링 합니다. | - |
| 진단 설정 | 솔루션에서 이벤트를 기록 하 고 Azure Monitor에 진단 로그를 전송 하 여 성능에 대 한 가시성을 확보 하 여 작업을 면밀 하 게 시청 하세요. 자세한 내용은 [IoT hub의 모니터링 및 진단 문제](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) 를 참조 하세요. | - |

## <a name="next-steps"></a>다음 단계

Azure IoT와 관련 된 고급 시나리오의 경우 추가 보안 요구 사항을 고려해 야 할 수 있습니다. 자세한 지침은 [IoT 보안 아키텍처](iot-security-architecture.md) 를 참조 하세요.

