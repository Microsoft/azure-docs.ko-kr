---
title: Azure Virtual Machine Scale Sets에 대한 디자인 고려 사항 | Microsoft Docs
description: Azure Virtual Machine Scale Sets에 대한 디자인 고려 사항에 대해 알아보기
keywords: linux 가상 머신, 가상 머신 크기 집합
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: c27c6a59-a0ab-4117-a01b-42b049464ca1
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/01/2017
ms.author: manayar
ms.openlocfilehash: 67bbad7e73f33d73d4c3f1d4f7e5599d2ef914e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618475"
---
# <a name="design-considerations-for-scale-sets"></a>확장 집합 디자인 고려 사항
이 문서에서는 Virtual Machine Scale Sets를 설계할 때 고려할 사항에 대해 논의합니다. Virtual Machine Scale Sets에 대한 자세한 내용은 [Virtual Machine Scale Sets 개요](virtual-machine-scale-sets-overview.md)를 참조하세요.

## <a name="when-to-use-scale-sets-instead-of-virtual-machines"></a>가상 머신 대신 확장 집합을 사용하는 경우
일반적으로 확장 집합은 구성이 유사한 일련의 컴퓨터로 구성된 고가용성 인프라를 배포하는 데 유용합니다. 그러나 일부 기능은 확장 집합에서만 사용할 수 있고, 다른 일부 기능은 VM에서만 사용할 수 있습니다. 각 기술을 언제 사용할지에 대한 정보를 바탕으로 의사 결정을 내리려면 먼저, 일반적으로 사용되는 기능 중 확장 집합에서는 사용 가능하지만 VM에서는 사용할 수 없는 기능 몇 가지를 살펴보면 됩니다.

### <a name="scale-set-specific-features"></a>확장 집합 특정 기능

