---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 12/14/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c26c037455b6d14a906894ec39bf46630826950b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301718"
---
Azure에서는 가상 머신에 대한 호스트 인프라의 안정성, 성능 및 보안을 개선하기 위해 주기적으로 플랫폼을 업데이트합니다. 이러한 업데이트는 호스팅 환경의 소프트웨어 구성 요소 패치, 네트워킹 구성 요소 업그레이드, 하드웨어 서비스 해제 등을 포함합니다. 대부분의 업데이트는 호스트된 가상 머신에 영향을 주지 않습니다. 그러나 업데이트가 영향을 주는 경우가 있으며, Azure는 업데이트의 영향을 최소화하는 방법을 선택합니다.

- 다시 부팅하지 않는 업데이트가 가능한 경우 호스트가 업데이트되는 동안 VM이 일시 중지되거나, 이미 업데이트된 호스트로 실시간 마이그레이션됩니다.

- 유지 관리를 다시 부팅해야 하는 경우 유지 관리가 계획된 시기에 대해 알림을 받을 수 있습니다. Azure는 사용자가 편리한 시간에 직접 유지 관리를 시작할 수 있는 기간도 제공합니다. Azure는 계획된 플랫폼 유지 관리를 위해 VM을 다시 부팅해야 하는 경우를 줄이는 기술에 투자하고 있습니다. 

이 페이지에서는 Azure에서 두 가지 유형의 유지 관리를 모두 수행하는 방법을 설명합니다. 계획되지 않은 이벤트(중단)에 대한 자세한 내용은 [Windows](../articles/virtual-machines/windows/manage-availability.md) 또는 [Linux](../articles/virtual-machines/linux/manage-availability.md)용 가상 머신의 가용성 관리를 참조하세요.

[Windows](../articles/virtual-machines/windows/scheduled-events.md) 또는 [Linux](../articles/virtual-machines/linux/scheduled-events.md)용 Scheduled Events를 사용하면 예정된 유지 관리에 대한 알림을 VM 내에서 받을 수 있습니다.

계획된 유지 보수의 관리에 대한 "방법" 정보는 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 또는 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)용 "계획된 유지 관리 알림 처리"를 참조하세요.

## <a name="maintenance-not-requiring-a-reboot"></a>다시 부팅을 요구 하지 않는 유지 관리

다시 부팅이 필요 하지 않은 대부분의 유지 관리에 대 한 목적은 10 초 미만의 VM에 대 한 일시 중지 합니다. VM을 최대 30초간 일시 중지하고 RAM의 메모리를 보존하는 메모리 보존 유지 관리 메커니즘을 사용하는 경우도 있습니다. 그러면 가상 머신이 다시 시작되고 가상 머신의 시계가 자동으로 동기화됩니다. Azure는 점점 더 실시간 마이그레이션 기술을 사용하고 메모리 보존 유지 관리 메커니즘을 개선하여 일시 중지 기간을 줄이고 있습니다.

이러한 재부팅 불가능 유지 관리 작업은 장애 도메인에 의해 적용된 장애 도메인이고 경고 상태 신호를 받는 경우 진행률이 중지됩니다. 

일부 애플리케이션은 이러한 종류의 업데이트에 영향을 받을 수 있습니다. VM을 다른 호스트로 실시간 마이그레이션하는 경우 일부 중요한 워크로드는 VM 일시 중지까지 몇 분간 성능이 약간 저하될 수 있습니다. 이러한 애플리케이션은 [Windows](../articles/virtual-machines/windows/scheduled-events.md) 또는 [Linux](../articles/virtual-machines/linux/scheduled-events.md)용 Scheduled Events를 사용하여 VM 유지 관리를 준비하면 Azure 유지 관리 중에 영향을 받지 않을 수 있습니다. Azure는 이러한 중요 애플리케이션의 유지 관리 제어 기능에 대해서도 작업하고 있습니다. 


## <a name="maintenance-requiring-a-reboot"></a>다시 부팅이 필요한 유지 관리

