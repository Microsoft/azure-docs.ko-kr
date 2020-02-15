---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 8607fd6f01ea02841017d09dac7236812f2330b9
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133720"
---
| 리소스 | 기본 제한 |
| --- | --- |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md) |지역 당 25000<sup>1</sup> . |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |지역 당 20<sup>개의 1</sup> 제한을 늘리려면 지원 담당자에 게 문의 하세요. |
| Azure 스폿 VM [구독](../articles/billing-buy-sign-up-azure-subscription.md) 당 총 코어 수 |지역 당 20<sup>개의 1</sup> 제한을 늘리려면 지원 담당자에 게 문의 하세요. |
| Dv2 및 F와 같은 시리즈 당 VM, [구독](../articles/billing-buy-sign-up-azure-subscription.md) 당 코어 수 |지역 당 20<sup>개의 1</sup> 제한을 늘리려면 지원 담당자에 게 문의 하세요. |
| 구독 당 [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |2000 지역입니다. |
| 가용성 집합당 가상 머신 | 200 |
| 구독당 인증서 |무제한<sup>2</sup> |

<sup>1</sup> 기본 제한은 무료 평가판, 종 량 제 및 Dv2, F, G 등의 시리즈와 같은 제품 범주 유형에 따라 달라 집니다. 예를 들어 기업계약 구독의 기본값은 350입니다.

<sup>2</sup> Azure Resource Manager를 사용 하면 인증서가 Azure Key Vault에 저장 됩니다. 구독에 대 한 인증서 수는 제한 되지 않습니다. 단일 VM 또는 가용성 집합으로 구성 되는 배포 당 1mb 인증서 한도가 있습니다.

> [!NOTE]
> 가상 컴퓨터 코어에는 지역별 총 한도가 있습니다. Dv2 및 F와 같은 지역 크기 계열에 대 한 제한도 있습니다. 이러한 한도는 개별적으로 적용 됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이 구독은 30 개의 A1 Vm 또는 30 D1 개의 Vm을 배포할 수 있으며, 총 30 개 코어를 초과 하지 않도록 둘의 조합을 사용할 수 있습니다. 결합의 예는 10 개의 A1 Vm과 20 D1 개의 Vm입니다.  
> <!-- -->
> 
