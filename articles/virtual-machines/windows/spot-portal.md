---
title: 포털을 사용 하 여 Azure 스폿 Vm 배포
description: Azure PowerShell를 사용 하 여 비용을 절감 하는 별색 Vm을 배포 하는 방법을 알아봅니다.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 8845d0f9277384c1cc32b31b5ea5151cd0668c79
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158981"
---
# <a name="preview-deploy-spot-vms-using-the-azure-portal"></a>미리 보기: Azure Portal을 사용 하 여 스폿 Vm 배포

[스폿 vm](spot-vms.md) 을 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 모든 시점에서 Azure 인프라는 스폿 Vm을 제거 합니다. 따라서 지점 Vm은 일괄 처리 작업, 개발/테스트 환경, 대규모 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

지점 Vm의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 최대 가격을 설정 하는 방법에 대 한 자세한 내용은 [지점 vm-가격 책정](spot-vms.md#pricing)을 참조 하세요.

VM에 대해 시간당 요금을 지불할 최대 가격을 설정 하는 옵션이 있습니다. 지점 VM의 최대 가격은 미국 달러 (USD)로 설정 하 여 최대 5 개의 소수 자릿수를 사용할 수 있습니다. 예를 들어 `0.05701`값은 시간당 $0.05701 USD의 최대 가격이 됩니다. 최대 가격을 `-1`로 설정 하는 경우 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과 하는 경우 더 작은 표준 VM의 현재 가격 또는 가격입니다.

> [!IMPORTANT]
> 현재 스폿 인스턴스는 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>


## <a name="create-the-vm"></a>VM 만들기

스폿 Vm을 사용 하는 VM을 만드는 프로세스는 [빠른](quick-create-portal.md)시작에 자세히 설명 된 것과 동일 합니다. VM을 배포 하는 경우 Azure 스폿 인스턴스를 사용 하도록 선택할 수 있습니다.


**기본 탭의** **인스턴스 세부 정보** 섹션에서 **아니요** 는 Azure 스폿 인스턴스 사용에 대 한 기본값입니다.

![아니요를 선택 하기 위한 화면 캡처, Azure 스폿 인스턴스 사용 안 함](media/spot-portal/no.png)

**예**를 선택 하면 섹션이 확장 되 고 [제거 유형 및 제거 정책을](spot-vms.md#eviction-policy)선택할 수 있습니다. 

![화면 캡처 예를 선택 하 고 Azure 스폿 인스턴스를 사용 합니다.](media/spot-portal/yes.png)


## <a name="next-steps"></a>다음 단계

[PowerShell](spot-powershell.md)을 사용 하 여 스폿 vm을 만들 수도 있습니다.