드물긴 하지만 계획된 유지 관리를 위해 VM을 다시 부팅해야 하는 경우 미리 알림을 받습니다. 계획된 유지 관리에는 셀프 서비스 기간과 예약된 유지 관리 기간이라는 두 단계가 있습니다.

**셀프 서비스 기간**을 통해 VM에 대한 유지 관리를 시작할 수 있습니다. 이 기간 동안 각 VM을 쿼리하여 해당 상태를 보고 최신 유지 관리 요청의 결과를 확인할 수 있습니다.

셀프 서비스 유지 관리를 시작하면 VM이 이미 업데이트된 노드에 다시 배포됩니다. VM이 다시 부팅되기 때문에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다.

셀프 서비스 유지 관리를 시작하고 프로세스 중 오류가 발생한 경우 작업이 중지되고 VM이 업데이트 되지 않으며 셀프 서비스 유지 관리를 다시 시도하는 옵션이 제공됩니다. 

셀프 서비스 기간이 지난 경우 **예약된 유지 관리 기간**이 시작됩니다. 이 유지 관리 기간 중에는 계속 쿼리할 수 있지만, 직접 유지 관리를 시작할 수는 없습니다.

다시 부팅이 필요한 유지 관리의 관리에 대한 자세한 내용은 [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) 또는 [Windows](../articles/virtual-machines/windows/maintenance-notifications.md)용 "계획된 유지 관리 알림 처리"를 참조하세요. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>예약된 유지 관리 기간의 가용성 고려 사항 

예약된 유지 관리 기간까지 기다리기로 결정한 경우 VM의 고가용성을 유지 관리하기 위해 고려해야 할 몇 가지 사항이 있습니다. 

#### <a name="paired-regions"></a>쌍을 이루는 지역

각 Azure 지역은 동일한 지리적 위치 내의 다른 지역과 쌍을 이루어 함께 지역 쌍을 만듭니다. 예약된 유지 관리 단계에서 Azure는 지역 쌍의 단일 지역에서만 VM을 업데이트합니다. 예를 들어 미국 중북부에 있는 VM을 업데이트할 때 Azure는 미국 중남부의 VM을 동시에 업데이트하지 않습니다. 그러나 북유럽 등의 다른 지역은 미국 동부와 동시에 유지 관리될 수 있습니다. 지역 쌍의 작동 방식을 이해하면 지역에 VM을 더 잘 배포할 수 있습니다. 자세한 내용은 [Azure 지역 쌍](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)을 참조하세요.

#### <a name="availability-sets-and-scale-sets"></a>가용성 집합 및 확장 집합

Azure VM에서 워크로드를 배포할 때 애플리케이션에 고가용성을 제공하기 위해 가용성 세트 내에 VM을 만들 수 있습니다. 이렇게 하면 중단 또는 다시 부팅되는 유지 관리 이벤트 중에도 하나 이상의 VM을 사용할 수 있습니다.

가용성 집합 내에서 개별 VM은 최대 20개의 UD(업데이트 도메인)에 걸쳐 분산됩니다. 예약된 유지 관리 중에는 항상 단일 업데이트 도메인만 업데이트됩니다. 업데이트 도메인이 반드시 순차적으로 업데이트되는 것은 아닙니다. 

가상 머신 확장 집합은 동일한 VM 세트를 단일 리소스로 배포하고 관리할 수 있게 하는 Azure 계산 리소스입니다. 확장 집합은 가용성 집합의 VM과 같은 업데이트 도메인에 걸쳐 자동으로 배포됩니다. 가용성 집합과 마찬가지로 확장 집합에서도, 예약된 유지 관리 중에 항상 단일 업데이트 도메인만 업데이트됩니다.

고가용성을 위해 VM을 구성하는 방법에 대한 자세한 내용은 [Windows](../articles/virtual-machines/windows/manage-availability.md) 또는 [Linux](../articles/virtual-machines/linux/manage-availability.md)용 가상 머신의 가용성 관리를 참조하세요.
