---
title: 지원 가능성 - Azure Dedicated HSM | Microsoft Docs
description: 다양한 시나리오에서 Azure Dedicated HSM의 지원 옵션 및 책임 영역
services: dedicated-hsm
author: johndaw
manager: barbkess
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 8341e9057b07fbb72957eddc342ae875e4591993
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912267"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure Dedicated HSM 지원 가능성

Azure Dedicated HSM 서비스는 고객이 완벽한 관리 제어 및 관리 책임을 통해 단독으로 사용할 수 있는 물리적 디바이스를 제공합니다. 사용 가능한 디바이스는 [Gemalto SafeNet Luna 7 HSM 모델 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)입니다. 일단 고객이 프로비전하면 Microsoft에는 모니터링 역할을 통한 물리적 직렬 포트 연결을 제외하고는 관리 액세스 권한이 없습니다.  액세스 권한이 없으면 Microsoft에서 소프트웨어 수준 유지 관리 또는 시스템 관리 책임을 계속 수행할 수가 없습니다. 따라서 고객이 일반적인 운영 활동을 수행해야 합니다.
고객은 HSM을 사용하는 애플리케이션을 전적으로 책임을 지며, 지원 또는 컨설팅 기반 지원을 받으려면 Gemalto와 협력해야 합니다. 운영 관리에 대한 고객 소유권의 범위로 인해 Microsoft는 이 서비스에 대해 어떠한 종류의 고가용성도 보장할 수 없습니다. 따라서 고객이 애플리케이션에서 고가용성을 달성하도록 제대로 구성해야 합니다. Microsoft는 디바이스 상태 및 네트워크 연결을 모니터링하고 유지합니다.

## <a name="getting-support"></a>지원 받기

고객 전용 HSM에 대 한 지원은 Microsoft 및 Gemalto 간의 협력 합니다. 하드웨어 문제 또는 네트워크 경로 문제를 Microsoft에서 해결 될 예정 및 구성, 소프트웨어, 펌웨어 및 응용 프로그램 개발을 같은 실제 HSM을 사용 하 여 관련이 Gemalto에서 해결 될 예정입니다. 이 지원 모델에는 가장 효과적인 지원 가장 빠른 경로 확인합니다. 특정 문제를 사용 하 여 확실 하지 않은의 경우 microsoft 지원 요청을 발생 시키고 하면 적절 하 게 이동 되도록 합니다. Microsoft는 모든 지원 시나리오에 참여 상태를 유지 하 고 고객에 대 한 최상의 지원 환경을 위해 노력 합니다.

## <a name="gemalto-support"></a>Gemalto 지원

전용 HSM 서비스를 사용 하 여 고객에 게 해당 Plus 지원 플랜에 따라 Gemalto에서 지원에 적합 합니다. Gemalto 지원 포털을 사용 하 여 등록 프로세스를 방금 필요 합니다. 전용 HSM 서비스에 대 한 액세스 권한을 얻으려고 Microsoft 사용 하 여 초기 계약의 일부로 고객 ID 및 지침은 제공 됩니다. Gemalto에서 지원하는 메커니즘은 [고객 지원 포털](https://supportportal.gemalto.com/csm/)을 통해 이루어집니다.
메모의 핵심은 고객 지원 포털에서 다운로드를 통해 모든 소프트웨어 및 설명서 (예: 클라이언트 액세스 소프트웨어 및 Sdk) HSM을 사용 하는 데 필요한 Gemalto는 제공입니다.

### <a name="software-components"></a>소프트웨어 구성 요소

HSM 디바이스의 구성에 사용되는 다양한 소프트웨어 구성 요소는 다음과 같습니다.

* 클라이언트 소프트웨어
* SDK)
* 도구

### <a name="guidance"></a>지침

