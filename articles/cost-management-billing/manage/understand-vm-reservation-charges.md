---
title: Azure Reserved VM Instances 할인 이해
description: 실행 중인 VM에 Azure Reserved Virtual Machine Instances 할인이 적용되는 방식을 알아봅니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: dd21faa07426c80bb3a95dbd1b6d4ee93f783881
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686448"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-virtual-machines"></a>Azure 예약 할인이 가상 머신에 적용되는 방식

Azure Reserved Virtual Machine Instance를 구입한 후, 예약 할인이 예약의 특성 및 수량과 일치하는 가상 머신에 자동으로 적용됩니다. 예약은 가상 머신의 컴퓨팅 비용을 포함합니다.

예약 할인은 Azure Marketplace에서 구매한 기본 VM에 적용됩니다.

SQL Database 예약된 용량의 경우 [Azure Reserved Instances 할인](../reservations/understand-reservation-charges.md)을 참조하세요.

다음 표에서는 Reserved VM Instance를 구입한 후의 가상 머신에 대한 비용을 설명합니다. 모든 경우에는 정상 요금으로 스토리지 및 네트워킹에 대한 요금이 청구됩니다.

| 가상 머신 형식  | Reserved VM Instance 요금 |
|-----------------------|--------------------------------------------|
|추가 소프트웨어가 없는 Linux VM | 예약은 VM 인프라 비용을 포함합니다.|
|소프트웨어 요금이 포함된 Linux VM(예: Red Hat) | 예약은 인프라 비용을 포함합니다. 추가 소프트웨어에 대한 요금이 청구됩니다.|
|추가 소프트웨어가 없는 Windows VM |예약은 인프라 비용을 포함합니다. Windows 소프트웨어에 대한 요금이 청구됩니다.|
|추가 소프트웨어가 있는 Windows VM(예: SQL Server) | 예약은 인프라 비용을 포함합니다. Windows 소프트웨어 및 추가 소프트웨어에 대한 요금이 청구됩니다.|
|[Azure Hybrid Benefit](../../virtual-machines/windows/hybrid-use-benefit-licensing.md)을 포함한 Windows VM | 예약은 인프라 비용을 포함합니다. Windows 소프트웨어 비용은 Azure Hybrid Benefit에 포함되어 있습니다. 추가 소프트웨어는 별도로 청구됩니다.|

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="reservation-discount-for-non-windows-vms"></a>비 Windows VM에 대한 예약 할인

 Azure Reservation 할인은 시간 단위로 실행 중인 VM 인스턴스에 적용됩니다. 구입한 예약은 예약 할인을 적용할 실행 중인 VM의 사용량과 일치합니다. 전체 시간을 실행하지 못한 VM의 경우 예약은 현재 실행 중인 VM을 비롯하여 예약을 사용하지 않는 다른 VM에서 채워집니다. 시간이 다 지날 때쯤 해당 시간의 VM에 대한 예약 애플리케이션이 잠깁니다. 1시간 동안 VM이 실행되지 않거나 시간 내 동시 VM이 예약 시간을 채우지 못하는 경우 예약이 해당 시간 동안 미달 사용됩니다. 다음 그래프는 청구 가능한 VM 사용량에 대한 예약 애플리케이션을 보여줍니다. 일러스트레이션은 하나의 예약 구매 및 일치하는 두 개의 VM 인스턴스를 기반으로 합니다.

![적용된 하나의 예약 및 일치하는 두 개의 VM 인스턴스에 대한 스크린샷](./media/understand-vm-reservation-charges/billing-reserved-vm-instance-application.png)

1. 예약 줄을 초과하는 사용량은 일반 종량제 요금으로 청구됩니다. 예약 선을 초과하지 않는 사용량은 이미 예약 구매의 일부로 지불되었기 때문에 청구되지 않습니다.
2. 시간 1 중에 인스턴스 1은 0.75시간 동안 실행되고 인스턴스 2는 0.5시간 동안 실행됩니다. 시간 1의 총 사용량은 1.25시간입니다. 나머지 0.25시간에 대한 종량제 요금이 청구됩니다.
3. 시간 2 및 시간 3의 경우 두 인스턴스는 1시간 동안 실행되었습니다. 하나의 인스턴스는 예약에 포함되고 다른 하나는 종량제 요금으로 요금이 부과됩니다.
4. 시간 4의 경우 인스턴스 1은 0.5시간 동안 실행되고 인스턴스 2는 1시간 동안 실행됩니다. 인스턴스 1은 예약에 완전히 포함되고 인스턴스 2 중 0.5시간도 포함됩니다. 나머지 0.5시간에 대한 종량제 요금이 청구됩니다.

