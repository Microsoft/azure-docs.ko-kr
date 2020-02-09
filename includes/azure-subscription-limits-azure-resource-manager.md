---
title: 포함 파일
description: 포함 파일
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d94937a738034904413eac8b256121f14221d1ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845969"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md) |지역 당 25000<sup>1</sup> . |25000 지역입니다. |
| [구독](../articles/billing-buy-sign-up-azure-subscription.md)당 VM 총 코어 수 |지역 당 20<sup>개의 1</sup> | 지원에 문의 |
| Azure 스폿 VM [구독](../articles/billing-buy-sign-up-azure-subscription.md) 당 총 코어 수 |지역 당 20<sup>개의 1</sup> | 지원에 문의 |
| Dv2 및 F와 같은 시리즈 당 VM, [구독](../articles/billing-buy-sign-up-azure-subscription.md) 당 코어 수 |지역 당 20<sup>개의 1</sup> | 지원에 문의 |
| 구독당 하위 지역별 [스토리지 계정](../articles/storage/common/storage-account-create.md) |250 |250 |
| 구독 당 [가용성 집합](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |2000 지역입니다. |2000 지역입니다. |
| [선호도 그룹](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) |N/A<sup>3</sup> |N/A<sup>3</sup> |
| [클라우드 서비스](../articles/cloud-services/cloud-services-choose-me.md) |N/A<sup>3</sup> |N/A<sup>3</sup> |
| 구독 당 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure Resource Manager API 요청 크기 |4194304 바이트 |4194304 바이트 |
| 구독 당 태그<sup>2</sup> |무제한. |무제한. |
| 구독 당 고유한 태그 계산<sup>2</sup> | 10000 | 10000 |
| 위치별 [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>4</sup> | 800 |
| Azure Active Directory 테넌트 당 구독 | 무제한. | 무제한. |
| 구독 당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |무제한. |무제한. |

<sup>1</sup> 기본 제한은 무료 평가판, 종 량 제 및 Dv2, F, G 등의 시리즈와 같은 제품 범주 유형에 따라 달라 집니다. 예를 들어 기업계약 구독의 기본값은 350입니다.

<sup>2</sup> 구독 당 개수에 제한 없이 태그를 적용할 수 있습니다. 리소스 또는 리소스 그룹당 태그 수는 50 개로 제한 됩니다. 리소스 관리자은 태그 수가 1만 이하인 경우에만 구독의 [고유 태그 이름 및 값 목록을](/rest/api/resources/tags) 반환 합니다. 숫자가 1만를 초과 하는 경우에도 태그를 기준으로 리소스를 찾을 수 있습니다.  

<sup>3</sup> 이러한 기능은 Azure 리소스 그룹 및 리소스 관리자에 더 이상 필요 하지 않습니다.

<sup>4</sup> 800 배포 제한에 도달 하면 더 이상 필요 하지 않은 기록에서 배포를 삭제 합니다. 구독 수준 배포를 삭제 하려면 [AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 또는 [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)를 사용 합니다.

> [!NOTE]
> 가상 컴퓨터 코어에는 지역별 총 한도가 있습니다. Dv2 및 F와 같은 지역 크기 계열에 대한 제한도 있습니다. 이러한 한도는 개별적으로 적용 됩니다. 미국 동부 총 VM 코어 한도가 30이고, A 시리즈 코어 한도 30이며, D 시리즈 코어 한도는 30인 구독을 예로 들 수 있습니다. 이 구독은 30 개의 A1 Vm 또는 30 D1 개의 Vm을 배포할 수 있으며, 총 30 개 코어를 초과 하지 않도록 둘의 조합을 사용할 수 있습니다. 결합의 예는 10 개의 A1 Vm과 20 D1 개의 Vm입니다.  
> <!-- -->
> 
> 