Gemalto는 [고객 지원 포털](https://supportportal.gemalto.com/csm/)을 통해 관리 및 구성 지침을 제공합니다. 유효한 고객 ID를 사용하여 로그인하면 이러한 문서를 다운로드할 수 있습니다. 또한 Gemalto는 고객에게 다양한 시나리오와 소프트웨어 통합을 지원하는 일련의 통합 가이드를 제공합니다. 자세한 내용은 [Microsoft에 대한 Gemalto 파트너 사이트](https://safenet.gemalto.com/partners/microsoft/)를 참조하세요.

### <a name="support"></a>지원

Dedicated HSM 서비스의 일부로 HSM을 사용하는 것과 관련된 모든 소프트웨어 수준 문제 또는 질문은 Gemalto 지원에서 직접 처리해야 합니다. 위에 나열된 모든 소프트웨어 구성 요소와 프로비전 이후의 모든 사용자 지정 HSM 구성은 Gemalto에서 처리됩니다. 자세한 내용은 [Gemalto 고객 지원 포털](https://supportportal.gemalto.com/csm/)을 참조하세요.

### <a name="consulting-services"></a>컨설팅 서비스

HSM을 사용하는 사용자 지정 애플리케이션의 설계, 개발 및 배포에 대한 도움이 필요하면 Gemalto 계정 담당자에게 문의하세요.

## <a name="microsoft-support"></a>Microsoft 지원

Microsoft는 물리적 HSM 장치는 네트워크 액세스 가능 하 고 단일 고객 전용 사용에 대 한 작업 상태를 확인 합니다. 고객은 구성, 관리 및 장치 관리를 담당합니다. Microsoft의 책임은 다음과 같습니다.

* 디바이스에 전원 및 냉각 디바이스가 있는지 확인합니다.
* HSM의 작동 상태를 유지합니다(예: 고장/수리 시나리오).
* 네트워크를 통해 디바이스에 액세스할 수 있습니다.

Microsoft에 보고되어야 하는 문제는 다음과 같습니다.

* 구성 요소 오류
* 전체 디바이스 오류
* 네트워크 액세스 문제
* 프로비전 및 프로비전 해제 문제

Microsoft는 기본 상태 원격 분석을 수행할 수 있는 모니터링 역할(관리 역할이 아님)을 통해 디바이스의 실제 직렬 포트에 액세스합니다.  이렇게 하면 고객이 이 권한을 제한하도록 선택하지 않는 한 Microsoft에서 고객에게 문제에 대한 자동 관리 알림을 제공할 수 있습니다. 

### <a name="provisioning-and-decommissioning"></a>프로비전 및 서비스 해제

Dedicated HSM 서비스에 대한 고객의 등록이 승인되면 HSM 리소스를 만들 수 있습니다(현재는 Azure Portal이 아닌 PowerShell 또는 명령줄 인터페이스를 통해). 리소스는 지정된 지역의 물리적 디바이스를 고객의 미리 정의된 VNet(가상 네트워크)에 매핑하는 할당 프로세스를 거칩니다. VNet에 표시되면 고객이 디바이스에 액세스하고 요구 사항에 따라 추가로 구성할 수 있습니다. 고객은 Gemalto 클라이언트 소프트웨어 및 도구를 사용하여 Dedicated HSM에 액세스합니다. 리소스 만들기 프로세스는 Microsoft에서 지원됩니다. 사용자 지정 구성 프로세스 등은 Gemalto에서 지원됩니다. (위의 Gemalto 지원을 참조하세요.) 고객의 HSM 사용이 완료되면 데이터가 유지되지 않도록 해당 HSM을 다시 설정(또는 초기화)해야 합니다. 디바이스를 다시 설정하는 프로세스에서는 모든 사용자 지정 구성 및 데이터가 제거됩니다. Microsoft는 디바이스를 할당 취소하고 초기 상태의 풀에 반환합니다. 즉 디바이스가 풀에 반환되면 이전 고객 활동의 증거가 없습니다. 

### <a name="hardware-issues"></a>하드웨어 문제

HSM 디바이스에는 중복 및 교체 가능한 전원 공급 디바이스와 팬 냉각 디바이스가 있습니다.  그러나 팬 단위 분리 조작 이벤트를 유발 됩니다. 구성 요소 오류가 발생하면 Microsoft에서 고객의 서비스 가용성에 대한 중단과 위험을 최소화하는 방식으로 구성 요소 수준 문제를 해결하는 데 가장 적절한 프로세스를 사용합니다.
디바이스에 더 심각한 오류가 발생하면 해당 디바이스가 사용 가능한 풀의 새 디바이스로 교체됩니다. 고객이 기존 HA 쌍에 새 디바이스를 포함하기만 하면 동기화하고 전체 작동 상태로 되돌릴 수 있습니다. 디바이스에 오류가 발생하면 데이터 센터의 현장에서 해당 데이터 관련 디바이스를 제거하고 단편화합니다. 섀시만 재활용을 위해 Gemalto에 반환됩니다.


### <a name="networking-issues"></a>네트워킹 문제

HSM 디바이스의 네트워킹 액세스 문제가 발생하면 Microsoft 지원에 문의해야 합니다. 네트워킹 액세스에 대한 간단한 테스트는 SSH를 사용하여 HSM 디바이스에 연결하는 것입니다. 이 작업이 실패하면 Microsoft 지원에 문의하세요.

## <a name="service-level-expectations-for-support"></a>지원 서비스 수준에 대한 요구

Microsoft 지원 서비스 수준은 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/)을 참조하세요.
Gemalto 지원 서비스 수준은 [Gemalto 지원 기본 정보](https://azure.microsoft.com/support/plans/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

디바이스를 프로비전하고 애플리케이션을 설계 또는 배포하기 전에 고가용성 및 보안과 같은 주요 개념을 잘 이해하는 것이 좋습니다.

* [배포 아키텍처](deployment-architecture.md)
* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)

