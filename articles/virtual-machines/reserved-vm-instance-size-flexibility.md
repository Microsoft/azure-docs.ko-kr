---
title: 가상 컴퓨터 크기 유연성-Azure Reserved VM Instances
description: 예약 할인은 예약 VM 인스턴스를 구입할 때에 어떤 크기 계열을 적용하는지 알아봅니다.
author: manish-shukla01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: manshuk
ms.openlocfilehash: 381023a0650b078a74c01ff7b698aebacb256147
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855499"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Reserved VM Instances에서 가상 머신 크기 유연성

예약 VM 인스턴스를 구입할 때 인스턴스 크기의 유연성 또는 용량 우선 순위를 최적화 하도록 선택할 수 있습니다. 예약 VM 인스턴스에 대 한 최적화 설정을 설정 하거나 변경 하는 방법에 대 한 자세한 내용은 [예약 된 vm 인스턴스에 대 한 최적화 설정 변경](../cost-management-billing/reservations/manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances)을 참조 하세요.

인스턴스 크기 유연성을 위해 최적화 된 예약 된 가상 머신 인스턴스를 사용 하 여 구입한 예약은 동일한 instance size 유연성 그룹의 Vm (가상 머신) 크기에 적용 될 수 있습니다. 예를 들어 Standard_DS5_v2 같이 DSv2 시리즈에 나열 된 VM 크기에 대 한 예약을 구매 하는 경우 예약 할인은 동일한 인스턴스 크기의 유연성 그룹에 나열 된 다른 4 개 크기에 적용 될 수 있습니다.

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

그러나 예약 할인은 다양 한 인스턴스 크기 유연성 그룹에 나열 된 Vm 크기에 적용 되지 않습니다. 예를 들어 DSv2 시리즈 High Memory의 Sku: Standard_DS11_v2, Standard_DS12_v2 등이 있습니다.

인스턴스 크기 유연성 그룹 내에서 예약 할인이 적용 되는 Vm의 수는 예약을 구매할 때 선택한 VM 크기에 따라 달라 집니다. 또한 실행하는 VM의 크기에 따라서도 달라집니다. 요율 열은 해당 인스턴스 크기 유연성 그룹의 각 VM 크기에 대 한 상대적 공간을 비교 합니다. 예약 할인을 실행하고 있는 VM에 적용하는 방법을 계산하기 위해 비율 값을 사용합니다.

## <a name="examples"></a>예제

다음 예제에서는 DSv2 시리즈 테이블의 크기 및 비율을 사용합니다.

크기의 비율 또는 해당 계열에서 다른 크기에 비해 상대적 공간은 8 Standard_DS4_v2 예약 VM 인스턴스를 구입 합니다.

- 시나리오 1: 1의 비율로 8대의 Standard_DS1_v2 크기 VM을 실행합니다. 예약 할인은 8대의 모든 해당 VM에 적용됩니다.
- 시나리오 2: 각각 2의 비율로 2대의 Standard_DS2_v2 크기 VM을 실행합니다. 또한 4의 비율로 Standard_DS3_v2 크기 VM을 실행합니다. 총 공간은 2+2+4=8입니다. 따라서 예약 할인은 3대의 모든 해당 VM에 적용됩니다.
- 시나리오 3: 16의 비율로 한 대의 Standard_DS5_v2를 실행합니다. 예약 할인은 해당 VM의 컴퓨팅 비용 절반에 적용됩니다.

다음 섹션에서는 인스턴스 크기 유연성에 최적화된 예약 VM 인스턴스를 구매하는 경우 동일한 크기 시리즈 그룹에 있는 크기를 보여줍니다.

## <a name="instance-size-flexibility-ratio-for-vms"></a>Vm에 대 한 인스턴스 크기 유연성 비율 

아래 CSV에는 인스턴스 크기의 유연성 그룹, ArmSkuName 및 비율이 있습니다.  

[인스턴스 크기 유연성 비율](https://isfratio.blob.core.windows.net/isfratio/ISFRatio.csv)

이 파일을 프로그래밍 방식으로 사용할 수 있도록 파일 URL과 스키마가 수정 됩니다. 곧 API를 통해 데이터를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Reservations 항목](../cost-management-billing/reservations/save-compute-costs-reservations.md)을 참조 하세요.