청구 사용량 보고서에서 Azure 예약 적용 결과를 이해하고 확인하려면 [예약 사용량 이해](../reservations/understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="reservation-discount-for-windows-vms"></a>Windows VM에 대한 예약 할인

Windows VM 인스턴스를 실행하면 인프라 비용을 포함하도록 예약이 적용됩니다. Windows VM의 VM 인프라 비용에 예약을 적용하는 것은 Windows 외 VM의 경우와 동일합니다. vCPU를 기준으로 Windows 소프트웨어에 대한 요금이 별도로 청구됩니다. [예약 사용 시 Windows 소프트웨어 비용](../reservations/reserved-instance-windows-software-costs.md)을 참조하세요. [Windows Server용 Azure 하이브리드 혜택](../../virtual-machines/windows/hybrid-use-benefit-licensing.md)으로 Windows 라이선스 비용을 처리할 수 있습니다.

## <a name="discount-can-apply-to-different-sizes"></a>할인은 다른 크기에 적용될 수 있습니다.

예약 VM 인스턴스를 구매하고 **인스턴스 크기 유연성에 최적화**를 선택하면 할인 범위는 선택하는 VM 크기에 적용됩니다. 동일한 시리즈 인스턴스 크기 유연성 그룹에 있는 다른 VM 크기에도 적용할 수 있습니다. 자세한 내용은 [예약 VM 인스턴스를 통해 유동적으로 가상 머신 크기 조정](../../virtual-machines/windows/reserved-vm-instance-size-flexibility.md)을 참조하세요.

## <a name="premium-storage-vms-dont-get-non-premium-discounts"></a>프리미엄 스토리지 VM에는 비프리미엄 할인이 제공되지 않습니다.

예를 들면 다음과 같습니다. 5개의 Standard_D1 VM에 대한 예약을 구입했다고 가정하면 예약 할인은 동일한 인스턴스 제품군의 Standard_D1 VM 또는 다른 VM에만 적용됩니다. 할인은 Standard_DS1 VM 또는 DS1 인스턴스 크기 유연성 그룹의 다른 크기에는 적용되지 않습니다.

예약 할인 애플리케이션은 VM에 사용되는 미터를 무시하고 ServiceType만 평가합니다. VM에 대한 인스턴스 유연성 그룹/시리즈 정보를 확인하려면 `AdditionalInfo`의 `ServiceType` 값을 확인합니다. 값은 사용량 CSV 파일에 있습니다.

구매 후에는 예약의 인스턴스 유연성 그룹/시리즈를 직접 변경할 수 없습니다. 그러나 하나의 인스턴스 유연성 그룹/시리즈에서 다른 인스턴스로 VM 예약을 *교환*할 수 있습니다.

## <a name="services-that-get-vm-reservation-discounts"></a>VM 예약 할인을 가져오는 서비스

VM 예약은 VM 배포뿐만 아니라 여러 서비스에서 내보낸 VM 사용량에 적용될 수 있습니다. 예약 할인을 받는 리소스는 인스턴스 크기 유연성 설정에 따라 변경됩니다.

### <a name="instance-size-flexibility-setting"></a>인스턴스 크기 유연성 설정

인스턴스 크기 유연성 설정은 예약 인스턴스 할인을 받는 서비스를 결정합니다.

설정이 on 또는 off인지 여부에 따라 예약 할인은 *ConsumedService*가 `Microsoft.Compute`일 때 일치하는 VM 사용량에 자동으로 적용됩니다. 따라서 *ConsumedService* 값에 대한 사용량 데이터를 확인합니다. 일부 사례:

- 가상 머신
- 가상 머신 크기 집합
- 컨테이너 서비스
- Azure Batch 배포(사용자 구독 모드에서)
- AKS(Azure Kubernetes Service)
- Service Fabric

설정이 on일 때 예약 할인은 *ConsumedService*가 다음 항목 중 하나에 해당하는 경우 일치하는 VM 사용량에 자동으로 적용됩니다.

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

사용량이 예약 할인에 적합한지 확인하려면 사용량 데이터의 *ConsumedService* 값을 확인합니다.

인스턴스 크기 유연성에 대한 자세한 내용은 [예약 VM 인스턴스에서 가상 머신 크기 유연성](../../virtual-machines/reserved-vm-instance-size-flexibility.md)을 참조하세요.


## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](../reservations/save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 컴퓨팅 리소스 요금 선결제](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure용 예약 관리](../reservations/manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](../reservations/understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](../reservations/understand-reserved-instance-usage-ea.md)
- [CSP 구독의 예약 사용량 이해](/partner-center/azure-reservations)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](../reservations/reserved-instance-windows-software-costs.md)
