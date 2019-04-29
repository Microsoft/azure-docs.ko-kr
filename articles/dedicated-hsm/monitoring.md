---
title: 모니터링 옵션 - Azure Dedicated HSM | Microsoft Docs
description: Azure Dedicated HSM 모니터링 옵션 및 모니터링 책임에 대한 개요
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2bcf1d1e007398cf80839f5e1d0bac78fd80db07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912232"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure Dedicated HSM 모니터링

Azure Dedicated HSM 서비스는 고객이 완벽한 관리 제어 및 관리 책임을 통해 단독으로 사용할 수 있는 물리적 디바이스를 제공합니다. 사용 가능한 디바이스는 [Gemalto SafeNet Luna 7 HSM 모델 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)입니다.  일단 고객이 프로비전하면 Microsoft에는 모니터링 역할을 통한 물리적 직렬 포트 연결을 제외하고는 관리 액세스 권한이 없습니다. 결과적으로 고객은 포괄적인 모니터링 및 로그 분석을 포함한 일반적인 작업 활동을 책임집니다.
고객은 HSM을 사용하는 애플리케이션을 완벽하게 책임지며 지원 또는 컨설팅 지원을 위해 Gemalto를 사용해야 합니다. 운영 관리에 대한 고객 소유권의 범위로 인해 Microsoft는 이 서비스에 대해 어떠한 종류의 고가용성도 보장할 수 없습니다. 고객의 책임은 애플리케이션을 올바로 구성하여 고가용성을 달성하도록 보장하는 것입니다. Microsoft는 디바이스 상태 및 네트워크 연결을 모니터링하고 유지합니다.

## <a name="microsoft-monitoring"></a>Microsoft 모니터링

사용 중인 Gemalto SafeNet 디바이스에는 기본적으로 디바이스 모니터링을 위한 옵션으로 SNMP 및 직렬 포트가 있습니다. Microsoft에서는 디바이스 상태에서 기본 원격 분석을 검색하기 위해 디바이스에 연결할 실제 수단으로 직렬 포트 연결을 사용했습니다. 여기에는 온도 같은 항목과 전원 공급 장치 및 팬 같은 구성 요소 상태가 포함됩니다.
이 역할을 완수하기 위해 Microsoft에서는 Gemalto 디바이스에 설정된 비관리 “모니터링” 역할을 사용합니다. 이 역할은 원격 분석을 검색하는 기능을 제공하지만 관리 작업 측면에서 또는 암호화 정보를 확인하는 방식에서 디바이스에 대한 액세스 권한을 전혀 부여하지 않습니다. 고객은 자신의 디바이스가 실제로 중요한 암호화 키 스토리지를 관리하고 사용할 수 있음을 확신할 수 있습니다. 모든 고객이 기본 상태 모니터링에 대한 이 최소한의 액세스에 만족하지 않는 경우 모니터링 계정을 사용하지 않도록 설정할 수 있는 옵션이 있습니다. 이 옵션의 명확한 결과로 Microsoft에는 아무런 정보가 없게 되고 따라서 디바이스 상태 문제에 대한 사전 알림을 제공할 능력도 없게 됩니다. 이 경우 고객은 디바이스 상태에 대해 책임을 져야 합니다.
모니터 함수 자체는 상태 데이터를 가져오려면 10분마다 디바이스를 폴링하도록 설정됩니다. 직렬 통신의 오류가 발생하기 쉬운 특성으로 인해 1시간 넘게 여러 부정적인 상태가 표시된 후에야 경고가 발생합니다. 이 경고는 궁극적으로 문제를 알리는 사전 고객 통신을 발생시킵니다.
문제의 특성에 따라 적절한 작업 과정을 사용하여 영향을 줄이고 낮은 위험 수정을 확인합니다. 예를 들어 전원 공급 장치 오류는 결과적으로 변조 이벤트가 없으므로 영향을 낮추고 작업 위험을 최소화하여 수행할 수 있는 핫스왑 프로시저입니다. 다른 프로시저의 경우 고객에게 모든 보안 위험을 최소화하기 위해 디바이스가 제로로 초기화되고 프로비전이 해제되어야 할 수 있습니다. 이 경우 고객은 대체 디바이스를 프로비전하고 고가용성 쌍에 다시 가입하여 디바이스 동기화를 트리거합니다. 중단을 최소화하고 보안 위험을 가장 낮게 유지하여 정상 작업이 최소한의 시간에 다시 시작됩니다.  

## <a name="customer-monitoring"></a>고객 모니터링

전용 HSM 서비스의 가치 제안은 특히 클라우드 배달 디바이스로 간주하면서 고객이 얻을 수 있는 디바이스의 컨트롤입니다. 이 컨트롤의 결과는 디바이스 상태를 모니터링하고 관리해야 하는 책임입니다. Gemalto SafeNet 디바이스는 SNMP 및 Syslog 구현에 대한 지침과 함께 제공됩니다. 전용 HSM 서비스의 고객은 Microsoft 모니터링 계정이 활성 상태로 유지되는 경우에도 이 디바이스를 사용하는 것이 좋으며, Microsoft 모니터 계정을 사용하지 않도록 설정하는 경우 이 디바이스를 반드시 사용해야 합니다.
사용 가능한 기술을 통해 고객이 문제를 식별할 수 있거나 Microsoft 지원팀에 적절한 수정 작업을 시작하도록 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

예를 들어 고가용성 및 보안과 같은 서비스의 모든 주요 개념은 모든 디바이스 프로비전 및 애플리케이션 디자인 또는 배포 전에 제대로 이해하는 것이 좋습니다. 추가 개념 수준 항목:

* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)
* [지원 가능성](supportability.md)
