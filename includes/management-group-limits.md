---
title: 포함 파일
description: 포함 파일
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 204ca121045d1bd53fd507acc7ea9691bfeac12e
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748895"
---
| 리소스 | 제한 |
| --- | --- |
| Azure AD 테넌트당 관리 그룹 | 10000 |
| 관리 그룹당 구독 | 무제한. |
| 관리 그룹 계층 구조의 수준 | 루트 수준 + 6 수준<sup>1</sup> |
| 관리 그룹당 직접 부모 관리 그룹 | 하나 |
| 위치당 [관리 그룹 수준 배포](../articles/azure-resource-manager/templates/deploy-to-management-group.md) | 800<sup>2</sup> |

<sup>1</sup>6 수준에는 구독 수준이 포함되지 않습니다.

<sup>2</sup>800개 배포 제한에 도달하면 기록에서 더 이상 필요하지 않은 배포를 삭제합니다. 관리 그룹 수준 배포를 삭제하려면 [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) 또는 [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete)를 사용합니다.
