---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516264"
---
GRS(지역 중복 저장소)는 기본 지역에서 수백 마일 떨어진 보조 지역에 데이터를 복제하여 지정된 1년에 걸쳐 99.99999999999999%(16개의 9) 이상의 개체 내구성을 제공하도록 설계되었습니다. 스토리지 계정에서 GRS를 활성화하면 전체 지역 가동 중단 또는 기본 지역을 복구할 수 없는 재해의 경우라도 데이터는 지속됩니다.

GRS를 선택하는 경우 선택할 수 있는 두 가지 관련 옵션은 다음과 같습니다.

* GRS는 데이터를 보조 지역의 다른 데이터 센터에 복제하지만, Microsoft에서 주 지역에서 보조 지역으로 장애 조치를 시작하는 경우에만 해당 데이터를 읽을 수 있습니다. 
* RA-GRS(읽기 액세스 지역 중복 저장소)는 GRS를 기반으로 합니다. RA-GRS는 데이터를 보조 지역의 다른 데이터 센터에 복제하고, 보조 지역에서 읽을 수 있는 옵션도 제공합니다. RA-GRS를 사용하면 Microsoft에서 주 지역에서 보조 지역으로 장애 조치(failover)를 시작하는지 여부에 관계없이 보조 지역에서 읽을 수 있습니다. 

GRS 또는 RA-GRS를 사용하도록 설정된 스토리지 계정의 경우 모든 데이터는 먼저 LRS(로컬 중복 스토리지)를 사용하여 복제됩니다. 업데이트는 먼저 기본 위치에 커밋되고 LRS를 사용하여 복제됩니다. 그런 다음, 업데이트는 GRS를 사용하여 보조 지역에 비동기적으로 복제됩니다. 데이터가 보조 위치에 기록되는 경우 LRS를 사용하여 해당 위치 내에도 복제됩니다. 

주 지역 및 보조 지역에서 모두 별도의 장애 도메인에서 복제본을 관리하고, 저장소 배율 단위 내에서 도메인을 업그레이드합니다. 저장소 배율 단위는 데이터 센터 내의 기본 복제 단위입니다. 이 수준의 복제 LRS;에서 제공 되며 자세한 내용은 참조 하세요. [로컬 중복 저장소 (LRS): Azure Storage에 대한 저렴한 데이터 중복성](../articles/storage/common/storage-redundancy-lrs.md)을 참조하세요.

사용할 복제 옵션을 결정할 때 다음 사항에 유의하세요.

* ZRS(영역 중복 저장소)는 동기 복제를 사용하여 고가용성을 제공하고, 일부 시나리오에서 GRS 또는 RA-GRS보다 더 적합할 수 있습니다. ZRS에 대한 자세한 내용은 [ZRS](../articles/storage/common/storage-redundancy-zrs.md)를 참조하세요.
* 비동기 복제에는 데이터가 주 지역에 기록되는 시간에서 보조 지역으로 복제되는 시간까지의 지연이 발생합니다. 지역 재해의 경우 주 지역에서 해당 데이터를 복구할 수 없으면 보조 지역에 아직 복제되지 않은 변경 내용이 손실될 수 있습니다.
* GRS를 사용하면 Microsoft에서 보조 지역으로 장애 조치(failover)를 시작하지 않는 한 읽기 또는 쓰기를 위해 복제본에 액세스할 수 없습니다. 장애 조치(failover)의 경우 장애 조치가 완료된 후 해당 데이터에 대한 읽기 및 쓰기 액세스 권한이 생성됩니다. 자세한 내용은 [재해 복구 지침](../articles/storage/common/storage-disaster-recovery-guidance.md)을 참조하세요.
* 애플리케이션이 보조 지역에서 읽어야 하는 경우 RA-GRS를 사용하도록 설정합니다.