- 확장 집합 구성을 지정하고 나면 추가 VM을 병렬로 배포하도록 *용량* 속성을 업데이트할 수 있습니다. 이 방법은 다수의 개별 VM을 병렬로 배포하는 작업을 오케스트레이션하는 스크립트를 작성하는 것보다 좋습니다.
- [Azure 자동 크기 조정을 사용하여 확장 집합의 크기를 자동으로 조정](./virtual-machine-scale-sets-autoscale-overview.md)할 수 있지만 개별 VM의 경우는 불가능합니다.
- [확장 집합 VM을 이미지로 다시 설치](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage)할 수 있지만 [개별 VM의 경우는 불가능합니다](https://docs.microsoft.com/rest/api/compute/virtualmachines).
- 안정성 향상과 배포 시간 단축을 위해 확장 집합 VM을 [오버프로비전](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)할 수 있습니다. 이 작업을 수행하는 사용자 지정 코드를 작성하지 않는 한 개별 VM을 과도하게 프로비전할 수 없습니다.
- 확장 집합 내 전체 VM에 대한 업그레이드 롤아웃을 수월하게 만들도록 [업그레이드 정책](./virtual-machine-scale-sets-upgrade-scale-set.md)을 지정할 수 있습니다. 개별 VM의 경우 업데이트를 직접 오케스트레이션해야 합니다.

### <a name="vm-specific-features"></a>VM 특정 기능

일부 기능은 현재 VM에서만 사용할 수 있습니다.

- 개별 VM에서 이미지를 캡처할 수 있지만 확장 집합의 VM에서는 캡처할 수 없습니다.
- 네이티브 디스크에서 관리 디스크로 개별 VM을 마이그레이션할 수 있지만, 확장 집합의 VM 인스턴스는 마이그레이션할 수 없습니다.
- 개별 VM 가상 NIC(네트워크 인터페이스 카드)에 IPv6 공용 IP 주소를 할당할 수 있지만 확장 집합의 VM 인스턴스에 대해서는 이렇게 할 수 없습니다. 개별 VM이나 확장 집합 VM 앞에 놓인 부하 분산 장치에 IPv6 공용 IP 주소를 할당할 수 있습니다.

## <a name="storage"></a>Storage

### <a name="scale-sets-with-azure-managed-disks"></a>Azure Managed Disks를 사용하는 확장 집합
확장 집합은 기존의 Azure Storage 계정 대신 [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)를 사용하여 만들 수 있습니다. Managed Disks는 다음과 같은 이점을 제공합니다.
- 확장 집합 VM에 대한 Azure 저장소 계정의 집합을 미리 만들 필요가 없습니다.
- 확장 집합에서 VM에 대한 [연결된 데이터 디스크](virtual-machine-scale-sets-attached-disks.md)를 정의할 수 있습니다.
- [집합에서 최대 1,000개의 VM을 지원](virtual-machine-scale-sets-placement-groups.md)하도록 확장 집합을 구성할 수 있습니다. 

기존 템플릿이 있는 경우에는 [Managed Disks를 사용하도록 템플릿을 업데이트](virtual-machine-scale-sets-convert-template-to-md.md)할 수도 있습니다.

### <a name="user-managed-storage"></a>사용자 관리 Storage
Azure Managed Disks로 정의되지 않은 확장 집합은 사용자가 생성한 저장소 계정을 사용하여 집합에 VM의 OS 디스크를 저장합니다. 저장소 계정당 20개 이하의 VM 비율은 최대 IO를 수행하고 _오버프로비전_을 활용하는 것도 좋습니다(아래 참조). 또한 저장소 계정 이름의 시작하는 문자에 알파벳을 고르게 사용하는 것이 좋습니다. 그러면 여러 내부 시스템에서 부하를 분산할 수 있습니다. 


## <a name="overprovisioning"></a>오버프로비전
확장 집합은 현재 VM을 “오버프로비전”하도록 기본적으로 설정되어 있습니다. 오버프로비전을 켜면 확장 집합은 실제로 사용자가 요청한 것보다 더 많은 VM을 작동한 다음 요청된 VM 수가 성공적으로 프로비전되면 추가 VM을 삭제합니다. 오버프로비전은 프로비전 성공률을 높이고 배포 시간을 절약합니다. 추가 VM에 대한 추가 비용은 청구되지 않으며 할당량 한도에 포함되지 않습니다.

오버프로비전을 사용하면 프로비전 성공률이 향상되지만 나타난 다음, 사라지는 추가 VM을 처리하도록 설계되지 않은 애플리케이션에서 동작이 혼란스러워질 수 있습니다. 오버프로비전을 해제하려면 템플릿에 `"overprovision": "false"` 문자열이 있어야 합니다. 자세한 내용은 [확장 집합 REST API 설명서](/rest/api/virtualmachinescalesets/create-or-update-a-set)에서 찾을 수 있습니다.

확장 집합이 사용자 관리 저장소를 사용하고 오버프로비전을 해제하는 경우 저장소 계정당 20개 이상의 VM을 가질 수 있지만 IO 성능상의 이유로 40개 이상은 권장되지 않습니다. 

## <a name="limits"></a>제한
Marketplace 이미지에 구축되고(플랫폼 이미지라고도 함) Azure Managed Disks를 사용하도록 구성된 확장 집합은 최대 1,000개의 VM 용량을 지원합니다. 100개 이상의 VM을 지원하도록 확장 집합을 구성하는 경우 모든 시나리오가 동일하게 동작하지 않습니다(예: 부하 분산). 자세한 내용은 [대규모 가상 머신 확장 집합과 작동](virtual-machine-scale-sets-placement-groups.md)을 참조하세요. 

사용자 관리 저장소 계정으로 구성된 확장 집합은 현재 100개의 VM으로 제한되어 있습니다(5개의 저장소 계정이 이 규모에 권장됨).

사용자 지정 이미지에 구축된 확장 집합은 Azure Managed Disks로 구성된 경우 최대 600개의 VM 용량을 가질 수 있습니다. 확장 집합이 사용자 관리 저장소 계정으로 구성된 경우 하나의 저장소 계정 내에서 모든 OS 디스크 VHD를 만들어야 합니다. 그 결과 사용자 지정 이미지 및 사용자 관리 저장소에 구축하는 확장 집합의 최대 추천 VM 수는 20개입니다. 오버프로비전을 해제할 경우 40개를 초과할 수 있습니다.

이러한 한도보다 더 많은 VM이 있는 경우 [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale)에 나오는 것처럼 여러 확장 집합을 배포해야 합니다.

