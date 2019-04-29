---
title: HSM 물리적 보안 - Azure Dedicated HSM | Microsoft Docs
description: 데이터 센터 내 Azure Dedicated HSM 디바이스의 물리적 보안에 대한 정보
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
ms.openlocfilehash: 6fe0557aa90ec3345d4ca0c3d9292cab3435bb2f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118063"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure Dedicated HSM 물리적 보안

Azure Dedicated HSM은 키 스토리지에 대한 고급 보안 요구 사항을 충족하도록 도와줍니다. 고객 요구를 충족하기 위해 전체 수명 주기 내내 엄격한 보안 모범 사례에 따라 관리됩니다.

## <a name="security-through-procurement"></a>조달 보안

Microsoft는 보안 조달 프로세스를 따릅니다. Microsoft 관리 연속성을 관리하고 주문 및 배송되는 디바이스가 Microsoft의 데이터 센터에 도착하는 디바이스인지 확인합니다. 디바이스는 변조 이벤트 비닐 백에 담겨 있습니다. 데이터 센터의 데이터 갤러리에 의뢰될 때까지 안전한 스토리지 영역에 보관됩니다.  HSM 디바이스가 장착된 랙은 HBI(High Business Impact)로 간주됩니다. 디바이스는 잠긴 상태로 보관되며 항상 전후방에서 비디오로 감시됩니다.

## <a name="security-through-deployment"></a>배포 보안

HSM은 연결된 네트워킹 구성 요소와 함께 랙에 설치됩니다. 설치 후에는 Azure Dedicated HSM 서비스의 일부로 사용할 수 있도록 구성해야 합니다. 이 구성 작업은 신원 조회를 마친 Microsoft 직원이 수행합니다. "JIT(Just In Time)" 관리를 사용하여 올바른 직원이 액세스가 필요한 시간에만 액세스할 수 있도록 제한됩니다. 사용되는 프로시저와 시스템 역시 HSM 디바이스와 관련된 모든 작업이 기록되도록 보장합니다.

## <a name="security-in-operations"></a>운영 보안

HSM은 하드웨어 어플라이언스(어플라이언스 내의 PCI 카드가 되는 실제 HSM)이므로 구성 요소 수준의 문제가 발생할 수 있습니다. 잠재적인 문제로 팬 및 전원 공급 장치 오류가 포함되며 이에 국한되지 않습니다. 이러한 유형의 이벤트가 발생하면 유지 관리 또는 고장/수리 작업을 통해 교체 가능한 구성 요소를 교체해야 합니다.

### <a name="component-replacement"></a>구성 요소 교체

디바이스가 프로비전되고 고객의 관리 하에 들어가는 이후부터 교체 할 수 있는 유일한 구성 요소는 핫 스왑 가능한 전원 공급 디바이스입니다. 이 구성 요소는 보안 경계 외부에 있으며 조작 이벤트를 발생시키지 않습니다. 티켓 시스템은 Microsoft 엔지니어에게 HBI 랙 후면에 액세스할 수 있는 권한을 부여하는 데 사용됩니다. 티켓이 처리되면 임시 실제 키가 발급됩니다. 이 키는 엔지니어가 디바이스에 액세스하여 영향을 받는 구성 요소를 교체할 수 있도록 허용합니다. 기타 액세스(즉, 조작 이벤트를 발생시키는 액세스)는 디바이스가 고객에게 할당되지 않아 보안 및 가용성 위험이 최소화될 때 처리됩니다.  

### <a name="device-replacement"></a>디바이스 교체

디바이스 전체에서 오류가 발생할 경우 구성 요소 오류 시 사용되는 프로세스와 비슷한 과정이 수행됩니다. 고객이 디바이스를 초기화할 수 없거나 디바이스 상태를 알 수 없는 경우 데이터가 들어 있는 디바이스는 분리되어 랙 내 폐기함으로 이동됩니다. 폐기함으로 이동된 디바이스는 철저하게 통제되는 안전한 방법으로 폐기됩니다. HBI의 어떤 디바이스도 데이터가 들어 있는 상태로 Microsoft 데이터 외부로 나갈 수 없습니다.

### <a name="other-rack-access-activities"></a>기타 랙 액세스 활동

Microsoft 엔지니어가 HSM 디바이스에서 사용하는 랙에 액세스해야 하는 경우(예: 네트워킹 디바이스 유지 관리) HBI 보안 랙에 액세스하기 위해 표준 보안 프로시저가 사용됩니다. 모든 액세스는 비디오로 감시됩니다. HSM 디바이스는 [FIPS 140-2 Level 3](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) 검증을 거쳤기 때문에 HSM 디바이스에 대한 무단 액세스가 발생할 경우 고객에게 신호를 보내고 데이터가 초기화됩니다.

## <a name="logical-level-security-considerations"></a>논리 수준 보안 고려 사항

HSM은 고객이 만든 가상 네트워크에 프로비전됩니다. 이 가상 네트워크는 고객의 비공개 IUP 주소 공간입니다.  이 구성은 중요한 논리 네트워크 수준 격리를 제공하고 고객만 액세스할 수 있도록 보장합니다. 즉, 모든 논리 수준 보안 제어는 고객의 책임입니다.

## <a name="next-steps"></a>다음 단계

디바이스를 프로비전하고 애플리케이션을 디자인 또는 배포하기 전에 고가용성, 보안, 지원 같은 서비스의 모든 주요 개념을 정확하게 이해하는 것이 좋습니다.

* [고가용성](high-availability.md)
* [네트워킹](networking.md)
* [지원 가능성](supportability.md)
* [모니터링](monitoring.md)
* [배포 아키텍처](deployment-architecture.md)