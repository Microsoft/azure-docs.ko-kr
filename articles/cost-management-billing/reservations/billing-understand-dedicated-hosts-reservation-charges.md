---
title: Azure Dedicated Hosts 예약 인스턴스 할인 이해
description: Azure Reserved VM Instances 할인이 Azure Dedicated Host에 적용되는 방식을 알아봅니다.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: banders
ms.openlocfilehash: f9080d13c4358a03b3f13ffcf346af42e49c47c6
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689459"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>Azure 예약 할인이 Azure Dedicated Host에 적용되는 방식

Azure Reserved Dedicated Host Instance를 구입한 후에 예약 할인이 예약의 특성 및 수량과 일치하는 전용 호스트에 자동으로 적용됩니다. 예약은 전용 호스트의 컴퓨팅 비용을 포함합니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

전용 호스트를 삭제하면 지정된 범위에 있는 일치하는 다른 리소스에 예약 할인이 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="reservation-discount-for-dedicated-hosts"></a>Dedicated Host에 대한 예약 할인

Azure Reserved Dedicated Hosts Instance는 전용 호스트에 사용되는 컴퓨팅 인프라 비용에 대한 할인은 제공합니다. 할인은 전용 호스트에 적용되며, 가상 머신에서 전용 호스트가 사용되는지 여부와는 관계가 없습니다. 예약에는 전용 호스트에 배포된 가상 머신의 라이선싱, 네트워크 사용 또는 스토리지 소비와 같은 추가 비용이 포함되지 않습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우  [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)

- [Azure Dedicated Host 사용](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)

- [Dedicated Host 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [Azure용 예약 관리](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)

- [종량제 구독의 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)

- [엔터프라이즈 등록에서 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)

- [CSP 구독의 예약 사용량 이해](https://docs.microsoft.com/partner-center/azure-reservations)

