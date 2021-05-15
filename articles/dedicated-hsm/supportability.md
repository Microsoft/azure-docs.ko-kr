---
title: 지원 가능성 - Azure Dedicated HSM | Microsoft Docs
description: 다양한 시나리오에서 Azure Dedicated HSM의 지원 옵션 및 책임 영역
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606932"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure Dedicated HSM 지원 가능성

Azure Dedicated HSM 서비스는 고객이 완벽한 관리 제어 및 관리 책임을 통해 단독으로 사용할 수 있는 물리적 디바이스를 제공합니다. 사용 가능한 디바이스는 [Thales Luna 7 HSM 모델 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms)입니다. 일단 고객이 프로비전하면 Microsoft에는 모니터링 역할을 통한 물리적 직렬 포트 연결을 제외하고는 관리 액세스 권한이 없습니다.  액세스 권한이 없으면 Microsoft에서 소프트웨어 수준 유지 관리 또는 시스템 관리 책임을 계속 수행할 수가 없습니다. 따라서 고객이 일반적인 운영 활동을 수행해야 합니다.
고객은 HSM을 사용하는 애플리케이션에 대해 전적으로 책임이 있으며 지원 또는 컨설팅 지원이 필요한 경우 Thales를 사용해야 합니다. 운영 관리에 대한 고객 소유권의 범위로 인해 Microsoft는 이 서비스에 대해 어떠한 종류의 고가용성도 보장할 수 없습니다. 따라서 고객이 애플리케이션에서 고가용성을 달성하도록 제대로 구성해야 합니다. Microsoft는 디바이스 상태 및 네트워크 연결을 모니터링하고 유지합니다.

## <a name="getting-support"></a>지원 받기

Dedicated HSM에 대한 고객 지원은 Microsoft와 Thales 간의 공동 작업입니다. 하드웨어 문제나 네트워크 경로 문제는 Microsoft에서 해결하며, 구성, 소프트웨어, 펌웨어 및 애플리케이션 개발과 같은 실제 HSM을 사용하는 모든 작업은 Thales에 의해 처리됩니다. 이 지원 모델은 가장 효율적인 지원으로 가장 빠르게 라우팅할 수 있습니다. 특정 문제에 관하여 의문이 있는 경우 Microsoft와의 지원 요청을 알려주시면 올바른 상태인지 확인해 드립니다. Microsoft는 모든 지원 시나리오를 계속 활용하고 고객을 위한 최고의 지원 환경을 제공합니다.

## <a name="thales-support"></a>Thales 지원

Dedicated HSM 서비스를 사용하는 고객은 Plus 지원 계획에 따라 Thales의 지원을 받을 수 있습니다. 이렇게 하려면 Thales 지원 포털을 사용하는 등록 프로세스만 있으면 됩니다. 이에 대한 고객 ID와 지침은 Dedicated HSM 서비스에 대한 액세스 권한을 얻기 위해 Microsoft와 초기 계약의 일부로 제공됩니다. Thales에서 지원을 받을 수 있는 메커니즘은 [고객 지원 포털](https://supportportal.thalesgroup.com/csm)을 통해 제공됩니다.
핵심은 고객이 지원 포털에서 다운로드를 통해 HSM(예: 클라이언트 액세스 소프트웨어 및 SDK)을 사용하는 데 필요한 모든 소프트웨어와 설명서를 Thales에서 제공한다는 점입니다.

### <a name="software-components"></a>소프트웨어 구성 요소

HSM 디바이스의 구성에 사용되는 다양한 소프트웨어 구성 요소는 다음과 같습니다.

* 클라이언트 소프트웨어
* SDK)
* 도구

### <a name="guidance"></a>지침

