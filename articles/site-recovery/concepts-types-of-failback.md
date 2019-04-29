---
title: Azure Site Recovery를 사용하여 재해 복구 중 장애 복구(failback) | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery 서비스를 사용하여 재해 복구 중 온-프레미스로 장애 복구(failback)하는 동안 고려해야 할 주의 사항과 다양한 장애 복구(failback) 유형에 대해 간략하게 설명합니다.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 6f178ef1a0aec7f742bce4c2570962b995876026
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124121"
---
# <a name="failback-after-disaster-recovery-of-vmware-vms"></a>VMware Vm의 재해 복구 후 장애 복구

재해 복구 프로세스의 일부로 Azure로 장애 조치(failover)한 후 온-프레미스 사이트로 장애 복구(failback)할 수 있습니다. Azure Site Recovery에서 가능한 두 가지 유형의 장애 복구(failback)가 있습니다. 

- 원래 위치로 장애 복구(failback) 
- 대체 위치로 장애 복구(failback)

VMware 가상 머신을 장애 조치했는데 원본 온-프레미스 가상 머신이 여전히 남아 있는 경우 동일한 원본 온-프레미스 가상 머신으로 장애 복구할 수 있습니다. 이 시나리오에서는 변경 내용만 다시 복제됩니다. 이 시나리오를 **원래 위치 복구**라고 합니다. 온-프레미스 가상 머신이 없는 경우에는 **대체 위치 복구** 시나리오라고 합니다.

> [!NOTE]
> 원래 vCenter 및 구성 서버에 다시만 조치할 수 있습니다. 새 구성 서버를 배포한 후 이 서버를 사용하여 장애 복구(failback)할 수 없습니다. 기존 구성 서버에 새 vCenter를 추가한 후 새 vCenter로 장애 복구(failback)할 수도 없습니다.

## <a name="original-location-recovery-olr"></a>OLR(원래 위치 복구)
원래 가상 머신으로 장애 복구(failback)하도록 선택하는 경우 다음 조건을 충족해야 합니다.

* 가상 머신이 vCenter 서버에 의해 관리되는 경우 마스터 대상의 ESX 호스트에서 가상 머신의 데이터 저장소에 액세스할 수 있어야 합니다.
* 가상 머신이 ESX 호스트에 있지만 vCenter에 의해 관리되는 경우 가상 머신의 하드 디스크는 마스터 대상의 호스트가 액세스할 수 있는 데이터 저장소에 있어야 합니다.
* 가상 컴퓨터가 ESX 호스트에 있고 vCenter를 사용하지 않는 경우 다시 보호하기 전에 마스터 대상의 ESX 호스트 검색을 완료해야 합니다. 이는 물리적 서버를 장애 복구하는 경우에도 적용됩니다.
* 디스크가 이미 있고 온-프레미스 가상 머신에 연결된 경우에는 vSAN(가상 저장 영역 네트워크) 또는 RDM(매핑 원시 디바이스)을 기반으로 하는 디스크로 장애를 복구할 수 있습니다.

> [!IMPORTANT]
> 장애 복구(failback) 중에 Azure Site Recovery 서비스가 보류 중인 변경 내용이 기록될 가상 머신의 원래 VMDK를 식별할 수 있도록 disk.enableUUID= TRUE를 설정하는 것이 중요합니다. 이 값을 TRUE로 설정하지 않으면 서비스가 최선을 다해 해당하는 온-프레미스 VMDK를 식별하려고 합니다. 올바른 VMDK를 찾을 수 없는 경우, 추가 디스크를 만들고 이 디스크에 데이터가 기록됩니다.

## <a name="alternate-location-recovery-alr"></a>ALR(대체 위치 복구)
가상 머신을 다시 보호하기 전에 온-프레미스 가상 머신이 없는 경우를 대체 위치 복구 시나리오라고 합니다. 다시 보호 워크플로에서 온-프레미스 가상 머신을 다시 만듭니다. 또한 전체 데이터 다운로드가 발생합니다.

* 대체 위치로 장애 복구(failback)하는 경우 가상 머신은 온-프레미스 마스터 대상 서버가 배포되는 것과 동일한 ESX 호스트로 복구됩니다. 디스크를 만드는 데 사용된 데이터 저장소는 가상 컴퓨터를 다시 보호할 때 선택된 데이터 저장소와 동일합니다.
* VMFS(가상 머신 파일 시스템) 또는 vSAN 데이터 저장소에만 장애 복구할 수 있습니다. RDM이 있는 경우 다시 보호 및 장애 복구(failback)가 작동하지 않습니다.
* 다시 보호에는 대량의 초기 데이터 전송과 변경이 수행됩니다. 이 프로세스가 있는 이유는 가상 머신이 온-프레미스에 없기 때문입니다. 전체 데이터를 다시 복제해야 합니다. 이러한 다시 보호는 원래 위치 복구보다 더 많은 시간이 걸립니다.
* RDM 기반 디스크로 장애 복구할 수 없습니다. 새로운 VMDK(가상 머신 디스크)는 VMFS/vSAN 데이터 저장소에서만 만들 수 있습니다.

> [!NOTE]
> Azure로 장애 조치(failover)된 물리적 컴퓨터는 VMware 가상 머신으로만 장애 복구(failback)할 수 있습니다. 이는 대체 위치 복구와 동일한 워크플로를 따릅니다. 하나 이상의 마스터 대상 서버와 장애 복구(failback)해야 하는 필수 ESX/ESXi 호스트를 검색해야 합니다.

## <a name="next-steps"></a>다음 단계

[장애 복구(failback) 작업](vmware-azure-failback.md)을 수행하는 단계를 따르세요.

