---
title: Azure 스프링 클라우드 지역 재해 복구 | Microsoft Docs
description: 지역 가동 중단 으로부터 스프링 클라우드 응용 프로그램을 보호 하는 방법을 알아봅니다.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: brendm
ms.openlocfilehash: 4961e5a63e5bc1933cf19b1f291b521d89cbda0e
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279148"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Azure 스프링 클라우드 재해 복구

이 문서에서는 가동 중지 시간이 발생 하지 않도록 Azure 스프링 클라우드 응용 프로그램을 보호 하는 데 사용할 수 있는 몇 가지 전략을 설명 합니다.  지역 또는 데이터 센터에서 지역 재해로 인 한 가동 중지 시간이 발생할 수 있지만 신중한 계획은 고객에 대 한 영향을 완화할 수 있습니다.

## <a name="plan-your-application-deployment"></a>응용 프로그램 배포 계획

Azure 스프링 클라우드 응용 프로그램은 특정 지역에서 실행 됩니다.  Azure는 전 세계 여러 지역에서 작동합니다. Azure 지리적 위치는 하나 이상의 Azure 지역을 포함하는 전 세계의 정의된 영역입니다. Azure 지역은 하나 이상의 데이터 센터를 포함 하는 지리 내의 영역입니다.  각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. Azure는 지역 쌍에서 플랫폼 업데이트 (계획 된 유지 관리)를 직렬화 하 여 한 번에 각 쌍의 한 지역이 업데이트 되도록 합니다. 여러 지역에 영향을 주는 중단의 경우 각 쌍에서 하나 이상의 지역에 복구 우선 순위가 지정됩니다.

재해 로부터 고가용성 및 보호를 보장 하려면 스프링 클라우드 응용 프로그램을 여러 지역에 배포 해야 합니다.  Azure는 지역 쌍에 대 한 스프링 클라우드 배포를 계획할 수 있도록 [쌍을 이루는 지역](../best-practices-availability-paired-regions.md) 목록을 제공 합니다.  마이크로 서비스 아키텍처를 설계할 때 지역 가용성, Azure 쌍을 이루는 지역 및 서비스 가용성의 세 가지 주요 요소를 고려 하는 것이 좋습니다.

*  지역 가용성: 네트워크 지연 및 전송 시간을 최소화 하기 위해 사용자에 게 가까운 지역 지역을 선택 합니다.
*  Azure 쌍을 이루는 지역: 선택한 지역 내에서 페어링된 지역을 선택 하 여 조정 된 플랫폼 업데이트를 보장 하 고 필요한 경우 복구 작업의 우선 순위를 지정 합니다.
*  서비스 가용성: 페어링된 지역이 핫/핫, 핫/웜 또는 핫/콜드을 실행 해야 하는지 여부를 결정 합니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 는 DNS 기반 트래픽 부하 분산을 제공 하 고 여러 지역에 걸쳐 네트워크 트래픽을 분산할 수 있습니다.  Azure Traffic Manager을 사용 하 여 고객에 게 가장 가까운 Azure 스프링 클라우드 서비스 인스턴스로 고객을 안내 합니다.  최상의 성능 및 중복성을 위해 azure 스프링 클라우드 서비스로 보내기 전에 Azure Traffic Manager를 통해 모든 응용 프로그램 트래픽을 전달 합니다.

Azure 스프링 클라우드 응용 프로그램이 여러 지역에 있는 경우 Azure Traffic Manager를 사용 하 여 각 지역의 응용 프로그램에 대 한 트래픽 흐름을 제어할 수 있습니다.  서비스 IP를 사용 하 여 각 서비스에 대 한 Azure Traffic Manager 엔드포인트을 정의 합니다. 고객은 Azure 스프링 클라우드 서비스를 가리키는 Azure Traffic Manager DNS 이름에 연결 해야 합니다.  Azure Traffic Manager 정의 된 엔드포인트에서 트래픽 부하를 분산 합니다.  재해가 발생 한 데이터 센터에서 Azure Traffic Manager는 해당 지역에서 해당 쌍으로 트래픽을 전달 하 여 서비스 연속성을 보장 합니다.