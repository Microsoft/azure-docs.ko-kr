---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133693"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 구독 당 [리소스 그룹](../articles/azure-resource-manager/management/overview.md) |980 |980 |
| Azure Resource Manager API 요청 크기 |4194304 바이트 |4194304 바이트 |
| 구독 당 태그<sup>1</sup> |제한 없음 |제한 없음 |
| 구독 당 고유한 태그 계산<sup>1</sup> | 10000 | 10000 |
| 위치별 [구독 수준 배포](../articles/azure-resource-manager/templates/deploy-to-subscription.md) | 800<sup>2</sup> | 800 |
| Azure Active Directory 테 넌 트 당 구독 | 제한 없음 | 제한 없음 |
| 구독 당 [공동 관리자](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) |제한 없음 |제한 없음 |

<sup>1</sup> 구독 당 개수에 제한 없이 태그를 적용할 수 있습니다. 리소스 또는 리소스 그룹당 태그 수는 50 개로 제한 됩니다. 리소스 관리자은 태그 수가 1만 이하인 경우에만 구독의 [고유 태그 이름 및 값 목록을](/rest/api/resources/tags) 반환 합니다. 숫자가 1만를 초과 하는 경우에도 태그를 기준으로 리소스를 찾을 수 있습니다.  

<sup>2</sup> 800 배포 제한에 도달 하면 더 이상 필요 하지 않은 기록에서 배포를 삭제 합니다. 구독 수준 배포를 삭제 하려면 [AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) 또는 [az deployment delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete)를 사용 합니다.
