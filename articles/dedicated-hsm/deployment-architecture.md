---
title: 배포 아키텍처 - Azure Dedicated HSM | Microsoft Docs
description: 애플리케이션 아키텍처의 일부로 Azure Dedicated HSM을 사용할 때 생각해야 하는 기본적인 설계 고려 사항
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: f078df7677e771d131f15056ac4a54a58a3134bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912291"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Azure 전용 HSM 배포 아키텍처

Azure Dedicated HSM은 Azure에서 암호화 키 스토리지를 제공합니다. 엄격한 보안 요구 사항을 충족합니다. 다음에 해당하는 고객은 Azure Dedicated HSM을 사용하면 혜택을 얻을 수 있습니다.

* FIPS 140-2 Level 3 인증을 충족해야 합니다.
* HSM에 대한 배타적 액세스가 필요합니다.
* 디바이스를 완벽하게 제어할 수 있어야 합니다.

HSM은 Microsoft의 데이터 센터에 분산되며 고가용성 솔루션의 기반이 되는 디바이스 쌍으로 쉽게 프로비전할 수 있습니다. 또한 여러 Azure 지역에 배포하여 재해 복구 솔루션을 구현할 수도 있습니다. 현재 Dedicated HSM을 사용할 수 있는 Azure 지역은 다음과 같습니다.

* 미국 동부
* 미국 동부 2
* 미국 서부
* 미국 중남부
* 동남아시아
* 동아시아
* 유럽 북부
* 서유럽
* 영국 남부
* 영국 서부
* 캐나다 중부
* 캐나다 동부
* 오스트레일리아 동부
* 오스트레일리아 남동부

이러한 각 지역에 있는 두 개의 독립적인 데이터 센터 또는 적어도 두 개 이상의 독립적인 가용성 영역에 HSM 랙이 배포되어 있습니다. 동남 아시아에는 3개의 가용성 영역, 미국 동부 2에는 2개의 가용성 영역이 있습니다. 유럽, 아시아, 미국에는 HSM 서비스를 제공하는 총 8개의 Azure 지역이 있습니다. Azure 지역에 대한 자세한 내용은 공식 [Azure 지역 정보](https://azure.microsoft.com/global-infrastructure/regions/)를 참조하세요.
Dedicated HSM 기반 솔루션의 디자인 요소로는 위치/대기 시간, 고가용성, 다른 분산 애플리케이션 지원이 포함됩니다.

## <a name="device-location"></a>디바이스 위치

최적의 HSM 디바이스 위치는 암호화 작업을 수행하는 애플리케이션과 가장 가까운 곳입니다. 지역 내 대기 시간은 밀리초 단위일 것으로 예상됩니다. 지역 간 대기 시간은 이 시간보다 5-10배 길 수도 있습니다.

## <a name="high-availability"></a>고가용성

고가용성을 달성하려는 고객은 한 지역에서 고가용성 쌍으로 Gemalto를 사용하여 구성되는 HSM 디바이스를 2대 사용해야 합니다. 단일 디바이스에서 키 작업을 방해하는 문제가 발생하는 경우 이 유형의 배포를 사용하면 키의 가용성이 보장됩니다. 또한 전원 공급 장치 교체 같은 고장/수리 유지 관리를 수행할 때 위험이 크게 감소합니다. 디자인 시 Azure 지역 수준 장애를 고려해야 합니다. 허리케인, 홍수, 지진 등의 자연 재해가 발생할 경우 Azure 지역 수준 장애가 발생할 수 있습니다. HSM 디바이스를 다른 Azure 지역에 프로비전하여 이와 같은 종류의 이벤트를 완화해야 합니다. 다른 Azure 지역에서 배포된 디바이스는 Gemalto 소프트웨어 구성을 통해 함께 쌍으로 묶을 수 있습니다. 즉, 고가용성 및 재해 복구 솔루션을 구현하려면 두 Azure 지역에 4개 이상의 HSM 디바이스를 배포해야 합니다. Azure 지역 간 로컬 중복 및 지역 중복을 기준으로 사용하여 대기 시간 및 용량을 지원하거나 다른 애플리케이션별 요구 사항을 충족하도록 HSM 디바이스 배포를 추가할 수 있습니다.

## <a name="distributed-application-support"></a>분산 애플리케이션 지원

Dedicated HSM 디바이스는 일반적으로 키 저장 및 키 검색 작업을 수행해야 하는 애플리케이션을 지원하기 위해 배포됩니다. Dedicated HSM 디바이스에는 독립 애플리케이션을 지원하기 위한 10개의 파티션이 있습니다. 디바이스 위치는 서비스를 사용해야 하는 모든 애플리케이션의 전체적인 관점을 기반으로 해야 합니다.

## <a name="next-steps"></a>다음 단계

배포 아키텍처가 결정되면 해당 아키텍처를 구현하기 위한 대부분의 구성 작업을 Gemalto에서 제공합니다. 여기에는 디바이스 구성뿐 아니라 애플리케이션 통합 시나리오가 포함됩니다. 자세한 내용을 알아보려면 [Gemalto 고객 지원](https://supportportal.gemalto.com/csm/) 포털을 사용하여 관리 및 구성 가이드를 다운로드하세요. Microsoft 파트너 사이트에는 다양한 통합 가이드가 있습니다.
예를 들어 고가용성 및 보안과 같은 서비스의 모든 주요 개념은 디바이스를 프로비전하거나 애플리케이션을 디자인하고 배포하기 전에 제대로 이해하는 것이 좋습니다.
추가 개념 수준 항목:

* [고가용성](high-availability.md)
* [물리적 보안](physical-security.md)
* [네트워킹](networking.md)
* [지원 가능성](supportability.md)
* [모니터링](monitoring.md)