Thales는 [Thales 고객 지원 포털](https://supportportal.thalesgroup.com/csm)을 통해 관리 및 구성 지침을 제공합니다. 유효한 고객 ID를 사용하여 로그인하면 이러한 문서를 다운로드할 수 있습니다. 또한 Thales는 다양한 시나리오와 소프트웨어 통합에 도움이 되는 일련의 통합 가이드를 제공합니다. 자세한 내용은 [Microsoft에 대한 Thales 파트너 사이트](https://cpl.thalesgroup.com/partners/overview)를 참조하세요.

### <a name="support"></a>지원

Dedicated HSM 서비스의 일부로 HSM을 사용하는 것과 관련된 모든 소프트웨어 수준 문제 또는 질문은 Thales 지원에서 직접 처리해야 합니다. 위에 나열된 모든 소프트웨어 구성 요소와 프로비전 이후의 모든 사용자 지정 HSM 구성은 Thales에서 처리됩니다. 자세한 내용은 [Thales 고객 지원 포털](https://supportportal.thalesgroup.com/csm)을 참조하세요.

### <a name="consulting-services"></a>컨설팅 서비스

HSM을 사용하는 사용자 지정 애플리케이션의 설계, 개발 및 배포에 대한 도움이 필요하면 Thales 계정 담당자에게 문의하세요.

## <a name="microsoft-support"></a>Microsoft 지원

Microsoft는 단일 고객의 독점적 사용을 위해 실제 HSM 디바이스를 네트워크에서 액세스할 수 있게 지원하고 작동 상태를 보장합니다. 고객은 디바이스 구성, 운영 및 관리를 담당합니다. Microsoft의 책임은 다음과 같습니다.

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

Dedicated HSM 서비스에 대한 고객의 등록이 승인되면 HSM 리소스를 만들 수 있습니다(현재는 Azure Portal이 아닌 PowerShell 또는 명령줄 인터페이스를 통해). 리소스는 지정된 지역의 물리적 디바이스를 고객의 미리 정의된 VNet(가상 네트워크)에 매핑하는 할당 프로세스를 거칩니다. VNet에 표시되면 고객이 디바이스에 액세스하고 요구 사항에 따라 추가로 구성할 수 있습니다. 고객은 Thales 클라이언트 소프트웨어 및 도구를 사용하여 Dedicated HSM에 액세스합니다. 리소스 만들기 프로세스는 Microsoft에서 지원됩니다. 사용자 지정 구성 프로세스 등은 Thales에서 지원됩니다. (위의 Thales 지원 참조). 고객의 HSM 사용이 완료되면 데이터가 유지되지 않도록 해당 HSM을 다시 설정(또는 초기화)해야 합니다. 디바이스를 다시 설정하는 프로세스에서는 모든 사용자 지정 구성 및 데이터가 제거됩니다. Microsoft는 디바이스를 할당 취소하고 초기 상태의 풀에 반환합니다. 즉 디바이스가 풀에 반환되면 이전 고객 활동의 증거가 없습니다. 

### <a name="hardware-issues"></a>하드웨어 문제

HSM 디바이스에는 중복 및 교체 가능한 전원 공급 디바이스와 팬 냉각 디바이스가 있습니다.  그러나 팬 장치를 제거하면 여전히 변조 이벤트가 발생할 수 있습니다. 구성 요소 오류가 발생하면 Microsoft에서 고객의 서비스 가용성에 대한 중단과 위험을 최소화하는 방식으로 구성 요소 수준 문제를 해결하는 데 가장 적절한 프로세스를 사용합니다.
장치에서 더 심각한 오류가 발생하면 해당 디바이스가 여유 풀에서 새 디바이스로 교체됩니다. 고객이 기존 HA 쌍에 새 디바이스를 포함하기만 하면 동기화하고 전체 작동 상태로 되돌릴 수 있습니다. 디바이스에 오류가 발생하면 데이터 센터의 현장에서 해당 데이터 관련 디바이스를 제거하고 단편화합니다. 

### <a name="networking-issues"></a>네트워킹 문제

HSM 디바이스의 네트워킹 액세스 문제가 발생하면 Microsoft 지원에 문의해야 합니다. 네트워킹 액세스에 대한 간단한 테스트는 SSH를 사용하여 HSM 디바이스에 연결하는 것입니다. 이 작업이 실패하면 Microsoft 지원에 문의하세요.

## <a name="service-level-expectations-for-support"></a>지원 서비스 수준에 대한 요구

Microsoft 지원 서비스 수준은 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/)을 참조하세요.
Thales 지원 서비스 수준은 [Thales 지원 기본 정보](https://azure.microsoft.com/support/plans/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

디바이스를 프로비전하고 애플리케이션을 설계 또는 배포하기 전에 고가용성 및 보안과 같은 주요 개념을 잘 이해하는 것이 좋습니다.

* [배포 아키텍처](deployment-architecture.md)
* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)

